---
title: Machine Learning Studio&quot;da basit bir deneme | Microsoft Belgeleri
description: "Bu makine öğrenimi öğreticisi kolay bir veri bilimi deneyinde size kılavuzluk etmektedir. Regresyon algoritması kullanarak bir arabanın fiyatını tahmin edeceğiz."
keywords: "deneme,doğrusal regresyon,makine öğrenimi algoritmaları,makine öğrenimi öğreticisi,tahmine dayalı modelleme teknikleri,veri bilimi deneyi"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 0539e9d04c61d35de56a29d350c07654c095c576
ms.lasthandoff: 03/22/2017


---

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Makine öğrenimi öğreticisi: Azure Machine Learning Studio'da ilk veri bilimi denemenizi oluşturma

Daha önce **Azure Machine Learning Studio** kullanmadıysanız, bu öğretici sizin için hazırlanmıştır.

Bu öğreticide, Studio’yu bir makine öğrenimi denemesi oluşturmak için ilk kullanma adımları gösterilmektedir. Deney, marka ve teknik belirtimler gibi farklı değişkenleri esas alarak otomobil fiyatını tahmin eden analitik bir modeli test eder.

> [!NOTE]
> Bu öğreticide denemenize modülleri sürükleyip bırakma, birbirine bağlama, denemeyi çalıştırma ve sonuçları görme konularında temel bilgiler verilmektedir. Genel olarak makine öğrenimi konusu veya Studio’ya dahil edilen 100’den fazla yerleşik algoritmanın nasıl seçileceği veya kullanılacağı konuları ele alınmamaktadır.
>
>Makine öğrenimine yeni başladıysanız, [Yeni Başlayanlar için Veri Bilimi](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) video serisi iyi bir başlangıç noktası olabilir. Günlük dilin ve genel kavramların kullanıldığı videolardan oluşan bu seri, makine öğrenimine harika bir giriş yapmanızı sağlar.
>
>Makine öğrenimi hakkında bilgi sahibiyseniz ancak Machine Learning Studio ve içerdiği makine öğrenimi algoritmaları hakkında daha genel bilgiler arıyorsanız, aşağıda faydalı olabilecek bazı kaynaklar verilmiştir:
>
- [Machine Learning Studio nedir?](machine-learning-what-is-ml-studio.md) - Bu Studio’ya yönelik ileri seviye bir genel bakıştır.
- [Algoritma örnekleri ile makine öğrenimi temelleri](machine-learning-basics-infographic-with-algorithm-examples.md) - Bu bilgi grafiği Machine Learning Studio ile birlikte gelen farklı makine öğrenimi algoritması türleri hakkında daha fazla bilgi almak istiyorsanız kullanışlıdır.
- [Machine Learning Kılavuzu](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) - Bu kılavuz yukarıdaki bilgi grafiği ile benze bilgileri kapsar ancak etkileşimli bir biçimdedir.
- [Makine öğrenimi algoritması bilgi sayfası](machine-learning-algorithm-cheat-sheet.md) ve [Microsoft Azure Machine Learning için algoritma seçme](machine-learning-algorithm-choice.md) - Bu indirilebilir poster ve tamamlayıcı makale Studio algoritmaları hakkında ayrıntılı bilgiler içerir.
- [Machine Learning Studio: Algoritma ve Modül Yardımı](https://msdn.microsoft.com/library/azure/dn905974.aspx) - Bu makine öğrenimi de dahil tüm Studio modülleri için tam başvurudur.

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Machine Learning Studio yardımı nasıl çalışır?

Machine Learning Studio tahmine dayalı modelleme teknikleriyle önceden programlanmış sürükle ve bırak modüllerini kullanarak bir deneme oluşturmayı kolaylaştırır.

Etkileşimli, görsel bir çalışma alanı kullanarak ***veri kümeleri*** ve analiz ***modüllerini*** etkileşimli bir tuvale sürükleyip bırakın. Machine Learning Studio’da çalıştırılabilen bir ***deneme*** oluşturmak için bunları birbirine bağlayın.
***Model oluşturun***, ***modeli eğitin*** ve ***modeli puanlayın ve test edin***.

İstediğiniz sonuçları alana kadar model tasarımını yineleyebilir, denemeyi düzenleyebilir ve çalıştırabilirsiniz. Modeliniz hazır olduğunda, başkalarının yeni veriler göndererek tahminler alabilmesi için bir ***web hizmeti*** olarak yayımlayabilirsiniz.

## <a name="open-machine-learning-studio"></a>Machine Learning Studio’yu açma

Studio kullanmaya başlamak için [https://studio.azureml.net](https://studio.azureml.net) adresine gidin. Machine Learning Studio’da daha önce oturum açtıysanız **Oturum Aç**’a tıklayın. Veya **Buradan kaydolun**’a tıklayıp ücretsiz ve ücretli seçenekler arasından seçim yapın.

![Machine Learning Studio’da oturum açın][sign-in-to-studio]
<br/>
***Machine Learning Studio’da oturum açın***

## <a name="five-steps-to-create-an-experiment"></a>Bir deneme oluşturmanın beş adımı

Bu makine öğrenimi öğreticisinde, modelinizi oluşturmak, eğitmek ve puanlamak amacıyla Machine Learning Studio'da bir deneme oluşturmak için beş temel adımı izleyeceksiniz.

- **Bir model oluşturma**
    - [1. Adım: Verileri alma]
    - [2. Adım: Verileri hazırlama]
    - [3. Adım: Özellikleri tanımlama]
- **Modeli eğitme**
    - [4. Adım: Bir öğrenme algoritması seçme ve uygulama]
- **Modeli puanlama ve test etme**
    - [5. Adım: Yeni otomobil fiyatlarını tahmin etme]

[1. Adım: Verileri alma]: #step-1-get-data
[2. Adım: Verileri hazırlama]: #step-2-prepare-the-data
[3. Adım: Özellikleri tanımlama]: #step-3-define-features
[4. Adım: Bir öğrenme algoritması seçme ve uygulama]: #step-4-choose-and-apply-a-learning-algorithm
[5. Adım: Yeni otomobil fiyatlarını tahmin etme]: #step-5-predict-new-automobile-prices

> [!TIP] 
> Aşağıdaki denemenin çalışan bir kopyasını [Cortana Intelligence Galerisi](https://gallery.cortanaintelligence.com)’nde bulabilirsiniz. **[İlk veri bilimi denemeniz - Otomobil fiyat tahmini](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)**’ne gidin ve denemenin bir kopyasını Machine Learning Studio çalışma alanınıza indirmek için **Studio’da Aç**’a tıklayın.


## <a name="step-1-get-data"></a>1. Adım: Verileri alma

Makine öğrenimi için ihtiyacınız olan ilk şey verilerdir.
Machine Learning Studio'da kullanabileceğiniz birçok örnek veri kümesi bulunur ve birçok kaynaktan verileri içeri aktarabilirsiniz. Bu örnekte, **Otomobil fiyat verileri (Ham)** adlı çalışma alanınıza dahil edilmiş örnek veri kümesini kullanacağız.
Bu veri kümesi; marka, model, teknik belirtimler ve fiyat gibi bilgiler dahil olmak üzere birçok ayrı otomobil için giriş içerir.

Veri kümesini denemenize aşağıdaki gibi aktarabilirsiniz.

1. Machine Learning Studio penceresinin alt kısmındaki **+NEW (+YENİ)** seçeneğine tıklayarak yeni bir deneme oluşturun, **EXPERIMENT (DENEME)** seçeneğini belirletin ve ardından **Blank Experiment (Boş Deneme)** öğesini seçin.

2. Denemenize tuvalin üzerinde görebileceğiniz bir varsayılan ad verilir. Bu adı seçerek anlamlı bir adla değiştirin, örneğin, **Otomobil fiyat tahmini**. Adın benzersiz olması gerekmez.

    ![Denemeyi yeniden adlandırma][rename-experiment]

2. Deneme tuvalinin sol tarafında bir veri kümesi ve modül paleti bulunur. **Otomobil fiyat verileri (Ham)** etiketli veri kümesini bulmak için bu paletin en üst kısmındaki Arama kutusuna **otomobil** yazın. Bu veri kümesini deneme tuvaline sürükleyin.

    ![Otomobil veri kümesini bulun ve deneme tuvaline sürükleyin][type-automobile]
    <br/>
    ***Otomobil veri kümesini bulun ve deneme tuvaline sürükleyin***

Bu verinin nasıl göründüğünü görmek için, otomobil veri kümesinin alt kısmındaki çıkış bağlantı noktasına tıklayın ve ardından **Görselleştir**'i seçin.

![Çıkış bağlantı noktasına tıklayın ve “Görselleştir”i seçin][select-visualize]
<br/>
***Çıkış bağlantı noktasına tıklayın ve “Görselleştir”i seçin***

> [!TIP]
> Veri kümeleri ve modülleri küçük dairelerle gösterilen giriş ve çıkış bağlantı noktalarına sahiptir. Giriş bağlantı noktaları yukarıda, çıkış bağlantı noktaları aşağıdadır.
Denemeniz üzerinden veri akışı oluşturmak için bir modülün çıkış bağlantı noktasını bir diğerinin giriş bağlantı noktasına bağlayacaksınız.
Herhangi bir zamanda bir veri kümesi veya modülün çıkış bağlantı noktasına tıklayarak, veri akışında bu noktadaki verileri görebilirsiniz.

Örnek veri kümesinde, bir otomobilin her örneği bir satır olarak görünür ve her otomobille ilişkili değişkenler sütun olarak görünür. Belirli bir otomobil için değişkenleri kullanarak en sağdaki sütundaki (“fiyat” başlıklı sütun 26) fiyatı tahmin etmeye çalışacağız.

![Veri görselleştirme penceresinde otomobil verilerini görüntüleme][visualize-auto-data]
<br/>
***Veri görselleştirme penceresinde otomobil verilerini görüntüleme***

Sağ üst köşedeki "**x**" işaretine tıklayarak görselleştirme penceresini kapatın.

## <a name="step-2-prepare-the-data"></a>2. Adım: Verileri hazırlama

Genellikle bir veri kümesi analiz edilmeden önce biraz ön işleme gerekir. Örneğin, çeşitli satırların sütunlarında bulunan eksik değerleri fark etmiş olabilirsiniz. Modelin verileri doğru şekilde analiz edebilmesi için bu eksik değerlerin temizlenmesi gerekir. Örneğimizde eksik değerleri olan satırları kaldıracağız. Ayrıca, **normalleştirilmiş kayıplar** sütununun büyük kısmı eksik değerlere sahiptir; bu nedenle bu sütunu modelin tamamen dışında bırakacağız.

> [!TIP]
> Giriş verilerinden eksik değerleri temizleme, çoğu modülü kullanmanın bir önkoşuludur.

Önce **normalized-losses** sütununu tamamen kaldıran bir modül ekleyecek ve ardından eksik veriler içeren satırları kaldıran diğer bir modülü ekleyeceğiz.

1. [Veri Kümesindeki Sütunları Seçme][select-columns] modülünü bulmak için modül paletinin en üst kısmındaki Arama kutusuna **sütun seçme** yazın, ardından bunu deneme tuvaline sürükleyin. Bu modül, modele hangi veri sütunlarını dahil etmek veya dışarıda bırakmak istediğimizi seçmemizi sağlar.

2. **Otomobil fiyat verileri (Ham)** veri kümesinin çıkış bağlantı noktasını [Veri Kümesindeki Sütunları Seçme][select-columns] modülündeki giriş bağlantı noktasına bağlayın.

    !["Veri Kümesindeki Sütunları Seçme" modülünü deneme tuvaline ekleyin ve bağlayın][type-select-columns]
    <br/>
    ***"Veri Kümesindeki Sütunları Seçme" modülünü deneme tuvaline ekleyin ve bağlayın***

3. [Select Columns in Dataset (Veri Kümesinde Sütun Seçme)][select-columns] modülüne tıklayın ve **Özellikler** bölmesinde **Sütun seçiciyi başlat** seçeneğine tıklayın.

    - Sol tarafta **Kurallar ile**’ye tıklayın
    - **Şununla Başla** altında **Tüm sütunlar**’a tıklayın. Bu, [Veri Kümesindeki Sütunları Seçme][select-columns] modülünü tüm sütunlardan geçmeye yönlendirir (dışarıda bırakacağımız sütunlar hariç).
    - Açılan menülerden **Hariç Tut** ve **sütun adlarını** seçerek metin kutusuna tıklayın. Sütun listesi görüntülenir. **Normalleştirilmiş kayıplar**’ı seçin; böylece metin kutusuna eklenir.
    - Sütun seçiciyi kapatmak için onay işareti (Tamam) düğmesine tıklayın (sağ alt köşede).

    ![Sütun seçiciyi başlatın ve "normalized-losses" sütununu hariç tutun][launch-column-selector]
    <br/>
    ***Sütun seçiciyi başlatın ve "normalized-losses" sütununu hariç tutun***

    Artık **Select Columns in Dataset (Veri Kümesinde Sütun Seçme)** için özellikler bölmesi, **normalleştirilmiş kayıplar** dışındaki tüm veri kümelerindeki tüm sütunlardan geçeceğini belirtir.

    ![Özellikler bölmesinde "normalized-losses" sütununun hariç tutulduğu gösterilir][showing-excluded-column]
    <br/>
    ***Özellikler bölmesinde "normalized-losses" sütununun hariç tutulduğu gösterilir***

    > [!TIP]
    Modüle çift tıklayıp metin girerek bir modüle yorum ekleyebilirsiniz. Bu, modülün denemenizde ne işe yaradığını bir bakışta görmenize yardımcı olabilir. Bu durumda, [Veri Kümesindeki Sütunları Seçme][select-columns] modülüne çift tıklayın ve "Normalleştirilmiş kayıpları dışarıda bırak" yorumunu yazın.
    >
    >


    ![Açıklama eklemek için bir modüle çift tıklayın][add-comment]
    <br/>
    ***Açıklama eklemek için bir modüle çift tıklayın***

3. [Eksik Verileri Temizleme][clean-missing-data] modülünü deneme tuvaline sürükleyin ve bunu [Veri Kümesindeki Sütunları Seçme][select-columns] modülüne bağlayın. **Özellikler** bölmesinde, **Temizleme modu** altında **Tüm satırı kaldır**’ı seçin. Bu [Eksik Verileri Temizleme][clean-missing-data] öğesini eksi değer içeren satırları kaldırarak verileri temizlemesi için yönlendirir. Modüle çift tıklayın ve "Eksik değerli satırları kaldır" yorumunu yazın.

    !["Eksik Verileri Temizleme" için temizleme modunu "Tüm satırı kaldır" olarak ayarlayın][set-remove-entire-row]
    <br/>
    ***"Eksik Verileri Temizleme" için temizleme modunu "Tüm satırı kaldır" olarak ayarlayın***

4. Sayfanın en altında yer alan **ÇALIŞTIR**'a tıklayarak denemeyi çalıştırın.

    Deneme çalıştırma bittiğinde, tüm modüllerin başarıyla tamamlandığını göstermek için yeşil bir onay işareti bulunur. Sağ üst köşede **Çalıştırma tamamlandı** durumunun olduğuna da dikkat edin.

![Çalıştırdıktan sonra, deneme aşağıdakine benzer görünmelidir][early-experiment-run]
<br/>
***Çalıştırdıktan sonra, deneme aşağıdakine benzer görünmelidir***

> [!TIP]
> Denemeyi neden şimdi çalıştırdık? Deneme çalıştırılarak, verilerimizin sütun tanımları [Veri Kümesindeki Sütunları Seçme][select-columns] modülü ve [Eksik Verileri Temizleme][clean-missing-data] modülü aracılığıyla veri kümesinden geçer. Bu, [Eksik Verileri Temizleme][clean-missing-data] öğesine bağladığımız modüllerin de aynı bilgilere sahip olacağı anlamına gelir.

Denemede bu noktaya kadar yalnızca verileri temizledik. Temizlenen veri kümesini görüntülemek istiyorsanız [Eksik Verileri Temizleme][clean-missing-data] modülünün sol çıkış bağlantı noktasına tıklayın ve **Görselleştir**'i seçin. **Normalleştirilmiş kayıplar** sütununun artık dahil olmadığına ve eksik değerlerin yok olduğuna dikkat edin.

Artık veriler temizlendiğine göre, tahmine dayalı modelde hangi özellikleri kullanacağımızı belirtmeye hazırız.

## <a name="step-3-define-features"></a>3. Adım: Özellikleri tanımlama

Machine learning'de *özellikler*, ilgilendiğiniz bir şeyin tek tek ölçülebilir özellikleridir. Veri kümemizde her bir satır bir otomobili temsil eder ve her bir sütun da bu otomobilin bir özelliğidir.

Tahmine dayalı bir model oluşturmaya yönelik iyi bir özellikler kümesi bulmak için, deneme ve çözmek istediğiniz sorun hakkında bilgi gerekir. Bazı özellikler, hedefi tahmin etmede diğerlerinden daha uygundur. Ayrıca, bazı özelliklerin diğer özelliklerle güçlü bir bağıntısı vardır ve kaldırılabilirler. Örneğin, city-mpg ve highway-mpg yakından ilişkilidir, bu nedenle tahmini önemli ölçüde etkilemeden birini tutabilir ve diğerini kaldırabiliriz.

Veri kümemizdeki bir alt özellikler kümesini kullanan bir model oluşturalım. Daha sonra geri dönüp farklı özellikler seçebilir, denemeyi tekrar çalıştırabilir ve daha iyi sonuçlar elde edip etmeyeceğinizi görebilirsiniz. Ancak başlarken aşağıdaki özellikleri deneyelim:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Deneme tuvaline başka bir [Veri Kümesindeki Sütunları Seçme][select-columns] modülü sürükleyin. [Eksik Verileri Temizleme][clean-missing-data] modülünün sol çıkış bağlantı noktasını [Veri Kümesindeki Sütunları Seçme][select-columns] modülünün girişine bağlayın.

    !["Veri Kümesindeki Sütunları Seçme" modülünü "Eksik Verileri Temizleme" modülüne bağlayın][connect-clean-to-select]
    <br/>
    ***"Veri Kümesindeki Sütunları Seçme" modülünü "Eksik Verileri Temizleme" modülüne bağlayın***

2. Modüle çift tıklayın ve "Tahmin için özellik seç" yazın.

2. **Properties (Özellikler)** bölmesindeki **Launch column selector (Sütun seçiciyi başlat)** seçeneğine tıklayın.

3. **Kurallar ile**’ye tıklayın.

4. **Şununla Başla** altında **Sütun yok**’a tıklayın. Filtre satırında, **Dahil et** ve **sütun adları** öğelerini seçin ve metin kutusunda sütun adları listemizi seçin. Bu, modülü özellikle belirttiklerimiz dışında hiçbir sütunu (özelliği) geçirmemesi için yönlendirir.

5. Onay işareti (Tamam) düğmesine tıklayın.

    ![Tahmine dahil edilecek sütunları (özellikleri) seçin][select-columns-to-include]
    <br/>
    ***Tahmine dahil edilecek sütunları (özellikleri) seçin***

Bu yalnızca bir sonraki adımda kullanacağımız öğrenim algoritmasına geçirmek istediğimiz özellikleri içeren filtrelenmiş bir veri kümesi üretir. Daha sonra geri dönüp farklı özellikler seçerek yeniden deneyebilirsiniz.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>4. Adım: Bir öğrenme algoritması seçme ve uygulama

Artık veriler hazır olduğuna göre, tahmine dayalı bir model oluşturmak için eğitim ve test etme gerekir. Modeli eğitmek ve sonra fiyatları tahmin etmeye ne kadar yaklaştığını görmek üzere modeli test etmek için verilerimizi kullanacağız.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

*Sınıflandırma* ve *regresyon*, denetimli makine öğrenimi algoritmasının iki türüdür. Sınıflandırma; renk gibi (kırmızı, mavi veya yeşil) tanımlanmış bir kategori kümesinden yanıt tahmin eder. Bir sayıyı tahmin etmek için regresyon kullanılır.

Bir sayı olan fiyatı tahmin etmek istediğimizden bir regresyon algoritması kullanırız. Bu örnek için, basit bir *çizgisel regresyon* modeli kullanacağız.

> [!TIP]
> Farklı makine öğrenimi algoritması türleri ve bunların ne zaman kullanılacağı hakkında daha fazla bilgi edinmek için, Yeni Başlayanlar için Veri Bilimi serisindeki ilk video olan [Veri biliminin yanıtladığı beş soru](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md)’yu izleyebilirsiniz. Ayrıca [Algoritma örnekleriyle makine öğrenimi temelleri](machine-learning-basics-infographic-with-algorithm-examples.md) bilgi grafiğine bakabilir veya [Makine öğrenimi algoritma bilgi sayfasına göz atabilirsiniz](machine-learning-algorithm-cheat-sheet.md).

Modele fiyatı da içeren bir veri kümesi vererek modeli eğitiriz. Model verileri tarar ve otomobilin özellikleri ile fiyatı arasındaki bağlantıları arar. Daha sonra modeli test edeceğiz. Bildiğimiz otomobiller için bir özellik kümesi vererek modelin bilinen fiyatı tahmin etmeye ne kadar yaklaştığını göreceğiz.

Verilerimizi modeli eğitmek ve verileri ayrı eğitim ve test kümelerine ayırarak modeli test etmek için kullanırız.

1. [Verileri Bölme][split] modülünü seçip deneme tuvaline sürükleyin ve bunu son [Veri Kümesindeki Sütunları Seçme][select-columns] modülüne bağlayın.

2. Seçmek için [Verileri Bölme][split] modülüne tıklayın. **İlk çıkış veri kümesinde satır kesiri**’ni bulun (tuvalin sağ tarafında **Özellikler** bölmesinde) ve 0,75 olarak ayarlayın. Bu şekilde, modeli eğitmek için verilerin yüzde 75'ini kullanıp test etmek için yüzde 25'ini ayıracağız (daha sonra farklı oranlarla deneme yapabilirsiniz).

    !["Verileri Bölme" modülünün bölüm kesirini 0,75 olarak ayarlayın][set-split-data-percentage]
    <br/>
    ***"Verileri Bölme" modülünün bölüm kesirini 0,75 olarak ayarlayın***

    > [!TIP]
    > **Rastgele doldurma** parametresini değiştirerek eğitim ve test etme için farklı rastgele örnekler oluşturabilirsiniz. Bu parametre, sözde rastgele sayı üreticisinin doldurulmasını denetler.

2. Denemeyi çalıştırın. Deneme çalıştırıldığında [Veri Kümesindeki Sütunları Seçme][select-columns] ve [Verileri Bölme][split] modüllerinin sütun tanımlarını sonra ekleyeceğimiz modüllere geçirmesine olanak sağlanır.  

3. Öğrenme algoritmasını seçmek için, tuvalin solundaki modül paletindeki **Machine Learning** kategorisini genişletin ve ardından **Modeli Başlat**'ı genişletin. Böylece makine öğrenimi algoritmalarını başlatmak için kullanılabilecek çeşitli modül kategorileri görüntülenir. Bu deneme için, **Regresyon** kategorisinin altında [Doğrusal Regresyon][linear-regression] modülünü seçin ve bunu deneme tuvaline sürükleyin.
(Modülü bulmak için palet Arama kutusuna “doğrusal regresyon” da yazabilirsiniz.)

4. [Modeli Eğitme][train-model] modülünü bulup deneme tuvaline sürükleyin. [Doğrusal Regresyon][linear-regression] modülünün çıkışını [Modeli Eğitme][train-model] modülünün sol girişine bağlayın ve [Verileri Bölme][split] modülünün eğitim verileri çıkışını (sol bağlantı noktası) [Modeli Eğitme][train-model] modülünün sağ girişine bağlayın.

    !["Modeli Eğitme" modülünü "Çizgisel Regresyon" ve "Verileri Bölme" modüllerine bağlayın][connect-train-model]
    <br/>
    ***"Modeli Eğitme" modülünü "Çizgisel Regresyon" ve "Verileri Bölme" modüllerine bağlayın***

5. [Modeli Eğitme][train-model] modülüne tıklayın, **Özellikler** bölmesinde **Sütun seçiciyi başlat** seçeneğine tıklayın ve ardından **fiyat** sütununu seçin. Bu, modelimizin tahmin edeceği değerdir.

    Sütun seçicide **fiyat** sütununu **Kullanılabilir sütunlar** listesinden **Seçili sütunlar** listesine taşıyarak seçersiniz.

    !["Modeli Eğitme" modülü için fiyat sütununu seçin][select-price-column]
    <br/>
    ***"Modeli Eğitme" modülü için fiyat sütununu seçin***

6. Denemeyi çalıştırın.

Şimdi, fiyat tahmininde bulunmak amacıyla yeni otomobil verilerini puanlamak için kullanılabilecek eğitilmiş bir regresyon modeli oluşturduk.

![Çalıştırdıktan sonra, deneme şimdi aşağıdakine benzer görünmelidir][second-experiment-run]
<br/>
***Çalıştırdıktan sonra, deneme şimdi aşağıdakine benzer görünmelidir***

## <a name="step-5-predict-new-automobile-prices"></a>5. Adım: Yeni otomobil fiyatlarını tahmin etme

Verilerimizin yüzde 75'ini kullanarak modeli eğittiğimize göre, modelimizin ne kadar iyi işlediğini görmek için verilerimizin diğer yüzde 25'ini puanlama amacıyla kullanabiliriz.

1. [Model Puanlama][score-model] modülünü bulup deneme tuvaline sürükleyin. [Modeli Eğitme][train-model] modülünün çıkışını [Model Puanlama][score-model] modülünün sol giriş bağlantı noktasına bağlayın. [Verileri Bölme][split] modülünün test verileri çıkışını (sağ bağlantı noktası) [Model Puanlama][score-model] modülünün sağ giriş bağlantı noktasına bağlayın.

    !["Model Puanlama" modülünü "Modeli Eğitme" ve "Verileri Bölme" modüllerine bağlayın][connect-score-model]
    <br/>
    ***"Model Puanlama" modülünü "Modeli Eğitme" ve "Verileri Bölme" modüllerine bağlayın***

2. Denemeyi çalıştırın ve [Model Puanlama][score-model] modülünü görüntüleyin ([Model Puanlama][score-model] modülünün çıkış bağlantı noktasına tıklayın ve **Görselleştir**’i seçin). Çıkış, fiyat için tahmin edilen değerleri ve test verileri için bilinen değerleri gösterir.  

    !["Model Puanlama" modülü çıkışı][score-model-output]
    <br/>
    ***"Model Puanlama" modülü çıkışı***

3. Son olarak, sonuç kalitesini test edeceğiz. [Model Değerlendirme][evaluate-model] modülünü seçip deneme tuvaline sürükleyin ve [Model Puanlama][score-model] modülünün çıkışını [Model Değerlendirme][evaluate-model] modülünün sol giriş bağlantı noktasına bağlayın.

    > [!TIP]
    > [Model Değerlendirme][evaluate-model] modülü iki modeli yan yana karşılaştırmak için kullanılabildiğinden, iki giriş bağlantı noktası bulunur. Daha sonra, denemeye başka bir algoritma ekleyebilir ve hangisinin daha iyi sonuç verdiğini öğrenmek için [Model Değerlendirme][evaluate-model] modülünü kullanabilirsiniz.

4. Denemeyi çalıştırın.

[Model Değerlendirme][evaluate-model] modülünden çıkışı görüntülemek için, çıkış bağlantı noktasına tıklayın ve ardından **Görselleştir**'i seçin.

![Deneme için değerlendirme sonuçları][evaluation-results]
<br/>
***Deneme için değerlendirme sonuçları***

Modelimiz için aşağıdaki istatistikler gösterilir:

- **Mean Absolute Error (Ortalama Mutlak Hata)** (MAE): Mutlak hataların ortalaması (*hata*, tahmin edilen değer ile gerçek değer arasındaki farktır).
- **Root Mean Squared Error (Kök Ortalama Karesi Alınmış Hata)** (RMSE): Test veri kümesinde yapılan tahminlerin karesi alınmış hata ortalamasının kare kökü.
- **Relative Absolute Error (Göreli Mutlak Hata)**: Gerçek değerler ve tüm gerçek değerlerin ortalaması arasındaki mutlak hataların mutlak farka göreli ortalaması.
- **Relative Squared Error (Göreli Karesi Alınmış Hata)**: Gerçek değerler ve tüm gerçek değerlerin ortalaması arasındaki karesi alınmış hataların karesi alınmış farka göreli ortalaması.
- **Coefficient of Determination (Determinasyon Katsayısı)**: **R karesi alınmış değer** olarak da bilinen ve modelin verilere ne kadar iyi uyumlu olduğunu gösteren istatistik ölçümleridir.

Her bir hata istatistiği ne kadar küçük olursa o kadar iyidir. Daha küçük olan bir değer, tahminlerin gerçek değerlerle daha yakından eşleştiğini gösterir. **Coefficient of Determination (Determinasyon Katsayısı)** değeri bire (1.0) ne kadar yakınsa tahminler o kadar iyi olur.

## <a name="final-experiment"></a>Son deneme

Son deneme şuna benzer şekilde görünecektir:

![Son deneme][complete-linear-regression-experiment]
<br/>
***Son deneme***

## <a name="next-steps"></a>Sonraki adımlar

Artık ilk makine öğrenimi öğreticinizi tamamladığınıza ve denemenizi kurduğunuza göre, modeli iyileştirmeye devam edebilir ve ardından tahmine dayalı web hizmeti olarak dağıtabilirsiniz.

- **Modeli geliştirmek için yineleme** - Örneğin, tahmininizde kullandığınız özellikleri değiştirebilirsiniz. Veya [Doğrusal Regresyon][linear-regression] algoritmasının özelliklerini değiştirebilir veya tamamen farklı bir algoritma deneyebilirsiniz. Ayrıca, denemenize tek bir seferde birden çok makine öğrenimi algoritması ekleyebilir ve [Model Değerlendirme][evaluate-model] modülünü kullanarak ikisini karşılaştırabilirsiniz.
Tek bir denemede birden çok modeli karşılaştırma örneği için [Cortana Intelligence Galerisi](https://gallery.cortanaintelligence.com)’nde [Regresörleri Karşılaştırma](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5)’ya bakın.

    > [!TIP]
    > Denemenizin herhangi bir yinelemesini kopyalamak için sayfanın en altındaki **FARKLI KAYDET** düğmesini kullanın. Sayfanın en altındaki **ÇALIŞTIRMA GEÇMİŞİNİ GÖRÜNTÜLE** seçeneğine tıklayarak denemenizin tüm yinelemelerini görebilirsiniz. Daha fazla ayrıntı için bkz. [Azure Machine Learning Studio'da deneme yinelemelerini yönetme][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

- **Modeli tahmine dayalı web hizmeti olarak dağıtma** - Modelinizi istediğiniz zaman, yeni veriler aracılığıyla otomobil fiyatlarını tahmin etmek için kullanılacak bir web hizmeti olarak dağıtabilirsiniz. Daha fazla ayrıntı için bkz. [Bir Azure Machine Learning web hizmetini dağıtma][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Daha fazla bilgi almak ister misiniz? Modeli oluşturma, eğitme, puanlama ve dağıtma sürecinin daha kapsamlı ve ayrıntılı kılavuzu için bkz. [Azure Machine Learning kullanarak tahmine dayalı bir çözüm geliştirme][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/machine-learning-create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/machine-learning-create-experiment/rename-experiment.png
[visualize-auto-data]:./media/machine-learning-create-experiment/visualize-auto-data.png
[select-visualize]: ./media/machine-learning-create-experiment/select-visualize.png
[showing-excluded-column]:./media/machine-learning-create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/machine-learning-create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/machine-learning-create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/machine-learning-create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/machine-learning-create-experiment/second-experiment-run.png
[connect-score-model]:./media/machine-learning-create-experiment/connect-score-model.png
[evaluation-results]:./media/machine-learning-create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/machine-learning-create-experiment/type-automobile.png
[type-select-columns]:./media/machine-learning-create-experiment/type-select-columns.png
[launch-column-selector]:./media/machine-learning-create-experiment/launch-column-selector.png
[add-comment]:./media/machine-learning-create-experiment/add-comment.png
[connect-clean-to-select]:./media/machine-learning-create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/machine-learning-create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/machine-learning-create-experiment/connect-train-model.png
[select-price-column]:./media/machine-learning-create-experiment/select-price-column.png

[score-model-output]:./media/machine-learning-create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

