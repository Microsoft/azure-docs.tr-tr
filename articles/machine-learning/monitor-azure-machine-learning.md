---
title: İzleme Azure Machine Learning | Microsoft Docs
description: Azure Izleyici 'yi kullanarak Azure Machine Learning ölçümler üzerinde uyarıları görüntüleme, çözümleme ve oluşturma hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: e5fd0fdd5a6f9a4a7537a844b096efdfef253638
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816863"
---
# <a name="monitor-azure-machine-learning"></a>Azure Machine Learning'i izleme

Azure kaynaklarına bağlı kritik Uygulamalarınız ve iş süreçleriniz olduğunda, bu kaynakları kullanılabilirlik, performans ve işlem için izlemek istersiniz. Bu makalede, Azure Machine Learning tarafından oluşturulan izleme verileri ve Azure Izleyici ile bu verileri çözümleme ve uyarma açıklanır.

> [!TIP]
> Bu belgedeki bilgiler, Azure Machine Learning hizmeti ve ilişkili Azure hizmetleri için izlemeyi açıkladığı gibi birincil olarak __Yöneticiler__ içindir. Bir __veri bilimcu__ veya __geliştiricisiyseniz__ ve *model eğitim çalışmalarınız* için özel bilgileri izlemek istiyorsanız aşağıdaki belgelere bakın:
>
> * [Eğitim çalıştırmalarını başlatın, izleyin ve iptal edin](how-to-track-monitor-analyze-runs.md)
> * [Eğitim çalıştırmaları için günlük ölçümleri](how-to-log-view-metrics.md)
> * [MLflow ile denemeleri izleme](how-to-use-mlflow.md)
> * [TensorBoard ile çalıştırmaları görselleştirme](how-to-monitor-tensorboard.md)
>
> Web Hizmetleri veya IoT Edge modülleri olarak dağıtılan modeller tarafından oluşturulan bilgileri izlemek isterseniz, bkz. [model verilerini toplama](how-to-enable-data-collection.md) ve [Application Insights ile izleme](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Azure İzleyici nedir?

Azure Machine Learning Azure 'da tam yığın izleme hizmeti olan [Azure izleyici](../azure-monitor/overview.md)'yi kullanarak izleme verileri oluşturur. Azure Izleyici, Azure kaynaklarınızı izlemeye yönelik kapsamlı bir özellik kümesi sağlar. Ayrıca, diğer bulutlardaki ve Şirket içindeki kaynakları da izleyebilir.

Aşağıdaki kavramları açıklayan Azure [izleyici Ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md)makalesini başlatın:

- Azure İzleyici nedir?
- İzleme ile ilişkili maliyetler
- Azure 'da toplanan verileri izleme
- Veri toplamayı yapılandırma
- İzleme verilerini analiz etmek ve uyarı vermek için Azure 'da standart araçlar

Aşağıdaki bölümler, Azure Machine Learning için toplanan belirli verileri açıklayarak bu makalede derleme oluşturur. Bu bölümler, veri toplamayı yapılandırmak ve bu verileri Azure araçlarıyla çözümlemek için örnekler de sağlar.

> [!TIP]
> Azure Izleyici ile ilişkili maliyetleri anlamak için bkz. [kullanım ve tahmini maliyetler](../azure-monitor//usage-estimated-costs.md). Verilerinizin Azure Izleyici 'de görünmesi için geçen süreyi anlamak için bkz. [günlük verisi alma süresi](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure Machine Learning verileri izleme

Azure Machine Learning, [Azure kaynaklarından gelen verileri izleme](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar. 

Azure Machine Learning tarafından oluşturulan günlüklere ve ölçümlere ilişkin ayrıntılı bir başvuru için bkz. [Azure Machine Learning izleme verileri başvurusu](monitor-resource-reference.md) .

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Toplama ve yönlendirme

Platform ölçümleri ve etkinlik günlüğü otomatik olarak toplanır ve depolanır, ancak bir tanılama ayarı kullanılarak başka konumlara yönlendirilebilir.  

Kaynak günlükleri, bir tanılama ayarı oluşturup bunları bir veya daha fazla konuma yönlendirene kadar toplanmaz ve depolanmaz.

Azure portal, CLı veya PowerShell kullanarak bir tanılama ayarı oluşturmaya yönelik ayrıntılı süreç için [Azure 'da platform günlüklerini ve ölçümlerini toplamak üzere tanılama ayarı oluşturma](../azure-monitor/essentials/diagnostic-settings.md) konusuna bakın. Bir tanılama ayarı oluşturduğunuzda hangi günlük kategorilerinin toplanacağını belirlersiniz. Azure Machine Learning kategorileri [Azure Machine Learning izleme veri başvurusunda](monitor-resource-reference.md#resource-logs)listelenmiştir.

> [!IMPORTANT]
> Bu ayarları etkinleştirmek, ek Azure Hizmetleri (depolama hesabı, Olay Hub 'ı veya Log Analytics) gerektirir ve bu da maliyetinizi artırabilir. Tahmini maliyeti hesaplamak için [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator)' ı ziyaret edin.

Azure Machine Learning için aşağıdaki günlükleri yapılandırabilirsiniz:

| Kategori | Açıklama |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning işlem kümelerinin olayları. |
| AmlComputeClusterNodeEvent | Bir Azure Machine Learning işlem kümesi içindeki düğümlerden olaylar. |
| AmlComputeJobEvent | Azure Machine Learning işlem sırasında çalışan işlerin olayları. |

> [!NOTE]
> Bir tanılama ayarında ölçümleri etkinleştirdiğinizde, boyut bilgisi şu anda bir depolama hesabına, Olay Hub 'ına veya Log Analytics 'e gönderilen bilgilerin bir parçası olarak dahil edilmez.

Toplayacağınız ölçümler ve Günlükler aşağıdaki bölümlerde ele alınmıştır.

## <a name="analyzing-metrics"></a>Ölçümler çözümleniyor

**Azure izleyici** menüsünden **ölçümler** ' i açarak, Azure Machine Learning ölçümlerini, diğer Azure hizmetlerinden alınan ölçümlerle birlikte analiz edebilirsiniz. Bu aracı kullanma hakkında ayrıntılı bilgi için bkz. [Azure Ölçüm Gezgini](../azure-monitor/essentials/metrics-getting-started.md) kullanmaya başlama.

Toplanan platform ölçümlerinin bir listesi için bkz. [izleme Azure Machine Learning veri başvuru ölçümleri](monitor-resource-reference.md#metrics).

Azure Machine Learning tüm ölçümler, **Machine Learning hizmet çalışma** alanında yer alan ad alanıdır.

![Machine Learning hizmeti çalışma alanı seçili Ölçüm Gezgini](./media/monitor-azure-machine-learning/metrics.png)

Başvuru için, [Azure izleyici 'de desteklenen tüm kaynak ölçümlerinin](../azure-monitor/essentials/metrics-supported.md)bir listesini görebilirsiniz.

> [!TIP]
> Azure Izleyici ölçüm verileri 90 gün boyunca kullanılabilir. Ancak grafik oluştururken yalnızca 30 gün görselleştirilebilir. Örneğin, 90 günlük bir dönemi görselleştirmek istiyorsanız, bu alanı 90 gün süresince 30 günlük üç grafik içine kesmeniz gerekir.
### <a name="filtering-and-splitting"></a>Filtreleme ve bölme

Boyutları destekleyen ölçümler için bir boyut değeri kullanarak filtre uygulayabilirsiniz. Örneğin, bir **küme adı** Için **etkin çekirdekleri** filtreleme `cpu-cluster` . 

Ayrıca, ölçümün farklı segmentlerinin birbirleriyle nasıl karşılaştırılacağını görselleştirmek için bir ölçümü boyuta göre ayırabilirsiniz. Örneğin, ardışık düzen **adım türünü** bölerek işlem hattında kullanılan adımların türlerin sayısını görebilirsiniz.

Filtreleme ve bölme hakkında daha fazla bilgi için bkz. [Azure izleyici 'Nin gelişmiş özellikleri](../azure-monitor/essentials/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Günlükler çözümleniyor

Azure Izleyici Log Analytics kullanmak için bir tanılama yapılandırması oluşturmanız ve __Log Analytics bilgi gönderilmesini__ etkinleştirmeniz gerekir. Daha fazla bilgi için [koleksiyon ve yönlendirme](#collection-and-routing) bölümüne bakın.

Azure Izleyici günlüklerindeki veriler, her tablo kendi benzersiz özelliklerine sahip olan tablolarda depolanır. Azure Machine Learning, verileri aşağıdaki tablolarda depolar:

| Tablo | Açıklama |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning işlem kümelerinin olayları. |
| AmlComputeClusterNodeEvent | Bir Azure Machine Learning işlem kümesi içindeki düğümlerden olaylar. |
| AmlComputeJobEvent | Azure Machine Learning işlem sırasında çalışan işlerin olayları. |

> [!IMPORTANT]
> Azure Machine Learning menüsünden **Günlükler** ' i seçtiğinizde, Log Analytics sorgu kapsamı geçerli çalışma alanına ayarlanmış olarak açılır. Bu, günlük sorgularının yalnızca bu kaynaktaki verileri dahil olacağı anlamına gelir. Diğer veritabanlarından veya diğer Azure hizmetlerinden verileri içeren bir sorgu çalıştırmak istiyorsanız, **Azure izleyici** menüsünden **Günlükler** ' i seçin. Ayrıntılar için bkz. [Azure izleyici 'de günlük sorgusu kapsamı ve zaman aralığı Log Analytics](../azure-monitor/logs/scope.md) .

Günlükler ve ölçümler hakkında ayrıntılı bir başvuru için bkz. [Azure Machine Learning izleme verileri başvurusu](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Örnek kusto sorguları

> [!IMPORTANT]
> [Hizmet-adı] menüsünden **Günlükler** ' i seçtiğinizde Log Analytics, sorgu kapsamı geçerli Azure Machine Learning çalışma alanına ayarlanmış olarak açılır. Bu, günlük sorgularının yalnızca bu kaynaktaki verileri dahil olacağı anlamına gelir. Diğer çalışma alanlarından veya diğer Azure hizmetlerinden verileri içeren bir sorgu çalıştırmak istiyorsanız, **Azure izleyici** menüsünden **Günlükler** ' i seçin. Ayrıntılar için bkz. [Azure izleyici 'de günlük sorgusu kapsamı ve zaman aralığı Log Analytics](../azure-monitor/logs/scope.md) .

Aşağıda, Azure Machine Learning kaynaklarınızı izlemenize yardımcı olması için kullanabileceğiniz sorgular verilmiştir: 

+ Son beş gündeki başarısız olan işleri al:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Belirli bir iş adı için kayıtları al:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ VM boyutunun Standard_D1_V2 olduğu kümeler için son beş günde küme olayları alın:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Son sekiz günde ayrılan düğümleri al:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Uyarılar

**Azure izleyici** menüsünden **uyarıları** açarak Azure Machine Learning uyarılara erişebilirsiniz. Uyarı oluşturma hakkında ayrıntılı bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-metric.md) .

Aşağıdaki tabloda Azure Machine Learning için ortak ve önerilen ölçüm uyarısı kuralları listelenmektedir:

| Uyarı türü | Koşul | Description |
|:---|:---|:---|
| Model Dağıtımı başarısız oldu | Toplama türü: Toplam, Işleç: büyüktür, eşik değeri: 0 | Bir veya daha fazla model dağıtımı başarısız olduğunda |
| Kota kullanım yüzdesi | Toplama türü: Average, Işleç: büyüktür, eşik değeri: 90| Kota kullanım yüzdesi %90 ' den büyük olduğunda |
| Kullanılamayan düğümler | Toplama türü: Toplam, Işleç: büyüktür, eşik değeri: 0 | Bir veya daha fazla kullanılamayan düğüm olduğunda |

## <a name="next-steps"></a>Sonraki adımlar

- Günlüklerin ve ölçümlerin bir başvurusu için bkz. [izleme Azure Machine Learning veri başvurusu](monitor-resource-reference.md).
- Azure Machine Learning ilgili kotalarla çalışma hakkında daha fazla bilgi için bkz. [Azure kaynakları için kotaları yönetme ve isteme](how-to-manage-quotas.md).
- Azure kaynaklarını izleme hakkında daha fazla bilgi için bkz. Azure [izleyici Ile Azure kaynaklarını izleme](../azure-monitor/essentials/monitor-azure-resource.md).
