---
title: Azure Data Factory 'de atlayan pencere tetikleme bağımlılıkları oluşturma
description: Azure Data Factory içindeki bir atlayan pencere tetikleyicisi üzerinde bağımlılık oluşturmayı öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 3780a50429b95c402459866b7e3c87e8a91207d3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683696"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Atlayan pencere tetikleyici bağımlılığı oluşturma

Bu makalede, atlayan pencere tetikleyicisinde bir bağımlılık oluşturma adımları sağlanmaktadır. Atlayan pencere Tetikleyicileri hakkında genel bilgi için bkz. [dönüştürme penceresi oluşturma tetikleyicisi](how-to-create-tumbling-window-trigger.md).

Bir bağımlılık zinciri derlemek ve bir tetikleyicinin yalnızca veri fabrikasında başka bir tetikleyicinin başarıyla yürütülmesinden sonra yürütüldüğü emin olmak için bu gelişmiş özelliği kullanarak bir pencere bağımlılığı oluşturun.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Data Factory Kullanıcı arabiriminde bağımlılık oluşturma

Bir tetikleyicide bağımlılık oluşturmak için **tetikle > gelişmiş > yeni**' yi seçin ve ardından uygun uzaklığa ve boyuta göre kullanılacak tetikleyiciyi seçin. **Son** ' u seçin ve bağımlılıkların etkili olması için veri fabrikası değişikliklerini yayımlayın.

![Bağımlılık oluşturma](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Bağımlılık oluşturma")

## <a name="tumbling-window-dependency-properties"></a>Atlayan pencere bağımlılığı özellikleri

Bağımlılık ile atlayan pencere tetikleyicisi aşağıdaki özelliklere sahiptir:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Aşağıdaki tabloda, bir atlayan pencere bağımlılığı tanımlamak için gereken özniteliklerin listesi verilmiştir.

| **Özellik adı** | **Açıklama**  | **Tür** | **Gerekli** |
|---|---|---|---|
| type  | Varolan tüm pencere Tetikleyicileri bu açılan pencerede görüntülenir. Bağımlılık yapılacak tetikleyiciyi seçin.  | TumblingWindowTriggerDependencyReference veya SelfDependencyTumblingWindowTriggerReference | Evet |
| konumu | Bağımlılık tetikleyicisinin boşluğu. Zaman aralığı biçiminde bir değer sağlayın ve hem negatif hem de pozitif uzaklıklara izin verilir. Tetikleyici kendisine bağlı ise ve tüm diğer durumlarda isteğe bağlı ise bu özellik zorunludur. Kendinden bağımlılık her zaman negatif bir konum olmalıdır. Değer belirtilmemişse pencere, tetikleyiciyle aynı olur. | Timespan<br/>(SS: DD: SS) | Kendinden bağımlılık: Evet<br/>Diğer: Hayır |
| boyutla | Bağımlılık penceresinin boyutu. Pozitif bir TimeSpan değeri belirtin. Bu özellik isteğe bağlıdır. | Timespan<br/>(SS: DD: SS) | Hayır  |

> [!NOTE]
> Atlayan bir pencere tetikleyicisi, en fazla iki tetikleyicisine bağlı olabilir.

## <a name="tumbling-window-self-dependency-properties"></a>Atlayan pencere iç bağımlılık özellikleri

Önceki pencere başarıyla tamamlanana kadar tetikleyicinin bir sonraki pencereye geçebilmesi gereken senaryolarda, kendinden bağımlılık oluşturun. Önceki HR içinde kendisinin önceki çalışmalarının başarısına bağımlı olan bir kendinden bağımlılık tetikleyicisi aşağıdaki özelliklere sahip olacaktır:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Kullanım senaryoları ve örnekler

Aşağıda Senaryo ve pencere bağımlılığı özelliklerinin kullanımı yer verilmiştir.

### <a name="dependency-offset"></a>Bağımlılık boşluğu

![Fark örneği](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Fark örneği")

### <a name="dependency-size"></a>Bağımlılık boyutu

![Boyut örneği](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Boyut örneği")

### <a name="self-dependency"></a>Kendi kendine bağımlılık

![Kendi kendine bağımlılık](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Kendi kendine bağımlılık")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Başka bir atlayan pencere tetikleyicisine bağımlılık

Son yedi gün çıktısını toplayarak günlük telemetri işleme işi ve yedi günlük bir pencere akışı üretir:

![Bağımlılık örneği](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Bağımlılık örneği")

### <a name="dependency-on-itself"></a>Üzerinde bağımlılık

İşin çıkış akışlarında boşluk olmayan günlük bir iş:

![Kendi kendine bağımlılık örneği](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Kendi kendine bağımlılık örneği")

## <a name="monitor-dependencies"></a>Bağımlılıkları izle

Bağımlılık zincirini ve ilgili pencereleri tetikleyici çalışma izleme sayfasından izleyebilirsiniz. **İzleme > tetikleme çalıştırmaları**' na gidin. Eylemler sütununun altında Tetikleyiciyi yeniden çalıştırabilir veya bağımlılıklarını görüntüleyebilirsiniz.

![Tetikleyici çalıştırmalarını izleme](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Tetikleme çalıştırmalarını izleme")

' Tetikleyici bağımlılıklarını görüntüle ' seçeneğine tıklarsanız, bağımlılıkların durumunu görebilirsiniz. Bağımlılık tetiklerinin biri başarısız olursa, bağımlı tetikleyicinin çalışması için onu başarıyla yeniden çalıştırmanız gerekir. Bir atlayan pencere tetikleyicisi, zaman aşımından önce yedi gün boyunca bağımlılıklarda bekler.

![Bağımlılıkları izle](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Bağımlılıkları izle")

Tetikleme bağımlılığı zamanlamasını görüntülemek için daha fazla görselde, Gantt görünümünü seçin.

![Bağımlılıkları izle](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Bağımlılıkları izle")

## <a name="next-steps"></a>Sonraki adımlar

* Atlayan [pencere tetikleyicisi oluşturmayı](how-to-create-tumbling-window-trigger.md) inceleyin
