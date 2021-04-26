---
title: Microsoft kimlik doğrulama kitaplığı ile & önbellek belirteçleri alma (MSAL) | Mavisi
titleSuffix: Microsoft identity platform
description: MSAL kullanarak belirteçleri edinme ve önbelleğe alma hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 98ae81626db637f5b0bd6bfe9e294c32293d09e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755065"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Microsoft kimlik doğrulama kitaplığı (MSAL) kullanarak belirteçleri alma ve önbelleğe alma

[Erişim belirteçleri](access-tokens.md) , istemcilerin Azure tarafından korunan Web API 'lerini güvenli bir şekilde çağırmasını sağlar. Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak bir belirteç edinmenin birkaç yolu vardır. Bazıları bir Web tarayıcısı üzerinden kullanıcı etkileşimi gerektirir, diğerleri ise Kullanıcı etkileşimi gerektirmez. Genel olarak, bir belirteci almak için kullanılan yöntem, uygulamanın masaüstü veya mobil uygulama gibi bir genel istemci uygulaması veya Web uygulaması, Web API 'SI ya da daemon uygulaması gibi bir gizli istemci uygulaması olmasına bağlıdır.

MSAL, bir belirteci alındıktan sonra önbelleğe alır. Uygulama kodunuz öncelikle bir belirteci başka yollarla almayı denemeden önce önbellekten sessizce sessizce bir belirteç almayı denemelidir.

Ayrıca, hesapları önbellekten kaldırarak elde edilen belirteç önbelleğini temizleyebilirsiniz. Ancak bu, tarayıcıda bulunan oturum tanımlama bilgisini kaldırmaz.

## <a name="scopes-when-acquiring-tokens"></a>Belirteçleri alırken kapsamlar

[Kapsamlar](v2-permissions-and-consent.md) , BIR Web API 'sinin istemci uygulamaların erişim isteyebilmesini sağlayan izinlerdir. İstemci uygulamaları, Web API 'Lerine erişmek için belirteçleri almak üzere kimlik doğrulama istekleri yaparken kullanıcının bu kapsamlar için izin vermesini ister. MSAL, geliştiriciler için Azure AD 'ye (v 1.0) ve Microsoft Identity platform API 'Lerine erişme belirteçleri almanızı sağlar. v 2.0 protokolü isteklerde kaynak yerine kapsamları kullanır. Daha fazla bilgi için, [v 1.0 ve v 2.0 karşılaştırması](../azuread-dev/azure-ad-endpoint-comparison.md)makalesini okuyun. Web API 'sinin kabul ettiği belirteç sürümünün yapılandırmasına bağlı olarak, v 2.0 uç noktası erişim belirtecini MSAL öğesine döndürür.

MSAL 'ın belirteç alma yöntemlerinden birkaçı için bir `scopes` parametre gerekir. Parametresi, istenen `scopes` izinleri ve istenen kaynakları bildiren dizelerin bir listesidir. İyi bilinen kapsamlar [Microsoft Graph izinleridir](/graph/permissions-reference).

V 1.0 kaynaklarına erişmek için MSAL de mümkündür. Daha fazla bilgi için bkz. [bir v 1.0 uygulaması Için kapsamlar](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Bir Web API 'SI için istek kapsamları

Uygulamanızın bir kaynak API 'si için belirli izinlere sahip bir erişim belirteci istemesi gerektiğinde, API 'nin uygulama KIMLIĞI URI 'sini içeren kapsamları bu biçimde geçirin `<app ID URI>/<scope>` .

Farklı kaynaklar için bazı örnek kapsam değerleri:

- Microsoft Graph API 'SI: `https://graph.microsoft.com/User.Read`
- Özel Web API 'SI: `api://11111111-1111-1111-1111-111111111111/api.read`

Kapsam değerinin biçimi, erişim belirtecini alan kaynağa (API) ve `aud` kabul ettiği talep değerlerine bağlı olarak değişir.

Yalnızca Microsoft Graph için, `user.read` kapsam ile eşlenir `https://graph.microsoft.com/User.Read` ve her iki kapsam biçimi birbirinin yerine kullanılabilir.

Azure Resource Manager API gibi bazı Web API 'Leri ( https://management.core.windows.net/) erişim belirtecinin hedef kitle talebinde () bir sondaki eğik çizgi ('/') bekler `aud` . Bu durumda, `https://management.core.windows.net//user_impersonation` çift eğik çizgi ('//') dahil olmak üzere kapsamı geçirin.

Diğer API 'Ler, kapsam değerine *hiçbir şema veya konağın* dahil edilmesini gerektirebilir ve yalnızca uygulama KIMLIĞI (GUID) ve kapsam adı bekler, örneğin:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Aşağı akış kaynağı `401` deneticinizin altındaysa, erişim belirtecini kaynağa geçirirken farklı kapsam değer biçimlerini (örneğin, düzen ve ana bilgisayar içeren/olmayan) denemeniz gerekebilir.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Artımlı izin için dinamik kapsamlar isteyin

Uygulamanız veya gereksinimleriyle ilgili özellikler değiştikçe, kapsam parametresini kullanarak gerektiğinde ek izinler isteyebilirsiniz. Bu tür *dinamik kapsamlar* , kullanıcılarınızın kapsamlar için artımlı onay sağlamasına izin verir.

Örneğin, Kullanıcı oturumu açabilir, ancak başlangıçta herhangi bir kaynağa erişimi reddedebilirsiniz. Daha sonra, belirteç al yönteminde takvim kapsamını isteyerek ve kullanıcının iznini elde etmek için bu kullanıcılara takvimini görüntüleme olanağı verebilirsiniz. Örneğin, `https://graph.microsoft.com/User.Read` ve `https://graph.microsoft.com/Calendar.Read` kapsamlarını isteyerek.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Belirteçleri sessizce alma (önbellekten)

MSAL, bir belirteç önbelleğini (veya gizli istemci uygulamaları için iki önbellek) tutar ve alındıktan sonra bir belirteci önbelleğe alır. Çoğu durumda, bir belirteci sessizce almaya çalışmak, önbellekteki bir belirtece göre daha fazla kapsam içeren başka bir belirteç elde eder. Ayrıca, süresi dolmak üzere (belirteç önbelleği de bir yenileme belirteci içerdiğinden) bir belirteci yenileyebilme özelliği de vardır.

### <a name="recommended-call-pattern-for-public-client-applications"></a>Ortak istemci uygulamaları için önerilen çağrı deseninin

Uygulama kodu öncelikle önbellekten sessizce bir belirteç almayı denemelidir. Yöntem çağrısı bir "UI gerekli" hatası veya özel durum döndürürse, bir belirteci başka yollarla aramayı deneyin.

Bununla birlikte, sessizce bir belirteç edinmeyi **denememelisiniz** , ancak bu iki akış vardır:

- Kullanıcı belirteci önbelleğini, ancak bir uygulama belirteci önbelleğini kullanmayan [istemci kimlik bilgileri akışı](msal-authentication-flows.md#client-credentials). Bu yöntem, güvenlik belirteci hizmeti 'ne (STS) istek göndermeden önce uygulama belirteci önbelleğini doğrulama işlemini gerçekleştirir.
- Web Apps 'teki [yetkilendirme kodu akışı](msal-authentication-flows.md#authorization-code) , uygulamanın kullanıcı oturumu açarak elde ettiği ve daha fazla kapsam için izin veren bir kodu kullandığından. Bir hesap değil, bir kod bir parametre olarak geçirilmediğinden, bir hizmete bir çağrı çağıran kodu benimseme işleminden önce Yöntem önbellekte görünmeyebilir.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Yetkilendirme kodu akışını kullanarak Web uygulamalarında önerilen çağrı deseninin

[OpenID Connect yetkilendirme kodu akışını](v2-protocols-oidc.md)kullanan Web uygulamaları için, denetleyicilerde önerilen model:

- Özelleştirilmiş serileştirme ile bir belirteç önbelleği ile gizli bir istemci uygulaması örneğini oluşturun.
- Yetkilendirme kodu akışını kullanarak belirteci alma

## <a name="acquiring-tokens"></a>Belirteçler alınıyor

Genellikle, bir belirteç alma yöntemi, bir genel istemci veya gizli istemci uygulaması olmasına bağlıdır.

### <a name="public-client-applications"></a>Ortak istemci uygulamaları

Ortak istemci uygulamaları için (masaüstü veya mobil uygulama) şunları yapabilirsiniz:

- Genellikle belirteçleri etkileşimli olarak alarak kullanıcı ARABIRIMI veya açılır pencere aracılığıyla oturum açmasını ister.
- Masaüstü uygulaması, etki alanına veya Azure 'a katılmış bir Windows bilgisayarında çalışıyorsa tümleşik Windows kimlik doğrulaması (ıWA/Kerberos) kullanan [oturum açmış kullanıcı için sessizce bir belirteç alabilir](msal-authentication-flows.md#integrated-windows-authentication) .
- .NET Framework masaüstü istemci uygulamalarında [Kullanıcı adı ve parola içeren bir belirteç alabilir](msal-authentication-flows.md#usernamepassword) (önerilmez). Gizli istemci uygulamalarında Kullanıcı adı/parola kullanmayın.
- , Bir Web tarayıcısına sahip olmayan cihazlarda çalışan uygulamalarda [cihaz kod akışı](msal-authentication-flows.md#device-code) aracılığıyla bir belirteç alabilir. Kullanıcı bir URL ve kod ile sağlanır, daha sonra başka bir cihazdaki bir Web tarayıcısına gider ve kodu girer ve oturum açar. Daha sonra Azure AD, tarayıcı-daha seyrek cihaza bir belirteç gönderir.

### <a name="confidential-client-applications"></a>Gizli istemci uygulamaları

Gizli istemci uygulamaları (Web uygulaması, Web API 'SI veya bir Windows hizmeti gibi bir Daemon uygulaması) için şunları yapmanız gerekir:

- [İstemci kimlik bilgileri akışını](msal-authentication-flows.md#client-credentials)kullanarak, bir kullanıcı için değil, **uygulamanın kendisi için** belirteç alın. Bu teknik, belirli bir kullanıcı için değil, genel olarak kullanıcıları işleyen araçların veya araçların eşitlenmesi için kullanılabilir.
- Bir Web API 'si için Kullanıcı adına bir API çağrısı yapmak üzere, bir Web API 'si için [Şirket adına akışını](msal-authentication-flows.md#on-behalf-of) kullanın. Uygulama, Kullanıcı onaylaması (SAML, örneğin veya JWT belirteci) temelinde bir belirteç almak için istemci kimlik bilgileriyle tanımlanır. Bu akış, hizmet-hizmet çağrılarında belirli bir kullanıcının kaynaklarına erişmesi gereken uygulamalar tarafından kullanılır.
- Kullanıcı yetkilendirme isteği URL 'SI aracılığıyla oturum açtıktan sonra Web Apps 'teki [yetkilendirme kodu akışını](msal-authentication-flows.md#authorization-code) kullanarak belirteçleri alın. OpenID Connect uygulaması genellikle bu mekanizmayı kullanarak kullanıcının açık KIMLIK bağlantısı kullanarak oturum açmasını sağlar ve ardından Kullanıcı adına Web API 'Lerine erişin.

## <a name="authentication-results"></a>Kimlik doğrulama sonuçları

İstemciniz bir erişim belirteci istediğinde, Azure AD erişim belirteci hakkında meta veriler içeren bir kimlik doğrulama sonucu da döndürür. Bu bilgiler, erişim belirtecinin süre sonu süresini ve geçerli olduğu kapsamları içerir. Bu veriler, uygulamanızın erişim belirtecinin kendisini ayrıştırmasına gerek kalmadan erişim belirteçlerinin akıllı önbelleğe alınmasına izin verir. Kimlik doğrulama sonucu şunları gösterir:

- Kaynaklara erişmek için Web API 'sinin [erişim belirteci](access-tokens.md) . Bu dize genellikle Base64 kodlamalı bir JWT, ancak istemci hiçbir şekilde erişim belirtecinin içinde görünmemelidir. Biçim kararlı olmaya garanti edilmez ve kaynak için şifrelenebilir. İstemci üzerindeki erişim belirteci içeriğine bağlı olarak kod yazan kişiler, en sık karşılaşılan hata ve istemci mantıksal ayırıcılarınızdan biridir.
- Kullanıcının [kimlik belirteci](id-tokens.md) (JWT).
- Belirtecin süresinin dolacağı tarih/saate bildiren belirteç süre sonu.
- Kiracı KIMLIĞI, kullanıcının bulunduğu kiracıyı içerir. Konuk kullanıcılar (Azure AD B2B senaryoları) için kiracı KIMLIĞI, benzersiz kiracı değil Konuk kiracıya ait değildir. Belirteç bir kullanıcı adına teslim edildiğinde, kimlik doğrulama sonucu bu kullanıcı hakkındaki bilgileri de içerir. Belirteçleri Kullanıcı (uygulama için) olmadan istenen gizli istemci akışları için, bu kullanıcı bilgileri null olur.
- Belirtecin verildiği kapsamlar.
- Kullanıcının benzersiz KIMLIĞI.

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Ileri Arka plan uygulamaları ve hizmetlerinde kullanıcının önbelleğe alınmış belirteçlerine erişme

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Sonraki adımlar

MSAL tarafından desteklenen platformların birkaçı, bu platformun kitaplığı için belgelerde ek belirteç önbelleğiyle ilgili bilgilere sahiptir. Örnek:
- [MSAL.NET kullanarak belirteç önbelleğinden belirteç alma](msal-net-acquire-token-silently.md)
- [MSAL.js ile çoklu oturum açma](msal-js-sso.md)
- [Python için MSAL içinde özel belirteç önbelleği serileştirme](msal-python-token-cache-serialization.md)
- [Java için MSAL içinde özel belirteç önbelleği serileştirme](msal-java-token-cache-serialization.md)
