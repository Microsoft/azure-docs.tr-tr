---
title: 'Öğretici: Isteğe bağlı tahmin & oto'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning otomatik makine öğrenimi (otomatik ML) arabirimini kullanarak bir istek tahmin modelini kod yazmadan eğitme ve dağıtma.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: a5f7c0cf95d62df2d06c91abd99a1827524d5d6b
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210559"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Öğretici: otomatik makine öğrenimi ile talep tahmini

Azure Machine Learning Studio 'da otomatik makine öğrenimi kullanarak tek bir kod satırı yazmadan [zaman serisi tahmin modeli](concept-automated-ml.md#time-series-forecasting) oluşturmayı öğrenin. Bu model, bir bisiklet paylaşma hizmeti için Kiralama taleplerini tahmin edecektir.  

Bu öğreticide herhangi bir kod yazmayacaksınız, eğitim gerçekleştirmek için Studio arabirimini kullanacaksınız.  Aşağıdaki görevleri nasıl gerçekleştireceğinizi öğreneceksiniz:

> [!div class="checklist"]
> * Bir veri kümesi oluşturun ve yükleyin.
> * Otomatik bir ML denemesi yapılandırın ve çalıştırın.
> * Tahmin ayarlarını belirtin.
> * Deneme sonuçlarını keşfet.
> * En iyi modeli dağıtın.

Ayrıca, bu diğer model türleri için otomatik makine öğrenimini deneyin:

* Sınıflandırma modelinin kod içermeyen bir örneği için bkz. [öğretici: Azure Machine Learning OTOMATIK ml ile sınıflandırma modeli oluşturma](tutorial-first-experiment-automated-ml.md).
* Regresyon modelinin kod örneği için bkz. [öğreticiye bakın: taksi Fares 'yi tahmin etmek için otomatik makine öğrenimi kullanma](tutorial-auto-train-models.md).

## <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md). 

* [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) veri dosyasını indirin

## <a name="sign-in-to-the-studio"></a>Studio 'da oturum açın

Bu öğreticide, tüm beceri seviyeleri için veri bilimi senaryoları gerçekleştirmek üzere Machine Learning araçları 'nı içeren birleştirilmiş bir Web arabirimi olan Azure Machine Learning Studio 'da otomatik ML denemelerinizi oluşturacaksınız. Studio, Internet Explorer tarayıcılarında desteklenmez.

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın.

1. Aboneliğinizi ve oluşturduğunuz çalışma alanını seçin.

1. **Kullanmaya** başlayın ' ı seçin.

1. Sol bölmede **Yazar** bölümü altında **Otomatik ml** ' yi seçin.

1. **+ Yeni OTOMATIK ml Çalıştır**' ı seçin. 

## <a name="create-and-load-dataset"></a>Veri kümesi oluşturma ve yükleme

Denemenizi yapılandırmadan önce, veri dosyanızı Azure Machine Learning veri kümesi biçiminde çalışma alanınıza yükleyin. Bunun yapılması, verilerinizin denemenize uygun şekilde biçimlendirildiğinden emin olmanızı sağlar.

1. **Veri kümesi Seç** formunda, **+ veri kümesi oluştur** açılır listesinden **yerel dosyalar** ' ı seçin. 

    1. **Temel bilgi** formunda, veri kümenize bir ad verin ve isteğe bağlı bir açıklama sağlayın. Azure Machine Learning Studio 'daki otomatik ML Şu anda yalnızca tablo veri kümelerini desteklediğinden veri kümesi türü varsayılan olarak **tablosal** olmalıdır.
    
    1. Sol alt kısımdaki **İleri ' yi** seçin

    1. **Veri deposu ve dosya seçimi** formunda, çalışma alanı oluşturma, çalışma alanı **BlobStore (Azure Blob depolama)** sırasında otomatik olarak ayarlanan varsayılan veri deposunu seçin. Bu, veri dosyanızı karşıya yükleyeceğiniz depolama konumudur. 

    1. **Gözat**'ı seçin. 
    
    1. Yerel bilgisayarınızda **bike-no.csv** dosyasını seçin. Bu, bir [Önkoşul](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)olarak indirdiğiniz dosyadır.

    1. **İleri**’yi seçin

       Karşıya yükleme tamamlandığında, ayarlar ve önizleme formu dosya türüne göre önceden doldurulur. 
       
    1. **Ayarlar ve önizleme** formunun aşağıdaki gibi doldurulduğunu doğrulayın ve **İleri ' yi** seçin.
        
        Alan|Açıklama| Öğretici için değer
        ---|---|---
        Dosya biçimi|Bir dosyada depolanan verilerin yerleşimini ve türünü tanımlar.| Ted
        Sınırlayıcı|&nbsp;Düz metin veya diğer veri akışlarında ayrı, bağımsız bölgeler arasındaki sınırı belirtmek için bir veya daha fazla karakter. |Virgül
        Encoding|Veri kümenizi okumak için kullanılacak bit karakter şeması tablosunu belirler.| UTF-8
        Sütun başlıkları| Veri kümesinin üst bilgilerinin (varsa) nasıl değerlendirileceğini gösterir.| İlk dosyadaki üst bilgileri kullan
        Satırları atla | Veri kümesinde kaç tane, ne varsa satırların atlandığını gösterir.| Yok

    1. **Şema** formu, bu deneme için verilerinizin daha fazla yapılandırılmasını sağlar. 
    
        1. Bu örnekte, **sıradan** ve **kayıtlı** sütunları yok saymayı seçin. Bu sütunlar,  **sayisi** sütununun bir dökümününüdir, bu nedenle bunları dahil ettik.

        1. Ayrıca, bu örnek için, **Özellikler** ve **tür** için varsayılan değerleri bırakın. 
        
        1. **İleri**’yi seçin.

    1. **Ayrıntıları Onayla** formunda, bilgilerin daha önce **temel bilgi** ve **Ayarlar ve önizleme** formlarında doldurulduğu ile eşleştiğini doğrulayın.

    1. Veri kümenizin oluşturulmasını gerçekleştirmek için **Oluştur** ' u seçin.

    1. Listede göründükten sonra veri kümenizi seçin.

    1. **İleri ' yi** seçin.

## <a name="configure-run"></a>Çalıştırmayı Yapılandır

Verilerinizi yükleyip yapılandırdıktan sonra, uzaktan işlem hedefini ayarlayın ve verilerinizde tahmin etmek istediğiniz sütunu seçin.

1. **Yapılandırma çalıştırması** formunu aşağıdaki gibi doldurun:
    1. Deneme adı girin: `automl-bikeshare`

    1. Hedef sütun olarak **sayisi** , ne tahmin etmek istediğinizi seçin. Bu sütun, toplam Bisiklet paylaşma sayısını gösterir.

    1. **Yeni Işlem oluştur** ' u seçin ve işlem hedefini yapılandırın. Otomatikleştirilmiş ML yalnızca Azure Machine Learning işlem destekler. 

        1. İşlem ortamınızı ayarlamak için **sanal makine** formunu doldurun.

            Alan | Açıklama | Öğretici için değer
            ----|---|---
            Sanal &nbsp; makine &nbsp; önceliği |Denemenizin hangi önceliğe sahip olması gerektiğini seçin| Ayrılmış
            Sanal &nbsp; makine &nbsp; türü| İşlem için sanal makine türünü seçin.|CPU (Merkezi Işlem birimi)
            Sanal &nbsp; makine &nbsp; boyutu| İşlem için sanal makine boyutunu seçin. Önerilen boyutların listesi verilerinize ve deneme türüne göre belirlenir. |Standard_DS12_V2
        
        1. **Yapılandırma ayarları formunu** doldurmak için **İleri ' yi** seçin.
        
             Alan | Açıklama | Öğretici için değer
            ----|---|---
            İşlem adı |  İşlem bağlamını tanımlayan benzersiz bir ad. | Bisiklet-işlem
            En az/en fazla düğüm| Veri profili için, 1 veya daha fazla düğüm belirtmeniz gerekir.|En az düğümler: 1<br>En fazla düğüm: 6
            Ölçeği ölçeklendirmeye başlamadan önce boşta geçen Saniyeler | Küme otomatik olarak en düşük düğüm sayısına ölçeklendirildiğinde boşta geçen süre.|120 (varsayılan)
            Gelişmiş ayarlar | Denemeniz için bir sanal ağ yapılandırma ve yetkilendirme ayarları.| Yok 
  
        1. İşlem hedefini almak için **Oluştur** ' u seçin. 

            **Bu, tamamlanacak birkaç dakika sürer.** 

        1. Oluşturulduktan sonra, açılan listeden yeni işlem hedefini seçin.

    1. **İleri**’yi seçin.

## <a name="select-forecast-settings"></a>Tahmin ayarlarını seçin

Machine Learning görev türünü ve yapılandırma ayarlarını belirterek otomatik ML denemenizin kurulumunu doldurun.

1. **Görev türü ve ayarlar** formunda, makine öğrenimi görev türü olarak **zaman serisi tahmini** ' ni seçin.

1. **Zaman sütununuz** olarak **Tarih** ' i seçin ve **zaman serisi tanımlayıcılarını** boş bırakın. 

1. **Tahmin ufku** , gelecekte tahmin etmek istediğiniz sürenin uzunluğudur.  Otomatik Algıla seçimini kaldırın ve alana 14 yazın. 

1. **Ek yapılandırma ayarlarını görüntüle** ' yi seçin ve alanları aşağıdaki gibi doldurun. Bu ayarlar, eğitim işini daha iyi denet, tahmininize yönelik ayarları belirtmenize yöneliktir. Aksi takdirde, denemeler seçimine ve verilerine göre varsayılan ayarlar uygulanır.

    Ek &nbsp; yapılandırma|Description|&nbsp;Öğretici için &nbsp; değer
    ------|---------|---
    Birincil ölçüm| Makine öğrenimi algoritmasının ölçülecek değerlendirme ölçümü.|Normalleştirilmiş kök ortalama kare hatası
    En iyi modeli açıkla| Otomatik ML tarafından oluşturulan en iyi modelde explainability 'yi otomatik olarak gösterir.| Etkinleştir
    Engellenen algoritmalar | Eğitim işinden dışlamak istediğiniz algoritmalar| Aşırı rastgele ağaçlar
    Ek tahmin ayarları| Bu ayarlar, modelinizin doğruluğunu artırmaya yardımcı olur. <br><br> _**Tahmin hedefi lags:**_ hedef değişkenin lags 'ı ne kadar geri oluşturmak istiyorsunuz <br> _**Hedef sıralı pencere**_: *maksimum, en düşük* ve *Toplam* gibi özelliklerin oluşturulacağı pencere sayısını belirtir. | <br><br>Tahmin &nbsp; hedefi &nbsp; lags: None <br> Hedef &nbsp; hareketli &nbsp; pencere &nbsp; boyutu: yok
    Çıkış ölçütü| Bir kriterle karşılanırsa eğitim işi durdurulur. |Eğitim &nbsp; işi &nbsp; süresi (saat): 3 <br> Ölçüm &nbsp; puan &nbsp; eşiği: yok
    Doğrulama | Çapraz doğrulama türü ve test sayısı seçin.|Doğrulama türü:<br>&nbsp;&nbsp;çapraz doğrulamayı yana kesme <br> <br> Doğrulama sayısı: 5
    Eşzamanlılık| Yineleme başına yürütülen en fazla paralel yineleme sayısı| En fazla &nbsp; eşzamanlı &nbsp; yineleme: 6
    
    **Kaydet**’i seçin.

## <a name="run-experiment"></a>Deneme Çalıştır

Denemenizi çalıştırmak için **son**' u seçin. Çalıştırma **ayrıntıları**  ekranı, çalışma numarasının yanında bulunan **çalıştırma durumuyla** birlikte açılır. Deneme ilerledikçe bu durum güncellenir. Deneme larınızın durumunu bilgilendirmek için Ayrıca, Studio 'nun sağ üst köşesinde bildirimler görüntülenir.

>[!IMPORTANT]
> Hazırlık, deneme çalıştırmasının hazırlanmasına **10-15 dakika** sürer.
> Çalışmaya başladıktan sonra, **her yinelemede 2-3 dakika daha** sürer.<br> <br>
> Üretimde, bu işlem zaman alacağından biraz daha fazla ilerleyelim. Beklerken, tüm **modeller** sekmesinde, sınanan algoritmaları keşfetmeye başlayacağız. 

##  <a name="explore-models"></a>Modelleri keşfet

Test edilen algoritmaları (modeller) görmek için **modeller** sekmesine gidin. Modeller, varsayılan olarak, tamamlandığı gibi ölçüm puanına göre sıralanır. Bu öğreticide, seçilen **normalleştirilmiş kök anlamına** gelen en yüksek değeri gösteren model, listenin en üstünde yer alır.

Deneme modellerinin tümünün bitmesini beklerken, performans ayrıntılarını araştırmak için tamamlanmış bir modelin **algoritma adını** seçin. 

Aşağıdaki örnek, seçili modelin özelliklerini, ölçümlerini ve performans grafiklerini görüntülemek için **Ayrıntılar** ve **ölçümler** sekmelerinde gezinir. 

![Ayrıntıları Çalıştır](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>Modeli dağıtma

Azure Machine Learning Studio 'da otomatik makine öğrenimi, birkaç adımda Web hizmeti olarak en iyi modeli dağıtmanıza olanak tanır. Dağıtım, yeni verileri tahmin etmek ve potansiyel fırsat bölgelerini belirlemek için modelin Tümleştirmesidir. 

Bu deneme için, bir Web hizmetine dağıtım, Bisiklet payı şirketinin tahmin Bisiklet payını sağlamak için artık yinelemeli ve ölçeklenebilir bir Web çözümüne sahip olduğu anlamına gelir. 

Çalıştırma tamamlandıktan sonra, ekranınızın en üstünde **1. Çalıştır** ' ı seçerek üst çalışma sayfasına geri gidin.

**En iyi model Özeti** bölümünde, **stackensediskte** **normalleştirilmiş kök ortalama kare hata** ölçüsüne bağlı olarak bu deneme bağlamında en iyi model kabul edilir.  

Bu modeli dağıyoruz ancak yapmanız önerilir, dağıtımın tamamlaması yaklaşık 20 dakika sürer. Dağıtım işlemi, modeli kaydetme, kaynakları oluşturma ve bunları Web hizmeti için yapılandırma dahil olmak üzere birkaç adım gerektirir.

1. Modele özgü sayfayı açmak için **Stackensebir** öğesini seçin.

1. Ekranın sol üst bölümünde bulunan **Dağıt** düğmesini seçin.

1. **Model dağıt** bölmesini aşağıdaki gibi doldurun:

    Alan| Değer
    ----|----
    Dağıtım adı| bıkespaylaş-dağıt
    Dağıtım açıklaması| Bisiklet payı talep dağıtımı
    İşlem türü | Azure Işlem örneği (acı) seçin
    Kimlik doğrulamayı etkinleştir| Dıı. 
    Özel dağıtım varlıklarını kullanma| Dıı. Devre dışı bırakmak varsayılan sürücü dosyası (Puanlama betiği) ve ortam dosyasının yeniden oluşturulmasına izin verir. 
    
    Bu örnekte, *Gelişmiş* menüsünde belirtilen Varsayılanları kullanırız. 

1. **Dağıt**'ı seçin.  

    **Çalıştırma** ekranının üst kısmında, dağıtımın başarıyla başlatıldığını belirten yeşil başarı iletisi görüntülenir. Dağıtımın ilerlemesi, dağıtım **durumu**' nun altındaki **model Özeti** bölmesinde bulunabilir.
    
Dağıtım başarılı olduktan sonra, tahminleri oluşturmak için işlemsel bir Web hizmetiniz vardır. 

Yeni Web hizmetinizi kullanma hakkında daha fazla bilgi edinmek için [**sonraki adımlara**](#next-steps) ilerleyin ve Power BI yerleşik Azure Machine Learning desteğini kullanarak tahminlerinizi test edin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Dağıtım dosyaları veri ve deneme dosyalarından daha büyüktür, bu nedenle daha fazla depolama alanı maliyetlidir. Hesap maliyetlerini en aza indirmek için yalnızca dağıtım dosyalarını silin veya çalışma alanınızı ve deneme dosyalarını korumak istiyorsanız. Aksi takdirde, herhangi bir dosyayı kullanmayı planlamıyorsanız tüm kaynak grubunu silin.  

### <a name="delete-the-deployment-instance"></a>Dağıtım örneğini silme

Diğer öğreticiler ve araştırmayla ilgili kaynak grubunu ve çalışma alanını tutmak istiyorsanız, yalnızca Azure Machine Learning Studio 'dan dağıtım örneğini silin. 

1. [Azure Machine Learning Studio](https://ml.azure.com/)'ya gidin. Çalışma alanınıza gidin ve **varlıklar** bölmesinin sol tarafında **uç noktalar**' ı seçin. 

1. Silmek istediğiniz dağıtımı seçin ve **Sil**' i seçin. 

1. **Devam**' ı seçin.

### <a name="delete-the-resource-group"></a>Kaynak grubunu silme

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Bisiklet payını tahmin eden bir zaman serisi tahmin modeli oluşturup dağıtmak için Azure Machine Learning Studio 'da otomatik ML kullandınız. 

Yeni dağıtılan Web hizmetinizin tüketimini kolaylaştırmak için Power BI desteklenen bir şema oluşturma adımları için bu makaleye bakın.

> [!div class="nextstepaction"]
> [Bir web hizmetini kullanma](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ [Otomatik makine öğrenimi](concept-automated-ml.md)hakkında daha fazla bilgi edinin.
+ Sınıflandırma ölçümleri ve grafikler hakkında daha fazla bilgi için bkz. [otomatik makine öğrenimi sonuçlarını anlama](how-to-understand-automated-ml.md) makalesi.
+ [Korleştirme](how-to-configure-auto-features.md#featurization)hakkında daha fazla bilgi edinin.
+ [Veri profili oluşturma](how-to-connect-data-ui.md#profile)hakkında daha fazla bilgi edinin.

>[!NOTE]
> Bu bisiklet payı veri kümesi, bu öğretici için değiştirilmiştir. Bu veri kümesi, bir [kale yarışmanın](https://www.kaggle.com/c/bike-sharing-demand/data) bir parçası olarak sunulmuştur ve başlangıçta [büyük bikespaylaşımı](https://www.capitalbikeshare.com/system-data)aracılığıyla sunuldu. Ayrıca, [uMachine Learning veritabanı](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)'nda da bulunabilir.<br><br>
> Kaynak: Fanaee-T, hadi ve gama, Joao, olay etiketleme birleştirme algılayıcıları ve arka plan bilgilerini birleştiren yapay zeka (2013): PP. 1-15, Sprümlberg.