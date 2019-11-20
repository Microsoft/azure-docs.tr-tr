---
title: Azure Data Factory içinde işlem hattı etkinliğini yürütme
description: Başka bir Data Factory işlem hattından bir Data Factory işlem hattını çağırmak için ardışık düzen yürütme etkinliğini nasıl kullanabileceğinizi öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 093050952ed826a540c35b2b73acd107fafc45ab
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73679930"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Azure Data Factory içinde işlem hattı etkinliğini yürütme
İşlem hattını Çalıştır etkinliği, bir Data Factory işlem hattının başka bir işlem hattını çağırmasına izin verir.

## <a name="syntax"></a>Sözdizimi

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
ad | İşlem hattının yürütülmesi etkinliğinin adı. | Dize | Evet
type | : **Executepipeline**olarak ayarlanmalıdır. | Dize | Evet
Konfigüre | Bu işlem hattının çağırdığı bağımlı işlem hattına işlem hattı başvurusu. İşlem hattı başvuru nesnesi iki özelliğe sahiptir: **ReferenceName** ve **Type**. ReferenceName özelliği, başvuru işlem hattının adını belirtir. Type özelliği PipelineReference olarak ayarlanmalıdır. | PipelineReference | Evet
parametreler | Çağrılan işlem hattına geçirilecek parametreler | Parametre adlarını bağımsız değişken değerleriyle eşleyen bir JSON nesnesi | Hayır
waitOnCompletion | Etkinlik yürütmenin bağımlı işlem hattı yürütmenin bitmesini bekleyip beklemediğini tanımlar. Varsayılan değer false’tur. | Boole | Hayır

## <a name="sample"></a>Örnek
Bu senaryonun iki işlem hattı vardır:

- **Ana** işlem hattı-bu işlem hattı çağrılan işlem hattını çağıran bir yürütme işlem hattı etkinliğine sahiptir. Ana işlem hattı iki parametre alır: `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Çağrılan işlem hattı** -bu işlem hattı, verileri bir Azure Blob kaynağından Azure Blob havuzuna kopyalayan bir kopyalama etkinliğine sahiptir. Çağrılan işlem hattı iki parametre alır: `sourceBlobContainer`, `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Ana işlem hattı tanımı

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Çağrılan işlem hattı tanımı

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Bağlı hizmet**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**Kaynak veri kümesi**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Havuz veri kümesi**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>İşlem hattını çalıştırma

Ana işlem hattını Bu örnekte çalıştırmak için, masterSourceBlobContainer ve masterSinkBlobContainer parametreleri için aşağıdaki değerler geçirilir: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Ana işlem hattı, aşağıdaki örnekte gösterildiği gibi, bu değerleri çağrılan işlem hattına iletir: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün: 

- [Her etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web etkinliği](control-flow-web-activity.md)
