---
title: 'Hızlı başlangıç: konuşmayı, amaçları ve varlıkları tanıma-konuşma hizmeti'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: df4604560e05899461b11ec0788f72b27241f1b9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125633"
---
Bu hızlı başlangıçta, bir mikrofondan yakalanan ses verilerinden konuşmayı etkileşimli olarak tanımak için [konuşma SDK 'sını](~/articles/cognitive-services/speech-service/speech-sdk.md) kullanacaksınız. Birkaç önkoşul karşıladıktan sonra, konuşmayı bir mikrofondan tanımayı yalnızca dört adım sürer:
> [!div class="checklist"]
>
> * Abonelik anahtarınızdan ve bölgenizde bir ````SpeechConfig```` nesnesi oluşturun.
> * Yukarıdaki ````SpeechConfig```` nesnesini kullanarak bir ````IntentRecognizer```` nesnesi oluşturun.
> * ````IntentRecognizer```` nesnesini kullanarak, tek bir söylik için tanıma sürecini başlatın.
> * Döndürülen ````IntentRecognitionResult```` inceleyin.
