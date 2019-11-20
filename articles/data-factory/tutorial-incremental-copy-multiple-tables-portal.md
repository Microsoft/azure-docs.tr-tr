---
title: 'Azure Data Factory kullanarak birden çok tabloyu artımlı olarak kopyalama '
description: Bu öğreticide, değişim verileri şirket içi SQL Server veritabanındaki birden çok tablodan Azure SQL veritabanına artımlı olarak kopyalayan bir Azure Data Factory işlem hattı oluşturacaksınız.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/20/2018
ms.author: yexu
ms.openlocfilehash: 3129a0629c4de69e6e3d65f2f74da97e8d39a467
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683405"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>SQL Server’daki birden fazla tablodan Azure SQL veritabanı’na artımlı olarak veri yükleme
Bu öğreticide, değişim verileri şirket içi SQL Server’daki birden çok tablodan Azure SQL Veritabanına yükleyen bir Azure veri fabrikası işlem hattı oluşturacaksınız.    

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Kaynak ve hedef veri depolarını hazırlayın.
> * Veri fabrikası oluşturma.
> * Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma.
> * Tümleştirme çalışma zamanını yükleyin. 
> * Bağlı hizmet oluşturma. 
> * Kaynak, havuz ve eşik veri kümeleri oluşturun.
> * İşlem hattını oluşturma, çalıştırma ve izleme.
> * Sonuçları gözden geçirin.
> * Kaynak tablolarına veri ekleyin veya bu verileri güncelleştirin.
> * İşlem hattını yeniden çalıştırın ve izleyin.
> * Son sonuçları gözden geçirin.

## <a name="overview"></a>Genel Bakış
Bu çözümü oluşturmak için önemli adımlar şunlardır: 

1. **Eşit sütununu seçin**.
    
    Kaynak veri deposunda her çalıştırma için yeni veya güncelleştirilmiş kayıtları tanımlamak için her tablodan kullanılabilen bir sütun seçin. Normalde, satırlar oluşturulduğunda veya güncelleştirildiğinde seçilen bu sütundaki veriler (örneğin, last_modify_time veya kimlik) artmaya devam eder. Bu sütundaki en büyük değer eşik olarak kullanılır.

1. **Eşik değerini depolamak için veri deposunu hazırlayın**.   
    
    Bu öğreticide, eşik değerini bir SQL veritabanında depolayacaksınız.

1. **Aşağıdaki eylemler ile bir işlem hattı oluşturun**: 
    
    a. İşlem hattına parametre olarak geçen kaynak tablosu adlarının bir listesi üzerinden yinelenen bir ForEach eylemi oluşturun. Her kaynak tablosunda, bu tabloya yönelik olarak yüklenen değişiklikleri gerçekleştirmek için aşağıdaki eylemleri çağırır.

    b. İki arama etkinliği oluşturun. Son eşik değerini almak için ilk Arama etkinliğini kullanın. Yeni eşik değerini almak için ikinci Arama etkinliğini kullanın. Bu eşik değerleri, Kopyalama etkinliğine geçirilir.

    c. Eşik sütununun değeri eski eşik değerinden büyük ve yeni eşik değerinden küçük olacak şekilde, satırları kaynak veri deposundan kopyalayan bir Kopyalama etkinliği oluşturun. Ardından, delta veriler kaynak veri deposundan Azure Blob depolama alanına yeni bir dosya olarak kopyalanır.

    d. Sonraki seferde çalışan işlem hattı için eşik değerini güncelleştiren bir StoredProcedure etkinliği oluşturun. 

    Yüksek düzeyli çözüm diyagramı aşağıdaki gibidir: 

    ![Artımlı olarak veri yükleme](media/tutorial-incremental-copy-multiple-tables-portal/high-level-solution-diagram.png)


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Ön koşullar
* **SQL Server**. Bu öğreticide şirket içi SQL Server veritabanını kaynak veri deposu olarak kullanırsınız. 
* **Azure SQL Veritabanı**. SQL veritabanını havuz veri deposu olarak kullanırsınız. SQL veritabanınız yoksa, oluşturma adımları için bkz. [Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md). 

### <a name="create-source-tables-in-your-sql-server-database"></a>SQL Server veritabanınızda kaynak tabloları oluşturma

1. SQL Server Management Studio’yu açın ve şirket içi SQL Server veritabanınıza bağlanın.

1. **Sunucu Gezgini**’nde veritabanına sağ tıklayın ve **Yeni Sorgu**’yu seçin.

1. `customer_table` ve `project_table` adlı tabloları oluşturmak için aşağıdaki SQL komutunu veritabanınızda çalıştırın:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Azure SQL veritabanınızda hedef tablo oluşturma
1. SQL Server Management Studio’yu açın ve Azure SQL veritabanınıza bağlanın.

1. **Sunucu Gezgini**’nde veritabanına sağ tıklayın ve **Yeni Sorgu**’yu seçin.

1. `customer_table` ve `project_table`adlı tablolar oluşturmak için Azure SQL veritabanınızda aşağıdaki SQL komutunu çalıştırın:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Üst eşik değerini depolamak için Azure SQL veritabanında başka bir tablo oluşturma
1. Eşik değerini depolamak için `watermarktable` adlı bir tablo oluşturmak üzere Azure SQL veritabanınızda aşağıdaki SQL komutunu çalıştırın: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Her iki kaynak tablonun ilk eşik değerlerini eşik tablosuna ekleyin.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Azure SQL veritabanında bir saklı yordam oluşturma 

Azure SQL veritabanınızda bir saklı yordam oluşturmak için aşağıdaki komutu çalıştırın. Bu saklı yordam, her işlem hattı çalıştırmasından sonra eşik değerini güncelleştirir. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-azure-sql-database"></a>Azure SQL veritabanında veri türleri ve ek saklı yordamlar oluşturma
Azure SQL veritabanınızda iki saklı yordam ve iki veri türü oluşturmak için aşağıdaki sorguyu çalıştırın. Bunlar, kaynak tablodaki verileri hedef tablolarla birleştirmek için kullanılır.

Yolculuğun başlamasını kolaylaştırmak için, bu saklı yordamları doğrudan bir tablo değişkeni aracılığıyla içindeki Delta verileri geçirerek ve ardından bunları hedef depoda birleştirerek kullanırız. Tablo değişkeninde bir "büyük" sayıda Delta satırı (100 ' den fazla) beklenmediğinden emin olun.  

Hedef depoda çok sayıda Delta satırını birleştirmeniz gerekiyorsa, tüm Delta verileri önce hedef depodaki geçici bir "hazırlama" tablosuna kopyalamak için kopyalama etkinliği 'ni kullanmanızı ve ardından tablo VARI ' nı kullanmadan kendi saklı yordamınıza sahip olup olmadığını öneririz bunları "hazırlama" tablosundan "son" tablosuna birleştirebiliyor. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
2. Sol menüde **kaynak oluştur** > **analiz** > **Data Factory**' yı seçin: 
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)

3. **Yeni veri fabrikası** sayfasında **ad** için **ADFMultiIncCopyTutorialDF** adını girin. 
 
   Azure data factory adı **küresel olarak benzersiz** olmalıdır. Aşağıdaki hatayla birlikte kırmızı bir ünlem işareti görürseniz veri fabrikasının adını değiştirin (örneğin adınızADFIncCopyTutorialDF) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.
  
   `Data factory name "ADFIncCopyTutorialDF" is not available`

4. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin. 
5. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
    - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin. 
    - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
6. **Sürüm** için **V2**'yi seçin.
7. Data factory için **konum** seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.
8. **Oluştur**'a tıklayın.      
9. Oluşturma işlemi tamamlandıktan sonra, resimde gösterildiği gibi **Data Factory** sayfasını görürsünüz.
   
   ![Data factory giriş sayfası](./media/doc-common-process/data-factory-home-page.png)
10. Azure Data Factory kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **Yazar ve İzleyici** kutucuğuna tıklayın.

## <a name="create-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma
Özel bir ağda yer alan (şirket içi) bir veri deposundaki verileri bir Azure veri deposuna taşırken, şirket içi ortamınıza şirket içinde barındırılan bir tümleştirme çalışma zamanı (IR) yükleyin. Şirket içinde barındırılan IR, özel ağınız ile Azure arasında veri taşır. 

1. Sol bölmenin altından **Bağlantılar**’a tıklayın ve **Bağlantılar** penceresinde **Tümleştirme Çalışma Zamanları**’na geçin. 

1. **Tümleştirme Çalışma Zamanları** sekmesinde **+ Yeni**’ye tıklayın. 

1. **Integration Runtime kurulum** penceresinde, **veri taşıma ve dağıtım etkinliklerini dış hesaplamalar**' ı seçin ve **devam**' a tıklayın. 

1. **Şirket Içinde barındırılan**' i seçin ve **devam**' a tıklayın. 
1. **Ad**Için **Myselfhostedır** girin ve **Oluştur**' a tıklayın. 

1. **1. Seçenek: Hızlı kurulum** bölümünde **Bu bilgisayarda hızlı kurulumu başlatmak için buraya tıklayın** seçeneğine tıklayın. 

   ![Hızlı kurulum bağlantısına tıklayın](./media/tutorial-incremental-copy-multiple-tables-portal/click-express-setup.png)
1. **Tümleştirme Çalışma Zamanı (Şirket İçinde Barındırılan) Hızlı Kurulum** penceresinde **Kapat**’a tıklayın. 

   ![Tümleştirme çalışma zamanı kurulumu - başarılı](./media/tutorial-incremental-copy-multiple-tables-portal/integration-runtime-setup-successful.png)
1. Web tarayıcısında, **Tümleştirme Çalışma Zamanı Kurulumu** penceresinde **Son**’a tıklayın. 

 
1. Tümleştirme çalışma zamanları listesinde **MySelfHostedIR** öğesini gördüğünüzü doğrulayın.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu bölümde, şirket içi SQL Server veritabanı ve Azure SQL veritabanı hesabınızla bağlı hizmetler oluşturacaksınız. 

### <a name="create-the-sql-server-linked-service"></a>SQL Server bağlı hizmet oluşturma
Bu adımda, şirket içi SQL Server veritabanınızı veri fabrikasına bağlarsınız.

1. **Bağlantılar** penceresinde **Tümleştirme Çalışma Zamanları** sekmesinden **Bağlı Hizmetler** sekmesine geçip **+ Yeni**’ye tıklayın.

1. **Yeni Bağlı Hizmet** penceresinde **SQL Server**’ı seçip **Devam**’a tıklayın. 

1. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin:

    1. **Ad** için **SqlServerLinkedService** adını girin. 
    1. **Tümleştirme çalışma zamanı aracılığıyla bağlan** için **MySelfHostedIR** seçeneğini belirleyin. Bu **önemli** bir adımdır. Varsayılan tümleştirme çalışma zamanı bir şirket içi veri deposuna bağlanamaz. Daha önce oluşturduğunuz şirket içinde barındırılan tümleştirme çalışma zamanını kullanın. 
    1. **Sunucu adı** olarak SQL Server veritabanını içeren bilgisayarınızın adını girin.
    1. **Veritabanı adı** olarak SQL Server’ınızdaki kaynak verileri içeren veritabanının adını girin. Ön koşulların bir parçası olarak bir tablo oluşturdunuz ve bu veritabanına veri eklediniz. 
    1. **Kimlik doğrulama türü** alanına veritabanına bağlanmak için kullanmak istediğiniz **kimlik doğrulama türünü** seçin. 
    1. **Kullanıcı adı** alanına SQL Server veritabanına erişimi olan kullanıcının adını girin. Kullanıcı hesabınızda veya sunucu adında eğik çizgi karakteri (`\`) kullanmanız gerekirse kaçış karakterini (`\`) kullanın. `mydomain\\myuser` bunun bir örneğidir.
    1. **Parola** alanına kullanıcının **parolasını** girin. 
    1. Data Factory’nin SQL Server veritabanınıza bağlanıp bağlanamadığını test etmek için **Bağlantıyı sına**’ya tıklayın. Bağlantı başarılı olana kadar tüm hataları düzeltin. 
    1. Bağlı hizmeti kaydetmek için **son**' a tıklayın.

### <a name="create-the-azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmetini oluşturun
Son adımda, kaynak SQL Server veritabanınızı veri fabrikasına bağlamak için bağlı bir hizmet oluşturursunuz. Bu adımda, hedef/havuz Azure SQL veritabanınızı veri fabrikasına bağlarsınız. 

1. **Bağlantılar** penceresinde **Tümleştirme Çalışma Zamanları** sekmesinden **Bağlı Hizmetler** sekmesine geçip **+ Yeni**’ye tıklayın.
1. **Yeni Bağlı Hizmet** penceresinde **Azure SQL Veritabanı**’nı seçip **Devam**’a tıklayın. 
1. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin:

    1. **Ad** için **AzureSqlDatabaseLinkedService** adını girin. 
    1. **Sunucu adı** alanına açılan listeden Azure SQL Server’ınızın adını seçin. 
    1. **Veritabanı adı** alanına, ön koşulların bir parçası olarak içinde customer_table ve project_table tablolarını oluşturduğunuz Azure SQL veritabanını girin. 
    1. **Kullanıcı adı** alanına SQL Server veritabanına erişimi olan kullanıcının adını girin. 
    1. **Parola** alanına kullanıcının **parolasını** girin. 
    1. Data Factory’nin SQL Server veritabanınıza bağlanıp bağlanamadığını test etmek için **Bağlantıyı sına**’ya tıklayın. Bağlantı başarılı olana kadar tüm hataları düzeltin. 
    1. Bağlı hizmeti kaydetmek için **son**' a tıklayın.

1. Listede iki bağlı hizmet gördüğünüzü doğrulayın. 
   
    ![İki bağlı hizmet](./media/tutorial-incremental-copy-multiple-tables-portal/two-linked-services.png) 

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda veri kaynağı, veri hedefi ve eşiğin depolanacağı yeri temsil eden veri kümeleri oluşturacaksınız.

### <a name="create-a-source-dataset"></a>Kaynak veri kümesi oluşturma

1. Sol bölmede, **+ (artı)** düğmesine ve sonra **Veri Kümesi**’ne tıklayın.

1. **Yeni veri kümesi** penceresinde **SQL Server**' yi seçin ve **devam**' a tıklayın. 

1. Web tarayıcısında veri kümesinin yapılandırılması için yeni bir sekme açıldığını görürsünüz. Ağaç görünümünde de bir veri kümesi görürsünüz. Alttaki Özellikler penceresinin **Genel** sekmesinde **Ad** için **SourceDataset** adını girin. 

1. Özellikler penceresinin **Bağlantı** sekmesine geçin ve **Bağlı hizmet** için **SqlServerLinkedService** hizmetini seçin. Burada bir tablo seçmezsiniz. İşlem hattındaki Kopyalama etkinliği, tüm tabloyu yüklemek yerine verileri yüklemek için bir SQL sorgusu kullanır.

   ![Kaynak veri kümesi - bağlantı](./media/tutorial-incremental-copy-multiple-tables-portal/source-dataset-connection.png)


### <a name="create-a-sink-dataset"></a>Havuz veri kümesi oluşturma
1. Sol bölmede, **+ (artı)** düğmesine ve sonra **Veri Kümesi**’ne tıklayın.

1. **Yeni veri kümesi** PENCERESINDE **Azure SQL veritabanı**' nı seçin ve **devam**' a tıklayın. 

1. Web tarayıcısında veri kümesinin yapılandırılması için yeni bir sekme açıldığını görürsünüz. Ağaç görünümünde de bir veri kümesi görürsünüz. Alttaki Özellikler penceresinin **Genel** sekmesinde, **Ad** için **SinkDataset** adını girin.

1. Özellikler penceresinin **Parametreler** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Parametre oluştur/güncelleştir** bölümünde **+ Yeni**’ye tıklayın. 
    1. **Ad** alanına **SinkTableName**, **tür** alanına **String** değerini girin. Bu veri kümesi, **SinkTableName** değerini bir parametre olarak alır. SinkTableName parametresi, çalışma zamanında dinamik olarak işlem hattı tarafından ayarlanır. İşlem hattındaki ForEach etkinliği, tablo adlarının bir listesi üzerinden yinelenir ve her yinelemede tablo adını bu veri kümesine geçirir.
   
    ![Havuz Veri Kümesi - özellikler](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-parameters.png)
1. Özellikler penceresi **bağlantı** sekmesine geçin ve **bağlı hizmet**Için **azuressqldatabaselinkedservice** ' i seçin. **Table** özelliği için **Dinamik içerik ekle**'ye tıklayın.   
    
1. **Dinamik Içerik Ekle** penceresinde **Parametreler** bölümünde **sinktablename** ' ı seçin. 
 
1. **Son**' a tıkladıktan sonra, "@dataset() görürsünüz. SinkTableName "Tablo adı olarak.

   ![Havuz Veri Kümesi - bağlantı](./media/tutorial-incremental-copy-multiple-tables-portal/sink-dataset-connection-completion.png)

### <a name="create-a-dataset-for-a-watermark"></a>Eşik için veri kümesi oluşturma
Bu adımda üst eşik değerini depolamak için bir veri kümesi oluşturacaksınız. 

1. Sol bölmede, **+ (artı)** düğmesine ve sonra **Veri Kümesi**’ne tıklayın.

1. **Yeni veri kümesi** PENCERESINDE **Azure SQL veritabanı**' nı seçin ve **devam**' a tıklayın. 

1. Alttaki Özellikler penceresinin **Genel** sekmesinde, **Ad** için **WatermarkDataset** adını girin.
1. **Bağlantı** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Bağlı hizmet** için **AzureSqlDatabaseLinkedService** hizmetini seçin.
    1. **Tablo** için **[dbo].[watermarktable]** seçeneğini belirleyin.

    ![Filigran Veri Kümesi - bağlantı](./media/tutorial-incremental-copy-multiple-tables-portal/watermark-dataset-connection.png)

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu işlem hattı parametre olarak tablo adları listesini alır. ForEach etkinliği, tablo adları listesi üzerinden yinelenir ve aşağıdaki işlemleri gerçekleştirir: 

1. Eski eşik değerini (ilk değer veya son yinelemede kullanılan değer) almak için Arama etkinliğini kullanın.

1. Yeni eşik değerini (kaynak tablodaki eşik sütununda bulunan en yüksek değer) almak için Arama etkinliğini kullanın.

1. Bu iki eşik değeri arasında kaynak veritabanından hedef veritabanına veri kopyalamak için Kopyalama etkinliğini kullanın.

1. Bir sonraki yinelemede kullanılacak eski eşik değerini güncelleştirmek için StoredProcedure etkinliğini kullanın. 

### <a name="create-the-pipeline"></a>İşlem hattını oluşturma

1. Sol bölmede, **+ (artı)** düğmesine ve sonra da **İşlem Hattı**’na tıklayın.

1. **Genel** sekmesinde, **ad**için **ıncrementalcopypipeline** girin. 

1. **Parametreler** sekmesinde aşağıdaki adımları uygulayın: 

    1. **+ Yeni** öğesine tıklayın. 
    1. **name** parametresi için **tableList** girin. 
    1. Parametre **türü**için **dizi** ' ı seçin.

1. **Etkinlikler** araç kutusunda **Yineleme ve Koşullar**’ı genişletin ve **ForEach** etkinliğini sürükleyerek işlem hattı tasarımcısı yüzeyine bırakın. **Özellikler** penceresinin **Genel** sekmesinde **IterateSQLTables** girin. 

1. **Ayarlar** sekmesine geçin ve `@pipeline().parameters.tableList`Öğeler**için** değerini girin. ForEach etkinliği, bir tablo listesi üzerinden yinelenir ve artımlı kopyalama işlemini gerçekleştirir. 

    ![ForEach etkinliği - ayarlar](./media/tutorial-incremental-copy-multiple-tables-portal/foreach-settings.png)

1. İşlem hattında **ForEach** etkinliği zaten seçili değilse bunu seçin. **Düzenle (Kalem simgesi)** düğmesine tıklayın.

1. **Etkinlikler** araç kutusunda **Genel**’i genişletin, **Arama** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın ve **Ad** için **LookupOldWaterMarkActivity** girin.

1. **Özellikler** penceresinin **Ayarlar** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Kaynak Veri Kümesi** için **WatermarkDataset**’i seçin.
    1. **Sorgu Kullan** için **Sorgu**’yu seçin. 
    1. **Sorgu** için aşağıdaki SQL sorgusunu girin. 

        ```sql
        select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'
        ```

        ![İlk Arama Etkinliği - ayarlar](./media/tutorial-incremental-copy-multiple-tables-portal/first-lookup-settings.png)
1. **Etkinlikler** araç kutusundan **Arama** etkinliğini sürükleyip bırakın ve **Ad** için **LookupNewWaterMarkActivity** adını girin.
        
1. **Ayarlar** sekmesine geçin.

    1. **Kaynak Veri Kümesi** için **SourceDataset**’i seçin. 
    1. **Sorgu Kullan** için **Sorgu**’yu seçin.
    1. **Sorgu** için aşağıdaki SQL sorgusunu girin.

        ```sql    
        select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}
        ```
    
        ![İkinci Arama Etkinliği - ayarlar](./media/tutorial-incremental-copy-multiple-tables-portal/second-lookup-settings.png)
1. **Etkinlikler** araç kutusundan **Kopyalama** etkinliğini sürükleyip bırakın ve **Ad** için **IncrementalCopyActivity** adını girin. 

1. **Arama** etkinliklerini tek tek **Kopyalama** etkinliğine bağlayın. Bağlanmak için **Arama** etkinliğine bağlı **yeşil** kutuyu sürüklemeye başlayın ve **Kopyalama** etkinliğinin üzerine başlayın. Kopyalama etkinliğinin kenarlık rengi **mavi** olduğunda fare düğmesini bırakın.

    ![Arama etkinliklerini Kopyalama etkinliğine bağlama](./media/tutorial-incremental-copy-multiple-tables-portal/connect-lookup-to-copy.png)
1. İşlem hattında **Kopyalama** etkinliğini seçin. **Özellikler** penceresinin **Kaynak** sekmesine geçin. 

    1. **Kaynak Veri Kümesi** için **SourceDataset**’i seçin. 
    1. **Sorgu Kullan** için **Sorgu**’yu seçin. 
    1. **Sorgu** için aşağıdaki SQL sorgusunu girin.

        ```sql
        select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'        
        ```

        ![Kopyalama Etkinliği - kaynak ayarları](./media/tutorial-incremental-copy-multiple-tables-portal/copy-source-settings.png)
1. **Havuz** sekmesine geçin ve **Havuz Veri Kümesi** alanı için **SinkDataset**’i seçin. 
        
1. Aşağıdaki adımları uygulayın:

    1. **DataSet özelliklerinde**, **sinktablename** parametresi için `@{item().TABLE_NAME}`girin.
    1. **Saklı yordam adı** özelliği için `@{item().StoredProcedureNameForMergeOperation}`girin.
    1. **Tablo türü** özelliği için `@{item().TableType}`girin.
    1. **Tablo türü parametre adı**için `@{item().TABLE_NAME}`girin.

    ![Kopyalama Etkinliği - parametreler](./media/tutorial-incremental-copy-multiple-tables-portal/copy-activity-parameters.png)
1. **Etkinlikler** araç kutusundan **Saklı Yordam** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. **Kopyalama** etkinliğini **Saklı Yordam** etkinliğine bağlayın. 

1. İşlem hattında **Saklı Yordam** etkinliğini seçin ve **Özellikler** penceresinin **Genel** sekmesinde **Ad** alanına **StoredProceduretoWriteWatermarkActivity** adını girin. 

1. **SQL Hesabı** sekmesine geçin ve **Bağlı Hizmet** için **AzureSqlDatabaseLinkedService** seçeneğini belirleyin.

    ![Saklı Yordam Etkinliği - SQL Hesabı](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sql-account.png)
1. **Saklı Yordam** sekmesine geçin ve aşağıdaki adımları uygulayın:

    1. **Saklı yordam adı** için `[dbo].[usp_write_watermark]` öğesini seçin. 
    1. **Parametreyi içeri aktar**’ı seçin. 
    1. Parametreler için aşağıdaki değerleri belirtin: 

        | Ad | Tür | Değer | 
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | `@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}` |
        | TableName | Dize | `@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}` |
    
        ![Saklı Yordam Etkinliği - saklı yordam ayarları](./media/tutorial-incremental-copy-multiple-tables-portal/sproc-activity-sproc-settings.png)
1. Oluşturduğunuz varlıkları Data Factory hizmetine yayımlamak için **Tümünü Yayımla** ' yı seçin. 

1. **Başarıyla yayımlandı** iletisini görene kadar bekleyin. Bildirimleri görmek için **Bildirimleri Göster** bağlantısına tıklayın. **X** simgesine tıklayarak bildirim penceresini kapatın.

 
## <a name="run-the-pipeline"></a>İşlem hattını çalıştırma

1. İşlem hattının araç çubuğunda **tetikleyici Ekle**' ye tıklayın ve **Şimdi Tetikle**' ye tıklayın.     

1. **İşlem Hattı Çalıştırma** penceresinde **tableList** parametresi için aşağıdaki değeri girip **Son**’a tıklayın. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

    ![İşlem Hattı Çalıştırma bağımsız değişkenleri](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-run-arguments.png)

## <a name="monitor-the-pipeline"></a>İşlem hattını izleme

1. Soldaki **İzleyici** sekmesine geçin. **El ile tetikleme** yoluyla tetiklenen işlem hattı çalıştırmasını görürsünüz. Listeyi yenilemek için **Yenile** düğmesine tıklayın. **Eylemler** sütunundaki bağlantılar, işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemenize ve işlem hattını yeniden çalıştırmanıza imkan tanır. 

    ![İşlem hattı çalıştırmaları](./media/tutorial-incremental-copy-multiple-tables-portal/pipeline-runs.png)
1. **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Görüntüle** bağlantısına tıklayın. Seçili işlem hattı çalıştırmasıyla ilişkili tüm etkinlik çalıştırmalarını görürsünüz. 

## <a name="review-the-results"></a>Sonuçları gözden geçirin
Verilerin kaynak tablolardan hedef tablolara kopyalandığını doğrulamak için, SQL Server Management Studio’da SQL veritabanında aşağıdaki sorguları çalıştırın: 

**Sorgu** 
```sql
select * from customer_table
```

**Çıktı**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Sorgu**

```sql
select * from project_table
```

**Çıktı**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Sorgu**

```sql
select * from watermarktable
```

**Çıktı**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Her iki tablonun da eşik değerlerinin güncelleştirildiğine dikkat edin. 

## <a name="add-more-data-to-the-source-tables"></a>Kaynak tablolara daha fazla veri ekleme

customer_table içerisindeki mevcut bir satırı güncelleştirmek için aşağıdaki sorguyu kaynak SQL Server veritabanında çalıştırın. Project_table içine yeni bir satır ekleyin. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>İşlem hattını yeniden çalıştırma
1. Web tarayıcısı penceresinde, soldaki **Düzenle** sekmesine geçin. 
1. İşlem hattının araç çubuğunda **tetikleyici Ekle**' ye tıklayın ve **Şimdi Tetikle**' ye tıklayın.   
1. **İşlem Hattı Çalıştırma** penceresinde **tableList** parametresi için aşağıdaki değeri girip **Son**’a tıklayın. 

    ```
    [
        {
            "TABLE_NAME": "customer_table",
            "WaterMark_Column": "LastModifytime",
            "TableType": "DataTypeforCustomerTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
        },
        {
            "TABLE_NAME": "project_table",
            "WaterMark_Column": "Creationtime",
            "TableType": "DataTypeforProjectTable",
            "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
        }
    ]
    ```

## <a name="monitor-the-pipeline-again"></a>İşlem hattını yeniden izleme

1. Soldaki **İzleyici** sekmesine geçin. **El ile tetikleme** yoluyla tetiklenen işlem hattı çalıştırmasını görürsünüz. Listeyi yenilemek için **Yenile** düğmesine tıklayın. **Eylemler** sütunundaki bağlantılar, işlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemenize ve işlem hattını yeniden çalıştırmanıza imkan tanır. 

1. **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Görüntüle** bağlantısına tıklayın. Seçili işlem hattı çalıştırmasıyla ilişkili tüm etkinlik çalıştırmalarını görürsünüz. 

## <a name="review-the-final-results"></a>Son sonuçları gözden geçirme
SQL Server Management Studio, güncelleştirilmiş/yeni verilerin kaynak tablolardan hedef tablolara kopyalandığını doğrulamak için, hedef SQL veritabanında aşağıdaki sorguları çalıştırın. 

**Sorgu** 
```sql
select * from customer_table
```

**Çıktı**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

3 numaraya ait **PersonID** için yeni **Name** ve **LastModifytime** değerlerine dikkat edin. 

**Sorgu**

```sql
select * from project_table
```

**Çıktı**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

**NewProject** girişinin project_table tablosuna eklendiğine dikkat edin. 

**Sorgu**

```sql
select * from watermarktable
```

**Çıktı**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Her iki tablonun da eşik değerlerinin güncelleştirildiğine dikkat edin.
     
## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide aşağıdaki adımları gerçekleştirdiniz: 

> [!div class="checklist"]
> * Kaynak ve hedef veri depolarını hazırlayın.
> * Veri fabrikası oluşturma.
> * Şirket içinde barındırılan tümleştirme çalışma (IR) zamanı oluşturun.
> * Tümleştirme çalışma zamanını yükleyin.
> * Bağlı hizmet oluşturma. 
> * Kaynak, havuz ve eşik veri kümeleri oluşturun.
> * İşlem hattını oluşturma, çalıştırma ve izleme.
> * Sonuçları gözden geçirin.
> * Kaynak tablolarına veri ekleyin veya bu verileri güncelleştirin.
> * İşlem hattını yeniden çalıştırın ve izleyin.
> * Son sonuçları gözden geçirin.

Azure üzerinde bir Spark kümesi kullanarak veri dönüştürme hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Değişiklik İzleme teknolojisini kullanarak Azure SQL Veritabanından Azure Blob depolama alanına verileri artımlı olarak yükleme](tutorial-incremental-copy-change-tracking-feature-portal.md)


