---
title: Şifrelenmiş Azure VM 'lerini yedekleme ve geri yükleme
description: Azure Backup hizmetiyle şifrelenmiş Azure VM 'lerinin nasıl yedeklendiğini ve geri yükleneceğini açıklar.
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: db06b64fba203fb3d2ed54d34235504ac6aa4e2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99223466"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Şifrelenmiş Azure sanal makinelerini yedekleme ve geri yükleme

Bu makalede, [Azure Backup](backup-overview.md) hizmetini kullanarak Windows veya Linux Azure sanal makinelerini (VM 'ler) şifrelenmiş disklerle yedekleme ve geri yükleme işlemlerinin nasıl yapılacağı açıklanır. Daha fazla bilgi için bkz. [Azure VM yedeklemelerini şifreleme](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).

## <a name="encryption-using-platform-managed-keys"></a>Platform tarafından yönetilen anahtarları kullanarak şifreleme

Varsayılan olarak, sanal makinelerinizdeki tüm diskler, [depolama hizmeti şifrelemesini](../storage/common/storage-service-encryption.md)kullanan platform tarafından yönetilen anahtarlar (PMK) kullanılarak otomatik olarak şifrelenir. Bu VM 'Leri, sonunda şifrelemeyi desteklemek için gerekli herhangi bir eylem olmadan Azure Backup kullanarak yedekleyebilirsiniz. Platform tarafından yönetilen anahtarlarla şifreleme hakkında daha fazla bilgi için [Bu makaleye bakın](../virtual-machines/disk-encryption.md#platform-managed-keys).

![Şifrelenmiş diskler](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları kullanarak şifreleme

Müşteri tarafından yönetilen anahtarlarla (CMK) diskleri şifrelerken, diskleri şifrelemek için kullanılan anahtar Azure Key Vault depolanır ve sizin tarafınızdan yönetilir. CMK kullanan Depolama Hizmeti Şifrelemesi (SSE), Azure disk şifrelemesi (ADE) şifrelemesi ile farklıdır. ADE, işletim sisteminin şifreleme araçlarını kullanır. SSE, depolama hizmetindeki verileri şifreler ve sanal makinelerinize yönelik herhangi bir işletim sistemini veya görüntüyü kullanmanıza olanak sağlar.

Disklerini şifrelemek için müşteri tarafından yönetilen anahtarlar kullanan VM 'lerin yedeklenmesi veya geri yüklenmesi için herhangi bir açık eylem gerçekleştirmeniz gerekmez. Kasada depolanan bu VM 'Ler için yedekleme verileri, [kasada kullanılan şifrelemeyle](encryption-at-rest-with-cmk.md)aynı yöntemlerle şifrelenir.

Yönetilen disklerin müşteri tarafından yönetilen anahtarlarla şifrelenmesi hakkında daha fazla bilgi için [Bu makaleye](../virtual-machines/disk-encryption.md#customer-managed-keys)bakın.

## <a name="encryption-support-using-ade"></a>ADE kullanarak şifreleme desteği

Azure Backup, işletim sistemi/veri disklerinin Azure disk şifrelemesi (ADE) ile şifrelenmiş olduğu Azure VM 'lerinin yedeklenmesini destekler. ADE, Windows VM 'leri şifrelemek için BitLocker 'ı ve Linux sanal makineleri için dm-crypt özelliğini kullanır. ADE, disk şifreleme anahtarlarını ve gizli dizileri yönetmek için Azure Key Vault ile tümleşir. Key Vault anahtar şifreleme anahtarları (KEKs), Key Vault yazmadan önce ek bir güvenlik katmanı eklemek ve şifreleme gizli dizilerini şifrelemek için kullanılabilir.

Azure Backup, aşağıdaki tabloda özetlenen Azure AD uygulaması olmadan ve ile Azure VM 'lerini kullanarak Azure sanal makinelerini yedekleyebilir ve geri yükleyebilir.

**VM disk türü** | **ADE (BEK/dm-crypt)** | **ADE ve KEK**
--- | --- | ---
**Yönetilmeyen** | Yes | Yes
**Yönetilen**  | Yes | Yes

- [Ade](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md)ve [Keks](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)hakkında daha fazla bilgi edinin.
- Azure VM disk şifrelemesi [hakkında SSS](../security/fundamentals/azure-disk-encryption-vms-vmss.md) makalesini okuyun.

### <a name="limitations"></a>Sınırlamalar

- Aynı abonelik ve bölge içinde, ADE şifreli VM 'Leri yedekleyebilir ve geri yükleyebilirsiniz.
- Azure Backup, tek başına anahtarlar kullanılarak şifrelenmiş VM 'Leri destekler. Bir VM 'yi şifrelemek için kullanılan bir sertifikanın parçası olan herhangi bir anahtar şu anda desteklenmemektedir.
- Kurtarma Hizmetleri yedekleme kasası ile aynı abonelik ve bölge içindeki ADE şifreli VM 'Leri yedekleyebilir ve geri yükleyebilirsiniz.
- Dosya/klasör düzeyinde, ADE şifreli VM 'Ler kurtarılamaz. Dosya ve klasörleri geri yüklemek için tüm VM 'yi kurtarmanız gerekir.
- Bir VM 'yi geri yüklerken, ADE şifreli VM 'Ler için [mevcut VM 'yi Değiştir](backup-azure-arm-restore-vms.md#restore-options) seçeneğini kullanamazsınız. Bu seçenek yalnızca şifrelenmemiş yönetilen diskler için desteklenir.

## <a name="before-you-start"></a>Başlamadan önce

Başlamadan önce aşağıdakileri yapın:

1. ADE özellikli bir veya daha fazla [Windows](../virtual-machines/linux/disk-encryption-overview.md) veya [Linux](../virtual-machines/linux/disk-encryption-overview.md) sanal makinesi kullandığınızdan emin olun.
2. Azure VM yedeklemesi için [destek matrisini gözden geçirin](backup-support-matrix-iaas.md)
3. Bir kurtarma hizmetleri Yedekleme Kasası yoksa, [oluşturun](backup-create-rs-vault.md) .
4. Yedekleme için zaten etkinleştirilmiş olan VM 'Lerde şifrelemeyi etkinleştirirseniz, yedeklemelerin kesintiye uğramadan devam edebilmesi için Key Vault erişmek için gereken izinlere sahip bir yedekleme sağlamanız gerekir. Bu izinleri atama hakkında [daha fazla bilgi edinin](#provide-permissions) .

Ayrıca, bazı durumlarda yapmanız gerekebilecek birkaç şey vardır:

- VM **ARACıSıNı VM 'ye yükleme**: Azure Backup makinede çalışan Azure VM aracısına bir uzantı yükleyerek Azure VM 'lerini yedekler. VM 'niz bir Azure Marketi görüntüsünden oluşturulduysa, aracı yüklenir ve çalışır. Özel bir VM oluşturursanız veya şirket içi bir makineyi geçirirseniz, [aracıyı el ile yüklemeniz](backup-azure-arm-vms-prepare.md#install-the-vm-agent)gerekebilir.

## <a name="configure-a-backup-policy"></a>Yedekleme ilkesi yapılandırma

1. Henüz bir kurtarma hizmetleri Yedekleme Kasası oluşturmadıysanız, [Bu yönergeleri](backup-create-rs-vault.md)izleyin.
1. Portalda kasayı açın ve **genel bakış** bölümünde **+ yedekleme** ' yi seçin.

    ![Yedekleme bölmesi](./media/backup-azure-vms-encryption/select-backup.png)

1.   >  **İş yükünüzün çalıştığı** yedekleme hedefi alanında **Azure**' ı seçin.
1. **Neleri yedeklemek istiyorsunuz?** **sanal makine**' yi seçin. Ardından **Yedekle**' yi seçin.

      ![Senaryo bölmesi](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. **Yedekleme ilkesi**  >  **yedekleme ilkesi**' nde, kasa ile ilişkilendirmek istediğiniz ilkeyi seçin. Ardından **Tamam**’ı seçin.
    - Yedekleme ilkesi, yedeklemelerin ne zaman alındığını ve ne kadar süreyle depolandığını belirtir.
    - Varsayılan ilkenin ayrıntıları, açılan menü altında listelenir.

    ![Yedekleme ilkesi seçin](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Varsayılan ilkeyi kullanmak istemiyorsanız, **Yeni oluştur**' u seçin ve [özel bir ilke oluşturun](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. **Sanal makineler** altında **Ekle**' yi seçin.

    ![Sanal makine Ekle](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Seçme ilkesini kullanarak yedeklemek istediğiniz şifrelenmiş VM 'Leri seçin ve **Tamam**' ı seçin.

      ![Şifrelenmiş VM 'Leri seçin](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Azure Key Vault kullanıyorsanız, kasa sayfasında, Azure Backup Key Vault anahtarlara ve gizli anahtarlara yalnızca okuma erişimi gerektiğini belirten bir ileti görürsünüz.

    - Bu iletiyi alırsanız herhangi bir eylem gerekmez.

        ![Erişim Tamam](./media/backup-azure-vms-encryption/access-ok.png)

    - Bu iletiyi alırsanız, [Aşağıdaki yordamda](#provide-permissions)açıklandığı gibi izinleri ayarlamanız gerekir.

        ![Erişim Uyarısı](./media/backup-azure-vms-encryption/access-warning.png)

1. Yedekleme ilkesini kasada dağıtmak için **yedeklemeyi etkinleştir** ' i seçin ve seçili VM 'ler için yedeklemeyi etkinleştirin.

## <a name="trigger-a-backup-job"></a>Bir yedekleme işi tetikleyin

İlk yedekleme zamanlamaya uygun olarak çalışır, ancak bunu hemen aşağıdaki gibi çalıştırabilirsiniz:

1. Kasa menüsünde **yedekleme öğeleri**' ni seçin.
2. **Yedekleme öğeleri**' nde **Azure sanal makine**' yi seçin.
3. **Yedekleme öğeleri** listesinde üç nokta (...) simgesini seçin.
4. **Şimdi Yedekle**' yi seçin.
5. **Şimdi Yedekle**' de, kurtarma noktasının tutulacağı son günü seçmek için Takvim denetimini kullanın. Ardından **Tamam**’ı seçin.
6. Portal bildirimlerini izleyin. İş ilerlemesini kasa panosunda izleyebilirsiniz > **yedekleme işleri**  >  **devam** ediyor. VM’nizin boyutuna bağlı olarak, ilk yedeklemenin oluşturulması biraz zaman alabilir.

## <a name="provide-permissions"></a>İzinleri sağla

Azure Backup, anahtar ve gizli dizileri, ilişkili VM 'lerle birlikte yedeklemek için salt okuma erişimi gerektirir.

- Key Vault Azure aboneliğinin Azure AD kiracısı ile ilişkilendirilir. **Üye bir Kullanıcı** ise, Azure Backup başka bir işlem yapmadan Key Vault erişim elde edin.
- **Konuk bir Kullanıcı** kullanıyorsanız, anahtar kasasına erişmek için Azure Backup izinleri sağlamanız gerekir.

İzinleri ayarlamak için:

1. Azure portal, **tüm hizmetler**' i seçin ve **anahtar** kasalarını arayın.
1. Yedeklemekte olduğunuz şifrelenmiş VM ile ilişkili anahtar kasasını seçin.

    >[!TIP]
    >Bir VM 'nin ilişkili anahtar kasasını belirlemek için aşağıdaki PowerShell komutunu kullanın. Kaynak grubu adınızı ve VM adını değiştirin:
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > Şu satırda Anahtar Kasası adını arayın:
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. Erişim **ilkeleri**  >  **Ekle erişim ilkesi**' ni seçin.

    ![Erişim İlkesi Ekle](./media/backup-azure-vms-encryption/add-access-policy.png)

1. Şablondan yapılandırma **İlkesi Ekle**  >  **(isteğe bağlı)** bölümünde **Azure Backup**' yi seçin.
    - **Anahtar izinleri** ve **gizli izinler** için gerekli izinler önceden doldurulur.
    - VM 'niz **yalnızca bek** kullanılarak şifrelendiyse, yalnızca gizli dizi izinlerine ihtiyaç duyduğundan bu yana **Anahtar izinlerinin** seçimini kaldırın.

    ![Azure Backup seçimi](./media/backup-azure-vms-encryption/select-backup-template.png)

1. **Add (Ekle)** seçeneğini belirleyin. **Yedekleme yönetimi hizmeti** **erişim ilkelerine** eklenir.

    ![Erişim ilkeleri](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. İzinlerle Azure Backup sağlamak için **Kaydet** ' i seçin.

## <a name="restore-an-encrypted-vm"></a>Şifrelenmiş bir VM 'yi geri yükleme

Şifrelenmiş VM 'Ler yalnızca, aşağıda açıklandığı gibi VM diski geri yüklenirken geri yüklenebilir. **Var olan** ve **geri yükleme VM 'sinin** yerini değiştirme desteklenmiyor.

Şifrelenmiş VM 'Leri şu şekilde geri yükleyin:

1. [VM diskini geri yükleyin](backup-azure-arm-restore-vms.md#restore-disks).
2. Aşağıdaki eylemlerden birini gerçekleştirerek sanal makine örneğini yeniden oluşturun:
    1. VM ayarlarını özelleştirmek için geri yükleme işlemi sırasında oluşturulan şablonu kullanın ve VM dağıtımını tetikleyin. [Daha fazla bilgi edinin](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. PowerShell kullanarak geri yüklenen disklerden yeni bir VM oluşturun. [Daha fazla bilgi edinin](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Linux sanal makineleri için, veri disklerinin açık ve bağlanmış olması için ADE uzantısını yeniden yükleyin.

## <a name="next-steps"></a>Sonraki adımlar

Herhangi bir sorunla karşılaşırsanız, şu makaleleri gözden geçirin:

- Şifrelenmiş Azure sanal makinelerini yedekleme ve geri yükleme sırasında [sık karşılaşılan hatalar](backup-azure-vms-troubleshoot.md) .
- [Azure VM Aracısı/yedekleme uzantısı](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) sorunları.
