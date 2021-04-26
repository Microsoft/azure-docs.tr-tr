---
title: Azure Service Fabric Izleme ve tanılama 'Ya genel bakış
description: Azure Service Fabric kümeleri, uygulamaları ve hizmetleri için izleme ve tanılama hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 71ec86f26de1e94b4e17e0990d2eafd1fff954e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627753"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric için izleme ve tanılama

Bu makalede, Azure Service Fabric izleme ve tanılama hakkında genel bir bakış sunulmaktadır. İzleme ve tanılama, iş yüklerini herhangi bir bulut ortamında geliştirmek, test etmek ve dağıtmak için önemlidir. Örneğin, uygulamalarınızın nasıl kullanıldığını, Service Fabric platformu tarafından gerçekleştirilen eylemleri, performans sayaçlarıyla kaynak kullanımınızı ve kümenizin genel durumunu izleyebilirsiniz. Sorunları tanılamak ve düzeltmek ve gelecekte oluşmasını önlemek için bu bilgileri kullanabilirsiniz. Sonraki birkaç bölümde, üretim iş yükleri için göz önünde bulundurulması gereken Service Fabric izlemenin her bir alanı kısaca açıklanacaktır. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Uygulama izleme
Uygulama izleme, uygulamanızın özelliklerinin ve bileşenlerinin nasıl kullanıldığını izler. Kullanıcıları etkileyen sorunların yakalanıp yakalanbileceğinden emin olmak için uygulamalarınızı izlemek istiyorsunuz. Uygulama izlemenin sorumluluğu, uygulamanızın iş mantığıyla benzersiz olduğundan, uygulamayı ve hizmetlerini geliştiren kullanıcılar üzerinde yer alır. Uygulamalarınızı izleme, aşağıdaki senaryolarda yararlı olabilir:
* Uygulamamda ne kadar trafik yaşanıyor? -Kullanıcı taleplerini karşılamak veya uygulamanızdaki olası bir sorunu gidermek için hizmetlerinizi ölçeklendirmeniz mi gerekiyor?
* Hizmetmin hizmet çağrıları başarılı ve izleniyor mu?
* Uygulamamın kullanıcıları tarafından hangi eylemler alınır? -Telemetri toplama gelecekteki Özellik geliştirmeye ve uygulama hatalarına yönelik daha iyi tanılamayı yönlendirebilir
* Uygulamam işlenmemiş özel durumlar veriyor mu? 
* Kapsayıcılarım içinde çalışan hizmetlerde neler oluyor?

Uygulama izlemeye ilişkin harika şeyler, geliştiricilerin uygulamanızın bağlamı dahilinde bulunduğundan beğendikleri araçları ve çerçeveyi kullanabilecekleri bir şeydir! Azure Izleyici ile uygulama izlemeye yönelik Azure çözümü hakkında daha fazla bilgi için [Application Insights Ile olay analizinde](service-fabric-diagnostics-event-analysis-appinsights.md)Application Insights.
Ayrıca, [bunu .NET uygulamaları için nasıl ayarlayacağız](service-fabric-tutorial-monitoring-aspnet.md)hakkında bir öğretici sunuyoruz. Bu öğreticide, doğru araçların nasıl yükleneceği, uygulamanıza özel telemetri yazmanın bir örneği, uygulama tanılama ve telemetri Azure portal. 


## <a name="platform-cluster-monitoring"></a>Platform (küme) izleme
Kullanıcı kodun kendisini yazmasından, ancak Service Fabric platformundan tanılama hakkında ne olduğundan, bir Kullanıcı kendi uygulamalarından hangi telemetrinin geldiğini kontrol ediyor mu? Service Fabric amaçlarından biri, uygulamaların donanım arızalarına dayanıklı tutulması için kullanılır. Bu hedef, platformun sistem hizmetlerinin altyapı sorunlarını algılama ve kümedeki diğer düğümlere hızlı yük devretme iş yüklerini algılama yeteneği aracılığıyla gerçekleştirilir. Ancak, bu durumda sistem hizmetlerinin sorun olması durumunda ne olacak? Ya da bir iş yükünü dağıtmaya veya taşımaya çalışıyorsanız, hizmetlerin yerleştirilme kuralları ihlal edilir mi? Service Fabric, kümenizde etkinlik yapma hakkında bilgi sahibi olduğunuzdan emin olmak için bu ve daha fazlası için tanılama sağlar. Küme izlemeye yönelik bazı örnek senaryolar şunlardır:

Service Fabric, kutudan çıkan kapsamlı bir olay kümesi sağlar. Bu [Service Fabric olaylarına](service-fabric-diagnostics-events.md) eventstore veya işlemsel kanal (platform tarafından sunulan olay kanalı) üzerinden erişilebilir. 

* Service Fabric olay kanalları-Windows 'Da Service Fabric olaylar, `logLevelKeywordFilters` işletimsel ve veri & mesajlaşma kanalları arasında seçim yapmak için kullanılan bir dizi ilgisi olan tek BIR ETW sağlayıcısından kullanılabilir. Bu, giden Service Fabric olaylarını gerektiği şekilde filtreleyebilmemiz için ayırdığımız yoldur. Linux 'ta, Service Fabric olayları LTTng aracılığıyla gelir ve gerektiğinde filtrelenebilir bir depolama tablosuna konur. Bu kanallar, kümenizin durumunu daha iyi anlamak için kullanılabilen, seçkin ve yapılandırılmış olayları içerir. Tanılama, küme oluşturma sırasında varsayılan olarak etkinleştirilir. Bu, gelecekte sorgulama yapmanız için bu kanallara ait olayların gönderildiği bir Azure depolama tablosu oluşturur. 

* EventStore-EventStore, Service Fabric Explorer ve REST API aracılığıyla kullanılabilir Service Fabric platform olayları sağlayan platform tarafından sunulan bir özelliktir. Düğüm, hizmet, uygulama ve sorgu gibi her bir varlık için kümenizde neler olduğunu bir anlık görüntü görünümünü görebilirsiniz. Eventstore hakkında daha fazla bilgi için de [Eventstore 'A genel bakış konusuna](service-fabric-diagnostics-eventstore.md)bakabilirsiniz.    

![Ekran görüntüsü düğümler bölmesinin olaylar sekmesini bir Nodeı olayı da dahil olmak üzere birkaç olay gösterir.](media/service-fabric-diagnostics-overview/eventstore.png)

Verilen Tanılamalar, kutudan gelen kapsamlı bir olay kümesi biçiminde bulunur. Bu [Service Fabric olaylar](service-fabric-diagnostics-events.md) , platform tarafından düğümler, uygulamalar, hizmetler, bölümler vb. gibi farklı varlıklarda gerçekleştirilen eylemleri gösterir. Yukarıdaki son senaryoda, bir düğüm daha aşağı gidiyordu `NodeDown` ve tercih ettiğiniz izleme aracınız tarafından anında bildirim alabilirsiniz. Diğer yaygın örnekler, `ApplicationUpgradeRollbackStarted` `PartitionReconfigured` Yük devretme sırasında veya içerir. **Aynı olaylar hem Windows hem de Linux kümelerinde kullanılabilir.**

Olaylar hem Windows hem de Linux 'ta standart kanallar aracılığıyla gönderilir ve bunları destekleyen herhangi bir izleme aracı tarafından okunabilir. Azure Izleyici çözümü Azure Izleyici günlüklerinizi kaydeder. Kümeniz için özel bir işletimsel Pano ve uyarı oluşturabileceğiniz bazı örnek sorgular içeren [Azure izleyici günlükleri tümleştirmemi](service-fabric-diagnostics-event-analysis-oms.md) hakkında daha fazla bilgi edinebilirsiniz. [Platform düzeyi olay ve günlük oluşturma](service-fabric-diagnostics-event-generation-infra.md)konusunda daha fazla küme izleme kavramı mevcuttur.

### <a name="health-monitoring"></a>Sistem durumu izleme
Service Fabric platformu, bir kümedeki varlıkların durumu için Genişletilebilir sistem durumu raporlaması sağlayan bir sistem durumu modeli içerir. Her düğüm, uygulama, hizmet, bölüm, çoğaltma veya örnek, sürekli olarak güncelleştirilebilir bir sistem durumuna sahiptir. Sistem durumu "Tamam", "uyarı" veya "hata" olabilir. Olayları, her bir varlık için sıfatıcı olarak çeşitli varlıklara ve sistem durumuna göre yapılan fiiller olarak Service Fabric düşünün. Belirli bir varlık geçişlerinin sistem durumu her seferinde bir olay da yayınlanır. Bu şekilde, diğer herhangi bir olay gibi, istediğiniz izleme aracında sistem durumu olayları için sorgular ve uyarılar ayarlayabilirsiniz. 

Ayrıca, kullanıcıların varlıklar için sistem durumunu geçersiz kılmasına de izin veririz. Uygulamanız bir yükseltmeden sonra doğrulama testleriniz başarısız olursa, uygulamanızın artık sağlıklı olmadığını belirtmek için sistem durumu API 'sini kullanarak Service Fabric sistem durumuna yazabilir ve Service Fabric yükseltmeyi otomatik olarak geri alır! Sistem durumu modeli hakkında daha fazla bilgi için, [Service Fabric sistem durumu izlemeye giriş](service-fabric-health-introduction.md) konusuna bakın

![SFX sistem durumu panosu](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdogs
Genellikle, bir izleme, hizmetler genelinde sistem durumunu ve yüklemeyi, ping uç noktaları ve kümedeki herhangi bir şey için rapor durumunu izleyebilecek ayrı bir hizmettir. Bu, tek bir hizmetin görünümüne göre algılanamayan hataları önlemeye yardımcı olabilir. Watchdogs, Kullanıcı etkileşimi olmadan (örneğin, belirli zaman aralıklarında depolama alanında günlük dosyalarını temizleme), düzeltici eylemleri gerçekleştiren kodu barındırmak için de iyi bir yerdir. Örnek bir izleme hizmeti uygulamasını [buradan](https://github.com/Azure-Samples/service-fabric-watchdog-service)edinebilirsiniz.

## <a name="infrastructure-performance-monitoring"></a>Altyapı (performans) izleme
Artık uygulamanızdaki ve platformdaki tanılamayı kapsadığımıza göre, donanımın beklendiği gibi çalıştığını nasıl anlarım? Temel altyapınızı izlemek, kümenizin durumunu ve kaynak kullanımınızı anlamak için önemli bir bölümüdür. Sistem performansının ölçülmesi, iş yüklerinize bağlı olarak öznel olabilecek birçok faktöre bağlıdır. Bu faktörler genellikle performans sayaçlarıyla ölçülür. Bu performans sayaçları, işletim sistemi, .NET Framework veya Service Fabric platformunun kendisi de dahil olmak üzere çeşitli kaynaklardan gelebilir. Yararlı olabilecek bazı senaryolar şunlardır

* Donanımım verimli bir şekilde kullanıyorum mi? Donanımınızı %90 CPU veya %10 CPU 'da kullanmak istiyor musunuz? Bu, kümenizi ölçeklendirirken veya uygulamanızın süreçlerini iyileştirirken yararlı bir şekilde sunulur.
* Altyapı sorunlarını önceden tahmin edebilir miyim? -çok sayıda sorun önünde ani değişiklikler (düşler), bu nedenle sorunları önceden tahmin etmek ve tanılamak için ağ g/ç ve CPU kullanımı gibi performans sayaçlarını kullanabilirsiniz.

Altyapı düzeyinde toplanması gereken performans sayaçlarının listesi, [performans ölçümlerinde](service-fabric-diagnostics-event-generation-perf.md)bulunabilir. 

Service Fabric Ayrıca, Reliable Services ve aktör programlama modelleri için bir dizi performans sayacı sağlar. Bu modellerden birini kullanıyorsanız, bu performans sayaçları Aktörlerinizin doğru bir şekilde dönmesini ve doğru şekilde işlenmesini veya güvenilir hizmet isteklerinizin yeterince hızlı işlendiğinden emin olmak için bilgi sağlayabilir. Daha fazla bilgi için bkz. Reliable Actors için [güvenilir hizmet uzaktan iletişim](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) ve [performans izleme](service-fabric-reliable-actors-diagnostics.md#performance-counters)için izleme. 

Bunları toplamaya yönelik Azure Izleyici çözümü, platform düzeyi izleme gibi Azure Izleyici günlüklerdir. Uygun performans sayaçlarını toplamak ve bunları Azure Izleyici günlüklerinde görüntülemek için [Log Analytics Aracısı](service-fabric-diagnostics-oms-agent.md) 'nı kullanmanız gerekir.

## <a name="recommended-setup"></a>Önerilen kurulum
İzleme ve örnek senaryolardaki her bir alanın üzerine gittiğinden, Azure izleme araçlarının bir özeti ve yukarıdaki tüm alanların izlenmesi için gereken ayarlar aşağıda verilmiştir. 

* [Application Insights](service-fabric-tutorial-monitoring-aspnet.md) ile uygulama izleme
* [Tanılama Aracısı](service-fabric-diagnostics-event-aggregation-wad.md) ve [Azure izleyici günlükleri](service-fabric-diagnostics-oms-setup.md) ile küme izleme
* [Azure izleyici günlükleri](service-fabric-diagnostics-oms-agent.md) ile altyapı izleme

Ayrıca, [burada](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) bulunan örnek ARM şablonunu kullanarak tüm gerekli kaynakların ve aracıların dağıtımını otomatik hale getirebilirsiniz. 

## <a name="other-logging-solutions"></a>Diğer günlük çözümleri

Önerdiğimiz iki çözüm, [Azure izleyici günlükleri](service-fabric-diagnostics-event-analysis-oms.md) ve [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) Service Fabric ile birlikte tümleştirilmesine sahip olsa da, birçok olay ETW sağlayıcıları aracılığıyla yazılır ve diğer günlük çözümleriyle genişletilebilir. [Elastik yığına](https://www.elastic.co/products) (özellikle bir çevrimdışı ortamda bir küme çalıştırmayı düşünüyorsanız), [dynaTrace](https://www.dynatrace.com/)veya tercih ettiğiniz başka bir platforma da bakmanız gerekir. [Burada](service-fabric-diagnostics-partners.md)bulunan tümleşik iş ortakları listesine ihtiyacımız var.

Seçtiğiniz her platformun önemli noktaları, Kullanıcı arabirimi, sorgulama özellikleri, kullanılabilir özel görselleştirmeler ve panolar ve izleme deneyiminizi geliştirmek için sağladıkları ek araçların ne kadar rahat olduğunu içermelidir. 

## <a name="next-steps"></a>Sonraki adımlar

* Uygulamalarınızı işaretleme ile çalışmaya başlamak için bkz. [uygulama düzeyi olay ve günlük oluşturma](service-fabric-diagnostics-event-generation-app.md).
* [Service Fabric bir ASP.NET Core uygulamasını izleyerek](service-fabric-tutorial-monitoring-aspnet.md)uygulamanız için Application Insights ayarlama adımlarını izleyin.
* Platformu izleme hakkında daha fazla bilgi edinin ve Service Fabric [Platform düzeyi olay ve günlük oluşturma](service-fabric-diagnostics-event-generation-infra.md)konusunda size sağlar.
* [Bir küme Için Azure izleyici günlüklerini ayarlama](service-fabric-diagnostics-oms-setup.md) sırasında Service Fabric Ile Azure izleyici günlükleri tümleştirmesini yapılandırma
* Azure [Service Fabric 'Da Windows kapsayıcıları için izleme ve tanılama](service-fabric-tutorial-monitoring-wincontainers.md)Için Azure izleyici günlüklerini ayarlama hakkında bilgi edinin.
* [Yaygın senaryoları tanılamada](service-fabric-diagnostics-common-scenarios.md) Service Fabric örnek tanılama sorunlarını ve çözümlerini inceleyin
* [Service Fabric tanılama iş ortaklarının](service-fabric-diagnostics-partners.md) Service Fabric ile tümleştirilen diğer tanılama ürünlerini inceleyin
* Azure kaynakları için genel izleme önerileri hakkında bilgi edinin- [En Iyi Yöntemler-izleme ve tanılama](/azure/architecture/best-practices/monitoring). 
