---
title: Azure Data Factory etkinlik değiştirme
description: Switch etkinliği, işleme akışını bir koşula göre denetlemenizi sağlar.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 08d93bd3d49dc345b35a942d4d5db4eef937d3de
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786150"
---
# <a name="switch-activity-in-azure-data-factory"></a>Azure Data Factory etkinlik değiştirme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Switch etkinliği, programlama dillerinde bir switch ifadesinin sağladığı işlevleri sağlar. Koşul değerlendirmesiyle eşleşen bir servis talebine karşılık gelen bir etkinlik kümesini değerlendirir.

## <a name="syntax"></a>Syntax

```json

{
   "name": "<Name of the activity>",
    "type": "Switch",
    "typeProperties": {
        "expression": {
            "value": "<expression that evaluates to some string value>",
            "type": "Expression"
        },
        "cases": [
            {
                "value": "<string value that matches expression evaluation>",
                "activities": [
                    {
                        "<Activity 1 definition>"
                    },
                    {
                        "<Activity 2 definition>"
                    },
                    {
                        "<Activity N definition>"
                    }
                ]
            }           
        ],
        "defaultActivities": [
            {
                "<Activity 1 definition>"
            },
            {
                "<Activity 2 definition>"
            },
            {
                "<Activity N definition>"
            }
        ]
    }
}
```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
name | Anahtar etkinliğinin adı. | Dize | Yes
tür | *Switch* olarak ayarlanmalıdır* | Dize | Yes
ifade | Dize değeri değerlendirmesi gereken ifade | Sonuç türü dize olan ifade | Yes
çalışmaların | Değer ifade değerlendirmesiyle eşleştiğinde yürütülecek bir değer ve bir etkinlik kümesi içeren servis talepleri kümesi. En az bir örnek sağlanmalıdır. 25 servis talebi için maksimum sınır vardır. | Case nesneleri dizisi | Yes
Defaultacmize | İfade değerlendirmesi karşılanmadığı zaman yürütülen etkinlik kümesi. | Etkinlik dizisi | Yes

## <a name="example"></a>Örnek

Bu örnekteki işlem hattı, verileri bir giriş klasöründen çıkış klasörüne kopyalar. Çıkış klasörü, işlem hattı parametresinin değeri tarafından belirlenir: routeSelection.

> [!NOTE]
> Bu bölüm, işlem hattını çalıştırmak için JSON tanımları ve örnek PowerShell komutları sağlar. Azure PowerShell ve JSON tanımlarını kullanarak Data Factory işlem hattı oluşturmaya yönelik adım adım yönergeler için bkz. [öğretici: Azure PowerShell kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-switch-activity-adfv2quickstartpipelinejson"></a>Switch etkinliğine sahip işlem hattı (Adfv2QuickStartPipeline.js)

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "MySwitch",
                "type": "Switch",
                "typeProperties": {
                    "expression": {
                        "value": "@pipeline().parameters.routeSelection",
                        "type": "Expression"
                    },
                    "cases": [
                        {
                            "value": "1",
                            "activities": [
                                {
                                    "name": "CopyFromBlobToBlob1",
                                    "type": "Copy",
                                    "inputs": [
                                        {
                                            "referenceName": "BlobDataset",
                                            "parameters": {
                                                "path": "@pipeline().parameters.inputPath"
                                            },
                                            "type": "DatasetReference"
                                        }
                                    ],
                                    "outputs": [
                                        {
                                            "referenceName": "BlobDataset",
                                            "parameters": {
                                                "path": "@pipeline().parameters.outputPath1",
                                            },
                                            "type": "DatasetReference"
                                        }
                                    ],
                                    "typeProperties": {
                                        "source": {
                                            "type": "BlobSource"
                                        },
                                        "sink": {
                                            "type": "BlobSink"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "value": "2",    
                                "activities": [
                                    {
                                        "name": "CopyFromBlobToBlob2",
                                        "type": "Copy",
                                        "inputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.inputPath",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.outputPath2",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "typeProperties": {
                                            "source": {
                                                "type": "BlobSource"
                                            },
                                            "sink": {
                                                "type": "BlobSink"
                                            }
                                        }
                                    }
                                ]
                            },
                            {
                                "value": "3",
                                "activities": [
                                    {
                                        "name": "CopyFromBlobToBlob3",
                                        "type": "Copy",
                                        "inputs": [
                                                {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.inputPath",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "referenceName": "BlobDataset",
                                                "parameters": {
                                                    "path": "@pipeline().parameters.outputPath3",
                                                },
                                                "type": "DatasetReference"
                                            }
                                        ],
                                        "typeProperties": {
                                            "source": {
                                                "type": "BlobSource"
                                            },
                                            "sink": {
                                                "type": "BlobSink"
                                            }
                                        }
                                    }
                                ]
                        },
                    ],
                    "defaultActivities": []
                }                    
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath1": {
                "type": "String"
            },
            "outputPath2": {
                "type": "String"
            },
            "outputPath3": {
                "type": "String"
            },
            "routeSelection": {
                "type": "String"
            }
        }
    }
}

```

### <a name="azure-storage-linked-service-azurestoragelinkedservicejson"></a>Azure depolama bağlı hizmeti (AzureStorageLinkedService.js)

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<Azure Storage account name>;AccountKey=<Azure Storage account key>"
        }
    }
}
```

### <a name="parameterized-azure-blob-dataset-blobdatasetjson"></a>Parametreli Azure blob veri kümesi (BlobDataset.js)

İşlem hattı **FolderPath** değerini, Işlem hattının **OutputPath1** ya da **outputPath2** parametresinin değerine ayarlar. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="pipeline-parameter-json-pipelineparametersjson"></a>Ardışık düzen parametresi JSON (PipelineParameters.json)

```json
{
    "inputPath": "adftutorial/input",
    "outputPath1": "adftutorial/outputCase1",
    "outputPath2": "adftutorial/outputCase2",
    "outputPath2": "adftutorial/outputCase3",
    "routeSelection": "1"
}
```

### <a name="powershell-commands"></a>PowerShell komutları

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu komutlar JSON dosyalarını şu klasöre kaydettiğiniz varsayılmaktadır: C:\ADF. 

```powershell
Connect-AzAccount
Select-AzSubscription "<Your subscription name>"

$resourceGroupName = "<Resource Group Name>"
$dataFactoryName = "<Data Factory Name. Must be globally unique>";
Remove-AzDataFactoryV2 $dataFactoryName -ResourceGroupName $resourceGroupName -force


Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile "C:\ADF\AzureStorageLinkedService.json"
Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile "C:\ADF\BlobDataset.json"
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile "C:\ADF\Adfv2QuickStartPipeline.json"
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile C:\ADF\PipelineParameters.json
while ($True) {
    $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

    if ($run) {
        if ($run.Status -ne 'InProgress') {
            Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
            $run
            break
        }
        Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
    }

    Start-Sleep -Seconds 30
}
Write-Host "Activity run details:" -foregroundcolor "Yellow"
$result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
$result

Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

## <a name="next-steps"></a>Sonraki adımlar

Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün: 

- [If Condition Etkinliği](control-flow-if-condition-activity.md)
- [İşlem hattı yürütme etkinliği](control-flow-execute-pipeline-activity.md)
- [Her Bir Etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
- [Web Etkinliği](control-flow-web-activity.md)
