---
title: Azure Data Factory veri kümesi sütunlarını eşleme
description: Kaynak sütunlarının hedef sütunlara nasıl eşlendiğini öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 80ba7dc48da7cb5c43aae209c4e76c54948b8f88
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666782"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Kaynak veri kümesi sütunlarını hedef veri kümesi sütunlarına eşleme
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. 

Sütun eşleme, kaynak tablonun "yapısı" içinde belirtilen sütunların havuz tablosunun "yapısı" içinde belirtilen sütunlara nasıl eşlendiğini belirtmek için kullanılabilir. **ColumnMapping** özelliği, kopyalama etkinliğinin **typeproperties** bölümünde bulunur.

Sütun eşleme aşağıdaki senaryoları destekler:

* Kaynak veri kümesi yapısındaki tüm sütunlar, havuz veri kümesi yapısındaki tüm sütunlarla eşleştirilir.
* Kaynak veri kümesi yapısındaki sütunların bir alt kümesi, havuz veri kümesi yapısındaki tüm sütunlarla eşleştirilir.

Aşağıda bir özel durumla sonuçlanan hata koşulları verilmiştir:

* Havuz tablosunun "Structure" öğesinde, eşlemede belirtilenden daha az sütun veya daha fazla sütun.
* Yinelenen eşleme.
* SQL sorgu sonucunun eşlemede belirtilen bir sütun adı yok.

> [!NOTE]
> Aşağıdaki örnekler Azure SQL ve Azure Blob içindir, ancak dikdörtgen veri kümelerini destekleyen tüm veri depolamaları için geçerlidir. Örneklerdeki veri kümesini ve bağlı hizmet tanımlarını, ilgili veri kaynağındaki verileri işaret etmek için ayarlayın.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Örnek 1 – Azure SQL 'den Azure Blob 'a sütun eşleme
Bu örnekte, giriş tablosunda bir yapı bulunur ve Azure SQL veritabanındaki bir SQL tablosunu işaret eder.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Bu örnekte, çıkış tablosunun bir yapısı vardır ve bir Azure Blob depolama alanındaki blobu işaret eder.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Aşağıdaki JSON bir işlem hattındaki kopyalama etkinliğini tanımlar. **Translator** özelliği kullanılarak, kaynak, havuz (**ColumnMappings**) içindeki sütunlara eşlenen sütunlar.

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Sütun eşleme akışı:**

![Sütun eşleme akışı](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Örnek 2 – Azure SQL 'den Azure Blob 'a SQL sorgusuyla sütun eşleme
Bu örnekte, yalnızca tablo adını ve "yapı" bölümünde sütun adlarını belirtmek yerine Azure SQL 'den veri ayıklamak için bir SQL sorgusu kullanılır. 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
Bu durumda, sorgu sonuçları ilk olarak kaynağın "yapısı" bölümünde belirtilen sütunlara eşlenir. Sonra, "Structure" kaynağından alınan sütunlar, columnMappings içinde belirtilen kurallarla birlikte "Structure" havuzu içindeki sütunlara eşlenir.  Sorgunun, kaynağın "yapısında" belirtilenden daha fazla sütun olduğunu varsayalım.

**Sütun eşleme akışı**

![Sütun eşleme akışı-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Sonraki adımlar
Kopyalama etkinliğini kullanma hakkında öğretici için makaleye bakın: 

- [Blob depolamadan SQL veritabanına veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
