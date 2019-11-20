---
title: Projenizi 3,0 API 'sine güncelleştirme
titleSuffix: Azure Cognitive Services
description: Özel Görüntü İşleme projelerini API 'nin önceki sürümünden 3,0 API 'sine güncelleştirme hakkında bilgi edinin.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647493"
---
# <a name="update-to-the-30-api"></a>3,0 API 'sine güncelleştirme

Özel Görüntü İşleme artık genel kullanılabilirliğe ulaştı ve bir API güncelleştirmesi gerçekleştirdi.
Bu güncelleştirme, birkaç yeni özellik ve daha önemlisi bazı önemli değişiklikler içerir:

* Tahmin API 'SI artık proje türüne göre iki olarak bölünür.
* Vision AI geliştirici seti (VAıDK) dışa aktarma seçeneği, bir projenin belirli bir şekilde oluşturulmasını gerektirir.
* Varsayılan yinelemeler, adlandırılmış bir yinelemeyi Yayımla/yayımdan kaldırma yararına kaldırılmıştır.

Bu kılavuzda, projelerinizi yeni API sürümüyle çalışacak şekilde nasıl güncelleşdirecektir. Değişikliklerin tam listesi için [sürüm notlarına](release-notes.md) bakın.

## <a name="use-the-updated-prediction-api"></a>Güncelleştirilmiş tahmin API 'sini kullanma

2\. x API 'Leri hem görüntü sınıflandırıcıları hem de nesne algılayıcı projeleri için aynı tahmin çağrısını kullandı. Her iki proje türü de **Predictımage** ve **Predictımageurl** çağrıları için kabul edilebilir. 3,0 ' den başlayarak, bu API 'yi, bu API 'yi, bu API 'yi, çağrı ile eşleştirmek için ayırdık:

* Görüntü sınıflandırma projelerine yönelik öngörülere ulaşmak için **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** ve **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** kullanın.
* Nesne algılama projelerine ilişkin tahminleri almak için **[Detectımage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** ve **[Detectımageurl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** kullanın.

## <a name="use-the-new-iteration-publishing-workflow"></a>Yeni yineleme yayımlama iş akışını kullanın

2\. x API 'Leri, tahmine göre kullanılacak yinelemeyi seçmek için varsayılan yinelemeyi veya belirtilen bir yineleme KIMLIĞINI kullandı. 3,0 ' den başlayarak, ilk olarak eğitim API 'sinden belirtilen bir ad altında bir yineleme yayımladığınızda yayımlama akışını benimsedik. Ardından, kullanılacak yinelemeyi belirtmek için adı tahmin yöntemlerine geçitirsiniz.

> [!IMPORTANT]
> 3,0 API 'Leri varsayılan yineleme özelliğini kullanmaz. Eski API 'Leri kullanımdan kaldırana kadar, bir yinelemeyi varsayılan olarak değiştirmek için 2. x API 'Lerini kullanmaya devam edebilirsiniz. Bu API 'Ler bir süre boyunca tutulacaktır ve bir yinelemeyi varsayılan olarak işaretlemek için **[Updateıteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metodunu çağırabilirsiniz.

### <a name="publish-an-iteration"></a>Yineleme yayımlama

Bir yineleme eğitilirken, **[Publishyineleme](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** yöntemini kullanarak tahmin için kullanılabilir hale getirebilirsiniz. Bir yinelemeyi yayımlamak için, CustomVision Web sitesinin ayarlar sayfasında bulunan tahmini kaynak KIMLIĞI gereklidir.

![Tahmin kaynak KIMLIĞI ana hatlarıyla Özel Görüntü İşleme Web sitesi ayarları sayfası.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Bu bilgileri [Azure portalından](https://portal.azure.com) özel görüntü işleme tahmin kaynağına giderek ve **Özellikler**' i seçerek de alabilirsiniz.

Yineleme yayımlandıktan sonra, uygulamalar bu adı tahmin API çağrısında belirterek tahmin için kullanabilir. Bir yinelemeyi tahmin çağrıları için kullanılamaz hale getirmek için **[Unpublishıteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API 'sini kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Eğitim API 'SI başvuru belgeleri (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Tahmin API 'SI başvuru belgeleri (REST)](https://go.microsoft.com/fwlink/?linkid=865445)