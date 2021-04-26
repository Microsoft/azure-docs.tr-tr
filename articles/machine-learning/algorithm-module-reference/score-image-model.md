---
title: Puan resmi model modülünü kullanma
titleSuffix: Azure Machine Learning
description: Eğitilen bir görüntü modeli kullanarak tahmin oluşturmak için Azure Machine Learning görüntü modeli modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324889"
---
# <a name="score-image-model"></a>Görüntü Modelini Puanlama

Bu makalede Azure Machine Learning tasarımcısında bir modül açıklanmaktadır.

Bu modülü, giriş görüntü verilerinde eğitilen bir görüntü modeli kullanarak tahmin oluşturmak için kullanın.

## <a name="how-to-configure-score-image-model"></a>Puan resmi modelini yapılandırma

1. İşlem hattınızı, **Puanlama görüntü modeli** modülünü ekleyin.

2. Eğitilen bir görüntü modeli ve girdi resim verilerini içeren bir veri kümesi ekleyin. 

    Verilerin ImageDirectory türünde olması gerekir. Görüntü dizini alma hakkında daha fazla bilgi için bkz. [görüntü dizini modülüne dönüştürme](convert-to-image-directory.md) . Giriş veri kümesinin şeması da genellikle modeli eğitmek için kullanılan verilerin şemasıyla aynı olmalıdır.

3. İşlem hattını gönderme.

## <a name="results"></a>Sonuçlar

[Puan görüntü modeli](score-image-model.md)kullanarak bir puan kümesi oluşturduktan sonra, modelin doğruluğunu (performans) değerlendirmek için kullanılan bir dizi ölçüm oluşturmak üzere, bu modülü ve puanlanmış veri kümesini [modeli değerlendirmek](evaluate-model.md)için bağlayabilirsiniz, 

### <a name="publish-scores-as-a-web-service"></a>Puanları Web hizmeti olarak yayımlama

Puanlama kullanımı, bir tahmine dayalı Web hizmetinin parçası olarak çıktıyı döndürmemelidir. Daha fazla bilgi için, Azure Machine Learning tasarımcısında bir işlem hattına göre gerçek zamanlı bir uç noktanın nasıl dağıtılacağı hakkında [Bu öğreticiye](../tutorial-designer-automobile-price-deploy.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure Machine Learning için [kullanılabilen modül kümesine](module-reference.md) bakın.