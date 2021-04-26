---
title: PowerShell kullanarak Değişiklik İzleme kullanarak verileri artımlı olarak kopyalama
description: Bu öğreticide, Delta verilerini bir SQL Server veritabanındaki birden çok tablodan Azure SQL veritabanı 'na artımlı olarak kopyalayan bir Azure Data Factory işlem hattı oluşturacaksınız.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 02/18/2021
ms.openlocfilehash: a31f8ce227175e65f7119c25dcc575dc6fafdcd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727762"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-powershell"></a>PowerShell kullanarak değişiklik izleme bilgilerini kullanarak Azure SQL veritabanından Azure Blob depolama alanına artımlı olarak veri yükleme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, Azure SQL veritabanı 'ndaki kaynak veritabanındaki **değişiklik izleme** bilgilerine göre Delta verileri bir Azure Blob depolama alanına yükleyen bir işlem hattı Ile bir Azure Data Factory oluşturacaksınız.  

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Kaynak veri deposunu hazırlama
> * Veri fabrikası oluşturma.
> * Bağlı hizmet oluşturma.
> * Kaynak, havuz ve değişiklik izleme veri kümelerini oluşturma.
> * Tam kopyalama işlem hattını oluşturma, çalıştırma ve izleme
> * Kaynak tabloya veri ekleme veya bu verileri güncelleştirme
> * Artımlı kopyalama işlem hattını oluşturma, çalıştırma ve izleme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Genel Bakış
İlk veriler yüklendikten sonra verileri artımlı olarak yükleme senaryosu, veri tümleştirme çözümlerinde sıkça kullanılır. Bazı durumlarda, kaynak veri deponuzda belirli bir süre içinde değiştirilen veriler (örneğin, LastModifyTime, CreationTime) kolayca dilimlenebilir. Bazı durumlarda değişiklik verilerini, verileri en son işlediğiniz zamandan açıkça ayırt etmenin bir yolu yoktur. Azure SQL Veritabanı ve SQL Server gibi veri depoları tarafından desteklenen Değişiklik İzleme teknolojisi, değişiklik verilerini belirlemek için kullanılabilir.  Bu öğreticide, Azure SQL Veritabanındaki değişiklik verilerini Azure Blob Depolama’ya artımlı olarak yüklemek amacıyla SQL Değişiklik İzleme teknolojisiyle Azure Data Factory’nin nasıl kullanılacağı açıklanır.  SQL Değişiklik İzleme teknolojisi hakkında daha kesin bilgiler için bkz. [SQL Server’da Değişiklik İzleme](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

## <a name="end-to-end-workflow"></a>Uçtan uca iş akışı
Değişiklik İzleme teknolojisini kullanarak verileri artımlı olarak yüklemek için sık kullanılan uçtan uca iş akışı adımları burada verilmiştir.

> [!NOTE]
> Hem Azure SQL Veritabanı hem de SQL Server, Değişiklik İzleme teknolojisini destekler. Bu öğreticide, Azure SQL Veritabanını kaynak veri deposu olarak kullanılır. Ayrıca, bir SQL Server örneği de kullanabilirsiniz.

1. **Geçmiş verilerin ilk yüklemesi** (bir kez çalıştır):
    1. Azure SQL veritabanı 'nda kaynak veritabanında Değişiklik İzleme teknolojisini etkinleştirin.
    2. Değiştirilen verileri yakalamaya yönelik taban çizgisi olarak veritabanındaki SYS_CHANGE_VERSION başlangıç değerini alır.
    3. Kaynak veritabanından bir Azure Blob depolama alanına tam veri yükleyin.
2. **Değişiklik verilerinin bir zamanlamaya göre artımlı olarak yüklenmesi** (verilerin ilk yüklemesinden sonra düzenli olarak çalıştır):
    1. Eski ve yeni SYS_CHANGE_VERSION değerleri alın.
    2. **sys.change_tracking_tables** yerine **kaynak tablosundaki verilerle** değiştirilen satırların (iki SYS_CHANGE_VERSION değeri arasında) birincil anahtarlarını birleştirerek değişiklik verilerini yükleyin ve ardından değişiklik verilerini hedefe taşıyın.
    3. SYS_CHANGE_VERSION değerini bir sonraki değişiklik yükleme için değiştirin.

## <a name="high-level-solution"></a>Yüksek düzeyli çözüm
Bu öğreticide, aşağıdaki iki işlemi gerçekleştiren iki işlem hattı oluşturursunuz:  

1. **İlk yükleme:** Kaynak veri deposundaki (Azure SQL Veritabanı) tüm verileri hedef veri deposuna (Azure Blob Depolama) kopyalayan bir kopyalama etkinliğine sahip bir işlem hattı oluşturursunuz.

    ![Verilerin tam yüklenmesi](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Artımlı yükleme:** Aşağıdaki etkinliklerle bir işlem hattı oluşturursunuz ve bunu düzenli olarak çalıştırırsınız.
    1. Eski ve yeni SYS_CHANGE_VERSION değerlerini Azure SQL Veritabanı’ndan almak için **iki arama etkinliği** oluşturun ve bunu kopyalama etkinliğine geçirin.
    2. SYS_CHANGE_VERSION değerleri arasındaki eklenen/güncelleştirilen/silinen verileri Azure SQL Veritabanı’ndan Azure Blob Depolama’ya kopyalamak için **bir kopyalama etkinliği** oluşturun.
    3. SYS_CHANGE_VERSION değerini bir sonraki işlem hattı çalıştırmasında güncelleştirmek için **bir saklı yordam etkinliği** oluşturun.

    ![Artımlı yükleme akış diyagramı](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Azure PowerShell. [Azure PowerShell nasıl yükleneceği ve yapılandırılacağı](/powershell/azure/install-Az-ps)konusundaki yönergeleri izleyerek en son Azure PowerShell modüllerini yükler.
* **Azure SQL veritabanı**. Veritabanını **kaynak** veri deposu olarak kullanabilirsiniz. Azure SQL veritabanında bir veritabanınız yoksa, oluşturma adımları için [Azure SQL veritabanı 'nda veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md) makalesine bakın.
* **Azure depolama hesabı**. Blob depolamayı **Havuz** veri deposu olarak kullanırsınız. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../storage/common/storage-account-create.md) makalesine bakın. **adftutorial** adlı bir kapsayıcı oluşturun. 

### <a name="create-a-data-source-table-in-your-database"></a>Veritabanınızda bir veri kaynağı tablosu oluşturma

1. **SQL Server Management Studio** başlatın ve SQL veritabanı 'na bağlanın.
2. **Sunucu Gezgini**’nde **veritabanınıza** sağ tıklayın ve **Yeni Sorgu**’yu seçin.
3. Veri kaynağı deposu olarak adlandırılan bir tablo oluşturmak için aşağıdaki SQL komutunu veritabanınızda çalıştırın `data_source_table` .  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Aşağıdaki SQL sorgusunu çalıştırarak veritabanınızda ve kaynak tabloda (data_source_table) **Değişiklik İzleme** mekanizmasını etkinleştirin:

    > [!NOTE]
    > - &lt;Veritabanınızın adını data_source_table olan &gt; veritabanınızın adıyla değiştirin.
    > - Değiştirilen veriler, geçerli örnekte iki gün boyunca saklanır. Değiştirilen verileri üç günlük veya daha uzun aralıklarla yüklerseniz, bazı değiştirilen veriler dahil edilmez.  Bu durumda CHANGE_RETENTION değerini değiştirip daha büyük bir sayı belirlemelisiniz. Alternatif olarak, değiştirilen verilerin iki gün içinde yüklenmesini de sağlayabilirsiniz. Daha fazla bilgi için bkz. [Veritabanı için değişiklik izlemeyi etkinleştirme](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Yeni bir tablo oluşturun ve aşağıdaki sorguyu çalıştırarak ChangeTracking_version değerini varsayılan değerle depolayın:

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > SQL Veritabanı için değişiklik izlemeyi etkinleştirdikten sonra veriler değiştirilmezse, değişiklik izleme sürümünün değeri 0 olur.
6. Veritabanınızda bir saklı yordam oluşturmak için aşağıdaki sorguyu çalıştırın. İşlem hattı, bu saklı yordamı, bir önceki adımda oluşturduğunuz tablodaki değişiklik izleme sürümünü güncelleştirmeye çağırır.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

    UPDATE table_store_ChangeTracking_version
    SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) konusundaki yönergeleri izleyerek en güncel Azure PowerShell modüllerini yükleyin.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
1. Daha sonra PowerShell komutlarında kullanacağınız kaynak grubu adı için bir değişken tanımlayın. Aşağıdaki komut metnini PowerShell'e kopyalayın [Azure kaynak grubu](../azure-resource-manager/management/overview.md) için çift tırnak içinde bir ad belirtin ve ardından komutu çalıştırın. Örneğin: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$resourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın
2. Veri fabrikasının konumu için bir değişken tanımlayın:

    ```powershell
    $location = "East US"
    ```
3. Azure kaynak grubunu oluşturmak için aşağıdaki komutu çalıştırın:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$resourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın.
3. Veri fabrikasının adı için bir değişken tanımlayın.

    > [!IMPORTANT]
    >  Veri fabrikasının adını genel olarak benzersiz olacak şekilde güncelleştirin.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Veri Fabrikası oluşturmak için aşağıdaki **set-AzDataFactoryV2** cmdlet 'ini çalıştırın:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

Aşağıdaki noktalara dikkat edin:

* Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Aşağıdaki hata iletisini alırsanız adı değiştirip yeniden deneyin.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory örnekleri oluşturmak için, Azure'da oturum açarken kullandığınız kullanıcı hesabı **katkıda bulunan** veya **sahip** rollerinin üyesi ya da bir Azure aboneliğinin **yöneticisi** olmalıdır.
* Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.


## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu bölümde, Azure depolama hesabınıza ve Azure SQL veritabanı ' nda veritabanınıza bağlı hizmetler oluşturacaksınız.

### <a name="create-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturma.
Bu adımda, Azure Depolama Hesabınızı veri fabrikasına bağlarsınız.

1. **:\ADFTutorials\IncCopyChangeTrackingTutorial** klasöründe şu içeriğe sahip **AzureStorageLinkedService.json** adlı bir JSON dosyası oluşturun: (Böyle bir klasör mevcut değilse, bunu oluşturun.) Dosyayı kaydetmeden önce `<accountName>`, `<accountKey>` değerlerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. **Azure PowerShell**' de **C:\ADFTutorials\IncCopyChangeTrackingTutorial** klasörüne geçin.
3. Bağlı hizmeti oluşturmak için **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın: **AzureStorageLinkedService**. Aşağıdaki örnekte, **Resourcegroupname** ve **datafactoryname** parametrelerinin değerlerini geçirirsiniz.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti oluşturun.
Bu adımda, veritabanınızı veri fabrikasına bağlarsınız.

1. **C:\ADFTutorials\IncCopyChangeTrackingTutorial** klasöründe aşağıdaki içeriğe sahip **AzureSQLDatabaseLinkedService.js** adlı bir JSON dosyası oluşturun: dosyayı kaydetmeden önce **&lt; sunucu &gt; &lt; veritabanı adını &gt; , &lt; Kullanıcı KIMLIĞINI &gt; ve &lt; parolayı &gt;** sunucunuzun adı, veritabanınızın adı, Kullanıcı kimliği ve parola ile değiştirin.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. **Azure PowerShell**, **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırarak bağlı hizmeti oluşturun: **azuressqldatabaselinkedservice**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Veri kümeleri oluşturma
Bu adımda veri kaynağını, veri hedefini ve SYS_CHANGE_VERSION depolanacağı yeri temsil eden veri kümeleri oluşturacaksınız.

### <a name="create-a-source-dataset"></a>Kaynak veri kümesi oluşturma
Bu adımda, kaynak verileri temsil etmek için bir veri kümesi oluşturursunuz.

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

2.  Veri kümesini oluşturmak için Set-AzDataFactoryV2Dataset cmdlet 'ini çalıştırın: SourceDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Cmdlet’in örnek çıktısı aşağıdaki gibidir:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Havuz veri kümesi oluşturma
Bu adımda, kaynak veri deposundan kopyalanan verileri temsil etmek için bir veri kümesi oluşturursunuz.

1. Aşağıdaki içeriğe sahip klasörde SinkDataset.json adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
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

    Önkoşulların bir parçası olarak, Azure Blob Depolama’nızda adftutorial kapsayıcısını oluşturursunuz. Henüz yoksa kapsayıcıyı oluşturun (veya) var olan bir kapsayıcının adına ayarlayın. Bu öğreticide, çıkış dosyasının adı şu ifade kullanılarak dinamik biçimde oluşturulur: @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Set-AzDataFactoryV2Dataset cmdlet 'ini çalıştırarak veri kümesini oluşturun: SinkDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Cmdlet’in örnek çıktısı aşağıdaki gibidir:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Değişiklik izleme veri kümesi oluşturma
Bu adımda değişiklik izleme sürümünü depolamak için bir veri kümesi oluşturacaksınız.  

1. Aşağıdaki içeriğe sahip klasörde ChangeTrackingDataset.json adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Önkoşulların bir parçası olarak table_store_ChangeTracking_version tablosunu oluşturursunuz.
2.  Set-AzDataFactoryV2Dataset cmdlet 'ini çalıştırarak veri kümesini oluşturun: ChangeTrackingDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Cmdlet’in örnek çıktısı aşağıdaki gibidir:

    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Tam kopya için işlem hattı oluşturma
Bu adımda, kaynak veri deposundaki (Azure SQL Veritabanı) tüm verileri hedef veri deposuna (Azure Blob Depolama) kopyalayan bir kopyalama etkinliğine sahip bir işlem hattı oluşturursunuz.

1. Şu içeriğe sahip klasörde bir FullCopyPipeline.json adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },

                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. İşlem hattını oluşturmak için Set-AzDataFactoryV2Pipeline cmdlet 'ini çalıştırın: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ```

   Örnek çıktı aşağıdaki gibidir:

   ```console
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```

### <a name="run-the-full-copy-pipeline"></a>Tam kopyalama işlem hattını çalıştırma
**Invoke-AzDataFactoryV2Pipeline** cmdlet 'Ini kullanarak **Fullcopypipeline** işlem hattını çalıştırın.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
```

### <a name="monitor-the-full-copy-pipeline"></a>Tam kopyalama işlem hattını izleme

1. [Azure portalı](https://portal.azure.com)’nda oturum açın.
2. **Tüm hizmetler**’e tıklayın, `data factories` anahtar sözcüğüyle arama yapın ve **Veri fabrikaları** seçeneğini belirleyin.

    ![Veri fabrikaları menüsü](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. Veri fabrikaları listesinde **veri fabrikanızı** arayın ve bunu seçerek Veri fabrikası sayfasını başlatın.

    ![Veri fabrikanızı arama](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. Veri fabrikası sayfasında **İzleme ve Yönetme** kutucuğuna tıklayın.

    ![İzleme ve Yönetme kutucuğu](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. **Veri tümleştirme uygulaması** ayrı bir sekmede başlatılır. Tüm işlem **hattı çalıştırmalarını** ve bunların durumlarını görebilirsiniz. Aşağıdaki örnekte işlem hattı çalıştırmasının durumunun **Başarılı** olarak belirtildiğini görebilirsiniz. **Parametreler** sütunundaki bağlantıya tıklayarak işlem hattına geçirilen parametreleri denetleyebilirsiniz. Bir hata oluştuysa, **Hata** sütununda bir bağlantı görürsünüz. **Eylemler** sütunundaki bağlantıya tıklayın.

    ![Ekran görüntüsü bir veri fabrikası için işlem hattı çalıştırmalarını gösterir.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. **Eylemler** sütunundaki bağlantıya tıkladığınızda, işlem hattına yönelik tüm **eylem çalıştırmalarını** gösteren sayfayı görürsünüz.

    ![Ekran görüntüsü, işlem hatları bağlantısı olan bir veri fabrikası için etkinlik çalıştırmalarını gösterir.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. **İşlem hattı çalıştırmaları** görünümüne dönmek için, resimde gösterildiği gibi **İşlem hatları** seçeneğine tıklayın.


### <a name="review-the-results"></a>Sonuçları gözden geçirin
`adftutorial` kapsayıcısının `incremental-<GUID>.txt` klasöründe `incchgtracking` adlı bir dosya görürsünüz.

![Tam kopyadan çıkış dosyası](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

Dosya, veritabanınızdaki verileri içermelidir:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Kaynak tabloya daha fazla veri ekleme

Bir satır eklemek ve bir satırı güncelleştirmek için aşağıdaki sorguyu veritabanınıza çalıştırın.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>Değişiklik kopyası için bir işlem hattı oluşturma
Bu adımda, aşağıdaki etkinliklerle bir işlem hattı oluşturursunuz ve bunu düzenli olarak çalıştırırsınız. **Arama etkinlikleri**, eski ve yeni SYS_CHANGE_VERSION değerlerini Azure SQL Veritabanı’ndan alır ve bunu kopyalama etkinliğine geçirir. **Kopyalama etkinliği**, SYS_CHANGE_VERSION değerleri arasındaki eklenen/güncelleştirilen/silinen verileri Azure SQL Veritabanı’ndan Azure Blob Depolama’ya kopyalar. **Saklı yordam etkinliği**, SYS_CHANGE_VERSION değerini bir sonraki işlem hattı çalıştırmasında güncelleştirir.

1. Şu içeriğe sahip klasörde bir IncrementalCopyPipeline.json adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupLastChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                        },
                        "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupCurrentChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
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
                            "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupLastChangeTrackingVersionActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupCurrentChangeTrackingVersionActivity",
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
                    "name": "StoredProceduretoUpdateChangeTrackingActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "Update_ChangeTracking_Version",
                        "storedProcedureParameters": {
                            "CurrentTrackingVersion": {
                                "value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}",
                                "type": "INT64"
                            },
                            "TableName": {
                                "value": "@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}",
                                "type": "String"
                            }
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

2. İşlem hattını oluşturmak için Set-AzDataFactoryV2Pipeline cmdlet 'ini çalıştırın: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Örnek çıktı aşağıdaki gibidir:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>Artımlı kopyalama işlem hattını çalıştırma
**Invoke-AzDataFactoryV2Pipeline** cmdlet 'Ini kullanarak **ıncrementalcopypipeline** ardışık düzenini çalıştırın.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
```


### <a name="monitor-the-incremental-copy-pipeline"></a>Artımlı kopyalama işlem hattını izleme
1. **Veri Tümleştirme Uygulaması**’nda **işlem hattı çalıştırmaları** görünümünü yenileyin. Listede IncrementalCopyPipeline değerini gördüğünüzü onaylayın. **Eylemler** sütunundaki bağlantıya tıklayın.  

    ![Ekran görüntüsü, ardışık düzen dahil bir veri fabrikası için işlem hattı çalıştırmalarını gösterir.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. **Eylemler** sütunundaki bağlantıya tıkladığınızda, işlem hattına yönelik tüm **eylem çalıştırmalarını** gösteren sayfayı görürsünüz.

    ![Ekran görüntüsü, birkaç tane başarıyla işaretlenmiş bir veri fabrikası için işlem hattı çalıştırmalarını gösterir.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. **İşlem hattı çalıştırmaları** görünümüne dönmek için, resimde gösterildiği gibi **İşlem hatları** seçeneğine tıklayın.

### <a name="review-the-results"></a>Sonuçları gözden geçirin
`adftutorial` kapsayıcısının `incchgtracking` klasöründe ikinci dosyayı görürsünüz.

![Artımlı kopyadan çıkış dosyası](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

Dosyanın yalnızca veritabanınızdaki Delta verileri olmalıdır. `U` bulunan kayıt, veritabanındaki güncelleştirilmiş satırdır ve `I` ise eklenen bir satırdır.

```
1,update,10,2,U
6,new,50,1,I
```
İlk üç sütun, data_source_table tablosundaki değiştirilmiş verilerdir. Son iki sütun, değişiklik izleme sistem tablosundaki meta verilerdir. Dördüncü sütun, değiştirilen her satıra ilişkin SYS_CHANGE_VERSION değeridir. Beşinci sütun ise işlemdir: U = güncelleştirme, I = ekleme.  Değişiklik izleme bilgileri hakkında daha fazla ayrıntı için bkz. [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql).

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10            2                                 U
6        new     50            1                                 I
```


## <a name="next-steps"></a>Sonraki adımlar
Yalnızca LastModifiedDate göre yeni ve değiştirilmiş dosyaları kopyalama hakkında bilgi edinmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Yeni dosyaları LastModifiedDate göre Kopyala](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
