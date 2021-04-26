---
title: 'Hızlı başlangıç: Node.js masaüstü uygulamasından Microsoft Graph çağırma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir Node.js elektron masaüstü uygulamasının, bir Microsoft kimlik platformu uç noktası tarafından korunan bir API 'yi çağırmak için kullanıcılara nasıl oturum açıp erişim belirteci alabileceğinizi öğrenirsiniz.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653278"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Hızlı başlangıç: bir erişim belirteci alın ve bir elektron masaüstü uygulamasından Microsoft Graph API 'sini çağırın

Bu hızlı başlangıçta, bir elektron masaüstü uygulamasının kullanıcılara nasıl oturum açıp Microsoft Graph API 'sini çağırmak için erişim belirteçleri edindiğini gösteren bir kod örneği indirip çalıştırırsınız.

Bu hızlı başlangıçta, [msal node) Node.js Için Microsoft kimlik doğrulama kitaplığı](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) , [PKI CE yetkilendirme kodu akışıyla](v2-oauth2-auth-code-flow.md)birlikte kullanılır.

## <a name="prerequisites"></a>Önkoşullar

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Örnek uygulamayı kaydedin ve indirin
>
> Başlamak için aşağıdaki adımları izleyin.
>
> #### <a name="step-1-register-the-application"></a>1. Adım: uygulamayı kaydetme
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze el ile eklemek için şu adımları izleyin:
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. Uygulamanız için bir **ad** girin (örneğin,) `msal-node-desktop` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
> 1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
> 1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
> 1. **Platform**  >  **mobil ve Masaüstü uygulamaları Ekle '** yi seçin.
> 1. **Yeniden yönlendirme URI 'leri** bölümünde, girin `msal://redirect` .
> 1. **Yapılandır**'ı seçin.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1. Adım: uygulamayı Azure portal yapılandırma
> Bu hızlı başlangıçta çalışması için kod örneği için **msal://Redirect** olarak bir yanıt URL 'si eklemeniz gerekir.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişikliği benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-windows-desktop/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-electron-sample-project"></a>2. Adım: elektron örnek projesini Indirin

> [!div renderon="docs"]
> [Kod örneğini indirin](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Kod örneğini indirin](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>3. Adım: elektron örnek projesini yapılandırma
>
> 1. ZIP dosyasını, diskin köküne yakın bir yerel klasöre ayıklayın, örneğin, *C:/Azure-Samples*.
> 1. *. Env* 'yi düzenleyin ve alanların değerlerini `TENANT_ID` ve `CLIENT_ID` Aşağıdaki kod parçacığını değiştirin:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Konum:
>    - `Enter_the_Application_Id_Here` - kaydettiğiniz uygulamanın **Uygulama (istemci) Kimliği** değeridir.
>    - `Enter_the_Tenant_Id_Here` -Bu değeri **Kiracı kimliği** veya **kiracı adıyla** değiştirin (örneğin, contoso.Microsoft.com)
>
> > [!TIP]
> > **Uygulama (istemci) kimliği**, **Dizin (kiracı) kimliği** değerlerini bulmak Için Azure Portal uygulamanın **genel bakış** sayfasına gidin.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. Adım: uygulamayı çalıştırma

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>4. Adım: uygulamayı çalıştırma

Bu örneğin bağımlılıklarını bir kez yüklemeniz gerekir:

```console
npm install
```

Ardından, komut istemi veya konsolu aracılığıyla uygulamayı çalıştırın:

```console
npm start
```

Uygulamanın kullanıcı arabirimini bir **oturum açma** düğmesi ile görmeniz gerekir.

## <a name="about-the-code"></a>Kod hakkında

Aşağıda, örnek uygulamanın bazı önemli yönleri ele alınmıştır.

### <a name="msal-node"></a>MSAL düğümü

[Msal node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) , Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılan ve kullanıcılara oturum açmak için kullanılan bir kitaplıktır. MSAL düğümünü masaüstü uygulamalarıyla kullanma hakkında daha fazla bilgi için [Bu makaleye](scenario-desktop-overview.md)bakın.

Aşağıdaki NPM komutunu çalıştırarak MSAL düğümünü yükleyebilirsiniz.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL başlatma

Aşağıdaki kodu ekleyerek MSAL düğümü için başvuru ekleyebilirsiniz:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Sonra şu kodu kullanarak MSAL'yi başlatın:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Konum: |Description |
> |---------|---------|
> | `clientId` | **Uygulama (istemci) Kimliği**, Azure portalda kayıtlı uygulamadır. Bu değeri Azure portalda uygulamanın **Genel bakış** sayfasında bulabilirsiniz. |
> | `authority`    | Kimlik doğrulaması yapılacak kullanıcı için STS uç noktası. Genellikle `https://login.microsoftonline.com/{tenant}` {Tenant}, kiracınızın adı veya kiracı kimliğiniz olduğu genel bulut için.|

### <a name="requesting-tokens"></a>Belirteç isteme

Yetkilendirme kodu akışının ilk baındaki PKCE, uygun parametrelerle bir yetkilendirme kodu isteği hazırlayın ve gönderin. Daha sonra akışın ikinci bacağı yetkilendirme kodu yanıtını dinler. Kod alındıktan sonra, bir belirteç almak için bunu Exchange.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Konum:| Description |
> |---------|---------|
> | `authWindow` | İşlemdeki geçerli elektron penceresi. |
> | `tokenRequest` | `"User.Read"`Microsoft Graph veya `"api://<Application ID>/access_as_user"` özel Web API 'leri için istenen kapsamları içerir. |

## <a name="next-steps"></a>Sonraki adımlar

MSAL node ile elektron Masaüstü uygulama geliştirme hakkında daha fazla bilgi için bkz. öğreticiye bakın:

> [!div class="nextstepaction"]
> [Öğretici: kullanıcılar oturum açıp bir elektron masaüstü uygulamasında Microsoft Graph API 'sini çağırma](tutorial-v2-nodejs-desktop.md)