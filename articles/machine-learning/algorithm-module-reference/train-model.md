---
title: 'Modeli eğitme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Sınıflandırma veya regresyon modelini eğitmek için Azure Machine Learning **modeli eğitme** modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 41b327e793c6b35a2b2aeae825c493a484d84fb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562775"
---
# <a name="train-model-module"></a>Model modülünü eğitme

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Sınıflandırma veya regresyon modelini eğitmek için bu modülü kullanın. Eğitim, bir modeli tanımladıktan ve parametrelerini ayarladıktan sonra ve etiketli veriler gerektirdiğinde gerçekleşir. Ayrıca, mevcut bir modeli yeni verilerle yeniden eğitmek için **eğitme modeli** ' ni de kullanabilirsiniz. 

## <a name="how-the-training-process-works"></a>Eğitim süreci nasıl işler?

Azure Machine Learning, makine öğrenimi modelinin oluşturulması ve kullanılması genellikle üç adımlı bir işlemdir. 

1. Bir modeli, belirli bir algoritma türü seçerek ve parametrelerini veya hiper parametrelerini tanımlayarak yapılandırırsınız. Aşağıdaki model türlerinden birini seçin: 

    + Sinir Networks, karar ağaçları ve karar ormanları ve diğer algoritmalara göre **Sınıflandırma** modelleri.
    + Standart doğrusal regresyon veya sinir ağları ile Bayeme gerileme dahil diğer algoritmaları kullanan **regresyon** modelleri.  

2. Etiketli ve verilerle uyumlu olan bir veri kümesi sağlayın. **Modeli eğitme** için hem verileri hem de modeli bağlayın.

    Eğitimin ürettiği, verilerden öğrenilen istatistiksel desenleri kapsayan, iLearner olan belirli bir ikili biçimdir. Bu biçimi doğrudan değiştiremez veya okuyabilirsiniz; Ancak, diğer modüller bu eğitilen modeli kullanabilir. 
    
    Ayrıca modelin özelliklerini görüntüleyebilirsiniz. Daha fazla bilgi için sonuçlar bölümüne bakın.

3. Eğitim tamamlandıktan sonra, yeni verilerde tahmine dayalı hale getirmek için, [Puanlama modülleriyle](./score-model.md)eğitilen modeli kullanın.

## <a name="how-to-use-train-model"></a>Eğitim modeli kullanma 
    
1. İşlem hattına **model eğitme** modülünü ekleyin.  Bu modülü **Machine Learning** kategorisi altında bulabilirsiniz. **Eğit**' i genişletin ve ardından **model eğitimi** modülünü işlem hattınızla sürükleyin.
  
1.  Sol girişte, eğitilen modunu ekleyin. Eğitim veri kümesini, **tren modelinin** sağ girdisine iliştirin.

    Eğitim veri kümesi bir etiket sütunu içermelidir. Etiketleri olmayan herhangi bir satır yok sayılır.
  
1.  **Etiket sütunu** için, modülün sağ panelindeki **sütunu Düzenle** ' ye tıklayın ve modelin eğitim için kullanabileceği sonuçları içeren tek bir sütun seçin.
  
    - Sınıflandırma sorunları için etiket sütunu **kategorik** değerler ya da **ayrık** değerler içermelidir. Bazı örnekler bir Evet/Hayır derecelendirmesi, bir dimevsimi sınıflandırma kodu veya adı ya da bir gelir grubu olabilir.  Kategorik olmayan bir sütun seçerseniz, modül eğitim sırasında bir hata döndürür.
  
    -   Regresyon sorunları için etiket sütunu, yanıt değişkenini temsil eden **sayısal** veriler içermelidir. İdeal olarak, sayısal veriler sürekli bir ölçeklendirmeyi temsil eder. 
    
    Örnekler, bir kredi risk puanı, bir sabit sürücü için öngörülen başarısızlık süresi veya belirli bir gün ya da zaman için bir çağrı merkezine tahmini çağrı sayısı olabilir.  Sayısal bir sütun belirtmezseniz bir hata alabilirsiniz.
  
    -   Kullanılacak etiket sütununu belirtmezseniz, Azure Machine Learning, veri kümesinin meta verilerini kullanarak ilgili etiket sütunu olduğunu belirtmektir. Yanlış sütunu seçer, düzeltmek için sütun seçiciyi kullanın.
  
    > [!TIP] 
    > Sütun seçiciyi kullanırken sorun yaşıyorsanız, ipuçları için [veri kümesindeki sütunları seçme](./select-columns-in-dataset.md) makalesine bakın. KURALLARıN ve **ad** seçeneklerinin kullanımı **ile** ilgili bazı yaygın senaryolar ve ipuçları açıklanmaktadır.
  
1.  İşlem hattını gönderme. Çok fazla veriniz varsa, bu biraz zaman alabilir.

    > [!IMPORTANT] 
    > Her satırın KIMLIĞI olan bir KIMLIK sütununuzu veya çok fazla benzersiz değer içeren bir metin sütununu varsa **eğitme modeli** , "sütunda benzersiz değerler sayısı:" {column_name} "izin verilenden daha büyük olabilir.
    >
    > Bunun nedeni, sütunun benzersiz değerler eşiğine isabet ettiğinden ve belleğin yetersiz olmasına neden olabilir. Bu sütunu **açık bir özellik** olarak Işaretlemek Için [meta verileri Düzenle](edit-metadata.md) ' yi kullanabilir ve eğitiminde kullanılmaz veya [metin modülünden N-gram özelliklerini](extract-n-gram-features-from-text.md) Önişle metin sütununa ayıklayın. Daha fazla hata ayrıntısı için [Tasarımcı hata koduna](././designer-error-codes.md) bakın.

## <a name="model-interpretability"></a>Model Yorumlenebilirliği

Model yorumlanma, ML modelini anlarsınız ve karar vermek için, insanların daha fazla anlaşılabilir olmasını sağlamak için temel alınan temeli sunar.

Şu anda **eğitme modeli** modülü, [ml modellerini açıklamak için ıntertability Package kullanımını](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs)destekler. Aşağıdaki yerleşik algoritmalar desteklenir:

- Çizgisel Regresyon
- Sinir Ağı Regresyonu
- Çift Sınıflı Lojistik Regresyon
- İki Sınıflı Destek Vektör Makinesi
- Çok sınıf karar ormanı

Model açıklamalarını oluşturmak için model oluşturma modülündeki **model açıklaması** ' nda bulunan açılan listede **doğru** seçeneğini belirleyebilirsiniz. Varsayılan olarak, **model eğitme** modülünde false olarak ayarlanır. Açıklama oluşturmanın ek işlem maliyeti gerektirdiğini lütfen unutmayın.

![Model açıklaması onay kutusunu gösteren ekran görüntüsü](./media/module/train-model-explanation-checkbox.png)

İşlem hattı çalıştırıldıktan sonra **model modülünü eğit** ' in sağ bölmesindeki **açıklamalar** sekmesini ziyaret edebilir ve model performansı, veri kümesi ve özellik önemini keşfedebilirsiniz.

![Model açıklaması grafiklerini gösteren ekran görüntüsü](./media/module/train-model-explanations-tab.gif)

Azure Machine Learning model açıklamalarını kullanma hakkında daha fazla bilgi edinmek için [ml modellerini yorumlama](../how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs)hakkında nasıl yapılır makalesine başvurun.

## <a name="results"></a>Sonuçlar

Model eğitilirken:


+ Modeli başka işlem hatlarında kullanmak için modülünü seçin ve sağ paneldeki **çıktılar** sekmesinin altında bulunan **veri kümesini kaydet** simgesini seçin. Kaydedilmiş modellere, **veri kümeleri** altında modül paletinde erişebilirsiniz.

+ Modeli yeni değerleri tahmin etmek üzere kullanmak için, yeni giriş verileriyle birlikte, [puan modeli](./score-model.md) modülüne bağlayın.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.