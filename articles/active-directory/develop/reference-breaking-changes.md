---
title: Azure Active Directory Son değişiklik başvurusu
description: Uygulamanızı etkileyebilecek Azure AD protokollerinde yapılan değişiklikler hakkında bilgi edinin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 3/30/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: eb75450527fc31d6ea4a9f9d60d676718ad79bda
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167592"
---
# <a name="whats-new-for-authentication"></a>Kimlik doğrulaması yenilikleri nelerdir?

> Bu URL 'YI RSS Akış okuyucunuzun içine yapıştırarak bu sayfada yapılan güncelleştirmeleri öğrenin:<br/>`https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us`

Kimlik doğrulama sistemi değiştirir ve güvenlik ve standartlar uyumluluğunu artırmak için sürekli olarak özellikler ekler. En son gelişmelerden haberdar olmak için, bu makalede aşağıdaki Ayrıntılar hakkında bilgi verilmektedir:

- En yeni özellikler
- Bilinen sorunlar
- Protokol değişiklikleri
- Kullanım dışı işlevsellik

> [!TIP]
> Bu sayfa düzenli olarak güncelleştirildiğinden sık ziyaret edin. Aksi belirtilmedikçe, bu değişiklikler yalnızca yeni kayıtlı uygulamalar için konur.

## <a name="upcoming-changes"></a>Yaklaşan değişiklikler

### <a name="bug-fix-azure-ad-will-no-longer-url-encode-the-state-parameter-twice"></a>Hata onarımı: Azure AD artık durum parametresini iki kez kodlayamayacak.

**Geçerlilik tarihi**: Mayıs 2021

**Etkilenen uç noktalar**: v 1.0 ve v 2.0 

**Etkilenen protokol**: uç noktayı ziyaret eden tüm akışlar `/authorize` (örtük akış ve yetkilendirme kodu akışı)

Azure AD yetkilendirme yanıtında bir hata bulundu ve düzeltildi. `/authorize`Kimlik doğrulamanın BAI sırasında, `state` uygulama durumunu korumak ve CSRF saldırılarını önlemeye yardımcı olmak için istekten alınan parametre yanıta dahil edilir. Azure AD yanlış URL, `state` bir kez daha sonra kodlandığından yanıta eklemeden önce parametreyi kodladı.  Bu, uygulamaların Azure AD 'den yanlışlıkla yanıt reddetmesine neden olur. 

Azure AD bu parametreyi artık çift kodlamaz ve uygulamaların sonucu doğru bir şekilde ayrıştırmasına izin verir. Bu değişiklik tüm uygulamalar için yapılır. 

### <a name="conditional-access-will-only-trigger-for-explicitly-requested-scopes"></a>Koşullu erişim yalnızca açıkça istenen kapsamlar için tetiklenecek

**Geçerlilik tarihi**: Mayıs 2021, Nisan 'dan itibaren aşamalı dağıtım ile. 

**Etkilenen uç noktalar**: v 2.0

**Etkilenen protokol**: [dinamik onay](v2-permissions-and-consent.md#requesting-individual-user-consent) kullanan tüm akışlar

Bugün dinamik izin kullanan uygulamalara,, parametre adına göre istenmese de, izin verilen tüm izinler verilir `scope` .  Bu, bir uygulamanın yalnızca bir `user.read` için istekte bulunmasına neden olabilir, ancak onayı ile `files.read` Izin atanmış koşullu erişimi geçirmeye zorlanır `files.read` . 

Gereksiz koşullu erişim istemlerinin sayısını azaltmak için, Azure AD, istenmeyen kapsamların uygulamalara nasıl sağlandığını, böylece yalnızca açıkça istenen kapsamların koşullu erişimi tetiklemesine olanak sağlar. Bu değişiklik, talep ettikleri belirteçlerin eksik olması nedeniyle, Azure AD 'nin önceki davranışına bağlı olan uygulamaların (istenmeseler bile, tüm izinlerin kesilmesine izin veriyor) kesintiye neden olabilir.

Uygulamalar artık bu istek içinde bir izin karışımına sahip olan erişim belirteçlerini ve bu istekler için koşullu erişim istemleri gerektirmeyen onay sahibi olduklarını alacak.  Erişim belirtecinin kapsamları belirteç yanıtının `scope` parametresine yansıtılır. 

Bu değişiklik, bu davranış üzerinde gözlemlenmeyen bir bağımlılığı hariç tüm uygulamalar için yapılır.  Geliştiriciler, ek koşullu erişim istemlerine bir bağımlılığı olabileceği için, bu değişiklikten muaf tutulduklarında, erişim elde edilir. 

**Örnekler**

Bir uygulama,, ve için onay içerir `user.read` `files.readwrite` `tasks.read` . `files.readwrite` Koşullu erişim ilkelerinin uygulanmış olması, diğer ikisi de değildir. Bir uygulama için bir belirteç isteği yapıyorsa `scope=user.read` ve şu anda oturum açmış olan Kullanıcı herhangi bir koşullu erişim ilkesi geçirmemişse, elde edilen belirteç `user.read` ve izinleri için olur `tasks.read` . `tasks.read` , uygulamanın kendisine izin vermiş olması ve bir koşullu erişim ilkesinin zorlanmasını gerektirmediği için dahil edilmiştir. 

Uygulama daha sonra istediğinde `scope=files.readwrite` , kiracı tarafından gereken koşullu erişim tetiklenecek ve uygulamayı koşullu erişim ilkesinin karşılanbileceği etkileşimli bir kimlik doğrulama istemi göstermeye zorluyor.  Döndürülen belirtecin içinde üç kapsam olacaktır. 

Daha sonra uygulama üç kapsamın herhangi biri için son bir istek yapıyorsa (deyin `scope=tasks.read` ), Azure AD kullanıcının için gereken koşullu erişim ilkelerini zaten tamamladığını `files.readwrite` ve bu durumda üç izinle daha sonra bir belirteç vermesini görür. 


## <a name="may-2020"></a>Mayıs 2020

### <a name="azure-government-endpoints-are-changing"></a>Azure Kamu uç noktaları değişiyor

**Geçerlilik tarihi**: Mayıs 5 mayıs (2020 Haziran tarihinde bitiriliyor) 

**Etkilenen uç noktalar**: tümü

**Etkilenen protokol**: tüm akışlar

1 Haziran 2018 ' de, Azure Kamu için resmi Azure Active Directory (AAD) yetkilisi ' den ' a değiştirilir `https://login-us.microsoftonline.com` `https://login.microsoftonline.us` . Bu değişiklik Ayrıca, Azure Kamu AAD 'nin de Hizmetleri olan GCC High ve DoD Microsoft 365 de uygulanır. ABD kamu kiracısında bir uygulamaya sahipseniz, son noktadaki kullanıcıları oturum açmak için uygulamanızı güncelleştirmeniz gerekir `.us` .  

Azure AD, Mayıs 5 ' ten itibaren bitiş noktası değişikliğini zorunlu hale getirmeye başlayacak ve kamu kullanıcılarının ortak uç nokta () kullanılarak ABD kamu kiracılarında barındırılan uygulamalarda oturum açmasını önlüyor `microsoftonline.com` .  Etkilenen uygulamalar bir hata görmeye başlayacaktır `AADSTS900439`  -  `USGClientNotSupportedOnPublicEndpoint` . Bu hata, uygulamanın genel bulut uç noktasında bir ABD kamu kullanıcısına oturum açmaya çalışıyor olduğunu gösterir. Uygulamanız ortak bir bulut kiracısında ise ve ABD hükümeti kullanıcılarını desteklemek istiyorsanız, [uygulamanızı açıkça destekleyecek şekilde güncelleştirmeniz](./authentication-national-cloud.md)gerekir. Bu, ABD kamu bulutunda yeni bir uygulama kaydı oluşturulmasını gerektirebilir. 

Bu değişikliğin uygulanması, ABD kamu bulutundaki kullanıcıların uygulamada oturum açma ve ABD devlet kullanıcılarına yönelik uygulamalar için ne sıklıkta oturum açması gerektiğini temel alan aşamalı bir dağıtım kullanılarak yapılır ve ABD hükümeti kullanıcıları tarafından sık kullanılan uygulamalar en son zorlama için geçerlidir. Uygulama, Haziran 2020 ' deki tüm uygulamalarda tamamlanmasını bekliyor. 

Daha ayrıntılı bilgi için lütfen [bu geçişte Azure Kamu blog gönderisine](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)bakın. 

## <a name="march-2020"></a>Mart 2020

### <a name="user-passwords-will-be-restricted-to-256-characters"></a>Kullanıcı parolaları 256 karakterle sınırlandırılır.

**Geçerlilik tarihi**: 13 Mart 2020

**Etkilenen uç noktalar**: tümü

**Etkilenen protokol**: tüm Kullanıcı akışları.

Doğrudan Azure AD 'de oturum açmak için 256 karakterden daha uzun parolalara sahip kullanıcılar (ADFS gibi bir federe ıDP 'nin aksine), 13 Mart 2020 tarihinden itibaren oturum açamıyor ve bunun yerine parolalarını sıfırlayacağız.  Yöneticiler, kullanıcıların parolasını sıfırlamasına yardımcı olan istekler alabilir.

Oturum açma günlüklerinde hata AADSTS 50052: ınvalidpasswordexceedsmaxlength olur

İleti: `The password entered exceeds the maximum length of 256. Please reach out to your admin to reset the password.`

Düzeltmesi

Parolası izin verilen en fazla uzunluğu aştığından Kullanıcı oturum açamıyor. Parolayı sıfırlamak için yöneticlerinden iletişim kurabilmesi gerekir. Bu kiracı için SSPR etkinleştirilmişse, "parolanızı unuttum" bağlantısını izleyerek parolalarını sıfırlayabilirler.



## <a name="february-2020"></a>Şubat 2020

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>Boş parçalar, oturum açma uç noktasındaki her HTTP yeniden yönlendirmesine eklenecektir.

**Geçerlilik tarihi**: 8 Şubat 2020

**Etkilenen uç noktalar**: v 1.0 ve v 2.0

**Etkilenen protokol**: response_type = Query kullanan OAUTH ve OIDC akışları-bu, bazı durumlarda [yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md) ve [dolaylı akışı](v2-oauth2-implicit-grant-flow.md)ele alır.

HTTP yeniden yönlendirme aracılığıyla login.microsoftonline.com 'den bir uygulamaya bir kimlik doğrulama yanıtı gönderildiğinde hizmet, yanıt URL 'sine boş bir parça ekler.  Bu, tarayıcının kimlik doğrulama isteğindeki var olan herhangi bir parçayı temizler olarak yeniden yönlendirme saldırılarını önler.  Hiçbir uygulamanın bu davranışa bağımlılığı olmamalıdır.


## <a name="august-2019"></a>Ağustos 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>GÖNDERI formu semantiği daha kesin boşluk sağlayacak ve tırnak işaretleri yoksayılacak

**Geçerlilik tarihi**: 2 Eylül 2019

**Etkilenen uç noktalar**: v 1.0 ve v 2.0

**Protokol etkilendi**: her yerden gönderi kullanılır ([istemci kimlik bilgileri](./v2-oauth2-client-creds-grant-flow.md), [yetkilendirme kodu satın](./v2-oauth2-auth-code-flow.md)alma, [ropc](./v2-oauth-ropc.md), [OBO](./v2-oauth2-on-behalf-of-flow.md)ve [yenileme belirteci yeniden satın](./v2-oauth2-auth-code-flow.md#refresh-the-access-token)alma)

9/2 haftası başlatılırken POST yöntemini kullanan kimlik doğrulama istekleri, daha sıkı HTTP standartları kullanılarak doğrulanacak.  Özellikle, boşluklar ve çift tırnak işaretleri (") artık istek formu değerlerinden kaldırılmayacak. Bu değişikliklerin mevcut istemcileri bozmak için beklenmez ve Azure AD 'ye gönderilen isteklerin her seferinde güvenilir bir şekilde işlenmesini sağlar. Gelecekte (bkz. Yukarıdaki) yinelenen parametreleri reddetmeyi ve isteklerin içindeki ürün reçetesini yoksaymayı planlıyoruz.

Örnek:

Bugün, `?e=    "f"&g=h` ile aynı şekilde ayrıştırılır `?e=f&g=h` `e`  ==  `f` .  Bu değişiklik ile, bu, `e`  ==  `    "f"` geçerli bir bağımsız değişken olması olası değildir ve istek şimdi başarısız olur.


## <a name="july-2019"></a>Temmuz 2019

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Yalnızca tek kiracılı uygulamalar için yalnızca uygulama belirteçleri, kaynak kiracısında istemci uygulaması varsa verilir

**Geçerlilik tarihi**: 26 Temmuz 2019

**Etkilenen uç noktalar**: [v 1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) ve [v 2.0](./v2-oauth2-client-creds-grant-flow.md)

**Etkilenen protokol**: [istemci kimlik bilgileri (yalnızca uygulama belirteçleri)](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)

Bir güvenlik değişikliği, yalnızca uygulama belirteçlerinin (istemci kimlik bilgileri verme aracılığıyla) verildiği şekilde değişiklik gösteren 26 Temmuz 'dan bir sorun oluştu. Daha önce, bu uygulama için, kiracının veya rollerdeki varlığına bakılmaksızın, uygulamaların başka herhangi bir uygulamayı çağırmak için belirteçleri almaya izin verildi.  Bu davranış, kaynaklar (bazen Web API 'Leri olarak adlandırılır) tek kiracılı (varsayılan) olarak ayarlanmış şekilde güncelleştirildiğinden, istemci uygulamasının kaynak kiracısında mevcut olması gerekir.  İstemci ile API arasındaki mevcut onay hala gerekli değildir ve uygulamalar, bir `roles` talebin mevcut olduğundan ve API için beklenen değeri içerdiğinden emin olmak için kendi yetkilendirme denetimlerini yapmaya devam etmelidir.

Bu senaryonun hata iletisi şu anda şu durumlar için:

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Bu sorunu çözmek için, kiracınızda istemci uygulama hizmeti sorumlusunu oluşturmak için yönetici onayı deneyimini kullanın veya el ile oluşturun.  Bu gereksinim, kiracının kiracı içinde çalışmaya yönelik uygulama iznini vermiş olmasını sağlar.

#### <a name="example-request"></a>Örnek istek

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` Bu örnekte, kaynak kiracısı (yetkili) contoso.com, kaynak uygulaması contoso kiracısı için çağrılan tek kiracılı bir uygulamadır `gateway.contoso.com/api` ve istemci uygulaması olur `14c88eee-b3e2-4bb0-9233-f5e3053b3a28` .  İstemci uygulamasının Contoso.com içinde bir hizmet sorumlusu varsa, bu istek devam edebilir.  Ancak yoksa, istek yukarıdaki hata ile başarısız olur.

Contoso Gateway uygulaması çok kiracılı bir uygulamatı, ancak, Contoso.com içinde hizmet sorumlusu olan istemci uygulamasına bakılmaksızın istek devam eder.

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Yeniden yönlendirme URI 'Leri artık sorgu dizesi parametreleri içerebilir

**Geçerlilik tarihi**: 22 Temmuz 2019

**Etkilenen uç noktalar**: v 1.0 ve v 2.0

**Etkilenen protokol**: tüm akışlar

[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)BAŞıNA Azure AD uygulamaları artık `https://contoso.com/oauth2?idp=microsoft` OAuth 2,0 istekleri için statik sorgu parametreleriyle (gibi) yeniden yönlendirme (yanıtlama) URI 'leri kaydedebilir ve kullanabilir.  Dinamik yeniden yönlendirme URI 'Leri, bir güvenlik riskini temsil ettiğinden hala yasaktır ve bu, bir kimlik doğrulama isteği genelinde durum bilgilerini sürdürmek için kullanılamaz; bunun için `state` parametresini kullanın.

Statik sorgu parametresi, yeniden yönlendirme URI 'sinin diğer herhangi bir bölümü gibi yeniden yönlendirme URI 'Leri için dize eşlemeye tabidir. URI kodu çözülen redirect_uri eşleşen bir dize yoksa istek reddedilir.  URI uygulama kaydında bulunursa, statik sorgu parametresi dahil olmak üzere, kullanıcının yeniden yönlendirileceği dizenin tamamı kullanılacaktır.

Şu anda (2019 Temmuz 'ın sonunda), uygulama kayıt UX Azure portal hala sorgu parametrelerini engellediğine göz önüne alın.  Bununla birlikte, sorgu parametreleri eklemek ve bunu uygulamanızda test etmek için uygulama bildirimini el ile düzenleyebilirsiniz.


## <a name="march-2019"></a>Mart 2019

### <a name="looping-clients-will-be-interrupted"></a>Döngü istemcileri kesintiye uğratılacaktır

**Geçerlilik tarihi**: 25 Mart 2019

**Etkilenen uç noktalar**: v 1.0 ve v 2.0

**Etkilenen protokol**: tüm akışlar

İstemci uygulamaları bazen, kısa bir süre boyunca aynı oturum açma isteği için yüzlerce şekilde davranabilir.  Bu istekler başarılı olabilir veya başarılı olmayabilir, ancak tüm kullanıcılar için zayıf Kullanıcı deneyimine ve tüm kullanıcılar için gecikme süresini artırarak ve ıDP kullanılabilirliğini azaltarak tüm kullanıcılar için zayıf Kullanıcı deneyimine katkıda bulunur.  Bu uygulamalar normal kullanım sınırları dışında çalışır ve doğru şekilde davranması için güncelleştirilmeleri gerekir.

Yinelenen istekleri birden çok kez veren istemcilere bir `invalid_grant` hata gönderilir: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` .

Çoğu istemcinin bu hatayı önlemek için davranışları değiştirmesi gerekmez.  Yalnızca yanlış yapılandırılmış istemciler (belirteç önbelleği olmayan veya bu komut istemi döngülerine sahip olanlar), bu hatadan etkilenecek.  İstemciler, aşağıdaki etkenlere göre örnek temelinde yerel olarak (tanımlama bilgisi aracılığıyla) izlenir:

* Varsa, Kullanıcı İpucu

* İstenen kapsamlar veya kaynaklar

* İstemci Kimliği

* Yeniden Yönlendirme URI'si

* Yanıt türü ve modu

Kısa bir süre içinde (5 dakika) birden çok istek (15 +) yapan uygulamalar, döngü olduğunu belirten bir `invalid_grant` hata alacaktır.  İstenen belirteçlerin süresi yeterince uzun süreli yaşam süresine sahiptir (varsayılan olarak 10 dakika, 60 dakika), bu nedenle bu süre içindeki yinelenen isteklerin gereksiz olması gereksizdir.

Tüm uygulamalar `invalid_grant` sessizce bir belirteç istemek yerine etkileşimli bir istem göstererek işlemelidir.  Bu hatadan kaçınmak için istemciler aldıkları belirteçleri doğru önbelleğe almadıklarından emin olmalıdır.


## <a name="october-2018"></a>Ekim 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Yetkilendirme kodları artık yeniden kullanılamayacak

**Geçerlilik tarihi**: 15 Kasım 2018

**Etkilenen uç noktalar**: v 1.0 ve v 2.0

**Protokol etkilendi**: [kod akışı](v2-oauth2-auth-code-flow.md)

15 Kasım 2018 tarihinden itibaren Azure AD, uygulamalar için önceden kullanılan kimlik doğrulama kodlarını kabul etmeyi durduracak. Bu güvenlik değişikliği, Azure AD 'yi OAuth belirtimine göre satıra getirmeye yardımcı olur ve hem v1 hem de v2 uç noktalarında zorunlu kılınır.

Uygulamanız birden fazla kaynağa belirteç almak için yetkilendirme kodlarını yeniden kullanıyorsa, bir yenileme belirteci almak için kodu kullanmanızı ve ardından bu yenileme belirtecini kullanarak diğer kaynaklar için ek belirteçler elde etmenizi öneririz. Yetkilendirme kodları yalnızca bir kez kullanılabilir, ancak yenileme belirteçleri birden çok kaynak arasında birden çok kez kullanılabilir. OAuth kod akışı sırasında bir kimlik doğrulama kodunu yeniden kullanmayı deneyen tüm yeni uygulamalar invalid_grant bir hata alır.

Belirteçleri yenileme hakkında daha fazla bilgi için bkz. [erişim belirteçlerini yenileme](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  ADAL veya MSAL kullanılıyorsa, bu işlem sizin için oluşturulur-' AcquireTokenByAuthorizationCodeAsync ' öğesinin ikinci örneğini ' AcquireTokenSilentAsync ' ile değiştirin.

## <a name="may-2018"></a>Mayıs 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>OBO akışı için KIMLIK belirteçleri kullanılamaz

**Tarih**: Mayıs 1, 2018

**Etkilenen uç noktalar**: v 1.0 ve v 2.0

**Etkilenen protokoller**: örtük akış ve [Şirket adına akış](v2-oauth2-on-behalf-of-flow.md)

1 Mayıs 2018 ' den sonra, yeni uygulamalar için bir OBO akışında onaylama olarak id_tokens kullanılamaz. API 'Lerin güvenliğini sağlamak için, aynı uygulamanın bir istemcisi ve orta katmanı arasında bile erişim belirteçleri kullanılmalıdır. 1 Mayıs 2018 tarihinden önce kaydolan uygulamalar çalışmaya devam eder ve erişim belirteci için id_tokens değiş tokuş edebilir. Ancak, bu model en iyi yöntem olarak kabul edilmez.

Bu değişikliği geçici olarak çözmek için şunları yapabilirsiniz:

1. Uygulamanız için bir veya daha fazla kapsamı olan bir Web API 'SI oluşturun. Bu açık giriş noktası, daha ayrıntılı denetim ve güvenliğe izin verir.
1. Uygulamanızın bildiriminde, [Azure Portal](https://portal.azure.com) veya [uygulama kayıt portalı](https://apps.dev.microsoft.com)' nda, uygulamanın örtük akış aracılığıyla erişim belirteçleri vermesine izin verildiğinden emin olun. Bu anahtar aracılığıyla denetlenir `oauth2AllowImplicitFlow` .
1. İstemci uygulamanız aracılığıyla bir id_token istediğinde `response_type=id_token` , `response_type=token` yukarıda oluşturulan Web API 'si için de bir erişim belirteci () isteyin. Bu nedenle, v 2.0 uç noktası kullanılırken `scope` parametresi şuna benzemelidir `api://GUID/SCOPE` . V 1.0 uç noktasında `resource` parametresi, Web API 'sinin uygulama URI 'si olmalıdır.
1. Bu erişim belirtecini id_token yerine Orta katmana geçirin.
