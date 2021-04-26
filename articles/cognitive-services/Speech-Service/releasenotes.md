---
title: Sürüm notları-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti Özellik sürümlerinin, geliştirmelerin, hata düzeltmelerinin ve bilinen sorunların çalışan bir günlüğü.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: f97ecedd4088a825b9ec5a076f4da70df92a3269
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775412"
---
# <a name="speech-service-release-notes"></a>Konuşma hizmeti sürüm notları

## <a name="text-to-speech-2021-april-release"></a>Metinden konuşmaya 2021-Nisan sürümü

**Sinir TTS, 21 bölgede kullanılabilir**

- **On iki yeni bölge eklenmiştir** -sinir TTS artık bu yeni 12 bölgede kullanılabilir: `Japan East` , `Japan West` ,, `Korea Central` `North Central US` , `North Europe` , `South Central US` , `Southeast Asia` , `UK South` , `west Central US` , `West Europe` , `West US` , `West US 2` . Desteklenen 21 bölgenin tam listesi için [buraya](regions.md#text-to-speech) bakın.

## <a name="text-to-speech-2021-march-release"></a>Metinden konuşmaya 2021-Mart yayını

**Sinir TTS için eklenen yeni diller ve sesler**

- **Sunulan altı yeni dil** -6 yeni yerel ayar sinir TTS dil listesine eklendi: `cy-GB` Galce (Birleşik Krallık), `cy-GB` Galce (Birleşik Krallık), İngilizce (Filipinler), Rosa, `en-PH` Ingilizce (Filipinler), can `en-PH` İngilizce (Filipinler), Fransızca 'daki Charline (Belçika), Fransızca (Belçika), Felemenkçe (Belçika), Felemenkçe (Belçika), Felemenkçe (Belçika), 4., Ukrayna (Ukrayna), `fr-BE` `fr-BE` `nl-BE` `nl-BE` `uk-UA` Ostap `uk-UA` `ur-PK` `ur-PK` (Ukrayna), Ukrayna (Ukrayna), Uzma in Urduca (Pakistan),

- **Önizlemeden** 20 ' ye kadar olan 5 dilde 2020-Kasım ' de tanıtılan yerel-10 ses önizlemesi, şu anda bir ga: `et-EE` Estonya dili (Estonya), SETA (İrlanda), Litvanya 'daki ( `ga-IE` `lv-LV` `lt-LT` Litvanya `mt-MT` ), (Letonya

- **Fransızca (Kanada) Için yeni erkek ses eklendi** - `fr-CA` Fransızca (Kanada) için yeni bir ses Antoine yer verilir.

- **Kalite geliştirme** - `hu-HU` % 48,17, `nb-NO` Norveççe-52,76%, `nl-NL` felemenkçe (Hollanda)-22,11% üzerinde telaffuz hata oranı azaltma.

Bu sürümle birlikte, artık 60 dil/yerel ayarlarda Toplam 142 sinir sesi destekliyoruz. Ayrıca, 70 standart sesten fazla 49 dil/yerel ayarlar. Tam liste için [dil desteğini](language-support.md#text-to-speech) ziyaret edin.

**Karakterlere animasyon uygulamak için yüz poz olayları alın**

[Viseme olayı](how-to-speech-synthesis-viseme.md) , kullanıcıların yüz pozma sırasını ve sentezleştirilmiş konuşmayı almasına Izin veren sinir TTS 'ye eklenir. Viseme, 2B ve 3B avatar modellerinin hareketini denetlemek için kullanılabilir ve birleştirilmiş konuşmaya yönelik kusursuz bir şekilde eşleşen ağız hareketlerine sahip olur. Artık, viseme yalnızca en-US-AriaNeural Voice için geçerlidir.

**Konuşma birleştirme biçimlendirme dili (SSML) içine Bookmark öğesi ekleme**

[Yer işareti öğesi](speech-synthesis-markup.md#bookmark-element) , her işaretin ses akışındaki sapmasını almak için ssml 'de özel işaretçiler eklemenize olanak tanır. Metin veya etiket dizisindeki belirli bir konuma başvurmak için kullanılabilir.

## <a name="speech-sdk-1160-2021-march-release"></a>Konuşma SDK 1.16.0:2021-Mart yayını

> [!NOTE]
> Windows üzerinde konuşma SDK 'Sı, Visual Studio 2015, 2017 ve 2019 için paylaşılan Microsoft Visual C++ yeniden dağıtılabilir öğesine bağlıdır. [Buradan](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)indirin.

#### <a name="new-features"></a>Yeni özellikler

- **C++/c #/Java/Python**: Windows, Linux ve Android 'de herhangi bir medya biçimi için destek eklemek üzere GStreamer 'nin en son sürümüne taşındı (1.18.3). [Belgelere bakın](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams).
- **C++/c #/Java/Objective-C/Python**: sıkıştırılmış TTS/BIRLEŞTIRILMIŞ sesin SDK 'ya çözülmesi için destek eklendi. Çıktı ses biçimini PCM olarak ayarlarsanız ve sisteminizde GStreamer varsa, SDK bant genişliğini kaydetmek ve istemcideki sesin kodunu çözmek için otomatik olarak hizmetten sıkıştırılmış ses ister. `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` `false` Bu özelliği devre dışı bırakmak için ' i ayarlayabilirsiniz. [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable), [Amaç-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid), [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python)için ayrıntılar.
- **JavaScript**: Node.js kullanıcılar artık [ `AudioConfig.fromWavFileInput` API 'yi](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_)kullanabilir. Bu, [GitHub sorununun #252](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252)adresleridir.
- **C++/c #/Java/Objective-C/Python**: `GetVoicesAsync()` tüm kullanılabilir sensıs SESSESINI döndürmek için TTS yöntemi eklendi. [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods), [Amaç-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)ve [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods)için ayrıntılar.
- **C++/c #/Java/JavaScript/Objective-C/Python**: `VisemeReceived` TTS/Speech sensepi için eklenen olay, zaman uyumlu viseme animasyonunu döndürmelidir. [Belgelere bakın](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme).
- **C++/c #/Java/JavaScript/Objective-C/Python**: `BookmarkReached` TTS için olay eklendi. SSML girişinde yer işaretlerini ayarlayabilir ve her bir yer işareti için ses uzaklıklarını alabilirsiniz. [Belgelere bakın](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element).
- **Java**: konuşmacı tanıma API 'leri için destek eklendi. Ayrıntıları [burada bulabilirsiniz](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable).
- **C++/c #/Java/JavaScript/Objective-C/Python**: TTS (Webm16Khz16BitMonoOpus ve Webm24Khz16BitMonoOpus) Için webd kapsayıcısına sahip iki yeni çıkış ses biçimi eklenmiştir. Bunlar, Opus codec bileşeniyle ses akışı için daha iyi biçimlerdir. [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable), [JavaScript](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest), [Amaç-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat), [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python)için ayrıntılar.
- **C++/c #/Java**: konuşmacı tanıma senaryosu için ses profili alma desteği eklendi. [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)ve [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)için ayrıntılar.
- **C++/c #/Java/Objective-C/Python**: ses mikrofonu ve konuşmacı denetimi için ayrı paylaşılan kitaplık desteği eklendi. Bu, gerekli ses kitaplığı bağımlılıkları olmayan ortamlarda SDK 'Yı kullanmanıza izin verir.
- **Amaç-C/Swift**: şemsiye Header ile modül çerçevesi için destek eklendi. Bu, konuşma SDK 'sını iOS/Mac hedefi-C/Swift uygulamalarında bir modül olarak içeri aktarmaya izin verir. Bu, [GitHub sorununun #452](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452)adresleridir.
- **Python**: Python [3,9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) Için destek eklendi ve Python 'un [3,5 için son geçerlilik süresi](https://devguide.python.org/devcycle/#end-of-life-branches)için Python 3,5 için destek eklendi.

**Bilinen sorunlar**

- **C++/c #/Java**: `DialogServiceConnector` `CustomCommandsConfig` özel bir komut uygulamasına erişmek için kullanılamaz ve bunun yerine bir bağlantı hatasıyla karşılaşır. Bu, uygulama KIMLIĞINIZI ile isteğe el ile ekleyerek geçici bir çözüm olabilir `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)` . Beklenen davranışı `CustomCommandsConfig` sonraki sürümde geri yüklenecektir.

#### <a name="improvements"></a>Geliştirmeleri

- Konuşma ikililerinin bellek kullanımını ve disk boyut düzeyini azaltmaya yönelik çoklu sürüm çabamızın bir parçası olarak, Android ikili dosyaları artık %3 oranında %5 oranında küçüktür.
- C# başvuru belgelerimizin de daha iyi doğruluk, okunabilirlik ve görme bölümlerine bakın [.](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet)

#### <a name="bug-fixes"></a>Hata düzeltmeleri

- **JavaScript**: büyük WAV dosya üstbilgileri artık doğru ayrıştırıldı (üst bilgi dilimini 512 bayta büyütür). Bu, [GitHub sorununun #962](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962)adresleridir.
- **JavaScript**: Microphone akışı, tanımayı durdurmadan önce sonlanıyorsa mikrofon zamanlaması sorunu düzeltildi, konuşma tanımada Firefox 'ta çalışmayan bir sorunu ele alıyor.
- **JavaScript**: turnOn tamamlanmadan önce tarayıcı mikrofonu kapatmaya zorlarsa başlatma taahhüdünü doğru şekilde işleyeceğiz.
- **JavaScript**: URL bağımlılığını URL ayrıştırması ile değiştirdik. Bu, [GitHub sorununun #264](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264)adresleridir.
- **Android**: `minifyEnabled` true olarak ayarlandığında, sabit geri çağrılar çalışmıyor.
- **C++/c #/Java/Objective-C/Python**: `TCP_NODELAY` , gecikmeyi azaltmak IÇIN doğru şekilde TTS IÇIN temeldeki yuva GÇ olarak ayarlanır.
- **C++/c #/Java/Python/Objective-C/go**: bir tanıma başladıktan hemen sonra tanıyıcı yok edildiğinde zaman zaman çökme düzeltildi.
- **C++/c #/Java**: konuşmacı tanıyıcı yok etme sırasında zaman zaman kilitlenme düzeltildi.

#### <a name="samples"></a>Örnekler

- **JavaScript**: [tarayıcı örnekleri](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) artık ayrı bir JavaScript kitaplık dosyası indirmesi gerektirmez.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Konuşma CLı (SPX olarak da bilinir): 2021-Mart yayını

> [!NOTE]
> Azure konuşma hizmeti komut satırı arabirimi 'ni (CLı) [buradan](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics)kullanmaya başlayın. CLı, herhangi bir kod yazmadan Azure konuşma hizmetini kullanmanıza olanak sağlar.

#### <a name="new-features"></a>Yeni özellikler

- `spx intent`Amaç tanıma için, değiştirme komutu eklendi `spx recognize intent` .
- Tanı ve amaç, artık kullanarak sözcük hata oranını hesaplamak için Azure işlevlerini kullanabilir `spx recognize --wer url <URL>` .
- Recognize artık sonuçları kullanarak sonuçları VTT dosyaları olarak çıktısını alabilir `spx recognize --output vtt file <FILENAME>` .
- Gizli anahtar bilgileri artık hata ayıklama/ayrıntılı çıktısında görünmeyebilir.
- Toplu iş dökümü oluşturma içindeki içerik alanı için URL denetimi ve hata iletisi eklendi.

**Covıd-19 ortak test**:

Devam eden pandemik, mühendislerimizin evden çalışmasını gerektirirken, ön pandemik el ile doğrulama betikleri önemli ölçüde azaltılmıştır. Daha az yapılandırmaya sahip daha az cihaz üzerinde test ediyoruz ve ortama özgü hataların oluşma olasılığı artırılabilir. Büyük bir Otomasyon kümesiyle hala dikkatli bir şekilde doğrulama yaptık. Bir şey kaçırdığımız olası bir olayda, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)'da bize bildirin.<br>
Sağlıklı olun!

## <a name="text-to-speech-2021-february-release"></a>Metinden konuşmaya 2021-Şubat yayını

**Özel sinir ses GA**

13 Mart 'da Haziran ayında özel sinir Voice, GA: Çince (Mandarin, Basitleştirilmiş), Ingilizce (Avustralya), İngilizce (Hindistan), Ingilizce (Birleşik Krallık), Ingilizce (Birleşik Devletler), Fransızca (Kanada), Fransızca (Fransa), Almanca (Almanya), Italyanca (Italya), Japonca (Japonya), Korece (Kore), Portekizce (Brezilya), Ispanyolca (Meksika) ve Ispanyolca (Ispanya). [Özel sinir Voice nedir](custom-neural-voice.md) ve [BT 'nin nasıl kullanılacağı](concepts-guidelines-responsible-deployment-synthetic.md)hakkında daha fazla bilgi edinin. Özel sinir Voice özelliği kayıt gerektirir ve Microsoft, erişimi Microsoft 'un uygunluk ölçütlerine göre sınırlayabilir. [Sınırlı erişim](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context)hakkında daha fazla bilgi edinin.  

## <a name="speech-sdk-1150-2021-january-release"></a>Konuşma SDK 1.15.0:2021-Ocak sürümü

> [!NOTE]
> Windows üzerinde konuşma SDK 'Sı, Visual Studio 2015, 2017 ve 2019 için paylaşılan Microsoft Visual C++ yeniden dağıtılabilir öğesine bağlıdır. [Buradan](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)indirin.

**Vurgular Özeti**
- Daha az bellek ve disk kaplama, SDK 'Yı daha verimli hale getirir.
- Özel sinir ses özel önizlemesi için daha yüksek doğruluk çıkış biçimleri mevcuttur.
- Amaç tanıyıcı artık en üst amacından daha fazla dönüş alabilir ve müşterinizin amacı hakkında ayrı bir değerlendirme yapma olanağı sunar.
- Sesli yardımcınızın veya bot 'ın daha kolay bir şekilde ayarlanması daha kolay hale gelir ve anında dinlemeyi durdurabilir ve hatalara nasıl yanıt vereceğini daha fazla kontrol edebilirsiniz.
- Sıkıştırmayı isteğe bağlı hale getirerek cihaz performansı geliştirildi.
- Windows ARM/ARM64 'de konuşma SDK 'sını kullanın.
- Gelişmiş düşük düzey hata ayıklama.
- Telaffuz değerlendirmesi özelliği artık daha geniş kullanılabilir.
- Değerli müşterilerimiz, GitHub 'da bayrak eklenmiş sorunları gidermeye yönelik çeşitli hata düzeltmeleri! Teşekkür ederiz! Geri bildirimin geldiğini koruyun!

**Geliştirmeleri**
- Konuşma SDK 'Sı artık daha verimli ve hafif. Konuşma SDK 'sının bellek kullanımını ve disk parmak izini azaltmak için çoklu sürüm çabamız başladık. İlk adım olarak, çoğu platformda paylaşılan kitaplıklarda önemli dosya boyutu indirimleri yaptık. 1,14 sürümüyle karşılaştırıldığında:
  - 64-bit UWP uyumlu Windows kitaplıkları yaklaşık %30 daha küçüktür.
  - 32-bit Windows kitaplıkları henüz bir boyut geliştirmesi görmüyor.
  - Linux kitaplıkları% 20-25 daha küçüktür.
  - Android kitaplıkları% 3-5 daha küçüktür.

**Yeni özellikler**
- **Tümü**: TTS konuşma SENSıS API 'si aracılığıyla özel sinir sesinizin özel önizlemesi için kullanılabilen yeni 48kHz çıkış biçimleri: Audio48Khz192KBitRateMonoMp3, Audio-48kHz-192kbıbit-mono-MP3, Audio48Khz96KBitRateMonoMp3, Audio-48kHz-96kbit-mono-MP3, Raw48Khz16BitMonoPcm, ham-48kHz-16bit-mono-PCM, Riff48Khz16BitMonoPcm, Riff-48kHz-16bit-mono-PCM.
- **Tümü**: özel sesin kullanımı da daha kolay. `EndpointId`([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [Amaç-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)) aracılığıyla özel ses ayarlama desteği eklendi. Bu değişiklikten önce, özel sesli kullanıcıların uç nokta URL 'sini yöntemi aracılığıyla ayarlaması gerekir `FromEndpoint` . Artık müşteriler `FromSubscription` yalnızca genel sesler gibi yöntemi kullanabilir ve sonra DAĞıTıM kimliğini ayarla olarak sağlayabilir `EndpointId` . Bu, özel seslerin ayarlanmasını basitleştirir. 
- **C++/c #/Java/Objective-C/Python**: en üstteki amacından daha fazla bilgi alın `IntentRecognizer` . Artık, `LanguageUnderstandingModel FromEndpoint` URI parametresini kullanarak yalnızca en üstteki Puanlama amacını değil, tüm hedefleri IÇEREN JSON sonuçlarının yapılandırılmasını desteklemektedir `verbose=true` . Bu, [GitHub sorununun #880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880)adresleridir. Güncelleştirilmiş [belgelere bakın](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents).
- **C++/c #/Java**: sesli yardımcınızın veya bot 'ın hemen dinlemeyi durdurmasını sağlayın. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) artık eşlik eden bir `StopListeningAsync()` yönteme sahiptir `ListenOnceAsync()` . Bu işlem, ses yakalamayı hemen durdurur ve bir sonuç için düzgün şekilde bekleyip "Şimdi durdur" düğmesine basarak kullanımı kusursuz hale getirir.
- **C++/c #/Java/JavaScript**: ses yardımcınızı veya bot 'un temeldeki sistem hatalarıyla daha iyi tepki vermesini sağlayın. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) artık yeni bir `TurnStatusReceived` olay işleyicisine sahiptir. Bu isteğe bağlı olaylar [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) , bot üzerindeki her çözünürlüğe karşılık gelir ve doğrudan çizgi konuşma ve bot arasında işlenmeyen bir özel durum, zaman aşımı veya ağ bırakması sonucu olarak, gerçekleşen yürütme başarısızlıklarını rapor eder. `TurnStatusReceived` başarısızlık koşullarına yanıt vermeyi kolaylaştırır. Örneğin, bir bot, arka uç veritabanı sorgusunda çok uzun sürüyorsa (örn. bir ürün aranıyor), `TurnStatusReceived` istemcinin "Maalesef" ne yazık ki bunu bir kez daha deneyebilirsiniz "veya benzer bir şekilde yeniden sormasını sağlamasına izin verir.
- **C++/c #**: konuşma SDK 'sını daha fazla platformda kullanın. Konuşma SDK 'sı [NuGet paketi](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) artık, konuşma SDK 'sının daha fazla makine türü üzerinde daha yararlı olması IÇIN Windows ARM/ARM64 masaüstü yerel ikili dosyalarını (UWP zaten desteklenmektedir) desteklemektedir.
- **Java**: [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) artık `setSpeechActivityTemplate()` daha önce dilden yanlışlıkla dışlanan bir yöntemi vardır. Bu, özelliği ayarlamaya eşdeğerdir `Conversation_Speech_Activity_Template` ve doğrudan hat konuşma hizmeti tarafından oluşturulan tüm gelecek bot Framework etkinliklerinin, belirtilen IÇERIĞI JSON yükleriyle birleştirmesine yönelik istekleri ister.
- **Java**: gelişmiş düşük düzey hata ayıklama. [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection)Sınıfın artık `MessageReceived` diğer programlama dillerine (C++, C#) benzer bir olayı vardır. Bu olay, hizmetten gelen verilere alt düzey erişim sağlar ve tanılama ve hata ayıklama için yararlı olabilir.
- **JavaScript**: [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) artık, `fromHost()` `fromEndpoint()` özel hizmet konumlarının kullanımını kolaylaştıran ve fabrika yöntemlerine sahip olan ve bu sayede, daha sonra, özellikleri el ile ayarlayarak, sesli yardımcılar ve botları aracılığıyla daha kolay kurulum Ayrıca, `botId` yapılandırma fabrikaları genelinde varsayılan olmayan bir bot kullanmak için isteğe bağlı belirtimini de standartlıyoruz.
- **JavaScript**: WebSocket sıkıştırması için eklenen dize denetimi özelliği aracılığıyla cihaz performansı geliştirildi. Performans nedenleriyle, varsayılan olarak WebSocket sıkıştırmasını devre dışı bırakmış olma. Bu, düşük bant genişliği senaryolarında yeniden etkinleştirilebilir. Burada daha fazla ayrıntı [bulabilirsiniz](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid). Bu, [GitHub sorununun #242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242)adresleridir.
- **JavaScript**: konuşma söylenişi değerlendirmesini sağlamak üzere Söyleniş değerlendirmesi için destek eklendi. [Hızlı başlangıç](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)başlığına bakın.

**Hata düzeltmeleri**
- **Tümü** (JavaScript hariç): tanıyıcı tarafından çok fazla belleğin ayrıldığı sürüm 1,14 ' de bir gerileme düzeltildi.
- **C++**: ile bir atık toplama sorunu düzeltildi `DialogServiceConnector` , [GitHub sorunu #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794)adreslenir.
- **C#**: iş parçacığı kapatmada bir sorun düzeltildi.
- **C++/c #/Java**: bir uygulamanın konuşma yetkilendirme belirtecini veya etkinlik şablonunu bir kereden fazla açmasını engellediği bir özel durum düzeltildi `DialogServiceConnector` .
- **C++/c #/Java**: teartik içindeki bir yarış durumu nedeniyle tanıyıcı kilitlenme düzeltildi.
- **JavaScript**: [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) daha önce `botId` , fabrikalar içinde belirtilen isteğe bağlı parametreyi karşılamadı `BotFrameworkConfig` . Bu, `botId` sorgu dizesi parametresini varsayılan olmayan bir bot kullanmak üzere el ile ayarlamak için gerekli hale yaptı. Hata düzeltildi ve `botId` fabrikalar için belirtilen değerler `BotFrameworkConfig` , yeni ve eklemeler dahil olmak üzere kabul edilecek ve kullanılacaktır `fromHost()` `fromEndpoint()` . Bu, `applicationId` için parametresi için de geçerlidir `CustomCommandsConfig` .
- **JavaScript**: [hata #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881)düzeltildi, tanıyıcı nesnesi yeniden kullanıma izin veriliyor.
- **JavaScript**: SKD 'nin `speech.config` tek bir TTS oturumunda birden çok kez gönderdiği bir sorun düzeltildi ve bant genişliği boşa alındı.
- **JavaScript**: bir kullanıcının tarayıcısında mikrofon girişine izin verilmediği durumlarda daha açıklayıcı bir ileti vermesini sağlayan mikrofon yetkilendirmesi üzerinde Basitleştirilmiş hata işleme.
- **JavaScript**: ' de tür hatalarının bulunduğu ve [](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) `ConversationTranslator` `ConversationTranscriber` TypeScript kullanıcıları Için derleme hatasına neden olan düzeltilen GitHub sorunu #249.
- **Amaç-C**: GStreamer derlemesinin xcode 11,4 üzerinde iOS için başarısız olduğu bir sorun düzeltildi, [GitHub sorununun #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python**: düzeltilen [GitHub sorunu #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870), "Kullanımdan kaldırılmış cationwarning uyarısı: imp modülü ımportlib 'in kullanım dışı bırakılmıştır".

**Örnekler**
- [JavaScript tarayıcısı için dosya öncesi örnek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) artık konuşma tanıma için dosyaları kullanıyor. Bu, [GitHub sorununun #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884)adresleridir.

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Konuşma CLı (SPX olarak da bilinir): 2021-Ocak sürümü

**Yeni özellikler**
- Konuşma CLı artık bir [NuGet paketi](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) olarak kullanılabilir ve kabuk/komut satırından çağrın .NET genel aracı olarak .net CLI aracılığıyla yüklenebilir.
- [Özel konuşma tanıma DevOps şablonu deposu](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) , özel konuşma tanıma iş akışları IÇIN konuşma CLI kullanacak şekilde güncelleştirilmiştir.

**Covıd-19 iş temelli test**: devam eden pandemik, mühendislerimizin evden çalışmasını gerektirirken, ön pandemik el ile doğrulama betikleri önemli ölçüde azaltılmıştır. Daha az yapılandırmaya sahip daha az cihaz üzerinde test ediyoruz ve ortama özgü hataların oluşma olasılığı artırılabilir. Büyük bir Otomasyon kümesiyle hala dikkatli bir şekilde doğrulama yaptık. Bir şey kaçırdığımız olası bir olayda, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)'da bize bildirin.<br>
Sağlıklı olun!

## <a name="text-to-speech-2020-december-release"></a>Metinden konuşmaya 2020-Aralık yayını

**GA ve Preview 'da yeni sinir sesleri**

54 diller/yerel ayarlar arasında Toplam 129 sinir seste 51 yeni ses yayınlandı:

- **46 yeni ses GA yerel ayarlar**: Arapça `ar-EG` (Mısır), `ar-SA` Arapça (Suudi Arabistan), Shakir (Bulgaristan), Katalanca (Bulgarca), Katalanca (Ispanya), Marana, (Çek Cumhuriyeti), Danca (Danimarka) ile Jonas, Almanca (Avusturya), Almanca (İsviçre), Kuzey (Yunanistan), Yunanca (Yunanistan), İngilizce (Kanada), İngilizce (Kanada), Doğu ve İngilizce (İrlanda), Hintçe (Hindistan), Tamil dili (Hindistan), Katalanca (İspanya), en düşük (İsviçre), Finlandiya 'Ta kert (Estonya), Fince (Finlandiya), Kuzey Kuzey (Finlandiya), Fince (Finlandiya), Kuzey (Finlandiya), `bg-BG` `ca-ES` `cs-CZ` `da-DK` `de-AT` `de-CH` `el-GR` `en-CA` `en-IE` `en-IN` `en-IN` `en-IN` `en-IN` `es-ES` `et-EE` `fi-FI` `fi-FI` Kuzey (İsviçre), Içte, `fr-CH` `ga-IE` Avrı `he-IL` İbranice (Israil), Hırvatça 'de Srecko `hr-HR` (Hırvatistan), tamas Içinde (Macaristan), gadis (Endonezya), (Endonezya), (Macarca), (Endonezya), `hu-HU` `id-ID` LEONAS `lt-LT` Litvanca (Litvanya `lv-LV` `ms-MY` `mt-MT` ),, Letonca (Letonya), , Norveççe 'de Finn, Bokmål (Norveç), Norveççe (Norveç), Bokmål (Norveç), Hollanda dili (Hollanda), Hollanda dili (Hollanda), Felemenkçe (Hollanda), Lehçe (Polonya), Lehçe (Polonya), Portekizce (Brezilya), Portekizce (Brezilya), Portekizce (Brezilya), Portekizce (Brezilya), Çemel Svetlana Rusça (Rusya), Lukas: Slovakça (Slovakya), Slovence (Slovenya), İsviçre 'de Yark (Slovak), İsveççe (İsviçre), İsveç dili (İsveç), deniz WAT (İsviçre), Vietnam dili (Türkiye), `nb-NO` `nb-NO` `nl-NL` `nl-NL` `pl-PL` `pl-PL` `pt-BR` `pt-PT` `ro-RO` Vietnam dili `ru-RU` `ru-RU` `sk-SK` `sl-SI` `sv-SE` `sv-SE` `th-TH` `tr-TR` `vi-VN` (Vietnam), `zh-TW` Kiwanese Mandarin (Tayvan), YunJhe ın `zh-TW` Taiwanese Mandarin (Tayvan), `zh-HK` Çin Cantonetik (Hong Kong), `zh-HK` Çince cantonetik (Hong Kong) içinde wanlung.

- **Önizleme yerel ayarları 'nda 5 yeni ses**: `et-EE` Estonya dili (Estonya), birlikte `ga-IE` İrlanda 'da (İrlanda), Litvanya 'daki (Letonya), `lv-LV` LEONAS, `lt-LT` Litvanca (Litvanya `mt-MT` ), Joseph içinde (Malta)

Bu sürümle birlikte, artık 54 dil/yerel ayarlarda Toplam 129 sinir sesi destekliyoruz. Ayrıca, 70 standart sesten fazla 49 dil/yerel ayarlar. Tam liste için [dil desteğini](language-support.md#text-to-speech) ziyaret edin.

**Ses Içeriği oluşturma güncelleştirmeleri**
- Ses kategorileri ve ayrıntılı ses açıklamaları ile iyileştirilmiş sesli seçim Kullanıcı arabirimi. 
- Farklı dillerdeki tüm sinir seslerine yönelik ayarlama ayarı etkinleştirildi.
- Tarayıcı dilini temel alarak kullanıcı arabirimi yerelleştirmesini otomatik hale.
- `StyleDegree`Tüm `zh-CN` sinir seslerine yönelik etkin denetimler.
Yeni özellikleri kullanıma almak için [Ses Içeriği oluşturma aracı](https://speech.microsoft.com/audiocontentcreation) 'nı ziyaret edin. 

**Zh-CN seslerindeki güncelleştirmeler**
- Tüm `zh-CN` sinir sesleri, İngilizce konuşmayı destekleyecek şekilde güncelleştirildi.
- Tüm `zh-CN` sinir sesleri, iç içe ayarlamayı destekleyecek şekilde etkinleştirildi. En iyi şekilde ayarlanması için SSML veya ses Içeriği oluşturma aracı kullanılabilir.
- Tüm `zh-CN` Multi-Style sinir sesleri, denetimi destekleyecek şekilde güncelleştirildi `StyleDegree` . Duygu yoğunluğu (geçici veya güçlü) ayarlanabilir.
- `zh-CN-YunyeNeural`Farklı lamalar gerçekleştirebilen birden çok stili destekleyecek şekilde güncelleştirildi.

## <a name="text-to-speech-2020-november-release"></a>Metinden konuşmaya 2020-Kasım sürümü

**Önizlemede yeni yerel ayarlar ve sesler**
- Sinir TTS portföyüne **beş yeni ses ve dil** eklenmiştir. Bunlar şunlardır: Malta dili (Malta), y de Litvanca (Litvanya), Estonca (Estonya), Orla, Irlanda 'da (Irlanda) ve Everita, Letonca (Letonya) içinde mehil süresi.
- **`zh-CN` Birden çok stil ve rol içeren beş yeni ses desteği**: Xiaohan, Xiaomo, Xiaoruı, Xiaoxuan ve Yunxi.

> Bu sesler üç Azure bölgesinde genel önizlemede sunulmaktadır: EastUS, Güneydoğu ve WestEurope.

**Sinir TTS kapsayıcısı GA**
- Sinir TTS kapsayıcısı sayesinde geliştiriciler, belirli güvenlik ve veri idare gereksinimleri için kendi ortamlarında en doğal dijital seslerle konuşma birleştirmelerini çalıştırabilir. [Konuşma kapsayıcılarını yüklemeyi](speech-container-howto.md)denetleyin. 

**Yeni özellikler**
- **Özel ses**: etkin kullanıcıların bir bölgeden diğerine ses modeli kopyalaması Desteklenen uç nokta askıya alma ve sürdürme. [Portala](https://speech.microsoft.com/customvoice) buradan gidin.
- [SSML sessizlik etiketi](speech-synthesis-markup.md#add-silence) desteği. 
- Genel TTS ses kalitesi geliştirmeleri: NB-NO ' da geliştirilmiş sözcük düzeyi telaffuz doğruluğu. %53 telaffuz hatası azaltılmıştır.

> [Bu teknik blogda](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)daha fazla bilgi edinin.

## <a name="text-to-speech-2020-october-release"></a>Metin okuma 2020-Ekim yayını

**Yeni özellikler**
- Jenny yeni bir `newscast` stili destekler. Bkz. [SSML 'de konuşma stillerini kullanma](speech-synthesis-markup.md#adjust-speaking-styles).
- **Sinir sescifinet vocoder 'e yükseltilmiş ve daha yüksek ses uygunluğu ve daha hızlı senişme hızı**. Bu, senaryosu video Dubbing, ses kitapları veya çevrimiçi eğitim malzemeleri dahil olmak üzere Hi-Fi seslerine veya uzun etkileşimlere bağlı olan müşteriler için yararlanır. [Öykü hakkında daha fazla bilgi edinin ve teknik topluluk blogumuzda sesli örnekleri dinleyin](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Özel ses](https://speech.microsoft.com/customvoice)  &  [ses içeriği oluşturma Studio](https://speech.microsoft.com/audiocontentcreation) , 17 yerel ayara yereldir**. Kullanıcılar, daha kolay bir deneyim için Kullanıcı arabirimini kolayca yerel bir dile değiştirebilir.   
- **Ses Içeriği oluşturma**: XiaoxiaoNeural için stil derecesi denetimi eklendi; Artan 50 MS 'nin artımlı sonlarını içerecek şekilde özelleştirilmiş kesme özelliği. 

**Genel TTS ses kalitesi geliştirmeleri**
- İçinde gelişmiş sözcük düzeyi Okunuş doğruluğu `pl-PL` (hata oranı azaltma: %51) ve `fi-FI` (hata oranı azaltma: %58)
- `ja-JP`Sözlük senaryosu için geliştirilmiş tek sözcük okuma. %80 oranında azaltılmış telaffuz hatası.
- `zh-CN-XiaoxiaoNeural`: Geliştirilmiş yaklaşım/CustomerService/Newscast/kimerli/angın stili ses kalitesi.
- `zh-CN`: Geliştirilmiş Erhua telaffuz ve hafif ton ve iyileştirilmiş alan Prosody, intelligibility büyük ölçüde geliştirilmiştir. 

## <a name="speech-sdk-1140-2020-october-release"></a>Konuşma SDK 1.14.0:2020-Ekim yayını

> [!NOTE]
> Windows üzerinde konuşma SDK 'Sı, Visual Studio 2015, 2017 ve 2019 için paylaşılan Microsoft Visual C++ yeniden dağıtılabilir öğesine bağlıdır. [Buradan](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)indirin.

**Yeni özellikler**
- **Linux**: de, 10 ve Ubuntu 20,04 LTS için destek eklendi.
- **Python/amaç-C**: API için destek eklendi `KeywordRecognizer` . Belgeler [burada](./custom-keyword-basics.md)görünür.
- **C++/Java/c #**: aracılığıyla herhangi bir `HttpHeader` anahtar/değer ayarlamak için destek eklendi `ServicePropertyChannel::HttpHeader` .
- **JavaScript**: API için destek eklendi `ConversationTranscriber` . Belgeleri [buradan](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript)okuyun. 
- **C++/c #**: yeni `AudioDataStream FromWavFileInput` Yöntem eklendi (okuma için. WAV dosyaları) [burada (C++)](/cpp/cognitive-services/speech/audiodatastream) ve [burada (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/c #/Java/Python/Objective-C/Swift**: `stopSpeakingAsync()` metin okuma senmetini durdurmak için bir yöntem eklendi. Burada başvuru belgelerini [(C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), burada ( [C#)](/dotnet/api/microsoft.cognitiveservices.speech), burada ( [Java)](/java/api/com.microsoft.cognitiveservices.speech), burada ( [Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)ve [burada (hedef-C/Swift)](/objectivec/cognitive-services/speech/)okuyun.
- **C#, C++, Java**: `FromDialogServiceConnector()` `Connection` sınıfına yönelik bağlantı ve bağlantı kesme olaylarını izlemek için kullanılabilecek bir işlev eklendi `DialogServiceConnector` . Başvuru belgelerini [burada (C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [burada (C++)](/cpp/cognitive-services/speech/connection)ve [burada (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection)okuyun.
- **C++/c #/Java/Python/Objective-C/Swift**: konuşma söylenişini değerlendiren ve konuşmadan elde edilecek doğruluk ve akıcı hale karşı konuşmacıya geri bildirimde bulunan telaffuz değerlendirmesi için destek eklendi. [Buradaki](how-to-pronunciation-assessment.md)belgeleri okuyun.

**Son değişiklik**
- **JavaScript**: puldefdiooutputstream. Read (), bir iç taahhüdden yerel bir JavaScript taahhüdüne bir dönüş türü değişikliğine sahiptir.

**Hata düzeltmeleri**
- **Tümü**: `SetServiceProperty` belirli özel karakterlere sahip olan değerlerin yoksayıldı durumunda 1,13 gerileme düzeltildi.
- **C#**: Visual Studio 2019 üzerinde sabit Windows konsol örnekleri yerel dll 'leri bulamıyor.
- **C#**: akış giriş olarak kullanılıyorsa bellek yönetimiyle birlikte çökme düzeltildi `KeywordRecognizer` .
- **ObjectiveC/Swift**: Stream, tanıyıcı girişi olarak kullanılıyorsa bellek yönetimiyle giderilir.
- **Windows**: UWP üzerinde BT HFP/A2DP ile birlikte sabit bir ortak sorun sorunu var.
- **JavaScript**: iç hata ayıklama/hizmet bağıntıları için günlük kaydı ve yardım işlemlerini geliştirmek üzere oturum kimliklerinin sabit eşlemesi.
- **JavaScript**: `DialogServiceConnector` ilk çağrı yapıldıktan sonra çağrıları devre dışı bırakmak için düzeltilme `ListenOnce` .
- **JavaScript**: sonuç çıktısının yalnızca "basit" olacağı sorun düzeltildi.
- **JavaScript**: MacOS 'ta Safari 'de sürekli tanıma sorunu düzeltildi.
- **JavaScript**: yüksek istek işleme senaryosu için CPU yükünü azaltma.
- **JavaScript**: ses profili kayıt sonucunun ayrıntılarına erişime izin verin.
- **JavaScript**: içinde sürekli tanıma için çözüm eklendi `IntentRecognizer` .
- **C++/c #/Java/Python/Swift/ObjectiveC**: içinde australiaeast ve brazilsouth için hatalı URL düzeltildi `IntentRecognizer` .
- **C++/c #**: `VoiceProfileType` nesne oluştururken bir bağımsız değişken olarak eklendi `VoiceProfile` .
- **C++/c #/Java/Python/Swift/ObjectiveC**: `SPX_INVALID_ARG` belirli bir konumdan okunmaya çalışılırken potansiyel olarak düzeltildi `AudioDataStream` .
- **IOS**: Unity üzerinde konuşma tanıma ile çökme düzeltildi

**Örnekler**
- **ObjectiveC**: [buraya](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples)anahtar sözcük tanıma örneği eklendi.
- **C#/JavaScript**: burada konuşma dökümü için hızlı başlangıç eklendi [(c#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) ve [burada (JavaScript)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription).
- **C++/c #/Java/Python/Swift/ObjectiveC**: telaffuz değerlendirmesi için [buraya](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples) örnek eklendi
- **Xamarin**: hızlı başlangıç, en son Visual Studio [şablonuna güncelleştirildi](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin).

**Bilinen Sorun**
- DigiCert genel kök G2 sertifikası, HoloLens 2 ve Android 4,4 (KitKat) içinde varsayılan olarak desteklenmez ve konuşma SDK 'sını çalışır hale getirmek için sisteme eklenmesi gerekir. Sertifika, yakın gelecekte HoloLens 2 işletim sistemi görüntülerine eklenecektir. Android 4,4 müşterilerinin güncelleştirilmiş sertifikayı sisteme eklemesi gerekir.

**Covıd-19 ortak test:** Son birkaç hafta içinde uzaktan çalışma nedeniyle, normalde yaptığımız için el ile doğrulama testi yapamadık. Herhangi bir şeyi bozduğumuz ve otomatikleştirilmiş testlerimiz geçtiğinden hiçbir değişiklik yapmadık. Bir şey kaçırdığımız olası bir olayda, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)'da bize bildirin.<br>
Sağlıklı olun!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Konuşma CLı (SPX olarak da bilinir): 2020-Ekim yayını
SPX, Azure konuşma hizmetini kod yazmadan kullanmak için komut satırı arabirimidir. En son sürümü [buradan](./spx-basics.md)indirin. <br>

**Yeni özellikler**
- `spx csr dataset upload --kind audio|language|acoustic` – yalnızca URL 'lerden değil yerel verilerden veri kümeleri oluşturun.
- `spx csr evaluation create|status|list|update|delete` – yeni modelleri temel Truth/diğer modellerle karşılaştırın.
- `spx * list` – disk belleğine alınmayan deneyimi destekler (--top X--Skip X ' i gerektirmez).
- `spx * --http header A=B` – özel üst bilgileri destekleme (özel kimlik doğrulaması için Office 'e eklendi). 
- `spx help` – Geliştirilmiş metin ve arka onay metin rengi (mavi).

## <a name="text-to-speech-2020-september-release"></a>Metinden konuşmaya 2020-Eylül yayını

### <a name="new-features"></a>Yeni özellikler

* **Sinir TTS** 
    * **18 yeni dil/yerel ayarı destekleyecek şekilde genişletildi.** Bunlar Bulgarca, Çekçe, Almanca (Avusturya), Almanca (Isviçre), Yunanca, Ingilizce (Irlanda), Fransızca (Isviçre), Ibranice, Hırvatça, Macarca, Endonezya, Malay, Rumence, Slovakça, Slovence, Tamil dili, Telugu ve Vietnam. 
    * **Mevcut dillerdeki çeşitlerinizi zenginleştirmek için 14 yeni ses yayınlandı.** Bkz. [tam dil ve ses listesi](language-support.md#neural-voices).
    * **`en-US`Ve seslerdeki yeni konuşma stilleri `zh-CN` .** Ingilizce (ABD) içindeki yeni ses, sohbet botu, müşteri hizmeti ve yardımcı stillerini destekler. 10 yeni konuşma stili, zh-CN Voice, Xiaoxıao ile kullanılabilir. Ayrıca, Xiaoxıao sinir Voice `StyleDegree` ayarlamayı destekler. Bkz. [SSML 'de konuşma stillerini kullanma](speech-synthesis-markup.md#adjust-speaking-styles).

* **Kapsayıcılar: sinir TTS kapsayıcısı, 14 dilde kullanılabilen 16 sesle genel önizlemede kullanıma sunulmuştur.** [Sınır TTS Için konuşma kapsayıcıları dağıtma](speech-container-howto.md) hakkında daha fazla bilgi edinin  

[Ignite 2020 IÇIN TTS güncelleştirmelerinin tam duyurusunu](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) okuyun 

## <a name="text-to-speech-2020-august-release"></a>Metinden konuşmaya 2020-Ağustos yayını

### <a name="new-features"></a>Yeni özellikler

* **Sınır TTS: yeni konuşma stili `en-US` Aria sesi**. AriaNeural, haberleri okurken bir haber kasaseine benzer. ' Newscast-resmi ' stili daha ciddi bir şekilde, ' newscast-normal ' stili daha gevşek ve resmi olmayan bir şekilde ses. Bkz. [SSML 'de konuşma stillerini kullanma](speech-synthesis-markup.md).

* **Özel ses: eğitim verileri kalitesini otomatik olarak denetlemek için yeni bir özellik yayımlanmıştır**. Verilerinizi karşıya yüklediğinizde, sistem sesli ve döküm verilerinizin çeşitli yönlerini inceler ve ses modelinin kalitesini artırmak için sorunları otomatik olarak düzeltir veya filtreler. Bu, ses ve betik biçimine ek olarak sesinizin sesini, gürültü düzeyini, konuşma okunuşini, normalleştirmenin normalleştirilmiş metinle, sesin sessizliği ile hizalamasını ele alır. 

* **Ses Içeriği oluşturma: daha güçlü ses ayarlama ve ses yönetimi özelliklerini etkinleştirmeye yönelik yeni özellikler kümesi**.

    * Telaffuz: telaffuz ayarlama özelliği en son Fonem kümesine güncelleştirilir. Kitaplıktan doğru Fonem öğesini seçebilir ve seçtiğiniz sözcüklerin söylenişini geliştirebilirsiniz. 

    * İndir: ses "Indir"/"dışarı aktar" özelliği, paragrafa göre ses oluşturmayı destekleyecek şekilde geliştirilmiştir. Aynı dosya/SSML içindeki içeriği, birden çok ses çıkışı oluştururken düzenleyebilirsiniz. "Indir" dosya yapısı da iyileştiriliyor. Şimdi, tüm ses dosyalarını tek bir klasörde kolayca alabilirsiniz. 

    * Görev durumu: çok dosya dışarı aktarma deneyimi geliştirildi. Geçmişte birden çok dosyayı dışarı aktardığınızda, dosyalardan biri başarısız olursa, tüm görev başarısız olur. Ancak şimdi, diğer tüm dosyalar başarıyla verilecek. Görev raporu, daha ayrıntılı ve yapılandırılmış bilgilerle zenginleştirılmıştır. Tüm başarısız dosyalar ve cümleler için günlükleri artık raporla kontrol edebilirsiniz. 

    * SSML belgeleri: tüm ayarlama özelliklerinin nasıl kullanılacağına ilişkin kuralları kontrol etmenize yardımcı olmak için SSML belgesine bağlanır.

* **Ses listesi API 'Si, Kullanıcı dostu bir ekran adı ve sinir sesleri için desteklenen konuşma stillerini içerecek şekilde güncelleştirilir**.

### <a name="general-tts-voice-quality-improvements"></a>Genel TTS ses kalitesi geliştirmeleri

* İçin azaltılmış sözcük düzeyi Söyleniş hatası% `ru-RU` (%56 oranında hatalı) ve `sv-SE` (hata %49 oranında azaltılır)

* `en-US`%40 tarafından sinir sesde okuma Polyphony Word ile iyileştirilmiş. "Okuma", "canlı", "içerik", "kayıt", "nesne" vb. Polyphony kelimelerine örnek olarak verilebilir. 

* ' Deki soru tonu naturalness geliştirildi `fr-FR` . MOS (ortalama görüşün puanı) Kazancı: + 0,28

* Aşağıdaki seslerle ilgili olarak, %40 oranında uyumluluk ve genel performans hızına sahip vocoders güncelleştirildi.

    | Yerel Ayar | Ses |
    |---|---|    
    | `en-GB` | MIA |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denıse |
    | `ja-JP` | NANAMI |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Hata düzeltmeleri

* Ses Içeriği oluşturma aracı ile bir dizi hata düzeltildi 
    * Otomatik yenileme ile ilgili sorun düzeltildi. 
    * Güney Doğu Asya bölgesinde zh-CN ' de ses stilleriyle ilgili sorunlar düzeltildi.
    * ' Break ' etiketiyle dışarı aktarma hatası ve noktalama işaretleri de dahil olmak üzere, sabit kararlılık sorunu.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Yeni konuşmayı metne göre yerel ayarlar: 2020-Ağustos yayını
Konuşmadan metne, Ağustos ayında 2 Avrupa dilleri `cs-CZ` ve `hu-HU` 5 İngilizce yerel ayar ve en Güney Amerika ülkelerinden oluşan 19 ' a kadar yeni yerel ayar yayınlandı. Yeni yerel ayarların listesi aşağıda verilmiştir. Dil listesinin tamamını [buradan](./language-support.md)görebilirsiniz.

| Yerel Ayar  | Dil                          |
|---------|-----------------------------------|
| `cs-CZ` | Çekçe (Çek Cumhuriyeti)            | 
| `en-HK` | İngilizce (Hong Kong)               | 
| `en-IE` | İngilizce (İrlanda)                 | 
| `en-PH` | İngilizce (Filipinler)             | 
| `en-SG` | İngilizce (Singapur)               | 
| `en-ZA` | İngilizce (Güney Afrika)            | 
| `es-AR` | İspanyolca (Arjantin)               | 
| `es-BO` | İspanyolca (Bolivya)                 | 
| `es-CL` | İspanyolca (Şili)                   | 
| `es-CO` | İspanyolca (Kolombiya)                | 
| `es-CR` | İspanyolca (Kosta Rika)              | 
| `es-CU` | İspanyolca (Küa)                    | 
| `es-DO` | İspanyolca (Dominik Cumhuriyeti)      | 
| `es-EC` | İspanyolca (Ekvador)                 | 
| `es-GT` | İspanyolca (Guatemala)               | 
| `es-HN` | İspanyolca (Honduras)                | 
| `es-NI` | İspanyolca (Nikaragua)               | 
| `es-PA` | İspanyolca (Panama)                  | 
| `es-PE` | İspanyolca (Peru)                    | 
| `es-PR` | İspanyolca (Porto Riko)             | 
| `es-PY` | İspanyolca (Paraguay)                | 
| `es-SV` | İspanyolca (El Salvador)             | 
| `es-US` | İspanyolca (ABD)                     | 
| `es-UY` | İspanyolca (Uruguay)                 | 
| `es-VE` | İspanyolca (Venezuela)               | 
| `hu-HU` | Macarca (Macaristan)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Konuşma SDK 1.13.0:2020-Temmuz sürümü

> [!NOTE]
> Windows üzerinde konuşma SDK 'Sı, Visual Studio 2015, 2017 ve 2019 için paylaşılan Microsoft Visual C++ yeniden dağıtılabilir öğesine bağlıdır. [Buradan](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)indirin ve yükleyin.

**Yeni özellikler**
- **C#**: zaman uyumsuz konuşma dökümü için destek eklendi. [Belgelere bakın](./how-to-async-conversation-transcription.md).  
- **JavaScript**: hem [tarayıcı](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) hem de [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition)için konuşmacı tanıma desteği eklendi.
- **JavaScript**: otomatik dil algılama/dil kimliği desteği eklendi. [Belgelere bakın](./how-to-automatic-language-detection.md?pivots=programming-language-javascript).
- **Amaç-C**: [Çok cihazlı konuşma](./multi-device-conversation.md) ve [konuşma dökümü](./conversation-transcription.md)için destek eklendi. 
- **Python**: Windows ve Linux 'ta Python için sıkıştırılmış ses desteği eklendi. [Belgelere bakın](./how-to-use-codec-compressed-audio-input-streams.md). 

**Hata düzeltmeleri**
- **Tümü**: bir tanımadan sonra keywordtanıyıcı 'e akış ilerlememesine neden olan bir sorun düzeltildi.
- **Tümü**: bir KeywordRecognitionResult öğesinden elde edilen akışa neden olan bir sorun düzeltildi anahtar sözcüğünü içermemelidir.
- **Tümü**: SendMessageAsync 'in, kullanıcıların bekleme süresinden önce iletiyi bir ileti göndermediği bir sorun düzeltildi.
- **Tümü**: kullanıcılar, VoiceProfileClient:: Hoparlörkerreckayıtlarını Profileasync yöntemini birden çok kez çağırıyorsa ve çağrıların bitmesini beklemediğinde konuşmacı tanıma API 'lerinde çökme düzeltildi.
- **Tümü**: sıkıştırılmış dosya günlüğü, VoiceProfileClient ve Hoparlörkertanıyıcı sınıflarında düzeltildi.
- **JavaScript**: tarayıcı simge durumuna küçültüldüğünde azaltma ile ilgili bir [sorun](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) düzeltildi.
- **JavaScript**: akışlarda bellek sızıntısı ile ilgili bir [sorun](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) düzeltildi.
- **JavaScript**: NodeJS ' den OCSP yanıtları için önbelleğe alma eklendi.
- **Java**: BigInteger alanlarının her zaman 0 döndürmesine neden olan bir sorun düzeltildi.
- **iOS**: IOS uygulama mağazası 'NDA konuşma SDK tabanlı uygulamalar yayımlamayla ilgili bir [sorun](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) düzeltildi.

**Örnekler**
- **C++**: [buraya](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)konuşmacı tanıma için örnek kod eklendi.

**Covıd-19 ortak test:** Son birkaç hafta içinde uzaktan çalışma nedeniyle, normalde yaptığımız için el ile doğrulama testi yapamadık. Herhangi bir şeyi bozduğumuz ve otomatikleştirilmiş testlerimiz geçtiğinden hiçbir değişiklik yapmadık. Bir şey kaçırdığımız olası bir olayda, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)'da bize bildirin.<br>
Sağlıklı olun!

## <a name="text-to-speech-2020-july-release"></a>Metinden konuşmaya 2020-Temmuz sürümü

### <a name="new-features"></a>Yeni özellikler

* **Sınır TTS, 15 yeni sinir sesleri**: sinir TTS portföyüne eklenen yeni sesler `ar-EG` Arapça (Mısır), `ar-SA` Arapça (Suudi Arabistan), Arapça (Suudi Arabistan), KAG (Danimarka), (Danimarka), Danca (Danimarka), `ca-ES` `da-DK` serja, `es-IN` İngilizce (Hindistan), Noora (Hindistan), Hinduca 'daki `fi-FI` Swara (Hindistan), Felemenkçe ( `hi-IN` `nl-NL` Hollanda), Zofia, Lehçe (Polonya), Portekizce (Portekiz), Rusça 'daki `pl-PL` `pt-PT` darda (Rusya) `ru-RU` `sv-SE` ), `th-TH` Tay dili (Tayland), `zh-HK` Çince (Cantoneo, geleneksel) ve Hsıaoyu `zh-TW` Çince (Taiwanese Mandarin) dilinde hiugaaı. Desteklenen tüm [dilleri](./language-support.md#neural-voices)denetleyin.  

* **Kullanıcı deneyimini basitleştirmek için eğitim akışıyla özel ses, akıcı bir sesli test**: yeni test özelliği ile her ses, her bir dil için en iyi duruma getirilmiş önceden tanımlanmış bir test kümesiyle otomatik olarak test edilir ve bu da genel ve sesli yardım senaryolarını kapsar. Bu test kümeleri dikkatle seçilir ve genel kullanım durumlarını ve telefon dilini içerecek şekilde test edilir. Bunun yanı sıra, kullanıcılar bir modeli eğitmek için kendi test betiklerini karşıya yüklemeyi yine de seçebilir.

* **Ses Içeriği oluşturma: daha güçlü ses ayarlama ve ses yönetimi özelliklerini etkinleştirmek için yeni özellikler kümesi yayınlandı**

    * `Pitch`, `rate` ve, `volume` yavaş, orta ve hızlı gibi önceden tanımlanmış bir değerle ayarlamayı destekleyecek şekilde geliştirilmiştir. Artık kullanıcıların ses düzenlemesi için bir ' sabit ' değeri seçmesini kolaylaştırır.

    ![Ses ayarlama](media/release-notes/audio-tuning.png)

    * Kullanıcılar artık `Audio history` kendi iş dosyalarını gözden geçirebilir. Bu özellik sayesinde kullanıcılar, çalışan bir dosyayla ilgili olarak üretilen tüm sesleri kolayca izleyebilir. Bu kişiler geçmiş sürümünü denetleyebilir ve aynı zamanda ayarlama sırasında kaliteyi karşılaştırabilir. 

    ![Ses geçmişi](media/release-notes/audio-history.png)

    * `Clear`Özellik artık daha esnektir. Kullanıcılar belirli bir ayarlama parametresini temizleyerek, seçilen içerik için diğer parametreleri kullanılabilir tutarken bu parametreyi temizleyebilir.  

    * Kullanıcıların TTS ses ayarlama ve ses yönetimi ile hızlı bir şekilde çalışmaya yardımcı olması için [giriş sayfasına](https://speech.microsoft.com/audiocontentcreation) bir öğretici videosu eklenmiştir. 

### <a name="general-tts-voice-quality-improvements"></a>Genel TTS ses kalitesi geliştirmeleri

* Daha yüksek doğruluk ve daha düşük gecikme için ' de geliştirilmiş TTS vocoder.

    * İçindeki Elsa 'yı `it-IT` , + 0,464 CMOS (karşılaştırmalı ortalama görüşeri puanı) ile sesli kalite ve %40 daha hızlı bir şekilde elde edilen yeni bir vocoder ve ilk bayt gecikmesi üzerinde %30 oranında azaltma. 
    * `zh-CN`Genel etki 0,348 alanı için + 0148 CMOS kazancı ve Lyrical stili için + 0,195 ile yeni vocoder 'Da Xiaoxıao 'da güncelleştirilmiş. 

* `de-DE` `ja-JP` TTS çıkışını daha doğal hale getirmek için güncelleştirilmiş ve ses modelleri.
    
    * `de-DE`En son Prosody modelleme yöntemiyle Katja güncellenmiştir, (yani, puan puanı) kazanç + 0,13 ' dir. 
    * `ja-JP`Yeni bir Aralık Vurgu Prosody modeliyle birlikte NANAMI güncelleştirildi 0,19,  

* Beş dilde sözcük düzeyinde telaffuz doğruluğu geliştirildi.

    | Dil | Telaffuz hatası azaltma |
    |---|---|
    | `en-GB` | %51 |
    | `ko-KR` | 17 |
    | `pt-BR` | %39 |
    | `pt-PT` | %77 |
    | `id-ID` | %46 |

### <a name="bug-fixes"></a>Hata düzeltmeleri

* Para birimi okuma
    * Ve için para birimi okumada sorun düzeltildi `es-ES``es-MX`
     
    | Dil | Giriş | İyileştirmelerden sonra yeniden okuma |
    |---|---|---|
    | `es-MX` | $1,58 | su cincuenta y Ocho centavos kaldır |
    | `es-ES` | $1,58 | dólar cincuenta y Ocho centavos 'yı kaldır |

    * Aşağıdaki yerel ayarlarda negatif para birimi ("-€325" gibi) için destek: `en-US` , `en-GB` ,, `fr-FR` `it-IT` , `en-AU` , `en-CA` .

* ' De geliştirilmiş Adres okuma `pt-PT` .
* `en-AU` `en-UK` "For" ve "dört" kelimesiyle düzeltilen Natasha () ve Libby () sorunları.  
* Ses Içeriği oluşturma aracında düzeltilen hatalar
    * İkinci paragraf sabitledikten sonra ek ve beklenmeyen duraklama.  
    * ' Kesme yok ' özelliği bir gerileme hatası ile geri eklenmez. 
    * Konuşma Studio 'nun rastgele yenileme sorunu düzeltildi.  

### <a name="samplessdk"></a>Örnekler/SDK

* JavaScript: Firefox 'ta kayıttan yürütme sorununu ve macOS ve iOS üzerinde Safari 'yi düzeltir. 

## <a name="speech-sdk-1121-2020-june-release"></a>Konuşma SDK 1.12.1:2020-Haziran sürümü
**Konuşma CLı (SPX olarak da bilinir)**
-   CLı içi yardım arama özellikleri eklendi:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Yeni dağıtılan v 3.0 toplu Işi ve Özel Konuşma Tanıma API 'Leriyle çalışacak şekilde güncelleştirildi:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Yeni özellikler**
-   **C \# , C++**: konuşmacı tanıma önizlemesi: Bu özellik, konuşmacı tanımlamasını (kim konuşuyor?) ve konuşmacı doğrulamayı (talep ettikleri konuşmacı mi?) sağlar. Bir [genel bakış](./speaker-recognition-overview.md)ile başlayın, [Konuşmacı Tanıma temel bilgileri makalesini](./get-started-speaker-recognition.md)veya [API başvuru belgelerini](/rest/api/speakerrecognition/)okuyun.

**Hata düzeltmeleri**
-   **C \# , C++**: sabit mikrofon kaydı, konuşmacı tanımada 1,12 ' de çalışmıyor.
-   **JavaScript**: Firefox 'Ta metin okuma ve MacOS ve IOS için Safari düzeltmeleri.
-   Sekiz kanallı akış kullanılırken konuşma dökümindeki Windows uygulama Doğrulayıcı erişim ihlali kilitlenmesiyle ilgili çözüm.
-   Çok cihazlı konuşma çevirisi için Windows uygulama Doğrulayıcı erişimi ihlali kilitlenme.

**Örnekler**
-   **C#**: konuşmacı tanıma için [kod örneği](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) .
-   **C++**: konuşmacı tanıma için [kod örneği](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) .
-   **Java**: Android 'de amaç tanıma için [kod örneği](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) . 

**Covıd-19 ortak test:** Son birkaç hafta içinde uzaktan çalışma nedeniyle, normalde yaptığımız için el ile doğrulama testi yapamadık. Herhangi bir şeyi bozduğumuz ve otomatikleştirilmiş testlerimiz geçtiğinden hiçbir değişiklik yapmadık. Bir şey kaçırdığımız olası bir olayda, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)'da bize bildirin.<br>
Sağlıklı olun!


## <a name="speech-sdk-1120-2020-may-release"></a>Konuşma SDK 1.12.0:2020-yayın Mayıs
**Konuşma CLı (Ayrıca, SPX olarak da bilinir)**
- **SPX** , komut satırından tanıma, birleştirme, çeviri, toplu iş dökümü ve özel konuşma yönetimi gerçekleştirmenize olanak sağlayan yeni bir komut satırı aracıdır. Konuşma hizmetini test etmek veya gerçekleştirmeniz gereken konuşma hizmeti görevlerinin kodunu almak için kullanın. Aracı indirin ve belgeleri [buradan](./spx-overview.md)okuyun.

**Yeni özellikler**
- **Go**: [konuşma tanıma](./get-started-speech-to-text.md?pivots=programming-language-go) ve [özel ses Yardımcısı](./quickstarts/voice-assistants.md?pivots=programming-language-go)için yeni go dili desteği. Geliştirme ortamınızı [buradan](./quickstarts/setup-platform.md?pivots=programming-language-go)ayarlayın. Örnek kod için aşağıdaki örnekler bölümüne bakın. 
- **JavaScript**: metin okuma için tarayıcı desteği eklendi. [Belgelere bakın](./get-started-text-to-speech.md?pivots=programming-language-JavaScript).
- **C++, C#, Java**: `KeywordRecognizer` Windows, Android, Linux & iOS platformlarında desteklenen yeni nesne ve API 'ler. [Buradaki](./custom-keyword-overview.md)belgeleri okuyun. Örnek kod için aşağıdaki örnekler bölümüne bakın. 
- **Java**: çeviri desteğiyle çok cihazlı konuşma eklendi. [Buraya](/java/api/com.microsoft.cognitiveservices.speech.transcription)başvuru belgesi bölümüne bakın.

**İyileştirmeler & iyileştirmeler**
- **JavaScript**: iyileştirilmiş tarayıcı mikrofon uygulamasının konuşma tanıma doğruluğunu artırma.
- **Java**: yeniden düzenlenmiş olmadan doğrudan JNI uygulamasını kullanarak bağlamaları. Bu değişiklik, Windows, Android, Linux ve Mac için kullanılan tüm Java paketlerinin bağlama boyutu olan 10 kat 'i azaltır ve konuşma SDK 'sı Java uygulamasının daha fazla geliştirilmesini kolaylaştırır.
- **Linux**: en son RHEL 7 özel notlarıyla destek [belgeleri](./speech-sdk.md?tabs=linux) güncelleştirildi.
- Hizmet ve ağ hataları oluştuğunda birden çok kez bağlanmayı denemek için geliştirilmiş bağlantı mantığı.
- [Portal.Azure.com](https://portal.azure.com) konuşma hızlı başlangıç sayfası, geliştiricilerin Azure konuşma yolculuğunda bir sonraki adımı geçirmesine yardımcı olacak şekilde güncelleştirildi.

**Hata düzeltmeleri**
- **C#, Java**: Linux ARM 'de SDK kitaplıkları yükleme ile ilgili bir [sorun](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) düzeltildi (32 bit ve 64 bit).
- **C#**: translationtanıyıcı, sattanıyıcı ve bağlantı nesneleri için yerel tanıtıcıların sabit olarak çıkarılması düzeltildi.
- **C#**: konuşma nesnesi için sabit ses girişi yaşam süresi yönetimi.
- `IntentRecognizer`Basit tümceciklerden amaçları tanıma sırasında sonuç nedeninin düzgün şekilde ayarlanmadığından oluşan bir sorun düzeltildi.
- `SpeechRecognitionEventArgs`Sonuç kaydırmasına doğru şekilde ayarlanmayan bir sorun düzeltildi.
- SDK 'nın WebSocket bağlantısını açmadan önce bir ağ iletisi gönderilmeye çalıştığı yarış durumu düzeltildi. Katılımcılar eklenirken için tekrarlandı `TranslationRecognizer` .
- Anahtar sözcük tanıyıcı altyapısında sabit bellek sızıntıları.

**Örnekler**
- **Git**: [konuşma tanıma](./get-started-speech-to-text.md?pivots=programming-language-go) ve [özel ses Yardımcısı](./quickstarts/voice-assistants.md?pivots=programming-language-go)için hızlı başlangıç eklendi. Örnek kodu [burada](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)bulabilirsiniz. 
- **JavaScript**: [metinden konuşmaya](./get-started-text-to-speech.md?pivots=programming-language-javascript), [çeviri](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)ve [Amaç tanıma](./get-started-intent-recognition.md?pivots=programming-language-javascript)için hızlı başlangıçlara eklendi.
- [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) ve [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) için anahtar sözcük tanıma örnekleri (Android).  

**Covıd-19 ortak test:** Son birkaç hafta içinde uzaktan çalışma nedeniyle, normalde yaptığımız için el ile doğrulama testi yapamadık. Herhangi bir şeyi bozduğumuz ve otomatikleştirilmiş testlerimiz geçtiğinden hiçbir değişiklik yapmadık. Bir şeyler kaçırdık, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)'da bize bildirin.<br>
Sağlıklı olun!

## <a name="speech-sdk-1110-2020-march-release"></a>Konuşma SDK 1.11.0:2020-Mart yayını
**Yeni özellikler**
- Linux: sistemi konuşma SDK 'Sı için yapılandırma [yönergeleriyle](./how-to-configure-rhel-centos-7.md) birlikte Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 Için destek eklendi.
- Linux: Linux ARM32 ve ARM64 üzerinde .NET Core C# desteği eklendi. Daha fazla bilgi için [buraya](./speech-sdk.md?tabs=linux) bakabilirsiniz. 
- C#, C++: `UtteranceId` içinde `ConversationTranscriptionResult` , tüm hammaddeleri ve nihai konuşma tanıma sonucunda TUTARLı bir kimlik eklenmiştir. [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult)için ayrıntılar.
- Python: için destek eklendi `Language ID` . [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console)deposunda speech_sample. Kopyala bölümüne bakın.
- Windows: tüm Win32 konsol uygulamaları için Windows platformunda sıkıştırılmış ses girişi biçim desteği eklendi. Ayrıntıları [burada bulabilirsiniz](./how-to-use-codec-compressed-audio-input-streams.md). 
- JavaScript: NodeJS 'de konuşma birleştirme (metinden konuşmaya) desteği. [Burada](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech) daha fazla bilgi edinin. 
- JavaScript: tüm gönderme ve alma iletilerinin incelemesini sağlamak için yeni API 'ler ekleyin. [Burada](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript) daha fazla bilgi edinin. 
        
**Hata düzeltmeleri**
- C#, C++: bir sorun düzeltildi, bu nedenle `SendMessageAsync` ikili ileti artık ikili tür olarak gönderilir. [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](/cpp/cognitive-services/speech/connection)için ayrıntılar.
- C#, C++: `Connection MessageReceived` `Recognizer` nesnesinden önce atıldığı takdirde olay kullanmanın kilitlenmeye neden olabileceği bir sorun düzeltildi `Connection` . [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived)için ayrıntılar.
- Android: gecikme süresini artırmak için mikrofondan gelen ses arabellek boyutu, 800ms 'den 100ms 'e düşer.
- Android: Android Studio 'de x86 Android öykünücüsü ile ilgili bir [sorun](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) düzeltildi.
- JavaScript: API ile Çin 'deki bölgeler için destek eklendi `fromSubscription` . Ayrıntıları [burada bulabilirsiniz](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-). 
- JavaScript: NodeJS ' den bağlantı hatalarıyla ilgili daha fazla hata bilgisi ekleyin.
        
**Örnekler**
- Unity: amaç tanıma genel örneği düzeltildi, burada LUSıS JSON içeri aktarma başarısız oldu. Ayrıntıları [burada bulabilirsiniz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: için örnek eklendi `Language ID` . Ayrıntıları [burada bulabilirsiniz](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Covid19 abişşlı testi:** Son birkaç hafta içinde uzaktan çalışırken, normalde yaptığımız kadar el ile cihaz doğrulama testi yapamadık. Örneğin, Linux, iOS ve macOS 'ta mikrofon girişini ve konuşmacı çıkışını sınayamadık. Bu platformlarda herhangi bir şeyi bozduğumuz ve otomatikleştirilmiş testlerimiz geçtiğinden hiçbir değişiklik yapmadık. Bir şey kaçırdığımız olası bir olayda, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)'da bize bildirin.<br> Devam eden destek için teşekkürler. Her zaman, lütfen [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) veya [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731)ilgili sorularınızı veya geri bildirimleri gönderin.<br>
Sağlıklı olun!

## <a name="speech-sdk-1100-2020-february-release"></a>Konuşma SDK 1.10.0:2020-Şubat yayını

**Yeni özellikler**

 - Python 'un yeni 3,8 sürümünü desteklemek için Python paketleri eklendi.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 desteği (C++, C#, Java, Python).
   > [!NOTE] 
   > Müşterilerin OpenSSL 'yi [Bu yönergelere](./how-to-configure-openssl-linux.md)göre yapılandırması gerekir.
 - DeARM32 ve Ubuntu için Linux desteği.
 - DialogServiceConnector artık BotFrameworkConfig üzerinde isteğe bağlı "bot ID" parametresini destekliyor. Bu parametre, tek bir Azure konuşma kaynağı ile birden çok doğrudan hat konuşma 'nın kullanımına izin verir. Belirtilen parametre olmadan, varsayılan bot (doğrudan hat konuşma kanalı yapılandırma sayfası tarafından belirlendiği şekilde) kullanılacaktır.
 - DialogServiceConnector artık bir SpeechActivityTemplate özelliğine sahip. Bu JSON dizesinin içeriği, konuşma tanıma gibi olaylara yanıt olarak otomatik olarak oluşturulan etkinlikler dahil olmak üzere doğrudan hat konuşma bot 'a ulaşan tüm etkinliklerde desteklenen çok çeşitli alanları önceden doldurmak için kullanılır.
 - Şimdi TTS, kimlik doğrulaması için abonelik anahtarını kullanır, birleştirici oluşturulduktan sonra ilk sende ilk bayt gecikme süresini azaltır.
 - Ortalama% 18,6 bir sözcük hata oranı azalmasıyla ilgili 19 yerel ayar için konuşma tanıma modelleri güncelleştirildi (ES-ES, es-MX, fr-CA, fr-FR, It-IT, ja-JP, ko-KR, PT-BR, zh-CN, zh-HK,, NB-NO, Fi-FL, ru-RU, PL-PL, CA-ES, zh-TW, TH-, Yeni modeller, dikte, Call-Center döküm ve video dizin oluşturma senaryoları dahil olmak üzere birden çok etki alanı arasında önemli geliştirmeler sunar.

**Hata düzeltmeleri**

 - Sohbet 'in JAVA API 'Lerinde düzgün bir şekilde beklemediği düzeltilen hata düzeltildi 
 - Xamarin [GitHub sorunu](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363) için Android x86 öykünücü düzeltilmesi
 - Eksik Ekle (Get | Özellik yöntemlerini AudioConfig olarak ayarla
 - Bağlantı başarısız olduğunda audioDataStream 'in durdurulabileceği bir TTS hatasını çözme
 - Bölge olmadan bir uç nokta kullanılması, konuşma çevirmeni için USP hatalarının oluşmasına neden olur
 - Evrensel Windows uygulamalarında KIMLIK oluşturma artık uygun bir benzersiz GUID algoritması kullanıyor; daha önce ve yanlışlıkla büyük etkileşimler kümesi üzerinde çarpışmaları üreten bir saplaması uygulamasına varsayılan olarak ayarlanır.
 
 **Örnekler**
 
 - [Unity mikrofon ve gönderim modu akışı](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone) ile konuşma SDK 'sını kullanmak için Unity örneği

**Diğer değişiklikler**

 - [Linux için OpenSSL yapılandırma belgeleri güncelleştirildi](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Konuşma SDK 1.9.0:2020-Ocak sürümü

**Yeni Özellikler**

- Çoklu cihaz konuşması: birden çok cihazı aynı konuşmaya veya metin tabanlı konuşmaya bağlayın ve isteğe bağlı olarak bunlar arasında gönderilen iletileri çevirin. [Bu makalede](multi-device-conversation.md)daha fazla bilgi edinin. 
- Android. AAR paketi için anahtar sözcük tanıma desteği eklendi ve x86 ve x64 türleri için destek eklendi. 
- Amaç-C: `SendMessage` ve `SetMessageProperty` yöntemleri nesnesine eklendi `Connection` . [Belgelere bakın](/objectivec/cognitive-services/speech/spxconnection).
- TTS C++ API 'si artık `std::wstring` sensıs metin girişi olarak desteklenir ve bir wstring 'ı SDK 'ya geçirmeden önce dizeye dönüştürme gereksinimini ortadan kaldırır. [Ayrıntılara bakın](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [DIL kimliği](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) ve [kaynak dil yapılandırması](./how-to-specify-source-language.md?pivots=programming-language-csharp) artık kullanılabilir.
- JavaScript: `Connection` nesneye, konuşma hizmetinden geri arama olarak özel iletiler geçirmek için bir özellik eklenmiştir `receivedServiceMessage` .
- JavaScript: `FromHost API` Şirket içi kapsayıcılar ve bağımsız bulutlarla kullanım kolaylığı sağlamak için desteği eklendi. [Belgelere bakın](speech-container-howto.md).
- JavaScript: artık orgads ' `NODE_TLS_REJECT_UNAUTHORIZED` dan bir katkı için teşekkürler [](https://github.com/orgads). [Ayrıntılara bakın](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Yeni değişiklikler**

- `OpenSSL` , 1.1.1 b sürümüne güncelleştirilmiştir ve Linux için konuşma SDK 'Sı temel kitaplığına statik olarak bağlanır. Bu, gelen kutunuz `OpenSSL` sistemdeki dizine yüklenmemişse kesintiye neden olabilir `/usr/lib/ssl` . Bu sorunu geçici olarak çözmek için lütfen konuşma SDK 'Sı belgeleri altındaki [belgelerimizi](how-to-configure-openssl-linux.md) kontrol edin.
- C# için döndürülen veri türünü, `WordLevelTimingResult.Offset` `int` `long` `WordLevelTimingResults` konuşma verileri 2 dakikadan uzun olduğunda erişime izin verecek şekilde değiştirdik.
- `PushAudioInputStream``PullAudioInputStream` `AudioStreamFormat` Ayrıca, isteğe bağlı olarak, bunlar oluşturulduğunda belirtilen konuşma hizmetine WAV başlık bilgilerini gönderin. Müşterilerin artık [desteklenen ses girişi biçimini](how-to-use-audio-input-streams.md)kullanması gerekir. Diğer tüm biçimler, bir veya daha fazla soruna neden olabilir. 

**Hata düzeltmeleri**

- `OpenSSL`Yukarıdaki değişiklikler altındaki güncelleştirme bölümüne bakın. Linux ve Java 'da aralıklı bir kilitlenmeyi ve bir performans sorununu (yüksek yük altında kilit çakışması) düzelttik. 
- Java: yüksek eşzamanlılık senaryolarında nesne kapanışına yönelik iyileştirmeler yapıldı.
- NuGet paketimize yeniden yapılandırılmış. LIB klasörlerinin üç kopyasını kaldırdık `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` , NuGet paketini indirmek için daha küçük ve daha hızlı hale getirir ve bazı C++ yerel uygulamalarını derlemek için gereken üst bilgileri ekledik.
- [Burada](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)sabit hızlı başlangıç örnekleri. Linux, macOS, Windows 'ta "mikrofon bulunamadı" özel durumu görüntülenmeden çıkılıyor.
- Uzun konuşma tanıma özelliği, [Bu örnek](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)gibi belirli kod yollarında sonuçları elde eden sabit SDK kilitlenmesi.
- [Bu müşteri sorununa](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)yönelik olarak Azure Web App ORTAMıNDA düzeltilen SDK dağıtım hatası.
- `<voice>` `<audio>` [Bu müşteri sorununa](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)yönelik birden çok etiket veya etiket kullanılırken bir TTS hatası düzeltildi. 
- SDK askıya alındı durumundan kurtarıldığı zaman TTS 401 hatası düzeltildi.
- JavaScript: [euirim](https://github.com/euirim)'ten bir katkı sayesinde ses verilerinin dairesel bir şekilde Içe aktarılması düzeltildi. 
- JavaScript: 1,7 ' de eklenen hizmet özelliklerini ayarlama desteği eklendi.
- JavaScript: bir bağlantı hatasının sürekli, başarısız WebSocket yeniden bağlanma girişimlerinde oluşmasına neden olabilecek bir sorun düzeltildi.

**Örnekler**

- Android için anahtar sözcük tanıma örneği [buraya](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)eklendi.
- [Burada](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)sunucu senaryosu için TTS örneği eklendi.
- C# ve C++ için çok cihazlı görüşme hızlı başlangıçlarını [buradan](quickstarts/multi-device-conversation.md)eklediniz.

**Diğer değişiklikler**

- Android 'de iyileştirilmiş SDK temel kitaplığı boyutu.
- 1.9.0 ve sonraki sürümlerde SDK `int` `string` , konuşma dökümü için ses imza sürümü alanındaki ve türlerini destekler.

## <a name="speech-sdk-180-2019-november-release"></a>Konuşma SDK 1.8.0:2019-Kasım sürümü

**Yeni Özellikler**

- Şirket içi `FromHost()` kapsayıcılar ve bağımsız bulutlarla kullanım kolaylığı sağlamak için bir API eklendi.
- Konuşma tanıma için otomatik kaynak Dil Algılama eklendi (Java ve C++ ' da)
- `SourceLanguageConfig`Beklenen kaynak dillerini (Java ve C++ ' da) belirtmek için kullanılan konuşma tanıma için nesne eklendi
- `KeywordRecognizer`NuGet ve Unity paketleri aracılığıyla Windows (UWP), Android ve iOS desteği eklendi
- Zaman uyumsuz toplu işlemlerdeki konuşma dökümü için uzak konuşma Java API 'SI eklendi.

**Yeni değişiklikler**

- Konuşma taşıma işlevleri ad alanı altına taşındı `Microsoft.CognitiveServices.Speech.Transcription` .
- Konuşma yöntemlerinin bölümleri yeni `Conversation` sınıfa taşınır.
- 32-bit (ARMv7 ve x86) iOS için bırakılan destek

**Hata düzeltmeleri**

- Yerel `KeywordRecognizer` geçerli bir konuşma hizmeti abonelik anahtarı olmadan kullanılıyorsa kilitlenme için çözüm yapın

**Örnekler**

- İçin Xamarin örneği `KeywordRecognizer`
- İçin Unity örneği `KeywordRecognizer`
- Otomatik Kaynak Dil Algılama için C++ ve Java örnekleri.

## <a name="speech-sdk-170-2019-september-release"></a>Konuşma SDK 1.7.0:2019-Eylül sürümü

**Yeni Özellikler**

- Xamarin on Evrensel Windows Platformu (UWP), Android ve iOS için Beta desteği eklendi
- Unity için iOS desteği eklendi
- `Compressed`ALaw, Mulaw, Android üzerinde FLAC, iOS ve Linux için giriş desteği eklendi
- `SendMessageAsync` `Connection` Hizmete ileti göndermek için sınıfına eklendi
- `SetMessageProperty` `Connection` Bir iletinin Setting özelliğinin sınıfına eklendi
- Java (JRE ve Android), Python, Swift ve amaç-C için TTS eklendi bağlamaları
- TTS, macOS, iOS ve Android için kayıttan yürütme desteği ekledi.
- TTS için "sözcük sınırı" bilgileri eklendi.

**Hata düzeltmeleri**

- Android için Unity 2019 üzerinde IL2CPP derleme sorunu düzeltildi
- WAV dosya girişi hatalı şekilde işlenmekte olan hatalı biçimlendirilmiş üst bilgilerdeki sorun düzeltildi
- Bazı bağlantı özelliklerinde benzersiz olmayan UUID 'ler ile ilgili sorun düzeltildi
- Swift bağlamalarında null değer alabilme belirticileri hakkında birkaç uyarı düzeltildi (küçük kod değişiklikleri gerektirebilir)
- WebSocket bağlantılarına neden olan bir hata düzeltildi ağ yükü altında düzgün şekilde kapatılacak
- Android 'de, bazen tarafından kullanılan yinelenen izlenme kimliklerine neden olan bir sorun düzeltildi `DialogServiceConnector`
- Birden çok yönlü etkileşimler genelinde bağlantı kararlılığı ve `Canceled` ile ortaya çıkabilecek hataların (olaylar aracılığıyla) raporlanması için geliştirmeler `DialogServiceConnector`
- `DialogServiceConnector`oturum başlar, etkin bir işlem sırasında çağrılırken de dahil olmak üzere artık olayları doğru şekilde sağlayacak `ListenOnceAsync()``StartKeywordRecognitionAsync()`
- Alınmakta olan etkinliklerle ilişkili bir kilitlenme olduğunu biliyor `DialogServiceConnector`

**Örnekler**

- Xamarin için hızlı başlangıç
- Linux ARM64 Information ile güncelleştirilmiş CPP hızlı başlangıcı
- İOS bilgileriyle Unity hızlı başlangıç güncelleştirildi

## <a name="speech-sdk-160-2019-june-release"></a>Konuşma SDK 1.6.0:2019-Haziran sürümü

**Örnekler**

- UWP ve Unity 'de metin okuma için hızlı başlangıç örnekleri
- İOS 'ta Swift için hızlı başlangıç örneği
- Konuşma & Amaç Tanıma ve çevirisi için Unity örnekleri
- İçin güncelleştirilmiş hızlı başlangıç örnekleri `DialogServiceConnector`

**Geliştirmeler/değişiklikler**

- İletişim kutusu ad alanı:
  - `SpeechBotConnector`, `DialogServiceConnector` olarak yeniden adlandırıldı
  - `BotConfig`, `DialogServiceConfig` olarak yeniden adlandırıldı
  - `BotConfig::FromChannelSecret()` ile yeniden eşlendi `DialogServiceConfig::FromBotSecret()`
  - Var olan tüm doğrudan hat konuşma istemcileri yeniden adlandırmadan sonra desteklenmeye devam eder
- Proxy, kalıcı bağlantıyı destekleyecek şekilde TTS REST bağdaştırıcısını güncelleştirme
- Geçersiz bir bölge geçirildiğinde hata iletisini geliştir
- Swift/amaç-C:
  - Geliştirilmiş hata raporlama: bir hata ile sonuçlanan Yöntemler Şu anda iki sürümde mevcuttur: `NSError` hata işleme için bir nesne ve bir özel durum oluşturan bir nesne. İlki Swift 'a açıktır. Bu değişiklik, mevcut Swift koduna uyarlamalar gerektirir.
  - Geliştirilmiş olay işleme

**Hata düzeltmeleri**

- TTS için çözüm: daha `SpeakTextAsync` sonra, sesin işleme tamamlanana kadar beklememeden döndürülmüyor
- Tam dil desteğini etkinleştirmek Için C# içinde sıralama dizelerini onarma
- Örneklerde net461 Target Framework ile çekirdek kitaplığı yükleme ile ilgili .NET Core uygulama sorunu için çözüm
- Örneklerindeki çıkış klasörüne yerel kitaplıkları dağıtmaya yönelik zaman zaman sorun giderme
- Güvenli bir şekilde Web yuvası kapatma için çözüm
- Linux 'ta ağır yük altında bir bağlantı açılırken olası kilitlenme için çözüm
- MacOS için çerçeve paketinde eksik meta veriler için çözüm
- Windows 'da sorun giderme `pip install --user`

## <a name="speech-sdk-151"></a>Konuşma SDK 1.5.1

Bu bir hata çözme sürümüdür ve yalnızca yerel/yönetilen SDK 'yi etkiliyor. SDK 'nın JavaScript sürümünü etkilemiyor.

**Hata düzeltmeleri**

- Konuşma dökümü ile kullanıldığında FromSubscription 'ı onarın.
- Sesli Yardımcılar için anahtar sözcük biriktirmesiyle ilgili hatayı düzeltir.

## <a name="speech-sdk-150-2019-may-release"></a>Konuşma SDK 1.5.0:2019-yayın Mayıs

**Yeni özellikler**

- Anahtar sözcük biriktirme (KWS) artık Windows ve Linux için kullanılabilir. KWS işlevselliği herhangi bir mikrofon türü ile çalışabilir, ancak resmi KWS desteği şu anda Azure Kinect DK donanımında veya konuşma cihazları SDK 'sında bulunan mikrofon dizileri ile sınırlıdır.
- Tümcecik ipucu işlevselliği SDK aracılığıyla kullanılabilir. Daha fazla bilgi için [buraya](./get-started-speech-to-text.md)bakın.
- Konuşma dökümü işlevselliği SDK aracılığıyla kullanılabilir. [Buraya](./conversation-transcription.md)bakın.
- Doğrudan hat konuşma kanalını kullanarak sesli Yardımcılar için destek ekleyin.

**Örnekler**

- Yeni özellikler veya SDK tarafından desteklenen yeni hizmetler için örnek eklendi.

**Geliştirmeler/değişiklikler**

- Hizmet davranışını veya hizmet sonuçlarını (maskeleme küfür ve diğerleri gibi) ayarlamak için çeşitli tanıyıcı özellikleri eklendi.
- Artık tanıyıcıyı oluşturmuş olsanız bile, tanıyıcıyı standart yapılandırma özellikleriyle yapılandırabilirsiniz `FromEndpoint` .
- Amaç-C: `OutputFormat` özelliği öğesine eklendi `SPXSpeechConfiguration` .
- SDK artık Linux dağıtımı olarak 9.

**Hata düzeltmeleri**

- Konuşmacı kaynağının metinden konuşmaya çok erken bir yere çıkarılması sorunu düzeltildi.

## <a name="speech-sdk-142"></a>Konuşma SDK 1.4.2

Bu bir hata çözme sürümüdür ve yalnızca yerel/yönetilen SDK 'yi etkiliyor. SDK 'nın JavaScript sürümünü etkilemiyor.

## <a name="speech-sdk-141"></a>Konuşma SDK 1.4.1

Bu yalnızca bir JavaScript sürümüdür. Hiçbir özellik eklenmedi. Aşağıdaki düzeltmeler yapılmıştır:

- Web paketinin HTTPS-proxy-Agent yüklemesini engelleyin.

## <a name="speech-sdk-140-2019-april-release"></a>Konuşma SDK 1.4.0:2019-Nisan sürümü

**Yeni özellikler**

- SDK artık beta sürümü olarak metinden konuşmaya hizmetini desteklemektedir. C++ ve C# ' den Windows ve Linux Desktop 'ta desteklenir. Daha fazla bilgi için, [metinden konuşmaya genel bakış ' a](text-to-speech.md#get-started)bakın.
- SDK artık MP3 ve Opus/OGG ses dosyalarını akış giriş dosyaları olarak desteklemektedir. Bu özellik yalnızca C++ ve C# ' den Linux üzerinde kullanılabilir ve şu anda beta aşamasındadır ( [daha ayrıntılı bilgi).](how-to-use-codec-compressed-audio-input-streams.md)
- Java, .NET Core, C++ ve amaç-C için konuşma SDK 'Sı, macOS desteği elde etmiş. MacOS için amaç-C desteği şu anda beta aşamasındadır.
- iOS: iOS için konuşma SDK 'Sı (hedef-C) artık bir CocoaPod olarak yayımlanmıştır.
- JavaScript: giriş cihazı olarak varsayılan olmayan mikrofonu destekler.
- JavaScript: Node.js için proxy desteği.

**Örnekler**

- Mac ile konuşma SDK 'sını kullanma örnekleri ve macOS üzerinde hedef-C eklenmiştir.
- Metin okuma hizmetinin kullanımını gösteren örnekler eklenmiştir.

**Geliştirmeler/değişiklikler**

- Python: tanıma sonuçlarının ek özellikleri artık özelliği aracılığıyla kullanıma sunulmuştur `properties` .
- Ek geliştirme ve hata ayıklama desteği için, SDK günlüğü ve tanılama bilgilerini bir günlük dosyasına yeniden yönlendirebilir ( [burada](how-to-use-logging.md)daha fazla bilgi bulabilirsiniz).
- JavaScript: ses işleme performansını geliştirme.

**Hata düzeltmeleri**

- Mac/iOS: konuşma hizmetine bir bağlantı kurulamazsa uzun bir beklemeye yol gösteren bir hata düzeltildi.
- Python: Python geri çağırmalar içindeki bağımsız değişkenler için hata işlemeyi geliştir.
- JavaScript: RequestSession üzerinde konuşma için yanlış durum bildirimi düzeltildi.

## <a name="speech-sdk-131-2019-february-refresh"></a>Konuşma SDK 1.3.1:2019-Şubat yenilemesi

Bu bir hata çözme sürümüdür ve yalnızca yerel/yönetilen SDK 'yi etkiliyor. SDK 'nın JavaScript sürümünü etkilemiyor.

**Hata onarımı**

- Mikrofon girişi kullanılırken bellek sızıntısı düzeltildi. Akış tabanlı veya dosya girişi etkilenmez.

## <a name="speech-sdk-130-2019-february-release"></a>Konuşma SDK 1.3.0:2019-Şubat yayını

**Yeni Özellikler**

- Konuşma SDK 'Sı, sınıf aracılığıyla giriş mikrofonunun seçimini destekler `AudioConfig` . Bu, konuşma hizmetine varsayılan olmayan bir mikrofondan ses verileri akışını sağlar. Daha fazla bilgi için bkz. [ses girişi cihaz seçimini](how-to-select-audio-input-devices.md)açıklayan belgeler. Bu özellik JavaScript 'te henüz kullanılamaz.
- Konuşma SDK 'Sı artık bir beta sürümünde Unity 'yi desteklemektedir. [GitHub örnek deposundaki](https://aka.ms/csspeech/samples)sorun bölümüne geri bildirim sağlayın. Bu sürüm, Windows x86 ve x64 (masaüstü veya Evrensel Windows Platformu uygulamaları) ve Android (ARM32/64, x86) üzerinde Unity 'yi destekler. [Unity hızlı](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)başlangıçmızda daha fazla bilgi bulabilirsiniz.
- Dosya `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (önceki sürümlerde sunulan) artık gerekli değildir. İşlevsellik artık temel SDK ile tümleşiktir.

**Örnekler**

[Örnek depoımızda](https://aka.ms/csspeech/samples)aşağıdaki yeni içerik mevcuttur:

- İçin ek örnekler `AudioConfig.FromMicrophoneInput` .
- Amaç tanıma ve çeviri için ek Python örnekleri.
- İOS 'da nesnesini kullanmak için ek örnekler `Connection` .
- Ses çıkışıyla çeviri için ek Java örnekleri.
- [Toplu Iş dökümü REST API](batch-transcription.md)kullanımı için yeni örnek.

**Geliştirmeler/değişiklikler**

- Python
  - Üzerinde geliştirilmiş parametre doğrulama ve hata iletileri `SpeechConfig` .
  - Nesnesi için destek ekleyin `Connection` .
  - Windows üzerinde 32-bit Python (x86) için destek.
  - Python için konuşma SDK 'Sı beta dışında.
- iOS
  - SDK artık iOS SDK 12,1 sürümüne göre oluşturulmuştur.
  - SDK artık iOS sürümleri 9,2 ve üstünü desteklemektedir.
  - Başvuru belgelerini geliştirir ve birçok özellik adını düzeltir.
- JavaScript
  - Nesnesi için destek ekleyin `Connection` .
  - Paketlenmiş JavaScript için tür tanımı dosyaları ekleme
  - Tümcecik ipuçları için başlangıç desteği ve uygulama.
  - Tanıma için Service JSON ile Özellikler koleksiyonunu döndürün
- Windows dll 'Leri artık bir sürüm kaynağı içeriyor.
- Bir tanıyıcı oluşturursanız, `FromEndpoint` doğrudan ENDPOINT URL 'sine parametreler ekleyebilirsiniz. Kullanarak, `FromEndpoint` tanıyıcıyı standart yapılandırma özellikleri aracılığıyla yapılandıramazsınız.

**Hata düzeltmeleri**

- Boş Proxy Kullanıcı adı ve proxy parolası doğru işlenmedi. Bu sürümle birlikte, Proxy Kullanıcı adı ve proxy parolasını boş bir dizeye ayarlarsanız, ara sunucuya bağlanılırken gönderilmez.
- SDK tarafından oluşturulan SessionID, bazı diller/ortamlar için her zaman gerçekten gelişigüzel değildir &nbsp; . Bu sorunu giderecek rastgele Oluşturucu başlatması eklendi.
- Yetkilendirme belirtecinin işlenmesini geliştirme. Bir yetkilendirme belirteci kullanmak istiyorsanız, içinde belirtin `SpeechConfig` ve abonelik anahtarını boş bırakın. Ardından tanıyıcı her zamanki gibi oluşturun.
- Bazı durumlarda `Connection` nesne doğru şekilde yayımlanmamıştı. Bu sorun düzeltilmiştir.
- JavaScript örneği, çeviri birleştirmesinin ses çıkışını desteklemek için de Safari 'de düzeltildi.

## <a name="speech-sdk-121"></a>Konuşma SDK 'Sı 1.2.1

Bu yalnızca bir JavaScript sürümüdür. Hiçbir özellik eklenmedi. Aşağıdaki düzeltmeler yapılmıştır:

- Stream. End sırasında değil, Stream 'in sonuna kadar bir akış sonu tetiklendir.
- Geçerli gönderme başarısız olduysa, sonraki gönderimi zamanlamadığı ses göndericisinin hatasını düzeltir.
- Kimlik doğrulama belirteciyle sürekli tanımayı düzeltir.
- Farklı tanıyıcı/uç noktalar için hata düzeltildi.
- Belge geliştirmeleri.

## <a name="speech-sdk-120-2018-december-release"></a>Konuşma SDK 1.2.0:2018-Aralık yayını

**Yeni Özellikler**

- Python
  - Python desteğinin beta sürümü (3,5 ve üzeri) Bu sürümde kullanılabilir. Daha fazla bilgi için buraya bakın] (hızlı başlangıç-python.md).
- JavaScript
  - JavaScript için konuşma SDK 'Sı açık kaynaklıdır. Kaynak kodu [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)' da kullanılabilir.
  - Artık Node.js destekliyoruz, [burada](./get-started-speech-to-text.md)daha fazla bilgi bulabilirsiniz.
  - Ses oturumları için uzunluk kısıtlaması kaldırılmıştır, otomatik olarak yeniden bağlanma işlemi, kapak altında otomatik olarak gerçekleşir.
- `Connection` nesne
  - İçinden `Recognizer` bir `Connection` nesnesine erişebilirsiniz. Bu nesne, hizmet bağlantısını açık bir şekilde başlatabilmeniz ve olaylara bağlanıp bağlantıyı kesmeye abone olmanızı sağlar.
    (Bu özellik JavaScript ve Python 'da henüz kullanılamaz.)
- Ubuntu 18,04 desteği.
- Android
  - APK oluşturma sırasında ProGuard desteği etkinleştirildi.

**Geliştirmeleri**

- İç iş parçacığı kullanımındaki geliştirmeler, iş parçacığı sayısı, kilitler, zaman uyumu sağlayıcılar sayısını azaltır.
- Geliştirilmiş hata raporlama/bilgi. Birçok durumda, hata iletileri hiç bir şekilde yayılmaz.
- Güncel modüller kullanmak için JavaScript 'teki geliştirme bağımlılıkları güncelleştirildi.

**Hata düzeltmeleri**

- İçindeki bir tür uyumsuzluğu nedeniyle sabit bellek sızıntıları `RecognizeAsync` .
- Bazı durumlarda özel durumlar sızdırılmıştı.
- Çeviri olay bağımsız değişkenlerinde bellek sızıntısı düzeltiliyor.
- Uzun süre çalışan oturumlarda yeniden bağlantı sırasında bir kilitleme sorunu düzeltildi.
- Başarısız çeviriler için eksik nihai sonuca neden olabilecek bir sorun düzeltildi.
- C#: bir `async` işlem ana iş parçacığında beklenmediyse, zaman uyumsuz görev tamamlanmadan önce tanıyıcı atılamamalıdır.
- Java: bir sorun düzeltildi Java VM kilitlenmesiyle sonuçlanır.
- Amaç-C: sabit enum eşlemesi; Yerine RecognizedIntent döndürüldü `RecognizingIntent` .
- JavaScript: içinde varsayılan çıkış biçimini ' Simple ' olarak ayarlayın `SpeechConfig` .
- JavaScript: JavaScript ve diğer dillerdeki yapılandırma nesnesindeki özellikler arasında tutarsızlık kaldırılıyor.

**Örnekler**

- Birkaç örnek güncelleştirildi ve düzeltildi (örneğin, çeviri için çıkış sesleriniz vb.).
- [Örnek depoda](https://aka.ms/csspeech/samples)Node.js örnekleri eklendi.

## <a name="speech-sdk-110"></a>Konuşma SDK 1.1.0

**Yeni Özellikler**

- Android x86/x64 desteği.
- Proxy desteği: `SpeechConfig` nesnesinde, artık proxy bilgilerini (ana bilgisayar adı, bağlantı noktası, Kullanıcı adı ve parola) ayarlamak için bir işlev çağırabilirsiniz. Bu özellik henüz iOS üzerinde kullanılamıyor.
- Geliştirilmiş hata kodu ve iletileri. Bir tanıma bir hata döndürdüğünden, bu zaten `Reason` (iptal edilen olayda) veya `CancellationDetails` (tanınma sonucunda) olarak ayarlanmıştır `Error` . İptal edilen olay artık iki ek üye içeriyor `ErrorCode` `ErrorDetails` . Sunucu, bildirilen hata ile ek hata bilgileri döndürdüğünden, artık yeni üyelerde kullanılabilir olacaktır.

**Geliştirmeleri**

- Tanıyıcı yapılandırmasına ek doğrulama eklendi ve ek hata iletisi eklendi.
- Bir ses dosyasının ortasında uzun süreli sessizlik geliştirilmiş işlem.
- NuGet paketi: .NET Framework projeleri için, AnyCPU yapılandırmasıyla oluşturmayı engeller.

**Hata düzeltmeleri**

- Tanıyıcılar içinde bulunan bazı özel durumlar düzeltildi. Ayrıca, özel durumlar yakalanıp `Canceled` olaya dönüştürülür.
- Özellik yönetiminde bellek sızıntısı 'nı düzeltir.
- Bir ses giriş dosyasının tanıyıcıyı kilitlenebildiği bir hata düzeltildi.
- Bir oturum durdurma olayından sonra olayların alınabileceği bir hata düzeltildi.
- İş parçacığı oluşturma sırasında bazı yarış koşulları düzeltildi.
- Bir çökmesine neden olabilecek bir iOS uyumluluğu sorunu düzeltildi.
- Android mikrofon desteği için kararlılık geliştirmeleri.
- JavaScript 'teki bir tanıyıcıın tanıma dilini yoksaymasına neden olan bir hata düzeltildi.
- `EndpointId`JavaScript içindeki (bazı durumlarda) bir hata düzeltildi.
- JavaScript 'te Addamaciçinde parametre sırası değiştirildi ve eksik `AddIntent` JavaScript imzası eklendi.

**Örnekler**

- [Örnek depoda](https://aka.ms/csspeech/samples)çekme ve gönderme akışı kullanımı için C++ ve C# örnekleri eklendi.

## <a name="speech-sdk-101"></a>Konuşma SDK 1.0.1

Güvenilirlik geliştirmeleri ve hata düzeltmeleri:

- Disposing tanıyıcı içindeki yarış durumu nedeniyle olası önemli hata düzeltildi
- Özellik ayarı gerçekleştiğinde olası önemli hata düzeltildi.
- Ek hata ve parametre denetimi eklendi.
- Amaç-C: NSString 'de geçersiz kılma adı nedeniyle olası önemli hata düzeltildi.
- Hedef-C: API 'nin görünürlüğü düzeltildi
- JavaScript: olaylar ve yükleri hakkında düzeltildi.
- Belge geliştirmeleri.

[Örnek depoımızda](https://aka.ms/csspeech/samples)JavaScript için yeni bir örnek eklenmiştir.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Bilişsel hizmetler konuşma SDK 1.0.0:2018-Eylül sürümü

**Yeni özellikler**

- İOS üzerinde amaç-C desteği. [İOS Için hedef C hızlı başlangıç](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone)öğreticimize göz atın.
- Tarayıcıda JavaScript desteği. [JavaScript hızlı](./get-started-speech-to-text.md)başlangıçmıza göz atın.

**Yeni değişiklikler**

- Bu sürümle birlikte, bir dizi Son değişiklik yapılmıştır.
  Ayrıntılar için [Bu sayfaya](https://aka.ms/csspeech/breakingchanges_1_0_0) bakın.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Bilişsel hizmetler konuşma SDK 0.6.0:2018-Ağustos sürümü

**Yeni özellikler**

- Konuşma SDK 'Sı ile oluşturulan UWP uygulamaları artık Windows uygulama sertifikasyon seti 'Ni (WACK) geçirebilir.
  [UWP hızlı](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp)başlangıcı ' nı inceleyin.
- Linux (Ubuntu 16,04 x64) üzerinde .NET Standard 2,0 desteği.
- Deneysel: Windows (64-bit) ve Linux (Ubuntu 16,04 x64) üzerinde Java 8 desteği.
  [Java Runtime Environment hızlı](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre)başlangıcı ' nı inceleyin.

**İşlevsel değişiklik**

- Bağlantı hatalarıyla ilgili ek hata ayrıntısı bilgilerini kullanıma sunun.

**Yeni değişiklikler**

- Java 'da (Android), `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` işlev artık bir yol parametresi gerektirmez. Artık tüm desteklenen platformlarda yol otomatik olarak algılanır.
- `EndpointUrl`Java ve C# ' de özelliğin get-erişimcisi kaldırılmıştır.

**Hata düzeltmeleri**

- Java 'da, ses birleştirme, çeviri tanıyıcısının sonucu olarak uygulanır.
- Etkin olmayan iş parçacıklarına ve daha fazla açık ve kullanılmayan yuva sayısına neden olabilecek bir hata düzeltildi.
- Uzun süre çalışan bir tanımanın iletim ortasında sonlanabilir olduğu bir sorun düzeltildi.
- Tanıyıcı kapatılırken bir yarış durumu düzeltildi.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Bilişsel hizmetler konuşma SDK 0.5.0:2018-Temmuz sürümü

**Yeni özellikler**

- Android platformu desteği (API 23: Android 6,0 sıralamada Mallow veya üzeri). [Android hızlı başlangıç](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)' a göz atın.
- Windows üzerinde 2,0 .NET Standard destekler. [.NET Core hızlı başlangıç](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)' i inceleyin.
- Deneysel: Windows 'da UWP 'yi destekleme (sürüm 1709 veya üzeri).
  - [UWP hızlı](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)başlangıcı ' nı inceleyin.
  - Konuşma SDK 'Sı ile oluşturulan UWP uygulamalarının Windows uygulama sertifikasyon seti 'Ni (WACK) henüz geçirmediğini unutmayın.
- Otomatik yeniden bağlantı ile uzun süre çalışan tanıma desteği.

**İşlevsel değişiklikler**

- `StartContinuousRecognitionAsync()` uzun süre çalışan tanımayı destekler.
- Tanınma sonucu daha fazla alan içeriyor. Bunlar, tanınan metnin ses başlangıcından ve süresinden (her ikisi de her ikisi de) ve tanınma durumunu temsil eden ek değerlere (örneğin, ve) göre denkleştirilir `InitialSilenceTimeout` `InitialBabbleTimeout` .
- Fabrika örnekleri oluşturmak için AuthorizationToken 'ı destekleme.

**Yeni değişiklikler**

- Tanıma olayları: olay `NoMatch` türü `Error` olayla birleştirildi.
- C# ' de SpeechOutputFormat, `OutputFormat` C++ ile hizalı kalmak için olarak yeniden adlandırıldı.
- Arabirimdeki bazı yöntemlerin dönüş türü `AudioInputStream` biraz değişti:
  - Java 'da, `read` yöntemi yerine artık döndürülür `long` `int` .
  - C# ' de, `Read` yöntemi artık `uint` yerine döndürülür `int` .
  - C++ ' da, `Read` ve `GetFormat` yöntemleri artık `size_t` yerine döndürülür `int` .
- C++: ses giriş akışlarının örnekleri artık yalnızca olarak geçirilebilir `shared_ptr` .

**Hata düzeltmeleri**

- Zaman aşımı durumunda sonuç içindeki hatalı dönüş değerleri düzeltildi `RecognizeAsync()` .
- Windows 'daki Media Foundation kitaplıkları bağımlılığı kaldırılmıştır. SDK artık çekirdek ses API 'Lerini kullanır.
- Belge onarımı: desteklenen bölgeleri anlatmak için bir [bölgeler](regions.md) sayfası eklendi.

**Bilinen sorun**

- Android için konuşma SDK 'Sı, çeviri için konuşma sonuçları raporlamaz. Bu sorun bir sonraki sürümde düzeltilecektir.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Bilişsel hizmetler konuşma SDK 0.4.0:2018-Haziran sürümü

**İşlevsel değişiklikler**

- Audioınputstream

  Bir tanıyıcı artık ses kaynağı olarak bir akış kullanabilir. Daha fazla bilgi için ilgili [nasıl yapılır Kılavuzu](how-to-use-audio-input-streams.md)' na bakın.

- Ayrıntılı çıkış biçimi

  Bir oluşturduğunuzda `SpeechRecognizer` , istek `Detailed` veya `Simple` çıkış biçimi yapabilirsiniz. , `DetailedSpeechRecognitionResult` Maskeli bir küfür ile Güvenirlik puanı, tanınan metin, ham sözlü form, normalleştirilmiş form ve normalleştirilmiş form içerir.

**Son değişiklik**

- `SpeechRecognitionResult.Text`C# ' den olarak değiştirilir `SpeechRecognitionResult.RecognizedText` .

**Hata düzeltmeleri**

- Kapatmadan önce USP katmanında olası bir geri çağırma sorunu düzeltildi.
- Bir tanıyıcı bir ses giriş dosyası kullanıyorsa, dosya tanıtıcısına gerekenden daha uzun süre içinde tutulmakta.
- İleti göndericisi ve tanıyıcı arasında birkaç kilitlenme kaldırıldı.
- `NoMatch`Hizmetten gelen yanıt zaman aşımına uğradığından bir sonuç tetikle.
- Windows 'daki Media Foundation kitaplıkları gecikmeli yüklendi. Bu kitaplık yalnızca mikrofon girişi için gereklidir.
- Ses verileri için karşıya yükleme hızı, özgün ses hızının iki katı ile sınırlıdır.
- Windows 'da C# .NET derlemeleri artık güçlü adlandırılmış.
- Belge onarımı: `Region` bir tanıyıcı oluşturmak için gerekli bilgiler.

Daha fazla örnek eklenmiştir ve sürekli olarak güncelleştirilir. En son örnek kümesi için bkz. [konuşma SDK örnekleri GitHub deposu](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Bilişsel hizmetler konuşma SDK 0.2.12733:2018-Mayıs yayını

Bu sürüm, bilişsel hizmetler konuşma SDK 'sının ilk genel önizleme sürümüdür.
