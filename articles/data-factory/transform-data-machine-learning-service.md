---
title: Azure Machine Learning işlem hatlarını yürütme
description: Azure Machine Learning işlem hatlarınızı Azure Data Factory işlem hatlarınız içinde nasıl çalıştıracağınızı öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.author: weetok
author: dcstwh
ms.date: 07/16/2020
ms.openlocfilehash: d0b941325d6bbabcb083b5f71a40c145d3ef48d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103479"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Data Factory Azure Machine Learning işlem hatlarını yürütme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Machine Learning işlem hatlarınızı, Azure Data Factory işlem hatlarında bir adım olarak çalıştırın. Machine Learning yürütme işlem hattı etkinliği, olası kredi varsayılanlarını tanımlama, yaklaşımı belirleme ve müşteri davranış düzenlerini çözümleme gibi toplu tahmin senaryolarına olanak tanır.

Aşağıdaki videoda, bu özelliğin altı dakikalık bir giriş ve gösterimi sunulmaktadır.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>Syntax

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
name | İşlem hattındaki etkinliğin adı | Dize | Yes
tür | Etkinliğin türü ' AzureMLExecutePipeline ' | Dize | Yes
linkedServiceName | Azure Machine Learning bağlı hizmet | Bağlı hizmet başvurusu | Yes
Mlpipelineıd | Yayınlanan Azure Machine Learning işlem hattının KIMLIĞI | Dize (veya dize resultType 'ı olan ifade) | Yes
experimentName | Machine Learning işlem hattı çalıştırmasının çalışma geçmişi deneme adı | Dize (veya dize resultType 'ı olan ifade) | No
mlPipelineParameters | Yayımlanan Azure Machine Learning ardışık düzen uç noktasına geçirilecek anahtar, değer çiftleri. Anahtarların yayımlanan Machine Learning işlem hattında tanımlanan işlem hattı parametrelerinin adlarıyla eşleşmesi gerekir | Anahtar değer çiftlerine sahip nesne (veya resultType nesnesi olan Ifade) | No
mlParentRunId | Ana Azure Machine Learning işlem hattı çalıştırma KIMLIĞI | Dize (veya dize resultType 'ı olan ifade) | No
Datapathatamalar | Azure Machine Learning 'de veri yollarını değiştirmek için kullanılan sözlük. Veri yollarının geçişini sunar | Anahtar değer çiftlerine sahip nesne | No
Devam Onstepfailure | Bir adım başarısız olursa Machine Learning ardışık düzen çalıştırmasında diğer adımların yürütülmesine devam edilip edilmeyeceğini belirtir | boolean | No

> [!NOTE]
> Machine Learning işlem hattı adı ve KIMLIĞINDE açılan öğeleri doldurmak için, kullanıcının ML işlem hatlarını listeleme izni olması gerekir. ADF UX, oturum açmış kullanıcının kimlik bilgilerini kullanarak doğrudan AzureMLService API 'Lerini çağırır.  

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
