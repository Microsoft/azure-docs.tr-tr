---
title: 'Hızlı başlangıç: ses dosyasına konuşma Sentezleştirme-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502950"
---
Bu hızlı başlangıçta, bir ses dosyasında metni sentezleştirilmiş konuşmaya dönüştürmek için [konuşma SDK 'sını](~/articles/cognitive-services/speech-service/speech-sdk.md) kullanacaksınız. Birkaç önkoşul karşıladıktan sonra, konuşma bir dosyaya senile yalnızca beş adım sürer:
> [!div class="checklist"]
> * Abonelik anahtarınızdan ve bölgenizde bir ````SpeechConfig```` nesnesi oluşturun.
> * Öğesini belirten bir ses yapılandırma nesnesi oluşturun. WAV dosya adı.
> * Yukarıdaki yapılandırma nesnelerini kullanarak bir ````SpeechSynthesizer```` nesnesi oluşturun.
> * ````SpeechSynthesizer```` nesnesini kullanarak, metninizi sentezleştirilmiş konuşmaya dönüştürerek belirtilen ses dosyasına kaydederek metninizi birleştirilmiş konuşmaya dönüştürün.
> * Hatalar için döndürülen ````SpeechSynthesizer```` inceleyin.
