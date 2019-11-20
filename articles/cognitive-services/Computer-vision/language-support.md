---
title: Dil desteği-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Bu makale, Görüntü İşleme özellikleri tarafından desteklenen doğal dillerin bir listesini sağlar; OCR, Metin Tanıma ve okuma.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718620"
---
# <a name="language-support-for-computer-vision"></a>Görüntü İşleme için dil desteği

Görüntü İşleme bazı özellikleri birden çok dili destekler; Burada bahsedilen özellikler yalnızca Ingilizce 'yi destekler.

## <a name="text-recognition"></a>Metin tanıma

Görüntü İşleme, metni birçok dilde tanıyabilir. Özellikle, [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API 'si çeşitli dilleri destekler, ancak [Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API ve [metin tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API yalnızca İngilizce 'yi destekler. Bu işlevsellik ve her bir API 'nin avantajları hakkında daha fazla bilgi için bkz. [basılı ve el yazısı metnini tanıma](concept-recognizing-text.md) .

OCR, giriş malzemesinin dilini otomatik olarak algılar, bu nedenle API çağrısında bir dil kodu belirtmeniz gerekmez. Ancak, dil kodları her zaman JSON yanıtında `"language"` düğümünün değeri olarak döndürülür.

|Dil| Dil kodu | OCR APı 'SI |
|:-----|:----:|:-----:|
|Arapça | `ar`|✔ |
|Çince (Basitleştirilmiş) | `zh-Hans`|✔ |
|Çince (Geleneksel) | `zh-Hant`|✔ |
|Çekçe | `cs` |✔ |
|Danca | `da` |✔ |
|Hollanda dili | `nl` |✔ |
|Türkçe | `en` |✔ |
|Fince | `fi` |✔ |
|Fransızca | `fr` |✔ |
|Almanca | `de` |✔ |
|Yunanca | `el` |✔ |
|Macarca | `hu` |✔ |
|İtalyanca | `it` |✔ |
|Japonca | `ja` |✔ |
|Korece | `ko` |✔ |
|Norveççe | `nb` |✔ |
|Lehçe | `pl` |✔ |
|Portekizce | `pt` |✔ |
|Rumence | `ro` |✔ |
|Rusça | `ru` |✔ |
|Sırpça (Kiril) | `sr-Cyrl` |✔ |
|Sırpça (Latin) | `sr-Latn` |✔ |
|Slovakça | `sk` |✔ |
|İspanyolca | `es` |✔ |
|İsveççe | `sw` |✔ |
|Türkçe | `tr` |✔ |

## <a name="image-analysis"></a>Görüntü Analizi

[Analyze-Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 'sindeki bazı eylemler, `language` sorgu parametresiyle belirtilen diğer dillerde sonuç döndürebilir. Diğer Eylemler, belirtilen dilin ne olduğuna bakmaksızın sonuçları Ingilizce olarak döndürür ve bazıları desteklenmeyen diller için bir özel durum oluşturur. Eylemler `visualFeatures` ve `details` sorgu parametreleriyle belirtilir; görüntü analizi ile yapabileceğiniz tüm eylemlerin listesini görmek için bkz. [genel bakış](home.md) .

|Dil | Dil kodu | Kategoriler | Etiketler | Açıklama | Yetişkin | Markalar | Renk | Yüzler | Resim Türü | Nesneler | Ünlüler | Sakal |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Çince | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Türkçe | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Japonca | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portekizce | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|İspanyolca | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda bahsedilen Görüntü İşleme özelliklerini kullanmaya başlayın.

* [Yerel görüntüyü çözümleme (REST)](./quickstarts/csharp-analyze.md)
* [Yazdırılan metni Ayıkla (REST)](./quickstarts/csharp-print-text.md)