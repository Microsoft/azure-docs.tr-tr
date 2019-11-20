---
title: Azure Active Directory son değişiklikler başvurusu | Microsoft Docs
description: Uygulamanızı etkileyebilecek Azure AD protokollerinde yapılan değişiklikler hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd50aa00368469a9c5b42c41826da28566268d4
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125428"
---
# <a name="whats-new-for-authentication"></a>Kimlik doğrulaması yenilikleri nelerdir? 

>Bu sayfadaki güncelleştirmeler hakkında bildirim alın. [Bu URL 'YI](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) RSS Akış okuyucunuzun altına eklemeniz yeterlidir.

Kimlik doğrulama sistemi değiştirir ve güvenlik ve standartlar uyumluluğunu artırmak için sürekli olarak özellikler ekler. En son gelişmelerden haberdar olmak için, bu makalede aşağıdaki Ayrıntılar hakkında bilgi verilmektedir:

- En yeni özellikler
- Bilinen sorunlar
- Protokol değişiklikleri
- Kullanım dışı işlev

> [!TIP] 
> Bu sayfa düzenli olarak güncelleştirildiğinden sık ziyaret edin. Aksi belirtilmedikçe, bu değişiklikler yalnızca yeni kayıtlı uygulamalar için konur.  

## <a name="upcoming-changes"></a>Yaklaşan değişiklikler

Eylül 2019: URL ayrıştırma kurallarına göre daha fazla POST semantiği zorlaması-yinelenen parametreler bir hata tetikler ve [bom](https://www.w3.org/International/questions/qa-byte-order-mark) yok sayılır.

## <a name="august-2019"></a>Ağustos 2019

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>GÖNDERI formu semantiği daha kesin boşluk sağlayacak ve tırnak işaretleri yoksayılacak

**Geçerlilik tarihi**: 2 Eylül 2019

**Etkilenen uç noktalar**: V 1.0 ve v 2.0

**Protokol etkilendi**: Her yerden GÖNDERI kullanılır ([istemci kimlik bilgileri](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), [yetkilendirme kodu satın](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)alma, [ropc](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [OBO](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)ve [yenileme belirteci satın](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)alma)

9/2 haftası başlatılırken POST yöntemini kullanan kimlik doğrulama istekleri, daha sıkı HTTP standartları kullanılarak doğrulanacak.  Özellikle, boşluklar ve çift tırnak işaretleri (") artık istek formu değerlerinden kaldırılmayacak. Bu değişikliklerin mevcut istemcileri bozmak için beklenmez ve Azure AD 'ye gönderilen isteklerin her seferinde güvenilir bir şekilde işlenmesini sağlar. Gelecekte (bkz. Yukarıdaki) yinelenen parametreleri reddetmeyi ve isteklerin içindeki ürün reçetesini yoksaymayı planlıyoruz. 

Örnek:

Bugün, `?e=    "f"&g=h` `?e=f&g=h` ile aynı şekilde `e`  == ayrıştırılır. `f`  Bu değişiklik ile, bu, `e`  ==  `    "f"` geçerli bir bağımsız değişken olması olası değildir ve istek şimdi başarısız olur. 


## <a name="july-2019"></a>2019 Temmuz

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>Yalnızca tek kiracılı uygulamalar için yalnızca uygulama belirteçleri, kaynak kiracısında istemci uygulaması varsa verilir

**Geçerlilik tarihi**: 26 Temmuz 2019

**Etkilenen uç noktalar**: [V 1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) ve [v 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)

**Protokol etkilendi**: [İstemci kimlik bilgileri (yalnızca uygulama belirteçleri)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

Bir güvenlik değişikliği, yalnızca uygulama belirteçlerinin (istemci kimlik bilgileri verme aracılığıyla) verildiği şekilde değişiklik gösteren 26 Temmuz 'dan bir sorun oluştu. Daha önce, bu uygulama için, kiracının veya rollerdeki varlığına bakılmaksızın, uygulamaların başka herhangi bir uygulamayı çağırmak için belirteçleri almaya izin verildi.  Bu davranış, kaynaklar (bazen Web API 'Leri olarak adlandırılır) tek kiracılı (varsayılan) olarak ayarlanmış şekilde güncelleştirildiğinden, istemci uygulamasının kaynak kiracısında mevcut olması gerekir.  İstemci ile API arasındaki mevcut onay hala gerekli değildir ve uygulamalar, bir `roles` talebin mevcut olduğundan ve API için beklenen değeri içerdiğinden emin olmak için kendi yetkilendirme denetimlerini yapmaya devam etmelidir.

Bu senaryonun hata iletisi şu anda şu durumlar için: 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

Bu sorunu çözmek için, kiracınızda istemci uygulama hizmeti sorumlusunu oluşturmak için yönetici onayı deneyimini kullanın veya el ile oluşturun.  Bu gereksinim, kiracının kiracı içinde çalışmaya yönelik uygulama iznini vermiş olmasını sağlar.  

#### <a name="example-request"></a>Örnek istek

`https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...`Bu örnekte, kaynak kiracısı (yetkili) contoso.com, kaynak uygulaması contoso kiracısı için çağrılan `gateway.contoso.com/api` tek kiracılı bir uygulamadır ve istemci uygulaması olur. `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`  İstemci uygulamasının Contoso.com içinde bir hizmet sorumlusu varsa, bu istek devam edebilir.  Ancak yoksa, istek yukarıdaki hata ile başarısız olur.  

Contoso Gateway uygulaması çok kiracılı bir uygulamatı, ancak, Contoso.com içinde hizmet sorumlusu olan istemci uygulamasına bakılmaksızın istek devam eder.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>Yeniden yönlendirme URI 'Leri artık sorgu dizesi parametreleri içerebilir

**Geçerlilik tarihi**: 22 Temmuz 2019

**Etkilenen uç noktalar**: V 1.0 ve v 2.0

**Protokol etkilendi**: Tüm akışlar

[RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)başına Azure AD uygulamaları artık statik sorgu parametreleriyle ( https://contoso.com/oauth2?idp=microsoft) Örneğin, OAuth 2,0 istekleri için) yeniden yönlendirme (yanıtlama) URI 'leri kaydedebilir ve kullanabilir.  Dinamik yeniden yönlendirme URI 'leri, bir güvenlik riskini temsil ettiğinden hala yasaktır ve bu, bir kimlik doğrulama isteği genelinde durum bilgilerini sürdürmek için kullanılamaz; bunun için `state` parametresini kullanın.

Statik sorgu parametresi, yeniden yönlendirme URI 'sinin diğer herhangi bir bölümü gibi yeniden yönlendirme URI 'Leri için dize eşlemeye tabidir. URI kodu çözülen redirect_uri ile eşleşen hiçbir dize kaydedilmemişse, istek reddedilir.  URI uygulama kaydında bulunursa, statik sorgu parametresi dahil olmak üzere, kullanıcının yeniden yönlendirileceği dizenin tamamı kullanılacaktır. 

Şu anda (2019 Temmuz 'ın sonunda), uygulama kayıt UX Azure portal hala sorgu parametrelerini engellediğine göz önüne alın.  Bununla birlikte, sorgu parametreleri eklemek ve bunu uygulamanızda test etmek için uygulama bildirimini el ile düzenleyebilirsiniz.  


## <a name="march-2019"></a>Mart 2019

### <a name="looping-clients-will-be-interrupted"></a>Döngü istemcileri kesintiye uğratılacaktır

**Geçerlilik tarihi**: 25 Mart 2019

**Etkilenen uç noktalar**: V 1.0 ve v 2.0

**Protokol etkilendi**: Tüm akışlar

İstemci uygulamaları bazen, kısa bir süre boyunca aynı oturum açma isteği için yüzlerce şekilde davranabilir.  Bu istekler başarılı olabilir veya başarılı olmayabilir, ancak tüm kullanıcılar için zayıf Kullanıcı deneyimine ve tüm kullanıcılar için gecikme süresini artırarak ve ıDP kullanılabilirliğini azaltarak tüm kullanıcılar için zayıf Kullanıcı deneyimine katkıda bulunur.  Bu uygulamalar normal kullanım sınırları dışında çalışır ve doğru şekilde davranması için güncelleştirilmeleri gerekir.  

Yinelenen istekleri birden çok kez veren istemcilere bir `invalid_grant` hata gönderilir:. `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 

Çoğu istemcinin bu hatayı önlemek için davranışları değiştirmesi gerekmez.  Yalnızca yanlış yapılandırılmış istemciler (belirteç önbelleği olmayan veya bu komut istemi döngülerine sahip olanlar), bu hatadan etkilenecek.  İstemciler, aşağıdaki etkenlere göre örnek temelinde yerel olarak (tanımlama bilgisi aracılığıyla) izlenir:

* Varsa, Kullanıcı İpucu

* İstenen kapsamlar veya kaynaklar

* İstemci Kimliği

* Yönlendirme URI'si

* Yanıt türü ve modu

Kısa bir süre içinde (5 dakika) birden çok istek (15 +) yapan uygulamalar, döngü olduğunu belirten `invalid_grant` bir hata alacaktır.  İstenen belirteçlerin süresi yeterince uzun süreli yaşam süresine sahiptir (varsayılan olarak 10 dakika, 60 dakika), bu nedenle bu süre içindeki yinelenen isteklerin gereksiz olması gereksizdir.  

Tüm uygulamalar sessizce bir `invalid_grant` belirteç istemek yerine etkileşimli bir istem göstererek işlemelidir.  Bu hatadan kaçınmak için istemciler aldıkları belirteçleri doğru önbelleğe almadıklarından emin olmalıdır.


## <a name="october-2018"></a>Ekim 2018

### <a name="authorization-codes-can-no-longer-be-reused"></a>Yetkilendirme kodları artık yeniden kullanılamayacak

**Geçerlilik tarihi**: 15 Kasım 2018

**Etkilenen uç noktalar**: V 1.0 ve v 2.0

**Protokol etkilendi**: [Kod akışı](v2-oauth2-auth-code-flow.md)

15 Kasım 2018'de başlayarak, Azure AD uygulamaları için daha önce kullanılan kimlik doğrulama kodlarını kabul durdurur. Bu güvenlik değişiklik v1 ve v2 Uç noktalara zorlanmasını sağlar ve Azure AD OAuth belirtimi ayarlarına uygun olarak çıkarmak yardımcı olur.

Uygulamanız için birden fazla kaynak belirteçlerini almak için yetkilendirme kodları yeniden kullanır, bir yenileme belirteci almak için kodu kullanın ve ardından diğer kaynaklar için ek belirteçlerini almak için yenileme belirtecini kullanmak öneririz. Yetkilendirme kodları yalnızca bir kez kullanılabilir, ancak yenileme belirteçleri birden fazla kaynak arasında birden çok kez kullanılabilir. OAuth kod akışı sırasında bir kimlik doğrulama kodunu yeniden kullanmayı deneyen tüm yeni uygulamalar bir invalid_grant hatası alır.

Belirteçleri yenileme hakkında daha fazla bilgi için bkz. [erişim belirteçlerini yenileme](v1-protocols-oauth-code.md#refreshing-the-access-tokens).  ADAL veya MSAL kullanılıyorsa, bu işlem sizin için oluşturulur-' AcquireTokenByAuthorizationCodeAsync ' öğesinin ikinci örneğini ' AcquireTokenSilentAsync ' ile değiştirin. 

## <a name="may-2018"></a>Mayıs 2018

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>OBO akışı için KIMLIK belirteçleri kullanılamaz

**Tarih**: 1 Mayıs 2018

**Etkilenen uç noktalar**: V 1.0 ve v 2.0

**Etkilenen protokoller**: Örtük akış ve [OBO akışı](v1-oauth2-on-behalf-of-flow.md)

1 Mayıs 2018 ' den sonra, id_tokens yeni uygulamalar için bir OBO akışında onaylama olarak kullanılamaz. API 'Lerin güvenliğini sağlamak için, aynı uygulamanın bir istemcisi ve orta katmanı arasında bile erişim belirteçleri kullanılmalıdır. 1 Mayıs 2018 tarihinden önce kaydolan uygulamalar çalışmaya devam eder ve erişim belirteci için id_tokens değiş tokuş edebilir. Ancak, bu model en iyi yöntem olarak kabul edilmez.

Bu değişikliği geçici olarak çözmek için şunları yapabilirsiniz:

1. Uygulamanız için bir veya daha fazla kapsamı olan bir Web API 'SI oluşturun. Bu açık giriş noktası, daha ayrıntılı denetim ve güvenliğe izin verir.
1. Uygulamanızın bildiriminde, [Azure Portal](https://portal.azure.com) veya [uygulama kayıt portalı](https://apps.dev.microsoft.com)' nda, uygulamanın örtük akış aracılığıyla erişim belirteçleri vermesine izin verildiğinden emin olun. Bu `oauth2AllowImplicitFlow` anahtar aracılığıyla denetlenir.
1. İstemci uygulamanız aracılığıyla `response_type=id_token`bir id_token istediğinde, yukarıda oluşturulan Web API 'si için de bir`response_type=token`erişim belirteci () isteyin. Bu nedenle, v 2.0 uç noktası `scope` kullanılırken parametresi `api://GUID/SCOPE`şuna benzemelidir. V 1.0 uç `resource` noktasında parametresi, Web API 'sinin uygulama URI 'si olmalıdır.
1. Bu erişim belirtecini id_token yerine Orta katmana geçirin.  
