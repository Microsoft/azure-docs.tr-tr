---
title: 'Hızlı başlangıç: eşleme veri akışı kullanarak veri dönüştürme'
description: Bu öğretici, veri akışı eşleme ile verileri dönüştürmek için Azure SYNAPSE Analytics 'i kullanmaya yönelik adım adım yönergeler sağlar.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 37696d2f4054e46125b39f3d5efa794ce54f94b5
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567732"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Hızlı başlangıç: veri akışlarını eşleme kullanarak verileri dönüştürme

Bu hızlı başlangıçta, verileri bir Azure Data Lake Storage 2. (ADLS 2.) kaynağından eşleme veri akışını kullanarak bir ADLS 2. havuzuna dönüştüren bir işlem hattı oluşturmak için Azure SYNAPSE Analytics 'i kullanacaksınız. Bu hızlı başlangıçta yapılandırma deseninin verileri, eşleme veri akışı kullanılarak dönüştürülürken Genişletilebilir

Bu hızlı başlangıçta aşağıdaki adımları uygulayın:

> [!div class="checklist"]
> * Azure SYNAPSE Analytics 'te veri akışı etkinliğine sahip bir işlem hattı oluşturun.
> * Dört dönüşümle bir eşleme veri akışı oluşturun.
> * İşlem hattında test çalıştırması yapma.
> * Veri akışı etkinliğini izleme

## <a name="prerequisites"></a>Önkoşullar

* **Azure aboneliği**: Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* **Azure SYNAPSE çalışma alanı**: [hızlı başlangıç: bir Synapse çalışma alanı oluşturma](quickstart-create-workspace.md)yönergelerini takip eden Azure Portal kullanarak bir Synapse çalışma alanı oluşturun.
* **Azure depolama hesabı**: ADLS depolamayı *kaynak* ve *Havuz* veri depoları olarak kullanırsınız. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../storage/common/storage-account-create.md).

    Bu öğreticide dönüştürtiğimiz dosya MoviesDB.csv, [burada](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)bulunabilir. Dosyayı GitHub 'dan almak için, bir. csv dosyası olarak yerel olarak kaydetmek üzere içeriği istediğiniz bir metin düzenleyicisine kopyalayın. Dosyayı depolama hesabınıza yüklemek için bkz. [Azure Portal Blobları karşıya yükleme](../storage/blobs/storage-quickstart-blobs-portal.md). Örnekler ' Sample-Data ' adlı bir kapsayıcıya başvuracaktır.

### <a name="navigate-to-the-synapse-studio"></a>SYNAPSE Studio 'ya gidin

Azure SYNAPSE çalışma alanınız oluşturulduktan sonra, SYNAPSE Studio 'Yu açmak için iki yol vardır:

* [Azure Portal](https://ms.portal.azure.com/#home)SYNAPSE çalışma alanınızı açın. Başlarken ' in altındaki Open SYNAPSE Studio kartını **Aç** ' ı seçin.
* [Azure SYNAPSE Analytics](https://web.azuresynapse.net/) 'i açın ve çalışma alanınızda oturum açın.

Bu hızlı başlangıçta, örnek olarak "adftest2020" adlı çalışma alanını kullanırız. SYNAPSE Studio giriş sayfasına otomatik olarak gidecektir.

![SYNAPSE Studio giriş sayfası](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Veri akışı etkinliği ile işlem hattı oluşturma

İşlem hattı, bir dizi etkinliğin yürütülmesi için mantıksal akışı içerir. Bu bölümde, bir veri akışı etkinliği içeren bir işlem hattı oluşturacaksınız.

1. **Tümleştirin** sekmesine gidin. Ardışık düzen üstbilgisinin yanındaki artı simgesini seçin ve işlem hattı ' nı seçin.

   ![Yeni işlem hattı oluşturma](media/doc-common-process/new-pipeline.png)

1. İşlem hattının **Özellikler** ayarları sayfasında, **ad** için **transformfilmlerini** girin.

1. *Etkinlikler* bölmesindeki *Taşı ve Dönüştür* altında, **veri akışını** işlem hattı tuvaline sürükleyin.

1. **Veri akışı ekleme** sayfası açılır penceresinde **Yeni veri akışı**  ->  **veri akışı** oluştur ' u seçin. Bittiğinde **Tamam** ' a tıklayın.

   ![Veri akışı oluşturma](media/quickstart-data-flow/new-data-flow.png)

1. **Özellikler** sayfasında veri akışı **dönüştürfilmlerinizi** adlandırın.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Veri akışı tuvalinde dönüştürme mantığı oluşturma

Veri akışınızı oluşturduktan sonra otomatik olarak veri akışı tuvaline gönderilir. Bu adımda, ADLS depolamadaki MoviesDB.csv alan bir veri akışı oluşturacaksınız ve 1910 'den 2000 ' e kadar olan ortalama Comedies sayısını toplayacaksınız. Daha sonra bu dosyayı ADLS depolamasına geri yazacaksınız.

1. Veri akışı tuvalinin üzerinde, **veri akışı hata ayıklama** kaydırıcısını üzerine kaydırın. Hata ayıklama modu, canlı bir Spark kümesine karşı dönüştürme mantığının etkileşimli olarak test edilmesine olanak tanır. Veri akışı kümelerinin ısınma için 5-7 dakika sürer ve veri akışı geliştirmeyi planlarsa, kullanıcıların hata ayıklamanın ilk kez etkinleştirilmesi önerilir. Daha fazla bilgi için bkz. [hata ayıklama modu](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    ![Hata ayıklamayı kaydır](media/quickstart-data-flow/debug-on.png)

1. Veri akışı tuvalinde, **Kaynak Ekle** kutusuna tıklayarak bir kaynak ekleyin.

1. Kaynak **MoviesDB** adlandırın. Yeni kaynak veri kümesi oluşturmak için **Yeni** ' ye tıklayın.

    ![Yeni kaynak veri kümesi oluşturma](media/quickstart-data-flow/new-source-dataset.png)

1. **Azure Data Lake Storage 2.** seçin. Devam’a tıklayın.

    ![Azure Data Lake Storage 2. seçin](media/quickstart-data-flow/select-source-dataset.png)

1. **Delimitedtext** öğesini seçin. Devam’a tıklayın.

1. Veri kümenizi **MoviesDB** olarak adlandırın. Bağlı hizmet açılan menüsünde **Yeni**' yi seçin.

1. Bağlı hizmet oluşturma ekranında, ADLS 2. bağlı hizmetinizi **ADLSGen2** olarak adlandırın ve kimlik doğrulama yönteminizi belirtin. Ardından bağlantı kimlik bilgilerinizi girin. Bu hızlı başlangıçta, depolama hesabımızla bağlantı kurmak için hesap anahtarı 'nı kullanıyoruz. Kimlik bilgilerinizin doğru girildiğini doğrulamak için **Bağlantıyı Sına** ' ya tıklayabilirsiniz. İşiniz bittiğinde **Oluştur** ' a tıklayın.

    ![Kaynak bağlı hizmeti oluşturma](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. Veri kümesi oluşturma ekranına geri döndüğünüzde, **dosya yolu** alanı altında dosyanızın bulunduğu yeri girin. Bu hızlı başlangıçta "MoviesDB.csv" dosyası "Sample-Data" kapsayıcısında bulunur. Dosya üst bilgileri içerdiğinden **ilk satırı üst bilgi olarak** denetleyin. Üst bilgi şemasını depolama alanındaki dosyadan doğrudan içeri aktarmak için **bağlantı/depolama alanından** seçim yapın. Bittiğinde **Tamam** ' a tıklayın.

    ![Kaynak veri kümesi ayarları](media/quickstart-data-flow/source-dataset-properties.png)

1. Hata ayıklama kümeniz başlatıldıysa, kaynak dönüşümünün **veri önizleme** sekmesine gidin ve verilerin anlık görüntüsünü almak için **Yenile** ' ye tıklayın. Dönüşümün doğru yapılandırıldığını doğrulamak için veri önizlemeyi kullanabilirsiniz.

    ![Veri önizlemesi](media/quickstart-data-flow/data-preview.png)

1. Veri akışı tuvalindeki kaynak Düğümünüzün yanında, yeni bir dönüşüm eklemek için artı simgesine tıklayın. Eklemekte olduğunuz ilk dönüşüm bir **filtredir**.

    ![Filtre ekleme](media/quickstart-data-flow/add-filter.png)

1. Filtre dönüştürmesinin **Filtreyıllarınızı** adlandırın. **Filtre Uygula** ' nın yanındaki ifade kutusuna tıklayıp deyim Oluşturucu 'yu açın. Burada filtreleme koşullarınızı belirtirsiniz.

1. Veri akışı ifade Oluşturucusu, çeşitli dönüşümlerde kullanılacak ifadeleri etkileşimli bir şekilde oluşturmanıza olanak tanır. İfadeler, yerleşik işlevleri, giriş şemasından sütunları ve Kullanıcı tanımlı parametreleri içerebilir. İfadelerin nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [veri akışı ifade Oluşturucusu](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    Bu hızlı başlangıçta, 1910 ve 2000 yılları arasında gelen tarz komedi filmlerini filtrelemek istiyorsunuz. Yıl şu anda bir dize olduğu için işlevi kullanarak bir tamsayıya dönüştürmeniz gerekir ```toInteger()``` . 1910 ve 200-sabit yıl değerlerine göre karşılaştırmak için büyüktür veya eşittir (>=) ve küçüktür veya eşittir (<=) işleçlerini kullanın. Bu ifadeleri ve (&&) işleciyle birlikte toplayın. İfade şu şekilde gelir:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Hangi filmlerin ortak olduğunu bulmak için, ```rlike()``` ' komedi ' stilini sütun tarzları içinde bulmak için işlevini kullanabilirsiniz. Şunun için yıl karşılaştırmasına sahip rlıke ifadesini birleşim:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Filtreleme koşulunu belirtin](media/quickstart-data-flow/visual-expression-builder.png)

    Etkin bir hata ayıklama kümeniz varsa, kullanılan girişlerle karşılaştırılan ifade çıktısını görmek için **Yenile** ' ye tıklayarak mantığınızı doğrulayabilirsiniz. Veri akışı ifade dilini kullanarak bu mantığı nasıl gerçekleştirebileceğiniz üzerinde birden fazla doğru yanıt vardır.

    Deyiminizi tamamladıktan sonra Kaydet ' e tıklayın **ve son** ' a tıklayın.

1. Filtrenin düzgün çalıştığını doğrulamak için bir **veri önizlemesi** getirin.

1. Ekleyeceğiniz bir sonraki dönüşüm, **şema değiştiricisi** altında bir **Toplam** dönüşümdir.

    ![Toplam ekleme](media/quickstart-data-flow/add-aggregate.png)

1. Toplam dönüşüm **Aggregatecomedyıderecelendirmeleri** olarak adlandırın. **Gruplandırma ölçütü** sekmesinde, toplamaların filmi filmin geldiği yıla göre gruplamak için açılan listeden **yıl** ' ı seçin.

    ![Toplama ayarları 1](media/quickstart-data-flow/aggregate-settings.png)

1. **Toplamlar** sekmesine gidin. Sol metin kutusunda, toplam sütununu **Averagecomedyıderecelendirme** olarak adlandırın. Deyim Oluşturucu aracılığıyla toplama ifadesini girmek için sağ ifade kutusuna tıklayın.

    ![Toplama ayarları 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Sütun **derecelendirmesinin** ortalamasını almak için ```avg()``` toplama işlevini kullanın. **Derecelendirme** bir dize olduğu ve ```avg()``` sayısal bir girişi aldığı için değeri işlev aracılığıyla bir sayıya dönüştürmemiz gerekir ```toInteger()``` . Bu ifade şöyle görünür:

    ```avg(toInteger(Rating))```

    İşiniz bittiğinde **Kaydet ve son** ' a tıklayın.

    ![Ortalama komedi derecelendirmesi](media/quickstart-data-flow/average-comedy-rating.png)

1. Dönüştürme çıkışını görüntülemek için **veri önizleme** sekmesine gidin. Yalnızca iki sütun varsa, **yıl** ve **Averageıda ortalama derecelendirmesine** dikkat edin.

    ![Veri önizlemeyi topla](media/quickstart-data-flow/transformation-output.png)

1. Ardından, **hedef** altına bir **Havuz** dönüştürmesi eklemek istersiniz.

    ![Havuz ekleme](media/quickstart-data-flow/add-sink.png)

1. Havuz **havuzunuzu** adlandırın. Havuz veri kümenizi oluşturmak için **Yeni** ' ye tıklayın.

1. **Azure Data Lake Storage 2.** seçin. Devam’a tıklayın.

1. **Delimitedtext** öğesini seçin. Devam’a tıklayın.

1. Havuz veri kümenizi **MoviesSink** olarak adlandırın. Bağlı hizmet için 7. adımda oluşturduğunuz bağlı hizmeti ADLS 2. seçin. Verilerinizi yazmak için bir çıkış klasörü girin. Bu hızlı başlangıçta, ' örnek-veri ' kapsayıcısının ' output ' klasörüne yazıyoruz. Klasörün önceden var olması gerekmez ve dinamik olarak oluşturulabilir. **İlk satırı üst bilgi olarak** ayarlayın ve **içeri aktarma şeması** için **hiçbiri** ' ni seçin. Bittiğinde **Tamam** ' a tıklayın.

    ![Havuz veri kümesi özellikleri](media/quickstart-data-flow/sink-dataset-properties.png)

Artık veri akışınızı oluşturmayı tamamladınız. İşlem hattınızda çalıştırmak için hazırsınız.

## <a name="running-and-monitoring-the-data-flow"></a>Veri akışını çalıştırma ve izleme

Yayımlamadan önce bir işlem hattında hata ayıklaması yapabilirsiniz. Bu adımda, veri akışı işlem hattının hata ayıklama çalıştırmasını tetikleyeceksiniz. Veri önizleme verileri yazmazsa, bir hata ayıklama çalıştırması havuz Hedefinizdeki verileri yazar.

1. İşlem hattı tuvaline gidin. Hata ayıklama çalıştırmasını tetiklemek için **Hata Ayıkla** ' ya tıklayın.

    ![Hata ayıklama ardışık düzeni](media/quickstart-data-flow/debug-pipeline.png)

1. Veri akışı etkinliklerinin ardışık düzen hata ayıklaması, etkin hata ayıklama kümesini kullanır, ancak yine de başlatmak için en az bir dakika sürer. İlerlemeyi **Çıkış** sekmesi aracılığıyla izleyebilirsiniz. Çalıştırma başarılı olduktan sonra, izleme bölmesini açmak için göz gözlük simgesine tıklayın.

    ![Hata ayıklama çıkışı](media/quickstart-data-flow/debugging-output.png)

1. İzleme bölmesinde her bir dönüştürme adımında harcanan satır sayısını ve süreyi görebilirsiniz.

    ![Dönüştürme izlemesi](media/quickstart-data-flow/4-transformations.png)

1. Verilerin sütunları ve bölümlenmesi hakkında ayrıntılı bilgi almak için bir dönüşüme tıklayın.

    ![Dönüştürme ayrıntıları](media/quickstart-data-flow/transformation-details.png)

Bu hızlı başlangıcı doğru bir şekilde izlediyseniz havuz klasörünüze 83 satır ve 2 sütun yazmış olmanız gerekir. BLOB depolama alanınızı denetleyerek verileri doğrulayabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Azure SYNAPSE Analytics desteği hakkında bilgi edinmek için aşağıdaki makalelere ilerleyin:

> [!div class="nextstepaction"]
> İşlem [hattı ve Etkinlikler](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Eşleme veri akışına genel bakış](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Veri akışı ifade dili](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)