---
title: İçeriğinizi Azure Media Services koruyun | Microsoft Docs
description: Bu makalede Azure Media Services V2 ile içerik korumasına genel bakış sunulmaktadır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2922586d586dbbb98ae85fb86f3f76889a75aa6b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067410"
---
# <a name="content-protection-overview"></a>İçerik korumaya genel bakış

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Ortamınızı, depolama, işleme ve teslim aracılığıyla bilgisayarınızdan ayrıldığı zamandan korumak için Azure Media Services kullanabilirsiniz. Media Services ile canlı ve isteğe bağlı içeriğinizi Gelişmiş Şifreleme Standardı (AES-128) veya üç ana dijital hak yönetimi (DRM) sisteminden (Microsoft PlayReady, Google Widevine ve Apple FairPlay ile) dinamik olarak dağıtabilirsiniz. Media Services, yetkili istemcilere AES anahtarları ve DRM (PlayReady, Widevine ve FairPlay) lisanslarını sunmaya yönelik bir hizmet sağlar. 

Aşağıdaki görüntüde içerik koruma iş akışı Media Services gösterilmektedir: 

![PlayReady ile koruma](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Bu makalede, Media Services ile içerik korumayı anlamak için ilgili kavramlar ve terminoloji açıklanmaktadır. Makale Ayrıca içeriği nasıl koruyabileceğinizi tartışan makalelere bağlantılar sağlar. 

## <a name="dynamic-encryption&quot;></a>Dinamik şifreleme

Media Services, PlayReady, Widevine veya FairPlay kullanarak, içeriğinizi AES şifresiz anahtar veya DRM şifrelemesi ile dinamik olarak dağıtmak için kullanabilirsiniz. İçerik AES şifresiz anahtarla şifrelenirse ve HTTPS üzerinden gönderildiğinde, istemciye ulaşıncaya kadar net değildir. 

Her şifreleme yöntemi aşağıdaki akış protokollerini destekler:
 
- AES: MPEG-DASH, Kesintisiz Akış ve HLS
- PlayReady: MPEG-DASH, Kesintisiz Akış ve HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Aşamalı indirmelerde şifreleme desteklenmez. 

Bir varlığı şifrelemek için bir şifreleme içerik anahtarını varlığınızla ilişkilendirmeniz ve ayrıca anahtar için bir Yetkilendirme İlkesi yapılandırmanız gerekir. İçerik anahtarları belirtilebilir veya Media Services tarafından otomatik olarak oluşturulabilir.

Ayrıca varlığın teslim ilkesini de yapılandırmanız gerekir. Depolama ile şifrelenen bir varlığı akışa almak istiyorsanız, varlık teslim ilkesini yapılandırarak nasıl teslim etmek istediğinizi belirttiğinizden emin olun.

Bir akış bir oyuncu tarafından istendiğinde, Media Services AES şifresiz anahtar veya DRM şifrelemesini kullanarak içeriğinizi dinamik olarak şifrelemek için belirtilen anahtarı kullanır. Akışın şifresini çözmek için, Player anahtarı Media Services anahtar teslim hizmetinden ister. Kullanıcının anahtarı almak için yetkilendirilip yetkilendirilmeyeceğine karar vermek için, hizmet anahtar için belirttiğiniz yetkilendirme ilkelerini değerlendirir.

## <a name=&quot;aes-128-clear-key-vs-drm&quot;></a>AES-128 şifresiz anahtar ve DRM
Müşteriler genellikle AES şifrelemesi mi yoksa bir DRM sistemi mi kullanacağınızı merak ediyor. İki sistem arasındaki birincil fark, AES şifrelemesi ile içerik anahtarının istemciye şifrelenmemiş bir biçimde (&quot;açık") aktarıldıysa. Sonuç olarak, içeriği şifrelemek için kullanılan anahtar, istemci üzerindeki bir ağ izlemede düz metin olarak görüntülenebilir. AES-128 şifresiz anahtar şifrelemesi, görüntüleyicisinin güvenilir bir taraf olduğu (örneğin, bir şirkette şirket içinde dağıtılan şirket videolarını, çalışanlar tarafından görüntülenmek üzere şifreleme) kullanım durumları için uygundur.

PlayReady, Widevine ve FairPlay All, AES-128 şifresiz anahtar şifrelemeye kıyasla daha yüksek düzeyde şifreleme sağlar. İçerik anahtarı şifreli bir biçimde iletilir. Ayrıca, şifre çözme, işletim sistemi düzeyinde güvenli bir ortamda işlenir ve bu, kötü niyetli bir kullanıcının saldırılarına karşı daha zordur. Görüntüleyici 'nin güvenilir bir taraf olmadığı ve en yüksek düzeyde güvenlik düzeyi gerektiren kullanım durumları için DRM önerilir.

## <a name="storage-encryption"></a>Depolama şifrelemesi
AES 256 bit şifrelemeyi kullanarak açık içeriğinizi yerel olarak şifrelemek için depolama şifrelemesini kullanabilirsiniz. Daha sonra bu dosyayı Azure depolama 'ya yükleyebilirsiniz ve burada şifreli olarak depolanır. Depolama şifrelemesi ile korunan varlıklar, kodlama öncesinde otomatik olarak şifrelenmez ve şifreli bir dosya sistemine yerleştirilir. Varlıklar, yeni bir çıkış varlığı olarak geri yüklenmeden önce isteğe bağlı olarak yeniden şifrelenir. Depolama şifrelemesi için birincil kullanım örneği, yüksek kaliteli giriş medya dosyalarınızı diskte bekleyen bir şekilde güçlü şifreleme ile güvenli hale getirmek istediğinizde kullanılır.

Depolama ile şifrelenen bir varlık sağlamak için, varlığın teslim ilkesini, Media Services içeriğinizi nasıl teslim etmek istediğinizi bilmek üzere yapılandırmanız gerekir. Varlığınızın akışı silinmeden önce, akış sunucusu belirtilen teslim ilkesini (örneğin, AES, ortak şifreleme veya şifreleme olmadan) kullanarak içeriğinizi çözer ve akışlar.

## <a name="types-of-encryption"></a>Şifreleme türleri
PlayReady ve Widevine, ortak şifrelemeyi (AES Mrk modu) kullanır. FairPlay, AES CBC modu şifrelemesini kullanır. AES-128 Clear anahtar şifrelemesi, zarf şifrelemesini kullanır.

## <a name="licenses-and-keys-delivery-service"></a>Lisanslar ve anahtarlar teslim hizmeti
Media Services, yetkili istemcilere DRM (PlayReady, Widevine, FairPlay) lisanslarını ve AES anahtarlarını sunmaya yönelik bir anahtar teslim hizmeti sağlar. Lisanslar ve anahtarlarınız için yetkilendirme ve kimlik doğrulama ilkelerini yapılandırmak üzere .NET için [Azure Portal](media-services-portal-protect-content.md), REST API veya Media Services SDK 'sını kullanabilirsiniz.

## <a name="control-content-access"></a>İçerik erişimini denetleme
İçerik anahtarı yetkilendirme ilkesini yapılandırarak içeriğinizi kimlerin erişebileceğini denetleyebilirsiniz. İçerik anahtarı yetkilendirme ilkesi, açık ya da belirteç kısıtlamasını destekler.

### <a name="open-authorization"></a>Yetkilendirmeyi aç
Açık bir yetkilendirme ilkesiyle, içerik anahtarı herhangi bir istemciye gönderilir (kısıtlama yoktur).

### <a name="token-authorization"></a>Belirteç yetkilendirme
Belirteç kısıtlı yetkilendirme ilkesiyle, içerik anahtarı yalnızca anahtar/lisans isteğinde geçerli bir JSON Web Token (JWT) veya basit Web belirteci (SWT) sunan bir istemciye gönderilir. Bu belirtecin bir güvenlik belirteci hizmeti (STS) tarafından verilmesi gerekir. Azure Active Directory STS olarak kullanabilir veya özel bir STS dağıtımı yapabilirsiniz. STS, belirtilen anahtarla imzalanmış bir belirteç oluşturacak ve belirteç kısıtlama yapılandırmasında belirttiğiniz talepler verecek şekilde yapılandırılmalıdır. Media Services anahtar teslim hizmeti, belirteç geçerliyse ve belirteçteki talepler anahtar/lisans için yapılandırılananlarla eşleşiyorsa istemciye istenen anahtarı/lisansı döndürür.

Belirteç kısıtlı ilkesini yapılandırırken, birincil doğrulama anahtarını, verenin ve hedef kitle parametrelerini belirtmeniz gerekir. Birincil doğrulama anahtarı, belirtecin imzalandığı anahtarı içerir. Veren, belirteci veren güvenli belirteç hizmetidir. Bazen kapsam olarak adlandırılan hedef kitle, belirtecin amacını veya belirtecin erişim yetkisi aldığı kaynağı açıklar. Media Services anahtar teslim hizmeti, belirteçteki bu değerlerin şablondaki değerlerle eşleştiğini doğrular.

### <a name="token-replay-prevention"></a>Belirteç yeniden yürütme engellemesi

*Belirteç yeniden yürütme engellemesi* özelliği, Media Services müşterilerin aynı belirtecin bir anahtar veya lisans istemek için kaç kez kullanılabileceği konusunda bir sınır ayarlamasına olanak tanır. Müşteri, `urn:microsoft:azure:mediaservices:maxuses` belirtecin bir lisans ya da anahtar almak için kaç kez kullanılabileceğini, belirteçte bir tür talep ekleyebilir. Anahtar teslimine aynı belirtece sahip sonraki tüm istekler, yetkisiz bir yanıt verecektir. Bkz. isteği [DRM örneğine](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)ekleme.
 
#### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Müşterilerin belirteç oluşturma üzerinde denetimi olmalıdır. Talebin kendisini belirtece yerleştirilmesi gerekir.
* Bu özellik kullanılırken, zaman aşımı süresi, isteğin alındığı zamandan bir saatten fazla olan istekleri yetkisiz bir Yanıtla reddedilir.
* Belirteçler, imzaları tarafından benzersiz şekilde tanımlanır. Yükte yapılan herhangi bir değişiklik (örneğin, sona erme saati veya talebin güncelleştirilmesi) belirtecin imzasını değiştirir ve anahtar tesliminin daha önce geldiği yeni bir belirteç olarak sayılır.
* Belirteç müşteri tarafından ayarlanan değeri aşarsa kayıttan yürütme başarısız olur `maxuses` .
* Bu özellik, var olan tüm korumalı içerik (yalnızca verilen belirtecin değiştirilmesi gerekir) için kullanılabilir.
* Bu özellik hem JWT hem de SWT ile birlikte kullanılabilir.

## <a name="streaming-urls"></a>Akış URL 'Leri
Varlığınız birden fazla DRM ile şifrelendiyse, akış URL 'sinde bir şifreleme etiketi kullanın: (format = 'm 3u8-AAPL ', ENCRYPTION = ' xxx ').

Aşağıdaki noktalara dikkat edilmelidir:

* Birden fazla şifreleme türü belirtilemez.
* Varlığa yalnızca bir şifreleme uygulanmışsa, şifreleme türünün URL 'de belirtilmesi gerekmez.
* Şifreleme türü, büyük/küçük harfe duyarlıdır.
* Aşağıdaki şifreleme türleri belirtilebilir:

  * **CENC**: PlayReady veya Widevine (ortak şifreleme) için
  * **CBCS-AAPL**: Fairplay IÇIN (AES CBC şifrelemesi)
  * **CBC**: AES zarf şifrelemesi için

## <a name="additional-notes"></a>Ek notlar

* Widevine, Google Inc. tarafından sunulan bir hizmettir ve Google, Inc 'nin hizmet koşullarına ve gizlilik Ilkesine tabidir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleler, içerik korumasına başlamanıza yardımcı olacak sonraki adımları anlatmaktadır:

* [Depolama şifrelemesi ile koruma](media-services-rest-storage-encryption.md)
* [AES şifrelemesi ile koruma](media-services-playready-license-template-overview.md)
* [PlayReady ve/veya Widevine ile koruma](media-services-protect-with-playready-widevine.md)
* [FairPlay ile koruma](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>İlgili bağlantılar

* [JWT belirteci kimlik doğrulaması](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Azure Media Services OWIN MVC tabanlı uygulama Azure Active Directory ve JWT taleplerine göre içerik anahtarı teslimini tümleştirin](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
