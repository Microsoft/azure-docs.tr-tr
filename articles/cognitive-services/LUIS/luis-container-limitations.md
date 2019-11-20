---
title: Kapsayıcı sınırlamaları-LUSıS
titleSuffix: Azure Cognitive Services
description: Desteklenen LUSıS kapsayıcı dilleridir.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507863"
---
# <a name="language-understanding-luis-container-limitations"></a>Language Understanding (LUSıS) kapsayıcı sınırlamaları

LUıN kapsayıcıları bazı önemli sınırlamalara sahiptir. Desteklenmeyen bağımlılıklardan, desteklenen dillerin bir alt kümesine, bu makalede bu kısıtlamalar ayrıntılı olarak anlatılmaktadır.

## <a name="supported-dependencies-for-latest-container"></a>`latest` kapsayıcısı için desteklenen bağımlılıklar

[Nbuild/2019](https://news.microsoft.com/build2019/)tarihinde yayınlanan en son Lua kapsayıcısı şunları destekleyecektir:

* [Yeni önceden oluşturulmuş etki alanları](luis-reference-prebuilt-domains.md): Bu kurumsal odaklı etki alanları varlıklar, örnek söylerin ve desenleri içerir. Bu etki alanlarını kendi kullanım için genişletin.

## <a name="unsupported-dependencies-for-latest-container"></a>`latest` kapsayıcısı için desteklenmeyen bağımlılıklar

[Kapsayıcıyı dışarı aktarmak](luis-container-howto.md#export-packaged-app-from-luis)IÇIN, lusıs uygulamanızdan desteklenmeyen bağımlılıkları kaldırmanız gerekir. Kapsayıcıyı dışarı aktarmaya çalıştığınızda, Lua portalı, kaldırmanız gereken bu desteklenmeyen özellikleri raporlar.

Aşağıdaki bağımlılıklardan herhangi birini **içermiyorsa** bir Luo uygulaması kullanabilirsiniz:

Desteklenmeyen uygulama yapılandırması|Ayrıntılar|
|--|--|
|Desteklenmeyen kapsayıcı kültürleri| Felemenkçe (`nl-NL`)<br>Japonca (`ja-JP`)<br>Almanca yalnızca [1.0.2 belirteç ayırıcı](luis-language-support.md#custom-tokenizer-versions)ile desteklenir.|
|Tüm kültürler için desteklenmeyen varlıklar|Tüm kültürler için [KeyPhrase](luis-reference-prebuilt-keyphrase.md) önceden oluşturulmuş varlık|
|Ingilizce (`en-US`) kültürü için desteklenmeyen varlıklar|[GeographyV2](luis-reference-prebuilt-geographyV2.md) önceden oluşturulmuş varlıklar|
|Konuşma primi|Kapsayıcıda dış bağımlılıklar desteklenmiyor.|
|Yaklaşım analizi|Kapsayıcıda dış bağımlılıklar desteklenmiyor.|
|Bing yazım denetimi|Kapsayıcıda dış bağımlılıklar desteklenmiyor.|

## <a name="languages-supported"></a>Desteklenen diller

LUSıS kapsayıcıları, LUYA tarafından [desteklenen dillerin](luis-language-support.md#languages-supported) bir alt kümesini destekler. LUıN kapsayıcıları, aşağıdaki dillerdeki söyleri anlayabilme yeteneğine sahiptir:

| Dil | Yerel Ayar | Önceden oluşturulmuş etki alanı | Önceden oluşturulmuş varlık | Tümcecik listesi önerileri | **[metin analizi](../text-analytics/language-support.md)<br>(Yaklaşım ve<br>Lerimi|
|--|--|:--:|:--:|:--:|:--:|
| Amerikan Ingilizcesi | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Çince](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Fransızca (Fransa) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Fransızca (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Almanca |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hintçe | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| İtalyanca |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Korece |`ko-KR` | ✔️ | ❌ | ❌ | Yalnızca *anahtar tümceciği* |
| Portekizce (Brezilya) |`pt-BR` | ✔️ | ✔️ | ✔️ | tüm alt kültürler değil |
| İspanyolca (Ispanya) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| İspanyolca (Meksika)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Türkçe | `tr-TR` |✔️| ❌ | ❌ | *Yalnızca yaklaşım* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]