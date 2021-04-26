---
title: 'Two-Class sinir ağı: modül başvurusu'
titleSuffix: Azure Machine Learning
description: İkili bir sınıflandırıcı oluşturmak için Azure Machine Learning Two-Class sinir ağ modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9131a2439facef00cae818bffef38e536a40a2fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421167"
---
# <a name="two-class-neural-network-module"></a>Two-Class sinir ağ modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Yalnızca iki değeri olan bir hedefi tahmin etmek için kullanılabilecek bir sinir ağ modeli oluşturmak için bu modülü kullanın.

Sinir Networks kullanan sınıflandırma, denetimli bir öğrenme yöntemidir ve bu nedenle etiket sütunu içeren *etiketli bir veri kümesi* gerektirir. Örneğin, bir hastanın belirli bir zaman çerçevesinde mi yoksa bir bilgisayarın başarısız olup olmadığı gibi ikili sonuçları tahmin etmek için bu sinir ağ modelini kullanabilirsiniz.  

Modeli tanımladıktan sonra [modeli eğitme](./train-model.md)için giriş olarak etiketli bir veri kümesi ve model sağlayarak bunu eğitme. Eğitilen model daha sonra yeni girişler için değerleri tahmin etmek için kullanılabilir.

### <a name="more-about-neural-networks"></a>Sinir Networks hakkında daha fazla bilgi

Bir sinir ağı, birbirine bağlı bir katman kümesidir. Girişler ilk katmandır ve ağırlıklı kenarlardan ve düğümlerden oluşan Çevrimsiz bir grafik tarafından çıkış katmanına bağlanır.

Giriş ve çıkış katmanları arasında birden çok gizli katman ekleyebilirsiniz. En tahmine dayalı görevler, yalnızca bir veya birkaç gizli katman ile kolayca gerçekleştirilebilir. Ancak, son araştırma, çok sayıda katmanlı derin sinir ağların (DNN), görüntü veya konuşma tanıma gibi karmaşık görevlerde etkili olduğunu göstermiştir. Art arda gelen katmanlar, daha yüksek semantik derinlik düzeylerini modellemek için kullanılır.

Girişler ve çıktılar arasındaki ilişki, giriş verilerinde sinir ağını öğreticiden öğrenilir. Grafiğin yönü, girdilerin gizli katman ve çıkış katmanına göre ilerler. Bir katmandaki tüm düğümler, sonraki katmandaki düğümlere ağırlıklı kenarlar ile bağlanır.

Belirli bir girdi için ağın çıkışını hesaplamak üzere, Gizli katmanlardaki ve çıkış katmanındaki her bir düğümde bir değer hesaplanır. Değer, önceki katmandaki düğümlerin değerlerinin ağırlıklı toplamı hesaplanarak ayarlanır. Daha sonra bu ağırlıklı Sum için bir etkinleştirme işlevi uygulanır.
  
## <a name="how-to-configure"></a>Yapılandırma

1.  **Iki sınıf sinir ağ** modülünü işlem hattınıza ekleyin. Bu modülü, **Sınıflandırma** kategorisinde **Machine Learning**, **Initialize** altında bulabilirsiniz.  
  
2.  Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.  
  
    -   **Tek parametre**: modeli nasıl yapılandırmak istediğinizi zaten biliyorsanız bu seçeneği belirleyin.

    -   **Parametre aralığı**: en iyi parametrelerden emin değilseniz, [model hiper parametrelerini ayarla](tune-model-hyperparameters.md) modülünü kullanarak en iyi parametreleri bulabilirsiniz. Birkaç değer aralığı sağlarsınız ve en iyi sonucu üreten değerlerin birleşimini öğrenmek için eğitmen ayarların birden fazla birleşimine yinelenir.  

3.  **Gizli katman belirtimi** için, oluşturulacak ağ mimarisi türünü seçin.  
  
    -   **Tam bağlantılı durum**: iki sınıf sinir ağları için tanımlanan varsayılan sinir ağ mimarisini aşağıdaki gibi kullanır:
  
        -   , Bir gizli katmana sahiptir.
  
        -   Çıktı Katmanı gizli katmana tamamen bağlanır ve gizli katman giriş katmanına tam olarak bağlanır.
  
        -   Giriş katmanındaki düğümlerin sayısı eğitim verilerinde özelliklerin sayısına eşittir.
  
        -   Gizli katmandaki düğümlerin sayısı Kullanıcı tarafından ayarlanır. Varsayılan değer 100’dür.
  
        -   Düğüm sayısı, sınıfların sayısına eşittir. İki sınıflı bir sinir ağı için bu, tüm girişlerin çıkış katmanındaki iki düğümden birine eşlenmesi gerektiği anlamına gelir.

5.  **Öğrenme oranı** için, düzeltmeden önce her yinelemede gerçekleştirilen adımın boyutunu tanımlayın. Öğrenme oranı için daha büyük bir değer modelin daha hızlı yakınsama olmasına neden olabilir, ancak yerel Mini ma 'yı aşırı gerçekleştirebilir.

6.  **Öğrenme yinelemeleri sayısı** için, algoritmanın eğitim çalışmalarını kaç kez işlemesi gerektiğini belirtin.

7.  **İlk öğrenme ağırlıkları çapı** için öğrenme sürecinin başlangıcında düğüm ağırlıklarını belirtin.

8.  **İtici güç** için, önceki yinelemelerdeki düğümlere öğrenme sırasında uygulanacak ağırlığı belirtin  

10. Yinelemeler arasındaki örnekleri karıştırmak için **karışık örnekler** seçeneğini belirleyin. Bu seçeneğin işaretini kaldırırsanız, işlem hattını her çalıştırdığınızda her zaman tam olarak aynı sırada işlenir.
  
11. **Rastgele sayı** için çekirdek olarak kullanılacak bir değer yazın.
  
     Aynı işlem hattının yinelenebilirlik üzerinde çalışmasını sağlamak istediğinizde bir çekirdek değeri belirtilmesi yararlı olur.  Aksi takdirde, bir sistem saati değeri çekirdek olarak kullanılır ve bu işlem hattı her çalıştırdığınızda biraz farklı sonuçlara neden olabilir.
  
13. Ardışık düzene etiketli bir veri kümesi ekleyin ve modeli eğitme:

    + **Tek parametre** için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](train-model.md) modülünü bağlayın.  
  
    + **Parametre aralığına** **oluşturma** , bir etiketli veri kümesini bağlama ve modeli [Ayarla hiper parametrelerini](tune-model-hyperparameters.md)kullanarak modeli eğitme.  
  
    > [!NOTE]
    > 
    > [Modeli Eğiteetmek](train-model.md)için bir parametre aralığı geçirirseniz, tek parametre listesindeki yalnızca varsayılan değeri kullanır.  
    > 
    > Tek bir parametre değerleri kümesini [ayarlama modeli hiper parametreleri](tune-model-hyperparameters.md) modülüne geçirirseniz, her parametre için bir dizi ayar beklerken, değerleri yoksayar ve öğrenici için varsayılan değerleri kullanır.  
    > 
    > **Parametre aralığı** seçeneğini belirleyip herhangi bir parametre için tek bir değer girerseniz, belirtilen tek değer, diğer parametrelerin bir değer aralığı üzerinde değişse bile, tarama boyunca kullanılır.  
  
14. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Eğitilen modelin anlık görüntüsünü kaydetmek için **model eğitimi** modülünün sağ panelindeki **çıktılar** sekmesini seçin. Modeli yeniden kullanılabilir bir modül olarak kaydetmek için **veri kümesini kaydet** simgesini seçin.

+ Puanlama için modeli kullanmak üzere, bir işlem hattına **puan modeli** modülünü ekleyin.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
