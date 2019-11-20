---
title: 'Kümeleme modeli eğitme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Kümeleme modellerini eğitmek için Azure Machine Learning kümeleme modelini eğitme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 1b26fc251aeb527041b1e648f19f9dd67f7701b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490394"
---
# <a name="train-clustering-model"></a>Kümeleme Modeli Eğitme

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Bir kümeleme modeli eğitme için bu modülü kullanın.

Modül, zaten [K-bit kümeleme](k-means-clustering.md) modülünü kullanarak yapılandırdığınız eğitilen bir kümeleme modeli alır ve etiketli ya da etiketsiz veri kümesi kullanarak modeli ilerleder. Modül hem tahmin için kullanabileceğiniz eğitilen bir model hem de eğitim verilerinde her bir durum için bir küme atamaları kümesi oluşturur.

> [!NOTE]
> Bir kümeleme modeli, eğitim makine öğrenimi modelleri için genel modül olan [model eğitme](train-model.md) modülü kullanılarak eğitilmiyor. Bunun nedeni, [eğitme modelinin](train-model.md) yalnızca denetimli öğrenme algoritmalarıyla çalışmasıdır. K-ve diğer kümeleme algoritmalarının, açıklanmadan önce, bu algoritmanın etiketli verilerden öğrenilecebileceği anlamına gelir.  
  
## <a name="how-to-use-train-clustering-model"></a>Bir kümeleme modeli eğitimi kullanma  

1.  Tasarımcıdaki işlem hattınızda **kümeleme modeli eğitimi** modülünü ekleyin. Modülü, **eğitme** kategorisinde **Machine Learning modüller**altında bulabilirsiniz.  
  
2. [K-anlamı kümeleme](k-means-clustering.md) modülünü veya uyumlu bir kümeleme modeli oluşturan başka bir özel modülü ekleyin ve kümeleme modelinin parametrelerini ayarlayın.  
    
3.  Bir eğitim veri kümesini, **kümeleme modeli eğitimi**için sağ girişe iliştirin.
  
5.  **Sütun kümesi**' nde, kümeler oluştururken kullanılacak veri kümesinden sütunları seçin. İyi özellikleri olan sütunları seçtiğinizden emin olun: Örneğin, benzersiz değerlere sahip kimlikleri veya diğer sütunları ya da aynı değere sahip sütunları kullanmaktan kaçının.

    Bir etiket varsa, bunu bir özellik olarak kullanabilir ya da kapatabilirsiniz.  
  
6. Yeni küme etiketiyle eğitim verilerini bir araya getirmek istiyorsanız, **yalnızca sonuç için Ekle veya işaretini kaldır**seçeneğini işaretleyin.

    Bu seçeneğin işaretini kaldırırsanız yalnızca küme atamaları çıkışlardır. 

7. İşlem hattını çalıştırın veya **kümeleme modeli eğitimi** modülüne tıklayın ve **Seçileni Çalıştır**' ı seçin.  
  
### <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:


+  Veri kümesindeki değerleri görüntülemek için modüle sağ tıklayın, **sonuç veri kümeleri**' ni seçin ve **Görselleştir**' e tıklayın.

+ Daha sonra yeniden kullanmak üzere eğitilen modeli kaydetmek için modüle sağ tıklayın, **eğitilen model**' i seçin ve **eğitilen model olarak kaydet**' e tıklayın.

+ Modelden puanlar oluşturmak için [kümelere veri ata](assign-data-to-clusters.md)' yı kullanın.



## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 