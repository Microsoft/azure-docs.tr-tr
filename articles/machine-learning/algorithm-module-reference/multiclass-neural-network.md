---
title: 'Birden çok Lass sinir ağı: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Çok sınıflı değerler içeren bir hedefi tahmin etmek için Azure Machine Learning tasarımcısında birden çok Lass sinir ağ modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a4f7f8a7793f31ffbf2099cbfb314fc5097319f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421269"
---
# <a name="multiclass-neural-network-module"></a>Birden çok Lass sinir ağ modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Birden çok değere sahip bir hedefi tahmin etmek için kullanılabilecek bir sinir ağ modeli oluşturmak için bu modülü kullanın. 

Örneğin, bu türdeki sinir ağları, sayı veya mektup tanıma, belge sınıflandırması ve model tanıma gibi karmaşık bilgisayar görme görevlerinde kullanılabilir.

Sinir Networks kullanan sınıflandırma, denetimli bir öğrenme yöntemidir ve bu nedenle etiket sütunu içeren *etiketli bir veri kümesi* gerektirir.

Modeli [eğitmek](./train-model.md)için bir girdi olarak modeli ve etiketli veri kümesini sağlayarak modeli eğitebilirsiniz. Eğitilen model daha sonra yeni giriş örneklerinin değerlerini tahmin etmek için kullanılabilir.  

## <a name="about-neural-networks"></a>Sinir Networks hakkında

Bir sinir ağı, birbirine bağlı bir katman kümesidir. Girişler ilk katmandır ve ağırlıklı kenarlardan ve düğümlerden oluşan Çevrimsiz bir grafik tarafından çıkış katmanına bağlanır.

Giriş ve çıkış katmanları arasında birden çok gizli katman ekleyebilirsiniz. En tahmine dayalı görevler, yalnızca bir veya birkaç gizli katman ile kolayca gerçekleştirilebilir. Ancak, son araştırma, çok sayıda katmanlı derin sinir ağların (DNN), görüntü veya konuşma tanıma gibi karmaşık görevlerde etkili olduğunu göstermiştir. Art arda gelen katmanlar, daha yüksek semantik derinlik düzeylerini modellemek için kullanılır.

Girişler ve çıktılar arasındaki ilişki, giriş verilerinde sinir ağını öğreticiden öğrenilir. Grafiğin yönü, girdilerin gizli katman ve çıkış katmanına göre ilerler. Bir katmandaki tüm düğümler, sonraki katmandaki düğümlere ağırlıklı kenarlar ile bağlanır.

Belirli bir girdi için ağın çıkışını hesaplamak üzere, Gizli katmanlardaki ve çıkış katmanındaki her bir düğümde bir değer hesaplanır. Değer, önceki katmandaki düğümlerin değerlerinin ağırlıklı toplamı hesaplanarak ayarlanır. Daha sonra bu ağırlıklı Sum için bir etkinleştirme işlevi uygulanır.

## <a name="configure-multiclass-neural-network"></a>Birden çok Lass sinir ağını yapılandırma

1. Tasarımcı 'daki işlem hattınızı çoklu **Lass sinir ağ** modülünü ekleyin. Bu modülü, **Sınıflandırma** kategorisinde **Machine Learning**, **Initialize** altında bulabilirsiniz.

2. **Eğitmen modu oluşturma**: modelin nasıl eğitilme etmek istediğinizi belirtmek için bu seçeneği kullanın:

    - **Tek parametre**: modeli nasıl yapılandırmak istediğinizi zaten biliyorsanız bu seçeneği belirleyin.

    - **Parametre aralığı**: en iyi parametrelerden emin değilseniz ve bir parametre süpürme çalıştırmak istiyorsanız bu seçeneği belirleyin. Yinelemek için bir değer aralığı seçin ve [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) , en iyi sonuçları üreten hiper parametreleri belirlemek için, belirttiğiniz ayarların tüm olası birleşimlerinin üzerinde yinelenir.  

3. **Gizli katman belirtimi**: oluşturulacak ağ mimarisi türünü seçin.

    - **Tam bağlı durum**: varsayılan sinir ağ mimarisini kullanarak bir model oluşturmak için bu seçeneği belirleyin. Birden çok Lass sinir ağ modelleri için varsayılanlar aşağıdaki gibidir:

        - Bir gizli katman
        - Çıktı Katmanı gizli katmana tam olarak bağlanır.
        - Gizli katman, giriş katmanına tam olarak bağlanır.
        - Giriş katmanındaki düğümlerin sayısı eğitim verilerinde özelliklerin sayısına göre belirlenir.
        - Gizli katmandaki düğümlerin sayısı Kullanıcı tarafından ayarlanabilir. Varsayılan değer 100'dür.
        - Çıkış katmanındaki düğümlerin sayısı, sınıfların sayısına bağlıdır.
  
   

5. **Gizli düğümlerin sayısı**: Bu seçenek, varsayılan mimarideki gizli düğüm sayısını özelleştirmenizi sağlar. Gizli düğümlerin sayısını yazın. Varsayılan değer, 100 düğümü olan bir gizli katmandır.

6. **Öğrenme oranı**: düzeltmeden önce her yinelemede gerçekleştirilen adımın boyutunu tanımlayın. Öğrenme oranı için daha büyük bir değer modelin daha hızlı yakınsama olmasına neden olabilir, ancak yerel Mini ma 'yı aşırı gerçekleştirebilir.

7. **Öğrenme yinelemesi sayısı**: algoritmanın eğitim çalışmalarını kaç kez işlemesi gerektiğini belirtin.

8. **İlk öğrenme ağırlıkları çapı**: öğrenme sürecinin başlangıcında düğüm ağırlıklarını belirtin.

9. **İtici güç**: önceki yinelemelerdeki düğümlere öğrenme sırasında uygulanacak ağırlığı belirtin.
  
11. **Örnekleri karıştır**: yinelemeler arasındaki örnekleri karıştırmak için bu seçeneği belirleyin.

    Bu seçeneğin işaretini kaldırırsanız, işlem hattını her çalıştırdığınızda her zaman tam olarak aynı sırada işlenir.

12. **Rastgele sayı çekirdek**: aynı işlem hattının çalıştırmaları arasında yinelenebilirlik sağlamak istiyorsanız, çekirdek olarak kullanılacak bir değer yazın.

14. Modeli eğitme:

    + **Tek parametre** için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](train-model.md) modülünü bağlayın.  
  
    + **Parametre aralığına** **oluşturma** , bir etiketli veri kümesini bağlama ve modeli [Ayarla hiper parametrelerini](tune-model-hyperparameters.md)kullanarak modeli eğitme.  
  
    > [!NOTE]
    > 
    > [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, tek parametre listesindeki yalnızca varsayılan değeri kullanır.  
    > 
    > Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.  
    > 
    > **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.  
  

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

- Eğitilen modelin anlık görüntüsünü kaydetmek için **model eğitimi** modülünün sağ panelindeki **çıktılar** sekmesini seçin. Modeli yeniden kullanılabilir bir modül olarak kaydetmek için **veri kümesini kaydet** simgesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 