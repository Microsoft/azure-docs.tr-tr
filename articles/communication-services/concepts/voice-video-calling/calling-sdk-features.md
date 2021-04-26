---
title: SDK 'ya genel bakış çağıran Azure Iletişim Hizmetleri
titleSuffix: An Azure Communication Services concept document
description: Çağıran SDK 'ya genel bakış sağlar.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 28072184d47beb32dc03e0d6ba52328bfceb5b73
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364881"
---
# <a name="calling-sdk-overview"></a>SDK 'ya genel bakış

Çağıran SDK, son kullanıcı cihazlarının sesli ve görüntülü iletişim deneyimlerini kullanmasına olanak sağlar. Bu sayfa, platform ve tarayıcı destek bilgileri de dahil olmak üzere, çağırma özelliklerinin ayrıntılı açıklamalarını sağlar. Hemen başlamak için, lütfen hızlı başlangıçlara [çağrı](../../quickstarts/voice-video-calling/getting-started-with-calling.md) yapın veya [Hero örneği çağırma](../../samples/calling-hero-sample.md)konusuna bakın. 

Geliştirmeye başladıktan sonra, üzerinde çalıştığımız hataları bulmak için [bilinen sorunlar sayfasına](../known-issues.md) göz atın.

Çağıran SDK 'nın temel özellikleri:

- **Adresleme** -Azure iletişim hizmetleri, iletişim uç noktalarına yönelik olarak kullanılan genel [kimlikler](../identity-model.md) sağlar. İstemciler bu kimlikleri, hizmette kimlik doğrulamak ve birbirleriyle iletişim kurmak için kullanır. Bu kimlikler, istemcilere, bir çağrıya (Roster) bağlı olan kullanıcılara görünürlük sağlayan API 'Leri çağırmak için kullanılır.
- **Şifreleme** -çağıran SDK trafiği şifreler ve tel karşı izinsiz değişiklik yapılmasını önler. 
- **Cihaz yönetimi ve medya** -çağıran SDK, ses ve video cihazlarına bağlama, iletişim veri düzlemi üzerinden etkili iletim için içerik kodlama ve içeriği çıktı cihazlarına ve belirttiğiniz görünümlere göre oluşturan olanaklar sunar. Ayrıca, ekran ve uygulama paylaşımı için API 'Ler de sağlanır.
- **PSTN** -çağıran SDK, Azure Portal veya programlı olarak [elde ettiğiniz telefon numaralarını kullanarak](../../quickstarts/telephony-sms/get-phone-number.md) geleneksel, genel olarak anahtarlamalı telefon sistemiyle sesli çağrılar alabilir ve başlatabilir.
- **Takımlar toplantıları** -çağıran SDK, [takımlar toplantılarına katılabilir](../../quickstarts/voice-video-calling/get-started-teams-interop.md) ve takımlar ses ve video veri düzlemi ile etkileşime geçebilir. 
- **Bildirimler** -çağıran SDK, istemcilere gelen bir çağrı hakkında bildirim almasına Izin veren API 'ler sağlar. Uygulamanızın ön planda çalışmadığı durumlarda, gelen bir çağrının son kullanıcılarını bilgilendirmek için açılır bildirimleri ("toyalar") [tetiklemesi](../notifications.md) için desenler kullanılabilir. 

## <a name="detailed-capabilities"></a>Ayrıntılı yetenekler 

Aşağıdaki liste, şu anda SDK 'Ları çağıran Azure Iletişim hizmetlerinde kullanılabilen özellikler kümesini gösterir.

| Özellik grubu | Özellik                                                                                                          | JS  | Java (Android) | Objective-C (iOS)
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Temel yetenekler | İki kullanıcı arasında bire bir çağrı yerleştir                                                                           | ✔️   | ✔️            | ✔️
|                   | İkiden fazla kullanıcısı olan bir grup çağrısı Yerleştir (en fazla 350 Kullanıcı)                                                       | ✔️   | ✔️            | ✔️
|                   | İki kullanıcıyla daha fazla kullanıcı içeren bir grup çağrısında bir tek-bir çağrıyı yükseltin                                 | ✔️   | ✔️            | ✔️
|                   | Başlatıldıktan sonra bir grup çağrısına katılır                                                                              | ✔️   | ✔️            | ✔️
|                   | Başka bir VoIP katılımcısını devam eden bir grup çağrısına katılmaya davet etme                                                       | ✔️   | ✔️            | ✔️
|  PARÇAAL çağrısı denetimi | Videonuzu açma/kapatma                                                                                              | ✔️   | ✔️            | ✔️
|                   | Mikrofonu sustur/aç                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Kameralar arasında geçiş yapma                                                                                              | ✔️   | ✔️            | ✔️
|                   | Yerel saklama/tutma                                                                                                  | ✔️   | ✔️            | ✔️
|                   | Etkin konuşmacı                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Çağrılar için konuşmacı seçin                                                                                            | ✔️   | ✔️            | ✔️
|                   | Çağrılar için mikrofon seçin                                                                                         | ✔️   | ✔️            | ✔️
|                   | Katılımcının durumunu göster<br/>*Boşta, erken medya, bağlanma, bağlı, bekleme süresi, giriş, bağlantısız*         | ✔️   | ✔️            | ✔️
|                   | Bir çağrının durumunu göster<br/>*Erken medya, gelen, bağlantı, çalma, bağlı, bekletme, bağlantısı kesiliyor, bağlantısı kesildi* | ✔️   | ✔️            | ✔️
|                   | Bir katılımcının kapalı olup olmadığını göster                                                                                      | ✔️   | ✔️            | ✔️
|                   | Katılımcının bir çağrı bıraktı nedenini göster                                                                       | ✔️   | ✔️            | ✔️
| Ekran paylaşımı    | Uygulamanın içinden ekranın tamamını paylaşma                                                                 | ✔️   | ❌            | ❌
|                   | Belirli bir uygulamayı paylaşma (çalışan uygulamalar listesinden)                                                | ✔️   | ❌            | ❌
|                   | Açık sekmeler listesinden bir Web tarayıcısı sekmesi paylaşma                                                                  | ✔️   | ❌            | ❌
|                   | Katılımcı, uzak ekran paylaşımından görüntüleyebilir                                                                            | ✔️   | ✔️            | ✔️
| Listesi            | Katılımcıları Listele                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Katılımcıyı kaldırma                                                                                                | ✔️   | ✔️            | ✔️
| PSTN              | PSTN katılımcısı ile bire bir çağrı yerleştirme                                                                     | ✔️   | ✔️            | ✔️
|                   | PSTN katılımcıları ile bir grup çağrısı yerleştirme                                                                           | ✔️   | ✔️            | ✔️
|                   | Bir PSTN katılımcısı ile bire bir çağrıyı bir grup çağrısına yükseltme                                                 | ✔️   | ✔️            | ✔️
|                   | Bir grup çağrısından PSTN katılımcısı olarak dışarıyı arama                                                                    | ✔️   | ✔️            | ✔️
| Genel           | Mikrofon, konuşmacı ve kameranızı bir ses sınama hizmeti (8 ' i çağırarak kullanılabilir: echo123) ile test edin                   | ✔️   | ✔️            | ✔️
| Aygıt Yönetimi | Ses ve/veya video kullanma izni iste                                                                       | ✔️   | ✔️            | ✔️
|                   | Kamera listesini al                                                                                                     | ✔️   | ✔️            | ✔️
|                   | Kamerayı ayarla                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Seçili kamerayı al                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Mikrofon listesini al                                                                                                 | ✔️   | ❌           |❌  
|                   | Mikrofonu ayarla                                                                                                      | ✔️   | ❌           | ❌  
|                   | Seçili mikrofonu al                                                                                             | ✔️   | ❌           | ❌  
|                   | Konuşmacı listesini al                                                                                                   | ✔️   | ❌           | ❌  
|                   | Konuşmacı ayarla                                                                                                         | ✔️   | ❌           | ❌  
|                   | Seçili konuşmacıyı al                                                                                                | ✔️   | ❌           | ❌  
| Video Işleme   | Tek bir videoyu birçok yerde (yerel kamera veya uzak akış) işleme                                                  | ✔️   | ✔️            | ✔️
|                   | Ölçek modunu ayarla/Güncelleştir                                                                                           | ✔️   | ✔️            | ✔️
|                   | Uzak video akışını işle                                                                                          | ✔️   | ✔️            | ✔️

## <a name="calling-sdk-streaming-support"></a>SDK akış desteğini çağırma
SDK 'Yı çağıran Iletişim Hizmetleri aşağıdaki akış yapılandırmasını destekler:

| Sınır          |Web | Android/iOS|
|-----------|----|------------|
|**aynı anda gönderilebilecek giden akış sayısı** |1 video veya 1 ekran paylaşımı | 1 video + 1 ekran paylaşımı|
|**aynı anda işlenebilen gelen akış sayısı** |1 video veya 1 ekran paylaşımı| 6 video + 1 ekran paylaşımı |

## <a name="calling-sdk-timeouts"></a>SDK zaman aşımlarını çağırma

SDK 'Ları çağıran Iletişim Hizmetleri için aşağıdaki zaman aşımları geçerlidir:

| Eylem           | Saniye olarak zaman aşımı |
| -------------- | ---------- |
| Yeniden bağlanma/kaldırma Katılımcısı | 120 |
| Bir çağrıdan yeni modlılık ekleme veya kaldırma (video veya ekran paylaşımını Başlat/Durdur) | 40 |
| Çağrı aktarımı işlem zaman aşımı | 60 |
| 1:1 çağrı kurma zaman aşımı | 85 |
| Grup çağrısı kurma zaman aşımı | 85 |
| PSTN çağrısı kurma zaman aşımı | 115 |
| Bir grup çağrısı zaman aşımı için 1:1 çağrısını yükseltin | 115 |

## <a name="javascript-calling-sdk-support-by-os-and-browser"></a>OS ve tarayıcı tarafından SDK desteğini çağıran JavaScript

Aşağıdaki tablo şu anda kullanılabilir olan desteklenen tarayıcıların kümesini temsil eder. Aksi belirtilmedikçe tarayıcının en son üç sürümünü destekliyoruz.

| Platform                         | Chrome | Uygulamasını  | Edge (Kmıum) |
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Safari sürümleri 13.1 + desteklenir, Safari 'de 1:1 çağrıları desteklenmez.

* * Safari 14 +/macOS 11 + giden video desteği için gereklidir.

Giden ekran paylaşımı, tarayıcı sürümünden bağımsız olarak yalnızca masaüstü platformlarında (Windows, macOS ve Linux) desteklenir ve herhangi bir mobil platformda (Android, iOS, iPad ve tabletlerde) desteklenmez.

Safari 'deki bir iOS uygulaması, MIC ve konuşmacı cihazlarını numaralandırılamıyor/seçemezsiniz (örneğin, Bluetooth); Bu, işletim sisteminin bir kısıtlamasıdır ve her zaman yalnızca bir cihaz vardır.


## <a name="calling-client---browser-security-model"></a>Çağıran istemci-tarayıcı güvenlik modeli

### <a name="user-webrtc-over-https"></a>HTTPS üzerinden Kullanıcı WebRTC

Gibi WebRTC API 'Leri `getUserMedia` , bu API 'leri çağıran UYGULAMANıN https üzerinden sunulmasını gerektirir.

Yerel geliştirme için kullanabilirsiniz `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>SDK 'Yı çağıran Iletişim hizmetlerini iframe 'e ekleme

Yeni bir [izinler ilkesi (özellik ilkesi de denir)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) çeşitli tarayıcılar tarafından benimsenmekte. Bu ilke, uygulamaların bir çapraz kaynak iframe öğesi aracılığıyla bir cihazın kameraya ve mikrofona nasıl erişebileceğini denetleyerek çağrı senaryolarını etkiler.

Uygulamanın bir parçasını farklı bir etki alanından barındırmak için bir iframe kullanmak istiyorsanız, `allow` özniteliğini doğru değeri olan IFRAME 'nize eklemeniz gerekir.

Örneğin, bu iframe hem kamera hem de mikrofon erişimine izin verir:

```html
<iframe allow="camera *; microphone *">
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Çağırma ile çalışmaya başlayın](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
- Genel [çağrı akışları](../call-flows.md) hakkında bilgi edinin
- [Çağrı türleri](../voice-video-calling/about-call-types.md) hakkında bilgi edinin
- [PSTN çözümünüzü planlayın](../telephony-sms/plan-solution.md)
