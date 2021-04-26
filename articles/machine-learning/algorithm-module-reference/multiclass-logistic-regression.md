---
title: 'Birden çok Lass Lojistik gerileme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Birden fazla değeri tahmin etmek için Azure Machine Learning tasarımcısında çok Lass lojistik regresyon modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420742"
---
# <a name="multiclass-logistic-regression-module"></a>Birden çok Lass lojistik regresyon modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Birden çok değeri tahmin etmek için kullanılabilecek bir lojistik regresyon modeli oluşturmak için bu modülü kullanın.

Lojistik regresyon kullanan sınıflandırma denetimli bir öğrenme yöntemidir ve bu nedenle etiketli bir veri kümesi gerektirir. Modeli ve etiketli veri kümesini model [eğitme](./train-model.md)gibi bir modüle giriş olarak sağlayarak modeli eğitebilirsiniz. Eğitilen model daha sonra yeni giriş örneklerine ilişkin değerleri tahmin etmek için kullanılabilir.

Azure Machine Learning Ayrıca, ikili veya dictom değişkenlerinin sınıflandırmasına uygun olan [Iki sınıf lojistik regresyon](./two-class-logistic-regression.md) modülünü de sağlar.

## <a name="about-multiclass-logistic-regression"></a>Birden çok Lass Lojistik gerileme hakkında

Lojistik regresyon, bir sonucun olasılığını tahmin etmek için kullanılan ve Sınıflandırma görevleri için popüler olan istatistiklerdeki iyi bilinen bir yöntemdir. Algoritma bir lojistik işlevine veri ekleyerek bir olayın oluşma olasılığını tahmin eder. 

Birden çok Lass lojistik regresyonda, sınıflandırıcı birden fazla sonucu tahmin etmek için kullanılabilir.

## <a name="configure-a-multiclass-logistic-regression"></a>Birden çok Lass lojistik gerilemesini yapılandırma

1. Birden çok **Lass lojistik regresyon** modülünü ardışık düzene ekleyin.

2. Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.

    + **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız ve bağımsız değişken olarak belirli bir değer kümesi sağlamak için bu seçeneği kullanın.

    + **Parametre aralığı**: en iyi parametrelerden emin değilseniz ve bir parametre süpürme çalıştırmak istiyorsanız bu seçeneği belirleyin. Yinelemek için bir değer aralığı seçin ve [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) , en iyi sonuçları üreten hiper parametreleri belirlemek için, belirttiğiniz ayarların tüm olası birleşimlerinin üzerinde yinelenir.  

3. **İyileştirme toleransı**, iyileştirici yakınsama için eşik değerini belirtin. Yinelemeler arasındaki iyileştirme eşikten küçükse, algoritma duraklar ve geçerli modeli döndürür.

4. **L1 düzenleme Weight**, **L2 düzenleme Weight**: düzenleme parametreleri L1 ve L2 için kullanılacak bir değer yazın. Her ikisi için sıfır olmayan bir değer önerilir.

    Düzenleme, çok büyük katsayı değerleriyle penalizing modellerle fazla sığdırmayı engellemek için bir yöntemdir. Düzenleme, katsayı değeri ile ilişkili ceza değerini, varsayım hatası ile ilişkilendirilen ceza ekleyerek işe yarar. Aşırı katsayı değeri olan doğru bir model daha fazla olabilir, ancak daha fazla koruyucu değere sahip daha az doğru bir model daha az olabilir.

     L1 ve L2 düzenleme farklı etkileri ve kullanımları vardır. L1, yüksek boyutlu verilerle çalışırken yararlı olabilecek seyrek modellere uygulanabilir. Buna karşılık L2 düzenleme, seyrek olmayan veriler için tercih edilir.  Bu algoritma, L1 ve L2 düzenleme değerlerinin doğrusal bir birleşimini destekler: Yani, `x = L1` ve `y = L2` , `ax + by = c` düzenleme koşullarının doğrusal yayılımını tanımlar.

     L1 ve L2 koşullarına ait farklı doğrusal birleşimler, [elastik net düzenleme](https://wikipedia.org/wiki/Elastic_net_regularization)gibi lojistik regresyon modelleriyle kaldırılmıştır.

6. **Rastgele sayı çekirdek**: sonuçların çalıştırmalar üzerinde tekrarlanabilir olmasını istiyorsanız, algoritma için çekirdek olarak kullanılacak bir tamsayı değeri yazın. Aksi takdirde, aynı işlem hattının çalıştırılmasıyla biraz farklı sonuçlar üretebilen çekirdek olarak bir sistem saati değeri kullanılır.

8. Etiketli bir veri kümesini bağlayın ve modeli eğitme:

    + **Tek parametre** için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](train-model.md) modülünü bağlayın.  
  
    + **Parametre aralığına** **oluşturma** , bir etiketli veri kümesini bağlama ve modeli [Ayarla hiper parametrelerini](tune-model-hyperparameters.md)kullanarak modeli eğitme.  
  
    > [!NOTE]
    > 
    > [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, tek parametre listesindeki yalnızca varsayılan değeri kullanır.  
    > 
    > Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.  
    > 
    > **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.

9. İşlem hattını gönderme.



## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 