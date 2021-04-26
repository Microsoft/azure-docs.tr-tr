---
title: Kasadan birden çok SQL Server VM yedekleme
description: Bu makalede, kurtarma hizmetleri kasasından Azure Backup sahip Azure sanal makinelerinde SQL Server veritabanlarını nasıl yedekleyeceğinizi öğrenin
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c03b833be6c5e4c352125f31ad8c5ed072674b49
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258478"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Birden çok SQL Server VM 'yi kurtarma hizmetleri kasasından yedekleme

SQL Server veritabanları, düşük kurtarma noktası hedefi (RPO) ve uzun süreli saklama gerektiren kritik iş yükleridir. [Azure Backup](backup-overview.md)kullanarak Azure sanal makinelerinde (VM) çalışan SQL Server veritabanlarını yedekleyebilirsiniz.

Bu makalede, bir Azure VM üzerinde çalışan SQL Server bir veritabanını Azure Backup kurtarma hizmetleri kasasına nasıl yedekleyeceğiniz gösterilmektedir.

Bu makalede şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
>
> * Kasa oluşturma ve yapılandırma.
> * Veritabanları bulun ve yedeklemeleri ayarlayın.
> * Veritabanları için otomatik korumayı ayarlama.

## <a name="prerequisites"></a>Önkoşullar

Bir SQL Server veritabanını yedeklemek için aşağıdaki ölçütleri kontrol edin:

1. SQL Server örneğini barındıran VM ile aynı bölgede ve abonelikte bir [Kurtarma Hizmetleri Kasası](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) oluşturun veya oluşturun.
1. VM 'nin [ağ bağlantısı](backup-sql-server-database-azure-vms.md#establish-network-connectivity)olduğunu doğrulayın.
1. SQL Server veritabanlarının [Azure Backup için veritabanı adlandırma yönergelerini](#database-naming-guidelines-for-azure-backup)izlediğinden emin olun.
1. SQL Server VM adının ve kaynak grubu adının Birleşik uzunluğunun Azure Resource Manager VM 'Ler için 84 karakteri (veya klasik VM 'Ler için 77 karakter) aşmadığından emin olun. Bu sınırlama, bazı karakterlerin hizmet tarafından ayrıldığı bir kısıtlamadır.
1. Veritabanı için etkinleştirilmiş başka bir yedekleme çözümünden emin olup olmadığınızı denetleyin. Veritabanını kapatmadan önce diğer tüm SQL Server yedeklemelerini devre dışı bırakın.
1. SQL Server 2008 R2 veya SQL Server 2012 kullanırken, [burada](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of)açıklandığı gibi yedekleme için saat dilimi sorunuyla karşılaşabilirsiniz. Yukarıda açıklanan saat dilimiyle ilgili sorunu önlemek için en son toplu güncelleştirmeler üzerinde olduğunuzdan emin olun. Güncelleştirmeleri Azure VM 'deki SQL Server örneğine uygulamak uygun değilse, sanal makinedeki saat dilimi için yaz kaydetme süresini (DST) devre dışı bırakın.

> [!NOTE]
> Azure VM için Azure Backup ve ayrıca çakışma olmadan VM üzerinde çalışan bir SQL Server veritabanı için etkinleştirebilirsiniz.

### <a name="establish-network-connectivity"></a>Ağ bağlantısını kurma

Tüm işlemler için bir SQL Server VM Azure Backup hizmeti, Azure depolama ve Azure Active Directory bağlantı gerektirir. Bu, Özel uç noktalar kullanılarak veya gerekli genel IP adreslerine veya FQDN 'lere erişim izni vererek elde edilebilir. Gerekli Azure hizmetlerine doğru bağlantının yapılmasına izin verilmemesi, veritabanı bulma, yedeklemeyi yapılandırma, yedeklemeleri gerçekleştirme ve verileri geri yükleme gibi işlemlerde hata oluşmasına yol açabilir.

Aşağıdaki tabloda bağlantı kurmak için kullanabileceğiniz çeşitli alternatifler listelenmektedir:

| **Seçenek**                        | **Avantajlar**                                               | **Dezavantajlar**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Özel uç noktalar                 | Sanal ağ içindeki özel IP 'Lerde yedeklemelere izin ver  <br><br>   Ağ ve kasa tarafında ayrıntılı denetim sağlama | Standart özel uç nokta [maliyetleri](https://azure.microsoft.com/pricing/details/private-link/) |
| NSG hizmet etiketleri                  | Aralık değişikliklerinin otomatik olarak birleştirilmesi için daha kolay yönetilmesi   <br><br>   Ek maliyet yok | Yalnızca NSG 'ler ile kullanılabilir  <br><br>    Hizmetin tamamına erişim sağlar |
| Azure Güvenlik Duvarı FQDN etiketleri          | Gerekli FQDN 'Ler otomatik olarak yönetildiğinden bu yana yönetilmesi daha kolay | Yalnızca Azure Güvenlik Duvarı ile kullanılabilir                         |
| Hizmet FQDN/IP 'lerine erişime izin ver | Ek maliyet yok   <br><br>  Tüm ağ güvenliği araçları ve güvenlik duvarları ile birlikte geçerlidir | Geniş bir IP veya FQDN kümesine erişilmesi gerekebilir   |
| HTTP ara sunucusu kullanma                 | VM 'lere tek bir internet erişimi noktası                       | Proxy yazılımıyla VM çalıştırmak için ek maliyetler         |

Bu seçenekleri kullanma hakkında daha fazla ayrıntı aşağıdaki şekilde paylaşılır:

#### <a name="private-endpoints"></a>Özel uç noktalar

Özel uç noktalar, bir sanal ağ içindeki sunuculardan kurtarma hizmetleri kasanıza güvenli bir şekilde bağlanmanıza olanak tanır. Özel uç nokta, kasanızın VNET adres alanından bir IP kullanır. Sanal ağın ve kasadaki kaynaklarınız arasındaki ağ trafiği, sanal ağınız üzerinden ve Microsoft omurga ağında özel bir bağlantı üzerinden dolaşır. Bu, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır. [Azure Backup için](./private-endpoints.md)özel uç noktalar hakkında daha fazla bilgi edinin.

#### <a name="nsg-tags"></a>NSG etiketleri

Ağ güvenlik grupları (NSG) kullanıyorsanız, Azure Backup giden erişime izin vermek için *AzureBackup* Service etiketini kullanın. Azure Backup etiketine ek olarak, Azure AD (*AzureActiveDirectory*) ve Azure depolama (*depolama*) için benzer [NSG kuralları](../virtual-network/network-security-groups-overview.md#service-tags) oluşturarak kimlik doğrulama ve veri aktarımı için de bağlantıya izin vermeniz gerekir.  Aşağıdaki adımlar Azure Backup etiketi için bir kural oluşturma işlemini anlatmaktadır:

1. **Tüm hizmetler**' de **ağ güvenlik grupları** ' na gidin ve ağ güvenlik grubunu seçin.

1. **Ayarlar** altında **giden güvenlik kuralları** ' nı seçin.

1. **Add (Ekle)** seçeneğini belirleyin. [Güvenlik kuralı ayarları](../virtual-network/manage-network-security-group.md#security-rule-settings)' nda açıklandığı gibi yeni bir kural oluşturmak için gereken tüm ayrıntıları girin. Seçenek **hedefinin** *hizmet etiketi* olarak ayarlandığından ve **hedef hizmet etiketinin** *AzureBackup* olarak ayarlandığından emin olun.

1. Yeni oluşturulan giden güvenlik kuralını kaydetmek için **Ekle**  ' yi seçin.

Benzer şekilde, Azure depolama ve Azure AD için NSG giden güvenlik kuralları oluşturabilirsiniz.

#### <a name="azure-firewall-tags"></a>Azure Güvenlik Duvarı etiketleri

Azure Güvenlik duvarı kullanıyorsanız, *AzureBackup* [Azure Güvenlik Duvarı FQDN etiketini](../firewall/fqdn-tags.md)kullanarak bir uygulama kuralı oluşturun. Bu, Azure Backup tüm giden erişimleri sağlar.

#### <a name="allow-access-to-service-ip-ranges"></a>Hizmet IP aralıklarına erişime izin ver

Erişim hizmeti IP 'lerine izin vermeyi seçerseniz, [burada](https://www.microsoft.com/download/confirmation.aspx?id=56519)KULLANILABILIR olan JSON dosyasındaki IP aralıklarına bakın. Azure Backup, Azure depolama ve Azure Active Directory karşılık gelen IP 'lere erişim izni vermeniz gerekir.

#### <a name="allow-access-to-service-fqdns"></a>Hizmet FQDN 'lere erişime izin ver

Sunucularınızdaki gerekli hizmetlere erişime izin vermek için aşağıdaki FQDN 'leri de kullanabilirsiniz:

| Hizmet    | Erişilecek etki alanı adları                             | Bağlantı noktaları
| -------------- | ------------------------------------------------------------ | ---
| Azure Backup  | `*.backup.windowsazure.com`                             | 443
| Azure depolama | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Azure AD      | [Bu makaleye](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) göre 56 ve 59 bölümlerinde FQDN 'lere erişime izin ver | Uygun olduğu şekilde

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Trafiği yönlendirmek için bir HTTP proxy sunucusu kullanma

Azure VM 'de bir SQL Server veritabanını yedeklerken, VM 'deki yedekleme uzantısı, Azure depolama 'ya Azure Backup ve verilere yönetim komutları göndermek için HTTPS API 'Lerini kullanır. Yedekleme uzantısı, kimlik doğrulaması için Azure AD 'yi de kullanır. Bu üç hizmet için yedekleme uzantısı trafiğini HTTP ara sunucusu üzerinden yönlendirin. Gerekli hizmetlere erişim izni vermek için yukarıda bahsedilen IP 'Ler ve FQDN 'lerin listesini kullanın. Kimliği doğrulanmış proxy sunucuları desteklenmez.

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure Backup için veritabanı adlandırma yönergeleri

Veritabanı adlarında aşağıdaki öğeleri kullanmaktan kaçının:

* Sondaki ve Öndeki boşluklar
* Sondaki ünlem işaretleri (!)
* Kapanış köşeli parantezleri (])
* Noktalı virgül '; '
* Eğik çizgi '/'

Diğer ad, desteklenmeyen karakterler için kullanılabilir, ancak bunlardan kaçınmasını öneririz. Daha fazla bilgi için bkz. [Tablo Hizmeti Veri Modelini anlama](/rest/api/storageservices/understanding-the-table-service-data-model).

>[!NOTE]
>Adında "+" veya "&" gibi özel karakterler içeren veritabanları için **koruma yapılandırma** işlemi desteklenmez. Veritabanı adını değiştirebilir veya **otomatik korumayı** etkinleştirerek bu veritabanlarını başarıyla koruyabilirler.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server veritabanlarını bulma

VM üzerinde çalışan veritabanlarını bulma:

1. [Azure portalında](https://portal.azure.com) veritabanını yedeklemek istediğiniz Kurtarma Hizmetleri kasasını açın.

2. **Kurtarma Hizmetleri Kasası** panosunda **yedekleme**' yi seçin.

   ![Yedekle'yi seçerek Yedekleme Hedefi menüsünü açın](./media/backup-azure-sql-database/open-backup-menu.png)

3. **Yedekleme hedefi**' nde, **iş yükünüzün çalıştığı yeri** **Azure**'da belirleyin.

4. **Neleri yedeklemek istiyorsunuz?** bölümünde **Azure VM'de SQL Server**'ı seçin.

    ![Yedekleme için Azure VM'de SQL Server'ı seçme](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **Yedekleme Hedefi** > **VM'lerin içinde VT bul** altında **Bulmayı Başlat**'a tıklayarak abonelikteki korunmayan sanal makineleri arayın. Bu arama, abonelikteki korumasız VM sayısına bağlı olarak biraz zaman alabilir.

   * Bulma işlemi tamamlandığında korunmayan sanal makineler adları ve kaynak gruplarıyla birlikte listelenir.
   * Bir VM beklendiği gibi listelenmiyorsa, bir kasada zaten yedeklenmiş olup olmadığına bakın.
   * Birden çok VM aynı ada sahip olabilir, ancak bunlar farklı kaynak gruplarına ait olacaktır.

     ![VM'lerin içinde VT bulma sırasında Backup bekliyor](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Sanal makine listesinde SQL Server veritabanını çalıştıran sanal makineyi bulup **Veritabanlarını bul**'u seçin.

7. **Bildirimlerde** veritabanı bulmayı izleyin. Bu eylem için gereken süre, VM veritabanlarının sayısına bağlıdır. Seçilen veritabanları bulunduğunda işlemin başarılı olduğunu belirten bir ileti görüntülenir.

    ![Dağıtım başarılı iletisi](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup, sanal makine üzerindeki tüm SQL Server veritabanlarını bulur. Bulma sırasında, arka planda aşağıdaki öğeler oluşur:

    * Azure Backup, VM 'yi iş yükü yedeklemesi için kasaya kaydeder. Kayıtlı VM 'deki tüm veritabanları yalnızca bu kasaya yedeklenebilir.
    * Azure Backup, sanal makineye AzureBackupWindowsWorkload uzantısını yükler. SQL veritabanında hiçbir aracı yüklü değil.
    * Azure Backup, sanal makinede NT Service\AzureWLBackupPluginSvc hesabını oluşturur.
      * Tüm yedekleme ve geri yükleme işlemleri için hizmet hesabı kullanılır.
      * NT Service\AzureWLBackupPluginSvc, SQL sysadmin izinleri gerektirir. Market 'te oluşturulan tüm SQL Server VM 'Ler, Sqliaasextenma yüklenmiş olarak gelir. AzureBackupWindowsWorkload uzantısı, gerekli izinleri otomatik olarak almak için SQLIaaSExtension uzantısını kullanır.
    * VM 'yi Market 'ten oluşturmadıysanız veya SQL 2008 ve 2008 R2 kullanıyorsanız, sanal makinede Sqliaasextenınulma yüklü olmayabilir ve bulma işlemi UserErrorSQLNoSysAdminMembership hata iletisiyle başarısız olur. Bu sorunu onarmak için [VM Izinleri ayarla](backup-azure-sql-database.md#set-vm-permissions)bölümündeki yönergeleri izleyin.

        ![Sanal makineyi ve veritabanını seçme](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Yedeklemeyi yapılandırma  

1. **Yedekleme hedefi**  >  **Adım 2: yedeklemeyi yapılandırma**' yı seçin, yedeklemeyi **Yapılandır**' ı seçin.

   ![Yedeklemeyi Yapılandır'ı seçin](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Tüm kayıtlı kullanılabilirlik gruplarını ve tek başına SQL Server örnekleri görmek için **Kaynak Ekle** ' yi seçin.

    ![Kaynak Ekle ' yi seçin](./media/backup-azure-sql-database/add-resources.png)

1. **Yedeklenecek öğeleri seçin** ekranında, söz konusu örnekteki veya Always on kullanılabilirlik grubundaki tüm korumasız veritabanlarının listesini genişletmek için satırın solundaki oku seçin.

    ![Yedeklenecek öğeleri seçin](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Korumak istediğiniz tüm veritabanları ' nı seçin ve ardından **Tamam**' ı seçin.

   ![Veritabanını koruma](./media/backup-azure-sql-database/select-database-to-protect.png)

   Azure Backup, yedekleme yüklerini iyileştirmek için tek bir yedekleme işindeki maksimum veritabanı sayısını 50 ile sınırlar.

     * 50 'den fazla veritabanını korumak için birden çok yedekleme yapılandırın.
     * Tüm örneği veya Always on kullanılabilirlik grubunu [etkinleştirmek](#enable-auto-protection) Için, **oto koru** açılan listesinde  **Açık**' ı seçin ve ardından **Tamam**' ı seçin.

         > [!NOTE]
         > [Otomatik koruma](#enable-auto-protection) özelliği, aynı anda var olan tüm veritabanlarında korumayı yalnızca bir kerede etkinleştirmez, ancak aynı zamanda bu örneğe veya kullanılabilirlik grubuna eklenen yeni veritabanlarını otomatik olarak korur.  

1. **Yedekleme ilkesini** tanımlayın. Aşağıdakilerden birini yapabilirsiniz:

   * Varsayılan ilkeyi *HourlyLogBackup* olarak seçin.
   * SQL için daha önce oluşturulmuş olan bir yedekleme ilkesini seçin.
   * Kurtarma noktası hedefinize ve bekletme aralığınıza uygun yeni bir ilke tanımlayın.

     ![Yedekleme ilkesi seçme](./media/backup-azure-sql-database/select-backup-policy.png)

1. **Korumayı Yapılandır** işlemini göndermek ve portalın **Bildirimler** alanındaki yapılandırma Ilerlemesini izlemek için **yedeklemeyi etkinleştir** ' i seçin.

   ![Yapılandırma ilerlemesini izle](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Yedekleme ilkesi oluşturma

Yedekleme ilkesi, yedekleme zamanını ve saklama süresini belirler.

* İlkeler, kasa düzeyinde oluşturulur.
* Bir yedekleme ilkesi birden fazla kasa tarafından kullanılabilir ancak ilgili yedekleme ilkesini her kasaya ayrıca uygulamanız gerekir.
* Bir yedekleme ilkesi oluşturduğunuzda varsayılan olarak günlük tam yedek oluşturulur.
* Haftalık tam yedek yapılandırırsanız değişiklik yedeği de ekleyebilirsiniz.
* [Farklı yedekleme ilkesi türleri](backup-architecture.md#sql-server-backup-types)hakkında bilgi edinin.

Yedekleme ilkesi oluşturmak için:

1. Kasada **Yedekleme ilkeleri** > **Ekle**'yi seçin.
1. **Ekle**' de, ilke türünü tanımlamak IÇIN **Azure VM 'de SQL Server** ' yi seçin.

   ![Yeni yedekleme ilkesi için ilke türü seçme](./media/backup-azure-sql-database/policy-type-details.png)

1. **İlke adı** alanına yeni ilkenin adını girin.

    ![İlke adını girin](./media/backup-azure-sql-database/policy-name.png)

1. Varsayılan ayarları değiştirmek için karşılık gelen **Düzenle** bağlantısını **tam yedekleme**' ye seçin.

   * Bir **yedekleme sıklığı** seçin. **Günlük** veya **haftalık** seçeneklerinden birini belirleyin.
   * **Günlük** için yedekleme işinin başlayacağı saati ve saat dilimini seçin. Günlük tam yedeklemeler için değişiklik yedeği oluşturamazsınız.

     ![Yeni yedekleme ilkesi alanları](./media/backup-azure-sql-database/full-backup-policy.png)  

1. **Saklama aralığı**' nda, tüm seçenekler varsayılan olarak seçilidir. İstemediğiniz tüm Bekletme aralığı sınırlarını temizleyin ve ardından kullanılacak aralıkları ayarlayın.

    * Herhangi bir yedekleme türü (tam, değişiklik ve günlük) için en düşük saklama süresi yedi gündür.
    * Kurtarma noktaları, belirtilen bekletme aralığına göre etiketlenir. Örneğin günlük tam yedek seçerseniz her gün yalnızca bir yedekleme işlemi tetiklenir.
    * Belirli bir günün yedeklemesi, haftalık bekletme aralığına ve haftalık bekletme ayarına göre etiketlenebilir ve korunur.
    * Aylık ve yıllık bekletme aralıkları benzer bir şekilde davranır.

       ![Bekletme aralığı ayarları](./media/backup-azure-sql-database/retention-range-interval.png)

1. Tam yedeklemeler ayarını kabul etmek için **Tamam ' ı** seçin.
1. Varsayılan ayarları değiştirmek için **değişiklik yedeklemesine** karşılık gelen **düzenleme** bağlantısını seçin.

    * **Değişiklik Yedeği ilkesi** sayfasında **Etkinleştir**'i seçerek sıklık ve bekletme denetimlerini açın.
    * Günde yalnızca bir değişiklik yedeklemesini tetikleyebilirsiniz. Değişiklik yedeklemesi, tam yedekleme ile aynı gün içinde tetiklenemez.
    * Değişiklik yedekleri en fazla 180 gün bekletilebilir.
    * Değişiklik yedeklemesi ana veritabanı için desteklenmiyor.

      ![Değişiklik yedekleme ilkesi](./media/backup-azure-sql-database/differential-backup-policy.png)

1. Varsayılan ayarları değiştirmek için, **günlük yedeğine** karşılık gelen **düzenleme** bağlantısını seçin

    * **Günlük Yedekleme**'de **Etkinleştir**'i seçip sıklığı ve bekletme denetimlerini ayarlayın.
    * Günlük yedeklemeleri her 15 dakikada bir gerçekleşebilir ve 35 güne kadar tutulabilir.
    * Veritabanı [Basit kurtarma modelinde](/sql/relational-databases/backup-restore/recovery-models-sql-server)ise, bu veritabanı için günlük yedekleme zamanlaması duraklatılır ve bu nedenle hiçbir günlük yedeklemesi tetiklenmeyecektir.
    * Veritabanının kurtarma modeli **tam** yerine **basit** olarak değişirse, kurtarma modelindeki değişikliğin 24 saat içinde günlük yedeklemeleri duraklatılır. Benzer şekilde, kurtarma modeli **basit** olarak değişirse, kesin günlük yedeklemeleri artık veritabanı için desteklenebilir, günlük yedeklemeleri zamanlamaları, kurtarma modelindeki değişikliğin 24 saat içinde etkinleştirilir.

      ![Günlük yedekleme ilkesi](./media/backup-azure-sql-database/log-backup-policy.png)

1. **Yedekleme ilkesi** menüsünde, **SQL Backup sıkıştırmasını** etkinleştirilip etkinleştirilmeyeceğini seçin. Bu seçenek varsayılan olarak devre dışıdır. Etkinleştirilirse, SQL Server VDı 'a sıkıştırılmış bir yedekleme akışı gönderir. Azure Backup, bu denetimin değerine bağlı olarak, örnek düzeyi varsayılan değerleri COMPRESSION/NO_COMPRESSION yan tümcesiyle geçersiz kılar.

1. Yedekleme ilkesinde yaptığınız değişiklikleri tamamladıktan sonra **Tamam**'ı seçin.

> [!NOTE]
> Her günlük yedeklemesi, bir kurtarma zinciri oluşturmak için önceki tam yedeklemeye zincirlenir. Bu tam yedekleme, son günlük yedeklemenin saklama süresi sona erene kadar bekletilecektir. Bu, tüm günlüklerin kurtarılabilmesini sağlamak için ek bir süre boyunca tam yedeklemenin korunduğu anlamına gelebilir. Haftalık tam yedekleme, günlük fark ve 2 saat günlüğü olduğunu varsayalım. Bunların hepsi 30 gün boyunca tutulur. Ancak, haftalık tam yedekleme yalnızca bir sonraki tam yedekleme kullanılabilir olduktan sonra silinebilir/silinir, yani 30 + 7 gün sonra. Örneğin, haftalık tam yedekleme 16 Kasım 'da gerçekleşir. Bekletme ilkesine göre, 16 Aralık 'a kadar saklanması gerekir. Bu tam için en son günlük yedeklemesi, 22nd tarihinde bir sonraki zamanlanmış tam önce gerçekleşir. Bu günlük, 22nd 'e kadar kullanılabilir olana kadar, 16. ve tam 6. Bu nedenle, 16. dolu, 22nd 'e kadar korunur.

## <a name="enable-auto-protection"></a>Otomatik korumayı etkinleştir  

Otomatik korumayı, tüm mevcut ve gelecekteki veritabanlarını tek başına bir SQL Server örneğine veya her zaman açık kullanılabilirlik grubuna otomatik olarak yedekleyecek şekilde etkinleştirebilirsiniz.

* Tek seferde otomatik koruma için seçebileceğiniz veritabanı sayısı için bir sınır yoktur. Bulma genellikle her sekiz saatte bir çalışır. Bununla birlikte, bulmayı **yeniden keşfet** seçeneğini belirleyerek el ile bir keşif çalıştırırsanız yeni veritabanlarını hemen bulabilir ve koruyabilirsiniz.
* Otomatik korumayı etkinleştirdiğiniz sırada bir örnekteki veritabanlarını seçmeli olarak koruyamazsınız veya dışlayabilirsiniz.
* Örneğiniz zaten bazı korumalı veritabanları içeriyorsa, otomatik korumayı etkinleştirdikten sonra bile kendi ilkeleri altında korunmaları gerekir. Daha sonra eklenen tüm korumasız veritabanları, otomatik korumayı etkinleştirme sırasında, **yedeklemeyi Yapılandır** altında listelenen tek bir ilkeye sahip olacaktır. Ancak, bir otomatik korumalı veritabanıyla ilişkili ilkeyi daha sonra değiştirebilirsiniz.  

Otomatik korumayı etkinleştirmek için:

  1. **Yedeklenecek öğeler**' de, otomatik korumayı etkinleştirmek istediğiniz örneği seçin.
  2. **Oto koru** altında aşağı açılan listeyi seçin, **Açık**' ı seçin ve ardından **Tamam**' ı seçin.

      ![Kullanılabilirlik grubunda otomatik korumayı etkinleştir](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Yedekleme tüm veritabanları için bir arada yapılandırılır ve **yedekleme işlerinde** izlenebilir.

Otomatik korumayı devre dışı bırakmanız gerekirse, **yedeklemeyi Yapılandır** altında örnek adını seçin ve ardından örnek Için **otomatik korumayı devre dışı bırak** ' ı seçin. Tüm veritabanları yedeklenmeye devam eder, ancak gelecekteki veritabanları otomatik olarak korunmaz.

![Bu örnekte otomatik korumayı devre dışı bırak](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Sonraki adımlar

Şunları nasıl yapacağınızı öğrenin:

* [Yedeklenen SQL Server veritabanlarını geri yükleme](restore-sql-database-azure-vm.md)
* [Yedeklenen SQL Server veritabanlarını yönetme](manage-monitor-sql-database-backup.md)
