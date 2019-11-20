---
title: C# Hizmet kitaplığı 'Nı kullanarak Microsoft konuşma tanıma API 'si ile çalışmaya başlama | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Konuşma dilini metne dönüştürmek için Bing Konuşma tanıma hizmeti kitaplığını kullanın.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 71c3e471a8844eb6c6b70921e40c94338a084a8b
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965851"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Hızlı Başlangıç: .NET Windows için C&#35; 'de Bing Konuşma tanıma hizmeti kitaplığını kullanma

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Hizmet kitaplığı, kendi bulut hizmetine sahip olan ve hizmetlerinden konuşma hizmeti 'ni çağırmak isteyen geliştiricilere yöneliktir. Konuşma tanıma hizmetini cihaz bağlantılı uygulamalardan çağırmak isterseniz, bu SDK 'yı kullanmayın. (Bunun için diğer istemci kitaplıklarını veya REST API 'Lerini kullanın.)

C# Hizmet kitaplığını kullanmak için [Microsoft. Bing. Speech NuGet paketini](https://www.nuget.org/packages/Microsoft.Bing.Speech/)yükledikten sonra. Kitaplık API 'SI başvurusu için bkz. [Microsoft konuşma C# hizmeti kitaplığı](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Aşağıdaki bölümlerde, C# C# hizmet kitaplığını kullanarak örnek uygulamanın nasıl yükleneceğini, oluşturulacağı ve çalıştırılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="platform-requirements"></a>Platform gereksinimleri

Aşağıdaki örnek, Windows 8 + ve .NET 4.5 + Framework için [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)kullanılarak geliştirilmiştir.

### <a name="get-the-sample-application"></a>Örnek uygulamayı al

[Konuşma C# hizmeti kitaplığı örnek](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary) deposundan örneği kopyalayın.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Konuşma tanıma API 'sine abone olun ve ücretsiz deneme aboneliği anahtarı alın

Konuşma API 'SI bilişsel hizmetler 'in (daha önce Project Oxford) bir parçasıdır. Bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasından ücretsiz deneme aboneliği anahtarlarına sahip olabilirsiniz. Konuşma API 'sini seçtikten sonra anahtarı almak için **API anahtarı al** ' ı seçin. Birincil ve ikincil anahtar döndürür. Her iki anahtar de aynı kotaya bağlıdır, bu nedenle her iki anahtarı da kullanabilirsiniz.

> [!IMPORTANT]
> * Abonelik anahtarı alın. Konuşma istemci kitaplıklarını kullanabilmeniz için önce bir [abonelik anahtarınız](https://azure.microsoft.com/try/cognitive-services/)olması gerekir.
>
> * Abonelik anahtarınızı kullanın. Belirtilen C# hizmet kitaplığı örnek uygulamasıyla, abonelik anahtarınızı komut satırı parametrelerinden biri olarak sağlamanız gerekir. Daha fazla bilgi için bkz. [Örnek uygulamayı çalıştırma](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1\. adım: Örnek uygulamayı yükler

1. Visual Studio 2015 ' u başlatın ve **Dosya** > **Aç** > **Proje/çözüm**' ı seçin.

2. SpeechClient. sln adlı Visual Studio 2015 çözüm (. sln) dosyasını açmak için çift tıklayın. Çözüm, Visual Studio 'da açılır.

## <a name="step-2-build-the-sample-application"></a>2\. adım: Örnek uygulamayı oluşturma

CTRL + SHIFT + B tuşlarına basın veya şerit menüsünde **Oluştur** ' u seçin. Ardından **Build Solution**öğesini seçin.

## <a name="step-3-run-the-sample-application"></a>3\. adım: Örnek uygulamayı çalıştırın

1. Derleme tamamlandıktan sonra, örneği çalıştırmak için F5 tuşuna basın veya şerit menüsünde **Başlat** ' ı seçin.

2. Örnek için çıkış dizinini açın, örneğin, SpeechClientSample\bin\Debug. SHIFT + sağ tıklama tuşlarına basın ve **burada komut penceresini aç**' ı seçin.

3. Aşağıdaki `SpeechClientSample.exe` bağımsız değişkenlerle Çalıştır:

   * Arg [0]: Bir giriş sesi WAV dosyası belirtin.
   * Arg [1]: Ses yerel ayarını belirtin.
   * Arg [2]: Tanıma modlarını belirtin: `ShortPhrase` Modun`LongDictation` ve modunun uzunluğu için kısa.
   * Arg [3]: Konuşma tanıma hizmetine erişmek için abonelik anahtarını belirtin.

## <a name="samples-explained"></a>Açıklanan örnekler

### <a name="recognition-modes"></a>Tanıma modları

* `ShortPhrase`modundaysa En fazla 15 saniye uzunluğunda bir utterlik. Veriler sunucuya gönderildiğinde, istemci birden çok kısmi sonuç ve en iyi sonucu alır.
* `LongDictation`modundaysa En fazla 10 dakika uzunluğunda bir utterlik. Veriler sunucuya gönderilirken, istemci, tümce duraklamaları gösterdiği yere bağlı olarak birden çok kısmi sonuç ve birden çok nihai sonuç alır.

### <a name="supported-audio-formats"></a>Desteklenen ses biçimleri

Konuşma API 'si aşağıdaki codec bileşenlerini kullanarak ses/WAV 'yi destekler:

* PCM tek kanal
* SIREN
* SirenSR

### <a name="preferences"></a>Tercihler

Bir SpeechClient oluşturmak için önce bir Tercihler nesnesi oluşturmanız gerekir. Tercihler nesnesi, konuşma hizmetinin davranışını yapılandıran bir parametre kümesidir. Aşağıdaki alanlardan oluşur:

* `SpeechLanguage`: Konuşma hizmetine gönderilen sesin yerel ayarı.
* `ServiceUri`: Konuşma hizmetini çağırmak için kullanılan uç nokta.
* `AuthorizationProvider`: Konuşma hizmetine erişmek için belirteçleri getirmek üzere kullanılan bir ıauthorizationprovider uygulaması. Örnek, bilişsel hizmetler yetkilendirme sağlayıcısı sağlasa da, belirteç önbelleğe alma işlemini idare etmek için kendi uygulamanızı oluşturmanızı kesinlikle öneririz.
* `EnableAudioBuffering`: Gelişmiş bir seçenek. Bkz. [bağlantı yönetimi](#connection-management).

### <a name="speech-input"></a>Konuşma girişi

SpeechInput nesnesi iki alandan oluşur:

* **Ses**: SDK 'nın ses çekmesi arasından tercih ettiğiniz bir akış uygulamasıdır. Okumayı destekleyen herhangi bir [akış](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) olabilir.
   > [!NOTE]
   > Akış okuma içinde **0** DÖNDÜRDÜĞÜNDE, SDK akışın sonunu algılar.

* **Requestmetadata**: Konuşma isteğiyle ilgili meta veriler. Daha fazla bilgi için bkz. [başvuru](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Konuşma isteği

Bir SpeechClient ve SpeechInput nesneleri örnekledikten sonra, konuşma hizmetine bir istek yapmak için RecognizeAsync kullanın.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

İstek bittikten sonra, RecognizeAsync tarafından döndürülen görev tamamlanır. Son Recognıtionresult, tanımanın sonu. Hizmet veya SDK beklenmedik bir şekilde başarısız olursa görev başarısız olabilir.

### <a name="speech-recognition-events"></a>Konuşma tanıma olayları

#### <a name="partial-results-event"></a>Kısmi sonuçlar olayı

Bu olay, konuşma hizmeti, konuşmayı bitirmeden ( `MicrophoneRecognitionClient`kullanıyorsanız) veya veri göndermeyi ( `DataRecognitionClient`kullanıyorsanız) bir kez her tahmin edildiğinde çağrılır. Kullanarak `SpeechClient.SubscribeToPartialResult()`olaya abone olabilirsiniz. Ya da genel olaylar abonelik yöntemini `SpeechClient.SubscribeTo<RecognitionPartialResult>()`kullanabilirsiniz.

**Dönüş biçimi** | Açıklama |
------|------
**LexicalForm** | Bu form, ham, işlenmemiş konuşma tanıma sonuçları gerektiren uygulamalar tarafından kullanılmak üzere idealdir.
**Görünüm** | Ters Metin normalleştirme, büyük/küçük harf, noktalama ve küfür maskeleme uygulanmış bilinen tümcecik. Küfür başlangıç karakterinden sonra yıldız işaretiyle maskelenir, örneğin, "d * * *." Bu form, konuşma tanıma sonuçlarını bir kullanıcıya görüntüleyen uygulamalar tarafından kullanılmak üzere idealdir.
**Likli** | Tanınan tümceciğin güvenirlik düzeyi, konuşma tanıma sunucusu tarafından tanımlanan ilişkili ses için temsil eder.
**MediaTime** | Ses akışının başlangıcına göre geçerli zaman (100-nanosaniyelik birimi).
**MediaDuration** | Ses kesimine (100-nanosaniyelik birimi) göre geçerli tümcecik süresi/uzunluğu.

#### <a name="result-event"></a>Sonuç olayı
Konuşmayı bitirdiğinizde ( `ShortPhrase` modunda), bu olay çağırılır. Sonuç için n en iyi seçenek sunulur. `LongDictation` Modunda, olay birden çok kez çağrılabilir ve bu, sunucunun tümce duraklamaları gösterdiği yere bağlıdır. Kullanarak `SpeechClient.SubscribeToRecognitionResult()`olaya abone olabilirsiniz. Ya da genel olaylar abonelik yöntemini `SpeechClient.SubscribeTo<RecognitionResult>()`kullanabilirsiniz.

**Dönüş biçimi** | Açıklama |
------|------|
**Recognıtionstatus** | Tanımanın nasıl üretilme durumu. Örneğin, başarılı bir tanıma sonucu olarak ya da bağlantının iptal edilmesine bir sonuç olarak üretildiyse, vb.
**Deyim** | Tanıma güveniyle birlikte n-en iyi tanınan tümcecikler kümesi.

Tanıma sonuçları hakkında daha fazla bilgi için bkz. [çıkış biçimi](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Konuşma tanıma yanıtı

Konuşma yanıtı örneği:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High)
```

## <a name="connection-management"></a>Bağlantı yönetimi

API, istek başına tek bir WebSocket bağlantısı kullanır. En iyi kullanıcı deneyimi için SDK, konuşma hizmetine yeniden bağlanmaya çalışır ve aldığı son Recognıtionresult 'dan tanımayı başlatır. Örneğin, ses isteği iki dakika uzunsa SDK, tek dakikalık bir noktada bir Recognıtionevent almıştır ve beş saniye sonra bir ağ hatası oluştuysa, SDK bir dakikalık işaretten başlayan yeni bir bağlantı başlatır.

>[!NOTE]
>Akış aramayı desteklemediği için SDK tek dakikalık işaretine geri arama yapmaz. Bunun yerine, SDK, ses arabelleği arabelleğe almak için kullandığı bir dahili arabelleği tutar ve bu, Recognıtionresult olaylarını aldığından arabelleği temizler.

## <a name="buffering-behavior"></a>Arabelleğe alma davranışı

Varsayılan olarak SDK, bir ağ kesintisi oluştuğunda kurtarabilmesi için sesi arabelleğe alır. Ağ bağlantısı kesilme sırasında kesilen sesi atmayı tercih eden bir senaryoda ve bağlantıyı yeniden başlattıktan sonra, Tercihler `EnableAudioBuffering` `false`nesnesinde ' i ayarlayarak ses arabelleğe almayı devre dışı bırakmak en iyisidir.

## <a name="related-topics"></a>İlgili konular

[Microsoft konuşma C# hizmeti kitaplığı başvurusu](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
