---
title: 'Sütun ekleme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: İki veri kümesini birleştirmek için sürükle ve bırak Azure Machine Learning Tasarımcısında sütun Ekle modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 36de827dff239dbeebc66e330a76b7a65fefb909
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421966"
---
# <a name="add-columns-module"></a>Sütun ekleme modülü

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

İki veri kümesini birleştirmek için bu modülü kullanın. Tek bir veri kümesi oluşturmak için giriş olarak belirttiğiniz iki veri kümesindeki tüm sütunları birleştirebilirsiniz. İkiden fazla veri kümesini birleştirmeniz gerekiyorsa, **sütun Ekle**' nin birkaç örneğini kullanın.



## <a name="how-to-configure-add-columns"></a>Sütun Ekle yapılandırma
1. İşlem hattınızla **sütun ekleme** modülünü ekleyin.

2. Birleştirmek istediğiniz iki veri kümesini bağlayın. İkiden fazla veri kümesini birleştirmek istiyorsanız, **sütun Ekle**' nin birkaç birleşimini birlikte zincirleyebilirsiniz.

    - Farklı sayıda satıra sahip iki sütunu birleştirmek mümkündür. Çıktı veri kümesi, daha küçük kaynak sütunundaki her bir satır için eksik değerlerle doldurulur.

    - Eklenecek sütunları tek tek seçemezsiniz. **Sütun Ekle** kullandığınızda her bir veri kümesindeki tüm sütunlar birleştirilir. Bu nedenle, sütunların yalnızca bir alt kümesini eklemek istiyorsanız, istediğiniz sütunları içeren bir veri kümesi oluşturmak için veri kümesinde sütunları seç ' i kullanın.

3. İşlem hattını gönderme.

### <a name="results"></a>Sonuçlar
İşlem hattı çalıştırıldıktan sonra:

- Yeni veri kümesinin ilk satırlarını görmek için, **sütun Ekle** modülüne sağ tıklayın ve Görselleştir ' i seçin. Ya da modülü seçip sağ paneldeki **çıktılar** sekmesine geçiş yapın ve sonucu görselleştirmek Için **bağlantı noktası çıktılarında** histogram simgesine tıklayın.

Yeni veri kümesindeki sütun sayısı, her iki giriş veri kümesinin sütunlarının toplamına eşittir.

Giriş veri kümelerinde aynı ada sahip iki sütun varsa, sütunun adına bir sayısal sonek eklenir. Örneğin, Targetoutal adlı bir sütunun iki örneği varsa, sol sütun TargetOutcome_1 olarak yeniden adlandırılır ve doğru sütun TargetOutcome_2 yeniden adlandırılır.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 