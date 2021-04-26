---
title: Azure Service Fabric platform düzeyinde Izleme
description: Azure Service Fabric kümelerini izlemek ve tanılamak için kullanılan platform düzeyi olayları ve günlükleri hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: f9104c390ab4115c626beb4759c6b6952d691ca9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626529"
---
# <a name="monitoring-the-cluster"></a>Kümeyi izleme

Donanımınızın ve kümenizin beklendiği gibi davranıp davranmadığını öğrenmek için küme düzeyinde izlemeniz önemlidir. Service Fabric, bir donanım hatası sırasında uygulamaları çalışır durumda tutabilir, ancak yine de bir uygulamada veya temeldeki altyapıda hata oluşup oluşmadığını tanılamanıza gerek kalmaz. Ayrıca, daha fazla kapasite planlamak için kümelerinizi izlemeniz ve donanım ekleme veya kaldırma kararlarına yardımcı olmanız gerekir.

Service Fabric, çeşitli yapılandırılmış platform olaylarını EventStore ve çeşitli günlük kanalları aracılığıyla [Service Fabric olaylar](service-fabric-diagnostics-events.md)olarak kullanıma sunar. 

Windows 'da Service Fabric olayları, `logLevelKeywordFilters` işletimsel ve veri & mesajlaşma kanalları arasında seçim yapmak için kullanılan bir dizi ilgili olan tek BIR ETW sağlayıcısından kullanılabilir. Bu, giden Service Fabric olaylarını gerektiği şekilde filtreleyebiliriz.

* **İşletimsel** Service Fabric ve kümesi tarafından gerçekleştirilen bir düğümün olayları, dağıtılan yeni bir uygulama veya yükseltme geri alma vb. tarafından gerçekleştirilen üst düzey işlemler. Olayların tam listesini [buradan](service-fabric-diagnostics-event-generation-operational.md)görebilirsiniz.  

* **İşletimsel-ayrıntılı**  
Durum raporları ve yük dengeleme kararları.

İşlem kanalına ETW/Windows olay [günlükleri (Windows](service-fabric-diagnostics-eventstore.md) kümeleri için 6,2 ve üzeri sürümlerde Windows 'da bulunur) dahil olmak üzere çeşitli yollarla erişilebilir. EventStore, kümenizin olaylarına bir varlık temelinde (küme, düğümler, uygulamalar, hizmetler, bölümler, çoğaltmalar ve kapsayıcılar dahil olmak üzere) erişim sağlar ve bunları REST API 'Leri ve Service Fabric istemci kitaplığı aracılığıyla kullanıma sunar. Geliştirme ve test kümelerinizi izlemek için EventStore 'u kullanın ve üretim kümelerinizin durumunu bir noktadan anlayabilirsiniz.

* **Veri & mesajlaşma**  
Mesajlaşma 'da oluşturulan kritik Günlükler ve Olaylar (Şu anda yalnızca Smarproxy) ve veri yolu (güvenilir hizmet modelleri).

* **Veri & mesajlaşma-ayrıntılı**  
Kümedeki tüm kritik olmayan günlüklerin (Bu kanalın çok yüksek sayıda olay hacmi vardır) bulunduğu ayrıntılı kanal.

Bunlara ek olarak, sunulan iki adet yapılandırılmış EventSource kanalı ve destek amaçlarıyla topladığımız günlükleri de vardır.

* [Reliable Services olayları](service-fabric-reliable-services-diagnostics.md)  
Model özel olayları programlama.

* [Reliable Actors olayları](service-fabric-reliable-actors-diagnostics.md)  
Model özel olayları ve performans sayaçlarını programlama.

* Destek günlükleri  
Service Fabric tarafından oluşturulan sistem günlükleri yalnızca destek sağlarken ABD tarafından kullanılır.

Bu çeşitli kanallar, önerilen platform düzeyinde günlüğe kaydetme işleminin çoğunu kapsar. Platform düzeyinde günlüğe kaydetmeyi geliştirmek için, sistem durumu modelini daha iyi anlamak ve özel durum raporları eklemek ve özel **performans sayaçlarını** ekleyerek, hizmet ve uygulamanızın kümedeki etkisini gerçek zamanlı olarak anlamak için kullanmayı düşünün.

Bu günlüklerin avantajlarından yararlanabilmek için, Azure portalında küme oluşturma sırasında "Tanılama" özelliğinin etkin bırakılması kesinlikle önerilir. Tanılama 'yı etkinleştirerek, küme dağıtıldığında Windows Azure Tanılama, Işletimsel, Reliable Services ve güvenilir aktör kanallarını kabul edebilir ve verileri [Azure tanılama toplama olaylarında](service-fabric-diagnostics-event-aggregation-wad.md)daha da ayrıntılı olarak depolar.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric sistem durumu ve yük raporlama

Service Fabric, bu makalelerde ayrıntılı olarak açıklanan kendi sistem durumu modeline sahiptir:

- [Service Fabric sistem durumu izlemeye giriş](service-fabric-health-introduction.md)
- [Hizmet durumunu raporlama ve denetleme](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Özel Service Fabric sistem durumu raporları ekleme](service-fabric-report-health.md)
- [Service Fabric sistem durumu raporlarını görüntüleme](service-fabric-view-entities-aggregated-health.md)

Sistem durumu izleme, özellikle bir uygulama yükseltmesi sırasında bir hizmetin birden çok yönü için kritik öneme sahiptir. Hizmetin her bir yükseltme etki alanı yükseltildikten sonra, dağıtım bir sonraki yükseltme etki alanına geçmeden önce yükseltme etki alanı, sistem durumu denetimleri iletmelidir. Tamam sistem durumu ulaşılıp, uygulamanın bilinen bir Tamam durumunda kalması için dağıtım geri alınır. Hizmetler geri alınmadan önce bazı müşteriler etkilenebilir, ancak müşterilerin çoğu bir sorunla karşılaşmaz. Ayrıca, bir çözüm bir insan işlecinden eylemi beklemek zorunda kalmadan nispeten daha hızlı gerçekleşir. Kodunuza eklenen durum denetimleri ne kadar fazla olduğunda, hizmetiniz dağıtım sorunlarına karşı daha dayanıklı olur.

Hizmet sistem durumunun başka bir yönü, hizmetten rapor ölçümleridir. Ölçümler, kaynak kullanımını dengelemek için kullanıldıkları için Service Fabric önemlidir. Ölçümler Ayrıca sistem durumunun bir göstergesi olabilir. Örneğin, çok sayıda hizmeti olan bir uygulamanız olabilir ve her örnek, saniye başına istek (RPS) ölçümü rapor edebilir. Bir hizmet başka bir hizmetten daha fazla kaynak kullanıyorsa, kaynak kullanımını bile sürdürmek için Service Fabric hizmet örneklerini küme etrafında hareket ettirir. Kaynak kullanımının nasıl çalıştığı hakkında daha ayrıntılı bir açıklama için bkz. [ölçüm ile Service Fabric kaynak tüketimini yönetme ve yükleme](service-fabric-cluster-resource-manager-metrics.md).

Ölçümler Ayrıca hizmetinizin nasıl çalıştığını kavramak için size yardımcı olabilir. Zaman içinde, hizmetin beklenen parametrelerde çalışıyor olduğunu denetlemek için ölçümleri kullanabilirsiniz. Örneğin, eğilimler Pazartesi sabah Pazartesi sabah 00 ' da, ortalama RPS 1.000 ise, RPS 500 ' in altında veya 1.500 ' in altındaysa sizi uyaran bir sistem durumu raporu ayarlayabilirsiniz. Her şey kusursuz olabilir, ancak müşterilerinizin harika bir deneyim yaşadığından emin olmak için bu bir görünüme sahip olabilirsiniz. Hizmetiniz, sistem durumu denetimi amacıyla bildirilemeyen, ancak kümenin kaynak dengelemesini etkilemeyen bir dizi ölçüm tanımlayabilir. Bunu yapmak için, ölçüm kalınlığını sıfır olarak ayarlayın. Tüm ölçümleri sıfır ağırlığıyla başlatmanız önerilir ve ölçümlerin, kümenizin kaynak dengeleyiciyi nasıl etkilediğini anladığınızdan emin oluncaya kadar ağırlığı artırmaz.

> [!TIP]
> Çok fazla ağırlıklı ölçüm kullanmayın. Hizmet örneklerinin dengeleme için neden taşınmakta olduğunu anlamak zor olabilir. Birkaç ölçüm uzun bir zaman alabilir!

Uygulamanızın sistem durumunu ve performansını gösterebilen herhangi bir bilgi, ölçümler ve sistem durumu raporları için aday bir adaydır. **Bir CPU performans sayacı, düğümünüz nasıl kullanıldığını söyleyebilir, ancak belirli bir hizmetin sağlıklı olup olmadığını söylemez, çünkü birden çok hizmet tek bir düğümde çalışıyor olabilir.** Ancak, RPS gibi ölçümler, işlenen öğeler ve istek gecikmesi tümü, belirli bir hizmetin sistem durumunu gösterebilir.

## <a name="service-fabric-support-logs"></a>Service Fabric destek günlükleri

Azure Service Fabric kümeniz hakkında yardım almak için Microsoft desteği 'ne başvurmanız gerekirse, destek günlükleri neredeyse her zaman gereklidir. Kümeniz Azure 'da barındırılıyorsa, destek günlükleri otomatik olarak yapılandırılır ve küme oluşturmanın bir parçası olarak toplanır. Günlükler, kümenizin kaynak grubundaki ayrılmış bir depolama hesabında depolanır. Depolama hesabının sabit bir adı yoktur, ancak hesapta, *yapıyla* başlayan adlara sahip blob kapsayıcıları ve tabloları görürsünüz. Tek başına küme için günlük koleksiyonları ayarlama hakkında daha fazla bilgi için, tek başına bir [Windows kümesi için](service-fabric-cluster-manifest.md) [tek başına Azure Service Fabric kümesi ve yapılandırma ayarlarını oluşturma ve yönetme](service-fabric-cluster-creation-for-windows-server.md) konusuna bakın. Tek başına Service Fabric örnekleri için, günlüklerin yerel bir dosya paylaşımında gönderilmesi gerekir. Bu günlüklere destek için sahip olmanız **gerekir** , ancak Microsoft müşteri destek ekibinin dışındaki herkes tarafından kullanılmaya yönelik değildir.

## <a name="measuring-performance"></a>Performansı ölçme

Kümenizin performans ölçümü, kümenizin ölçeklendirilmesine ilişkin yükleme ve sürücü kararlarını nasıl işleyebileceğinizi anlamanıza yardımcı olur (bkz. [Azure 'da](service-fabric-cluster-scale-in-out.md)küme ölçekleme veya [Şirket içi](service-fabric-cluster-windows-server-add-remove-nodes.md)). Performans verileri, gelecekte Günlükler analiz edilirken sizin veya uygulama ve hizmetleriniz ile karşılaştırıldığında da yararlıdır. 

Service Fabric kullanırken toplanacak performans sayaçlarının listesi için, bkz. [Service Fabric performans sayaçları](service-fabric-diagnostics-event-generation-perf.md)

Kümeniz için performans verilerini toplamayı ayarlayabileceğiniz iki ortak yol aşağıda verilmiştir:

* **Aracı kullanma**  
Bu, aracıların genellikle toplanabilecek olası performans ölçümlerinin bir listesi olduğundan ve toplamak veya değiştirmek istediğiniz ölçümleri belirlemek görece daha kolay bir işlemdir ve bir makineden performansı toplamanın tercih edilen yoludur. Azure izleyici hakkında bilgi edinin Azure izleyici günlüklerini Service Fabric Azure izleyici günlükleri ile [tümleştirerek](service-fabric-diagnostics-event-analysis-oms.md) , küme VM 'leri ve dağıtılan kapsayıcılar için performans verilerini seçebilen bir izleme aracısı olan Log Analytics Aracısı hakkında daha fazla bilgi edinmek için [Log Analytics aracısını ayarlama](../azure-monitor/agents/agent-windows.md) .

* **Azure Tablo depolama için performans sayaçları**  
Ayrıca, performans ölçümlerini aynı tablo depolama alanına olaylarla gönderebilirsiniz. Bunun yapılması, Azure Tanılama yapılandırmasının kümenizdeki VM 'lerden uygun performans sayaçlarını seçmesini ve herhangi bir kapsayıcı dağıtabilmeniz durumunda Docker istatistiklerini seçmesini sağlar. Performans sayacı toplamayı ayarlamak için Service Fabric 'da [WAD 'de performans sayaçlarını](service-fabric-diagnostics-event-aggregation-wad.md) yapılandırma hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

* Küme tanılamayı toplamak ve özel sorgular ve uyarılar oluşturmak için Service Fabric [Azure izleyici günlüklerini tümleştirme](service-fabric-diagnostics-event-analysis-oms.md) hakkında bilgi edinin
* Oluşturulan tanılama deneyimi, [Eventstore](service-fabric-diagnostics-eventstore.md) hakkında bilgi edinin Service Fabric
* Service Fabric içindeki bazı [yaygın tanılama senaryolarına](service-fabric-diagnostics-common-scenarios.md) yol gösterir
