---
title: Konuşma kapsayıcılarını yükler
titleSuffix: Azure Cognitive Services
description: Konuşma şemsiye Held grafik yapılandırma seçeneklerini ayrıntılı olarak ayrıntılardır.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73524093"
---
### <a name="speech-umbrella-chart"></a>Konuşma (şemsiye grafik)

Üst düzey "şemsiye" grafiğindeki değerler ilgili alt grafik değerlerini geçersiz kılar. Bu nedenle, tüm şirket içi özelleştirilmiş değerler buraya eklenmelidir.

|Parametre|Açıklama|Varsayılan|
| -- | -- | -- | -- |
| `speechToText.enabled` | **Konuşmaya metin** hizmetinin etkinleştirilip etkinleştirilmeyeceğini belirtir. | `true` |
| `speechToText.verification.enabled` | **Konuşmaya metin** hizmeti için `helm test` yeteneğinin etkinleştirilip etkinleştirilmeyeceğini belirtir. | `true` |
| `speechToText.verification.image.registry` | `helm test` **konuşmayı metin** hizmetini test etmek için kullandığı Docker görüntü deposu. Hela, test için küme içinde ayrı Pod oluşturuyor ve bu kayıt defterinden *test kullanımı* görüntüsünü çeker. | `docker.io` |
| `speechToText.verification.image.repository` | `helm test` **konuşmayı metin** hizmetini test etmek için kullandığı Docker görüntü deposu. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu depoyu kullanır. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | **Konuşma-metin** hizmeti için `helm test` birlikte kullanılan Docker görüntü etiketi. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu etiketi kullanır. | `latest` |
| `speechToText.verification.image.pullByHash` | *Test kullanan* Docker görüntüsünün karma tarafından çekilip çekilmediğini belirtir. `true`, geçerli görüntü karması değeri ile `speechToText.verification.image.hash` eklenmelidir. | `false` |
| `speechToText.verification.image.arguments` | Testi yürütmek için kullanılan bağımsız değişkenler Docker görüntüsünü *kullanın* . Hele test Pod, `helm test`çalıştırılırken bu bağımsız değişkenleri kapsayıcıya geçirir. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | **Metin okuma** hizmetinin etkin olup olmadığı. | `true` |
| `textToSpeech.verification.enabled` | **Konuşmaya metin** hizmeti için `helm test` yeteneğinin etkinleştirilip etkinleştirilmeyeceğini belirtir. | `true` |
| `textToSpeech.verification.image.registry` | `helm test` **konuşmayı metin** hizmetini test etmek için kullandığı Docker görüntü deposu. Hela, test için küme içinde ayrı Pod oluşturuyor ve bu kayıt defterinden *test kullanımı* görüntüsünü çeker. | `docker.io` |
| `textToSpeech.verification.image.repository` | `helm test` **konuşmayı metin** hizmetini test etmek için kullandığı Docker görüntü deposu. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu depoyu kullanır. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | **Konuşma-metin** hizmeti için `helm test` birlikte kullanılan Docker görüntü etiketi. Helmtest Pod, *sınama kullanımı* görüntüsünü çekmek için bu etiketi kullanır. | `latest` |
| `textToSpeech.verification.image.pullByHash` | *Test kullanan* Docker görüntüsünün karma tarafından çekilip çekilmediğini belirtir. `true`, geçerli görüntü karması değeri ile `textToSpeech.verification.image.hash` eklenmelidir. | `false` |
| `textToSpeech.verification.image.arguments` | *Test-* Docker görüntüsünü kullanarak yürütülecek bağımsız değişkenler. Hele test Pod, `helm test`çalıştırılırken bu bağımsız değişkenleri kapsayıcıya geçirir. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |