---
title: "Hızlı başlangıç: Microsoft Identity platformu tarafından korunan bir ASP.NET Web API 'SI çağırın | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir Windows Masaüstü (WPF) uygulamasından Microsoft Identity platformu tarafından korunan bir ASP.NET Web API 'SI çağırma hakkında bilgi edinin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 7d802aa83e2d48e7b66980b5c06aece10a93f37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658535"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Hızlı başlangıç: Microsoft Identity platform tarafından korunan bir ASP.NET Web API 'SI çağırma

Bu hızlı başlangıçta, kaynaklarına erişimi yalnızca yetkili hesaplara kısıtlayarak bir ASP.NET Web API 'sinin nasıl korunacağını gösteren bir kod örneği indirip çalıştırırsınız. Örnek, tüm Azure Active Directory (Azure AD) kuruluşunda kişisel Microsoft hesaplarının ve hesaplarının yetkilendirmesini destekler.

Makale Ayrıca bir Web API 'sine erişmek için bir erişim belirteci isteme yöntemini göstermek için bir Windows Presentation Foundation (WPF) uygulaması kullanır.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 veya 2019. [Visual Studio 'yu ücretsiz](https://www.visualstudio.com/downloads/)indirin.

## <a name="clone-or-download-the-sample"></a>Örneği kopyalayın veya indirin

Örneği iki şekilde elde edebilirsiniz:

* Kabuğunu veya komut satırınızdan kopyalayın:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [ZIP dosyası olarak indirin](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

## <a name="register-your-web-api"></a>Web API 'nizi kaydetme

Bu bölümde, Azure portal Web API 'nizi **uygulama kayıtları** kaydedersiniz.

### <a name="choose-your-azure-ad-tenant"></a>Azure AD kiracınızı seçin

Uygulamalarınızı el ile kaydetmek için uygulamalarınızı oluşturmak istediğiniz Azure Active Directory (Azure AD) kiracısını seçin.

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden çok kiracıya erişiminiz varsa,  :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: kullanmak istediğiniz kiracıyı seçmek Için üst menüdeki Dizin + abonelik filtresini kullanın.

### <a name="register-the-todolistservice-app"></a>TodoListService uygulamasını kaydetme

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin (örneğin,) `AppModelv2-NativeClient-DotNet-TodoListService` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Desteklenen hesap türleri** için **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. Uygulamaya **genel bakış** sayfasında, **uygulama (istemci) kimliği** değerini bulup daha sonra kullanmak üzere kaydedin. Bu proje için Visual Studio yapılandırma dosyasını yapılandırmanız gerekir (yani, `ClientId` *TodoListService\Web.config* dosyasında).
1. **Yönet**' in altında **bir API 'yi kullanıma**  >  **ekleme kapsam Ekle**' yi seçin. `api://{clientId}` **Kaydet ve devam et**' i seçerek ÖNERILEN uygulama kimliği URI 'sini () kabul edin ve ardından aşağıdaki bilgileri girin:

    1. **Kapsam adı** için girin `access_as_user` .
    1. **Kimler izin verebilir**, **Yöneticiler ve kullanıcılar** seçeneğinin seçildiğinden emin olun.
    1. **Yönetici onayı görünen adı** kutusuna yazın `Access TodoListService as a user` .
    1. **Yönetici onayı açıklama** kutusunda, girin `Accesses the TodoListService web API as a user` .
    1. **Kullanıcı izni görünen adı** kutusuna girin `Access TodoListService as a user` .
    1. **Kullanıcı izni açıklama** kutusunda, girin `Accesses the TodoListService web API as a user` .
    1. **Durum** için **etkin** kalsın.
1. **Kapsam Ekle**' yi seçin.

### <a name="configure-the-service-project"></a>Hizmet projesini yapılandırma

Aşağıdaki işlemleri gerçekleştirerek hizmet projesini kayıtlı Web API 'siyle eşleşecek şekilde yapılandırın:

1. Visual Studio 'da çözümü açın ve ardından TodoListService projesinin kökünün altındaki *Web.config* dosyasını açın.

1. Parametresinin değerini, `ida:ClientId` **uygulama kayıtları** portalında yeni KAYDETTIĞINIZ uygulamadan Istemci KIMLIĞI (uygulama kimliği) değeri ile değiştirin.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Yeni kapsamı app.config dosyasına ekleyin

Yeni kapsamı TodoListClient *app.config* dosyasına eklemek için aşağıdakileri yapın:

1. TodoListClient proje kök klasöründe *app.config* dosyasını açın.

1. TodoListService projeniz için yeni kaydettiğiniz uygulamadan, dizeyi değiştirerek uygulama KIMLIĞINI yapıştırın `TodoListServiceScope` `{Enter the Application ID of your TodoListService from the app registration portal}` .

  > [!NOTE]
  > Uygulama KIMLIĞININ şu biçimi kullandığından emin olun: `api://{TodoListService-Application-ID}/access_as_user` (burada, TodoListService uygulamanızın `{TodoListService-Application-ID}` uygulama kimliğini temsıl eden GUID 'dir).

## <a name="register-the-todolistclient-client-app"></a>TodoListClient istemci uygulamasını kaydetme

Bu bölümde, TodoListClient uygulamanızı Azure portal **uygulama kayıtları** kaydeder ve sonra TodoListClient projesindeki kodu yapılandırırsınız. İstemci ve sunucu *aynı uygulama* olarak kabul edildiğinde, adım 2 ' de kayıtlı uygulamayı yeniden kullanabilirsiniz. Kullanıcıların bir Microsoft kişisel hesabıyla oturum açmasını istiyorsanız aynı uygulamayı kullanın.

### <a name="register-the-app"></a>Uygulamayı kaydetme

TodoListClient uygulamasını kaydetmek için aşağıdakileri yapın:

1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) Portal ' a gidin.
1. **Yeni kayıt** seçeneğini belirleyin.
1. **Bir uygulamayı Kaydet sayfası** açıldığında, uygulamanızın kayıt bilgilerini girin:

    1. **Ad** bölümünde, uygulamanın kullanıcılarına gösterilecek anlamlı bir uygulama adı girin (örneğin, **NativeClient-DotNet-TodoListClient**).
    1. **Desteklenen hesap türleri** için **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
    1. Uygulamayı kaydetmek için **Kaydet**'i seçin.

   > [!NOTE]
   > TodoListClient proje *app.config* dosyasında varsayılan değeri `ida:Tenant` olarak ayarlanır `common` . Olası değerler şunlardır:
   > - `common`: Bir iş veya okul hesabı ya da bir Microsoft Kişisel hesabı kullanarak oturum açabilirsiniz (adım 3B 'deki **herhangi bir kuruluş dizininde hesapları** seçtiğinizden).
   > - `organizations`: Bir iş veya okul hesabı kullanarak oturum açabilirsiniz.
   > - `consumers`: Yalnızca bir Microsoft Kişisel hesabı kullanarak oturum açabilirsiniz.

1. Uygulamaya **genel bakış** sayfasında **kimlik doğrulaması**' nı seçin ve ardından aşağıdakileri yapın:

    1. **Platform yapılandırması** altında **Platform Ekle** düğmesini seçin.
    1. **Mobil ve Masaüstü uygulamaları** için **mobil ve Masaüstü uygulamaları**' nı seçin.
    1. **Yeniden yönlendirme URI 'leri** için **https://login.microsoftonline.com/common/oauth2/nativeclient** onay kutusunu işaretleyin.
    1. **Yapılandır**'ı seçin.

1. **API izinleri**' ni seçin ve ardından aşağıdakileri yapın:

    1. **İzin ekleyin** düğmesini seçin.
    1. **API 'Lerim** sekmesini seçin.
    1. API 'Ler listesinde **AppModelv2-NativeClient-DotNet-TodoListService API 'sini** veya Web API 'si için girdiğiniz adı seçin.
    1. Zaten seçili değilse **access_as_user** izin onay kutusunu seçin. Gerekirse arama kutusunu kullanın.
    1. **Izin Ekle** düğmesini seçin.

### <a name="configure-your-project"></a>Projenizi yapılandırma

TodoListClient projenizi yapılandırmak için şunları yapın:

1. **Uygulama kayıtları** portalında **genel bakış** sayfasında, **uygulama (istemci) kimliğinin** değerini kopyalayın.

1. TodoListClient proje kök klasöründen *app.config* dosyasını açın ve ardından parametre IÇINDEKI uygulama kimliği değerini yapıştırın `ida:ClientId` .

## <a name="run-your-todolistclient-project"></a>TodoListClient projenizi çalıştırma

TodoListClient Projenizi çalıştırmak için şunları yapın:

1. F5 tuşuna basarak TodoListClient projenizi açın. Proje sayfası açılır.

1. Sağ üst köşedeki **oturum aç**' ı seçin ve ardından uygulamanızı kaydetmek için kullandığınız kimlik bilgileriyle oturum açın veya aynı dizinde Kullanıcı olarak oturum açın.

   İlk kez oturum açıyorsanız, TodoListService Web API 'sine onay vermeniz istenebilir.

   TodoListService Web *API 'sine erişmenize ve yapılacaklar listesini değiştirmenize* yardımcı olması için, oturum açma, *access_as_user* kapsamına bir erişim belirteci de ister.

## <a name="pre-authorize-your-client-application"></a>İstemci uygulamanızı önceden yetkilendirme

Diğer dizinlerden kullanıcıların Web API 'nize erişmesine izin vermenin bir yolu, istemci uygulamasını Web API 'nize erişmek için önceden yetkilendirmeniz olabilir. Bunu, istemci uygulamadan Web API 'niz için önceden yetkilendirilmiş uygulamalar listesine ekleyerek yapabilirsiniz. Önceden yetkilendirilmiş bir istemci ekleyerek, kullanıcıların izin sağlamak zorunda kalmadan Web API 'nize erişmesine izin vermiş olursunuz. İstemci uygulamanızı önceden yetkilendirmek için aşağıdakileri yapın:

1. **Uygulama kayıtları** portalında, TodoListService uygulamanızın özelliklerini açın.
1. API 'yi **kullanıma** sunma bölümünde, **yetkilendirilmiş istemci uygulamaları** altında, **istemci uygulaması Ekle**' yi seçin.
1. **ISTEMCI kimliği** kutusunda TodoListClient UYGULAMASıNıN uygulama kimliğini yapıştırın.
1. **Yetkili kapsamlar** bölümünde, `api://<Application ID>/access_as_user` Web API 'sinin kapsamını seçin.
1. **Uygulama Ekle**' yi seçin.

### <a name="run-your-project"></a>Projenizi çalıştırma

1. Projenizi çalıştırmak için F5 tuşuna basın. TodoListClient uygulamanız açık olmalıdır.
1. Sağ üst köşedeki **oturum aç**' ı seçin ve ardından live.com veya hotmail.com gibi bir kişisel Microsoft hesabı veya bir iş veya okul hesabı kullanarak oturum açın.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>İsteğe bağlı: belirli kullanıcılara yönelik oturum açma erişimini sınırlayın

Varsayılan olarak, yukarıdaki adımları izlediyseniz, outlook.com veya live.com gibi kişisel hesaplar ya da Azure AD ile tümleştirilen kuruluşların iş veya okul hesapları, belirteçler isteyebilir ve Web API 'nize erişebilir.

Uygulamanızda kimlerin oturum açmasını belirtmek için aşağıdaki seçeneklerden birini kullanın:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Seçenek 1: erişimi tek bir kuruluşa sınırlayın (tek kiracılı)

Uygulamanıza yönelik oturum açma erişimini, tek bir Azure AD kiracısında bulunan ve bu kiracının *Konuk hesapları* dahil olmak üzere Kullanıcı hesaplarıyla sınırlayabilirsiniz. Bu senaryo *iş kolu uygulamaları* için ortaktır.

1. *App_Start \Startup.exe* dosyasını açın ve sonra öğesine geçirilen meta veri uç noktasının değerini değiştirin `OpenIdConnectSecurityTokenProvider` `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` . Ayrıca, gibi kiracı adını da kullanabilirsiniz `contoso.onmicrosoft.com` .
2. Aynı dosyada, `ValidIssuer` üzerinde özelliğini `TokenValidationParameters` olarak ayarlayın `"https://sts.windows.net/{Tenant ID}/"` ve `ValidateIssuer` bağımsız değişkenini olarak ayarlayın `true` .

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Seçenek 2: verenler doğrulamak için özel bir yöntem kullanma

Parametresini kullanarak verenler doğrulamak için özel bir yöntem uygulayabilirsiniz `IssuerValidator` . Bu parametre hakkında daha fazla bilgi için bkz. [Tokenvalidationparameters sınıfı](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Microsoft Identity platformunun desteklediği korumalı Web API senaryosu hakkında daha fazla bilgi edinin:
> [!div class="nextstepaction"]
> [Korumalı Web API senaryosu](scenario-protected-web-api-overview.md)
