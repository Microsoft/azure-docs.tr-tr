---
title: Databricks jar ile verileri dönüştürme-Azure
description: Databricks jar çalıştırarak verileri nasıl işleyeceğini veya dönüştüreceğinizi öğrenin.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 982f00b5de9fd3e84233e5fe3b68e22fa6f7fe2a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683965"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Azure Databricks bir jar etkinliği çalıştırarak verileri dönüştürme

Bir [Data Factory](concepts-pipelines-activities.md) işlem hattındaki Azure Databricks Jar etkinliği Azure Databricks kümenizde Spark jar 'i çalıştırır. Bu makale, veri dönüştürme ve desteklenen dönüştürme etkinliklerine genel bir bakış sunan [veri dönüştürme etkinlikleri](transform-data.md) makalesine yönelik olarak oluşturulur. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur.

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
|ad|İşlem hattındaki etkinliğin adı.|Evet|
|açıklama|Etkinliğin ne yaptığını açıklayan metin.|Hayır|
|type|Databricks jar etkinliği için etkinlik türü Databricksmini jar ' dir.|Evet|
|linkedServiceName|Jar etkinliğinin üzerinde çalıştığı Databricks bağlı hizmetinin adı. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlantılı hizmetleri](compute-linked-services.md) makalesi.|Evet|
|mainClassName|Yürütülecek ana yöntemi içeren sınıfın tam adı. Bu sınıf, kitaplık olarak sunulan bir JAR içinde yer almalıdır.|Evet|
|parametreler|Main yöntemine geçirilecek parametreler.  Bu bir dizeler dizisidir.|Hayır|
|Kütüphaneler|İşi yürütecek olan kümeye yüklenecek kitaplıkların listesi. < Dize, nesne > dizisi olabilir|Evet (mainClassName yöntemini içeren en az bir tane)|

> [!NOTE]
> **Bilinen sorun** -eşzamanlı Databricks jar etkinliklerini çalıştırmak Için aynı [etkileşimli kümeyi](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) kullanırken (küme yeniden başlatma olmadan), databricks 'te, 1. etkinliğin parametrelerinin aşağıdaki etkinlikler tarafından kullanılacağı bilinen bir sorun vardır aynı zamanda. Bu nedenle, sonraki işlere geçirilen yanlış parametrelere neden oldu. Bunu azaltmak için bunun yerine bir [iş kümesi](compute-linked-services.md#example---using-new-job-cluster-in-databricks) kullanın. 

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

Daha fazla ayrıntı için kitaplık türleri için [Databricks belgeleri](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) bölümüne bakın.

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks 'te bir kitaplığı karşıya yükleme

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Databricks çalışma alanı kullanıcı arabirimini kullanma](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Kullanıcı arabirimi kullanılarak eklenen kitaplığın dBFS yolunu almak için [Databricks CLI (yükleme)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)kullanabilirsiniz. 

Genellikle jar kitaplıkları, Kullanıcı arabirimi kullanılırken dBFS:/FileStore/jars altında depolanır. CLı aracılığıyla tümünü listeleme: *databricks FS ls dBFS:/FileStore/Job-jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Databricks CLı kullanarak Kitaplığı kopyalama](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Databricks CLı [(yükleme adımları)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)kullanın. 

Örnek-JAR 'yi dBFS 'e kopyalama: *dBFS CP sparkpi-Assembly-0.1. jar dBFS:/docs/parlak Pi. jar*
