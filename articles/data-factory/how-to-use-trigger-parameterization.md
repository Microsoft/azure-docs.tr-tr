---
title: Tetikleyici bilgilerini işlem hattına geçir
description: İşlem hattında tetikleyici meta verilerine nasıl başvurulacağını öğrenin
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: ''
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 50a9f9cd59ebeecae89580c878442eb20788f462
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593654"
---
# <a name="reference-trigger-metadata-in-pipeline-runs"></a>İşlem hattı çalıştırmalarından başvuru tetikleyici meta verileri

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, tetikleme başlangıç zamanı gibi tetikleyici meta verilerinin işlem hattı çalıştırmasında nasıl kullanılabileceği açıklanmaktadır.

İşlem hattının bazen onu çağıran tetikleyiciden meta verileri anlaması ve okuması gerekir. Örneğin, pencere başlangıç ve bitiş zamanına bağlı olarak bir pencere tetikleme çalıştırması ile, işlem hattı farklı veri dilimleri veya klasörleri işler. Azure Data Factory ' de, meta verileri tetikleyiciden işlem hattına geçirmek için Parametreleştirme ve [sistem değişkenini](control-flow-system-variables.md) kullanırız.

Bu model, tetikleyici pencere [tetikleyicisi](how-to-create-tumbling-window-trigger.md)için oldukça yararlıdır; burada tetikleyici, Windows başlangıç ve bitiş [zamanı ve özel](how-to-create-custom-event-trigger.md) [tanımlanmış _veri_ alanındaki](../event-grid/event-schema.md)değerleri ayrıştırır ve işler.

> [!NOTE]
> Farklı tetikleyici türü farklı meta veri bilgileri sağlar. Daha fazla bilgi için bkz. [sistem değişkeni](control-flow-system-variables.md)

## <a name="data-factory-ui"></a>Data Factory Kullanıcı Arabirimi (UI)

Bu bölümde, Azure Data Factory Kullanıcı arabiriminde tetikleyiciden meta veri bilgilerinin nasıl işlem hattına iletilmesi gösterilmektedir.

1. **Yazma tuvaline** gidin ve bir işlem hattını düzenleyin

1. Ardışık düzen ayarlarını görüntülemek için boş tuvale tıklayın. Herhangi bir etkinlik seçmeyin. Daraltılmış olabileceği için, tuvalin alt kısmından ayar panelini almanız gerekebilir

1. Parametreler **bölümünü seçin** ve parametreler eklemek Için **+ Yeni** ' yi seçin

    :::image type="content" source="media/how-to-use-trigger-parameterization/01-create-parameter.png" alt-text="Ardışık düzende parametrelerin nasıl tanımlanacağını gösteren işlem hattı ayarının ekran görüntüsü.":::

1. ' A ve ' a tıklayarak işlem hattına tetikler **ekleyin.**

1. İşlem hattına bir tetikleyici oluşturun veya ekleyin ve **Tamam 'a** tıklayın

1. Aşağıdaki sayfada, her bir parametre için tetikleyici meta verilerini girin. Tetikleyici bilgilerini almak için [sistem değişkeninde](control-flow-system-variables.md) tanımlanan biçimi kullanın. Tüm parametrelerin bilgilerini doldurmanız gerekmez, yalnızca tetikleyici meta veri değerlerini varsayacaktır. Örneğin, burada *parameter_1* için tetikleyici çalıştırma başlangıç zamanı atandık.

    :::image type="content" source="media/how-to-use-trigger-parameterization/02-pass-in-system-variable.png" alt-text="Tetikleyici bilgilerinin işlem hattı parametrelerine nasıl geçirileceğini gösteren tetikleyici tanımı sayfasının ekran görüntüsü.":::

1. Ardışık düzen değerlerini kullanmak için, işlem hattı tanımlarında sistem değişkeni __değil__ Parameters _@pipeline (). Parameters. ParameterName_ kullanın. Örneğin, bu durumda tetikleyici başlangıç zamanını okumak için @pipeline () .Parameters.parameter_1 başvuracağız.

## <a name="json-schema"></a>JSON şeması

Tetikleyici bilgilerini işlem hattı çalışmalarıyla geçirmek için hem tetikleyici hem de JSON işlem hattı _parametreleri_ bölümüyle birlikte güncelleştirilmeleri gerekir.

### <a name="pipeline-definition"></a>İşlem hattı tanımı

**Özellikler** bölümü altında **Parametreler** bölümüne parametre tanımları ekleyin

```json
{
    "name": "demo_pipeline",
    "properties": {
        "activities": [
            {
                "name": "demo_activity",
                "type": "WebActivity",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "url": {
                        "value": "@pipeline().parameters.parameter_2",
                        "type": "Expression"
                    },
                    "method": "GET"
                }
            }
        ],
        "parameters": {
            "parameter_1": {
                "type": "string"
            },
            "parameter_2": {
                "type": "string"
            },
            "parameter_3": {
                "type": "string"
            },
            "parameter_4": {
                "type": "string"
            },
            "parameter_5": {
                "type": "string"
            }
        },
        "annotations": [],
        "lastPublishTime": "2021-02-24T03:06:23Z"
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

### <a name="trigger-definition"></a>Tetikleyici tanımı

İşlem **hatları** bölümünde **Parametreler** bölümünde parametre değerlerini atayın. Tüm parametrelerin bilgilerini doldurmanız gerekmez, yalnızca tetikleyici meta veri değerlerini varsayacaktır.

```json
{
    "name": "trigger1",
    "properties": {
        "annotations": [],
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "demo_pipeline",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "parameter_1": "@trigger().startTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Minute",
                "interval": 15,
                "startTime": "2021-03-03T04:38:00Z",
                "timeZone": "UTC"
            }
        }
    }
}
```

### <a name="use-trigger-information-in-pipeline"></a>İşlem hattında tetikleyici bilgilerini kullanma

Ardışık düzen değerlerini kullanmak için, işlem hattı tanımlarında sistem değişkeni __değil__ Parameters _@pipeline (). Parameters. ParameterName_ kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Tetikleyiciler hakkında ayrıntılı bilgi için bkz. işlem [hattı yürütme ve Tetikleyiciler](concepts-pipeline-execution-triggers.md#trigger-execution).
