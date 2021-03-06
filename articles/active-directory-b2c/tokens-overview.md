---
title: Belirteçlere genel bakış-Azure Active Directory B2C
description: Azure Active Directory B2C ' de kullanılan belirteçler hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b4e268d35a2e31db0ce92ff61e66fd23bce68e38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516355"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C belirteçlere genel bakış

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C), her [kimlik doğrulama akışını](application-types.md)işlediği için çeşitli türlerde güvenlik belirteçleri yayar. Bu belge, her bir belirteç türünün biçimini, güvenlik özelliklerini ve içeriğini açıklar.

## <a name="token-types"></a>Belirteç türleri

Azure AD B2C, [OAuth 2,0 ve OpenID Connect protokollerini](protocols-overview.md)destekler, bu da kimlik doğrulama ve kaynaklara güvenli erişim belirteçlerini kullanır. Azure AD B2C kullanılan tüm belirteçler, taşıyıcı ve belirtecin konusu hakkında bilgi onayları içeren [JSON Web belirteçleri (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) .

Aşağıdaki belirteçler Azure AD B2C ile iletişim için kullanılır:

- *Kimlik belirteci* -uygulamanızdaki kullanıcıları tanımlamak için kullanabileceğiniz talepleri IÇEREN bir JWT. Bu belirteç, aynı uygulamanın veya hizmetin iki bileşeni arasındaki iletişim için HTTP isteklerinde güvenli bir şekilde gönderilir. Bir KIMLIK belirtecindeki talepleri, uygun gördüğünüz şekilde kullanabilirsiniz. Bunlar, genellikle hesap bilgilerini göstermek veya bir uygulamada erişim denetimi kararları almak için kullanılır. KIMLIK belirteçleri imzalanır, ancak şifrelenmez. Uygulamanız veya API 'niz bir KIMLIK belirteci aldığında, belirtecin gerçek olduğunu kanıtlamak için imzayı doğrulaması gerekir. Uygulamanızın veya API 'nizin geçerli olduğunu kanıtlamak için, belirteçteki bazı talepleri de doğrulaması gerekir. Senaryo gereksinimlerine bağlı olarak, bir uygulama tarafından doğrulanan talepler farklılık gösterebilir, ancak uygulamanız her senaryo için bazı ortak talep doğrulamaları gerçekleştirmelidir.
- *Erişim belirteci* -API 'lerinize verilen izinleri tanımlamak için kullanabileceğiniz talepleri IÇEREN bir JWT. Erişim belirteçleri imzalanır, ancak şifrelenmez. Erişim belirteçleri, API 'lere ve kaynak sunuculara erişim sağlamak için kullanılır.  API 'niz bir erişim belirteci aldığında, belirtecin gerçek olduğunu kanıtlamak için imzayı doğrulaması gerekir. Ayrıca, API 'nizin geçerli olduğunu kanıtlamak için belirteçteki bazı talepleri doğrulaması gerekir. Senaryo gereksinimlerine bağlı olarak, bir uygulama tarafından doğrulanan talepler farklılık gösterebilir, ancak uygulamanız her senaryo için bazı ortak talep doğrulamaları gerçekleştirmelidir.
- *Belirteç yenileme* -yenileme belirteçleri, bir OAuth 2,0 AKıŞıNDAKI yeni kimlik belirteçlerini ve erişim belirteçlerini almak için kullanılır. Bu kullanıcılar, uygulamanıza, kullanıcılar adına bu kullanıcılarla etkileşime gerek kalmadan, kaynaklara uzun süreli erişim sağlar. Yenileme belirteçleri uygulamanızda anlaşılmaz. Bunlar Azure AD B2C tarafından verilir ve yalnızca Azure AD B2C tarafından incelenebilir ve yorumlanamaz. Bunlar uzun süreli olmakla birlikte, uygulamanız belirli bir süre için yenileme belirtecinin son olacağı beklentisiyle yazılmamalıdır. Yenileme belirteçleri, çeşitli nedenlerle herhangi bir anda geçersiz kılınabilir. Uygulamanızın yenileme belirtecinin geçerli olup olmadığını bilmek için tek yol, Azure AD B2C bir belirteç isteği yaparak kullanmayı denemenin denenme yoludur. Yeni bir belirteç için yenileme belirteci kullandığınızda, belirteç yanıtında yeni bir yenileme belirteci alırsınız. Yeni yenileme belirtecini kaydedin. İstekte daha önce kullandığınız yenileme belirtecinin yerini alır. Bu eylem, yenileme belirteçlerinizin olabildiğince uzun süre geçerli kalmasını sağlamaya yardımcı olur. PKI CE ile yetkilendirme kodu akışını kullanan tek sayfalı uygulamaların her zaman 24 saat yenileme belirteci ömrü olduğunu unutmayın. [Tarayıcıda belirteçleri yenileme güvenlik etkileri hakkında daha fazla bilgi edinin](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="endpoints"></a>Uç Noktalar

[Kayıtlı bir uygulama](tutorial-register-applications.md) belirteçleri alır ve bu uç noktalara istek göndererek Azure AD B2C ile iletişim kurar:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Uygulamanızın Azure AD B2C aldığı güvenlik belirteçleri, `/authorize` veya `/token` uç noktalarından gelebilir. Uç noktadan KIMLIK belirteçleri alınırsa `/authorize` , genellikle JavaScript tabanlı Web uygulamalarında oturum açan kullanıcılar için kullanılan [örtük akış](implicit-flow-single-page-application.md)kullanılarak yapılır. Uç noktadan KIMLIK belirteçleri alınırsa `/token` , belirteci tarayıcıdan gizli tutan [yetkilendirme kodu akışı](openid-connect.md#get-a-token)kullanılarak yapılır.

## <a name="claims"></a>Talepler

Azure AD B2C kullandığınızda belirteçlerinizin içeriği üzerinde ayrıntılı denetime sahip olursunuz. [Kullanıcı akışlarını](user-flow-overview.md) ve [özel ilkeleri](custom-policy-overview.md) , uygulamanız için gerekli olan taleplerde belirli kullanıcı verisi kümelerini gönderecek şekilde yapılandırabilirsiniz. Bu talepler, **DisplayName** ve **emapostaadı** gibi standart özellikleri içerebilir. Uygulamalarınız, kullanıcıların ve isteklerin kimliğini güvenli bir şekilde doğrulamak için bu talepleri kullanabilir.

KIMLIK belirteçlerdeki talepler belirli bir sırada döndürülmez. Yeni talepler, KIMLIK belirteçlerinde herhangi bir zamanda tanıtılamaz. Yeni talepler tanıtıldığında uygulamanız kesmemelidir. Taleplerinizi [Özel Kullanıcı özniteliklerini](user-flow-custom-attributes.md) de dahil edebilirsiniz.

Aşağıdaki tabloda, KIMLIK belirteçleri ve Azure AD B2C tarafından verilen erişim belirteçlerinde bekleneceğiniz talepler listelenmektedir.

| Name | İste | Örnek değer | Description |
| ---- | ----- | ------------- | ----------- |
| Hedef kitle | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Belirtecin amaçlanan alıcısını tanımlar. Azure AD B2C için hedef kitle uygulama KIMLIĞIDIR. Uygulamanız bu değeri doğrulamalıdır ve eşleşmezse belirteci reddeder. Hedef kitle, kaynak ile eşanlamlıdır. |
| Veren | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Belirteci oluşturan ve döndüren güvenlik belirteci hizmetini (STS) belirler. Ayrıca, kullanıcının kimliğinin doğrulandığı dizini tanımlar. Uygulamanızın, uygun uç noktadan geldiğinden emin olmak için veren talebini doğrulaması gerekir. |
| Verilen: | `iat` | `1438535543` | Belirtecin verildiği zaman, dönem süresi cinsinden temsil edilir. |
| Sona erme saati | `exp` | `1438539443` | Belirtecin, dönem zamanında temsil edilen geçersiz hale geldiği zaman. Uygulamanızın belirteç ömrünün geçerliliğini doğrulamak için bu talebi kullanması gerekir. |
| Önce değil | `nbf` | `1438535543` | Belirtecin geçerli hale geldiği zaman, dönem süresi cinsinden temsil edilir. Bu zaman, genellikle belirtecin verildiği zaman ile aynıdır. Uygulamanızın belirteç ömrünün geçerliliğini doğrulamak için bu talebi kullanması gerekir. |
| Sürüm | `ver` | `1.0` | Azure AD B2C tarafından tanımlanan KIMLIK belirtecinin sürümü. |
| Kod karması | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Yalnızca belirteç bir OAuth 2,0 yetkilendirme koduyla birlikte verildiğinde KIMLIK belirtecine eklenen bir kod karması. Bir kod karması, bir yetkilendirme kodunun gerçekliğini doğrulamak için kullanılabilir. Bu doğrulamanın nasıl gerçekleştirileceği hakkında daha fazla bilgi için, [OpenID Connect belirtimine](https://openid.net/specs/openid-connect-core-1_0.html)bakın.  |
| Erişim belirteci karması | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Yalnızca belirteç bir OAuth 2,0 erişim belirteciyle birlikte verildiğinde, KIMLIK belirtecine eklenen bir erişim belirteci karması. Erişim belirteci karması, bir erişim belirtecinin orijinalliğini doğrulamak için kullanılabilir. Bu doğrulamanın nasıl gerçekleştirileceği hakkında daha fazla bilgi için, [OpenID Connect belirtimine](https://openid.net/specs/openid-connect-core-1_0.html) bakın  |
| Nonce | `nonce` | `12345` | Nonce, belirteç yeniden yürütme saldırılarını azaltmak için kullanılan bir stratejidir. Uygulamanız, sorgu parametresini kullanarak bir yetkilendirme isteğinde bir kerelik anahtar belirtebilir `nonce` . İstekte sağladığınız değer, `nonce` yalnızca BIR kimlik belirtecinin talebine değiştirilmemiş şekilde yayılır. Bu talep, uygulamanızın değeri istekte belirtilen değerle doğrulamasını sağlar. Uygulamanız, KIMLIK belirteci doğrulama işlemi sırasında bu doğrulamayı gerçekleştirmelidir. |
| Konu | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Belirtecin, bir uygulamanın kullanıcısı gibi bilgilerin hangi sorumluya ait olduğunu belirten sorumlu. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Bu, belirtecin bir kaynağa erişmek için ne zaman kullanıldığı gibi, güvenli bir şekilde, yetkilendirme denetimleri gerçekleştirmek için de kullanılabilir. Varsayılan olarak, konu talebi, dizindeki kullanıcının nesne KIMLIĞIYLE doldurulur. |
| Kimlik doğrulama bağlamı sınıfı başvurusu | `acr` | Uygulanamaz | Yalnızca eski ilkelerle kullanılır. |
| Güven çerçevesi ilkesi | `tfp` | `b2c_1_signupsignin1` | KIMLIK belirtecini almak için kullanılan ilkenin adı. |
| Kimlik doğrulama zamanı | `auth_time` | `1438535543` | Bir kullanıcının en son girilen kimlik bilgileri dönem içinde temsil edildiği zaman. Bu kimlik doğrulaması arasında yeni bir oturum açma, çoklu oturum açma (SSO) oturumu veya başka bir oturum açma türü gibi bir ayırt edici yok. `auth_time`Uygulamanın (veya kullanıcının) Azure AD B2C karşı bir kimlik doğrulama denemesi başlattığı son zaman. Kimlik doğrulaması için kullanılan yöntem farklılaştırmıyor. |
| Kapsam | `scp` | `Read`| Erişim belirteci için kaynağa verilen izinler. Verilen birden fazla izin boşlukla ayrılır. |
| Yetkili taraf | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | İsteği başlatan istemci uygulamasının **uygulama kimliği** . |

## <a name="configuration"></a>Yapılandırma

Aşağıdaki özellikler Azure AD B2C tarafından yayılan [güvenlik belirteçlerinin ömrünü yönetmek](configure-tokens.md) için kullanılır:

- **Erişim & kimliği belirteci yaşam süreleri (dakika)** -korumalı bir kaynağa erişim kazanmak Için kullanılan OAuth 2,0 taşıyıcı belirtecinin ömrü. Varsayılan değer 60 dakikadır. En düşük (kapsamlı) 5 dakikadır. Maksimum (dahil) 1440 dakikadır.

- **Yenileme belirteci yaşam süresi (gün)** -yeni ERIŞIM veya kimlik belirteci almak için yenileme belirtecinin kullanılabileceği en uzun süre. Ayrıca, uygulamanıza kapsam verildiyse, zaman aralığı yeni bir yenileme belirteci almak da içerir `offline_access` . Varsayılan değer 14 gündür. En düşük (kapsamlı) bir gündür. Maksimum (dahil) 90 gündür.

- **Yenileme belirteci kayan pencere ömrü (gün)** -bu süre dolduktan sonra, uygulama tarafından alınan en son yenileme belirtecinin geçerlilik süresi ne olursa olsun Kullanıcı yeniden kimlik doğrulaması zorlanır. Yalnızca, anahtar **sınırlanmış** olarak ayarlandıysa belirtilebilir. **Belirteç ömrü (gün)** değerinden büyük veya bu değere eşit olmalıdır. Anahtar **sınırsız** olarak ayarlandıysa, belirli bir değer belirtemezsiniz. Varsayılan değer 90 gündür. En düşük (kapsamlı) bir gündür. Maksimum (dahil) 365 gündür.

Aşağıdaki kullanım örnekleri şu özellikler kullanılarak etkinleştirilir:

- Kullanıcı uygulamada sürekli olarak etkin olduğu sürece kullanıcının bir mobil uygulamada süresiz olarak oturum açmış kalmasına izin verin. Oturum açma Kullanıcı akışınızda **yenileme belirteci kayan pencere yaşam süresi 'ni (gün)** **sınırsız** olarak ayarlayabilirsiniz.
- Uygun erişim belirteci yaşam sürelerini ayarlayarak sektörün güvenlik ve uyumluluk gereksinimlerini karşılayın.

Bu ayarlar, parola sıfırlama Kullanıcı akışları için kullanılamaz.

## <a name="compatibility"></a>Uyumluluk

Aşağıdaki özellikler, [belirteç uyumluluğunu yönetmek](configure-tokens.md)için kullanılır:

- **Verenin (ISS) talebi** -bu özellik, belirteci veren Azure AD B2C kiracıyı tanımlar. `https://<domain>/{B2C tenant GUID}/v2.0/` varsayılan değerdir. Değeri, `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` hem Azure AD B2C kiracının hem de belirteç isteğinde kullanılan Kullanıcı akışının kimliklerini içerir. Uygulamanızın veya kitaplığınızın [OpenID Connect Discovery 1,0 belirtiminde](https://openid.net/specs/openid-connect-discovery-1_0.html)uyumlu olması Azure AD B2C gerekiyorsa, bu değeri kullanın.

- **Subject (Sub) talebi** -bu özellik, belirtecinin bilgi onaylarsa varlığı tanımlar. Varsayılan değer **ObjectID**'dir ve bu, `sub` belirteçteki TALEBI kullanıcının nesne kimliğiyle doldurur. **Desteklenmeyen** değer yalnızca geriye dönük uyumluluk için sağlanır. Mümkün olan en kısa sürede **ObjectID** 'ye geçmeniz önerilir.

- **Ilke kimliğini temsil eden talep** -bu özellik, belirteç isteğinde kullanılan ilke adının doldurulduğu talep türünü tanımlar. `tfp` varsayılan değerdir. Değeri `acr` yalnızca geriye dönük uyumluluk için sağlanır.

## <a name="pass-through"></a>Geçiş

Kullanıcı yolculuğu başladığında Azure AD B2C bir kimlik sağlayıcısından erişim belirteci alır. Azure AD B2C, kullanıcı hakkındaki bilgileri almak için bu belirteci kullanır. Kullanıcı akışındaki bir talebi [, belirteci,](idp-pass-through-user-flow.md) Azure AD B2C kaydettiğinizde bulunan uygulamalara geçirmek için etkinleştirirsiniz. Uygulamanız, belirteci talep olarak geçirmenin avantajlarından yararlanmak için [Önerilen bir Kullanıcı akışı](user-flow-versions.md) kullanıyor olmalıdır.

Azure AD B2C Şu anda yalnızca Facebook ve Google içeren OAuth 2,0 kimlik sağlayıcılarının erişim belirtecinin geçirilmesini destekler. Diğer tüm kimlik sağlayıcıları için talep boş döndürülür.

## <a name="validation"></a>Doğrulama

Bir belirteci doğrulamak için, uygulamanız hem imzayı hem de belirtecin taleplerini denetlemelidir. Birçok açık kaynak kitaplığı, tercih ettiğiniz dile bağlı olarak JWTs 'yi doğrulamak için kullanılabilir. Kendi doğrulama mantığınızı gerçekleştirmek yerine bu seçenekleri araştırmanızı öneririz.

### <a name="validate-signature"></a>İmzayı Doğrula

JWT üç bölüt, bir *üst bilgi*, *gövde* ve *imza* içerir. İmza segmenti, uygulamanızın güvenilebilmesi için belirtecin orijinalliğini doğrulamak üzere kullanılabilir. Azure AD B2C belirteçleri, RSA 256 gibi sektör standardı asimetrik şifreleme algoritmaları kullanılarak imzalanır.

Belirtecin üst bilgisi, belirteci imzalamak için kullanılan anahtar ve şifreleme yöntemiyle ilgili bilgiler içerir:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

**Algoritma** talebinin değeri, belirteci imzalamak için kullanılan algoritmadır. **Çocuk** talebinin değeri, belirteci imzalamak için kullanılan ortak anahtardır. Belirli bir zamanda Azure AD B2C, bir belirteci bir genel özel anahtar çiftleri kümesinden birini kullanarak imzalayabilirler. Azure AD B2C, olası anahtar kümesini düzenli aralıklarla döndürür. Bu anahtar değişikliklerini otomatik olarak işlemek için uygulamanızın yazılması gerekir. Her 24 saatte bir Azure AD B2C tarafından kullanılan ortak anahtarların güncelleştirmelerini denetlemek için makul bir sıklık. Beklenmeyen anahtar değişikliklerini işlemek için, uygulamanız beklenmeyen bir **KID** değeri alırsa ortak anahtarları yeniden almak üzere yazılmalıdır.

Azure AD B2C bir OpenID Connect meta veri uç noktası vardır. Bu uç noktayı kullanarak, uygulamalar çalışma zamanında Azure AD B2C hakkında bilgi isteyebilir. Bu bilgiler uç noktaları, belirteç içerikleri ve belirteç imzalama anahtarlarını içerir. Azure AD B2C kiracınız her ilke için bir JSON meta veri belgesi içerir. Meta veri belgesi, çeşitli yararlı bilgi parçalarını içeren bir JSON nesnesidir. Meta veriler, belirteçleri imzalamak için kullanılan ortak anahtar kümesinin konumunu veren **jwks_uri** içerir. Bu konum burada sağlanır, ancak meta veri belgesi kullanarak konumu dinamik olarak getirmek ve **jwks_uri** ayrıştırmak en iyisidir:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
Bu URL 'de bulunan JSON belgesi, belirli bir anda kullanımda olan tüm ortak anahtar bilgilerini içerir. Uygulamanız, `kid` belirli bir belirteci imzalamak için kullanılan JSON belgesinde ortak anahtarı seçmek IÇIN JWT üstbilgisindeki talebi kullanabilir. Daha sonra doğru ortak anahtarı ve belirtilen algoritmayı kullanarak imza doğrulaması gerçekleştirebilir.

Kiracıdaki ilke için meta veri belgesi `B2C_1_signupsignin1` `contoso.onmicrosoft.com` şurada bulunur:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Bir belirteci imzalamak için kullanılan ilkeyi (ve meta verileri istemek için nereye gideceğini) anlamak için iki seçeneğiniz vardır. İlk olarak, ilke adı `tfp` belirteçte (varsayılan) veya `acr` talebe (yapılandırıldığı gibi) dahil edilir. No Base-64, gövdenin gövdesinden dışarı kod çözmede ve sonuç olarak JSON dizesinin serisini kaldırarak bu talepleri ayrıştırdırabilirsiniz. `tfp`Veya `acr` talebi, belirteci vermek için kullanılan ilkenin adıdır. Diğer seçenek ise, `state` isteği keserken parametre değerindeki ilkeyi kodlamak ve sonra hangi ilkenin kullanıldığını anlamak için onu çözmeye yönelik olur. Her iki yöntem de geçerlidir.

İmza doğrulamanın nasıl gerçekleştirileceği hakkında bir açıklama bu belgenin kapsamı dışındadır. Bir belirteci doğrulamanızı sağlayacak pek çok açık kaynak kitaplığı mevcuttur.

### <a name="validate-claims"></a>Talepleri doğrula

Uygulamalarınız veya API 'niz bir KIMLIK belirteci aldığında, KIMLIK belirtecindeki taleplere karşı çeşitli denetimler de gerçekleştirmelidir. Aşağıdaki talepler denetlenmelidir:

- **hedef kitle** -kimlik belirtecinin uygulamanıza verilme amacını doğrular.
- **Before** ve **EXPIRATION saati** -kimlik belirtecinin süresinin dolmadığını doğrular.
- **Issuer** -belirtecin uygulamanıza Azure AD B2C tarafından verildiğini doğrular.
- **nonce** -belirteç yeniden yürütme saldırıları risk azaltma.

Uygulamanızın gerçekleştirmesi gereken doğrulamaları tam bir listesi için [OpenID Connect belirtimine](https://openid.net)bakın.

## <a name="next-steps"></a>Sonraki adımlar

[Erişim belirteçlerini kullanma](access-tokens.md)hakkında daha fazla bilgi edinin.

