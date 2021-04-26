---
title: Azure Veri Kopyalama aracını kullanarak veri kopyalama
description: Azure Data Factory oluşturun ve ardından Veri Kopyalama aracını kullanarak verileri Azure Blob depolama alanındaki bir konumdan başka bir konuma kopyalayın.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 78bc815aff195124d3e79a83445b8d12506fc70d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104613099"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Hızlı başlangıç: Veri Kopyalama aracını kullanarak veri kopyalama

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Güncel sürüm](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu hızlı başlangıçta Azure portalını kullanarak bir veri fabrikası oluşturursunuz. Sonra, Veri Kopyalama aracını kullanarak Azure Blob depolama alanındaki bir klasörde bulunan verileri başka bir klasöre kopyalarsınız. 

> [!NOTE]
> Azure Data Factory'yi kullanmaya yeni başlıyorsanız, bu hızlı başlangıçtaki işlemleri gerçekleştirmeden önce [Azure Data Factory'ye giriş](introduction.md) konusuna bakın. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. [Azure Portal](https://portal.azure.com) gidin. 
1. Azure Portal menüsünden **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin:

    ![Yeni veri fabrikası oluşturma](./media/doc-common-process/new-azure-data-factory-menu.png)

1. **Yeni veri fabrikası** sayfasında **Ad** için **ADFTutorialDataFactory** girin. 
 
   Azure Data Factory adı *genel olarak benzersiz* olmalıdır. Aşağıdaki hatayı görürseniz veri fabrikasının adını değiştirin (örneğin, **&lt; adınız &gt; ADFTutorialDataFactory**) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - adlandırma kuralları](naming-rules.md) makalesini inceleyin.
  
   ![Bir ad kullanılamadığında alınan hata](./media/doc-common-process/name-not-available-error.png)
1. **Abonelik** için, veri fabrikasını oluşturmak istediğiniz Azure aboneliğini seçin. 
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
   - **Var olanı kullan**’ı ve ardından listeden var olan bir kaynak grubunu seçin. 
   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
   Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md).  
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** için, veri fabrikasının konumunu seçin.

   Listede yalnızca Data Factory tarafından desteklenen ve Azure Data Factory meta verilerinizin depolanacağı konumlar gösterilir. Data Factory kullanımı ile ilişkili veri depoları (Azure depolama ve Azure SQL veritabanı gibi) ve bu işlemler (Azure HDInsight gibi) başka bölgelerde çalıştırılabilir.

1. **Oluştur**’u seçin.

1. Oluşturma işlemi tamamlandıktan sonra **Data Factory** sayfasını görürsünüz. Azure Data Factory kullanıcı arabirimi (UI) uygulamasını ayrı bir sekmede başlatmak için **Yazar ve İzleyici** kutucuğunu seçin.
   
:::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::

## <a name="start-the-copy-data-tool"></a>Veri Kopyalama aracını başlatma

1. **Başlayalım** sayfasında, Veri Kopyalama aracını başlatmak için **Veri Kopyala** kutucuğunu seçin. 

   ![“Veri kopyala” kutucuğu](./media/doc-common-process/get-started-page.png)

1. Veri Kopyalama aracının **Özellikler** sayfasında işlem hattı için bir ad ve açıklama belirtip **İleri**'yi seçebilirsiniz. 

   ![“Özellikler” sayfası](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. **Kaynak veri deposu** sayfasında aşağıdaki adımları tamamlayın:

    a. Bağlantı eklemek için **+ Yeni bağlantı oluştur**'a tıklayın.

    b. Kaynak bağlantısı için oluşturmak istediğiniz bağlı hizmet türünü seçin. Bu öğreticide **Azure Blob depolamayı** kullanırız. Galeriden seçin ve ardından **devam**' ı seçin.
    
    ![Blob seçin](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. **Yeni bağlı hizmet (Azure Blob depolama)** sayfasında, bağlı hizmetiniz için bir ad belirtin. **Depolama hesabı adı** listesinden depolama hesabınızı seçin, bağlantıyı test edin ve **Oluştur**' u seçin. 

    ![Azure Blob depolama hesabını yapılandırın](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Yeni oluşturulan bağlı hizmeti kaynak olarak seçin ve ardından **İleri**' ye tıklayın.


1. **Girdi dosyasını veya klasörünü seçin** sayfasında aşağıdaki adımları uygulayın:

   a. **Adföğreticisi/giriş** klasörüne gitmek Için, **Gözden** geçirme ' ye tıklayın, **emp.txt** dosyasını seçin ve ardından **Seç**' e tıklayın. 

   d. Dosyayı olduğu gibi kopyalamak için **ikili kopya** onay kutusunu seçin ve ardından **İleri**' yi seçin. 

   ![“Girdi dosyası veya klasörü seçin” sayfası](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. **Hedef veri deposu** sayfasında, oluşturduğunuz **Azure Blob depolama** bağlı hizmeti ' ni seçin ve ardından **İleri**' yi seçin. 

1. **Çıktı dosyasını veya klasörünü seçin** sayfasında, klasör yolu için **adföğreticisi/çıkış** girin ve ardından **İleri**' yi seçin. 

   ![“Çıktı dosyasını veya klasörünü seçin” sayfası](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. **Ayarlar** sayfasında varsayılan yapılandırmaları kullanmak için **İleri**'yi seçin. 

1. **Özet** sayfasında tüm ayarları gözden geçirin ve **İleri**'yi seçin. 

1. **Dağıtım tamamlandı** sayfasında, oluşturduğunuz işlem hattını izlemek için **İzleyici**’yi seçin. 

    ![“Dağıtım tamamlandı” sayfası](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Uygulama **izleyici** sekmesine geçer. Bu sekmede işlem hattının durumunu görürsünüz. Listeyi yenilemek için **Yenile** ' yi seçin. Etkinlik çalıştırma ayrıntılarını görüntülemek veya işlem hattını yeniden çalıştırmak için işlem **hattı adı** altındaki bağlantıya tıklayın. 
   
    ![İşlem hattını Yenile](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Etkinlik çalıştırmaları sayfasında, kopyalama işlemi hakkında daha fazla bilgi için **etkınlık adı** sütununun altındaki **Ayrıntılar** bağlantısını (eyegözlük simgesi) seçin. Özelliklerle ilgili ayrıntılar için bkz. [Kopyalama Etkinliğine genel bakış](copy-activity-overview.md). 

1. İşlem hattı çalıştırmaları görünümüne geri dönmek için, içerik haritası menüsünde **tüm işlem hattı çalıştırmaları** bağlantısını seçin. Görünümü yenilemek için **Yenile**’yi seçin. 

1. **adftutorial** kapsayıcısının **çıktı** klasöründe **emp.txt** dosyasının oluşturulduğunu doğrulayın. Çıkış klasörü yoksa, Data Factory hizmeti tarafından otomatik olarak oluşturulur. 

1. Bağlantılı hizmetleri, veri kümelerini ve işlem hatlarını düzenlemek için sol panelde **İzleyici** sekmesinin üzerindeki **Yazar** sekmesine geçin. Bunları Data Factory kullanıcı arabiriminde düzenleme hakkında bilgi edinmek için bkz. [Azure portalını kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-portal.md).

    ![Yazar sekmesini seçin](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalar. Data Factory’yi daha fazla senaryoda kullanma hakkında bilgi almak için [öğreticileri](tutorial-copy-data-portal.md) okuyun. 
