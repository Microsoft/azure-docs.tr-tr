---
title: MSAL kimlik doğrulama akışları | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı (MSAL) tarafından kullanılan kimlik doğrulama akışları ve izinler hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 78932e5852453fe996e26a278f8a1859a8ecf546
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755016"
---
# <a name="authentication-flows"></a>Kimlik doğrulama akışları

Microsoft kimlik doğrulama kitaplığı (MSAL), farklı uygulama senaryolarında kullanılmak üzere çeşitli kimlik doğrulama akışlarını destekler.

| Akış | Açıklama | Kullanıldığı yer |
|--|--|--|
| [Yetkilendirme kodu](#authorization-code) | Web API 'Leri gibi korumalı kaynaklara erişim kazanmak için bir cihaza yüklenen uygulamalarda kullanılır. Mobil ve Masaüstü uygulamalarınıza oturum açma ve API erişimi eklemenize olanak sağlar. | [Masaüstü uygulamaları](scenario-desktop-overview.md), [mobil uygulamalar](scenario-mobile-overview.md), [Web uygulamaları](scenario-web-app-call-api-overview.md) |
| [İstemci kimlik bilgileri](#client-credentials) | Bir uygulamanın kimliğini kullanarak Web 'de barındırılan kaynaklara erişmenizi sağlar. Yaygın olarak, bir kullanıcıyla etkileşim kurmadan, arka planda çalışması gereken sunucu-sunucu etkileşimleri için kullanılır. | [Daemon uygulamaları](scenario-daemon-overview.md) |
| [Cihaz kodu](#device-code) | Kullanıcıların, akıllı TV, IoT cihazı veya yazıcı gibi giriş kısıtlı cihazlarda oturum açmasına olanak sağlar. | [Masaüstü/mobil uygulamalar](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Örtük izin](#implicit-grant) | Uygulamanın arka uç sunucu kimlik bilgisi değişimi gerçekleştirmeden belirteçleri almasına izin verir. Uygulamanın kullanıcı tarafından oturum açmasını, oturum korumasını ve diğer Web API 'Lerine belirteç almasını sağlar ve bunların hepsi istemci JavaScript kodu içindedir. | [Tek sayfalı uygulamalar (SPA)](scenario-spa-overview.md) |
| [Adına-](#on-behalf-of) | Bir uygulama bir hizmet veya Web API 'sini çağırır, bu da başka bir hizmet veya Web API çağrısı gerektirir. Amaç, temsilci kullanıcı kimliğini ve izinleri istek zinciri aracılığıyla yaymanız önerilir. | [Web API'leri](scenario-web-api-call-api-overview.md) |
| [Kullanıcı adı/parola](#usernamepassword) | Bir uygulamanın, parolasını doğrudan işleyerek kullanıcının oturum açmasına izin verir. Bu akış önerilmez. | [Masaüstü/mobil uygulamalar](scenario-desktop-acquire-token.md#username-and-password) |
| [Tümleşik Windows Kimlik Doğrulaması](#integrated-windows-authentication) | Etki alanı veya Azure Active Directory (Azure AD) ile Birleşik bilgisayarlardaki uygulamaların sessizce bir belirteç almasına izin verir (kullanıcıdan herhangi bir kullanıcı ARABIRIMI etkileşimi olmadan). | [Masaüstü/mobil uygulamalar](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Her akışın belirteçleri ve kodları nasıl yayar

İstemci uygulamanızın nasıl oluşturulduğuna bağlı olarak, Microsoft Identity platform tarafından desteklenen bir veya daha fazla kimlik doğrulama akışı kullanabilir. Bu akışlar, birkaç belirteç türü ve yetkilendirme kodu üretebilir ve bunların çalışmasını sağlamak için farklı belirteçler gerektirebilir.

| Akış                                                                               | Gerektirmeyen            | id_token | erişim belirteci | belirteci Yenile | yetkilendirme kodu |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [Yetkilendirme kodu akışı](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [İstemci kimlik bilgileri](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x (yalnızca uygulama) |               |                    |
| [Cihaz kod akışı](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [Örtük akış](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [On-behalf-of akışı](v2-oauth2-on-behalf-of-flow.md)                                | erişim belirteci        | x        | x            | x             |                    |
| [Kullanıcı adı/parola](v2-oauth-ropc.md) (ropc)                                       | Kullanıcı adı & parola | x        | x            | x             |                    |
| [Karma OıDC akışı](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [Belirteç satın alma yenileme](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | belirteci Yenile       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>Etkileşimli ve etkileşimli olmayan kimlik doğrulaması

Bu akışların bazıları hem etkileşimli hem de etkileşimli olmayan belirteç alımını destekler.

  - **Etkileşimli** , kullanıcıdan giriş istenmeyeceği anlamına gelir. Örneğin, kullanıcıdan oturum açmasını, Multi-Factor Authentication (MFA) gerçekleştirmesini veya kaynaklara ek izin vermesini isteme.
  - **Etkileşimli olmayan** veya *sessiz*, kimlik doğrulama, oturum açma sunucusunun kullanıcıdan ek bilgi *istemeyen* bir şekilde belirteç edinmeye çalışır.

MSAL tabanlı uygulamanız öncelikle bir belirteci *sessizce* ve etkileşimli olmayan yöntem başarısız olursa etkileşimli olarak almayı denemelidir. Bu model hakkında daha fazla bilgi için bkz. [Microsoft kimlik doğrulama kitaplığı 'nı (msal) kullanarak belirteçleri alma ve önbelleğe](msal-acquire-cache-tokens.md)alma.

## <a name="authorization-code"></a>Yetkilendirme kodu

[OAuth 2 yetkilendirme kodu verme](v2-oauth2-auth-code-flow.md) , Web API 'leri gibi korumalı kaynaklara erişim kazanmak için bir cihaza yüklenen uygulamalarda kullanılabilir. Bu, mobil ve Masaüstü uygulamalarınıza oturum açma ve API erişimi eklemenize olanak tanır.

Kullanıcılar Web uygulamalarında (Web siteleri) oturum açtığında, Web uygulaması bir yetkilendirme kodu alır. Yetkilendirme kodu, Web API 'Lerini çağırmak için bir belirteç almak üzere kullanılır.

![Yetkilendirme kod akışı diyagramı](media/msal-authentication-flows/authorization-code.png)

Önceki diyagramda, uygulama:

1. Erişim belirteci için kullanılan bir yetkilendirme kodu ister.
2. Bir Web API 'sini çağırmak için erişim belirtecini kullanır.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

- Bir belirteci kullanmak için yalnızca bir kez yetkilendirme kodu kullanabilirsiniz. Protokol standart belirtimi tarafından açıkça yasaklanmış olduğundan, belirteci aynı yetkilendirme koduyla birden çok kez almaya çalışmayın. Kodu kasıtlı olarak veya bir Framework 'ün sizin için de yaptığı farkında olmadığınız durumlarda kodu birkaç kez kullandıysanız şu hatayı alırsınız:

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>İstemci kimlik bilgileri

[OAuth 2 istemci kimlik bilgileri akışı](v2-oauth2-client-creds-grant-flow.md) , bir uygulamanın kimliğini kullanarak Web 'de barındırılan kaynaklara erişmenizi sağlar. Kullanıcının anında etkileşime geçmesi gerekmeyen ve arka planda çalışması gereken sunucular arası etkileşimler için genellikle bu izin türü kullanılır. Bu tür uygulamalar genellikle daemon veya hizmet hesabı olarak adlandırılır.

İstemci kimlik bilgileri verme akışı, bir Web hizmetinin (gizli bir istemci) başka bir Web hizmetini çağırırken kimlik doğrulaması yapmak yerine kendi kimlik bilgilerini kullanmasına izin verir. Bu senaryoda, istemci genellikle bir orta katman Web hizmeti, bir Daemon hizmeti veya bir Web sitesidir. Daha yüksek bir güvence düzeyi için, Microsoft Identity platformu, çağıran hizmetin kimlik bilgileri olarak bir sertifika (paylaşılan gizlilik yerine) kullanmasına de olanak tanır.

> [!NOTE]
> Gizli istemci akışı, yalnızca genel istemci uygulamalarını desteklediklerinden UWP, Xamarin. iOS ve Xamarin. Android gibi mobil platformlarda kullanılabilir değildir. Ortak istemci uygulamaları, uygulamanın kimliğini kimlik sağlayıcısına nasıl kanıtlayabileceğinizi bilmez. Bir sertifika dağıtarak Web uygulaması veya Web API 'SI arka uçları üzerinde güvenli bir bağlantı elde edilebilir.

### <a name="application-secrets"></a>Uygulama gizli dizileri

![Parola ile gizli istemci diyagramı](media/msal-authentication-flows/confidential-client-password.png)

Önceki diyagramda, uygulama:

1. Uygulama gizli anahtarı veya parola kimlik bilgilerini kullanarak bir belirteç alır.
2. , Kaynak isteklerini yapmak için belirtecini kullanır.

### <a name="certificates"></a>Sertifikalar

![Sertifika ile gizli istemci diyagramı](media/msal-authentication-flows/confidential-client-certificate.png)

Önceki diyagramda, uygulama:

1. Sertifika kimlik bilgilerini kullanarak bir belirteç alır.
2. , Kaynak isteklerini yapmak için belirtecini kullanır.

Bu istemci kimlik bilgilerinin olması gerekir:

- Azure AD 'ye kayıtlı.
- Kodunuzda gizli istemci uygulama nesnesi oluşturulurken geçirilir.

## <a name="device-code"></a>Cihaz kodu

[OAuth 2 cihaz kodu akışı](v2-oauth2-device-code.md) , kullanıcıların akıllı TV 'Ler, IoT cihazları ve yazıcılar gibi giriş kısıtlı cihazlarda oturum açmasına olanak tanır. Azure AD ile etkileşimli kimlik doğrulaması için bir Web tarayıcısı gerekir. Cihazın veya işletim sisteminin bir Web tarayıcısı sunmazsa, cihaz kodu akışı kullanıcının etkileşimli olarak oturum açmak için bir bilgisayar veya cep telefonu gibi başka bir cihazı kullanmasına olanak sağlar.

Uygulama, cihaz kod akışını kullanarak belirteçleri bu cihazlar ve işletim sistemleri için tasarlanan iki adımlı bir işlemle edinir. Bu tür uygulamalara örnekler, IoT cihazlarında ve komut satırı arabirimi (CLı) araçlarında çalışan bunları içerir.

![Cihaz kod akışı diyagramı](media/msal-authentication-flows/device-code.png)

Yukarıdaki diyagramda:

1. Kullanıcı kimlik doğrulaması gerekli olduğunda, uygulama bir kod sağlar ve kullanıcıdan bir URL 'YI (örneğin,) ziyaret etmek için internet 'e bağlı bir akıllı telefonu gibi başka bir cihazı kullanmasını ister `https://microsoft.com/devicelogin` . Daha sonra kullanıcıya kodu girmesi istenir ve gerekirse izin istemleri ve [çok faktörlü kimlik doğrulaması](../authentication/concept-mfa-howitworks.md)dahil olmak üzere normal bir kimlik doğrulama deneyimine devam edilir.
1. Başarılı kimlik doğrulamasından sonra, komut satırı uygulaması gerekli belirteçleri bir arka kanal aracılığıyla alır ve bunları, gereken Web API çağrılarını gerçekleştirmek için kullanır.

### <a name="constraints"></a>Kısıtlamalar

- Cihaz kod akışı yalnızca genel istemci uygulamalarında kullanılabilir.
- Ortak istemci uygulamasını oluştururken geçirilen yetkili aşağıdakilerden biri olmalıdır:
  - Kiracıda, `https://login.microsoftonline.com/{tenant}/,` `{tenant}` Kiracı kimliğini temsıl eden GUID veya kiracı ile ilişkili bir etki alanı adı olan biçimindedir.
  - Formdaki iş ve okul hesapları için `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>Örtük izin

[OAuth 2 örtülü izin verme](v2-oauth2-implicit-grant-flow.md) akışı, uygulamanın bir arka uç sunucu kimlik bilgisi değişimi gerçekleştirmeden Microsoft Identity platformundan belirteç almasına izin verir. Bu akış, uygulamanın kullanıcıya oturum açmasını, oturum korumasını ve diğer Web API 'Leri için, tüm istemci JavaScript kodu içindeki belirteçleri almasını sağlar.

![Örtük verme akışı diyagramı](media/msal-authentication-flows/implicit-grant.svg)

Birçok modern web uygulaması, istemci tarafı, JavaScript 'te yazılmış tek sayfalı uygulamalar (SPA) veya angular, Vue.js ve React.js gibi bir SPA çerçevesi olarak oluşturulmuştur. Bu uygulamalar bir Web tarayıcısında çalışır ve geleneksel sunucu tarafı Web uygulamalarından farklı kimlik doğrulama özelliklerine sahiptir. Microsoft Identity platformu, tek sayfalı uygulamaların kullanıcılara oturum açmasını sağlar ve dolaylı verme akışını kullanarak arka uç hizmetlerine veya Web API 'Lerine erişim belirteçleri alır. Örtük akış, uygulamanın kimliği doğrulanmış kullanıcıyı temsil etmesi için kimlik belirteçleri almasına izin verir ve ayrıca korumalı API 'Leri çağırmak için gereken belirteçlere erişim sağlar.

Bu kimlik doğrulama akışı, yerel platformlarla etkileşim için daha fazla özellik gerektirdiğinden, elektron veya React-Native gibi platformlar arası JavaScript çerçeveleri kullanan uygulama senaryoları içermez.

Örtük akış modu aracılığıyla verilen belirteçlerin, tarayıcıya URL tarafından döndürüldüğünden (ya da) bir **uzunluk sınırlaması** vardır `response_mode` `query` `fragment` . Bazı tarayıcılar, tarayıcı çubuğundaki URL 'nin uzunluğunu sınırlar ve çok uzunsa başarısız olur. Bu nedenle, bu örtük akış belirteçleri `groups` veya `wids` talep içermez.

## <a name="on-behalf-of"></a>Adına-

[OAuth 2 adına sahip kimlik doğrulama akışı](v2-oauth2-on-behalf-of-flow.md) akışı, bir uygulama bir hizmeti veya Web API 'sini çağıran bir hizmetin başka bir hizmet veya Web API 'si çağırması gerektiği durumlarda kullanılır. Amaç, temsilci kullanıcı kimliğini ve izinleri istek zinciri aracılığıyla yaymanız önerilir. Orta katman hizmetin, aşağı akış hizmetine kimliği doğrulanmış istekler yapması için, Kullanıcı *adına* Microsoft Identity platformundan bir erişim belirtecinin güvenliğini sağlaması gerekir.

![Şirket adına akış diyagramı](media/msal-authentication-flows/on-behalf-of.png)

Yukarıdaki diyagramda:

1. Uygulama, Web API 'SI için bir erişim belirteci alır.
2. İstemci (Web, Masaüstü, mobil veya tek sayfalı uygulama) korumalı bir Web API 'sini çağırır ve erişim belirtecini HTTP isteğinin kimlik doğrulama üstbilgisinde bir taşıyıcı belirteci olarak ekler. Web API 'SI kullanıcının kimliğini doğrular.
3. İstemci, Web API 'sini çağırdığında, Web API 'SI Kullanıcı adına başka bir belirteç ister.
4. Korunan Web API 'si, kullanıcının adına bir aşağı akış Web API 'SI çağırmak için bu belirteci kullanır. Web API 'SI daha sonra diğer aşağı akış API 'Leri için belirteçler isteyebilir (ancak hala aynı kullanıcı adına).

## <a name="usernamepassword"></a>Kullanıcı adı/parola

[OAuth 2 kaynak sahibi parola kimlik bilgileri](v2-oauth-ropc.md) (ropc) verme izni, bir uygulamanın kullanıcı parolasını doğrudan işlemesini sağlayarak oturum açmasına izin verir. Masaüstü uygulamanızda Kullanıcı adı/parola akışını sessizce bir belirteç elde edebilirsiniz. Uygulama kullanılırken hiçbir Kullanıcı arabirimi gerekli değildir.

![Kullanıcı adı/parola akışı diyagramı](media/msal-authentication-flows/username-password.png)

Önceki diyagramda, uygulama:

1. Kimlik sağlayıcısına Kullanıcı adı ve parola göndererek bir belirteç alır.
2. Belirtecini kullanarak bir Web API 'SI çağırır.

> [!WARNING]
> Bu akış önerilmez. Yüksek derecede güven ve kimlik bilgisi pozlaması gerektirir. Bu akışı *yalnızca* daha güvenli akışlar kullanılmıyorsa kullanmanız gerekir. Daha fazla bilgi için, bkz. [parola büyüyen soruna neden olan çözüm nedir?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Windows etki alanına katılmış makinelerde sessizce belirteç almak için tercih edilen akış, [Windows kimlik doğrulaması ' nı tümleştirilmiştir](#integrated-windows-authentication). Diğer durumlarda, [cihaz kod akışını](#device-code)kullanın.

Kullanıcı adı/parola akışı DevOps gibi bazı senaryolarda yararlı olsa da, kendi Kullanıcı arabiriminizi sağladığınız Etkileşimli senaryolarda Kullanıcı adı/parola kullanmak istiyorsanız bunu önleyin.

Kullanıcı adı/parola kullanarak:

- Çok faktörlü kimlik doğrulaması gerçekleştirmesi gereken kullanıcılar hiçbir etkileşim olmadığından oturum açamaz.
- Kullanıcılar çoklu oturum açma yapamaz.

### <a name="constraints"></a>Kısıtlamalar

[Tümleşik Windows kimlik doğrulaması kısıtlamalarından](#integrated-windows-authentication)ayrı olarak aşağıdaki kısıtlamalar da geçerlidir:

- Kullanıcı adı/parola akışı, koşullu erişim ve çok faktörlü kimlik doğrulamasıyla uyumlu değildir. Sonuç olarak, uygulamanız kiracı yöneticisinin çok faktörlü kimlik doğrulaması gerektirdiği bir Azure AD kiracısında çalışıyorsa, bu akışı kullanamazsınız. Birçok kuruluş bu şekilde yapılır.
- ROPC yalnızca iş ve okul hesaplarında çalışır. Microsoft hesapları için ROPC kullanamazsınız (MSA).
- Flow, .NET masaüstü ve .NET Core 'ta mevcuttur, ancak Evrensel Windows Platformu.
- Azure AD B2C, ROPC akışı yalnızca yerel hesaplar için kullanılır. MSAL.NET ve Azure AD B2C 'de ROPC hakkında bilgi için bkz. [ROPC 'yi Azure AD B2C kullanma](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc).

## <a name="integrated-windows-authentication"></a>Tümleşik Windows Kimlik Doğrulaması

MSAL, etki alanına katılmış veya Azure AD 'ye katılmış Windows bilgisayarında çalışan masaüstü ve mobil uygulamalar için tümleşik Windows kimlik doğrulamasını (ıWA) destekler. IWA kullanarak bu uygulamalar kullanıcı ARABIRIMI etkileşimi gerektirmeden sessizce bir belirteç alabilir.

![Tümleşik Windows kimlik doğrulaması diyagramı](media/msal-authentication-flows/integrated-windows-authentication.png)

Önceki diyagramda, uygulama:

1. Tümleşik Windows kimlik doğrulamasını kullanarak bir belirteç alır.
2. , Kaynak isteklerini yapmak için belirtecini kullanır.

### <a name="constraints"></a>Kısıtlamalar

Tümleşik Windows kimlik doğrulaması (ıWA) *yalnızca* Federal kullanıcıları destekler-Active Directory oluşturulan ve Azure AD tarafından desteklenen kullanıcılar. Active Directory yedekleme (yönetilen kullanıcılar) olmadan doğrudan Azure AD 'de oluşturulan kullanıcılar bu kimlik doğrulama akışını kullanamaz. Bu sınırlama, [Kullanıcı adı/parola akışını](#usernamepassword)etkilemez.

IWA .NET Framework, .NET Core ve Evrensel Windows Platformu uygulamalar içindir.

IWA Multi-Factor Authentication 'ı atlamaz. Multi-Factor Authentication yapılandırılırsa, çok faktörlü kimlik doğrulama sınaması gerekliyse ıWA başarısız olabilir. Multi-Factor Authentication Kullanıcı etkileşimi gerektirir.

Kimlik sağlayıcısı 'nın gerçekleştirilecek iki öğeli kimlik doğrulaması istediğinde bunu denetkalmazsınız. Kiracı Yöneticisi. Genellikle, farklı bir ülke/bölgeden oturum açtığınızda iki öğeli kimlik doğrulaması gerekir. bir şirket ağına VPN aracılığıyla bağlı değilseniz ve bazen VPN aracılığıyla bağlandığınızda bile. Azure AD, iki öğeli kimlik doğrulamasının gerekli olup olmadığını sürekli olarak öğrenmek için AI 'yi kullanır. IWA başarısız olursa [etkileşimli bir Kullanıcı istemine](#interactive-and-non-interactive-authentication)geri dönebilmelisiniz.

Ortak istemci uygulamasını oluştururken geçirilen yetkili aşağıdakilerden biri olmalıdır:

- Kiracıda, `https://login.microsoftonline.com/{tenant}/,` `{tenant}` Kiracı kimliğini temsıl eden GUID veya kiracı ile ilişkili bir etki alanı adı olan biçimindedir.
- Herhangi bir iş ve okul hesabı ( `https://login.microsoftonline.com/organizations/` ) için. Microsoft kişisel hesapları (MSA) desteklenmez; `/common` veya `/consumers` kiracılarını kullanamazsınız.

IWA sessiz akış olduğundan, aşağıdakilerden biri doğru olmalıdır:

- Uygulamanızın kullanıcısının, uygulamayı kullanmak için önceden verilmiş olması gerekir.
- Kiracı yöneticisinin uygulamayı kullanabilmesi için Kiracıdaki tüm kullanıcılara daha önce sahip olması gerekir.

Bu, aşağıdakilerden birinin doğru olduğu anlamına gelir:

- Bir geliştirici için Azure portal bir geliştirici **seçmiş olursunuz** .
- Bir kiracı yöneticisi, Azure portal uygulama kaydının **API izinleri** sekmesinde **{kiracı etki alanı} için izin verme/iptal etme onayı** ' nı seçti (bkz. [Web API 'nize erişmek için izin ekleme](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)).
- Kullanıcıların uygulamayı kabul etmek için bir yol sağladınız; bkz. [bireysel kullanıcı Izni isteme](v2-permissions-and-consent.md#requesting-individual-user-consent).
- Kiracı yöneticisinin uygulamayı kabul etmek için bir yol sağladınız; bkz. [yönetici onayı](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

IWA Flow, .NET Masaüstü, .NET Core ve Windows Evrensel platform uygulamaları için etkinleştirilmiştir.

Onay hakkında daha fazla bilgi için bkz. [v 2.0 izinleri ve onayı](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Sonraki adımlar

Artık Microsoft kimlik doğrulama kitaplığı (MSAL) tarafından desteklenen kimlik doğrulama akışlarını gözden geçirdikten sonra, bu akışlarda kullanılan belirteçleri alma ve önbelleğe alma hakkında bilgi edinin:

[Microsoft kimlik doğrulama kitaplığı (MSAL) kullanarak belirteçleri alma ve önbelleğe alma](msal-acquire-cache-tokens.md)
