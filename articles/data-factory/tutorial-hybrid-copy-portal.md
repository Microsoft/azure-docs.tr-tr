---
title: Azure portal kullanarak SQL Server verileri blob depolamaya kopyalama
description: Azure Data Factory’de şirket içinde barındırılan tümleştirme çalışma zamanını kullanarak şirket içi veri deposundan buluta veri kopyalama hakkında bilgi edinin.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4bfbd83f3f3910e1231bcce4043d9b59ccc512db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606659"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>SQL Server veritabanından Azure Blob depolama alanına veri kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, verileri bir SQL Server veritabanından Azure Blob depolama alanına kopyalayan bir veri fabrikası işlem hattı oluşturmak için Azure Data Factory Kullanıcı arabirimini (UI) kullanırsınız. Verileri şirket içi ile bulut veri depoları arasında taşıyan, şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturup kullanabilirsiniz.

> [!NOTE]
> Bu makale, Data Factory’ye giriş konusunda ayrıntılı bilgi sağlamaz. Daha fazla bilgi için bkz. [Data Factory'ye giriş](introduction.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma.
> * SQL Server ve Azure Depolama bağlı hizmetlerini oluşturma.
> * SQL Server ve Azure Blob veri kümeleri oluşturma.
> * Verileri taşımak için kopyalama etkinliği ile işlem hattı oluşturma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı çalıştırmasını izleme.

## <a name="prerequisites"></a>Önkoşullar
### <a name="azure-subscription"></a>Azure aboneliği
Başlamadan önce, mevcut bir Azure aboneliğiniz yoksa [ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Azure rolleri
Veri fabrikası örnekleri oluşturmak için Azure’da oturum açarken kullandığınız kullanıcı hesabına *Katkıda bulunan* veya *Sahip* rolü atanmalı ya da bu hesap Azure aboneliğinin *yöneticisi* olmalıdır.

Abonelikte sahip olduğunuz izinleri görüntülemek için Azure portalına gidin. Sağ üst köşeden kullanıcı adınızı ve sonra **İzinler**’i seçin. Birden çok aboneliğe erişiminiz varsa uygun aboneliği seçin. Bir role kullanıcı eklemeye ilişkin örnek yönergeler için, bkz. [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 ve 2017
Bu öğreticide, bir SQL Server veritabanını *kaynak* veri deposu olarak kullanırsınız. Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri bu SQL Server veritabanından (kaynak) BLOB depolama alanına (havuz) kopyalar. Daha sonra SQL Server veritabanınızda **"adlı bir** tablo oluşturur ve tabloya birkaç örnek girdi eklersiniz.

1. SQL Server Management Studio’yu başlatın. Makinenizde zaten yüklü değilse [SQL Server Management Studio'yu indirme](/sql/ssms/download-sql-server-management-studio-ssms) sayfasına gidin.

1. Kimlik bilgilerinizi kullanarak SQL Server örneğinize bağlanın.

1. Örnek bir veritabanı oluşturun. Ağaç görünümünde **Veritabanları**'na sağ tıklayın ve **Yeni Veritabanı**'nı seçin.
1. **Yeni Veritabanı** penceresinde, veritabanı için bir ad girin ve **Tamam**'ı seçin.

1. Çalışan **tablosunu oluşturmak** ve içine bazı örnek verileri eklemek için veritabanında aşağıdaki sorgu betiğini çalıştırın. Ağaç görünümünde, oluşturduğunuz veritabanına sağ tıklayın ve **Yeni Sorgu**'yu seçin.

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

### <a name="azure-storage-account"></a>Azure depolama hesabı
Bu öğreticide, genel amaçlı Azure depolama hesabını (özel olarak Blob depolama) hedef/havuz veri deposu olarak kullanırsınız. Genel amaçlı bir Azure depolama hesabınız yoksa bkz. [Depolama hesabı oluşturma](../storage/common/storage-account-create.md). Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri SQL Server veritabanından (kaynak) BLOB depolama alanına (havuz) kopyalar. 

#### <a name="get-the-storage-account-name-and-account-key"></a>Depolama hesabı adını ve hesap anahtarını alma
Bu öğreticide, depolama hesabınızın adını ve anahtarını kullanırsınız. Depolama hesabınızın adını ve anahtarını almak için aşağıdaki adımları gerçekleştirin:

1. Azure kullanıcı adı ve parolanızla [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Depolama** anahtar sözcüğünü kullanarak filtreleyin ve **Depolama hesapları**’nı seçin.

    ![Depolama hesabı araması](media/doc-common-process/search-storage-account.png)

1. Depolama hesapları listesinde, depolama hesabınız için gerekiyorsa filtreleyin. Sonra depolama hesabınızı seçin.

1. **Depolama hesabı** penceresinde **Erişim anahtarları**'nı seçin.

1. **Depolama hesabı adı** ve **key1** kutularında değerleri kopyalayın ve ardından onları öğreticide daha sonra kullanmak için Not Defteri'ne veya başka bir düzenleyiciye yapıştırın.

#### <a name="create-the-adftutorial-container"></a>Adftutorial kapsayıcını oluşturma
Bu bölümde, Blob depolama alanınızda **adftutorial** adlı bir blob kapsayıcısı oluşturursunuz.

1. **Depolama hesabı** penceresinde **genel bakış**' a gidin ve **kapsayıcılar**' ı seçin.

    ![Bloblar seçeneğini belirleyin](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. **Kapsayıcılar** penceresinde, yeni bir tane oluşturmak Için **+ kapsayıcı** ' yı seçin.

1. **Yeni kapsayıcı** penceresinde, **Ad** bölümüne **adftutorial** adını girin. Ardından **Oluştur**’u seçin.

1. Kapsayıcılar listesinde, az önce oluşturduğunuz **adföğreticisi** ' ni seçin.

1. **Adföğreticisi** için **kapsayıcı** penceresini açık tutun. Öğreticinin sonundaki çıktıyı doğrulamak için bunu kullanırsınız. Data Factory bu kapsayıcıda çıktı klasörünü otomatik olarak oluşturduğundan sizin oluşturmanız gerekmez.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Bu adımda, bir veri fabrikası oluşturacak ve veri fabrikasında bir işlem hattı oluşturmak için Data Factory kullanıcı arabirimini başlatacaksınız.

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. Sol menüde, **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin:

   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)

1. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

   Veri fabrikasının adı *genel olarak benzersiz* olmalıdır. Ad alanı için aşağıdaki hata iletisini görürseniz veri fabrikasının adını değiştirin (örneğin, adınızADFTutorialDataFactory). Data Factory yapıtlarının adlandırma kuralları için bkz.[Data Factory adlandırma kuralları](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Yinelenen ad için yeni veri fabrikası hata iletisi.":::

1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.
        
     Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** bölümünde veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Data Factory tarafından kullanılan veri depoları (örneğin, Depolama ve SQL Veritabanı) ve işlemler (örneğin, Azure HDInsight) başka bölgelerde olabilir.
1. **Oluştur**’u seçin.

1. Oluşturma işlemi tamamlandıktan sonra, görüntüde gösterildiği gibi **Data Factory** sayfasını görürsünüz:

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::
1. Data Factory kullanıcı arabirimini ayrı bir sekmede açmak için **Geliştir ve İzle** kutucuğunu seçin.


## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

1. **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin. Sizin için otomatik olarak bir işlem hattı oluşturulur. İşlem hattının ağaç görünümünde yer aldığını ve düzenleyicisinin açık olduğunu görürsünüz.

   ![Başlayalım sayfası](./media/doc-common-process/get-started-page.png)

1. Genel panelinde **Özellikler** altında, **ad** Için **sqlservertoblobpipeline** ' ı belirtin. Sonra sağ üst köşedeki Özellikler simgesine tıklayarak paneli daraltın.

1. **Etkinlikler** araç kutusunda **Taşı & Dönüştür**' ü genişletin. **Kopyalama** etkinliğini kopyalayıp işlem hattı tasarım yüzeyine bırakın. Etkinliğin adını **CopySqlServerToAzureBlobActivity** olarak ayarlayın.

1. **Özellikler** penceresinde **Kaynak** sekmesine gidin ve **+ Yeni**’yi seçin.

1. **Yeni veri kümesi** iletişim kutusunda **SQL Server** aratın. **SQL Server**' yi seçin ve ardından **devam**' ı seçin.
    ![Yeni SqlServer veri kümesi](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. **Özellikleri ayarla** Iletişim kutusundaki **ad**' ın altına **sqlserverdataset** adını girin. **Bağlı hizmet** altında **+ Yeni**' yi seçin. Bu adımda, kaynak veri deposuna (SQL Server veritabanı) yönelik bir bağlantı oluşturursunuz.

1. **Yeni bağlı hizmet** Iletişim kutusunda **adı** **sqlserverlinkedservice** olarak ekleyin. **Tümleştirme çalışma zamanı aracılığıyla Bağlan** altında **+ Yeni**' yi seçin.  Bu bölümde, şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturur ve SQL Server veritabanını içeren bir şirket içi makine ile ilişkilendirirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanı, makinenizdeki SQL Server veitabanınızdaki verileri Blob depolama alanına kopyalayan bileşendir.

1. **Integration Runtime kurulum** iletişim kutusunda, **Şirket içinde barındırılan**' i seçin ve ardından **devam**' ı seçin.

1. Ad alanına **TutorialIntegrationRuntime** girin. Ardından **Oluştur**’u seçin.

1. Ayarlar için, **Bu bilgisayarın hızlı kurulumunu başlatmak üzere buraya tıklayın ' ı** seçin. Bu işlem, tümleştirme çalışma zamanını makinenize yükler ve Data Factory’ye kaydeder. Alternatif olarak, el ile kurulum seçeneğini kullanarak yükleme dosyasını indirip çalıştırabilir ve anahtarı kullanarak tümleştirme çalışma zamanını kaydedebilirsiniz.
    ![Tümleştirme çalışma zamanı kurulumu](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. **Integration Runtime (Şirket içinde barındırılan) Express kurulum** penceresinde, Işlem bittiğinde **Kapat** ' ı seçin.

    ![Integration runtime (şirket içinde barındırılan) hızlı kurulum](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. **Yeni bağlı hizmet (SQL Server)** iletişim kutusunda, **tümleştirme çalışma zamanı aracılığıyla Bağlan** altında **TutorialIntegrationRuntime** öğesinin seçili olduğunu doğrulayın. Ardından, aşağıdaki adımları uygulayın:

    a. **Ad** bölümüne **SqlServerLinkedService** adını girin.

    b. **Sunucu adı** bölümüne SQL Server örneğinizin adını girin.

    c. **Veritabanı adı** alanında, **emp** tablosunu içeren veritabanının adını belirtin.

    d. **Kimlik doğrulaması türü** bölümünde, Data Factory’nin SQL Server veritabanınıza bağlanmak için kullanması gereken uygun kimlik doğrulaması türünü seçin.

    e. **Kullanıcı adı** ve **Parola** bölümlerine kullanıcı adını ve parolasını girin. Gerekirse Kullanıcı adı olarak *etkialanım \\ MyUser* kullanın.

    f. **Bağlantıyı sına**’yı seçin. Bu adım, Data Factory oluşturduğunuz şirket içinde barındırılan tümleştirme çalışma zamanını kullanarak SQL Server veritabanınıza bağlanabildiğini doğrulamadır.

    örneğin: Bağlı hizmeti kaydetmek için **Oluştur**' u seçin.
 
    ![Yeni bağlı hizmet (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. Bağlı hizmet oluşturulduktan sonra, SqlServerDataset için **Özellikleri ayarla** sayfasına geri dönebilirsiniz. Aşağıdaki adımları izleyin:

    a. **Bağlı hizmet** bölümünde **SqlServerLinkedService**’i gördüğünüzü onaylayın.

    b. **Tablo adı** bölümünde **[dbo] öğesini seçin. [ i]**.
    
    c. **Tamam**’ı seçin.

1. **SQLServerToBlobPipeline**’ı içeren sekmeye gidin veya ağaç görünümünden **SQLServerToBlobPipeline**’ı seçin.

1. **Özellikler** penceresinin altındaki **Havuz** sekmesine gidin ve **+ Yeni**’yi seçin.

1. **Yeni veri kümesi** Iletişim kutusunda **Azure Blob depolama**' yı seçin. Daha sonra **Devam** seçeneğini belirleyin.

1. **Biçim Seç** iletişim kutusunda verilerinizin biçim türünü seçin. Daha sonra **Devam** seçeneğini belirleyin.

    ![Veri biçimi seçimi](./media/doc-common-process/select-data-format.png)

1. **Özellikleri ayarla** Iletişim kutusunda ad için **AzureBlobDataset** girin. **Bağlı hizmet** metin kutusunun yanındaki **+ Yeni** seçeneğini belirleyin.

1. **Yeni bağlı hizmet (Azure Blob depolama)** Iletişim kutusunda **AzureStorageLinkedService** as Name yazın, **depolama** hesabı adı listesinden depolama hesabınızı seçin. Bağlantıyı test edin ve ardından bağlı hizmeti dağıtmak için **Oluştur** ' u seçin.

1. Bağlı hizmet oluşturulduktan sonra **Özellikleri ayarla** sayfasına geri dönebilirsiniz. **Tamam**’ı seçin.

1. Havuz veri kümesini açın. **Bağlantı** sekmesinde aşağıdaki adımları uygulayın:

    a. **Bağlı hizmet** bölümünde **AzureStorageLinkedService**’in seçildiğini onaylayın.

    b. **Dosya yolu**' nda, **kapsayıcı/Dizin** bölümü için **adföğreticisi/fromonpred** girin. Çıktı klasörü adftutorial kapsayıcısında mevcut değilse Data Factory tarafından otomatik olarak oluşturulur.

    c. **Dosya** bölümü için **dinamik içerik Ekle**' yi seçin.
    ![Dosya adını çözümlemek için dinamik ifade](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Ekle `@CONCAT(pipeline().RunId, '.txt')` ' yi ve ardından **son**' u seçin. Bu eylem, dosyayı PipelineRunID.txt olarak yeniden adlandıracak.

1. İşlem hattının açık olduğu sekmeye gidin veya ağaç görünümünde işlem hattını seçin. **Havuz Veri Kümesi** bölümünde **AzureBlobDataset**’in seçili olduğunu onaylayın.

1. İşlem hattı ayarlarını doğrulamak için işlem hattının araç çubuğunda **Doğrula**’yı seçin. **Kanal doğrulama çıkışını** kapatmak için **>>** simgeyi seçin.
    ![işlem hattını doğrulama](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Data Factory için oluşturduğunuz varlıkları yayımlamak için **Tümünü Yayımla**' yı seçin.

1. **Yayımlama tamamlandı** açılır penceresini görene kadar bekleyin. Yayımlamanın durumunu denetlemek için pencerenin üst kısmındaki **bildirimleri göster** bağlantısını seçin. Bildirim penceresini kapatmak için **Kapat**’ı seçin.


## <a name="trigger-a-pipeline-run"></a>İşlem hattı çalıştırmasını tetikleme
İşlem hattının araç çubuğunda **tetikleyici Ekle** ' yi seçin ve sonra **Şimdi Tetikle**' yi seçin.

## <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. **İzleyici** sekmesine gidin. Önceki adımda el ile tetiklenen bir işlem hattı görürsünüz.

1. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görüntülemek için işlem *hattı adı* altında **Sqlservertoblobpipeline** bağlantısını seçin. 
    ![İşlem hattı çalıştırmalarını izleme](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. **Etkinlik çalıştırmaları** sayfasında, kopyalama işlemiyle ilgili ayrıntıları görmek için Ayrıntılar (eyezlik resmi) bağlantısını seçin. İşlem hattı çalıştırmaları görünümüne geri dönmek için üstteki tüm işlem **hattı çalıştırmalarını** seçin.

## <a name="verify-the-output"></a>Çıktıyı doğrulama
İşlem hattı, `adftutorial` blob kapsayıcısında *fromonprem* adlı çıktı klasörünü otomatik olarak oluşturur. Çıktı klasöründe *[pipeline().RunId].txt* dosyasını gördüğünüzü onaylayın.


## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri bir konumdan Blob depolama alanındaki başka bir konuma kopyalar. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma.
> * SQL Server ve Depolama bağlı hizmetlerini oluşturun.
> * SQL Server ve Blob depolama veri kümeleri oluşturun.
> * Verileri taşımak için kopyalama etkinliği ile işlem hattı oluşturma.
> * Bir işlem hattı çalıştırması başlatma.
> * İşlem hattı çalıştırmasını izleme.

Data Factory tarafından desteklenen veri depolarının listesi için [Desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) konusuna bakın.

Verilerin toplu olarak kaynaktan hedefe nasıl kopyalanacağını öğrenmek için aşağıdaki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
>[Verileri toplu halde kopyalama](tutorial-bulk-copy-portal.md)