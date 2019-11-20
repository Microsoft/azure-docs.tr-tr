---
title: Öğretici-SQL Server veritabanlarını Azure 'a yedekleme
description: Bu öğreticide, bir Azure VM üzerinde çalışan SQL Server bir veritabanını Azure Backup kurtarma hizmetleri kasasına nasıl yedekleyeceğinizi öğrenin.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 3216404b04647e2fb5f15beb09b9a4ccd6648bba
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171878"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Azure VM 'de SQL Server veritabanını yedekleme

Bu öğreticide, bir Azure VM üzerinde çalışan SQL Server bir veritabanını Azure Backup kurtarma hizmetleri kasasına nasıl yedekleyeceğiniz gösterilmektedir. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Kasa oluşturun ve yapılandırın.
> * Veritabanlarını bulun ve yedeklemeleri ayarlayın.
> * Veritabanları için otomatik korumayı ayarlayın.
> * İsteğe bağlı yedekleme çalıştırın.

## <a name="prerequisites"></a>Önkoşullar

SQL Server veritabanınızı yedeklemebilmeniz için aşağıdaki koşulları kontrol edin:

1. SQL Server örneğini barındıran VM ile aynı bölgede veya yerel ayarda bir kurtarma hizmetleri Kasası belirler veya [oluşturun](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) .
2. SQL veritabanlarını yedeklemek için gereken [VM Izinlerini denetleyin](backup-azure-sql-database.md#set-vm-permissions) .
3. VM 'nin [ağ bağlantısı](backup-sql-server-database-azure-vms.md#establish-network-connectivity)olduğunu doğrulayın.
4. SQL Server veritabanlarının Azure Backup için [adlandırma yönergelerine](#verify-database-naming-guidelines-for-azure-backup) uygun olarak adlandırıldığından emin olun.
5. Veritabanı için etkinleştirilmiş başka bir yedekleme çözümünden emin olun. Bu senaryoyu ayarlamadan önce diğer tüm SQL Server yedeklemelerini devre dışı bırakın. Bir Azure VM için Azure Backup, bir çakışma olmadan VM üzerinde çalışan bir SQL Server veritabanının Azure Backup birlikte etkinleştirebilirsiniz.

### <a name="establish-network-connectivity"></a>Ağ bağlantısı kurma

Tüm işlemler için SQL Server VM sanal makinenin Azure genel IP adreslerine bağlanması gerekir. VM işlemleri (veritabanı bulma, yedeklemeleri yapılandırma, yedeklemeleri zamanlama, kurtarma noktalarını geri yükleme vb.), genel IP adreslerine bağlantı olmadan başarısız olur. Şu seçeneklerden biriyle bağlantı kurun:

* **Azure veri MERKEZI IP aralıklarına Izin ver**: Indirme sırasında [IP aralıklarına](https://www.microsoft.com/download/details.aspx?id=41653) izin verin. Ağ güvenlik grubuna (NSG) erişmek için **set-AzureNetworkSecurityRule** cmdlet 'ini kullanın.
* **Trafiği yönlendirmek için BIR http proxy sunucusu dağıtma**: Azure VM 'de bir SQL Server veritabanını YEDEKLERKEN, VM 'deki yedekleme uzantısı, yönetim komutlarını Azure Backup ve Azure depolama 'ya veri göndermek Için https API 'lerini kullanır. Yedekleme Uzantısı ayrıca kimlik doğrulaması için Azure Active Directory (Azure AD) kullanır. Bu üç hizmetin yedekleme uzantısı trafiğini HTTP proxy üzerinden yönlendirin. Uzantılar, genel internet erişimi için yapılandırılan tek bileşendir.

Her seçeneğin avantajları ve dezavantajları vardır

**Seçenek** | **Üstünlü** | **Olumsuz**
--- | --- | ---
IP aralıklarına izin ver | Ek maliyet yok. | IP adresi aralıkları zaman içinde değiştiğinden yönetilmesi karmaşıktır. <br/><br/> Yalnızca Azure Storage değil Azure 'un tamamına erişim sağlar.
HTTP proxy kullanma   | Depolama URL 'Lerinde ara sunucuya ayrıntılı denetime izin verilir. <br/><br/> VM 'lere tek bir internet erişimi noktası. <br/><br/> Azure IP adresi değişikliklerine tabi değildir. | Proxy yazılımıyla VM çalıştırmak için ek maliyetler.

### <a name="set-vm-permissions"></a>VM izinlerini ayarla

Azure Backup, SQL Server veritabanı için yedekleme yapılandırdığınızda bir dizi şey yapar:

* **AzureBackupWindowsWorkload** uzantısını ekler.
* Sanal makinedeki veritabanlarını saptamak için Azure Backup **NT SERVICE\AzureWLBackupPluginSvc**hesabını oluşturur. Bu hesap yedekleme ve geri yükleme için kullanılır ve SQL sysadmin izinleri gerektirir.
* Azure Backup, veritabanı bulma/sorgulama için **NT AUTHORITY\SYSTEM** hesabını kullanır, bu nedenle bu hesabın SQL 'de genel oturum açması gerekir.

Azure Marketi 'nden SQL Server VM oluşturmadıysanız **Usererrorsqlnosysadminmembership**hatası alabilirsiniz. Bu durum oluşursa, [Bu yönergeleri izleyin](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Azure Backup için veritabanı adlandırma yönergelerini doğrulama

Veritabanı adları için aşağıdakilerden kaçının:

* Sondaki/önde gelen boşluklar
* Sondaki '! '
* Kapalı köşeli ayraç '] '
* ' F:\ ' ile başlayan veritabanı adları

Azure Tablo desteklenmeyen karakterler için diğer ad veriyoruz, ancak bunlardan kaçınıyoruz. [Daha fazla bilgi edinin](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server veritabanlarını bul

VM üzerinde çalışan veritabanlarını bulur.

1. [Azure Portal](https://portal.azure.com), veritabanını yedeklemek Için kullandığınız kurtarma hizmetleri kasasını açın.

2. **Kurtarma Hizmetleri Kasası** panosunda **yedekleme**' yi seçin.

   ![Yedekleme hedefi menüsünü açmak için yedekleme 'yi seçin](./media/backup-azure-sql-database/open-backup-menu.png)

3. **Yedekleme hedefi**' nde, **Iş yükünüzün** **Azure** 'da çalıştığı, (varsayılan) seçeneğini belirleyin.

4. **Neleri yedeklemek**istiyorsunuz ' de **Azure VM 'de SQL Server**' yi seçin.

    ![Yedekleme için Azure VM 'de SQL Server seçin](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **Yedekleme hedefi** ' nde, **VM 'lerde veritabanlarını bulur** > abonelikte korumasız VM 'Leri aramak için **bulmayı Başlat** ' ı seçin. Abonelikte korunmayan sanal makinelerin sayısına bağlı olarak bu işlem biraz zaman alabilir.

   * Korumasız VM 'Ler, ad ve kaynak grubuna göre listelenmiş bulma sonrasında listede görünmelidir.
   * Bir VM beklendiği gibi listelenmiyorsa, bir kasada zaten yedeklenmiş olup olmadığını kontrol edin.
   * Birden çok VM aynı ada sahip olabilir, ancak bunlar farklı kaynak gruplarına ait olacaktır.

     ![VM 'lerde veritabanları için arama sırasında yedekleme bekliyor](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM listesinde, SQL Server veritabanını çalıştıran VM 'yi seçin > **DB 'Leri bulun**.

7. **Bildirim** alanında veritabanı bulmayı izleyin. VM üzerinde kaç veritabanının olduğuna bağlı olarak işin tamamlanması biraz zaman alabilir. Seçilen veritabanları bulunduğunda, bir başarı iletisi görüntülenir.

    ![Dağıtım başarılı iletisi](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup VM 'deki tüm SQL Server veritabanlarını bulur. Bulma sırasında arka planda aşağıdakiler gerçekleşir:

    * Azure Backup, VM 'yi iş yükü yedeklemesi için kasaya kaydedin. Kayıtlı VM 'deki tüm veritabanları yalnızca bu kasaya yedeklenebilir.
    * Azure Backup, **AzureBackupWindowsWorkload** uzantısını VM 'ye yüklenir. SQL veritabanında hiçbir aracı yüklü değil.
    * Azure Backup VM üzerinde **NT Service\AzureWLBackupPluginSvc** hizmet hesabını oluşturur.
      * Tüm yedekleme ve geri yükleme işlemleri hizmet hesabını kullanır.
      * **NT Service\AzureWLBackupPluginSvc** , SQL sysadmin izinlerine sahip olmalıdır. Azure Marketi 'nde oluşturulan tüm SQL Server VM 'Ler, **Sqliaasextenma** yüklenmiş olarak gelir. **AzureBackupWindowsWorkload** uzantısı, gerekli izinleri otomatik olarak almak Için **Sqliaasexten** kullanır.
    * VM 'yi Market 'ten oluşturmadıysanız, sanal makinede **Sqliaasexten,** yüklü değildir ve bulma Işlemi **Usererrorsqlnosysadminmembership**hata iletisiyle başarısız olur. Bu sorunu gidermeye yönelik [yönergeleri](backup-azure-sql-database.md#set-vm-permissions) izleyin.

        ![VM ve veritabanını seçin](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Yedeklemeyi yapılandırma  

Yedeklemeyi şu şekilde yapılandırın:

1. **Yedekleme hedefi**' nde **yedeklemeyi Yapılandır**' ı seçin.

   ![Yedeklemeyi Yapılandır ' ı seçin](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **Yedeklemeyi Yapılandır**' a tıklayın, **yedeklenecek öğeleri seçin** dikey penceresi görüntülenir. Bu, tüm kayıtlı kullanılabilirlik gruplarını ve tek başına SQL Server 'ı listeler. Söz konusu örnekteki tüm korumasız veritabanlarını veya her zaman AG 'yi görmek için satırın solundaki köşeli çift ayraca göre genişletin.  

    ![Tek başına veritabanları ile tüm SQL Server örneklerini görüntüleme](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Korumak istediğiniz tüm veritabanlarını seçin > **Tamam**' a tıklayın.

   ![Veritabanını koruma](./media/backup-azure-sql-database/select-database-to-protect.png)

   Yedekleme yüklerini iyileştirmek için Azure Backup bir yedekleme işinde en fazla veritabanı sayısını 50 olarak ayarlar.

     * Alternatif olarak **, otomatik koruma sütunundaki ilgili** açılan menüde **bulunan** seçeneğini belirleyerek tüm örnek veya Always on kullanılabilirlik grubu üzerinde otomatik korumayı etkinleştirebilirsiniz. Otomatik koruma özelliği yalnızca tek bir go içindeki tüm mevcut veritabanlarında koruma olanağı sağlar, ancak Ayrıca bu örneğe veya gelecekte kullanılabilirlik grubuna eklenecek yeni veritabanlarını otomatik olarak korur.  

4. **Tamam** ' a tıklayarak **yedekleme ilkesi** dikey penceresini açın.

    ![Always on kullanılabilirlik grubunda otomatik korumayı etkinleştir](./media/backup-azure-sql-database/enable-auto-protection.png)

5. **Yedekleme Ilkesini seçin**bölümünde bir ilke seçin ve ardından **Tamam**' a tıklayın.

   * Varsayılan ilkeyi seçin: HourlyLogBackup.
   * Daha önce SQL için oluşturulmuş mevcut bir yedekleme ilkesi seçin.
   * RPO ve bekletme aralığınızı temel alarak yeni bir ilke tanımlayın.

     ![Yedekleme ilkesi seçin](./media/backup-azure-sql-database/select-backup-policy.png)

6. **Yedekleme** menüsünde **yedeklemeyi etkinleştir**' i seçin.

    ![Seçilen yedekleme ilkesini etkinleştir](./media/backup-azure-sql-database/enable-backup-button.png)

7. Portalın **Bildirimler** alanındaki yapılandırma ilerlemesini izleyin.

    ![Bildirim alanı](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Yedekleme ilkesi oluşturma

Yedekleme ilkesi, yedeklemelerin ne zaman alındığını ve ne kadar süreyle korunduğunu tanımlar.

* Kasa düzeyinde bir ilke oluşturulur.
* Birden çok kasa aynı yedekleme ilkesini kullanabilir, ancak yedekleme ilkesini her kasaya uygulamanız gerekir.
* Bir yedekleme ilkesi oluşturduğunuzda, varsayılan olarak günlük tam yedekleme varsayılandır.
* Değişiklik yedeklemesi ekleyebilirsiniz, ancak yalnızca tam yedeklemeleri haftalık olarak gerçekleşecek şekilde yapılandırabilirsiniz.
* Farklı yedekleme ilkesi türleri [hakkında bilgi edinin](backup-architecture.md#sql-server-backup-types) .

Bir yedekleme ilkesi oluşturmak için:

1. Kasada, **ekle** > **yedekleme ilkeleri** ' ne tıklayın.
2. **Ekle** menüsünde, ilke türünü tanımlamak IÇIN **Azure VM 'de SQL Server** ' a tıklayın.

   ![Yeni yedekleme ilkesi için bir ilke türü seçin](./media/backup-azure-sql-database/policy-type-details.png)

3. **İlke adı**alanına yeni ilke için bir ad girin.
4. **Tam yedekleme ilkesinde**, bir **yedekleme sıklığı**seçin, **günlük** veya **haftalık**' ı seçin.

   * **Günlük**için, yedekleme işinin başladığı saat ve saat dilimini seçin.
   * **Tam yedekleme** seçeneğini kapatamıyoruz, tam yedekleme çalıştırmanız gerekir.
   * İlkeyi görüntülemek için **tam yedekleme** ' ye tıklayın.
   * Günlük tam yedeklemeler için fark yedeklemeleri oluşturamazsınız.
   * **Haftalık**olarak, yedekleme işinin başladığı hafta, saat ve saat diliminin gününü seçin.

     ![Yeni yedekleme ilkesi alanları](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **Bekletme aralığı**için varsayılan olarak tüm seçenekler seçilidir. Kullanmak istemediğiniz istenmeyen bekletme aralığı sınırlarını temizleyin ve kullanılacak aralıkları ayarlayın.

    * Herhangi bir yedekleme türü için en düşük saklama süresi (tam/değişiklik/günlük) yedi gündür.
    * Kurtarma noktaları, bekletme aralığına göre bekletme için etiketlenir. Örneğin, günlük tam yedekleme seçerseniz, her gün yalnızca bir tam yedekleme tetiklenir.
    * Belirli bir günün yedeklemesi, haftalık bekletme aralığına ve haftalık saklama ayarına göre etiketlenebilir ve korunur.
    * Aylık ve yıllık bekletme aralıkları benzer bir şekilde davranır.

   ![Bekletme aralığı aralığı ayarları](./media/backup-azure-sql-database/retention-range-interval.png)

6. **Tam yedekleme ilkesi** menüsünde, ayarları kabul etmek için **Tamam** ' ı seçin.
7. Bir değişiklik yedekleme ilkesi eklemek için, **fark yedeklemesi**' ni seçin.

   ![saklama aralığı aralığı ayarları](./media/backup-azure-sql-database/retention-range-interval.png)
   ![fark yedekleme ilkesi menüsünü açın](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **Değişiklik yedekleme ilkesinde**, sıklık ve bekletme denetimlerini açmak için **Etkinleştir** ' i seçin.

    * En çok, her gün bir değişiklik yedeklemesi tetikleyebilirsiniz.
    * Değişiklik yedeklemeleri, en fazla 180 gün boyunca korunabilir. Daha uzun bekletme yapmanız gerekiyorsa tam yedeklemeler kullanmanız gerekir.

9. İlkeyi kaydetmek ve ana **yedekleme ilkesi** menüsüne dönmek için **Tamam ' ı** seçin.

10. İşlem günlüğü yedekleme ilkesi eklemek için **günlük yedeklemesi**' ni seçin.
11. **Günlük yedeklemesi**bölümünde **Etkinleştir**' i seçin ve ardından sıklık ve bekletme denetimlerini ayarlayın. Günlük yedeklemeleri 15 dakikada bir gerçekleşebilir ve 35 güne kadar tutulabilirler.
12. İlkeyi kaydetmek ve ana **yedekleme ilkesi** menüsüne dönmek için **Tamam ' ı** seçin.

    ![Günlük yedekleme ilkesini düzenleme](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **Yedekleme ilkesi** menüsünde, **SQL Backup sıkıştırmasını**etkinleştirilip etkinleştirilmeyeceğini seçin.
    * Sıkıştırma varsayılan olarak devre dışıdır.
    * Arka uçta Azure Backup SQL yerel yedekleme sıkıştırmasını kullanır.

14. Yedekleme ilkesinde yapılan düzenlemeleri tamamladıktan sonra **Tamam**' ı seçin.

## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

1. Kurtarma Hizmetleri kasasında yedekleme öğeleri ' ni seçin.
2. "Azure VM 'de SQL" seçeneğine tıklayın.
3. Veritabanına sağ tıklayın ve "Şimdi Yedekle" yi seçin.
4. Yedekleme türünü seçin (tam/fark/günlük/kopya yalnızca tam) ve sıkıştırma (etkinleştir/devre dışı bırak)
5. Yedeklemeyi başlatmak için Tamam ' ı seçin.
6. Kurtarma Hizmetleri kasanıza gidip "yedekleme Işleri" seçeneğini belirleyerek yedekleme işini izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portalı kullanarak şu işlemleri gerçekleştirdiniz:

> [!div class="checklist"]
>
> * Kasa oluşturun ve yapılandırın.
> * Veritabanlarını bulun ve yedeklemeleri ayarlayın.
> * Veritabanları için otomatik korumayı ayarlayın.
> * İsteğe bağlı yedekleme çalıştırın.

Bir Azure sanal makinesini diskten geri yüklemek için sonraki öğreticiye devam edin.

> [!div class="nextstepaction"]
> [Azure VM 'lerinde SQL Server veritabanlarını geri yükleme](./restore-sql-database-azure-vm.md)
