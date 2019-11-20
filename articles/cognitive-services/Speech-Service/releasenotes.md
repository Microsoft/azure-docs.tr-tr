---
title: Sürüm notları-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Konuşma hizmeti için çalışan bir dizi özellik sürümü, geliştirmeler, hata düzeltmeleri ve bilinen sorunlar hakkında bilgi bulabilirsiniz.
services: cognitive-services
author: BrianMouncer
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: f38e23f0648d802ba7b09391d6f6bbd0c093d989
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888169"
---
# <a name="release-notes"></a>Sürüm notları

## <a name="speech-sdk-180-2019-november-release"></a>Konuşma SDK 1.8.0:2019-Kasım sürümü

**Yeni özellikler**

- Şirket içi kapsayıcılar ve bağımsız bulutlarla kullanım kolaylığı sağlamak için bir FromHost () API 'SI eklendi.
- Konuşma tanıma için otomatik kaynak Dil Algılama eklendi (Java ve C++)
- Beklenen kaynak dillerini (Java ve C++) belirtmek için kullanılan, konuşma tanıma Için SourceLanguageConfig nesnesi eklendi
- NuGet ve Unity paketleri aracılığıyla Windows (UWP), Android ve iOS 'ta Keywordtanıyıcı desteği eklendi
- Zaman uyumsuz toplu işlemlerdeki konuşma dökümü için uzak konuşma Java API 'SI eklendi.

**Son değişiklikler**

- Konuşma yazılı işlevleri Microsoft. Biliveservices. Speech. Transcriptıon ad alanı altına taşındı.
- Görüşmenin bir parçası olan yöntemler yeni konuşma sınıfına taşınır.
- 32-bit (ARMv7 ve x86) iOS için bırakılan destek

**Hata düzeltmeleri**

- Yerel Keywordtanıyıcı geçerli bir konuşma hizmeti abonelik anahtarı olmadan kullanılıyorsa kilitlenme için çözüm yapın

**Örnekler**

- Keywordtanıyıcı için Xamarin örneği
- Keywordtanıyıcı için Unity örneği
- C++ve otomatik kaynak Dil Algılama için Java örnekleri.

## <a name="speech-sdk-170-2019-september-release"></a>Konuşma SDK 1.7.0:2019-Eylül sürümü

**Yeni özellikler**

- Xamarin on Evrensel Windows Platformu (UWP), Android ve iOS için Beta desteği eklendi
- Unity için iOS desteği eklendi
- IW, Mulaw, Android üzerinde FLAC, iOS ve Linux için sıkıştırılmış giriş desteği eklendi
- Hizmete ileti göndermek için bağlantı sınıfında SendMessageAsync eklendi
- Bir iletinin özelliği ayarlanıyor için bağlantı sınıfında SetMessageProperty eklendi
- Java (JRE ve Android), Python, Swift ve amaç-C için TTS eklendi bağlamaları
- TTS, macOS, iOS ve Android için kayıttan yürütme desteği ekledi.
- TTS için "sözcük sınırı" bilgileri eklendi.

**Hata düzeltmeleri**

- Android için Unity 2019 üzerinde IL2CPP derleme sorunu düzeltildi
- WAV dosya girişi hatalı şekilde işlenmekte olan hatalı biçimlendirilmiş üst bilgilerdeki sorun düzeltildi
- Bazı bağlantı özelliklerinde benzersiz olmayan UUID 'ler ile ilgili sorun düzeltildi
- Swift bağlamalarında null değer alabilme belirticileri hakkında birkaç uyarı düzeltildi (küçük kod değişiklikleri gerektirebilir)
- WebSocket bağlantılarına neden olan bir hata düzeltildi ağ yükü altında düzgün şekilde kapatılacak
- Android 'de, bazı durumlarda DialogServiceConnector tarafından kullanılan yinelenen izlenme kimliklerine neden olan bir sorun düzeltildi
- Iletişim kutusu bağlayıcı ile oluştuğunda çok yönlü etkileşimler genelinde bağlantı kararlılığı ve hataların raporlaması (Iptal edilen olaylar aracılığıyla)
- DialogServiceConnector oturumu artık, etkin bir StartKeywordRecognitionAsync () sırasında ListenOnceAsync () çağrılırken de dahil olmak üzere olayları doğru olarak sağlayacak
- Alınmakta olan DialogServiceConnector etkinlikleriyle ilişkili bir kilitlenme olduğunu biliyor

**Örnekler**

- Xamarin için hızlı başlangıç
- Linux ARM64 Information ile güncelleştirilmiş CPP hızlı başlangıcı
- İOS bilgileriyle Unity hızlı başlangıç güncelleştirildi

## <a name="speech-sdk-160-2019-june-release"></a>Konuşma SDK 1.6.0:2019-Haziran sürümü

**Örnekler**

- UWP ve Unity 'de metin okuma için hızlı başlangıç örnekleri
- İOS 'ta Swift için hızlı başlangıç örneği
- Konuşma & Amaç Tanıma ve çevirisi için Unity örnekleri
- DialogServiceConnector için hızlı başlangıç örnekleri

**Geliştirmeler/değişiklikler**

- İletişim kutusu ad alanı:
  - SpeechBotConnector, DialogServiceConnector olarak yeniden adlandırıldı
  - BotConfig, DialogServiceConfig olarak yeniden adlandırıldı
  - BotConfig:: FromChannelSecret (), DialogServiceConfig:: FromBotSecret () ile yeniden eşlendi
  - Var olan tüm doğrudan hat konuşma istemcileri yeniden adlandırmadan sonra desteklenmeye devam eder
- Proxy, kalıcı bağlantıyı destekleyecek şekilde TTS REST bağdaştırıcısını güncelleştirme
- Geçersiz bir bölge geçirildiğinde hata iletisini geliştir
- Swift/amaç-C:
  - Geliştirilmiş hata raporlama: hata ile sonuçlanan Yöntemler Şu anda iki sürümde mevcuttur: hata işleme için bir `NSError` nesnesi ve bir özel durum Başlatan bir nesne. İlki Swift 'a açıktır. Bu değişiklik, mevcut Swift koduna uyarlamalar gerektirir.
  - Geliştirilmiş olay işleme

**Hata düzeltmeleri**

- TTS için çözüm: Hoparlörktextasync, sesin işleme tamamlanana kadar beklemeden döndürüldüğü
- Tam dil desteğini etkinleştirmek için C# içinde sıralama dizelerini onarma
- Örneklerde net461 Target Framework ile çekirdek kitaplığı yükleme ile ilgili .NET Core uygulama sorunu için çözüm
- Örneklerindeki çıkış klasörüne yerel kitaplıkları dağıtmaya yönelik zaman zaman sorun giderme
- Güvenli bir şekilde Web yuvası kapatma için çözüm
- Linux üzerinde çok ağır yük altında bir bağlantı açılırken olası kilitlenme için çözüm
- MacOS için çerçeve paketinde eksik meta veriler için çözüm
- Windows üzerinde `pip install --user` sorunları giderme

## <a name="speech-sdk-151"></a>Konuşma SDK 1.5.1

Bu bir hata çözme sürümüdür ve yalnızca yerel/yönetilen SDK 'yi etkiliyor. SDK 'nın JavaScript sürümünü etkilemiyor.

**Hata düzeltmeleri**

- Konuşma dökümü ile kullanıldığında FromSubscription 'ı onarın.
- Sesli Yardımcılar için anahtar sözcük biriktirmesiyle ilgili hatayı düzeltir.

## <a name="speech-sdk-150-2019-may-release"></a>Konuşma SDK 1.5.0:2019-yayın Mayıs

**Yeni özellikler**

- Anahtar sözcük biriktirme (KWS) artık Windows ve Linux için kullanılabilir. KWS işlevselliği herhangi bir mikrofon türü ile çalışabilir, ancak resmi KWS desteği şu anda Azure Kinect DK donanımında veya konuşma cihazları SDK 'sında bulunan mikrofon dizileri ile sınırlıdır.
- Tümcecik ipucu işlevselliği SDK aracılığıyla kullanılabilir. Daha fazla bilgi için [buraya](how-to-phrase-lists.md)bakın.
- Konuşma dökümü işlevselliği SDK aracılığıyla kullanılabilir. [Buraya](conversation-transcription-service.md)bakın.
- Doğrudan hat konuşma kanalını kullanarak sesli Yardımcılar için destek ekleyin.

**Örnekler**

- Yeni özellikler veya SDK tarafından desteklenen yeni hizmetler için örnek eklendi.

**Geliştirmeler/değişiklikler**

- Hizmet davranışını veya hizmet sonuçlarını (maskeleme küfür ve diğerleri gibi) ayarlamak için çeşitli tanıyıcı özellikleri eklendi.
- Artık tanıyıcı `FromEndpoint`oluşturmuş olsanız bile, tanıyıcıyı standart yapılandırma özellikleri aracılığıyla yapılandırabilirsiniz.
- Hedef-C: `OutputFormat` özelliği SPXSpeechConfiguration 'e eklendi.
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

- SDK artık beta sürümü olarak metinden konuşmaya hizmetini desteklemektedir. Ve ' C++ C#den Windows ve Linux Desktop 'ta desteklenir. Daha fazla bilgi için, [metinden konuşmaya genel bakış ' a](text-to-speech.md#get-started-with-text-to-speech)bakın.
- SDK artık MP3 ve Opus/OGG ses dosyalarını akış giriş dosyaları olarak desteklemektedir. Bu özellik yalnızca Linux C++ üzerinde kullanılabilir C# ve şu anda beta aşamasındadır (daha ayrıntılı bilgi). [](how-to-use-codec-compressed-audio-input-streams.md)
- Java, .NET Core C++ ve amaç-C için konuşma SDK 'Sı, MacOS desteği elde etmiş. MacOS için amaç-C desteği şu anda beta aşamasındadır.
- iOS: iOS için konuşma SDK 'Sı (hedef-C) artık bir CocoaPod olarak yayımlanmıştır.
- JavaScript: giriş cihazı olarak varsayılan olmayan mikrofonu destekler.
- JavaScript: node. js için proxy desteği.

**Örnekler**

- MacOS üzerinde WITH C++ hedefi-C ile ve ile konuşma SDK 'sını kullanmaya yönelik örnekler eklenmiştir.
- Metin okuma hizmetinin kullanımını gösteren örnekler eklenmiştir.

**Geliştirmeler/değişiklikler**

- Python: tanıma sonuçlarının ek özellikleri artık `properties` özelliği aracılığıyla kullanıma sunulmuştur.
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

**Yeni özellikler**

- Konuşma SDK 'Sı, ses yapılandırma sınıfı aracılığıyla giriş mikrofonunun seçimini destekler. Bu, konuşma hizmetlerinde varsayılan olmayan bir mikrofondan ses verileri akışını sağlar. Daha fazla bilgi için bkz. [ses girişi cihaz seçimini](how-to-select-audio-input-devices.md)açıklayan belgeler. Bu özellik JavaScript 'te henüz kullanılamaz.
- Konuşma SDK 'Sı artık bir beta sürümünde Unity 'yi desteklemektedir. [GitHub örnek deposundaki](https://aka.ms/csspeech/samples)sorun bölümüne geri bildirim sağlayın. Bu sürüm, Windows x86 ve x64 (masaüstü veya Evrensel Windows Platformu uygulamaları) ve Android (ARM32/64, x86) üzerinde Unity 'yi destekler. [Unity hızlı](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)başlangıçmızda daha fazla bilgi bulabilirsiniz.
- `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` dosya (önceki sürümlerde sunulan) artık gerekli değildir. İşlevsellik artık temel SDK ile tümleşiktir.

**Örnekler**

[Örnek depoımızda](https://aka.ms/csspeech/samples)aşağıdaki yeni içerik mevcuttur:

- AudioConfig. Frommikro Phoneınput için ek örnekler.
- Amaç tanıma ve çeviri için ek Python örnekleri.
- İOS 'ta bağlantı nesnesini kullanmak için ek örnekler.
- Ses çıkışıyla çeviri için ek Java örnekleri.
- [Toplu Iş dökümü REST API](batch-transcription.md)kullanımı için yeni örnek.

**Geliştirmeler/değişiklikler**

- Python
  - SpeechConfig ' de geliştirilmiş parametre doğrulama ve hata iletileri.
  - Bağlantı nesnesi için destek ekleyin.
  - Windows üzerinde 32-bit Python (x86) için destek.
  - Python için konuşma SDK 'Sı beta dışında.
- iOS
  - SDK artık iOS SDK 12,1 sürümüne göre oluşturulmuştur.
  - SDK artık iOS sürümleri 9,2 ve üstünü desteklemektedir.
  - Başvuru belgelerini geliştirir ve birçok özellik adını düzeltir.
- JavaScript
  - Bağlantı nesnesi için destek ekleyin.
  - Paketlenmiş JavaScript için tür tanımı dosyaları ekleme
  - Tümcecik ipuçları için başlangıç desteği ve uygulama.
  - Tanıma için Service JSON ile Özellikler koleksiyonunu döndürün
- Windows dll 'Leri artık bir sürüm kaynağı içeriyor.
- Bir tanıyıcı oluşturursanız `FromEndpoint` doğrudan uç nokta URL 'sine parametreler ekleyebilirsiniz. `FromEndpoint` kullanarak, tanıyıcıyı standart yapılandırma özellikleri aracılığıyla yapılandıramazsınız.

**Hata düzeltmeleri**

- Boş Proxy Kullanıcı adı ve proxy parolası doğru işlenmedi. Bu sürümle birlikte, Proxy Kullanıcı adı ve proxy parolasını boş bir dizeye ayarlarsanız, ara sunucuya bağlanılırken gönderilmez.
- SDK tarafından oluşturulan SessionID, bazı diller&nbsp;/ortamları için her zaman gerçekten gelişigüzel değildir. Bu sorunu giderecek rastgele Oluşturucu başlatması eklendi.
- Yetkilendirme belirtecinin işlenmesini geliştirme. Bir yetkilendirme belirteci kullanmak istiyorsanız, SpeechConfig içinde belirtin ve abonelik anahtarını boş bırakın. Ardından tanıyıcı her zamanki gibi oluşturun.
- Bazı durumlarda bağlantı nesnesi doğru şekilde yayımlanmamıştı. Bu sorun düzeltildi.
- JavaScript örneği, çeviri birleştirmesinin ses çıkışını desteklemek için de Safari 'de düzeltildi.

## <a name="speech-sdk-121"></a>Konuşma SDK 'Sı 1.2.1

Bu yalnızca bir JavaScript sürümüdür. Hiçbir özellik eklenmedi. Aşağıdaki düzeltmeler yapılmıştır:

- Stream. End sırasında değil, Stream 'in sonuna kadar bir akış sonu tetiklendir.
- Geçerli gönderme başarısız olduysa, sonraki gönderimi zamanlamadığı ses göndericisinin hatasını düzeltir.
- Kimlik doğrulama belirteciyle sürekli tanımayı düzeltir.
- Farklı tanıyıcı/uç noktalar için hata düzeltildi.
- Belge geliştirmeleri.

## <a name="speech-sdk-120-2018-december-release"></a>Konuşma SDK 1.2.0:2018-Aralık yayını

**Yeni özellikler**

- Python
  - Python desteğinin beta sürümü (3,5 ve üzeri) Bu sürümde kullanılabilir. Daha fazla bilgi için buraya bakın] (hızlı başlangıç-python.md).
- JavaScript
  - JavaScript için konuşma SDK 'Sı açık kaynaklıdır. Kaynak kodu [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)' da kullanılabilir.
  - Artık Node. js ' yi destekliyoruz, [burada](quickstart-js-node.md)daha fazla bilgi bulabilirsiniz.
  - Ses oturumları için uzunluk kısıtlaması kaldırılmıştır, otomatik olarak yeniden bağlanma işlemi, kapak altında otomatik olarak gerçekleşir.
- Bağlantı nesnesi
  - Tanıyıcıdan bir bağlantı nesnesine erişebilirsiniz. Bu nesne, hizmet bağlantısını açık bir şekilde başlatabilmeniz ve olaylara bağlanıp bağlantıyı kesmeye abone olmanızı sağlar.
    (Bu özellik JavaScript ve Python 'da henüz kullanılamaz.)
- Ubuntu 18,04 desteği.
- Android
  - APK oluşturma sırasında ProGuard desteği etkinleştirildi.

**Geliştirmeleri**

- İç iş parçacığı kullanımındaki geliştirmeler, iş parçacığı sayısı, kilitler, zaman uyumu sağlayıcılar sayısını azaltır.
- Geliştirilmiş hata raporlama/bilgi. Birçok durumda, hata iletileri hiç bir şekilde yayılmaz.
- Güncel modüller kullanmak için JavaScript 'teki geliştirme bağımlılıkları güncelleştirildi.

**Hata düzeltmeleri**

- RecognizeAsync içindeki bir tür uyumsuzluğu nedeniyle sabit bellek sızıntıları.
- Bazı durumlarda özel durumlar sızdırılmıştı.
- Çeviri olay bağımsız değişkenlerinde bellek sızıntısı düzeltiliyor.
- Uzun süre çalışan oturumlarda yeniden bağlantı sırasında bir kilitleme sorunu düzeltildi.
- Başarısız çeviriler için eksik nihai sonuca neden olabilecek bir sorun düzeltildi.
- C#: Zaman uyumsuz bir işlem ana iş parçacığında beklenmediyse, zaman uyumsuz görev tamamlanmadan önce tanıyıcı atılamamalıdır.
- Java: bir sorun düzeltildi Java VM kilitlenmesiyle sonuçlanır.
- Amaç-C: sabit enum eşlemesi; RecognizedIntent, Recognizingamaç yerine döndürüldü.
- JavaScript: SpeechConfig içinde varsayılan çıkış biçimini ' Simple ' olarak ayarlayın.
- JavaScript: JavaScript ve diğer dillerdeki yapılandırma nesnesindeki özellikler arasında tutarsızlık kaldırılıyor.

**Örnekler**

- Birkaç örnek güncelleştirildi ve düzeltildi (örneğin, çeviri için çıkış sesleriniz vb.).
- [Örnek depoya](https://aka.ms/csspeech/samples)Node. js örnekleri eklendi.

## <a name="speech-sdk-110"></a>Konuşma SDK 1.1.0

**Yeni özellikler**

- Android x86/x64 desteği.
- Proxy desteği: SpeechConfig nesnesinde, artık proxy bilgilerini (konak adı, bağlantı noktası, Kullanıcı adı ve parola) ayarlamak için bir işlev çağırabilirsiniz. Bu özellik henüz iOS üzerinde kullanılamıyor.
- Geliştirilmiş hata kodu ve iletileri. Bir tanıma hata döndürmediğinde bu, zaten `Reason` (iptal edildi olayında) veya `CancellationDetails` (tanınma sonucunda) `Error`olarak ayarlanmıştır. İptal edilen olay artık iki ek üye içerir, `ErrorCode` ve `ErrorDetails`. Sunucu, bildirilen hata ile ek hata bilgileri döndürdüğünden, artık yeni üyelerde kullanılabilir olacaktır.

**Geliştirmeleri**

- Tanıyıcı yapılandırmasına ek doğrulama eklendi ve ek hata iletisi eklendi.
- Bir ses dosyasının ortasında uzun süreli sessizlik geliştirilmiş işlem.
- NuGet paketi: .NET Framework projeleri için, AnyCPU yapılandırmasıyla oluşturmayı engeller.

**Hata düzeltmeleri**

- Tanıyıcılar içinde bulunan bazı özel durumlar düzeltildi. Ayrıca, özel durumlar yakalanıp Iptal edildi olayına dönüştürülür.
- Özellik yönetiminde bellek sızıntısı 'nı düzeltir.
- Bir ses giriş dosyasının tanıyıcıyı kilitlenebildiği bir hata düzeltildi.
- Bir oturum durdurma olayından sonra olayların alınabileceği bir hata düzeltildi.
- İş parçacığı oluşturma sırasında bazı yarış koşulları düzeltildi.
- Bir çökmesine neden olabilecek bir iOS uyumluluğu sorunu düzeltildi.
- Android mikrofon desteği için kararlılık geliştirmeleri.
- JavaScript 'teki bir tanıyıcıın tanıma dilini yoksaymasına neden olan bir hata düzeltildi.
- JavaScript içindeki EndpointId (bazı durumlarda) ayarlanmasını engellediği bir hata düzeltildi.
- JavaScript 'te Addamaciçinde parametre sırası değiştirildi ve eksik Addamacını JavaScript imzası eklendi.

**Örnekler**

- C++ [Örnek depoda](https://aka.ms/csspeech/samples)çekme ve gönderme akışı kullanımı için ve C# Samplea eklendi.

## <a name="speech-sdk-101"></a>Konuşma SDK 1.0.1

Güvenilirlik geliştirmeleri ve hata düzeltmeleri:

- Disposing tanıyıcı içindeki yarış durumu nedeniyle olası önemli hata düzeltildi
- Özellik ayarı sırasında olası önemli hata düzeltildi.
- Ek hata ve parametre denetimi eklendi.
- Amaç-C: NSString 'de geçersiz kılma adı nedeniyle olası önemli hata düzeltildi.
- Hedef-C: API 'nin görünürlüğü düzeltildi
- JavaScript: olaylar ve yükleri hakkında düzeltildi.
- Belge geliştirmeleri.

[Örnek depoımızda](https://aka.ms/csspeech/samples)JavaScript için yeni bir örnek eklenmiştir.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Bilişsel hizmetler konuşma SDK 1.0.0:2018-Eylül sürümü

**Yeni özellikler**

- İOS üzerinde amaç-C desteği. [İOS Için hedef C hızlı başlangıç](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)öğreticimize göz atın.
- Tarayıcıda JavaScript desteği. [JavaScript hızlı](quickstart-js-browser.md)başlangıçmıza göz atın.

**Son değişiklikler**

- Bu sürümle birlikte, bir dizi Son değişiklik yapılmıştır.
  Ayrıntılar için [Bu sayfaya](https://aka.ms/csspeech/breakingchanges_1_0_0) bakın.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Bilişsel hizmetler konuşma SDK 0.6.0:2018-Ağustos sürümü

**Yeni özellikler**

- Konuşma SDK 'Sı ile oluşturulan UWP uygulamaları artık Windows uygulama sertifikasyon seti 'Ni (WACK) geçirebilir.
  [UWP hızlı](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)başlangıcı ' nı inceleyin.
- Linux (Ubuntu 16,04 x64) üzerinde .NET Standard 2,0 desteği.
- Deneysel: Windows (64-bit) ve Linux (Ubuntu 16,04 x64) üzerinde Java 8 desteği.
  [Java Runtime Environment hızlı](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)başlangıcı ' nı inceleyin.

**İşlevsel değişiklik**

- Bağlantı hatalarıyla ilgili ek hata ayrıntısı bilgilerini kullanıma sunun.

**Son değişiklikler**

- Java 'da (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` işlevi artık bir yol parametresi gerektirmez. Artık tüm desteklenen platformlarda yol otomatik olarak algılanır.
- Özelliğin get-erişimcisi Java 'da `EndpointUrl` ve C# kaldırılmıştır.

**Hata düzeltmeleri**

- Java 'da, ses birleştirme, çeviri tanıyıcısının sonucu olarak uygulanır.
- Etkin olmayan iş parçacıklarına ve daha fazla açık ve kullanılmayan yuva sayısına neden olabilecek bir hata düzeltildi.
- Uzun süre çalışan bir tanımanın iletim ortasında sonlanabilir olduğu bir sorun düzeltildi.
- Tanıyıcı kapatılırken bir yarış durumu düzeltildi.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Bilişsel hizmetler konuşma SDK 0.5.0:2018-Temmuz sürümü

**Yeni özellikler**

- Android platformu desteği (API 23: Android 6,0 sıralamada Mallow veya üzeri). [Android hızlı başlangıç](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)' a göz atın.
- Windows üzerinde 2,0 .NET Standard destekler. [.NET Core hızlı başlangıç](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)' i inceleyin.
- Deneysel: Windows 'da UWP 'yi destekleme (sürüm 1709 veya üzeri).
  - [UWP hızlı](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)başlangıcı ' nı inceleyin.
  - Not: konuşma SDK 'Sı ile derlenen UWP uygulamaları henüz Windows uygulama sertifikasyon Kit 'ı (WACK) iletmez.
- Otomatik yeniden bağlantı ile uzun süre çalışan tanıma desteği.

**İşlevsel değişiklikler**

- `StartContinuousRecognitionAsync()` uzun süre çalışan tanımayı destekler.
- Tanınma sonucu daha fazla alan içeriyor. Bunlar, tanınan metnin ses başlangıcından ve süresinden (her ikisi de Tick) ve tanınma durumunu temsil eden ek değerlerin (örneğin, `InitialSilenceTimeout` ve `InitialBabbleTimeout`) denkleştirilir.
- Fabrika örnekleri oluşturmak için AuthorizationToken 'ı destekleme.

**Son değişiklikler**

- Tanıma olayları: NoMatch olay türü hata olayında birleştirildi.
- SpeechOutputFormat içinde C# , ile C++hizalı kalmak için OutputFormat olarak yeniden adlandırıldı.
- `AudioInputStream` arabirimindeki bazı yöntemlerin dönüş türü biraz değişti:
  - Java 'da `read` yöntemi artık `int`yerine `long` döndürüyor.
  - ' C#De `Read` yöntemi artık `int`yerine `uint` döndürüyor.
  - ' C++De, `Read` ve `GetFormat` yöntemleri artık `int`yerine `size_t` döndürüyor.
- C++: Ses giriş akışlarının örnekleri artık yalnızca bir `shared_ptr`olarak geçirilebilir.

**Hata düzeltmeleri**

- `RecognizeAsync()` zaman aşımı olduğunda sonuçdaki hatalı dönüş değerleri düzeltildi.
- Windows 'daki Media Foundation kitaplıkları bağımlılığı kaldırılmıştır. SDK artık çekirdek ses API 'Lerini kullanır.
- Belge onarımı: desteklenen bölgeleri anlatmak için bir [bölgeler](regions.md) sayfası eklendi.

**Bilinen sorun**

- Android için konuşma SDK 'Sı, çeviri için konuşma sonuçları raporlamaz. Bu sorun bir sonraki sürümde düzeltilecektir.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Bilişsel hizmetler konuşma SDK 0.4.0:2018-Haziran sürümü

**İşlevsel değişiklikler**

- Audioınputstream

  Bir tanıyıcı artık ses kaynağı olarak bir akış kullanabilir. Daha fazla bilgi için ilgili [nasıl yapılır Kılavuzu](how-to-use-audio-input-streams.md)' na bakın.

- Ayrıntılı çıkış biçimi

  Bir `SpeechRecognizer`oluşturduğunuzda, `Detailed` veya `Simple` çıkış biçimi isteyebilirsiniz. `DetailedSpeechRecognitionResult`, maskeli bir küfür ile Güvenirlik puanı, tanınan metin, ham sözlü form, normalleştirilmiş form ve normalleştirilmiş form içerir.

**Son değişiklik**

- İçindeki C#`SpeechRecognitionResult.RecognizedText` `SpeechRecognitionResult.Text` olarak değiştirildi.

**Hata düzeltmeleri**

- Kapatmadan önce USP katmanında olası bir geri çağırma sorunu düzeltildi.

- Bir tanıyıcı bir ses giriş dosyası kullanıyorsa, dosya tanıtıcısına gerekenden daha uzun süre içinde tutulmakta.

- İleti göndericisi ve tanıyıcı arasında birkaç kilitlenme kaldırıldı.

- Hizmetten gelen yanıt zaman aşımına uğradığından `NoMatch` bir sonuç tetikle.

- Windows 'daki Media Foundation kitaplıkları gecikmeli yüklendi. Bu kitaplık yalnızca mikrofon girişi için gereklidir.

- Ses verileri için karşıya yükleme hızı, özgün ses hızının iki katı ile sınırlıdır.

- Windows 'da, C# .NET derlemeleri artık güçlü adlandırılmış.

- Belge onarımı: `Region` tanıyıcı oluşturmak için gerekli bilgiler.

Daha fazla örnek eklenmiştir ve sürekli olarak güncelleştirilir. En son örnek kümesi için bkz. [konuşma SDK örnekleri GitHub deposu](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Bilişsel hizmetler konuşma SDK 0.2.12733:2018-Mayıs yayını

Bu sürüm, bilişsel hizmetler konuşma SDK 'sının ilk genel önizleme sürümüdür.
