---
title: 'Hızlı başlangıç: konuşmayı metne çevirme-metin okuma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502789"
---
Bu hızlı başlangıçta konuşmayı bir dilden başka bir dilde metne etkileşimli bir şekilde çevirmek için [konuşma SDK 'sını](~/articles/cognitive-services/speech-service/speech-sdk.md) kullanacaksınız. Birkaç önkoşulu karşıladıktan sonra, konuşmayı metne çevirme yalnızca beş adım sürer:
> [!div class="checklist"]
> * Abonelik anahtarınızdan ve bölgenizde bir ````SpeechConfig```` nesnesi oluşturun.
> * Kaynak ve hedef dilleri belirtmek için ````SpeechConfig```` nesnesini güncelleştirin.
> * Yukarıdaki ````SpeechConfig```` nesnesini kullanarak bir ````TranslationRecognizer```` nesnesi oluşturun.
> * ````TranslationRecognizer```` nesnesini kullanarak, tek bir söylik için tanıma sürecini başlatın.
> * Döndürülen ````TranslationRecognitionResult```` inceleyin.
