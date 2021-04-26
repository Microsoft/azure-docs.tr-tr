---
title: Kimlik doğrulaması ve yetkilendirme
description: Azure App Service ve Azure Işlevlerinde yerleşik kimlik doğrulama ve yetkilendirme desteği hakkında bilgi edinin ve uygulamanızın yetkisiz erişime karşı nasıl güvenli hale getirilmesine yardımcı olabilir.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 03/29/2021
ms.reviewer: mahender
ms.custom: seodec18, fasttrack-edit, has-adal-ref
ms.openlocfilehash: 1b6e600fcaf32a115af14be2444144fee099d635
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075347"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Azure App Service ve Azure Işlevlerinde kimlik doğrulama ve yetkilendirme

Azure App Service, yerleşik kimlik doğrulama ve yetkilendirme özellikleri (bazen "kolay kimlik doğrulaması" olarak adlandırılır) sağlar. böylece, Web uygulamanızda, yeniden derlenen API 'de ve mobil arka uçta ve ayrıca [Azure işlevlerinde](../azure-functions/functions-overview.md)en az veya hiç kod yazmadan kullanıcılara oturum açabilir ve verilere erişebilirsiniz. Bu makalede, App Service uygulamanızın kimlik doğrulama ve yetkilendirme işlemlerini basitleştirmeye nasıl yardımcı olduğu açıklanır.

## <a name="why-use-the-built-in-authentication"></a>Yerleşik kimlik doğrulaması neden kullanılmalıdır?

Kimlik doğrulama ve yetkilendirme için bu özelliği kullanmanız gerekli değildir. Seçtiğiniz Web çerçevesindeki paketlenmiş güvenlik özelliklerini kullanabilir veya kendi yardımcı programlarını yazabilirsiniz. Ancak, en son güvenlik, protokol ve tarayıcı güncelleştirmeleriyle çözümünüzün güncel kalmasını sağladığınızdan emin olmanız gerekir.

Kimlik doğrulaması için güvenli bir çözüm (oturum açma kullanıcıları) ve yetkilendirme (güvenli verilere erişim sağlamak) uygulamak çok önemli bir çaba alabilir. Sektörde en iyi uygulamaları ve standartları izlediğinizden emin olmanız ve uygulamanızı güncel tutmanız gerekir. App Service ve Azure Işlevleri için yerleşik kimlik doğrulama özelliği, Federasyon kimlik sağlayıcıları ile kullanıma hazır kimlik doğrulama sağlayarak uygulamanızın geri kalanına odaklanmanızı sağlayarak zaman ve çaba tasarrufu sağlayabilir.

- Azure App Service, çeşitli kimlik doğrulama yeteneklerini Web uygulamanız veya API 'niz için kendi kendinize uygulamadan tümleştirmenize imkan tanır.
- Doğrudan platformda oluşturulmuştur ve belirli bir dil, SDK, güvenlik uzmanlığı veya hatta kullanmak için herhangi bir kod gerektirmez.
- Birden çok oturum açma sağlayıcısıyla tümleştirilebilir. Örneğin, Azure AD, Facebook, Google, Twitter.

## <a name="identity-providers"></a>Kimlik sağlayıcıları

App Service, üçüncü taraf bir kimlik sağlayıcısının sizin için Kullanıcı kimliklerini ve kimlik doğrulama akışını yönettiği [Federal Kimlik](https://en.wikipedia.org/wiki/Federated_identity)kullanır. Aşağıdaki kimlik sağlayıcıları varsayılan olarak kullanılabilir:

| Sağlayıcı | Oturum açma uç noktası | How-To Kılavuzu |
| - | - | - |
| [Microsoft Identity platformu](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` | [App Service Microsoft Identity platform oturumu açma](configure-authentication-provider-aad.md) |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` | [Facebook oturum App Service](configure-authentication-provider-facebook.md) |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` | [Google oturum App Service](configure-authentication-provider-google.md) |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` | [App Service Twitter oturumu açma](configure-authentication-provider-twitter.md) |
| Herhangi bir [OpenID Connect](https://openid.net/connect/) sağlayıcısı (Önizleme) | `/.auth/login/<providerName>` | [App Service OpenID Connect oturum açma](configure-authentication-provider-openid-connect.md) |

Kimlik doğrulama ve yetkilendirmeyi bu sağlayıcılardan biriyle etkinleştirdiğinizde, oturum açma uç noktası Kullanıcı kimlik doğrulaması ve sağlayıcıdan kimlik doğrulama belirteçleri doğrulaması için kullanılabilir. Kullanıcılarınıza, bu oturum açma seçeneklerinin herhangi bir sayısını sağlayabilirsiniz.

## <a name="considerations-for-using-built-in-authentication"></a>Yerleşik kimlik doğrulaması kullanma konuları

Bu özelliği etkinleştirmek, HTTPS 'yi zorlamak için App Service yapılandırma ayarından bağımsız olarak, uygulamanıza yönelik tüm isteklerin otomatik olarak HTTPS 'ye yönlendirilmesini sağlar. Bunu  `requireHttps` v2 yapılandırmasındaki ayarla devre dışı bırakabilirsiniz. Bununla birlikte, HTTPS ile bir çıkartma önereceğiz ve güvenli olmayan HTTP bağlantıları üzerinden hiçbir güvenlik belirtecinin iletilmemesini güvence altına almalısınız.

App Service, site içeriklerinize ve API 'Lerine erişimi kısıtlamadan veya olmadan kimlik doğrulaması için kullanılabilir. Uygulama erişimini yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için, isteğin kimliği doğrulanmamış olarak, yapılandırılmış kimlik sağlayıcılarından biriyle oturum açmak üzere **kimlik doğrulaması yapıldığında gerçekleştirilecek eylemi** ayarlayın. Kimlik doğrulaması yapmak ancak erişimi kısıtlamak için, isteğin kimliği doğrulanmamış "anonim isteklere Izin ver (eylem yok)" olarak **doğrulanmadığında gerçekleştirilecek eylemi** ayarlayın.

> [!NOTE]
> Her uygulamaya kendi iznini ve onayını vermeniz gerekir. Ayrı dağıtım yuvaları için ayrı uygulama kayıtları kullanarak ortamlar arasında izin paylaşımını önleyin. Yeni kodu sınarken, bu uygulama sorunların üretim uygulamasını etkilemesini önlemeye yardımcı olabilir.

## <a name="how-it-works"></a>Nasıl çalışır?

[Windows üzerinde özellik mimarisi (kapsayıcı olmayan dağıtım)](#feature-architecture-on-windows-non-container-deployment))

[Linux ve kapsayıcılarda Özellik mimarisi](#feature-architecture-on-linux-and-containers)

[Kimlik doğrulama akışı](#authentication-flow)

[Yetkilendirme davranışı](#authorization-behavior)

[Kullanıcı ve uygulama talepleri](#user-and-application-claims)

[Belirteç deposu](#token-store)

[Günlüğe kaydetme ve izleme](#logging-and-tracing)

#### <a name="feature-architecture-on-windows-non-container-deployment"></a>Windows üzerinde özellik mimarisi (kapsayıcı olmayan dağıtım)

Kimlik doğrulama ve yetkilendirme modülü, uygulama kodunuzla aynı korumalı alanda çalışır. Etkin olduğunda, her gelen HTTP isteği, uygulama kodunuz tarafından işlenemeden önce üzerinden geçirilir.

:::image type="content" source="media/app-service-authentication-overview/architecture.png" alt-text="Dağıtılan sitede trafiğe izin vermeden önce kimlik sağlayıcılarıyla etkileşen site korumalı bir işlem tarafından kesilen istekleri gösteren bir mimari diyagramı" lightbox="media/app-service-authentication-overview/architecture.png":::

Bu modül, uygulamanız için çeşitli şeyleri işler:

- Belirtilen sağlayıcıyla kullanıcıların kimliğini doğrular
- Belirteçleri doğrular, depolar ve yeniler
- Kimliği doğrulanmış oturumu yönetir
- Kimlik bilgilerini istek üst bilgilerine ekler

Modül, uygulama kodınızdan ayrı olarak çalışır ve uygulama ayarları kullanılarak yapılandırılır. SDK, belirli dil veya uygulama kodunuzda değişiklik yapılması gerekmez. 

#### <a name="feature-architecture-on-linux-and-containers"></a>Linux ve kapsayıcılarda Özellik mimarisi

Kimlik doğrulama ve yetkilendirme modülü, uygulama kodınızdan yalıtılmış ayrı bir kapsayıcıda çalışır. [Amelçi deseninin](/azure/architecture/patterns/ambassador)bilindiğinin kullanılması, Windows 'da olduğu gibi benzer işlevleri gerçekleştirmek üzere gelen trafikle etkileşime girer. İşlem içinde çalıştırılmadığından, belirli dil çerçeveleri ile doğrudan tümleştirme mümkün değildir; Ancak, uygulamanızın ihtiyaç duyacağı ilgili bilgiler, aşağıda açıklandığı gibi istek üstbilgileri kullanılarak geçirilir.

#### <a name="authentication-flow"></a>Kimlik doğrulaması akışı

Kimlik doğrulama akışı tüm sağlayıcılar için aynıdır, ancak sağlayıcının SDK 'Sı ile oturum açmak isteyip istemediğinize bağlı olarak farklılık gösterir:

- Sağlayıcı SDK 'Sı olmadan: uygulama App Service için Federasyon oturum açma temsilcisi sağlar. Bu durum genellikle, sağlayıcının oturum açma sayfasını kullanıcıya sunmanızı sağlayan tarayıcı uygulamaları ile aynıdır. Sunucu kodu, oturum açma işlemini yönetir, bu nedenle _sunucu yönlendirmeli akış_ veya _sunucu akışı_ olarak da adlandırılır. Bu durum tarayıcı uygulamaları için geçerlidir. SDK, kullanıcıların App Service kimlik doğrulamasıyla oturum açmasını sağlamak üzere bir Web görünümü açtığından Mobile Apps istemci SDK 'sını kullanarak kullanıcıları imzalayasağlayan yerel uygulamalar için de geçerlidir.
- Sağlayıcı SDK ile: uygulama, kullanıcıları sağlayıcıya el ile imzalar ve doğrulama için App Service kimlik doğrulama belirtecini gönderir. Bu durum genellikle, sağlayıcının oturum açma sayfasını kullanıcıya sunamıyoruz ve tarayıcı tarafından daha az uygulamalar için kullanılır. Uygulama kodu, oturum açma işlemini yönetir, bu nedenle _istemci yönlendirmeli akış_ veya _istemci akışı_ olarak da adlandırılır. Bu durum REST API 'Leri, [Azure işlevleri](../azure-functions/functions-overview.md)ve JavaScript tarayıcı istemcileri için, oturum açma sürecinde daha fazla esneklik gerektiren tarayıcı uygulamaları için de geçerlidir. Ayrıca, sağlayıcının SDK 'sını kullanarak kullanıcıları imzalayasağlayan yerel mobil uygulamalar için de geçerlidir.

App Service bir güvenilen tarayıcı uygulamasından App Service veya [Azure işlevlerinde](../azure-functions/functions-overview.md) başka bir REST API olan çağrılar, sunucu ile yönlendirilen Flow kullanılarak doğrulanabilir. Daha fazla bilgi için bkz. [App Service kimlik doğrulaması ve yetkilendirmeyi özelleştirme](app-service-authentication-how-to.md).

Aşağıdaki tabloda, kimlik doğrulama akışı adımları gösterilmektedir.

| Adım | Sağlayıcı SDK 'Sı olmadan | Sağlayıcı SDK ile |
| - | - | - |
| 1. Kullanıcı oturum açma | İstemcisini olarak yeniden yönlendirir `/.auth/login/<provider>` . | İstemci kodu, kullanıcıyı doğrudan sağlayıcının SDK 'Sı ile imzalar ve bir kimlik doğrulama belirteci alır. Bilgi için sağlayıcının belgelerine bakın. |
| 2. kimlik doğrulama sonrası | Sağlayıcı istemciyi öğesine yeniden yönlendirir `/.auth/login/<provider>/callback` . | İstemci kodu, sağlayıcıya doğrulama için [olan belirteci gönderir](app-service-authentication-how-to.md#validate-tokens-from-providers) `/.auth/login/<provider>` . |
| 3. kimliği doğrulanmış oturum oluşturma | App Service, yanıta kimliği doğrulanmış tanımlama bilgisi ekler. | App Service, kendi kimlik doğrulama belirtecini istemci koduna döndürür. |
| 4. kimliği doğrulanmış içerik hizmeti | İstemci sonraki isteklerde kimlik doğrulama tanımlama bilgisi içerir (otomatik olarak tarayıcı tarafından işlenir). | İstemci kodu, kimlik doğrulama belirtecini `X-ZUMO-AUTH` üst bilgide gösterir (Mobile Apps Istemci SDK 'ları tarafından otomatik olarak işlenir). |

İstemci tarayıcıları için, App Service tüm kimliği doğrulanmamış kullanıcıları otomatik olarak öğesine yönlendirebilir `/.auth/login/<provider>` . Ayrıca, bir veya daha fazla bağlantıyla kullanıcılara `/.auth/login/<provider>` tercih ettiğiniz sağlayıcıyı kullanarak oturum açmak için bir veya daha fazla bağlantı sunabilirsiniz.

<a name="authorization"></a>

#### <a name="authorization-behavior"></a>Yetkilendirme davranışı

[Azure Portal](https://portal.azure.com), gelen isteğin kimliği doğrulanmamış bir dizi davranışa sahip App Service yapılandırabilirsiniz. Aşağıdaki başlıklar seçenekleri anlatmaktadır.

**Kimliği doğrulanmamış isteklere izin ver**

Bu seçenek, uygulama kodunuza kimliği doğrulanmamış trafik yetkilendirmesini erteler. Kimliği doğrulanmış istekler için, App Service HTTP üstbilgilerinde kimlik doğrulama bilgileri boyunca de geçirilir.

Bu seçenek, anonim istekleri işlemek için daha fazla esneklik sağlar. Örneğin, kullanıcılarınıza [birden çok oturum açma sağlayıcısı sunmanıza](app-service-authentication-how-to.md#use-multiple-sign-in-providers) olanak tanır. Ancak, kod yazmanız gerekir.

**Kimlik doğrulaması gerektir**

Bu seçenek, uygulamanıza yönelik tüm kimliği doğrulanmamış trafiği reddeder. Bu reddetme, yapılandırılmış kimlik sağlayıcılarından birine bir yeniden yönlendirme eylemi olabilir. Bu durumlarda, bir tarayıcı istemcisi seçtiğiniz sağlayıcıya yeniden yönlendirilir `/.auth/login/<provider>` . Anonim istek yerel bir mobil uygulamadan geliyorsa, döndürülen yanıt bir olur `HTTP 401 Unauthorized` . Reddetme `HTTP 401 Unauthorized` isteğini, tüm istekler için veya olarak da yapılandırabilirsiniz `HTTP 403 Forbidden` .

Bu seçenekle, uygulamanızda herhangi bir kimlik doğrulama kodu yazmanız gerekmez. Role özgü yetkilendirme gibi daha ayrıntılı yetkilendirme, kullanıcının taleplerini inceleyerek (bkz. [erişim kullanıcı talepleri](app-service-authentication-how-to.md#access-user-claims)) işlenebilir.

> [!CAUTION]
> Erişimin bu şekilde kısıtlanması, uygulamanıza yönelik tüm çağrılar için geçerlidir. Bu, birçok tek sayfalı uygulamalarda olduğu gibi genel kullanıma açık bir giriş sayfası gerektiren uygulamalar için istenmeyebilir.

> [!NOTE]
> Varsayılan olarak, Azure AD kiracınızdaki herhangi bir Kullanıcı Azure AD 'den uygulamanız için bir belirteç talep edebilir. Uygulamanıza erişimi tanımlı bir Kullanıcı kümesiyle kısıtlamak istiyorsanız [, Azure AD 'de uygulamayı yapılandırabilirsiniz](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) .


#### <a name="user-and-application-claims"></a>Kullanıcı ve uygulama talepleri

Tüm dil çerçeveleri için App Service, gelen belirteçteki talepleri (kimliği doğrulanmış bir son kullanıcının veya bir istemci uygulamasından mi olsun), istek üst bilgilerine ekleme göre kodunuzun kullanımına sunar. ASP.NET 4,6 uygulamaları için App Service [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) 'ı kimliği doğrulanmış kullanıcının taleplerine göre doldurur. böylece, özniteliği de dahil olmak üzere standart .NET kod modelini izleyebilirsiniz `[Authorize]` . Benzer şekilde, PHP uygulamaları için App Service değişkeni doldurur `_SERVER['REMOTE_USER']` . Java uygulamaları için, talepler [Tomcat servlet 'ten erişilebilir](configure-language-java.md#authenticate-users-easy-auth).

[Azure işlevleri](../azure-functions/functions-overview.md)için, `ClaimsPrincipal.Current` .NET kodu için doldurulmamış, ancak istek üst bilgilerinde kullanıcı taleplerini bulabilir veya istek `ClaimsPrincipal` bağlamından ya da bir bağlama parametresi aracılığıyla nesne alabilirsiniz. Daha fazla bilgi için bkz. [istemci kimlikleriyle çalışma](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) .

Daha fazla bilgi için bkz. [Kullanıcı taleplerine erişme](app-service-authentication-how-to.md#access-user-claims).

Şu anda ASP.NET Core, geçerli kullanıcıyı kimlik doğrulama/yetkilendirme özelliğiyle doldurmayı desteklememektedir. Ancak, bu boşluğun doldurulmasına yardımcı olmak için bazı [üçüncü taraf, açık kaynaklı ara yazılım bileşenleri](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth) bulunur.

#### <a name="token-store"></a>Belirteç deposu

App Service, Web uygulamalarınızın, API 'lerin veya yerel mobil uygulamalarınızın kullanıcılarıyla ilişkili belirteçlerin bir deposu olan yerleşik bir belirteç deposu sağlar. Herhangi bir sağlayıcı ile kimlik doğrulamasını etkinleştirdiğinizde, bu belirteç deposu uygulamanız için hemen kullanılabilir. Uygulama kodunuzun Kullanıcı adına bu sağlayıcılardan veriye erişmesi gerekiyorsa, örneğin:

- kimliği doğrulanmış kullanıcının Facebook zaman çizelgesinde gönderi
- Microsoft Graph API 'sini kullanarak kullanıcının şirket verilerini okuyun

Uygulamanızda bu belirteçleri toplamak, depolamak ve yenilemek için genellikle kod yazmanız gerekir. Belirteç deposu ile, ihtiyacınız olduğunda [belirteçleri alır](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) ve geçersiz hale geldiklerinde [bunları yenilemek App Service söyleyin](app-service-authentication-how-to.md#refresh-identity-provider-tokens) . 

KIMLIK belirteçleri, erişim belirteçleri ve yenileme belirteçleri kimliği doğrulanmış oturum için önbelleğe alınır ve yalnızca ilişkili kullanıcı tarafından erişilebilir.  

Uygulamanızdaki belirteçlerle çalışmanız gerekmiyorsa, uygulamanızın **kimlik doğrulama/yetkilendirme** sayfasında belirteç deposunu devre dışı bırakabilirsiniz.

#### <a name="logging-and-tracing"></a>Günlüğe kaydetme ve izleme

[Uygulama günlüğünü etkinleştirirseniz](troubleshoot-diagnostic-logs.md), kimlik doğrulama ve yetkilendirme izlemelerini doğrudan günlük dosyalarınızda görürsünüz. Beklemediğiniz bir kimlik doğrulama hatası görürseniz, mevcut uygulama günlüklerinizi inceleyerek tüm ayrıntıları rahat bir şekilde bulabilirsiniz. [Başarısız istek izlemeyi](troubleshoot-diagnostic-logs.md)etkinleştirirseniz, kimlik doğrulama ve yetkilendirme modülünün başarısız bir istekte hangi rol oynadığını tam olarak görebilirsiniz. İzleme günlüklerinde adlı bir modüle başvuruları arayın `EasyAuthModule_32/64` .

## <a name="more-resources"></a>Diğer kaynaklar

- [Nasıl yapılır: App Service veya Azure Işlevleri uygulamanızı Azure AD oturum açma bilgilerini kullanacak şekilde yapılandırma](configure-authentication-provider-aad.md)
- [Azure App Service 'da gelişmiş kimlik doğrulama ve yetkilendirme kullanımı](app-service-authentication-how-to.md)

Lerinizi
- [Öğretici: Azure App Service üzerinde çalışan Web uygulamanıza kimlik doğrulaması ekleme](scenario-secure-app-authentication-app-service.md)
- [Öğretici: kullanıcıların Azure App Service (Windows veya Linux) uçtan uca kimlik doğrulama ve yetkilendirme](tutorial-auth-aad.md)
- [Azure AppService EasyAuth (3. taraf) .NET Core tümleştirmesi](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
- [.NET Core ile çalışan Azure App Service kimlik doğrulaması alma (3. taraf)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)
