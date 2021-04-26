---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 832cc1d4f9ec3cec4ada6e964e3ab2f6f023dd41
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554542"
---
### <a name="neural-and-standard-voices"></a>Sinir ve standart sesler

Bölgeye/uç noktaya göre **sinir ve standart seslerin kullanılabilirliğini** öğrenmek için bu tabloyu kullanın:

| Bölge | Uç Nokta |
|--------|----------|
| Doğu Avustralya | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Güney Brezilya | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Orta Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Doğu Asya | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Doğu ABD | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Doğu ABD 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| Orta Fransa | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Hindistan Orta | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Doğu Japonya | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Batı Japonya | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Güney Kore - Orta | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Orta Kuzey ABD | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Kuzey Avrupa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Orta Güney ABD | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Güneydoğu Asya | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Güney Birleşik Krallık | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Orta Batı ABD | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| West Europe | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| Batı ABD | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Batı ABD 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> [Önizlemede bulunan sesler](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) yalnızca şu 3 bölgede kullanılabilir: Doğu ABD, Batı Avrupa ve Güneydoğu Asya.

### <a name="custom-voices"></a>Özel sesler

Özel bir ses yazı tipi oluşturduysanız, oluşturduğunuz uç noktayı kullanın. Aşağıda listelenen uç noktaları, `{deploymentId}` Ses modelinize ait DAĞıTıM kimliği ile değiştirerek de kullanabilirsiniz.

| Bölge | Uç Nokta |
|--------|----------|
| Doğu Avustralya | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Güney Brezilya | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Orta Kanada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Central US | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Doğu Asya | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Doğu ABD | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Doğu ABD 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Orta Fransa | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Hindistan Orta | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Doğu Japonya | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Batı Japonya | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Güney Kore - Orta | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Orta Kuzey ABD | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kuzey Avrupa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Orta Güney ABD | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Güneydoğu Asya | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Güney Birleşik Krallık | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| West Europe | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Batı ABD | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Batı ABD 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Özel sinir sesi

Aşağıdaki tabloda, özel sinir ses özellikleri için bölgesel destek ayrıntıları verilmiştir.

| Özellik | Desteklenen bölgeler |
|---|---|
| Ses modeli barındırma | Doğu ABD, Batı ABD 2, Orta Güney ABD, Güneydoğu Asya, UK Güney, Batı Avrupa Avustralya Doğu |
| Gerçek zamanlı karakterler | Doğu ABD, Batı ABD 2, Orta Güney ABD, Güneydoğu Asya, UK Güney, Batı Avrupa Avustralya Doğu |
| Uzun ses karakterleri | Doğu ABD, Batı Avrupa, UK Güney, Güneydoğu Asya, Hindistan Orta |
| Özel sinir eğitimi | Doğu ABD, UK Güney |

### <a name="long-audio-api"></a>Uzun ses API 'SI

Uzun ses API 'SI, benzersiz uç noktaları olan birden çok bölgede kullanılabilir.

| Bölge | Uç Nokta |
|--------|----------|
| Doğu ABD | `https://eastus.customvoice.api.speech.microsoft.com` |
| Hindistan Orta | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Güneydoğu Asya | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Güney Birleşik Krallık | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |
