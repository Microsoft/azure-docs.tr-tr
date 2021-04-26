---
title: Azure Service Fabric CLı-sfctl Chaos zamanlaması
description: Azure Service Fabric komut satırı arabirimi olan sfctl hakkında bilgi edinin. Chaos zamanlaması için komutların bir listesini içerir.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: dd911574cd37c18e3043cfc2482bd1fcd5f3857b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260849"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Chaos zamanlamasını alın ve ayarlayın.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| get | Chaos 'ın ne zaman ve nasıl çalıştırılacağını tanımlayan Chaos zamanlamasını alın. |
| set | Chaos tarafından kullanılan zamanlamayı ayarlayın. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl Chaos zamanlaması al
Chaos 'ın ne zaman ve nasıl çalıştırılacağını tanımlayan Chaos zamanlamasını alın.

Kullanımdaki Chaos zamanlamasının sürümünü ve Chaos 'ın ne zaman ve nasıl çalıştırılacağını tanımlayan Chaos zamanlamasını alır.

### <a name="arguments"></a>Bağımsız değişkenler

|Bağımsız Değişken|Description|
| --- | --- |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan \: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Description|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen \: JSON, jsonc, tablo, TSV değerleri.  Varsayılan \: JSON. |
| --sorgu | JMESPath sorgu dizesi. \:Daha fazla bilgi ve örnek için bkz. http//jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl Chaos zamanlama kümesi
Chaos tarafından kullanılan zamanlamayı ayarlayın.

Chaos, Chaos zamanlaması temelinde otomatik olarak çalıştırmalar zamanlar. Belirtilen sürüm sunucudaki sürümle eşleşiyorsa, Chaos zamanlaması güncelleştirilecektir. Chaos zamanlaması güncelleştirilirken, sunucudaki sürüm 1 artırılır. Sunucu üzerindeki sürüm, büyük bir sayıya ulaştıktan sonra tekrar 0 olarak kaydırılır. Bu çağrı yapıldığında Chaos çalışıyorsa, çağrı başarısız olur.

### <a name="arguments"></a>Bağımsız değişkenler

|Bağımsız Değişken|Description|
| --- | --- |
| --Chaos-Parameters-sözlük | Iş tarafından kullanılacak bir dize adları ile ChaosParameters eşlemesini temsil eden JSON kodlu liste. |
| --süre sonu-tarih-UTC | Chaos zamanlamak için zamanlamanın ne zaman durdurulacağını durdurulacağı tarih ve saat.  Varsayılan \: 9999-12-31T23 \: 59 \: 59.999 z. |
| --işler | Chaos 'ın ne zaman çalıştırılacağını temsil eden ve ile Chaos 'in hangi parametreleri çalıştıracağınızı temsil eden JSON kodlu bir liste. |
| --başlangıç-tarih-UTC | Chaos zamanlamak için zamanlamayı kullanmanın bitiş tarihi ve saati.  Varsayılan \: 1601-01-01T00 \: 00 \: 00.000 z. |
| --timeout-t | Varsayılan \: 60. |
| --sürüm | Zamanlamanın sürüm numarası. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Description|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen \: JSON, jsonc, tablo, TSV değerleri.  Varsayılan \: JSON. |
| --sorgu | JMESPath sorgu dizesi. \:Daha fazla bilgi ve örnek için bkz. http//jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

### <a name="examples"></a>Örnekler

Aşağıdaki komut, bir zamanlamayı (geçerli zamanlamanın 0 ' dır), 2016-01-01 tarihinde başlayan ve gün 24 saat, haftanın 7 günü çalışan 2038-01-01 tarihinde sona erecek şekilde ayarlar.
Chaos bu süre için kümede zamanlanır.
```
sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]
```



## <a name="next-steps"></a>Sonraki adımlar
- Service Fabric CLı 'yi [ayarlayın](service-fabric-cli.md) .
- [Örnek betikleri](./scripts/sfctl-upgrade-application.md)kullanarak Service Fabric CLI 'nın nasıl kullanılacağını öğrenin.
