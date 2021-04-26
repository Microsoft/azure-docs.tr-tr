---
title: 'Two-Class ortalama Perceptron: modül başvurusu'
titleSuffix: Azure Machine Learning
description: İkili bir sınıflandırıcı oluşturmak için tasarımcıda Two-Class ortalama Perceptron modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421150"
---
# <a name="two-class-averaged-perceptron-module"></a>Two-Class ortalama Perceptron modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Ortalama Perceptron algoritmasını temel alan bir makine öğrenimi modeli oluşturmak için bu modülü kullanın.  
  
Bu sınıflandırma algoritması denetimli bir öğrenme yöntemidir ve etiket sütunu içeren etiketli bir *veri kümesi* gerektirir. Modeli [eğitmek](./train-model.md)için bir girdi olarak modeli ve etiketli veri kümesini sağlayarak modeli eğitebilirsiniz. Eğitilen model daha sonra yeni giriş örneklerinin değerlerini tahmin etmek için kullanılabilir.  

### <a name="about-averaged-perceptron-models"></a>Ortalama Perceptron modelleri hakkında

*Ortalama Perceptron yöntemi* , bir sinir ağının erken ve basit sürümüdür. Bu yaklaşımda girişler, doğrusal bir işleve göre birkaç olası çıkış halinde sınıflandırılır ve sonra özellik vektöründen türetilmiş bir ağırlık kümesiyle birleştirilir, bu nedenle "Perceptron" adı.

Daha basit Perceptron modelleri öğrenerek doğrusal desenleri öğrenirken, sinir Networks (özellikle derin sinir ağlar) daha karmaşık sınıf sınırlarını modelleyebilir. Ancak, perceptrons daha hızlıdır ve servis taleplerini hizmet olarak işledikleri için, perceptrons sürekli eğitim ile kullanılabilir.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Two-Class ortalama Perceptron yapılandırma

1.  Ardışık düzene **Iki sınıftaki ortalama Perceptron** modülünü ekleyin.  

2.  Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.  
  
    -   **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayın.

    -   **Parametre aralığı**: en iyi parametrelerden emin değilseniz ve bir parametre süpürme çalıştırmak istiyorsanız bu seçeneği belirleyin. Yinelemek için bir değer aralığı seçin ve [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) , en iyi sonuçları üreten hiper parametreleri belirlemek için, belirttiğiniz ayarların tüm olası birleşimlerinin üzerinde yinelenir.  
  
3.  **Öğrenme oranı** için *öğrenme oranı* için bir değer belirtin. Öğrenme oranı değerleri, her model test edildiğinde ve düzeltildiğinde, stochastik degradede kullanılan adımın boyutunu denetler.
  
     Hızı daha az yaparak, yerel bir Play 'de kalmış olabileceğiniz riskle modeli daha sık test edersiniz. Adımı daha büyük yaparak, doğru Minima 'yı aşırı atma riskine göre daha hızlı bir şekilde yakınsama yapabilirsiniz.
  
4.  **En fazla yineleme sayısı** için, algoritmanın eğitim verilerini incelemesi için kaç kez istediğinizi yazın.  
  
     Erken durdurma daha iyi Genelleştirme sağlar. Yineleme sayısının artırılması, fazla sığdırma riski altında sığdırmayı geliştirir.
  
5.  **Rastgele sayı çekirdek** için, isteğe bağlı olarak, çekirdek olarak kullanılacak bir tamsayı değeri yazın. İşlem hattının reproducibility arasında olmasını sağlamak istiyorsanız çekirdek kullanılması önerilir.  
  
1.  Eğitim veri kümesini bağlayın ve modeli eğitme:

    + **Tek parametre** için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](train-model.md) modülünü bağlayın.  
  
    + **Parametre aralığına** **oluşturma** , bir etiketli veri kümesini bağlama ve modeli [Ayarla hiper parametrelerini](tune-model-hyperparameters.md)kullanarak modeli eğitme.  
  
    > [!NOTE]
    > 
    > [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, tek parametre listesindeki yalnızca varsayılan değeri kullanır.  
    > 
    > Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.  
    > 
    > **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.




## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 