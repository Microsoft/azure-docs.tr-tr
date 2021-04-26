---
title: Değişiklik verilerini yakalama kullanarak artımlı olarak veri kopyalama
description: Bu öğreticide, Delta verilerini Azure SQL yönetilen örnek veritabanındaki bir tablodan Azure depolama birimine artımlı olarak kopyalayan bir Azure Data Factory işlem hattı oluşturacaksınız.
ms.author: nihurt
author: hurtn
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: a00ec8698b188b8fa87935e498e8cfab3aeab5aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101724991"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Değişiklik verilerini yakalama (CDC) kullanarak Azure SQL yönetilen örneğinden Azure depolama 'ya artımlı olarak veri yükleme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, kaynak Azure SQL yönetilen örnek veritabanındaki **değişiklik verilerini yakalama (CDC)** bilgilerine göre Delta verileri bir Azure Blob depolama alanına yükleyen bir işlem hattına sahip bir Azure Veri Fabrikası oluşturacaksınız.  

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Kaynak veri deposunu hazırlama
> * Veri fabrikası oluşturma.
> * Bağlı hizmet oluşturma.
> * Kaynak ve havuz veri kümeleri oluşturma.
> * Değiştirilen verileri denetlemek için işlem hattını oluşturma, hata ayıklama ve çalıştırma
> * Kaynak tablodaki verileri değiştirme
> * Tam artımlı kopyalama işlem hattını eksiksiz, çalıştırma ve izleme

## <a name="overview"></a>Genel Bakış
Azure SQL yönetilen örnekler (mı) ve SQL Server gibi veri depoları tarafından desteklenen değişiklik verilerini yakalama teknolojisi, değiştirilen verileri belirlemek için kullanılabilir.  Bu öğreticide, Azure SQL yönetilen örneği 'nden Azure Blob depolama alanına artımlı verileri artımlı olarak yüklemek için SQL değişiklik verilerini yakalama teknolojisine Azure Data Factory nasıl kullanılacağı açıklanmaktadır.  SQL değişiklik verilerini yakalama teknolojisi hakkında daha somut bilgiler için, bkz. [SQL Server veri yakalamayı değiştirme](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Uçtan uca iş akışı
Değişiklik verilerini yakalama teknolojisini kullanarak verileri artımlı olarak yüklemek için tipik uçtan uca iş akışı adımları aşağıda verilmiştir.

> [!NOTE]
> Hem Azure SQL MI hem de SQL Server değişiklik verilerini yakalama teknolojisini destekler. Bu öğretici, kaynak veri deposu olarak Azure SQL yönetilen örneğini kullanır. Dilerseniz şirket içi bir SQL Server kullanabilirsiniz.

## <a name="high-level-solution"></a>Yüksek düzeyli çözüm
Bu öğreticide, aşağıdaki işlemleri gerçekleştiren bir işlem hattı oluşturacaksınız:  

   1. SQL veritabanı CDC tablosundaki Değiştirilen kayıt sayısını saymak ve bir If koşulu etkinliğine geçirmek için bir **arama etkinliği** oluşturun.
   2. Değiştirilen kayıtlar olup olmadığını denetlemek için bir **If koşulu** oluşturun ve bu durumda kopyalama etkinliğini çağırın.
   3. CDC tablosu arasında ekli/güncelleştirilmiş/silinmiş verileri Azure Blob depolama alanına kopyalamak için bir **kopyalama etkinliği** oluşturun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Önkoşullar
* **Azure SQL veritabanı yönetilen örneği**. Veritabanını **kaynak** veri deposu olarak kullanabilirsiniz. Bir Azure SQL veritabanı yönetilen örneği yoksa, oluşturma adımları için [Azure SQL veritabanı yönetilen örneği oluşturma](../azure-sql/managed-instance/instance-create-quickstart.md) makalesine bakın.
* **Azure depolama hesabı**. Blob depolamayı **Havuz** veri deposu olarak kullanırsınız. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../storage/common/storage-account-create.md) makalesine bakın. **RAW** adlı bir kapsayıcı oluşturun. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Azure SQL veritabanı 'nda veri kaynağı tablosu oluşturma

1. **SQL Server Management Studio** başlatın ve Azure SQL yönetilen örnekler sunucunuza bağlanın.
2. **Sunucu Gezgini**’nde **veritabanınıza** sağ tıklayın ve **Yeni Sorgu**’yu seçin.
3. Veri kaynağı deposu olarak adlandırılan bir tablo oluşturmak için Azure SQL yönetilen örnekler veritabanınızda aşağıdaki SQL komutunu çalıştırın `customers` .  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Aşağıdaki SQL sorgusunu çalıştırarak veritabanınızda ve kaynak tablonuzda (müşteriler) **değişiklik verilerini yakalama** mekanizmasını etkinleştirin:

    > [!NOTE]
    > - &lt;Kaynak şemanızın adını, &gt; Müşteriler tablosuna sahıp Azure SQL mı 'nizin şeması ile değiştirin.
    > - Değişiklik verilerini yakalama, izlenen tabloyu değiştiren işlemlerin bir parçası olarak hiçbir şey yapmaz. Bunun yerine, ekleme, güncelleştirme ve silme işlemleri işlem günlüğüne yazılır. Değişiklik tablolarına bırakılan veriler, düzenli aralıklarla ve verileri sistematik olarak ayıkladıysanız, geriye doğru bir şekilde büyümez. Daha fazla bilgi için bkz. [veritabanı Için değişiklik verilerini yakalamayı etkinleştirme](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Aşağıdaki komutu çalıştırarak Müşteriler tablosuna veri ekleyin:

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Değişiklik verilerini yakalama özelliği etkinleştirilmeden önce tabloda hiçbir geçmiş değişiklik yakalanmaz.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. Sol taraftaki menüden veri ve analiz **kaynak oluştur**' u seçin  >    >  **Data Factory**:

   ![“Yeni” bölmesinde Data Factory seçimi](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. **Yeni veri fabrikası** sayfasında **ad** için **ADFTutorialDataFactory** adını girin.

     ![Yeni veri fabrikası sayfası](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Azure veri fabrikasının adı **genel olarak benzersiz** olmalıdır. Aşağıdaki hatayı alırsanız veri fabrikasının adını değiştirin (örneğin adınızADFTutorialDataFactory) ve oluşturmayı yeniden deneyin. Data Factory yapıtlarını adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.

    *"ADFTutorialDataFactory" Veri Fabrikası adı kullanılamıyor.*
3. **Sürüm** için **V2**'yi seçin.
4. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin.
5. **Kaynak grubu** için aşağıdaki adımlardan birini yapın:

   1. **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.
   2. **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).  
5. Data factory için **konum** seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.
6. **GIT 'ı etkinleştir** seçimini kaldırın.     
7. **Oluştur**’a tıklayın.
8. Dağıtım tamamlandıktan sonra **Kaynağa Git ' e** tıklayın.

   ![Ekran görüntüsünde, dağıtımınızın tamamlandığını ve kaynağa gitme seçeneğini gösteren bir ileti gösterilir.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Oluşturma işlemi tamamlandıktan sonra, resimde gösterildiği gibi **Data Factory** sayfasını görürsünüz.

   ![Ekran görüntüsü, dağıttığınız veri fabrikasını gösterir.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Azure Data Factory kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **Geliştir ve İzle** kutucuğuna tıklayın.
11. **Başlarken** sayfasında, aşağıdaki resimde gösterildiği gibi sol bölmede bulunan **Düzenle** sekmesine geçin:

    ![İşlem hattı oluştur düğmesi](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu bölümde, Azure depolama hesabınıza ve Azure SQL MI 'ye bağlı hizmetler oluşturacaksınız.

### <a name="create-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturma.
Bu adımda, Azure Depolama Hesabınızı veri fabrikasına bağlarsınız.

1. **Bağlantılar**’a ve sonra **+ Yeni**’ye tıklayın.

   ![Yeni bağlantı düğmesi](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. **New Linked Service** (Yeni Bağlı Hizmet) penceresinde **Azure Blob Depolama**’yı seçip **Devam**’a tıklayın.

   ![Azure Blob Depolama’yı seçin](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin:

   1. **Ad** için **AzureStorageLinkedService** adını girin.
   2. **Depolama hesabı adı** Için Azure depolama hesabınızı seçin.
   3. **Kaydet**’e tıklayın.

   ![Azure Depolama Hesabı ayarları](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Azure SQL MI veritabanı bağlı hizmetini oluşturun.
Bu adımda, Azure SQL MI veritabanınızı veri fabrikasına bağlarsınız.

> [!NOTE]
> SQL MI kullanan kişiler için, genel ve özel uç nokta aracılığıyla erişim hakkında bilgi için [buraya](./connector-azure-sql-managed-instance.md#prerequisites) bakın. Özel uç nokta kullanılıyorsa, bu işlem hattının şirket içinde barındırılan tümleştirme çalışma zamanı kullanılarak çalıştırılması gerekir. Aynı durum, bir VM veya VNet senaryolarında şirket içi SQL Server çalışan bu işlemler için de geçerlidir.

1. **Bağlantılar**’a ve sonra **+ Yeni**’ye tıklayın.
2. **Yeni bağlı hizmet** PENCERESINDE **Azure SQL veritabanı yönetilen örneği**' ni seçin ve **devam**' a tıklayın.
3. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin:

   1. **Ad** alanı için **AzureSqlMI1** girin.
   2. **Sunucu adı** alanı için SQL Server 'nizi seçin.
   4. **Veritabanı adı** alanı için SQL veritabanınızı seçin.
   5. **Kullanıcı adı** alanına kullanıcının adını girin.
   6. **Parola** alanına kullanıcının parolasını girin.
   7. Bağlantıyı test etmek için **Bağlantıyı sına**’ya tıklayın.
   8. Bağlı hizmeti kaydetmek için **Kaydet**’e tıklayın.

   ![Azure SQL MI veritabanı bağlı hizmet ayarları](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Veri kümeleri oluşturma
Bu adımda, veri kaynağını ve veri hedefini temsil eden veri kümeleri oluşturacaksınız.

### <a name="create-a-dataset-to-represent-source-data"></a>Kaynak verileri temsil eden bir veri kümesi oluşturma
Bu adımda, kaynak verileri temsil etmek için bir veri kümesi oluşturursunuz.

1. Ağaç görünümünde **+ (artı)** seçeneğine ve sonra **Veri Kümesi**’ne tıklayın.

   ![Yeni Veri Kümesi menüsü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. **Azure SQL veritabanı yönetilen örneği**' ni seçin ve **devam**' a tıklayın.

   ![Kaynak veri kümesi türü - Azure SQL Veritabanı](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. **Özellikleri ayarla** sekmesinde, veri kümesi adını ve bağlantı bilgilerini ayarlayın:
 
   1. **Bağlı hizmet** için **AzureSqlMI1** seçin.
   2. **[Dbo] öğesini seçin. [** **tablo adı** için dbo_customers_CT].  Note: Müşteriler tablosunda CDC etkinleştirildiğinde bu tablo otomatik olarak oluşturulmuştur. Değiştirilen veriler hiçbir şekilde bu tablodan doğrudan sorgulanmaz ancak bunun yerine [CDC işlevleri](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql)aracılığıyla ayıklanır.

   ![Kaynak bağlantısı](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Havuz veri deposuna kopyalanan verileri temsil eden bir veri kümesi oluşturun.
Bu adımda, kaynak veri deposundan kopyalanan verileri temsil etmek için bir veri kümesi oluşturursunuz. Önkoşulların bir parçası olarak Azure Blob depolama alanında Data Lake kapsayıcısını oluşturdunuz. Henüz yoksa kapsayıcıyı oluşturun (veya) var olan bir kapsayıcının adına ayarlayın. Bu öğreticide, çıkış dosyası adı, daha sonra yapılandırılacak olan tetikleme süresi kullanılarak dinamik olarak oluşturulur.

1. Ağaç görünümünde **+ (artı)** seçeneğine ve sonra **Veri Kümesi**’ne tıklayın.

   ![Yeni Veri Kümesi menüsü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. **Azure Blob depolama**' yı seçin ve **devam**' a tıklayın.

   ![Havuz veri kümesi türü - Azure Blob Depolama](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. **Delimitedtext**' i seçin ve **devam**' a tıklayın.

   ![Havuz veri kümesi biçimi-Delimitedmetni](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. **Özellikleri ayarla** sekmesinde, veri kümesi adını ve bağlantı bilgilerini ayarlayın:

   1. **Bağlı hizmet** için **AzureStorageLinkedService** hizmetini seçin.
   2. **FilePath** öğesinin **kapsayıcı** bölümü için **Ham** girin.
   3. **İlk satırı üst bilgi olarak** etkinleştir
   4. **Tamam 'a** tıklayın

   ![Havuz veri kümesi - bağlantı](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Değiştirilen verileri kopyalamak için bir işlem hattı oluşturma
Bu adımda, bir **arama etkinliği** kullanarak değişiklik tablosunda bulunan değiştirilen kayıtların sayısını denetleyen bir işlem hattı oluşturacaksınız. If koşulu etkinliği, değiştirilen kayıt sayısının sıfırdan büyük olup olmadığını denetler ve ekli/güncelleştirilmiş/silinmiş verileri Azure Blob depolama alanına kopyalamak için bir **kopyalama etkinliği** çalıştırır. Son olarak, bir atlayan pencere tetikleyicisi yapılandırılır ve başlangıç ve bitiş zamanları etkinliklere başlangıç ve bitiş penceresi parametreleri olarak geçirilir. 

1. Data Factory Kullanıcı arabiriminde, **Düzenle** sekmesine geçin. Sol bölmedeki **+ (artı)** düğmesine tıklayın ve işlem **hattı**' na tıklayın.

    ![Yeni işlem hattı menüsü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. İşlem hattını yapılandırmak için yeni bir sekme görürsünüz. Ayrıca, işlem hattını ağaç görünümünde de görürsünüz. **Özellikler** penceresinde, işlem hattının adını **IncrementalCopyPipeline** olarak değiştirin.

    ![İşlem hattı adı](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. **Etkinlikler** araç kutusunda **Genel**’i genişletin ve **Arama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **Getchangecount** olarak ayarlayın. Bu etkinlik belirli bir zaman penceresi için değişiklik tablosundaki kayıt sayısını alır.

    ![Arama Etkinliği - ad](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. **Özellikler** penceresinde **Ayarlar** ' a geçin:
   1. **Kaynak veri kümesi** alanı IÇIN SQL mı veri kümesi adını belirtin.
   2. Sorgu seçeneğini belirleyin ve sorgu kutusuna şunu girin:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. **Yalnızca ilk satırı** etkinleştir

    ![Arama Etkinliği - ayarlar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Arama etkinliği tarafından geçerli bir çıktının elde olduğundan emin olmak için **Verileri Önizle** düğmesine tıklayın

    ![Arama etkinliği-Önizleme](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. **Etkinlikler** araç kutusunda **yineleme & koşulları** ' nı genişletin ve **If koşulu** etkinliğini, işlem hattı Tasarımcısı yüzeyine sürükleyin. Etkinliğin adını **HasChangedRows** olarak ayarlayın. 

    ![If koşulu etkinliği-adı](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. **Özellikler** penceresinde **etkinliklere** geçin:

   1. Aşağıdaki **ifadeyi** girin
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Doğru koşulu düzenlemek için kalem simgesine tıklayın.

   ![If koşulu etkinliği-ayarlar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. **Etkinlikler** araç kutusunda **genel** ' i genişletin ve bir **bekleme** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Bu, IF koşulunda hata ayıklamak ve öğreticide daha sonra değiştirilebilmek için geçici bir etkinliktir. 

   ![Koşul doğruysa-bekle](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Ana işlem hattına dönmek için ıncrementalcopypipeline içerik haritası ' na tıklayın.

8. İşlem hattının başarıyla yürütüldüğünü doğrulamak için işlem hattını **hata ayıklama** modunda çalıştırın. 

   ![İşlem hattı-hata ayıklama](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Sonra, doğru koşul adımına dönün ve **bekleme** etkinliğini silin. **Etkinlikler** araç kutusunda **Taşı & Dönüştür**' ü genişletin ve **kopyalama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. Etkinliğin adını **IncrementalCopyActivity** olarak ayarlayın. 

   ![Kopyalama Etkinliği - ad](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. **Özellikler** penceresinde **Kaynak** sekmesine geçin ve aşağıdaki adımları uygulayın:

   1. **Kaynak veri kümesi** alanı IÇIN SQL mı veri kümesi adını belirtin. 
   2. **Sorgu Kullan** için **Sorgu**’yu seçin.
   3. **Sorgu** için aşağıdakileri girin.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Kopyalama Etkinliği - kaynak ayarları](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Sorgunun değiştirilen satırları doğru döndürdüğünden emin olmak için Önizleme ' ye tıklayın.

    ![Ekran görüntüsünde sorguyu doğrulamak için Önizleme gösterilmektedir.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. **Havuz** sekmesine geçin ve **Havuz veri kümesi** alanı için Azure depolama veri kümesini belirtin.

    ![Ekran görüntüsü havuz sekmesini gösterir.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Ana işlem hattı tuvaline geri tıklayın ve **arama** etkinliğini tek tek **koşul** etkinliğine bağlayın. **Arama** etkinliğine eklenen **yeşil** düğmeyi **If koşulu** etkinliğine sürükleyin.

    ![Arama ve Kopyalama etkinliklerini bağlama](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Araç çubuğunda **Doğrula**’ya tıklayın. Doğrulama hatası olmadığından emin olun. **>>** seçeneğine tıklayarak **İşlem Hattı Doğrulama Raporu** penceresini kapatın.

    ![Doğrula düğmesi](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. İşlem hattını test etmek ve depolama konumunda bir dosyanın oluşturulduğunu doğrulamak için hata ayıkla ' ya tıklayın.

    ![Artımlı işlem hattı hata ayıklaması-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. **Tümünü Yayımla** düğmesine tıklayarak varlıkları (bağlı hizmetler, veri kümeleri ve işlem hatları) Data Factory hizmetine yayımlayın. **Yayımlama başarılı** iletisini görene kadar bekleyin.

    ![Yayımla düğmesi](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Atlayan pencere tetikleyicisi ve CDC pencere parametrelerini yapılandırma 
Bu adımda, işi sık sık bir zamanlamaya göre çalıştırmak için atlayan bir pencere tetikleyicisi oluşturacaksınız. Dönen pencere tetikleyicisinin WindowStart ve WindowEnd sistem değişkenlerini kullanacaksınız ve CDC sorgusunda kullanılacak işlem hattınızı parametre olarak geçirirsiniz.

1. **Incrementalcopypipeline** Işlem hattının **Parametreler** sekmesine gidin ve **+ Yeni** düğmesini kullanarak ardışık düzen penceresi başlangıç ve bitiş zamanını temsil eden Iki parametre (**triggerstarttime** ve **triggerbitişsaati**) ekleyin. Hata ayıklama amacıyla, **yyyy-aa-gg hh24: mı: ss. FFF** biçiminde varsayılan değerleri ekleyin ancak triggerstarttime 'in tabloda bir CDC etkinleştirilmeden önce olmadığından emin olun, aksi takdirde bu bir hataya neden olur.

    ![Şimdi Tetikle menüsü](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. **Arama** etkinliğinin Ayarlar sekmesine tıklayın ve sorguyu Start ve End parametrelerini kullanacak şekilde yapılandırın. Aşağıdakileri sorguya kopyalayın:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. **If koşulu** etkinliğinin doğru olması durumunda **kopyalama** etkinliğine gidin ve **kaynak** sekmesine tıklayın. Aşağıdakileri sorguya kopyalayın:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. **Kopyalama** etkinliğinin **Havuz** sekmesine tıklayın ve veri kümesi özelliklerini düzenlemek için **Aç** ' a tıklayın. **Parametreler** sekmesine tıklayın ve **triggerstart** adlı yeni bir parametre ekleyin    

    ![Ekran görüntüsü parametreler sekmesine yeni bir parametre eklemeyi gösterir.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Daha sonra, veri kümesi özelliklerini, verileri tarih tabanlı bölümlerle **müşteriler/artımlı** alt dizininde depolamak üzere yapılandırın.
   1. Veri kümesi özelliklerinin **bağlantı** sekmesine tıklayın ve hem **Dizin** hem de **Dosya** bölümleri için dinamik içerik ekleyin. 
   2. Metin kutusunun altındaki dinamik içerik bağlantısına tıklayarak **Dizin** bölümüne aşağıdaki ifadeyi girin:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. **Dosya** bölümüne aşağıdaki ifadeyi girin. Bu işlem, tetikleyici başlangıç tarihi ve saatine göre dosya adları oluşturacak ve bu da csv uzantısı ile düzeltilmelidir:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Havuz veri kümesi yapılandırması-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. **Incrementalcopypipeline** sekmesine tıklayarak **kopyalama** etkinliğindeki **Havuz** ayarlarına geri gidin. 
   5. Veri kümesi özelliklerini genişletin ve triggerStart parametre değerine aşağıdaki ifadeyle dinamik içerik girin:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Havuz veri kümesi yapılandırması-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. İşlem hattını test etmek için hata ayıkla ' ya tıklayın ve klasör yapısının ve çıkış dosyasının beklendiği şekilde oluşturulduğundan emin olun. İçeriğini doğrulamak için dosyayı indirip açın. 

    ![Artımlı kopya hata ayıklama-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. İşlem hattı çalıştırmasının giriş parametrelerini inceleyerek parametrelerin sorguya eklendiğinden emin olun.

    ![Artımlı kopya hata ayıklama-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. **Tümünü Yayımla** düğmesine tıklayarak varlıkları (bağlı hizmetler, veri kümeleri ve işlem hatları) Data Factory hizmetine yayımlayın. **Yayımlama başarılı** iletisini görene kadar bekleyin.
9. Son olarak, ardışık düzeni düzenli bir aralıkta çalıştırmak ve başlangıç ve bitiş zamanı parametrelerini ayarlamak için bir atlayan pencere tetikleyicisi yapılandırın. 
   1. **Tetikleyici Ekle** düğmesine tıklayın ve **Yeni/Düzenle** ' yi seçin.

   ![Yeni tetikleyici Ekle](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Bir tetikleyici adı girin ve yukarıdaki hata ayıklama penceresinin bitiş zamanına eşit olan bir başlangıç saati belirtin.

   ![Atlayan Pencere Tetikleyicisi](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. Sonraki ekranda, sırasıyla başlangıç ve bitiş parametreleri için aşağıdaki değerleri belirtin.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Atlayan pencere tetikleyicisi-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Tetikleyici yalnızca yayımlandıktan sonra çalışır. Ayrıca, ortaya dönerek beklenen davranış, başlangıç tarihinden hemen itibaren tüm geçmiş aralıklarını çalıştırmlardır. Atlayan pencere Tetikleyicileri hakkında daha fazla bilgiyi [burada](./how-to-create-tumbling-window-trigger.md)bulabilirsiniz. 
  
10. **SQL Server Management Studio** kullanarak, aşağıdaki SQL 'i çalıştırarak Müşteri tablosunda bazı ek değişiklikler yapın:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. **Tümünü Yayımla** düğmesine tıklayın. **Yayımlama başarılı** iletisini görene kadar bekleyin.  
12. Birkaç dakika sonra işlem hattı tetiklenir ve Azure Storage 'a yeni bir dosya yüklenir


### <a name="monitor-the-incremental-copy-pipeline"></a>Artımlı kopyalama işlem hattını izleme
1. Soldaki **İzleyici** sekmesine tıklayın. Listede işlem hattı çalıştırmasını ve çalıştırmanın durumunu görebilirsiniz. Listeyi yenilemek için **Yenile**’ye tıklayın. Yeniden çalıştırma eylemi ve tüketim raporuna erişmek için işlem hattının adının yanına gelin.

    ![İşlem hattı çalıştırmaları](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için işlem hattı adına tıklayın. Değiştirilen veriler algılanırsa, kopyalama etkinliği dahil olmak üzere üç etkinlik olacaktır, aksi takdirde listede yalnızca iki girdi olur. İşlem hattı çalıştırmaları görünümüne geri dönmek için üstteki **Tüm Işlem hatları** bağlantısına tıklayın.

    ![Etkinlik çalıştırmaları](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Sonuçları gözden geçirin
`raw` kapsayıcısının `customers/incremental/YYYY/MM/DD` klasöründe ikinci dosyayı görürsünüz.

![Artımlı kopyadan çıkış dosyası](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Sonraki adımlar
Yalnızca LastModifiedDate göre yeni ve değiştirilmiş dosyaları kopyalama hakkında bilgi edinmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Yeni dosyaları LastModifiedDate göre Kopyala](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
