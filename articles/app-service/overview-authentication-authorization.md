---
title: Kimlik doğrulama ve yetkilendirme-Azure App Service | Microsoft Docs
description: Azure App Service için kimlik doğrulama/yetkilendirme özelliğine kavramsal başvuru ve genel bakış
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: 2179f4e7d5350cdf9d82413e4f70647c20c3c399
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808758"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Azure Uygulama Hizmeti’nde kimlik doğrulaması ve yetkilendirme

> [!NOTE]
> Şu anda, AAD v2 (MSAL dahil) Azure uygulama hizmetleri ve Azure Işlevleri için desteklenmez. Lütfen güncelleştirmeler için yeniden denetleyin.
>

Azure App Service, yerleşik kimlik doğrulama ve yetkilendirme desteği sunarak, Web uygulamanızda, yeniden derlenen API 'de ve mobil arka uçta ve ayrıca [Azure işlevlerinde](../azure-functions/functions-overview.md)en az veya hiç kod yazmadan kullanıcılara oturum açabilir ve verilere erişebilirsiniz. Bu makalede, App Service uygulamanızın kimlik doğrulama ve yetkilendirme işlemlerini basitleştirmeye nasıl yardımcı olduğu açıklanır.

Güvenli kimlik doğrulama ve yetkilendirme, Federasyon, şifreleme, [JSON Web belirteçleri (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) yönetimi, [izin verme](https://oauth.net/2/grant-types/)vb. gibi güvenlik konusunda ayrıntılı bilgiler gerektirir. App Service, müşterinize iş değeri sağlamaya yönelik daha fazla zaman ve enerji harcayabilmeniz için bu yardımcı programları sağlar.

> [!NOTE]
> Kimlik doğrulama ve yetkilendirme için App Service kullanmanız gerekli değildir. Birçok Web çerçevesi güvenlik özellikleriyle birlikte paketlenmiştir ve isterseniz bunları kullanabilirsiniz. App Service sağladığından daha fazla esneklik gerekiyorsa kendi yardımcı programlarını da yazabilirsiniz.  
>

Yerel mobil uygulamalara özgü bilgiler için, bkz. [Azure App Service ile mobil uygulamalar Için Kullanıcı kimlik doğrulaması ve yetkilendirme](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Nasıl çalışır

Kimlik doğrulama ve yetkilendirme modülü, uygulama kodunuzla aynı korumalı alanda çalışır. Etkin olduğunda, her gelen HTTP isteği, uygulama kodunuz tarafından işlenemeden önce üzerinden geçirilir.

![](media/app-service-authentication-overview/architecture.png)

Bu modül, uygulamanız için çeşitli şeyleri işler:

- Belirtilen sağlayıcıyla kullanıcıların kimliğini doğrular
- Belirteçleri doğrular, depolar ve yeniler
- Kimliği doğrulanmış oturumu yönetir
- Kimlik bilgilerini istek üst bilgilerine ekler

Modül, uygulama kodınızdan ayrı olarak çalışır ve uygulama ayarları kullanılarak yapılandırılır. SDK, belirli dil veya uygulama kodunuzda değişiklik yapılması gerekmez. 

### <a name="user-claims"></a>Kullanıcı talepleri

Tüm dil çerçeveleri için App Service, kullanıcının taleplerini istek üst bilgilerine ekleme göre kodunuzda kullanılabilir hale getirir. ASP.NET 4,6 uygulamaları için App Service, kimliği doğrulanmış kullanıcının taleplerini [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) ile doldurur. böylece, `[Authorize]` özniteliği de dahil olmak üzere standart .NET kod modelini izleyebilirsiniz. Benzer şekilde, PHP uygulamaları için App Service `_SERVER['REMOTE_USER']` değişkenini doldurur. Java uygulamaları için, talepler [Tomcat servlet 'ten erişilebilir](containers/configure-language-java.md#authenticate-users-easy-auth).

[Azure işlevleri](../azure-functions/functions-overview.md)için `ClaimsPrincipal.Current` .NET kodu için sabit değildir ancak kullanıcı taleplerini istek başlıklarında bulabilirsiniz.

Daha fazla bilgi için bkz. [Kullanıcı taleplerine erişme](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Belirteç deposu

App Service, Web uygulamalarınızın, API 'lerin veya yerel mobil uygulamalarınızın kullanıcılarıyla ilişkili belirteçlerin bir deposu olan yerleşik bir belirteç deposu sağlar. Herhangi bir sağlayıcı ile kimlik doğrulamasını etkinleştirdiğinizde, bu belirteç deposu uygulamanız için hemen kullanılabilir. Uygulama kodunuzun Kullanıcı adına bu sağlayıcılardan veriye erişmesi gerekiyorsa, örneğin: 

- kimliği doğrulanmış kullanıcının Facebook zaman çizelgesinde gönderi
- kullanıcının Azure Active Directory Graph API şirket verilerini okuyun veya hatta Microsoft Graph

Uygulamanızda bu belirteçleri toplamak, depolamak ve yenilemek için genellikle kod yazmanız gerekir. Belirteç deposu ile, ihtiyacınız olduğunda [belirteçleri alır](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) ve geçersiz hale geldiklerinde [bunları yenilemek App Service söyleyin](app-service-authentication-how-to.md#refresh-identity-provider-tokens) . 

Kimliği doğrulanmış oturum için önbelleğe alınan kimlik belirteçleri, erişim belirteçleri ve yenileme belirteçleri ve yalnızca ilişkili kullanıcı tarafından erişilebilir.  

Uygulamanızdaki belirteçlerle çalışmanız gerekmiyorsa, belirteç deposunu devre dışı bırakabilirsiniz.

### <a name="logging-and-tracing"></a>Günlüğe kaydetme ve izleme

[Uygulama günlüğünü etkinleştirirseniz](troubleshoot-diagnostic-logs.md), kimlik doğrulama ve yetkilendirme izlemelerini doğrudan günlük dosyalarınızda görürsünüz. Beklemediğiniz bir kimlik doğrulama hatası görürseniz, mevcut uygulama günlüklerinizi inceleyerek tüm ayrıntıları rahat bir şekilde bulabilirsiniz. [Başarısız istek izlemeyi](troubleshoot-diagnostic-logs.md)etkinleştirirseniz, kimlik doğrulama ve yetkilendirme modülünün başarısız bir istekte hangi rol oynadığını tam olarak görebilirsiniz. İzleme günlüklerinde, `EasyAuthModule_32/64`adlı bir modüle başvuruları arayın. 

## <a name="identity-providers"></a>Kimlik sağlayıcıları

App Service, üçüncü taraf bir kimlik sağlayıcısının sizin için Kullanıcı kimliklerini ve kimlik doğrulama akışını yönettiği [Federal Kimlik](https://en.wikipedia.org/wiki/Federated_identity)kullanır. Beş kimlik sağlayıcısı varsayılan olarak kullanılabilir: 

| Sağlayıcı | Oturum açma uç noktası |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft Hesabı](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Kimlik doğrulama ve yetkilendirmeyi bu sağlayıcılardan biriyle etkinleştirdiğinizde, oturum açma uç noktası Kullanıcı kimlik doğrulaması ve sağlayıcıdan kimlik doğrulama belirteçleri doğrulaması için kullanılabilir. Kullanıcılarınıza dilediğiniz sayıda bu oturum açma seçeneğini kolayca sağlayabilirsiniz. Ayrıca, başka bir kimlik sağlayıcısını veya [kendi özel kimlik çözümünüzü][custom-auth]tümleştirebilirsiniz.

## <a name="authentication-flow"></a>Kimlik doğrulama akışı

Kimlik doğrulama akışı tüm sağlayıcılar için aynıdır, ancak sağlayıcının SDK 'Sı ile oturum açmak isteyip istemediğinize bağlı olarak farklılık gösterir:

- Sağlayıcı SDK 'Sı olmadan: uygulama App Service için Federasyon oturum açma temsilcisi sağlar. Bu durum genellikle, sağlayıcının oturum açma sayfasını kullanıcıya sunmanızı sağlayan tarayıcı uygulamaları ile aynıdır. Sunucu kodu, oturum açma işlemini yönetir, bu nedenle _sunucu yönlendirmeli akış_ veya _sunucu akışı_olarak da adlandırılır. Bu durum tarayıcı uygulamaları için geçerlidir. SDK, kullanıcıların App Service kimlik doğrulamasıyla oturum açmasını sağlamak üzere bir Web görünümü açtığından Mobile Apps istemci SDK 'sını kullanarak kullanıcıları imzalayasağlayan yerel uygulamalar için de geçerlidir. 
- Sağlayıcı SDK ile: uygulama, kullanıcıları sağlayıcıya el ile imzalar ve doğrulama için App Service kimlik doğrulama belirtecini gönderir. Bu durum genellikle, sağlayıcının oturum açma sayfasını kullanıcıya sunamıyoruz ve tarayıcı tarafından daha az uygulamalar için kullanılır. Uygulama kodu, oturum açma işlemini yönetir, bu nedenle _istemci yönlendirmeli akış_ veya _istemci akışı_olarak da adlandırılır. Bu durum REST API 'Leri, [Azure işlevleri](../azure-functions/functions-overview.md)ve JavaScript tarayıcı istemcileri için, oturum açma sürecinde daha fazla esneklik gerektiren tarayıcı uygulamaları için de geçerlidir. Ayrıca, sağlayıcının SDK 'sını kullanarak kullanıcıları imzalayasağlayan yerel mobil uygulamalar için de geçerlidir.

> [!NOTE]
> App Service bir güvenilen tarayıcı uygulamasından yapılan çağrılar, App Service veya [Azure işlevlerinde](../azure-functions/functions-overview.md) başka bir REST API çağırarak sunucu ile yönlendirilen Flow kullanılarak doğrulanabilir. Daha fazla bilgi için bkz. [App Service kimlik doğrulaması ve yetkilendirmeyi özelleştirme](app-service-authentication-how-to.md).
>

Aşağıdaki tabloda, kimlik doğrulama akışı adımları gösterilmektedir.

| Adım | Sağlayıcı SDK 'Sı olmadan | Sağlayıcı SDK ile |
| - | - | - |
| 1. Kullanıcı oturum açma | İstemciyi `/.auth/login/<provider>`yönlendirir. | İstemci kodu, kullanıcıyı doğrudan sağlayıcının SDK 'Sı ile imzalar ve bir kimlik doğrulama belirteci alır. Bilgi için sağlayıcının belgelerine bakın. |
| 2. kimlik doğrulama sonrası | Sağlayıcı istemciyi `/.auth/login/<provider>/callback`yönlendirir. | İstemci kodu, doğrulama için [sağlayıcıdan `/.auth/login/<provider>` belirteç gönderir](app-service-authentication-how-to.md#validate-tokens-from-providers) . |
| 3. kimliği doğrulanmış oturum oluşturma | App Service, yanıta kimliği doğrulanmış tanımlama bilgisi ekler. | App Service, kendi kimlik doğrulama belirtecini istemci koduna döndürür. |
| 4. kimliği doğrulanmış içerik hizmeti | İstemci sonraki isteklerde kimlik doğrulama tanımlama bilgisi içerir (otomatik olarak tarayıcı tarafından işlenir). | İstemci kodu `X-ZUMO-AUTH` üst bilgisinde kimlik doğrulama belirteci sunar (Mobile Apps istemci SDK 'Ları tarafından otomatik olarak işlenir). |

İstemci tarayıcıları için, App Service tüm kimliği doğrulanmamış kullanıcıları otomatik olarak `/.auth/login/<provider>`yönlendirebilir. Ayrıca, bir veya daha fazla `/.auth/login/<provider>` bağlantısı olan kullanıcılara kendi tercih ettiği sağlayıcıyı kullanarak uygulamanızdaki oturum açma olanağı da sunabilirsiniz.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Yetkilendirme davranışı

[Azure Portal](https://portal.azure.com), gelen isteğin kimliği doğrulanmamış bir dizi davranışla App Service yetkilendirmeyi yapılandırabilirsiniz.

![](media/app-service-authentication-overview/authorization-flow.png)

Aşağıdaki başlıklar seçenekleri anlatmaktadır.

### <a name="allow-anonymous-requests-no-action"></a>Anonim isteklere izin ver (eylem yok)

Bu seçenek, uygulama kodunuza kimliği doğrulanmamış trafik yetkilendirmesini erteler. Kimliği doğrulanmış istekler için, App Service HTTP üstbilgilerinde kimlik doğrulama bilgileri boyunca de geçirilir. 

Bu seçenek, anonim istekleri işlemek için daha fazla esneklik sağlar. Örneğin, kullanıcılarınıza [birden çok oturum açma sağlayıcısı sunmanıza](app-service-authentication-how-to.md#use-multiple-sign-in-providers) olanak tanır. Ancak, kod yazmanız gerekir. 

### <a name="allow-only-authenticated-requests"></a>Yalnızca kimliği doğrulanmış isteklere izin ver

Seçeneği **\<sağlayıcısı > oturum açın**. App Service, tüm anonim istekleri seçtiğiniz sağlayıcı için `/.auth/login/<provider>` yönlendirir. Anonim istek yerel bir mobil uygulamadan geliyorsa, döndürülen yanıt bir `HTTP 401 Unauthorized`.

Bu seçenekle, uygulamanızda herhangi bir kimlik doğrulama kodu yazmanız gerekmez. Role özgü yetkilendirme gibi daha ayrıntılı yetkilendirme, kullanıcının taleplerini inceleyerek (bkz. [erişim kullanıcı talepleri](app-service-authentication-how-to.md#access-user-claims)) işlenebilir.

> [!CAUTION]
> Erişimin bu şekilde kısıtlanması, uygulamanıza yönelik tüm çağrılar için geçerlidir. Bu, birçok tek sayfalı uygulamalarda olduğu gibi genel kullanıma açık bir giriş sayfası gerektiren uygulamalar için istenmeyebilir.

## <a name="more-resources"></a>Daha fazla kaynak

[Öğretici: kullanıcıların Azure App Service (Windows) ile uçtan uca kimlik doğrulama ve yetkilendirme](app-service-web-tutorial-auth-aad.md)  
[Öğretici: Linux için Azure App Service Kullanıcı tarafından uçtan uca kimlik doğrulama ve yetkilendirme](containers/tutorial-auth-aad.md)  
[App Service kimlik doğrulaması ve yetkilendirmeyi özelleştirme](app-service-authentication-how-to.md)

Sağlayıcıya özgü nasıl yapılır Kılavuzu:

* [Uygulamanızı Azure Active Directory oturum açma bilgilerini kullanacak şekilde yapılandırma][AAD]
* [Uygulamanızı Facebook oturum açma bilgilerini kullanacak şekilde yapılandırma][Facebook]
* [Uygulamanızı Google oturum açma bilgilerini kullanacak şekilde yapılandırma][Google]
* [Uygulamanızı Microsoft Hesabı oturum açma bilgilerini kullanacak şekilde yapılandırma][MSA]
* [Uygulamanızı Twitter oturum açma bilgilerini kullanacak şekilde yapılandırma][Twitter]
* [Nasıl yapılır: uygulamanız için özel kimlik doğrulaması kullanma][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
