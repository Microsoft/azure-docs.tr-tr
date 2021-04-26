---
title: 'Anomali algılama modeli eğitme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Eğitimli anomali algılama modeli oluşturmak için anomali algılama modeli modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: edf35fada4233fbe43bc7f859c2414bfb8130714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "90905725"
---
# <a name="train-anomaly-detection-model-module"></a>Anomali algılama modeli modülünü eğitme

Bu makalede, eğitilen bir algılama modeli oluşturmak için Azure Machine Learning tasarımcısında anomali algılama modeli modülünün nasıl kullanılacağı açıklanır.

Modül, anomali algılama modeli için bir parametre kümesi ve etiketli olmayan bir veri kümesi olarak alır. Eğitim verileri için bir Etiketler kümesiyle birlikte eğitilen bir algılama modeli döndürür.  

Tasarımcıda sunulan anomali algılama algoritmaları hakkında daha fazla bilgi için bkz. [PCA tabanlı anomali algılama](pca-based-anomaly-detection.md).  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Tren anomali algılama modelini yapılandırma 

1.  Tasarımcı 'daki işlem hattınızı **eğitme algılama modeli** modülünü ekleyin. Bu modülü **anomali algılama** kategorisinde bulabilirsiniz.

2. [PCA tabanlı anomali algılama](pca-based-anomaly-detection.md)gibi anomali algılama için tasarlanan modüllerden birini bağlayın.

    Diğer model türleri desteklenmez. İşlem hattını çalıştırdığınızda "tüm modeller aynı öğrenner türüne sahip olmalıdır." hatasını alırsınız.  

3.  Etiket sütununu seçerek ve algoritmaya özgü diğer parametreleri ayarlayarak anomali algılama modülünü yapılandırın.  

4.  **Anomali algılama modeli eğitimi**'nin sağ tarafındaki girişine bir eğitim veri kümesi ekleyin.  

5.  İşlem hattını gönderme.  

## <a name="results"></a>Sonuçlar

Eğitim tamamlandıktan sonra:

+ Modelin parametrelerini görüntülemek için modüle sağ tıklayın ve **Görselleştir**' i seçin. 

+ Tahmine dayalı oluşturmak için, yeni giriş verileriyle [model puan](score-model.md) modülünü kullanın.

+ Eğitilen modelin anlık görüntüsünü kaydetmek için modülünü seçin. Ardından sağ paneldeki **çıktılar + Günlükler** sekmesinin altında **veri kümesi kaydet** simgesini seçin.   

 
## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın. 

Tasarımcı modüllerine özgü hataların listesi için bkz. [Tasarımcı Için özel durumlar ve hata kodları](designer-error-codes.md) .
'