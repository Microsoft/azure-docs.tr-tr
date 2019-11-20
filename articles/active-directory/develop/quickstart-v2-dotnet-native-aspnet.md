---
title: Azure AD tarafından korunan bir ASP.NET Web API 'SI çağırma-Microsoft Identity
description: Bu hızlı başlangıçta, Windows Masaüstü (WPF) uygulamasından Azure Active Directory tarafından korunan bir ASP.NET Web API 'sinin nasıl çağrılacağını öğrenin. WPF istemcisi bir kullanıcının kimliğini doğrular, bir erişim belirteci ister ve Web API 'sini çağırır.
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e0fdeb2c1955eab18b440c3ef3bcac725ad92b6
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200250"
---
# <a name="quickstart-call-an-aspnet-web-api-protected-by-azure-ad"></a>Hızlı başlangıç: Azure AD tarafından korunan bir ASP.NET Web API 'SI çağırma

Bu hızlı başlangıçta, bir Web API 'sini kullanıma sunar ve bunu yalnızca kimliği doğrulanmış kullanıcının erişebileceği şekilde koruyabilirsiniz. Bu örnek, kişisel hesaplar tarafından verilen belirteçleri (outlook.com, live.com ve diğerleri dahil) ve Azure Active Directory tümleştirilmiş herhangi bir şirketten veya kuruluştan iş ve okul hesaplarını kabul edecek şekilde bir ASP.NET Web API 'sinin nasıl kullanıma sunuleceği gösterilmektedir.

Örnek ayrıca, bir Web API 'sine erişmek için bir erişim belirteci isteme şeklini gösteren bir Windows masaüstü uygulaması (WPF) istemcisi içerir.

## <a name="prerequisites"></a>Önkoşullar

Bu örneği çalıştırmak için aşağıdakilere ihtiyacınız olacaktır:

* Visual Studio 2017 veya 2019.  [Visual Studio 'yu ücretsiz](https://www.visualstudio.com/downloads/)indirin.

* [Microsoft hesabı](https://www.outlook.com) veya [Office 365 Geliştirici Programı](/office/developer-program/office-365-developer-program)

## <a name="download-or-clone-this-sample"></a>Bu örneği indir veya Kopyala

Bu örneği kabuktan veya komut satırınızdan kopyalayabilirsiniz:

  ```console
  git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
  ```

Ya da [ÖRNEĞI ZIP dosyası olarak indirebilirsiniz](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api-in-the-application-registration-portal"></a>Web API 'nizi uygulama kayıt portalına kaydetme

### <a name="choose-the-azure-ad-tenant-where-you-want-to-create-your-applications"></a>Uygulamalarınızı oluşturmak istediğiniz Azure AD kiracısını seçin

Uygulamalarınızı el ile kaydetmek istiyorsanız, ilk adım olarak şunları yapmanız gerekir:

1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalında](https://portal.azure.com) oturum açın.
1. Hesabınız birden fazla Azure AD kiracısında mevcutsa, sayfanın üstündeki menüdeki sağ üst köşedeki profilinizi seçin ve ardından dizin ' i **değiştirin**.
   Portal oturumunuzu istenen Azure AD kiracısına değiştirin.

### <a name="register-the-service-app-todolistservice"></a>Hizmet uygulamasını kaydetme (TodoListService)

1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin.
1. **Yeni kayıt**seçeneğini belirleyin.
1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
   - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `AppModelv2-NativeClient-DotNet-TodoListService`.
   - **Desteklenen hesap türlerini** **herhangi bir kuruluş dizinindeki hesaplara**değiştirin.
   - Uygulamayı kaydetmek için **Kaydet**'i seçin.

1. Uygulamaya **genel bakış** sayfasında, **uygulama (istemci) kimlik** değerini bulun ve daha sonra için kaydedin. Bu proje için Visual Studio yapılandırma dosyasını yapılandırmanız gerekir (`TodoListService\Web.config``ClientId`).
1. API 'yi **kullanıma** sunma bölümünü seçin ve:
   - **Kapsam Ekle** ' yi seçin
   - **Kaydet ve devam et ' i** seçerek ÖNERILEN uygulama kimliği URI 'sini (API://{ClientID}) kabul edin
   - Aşağıdaki parametreleri girin:
     - **kapsam adı** kullanım `access_as_user` için
     - **Yöneticiler ve kullanıcılar** seçeneğinin **izin verebilir** olarak seçildiğinden emin olun
     - **yönetici onayı görünen adı** türü `Access TodoListService as a user`
     - **yönetici onayı açıklama** türü `Accesses the TodoListService Web API as a user`
     - **Kullanıcı izni görünen ad** türü ' nde `Access TodoListService as a user`
     - **Kullanıcı onayı açıklama** türü ' nde `Accesses the TodoListService Web API as a user`
     - **Durumu** **etkin** olarak tut
     - **Kapsam Ekle** ' yi seçin

### <a name="configure-the-service-and-client-projects-to-match-the-registered-web-api"></a>Hizmet ve istemci projelerini kayıtlı Web API 'siyle eşleşecek şekilde yapılandırma 

1. Visual Studio 'da çözümü açın ve ardından **TodoListService** projesi kökünün altındaki **Web. config** dosyasını açın.
1. `ida:ClientId` parametresinin değerini, uygulama kayıt portalı 'nda yeni kaydettiğiniz uygulamadan **ISTEMCI kimliği (uygulama kimliği)** ile değiştirin.

### <a name="add-the-new-scope-to-the-todolistclients-appconfig"></a>Yeni kapsamı *TodoListClient*'ın App. config dosyasına ekleyin

1. **TodoListClient** projesinin kök klasöründe bulunan **app. config** dosyasını açın ve ardından `TodoListServiceScope` *parametresi altında,* dizeyi değiştirerek **uygulama kimliğini** yeni kaydettiğiniz uygulamadan yapıştırın `{Enter the Application ID of your TodoListService from the app registration portal}`.

   > Unutmayın: aşağıdaki biçimi kullandığından emin olun:
   >
   > `api://{TodoListService-Application-ID}/access_as_user` 
   >
   >(burada {TodoListService-Application-ID}, TodoListService için uygulama KIMLIĞINI temsil eden GUID 'dir).

## <a name="register-the-client-app-todolistclient"></a>İstemci uygulamasını kaydetme (TodoListClient)

Bu adımda, uygulama kayıt portalı 'nda yeni bir uygulama kaydederek *TodoListClient* projenizi yapılandırırsınız. İstemci ve sunucunun *aynı uygulamayı* kabul ettiği durumlarda, ' 2. adım ' içinde kayıtlı aynı uygulamayı da kullanabilirsiniz. Kullanıcıların Microsoft kişisel hesaplarıyla oturum açmasını isterseniz aynı uygulamanın kullanılması gerekir

### <a name="register-the-todolistclient-application-in-the-application-registration-portal"></a>*TodoListClient* uygulamasını *uygulama kayıt portalına* kaydetme

1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin.
1. **Yeni kayıt**seçeneğini belirleyin.
1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
   - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `NativeClient-DotNet-TodoListClient`.
   - **Desteklenen hesap türlerini** **herhangi bir kuruluş dizinindeki hesaplara**değiştirin.
   - Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. Uygulamanın genel bakış sayfasından **kimlik doğrulama** bölümünü seçin.
   - **Yeniden yönlendirme URL 'leri** | **ortak Istemciler Için önerilen yeniden yönlendirme URL 'leri (mobil, masaüstü)** bölümünde **urn: ietf: WG: OAuth: 2.0: OOB** ' yi denetleyin
   - **Kaydet**’i seçin.
1. **API izinleri** bölümünü seçin
   - **Izin Ekle** düğmesine tıklayın ve ardından
   - **API 'Lerim** sekmesini seçin.
   - API 'Ler listesinde `AppModelv2-NativeClient-DotNet-TodoListService API`veya Web API 'SI için girdiğiniz adı seçin.
   - Zaten işaretli değilse **access_as_user** iznini denetleyin. Gerekirse arama kutusunu kullanın.
   - **Izin Ekle** düğmesini seçin

### <a name="configure-your-todolistclient-project"></a>*TodoListClient* projenizi yapılandırma

1. *Uygulama kayıt portalı*' nda, **genel bakış** sayfasında, **uygulama (istemci) kimliğinin** değerini kopyalayın
1. **TodoListClient** projesinin kök klasöründe bulunan **app. config** dosyasını açın ve `ida:ClientId` parametre değerindeki değeri yapıştırın

## <a name="run-your-project"></a>Projenizi çalıştırma

1. Projenizi çalıştırmak için `<F5>` tuşuna basın. *TodoListClient* ' i açmanız gerekir.
1. Sağ üst köşedeki **oturum aç** ' ı seçin ve uygulamanızı kaydetmek için kullandığınız kullanıcıyla veya aynı dizindeki bir kullanıcıyla oturum açın.
1. Bu noktada, ilk kez oturum açıyorsanız, *TodoListService* Web API 'sine onay vermeniz istenebilir.
1. Oturum açma Ayrıca, *TodoListService* Web API 'sine erişmek ve *Yapılacaklar* listesini değiştirmek için *access_as_user* kapsamına erişim belirteci ister.

## <a name="pre-authorize-your-client-application"></a>İstemci uygulamanızı önceden yetkilendirme

Diğer dizinlerden kullanıcıların Web API 'nize erişmesine izin verme yöntemlerinden biri, istemci uygulamaları için *önceden yetkilendirilmiş* uygulamalar listesindeki uygulama kimliklerini ekleyerek Web API 'nize erişmek üzere *önceden yetkilendirerek* , Web API 'niz. Önceden yetkilendirilmiş bir istemci ekleyerek, kullanıcının Web API 'nizi kullanma onayını zorunlu kılmayacaktır. Web uygulamanızı önceden yetkilendirmek için aşağıdaki adımları izleyin::

1. *Uygulama kayıt portalına* geri dönün ve **TodoListService**özelliklerini açın.
1. **API 'Yi kullanıma** sunma bölümünde, *yetkili istemci uygulamaları* bölümünde **istemci uygulaması Ekle** ' ye tıklayın.
1. *ISTEMCI kimliği* alanında `TodoListClient` UYGULAMASıNıN uygulama kimliğini yapıştırın.
1. *Yetkili kapsamlar* bölümünde, bu Web API `api://<Application ID>/access_as_user`kapsamını seçin.
1. Sayfanın alt kısmındaki **Uygulama Ekle** düğmesine basın.

## <a name="run-your-project"></a>Projenizi çalıştırma

1. Projenizi çalıştırmak için `<F5>` tuşuna basın. *TodoListClient* ' i açmanız gerekir.
1. Sağ üst köşedeki **oturum aç** ' ı (veya temiz önbellek/oturum açma) seçin ve ardından kişisel Microsoft hesabı (live.com veya hotmail.com) veya iş ya da okul hesabı kullanarak oturum açın.

## <a name="optional-restrict-sign-in-access-to-your-application"></a>İsteğe bağlı: uygulamanıza yönelik oturum açma erişimini kısıtlayın

Varsayılan olarak, bu kod örneğini indirir ve uygulamayı önceki adımlardan sonra gelen Azure Active Directory v2 uç noktasını kullanacak şekilde yapılandırdığınızda, her ikisi de outlook.com, live.com ve diğer kişilerin Iş veya okul hesapları Azure AD ile tümleştirilmiş kuruluşlar, belirteç isteyebilir ve Web API 'nize erişebilir. 

Uygulamanızda kimlerin oturum açabileceğini kısıtlamak için seçeneklerden birini kullanın:

### <a name="option-1-restrict-access-to-a-single-organization-single-tenant"></a>Seçenek 1: tek bir kuruluşa erişimi kısıtlama (tek kiracılı)

Uygulamanız için oturum açma erişimini, yalnızca tek bir Azure AD kiracısında bulunan Kullanıcı hesaplarına (Bu kiracının *Konuk hesapları* dahil) kısıtlayabilirsiniz. Bu senaryo *iş kolu uygulamaları*için ortaktır:

1. **App_Start\Startup.auth** dosyasını açın ve `OpenIdConnectSecurityTokenProvider` geçirilen meta veri uç noktasının değerini `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` olarak değiştirin (`contoso.onmicrosoft.com`gibi kiracı adını da kullanabilirsiniz).
2. Aynı dosyada, `TokenValidationParameters` `ValidIssuer` özelliğini `"https://sts.windows.net/{Tenant ID}/"` ve `ValidateIssuer` bağımsız değişkenini `true`olarak ayarlayın.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Seçenek 2: verenler doğrulamak için özel bir yöntem kullanma

**Issuervalidator** parametresini kullanarak verenler doğrulamak için özel bir yöntem uygulayabilirsiniz. Bu parametrenin nasıl kullanılacağı hakkında daha fazla bilgi için, [Tokenvalidationparameters sınıfı](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet)hakkında makalesini okuyun.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Microsoft Identity platformunun desteklediği korumalı Web API senaryosu hakkında daha fazla bilgi edinin:
> [!div class="nextstepaction"]
> [Korumalı Web API senaryosu](scenario-protected-web-api-overview.md)
