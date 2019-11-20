---
title: Azure Data Factory etkinliği silme
description: Çeşitli dosya depolarındaki dosyaları Azure Data Factory silme etkinliğiyle silme hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 8efbf5cd6003781450afe70b8019b39d75290546
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675632"
---
# <a name="delete-activity-in-azure-data-factory"></a>Azure Data Factory etkinliği silme

Şirket içi depolama mağazalarındaki veya bulut depolama mağazalarındaki dosyaları veya klasörleri silmek için Azure Data Factory silme etkinliğini kullanabilirsiniz. Artık gerekli olmadığında dosyaları temizlemek veya arşivlemek için bu etkinliği kullanın.

> [!WARNING]
> Silinen dosyalar veya klasörler geri yüklenemez. Dosya veya klasörleri silmek için Silme etkinliğini kullanırken dikkatli olun.

## <a name="best-practices"></a>En iyi uygulamalar

Silme etkinliğini kullanmaya yönelik bazı öneriler aşağıda verilmiştir:

-   Bunları gelecekte geri yüklemeniz gerekiyorsa, silme etkinliği ile silmeden önce dosyalarınızı yedekleyin.

-   Data Factory, depolama deposundan klasörleri veya dosyaları silmek için yazma izinlerine sahip olduğundan emin olun.

-   Yazılmakta olan dosyaları aynı anda sildiğinizden emin olun. 

-   Şirket içi bir sistemden dosya veya klasör silmek istiyorsanız, 3,14 'den büyük bir sürüme sahip şirket içinde barındırılan bir tümleştirme çalışma zamanı kullandığınızdan emin olun.

## <a name="supported-data-stores"></a>Desteklenen veri depoları

-   [Azure Blob Depolama](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md)
-   [Azure Dosya Depolama](connector-azure-file-storage.md)

### <a name="file-system-data-stores"></a>Dosya sistemi veri depoları

-   [Dosya Sistemi](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google bulut depolaması](connector-google-cloud-storage.md)

## <a name="syntax"></a>Sözdizimi

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Tür özellikleri

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| Veri kümesi | Hangi dosya veya klasörün silineceğini belirleyen veri kümesi başvurusunu sağlar | Evet |
| öz | Dosyaların alt klasörlerden veya yalnızca belirtilen klasörden yinelemeli olarak silinip silinmediğini belirtir.  | Hayır. Varsayılan değer: `false`. |
| maxConcurrentConnections | Klasör veya dosyaları silmek için aynı anda depolama deposuna bağlanacak bağlantı sayısı.   |  Hayır. Varsayılan değer: `1`. |
| enablelogging | Silinmiş olan klasörü veya dosya adlarını kaydetmeniz gerekip gerekmediğini belirtir. Doğru ise, günlük dosyasını okuyarak silme etkinliğinin davranışlarını izleyebilmeniz için günlük dosyasını kaydetmek üzere bir depolama hesabı sağlamanız gerekir. | Hayır |
| logStorageSettings | Yalnızca EnableLogging = true olduğunda geçerlidir.<br/><br/>Silme etkinliği tarafından silinmiş klasörü veya dosya adlarını içeren günlük dosyasını kaydetmek istediğiniz yerde belirtilebileceği bir depolama özellikleri grubu. | Hayır |
| linkedServiceName | Yalnızca EnableLogging = true olduğunda geçerlidir.<br/><br/>Silme etkinliği tarafından silinmiş klasörü veya dosya adlarını içeren günlük dosyasını depolamak için [Azure depolama](connector-azure-blob-storage.md#linked-service-properties)'nın bağlı hizmeti, [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md#linked-service-properties)veya [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#linked-service-properties) . Dosya silmek için silme etkinliği tarafından kullanılan Integration Runtime aynı türde bir yapılandırılmalıdır. | Hayır |
| Yolun | Yalnızca EnableLogging = true olduğunda geçerlidir.<br/><br/>Günlük dosyasını depolama hesabınıza kaydetme yolu. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | Hayır |

## <a name="monitoring"></a>İzleme

Silme etkinliğinin sonuçlarını görebileceğiniz ve izleyebileceğiniz iki yer vardır: 
-   Sil etkinliğinin çıktısından.
-   Günlük dosyasından.

### <a name="sample-output-of-the-delete-activity"></a>Delete etkinliğinin örnek çıktısı

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Delete etkinliğinin örnek günlük dosyası

| Ad | Kategori | Durum | Hata |
|:--- |:--- |:--- |:--- |
| Test1/yyy. JSON | Dosya | Silme |  |
| Test2/hello789. txt | Dosya | Silme |  |
| Test2/test3/hello000. txt | Dosya | Silme |  |
| Test2/test3/zzz. JSON | Dosya | Silme |  |

## <a name="examples-of-using-the-delete-activity"></a>Delete etkinliğini kullanma örnekleri

### <a name="delete-specific-folders-or-files"></a>Belirli klasörleri veya dosyaları sil

Mağaza aşağıdaki klasör yapısına sahiptir:

Asıl<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8. txt

Artık, klasörü veya dosyaları veri kümesinden ve silme etkinliğinden farklı özellik değeri bileşimine göre silmek için Sil etkinliğini kullanıyorsunuz:

| folderPath (veri kümesi) | Dosya adı (veri kümesi) | özyinelemeli (silme etkinliğinden) | Çıktı |
|:--- |:--- |:--- |:--- |
| Root/Folder_A_2 | NULL | False | Asıl<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4. txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5. csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8. txt |
| Root/Folder_A_2 | NULL | True | Asıl<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4. txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5. csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6. txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7. csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8. txt</strike> |
| Root/Folder_A_2 | *. txt | False | Asıl<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4. txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8. txt |
| Root/Folder_A_2 | *. txt | True | Asıl<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4. txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6. txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8. txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Zaman bölümlenmiş klasörü veya dosyaları düzenli aralıklarla Temizleme

Bölümlenmiş klasör veya dosyaları düzenli aralıklarla temizlemek için bir işlem hattı oluşturabilirsiniz.  Örneğin, klasör yapısı şöyle benzerdir: `/mycontainer/2018/12/14/*.csv`.  Her bir işlem hattı çalıştırmasında hangi klasör veya dosyaların silineceğini belirlemek için, zamanlama tetikleyicisinden ADF sistem değişkeninden yararlanabilirsiniz. 

#### <a name="sample-pipeline"></a>Örnek işlem hattı

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Örnek veri kümesi

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Örnek tetikleyici

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>2018.1.1 önce en son değiştirilen dosyaları temizle

Veri kümesindeki "LastModified" dosya özniteliği filtresinden yararlanarak eski veya süre dola dosyalarını temizlemek için bir işlem hattı oluşturabilirsiniz.  

#### <a name="sample-pipeline"></a>Örnek işlem hattı

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Örnek veri kümesi

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Kopyalama etkinliğini ve silme etkinliğini zincirleyerek dosyaları taşıyın

Bir dosyayı kopyalamak için kopyalama etkinliği kullanarak bir dosyayı taşıyabilir ve sonra bir işlem hattındaki dosyayı silebilirsiniz.  Birden çok dosyayı taşımak istediğinizde, aşağıdaki örnekte gösterildiği gibi GetMetadata etkinlik + Filtre etkinliği + ForEach etkinlik + kopyalama etkinliği + sil etkinliğini kullanabilirsiniz:

> [!NOTE]
> Tüm klasörü yalnızca bir klasör yolu içeren bir veri kümesi tanımlayarak ve sonra bir kopyalama etkinliği ve bir klasörü temsil eden aynı veri kümesine başvurmak için silme etkinliği kullanarak taşımak istiyorsanız, çok dikkatli olmanız gerekir. Bunun nedeni, kopyalama işlemi ve silme işlemi arasında klasöre ulaşan yeni dosyalar OLMADıĞıNDAN emin olmanızı sağlar.  Kopyalama etkinliğinizi kopyalama işini tamamlamış ancak silme etkinliği henüz tamamlanmadığında, klasörde klasöre ulaşan yeni dosyalar varsa, DELETE etkinliğinin destinati kopyalanmamış olan bu yeni gelen dosyayı silmesi mümkündür. henüz tüm klasörü silerek. 

#### <a name="sample-pipeline"></a>Örnek işlem hattı

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Örnek veri kümeleri

Dosya listesini numaralandırmak için GetMetadata etkinliği tarafından kullanılan veri kümesi.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Kopyalama etkinliği ve silme etkinliği tarafından kullanılan veri kaynağı için veri kümesi.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Kopyalama etkinliği tarafından kullanılan veri hedefi için veri kümesi.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```

Ayrıca, dosyaları [buradan](solution-template-move-files.md)taşıyacağınız şablonu da alabilirsiniz.

## <a name="known-limitation"></a>Bilinen sınırlama

-   Silme etkinliği joker karakterle açıklanan klasörlerin listesini silmeyi desteklemez.

-   Dosya özniteliği filtresi kullanılırken, silinecek dosyaları seçmek için modifiedDatetimeStart ve modifiedDatetimeEnd ' i, veri kümesinde "fileName": "*" ayarladığınızdan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory dosyaları taşıma hakkında daha fazla bilgi edinin.

-   [Azure Data Factory'deki Veri Kopyalama aracı](copy-data-tool.md)