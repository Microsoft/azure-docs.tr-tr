---
title: Azure Data Factory Kullanıcı arabirimini kullanarak Azure Veri Fabrikası oluşturma
description: Azure Blob’daki bir konumdan başka bir konuma veri kopyalayan işlem hattı içeren bir veri fabrikası oluşturun.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 642039e99df19939def5ac850f1abccca0e67222
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684489"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Hızlı başlangıç: Azure Data Factory Kullanıcı arabirimini kullanarak veri fabrikası oluşturma

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Geçerli sürüm](quickstart-create-data-factory-portal.md)

Bu hızlı başlangıçta, Azure Data Factory UI kullanarak veri fabrikasını oluşturma ve izleme işlemi açıklanır. Bu veri fabrikasında oluşturduğunuz işlem hattı, verileri Azure Blob depolama alanındaki bir klasörden başka bir klasöre *kopyalar*. Azure Data Factory kullanarak verileri *dönüştürme* hakkında bir öğretici için bkz. [Öğretici: Spark kullanarak verileri dönüştürme](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Azure Data Factory kullanmaya yeni başlıyorsanız, bu hızlı başlangıçtaki işlemleri gerçekleştirmeden önce [Azure Data Factory'ye giriş](data-factory-introduction.md) konusuna bakın. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Bu videoyu izlemeniz, Data Factory kullanıcı arabirimini anlamanıza yardımcı olur: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. [Azure Portal](https://portal.azure.com) gidin. 
1. Azure portal menüsünde **kaynak oluştur**' u seçin.
   
   ![Azure portal menüsünden kaynak oluştur ' u seçin](./media/doc-common-process/create-a-resource.png)
1. **Analiz**' i ve ardından **Data Factory**' yi seçin. 
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)
1. **Yeni veri fabrikası** sayfasında **Ad** için **ADFTutorialDataFactory** girin. 
 
   Azure data factory adı *küresel olarak benzersiz* olmalıdır. Aşağıdaki hatayı alırsanız veri fabrikasının adını değiştirin (örneğin **&lt; adınız&gt;ADFTutorialDataFactory**) ve veri fabrikası oluşturmayı yeniden deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - adlandırma kuralları](naming-rules.md) makalesini inceleyin.
  
   ![Bir ad kullanılamadığında alınan hata](./media/doc-common-process/name-not-available-error.png)
1. **Abonelik** için, veri fabrikasını oluşturmak istediğiniz Azure aboneliğini seçin. 
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
   - **Var olanı kullan**’ı ve ardından listeden var olan bir kaynak grubunu seçin. 
   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
   Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** için, veri fabrikasının konumunu seçin.

   Listede yalnızca Data Factory tarafından desteklenen ve Azure Data Factory meta verilerinizin depolanacağı konumlar gösterilir. Data Factory kullanımı ile ilişkili veri depoları (Azure depolama ve Azure SQL veritabanı gibi) ve bu işlemler (Azure HDInsight gibi) başka bölgelerde çalıştırılabilir.

1. **Oluştur**'u seçin.

1. Oluşturma işlemi tamamlandıktan sonra, **Data Factory** sayfasını görürsünüz. Azure Data Factory kullanıcı arabirimi (UI) uygulamasını ayrı bir sekmede başlatmak için **Yazar ve İzleyici** kutucuğunu seçin.
   
   ![Veri fabrikasının “Yazar ve İzleyici” kutucuğuna sahip ana sayfası](./media/doc-common-process/data-factory-home-page.png)
1. **Başlayalım** sayfasında, sol bölmede bulunan **Yazar** sekmesine geçin. 

    ![“Başlayalım” sayfası](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Bağlı hizmet oluşturma
Bu yordamda, Azure depolama hesabınızı veri fabrikasına bağlamak için bağlı bir hizmet oluşturursunuz. Bağlı hizmetler, Data Factory hizmetinin bunlara bağlanmak için çalışma zamanında kullandığı bağlantı bilgilerini içerir.

1. **Bağlantılar**' ı seçin ve ardından araç çubuğunda **Yeni** düğmesini seçin (**Bağlantılar** düğmesi, **fabrika kaynakları**altındaki sol sütunun altında bulunur). 

1. **Yeni Bağlı Hizmet** sayfasında **Azure Blob Depolama**’yı seçip **Devam**’ı seçin. 

   !["Azure Blob Depolama" kutucuğunu seçme](./media/quickstart-create-data-factory-portal/select-azure-blob-linked-service.png)
1. Yeni bağlı hizmet (Azure Blob depolama) sayfasında, aşağıdaki adımları izleyin: 

   a. **Ad** için **AzureStorageLinkedService** adını girin.

   b. **Depolama hesabı adı** alanı için Azure depolama hesabınızın adını seçin.

   c. Data Factory hizmetinin depolama hesabına bağlanabildiğini onaylamak için **Bağlantıyı sına**'yı seçin. 

   d. Bağlı hizmeti kaydetmek için **Son**’u seçin. 

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu yordamda iki veri kümesi oluşturursunuz: **InputDataset** ve **OutputDataset**. Bu veri kümeleri **AzureBlob** türündedir. Bunlar, önceki bölümde oluşturduğunuz Azure Depolama bağlı hizmetine başvurur. 

Giriş veri kümesi, giriş klasöründeki kaynak verileri temsil eder. Giriş veri kümesi tanımında, kaynak verileri içeren blob kapsayıcısını (**adftutorial**), klasörü (**input**) ve dosyayı (**emp.txt**) belirtirsiniz. 

Çıkış veri kümesi hedefe kopyalanan verileri temsil eder. Çıkış veri kümesi tanımında, verilerin kopyalandığı blob kapsayıcısını (**adftutorial**), klasörü (**output**) ve dosyayı belirtirsiniz. Bir işlem hattının her çalıştırmasıyla ilişkili benzersiz bir Kimlik vardır. Bu kimliğe **RunId** sistem değişkenini kullanarak erişebilirsiniz. Çıkış dosyasının adı, işlem hattının çalıştırma kimliği temelinde dinamik olarak belirlenir.   

Bağlı hizmet ayarlarında, kaynak verileri içeren Azure depolama hesabını belirtmiştiniz. Kaynak veri kümesi ayarlarında, kaynak verilerin tam olarak nerede durduğunu (blob kapsayıcısı, klasör ve dosya) belirtirsiniz. Havuz veri kümesi ayarlarında, verilerin nereye kopyalandığını (blob kapsayıcısı, klasör ve dosya) belirtirsiniz. 
 
1. **+** (artı) düğmesini seçip **Veri Kümesi**'ni seçin.

   ![Veri kümesi oluşturma menüsü](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. **Yeni veri kümesi** sayfasında **Azure Blob depolama**' yı seçin ve ardından **devam**' ı seçin. 

   ![“Azure Blob Depolama”yı seçme](./media/quickstart-create-data-factory-portal/select-azure-blob-dataset.png)
1. **Biçim Seç** sayfasında verilerinizin biçim türünü seçin ve ardından **devam**' ı seçin. Bu durumda, içeriği ayrıştırmadan dosyaları olduğu gibi kopyaladığınızda **ikili** ' ı seçin.

    ![Veri biçimi türü](./media/doc-common-process/select-binary.png)

1. **Özellikleri ayarla** sayfasında, aşağıdaki adımları uygulayın:

    a. **Ad**' ın altında **ınputdataset**girin. 

    b. **Bağlı hizmet** için **AzureStorageLinkedService** hizmetini seçin.

    c. **Dosya yolu** için **Gözat** düğmesini seçin.

    d. **Dosya veya klasör seçin** penceresinde **adftutorial** kapsayıcısındaki **input** klasörüne göz atın ve **emp.txt** dosyasını seçip **Son**'u seçin.
    
    e. **Devam**'ı seçin.   

    ![Inputdataset özelliklerini ayarlama](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Çıktı veri kümesini oluşturmak için adımları yineleyin:  

    a. **+** (artı) düğmesini seçip **Veri Kümesi**'ni seçin.

    b. **Yeni veri kümesi** sayfasında **Azure Blob depolama**' yı seçin ve ardından **devam**' ı seçin.

    c. **Biçim Seç** sayfasında verilerinizin biçim türünü seçin ve ardından **devam**' ı seçin.

    d. **Özellikleri ayarla** sayfasında, ad Için **outputdataset** ' i belirtin. Bağlı hizmet olarak **AzureStorageLinkedService** öğesini seçin.

    e. **Dosya yolu**altında **adföğreticisi/çıkış**girin. **Çıkış** klasörü yoksa, kopyalama etkinliği çalışma zamanında oluşturur.

    f. **Devam**'ı seçin.   

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma 
Bu yordamda, giriş ve çıkış veri kümelerini kullanan kopyalama etkinliğiyle bir işlem hattı oluşturur ve doğrularsınız. Kopyalama etkinliği, giriş veri kümesi ayarlarında belirttiğiniz dosyadaki verileri çıkış veri kümesi ayarlarında belirttiğiniz dosyaya kopyalar. Giriş veri kümesi yalnızca bir klasörü belirtiyorsa (dosya adını belirtmiyorsa), kopyalama etkinliği kaynak klasördeki tüm dosyaları hedefe kopyalar. 

1. **+** (artı) düğmesini seçip **İşlem Hattı**'nı seçin. 

1. **Genel** sekmesinde **Ad** için **CopyPipeline** değerini belirtin. 

1. **Etkinlikler** araç kutusunda **Taşı ve Dönüştür**’ü genişletin. **Etkinlikler** araç kutusundan **veri kopyalama** etkinliğini ardışık düzen Tasarımcısı yüzeyine sürükleyin. Ayrıca, **Etkinlikler** araç kutusunda etkinlikler için arama yapabilirsiniz. **Ad** için **CopyFromBlobToBlob** adını belirtin.

1. Kopyalama etkinliği ayarlarında **Kaynak** sekmesine geçin ve **Kaynak Veri Kümesi** olarak **InputDataset** öğesini seçin.

1. Kopyalama etkinliği ayarlarında **Kaynak** sekmesine geçin ve **Havuz Veri Kümesi** olarak **OutputDataset** öğesini seçin.

1. İşlem hattı ayarlarını doğrulamak için işlem hattı araç çubuğunda **Doğrula**'ya tıklayın. İşlem hattının başarıyla doğrulandığını onaylayın. Doğrulama çıktısını kapatmak için **>>** (sağ ok) düğmesini seçin. 

## <a name="debug-the-pipeline"></a>İşlem hattında hata ayıklama
Bu adımda, işlem hattını Data Factory'de dağıtmadan önce hata ayıklama yaparsınız. 

1. Tuvalin üzerindeki işlem hattı araç çubuğunun üzerinde **Hata Ayıkla**'ya tıklayarak test çalıştırması tetikleyin. 
    
1. Alt kısımdaki işlem hattı ayarlarının **Çıktı** sekmesinde işlem hattı çalıştırmasının durumunu gördüğünüzü onaylayın. 

1. **adftutorial** kapsayıcısının **output** klasöründe bir çıkış dosyası gördüğünüzü onaylayın. Çıkış klasörü yoksa, Data Factory hizmeti tarafından otomatik olarak oluşturulur. 

## <a name="trigger-the-pipeline-manually"></a>İşlem hattını el ile tetikleme
Bu yordamda, varlıkları (bağlı hizmetler, veri kümeleri, işlem hatları) Azure Data Factory'ye dağıtırsınız. Ardından, işlem hattı çalıştırmasını el ile tetiklersiniz. 

1. Bir işlem hattını tetiklemeden önce varlıkları Data Factory'de yayımlamanız gerekir. Yayımlamak için üst taraftan **Tümünü Yayımla**'yı seçin. 

   ![Yayımla düğmesi](./media/quickstart-create-data-factory-portal/publish-button.png)
1. İşlem hattını el ile tetiklemek için, işlem hattı araç çubuğunda **tetikleyici Ekle** ' yi seçin ve sonra **Şimdi Tetikle**' yi seçin. **İşlem Hattı Çalıştırma** sayfasında **Son**’u seçin.

## <a name="monitor-the-pipeline"></a>İşlem hattını izleme

1. Soldaki **İzleyici** sekmesine geçin. Listeyi yenilemek için **Yenile** düğmesini kullanın.

   ![İşlem hattı çalıştırmalarını izleme sekmesi](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. **Eylemler**'in altındaki **Etkinlik Çalıştırmalarını Görüntüle** bağlantısını seçin. Bu sayfada kopyalama etkinliği çalıştırmasının durumunu görürsünüz. 

1. Kopyalama işlemiyle ilgili ayrıntıları görüntülemek için **Eylemler** sütunundaki **Ayrıntılar**’ı (gözlük resmi) seçin. Özelliklerle ilgili ayrıntılar için bkz. [Kopyalama Etkinliğine genel bakış](copy-activity-overview.md). 

   ![Kopyalama işlemi ayrıntıları](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. **Çıkış** klasöründe yeni bir dosya gördüğünüzü onaylayın. 
1. İşlem **hattı çalıştırmaları** bağlantısını seçerek **etkinlik çalıştırmaları** görünümünden işlem **hattı çalıştırmaları** görünümüne dönebilirsiniz. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>İşlem hattını bir zamanlamaya göre tetikleme
Bu yordamda bu adım isteğe bağlıdır. İşlem hattını düzenli aralıklarla (saatlik, günlük, vb.) çalıştırılacak şekilde zamanlamak için bir *zamanlayıcı tetikleyicisi* oluşturabilirsiniz. Bu yordamda, belirttiğiniz bitiş tarihine ve saatine kadar her dakika çalıştırılacak bir tetikleyici oluşturursunuz. 

1. **Yazar** sekmesine geçin. 

1. İşlem hattınızla gidin, işlem hattı araç çubuğunda **tetikleyici Ekle** ' yi seçin ve ardından **Yeni/Düzenle**' yi seçin. 

1. **Tetikleyici Ekle** sayfasında **Tetikleyici seç**’i ve sonra **Yeni**’yi seçin. 

1. **Yeni Tetikleyici** sayfasındaki **Son** bölümünden **Tarihinde** öğesini seçin, bitiş saati olarak geçerli saatten birkaç dakika sonrasını belirtin ve **Uygula**'yı seçin. 

   Her işlem hattı çalıştırmasının bir maliyeti olduğundan, bitiş saati olarak başlangıç saatinden yalnızca birkaç dakika sonrasını belirtin. Bunun aynı güne ait olduğundan emin olun. Ancak, işlem hattının yayımlama süresi ve bitiş zamanı arasında çalışması için yeterli zaman olduğundan emin olun. Tetikleyici siz çözümü kullanıcı arabiriminde kaydettiğinizde değil ancak Data Factory'de yayımladığınızda devreye girer. 

1. **Yeni tetikleyici** sayfasında, **etkinleştirilmiş** onay kutusunu seçin ve ardından **Kaydet**' i seçin. 

   ![Yeni tetikleyici ayarı](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Uyarı iletisini gözden geçirin ve **Son**’u seçin.

1. Değişiklikleri Data Factory'de yayımlamak için **Tümünü Yayımla**'yı seçin. 

1. Soldaki **İzleyici** sekmesine geçin. Listeyi yenilemek için **Yenile**’yi seçin. İşlem hattının yayımlama saatinden bitiş saatine kadar dakikada bir çalıştırıldığını görürsünüz. 

   **Tetikleyen** sütunundaki değerlere dikkat edin. El ile tetikleyici çalıştırması daha önce uyguladığınız bir adıma (**Şimdi Tetikle**) aittir. 

1. **Tetikleyici Çalıştırmaları** görünümüne geçin. 

1. **Çıktı** klasöründe belirtilen son tarih ve saate kadar her işlem hattı çalıştırması için bir çıktı dosyası oluşturulduğunu onaylayın. 

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalar. Data Factory’yi daha fazla senaryoda kullanma hakkında bilgi almak için [öğreticileri](tutorial-copy-data-portal.md) okuyun. 
