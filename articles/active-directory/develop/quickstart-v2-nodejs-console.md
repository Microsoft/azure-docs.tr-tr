---
title: 'Hızlı başlangıç: Node.js konsol uygulamasından Microsoft Graph çağırma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir Node.js konsol uygulamasının bir erişim belirtecini nasıl alabilirim ve uygulamanın kendi kimliğini kullanarak bir Microsoft Identity platform uç noktası tarafından korunan bir API 'yi nasıl çağıracağınızı gösteren bir kod örneği indirir ve çalıştırırsınız
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653754"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Hızlı başlangıç: uygulamanın kimliğini kullanarak bir Node.js konsol uygulamasından belirteç alma ve Microsoft Graph API 'SI çağırma

Bu hızlı başlangıçta, bir Node.js konsol uygulamasının, Microsoft Graph API 'sini çağırmak ve dizindeki [kullanıcıların listesini](/graph/api/user-list) göstermek için uygulamanın kimliğini kullanarak nasıl erişim belirteci alabilirim olduğunu gösteren bir kod örneği indirip çalıştırırsınız. Kod örneği, katılımsız bir işin veya Windows hizmetinin bir kullanıcının kimliği yerine bir uygulama kimliğiyle nasıl çalışacağını gösterir.

Bu hızlı başlangıçta, [istemci kimlik bilgileri izni](v2-oauth2-client-creds-grant-flow.md)ile [Node.js (msal node) Için Microsoft kimlik doğrulama kitaplığı](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) kullanılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Örnek uygulamayı kaydedin ve indirin
>
> Başlamak için aşağıdaki adımları izleyin.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>1. Adım: uygulamayı kaydetme
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze el ile eklemek için şu adımları izleyin:
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. Uygulamanız için bir **ad** girin (örneğin,) `msal-node-cli` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
> 1. **Kaydet**’i seçin.
> 1. **Yönet**’in altında **Sertifikalar ve gizli diziler**’i seçin.
> 1. **İstemci gizli** dizileri altında **yeni istemci parolası**' nı seçin, bir ad girin ve ardından **Ekle**' yi seçin. Daha sonraki bir adımda kullanmak üzere gizli bir konuma gizli değeri kaydedin.
> 1. **Yönet** altında **API izinleri**  >  **bir izin Ekle**' yi seçin. **Microsoft Graph** seçin.
> 1. **Uygulama izinleri**' ni seçin.
> 1. **Kullanıcı** düğümü altında **User. Read. All**' ı seçin ve ardından **izin Ekle**' yi seçin.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Örnek uygulamayı indirme ve yapılandırma
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1. Adım: uygulamayı Azure portal yapılandırma
> Bu hızlı başlangıçta çalışması için kod örneği için, bir istemci parolası oluşturmanız ve Graph API **Kullanıcı. Read. All** uygulama iznini eklemeniz gerekir.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-netcore-daemon/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-nodejs-sample-project"></a>2. Adım: Node.js örnek projeyi Indirme

> [!div renderon="docs"]
> [Kod örneğini indirin](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Kod örneğini indirin](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>3. Adım: Node.js örnek projeyi yapılandırma
>
> 1. ZIP dosyasını, diskin köküne yakın bir yerel klasöre ayıklayın, örneğin, *C:/Azure-Samples*.
> 1. *. Env* 'yi düzenleyin ve alanları `TENANT_ID` , `CLIENT_ID` , ve değerlerini `CLIENT_SECRET` Aşağıdaki kod parçacığıyla değiştirin:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Konum:
>    - `Enter_the_Application_Id_Here` -daha önce kaydettiğiniz uygulamanın **uygulama (istemci) kimliği** . Bu KIMLIĞI, uygulama kaydının **genel bakış** bölmesinde Azure Portal bulun.
>    - `Enter_the_Tenant_Id_Here` -Bu değeri **KIRACı kimliği** veya **kiracı adıyla** değiştirin (örneğin, contoso.Microsoft.com).  Bu değerleri, Azure portal uygulama kaydının **genel bakış** bölmesinde bulabilirsiniz.
>    - `Enter_the_Client_Secret_Here` -Bu değeri, daha önce oluşturduğunuz istemci sırrı ile değiştirin. Yeni bir anahtar oluşturmak için, Azure portal uygulama kayıt ayarlarındaki **sertifikaları & gizli** dizileri kullanın.
>
> > [!WARNING]
> > Kaynak kodda herhangi bir düz metin gizli dizisi daha fazla güvenlik riski oluşturur. Bu makalede yalnızca kolaylık olması için düz metin biçiminde bir gizli dizi kullanılmaktadır. Özellikle üretime dağıtmayı düşündüğünüz uygulamalar, gizli istemci uygulamalarınızda istemci gizli dizileri yerine [sertifika kimlik bilgilerini](active-directory-certificate-credentials.md) kullanın.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. Adım: yönetici onayı

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. Adım: yönetici onayı

Bu noktada uygulamayı çalıştırmayı denerseniz, *HTTP 403-Yasak* hatası: ' ı alırsınız `Insufficient privileges to complete the operation` . Bu hata, *yalnızca uygulama izni* **yönetici onayı** gerektirdiğinden oluşur: dizininizin genel Yöneticisi uygulamanıza onay vermelidir. Rolünüze bağlı olarak aşağıdaki seçeneklerden birini belirleyin:

##### <a name="global-tenant-administrator"></a>Genel Kiracı Yöneticisi

> [!div renderon="docs"]
> Küresel kiracı yöneticisiyseniz, Azure portal uygulama kaydında **API izinleri** sayfasına gidin ve **{Tenant Name} Için yönetici onayı ver** ' i seçin (burada {Tenant Name} dizininizin adıdır).

> [!div renderon="portal" class="sxs-lookup"]
> Genel yöneticiyseniz, **API izinleri** sayfasına gidin **Enter_the_Tenant_Name_Here Için yönetici onayı ver** ' i seçin
> > [!div id="apipermissionspage"]
> > [API Izinleri sayfasına gidin]()

##### <a name="standard-user"></a>Standart Kullanıcı

Kiracınızın standart bir kullanıcısı kullanıyorsanız, uygulamanız için **yönetici onayı** vermesini istemek üzere bir genel yönetici yapmanız gerekir. Bunu yapmak için, yöneticinize aşağıdaki URL 'YI verin:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Konum:
>> * `Enter_the_Tenant_Id_Here` -Bu değeri **Kiracı kimliği** veya **kiracı adıyla** değiştirin (örneğin, contoso.Microsoft.com)
>> * `Enter_the_Application_Id_Here` - kaydettiğiniz uygulamanın **Uygulama (istemci) Kimliği** değeridir.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. Adım: uygulamayı çalıştırma

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. Adım: uygulamayı çalıştırma

Örnek kök klasörünü (nerede `package.json` bulunur) bir komut isteminde veya konsolda bulun. Bu örneğin bağımlılıklarını bir kez yüklemeniz gerekir:

```console
npm install
```

Ardından, komut istemi veya konsolu aracılığıyla uygulamayı çalıştırın:

```console
node . --op getUsers
```

Konsol çıkışında Azure AD dizininizde bulunan kullanıcıların listesini temsil eden bazı JSON parçaları ' nı görmeniz gerekir.

## <a name="about-the-code"></a>Kod hakkında

Aşağıda, örnek uygulamanın bazı önemli yönleri ele alınmıştır.

### <a name="msal-node"></a>MSAL düğümü

[Msal node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) , Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılan ve kullanıcılara oturum açmak için kullanılan bir kitaplıktır. Bu hızlı başlangıç, açıklandığı gibi, uygulama izinlerine göre belirteçleri (uygulamanın kendi kimliğini kullanarak) temsilci izinleri yerine ister. Bu durumda kullanılan kimlik doğrulama akışı, [OAuth 2,0 istemci kimlik bilgileri akışı](v2-oauth2-client-creds-grant-flow.md)olarak bilinir. Daemon uygulamalarıyla MSAL node 'u kullanma hakkında daha fazla bilgi için bkz. [Senaryo: Daemon uygulaması](scenario-daemon-overview.md).

 Aşağıdaki NPM komutunu çalıştırarak MSAL düğümünü yükleyebilirsiniz.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL başlatma

Şu kodu ekleyerek MSAL başvurusunu ekleyebilirsiniz:

```javascript
const msal = require('@azure/msal-node');
```

Sonra şu kodu kullanarak MSAL'yi başlatın:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Konum: |Description |
> |---------|---------|
> | `clientId` | **Uygulama (istemci) Kimliği**, Azure portalda kayıtlı uygulamadır. Bu değeri Azure portalda uygulamanın **Genel bakış** sayfasında bulabilirsiniz. |
> | `authority`    | Kimlik doğrulaması yapılacak kullanıcı için STS uç noktası. Genellikle `https://login.microsoftonline.com/{tenant}` {Tenant}, kiracınızın adı veya kiracı kimliğiniz olduğu genel bulut için.|
> | `clientSecret` | Azure portalında uygulama için istemci gizli dizisi oluşturulmuştur. |

Daha fazla bilgi için lütfen [başvuru belgelerine `ConfidentialClientApplication` ](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) bakın

### <a name="requesting-tokens"></a>Belirteç isteme

Uygulamanın kimliğini kullanarak bir belirteç istemek için `acquireTokenByClientCredential` yöntemi kullanın:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Konum:| Description |
> |---------|---------|
> | `tokenRequest` | İstenen kapsamları içerir. Gizli istemciler için, `{Application ID URI}/.default` istenen kapsamların Azure portalında ayarlanmış uygulama nesnesi içinde statik olarak tanımlanmış olduğunu göstermek için şuna benzer biçimi kullanmalıdır (Microsoft Graph için, `{Application ID URI}` işaret eder `https://graph.microsoft.com` ). Özel Web API 'Leri için, `{Application ID URI}` Azure portalının uygulama kaydında **bir API 'yi kullanıma** sunma bölümünde tanımlanmıştır. |
> | `tokenResponse` | Yanıt, istenen kapsamlar için bir erişim belirteci içeriyor. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

MSAL node ile DAEMON/konsol uygulaması geliştirme hakkında daha fazla bilgi edinmek için bkz. Öğretici:

> [!div class="nextstepaction"]
> [Web API 'Lerini çağıran Daemon uygulaması](tutorial-v2-nodejs-console.md)