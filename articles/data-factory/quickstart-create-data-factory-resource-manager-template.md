---
title: Kaynak Yöneticisi şablonu kullanarak Azure Veri Fabrikası oluşturma
description: Bu öğreticide, bir Azure Resource Manager şablonu kullanarak örnek bir Azure Data Factory işlem hattı oluşturacaksınız.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/20/2019
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: 6bba764060c243417711205083bf02f84a68fed1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684516"
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Öğretici: Azure Resource Manager şablonu kullanarak Azure veri fabrikası oluşturma

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Geçerli sürüm](quickstart-create-data-factory-resource-manager-template.md)

Bu hızlı başlangıçta, Azure Resource Manager şablonu kullanarak bir Azure veri fabrikasını nasıl oluşturacağınız ve izleyeceğiniz açıklanmaktadır. Bu veri fabrikasında oluşturduğunuz işlem hattı, verileri Azure blob depolama alanındaki bir klasörden başka bir klasöre **kopyalar**. Azure Data Factory kullanarak verileri **dönüştürme** hakkında bir öğretici için bkz. [Öğretici: Spark kullanarak verileri dönüştürme](transform-data-using-spark.md).

> [!NOTE]
> Bu makale, Data Factory hizmetine ayrıntılı giriş bilgileri sağlamaz. Azure Data Factory hizmetine giriş bilgileri için bkz. [Azure Data Factory'ye giriş](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) konusundaki yönergeleri izleyerek en güncel Azure PowerShell modüllerini yükleyin.

## <a name="resource-manager-templates"></a>Resource Manager şablonları

Azure Resource Manager şablonları hakkında genel bir bilgi almak için bkz. [Azure Resource Manager Şablonları Yazma](../azure-resource-manager/resource-group-authoring-templates.md).

Aşağıdaki bölümde, öğreticiyi hızlıca geçip şablonu test etmeniz için Data Factory varlıklarını tanımlamaya yönelik tam bir Resource Manager şablonu verilmektedir. Her bir Data Factory varlığının nasıl tanımlandığını anlamak için [Şablondaki Data Factory varlıkları](#data-factory-entities-in-the-template) bölümüne bakın.

Bir şablondaki Data Factory kaynakların JSON sözdizimi ve özellikleri hakkında bilgi edinmek için bkz. [Microsoft. DataFactory kaynak türleri](/azure/templates/microsoft.datafactory/allversions).

## <a name="data-factory-json"></a>Data Factory JSON

**C:\adföğreticisi** klasöründe **ADFTutorialARM. JSON** adlı bir JSON dosyası oluşturun (henüz yoksa adföğreticisi klasörünü oluşturun) ve aşağıdaki içeriği oluşturun:

```json
{  
    "$schema":"http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "type":"string",
            "metadata":"Data Factory Name"
        },
        "dataFactoryLocation":{  
            "type":"string",
            "defaultValue":"East US",
            "metadata":{  
                "description":"Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName":{  
            "type":"string",
            "metadata":{  
                "description":"Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey":{  
            "type":"securestring",
            "metadata":{  
                "description":"Key for the Azure storage account."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },      
    "variables":{  
        "factoryId":"[concat('Microsoft.DataFactory/factories/', parameters('dataFactoryName'))]"
    },
    "resources":[  
        {  
            "name":"[parameters('dataFactoryName')]",
            "apiVersion":"2018-06-01",
            "type":"Microsoft.DataFactory/factories",
            "location":"[parameters('dataFactoryLocation')]",
            "identity":{  
                "type":"SystemAssigned"
            },
            "resources":[  
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
                    "type":"Microsoft.DataFactory/factories/linkedServices",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "type":"AzureBlobStorage",
                        "typeProperties":{  
                            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "fileName":"emp.txt",
                                "folderPath":"input",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
                    "type":"Microsoft.DataFactory/factories/datasets",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "linkedServiceName":{  
                            "referenceName":"ArmtemplateStorageLinkedService",
                            "type":"LinkedServiceReference"
                        },
                        "annotations":[  

                        ],
                        "type":"Binary",
                        "typeProperties":{  
                            "location":{  
                                "type":"AzureBlobStorageLocation",
                                "folderPath":"output",
                                "container":"adftutorial"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
                    "type":"Microsoft.DataFactory/factories/pipelines",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "activities":[  
                            {  
                                "name":"MyCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  

                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  

                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"BinarySource",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageReadSettings",
                                            "recursive":true
                                        }
                                    },
                                    "sink":{  
                                        "type":"BinarySink",
                                        "storeSettings":{  
                                            "type":"AzureBlobStorageWriteSettings"
                                        }
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetIn",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"ArmtemplateTestDatasetOut",
                                        "type":"DatasetReference",
                                        "parameters":{  

                                        }
                                    }
                                ]
                            }
                        ],
                        "annotations":[  

                        ]
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
                        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
                    ]
                },
                {  
                    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
                    "type":"Microsoft.DataFactory/factories/triggers",
                    "apiVersion":"2018-06-01",
                    "properties":{  
                        "annotations":[  

                        ],
                        "runtimeState":"Started",
                        "pipelines":[  
                            {  
                                "pipelineReference":{  
                                    "referenceName":"ArmtemplateSampleCopyPipeline",
                                    "type":"PipelineReference"
                                },
                                "parameters":{  

                                }
                            }
                        ],
                        "type":"ScheduleTrigger",
                        "typeProperties":{  
                            "recurrence":{  
                                "frequency":"Hour",
                                "interval":1,
                                "startTime":"[parameters('triggerStartTime')]",
                                "endTime":"[parameters('triggerEndTime')]",
                                "timeZone":"UTC"
                            }
                        }
                    },
                    "dependsOn":[  
                        "[parameters('dataFactoryName')]",
                        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
                    ]
                }
            ]
        }
    ]
}
```
## <a name="parameters-json"></a>Parametreler JSON

Azure Resource Manager şablonuna yönelik parametreleri içeren **ADFTutorialARM-Parameters.json** adlı bir JSON dosyası oluşturun.

> [!IMPORTANT]
> - Bu parametre dosyasında **storageAccountName** ve **storageAccountKey** parametreleri için Azure Storage hesabınızın adını ve anahtarını belirtin. Adftutorial kapsayıcısını oluşturdunuz ve örnek dosyayı (emp.txt) bu Azure blob depolama alanındaki giriş klasörüne yüklediniz.
> - **dataFactoryName** parametresi için veri fabrikasına ait genel olarak benzersiz bir ad belirtin. Örneğin: ARMTutorialFactoryJohnDoe11282017.
> - **triggerStartTime** için geçerli tarihi şu biçimde belirtin: `2019-09-08T00:00:00`.
> - **triggerEndTime** için sonraki günü şu biçimde belirtin: `2019-09-09T00:00:00`. Ayrıca, geçerli UTC saatini işaretleyip bitiş saati için sonraki bir veya iki saati belirtebilirsiniz. Örneğin, geçerli UTC saati gece 01:32 ise bitiş tarihi olarak `2019-09-09:03:00:00` değerini belirtin. Bu durumda tetikleyici, işlem hattını iki kez (gece 2 ve 3’te) çalıştırır.

```json
{  
    "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":"1.0.0.0",
    "parameters":{  
        "dataFactoryName":{  
            "value":"<datafactoryname>"
        },
        "dataFactoryLocation":{  
            "value":"East US"
        },
        "storageAccountName":{  
            "value":"<yourstorageaccountname>"
        },
        "storageAccountKey":{  
            "value":"<yourstorageaccountkey>"
        },
        "triggerStartTime":{  
            "value":"2019-09-08T11:00:00"
        },
        "triggerEndTime":{  
            "value":"2019-09-08T14:00:00"
        }
    }
}
```

> [!IMPORTANT]
> Aynı Data Factory JSON şablonuyla birlikte kullanabileceğiniz geliştirme, test ve üretim ortamları için ayrı parametre JSON dosyalarına sahip olabilirsiniz. Bir Power Shell komut dosyası kullanarak, bu ortamlarda Data Factory varlıklarını dağıtmayı otomatik hale getirebilirsiniz.

## <a name="deploy-data-factory-entities"></a>Data Factory varlıklarını dağıtma

PowerShell 'de, bu hızlı başlangıçta daha önce oluşturduğunuz Kaynak Yöneticisi şablonunu kullanarak kaynak grubunuzdaki Data Factory varlıkları dağıtmak için aşağıdaki komutu çalıştırın (Bu durumda, örnek olarak ADFTutorialResourceGroup alın).

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Aşağıdaki örneğe benzer bir çıktı görürsünüz:

```console
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 9/8/2019 10:52:29 AM
Mode                    : Incremental
TemplateLink            : 
Parameters              : 
                          Name                   Type                       Value     
                          =====================  =========================  ==========
                          dataFactoryName        String                     <data factory name>
                          dataFactoryLocation    String                     East US   
                          storageAccountName     String                     <storage account name>
                          storageAccountKey      SecureString                         
                          triggerStartTime       String                     9/8/2019 11:00:00 AM
                          triggerEndTime         String                     9/8/2019 2:00:00 PM
                          
Outputs                 : 
DeploymentDebugLogLevel : 
```

## <a name="start-the-trigger"></a>Tetikleyiciyi başlatma

Şablon aşağıdaki Data Factory varlıklarını dağıtır:

- Azure Storage bağlı hizmeti
- İkili veri kümeleri (giriş ve çıkış)
- Kopyalama etkinliği içeren işlem hattı
- İşlem hattını tetikleyen tetikleyici

Dağıtılan tetikleyici durdurulmuş durumdadır. Tetikleyiciyi başlatma yöntemlerinden biri **Start-AzDataFactoryV2Trigger** PowerShell cmdlet 'ini kullanmaktır. Aşağıdaki yordamda ayrıntılı adımlar verilmektedir:

1. PowerShell penceresinde kaynak grubunun adını tutacak bir değişken oluşturun. Aşağıdaki komutu PowerShell penceresine kopyalayıp ENTER tuşuna basın. New-AzResourceGroupDeployment komutu için farklı bir kaynak grubu adı belirttiyseniz, değeri burada güncelleştirin.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Veri fabrikasının adını tutacak bir değişken oluşturun. ADFTutorialARM-Parameters.json dosyasında belirttiğiniz adın aynısını belirtin.

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Tetikleyicinin adı için bir değişken ayarlayın. Tetikleyicinin adı, Resource Manager şablon dosyasında (ADFTutorialARM.json) sabit kodlanmıştır.

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Veri fabrikanızın ve tetikleyicinin adını belirttikten sonra aşağıdaki PowerShell komutunu çalıştırarak **tetikleyicinin durumunu** alın:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json

    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Tetikleyicinin çalışma zamanı durumunun **Durduruldu** olduğuna dikkat edin.
5. **Tetikleyiciyi başlatın**. Tetikleyici, şablonda tanımlanan işlem hattını belirtilen saatte çalıştırır. Diğer bir deyişle, bu komutu öğleden sonra 2:25’te yürüttüyseniz, tetikleyici işlem hattını ilk kez öğleden sonra 3’te çalıştırır. Sonra, işlem hattını tetikleyici için belirlediğiniz bitiş zamanına kadar saatlik olarak çalıştırır.

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Örnek çıktı aşağıdaki gibidir:
    
    ```console
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Tetikleyiciyi, Get-AzDataFactoryV2Trigger komutunu yeniden çalıştırarak başlatıldığını onaylayın.

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Örnek çıktı aşağıdaki gibidir:
    
    ```console
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFQuickstartsDataFactory0905
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>İşlem hattını izleme

1. [Azure portalında](https://portal.azure.com/) oturum açtıktan sonra **Tüm hizmetler**’e tıklayın, **veri fa** gibi bir anahtar sözcükle arama yapın ve **Veri fabrikaları**’nı seçin.

2. **Veri Fabrikaları** sayfasında, oluşturduğunuz veri fabrikasına tıklayın. Gerekirse, veri fabrikanızın adıyla listeyi filtreleyin.

3. Data Factory sayfasında, **& İzleyicisi** kutucuğunu yaz ' a tıklayın.

4. **Haydi başlayalım** sayfasında, **izleyici sekmesini**seçin.  ![Izleme işlem hattı çalıştırma](media/doc-common-process/get-started-page-monitor-button.png)

    > [!IMPORTANT]
    > İşlem hattının yalnızca saat başı çalıştığını görürsünüz (örneğin: sabah 4, 5, 6 vb.). Zaman sonraki saate ulaştığında listeyi yenilemek için araç çubuğunda **Yenile**’ye tıklayın.

5. **Eylemler** sütunundaki **etkinlik çalıştırmalarını görüntüle** bağlantısına tıklayın.

    ![İşlem hattı eylemleri bağlantısı](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)

6. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görürsünüz. Bu hızlı başlangıçta işlem hattı yalnızca bir etkinlik türü içerir: Kopyalama. Bu nedenle, bu etkinliğe ait bir çalıştırma görürsünüz.

    ![Etkinlik çalıştırmaları](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
7. Eylemler sütununun altındaki **Çıkış** bağlantısına tıklayın. Kopyalama işleminin çıktısını bir **Çıktı** penceresinde görürsünüz. Tam çıktıyı görmek için ekranı kapla düğmesine tıklayın. Ekranı kaplayan çıktı penceresini veya çıktıyı kapatabilirsiniz.

8. Başarılı/başarısız çalıştırma gördüğünüzde tetikleyiciyi durdurun. Tetikleyici, işlem hattını saatte bir kez çalıştırır. İşlem hattı her çalıştırma için aynı dosyayı girdi klasöründen çıktı klasörüne kopyalar. Tetikleyiciyi durdurmak için PowerShell penceresinde aşağıdaki komutu çalıştırın.
    
    ```powershell
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="data-factory-entities-in-the-template"></a> Varlıklar için JSON tanımları

Aşağıdaki Data Factory varlıkları JSON şablonunda tanımlanır:

- [Azure Storage bağlı hizmeti](#azure-storage-linked-service)
- [İkili giriş veri kümesi](#binary-input-dataset)
- [İkili çıkış veri kümesi](#binary-output-dataset)
- [Kopyalama etkinliği içeren bir veri işlem hattı](#data-pipeline)
- [Tetikleyici](#trigger)

#### <a name="azure-storage-linked-service"></a>Azure Storage bağlı hizmeti

AzureStorageLinkedService, Azure depolama hesabınızı veri fabrikasına bağlar. Bir kapsayıcı oluşturup verileri ön koşulların parçası olarak bu depolama hesabına yüklediniz. Bu bölümde Azure depolama hesabınızın adını ve anahtarını belirtirsiniz. Bir Azure Storage bağlı hizmetini tanımlamak için kullanılan JSON özelliklerine ilişkin ayrıntılar için bkz. [Azure Storage bağlı hizmeti](connector-azure-blob-storage.md#linked-service-properties).

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateStorageLinkedService')]",
    "type":"Microsoft.DataFactory/factories/linkedServices",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]"
    ]
}
```

ConnectionString, storageAccountName ve storageAccountKey parametrelerini kullanır. Bu parametrelerin değerleri bir yapılandırma dosyası kullanılarak geçirilir. Tanım ayrıca şablonda tanımlanan değişkenleri kullanır: azureStorageLinkedService ve dataFactoryName.

#### <a name="binary-input-dataset"></a>İkili giriş veri kümesi

Azure depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Ikili veri kümesi tanımında, blob kapsayıcısı, klasör ve girdi verilerini içeren dosyanın adını belirtirsiniz. Ikili veri kümesini tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi için bkz. [ikili veri kümesi özellikleri](format-binary.md#dataset-properties) .

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetIn')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="binary-output-dataset"></a>İkili çıkış veri kümesi

Azure Blob Depolamada girdi klasöründen kopyalanmış verileri tutan klasörün adını belirtin. Ikili veri kümesini tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi için bkz. [ikili veri kümesi özellikleri](format-binary.md#dataset-properties) .

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateTestDatasetOut')]",
    "type":"Microsoft.DataFactory/factories/datasets",
    "apiVersion":"2018-06-01",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"ArmtemplateStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/linkedServices/ArmtemplateStorageLinkedService')]"
    ]
}
```

#### <a name="data-pipeline"></a>Veri işlem hattı

Verileri bir Ikili veri kümesinden başka bir Ikili veri kümesine kopyalayan bir işlem hattı tanımlayın. Bu örnekte bir işlem hattı tanımlamak için kullanılan JSON öğelerinin açıklamaları için bkz. [İşlem Hattı JSON](concepts-pipelines-activities.md#pipeline-json).

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmtemplateSampleCopyPipeline')]",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "apiVersion":"2018-06-01",
    "properties":{  
        "activities":[  
            {  
                "name":"MyCopyActivity",
                "type":"Copy",
                "dependsOn":[  

                ],
                "policy":{  
                    "timeout":"7.00:00:00",
                    "retry":0,
                    "retryIntervalInSeconds":30,
                    "secureOutput":false,
                    "secureInput":false
                },
                "userProperties":[  

                ],
                "typeProperties":{  
                    "source":{  
                        "type":"BinarySource",
                        "storeSettings":{  
                            "type":"AzureBlobStorageReadSettings",
                            "recursive":true
                        }
                    },
                    "sink":{  
                        "type":"BinarySink",
                        "storeSettings":{  
                            "type":"AzureBlobStorageWriteSettings"
                        }
                    },
                    "enableStaging":false
                },
                "inputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetIn",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ],
                "outputs":[  
                    {  
                        "referenceName":"ArmtemplateTestDatasetOut",
                        "type":"DatasetReference",
                        "parameters":{  

                        }
                    }
                ]
            }
        ],
        "annotations":[  

        ]
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetIn')]",
        "[concat(variables('factoryId'), '/datasets/ArmtemplateTestDatasetOut')]"
    ]
}
```

#### <a name="trigger"></a>Tetikleyici

İşlem hattını saatte bir kez çalıştıran bir tetikleyici tanımlayın. Dağıtılan tetikleyici durdurulmuş durumdadır. **Start-AzDataFactoryV2Trigger** cmdlet 'ini kullanarak tetikleyiciyi başlatın. Tetikleyiciler hakkında daha fazla bilgi için [İşlem hattı yürütme ve tetikleyicileri](concepts-pipeline-execution-triggers.md#triggers) makalesine bakın.

```json
{  
    "name":"[concat(parameters('dataFactoryName'), '/ArmTemplateTestTrigger')]",
    "type":"Microsoft.DataFactory/factories/triggers",
    "apiVersion":"2018-06-01",
    "properties":{  
        "annotations":[  

        ],
        "runtimeState":"Started",
        "pipelines":[  
            {  
                "pipelineReference":{  
                    "referenceName":"ArmtemplateSampleCopyPipeline",
                    "type":"PipelineReference"
                },
                "parameters":{  

                }
            }
        ],
        "type":"ScheduleTrigger",
        "typeProperties":{  
            "recurrence":{  
                "frequency":"Hour",
                "interval":1,
                "startTime":"[parameters('triggerStartTime')]",
                "endTime":"[parameters('triggerEndTime')]",
                "timeZone":"UTC"
            }
        }
    },
    "dependsOn":[  
        "[parameters('dataFactoryName')]",
        "[concat(variables('factoryId'), '/pipelines/ArmtemplateSampleCopyPipeline')]"
    ]
}
```

## <a name="reuse-the-template"></a>Şablonu yeniden kullanma

Bu öğreticide, Data Factory varlıkları tanımlamaya yönelik bir şablon ve parametrelerin değerlerini geçirmeye yönelik bir şablon oluşturdunuz. Data Factory varlıklarını farklı ortamlara dağıtmak üzere aynı şablonu kullanmak için, her bir ortama yönelik bir parametre dosyası oluşturur ve bu ortama dağıtırken kullanırsınız.

Örnek:

```powershell
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```

Birinci komut geliştirme ortamına, ikinci komut test ortamına ve üçüncü komut üretim ortamına yönelik parametre dosyasını kullanır.

Ayrıca tekrarlanan görevleri gerçekleştirmek için şablonu yeniden kullanabilirsiniz. Örneğin, aynı mantığı uygulamasına karşın her veri fabrikasının farklı Azure depolama hesaplarını kullandığı bir veya daha fazla işlem hattı ile çok sayıda veri fabrikası oluşturun. Bu senaryoda, aynı şablonu veri fabrikaları oluşturmaya yönelik farklı parametre dosyaları ile aynı ortamda (geliştirme, test veya üretim) kullanırsınız.

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekteki işlem hattı, verileri bir konumdan Azure blob depolama alanındaki başka bir konuma kopyalar. Daha fazla senaryoda Data Factory’yi kullanma hakkında bilgi almak için [öğreticileri](tutorial-copy-data-dot-net.md) okuyun.
