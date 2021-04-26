---
title: Veri Kopyalama aracını kullanarak Azure Blob depolama 'dan SQL 'e veri kopyalama
description: Azure Data Factory oluşturun ve ardından Veri Kopyalama aracını kullanarak Azure Blob depolama alanından bir SQL veritabanına veri kopyalayın.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 20db863f1e2ae66acada928687b0bcd572f729f9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103547"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Veri Kopyalama aracını kullanarak Azure Blob depolamadan SQL veritabanına veri kopyalama

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory hizmetinin sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Güncel sürüm](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğreticide, Azure portalını kullanarak bir veri fabrikası oluşturursunuz. Ardından, verileri Azure Blob depolama alanından bir SQL veritabanına kopyalayan bir işlem hattı oluşturmak için Veri Kopyalama aracını kullanın.

> [!NOTE]
> Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:
> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**: BLOB depolama alanını _kaynak_ veri deposu olarak kullanın. Azure depolama hesabınız yoksa [depolama hesabı oluşturma](../storage/common/storage-account-create.md)bölümündeki yönergelere bakın.
* **Azure SQL veritabanı**: _Havuz_ VERI deposu olarak bir SQL veritabanı kullanın. SQL veritabanınız yoksa, [SQL veritabanı oluşturma](../azure-sql/database/single-database-create-quickstart.md)bölümündeki yönergelere bakın.

### <a name="create-a-blob-and-a-sql-table"></a>Bir blob ve SQL tablosu oluşturma

Bu adımları gerçekleştirerek BLOB depolama alanınızı ve SQL veritabanınızı öğreticiye hazırlayın.

#### <a name="create-a-source-blob"></a>Kaynak blob oluşturma

1. **Not defteri 'ni** başlatın. Aşağıdaki metni kopyalayıp diskinizde **inputEmp.txt** adlı bir dosyaya kaydedin:

   ```text
   FirstName|LastName
   John|Doe
   Jane|Doe
   ```

1. **adfv2tutorial** adlı bir kapsayıcı oluşturun ve inputEmp.txt dosyasını kapsayıcıya yükleyin. Bu görevleri gerçekleştirmek için Azure portal veya [Azure Depolama Gezgini](https://storageexplorer.com/) gibi çeşitli araçları kullanabilirsiniz.

#### <a name="create-a-sink-sql-table"></a>Havuz SQL tablosu oluşturma

1. SQL veritabanınızda **dbo.** on adlı bir tablo oluşturmak IÇIN aşağıdaki SQL betiğini kullanın:

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

2. Azure hizmetlerinin SQL Server’a erişmesine izin ver. **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin ver** ayarının SQL veritabanı çalıştıran sunucunuz için etkinleştirildiğini doğrulayın. Bu ayar, Data Factory’nin veritabanı örneğinize veri yazmasına imkan tanır. Bu ayarı doğrulamak ve etkinleştirmek için, mantıksal SQL Server > güvenlik > güvenlik duvarları ve sanal ağlar ' a gidin > **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin ver** seçeneğini **Açık** olarak ayarlayın.

   > [!NOTE]
   > **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin verme** seçeneği, yalnızca aboneliğinizdeki değil, herhangi bir Azure kaynağından SQL Server ağ erişimi sağlar. Daha fazla bilgi için bkz. [Azure SQL Server güvenlik duvarı kuralları](../azure-sql/database/firewall-configure.md). Bunun yerine, genel IP 'Leri kullanmadan Azure PaaS hizmetlerine bağlanmak için [Özel uç noktaları](../private-link/private-endpoint-overview.md) kullanabilirsiniz.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. Sol menüde, **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin:

   ![Yeni veri fabrikası oluşturma](./media/doc-common-process/new-azure-data-factory-menu.png)

1. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

   Veri fabrikanızın adı _genel olarak benzersiz_ olmalıdır. Aşağıdaki hata iletisini alabilirsiniz:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Yinelenen ad için yeni veri fabrikası hata iletisi.":::

   Ad değeriyle ilgili bir hata iletisi alırsanız, veri fabrikası için farklı bir ad girin. Örneğin, _**adınız**_**ADFTutorialDataFactory** adını kullanın. Data Factory yapıtlarını adlandırma kuralları için bkz. [Data Factory adlandırma kuralları](naming-rules.md).

1. Yeni veri fabrikasının oluşturulacağı Azure **aboneliğini** seçin.

1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

   a. **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

   b. **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.

   Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).

1. **Sürüm** bölümünde **V2**'yi seçin.

1. **Konum** bölümünde veri fabrikasının konumunu seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri fabrikanız tarafından kullanılan veri depoları (örneğin, Azure Depolama ve SQL Veritabanı) ve işlemler (örneğin, Azure HDInsight) başka konumlarda ve bölgelerde olabilir.

1. **Oluştur**’u seçin.

1. Oluşturma işlemi tamamlandıktan sonra **Data Factory** giriş sayfası görüntülenir.

   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::

1. Azure Data Factory kullanıcı arabirimini (UI) ayrı bir sekmede açmak için **Yazar ve İzleyici** kutucuğunu seçin.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Veri Kopyalama aracını kullanarak işlem hattı oluşturma

1. **Başlayalım** sayfasında, Veri Kopyalama aracını açmak için **Veri Kopyala** kutucuğunu seçin.

   ![Veri Kopyalama aracının kutucuğu](./media/doc-common-process/get-started-page.png)

1. **Özellikler** sayfasındaki **Görev adı** bölümüne **CopyFromBlobToSqlPipeline** adını girin. Sonra **İleri**’yi seçin. Data Factory kullanıcı arabirimi, belirtilen görev adına sahip bir işlem hattı oluşturur.

   ![İşlem hattı oluşturma](./media/tutorial-copy-data-tool/create-pipeline.png)

1. **Kaynak veri deposu** sayfasında aşağıdaki adımları tamamlayın:

   a. Bağlantı eklemek için **+ Yeni bağlantı oluştur** ' u seçin

   b. Galeriden **Azure Blob depolama** ' yı seçin ve ardından **devam**' ı seçin.

   c. **Yeni bağlı hizmet** sayfasında, Azure aboneliğinizi seçin ve **depolama hesabı adı** listesinden depolama hesabınızı seçin. Bağlantıyı test edin ve **Oluştur**' u seçin.

   d. Yeni oluşturulan bağlı hizmeti kaynak olarak seçin ve ardından **İleri**' yi seçin.

   ![Kaynak olarak bağlantılı hizmeti seçin](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. **Girdi dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:

   a. **Adfv2tutorial/input** klasörüne gitmek için **Gözden** geçirme ' yi seçin, **inputEmp.txt** dosyasını seçin ve ardından **Seç**' i seçin.

   b. Sonraki adıma geçmek için **İleri ' yi** seçin.

1. **Dosya biçimi ayarları** sayfasında, ilk satır için onay kutusunu *üst bilgi olarak* etkinleştirin. Aracın sütun ve satır sınırlayıcılarını otomatik olarak algıladığına dikkat edin. **İleri**’yi seçin. Ayrıca, verilerin önizlemesini yapabilir ve bu sayfadaki giriş verilerinin şemasını görüntüleyebilirsiniz.

   ![Dosya biçimi ayarları](./media/tutorial-copy-data-tool/file-format-settings-page.png)

1. **Hedef veri deposu** sayfasında aşağıdaki adımları tamamlayın:

   a. Bağlantı eklemek için **+ Yeni bağlantı oluştur** ' u seçin

   b. Galeriden **Azure SQL veritabanı** ' nı seçin ve ardından **devam**' ı seçin.

   c. **Yeni bağlı hizmet** sayfasında, açılır listeden sunucu ADıNıZı ve DB adınızı seçin, Kullanıcı adı ve parolayı belirtip **Oluştur**' u seçin.

      ![Azure SQL veritabanını yapılandırma](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

   d. Yeni oluşturulan bağlı hizmeti havuz olarak seçin ve ardından **İleri**' yi seçin.

1. **Tablo eşleme** sayfasında **[dbo].[emp]** tablosunu seçip **İleri**’yi seçin.

1. **Sütun eşleme** sayfasında, giriş dosyasındaki ikinci ve üçüncü **sütunların, urluya tablosunun** **FirstName** ve **LastName** sütunlarına eşlendiğine dikkat edin. Bir hata olmadığından emin olmak için eşlemeyi ayarlayın ve ardından **İleri**' yi seçin.

   ![Sütun eşleme sayfası](./media/tutorial-copy-data-tool/column-mapping.png)

1. **Ayarlar** sayfasında **İleri**’yi seçin.

1. **Özet** sayfasında ayarları gözden geçirin ve **İleri**’yi seçin.

1. **Dağıtım** sayfasında, işlem hattını (görev) izlemek için **İzleyici**’yi seçin.

   ![İşlem hattını izleme](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. İşlem hattı çalıştırmaları sayfasında, Listeyi yenilemek için **Yenile** ' yi seçin. Etkinlik çalıştırma ayrıntılarını görüntülemek veya işlem hattını yeniden çalıştırmak için işlem **hattı adı** altındaki bağlantıyı seçin. 

   ![İşlem hattı çalıştırması](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Etkinlik çalıştırmaları sayfasında, kopyalama işlemi hakkında daha fazla bilgi için **etkınlık adı** sütununun altındaki **Ayrıntılar** bağlantısını (eyegözlük simgesi) seçin. İşlem hattı çalıştırmaları görünümüne geri dönmek için, içerik haritası menüsünde **tüm işlem hattı çalıştırmaları** bağlantısını seçin. Görünümü yenilemek için **Yenile**’yi seçin.

   ![Etkinlik çalıştırmalarını izleme](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Verilerin SQL veritabanınızdaki **dbo.** , tablosuna eklendiğini doğrulayın.

1. Düzenleyici moduna geçmek için soldaki **Yazar** sekmesini seçin. Düzenleyici kullanılarak araç üzerinden oluşturulan bağlı hizmetleri, veri kümelerini ve işlem hatlarını güncelleştirebilirsiniz. Bu varlıkları Data Factory kullanıcı arabiriminde düzenlemeyle ilgili ayrıntılar için [bu öğreticinin Azure portalı sürümüne](tutorial-copy-data-portal.md) bakın.

   ![Yazar sekmesini seçin](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri blob depolamadan bir SQL veritabanına kopyalar. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri Kopyalama aracını kullanarak bir işlem hattı oluşturun.
> * İşlem hattı ve etkinlik çalıştırmalarını izleme.

Verileri şirket içinden buluta kopyalamayı öğrenmek için aşağıdaki öğreticiye geçin:

>[!div class="nextstepaction"]
>[Şirket içinden buluta veri kopyalama](tutorial-hybrid-copy-data-tool.md)
