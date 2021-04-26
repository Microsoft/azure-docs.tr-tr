---
title: Azure Data Factory Kullanıcı arabirimini kullanarak Azure Veri Fabrikası oluşturma
description: Azure Blob’daki bir konumdan başka bir konuma veri kopyalayan işlem hattı içeren bir veri fabrikası oluşturun.
author: linda33wj
ms.service: data-factory
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 8b33005b7f0d08e6da963457813b012199d89f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655110"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Hızlı Başlangıç: Azure Data Factory kullanıcı arabirimini kullanarak bir veri fabrikası oluşturma 

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Güncel sürüm](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu hızlı başlangıçta, Azure Data Factory UI kullanarak veri fabrikasını oluşturma ve izleme işlemi açıklanır. Bu veri fabrikasında oluşturduğunuz işlem hattı, verileri Azure Blob depolama alanındaki bir klasörden başka bir klasöre *kopyalar*. Azure Data Factory kullanarak verileri *dönüştürmek* için bkz. [veri akışını eşleme](concepts-data-flow-overview.md).

> [!NOTE]
> Azure Data Factory'yi kullanmaya yeni başlıyorsanız, bu hızlı başlangıçtaki işlemleri gerçekleştirmeden önce [Azure Data Factory'ye giriş](introduction.md) konusuna bakın. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Bu videoyu izlemeniz, Data Factory kullanıcı arabirimini anlamanıza yardımcı olur: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. [Azure Portal](https://portal.azure.com) gidin. 
1. Azure portalı menüsünden **Kaynak oluştur**'u seçin.
1. **Tümleştirme**' i ve ardından **Data Factory**' yi seçin. 
   
   :::image type="content" source="./media/doc-common-process/new-azure-data-factory-menu.png" alt-text="Yeni bölmede Data Factory seçimi.":::

1. **Data Factory oluştur** sayfasında, **temel bilgiler** sekmesinde, veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

    a. Açılan listeden var olan bir kaynak grubunu seçin.

    b. **Yeni oluştur**' u seçin ve yeni bir kaynak grubunun adını girin.
    
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md). 

1. **Bölge** için, veri fabrikasının konumunu seçin.

   Listede yalnızca Data Factory tarafından desteklenen ve Azure Data Factory meta verilerinizin depolanacağı konumlar gösterilir. Data Factory kullanımı ile ilişkili veri depoları (Azure depolama ve Azure SQL veritabanı gibi) ve bu işlemler (Azure HDInsight gibi) başka bölgelerde çalıştırılabilir.
 
1. **Ad** için **ADFTutorialDataFactory** girin.
   Azure veri fabrikasının adı *genel olarak benzersiz* olmalıdır. Aşağıdaki hatayı görürseniz veri fabrikasının adını değiştirin (örneğin, **&lt; adınız &gt; ADFTutorialDataFactory**) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - adlandırma kuralları](naming-rules.md) makalesini inceleyin.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Yinelenen ad için yeni veri fabrikası hata iletisi.":::

1. **Sürüm** bölümünde **V2**'yi seçin.

1. Ileri ' yi seçin ve git **yapılandırması**' nı sonra **Git** yapılandırma onay kutusunu seçin.

1. **Gözden geçir + oluştur**' u seçin ve doğrulama geçtikten sonra **Oluştur** ' u seçin. Oluşturma işlemi tamamlandıktan sonra **Data Factory** sayfasına gitmek Için **Kaynağa Git** ' i seçin. 

1. Azure Data Factory Kullanıcı arabirimi (UI) uygulamasını ayrı bir tarayıcı sekmesinde başlatmak için **yazar & izleyici** kutucuğunu seçin.
   
    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Yazar & Izleyici kutucuğuna sahip Azure Data Factory ana sayfası.":::
   
   > [!NOTE]
   > Web tarayıcısının "yetkilendirme" sırasında takılı olduğunu görürseniz, **üçüncü taraf tanımlama bilgilerini ve site verilerini engelle** onay kutusunu temizleyin. Veya seçili durumda tutun, **login.microsoftonline.com** için bir özel durum oluşturun ve uygulamayı yeniden açmayı deneyin.
   

## <a name="create-a-linked-service"></a>Bağlı hizmet oluşturma
Bu yordamda, Azure depolama hesabınızı veri fabrikasına bağlamak için bağlı bir hizmet oluşturursunuz. Bağlı hizmetler, Data Factory hizmetinin bunlara bağlanmak için çalışma zamanında kullandığı bağlantı bilgilerini içerir.

1. Azure Data Factory Kullanıcı arabirimi sayfasında, sol bölmedeki [**Yönet**](./author-management-hub.md) sekmesini açın.

1. Bağlı hizmetler sayfasında **+ Yeni** ' yi seçerek yeni bir bağlı hizmet oluşturun.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Yeni bağlı hizmet.":::
   
1. **Yeni Bağlı Hizmet** sayfasında **Azure Blob Depolama**’yı seçip **Devam**’ı seçin. 

1. Yeni bağlı hizmet (Azure Blob depolama) sayfasında, aşağıdaki adımları izleyin: 

   a. **Ad** için **AzureStorageLinkedService** adını girin.

   b. **Depolama hesabı adı** Için Azure depolama hesabınızın adını seçin.

   c. Data Factory hizmetinin depolama hesabına bağlanabildiğini onaylamak için **Bağlantıyı sına**'yı seçin. 

   d. Bağlı hizmeti kaydetmek için **Oluştur** ' u seçin. 

      :::image type="content" source="./media/quickstart-create-data-factory-portal/linked-service.png" alt-text="Bağlı hizmet.":::


## <a name="create-datasets"></a>Veri kümeleri oluşturma
Bu yordamda iki veri kümesi oluşturursunuz: **InputDataset** ve **OutputDataset**. Bu veri kümeleri **AzureBlob** türündedir. Bunlar, önceki bölümde oluşturduğunuz Azure Depolama bağlı hizmetine başvurur. 

Giriş veri kümesi, giriş klasöründeki kaynak verileri temsil eder. Giriş veri kümesi tanımında, kaynak verileri içeren blob kapsayıcısını (**adftutorial**), klasörü (**input**) ve dosyayı (**emp.txt**) belirtirsiniz. 

Çıkış veri kümesi hedefe kopyalanan verileri temsil eder. Çıkış veri kümesi tanımında, verilerin kopyalandığı blob kapsayıcısını (**adftutorial**), klasörü (**output**) ve dosyayı belirtirsiniz. Bir işlem hattının her çalıştırmasıyla ilişkili benzersiz bir Kimlik vardır. Bu kimliğe **RunId** sistem değişkenini kullanarak erişebilirsiniz. Çıkış dosyasının adı, işlem hattının çalıştırma kimliği temelinde dinamik olarak belirlenir.   

Bağlı hizmet ayarlarında, kaynak verileri içeren Azure Depolama hesabını belirttiniz. Kaynak veri kümesi ayarlarında, kaynak verilerin tam olarak nerede durduğunu (blob kapsayıcısı, klasör ve dosya) belirtirsiniz. Havuz veri kümesi ayarlarında, verilerin nereye kopyalandığını (blob kapsayıcısı, klasör ve dosya) belirtirsiniz. 

1. Sol bölmeden **Yazar** sekmesini seçin.

1. **+**(Artı) düğmesini seçin ve ardından **veri kümesi**' ni seçin.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-dataset-menu.png" alt-text="Veri kümesi oluşturmak için menü.":::

1. **Yeni veri kümesi** sayfasında **Azure Blob depolama**' yı seçin ve ardından **devam**' ı seçin. 

1. **Biçim Seç** sayfasında verilerinizin biçim türünü seçin ve ardından **devam**' ı seçin. Bu durumda, içeriği ayrıştırmadan dosyaları olduğu gibi kopyaladığınızda **ikili** ' ı seçin.

   :::image type="content" source="./media/quickstart-create-data-factory-portal/select-format.png" alt-text="Biçim ' i seçin.":::   
   
1. **Özellikleri ayarla** sayfasında, aşağıdaki adımları uygulayın:

    a. **Ad**' ın altında **ınputdataset** girin. 

    b. **Bağlı hizmet** için **AzureStorageLinkedService** hizmetini seçin.

    c. **Dosya yolu** için **Gözat** düğmesini seçin.

    d. **Bir dosya veya klasör seçin** penceresinde, **adföğreticisi** kapsayıcısındaki **giriş** klasörüne gidin, **emp.txt** dosyasını seçin ve ardından **Tamam**' ı seçin.
    
    e. **Tamam**’ı seçin.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png" alt-text="Inputdataset için özellikleri ayarlayın.":::

1. Çıktı veri kümesini oluşturmak için adımları yineleyin:  

    a. **+**(Artı) düğmesini seçin ve ardından **veri kümesi**' ni seçin.

    b. **Yeni veri kümesi** sayfasında **Azure Blob depolama**' yı seçin ve ardından **devam**' ı seçin.

    c. **Biçim Seç** sayfasında verilerinizin biçim türünü seçin ve ardından **devam**' ı seçin.

    d. **Özellikleri ayarla** sayfasında, ad Için **outputdataset** ' i belirtin. Bağlı hizmet olarak **AzureStorageLinkedService** öğesini seçin.

    e. **Dosya yolu** altında **adföğreticisi/çıkış** girin. **Çıkış** klasörü yoksa, kopyalama etkinliği çalışma zamanında oluşturur.

    f. **Tamam**’ı seçin.   

    :::image type="content" source="./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png" alt-text="OutputDataset için özellikleri ayarlayın.":::    

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma 
Bu yordamda, giriş ve çıkış veri kümelerini kullanan kopyalama etkinliğiyle bir işlem hattı oluşturur ve doğrularsınız. Kopyalama etkinliği, giriş veri kümesi ayarlarında belirttiğiniz dosyadaki verileri çıkış veri kümesi ayarlarında belirttiğiniz dosyaya kopyalar. Giriş veri kümesi yalnızca bir klasörü belirtiyorsa (dosya adını belirtmiyorsa), kopyalama etkinliği kaynak klasördeki tüm dosyaları hedefe kopyalar. 

1. **+**(Artı) düğmesini seçin ve ardından Işlem **hattı**' nı seçin. 

1. Genel panelinde **Özellikler** altında, **ad** için **copypipeline** ' yı belirtin. Sonra sağ üst köşedeki Özellikler simgesine tıklayarak paneli daraltın.

1. **Etkinlikler** araç kutusunda **Taşı ve Dönüştür**’ü genişletin. **Etkinlikler** araç kutusundan **veri kopyalama** etkinliğini ardışık düzen Tasarımcısı yüzeyine sürükleyin. Ayrıca, **Etkinlikler** araç kutusunda etkinlikler için arama yapabilirsiniz. **Ad** için **CopyFromBlobToBlob** adını belirtin.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-activity.png" alt-text="Veri kopyalama etkinliği oluşturuluyor.":::   

1. Kopyalama etkinliği ayarlarında **Kaynak** sekmesine geçin ve **Kaynak Veri Kümesi** olarak **InputDataset** öğesini seçin.

1. Kopyalama etkinliği ayarlarında **Kaynak** sekmesine geçin ve **Havuz Veri Kümesi** olarak **OutputDataset** öğesini seçin.

1. İşlem hattı ayarlarını doğrulamak için işlem hattı araç çubuğunda **Doğrula**'ya tıklayın. İşlem hattının başarıyla doğrulandığını onaylayın. Doğrulama çıkışını kapatmak için sağ üst köşedeki doğrulama düğmesini seçin. 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-validate.png"   alt-text="İşlem hattını doğrulayın.":::

## <a name="debug-the-pipeline"></a>İşlem hattında hata ayıklama
Bu adımda, işlem hattını Data Factory'de dağıtmadan önce hata ayıklama yaparsınız. 

1. Tuvalin üzerindeki işlem hattı araç çubuğunun üzerinde **Hata Ayıkla**'ya tıklayarak test çalıştırması tetikleyin. 
    
1. Alt kısımdaki işlem hattı ayarlarının **Çıktı** sekmesinde işlem hattı çalıştırmasının durumunu gördüğünüzü onaylayın. 
 
    :::image type="content" source="./media/quickstart-create-data-factory-portal/pipeline-output.png" alt-text="İşlem hattı çalıştırma çıkışı":::

1. **adftutorial** kapsayıcısının **output** klasöründe bir çıkış dosyası gördüğünüzü onaylayın. Çıkış klasörü yoksa, Data Factory hizmeti tarafından otomatik olarak oluşturulur. 

## <a name="trigger-the-pipeline-manually"></a>İşlem hattını el ile tetikleme
Bu yordamda, varlıkları (bağlı hizmetler, veri kümeleri, işlem hatları) Azure Data Factory'ye dağıtırsınız. Ardından, işlem hattı çalıştırmasını el ile tetiklersiniz. 

1. Bir işlem hattını tetiklemeden önce varlıkları Data Factory'de yayımlamanız gerekir. Yayımlamak için, üstteki **Tümünü Yayımla** ' yı seçin. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/publish-all.png" alt-text="Tümünü Yayımla.":::    

1. İşlem hattını el ile tetiklemek için, işlem hattı araç çubuğunda **tetikleyici Ekle** ' yi seçin ve sonra **Şimdi Tetikle**' yi seçin. İşlem **hattı çalıştırma** sayfasında **Tamam**' ı seçin.

## <a name="monitor-the-pipeline"></a>İşlem hattını izleme

1. Soldaki **İzleyici** sekmesine geçin. Listeyi yenilemek için **Yenile** düğmesini kullanın.

    :::image type="content" source="./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png" alt-text="İşlem hattı çalıştırmalarını izleme sekmesi"::: 
1. **Copypipeline** bağlantısını seçtiğinizde, bu sayfada kopyalama etkinliğinin durumunu görürsünüz. 

1. Kopyalama işlemi hakkındaki ayrıntıları görüntülemek için **Ayrıntılar** (gözlük resmi) bağlantısını seçin. Özelliklerle ilgili ayrıntılar için bkz. [Kopyalama Etkinliğine genel bakış](copy-activity-overview.md). 

   :::image type="content" source="./media/quickstart-create-data-factory-portal/copy-operation-details.png" alt-text="İşlem ayrıntılarını kopyalama.":::
1. **Çıkış** klasöründe yeni bir dosya gördüğünüzü onaylayın. 
1. **Tüm işlem hattı çalıştırmaları** bağlantısını seçerek **etkinlik çalıştırmaları** görünümünden işlem **hattı çalıştırmaları** görünümüne dönebilirsiniz. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>İşlem hattını bir zamanlamaya göre tetikleme
Bu yordamda bu adım isteğe bağlıdır. İşlem hattını düzenli aralıklarla (saatlik, günlük, vb.) çalıştırılacak şekilde zamanlamak için bir *zamanlayıcı tetikleyicisi* oluşturabilirsiniz. Bu yordamda, belirttiğiniz bitiş tarihine ve saatine kadar her dakika çalıştırılacak bir tetikleyici oluşturursunuz. 

1. **Yazar** sekmesine geçin. 

1. İşlem hattınızla gidin, işlem hattı araç çubuğunda **tetikleyici Ekle** ' yi seçin ve ardından **Yeni/Düzenle**' yi seçin. 

1. **Tetikleyici Ekle** sayfasında **Tetikleyici seç**’i ve sonra **Yeni**’yi seçin. 

1. **Yeni tetikleyici** sayfasında, **son** bölümünde, **Tarih**' i seçin, geçerli saatten birkaç dakika sonra bir bitiş saati belirtin ve ardından **Tamam**' ı seçin. 

   Her işlem hattı çalıştırmasının bir maliyeti olduğundan, bitiş saati olarak başlangıç saatinden yalnızca birkaç dakika sonrasını belirtin. Bunun aynı güne ait olduğundan emin olun. Ancak, işlem hattının yayımlama süresi ve bitiş zamanı arasında çalışması için yeterli zaman olduğundan emin olun. Tetikleyici siz çözümü kullanıcı arabiriminde kaydettiğinizde değil ancak Data Factory'de yayımladığınızda devreye girer. 

1. **Yeni tetikleyici** sayfasında, **etkinleştirilmiş** onay kutusunu seçin ve ardından **Tamam**' ı seçin. 

    :::image type="content" source="./media/quickstart-create-data-factory-portal/trigger-settings-next.png" alt-text="Yeni tetikleyici ayarı.":::   
1. Uyarı iletisini gözden geçirin ve **Tamam**' ı seçin.

1. Değişiklikleri Data Factory yayımlamak için **Tümünü Yayımla** ' yı seçin. 

1. Soldaki **İzleyici** sekmesine geçin. Listeyi yenilemek için **Yenile**’yi seçin. İşlem hattının yayımlama saatinden bitiş saatine kadar dakikada bir çalıştırıldığını görürsünüz. 

   **Tetiklenen** sütunundaki değerlere dikkat edin. El ile tetikleyici çalıştırması daha önce uyguladığınız bir adıma (**Şimdi Tetikle**) aittir. 

1. **Tetikleyici çalıştırmaları** görünümüne geçin. 

1. **Çıktı** klasöründe belirtilen son tarih ve saate kadar her işlem hattı çalıştırması için bir çıktı dosyası oluşturulduğunu onaylayın. 

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, verileri bir konumdan Azure Blob depolama alanındaki başka bir konuma kopyalar. Data Factory’yi daha fazla senaryoda kullanma hakkında bilgi almak için [öğreticileri](tutorial-copy-data-portal.md) okuyun.
