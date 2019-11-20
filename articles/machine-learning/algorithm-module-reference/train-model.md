---
title: 'Modeli eğitme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Sınıflandırma veya regresyon modelini eğitmek için Azure Machine Learning **modeli eğitme** modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 28cc2e1f8782f1ffd37e3a20fcc68264b481e42e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490374"
---
# <a name="train-model-module"></a>Model modülünü eğitme

Bu makalede Azure Machine Learning tasarımcısında modül (Önizleme) açıklanmaktadır.

Sınıflandırma veya regresyon modelini eğitmek için bu modülü kullanın. Eğitim, bir modeli tanımladıktan ve parametrelerini ayarladıktan sonra ve etiketli veriler gerektirdiğinde gerçekleşir. Ayrıca, mevcut bir modeli yeni verilerle yeniden eğitmek için **eğitme modeli** ' ni de kullanabilirsiniz. 

## <a name="how-the-training-process-works"></a>Eğitim süreci nasıl işler?

Azure Machine Learning, makine öğrenimi modelinin oluşturulması ve kullanılması genellikle üç adımlı bir işlemdir. 

1. Bir modeli, belirli bir algoritma türü seçerek ve parametrelerini veya hiper parametrelerini tanımlayarak yapılandırırsınız. Aşağıdaki model türlerinden birini seçin: 

    + Sinir Networks, karar ağaçları ve karar ormanları ve diğer algoritmalara göre **Sınıflandırma** modelleri.
    + Standart doğrusal regresyon veya sinir ağları ile Bayeme gerileme dahil diğer algoritmaları kullanan **regresyon** modelleri.  

2. Etiketli ve verilerle uyumlu olan bir veri kümesi sağlayın. **Modeli eğitme**için hem verileri hem de modeli bağlayın.

    Eğitimin ürettiği, verilerden öğrenilen istatistiksel desenleri kapsayan, iLearner olan belirli bir ikili biçimdir. Bu biçimi doğrudan değiştiremez veya okuyabilirsiniz; Ancak, diğer modüller bu eğitilen modeli kullanabilir. 
    
    Ayrıca modelin özelliklerini görüntüleyebilirsiniz. Daha fazla bilgi için sonuçlar bölümüne bakın.

3. Eğitim tamamlandıktan sonra, yeni verilerde tahmine dayalı hale getirmek için, [Puanlama modülleriyle](./score-model.md)eğitilen modeli kullanın.

## <a name="how-to-use-train-model"></a>**Eğitim modeli** kullanma  
  
1.  Azure Machine Learning, bir sınıflandırma modeli veya regresyon modeli yapılandırın.
    
2. İşlem hattına **model eğitme** modülünü ekleyin.  Bu modülü **Machine Learning** kategorisi altında bulabilirsiniz. **Eğit**' i genişletin ve ardından **model eğitimi** modülünü işlem hattınızla sürükleyin.
  
3.  Sol girişte, eğitilen modunu ekleyin. Eğitim veri kümesini, **tren modelinin**sağ girdisine iliştirin.

    Eğitim veri kümesi bir etiket sütunu içermelidir. Etiketleri olmayan herhangi bir satır yok sayılır.
  
4.  **Etiket sütunu**için, **sütun seçiciyi Başlat**' a tıklayın ve modelin eğitim için kullanabileceği sonuçları içeren tek bir sütun seçin.
  
    - Sınıflandırma sorunları için etiket sütunu **kategorik** değerler ya da **ayrık** değerler içermelidir. Bazı örnekler bir Evet/Hayır derecelendirmesi, bir dimevsimi sınıflandırma kodu veya adı ya da bir gelir grubu olabilir.  Kategorik olmayan bir sütun seçerseniz, modül eğitim sırasında bir hata döndürür.
  
    -   Regresyon sorunları için etiket sütunu, yanıt değişkenini temsil eden **sayısal** veriler içermelidir. İdeal olarak, sayısal veriler sürekli bir ölçeklendirmeyi temsil eder. 
    
    Örnekler, bir kredi risk puanı, bir sabit sürücü için öngörülen başarısızlık süresi veya belirli bir gün ya da zaman için bir çağrı merkezine tahmini çağrı sayısı olabilir.  Sayısal bir sütun belirtmezseniz bir hata alabilirsiniz.
  
    -   Kullanılacak etiket sütununu belirtmezseniz, Azure Machine Learning, veri kümesinin meta verilerini kullanarak ilgili etiket sütunu olduğunu belirtmektir. Yanlış sütunu seçer, düzeltmek için sütun seçiciyi kullanın.
  
    > [!TIP] 
    > Sütun seçiciyi kullanırken sorun yaşıyorsanız, ipuçları için [veri kümesindeki sütunları seçme](./select-columns-in-dataset.md) makalesine bakın. KURALLARıN ve **ad** seçeneklerinin kullanımı **ile** ilgili bazı yaygın senaryolar ve ipuçları açıklanmaktadır.
  
5.  İşlem hattını çalıştırma. Çok fazla veriniz varsa bu işlem biraz zaman alabilir.

## <a name="bkmk_results"></a>Sonucunun

Model eğitilirken:

+ Model parametrelerini ve özellik ağırlıklarını görüntülemek için, çıkışa sağ tıklayın ve **Görselleştir**' i seçin.
+ Modeli başka işlem hatlarında kullanmak için modele sağ tıklayın ve **modeli Kaydet**' i seçin. Model için bir ad yazın. 

    Bu, modeli, ardışık düzenin yinelenen çalıştırmaları tarafından güncelleştirilmemiş bir anlık görüntü olarak kaydeder.
+ Modeli yeni değerleri tahmin etmek üzere kullanmak için, yeni giriş verileriyle birlikte, [puan modeli](./score-model.md) modülüne bağlayın.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 