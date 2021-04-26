---
title: Veri akışları ile Delta Gölü ETL
description: Bu öğretici, Delta Gölü verileri dönüştürmek ve analiz etmek için veri akışlarını kullanmaya yönelik adım adım yönergeler sağlar
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/16/2021
ms.openlocfilehash: 4a88ed2df74d3eebb96c42e2cdc87b14153419cd
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565381"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Veri akışlarını eşleme kullanarak Delta Gölü verileri dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, Azure Data Lake Storage (ADLS) Gen2 'de verileri analiz etmenize ve dönüştürmenizi ve bunu Delta Gölü içinde depolamanıza olanak tanıyan veri akışları oluşturmak için veri akışı tuvalini kullanacaksınız.

## <a name="prerequisites"></a>Önkoşullar
* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**. ADLS depolamayı *kaynak* ve *Havuz* veri depoları olarak kullanırsınız. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md).

Bu öğreticide dönüştürtiğimiz dosya MoviesDB.csv, [burada](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)bulunabilir. Dosyayı GitHub 'dan almak için, bir. csv dosyası olarak yerel olarak kaydetmek üzere içeriği istediğiniz bir metin düzenleyicisine kopyalayın. Dosyayı depolama hesabınıza yüklemek için bkz. [Azure Portal Blobları karşıya yükleme](../storage/blobs/storage-quickstart-blobs-portal.md). Örnekler ' Sample-Data ' adlı bir kapsayıcıya başvuracaktır.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Bu adımda, bir veri fabrikası oluşturur ve veri fabrikasında bir işlem hattı oluşturmak için Data Factory UX 'i açarsınız.

1. **Microsoft Edge** veya **Google Chrome**'ı açın. Şu anda Data Factory UI yalnızca Microsoft Edge ve Google Chrome Web tarayıcılarında desteklenir.
1. Sol taraftaki menüden **kaynak**  >  **tümleştirmesi**  >  **Data Factory** oluştur ' u seçin.
1. **Yeni Veri Fabrikası** sayfasında, **ad**' ın altına **ADFTutorialDataFactory** girin.
1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

    a. **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

    b. **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
         
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md). 
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** bölümünden veri fabrikası için bir konum seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri Fabrikası tarafından kullanılan veri depoları (örneğin, Azure depolama ve SQL veritabanı) ve işlemler (örneğin, Azure HDInsight) başka bölgelerde olabilir.
1. **Oluştur**’u seçin.
1. Oluşturma işlemi tamamlandıktan sonra, Bildirim Merkezi ' nde bildirimi görürsünüz. Data Factory sayfasına gitmek için **Kaynağa Git** ' i seçin.
1. Data Factory Kullanıcı Arabirimini (UI) ayrı bir sekmede başlatmak için **Geliştir ve İzle**’yi seçin.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Veri akışı etkinliği ile işlem hattı oluşturma

Bu adımda, bir veri akışı etkinliği içeren bir işlem hattı oluşturacaksınız.

1. **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin.

   ![İşlem hattı oluşturma](./media/doc-common-process/get-started-page.png)

1. İşlem hattının **genel** sekmesinde, Işlem hattının **adı** için **deltalake** girin.
1. **Etkinlikler** bölmesinde, **taşıma ve dönüştürme** Accordion ' ı genişletin. Bölmedeki **veri akışı** etkinliğini, işlem hattı tuvaline sürükleyin ve bırakın.

    ![Veri akışı etkinliğini geçirebileceğiniz işlem hattı tuvalini gösteren ekran görüntüsü.](media/tutorial-data-flow/activity1.png)
1. **Veri akışı ekleme** açılır penceresinde **Yeni veri akışı oluştur** ' u seçin ve ardından veri akışınızı **deltalake** olarak adlandırın. Bittiğinde son ' a tıklayın.

    ![Yeni bir veri akışı oluştururken veri akışınızı nerede bulabileceğiniz gösteren ekran görüntüsü.](media/tutorial-data-flow/activity2.png)
1. İşlem hattı tuvalinin üst çubuğunda, **veri akışı hata ayıklama** kaydırıcısını üzerine kaydırın. Hata ayıklama modu, canlı bir Spark kümesine karşı dönüştürme mantığının etkileşimli olarak test edilmesine olanak tanır. Veri akışı kümelerinin ısınma için 5-7 dakika sürer ve veri akışı geliştirmeyi planlarsa, kullanıcıların hata ayıklamanın ilk kez etkinleştirilmesi önerilir. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

    ![Veri akışı hata ayıklama kaydırıcısının nerede olduğunu gösteren ekran görüntüsü.](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Veri akışı tuvalinde dönüştürme mantığı oluşturma

Bu öğreticide iki veri akışı oluşturacaksınız. Cist veri akışı, yukarıdaki filmler CSV dosyasından yeni bir Delta Gölü oluşturmak için havuza yönelik basit bir kaynaktır. Son olarak, bu akış tasarımını, Delta Gölü verileri güncelleştirmek için aşağıda oluşturacaksınız.

![Son akış](media/data-flow/data-flow-tutorial-6.png &quot;Son akış")

### <a name="tutorial-objectives"></a>Öğretici amaçları

1. Yukarıdaki MoviesCSV veri kümesi kaynağını alın, bundan yeni bir Delta Gölü oluşturur
1. ' 1 ' için 1988 filmlere ilişkin güncelleştirilmiş derecelendirmelere yönelik mantığı oluşturun
1. 1950 'deki tüm filmleri silme
1. Videoları 1960 ' den çoğaltarak 2021 için yeni filmler ekleyin

### <a name="start-from-a-blank-data-flow-canvas"></a>Boş bir veri akışı tuvalinden başlayın

1. Kaynak dönüşümüne tıklayın
1. Alt panelde veri kümesinin yanındaki yeni ' ye tıklayın 1 ADLS 2. için yeni bir bağlı hizmet oluşturun
1. Veri kümesi türü için ayrılmış metin Seç
1. "MoviesCSV" veri kümesini adlandırın 
1. Yukarıdaki depolamaya yüklediğiniz MoviesCSV dosyasının üzerine gelin
1. Virgülle sınırlandırılmış olarak ayarlayın ve ilk satıra üst bilgi ekleyin 
1. Kaynak yansıtma sekmesine gidin ve "veri türlerini Algıla" ya tıklayın
1. Projeksiyon kümesi oluşturduktan sonra devam edebilirsiniz 
1. Havuz dönüştürmesi ekleme
1. Delta, bir satır içi veri kümesi türüdür. ADLS 2. depolama hesabınıza işaret etmeniz gerekir.
   
   ![Satır içi veri kümesi](media/data-flow/data-flow-tutorial-5.png "Satır içi veri kümesi")

1. ADF 'nin Delta Gölü oluşturulmasını istediğiniz depolama kapsayıcıda bir klasör adı seçin
1. İşlem hattı tasarımcısına geri dönün ve hata ayıkla ' ya tıklayarak işlem hattını yalnızca bu veri akışı etkinliği tuvaliyle hata ayıklama modunda yürütün. Bu, yeni Delta Gölü ADLS 2. ' de oluşturur.
1. Fabrika kaynaklarından yeni > veri akışı ' na tıklayın. 
1. Kaynak olarak MoviesCSV yeniden kullanın ve "veri türlerini Algıla" düğmesine tıklayın.
1. Grafikte kaynak dönüşümünüze bir filtre dönüşümü ekleme
1. Yalnızca, çalışmak istediğiniz üç yıl ile eşleşen film satırlarına izin verin; bu, 1950, 1988 ve 1960 olacaktır.
1. Şimdi filtre dönüşümünüze türetilmiş bir sütun dönüştürmesi ekleyerek her bir 1988 filmi için derecelendirmeleri ' 1 ' öğesine güncelleştirin
1. Aynı türetilmiş sütunda, mevcut bir yılı alarak 2021 için film oluşturun ve yılı 2021 olarak değiştirin. Şimdi 1960 ' i seçelim.
1. Bu üç türetilmiş sütunlarınızın şöyle görünmesini sağlayacak

   ![Türetilmiş sütun](media/data-flow/data-flow-tutorial-2.png "Türetilmiş sütun")
   
1. ```Update, insert, delete, and upsert``` ilkeler alter Row dönüşümünden oluşturulur. Türetilmiş sütununuzu değiştirdikten sonra bir alter Row dönüştürmesi ekleyin.
1. Alter Row ilkeleriniz şöyle görünmelidir.

   ![Satırı değiştirme](media/data-flow/data-flow-tutorial-3.png "Satırı değiştirme")
   
1. Her bir alter Row türü için uygun ilkeyi ayarlamış olduğunuza göre, havuz dönüşümünde doğru güncelleştirme kurallarının ayarlanmış olduğunu kontrol edin

   ![Havuz](media/data-flow/data-flow-tutorial-4.png "Havuz")
   
1. Burada, ADLS 2. Data Lake ' de Delta Gölü havuzunu kullanıyoruz ve ekleme, güncelleştirme, silme yapılmasına izin veriliyor. 
1. Anahtar sütunlarının, film birincil anahtar sütunu ve yıl sütunundan oluşan bir bileşik anahtar olduğunu unutmayın. Bunun nedeni, 1960 satırlarını çoğaltarak sahte 2021 filmler oluşturuyoruz. Bu, benzersizlik sağlayarak mevcut satırları ararken çarpışmaları önler.

### <a name="download-completed-sample"></a>Tamamlanan örnek indir
[Aşağıda, Gölü satırları güncelleştirme/silme için bir veri akışı olan Delta işlem hattı için örnek bir çözüm verilmiştir:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Sonraki adımlar

[Veri akışı ifade dili](data-flow-expression-functions.md)hakkında daha fazla bilgi edinin.
