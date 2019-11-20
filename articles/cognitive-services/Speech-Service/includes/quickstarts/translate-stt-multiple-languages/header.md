---
title: 'Hızlı başlangıç: konuşmayı birden çok dile çevirme-konuşma hizmeti'
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
ms.openlocfilehash: 6c65fd9a504b5f399afa99280ca2a75b476c750c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502817"
---
Bu hızlı başlangıçta konuşmayı bir dilden başka bir dilde konuşmaya etkileşimli bir şekilde çevirmek için [konuşma SDK 'sını](~/articles/cognitive-services/speech-service/speech-sdk.md) kullanacaksınız. Birkaç önkoşul karşıladıktan sonra, birden çok dilde konuşmayı metne çevirme yalnızca altı adımdan yararlanır:
> [!div class="checklist"]
> * Abonelik anahtarınızdan ve bölgenizde bir ````SpeechTranslationConfig```` nesnesi oluşturun.
> * Konuşma tanıma kaynak dilini belirtmek için ````SpeechTranslationConfig```` nesnesini güncelleştirin.
> * Birden çok çeviri hedefi dili belirtmek için ````SpeechTranslationConfig```` nesnesini güncelleştirin.
> * Yukarıdaki ````SpeechTranslationConfig```` nesnesini kullanarak bir ````TranslationRecognizer```` nesnesi oluşturun.
> * ````TranslationRecognizer```` nesnesini kullanarak, tek bir söylik için tanıma sürecini başlatın.
> * Döndürülen ````TranslationRecognitionResult```` inceleyin.
