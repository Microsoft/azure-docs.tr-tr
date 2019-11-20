---
title: SAP HANA veritabanını Azure 'a yedekleme
description: Bu öğreticide, Azure Backup hizmeti ile bir SAP HANA veritabanının Azure 'da nasıl yedekleneceği açıklanmaktadır.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 519e47c6b6793c638e64c4e4bcc4fafdb678c9fb
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172736"
---
# <a name="back-up-an-sap-hana-database-to-azure"></a>SAP HANA veritabanını Azure 'a yedekleme

[Azure Backup](backup-overview.md) , SAP HANA veritabanlarının Azure 'a yedeklenmesini destekler.

> [!NOTE]
> Bu özellik şu anda genel önizleme aşamasındadır. Şu anda üretime hazırlanma ve garantili bir SLA 'Sı yok.

## <a name="scenario-support"></a>Senaryo desteği

**Destek** | **Ayrıntılar**
--- | ---
**Desteklenen coğrafyalar** | Avustralya Güney Doğu, Doğu Avustralya <br> Güney Brezilya <br> Kanada Orta, Kanada Doğu <br> Güney Doğu Asya, Doğu Asya <br> Doğu ABD, Doğu ABD 2, Orta Batı ABD, Batı ABD, Batı ABD 2, Orta Kuzey ABD, Orta ABD, Orta Güney ABD<br> Hindistan Orta, Hindistan Güney <br> Doğu Japonya, Batı Japonya<br> Kore Orta, Kore Güney <br> Kuzey Avrupa, Batı Avrupa <br> UK Güney, UK Batı
**Desteklenen VM işletim sistemleri** | SLES 12 SP2, SP3 veya SP4.
**Desteklenen HANA sürümleri** | Hana 1. x üzerinde SDC, HANA 2. x < = SPS04 Rev 43

### <a name="current-limitations"></a>Geçerli sınırlamalar

- Yalnızca Azure VM 'lerinde çalışan SAP HANA veritabanlarını yedekleyebilirsiniz.
- Yalnızca tek bir Azure VM 'de çalışan SAP HANA örneğini yedekleyebilirsiniz. Aynı Azure VM 'de birden çok HANA örneği şu anda desteklenmiyor.
- Veritabanlarını yalnızca genişleme modunda yedekleyebilirsiniz. Ölçeği genişletme, birden çok Azure sanal makinesi üzerindeki bir HANA örneği şu anda yedekleme için desteklenmiyor.
- Genişletilmiş sunucuda dinamik katmanlama ile SAP HANA örneğini yedeklenemez, yani başka bir düğümde bulunan dinamik katmanlama. Bu aslında, desteklenmeyen bir ölçeğe sahiptir.
- Aynı sunucuda dinamik katmanlama etkinken SAP HANA örneğini yedeklenemez. Dinamik katmanlama Şu anda desteklenmiyor.
- Azure portal SAP HANA yedeklemeyi yalnızca yapılandırabilirsiniz. Özellik PowerShell, CLı ile yapılandırılamaz.
- Veritabanı günlüklerini 15 dakikada bir yedekleyebilirsiniz. Günlük yedeklemeleri, veritabanı için başarılı bir tam yedekleme işlemi tamamlandıktan sonra yalnızca Flow 'a başlar.
- Tam ve fark yedeklemeleri gerçekleştirebilirsiniz. Artımlı yedekleme şu anda desteklenmiyor.
- Yedekleme ilkesini, SAP HANA yedeklemeleri için uyguladıktan sonra değiştiremezsiniz. Farklı ayarlarla yedeklemek istiyorsanız yeni bir ilke oluşturun veya farklı bir ilke atayın.
  - Yeni bir ilke oluşturmak için kasada **ilkeler** > **yedekleme ilkeleri** ' ne tıklayın >  **+** **Azure VM 'de > SAP HANA**ekleyin ve ilke ayarlarını belirtin.
  - Farklı bir ilke atamak için, veritabanını çalıştıran VM 'nin özelliklerinde geçerli ilke adına tıklayın. Ardından **yedekleme ilkesi** sayfasında, yedekleme için kullanılacak farklı bir ilke seçebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Yedeklemeleri yapılandırmadan önce aşağıdakileri yaptığınızdan emin olun:

1. SAP HANA veritabanını çalıştıran VM 'de, aşağıdaki gibi, zypper kullanarak resmi SLES paketinden/medyasından ODBC sürücü paketlerini yükleyip etkinleştirin:

    ```unix
    sudo zypper update
    sudo zypper install unixODBC
    ```

    > [!NOTE]
    > Depoları güncelleştirmediğiniz için, unixODBC sürümünün min 2.3.4 olduğundan emin olun. UniXODBC sürümünü bildirmek için ```odbcinst -j``` kök olarak çalıştırın

2. [Aşağıdaki](#set-up-network-connectivity)yordamda açıklandığı gibi, Azure 'a ULAŞABILMESI için VM 'den internet 'e bağlantıya izin verin.

3. HANA 'nın kök kullanıcı olarak yüklendiği sanal makinede ön kayıt betiğini çalıştırın. Betik, akışta [portalda](#discover-the-databases) verilmiştir ve [doğru izinleri](backup-azure-sap-hana-database-troubleshoot.md#setting-up-permissions)ayarlamak için gereklidir.

### <a name="set-up-network-connectivity"></a>Ağ bağlantısını ayarlama

Tüm işlemler için SAP HANA VM, Azure genel IP adresleri için bağlantı gerektirir. VM işlemleri (veritabanı bulma, yedeklemeleri yapılandırma, yedeklemeleri zamanlama, kurtarma noktalarını geri yükleme vb.) bağlantı olmadan çalışmaz. Azure veri merkezi IP aralıklarına erişime izin vererek bağlantı kurun: 

- Azure veri merkezleri için [IP adresi aralıklarını](https://www.microsoft.com/download/details.aspx?id=41653) indirebilir ve ardından bu IP adreslerine erişime izin verebilirsiniz.
- Ağ güvenlik grupları (NSG 'ler) kullanıyorsanız, tüm Azure genel IP adreslerine izin vermek için Azurecsesli [hizmet etiketini](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) kullanabilirsiniz. NSG kurallarını değiştirmek için [set-AzureNetworkSecurityRule cmdlet 'ini](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurenetworksecurityrule?view=azuresmps-4.0.0) kullanabilirsiniz.
- 443 bağlantı noktası, aktarım HTTPS üzerinden olduğundan beyaz listeye eklenmelidir.

## <a name="onboard-to-the-public-preview"></a>Genel önizlemeye ekleme

Genel önizlemeye aşağıdaki şekilde katılın:

- Portalda, [Bu makaleyi izleyerek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)abonelik kimliğinizi Kurtarma Hizmetleri hizmet sağlayıcısına kaydedin. 
- PowerShell için bu cmdlet 'i çalıştırın. "Kayıtlı" olarak tamamlanmalıdır.

    ```powershell
    PS C:>  Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Veritabanlarını bulma

1. Kasadaki **Başlarken**' de, **Yedekle**' ye tıklayın. **İş yükünüz nerede çalışıyor?** , **Azure VM 'de SAP HANA**' yi seçin.
2. **Bulmayı Başlat**' a tıklayın. Bu işlem, kasa bölgesinde korunmayan Linux VM 'lerinin bulunmasını başlatır.

   - Bulma sonrasında, korumasız VM 'Ler ad ve kaynak grubuna göre listelenmiş şekilde portalda görüntülenir.
   - Bir VM beklendiği gibi listelenmiyorsa, bir kasada zaten yedeklenmiş olup olmadığını kontrol edin.
   - Birden çok VM aynı ada sahip olabilir, ancak bunlar farklı kaynak gruplarına aittir.

3. **Sanal makineler Seç**bölümünde, veritabanı bulma Için SAP HANA VM 'lerine erişmek üzere Azure Backup hizmetinin izinlerini sağlayan betiği indirmek için bağlantıya tıklayın
4. Betiği, yedeklemek istediğiniz her bir sanal makine barındırma SAP HANA veritabanları üzerinde çalıştırın.
5. VM 'lerde betiği çalıştırdıktan sonra, **sanal makineleri seçin**' de VM 'ler ' i seçin. Ardından veritabanlarını **bul**' a tıklayın.
6. Azure Backup VM 'deki tüm SAP HANA veritabanlarını bulur. Bulma sırasında Azure Backup, VM 'yi kasayla kaydeder ve VM 'ye bir uzantı kurar. Veritabanına hiçbir aracı yüklü değil.

    ![SAP HANA veritabanlarını bul](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Yedeklemeyi yapılandırma  

Şimdi yedeklemeyi etkinleştirin.

1. 2\. adımda **yedeklemeyi Yapılandır**' a tıklayın.
2. **Yedeklenecek öğeleri seçin**' de, korumak istediğiniz tüm veritabanlarını seçin > **Tamam**' a tıklayın.
3. **Yedekleme ilkesi** > **yedekleme Ilkesi**' ni seçin ve aşağıdaki yönergelere uygun olarak veritabanları için yeni bir yedekleme ilkesi oluşturun.
4. İlkeyi oluşturduktan sonra **yedekleme** menüsünde **yedeklemeyi etkinleştir**' e tıklayın.
5. Yedekleme Yapılandırma ilerlemesini portalın **Bildirimler** alanında izleyin.

### <a name="create-a-backup-policy"></a>Yedekleme ilkesi oluşturma

Yedekleme ilkesi, yedeklemelerin ne zaman alındığını ve ne kadar süreyle korunduğunu tanımlar.

- Kasa düzeyinde bir ilke oluşturulur.
- Birden çok kasa aynı yedekleme ilkesini kullanabilir, ancak yedekleme ilkesini her kasaya uygulamanız gerekir.

İlke ayarlarını aşağıdaki gibi belirtin:

1. **İlke adı**alanına yeni ilke için bir ad girin.
2. **Tam yedekleme ilkesinde**, bir **yedekleme sıklığı**seçin, **günlük** veya **haftalık**' ı seçin.
   - **Günlük**: yedekleme işinin başladığı saat ve saat dilimini seçin.
   
       - Tam yedekleme çalıştırmanız gerekir. Bu seçeneği devre dışı bırakabilirsiniz.
       - İlkeyi görüntülemek için **tam yedekleme** ' ye tıklayın.
       - Günlük tam yedeklemeler için fark yedeklemeleri oluşturamazsınız.
       
   - **Haftalık**: yedekleme işinin çalıştığı haftanın gününü, saatini ve saat dilimini seçin.
3. **Bekletme aralığı**' nda, tam yedekleme için bekletme ayarlarını yapılandırın.
    - Varsayılan olarak tüm seçenekler seçilidir. Kullanmak istemediğiniz tüm Bekletme aralığı sınırlarını temizleyin ve yaptığınız değişiklikleri ayarlayın.
    - Herhangi bir yedekleme türü için en düşük saklama süresi (tam/fark/günlük) yedi gündür.
    - Kurtarma noktaları, bekletme aralığına göre bekletme için etiketlenir. Örneğin, günlük tam yedekleme seçerseniz, her gün yalnızca bir tam yedekleme tetiklenir.
    - Belirli bir günün yedeklemesi, haftalık bekletme aralığına ve ayarına göre etiketlenebilir ve korunur.
    - Aylık ve yıllık bekletme aralıkları benzer bir şekilde davranır.

4. **Tam yedekleme ilkesi** menüsünde, ayarları kabul etmek için **Tamam** ' ı tıklatın.
5. Fark ilkesi eklemek için değişiklik **yedeklemesi** ' ni seçin.
6. **Değişiklik yedekleme ilkesinde**, sıklık ve bekletme denetimlerini açmak için **Etkinleştir** ' i seçin.
    - En çok, her gün bir değişiklik yedeklemesi tetikleyebilirsiniz.
    - Değişiklik yedeklemeleri, en fazla 180 gün boyunca korunabilir. Daha uzun bekletme yapmanız gerekiyorsa tam yedeklemeler kullanmanız gerekir.

    > [!NOTE]
    > Artımlı yedeklemeler Şu anda desteklenmiyor. 

7. İlkeyi kaydetmek ve ana **yedekleme ilkesi** menüsüne dönmek için **Tamam** ' ı tıklatın.
8. İşlem günlüğü yedekleme ilkesi eklemek için **günlük yedeklemesi** ' ni seçin,
    - **Günlük yedeklemesi**' nde **Etkinleştir**' i seçin.
    - Sıklık ve bekletme denetimlerini ayarlayın.

    > [!NOTE]
    > Günlük yedeklemeleri, yalnızca başarılı bir tam yedekleme tamamlandıktan sonra Flow 'a başlar.

9. İlkeyi kaydetmek ve ana **yedekleme ilkesi** menüsüne dönmek için **Tamam** ' ı tıklatın.
10. Yedekleme ilkesini tanımlamayı tamamladıktan sonra, **Tamam**' a tıklayın.


## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

Yedeklemeler, ilke zamanlamasına uygun olarak çalışır. İsteğe bağlı bir yedeklemeyi aşağıdaki gibi çalıştırabilirsiniz:


1. Kasa menüsünde, **yedekleme öğeleri**' ne tıklayın.
2. **Yedekleme öğeleri**' nde, SAP HANA VERITABANıNı çalıştıran VM 'yi seçin ve **Şimdi Yedekle**' ye tıklayın.
3. **Şimdi Yedekle**' de, kurtarma noktasının tutulacağı son günü seçmek için Takvim denetimini kullanın. Daha sonra, **Tamam**'a tıklayın.
4. Portal bildirimlerini izleyin. İş ilerlemesini kasa panosunda izleyebilirsiniz > **yedekleme işleri** > devam ediyor. Veritabanınızın boyutuna bağlı olarak, ilk yedeklemenin oluşturulması biraz zaman alabilir.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup etkin bir veritabanında SAP HANA Studio yedeklemesini çalıştırma

Azure Backup ile yedeklenen bir veritabanının yerel yedeklemesini (HANA Studio 'Yu kullanarak) almak istiyorsanız aşağıdakileri yapın:

1. Veritabanının tamamlaması için herhangi bir tam veya günlük yedeklemesi bekleyin. SAP HANA Studio 'daki durumu denetleyin.
2. Günlük yedeklemelerini devre dışı bırakın ve ilgili veritabanı için yedekleme kataloğunu dosya sistemine ayarlayın.
3. Bunu yapmak için **SystemDB** > **yapılandırması** ' na çift tıklayın > veritabanı > **filtresi (günlük)** **seçeneğini belirleyin** .
4. **Enable_auto_log_backup** **Hayır**olarak ayarlayın.
5. **Log_backup_using_backint** **false**olarak ayarlayın.
6. Veritabanının geçici bir tam yedeklemesini alın.
7. Tam yedekleme ve Katalog yedeklemesinin bitmesini bekleyin.
8. Önceki ayarları Azure için geri döndürür:
    - **Enable_auto_log_backup** **Evet**olarak ayarlayın.
    - **Log_backup_using_backint** **true**olarak ayarlayın.


## <a name="upgrading-protected-10-dbs-to-20"></a>Protected 1,0 DBs 'yi 2,0 olarak yükseltme

SAP HANA 1,0 DBs 'yi koruyorsanız ve 2,0 ' ye yükseltmek istiyorsanız, aşağıda özetlenen adımları gerçekleştirin.

- ESKI SDC DB için verileri koru ile korumayı durdurun.
- Ön kayıt betiğini (SID ve MDC) doğru ayrıntılarla yeniden çalıştırın. 
- Uzantıyı yeniden Kaydet (yedekleme-> Görünümü ayrıntıları-> ilgili Azure VM 'yi > yeniden Kaydet ' i seçin). 
- Aynı VM için veritabanlarını yeniden keşfet ' e tıklayın. Bu, 2. adımdaki yeni DBs 'Leri doğru ayrıntılarla (SDC değil, SYSTEMDB ve kiracı DB) göstermelidir. 
- Bu yeni veritabanlarını koruyun.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM 'lerinde SAP HANA yedekleme kullanırken sık karşılaşılan hataların nasıl giderileceği [hakkında bilgi edinin](backup-azure-sap-hana-database-troubleshoot.md) .
Azure VM 'lerini yedekleme [hakkında bilgi edinin](backup-azure-arm-vms-prepare.md) .
