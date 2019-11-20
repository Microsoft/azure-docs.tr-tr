---
title: MSAL kullanarak belirteçleri alma ve önbelleğe alma
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak belirteçleri edinme ve önbelleğe alma hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df30156cde0244209f5f07c2627887252642525
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903012"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Microsoft kimlik doğrulama kitaplığı (MSAL) kullanarak belirteçleri alma ve önbelleğe alma

[Erişim belirteçleri](access-tokens.md) , istemcilerin Azure tarafından korunan Web API 'lerini güvenli bir şekilde çağırmasını sağlar. Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanarak bir belirteç edinmenin birçok yolu vardır. Bazı yollarla bir Web tarayıcısı aracılığıyla Kullanıcı etkileşimi gerekir. Bazıları hiçbir Kullanıcı etkileşimi gerektirmez. Genel olarak, bir belirteç alma yöntemi, uygulamanın ortak bir istemci uygulaması (masaüstü veya mobil uygulama) veya gizli bir istemci uygulaması (bir Windows hizmeti gibi Web uygulaması, Web API 'SI veya Daemon uygulaması) olması halinde değişir.

MSAL bir belirteci aldıktan sonra önbelleğe alır.  Uygulama kodu, belirteci başka yollarla almadan önce sessizce (önbellekten) bir belirteç almayı denemelidir.

Ayrıca, hesapları önbellekten kaldırarak elde edilen belirteç önbelleğini temizleyebilirsiniz. Bu, tarayıcıda bulunan oturum tanımlama bilgisini kaldırmaz, ancak.

## <a name="scopes-when-acquiring-tokens"></a>Belirteçleri alırken kapsamlar

[Kapsamlar](v2-permissions-and-consent.md) , BIR Web API 'sinin istemci uygulamalar için erişim istemesi için sunduğu izinlerdir. İstemci uygulamaları, Web API 'Lerine erişmek için belirteçleri almak üzere kimlik doğrulama istekleri yaparken kullanıcının bu kapsamlar için izin vermesini ister. MSAL, geliştiriciler için Azure AD 'ye (v 1.0) ve Microsoft Identity platform (v 2.0) API 'Lerine erişme belirteçleri almanızı sağlar. v 2.0 protokolü isteklerde kaynak yerine kapsamları kullanır. Daha fazla bilgi için, [v 1.0 ve v 2.0 karşılaştırması](active-directory-v2-compare.md)makalesini okuyun. Web API 'sinin kabul ettiği belirteç sürümünün yapılandırmasına bağlı olarak, v 2.0 uç noktası erişim belirtecini MSAL öğesine döndürür.

Bir dizi MSAL alma belirteci yöntemi bir *kapsamlar* parametresi gerektirir. Bu parametre, istenen izinleri ve istenen kaynakları bildiren dizelerin basit bir listesidir. İyi bilinen kapsamlar [Microsoft Graph izinleridir](/graph/permissions-reference).

V 1.0 kaynaklarına erişmek için MSAL de mümkündür. Daha fazla bilgi için, [bir v 1.0 uygulaması Için kapsamları](msal-v1-app-scopes.md)okuyun.

### <a name="request-specific-scopes-for-a-web-api"></a>Bir Web API 'SI için belirli kapsamlar isteme

Uygulamanızın bir kaynak API 'SI için belirli izinlerle belirteç istemesi gerektiğinde, API 'nin uygulama KIMLIĞI URI 'sini içeren kapsamları aşağıdaki biçimde geçirmeniz gerekir: *&lt;uygulama KIMLIĞI URI 'si&gt;/&lt;kapsam&gt;*

Örneğin, Microsoft Graph API için kapsamlar: `https://graph.microsoft.com/User.Read`

Ya da, örneğin, özel bir Web API 'SI için kapsamlar: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Yalnızca Microsoft Graph API 'SI için, bir kapsam değeri `user.read` `https://graph.microsoft.com/User.Read` biçimine eşlenir ve birbirlerinin yerine kullanılabilir.

> [!NOTE]
> Azure Resource Manager API (https://management.core.windows.net/) gibi bazı Web API 'Leri, erişim belirtecinin hedef kitle talebinde (AUD) sonunda bir '/' bekliyor. Bu durumda, belirtecin API 'de geçerli olması için kapsamı https://management.core.windows.net//user_impersonation (çift eğik çizgiye dikkat edin) olarak geçirmek önemlidir.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Artımlı izin için dinamik kapsamlar isteyin

V 1.0 kullanarak uygulamalar oluştururken, kullanıcının oturum açma sırasında izin vermesini sağlamak için uygulamanın gerektirdiği tüm izin (statik kapsamlar) kümesini kaydetmeniz gerekiyordu. V 2.0 'da kapsam parametresini kullanarak gerektiğinde ek izinler isteyebilirsiniz. Bunlara dinamik kapsamlar denir ve kullanıcının kapsamlar için artımlı onay sağlamasına izin verir.

Örneğin, başlangıçta Kullanıcı oturumu açabilir ve her türlü erişimi reddedebilirsiniz. Daha sonra, belirteç al yöntemlerinde takvim kapsamını isteyerek ve kullanıcının iznini elde ederek kullanıcının takvimini okuma özelliği verebilirsiniz.

Örneğin: `https://graph.microsoft.com/User.Read` ve `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Belirteçleri sessizce alma (önbellekten)

MSAL, bir belirteç önbelleğini (veya gizli istemci uygulamaları için iki önbellek) tutar ve alındıktan sonra bir belirteci önbelleğe alır.  Çoğu durumda, bir belirteci sessizce almaya çalışmak, önbellekteki bir belirtece göre daha fazla kapsam içeren başka bir belirteç elde eder. Ayrıca, süresi dolmak üzere (belirteç önbelleği de bir yenileme belirteci içerdiğinden) bir belirteci yenileyebilme özelliği de vardır.

### <a name="recommended-call-pattern-for-public-client-applications"></a>Ortak istemci uygulamaları için önerilen çağrı deseninin

Uygulama kodu, önce bir belirteci sessizce (önbellekten) almayı denemelidir.  Yöntem çağrısı bir "UI gerekli" hatası veya özel durum döndürürse, bir belirteci başka yollarla aramayı deneyin. 

Bununla birlikte, sessizce bir belirteç elde etmek zorunda kalmadan iki akış vardır:

- Kullanıcı belirteci önbelleğini kullanmayan [istemci kimlik bilgileri akışı](msal-authentication-flows.md#client-credentials), ancak uygulama belirteci önbelleği. Bu yöntem, STS 'ye bir istek göndermeden önce bu uygulama belirteci önbelleğini doğrulama işlemini gerçekleştirir.
- Web Apps ' de [yetkilendirme kodu akışı](msal-authentication-flows.md#authorization-code) , uygulamanın aldığı bir kodu, kullanıcının oturum açtığı ve daha fazla kapsam için izin vermesini sağlayarak. Bir kod bir hesap değil, bir parametre olarak geçirildiğinden, bir hizmet için bir çağrı gerektiren ve bu durumda, bir hizmetin önüne girmeden önce önbellekte arama yapılamaz.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Yetkilendirme kodu akışını kullanarak Web Apps önerilen çağrı deseninin

[OpenID Connect yetkilendirme kodu akışını](v2-protocols-oidc.md)kullanan Web uygulamaları için, denetleyicilerde önerilen model:

- Özelleştirilmiş serileştirme ile bir belirteç önbelleği ile gizli bir istemci uygulaması örneğini oluşturun. 
- Yetkilendirme kodu akışını kullanarak belirteci alma

## <a name="acquiring-tokens"></a>Belirteçler alınıyor

Genellikle, bir belirteç alma yöntemi, bir genel istemci veya gizli istemci uygulaması olmasına bağlıdır.

### <a name="public-client-applications"></a>Ortak istemci uygulamaları

Ortak istemci uygulamaları için (masaüstü veya mobil uygulama) şunları yapabilirsiniz:
- Genellikle belirteçleri etkileşimli olarak alarak kullanıcı ARABIRIMI veya açılır pencere aracılığıyla oturum açmasını ister.
- Masaüstü uygulaması, etki alanına veya Azure 'a katılmış bir Windows bilgisayarında çalışıyorsa tümleşik Windows kimlik doğrulaması (ıWA/Kerberos) kullanan [oturum açmış kullanıcı için sessizce bir belirteç alabilir](msal-authentication-flows.md#integrated-windows-authentication) .
- , .NET Framework masaüstü istemci uygulamalarında [Kullanıcı adı ve parola içeren bir belirteç alabilir](msal-authentication-flows.md#usernamepassword) , ancak bu önerilmez. Gizli istemci uygulamalarında Kullanıcı adı/parola kullanmayın.
- , Bir Web tarayıcısına sahip olmayan cihazlarda çalışan uygulamalarda [cihaz kod akışı](msal-authentication-flows.md#device-code) aracılığıyla bir belirteç alabilir. Kullanıcı bir URL ve kod ile sağlanır, daha sonra başka bir cihazdaki bir Web tarayıcısına gider ve kodu girer ve oturum açar.  Daha sonra Azure AD, tarayıcı-daha seyrek cihaza bir belirteç gönderir.

### <a name="confidential-client-applications"></a>Gizli istemci uygulamaları

Gizli istemci uygulamaları (Web uygulaması, Web API 'SI veya Windows hizmeti gibi Daemon uygulaması) için şunları yapmanız gerekir:
- [İstemci kimlik bilgileri akışını](msal-authentication-flows.md#client-credentials)kullanarak, bir kullanıcı için değil, **uygulamanın kendisi için** belirteç alın. Bu, araçları eşitlemek için veya belirli bir kullanıcı değil, genel olarak kullanıcıları işleyen araçlar için kullanılabilir. 
- Bir Web API 'si için Kullanıcı adına bir API çağrısı yapmak üzere, bir Web API 'si için [Şirket adına akışını](msal-authentication-flows.md#on-behalf-of) kullanın. Uygulama, Kullanıcı onayını temel alan bir belirteç almak için istemci kimlik bilgileriyle tanımlanır (örneğin, SAML veya JWT belirteci). Bu akış, hizmet-hizmet çağrılarında belirli bir kullanıcının kaynaklarına erişmesi gereken uygulamalar tarafından kullanılır.
- Kullanıcı yetkilendirme isteği URL 'SI aracılığıyla oturum açtıktan sonra Web Apps 'teki [yetkilendirme kodu akışını](msal-authentication-flows.md#authorization-code) kullanarak belirteçleri alın. OpenID Connect uygulaması genellikle bu mekanizmayı kullanarak kullanıcının açık KIMLIK bağlantısı kullanarak oturum açmasını sağlar ve ardından Kullanıcı adına Web API 'Lerine erişin.

## <a name="authentication-results"></a>Kimlik doğrulama sonuçları

İstemciniz bir erişim belirteci istediğinde, Azure AD, erişim belirteci hakkında bazı meta veriler içeren bir kimlik doğrulama sonucu da döndürür. Bu bilgiler, erişim belirtecinin süre sonu süresini ve geçerli olduğu kapsamları içerir. Bu veriler, uygulamanızın erişim belirtecinin kendisini ayrıştırmasına gerek kalmadan erişim belirteçlerinin akıllı önbelleğe alınmasına izin verir.  Kimlik doğrulama sonucu şunları gösterir:

- Kaynaklara erişmek için Web API 'sinin [erişim belirteci](access-tokens.md) . Bu bir dizedir, genellikle Base64 kodlamalı JWT ancak istemci, erişim belirtecinin içinde asla görünmemelidir. Biçim kararlı kalacak şekilde garanti edilmez ve kaynak için şifrelenebilir. İstemci üzerindeki erişim belirteci içeriğine bağlı olarak kod yazan kişiler, hata ve istemci mantıksal sonlarının en büyük kaynaklarından biridir.
- Kullanıcının [kimlik belirteci](id-tokens.md) (JWT).
- Belirtecin süresinin dolacağı tarih/saate bildiren belirteç süre sonu.
- Kiracı KIMLIĞI, kullanıcının bulunduğu kiracıyı içerir. Konuk kullanıcılar (Azure AD B2B senaryoları) için kiracı KIMLIĞI, benzersiz kiracı değil Konuk kiracıya ait değildir. Belirteç bir kullanıcı adına teslim edildiğinde, kimlik doğrulama sonucu bu kullanıcı hakkındaki bilgileri de içerir. Belirteçleri Kullanıcı (uygulama için) olmadan istenen gizli istemci akışları için, bu kullanıcı bilgileri null olur.
- Belirtecin verildiği kapsamlar.
- Kullanıcının benzersiz KIMLIĞI.

## <a name="next-steps"></a>Sonraki adımlar

Java için MSAL kullanıyorsanız, [Java IÇIN msal Içindeki özel belirteç önbelleği serileştirme](msal-java-token-cache-serialization.md)hakkında bilgi edinin.

[Hataları ve özel durumları işleme](msal-handling-exceptions.md)hakkında bilgi edinin.
