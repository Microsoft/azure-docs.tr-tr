---
title: Databricks jar ile veri dönüştürme
description: Azure Data Factory bir işlem hattı içinde Databricks jar çalıştırarak verileri nasıl işleyeceğini veya dönüştürebileceğinizi öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374023"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Azure Databricks bir jar etkinliği çalıştırarak verileri dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bir [Data Factory](concepts-pipelines-activities.md) işlem hattındaki Azure Databricks Jar etkinliği Azure Databricks kümenizde Spark jar 'i çalıştırır. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesinde oluşturulur. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur.

Bu özelliğe yönelik on bir dakikalık bir giriş ve tanıtım için, aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks jar etkinlik tanımı

Databricks jar etkinliğinin örnek JSON tanımı aşağıda verilmiştir:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks jar etkinlik özellikleri

Aşağıdaki tabloda JSON tanımında kullanılan JSON özellikleri açıklanmaktadır:

|Özellik|Açıklama|Gerekli|
|:--|---|:-:|
|name|İşlem hattındaki etkinliğin adı.|Yes|
|açıklama|Etkinliğin ne yaptığını açıklayan metin.|No|
|tür|Databricks jar etkinliği için etkinlik türü Databricksmini jar ' dir.|Yes|
|linkedServiceName|Jar etkinliğinin üzerinde çalıştığı Databricks bağlı hizmetinin adı. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi.|Yes|
|mainClassName|Yürütülecek ana yöntemi içeren sınıfın tam adı. Bu sınıf, kitaplık olarak sunulan bir JAR içinde yer almalıdır. JAR dosyası birden çok sınıf içerebilir. Sınıfların her biri, bir Main yöntemi içerebilir.|Yes|
|parameters|Main yöntemine geçirilecek parametreler. Bu özellik bir dizeler dizisidir.|No|
|kitaplıklar|İşi yürütecek olan kümeye yüklenecek kitaplıkların listesi. <dize, nesne> dizisi olabilir|Evet (mainClassName yöntemini içeren en az bir tane)|

> [!NOTE]
> **Bilinen sorun** -eşzamanlı Databricks jar etkinliklerini çalıştırmak Için aynı [etkileşimli kümeyi](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) kullanırken (küme yeniden başlatma olmadan), databricks 'te, 1. etkinliğin parametrelerinin de aşağıdaki etkinlikler tarafından da kullanılacağı bilinen bir sorun vardır. Bu nedenle, sonraki işlere geçirilen yanlış parametrelere neden oldu. Bunu azaltmak için bunun yerine bir [iş kümesi](compute-linked-services.md#example---using-new-job-cluster-in-databricks) kullanın.

## <a name="supported-libraries-for-databricks-activities"></a>Databricks etkinlikleri için desteklenen kitaplıklar

Önceki Databricks etkinlik tanımında şu kitaplık türlerini belirttiniz: `jar` , `egg` ,, `maven` `pypi` , `cran` .

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Daha fazla bilgi için bkz. kitaplık türleri için [Databricks belgeleri](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) .

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks 'te bir kitaplığı karşıya yükleme

### <a name="you-can-use-the-workspace-ui"></a>Çalışma alanı kullanıcı arabirimini kullanabilirsiniz:

1. [Databricks çalışma alanı kullanıcı arabirimini kullanma](/azure/databricks/libraries/#create-a-library)

2. Kullanıcı arabirimi kullanılarak eklenen kitaplığın dBFS yolunu almak için [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli)kullanabilirsiniz.

   Genellikle jar kitaplıkları, Kullanıcı arabirimi kullanılırken dBFS:/FileStore/jars altında depolanır. CLı aracılığıyla tümünü listeleme: *databricks FS ls dBFS:/FileStore/Job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ya da Databricks CLı 'yi de kullanabilirsiniz:

1. [Databricks CLI kullanarak kitaplığı kopyalamayı](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs) izleyin

2. Databricks CLı kullanma [(yükleme adımları)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Örnek olarak, bir JAR 'yi dBFS 'e kopyalamak için: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Sonraki adımlar

Bu özelliğin on bir bir ön eki ve tanıtımı için [videoyu](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player)izleyin.
