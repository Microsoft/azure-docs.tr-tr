---
title: JSON Azure Time Series Insights sorguları şekillendirmeye yönelik en iyi uygulamalar | Microsoft Docs
description: JSON 'yi şekillendirerek Azure Time Series Insights sorgu verimliliğini geliştirmeyi öğrenin.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 386d10c8e4bd7d5f46d2081d5a26371fb37ff30f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006995"
---
# <a name="shape-json-to-maximize-query-performance"></a>Sorgu performansını en üst düzeye çıkarmak için JSON şekli 

Bu makalede, Azure Time Series Insights sorgularınızın verimliliğini en üst düzeye çıkarmak için JSON nasıl şekillendirilebileceğine ilişkin yönergeler sunulmaktadır.

## <a name="video"></a>Video

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>JSON 'ı depolama ihtiyaçlarınızı karşılayacak şekilde şekillendirmeye yönelik en iyi yöntemleri öğrenin.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>En iyi uygulamalar

Time Series Insights için nasıl olay gönderileceğini düşünün. Yani, her zaman şunları yapabilirsiniz:

1. verileri ağ üzerinden mümkün olduğunca verimli bir şekilde gönderin.
1. Senaryonuza uygun toplamaları gerçekleştirebilmeniz için verilerinizin bir şekilde depolandığından emin olun.
1. En fazla Time Series Insights Özellik sınırlarına ulaşamadığınızdan emin olun:
   - S1 ortamları için 600 özellikleri (sütun).
   - S2 ortamları için 800 Özellikleri (sütunlar).

> [!TIP]
> Azure Time Series Insights önizlemede [limitleri ve planlamayı](time-series-insights-update-plan.md) gözden geçirin.

Aşağıdaki kılavuz mümkün olan en iyi sorgu performansını sağlamanıza yardımcı olur:

1. Bir etiket KIMLIĞI gibi dinamik özellikleri özellik adı olarak kullanmayın. Bu kullanım, en fazla özellik sınırına ulaşmak için katkıda bulunur.
1. Gereksiz özellikleri göndermeyin. Sorgu özelliği gerekmiyorsa, bunu göndermemek en iyisidir. Bu şekilde depolama sınırlamalarından kaçınabilirsiniz.
1. Ağ üzerinden statik veri gönderilmesini önlemek için [başvuru verilerini](time-series-insights-add-reference-data-set.md) kullanın.
1. Ağ üzerinden verileri daha verimli bir şekilde göndermek için birden çok olay arasında boyut özelliklerini paylaşabilirsiniz.
1. Kapsamlı bir dizi iç içe kullanmayın. Time Series Insights, nesneleri içeren iç içe geçmiş dizilerin en fazla iki düzeyini destekler. Time Series Insights, iletilerdeki dizileri Özellik değeri çiftleriyle birden çok olaya düzleştirir.
1. Yalnızca birkaç ölçüleri tüm veya çoğu olaylar için mevcut, bu ölçümleri ayrı Özellikler içinde aynı nesne olarak göndermek daha iyidir. Bunları ayrı olarak göndermek olayların sayısını azaltır ve daha az olay işlenmesi gerektiğinden sorgu performansını iyileştirebilirler. Birkaç ölçü olduğunda, tek bir özellikte değer olarak göndermek, en fazla özellik sınırına ulaşma olasılığını en aza indirir.

## <a name="example-overview"></a>Örneğe genel bakış

Aşağıdaki iki örnek, önceki önerileri vurgulamak için olayların nasıl gönderileceğini göstermektedir. Her örneği izleyerek önerilerin nasıl uygulandığını görebilirsiniz.

Örnekler, birden çok cihazın ölçüm veya sinyal gönderen bir senaryoya dayalıdır. Ölçümler veya sinyaller Flow ücreti, altyapı yağ baskısı, sıcaklık ve nem olabilir. İlk örnekte, tüm cihazlarda birkaç ölçüm vardır. İkinci örnekte birçok cihaz vardır ve her bir cihaz birçok benzersiz ölçü gönderir.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Senaryo bir: yalnızca birkaç ölçüm mevcuttur

> [!TIP]
> Her ölçüyü veya sinyali ayrı bir özellik veya sütun olarak göndermenizi öneririz.

Aşağıdaki örnekte, dış dizinin ortak boyut değerlerinin paylaşılan bir bölümünü içerdiği tek bir Azure IoT Hub iletisi vardır. Dış dizi, iletinin verimliliğini artırmak için başvuru verilerini kullanır. Başvuru verileri, her olayla değişmeyen cihaz meta verilerini içerir, ancak veri analizi için yararlı özellikler sağlar. Ortak boyut değerlerini toplu işleme ve başvuru verileri oluşturma, hat üzerinden gönderilen baytlara kaydedilir ve bu da iletiyi daha verimli hale getirir.

Azure buluta gönderildiğinde JSON 'a serileştirilmiş bir [IoT cihaz iletisi nesnesi](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.message?view=azure-dotnet) kullanarak Time Series Insights GA ortamınıza GÖNDERILEN aşağıdaki JSON yükünü göz önünde bulundurun:


```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

* Anahtar özelliği **DeviceID**olan başvuru verileri tablosu:

   | deviceId | Ileti | deviceLocation |
   | --- | --- | --- |
   | FXXX | LINE\_DATA | AV |
   | FYYY | LINE\_DATA | US |

* Time Series Insights olay tablosu, düzleştirme sonrasında:

   | deviceId | Ileti | deviceLocation | timestamp | dizi. Akış hızı ft3/sn | dizi. Petrol baskısı PSI altyapısı |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | LINE\_DATA | AV | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | LINE\_DATA | AV | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | LINE\_DATA | US | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

> [!NOTE]
> - **DeviceID** sütunu, bir Fleet içindeki çeşitli cihazlar için sütun üst bilgisi görevi görür. **DeviceID** değerinin kendi özellik adı, diğer beş sütunlu ile toplam cihazları 595 (S1 ortamları için) veya 795 (S2 ortamları için) olarak sınırlar.
> - Gereksiz özellikler engellenir (örneğin, oluşturma ve model bilgisi). Özellikler gelecekte sorgulanmayacak, bunları ortadan kaldıran daha iyi ağ ve depolama verimliliği sağlanır.
> - Başvuru verileri, ağ üzerinden aktarılan bayt sayısını azaltmak için kullanılır. **MessageID** ve **devicelocation** iki öznitelik, **DeviceID**anahtar özelliği kullanılarak birleştirilir. Bu veriler, giriş zamanında telemetri verileriyle birleştirilir ve daha sonra sorgulamak için Time Series Insights depolanır.
> - Time Series Insights tarafından desteklenen en yüksek iç içe geçme miktarı olan iki iç içe geçme katmanı kullanılır. İç içe dizi önlemek için önemlidir.
> - Ölçüler, birkaç ölçü olduğundan aynı nesne içinde ayrı özellikler olarak gönderilir. Burada, **serisi. Akış hızı psi** ve **serisi. Motor yağ basıncı ft3/s** , benzersiz sütunlardır.

## <a name="scenario-two-several-measures-exist"></a>Senaryo iki: birkaç ölçü mevcuttur

> [!TIP]
> Ölçümleri "tür", "birim" ve "değer" tanımlama grupları olarak göndermenizi öneririz.

Örnek JSON yükü:

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Anahtar özellikleri **DeviceID** ve **Series. TagId**olan başvuru verileri tablosu:

   | deviceId | Series. TagId | Ileti | deviceLocation | type | birim |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | porate oranı | LINE\_DATA | AV | Akış hızı | ft3/s |
   | FXXX | Oilbasınç | LINE\_DATA | AV | Motor yağ basıncı | psi |
   | FYYY | porate oranı | LINE\_DATA | US | Akış hızı | ft3/s |
   | FYYY | Oilbasınç | LINE\_DATA | US | Motor yağ basıncı | psi |

* Time Series Insights olay tablosu, düzleştirme sonrasında:

   | deviceId | Series. TagId | Ileti | deviceLocation | type | birim | timestamp | Series. Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | porate oranı | LINE\_DATA | AV | Akış hızı | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | Oilbasınç | LINE\_DATA | AV | Motor yağ basıncı | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | porate oranı | LINE\_DATA | AV | Akış hızı | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | Oilbasınç | LINE\_DATA | AV | Motor yağ basıncı | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | porate oranı | LINE\_DATA | US | Akış hızı | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | Oilbasınç | LINE\_DATA | US | Motor yağ basıncı | psi | 2018-01-17T01:18:00Z | 22.2 |

> [!NOTE]
> - **DeviceID** ve **Series. TagId** sütunları, bir Fleet içindeki çeşitli cihazlar ve Etiketler için sütun üst bilgileri olarak görev yapar. Her birinin kendi özniteliği olarak kullanılması, sorguyu 594 (S1 ortamları için) veya 794 (S2 ortamları için) ile diğer altı sütunlu toplam cihaz sayısını sınırlar.
> - İlk örnekte alıntı yapılan nedenden dolayı gereksiz özellikler atlanmıştı.
> - Başvuru verileri, benzersiz **MessageID** ve **devicelocation**için kullanılan **DeviceID**'ye bakarak ağ üzerinden aktarılan bayt sayısını azaltmak için kullanılır. Bileşik anahtar **serisi. TagId** , **türü** ve **birimi**benzersiz çifti için kullanılır. Bileşik anahtar, **DeviceID** ve **Series. TagId** çiftinin dört değere başvurmak için kullanılmasına izin verir: **MessageID, devicelocation, Type** ve **Unit**. Bu veriler, giriş zamanında telemetri verileriyle birleştirilir. Daha sonra sorgulamak için Time Series Insights depolanır.
> - İkinci iç içe geçme katmanı, ilk örnekte alıntı yapılan nedenle kullanılır.

### <a name="for-both-scenarios"></a>Her iki senaryo için

Çok sayıda olası değeri olan bir özellik için, her bir değer için yeni bir sütun oluşturmak yerine, tek bir sütunda ayrı değerler olarak gönderilmesi en iyisidir. Önceki iki örnekten:

  - İlk örnekte, birkaç özellik birkaç değere sahiptir, bu nedenle her birini ayrı bir özellik yapmak uygun olur.
  - İkinci örnekte ölçüler ayrı özellikler olarak belirtilmez. Bunun yerine, ortak bir seri özelliği altındaki değer veya ölçülerden oluşan bir dizidir. Yeni anahtar **TagId** gönderilir. Bu, düzleştirilmiş tablodaki New **. TagId** sütununu oluşturur. Yeni Özellikler **türü** ve **birim** , özellik sınırına ulaşılmaması için başvuru verileri kullanılarak oluşturulur.

## <a name="next-steps"></a>Sonraki adımlar

- [IoT Hub cihaz iletilerini buluta](../iot-hub/iot-hub-devguide-messages-construct.md)gönderme hakkında daha fazla bilgi edinin.

- Time Series Insights veri erişimi REST API sorgu sözdizimi hakkında daha fazla bilgi edinmek için [Azure Time Series Insights sorgu söz dizimini](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-syntax) okuyun.

- [Olayları nasıl şekillendirileyeceğinizi](./time-series-insights-send-events.md)öğrenin.
