---
title: 'Öneren değerlendir: modül başvurusu'
titleSuffix: Azure Machine Learning service
description: Öneren model tahminlerinin doğruluğunu değerlendirmek için Azure Machine Learning hizmetindeki değerlendir öneren modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 9a80fce04aa939895d1dc9572714046d9203bad7
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717163"
---
# <a name="evaluate-recommender"></a>Öneren değerlendir

Bu makalede Azure Machine Learning tasarımcısında değerlendir öneren modülünün nasıl kullanılacağı açıklanmaktadır (Önizleme). Amaç, bir öneri modelinin yaptığı tahminlerin doğruluğunu ölçmektir. Bu modülü kullanarak, farklı öneri türlerini değerlendirebilirsiniz:  
  
-   Bir Kullanıcı ve bir öğe için tahmin edilen derecelendirmeler    
-   Bir kullanıcı için önerilen öğeler  
  
Öneri modeli kullanarak tahmin oluştururken, bu desteklenen tahmin türlerinin her biri için biraz farklı sonuçlar döndürülür. Değerlendirin öneren modülü, puanlanmış veri kümesinin sütun biçiminden tahmin türünü çıkarır. Örneğin, puanlanmış veri kümesi şunları içerebilir:

- Kullanıcı-öğe-derecelendirme Üçlü
- Kullanıcılar ve bunların önerilen öğeleri

Modül, yapılan tahmin türüne göre uygun performans ölçümlerini de uygular. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Öneren değerlendir yapılandırma

Öneren değerlendir modülü, karşılık gelen "temel olan" verilerle bir öneri modeli kullanarak tahmin çıktısını karşılaştırır. Örneğin, [SVD öneren modülünün puanı](score-svd-recommender.md) , öneren değerlendir kullanarak çözümleyebileceğiniz puanlanmış veri kümeleri üretir.

### <a name="requirements"></a>Gereksinimler

Değerlendirme öneren, aşağıdaki veri kümelerini giriş olarak gerektirir. 
  
#### <a name="test-dataset"></a>Test veri kümesi

Test veri kümesi, Kullanıcı-öğe derecelendirme Üçlü biçiminde "toprak" verileri içerir.  

#### <a name="scored-dataset"></a>Puanlanmış veri kümesi

Puanlanmış veri kümesi, öneri modelinin ürettiği tahminleri içerir.  
  
Bu ikinci veri kümesindeki sütunlar, Puanlama işlemi sırasında gerçekleştirdiğiniz tahmin türüne bağlıdır. Örneğin, puanlanmış veri kümesi aşağıdakilerden birini içerebilir:

- Kullanıcılar, öğeler ve kullanıcının öğe için büyük olasılıkla sahip olacağı derecelendirmeler
- Kendileri için önerilen kullanıcıların ve öğelerin listesi 

### <a name="metrics"></a>Ölçümler

Model için performans ölçümleri, giriş türüne göre oluşturulur. Aşağıdaki bölümlerde ayrıntılar verilmektedir.

## <a name="evaluate-predicted-ratings"></a>Tahmin edilen derecelendirmeleri değerlendir  

Tahmin edilen derecelendirmeleri değerlendirirken, puanlanmış veri kümesi (öneren değerlendirmek için ikinci giriş), bu gereksinimleri karşılayan Kullanıcı-öğe derecelendirme engelleri içermelidir:
  
-   Veri kümesinin ilk sütunu Kullanıcı tanımlayıcılarını içerir.    
-   İkinci sütunda öğe tanımlayıcıları bulunur.  
-   Üçüncü sütun karşılık gelen Kullanıcı öğesi derecelendirmelerini içerir.  
  
> [!IMPORTANT] 
> Değerlendirmenin başarılı olması için, sütun adlarının sırasıyla `User`, `Item`ve `Rating`olması gerekir.  
  
Değerlendirin öneren, "zemin gerçeği" veri kümesindeki derecelendirmeleri, puanlanmış veri kümesinin öngörülen derecelendirmelerine göre karşılaştırır. Daha sonra ortalama mutlak hata (MAE) ve kök ortalama kare hatası (rmo) hesaplar.



## <a name="evaluate-item-recommendations"></a>Öğe önerilerini değerlendir

Öğe önerilerini değerlendirirken, her kullanıcı için önerilen öğeleri içeren bir puanlanmış veri kümesi kullanın:
  
-   Veri kümesinin ilk sütununun Kullanıcı tanımlayıcısı içermesi gerekir.    
-   Sonraki tüm sütunlar, ilgili önerilen öğe tanımlayıcılarını içermesi gerekir ve ilgili bir öğenin kullanıcıya ait olduğu şekilde sıralanır. 

Bu veri kümesini bağlanmadan önce, en ilgili öğelerin ilk olması için veri kümesini sıralamanıza önerilir.  

> [!IMPORTANT] 
> Öneren 'ın çalışmasını değerlendirmek için, sütun adlarının `User`, `Item 1`, `Item 2`, `Item 3` ve benzeri olması gerekir.  
  
Öneren değerlendir, ortalama normalleştirilmiş indirimli kümülatif kazancı (NDCG) hesaplar ve bunu çıkış veri kümesinde döndürür.  
  
Önerilen öğeler için gerçek "zemin gerçeği" özelliğini bildirmek imkansız olduğundan, öneren değerlendirin, test veri kümesindeki Kullanıcı öğesi derecelendirmelerini NDCG hesaplamasında kazanç olarak kullanır. Değerlendirmek için, öneren Puanlama modülünün yalnızca "temel doğru" derecelendirmelere (test veri kümesinde) sahip öğeler için öneriler üretmesi gerekir.  
  

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
