---
title: Coğrafi çoğaltma ile SaaS uygulamaları için olağanüstü durum kurtarma
description: Bir kesinti durumunda çok kiracılı SaaS uygulamasını kurtarmak için Azure SQL veritabanı coğrafi çoğaltmalarını nasıl kullanacağınızı öğrenin
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 0668ccf5ceb972dd120e4e3f37be6d879a12d0a7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811717"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Veritabanı coğrafi çoğaltma kullanarak çok kiracılı SaaS uygulaması için olağanüstü durum kurtarma

Bu öğreticide, kiracı başına veritabanı modeli kullanılarak gerçekleştirilen çok kiracılı bir SaaS uygulaması için tam bir olağanüstü durum kurtarma senaryosunu araştırdığınızda. Uygulamayı kesintiye karşı korumak için, alternatif bir kurtarma bölgesinde Katalog ve kiracı veritabanları için çoğaltmalar oluşturmak üzere [_Coğrafi çoğaltmayı_](sql-database-geo-replication-overview.md) kullanırsınız. Bir kesinti oluşursa, normal iş işlemlerini sürdürmeniz için bu çoğaltmalara hızlı bir şekilde yük devreolursunuz. Yük devretmede, özgün bölgedeki veritabanları kurtarma bölgesindeki veritabanlarının ikincil çoğaltmaları haline gelir. Bu çoğaltmalar yeniden çevrimiçi olduktan sonra, kurtarma bölgesindeki veritabanlarının durumuna otomatik olarak gelir. Kesinti çözümlendikten sonra özgün üretim bölgesindeki veritabanlarına geri dönebilirsiniz.

Bu öğretici hem yük devretme hem de yeniden çalışma iş akışlarını araştırır. Şunları öğrenirsiniz:
> [!div class="checklist"]
> 
> * Veritabanı ve elastik havuz yapılandırma bilgilerini kiracı kataloğu ile eşitleyin
> * Farklı bir bölgede, uygulama, sunucular ve havuzlardan oluşan bir kurtarma ortamı ayarlama
> * Katalog ve kiracı veritabanlarını kurtarma bölgesine çoğaltmak için _Coğrafi çoğaltmayı_ kullanın
> * Uygulama ve Katalog ve kiracı veritabanlarının yükünü kurtarma bölgesine devreder 
> * Daha sonra, kesinti çözümlendikten sonra uygulamanın, kataloğun ve kiracı veritabanlarının yükünü özgün bölgeye geri devreder
> * Her bir kiracı veritabanının, her bir kiracının veritabanının birincil konumunu izlemek için yük devretdiği için kataloğu Güncelleştir
> * Gecikme süresini azaltmak için uygulamanın ve birincil kiracı veritabanının aynı Azure bölgesinde her zaman açık olduğundan emin olun  
 

Bu öğreticiye başlamadan önce, aşağıdaki önkoşulların tamamlandığından emin olun:
* Kiracı uygulaması başına Wingtip bilet SaaS veritabanı dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md)  
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Coğrafi çoğaltma kurtarma düzenine giriş

![Kurtarma mimarisi](media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)
 
Olağanüstü durum kurtarma (DR), uyumluluk nedenleri veya iş sürekliliği gibi birçok uygulama için önemli bir konudur. Uzun süren bir hizmet kesintisi olması gerekir, iyi hazırlanmış bir DR planı İş kesintisini en aza indirebilir. Coğrafi çoğaltmanın kullanılması, veritabanı çoğaltmalarını kısa bildirimde yük devredilecek bir kurtarma bölgesinde tutarak en düşük RPO ve RTO 'ı sağlar.

Coğrafi çoğaltmayı temel alan bir DR planı üç ayrı bölümden oluşur:
* Kurtarma ortamının kurulumunu ve bakımını oluşturma
* Kurtarma-bir kesinti oluşursa uygulama ve veritabanlarının kurtarma ortamına yük devretmesi,
* Yeniden atma-uygulama çözümlendikten sonra uygulamanın ve veritabanlarının özgün bölgeye geri yük devretmesi 

Özellikle ölçeklendirmeye çalışıyorsanız, tüm parçalar dikkatle düşünülmelidir. Genel olarak, plan birkaç hedefi yerine getirmelidir:

* Kurulum
    * Kurtarma bölgesinde bir yansıtma görüntüsü ortamı oluşturun ve saklayın. Elastik havuzlar oluşturma ve bu kurtarma ortamındaki veritabanlarının çoğaltılması, kapasiteyi kurtarma bölgesinde ayırır. Bu ortamın sürdürülmesi, yeni kiracı veritabanlarının sağlandığı gibi çoğaltılmasını içerir.  
* Kurtarma
    * Günlük maliyetleri en aza indirmek için ölçeklendirilmiş bir kurtarma ortamının kullanıldığı, havuzların ve veritabanlarının kurtarma bölgesinde tam işlem kapasitesi elde etmek üzere ölçeği artırmalıdır.
    * Kurtarma bölgesinde mümkün olan en kısa sürede yeni kiracı sağlamasını etkinleştir  
    * Kiracıların öncelik sırasına geri yüklenmesi için en iyi duruma getirilmesi
    * Adımları paralel olarak, mümkün olduğunca hızlı bir şekilde gerçekleştirmek için en iyi duruma getirme
    * Hataya dayanıklı, yeniden başlatılabilir ve ıdempotent
    * Özgün bölge çevrimiçi olarak geri geliyorsa, orta uçuşdaki işlemi iptal etmek mümkün olacaktır.
* Repama 
    * Kiracılar için en az etkiyle, kurtarma bölgesindeki veritabanlarının yükünü, özgün bölgedeki çoğaltmalar için yük devretme: kiracı başına veri kaybı ve en düşük süre.   

Bu öğreticide, Azure SQL veritabanı ve Azure platformunun özellikleri kullanılarak bu sorunlar ele alınır:

* Tüm gerekli kapasiteyi mümkün olduğunca hızlı bir şekilde ayırmak için [şablonlar Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template). Azure Resource Manager şablonlar, kurtarma bölgesindeki üretim sunucularının ve elastik havuzların ayna görüntüsünü sağlamak için kullanılır.
* [Coğrafi çoğaltma](sql-database-geo-replication-overview.md), tüm veritabanları için zaman uyumsuz olarak çoğaltılan salt okunurdur. Kesinti sırasında, kurtarma bölgesindeki çoğaltmalara yük devreder.  Kesinti çözümlendikten sonra, özgün bölgedeki veritabanlarına veri kaybı olmadan geri dönebilirsiniz.
* Çok sayıda veritabanı için yük devretme süresini en aza indirmek amacıyla kiracı öncelikli sırada gönderilen [zaman uyumsuz](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) yük devretme işlemleri.
* Kurtarma ve geri dönüştürme sırasında katalogdaki veritabanı girişlerini değiştirmek için parça [Yönetimi kurtarma özellikleri](sql-database-elastic-database-recovery-manager.md). Bu özellikler, uygulamanın, uygulamayı yeniden yapılandırmadan bağımsız olarak kiracı veritabanlarına bağlanmasına izin verir.
* Uygulamanın hangi bölgeden çalıştığı ile bağımsız olarak yeni kiracıların sorunsuz sağlanmasını sağlamak için [SQL Server DNS diğer adları](dns-alias-overview.md). DNS diğer adları, katalog eşitleme işleminin, konumundan bağımsız olarak etkin kataloğa bağlanmasına izin vermek için de kullanılır.

## <a name="get-the-disaster-recovery-scripts"></a>Olağanüstü durum kurtarma betiklerini al 

> [!IMPORTANT]
> Tüm Wingtip bilet yönetimi betiklerinde olduğu gibi, DR betikleri örnek kalitelidir ve üretimde kullanılmamalıdır. 

Bu öğreticide kullanılan kurtarma betikleri ve Wingtip uygulama kaynak kodu, [kiracı GitHub deposu başına Wingtip bilet SaaS veritabanında](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/)bulunur. Wingtip bilet yönetim betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

## <a name="tutorial-overview"></a>Öğreticiye genel bakış
Bu öğreticide, ilk olarak Wingtip bilet uygulamasının ve veritabanlarının farklı bir bölgedeki çoğaltmaları oluşturmak için Coğrafi çoğaltmayı kullanırsınız. Daha sonra bu bölgeye yük devretmek için yük devretme işleminin kesintiye dönmesi gerekir. Bu tamamlandığında, uygulama kurtarma bölgesinde tamamen işlevseldir.

Daha sonra, ayrı bir yeniden bir geri alma adımında, kurtarma bölgesindeki Katalog ve kiracı veritabanlarının sürümünü özgün bölgeye devreder. Uygulama ve veritabanları, yeniden onarımlama boyunca kullanılabilir kalır. Bu tamamlandığında, uygulama özgün bölgede tamamen işlevseldir.

> [!Note]
> Uygulama, uygulamanın dağıtıldığı bölgenin _eşleştirilmiş bölgesine_ kurtarılır. Daha fazla bilgi için bkz. [Azure eşlenmiş bölgeler](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="review-the-healthy-state-of-the-application"></a>Uygulamanın sağlıklı durumunu gözden geçirin

Kurtarma işlemine başlamadan önce, uygulamanın normal sağlıklı durumunu gözden geçirin.
1. Web tarayıcınızda, Wingtip bilet olayları hub 'ını açın (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net-&lt;Kullanıcı&gt; dağıtımınızın Kullanıcı değeriyle değiştirin).
    * Sayfanın alt kısmına ilerleyin ve altbilgide katalog sunucusu adı ve konumuna dikkat edin. Konum, uygulamayı dağıttığınız bölgedir.
    *İpucu: ekranı büyütmek için fareyi konumun üzerine getirin.* özgün bölgede ![Olay Hub 'ı sağlıklı durumunu
    ](media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Contoso Concert Salı kiracısına tıklayın ve olay sayfasını açın.
    * Altbilgide, kiracı sunucu adına dikkat edin. Konum, katalog sunucusunun konumuyla aynı olacaktır.

3. [Azure Portal](https://portal.azure.com), uygulamanın dağıtıldığı kaynak grubunu açın
    * Sunucularının dağıtıldığı bölgeye dikkat edin. 

## <a name="sync-tenant-configuration-into-catalog"></a>Kiracı yapılandırmasını katalogla eşitleme

Bu görevde, sunucuların, elastik havuzların ve veritabanlarının yapılandırmalarını kiracı kataloğu 'nda eşitlenen bir işlem başlatabilirsiniz. İşlem bu bilgileri katalogda güncel tutar.  İşlem, özgün bölgede veya kurtarma bölgesinde olan etkin katalogla birlikte kullanılır. Yapılandırma bilgileri, kurtarma ortamının orijinal ortamla tutarlı olduğundan emin olmak için kurtarma sürecinin bir parçası olarak kullanılır ve daha sonra özgün bölgenin bu şekilde yapılan değişikliklerle tutarlı hale getirildiğinden emin olmak için kurtarma ortamı. Katalog, kiracı kaynaklarının kurtarma durumunu izlemek için de kullanılır

> [!IMPORTANT]
> Kolaylık olması için, eşitleme işlemi ve diğer uzun süre çalışan kurtarma ve geri alma işlemleri bu öğreticilerde, istemci kullanıcı oturumu açma bölümünde çalışan yerel PowerShell işleri veya oturumları olarak uygulanır. Oturum açtığınızda verilen kimlik doğrulama belirteçleri, birkaç saat sonra sona erer ve sonra işler başarısız olur. Bir üretim senaryosunda, uzun süre çalışan süreçler, bir hizmet sorumlusu altında çalışan bazı tür güvenilir Azure hizmetleri olarak uygulanmalıdır. Bkz. [sertifika ile hizmet sorumlusu oluşturmak için Azure PowerShell kullanma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal).

1. _PowerShell ISE_'de. ..\Learning Modules\userconfig.exe dosyasını açın. 10 ve 11. satırlardaki `<resourcegroup>` ve `<user>`, uygulamayı dağıtırken kullanılan değerle değiştirin.  Dosyayı kaydedin!

2. *PowerShell ISE*'de. ..\Learning Modules\Business Continuity ve olağanüstü durum Recovery\dr-failovertoreplica\demo-failovertoreplica.exe ve set ' i açın:
    * **$DemoScenario = 1**, kiracı sunucusunu eşitlenen bir arka plan işi başlatın ve yapılandırma bilgilerini katalogla havuzlayın

3. Eşitleme betiğini çalıştırmak için **F5** tuşuna basın. Kiracı kaynaklarının yapılandırmasını eşitlemek için yeni bir PowerShell oturumu açılır.
Eşitleme işlemini ![](media/saas-dbpertenant-dr-geo-replication/sync-process.png)

PowerShell penceresini arka planda çalışır durumda bırakın ve öğreticinin geri kalanı ile devam edin. 

> [!Note]
> Eşitleme işlemi, kataloğa bir DNS diğer adı aracılığıyla bağlanır. Bu diğer ad, etkin kataloğa işaret etmek için geri yükleme ve yeniden yükleme sırasında değiştirilir. Eşitleme işlemi, kurtarma bölgesinde yapılan herhangi bir veritabanı veya havuz yapılandırma değişiklikleriyle kataloğu güncel tutar.  Bu değişiklikler, repama sırasında özgün bölgedeki eşdeğer kaynaklara uygulanır.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Kurtarma bölgesinde ikincil veritabanı çoğaltmaları oluşturma

Bu görevde, yinelenen bir uygulama örneği dağıtan ve kataloğu ve tüm kiracı veritabanlarını bir kurtarma bölgesine çoğaltan bir işlem başlatır.

> [!Note]
> Bu öğretici, Wingtip bilet örnek uygulamasına coğrafi çoğaltma koruması ekler. Coğrafi çoğaltma kullanan bir uygulama için bir üretim senaryosunda, her kiracı, coğrafi olarak çoğaltılan bir veritabanıyla birlikte kaynak olarak sağlanabilir. Bkz. [Azure SQL veritabanı 'nı kullanarak yüksek oranda kullanılabilir hizmetler tasarlama](sql-database-designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. *PowerShell ISE*'de. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\dr-failovertoreplica\demo-failovertoreplica.exe. ps1 betiğini açın ve aşağıdaki değerleri ayarlayın:
    * **$DemoScenario = 2**, yansıtma görüntüsü kurtarma ortamı oluşturma ve Katalog ve kiracı veritabanlarını çoğaltma

2. Betiği çalıştırmak için **F5**'e basın. Çoğaltmaları oluşturmak için yeni bir PowerShell oturumu açıldı.
Eşitleme işlemini ![](media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>Normal uygulama durumunu gözden geçirme

Bu noktada, uygulama normal olarak özgün bölgede çalışıyor ve şimdi coğrafi çoğaltma tarafından korunuyor.  Tüm veritabanları için kurtarma bölgesinde bulunan salt okunurdur. 

1. Azure portal, Kaynak gruplarınızı inceleyin ve kurtarma bölgesinde kurtarma son eki ile bir kaynak grubu oluşturulduğunu unutmayın. 

2. Kurtarma kaynak grubundaki kaynakları keşfet.  

3. _Tenants1-DPT-&lt;user&gt;-Recovery_ Server üzerindeki contoso Concert salonu veritabanına tıklayın.  Sol tarafta coğrafi çoğaltma ' ya tıklayın. 

    ![Contoso Concert coğrafi çoğaltma bağlantısı](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Azure bölgeleri eşlemesinde, özgün bölgedeki birincil ve kurtarma bölgesindeki ikincil arasındaki coğrafi çoğaltma bağlantısını aklınızda bir yere göz önünde.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Uygulamanın yükünü kurtarma bölgesine devreder

### <a name="geo-replication-recovery-process-overview"></a>Coğrafi çoğaltma kurtarma işlemine genel bakış

Kurtarma betiği aşağıdaki görevleri gerçekleştirir:

1. Özgün bölgedeki Web uygulaması için Traffic Manager uç noktasını devre dışı bırakır. Uç noktanın devre dışı bırakılması, kullanıcıların, kurtarma sırasında özgün bölgenin çevrimiçi olması gerekir.

1. , Birincil veritabanını yapmak için kurtarma bölgesindeki Katalog veritabanının yük devretmesini zorla kullanır ve _activecatalog_ diğer adını kurtarma kataloğu sunucusuna işaret etmek üzere güncelleştirir.

1. _Newtenant_ diğer adını, kurtarma bölgesindeki kiracı sunucusunu işaret etmek üzere güncelleştirir. Bu diğer adı değiştirmek, tüm yeni kiracılar için veritabanlarının kurtarma bölgesinde sağlanmış olmasını sağlar. 

1. Yük devredilebilmeleri için kiracı veritabanlarına erişimi engellemek üzere Kurtarma kataloğundaki tüm mevcut kiracılarını çevrimdışı olarak işaretler.

1. Tüm elastik havuzların yapılandırmasını güncelleştirir ve kurtarma bölgesindeki tek veritabanlarının çoğaltılan veritabanlarını özgün bölgede yansıtmasını sağlar. (Bu görev yalnızca, kurtarma ortamındaki havuzlar veya çoğaltılan veritabanlarının maliyetleri azaltmak için normal işlemler sırasında ölçeği azaltılırsa gereklidir).

1. Kurtarma bölgesindeki Web uygulaması için Traffic Manager uç noktasını etkinleştirilir. Bu uç noktanın etkinleştirilmesi, uygulamanın yeni kiracılar sağlamasına izin verir. Bu aşamada, mevcut kiracılar hala çevrimdışı.

1. Veritabanlarını yük devretmek için öncelik sırasına göre istek toplu işlemleri gönderir.
    * Toplu işler, veritabanlarının tüm havuzlarda paralel yük devretmesine olanak sağlayacak şekilde düzenlenir.
    * Yük devretme istekleri zaman uyumsuz işlemler kullanılarak gönderilir ve bu sayede hızlı bir şekilde gönderilir ve birden çok istek aynı anda işlenebilir.

   > [!Note]
   > Kesinti senaryosunda, özgün bölgedeki birincil veritabanları çevrimdışı.  İkincil üzerinde yük devretmek, kalan bekleyen işlemleri uygulamaya çalışmamaya gerek kalmadan birincili bağlantıyı keser. Bu öğreticide olduğu gibi bir DR detaya gitme senaryosunda, yük devretme sırasında herhangi bir güncelleştirme etkinliği varsa, bazı veri kaybı olabilir. Daha sonra, yeniden atma sırasında, kurtarma bölgesindeki veritabanlarının yükünü özgün bölgeye geri devretmek için bir veri kaybı olmadığından emin olmak için normal bir yük devretme kullanılır.

1. Veritabanlarının yük devredildiğini öğrenmek için SQL veritabanı hizmetini izler. Bir kiracı veritabanının yük devrettikten sonra, kiracı veritabanının kurtarma durumunu kaydetmek ve kiracıyı çevrimiçi olarak işaretlemek için Kataloğu güncelleştirir.
    * Kiracı veritabanlarına, katalogda çevrimiçi olarak işaretlendikleri anda, uygulama tarafından erişilebilir.
    * Kiracı veritabanındaki rowversion değerlerinin toplamı katalogda depolanır. Bu değer, kurtarma bölgesinde veritabanının güncelleştirilip güncelleştirilmediğini belirlemesine izin veren bir parmak izi görevi görür.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>Kurtarma bölgesinde yük devretmek için betiği çalıştırın

Şimdi uygulamanın dağıtıldığı bölgede bir kesinti olduğunu ve kurtarma betiğini çalıştırmayı düşünün:

1. *PowerShell ISE*'de. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\dr-failovertoreplica\demo-failovertoreplica.exe. ps1 betiğini açın ve aşağıdaki değerleri ayarlayın:
    * **$DemoScenario = 3**, çoğaltmaya yük devreterek uygulamayı bir kurtarma bölgesine kurtarın

2. Betiği çalıştırmak için **F5**'e basın.  
    * Komut dosyası yeni bir PowerShell penceresinde açılır ve sonra paralel olarak çalışan bir dizi PowerShell işi başlatır. Bu işler, kiracı veritabanlarının yükünü kurtarma bölgesine devreder.
    * Kurtarma bölgesi, uygulamayı dağıttığınız Azure bölgesiyle ilişkili _eşleştirilmiş bölgedir_ . Daha fazla bilgi için bkz. [Azure eşlenmiş bölgeler](https://docs.microsoft.com/azure/best-practices-availability-paired-regions). 

3. Kurtarma işleminin durumunu PowerShell penceresinde izleyin.
    Yük devretme işlemi ![](media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> Kurtarma işlerinin kodunu araştırmak için,. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\drtoreplica\recoveryjobs klasöründeki PowerShell betiklerini inceleyin.

### <a name="review-the-application-state-during-recovery"></a>Kurtarma sırasında uygulama durumunu gözden geçirme

Uygulama uç noktası Traffic Manager devre dışı bırakılsa da, uygulama kullanılamaz. Katalog yük devretmeden kurtarma bölgesine ve çevrimdışı olarak işaretlenen tüm kiracılara devredildikten sonra, uygulama yeniden çevrimiçi duruma getirilir. Uygulama kullanılabilir olsa da, veritabanı yük devretene kadar her kiracı, olaylar hub 'ında çevrimdışı olarak görünür. Uygulamanızı çevrimdışı kiracı veritabanlarını işleyecek şekilde tasarlamak önemlidir.

1. Katalog veritabanı kurtarıldıktan hemen sonra, Web tarayıcınızda Wingtip bilet olayları merkezini yenileyin.
   * Altbilgide, katalog sunucusu adının şimdi bir _Kurtarma_ sonekine sahip olduğunu ve kurtarma bölgesinde bulunduğunu unutmayın.
   * Henüz geri yüklenmemiş kiracılar, çevrimdışı olarak işaretlenir ve seçilemediğini fark edebilirsiniz.  

     > [!Note]
     > Kurtarmak için yalnızca birkaç veritabanı sayesinde, kurtarma tamamlanmadan önce tarayıcıyı yenileyemeyebilirsiniz, bu nedenle kiracılar çevrimdışıyken görünmeyebilir. 
 
     ![Etkinlik Hub 'ı çevrimdışı](media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Bir çevrimdışı kiracının Olaylar sayfasını doğrudan açarsanız, ' kiracı çevrimdışı ' bildirimini görüntüler. Örneğin, contoso Concert Salı çevrimdışıysa http://events.wingtip-dpt.&ltaçmayı deneyin; User&gt;. trafficmanager.net/contosoconcerthall ![contoso çevrimdışı sayfası](media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Kurtarma bölgesinde yeni bir kiracı sağlayın
Tüm mevcut kiracı veritabanlarının yük devretmemesine karşın, kurtarma bölgesinde yeni kiracılar sağlayabilirsiniz.  

1. *PowerShell ISE*'de. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\dr-failovertoreplica\demo-failovertoreplica.exe. ps1 betiğini açın ve aşağıdaki özelliği ayarlayın:
    * **$DemoScenario = 4**, kurtarma bölgesinde yeni bir kiracı sağlayın

2. Betiği çalıştırmak ve yeni kiracıyı sağlamak için **F5** tuşuna basın. 

3. Bu işlem tamamlandığında, tarayıcıda, Havth, Salon Salonu olayları sayfası açılır. Ana bilgisayardan, bir kurtarma bölgesinde, Havthtsalya salonu veritabanının sağlandığını unutmayın.
    ![,](media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. Tarayıcıda, Wingtip bilet olayları hub 'ını yenileyerek, havdan dahil edilen malzemeleri görüntüleyin. 
    * Diğer kiracıları geri yüklemek için beklemeden, diğer kiracılar hala çevrimdışı olabilir.


## <a name="review-the-recovered-state-of-the-application"></a>Uygulamanın kurtarılan durumunu gözden geçirin

Kurtarma işlemi tamamlandığında, uygulama ve tüm kiracılar kurtarma bölgesinde tamamen işlevseldir. 

1. PowerShell konsolu penceresinde görüntülenen tüm kiracılar varsa, Olay Hub 'ını yenileyin.  Kiracıların hepsi, yeni kiracı, Havsalya salonu dahil olmak üzere çevrimiçi olarak görünür.

    ![Olaylar hub 'ında kurtarılan ve yeni kiracılar](media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. [Azure Portal](https://portal.azure.com), kaynak grupları listesini açın.  
    * Dağıttığınız kaynak grubuna ve kurtarma kaynak _grubuna ve kurtarma sonekine sahip_ olun.  Kurtarma kaynak grubu, kurtarma işlemi sırasında oluşturulan tüm kaynakları ve kesinti sırasında oluşturulan yeni kaynakları içerir.  

3. Kurtarma kaynak grubunu açın ve aşağıdaki öğelere dikkat edin:
   * Katalog ve tenants1 sunucularının kurtarma sürümleri ve _Kurtarma_ son eki.  Bu sunuculardaki geri yüklenen Katalog ve kiracı veritabanlarının hepsi özgün bölgede kullanılan adlara sahiptir.

   * _Tenants2-DPT-&lt;user&gt;-Recovery_ SQL Server.  Bu sunucu, kesinti sırasında yeni kiracılar sağlamak için kullanılır.
   * Olaylar uygulamasının kurtarma örneği olan _&lt;user & gt; -, olaylar-Wingtip-DPT-&lt;recoveryregion&gt;_ adlı App Service. 

     ![Azure kurtarma kaynakları](media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 
    
4. _Tenants2-DPT-&lt;user&gt;-Recovery_ SQL Server 'ı açın.  _Hawthornhall_ veritabanı ve elastik havuz, _Pool1_içerdiğini unutmayın.  _Hawthornhall_ veritabanı, _Pool1_ elastik havuzda esnek veritabanı olarak yapılandırılır.

5. Kaynak grubuna geri gidin ve _tenants1-DPT-&lt;user&gt;-Recovery_ Server üzerindeki contoso Concert salonu veritabanına tıklayın. Sol tarafta coğrafi çoğaltma ' ya tıklayın.
    
    ![Yük devretmeden sonra contoso veritabanı](media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Kiracı verilerini değiştirme 
Bu görevde, kiracı veritabanlarından birini güncelleştirin. 

1. Tarayıcınızda, contoso Concert salonu için olaylar listesini bulun ve son olay adını göz önünde bulun.
2. *PowerShell ISE*'de. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\dr-failovertoreplica\demo-failovertoreplica.exe. ps1 komut dosyasında aşağıdaki değeri ayarlayın:
    * **$DemoScenario = 5** Kurtarma bölgesindeki bir kiracıdan bir olayı silme
3. Betiği yürütmek için **F5** 'e basın
4. Contoso Concert Saletkinlikleri sayfasını (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net/contosoconcerthall-&lt;Kullanıcı&gt; dağıtımınızın Kullanıcı değeriyle değiştirin) ve son olayın silindiğini görürsünüz.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Uygulamayı özgün üretim bölgesine yeniden boyar

Bu görev, uygulamayı özgün bölgesine ayırır. Gerçek bir senaryoda kesinti çözümlenirse yeniden tarama işlemi başlatabilirsiniz.

### <a name="repatriation-process-overview"></a>Repama işlemine genel bakış

![Repama mimarisi](media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

Repama işlemi:
1. Tüm bekleyen veya uçuş veritabanı geri yükleme isteklerini iptal eder.
2. _Newtenant_ diğer adını, kaynak bölgesindeki kiracılar sunucusuna işaret etmek üzere güncelleştirir. Bu diğer adı değiştirmek, yeni kiracılar için veritabanlarının artık kaynak bölgede sağlanmasını sağlar.
3. Değiştirilen tüm kiracı verilerini özgün bölgeye göre çevir
4. Kiracı veritabanlarının üzerinden öncelik sıralaması başarısız olur.

Yük devretme, veritabanını özgün bölgeye etkin bir şekilde taşıdıkça. Veritabanı yük devri yaptığında, herhangi bir açık bağlantı bırakılır ve veritabanı birkaç saniye boyunca kullanılamaz. Yeniden bağlandıklarından emin olmak için uygulamaların yeniden deneme mantığı ile yazılması gerekir.  Bu kısa bağlantı kesilmesi genellikle fark edilmese de, veritabanlarını iş saatleri dışına yeniden çekmeyi tercih edebilirsiniz. 


### <a name="run-the-repatriation-script"></a>Repama betiğini çalıştırma
Şimdi kesinti çözümlendiğini ve repama betiğini çalıştırmayı düşünün.

1. *PowerShell ISE*'de. ..\Learning Modules\iş sürekliliği ve olağanüstü durum Recovery\dr-failovertoreplica\demo-failovertoreplica.exe.

2. Katalog eşitleme işleminin hala PowerShell örneğinde çalıştığını doğrulayın.  Gerekirse, şu ayarları yaparak yeniden başlatın:
    * **$DemoScenario = 1**, kiracı sunucusu, havuz ve veritabanı yapılandırma bilgilerini katalogla eşitlemeye başlayın
    * Betiği çalıştırmak için **F5**'e basın.

3.  Ardından, yeniden deneme işlemini başlatmak için şunu ayarlayın:
    * **$DemoScenario = 6**, uygulamayı özgün bölgesine yeniden boyar
    * Kurtarma betiğini yeni bir PowerShell penceresinde çalıştırmak için **F5** tuşuna basın.  Repama, birkaç dakika sürer ve PowerShell penceresinde izlenebilir.
    ![Repama işlemi](media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. Betik çalışırken, Olay Hub 'ı sayfasını (http://events.wingtip-dpt.&lt; User&gt;. trafficmanager.net) yenileyin
    * Bu süreç boyunca tüm kiracıların çevrimiçi ve erişilebilir olduğuna dikkat edin.

5. Yeniden atma işlemi tamamlandıktan sonra, Olay Hub 'ını yenileyin ve ayrıntılı Salkayı için Olaylar sayfasını açın. Bu veritabanının özgün bölgeye yeniden onarımduğuna dikkat edin.
    ![Olay Hub 'ı repaılan](media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Uygulamanın ve veritabanının birlikte bulunduğundan emin olmak için uygulamayı tasarlama 
Uygulama, her zaman kiracı veritabanıyla aynı bölgedeki bir örnekten bağlanacak şekilde tasarlanmıştır. Bu tasarım, uygulama ve veritabanı arasındaki gecikmeyi azaltır. Bu en iyi duruma getirme, uygulamadan veritabanına etkileşimin, kullanıcıdan uygulamaya müdahaleden chattier olduğunu varsayar.  

Kiracı veritabanları, geri alma sırasında bir süre boyunca kurtarma ve özgün bölgelere yayılabilir. Her veritabanı için, uygulama, kiracı sunucusu adında bir DNS araması gerçekleştirerek veritabanının bulunduğu bölgeyi arar. SQL veritabanında sunucu adı bir diğer addır. Diğer ad sunucu adı, bölge adını içerir. Uygulama veritabanıyla aynı bölgede değilse, veritabanı sunucusuyla aynı bölgedeki örneğe yeniden yönlendirir.  Veritabanıyla aynı bölgedeki örneğe yeniden yönlendirme, uygulama ve veritabanı arasındaki gecikmeyi en aza indirir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunları öğrendiniz:
> [!div class="checklist"]
> 
> * Veritabanı ve elastik havuz yapılandırma bilgilerini kiracı kataloğu ile eşitleyin
> * Farklı bir bölgede, uygulama, sunucular ve havuzlardan oluşan bir kurtarma ortamı ayarlama
> * Katalog ve kiracı veritabanlarını kurtarma bölgesine çoğaltmak için _Coğrafi çoğaltmayı_ kullanın
> * Uygulama ve Katalog ve kiracı veritabanlarının yükünü kurtarma bölgesine devreder 
> * Kesinti çözümlendikten sonra uygulama, Katalog ve kiracı veritabanlarını özgün bölgeye geri dönme

[Iş sürekliliği genel bakış](sql-database-business-continuity.md) belgelerinde iş sürekliliği sağlamak IÇIN Azure SQL veritabanı 'nın sağladığı teknolojiler hakkında daha fazla bilgi edinebilirsiniz.

## <a name="additional-resources"></a>Ek kaynaklar

* [Wingtip SaaS uygulaması üzerine inşa eden ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
