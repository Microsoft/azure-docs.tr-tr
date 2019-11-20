---
title: Azure Data Factory içindeki saklı yordam etkinliğini kullanarak verileri dönüştürme
description: Bir Data Factory işlem hattından bir Azure SQL veritabanı/veri ambarında saklı yordam çağırmak için SQL Server saklı yordam etkinliğinin nasıl kullanılacağını açıklar.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/27/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 5ebb2b9cdcbef59e07476dbebd289bb4402ca5fa
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683711"
---
# <a name="transform-data-by-using-the-sql-server-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory SQL Server saklı yordam etkinliğini kullanarak verileri dönüştürme
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-stored-proc-activity.md)
> * [Geçerli sürüm](transform-data-using-stored-procedure.md)

Ham verileri tahmine dayalı ve Öngörüler halinde dönüştürmek ve [işlemek için bir](concepts-pipelines-activities.md) Data Factory işlem hattındaki veri dönüştürme etkinliklerini kullanırsınız. Saklı yordam etkinliği Data Factory desteklediği dönüştürme etkinliklerinden biridir. Bu makalede, veri dönüşümüyle ilgili genel bir genel bakış ve Data Factory içindeki desteklenen dönüştürme etkinlikleri sunan [verileri Dönüştür](transform-data.md) makalesinde derleme yapılır.

> [!NOTE]
> Azure Data Factory yeni bir deyişle, [Azure Data Factory 'ye giriş](introduction.md) yapın ve öğreticiyi yapın: Öğretici: Bu makaleyi okumadan önce [verileri dönüştürün](tutorial-transform-data-spark-powershell.md) . 

Saklı yordam etkinliğini, kuruluşunuzda bulunan aşağıdaki veri depolarından birinde veya bir Azure sanal makinesinde (VM) bulunan bir saklı yordamı çağırmak için kullanabilirsiniz: 

- Azure SQL Veritabanı
- Azure SQL Veri Ambarı
- SQL Server veritabanı.  SQL Server kullanıyorsanız, kendisini barındıran aynı makineye veya veritabanına erişimi olan ayrı bir makineye şirket içinde barındırılan tümleştirme çalışma zamanı 'nı yükleyebilirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanı, Azure VM 'deki veri kaynaklarını güvenli ve yönetilen bir şekilde bulut hizmetleriyle bağlayan bir bileşendir. Ayrıntılar için bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanı](create-self-hosted-integration-runtime.md) makalesi.

> [!IMPORTANT]
> Verileri Azure SQL veritabanı 'na veya SQL Server kopyalarken, Copy etkinliğinde **Sqlsink** öğesini, **sqlWriterStoredProcedureName** özelliğini kullanarak bir saklı yordam çağırmak üzere yapılandırabilirsiniz. Özelliği hakkında daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md). Bir kopyalama etkinliği kullanarak verileri Azure SQL veri ambarı 'na kopyalarken saklı bir yordamı çağırmak desteklenmez. Ancak, bir SQL veri ambarında saklı yordam çağırmak için saklı yordam etkinliğini kullanabilirsiniz. 
>
> Azure SQL veritabanı veya SQL Server ya da Azure SQL veri ambarı 'ndan veri kopyalarken, **sqlReaderStoredProcedureName** kullanarak kaynak veritabanından veri okumak için bir saklı yordam çağırmak üzere **SQLSource** 'u Copy etkinliğinde yapılandırabilirsiniz. özelliði. Daha fazla bilgi için şu bağlayıcı makalelerine bakın: [Azure SQL veritabanı](connector-azure-sql-database.md), [SQL Server](connector-sql-server.md), [Azure SQL veri ambarı](connector-azure-sql-data-warehouse.md)          

 

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
| ad                      | Etkinliğin adı                     | Evet      |
| açıklama               | Etkinliğin ne için kullanıldığını açıklayan metin | Hayır       |
| type                      | Saklı yordam etkinliği için etkinlik türü **Sqlserverstoredprocedure** olur | Evet      |
| linkedServiceName         | **Azure SQL veritabanı** veya **Azure SQL veri ambarı** 'na veya Data Factory bağlı hizmet olarak kaydedilmiş **SQL Server** başvuru. Bu bağlı hizmet hakkında bilgi edinmek için bkz. [işlem bağlı hizmetleri](compute-linked-services.md) makalesi. | Evet      |
| storedProcedureName       | Çağrılacak saklı yordamın adını belirtin. | Evet      |
| storedProcedureParameters | Saklı yordam parametrelerinin değerlerini belirtin. Parametre değerlerini ve veri kaynağı tarafından desteklenen türlerini iletmek için `"param1": { "value": "param1Value","type":"param1Type" }` kullanın. Bir parametre için null değeri geçirmeniz gerekiyorsa `"param1": { "value": null }` (tümü küçük harf) kullanın. | Hayır       |

## <a name="parameter-data-type-mapping"></a>Parametre veri türü eşleme
Parametresi için belirttiğiniz veri türü, kullanmakta olduğunuz veri kaynağındaki veri türüyle eşleşen Azure Data Factory türüdür. Veri kaynağınız için veri türü eşlemelerini bağlayıcılar alanında bulabilirsiniz. Bazı örnekler

| Veri kaynağı          | Veri türü eşleme |
| ---------------------|-------------------|
| Azure SQL Veri Ambarı | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#data-type-mapping-for-azure-sql-data-warehouse |
| Azure SQL Veritabanı   | https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#data-type-mapping-for-azure-sql-database | 
| Oracle               | https://docs.microsoft.com/azure/data-factory/connector-oracle#data-type-mapping-for-oracle |
| SQL Server           | https://docs.microsoft.com/azure/data-factory/connector-sql-server#data-type-mapping-for-sql-server |


## <a name="error-info"></a>Hata bilgisi

Saklı yordam başarısız olduğunda ve hata ayrıntılarını döndürdüğünde, hata bilgilerini doğrudan etkinlik çıkışında yakalayamazsınız. Ancak, tüm etkinliklerini Data Factory pompalar Azure Izleyici 'ye olay çalıştırır. Azure izleyici 'de pompalara Data Factory olaylar arasında, burada hata ayrıntılarına gönderim yapılır. Örneğin, bu olaylardan e-posta uyarılarını ayarlayabilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici kullanarak uyarı ve izleme veri fabrikaları](monitor-using-azure-monitor.md).

## <a name="next-steps"></a>Sonraki adımlar
Verileri başka yollarla nasıl dönüştürebileceğinizi açıklayan aşağıdaki makalelere bakın: 

* [U-SQL etkinliği](transform-data-using-data-lake-analytics.md)
* [Hive etkinliği](transform-data-using-hadoop-hive.md)
* [Pig etkinliği](transform-data-using-hadoop-pig.md)
* [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md)
* [Hadoop akışı etkinliği](transform-data-using-hadoop-streaming.md)
* [Spark etkinliği](transform-data-using-spark.md)
* [.NET özel etkinliği](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach yürütme etkinliği](transform-data-using-machine-learning.md)
* [Saklı yordam etkinliği](transform-data-using-stored-procedure.md)
