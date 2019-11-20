---
title: Model Özel Görüntü İşleme Hizmeti test etme ve yeniden eğitme
titleSuffix: Azure Cognitive Services
description: Bu makalede bir görüntünün nasıl test yapılacağı ve Özel Görüntü İşleme hizmetinde modelinizi yeniden eğitebilmeniz için nasıl kullanılacağı gösterilir.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: c6ed8869f6d33609381a42fd22d728e9e5542802
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721188"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Özel Görüntü İşleme Hizmeti bir modeli test etme ve yeniden eğitme

Modelinize eğdikten sonra, yerel olarak depolanan bir görüntü veya çevrimiçi görüntü kullanarak hızlı bir şekilde test edebilirsiniz. Test en son eğitilen yinelemeyi kullanır.

## <a name="test-your-model"></a>Modelinizi test etme

1. [Özel görüntü işleme Web sayfasından](https://customvision.ai)projenizi seçin. Üstteki menü çubuğunun sağ tarafındaki **hızlı test** ' i seçin. Bu eylem **hızlı test**etiketli bir pencere açar.

    ![Hızlı test düğmesi, pencerenin sağ üst köşesinde görüntülenir.](./media/test-your-model/quick-test-button.png)

2. **Hızlı test** penceresinde **görüntü gönder** alanına tıklayın ve testiniz için kullanmak istediğiniz görüntünün URL 'sini girin. Bunun yerine yerel olarak depolanmış bir görüntü kullanmak istiyorsanız, **yerel dosyalara gözatıp** düğmesine tıklayın ve bir yerel görüntü dosyası seçin.

    ![Görüntü gönder sayfasının görüntüsü](./media/test-your-model/submit-image.png)

Seçtiğiniz görüntü sayfanın ortasında görünür. Sonra sonuçlar, **etiket** ve **güvenle**etiketlenmiş iki sütunlu bir tablo biçiminde görüntünün altında görüntülenir. Sonuçları güncelleştirdikten sonra **hızlı test** penceresini kapatabilirsiniz.

Artık bu test görüntüsünü modelinize ekleyebilir ve sonra modelinize yeniden eğitebilirsiniz.

## <a name="use-the-predicted-image-for-training"></a>Eğitim için tahmin edilen görüntüyü kullanma

Daha önce eğitim için gönderilen görüntüyü kullanmak için aşağıdaki adımları kullanın:

1. Sınıflandırıcıya gönderilen görüntüleri görüntülemek için, [özel görüntü işleme Web sayfasını](https://customvision.ai) açın ve __tahmin__ sekmesini seçin.

    ![Tahmin sekmesinin görüntüsü](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Varsayılan görünüm geçerli yinelemeden resimleri gösterir. Önceki yinelemeler sırasında gönderilen görüntüleri görüntülemek için __yineleme__ açılan alanını kullanabilirsiniz.

2. Sınıflandırıcı tarafından tahmin edilen etiketleri görmek için bir görüntünün üzerine gelin.

    > [!TIP]
    > Resim, sınıflandırıcının en iyi kazançlarını getirebilmesi için derecelendirilir. Farklı bir sıralama seçmek için __sıralama__ bölümünü kullanın.

    Eğitim verilerinize bir görüntü eklemek için görüntüyü seçin, etiketi seçin ve ardından __Kaydet ve Kapat__' ı seçin. Görüntü __tahminlerden__ kaldırılır ve eğitim görüntülerine eklenir. Bunu, __eğitim görüntüleri__ sekmesini seçerek görüntüleyebilirsiniz.

    ![Etiketleme sayfasının görüntüsü](./media/test-your-model/tag-image.png)

3. Sınıflandırıcının yeniden eğmesi için __eğitme__ düğmesini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

[Sınıflandırıcınızı geliştirme](getting-started-improving-your-classifier.md)
