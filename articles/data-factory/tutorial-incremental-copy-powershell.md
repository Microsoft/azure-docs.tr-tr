---
title: 'Azure Data Factory kullanarak bir tabloyu artımlı olarak kopyalama '
description: Bu öğreticide, verileri Azure SQL veritabanından Azure Blob depolama alanına artımlı olarak kopyalayan bir Azure veri fabrikası işlem hattı oluşturacaksınız.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: bb1ddbddfb2e06f394ac2f57b10e18893879b51b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683338"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Azure SQL veritabanından Azure Blob depolama alanına verileri artımlı olarak yükleme
Bu öğreticide, Azure SQL veritabanındaki bir tablodan Azure Blob depolama alanına delta veri yükleyen işlem hattına sahip bir Azure veri fabrikası oluşturacaksınız. 

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Eşik değerini depolamak için veri deposunu hazırlama.
> * Veri fabrikası oluşturma.
> * Bağlı hizmet oluşturma. 
> * Kaynak, havuz ve eşik veri kümeleri oluşturun.
> * İşlem hattı oluşturma.
> * İşlem hattını çalıştırma.
> * İşlem hattı çalıştırmasını izleme. 

## <a name="overview"></a>Genel Bakış
Yüksek düzeyli çözüm diyagramı aşağıdaki gibidir: 

![Artımlı olarak veri yükleme](media/tutorial-Incrementally-copy-powershell/incrementally-load.png)

Bu çözümü oluşturmak için önemli adımlar şunlardır: 

1. **Eşit sütununu seçin**.
    Kaynak veri deposunda her çalıştırma için yeni veya güncelleştirilmiş kayıtları dilimlemek için kullanılabilen bir sütun seçin. Normalde, satırlar oluşturulduğunda veya güncelleştirildiğinde seçilen bu sütundaki veriler (örneğin, last_modify_time veya kimlik) artmaya devam eder. Bu sütundaki en büyük değer eşik olarak kullanılır.

2. **Eşik değerini depolamak için veri deposunu hazırlayın**.   
    Bu öğreticide, eşik değerini bir SQL veritabanında depolayacaksınız.
    
3. **Aşağıdaki iş akışı ile bir işlem hattı oluşturun**: 
    
    Bu çözümdeki işlem hattı aşağıdaki etkinlikleri içerir:
  
    * İki Arama etkinliği oluşturun. Son eşik değerini almak için ilk Arama etkinliğini kullanın. Yeni eşik değerini almak için ikinci Arama etkinliğini kullanın. Bu eşik değerleri, Kopyalama etkinliğine geçirilir. 
    * Eşik sütununun değeri eski eşik değerinden büyük ve yeni eşik değerinden küçük olacak şekilde, satırları kaynak veri deposundan kopyalayan bir Kopyalama etkinliği oluşturun. Ardından, delta veriler kaynak veri deposundan Blob depolama alanına yeni bir dosya olarak kopyalanır. 
    * Sonraki seferde çalışan işlem hattı için eşik değerini güncelleştiren bir StoredProcedure etkinliği oluşturun. 


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure SQL Veritabanı**. Veritabanını kaynak veri deposu olarak kullanabilirsiniz. SQL veritabanınız yoksa, oluşturma adımları için bkz. [Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md).
* **Azure Depolama**. Blob depolamayı havuz veri deposu olarak kullanabilirsiniz. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md). adftutorial adlı bir kapsayıcı oluşturun. 
* **Azure PowerShell**. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) bölümündeki yönergeleri izleyin.

### <a name="create-a-data-source-table-in-your-sql-database"></a>SQL veritabanınızda bir veri kaynağı tablosu oluşturma
1. SQL Server Management Studio’yu açın. **Sunucu Gezgini**’nde veritabanına sağ tıklayın ve **Yeni Sorgu**’yu seçin.

2. SQL veritabanınızda aşağıdaki SQL komutunu çalıştırarak veri kaynağı deponuz olarak `data_source_table` adlı bir tablo oluşturun: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Bu öğreticide eşik sütunu olarak LastModifytime kullanılır. Veri kaynağı deposundaki veriler aşağıdaki tabloda gösterilmiştir:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Üst eşik değerini depolamak için SQL veritabanınızda başka bir tablo oluşturma
1. SQL veritabanınızda aşağıdaki SQL komutunu çalıştırarak eşik değerini depolamak için `watermarktable` adlı bir tablo oluşturun:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Üst eşiğin varsayılan değerini kaynak veri deposunun tablo adıyla ayarlayın. Bu öğreticide tablo adı data_source_table şeklindedir.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. `watermarktable` tablosundaki verileri gözden geçirin.
    
    ```sql
    Select * from watermarktable
    ```
    Çıktı: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>SQL veritabanınızda bir saklı yordam oluşturma 

SQL veritabanınızda bir saklı yordam oluşturmak için aşağıdaki komutu çalıştırın:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
1. Daha sonra PowerShell komutlarında kullanacağınız kaynak grubu adı için bir değişken tanımlayın. Aşağıdaki komut metnini PowerShell'e kopyalayın [Azure kaynak grubu](../azure-resource-manager/resource-group-overview.md) için tırnak işaretleri içinde bir ad belirtin ve ardından komutu çalıştırın. `"adfrg"` bunun bir örneğidir. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$resourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın.

2. Veri fabrikasının konumu için bir değişken tanımlayın. 

    ```powershell
    $location = "East US"
    ```
3. Azure kaynak grubunu oluşturmak için aşağıdaki komutu çalıştırın: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$resourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın.

4. Veri fabrikasının adı için bir değişken tanımlayın. 

    > [!IMPORTANT]
    >  Veri fabrikasının adını genel olarak benzersiz olacak şekilde güncelleştirin. Örnek olarak ADFTutorialFactorySP1127 olabilir. 

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Veri Fabrikası oluşturmak için aşağıdaki **set-AzDataFactoryV2** cmdlet 'ini çalıştırın: 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Aşağıdaki noktalara dikkat edin:

* Veri fabrikasının adı genel olarak benzersiz olmalıdır. Aşağıdaki hata iletisini alırsanız adı değiştirip yeniden deneyin:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Data Factory örnekleri oluşturmak için, Azure’da oturum açarken kullandığınız kullanıcı hesabı, katkıda bulunan veya sahip rollerinin üyesi ya da bir Azure aboneliğinin yöneticisi olmalıdır.
* Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Depolama, SQL Veritabanı vb.) ve işlemler (Azure HDInsight vb.) başka bölgelerde olabilir.


## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu bölümde, Depolama ve SQL veritabanı hesabınızla bağlı hizmetler oluşturacaksınız. 

### <a name="create-a-storage-linked-service"></a>Depolama bağlı hizmeti oluşturma
1. C:\ADF klasöründe aşağıdaki içerikle AzureStorageLinkedService.json adlı bir JSON dosyası oluşturun. (Henüz yoksa ADF klasörünü oluşturun.) Dosyayı kaydetmeden önce `<accountName>` ve `<accountKey>` değerini depolama hesabınızın adı ve anahtarıyla değiştirin.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. PowerShell’de ADF klasörüne geçin.

3. AzureStorageLinkedService bağlı hizmetini oluşturmak için **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın. Aşağıdaki örnekte, *ResourceGroupName* ve *DataFactoryName* parametrelerinin değerlerini geçirirsiniz: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-a-sql-database-linked-service"></a>SQL Veritabanı bağlı hizmeti oluşturma
1. C:\ADF klasöründe aşağıdaki içerikle AzureSQLDatabaseLinkedService.json adlı bir JSON dosyası oluşturun. (Henüz yoksa ADF klasörünü oluşturun.) Dosyayı kaydetmeden önce &lt;Server&gt;, &lt;veritabanı&gt;, &lt;Kullanıcı kimliği&gt;ve &lt;Password&gt; sunucunuzun adı, veritabanınız, Kullanıcı KIMLIĞI ve parola ile değiştirin. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. PowerShell’de ADF klasörüne geçin.

3. Azuressqldatabaselinkedservice bağlı hizmetini oluşturmak için **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda, kaynak ve havuz verilerini temsil eden veri kümeleri oluşturacaksınız. 

### <a name="create-a-source-dataset"></a>Kaynak veri kümesi oluşturma

1. Aşağıdaki içeriğe sahip klasörde SourceDataset.json adlı bir JSON dosyası oluşturun: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    Bu öğreticide data_source_table tablo adını kullanırsınız. Farklı ada sahip bir tablo kullanıyorsanız değiştirin.

2. Veri kümesi SourceDataset oluşturmak için **set-AzDataFactoryV2Dataset** cmdlet 'ini çalıştırın.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Cmdlet’in örnek çıktısı aşağıdaki gibidir:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Havuz veri kümesi oluşturma

1. Aşağıdaki içeriğe sahip klasörde SinkDataset.json adlı bir JSON dosyası oluşturun: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
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

    > [!IMPORTANT]
    > Bu kod parçacığı blob depolama hesabınızda adftutorial adlı bir blob kapsayıcıya sahip olduğunuzu varsayar. Henüz yoksa kapsayıcıyı oluşturun veya var olan bir kapsayıcının adına ayarlayın. `incrementalcopy` çıktı klasörü kapsayıcıda mevcut değilse otomatik olarak oluşturulur. Bu öğreticide dosya adı `@CONCAT('Incremental-', pipeline().RunId, '.txt')` ifadesi kullanılarak dinamik olarak oluşturulur.

2. , SinkDataset veri kümesini oluşturmak için **set-AzDataFactoryV2Dataset** cmdlet 'ini çalıştırın.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Cmdlet’in örnek çıktısı aşağıdaki gibidir:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Eşik için veri kümesi oluşturma
Bu adımda üst eşik değerini depolamak için bir veri kümesi oluşturacaksınız. 

1. Aşağıdaki içeriğe sahip klasörde WatermarkDataset.json adlı bir JSON dosyası oluşturun: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  DataSet sulu veri kümesini oluşturmak için **set-AzDataFactoryV2Dataset** cmdlet 'ini çalıştırın.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Cmdlet’in örnek çıktısı aşağıdaki gibidir:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu öğreticide tek işlem hattında zincirlenmiş iki Arama etkinliği, bir Kopyalama etkinliği ve bir StoredProcedure etkinliği ile işlem hattı oluşturacaksınız. 


1. Aynı klasörde aşağıdaki içerikle IncrementalCopyPipeline.json adlı bir JSON dosyası oluşturun: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
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
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
            
        }
    }
    ```
    

2. Incrementalcopypipeline işlem hattını oluşturmak için **set-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Örnek çıktı aşağıdaki gibidir: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>İşlem hattını çalıştırma

1. **Invoke-AzDataFactoryV2Pipeline** cmdlet 'ini kullanarak Işlem hattı ıncrementalcopypipeline çalıştırın. Yer tutucuları kendi kaynak grubu ve veri fabrikası adınızla değiştirin.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ``` 
2. Tüm etkinliklerin başarıyla çalıştığını görene kadar **Get-AzDataFactoryV2ActivityRun** cmdlet 'ini çalıştırarak işlem hattının durumunu denetleyin. Yer tutucuları *RunStartedAfter* ve *RunStartedBefore* parametresi için uygun bulduğunuz süreyle değiştirin. Bu öğreticide *-RunStartedAfter "2017/09/14"* ve *-RunStartedBefore "2017/09/15"* kullanılır.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Örnek çıktı aşağıdaki gibidir:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Sonuçları gözden geçirin

1. Blob depolama (havuz deposu) alanında verilerin SinkDataset içinde tanımlanan dosyaya kopyalandığını görürsünüz. Geçerli öğreticide dosya adı `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt` şeklindedir. Dosyayı açtığınızda, dosyada SQL veritabanındaki verilerle aynı kayıtları görebilirsiniz.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. `watermarktable` içindeki en son değeri denetleyin. Eşik değerinin güncelleştirildiğini görürsünüz.

    ```sql
    Select * from watermarktable
    ```
    
    Örnek çıktı aşağıdaki gibidir:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 2017-09-05 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Delta veri yüklemeyi doğrulamak için veri kaynağı deposuna veri ekleme

1. Yeni verileri SQL veritabanına (veri kaynağı deposu) ekleyin.

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    SQL veritabanında güncelleştirilmiş veriler:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. **Invoke-AzDataFactoryV2Pipeline** cmdlet 'Ini kullanarak ıncrementalcopypipeline işlem hattını yeniden çalıştırın. Yer tutucuları kendi kaynak grubu ve veri fabrikası adınızla değiştirin.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Tüm etkinliklerin başarıyla çalıştığını görene kadar **Get-AzDataFactoryV2ActivityRun** cmdlet 'ini çalıştırarak işlem hattının durumunu denetleyin. Yer tutucuları *RunStartedAfter* ve *RunStartedBefore* parametresi için uygun bulduğunuz süreyle değiştirin. Bu öğreticide *-RunStartedAfter "2017/09/14"* ve *-RunStartedBefore "2017/09/15"* kullanılır.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Örnek çıktı aşağıdaki gibidir:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. Blob depolama alanında başka bir dosyanın oluşturulduğunu görürsünüz. Bu öğreticide yeni dosya adı `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt` şeklindedir. Bu dosyayı açın, içinde 2 satır kaydı göreceksiniz.

5. `watermarktable` içindeki en son değeri denetleyin. Eşik değerinin tekrar güncelleştirildiğini görürsünüz.

    ```sql
    Select * from watermarktable
    ```
    örnek çıktı: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Eşik değerini depolamak için veri deposunu hazırlama. 
> * Veri fabrikası oluşturma.
> * Bağlı hizmet oluşturma. 
> * Kaynak, havuz ve eşik veri kümeleri oluşturun.
> * İşlem hattı oluşturma.
> * İşlem hattını çalıştırma.
> * İşlem hattı çalıştırmasını izleme. 

Bu öğreticide, işlem hattı SQL veritabanındaki tek bir tablodan Blob depolama alanına veri kopyalamıştır. Şirket içi SQL Server veritabanındaki birden çok tablodan SQL veritabanına veri kopyalama hakkında bilgi edinmek için şu öğreticiye ilerleyin. 

> [!div class="nextstepaction"]
>[SQL Server’daki birden fazla tablodan Azure SQL Veritabanı’na artımlı olarak veri yükleme](tutorial-incremental-copy-multiple-tables-powershell.md)



