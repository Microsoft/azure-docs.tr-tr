---
title: 'Öğretici: Node.js & Express web uygulamasında kullanıcılar oturum açma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, bir Web uygulamasında oturum açma kullanıcıları için destek eklersiniz.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 58132b6d038abe0db02d1e58f8c8a8f64962ff49
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279723"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Öğretici: bir Node.js & Express web uygulamasında kullanıcılar oturum açma

Bu öğreticide, kullanıcıları oturum açan bir Web uygulaması oluşturacaksınız. Oluşturduğunuz Web uygulaması, [düğümü Için Microsoft kimlik doğrulama kitaplığı 'nı (msal)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)kullanır.

Bu öğreticideki adımları izleyerek şunları yapın:

> [!div class="checklist"]
> - Uygulamayı Azure portal kaydetme
> - Hızlı Web uygulaması projesi oluşturma
> - Kimlik doğrulama kitaplığı paketlerini yükler
> - Uygulama Kayıt ayrıntıları ekleme
> - Kullanıcı oturum açması için kod ekleme
> - Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

## <a name="register-the-application"></a>Uygulamayı kaydetme

İlk olarak, uygulamanızı kaydetmek için [Microsoft Identity platformu ile uygulama kaydetme](quickstart-register-app.md) bölümündeki adımları uygulayın.

Uygulama kaydınız için aşağıdaki ayarları kullanın:

- Ad: `ExpressWebApp` (önerilen)
- Desteklenen hesap türleri: **herhangi bir kuruluş dizinindeki (herhangi bir Azure ad dizini-çok kiracılı) hesaplar ve kişisel Microsoft hesapları (ör. Skype, Xbox)**
- Platform türü: **Web**
- Yeniden yönlendirme URI 'SI: `http://localhost:3000/redirect`
- İstemci parolası: `*********` (Bu değeri sonraki bir adımda kullanmak üzere Kaydet-yalnızca bir kez gösteriliyor)

## <a name="create-the-project"></a>Proje oluşturma

Uygulamanızı barındırmak için bir klasör oluşturun, örneğin, *ifade* edin.

1. İlk olarak, terminalinizdeki proje dizininize geçin ve aşağıdaki `npm` komutları çalıştırın:

```console
    npm init -y
    npm install --save express
```

2. Sonra, *index.js* adlı dosyayı oluşturun ve aşağıdaki kodu ekleyin:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Artık 3000 numaralı bağlantı noktasında çalışan basit bir Web sunucunuz var. Projenizin dosya ve klasör yapısı aşağıdakine benzer olmalıdır:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Kimlik doğrulama kitaplığını yükler

Bir terminalde proje dizininizin kökünü bulun ve NPM aracılığıyla MSAL node paketini yükledikten sonra.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Uygulama Kayıt ayrıntıları ekleme

Daha önce oluşturduğunuz *index.js* dosyasında aşağıdaki kodu ekleyin:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
```

Bu ayrıntıları Azure uygulama kayıt portalından elde ettiğiniz değerlerle birlikte girin:

- `Enter_the_Tenant_Id_here` aşağıdakilerden biri olmalıdır:
  - Uygulamanız *bu kuruluş dizinindeki hesapları* destekliyorsa, bu DEĞERI **Kiracı kimliği** veya **kiracı adı** ile değiştirin. Örneğin, `contoso.microsoft.com`.
  - Uygulamanız *herhangi bir kuruluş dizinindeki hesapları* destekliyorsa, bu değeri ile değiştirin `organizations` .
  - Uygulamanız *herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesapları* destekliyorsa, bu değeri ile değiştirin `common` .
  - *Yalnızca kişisel Microsoft hesaplarına* yönelik desteği kısıtlamak için bu değeri ile değiştirin `consumers` .
- `Enter_the_Application_Id_Here`: Kaydettiğiniz uygulamanın **uygulama (istemci) kimliği** .
- `Enter_the_Cloud_Instance_Id_Here`: Uygulamanızın kaydedildiği Azure bulut örneği.
  - Ana (veya *küresel*) Azure bulutu için girin `https://login.microsoftonline.com` .
  - **Ulusal** bulutlar için (örneğin, Çin), [Ulusal bulutlarda](authentication-national-cloud.md)uygun değerleri bulabilirsiniz.
- `Enter_the_Client_secret`: Bu değeri, daha önce oluşturduğunuz istemci sırrı ile değiştirin. Yeni bir anahtar oluşturmak için, Azure portal uygulama kayıt ayarlarındaki **sertifikaları & gizli** dizileri kullanın.

> [!WARNING]
> Kaynak kodda herhangi bir düz metin gizli dizisi daha fazla güvenlik riski oluşturur. Bu makalede yalnızca kolaylık olması için düz metin biçiminde bir gizli dizi kullanılmaktadır. Özellikle üretime dağıtmayı düşündüğünüz uygulamalar, gizli istemci uygulamalarınızda istemci gizli dizileri yerine [sertifika kimlik bilgilerini](active-directory-certificate-credentials.md) kullanın.

## <a name="add-code-for-user-login"></a>Kullanıcı oturum açması için kod ekleme

Daha önce oluşturduğunuz *index.js* dosyasında aşağıdaki kodu ekleyin:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Oturum açma testi

Uygulamanın oluşturulmasını tamamladınız ve artık uygulamanın işlevlerini test etmeye hazır.

1. Proje klasörünüzün kökünün içinden aşağıdaki komutu çalıştırarak Node.js konsol uygulamasını başlatın:

```console
   node index.js
```

2. Bir tarayıcı penceresi açın ve `http://localhost:3000` adresine gidin. Oturum açma ekranı görmeniz gerekir:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Azure AD oturum açma ekranı görüntüleme":::

3. Kimlik bilgilerinizi girdikten sonra, uygulama için izinleri onaylamanızı isteyen bir onay ekranı görmeniz gerekir.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Azure AD onay ekranı görüntüleniyor":::

## <a name="how-the-application-works"></a>Uygulamanın nasıl çalıştığı

Bu öğreticide, Azure portal Azure AD uygulama kaydınızdan elde edilen parametreleri içeren bir yapılandırma nesnesi (*Msalconfig*) GEÇIREREK bir msal node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) nesnesi başlattığını caksınız. Oluşturduğunuz Web uygulaması, oturum açma kullanıcıları için [OAuth 2,0 yetkilendirme kodu verme akışını](./v2-oauth2-auth-code-flow.md) KULLANıR ve kimlik ve erişim belirteçleri elde eder.

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Identity platformunda Node.js & Express web uygulaması geliştirmesi hakkında daha ayrıntılı bilgi edinmek istiyorsanız, bkz. çok parçalı senaryo serimiz:

> [!div class="nextstepaction"]
> [Senaryo: kullanıcılarda oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md)
