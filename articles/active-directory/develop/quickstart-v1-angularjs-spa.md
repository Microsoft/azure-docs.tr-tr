---
title: Azure AD 'de oturum açmak ve oturumu kapatmak için AngularJS tek sayfalı uygulama oluşturun | Microsoft Docs
description: Oturum açma işlemi için Azure AD ile tümleştirilen ve OAuth kullanarak Azure AD korumalı API'leri çağıran bir AngularJS tek sayfalı uygulaması oluşturmayı öğrenin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c239b88a67e1be19e3a95130839ab6fd4598fe77
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150053"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Hızlı başlangıç: Azure Active Directory ile oturum açmak ve oturum kapatmak için AngularJS tek sayfalı uygulaması oluşturma

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

> [!IMPORTANT]
> [Microsoft Identity platform](v2-overview.md) , Azure Active Directory (Azure AD) geliştirici platformunun bir evmidir. Bu platform geliştiricilerin tüm Microsoft kimlikleriyle oturum açan ve Microsoft Graph veya diğer Microsoft API'leri ya da geliştiricilerin derlemiş olduğu API'lere çağrı göndermek için gerekli belirteçleri alan uygulamalar derlemesini sağlar.
> İş ve okul hesaplarına ek olarak kişisel hesaplar için oturum açmayı etkinleştirmeniz gerekiyorsa, *[Microsoft Identity platform uç noktasını](azure-ad-endpoint-comparison.md)* kullanabilirsiniz.
> Bu hızlı başlangıç, Azure AD v 1.0 uç noktası için daha eski bir noktadır. Yeni projeler için v 2.0 uç noktasını kullanmanızı öneririz. Daha fazla bilgi için [Bu JAVASCRIPT Spa öğreticisinin](tutorial-v2-javascript-spa.md) yanı sıra *Microsoft Identity platform uç noktasını*açıklayan [Bu makaleye](active-directory-v2-limitations.md) bakın.

Azure Active Directory (Azure AD), basit ve anlaşılır bir şekilde tek sayfalı uygulamalarınıza oturum açma, oturum kapatma ve güvenli OAuth API'si çağrıları yapmanızı sağlar. Uygulamalarınızın kullanıcıların kimliklerini Windows Server Active Directory hesaplarıyla doğrulamasına ve Office 365 API'leri veya Azure API'si gibi Azure AD'nin korunmasına yardımcı olduğu herhangi bir web API'sini kullanmasına olanak tanır.

Tarayıcıda çalıştırılan JavaScript uygulamaları için, Azure AD Active Directory Authentication Library (ADAL) veya adal.js sağlar. Adal.js'nin tek amacı uygulamanızın erişim belirteçleri almasını kolaylaştırmaktır.

Bu hızlı başlangıçta, aşağıdakileri yapan bir AngularJS To Do List uygulaması oluşturmayı öğreneceksiniz:

* Kimlik sağlayıcısı olarak Azure AD'yi kullanıp kullanıcının uygulama oturumunu açma.
* Kullanıcı hakkında bazı bilgileri görüntüleme.
* Azure AD'den taşıyıcı belirteçleri kullanarak uygulamanın To Do List API'sine güvenli çağrı yapma.
* Kullanıcının uygulamada oturumunu kapatma.

Eksiksiz, çalışan bir uygulama oluşturmak için şunları yapmalısınız:

1. Uygulamanızı Azure AD'ye kaydetme.
2. ADAL'ı yükleme ve tek sayfalı uygulamayı yapılandırma.
3. Tek sayfalı uygulamada sayfaların güvenliğini sağlamaya yardımcı olmak için ADAL'ı kullanma.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şu önkoşulları tamamlayın:

* [Uygulama çatısını indirin](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) veya [tamamlanmış örneği indirin](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Altında kullanıcıları oluşturabileceğiniz ve uygulamayı kaydedebileceğiniz bir Azure AD kiracınız olsun. Henüz bir kiracınız yoksa [nasıl alabileceğinizi öğrenin](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>1\. Adım: DirectorySearcher uygulamasını kaydetme

Uygulamanızın kullanıcı kimliklerini doğrulamasını ve belirteçleri almasını sağlamak için, ilk olarak uygulamayı Azure AD kiracısına kaydetmeniz gerekir:

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. Birden çok dizinde oturum açtıysanız, doğru dizini görüntülediğinizden emin olmalısınız. Bunu yapmak için, üst çubukta hesabınıza tıklayın. **Dizin** listesi altında, uygulamanızı kaydetmek istediğiniz Azure AD kiracısını seçin.
1. Sol bölmede **Tüm hizmetler**'e tıklayın ve ardından **Azure Active Directory**'yi seçin.
1. **Uygulama kayıtları**' a ve ardından **Yeni kayıt**' ı seçin.
1. **Bir uygulamayı kaydet** sayfası göründüğünde, uygulamanız için bir ad girin.
1. **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** bölümünün altında **Web** platformu ' nu seçin ve DEĞERI `https://localhost:44326/` (Azure AD 'nin belirteçleri döndürdüğü konum) olarak ayarlayın.
1. Bittiğinde **Kaydet**’i seçin. Uygulamaya **genel bakış** sayfasında, **uygulama (istemci) kimliği** değerini aklınızda edin.
1. Adal.js. Azure AD ile iletişim kurmak için OAuth örtük akışını kullanır. Uygulamanız için örtük akışı etkinleştirmeniz gerekir. Kayıtlı uygulamanın sol taraftaki Gezinti bölmesinde **kimlik doğrulaması**' nı seçin.
1. **Gelişmiş ayarlar**' da, **örtük izin**' ın altında, **kimlik belirteçlerini** ve **erişim belirteçleri** onay kutularını etkinleştirin. Bu uygulamanın kullanıcıları oturum açması ve bir API çağırması gerektiğinden, KIMLIK belirteçleri ve erişim belirteçleri gereklidir.
1. **Kaydet**’i seçin.
1. Uygulamanız için kiracı genelinde izinleri verin. **API izinleri**' ne gidin ve **izin ver**' ın altındaki **yönetici izni ver** düğmesini seçin.
1. Onaylamak için **Evet**'i seçin.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>2\. Adım: ADAL'ı yükleme ve tek sayfalı uygulamayı yapılandırma

Artık Azure AD'de bir uygulamanız olduğuna göre, adal.js'yi yükleyebilir ve kimlikle ilgili kodunuzu yazabilirsiniz.

### <a name="configure-the-javascript-client"></a>JavaScript istemcisini yapılandırma

Başlangıç olarak Paket Yöneticisi Konsolu'nu kullanıp adal.js'yi TodoSPA projesine ekleyin:

1. [Adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js)'yi indirin ve `App/Scripts/` proje dizinine ekleyin.
2. [Adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js)'yi indirin ve `App/Scripts/` proje dizinine ekleyin.
3. Her betiği `index.html` dosyasında `</body>` bölümünün sonundan önceye ekleyin:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Arka uç sunucusunu yapılandırma

Tek sayfalı uygulamanın arka uç To Do List API'sinin tarayıcıdan belirteçleri kabul etmesi için, arka uca uygulama kaydı hakkında yapılandırma bilgileri gerekir. TodoSPA projesinde `web.config` dosyasını açın. `<appSettings>` bölümündeki öğelerin değerlerini, Azure portalında kullandığınız değerleri yansıtacak şekilde değiştirin. Kodunuz ADAL'ı her kullandığında bu değerlere başvurur.

   * `ida:Tenant`, Azure AD kiracınızın etki alanıdır (örneğin, contoso.onmicrosoft.com).
   * `ida:Audience`, portaldan kopyaladığınız uygulamanızın istemci kimliğidir.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>3\. Adım: Tek sayfalı uygulamada sayfaların güvenliğini sağlamaya yardımcı olmak için ADAL'ı kullanma

Adal.js AngularJS yoluyla ve HTTP sağlayıcılarıyla tümleştirildiğinden, tek sayfalı uygulamanızda tek tek görünümleri güvenlik altına almaya yardımcı olabilirsiniz.

1. `App/Scripts/app.js` içinde, adal.js modülünü getirin:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. `App/Scripts/app.js` içinde de uygulama kaydınızın yapılandırma değerlerini kullanarak `adalProvider` öğesini başlatın:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Tek bir kod satırı kullanarak uygulamada `TodoList` görünümünün korunmasına yardımcı olun: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Özet

Artık kullanıcıların oturumunu açabilen ve arka uç API'sine taşıyıcı belirteçle korunan istekler gönderebilen güvenli, tek sayfalı bir uygulamanız vardır. Kullanıcı **TodoList** bağlantısına tıkladığında, adal.js gerekiyorsa oturum açma için otomatik olarak Azure AD'ye yeniden yönlendirir. Buna ek olarak, adal.js uygulamanın arka ucuna gönderilen tüm Ajax isteklerine otomatik olarak bir erişim belirteci ekler.

Önceki adımlar, adal.js kullanarak tek sayfalı uygulama oluşturmak için gereken en az işlemin adımlarıdır. Ama tek sayfalı uygulamada yararlı birkaç özellik daha vardır:

* Oturum açma ve oturum kapatma isteklerini açıkça göndermek için, denetleyicilerinizde adal.js'yi çağıran işlevler tanımlayabilirsiniz. `App/Scripts/homeCtrl.js` içinde:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Kullanıcı bilgilerinin uygulamanın kullanıcı arabiriminde gösterilmesini isteyebilirsiniz. ADAL hizmeti `userDataCtrl` denetleyicisine zaten eklenmişti, dolayısıyla ilişkili `App/Views/UserData.html` görünümünde `userInfo` nesnesine erişebilirsiniz:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Kullanıcının oturumunun açık mı yoksa kapalı mı olduğunu bilmek isteyeceğiniz birçok senaryo vardır. Bu bilgiyi toplamak için `userInfo` nesnesini de kullanabilirsiniz. Örneğin, `index.html` altında, kimlik doğrulama durumuna göre **Oturum Aç** veya **Oturumu Kapat** düğmesini gösterebilirsiniz:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Azure AD ile tümleşik tek sayfalı uygulamanız kullanıcıların kimliğini doğrulayabilir, OAuth 2.0 kullanarak güvenle arka ucunu çağırabilir ve kullanıcı hakkında temel bilgiler alabilir. Henüz yapmadıysanız, kiracınızı biraz kullanıcıyla doldurmanın zamanı geldi. To Do List tek sayfalı uygulamanızı çalıştırın ve bu kullanıcılardan biriyle oturum açın. Kullanıcının yapılacaklar listesine görev ekleyin, oturumu kapatın ve yeniden oturum açın.

Adal.js, yaygın kimlik özelliklerini uygulamanıza eklemenizi kolaylaştırır. Sizin yerinize tüm sıkıcı işleri yapar: önbellek yönetimi, OAuth protokol desteği, kullanıcıya oturum açma kullanıcı arabirimini gösterme, süresi dolmuş belirteçleri yenileme ve dahası.

Tamamlanan örnek, başvuru için (yapılandırma değerleriniz olmadan) [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip)'da sağlanır.

## <a name="next-steps"></a>Sonraki adımlar

Artık ek senaryolara geçebilirsiniz.

> [!div class="nextstepaction"]
> [Tek sayfalı uygulamadan CORS web API'si çağırma](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
