---
title: Azure Işlevlerinde tüketim planı maliyetlerini tahmin etme
description: İşlev uygulamanızı Azure 'da bir tüketim planında çalıştırırken tabi olabilecek maliyetleri daha iyi tahmin etme hakkında bilgi edinin.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 648be6325cce5bad36795b113c8bbccb3e21d37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774012"
---
# <a name="estimating-consumption-plan-costs"></a>Tüketim planı maliyetlerini tahmin etme

Azure Işlevleri 'nde çalışan bir uygulama için şu anda üç tür barındırma planı vardır: her plan kendi fiyatlandırma modeline sahiptir: 

| Planlama | Description |
| ---- | ----------- |
| [**Tüketim**](consumption-plan.md) | Yalnızca işlev uygulamanızın çalıştırıldığı zaman ücretlendirilirsiniz. Bu plan, abonelik bazında [ücretsiz]bir[fiyatlandırma sayfası] içerir.|
| [**Premium**](functions-premium-plan.md) | Tüketim planıyla aynı özellikleri ve ölçeklendirme mekanizmasını, ancak gelişmiş performans ve VNET erişimi sağlar. Maliyet, seçtiğiniz fiyatlandırma katmanına göre belirlenir. Daha fazla bilgi için bkz. [Azure Işlevleri Premium planı](functions-premium-plan.md). |
| [**Adanmış (App Service)**](dedicated-plan.md) <br/>(temel katman veya daha yüksek) | Adanmış VM 'lerde veya yalıtımda çalıştırmanız gerektiğinde, özel görüntüleri kullanın veya fazla App Service planı kapasitenizi kullanmak isteyebilirsiniz. [Düzenli App Service planı faturalandırmasını](https://azure.microsoft.com/pricing/details/app-service/)kullanır. Maliyet, seçtiğiniz fiyatlandırma katmanına göre belirlenir.|

İşlev performansınızı ve maliyet gereksinimlerinizi en iyi şekilde destekleyen planı seçtiniz. Daha fazla bilgi için bkz. [Azure işlevleri ölçeklendirme ve barındırma](functions-scale.md).

Bu makale, değişken maliyetleriyle sonuçlandığından yalnızca tüketim planıyla ilgilidir. Bu makale, [Tüketim planı maliyet faturalama SSS](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) makalesinin yerini alır.

Dayanıklı İşlevler, bir tüketim planında da çalıştırılabilir. Dayanıklı İşlevler kullanırken maliyet konuları hakkında daha fazla bilgi için, bkz. [dayanıklı işlevler faturalandırma](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Tüketim planı maliyetleri

Tek bir işlev yürütmenin yürütme *ücreti* *GB saniye* cinsinden ölçülür. Yürütme maliyeti, bellek kullanımını yürütme süresi ile birleştirerek hesaplanır. Daha fazla bellek tüketen bir işlev olduğu gibi daha uzun maliyetler için çalışan bir işlev. 

İşlev tarafından kullanılan bellek miktarının sabit kaldığından bir durum düşünün. Bu durumda, maliyeti hesaplamak basit çarpma olur. Örneğin, işlevinizin 3 saniye boyunca 0,5 GB tükettiğini varsayalım. Ardından yürütme maliyeti olur `0.5GB * 3s = 1.5 GB-seconds` . 

Bellek kullanımı zaman içinde değiştiği için, hesaplama temelde bellek kullanımının zaman içindeki integrali olur.  Sistem, düzenli aralıklarla işlemin bellek kullanımını (alt işlemlerle birlikte) örnekleyerek bu hesaplamayı yapar. [Fiyatlandırma sayfasında]belirtildiği gibi, bellek kullanımı en yakın 128 MB 'lık sepete yuvarlanır. İşleminiz 160 MB kullanıyorsa, 256 MB üzerinden ücretlendirilirsiniz. Hesaplama, aynı işlemde birden çok eş zamanlı işlev yürütmesi olan eşzamanlılık hesabı ' na geçer.

> [!NOTE]
> CPU kullanımı doğrudan yürütme maliyetinde düşünülmediğinde, işlevin yürütme süresini etkilediğinde maliyeti etkileyebilir.

HTTP ile tetiklenen bir işlev için, işlev kodunuz yürütülmeye başlamadan önce bir hata oluştuğunda bir yürütme ücretsizdir. Bu, API anahtar doğrulaması veya App Service kimlik doğrulama/yetkilendirme özelliği yürütme maliyetinizden bu özelliğin sayılamamasından dolayı platformdan 401 yanıt olduğunu gösterir. Benzer şekilde, isteği işleyen bir işlevden önce platformda meydana geldiğinde 5xx durum kodu yanıtları sayılmaz. İşlev kodunuz yürütülmeye başladıktan sonra platform tarafından oluşturulan 5xx yanıtı, hata işlev kodunuz tarafından çıkarılmasa bile yürütme olarak sayılır.

## <a name="other-related-costs"></a>Diğer ilgili maliyetler

İşlevlerinizi herhangi bir planda çalıştırmanın genel maliyetini tahmin edildiğinde, Işlevler çalışma zamanının her biri ayrı olarak faturalandırılan çeşitli Azure hizmetlerini kullandığını unutmayın. İşlev uygulamaları için fiyatlandırma hesaplanırken, diğer Azure hizmetleriyle tümleşen tüm tetikleyiciler ve bağlamalar, bu ek hizmetler için oluşturmanız ve ödemeniz gerekir. 

Tüketim planında çalışan işlevler için toplam maliyet, işlevlerinizin yürütme maliyetleridir ve bant genişliği ile ek hizmetler maliyetlidir. 

İşlev uygulamanızın ve ilgili hizmetlerin genel maliyetlerinin tahmin edildiğinde [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=functions)' ı kullanın. 

| İlgili maliyet | Açıklama |
| ------------ | ----------- |
| **Depolama hesabı** | Her işlev uygulaması, [ayrı olarak faturalandırılan](https://azure.microsoft.com/pricing/details/storage/)ilişkili bir genel amaçlı [Azure depolama hesabına](../storage/common/storage-introduction.md#types-of-storage-accounts)sahip olmanızı gerektirir. Bu hesap, Işlevler çalışma zamanı tarafından dahili olarak kullanılır, ancak bunu depolama Tetikleyicileri ve bağlamaları için de kullanabilirsiniz. Depolama hesabınız yoksa, işlev uygulaması oluşturulduğunda sizin için bir tane oluşturulur. Daha fazla bilgi için bkz. [depolama hesabı gereksinimleri](storage-considerations.md#storage-account-requirements).|
| **Uygulama Bilgileri** | İşlevler, işlev uygulamalarınız için yüksek performanslı bir izleme deneyimi sağlamak üzere [Application Insights](../azure-monitor/app/app-insights-overview.md) bağımlıdır. Gerekli olmasa da [Application Insights tümleştirmeyi etkinleştirmelisiniz](configure-monitoring.md#enable-application-insights-integration). Her ay Telemetri verilerinin ücretsiz olarak verilmesi dahildir. Daha fazla bilgi edinmek için bkz. [Azure Monitor fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/monitor/). |
| **Ağ bant genişliği** | Aynı bölgedeki Azure hizmetleri arasında veri aktarımı için ödeme yapmazsınız. Ancak, giden veri aktarımları için başka bir bölgeye veya Azure dışında maliyetlerine tabi olabilirsiniz. Daha fazla bilgi için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Yürütme süresini etkileyen davranışlar

İşlevlerinizin aşağıdaki davranışları yürütme süresini etkileyebilir:

+ **Tetikleyiciler ve bağlamalar**: [işlev Bağlamalarınızın](functions-triggers-bindings.md) giriş ve yazma çıktısından alınması için geçen süre, yürütme süresi olarak sayılır. Örneğin, işleviniz bir Azure depolama kuyruğuna ileti yazmak için bir çıkış bağlaması kullandığında, yürütme süresi, ileti maliyeti hesaplamasına dahil edilen iletiyi sıraya yazmak için geçen süreyi içerir. 

+ **Zaman uyumsuz yürütme**: işlevinizin zaman uyumsuz bir isteğin sonuçları için beklediği zaman ( `await` C# ' de) yürütme süresi olarak sayılır. GB saniyelik hesaplama, işlevin başlangıç ve bitiş zamanına ve bu döneme ait bellek kullanımına göre belirlenir. CPU etkinliği açısından bu sürenin üzerinde neler olduğu, hesaplamaya hiçbir şekilde bölünmez. [Dayanıklı işlevler](durable/durable-functions-overview.md)kullanarak, zaman uyumsuz işlemler sırasında maliyetleri azaltabilirsiniz. Orchestrator işlevlerinde beklemeleri sırasında harcanan zaman için faturalandırılırsınız.

## <a name="viewing-cost-related-data"></a>Maliyetle ilgili verileri görüntüleme

[Faturanızda](../cost-management-billing/understand/download-azure-invoice.md), **Toplam yürütme-Işlev** ve **yürütme süresi-işlevlerinin** maliyet ile ilgili verilerini, gerçek fatura maliyetleriyle birlikte görüntüleyebilirsiniz. Ancak, bu fatura verileri geçmiş bir fatura dönemi için aylık bir topladır. 

### <a name="function-app-level-metrics"></a>İşlev uygulama düzeyi ölçümleri

İşlevlerinizin maliyet etkisini daha iyi anlamak için, Azure Izleyici 'yi kullanarak işlev uygulamalarınız tarafından şu anda oluşturulan maliyetlerle ilgili ölçümleri görüntüleyebilirsiniz. Bu verileri almak için [Azure Portal] veya REST API 'Lerinde [Azure izleyici ölçüm Gezgini](../azure-monitor/essentials/metrics-getting-started.md) 'ni kullanabilirsiniz.

#### <a name="monitor-metrics-explorer"></a>Ölçüm Gezginini izleme

Tüketim planı işlev uygulamalarınızın maliyet ile ilgili verilerini grafik biçiminde görüntülemek için [Azure izleyici ölçümleri Gezginini](../azure-monitor/essentials/metrics-getting-started.md) kullanın. 

1. **Arama Hizmetleri, kaynaklar ve docs** 'daki [Azure Portal] üst kısmında, `monitor` **Hizmetler**' in altında **izleme** ' yi arayın ve seçin.

1. Sol tarafta **ölçümler**' i seçin  >  ve ardından işlev uygulamanızı seçmek için görüntünün altındaki ayarları kullanın.

    ![İşlev uygulaması kaynağını seçin](media/functions-consumption-costing/select-a-resource.png)

      
    |Ayar  |Önerilen değer  |Açıklama  |
    |---------|---------|---------|
    | Abonelik    |  Aboneliğiniz  | İşlev uygulamanıza sahip abonelik.  |
    | Kaynak grubu     | Kaynak grubunuz  | İşlev uygulamanızı içeren kaynak grubu.   |
    | Kaynak türü     |  Uygulama Hizmetleri | İşlev uygulamaları, Monitor 'daki App Services örnekleri olarak gösterilir. |
    | Kaynak     |  İşlev uygulamanız  | İzlenecek işlev uygulaması.        |

1. İşlev uygulamanızı izlenecek kaynak olarak seçmek için **Uygula** ' yı seçin.

1. **Ölçüm**'Den, **toplama** için **işlev yürütme sayısı** ve **toplamı** ' nı seçin. Bu, seçilen dönemdeki yürütme sayılarının toplamını grafiğe ekler.

    ![Grafiğe eklemek için bir işlevler uygulama ölçümü tanımlayın](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Grafiğe **Işlev yürütme birimleri** eklemek Için **ölçüm Ekle** ve 2-4 adımlarını tekrarlayın ' i seçin. 

Elde edilen grafik, seçilen zaman aralığındaki her iki yürütme ölçümlerinin toplamını içerir. Bu durumda iki saattir.

![İşlev yürütme sayıları ve yürütme birimlerinin grafiği](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Yürütme birimlerinin sayısı, yürütme sayısından çok daha fazla olduğu için grafik yalnızca yürütme birimlerini gösterir.

Bu grafik `Function Execution Units` , MB cinsinden ölçülen, iki saatlik bir dönemde tüketilen toplam 1.110.000.000 sayısını gösterir. GB saniyeye dönüştürmek için 1024000 ile bölün. Bu örnekte, işlev uygulaması `1110000000 / 1024000 = 1083.98` GB-saniye tüketilebilir. Bu değeri alabilir ve [işlevler fiyatlandırma sayfası][fiyatlandırma sayfasındaki]geçerli yürütme süresi fiyatına göre çarpıp, size bu iki saatin maliyetini sağlayan, size herhangi bir ücretsiz yürütme süresi verdiğini kabul eden bir ücret verir. 

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/) , ölçümleri almak için komutlar içerir. CLı 'yi yerel bir komut ortamından veya [Azure Cloud Shell](../cloud-shell/overview.md)kullanarak doğrudan portaldan kullanabilirsiniz. Örneğin, aşağıdaki [az Monitor ölçümleri List](/cli/azure/monitor/metrics#az_monitor_metrics_list) komutu, daha önce kullanılan aynı zaman dilimine göre saatlik verileri döndürür.

`<AZURE_SUBSCRIPTON_ID>`Komutunu çalıştıran Azure ABONELIK Kimliğiniz ile değiştirdiğinizden emin olun.

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
Bu belirli yanıt `2019-09-11T21:46` `2019-09-11T23:18` , uygulamasının öğesinden, UYGULAMANıN 1110000000 MB-milisaniye (1083,98 GB-saniye) tükettiğini gösterir.

### <a name="function-level-metrics"></a>İşlev düzeyi ölçümleri

İşlev yürütme birimleri, yürütme süresi ve bellek kullanımınızın bir birleşimidir ve bu da bellek kullanımını anlamak için zor bir ölçüm sağlar. Bellek verileri şu anda Azure Izleyici aracılığıyla kullanılabilen bir ölçüm değildir. Ancak, uygulamanızın bellek kullanımını iyileştirmek isterseniz, Application Insights tarafından toplanan performans sayacı verilerini kullanabilir.  

Daha önce yapmadıysanız, [işlev uygulamanızda Application Insights etkinleştirin](configure-monitoring.md#enable-application-insights-integration). Bu tümleştirme etkinken, [portalda bu telemetri verilerini sorgulayabilirsiniz](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İşlev uygulamalarını Izleme hakkında daha fazla bilgi edinin](functions-monitoring.md)

[fiyatlandırma sayfası]:https://azure.microsoft.com/pricing/details/functions/
[Azure portalı]: https://portal.azure.com
