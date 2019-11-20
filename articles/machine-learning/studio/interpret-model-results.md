---
title: Model sonuçlarını yorumlama
titleSuffix: ML Studio (classic) - Azure
description: Puan modeli çıkışlarını kullanarak bir algoritma için en uygun parametre kümesini seçme ve görselleştirme.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 11/29/2017
ms.openlocfilehash: acac881a790644532121697e6b79e57a98b168fe
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839838"
---
# <a name="interpret-model-results-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio model sonuçlarını yorumlama (klasik)
Bu konuda, tahmin sonuçlarının Azure Machine Learning Studio (klasik) görselleştirilmesi ve yorumlanması açıklanmaktadır. Bir modeli eğitdikten ve bunun üzerine ("model puanlanır") ilişkin tahminleri tamamladıktan sonra, tahmin sonucunu anlamanız ve yorumlamanıza gerek duyarsınız.



Azure Machine Learning Studio klasik sürümünde dört ana makine öğrenimi modeli vardır:

* Sınıflandırma
* Lenmesi
* regresyon
* Öneren sistemleri

Bu modellerin üzerine tahmin için kullanılan modüller şunlardır:

* Sınıflandırma ve gerileme için [model modülü puanı][score-model]
* Kümeleme için [kümeler modülüne atama][assign-to-clusters]
* Öneri sistemleri için [Matchbox öneren puanı][score-matchbox-recommender]

Bu belgede, bu modüllerin her biri için tahmin sonuçlarının nasıl yorumlanacağı açıklanmaktadır. Bu modüllerle ilgili genel bir bakış için bkz. [Azure Machine Learning Studio (klasik) algoritmalarınızı iyileştirmek için parametreler seçme](algorithm-parameters-optimize.md).

Bu konuda tahmin yorumu ele alınmaktadır, ancak model değerlendirmesi uygulanmaz. Modelinizin nasıl değerlendirileceği hakkında daha fazla bilgi için bkz. [Azure Machine Learning Studio model performansını değerlendirme (klasik)](evaluate-model-performance.md).

Azure Machine Learning Studio klasik sürümüne yeni başladıysanız ve başlamak için basit bir deneme oluşturma konusunda yardıma ihtiyacınız varsa, bkz. [Azure Machine Learning studio 'de basit bir deneme oluşturma (klasik)](create-experiment.md).

## <a name="classification"></a>Sınıflandırma
Sınıflandırma sorunlarının iki alt kategorisi vardır:

* Yalnızca iki sınıfla ilgili sorunlar (iki sınıf veya ikili sınıflandırma)
* İkiden fazla sınıftan sorunlar (çok sınıf sınıflandırması)

Azure Machine Learning Studio (klasik), bu sınıflandırma türlerinin her biriyle başa çıkmak için farklı modüllere sahiptir, ancak tahmin sonuçlarını yorumlama yöntemleri benzerdir.

### <a name="two-class-classification"></a>İki sınıf sınıflandırma
**Örnek deneme**

İki sınıflı bir sınıflandırma sorununa örnek Iris çiçekler sınıflandırmasıdır. Görev, Iris çiçekler özelliklerine göre sınıflandırılır. Azure Machine Learning Studio klasik sürümünde belirtilen Iris veri kümesi, yalnızca iki çiçek türler (sınıflar 0 ve 1) örneklerini içeren popüler [Iris veri kümesinin](https://en.wikipedia.org/wiki/Iris_flower_data_set) bir alt kümesidir. Her çiçek için dört Özellik (sepal uzunluğu, sepal genişliği, Petal uzunluğu ve Petal genişliği) vardır.

![Iris denemesinin ekran görüntüsü](./media/interpret-model-results/1.png)

Şekil 1. Iris iki sınıflı sınıflandırma sorunu denemesi

Şekil 1 ' de gösterildiği gibi, bu sorunu çözmek için bir deneme gerçekleştirildi. İki sınıf bir artırılmış karar ağacı modeli eğitildi ve puanlanmış. Artık, [puan][score-model] modeli modülünün çıkış bağlantı noktasına tıklayıp **Görselleştir**' e tıklayarak [puan modeli][score-model] modülünün tahmin sonuçlarını görselleştirebilirsiniz.

![Puan modeli modülü](./media/interpret-model-results/1_1.png)

Bu, Şekil 2 ' de gösterildiği gibi Puanlama sonuçlarını getirir.

![Iris iki sınıflı sınıflandırma denemenizin sonuçları](./media/interpret-model-results/2.png)

Şekil 2. İki sınıf sınıflandırmasında puan modeli sonucunu görselleştirin

**Sonuç yorumu**

Sonuçlar tablosunda altı sütun vardır. Sol dört sütun dört özelliklerdir. Sağ iki sütun, puanlanmış Etiketler ve puanlanmış olasılıkların tahmin sonuçlarıdır. Puanlanmış olasılıkların sütunu, bir çiçek 'nin pozitif sınıfa ait olma olasılığını gösterir (sınıf 1). Örneğin, sütundaki ilk sayı (0,028571), ilk çiçek 'un Sınıf 1 ' e ait olduğu 0,028571 olasılık olduğu anlamına gelir. Puanlanmış Etiketler sütunu her çiçek için tahmin edilen sınıfı gösterir. Bu, puanlanmış olasılıkların sütununu temel alır. Bir çiçek 'un puanlanması 0,5 ' den büyükse, sınıf 1 olarak tahmin edilir. Aksi takdirde, Sınıf 0 olarak tahmin edilir.

**Web hizmeti yayını**

Tahmin sonuçları anlaşıldıktan ve sesi karardığında, deneme bir Web hizmeti olarak yayımlanabilir ve bu sayede, çeşitli uygulamalarda dağıtabilmeniz ve yeni bir Iris çiçek üzerinde sınıf tahminleri elde etmek üzere çağrılamaz. Eğitim denemesinin bir Puanlama denemenize nasıl değiştirileceğini ve bir Web hizmeti olarak nasıl yayımlanacağını öğrenmek için bkz. [öğretici 3: Kredi risk modelini dağıtma](tutorial-part3-credit-risk-deploy.md). Bu yordam, Şekil 3 ' te gösterildiği gibi bir Puanlama denemesi sağlar.

![Puanlama denemesinin ekran görüntüsü](./media/interpret-model-results/3.png)

Şekil 3. Iris iki sınıflı sınıflandırma sorunu denemesini Puanlama

Şimdi Web hizmeti için giriş ve çıkış ayarlamanız gerekir. Giriş, Iris çiçek özellikleri girişi olan [puan modelinin][score-model]doğru giriş bağlantı noktasıdır. Çıktı seçimi, tahmin edilen Sınıf (puanlanmış etiket), puanlanmış olasılık veya her ikisi ile ilgileniyor olmanıza bağlıdır. Bu örnekte, her ikisiyle de ilgilendiğiniz varsayılır. İstenen çıkış sütunlarını seçmek için [veri kümesi modülündeki sütunları seç][select-columns] ' i kullanın. [Veri kümesinde sütunları seç][select-columns]' e tıklayın, **sütun seçiciyi Başlat**' a tıklayın ve **puanlanmış Etiketler** ve **puanlanmış olasılıklara**seçin. [Veri kümesinde sütun Seç][select-columns] ' in çıkış bağlantı noktasını ayarladıktan ve yeniden çalıştırdıktan sonra, **Web hizmetini Yayımla**' ya tıklayarak Puanlama denemesini bir Web hizmeti olarak yayımlamaya hazırlanmalısınız. Son deneme Şekil 4 gibi görünür.

![Iris iki sınıf sınıflandırma denemesi](./media/interpret-model-results/4.png)

Şekil 4. Iris iki sınıflı sınıflandırma sorununun nihai Puanlama denemesi

Web hizmetini çalıştırdıktan ve bir test örneğinin bazı özellik değerlerini girdikten sonra, sonuç iki sayı döndürür. İlk sayı puanlanmış etikettir ve ikincisi de puanlanır. Bu çiçek, 0,9655 olasılığa sahip sınıf 1 olarak tahmin edilir.

![Test yorumlama Puanlama modeli](./media/interpret-model-results/4_1.png)

![Puanlama test sonuçları](./media/interpret-model-results/5.png)

Şekil 5. Iris iki sınıf sınıflandırmasının Web hizmeti sonucu

### <a name="multi-class-classification"></a>Çok sınıflı sınıflandırma
**Örnek deneme**

Bu deneyde, birden çok Lass sınıflandırmasına örnek olarak bir mektup tanıma görevi gerçekleştirirsiniz. Sınıflandırıcı, el ile yazılmış görüntülerden ayıklanan, el ile yazılmış bazı öznitelik değerlerine göre belirli bir harf (sınıf) tahmin etmeye çalışır.

![Mektup tanıma örneği](./media/interpret-model-results/5_1.png)

Eğitim verilerinde, el ile yazılmış mektup görüntülerinden ayıklanan 16 özellik vardır. 26 harfli 26 sınıflarımızda bir biçim vardır. Şekil 6 ' da, birden çok Lass sınıflandırma modelini mektup tanıma için eğiten ve bir test veri kümesindeki aynı özellik kümesinde tahmin edilecek bir deneme gösterilmektedir.

![Mektup tanıma birden çok sınıf sınıflandırma denemesi](./media/interpret-model-results/6.png)

Şekil 6. Mektup tanıma birden çok sınıf sınıflandırması sorunu deneme

Puanlama [modeli modülünün][score-model] çıkış bağlantı noktasına tıklayın ve ardından **Görselleştir**' e tıklayarak, [][score-model] Şekil 7 ' de gösterildiği gibi içeriği görmeniz gerekir.

![Model sonuçlarını Puanlama](./media/interpret-model-results/7.png)

Şekil 7. Çok sınıflı bir sınıflandırmayla puan modeli sonuçlarını görselleştirin

**Sonuç yorumu**

Sol 16 sütun, test kümesinin özellik değerlerini temsil eder. "XX" sınıfı için puanlanmış olasılıkların gibi adlara sahip sütunlar, iki sınıf büyük küçük harfli yalnızca puanlanmış olasılıklara benzer. Bunlara karşılık gelen girişin belirli bir sınıfa denk gelir gösterilir. Örneğin, ilk giriş için, "B" ve benzeri bir "A" olduğu gibi bir "A", 0,000451 olasılık olduğu için 0,003571 olasılığı vardır. Son sütun (puanlanmış Etiketler) iki sınıflı büyük harfli puanlanmış etiketlerle aynıdır. Karşılık gelen girişin öngörülen sınıfı olarak en büyük puanlanmış olasılığa sahip sınıfı seçer. Örneğin, ilk giriş için, "f" (0,916995) olarak en büyük olasılığa sahip olduğundan, puanlanmış etiket "F" olur.

**Web hizmeti yayını**

Ayrıca, her giriş için puanlanmış etiketi ve puanlanmış etiketin olasılığını da alabilirsiniz. Temel mantık, tüm puanlanmış olasılıkların arasındaki en büyük olasılığı bulmadır. Bunu yapmak için, [R betiği Yürüt][execute-r-script] modülünü kullanmanız gerekir. R kodu Şekil 8 ' de gösterilir ve deneme sonucu Şekil 9 ' da gösterilir.

![R kodu örneği](./media/interpret-model-results/8.png)

Şekil 8. Puanlanmış etiketlerin ve etiketlerin ilişkili olasılıkların ayıklanması için R kodu

![Deneme sonucu](./media/interpret-model-results/9.png)

Şekil 9. Mektup tanıma çok birimli sınıflandırma sorununun nihai Puanlama denemesi

Web hizmetini yayımladıktan ve çalıştırdıktan sonra bazı giriş özelliği değerlerini girdikten sonra, döndürülen sonuç Şekil 10 ' u gibi görünür. Ayıklanmış 16 özellikleriyle birlikte yazılan bu harf, 0,9715 olasılığa sahip bir "T" olarak tahmin edilir.

![Test yorumlama Puanlama modülü](./media/interpret-model-results/9_1.png)

![Test sonucu](./media/interpret-model-results/10.png)

Şekil 10. Birden çok Lass sınıflandırmasının Web hizmeti sonucu

## <a name="regression"></a>regresyon
Gerileme sorunları, sınıflandırma sorunlarından farklıdır. Bir sınıflandırma sorunu içinde, bir Iris çiçeği ait olduğu sınıf gibi ayrı sınıfları tahmin etmeye çalışıyorsunuz. Ancak aşağıdaki bir gerileme sorunu örneğinde görebileceğiniz gibi, bir otomobil fiyatı gibi sürekli bir değişkeni tahmin etmeye çalışıyorsunuz demektir.

**Örnek deneme**

Gerileme için örnek olarak, otomobil fiyat tahminini kullanın. Marka, yakıt türü, gövde türü ve sürücü tekerleği gibi özelliklerine göre bir otomobil fiyatını tahmin etmeye çalışıyorsunuz. Deneme Şekil 11 ' de gösterilmiştir.

![Otomobil fiyat gerileme denemesi](./media/interpret-model-results/11.png)

Şekil 11. Otomobil fiyat regresyon sorunu denemesi

[Puan modeli][score-model] modülünü görselleştirirken sonuç Şekil 12 ' ye benzer.

![Otomobil fiyat tahmini sorunu için Puanlama sonuçları](./media/interpret-model-results/12.png)

Şekil 12. Otomobil fiyat tahmini sorunu için Puanlama sonucu

**Sonuç yorumu**

Puanlanmış Etiketler, bu Puanlama sonucunda elde edilen sonuç sütunudur. Numaralar her bir araba için öngörülen fiyattan alınır.

**Web hizmeti yayını**

Regresyon denemesini bir Web hizmetine yayımlayabilir ve bunu, iki sınıf sınıflandırma kullanım örneği ile aynı şekilde, otomobil fiyat tahmini için çağırabilirsiniz.

![Otomobil fiyat regresyon sorunu için Puanlama denemesi](./media/interpret-model-results/13.png)

Şekil 13. Bir otomobil fiyat gerileme sorununun Puanlama denemesi

Web hizmeti çalıştırıldığında döndürülen sonuç Şekil 14 ' ü gibi görünür. Bu araba için öngörülen fiyat $15.085,52 ' dir.

![Test yorumlama Puanlama modülü](./media/interpret-model-results/13_1.png)

![Puanlama modülü sonuçları](./media/interpret-model-results/14.png)

Şekil 14. Bir otomobil fiyat gerileme sorununun Web hizmeti sonucu

## <a name="clustering"></a>Lenmesi
**Örnek deneme**

Bir kümeleme denemesi oluşturmak için Iris veri kümesini yeniden kullanalım. Burada, veri kümesindeki sınıf etiketlerini filtreleyerek yalnızca özellikler ve kümeleme için kullanılabilir hale getirebilirsiniz. Bu Iris kullanım durumu ' nda, eğitim süreci sırasında iki sınıf olacak küme sayısını belirtin. Bu, çiçekleri iki sınıfa Kümelendirmek anlamına gelir. Deneme şekil 15 ' te gösterilmiştir.

![Iris kümeleme sorunu denemesi](./media/interpret-model-results/15.png)

Şekil 15. Iris kümeleme sorunu denemesi

Kümeleme, eğitim veri kümesinin kendine ait taban-Ilk etiketlerini içermediğinden sınıflandırmadan farklıdır. Kümeleme grupları eğitim verileri, örnekleri ayrı kümeler halinde ayarlar. Eğitim süreci sırasında model, özellikleri arasındaki farkları öğrenerek girişleri Etiketler. Bundan sonra, eğitilen model gelecekteki girişleri daha fazla sınıflandırmak için kullanılabilir. Sonucun bir kümeleme sorunu içinde ilgilendiğimiz iki bölümü vardır. İlk bölümde eğitim veri kümesinin etiketlenmesi ve ikincisi, eğitilen modelle yeni bir veri kümesini sınıflandırırken.

Sonucun ilk bölümü, [kümeleme modeli eğitimi][train-clustering-model] ' nin sol çıkış bağlantı noktasına tıklanarak ve ardından **Görselleştir**' i tıklatarak görselleştirilir. Görselleştirme Şekil 16 ' da gösterilmiştir.

![Kümeleme sonucu](./media/interpret-model-results/16.png)

Şekil 16. Eğitim veri kümesi için kümeleme sonucunu görselleştirin

İkinci parçanın sonucu olarak, eğitilen kümeleme modeliyle yeni girişler kümeleme, şekil 17 ' de gösterilmiştir.

![Kümeleme sonucunu görselleştirin](./media/interpret-model-results/17.png)

Şekil 17. Yeni bir veri kümesi üzerinde kümeleme sonucunu görselleştirin

**Sonuç yorumu**

İki bölümden oluşan sonuçlar farklı deneme aşamalarından gövdeli, aynı şekilde görünür ve aynı şekilde yorumlanır. İlk dört sütun özelliklerdir. Son sütun, Atamalar, tahmin sonucudur. Aynı sayının atandığı girişlerin aynı kümede olduğu tahmin edilir, yani benzerlikler bir şekilde paylaşır (Bu deneme varsayılan Euclidean Distance ölçüsünü kullanır). 2 olacak küme sayısını belirttiğinden, atamalardaki girdiler 0 veya 1 etiketlidir.

**Web hizmeti yayını**

Kümeleme denemesini bir Web hizmetine yayımlayabilir ve bunu iki sınıf sınıflandırma kullanım örneği ile aynı şekilde kümeleme tahminleri için çağırabilirsiniz.

![Iris Kümelemesi sorunu için Puanlama denemesi](./media/interpret-model-results/18.png)

Şekil 18. Bir Iris Kümelemesi sorununun Puanlama denemesi

Web hizmetini çalıştırdıktan sonra, döndürülen sonuç Şekil 19 gibi görünür. Bu çiçek 0 kümesinde olduğu tahmin edilir.

![Test yorumlaması modülü](./media/interpret-model-results/18_1.png)

![Puanlama modülü sonucu](./media/interpret-model-results/19.png)

Şekil 19. Iris iki sınıf sınıflandırmasının Web hizmeti sonucu

## <a name="recommender-system"></a>Öneren sistemi
**Örnek deneme**

Öneren sistemleri için, Restoran önerisi sorununu örnek olarak kullanabilirsiniz: Bu müşterilere kendi derecelendirme geçmişine göre Restoran önerebilirsiniz. Giriş verileri üç bölümden oluşur:

* Müşterilerden Restoran derecelendirmeleri
* Müşteri özelliği verileri
* Restoran özellik verileri

Azure Machine Learning Studio klasik sürümünde [tren Matchbox öneren][train-matchbox-recommender] modülü ile yapabiliriz birkaç şey vardır:

* Belirli bir Kullanıcı ve öğe için derecelendirmeleri tahmin etme
* Belirli bir kullanıcıya öğe önerme
* Belirli bir kullanıcı ile ilgili kullanıcıları bulma
* Belirli bir öğeyle ilgili öğeleri bul

**Öneren tahmin türü** menüsündeki dört seçenekten birini seçerek ne yapmak istediğinizi seçebilirsiniz. Burada, dört senaryonun tümünde gezinebilirsiniz.

![Matchbox öneren](./media/interpret-model-results/19_1.png)

Bir öneren sistemi için tipik bir Azure Machine Learning Studio (klasik) deneme, Şekil 20 ' ye benzer. Bu öneren sistem modüllerinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Matchbox öneren][train-matchbox-recommender] and [Score Matchbox öneren][score-matchbox-recommender].

![Öneren sistem denemesi](./media/interpret-model-results/20.png)

Şekil 20. Öneren sistem denemesi

**Sonuç yorumu**

**Belirli bir Kullanıcı ve öğe için derecelendirmeleri tahmin etme**

**Öneren tahmin türü**altında **Derecelendirme tahmini** ' ni seçerek, öneren sistemine belirli bir Kullanıcı ve öğe için derecelendirmeyi tahmin etmek isteyip istemediğinizi sorar. [Score Matchbox öneren][score-matchbox-recommender] çıkışının görselleştirmesi şekil 21 gibi görünür.

![Öneren sisteminin sonucunu Puanlama--derecelendirme tahmini](./media/interpret-model-results/21.png)

Şekil 21. Öneren sisteminin puan sonucunu görselleştirin--derecelendirme tahmini

İlk iki sütun, giriş verileri tarafından sunulan Kullanıcı öğesi çiftleridir. Üçüncü sütun, bir kullanıcının belirli bir öğe için öngörülen derecelendirmesidir. Örneğin, ilk satırda Customer U1048, Restoran 135026 ' i 2 olarak ücretlendirebilirsiniz.

**Belirli bir kullanıcıya öğe önerme**

**Öneren tahmin türü**' nün altında **öğe önerisi** ' ni seçerek, öneren sistemine belirli bir kullanıcıya öğe önermesinin soruluyor demektir. Bu senaryoda seçeceğiniz son parametre *Önerilen öğe seçiminden*. **Derecelendirilen öğelerin seçeneği (model değerlendirmesi için)** öncelikle eğitim süreci sırasında model değerlendirmesi içindir. Bu tahmin aşaması için **tüm öğeler**arasından seçim yaptık. [Score Matchbox öneren][score-matchbox-recommender] çıkışının görselleştirmesi şekil 22 gibi görünür.

![Öneren sisteminin sonucunu Puanlama--öğe önerisi](./media/interpret-model-results/22.png)

Şekil 22. Öneren sisteminin sonucunu görselleştirin--öğe önerisi

Altı sütunun ilki, giriş verileri tarafından sağlandığı gibi öğelerini önermek için verilen kullanıcı kimliklerini temsil eder. Diğer beş sütun, Kullanıcı için önerilen öğeleri azalan ilgi sırasına göre temsil eder. Örneğin, ilk satırda, Customer U1048 için en önerilen Restoran 134986, ardından 135018, 134975, 135021 ve 132862 ' dir.

**Belirli bir kullanıcı ile ilgili kullanıcıları bulma**

**Öneren tahmin türü**altında **ilgili kullanıcılar** ' ı seçerek, öneren sistemine belirli bir kullanıcı için ilgili kullanıcıları bulmasını soruyoruz. İlgili kullanıcılar, benzer tercihleri olan kullanıcılardır. Bu senaryoda seçeceğiniz son parametre *ilgili Kullanıcı seçiminden*. **Öğeleri derecelendirmeden kullananlar (model değerlendirmesi için)** öncelikle eğitim süreci sırasında model değerlendirmesi içindir. Bu tahmin aşaması için **tüm kullanıcılar** arasından seçim yapın. [Score Matchbox öneren][score-matchbox-recommender] çıkışının görselleştirmesi Şekil 23 gibi görünür.

![Öneren sistemiyle ilgili kullanıcıların sonucunu Puanlama](./media/interpret-model-results/23.png)

Şekil 23. Öneren sistemiyle ilgili kullanıcıların puan sonuçlarını görselleştirin

Altı sütunun ilki, giriş verileri tarafından sağlanan ilgili kullanıcıları bulmak için gereken kullanıcı kimliklerini gösterir. Diğer beş sütun, kullanıcının tahmini ilgili kullanıcılarını azalan ilgi sırasına göre depolar. Örneğin, ilk satırda, Customer U1048 için en ilgili müşteri U1051, ardından U1066, U1044, U1017 ve U1072.

**Belirli bir öğeyle ilgili öğeleri bul**

**Öneren tahmin türü**altında **ilgili öğeleri** seçerek, öneren sistemine belirli bir öğe için ilgili öğeleri bulmasını sormanız gerekir. İlgili öğeler, aynı kullanıcı tarafından beğenilen en büyük öğelerdir. Bu senaryoda seçeceğiniz son parametre *ilgili öğe seçiminden*. **Derecelendirilen öğelerin seçeneği (model değerlendirmesi için)** öncelikle eğitim süreci sırasında model değerlendirmesi içindir. Bu tahmin aşaması için **tüm öğeler** arasından seçim yaptık. [Score Matchbox öneren][score-matchbox-recommender] çıkışının görselleştirmesi Şekil 24 gibi görünür.

![Öneren sistemiyle ilgili öğelerin sonucunu Puanlama](./media/interpret-model-results/24.png)

Şekil 24. Öneren sistemiyle ilgili öğelerin puan sonuçlarını görselleştirin

Altı sütunun ilki, giriş verileri tarafından sağlandığı gibi ilgili öğeleri bulmak için gereken belirli öğe kimliklerini temsil eder. Diğer beş sütun, öğenin öngörülen ilgili öğelerini ilgi açısından azalan sırada depolar. Örneğin, ilk satırda, 135026 öğesi için en ilgili öğe 135074, ardından 135035, 132875, 135055 ve 134992.

**Web hizmeti yayını**

Öngörülere ulaşmak için bu denemeleri web hizmeti olarak yayımlama işlemi dört senaryonun her biri için benzerdir. Burada örnek olarak İkinci senaryo (belirli bir kullanıcıya yönelik öğeler önerilir) sunuyoruz. Aynı yordamı diğer üç ile de izleyebilirsiniz.

Eğitilen öneren sistemi eğitilen bir model olarak kaydediliyor ve giriş verilerini istenen şekilde tek bir kullanıcı KIMLIĞI sütununa filtreleyerek, denemeyi Şekil 25 ' te açabilir ve bir Web hizmeti olarak yayımlayabilirsiniz.

![Restoran önerisi sorununun Puanlama denemesi](./media/interpret-model-results/25.png)

Şekil 25. Restoran önerisi sorununun Puanlama denemesi

Web hizmeti çalıştırıldığında döndürülen sonuç Şekil 26 ' ya benzer. Kullanıcı U1048 için önerilen beş Restoran 134986, 135018, 134975, 135021 ve 132862.

![Öneren sistem hizmeti örneği](./media/interpret-model-results/25_1.png)

![Örnek deneme sonuçları](./media/interpret-model-results/26.png)

Şekil 26. Restoran önerisi sorununun Web hizmeti sonucu

<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
