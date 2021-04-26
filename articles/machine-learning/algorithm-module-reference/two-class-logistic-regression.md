---
title: 'Two-Class Lojistik gerileme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: İkili bir sınıflandırıcı oluşturmak için Azure Machine Learning Two-Class lojistik regresyon modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2e29a666f4d478e11986f834cff94d9743223f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012655"
---
# <a name="two-class-logistic-regression-module"></a>Two-Class lojistik regresyon modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

İki (ve yalnızca iki) sonucu tahmin etmek için kullanılabilecek bir lojistik regresyon modeli oluşturmak için bu modülü kullanın. 

Lojistik regresyon, birçok tür sorunu modellemeye yönelik kullanılan iyi bilinen istatistiksel bir tekniktir. Bu algoritma, *denetimli bir öğrenme* yöntemidir;  Bu nedenle, modeli eğitetmek için sonuçları zaten içeren bir veri kümesi sağlamanız gerekir.  

### <a name="about-logistic-regression"></a>Lojistik regresyon hakkında  

Lojistik regresyon, bir sonucun olasılığını tahmin etmek için kullanılan istatistiklerde iyi bilinen bir yöntemdir ve özellikle sınıflandırma görevlerinde popüler olur. Algoritma bir lojistik işlevine veri ekleyerek bir olayın oluşma olasılığını tahmin eder.
  
Bu modülde sınıflandırma algoritması, dichotom veya ikili değişkenler için iyileştirilmiştir. birden çok sonucu sınıflandırmanız gerekiyorsa, çoklu [Lass lojistik regresyon](./multiclass-logistic-regression.md) modülünü kullanın.

##  <a name="how-to-configure"></a>Yapılandırma  

Bu modeli eğitebilmeniz için etiket veya sınıf sütunu içeren bir veri kümesi sağlamanız gerekir. Bu modül iki sınıf sorunlara yönelik olduğu için, etiket veya sınıf sütunu tam olarak iki değer içermelidir. 

Örneğin, etiket sütunu, olası "Yes" veya "No" değerleriyle [Voted] olabilir. Ya da, olası "yüksek" veya "düşük" değerleriyle [kredi riski] olabilir. 
  
1.  **Iki sınıf lojistik regresyon** modülünü işlem hattınıza ekleyin.  
  
2.  Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.  
  
    -   **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz.  

    -   **Parametre aralığı**: en iyi parametrelerden emin değilseniz, [model hiper parametrelerini ayarla](tune-model-hyperparameters.md) modülünü kullanarak en iyi parametreleri bulabilirsiniz. Birkaç değer aralığı sağlarsınız ve en iyi sonucu üreten değerlerin birleşimini öğrenmek için eğitmen ayarların birden fazla birleşimine yinelenir.
  
3.  **İyileştirme toleransı** için modeli iyileştirirken kullanılacak bir eşik değeri belirtin. Yinelemeler arasındaki geliştirme belirtilen eşiğin altına düşerse, algoritma bir çözüme yakınsamış olarak kabul edilir ve eğitim duraklar.  
  
4.  **L1 düzenleme Weight** ve **L2 düzenleme ağırlığı** Için, L1 ve L2 düzenleme parametreleri için kullanılacak bir değer yazın. Her ikisi için sıfır olmayan bir değer önerilir.  
     *Düzenleme* , çok büyük katsayı değerleriyle penalizing modellerle fazla sığdırmayı engellemek için bir yöntemdir. Düzenleme, katsayı değeri ile ilişkili ceza değerini, varsayım hatası ile ilişkilendirilen ceza ekleyerek işe yarar. Bu nedenle, çok büyük katsayı değerleri olan doğru bir model daha fazla sızlaştırılacağından, daha fazla koruyucu değere sahip daha az doğru bir model daha az olabilir.  
  
     L1 ve L2 düzenleme farklı etkileri ve kullanımları vardır.  
  
    -   L1, yüksek boyutlu verilerle çalışırken yararlı olabilecek seyrek modellere uygulanabilir.  
  
    -   Buna karşılık L2 düzenleme, seyrek olmayan veriler için tercih edilir.  
  
     Bu algoritma, L1 ve L2 düzenleme değerlerinin doğrusal bir birleşimini destekler: Yani, ve ise <code>x = L1</code> <code>y = L2</code> <code>ax + by = c</code> düzenleme koşullarının doğrusal yayılımını tanımlar.  
  
    > [!NOTE]
    >  L1 ve L2 düzenleme hakkında daha fazla bilgi edinmek istiyor musunuz? Aşağıdaki makalede, L1 ve L2 düzenleme 'in nasıl farklı olduğu ve model sığdırmayı nasıl etkilediği hakkında bir tartışma sunulmaktadır: lojistik regresyon ve sinir ağ modelleri için kod örnekleri,  [Machine Learning Için L1 ve L2 düzenleme](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)  
    >
    > L1 ve L2 koşullarına ait farklı doğrusal birleşimler lojistik regresyon modelleriyle kaldırılmıştır: Örneğin, [elastik net düzenleme](https://wikipedia.org/wiki/Elastic_net_regularization). Modelinizde etkin olan doğrusal bir bileşim tanımlamak için bu kombinasyonlara başvurulacağını öneririz.
      
5.  **L-** BFGS için bellek boyutu Için, *l-BFGS* iyileştirmesi için kullanılacak bellek miktarını belirtin.  
  
     L-BFGS, "sınırlı bellek Broyıdan-Fletu-Goldfarb-shanno" için temsil eder. Bu, parametre tahmini için popüler bir iyileştirme algoritmasıdır. Bu parametre, sonraki adım hesaplamasında depolanacak geçmiş konumların ve degradelerin sayısını belirtir.  
  
     Bu iyileştirme parametresi, sonraki adımı ve yönü hesaplamak için kullanılan bellek miktarını sınırlandırır. Daha az bellek belirttiğinizde, eğitim daha hızlıdır ancak daha az doğru olur.  
  
6.  **Rastgele numara kaynağı** için bir tamsayı değeri yazın. Sonuçların aynı işlem hattının birden fazla çalıştırması üzerinden tekrarlanabilir olmasını istiyorsanız çekirdek değeri tanımlama önemlidir.  
  
  
8. Ardışık düzene etiketli bir veri kümesi ekleyin ve modeli eğitme:

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
  
## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:
 
  
+ Yeni verileri tahmin etmek için eğitilen modeli ve yeni verileri [puan modeli](./score-model.md) modülüne giriş olarak kullanın. 


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.