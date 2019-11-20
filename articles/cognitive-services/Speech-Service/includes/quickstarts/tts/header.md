---
title: 'Hızlı başlangıç: Sentezleştirme konuşma-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: Konuşma SDK 'sını kullanarak konuşmayı nasıl sentezleyeceğinizi öğrenin
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503083"
---
Bu hızlı başlangıçta, metni sentezleştirilmiş konuşmaya dönüştürmek için [konuşma SDK 'sını](~/articles/cognitive-services/speech-service/speech-sdk.md) kullanacaksınız. Birkaç önkoşul karşıladıktan sonra, birleştirilmiş konuşmayı varsayılan hoparlörlere işleme yalnızca dört adım sürer:
> [!div class="checklist"]
> * Abonelik anahtarınızdan ve bölgenizde bir ````SpeechConfig```` nesnesi oluşturun.
> * Yukarıdaki ````SpeechConfig```` nesnesini kullanarak bir ````SpeechSynthesizer```` nesnesi oluşturun.
> * Metni konuşmak için ````SpeechSynthesizer```` nesnesini kullanma.
> * Hatalar için döndürülen ````SpeechSynthesisResult```` denetleyin.
