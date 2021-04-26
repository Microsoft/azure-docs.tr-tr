---
title: 'Hızlı orman Quantile gerileme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Hızlı orman Quantile regresyon modülünü kullanarak, belirtilen sayıda ölçü için değerleri tahmin edebilen bir regresyon modeli oluşturma hakkında bilgi edinin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6d59d2fabb0b74ef12e33f55467b0ba68e0b7386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90907963"
---
# <a name="fast-forest-quantile-regression"></a>Hızlı Orman Dağılım Dilimi Regresyonu

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Bir işlem hattındaki hızlı orman quantile regresyon modeli oluşturmak için bu modülü kullanın. Hızlı orman quantile gerileme, tek bir ortalama tahmin değeri almak yerine tahmin edilen değerin dağılımı hakkında daha fazla bilgi edinmek istiyorsanız faydalıdır. Bu yöntemde aşağıdakiler dahil birçok uygulama vardır:  
  
- Fiyatları tahmin etme  
  
- Alt geliştirmeyi değerlendirmek için öğrenci performansı tahmini veya büyüme grafikleri uygulama  
  
- Değişkenler arasında yalnızca zayıf bir ilişki olduğu durumlarda tahmine dayalı ilişkiler bulma  
  
Bu regresyon algoritması **denetimli** bir öğrenme yöntemidir ve bu, etiket sütunu içeren etiketli bir veri kümesi gerektirdiği anlamına gelir. Bir gerileme algoritması olduğundan, etiket sütunu yalnızca sayısal değerler içermelidir.

## <a name="more-about-quantile-regression"></a>Quantile gerileme hakkında daha fazla bilgi

Birçok farklı gerileme türü vardır. Yalnızca put, regresyon, bir modeli sayısal bir vektör olarak ifade edilen hedefe ekleme anlamına gelir. Ancak İstatistikçilerin, gerileme için giderek daha fazla gelişmiş yöntem geliştirmiştir.

*Quantile* 'nın en basit tanımı, bir veri kümesini eşit boyutlu gruplara ayıran bir değerdir; Bu nedenle, quantile değerleri gruplar arasındaki sınırları işaretler. İstatistiksel olarak konuşmak, quantiles, rastgele bir değişkenin kümülatif dağıtım işlevinin (CDF) tersidir düzenli aralıklarla gerçekleştirilen değerlerdir.

Doğrusal regresyon modelleri tek bir tahmin kullanarak sayısal bir değişkenin değerini tahmin etmeye çalışır, *Ortalama*, bazen aralığın veya hedef değişkenin tüm dağıtımının tahmin edilmesi gerekir. Bu amaçla Bayeme gerileme ve quantile gerileme gibi teknikler geliştirilmiştir.

Quantile gerileme, tahmin edilen değerin dağıtımını anlamanıza yardımcı olur. Bu modülde kullanılan gibi ağaç tabanlı quantile regresyon modelleri, parametrik olmayan dağıtımları tahmin etmek için kullanılabilecekleri ek avantaja sahip olur.

  
## <a name="how-to-configure-fast-forest-quantile-regression"></a>Hızlı orman Quantile gerilemesini yapılandırma

1. Tasarımcı 'daki işlem hattınıza **hızlı orman Quantile regresyon** modülünü ekleyin. Bu modülü, **regresyon** kategorisinde **Machine Learning algoritmalarda** bulabilirsiniz.

2. **Hızlı orman Quantile regresyon** modülünün sağ bölmesinde, model **oluşturma modu** seçeneğini ayarlayarak modelin nasıl eğitilmesini istediğinizi belirtin.  
  
    - **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayın. Modeli eğitedığınızda [modeli eğitme](train-model.md)' yi kullanın.
  
    - **Parametre aralığı**: en iyi parametrelerden emin değilseniz, [model hiper parametreleri ayarla](tune-model-hyperparameters.md) modülünü kullanarak bir parametre tarama işlemi yapın. En iyi yapılandırmayı bulmak için, eğitmen, belirttiğiniz birden çok değer üzerinde yinelenir.

3. **Ağaç sayısı**' nda, en yüksek ağaç sayısını yazın. Daha fazla ağaç oluşturursanız, genellikle daha uzun eğitim süresi masrafında daha fazla doğruluk doğurur.  

4. **Yaprakları sayısı**, herhangi bir ağaçta oluşturulabilecek en fazla yaprakları veya Terminal düğümlerini yazın.  

5. **Bir yaprak oluşturmak için gereken en az eğitim örneği sayısı**, bir ağaçta herhangi bir Terminal düğümü (yaprak) oluşturmak için gereken en az örnek sayısını belirtin.  
  
     Bu değeri artırarak, yeni kurallar oluşturma eşiğini artırırsınız. Örneğin, varsayılan 1 değeri ile tek bir durum bile yeni bir kuralın oluşturulmasına neden olabilir. Değeri 5 ' e artırırsanız eğitim verilerinin aynı koşulları karşılayan en az 5 durum içermesi gerekir.

6. Her bir ölçü grubu oluşturulurken kullanılacak örneklerin kesirlerini temsil eden 0 ile 1 arasında bir sayı belirtin. Örnekler, değiştirme ile rastgele seçilir.

7. **Bölme kesri**, ağacın her bölünmesi içinde kullanılacak özelliklerin kesirini temsil eden 0 ile 1 arasında bir sayı yazın. Kullanılan özellikler her zaman rastgele seçilir.

8. **Tahmin edilecek quantiles, modelin eğmesini** ve tahmin oluşturmasını istediğiniz quantiles 'in noktalı virgülle ayrılmış bir listesini yazın.
  
     Örneğin, kuvaryansı tahmin eden bir model oluşturmak istiyorsanız, yazmanız gerekir `0.25; 0.5; 0.75` .  

9. İsteğe bağlı olarak, model tarafından kullanılan rastgele sayı oluşturucusunun çekirdek olması için **rastgele sayı temel** değeri için bir değer yazın.  Varsayılan değer 0 ' dır, yani rastgele bir çekirdek seçilir.
  
     Aynı verilerdeki art arda yapılan çalışmalarla ilgili sonuçları yeniden oluşturmanız gerekiyorsa bir değer sağlamalısınız.  

10. Eğitim veri kümesini ve eğitilen modeli eğitim modülleriyle birine bağlayın: 

    - **Tek parametreye** oluşturma ve ayarlama **modunu** ayarlarsanız, [model eğitme](train-model.md) modülünü kullanın.

    - **Parametre aralığına** **oluşturma** sağlayan bir mod ayarlarsanız, [model hiper parametrelerini ayarla](tune-model-hyperparameters.md) modülünü kullanın.

    > [!WARNING]
    > 
    > - [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, parametre aralığı listesindeki yalnızca ilk değeri kullanır.
    > 
    > - Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.
    > 
    > - **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.

11. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Eğitilen modelin anlık görüntüsünü kaydetmek için eğitim modülünü seçin ve sağ paneldeki **çıktılar + Günlükler** sekmesine geçin. Simge **yazmaç veri kümesine** tıklayın.  Kaydedilmiş modeli modül ağacında bir modül olarak bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.
