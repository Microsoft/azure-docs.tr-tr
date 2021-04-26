---
title: Zaman serisi modeli-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 ' de zaman serisi modeli hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/22/2021
ms.custom: seodec18
ms.openlocfilehash: 37c24f2d785bbdd9847e0dadaa47969f5b090ef2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698102"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2 içinde zaman serisi modeli

Bu makalede zaman serisi modeli, özellikleri ve Azure Time Series Insights Gen2 ortamında kendi modellerinizi oluşturma ve güncelleştirme işlemlerinin nasıl başlatılacağı açıklanmaktadır.

> [!TIP]
>
> * Canlı bir zaman serisi modeli örneği için [contoso rüzgar grubu tanıtım](https://insights.timeseries.azure.com/preview/samples) ortamına gidin.
> * Azure Time Series Insights gezginini kullanarak [zaman serisi modeliyle nasıl çalışacağınızı](./how-to-edit-your-model.md) öğrenin.

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

[![Zaman serisi modeli akıllı oven grafik örneği](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Zaman serisi modeli sorgular ve gezinmede önemli bir rol oynar, bu da karşılaştırmalar, zaman aralıklarıyla ve algılayıcı ile cihaz çeşitleri arasında çizilmesine izin vererek verileri ayırır. (**A**)
* Zaman serisi modelinde kalıcı veriler, zaman serisi sorgu hesaplamalarıyla değişken olarak korunduğu ve bunları sorgu sırasında yeniden kullandığı için veriler daha kapsamlı hale getirilir.
* Zaman serisi modeli, geliştirilmiş görselleştirme ve yönetim özellikleri için verileri düzenler ve toplar. (**B**)

### <a name="key-capabilities"></a>Temel işlevler

Zaman serisi bağlamı 'nı yönetmeyi basit ve kolay hale getirmek için amaç ile zaman serisi modeli, Azure Time Series Insights Gen2 ' de aşağıdaki özellikleri sağlar. Size yardımcı olur:

* Skaler işlevler, toplama işlemleri vb. kullanarak hesaplamalar veya formüller yazın ve yönetin.
* Gezinti, arama ve başvuruyu etkinleştirmek için üst-alt ilişkilerini tanımlayın.
* *Örnek alanları* olarak tanımlanan örneklerle ilişkili özellikleri tanımlayın ve bunları hiyerarşi oluşturmak için kullanın.

### <a name="components"></a>Bileşenler

Zaman serisi modelinin üç çekirdek bileşeni vardır:

* [Zaman serisi model örnekleri](#time-series-model-instances)
* [Zaman serisi model hiyerarşileri](#time-series-model-hierarchies)
* [Zaman serisi model türleri](#time-series-model-types)

Bu bileşenler, zaman serisi modeli belirtmek ve verilerinizi düzenlemek için birleştirilir.

[![Zaman serisi modeline genel bakış grafiği](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Bir zaman serisi modeli [Azure Time Series Insights Gezgini](./concepts-ux-panels.md)aracılığıyla oluşturulup yönetilebilir. Zaman serisi modeli ayarları, [model ayarları API 'si](/rest/api/time-series-insights/reference-model-apis)aracılığıyla yönetilebilir.

## <a name="time-series-model-instances"></a>Zaman serisi model örnekleri

Zaman serisi model *örnekleri* , zaman serisinin kendi sanal temsilleridir.

Çoğu durumda, örnekler otomatik olarak **DeviceID** veya **AssetID** tarafından tanımlanır ve bu, zaman serisi kimlikleri olarak kaydedilir.

Örneklere, zaman serisi KIMLIĞI, tür, ad, açıklama, hiyerarşiler ve örnek alanları gibi *örnek özellikleri* olarak adlandırılan açıklayıcı bilgiler vardır. En azından, örnek özellikleri hiyerarşi bilgilerini içerir.

*Örnek alanları* , hiyerarşi düzeyleri ve üretici, işleç vb. değerleri içerebilen açıklayıcı bilgilerin bir koleksiyonudur.

Azure Time Series Insights Gen2 ortamı için bir olay kaynağı yapılandırıldıktan sonra, örnekler otomatik olarak keşfedilir ve bir zaman serisi modelinde oluşturulur. Örnekler, zaman serisi modeli sorguları kullanılarak Azure Time Series Insights Gezgini aracılığıyla oluşturulabilir veya güncelleştirilir.

[Contoso rüzgar grubu tanıtımı](https://insights.timeseries.azure.com/preview/samples) çeşitli canlı örnek örnekleri sunar.

[![Zaman serisi modeli örnek örneği](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Örnek özellikleri

Örnekler **Timeseriesıd**, **TypeId**, **Name**, **Description**, **hierarchyıds** ve **ınstancefields** tarafından tanımlanır. Her örnek yalnızca bir *türe* ve bir veya daha fazla *hiyerarşilere* eşlenir.

| Özellik | Açıklama |
| --- | ---|
| Timeseriesıd | Örneğin ilişkilendirildiği zaman serisinin benzersiz KIMLIĞI. Çoğu durumda, örnekler DeviceID veya assetId gibi bir özellik tarafından benzersiz şekilde tanımlanır. Bazı durumlarda, 3 ' e kadar özellik birleştiren daha belirli bir bileşik KIMLIK kullanılabilir. |
| Türü | Örneğin ilişkilendirildiği zaman serisi modeli türünün büyük/küçük harfe duyarlı benzersiz dize KIMLIĞI. Varsayılan olarak, bulunan tüm yeni örnekler varsayılan bir türle ilişkili alır.
| name | **Name** özelliği isteğe bağlıdır ve büyük/küçük harfe duyarlıdır. **Ad** yoksa, varsayılan olarak **Timeseriesıd değerini** alır. Bir ad sağlanmışsa, **Timeseriesıd** [yine de kullanılabilir.](./concepts-ux-panels.md#4-time-series-well) |
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
> Örnek API oluşturma, okuma, güncelleştirme ve silme (CRUD) desteği için, [verileri sorgulama](./concepts-query-overview.md#time-series-model-query-tsm-q-apis) makalesini ve [örnek API Rest belgelerini](/rest/api/time-series-insights/reference-model-apis#instances-api)okuyun.

## <a name="time-series-model-hierarchies"></a>Zaman serisi model hiyerarşileri

Zaman serisi model *hiyerarşileri* , özellik adlarını ve bunların ilişkilerini belirterek örnekleri düzenler.

Verilen bir Azure Time Series Insights Gen2 ortamında birden çok hiyerarşi yapılandırabilirsiniz. Zaman serisi model örneği, tek bir hiyerarşiye veya birden çok hiyerarşiyle (çoktan çoğa ilişki) eşleyebilirsiniz.

[Contoso rüzgar grubu tanıtımı](https://insights.timeseries.azure.com/preview/samples) , standart bir örnek ve tür hiyerarşisi görüntüler.

[![Zaman serisi modeli hiyerarşisi örneği](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hiyerarşi tanımı

Hiyerarşiler hiyerarşi **kimliği**, **ad** ve **kaynak** tarafından tanımlanır.

| Özellik | Açıklama |
| ---| ---|
| kimlik | Hiyerarşinin, örneğin bir örnek tanımladığınızda kullanılan benzersiz tanımlayıcısı. |
| name | Hiyerarşi için bir ad sağlamak üzere kullanılan dize. |
| kaynak | Kullanıcıların oluşturmak istedikleri hiyerarşinin üst-alt öğe sırası olan kuruluş hiyerarşisini veya yolunu belirtir. Üst-alt özellikleri eşleme örneği alanları. |

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

* `Location` üst ve alt öğesi olan bir hiyerarşiyi tanımlar `states` `cities` . Her biri `location` birden çok içerebilir `states` , bu da birden fazla bulunabilir `cities` .
* `ManufactureDate` üst ve alt öğesi olan bir hiyerarşiyi tanımlar `year` `month` . Her biri `ManufactureDate` birden çok içerebilir `years` , bu da birden fazla bulunabilir `months` .

> [!TIP]
> Hiyerarşi API 'SI oluşturma, okuma, güncelleştirme ve silme (CRUD) desteği için, [verileri sorgulama](concepts-query-overview.md#time-series-model-query-tsm-q-apis) makalesini ve [HIYERARŞI API 'si Rest belgelerini](/rest/api/time-series-insights/reference-model-apis#hierarchies-api)okuyun.

### <a name="hierarchy-example"></a>Hiyerarşi örneği

Bu **hiyerarşinin** `building` , `floor` , ve hiyerarşisinde `room` **ınstancefieldnames** tanımının bir parçası olarak bulunduğu bir örnek düşünün:

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

**ID1** ve **ID4** zaman serisi, tam olarak tanımlanmış ve doğru sıralı *oluşturma*, *kat* ve *Oda* parametrelerine sahip olduklarından [Azure Time Series Insights Explorer](./concepts-ux-panels.md) 'ın **H1** hiyerarşisinin bir parçası olarak görüntülenir.

Bunlar, belirtilen veri hiyerarşisine uygun olmadıkları için, *üst öğe olmayan örnekler* altında sınıflandırılmaktadır.

## <a name="time-series-model-types"></a>Zaman serisi model türleri

Zaman serisi model *türleri* , hesaplamalar yapmak için değişkenler veya formüller tanımlamanıza yardımcı olur. Türler belirli bir örnekle ilişkilendirilir.

Bir tür bir veya daha fazla değişkene sahip olabilir. Örneğin, bir zaman serisi model örneği, *Ortalama sıcaklık*, *en az sıcaklık* ve *en fazla sıcaklık* değişkenleriyle oluşan *sıcaklık algılayıcısı* türünde olabilir.

[Contoso rüzgar grubu gösterimi](https://insights.timeseries.azure.com/preview/samples) , ilgili örneklerle Ilişkili çeşitli zaman serisi modeli türlerini görselleştirir.

[![Zaman serisi model türü örneği](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> API oluşturma, okuma, güncelleştirme ve silme (CRUD) türleri için, [veri sorgulama](concepts-query-overview.md#time-series-model-query-tsm-q-apis) makalesini okuyun ve [API Rest belgelerini yazın](/rest/api/time-series-insights/reference-model-apis#types-api).

### <a name="type-properties"></a>Tür özellikleri

Zaman serisi model türleri **kimlik**, **ad**, **Açıklama** ve **değişkenler** tarafından tanımlanır.

| Özellik | Açıklama |
| ---| ---|
| kimlik | Tür için büyük/küçük harfe duyarlı benzersiz dize KIMLIĞI. |
| name | Tür için bir ad sağlamak üzere kullanılan dize. |
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
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

Zaman serisi model türlerinde, olaylar üzerinde formül ve hesaplama kurallarını belirten birçok değişken olabilir. [Zaman serisi model değişkenlerinin nasıl tanımlanacağı](./concepts-variables.md) hakkında daha fazla bilgi edinin

## <a name="next-steps"></a>Sonraki adımlar

* Modeli API 'Ler aracılığıyla düzenleme hakkında daha fazla bilgi için [zaman serisi modeli](/rest/api/time-series-insights/reference-model-apis) başvuru belgelerini okuyun.

* [Zaman serisi model değişkenleriyle](./concepts-variables.md) oluşturabileceğiniz formülleri ve hesaplamaları keşfet

* Azure Time Series Insights Gen2 'de [veri sorgulama](concepts-query-overview.md) hakkında bilgi edinin