---
title: Azure Data Factory bir eşleme veri akışı kullanarak veri dönüştürme
description: Bu öğretici, veri akışı eşleme ile verileri dönüştürmek için Azure Data Factory kullanmaya yönelik adım adım yönergeler sağlar
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 886e6e659dee2a898167054c5d76bc3977f27e11
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683633"
---
# <a name="transform-data-using-mapping-data-flows"></a>Veri akışlarını eşleme kullanarak verileri dönüştürme

Azure Data Factory kullanmaya yeni başlıyorsanız bkz. [Azure Data Factory'ye giriş](introduction.md).

Bu öğreticide, veri akışını eşleme kullanarak bir Azure Data Lake Storage (ADLS) Gen2 kaynağından bir ADLS 2. havuzuna verileri kopyalayan ve dönüştüren bir işlem hattı oluşturmak için Azure Data Factory Kullanıcı arabirimini (UX) kullanacaksınız. Bu öğreticideki yapılandırma deseninin verileri, eşleme veri akışı kullanılarak dönüştürülürken Genişletilebilir

Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri akışı etkinliğine sahip bir işlem hattı oluşturun.
> * Dört dönüşümle bir eşleme veri akışı oluşturun. 
> * İşlem hattında test çalıştırması yapma.
> * Veri akışı etkinliğini izleme

## <a name="prerequisites"></a>Ön koşullar
* **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* **Azure depolama hesabı**. ADLS depolamayı *kaynak* ve *Havuz* veri depoları olarak kullanırsınız. Depolama hesabınız yoksa, oluşturma adımları için bkz. [Azure depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md).

Bu öğreticide dönüştürtiğimiz dosya MoviesDB. csv ' dir ve [burada](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)bulunabilir. Dosyayı GitHub 'dan almak için, bir. csv dosyası olarak yerel olarak kaydetmek üzere içeriği istediğiniz bir metin düzenleyicisine kopyalayın. Dosyayı depolama hesabınıza yüklemek için bkz. [Azure portalı ile Blobları karşıya yükleme](../storage/blobs/storage-quickstart-blobs-portal.md). Örnekler ' Sample-Data ' adlı bir kapsayıcıya başvuracaktır.

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Bu adımda, bir veri fabrikası oluşturur ve veri fabrikasında bir işlem hattı oluşturmak için Data Factory UX 'i açarsınız. 

1. **Microsoft Edge** veya **Google Chrome**'ı açın. Şu anda Data Factory UI yalnızca Microsoft Edge ve Google Chrome Web tarayıcılarında desteklenir.
2. Sol menüde **kaynak oluştur** > **analiz** > **Data Factory**' yı seçin: 
  
   ![“Yeni” bölmesinde Data Factory seçimi](./media/doc-common-process/new-azure-data-factory-menu.png)

3. **Yeni veri fabrikası** sayfasında **Ad** bölümüne **ADFTutorialDataFactory** girin. 
 
   Azure data factory adı *küresel olarak benzersiz* olmalıdır. Ad değeriyle ilgili bir hata iletisi alırsanız, veri fabrikası için farklı bir ad girin. (örneğin, Adınızadftutorialdatafactory). Data Factory yapıtlarının adlandırma kuralları için bkz.[Data Factory adlandırma kuralları](naming-rules.md).
        
     ![Yeni veri fabrikası](./media/doc-common-process/name-not-available-error.png)
4. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğinizi** seçin. 
5. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
    a. **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin.

    b. **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin. 
         
    Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md). 
6. **Sürüm** bölümünde **V2**'yi seçin.
7. **Konum** bölümünden veri fabrikası için bir konum seçin. Açılan listede yalnızca desteklenen konumlar görüntülenir. Veri Fabrikası tarafından kullanılan veri depoları (örneğin, Azure depolama ve SQL veritabanı) ve işlemler (örneğin, Azure HDInsight) başka bölgelerde olabilir.
8. **Oluştur**'u seçin. 
9. Oluşturma işlemi tamamlandıktan sonra, Bildirim Merkezi ' nde bildirimi görürsünüz. Data Factory sayfasına gitmek için **Kaynağa Git** ' i seçin.
10. Data Factory Kullanıcı Arabirimini (UI) ayrı bir sekmede başlatmak için **Geliştir ve İzle**’yi seçin.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Veri akışı etkinliği ile işlem hattı oluşturma

Bu adımda, bir veri akışı etkinliği içeren bir işlem hattı oluşturacaksınız.

1. **Kullanmaya başlama** sayfasında **İşlem hattı oluştur** seçeneğini belirleyin. 

   ![İşlem hattı oluşturma](./media/doc-common-process/get-started-page.png)

1. İşlem hattının **genel** sekmesinde, Işlem hattının **adı** için **transformfilmlerini** girin.
1. Fabrika üst çubuğunda, **veri akışı hata ayıklama** kaydırıcısını üzerine kaydırın. Hata ayıklama modu, canlı bir Spark kümesine karşı dönüştürme mantığının etkileşimli olarak test edilmesine olanak tanır. Veri akışı kümelerinin ısınma için 5-7 dakika sürer ve veri akışı geliştirmeyi planlarsa, kullanıcıların hata ayıklamanın ilk kez etkinleştirilmesi önerilir. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

    ![Veri akışı etkinliği](media/tutorial-data-flow/dataflow1.png)
1. **Etkinlikler** bölmesinde, **taşıma ve dönüştürme** Accordion ' ı genişletin. Bölmedeki **veri akışı** etkinliğini, işlem hattı tuvaline sürükleyin ve bırakın.

    ![Veri akışı etkinliği](media/tutorial-data-flow/activity1.png)
1. **Veri akışı ekleme** açılır penceresinde **Yeni veri akışı oluştur** ' u seçin ve ardından veri akışı **dönüştürfilmlerinizi**adlandırın. Bittiğinde son ' a tıklayın.

    ![Veri akışı etkinliği](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Veri akışı tuvalinde dönüştürme mantığı oluşturma

Veri akışınızı oluşturduktan sonra otomatik olarak veri akışı tuvaline gönderilir. Bu adımda, ADLS depolamada moviesDB. csv ' yi alan ve 1910 ' den 2000 ' e kadar olan Ortalama derecelendirme derecelendirmesini toplayan bir veri akışı oluşturacaksınız. Daha sonra bu dosyayı ADLS depolamasına geri yazacaksınız.

1. Veri akışı tuvalinde, **Kaynak Ekle** kutusuna tıklayarak bir kaynak ekleyin.

    ![Veri akışı tuvali](media/tutorial-data-flow/dataflow2.png)
1. Kaynak **MoviesDB**adlandırın. Yeni kaynak veri kümesi oluşturmak için **Yeni** ' ye tıklayın.
    
    ![Veri akışı tuvali](media/tutorial-data-flow/dataflow3.png)
1. **Azure Data Lake Storage 2.** seçin. Devam ' a tıklayın.

    ![Veri kümesi](media/tutorial-data-flow/dataset1.png)
1. **Delimitedtext**öğesini seçin. Devam ' a tıklayın.

    ![Veri kümesi](media/tutorial-data-flow/dataset2.png)
1. Veri kümenizi **MoviesDB**olarak adlandırın. Bağlı hizmet açılan menüsünde **Yeni**' yi seçin.

    ![Veri kümesi](media/tutorial-data-flow/dataset3.png)
1. Bağlı hizmet oluşturma ekranında, ADLS Gen2 Linked Service **ADLSGen2** ' yi adlandırın ve kimlik doğrulama yönteminizi belirtin. Ardından bağlantı kimlik bilgilerinizi girin. Bu öğreticide, depolama hesabımızla bağlantı kurmak için hesap anahtarı 'nı kullanıyoruz. Kimlik bilgilerinizin doğru girildiğini doğrulamak için **Bağlantıyı Sına** ' ya tıklayabilirsiniz. İşiniz bittiğinde oluştur ' a tıklayın.

    ![Bağlı hizmet](media/tutorial-data-flow/ls1.png)
1. Veri kümesi oluşturma ekranına geri döndüğünüzde, dosyanın **dosya yolu** alanının altında bulunduğu yeri girin. Bu öğreticide, moviesDB. csv dosyası kapsayıcı örnek verilerinde bulunur. Dosya üst bilgileri içerdiğinden **ilk satırı üst bilgi olarak**denetleyin. Üst bilgi şemasını depolama alanındaki dosyadan doğrudan içeri aktarmak için **bağlantı/depolama alanından** seçim yapın. Bittiğinde Tamam ' a tıklayın.

    ![Veri kümeleri](media/tutorial-data-flow/dataset4.png)
1. Hata ayıklama kümeniz başlatıldıysa, kaynak dönüşümünün **veri önizleme** sekmesine gidin ve verilerin anlık görüntüsünü almak için **Yenile** ' ye tıklayın. Dönüşümün doğru yapılandırıldığını doğrulamak için veri önizlemeyi kullanabilirsiniz.
    
    ![Veri akışı tuvali](media/tutorial-data-flow/dataflow4.png)
1. Veri akışı tuvalindeki kaynak Düğümünüzün yanında, yeni bir dönüşüm eklemek için artı simgesine tıklayın. Eklemekte olduğunuz ilk dönüşüm bir **filtredir**.
    
    ![Veri akışı tuvali](media/tutorial-data-flow/dataflow5.png)
1. Filtre dönüştürmesinin **Filtreyıllarınızı**adlandırın. **Filtre Uygula** ' nın yanındaki ifade kutusuna tıklayıp deyim Oluşturucu 'yu açın. Burada filtreleme koşullarınızı belirtirsiniz. 
    
    ![Filtre](media/tutorial-data-flow/filter1.png)
1. Veri akışı ifade Oluşturucusu, çeşitli dönüşümlerde kullanılacak ifadeleri etkileşimli bir şekilde oluşturmanıza olanak tanır. İfadeler, yerleşik işlevleri, giriş şemasından sütunları ve Kullanıcı tanımlı parametreleri içerebilir. İfadelerin nasıl oluşturulacağı hakkında daha fazla bilgi için bkz. [veri akışı ifade Oluşturucusu](concepts-data-flow-expression-builder.md).
    
    Bu öğreticide, 1910 ve 2000 yılları arasında gelen tarz komedi filmlerini filtrelemek istersiniz. Yıl şu anda bir dize olduğu için ```toInteger()``` işlevini kullanarak bunu bir tamsayıya dönüştürmeniz gerekir. 1910 ve 200-sabit yıl değerlerine göre karşılaştırmak için büyüktür veya eşittir (> =) ve küçüktür veya eşittir (< =) işleçlerini kullanın. Bu ifadeleri ve (& &) işleciyle toplayın. İfade şu şekilde gelir:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Hangi filmlerin ortak olduğunu bulmak için ```rlike()``` işlevini kullanarak sütun tarzları ' komedi ' stilini bulabilirsiniz. Şunun için yıl karşılaştırmasına sahip rlıke ifadesini birleşim:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Etkin bir hata ayıklama kümeniz varsa, kullanılan girişlerle karşılaştırılan ifade çıktısını görmek için **Yenile** ' ye tıklayarak mantığınızı doğrulayabilirsiniz. Veri akışı ifade dilini kullanarak bu mantığı nasıl gerçekleştirebileceğiniz üzerinde birden fazla doğru yanıt vardır.
    
    ![Filtre](media/tutorial-data-flow/filter2.png)

    Deyiminizi tamamladıktan sonra Kaydet ' e tıklayın **ve son** ' a tıklayın.

1. Filtrenin düzgün çalıştığını doğrulamak için bir **veri önizlemesi** getirin.
    
    ![Filtre](media/tutorial-data-flow/filter3.png)
1. Ekleyeceğiniz bir sonraki dönüşüm, **şema değiştiricisi**altında bir **Toplam** dönüşümdir.
    
    ![Toplama](media/tutorial-data-flow/agg1.png)
1. Toplam dönüşüm **Aggregatecomedyıderecelendirmeleri**olarak adlandırın. **Gruplandırma ölçütü** sekmesinde, toplamaların filmi filmin geldiği yıla göre gruplamak için açılan listeden **yıl** ' ı seçin.
    
    ![Toplama](media/tutorial-data-flow/agg2.png)
1. **Toplamlar** sekmesine gidin. Sol metin kutusunda, toplam sütununu **Averagecomedyıderecelendirme**olarak adlandırın. Deyim Oluşturucu aracılığıyla toplama ifadesini girmek için sağ ifade kutusuna tıklayın.
    
    ![Toplama](media/tutorial-data-flow/agg3.png)
1. Sütun **derecelendirmesinin**ortalamasını almak için ```avg()``` toplama işlevini kullanın. **Derecelendirme** bir dize olduğundan ve ```avg()``` sayısal bir girişte yer alıyorsa, ```toInteger()``` işlevi yoluyla değeri bir sayıya dönüştürmemiz gerekir. Bu ifade şöyle görünür:

    ```avg(toInteger(Rating))```
    
    İşiniz bittiğinde **Kaydet ve son** ' a tıklayın. 

    ![Toplama](media/tutorial-data-flow/agg4.png)
1. Dönüştürme çıkışını görüntülemek için **veri önizleme** sekmesine gidin. Yalnızca iki sütun varsa, **yıl** ve **Averageıda ortalama derecelendirmesine**dikkat edin.
    
    ![Toplama](media/tutorial-data-flow/agg3.png)
1. Ardından, **hedef**altına bir **Havuz** dönüştürmesi eklemek istersiniz.
    
    ![Havuz](media/tutorial-data-flow/sink1.png)
1. Havuz **havuzunuzu**adlandırın. Havuz veri kümenizi oluşturmak için **Yeni** ' ye tıklayın.
    
    ![Havuz](media/tutorial-data-flow/sink2.png)
1. **Azure Data Lake Storage 2.** seçin. Devam ' a tıklayın.

    ![Veri kümesi](media/tutorial-data-flow/dataset1.png)
1. **Delimitedtext**öğesini seçin. Devam ' a tıklayın.

    ![Veri kümesi](media/tutorial-data-flow/dataset2.png)
1. Havuz veri kümenizi **MoviesSink**olarak adlandırın. Bağlı hizmet için 6. adımda oluşturduğunuz ADLS Gen2 bağlı hizmetini seçin. Verilerinizi yazmak için bir çıkış klasörü girin. Bu öğreticide, ' Sample-Data ' kapsayıcısının ' output ' klasörüne yazıyoruz. Klasörün önceden var olması gerekmez ve dinamik olarak oluşturulabilir. **İlk satırı üst bilgi olarak** ayarlayın ve **içeri aktarma şeması**için **hiçbiri** ' ni seçin. Son'a tıklayın.
    
    ![Havuz](media/tutorial-data-flow/sink3.png)

Artık veri akışınızı oluşturmayı tamamladınız. İşlem hattınızda çalıştırmak için hazırsınız.

## <a name="running-and-monitoring-the-data-flow"></a>Veri akışını çalıştırma ve izleme

Yayımlamadan önce bir işlem hattında hata ayıklaması yapabilirsiniz. Bu adımda, veri akışı işlem hattının hata ayıklama çalıştırmasını tetikleyeceksiniz. Veri önizleme verileri yazmazsa, bir hata ayıklama çalıştırması havuz Hedefinizdeki verileri yazar.

1. İşlem hattı tuvaline gidin. Hata ayıklama çalıştırmasını tetiklemek için **Hata Ayıkla** ' ya tıklayın.
    
    ![İşlem hattı](media/tutorial-data-flow/pipeline1.png)
1. Veri akışı etkinliklerinin ardışık düzen hata ayıklaması, etkin hata ayıklama kümesini kullanır, ancak yine de başlatmak için en az bir dakika sürer. İlerlemeyi **Çıkış** sekmesi aracılığıyla izleyebilirsiniz. Çalıştırma başarılı olduktan sonra, izleme bölmesini açmak için göz gözlük simgesine tıklayın.
    
    ![İşlem hattı](media/tutorial-data-flow/pipeline2.png)
1. İzleme bölmesinde her bir dönüştürme adımında harcanan satır sayısını ve süreyi görebilirsiniz.
    
    ![İzleme](media/tutorial-data-flow/pipeline3.png)
1. Verilerin sütunları ve bölümlenmesi hakkında ayrıntılı bilgi almak için bir dönüşüme tıklayın.
    
    ![İzleme](media/tutorial-data-flow/pipeline4.png)

Bu öğreticiyi izlediyseniz, havuz klasörünüze 83 satır ve 2 sütun yazmış olmanız gerekir. BLOB depolama alanınızı denetleyerek verilerin doğru olduğunu doğrulayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticideki işlem hattı, 1910 ' den 2000 ' e kadar olan ve verileri ADLS 'ye yazan ortalama Comedies derecelendirmesini toplayan bir veri akışı çalıştırır. Şunları öğrendiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Veri akışı etkinliğine sahip bir işlem hattı oluşturun.
> * Dört dönüşümle bir eşleme veri akışı oluşturun. 
> * İşlem hattında test çalıştırması yapma.
> * Veri akışı etkinliğini izleme

[Veri akışı ifade dili](data-flow-expression-functions.md)hakkında daha fazla bilgi edinin.