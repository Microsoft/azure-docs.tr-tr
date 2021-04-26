---
title: "Öğretici: Azure AD B2C kullanarak Node.js Web API 'sini koruma ve tek sayfalı bir uygulamaya (SPA) erişim izni verme"
titleSuffix: Azure AD B2C
description: Bu öğreticide, bir Node.js Web API 'sini korumak ve tek sayfalı bir uygulamadan çağırmak için Active Directory B2C nasıl kullanacağınızı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93421099"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Öğretici: Azure AD B2C ile tek sayfalı bir uygulamadan Node.js Web API 'sine koruma ve erişim izni verme

Bu öğreticide, tek sayfalı bir uygulamadan Azure Active Directory B2C (Azure AD B2C) korumalı Node.js Web API 'sinin nasıl çağrılacağını gösterir.

Bu öğreticide, ikinci bölümlü bir serinin saniyesi:

> [!div class="checklist"]
> * Azure AD B2C kiracınızda bir Web API uygulaması kaydı oluşturma
> * Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Kiracınızla çalışmak için bir Web API kodu örneğini değiştirme

Bu serideki [ilk öğreticide](tutorial-single-page-app.md) , kod örneğini indirmiş ve Azure AD B2C kiracınızda Kullanıcı akışı olan kullanıcıların oturum açması için değiştirmiş olursunuz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Öğreticideki adımları ve önkoşulları doldurun [: Azure AD B2C ile tek sayfalı bir uygulamada kimlik doğrulamasını etkinleştirme](tutorial-single-page-app.md)
* [Visual Studio Code](https://code.visualstudio.com/) veya başka bir kod Düzenleyicisi
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Web API'si uygulaması ekleme

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Kapsamları yapılandırma

Kapsamlar korumalı kaynaklara erişimi yönetmenin bir yolunu sunar. Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin, bazı kullanıcılar hem okuma hem de yazma erişimine sahipken diğer kullanıcıların salt okunur izinleri olabilir. Bu öğreticide Web API 'SI için hem okuma hem de yazma izinlerini tanımlarsınız.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

 `demo.read` Tek sayfalı uygulamayı yapılandırırken daha sonraki bir adımda kullanılacak kapsamın kapsamları altındaki değeri kaydedin. Tam kapsam değeri öğesine benzerdir `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>İzinleri verme

Başka bir uygulamadan korumalı bir Web API 'SI çağırmak için, bu uygulama izinlerini Web API 'sine vermeniz gerekir.

Önkoşul öğreticisinde, *spaapp1* adlı tek sayfalı bir uygulama oluşturdunuz. Bu öğreticide, *spaapp1* önceki bir bölümünde oluşturduğunuz Web API 'sini çağırmak için bu uygulamayı yapılandırırsınız.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Tek sayfalı web uygulamanıza, belirtilen kapsamlar için korunan Web API 'sine izin verildi. Bir kullanıcı tek sayfalı uygulamayı kullanmak üzere Azure AD B2C kimliğini doğrular. Tek sayfalı uygulama, korumalı Web API 'sine erişmek için Azure AD B2C bir erişim belirteci alır.

## <a name="configure-the-sample"></a>Örneği yapılandırma

Web API 'SI kayıtlı ve kapsamları tanımladığınıza göre, Web API kodunu Azure AD B2C kiracınızla çalışacak şekilde yapılandırın. Bu öğreticide, GitHub 'dan indirdiğinizde bir örnek Node.js Web API 'SI yapılandırırsınız.

[Bir \* . zip arşivi indirin](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) veya örnek Web API projesini GitHub 'dan kopyalayın. GitHub 'da doğrudan [Azure-Samples/Active-Directory-B2C-JavaScript-NodeJS-WebApi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) projesine de gidebilirsiniz.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Web API’sini yapılandırma

1. Kod Düzenleyicinizde dosya *config.js* açın.
1. Değişken değerlerini, daha önce oluşturduğunuz uygulama kaydını yansıtacak şekilde değiştirin. Ayrıca, `policyName` önkoşullarını, önkoşulların bir parçası olarak oluşturduğunuz Kullanıcı akışıyla güncelleştirin. Örneğin, *B2C_1_signupsignin1*.
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>CORS'yi etkinleştirme

Tek sayfalı uygulamanızın Node.js Web API 'sini çağırmasını sağlamak için, Web API 'sinde [CORS](https://expressjs.com/en/resources/middleware/cors.html) 'yi etkinleştirmeniz gerekir. Bir üretim uygulamasında, hangi etki alanının istek yapmakta olduğu konusunda dikkatli olmanız gerekir, ancak bu öğreticide herhangi bir etki alanından gelen isteklere izin verilir.

CORS 'yi etkinleştirmek için aşağıdaki ara yazılımı kullanın. Bu öğreticideki Node.js Web API kodu örneğinde, *index.js* dosyasına zaten eklendiniz.

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Tek sayfalı uygulamayı yapılandırma

Serideki [önceki öğreticideki](tutorial-single-page-app.md) tek sayfalı uygulama (Spa), Kullanıcı kaydı ve oturum açma için Azure AD B2C kullanır ve varsayılan olarak *fabrikamb2c* demo kiracısı tarafından korunan Node.js Web API 'sini çağırır.

Bu bölümde, tek sayfalı Web uygulamasını, Azure AD B2C *kiracınız* (ve yerel makinenizde çalıştırdığınız) tarafından korunan Node.js Web API 'sini çağırmak üzere güncelleşolursunuz.

SPA 'daki ayarları değiştirmek için:

1. Önceki öğreticide indirdiğiniz veya Klonladığınız [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] projesinde, *apiConfig.js* dosyasını *javascriptspa* klasörü içinde açın.
1. Örneği için URI ile birlikte yapılandırın. daha önce oluşturduğunuz ve Web API 'sinin URL 'si. *okuma* kapsamı.
    1. `apiConfig`Tanımında `b2cScopes` değeri *demo. Read* Scope (daha önce kaydettiğiniz **kapsam** değeri) için tam URI ile değiştirin.
    1. Bu `webApi` değeri, önceki bir adımda Web API uygulamasını kaydettiğinizde eklediğiniz yeniden yönlendirme URI 'si olarak değiştirin.

    API 'nin `/hello` uç noktada erişilebilir olduğundan, URI 'de */Hello* ' ı bırakın.

    `apiConfig`Tanım aşağıdaki kod bloğuna benzer olmalıdır, ancak bunun yerıne B2C kiracınızın adı ile birlikte şöyle görünmelidir `<your-tenant-name>` :

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>SPA ve Web API 'sini çalıştırma

Artık tek sayfalı uygulamanın API 'ye kapsamlı erişimini test etmeye hazırsınız. Yerel makinenizde hem Node.js Web API 'sini hem de örnek JavaScript tek sayfalı uygulamayı çalıştırın. Ardından, tek sayfalı uygulamada oturum açın ve korunan API 'ye bir istek başlatmak için **API çağrısı** düğmesini seçin.

Bu öğreticiyi izlediğinizde her iki uygulama da yerel olarak çalışıyor olsa da, bunları güvenli kaydolma/oturum açma için Azure AD B2C kullanacak ve korunan Web API 'sine erişim izni verecek şekilde yapılandırdık.

### <a name="run-the-nodejs-web-api"></a>Node.js Web API 'sini çalıştırma

1. Bir konsol penceresi açın ve Node.js Web API örneğini içeren dizine geçin. Örnek:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Aşağıdaki komutları çalıştırın:

    ```console
    npm install && npm update
    node index.js
    ```

    Konsol penceresi, uygulamanın barındırıldığı bağlantı noktası numarasını görüntüler.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Tek sayfalı uygulamayı çalıştırma

1. Başka bir konsol penceresi açın ve JavaScript SPA örneğini içeren dizine geçin. Örnek:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Aşağıdaki komutları çalıştırın:

    ```console
    npm install && npm update
    npm start
    ```

    Konsol penceresi, uygulamanın barındırıldığı bağlantı noktası numarasını görüntüler.

    ```console
    Listening on port 6420...
    ```

1. `http://localhost:6420`Uygulamayı görüntülemek için tarayıcınızda sayfasına gidin.

    ![Tarayıcıda gösterilen tek sayfalı uygulama örnek uygulaması](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. [Önceki öğreticide](tutorial-single-page-app.md)kullandığınız e-posta adresini ve parolayı kullanarak oturum açın. Başarılı oturum açma sırasında iletiyi görmeniz gerekir `User 'Your Username' logged-in` .
1. **API çağrısı** düğmesini seçin. SPA, Azure AD B2C bir yetkilendirme izni alır ve ardından, oturum açmış kullanıcının adını göstermek için korunan Web API 'sine erişir:

    ![API tarafından döndürülen Kullanıcı adı JSON sonucunu gösteren tarayıcıda tek sayfalı uygulama](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure AD B2C kiracınızda bir Web API uygulaması kaydı oluşturuldu
> * Web API 'SI için yapılandırılmış kapsamlar
> * Web API 'sine izin verildi
> * Kiracınızla çalışmak için bir Web API kodu örneğini değiştirme

Artık bir SPA 'nın korumalı bir Web API 'sinden kaynak isteği gördüğünüze göre, bu uygulama türlerinin birbirleriyle ve Azure AD B2C nasıl etkileşime gireceğini daha ayrıntılı bir şekilde anlayın.

> [!div class="nextstepaction"]
> [Active Directory B2C >kullanılabilecek uygulama türleri ](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
