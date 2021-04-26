---
title: Azure Izleyici günlükleri ile Azure Service Fabric olay Analizi
description: Azure Service Fabric kümelerini izleme ve tanılamaya yönelik Azure Izleyici günlüklerini kullanarak olayları görselleştirme ve çözümleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: f1e22213c857b400cc36c51cefb90e2379352893
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628977"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Azure Izleyici günlükleri ile olay Analizi ve görselleştirme
 Azure Izleyici günlükleri, bulutta barındırılan uygulama ve hizmetlerden Telemetriyi toplayıp analiz eder ve kullanılabilirlik ve performansını en üst düzeye çıkarmanıza yardımcı olacak analiz araçları sağlar. Bu makalede, Azure Izleyici günlüklerinde sorguların nasıl çalıştırılacağı özetlenmektedir ve kümenizde neler olduğunu gidermeye yönelik bilgiler verilmektedir. Aşağıdaki sık sorulan sorular giderilmiştir:

* Nasıl yaparım? sistem durumu olaylarıyla ilgili sorun giderme
* Nasıl yaparım? bir düğüm ne zaman geçer?
* Nasıl yaparım? uygulamanızın Hizmetleri başlatılıp başlatılmamışsa veya durdurulduğunda emin misiniz?

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Log Analytics çalışma alanına genel bakış

>[!NOTE] 
>Tanılama depolaması, küme oluşturma sırasında varsayılan olarak etkinken, tanılama depolamadan okumak için Log Analytics çalışma alanını ayarlamaya devam etmeniz gerekir.

Azure Izleyici günlükleri, Azure depolama tablosu veya aracı dahil olmak üzere yönetilen kaynaklardan veri toplar ve merkezi bir depoda saklar. Veriler daha sonra analiz, uyarı, görselleştirme veya daha fazla dışa aktarma için kullanılabilir. Azure Izleyici günlükleri olayları, performans verilerini veya diğer özel verileri destekler. Verilerin Azure Izleyici günlüklerine akışını sağlamak için [depolama alanındaki olaylardan okumak üzere bir Log Analytics çalışma alanı oluşturmaya](service-fabric-diagnostics-oms-setup.md) yönelik olayları ve adımları [toplamak üzere tanılama uzantısını yapılandırmak için adımları](service-fabric-diagnostics-event-aggregation-wad.md) gözden geçirin.

Azure Izleyici günlükleri tarafından veriler alındıktan sonra Azure, çok sayıda senaryoya özelleştirilmiş, gelen verileri izlemek için önceden paketlenmiş çözümler veya işlem panoları olan çok sayıda *Izleme çözümü* içerir. Bunlar, Service Fabric kümeler kullanılırken Tanılamanın ve izlemenin en ilgili iki ilgisi olan bir *Service Fabric Analytics* çözümü ve *kapsayıcılar* çözümü içerir. Bu makalede, çalışma alanıyla oluşturulan Service Fabric Analytics çözümünün nasıl kullanılacağı açıklanır.

## <a name="access-the-service-fabric-analytics-solution"></a>Service Fabric Analytics çözümüne erişin

[Azure portalında](https://portal.azure.com), Service Fabric Analytics çözümünü oluşturduğunuz kaynak grubuna gidin.

Kaynak **Servicefabric \<nameOfOMSWorkspace\>**' i seçin.

İçinde `Summary` , Service Fabric için bir de dahil olmak üzere her bir çözümün etkin olduğu bir grafik biçiminde kutucuklar görürsünüz. Service Fabric Analytics çözümüne devam etmek için **Service Fabric** grafiğine tıklayın.

![Service Fabric çözümü](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

Aşağıdaki görüntüde Service Fabric Analytics çözümünün giriş sayfası gösterilmektedir. Bu giriş sayfası, kümenizde neler olduğunu bir anlık görüntü görünümü sağlar.

![Service Fabric Analytics çözümünün ana sayfasını gösteren ekran görüntüsü.](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 Küme oluşturma sonrasında tanılamayı etkinleştirdiyseniz, için olayları görebilirsiniz 

* [Küme olaylarını Service Fabric](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programlama modeli olayları](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programlama modeli olayları](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Service Fabric olaylarını kutudan sonuna ek olarak, [Tanılama uzantınızın yapılandırması güncelleştirilerek](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)daha ayrıntılı sistem olayları toplanabilir.

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>Düğümlerde eylemler dahil Service Fabric olaylarını görüntüleme

Service Fabric Analytics sayfasında, **Service Fabric olaylar** için grafiğe tıklayın.

![Service Fabric çözümü Işlem kanalı](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

Bir listedeki olayları görüntülemek için **Listele** ' ye tıklayın. Bundan sonra, toplanan tüm sistem olaylarını görürsünüz. Bu, Azure depolama hesabındaki **Wadservicefabricsystemeventstable** ve benzer şekilde daha sonra gördüğünüz güvenilir hizmetler ve aktör olayları bu ilgili tablolardan alınan bir başvurudur.
    
![Işlem kanalını sorgula](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

Alternatif olarak, sol taraftaki Büyüteç Camı ' na tıklayıp kusto sorgu dilini kullanarak aradığınızı bulabilirsiniz. Örneğin, kümedeki düğümlerde gerçekleştirilen tüm eylemleri bulmak için aşağıdaki sorguyu kullanabilirsiniz. Aşağıda kullanılan olay kimlikleri [İşlemsel kanal olayları başvurusunda](service-fabric-diagnostics-event-generation-operational.md)bulunur.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Sistem hizmeti (görevadı) gibi bir çok daha fazla alanda sorgulama yapabilirsiniz.

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>Güvenilir hizmet ve aktör olaylarını görüntüleme Service Fabric

Service Fabric Analytics sayfasında, **Reliable Services** grafiğine tıklayın.

![Service Fabric çözümü Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

Bir listedeki olayları görüntülemek için **Listele** ' ye tıklayın. Burada güvenilir hizmetlerden gelen olayları görebilirsiniz. RunAsync hizmeti başlatıldığında ve tamamlandığında, genellikle dağıtımlar ve yükseltmelerde gerçekleşen farklı olayları görebilirsiniz. 

![Sorgu Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

Güvenilir aktör olayları benzer bir biçimde görüntülenebilir. Güvenilir aktörler için daha ayrıntılı olaylar yapılandırmak üzere, `scheduledTransferKeywordFilter` tanı uzantısının (aşağıda gösterilen) yapılandırmasında öğesini değiştirmeniz gerekir. Bunların değerlerinin ayrıntıları, [güvenilir aktör olayları başvurusunda](service-fabric-reliable-actors-diagnostics.md#keywords)bulunur.

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Kusto sorgu dili güçlü. En fazla olayı hangi düğümlerin oluşturduğunu öğrenmek için çalıştırabileceğiniz başka bir değerli sorgu de kullanabilirsiniz. Aşağıdaki ekran görüntüsünde bulunan sorgu, belirli bir hizmet ve düğümle toplanan Service Fabric işletimsel olayları gösterir.

![Düğüm başına sorgu olayları](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>Sonraki adımlar

* Altyapı izlemeyi etkinleştirmek için (performans sayaçlarını, [Log Analytics aracısını eklemek](service-fabric-diagnostics-oms-agent.md)için üzerine gidin. Aracı, performans sayaçlarını toplar ve bunları mevcut çalışma alanınıza ekler.
* Azure Izleyici günlükleri, şirket içi kümeler için Azure Izleyici günlüklerine veri göndermek için kullanılabilen bir ağ geçidi (HTTP Iletme proxy) sunar. [Log Analytics ağ geçidini kullanarak Azure izleyici günlüklerine Internet erişimi olmadan bilgisayarları bağlama](../azure-monitor/agents/gateway.md)konusunda daha fazla bilgi edinin.
* Algılama ve tanılamada yardımcı olması için  [otomatik uyarı](../azure-monitor/alerts/alerts-overview.md) yapılandırma.
* [Günlük araması ve](../azure-monitor/logs/log-query-overview.md) Azure izleyici günlüklerinin bir parçası olarak sunulan özellikleri sorgulama ile familiarized alın.
* Azure Izleyici günlüklerine ve neler sunduğunu daha ayrıntılı bir şekilde öğrenin, [Azure izleyici günlüklerini okuyun?](../azure-monitor/overview.md).
