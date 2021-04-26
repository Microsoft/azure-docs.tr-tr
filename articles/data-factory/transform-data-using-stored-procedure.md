---
title: Saklı yordam etkinliğini kullanarak verileri dönüştürme
description: Bir Data Factory işlem hattından bir Azure SQL veritabanı/veri ambarında saklı yordam çağırmak için SQL Server saklı yordam etkinliğinin nasıl kullanılacağını açıklar.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 11/27/2018
ms.openlocfilehash: b9ba2f9de82522d4348fa341ad0b41d43c3eebcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375655"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory SQL Server saklı yordam etkinliğini kullanarak verileri dönüştürme
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-stored-proc-activity.md)
> * [Güncel sürüm](transform-data-using-stored-procedure.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ham verileri tahmine dayalı ve Öngörüler halinde dönüştürmek ve [işlemek için bir](concepts-pipelines-activities.md) Data Factory işlem hattındaki veri dönüştürme etkinliklerini kullanırsınız. Saklı yordam etkinliği Data Factory desteklediği dönüştürme etkinliklerinden biridir. Bu makalede, veri dönüşümüyle ilgili genel bir genel bakış ve Data Factory içindeki desteklenen dönüştürme etkinlikleri sunan [verileri Dönüştür](transform-data.md) makalesinde derleme yapılır.

> [!NOTE]
> Azure Data Factory yeni bir deyişle, [Azure Data Factory 'ye giriş](introduction.md) yapın ve öğreticiyi yapın: Öğretici: Bu makaleyi okumadan önce [verileri dönüştürün](tutorial-transform-data-spark-powershell.md) . 

Saklı yordam etkinliğini, kuruluşunuzda bulunan aşağıdaki veri depolarından birinde veya bir Azure sanal makinesinde (VM) bulunan bir saklı yordamı çağırmak için kullanabilirsiniz: 

- Azure SQL Veritabanı
- Azure Synapse Analytics
- SQL Server veritabanı.  SQL Server kullanıyorsanız, kendisini barındıran aynı makineye veya veritabanına erişimi olan ayrı bir makineye şirket içinde barındırılan tümleştirme çalışma zamanı 'nı yükleyebilirsiniz. Self-Hosted Integration Runtime, bulut hizmetleriyle şirket içi/Şirket içindeki veri kaynaklarını güvenli ve yönetilen bir şekilde bağlayan bir bileşendir. Ayrıntılar için bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanı](create-self-hosted-integration-runtime.md) makalesi.

> [!IMPORTANT]
> Verileri Azure SQL veritabanı 'na veya SQL Server kopyalarken, Copy etkinliğinde **Sqlsink** öğesini, **sqlWriterStoredProcedureName** özelliğini kullanarak bir saklı yordam çağırmak üzere yapılandırabilirsiniz. Özelliği hakkında daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Bir kopyalama etkinliği kullanarak verileri Azure SYNAPSE Analytics 'e kopyalarken saklı yordamı çağırmak desteklenmez. Ancak, Azure SYNAPSE Analytics 'te saklı yordam çağırmak için saklı yordam etkinliğini kullanabilirsiniz. 
>
> Verileri Azure SQL veritabanı veya SQL Server ya da Azure SYNAPSE Analytics 'ten kopyalarken, **sqlReaderStoredProcedureName** özelliğini kullanarak kaynak veritabanından veri okumak için bir saklı yordam çağırmak üzere, Copy etkinliğinde **SQLSource** ' u yapılandırabilirsiniz. Daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SYNAPSE Analytics](connector-azure-sql-data-warehouse.md)          

 

## <a name="syntax-details"></a>Söz dizimi ayrıntıları
Saklı yordam etkinliğinin tanımlanması için JSON biçimi aşağıda verilmiştir:

```json
{
    "name": "Stored Procedure Activity",
    "description":"Description",
    "type": "SqlServerStoredProcedure",
    "linkedServiceName": {
        "referenceName": "AzureSqlLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

Aşağıdaki tabloda bu JSON özellikleri açıklanmaktadır:

| Özellik                  | Açıklama                              | Gerekli |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | Etkinliğin adı                     | Yes      |
| açıklama               | Etkinliğin ne için kullanıldığını açıklayan metin | No       |
| tür                      | Saklı yordam etkinliği için etkinlik türü **Sqlserverstoredprocedure** olur | Yes      |
| linkedServiceName         | **Azure SQL veritabanı** veya **Azure SYNAPSE Analytics** 'e veya Data Factory bağlı hizmet olarak kaydedilen **SQL Server** başvuru. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Yes      |
| storedProcedureName       | Çağrılacak saklı yordamın adını belirtin. | Yes      |
| storedProcedureParameters | Saklı yordam parametrelerinin değerlerini belirtin. `"param1": { "value": "param1Value","type":"param1Type" }`Parametre değerlerini ve veri kaynağı tarafından desteklenen türlerini geçirmek için kullanın. Bir parametre için null değer geçirmeniz gerekiyorsa, `"param1": { "value": null }` (tüm küçük harf) seçeneğini kullanın. | No       |

## <a name="parameter-data-type-mapping"></a>Parametre veri türü eşleme
Parametresi için belirttiğiniz veri türü, kullanmakta olduğunuz veri kaynağındaki veri türüyle eşleşen Azure Data Factory türüdür. Veri kaynağınız için veri türü eşlemelerini bağlayıcılar alanında bulabilirsiniz. Bazı örnekler

| Veri Kaynağı          | Veri türü eşleme |
| ---------------------|-------------------|
| Azure Synapse Analytics | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Veritabanı   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |




## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın: 

* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md)
* [Hadoop akışı etkinliği](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klasik) Batch Yürütme Etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
