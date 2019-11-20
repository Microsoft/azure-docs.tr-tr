---
title: 'Azure Data Factory kullanarak verileri toplu olarak kopyalama '
description: Azure Data Factory ve Kopyalama Etkinliği’ni kullanarak bir kaynak veri deposundan hedef veri deposuna toplu veri kopyalama hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 5d695c7a74945fd68591360864e107aadc826240
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683690"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Azure Data Factory kullanarak birden çok tabloyu toplu olarak kopyalama
Bu öğreticide **Azure SQL Veritabanından Azure SQL Veri Ambarı'na birkaç tabloyu kopyalama** işlemi gösterilmektedir. Aynı düzeni diğer kopyalama senaryolarında da uygulayabilirsiniz. Örneğin, SQL Server/Oracle’dan Azure SQL Veritabanı/Veri Ambarı/Azure Blob’a tablo kopyalama, Blob’dan Azure SQL Veritabanı tablolarına farklı yollar kopyalama.

Yüksek düzeyde, bu öğretici aşağıdaki adımları içerir:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure SQL Veritabanı, Azure SQL Veri Ambarı ve Azure Depolama bağlı hizmetleri oluşturma.
> * Azure SQL Veritabanı ve Azure SQL Veri Ambarı veri kümeleri oluşturma.
> * Kopyalanacak tabloları aramak için bir işlem hattı ve gerçek kopyalama işlemini gerçekleştirmek için başka bir işlem hattı oluşturma. 
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Bu öğretici Azure PowerShell kullanır. Veri fabrikası oluşturmaya yönelik diğer araçlar/SDK’lar hakkında bilgi edinmek için bkz. [Hızlı Başlangıçlar](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Uçtan uca iş akışı
Bu senaryoda, Azure SQL Veritabanında SQL Veri Ambarı’na kopyalamak istediğimiz birkaç tablo vardır. İş akışının işlem hatlarında gerçekleşen adımlarının mantıksal sırası şöyledir:

![İş akışı](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* İlk işlem hattı, havuz veri depolarına kopyalanması gereken tabloların listesini arar.  Alternatif olarak, havuz veri deposuna kopyalanacak tüm tabloları listeleyen bir meta veri tablosu tutabilirsiniz. İşlem hattı daha sonra veritabanındaki her bir tabloda yinelenen ve veri kopyalama işlemini gerçekleştiren başka bir işlem hattını tetikler.
* İkinci işlem hattı gerçek kopyalama işlemini gerçekleştirir. Tablo listesini bir parametre olarak alır. Listedeki her tablo için, en iyi performansı elde etmek üzere [Blob depolama ve PolyBase yoluyla hazırlanan kopyayı](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) kullanarak Azure SQL Veritabanındaki ilgili tabloyu SQL Veri Ambarında karşılık gelen tabloya kopyalayın. Bu örnekte, ilk işlem hattı tablo listesini bir parametre değeri olarak geçirir. 

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) bölümündeki yönergeleri izleyin.
* **Azure Depolama hesabı**. Azure Depolama hesabı, toplu kopyalama işleminde hazırlama blob depolama alanı olarak kullanılır. 
* **Azure SQL Veritabanı**. Bu veritabanı, kaynak verileri içerir. 
* **Azure SQL Veri Ambarı**. Bu veri ambarı, SQL Veritabanından kopyalanan verileri tutar. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>SQL Veritabanı ve SQL Veri Ambarı’nı hazırlama

**Kaynak Azure SQL Veritabanı’nı hazırlama**:

[Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md) makalesini izleyerek Adventure Works LT örnek verileriyle bir Azure SQL Veritabanı oluşturun. Bu öğretici, bu örnek veritabanındaki tüm tabloları SQL ambarına kopyalar.

**Havuz Azure SQL Veri Ambarını hazırlama**:

1. Azure SQL Veri Ambarınız yoksa, oluşturma adımları için [Azure SQL Veri Ambarı oluşturma](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) makalesine bakın.

2. SQL Veri Ambarında karşılık gelen tablo şemalarını oluşturun. Azure SQL Veritabanından Azure SQL Veri Ambarına [şema geçirmek](https://www.microsoft.com/download/details.aspx?id=49100) için **Geçiş Aracı**’nı kullanabilirsiniz. Daha sonraki bir adımda verileri geçirmek/kopyalamak için Azure Data Factory’yi kullanın.

## <a name="azure-services-to-access-sql-server"></a>SQL sunucusuna erişime yönelik Azure hizmetleri

Hem SQL Veritabanı hem de SQL Veri Ambarı için Azure hizmetlerinin SQL sunucusuna erişmesine izin verin. **Azure hizmetlerine erişime izin ver** ayarının Azure SQL sunucusunda **AÇIK** olduğundan emin olun. Bu ayar, Data Factory hizmetinin Azure SQL Veritabanınızdan verileri okumasına ve Azure SQL Veri Ambarına veri yazmasına olanak tanır. Bu ayarı doğrulamak ve etkinleştirmek için aşağıdaki adımları uygulayın:

1. Soldaki **Tüm hizmetler**’e ve sonra **SQL sunucuları**’na tıklayın.
2. Sunucunuzu seçin ve **AYARLAR** altındaki **Güvenlik Duvarı**’na tıklayın.
3. **Güvenlik Duvarı ayarları** sayfasında **Azure hizmetlerine erişime izin ver** için **AÇIK**’a tıklayın.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **PowerShell**’i başlatın. Bu öğreticide sonuna kadar Azure PowerShell’i açık tutun. Kapatıp yeniden açarsanız komutları yeniden çalıştırmanız gerekir.

    Aşağıdaki komutu çalıştırın ve Azure portalda oturum açmak için kullandığınız kullanıcı adı ve parolayı girin:
        
    ```powershell
    Connect-AzAccount
    ```
    Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzSubscription
    ```
    Çalışmak isteğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. **SubscriptionId**’yi Azure aboneliğinizin kimliği ile değiştirin:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Bir veri fabrikası oluşturmak için **set-AzDataFactoryV2** cmdlet 'ini çalıştırın. Komutu yürütmeden önce yer tutucuları kendi değerlerinizle değiştirin. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Aşağıdaki noktalara dikkat edin:

    * Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Aşağıdaki hata iletisini alırsanız adı değiştirip yeniden deneyin.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory örnekleri oluşturmak için Azure aboneliğinde Katkıda Bulunan veya Yönetici rolünüz olmalıdır.
    * Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma

Bu öğreticide sırasıyla kaynak, havuz ve hazırlama blob’u için veri depolarınızla bağlantılar içeren üç bağlı hizmet oluşturacaksınız:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Kaynak Azure SQL Veritabanı bağlı hizmetini oluşturma

1. **C:\ADFv2TutorialBulkCopy** klasöründe şu içeriğe sahip **AzureSqlDatabaseLinkedService.json** adlı bir JSON dosyası oluşturun: (Henüz yoksa ADFv2TutorialBulkCopy adlı bir klasör oluşturun.)

    > [!IMPORTANT]
    > &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; ve &lt;password&gt; sözcüklerini Azure SQL Veritabanınızın değerleriyle değiştirin.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. **Azure PowerShell**’de **ADFv2TutorialBulkCopy** klasörüne geçin.

3. Şu bağlı hizmeti oluşturmak için **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın: **Azuressqldatabaselinkedservice**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Havuz Azure SQL Veri Ambarı bağlı hizmetini oluşturma

1. **C:\ADFv2TutorialBulkCopy** klasöründe aşağıdaki içeriğe sahip **AzureSqlDWLinkedService.json** adlı bir JSON dosyası oluşturun:

    > [!IMPORTANT]
    > &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; ve &lt;password&gt; sözcüklerini Azure SQL Veritabanınızın değerleriyle değiştirin.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. , **Azuresqldwlinkedservice**bağlı hizmetini oluşturmak için **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Hazırlama Azure Depolama bağlı hizmetini oluşturma

Bu öğreticide Azure Blob depolamayı daha iyi bir kopyalama performansı için PolyBase’i etkinleştiren geçici bir hazırlama alanı olarak kullanırsınız.

1. **C:\ADFv2TutorialBulkCopy** klasöründe aşağıdaki içeriğe sahip **AzureStorageLinkedService.json** adlı bir JSON dosyası oluşturun:

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce &lt;accountName&gt; ve &lt;accountKey&gt; değerlerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Bağlı hizmeti oluşturmak için: **AzureStorageLinkedService**, **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın.

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

## <a name="create-datasets"></a>Veri kümeleri oluşturun

Bu öğreticide, verilerin depolandığı konumu belirten kaynak ve havuz veri kümelerini oluşturacaksınız:

### <a name="create-a-dataset-for-source-sql-database"></a>Kaynak SQL Veritabanı için veri kümesi oluşturma

1. **C:\ADFv2TutorialBulkCopy** klasöründe aşağıdaki içeriğe sahip **AzureSqlDatabaseDataset.json** adlı bir JSON dosyası oluşturun. Daha sonra verileri almak için kopyalama etkinliğindeki SQL sorgusunu kullandığınızdan, "tableName" değeri etkisizdir.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. **Azuressqldatabasedataset**veri kümesini oluşturmak için **set-AzDataFactoryV2Dataset** cmdlet 'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Havuz SQL Veri Ambarı için veri kümesi oluşturma

1. **C:\ADFv2TutorialBulkCopy** klasöründe aşağıdaki içeriğe sahip **AzureSqlDWDataset.json** adlı bir JSON dosyası oluşturun: "tableName" bir parametre olarak ayarlanır; daha sonra bu veri kümesine başvuran kopyalama etkinliği gerçek değeri veri kümesine geçirir.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. **Azuresqldwdataset**veri kümesini oluşturmak için **set-AzDataFactoryV2Dataset** cmdlet 'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>İşlem hattı oluşturma

Bu öğreticide, iki işlem hattı oluşturacaksınız:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>"IterateAndCopySQLTables" işlem hattını oluşturma

Bu işlem hattı parametre olarak tablo listesini alır. Listedeki her bir tablo için verileri hazırlanmış kopya ve PolyBase kullanarak Azure SQL Veritabanından Azure SQL Veri Ambarına kopyalar.

1. **C:\ADFv2TutorialBulkCopy** klasöründe aşağıdaki içeriğe sahip **IterateAndCopySQLTables.json** adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from SQL database to SQL DW",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. **Iterateandcopysqltables**işlem hattını oluşturmak için **set-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>"GetTableListAndTriggerCopyData" işlem hattını oluşturma

Bu işlem hattı iki adım gerçekleştirir:

* Kopyalanacak tabloların listesini almak için Azure SQL Veritabanı sistem tablosunu arar.
* Gerçek veri kopyalamayı yapmak için "IterateAndCopySQLTables" işlem hattını tetikler.

1. **C:\ADFv2TutorialBulkCopy** klasöründe aşağıdaki içeriğe sahip **GetTableListAndTriggerCopyData.json** adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
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

2. **Gettablelistandtriggercopydata**işlem hattını oluşturmak için **set-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```json
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>İşlem hattı çalıştırmasını başlatma ve izleme

1. Ana "GetTableListAndTriggerCopyData" işlem hattı için bir işlem hattı çalıştırması başlatın ve gelecekte izlemek üzere işlem hattı çalıştırma kimliğini yakalayın. Altında, "IterateAndCopySQLTables" işlem hattının çalıştırmasını ExecutePipeline etkinliğinde belirtilen şekilde tetikler.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  **GetTableListAndTriggerCopyData** işlem hattının durumunu sürekli olarak denetlemek için aşağıdaki betiği çalıştırın ve son işlem hattı çalıştırma ve etkinlik çalıştırma sonucunun çıktısını alın.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Örnek çalıştırmanın çıktısı aşağıdaki gibidir:

    ```json
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. "**IterateAndCopySQLTables**" işlem hattının çalıştırma kimliğini alabilir ve ayrıntılı etkinlik çalıştırma sonucunu aşağıdaki gibi denetleyebilirsiniz.

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Örnek çalıştırmanın çıktısı aşağıdaki gibidir:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Havuz Azure SQL Veri Ambarınıza bağlanın ve verilerin Azure SQL Veritabanından düzgün şekilde kopyalandığını onaylayın.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure SQL Veritabanı, Azure SQL Veri Ambarı ve Azure Depolama bağlı hizmetleri oluşturma.
> * Azure SQL Veritabanı ve Azure SQL Veri Ambarı veri kümeleri oluşturma.
> * Kopyalanacak tabloları aramak için bir işlem hattı ve gerçek kopyalama işlemini gerçekleştirmek için başka bir işlem hattı oluşturma. 
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Kaynaktan hedefe verileri artımlı olarak koppyalama hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:
> [!div class="nextstepaction"]
>[Veri artımlı olarak kopyalama](tutorial-incremental-copy-powershell.md)
