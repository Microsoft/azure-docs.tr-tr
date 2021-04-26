---
title: İzleme Time Series Insights | Microsoft Docs
description: Kullanılabilirlik, performans ve işlem için Time Series Insights izleyin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 15054cd508ce7a6de51fea86ca02302fba258757
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504932"
---
# <a name="monitoring-time-series-insights"></a>İzleme Time Series Insights

Azure kaynaklarına bağlı kritik Uygulamalarınız ve iş süreçleriniz olduğunda, bu kaynakları kullanılabilirlik, performans ve işlem için izlemek istersiniz. Bu makalede, Time Series Insights tarafından oluşturulan izleme verileri ve bu verileri çözümlemek ve uyarmak için Azure Izleyici 'nin özelliklerini nasıl kullanabileceğiniz açıklanmaktadır.

## <a name="monitor-overview"></a>İzlemeye genel bakış

Her Time Series Insights ortamı için Azure portal **genel bakış** sayfası, alınan ileti sayısı ve depolanan bayt sayısı gibi kaynak kullanımının kısa bir görünümünü içerir. Bu bilgiler faydalı olur, ancak bu bölmeden yalnızca küçük miktarda izleme verisi bulunabilir. Bu verilerden bazıları otomatik olarak toplanır ve kaynağı oluşturur almaz analiz için kullanılabilir. Bazı yapılandırma ile ek veri koleksiyonu türlerini etkinleştirebilirsiniz.

## <a name="what-is-azure-monitor"></a>Azure Izleyici nedir?

Time Series Insights, Azure 'da, diğer bulutlardaki ve Şirket içindeki kaynaklara ek olarak Azure kaynaklarınızı izlemeye yönelik eksiksiz bir özellik kümesi sunan Azure [izleyici](../azure-monitor/overview.md)'yi kullanarak izleme verileri oluşturur.

Aşağıdaki kavramları açıklayan Azure [izleyici Ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md)makalesini başlatın:

- Azure İzleyici nedir?
- İzleme ile ilişkili maliyetler
- Azure 'da toplanan verileri izleme
- Veri toplamayı yapılandırma
- İzleme verilerini analiz etmek ve uyarı vermek için Azure 'da standart araçlar

Aşağıdaki bölümler, Azure Time Series Insights için toplanan belirli verileri açıklayarak bu makalede derleme oluşturur. Bu bölümler, veri toplamayı yapılandırmak ve bu verileri Azure araçlarıyla çözümlemek için örnekler de sağlar.

> [!TIP]
> Azure Izleyici ile ilişkili maliyetleri anlamak için bkz. [kullanım ve tahmini maliyetler](../azure-monitor//usage-estimated-costs.md). Verilerinizin Azure Izleyici 'de görünmesi için geçen süreyi anlamak için bkz. [günlük verisi alma süresi](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Azure Time Series Insights verileri izleme

Azure Time Series Insights, [Azure kaynaklarından gelen verileri izleme](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar.

Toplayacağınız günlüklere ve ölçümlere ilişkin ayrıntılı bir başvuru için bkz. [Azure Time Series Insights izleme verileri başvurusu](how-to-monitor-tsi-reference.md) .

## <a name="collection-and-routing"></a>Toplama ve yönlendirme

Platform ölçümleri otomatik olarak toplanır ve depolanır, ancak bir tanılama ayarı kullanılarak başka konumlara yönlendirilebilir.

Kaynak günlükleri, bir tanılama ayarı oluşturup bunları bir veya daha fazla konuma yönlendirene kadar toplanmaz ve depolanmaz.
Azure portal, CLı veya PowerShell kullanarak bir tanılama ayarı oluşturmaya yönelik ayrıntılı süreç için [Azure 'da platform günlüklerini ve ölçümlerini toplamak üzere tanılama ayarı oluşturma](../azure-monitor/essentials/diagnostic-settings.md) konusuna bakın. Bir tanılama ayarı oluşturduğunuzda hangi günlük kategorilerinin toplanacağını belirlersiniz.

Azure Time Series Insights için aşağıdaki kategorilerden günlükleri toplayabilirsiniz:

   | Kategori | Açıklama |
   |---|---|
   | Giriş  | Giriş kategorisi, giriş ardışık düzeninde oluşan hataları izler. Bu kategori olayları alırken meydana gelen hataları (örneğin, bir olay kaynağına bağlanma hataları) ve olayları (bir olay yükünü ayrıştırırken hatalar gibi) işlemeyi içerir. |

## <a name="analyzing-metrics"></a>Ölçümler çözümleniyor

Azure Izleyici menüsünden ölçümler ' i açarak, Azure Time Series Insights ölçümlerini, diğer Azure hizmetlerinden alınan ölçümlerle birlikte analiz edebilirsiniz. Bu aracı kullanma hakkında ayrıntılı bilgi için bkz. [Azure Ölçüm Gezgini](../azure-monitor/essentials/metrics-getting-started.md) kullanmaya başlama.

Toplanan platform ölçümlerinin bir listesi için bkz. [Azure Time Series Insights veri başvurusunu izleme](how-to-monitor-tsi-reference.md#metrics)

Bu örnek, tüm olay kaynaklarından alınan bayt sayısını Azure Time Series Insights ortamınıza gösterir.

Giriş **alınan bayt sayısı** [ ![ Azure zaman serisi giriş bayt sayısı](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

Örnek, Azure Time Series Insights ortamınızda başarıyla işlenen ve sorgu için kullanılabilir bayt sayısını gösterir.

 [ ![ Azure zaman serisi giriş depolanan bayt](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox) kaynakları

## <a name="analyzing-logs"></a>Günlükler çözümleniyor
Kaynak günlüklerine bir depolama hesabında blob olarak, olay verileri olarak veya log analitik sorguları aracılığıyla erişebilirsiniz.

Azure Izleyici günlüklerindeki veriler, her tablonun kendine ait benzersiz özellikler kümesine sahip olduğu tablolarda depolanır.

Azure Izleyici 'deki tüm kaynak günlüklerine aynı alanlar ve hizmete özgü alanlar gelir. Ortak şema, [Azure izleyici kaynak günlüğü şemasında](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)özetlenmiştir. Azure Time Series Insights için toplanan kaynak günlüklerinin türlerinin bir listesi için, bkz. [Azure Time Series Insights izleme veri başvurusu](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights, verileri aşağıdaki tablolarda depolar.

| Tablo | Açıklama |
|:---|:---|
| Tsıingress | Giriş kategorisinden veri depolayan tablo. Giriş kategorisi, giriş ardışık düzeninde oluşan hataları izler. Bu kategori olayları alırken meydana gelen hataları (örneğin, bir olay kaynağına bağlanma hataları) ve olayları (bir olay yükünü ayrıştırırken hatalar gibi) işlemeyi içerir.

Verileri Azure Izleyici günlüklerine yönlendirmek için bir Log Analytics çalışma alanına kaynak günlükleri veya platform ölçümleri göndermek üzere bir tanılama ayarı oluşturmanız gerekir. Daha fazla bilgi için bkz. [koleksiyon ve yönlendirme](../iot-hub/monitor-iot-hub.md#collection-and-routing).

## <a name="sample-queries"></a>Örnek sorgular

Azure Time Series Insights ortamınızı izlemenize yardımcı olması için kullanabileceğiniz sorgular aşağıda verilmiştir:

+ Son beş gündeki olay kaynağı bağlantı hatalarıyla ilgili ayrıntıları alın:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Son beş gün içinde alınan geçersiz iletilerle ilgili ayrıntıları alma:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Uyarılar

İzleme verilerinizde önemli koşullar bulunduğunda Azure Izleyici uyarıları size önceden bildirimde bulunur. Bunlar, müşterilerinizin sorunları fark etmeden önce sisteminizdeki sorunları tanımlamanızı ve ele belirlemenizi sağlar. [Ölçümler](../azure-monitor/alerts/alerts-metric-overview.md), [Günlükler](../azure-monitor/alerts/alerts-unified-log.md)ve [etkinlik günlüğü](../azure-monitor/alerts/activity-log-alerts.md)hakkında uyarı ayarlayabilirsiniz. Farklı uyarı türlerinin avantajları ve dezavantajları vardır.

Platform ölçümlerini temel alan bir uyarı kuralı oluştururken, sayı birimlerinde toplanan Time Series Insights platform ölçümleri için bazı toplamaların kullanılamayabilir veya kullanılamıyor olabileceğini unutmayın.

## <a name="next-steps"></a>Sonraki Adımlar

* Azure Time Series Insights tarafından oluşturulan günlüklerin ve ölçümlerin bir başvurusu için bkz. [Azure Time Series Insights izleme verileri başvurusu](how-to-monitor-tsi-reference.md) .
* Azure kaynaklarını izleme hakkında ayrıntılı bilgi için bkz. Azure [izleyici ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md) .