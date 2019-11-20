---
title: JavaScript 'te Bing Konuşma tanıma API 'SI ile çalışmaya başlama | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Konuşulan sesi sürekli metne dönüştüren uygulamalar geliştirmek için bilişsel hizmetler 'de Bing Konuşma tanıma API 'sini kullanın.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eef1a4885b77ae94f11d3d5bda5ded9b70ed63a4
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965807"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>JavaScript 'te konuşma tanıma API 'SI ile çalışmaya başlama

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Konuşma tanıma API 'sini kullanarak, konuşulan sesi metne dönüştüren uygulamalar geliştirebilirsiniz. JavaScript istemci kitaplığı, [konuşma hizmeti WebSocket protokolünü](../API-Reference-REST/websocketprotocol.md)kullanır, bu da aynı anda metni konuşmanıza ve almanızı sağlar. Bu makale, JavaScript 'te konuşma tanıma API 'sini kullanmaya başlamanıza yardımcı olur.

## <a name="prerequisites"></a>Önkoşullar

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Konuşma tanıma API 'sine abone olun ve ücretsiz deneme aboneliği anahtarı alın

Konuşma API 'SI bilişsel hizmetlerin bir parçasıdır. Bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasından ücretsiz deneme aboneliği anahtarlarına sahip olabilirsiniz. Konuşma API 'sini seçtikten sonra anahtarı almak için **API anahtarı al** ' ı seçin. Birincil ve ikincil anahtar döndürür. Her iki anahtar de aynı kotaya bağlıdır, bu nedenle her iki anahtarı da kullanabilirsiniz.

> [!IMPORTANT]
> Abonelik anahtarı alın. Konuşma istemci kitaplıklarını kullanabilmeniz için önce bir [abonelik anahtarınız](https://azure.microsoft.com/try/cognitive-services/)olması gerekir.

## <a name="get-started"></a>başlarken

Bu bölümde, örnek bir HTML sayfası yüklemek için gerekli adımlarda size yol göstereceğiz. Örnek, [GitHub deponuzda](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)bulunur. **Örneği doğrudan depodan açabilir** veya **bir deponun yerel kopyasından örneği açabilirsiniz** .

> [!NOTE]
> Bazı tarayıcılar güvenli olmayan kaynak üzerinde mikrofon erişimini engeller. Bu nedenle, desteklenen tüm tarayıcılarda çalışmasını sağlamak için ' Sample '/' App ' ' in https üzerinde barındırılmasına önerilir.

### <a name="open-the-sample-directly"></a>Örneği doğrudan açın

Yukarıda açıklandığı gibi bir abonelik anahtarı edinin. Ardından, [Örneğin bağlantısını](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html)açın. Bu, sayfayı varsayılan tarayıcınıza yükler ( [Htmlpreview](https://github.com/htmlpreview/htmlpreview.github.com)kullanılarak oluşturulur).

### <a name="open-the-sample-from-a-local-copy"></a>Örneği yerel kopyadan aç

Örneği yerel olarak denemek için bu depoyu kopyalayın:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

TypeScript kaynaklarını derleyin ve tek bir JavaScript dosyasına ([NPM](https://www.npmjs.com/) 'nin makinenizde yüklü olması gerekir) paketleyin. Kopyalanmış deponun köküne geçin ve komutları çalıştırın:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

En `samples\browser\Sample.html` sevdiğiniz tarayıcınızda açın.

## <a name="next-steps"></a>Sonraki adımlar

SDK 'nın kendi Web sayfanıza eklenmesi hakkında daha fazla bilgiye [buradan](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)ulaşabilirsiniz.

## <a name="remarks"></a>Açıklamalar

- Konuşma tanıma API 'SI üç [tanıma modunu](../concepts.md#recognition-modes)destekler. Sample. HTML dosyasında bulunan **Setup ()** işlevini güncelleştirerek modu değiştirebilirsiniz. Örnek, varsayılan `Interactive` olarak modunu ayarlar. Modu değiştirmek için parametreyi `SR.RecognitionMode.Interactive` başka bir moda güncelleştirin. Örneğin, parametresini olarak `SR.RecognitionMode.Conversation`değiştirin.
- Desteklenen dillerin tüm listesi için bkz. [desteklenen diller](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>İlgili konular

- [JavaScript konuşma tanıma API 'SI örnek deposu](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [REST API kullanmaya başlayın](GetStartedREST.md)
