---
title: Şifrelenmiş Azure VM 'lerini yedekleme ve geri yükleme
description: Azure Backup hizmetiyle şifrelenmiş Azure VM 'lerinin nasıl yedeklendiğini ve geri yükleneceğini açıklar.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c4bc413e70d8e19f8006580c0631641651dcaf92
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172531"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Şifrelenmiş Azure VM 'yi yedekleme ve geri yükleme

Bu makalede, [Azure Backup](backup-overview.md) hizmetini kullanarak Windows veya Linux Azure sanal makinelerini (VM 'ler) şifrelenmiş disklerle yedekleme ve geri yükleme işlemlerinin nasıl yapılacağı açıklanır.

Başlamadan önce Azure Backup Azure VM 'leriyle nasıl etkileşime gireceğini öğrenmek istiyorsanız şu kaynakları gözden geçirin:

- Azure VM yedekleme mimarisini [gözden geçirin](backup-architecture.md#architecture-built-in-azure-vm-backup) .
- [Hakkında bilgi edinin](backup-azure-vms-introduction.md) Azure VM yedeklemesi ve Azure Backup uzantısı.

## <a name="encryption-support"></a>Şifreleme desteği

Azure Backup, işletim sistemi/veri disklerinin Azure disk şifrelemesi (ADE) ile şifrelenmiş olduğu Azure VM 'lerinin yedeklenmesini destekler. ADE, Windows VM 'leri şifrelemek için BitLocker 'ı ve Linux sanal makineleri için dm-crypt özelliğini kullanır. ADE, disk şifreleme anahtarlarını ve gizli dizileri yönetmek için Azure Key Vault ile tümleşir. Key Vault anahtar şifreleme anahtarları (KEKs), Key Vault yazmadan önce ek bir güvenlik katmanı eklemek ve şifreleme gizli dizilerini şifrelemek için kullanılabilir.

Azure Backup, aşağıdaki tabloda özetlenen Azure AD uygulaması olmadan ve ile Azure VM 'lerini kullanarak Azure sanal makinelerini yedekleyebilir ve geri yükleyebilir.

**VM disk türü** | **ADE (BEK/dm-crypt)** | **ADE ve KEK**
--- | --- | ---
**Yönetilmeyen** | Yes | Yes
**Lebilmesi**  | Yes | Yes

- [Ade](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md)ve [Keks](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)hakkında daha fazla bilgi edinin.
- Azure VM disk şifrelemesi [hakkında SSS](../security/azure-security-disk-encryption-faq.md) makalesini okuyun.

### <a name="limitations"></a>Sınırlamalar

- Şifrelenmiş VM 'Leri aynı abonelik ve bölge içinde yedekleyebilir ve geri yükleyebilirsiniz.
- Azure Backup, tek başına anahtarlar kullanılarak şifrelenmiş VM 'Leri destekler. Bir sanal makineyi şifrelemek için kullanılan bir sertifikanın parçası olan herhangi bir anahtar şu anda desteklenmemektedir.
- Kurtarma Hizmetleri yedekleme kasası ile aynı abonelik ve bölge içinde şifrelenmiş VM 'Leri yedekleyebilir ve geri yükleyebilirsiniz.
- Şifrelenmiş VM 'Ler dosya/klasör düzeyinde kurtarılamaz. Dosya ve klasörleri geri yüklemek için tüm VM 'yi kurtarmanız gerekir.
- Bir VM 'yi geri yüklerken, şifrelenen VM 'Ler için [mevcut VM 'yi Değiştir](backup-azure-arm-restore-vms.md#restore-options) seçeneğini kullanamazsınız. Bu seçenek yalnızca şifrelenmemiş yönetilen diskler için desteklenir.

## <a name="before-you-start"></a>Başlamadan önce

Başlamadan önce aşağıdakileri yapın:

1. ADE özellikli bir veya daha fazla [Windows](../security/azure-security-disk-encryption-windows.md) veya [Linux](../virtual-machines/linux/disk-encryption-overview.md) sanal makinesi kullandığınızdan emin olun.
2. Azure VM yedeklemesi için [destek matrisini gözden geçirin](backup-support-matrix-iaas.md)
3. Bir kurtarma hizmetleri Yedekleme Kasası yoksa, [oluşturun](backup-azure-arm-vms-prepare.md#create-a-vault) .
4. Yedekleme için zaten etkinleştirilmiş olan VM 'Lerde şifrelemeyi etkinleştirirseniz, yedeklemelerin kesintiye uğramadan devam edebilmesi için Key Vault erişmek için gereken izinlere sahip bir yedekleme sağlamanız gerekir. Bu izinleri atama hakkında [daha fazla bilgi edinin](#provide-permissions) .

Ayrıca, bazı durumlarda yapmanız gerekebilecek birkaç şey vardır:

- VM **ARACıSıNı VM 'ye yükleme**: Azure Backup makinede çalışan Azure VM aracısına bir uzantı yükleyerek Azure VM 'lerini yedekler. VM 'niz bir Azure Marketi görüntüsünden oluşturulduysa, aracı yüklenir ve çalışır. Özel bir VM oluşturursanız veya şirket içi bir makineyi geçirirseniz, [aracıyı el ile yüklemeniz](backup-azure-arm-vms-prepare.md#install-the-vm-agent)gerekebilir.
- **Giden erişime açıkça izin ver**: genel olarak, Azure Backup ile iletişim kurması Için BIR Azure VM 'ye giden ağ erişimine açıkça izin vermeniz gerekmez. Ancak, bazı VM 'Ler bağlantı sorunlarıyla karşılaşabilir ve bağlanmaya çalışırken **Extensionsnapshotfailednonetwork** hatası gösterir. Bu durumda, Azure Backup uzantısı yedekleme trafiği için Azure genel IP adresleriyle iletişim kurabildiğinden, [giden erişime açık bir şekilde izin vermeniz](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access)gerekir.

## <a name="configure-a-backup-policy"></a>Yedekleme ilkesi yapılandırma

1. Henüz bir kurtarma hizmetleri Yedekleme Kasası oluşturmadıysanız, [Bu yönergeleri](backup-azure-arm-vms-prepare.md#create-a-vault) izleyin
2. Portalda kasayı açın ve **Başlarken** bölümünde **Yedekle** ' yi seçin.

    ![Yedekleme dikey penceresi](./media/backup-azure-vms-encryption/select-backup.png)

3. **Yedekleme hedefi** 'nde > **Iş yükünüz nerede çalışıyor?** **Azure**' u seçin.
4. **Neleri yedeklemek istiyorsunuz?** **sanal makine** > **Tamam ' ı**seçin.

      ![Senaryo dikey penceresi](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. **Yedekleme ilkesi** > **yedekleme Ilkesi**' ni seçin, kasa ile ilişkilendirmek istediğiniz ilkeyi seçin. Daha sonra, **Tamam**'a tıklayın.
    - Yedekleme ilkesi, yedeklemelerin ne zaman alındığını ve ne kadar süreyle depolandığını belirtir.
    - Varsayılan ilkenin ayrıntıları, açılan menü altında listelenir.

    ![Senaryo dikey penceresini açma](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Varsayılan ilkeyi kullanmak istemiyorsanız, **Yeni oluştur**' u seçin ve [özel bir ilke oluşturun](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Seçme ilkesini kullanarak yedeklemek istediğiniz şifrelenmiş VM 'Leri seçin ve **Tamam**' ı seçin.

      ![Şifrelenmiş VM 'Leri seçin](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Azure Key Vault kullanıyorsanız, kasa sayfasında, Azure Backup Key Vault anahtarlara ve gizli anahtarlara salt okuma erişiminin gerektiğini belirten bir ileti görürsünüz.

    - Bu iletiyi alırsanız herhangi bir eylem gerekmez.

        ![Erişim Tamam](./media/backup-azure-vms-encryption/access-ok.png)

    - Bu iletiyi alırsanız, [Aşağıdaki yordamda](#provide-permissions)açıklandığı gibi izinleri ayarlamanız gerekir.

        ![Erişim Uyarısı](./media/backup-azure-vms-encryption/access-warning.png)

9. Yedekleme ilkesini kasada dağıtmak için **yedeklemeyi etkinleştir** ' e tıklayın ve seçili VM 'ler için yedeklemeyi etkinleştirin.

## <a name="trigger-a-backup-job"></a>Bir yedekleme işi tetikleyin

İlk yedekleme zamanlamaya uygun olarak çalışır, ancak bunu hemen aşağıdaki gibi çalıştırabilirsiniz:

1. Kasa menüsünde, **yedekleme öğeleri**' ne tıklayın.
2. **Yedekleme öğeleri**' nde **Azure sanal makine**' ye tıklayın.
3. **Yedekleme öğeleri** listesinde üç noktaya (...) tıklayın.
4. **Şimdi Yedekle**'ye tıklayın.
5. **Şimdi Yedekle**' de, kurtarma noktasının tutulacağı son günü seçmek için Takvim denetimini kullanın. Daha sonra, **Tamam**'a tıklayın.
6. Portal bildirimlerini izleyin. İş ilerlemesini kasa panosunda izleyebilirsiniz > **yedekleme işleri** > **devam**ediyor. VM’nizin boyutuna bağlı olarak, ilk yedeklemenin oluşturulması biraz zaman alabilir.

## <a name="provide-permissions"></a>İzinleri sağla

Azure VM, anahtar ve gizli dizileri, ilişkili VM 'lerle birlikte yedeklemek için salt okuma erişimi gerektirir.

- Key Vault Azure aboneliğinin Azure AD kiracısı ile ilişkilendirilir. **Üye bir Kullanıcı**ise, Azure Backup başka bir işlem yapmadan Key Vault erişim elde edin.
- **Konuk bir Kullanıcı**kullanıyorsanız, anahtar kasasına erişmek için Azure Backup izinleri sağlamanız gerekir.

İzinleri ayarlamak için:

1. Azure portal, **tüm hizmetler**' i seçin ve **anahtar**kasalarını arayın.
2. Yedeklemekte olduğunuz şifrelenmiş VM ile ilişkili anahtar kasasını seçin.
3. **Yeni ekle** > **erişim ilkeleri** ' ni seçin.
4. **Asıl seçin**' i seçin ve ardından **yedekleme yönetimi**yazın.
5. **Yedekleme yönetimi hizmetini** seçin > **seçin**.

    ![Yedekleme hizmeti seçimi](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Şablondan yapılandırma > **erişim Ilkesi Ekle** **(isteğe bağlı)** bölümünde **Azure Backup**' i seçin.
    - **Anahtar izinleri** ve **gizli izinler**için gerekli izinler önceden doldurulur.
    - VM 'niz **yalnızca bek**kullanılarak şifrelendiyse, yalnızca gizli dizi izinlerine ihtiyaç duyduğundan bu yana **Anahtar izinlerinin** seçimini kaldırın.

    ![Azure Backup seçimi](./media/backup-azure-vms-encryption/select-backup-template.png)

7. **OK (Tamam)** düğmesine tıklayın. **Yedekleme yönetimi hizmeti** **erişim ilkelerine**eklenir.

    ![Erişim ilkeleri](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. İzinlerle Azure Backup sağlamak için **Kaydet** ' e tıklayın.

## <a name="restore-an-encrypted-vm"></a>Şifrelenmiş bir VM 'yi geri yükleme

Şifrelenmiş VM 'Leri aşağıdaki şekilde geri yükleyebilirsiniz:

1. [VM diskini geri yükleyin](backup-azure-arm-restore-vms.md#restore-disks).
2. Ardından aşağıdakilerden birini yapın:
    - VM ayarlarını özelleştirmek için geri yükleme işlemi sırasında oluşturulan şablonu kullanın ve VM dağıtımını tetikleyin. [Daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - PowerShell kullanarak geri yüklenen disklerden yeni bir VM oluşturun. [Daha fazla bilgi edinin](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
    - Linux VM 'Ler için, veri disklerinin açık ve bağlanmış olması için ADE uzantısını sıfırlayın.

## <a name="next-steps"></a>Sonraki adımlar

Herhangi bir sorunla karşılaşırsanız, şu makaleleri gözden geçirin:

- Şifrelenmiş Azure sanal makinelerini yedekleme ve geri yükleme sırasında [sık karşılaşılan hatalar](backup-azure-vms-troubleshoot.md) .
- [Azure VM Aracısı/yedekleme uzantısı](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) sorunları.
