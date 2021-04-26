---
title: Kapsayıcı sınırlamaları-LUSıS
titleSuffix: Azure Cognitive Services
description: Desteklenen LUSıS kapsayıcı dilleridir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002366"
---
# <a name="language-understanding-luis-container-limitations"></a>Language Understanding (LUSıS) kapsayıcı sınırlamaları

LUıN kapsayıcıları bazı önemli sınırlamalara sahiptir. Desteklenmeyen bağımlılıklardan, desteklenen dillerin bir alt kümesine, bu makalede bu kısıtlamalar ayrıntılı olarak anlatılmaktadır.

## <a name="supported-dependencies-for-latest-container"></a>Kapsayıcı için desteklenen bağımlılıklar `latest`

En son LUSıS kapsayıcısı şunları destekler:

* [Yeni önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md): Bu kurumsal odaklı etki alanları varlıklar, örnek söylerin ve desenleri içerir. Bu etki alanlarını kendi kullanım için genişletin.

## <a name="unsupported-dependencies-for-latest-container"></a>Kapsayıcı için desteklenmeyen bağımlılıklar `latest`

[Kapsayıcıyı dışarı aktarmak](luis-container-howto.md#export-packaged-app-from-luis)IÇIN, lusıs uygulamanızdan desteklenmeyen bağımlılıkları kaldırmanız gerekir. Kapsayıcıyı dışarı aktarmaya çalıştığınızda, Lua portalı, kaldırmanız gereken bu desteklenmeyen özellikleri raporlar.

Aşağıdaki bağımlılıklardan herhangi birini **içermiyorsa** bir Luo uygulaması kullanabilirsiniz:

Desteklenmeyen uygulama yapılandırması|Ayrıntılar|
|--|--|
|Desteklenmeyen kapsayıcı kültürleri| Hollanda dili ( `nl-NL` ), Japonca () `ja-JP` ve Almanca ( `de-DE` ) dilleri yalnızca [1.0.2 belirteç ayırıcı](luis-language-support.md#custom-tokenizer-versions)ile desteklenir.|
|Tüm kültürler için desteklenmeyen varlıklar|Tüm kültürler için [KeyPhrase](luis-reference-prebuilt-keyphrase.md) önceden oluşturulmuş varlık|
|Ingilizce () kültürü için desteklenmeyen varlıklar `en-US`|[GeographyV2](luis-reference-prebuilt-geographyV2.md) önceden oluşturulmuş varlıklar|
|Konuşma primi|Kapsayıcıda dış bağımlılıklar desteklenmiyor.|
|Yaklaşım analizi|Kapsayıcıda dış bağımlılıklar desteklenmiyor.|
|Bing yazım denetimi|Kapsayıcıda dış bağımlılıklar desteklenmiyor.|

## <a name="languages-supported"></a>Desteklenen diller

LUSıS kapsayıcıları, LUYA tarafından [desteklenen dillerin](luis-language-support.md#languages-supported) bir alt kümesini destekler. LUıN kapsayıcıları, aşağıdaki dillerdeki söyleri anlayabilme yeteneğine sahiptir:

| Dil | Yerel Ayar | Önceden oluşturulmuş etki alanı | Önceden oluşturulmuş varlık | Tümcecik listesi önerileri | **[Metin Analizi](../text-analytics/language-support.md)<br>(Yaklaşım ve<br>Lerimi|
|--|--|:--:|:--:|:--:|:--:|
| İngilizce (ABD) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Arapça (Önizleme-modern Standart Arapça) |`ar-AR`|❌|❌|❌|❌|
| *[Çince](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Fransızca (Fransa) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Fransızca (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Almanca |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hintçe | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| İtalyanca |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Korece |`ko-KR` | ✔️ | ❌ | ❌ | Yalnızca *anahtar tümceciği* |
| Marathi | `mr-IN`|❌|❌|❌|❌|
| Portekizce (Brezilya) |`pt-BR` | ✔️ | ✔️ | ✔️ | tüm alt kültürler değil |
| İspanyolca (İspanya) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| İspanyolca (Meksika)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Tamil dili | `ta-IN`|❌|❌|❌|❌|
| Telugu dili | `te-IN`|❌|❌|❌|❌|
| Türkçe | `tr-TR` |✔️| ❌ | ❌ | *Yalnızca yaklaşım* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
