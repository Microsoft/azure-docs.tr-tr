---
title: 'Değişiklik İzleme ve Azure Data Factory kullanarak verileri artımlı olarak kopyalama '
description: 'Bu öğreticide, değişim verileri şirket içi SQL Server veritabanındaki birden çok tablodan Azure SQL veritabanına artımlı olarak kopyalayan bir Azure Data Factory işlem hattı oluşturacaksınız. '
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/12/2018
ms.author: yexu
ms.openlocfilehash: c754b5755cbab3720ca0ffebc891ce76ce7aebc5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683509"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>Değişiklik izleme bilgilerini kullanarak Azure SQL Veritabanından Azure Blob Depolama alanına verileri artımlı olarak yükleme 
Bu öğreticide, kaynak Azure SQL veritabanındaki **değişiklik izleme** bilgilerine dayanan değişiklik verilerini Azure blob depolamasına yükleyen bir işlem hattına sahip olan bir Azure veri fabrikası oluşturursunuz.  

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Kaynak veri deposunu hazırlama
> * Veri fabrikası oluşturma.
> * Bağlı hizmet oluşturma. 
> * Kaynak, havuz ve değişiklik izleme veri kümelerini oluşturma.
> * Tam kopyalama işlem hattını oluşturma, çalıştırma ve izleme
> * Kaynak tabloya veri ekleme veya bu verileri güncelleştirme
> * Artımlı kopyalama işlem hattını oluşturma, çalıştırma ve izleme

## <a name="overview"></a>Genel Bakış
İlk veriler yüklendikten sonra verileri artımlı olarak yükleme senaryosu, veri tümleştirme çözümlerinde sıkça kullanılır. Bazı durumlarda, kaynak veri deponuzda belirli bir süre içinde değiştirilen veriler (örneğin, LastModifyTime, CreationTime) kolayca dilimlenebilir. Bazı durumlarda değişiklik verilerini, verileri en son işlediğiniz zamandan açıkça ayırt etmenin bir yolu yoktur. Azure SQL Veritabanı ve SQL Server gibi veri depoları tarafından desteklenen Değişiklik İzleme teknolojisi, değişiklik verilerini belirlemek için kullanılabilir.  Bu öğreticide, Azure SQL Veritabanındaki değişiklik verilerini Azure Blob Depolama’ya artımlı olarak yüklemek amacıyla SQL Değişiklik İzleme teknolojisiyle Azure Data Factory’nin nasıl kullanılacağı açıklanır.  SQL Değişiklik İzleme teknolojisi hakkında daha kesin bilgiler için bkz. [SQL Server’da Değişiklik İzleme](/sql/relational-databases/track-changes/about-change-tracking-sql-server). 

## <a name="end-to-end-workflow"></a>Uçtan uca iş akışı
Değişiklik İzleme teknolojisini kullanarak verileri artımlı olarak yüklemek için sık kullanılan uçtan uca iş akışı adımları burada verilmiştir.

> [!NOTE]
> Hem Azure SQL Veritabanı hem de SQL Server, Değişiklik İzleme teknolojisini destekler. Bu öğreticide, Azure SQL Veritabanını kaynak veri deposu olarak kullanılır. Dilerseniz şirket içi bir SQL Server kullanabilirsiniz. 

1. **Geçmiş verilerin ilk yüklemesi** (bir kez çalıştır):
    1. Kaynak Azure SQL veritabanında Değişiklik İzleme teknolojisini etkinleştirin.
    2. Değiştirilen verileri yakalamanın temeli olarak Azure SQL veritabanındaki ilk SYS_CHANGE_VERSION değerini alın.
    3. Azure SQL veritabanındaki tüm verileri bir Azure blob depolama alanına yükleyin. 
2. **Değişiklik verilerinin bir zamanlamaya göre artımlı olarak yüklenmesi** (verilerin ilk yüklemesinden sonra düzenli olarak çalıştır):
    1. Eski ve yeni SYS_CHANGE_VERSION değerleri alın.
    3. **sys.change_tracking_tables** yerine **kaynak tablosundaki verilerle** değiştirilen satırların (iki SYS_CHANGE_VERSION değeri arasında) birincil anahtarlarını birleştirerek değişiklik verilerini yükleyin ve ardından değişiklik verilerini hedefe taşıyın.
    4. SYS_CHANGE_VERSION değerini bir sonraki değişiklik yükleme için değiştirin.

## <a name="high-level-solution"></a>Yüksek düzeyli çözüm
Bu öğreticide, aşağıdaki iki işlemi gerçekleştiren iki işlem hattı oluşturursunuz:  

1. **İlk yükleme:** Kaynak veri deposundaki (Azure SQL Veritabanı) tüm verileri hedef veri deposuna (Azure Blob Depolama) kopyalayan bir kopyalama etkinliğine sahip bir işlem hattı oluşturursunuz.

    ![Verilerin tam yüklenmesi](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Artımlı yükleme:** Aşağıdaki etkinliklerle bir işlem hattı oluşturursunuz ve bunu düzenli olarak çalıştırırsınız. 
    1. Eski ve yeni SYS_CHANGE_VERSION değerlerini Azure SQL Veritabanı’ndan almak için **iki arama etkinliği** oluşturun ve bunu kopyalama etkinliğine geçirin.
    2. SYS_CHANGE_VERSION değerleri arasındaki eklenen/güncelleştirilen/silinen verileri Azure SQL Veritabanı’ndan Azure Blob Depolama’ya kopyalamak için **bir kopyalama etkinliği** oluşturun.
    3. SYS_CHANGE_VERSION değerini bir sonraki işlem hattı çalıştırmasında güncelleştirmek için **bir saklı yordam etkinliği** oluşturun.

    ![Artımlı yükleme akış diyagramı](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar
* **Azure SQL Veritabanı**. Veritabanını **kaynak** veri deposu olarak kullanabilirsiniz. Azure SQL Veritabanınız yoksa, oluşturma adımları için [Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md) makalesine bakın.
* **Azure Depolama hesabı**. Blob depolamayı **havuz** veri deposu olarak kullanabilirsiniz. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md) makalesine bakın. **adftutorial** adlı bir kapsayıcı oluşturun. 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Azure SQL veritabanınızda bir veri kaynağı tablosu oluşturma
1. **SQL Server Management Studio**’yu başlatın ve Azure SQL Server'ınıza bağlanın. 
2. **Sunucu Gezgini**’nde **veritabanınıza** sağ tıklayın ve **Yeni Sorgu**’yu seçin.
3. Azure SQL veritabanınızda aşağıdaki SQL komutunu çalıştırarak veri kaynağı deponuz olarak `data_source_table` adlı bir tablo oluşturun.  
    
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
    > - &lt;Veri tabanınızın adını&gt; değiştirerek data_source_table tablosuna sahip olan Azure SQL veritabanınızın adını verin. 
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
6. Azure SQL veritabanınızda bir saklı yordam oluşturmak için aşağıdaki sorguyu çalıştırın. İşlem hattı, bu saklı yordamı, bir önceki adımda oluşturduğunuz tablodaki değişiklik izleme sürümünü güncelleştirmeye çağırır. 

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) konusundaki yönergeleri izleyerek en güncel Azure PowerShell modüllerini yükleyin.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. Sol taraftaki menüden > **kaynak oluştur** ' u seçin **veri ve analiz** > **Data Factory**: 
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. **Yeni veri fabrikası** sayfasında **ad** için **ADFTutorialDataFactory** girin. 
      
     ![Yeni veri fabrikası sayfası](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)
 
   Azure data factory adı **küresel olarak benzersiz** olmalıdır. Aşağıdaki hatayı alırsanız veri fabrikasının adını değiştirin (örneğin adınızADFTutorialDataFactory) ve oluşturmayı yeniden deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin. 
4. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
      - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin. 
      - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
        Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
4. **Sürüm** için **V2 (Önizleme)** öğesini seçin.
5. Data factory için **konum** seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.
6. **Panoya sabitle**’yi seçin.     
7. **Oluştur**'a tıklayın.      
8. Panoda şu kutucuğu ve üzerinde şu durumu görürsünüz: **Veri fabrikası dağıtılıyor**. 

    ![veri fabrikası dağıtılıyor kutucuğu](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. Oluşturma işlemi tamamlandıktan sonra, resimde gösterildiği gibi **Data Factory** sayfasını görürsünüz.
   
   ![Data factory giriş sayfası](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Azure Data Factory kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **Yazar ve İzleyici** kutucuğuna tıklayın.
11. **Başlarken** sayfasında, aşağıdaki resimde gösterildiği gibi sol bölmede bulunan **Düzenle** sekmesine geçin: 

    ![İşlem hattı oluştur düğmesi](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu bölümde, Azure Depolama ve Azure SQL veritabanı hesabınızla bağlı hizmetler oluşturacaksınız. 

### <a name="create-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturun.
Bu adımda, Azure Depolama Hesabınızı veri fabrikasına bağlarsınız.

1. **Bağlantılar**’a ve sonra **+ Yeni**’ye tıklayın.

   ![Yeni bağlantı düğmesi](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. **Yeni Bağlı Hizmet** penceresinde **Azure Blob Depolama**’yı seçip **Devam**’a tıklayın. 

   ![Azure Blob Depolama’yı seçin](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin: 

    1. **Ad** için **AzureStorageLinkedService** adını girin. 
    2. **Depolama hesabı adı** için Azure Depolama hesabınızı seçin. 
    3. **Kaydet** düğmesine tıklayın. 
    
   ![Azure Depolama Hesabı ayarları](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti oluşturun.
Bu adımda, Azure SQL veritabanınızı veri fabrikasına bağlarsınız.

1. **Bağlantılar**’a ve sonra **+ Yeni**’ye tıklayın.
2. **Yeni Bağlı Hizmet** penceresinde **Azure SQL Veritabanı**’nı seçip **Devam**’a tıklayın. 
3. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin: 

    1. **Ad** alanına **AzureSqlDatabaseLinkedService** adını girin. 
    2. **Sunucu adı** alanı için Azure SQL sunucunuzu seçin.
    4. **Veritabanı adı** alanı için Azure SQL veritabanınızı seçin. 
    5. **Kullanıcı adı** alanına kullanıcının adını girin. 
    6. **Parola** alanına kullanıcının parolasını girin. 
    7. Bağlantıyı test etmek için **Bağlantıyı sına**’ya tıklayın.
    8. Bağlı hizmeti kaydetmek için **Kaydet**’e tıklayın. 
    
       ![Azure SQL Veritabanı bağlı hizmet ayarları](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda veri kaynağını, veri hedefini ve SYS_CHANGE_VERSION depolanacağı yeri temsil eden veri kümeleri oluşturacaksınız.

### <a name="create-a-dataset-to-represent-source-data"></a>Kaynak verileri temsil eden bir veri kümesi oluşturma 
Bu adımda, kaynak verileri temsil etmek için bir veri kümesi oluşturursunuz. 

1. Ağaç görünümünde **+ (artı)** seçeneğine ve sonra **Veri Kümesi**’ne tıklayın. 

   ![Yeni Veri Kümesi menüsü](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. **Azure SQL Veritabanı**’nı seçip **Son**’a tıklayın. 

   ![Kaynak veri kümesi türü - Azure SQL Veritabanı](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Veri kümesini yapılandırmak için yeni bir sekme görürsünüz. Ayrıca, veri kümesini ağaç görünümünde de görürsünüz. **Özellikler** penceresinde, veri kümesinin adını **SourceDataset** olarak değiştirin.

   ![Kaynak veri kümesi adı](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. **Bağlantı** sekmesine geçin ve aşağıdaki adımları uygulayın: 
    
    1. **Bağlı hizmet** için **AzureSqlDatabaseLinkedService** hizmetini seçin. 
    2. **Tablo** için **[dbo].[data_source_table]** tablosunu seçin. 

   ![Kaynak bağlantısı](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Havuz veri deposuna kopyalanan verileri temsil eden bir veri kümesi oluşturun. 
Bu adımda, kaynak veri deposundan kopyalanan verileri temsil etmek için bir veri kümesi oluşturursunuz. Önkoşulların bir parçası olarak, Azure Blob Depolama’nızda adftutorial kapsayıcısını oluşturdunuz. Henüz yoksa kapsayıcıyı oluşturun (veya) var olan bir kapsayıcının adına ayarlayın. Bu öğreticide çıktı dosyasının adı `@CONCAT('Incremental-', pipeline().RunId, '.txt')` ifadesi kullanılarak dinamik olarak oluşturulur.

1. Ağaç görünümünde **+ (artı)** seçeneğine ve sonra **Veri Kümesi**’ne tıklayın. 

   ![Yeni Veri Kümesi menüsü](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. **Azure Blob Depolama**’yı seçip **Son**’a tıklayın. 

   ![Havuz veri kümesi türü - Azure Blob Depolama](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Veri kümesini yapılandırmak için yeni bir sekme görürsünüz. Ayrıca, veri kümesini ağaç görünümünde de görürsünüz. **Özellikler** penceresinde, veri kümesinin adını **SinkDataset** olarak değiştirin.

   ![Havuz veri kümesi - ad](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Özellikler penceresinde **Bağlantı** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Bağlı hizmet** için **AzureStorageLinkedService** hizmetini seçin.
    2. **filePath** yolunun **klasör** bölümü için **adftutorial/incchgtracking** yolunu girin.
    3. **\@Concat (' artımlı-', işlem hattı () girin. RunId, '. txt ')** FilePath **dosyasının dosya** bölümü.  

       ![Havuz veri kümesi - bağlantı](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Değişiklik izleme verilerini temsil eden bir veri kümesi oluşturma 
Bu adımda değişiklik izleme sürümünü depolamak için bir veri kümesi oluşturacaksınız.  Önkoşulların bir parçası olarak table_store_ChangeTracking_version tablosunu oluşturdunuz.

1. Ağaç görünümünde **+ (artı)** seçeneğine ve sonra **Veri Kümesi**’ne tıklayın. 
2. **Azure SQL Veritabanı**’nı seçip **Son**’a tıklayın. 
3. Veri kümesini yapılandırmak için yeni bir sekme görürsünüz. Ayrıca, veri kümesini ağaç görünümünde de görürsünüz. **Özellikler** penceresinde, veri kümesinin adını **ChangeTrackingDataset** olarak değiştirin.
4. **Bağlantı** sekmesine geçin ve aşağıdaki adımları uygulayın: 
    
    1. **Bağlı hizmet** için **AzureSqlDatabaseLinkedService** hizmetini seçin. 
    2. **Tablo** için **[dbo].[table_store_ChangeTracking_version]** seçeneğini belirleyin. 

## <a name="create-a-pipeline-for-the-full-copy"></a>Tam kopya için işlem hattı oluşturma
Bu adımda, kaynak veri deposundaki (Azure SQL Veritabanı) tüm verileri hedef veri deposuna (Azure Blob Depolama) kopyalayan bir kopyalama etkinliğine sahip bir işlem hattı oluşturursunuz.

1. Sol bölmedeki **+ (artı)** seçeneğine tıklayıp **İşlem Hattı**’na tıklayın. 

    ![Yeni işlem hattı menüsü](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. İşlem hattını yapılandırmak için yeni bir sekme görürsünüz. Ayrıca, işlem hattını ağaç görünümünde de görürsünüz. **Özellikler** penceresinde, işlem hattının adını **FullCopyPipeline** olarak değiştirin.

    ![Yeni işlem hattı menüsü](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. **Etkinlikler** araç kutusunda **Veri Akışı**’nı genişletin, **Kopyalama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın ve adı **FullCopyActivity** olarak ayarlayın. 

    ![Tam kopyalama etkinliği adı](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. **Kaynak** sekmesine geçin ve **Kaynak Veri Kümesi** alanı için **SourceDataset**’i seçin. 

    ![Kopyalama etkinliği - kaynak](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. **Havuz** sekmesine geçin ve **Havuz Veri Kümesi** alanı için **SinkDataset**’i seçin. 

    ![Kopyalama etkinliği - havuz](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. İşlem hattı tanımını doğrulamak için araç çubuğunda **Doğrula**’ya tıklayın. Doğrulama hatası olmadığından emin olun. **seçeneğine tıklayarak >>İşlem Hattı Doğrulama Raporu**’nu kapatın. 

    ![İşlem hattını doğrulama](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Varlıkları (bağlı hizmetler, veri kümeleri ve işlem hatları) yayımlamak için **Yayımla**’ya tıklayın. Yayımlama başarılı olana kadar bekleyin. 

    ![Yayımla düğmesi](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. **Başarıyla yayımlandı** iletisini görene kadar bekleyin. 

    ![Yayımlama başarılı](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Bildirimleri soldaki **Bildirimleri Göster** düğmesine tıklayarak da görebilirsiniz. Bildirim penceresini kapatmak için **X** simgesine tıklayın.

    ![Bildirimleri göster](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Tam kopyalama işlem hattını çalıştırma
İşlem hattının araç çubuğunda **Tetikle**’ye tıklayıp **Şimdi Tetikle**’ye tıklayın. 

![Şimdi Tetikle menüsü](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Tam kopyalama işlem hattını izleme

1. Soldaki **İzleyici** sekmesine tıklayın. Listede işlem hattı çalıştırmasını ve çalıştırmanın durumunu görebilirsiniz. Listeyi yenilemek için **Yenile**’ye tıklayın. Eylemler sütunundaki bağlantılar, işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemenize ve işlem hattını yeniden çalıştırmanıza imkan tanır. 

    ![İşlem hattı çalıştırmaları](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Göster** bağlantısına tıklayın. İşlem hattında yalnızca bir etkinlik olduğundan listede tek bir girdi görürsünüz. İşlem hattı çalıştırmaları görünümüne dönmek için üstteki **İşlem hatları** bağlantısına tıklayın. 

    ![Etkinlik çalıştırmaları](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Sonuçları gözden geçirin
`incremental-<GUID>.txt` kapsayıcısının `incchgtracking` klasöründe `adftutorial` adlı bir dosya görürsünüz. 

![Tam kopyadan çıkış dosyası](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

Dosyada Azure SQL veritabanından veriler olmalıdır:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Kaynak tabloya daha fazla veri ekleme

Satır eklemek ve satırı güncelleştirmek için aşağıdaki sorguyu Azure SQL veritabanında çalıştırın. 

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

1. Data Factory Kullanıcı arabiriminde, **Düzenle** sekmesine geçin. sol bölmedeki **+ (artı)** düğmesine tıklayın ve işlem **hattı**' na tıklayın. 

    ![Yeni işlem hattı menüsü](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. İşlem hattını yapılandırmak için yeni bir sekme görürsünüz. Ayrıca, işlem hattını ağaç görünümünde de görürsünüz. **Özellikler** penceresinde, işlem hattının adını **IncrementalCopyPipeline** olarak değiştirin.

    ![İşlem hattı adı](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. **Etkinlikler** araç kutusunda **Genel**’i genişletin ve **Arama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **LookupLastChangeTrackingVersionActivity** olarak ayarlayın. Bu etkinlik, **table_store_ChangeTracking_version** tablosunda depolanan son kopyalama işleminde kullanılan değişiklik izleme sürümünü alır.

    ![Arama Etkinliği - ad](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. **Özellikler** penceresinde **Ayarlar**’a geçin ve **Kaynak Veri Kümesi** alanı için **ChangeTrackingDataset** seçeneğini belirleyin. 

    ![Arama Etkinliği - ayarlar](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. **Etkinlikler** araç kutusundan **Arama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **LookupCurrentChangeTrackingVersionActivity** olarak ayarlayın. Bu etkinlik, geçerli değişiklik izleme sürümünü alır.

    ![Arama Etkinliği - ad](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. **Özellikler** penceresinde **Ayarlar**’a geçin ve aşağıdaki adımları uygulayın:

   1. **Kaynak Veri Kümesi** alanı için **SourceDataset**’i seçin.
   2. **Sorgu Kullan** için **Sorgu**’yu seçin. 
   3. **Sorgu** için aşağıdaki SQL sorgusunu girin. 

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      ![Arama Etkinliği - ayarlar](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. **Etkinlikler** araç kutusunda **Veri Akışı**’nı genişletin ve **Kopyalama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **IncrementalCopyActivity** olarak ayarlayın. Bu etkinlik, son değişiklik izleme sürümü ile geçerli değişiklik izleme sürümü arasındaki verileri hedef veri deposuna kopyalar. 

    ![Kopyalama Etkinliği - ad](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. **Özellikler** penceresinde **Kaynak** sekmesine geçin ve aşağıdaki adımları uygulayın:

   1. **Kaynak Veri Kümesi** için **SourceDataset**’i seçin. 
   2. **Sorgu Kullan** için **Sorgu**’yu seçin. 
   3. **Sorgu** için aşağıdaki SQL sorgusunu girin. 

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```
    
      ![Kopyalama Etkinliği - kaynak ayarları](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. **Havuz** sekmesine geçin ve **Havuz Veri Kümesi** alanı için **SinkDataset**’i seçin. 

    ![Kopyalama Etkinliği - havuz ayarları](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. Tek tek **her iki Arama etkinliğini Kopyalama etkinliğine bağlayın**. **Arama** etkinliğine bağlı **yeşil** düğmeyi **Kopyalama** etkinliğine sürükleyin. 

    ![Arama ve Kopyalama etkinliklerini bağlama](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. **Etkinlikler** araç kutusundan **Saklı Yordam** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **StoredProceduretoUpdateChangeTrackingActivity** olarak ayarlayın. Bu etkinlik, **table_store_ChangeTracking_version** tablosunda değişiklik izleme sürümünü güncelleştirir.

    ![Saklı Yordam Etkinliği - ad](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. *SQL Hesabı** sekmesine geçin ve **Bağlı hizmet** için **AzureSqlDatabaseLinkedService** seçeneğini belirleyin. 

    ![Saklı Yordam Etkinliği - SQL Hesabı](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. **Saklı Yordam** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Saklı yordam adı** için **Update_ChangeTracking_Version** adını seçin.  
    2. **Parametreyi içeri aktar**’ı seçin. 
    3. **Saklı yordam parametreleri** bölümünde, parametreler için aşağıdaki değerleri belirtin: 

        | Ad | Tür | Değer | 
        | ---- | ---- | ----- | 
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} | 
        | TableName | Dize | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} | 
    
        ![Saklı Yordam Etkinliği - Parametreler](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Kopyalama etkinliğini Saklı Yordam Etkinliğine bağlayın**. Kopyalama etkinliğine bağlı **yeşil** düğmeyi sürükleyip Saklı Yordam etkinliğine bırakın. 

    ![Kopyalama ve Saklı Yordam etkinliklerini bağlama](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Araç çubuğunda **Doğrula**’ya tıklayın. Doğrulama hatası olmadığından emin olun. **seçeneğine tıklayarak >>İşlem Hattı Doğrulama Raporu** penceresini kapatın. 

    ![Doğrula düğmesi](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16. **Tümünü Yayımla** düğmesine tıklayarak varlıkları (bağlı hizmetler, veri kümeleri ve işlem hatları) Data Factory hizmetinde yayımlayın. **Yayımlama başarılı** iletisini görene kadar bekleyin. 

       ![Yayımla düğmesi](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Artımlı kopyalama işlem hattını çalıştırma
1. İşlem hattının araç çubuğunda **Tetikle**’ye tıklayıp **Şimdi Tetikle**’ye tıklayın. 

    ![Şimdi Tetikle menüsü](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. **İşlem Hattı Çalıştırma** penceresinde **Son**’u seçin.

### <a name="monitor-the-incremental-copy-pipeline"></a>Artımlı kopyalama işlem hattını izleme
1. Soldaki **İzleyici** sekmesine tıklayın. Listede işlem hattı çalıştırmasını ve çalıştırmanın durumunu görebilirsiniz. Listeyi yenilemek için **Yenile**’ye tıklayın. **Eylemler** sütunundaki bağlantılar, işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemenize ve işlem hattını yeniden çalıştırmanıza imkan tanır. 

    ![İşlem hattı çalıştırmaları](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Göster** bağlantısına tıklayın. İşlem hattında yalnızca bir etkinlik olduğundan listede tek bir girdi görürsünüz. İşlem hattı çalıştırmaları görünümüne dönmek için üstteki **İşlem hatları** bağlantısına tıklayın. 

    ![Etkinlik çalıştırmaları](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Sonuçları gözden geçirin
`incchgtracking` kapsayıcısının `adftutorial` klasöründe ikinci dosyayı görürsünüz. 

![Artımlı kopyadan çıkış dosyası](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

Dosyada yalnızca Azure SQL veritabanındaki değişiklik verileri olmalıdır. `U` bulunan kayıt, veritabanındaki güncelleştirilmiş satırdır ve `I` ise eklenen bir satırdır. 

```
1,update,10,2,U
6,new,50,1,I
```
İlk üç sütun, data_source_table tablosundaki değiştirilmiş verilerdir. Son iki sütun, değişiklik izleme sistem tablosundaki meta verilerdir. Dördüncü sütun, değiştirilen her satıra ilişkin SYS_CHANGE_VERSION değeridir. Beşinci sütun ise işlemdir: U = güncelleştirme, I = ekleme.  Değişiklik izleme bilgileri hakkında daha fazla ayrıntı için bkz. [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql). 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>Sonraki adımlar
Yalnızca LastModifiedDate göre yeni ve değiştirilmiş dosyaları kopyalama hakkında bilgi edinmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Yeni dosyaları LastModifiedDate göre Kopyala](tutorial-incremental-copy-lastmodified-copy-data-tool.md)



