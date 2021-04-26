---
title: Uygulama senaryolarına & Microsoft Identity platform kimlik doğrulama akışları | Mavisi
description: Kimlik doğrulama, belirteçleri alma ve korumalı API 'Leri çağırma dahil olmak üzere Microsoft Identity platformu için uygulama senaryoları hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: ffa52805a5e2680d534b2b24a210465cb3fc7cac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557882"
---
# <a name="authentication-flows-and-application-scenarios"></a>Kimlik doğrulama akışları ve uygulama senaryoları

Microsoft Identity platformu, farklı türlerde modern uygulama mimarilerinin kimlik doğrulamasını destekler. Mimarilerin hepsi, [OAuth 2,0 ve OpenID Connect](active-directory-v2-protocols.md)sektör standardı protokollerine dayanır. Uygulamalar, [Microsoft Identity platformu için kimlik doğrulama kitaplıklarını](reference-v2-libraries.md)kullanarak kimliklerin kimliğini doğrular ve korunan API 'lere erişmek için belirteçleri elde edin.

Bu makalede, kimlik doğrulama akışları ve içinde kullanıldıkları uygulama senaryoları açıklanmaktadır.

## <a name="application-categories"></a>Uygulama kategorileri

Belirteçleri aşağıdakiler dahil olmak üzere çeşitli türde uygulamalardan elde edilebilir:

- Web uygulamaları
- Mobil uygulamalar
- Masaüstü uygulamaları
- Web API'leri

Belirteçler Ayrıca, tarayıcıya sahip olmayan veya Nesnelerin İnterneti (IoT) üzerinde çalışan cihazlarda çalışan uygulamalar tarafından da elde edilebilir.

Aşağıdaki bölümlerde uygulama kategorileri açıklanır.

### <a name="protected-resources-vs-client-applications"></a>Korumalı kaynaklara ve istemci uygulamalarına karşı

Kimlik doğrulama senaryoları iki etkinlik içerir:

- **Korumalı bir Web API 'si için güvenlik belirteçleri alınıyor**: Microsoft tarafından geliştirilen ve desteklenen [Microsoft kimlik doğrulama KITAPLıĞı 'nı (msal)](reference-v2-libraries.md)kullanmanızı öneririz.
- **Bir Web API 'sini veya bir Web uygulamasını koruma**: bu kaynakları korumanın bir testi güvenlik belirtecini doğruluyor. Bazı platformlarda Microsoft, [Ara yazılım kitaplıkları](reference-v2-libraries.md)sunmaktadır.

### <a name="with-users-or-without-users"></a>Kullanıcılar veya kullanıcılar olmadan

Çoğu kimlik doğrulama senaryosunda, oturum açmış kullanıcılar adına belirteç elde edin.

![Kullanıcıları olan senaryolar](media/scenarios/scenarios-with-users.svg)

Ancak, Daemon uygulamaları da vardır. Bu senaryolarda, uygulamalar kendi adına belirteçleri Kullanıcı olmadan alır.

![Daemon uygulamalarıyla senaryolar](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Tek sayfalı, ortak istemci ve gizli istemci uygulamaları

Güvenlik belirteçleri birden çok tür uygulama tarafından alınabilir. Bu uygulamalar aşağıdaki üç kategoride ayrılmalıdır. Her biri farklı kitaplıklar ve nesneler ile kullanılır.

- **Tek sayfalı uygulamalar**: Ayrıca, maça 'lar olarak da bilinen, bunlar, belirteçlerin tarayıcıda çalışan bir JavaScript veya TypeScript uygulaması tarafından alındığı Web uygulamalardır. Birçok modern uygulama ön uçta birincil olarak JavaScript 'te yazılmış tek sayfalı bir uygulamadır. Uygulama genellikle angular, yanıt verme veya Vue gibi bir çerçeve kullanır. Tek sayfalı uygulamaları destekleyen tek Microsoft kimlik doğrulama kitaplığı MSAL.js.

- **Ortak istemci uygulamaları**: Bu kategorideki uygulamalar, aşağıdaki türler gibi, her zaman oturum açabilirler:
  - Oturum açmış kullanıcılar adına Web API 'Lerini çağıran masaüstü uygulamaları
  - Mobil uygulamalar
  - IoT üzerinde çalışan cihazlar gibi tarayıcıya sahip olmayan cihazlarda çalışan uygulamalar

- **Gizli istemci uygulamaları**: Bu kategorideki uygulamalar şunlardır:
  - Web API 'sini çağıran Web uygulamaları
  - Web API 'sini çağıran Web API 'Leri
  - Linux Daemon veya Windows hizmeti gibi bir konsol hizmeti olarak uygulandığında bile Daemon uygulamaları

### <a name="sign-in-audience"></a>Hedef kitlenin oturumunu açma

Kullanılabilir kimlik doğrulama akışları, oturum açma izleyicisine bağlı olarak farklılık gösterir. Bazı akışlar yalnızca iş veya okul hesapları için kullanılabilir. Diğerleri hem iş veya okul hesapları hem de kişisel Microsoft hesapları için kullanılabilir.

Daha fazla bilgi için bkz. [Desteklenen hesap türleri](v2-supported-account-types.md#account-type-support-in-authentication-flows).

## <a name="application-scenarios"></a>Uygulama senaryoları

Microsoft Identity platformu, bu uygulama mimarilerinin kimlik doğrulamasını destekler:

- Tek sayfalı uygulamalar
- Web uygulamaları
- Web API'leri
- Mobil uygulamalar
- Yerel uygulamalar
- Daemon uygulamaları
- Sunucu tarafı uygulamalar

Uygulamalar, kullanıcıları oturum açmak ve korunan API 'Leri çağırmak için belirteçleri almak üzere farklı kimlik doğrulama akışlarını kullanır.

### <a name="single-page-application"></a>Tek sayfalı uygulama

Birçok modern web uygulaması, istemci tarafı tek sayfalı uygulamalar olarak oluşturulmuştur. Bu uygulamalar JavaScript veya angular, Vue ve tepki verme gibi bir çatı kullanır. Bu uygulamalar bir Web tarayıcısında çalışır.

Tek sayfalı uygulamalar, kimlik doğrulama özellikleri bakımından geleneksel sunucu tarafı Web uygulamalarından farklıdır. Tek sayfalı uygulamalar, Microsoft Identity platformunu kullanarak kullanıcıların oturumunu açabilir ve arka uç hizmetlerine veya Web API 'Lerine erişim belirteçleri alabilir. Microsoft Identity platformu, JavaScript uygulamaları için iki verme türü sunar:

| MSAL.js (2. x) | MSAL.js (1. x) |
|---|---|
| ![Tek sayfalı uygulama kimlik doğrulaması](media/scenarios/spa-app-auth.svg) | ![Tek sayfalı bir uygulama örtük](media/scenarios/spa-app.svg) |

### <a name="web-app-that-signs-in-a-user"></a>Kullanıcı oturumu açan Web uygulaması

![Kullanıcı oturumu açan bir Web uygulaması](media/scenarios/scenario-webapp-signs-in-users.svg)

Bir kullanıcının oturum açtığı bir Web uygulamasını korumaya yardımcı olmak için:

- .NET ortamında geliştirirseniz, ASP.NET OpenID Connect ara yazılımı ile ASP.NET veya ASP.NET Core kullanırsınız. Bir kaynağın korunması, [.net Için IdentityModel uzantıları](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) tarafından GERÇEKLEŞTIRILEN ve msal kitaplıklarını değil, güvenlik belirtecini doğrulamayı içerir.

- Node.js geliştirirseniz, [msal node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) veya [Passport.js](https://github.com/AzureAD/passport-azure-ad)kullanırsınız.

Daha fazla bilgi için, bkz. [Kullanıcılar oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md).

### <a name="web-app-that-signs-in-a-user-and-calls-a-web-api-on-behalf-of-the-user"></a>Bir Kullanıcı oturumu açan ve Kullanıcı adına bir Web API 'SI çağıran Web uygulaması

![Web API 'Lerini çağıran bir Web uygulaması](media/scenarios/web-app.svg)

Bir Web uygulamasından Kullanıcı adına bir Web API 'SI çağırmak için, yetkilendirme kodu akışını kullanın ve alınan belirteçleri belirteç önbelleğinde depolayın. Gerektiğinde, MSAL belirteçleri yeniler ve denetleyici önbellekteki belirteçleri sessizce alır.

Daha fazla bilgi için bkz. [Web API 'lerini çağıran Web uygulaması](scenario-web-app-call-api-overview.md).

### <a name="desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user"></a>Bir Web API 'sini oturum açmış bir kullanıcı adına çağıran masaüstü uygulaması

Bir masaüstü uygulamasının kullanıcılar tarafından oturum açan bir Web API 'sini çağırması için, MSAL 'in etkileşimli belirteç alma yöntemlerini kullanın. Bu etkileşimli yöntemlerle oturum açma kullanıcı arabirimi deneyimini kontrol edebilirsiniz. MSAL bu etkileşim için bir Web tarayıcısı kullanır.

![Web API 'sini çağıran masaüstü uygulaması](media/scenarios/desktop-app.svg)

Windows etki alanına veya Azure Active Directory (Azure AD) ile Birleşik bilgisayarlarda Windows 'da barındırılan uygulamalar için başka bir olasılık vardır. Bu uygulamalar [Tümleşik Windows kimlik doğrulaması](https://aka.ms/msal-net-iwa)kullanarak sessizce bir belirteç elde edebilir.

Tarayıcı olmadan bir cihazda çalışan uygulamalar, bir kullanıcı adına API 'yi hala çağırabilir. Kimlik doğrulamak için, kullanıcının Web tarayıcısına sahip başka bir cihazda oturum açması gerekir. Bu senaryo, [cihaz kodu akışını](https://aka.ms/msal-net-device-code-flow)kullanmanızı gerektirir.

![Cihaz kod akışı](media/scenarios/device-code-flow-app.svg)

Kullanmanız önerilmez, ancak [Kullanıcı adı/parola akışı](scenario-desktop-acquire-token.md#username-and-password) ortak istemci uygulamalarında kullanılabilir. Bu akış, DevOps gibi bazı senaryolarda hala gereklidir.

Kullanıcı adı/parola akışını kullanmak uygulamalarınızı kısıtlar. Örneğin, uygulamalar, Azure AD 'de çok faktörlü kimlik doğrulaması veya koşullu erişim aracı kullanması gereken bir kullanıcıya oturum açamaz. Uygulamalarınız aynı zamanda çoklu oturum açma özelliğinden de yararlanır. Kullanıcı adı/parola akışı ile kimlik doğrulaması, modern kimlik doğrulamasının ilkelerine karşı gider ve yalnızca eski nedenlerle sağlanır.

Masaüstü uygulamalarında, belirteç önbelleğinin kalıcı olmasını istiyorsanız [belirteç önbelleği serileştirmesini](scenario-desktop-acquire-token.md#file-based-token-cache)özelleştirebilirsiniz. [Çift belirteç önbelleği serileştirmesini](scenario-desktop-acquire-token.md#dual-token-cache-serialization-msal-unified-cache--adal-v3)uygulayarak, geriye dönük uyumlu ve ileri uyumlu belirteç önbellekleri kullanabilirsiniz. Bu belirteçler, önceki nesil kimlik doğrulama kitaplıklarını destekler. Belirli kitaplıklar .NET için Azure AD kimlik doğrulama kitaplığı 'nı (ADAL.NET) sürüm 3 ve sürüm 4 içerir.

Daha fazla bilgi için bkz. [Web API 'lerini çağıran masaüstü uygulaması](scenario-desktop-overview.md).

### <a name="mobile-app-that-calls-a-web-api-on-behalf-of-an-interactive-user"></a>Etkileşimli kullanıcı adına Web API 'SI çağıran mobil uygulama

Bir masaüstü uygulamasına benzer şekilde, bir mobil uygulama, bir Web API 'SI çağırmaya yönelik bir belirteç almak için MSAL etkileşimli belirteç alma yöntemlerini çağırır.

![Web API 'sini çağıran bir mobil uygulama](media/scenarios/mobile-app.svg)

MSAL iOS ve MSAL Android varsayılan olarak sistem Web tarayıcısını kullanır. Ancak, bunun yerine katıştırılmış Web görünümünü kullanmak için bunları yönlendirebilirsiniz. Evrensel Windows Platformu (UWP), iOS veya Android mobil platformuna bağlı olan değerler vardır.

Bir cihaz KIMLIĞI veya cihaz kaydıyla ilgili koşullu erişim içeren bazı senaryolar, cihaza bir aracı yüklenmesini gerektirir. Aracıların örnekleri, Android ve iOS üzerinde Microsoft Şirket Portalı Microsoft Authenticator. MSAL artık aracılar ile etkileşime geçebilir. Aracılar hakkında daha fazla bilgi için bkz. [Android ve iOS 'ta aracıları](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)kullanma.

Daha fazla bilgi için bkz. [Web API 'lerini çağıran mobil uygulama](scenario-mobile-overview.md).

> [!NOTE]
> MSAL. iOS, MSAL kullanan bir mobil uygulama. Xamarin üzerinde Android veya MSAL.NET, uygulama koruma ilkelerinin uygulanmasını sağlayabilir. Örneğin, ilkeler bir kullanıcının korumalı metni kopyalamasını engelleyebilir. Mobil uygulama Intune tarafından yönetilir ve Intune tarafından yönetilen bir uygulama olarak tanınır. Daha fazla bilgi için bkz. [Microsoft Intune uygulama SDK 'sına genel bakış](/intune/app-sdk).
>
> [Intune uygulama SDK 'Sı](/intune/app-sdk-get-started) msal kitaplıklarından ayrıdır ve kendi kendıne Azure AD ile etkileşime girer.

### <a name="protected-web-api"></a>Korumalı web API’si

Uygulamanızın yeniden Web API 'SI gibi Web hizmetlerini güvenli hale getirmek için Microsoft Identity platform uç noktasını kullanabilirsiniz. Korumalı bir Web API 'SI, erişim belirteci aracılığıyla çağrılır. Belirteç, API 'nin verilerinin güvenliğini sağlar ve gelen isteklerin kimliğini doğrular. Bir Web API 'SI çağıran, bir HTTP isteğinin yetkilendirme üstbilgisine bir erişim belirteci ekler.

ASP.NET veya ASP.NET Core Web API 'nizi korumak istiyorsanız, erişim belirtecini doğrulayın. Bu doğrulama için ASP.NET JWT ara yazılımını kullanırsınız. Doğrulama, MSAL.NET tarafından değil [.NET kitaplığı Için IdentityModel uzantıları](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) tarafından yapılır.

Daha fazla bilgi için bkz. [korumalı Web API 'si](scenario-protected-web-api-overview.md).

### <a name="web-api-that-calls-another-web-api-on-behalf-of-a-user"></a>Kullanıcı adına başka bir Web API 'SI çağıran Web API 'SI

Korunan Web API 'nizin bir kullanıcı adına başka bir Web API 'SI çağırması için, uygulamanızın aşağı akış Web API 'SI için bir belirteç edinmesi gerekir. Bu tür çağrılar bazen *hizmetten hizmete* çağrılar olarak adlandırılır. Diğer Web API 'Lerini çağıran Web API 'Lerinin özel önbellek serileştirme sağlaması gerekir.

![Başka bir Web API 'SI çağıran Web API 'SI](media/scenarios/web-api.svg)

Daha fazla bilgi için bkz. [Web API 'lerini çağıran Web API 'si](scenario-web-api-call-api-overview.md).

### <a name="daemon-app-that-calls-a-web-api-in-the-daemons-name"></a>Daemon 'ın adında bir Web API 'SI çağıran Daemon uygulaması

Uzun süre çalışan işlemlere sahip olan veya Kullanıcı etkileşimi olmadan çalışan uygulamalarda güvenli Web API 'Lerine erişmek için bir yol gerekir. Bu tür bir uygulama uygulamanın kimliğini kullanarak kimlik doğrulaması yapabilir ve belirteçleri alabilir. Uygulama, bir istemci gizli dizisi veya sertifikası kullanarak kimliğini kanıtlar.

MSAL ' deki [istemci kimlik bilgileri](scenario-daemon-acquire-token.md#acquiretokenforclient-api) alma yöntemlerini kullanarak, çağıran uygulama için bir belirteç alan Daemon uygulamalarını yazabilirsiniz. Bu yöntemler, Azure AD 'de uygulama kaydına eklediğiniz bir istemci gizli anahtarı gerektirir. Ardından uygulama, parolayı çağrılan daemon ile paylaşır. Bu gizli dizi örnekleri uygulama parolaları, sertifika onaylama ve istemci onaylama işlemlerini içerir.

![Diğer uygulamalar ve API 'Ler tarafından çağrılan bir Daemon uygulaması](media/scenarios/daemon-app.svg)

Daha fazla bilgi için bkz. [Web API 'lerini çağıran Daemon uygulaması](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Senaryolar ve desteklenen kimlik doğrulama akışları

Belirteç isteyen uygulama senaryolarını uygulamak için kimlik doğrulama akışlarını kullanırsınız. Uygulama senaryoları ve kimlik doğrulama akışları arasında bire bir eşleme yoktur.

Belirteçleri alma ile ilgili senaryolar Ayrıca OAuth 2,0 kimlik doğrulama akışlarıyla eşlenir. Daha fazla bilgi için bkz. [Microsoft Identity platformunda OAuth 2,0 ve OpenID Connect Protocols](active-directory-v2-protocols.md).

<table>
 <thead>
  <tr><th>Senaryo</th> <th>Ayrıntılı senaryo izlenecek yol</th> <th>OAuth 2,0 Flow ve verme</th> <th>Hedef kitle</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Auth code" src="media/scenarios/spa-app-auth.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Tek sayfalı uygulama</a></td>
   <td>PKCE ile <a href="v2-oauth2-auth-code-flow.md">yetkilendirme kodu</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App with Implicit" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Tek sayfalı uygulama</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Örtük</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Kullanıcılar için oturum açan web uygulaması</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Yetkilendirme kodu</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that calls web APIs" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Web API’lerini çağıran web uygulaması</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Yetkilendirme kodu</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Web API'lerini çağıran masaüstü uygulaması</a></td>
   <td>PKI ile <a href="v2-oauth2-auth-code-flow.md">yetkilendirme kodu</a> kullanarak etkileşimli</td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td>Tümleşik Windows Kimlik Doğrulaması</td>
   <td>İş veya okul hesapları</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Kaynak sahibi parolası</a></td>
   <td>İş veya okul hesapları ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Cihaz kodu</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Web API'lerini çağıran mobil uygulama</a></td>
   <td>PKI ile <a href="v2-oauth2-auth-code-flow.md">yetkilendirme kodu</a> kullanarak etkileşimli</td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Kaynak sahibi parolası</a></td>
   <td>İş veya okul hesapları ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Web API 'Lerini çağıran Daemon uygulaması</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">İstemci kimlik bilgileri</a></td>
   <td>Yalnızca uygulama olmayan ve yalnızca Azure AD kuruluşlarında kullanılan uygulama izinleri</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Web API'lerini çağıran web API'si</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Adına-</a></td>
   <td>İş veya okul hesapları ve kişisel hesaplar</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Senaryolar ve desteklenen platformlar ve diller

Microsoft kimlik doğrulama kitaplıkları Çoklu platformları destekler:

- .NET Core
- .NET Framework
- Java
- JavaScript
- Mac OS
- Yerel Android
- Yerel iOS
- Node.js
- Python
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android

Uygulamalarınızı oluşturmak için çeşitli diller de kullanabilirsiniz.

> [!NOTE]
> Bazı uygulama türleri her platformda kullanılamaz.

Aşağıdaki tablonun Windows sütununda, .NET Core her bahsedildiğinde .NET Framework de mümkündür. İkinci değer, tablonun karışıklık oluşmasını önlemek için atlanır.

|Senaryo  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Tek sayfalı uygulama](scenario-spa-overview.md) <br/>[![Tek sayfalı uygulama kimlik doğrulaması](media/scenarios/spa-app-auth.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Tek sayfalı uygulama](scenario-spa-overview.md) <br/>[![Tek sayfalı uygulama örtük](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Kullanıcılar için oturum açan web uygulaması](scenario-web-app-sign-user-overview.md) <br/>[![Kullanıcıları oturum açan Web uygulaması](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>
| [Web API’lerini çağıran web uygulaması](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web API’lerini çağıran web uygulaması](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>
| [Web API'lerini çağıran masaüstü uygulaması](scenario-desktop-overview.md) <br/> <br/>[ ![ Web API 'lerini](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) ![ çağıran masaüstü uygulaması Cihaz kod akışı](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/> ![iOS/amaç C veya Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC |
| [Web API'lerini çağıran mobil uygulama](scenario-mobile-overview.md) <br/> [![Web API'lerini çağıran mobil uygulama](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/amaç C veya Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon uygulaması](scenario-daemon-overview.md) <br/> [![Daemon uygulaması](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>
| [Web API'lerini çağıran web API'si](scenario-web-api-call-api-overview.md) <br/><br/> [![Web API'lerini çağıran web API'si](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python ![MSAL düğümü](media/sample-v2-code/small-logo-nodejs.png) <br/>MSAL düğümü<br/>

Daha fazla bilgi için bkz. [Microsoft Identity platform kimlik doğrulama kitaplıkları](reference-v2-libraries.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Kimlik doğrulama temelleri](./authentication-vs-authorization.md) ve [Microsoft Identity platformunda erişim belirteçleri](access-tokens.md)hakkında daha fazla bilgi edinin.
* [IoT uygulamalarına erişimin güvenliğini sağlama](/azure/architecture/example-scenario/iot-aad/iot-aad)hakkında daha fazla bilgi edinin.
