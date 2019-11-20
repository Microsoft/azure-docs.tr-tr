---
title: Azure Machine Learning işlem hatlarını Azure Data Factory işlem hatlarında yürütün
description: Azure Machine Learning işlem hatlarınızı Azure Data Factory işlem hatlarınız içinde nasıl çalıştıracağınızı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: a257c7d3f288f9a2e1998bf0679b7146b089cb50
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683895"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Data Factory Azure Machine Learning işlem hatlarını yürütme

Azure Machine Learning işlem hatlarınızı, Azure Data Factory işlem hatlarında bir adım olarak çalıştırın. Machine Learning yürütme işlem hattı etkinliği, olası kredi varsayılanlarını tanımlama, yaklaşımı belirleme ve müşteri davranış düzenlerini çözümleme gibi toplu tahmin senaryolarına olanak tanır.

## <a name="syntax"></a>Sözdizimi

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
ad | İşlem hattındaki etkinliğin adı | Dize | Evet
type | Etkinliğin türü ' AzureMLExecutePipeline ' | Dize | Evet
linkedServiceName | Azure Machine Learning bağlı hizmet | Bağlı hizmet başvurusu | Evet
Mlpipelineıd | Yayınlanan Azure Machine Learning işlem hattının KIMLIĞI | Dize (veya dize resultType 'ı olan ifade) | Evet
experimentName | Machine Learning işlem hattı çalıştırmasının çalışma geçmişi deneme adı | Dize (veya dize resultType 'ı olan ifade) | Hayır
mlPipelineParameters | Yayımlanan Azure Machine Learning ardışık düzen uç noktasına geçirilecek anahtar, değer çiftleri. Anahtarların yayımlanan Machine Learning işlem hattında tanımlanan işlem hattı parametrelerinin adlarıyla eşleşmesi gerekir | Anahtar değer çiftlerine sahip nesne (veya resultType nesnesi olan Ifade) | Hayır
mlParentRunId | Ana Azure Machine Learning işlem hattı çalıştırma KIMLIĞI | Dize (veya dize resultType 'ı olan ifade) | Hayır
Devam Onstepfailure | Bir adım başarısız olursa Machine Learning ardışık düzen çalıştırmasında diğer adımların yürütülmesine devam edilip edilmeyeceğini belirtir | boole | Hayır

## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın:

* [Veri akışı yürütme etkinliği](control-flow-execute-data-flow-activity.md)
* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md)
* [Hadoop akışı etkinliği](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
