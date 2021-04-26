---
title: 'Öğretici: auth Code Flow kullanan bir JavaScript tek sayfalı uygulama oluşturma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, kullanıcıların oturum açmasını sağlayan bir JavaScript SPA oluşturun ve Microsoft Identity platformu 'ndan bir erişim belirteci elde etmek ve Microsoft Graph API 'sini çağırmak için kimlik doğrulama kod akışını kullanabilirsiniz.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 1ec046ca6b42a5ca8f33b0347c562c85abd42684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756171"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Öğretici: Kullanıcı oturum açma ve kimlik doğrulama kod akışı kullanarak JavaScript tek sayfalı uygulama (SPA) Microsoft Graph API 'sini çağırma

Bu öğreticide, PCE ile yetkilendirme kodu akışını kullanarak, kullanıcılar ve Microsoft Graph çağıran bir JavaScript tek sayfalı uygulama (SPA) oluşturacaksınız. Oluşturduğunuz SPA, JavaScript v 2.0 için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır.

Bu öğreticide:
> [!div class="checklist"]
> * PCE ile OAuth 2,0 yetkilendirme kodu akışını gerçekleştirme
> * Kişisel Microsoft hesaplarında ve iş ve okul hesaplarında oturum açın
> * Erişim belirteci alma
> * Microsoft Identity platform 'dan elde edilen erişim belirteçleri gerektiren Microsoft Graph veya kendi API 'nizi çağırın

MSAL.js 2,0, örtük izin akışı yerine tarayıcıda yetkilendirme kodu akışını destekleyerek MSAL.js 1,0 ' de geliştirilir. MSAL.js **2,0, örtük akışı desteklemez.**

## <a name="prerequisites"></a>Önkoşullar

* Yerel Web sunucusu çalıştırmak için [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

## <a name="how-the-tutorial-app-works"></a>Öğretici uygulamasının nasıl çalıştığı

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Tek sayfalı bir uygulamada yetkilendirme kodu akışını gösteren diyagram":::

Bu öğreticide oluşturduğunuz uygulama, JavaScript SPA 'nın Microsoft Identity platformundan güvenlik belirteçleri alarak Microsoft Graph API 'sini sorgulamasını sağlar. Bu senaryoda, bir Kullanıcı oturum açtıktan sonra yetkilendirme üstbilgisindeki HTTP isteklerine bir erişim belirteci istenir ve eklenir. Belirteç alma ve yenileme, JavaScript (MSAL.js) için Microsoft kimlik doğrulama kitaplığı tarafından işlenir.

Bu öğretici aşağıdaki kitaplığı kullanır:

JavaScript v 2.0 tarayıcı paketi için Microsoft kimlik doğrulama Kitaplığı [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

## <a name="get-the-completed-code-sample"></a>Tamamlanan kod örneğini al

Bunun yerine Bu öğreticinin tamamlanmış örnek projesini indirmek ister misiniz? Projeyi Node.js gibi yerel bir Web sunucusu kullanarak çalıştırmak için, [MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) deposunu kopyalayın:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Daha sonra kod örneğini yürütmeden önce yapılandırmak için [yapılandırma adımına](#register-your-application)atlayın.

Öğreticiye devam etmek ve uygulamayı kendiniz derlemek için bir sonraki bölüme [geçin.](#prerequisites)

## <a name="create-your-project"></a>Projenizi oluşturun

[Node.js](https://nodejs.org/en/download/) yükledikten sonra, uygulamanızı barındıracak bir klasör oluşturun, örneğin, *msal-Spa-öğreticisi*.

Sonra, *index.html* dosyanızı sağlamak için küçük bir [hızlı](https://expressjs.com/) Web sunucusu uygulayın.

1. İlk olarak, terminalinizdeki proje dizininize geçin ve aşağıdaki `npm` komutları çalıştırın:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Sonra, *server.js* adlı dosyayı oluşturun ve aşağıdaki kodu ekleyin:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Artık SPA 'larınızı karşılamak için küçük bir Web sunucusu var. Öğreticinin geri kalanını tamamladıktan sonra, projenizin dosya ve klasör yapısı şuna benzer olmalıdır:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>SPA Kullanıcı arabirimini oluşturma

1. Proje dizininizde bir *uygulama* klasörü oluşturun ve bu KLASÖRDE JavaScript Spa 'niz için bir *index.html* dosyası oluşturun. Bu dosya, **önyükleme 4 çerçevesiyle** oluşturulmuş bir kullanıcı arabirimi uygular ve yapılandırma, kimlik doğrulama ve API çağrıları için betik dosyalarını içeri aktarır.

    *index.html* dosyasında aşağıdaki kodu ekleyin:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Ayrıca, *uygulama* klasöründe, *ui.js* adlı bir dosya oluşturun ve aşağıdaki kodu ekleyin. Bu dosya, DOM öğelerine erişir ve bu öğeleri güncelleştirir.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Uygulamanızı kaydetme

SPA 'niz için bir uygulama kaydı oluşturmak üzere [tek sayfalı uygulama: uygulama kaydı](scenario-spa-app-registration.md) bölümündeki adımları izleyin.

[Yeniden yönlendirme URI 'si: MSAL.js 2,0 kimlik doğrulama kodu Flow](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) adımla, `http://localhost:3000` Bu öğretici uygulamasının çalıştığı varsayılan konumu girin.

Farklı bir bağlantı noktası kullanmak istiyorsanız `http://localhost:<port>` , `<port>` tercıh ettiğiniz TCP bağlantı noktası numaranız yazın. Dışında bir bağlantı noktası numarası belirtirseniz `3000` , *server.js* tercih ettiğiniz bağlantı noktası numarası ile güncelleştirin.

### <a name="configure-your-javascript-spa"></a>JavaScript SPA 'nizi yapılandırma

Kimlik doğrulaması için yapılandırma parametrelerinizi içerecek şekilde *uygulama* klasöründe *authConfig.js* adlı bir dosya oluşturun ve ardından aşağıdaki kodu ekleyin:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

`msalConfig`Bölümündeki değerleri burada açıklandığı gibi değiştirin:

- `Enter_the_Application_Id_Here`: Kaydettiğiniz uygulamanın **uygulama (istemci) kimliği** .
- `Enter_the_Cloud_Instance_Id_Here`: Uygulamanızın kaydedildiği Azure bulut örneği.
  - Ana (veya *küresel*) Azure bulutu için girin `https://login.microsoftonline.com` .
  - **Ulusal** bulutlar için (örneğin, Çin), [Ulusal bulutlarda](authentication-national-cloud.md)uygun değerleri bulabilirsiniz.
- `Enter_the_Tenant_info_here` aşağıdakilerden biri olmalıdır:
  - Uygulamanız *bu kuruluş dizinindeki hesapları* destekliyorsa, bu DEĞERI **Kiracı kimliği** veya **kiracı adı** ile değiştirin. Örneğin, `contoso.microsoft.com`.
  - Uygulamanız *herhangi bir kuruluş dizinindeki hesapları* destekliyorsa, bu değeri ile değiştirin `organizations` .
  - Uygulamanız *herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesapları* destekliyorsa, bu değeri ile değiştirin `common` .
  - *Yalnızca kişisel Microsoft hesaplarına* yönelik desteği kısıtlamak için bu değeri ile değiştirin `consumers` .
- `Enter_the_Redirect_Uri_Here`, `http://localhost:3000` değeridir.

`authority`Genel Azure bulutu kullanıyorsanız, *authConfig.js* değer şuna benzer olmalıdır:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Hala *uygulama* klasöründe, *graphConfig.js* adlı bir dosya oluşturun. Uygulamanızı Microsoft Graph API çağrısı için yapılandırma parametrelerini sağlamak üzere aşağıdaki kodu ekleyin:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

`graphConfig`Bölümündeki değerleri burada açıklandığı gibi değiştirin:

- `Enter_the_Graph_Endpoint_Here` , uygulamanın iletişim kurması gereken Microsoft Graph API örneğidir.
  - **Genel** Microsoft Graph API uç noktası için bu dizenin her iki örneğini ile değiştirin `https://graph.microsoft.com` .
  - **Ulusal** bulut dağıtımlarındaki uç noktalar için Microsoft Graph belgelerindeki [Ulusal bulut dağıtımları](/graph/deployments) bölümüne bakın.

`graphMeEndpoint` `graphMailEndpoint` Genel uç noktasını kullanıyorsanız, *graphConfig.js* ve değerleri aşağıdakine benzer olmalıdır:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-user"></a>Kullanıcı oturumu açmak için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanın

### <a name="pop-up"></a>Açılır pencere

*Uygulama* klasöründe, *authPopup.js* adlı bir dosya oluşturun ve oturum açma açılır penceresi için aşağıdaki kimlik doğrulama ve belirteç alma kodunu ekleyin:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Yeniden yönlendirme

*Uygulama* klasöründe *authRedirect.js* adlı bir dosya oluşturun ve oturum yeniden yönlendirme için aşağıdaki kimlik doğrulama ve belirteç alma kodunu ekleyin:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Kod nasıl çalışır

Bir Kullanıcı ilk kez **oturum aç** düğmesini seçtiğinde, `signIn` yöntemi `loginPopup` kullanıcının oturum açmasını çağırır. `loginPopup`Yöntemi, kullanıcının kimlik bilgilerini sormak ve doğrulamak Için *Microsoft Identity platform uç noktasıyla* bir açılır pencere açar. Başarılı bir oturum açma işleminden sonra, *msal.js* [yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md)başlatır.

Bu noktada, bir PCE korumalı yetkilendirme kodu CORS korumalı belirteç uç noktasına gönderilir ve belirteçler için değiştirilir. Uygulamanız tarafından bir KIMLIK belirteci, erişim belirteci ve yenileme belirteci alınır ve *msal.js* tarafından işlenir ve belirteçlerde yer alan bilgiler önbelleğe alınır.

KIMLIK belirteci, kullanıcı hakkındaki, görünen adı gibi temel bilgileri içerir. KIMLIK belirteci tarafından verilen herhangi bir veriyi kullanmayı planlıyorsanız, belirtecin uygulamanız için geçerli bir kullanıcıya verildiğini güvence altına almak için arka uç sunucunuz tarafından *doğrulanmalıdır* .

Erişim belirtecinin ömrü sınırlı ve 24 saat sonra dolar. Yenileme belirteci, sessizce yeni erişim belirteçleri elde etmek için kullanılabilir.

Bu öğreticide oluşturduğunuz SPA, `acquireTokenSilent` `acquireTokenPopup` Kullanıcı profili bilgileri IÇIN Microsoft Graph API 'sini sorgulamak üzere kullanılan bir *erişim belirteci* almak için ve/veya çağırır. KIMLIK belirtecini doğrulayan bir örneğe ihtiyacınız varsa, GitHub 'da [Active-Directory-JavaScript-singlepageapp-DotNet-WebApi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) örnek uygulamasına bakın. Örnek, belirteç doğrulaması için bir ASP.NET Web API 'SI kullanır.

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma

İlk oturum açma işleminden sonra uygulamanız, kullanıcılardan korumalı bir kaynağa erişmesi gereken her seferinde (bir belirteç istemek için) kimlik doğrulaması yapmasını istemez. Bu tür yeniden kimlik doğrulama isteklerini engellemek için çağrısı yapın `acquireTokenSilent` . Ancak, kullanıcıların Microsoft Identity platformu ile etkileşime geçmesini zorunlu kılmak isteyebileceğiniz bazı durumlar vardır. Örnek:

- Parolanın süresi sona erdiği için kullanıcıların kimlik bilgilerini yeniden girmesi gerekir.
- Uygulamanız bir kaynağa erişim istiyor ve kullanıcının izni gerekiyor.
- İki öğeli kimlik doğrulaması gereklidir.

Çağırma `acquireTokenPopup` bir açılır pencere açar (veya `acquireTokenRedirect` kullanıcıları Microsoft Identity platformu 'na yönlendirir). Bu pencerede, kullanıcıların kimlik bilgilerini onaylayarak, gerekli kaynağa onay vererek veya iki öğeli kimlik doğrulamasını tamamlayarak etkileşimde olmaları gerekir.

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

`acquireTokenSilent`Yöntemi, Kullanıcı etkileşimi olmadan belirteç alımı ve yenilemeyi işler. `loginPopup`(Veya `loginRedirect` ) ilk kez yürütüldükten sonra, `acquireTokenSilent` sonraki çağrılar için korunan kaynaklara erişmek üzere kullanılan belirteçleri elde etmek için yaygın olarak kullanılan yöntemdir. (Belirteçleri istek veya yenileme çağrısı sessizce yapılır.) `acquireTokenSilent` bazı durumlarda başarısız olabilir. Örneğin, kullanıcının parolasının kullanım süreniz olabilir. Uygulamanız bu özel durumu iki şekilde işleyebilir:

1. `acquireTokenPopup`Kullanıcı oturum açma isteğini tetiklemek için hemen bir çağrı yapın. Bu model yaygın olarak, uygulamada kullanıcının kullanabileceği kimliği doğrulanmamış içerik olmayan çevrimiçi uygulamalarda kullanılır. Bu Kılavuzlu kurulum tarafından oluşturulan örnek bu düzeni kullanır.
1. Kullanıcıya, kullanıcının oturum açmak için doğru zamanı belirleyebilmesi için etkileşimli bir oturum açma 'nın gerekli olduğunu ve uygulamanın daha sonra yeniden denenebilmesini sağlamak için görsel olarak kullanıcıya işaret edin `acquireTokenSilent` . Bu teknik yaygın olarak, kullanıcı uygulamanın kesintiye uğramadan diğer işlevlerini kullanabilmesi durumunda kullanılır. Örneğin, uygulamada kimliği doğrulanmamış içerik bulunabilir. Bu durumda, Kullanıcı, korunan kaynağa erişmek veya güncel olmayan bilgileri yenilemek için ne zaman oturum açmak istediğinize karar verebilir.

> [!NOTE]
> Bu öğretici, `loginPopup` `acquireTokenPopup` Varsayılan olarak ve yöntemlerini kullanır. Internet Explorer kullanıyorsanız, `loginRedirect` `acquireTokenRedirect` Internet Explorer ve açılır pencereler ile ilgili [bilinen bir sorun](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) nedeniyle ve yöntemlerini kullanmanızı öneririz. Yeniden yönlendirme yöntemlerini kullanarak aynı sonucu elde etmeye yönelik bir örnek için bkz. GitHub üzerinde [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) .

## <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 'sini çağırma

*Uygulama* klasöründe *graph.js* adlı dosyayı oluşturun ve Microsoft Graph API 'sine Rest çağrıları yapmak için aşağıdaki kodu ekleyin:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

Bu öğreticide oluşturulan örnek uygulamada, `callMSGraph()` yöntemi, `GET` bir belirteç gerektiren korumalı bir kaynağa karşı http isteği oluşturmak için kullanılır. İstek daha sonra içeriği çağırana döndürür. Bu yöntem, alınan belirteci *http yetkilendirme üst bilgisine* ekler. Bu öğreticide oluşturulan örnek uygulamada, korunan kaynak, oturum açan kullanıcının profil bilgilerini görüntüleyen Microsoft Graph API *Me* uç noktasıdır.

## <a name="test-your-application"></a>Uygulamanızı test etme

Uygulamanın oluşturulmasını tamamladınız ve artık Node.js Web sunucusunu başlatmaya ve uygulamanın işlevlerini test etmeye hazır.

1. Node.js Web sunucusunu, proje klasörünüzün kökünün içinden aşağıdaki komutu çalıştırarak başlatın:

   ```console
   npm start
   ```
1. Tarayıcınızda, `http://localhost:3000` `http://localhost:<port>` `<port>` Web sunucunuzun dinlediği bağlantı noktası olan veya ' a gidin. *index.html* dosyanızın Içeriğini ve **oturum aç** düğmesini görmeniz gerekir.

### <a name="sign-in-to-the-application"></a>Uygulamada oturum açma

Tarayıcı *index.html* dosyanızı yükledikten sonra **oturum aç**' ı seçin. Microsoft Identity platformu ile oturum açmanız istenir:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Oturum açma iletişim kutusunu görüntüleyen Web tarayıcısı":::

### <a name="provide-consent-for-application-access"></a>Uygulama erişimi için onay sağlayın

Uygulamanızda ilk kez oturum açtığınızda profilinize erişim vermeniz ve oturumunuzu açmanız istenir:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Web tarayıcısında görünen içerik iletişim kutusu":::

İstenen izinleri kabul ediyorsanız web uygulamaları, başarılı bir oturum açmayı belirten Kullanıcı adınızı görüntüler:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Web tarayıcısında başarılı bir oturum açma işleminin sonuçları":::

### <a name="call-the-graph-api"></a>Graph API çağırın

Oturum açtıktan sonra, Microsoft Graph API 'sine yapılan çağrıdan gelen yanıtta döndürülen Kullanıcı profili bilgilerini görüntülemek için **profil** görüntüle ' yi seçin:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Tarayıcıda görünen Microsoft Graph profil bilgileri":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Kapsamlar ve temsilci izinleri hakkında daha fazla bilgi

Microsoft Graph API 'SI, kullanıcının profilini okumak için *Kullanıcı. Read* kapsamını gerektirir. Varsayılan olarak, bu kapsam Azure portal kayıtlı her uygulamaya otomatik olarak eklenir. Microsoft Graph için diğer API 'Ler ve arka uç sunucunuza yönelik özel API 'Ler için ek kapsamlar gerekebilir. Örneğin, Microsoft Graph API 'SI, kullanıcının e-postasını listelemek için *mail. Read* kapsamını gerektirir.

Kapsamları eklerken, kullanıcılarınızın eklenen kapsamlar için ek onay sağlaması istenebilir.

Bir arka uç API 'SI için gerekli olmayan bir kapsam yoksa, `clientId` belirteç almak için çağrılarında kapsam olarak kullanabilirsiniz.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Identity platformunda JavaScript tek sayfalı uygulama geliştirmeyi daha ayrıntılı bir şekilde incelemek istiyorsanız, bkz. çok parçalı senaryo serimiz:

> [!div class="nextstepaction"]
> [Senaryo: tek sayfalı uygulama](scenario-spa-overview.md)