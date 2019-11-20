---
title: 'Birden çok Lass Lojistik gerileme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Birden fazla değeri tahmin etmek için kullanılabilecek bir lojistik regresyon modeli oluşturmak için Azure Machine Learning içinde çok Lass lojistik regresyon modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 9d6e6c11f0e2a8fb7e08385f7abf26fb4a8d21c8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465971"
---
# <a name="multiclass-logistic-regression-module"></a>Birden çok Lass lojistik regresyon modülü

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

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

    + **Parametre aralığı**: en iyi parametrelerden emin değilseniz ve bir parametre tarama kullanmak istiyorsanız bu seçeneği kullanın.

3. **İyileştirme toleransı**, iyileştirici yakınsama için eşik değerini belirtin. Yinelemeler arasındaki iyileştirme eşikten küçükse, algoritma duraklar ve geçerli modeli döndürür.

4. **L1 düzenleme Weight**, **L2 düzenleme Weight**: düzenleme parametreleri L1 ve L2 için kullanılacak bir değer yazın. Her ikisi için sıfır olmayan bir değer önerilir.

    Düzenleme, çok büyük katsayı değerleriyle penalizing modellerle fazla sığdırmayı engellemek için bir yöntemdir. Düzenleme, katsayı değeri ile ilişkili ceza değerini, varsayım hatası ile ilişkilendirilen ceza ekleyerek işe yarar. Aşırı katsayı değeri olan doğru bir model daha fazla olabilir, ancak daha fazla koruyucu değere sahip daha az doğru bir model daha az olabilir.

     L1 ve L2 düzenleme farklı etkileri ve kullanımları vardır. L1, yüksek boyutlu verilerle çalışırken yararlı olabilecek seyrek modellere uygulanabilir. Buna karşılık L2 düzenleme, seyrek olmayan veriler için tercih edilir.  Bu algoritma, L1 ve L2 düzenleme değerlerinin doğrusal bir birleşimini destekler: Yani, `x = L1` ve `y = L2`, `ax + by = c` düzenleme koşullarının doğrusal yayılımını tanımlar.

     L1 ve L2 koşullarına ait farklı doğrusal birleşimler, [elastik net düzenleme](https://wikipedia.org/wiki/Elastic_net_regularization)gibi lojistik regresyon modelleriyle kaldırılmıştır.

6. **Rastgele sayı çekirdek**: sonuçların çalıştırmalar üzerinde tekrarlanabilir olmasını istiyorsanız, algoritma için çekirdek olarak kullanılacak bir tamsayı değeri yazın. Aksi takdirde, aynı işlem hattının çalıştırılmasıyla biraz farklı sonuçlar üretebilen çekirdek olarak bir sistem saati değeri kullanılır.

8. Etiketli bir veri kümesini ve eğitme modüllerinden birini bağlayın:

    + **Tek parametreye**oluşturma ve ayarlama **modunu** ayarlarsanız, [model eğitme](./train-model.md) modülünü kullanın.

9. İşlem hattını çalıştırma.

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra, modellerden öğrenilen Özellik ağırlıklarından [, model oluşturma modülünün çıktısına](./train-model.md) sağ tıklayıp **Görselleştir**' i seçerek modelin parametrelerinin bir özetini görebilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 