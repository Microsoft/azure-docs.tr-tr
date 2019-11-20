---
title: Öğretici-tek sayfalı bir uygulamadan ASP.NET Core Web API 'sine erişim Izni verme-Azure Active Directory B2C
description: .NET Core Web API 'sini korumak ve tek sayfalı bir Node. js uygulamasından API 'yi çağırmak için Active Directory B2C kullanmayı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 30e7059605ef86e6afd86251db0e416c9143a9ec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475119"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C kullanarak tek sayfalı bir uygulamadan ASP.NET Core Web API 'sine erişim Izni verme

Bu öğreticide, tek sayfalı bir uygulamadan Azure Active Directory B2C (Azure AD B2C) korumalı ASP.NET Core Web API kaynağını çağırma gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği yapılandırma

## <a name="prerequisites"></a>Önkoşullar

* Öğreticideki adımları ve önkoşulları doldurun [: Azure Active Directory B2C kullanarak tek sayfalı bir uygulamada kimlik doğrulamasını etkinleştirin](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 veya üzeri ya da Visual Studio Code
* .NET Core 2,2 veya üzeri
* Node.js

## <a name="add-a-web-api-application"></a>Web API'si uygulaması ekleme

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Kapsamları yapılandırma

Kapsamlar, korumalı kaynaklara erişimi yönetmek için bir yol sağlar. Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin, bazı kullanıcılar hem okuma hem de yazma erişimine sahipken diğer kullanıcıların salt okunur izinleri olabilir. Bu öğreticide Web API 'SI için hem okuma hem de yazma izinlerini tanımlarsınız.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Tek sayfalı uygulamayı yapılandırırken daha sonraki bir adımda kullanmak üzere `demo.read` kapsamı için **kapsamlar** altındaki değeri kaydedin. Tam kapsam değeri `https://contosob2c.onmicrosoft.com/api/demo.read`benzerdir.

## <a name="grant-permissions"></a>İzinleri verme

Başka bir uygulamadan korumalı bir Web API 'SI çağırmak için, bu uygulama izinlerini Web API 'sine vermeniz gerekir.

Önkoşul öğreticisinde, *WebApp1*adlı bir Web uygulaması oluşturdunuz. Bu öğreticide, *webapi1*önceki bir bölümünde oluşturduğunuz Web API 'sini çağırmak için bu uygulamayı yapılandırırsınız.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Tek sayfalı Web uygulamanız, korumalı Web API 'sini çağırmak için kaydedilir. Bir kullanıcı tek sayfalı uygulamayı kullanmak üzere Azure AD B2C kimliğini doğrular. Tek sayfalı uygulama, korumalı Web API 'sine erişmek için Azure AD B2C bir yetkilendirme izni alır.

## <a name="configure-the-sample"></a>Örneği yapılandırma

Web API 'SI kayıtlı olduğuna ve kapsamlarınızın tanımlandığından, Web API kodunu Azure AD B2C kiracınızı kullanacak şekilde yapılandırırsınız. Bu öğreticide, GitHub 'dan indirdiğinizde örnek bir .NET Core Web uygulaması yapılandırırsınız.

[Bir \*. zip arşivi indirin](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) veya örnek Web API projesini GitHub 'dan kopyalayın.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Web API’sini yapılandırma

1. <em>B2C-WebApi/**appSettings. JSON**</em>  dosyasını Visual Studio 'da veya Visual Studio Code açın.
1. `AzureAdB2C` bloğunu, kiracı adınızı, Web API 'SI uygulamasının uygulama KIMLIĞINI, kaydolma/oturum açma ilkenizin adını ve daha önce tanımladığınız kapsamları yansıtacak şekilde değiştirin. Blok aşağıdaki örneğe benzer olmalıdır (uygun `Tenant` ve `ClientId` değerleriyle):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>CORS'yi etkinleştirme

Tek sayfalı uygulamanızın ASP.NET Core Web API 'sini çağırmasını sağlamak için, Web API 'sinde [CORS](https://docs.microsoft.com/aspnet/core/security/cors) 'yi etkinleştirmeniz gerekir.

1. *Startup.cs* dosyasında `ConfigureServices()` yöntemine CORS ekleyin.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Ayrıca `ConfigureServices()` yöntemi içinde, `jwtOptions.Authority` değerini aşağıdaki belirteç veren URI 'sine ayarlayın.

    `<your-tenant-name>`, B2C kiracınızın adıyla değiştirin.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. `Configure()` yönteminde CORS 'yi yapılandırın.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Yalnızca Visual Studio) Çözüm Gezgini **Özellikler** altında, *launchsettings. JSON* dosyasını açın ve sonra `iisExpress` bloğunu bulun.
1. (Yalnızca Visual Studio) `applicationURL` değerini, önceki bir adımda *webapi1* uygulamasını kaydettiğiniz zaman, belirttiğiniz bağlantı noktası numarasıyla güncelleştirin. Örneğin:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Tek sayfalı uygulamayı yapılandırma

Serideki [önceki öğreticideki](active-directory-b2c-tutorials-spa.md) tek sayfalı uygulama (Spa), Kullanıcı kaydı ve oturum açma için Azure AD B2C kullanır ve *frabrikamb2c* demo kiracısı tarafından korunan ASP.NET Core Web API 'sini çağırır.

Bu bölümde, tek sayfalı uygulamayı Azure AD B2C *kiracınız* tarafından korunan ve yerel makinenizde çalıştırdığınız ASP.NET Core Web API 'sini çağırmak üzere güncelleşolursunuz.

SPA 'daki ayarları değiştirmek için:

1. Önceki öğreticide indirdiğiniz veya Klonladığınız [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] projesinde *index. html* dosyasını açın.
1. Örneği için URI ile birlikte yapılandırın. daha önce oluşturduğunuz ve Web API 'sinin URL 'si. *okuma* kapsamı.
    1. `appConfig` tanımında, `b2cScopes` değerini kapsamın tam URI 'siyle değiştirin (daha önce kaydettiğiniz **kapsam** değeri).
    1. `webApi` değerini, Web API uygulamasını önceki bir adımda kaydettiğinizde eklediğiniz yeniden yönlendirme URI 'SI olarak değiştirin.

    `appConfig` tanımı aşağıdaki kod bloğuna benzer olmalıdır (kiracı adınızla `<your-tenant-name>`yerine):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>SPA ve Web API 'sini çalıştırma

Son olarak, hem ASP.NET Core Web API 'sini hem de Node. js tek sayfalı uygulamayı yerel makinenizde çalıştırırsınız. Ardından, tek sayfalı uygulamada oturum açıp korunan API 'ye bir istek başlatmak için bir düğmeye basın.

Her iki uygulama da bu öğreticide yerel olarak çalıştırılsa da, güvenli kaydolma/oturum açma için Azure AD B2C kullanırlar ve korunan Web API 'sine erişim izni verir.

### <a name="run-the-aspnet-core-web-api"></a>ASP.NET Core Web API 'sini çalıştırma

Visual Studio 'da, *B2C-WebAPI. sln* çözümünü derlemek ve hata ayıklamak için **F5** 'e basın. Proje başlatıldığında, varsayılan tarayıcınızda Web API 'sinin istekler için kullanılabildiğini duyurduğunu bildiren bir Web sayfası görüntülenir.

Visual Studio yerine `dotnet` CLı kullanmayı tercih ediyorsanız:

1. Bir konsol penceresi açın ve *\*. csproj* dosyasını içeren dizine geçin. Örneğin:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. `dotnet run`yürüterek Web API 'sini derleyin ve çalıştırın.

    API çalışır duruma geldiğinde aşağıdakine benzer bir çıktı görmeniz gerekir (öğretici için `NETSDK1059` uyarılarını güvenle yoksayabilirsiniz):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Tek sayfalı uygulamayı çalıştırma

1. Bir konsol penceresi açın ve Node. js örneğini içeren dizine geçin. Örneğin:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Aşağıdaki komutları çalıştırın:

    ```console
    npm install && npm update
    node server.js
    ```

    Konsol penceresi, uygulamanın barındırıldığı bağlantı noktası numarasını görüntüler.

    ```console
    Listening on port 6420...
    ```

1. Uygulamayı görüntülemek için tarayıcınızda `http://localhost:6420` gidin.
1. [Önceki öğreticide](active-directory-b2c-tutorials-spa.md)kullandığınız e-posta adresini ve parolayı kullanarak oturum açın. Başarılı oturum açma sonrasında `User 'Your Username' logged-in` iletisini görmeniz gerekir.
1. **Web API 'Si çağır** düğmesini seçin. SPA, Azure AD B2C bir yetkilendirme izni edinir ve sonra, dizin sayfasının içeriğini göstermek için korunan Web API 'sine erişir:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği yapılandırma

Artık bir SPA 'nın korumalı bir Web API 'sinden kaynak isteği gördüğünüze göre, bu uygulama türlerinin birbirleriyle ve Azure AD B2C nasıl etkileşime gireceğini daha ayrıntılı bir şekilde anlayın.

> [!div class="nextstepaction"]
> [Active Directory B2C > kullanılabilecek uygulama türleri](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
