---
title: Microsoft Azure Kurtarma Hizmetleri kasasını silme
description: Bu makalede, bağımlılıkları kaldırmayı ve sonra bir Microsoft Azure Backup Recovery Services (MARS) kasasını silmeyi öğrenin.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 23e0d2874229616037c44800639411f66bc4d1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173181"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure Backup Recovery Services kasasını silme

Bu makalede, bir Microsoft [Azure Backup](backup-overview.md) Recovery SERVICES (mars) kasasının nasıl silineceği açıklanır. Bağımlılıkları kaldırmak ve ardından kasayı silmek için yönergeler içerir.

## <a name="before-you-start"></a>Başlamadan önce

Korumalı sunucular veya yedekleme yönetim sunucuları gibi bağımlılıkları olan bir kurtarma hizmetleri kasasını silemezsiniz.

- Yedekleme verileri içeren kasaların silinemediği (korumayı durdursanız ancak yedekleme verilerini korusanız bile).

- Bağımlılıklar içeren bir kasayı silerseniz aşağıdaki ileti görünür:

  ![Kasa hatasını silin.](./media/backup-azure-delete-vault/error.png)

- Bağımlılıklar içeren bir portaldan şirket içi korumalı öğeyi silerseniz, bir uyarı iletisi görünür:

  ![Korumalı sunucu hatasını silin.](./media/backup-azure-delete-vault/error-message.jpg)

- Yedekleme öğeleri geçici olarak silinmiş durumdaysa uyarı iletisi görünür ve kalıcı olarak silinene kadar beklemeniz gerekir. Daha fazla bilgi için [bu makaleye](https://aka.ms/SoftDeleteCloudWorkloads) bakın.

   ![Kasa hatasını silin.](./media/backup-azure-delete-vault/error-message-soft-delete.png)
  
Kasayı silmek için, kurulumla eşleşen senaryoyu seçin ve önerilen adımları izleyin:

Senaryo | Kasayı silmek için bağımlılıkları kaldırma adımları |
-- | --
Azure Backup Aracısı kullanılarak korunan şirket içi dosya ve Klasörlerim var ve Azure 'a yedekleniyor | [Mars yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mars-management-console) adımlarını gerçekleştirin.
Azure 'da MABS (Microsoft Azure Backup Server) veya DPM (System Center Data Protection Manager) kullanılarak korunan şirket içi makinelerim var | [MABS yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mabs-management-console) adımlarını gerçekleştirin.
Bulutta korumalı öğeler var (örneğin, bir laaS sanal makinesi veya Azure dosya paylaşımında)  | [Bulutta korunan öğeleri silme](#delete-protected-items-in-the-cloud) adımlarını uygulayın
Hem şirket içinde hem de bulutta korumalı Öğelerim var | Aşağıdaki bölümlerde bulunan adımları aşağıdaki sırayla gerçekleştirin: <br> 1. [buluttaki korumalı öğeleri silme](#delete-protected-items-in-the-cloud)<br> 2. [Mars yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mars-management-console) <br> 3. [MABS yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mabs-management-console)
Şirket içinde veya bulutta herhangi bir korumalı öğe yok; Ancak yine de kasa silme hatası alıyorum | [Azure Resource Manager kullanarak kurtarma hizmetleri kasasını silme](#delete-the-recovery-services-vault-by-using-azure-resource-manager) bölümündeki adımları gerçekleştirin

## <a name="delete-protected-items-in-the-cloud"></a>Bulutta korunan öğeleri silme

İlk olarak, bağımlılıklar ve kasa silme işlemini anlamak için **[başlamadan önce](#before-you-start)** bölümünü okuyun.

Korumayı durdurmak ve yedekleme verilerini silmek için aşağıdaki adımları gerçekleştirin:

1. Portaldan **Kurtarma Hizmetleri Kasası**' na gidin ve ardından **yedekleme öğeleri**' ne gidin. Ardından, bulutta korunan öğeleri (örneğin, Azure sanal makineleri, Azure depolama [Azure dosyaları hizmeti] veya Azure sanal makinelerinde SQL Server) seçin.

    ![Yedekleme türünü seçin.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Yedekleme öğesini seçmek için sağ tıklayın. Yedekleme öğesinin korumalı olup olmadığına bağlı olarak, menü **Yedeklemeyi Durdur** bölmesini veya **yedekleme verilerini sil** bölmesini görüntüler.

    - **Yedeklemeyi Durdur** bölmesi görünürse, açılan menüden **yedekleme verilerini sil** ' i seçin. Yedekleme öğesinin adını girin (Bu alan büyük/küçük harfe duyarlıdır) ve ardından açılan menüden bir neden seçin. Varsa yorumlarınızı girin. Ardından, **Yedeklemeyi Durdur**' u seçin.

        ![Yedeklemeyi Durdur bölmesi.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - **Yedekleme verilerini sil** bölmesi görüntülenirse, yedekleme öğesinin adını girin (Bu alan büyük/küçük harfe duyarlıdır) ve ardından açılan menüden bir neden seçin. Varsa yorumlarınızı girin. Ardından **Sil**' i seçin.

         ![Yedekleme verilerini sil bölmesi.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. **Bildirim simgesine** bakın: bildirim simgesini ![.](./media/backup-azure-delete-vault/messages.png) İşlem tamamlandıktan sonra hizmet şu iletiyi görüntüler: *yedekleme durduruluyor ve "yedekleme öğesi" için yedekleme verileri siliniyor*. *Işlem başarıyla tamamlandı*.
4. Yedekleme öğesinin silindiğinden emin olmak için **yedekleme öğeleri** menüsünde **Yenile** ' yi seçin.

      ![Yedekleme öğelerini silme sayfası.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Şirket içi korumalı öğeleri sil

İlk olarak, bağımlılıklar ve kasa silme işlemini anlamak için **[başlamadan önce](#before-you-start)** bölümünü okuyun.

1. Kasa panosu menüsünden **Yedekleme altyapısı**' nı seçin.
2. Şirket içi senaryonuza bağlı olarak, aşağıdaki seçeneklerden birini seçin:

      - MARS için **korumalı sunucular** ' ı seçin ve ardından **Aracı Azure Backup**. Ardından, silmek istediğiniz sunucuyu seçin.

        ![MARS için, kendi panosunu açmak için kasanızı seçin.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS veya DPM için **yedekleme yönetim sunucuları**' nı seçin. Ardından, silmek istediğiniz sunucuyu seçin.

          ![MABS için, kendi panosunu açmak için kasanızı seçin.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **Silme** bölmesi bir uyarı iletisiyle birlikte görüntülenir.

     ![Silme bölmesi.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Uyarı iletisini ve onay onay kutusundaki yönergeleri gözden geçirin.
    > [!NOTE]
    >- Korumalı sunucu Azure hizmetleri ile eşitlenmişse ve yedekleme öğeleri mevcutsa, izin onay kutusu bağımlı yedekleme öğelerinin sayısını ve yedekleme öğelerini görüntülemek için bağlantıyı görüntüler.
    >- Korumalı sunucu Azure hizmetleriyle eşitlenmediği ve yedekleme öğeleri mevcutsa, izin onay kutusu yalnızca yedekleme öğelerinin sayısını görüntüler.
    >- Yedekleme öğesi yoksa, izin onay kutusu silmeyi ister.

4. Onay onay kutusunu seçin ve **Sil**' i seçin.

5. Yedekleme verilerini silme](./media/backup-azure-delete-vault/messages.png)![**bildirim** simgesine bakın. İşlem tamamlandıktan sonra hizmet şu iletiyi görüntüler: *yedekleme durduruluyor ve "yedekleme öğesi" için yedekleme verilerini silme.* *Işlem başarıyla tamamlandı*.
6. Yedekleme öğesinin silindiğinden emin olmak için **yedekleme öğeleri** menüsünde **Yenile** ' yi seçin.

Bu işlem tamamlandıktan sonra, yedekleme öğelerini yönetim konsolundan silebilirsiniz:

- [MARS yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mars-management-console)
- [MABS yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>MARS yönetim konsolundan yedekleme öğelerini silme

1. MARS yönetim konsolunu açın, **Eylemler** bölmesine gidin ve **yedeklemeyi zamanla**' yı seçin.
2. **Zamanlanmış bir yedeklemeyi Değiştir veya Durdur** sayfasında, **Bu yedekleme zamanlamasını kullanarak Durdur ' u seçin ve tüm depolanan yedeklemeleri silin**. Sonra **İleri**’yi seçin.

    ![Zamanlanmış bir yedeklemeyi değiştirin veya durdurun.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **Zamanlanan yedeklemeyi durdur** sayfasında **son**' u seçin.

    ![Zamanlanmış bir yedeklemeyi durdurun.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. El ile oluşturmanız gereken bir güvenlik PIN 'ı (kişisel kimlik numarası) girmeniz istenir. Bunu yapmak için öncelikle Azure portal oturum açın.
5. **Kurtarma Hizmetleri kasası** > **ayarları** > **Özellikler**' e gidin.
6. **GÜVENLIK PIN**'ı altında **Oluştur**' u seçin. Bu PIN 'ı kopyalayın. PIN yalnızca beş dakika için geçerlidir.
7. Yönetim konsolunda PIN 'i yapıştırın ve **Tamam**' ı seçin.

    ![Güvenlik PIN 'ı oluştur.](./media/backup-azure-delete-vault/security-pin.png)

8. **Yedekleme Ilerlemesini Değiştir** sayfasında aşağıdaki ileti görüntülenir: *silinen yedekleme verileri 14 gün boyunca saklanacaktır. Bu süreden sonra, yedekleme verileri kalıcı olarak silinir.*  

    ![Yedekleme altyapısını silin.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Şirket içi yedekleme öğelerini sildikten sonra, portaldan sonraki adımları izleyin.

### <a name="delete-backup-items-from-the-mabs-management-console"></a>MABS yönetim konsolundan yedekleme öğelerini silme

MABS yönetim konsolundan yedekleme öğelerini silmek için kullanabileceğiniz iki yöntem vardır.

#### <a name="method-1"></a>Yöntem 1

Korumayı durdurmak ve yedekleme verilerini silmek için aşağıdaki adımları uygulayın:

1. DPM Yönetici Konsolu açın ve ardından Gezinti çubuğunda **koruma** ' yı seçin.
2. Görüntü bölmesinde, kaldırmak istediğiniz koruma grubu üyesini seçin. **Grup üyelerinin korumasını durdur** seçeneğini belirlemek için sağ tıklayın.
3. **Korumayı Durdur** iletişim kutusunda **korunan verileri Sil**' i seçin ve ardından **depolamayı çevrimiçi Sil** onay kutusunu seçin. Ardından, **Korumayı Durdur**' u seçin.

    ![Korumayı Durdur bölmesinden korumalı verileri Sil ' i seçin.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Korumalı üye durumu, *etkin olmayan çoğaltma*olarak değişir.

4. Etkin olmayan koruma grubuna sağ tıklayın ve **etkin olmayan korumayı kaldır**' ı seçin.

    ![Etkin olmayan korumayı kaldırın.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. **Etkin olmayan korumayı Sil** penceresinde, **çevrimiçi depolamayı sil** onay kutusunu seçin ve ardından **Tamam**' ı seçin.

    ![Çevrimiçi depolamayı silin.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Yöntem 2

**Mabs yönetim** konsolunu açın. **Veri koruma yöntemini seçin**altında, **çevrimiçi koruma** istiyorum onay kutusunu temizleyin.

  ![Veri koruma yöntemini seçin.](./media/backup-azure-delete-vault/data-protection-method.png)

Şirket içi yedekleme öğelerini sildikten sonra, portaldan sonraki adımları izleyin.

## <a name="delete-the-recovery-services-vault"></a>Kurtarma Hizmetleri kasasını silme

1. Tüm bağımlılıklar kaldırıldığında, kasa menüsündeki **temel** bileşenler bölmesine gidin.
2. Herhangi bir yedekleme öğesi, yedek yönetim sunucusu veya yinelenen öğe olmadığını doğrulayın. Öğeler hala kasada görünüyorsa, [başlamadan önce](#before-you-start) bölümüne bakın.

3. Kasada daha fazla öğe yoksa, kasa panosunda **Sil** ' i seçin.

    ![Kasa panosunda Sil ' i seçin.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Kasayı silmek istediğinizi doğrulamak için **Evet** ' i seçin. Kasa silinir. Portal **Yeni** hizmet menüsüne geri döner.

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>PowerShell kullanarak kurtarma hizmetleri kasasını silme

İlk olarak, bağımlılıklar ve kasa silme işlemini anlamak için **[başlamadan önce](#before-you-start)** bölümünü okuyun.

Korumayı durdurmak ve yedekleme verilerini silmek için:

- Azure VM 'Leri yedeklemesi ve SQL örnekleri için otomatik korumayı etkinleştirmek için SQL kullanıyorsanız, önce otomatik korumayı devre dışı bırakın.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  Azure Backup korumalı bir öğe için korumayı devre dışı bırakma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) .

- Bulutu Durdur ve bulutta bulunan tüm yedekleme korumalı öğeler için verileri sil (örn. laaS VM, Azure dosya paylaşma vb.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Daha fazla bilgi edinin](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) yedekleme korumalı bir öğe için korumayı devre dışı bırakır.

- Azure 'a yedekleme Azure Backup Aracısı (MARS) kullanılarak korunan şirket içi dosyalar ve klasörler için, her bir MARS PowerShell modülündeki yedeklenen verileri silmek için aşağıdaki PowerShell komutunu kullanın:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Aşağıdaki istem nerede görünür:

    *Microsoft Azure Backup bu yedekleme ilkesini kaldırmak istediğinizden emin misiniz? Silinen yedekleme verileri 14 gün boyunca tutulacaktır. Bu süreden sonra, yedekleme verileri kalıcı olarak silinir. <br/> [Y] Evet [A] Evet [A] Evet [A] Hayır [N] tüm [S] askıya alma [?] Yardım (varsayılan: "Y"):*

- MABS (Microsoft Azure Backup Server) veya DPM 'den Azure 'a (System Center Data Protection Manager) göre korunan şirket içi makineler için, Azure 'daki yedeklenen verileri silmek üzere aşağıdaki komutu kullanın.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Aşağıdaki istem nerede görünür:

   *Microsoft Azure Backup* Bu yedekleme ilkesini kaldırmak istediğinizden emin misiniz? Silinen yedekleme verileri 14 gün boyunca tutulacaktır. Bu süreden sonra, yedekleme verileri kalıcı olarak silinir. <br/>
   [Y] Evet [A] Evet [A] Evet [A] Hayır [N] tüm [S] askıya alma [?] Yardım (varsayılan "Y"):*

Yedeklenen verileri sildikten sonra şirket içi kapsayıcıların ve yönetim sunucularının kaydını kaldırın.

- Azure 'a yedekleme Azure Backup Aracısı (MARS) kullanılarak korunan şirket içi dosyalar ve klasörler için:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    Bir Windows sunucusu veya başka bir kapsayıcının kasadan kaydını kaldırma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) .

- MABS (Microsoft Azure Backup Server) veya DPM ile Azure arasında korunan şirket içi makineler için (System Center veri koruma yönetimi:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    Bir yedekleme yönetimi kapsayıcısının kasadan kaydını kaldırma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) .

Yedeklenen verileri kalıcı olarak sildikten ve tüm kapsayıcıların kaydını kaldırdıktan sonra kasayı silme işlemine devam edin.

Kurtarma Hizmetleri kasasını silmek için:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

Kurtarma Hizmetleri kasasını silme hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) .

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>CLı kullanarak kurtarma hizmetleri kasasını silme

İlk olarak, bağımlılıklar ve kasa silme işlemini anlamak için **[başlamadan önce](#before-you-start)** bölümünü okuyun.

> [!NOTE]
> Şu anda Azure Backup CLı yalnızca Azure VM yedeklemelerini yönetmeyi destekler, bu nedenle kasayı silmek için aşağıdaki komut yalnızca kasa Azure VM yedeklemeleri içeriyorsa işe yarar. Kasa, Azure VM 'lerinden farklı türde herhangi bir yedekleme öğesi içeriyorsa Azure Backup CLı kullanarak bir kasayı silemezsiniz.

Mevcut kurtarma hizmetleri kasasını silmek için aşağıdakileri yapın:

- Korumayı durdurmak ve yedekleme verilerini silmek için

    ```CLI
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Daha fazla bilgi için bu [makaleye](/cli/azure/backup/protection#az-backup-protection-disable)bakın.

- Var olan bir kurtarma hizmetleri kasasını silme:

    ```CLI
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Daha fazla bilgi için bu [makaleye](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest) bakın

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Kurtarma Hizmetleri kasasını Azure Resource Manager kullanarak silme

Kurtarma Hizmetleri kasasını silmek için bu seçenek yalnızca tüm bağımlılıkların kaldırılması ve *kasa silme hatasını*almaya devam ediyorsanız önerilir. Aşağıdaki ipuçlarından birini veya tümünü deneyin:

- Kasa menüsündeki **temel** bileşenler bölmesinden hiçbir yedekleme öğesi, yedekleme yönetim sunucusu veya yinelenen öğe olmadığını doğrulayın. Yedekleme öğeleri varsa, [başlamadan önce](#before-you-start) bölümüne bakın.
- [Kasayı portaldan silmeyi](#delete-the-recovery-services-vault) yeniden deneyin.
- Tüm bağımlılıklar kaldırılırsa ve yine de *kasa silme hatası*alıyorsanız, aşağıdaki adımları gerçekleştirmek Için ARMClient aracını kullanın (NotDan sonra).

1. Chocolatey indirmek ve yüklemek için [Chocolatey.org](https://chocolatey.org/) adresine gidin. Daha sonra, aşağıdaki komutu çalıştırarak ARMClient 'ı yüklemelisiniz:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure hesabınızda oturum açın ve ardından şu komutu çalıştırın:

    `ARMClient.exe login [environment name]`

3. Azure portal, silmek istediğiniz kasasının abonelik KIMLIĞINI ve kaynak grubu adını toplayın.

ARMClient komutu hakkında daha fazla bilgi için bkz. [ARMCLIENT Benioku](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasasını silmek için Azure Resource Manager istemcisini kullanma

1. Abonelik KIMLIĞINIZI, kaynak grubu adını ve kasa adını kullanarak aşağıdaki komutu çalıştırın. Hiçbir bağımlılığı yoksa, aşağıdaki komutu çalıştırdığınızda kasa silinir:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Kasa boş değilse şu hata iletisini alırsınız: *Bu kasada mevcut kaynaklar olduğundan kasa silinemez.* Bir kasadaki korumalı bir öğeyi veya kapsayıcıyı kaldırmak için aşağıdaki komutu çalıştırın:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure portal kasanın silindiğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

[Kurtarma Hizmetleri kasaları hakkında bilgi edinin](backup-azure-recovery-services-vault-overview.md)<br/>
[Kurtarma Hizmetleri kasalarını izleme ve yönetme hakkında bilgi edinin](backup-azure-manage-windows-server.md)
