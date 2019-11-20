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
ms.date: 11/04/2019
ms.openlocfilehash: 0a28292ac95dca7b46d9f82c32b9881c18d74b87
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123535"
---
# <a name="monitoring-azure-machine-learning"></a>İzleme Azure Machine Learning

Bu makalede, Azure Machine Learning tarafından oluşturulan izleme verileri açıklanmaktadır. Ayrıca, verilerinizi çözümlemek ve uyarıları tanımlamak için Azure Izleyicisini nasıl kullanabileceğinizi açıklar.

> [!TIP]
> Bu belgedeki bilgiler birincil olarak yöneticiler içindir, çünkü Azure Machine Learning hizmeti için izlemeyi açıklar. Bir veri bilimcu veya geliştiricisiyseniz ve model eğitim çalışmalarınız için özel bilgileri izlemek istiyorsanız aşağıdaki belgelere bakın:
>
> * [Eğitim çalıştırmalarını başlatın, izleyin ve iptal edin](how-to-manage-runs.md)
> * [Eğitim çalıştırmaları için günlük ölçümleri](how-to-track-experiments.md)
> * [MLflow ile eexperiments izleyin](how-to-use-mlflow.md)
> * [TensorBoard ile çalıştırmaları görselleştirin](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure İzleyici

Azure 'da tam yığın izleme hizmeti olan Azure Izleyici 'yi kullanarak verileri izleme Azure Machine Learning günlüğe kaydeder. Azure Izleyici, Azure kaynaklarınızı izlemeye yönelik kapsamlı bir özellik kümesi sağlar. Ayrıca, diğer bulutlardaki ve Şirket içindeki kaynakları da izleyebilir.

İzleme özelliklerine genel bir bakış sağlayan [Azure izleyici genel bakış](/azure/azure-monitor/overview)makalesini başlatın. Aşağıdaki bölümlerde, Azure Machine Learning ile Azure Izleyici 'yi kullanarak bu bilgileri, Azure Izleyici 'yi kullanarak oluşturma hakkında bilgi sağlanır.

Azure Izleyici ile ilişkili maliyetleri anlamak için bkz. [kullanım ve tahmini maliyetler](/azure/azure-monitor/platform/usage-estimated-costs). Verilerinizin Azure Izleyici 'de görünmesi için geçen süreyi anlamak için bkz. [günlük verisi alma süresi](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure Machine Learning verileri izleme

Azure Machine Learning, [Azure kaynaklarından gelen verileri izleme](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)bölümünde açıklanan diğer Azure kaynaklarıyla aynı türde izleme verilerini toplar. Azure Machine Learning tarafından oluşturulan günlüklere ve ölçümlere ilişkin ayrıntılı bir başvuru için bkz. [Azure Machine Learning izleme verileri başvurusu](monitor-resource-reference.md) .

## <a name="analyzing-metric-data"></a>Ölçüm verileri çözümleniyor

**Azure izleyici** menüsünden **ölçümler** ' i açarak Azure Machine Learning ölçümlerini çözümleyebilirsiniz. Bu aracı kullanma hakkında ayrıntılı bilgi için bkz. [Azure Ölçüm Gezgini](/azure/azure-monitor/platform/metrics-getting-started) kullanmaya başlama.

Azure Machine Learning tüm ölçümler, **Machine Learning hizmet çalışma**alanında yer alan ad alanıdır.

![Machine Learning hizmeti çalışma alanı seçili Ölçüm Gezgini](media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtreleme ve bölme

Boyutları destekleyen ölçümler için bir boyut değeri kullanarak filtre uygulayabilirsiniz. Örneğin, `cpu-cluster`bir **küme adı** Için **etkin çekirdekleri** filtreleme. 

Ayrıca, ölçümün farklı segmentlerinin birbirleriyle nasıl karşılaştırılacağını görselleştirmek için bir ölçümü boyuta göre ayırabilirsiniz. Örneğin, ardışık düzen **adım türünü** bölerek işlem hattında kullanılan adımların türlerin sayısını görebilirsiniz.

Filtreleme ve bölme hakkında daha fazla bilgi için bkz. [Azure izleyici 'Nin gelişmiş özellikleri](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Uyarılar

**Azure izleyici** menüsünden **uyarıları** açarak Azure Machine Learning uyarılara erişebilirsiniz. Uyarı oluşturma hakkında ayrıntılı bilgi için bkz. [Azure izleyici kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](/azure/azure-monitor/platform/alerts-metric) .

Aşağıdaki tabloda Azure Machine Learning için ortak ve önerilen ölçüm uyarısı kuralları listelenmektedir:

| Uyarı türü | Koşul | Açıklama |
|:---|:---|:---|
| Model Dağıtımı başarısız oldu | Toplama türü: Toplam, Işleç: büyüktür, eşik değeri: 0 | Bir veya daha fazla model dağıtımı başarısız olduğunda |
| Kota kullanım yüzdesi | Toplama türü: Average, Işleç: büyüktür, eşik değeri: 90| Kota kullanım yüzdesi %90 ' den büyük olduğunda |
| Kullanılamayan düğümler | Toplama türü: Toplam, Işleç: büyüktür, eşik değeri: 0 | Bir veya daha fazla kullanılamayan düğüm olduğunda |

## <a name="configuration"></a>Yapılandırma

> [!IMPORTANT]
> __Azure Machine Learning ölçümlerinin yapılandırılması gerekmez__, bunlar otomatik olarak toplanır ve izleme ve uyarı için ölçüm Gezgini kullanılabilir.

Aşağıdaki işlevleri yapılandırmak için bir tanılama ayarı ekleyebilirsiniz:

* Günlük ve ölçüm bilgilerini bir Azure depolama hesabına arşivleme.
* Günlük ve ölçüm bilgilerini bir Azure Olay Hub 'ına akış.
* Günlük ve ölçüm bilgilerini Azure Izleyici Log Analytics gönderin.

Bu ayarları etkinleştirmek, ek Azure Hizmetleri (depolama hesabı, Olay Hub 'ı veya Log Analytics) gerektirir ve bu da maliyetinizi artırabilir. Tahmini maliyeti hesaplamak için [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator)' ı ziyaret edin.

Tanılama ayarı oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da platform günlüklerini ve ölçümlerini toplamak için tanılama ayarı oluşturma](/azure/azure-monitor/platform/diagnostic-settings).

Azure Machine Learning için aşağıdaki günlükleri yapılandırabilirsiniz:

| Kategori | Açıklama |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning işlem kümelerinin olayları. |
| AmlComputeClusterNodeEvent | Bir Azure Machine Learning işlem kümesi içindeki düğümlerden olaylar. |
| AmlComputeJobEvent | Azure Machine Learning işlem sırasında çalışan işlerin olayları. |

> [!NOTE]
> Bir tanılama ayarında ölçümleri etkinleştirdiğinizde, boyut bilgisi şu anda bir depolama hesabına, Olay Hub 'ına veya Log Analytics 'e gönderilen bilgilerin bir parçası olarak dahil edilmez.

## <a name="analyzing-log-data"></a>Günlük verileri çözümleniyor

Azure Izleyici Log Analytics kullanmak için bir tanılama yapılandırması oluşturmanız ve __Log Analytics bilgi gönderilmesini__etkinleştirmeniz gerekir. Daha fazla bilgi için [yapılandırma](#configuration) bölümüne bakın.

Azure Izleyici günlüklerindeki veriler, her tablo kendi benzersiz özelliklerine sahip olan tablolarda depolanır. Azure Machine Learning, verileri aşağıdaki tablolarda depolar:

| Tablo | Açıklama |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning işlem kümelerinin olayları. |
| AmlComputeClusterNodeEvent | Bir Azure Machine Learning işlem kümesi içindeki düğümlerden olaylar. |
| AmlComputeJobEvent | Azure Machine Learning işlem sırasında çalışan işlerin olayları. |

> [!IMPORTANT]
> Azure Machine Learning menüsünden **Günlükler** ' i seçtiğinizde, Log Analytics sorgu kapsamı geçerli çalışma alanına ayarlanmış olarak açılır. Bu, günlük sorgularının yalnızca bu kaynaktaki verileri dahil olacağı anlamına gelir. Diğer veritabanlarından veya diğer Azure hizmetlerinden verileri içeren bir sorgu çalıştırmak istiyorsanız, **Azure izleyici** menüsünden **Günlükler** ' i seçin. Ayrıntılar için bkz. [Azure izleyici 'de günlük sorgusu kapsamı ve zaman aralığı Log Analytics](/azure/azure-monitor/log-query/scope/) .

Günlükler ve ölçümler hakkında ayrıntılı bir başvuru için bkz. [Azure Machine Learning izleme verileri başvurusu](monitor-resource-reference.md).

### <a name="sample-queries"></a>Örnek sorgular

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

## <a name="next-steps"></a>Sonraki adımlar

- Günlüklerin ve ölçümlerin bir başvurusu için bkz. [Azure Machine Learning izleme verileri başvurusu](monitor-resource-reference.md).
- Azure Machine Learning ilgili kotalarla çalışma hakkında daha fazla bilgi için bkz. [Azure kaynakları için kotaları yönetme ve isteme](how-to-manage-quotas.md).
- Azure kaynaklarını izleme hakkında daha fazla bilgi için bkz. Azure [izleyici Ile Azure kaynaklarını izleme](/azure/azure-monitor/insights/monitor-azure-resource).
