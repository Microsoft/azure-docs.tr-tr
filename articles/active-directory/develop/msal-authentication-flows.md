---
title: Kimlik doğrulama akışları (Microsoft kimlik doğrulama kitaplığı)
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı (MSAL) tarafından kullanılan kimlik doğrulama akışları ve izinler hakkında bilgi edinin.
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
ms.date: 10/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55d618a24b957fedb6fc2af3e75b7a7d2bd23d96
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473820"
---
# <a name="authentication-flows"></a>Kimlik doğrulama akışları

Bu makalede, Microsoft kimlik doğrulama kitaplığı (MSAL) tarafından sunulan farklı kimlik doğrulama akışları açıklanmaktadır.  Bu akışlar, çeşitli farklı uygulama senaryolarında kullanılabilir.

| Akış | Açıklama | Kullanıldığı yer|  
| ---- | ----------- | ------- | 
| [Etkileşimli](#interactive) | Bir tarayıcı veya açılır pencere aracılığıyla kullanıcıdan kimlik bilgilerini isteyen etkileşimli bir işlem aracılığıyla belirteci alır. | [Masaüstü uygulamaları](scenario-desktop-overview.md), [mobil uygulamalar](scenario-mobile-overview.md) |
| [Örtük izin](#implicit-grant) | Uygulamanın arka uç sunucu kimlik bilgisi değişimi gerçekleştirmeden belirteçleri almasına izin verir. Bu, uygulamanın kullanıcıya oturum açmasını, oturum korumasını ve diğer Web API 'Lerine, tüm istemci JavaScript kodu içindeki belirteçleri almasına olanak tanır.| [Tek sayfalı uygulamalar (SPA)](scenario-spa-overview.md) |
| [Yetkilendirme kodu](#authorization-code) | Web API 'Leri gibi korumalı kaynaklara erişim kazanmak için bir cihaza yüklenen uygulamalarda kullanılır. Bu, mobil ve Masaüstü uygulamalarınıza oturum açma ve API erişimi eklemenize olanak tanır. | [Masaüstü uygulamaları](scenario-desktop-overview.md), [mobil uygulamalar](scenario-mobile-overview.md), [Web uygulamaları](scenario-web-app-call-api-overview.md) | 
| [Adına-](#on-behalf-of) | Bir uygulama bir hizmet veya Web API 'sini çağırır, bu da başka bir hizmet veya Web API çağrısı gerektirir. Amaç, temsilci kullanıcı kimliğini ve izinleri istek zinciri aracılığıyla yaymanız önerilir. | [Web API'leri](scenario-web-api-call-api-overview.md) |
| [İstemci kimlik bilgileri](#client-credentials) | Bir uygulamanın kimliğini kullanarak Web 'de barındırılan kaynaklara erişmenizi sağlar. Yaygın olarak, bir kullanıcıyla etkileşim kurmadan, arka planda çalışması gereken sunucu-sunucu etkileşimleri için kullanılır. | [Daemon uygulamaları](scenario-daemon-overview.md) |
| [Cihaz kodu](#device-code) | Kullanıcıların, akıllı TV, IoT cihazı veya yazıcı gibi giriş kısıtlı cihazlarda oturum açmasına olanak sağlar. | [Masaüstü/mobil uygulamalar](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Tümleşik Windows kimlik doğrulaması](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Etki alanı veya Azure Active Directory (Azure AD) ile Birleşik bilgisayarlardaki uygulamaların sessizce bir belirteç almasına izin verir (kullanıcıdan herhangi bir kullanıcı ARABIRIMI etkileşimi olmadan).| [Masaüstü/mobil uygulamalar](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Kullanıcı adı/parola](scenario-desktop-acquire-token.md#username--password) | Bir uygulamanın, parolasını doğrudan işleyerek kullanıcının oturum açmasına izin verir. Bu akış önerilmez. | [Masaüstü/mobil uygulamalar](scenario-desktop-acquire-token.md#username--password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>Her akışın belirteçleri ve kodları nasıl yayar
 
İstemcinizin nasıl oluşturulduğuna bağlı olarak, Microsoft Identity platform tarafından desteklenen kimlik doğrulama akışlarının birini (veya birkaçını) kullanabilir.  Bu akışlar çeşitli belirteçler (id_tokens, yenileme belirteçleri, erişim belirteçleri) ve yetkilendirme kodlarını oluşturabilir ve bunların çalışmasını sağlamak için farklı belirteçler gerektirebilir. Bu grafik genel bir bakış için:
 
|Akış | Gerektirmeyen | id_token | erişim belirteci | belirteci Yenile | yetkilendirme kodu | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Yetkilendirme kodu akışı](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Örtük akış](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Karma OıDC akışı](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Belirteç satın alma yenileme](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | belirteci Yenile | x | x | x| |
|[On-behalf-of akışı](v2-oauth2-on-behalf-of-flow.md) | erişim belirteci| x| x| x| |
|[Cihaz kod akışı](v2-oauth2-device-code.md) | | x| x| x| |
|[İstemci kimlik bilgileri](v2-oauth2-client-creds-grant-flow.md) | | | x (yalnızca uygulama)| | |
 
Örtülü mod aracılığıyla yayınlanan belirteçlerin, URL aracılığıyla tarayıcıya geri geçirilmesi nedeniyle bir uzunluk sınırlaması vardır (`response_mode` `query` veya `fragment`).  Bazı tarayıcıların, tarayıcı çubuğuna koyabileceğiniz ve çok uzun olduğunda başarısız olan URL 'nin boyutunda bir sınırı vardır.  Bu nedenle, bu belirteçlerin `groups` veya `wids` talepleri yoktur.

## <a name="interactive"></a>Etkileşimli

MSAL, kullanıcıdan oturum açmasını etkileşimli olarak isteme ve bu kimlik bilgilerini kullanarak bir belirteç alma özelliğini destekler.

![Etkileşimli akış diyagramı](media/msal-authentication-flows/interactive.png)

Belirli platformlarda belirteçleri etkileşimli olarak almak için MSAL.NET kullanma hakkında daha fazla bilgi için bkz.:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Evrensel Windows Platformu](msal-net-uwp-considerations.md)

MSAL. js ' deki etkileşimli çağrılar hakkında daha fazla bilgi için bkz. [msal. js etkileşimli Isteklerindeki istem davranışı](msal-js-prompt-behavior.md).

## <a name="implicit-grant"></a>Örtük izin

MSAL, bir arka uç sunucu kimlik bilgisi alışverişi yapmadan uygulamanın Microsoft Identity platformundan belirteç almasına izin veren [OAuth 2 örtülü izin akışını](v2-oauth2-implicit-grant-flow.md)destekler. Bu, uygulamanın kullanıcıya oturum açmasını, oturum korumasını ve diğer Web API 'Lerine, tüm istemci JavaScript kodu içindeki belirteçleri almasına olanak tanır.

![Örtük verme akışı diyagramı](media/msal-authentication-flows/implicit-grant.svg)

Birçok modern web uygulaması, istemci tarafı, tek sayfalı uygulamalar, JavaScript veya angular, Vue. js ve tepki. js gibi bir SPA çerçevesi kullanılarak yazılmış şekilde oluşturulmuştur. Bu uygulamalar bir Web tarayıcısında çalışır ve geleneksel sunucu tarafı Web uygulamalarından farklı kimlik doğrulama özelliklerine sahiptir. Microsoft Identity platformu, tek sayfalı uygulamaların kullanıcılara oturum açmasını sağlar ve dolaylı verme akışını kullanarak arka uç hizmetlerine veya Web API 'Lerine erişim belirteçleri alır. Örtük akış, uygulamanın kimliği doğrulanmış kullanıcıyı temsil etmesi için kimlik belirteçleri almasına izin verir ve ayrıca korumalı API 'Leri çağırmak için gereken belirteçlere erişim sağlar.

Bu kimlik doğrulama akışı, yerel platformlarla etkileşim için daha fazla özellik gerektirdiğinden, elektron ve tepki verme gibi platformlar arası JavaScript çerçeveleri kullanan uygulama senaryoları içermez.

## <a name="authorization-code"></a>yetkilendirme kodu

MSAL, [OAuth 2 yetkilendirme kodu vermeyi](v2-oauth2-auth-code-flow.md)destekler. Bu izin, Web API 'Leri gibi korumalı kaynaklara erişim kazanmak için bir cihaza yüklenen uygulamalarda kullanılabilir. Bu, mobil ve Masaüstü uygulamalarınıza oturum açma ve API erişimi eklemenize olanak tanır. 

Kullanıcılar Web uygulamalarında (Web siteleri) oturum açtığında, Web uygulaması bir yetkilendirme kodu alır.  Yetkilendirme kodu, Web API 'Lerini çağırmak için bir belirteç almak üzere kullanılır. ASP.NET ve ASP.NET Core Web Apps 'te, tek `AcquireTokenByAuthorizationCode` hedefi belirteç önbelleğine bir belirteç eklemektir. Belirteç daha sonra uygulama tarafından (genellikle `AcquireTokenSilent`kullanarak bir API için belirteç almış olan denetleyicilerde) kullanılabilir.

![Yetkilendirme kod akışı diyagramı](media/msal-authentication-flows/authorization-code.png)

Önceki diyagramda, uygulama:

1. Erişim belirteci için kullanılan bir yetkilendirme kodu ister.
2. Bir Web API 'sini çağırmak için erişim belirtecini kullanır.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

- Bir belirteci kullanmak için yalnızca bir kez yetkilendirme kodu kullanabilirsiniz. Aynı yetkilendirme koduyla bir belirteci birden çok kez edinmeyi denemeyin (protokol standart belirtimi tarafından açıkça yasaklanmıştır). Kodu kasıtlı olarak birkaç kez kullandıysanız veya bir Framework 'ün sizin için de yaptığı farkında olmadığınız için şu hatayı alırsınız: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Bir ASP.NET veya ASP.NET Core uygulaması yazıyorsanız, çerçeveye zaten yetkilendirme kodu kullandığınızı söylüyorsanız bu durum oluşabilir. Bunun için, `AuthorizationCodeReceived` olay işleyicisinin `context.HandleCodeRedemption()` yöntemini çağırmanız gerekir.

- Erişim belirtecinin ASP.NET ile paylaşılmasından kaçının, bu da artımlı izin doğru şekilde oluşmasını engelleyebilir. Daha fazla bilgi için bkz. [sorun #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>Adına-

MSAL, [OAuth 2. adına kimlik doğrulama akışını](v2-oauth2-on-behalf-of-flow.md)destekler.  Bu akış, bir uygulama bir hizmet veya Web API 'SI istediğinde kullanılır, bu da başka bir hizmet veya Web API 'SI çağırmalıdır. Amaç, temsilci kullanıcı kimliğini ve izinleri istek zinciri aracılığıyla yaymanız önerilir. Orta katman hizmetin, aşağı akış hizmetine kimliği doğrulanmış istekleri yapması için Kullanıcı adına Microsoft Identity platformundan bir erişim belirtecinin güvenli hale getirme ihtiyacı vardır.

![Şirket adına akış diyagramı](media/msal-authentication-flows/on-behalf-of.png)

Önceki diyagramda:

1. Uygulama, Web API 'SI için bir erişim belirteci alır.
2. İstemci (Web, Masaüstü, mobil veya tek sayfalı uygulama) korumalı bir Web API 'sini çağırır ve erişim belirtecini HTTP isteğinin kimlik doğrulama üstbilgisinde bir taşıyıcı belirteci olarak ekler. Web API 'SI kullanıcının kimliğini doğrular.
3. İstemci, Web API 'sini çağırdığında, Web API 'SI Kullanıcı adına başka bir belirteç ister.  
4. Korunan Web API 'si, kullanıcının adına bir aşağı akış Web API 'SI çağırmak için bu belirteci kullanır.  Web API 'SI daha sonra diğer aşağı akış API 'Leri için belirteçler isteyebilir (ancak hala aynı kullanıcı adına).

## <a name="client-credentials"></a>İstemci kimlik bilgileri

MSAL, [OAuth 2 istemci kimlik bilgileri akışını](v2-oauth2-client-creds-grant-flow.md)destekler. Bu akış, bir uygulamanın kimliğini kullanarak Web 'de barındırılan kaynaklara erişmenizi sağlar. Bu tür bir izin, genellikle bir kullanıcıyla etkileşimde bulunmadan, arka planda çalışması gereken sunucu-sunucu etkileşimleri için kullanılır. Bu tür uygulamalar genellikle Daemon 'ları veya hizmet hesapları olarak adlandırılır. 

İstemci kimlik bilgileri verme akışı, bir Web hizmetinin (gizli bir istemci) başka bir Web hizmetini çağırırken kimlik doğrulaması yapmak yerine kendi kimlik bilgilerini kullanmasına izin verir. Bu senaryoda, istemci genellikle bir orta katman Web hizmeti, bir Daemon hizmeti veya bir Web sitesidir. Daha yüksek bir güvence düzeyi için, Microsoft Identity platformu, çağıran hizmetin kimlik bilgileri olarak bir sertifika (paylaşılan gizlilik yerine) kullanmasına de olanak tanır.

> [!NOTE]
> Gizli istemci akışı, yalnızca genel istemci uygulamalarını destekledikleri için mobil platformlarda (UWP, Xamarin. iOS ve Xamarin. Android) kullanılabilir değildir. Ortak istemci uygulamaları, uygulamanın kimliğini kimlik sağlayıcısına nasıl kanıtlayabileceğinizi bilmez. Bir sertifika dağıtarak Web uygulaması veya Web API 'SI arka uçları üzerinde güvenli bir bağlantı elde edilebilir.

MSAL.NET iki tür istemci kimlik bilgilerini destekler. Bu istemci kimlik bilgilerinin Azure AD 'ye kayıtlı olması gerekir. Kimlik bilgileri kodunuzda gizli istemci uygulamasının oluşturuculara geçirilir.

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
- Kodunuzda gizli istemci uygulamasının oluşturulması sırasında geçirilir.

## <a name="device-code"></a>Cihaz kodu

MSAL, kullanıcıların akıllı TV, IoT cihazı veya yazıcı gibi giriş kısıtlı cihazlarda oturum açmasına olanak tanıyan [OAuth 2 cihaz kod akışını](v2-oauth2-device-code.md)destekler. Azure AD ile etkileşimli kimlik doğrulaması için bir Web tarayıcısı gerekir. Cihaz kod akışı, kullanıcının, cihazın veya işletim sisteminin bir Web tarayıcısı sağlamayan etkileşimli olarak oturum açmasını sağlamak için başka bir cihaz (örneğin, başka bir bilgisayar veya cep telefonu) kullanmasına olanak sağlar.

Uygulama, cihaz kod akışını kullanarak belirteçleri, özellikle bu cihazlar veya işletim sistemleri için tasarlanan iki adımlı bir işlemle edinir. IoT cihazlarında veya komut satırı araçlarında (CLı) çalışan bu uygulamalara örnek olarak verilebilir. 

![Cihaz kod akışı diyagramı](media/msal-authentication-flows/device-code.png)

Önceki diyagramda:

1. Kullanıcı kimlik doğrulaması gerekli olduğunda, uygulama bir kod sağlar ve kullanıcıdan bir URL 'ye (örneğin, internet 'e bağlı bir akıllı telefon) gitmesini ister (örneğin, https://microsoft.com/devicelogin)). Daha sonra kullanıcıya kodu girmesi istenir ve gerekirse onay istemleri ve çok faktörlü kimlik doğrulaması dahil olmak üzere normal bir kimlik doğrulama deneyimi üzerinden ilerler.

2. Başarılı kimlik doğrulamasından sonra, komut satırı uygulaması gerekli belirteçleri bir arka kanal aracılığıyla alır ve bunları, gereken Web API çağrılarını gerçekleştirmek için kullanır.

### <a name="constraints"></a>Kısıtlamalar

- Cihaz kodu akışı yalnızca genel istemci uygulamalarında kullanılabilir.
- Ortak istemci uygulamasını oluştururken geçirilen yetkili aşağıdakilerden biri olmalıdır:
  - Kiralanan (form `https://login.microsoftonline.com/{tenant}/`, `{tenant}` kiracı KIMLIĞINI temsil eden GUID veya kiracı ile ilişkili bir etki alanı).
  - Her türlü iş ve okul hesabı için (`https://login.microsoftonline.com/organizations/`).
- Microsoft kişisel hesapları henüz Azure AD v 2.0 uç noktası tarafından desteklenmemektedir (`/common` veya `/consumers` kiracılarını kullanamazsınız).

## <a name="integrated-windows-authentication"></a>Tümleşik Windows kimlik doğrulaması

MSAL, masaüstü veya etki alanına katılmış veya Azure AD 'ye katılmış Windows bilgisayarında çalışan mobil uygulamalar için tümleşik Windows kimlik doğrulamasını (ıWA) destekler. IWA 'yi kullanarak bu uygulamalar sessizce (kullanıcıdan herhangi bir kullanıcı ARABIRIMI etkileşimi olmadan) belirteç alabilir. 

![Tümleşik Windows kimlik doğrulaması diyagramı](media/msal-authentication-flows/integrated-windows-authentication.png)

Önceki diyagramda, uygulama:

1. Tümleşik Windows kimlik doğrulamasını kullanarak bir belirteç alır.
2. , Kaynak isteklerini yapmak için belirtecini kullanır.

### <a name="constraints"></a>Kısıtlamalar

IWA yalnızca federe kullanıcıları destekler, bu, Active Directory içinde oluşturulan ve Azure AD tarafından desteklenen anlamına gelir. Active Directory yedekleme (yönetilen kullanıcılar) olmadan doğrudan Azure AD 'de oluşturulan kullanıcılar bu kimlik doğrulama akışını kullanamaz. Bu sınırlama, [Kullanıcı adı/parola akışını](#usernamepassword)etkilemez.

IWA, .NET Framework, .NET Core ve Evrensel Windows Platformu platformları için yazılan uygulamalar içindir.

IWA Multi-Factor Authentication 'ı atlamaz. Multi-Factor Authentication yapılandırılırsa, çok faktörlü kimlik doğrulama sınaması gerekliyse ıWA başarısız olabilir. Multi-Factor Authentication Kullanıcı etkileşimi gerektirir.

Kimlik sağlayıcısı 'nın gerçekleştirilecek iki öğeli kimlik doğrulaması istediğinde bunu denetkalmazsınız. Kiracı Yöneticisi. Genellikle, farklı bir ülkede oturum açtığınızda iki öğeli kimlik doğrulaması gerekir, VPN aracılığıyla bir kurumsal ağa bağlı olmadığınız zaman ve bazen VPN aracılığıyla bağlı olduğunuzda bile. Azure AD, iki öğeli kimlik doğrulamasının gerekli olup olmadığını sürekli olarak öğrenmek için AI 'yi kullanır. IWA başarısız olursa, bir [etkileşimli kullanıcı istemi] (#interactive) öğesine geri dönebilmelisiniz.

Ortak istemci uygulamasını oluştururken geçirilen yetkili aşağıdakilerden biri olmalıdır:
- Kiralanan (form `https://login.microsoftonline.com/{tenant}/`, `tenant` kiracı KIMLIĞINI temsil eden GUID veya kiracı ile ilişkili bir etki alanı).
- Her türlü iş ve okul hesabı için (`https://login.microsoftonline.com/organizations/`). Microsoft kişisel hesapları desteklenmez (`/common` veya `/consumers` kiracılarını kullanamazsınız).

IWA sessiz akış olduğundan, aşağıdakilerden biri doğru olmalıdır:
- Uygulamanızın kullanıcısının, uygulamayı kullanmak için önceden verilmiş olması gerekir. 
- Kiracı yöneticisinin uygulamayı kullanabilmesi için Kiracıdaki tüm kullanıcılara daha önce sahip olması gerekir.

Bu, aşağıdakilerden birinin doğru olduğu anlamına gelir:
- Geliştirici için Azure portal bir **Geliştirici seçmiş olursunuz** .
- Bir kiracı yöneticisi, uygulamaya yönelik kaydın **API izinleri** sekmesinde **{kiracı etki alanı} Için yönetici izni verme/iptal etme** işlemi seçti (bkz. [Web API 'lerine erişim izinleri ekleme](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)).
- Kullanıcıların uygulamayı onaylaması için bir yol sağladınız (bkz. [bireysel kullanıcı Izni isteme](v2-permissions-and-consent.md#requesting-individual-user-consent)).
- Kiracı yöneticisinin uygulamayı kabul etmek için bir yol sağladınız (bkz. [yönetici onayı](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)).

IWA Flow, .NET Masaüstü, .NET Core ve Windows Evrensel platform uygulamaları için etkinleştirilmiştir. .NET Core 'da, .NET Core işletim sisteminden Kullanıcı adları edinemediği için ıWA için Kullanıcı adı sağlamanız gerekir.
  
Onay hakkında daha fazla bilgi için bkz. [v 2.0 izinleri ve onayı](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Kullanıcı adı/parola

MSAL, bir uygulamanın kullanıcı parolasını doğrudan işlemesini sağlayarak oturum açmasına izin veren [OAuth 2 kaynak sahibi parolası kimlik bilgileri verme](v2-oauth-ropc.md)'yi destekler. Masaüstü uygulamanızda Kullanıcı adı/parola akışını sessizce bir belirteç elde edebilirsiniz. Uygulama kullanılırken hiçbir Kullanıcı arabirimi gerekli değildir.

![Kullanıcı adı/parola akışı diyagramı](media/msal-authentication-flows/username-password.png)

Önceki diyagramda, uygulama:

1. Kimlik sağlayıcısına Kullanıcı adı ve parola göndererek bir belirteç alır.
2. Belirtecini kullanarak bir Web API 'SI çağırır.

> [!WARNING]
> Bu akış önerilmez. Yüksek derecede güven ve Kullanıcı pozlaması gerektirir.  Bu akışı yalnızca diğer, daha güvenli ve akış kullanılabilir olduğunda kullanmanız gerekir. Daha fazla bilgi için, bkz. [parola büyüyen soruna neden olan çözüm nedir?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Windows etki alanına katılmış makinelerde sessizce belirteç almak için tercih edilen akış, [Windows kimlik doğrulaması ' nı tümleştirilmiştir](#integrated-windows-authentication). Aksi takdirde, [cihaz kod akışını](#device-code)da kullanabilirsiniz.

Bu, bazı durumlarda (DevOps senaryolarında) yararlı olsa da, kendi Kullanıcı arabiriminizi sağladığınız Etkileşimli senaryolarda Kullanıcı adını/parolayı kullanmak istiyorsanız, kullanmaktan kaçınmaya çalışın. Kullanıcı adı/parola kullanarak:
- Multi-Factor Authentication yapması gereken kullanıcılar oturum açamaz (hiçbir etkileşim yoktur).
- Kullanıcılar çoklu oturum açma yapamaz.

### <a name="constraints"></a>Kısıtlamalar

[Tümleşik Windows kimlik doğrulaması kısıtlamalarından](#integrated-windows-authentication)ayrı olarak aşağıdaki kısıtlamalar da geçerlidir:

- Kullanıcı adı/parola akışı, koşullu erişim ve çok faktörlü kimlik doğrulamasıyla uyumlu değildir. Sonuç olarak, uygulamanız kiracı yöneticisinin çok faktörlü kimlik doğrulaması gerektirdiği bir Azure AD kiracısında çalışıyorsa, bu akışı kullanamazsınız. Birçok kuruluş bu şekilde yapılır.
- Yalnızca iş ve okul hesaplarında (Microsoft hesapları için değil) çalışır.
- Flow, .NET masaüstü ve .NET Core 'ta mevcuttur, ancak Evrensel Windows Platformu.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C özellikleri

MSAL.NET ve Azure AD B2C kullanma hakkında daha fazla bilgi için bkz. [ROPC 'yi Azure AD B2C Ile kullanma (msal.net)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
