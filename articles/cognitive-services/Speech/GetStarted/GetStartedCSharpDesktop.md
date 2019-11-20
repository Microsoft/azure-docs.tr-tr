---
title: C# Masaüstü kitaplığı 'nı kullanarak Bing Konuşma tanıma API 'si ile çalışmaya başlama | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Konuşma sesini metne dönüştürmek için Bing Konuşma tanıma API 'sini kullanan temel Windows Uygulamaları geliştirin.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3160ccc1c8741d87fcee94a6face48551a79052d
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966893"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Hızlı Başlangıç: Windows 'da .NET için C&#35; 'de BING konuşma tanıma API 'sini kullanma

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bu sayfada, konuşulan sesi metne dönüştürmek için konuşma tanıma API 'sini kullanan temel bir Windows uygulamasının nasıl geliştirilmesi gösterilmektedir. İstemci kitaplığını kullanmak gerçek zamanlı akışa izin verir, yani istemci uygulamanız hizmete ses gönderdiğinde, aynı anda ve zaman uyumsuz olarak kısmi tanıma sonuçları geri alır.

Herhangi bir cihazda çalışan uygulamalardan konuşma hizmetini kullanmak isteyen geliştiriciler C# masaüstü kitaplığını kullanabilir. Kitaplığı kullanmak için, 32 bitlik bir platform için [Microsoft. ProjectOxford. SpeechRecognition-x86 NuGet paketini](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) ve 64 bit platform için [Microsoft. Projectoxford. SpeechRecognition-x64 adlı NuGet paketini](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) yükledikten sonra. İstemci kitaplığı API başvurusu için bkz. [Microsoft Speech C# masaüstü kitaplığı](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Aşağıdaki bölümlerde, C# C# masaüstü kitaplığı kullanılarak örnek uygulamanın nasıl yükleneceği, oluşturulacağı ve çalıştırılacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

### <a name="platform-requirements"></a>Platform gereksinimleri

Aşağıdaki örnek, Windows 8 + ve .NET Framework 4.5 + için [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs)kullanılarak geliştirilmiştir.

### <a name="get-the-sample-application"></a>Örnek uygulamayı al

[Konuşma C# masaüstü kitaplığı örnek](https://github.com/microsoft/cognitive-speech-stt-windows) deposundan örneği kopyalayın.

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Konuşma tanıma API 'sine abone olun ve ücretsiz deneme aboneliği anahtarı alın

Konuşma API 'SI bilişsel hizmetler 'in (daha önce Project Oxford) bir parçasıdır. Bilişsel [Hizmetler aboneliği](https://azure.microsoft.com/try/cognitive-services/) sayfasından ücretsiz deneme aboneliği anahtarlarına sahip olabilirsiniz. Konuşma API 'sini seçtikten sonra anahtarı almak için **API anahtarı al** ' ı seçin. Birincil ve ikincil anahtar döndürür. Her iki anahtar de aynı kotaya bağlıdır, bu nedenle her iki anahtarı da kullanabilirsiniz.

> [!IMPORTANT]
> * Abonelik anahtarı alın. Konuşma istemci kitaplıklarını kullanmadan önce bir [abonelik anahtarınız](https://azure.microsoft.com/try/cognitive-services/)olmalıdır.
>
> * Abonelik anahtarınızı kullanın. Sunulan C# masaüstü örnek uygulamasıyla, örneği çalıştırdığınızda abonelik anahtarınızı metin kutusuna yapıştırın. Daha fazla bilgi için bkz. [Örnek uygulamayı çalıştırma](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>1\. adım: Örnek uygulamayı yükler

1. Visual Studio 2015 ' u başlatın ve **Dosya** > **Aç** > **Proje/çözüm**' ı seçin.

2. İndirilen konuşma tanıma API dosyalarını kaydettiğiniz klasöre gidin. **Konuşma** > **pencerelerini**seçin ve ardından Sample-WP klasörünü seçin.

3. SpeechToText-WPF-Samples. sln adlı Visual Studio 2015 çözüm (. sln) dosyasını açmak için çift tıklayın. Çözüm, Visual Studio 'da açılır.

## <a name="step-2-build-the-sample-application"></a>2\. adım: Örnek uygulamayı oluşturma

1. *Tanımayı amaç ile birlikte*kullanmak istiyorsanız, önce [Language Understanding Intelligent Service (Luo)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)için kaydolmanız gerekir. Ardından, Samples/SpeechRecognitionServiceExample klasöründeki App. config dosyasındaki anahtarın `LuisEndpointUrl` değerini ayarlamak için lusıs uygulamanızın Endpoint URL 'sini kullanın. LUO uygulamasının uç nokta URL 'SI hakkında daha fazla bilgi için bkz. [uygulamanızı yayımlama](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > URL 'nin XML ayrıştırıcısı tarafından doğru şekilde yorumlandığından emin `&amp;` olmak için lusıs uç nokta URL 'sindeki karakteriiledeğiştirin.`&`

2. CTRL + SHIFT + B tuşlarına basın veya şerit menüsünde **Oluştur** ' u seçin. Ardından **Build Solution**öğesini seçin.

## <a name="step-3-run-the-sample-application"></a>3\. adım: Örnek uygulamayı çalıştırın

1. Derleme tamamlandıktan sonra, örneği çalıştırmak için F5 tuşuna basın veya şerit menüsünde **Başlat** ' ı seçin.

2. **Project Oxford konuşmayı metin örneği** penceresine gidin. Abonelik anahtarınızı, burada gösterilen metin kutusunu **başlatmak için abonelik anahtarınızı buraya yapıştırın** . Abonelik anahtarınızı PC veya dizüstü bilgisayarınızda kalıcı hale getirmek için **anahtarı kaydet**' i seçin. Abonelik anahtarını sistemden silmek için, **anahtarı sil** ' i seçerek bilgisayarınızdan veya dizüstü bilgisayarınızdan kaldırın.

   ![Konuşma tanıma yapıştırma anahtarı](../Images/SpeechRecog_paste_key.PNG)

3. **Konuşma tanıma kaynağı**altında, iki ana giriş kategorisine giren altı konuşma kaynağından birini seçin:

   * Konuşmayı yakalamak için bilgisayarınızın mikrofonunu veya takılı bir mikrofonu kullanın.
   * Ses dosyası çal.

   Her kategori üç tanıma moduna sahiptir:

    * **Shorttümcecik modu**: En fazla 15 saniye uzunluğunda bir utterlik. Veriler sunucuya gönderildiğinde, istemci birden çok kısmi sonuç ve birden çok n en iyi seçenek ile bir son sonuç alır.
    * **Longdikte modu**: En fazla iki dakikalık bir utterlik. Veriler sunucuya gönderilirken, istemci, tümce duraklamaları gösterdiği yere bağlı olarak birden çok kısmi sonuç ve birden çok nihai sonuç alır.
    * **Amaç algılama**: Sunucu, konuşma girişi hakkında ek yapılandırılmış bilgiler döndürür. Amaç algılamayı kullanmak için, önce [lusıs](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)kullanarak bir modeli eğmelisiniz.

Örnek ses dosyalarını bu örnek uygulamayla birlikte kullanın. Samples/SpeechRecognitionServiceExample klasörü altında bu örnekle indirdiğiniz Depodaki dosyaları bulun. Bu örnek ses dosyaları, **Shorttümcecik modu için wav dosyasını kullan** ' ı seçtiğinizde başka hiçbir dosya seçili değilse otomatik olarak çalışır veya konuşma girdisi olarak **longdikte modu Için wav dosyasını kullanabilirsiniz** . Şu anda yalnızca WAV ses biçimi destekleniyor.

![Konuşmayı metin arabirimine](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Açıklanan örnekler

### <a name="recognition-events"></a>Tanıma olayları

* **Kısmi sonuçlar olayları**: Bu olay, konuşma hizmeti, konuşmayı bitirmeden ( `MicrophoneRecognitionClient`kullanıyorsanız) veya veri göndermeyi ( `DataRecognitionClient`kullanıyorsanız) bir kez her tahmin edildiğinde çağrılır.
* **Hata olayları**: Hizmet bir hata algıladığında çağırılır.
* **Amaç olayları**: Son tanıma sonucu yapılandırılmış bir JSON amacına ayrıştırıldıktan sonra, "Withamacını" istemcilerinde (yalnızca Shorttümcecik modunda) çağırılır.
* **Sonuç olayları**:
  * `ShortPhrase` Modda bu olay çağrılır ve konuşmayı tamamladıktan sonra n en iyi sonuçları döndürür.
  * `LongDictation` Modunda, olay işleyicisi hizmetin cümle duraklamaları tanımladığı yere bağlı olarak birden çok kez çağrılır.
  * **Her n en iyi seçenek için**, tanınan metnin bir güvenirlik değeri ve birkaç farklı biçimi döndürülür. Daha fazla bilgi için bkz. [çıkış biçimi](../Concepts.md#output-format).

Olay işleyicileri kod açıklamaları biçimindeki kodda zaten kullanıma hazır.

## <a name="related-topics"></a>İlgili konular

* [Microsoft Speech masaüstü kitaplığı başvurusu](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Android 'de Java 'da Microsoft konuşma tanıma API 'SI ile çalışmaya başlama](GetStartedJavaAndroid.md)
* [İOS üzerinde amaç-C ' d a Microsoft konuşma tanıma API 'SI ile çalışmaya başlama](Get-Started-ObjectiveC-iOS.md)
* [JavaScript 'te Microsoft konuşma tanıma API 'SI ile çalışmaya başlama](GetStartedJSWebsockets.md)
* [REST aracılığıyla Microsoft konuşma tanıma API 'sini kullanmaya başlama](GetStartedREST.md)
