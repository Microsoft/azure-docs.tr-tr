---
title: Parçalı bir çok kiracılı veritabanının performansını izleme
description: Çok kiracılı bir SaaS uygulamasında parçalı, çok kiracılı Azure SQL veritabanı 'nın performansını izleme ve yönetme
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: cc8ccbbde56b57af684ad47840002a846bdcd8c0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827960"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Çok kiracılı bir SaaS uygulamasında parçalı, çok kiracılı Azure SQL veritabanı 'nın performansını izleme ve yönetme

Bu öğreticide, SaaS uygulamalarında kullanılan bazı önemli performans yönetimi senaryoları araştırılabilir. Parçalı çok kiracılı veritabanları genelinde etkinliğin benzetimini yapmak için bir yük Oluşturucu kullanma, SQL veritabanı 'nın yerleşik izleme ve uyarı özellikleri gösterilmiştir.

Wingtip biletleri SaaS çok kiracılı veritabanı uygulaması, mekan çok kiracılı bir veri modeli kullanır. burada yer (kiracı) verileri, potansiyel olarak birden çok veritabanı arasında kiracı KIMLIĞIYLE dağıtılır. Birçok SaaS uygulaması gibi, beklenen kiracı iş yükü düzeni öngörülemez ve düzensizdir. Diğer bir deyişle, bilet satışı herhangi bir zamanda gerçekleşebilir. Bu tipik veritabanı kullanım deseninin avantajlarından yararlanmak için, veritabanlarının maliyetini iyileştirmek üzere veritabanları yukarı ve aşağı ölçeklendirilebilir. Bu tür bir düzende, yüklerin potansiyel olarak birden çok veritabanı arasında makul bir şekilde dengelendiği garantilemek için veritabanı kaynak kullanımını izlemeniz önemlidir. Ayrıca, ayrı veritabanlarının yeterli kaynaklara sahip olduğundan ve [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) sınırlarına ulaşmadığından emin olmanız gerekir. Bu öğretici veritabanlarını izlemenin ve yönetmenin yollarını ve iş yükündeki değişikliklere yanıt olarak düzeltici eylemi nasıl ele alınacağını anlatıyor.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> 
> * Sağlanan yük oluşturucuyu çalıştırarak, parçalı bir çok kiracılı veritabanında kullanım benzetimi yapma
> * Yükleme sırasında artışa yanıt verdiği için veritabanını izleme
> * Artan veritabanı yüküne yanıt olarak veritabanını ölçeklendirin
> * Tek kiracılı bir veritabanına kiracı sağlama

Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Wingtip bilet SaaS çok kiracılı veritabanı uygulaması dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. Wingtip biletleri SaaS çok kiracılı veritabanı uygulaması dağıtma ve araştırma](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>SaaS performans yönetimi desenlerine giriş

Veritabanı performans yönetimi, performans verilerini derleyip çözümlemeyi ve ardından uygulamanız için kabul edilebilir bir yanıt süresi sağlamak için parametreleri ayarlayarak bu verilere yanıt vermeyi içerir. 

### <a name="performance-management-strategies"></a>Uygulama performansı stratejileri

* Performansı el ile izlemek zorunda kalmamak için, **veritabanları normal aralıklar dışında bırakıldığında tetiklenecek uyarıları ayarlamak**en etkilidir.
* Bir veritabanının işlem boyutundaki kısa vadeli dalgalanmalara yanıt vermek için **DTU düzeyi yukarı veya aşağı ölçeklendirilebilir**. Bu dalgalanma düzenli veya öngörülebilir bir şekilde gerçekleşirse, **veritabanının ölçeklendirilmesi otomatik olarak gerçekleşecek şekilde zamanlanabilir**. Örneğin, iş yükünüzün hafif olduğunu bildiğiniz gece veya hafta sonları gibi zamanlarda ölçeği azaltabilirsiniz.
* Daha uzun süreli dalgalanmalara veya kiracılardaki değişikliklere yanıt vermek için, **tek tek kiracılar diğer veritabanına taşınabilir**.
* *Tek* tek kiracı yükünde kısa süreli artışlara yanıt vermek için, **tek tek kiracılar bir veritabanını alabilir ve bireysel bir işlem boyutu atanabilir**. Yük düşürültikten sonra kiracı, çok kiracılı veritabanına geri döndürülebilir. Bu, önceden bilindiğinde, veritabanı her zaman gereken kaynaklara sahip olduğundan ve çok kiracılı veritabanındaki diğer kiracılarda etkileri önlemek için kiracılar önceden hale taşınabilir. Popüler bir etkinlik için bilet satışı yoğunluğu yaşanan bir mekanda olduğu gibi bu gereksinim öngörülebildiği takdirde bu yönetim davranışı uygulamayla tümleştirilebilir.

[Azure portalı](https://portal.azure.com), çoğu kaynak üzerinde yerleşik izleme ve uyarı özelliği sağlar. SQL veritabanı için, veritabanlarında izleme ve uyarı verme vardır. Bu yerleşik izleme ve uyarı kaynağa özgüdür, bu nedenle az sayıda kaynak için kullanılması uygundur, ancak birçok kaynakla çalışırken kullanışlı değildir.

Birçok kaynakla çalıştığınız yüksek hacimli senaryolar için [Azure izleyici günlükleri](https://azure.microsoft.com/services/log-analytics/) kullanılabilir. Bu, bir Log Analytics çalışma alanında toplanan, yayılan tanılama günlükleri ve telemetri üzerinde analiz sağlayan ayrı bir Azure hizmetidir. Azure Izleyici günlükleri birçok hizmetten telemetri toplayabilir ve uyarıları sorgulamak ve ayarlamak için kullanılabilir.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Wingtip bilet SaaS çok kiracılı veritabanı uygulaması kaynak kodu ve betikleri alın

Wingtip biletleri SaaS çok kiracılı veritabanı betikleri ve uygulama kaynak kodu [Wingtipbilet ssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub deposunda mevcuttur. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

## <a name="provision-additional-tenants"></a>Ek kiracılar sağlama

Performans izleme ve yönetiminin ölçekli olarak nasıl çalıştığını anlamak için bu öğreticide, parçalı bir çok kiracılı veritabanında birden çok kiracının olması gerekir.

Önceki bir öğreticide bir grup kiracıyı zaten sağladıysanız, [Tüm kiracı veritabanlarında kullanım benzetimi yap](#simulate-usage-on-all-tenant-databases) bölümüne atlayın.

1. **PowerShell ISE**'de,...\\öğrenme modüllerini\\performans Izleme ve yönetim\\*demo-PerformanceMonitoringAndManagement. ps1*' yi açın. Bu öğretici sırasında birkaç senaryo çalıştıracağından bu betiği açık tutun.
1. **$DemoScenario** = **1**, _Kiracı grubu sağlama_ değerini ayarlayın
1. Betiği çalıştırmak için **F5**'e basın.

Betik, 17 kiracıları çok kiracılı veritabanına birkaç dakika içinde dağıtır. 

*New-TenantBatch* betiği, parçalı çok kiracılı veritabanı içinde benzersiz kiracı anahtarlarına sahip yeni kiracılar oluşturur ve bunları kiracı adı ve mekan türü ile başlatır. Bu, uygulamanın yeni bir kiracı sağlama yöntemiyle tutarlıdır. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Tüm kiracı veritabanlarındaki kullanımın benzetimini gerçekleştirme

*Demo-PerformanceMonitoringAndManagement. ps1* betiği, çok kiracılı veritabanına karşı çalışan bir iş yükünün benzetimini yapar. Yük, kullanılabilir yükleme senaryolarından biri kullanılarak oluşturulur:

| Tanıtım | Senaryo |
|:--|:--|
| 2 | Normal yoğunluk yükü oluştur (yaklaşık 30 DTU) |
| 3 | Kiracı başına daha uzun artışlarıyla ile yük oluştur|
| 4 | Kiracı başına daha yüksek DTU artışlarıyla ile yük oluştur (yaklaşık 70 DTU)|
| 5 | Tek bir kiracı üzerinde yüksek yoğunluk (yaklaşık 90 DTU) ve diğer tüm kiracılarda normal bir yoğunluk yükü oluşturun |

Yük oluşturucu her kiracı veritabanına *yapay* bir yalnızca CPU yükü uygular. Oluşturucu her kiracı veritabanı için yükü oluşturan saklı yordamı düzenli olarak çağıran bir iş başlatır. Yük düzeyleri (DTU 'Lar), süre ve aralıklar tüm veritabanları genelinde farklılık gösterir ve bu da öngörülemeyen kiracı etkinliğinin benzetimini yapılır.

1. **PowerShell ISE**'de,...\\öğrenme modüllerini\\performans Izleme ve yönetim\\*demo-PerformanceMonitoringAndManagement. ps1*' yi açın. Bu öğretici sırasında birkaç senaryo çalıştıracağından bu betiği açık tutun.
1. **$DemoScenario** = **2**ayarla, _Normal yoğunluk yükü oluştur_
1. Tüm kiracılarınıza bir yük uygulamak için **F5** tuşuna basın.

Wingtip bilet SaaS çok kiracılı veritabanı bir SaaS uygulamasıdır ve SaaS uygulamasındaki gerçek dünyada yük genellikle tek tek ve tahmin edilemez. Yük oluşturucu, bunun benzetimini gerçekleştirmek için kiracılar genelinde dağıtılan rastgele yük oluşturur. Yük deseninin ortaya geçmesi için birkaç dakika gerekir. bu nedenle, aşağıdaki bölümlerde yükü izlemeye çalışmadan önce 3-5 dakika için yük oluşturucuyu çalıştırın.

> [!IMPORTANT]
> Yük Oluşturucu yeni bir PowerShell penceresinde bir dizi iş olarak çalışıyor. Oturumu kapatırsanız, yük Oluşturucu duraklar. Yük Oluşturucu, Oluşturucu başlatıldıktan sonra sağlanan yeni kiracılar üzerinde yük oluşturduğu bir *iş çağırma* durumunda kalır. Yeni işleri çağırmayı durdurup betikten çıkmak için *CTRL-C* kullanın. Yük Oluşturucu çalıştırılmaya devam eder, ancak yalnızca var olan kiracılar üzerinde.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Azure portal kullanarak kaynak kullanımını izleme

Uygulanan yükün sonucu olan kaynak kullanımını izlemek için, portalı, kiracıların bulunduğu **tenants1**çok kiracılı veritabanına açın:

1. [Azure Portal](https://portal.azure.com) açın ve Server *tenants1-MT-&lt;User&gt;* ' a gidin.
1. Aşağı kaydırın ve veritabanlarını bulun ve **tenants1**' a tıklayın. Bu parçalı çok kiracılı veritabanı şimdiye kadar oluşturulan tüm kiracılar içerir.

![veritabanı grafiği](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

**DTU** grafiğini gözlemleyin.

## <a name="set-performance-alerts-on-the-database"></a>Veritabanında performans uyarılarını ayarlama

Veritabanı üzerinde \>%75 kullanımı tetikleyen bir uyarı ayarlayın:

1. [Azure Portal](https://portal.azure.com) *tenants1* veritabanını ( *TENANTS1-MT-&lt;User&gt;* Server) açın.
1. **Uyarı Kuralları**ve ardından **+ Uyarı ekle**’ye tıklayın:

   ![uyarı ekle](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. **Yüksek DTU** gibi bir ad belirtin,
1. Aşağıdaki değerleri ayarlayın:
   * **Ölçüm = DTU yüzdesi**
   * **Koşul = büyüktür**
   * **Eşik = 75**.
   * **Period = son 30 dakika Içinde**
1. *Ek yönetici e-postaları* kutusuna bir e-posta adresi ekleyin ve **Tamam**' a tıklayın.

   ![uyarı ayarlama](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Yoğun bir veritabanını büyütme

Yük düzeyi bir veritabanında veritabanını izleyen ve %100 DTU kullanımına ulaşan bir noktaya yükseliyorsa, veritabanı performansı etkilenir ve sorgu yanıt sürelerini yavaşlatabilirler.

**Kısa dönem**, ek kaynaklar sağlamak ya da kiracıların çok kiracılı veritabanından (bunları çok kiracılı veritabanını tek başına bir veritabanına taşımak) kaldırmak için veritabanını ölçeklendirmeyi düşünün.

**Daha uzun süreli**, veritabanı performansını iyileştirmek için sorguları veya dizin kullanımını iyileştirmeyi düşünün. Uygulamanın performans sorunlarını gidermek için en iyi yöntem, %100 DTU kullanımına ulaşmadan önce bir veritabanını ölçeklendirmektir. Sizi önceden uyarması için bir uyarı ayarlayın.

Oluşturucu tarafından üretilen yükü artırarak, meşgul bir veritabanının benzetimini yapabilirsiniz. Kiracıların daha sık yük vermesine ve daha uzun süre boyunca, tek kiracılar için gereksinimleri değiştirmeden çok kiracılı veritabanı yükünü artırmasına neden olur. Veritabanının ölçeği, portalda veya PowerShell 'den kolayca yapılır. Bu alıştırmada portal kullanılmaktadır.

1. Her bir kiracı için gereken en yüksek yükü değiştirmeden veritabanındaki Toplam yükün yoğunluğunu artırmak için, veritabanı _başına daha uzun ve daha sık yinelenen yük oluşturma_ *$DemoScenario* = **3**' ü ayarlayın.
1. Bir yükü tüm kiracı veritabanlarınıza uygulamak için **F5** tuşuna basın.
1. Azure portal **tenants1** veritabanına gidin.

Üst grafikteki artan veritabanı DTU kullanımını izleyin. Yeni daha yüksek yükün başlatılması birkaç dakika sürer, ancak veritabanının en fazla kullanım için hızlı bir şekilde başlaması ve yükün yeni bir düzene göre yüklenmesi sayesinde, veritabanını hızlı bir şekilde aşırı yükler.

1. Veritabanını ölçeklendirmek için, ayarlar dikey penceresinde **Fiyatlandırma Katmanı (DTU 'lar)** seçeneğine tıklayın.
1. **DTU** ayarını **100**olarak ayarlayın. 
1. Veritabanını ölçeklendirmeye yönelik isteği göndermek için **Uygula** ' ya tıklayın.

İzleme grafiklerini görüntülemek için **tenants1** > **genel bakışa** geri dönün. Veritabanını daha fazla kaynakla sağlama etkisini izleyin (birkaç kiracı ve rastgele bir yük olsa da, bir süredir çalıştırana kadar yaratacağı görmeniz her zaman kolay değildir). Grafikler göz önünde bulundurularak, üstteki grafik üzerinde %100 ' un Şu anda 100 DTU 'yu temsil ettiğini unutmayın. Bu durumda, daha düşük grafik %100 yine de 50 DTU.

İşlem boyunca veritabanları çevrimiçi ve tam olarak kullanılabilir durumdadır. Bırakılan bağlantıları yeniden denemek için uygulama kodu her zaman yazılmalıdır ve bu nedenle veritabanına yeniden bağlanır.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Kendi veritabanında yeni bir kiracı sağlama 

Parçalı çok kiracılı model, diğer kiracıların yanı sıra çok kiracılı bir veritabanında yeni bir kiracı sağlamayı veya kiracının kendi veritabanına sağlanması gerektiğini seçmenize olanak sağlar. Bir kiracıyı kendi veritabanında sunarak, ayrı veritabanında bulunan yalıtımın avantajlarından yararlanır ve bu kiracının performansını diğerlerinden bağımsız olarak yönetmenizi sağlar, bu kiracıyı diğerlerinden bağımsız olarak geri yükleyebilirsiniz, vb. Örneğin, ücretsiz deneme veya normal müşterileri çok kiracılı bir veritabanına ve bireysel veritabanlarındaki Premium müşterilere koyabilirsiniz.  Yalıtılmış tek kiracılı veritabanları oluşturulduysa, kaynak maliyetlerini iyileştirmek için bir elastik havuzda toplu olarak yönetilebilecek.

Kendi veritabanında zaten yeni bir kiracı sağladıysanız, sonraki birkaç adımı atlayın.

1. **PowerShell ISE**'de,...\\öğrenme modüllerini\\provisionkiracılar\\*demo-ProvisionTenants. ps1*' yi açın. 
1. Değiştirin **$TenantName = "SALX salsa"** ve **$VenueType = "dans"**
1. **$Scenario** = **2**ayarlama, _Yeni bir tek kiracılı veritabanında kiracı sağlama_
1. Betiği çalıştırmak için **F5**'e basın.

Betik bu kiracıyı ayrı bir veritabanında hazırlar, veritabanını ve kiracıyı katalogla kaydeder ve ardından kiracının etkinlikler sayfasını tarayıcıda açar. Olay Hub 'ı sayfasını yenileyin ve "SALX salsa" nın bir mekan eklendiğini görürsünüz.

## <a name="manage-performance-of-an-individual-database"></a>Tek bir veritabanının performansını yönetme

Çok kiracılı bir veritabanı içindeki tek bir kiracı sürekli yüksek bir yük yaşıyorsa, veritabanı kaynaklarını ayırt ediyor ve aynı veritabanındaki diğer kiracıların etkisi olabilir. Etkinliğin bir süre devam edebilmesi olasıdır, kiracı geçici olarak veritabanından ve kendi tek kiracılı veritabanına taşınabilir. Bu, kiracının ihtiyacı olan ek kaynaklara sahip olmasını sağlar ve diğer kiracılardan tam olarak yalıtır.

Bu alıştırma, popüler bir olay için bilet satışa geldiğinde yüksek bir yük yaşayan SALX salsa 'nın etkisinin benzetimini yapar.

1. ...\\*demo-PerformanceMonitoringAndManagement. ps1* betiğini açın.
1. **$DemoScenario = 5**olarak ayarlayın, _tek bir kiracı üzerinde normal yük ve yüksek yük oluşturun (yaklaşık 90 DTU)._
1. **$SingleTenantName = SALX salsa** ayarla
1. **F5**’i kullanarak betiği yürütün.

Portal ' a gidin ve izleme grafiklerini görüntülemek için **salixsalsa** > **Genel Bakış ' a** gidin. 

## <a name="other-performance-management-patterns"></a>Diğer performans yönetimi desenleri

**Kiracı Self Servis ölçeklendirme**

Ölçeklendirme, yönetim API 'SI aracılığıyla kolayca çağrılan bir görev olduğundan, kiracı veritabanlarını kiracıya yönelik uygulamanıza ölçeklendirebilme özelliğini kolayca oluşturabilir ve bunu SaaS hizmetinizin bir özelliği olarak sunabilirsiniz. Örneğin, kiracıların ölçek artırma ve azaltma işlemini kendi kendine yönetmesine, belki de doğrudan faturalarına bağlamasına izin verebilirsiniz!

**Kullanım düzenlerini eşleştirmek için bir zamanlamaya göre bir veritabanını yukarı ve aşağı ölçeklendirin**

Toplam kiracı kullanımının öngörülebilir kullanım desenlerinin izlediği yerde, bir zamanlamaya göre bir veritabanını yukarı ve aşağı ölçeklendirmek için Azure Otomasyonu 'nu kullanabilirsiniz. Örneğin, kaynak gereksinimlerinde bir bırakma olduğunu bildiğiniz zaman, hafta içi bir veritabanını 18:00 'den sonra ölçeklendirin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sağlanan yük oluşturucuyu çalıştırarak, parçalı bir çok kiracılı veritabanında kullanım benzetimi yapma
> * Yükleme sırasında artışa yanıt verdiği için veritabanını izleme
> * Artan veritabanı yüküne yanıt olarak veritabanını ölçeklendirin
> * Tek kiracılı bir veritabanına kiracı sağlama

## <a name="additional-resources"></a>Ek kaynaklar

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure otomasyonu](../automation/automation-intro.md)