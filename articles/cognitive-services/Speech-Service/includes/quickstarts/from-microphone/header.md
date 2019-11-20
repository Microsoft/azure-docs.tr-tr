---
title: 'Hızlı başlangıç: bir mikrofon konuşma hizmetinden konuşmayı tanıma'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960809"
---
Bu hızlı başlangıçta, bir mikrofondan yakalanan ses verilerinden konuşmayı etkileşimli olarak tanımak için [konuşma SDK 'sını](~/articles/cognitive-services/speech-service/speech-sdk.md) kullanacaksınız. Birkaç önkoşul karşıladıktan sonra, konuşmayı bir mikrofondan tanımayı yalnızca dört adım sürer:

> [!div class="checklist"]
> * Abonelik anahtarınızdan ve bölgenizde bir `SpeechConfig` nesnesi oluşturun.
> * Yukarıdaki `SpeechConfig` nesnesini kullanarak bir `SpeechRecognizer` nesnesi oluşturun.
> * `SpeechRecognizer` nesnesini kullanarak, tek bir söylik için tanıma sürecini başlatın.
> * Döndürülen `SpeechRecognitionResult` inceleyin.
