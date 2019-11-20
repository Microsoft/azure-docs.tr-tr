---
title: 'SaaS uygulaması: birçok veritabanının performansını Izleme'
description: Çok kiracılı bir SaaS uygulamasında Azure SQL veritabanlarının ve havuzlarının performansını izleme ve yönetme
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
ms.openlocfilehash: e2e752ec37f71ea501dcee586e7daf0fc950919d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822228"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Çok kiracılı bir SaaS uygulamasında Azure SQL veritabanlarının ve havuzlarının performansını izleme ve yönetme

Bu öğreticide, SaaS uygulamalarında kullanılan bazı önemli performans yönetimi senaryoları araştırılabilir. Tüm kiracı veritabanlarında etkinliğin benzetimini yapmak için bir yük Oluşturucu kullanarak, SQL veritabanı ve elastik havuzların yerleşik izleme ve uyarı özellikleri gösterilmiştir.

Kiracı başına Wingtip bilet SaaS veritabanı uygulaması, her bir mekanın (kiracının) kendi veritabanına sahip olduğu tek kiracılı bir veri modeli kullanır. Birçok SaaS uygulaması gibi, beklenen kiracı iş yükü düzeni öngörülemez ve düzensizdir. Diğer bir deyişle, bilet satışı herhangi bir zamanda gerçekleşebilir. Bu tipik veritabanı kullanım düzeninden faydalanmak için, kiracı veritabanları elastik havuzlara dağıtılır. Elastik havuzlar, kaynakları çok sayıda veritabanı arasında paylaştırarak çözüm maliyetini en iyi duruma getirir. Bu düzen türü ile yüklerin havuzlar arasında makul bir şekilde dengelendiğinden emin olmak için veritabanı ve havuz kaynak kullanımının izlenmesi önemlidir. Ayrıca, her bir veritabanının yeterli kaynağa sahip olduğundan ve havuzların [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model) sınırına ulaşmadığından emin olmanız gerekir. Bu öğreticide, veritabanı ve havuzları izleyip yönetme yollarını ve iş yükündeki değişikliklere yanıt olarak nasıl düzeltici işlem yapılacağını incelenmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> 
> * Sağlanan bir yük oluşturucuyu çalıştırarak kiracı veritabanlarındaki kullanımın benzetimini gerçekleştirme
> * Yükteki artışa yanıt veren kiracı veritabanlarını izleme
> * Artan veritabanı yüküne yanıt olarak Elastik havuzun ölçeğini artırma
> * Veritabanı etkinliğinin yükünü dengelemek üzere ikinci bir Elastik havuz sağlama


Bu öğreticiyi tamamlamak için aşağıdaki ön koşulların karşılandığından emin olun:

* Kiracı uygulaması başına Wingtip bilet SaaS veritabanı dağıtılır. Beş dakikadan kısa bir süre içinde dağıtmak için bkz [. kiracı uygulaması başına Wingtip bilet SaaS veritabanını dağıtma ve araştırma](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell’in yüklendiğinden. Ayrıntılar için bkz. [Azure PowerShell’i kullanmaya başlama](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>SaaS performans yönetimi desenlerine giriş

Veritabanı performans yönetimi, performans verilerini derleyip çözümlemeyi ve ardından uygulamanız için kabul edilebilir bir yanıt süresi sağlamak için parametreleri ayarlayarak bu verilere yanıt vermeyi içerir. Birden çok kiracıyı barındırırken, elastik havuzlar, öngörülemeyen iş yükleriyle bir grup veritabanı için kaynakları sağlamak ve yönetmek için uygun maliyetli bir yoldur. Bazı iş yükü düzenlerinde iki S3 veritabanı bile tek bir havuzda yönetilebilir.

![Uygulama diyagramı](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Havuzlar ve havuzlardaki veritabanları, kabul edilebilir performans aralıklarında kalmasını sağlamak için izlenmelidir. Havuz kullanımını tüm veritabanlarının toplam iş yükünün ihtiyaçlarını karşılayacak şekilde ayarlayın, bu da havuz eDTU 'larının genel iş yükü için uygun olmasını sağlar. Veritabanı başına en düşük ve veritabanı başına en yüksek eDTU değerlerini, uygulamanıza özel gereksinimler için uygun değerlere ayarlayın.

### <a name="performance-management-strategies"></a>Uygulama performansı stratejileri

* Performansı el ile izlemek zorunda kalmamak için, **veritabanları veya havuzların normal aralıklar dışına çıkar durumunda tetiklenecek uyarıları ayarlamak**en etkilidir.
* Havuzun toplam işlem boyutundaki kısa süreli dalgalanmalara yanıt vermek için, **Havuz eDTU düzeyi yukarı veya aşağı ölçeklendirilebilir**. Bu dalgalanma düzenli veya öngörülebilir aralıklarla gerçekleşiyorsa, **havuz ölçeklendirmesi otomatik olarak gerçekleşecek şekilde zamanlanabilir**. Örneğin, iş yükünüzün hafif olduğunu bildiğiniz gece veya hafta sonları gibi zamanlarda ölçeği azaltabilirsiniz.
* Daha uzun vadeli dalgalanmalara ya da veritabanı sayısındaki değişikliklere yanıt vermek için, **tek veritabanları diğer havuzlara taşınabilir**.
* *Tek* bir veritabanı yüklemesinin kısa süreli artışlarına yanıt vermek için **tek tek veritabanları bir havuzdan alınmış olabilir ve bireysel bir işlem boyutu atanabilir**. Yükü azaldıktan sonra veritabanını havuza döndürebilirsiniz. Bu, önceden bilindiğinde, veritabanı her zaman gereken kaynaklara sahip olduğundan ve havuzdaki diğer veritabanları üzerindeki etkileri önlemek için veritabanları önceden hale taşınabilir. Popüler bir etkinlik için bilet satışı yoğunluğu yaşanan bir mekanda olduğu gibi bu gereksinim öngörülebildiği takdirde bu yönetim davranışı uygulamayla tümleştirilebilir.

[Azure portalı](https://portal.azure.com), çoğu kaynak üzerinde yerleşik izleme ve uyarı özelliği sağlar. SQL Veritabanı için veritabanı ve havuzlarda izleme ve uyarı özellikleri kullanılabilir. Bu yerleşik izleme ve uyarı kaynağa özgüdür, bu nedenle az sayıda kaynak için kullanılması uygundur, ancak birçok kaynakla çalışırken çok kullanışlı değildir.

Birçok kaynakla çalıştığınız yüksek hacimli senaryolar için [Azure izleyici günlükleri](saas-dbpertenant-log-analytics.md) kullanılabilir. Bu, bir Log Analytics çalışma alanında toplanan, yayılan tanılama günlükleri ve telemetri üzerinde analiz sağlayan ayrı bir Azure hizmetidir. Azure Izleyici günlükleri birçok hizmetten telemetri toplayabilir ve uyarıları sorgulamak ve ayarlamak için kullanılabilir.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Kiracı uygulama betikleri başına Wingtip bilet SaaS veritabanını alın

Wingtip biletleri SaaS çok kiracılı veritabanı betikleri ve uygulama kaynak kodu [Wingtipbilet ssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub deposunda mevcuttur. Wingtip bilet SaaS betiklerini indirme ve engellemesini kaldırma adımları için [genel kılavuza](saas-tenancy-wingtip-app-guidance-tips.md) göz atın.

## <a name="provision-additional-tenants"></a>Ek kiracılar sağlama

Havuzlar yalnızca iki adet S3 veritabanı ile uygun maliyetli olabilse de, havuzdaki veritabanı sayısı arttıkça ortalama etki daha uygun maliyetli olur. Performans izleme ve yönetiminin ölçeklenerek nasıl çalıştığını anlamak için bu öğreticide en az 20 veritabanınızın dağıtılmış olması gerekir.

Önceki bir öğreticide bir grup kiracıyı zaten sağladıysanız, [Tüm kiracı veritabanlarında kullanım benzetimi yap](#simulate-usage-on-all-tenant-databases) bölümüne atlayın.

1. **PowerShell ISE**'de,...\\öğrenme modüllerini\\performans Izleme ve yönetim\\*demo-PerformanceMonitoringAndManagement. ps1*' yi açın. Bu öğretici sırasında birkaç senaryo çalıştıracağından bu betiği açık tutun.
1. **$DemoScenario** = **1**, **Kiracı grubu sağlama** değerini ayarlayın
1. Betiği çalıştırmak için **F5**'e basın.

Bu betik, beş dakikadan daha kısa bir süre içinde 17 kiracı dağıtır.

*New-tenantbatch* betiği, varsayılan olarak, yeni kiracı veritabanlarını oluşturmak üzere Katalog sunucusundaki **basetenantdb** veritabanını kopyalayan, iç içe geçmiş veya bağlı bir [Kaynak Yöneticisi](../azure-resource-manager/index.yml) şablonları kümesi kullanır. Bunları kataloğa kaydeder ve son olarak bunları kiracı adı ve mekan türü ile başlatır. Bu, uygulamanın yeni bir kiracı sağlama yöntemiyle tutarlıdır. *Basetenantdb* üzerinde yapılan tüm değişiklikler, bundan sonra sağlanan tüm yeni kiracılar için uygulanır. *Mevcut* kiracı veritabanlarında ( *basetenantdb* veritabanı dahil) şema değişiklikleri yapmayı öğrenmek için [şema yönetimi öğreticisine](saas-tenancy-schema-management.md) bakın.

## <a name="simulate-usage-on-all-tenant-databases"></a>Tüm kiracı veritabanlarındaki kullanımın benzetimini gerçekleştirme

*Demo-PerformanceMonitoringAndManagement. ps1* betiği, tüm kiracı veritabanlarına karşı çalışan bir iş yükünün benzetimini yapar. Yük, kullanılabilir yükleme senaryolarından biri kullanılarak oluşturulur:

| Tanıtım | Senaryo |
|:--|:--|
| 2 | Normal yoğunluk yükü oluştur (yaklaşık 40 DTU) |
| 3 | Veritabanı başına daha uzun ve daha sık artışla yük oluşturma|
| 4 | Veritabanı başına daha yüksek DTU artışlarıyla ile yük oluştur (yaklaşık 80 DTU)|
| 5 | Tek bir kiracı üzerinde normal yük ve yüksek yük oluşturma (yaklaşık 95 DTU)|
| 6 | Birden çok havuzda dengesiz yük oluşturma|

Yük oluşturucu her kiracı veritabanına *yapay* bir yalnızca CPU yükü uygular. Oluşturucu her kiracı veritabanı için yükü oluşturan saklı yordamı düzenli olarak çağıran bir iş başlatır. Yük düzeyleri (eDTU cinsinden), süresi ve aralıkları tüm veritabanlarında farklıdır ve öngörülemez kiracı etkinliğini benzetimi gerçekleştirir.

1. **PowerShell ISE**'de,...\\öğrenme modüllerini\\performans Izleme ve yönetim\\*demo-PerformanceMonitoringAndManagement. ps1*' yi açın. Bu öğretici sırasında birkaç senaryo çalıştıracağından bu betiği açık tutun.
1. **$DemoScenario** = **2**' i ayarlayın, *normal yoğunlukta yük oluşturun*.
1. Bir yükü tüm kiracı veritabanlarınıza uygulamak için **F5** tuşuna basın.

Her kiracı için Wingtip bilet SaaS veritabanı bir SaaS uygulamasıdır ve SaaS uygulamasındaki gerçek dünyada yükün genellikle tek tek ve öngörülemeyen bir uygulamadır. Yük oluşturucu, bunun benzetimini gerçekleştirmek için kiracılar genelinde dağıtılan rastgele yük oluşturur. Yük deseninin ortaya geçmesi için birkaç dakika gerekir. bu nedenle, aşağıdaki bölümlerde yükü izlemeye çalışmadan önce 3-5 dakika için yük oluşturucuyu çalıştırın.

> [!IMPORTANT]
> Yük oluşturucu, yerel PowerShell oturumunuzda bir dizi iş çalıştırıyor. *Demo-PerformanceMonitoringAndManagement.ps1* sekmesini açık tutun! Sekmeyi kapatırsanız veya makineyi askıya alırsanız, yük oluşturucu durur. Yük Oluşturucu, Oluşturucu başlatıldıktan sonra sağlanan yeni kiracılar üzerinde yük oluşturduğu bir *iş çağırma* durumunda kalır. Yeni işleri çağırmayı durdurup betikten çıkmak için *CTRL-C* kullanın. Yük Oluşturucu çalıştırılmaya devam eder, ancak yalnızca var olan kiracılar üzerinde.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Azure portal kullanarak kaynak kullanımını izleme

Uygulanan yükün sonucu olan kaynak kullanımını izlemek için, kiracı veritabanlarını içeren havuzda portalı açın:

1. [Azure Portal](https://portal.azure.com) açın ve *tenants1-dpt-&lt;User&gt;* sunucusuna gidin.
1. Aşağı kaydırıp elastik havuzları bulun ve **Pool1**’e tıklayın. Bu havuz o ana kadar oluşturulmuş tüm kiracı veritabanlarını içerir.

**Elastik havuz izleme** ve **elastik veritabanı izleme** grafiklerini gözlemleyin.

Havuzun kaynak kullanımı, havuzdaki tüm veritabanlarının toplam veritabanı kullanımdır. Veritabanı grafiği beş en yoğun veritabanını gösterir:

![veritabanı grafiği](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Havuzda ilk beşinin ötesinde ek veritabanları olduğundan, havuz kullanımı, ilk beş veritabanı grafiğinde yansıtılmayan etkinlikleri gösterir. Daha fazla bilgi için **veritabanı kaynak kullanımı**' na tıklayın:

![veritabanı kaynak kullanımı](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Havuzda performans uyarılarını ayarlama

\>%75 kullanımı üzerinde tetiklenen havuzda şu şekilde bir uyarı ayarlayın:

1. [Azure Portal](https://portal.azure.com) *Pool1* ( *tenants1-dpt-\<User\>* Server) açın.
1. **Uyarı Kuralları**ve ardından **+ Uyarı ekle**’ye tıklayın:

   ![uyarı ekle](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. **Yüksek DTU** gibi bir ad belirtin,
1. Aşağıdaki değerleri ayarlayın:
   * **Ölçüm = eDTU yüzdesi**
   * **Koşul = büyüktür**
   * **Eşik = 75**
   * **Period = son 30 dakika Içinde**
1. *Ek yönetici e-postaları* kutusuna bir e-posta adresi ekleyin ve **Tamam**' a tıklayın.

   ![uyarı ayarlama](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Meşgul bir havuzun ölçeğini artırma

Bir havuzdaki toplam yük düzeyi havuzu kapasitesini aşacak bir noktaya yükselir ve %100 eDTU kullanımına ulaşırsa, bağımsız veritabanı performansı etkilenir ve havuzdaki tüm veritabanları için sorgu yanıt süreleri yavaşlayabilir.

**Kısa dönem**, ek kaynaklar sağlamak veya havuzdan veritabanlarını kaldırmak (bunları diğer havuzlara taşımak ya da havuzdan bağımsız bir hizmet katmanına taşımak) için havuzu ölçeklendirmeyi düşünün.

**Daha uzun süreli**, veritabanı performansını iyileştirmek için sorguları veya dizin kullanımını iyileştirmeyi düşünün. Uygulamanın performans sorunlarına karşı duyarlılığına bağlı olarak, bir havuzun ölçeğini havuz %100 eDTU kullanımına ulaşmadan artırmak idealdir. Sizi önceden uyarması için bir uyarı ayarlayın.

Oluşturucu tarafından üretilen yükü artırarak meşgul bir havuzun benzetimini gerçekleştirebilirsiniz. Veritabanlarının, bireysel veritabanlarının gereksinimlerini değiştirmeden havuzdaki toplam yükü artırarak, daha sık veri patlaması ve daha uzun süre boyunca. Havuz ölçeğini, portaldan veya PowerShell’den kolayca artırabilirsiniz. Bu alıştırmada portal kullanılmaktadır.

1. Her bir veritabanı için gereken en büyük yükü değiştirmeden toplam yükün yoğunluğunu artırmak üzere *$DemoScenario* = **3**, _Veritabanı başına daha uzun ve daha sık artışla yük oluşturma_ değerini ayarlayın.
1. Bir yükü tüm kiracı veritabanlarınıza uygulamak için **F5** tuşuna basın.

1. Azure portal **Pool1** adresine gidin.

Üst grafikteki artan havuz eDTU kullanımını izleyin. Yeni daha yüksek yükün başlatılması birkaç dakika sürer, ancak havuzun en fazla kullanım için hızlı bir şekilde başlaması gerektiğini ve yük duyalarını yeni düzene göre daha hızlı bir şekilde yeniden yüklemelerini görmeniz gerekir.

1. Havuzun ölçeğini ölçeklendirmek için **Pool1** sayfasının en üstünde **havuzu Yapılandır** ' a tıklayın.
1. **Havuz eDTU** ayarını **100**olarak ayarlayın. Havuz eDTU değerinin değiştirilmesi, veritabanı başına ayarları değiştirmez (veritabanı başına hala en fazla 50 eDTU’dur). **Havuzu Yapılandır** sayfasının sağ tarafında veritabanı başına ayarları görebilirsiniz.
1. Havuzu ölçeklendirmeye yönelik isteği göndermek için **Kaydet** ' e tıklayın.

İzleme grafiklerini görüntülemek için **Pool1** > **genel bakışa** geri dönün. Havuzu daha fazla kaynakla sağlama etkisini izleyin (birkaç veritabanı ve rastgele bir yük olsa da, bir süredir çalıştırana kadar yaratacağı görmeniz her zaman kolay değildir). Grafiklere bakarken, üst grafikteki %100 değerinin 100 eDTU’yu, alt grafikteki %100 değerinin ise veritabanı başına en yüksek değer hala 50 eDTU olduğundan 50 eDTU’yu temsil ettiğini aklınızda bulundurun.

İşlem boyunca veritabanları çevrimiçi ve tam olarak kullanılabilir durumdadır. Her veritabanının yeni havuz eDTU değeriyle etkinleştirilmeye hazır olduğu son anda tüm etkin bağlantılar kesilir. Kesilen bağlantıları yeniden denemek için her zaman uygulama kodunun yazılması gerekir, böylece ölçeği artırılmış havuzda veritabanına yeniden bağlanılır.

## <a name="load-balance-between-pools"></a>Havuzlar arasında yük dengeleme

Havuz ölçeğini artırmanın alternatif bir yolu, ikinci bir havuz oluşturup veritabanlarını bu havuza taşımak ve iki havuz arasındaki yükü dengelemektir. Bunu yapmak için yeni havuzun birinci havuzla aynı sunucuda oluşturulması gerekir.

1. [Azure Portal](https://portal.azure.com), **tenants1-dpt-&lt;Kullanıcı&gt;** sunucusu ' nu açın.
1. Geçerli sunucuda bir havuz oluşturmak için **+ yeni havuz** ' a tıklayın.
1. **Elastik havuz** şablonunda:

   1. **Adı** *pool2*olarak ayarlayın.
   1. Fiyatlandırma katmanını **Standart Havuz** olarak bırakın.
   1. **Havuzu yapılandır**'a tıklayın,
   1. **Havuz edtu** *50 eDTU*olarak ayarlayın.
   1. Sunucuda *pool2*'e eklenebilecek veritabanlarının listesini görmek Için **veritabanı Ekle** ' ye tıklayın.
   1. Bunları yeni havuza taşımak için 10 veritabanı seçin ve ardından **Seç**' e tıklayın. Yük oluşturucuyu çalıştırıyorsanız, hizmet performans profilinizin varsayılan 50 eDTU boyutundan daha büyük bir havuz gerektirdiğini ve 100 eDTU ayarı ile başlamasını önermenizi zaten biliyor.

      ![Önerilen](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Bu öğreticide, varsayılan değer olarak 50 eDTU 'yu bırakın ve yeniden **Seç** ' e tıklayın.
   1. Yeni havuzu oluşturmak ve seçili veritabanlarını bu havuza taşımak için **Tamam ' ı** seçin.

Havuzun oluşturulması ve veritabanlarının taşınması birkaç dakika sürer. Veritabanları taşındığında, çevrimiçi kalır ve en son bir kez açık olan tüm bağlantılar kapalı olana kadar tamamen erişilebilir olur. Bir yeniden deneme mantığınızın olduğu sürece, istemciler yeni havuzdaki veritabanına bağlanır.

Havuzu açmak ve performansını izlemek için **pool2** ( *tenants1-dpt-\<User\>* Server) konumuna gidin. Bunu görmüyorsanız, yeni havuzun sağlanması için bekleyin.

Artık *Pool1* üzerinde kaynak kullanımının bırakıldığına ve *pool2* 'in artık benzer şekilde yüklendiğini görürsünüz.

## <a name="manage-performance-of-an-individual-database"></a>Tek bir veritabanının performansını yönetme

Havuzdaki tek bir veritabanı, havuz yapılandırmasına bağlı olarak sürekli yüksek bir yük ile karşılaşırsa, havuzdaki kaynakları ayırt edebilir ve diğer veritabanlarını etkiler. Etkinliğin bir süre devam edebilmesi olasıdır, veritabanı geçici olarak havuzdan taşınabilir. Bu, veritabanının ihtiyacı olan ek kaynaklara sahip olmasını sağlar ve diğer veritabanlarından yalıtır.

Bu alıştırmada, popüler bir konser için biletler satışa çıktığında yüksek bir yükle karşılaşan Contoso Konser Salonu etkisinin benzetimi gerçekleştirilmektedir.

1. **PowerShell ISE**'de...\\*demo-PerformanceMonitoringAndManagement. ps1* betiğini açın.
1. **$DemoScenario = 5 olarak ayarlayın, tek bir kiracı üzerinde normal yük ve yüksek yük oluşturun (yaklaşık 95 DTU).**
1. **$SingleTenantDatabaseName = contosoconcerthall** değerini ayarlayın
1. **F5**’i kullanarak betiği yürütün.


1. [Azure Portal](https://portal.azure.com), *tenants1-dpt-\<User\>* sunucusundaki veritabanları listesine gidin. 
1. **Contosoconcerthall** veritabanına tıklayın.
1. **Contosoconcerthall** bulunduğu havuza tıklayın. **Elastik havuz** bölümünde havuzu bulun.

1. **Elastik havuz izleme** grafiğini inceleyin ve daha fazla havuz eDTU kullanımını arayın. Bir veya iki dakika sonra, daha yüksek olan yük etkisini göstermeye başlar ve havuzun %100 kullanıma ulaştığını görürsünüz.
2. Son saatteki en yoğun veritabanlarını gösteren **elastik veritabanı izleme** ekranını inceleyin. *Contosoconcerthall* veritabanı kısa süre önce beş en yoğun veritabanından biri olarak görünmelidir.
3. **Elastik veritabanı izleme** **grafiğine** tıklayın ve veritabanlarını Izleyebileceğiniz **veritabanı kaynak kullanımı** sayfasını açar. Bu, *contosoconcerthall* veritabanının görüntüsünü yalıtmanızı sağlar.
4. Veritabanları listesinden **contosoconcerthall**' ye tıklayın.
5. Veritabanı için tek başına bir işlem boyutu ayarlayabileceğiniz **performansı Yapılandır** sayfasını açmak Için **Fiyatlandırma Katmanı (DTU 'lar)** ' ne tıklayın.
6. Standart katmanındaki ölçeklendirme seçeneklerini açmak için **Standart** sekmesine tıklayın.
7. **DTU kaydırıcısını sağa kaydırarak** **100** DTU ' ı seçin. Bu, **S3**hizmet hedefine karşılık gelir.
8. Veritabanını havuzun dışına taşımak ve *Standart S3* veritabanı yapmak için **Uygula** ' ya tıklayın.
9. Ölçeklendirmeyi tamamladıktan sonra, contosoconcerthall veritabanı ve Pool1 ' deki etkiyi esnek havuzda ve veritabanı dikey pencerelerinde izleyin.

Contosoconcerthall veritabanı alt tarafları üzerinde yüksek yük olduktan sonra, maliyetini azaltmak için onu hemen havuza döndürün. Söz konusu olduğunda, bu durum açık değilse, veritabanında DTU kullanımı, havuzdaki veritabanı başına Max 'ın altına düştüğünde tetiklenecek bir uyarı ayarlayabilirsiniz. Veritabanını havuza taşıma işlemi 5. alıştırmada açıklanmıştır.

## <a name="other-performance-management-patterns"></a>Diğer performans yönetimi desenleri

**Önücü ölçekleme** Yukarıdaki alıştırmada, yalıtılmış bir veritabanının nasıl ölçeklendirilebileceği hakkında daha fazla bir veritabanı olduğunu bilirsiniz. Contoso Concert salonu yönetimi, yaklaşan bilet satışı hakkında bilgi sahibi olduysa, veritabanı havuzdan daha önceden bir şekilde taşınmış olabilir. Aksi takdirde, neler olduğunu belirlemek için havuz veya veritabanı üzerinde bir uyarı verilmesini isteyebilirdi. Bu durumu, havuzda performans düşüklüğünden şikayetçi olan diğer kiracılardan öğrenmek istemezsiniz. Kiracı ek kaynaklara ne süreyle ihtiyaç duyduğunu öngörebiliyorsa, tanımlanmış bir zamanlamaya göre veritabanını havuz dışına ve sonra tekrar içine taşımak için bir Azure Otomasyonu runbook'u ayarlayabilirsiniz.

**Kiracı self-servis ölçeklendirme** Ölçeklendirme, yönetim API’si aracılığıyla kolayca çağrılan bir görev olduğundan, kiracıya yönelik uygulamanızda kiracı veritabanlarını ölçeklendirme özelliğini kolayca oluşturabilir ve SaaS hizmetinizin bir özelliği olarak sunabilirsiniz. Örneğin, kiracıların ölçek artırma ve azaltma işlemini kendi kendine yönetmesine, belki de doğrudan faturalarına bağlamasına izin verebilirsiniz!

**Kullanım düzenlerini eşleştirmek için bir zamanlamaya göre bir havuzu yukarı ve aşağı ölçeklendirin**

Toplu kiracı kullanımının öngörülebilir kullanım düzenlerini takip ettiği durumlarda, belirli bir zamanlamaya göre bir havuzun ölçeğini artırıp azaltmak için Azure Otomasyonu’nu kullanabilirsiniz. Örneğin, kaynak gereksinimlerinde düşüş olduğunu bildiğiniz hafta içi günlerinde havuz ölçeğini akşam 6’dan sonra azaltıp sabah 6’dan önce yeniden artırabilirsiniz.



## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sağlanan bir yük oluşturucuyu çalıştırarak kiracı veritabanlarındaki kullanımın benzetimini gerçekleştirme
> * Yükteki artışa yanıt veren kiracı veritabanlarını izleme
> * Artan veritabanı yüküne yanıt olarak Elastik havuzun ölçeğini artırma
> * Veritabanı etkinliğinin yük dengelemesini yapmak üzere ikinci bir Elastik havuz sağlama

[Tek bir kiracıyı geri yükleme öğreticisi](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Ek kaynaklar

* [Kiracı uygulaması dağıtımı başına Wingtip bilet SaaS veritabanı üzerine inşa eden ek öğreticiler](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [SQL Elastik havuzları](sql-database-elastic-pool.md)
* [Azure otomasyonu](../automation/automation-intro.md)
* [Azure izleyici günlükleri](saas-dbpertenant-log-analytics.md) -Azure izleyici günlüklerini ayarlama ve kullanma öğreticisi
