---
title: Ulusal bulutlarda Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanma-Microsoft Identity platform
description: Microsoft kimlik doğrulama kitaplığı (MSAL), uygulama geliştiricilerinin güvenli Web API 'Lerini çağırmak için belirteçleri almasına olanak sağlar. Bu Web API 'Leri Microsoft Graph, diğer Microsoft API 'leri, iş ortağı Web API 'Leri veya kendi Web API 'niz olabilir. MSAL birden çok uygulama mimarilerini ve platformunu destekler.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97855a52831a63a92a46bd0d25d23ba3fc91a07b
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268571"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Ulusal bir bulut ortamında MSAL kullanma

[Ulusal bulutlar](authentication-national-cloud.md) , Azure 'un fiziksel olarak yalıtılmış örnekleridir. Azure yardım 'ın bu bölgeleri, verilerin yer aldığı, egemenlik ve uyumluluk gereksinimlerinin coğrafi sınırlar içinde kabul edilen şekilde sağlandığından emin olmanızı sağlamak.

Microsoft kimlik doğrulama kitaplığı (MSAL), Microsoft Dünya çapındaki buluta ek olarak, Ulusal bulutlar 'daki uygulama geliştiricilerinin, güvenli Web API 'Lerini doğrulamak ve çağırmak için belirteçleri almasına olanak sağlar. Bu Web API 'Leri Microsoft Graph veya diğer Microsoft API 'Leri olabilir.

Genel bulut dahil, Azure Active Directory (Azure AD) aşağıdaki Ulusal bulutlara dağıtılır:  

- Azure Kamu
- Azure Çin 21Vianet
- Azure Almanya

Bu kılavuzda, iş ve okul hesaplarında oturum açma, erişim belirteci alma ve [Azure Kamu bulut](https://azure.microsoft.com/global-infrastructure/government/) ORTAMıNDA Microsoft Graph API 'sini çağırma işlemlerinin nasıl yapılacağı gösterilmiştir.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, bu önkoşulları karşıladığınızdan emin olun.

### <a name="choose-the-appropriate-identities"></a>Uygun kimlikleri seçin

[Azure Kamu](https://docs.microsoft.com/azure/azure-government/) uygulamaları, kullanıcıların kimliğini doğrulamak IÇIN Azure AD kamu kimliklerini ve Azure AD ortak kimliklerini kullanabilir. Bu kimliklerden herhangi birini kullanabilmeniz için senaryonuz için hangi yetkili uç noktasını seçmeniz gerektiğine karar vermeniz gerekir:

- Azure AD Genel: Genellikle kuruluşunuzun Office 365 (genel veya GCC) veya başka bir uygulamayı desteklemesi için bir Azure AD ortak kiracısı zaten varsa kullanılır.
- Azure AD kamu: Genellikle kuruluşunuzun Office 365 (GCC High veya DoD) desteği için bir Azure AD kamu kiracısı zaten varsa veya Azure AD kamu 'da yeni bir kiracı oluşturmak için kullanılır.

Karar verdikten sonra, uygulama kaydınızı gerçekleştirdiğiniz özel bir noktadır. Azure Kamu uygulamanız için Azure AD ortak kimlikleri ' ni seçerseniz, uygulamayı Azure AD Genel kiracınıza kaydetmeniz gerekir.

### <a name="get-an-azure-government-subscription"></a>Azure Kamu aboneliği edinme

Azure Kamu aboneliği almak için bkz. [Azure Kamu 'da aboneliğinizi yönetme ve ile bağlanma](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Azure Kamu aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/global-infrastructure/government/request/) oluşturun.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>1\. adım: Uygulamanızı kaydedin

1. [Azure Portal](https://portal.azure.us/) oturum açın.
    
   Diğer ulusal bulutlar için Azure portal uç noktaları bulmak için bkz. [uygulama kayıt uç noktaları](authentication-national-cloud.md#app-registration-endpoints).

1. Hesabınız birden fazla kiracıya erişim veriyorsa, sağ üst köşede hesabınızı seçin ve Portal oturumunuzu istenen Azure AD kiracısı olarak ayarlayın.
1. Geliştiriciler için Microsoft Identity platformunda [uygulama kayıtları](https://aka.ms/ra/ff) sayfasına gidin.
1. **Bir uygulamayı kaydet** sayfası göründüğünde, uygulamanız için bir ad girin.
1. **Desteklenen hesap türleri**altında, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** bölümünde **Web** platformu ' nu seçin ve değeri Web sunucunuza göre uygulamanın URL 'si olarak ayarlayın. Visual Studio ve Node 'da yeniden yönlendirme URL 'sini ayarlama ve alma hakkında yönergeler için sonraki bölümlere bakın.
1. **Kaydol**’u seçin.
1. Uygulamaya **genel bakış** sayfasında, **uygulama (istemci) kimliği** değerini aklınızda edin.
1. Bu öğretici, [örtük izin akışını](v2-oauth2-implicit-grant-flow.md)etkinleştirmenizi gerektirir. Kayıtlı uygulamanın sol bölmesinde **kimlik doğrulaması**' nı seçin.
1. **Gelişmiş ayarlar**' da, **örtük izin**' ın altında, **Kimlik belirteçleri** ve **erişim belirteçleri** onay kutularını seçin. KIMLIK belirteçleri ve erişim belirteçleri gereklidir çünkü bu uygulamanın kullanıcıları oturum açması ve bir API çağırması gerekir.
1. **Kaydet**’i seçin.

### <a name="step-2--set-up-your-web-server-or-project"></a>2\. adım:  Web sunucunuzu veya projenizi ayarlama

- Düğüm gibi yerel bir Web sunucusu için [Proje dosyalarını indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) .

  or

- [Visual Studio projesini indirin](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Ardından, kod örneğini çalıştırmadan önce yapılandırmak için [JAVASCRIPT Spa 'Nizi yapılandırma](#step-4-configure-your-javascript-spa) bölümüne atlayın.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>3\. adım: Kullanıcı oturumu açmak için Microsoft kimlik doğrulama kitaplığını kullanma

Projenizi oluşturmak için [JavaScript öğreticisindeki](tutorial-v2-javascript-spa.md#create-your-project) adımları izleyin ve Kullanıcı oturumu açmak için msal ile tümleştirin.

### <a name="step-4-configure-your-javascript-spa"></a>4\. Adım: JavaScript SPA 'nizi yapılandırma

Proje kurulumu sırasında oluşturulan dosyada,uygulamakayıtbilgileriniekleyin.`index.html` Aşağıdaki kodu, `<script></script>` `index.html` dosyanızın gövdesinde yer alan etiketlerin içine ekleyin:

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

- `Enter_the_Application_Id_here`, kaydettiğiniz uygulamanın **uygulama (istemci) kimlik** değeridir.
- `Enter_the_Tenant_Info_Here`Aşağıdaki seçeneklerden birine ayarlanır:
    - Uygulamanız **bu kuruluş dizinindeki hesapları**destekliyorsa, bu DEĞERI Kiracı kimliği veya kiracı adı (örneğin, contoso.Microsoft.com) ile değiştirin.
    - Uygulamanız **herhangi bir kuruluş dizinindeki hesapları**destekliyorsa, bu değeri ile `organizations`değiştirin.
    
    Tüm ulusal bulutların kimlik doğrulama uç noktalarını bulmak için bkz. [Azure AD kimlik doğrulama uç noktaları](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Kişisel Microsoft hesapları Ulusal bulutlarda desteklenmez.
  
- `graphEndpoint`, ABD kamu için Microsoft bulutu 'nın Microsoft Graph uç noktasıdır.

   Tüm ulusal bulutlar için Microsoft Graph uç noktaları bulmak için bkz. [National bulutlar içindeki Microsoft Graph uç noktaları](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Kullanıcıları oturum açmak, belirteçleri almak ve Microsoft Graph API 'sini Ulusal bulutlarda çağırmak için MSAL.NET kullanabilirsiniz.

Aşağıdaki öğreticilerde .NET Core 2,2 MVC web uygulamasının nasıl oluşturulacağı gösterilmektedir. Uygulama, ulusal bir buluta ait bir kuruluştaki iş ve okul hesabıyla kullanıcıları oturum açmak için OpenID Connect kullanır.

- Kullanıcıları oturum açmak ve belirteçleri almak için [Bu öğreticiyi](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)izleyin.
- Microsoft Graph API 'sini çağırmak için [Bu öğreticiyi](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)izleyin.

## <a name="msal-for-ios-and-macos"></a>İOS ve macOS için MSAL

İOS ve macOS için MSAL, Ulusal bulutlarda belirteçleri almak için kullanılabilir, ancak oluştururken `MSALPublicClientApplication`ek yapılandırma gerektirir.

Örneğin, uygulamanızın ulusal bir bulutta (burada ABD devlet) çok kiracılı bir uygulama olmasını istiyorsanız şunu yazabilirsiniz:

Amaç-C:

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

SWIFT

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi:

- [Azure Devlet Kurumları](https://docs.microsoft.com/azure/azure-government/)
- [Azure Çin 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Almanya](https://docs.microsoft.com/azure/germany/)
