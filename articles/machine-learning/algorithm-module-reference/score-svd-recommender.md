---
title: 'Skor SVD öneren: modül başvurusu'
titleSuffix: Azure Machine Learning service
description: Bir veri kümesi için öneri tahminleri için Azure Machine Learning hizmetinde, SVD öneren modülünün Puanını nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: be590ba04de5c8ee671e7e865d04b4ea6c638c00
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717228"
---
# <a name="score-svd-recommender"></a>Skor SVD öneren

Bu makalede Azure Machine Learning tasarımcısında (Önizleme), SVD öneren modülünün Puanını kullanma açıklanmaktadır. Tek değer ayrıştırma (SVD) algoritmasını temel alan eğitilen bir öneri modeli kullanarak tahmin oluşturmak için bu modülü kullanın.

SVD öneren iki farklı tahmin türü oluşturabilir:

- [Belirli bir Kullanıcı ve öğe için derecelendirmeleri tahmin etme](#prediction-of-ratings)
- [Kullanıcılara öğe önerme](#recommendations-for-users)

İkinci tahmine dayalı tür oluştururken, şu modlardan birinde işlem yapabilirsiniz:

- **Üretim modu** tüm kullanıcıları veya öğeleri dikkate alır. Genellikle bir Web hizmetinde kullanılır.

  Yalnızca eğitim sırasında görülen kullanıcıları değil, yeni kullanıcılar için puanlar oluşturabilirsiniz. Daha fazla bilgi için [Teknik notlara](#technical-notes)bakın. 

- **Değerlendirme modu** , azaltılan bir kullanıcı veya öğe kümesi üzerinde çalışır. Genellikle işlem hattı işlemleri sırasında kullanılır.

SVD öneren algoritması hakkında daha fazla bilgi için bkz. [öneren Systems için](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf)Araştırma sayfası matrisi, Teknik İnceleme teknikleri.

## <a name="how-to-configure-score-svd-recommender"></a>Skor SVD öneren 'ı yapılandırma

Bu modül, her biri farklı gereksinimlere sahip iki tür tahmin destekler. 

###  <a name="prediction-of-ratings"></a>Derecelendirmelerin tahmini

Derecelendirmeleri tahmin ettiğinizde, model kullanıcının eğitim verileri verildiğinde belirli bir öğeye nasıl tepki verdiğini hesaplar. Puanlama için giriş verilerinde hem Kullanıcı hem de bir öğe sağlanmalıdır.

1. İşlem hattınızı eğitimli bir öneri modeli ekleyin ve **eğitilen SVD öneren**'e bağlayın. Modeli, [tren SVD öneren](train-SVD-recommender.md) modülünü kullanarak oluşturmanız gerekir.

2. **Öneren tahmin türü**Için, **Derecelendirme tahmini**' ni seçin. Başka parametre gerekmez.

3. Tahmine dayalı hale getirmek istediğiniz verileri ekleyin ve **skor olarak veri kümesine**bağlayın.

   Modelin derecelendirmeleri tahmin edebilmesi için, giriş veri kümesinin Kullanıcı-öğe çiftleri içermesi gerekir.

   Veri kümesi, birinci ve ikinci sütunlardaki Kullanıcı öğesi çifti için isteğe bağlı üçüncü bir derecelendirme sütunu içerebilir. Ancak, üçüncü sütun tahmin sırasında yok sayılır.

4. İşlem hattını çalıştırma.

### <a name="results-for-rating-predictions"></a>Değerlendirme tahminleri sonuçları 

Çıktı veri kümesi üç sütun içerir: kullanıcılar, öğeler ve her giriş kullanıcısı ve öğesi için tahmin edilen derecelendirme.

###  <a name="recommendations-for-users"></a>Kullanıcılar için öneriler 

Kullanıcılara yönelik öğeleri önermek için, giriş olarak Kullanıcı ve öğe listesi sağlarsınız. Bu verilerden, model varolan öğeler ve kullanıcılar hakkında bilgisini kullanarak her bir kullanıcıya çok daha fazla bilgi sahibi olan öğelerin bir listesini oluşturur. Döndürülen önerilerin sayısını özelleştirebilirsiniz. Ve bir öneri oluşturmak için gereken önceki önerilerin sayısı için bir eşik belirleyebilirsiniz.

1. İşlem hattınızı eğitimli bir öneri modeli ekleyin ve **eğitilen SVD öneren**'e bağlayın.  Modeli, [tren SVD öneren](train-svd-recommender.md) modülünü kullanarak oluşturmanız gerekir.

2. Bir kullanıcı listesi için öğeleri önermek için, **öneren tahmin türünü** **öğe önerisine**ayarlayın.

3. **Önerilen öğe seçimi**için üretimde veya model değerlendirmesi için Puanlama modülünü kullanıp kullanmayacağınızı belirtin. Şu değerlerden birini seçin:

    - **Tüm öğeler**: bir Web hizmetinde veya üretimde kullanmak üzere bir işlem hattı ayarlıyorsanız bu seçeneği belirleyin.  Bu seçenek *üretim moduna*izin vermez. Modül, eğitim sırasında görülen tüm öğelerden öneriler sağlar.

    - **Derecelendirilen öğelerden (model değerlendirmesi için)** : bir modeli geliştirmekte veya test ediyorsanız bu seçeneği belirleyin. Bu seçenek *değerlendirme modunu*sunar. Modül, yalnızca giriş veri kümesindeki öğelerin derecelendirilmesine yönelik öneriler sağlar.
    
    - **Derecelendirilmemiş öğelerden (kullanıcılara yeni öğeler önermek için)** : modülün yalnızca eğitim veri kümesindeki öğelerin derecelendirilmemiş olan öğelerden öneriler yapmasını istiyorsanız bu seçeneği belirleyin. 

4. Tahmin yapmak istediğiniz veri kümesini ekleyin ve **skor Için veri kümesine**bağlayın.

    - **Tüm öğeler**için, giriş veri kümesi bir sütundan oluşmalıdır. Önerilerde bulunan kullanıcıların tanımlayıcılarını içerir.

      Veri kümesi, öğe tanımlayıcılarının ve derecelendirmelerin fazladan iki sütununu içerebilir, ancak bu iki sütun yok sayılır. 

    - **Derecelendirilen öğeler için (model değerlendirmesi için)** , giriş veri kümesi Kullanıcı-öğe çiftlerinden oluşmalıdır. İlk sütun kullanıcı tanımlayıcısını içermelidir. İkinci sütun karşılık gelen öğe tanımlayıcılarını içermelidir.

      Veri kümesi, Kullanıcı-öğe derecelendirmelerinin üçüncü sütununu içerebilir, ancak bu sütun yok sayılır.

    - **Derecelendirilmemiş öğelerden (kullanıcılara yeni öğeler önermek için)** , giriş veri kümesi, Kullanıcı-öğe çiftlerinden oluşmalıdır. İlk sütun kullanıcı tanımlayıcısını içermelidir. İkinci sütun karşılık gelen öğe tanımlayıcılarını içermelidir.

     Veri kümesi, Kullanıcı-öğe derecelendirmelerinin üçüncü sütununu içerebilir, ancak bu sütun yok sayılır.

5. **Bir kullanıcı için önerilen en fazla öğe sayısı**: her bir kullanıcı için döndürülecek öğe sayısını girin. Varsayılan olarak, modül beş öğe önerir.

6. **Kullanıcı başına öneri havuzunun en küçük boyutu**: önceki önerilerin kaç tane gerektiğini belirten bir değer girin. Varsayılan olarak, bu parametre **2**olarak ayarlanır; Yani, en az iki kullanıcının öğeyi önerdiği anlamına gelir.

   Bu seçeneği yalnızca değerlendirme modunda puansanız kullanın. **Tüm öğeler** veya **derecelendirilmemiş öğelerden (kullanıcılara yeni öğeler önermek için)** seçeneğini belirlerseniz seçeneği kullanılamaz.

7.  **Derecelendirilmemiş öğelerin (kullanıcılara yeni öğeler önermek için)** için, tahmin sonuçlarından zaten derecelendirilmiş öğeleri kaldırmak Için **eğitim verileri**adlı üçüncü giriş bağlantı noktasını kullanın.

    Bu filtreyi uygulamak için, özgün eğitim veri kümesini giriş bağlantı noktasına bağlayın.

8. İşlem hattını çalıştırma.

### <a name="results-of-item-recommendation"></a>Öğe önerisi sonuçları

Skor SVD öneren tarafından döndürülen puanlanmış veri kümesi, her kullanıcı için önerilen öğeleri listeler:

- İlk sütunda kullanıcı tanımlayıcıları bulunur.
- **Bir kullanıcı için önerilen en fazla öğe sayısı**için ayarladığınız değere bağlı olarak, bir dizi ek sütun oluşturulur. Her sütunda önerilen bir öğe (tanımlayıcıya göre) bulunur. Öneriler, Kullanıcı öğesi benzeşimi tarafından sıralanır. En yüksek benzeşimi olan öğe, sütun **öğesi 1**' de konur.

> [!WARNING]
> Bu puanlanmış veri kümesini [değerlendir öneren](evaluate-recommender.md) modülünü kullanarak değerlendiremiyoruz.


##  <a name="technical-notes"></a>Teknik notlar

SVD öneren ile bir işlem hattına sahipseniz ve modeli üretime taşırsanız, değerlendirme modunda öneren kullanma ve üretim modunda kullanma arasında önemli farklar olduğunu unutmayın.

Tanıma göre değerlendirme, bir test kümesindeki *taban gerçeği* için doğrulanamayan tahminleri gerektirir. Öneren değerlendirirken, yalnızca test kümesinde derecelendirilen öğeleri tahmin etmelidir. Bu, tahmin edilen olası değerleri kısıtlar.

Modeli çalıştırdığınızda, en iyi öngörülere ulaşmak için genellikle tahmin modunu, olası tüm öğelere göre öneriler olacak şekilde değiştirirsiniz. Bu tahminlerden birçoğu için ilgili hiçbir taban gerçeği yoktur. Bu nedenle, işlem hattı işlemleri sırasında, önerinin doğruluğu aynı şekilde doğrulanamaz.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
