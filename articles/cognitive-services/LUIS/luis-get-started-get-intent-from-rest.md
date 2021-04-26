---
title: REST API kullanarak amaç alma
description: Bu makalede, kullanıcının konuşma metinleriyle bir amaç olduğunu öğrenmek için kullanılabilir bir genel LUL uygulaması kullanın.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: b49d90ffe2a262895727fdd4ef5f8a7950104102
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96437023"
---
# <a name="how-to-get-an-intent-using-the-rest-apis"></a>REST API 'Leri kullanarak amaç alma

Bu makalede, bir kullanıcının konuşma metinden bir amaç olduğunu öğrenmek için bir LUO uygulaması kullanacaksınız. Kullanıcının amaç metnini, pizza uygulamasının HTTP tahmin uç noktasına gönderin. Uç noktada, Lu, doğal dil metnini anlam açısından analiz etmek için pizza uygulamasının modelini uygular, bu da genel amaçlı olarak belirlenir ve uygulamanın konu etki alanıyla ilgili verileri ayıklamaktır.

Bu makale için ücretsiz bir [LUIS](https://www.luis.ai) hesabınız olması gerekir.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
