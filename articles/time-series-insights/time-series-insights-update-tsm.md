---
title: Zaman serisi modeli-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights önizlemede zaman serisi modeli hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 5c045a4b5ccda47b786d86f1c004e9da4c8d85f3
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112301"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Azure Time Series Insights önizlemede zaman serisi modeli

Bu makalede zaman serisi modeli, özellikleri ve Azure Time Series Insights önizleme ortamında kendi modellerinizi oluşturma ve güncelleştirme işlemlerinin nasıl başlatılacağı açıklanmaktadır.

> [!TIP]
>  * Canlı bir zaman serisi modeli örneği için [contoso rüzgar grubu tanıtım](https://insights.timeseries.azure.com/preview/samples) ortamına gidin.
> * Zaman serisi modeli Kullanıcı arabiriminize nasıl gidebileceğinizi öğrenmek için [Azure Time Series Insights önizleme Gezgini](time-series-insights-update-explorer.md) hakkında bilgi edinin.

## <a name="summary"></a>Özet

Geleneksel olarak, IoT cihazlarından toplanan verilerin bağlamsal bilgileri yoktur, bu da algılayıcıların hızla bulunup çözümlenmesini zorlaştırır. Zaman serisi modeli için ana mosyon, IoT veya zaman serisi verilerinin bulunmasını ve çözümlenmesini basitleştiriyor. Analiz için tüketiciye yönelik veri kümelerine hazırlanmaya yardımcı olmak üzere zaman serisi verilerinin gizleme, bakım ve zenginleştirmesini etkinleştirerek bu amaca erişir.

## <a name="scenario-contosos-new-smart-oven"></a>Senaryo: contoso 'nun yeni akıllı oven 'si

**Contoso Smart oven 'nin kurgusal senaryosunu göz önünde bulundurun.** Bu senaryoda, her contoso akıllı oven 'nin her dört üst yazıcılar ve bir oven için biri olmak üzere beş sıcaklık sensöri olduğunu varsayalım. Son olarak, her contoso sıcaklık algılayıcısı, verileri ayrı ayrı gönderdi, depolamıştır ve görselleştirilir. Contoso gereç izleme için contoso, her bir algılayıcı için bir tane olmak üzere temel grafiklere güvendi.

Contoso, ilk veri ve görselleştirme çözümüyle karşılandıysa, bazı sınırlamalar görünür hale gelmiştir:

* Müşteriler, en üst kağıdın büyük bir kısmında ne kadar hızlı bir şekilde yararlandığını öğrenmek istedi. Contoso, genel oven koşullarına ilişkin Birleşik bir cevap çözümleme ve sunma konusunda daha zorluk yaşadı.
* Contoso mühendisleri, aynı anda çalışan en üst yazıcılar 'ın verimsiz güç çekilmesine neden olduğunu doğrulamak istedi. Birbirleriyle ilgili sıcaklık ve voltaj sensörleri ve bunların depoda nasıl konumlandırması arasında çapraz başvurmaya yönelik zorluk vardı.
* Contoso kalite güvencesi ekibi, iki algılayıcı sürümü arasındaki geçmişi denetlemek ve karşılaştırmak istedi. Hangi verilerin hangi algılayıcı sürümüne ait olduğunu belirlemede zorluk vardı.

Çok sayıda akıllı oven zaman serisi modelini yapı, düzenleme ve tanımlama özelliği olmadan, her sıcaklık algılayıcısı, yalıtılmış ve daha az bilgilendirici veri noktası olarak tutulur. Bu veri noktalarının, her veri kümesi diğerlerinden bağımsız olduğundan, eyleme dönüştürülebilir içgörüler halinde açılması daha zordur.

Bu sınırlamalar, contoso 'nun yeni oven 'i ile birlikte akıllı veri toplama ve görselleştirme araçlarının önemini ortaya çıkarmaktadır:

* Veri görselleştirme, verileri uygun bir görünümle ilişkilendirip birleştirebildiğinizde yararlı olur. Sıcaklık sensörleriyle birlikte voltaj algılayıcıları gösteren bir örnek.
* Birçok varlık için çok boyutlu verilerin, karşılaştırma, yakınlaştırma ve zaman aralığı işlevselleriyle birlikte yönetilmesi zor olabilir.

Zaman serisi modeli, bu kurgusal örnekte karşılaşılan birçok senaryoya **uygun bir çözüm sağlar** :

[![zaman serisi modeli grafiğini oluşturma](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* Zaman serisi modeli sorgular ve gezinmede önemli bir rol oynar, bu da karşılaştırmalar, zaman aralıklarıyla ve algılayıcı ile cihaz çeşitleri arasında çizilmesine izin vererek verileri ayırır.
* Zaman serisi modelinde kalıcı veriler, zaman serisi sorgu hesaplamalarıyla değişken olarak korunduğu ve bunları sorgu zamanında kullandığı için veriler daha kapsamlı hale getirilir.
* Zaman serisi modeli, geliştirilmiş görselleştirme ve yönetim özellikleri için verileri düzenler ve toplar.

### <a name="key-capabilities"></a>Temel işlevler

Zaman serisi bağlamı 'nı yönetmeyi basit ve kolay hale getirmek için amaç sayesinde, zaman serisi modeli Time Series Insights önizlemede aşağıdaki özellikleri sağlar. Size yardımcı olur:

* Skaler işlevler, toplama işlemleri vb. kullanarak hesaplamalar veya formüller yazın ve yönetin.
* Gezinti, arama ve başvuruyu etkinleştirmek için üst-alt ilişkilerini tanımlayın.
* *Örnek alanları*olarak tanımlanan örneklerle ilişkili özellikleri tanımlayın ve bunları hiyerarşi oluşturmak için kullanın.

### <a name="components"></a>Bileşenler

Zaman serisi modelinin üç çekirdek bileşeni vardır:

* [Zaman serisi model örnekleri](#time-series-model-instances)
* [Zaman serisi model hiyerarşileri](#time-series-model-hierarchies)
* [Zaman serisi model türleri](#time-series-model-types)

Bu bileşenler, zaman serisi modeli belirtmek ve Azure Time Series Insights verilerinizi düzenlemek için birleştirilir.

[![zaman serisi modeline genel bakış](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

[Time Series Insights Preview](time-series-insights-update-how-to-tsm.md) arabirimi aracılığıyla bir zaman serisi modeli oluşturulup yönetebilirsiniz. Zaman serisi modeli ayarları, [model ayarları API 'si](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)aracılığıyla yönetilebilir.

## <a name="time-series-model-instances"></a>Zaman serisi model örnekleri

Zaman serisi model *örnekleri* , zaman serisinin kendi sanal temsilleridir.

Çoğu durumda, örnekler otomatik olarak **DeviceID** veya **AssetID**tarafından tanımlanır ve bu, zaman serisi kimlikleri olarak kaydedilir.

Örneklere, zaman serisi KIMLIĞI, tür, ad, açıklama, hiyerarşiler ve örnek alanları gibi *örnek özellikleri*olarak adlandırılan açıklayıcı bilgiler vardır. En azından, örnek özellikleri hiyerarşi bilgilerini içerir.

*Örnek alanları* , hiyerarşi düzeyleri ve üretici, işleç vb. değerleri içerebilen açıklayıcı bilgilerin bir koleksiyonudur.

Time Series Insights ortamı için bir olay kaynağı yapılandırıldıktan sonra, örnekler otomatik olarak keşfedilir ve bir zaman serisi modelinde oluşturulur. Örnekler, zaman serisi modeli sorguları kullanılarak Time Series Insights Gezgini aracılığıyla oluşturulabilir veya güncelleştirilir.

[Contoso rüzgar grubu tanıtımı](https://insights.timeseries.azure.com/preview/samples) çeşitli canlı örnek örnekleri sunar.

[![zaman serisi model örnekleri](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>Örnek özellikleri

Örnekler **Timeseriesıd**, **TypeId**, **Name**, **Description**, **hierarchyıds**ve **ınstancefields**tarafından tanımlanır. Her örnek yalnızca bir *türe*ve bir veya daha fazla *hiyerarşilere*eşlenir.

| Özellik | Açıklama |
| --- | ---|
| Timeseriesıd | Örneğin ilişkilendirildiği zaman serisinin UUID 'SI. |
| Türü | Örneğin ilişkilendirildiği zaman serisi modeli türünün UUID 'SI. Varsayılan olarak, bulunan tüm yeni örnekler varsayılan bir türle ilişkili alır.
| ad | **Name** özelliği isteğe bağlıdır ve büyük/küçük harfe duyarlıdır. **Ad** yoksa, varsayılan olarak **Timeseriesıd değerini**alır. Bir ad sağlanmışsa, **Timeseriesıd** [yine de kullanılabilir.](time-series-insights-update-explorer.md#4-time-series-well) |
| açıklama | Örneğin metin açıklaması. |
| Hierarchyıds | Örneğin hangi hiyerarşilerin ait olduğunu tanımlar. |
| ınstancefields | Bir örneğin ve bir örneği tanımlayan herhangi bir statik verinin özellikleri. Bunlar hiyerarşi veya hiyerarşi dışı özelliklerin değerlerini tanımlar, Ayrıca, arama işlemlerini gerçekleştirmek için dizin oluşturmayı da destekler. |

> [!NOTE]
> Hiyerarşiler örnek alanları kullanılarak oluşturulur. Ek örnek özellik tanımları için ek **ınstancefields** eklenebilir.

Örnekler aşağıdaki JSON gösterimine sahiptir:

```JSON
{
    "timeSeriesId": ["PU2"],
    "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
    "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
    "description": "Pump #2",
    "instanceFields": {
        "Location": "Redmond",
        "Fleet": "Fleet 5",
        "Unit": "Pump Unit 3",
        "Manufacturer": "Contoso",
        "ScalePres": "0.54",
        "scaleTemp": "0.54"
    }
}
```

> [!TIP]
> Time Series Insights örneği API 'SI ve oluşturma, okuma, güncelleştirme ve silme (CRUD) desteği için bkz. [veri sorgulama](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) makalesi ve [örnek API Rest belgeleri](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Zaman serisi model hiyerarşileri

Zaman serisi model *hiyerarşileri* , özellik adlarını ve bunların ilişkilerini belirterek örnekleri düzenler.

Belirli bir Time Series Insights ortamında birden fazla hiyerarşiyi yapılandırabilirsiniz. Zaman serisi model örneği, tek bir hiyerarşiye veya birden çok hiyerarşiyle (çoktan çoğa ilişki) eşleyebilirsiniz.

[Contoso rüzgar grubu demo](https://insights.timeseries.azure.com/preview/samples) istemci arabirimi standart bir örnek ve tür hiyerarşisi görüntüler.

[![zaman serisi modeli hiyerarşileri](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>Hiyerarşi tanımı

Hiyerarşiler hiyerarşi **kimliği**, **ad**ve **kaynak**tarafından tanımlanır.

| Özellik | Açıklama |
| ---| ---|
| id | Hiyerarşinin, örneğin bir örnek tanımladığınızda kullanılan benzersiz tanımlayıcısı. |
| ad | Hiyerarşi için bir ad sağlamak üzere kullanılan dize. |
| source | Kullanıcıların oluşturmak istedikleri hiyerarşinin üst-alt öğe sırası olan kuruluş hiyerarşisini veya yolunu belirtir. Üst-alt özellikleri eşleme örneği alanları. |

Hiyerarşiler JSON içinde şu şekilde temsil edilir:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

Önceki JSON örneğinde:

* `Location`, üst `states` ve alt `cities`sahip bir hiyerarşiyi tanımlar. Her `location` birden çok `states`olabilir ve bu da birden çok `cities`sahip olabilir.
* `ManufactureDate`, üst `year` ve alt `month`sahip bir hiyerarşiyi tanımlar. Her `ManufactureDate` birden çok `years`olabilir ve bu da birden çok `months`sahip olabilir.

> [!TIP]
> Time Series Insights örneği API 'SI ve CRUD desteği için, [veri sorgulama](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) makalesi ve [HIYERARŞI API 'si Rest belgeleri](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)bölümüne bakın.

### <a name="hierarchy-example"></a>Hiyerarşi örneği

`building`hiyerarşisinde **ınstancefieldnames** tanımının bir parçası olarak, `floor`ve `room` **sahip olduğu bir** örneği göz önünde bulundurun:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Önceki tanımda ve birkaç zaman serisinde kullanılan örnek alanları verildiğinde, hiyerarşi öznitelikleri ve değerler aşağıdaki tabloda gösterildiği gibi görünür:

| Zaman serisi KIMLIĞI | Örnek alanları |
| --- | --- |
| ID1 | "derleme" = "1000", "Floor" = "10", "Oda" = "55"  |
| ID2 | "derleme" = "1000", "Oda" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "derleniyor" = "1000", "Floor" = "10"  |
| ID5 | Hiçbir "derleme", "kat" veya "Oda" ayarlanmamış. |

**ID1** ve **ID4** zaman serisi, tam olarak tanımlanmış ve doğru şekilde sıralı *oluşturma*, *kat*ve *Oda* içerdiğinden, [Azure Time Series Insights Gezgini](time-series-insights-update-explorer.md) 'nde **H1** hiyerarşisinin bir parçası olarak görüntülenir parametrelere.

Bunlar, belirtilen veri hiyerarşisine uygun olmadıkları için, *üst öğe olmayan örnekler* altında sınıflandırılmaktadır.

## <a name="time-series-model-types"></a>Zaman serisi model türleri

Zaman serisi model *türleri* , hesaplamalar yapmak için değişkenler veya formüller tanımlamanıza yardımcı olur. Türler, belirli bir Time Series Insights örneğiyle ilişkilendirilir.

Bir türü veya daha fazla değişken olabilir. Örneğin, bir zaman serisi model örneği, *Ortalama sıcaklık*, *en az sıcaklık*ve *en fazla sıcaklık*değişkenleriyle oluşan *sıcaklık algılayıcısı*türünde olabilir.

[Contoso rüzgar grubu gösterimi](https://insights.timeseries.azure.com/preview/samples) , ilgili örneklerle Ilişkili çeşitli zaman serisi modeli türlerini görselleştirir.

[![zaman serisi model türleri](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> Time Series Insights örneği API 'SI ve CRUD desteği için bkz. [veri sorgulama](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) MAKALESI ve [API Rest belge türü](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Tür özellikleri

Zaman serisi model türleri **kimlik**, **ad**, **Açıklama**ve **değişkenler**tarafından tanımlanır.

| Özellik | Açıklama |
| ---| ---|
| id | Türün UUID 'SI. |
| ad | Tür için bir ad sağlamak üzere kullanılan dize. |
| açıklama | Tür için bir dize açıklaması. |
| değişkenlerinin | Türle ilişkili değişkenleri belirtin. |

Türler aşağıdaki JSON örneğine uyar:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Değişkenler

Time Series Insights türler, olaylar üzerinde formül ve hesaplama kurallarını belirten birçok değişkene sahip olabilir.

Her değişken üç *türden*biri olabilir: *sayısal*, *kategorik*ve *Toplam*.

* **Sayısal** türleri sürekli değerlerle çalışır. 
* **Kategorik** türleri tanımlanmış bir dizi farklı değer kümesiyle çalışır.
* **Toplama** değerleri, tek bir türdeki birden çok değişkeni (tüm sayısal veya tüm kategorik) birleştirir.

Aşağıdaki tabloda, her değişken türü için uygun olan özellikler görüntülenmektedir.

[![zaman serisi model türleri](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Sayısal değişkenler

| Variable Özelliği | Açıklama |
| --- | ---|
| Değişken filtresi | Filtreler, hesaplama için göz önünde bulundurulmakta olan satır sayısını kısıtlamak için isteğe bağlı Koşullu yan tümcelerdir. |
| Değişken değeri | Cihaz veya sensörlerden gelen ve zaman serisi Ifadeleri kullanılarak dönüştürülen hesaplama için kullanılan telemetri değerleri. Sayısal tür değişkenleri *Double*türünde olmalıdır.|
| Değişken ilişkilendirme | Enterpolasyon, var olan verileri kullanarak bir sinyalin nasıl yeniden oluşturulacağını belirtir. *Adım* ve *Doğrusal* enterpolasyon seçenekleri sayısal değişkenler için kullanılabilir. |
| Değişken toplama | *AVG*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last* ve Time-ağırlıklı (*AVG*, *Min*, *Max*, *Sum*, *Left*) işleçleri ile hesaplamayı destekler. |

Değişkenler aşağıdaki JSON örneğine uyar:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Kategorik değişkenler

| Variable Özelliği | Açıklama |
| --- | ---|
| Değişken filtresi | Filtreler, hesaplama için göz önünde bulundurulmakta olan satır sayısını kısıtlamak için isteğe bağlı Koşullu yan tümcelerdir. |
| Değişken değeri | Cihazdan veya sensörlerden gelen hesaplama için kullanılan telemetri değerleri. Kategorik tür değişkenleri *Long* veya *String*olmalıdır. |
| Değişken ilişkilendirme | Enterpolasyon, var olan verileri kullanarak bir sinyalin nasıl yeniden oluşturulacağını belirtir. *Adım* ilişkilendirme seçeneği kategorik değişkenler için kullanılabilir. |
| Değişken kategorileri | Kategoriler cihazdan veya algılayıcılardan bir etikete gelen değerler arasında bir eşleme oluşturur. |
| Değişken varsayılan kategorisi | Varsayılan kategori, "Categories" özelliğinde eşlenmemiş tüm değerler içindir. |

Değişkenler aşağıdaki JSON örneğine uyar:

```JSON
"Status": {
  "kind": "categorical",
  "value": "toLong($event.[Status].Double)",
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Toplam değişkenleri

| Variable Özelliği | Açıklama |
| --- | ---|
| Değişken filtresi | Filtreler, hesaplama için göz önünde bulundurulmakta olan satır sayısını kısıtlamak için isteğe bağlı Koşullu yan tümcelerdir. |
| Değişken toplama | *AVG*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last*ile hesaplamayı destekler. |

Değişkenler aşağıdaki JSON örneğine uyar:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Değişkenler, zaman serisi modelinin tür tanımında depolanır ve depolanan tanımı geçersiz kılmak için [sorgu API 'leri](time-series-insights-update-tsq.md) aracılığıyla satır içi olarak sağlanır.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Azure Time Series Insights Preview Storage and ınress](./time-series-insights-update-storage-ingress.md).
- [Azure Time Series Insights önizlemede veri modellemesinde](./time-series-insights-update-how-to-tsm.md) yaygın zaman serisi modeli işlemleri hakkında bilgi edinin
- Yeni [zaman serisi modeli](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) başvuru belgelerini okuyun.
