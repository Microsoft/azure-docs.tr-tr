---
title: Konuşma SDK 'Sı-konuşma hizmeti hakkında
titleSuffix: Azure Cognitive Services
description: Konuşma yazılım geliştirme seti (SDK) birçok konuşma hizmeti özelliği sunar ve böylece konuşma özellikli uygulamalar geliştirmeyi kolaylaştırır.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 65e55d8e9d81f4045dde29c943f034ed0a24bf4c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608094"
---
# <a name="about-the-speech-sdk"></a>Konuşma SDK'sı hakkında

Konuşma yazılım geliştirme seti (SDK), konuşma özellikli uygulamalar geliştirmenize olanak sağlamak için birçok konuşma hizmeti özelliği sunar. Konuşma SDK 'Sı birçok programlama dilinde ve tüm platformlarda kullanılabilir.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>Senaryo özellikleri

Konuşma SDK 'Sı, konuşma hizmetinden pek çok özelliği kullanıma sunar, ancak bunların tümünü kullanmaz. Konuşma SDK 'sının özellikleri genellikle senaryolarla ilişkilendirilir. Konuşma SDK 'Sı, yerel cihazlar, dosyalar, Azure Blob depolama, hatta giriş ve çıkış akışları kullanılarak hem gerçek zamanlı hem de gerçek zamanlı olmayan senaryolar için idealdir. Bir senaryo konuşma SDK 'Sı ile ulaşılabilir olmadığında, REST API alternatif bulun.

### <a name="speech-to-text"></a>Konuşmayı metne dönüştürme

[Konuşmadan metne](speech-to-text.md) ( *konuşma tanıma* olarak da bilinir), uygulamalarınızın, araçlarınızın veya cihazlarınızın tüketebileceği veya görüntüleyeceği metinlere ses akışları. Kullanıcı amaçlarını [Language Understanding (lusıs)](../luis/index.yml) ile birlikte kullanarak konuşma ve ses komutları üzerinde Kullanıcı hedefleri türetebilirsiniz. Konuşma girişini tek bir çağrıda farklı bir dile çevirmek için [konuşma çevirisini](speech-translation.md) kullanın. Daha fazla bilgi için bkz. [konuşmayı metne yönelik temel bilgiler](./get-started-speech-to-text.md).

**Konuşma tanıma (SR), tümcecik listesi, amaç, çeviri ve şirket içi kapsayıcılar** aşağıdaki platformlarda kullanılabilir:

  - C++/Windows & Linux & macOS
  - C# (Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (JRE ve Android)
  - JavaScript (Brower ve NodeJS)
  - Python
  - Swift
  - Objective-C  
  - Git (yalnızca SR)

### <a name="text-to-speech"></a>Metin okuma

[Metinden konuşmaya](text-to-speech.md) ( *konuşma sentezi* olarak da bilinir), metni insan benzeri birleştirilmiş konuşmaya dönüştürür. Giriş metni dize sabit değerleri ya da [konuşma Sensıs Işaretleme dili (SSML)](speech-synthesis-markup.md)kullanıyor. Standart veya sinir sesleri hakkında daha fazla bilgi için bkz. [metin okuma dili ve ses desteği](language-support.md#text-to-speech).

**Metinden konuşmaya (TTS)** aşağıdaki platformlarda kullanılabilir:

  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE ve Android)
  - Python
  - Swift
  - Objective-C
  - TTS REST API, her durumda kullanılabilir.

### <a name="voice-assistants"></a>Ses yardımcıları

Konuşma SDK 'sını kullanan [sesli yardımcılar](voice-assistants.md) , uygulamalarınız ve deneyimleriniz için doğal ve insan benzeri konuşma arabirimleri oluşturmanıza imkan tanır. Konuşma SDK 'Sı, tek bir bağlantıda konuşmadan metne, metinden konuşmaya ve konuşma verileri içeren hızlı ve güvenilir bir etkileşim sağlar. Uygulamanız, bot çerçevesinin doğrudan hat konuşma kanalını veya görev tamamlama için tümleşik özel komutlar hizmetini kullanabilir. Ayrıca, sesli yardımcılar benzersiz bir ses çıkışı deneyimi eklemek için [özel ses portalında](https://aka.ms/customvoice) oluşturulan özel sesleri kullanabilir.

**Ses Yardımcısı** desteği aşağıdaki platformlarda kullanılabilir:

  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (konuşma cihazları SDK)
  - Go

#### <a name="keyword-recognition"></a>Anahtar sözcük tanıma

[Anahtar sözcük tanıma](./custom-keyword-basics.md) kavramı, konuşma SDK 'sında desteklenir. Anahtar sözcük tanıma, konuşma içinde bir anahtar sözcüğü tanımlama ve anahtar sözcüğü işitme işlemi sırasında bir eylem tarafından yapılacak bir işlemdir. Örneğin, "Hey Cortana" Cortana yardımcısını etkinleştirir.

**Anahtar sözcük tanıma** özelliği aşağıdaki platformlarda kullanılabilir:

  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (konuşma cihazları SDK)
  - Anahtar sözcük tanıma işlevselliği herhangi bir mikrofon türü ile çalışabilir, ancak resmi anahtar sözcük tanıma desteği, şu anda Azure Kinect DK donanımında veya konuşma cihazları SDK 'sında bulunan mikrofon dizileri ile sınırlıdır

### <a name="meeting-scenarios"></a>Toplantı senaryoları

Konuşma SDK 'Sı, tek bir cihazdan veya çok cihazlı bir konuşmadan, bu toplantı senaryolarında kusursuz.

#### <a name="conversation-transcription"></a>Konuşma Transkripsiyonu

[Konuşma dökümü](conversation-transcription.md) , her konuşmacı için gerçek zamanlı (ve zaman uyumsuz) konuşma tanımayı, konuşmacı tanımlamayı ve tümce belirlenmesini sağlar (Ayrıca, daha da *bilinir).* Hoparlörleri, konuşmacıları ayırt etme imkanına sahip olmak için bir kişi içi toplantılar için mükemmeldir.

**Konuşma dökümü** aşağıdaki platformlarda kullanılabilir:

  - C++/Windows & Linux
  - C# (Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (konuşma cihazları SDK)

#### <a name="multi-device-conversation"></a>Çok cihazlı konuşma

[Birden çok cihazlı konuşmayla](multi-device-conversation.md), konuşma ve çeviri için kolay destek sunan konuşma tabanlı veya metin tabanlı iletiler göndermek üzere bir konuşmadaki birden fazla cihaza veya istemciye bağlanın.

**Çok cihazlı konuşma** aşağıdaki platformlarda kullanılabilir:

  - C++/Windows
  - C# (Framework & .NET Core)/Windows

### <a name="custom--agent-scenarios"></a>Özel/aracı senaryoları

Konuşma SDK 'Sı, telefon verilerinin oluşturulduğu, çağrı merkezi senaryoları için kullanılabilir.

#### <a name="call-center-transcription"></a>Çağrı merkezi Transkripsiyonu

[Çağrı merkezi dökümü](call-center-transcription.md) , etkileşimli sesli yanıt (IVR) gibi çeşitli sistemlerden gelmiş olabilecek büyük miktarlarda telefon verilerinin dökümünü almak için konuşmaya metne yönelik yaygın bir senaryodur. Bu telefon verilerini bir insan tarafından anlaşılması zor olduğu durumlarda bile, konuşma hizmeti Excel 'den en son konuşma tanıma modelleri.

**Çağrı merkezi dökümü** , REST API aracılığıyla Batch konuşma hizmeti aracılığıyla kullanılabilir ve herhangi bir durumda kullanılabilir.

### <a name="codec-compressed-audio-input"></a>Codec sıkıştırılmış ses girişi

Konuşma SDK 'Sı programlama dillerinin birkaçı codec sıkıştırılmış ses giriş akışlarını destekler. Daha fazla bilgi için bkz. <a href="/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">sıkıştırılmış ses girişi biçimlerini kullanma </a>.

**Codec ile sıkıştırılmış ses girişi** aşağıdaki platformlarda kullanılabilir:

  - C++/Linux
  - C#/Linux
  - Java/Linux, Android ve iOS

## <a name="rest-api"></a>REST API

Konuşma SDK 'Sı, konuşma hizmetinin pek çok özellik özelliğini ele alırken, bazı senaryolarda REST API kullanmak isteyebilirsiniz.

### <a name="batch-transcription"></a>Toplu iş transkripsiyonu

[Toplu iş dökümü](batch-transcription.md) , büyük hacimde verilerin zaman uyumsuz konuşmadan metne dökümünü sağlar. Toplu iş dökümü yalnızca REST API mümkündür. Konuşma sesini metne dönüştürmenin yanı sıra, toplu konuşma tanıma özelliği de, yaklaşım ve yaklaşım analizi için de izin verir.

## <a name="customization"></a>Özelleştirme

Konuşma hizmeti, konuşmadan metne, metinden konuşmaya ve konuşma çevirisi arasında varsayılan modelleriyle harika işlevler sunar. Bazen benzersiz kullanım çalışmalarınız ile daha iyi çalışmak için temel performansı artırmak isteyebilirsiniz. Konuşma hizmeti, size kolay hale getirir ve kendi verilerinize göre özel modellerle rekabet avantajı oluşturmanıza olanak sağlayan çeşitli kod içermeyen özelleştirme araçlarına sahiptir. Bu modeller yalnızca siz ve kuruluşunuz tarafından kullanılabilir.

### <a name="custom-speech-to-text"></a>Özel Konuşma Tanıma metin

Benzersiz bir ortamda tanıma ve bulma için konuşmayı metne dönüştürme işlemi yaparken, çevresel gürültü veya sektöre özgü sözlük için özel akustik, dil ve telaffuz modeller oluşturup eğitebilirsiniz. Kod içermeyen Özel Konuşma Tanıma modellerinin oluşturulması ve yönetilmesi [özel konuşma tanıma Portal](https://aka.ms/customspeech)aracılığıyla kullanılabilir. Özel Konuşma Tanıma modeli yayımlandıktan sonra konuşma SDK 'Sı tarafından tüketilebilir.

### <a name="custom-text-to-speech"></a>Özel metin okuma

Özel ses olarak da bilinen özel metin okuma, markanız için tanınabilir, tek bir tür ses oluşturmanıza olanak sağlayan bir dizi çevrimiçi araç. Kod içermeyen özel ses modellerinin oluşturulması ve yönetilmesi [özel ses portalı](https://aka.ms/customvoice)aracılığıyla kullanılabilir. Özel ses modeli yayımlandıktan sonra konuşma SDK 'Sı tarafından tüketilebilir.

## <a name="get-the-speech-sdk"></a>Konuşma SDK 'sını alın

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[Tarayıcı](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Sonraki adımlar

* [Ücretsiz bir Azure hesabı oluşturma](https://azure.microsoft.com/free/cognitive-services/)
* [Bkz. C 'de konuşmayı tanıma #](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)