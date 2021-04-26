---
title: Azure Data Factory kullanarak blob depolamada veri kopyalama
description: Azure Blob depolama alanındaki bir konumdan başka bir konuma veri kopyalamak için PowerShell kullanarak bir Azure Data Factory oluşturun.
author: linda33wj
ms.service: data-factory
ms.devlang: powershell
ms.topic: quickstart
ms.date: 04/10/2020
ms.author: jingwang
ms.openlocfilehash: 9f419d89a9757a11055781335cbf98e9eb651548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372731"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak Azure Data Factory oluşturma

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Güncel sürüm](quickstart-create-data-factory-powershell.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu hızlı başlangıç, PowerShell kullanarak Azure Data Factory oluşturma işlemini açıklar. Bu veri fabrikasında oluşturduğunuz işlem hattı, verileri bir Azure Blob depolama alanındaki bir klasörden başka bir klasöre **kopyalar** . Azure Data Factory kullanarak verileri **dönüştürme** hakkında bir öğretici için bkz. [Öğretici: Spark kullanarak verileri dönüştürme](transform-data-using-spark.md).

> [!NOTE]
> Bu makale, Data Factory hizmetine ayrıntılı giriş bilgileri sağlamaz. Azure Data Factory hizmetine giriş bilgileri için bkz. [Azure Data Factory'ye giriş](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell nasıl yükleneceği ve yapılandırılacağı](/powershell/azure/install-Az-ps)konusundaki yönergeleri izleyerek en son Azure PowerShell modüllerini yükler.

>[!WARNING]
>PowerShell ve Data Factory modülünün en son sürümlerini kullanmıyorsanız, komutları çalıştırırken seriyi kaldırma hatalarıyla karşılaşabilirsiniz. 

#### <a name="log-in-to-powershell"></a>PowerShell’de oturum açın

1. Makinenizde **PowerShell**'i başlatın. Bu hızlı başlangıcın sonuna kadar PowerShell’i açık tutun. Kapatıp yeniden açarsanız, bu komutları yeniden çalıştırmanız gerekir.

2. Aşağıdaki komutu çalıştırın ve Azure Portal'da oturum açmak için kullandığınız aynı Azure kullanıcı adını ve parolasını girin:

    ```powershell
    Connect-AzAccount
    ```

3. Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzSubscription
    ```

4. Hesabınızla ilişkili birden çok aboneliğiniz varsa, birlikte çalışmak istediğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. **SubscriptionId**’yi Azure aboneliğinizin kimliği ile değiştirin:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Daha sonra PowerShell komutlarında kullanacağınız kaynak grubu adı için bir değişken tanımlayın. Aşağıdaki komut metnini PowerShell'e kopyalayın [Azure kaynak grubu](../azure-resource-manager/management/overview.md) için çift tırnak içinde bir ad belirtin ve ardından komutu çalıştırın. Örneğin: `"ADFQuickStartRG"`.

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$ResourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın

2. Azure kaynak grubunu oluşturmak için aşağıdaki komutu çalıştırın:

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$ResourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın.

3. Veri fabrikasının adı için bir değişken tanımlayın. 

    > [!IMPORTANT]
    >  Veri fabrikasının adını genel olarak benzersiz olacak şekilde güncelleştirin. Örneğin, ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. Veri Fabrikası oluşturmak için, $ResGrp değişkeninden location ve ResourceGroupName özelliğini kullanarak aşağıdaki **set-AzDataFactoryV2** cmdlet 'ini çalıştırın:

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

Aşağıdaki noktalara dikkat edin:

* Azure Data Factory adı küresel olarak benzersiz olmalıdır. Aşağıdaki hata iletisini alırsanız adı değiştirip yeniden deneyin.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory örnekleri oluşturmak için, Azure'da oturum açarken kullandığınız kullanıcı hesabı **katkıda bulunan** veya **sahip** rollerinin üyesi ya da bir Azure aboneliğinin **yöneticisi** olmalıdır.

* Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.


## <a name="create-a-linked-service"></a>Bağlı hizmet oluşturma

Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturun. Bu hızlı başlangıçta hem kaynak hem de havuz deposu olarak kullanılan bir Azure Depolama bağlı hizmeti oluşturursunuz. Bağlı hizmetler, Data Factory hizmetinin bunlara bağlanmak için çalışma zamanında kullandığı bağlantı bilgilerini içerir.

>[!TIP]
>Bu hızlı başlangıçta, *hesap anahtarını* veri deponuzu kimlik doğrulama türü olarak kullanacaksınız, ancak desteklenen diğer kimlik doğrulama yöntemlerini seçebilirsiniz: *SAS URI 'Si*,*hizmet sorumlusu* ve gerekirse *yönetilen kimlik* . Ayrıntılar için [Bu makaledeki](./connector-azure-blob-storage.md#linked-service-properties) ilgili bölümlere bakın.
>Veri depolarının gizli dizilerini güvenli bir şekilde depolamak için bir Azure Key Vault kullanılması da önerilir. Ayrıntılı çizimler için [Bu makaleye](./store-credentials-in-key-vault.md) bakın.

1. **C:\ADFv2QuickStartPSH** klasöründe aşağıdaki içeriğe sahip **AzureStorageLinkedService.json** adlı bir JSON dosyası oluşturun: (Henüz yoksa ADFv2QuickStartPSH adlı bir klasör oluşturun.).

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce &lt;accountName&gt; ve &lt;accountKey&gt; değerlerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
    ```

    Not Defteri’ni kullanıyorsanız, **Farklı kaydet** iletişim kutusunda **Farklı kaydetme türü** için **Tüm dosyalar**’ı seçin. Aksi takdirde, dosyaya `.txt` uzantısını ekleyebilir. Örneğin, `AzureStorageLinkedService.json.txt`. Dosyayı Not Defteri’nde açmadan önce Dosya Gezgini’nde oluşturduysanız, **Bilinen dosya türleri için uzantıları gizle** seçeneği varsayılan olarak ayarlandığı için `.txt` uzantısını görmeyebilirsiniz. Sonraki adıma geçmeden önce `.txt` uzantısını kaldırın.

2. **PowerShell**’de **ADFv2QuickStartPSH** klasörüne geçin.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. Bağlı hizmeti oluşturmak için **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın: **AzureStorageLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

## <a name="create-datasets"></a>Veri kümeleri oluşturma

Bu yordamda iki veri kümesi oluşturursunuz: **InputDataset** ve **OutputDataset**. Bu veri kümeleri **binary** türündedir. Bunlar, önceki bölümde oluşturduğunuz Azure Depolama bağlı hizmetine başvurur.
Giriş veri kümesi, giriş klasöründeki kaynak verileri temsil eder. Giriş veri kümesi tanımında, kaynak verileri içeren blob kapsayıcısını (**adftutorial**), klasörü (**input**) ve dosyayı (**emp.txt**) belirtirsiniz.
Çıkış veri kümesi hedefe kopyalanan verileri temsil eder. Çıkış veri kümesi tanımında, verilerin kopyalandığı blob kapsayıcısını (**adftutorial**), klasörü (**output**) ve dosyayı belirtirsiniz. 
1. **C:\ADFv2QuickStartPSH** klasöründe aşağıdaki içeriğe sahip **InputDataset.js** adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "fileName": "emp.txt",
                    "folderPath": "input",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

2. **Inputdataset** veri kümesini oluşturmak için **set-AzDataFactoryV2Dataset** cmdlet 'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "InputDataset" `
        -DefinitionFile ".\InputDataset.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```console
    DatasetName       : InputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```

3. Çıkış veri kümesini oluşturmak için adımları yineleyin. **C:\ADFv2QuickStartPSH** klasöründe aşağıdaki içeriğe sahip **OutputDataset.js** adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "folderPath": "output",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

4. **Outdataset**'i oluşturmak için **set-AzDataFactoryV2Dataset** cmdlet 'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "OutputDataset" `
        -DefinitionFile ".\OutputDataset.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```console
    DatasetName       : OutputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```
## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

Bu yordamda, giriş ve çıkış veri kümelerini kullanan bir kopyalama etkinliğine sahip bir işlem hattı oluşturacaksınız. Kopyalama etkinliği, giriş veri kümesi ayarlarında belirttiğiniz dosyadaki verileri çıkış veri kümesi ayarlarında belirttiğiniz dosyaya kopyalar.  

1. **C:\ADFv2QuickStartPSH** klasöründe aşağıdaki içeriğe sahip **Adfv2QuickStartPipeline.json** adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 30,
                        "secureOutput": false,
                        "secureInput": false
                    },
                    "userProperties": [],
                    "typeProperties": {
                        "source": {
                            "type": "BinarySource",
                            "storeSettings": {
                                "type": "AzureBlobStorageReadSettings",
                                "recursive": true
                            }
                        },
                        "sink": {
                            "type": "BinarySink",
                            "storeSettings": {
                                "type": "AzureBlobStorageWriteSettings"
                            }
                        },
                        "enableStaging": false
                    },
                    "inputs": [
                        {
                            "referenceName": "InputDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ],
            "annotations": []
        }
    }
    ```

2. İşlem hattını oluşturmak için: **Adfv2QuickStartPipeline**, **set-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>İşlem hattı çalıştırması oluşturma

Bu adımda bir işlem hattı çalıştırması oluşturursunuz.

Bir işlem hattı çalıştırması oluşturmak için **Invoke-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın. Cmdlet, gelecekte izlemek üzere işlem hattı çalıştırma kimliğini döndürür.

  ```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline `
    -DataFactoryName $DataFactory.DataFactoryName `
    -ResourceGroupName $ResGrp.ResourceGroupName `
    -PipelineName $DFPipeLine.Name 
```

## <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. İşlem hattı çalıştırma durumunu, verileri kopyalama işlemi tamamlanıncaya kadar sürekli olarak denetlemek için aşağıdaki PowerShell betiğini çalıştırın. Aşağıdaki betiği kopyalayıp PowerShell penceresine yapıştırın ve ENTER tuşuna basın.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ( ($Run.Status -ne "InProgress") -and ($Run.Status -ne "Queued") ) {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output ("Pipeline is running...status: " + $Run.Status)
        }

        Start-Sleep -Seconds 10
    }
    ```

    İşlem hattı çalıştırmasının örnek çıktısı aşağıdaki gibidir:

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    RunId             : 00000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 8/27/2019 7:23:07 AM
    Parameters        : {}
    RunStart          : 8/27/2019 7:22:56 AM
    RunEnd            : 8/27/2019 7:23:07 AM
    DurationInMs      : 11324
    Status            : Succeeded
    Message           : 
    ```
2. Kopyalama etkinliği çalıştırma ayrıntılarını (örneğin, okunan/yazılan verilerin boyutu) almak için aşağıdaki betiği çalıştırın.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Etkinlik çalıştırma sonucunun aşağıdaki örnek çıktısına benzer çıktıyı gördüğünüzü onaylayın:

    ```console
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    ActivityRunId     : 00000000-0000-0000-0000-000000000000
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink, enableStaging}
    Output            : {dataRead, dataWritten, filesRead, filesWritten...}
    LinkedServiceName :
    ActivityRunStart  : 8/27/2019 7:22:58 AM
    ActivityRunEnd    : 8/27/2019 7:23:05 AM
    DurationInMs      : 6828
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 20
    "dataWritten": 20
    "filesRead": 1
    "filesWritten": 1
    "sourcePeakConnections": 1
    "sinkPeakConnections": 1
    "copyDuration": 4
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (Central US)"
    "usedDataIntegrationUnits": 4
    "usedParallelCopies": 1
    "executionDetails": [
      {
        "source": {
          "type": "AzureBlobStorage"
        },
        "sink": {
          "type": "AzureBlobStorage"
        },
        "status": "Succeeded",
        "start": "2019-08-27T07:22:59.1045645Z",
        "duration": 4,
        "usedDataIntegrationUnits": 4,
        "usedParallelCopies": 1,
        "detailedDurations": {
          "queuingDuration": 3,
          "transferDuration": 1
        }
      }
    ]
    
    Activity 'Error' section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "CopyFromBlobToBlob"
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekteki işlem hattı, verileri bir konumdan Azure blob depolama alanındaki başka bir konuma kopyalar. Daha fazla senaryoda Data Factory’yi kullanma hakkında bilgi almak için [öğreticileri](tutorial-copy-data-dot-net.md) okuyun.
