---
title: Azure Data Factory işlem hattı oluşturmak için özel uç noktaları kullanma
description: Bu öğreticide işlem hattıyla veri fabrikası oluşturmak için Azure portalını kullanmaya yönelik adım adım yönergeler sağlanır. İşlem hattı, Azure Blob depolama alanından Azure SQL veritabanına veri kopyalamak için kopyalama etkinliğini kullanır.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.author: jingwang
ms.openlocfilehash: 191ad61990e10fdb718eebf1a8f57d8edaadcf35
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515466"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Özel uç noktaları kullanarak verileri Azure Blob depolamadan bir SQL veritabanına güvenli bir şekilde kopyalama

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, Azure Data Factory kullanıcı arabirimini (UI) kullanarak bir veri fabrikası oluşturursunuz. *Bu veri fabrikasındaki işlem hattı, verileri Azure Blob depolama 'dan Azure SQL veritabanına güvenli bir şekilde kopyalar (her ikisi de yalnızca seçili ağlara erişime izin verir) [Azure Data Factory yönetilen sanal ağdaki](managed-virtual-network-private-endpoint.md)özel uç noktaları kullanarak.* Bu öğreticideki yapılandırma düzeni, dosya tabanlı bir veri deposundan ilişkisel bir veri deposuna kopyalama için geçerlidir. Kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimler](./copy-activity-overview.md) tablosu.

> [!NOTE]
> İlk kez Data Factory kullanıyorsanız bkz. [Azure Data Factory'ye giriş](./introduction.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

* Veri fabrikası oluşturma.
* Kopyalama etkinliğiyle işlem hattı oluşturma.


## <a name="prerequisites"></a>Önkoşullar
* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**. Blob depolama alanını *kaynak* veri deposu olarak kullanabilirsiniz. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal). *Depolama hesabının yalnızca seçili ağlardan erişime izin verdiğinden emin olun.* 
* **Azure SQL veritabanı**. Veritabanını *havuz* veri deposu olarak kullanabilirsiniz. Azure SQL veritabanınız yoksa, oluşturma adımları için bkz. [SQL veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md) . *SQL veritabanı hesabının yalnızca seçili ağlardan erişime izin verdiğinden emin olun.* 

### <a name="create-a-blob-and-a-sql-table"></a>Bir blob ve SQL tablosu oluşturma

Şimdi aşağıdaki adımları gerçekleştirerek BLOB depolama alanınızı ve SQL veritabanınızı öğreticiye hazırlayın.

#### <a name="create-a-source-blob"></a>Kaynak blob oluşturma

1. Not defteri 'Ni açın. Aşağıdaki metni kopyalayın ve diskinizde **emp.txt** dosyası olarak kaydedin:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Blob depolamada **adföğreticisi** adlı bir kapsayıcı oluşturun. Bu kapsayıcıda **input** adlı bir klasör oluşturun. Sonra, **emp.txt** dosyasını **input** klasörüne yükleyin. Bu görevleri yerine getirmek için Azure Portal'ı veya [Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

#### <a name="create-a-sink-sql-table"></a>Havuz SQL tablosu oluşturma

SQL veritabanınızda **dbo.emp** tablosu oluşturmak için aşağıdaki SQL betiğini kullanın:

```sql
CREATE TABLE dbo.emp
(
    ID int IDENTITY(1,1) NOT NULL,
    FirstName varchar(50),
    LastName varchar(50)
)
GO

CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Bu adımda, bir veri fabrikası oluşturacak ve veri fabrikasında bir işlem hattı oluşturmak için Data Factory kullanıcı arabirimini başlatacaksınız.

1. Microsoft Edge veya Google Chrome 'ı açın. Şu anda yalnızca Microsoft Edge ve Google Chrome Web tarayıcıları Data Factory Kullanıcı arabirimini destekler.

1. Sol menüde, **kaynak**  >  **Analizi**  >  **Data Factory** oluştur ' u seçin.

1. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

   Azure veri fabrikasının adı *genel olarak benzersiz* olmalıdır. Ad değeri hakkında bir hata iletisi alırsanız, Data Factory için farklı bir ad girin (örneğin, Adınızadftutorialdatafactory). Data Factory yapıtlarının adlandırma kuralları için bkz.[Data Factory adlandırma kuralları](./naming-rules.md).

1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.

1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

    - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.
    - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
     
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md). 

1. **Sürüm** bölümünde **V2**'yi seçin.

1. **Konum** bölümünden veri fabrikası için bir konum seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan veri depoları (örneğin, Azure Depolama ve SQL Veritabanı) ve işlemler (örneğin, Azure HDInsight) başka bölgelerde olabilir.

1. **Oluştur**’u seçin.

1. Oluşturma işlemi tamamlandıktan sonra bildirim merkezinde bildirimi görürsünüz. **Data Factory** sayfasına gitmek Için **Kaynağa Git** ' i seçin.

1. Data Factory Kullanıcı Arabirimini (UI) ayrı bir sekmede başlatmak için **Geliştir ve İzle**’yi seçin.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Data Factory yönetilen sanal ağda Azure tümleştirme çalışma zamanı oluşturma
Bu adımda, bir Azure tümleştirme çalışma zamanı oluşturur ve yönetilen sanal ağı Data Factory etkinleştirirsiniz.

1. Data Factory portalında **Yönet** ' e gidin ve yeni bir Azure tümleştirme çalışma zamanı oluşturmak için **Yeni** ' yi seçin.

   ![Yeni bir Azure tümleştirme çalışma zamanı oluşturmayı gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. **Tümleştirme çalışma zamanı kurulumu** sayfasında, gereken yeteneklere göre oluşturulacak tümleştirme çalışma zamanını seçin. Bu öğreticide, **Azure ve kendiliğinden konak** ' ı seçip **devam**' a tıklayın. 
1. Azure **' ı seçin ve** ardından **devam** ' a tıklayarak bir Azure tümleştirme çalışma zamanı oluşturun.

   ![Yeni bir Azure tümleştirme çalışma zamanı gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. **Sanal ağ yapılandırması (Önizleme)** altında **Etkinleştir**' i seçin.

   ![Yeni bir Azure tümleştirme çalışma zamanının etkinleştirilmesini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. **Oluştur**’u seçin.

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu adımda, veri fabrikasında kopyalama etkinliği ile bir işlem hattı oluşturacaksınız. Kopyalama etkinliği, verileri Blob depolama alanından SQL Veritabanı'na kopyalar. [Hızlı başlangıç eğitiminde](./quickstart-create-data-factory-portal.md), şu adımları izleyerek bir işlem hattı oluşturdunuz:

1. Bağlı hizmeti oluşturma.
1. Giriş ve çıkış veri kümelerini oluşturma.
1. İşlem hattı oluşturma.

Bu öğreticide, bir işlem hattı oluşturarak başlayın. Daha sonra işlem hattını yapılandırmanız gerektiğinde bağlı hizmetleri ve veri kümelerini oluşturacaksınız.

1. **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin.

   ![İşlem hattı oluşturmayı gösteren ekran görüntüsü.](./media/doc-common-process/get-started-page.png)
1. İşlem hattının Özellikler bölmesinde, işlem hattı adı için **Copypipeline** girin.

1. **Etkinlikler** araç kutusunda **taşıma ve dönüştürme** kategorisini genişletin ve **veri kopyalama** etkinliğini araç kutusundan işlem hattı Tasarımcısı yüzeyine sürükleyin. Ad için **Copyfromblobtosql** girin.

    ![Kopyalama etkinliğini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Kaynak yapılandırma

>[!TIP]
>Bu öğreticide, **hesap anahtarı** ' nı kaynak veri deponuzu kimlik doğrulama türü olarak kullanacaksınız. Ayrıca, **SAS URI 'si**,**hizmet sorumlusu** ve gerekirse **yönetilen kimlik** gibi diğer desteklenen kimlik doğrulama yöntemlerini de seçebilirsiniz. Daha fazla bilgi için [Azure Data Factory kullanarak Azure Blob depolamada verileri kopyalama ve dönüştürme](./connector-azure-blob-storage.md#linked-service-properties)bölümündeki ilgili bölümlere bakın.
>
>Veri mağazalarının gizli dizilerini güvenli bir şekilde depolamak için, Azure Key Vault kullanmanızı da öneririz. Daha fazla bilgi ve çizimler için bkz. [Azure Key Vault kimlik bilgilerini depolama](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Kaynak veri kümesi ve bağlı hizmet oluşturma

1. **Kaynak** sekmesine gidin. Kaynak veri kümesi oluşturmak için **+ Yeni** seçeneğini belirleyin.

1. **Yeni veri kümesi** Iletişim kutusunda **Azure Blob depolama**' yı seçin ve ardından **devam**' ı seçin. Kaynak veriler bir Blob depolama alanında olduğundan kaynak veri kümesi olarak **Azure Blob Depolama Alanı**'nı seçmeniz gerekir.

1. **Biçim Seç** iletişim kutusunda verilerinizin biçim türünü seçin ve ardından **devam**' ı seçin.

1. **Özellikleri ayarla** Iletişim kutusunda **ad** için **sourceblobdataset** girin. **Üst bilgi olarak ilk satırın** onay kutusunu seçin. **Bağlı hizmet** metin kutusu altında **+ Yeni**' yi seçin.

1. **Yeni bağlı hizmet (Azure Blob depolama)** iletişim kutusunda, **ad** olarak **AzureStorageLinkedService** girin ve depolama hesabı **adı** listesinden depolama hesabınızı seçin. 

1. **Etkileşimli yazma** özelliğini etkinleştirdiğinizden emin olun. Bu işlem, bir dakika içinde etkinleştirilebilir.

    ![Etkileşimli yazma gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. **Bağlantıyı sına**’yı seçin. Depolama hesabı yalnızca **Seçili ağlardan** erişime izin veriyorsa ve bu, kullanılmadan önce onaylanması gereken Data Factory özel bir uç nokta oluşturmasını gerektirdiğinde başarısız olur. Hata iletisinde, yönetilen özel uç nokta oluşturmak için izleyebileceğiniz özel bir uç nokta oluşturmak için bir bağlantı görmeniz gerekir. Diğer bir seçenek de doğrudan **Yönet** sekmesine gidip bir [sonraki bölümdeki](#create-a-managed-private-endpoint) yönergeleri izleyerek yönetilen özel uç nokta oluşturur.

   > [!NOTE]
   > **Yönet** sekmesi tüm Data Factory örnekleri için kullanılamayabilir. Bunu görmüyorsanız özel uç noktalara, **Yazar**  >  **bağlantıları**  >  **Özel uç noktası**' nı seçerek erişebilirsiniz.
1. İletişim kutusunu açık tutun ve depolama hesabınıza gidin.

1. Özel bağlantıyı onaylamak için [Bu bölümdeki](#approval-of-a-private-link-in-a-storage-account) yönergeleri izleyin.

1. İletişim kutusuna geri dönün. **Bağlantıyı yeniden sına** ' yı seçin ve bağlı hizmeti dağıtmak için **Oluştur** ' u seçin.

1. Bağlı hizmet oluşturulduktan sonra, **özelliği ayarla** sayfasına geri döner. **Dosya yolu**’nun yanındaki **Gözat** seçeneğini belirleyin.

1. **Adföğreticisi/giriş** klasörüne gidin, **emp.txt** dosyasını seçin ve ardından **Tamam**' ı seçin.

1. **Tamam**’ı seçin. Otomatik olarak işlem hattı sayfasına gider. **Kaynak** sekmesinde **sourceblobdataset** ' in seçili olduğunu onaylayın. Bu sayfadaki verilerin önizlemesini görüntülemek için **Veri önizleme**‘yi seçin.

    ![Kaynak veri kümesini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Yönetilen özel uç nokta oluşturma

Bağlantıyı test ettiğinizde köprüyü seçmediyseniz yolu izleyin. Şimdi oluşturduğunuz bağlı hizmete bağlanacak bir yönetilen özel uç noktası oluşturmanız gerekir.

1. **Yönet** sekmesine gidin.

   > [!NOTE]
   > **Yönet** sekmesi tüm Data Factory örnekleri için kullanılamayabilir. Bunu görmüyorsanız özel uç noktalara, **Yazar**  >  **bağlantıları**  >  **Özel uç noktası**' nı seçerek erişebilirsiniz.

1. **Yönetilen özel uç noktalar** bölümüne gidin.

1. **Yönetilen özel uç noktalar** altında **+ Yeni** ' yi seçin.

    ![Yönetilen özel uç noktalar Yeni düğmesini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Listeden **Azure Blob depolama** kutucuğunu seçin ve **devam**' ı seçin.

1. Oluşturduğunuz depolama hesabının adını girin.

1. **Oluştur**’u seçin.

1. Birkaç saniye sonra, oluşturulan özel bağlantının bir onay ihtiyacı olduğunu görmeniz gerekir.

1. Oluşturduğunuz özel uç noktayı seçin. Özel uç noktayı, depolama hesabı düzeyinde onaylamaya yol açacak bir köprü görebilirsiniz.

    ![Yönetilen özel uç nokta bölmesini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Bir depolama hesabındaki özel bağlantının onaylanması
1. Depolama hesabında, **Ayarlar** bölümü altında **Özel uç nokta bağlantıları** ' na gidin.

1. Oluşturduğunuz özel uç noktanın onay kutusunu seçin ve **Onayla**' yı seçin.

    ![Özel uç nokta için Onayla düğmesini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Bir açıklama ekleyin ve **Evet**' i seçin.
1. Data Factory ' deki **Yönet** sekmesinin **yönetilen özel uç noktalar** bölümüne geri dönün.
1. Bir veya iki dakika sonra, Özel uç noktanızın onayını Data Factory Kullanıcı arabiriminde göründüğünü görmeniz gerekir.


### <a name="configure-a-sink"></a>Havuz yapılandırma
>[!TIP]
>Bu öğreticide, havuz veri deponuzu kimlik doğrulama türü olarak **SQL kimlik doğrulamasını** kullanacaksınız. Ayrıca, **hizmet sorumlusu** ve gerekirse **yönetilen kimlik** gibi diğer desteklenen kimlik doğrulama yöntemlerini de seçebilirsiniz. Daha fazla bilgi için [Azure Data Factory kullanarak Azure SQL veritabanındaki verileri kopyalama ve dönüştürme](./connector-azure-sql-database.md#linked-service-properties)bölümündeki ilgili bölümlere bakın.
>
>Veri mağazalarının gizli dizilerini güvenli bir şekilde depolamak için, Azure Key Vault kullanmanızı da öneririz. Daha fazla bilgi ve çizimler için bkz. [Azure Key Vault kimlik bilgilerini depolama](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Havuz veri kümesi ve bağlı hizmet oluşturma
1. **Havuz** sekmesine gidin ve havuz veri kümesi oluşturmak için **+Yeni** seçeneğini belirleyin.

1. **Yeni veri kümesi** iletişim kutusunda, bağlayıcılar filtrelemek için arama kutusuna **SQL** yazın. **Azure SQL veritabanı**' nı seçin ve ardından **devam**' ı seçin. Bu öğreticide verileri bir SQL veritabanına kopyalayacaksınız.

1. **Özellikleri ayarla** Iletişim kutusunda **ad** için **outputsqldataset** girin. **Bağlı hizmet** açılan listesinden **+ Yeni**' yi seçin. Bağlı hizmeti bir veri kümesi ile ilişkilendirilmelidir. Bağlı hizmet, Data Factory’nin çalışma zamanında SQL veritabanına bağlanmak için kullandığı bağlantı dizesini içerir. Veri kümesi, verilerin kopyalanacağı kapsayıcıyı, klasörü ve dosyayı (isteğe bağlı) belirtilir.

1. **Yeni bağlı hizmet (Azure SQL veritabanı)** iletişim kutusunda aşağıdaki adımları uygulayın:

    1. **Ad** bölümüne **AzureSqlDatabaseLinkedService** girin.
    1. **Sunucu adı** bölümünde SQL Server örneğinizi seçin.
    1. **Etkileşimli yazma** özelliğini etkinleştirdiğinizden emin olun.
    1. **Veritabanı adı** bölümünde SQL veritabanınızı seçin.
    1. **Kullanıcı adı** bölümüne kullanıcının adını girin.
    1. **Parola** bölümüne kullanıcının parolasını girin.
    1. **Bağlantıyı sına**’yı seçin. SQL Server yalnızca **Seçili ağlardan** erişime izin verdiğinden ve kendisine özel bir uç nokta oluşturmasını Data Factory gerektirdiğinden (Bu, kullanılmadan önce onaylanması gerekir) başarısız olmalıdır. Hata iletisinde, yönetilen özel uç nokta oluşturmak için izleyebileceğiniz özel bir uç nokta oluşturmak için bir bağlantı görmeniz gerekir. Diğer bir seçenek de doğrudan **Yönet** sekmesine gidip bir sonraki bölümdeki yönergeleri izleyerek yönetilen özel uç nokta oluşturur.
    1. İletişim kutusunu açık tutun ve seçtiğiniz SQL Server 'a gidin.
    1. Özel bağlantıyı onaylamak için [Bu bölümdeki](#approval-of-a-private-link-in-sql-server) yönergeleri izleyin.
    1. İletişim kutusuna geri dönün. **Bağlantıyı yeniden sına** ' yı seçin ve bağlı hizmeti dağıtmak için **Oluştur** ' u seçin.

1. Otomatik olarak **özelliği ayarla** iletişim kutusuna gider. **Tablo**’da **[dbo].[emp]** seçeneğini belirleyin. Ardından **Tamam**’ı seçin.

1. İşlem hattının sekmesine gidin ve **Havuz veri kümesi**' nde **outputsqldataset** ' in seçili olduğunu doğrulayın.

    ![İşlem hattı sekmesini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

İsteğe bağlı olarak, [kopyalama etkinliğinde şema eşlemesini](./copy-activity-schema-and-type-mapping.md)izleyerek kaynak şemasını hedefin karşılık gelen şemasına eşleyebilirsiniz.

#### <a name="create-a-managed-private-endpoint"></a>Yönetilen özel uç nokta oluşturma

Bağlantıyı test ettiğinizde köprüyü seçmediyseniz yolu izleyin. Şimdi oluşturduğunuz bağlı hizmete bağlanacak bir yönetilen özel uç noktası oluşturmanız gerekir.

1. **Yönet** sekmesine gidin.
1. **Yönetilen özel uç noktalar** bölümüne gidin.
1. **Yönetilen özel uç noktalar** altında **+ Yeni** ' yi seçin.

    ![Yönetilen özel uç noktalar Yeni düğmesini gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Listeden **Azure SQL veritabanı** kutucuğunu seçin ve **devam**' ı seçin.
1. Seçtiğiniz SQL Server 'ın adını girin.
1. **Oluştur**’u seçin.
1. Birkaç saniye sonra, oluşturulan özel bağlantının bir onay ihtiyacı olduğunu görmeniz gerekir.
1. Oluşturduğunuz özel uç noktayı seçin. SQL Server düzeyinde özel uç noktayı onaylamaya yönelik bir köprü görebilirsiniz.


#### <a name="approval-of-a-private-link-in-sql-server"></a>SQL Server bir özel bağlantının onaylanması
1. SQL Server 'da, **Ayarlar** bölümü altında **Özel uç nokta bağlantıları** ' na gidin.
1. Oluşturduğunuz özel uç noktanın onay kutusunu seçin ve **Onayla**' yı seçin.
1. Bir açıklama ekleyin ve **Evet**' i seçin.
1. Data Factory ' deki **Yönet** sekmesinin **yönetilen özel uç noktalar** bölümüne geri dönün.
1. Onayın özel uç noktanıza gösterilmesi için bir veya iki dakika sürer.

#### <a name="debug-and-publish-the-pipeline"></a>İşlem hattında hata ayıklama ve işlem hattını yayımlama

Yapıtları (bağlı hizmetler, veri kümeleri ve işlem hattı) Data Factory'de veya kendi Azure Repos Git deponuzda yayımlamadan önce işlem hattında hata ayıklayabilirsiniz.

1. İşlem hattında hata ayıklamak için araç çubuğunda **Hata Ayıkla**'yı seçin. Pencerenin altındaki **Çıkış** sekmesinde işlem hattı çalıştırmasının durumu görüntülenir.
1. İşlem hattı başarıyla çalıştıktan sonra, üstteki araç çubuğunda **Tümünü Yayımla**' yı seçin. Bu eylem, Data Factory için oluşturduğunuz varlıkları (veri kümeleri ve işlem hatları) yayımlar.
1. **Başarıyla yayımlandı** iletisini görene kadar bekleyin. Bildirim iletilerini görmek için sağ üst köşedeki **bildirimleri göster** ' i (zil düğmesi) seçin.


#### <a name="summary"></a>Özet
Bu örnekteki işlem hattı, Data Factory yönetilen sanal ağdaki özel uç noktaları kullanarak blob depolamadan SQL veritabanına veri kopyalar. Şunları öğrendiniz:

* Veri fabrikası oluşturma.
* Kopyalama etkinliğiyle işlem hattı oluşturma.