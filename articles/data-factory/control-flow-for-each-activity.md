---
title: Azure Data Factory 'de ForEach etkinliği
description: Her etkinlik Için, işlem hattınızda yinelenen bir denetim akışını tanımlar. Bir koleksiyon üzerinde yineleme yapmak ve belirtilen etkinlikleri yürütmek için kullanılır.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: d0fd9ab8286496b9801d6c10682761f1264e040a
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120920"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Azure Data Factory 'de ForEach etkinliği
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ForEach etkinliği, işlem hattınızda yinelenen bir denetim akışını tanımlar. Bu etkinlik bir koleksiyon üzerinde yinelemek için kullanılır ve bir döngüde belirtilen etkinlikleri yürütür. Bu etkinliğin döngü uygulaması, programlama dillerindeki Foreach döngü yapısına benzer.

## <a name="syntax"></a>Syntax
Bu makalenin ilerleyen bölümlerinde özellikler açıklanmaktadır. Items özelliği, koleksiyondur ve koleksiyondaki her öğe, `@item()` aşağıdaki sözdiziminde gösterildiği gibi kullanılarak adlandırılır:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
name | Her etkinliğin adı. | Dize | Yes
tür | **Foreach** olarak ayarlanmalıdır | Dize | Yes
ısequential | Döngünün sıralı olarak veya paralel olarak yürütülmesi gerekip gerekmediğini belirtir.  En fazla 20 döngü yinelemesi paralel olarak çalıştırılabilir). Örneğin, **ıssequential** değeri false olarak ayarlanmış olan 10 farklı kaynak ve havuz veri kümesi içeren bir kopyalama etkinliği üzerinde yineleme Için bir foreach etkinliğinizi varsa, tüm kopyalar aynı anda yürütülür. Varsayılan değer false 'dur. <br/><br/> "Issequential" yanlış olarak ayarlanırsa, birden çok yürütülebilir dosya çalıştırmak için doğru bir yapılandırma olduğundan emin olun. Aksi takdirde, yazma çakışmalarını önlemek için bu özellik dikkatli kullanılmalıdır. Daha fazla bilgi için bkz. [paralel yürütme](#parallel-execution) bölümü. | Boole | Hayır. Varsayılan değer false 'dur.
batchCount | Paralel yürütme sayısını denetlemek için kullanılacak toplu iş sayısı (ıssequential false olarak ayarlandığında). Bu, üst eşzamanlılık sınırdır, ancak for-each etkinliği her zaman bu sayıda yürütülmeyecektir | Tamsayı (en fazla 50) | Hayır. Varsayılan değer 20 ' dir.
Öğeler | Tekrarlandırılmış bir JSON dizisi döndüren ifade. | İfade (bir JSON dizisi döndürür) | Yes
Etkinlikler | Yürütülecek etkinlikler. | Etkinlikler Listesi | Yes

## <a name="parallel-execution"></a>Paralel yürütme
**Issequential** , false olarak ayarlandıysa, etkinlik en fazla 20 eşzamanlı yineleme ile paralel olarak yinelenir. Bu ayar dikkatli kullanılmalıdır. Eşzamanlı yinelemeler aynı klasöre ancak farklı dosyalara yazıyorsanız, bu yaklaşım iyidir. Eşzamanlı yinelemeler aynı dosyaya aynı anda yazıyorsanız, bu yaklaşım büyük olasılıkla hataya neden olur. 

## <a name="iteration-expression-language"></a>Yineleme ifadesi dili
ForEach etkinliğinde, özellik **öğeleri** için tekrarlandırılmış bir dizi sağlayın. " `@item()`Foreach etkinliğinde tek bir sabit listesi üzerinden yinelemek için kullanın. Örneğin, **öğeler** bir diziyse: [1, 2, 3], `@item()` ilk yinelemede 1, ikinci yinelemede 2 ve üçüncü yinelemede 3 döndürür. `@range(0,10)`9 ile biten 0 ile başlayarak on kez yinelemek için LIKE ifadesi de kullanabilirsiniz.

## <a name="iterating-over-a-single-activity"></a>Tek bir etkinliğin üzerinde yineleme yapma
**Senaryo:** Azure Blob 'daki aynı kaynak dosyasından Azure Blob 'daki birden çok hedef dosyasına kopyalama.

### <a name="pipeline-definition"></a>İşlem hattı tanımı

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Blob veri kümesi tanımı

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Parametre değerlerini Çalıştır

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Birden çok etkinlik üzerinde yineleme
Bir ForEach etkinliğinde birden çok etkinliği yinelemek (örneğin: kopyalama ve Web etkinlikleri) mümkündür. Bu senaryoda, birden çok etkinliği ayrı bir işlem hattına soyutlanmasını öneririz. Daha sonra, birden çok etkinlikle ayrı işlem hattını çağırmak için ForEach etkinliğindeki işlem hattındaki [executepipeline etkinliğini](control-flow-execute-pipeline-activity.md) kullanabilirsiniz. 


### <a name="syntax"></a>Syntax

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Örnek
**Senaryo:** Bir ForEach etkinlik içindeki bir ınnerpipeline üzerinde işlem hattı yürütme etkinliği ile yineleme yapın. İç ardışık düzen şema tanımları parametreli olarak kopyalanır.

#### <a name="master-pipeline-definition"></a>Ana işlem hattı tanımı

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>İç ardışık düzen tanımı

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Kaynak veri kümesi tanımı

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Havuz veri kümesi tanımı

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Ana işlem hattı parametreleri
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Çıkışları toplama

__Foreach__ etkinliğinin çıkışlarını toplamak Için lütfen _değişkenleri_ kullanın ve _değişken Ekle_ etkinliğini kullanın.

İlk olarak, işlem hattında bir `array` _değişken_ bildirin. Ardından, her __foreach__ döngüsünün Içinde _değişken ekleme_ etkinliğini çağırın. Daha sonra, diziden toplamayı elde edebilirsiniz.

## <a name="limitations-and-workarounds"></a>Sınırlamalar ve geçici çözümler

ForEach etkinliğinin ve önerilen geçici çözümlerin bazı sınırlamaları aşağıda verilmiştir.

| Sınırlama | Geçici çözüm |
|---|---|
| ForEach döngüsünü başka bir ForEach döngüsünün içine (veya bir Until döngüsü) iç içe geçirilemez. | Dış ForEach döngüsü ile iç işlem hattının iç içe döngülü bir iç işlem hattı üzerinde yineleme olduğu iki düzeyli bir işlem hattı tasarlayın. |
| ForEach etkinliğinin `batchCount` paralel işleme için en fazla 50 ve en fazla 100.000 öğe vardır. | ForEach etkinliğinin dış işlem hattının bir iç işlem hattı üzerinde yineleme olduğu iki düzeyli bir işlem hattı tasarlayın. |
| SetVariable, değişken tüm işlem hattının genel olduğu için paralel olarak çalışan bir ForEach etkinliği içinde kullanılamaz, bunlar bir ForEach veya başka bir etkinliğe Kapsamsız değildir. | Sıralı ForEach kullanmayı düşünün veya ForEach içinde yürütme işlem hattını kullanın (alt işlem hattında işlenen değişken/parametre).|
| | |

## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün: 

- [İşlem hattı yürütme etkinliği](control-flow-execute-pipeline-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web Etkinliği](control-flow-web-activity.md)
