---
title: "Öğretici: kullanıcılar oturum açıp bir elektron masaüstü uygulamasında Microsoft Graph API 'sini çağırma | Mavisi"
titleSuffix: Microsoft identity platform
description: Bu öğreticide, kullanıcıları oturum açıp kimlik doğrulama kod akışını kullanarak Microsoft Identity platformundan bir erişim belirteci elde edebilir ve Microsoft Graph API 'sini çağırabilir.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: e9e36a3ed2cab05eb63168452bc8472656ab5efb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644298"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Öğretici: kullanıcılar oturum açıp bir elektron masaüstü uygulamasında Microsoft Graph API 'sini çağırma

Bu öğreticide, PCE ile yetkilendirme kodu akışını kullanarak kullanıcılar ve Microsoft Graph çağıran bir elektron masaüstü uygulaması oluşturacaksınız. Oluşturduğunuz masaüstü uygulaması, [Node.jsIçin Microsoft kimlik doğrulama kitaplığı 'nı (msal) ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)kullanır.

Bu öğreticideki adımları izleyerek şunları yapın:

> [!div class="checklist"]
> - Uygulamayı Azure portal kaydetme
> - Elektron masaüstü uygulaması projesi oluşturma
> - Uygulamanıza kimlik doğrulama mantığı ekleyin
> - Web API 'SI çağırmak için bir yöntem ekleme
> - Uygulama Kayıt ayrıntıları ekleme
> - Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

- [Node.js](https://nodejs.org/en/download/)
- [Tron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

## <a name="register-the-application"></a>Uygulamayı kaydetme

İlk olarak, uygulamanızı kaydetmek için [Microsoft Identity platformu ile uygulama kaydetme](quickstart-register-app.md) bölümündeki adımları uygulayın.

Uygulama kaydınız için aşağıdaki ayarları kullanın:

- Ad: `ElectronDesktopApp` (önerilen)
- Desteklenen hesap türleri: **herhangi bir kuruluş dizinindeki (herhangi bir Azure ad dizini-çok kiracılı) hesaplar ve kişisel Microsoft hesapları (ör. Skype, Xbox)**
- Platform türü: **mobil ve Masaüstü uygulamaları**
- Yeniden yönlendirme URI 'SI: `msal://redirect`

## <a name="create-the-project"></a>Proje oluşturma

Uygulamanızı barındırmak için bir klasör oluşturun, örneğin *ElectronDesktopApp*.

1. İlk olarak, terminalinizdeki proje dizininize geçin ve aşağıdaki `npm` komutları çalıştırın:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Ardından, *uygulama* adlı bir klasör oluşturun. Bu klasörün içinde, Kullanıcı arabirimi olarak kullanılacak *index.html* adlı bir dosya oluşturun. Aşağıdaki kodu buraya ekleyin:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Sonra, *main.js* adlı dosyayı oluşturun ve aşağıdaki kodu ekleyin:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

Yukarıdaki kod parçacığında, bir elektron ana pencere nesnesi başladık ve elektron penceresi ile etkileşimler için bazı olay işleyicileri oluşturacağız. Ayrıca yapılandırma parametrelerini içeri aktardık, oturum açma, oturum kapatma ve belirteç alma işlemleri için *Authprovider* sınıfını örnekliyoruz ve Microsoft Graph API 'sini çağıracağız.

4. Aynı klasörde (*uygulama*), *renderer.js* adlı başka bir dosya oluşturun ve aşağıdaki kodu ekleyin:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Son olarak, uygulama **olaylarını** açıklamak için dizelerin sabitlerini depolayacak *constants.js* adlı bir dosya oluşturun:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Artık, elektron uygulamanız için basit bir GUI ve etkileşimler vardır. Öğreticinin geri kalanını tamamladıktan sonra, projenizin dosya ve klasör yapısı şuna benzer olmalıdır:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Uygulamanıza kimlik doğrulama mantığı ekleyin

*Uygulama* klasörü ' nde, *AuthProvider.js* adlı bir dosya oluşturun. Bu, MSAL düğümünü kullanarak oturum açma, oturum kapatma, belirteç alma, hesap seçimi ve ilgili kimlik doğrulama görevlerini işleyecek bir kimlik doğrulama sağlayıcısı sınıfı içerir. Aşağıdaki kodu buraya ekleyin:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

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

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

Yukarıdaki kod parçacığında, ilk olarak `PublicClientApplication` bir yapılandırma nesnesi () GEÇIREREK msal düğümünü başlatıyoruz `msalConfig` . Daha sonra `login` , `logout` `getToken` ana modül (*main.js*) tarafından çağrılacak Yöntemler ve yöntemleri ortaya çıkardık. Ve ' de, `login` `getToken` önce bir yetkilendirme kodu isteyerek ve sonra bunu msal node public API kullanarak bir belirteçle değiş tokuş yaparak kimlik ve erişim belirteçleri elde ediyoruz `acquireTokenByCode` .

## <a name="add-a-method-to-call-a-web-api"></a>Web API 'SI çağırmak için bir yöntem ekleme

*fetch.js* adlı başka bir dosya oluşturun. Bu dosya, Microsoft Graph API 'sine REST çağrıları yapmak için bir Axios HTTP istemcisi içerir.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Uygulama Kayıt ayrıntıları ekleme

Son olarak, belirteçleri alırken kullanılacak uygulama kayıt ayrıntılarını depolamak için bir ortam dosyası oluşturun. Bunu yapmak için örneğin kök klasörü içinde *. env* adlı bir dosya oluşturun (*ElectronDesktopApp*) ve aşağıdaki kodu ekleyin:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Bu ayrıntıları Azure uygulama kayıt portalından elde ettiğiniz değerlerle birlikte girin:

- `Enter_the_Tenant_Id_here` aşağıdakilerden biri olmalıdır:
  - Uygulamanız *bu kuruluş dizinindeki hesapları* destekliyorsa, bu DEĞERI **Kiracı kimliği** veya **kiracı adı** ile değiştirin. Örneğin, `contoso.microsoft.com`.
  - Uygulamanız *herhangi bir kuruluş dizinindeki hesapları* destekliyorsa, bu değeri ile değiştirin `organizations` .
  - Uygulamanız *herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesapları* destekliyorsa, bu değeri ile değiştirin `common` .
  - *Yalnızca kişisel Microsoft hesaplarına* yönelik desteği kısıtlamak için bu değeri ile değiştirin `consumers` .
- `Enter_the_Application_Id_Here`: Kaydettiğiniz uygulamanın **uygulama (istemci) kimliği** .
- `Enter_the_Cloud_Instance_Id_Here`: Uygulamanızın kaydedildiği Azure bulut örneği.
  - Ana (veya *küresel*) Azure bulutu için girin `https://login.microsoftonline.com/` .
  - **Ulusal** bulutlar için (örneğin, Çin), [Ulusal bulutlarda](authentication-national-cloud.md)uygun değerleri bulabilirsiniz.
- `Enter_the_Graph_Endpoint_Here` , uygulamanın iletişim kurması gereken Microsoft Graph API örneğidir.
  - **Genel** Microsoft Graph API uç noktası için bu dizenin her iki örneğini ile değiştirin `https://graph.microsoft.com/` .
  - **Ulusal** bulut dağıtımlarındaki uç noktalar için Microsoft Graph belgelerindeki [Ulusal bulut dağıtımları](/graph/deployments) bölümüne bakın.

## <a name="test-the-app"></a>Uygulamayı test etme

Uygulamanın oluşturulmasını tamamladınız ve artık elektron masaüstü uygulamasını başlatmaya hazır ve uygulamanın işlevlerini test etmeye hazırsınız.

1. Proje klasörünüzün kökü içinden aşağıdaki komutu çalıştırarak uygulamayı başlatın:

```console
electron App/main.js
```

2. Uygulama ana penceresinde, *index.html* dosyanızın Içeriğini ve **oturum aç** düğmesini görmeniz gerekir.

## <a name="test-sign-in-and-sign-out"></a>Oturum açma ve oturumu kapatma

*index.html* dosyası yüklendikten sonra **oturum aç**' ı seçin. Microsoft Identity platformu ile oturum açmanız istenir:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="oturum açma istemi":::

İstenen izinleri kabul ediyorsanız web uygulamaları, başarılı bir oturum açmayı belirten Kullanıcı adınızı görüntüler:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="başarılı oturum açma":::

## <a name="test-web-api-call"></a>Web API çağrısını sına

Oturum açtıktan sonra, Microsoft Graph API 'sine yapılan çağrıdan gelen yanıtta döndürülen Kullanıcı profili bilgilerini görüntülemek için **profil** görüntüle ' yi seçin:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="Microsoft Graph profil bilgileri":::

Kullanıcı hesabındaki iletileri görüntülemek için **postaları oku** ' yi seçin. Size bir onay ekranı sunulacaktır:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="oku. Mail izni için onay ekranı":::

Onay sonrasında, Microsoft Graph API 'sine yapılan çağrıdan gelen yanıtta döndürülen iletileri göreceksiniz:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="Microsoft Graph posta bilgileri":::

## <a name="how-the-application-works"></a>Uygulamanın nasıl çalıştığı

Bir Kullanıcı ilk kez **oturum aç** düğmesini seçtiğinde `getTokenInteractive` *AuthProvider.js* get yöntemi çağırılır. Bu yöntem, kullanıcıyı *Microsoft Identity platform uç noktasıyla* oturum açıp kullanıcının kimlik bilgilerini doğrulamak ve ardından bir **yetkilendirme kodu** elde etmek için yeniden yönlendirir. Bu kod daha sonra `acquireTokenByCode` msal düğümünün genel API 'sini kullanarak bir erişim belirteci için değiştirilir.

Bu noktada, bir PCE korumalı yetkilendirme kodu CORS korumalı belirteç uç noktasına gönderilir ve belirteçler için değiştirilir. Uygulamanız tarafından bir KIMLIK belirteci, erişim belirteci ve yenileme belirteci alınır ve MSAL düğümü tarafından işlenir ve belirteçlerde yer alan bilgiler önbelleğe alınır.

KIMLIK belirteci, kullanıcı hakkındaki, görünen adı gibi temel bilgileri içerir. Erişim belirtecinin ömrü sınırlı ve 24 saat sonra dolar. Korunan kaynağa erişim için bu belirteçleri kullanmayı planlıyorsanız, belirtecin uygulamanızın geçerli bir kullanıcıya verildiğini garantilemek için arka uç sunucunuz tarafından *doğrulanmalıdır* .

Bu öğreticide oluşturduğunuz masaüstü uygulaması, bir erişim belirtecini istek üst bilgisinde taşıyıcı belirteci olarak kullanarak Microsoft Graph API 'sine REST çağrısı yapar ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

Microsoft Graph API 'SI, kullanıcının profilini okumak için *Kullanıcı. Read* kapsamını gerektirir. Varsayılan olarak, bu kapsam Azure portal kayıtlı her uygulamaya otomatik olarak eklenir. Microsoft Graph için diğer API 'Ler ve arka uç sunucunuza yönelik özel API 'Ler için ek kapsamlar gerekebilir. Örneğin, Microsoft Graph API 'SI, kullanıcının e-postasını listelemek için *mail. Read* kapsamını gerektirir.

Kapsamları eklerken, kullanıcılarınızın eklenen kapsamlar için ek onay sağlaması istenebilir.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Identity platformunda Node.js ve elektron masaüstü uygulaması geliştirmeye daha ayrıntılı bilgi edinmek istiyorsanız, bkz. çok parçalı senaryo serimiz:

> [!div class="nextstepaction"]
> [Senaryo: Web API 'Lerini çağıran masaüstü uygulaması](scenario-desktop-overview.md)
