---
title: Microsoft Azure Kurtarma Hizmetleri kasasını silme
description: Bu makalede, bağımlılıkları kaldırmayı ve sonra bir Azure Backup Recovery Services kasasını silmeyi öğrenin.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: bb6be070ac0fb408ac37c8ae7b003b54da5d6dea
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773666"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Azure Backup Recovery Services kasasını silme

Bu makalede [Azure Backup](backup-overview.md) kurtarma hizmetleri kasasının nasıl silineceği açıklanır. Bağımlılıkları kaldırmak ve ardından kasayı silmek için yönergeler içerir.

## <a name="before-you-start"></a>Başlamadan önce

Aşağıdaki bağımlılıklara sahip Kurtarma Hizmetleri kasalarını silemezsiniz:

- Korumalı veri kaynakları (örneğin IaaS VM 'Leri, SQL veritabanları, Azure dosya paylaşımları) içeren bir kasayı silemezsiniz.
- Yedekleme verileri içeren bir kasayı silemezsiniz. Yedekleme verileri silindikten sonra geçici silme durumuna geçer.
- Geçici olarak silinen durumunda yedekleme verileri içeren bir kasayı silemezsiniz.
- Kayıtlı depolama hesapları olan bir kasayı silemezsiniz.

Bağımlılıkları kaldırmadan kasayı silmeye çalışırsanız, aşağıdaki hata iletilerinden biri ile karşılaşırsınız:

- Kasanın içinde mevcut kaynaklar bulunduğundan kasa silinemiyor. Lütfen bu kasayla ilişkili yedekleme öğesi, korumalı sunucu veya yedek yönetim sunucusu olmadığından emin olun. Silmeye devam etmeden önce bu kasayla ilişkilendirilen aşağıdaki kapsayıcıların kaydını silin.

- Kurtarma Hizmetleri kasasında geçici silme durumunda yedek öğeler bulunduğundan kasa silinemiyor. Geçici olarak silinen öğeler, silme işleminin 14 gün sonra kalıcı olarak silinir. Lütfen yedekleme öğeleri kalıcı olarak silindikten sonra kasadaki geçici olarak silinen durumda hiçbir öğe yoksa kasa silme işlemini deneyin. Daha fazla bilgi için bkz. [Azure Backup Için geçici silme](./backup-azure-security-feature-cloud.md).

## <a name="proper-way-to-delete-a-vault"></a>Kasayı silmenin doğru yolu

>[!WARNING]
>Aşağıdaki işlem bozucu olduğundan geri alınamaz. Korunan sunucuyla ilişkili tüm yedekleme verileri ve yedekleme öğeleri kalıcı olarak silinecek. Dikkatli olun.

Bir kasayı doğru bir şekilde silmek için şu sırada bulunan adımları izlemeniz gerekir:

- **1. adım**: geçici silme özelliğini devre dışı bırakın. Geçici silme devre dışı bırakma adımları için [buraya bakın](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) .

- **2. adım**: geçici silmeyi devre dışı bıraktıktan sonra, daha önce geçici olarak silinen durumda kalan herhangi bir öğe olup olmadığını denetleyin. Geçici olarak silinen durumda öğeler varsa, bunları *geri* ve *silmeniz* gerekir. Geçici silme öğelerini bulmak ve bunları kalıcı olarak silmek için [aşağıdaki adımları izleyin](./backup-azure-security-feature-cloud.md#permanently-deleting-soft-deleted-backup-items) .

- **3. adım**: herhangi bir korumalı öğe olup olmadığını doğrulamak için aşağıdaki üç yeri denetlemeniz gerekir:

  - **Bulut korumalı öğeler**: **yedekleme öğeleri**> kasa panosu menüsüne gidin. Burada listelenen tüm öğeler, yedeklemeyi **durdurma** veya yedekleme verilerini yedekleme verileriyle birlikte **silme** ile kaldırılmalıdır.  Bu öğeleri kaldırmak için [aşağıdaki adımları izleyin](#delete-protected-items-in-the-cloud) .
  - **SQL Server örneği**: **Yedekleme altyapısı**  >  **Korumalı sunucuları**> kasa panosu menüsüne gidin. Korumalı sunucular ' da, kaydı kaldırmak istediğiniz sunucuyu seçin. Kasayı silmek için tüm sunucuların kaydını kaldırmanız gerekir. Korumalı sunucuya sağ tıklayın ve **kayıt kaldır**' ı seçin.
  - **Mars korumalı sunucular**: **Yedekleme altyapısı**  >  **Korumalı sunucuları**> kasa panosu menüsüne gidin. MARS korumalı sunucularınız varsa burada listelenen tüm öğelerin, yedekleme verileriyle birlikte silinmesi gerekir. MARS korumalı sunucularını silmek için [Bu adımları izleyin](#delete-protected-items-on-premises) .
  - **Mabs veya DPM Yönetim sunucuları**: **Yedekleme altyapısı**  >  **yedekleme yönetim sunucuları**> kasa panosu menüsüne gidin. DPM veya Azure Backup Sunucusu (MABS) varsa, burada listelenen tüm öğelerin, yedekleme verileriyle birlikte silinmesi veya kaydı kaldırılmalıdır. Yönetim sunucularını silmek için [aşağıdaki adımları izleyin](#delete-protected-items-on-premises) .

- **4. adım**: tüm kayıtlı depolama hesaplarının silindiğinden emin olmanız gerekir. **Yedekleme altyapısı**  >  **depolama hesaplarına**> kasa panosu menüsüne gidin. Burada listelenen depolama hesaplarınız varsa, bunların tümünün kaydını silmeniz gerekir. Hesabın kaydını silme hakkında bilgi edinmek için bkz. [depolama hesabının kaydını silme](manage-afs-backup.md#unregister-a-storage-account).
- **5. adım**: kasa Için oluşturulmuş özel uç nokta olmadığından emin olun. Kasa Pano menüsüne git ' ayarlar > ' altındaki **Özel uç nokta bağlantıları** > kasalarda oluşturulmuş veya oluşturmaya çalışılan özel uç nokta bağlantıları varsa, kasa silme işlemine devam etmeden önce bunların kaldırıldığından emin olun. 

Bu adımları tamamladıktan sonra [kasayı silmeye](#delete-the-recovery-services-vault)devam edebilirsiniz.

Şirket içinde veya bulutta korumalı öğeleriniz yoksa ancak hala kasa silme hatası alıyorsanız, [Azure Resource Manager kullanarak kurtarma hizmetleri kasasını silme](#delete-the-recovery-services-vault-by-using-azure-resource-manager) adımlarını gerçekleştirin.

## <a name="delete-protected-items-in-the-cloud"></a>Bulutta korunan öğeleri silme

İlk olarak, bağımlılıklar ve kasa silme işlemini anlamak için **[başlamadan önce](#before-you-start)** bölümünü okuyun.

Korumayı durdurmak ve yedekleme verilerini silmek için aşağıdaki adımları gerçekleştirin:

1. Portaldan **Kurtarma Hizmetleri Kasası**' na gidin ve ardından **yedekleme öğeleri**' ne gidin. Ardından, **yedekleme yönetimi türü** listesinde, bulutta korunan öğeleri (örneğin, Azure sanal makineleri, Azure depolama [Azure dosyaları hizmeti] veya Azure sanal makinelerinde SQL Server) seçin.

    ![Yedekleme türünü seçin.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Kategori için tüm öğelerin bir listesini görürsünüz. Yedekleme öğesini seçmek için sağ tıklayın. Yedekleme öğesinin korumalı olup olmadığına bağlı olarak, menü **Yedeklemeyi Durdur** bölmesini veya **yedekleme verilerini sil** bölmesini görüntüler.

    - **Yedeklemeyi Durdur** bölmesi görünürse, açılan menüden **yedekleme verilerini sil** ' i seçin. Yedekleme öğesinin adını girin (Bu alan büyük/küçük harfe duyarlıdır) ve ardından açılan menüden bir neden seçin. Varsa yorumlarınızı girin. Ardından, **Yedeklemeyi Durdur**' u seçin.

        ![Yedeklemeyi Durdur bölmesi.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - **Yedekleme verilerini sil** bölmesi görüntülenirse, yedekleme öğesinin adını girin (Bu alan büyük/küçük harfe duyarlıdır) ve ardından açılan menüden bir neden seçin. Varsa yorumlarınızı girin. Ardından **Sil**' i seçin.

         ![Yedekleme verilerini sil bölmesi.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

   Bu seçenek, zamanlanmış yedeklemeleri siler ve isteğe bağlı yedeklemeleri de siler.
3. **Bildirim simgesine** bakın: ![ bildirim simgesi.](./media/backup-azure-delete-vault/messages.png) İşlem tamamlandıktan sonra hizmet şu iletiyi görüntüler: *yedekleme durduruluyor ve "yedekleme öğesi" için yedekleme verileri siliniyor*. *Işlem başarıyla tamamlandı*.
4. Yedekleme öğesinin silindiğinden emin olmak için **yedekleme öğeleri** menüsünde **Yenile** ' yi seçin.

      ![Yedekleme öğelerini silme sayfası.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Şirket içi korumalı öğeleri sil

İlk olarak, bağımlılıklar ve kasa silme işlemini anlamak için **[başlamadan önce](#before-you-start)** bölümünü okuyun.

1. Kasa panosu menüsünden **Yedekleme altyapısı**' nı seçin.
2. Şirket içi senaryonuza bağlı olarak, aşağıdaki seçeneklerden birini seçin:

      - MARS için **korumalı sunucular** ' ı seçin ve ardından  **Aracı Azure Backup**. Ardından, silmek istediğiniz sunucuyu seçin.

        ![MARS için, kendi panosunu açmak için kasanızı seçin.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - MABS veya DPM için **yedekleme yönetim sunucuları**' nı seçin. Ardından, silmek istediğiniz sunucuyu seçin.

          ![MABS veya DPM için, kendi panosunu açmak için kasanızı seçin.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. **Silme** bölmesi bir uyarı iletisiyle birlikte görüntülenir.

     ![Silme bölmesi.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Uyarı iletisini ve onay onay kutusundaki yönergeleri gözden geçirin.
    > [!NOTE]
    >
    >- Korumalı sunucu Azure hizmetleri ile eşitlenmişse ve yedekleme öğeleri mevcutsa, izin onay kutusu bağımlı yedekleme öğelerinin sayısını ve yedekleme öğelerini görüntülemek için bağlantıyı görüntüler.
    >- Korumalı sunucu Azure hizmetleriyle eşitlenmemişse ve yedekleme öğeleri mevcutsa, izin onay kutusu yalnızca yedekleme öğelerinin sayısını görüntüler.
    >- Yedekleme öğesi yoksa, izin onay kutusu silmeyi ister.

4. Onay onay kutusunu seçin ve **Sil**' i seçin.

5. Yedekleme verilerini silme **bildirim** simgesine bakın ![ ](./media/backup-azure-delete-vault/messages.png) . İşlem tamamlandıktan sonra hizmet şu iletiyi görüntüler: *yedekleme durduruluyor ve "yedekleme öğesi" için yedekleme verilerini silme.* *Işlem başarıyla tamamlandı*.
6. Yedekleme öğesinin silindiğinden emin olmak için **yedekleme öğeleri** menüsünde **Yenile** ' yi seçin.

>[!NOTE]
>Bağımlılıklar içeren bir portaldan şirket içi korumalı öğeyi silerseniz, "sunucu kaydını silme işlemi bozucu bir işlemdir ve geri alınamaz" uyarısı alırsınız. Tüm yedekleme verileri (verileri geri yüklemek için gereken kurtarma noktaları) ve korunan sunucuyla ilişkili yedekleme öğeleri kalıcı olarak silinir. "

Bu işlem tamamlandıktan sonra, yedekleme öğelerini yönetim konsolundan silebilirsiniz:

- [MARS yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mars-management-console)
- [MABS veya DPM Yönetim konsolundan yedekleme öğelerini silme](#delete-backup-items-from-the-mabs-or-dpm-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>MARS yönetim konsolundan yedekleme öğelerini silme

>[!NOTE]
>Yedeklemeyi durdurmadan kaynak makineyi sildiyseniz veya kaybettiyseniz, sonraki zamanlanmış yedekleme başarısız olur. Eski kurtarma noktası ilkeye göre sona erer, ancak yedeklemeyi durdurup verileri silinceye kadar son tek kurtarma noktası her zaman tutulur. Bunu, [Bu bölümdeki](#delete-protected-items-on-premises)adımları izleyerek yapabilirsiniz.

1. MARS yönetim konsolunu açın, **Eylemler** bölmesine gidin ve **yedeklemeyi zamanla**' yı seçin.
2. **Zamanlanmış bir yedeklemeyi Değiştir veya Durdur** sayfasında, **Bu yedekleme zamanlamasını kullanarak Durdur ' u seçin ve tüm depolanan yedeklemeleri silin**. Ardından **İleri**' yi seçin.

    ![Zamanlanmış bir yedeklemeyi değiştirin veya durdurun.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. **Zamanlanan yedeklemeyi durdur** sayfasında **son**' u seçin.

    ![Zamanlanmış bir yedeklemeyi durdurun.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. El ile oluşturmanız gereken bir güvenlik PIN 'ı (kişisel kimlik numarası) girmeniz istenir. Bunu yapmak için öncelikle Azure portal oturum açın.
5. **Kurtarma Hizmetleri Kasası**  >  **ayarları**  >  **özellikleri**' ne gidin.
6. **GÜVENLIK PIN**'ı altında **Oluştur**' u seçin. Bu PIN 'ı kopyalayın. PIN yalnızca beş dakika için geçerlidir.
7. Yönetim konsolunda PIN 'i yapıştırın ve **Tamam**' ı seçin.

    ![Güvenlik PIN 'ı oluştur.](./media/backup-azure-delete-vault/security-pin.png)

8. **Yedekleme Ilerlemesini Değiştir** sayfasında aşağıdaki ileti görüntülenir: *silinen yedekleme verileri 14 gün boyunca saklanacaktır. Bu süreden sonra, yedekleme verileri kalıcı olarak silinir.*  

    ![Yedekleme altyapısını silin.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Şirket içi yedekleme öğelerini sildikten sonra, portaldan sonraki adımları izleyin.

### <a name="delete-backup-items-from-the-mabs-or-dpm-management-console"></a>MABS veya DPM Yönetim konsolundan yedekleme öğelerini silme

>[!NOTE]
>Yedeklemeyi durdurmadan kaynak makineyi sildiyseniz veya kaybettiyseniz, sonraki zamanlanmış yedekleme başarısız olur. Eski kurtarma noktası ilkeye göre sona erer, ancak yedeklemeyi durdurup verileri silinceye kadar son tek kurtarma noktası her zaman tutulur. Bunu, [Bu bölümdeki](#delete-protected-items-on-premises)adımları izleyerek yapabilirsiniz.

MABS veya DPM Yönetim konsolundan yedekleme öğelerini silmek için kullanabileceğiniz iki yöntem vardır.

#### <a name="method-1"></a>1. Yöntem

Korumayı durdurmak ve yedekleme verilerini silmek için aşağıdaki adımları uygulayın:

1. DPM Yönetici Konsolu açın ve ardından Gezinti çubuğunda **koruma** ' yı seçin.
2. Görüntü bölmesinde, kaldırmak istediğiniz koruma grubu üyesini seçin. **Grup üyelerinin korumasını durdur** seçeneğini belirlemek için sağ tıklayın.
3. **Korumayı Durdur** iletişim kutusunda **korunan verileri Sil**' i seçin ve ardından **depolamayı çevrimiçi Sil** onay kutusunu seçin. Ardından, **Korumayı Durdur**' u seçin.

    ![Korumayı Durdur bölmesinden korumalı verileri Sil ' i seçin.](./media/backup-azure-delete-vault/delete-storage-online.png)

    Korumalı üye durumu, *etkin olmayan çoğaltma* olarak değişir.

4. Etkin olmayan koruma grubuna sağ tıklayın ve **etkin olmayan korumayı kaldır**' ı seçin.

    ![Etkin olmayan korumayı kaldırın.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. **Etkin olmayan korumayı Sil** penceresinde, **çevrimiçi depolamayı sil** onay kutusunu seçin ve ardından **Tamam**' ı seçin.

    ![Çevrimiçi depolamayı silin.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>2. Yöntem

**Mabs yönetimi** veya **DPM Yönetim** konsolunu açın. **Veri koruma yöntemini seçin** altında, **çevrimiçi koruma** istiyorum onay kutusunu temizleyin.

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

  Azure Backup korumalı bir öğe için korumayı devre dışı bırakma hakkında [daha fazla bilgi edinin](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) .

- Buluttaki tüm yedekleme korumalı öğelerin korumasını durdurun ve verileri silin (örneğin, IaaS VM, Azure dosya paylaşma vb.):

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

    [Daha fazla bilgi](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection)   hakkında, yedekleme korumalı bir öğe için korumayı devre dışı bırakır.

- Azure 'a yedekleme Azure Backup Aracısı (MARS) kullanılarak korunan şirket içi dosyalar ve klasörler için, her bir MARS PowerShell modülündeki yedeklenen verileri silmek için aşağıdaki PowerShell komutunu kullanın:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Daha sonra aşağıdaki istem görüntülenir:

    *Microsoft Azure Backup bu yedekleme ilkesini kaldırmak istediğinizden emin misiniz? Silinen yedekleme verileri 14 gün boyunca tutulacaktır. Bu süreden sonra, yedekleme verileri kalıcı olarak silinir. <br/> [Y] Evet [A] Evet [A] Evet [A] Hayır [N] tüm [S] askıya alma [?] Yardım (varsayılan: "Y"):*

- MABS (Microsoft Azure Backup Server) veya DPM (System Center Data Protection Manager) ile Azure 'a korunan şirket içi makineler için, Azure 'daki yedeklenen verileri silmek üzere aşağıdaki komutu kullanın.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Daha sonra aşağıdaki istem görüntülenir:

   *Microsoft Azure Backup* Bu yedekleme ilkesini kaldırmak istediğinizden emin misiniz? Silinen yedekleme verileri 14 gün boyunca tutulacaktır. Bu süre geçtikten sonra yedekleme verileri kalıcı olarak silinir. <br/>
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

    Bir Windows sunucusu veya başka bir kapsayıcının kasadan kaydını kaldırma hakkında [daha fazla bilgi edinin](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) .

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

    Bir yedekleme yönetimi kapsayıcısının kasadan kaydını kaldırma hakkında [daha fazla bilgi edinin](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) .

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

Kurtarma Hizmetleri kasasını silme hakkında [daha fazla bilgi edinin](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) .

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>CLı kullanarak kurtarma hizmetleri kasasını silme

İlk olarak, bağımlılıklar ve kasa silme işlemini anlamak için **[başlamadan önce](#before-you-start)** bölümünü okuyun.

> [!NOTE]
> Şu anda Azure Backup CLı yalnızca Azure VM yedeklemelerini yönetmeyi destekler, bu nedenle kasayı silmek için aşağıdaki komut yalnızca kasa Azure VM yedeklemeleri içeriyorsa işe yarar. Kasa, Azure VM 'lerinden farklı türde herhangi bir yedekleme öğesi içeriyorsa Azure Backup CLı kullanarak bir kasayı silemezsiniz.

Mevcut kurtarma hizmetleri kasasını silmek için aşağıdaki adımları gerçekleştirin:

- Korumayı durdurmak ve yedekleme verilerini silmek için

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Daha fazla bilgi için bu [makaleye](/cli/azure/backup/protection#az_backup_protection_disable)bakın.

- Var olan bir kurtarma hizmetleri kasasını silme:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Daha fazla bilgi için bu [makaleye](/cli/azure/backup/vault) bakın

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Kurtarma Hizmetleri kasasını Azure Resource Manager kullanarak silme

Kurtarma Hizmetleri kasasını silmek için bu seçenek yalnızca tüm bağımlılıkların kaldırılması ve *kasa silme hatasını* almaya devam ediyorsanız önerilir. Aşağıdaki ipuçlarından birini veya tümünü deneyin:

- Kasa menüsündeki **temel** bileşenler bölmesinden hiçbir yedekleme öğesi, yedekleme yönetim sunucusu veya yinelenen öğe olmadığını doğrulayın. Yedekleme öğeleri varsa, [başlamadan önce](#before-you-start) bölümüne bakın.
- [Kasayı portaldan silmeyi](#delete-the-recovery-services-vault) yeniden deneyin.
- Tüm bağımlılıklar kaldırılırsa ve yine de *kasa silme hatası* alıyorsanız, aşağıdaki adımları gerçekleştirmek Için ARMClient aracını kullanın (NotDan sonra).

1. Chocolatey indirmek ve yüklemek için [Chocolatey.org](https://chocolatey.org/) adresine gidin. Daha sonra, aşağıdaki komutu çalıştırarak ARMClient 'ı yüklemelisiniz:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Azure hesabınızda oturum açın ve ardından şu komutu çalıştırın:

    `ARMClient.exe login [environment name]`

3. Azure portal, silmek istediğiniz kasasının abonelik KIMLIĞINI ve kaynak grubu adını toplayın.

ARMClient komutu hakkında daha fazla bilgi için bkz. [ARMCLIENT Benioku](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasasını silmek için Azure Resource Manager istemcisini kullanma

1. Abonelik KIMLIĞINIZI, kaynak grubu adını ve kasa adını kullanarak aşağıdaki komutu çalıştırın. Hiçbir bağımlılığı yoksa, aşağıdaki komutu çalıştırdığınızda kasa silinir:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>?api-version=2015-03-15
   ```

2. Kasa boş değilse şu hata iletisini alırsınız: *Bu kasada mevcut kaynaklar olduğundan kasa silinemez.* Bir kasadaki korumalı bir öğeyi veya kapsayıcıyı kaldırmak için aşağıdaki komutu çalıştırın:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<Recovery Services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. Azure portal kasanın silindiğinden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

[Kurtarma Hizmetleri kasaları](backup-azure-recovery-services-vault-overview.md) 
 hakkında bilgi edinin [Kurtarma Hizmetleri kasalarını izleme ve yönetme hakkında bilgi edinin](backup-azure-manage-windows-server.md)
