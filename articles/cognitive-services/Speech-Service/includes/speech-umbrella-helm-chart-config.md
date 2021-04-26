---
title: Konuşma kapsayıcılarını yükler
titleSuffix: Azure Cognitive Services
description: Konuşma şemsiye Held grafik yapılandırma seçeneklerini ayrıntılı olarak ayrıntılardır.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002315"
---
### <a name="speech-umbrella-chart"></a>Konuşma (şemsiye grafik)

Üst düzey "şemsiye" grafiğindeki değerler ilgili alt grafik değerlerini geçersiz kılar. Bu nedenle, tüm şirket içi özelleştirilmiş değerler buraya eklenmelidir.

|Parametre|Açıklama|Varsayılan|
| -- | -- | -- | -- |
| `speechToText.enabled` | **Konuşmaya metin** hizmetinin etkinleştirilip etkinleştirilmeyeceğini belirtir. | `true` |
| `speechToText.verification.enabled` | `helm test` **Konuşma-metin** Hizmeti özelliğinin etkinleştirilip etkinleştirilmeyeceğini belirtir. | `true` |
| `speechToText.verification.image.registry` | `helm test` **Konuşma-metin** hizmetini test etmek için kullanan Docker görüntü deposu. Hela, test için küme içinde ayrı Pod oluşturuyor ve bu kayıt defterinden *test kullanımı* görüntüsünü çeker. | `docker.io` |
| `speechToText.verification.image.repository` | `helm test` **Konuşma-metin** hizmetini test etmek için kullanan Docker görüntü deposu. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu depoyu kullanır. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | `helm test` **Konuşma-metin** hizmeti için ile kullanılan Docker Image etiketi. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu etiketi kullanır. | `latest` |
| `speechToText.verification.image.pullByHash` | *Test kullanan* Docker görüntüsünün karma tarafından çekilip çekilmediğini belirtir. Varsa `true` , `speechToText.verification.image.hash` geçerli görüntü karma değeri ile eklenmelidir. | `false` |
| `speechToText.verification.image.arguments` | Testi yürütmek için kullanılan bağımsız değişkenler Docker görüntüsünü *kullanın* . Hele test Pod, çalışırken bu bağımsız değişkenleri kapsayıcıya geçirir `helm test` . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | **Metin okuma** hizmetinin etkin olup olmadığı. | `true` |
| `textToSpeech.verification.enabled` | `helm test` **Konuşma-metin** Hizmeti özelliğinin etkinleştirilip etkinleştirilmeyeceğini belirtir. | `true` |
| `textToSpeech.verification.image.registry` | `helm test` **Konuşma-metin** hizmetini test etmek için kullanan Docker görüntü deposu. Hela, test için küme içinde ayrı Pod oluşturuyor ve bu kayıt defterinden *test kullanımı* görüntüsünü çeker. | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test` **Konuşma-metin** hizmetini test etmek için kullanan Docker görüntü deposu. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu depoyu kullanır. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | `helm test` **Konuşma-metin** hizmeti için ile kullanılan Docker Image etiketi. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu etiketi kullanır. | `latest` |
| `textToSpeech.verification.image.pullByHash` | *Test kullanan* Docker görüntüsünün karma tarafından çekilip çekilmediğini belirtir. Varsa `true` , `textToSpeech.verification.image.hash` geçerli görüntü karma değeri ile eklenmelidir. | `false` |
| `textToSpeech.verification.image.arguments` | *Test-* Docker görüntüsünü kullanarak yürütülecek bağımsız değişkenler. Hele test Pod, çalışırken bu bağımsız değişkenleri kapsayıcıya geçirir `helm test` . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |