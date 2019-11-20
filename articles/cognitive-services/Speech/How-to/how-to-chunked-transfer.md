---
title: Yığın aktarımı aktarım ses akışı | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Konuşma hizmetine ses akışı göndermek için öbekli trasfer kullanma
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 60428a7a5e28b7c9d6cd76f31374f24df48423a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966499"
---
# <a name="chunked-transfer-encoding"></a>Öbekli aktarım kodlaması

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Konuşmayı metne dönüştürmek için Microsoft konuşma tanıma API 'SI, sesi bir bütün öbek olarak veya sesi küçük parçalara dönüştürür. Etkili ses akışı ve döküm gecikmesini azaltmak için, sesi hizmete akışını sağlamak üzere [öbekli aktarım kodlamasını](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) kullanmanız önerilir. Diğer uygulamalar, daha yüksek Kullanıcı tarafından algılanan gecikmeye neden olabilir. Daha fazla bilgi için [ses akışları](../concepts.md#audio-streams) sayfasına bakın.

> [!NOTE]
> Herhangi bir istekte 10 ' dan fazla saniyeden fazlasını karşıya yükleyemeyebilirsiniz ve toplam istek süresi 14 saniyeyi aşamaz.
> [!NOTE]
> Yalnızca konuşma hizmetini çağırmak için [REST API 'lerini](../GetStarted/GetStartedREST.md) kullanırsanız, öbekli aktarım kodlamasını belirtmeniz gerekir. [İstemci kitaplıklarını](../GetStarted/GetStartedClientLibraries.md) kullanan uygulamaların, öbekli aktarım kodlamasını yapılandırması gerekmez.

Aşağıdaki kod, öbekli aktarım kodlamasının nasıl ayarlanacağını ve bir ses dosyasının 1024 baytlık parçalara nasıl gönderileceğini gösterir.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
