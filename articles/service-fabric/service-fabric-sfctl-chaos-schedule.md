---
title: Azure Service Fabric CLı-sfctl Chaos zamanlaması | Microsoft Docs
description: CLı sfctl Chaos zamanlama komutlarını Service Fabric açıklar.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 443a8186ac6e57360105e59e30f84db997cd2251
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72897549"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
Chaos zamanlamasını alın ve ayarlayın.

## <a name="commands"></a>Komutlar

|Komut|Açıklama|
| --- | --- |
| Al | Chaos 'ın ne zaman ve nasıl çalıştırılacağını tanımlayan Chaos zamanlamasını alın. |
| set | Chaos tarafından kullanılan zamanlamayı ayarlayın. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl Chaos zamanlaması al
Chaos 'ın ne zaman ve nasıl çalıştırılacağını tanımlayan Chaos zamanlamasını alın.

Kullanımdaki Chaos zamanlamasının sürümünü ve Chaos 'ın ne zaman ve nasıl çalıştırılacağını tanımlayan Chaos zamanlamasını alır.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --timeout-t | İşlemi saniye cinsinden gerçekleştirmek için sunucu zaman aşımı. Bu zaman aşımı, istemcinin istenen işlemin tamamlanmasını beklemek istediği süreyi belirtir. Bu parametre için varsayılan değer 60 saniyedir.  Varsayılan\: 60. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

## <a name="sfctl-chaos-schedule-set"></a>sfctl Chaos zamanlama kümesi
Chaos tarafından kullanılan zamanlamayı ayarlayın.

Chaos, Chaos zamanlaması temelinde otomatik olarak çalıştırmalar zamanlar. Belirtilen sürüm sunucudaki sürümle eşleşiyorsa, Chaos zamanlaması güncelleştirilecektir. Chaos zamanlaması güncelleştirilirken, sunucudaki sürüm 1 artırılır. Sunucu üzerindeki sürüm, büyük bir sayıya ulaştıktan sonra tekrar 0 olarak kaydırılır. Bu çağrı yapıldığında Chaos çalışıyorsa, çağrı başarısız olur.

### <a name="arguments"></a>Bağımsız Değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Chaos-Parameters-sözlük | Iş tarafından kullanılacak bir dize adları ile ChaosParameters eşlemesini temsil eden JSON kodlu liste. |
| --süre sonu-tarih-UTC | Chaos zamanlamak için zamanlamanın ne zaman durdurulacağını durdurulacağı tarih ve saat.  Varsayılan\: 9999-31-31T23\:59\:59.999 Z. |
| --işler | Chaos 'ın ne zaman çalıştırılacağını temsil eden ve ile Chaos 'in hangi parametreleri çalıştıracağınızı temsil eden JSON kodlu bir liste. |
| --başlangıç-tarih-UTC | Chaos zamanlamak için zamanlamayı kullanmanın bitiş tarihi ve saati.  Varsayılan\: 1601-01-01T00\:00\:00.000 Z. |
| --timeout-t | Varsayılan\: 60. |
| --sürüm | Zamanlamanın sürüm numarası. |

### <a name="global-arguments"></a>Genel bağımsız değişkenler

|Bağımsız Değişken|Açıklama|
| --- | --- |
| --Hata Ayıkla | Tüm hata ayıklama günlüklerini göstermek için günlük ayrıntı düzeyini artırın. |
| --Yardım-h | Bu yardım iletisini gösterin ve çıkın. |
| --çıkış-o | Çıkış biçimi.  İzin verilen değerler JSON, jsonc, tablo, TSV\:.  JSON\: varsayılan. |
| --sorgu | JMESPath sorgu dizesi. Daha fazla bilgi ve örnek için bkz. http\://jmespath.org/. |
| --ayrıntılı | Günlük ayrıntı düzeyini artırın. Tam hata ayıklama günlükleri için--Debug kullanın. |

### <a name="examples"></a>Örnekler

Aşağıdaki komut, bir zamanlamayı (geçerli zamanlamanın 0 ' dır), 2016-01-01 tarihinde başlayan ve gün 24 saat, haftanın 7 günü çalışan 2038-01-01 tarihinde sona erecek şekilde ayarlar. Chaos bu süre için kümede zamanlanır.

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


## <a name="next-steps"></a>Sonraki adımlar
- Service Fabric CLı 'yi [ayarlayın](service-fabric-cli.md) .
- [Örnek betikleri](/azure/service-fabric/scripts/sfctl-upgrade-application)kullanarak Service Fabric CLI 'nın nasıl kullanılacağını öğrenin.
