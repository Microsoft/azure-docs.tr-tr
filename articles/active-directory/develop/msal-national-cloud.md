---
title: Ulusal bir bulut uygulamasında MSAL kullanma | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft kimlik doğrulama kitaplığı (MSAL), uygulama geliştiricilerinin güvenli Web API 'Lerini çağırmak için belirteçleri almasına olanak sağlar. Bu Web API 'Leri Microsoft Graph, diğer Microsoft API 'leri, iş ortağı Web API 'Leri veya kendi Web API 'niz olabilir. MSAL birden çok uygulama mimarilerini ve platformunu destekler.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: marsma, nacanuma
ms.custom: aaddev
ms.openlocfilehash: 09c4dadd7a6560bd5163d623dd8a7f247b57860e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102504"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Ulusal bir bulut ortamında MSAL kullanma

Sovereign bulutu olarak da bilinen [Ulusal bulutlar](authentication-national-cloud.md), fiziksel olarak yalıtılmış Azure örnekleridir. Azure yardım 'ın bu bölgeleri, verilerin yer aldığı, egemenlik ve uyumluluk gereksinimlerinin coğrafi sınırlar içinde kabul edilen şekilde sağlandığından emin olmanızı sağlamak.

Microsoft kimlik doğrulama kitaplığı (MSAL), Microsoft Dünya çapındaki buluta ek olarak, Ulusal bulutlar 'daki uygulama geliştiricilerinin, güvenli Web API 'Lerini doğrulamak ve çağırmak için belirteçleri almasına olanak sağlar. Bu Web API 'Leri Microsoft Graph veya diğer Microsoft API 'Leri olabilir.

Genel bulut dahil, Azure Active Directory (Azure AD) aşağıdaki Ulusal bulutlara dağıtılır:  

- Azure Kamu
- Azure China 21Vianet
- Azure Almanya

Bu kılavuzda, iş ve okul hesaplarında oturum açma, erişim belirteci alma ve [Azure Kamu bulut](https://azure.microsoft.com/global-infrastructure/government/) ORTAMıNDA Microsoft Graph API 'sini çağırma işlemlerinin nasıl yapılacağı gösterilmiştir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, bu önkoşulları karşıladığınızdan emin olun.

### <a name="choose-the-appropriate-identities"></a>Uygun kimlikleri seçin

[Azure Kamu](../../azure-government/index.yml) uygulamaları, kullanıcıların kimliğini doğrulamak IÇIN Azure AD kamu kimliklerini ve Azure AD ortak kimliklerini kullanabilir. Bu kimliklerden herhangi birini kullanabilmeniz için senaryonuz için hangi yetkili uç noktasını seçmeniz gerektiğine karar verin:

- Azure AD Genel: genellikle kuruluşunuzun Microsoft 365 (public veya GCC) veya başka bir uygulamayı desteklemesi için bir Azure AD ortak kiracıya sahip olması halinde kullanılır.
- Azure AD kamu: genellikle Kuruluşunuzda Office 365 (GCC High veya DoD) desteği için bir Azure AD kamu kiracısı zaten varsa veya Azure AD kamu 'da yeni bir kiracı oluşturmak için kullanılır.

Karar verdikten sonra, uygulama kaydınızı gerçekleştirdiğiniz özel bir noktadır. Azure Kamu uygulamanız için Azure AD ortak kimlikleri ' ni seçerseniz, uygulamayı Azure AD Genel kiracınıza kaydetmeniz gerekir.

### <a name="get-an-azure-government-subscription"></a>Azure Kamu aboneliği edinme

Azure Kamu aboneliği almak için bkz. [Azure Kamu 'da aboneliğinizi yönetme ve ile bağlanma](../../azure-government/compare-azure-government-global-azure.md).

Azure Kamu aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/global-infrastructure/government/request/) oluşturun.

Belirli bir programlama diliyle ulusal bir bulut kullanma hakkında ayrıntılı bilgi için, diliniz ile eşleşen sekmeyi seçin:

## <a name="net"></a>[.NET](#tab/dotnet)

Kullanıcıları oturum açmak, belirteçleri almak ve Microsoft Graph API 'sini Ulusal bulutlarda çağırmak için MSAL.NET kullanabilirsiniz.

Aşağıdaki öğreticilerde .NET Core 2,2 MVC web uygulamasının nasıl oluşturulacağı gösterilmektedir. Uygulama, ulusal bir buluta ait bir kuruluştaki iş ve okul hesabıyla kullanıcıları oturum açmak için OpenID Connect kullanır.

- Kullanıcıların oturum açması ve belirteçleri elde etmek için şu öğreticiyi izleyin: [Microsoft Identity platformu ile yvereign bulutlarında ASP.NET Core Web uygulaması oturumu açma](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Microsoft Graph API 'sini çağırmak için bu öğreticiyi izleyin: Microsoft [Identity platformunu kullanarak, bir Kullanıcı oturumu açma adına bir ASP.NET Core 2. x Web uygulamasından MICROSOFT Graph API 'sini çağırmak için Microsoft Ulusal bulutta iş ve okul hesabını kullanma](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="javascript"></a>[JavaScript](#tab/javascript)

Bağımsız bulutları için MSAL.js uygulamanızı etkinleştirmek için:

### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme

1. <a href="https://portal.azure.us/" target="_blank">Azure portalında</a> oturum açın.

   Diğer ulusal bulutlar için Azure portal uç noktaları bulmak için bkz. [uygulama kayıt uç noktaları](authentication-national-cloud.md#app-registration-endpoints).

1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin. Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Desteklenen hesap türleri** altında, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** bölümünde **Web** platformu ' nu seçin ve değeri Web sunucunuza göre uygulamanın URL 'si olarak ayarlayın. Visual Studio ve Node 'da yeniden yönlendirme URL 'sini ayarlama ve alma hakkında yönergeler için sonraki bölümlere bakın.
1. **Kaydet**’i seçin.
1. **Genel bakış** sayfasında, daha sonra kullanmak üzere **uygulama (istemci) kimliği** değerini aklınızda edin.
    Bu öğretici, [örtük izin akışını](v2-oauth2-implicit-grant-flow.md)etkinleştirmenizi gerektirir. 
1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Örtük verme ve karma akışlar** altında **Kimlik belirteçleri** ve **erişim belirteçleri**' ni seçin. KIMLIK belirteçleri ve erişim belirteçleri gereklidir çünkü bu uygulamanın kullanıcıları oturum açması ve bir API çağırması gerekir.
1. **Kaydet**’i seçin.

### <a name="step-2--set-up-your-web-server-or-project"></a>2. Adım: Web sunucunuzu veya projenizi ayarlama

- Düğüm gibi yerel bir Web sunucusu için [Proje dosyalarını indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) .

  veya

- [Visual Studio projesini indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Ardından, kod örneğini çalıştırmadan önce yapılandırmak için [JAVASCRIPT Spa 'Nizi yapılandırma](#step-4-configure-your-javascript-spa) bölümüne atlayın.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>3. Adım: Kullanıcı oturumu açmak için Microsoft kimlik doğrulama kitaplığını kullanma

Projenizi oluşturmak için [JavaScript öğreticisindeki](tutorial-v2-javascript-spa.md#create-your-project) adımları izleyin ve Kullanıcı oturumu açmak için msal ile tümleştirin.

### <a name="step-4-configure-your-javascript-spa"></a>4. Adım: JavaScript SPA 'nizi yapılandırma

`index.html`Proje kurulumu sırasında oluşturulan dosyada, uygulama kayıt bilgilerini ekleyin. Aşağıdaki kodu, `<script></script>` dosyanızın gövdesinde yer alan etiketlerin içine ekleyin `index.html` :

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Bu kodda:

- `Enter_the_Application_Id_here` , kaydettiğiniz uygulamanın **uygulama (istemci) kimlik** değeridir.
- `Enter_the_Tenant_Info_Here` Aşağıdaki seçeneklerden birine ayarlanır:
    - Uygulamanız **bu kuruluş dizinindeki hesapları** destekliyorsa, bu DEĞERI Kiracı kimliği veya kiracı adı (örneğin, contoso.Microsoft.com) ile değiştirin.
    - Uygulamanız **herhangi bir kuruluş dizinindeki hesapları** destekliyorsa, bu değeri ile değiştirin `organizations` .

    Tüm ulusal bulutların kimlik doğrulama uç noktalarını bulmak için bkz. [Azure AD kimlik doğrulama uç noktaları](./authentication-national-cloud.md#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Kişisel Microsoft hesapları Ulusal bulutlarda desteklenmez.

- `graphEndpoint` , ABD kamu için Microsoft bulutu 'nın Microsoft Graph uç noktasıdır.

   Tüm ulusal bulutlar için Microsoft Graph uç noktaları bulmak için bkz. [National bulutlar içindeki Microsoft Graph uç noktaları](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="python"></a>[Python](#tab/python)

MSAL Python uygulamanızı bağımsız bulutlara yönelik olarak etkinleştirmek için:

- Uygulamanızı buluta bağlı olarak belirli bir portala kaydedin. Portalı seçme hakkında daha fazla bilgi için [uygulama kayıt uç noktalarına](authentication-national-cloud.md#app-registration-endpoints) başvurun
- Sonraki bir adımda belirtilen buluta bağlı olarak, yapılandırma üzerinde birkaç değişiklik ile depodaki [örneklerden](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) herhangi birini kullanın.
- Uygulamayı kaydettiğiniz buluta bağlı olarak belirli bir yetkili kullanın. Farklı bulutlara yönelik yetkililer hakkında daha fazla bilgi için [Azure AD kimlik doğrulama uç noktalarına](authentication-national-cloud.md#azure-ad-authentication-endpoints)bakın.

    Örnek bir yetkili aşağıda verilmiştir:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Microsoft Graph API 'sinin çağrılması, kullanmakta olduğunuz buluta özel bir uç nokta URL 'SI gerektirir. Tüm ulusal bulutların Microsoft Graph uç noktalarını bulmak için [Microsoft Graph ve Graph Explorer hizmeti kök uç noktaları](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)' na bakın.

    Kapsama sahip Microsoft Graph uç noktası örneği aşağıda verilmiştir:

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

Bağımsız bulutları için MSAL Java uygulamanızı etkinleştirmek için:

- Uygulamanızı buluta bağlı olarak belirli bir portala kaydedin. Portalı seçme hakkında daha fazla bilgi için [uygulama kayıt uç noktalarına](authentication-national-cloud.md#app-registration-endpoints) başvurun
- Daha sonra bahsedilen buluta bağlı olarak, yapılandırma üzerinde yapılan birkaç değişiklik ile depodaki [örneklerden](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) herhangi birini kullanın.
- Uygulamayı kaydettiğiniz buluta bağlı olarak belirli bir yetkili kullanın. Farklı bulutlara yönelik yetkililer hakkında daha fazla bilgi için [Azure AD kimlik doğrulama uç noktalarına](authentication-national-cloud.md#azure-ad-authentication-endpoints)bakın.

Örnek bir yetkili aşağıda verilmiştir:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Microsoft Graph API 'sinin çağrılması, kullanmakta olduğunuz buluta özel bir uç nokta URL 'SI gerektirir. Tüm ulusal bulutların Microsoft Graph uç noktalarını bulmak için [Microsoft Graph ve Graph Explorer hizmeti kök uç noktaları](/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)' na bakın.

Aşağıda kapsam içeren bir grafik uç noktası örneği verilmiştir:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

İOS ve macOS için MSAL, Ulusal bulutlarda belirteçleri almak için kullanılabilir, ancak oluştururken ek yapılandırma gerektirir `MSALPublicClientApplication` .

Örneğin, uygulamanızın ulusal bir bulutta (burada ABD devlet) çok kiracılı bir uygulama olmasını istiyorsanız şunu yazabilirsiniz:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

İOS ve macOS için MSAL, Ulusal bulutlarda belirteçleri almak için kullanılabilir, ancak oluştururken ek yapılandırma gerektirir `MSALPublicClientApplication` .

Örneğin, uygulamanızın ulusal bir bulutta (burada ABD devlet) çok kiracılı bir uygulama olmasını istiyorsanız şunu yazabilirsiniz:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Sonraki adımlar

Her bulut için Azure portal URL 'Ler ve belirteç uç noktaları listesi için bkz. [Ulusal bulut kimlik doğrulama uç noktaları](authentication-national-cloud.md) .

Ulusal bulut belgeleri:

- [Azure Devlet Kurumları](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Almanya](../../germany/index.yml)