---
title: 'İki sınıf tarafından artırılmış karar ağacı: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ' de Iki sınıf önceden artırılmış karar ağacı modülünü kullanarak, önceden artırılmış karar ağaçları algoritmasına dayalı bir makine öğrenimi modeli oluşturmayı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 7590cab00670c92e8a5b90006778542ec44bf83f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490166"
---
# <a name="two-class-boosted-decision-tree-module"></a>İki sınıf önceden artırılmış karar ağacı modülü

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Bu modülü, bir makine öğrenimi modeli oluşturmak için, artırılmış karar ağaçları algoritmasına dayalı olarak kullanın. 

Artırılmış bir karar ağacı, ikinci ağacın ilk ağacın hatalarını düzelttiğinde, üçüncü ağaç birinci ve ikinci ağaçların hatalarını düzelttiğinde ve benzeri bir öğrenme yöntemidir.  Tahmine dayalı olarak, tahminleri bir araya getiren tüm ağaçları temel alır.
  
Genellikle, düzgün şekilde yapılandırıldığında, çok çeşitli makine öğrenimi görevlerinde en iyi performansa sahip olmanın en kolay yöntemi olan, artırılmış karar ağaçları vardır. Bununla birlikte, bunlar aynı zamanda daha fazla bellek yoğun öğrenipden biridir ve geçerli uygulama bellekte her şeyi barındırır. Bu nedenle, artırılmış bir karar ağacı modeli, bazı doğrusal öğrenipalların işleyebileceği büyük veri kümelerini işleyemeyebilir.

## <a name="how-to-configure"></a>Yapılandırma

Bu modül, eğitimli olmayan bir sınıflandırma modeli oluşturur. Sınıflandırma denetimli bir öğrenme yöntemi olduğundan, modeli eğitebilmeniz için tüm satırlar için bir değer içeren *etiketli bir veri kümesine* ihtiyacınız vardır.

Bu tür modeli [eğitme modeli](././train-model.md)kullanarak eğitebilirsiniz. 

1.  Azure Machine Learning ' de, işlem hattınızda, çalıştırılabilir **karar ağacı** modülünü ekleyin.
  
2.  Model **oluşturma modunu** ayarlayarak modelin eğitilme şeklini belirleyin.
  
    + **Tek parametre**: modeli nasıl yapılandırmak istediğinizi biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz.
  
  
3.  **Ağaç başına en fazla yaprakları**için, herhangi bir ağaçta oluşturulabilecek maksimum Terminal düğümü sayısını (yaprakları) belirtin.
  
     Bu değeri artırarak ağacın boyutunu artırabilir ve daha fazla anlayışın ve daha uzun eğitim süresi riskinden daha iyi bir duyarlık elde edersiniz.
  
4.  **Yaprak düğüm başına minimum örnek sayısı**için, bir ağaçta herhangi bir Terminal düğümü (yaprak) oluşturmak için gereken durum sayısını belirtin.  
  
     Bu değeri artırarak, yeni kurallar oluşturma eşiğini artırırsınız. Örneğin, varsayılan 1 değeri ile tek bir durum bile yeni bir kuralın oluşturulmasına neden olabilir. Değeri 5 ' e artırırsanız eğitim verilerinin aynı koşulları karşılayan en az beş durum içermesi gerekir.
  
5.  **Öğrenme oranı**için, öğrenirken adım boyutunu tanımlayan 0 ile 1 arasında bir sayı yazın.  
  
     Öğrenme oranı, öğrenimi en iyi çözüm üzerinde ne kadar hızlı veya yavaş söylebileceğinizi belirler. Adım boyutu çok büyükse en iyi çözümü fazla gerçekleştirebilirsiniz. Adım boyutu çok küçükse, eğitimin en iyi çözüm üzerinde yakınsama işlemi daha uzun sürer.
  
6.  **Oluşturulan ağaç sayısı**için, ensede birleştirmek üzere oluşturulacak karar ağacının toplam sayısını belirtin. Daha fazla karar ağacı oluşturarak daha iyi tedarik sağlayabilirsiniz, ancak eğitim süresi artar.
  
     Bu değer aynı zamanda eğitilen modeli görselleştirirken gösterilecek ağaç sayısını da denetler. tek bir ağacı görmek veya yazdırmak istiyorsanız, değeri 1 olarak ayarlayın. Ancak bunu yaptığınızda, yalnızca bir ağaç oluşturulur (ilk parametre kümesini içeren ağaç) ve başka yineleme gerçekleştirilmez.
  
7.  **Rastgele sayı çekirdek**için isteğe bağlı olarak rastgele çekirdek değeri olarak kullanılacak negatif olmayan bir tamsayı yazın. Bir çekirdek belirtmek, aynı verilere ve parametrelere sahip olan çalışmalarda reproducibility sağlar.  
  
     Rastgele çekirdek varsayılan olarak 0 olarak ayarlanır; Bu, başlangıçtaki çekirdek değerinin sistem saatinden elde ettiği anlamına gelir.  Rastgele bir çekirdek kullanan art arda çalıştırılan çalıştırmalar farklı sonuçlara sahip olabilir.
  

9. Modeli eğitme.
  
    + **Tek parametre**için bir görüntü **oluşturma modu** ayarlarsanız, etiketli bir veri kümesini ve [model eğitimi](./train-model.md) modülünü bağlayın.  
  
   
## <a name="results"></a>Sonuçlar

Model eğitimi tamamlandıktan sonra, sonuçları görüntülemek için [eğitim modeli](./train-model.md) çıktısına sağ tıklayın:

+ Her yinelemede oluşturulan ağacı görmek için, **Görselleştir**' i seçin. 
+ Bölünmeleri incelemek ve her bir düğümün kurallarını görmek için her bir ağaca tıklayın.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 