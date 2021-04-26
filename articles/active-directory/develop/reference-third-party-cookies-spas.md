---
title: Safari 'de akıllı Izleme koruması 'nı (ıOP) işleme | Mavisi
titleSuffix: Microsoft identity platform
description: Üçüncü taraf tanımlama bilgilerine artık izin verilmediği zaman tek sayfalı uygulama (SPA) kimlik doğrulaması.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753971"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Safari 'de ve üçüncü taraf tanımlama bilgilerinin engellendiği diğer tarayıcılarda ıOP 'yi işleme

Günümüzde birçok tarayıcı, üçüncü taraf tanımlama bilgilerini (tarayıcı çubuğunda gösterle aynı olmayan etki alanlarına yönelik isteklere yönelik tanımlama bilgilerini) engellemektedir. Bu, örtük akışı keser ve kullanıcılara başarıyla oturum açmak için yeni kimlik doğrulama desenleri gerektirir. Microsoft Identity platformunda, üçüncü taraf tanımlama bilgileri engellendiğinde kullanıcıların oturum açmasından haberdar olmak için PKI ve yenileme belirteçleriyle yetkilendirme akışını kullanırız.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Akıllı Izleme koruması (ıOP) nedir?

Apple Safari, [akıllı Izleme koruması](https://webkit.org/tracking-prevention-policy/)veya *IOP* adlı bir varsayılan gizlilik koruması özelliğine sahiptir. IOP, "üçüncü taraf" tanımlama bilgilerini, etki alanlarını çapraz istekler üzerinde tanımlama bilgilerini engeller.

Yaygın olarak kullanılan bir kullanıcı izleme formu, arka planda üçüncü taraf siteye bir iframe yükleyerek ve kullanıcının Internet üzerinden ilişkilendirilmesi için tanımlama bilgilerinin kullanılmasına göre yapılır. Ne yazık ki bu model ayrıca tek sayfalı uygulamalarda (maça) [örtük akışı](v2-oauth2-implicit-grant-flow.md) uygulamanın standart yoludur. Tarayıcı, üçüncü taraf tanımlama bilgilerini Kullanıcı izlemeyi engelleyecek şekilde engellediğinde, maça da bozulur.

Safari, Kullanıcı gizliliğini geliştirmek için yalnızca üçüncü taraf tanımlama bilgilerini engellemeye yönelik değildir. Brave, üçüncü taraf tanımlama bilgilerini varsayılan olarak engellemiş ve Kmıum (Google Chrome ve Microsoft Edge arkasındaki Platform), gelecekte üçüncü taraf tanımlama bilgilerini desteklemeyi durdurduğunu duyurdu.

Bu makalede özetlenen çözüm bu tarayıcıların tümünde veya her yerde üçüncü taraf tanımlama bilgilerinin engellenmesi ile çalışmaktadır.

## <a name="overview-of-the-solution"></a>Çözüme genel bakış

SPAs 'de kullanıcıların kimliğini doğrulamaya devam etmek için, uygulama geliştiricilerinin [yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md)kullanması gerekir. Kimlik doğrulama kod akışında, kimlik sağlayıcısı bir kodu yayınlar ve SPA 'nın kodu bir erişim belirteci ve yenileme belirteci için yeniden EMS 'yi kullanıyor. Uygulama ek belirteçler gerektirdiğinde, yeni belirteçleri almak için [yenileme belirteci akışını](v2-oauth2-auth-code-flow.md#refresh-the-access-token) kullanabilir. MSAL.js 2,0, maça için Microsoft Identity platform kitaplığı, SPAs için yetkilendirme kodu akışını ve küçük güncelleştirmelerle birlikte, MSAL.js 1. x için bir bırakma değişikliği uygular.

Microsoft Identity platformu için, SPAs ve yerel istemciler benzer protokol kılavuzlarını izler:

* Bir [Pkce kod sınaması](https://tools.ietf.org/html/rfc7636) kullanımı
    * Microsoft Identity platformunda SPAs için PKCE *gereklidir* . PKI CE, yerel ve gizli istemciler için *önerilir* .
* İstemci parolasının kullanımı

Spaon iki ek kısıtlama vardır:

* [Yeniden yönlendirme URI 'si `spa` tür olarak işaretlenmelidir](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) oturum açma uç noktalarında CORS 'yi etkinleştirmek için.
* URI 'Leri yeniden yönlendirmek için yetkilendirme kodu akışı aracılığıyla verilen belirteçleri yenileme, `spa` 90 günlük ömür yerine 24 saatlik bir yaşam süresine sahiptir.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Tek sayfalı bir uygulama ve güvenlik belirteci hizmeti uç noktası arasındaki OAuth 2 yetkilendirme kodu akışını gösteren diyagram." border="false":::

## <a name="performance-and-ux-implications"></a>Performans ve UX etkileri

Örtük akışı kullanan bazı uygulamalar, kullanarak oturum açma iframe 'i açarak oturum açmayı dener `prompt=none` . Çoğu tarayıcıda, bu istek şu anda oturum açmış olan kullanıcının belirteçleri ile yanıt verir (izin verilen onay varsayılırsa). Bu model, Kullanıcı oturumunu açmak için bir tam sayfa yönlendirmesi gerektirmez, performans ve Kullanıcı deneyimini geliştirir; Kullanıcı Web sayfasını ziyaret ediyor ve zaten oturum açtı. `prompt=none`Bir IFRAME 'de, üçüncü taraf tanımlama bilgileri engellendiğinde bir seçenek olmadığından, uygulamalar, bir yetkilendirme kodu verilen bir üst düzey çerçevede oturum açma sayfasını ziyaret etmelidir.

Oturum açmayı gerçekleştirmenin iki yolu vardır:

* **Tam sayfa yeniden yönlendirmeleri**
    * SPA 'nın ilk yükünde, oturum yoksa (veya oturumun kullanım tarihi dolmuşsa) kullanıcıyı oturum açma sayfasına yönlendirin. Kullanıcının tarayıcısı, oturum açma sayfasını ziyaret eder, Kullanıcı oturumunu içeren tanımlama bilgilerini sunar ve sonra bir parçadaki kod ve belirteçlerle uygulamaya yeniden yönlendirilir.
    * Yeniden yönlendirme, SPA 'nın iki kez yüklenmekte neden olacak. Uygulamanın tam olarak iki kez indirilmemesi için, maça 'Ları önbelleğe almak için en iyi uygulamaları izleyin.
    * Uygulamada, oturum açma oturumunu denetleyen ve uygulama ve JavaScript yükünü yürütmeden önce oturum açma sayfasına yönlendiren bir ön yükleme sırası kullanmayı düşünün.
* **'Ları**
    * Tam sayfa yeniden yönlendirmenin Kullanıcı deneyimi (UX) uygulama için işe yaramazsa, kimlik doğrulamasını işlemek için bir açılan pencere kullanmayı düşünün.
    * Açılan pencere, kimlik doğrulamasından sonra uygulamaya yönlendirmeyi bitirdiğinde, yeniden yönlendirme işleyicisindeki kod, uygulamanın kullanması için kodu ve belirteçleri yerel depoda depolar. MSAL.js, çoğu kitaplığı da olduğu gibi kimlik doğrulaması için açılır pencereleri destekler.
    * Tarayıcılar, açılan pencereler için destek azalttıkları için en güvenilir seçenek olmayabilir. Açılan pencereyi oluşturmadan önce SPA ile Kullanıcı etkileşimi, tarayıcı gereksinimlerini karşılamak için gerekli olabilir.

>[!NOTE]
> Apple, özgün pencereye üçüncü taraf tanımlama bilgilerine erişim vermek için geçici bir uyumluluk düzeltmesinin bulunduğu bir [açılır yöntemi açıklar](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) . Apple bu izinlerin daha sonra bu sırada kaldırılmasına rağmen buradaki Kılavuzu etkilemez. Burada açılan pencere, oturum açma sayfasına, bir oturumun bulunması ve bir kimlik doğrulama kodu sağlanabilmesi için ilk taraf gezinmesi olarak kullanılmaktadır. Bu, gelecekte çalışmaya devam etmelidir.

### <a name="a-note-on-iframe-apps"></a>İframe uygulamalarında bir notun

Web Apps 'teki ortak bir model, bir uygulamayı başka bir uygulama içine eklemek için iframe kullanmaktır. Üst düzey çerçeve, kullanıcının kimliğini doğrulamayı işler ve iframe 'de barındırılan uygulama, kullanıcının oturum açmış olduğunu ve örtülü akış kullanılarak belirteçleri sessizce getirmeyi sağlayabilir. Sessiz belirteç alımı, üçüncü taraf tanımlama bilgileri engellendiğinde artık çalışmaz. iframe 'e katıştırılmış uygulama, oturum açma sayfasına gidemediği için açılan pencere, kullanıcı oturumuna erişmek üzere açılır pencere kullanılarak geçiş yapılmalıdır.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Tarayıcıda belirteçleri yenileme güvenlik etkileri

Tarayıcıya yenileme belirteçleri vermek bir güvenlik sorunu olarak kabul edilir. Siteler arası betik yazma (XSS) saldırıları veya güvenliği aşılmış JS paketleri yenileme belirtecini çalabilir ve süresi dolana veya iptal edilene kadar uzaktan kullanabilir. Çalınan yenileme belirteçlerinin riskini en aza indirmek için, maça 'Lar yalnızca 24 saat için geçerli olan belirteçleri verir. 24 saat sonra, uygulamanın, üst düzey bir çerçeve aracılığıyla oturum açma sayfasına ziyaret eden yeni bir yetkilendirme kodu edinmesi gerekir.

Bu sınırlı ömür boyu yenileme belirteci, güvenlik ve düşürülmüş UX arasında bir denge olarak seçildi. Belirteçleri veya üçüncü taraf tanımlama bilgilerini yenilemeksizin, yeni veya ek belirteçler gerektiğinde yetkilendirme kodu akışı ( [OAuth güvenlik en iyi geçerli uygulamalar taslağı](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)tarafından önerildiği gibi) onerou olur. Her bir belirtecin süresi dolduğunda (genellikle Microsoft Identity platform belirteçleri için), her bir belirteç için tam sayfa yönlendirmesi veya açılan pencere gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[Yetkilendirme kodu akışı](v2-oauth2-auth-code-flow.md)hakkında daha fazla bilgi edinin.

[MSAL.js 2,0 hızlı](quickstart-v2-javascript-auth-code.md)başlangıcı ile yetkilendirme kodu akışını deneyin.
