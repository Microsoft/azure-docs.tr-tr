---
title: Model performansını değerlendirme
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) ve bu görev için kullanılabilen ölçümler hakkında model performansını değerlendirmeyi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: b37844ff93ed1cfb631c2d8da12d0729f61f44ed
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837652"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio 'de model performansını değerlendirme (klasik)

Bu makalede, Azure Machine Learning Studio (klasik) ' de bir modelin performansının nasıl değerlendirileceği gösterilmektedir ve bu görev için kullanılabilen ölçümlere ilişkin kısa bir açıklama sunulmaktadır. Yaygın olarak denetlenen üç öğrenme senaryosu sunulmaktadır: 

* regresyon
* İkili sınıflandırma 
* Birden çok Lass sınıflandırması



Bir modelin performansını değerlendirmek, veri bilimi işlemindeki temel aşamalardan biridir. Bir veri kümesinin Puanlama (tahminleri), eğitilen bir model tarafından ne kadar başarılı olduğunu gösterir. 

Azure Machine Learning Studio (klasik), ana makine öğrenimi modüllerinin ikisi aracılığıyla model değerlendirmesini destekler: [modeli değerlendirin][evaluate-model] ve [çapraz doğrulama modeli][cross-validate-model]. Bu modüller, modelinizin makine öğrenimi ve istatistiklerinde yaygın olarak kullanılan bir dizi ölçüm bakımından nasıl gerçekleştiğini görmenizi sağlar.

## <a name="evaluation-vs-cross-validation"></a>Değerlendirme ve çapraz doğrulama karşılaştırması
Değerlendirme ve çapraz doğrulama, modelinizin performansını ölçmek için standart yollardır. Bunlar her ikisi de diğer modellerle ilgili olarak incelemenize veya karşılaştırabileceğiniz değerlendirme ölçümleri üretir.

[Değerlendirme modeli][evaluate-model] , giriş olarak bir puanlanmış veri kümesi bekler (veya iki farklı modelin performansını karşılaştırmak istediğiniz durumlarda iki durumda). Bu nedenle, sonuçları değerlendirebilmeniz için önce modeli [eğitme][train-model] modülünü kullanarak modelinizi eğitmeniz ve [model Puanlama][score-model] modülünü kullanarak bazı veri kümelerinde tahmin yapmanız gerekir. Değerlendirme, puanlanmış etiketlere/olasılıklara, hepsi de [puan modeli][score-model] modülü tarafından çıkış olan doğru etiketlere göre yapılır.

Alternatif olarak, giriş verilerinin farklı alt kümelerine otomatik olarak bir dizi tren (10 kattı) işlemi gerçekleştirmek için çapraz doğrulama kullanabilirsiniz. Giriş verileri 10 parçaya bölünür, burada bir test için ayrılmıştır ve eğitim için diğer 9. Bu işlem 10 kez yinelenir ve değerlendirme ölçümlerinin ortalaması alınır. Bu, bir modelin yeni veri kümelerine ne kadar iyi genelleştireceğini belirlemeye yardımcı olur. [Çapraz doğrulama modeli][cross-validate-model] modülü, eğitilmiş bir modeli ve etiketli bir veri kümesini alır ve ortalama sonuçlara ek olarak 10 katların her birinin değerlendirme sonuçlarını verir.

Aşağıdaki bölümlerde, hem [değerlendirme modelini][evaluate-model] hem de [çapraz doğrulama model][cross-validate-model] modüllerini kullanarak basit regresyon ve sınıflandırma modelleri oluşturacağız ve performanslarını değerlendiririz.

## <a name="evaluating-a-regression-model"></a>Regresyon modelini değerlendirme
Bir otomobil 'nin boyut, Horsepower, altyapı özellikleri gibi özellikleri kullanarak bir fiyat tahmin etmek istediğimiz olduğunu varsayalım. Bu, hedef değişkenin (*Fiyat*) sürekli sayısal bir değer olduğu tipik bir gerileme sorunudur. Belirli bir otomobilin özellik değerleri verildiğinde, bu otomobilin fiyatını tahmin edebilen bir doğrusal regresyon modeline uyabiliriz. Bu regresyon modeli, eğitidiğimiz veri kümesini öğrenmek için kullanılabilir. Tahmin edilen otomobil fiyatlarına sahip olduktan sonra, tahmine dayalı olarak gerçek fiyatlardan ne kadar tahmin edilebileceğini inceleyerek model performansını değerlendirebiliriz. Bunu göstermek için, ML Studio (klasik) içindeki **kayıtlı veri kümeleri** bölümünde bulunan *otomatik mobil fiyat verileri (ham) veri kümesini* kullanırız.

### <a name="creating-the-experiment"></a>Deneme oluşturma
Aşağıdaki modülleri, Azure Machine Learning Studio klasik sürümünde çalışma alanınıza ekleyin:

* Otomobil fiyat verileri (ham)
* [Doğrusal regresyon][linear-regression]
* [Modeli eğitme][train-model]
* [Puan modeli][score-model]
* [Modeli değerlendir][evaluate-model]

Bağlantı noktalarını aşağıda gösterildiği gibi Şekil 1 ' de bağlayın ve [model eğitme][train-model] modülünün etiket sütununu *Price*olarak ayarlayın.

![Regresyon modelini değerlendirme](./media/evaluate-model-performance/1.png)

Şekil 1. Regresyon modeli değerlendiriliyor.

### <a name="inspecting-the-evaluation-results"></a>Değerlendirme sonuçlarını inceleme
Denemeyi çalıştırdıktan sonra, [modeli değerlendir][evaluate-model] modülünün çıkış bağlantı noktasına tıklayabilir ve değerlendirme sonuçlarını görmek Için *Görselleştir* ' i seçebilirsiniz. Regresyon modelleri için kullanılabilen değerlendirme ölçümleri şunlardır: *Ortalama mutlak hata*, *kök anlamı mutlak hata*, *göreli mutlak hata*, *göreli kare hata*ve *belirleme katsayısı*.

Burada "Error" terimi, tahmin edilen değer ile true değeri arasındaki farkı temsil eder. Bu farkın mutlak değeri veya kare değeri, tahmin edilen ve gerçek değer arasındaki fark bazı durumlarda negatif olabilir, ancak tüm örneklerde oluşan toplam hata boyutunu yakalamak için hesaplanır. Hata ölçümleri, bir regresyon modelinin tahmine dayalı performansını, tahminlerinin gerçek değerlerden oluşan ortalama sapması bakımından ölçer. Daha düşük hata değerleri, modelin tahminlerde daha doğru olduğu anlamına gelir. Tam bir hata ölçüsü, modelin verileri mükemmel bir şekilde sığdığı anlamına gelir.

Ayrıca, R kare olarak da bilinen belirleme katsayısı, modelin verilere ne kadar iyi uyduğunu ölçmenin standart bir yoludur. Model tarafından açıklanan çeşitleme oranı olarak yorumlanabilir. Bu örnekte daha yüksek bir oran daha iyidir, burada 1 mükemmel bir uyum gösterir.

![Doğrusal regresyon değerlendirmesi ölçümleri](./media/evaluate-model-performance/2.png)

Şekil 2. Doğrusal regresyon değerlendirmesi ölçümleri.

### <a name="using-cross-validation"></a>Çapraz doğrulama kullanma
Daha önce belirtildiği gibi, [çapraz doğrulama model][cross-validate-model] modülünü kullanarak yinelenen eğitim, Puanlama ve değerlendirmeleri otomatik olarak gerçekleştirebilirsiniz. Bu durumda tüm ihtiyacınız olan bir veri kümesi, eğitilmiş bir model ve [çapraz doğrulama modeli][cross-validate-model] modülüdür (aşağıdaki şekle bakın). [Çapraz doğrulama model][cross-validate-model] modülünün özelliklerinde Label sütununu *Price* olarak ayarlamanız gerekir.

![Regresyon modelinin çapraz doğrulanması](./media/evaluate-model-performance/3.png)

Şekil 3. Regresyon modelinin çapraz doğrulanması.

Denemeyi çalıştırdıktan sonra, [çapraz doğrulama modeli][cross-validate-model] modülünün sağ çıkış bağlantı noktasına tıklayarak değerlendirme sonuçlarını inceleyebilirsiniz. Bu, her yineleme için ölçümlerin ayrıntılı bir görünümünü (katlama) ve ölçümlerin her birinin ortalama sonucunu sağlar (Şekil 4).

![Regresyon modelinin çapraz doğrulama sonuçları](./media/evaluate-model-performance/4.png)

Şekil 4. Regresyon modelinin çapraz doğrulama sonuçları.

## <a name="evaluating-a-binary-classification-model"></a>Ikili sınıflandırma modelini değerlendirme
İkili sınıflandırma senaryosunda, hedef değişkeni yalnızca iki olası sonuç içerir, örneğin: {0, 1} veya {false, true}, {negative, pozitif}. Bazı demografik ve istihdam değişkenlerine sahip yetişkin çalışanları veri kümesi verildiğini ve {"< = 50 K", "> 50 K"} değerlerine sahip bir ikili değişken olan gelir düzeyini tahmin etmek isteyip istemediğiniz varsayıyoruz. Diğer bir deyişle, negatif sınıf, yılda 50 K veya daha küçük bir değere sahip olan çalışanları temsil eder ve pozitif sınıf diğer tüm çalışanları temsil eder. Regresyon senaryosunda, bir modeli eğtireceğiz, bazı verileri değerlendiyoruz ve sonuçları değerlendiririz. Buradaki temel fark, Azure Machine Learning Studio hesaplar ve çıktılar için klasik sürümü olan ölçüm seçimleridir. Gelir düzeyi tahmin senaryosunu göstermek için [yetişkinlere](https://archive.ics.uci.edu/ml/datasets/Adult) yönelik veri kümesini, yaygın olarak kullanılan bir ikili sınıflandırıcının bir Studio (klasik) denemesi oluşturmak ve iki sınıf lojistik regresyon modelinin performansını değerlendirmek için kullanacağız.

### <a name="creating-the-experiment"></a>Deneme oluşturma
Aşağıdaki modülleri, Azure Machine Learning Studio klasik sürümünde çalışma alanınıza ekleyin:

* Yetişkin Census geliri Ikili sınıflandırma veri kümesi
* [İki sınıf Lojistik gerileme][two-class-logistic-regression]
* [Modeli eğitme][train-model]
* [Puan modeli][score-model]
* [Modeli değerlendir][evaluate-model]

Bu bağlantı noktalarını Şekil 5 ' te aşağıda gösterildiği gibi bağlayın ve [model eğitme][train-model] modülünün etiket sütununu *gelir*olarak ayarlayın.

![Ikili sınıflandırma modelini değerlendirme](./media/evaluate-model-performance/5.png)

Şekil 5. Ikili sınıflandırma modeli değerlendiriliyor.

### <a name="inspecting-the-evaluation-results"></a>Değerlendirme sonuçlarını inceleme
Denemeyi çalıştırdıktan sonra, [modeli değerlendir][evaluate-model] modülünün çıkış bağlantı noktasına tıklayabilir ve değerlendirme sonuçlarını görmek Için *Görselleştir* ' i seçebilirsiniz (Şekil 7). İkili sınıflandırma modelleriyle kullanılabilen değerlendirme ölçümleri şunlardır: *doğruluk*, *duyarlık*, *geri çağırma*, *F1 puanı*ve *AUC*. Ayrıca modül, doğru pozitif sonuç sayısı, yanlış negatifler, yanlış pozitif sonuçlar ve gerçek negatiflerin sayısını gösteren bir karışıklık matrisi çıkarır, bu da *Roc*, *duyarlık/geri çekme*ve *yükseltme eğrileri.*

Doğruluk, doğru sınıflandırılan örneklerin oranını sağlar. Bu, genellikle bir sınıflandırıcının değerlendirmesi sırasında baktığın ilk ölçümdür. Bununla birlikte, test verileri dengesiz olduğunda (örneklerin çoğu sınıflardan birine aittir) veya sınıflardan birindeki performanstan daha fazla ilgileniyorsanız, doğruluk gerçekten bir sınıflandırıcının verimliliğini yakalamaz. Gelir düzeyi sınıflandırma senaryosunda, örneklerin %99 ' unun yıl başına 50 ' ye eşit veya daha az alan kişileri gösterdiği bazı verileri test ettiğini varsayın. Tüm örnekler için "< = 50K" sınıfını tahmin ederek 0,99 doğruluğu elde etmek mümkündür. Bu durumdaki sınıflandırıcının genel olarak iyi bir iş yapmakta olduğu görülüyor, ancak gerçekte, yüksek gelir bireylerini sınıflandırmayacak (%1) kopyalan.

Bu nedenle, değerlendirmenin daha belirgin yönlerini yakalayan ek ölçümleri hesaplamak yararlı olur. Bu tür ölçümlerin ayrıntılarına geçmeden önce, bir ikili sınıflandırma değerlendirmesinin karışıklık matrisini anlamak önemlidir. Eğitim kümesindeki sınıf etiketleri, genellikle pozitif veya negatif olarak ifade ettiğimiz yalnızca iki olası değeri alabilir. Bir sınıflandırıcının doğru şekilde tahmin edilen pozitif ve negatif örneklere, sırasıyla doğru pozitif sonuçlar (TP) ve gerçek negatifler (TN) denir. Benzer şekilde, yanlış sınıflandırılmış örneklere yanlış pozitif durumlar (FP) ve yanlış negatifler (FN) denir. Karışıklık matrisi, bu dört kategorinin her biri altında kalan örneklerin sayısını gösteren bir tablodur. Azure Machine Learning Studio klasik sürümü otomatik olarak veri kümesindeki iki sınıftan hangisinin pozitif sınıf olduğunu belirler. Sınıf etiketleri Boole veya tamsayı ise, ' true ' veya ' 1 ' olarak etiketlenmiş örnekler pozitif sınıfa atanır. Etiketler, gelir veri kümesi gibi dizelerdir, Etiketler alfabetik olarak sıralanır ve ikinci düzey pozitif sınıf olduğunda, ilk düzey negatif sınıf olarak seçilir.

![İkili sınıflandırma karışıklık matrisi](./media/evaluate-model-performance/6a.png)

Şekil 6. İkili sınıflandırma karışıklık matrisi.

Gelir sınıflandırması sorununa geri dönerek, kullanılan sınıflandırıcının performansını anmamıza yardımcı olan birkaç değerlendirme sorusu istemek istiyoruz. Doğal bir soru: ' modelin, > 50 K (TP + FP) kazanmakta tahmin edildiği kişilerin dışına, kaç tane doğru şekilde sınıflandırıldığı (TP)? ' Bu soru, doğru sınıflandırılan pozitif sonuç oranı olan modelin **hassasiyetini** arayarak yanıtlanır: TP/(TP + FP). Diğer bir yaygın soru, "50. gelir > 50K (TP + FN) ve sınıflandırıcının doğru sınıflandırmasına (TP) kadar çok büyük olan çalışanların tümünün dışındadır. Bu aslında **geri çektir**veya sınıflandırıcının gerçek pozitif ORANı: TP/(TP + fn). Duyarlık ve geri çekme arasında açık bir denge olduğunu fark edebilirsiniz. Örneğin görece dengeli bir veri kümesi verildiğinde, genellikle pozitif örnekleri tahmin eden bir sınıflandırıcı yüksek bir geri çekmelidir, ancak negatif örneklerin birçoğu büyük miktarda yanlış pozitif sonuç oluşmasına neden olacak şekilde düşük bir duyarlık olabilir. Bu iki ölçümün nasıl değişeceğini gösteren bir çizim görmek için değerlendirme sonucu çıktısı sayfasında **duyarlık/GERI çağırma** eğrisine (Şekil 7 ' nin sol üst kısmında) tıklayabilirsiniz.

![İkili sınıflandırma değerlendirme sonuçları](./media/evaluate-model-performance/7.png)

Şekil 7. İkili sınıflandırma değerlendirme sonuçları.

Genellikle kullanılan başka bir ilgili ölçüm, her iki duyarlık ve geri çekmeyi göz önüne alan **F1 puandır**. Bu iki ölçümün harmonik ortalaması vardır ve şu şekilde hesaplanır: F1 = 2 (duyarlık x geri çekme)/(duyarlık + geri çağırma). F1 puanı, değerlendirmeyi tek bir sayı içinde özetlemek için iyi bir yoldur, ancak her zaman bir sınıflandırıcının nasıl davranacağını daha iyi anlamak için her iki duyarlığa bakmak ve birlikte çağırmak iyi bir uygulamadır.

Ayrıca, bir diğeri gerçek pozitif oranı ve **alıcı Işletim özelliği (ROC)** eğrisi ve **eğri (AUC) değeri altındaki** karşılık gelen alanı yanlış pozitif orandır. Bu eğriye yaklaşmak, sol üst köşenin ne kadar iyi olduğunu, sınıflandırıcının performansının ne kadar iyi olduğunu (yanlış pozitif oranı en aza indirerek gerçek pozitif oranı en üst düzeye çıkarmasıdır) sağlar. Çizimin köşegenini yakın olan eğriler, rastgele tahmine yakın tahminlere yol açacak Sınıflandırıcılardan oluşur.

### <a name="using-cross-validation"></a>Çapraz doğrulama kullanma
Regresyon örneğinde olduğu gibi, verilerin farklı alt kümelerini otomatik olarak eğitme, Puanlama ve değerlendirmek için çapraz doğrulama gerçekleştirebiliriz. Benzer şekilde, [çapraz doğrulama model][cross-validate-model] modülünü, eğitilen lojistik regresyon modelini ve bir veri kümesini kullanabiliriz. [Çapraz doğrulama model][cross-validate-model] modülünün özelliklerinde, etiket sütunu *gelir* olarak ayarlanmalıdır. Denemeyi çalıştırdıktan ve [çapraz doğrulama modeli][cross-validate-model] modülünün sağ çıkış bağlantı noktasına tıkladıktan sonra, her katlama için ikili sınıflandırma ölçümü değerlerini, her birinin ortalama ve standart sapmasına ek olarak görebiliriz. 

![Ikili sınıflandırma modelinin çapraz doğrulanması](./media/evaluate-model-performance/8.png)

Şekil 8. Ikili sınıflandırma modelinin çapraz doğrulanması.

![Ikili sınıflandırıcının çapraz doğrulama sonuçları](./media/evaluate-model-performance/9.png)

Şekil 9. Ikili sınıflandırıcının çapraz doğrulama sonuçları.

## <a name="evaluating-a-multiclass-classification-model"></a>Birden çok Lass sınıflandırma modelini değerlendirme
Bu deneyde, Iris tesisindeki üç farklı türde (sınıf) örnekler içeren popüler [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "İs") veri kümesini kullanacağız. Her örnek için dört Özellik değeri (sepal uzunluğu/genişliği ve Petal uzunluğu/genişliği) vardır. Önceki denemeleri, modelleri aynı veri kümelerini kullanarak eğitimli ve test ediyoruz. Burada, verilerin iki alt kümesini oluşturmak, birincinin üzerinde eğmek ve ikincisi değerlendirmek ve değerlendirmek için [bölünmüş veri][split] modülünü kullanacağız. Iris veri kümesi, [UCI Machine Learning deposunda](https://archive.ics.uci.edu/ml/index.html)herkese açık bir şekilde bulunabilir ve [içeri aktarma verileri][import-data] modülü kullanılarak indirilebilir.

### <a name="creating-the-experiment"></a>Deneme oluşturma
Aşağıdaki modülleri, Azure Machine Learning Studio klasik sürümünde çalışma alanınıza ekleyin:

* [Verileri içeri aktar][import-data]
* [Birden çok Lass karar ormanı][multiclass-decision-forest]
* [Verileri Böl][split]
* [Modeli eğitme][train-model]
* [Puan modeli][score-model]
* [Modeli değerlendir][evaluate-model]

Bağlantı noktalarını Şekil 10 ' da aşağıda gösterildiği gibi bağlayın.

[Model eğitimi][train-model] modülünün etiket sütun dizinini 5 olarak ayarlayın. DataSet 'in başlık satırı yok ancak sınıf etiketlerinin beşinci sütunda olduğunu biliyoruz.

[Veri alma][import-data] modülüne tıklayın ve *VERI kaynağı* özelliğini *http aracılığıyla Web URL 'Si*olarak ve http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data*URL 'si* olarak ayarlayın.

[Veri ayırma][split] modülündeki eğitim için kullanılacak örneklerin kesirini ayarlayın (örneğin, 0,7).

![Birden çok Lass sınıflandırıcısını değerlendirme](./media/evaluate-model-performance/10.png)

Şekil 10. Birden çok Lass sınıflandırıcısını değerlendirme

### <a name="inspecting-the-evaluation-results"></a>Değerlendirme sonuçlarını inceleme
Denemeyi çalıştırın ve [modeli değerlendir][evaluate-model]çıkış bağlantı noktasına tıklayın. Değerlendirme sonuçları, bu durumda bir karışıklık matrisi biçiminde sunulmuştur. Matris, üç sınıf için gerçek ve tahmin edilen örnekleri gösterir.

![Birden çok Lass sınıflandırma değerlendirmesi sonuçları](./media/evaluate-model-performance/11.png)

Şekil 11. Birden çok Lass sınıflandırma değerlendirmesi sonuçları.

### <a name="using-cross-validation"></a>Çapraz doğrulama kullanma
Daha önce belirtildiği gibi, [çapraz doğrulama model][cross-validate-model] modülünü kullanarak yinelenen eğitim, Puanlama ve değerlendirmeleri otomatik olarak gerçekleştirebilirsiniz. Bir veri kümesi, eğitilmiş bir model ve [çapraz doğrulama modeli][cross-validate-model] modülü gerekir (aşağıdaki şekle bakın). Yine de [çapraz doğrulama modeli][cross-validate-model] modülünün etiket sütununu ayarlamanız gerekir (Bu durumda sütun dizini 5). Denemeyi çalıştırdıktan ve [çapraz doğrulama modelinin][cross-validate-model]sağ çıkış bağlantı noktasına tıkladıktan sonra, her katlama ve ortalama ve standart sapmanın ölçüm değerlerini inceleyebilirsiniz. Burada görüntülenen ölçümler, ikili sınıflandırma durumunda açıklananlara benzer. Ancak, birden çok Lass sınıflandırmasında, bir genel pozitif veya negatif sınıf olmadığından, gerçek pozitif sonuçlar/negatifler ve hatalı pozitif sonuçlar/negatifler için bir sınıf başına olarak sayım yapılır. Örneğin, ' Iris-setosa ' sınıfının kesinlik veya geri çağırma işlemi hesaplanırken, bu pozitif sınıf ve diğerlerinin negatif olduğu varsayılır.

![Birden çok Lass sınıflandırma modelinin çapraz doğrulanması](./media/evaluate-model-performance/12.png)

Şekil 12. Birden çok Lass sınıflandırma modelinin çapraz doğrulanması.

![Birden çok Lass sınıflandırma modelinin çapraz doğrulama sonuçları](./media/evaluate-model-performance/13.png)

Şekil 13. Birden çok Lass sınıflandırma modelinin çapraz doğrulama sonuçları.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
