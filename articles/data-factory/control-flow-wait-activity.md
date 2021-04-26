---
title: Azure Data Factory bekleme etkinliği
description: Bekleme etkinliği, belirtilen dönem için işlem hattının yürütülmesini duraklatır.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/12/2018
ms.openlocfilehash: 9271723e68e5edd6805f0197bca9cdefc3c5e921
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100388337"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Azure Data Factory 'de bekleme etkinliğini Yürüt
İşlem hattında Bekleme etkinliğini kullandığınızda, işlem hattı izleyen etkinlikleri yürütmeye devam etmeden önce belirtilen süre kadar bekler. 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntax

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
name | `Wait`Etkinliğin adı. | Dize | Yes
tür | **Wait** olarak ayarlanmalıdır. | Dize | Yes
Waittimeınseconds | İşlem hattının işleme devam etmeden önce bekleyeceği saniye sayısı. | Tamsayı | Yes

## <a name="example"></a>Örnek

> [!NOTE]
> Bu bölüm, işlem hattını çalıştırmak için JSON tanımları ve örnek PowerShell komutları sağlar. Azure PowerShell ve JSON tanımlarını kullanarak Data Factory işlem hattı oluşturmaya yönelik adım adım yönergeler için bkz. [öğretici: Azure PowerShell kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Bekleme etkinliği ile işlem hattı
Bu örnekte, işlem hattının iki etkinliği vardır: **until** ve **wait**. Bekleme etkinliği bir saniye bekleyecek şekilde yapılandırılmıştır. İşlem hattı, her çalıştırma arasında ikinci bir bekleme süresine sahip bir döngüde Web etkinliğini çalıştırır. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün: 

- [If Condition Etkinliği](control-flow-if-condition-activity.md)
- [İşlem hattı yürütme etkinliği](control-flow-execute-pipeline-activity.md)
- [Her Bir Etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web Etkinliği](control-flow-web-activity.md)
- [Until Etkinliği](control-flow-until-activity.md)
