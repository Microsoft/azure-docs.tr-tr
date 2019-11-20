---
title: 'Çapraz doğrulama modeli: modül başvurusu'
titleSuffix: Azure Machine Learning service
description: Verileri bölümleyerek sınıflandırma veya regresyon modelleriyle ilgili parametre tahminlerini çapraz doğrulamak için Azure Machine Learning hizmetinde çapraz doğrulama modeli modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: d83a9b5df7acc9d626613e53369f483367e55a54
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717246"
---
# <a name="cross-validate-model"></a>Çapraz doğrulama modeli

Bu makalede, Azure Machine Learning Tasarımcısı 'nda (Önizleme) çapraz doğrulama modeli modülünün nasıl kullanılacağı açıklanır. *Çapraz doğrulama* , genellikle bir veri kümesinin çeşitliliğini ve bu verilerden eğitilen herhangi bir modelin güvenilirliğini değerlendirmek için makine öğreniminde kullanılan bir tekniktir.  

Çapraz Validate model modülü, bir etiketli veri kümesi olarak, eğitilmiş bir sınıflandırma veya regresyon modeliyle birlikte giriş olarak alır. Veri kümesini bazı alt*kümeler (kat*) halinde böler, her katlama üzerinde bir model oluşturur ve ardından her katlama için bir doğruluk istatistikleri kümesi döndürür. Tüm katların doğruluk istatistiklerini karşılaştıran şekilde, veri kümesinin kalitesini yorumlayabilir. Daha sonra modelin veride çeşitliliğe açık olup olmadığını anlayabilirsiniz.  

Çapraz doğrulama modeli ayrıca veri kümesi için tahmin edilen sonuçları ve olasılıkların yanı sıra tahmine yönelik güvenilirliği değerlendirebilmeniz için bu veri kümesine ilişkin olasılıklara de döner.  

### <a name="how-cross-validation-works"></a>Çapraz doğrulamanın nasıl çalıştığı

1. Çapraz doğrulama, eğitim verilerini rastgele katlara ayırır. 

   Daha önce veri kümesini bölümlendirdiyseniz, algoritma varsayılan olarak 10 katmaz. Veri kümesini farklı sayıda katlara bölmek için [bölüm ve örnek](partition-and-sample.md) modülünü kullanabilir ve kaç tane katın kullanılacağını belirtebilirsiniz.  

2.  Modül, doğrulama için kullanmak üzere verileri 1 kat olarak belirler. (Buna bazen *gizleme katlama*denir.) Modül, bir modeli eğitmek için kalan katları kullanır. 

    Örneğin, beş katlama oluşturursanız modül çapraz doğrulama sırasında beş model oluşturur. Modül her modeli, verilerin dört beş bir kısmını kullanarak ilerleder. Her modeli kalan bir beşinci test eder.  

3.  Her katlama için modelin testi sırasında, modül birden çok doğruluk istatistiğini değerlendirir. Modülün kullandığı istatistik, değerlendirdiğiniz modelin türüne bağlıdır. Farklı istatistikler, sınıflandırma modellerini ve gerileme modellerini değerlendirmek için kullanılır.  

4.  Oluşturma ve değerlendirme işlemi tüm katlara tamamlandığında, çapraz doğrulama modeli bir dizi performans ölçümü oluşturur ve tüm veriler için sonuç elde edilir. Bir tek katın yüksek veya düşük doğrulukta olup olmadığını görmek için bu ölçümleri gözden geçirin. 

### <a name="advantages-of-cross-validation"></a>Çapraz doğrulamanın avantajları

Bir modeli değerlendirirken farklı ve yaygın bir yolu, verileri [bölünmüş verileri](split-data.md)kullanarak bir eğitime ve test kümesine bölmektir ve ardından eğitim verilerinde modeli doğrular. Ancak çapraz doğrulama bazı avantajlar sunar:  

-   Çapraz doğrulama daha fazla test verisi kullanır.

    Çapraz doğrulama, modelin performansını daha büyük bir veri alanında belirtilen parametrelerle ölçer. Diğer bir deyişle, çapraz doğrulama bir bölüm yerine hem eğitim hem de değerlendirme için tüm eğitim veri kümesini kullanır. Buna karşılık, bir modeli rastgele bir bölünmeden üretilen verileri kullanarak doğrularsınız, genellikle modeli yalnızca yüzde 30 veya daha az kullanılabilir veri üzerinde değerlendirdiğiniz bir.  

    Ancak, çapraz doğrulama, modeli daha büyük bir veri kümesi üzerinden birden çok kez doğruladığından, daha fazla hesaplama yoğunluğu vardır. Rastgele bir bölme üzerinde doğrulamadan çok daha uzun sürer.  

-   Çapraz doğrulama hem veri kümesini hem de modeli değerlendirir.

    Çapraz doğrulama, bir modelin doğruluğunu ölçmez. Ayrıca, veri kümesinin temsilcisinden ne kadar önemli olduğunu ve modelin verilerdeki farklılıklara ne kadar duyarlı olabileceğini de sağlar.  

## <a name="how-to-use-cross-validate-model"></a>Çapraz doğrulama modelini kullanma

Veri kümeniz büyükse, çapraz doğrulamanın çalışması uzun zaman alabilir.  Bu nedenle, modelinizi oluşturma ve test etme başlangıç aşamasında çapraz doğrulama modelini kullanabilirsiniz. Bu aşamada, model parametrelerinin iyiyiyiyi (hesaplama zamanının toleranable olduğu varsayıldığında) değerlendirebilirsiniz. Daha sonra modeli [eğitme](train-model.md) modeli ile sağlanan parametreleri kullanarak modelinizi eğleyebilir ve değerlendirebilirsiniz ve model modüllerini [değerlendirin](evaluate-model.md) .

Bu senaryoda, çapraz doğrulama modelini kullanarak modeli eğitetin ve test edersiniz.

1. Ardışık düzene çapraz Validate model modülünü ekleyin. Bunu Azure Machine Learning tasarımcısında bulabilirsiniz, **model puanlama & değerlendirme** kategorisinde bulabilirsiniz. 

2. Herhangi bir sınıflandırma veya regresyon modelinin çıkışını bağlayın. 

    Örneğin, sınıflandırma için **Iki sınıf Bayes noktası makinesi** kullanıyorsanız, modeli istediğiniz parametrelerle yapılandırın. Ardından, sınıflandırıcının **eğitilen model** bağlantı noktasındaki bağlayıcıyı çapraz doğrulama modelinin eşleşen bağlantı noktasına sürükleyin. 

    > [!TIP] 
    > Çapraz doğrulama modeli, değerlendirmenin bir parçası olarak modeli otomatik olarak yaptığından modeli eğmenize gerek kalmaz.  
3.  Çapraz doğrulama modelinin **veri kümesi** bağlantı noktasındaki tüm etiketli eğitim veri kümelerini bağlayın.  

4.  Çapraz doğrulama modelinin **Özellikler** bölmesinde, **sütun seçiciyi Başlat**' ı seçin. Sınıf etiketini veya tahmin edilebilir değeri içeren tek bir sütun seçin. 

5. Aynı verilerdeki art arda çalıştırılan çalıştırmalar arasında çapraz doğrulama sonuçlarını yinelemek istiyorsanız **rastgele çekirdek** parametresi için bir değer ayarlayın.  

6. İşlem hattını çalıştırma.

7. Raporların açıklaması için [sonuçlar](#results) bölümüne bakın.

    Modelin daha sonra yeniden kullanmak üzere bir kopyasını almak için, algoritmayı içeren modülün çıktısına sağ tıklayın (örneğin, **Iki sınıf Bayes noktası makinesi**). Ardından **eğitilen model olarak kaydet**' i seçin.

## <a name="results"></a>Sonuçlar

Tüm yinelemeler tamamlandıktan sonra, çapraz doğrulama modeli, tüm veri kümesinin puanlarını oluşturur. Ayrıca, modelin kalitesini değerlendirmek için kullanabileceğiniz performans ölçümleri de oluşturur.

### <a name="scored-results"></a>Puanlanmış sonuçlar

Modülün ilk çıktısı, bazı tahmin edilen değerler ve ilgili olasılıkların yanı sıra her satır için kaynak verileri sağlar. 

Bu sonuçları görüntülemek için, işlem hattında çapraz doğrulama modeli modülüne sağ tıklayın. **Puanlanmış sonuçları**seçin ve ardından **Görselleştir**' i seçin.

| Yeni sütun adı      | Açıklama                              |
| -------------------- | ---------------------------------------- |
| Puanlanmış Etiketler        | Bu sütun, veri kümesinin sonuna eklenir. Her satır için tahmin edilen değeri içerir. |
| Puanlanmış olasılıklara | Bu sütun, veri kümesinin sonuna eklenir. Değerin, **puanlanmış etiketlerde**tahmini olasılığını gösterir. |
| Katlama sayısı          | Çapraz doğrulama sırasında her bir veri satırının atandığı katın sıfır tabanlı dizinini gösterir. |

 ### <a name="evaluation-results"></a>Değerlendirme sonuçları

İkinci rapor, katlara göre gruplandırılır. Yürütme sırasında, çapraz Validate modelinin eğitim verilerini *n* katlara (varsayılan olarak, 10) rastgele bir şekilde ayırır. Veri kümesinin her yinelemesinde, çapraz doğrulama modeli, doğrulama veri kümesi olarak bir katlama kullanır. Bir modeli eğitmek için kalan *n-1* katlarını kullanır. *N* modellerinin her biri, tüm diğer katların verilerinde test edilir.

Bu raporda, katların dizin değeri, artan sırada listelenir.  Diğer herhangi bir sütunu sıralamak için sonuçları bir veri kümesi olarak kaydedebilirsiniz.

Bu sonuçları görüntülemek için, işlem hattında çapraz doğrulama modeli modülüne sağ tıklayın. **Değerlendirme sonuçlarını katlayarak**seçin ve ardından **Görselleştir**' i seçin.


|Sütun adı| Açıklama|
|----|----|
|Katlama sayısı| Her katlama için bir tanımlayıcı. Beş katlama oluşturduysanız, 0 ile 4 arasında bir beş veri kümesi vardır.
|Katdaki örneklerin sayısı|Her kata atanan satır sayısı. Bunlar kabaca eşit olmalıdır. |


Modül, değerlendirdiğiniz modelin türüne bağlı olarak her katlama için aşağıdaki ölçümleri de içerir: 

+ **Sınıflandırma modelleri**: Precision, geri çek, F PUANı, AUC, doğruluk  

+ **Regresyon modelleri**: Ortalama mutlak hata, kök ortalama kare hatası, göreli mutlak hata, göreli kare hatası ve belirleme katsayısı


## <a name="technical-notes"></a>Teknik notlar  

+ Bunları çapraz doğrulama için kullanmadan önce veri kümelerini normalleştirmek en iyi uygulamadır. 

+ Çapraz doğrulama modeli, daha fazla hesaplama açısından çok daha yoğun ve rastgele bölünmüş bir veri kümesi kullanarak modeli doğruladıysanız daha uzun sürer. Bunun nedeni, modelin çapraz doğrulama modelinin, modeli birden çok kez izleme ve doğrulama nedenidir.

+ Modelin doğruluğunu ölçmek için çapraz doğrulamayı kullandığınızda veri kümesini eğitim ve test kümelerine bölmeniz gerekmez. 


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 

