---
title: 'Sütunları seçin dönüştürme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Bir SELECT dönüştürmesi gerçekleştirmek için Azure Machine Learning tasarımcısında sütunları seçme dönüştürme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420725"
---
# <a name="select-columns-transform"></a>Sütun Dönüştürmeyi Seçme

Bu makalede Azure Machine Learning tasarımcısında sütunları seçme dönüştürme modülünün nasıl kullanılacağı açıklanır. Sütunları seçin dönüştürme modülünün amacı, aşağı akış makine öğrenimi işlemlerinde öngörülebilir, tutarlı bir sütun kümesinin kullanılmasını sağlamaktır.

Bu modül, belirli sütunlar gerektiren Puanlama gibi görevler için yararlıdır. Kullanılabilir sütunlardaki değişiklikler işlem hattını bölebilir veya sonuçları değiştirebilir.

Sütun kümesi oluşturmak ve kaydetmek için sütunları seç dönüşümünü kullanın. Ardından, bu seçimleri yeni verilere uygulamak için [dönüştürme modülünü Uygula](apply-transformation.md) ' yı kullanın.

## <a name="how-to-use-select-columns-transform"></a>Sütunları seçme dönüşümünü kullanma

Bu senaryo, model eğitimi için kullanılacak dinamik bir sütun kümesi oluşturmak üzere özellik seçimini kullanmak istediğinizi varsayar. Sütun seçimlerinin Puanlama sürecinde aynı olduğundan emin olmak için, sütun seçimlerini yakalamak ve bunları işlem hattında başka bir yere uygulamak için sütunları seç dönüştürme modülünü kullanın.

1. Tasarımcı 'daki işlem hattınızı bir giriş veri kümesi ekleyin.

2. [Filtre tabanlı özellik seçimine](filter-based-feature-selection.md)ait bir örnek ekleyin.

3. Modüller ' i bağlayın ve Özellik seçimi modülünü, giriş veri kümesindeki en iyi sayıda özelliği otomatik olarak bulacak şekilde yapılandırın.

4. [Model eğitimi](train-model.md) 'nin bir örneğini ekleyin ve eğitim için giriş olarak [filtre tabanlı özellik seçimi](filter-based-feature-selection.md) çıkışını kullanın.

    > [!IMPORTANT]
    > Özellik önemi sütunundaki değerleri temel aldığı için, [modeli eğitme](train-model.md)girişi için hangi sütunların kullanılabileceğini önceden bilemezsiniz.  
5. Sütunları seçme dönüştürme modülünün bir örneğini iliştirin. 

    Bu adım, bir sütun seçimini, başka veri kümelerine kaydedilebilecek veya uygulanabilen bir dönüşüm olarak oluşturur. Bu adım, diğer modüllerin yeniden kullanılması için özellik seçiminde tanımlanan sütunların kaydedilmesini sağlar.

6. [Puan modeli](score-model.md) modülünü ekleyin. 

   *Giriş veri kümesini bağlama.* Bunun yerine, [dönüştürme modülünü Uygula](apply-transformation.md) ' yı ekleyin ve Özellik seçimi dönüşümünün çıkışını bağlayın.

   İşlem hattı yapısı aşağıdaki gibi olmalıdır:

   > [!div class="mx-imgBorder"]
   > ![Örnek işlem hattı](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > Puanlama veri kümesine [filtre tabanlı özellik seçimi](filter-based-feature-selection.md) uygulamayı ve aynı sonuçları elde etmek için beklemeniz gerekmez. Özellik seçimi değerleri temel aldığı için, farklı bir sütun kümesi seçebilir ve bu da Puanlama işleminin başarısız olmasına neden olabilir.
    
7. İşlem hattını gönderme.

Bu işlem kaydetme ve sonra bir sütun seçimini uygulama, aynı veri şemasının eğitim ve Puanlama için kullanılabilir olmasını sağlar.


## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 
