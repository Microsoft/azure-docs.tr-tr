---
title: Azure Işlevlerinde tüketim planı maliyetlerini tahmin etme
description: İşlev uygulamanızı Azure 'da bir tüketim planında çalıştırırken tabi olabilecek maliyetleri daha iyi tahmin etme hakkında bilgi edinin.
author: ggailey777
ms.author: glenga
ms.date: 9/20/2019
ms.topic: conceptual
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 0ff41eb511ad4513fc9bf5a2ded7ef47b08d12ab
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243318"
---
# <a name="estimating-consumption-plan-costs"></a>Tüketim planı maliyetlerini tahmin etme

Azure Işlevleri 'nde çalışan bir uygulama için şu anda üç tür barındırma planı vardır: her plan kendi fiyatlandırma modeline sahiptir: 

| Planınızın | Açıklama |
| ---- | ----------- |
| [**Mine**](functions-scale.md#consumption-plan) | Yalnızca işlev uygulamanızın çalıştırıldığı zaman ücretlendirilirsiniz. Bu plan, abonelik bazında [ücretsiz]bir[fiyatlandırma sayfası] içerir.|
| [**Premium**](functions-scale.md#premium-plan) | Tüketim planıyla aynı özellikleri ve ölçeklendirme mekanizmasını, ancak gelişmiş performans ve VNET erişimi sağlar. Maliyet, seçtiğiniz fiyatlandırma katmanına göre belirlenir. Daha fazla bilgi için bkz. [Azure Işlevleri Premium planı](functions-premium-plan.md). |
| [**Adanmış (App Service)** ](functions-scale.md#app-service-plan) <br/>(temel katman veya daha yüksek) | Adanmış VM 'lerde veya yalıtımda çalıştırmanız gerektiğinde, özel görüntüleri kullanın veya fazla App Service planı kapasitenizi kullanmak isteyebilirsiniz. [Düzenli App Service planı faturalandırmasını](https://azure.microsoft.com/pricing/details/app-service/)kullanır. Maliyet, seçtiğiniz fiyatlandırma katmanına göre belirlenir.|

İşlev performansınızı ve maliyet gereksinimlerinizi en iyi şekilde destekleyen planı seçtiniz. Daha fazla bilgi için bkz. [Azure işlevleri ölçeklendirme ve barındırma](functions-scale.md).

Bu makale, değişken maliyetleriyle sonuçlandığından yalnızca tüketim planıyla ilgilidir. 

Dayanıklı İşlevler, bir tüketim planında da çalıştırılabilir. Dayanıklı İşlevler kullanırken maliyet konuları hakkında daha fazla bilgi için, bkz. [dayanıklı işlevler faturalandırma](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Tüketim planı maliyetleri

Tek bir işlev yürütmenin yürütme *ücreti* *GB saniye*cinsinden ölçülür. Yürütme maliyeti, bellek kullanımını yürütme süresi ile birleştirerek hesaplanır. Daha fazla bellek tüketen bir işlev olduğu gibi daha uzun maliyetler için çalışan bir işlev. 

İşlev tarafından kullanılan bellek miktarının sabit kaldığından bir durum düşünün. Bu durumda, maliyeti hesaplamak basit çarpma olur. Örneğin, işlevinizin 3 saniye boyunca 0,5 GB tükettiğini varsayalım. Ardından yürütme maliyeti `0.5GB * 3s = 1.5 GB-seconds` ' dır. 

Bellek kullanımı zaman içinde değiştiği için, hesaplama temelde bellek kullanımının zaman içindeki integrali olur.  Sistem, düzenli aralıklarla işlemin bellek kullanımını (alt işlemlerle birlikte) örnekleyerek bu hesaplamayı yapar. [Fiyatlandırma sayfasında]belirtildiği gibi, bellek kullanımı en yakın 128 MB 'lık sepete yuvarlanır. İşleminiz 160 MB kullanıyorsa, 256 MB üzerinden ücretlendirilirsiniz. Hesaplama, aynı işlemde birden çok eş zamanlı işlev yürütmesi olan eşzamanlılık hesabı ' na geçer.

> [!NOTE]
> CPU kullanımı doğrudan yürütme maliyetinde düşünülmediğinde, işlevin yürütme süresini etkilediğinde maliyeti etkileyebilir.

## <a name="other-related-costs"></a>Diğer ilgili maliyetler

İşlevlerinizi herhangi bir planda çalıştırmanın genel maliyetini tahmin edildiğinde, Işlevler çalışma zamanının her biri ayrı olarak faturalandırılan çeşitli Azure hizmetlerini kullandığını unutmayın. İşlev uygulamaları için fiyatlandırma hesaplanırken, diğer Azure hizmetleriyle tümleşen tüm tetikleyiciler ve bağlamalar, bu ek hizmetler için oluşturmanız ve ödemeniz gerekir. 

Tüketim planında çalışan işlevler için toplam maliyet, işlevlerinizin yürütme maliyetleridir ve bant genişliği ile ek hizmetler maliyetlidir. 

İşlev uygulamanızın ve ilgili hizmetlerin genel maliyetlerinin tahmin edildiğinde [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=functions)' ı kullanın. 

| İlgili maliyet | Açıklama |
| ------------ | ----------- |
| **Depolama hesabı** | Her işlev uygulaması, [ayrı olarak faturalandırılan](https://azure.microsoft.com/pricing/details/storage/)ilişkili bir genel amaçlı [Azure depolama hesabına](../storage/common/storage-introduction.md#types-of-storage-accounts)sahip olmanızı gerektirir. Bu hesap, Işlevler çalışma zamanı tarafından dahili olarak kullanılır, ancak bunu depolama Tetikleyicileri ve bağlamaları için de kullanabilirsiniz. Depolama hesabınız yoksa, işlev uygulaması oluşturulduğunda sizin için bir tane oluşturulur. Daha fazla bilgi için bkz. [depolama hesabı gereksinimleri](functions-scale.md#storage-account-requirements).|
| **Application Insights** | İşlevler, işlev uygulamalarınız için yüksek performanslı bir izleme deneyimi sağlamak üzere [Application Insights](../azure-monitor/app/app-insights-overview.md) bağımlıdır. Gerekli olmasa da [Application Insights tümleştirmeyi etkinleştirmelisiniz](functions-monitoring.md#enable-application-insights-integration). Her ay Telemetri verilerinin ücretsiz olarak verilmesi dahildir. Daha fazla bilgi edinmek için bkz. [Azure Monitor fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/monitor/). |
| **Ağ bant genişliği** | Aynı bölgedeki Azure hizmetleri arasında veri aktarımı için ödeme yapmazsınız. Ancak, giden veri aktarımları için başka bir bölgeye veya Azure dışında maliyetlerine tabi olabilirsiniz. Daha fazla bilgi için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Yürütme süresini etkileyen davranışlar

İşlevlerinizin aşağıdaki davranışları yürütme süresini etkileyebilir:

+ **Tetikleyiciler ve bağlamalar**: [işlev Bağlamalarınızın](functions-triggers-bindings.md) giriş ve yazma çıktısından alınması için geçen süre, yürütme süresi olarak sayılır. Örneğin, işleviniz bir Azure depolama kuyruğuna ileti yazmak için bir çıkış bağlaması kullandığında, yürütme süresi, ileti maliyeti hesaplamasına dahil edilen iletiyi sıraya yazmak için geçen süreyi içerir. 

+ **Zaman uyumsuz yürütme**: işlevinizin zaman uyumsuz bir isteğin (`await` C#) sonuçları için beklediği süre yürütme süresi olarak sayılır. GB saniyelik hesaplama, işlevin başlangıç ve bitiş zamanına ve bu döneme ait bellek kullanımına göre belirlenir. CPU etkinliği açısından bu sürenin üzerinde neler olduğu, hesaplamaya hiçbir şekilde bölünmez. [Dayanıklı işlevler](durable/durable-functions-overview.md)kullanarak, zaman uyumsuz işlemler sırasında maliyetleri azaltabilirsiniz. Orchestrator işlevlerinde beklemeleri sırasında harcanan zaman için faturalandırılırsınız.

## <a name="view-execution-data"></a>Yürütme verilerini görüntüle

[Faturanızda](/azure/billing/billing-download-azure-invoice), **Toplam yürütme-Işlev** ve **yürütme süresi-işlevlerinin**maliyet ile ilgili verilerini, gerçek fatura maliyetleriyle birlikte görüntüleyebilirsiniz. Ancak, bu fatura verileri geçmiş bir fatura dönemi için aylık bir topladır. 

İşlevlerinizin maliyet etkisini daha iyi anlamak için, Azure Izleyici 'yi kullanarak işlev uygulamalarınız tarafından şu anda oluşturulan maliyetlerle ilgili ölçümleri görüntüleyebilirsiniz. Bu verileri almak için [Azure Portal] veya REST API 'Lerinde [Azure izleyici ölçüm Gezgini](../azure-monitor/platform/metrics-getting-started.md) 'ni kullanabilirsiniz.

### <a name="monitor-metrics-explorer"></a>Ölçüm Gezginini izleme

Tüketim planı işlev uygulamalarınızın maliyet ile ilgili verilerini grafik biçiminde görüntülemek için [Azure izleyici ölçümleri Gezginini](../azure-monitor/platform/metrics-getting-started.md) kullanın. 

1. **Arama Hizmetleri, kaynaklar ve docs** 'daki [Azure Portal] en üstünde, `monitor` Için arama yapın ve **Hizmetler**altında **izleme** ' yi seçin.

1. Solda, **ölçüm**@no__t seçin-1**kaynak seçin**ve ardından işlev uygulamanızı seçmek için görüntünün altındaki ayarları kullanın.

    ![İşlev uygulaması kaynağını seçin](media/functions-consumption-costing/select-a-resource.png)

      
    |Ayar  |Önerilen değer  |Açıklama  |
    |---------|---------|---------|
    | Aboneliğiniz    |  Aboneliğiniz  | İşlev uygulamanıza sahip abonelik.  |
    | Kaynak grubu     | Kaynak grubunuz  | İşlev uygulamanızı içeren kaynak grubu.   |
    | Kaynak türü     |  Uygulama Hizmetleri | İşlev uygulamaları, Monitor 'daki App Services örnekleri olarak gösterilir. |
    | Kaynak     |  İşlev uygulamanız  | İzlenecek işlev uygulaması.        |

1. İşlev uygulamanızı izlenecek kaynak olarak seçmek için **Uygula** ' yı seçin.

1. **Ölçüm**'Den, **toplama**için **işlev yürütme sayısı** ve **toplamı** ' nı seçin. Bu, seçilen dönemdeki yürütme sayılarının toplamını grafiğe ekler.

    ![Grafiğe eklemek için bir işlevler uygulama ölçümü tanımlayın](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Grafiğe **işlev yürütme birimleri** eklemek Için **ölçüm Ekle** ve 2-4 adımlarını tekrarlayın ' i seçin. 

Elde edilen grafik, seçilen zaman aralığındaki her iki yürütme ölçümlerinin toplamını içerir. Bu durumda iki saattir.

![İşlev yürütme sayıları ve yürütme birimlerinin grafiği](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Yürütme birimlerinin sayısı, yürütme sayısından çok daha fazla olduğu için grafik yalnızca yürütme birimlerini gösterir.

Bu grafik, iki saatlik bir dönemde tüketilen toplam 1.110.000.000 `Function Execution Units` ' ı MB milisaniyelik olarak gösterir. GB saniyeye dönüştürmek için 1024000 ile bölün. Bu örnekte, işlev uygulaması @no__t ile 0 GB saniye tüketilebilir. Bu değeri alabilir ve [işlevler fiyatlandırma sayfası][fiyatlandırma sayfasında]geçerli yürütme süresi fiyatına göre çarpıp, size bu iki saatin maliyetini sağlayan, size herhangi bir ücretsiz yürütme süresi verdiğini kabul eden bir ücret verir. 

### <a name="azure-cli"></a>Azure CLı

[Azure CLI](/cli/azure/) , ölçümleri almak için komutlar içerir. CLı 'yi yerel bir komut ortamından veya [Azure Cloud Shell](../cloud-shell/overview.md)kullanarak doğrudan portaldan kullanabilirsiniz. Örneğin, aşağıdaki [az Monitor ölçümleri List](/cli/azure/monitor/metrics#az-monitor-metrics-list) komutu, daha önce kullanılan aynı zaman dilimine göre saatlik verileri döndürür.

Komutu çalıştıran Azure abonelik KIMLIĞINIZLE `<AZURE_SUBSCRIPTON_ID>` ' ın yerine geldiğinden emin olun.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Bu komut, aşağıdaki örnekte olduğu gibi görünen bir JSON yükü döndürür:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Bu belirli yanıt, `2019-09-11T21:46` ' dan `2019-09-11T23:18` ' e kadar, uygulamanın 1110000000 MB-milisaniye (1083,98 GB-saniye) tükettiğini gösterir.

## <a name="determine-memory-usage"></a>Bellek kullanımını belirleme

İşlev yürütme birimleri, yürütme süresi ve bellek kullanımınızın bir birleşimidir ve bu da bellek kullanımını anlamak için zor bir ölçüm sağlar. Bellek verileri şu anda Azure Izleyici aracılığıyla kullanılabilen bir ölçüm değildir. Ancak, uygulamanızın bellek kullanımını iyileştirmek isterseniz, Application Insights tarafından toplanan performans sayacı verilerini kullanabilir.  

Daha önce yapmadıysanız, [işlev uygulamanızda Application Insights etkinleştirin](functions-monitoring.md#enable-application-insights-integration). Bu tümleştirme etkinken, [portalda bu telemetri verilerini sorgulayabilirsiniz](functions-monitoring.md#query-telemetry-data).  

**İzleme**altında **Günlükler (Analiz)** öğesini seçin, ardından aşağıdaki telemetri sorgusunu kopyalayın ve sorgu penceresine yapıştırın ve **Çalıştır**' ı seçin. Bu sorgu, her örneklenmiş zamanda toplam bellek kullanımını döndürür.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

Sonuçlar aşağıdaki örneğe benzer şekilde görünür:

| zaman damgası \[UTC @ no__t-1          | name          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 @ no__t-0947 har | Özel baytlar | 209.932.288 |
| 9/12/2019, 1:06:14 @ no__t-0994 har | Özel baytlar | 212.189.184 |
| 9/12/2019, 1:06:30 @ no__t-0010 har | Özel baytlar | 231.714.816 |
| 9/12/2019, 1:07:15 @ no__t-0040 har | Özel baytlar | 210.591.744 |
| 9/12/2019, 1:12:16 @ no__t-0285 har | Özel baytlar | 216.285.184 |
| 9/12/2019, 1:12:31 @ no__t-0376 har | Özel baytlar | 235.806.720 |

## <a name="function-level-metrics"></a>İşlev düzeyi ölçümleri

Azure Izleyici, kaynak düzeyindeki ölçümleri izler, bu Işlevler işlev uygulamadır. Application Insights tümleştirme ölçümleri işlev başına temelinde yayar. Bir işlevin ortalama süresini almak için örnek bir analiz sorgusu aşağıda verilmiştir:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 @ no__t-0087                     |
| QueueTrigger MaxDurationMs | 90 @ no__t-0249                     |
| QueueTrigger MinDurationMs | 8 @ no__t-0522                      |

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İşlev uygulamalarını Izleme hakkında daha fazla bilgi edinin](functions-monitoring.md)

[Fiyatlandırma sayfasında]: https://azure.microsoft.com/pricing/details/functions/
[Azure portal]: https://portal.azure.com
