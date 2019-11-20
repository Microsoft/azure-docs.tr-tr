---
title: 'SaaS uygulamaları: olağanüstü durum kurtarma için coğrafi olarak yedekli yedeklemeler'
description: Bir kesinti durumunda çok kiracılı SaaS uygulamasını kurtarmak için Azure SQL veritabanı coğrafi olarak yedekli yedeklemeleri kullanmayı öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/14/2019
ms.openlocfilehash: 270fc157fa14efa19ed30d35b614fb769804b72e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826467"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Bir çok kiracılı SaaS uygulamasını veritabanı yedeklemelerinden kurtarmak için coğrafi geri yükleme kullanın

Bu öğretici, kiracı modeline göre veritabanı ile uygulanan çok kiracılı bir SaaS uygulaması için tam bir olağanüstü durum kurtarma senaryosunu araştırır. [Coğrafi geri yüklemeyi](sql-database-recovery-using-backups.md) , Katalog ve kiracı veritabanlarını otomatik olarak korunan coğrafi olarak yedekli yedeklerden alternatif bir kurtarma bölgesine kurtarmak için kullanabilirsiniz. Kesinti çözümlendikten sonra, değişen veritabanlarını özgün bölgelerine yeniden eklemek için [coğrafi çoğaltma](sql-database-geo-replication-overview.md) kullanırsınız.

![Coğrafi geri yükleme-mimari](media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

Coğrafi geri yükleme, Azure SQL veritabanı için en düşük maliyetli olağanüstü durum kurtarma çözümüdür. Ancak, coğrafi olarak yedekli yedeklerden geri yükleme işlemi bir saate kadar veri kaybına neden olabilir. Her bir veritabanının boyutuna bağlı olarak bu, önemli ölçüde zaman alabilir. 

> [!NOTE]
> Coğrafi geri yükleme yerine coğrafi çoğaltma kullanarak, mümkün olan en düşük RPO ve RTO ile uygulamaları kurtarın.

Bu öğretici hem geri yükleme hem de repama iş akışlarını araştırır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:
> [!div class="checklist"]
> 
> * Veritabanı ve elastik havuz yapılandırma bilgilerini kiracı kataloğu 'nda eşitleyin.
> * Uygulama, sunucular ve havuzlar içeren bir kurtarma bölgesinde yansıtma görüntüsü ortamı ayarlayın.   
> * Coğrafi geri yükleme kullanarak kataloğu ve kiracı veritabanlarını kurtarın.
> * Kaybı çözmeden sonra kiracı kataloğunu yeniden eklemek ve kiracı veritabanlarını değiştirilen Coğrafi çoğaltmayı kullanın.
> * Her bir veritabanı geri yüklendiği (veya onarımda) her bir kiracının veritabanının etkin kopyasının geçerli konumunu izlemek için kataloğu güncelleştirin.
> * Gecikme süresini azaltmak için uygulama ve kiracı veritabanının aynı Azure bölgesinde her zaman birlikte bulunduğundan emin olun. 
 

Bu öğreticiye başlamadan önce aşağıdaki önkoşulları doldurun:
* Kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtın. Beş dakikadan kısa bir süre içinde dağıtmak için bkz. [kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtma ve](saas-dbpertenant-get-started-deploy.md)İnceleme. 
* Azure PowerShell'i yükleyin. Ayrıntılar için bkz. [Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)kullanmaya başlama.

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Coğrafi geri yükleme kurtarma düzenine giriş

Olağanüstü durum kurtarma (DR), uyumluluk nedenleri veya iş sürekliliği gibi birçok uygulama için önemli bir konudur. Uzun süren bir hizmet kesintisi varsa, iyi hazırlanmış bir DR planı İş kesintisini en aza indirebilir. Coğrafi geri yüklemeyi temel alan bir DR planı çeşitli hedefleri yerine getirmelidir:
 * Kiracı veritabanlarının geri yüklenmesi için kullanılabilir olduğundan emin olmak için, seçilen kurtarma bölgesinde gereken tüm kapasiteyi olabildiğince çabuk ayırın.
 * Özgün havuzu ve veritabanı yapılandırmasını yansıtan bir yansıtma görüntüsü kurtarma ortamı oluşturun. 
 * Özgün bölge yeniden çevrimiçi olduğunda geri yükleme işleminin orta uçuşta iptal edilmesine izin verin.
 * Yeni kiracı ekleme işlemini mümkün olan en kısa sürede yeniden başlatmak için kiracı sağlamayı hızlı bir şekilde etkinleştirin.
 * Kiracılar 'ı öncelik sırasına göre geri yüklemek için iyileştirilmelidir.
 * Adımları paralel olarak, mümkün olduğunca kısa sürede almak için en iyi duruma getirilmiş bir işlem yapın.
 * Hataya, yeniden başlatılabilir ve ıdempotent için dayanıklı olun.
 * Kesinti çözümlendiğinde kiracılar için en az etkiyle veritabanlarını özgün bölgelerine yeniden boyar.  

> [!NOTE]
> Uygulama, uygulamanın dağıtıldığı bölgenin eşleştirilmiş bölgesine kurtarılır. Daha fazla bilgi için bkz. [Azure eşlenmiş bölgeler](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).   

Bu öğretici, Azure SQL veritabanı ve Azure platformunun özelliklerini kullanarak bu sorunları ele alır:

* Tüm gerekli kapasiteyi mümkün olduğunca hızlı bir şekilde ayırmak için [şablonlar Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template). Azure Resource Manager şablonlar, kurtarma bölgesindeki özgün sunucuların ve elastik havuzların ayna görüntüsünü sağlamak için kullanılır. Yeni kiracılar sağlamak için ayrı bir sunucu ve havuz de oluşturulur.
* Kiracı veritabanı kataloğu oluşturmak ve sürdürmek için [elastik veritabanı Istemci kitaplığı](sql-database-elastic-database-client-library.md) (edcl). Genişletilmiş Katalog, düzenli aralıklarla yenilenen havuz ve veritabanı yapılandırma bilgilerini içerir.
* Kurtarma ve geri alma sırasında katalogdaki veritabanı konumu girdilerini sürdürmek için EDCL 'nin parça [Yönetimi kurtarma özellikleri](sql-database-elastic-database-recovery-manager.md) .  
* [Coğrafi geri yükleme](sql-database-disaster-recovery.md), Katalog ve kiracı veritabanlarını otomatik olarak korunan coğrafi olarak yedekli yedeklerden kurtarmak için. 
* Kiracı öncelikli sırada gönderilen [zaman uyumsuz geri yükleme işlemleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations), sistem tarafından her havuz için sıraya alınır ve havuzun aşırı yüklü olmaması için toplu olarak işlenir. Bu işlemler gerekirse, yürütme sırasında veya sırasında iptal edilebilir.   
* [Coğrafi çoğaltma](sql-database-geo-replication-overview.md), kesintiden sonra veritabanlarını özgün bölgeye yeniden boyar. Coğrafi Çoğaltma kullandığınızda, kiracı üzerinde veri kaybı ve en az etkisi yoktur.
* Katalog eşitleme işleminin, konumundan bağımsız olarak etkin kataloğa bağlanmasına izin vermek için [SQL Server DNS diğer adları](dns-alias-overview.md).  

## <a name="get-the-disaster-recovery-scripts"></a>Olağanüstü durum kurtarma betiklerini al

Bu öğreticide kullanılan DR betikleri, [kiracı GitHub deposu başına Wingtip bilet SaaS veritabanında](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)bulunur. Wingtip bilet yönetim betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

> [!IMPORTANT]
> Tüm Wingtip bilet yönetimi betiklerinde olduğu gibi, DR betikleri örnek kalitelidir ve üretimde kullanılmamalıdır.

## <a name="review-the-healthy-state-of-the-application"></a>Uygulamanın sağlıklı durumunu gözden geçirin
Kurtarma işlemine başlamadan önce, uygulamanın normal sağlıklı durumunu gözden geçirin.

1. Web tarayıcınızda, Wingtip bilet olayları hub 'ını açın (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net, &lt;Kullanıcı&gt; yerine dağıtımınızın Kullanıcı değeri).
    
   Sayfanın alt kısmına ilerleyin ve altbilgide katalog sunucusu adı ve konumuna dikkat edin. Konum, uygulamayı dağıttığınız bölgedir.    

   > [!TIP]
   > Ekranı büyütmek için fareyi konumun üzerine getirin.

   ![Özgün bölgedeki Olay Hub 'ı sağlıklı durumu](media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Contoso Concert Salı kiracısı ' ni seçin ve olay sayfasını açın.

   Altbilgide, kiracının sunucu adına dikkat edin. Konum, katalog sunucusunun konumuyla aynıdır.

   ![Contoso Concert salonu orijinal bölgesi](media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png) 

3. [Azure Portal](https://portal.azure.com), uygulamayı dağıttığınız kaynak grubunu gözden geçirin ve açın.

   Uygulama Hizmeti bileşenlerinin ve SQL veritabanı sunucularının dağıtıldığı kaynaklara ve bölgeye dikkat edin.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>Kiracı yapılandırmasını katalogla eşitleme

Bu görevde, sunucuların, elastik havuzların ve veritabanlarının yapılandırmasını kiracı kataloğuna eşitlemek için bir işlem başlatabilirsiniz. Bu bilgiler daha sonra kurtarma bölgesinde bir yansıtma görüntüsü ortamı yapılandırmak için kullanılır.

> [!IMPORTANT]
> Kolaylık olması için, eşitleme işlemi ve uzun süre çalışan diğer kurtarma ve geri alma işlemleri bu örneklerde, istemci kullanıcı oturumu açma bölümünde çalışan yerel PowerShell işleri veya oturumları olarak uygulanır. Oturum açtığınızda verilen kimlik doğrulama belirteçleri birkaç saat sonra sona erdiğinde ve işler başarısız olur. Bir üretim senaryosunda, uzun süre çalışan süreçler, bir hizmet sorumlusu altında çalışan bazı tür güvenilir Azure hizmetleri olarak uygulanmalıdır. Bkz. [sertifika ile hizmet sorumlusu oluşturmak için Azure PowerShell kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal). 

1. PowerShell ıSE 'de. ..\Learning Modules\userconfig.exe dosyasını açın. 10 ve 11. satırlardaki `<resourcegroup>` ve `<user>`, uygulamayı dağıtırken kullanılan değerle değiştirin. Dosyayı kaydedin.

2. PowerShell ıSE 'de. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 betiği ' ni açın.

    Bu öğreticide, bu PowerShell betiğinin her bir senaryosunu çalıştırırsınız, bu yüzden bu dosyayı açık tutun.

3. Şunları ayarlayın:

    $DemoScenario = 1: kiracı sunucusunu ve havuz yapılandırma bilgilerini katalogla eşitlenen bir arka plan işi başlatın.

4. Eşitleme betiğini çalıştırmak için F5 ' i seçin. 

    Bu bilgiler daha sonra kurtarmanın kurtarma bölgesindeki sunucuların, havuzların ve veritabanlarının yansıtılmış bir görüntüsünü oluşturduğundan emin olmak için kullanılır.  

    ![Eşitleme işlemi](media/saas-dbpertenant-dr-geo-restore/sync-process.png)

PowerShell penceresini arka planda çalışır durumda bırakın ve Bu öğreticinin geri kalanına devam edin.

> [!NOTE]
> Eşitleme işlemi, kataloğa bir DNS diğer adı aracılığıyla bağlanır. Diğer ad, etkin kataloğa işaret etmek için geri yükleme ve yeniden yükleme sırasında değiştirilir. Eşitleme işlemi, kurtarma bölgesinde yapılan herhangi bir veritabanı veya havuz yapılandırma değişiklikleriyle kataloğu güncel tutar. Bu değişiklikler, repama sırasında özgün bölgedeki eşdeğer kaynaklara uygulanır.

## <a name="geo-restore-recovery-process-overview"></a>Coğrafi geri yükleme kurtarma işlemine genel bakış

Coğrafi geri yükleme kurtarma işlemi, uygulamayı dağıtır ve veritabanlarını yedeklerden kurtarma bölgesine geri yükler.

Kurtarma işlemi şunları yapar:

1. Özgün bölgedeki Web uygulaması için Azure Traffic Manager uç noktasını devre dışı bırakır. Uç noktanın devre dışı bırakılması, kullanıcıların, kurtarma sırasında özgün bölgenin çevrimiçi olması gerekir.

2. Kurtarma bölgesinde bir kurtarma kataloğu sunucusu sağlar, katalog veritabanını coğrafi olarak geri yükler ve activecatalog diğer adını geri yüklenen katalog sunucusuna işaret etmek üzere güncelleştirir. Katalog diğer adının değiştirilmesi, katalog eşitleme işleminin her zaman etkin katalogla eşitlenmesine olanak sağlar.

3. Geri yüklenmeden önce kiracı veritabanlarına erişimi engellemek için kurtarma kataloğundaki tüm mevcut kiracılar ' i çevrimdışı olarak işaretler.

4. Kurtarma bölgesinde uygulamanın bir örneğini sağlar ve bu bölgedeki geri yüklenen kataloğu kullanacak şekilde yapılandırır. Gecikme süresini en az bir durumda tutmak için örnek uygulama, her zaman aynı bölgedeki bir kiracı veritabanına bağlanacak şekilde tasarlanmıştır.

5. Yeni kiracıların sağlandığı bir sunucu ve elastik havuz sağlar. Bu kaynakların oluşturulması, yeni kiracının sağlanması, mevcut kiracıların kurtarılmasına engel olmamasını sağlar.

6. Yeni kiracı diğer adını, kurtarma bölgesindeki yeni kiracı veritabanları için sunucuya işaret etmek üzere güncelleştirir. Bu diğer adı değiştirmek, tüm yeni kiracılar için veritabanlarının kurtarma bölgesinde sağlanmasını sağlar.
        
7. , Kiracı veritabanlarını geri yüklemek için kurtarma bölgesinde sunucular ve elastik havuzlar sağlar. Bu sunucular ve havuzlar, özgün bölgedeki yapılandırmanın yansıtma görüntüsüdür. Sağlama havuzları ön uç, tüm veritabanlarını geri yüklemek için gereken kapasiteyi saklı tutar.

    Bir bölgedeki kesinti, eşleştirilmiş bölgede bulunan kaynaklara önemli bir basınç yerleştirebilir. DR için coğrafi geri yükleme kullanıyorsanız, kaynakları hızlı bir şekilde rezerve etmeniz önerilir. Bir uygulamanın belirli bir bölgede kurtarılması önemliyse, Coğrafi çoğaltmayı göz önünde bulundurun. 

8. Kurtarma bölgesindeki Web uygulaması için Traffic Manager uç noktasını etkinleştirilir. Bu uç noktanın etkinleştirilmesi, uygulamanın yeni kiracılar sağlamasına izin verir. Bu aşamada, mevcut kiracılar hala çevrimdışı.

9. Veritabanlarını öncelik sırasına göre geri yüklemek için istek toplu işlemleri gönderir. 

    * Toplu işler, veritabanlarının tüm havuzlarda paralel olarak geri yüklenmesi için düzenlenir.  

    * Geri yükleme istekleri zaman uyumsuz olarak gönderilir, böylece bunlar hızlı bir şekilde gönderilir ve her havuzda yürütülmek üzere sıraya alınır.

    * Geri yükleme istekleri tüm havuzlarda paralel olarak işlendiğinden, önemli kiracıların çoğunu birçok havuza dağıtmak daha iyidir. 

10. Veritabanlarının ne zaman geri yüklendiğini anlamak için SQL veritabanı hizmetini izler. Bir kiracı veritabanı geri yüklendikten sonra katalogda çevrimiçi olarak işaretlenir ve Kiracı veritabanı için bir rowversion toplamı kaydedilir. 

    * Kiracı veritabanlarına, katalogda çevrimiçi olarak işaretlendikleri anda, uygulama tarafından erişilebilir.

    * Kiracı veritabanındaki rowversion değerlerinin toplamı katalogda depolanır. Bu Sum, kurtarma bölgesinde veritabanının güncelleştirilip güncelleştirilmediğini belirlemesine izin veren bir parmak izi görevi görür.       

## <a name="run-the-recovery-script"></a>Kurtarma betiğini Çalıştır

> [!IMPORTANT]
> Bu öğretici, coğrafi olarak yedekli yedeklerden veritabanlarını geri yükler. Bu yedeklemeler genellikle 10 dakika içinde kullanılabilir olsa da, bir saat kadar sürebilir. Betik, kullanılabilir olana kadar duraklatılır.

Uygulamanın dağıtıldığı bölgede bir kesinti olduğunu düşünün ve kurtarma betiğini çalıştırın:

1. PowerShell ıSE 'de,. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 betiğine aşağıdaki değeri ayarlayın:

    $DemoScenario = 2: coğrafi olarak yedekli yedeklerden geri yükleyerek uygulamayı bir kurtarma bölgesine kurtarın.

2. Betiği çalıştırmak için F5 ' i seçin.  

    * Komut dosyası yeni bir PowerShell penceresinde açılır ve sonra paralel olarak çalışan bir PowerShell işleri kümesini başlatır. Bu işler, sunucuları, havuzları ve veritabanlarını kurtarma bölgesine geri yükler.

    * Kurtarma bölgesi, uygulamayı dağıttığınız Azure bölgesiyle ilişkili eşleştirilmiş bölgedir. Daha fazla bilgi için bkz. [Azure eşlenmiş bölgeler](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Kurtarma işleminin durumunu PowerShell penceresinde izleyin.

    ![Kurtarma işlemi](media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> Kurtarma işlerinin kodunu araştırmak için,. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\DR-RestoreFromBackup\RecoveryJobs klasöründeki PowerShell betiklerini inceleyin.

## <a name="review-the-application-state-during-recovery"></a>Kurtarma sırasında uygulama durumunu gözden geçirme
Uygulama uç noktası Traffic Manager devre dışı bırakılsa da, uygulama kullanılamaz. Katalog geri yüklenir ve tüm kiracılar çevrimdışı olarak işaretlenir. Kurtarma bölgesindeki uygulama uç noktası daha sonra etkinleştirilir ve uygulama yeniden çevrimiçi olur. Uygulama kullanılabilir olsa da, veritabanları geri yüklenene kadar kiracılar olayları hub 'ında çevrimdışı olarak görünür. Uygulamanızı çevrimdışı kiracı veritabanlarını işleyecek şekilde tasarlamak önemlidir.

* Katalog veritabanı kurtarıldıktan ve kiracılar yeniden çevrimiçi olduktan önce, Web tarayıcınızda Wingtip bilet olayları merkezini yenileyin.

  * Altbilgide, katalog sunucusu adının şimdi bir kurtarma sonekine sahip olduğunu ve kurtarma bölgesinde bulunduğunu unutmayın.

  * Henüz geri yüklenmemiş kiracıların çevrimdışı olarak işaretlendiğinden ve seçilemediğini unutmayın.   
 
    ![Kurtarma işlemi](media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)    

  * Kiracı çevrimdışıyken bir kiracının Olaylar sayfasını açarsanız, sayfada kiracı çevrimdışı bildirimi görüntülenir. Örneğin, contoso Concert Salı çevrimdışıysa http://events.wingtip-dpt.&ltaçmayı deneyin; User&gt;. trafficmanager.net/contosoconcerthall.

    ![Kurtarma işlemi](media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Kurtarma bölgesinde yeni bir kiracı sağlayın
Kiracı veritabanları geri yüklenmeden önce bile, kurtarma bölgesinde yeni kiracılar sağlayabilirsiniz. Kurtarma bölgesinde sağlanan yeni kiracı veritabanları, kurtarılan veritabanlarıyla daha sonra yeniden boyar.   

1. PowerShell ıSE 'de,. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 betiğine aşağıdaki özelliği ayarlayın:

    $DemoScenario = 3: kurtarma bölgesinde yeni bir kiracı sağlayın.

2. Betiği çalıştırmak için F5 ' i seçin.

3. Sağlama tamamlandığında, tarayıcıda, Havth, Salon Salonu olayları sayfası açılır. 

    , Havuz veritabanının kurtarma bölgesinde bulunduğundan emin olun.

    ![Kurtarma bölgesinde sağlanan havthtsalonu](media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. Tarayıcıda, Wingtip bilet olayları hub 'ını yenileyerek, havdan dahil edilen malzemeleri görüntüleyin. 

    Diğer kiracıları geri yüklemek için beklemeden, diğer kiracılar hala çevrimdışı olabilir.

## <a name="review-the-recovered-state-of-the-application"></a>Uygulamanın kurtarılan durumunu gözden geçirin

Kurtarma işlemi tamamlandığında, uygulama ve tüm kiracılar kurtarma bölgesinde tamamen işlevseldir. 

1. PowerShell konsolu penceresinde görüntülenen tüm kiracılar varsa, Olay Hub 'ını yenileyin. 

    Kiracıların hepsi, yeni kiracı, Havsalda dahil olmak üzere çevrimiçi olarak görünür.

    ![Olaylar hub 'ında kurtarılan ve yeni kiracılar](media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Contoso Concert Salı ' na tıklayın ve olayları sayfasını açın. 

    Altbilgide, veritabanının kurtarma bölgesinde bulunan kurtarma sunucusunda bulunduğundan emin olun.

    ![Kurtarma bölgesinde contoso](media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. [Azure Portal](https://portal.azure.com), kaynak grupları listesini açın.  

    Dağıttığınız kaynak grubuna ve kurtarma kaynak grubuna ve kurtarma sonekine sahip olun. Kurtarma kaynak grubu, kurtarma işlemi sırasında oluşturulan tüm kaynakları ve kesinti sırasında oluşturulan yeni kaynakları içerir. 

4. Kurtarma kaynak grubunu açın ve aşağıdaki öğelere dikkat edin:

   * -Recovery sonekiyle Katalog ve tenants1 sunucularının kurtarma sürümleri. Bu sunuculardaki geri yüklenen Katalog ve kiracı veritabanlarının hepsi özgün bölgede kullanılan adlara sahiptir.

   * Tenants2-DPT-&lt;User&gt;-Recovery SQL Server. Bu sunucu, kesinti sırasında yeni kiracılar sağlamak için kullanılır.

   * Olayları adlı App Service-Wingtip-DPT-&lt;recoveryregion&gt;-olaylar uygulamasının kurtarma örneği olan Kullanıcı&gt;&lt;.

     ![Kurtarma bölgesindeki contoso kaynakları](media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Tenants2-DPT-&lt;User&gt;-Recovery SQL Server 'ı açın. Hawthornhall veritabanı ve elastik havuz Pool1 içerdiğini unutmayın. Hawthornhall veritabanı, Pool1 elastik havuzunda elastik bir veritabanı olarak yapılandırılır.

## <a name="change-the-tenant-data"></a>Kiracı verilerini değiştirme 
Bu görevde, geri yüklenen kiracı veritabanlarından birini güncelleştirin. Repama işlemi, geri yüklenen veritabanlarını özgün bölgeye kopyalar. 

1. Tarayıcınızda, contoso Concert salonu için olaylar listesini bulun, olaylar arasında ilerleyin ve son olaya, önemli ölçüde dikkat edin.

2. PowerShell ıSE 'de,. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 betiğine aşağıdaki değeri ayarlayın:

    $DemoScenario = 4: kurtarma bölgesindeki bir kiracıdan bir olayı silin.

3. Betiği yürütmek için F5 ' i seçin.

4. Contoso Concert sald olayları sayfasını (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net/contosoconcerthall) yenileyin ve olay önemli deyimlerini eksik olduğuna dikkat edin.

Öğreticinin bu noktasında, artık kurtarma bölgesinde çalışan uygulamayı kurtardı. Kurtarma bölgesinde yeni bir kiracı sağladınız ve geri yüklenen Kiracılardan birinin verilerini değiştirmiş olursunuz.  

> [!NOTE]
> Örnekteki diğer öğreticiler, kurtarma durumunda uygulamayla çalışacak şekilde tasarlanmamıştır. Diğer öğreticileri araştırmak isterseniz, önce uygulamayı yeniden incelemeyi unutmayın.

## <a name="repatriation-process-overview"></a>Repama işlemine genel bakış

Yeniden yapılandırma işlemi, bir kesinti çözümlendikten sonra uygulamayı ve veritabanlarını özgün bölgesine geri döndürür.

![Coğrafi geri yükleme yeniden yükleme](media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png) 

İşlem:

1. Devam eden geri yükleme etkinliklerini durdurup bekleyen veya uçuş dışı veritabanı geri yükleme isteklerini iptal eder.

2. Kesinti sonrasında değiştirilmemiş olan özgün bölgede kiracı veritabanlarında yeniden etkinleştirir. Bu veritabanları henüz kurtarılmayan ve kurtarılan ancak daha sonra değişmemiş olanları içerir. Yeniden etkinleştirilen veritabanları, kiracılar tarafından tam olarak son erişimlerdir.

3. Yeni kiracının sunucusunun ve esnek havuzunun orijinal bölgede ayna görüntüsünü sağlar. Bu eylem tamamlandıktan sonra, yeni kiracı diğer adı bu sunucuyu işaret etmek üzere güncelleştirilir. Diğer adı güncelleştirme, yeni kiracı ekleme işleminin kurtarma bölgesi yerine özgün bölgede oluşmasına neden olur.

3. , Kataloğu kurtarma bölgesinden özgün bölgeye taşımak için Coğrafi çoğaltmayı kullanır.

4. Havuz yapılandırmasını, kesinti sırasında kurtarma bölgesinde yapılan değişikliklerle tutarlı olması için özgün bölgede güncelleştirir.

5. Kesinti sırasında oluşturulan tüm yeni veritabanlarını barındırmak için gerekli sunucuları ve havuzları oluşturur.

6. Geri yükleme sonrası güncelleştirilmiş geri yüklenen kiracı veritabanlarının ve kesinti sırasında sağlanan tüm yeni kiracı veritabanlarının yeniden onarımda olması için Coğrafi çoğaltmayı kullanır. 

7. Geri yükleme işlemi sırasında kurtarma bölgesinde oluşturulan kaynakları temizler.

Yeniden onarılamayan kiracı veritabanlarının sayısını sınırlandırmak için 1 ile 3 arasındaki adımlar hemen yapılır.  

4\. adım yalnızca kurtarma bölgesindeki Katalog kesinti sırasında değiştirilmişse yapılır. Yeni kiracılar oluşturulduysa veya kurtarma bölgesinde herhangi bir veritabanı veya havuz yapılandırması değiştirilirse Katalog güncelleştirilir.

Adım 7 ' nin kiracılar için en düşük kesintiye neden olmasının ve verilerin kaybedilmesi önemli değildir. Bu hedefe ulaşmak için, işlem coğrafi çoğaltma kullanır.

Her veritabanı coğrafi olarak çoğaltılmadan önce, özgün bölgedeki karşılık gelen veritabanı silinir. Kurtarma bölgesindeki veritabanı daha sonra coğrafi olarak çoğaltılır ve özgün bölgede ikincil bir çoğaltma oluşturulur. Çoğaltma tamamlandıktan sonra, kiracı katalogda çevrimdışı olarak işaretlenir ve bu, kurtarma bölgesindeki veritabanına olan bağlantıları keser. Daha sonra veritabanı yük devretmeye devam eder, ancak hiçbir bekleyen işlem ikincil üzerinde işleme sağlar, böylece hiç veri kaybı olmaz. 

Yük devretmede, veritabanı rolleri tersine çevrilir. Özgün bölgedeki ikincil, birincil okuma/yazma veritabanı olur ve kurtarma bölgesindeki veritabanı salt okunurdur. Katalogdaki kiracı girdisi, özgün bölgedeki veritabanına başvuracak şekilde güncelleştirilir ve kiracı çevrimiçi olarak işaretlenir. Bu noktada, veritabanının onarımına ilişkin bir değer tamamlanmıştır. 

Bağlantılar kesildiği zaman otomatik olarak yeniden bağlandıklarından emin olmak için uygulamaların yeniden deneme mantığı ile yazılması gerekir. Yeniden bağlantı kurmak için kataloğunu kullandıklarında, özgün bölgedeki bir önceki veritabanına bağlanırlar. Kısa bağlantı kesilmesi genellikle fark edilmese de, veritabanlarını iş saatleri dışına yeniden çekmeyi tercih edebilirsiniz.

Bir veritabanı yeniden onarımladıktan sonra, kurtarma bölgesindeki ikincil veritabanı silinebilir. Özgün bölgedeki veritabanı, daha sonra DR koruması için coğrafi geri yükleme ' yi kullanır.

8\. adımda, kurtarma sunucuları ve havuzları da dahil olmak üzere kurtarma bölgesindeki kaynaklar silinir.

## <a name="run-the-repatriation-script"></a>Repama betiğini çalıştırma
Kesinti çözümlendiğini ve repama betiğini çalıştırmayı düşünün.

Öğreticiyi takip ediyorsanız, komut dosyası değiştirilmeden oldukları için, özgün bölgedeki fabrikam Cakkkkkisini ve Dogwood Dojo 'yı hemen yeniden etkinleştirir. Daha sonra, değiştirilmiş olduğu için yeni kiracıyı ve contoso Concert salonu 'u yeniden boyar. Komut dosyası aynı zamanda, Havunsalya salonu sağlandığında güncelleştirilmiş olan kataloğu de boyar.
  
1. PowerShell ıSE 'de. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 betiği içinde, katalog eşitleme işleminin hala PowerShell örneğinde çalıştığını doğrulayın. Gerekirse, şu ayarları yaparak yeniden başlatın:

    $DemoScenario = 1: kiracı sunucusu, havuz ve veritabanı yapılandırma bilgilerini katalogla eşitlemeye başlayın.

    Betiği çalıştırmak için F5 ' i seçin.

2.  Ardından, yeniden deneme işlemini başlatmak için şunu ayarlayın:

    $DemoScenario = 5: uygulamayı özgün bölgesine yeniden boyar.

    Kurtarma betiğini yeni bir PowerShell penceresinde çalıştırmak için F5 ' i seçin. Repama, birkaç dakika sürer ve PowerShell penceresinde izlenebilir.

3. Betik çalışırken, Olay Hub 'ı sayfasını (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net) yenileyin.

    Bu süreç boyunca tüm kiracıların çevrimiçi ve erişilebilir olduğuna dikkat edin.

4. Bu öğeyi açmak için fabrikam caz kulübü seçin. Bu kiracıyı değiştirmediyseniz, altbilginin, sunucunun zaten özgün sunucuya geri döndürüldüğünü fark edersiniz.

5. Contoso Concert salon etkinlikleri sayfasını açın veya yenileyin. İlk olarak, veritabanının hala-kurtarma sunucusunda olduğunu belirten alt bilgiden dikkat edin. 

6. Repama işlemi tamamlandığında contoso Concert salonı olayları sayfasını yenileyin ve veritabanının şimdi özgün bölgenizde olduğunu fark edersiniz.

7. Olay Hub 'ını yeniden yenileyin ve sonra da bir açık Havsalda açın. Veritabanının özgün bölgede de bulunduğundan emin olun. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Kurtarma bölgesi kaynaklarını temizlemeden sonra temizle
Repama geri alma işlemi tamamlandıktan sonra kurtarma bölgesindeki kaynakları silmek güvenlidir. 

> [!IMPORTANT]
> Tüm faturalandırmaları durdurmak için bu kaynakları hemen silin.

Geri yükleme işlemi, bir kurtarma kaynak grubundaki tüm kurtarma kaynaklarını oluşturur. Temizleme işlemi bu kaynak grubunu siler ve katalogdaki kaynakların tüm başvurularını kaldırır. 

1. PowerShell ıSE 'de,. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 betiği ' nde şunları ayarlayın:
    
    $DemoScenario = 6: kurtarma bölgesinden kullanılmayan kaynakları silin.

2. Betiği çalıştırmak için F5 ' i seçin.

Betikleri temizledikten sonra uygulama başlatıldığı yerde geri gönderilir. Bu noktada, betiği yeniden çalıştırabilir veya diğer öğreticileri deneyebilirsiniz.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Uygulamanın ve veritabanının birlikte bulunduğundan emin olmak için uygulamayı tasarlama 
Uygulama, kiracının veritabanıyla aynı bölgedeki bir örnekten her zaman bağlanacak şekilde tasarlanmıştır. Bu tasarım, uygulama ve veritabanı arasındaki gecikmeyi azaltır. Bu en iyi duruma getirme, uygulamadan veritabanına etkileşimin, kullanıcıdan uygulamaya müdahaleden chattier olduğunu varsayar.  

Kiracı veritabanları, geri alma sırasında bir süre boyunca kurtarma ve özgün bölgelere yayılabilen olabilir. Her veritabanı için, uygulama, kiracı sunucusu adında bir DNS araması gerçekleştirerek veritabanının bulunduğu bölgeyi arar. SQL veritabanında sunucu adı bir diğer addır. Diğer ad sunucu adı, bölge adını içerir. Uygulama veritabanıyla aynı bölgede değilse, veritabanı sunucusuyla aynı bölgedeki örneğe yeniden yönlendirir. Veritabanı ile aynı bölgedeki örneğe yeniden yönlendirme, uygulama ve veritabanı arasındaki gecikmeyi en aza indirir.  

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> 
> * Bir yansıtma görüntüsü kurtarma ortamının başka bir bölgede oluşturulmasına izin veren düzenli aralıklarla yenilenen yapılandırma bilgilerini tutmak için kiracı kataloğunu kullanın.
> * Coğrafi geri yükleme kullanarak Azure SQL veritabanlarını kurtarma bölgesine kurtarın.
> * Kiracı kataloğunu, geri yüklenen Kiracı veritabanı konumlarını yansıtacak şekilde güncelleştirin. 
> * Bir uygulamanın kiracı kataloğuna yeniden yapılandırma olmadan bağlanmasını sağlamak için bir DNS diğer adı kullanın.
> * Bir kesinti çözümlendikten sonra kurtarılan veritabanlarını özgün bölgelerine yeniden eklemek için Coğrafi çoğaltmayı kullanın.

Büyük ölçekli çok kiracılı bir uygulamayı kurtarmak için gereken süreyi önemli ölçüde azaltmak üzere Coğrafi çoğaltmayı nasıl kullanacağınızı öğrenmek için, [çok kiracılı SaaS uygulaması için veritabanı coğrafi çoğaltma öğreticisini kullanarak olağanüstü durum kurtarmayı](saas-dbpertenant-dr-geo-replication.md) deneyin.

## <a name="additional-resources"></a>Ek kaynaklar

[Wingtip SaaS uygulaması üzerine inşa eden ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
