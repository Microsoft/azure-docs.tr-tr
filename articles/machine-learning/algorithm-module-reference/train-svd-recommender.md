---
title: 'Eğitim SVD öneren: modül başvurusu'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning hizmetinde tren SVD öneren modülünü kullanarak, SVD algoritmasını kullanarak bir Bayeme öneren eğeceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 2019b752ab224abc244e471de3d427a77f7ed93a
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716910"
---
# <a name="train-svd-recommender"></a>SVD öneren eğitme

Bu makalede, Azure Machine Learning Tasarımcısı 'nda (Önizleme) bulunan SVD öneren modülünün nasıl kullanılacağı açıklanır. Tek değer ayrıştırma (SVD) algoritmasına dayalı bir öneri modeli geliştirmek için bu modülü kullanın.  

Tren SVD öneren modülü kullanıcı-öğe derecelendirme üçlü veri kümesini okur. Eğitilen bir SVD öneren döndürür. Daha sonra eğitilen modeli kullanarak, Puanlama tahmin edebilir veya öneriler oluşturabilir ve bu da [SVD öneren](score-svd-recommender.md) modülünü kullanabilirsiniz.  


  
## <a name="more-about-recommendation-models-and-the-svd-recommender"></a>Öneri modelleri ve SVD öneren hakkında daha fazla bilgi  

Öneri sisteminin ana amacı, sistem *kullanıcılarına* bir veya daha fazla *öğe* önermektir. Bir öğe örnekleri bir film, Restoran, kitap veya şarkı olabilir. Kullanıcı bir kişi, bir kişi grubu veya öğe tercihleri olan başka bir varlık olabilir.  

Öneren sistemlerinde iki ana yaklaşım vardır: 

+ **İçerik tabanlı** bir yaklaşım, kullanıcıların ve öğelerin özelliklerinin kullanımını sağlar. Kullanıcılar Age ve cinsiyet gibi özelliklerle açıklanabilir. Öğeler, yazar ve üretici gibi özelliklerle açıklanabilir. Sosyal eşleme sitelerinde, içerik tabanlı öneri sistemlerinin tipik örneklerini bulabilirsiniz. 
+ **Işbirliğine dayalı filtreleme** yalnızca kullanıcıların ve öğelerin tanımlayıcılarını kullanır. Bu varlıklar hakkında, kullanıcılar tarafından verilen derecelendirmelerin (seyrek) bir matrisinden bu varlıklarla ilgili örtük bilgiler alır. Derecelendirdikleri öğelerden ve aynı öğeleri derecelendirdiğiniz diğer kullanıcılardan bir kullanıcı hakkında bilgi edinebilirsiniz.  

SVD öneren, kullanıcıların ve öğelerin tanımlayıcılarını ve kullanıcılar tarafından öğeler tarafından verilen derecelendirmelerin bir matrisini kullanır. *İşbirlikçi bir öneren*. 

SVD öneren hakkında daha fazla bilgi için bkz. ilgili araştırma sayfası: [öneren sistemleri Için matris oluşturma teknikleri](https://datajobs.com/data-science-repo/Recommender-Systems-[Netflix].pdf).


## <a name="how-to-configure-train-svd-recommender"></a>Eğitim nasıl yapılandırılır SVD öneren  

### <a name="prepare-data"></a>Verileri hazırlama

Modülünü kullanmadan önce, giriş verileriniz öneri modelinin beklediği biçimde olmalıdır. Eğitim veri kümesi Kullanıcı-öğe derecelendirme Üçlü listesi gereklidir.

+ İlk sütunda kullanıcı tanımlayıcıları bulunur.
+ İkinci sütunda öğe tanımlayıcıları bulunur.
+ Üçüncü sütun, Kullanıcı öğesi çiftinin derecelendirmesini içerir. Derecelendirme değerleri sayısal türde olmalıdır.  

Azure Machine Learning tasarımcısında **Restoran derecelendirme** veri kümesi ( **kaydedilmiş veri kümelerini** seçin ve ardından **örnekler**) beklenen biçimi gösterir:

|UserID|Placeıd|rating|
|------------|-------------|------------|
|U1077|135085|2|
|U1077|135038|2|

Bu örnekten, tek bir kullanıcının iki ayrı Restoranları derecelendirdiğinden emin olabilirsiniz. 

### <a name="train-the-model"></a>Modeli eğitme

1.  Tasarımcıda SVD öneren modülünü eğit hale ekleyin ve eğitim verilerine bağlayın.  
   
2.  **Faktörlerin sayısı**için öneren ile kullanılacak faktörlerin sayısını belirtin.  
    
    Her faktörün, kullanıcının öğeyle ne kadar ilgili olduğunu ölçer. Faktörlerin sayısı Ayrıca, görünmeyen faktör alanının boyutaldır. Artan Kullanıcı ve öğe sayısı sayesinde daha fazla sayıda etmen ayarlamanız daha iyidir. Ancak sayı çok büyükse performans kaybolabilir.
    
3.  **Öneri algoritması yinelemesi sayısı** , algoritmanın giriş verilerini kaç kez işlemesi gerektiğini gösterir. Bu sayı arttıkça, tahmine göre daha kesin olur. Ancak, daha yüksek bir sayı daha yavaş eğitim anlamına gelir. Varsayılan değer 30 ' dur.

4.  **Öğrenme oranı**için, öğrenme için adım boyutunu tanımlayan 0,0 ile 2,0 arasında bir sayı girin.

    Öğrenme oranı her yinelemede adımın boyutunu belirler. Adım boyutu çok büyükse en iyi çözümü fazla gerçekleştirebilirsiniz. Adım boyutu çok küçükse, eğitim en iyi çözümü bulmayı daha uzun sürer. 
  
5.  İşlem hattını çalıştırma.  


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning hizmeti için [kullanılabilen modül kümesine](module-reference.md) bakın. 
