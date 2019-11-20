---
title: Azure Data Factory 'de arama etkinliği
description: Bir dış kaynaktan bir değer aramak için arama etkinliğini nasıl kullanacağınızı öğrenin. Bu çıkışa, izleyen etkinlikler tarafından daha fazla başvuru yapılabilir.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 04285de6fa7ef678e36767b7336f732ed9b45329
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679713"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory 'de arama etkinliği

Arama etkinliği, Azure Data Factory tarafından desteklenen herhangi bir veri kaynağından bir veri kümesi alabilir. Aşağıdaki senaryoda kullanın:
- Nesne adını sabit kodlamak yerine, sonraki bir etkinlikte hangi nesnelerin üzerinde çalışacağını dinamik olarak belirleme. Bazı nesne örnekleri dosya ve tablolardır.

Arama etkinliği, bir yapılandırma dosyasının veya tablosunun içeriğini okur ve döndürür. Ayrıca, bir sorgu veya saklı yordam yürütmenin sonucunu döndürür. Arama etkinliğinin çıktısı, tek bir değer ise sonraki bir kopya veya dönüştürme etkinliğinde kullanılabilir. Çıktı bir öznitelik dizisi ise, ForEach etkinliğinde kullanılabilir.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Aşağıdaki veri kaynakları arama etkinliği için desteklenir. Arama etkinliği tarafından döndürülebilecek en büyük satır sayısı, en fazla 2 MB boyutunda 5.000. Şu anda, zaman aşımından önce arama etkinliğinin en uzun süresi bir saattir.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Sözdizimi

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Tür özellikleri

Ad | Açıklama | Tür | Gerekli mi?
---- | ----------- | ---- | --------
Veri kümesi | Arama için veri kümesi başvurusu sağlar. İlgili her bağlayıcı makalesinin **veri kümesi özellikleri** bölümünden Ayrıntılar alın. | Anahtar/değer çifti | Evet
source | Kopyalama etkinliği kaynağıyla aynı olan veri kümesine özgü kaynak özelliklerini içerir. İlgili her bağlayıcı makalesinin **etkinlik özelliklerini kopyalama** bölümünden Ayrıntılar alın. | Anahtar/değer çifti | Evet
yalnızca FirstRow | Yalnızca ilk satırın mi yoksa tüm satırların mi döndürülüp döndürülmeyeceğini gösterir. | Boole | Hayır. Varsayılan değer: `true`.

> [!NOTE]
> 
> * **ByteArray** türüne sahip kaynak sütunları desteklenmez.
> * **Yapı** veri kümesi tanımlarında desteklenmiyor. Metin biçimli dosyalar için, sütun adını sağlamak üzere üst bilgi satırını kullanın.
> * Arama kaynağınız bir JSON dosyası ise, JSON nesnesinin yeniden şekillendirilmesi için `jsonPathDefinition` ayarı desteklenmez. Tüm nesneler alınacaktır.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Sonraki bir etkinliğin arama etkinliği sonucunu kullanın

Arama sonucu, etkinlik çalıştırma sonucunun `output` bölümünde döndürülür.

* **`firstRowOnly` `true` (varsayılan) olarak ayarlandığında**, çıkış biçimi aşağıdaki kodda gösterildiği gibidir. Arama sonucu sabit bir `firstRow` anahtarı altında. Sonraki etkinlikteki sonucu kullanmak için `@{activity('MyLookupActivity').output.firstRow.TableName}`örüntüsünün kullanın.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **`firstRowOnly` `false`olarak ayarlandığında** , çıkış biçimi aşağıdaki kodda gösterildiği gibidir. `count` alan, kaç kaydın döndürüleceğini gösterir. Ayrıntılı değerler sabit bir `value` dizisi altında görüntülenir. Böyle bir durumda, arama etkinliğinin ardından bir [foreach etkinliği](control-flow-for-each-activity.md)gelir. `value` diziyi, `@activity('MyLookupActivity').output.value`modelini kullanarak ForEach etkinlik `items` alanına geçirirsiniz. `value` dizisindeki öğelere erişmek için şu sözdizimini kullanın: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. `@{activity('lookupActivity').output.value[0].tablename}` bunun bir örneğidir.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Kopyalama etkinliği örneği
Bu örnekte, kopyalama etkinliği verileri Azure SQL veritabanı örneğiniz içindeki bir SQL tablosundan Azure Blob depolama alanına kopyalar. SQL tablosunun adı, blob depolamada bir JSON dosyasında depolanır. Arama etkinliği çalışma zamanında tablo adını arar. JSON bu yaklaşım kullanılarak dinamik olarak değiştirilir. İşlem hatlarını veya veri kümelerini yeniden dağıtmanız gerekmez. 

Bu örnek yalnızca ilk satır için arama gösterir. Tüm satırları aramak ve sonuçları ForEach etkinliğiyle zincirlemek için [Azure Data Factory kullanarak birden çok tabloyu toplu olarak kopyalama](tutorial-bulk-copy.md)içindeki örneklere bakın.

### <a name="pipeline"></a>İşlem hattı
Bu işlem hattı iki etkinlik içerir: arama ve kopyalama. 

- Arama etkinliği, Azure Blob depolama alanındaki bir konuma başvuran **Lookupdataset**öğesini kullanacak şekilde yapılandırılmıştır. Arama etkinliği, SQL tablosunun adını bu konumdaki bir JSON dosyasından okur. 
- Kopyalama etkinliği, SQL tablosunun adı olan arama etkinliğinin çıkışını kullanır. **SourceDataset** 'teki **TableName** özelliği, arama etkinliğinin çıktısını kullanacak şekilde yapılandırılmıştır. Kopyalama etkinliği, verileri SQL tablosundan Azure Blob depolama alanındaki bir konuma kopyalar. Konum **Sinkdataset** özelliği tarafından belirtilir. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Arama veri kümesi
**Arama** veri kümesi, **AzureStorageLinkedService** türü tarafından belirtilen Azure depolama Arama klasöründeki **SourceTable. JSON** dosyasıdır. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>Kopyalama etkinliği için **kaynak** veri kümesi
**Kaynak** veri KÜMESI, SQL tablosunun adı olan arama etkinliğinin çıkışını kullanır. Kopyalama etkinliği, verileri bu SQL tablosundan Azure Blob depolama alanındaki bir konuma kopyalar. Konum, **Havuz** veri kümesi tarafından belirtilir. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Kopyalama etkinliği için **Havuz** veri kümesi
Kopyalama etkinliği, verileri SQL tablosundan Azure Storage 'daki **CSV** klasörü içindeki **filebylookup. csv** dosyasına kopyalar. Dosya, **AzureStorageLinkedService** özelliği tarafından belirtilir. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti
Bu depolama hesabı, SQL tablolarının adlarıyla birlikte JSON dosyasını içerir. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti
Bu Azure SQL veritabanı örneği, blob depolamaya kopyalanacak verileri içerir. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>SourceTable. JSON

#### <a name="set-of-objects"></a>Nesne kümesi

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>nesne dizisi

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Sınırlamalar ve geçici çözümler

Arama etkinliğinin ve önerilen geçici çözümlerin bazı sınırlamaları aşağıda verilmiştir.

| Sınırlama | Geçici çözüm |
|---|---|
| Arama etkinliğinin en fazla 5.000 satırı ve en fazla 2 MB boyutu vardır. | Dış işlem hattının, en fazla satır veya boyutu aşmayacak verileri alan bir iç işlem hattı üzerinden yineleyen iki düzeyli bir işlem hattı tasarlayın. |
| | |

## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün: 

- [İşlem hattı yürütme etkinliği](control-flow-execute-pipeline-activity.md)
- [ForEach etkinliği](control-flow-for-each-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
- [Web etkinliği](control-flow-web-activity.md)
