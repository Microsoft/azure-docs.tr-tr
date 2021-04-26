---
title: 'Two-Class artırılmış karar ağacı: modül başvurusu'
titleSuffix: Azure Machine Learning
description: İkili bir sınıflandırıcı oluşturmak için tasarımcıda Two-Class artırılmış karar ağacı modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 810878956a89b6e7400be99692cf43925d1cd134
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94375936"
---
# <a name="two-class-boosted-decision-tree-module"></a>Two-Class artırılmış karar ağacı modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Bu modülü, bir makine öğrenimi modeli oluşturmak için, artırılmış karar ağaçları algoritmasına dayalı olarak kullanın. 

Artırılmış bir karar ağacı, ikinci ağacın ilk ağacın hatalarını düzelttiğinde, üçüncü ağaç birinci ve ikinci ağaçların hatalarını düzelttiğinde ve benzeri bir öğrenme yöntemidir. Tahmine dayalı olarak, tahminleri bir araya getiren tüm ağaçları temel alır.
  
Genellikle, düzgün şekilde yapılandırıldığında, çok çeşitli makine öğrenimi görevlerinde en iyi performansa sahip olmanın en kolay yöntemi olan, artırılmış karar ağaçları vardır. Bununla birlikte, bunlar aynı zamanda daha fazla bellek yoğun öğrenipden biridir ve geçerli uygulama bellekte her şeyi barındırır. Bu nedenle, artırılmış bir karar ağacı modeli, bazı doğrusal öğrenipalların işleyebileceği büyük veri kümelerini işleyemeyebilir.

Bu modül, LightGBM algoritmasını temel alır.

## <a name="how-to-configure"></a>Yapılandırma

Bu modül, eğitimli olmayan bir sınıflandırma modeli oluşturur. Sınıflandırma denetimli bir öğrenme yöntemi olduğundan, modeli eğitebilmeniz için tüm satırlar için bir değer içeren *etiketli bir veri kümesine* ihtiyacınız vardır.

Bu tür modeli [eğitme modeli](././train-model.md)kullanarak eğitebilirsiniz. 

1.  Azure Machine Learning ' de, işlem hattınızda, çalıştırılabilir **karar ağacı** modülünü ekleyin.
  
2.  Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.
  
    + **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz.
  
    + **Parametre aralığı**: en iyi parametrelerden emin değilseniz, [model hiper parametrelerini ayarla](tune-model-hyperparameters.md) modülünü kullanarak en iyi parametreleri bulabilirsiniz. Birkaç değer aralığı sağlarsınız ve en iyi sonucu üreten değerlerin birleşimini öğrenmek için eğitmen ayarların birden fazla birleşimine yinelenir.
  
3.  **Ağaç başına en fazla yaprakları** için, herhangi bir ağaçta oluşturulabilecek maksimum Terminal düğümü sayısını (yaprakları) belirtin.
  
     Bu değeri artırarak ağacın boyutunu artırabilir ve daha fazla anlayışın ve daha uzun eğitim süresi riskinden daha iyi bir duyarlık elde edersiniz.
  
4.  **Yaprak düğüm başına minimum örnek sayısı** için, bir ağaçta herhangi bir Terminal düğümü (yaprak) oluşturmak için gereken durum sayısını belirtin.  
  
     Bu değeri artırarak, yeni kurallar oluşturma eşiğini artırırsınız. Örneğin, varsayılan 1 değeri ile tek bir durum bile yeni bir kuralın oluşturulmasına neden olabilir. Değeri 5 ' e artırırsanız eğitim verilerinin aynı koşulları karşılayan en az beş durum içermesi gerekir.
  
5.  **Öğrenme oranı** için, öğrenirken adım boyutunu tanımlayan 0 ile 1 arasında bir sayı yazın.  
  
     Öğrenme oranı, öğrenimi en iyi çözüm üzerinde ne kadar hızlı veya yavaş söylebileceğinizi belirler. Adım boyutu çok büyükse en iyi çözümü fazla gerçekleştirebilirsiniz. Adım boyutu çok küçükse, eğitimin en iyi çözüm üzerinde yakınsama işlemi daha uzun sürer.
  
6.  **Oluşturulan ağaç sayısı** için, ensede birleştirmek üzere oluşturulacak karar ağacının toplam sayısını belirtin. Daha fazla karar ağacı oluşturarak daha iyi tedarik sağlayabilirsiniz, ancak eğitim süresi artar.
  
     Değeri 1 olarak ayarlarsanız, yalnızca bir ağaç oluşturulur (ilk parametre kümesini içeren ağaç) ve başka yineleme yapılmaz.
  
7.  **Rastgele sayı çekirdek** için isteğe bağlı olarak rastgele çekirdek değeri olarak kullanılacak negatif olmayan bir tamsayı yazın. Bir çekirdek belirtmek, aynı verilere ve parametrelere sahip olan çalışmalarda reproducibility sağlar.  
  
     Rastgele çekirdek varsayılan olarak 0 olarak ayarlanır; Bu, başlangıçtaki çekirdek değerinin sistem saatinden elde ettiği anlamına gelir.  Rastgele bir çekirdek kullanan art arda çalıştırılan çalıştırmalar farklı sonuçlara sahip olabilir.
  

9. Modeli eğitme:

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

+ Eğitilen modelin anlık görüntüsünü kaydetmek için **model eğitimi** modülünün sağ panelindeki **çıktılar** sekmesini seçin. Modeli yeniden kullanılabilir bir modül olarak kaydetmek için **veri kümesini kaydet** simgesini seçin.

+ Puanlama için modeli kullanmak üzere, bir işlem hattına **puan modeli** modülünü ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 