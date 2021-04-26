---
title: Azure Data Factory yönetilen bir sanal ağ eşleme veri akışı ile veri dönüştürme
description: Bu öğretici, veri akışları eşleme ile verileri dönüştürmek için Azure Data Factory kullanmaya yönelik adım adım yönergeler sağlar.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ad101bee84256662d1436ba8d8a49304aecb9129
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518289"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Eşleme veri akışını kullanarak verileri güvenli bir şekilde dönüştürme

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](./introduction.md).

Bu öğreticide, [Data Factory yönetilen sanal ağda](managed-virtual-network-private-endpoint.md)eşleme veri akışını kullanarak bir *Azure Data Lake Storage 2. kaynağından Data Lake Storage 2. havuzuna* veri kopyalayan ve dönüştüren bir işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini (UI) kullanacaksınız. Veri akışını eşleme kullanarak verileri dönüştürdüğünüzde, bu öğreticideki yapılandırma düzenine genişletebilirsiniz.

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
>
> * Veri fabrikası oluşturma.
> * Veri akışı etkinliğine sahip bir işlem hattı oluşturun.
> * Dört dönüşümle bir eşleme veri akışı oluşturun.
> * İşlem hattında test çalıştırması yapma.
> * Veri akışı etkinliğini izleyin.

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**. Data Lake Storage *kaynak* ve *Havuz* veri depoları olarak kullanırsınız. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal). *Depolama hesabının yalnızca seçili ağlardan erişime izin verdiğinden emin olun.* 

Bu öğreticide dönüştürecağımız dosya, bu [GitHub içerik sitesinde](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)bulunan moviesDB.csv. Dosyayı GitHub 'dan almak için, içeriği bir. csv dosyası olarak yerel olarak kaydetmek üzere seçtiğiniz bir metin düzenleyicisine kopyalayın. Dosyayı depolama hesabınıza yüklemek için bkz. [Azure Portal Blobları karşıya yükleme](../storage/blobs/storage-quickstart-blobs-portal.md). Örnekler **örnek veri** adlı bir kapsayıcıya başvuracaktır.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Bu adımda, bir veri fabrikası oluşturur ve veri fabrikasında bir işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini açarsınız.

1. Microsoft Edge veya Google Chrome 'ı açın. Şu anda yalnızca Microsoft Edge ve Google Chrome Web tarayıcıları Data Factory Kullanıcı arabirimini destekler.
1. Sol menüde, **kaynak**  >  **Analizi**  >  **Data Factory** oluştur ' u seçin.
1. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin.

   Veri fabrikasının adı *genel olarak benzersiz* olmalıdır. Ad değeri hakkında bir hata iletisi alırsanız, Data Factory için farklı bir ad girin (örneğin, Adınızadftutorialdatafactory). Data Factory yapıtlarının adlandırma kuralları için bkz.[Data Factory adlandırma kuralları](naming-rules.md).

1. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin.
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:

    * **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.
    * **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
         
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/management/overview.md). 
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** bölümünden veri fabrikası için bir konum seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri Fabrikası tarafından kullanılan veri depoları (örneğin, Azure depolama ve Azure SQL veritabanı) ve işlemler (örneğin, Azure HDInsight) başka bölgelerde olabilir.

1. **Oluştur**’u seçin.
1. Oluşturma işlemi tamamlandıktan sonra bildirim merkezinde bildirimi görürsünüz. **Data Factory** sayfasına gitmek Için **Kaynağa Git** ' i seçin.
1. Data Factory Kullanıcı Arabirimini (UI) ayrı bir sekmede başlatmak için **Geliştir ve İzle**’yi seçin.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Data Factory yönetilen sanal ağda Azure IR oluşturma

Bu adımda, bir Azure IR oluşturup Data Factory yönetilen sanal ağı etkinleştirirsiniz.

1. Data Factory portalında **Yönet**' e gidin ve yeni bir Azure IR oluşturmak için **Yeni** ' yi seçin.

   ![Yeni Azure IR oluşturmayı gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. **Tümleştirme çalışma zamanı kurulumu** sayfasında, gereken yeteneklere göre oluşturulacak tümleştirme çalışma zamanını seçin. Bu öğreticide, **Azure ve kendiliğinden konak** ' ı seçip **devam**' a tıklayın. 
1. Azure **' ı seçin ve** ardından **devam** ' a tıklayarak bir Azure tümleştirme çalışma zamanı oluşturun.

   ![Yeni bir Azure IR gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. **Sanal ağ yapılandırması (Önizleme)** altında **Etkinleştir**' i seçin.

   ![Yeni bir Azure IR etkinleştirmeyi gösteren ekran görüntüsü.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. **Oluştur**’u seçin.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Veri akışı etkinliği ile işlem hattı oluşturma

Bu adımda, bir veri akışı etkinliği içeren bir işlem hattı oluşturacaksınız.

1. **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin.

   ![İşlem hattı oluşturmayı gösteren ekran görüntüsü.](./media/doc-common-process/get-started-page.png)

1. İşlem hattının Özellikler bölmesinde işlem hattı adı için **Transformfilmlerini** girin.
1. **Etkinlikler** bölmesinde, **Taşı ve Dönüştür**' ü genişletin. **Veri akışı** etkinliğini bölmeden işlem hattı tuvaline sürükleyin.

1. **Veri akışı ekleme** açılır penceresinde **Yeni veri akışı oluştur** ' u seçin ve ardından **veri akışını eşleme**' yi seçin. İşiniz bittiğinde **Tamam ' ı** seçin.

    ![Eşleme veri akışını gösteren ekran görüntüsü.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Özellikler bölmesinde veri akışınızı **Dönüştürfilmlerinizi** adlandırın.
1. İşlem hattı tuvalinin üst çubuğunda, **veri akışı hata ayıklama** kaydırıcısını üzerine kaydırın. Hata ayıklama modu, canlı bir Spark kümesine karşı dönüştürme mantığının etkileşimli olarak test edilmesine olanak tanır. Veri akışı kümelerinin ısınma için 5-7 dakika sürer ve veri akışı geliştirmeyi planlarsa, kullanıcıların hata ayıklamanın ilk kez etkinleştirilmesi önerilir. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

    ![Veri akışı hata ayıklama kaydırıcısını gösteren ekran görüntüsü.](media/tutorial-data-flow-private/dataflow-debug.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Veri akışı tuvalinde dönüştürme mantığı oluşturma

Veri akışınızı oluşturduktan sonra otomatik olarak veri akışı tuvaline gönderilir. Bu adımda, Data Lake Storage moviesDB.csv dosyasını alan ve 1910 ' den 2000 ' e kadar olan Ortalama derecelendirme derecelendirmesine yönelik bir veri akışı oluşturacaksınız. Daha sonra bu dosyayı Data Lake Storage geri yazacaksınız.

### <a name="add-the-source-transformation"></a>Kaynak dönüşümünü ekleme

Bu adımda, Data Lake Storage 2. kaynak olarak ayarlarsınız.

1. Veri akışı tuvalinde, **Kaynak Ekle** kutusunu seçerek bir kaynak ekleyin.

1. Kaynak **MoviesDB** adlandırın. Yeni bir kaynak veri kümesi oluşturmak için **Yeni** ' yi seçin.

1. **Azure Data Lake Storage 2.**' yi seçin ve ardından **devam**' ı seçin.

1. **Delimitedtext**' i seçin ve ardından **devam**' ı seçin.

1. Veri kümenizi **MoviesDB** olarak adlandırın. Bağlı hizmet açılır penceresinde **Yeni**' yi seçin.

1. Bağlı hizmet oluşturma ekranında, Data Lake Storage 2. bağlı hizmetinizi **ADLSGen2** olarak adlandırın ve kimlik doğrulama yönteminizi belirtin. Ardından bağlantı kimlik bilgilerinizi girin. Bu öğreticide, depolama hesabımızla bağlantı kurmak için **hesap anahtarı** 'nı kullanıyoruz. 

1. **Etkileşimli yazma** özelliğini etkinleştirdiğinizden emin olun. Etkinleştirilmesi bir dakika sürebilir.

    ![Etkileşimli yazma gösteren ekran görüntüsü.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. **Bağlantıyı sına**’yı seçin. Depolama hesabı özel bir uç noktanın oluşturulması ve onaylanması gerekmeden bu hesaba erişimi etkinleştirmediğinden başarısız olması gerekir. Hata iletisinde, yönetilen özel uç nokta oluşturmak için izleyebileceğiniz özel bir uç nokta oluşturmak için bir bağlantı görmeniz gerekir. Alternatif olarak, **Yönet** sekmesine doğrudan gitmeniz ve yönetilen özel uç nokta oluşturmak için [Bu bölümdeki](#create-a-managed-private-endpoint) yönergeleri izlemeniz önemlidir.

1. İletişim kutusunu açık tutun ve depolama hesabınıza gidin.

1. Özel bağlantıyı onaylamak için [Bu bölümdeki](#approval-of-a-private-link-in-a-storage-account) yönergeleri izleyin.

1. İletişim kutusuna geri dönün. **Bağlantıyı yeniden sına** ' yı seçin ve bağlı hizmeti dağıtmak için **Oluştur** ' u seçin.

1. Veri kümesi oluşturma ekranında, dosyanın **dosya yolu** alanının altında bulunduğu yeri girin. Bu öğreticide, dosya moviesDB.csv kapsayıcı **örneği-verilerinde** bulunur. Dosyanın üst bilgileri olduğundan, **ilk satırı üst bilgi** onay kutusunu seçin. Üst bilgi şemasını depolama alanındaki dosyadan doğrudan içeri aktarmak için **bağlantı/depolama alanından** seçim yapın. İşiniz bittiğinde **Tamam ' ı** seçin.

    ![Kaynak yolunu gösteren ekran görüntüsü.](media/tutorial-data-flow-private/source-file-path.png)

1. Hata ayıklama kümeniz başlatıldıysa, kaynak dönüşümünün **veri önizleme** sekmesine gidin ve verilerin anlık görüntüsünü almak için **Yenile** ' yi seçin. Dönüşümün doğru yapılandırıldığını doğrulamak için veri önizlemeyi kullanabilirsiniz.

    ![Veri önizleme sekmesini gösteren ekran görüntüsü.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Yönetilen özel uç nokta oluşturma

Önceki bağlantıyı test ettiğinizde köprü kullanmıyorsanız, yolu izleyin. Şimdi oluşturduğunuz bağlı hizmete bağlanacak bir yönetilen özel uç noktası oluşturmanız gerekir.

1. **Yönet** sekmesine gidin.

   > [!NOTE]
   > **Yönet** sekmesi tüm Data Factory örnekleri için kullanılamayabilir. Bunu görmüyorsanız özel uç noktalara, **Yazar**  >  **bağlantıları**  >  **Özel uç noktası**' nı seçerek erişebilirsiniz.

1. **Yönetilen özel uç noktalar** bölümüne gidin.
1. **Yönetilen özel uç noktalar** altında **+ Yeni** ' yi seçin.

    ![Yönetilen özel uç noktalar Yeni düğmesini gösteren ekran görüntüsü.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Listeden **Azure Data Lake Storage 2.** kutucuğunu seçin ve **devam**' ı seçin.
1. Oluşturduğunuz depolama hesabının adını girin.
1. **Oluştur**’u seçin.
1. Birkaç saniye sonra, oluşturulan özel bağlantının bir onay ihtiyacı olduğunu görmeniz gerekir.
1. Oluşturduğunuz özel uç noktayı seçin. Özel uç noktayı, depolama hesabı düzeyinde onaylamaya yol açacak bir köprü görebilirsiniz.

    ![Özel uç nokta yönetme bölmesini gösteren ekran görüntüsü.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Bir depolama hesabındaki özel bağlantının onaylanması

1. Depolama hesabında, **Ayarlar** bölümü altında **Özel uç nokta bağlantıları** ' na gidin.

1. Oluşturduğunuz özel uç nokta ile onay kutusunu seçin ve **Onayla**' yı seçin.

    ![Özel uç nokta onaylama düğmesini gösteren ekran görüntüsü.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Bir açıklama ekleyin ve **Evet**' i seçin.
1. Data Factory ' deki **Yönet** sekmesinin **yönetilen özel uç noktalar** bölümüne geri dönün.
1. Bir dakika sonra, Özel uç noktanız için onay göründüğünü görmeniz gerekir.

### <a name="add-the-filter-transformation"></a>Filtre dönüşümünü ekleme

1. Veri akışı tuvalindeki kaynak Düğümünüzün yanında, yeni bir dönüşüm eklemek için artı simgesini seçin. Ekleyeceğiniz ilk dönüşüm bir **filtredir**.

    ![Filtre eklemeyi gösteren ekran görüntüsü.](media/tutorial-data-flow-private/add-filter.png)
1. Filtre dönüştürmesinin **Filtreyıllarınızı** adlandırın. **Filtre Uygula '** nın yanındaki ifade kutusunu seçerek ifade oluşturucuyu açın. Burada filtreleme koşullarınızı belirtirsiniz.

    ![Filteryıllar gösteren ekran görüntüsü.](media/tutorial-data-flow-private/filter-years.png)
1. Veri akışı ifade Oluşturucusu, çeşitli dönüşümlerde kullanılacak ifadeleri etkileşimli bir şekilde oluşturmanıza olanak tanır. İfadeler, yerleşik işlevleri, giriş şemasından sütunları ve Kullanıcı tanımlı parametreleri içerebilir. İfadelerin nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [veri akışı ifade Oluşturucusu](./concepts-data-flow-expression-builder.md).

    * Bu öğreticide, komedi tarzı 1910 ve 2000 yılları arasında gelen filmleri filtrelemek istersiniz. Yıl şu anda bir dize olduğundan, işlevini kullanarak bunu bir tamsayıya dönüştürmeniz gerekir ```toInteger()``` . 1910 ve 2000 sabit değer değerlerine göre karşılaştırmak için büyüktür veya eşittir (>=) ve küçüktür veya eşittir (<=) işleçlerini kullanın. Bu ifadeleri ve (&&) işleciyle birlikte toplayın. İfade şu şekilde gelir:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Hangi filmlerin ortak olduğunu bulmak için, ```rlike()``` ' komedi ' stilini sütun tarzları içinde bulmak için işlevini kullanabilirsiniz. Şunun için yıl karşılaştırmasına sahip rlıke ifadesini birleşim:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Etkin bir hata ayıklama kümeniz varsa, kullanılan girişlerle karşılaştırılan ifade çıktısını görmek için **Yenile** ' yi seçerek mantığınızı doğrulayabilirsiniz. Veri akışı ifade dilini kullanarak bu mantığı nasıl gerçekleştirebileceğiniz üzerinde birden fazla doğru yanıt vardır.

        ![Filtre ifadesini gösteren ekran görüntüsü.](media/tutorial-data-flow-private/filter-expression.png)

    * Deyiminizi tamamladıktan sonra **Kaydet ' i ve son '** u seçin.

1. Filtrenin düzgün çalıştığını doğrulamak için bir **veri önizlemesi** getirin.

    ![Filtrelenmiş veri önizlemeyi gösteren ekran görüntüsü.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Toplam dönüşümü ekleme

1. Ekleyeceğiniz bir sonraki dönüşüm, **şema değiştiricisi** altında bir **Toplam** dönüşümdir.

    ![Toplama eklemeyi gösteren ekran görüntüsü.](media/tutorial-data-flow-private/add-aggregate.png)
1. Toplam dönüşüm **Aggregatecomediderecelendirmenizi** adlandırın. **Gruplandırma ölçütü** sekmesinde, açılan kutudan **yıl** ' yı seçerek toplamaları filmin geldiği yıla göre gruplayın.

    ![Toplam grubunu gösteren ekran görüntüsü.](media/tutorial-data-flow-private/group-by-year.png)
1. **Toplamlar** sekmesine gidin. Sol metin kutusunda, toplam sütununu **Averagecomedyıderecelendirme** olarak adlandırın. Deyim Oluşturucu aracılığıyla toplama ifadesini girmek için sağ ifade kutusunu seçin.

    ![Toplam sütun adını gösteren ekran görüntüsü.](media/tutorial-data-flow-private/name-column.png)
1. Sütun **derecelendirmesinin** ortalamasını almak için ```avg()``` toplama işlevini kullanın. **Derecelendirme** bir dize olduğundan ve ```avg()``` sayısal bir girişi üstlendiği için değeri işlev aracılığıyla bir sayıya dönüştürmemiz gerekir ```toInteger()``` . Bu ifade şöyle görünür:

    ```avg(toInteger(Rating))```

1. İşiniz bittiğinde **Kaydet ' i ve son '** u seçin.

    ![Toplamanın kaydedilmesini gösteren ekran görüntüsü.](media/tutorial-data-flow-private/save-aggregate.png)
1. Dönüştürme çıkışını görüntülemek için **veri önizleme** sekmesine gidin. Yalnızca iki sütun varsa, **yıl** ve **Averageıda ortalama derecelendirmesine** dikkat edin.

### <a name="add-the-sink-transformation"></a>Havuz dönüşümünü ekleme

1. Ardından, **hedef** altına bir **Havuz** dönüştürmesi eklemek istersiniz.

    ![Havuz eklemeyi gösteren ekran görüntüsü.](media/tutorial-data-flow-private/add-sink.png)
1. Havuz **havuzunuzu** adlandırın. Havuz veri kümenizi oluşturmak için **Yeni** ' yi seçin.

    ![Havuz oluşturmayı gösteren ekran görüntüsü.](media/tutorial-data-flow-private/create-sink.png)
1. **Yeni veri kümesi** sayfasında **Azure Data Lake Storage 2.** ' yi seçin ve ardından **devam**' ı seçin.

1. **Biçim Seç** sayfasında, **Delimitedtext** ' i seçin ve ardından **devam**' ı seçin.

1. Havuz veri kümenizi **MoviesSink** olarak adlandırın. Bağlı hizmet için, kaynak dönüştürmesi için oluşturduğunuz **ADLSGen2** bağlı hizmeti seçin. Verilerinizi yazmak için bir çıkış klasörü girin. Bu öğreticide kapsayıcı **örnek verilerinde** klasör **çıkışına** yazıyoruz. Klasörün önceden var olması gerekmez ve dinamik olarak oluşturulabilir. **İlk satırı üst bilgi** onay kutusunu seçin ve **içeri aktarma şeması** için **hiçbiri** ' ni seçin. **Tamam**’ı seçin.

    ![Havuz yolunu gösteren ekran görüntüsü.](media/tutorial-data-flow-private/sink-file-path.png)

Artık veri akışınızı oluşturmayı tamamladınız. İşlem hattınızda çalıştırmak için hazırsınız.

## <a name="run-and-monitor-the-data-flow"></a>Veri akışını çalıştırma ve izleme

Yayımlamadan önce bir işlem hattında hata ayıklaması yapabilirsiniz. Bu adımda, veri akışı işlem hattının hata ayıklama çalıştırmasını tetiklersiniz. Veri önizleme verileri yazmıyor olsa da bir hata ayıklama çalıştırması, havuz Hedefinizdeki verileri yazar.

1. İşlem hattı tuvaline gidin. Hata ayıklama çalıştırmasını tetiklemek için **Hata Ayıkla** ' yı seçin.

1. Veri akışı etkinliklerinin ardışık düzen hata ayıklaması, etkin hata ayıklama kümesini kullanır, ancak yine de başlatmak için en az bir dakika sürer. İlerlemeyi **Çıkış** sekmesi aracılığıyla izleyebilirsiniz. Çalıştırma başarılı olduktan sonra, çalışma ayrıntıları için göz gözlük simgesini seçin.

1. Ayrıntılar sayfasında, satır sayısını ve her bir dönüştürme adımında harcanan süreyi görebilirsiniz.

    ![İzleme çalıştırmasını gösteren ekran görüntüsü.](media/tutorial-data-flow-private/run-details.png)
1. Verilerin sütunları ve bölümlenmesi hakkında ayrıntılı bilgi almak için bir dönüşüm seçin.

Bu öğreticiyi izlediyseniz, havuz klasörünüze 83 satır ve 2 sütun yazmış olmanız gerekir. BLOB depolama alanınızı denetleyerek verilerin doğru olduğunu doğrulayabilirsiniz.

## <a name="summary"></a>Özet

Bu öğreticide, [Data Factory yönetilen sanal ağ](managed-virtual-network-private-endpoint.md)'da veri akışını eşleme kullanarak bir Data Lake Storage 2. kaynağından Data Lake Storage 2. havuzuna veri kopyalayan ve dönüştüren bir işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini kullandınız.