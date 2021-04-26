---
title: Databricks Python ile veri dönüştürme
description: Azure Data Factory bir işlem hattında Databricks Python etkinliğini çalıştırarak verileri nasıl işleyeceğini veya dönüştürebileceğinizi öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373955"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Azure Databricks bir Python etkinliği çalıştırarak verileri dönüştürme
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bir [Data Factory işlem hattının](concepts-pipelines-activities.md) Azure Databricks python etkinliği Azure Databricks kümenizdeki bir Python dosyası çalıştırır. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesinde oluşturulur. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur.

Bu özelliğe yönelik on bir dakikalık bir giriş ve tanıtım için, aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python etkinlik tanımı

Databricks Python etkinliğinin örnek JSON tanımı aşağıda verilmiştir:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python etkinlik özellikleri

Aşağıdaki tabloda JSON tanımında kullanılan JSON özellikleri açıklanmaktadır:

|Özellik|Açıklama|Gerekli|
|---|---|---|
|name|İşlem hattındaki etkinliğin adı.|Yes|
|açıklama|Etkinliğin ne yaptığını açıklayan metin.|No|
|tür|Databricks Python etkinliği için etkinlik türü Databricksmini Python ' dır.|Yes|
|linkedServiceName|Python etkinliğinin çalıştırıldığı Databricks bağlı hizmetinin adı. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi.|Yes|
|pythonFile|Yürütülecek Python dosyasının URI 'SI. Yalnızca DBFS yolları desteklenir.|Yes|
|parameters|Python dosyasına geçirilecek komut satırı parametreleri. Bu bir dizeler dizisidir.|No|
|kitaplıklar|İşi yürütecek olan kümeye yüklenecek kitaplıkların listesi. <dize, nesne> dizisi olabilir|No|

## <a name="supported-libraries-for-databricks-activities"></a>Databricks etkinlikleri için desteklenen kitaplıklar

Yukarıdaki databricks etkinlik tanımında şu kitaplık türlerini belirtirsiniz: *jar*, *Egg*, *Maven*, *Pypı*, *Cran*.

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

Daha fazla ayrıntı için kitaplık türleri için [Databricks belgeleri](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) bölümüne bakın.

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks 'te bir kitaplığı karşıya yükleme

### <a name="you-can-use-the-workspace-ui"></a>Çalışma alanı kullanıcı arabirimini kullanabilirsiniz:

1. [Databricks çalışma alanı kullanıcı arabirimini kullanma](/azure/databricks/libraries/#create-a-library)

2. Kullanıcı arabirimi kullanılarak eklenen kitaplığın dBFS yolunu almak için [Databricks CLI](/azure/databricks/dev-tools/cli/#install-the-cli)kullanabilirsiniz.

   Genellikle jar kitaplıkları, Kullanıcı arabirimi kullanılırken dBFS:/FileStore/jars altında depolanır. CLı aracılığıyla tümünü listeleme: *databricks FS ls dBFS:/FileStore/Job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Ya da Databricks CLı 'yi de kullanabilirsiniz:

1. [Databricks CLI kullanarak kitaplığı kopyalamayı](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs) izleyin

2. Databricks CLı kullanma [(yükleme adımları)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Örnek olarak, bir JAR 'yi dBFS 'e kopyalamak için: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
