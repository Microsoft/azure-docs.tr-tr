---
title: Azure Veri Kopyalama aracını kullanarak şirket içi verileri kopyalama
description: Bir Azure Data Factory oluşturun ve Veri Kopyalama aracını kullanarak verileri SQL Server veritabanından Azure Blob depolama alanına kopyalayın.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/26/2021
ms.openlocfilehash: 70905d23b61ee9fee319bae41aeb421d808fa4d0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566770"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Veri Kopyalama aracını kullanarak bir SQL Server veritabanından Azure Blob depolama alanına veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Güncel sürüm](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, Azure portalını kullanarak bir veri fabrikası oluşturursunuz. Daha sonra, verileri bir SQL Server veritabanından Azure Blob depolama alanına kopyalayan bir işlem hattı oluşturmak için Veri Kopyalama aracını kullanırsınız.

> [!NOTE]
> - Azure Data Factory’yi ilk kez kullanıyorsanız bkz. [Data Factory'ye giriş](introduction.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

## <a name="prerequisites"></a>Önkoşullar
### <a name="azure-subscription"></a>Azure aboneliği
Başlamadan önce, mevcut bir Azure aboneliğiniz yoksa [ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Azure rolleri
Data Factory örnekleri oluşturmak için, Azure 'da oturum açmak için kullandığınız kullanıcı hesabına *katkıda* bulunan veya *sahip* rolü atanmalıdır veya Azure aboneliğinin *Yöneticisi* olması gerekir.

Abonelikte sahip olduğunuz izinleri görüntülemek için Azure portalına gidin. Sağ üst köşeden kullanıcı adınızı ve sonra **İzinler**’i seçin. Birden çok aboneliğe erişiminiz varsa uygun aboneliği seçin. Bir role kullanıcı eklemeye ilişkin örnek yönergeler için, bkz. [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 ve 2017
Bu öğreticide, bir SQL Server veritabanını *kaynak* veri deposu olarak kullanırsınız. Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri bu SQL Server veritabanından (kaynak) BLOB depolama alanına (havuz) kopyalar. Daha sonra SQL Server veritabanınızda **"adlı bir** tablo oluşturur ve tabloya birkaç örnek girdi eklersiniz.

1. SQL Server Management Studio’yu başlatın. Makinenizde zaten yüklü değilse [SQL Server Management Studio'yu indirme](/sql/ssms/download-sql-server-management-studio-ssms) sayfasına gidin.

1. Kimlik bilgilerinizi kullanarak SQL Server örneğinize bağlanın.

1. Örnek bir veritabanı oluşturun. Ağaç görünümünde **Veritabanları**'na sağ tıklayın ve **Yeni Veritabanı**'nı seçin.

1. **Yeni Veritabanı** penceresinde, veritabanı için bir ad girin ve **Tamam**'ı seçin.

1. Çalışan **tablosunu oluşturmak** ve içine bazı örnek verileri eklemek için veritabanında aşağıdaki sorgu betiğini çalıştırın. Ağaç görünümünde, oluşturduğunuz veritabanına sağ tıklayın ve **Yeni Sorgu**'yu seçin.

    ```sql
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
Bu öğreticide, genel amaçlı Azure depolama hesabını (özel olarak Blob depolama) hedef/havuz veri deposu olarak kullanırsınız. Genel amaçlı bir depolama hesabınız yoksa yeni hesap oluşturma yönergeleri için bkz. [Depolama hesabı oluşturma](../storage/common/storage-account-create.md). Bu öğreticide oluşturduğunuz veri fabrikasındaki işlem hattı, verileri SQL Server veritabanından (kaynak) bu blob depolama alanına (havuz) kopyalar. 

#### <a name="get-the-storage-account-name-and-account-key"></a>Depolama hesabı adını ve hesap anahtarını alma
Bu öğreticide, depolama hesabınızın adını ve anahtarını kullanırsınız. Depolama hesabınızın adını ve anahtarını almak için aşağıdaki adımları gerçekleştirin:

1. Azure kullanıcı adı ve parolanızla [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmede **Tüm hizmetler**'i seçin. **Depolama** anahtar sözcüğünü kullanarak filtreleyin ve **Depolama hesapları**’nı seçin.

    ![Depolama hesabı araması](media/doc-common-process/search-storage-account.png)

1. Depolama hesapları listesinde, depolama hesabınız için filtre uygulayın (gerekirse). Sonra depolama hesabınızı seçin.

1. **Depolama hesabı** penceresinde **Erişim anahtarları**'nı seçin.


1. **Depolama hesabı adı** ve **key1** kutularında değerleri kopyalayın ve ardından onları öğreticide daha sonra kullanmak için Not Defteri'ne veya başka bir düzenleyiciye yapıştırın.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Soldaki menüde, **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin.

   ![Yeni veri fabrikası oluşturma](./media/doc-common-process/new-azure-data-factory-menu.png)

1. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

   Veri fabrikasının adı *genel olarak benzersiz* olmalıdır. Ad alanı için aşağıdaki hata iletisini görürseniz veri fabrikasının adını değiştirin (örneğin, adınızADFTutorialDataFactory). Data Factory yapıtlarının adlandırma kuralları için bkz.[Data Factory adlandırma kuralları](naming-rules.md).

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Yinelenen ad için yeni veri fabrikası hata iletisi.":::
1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
        
     Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** bölümünde veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri Fabrikası tarafından kullanılan veri depoları (örneğin, Azure Depolama ve SQL Veritabanı) ve işlemler (örneğin, Azure HDInsight) başka konumlarda/bölgelerde olabilir.
1. **Oluştur**’u seçin.

1. Oluşturma işlemi bittikten sonra, resimde gösterildiği gibi **Veri Fabrikası** sayfası görüntülenir.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::
1. Azure Data Factory kullanıcı arabirimini ayrı bir sekmede açmak için **Geliştir ve İzle**’yi seçin.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Veri Kopyalama aracını kullanarak işlem hattı oluşturma

1. **Başlayalım** sayfasında, Veri Kopyalama aracını açmak için **Veri Kopyala**’yı seçin.

   ![Başlarken sayfası](./media/doc-common-process/get-started-page.png)

1. Veri Kopyalama aracının **Özellikler** sayfasında, **Görev adı** bölümüne **CopyFromOnPremSqlToAzureBlobPipeline** adını girin. Sonra **İleri**’yi seçin. Veri Kopyalama aracı, bu alan için belirttiğiniz ada sahip bir işlem hattı oluşturur.
  ![Görev adı](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. **Kaynak veri deposu** sayfasında **Yeni bağlantı oluştur**'a tıklayın.

1. **Yeni bağlı hizmet** altında **SQL Server** arayın ve ardından **devam**' ı seçin.

1. **Yeni bağlı hizmet (SQL Server)** iletişim kutusunda, **ad**' ın altında **sqlserverlinkedservice** adını girin. **Tümleştirme çalışma zamanı aracılığıyla Bağlan** altında **+ Yeni** ' yi seçin. Şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturup bunu makinenize indirmeniz ve Data Factory’ye kaydetmeniz gerekir. Şirket içinde barındırılan tümleştirme çalışma zamanı, şirket içi ortamınızla bulut arasında veri kopyalar.

1. **Integration Runtime kurulum** iletişim kutusunda, **Şirket içinde barındırılan**' ı seçin. Daha sonra **Devam** seçeneğini belirleyin.

   ![Tümleştirme çalışma zamanı oluşturma](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. **Integration Runtime kurulum** iletişim kutusunda, **ad**' ın altında, **TutorialIntegrationRuntime** girin. Ardından **Oluştur**’u seçin.

1. **Integration Runtime kurulum** iletişim kutusunda, **Bu bilgisayar için hızlı kurulumu başlatmak Için buraya tıklayın ' ı** seçin. Bu işlem, tümleştirme çalışma zamanını makinenize yükler ve Data Factory’ye kaydeder. Alternatif olarak, el ile kurulum seçeneğini kullanarak yükleme dosyasını indirip çalıştırabilir ve anahtarı kullanarak tümleştirme çalışma zamanını kaydedebilirsiniz.

1. İndirilen uygulamayı çalıştırın. Pencerede hızlı kurulum durumunu görürsünüz.

    ![Hızlı kurulum durumu](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. **Yeni bağlı hizmet (SQL Server)** iletişim kutusunda, Integration Runtime alanı için **TutorialIntegrationRuntime** seçili olduğunu onaylayın. Ardından, aşağıdaki adımları uygulayın:

    a. **Ad** bölümüne **SqlServerLinkedService** adını girin.

    b. **Sunucu adı** bölümüne SQL Server örneğinizin adını girin.

    c. **Veritabanı adı** bölümüne şirket içi veritabanınızın adını girin.

    d. **Kimlik doğrulaması türü** bölümünde uygun kimlik doğrulamasını seçin.

    e. **Kullanıcı adı** altında, SQL Server erişimi olan kullanıcının adını girin.

    f. Kullanıcının **parolasını** girin.

    örneğin: Bağlantıyı test edin ve **son**' u seçin.

      ![Tümleştirme çalışma zamanı seçildi](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. **Kaynak veri deposu** sayfasında **İleri**' yi seçin.

1. **Verilerin kopyalanacağı tabloları seçin veya özel sorgu kullanın** sayfasında, listeden **[dbo].[emp]** tablosunu seçip **İleri**’yi seçin. Veritabanınıza göre diğer tablolardan da seçim yapabilirsiniz.

1. **Hedef veri deposu** sayfasında **Yeni bağlantı oluştur**'u seçin


1. **Yeni bağlı hizmet**' de **Azure Blob**' u arayıp seçin ve ardından **devam**' ı seçin.

   ![Blob depolama seçimi](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. **Yeni Bağlı Hizmet (Azure Blob Depolama)** iletişim kutusunda aşağıdaki adımları uygulayın:

   a. **Ad** bölümüne **AzureStorageLinkedService** adını girin.

   b. **Tümleştirme çalışma zamanıyla bağlan** bölümünde **TutorialIntegrationRuntime** öğesini seçin

   c. **Depolama hesabı adı** bölümünde, açılan listeden depolama hesabınızı seçin.

   d. **Son**'u seçin.

1. **Hedef veri deposu** Iletişim kutusunda **Azure Blob Storage** ' ın seçili olduğundan emin olun. Sonra **İleri**’yi seçin.

1. **Çıkış dosyasını veya klasörünü seçin** iletişim kutusunda, **Klasör yolu** bölümüne **adftutorial/fromonprem** yolunu girin. Ön koşulların bir parçası olarak **adftutorial** kapsayıcısını oluşturdunuz. Çıkış klasörü yoksa (bu örnekte **fromonprem**), Data Factory tarafından otomatik olarak oluşturulur. Blob depolamaya ve onun kapsayıcılarına/klasörlerine gitmek için de **Araştır** düğmesini kullanabilirsiniz. **Dosya adı** bölümünde değer belirtmezseniz varsayılan olarak kaynaktaki ad (bu örnekte **dbo.emp**) kullanılır.

   ![Çıktı dosyasını veya klasörünü seçin](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. **Dosya biçimi ayarları** iletişim kutusunda **İleri**’yi seçin.

1. **Ayarlar** iletişim kutusunda **İleri**’yi seçin.

1. **Özet** iletişim kutusunda tüm ayarların değerlerini gözden geçirin ve **İleri**’yi seçin.

1. **Dağıtım** sayfasında, işlem hattını (görev) izlemek için **İzleyici**’yi seçin. 

1. İşlem hattı çalıştırması tamamlandığında, oluşturduğunuz işlem hattının durumunu görüntüleyebilirsiniz. 

1. İşlem hattı çalıştırmaları sayfasında, Listeyi yenilemek için **Yenile** ' yi seçin. Etkinlik çalıştırma ayrıntılarını görüntülemek veya işlem hattını yeniden çalıştırmak için işlem **hattı adı** altındaki bağlantıya tıklayın. 

1. Etkinlik çalıştırmaları sayfasında, kopyalama işlemi hakkında daha fazla bilgi için **etkınlık adı** sütununun altındaki **Ayrıntılar** bağlantısını (eyegözlük simgesi) seçin. İşlem hattı çalıştırmaları görünümüne geri dönmek için, içerik haritası menüsünde **tüm işlem hattı çalıştırmaları** bağlantısını seçin. Görünümü yenilemek için **Yenile**’yi seçin.

1. Çıktı dosyasını **adftutorial** kapsayıcısının **fromonprem** klasöründe gördüğünüzü onaylayın.

1. Düzenleyici moduna geçmek için soldaki **Düzenle** sekmesini seçin. Düzenleyiciyi kullanarak araç tarafından oluşturulan bağlı hizmetleri, veri kümelerini ve işlem hatlarını güncelleştirebilirsiniz. Düzenleyicide açılan varlıkla ilişkili JSON kodunu görüntülemek için **Kod**’u seçin. Bu varlıklarım Data Factory kullanıcı arabiriminde nasıl düzenleneceği ile ilgili ayrıntılar için [bu öğreticinin Azure portalı sürümüne](tutorial-copy-data-portal.md) bakın.


## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri bir SQL Server veritabanından BLOB depolama alanına kopyalar. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Data Factory tarafından desteklenen veri depolarının listesi için [Desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) konusuna bakın.

Verilerin toplu olarak kaynaktan hedefe nasıl kopyalanacağı hakkında bilgi edinmek için aşağıdaki öğreticiye geçin:

> [!div class="nextstepaction"]
>[Verileri toplu halde kopyalama](tutorial-bulk-copy-portal.md)
