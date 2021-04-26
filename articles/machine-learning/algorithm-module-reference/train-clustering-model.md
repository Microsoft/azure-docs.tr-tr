---
title: 'Kümeleme modeli eğitme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Kümeleme modellerini eğitmek için Azure Machine Learning kümeleme modelini eğitme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/17/2021
ms.openlocfilehash: ea6673a04bf9f5f568c660658e51036f2d2712e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654739"
---
# <a name="train-clustering-model"></a>Kümeleme Modelini Eğitme

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Bir kümeleme modeli eğitme için bu modülü kullanın.

Modül, zaten [K-bit kümeleme](k-means-clustering.md) modülünü kullanarak yapılandırdığınız eğitilen bir kümeleme modeli alır ve etiketli ya da etiketsiz veri kümesi kullanarak modeli ilerleder. Modül hem tahmin için kullanabileceğiniz eğitilen bir model hem de eğitim verilerinde her bir durum için bir küme atamaları kümesi oluşturur.

> [!NOTE]
> Bir kümeleme modeli, eğitim makine öğrenimi modelleri için genel modül olan [model eğitme](train-model.md) modülü kullanılarak eğitilemez. Bunun nedeni, [eğitme modelinin](train-model.md) yalnızca denetimli öğrenme algoritmalarıyla çalışmasıdır. K-ve diğer kümeleme algoritmalarının, açıklanmadan önce, bu algoritmanın etiketli verilerden öğrenilecebileceği anlamına gelir.  
  
## <a name="how-to-use-train-clustering-model"></a>Bir kümeleme modeli eğitimi kullanma  

1.  Tasarımcıdaki işlem hattınızda **kümeleme modeli eğitimi** modülünü ekleyin. Modülü, **eğitme** kategorisinde **Machine Learning modüller** altında bulabilirsiniz.  
  
2. [K-anlamı kümeleme](k-means-clustering.md) modülünü veya uyumlu bir kümeleme modeli oluşturan başka bir özel modülü ekleyin ve kümeleme modelinin parametrelerini ayarlayın.  
    
3.  Bir eğitim veri kümesini, **kümeleme modeli eğitimi** için sağ girişe iliştirin.
  
5.  **Sütun kümesi**' nde, kümeler oluştururken kullanılacak veri kümesinden sütunları seçin. İyi özellikleri olan sütunları seçtiğinizden emin olun: Örneğin, benzersiz değerlere sahip kimlikleri veya diğer sütunları ya da aynı değere sahip sütunları kullanmaktan kaçının.

    Bir etiket varsa, bunu bir özellik olarak kullanabilir ya da kapatabilirsiniz.  
  
6. Yeni küme etiketiyle eğitim verilerini bir araya getirmek istiyorsanız, **yalnızca sonuç için Ekle veya işaretini kaldır** seçeneğini işaretleyin.

    Bu seçeneğin işaretini kaldırırsanız yalnızca küme atamaları çıkışlardır. 

7. İşlem hattını gönder veya **Küme modelini eğitme** modülüne tıklayın ve **Seçileni Çalıştır**' ı seçin.  
  
### <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Eğitilen modelin anlık görüntüsünü kaydetmek için **model eğitimi** modülünün sağ panelindeki **çıktılar** sekmesini seçin. Modeli yeniden kullanılabilir bir modül olarak kaydetmek için **veri kümesini kaydet** simgesini seçin.

+ Modelden puanlar oluşturmak için [kümelere veri ata](assign-data-to-clusters.md)' yı kullanın.

> [!NOTE]
> Eğitilen modeli tasarımcıda dağıtmanız gerekiyorsa, **Puanlama modeli** yerine [kümelere veri atama](assign-data-to-clusters.md) , çıkarım ardışık düzeninde [Web hizmeti çıkış modülünün](web-service-input-output.md) girişine bağlı olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 