---
title: Microsoft Identity platform için kimlik doğrulama senaryoları | Mavisi
description: Microsoft Identity platformu için kimlik doğrulama akışları ve uygulama senaryoları hakkında bilgi edinin. Kimliklerin kimliğini doğrulayabilecek, belirteçleri elde eden ve korunan API 'Leri çağıran farklı türde uygulamalar hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d6a36d66d6094bbac1b3e39a4ee5cea55737971
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473832"
---
# <a name="authentication-flows-and-application-scenarios"></a>Kimlik doğrulama akışları ve uygulama senaryoları

Microsoft Identity platform (v 2.0) uç noktası, farklı türlerde modern uygulama mimarilerinin kimlik doğrulamasını destekler. Mimarilerin hepsi, [OAuth 2,0 ve OpenID Connect](active-directory-v2-protocols.md)sektör standardı protokollerine dayanır.  [Kimlik doğrulama kitaplıklarını](reference-v2-libraries.md)kullanarak, uygulamalar kimliklerin kimliğini doğrular ve korunan API 'lere erişmek için belirteçleri elde edin.

Bu makalede, farklı kimlik doğrulama akışları ve içinde kullanıldıkları uygulama senaryoları açıklanmaktadır. Bu makalede ayrıca aşağıdakiler listelenmektedir:
- [Uygulama senaryoları ve desteklenen kimlik doğrulama akışları](#scenarios-and-supported-authentication-flows).
- [Uygulama senaryoları ve desteklenen platformlar ve diller](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Uygulama kategorileri

Belirteçleri aşağıdakiler dahil olmak üzere çeşitli türde uygulamalardan elde edilebilir:

- Web uygulamaları
- Mobil uygulamalar
- Masaüstü uygulamaları
- Web API'leri

Ayrıca, tarayıcıya sahip olmayan veya IoT üzerinde çalışan cihazlarda çalışan uygulamalardan de elde edilebilir.

Uygulamalar aşağıdaki listede olduğu gibi kategorilere ayrılır:

- [Korumalı kaynaklara karşı istemci uygulamalar](#protected-resources-vs-client-applications): bazı senaryolar, Web uygulamaları veya Web API 'leri gibi kaynakları korumaya yönelik bir uygulamalardır. Diğer senaryolar, korumalı bir Web API 'SI çağırmak için bir güvenlik belirteci alma ile ilgilidir.
- [Kullanıcılar veya kullanıcılar olmadan](#with-users-or-without-users): bazı senaryolar oturum açmış bir Kullanıcı içerir, ancak arka plan programı senaryoları gibi diğerleri de Kullanıcı içermez.
- [Tek sayfalı, genel istemci ve gizli istemci uygulamaları](#single-page-public-client-and-confidential-client-applications): Bunlar, uygulama türlerinin üç büyük kategorileridir. Her biri farklı kitaplıklar ve nesneler ile kullanılır.
- [Oturum açma hedef kitlesi](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): kullanılabilir kimlik doğrulama akışları, oturum açma izleyicisine bağlı olarak farklılık gösterir. Bazı akışlar yalnızca iş veya okul hesapları için kullanılabilir. Bazıları hem iş ya da okul hesapları hem de kişisel Microsoft hesapları için kullanılabilir. İzin verilen hedef kitle, kimlik doğrulama akışlarına bağlıdır.
- [Desteklenen OAuth 2,0 akışları](#scenarios-and-supported-authentication-flows): kimlik doğrulama akışları, belirteç isteyen uygulama senaryolarını uygulamak için kullanılır. Uygulama senaryoları ve kimlik doğrulama akışları arasında bire bir eşleme yoktur.
- [Desteklenen platformlar](#scenarios-and-supported-platforms-and-languages): tüm uygulama senaryoları her platformda kullanılamaz.

### <a name="protected-resources-vs-client-applications"></a>Korumalı kaynaklara ve istemci uygulamalarına karşı

Kimlik doğrulama senaryoları iki etkinlik içerir:

- **Korumalı bir Web API 'si için güvenlik belirteçleri**alma: Microsoft, özel Microsoft kimlik doğrulama KITAPLıĞı (msal) ailesinde belirteçleri almak için [kimlik doğrulama kitaplıklarını](reference-v2-libraries.md#microsoft-supported-client-libraries) kullanmanızı önerir.
- Web **API 'sini veya Web uygulamasını koruma**: BIR Web API 'sini veya Web uygulaması kaynağını korumanın bir testi güvenlik belirtecini doğruluyor. Bazı platformlarda Microsoft, [Ara yazılım kitaplıkları](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries)sunmaktadır.

### <a name="with-users-or-without-users"></a>Kullanıcılar veya kullanıcılar olmadan

Çoğu kimlik doğrulama senaryosunda, oturum açmış kullanıcılar adına belirteç elde edin.

![Kullanıcıları olan senaryolar](media/scenarios/scenarios-with-users.svg)

Ancak, uygulamaların kullanıcı olmadan kendi adına belirteç edinmeleri için de Daemon-App senaryoları vardır.

![Daemon uygulamalarıyla senaryolar](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Tek sayfalı, ortak istemci ve gizli istemci uygulamaları

Güvenlik belirteçleri, birden çok uygulama türünden alınabilir. Bu uygulamalar üç kategoride ayrılmalıdır:

- **Tek sayfalı uygulamalar**: Ayrıca, Spas olarak da bilinen, bunlar, tarayıcıda çalışan bir JavaScript veya TypeScript uygulamasından edinilen Web uygulamalardır. Birçok modern uygulamanın, birincil olarak JavaScript 'te yazılmış tek sayfalı bir uygulama ön ucu vardır. Uygulama genellikle angular, yanıt verme veya Vue gibi bir çerçeve kullanır. Tek sayfalı uygulamaları destekleyen tek Microsoft kimlik doğrulama kitaplığı MSAL. js ' dir.

- **Ortak istemci uygulamaları**: Bu uygulamalar kullanıcıların her zaman oturum açmasını ister:
  - Oturum açmış kullanıcı adına Web API 'Lerini çağıran masaüstü uygulamaları
  - Mobil uygulamalar
  - IoT üzerinde çalışan cihazlar gibi tarayıcıya sahip olmayan cihazlarda çalışan uygulamalar

  Bu uygulamalar MSAL [Publicclientapplication](msal-client-applications.md) sınıfı tarafından temsil edilir.

- **Gizli istemci uygulamaları**:
  - Web API 'sini çağıran Web uygulamaları
  - Web API 'sini çağıran Web API 'Leri
  - Linux Daemon veya Windows hizmeti gibi bir konsol hizmeti olarak uygulandığında bile Daemon uygulamaları

  Bu tür uygulamalar [ConfidentialClientApplication](msal-client-applications.md) sınıfını kullanır.

## <a name="application-scenarios"></a>Uygulama senaryoları

Microsoft Identity platform uç noktası, farklı türlerdeki uygulama mimarilerinde kimlik doğrulamasını destekler:

- Tek sayfalı uygulamalar
- Web uygulamaları
- Web API'leri
- Mobil uygulamalar
- Yerel uygulamalar
- Daemon uygulamaları
- Sunucu tarafı uygulamalar

Uygulamalar, kullanıcıları oturum açmak ve korunan API 'Leri çağırmak için belirteçleri almak üzere farklı kimlik doğrulama akışlarını kullanır.

### <a name="a-single-page-application"></a>Tek sayfalı uygulama

Birçok modern web uygulaması, JavaScript veya angular, Vue. js ve yanıt verme. js gibi bir SPA çerçevesi kullanılarak yazılmış istemci tarafı tek sayfalı uygulamalar olarak oluşturulmuştur. Bu uygulamalar bir Web tarayıcısında çalışır. Kimlik doğrulama özellikleri geleneksel sunucu tarafı Web uygulamalarından farklıdır. Tek sayfalı uygulamalar, Microsoft Identity platformunu kullanarak kullanıcıların oturumunu açabilir ve arka uç hizmetlerine veya Web API 'Lerine erişim belirteçleri alabilir.

![Tek sayfalı uygulama](media/scenarios/spa-app.svg)

Daha fazla bilgi için bkz. [tek sayfalı uygulamalar](scenario-spa-overview.md).

### <a name="a-web-app-that-is-signing-in-a-user"></a>Kullanıcı oturumu açan bir Web uygulaması

![Kullanıcı oturumu açan bir Web uygulaması](media/scenarios/scenario-webapp-signs-in-users.svg)

Bir kullanıcının oturum açtığı bir Web uygulamasını korumak için:

- .NET ortamında geliştirirseniz, ASP.NET Open ID Connect ara yazılımı ile ASP.NET veya ASP.NET Core kullanırsınız. Bir kaynağın korunması, [.NET kitaplığı Için IdentityModel uzantıları](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) tarafından GERÇEKLEŞTIRILEN ve msal kitaplıklarını değil güvenlik belirtecinin doğrulanmasını içerir.

- Node. js ' de geliştirme yaptıysanız, Passport. js ' yi kullanırsınız.

Daha fazla bilgi için, bkz. [Kullanıcılar oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Kullanıcıya oturum açan ve Kullanıcı adına bir Web API 'SI çağıran bir Web uygulaması

![Web API 'Lerini çağıran bir Web uygulaması](media/scenarios/web-app.svg)

Bir Web uygulamasından Kullanıcı adına bir Web API 'SI çağırmak için MSAL **ConfidentialClientApplication** sınıfını kullanın. Yetkilendirme kodu akışını kullanır ve alınan belirteçleri belirteç önbelleğinde depolar. Gerektiğinde, MSAL belirteçleri yeniler ve denetleyici önbellekteki belirteçleri sessizce alır.

Daha fazla bilgi için bkz. [Web API 'leri çağıran bir Web uygulaması](scenario-web-app-call-api-overview.md).

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Bir Web API 'sini, oturum açmış bir kullanıcı adına çağıran bir masaüstü uygulaması

Bir masaüstü uygulamasının kullanıcılar tarafından oturum açan bir Web API 'sini çağırması için MSAL **Publicclientapplication** sınıfının etkileşimli belirteç alma yöntemlerini kullanın. Bu etkileşimli yöntemlerle oturum açma kullanıcı arabirimi deneyimini kontrol edebilirsiniz. MSAL bu etkileşim için bir Web tarayıcısı kullanır.

![Web API 'sini çağıran masaüstü uygulaması](media/scenarios/desktop-app.svg)

Windows etki alanına veya Azure Active Directory (Azure AD) ile Birleşik bilgisayarlarda Windows 'da barındırılan uygulamalar için başka bir olasılık vardır. Bu uygulamalar [Tümleşik Windows kimlik doğrulaması](https://aka.ms/msal-net-iwa)kullanarak sessizce bir belirteç elde edebilir.

Tarayıcı olmadan bir cihazda çalışan uygulamalar, bir kullanıcı adına API 'yi hala çağırabilir. Kimlik doğrulamak için, kullanıcının Web tarayıcısına sahip başka bir cihazda oturum açması gerekir. Bu senaryo, [cihaz kodu akışını](https://aka.ms/msal-net-device-code-flow)kullanmanızı gerektirir.

![Cihaz kod akışı](media/scenarios/device-code-flow-app.svg)

Bunu kullanmanızı önermeyiz, ancak [Kullanıcı adı/parola akışı](https://aka.ms/msal-net-up) ortak istemci uygulamalarında kullanılabilir. Bu akış, DevOps gibi bazı senaryolarda hala gereklidir.

Ancak bu akışın kullanılması, uygulamalarınızda kısıtlamalar uygular. Örneğin, bu akışı kullanan uygulamalar Multi-Factor Authentication veya koşullu erişim gerçekleştirmesi gereken bir kullanıcıya oturum açamaz. Uygulamalarınız aynı zamanda çoklu oturum açma özelliğinden de yararlanır.

Kullanıcı adı/parola akışı ile kimlik doğrulaması, modern kimlik doğrulamasının ilkelerine karşı gider ve yalnızca eski nedenlerle sağlanır.

Masaüstü uygulamalarında, belirteç önbelleğinin kalıcı olmasını istiyorsanız [belirteç önbelleği serileştirmesini özelleştirmeniz](https://aka.ms/msal-net-token-cache-serialization)gerekir. [Çift belirteç önbelleği serileştirmesini](https://aka.ms/msal-net-dual-cache-serialization)uygulayarak, önceki nesil kimlik doğrulama kitaplıklarıyla birlikte, geriye dönük uyumlu ve ileri uyumlu belirteç önbellekleri kullanabilirsiniz. Belirli kitaplıklar .NET için Azure AD kimlik doğrulama kitaplığı 'nı (ADAL.NET) sürüm 3 ve sürüm 4 içerir.

Daha fazla bilgi için bkz. [Web API 'lerini çağıran masaüstü uygulaması](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Etkileşimli kullanıcı adına Web API 'SI çağıran bir mobil uygulama

Bir masaüstü uygulamasına benzer şekilde, bir mobil uygulama, Web API 'sini çağırmak için bir belirteç almak üzere MSAL **Publicclientapplication** sınıfının etkileşimli belirteç alma yöntemlerini çağırır.

![Web API 'sini çağıran bir mobil uygulama](media/scenarios/mobile-app.svg)

MSAL iOS ve MSAL Android varsayılan olarak sistem Web tarayıcısını kullanır. Ancak, bunun yerine katıştırılmış Web görünümünü kullanmak için bunları yönlendirebilirsiniz. Evrensel Windows Platformu (UWP), iOS veya Android mobil platformuna bağlı olan değerler vardır.

Bir cihaz KIMLIĞI veya cihaz kaydıyla ilgili koşullu erişim içeren bazı senaryolar, cihaza bir [Aracı](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) yüklenmesini gerektirir. Aracıların örnekleri, Android ve iOS üzerinde Microsoft Şirket Portalı Microsoft Authenticator. Ayrıca, MSAL artık aracılar ile etkileşime geçebilir.

> [!NOTE]
> MSAL. iOS, MSAL kullanan mobil uygulamanız. Xamarin üzerinde Android veya MSAL.NET, uygulama koruma ilkelerinin uygulanmasını sağlayabilir. Örneğin, ilkeler bir kullanıcının korumalı metni kopyalamasını engelleyebilir. Mobil uygulama, [Intune tarafından yönetilir](https://docs.microsoft.com/intune/app-sdk) ve Intune tarafından yönetilen bir uygulama olarak tanınır. [Intune uygulama SDK 'Sı](https://docs.microsoft.com/intune/app-sdk-get-started) msal kitaplıklarından ayrıdır ve kendi kendıne Azure AD ile etkileşime girer.

Daha fazla bilgi için bkz. [Web API 'lerini çağıran mobil uygulama](scenario-mobile-overview.md).

### <a name="a-protected-web-api"></a>Korumalı bir Web API 'SI

Uygulamanızın yeniden Web API 'SI gibi Web hizmetlerini güvenli hale getirmek için Microsoft Identity platform uç noktasını kullanabilirsiniz. Korumalı bir Web API 'SI, API 'nin verilerini güvenli hale getirmek ve gelen isteklerin kimliğini doğrulamak için bir erişim belirteciyle çağırılır. Bir Web API 'SI çağıran, bir HTTP isteğinin yetkilendirme üstbilgisine bir erişim belirteci ekler.

ASP.NET veya ASP.NET Core Web API 'nizi korumak istiyorsanız, erişim belirtecini doğrulamanız gerekir. Bu doğrulama için ASP.NET JWT ara yazılımını kullanırsınız. Doğrulama, MSAL.NET tarafından değil [.NET kitaplığı Için IdentityModel uzantıları](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) tarafından yapılır.

Daha fazla bilgi için bkz. [korumalı Web API 'si](scenario-protected-web-api-overview.md).

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Kullanıcı adına başka bir Web API 'SI çağıran Web API 'SI

ASP.NET veya ASP.NET Core korunan Web API 'niz Kullanıcı adına başka bir Web API 'SI çağırmak için uygulamanızın, aşağı akış Web API 'SI için bir belirteç edinmesi gerekir. Bu, **ConfidentialClientApplication** sınıfının [Acquiretokenonbehalfof](https://aka.ms/msal-net-on-behalf-of) metodunu çağırarak yapar. Bu tür çağrılar Ayrıca hizmetten hizmete çağrılar olarak adlandırılır. Diğer Web API 'Lerini çağıran Web API 'Lerinin özel önbellek serileştirme sağlaması gerekir.

  ![Başka bir Web API 'SI çağıran Web API 'SI](media/scenarios/web-api.svg)

Daha fazla bilgi için bkz. [Web API 'lerini çağıran Web API 'si](scenario-web-api-call-api-overview.md).

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Arka plan programının adında Web API 'sini çağıran bir Daemon uygulaması

Uzun süre çalışan işlemlere sahip olan veya Kullanıcı etkileşimi olmadan çalışan uygulamalarda güvenli Web API 'Lerine erişmek için bir yol gerekir. Bu tür bir uygulama, kullanıcının temsilci kimliği yerine uygulamanın kimliğini kullanarak kimlik doğrulaması yapabilir ve belirteçleri alabilir. Uygulama, bir istemci gizli dizisi veya sertifikası kullanarak kimliğini kanıtlar.

MSAL **ConfidentialClientApplication** sınıfının [istemci kimlik bilgileri](https://aka.ms/msal-net-client-credentials) alma yöntemlerini kullanarak, çağıran uygulama için bir belirteç alan Daemon uygulamalarını yazabilirsiniz. Bu yöntemler, çağıran uygulamanın Azure AD ile gizli bir parola kaydettirmesini gerektirir. Ardından uygulama, parolayı çağrılan daemon ile paylaşır. Bu gizli dizi örnekleri uygulama parolaları, sertifika onaylama veya istemci onaylama işlemlerini içerir.

![Diğer uygulamalar ve API 'Ler tarafından çağrılan bir Daemon uygulaması](media/scenarios/daemon-app.svg)

Daha fazla bilgi için bkz. [Web API 'lerini çağıran Daemon uygulaması](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Senaryolar ve desteklenen kimlik doğrulama akışları

Belirteçleri alma ile ilgili senaryolar, [Microsoft Identity platform protokolleri](active-directory-v2-protocols.md)bölümünde açıklandığı şekilde OAuth 2,0 kimlik doğrulama akışlarıyla de eşlenir.

<table>
 <thead>
  <tr><th>Senaryo</th> <th>Ayrıntılı senaryo izlenecek yol</th> <th>OAuth 2,0 Flow ve verme</th> <th>Hedef kitle</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">Tek sayfalı uygulama</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">İndirgen</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Kullanıcılara oturum açan bir Web uygulaması</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Yetkilendirme kodu</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Web API 'Lerini çağıran bir Web uygulaması</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Yetkilendirme kodu</a></td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Web API 'Lerini çağıran bir masaüstü uygulaması</a></td>
   <td>PKI ile <a href="v2-oauth2-auth-code-flow.md">yetkilendirme kodu</a> kullanarak etkileşimli</td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td>Tümleşik Windows kimlik doğrulaması</td>
   <td>İş veya okul hesapları</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Kaynak sahibi parolası</a></td>
   <td>İş veya okul hesapları ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Cihaz kodu</a></td>
   <td>İş veya okul hesapları</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Web API 'Lerini çağıran bir mobil uygulama</a></td>
   <td>PKI ile <a href="v2-oauth2-auth-code-flow.md">yetkilendirme kodu</a> kullanarak etkileşimli</td>
   <td>İş veya okul hesapları, kişisel hesaplar ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Kaynak sahibi parolası</a></td>
   <td>İş veya okul hesapları ve Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">Web API 'Lerini çağıran bir Daemon uygulaması</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">İstemci kimlik bilgileri</a></td>
   <td>Kullanıcı olmadan ve yalnızca Azure AD kuruluşlarda kullanılan yalnızca uygulama izinleri</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">Web API 'Lerini çağıran bir Web API 'SI</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Adına</a></td>
   <td>İş veya okul hesapları ve kişisel hesaplar</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Senaryolar ve desteklenen platformlar ve diller

Microsoft kimlik doğrulama kitaplıkları Çoklu platformları destekler:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Yerel iOS
- macOS
- Yerel Android
- Java
- Python

Uygulamalarınızı oluşturmak için çeşitli diller de kullanabilirsiniz. Bazı uygulama türlerinin her platformda kullanılabilir olmadığına unutmayın.

Aşağıdaki tablonun Windows sütununda, .NET Core her bahsedildiğinde .NET Framework de mümkündür. İkinci değer, tablonun karışıklık oluşmasını önlemek için atlanır.

|Senaryo  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [Tek sayfalı uygulama](scenario-spa-overview.md) <br/>[Tek sayfalı uygulama ![](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Kullanıcılarda oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md) <br/>[kullanıcıları oturum açan ![Web uygulaması](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Çekirdeği](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Çekirdeği | ![ASP.NET Çekirdeği](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Çekirdeği | ![ASP.NET Çekirdeği](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Çekirdeği
| [Web API 'Lerini çağıran Web uygulaması](scenario-web-app-call-api-overview.md) <br/> <br/>[Web API 'Lerini çağıran ![Web uygulaması](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Çekirdeği](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Çekirdeği](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python| ![ASP.NET Çekirdeği](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Flask + MSAL Python
| [Web API'lerini çağıran masaüstü uygulaması](scenario-desktop-overview.md) <br/> <br/>Cihaz kod akışı ![[Web API 'lerini çağıran![masaüstü uygulaması](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS/amaç C veya Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC |
| [Web API 'Lerini çağıran mobil uygulama](scenario-mobile-overview.md) <br/> [Web API 'Lerini çağıran mobil uygulama ![](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/amaç C veya Swift](media/sample-v2-code/small_logo_iOS.png) MSAL. ObjC | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon uygulaması](scenario-daemon-overview.md) <br/> [![Daemon uygulaması](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Web API 'Lerini çağıran Web API 'SI](scenario-web-api-call-api-overview.md) <br/><br/> [Web API 'Lerini çağıran ![Web API 'SI](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Çekirdeği](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Ayrıca bkz. [OS/Language tarafından desteklenen Microsoft kitaplıkları](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Sonraki adımlar
[Kimlik doğrulama temelleri](authentication-scenarios.md) ve [erişim belirteçleri](access-tokens.md)hakkında daha fazla bilgi edinin.
