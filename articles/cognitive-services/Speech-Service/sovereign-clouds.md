---
title: Sovereign bulutlar-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Sovereign bulutlarını nasıl kullanacağınızı öğrenin
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.custom: references_regions
ms.date: 01/07/2021
ms.author: alexeyo
ms.openlocfilehash: f30b1f0f14bba54b8b4fcd7c5190f3c533f199a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98061761"
---
# <a name="speech-services-in-sovereign-clouds"></a>Sogeign bulutlarında konuşma Hizmetleri

## <a name="azure-government-united-states"></a>Azure Kamu (Birleşik Devletler)

Yalnızca ABD kamu varlıkları ve iş ortakları tarafından kullanılabilir. [Burada Azure Kamu hakkında](../../azure-government/documentation-government-welcome.md) daha fazla bilgi görüntüleyin [.](../../azure-government/compare-azure-government-global-azure.md)

- **Azure portal:**
  - [https://portal.azure.us/](https://portal.azure.us/)
- **Düzenleye**
  - US Gov Arizona
  - US Gov Virginia
- **Kullanılabilir fiyatlandırma katmanları:**
  - Ücretsiz (F0) ve standart (S0). Daha fazla ayrıntı için [buraya](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) bakın
- **Desteklenen Özellikler:**
  - Konuşmayı metne dönüştürme
    - Özel konuşma (akustik model (Har) ve dil modeli (LM) uyarlama)
      - [Speech Studio](https://speech.azure.us/)
  - Metin okuma
    - Standart ses
    - Sinir sesi
  - Konuşma Çeviricisi
- **Desteklenmeyen özellikler:**
  - Özel ses
- **Desteklenen Diller:**
  - Desteklenen dillerin listesini [burada](language-support.md) görebilirsiniz

### <a name="endpoint-information"></a>Uç nokta bilgileri

Bu bölüm, konuşma [SDK 'sı](speech-sdk.md), [konuşmadan metne REST API](rest-speech-to-text.md)ve [metinden konuşmaya REST API](rest-text-to-speech.md)kullanım için konuşma Hizmetleri uç noktası bilgilerini içerir.

#### <a name="speech-services-rest-api"></a>Konuşma Hizmetleri REST API

Azure Kamu 'daki konuşma Hizmetleri REST API uç noktaları aşağıdaki biçimdedir:

|  Tür/işlem REST API | Uç nokta biçimi |
|--|--|
| Erişim belirteci | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/sts/v1.0/issueToken`
| [Konuşmayı metne REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.microsoft.us/<URL_PATH>` |
| [Kısa ses için konuşmaya metne REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.us/<URL_PATH>` |
| [Metin okuma REST API'si](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.us/<URL_PATH>` |

`<REGION_IDENTIFIER>`Bu tablodaki aboneliğinizin bölgesiyle eşleşen tanımlayıcıyla değiştirin:

|                     | Bölge tanımlayıcısı |
|--|--|
| **US Gov Arizona**  | `usgovarizona` |
| **US Gov Virginia** | `usgovvirginia` |

#### <a name="speech-sdk"></a>Konuşma SDK'sı

Bağımsız bulutlarda konuşma SDK 'Sı için, `SpeechConfig` `--host` [konuşma CLI](spx-overview.md)sınıfının veya seçeneğinin "konaktan" örneğini kullanmanız gerekir. (Ayrıca "bitiş noktası" başlatma ve `--endpoint` Konuşma CLı seçeneği).

`SpeechConfig` sınıfın örneği şöyle oluşturulmalıdır:
```csharp
var config = SpeechConfig.FromHost(usGovHost, subscriptionKey);
```
Konuşma CLı aşağıdaki gibi kullanılmalıdır ( `--host` seçeneğini aklınızda):
```dos
spx recognize --host "usGovHost" --file myaudio.wav
```
`subscriptionKey`Konuşma kaynak anahtarınızla değiştirin. `usGovHost`Gerekli hizmet teklifiyle eşleşen ifadeyle ve bu tablodaki aboneliğinizin bölgesiyle değiştirin:

|  Bölge/hizmet teklifi | Ana bilgisayar ifadesi |
|--|--|
| **US Gov Arizona** | |
| Konuşmayı metne dönüştürme | `wss://usgovarizona.stt.speech.azure.us` |
| Metin Okuma | `https://usgovarizona.tts.speech.azure.us` |
| **US Gov Virginia** | |
| Konuşmayı metne dönüştürme | `wss://usgovvirginia.stt.speech.azure.us` |
| Metin Okuma | `https://usgovvirginia.tts.speech.azure.us` |


## <a name="azure-china"></a>Azure Çin

BT, Çin 'de iş varlığına sahip kuruluşlar tarafından kullanılabilir. Azure Çin hakkında daha fazla bilgi için [buraya bakın.](/azure/china/overview-operations) 


- **Azure portal:**
  - [https://portal.azure.cn/](https://portal.azure.cn/)
- **Düzenleye**
  - Çin Doğu 2
- **Kullanılabilir fiyatlandırma katmanları:**
  - Ücretsiz (F0) ve standart (S0). Daha fazla ayrıntı için [buraya](https://www.azure.cn/pricing/details/cognitive-services/index.html) bakın
- **Desteklenen Özellikler:**
  - Konuşmayı metne dönüştürme
    - Özel konuşma (akustik model (Har) ve dil modeli (LM) uyarlama)
      - [Speech Studio](https://speech.azure.cn/)
  - Metin okuma
    - Standart ses
    - Sinir sesi
  - Konuşma Çeviricisi
- **Desteklenmeyen özellikler:**
  - Özel ses
- **Desteklenen Diller:**
  - Desteklenen dillerin listesini [burada](language-support.md) görebilirsiniz

### <a name="endpoint-information"></a>Uç nokta bilgileri

Bu bölüm, konuşma [SDK 'sı](speech-sdk.md), [konuşmadan metne REST API](rest-speech-to-text.md)ve [metinden konuşmaya REST API](rest-text-to-speech.md)kullanım için konuşma Hizmetleri uç noktası bilgilerini içerir.

#### <a name="speech-services-rest-api"></a>Konuşma Hizmetleri REST API

Azure Çin 'de konuşma Hizmetleri REST API uç noktaları aşağıdaki biçimdedir:

|  Tür/işlem REST API | Uç nokta biçimi |
|--|--|
| Erişim belirteci | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/sts/v1.0/issueToken`
| [Konuşmayı metne REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | `https://<REGION_IDENTIFIER>.api.cognitive.azure.cn/<URL_PATH>` |
| [Kısa ses için konuşmaya metne REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) | `https://<REGION_IDENTIFIER>.stt.speech.azure.cn/<URL_PATH>` |
| [Metin okuma REST API'si](rest-text-to-speech.md) | `https://<REGION_IDENTIFIER>.tts.speech.azure.cn/<URL_PATH>` |

`<REGION_IDENTIFIER>`Bu tablodaki aboneliğinizin bölgesiyle eşleşen tanımlayıcıyla değiştirin:

|                     | Bölge tanımlayıcısı |
|--|--|
| **Çin Doğu 2**  | `chinaeast2` |

#### <a name="speech-sdk"></a>Konuşma SDK'sı

Bağımsız bulutlarda konuşma SDK 'Sı için, `SpeechConfig` `--host` [konuşma CLI](spx-overview.md)sınıfının veya seçeneğinin "konaktan" örneğini kullanmanız gerekir. (Ayrıca "bitiş noktası" başlatma ve `--endpoint` Konuşma CLı seçeneği).

`SpeechConfig` sınıfın örneği şöyle oluşturulmalıdır:
```csharp
var config = SpeechConfig.FromHost(azCnHost, subscriptionKey);
```
Konuşma CLı aşağıdaki gibi kullanılmalıdır ( `--host` seçeneğini aklınızda):
```dos
spx recognize --host "azCnHost" --file myaudio.wav
```
`subscriptionKey`Konuşma kaynak anahtarınızla değiştirin. `azCnHost`Gerekli hizmet teklifiyle eşleşen ifadeyle ve bu tablodaki aboneliğinizin bölgesiyle değiştirin:

|  Bölge/hizmet teklifi | Ana bilgisayar ifadesi |
|--|--|
| **Çin Doğu 2** | |
| Konuşmayı metne dönüştürme | `wss://chinaeast2.stt.speech.azure.cn` |
| Metin Okuma | `https://chinaeast2.tts.speech.azure.cn` |