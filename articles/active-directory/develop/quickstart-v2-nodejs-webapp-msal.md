---
title: 'Hızlı başlangıç: MSAL node ile bir Node Web uygulamasına kimlik doğrulaması ekleme | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, Node.js için Node.js bir Web uygulamasıyla ve Microsoft kimlik doğrulama kitaplığı (MSAL) ile kimlik doğrulaması uygulamayı öğreneceksiniz.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 72eb6e77cfbcae662181f642393085185514eed6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550957"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>Hızlı başlangıç: kimlik doğrulama kod akışını kullanarak Kullanıcı oturumu açma ve düğüm Web uygulamasında erişim belirteci edinme

Bu hızlı başlangıçta, bir Node.js Web uygulamasının yetkilendirme kodu akışını kullanarak kullanıcılara nasıl oturum açabildiğini gösteren bir kod örneği indirip çalıştırırsınız. Kod örneği ayrıca Microsoft Graph API çağrısı için bir erişim belirtecinin nasıl alınacağını gösterir. 

Örneğin bir çizim için [nasıl çalıştığını](#how-the-sample-works) görün.

Bu hızlı başlangıçta yetkilendirme kodu akışıyla Node.js (MSAL node) için Microsoft kimlik doğrulama kitaplığı kullanılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz bir Azure aboneliği oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) veya başka bir kod Düzenleyicisi

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Hızlı başlangıç uygulamanızı kaydedin ve indirin
>
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. Uygulamanız için bir **ad** girin. Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
> 1. **Desteklenen hesap türleri** altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
> 1. **Yeniden YÖNLENDIRME URI** değerini olarak ayarlayın `http://localhost:3000/redirect` .
> 1. **Kaydet**’i seçin. 
> 1. Uygulamaya **genel bakış** sayfasında, daha sonra kullanılmak üzere **uygulama (istemci) kimliği** değerini aklınızda edin.
> 1. **Yönet**' in altında **Sertifikalar & gizli**  >  **anahtar istemci parolası**' nı seçin.  Açıklamayı boş bırakın ve varsayılan süre sonu ' nu ve ardından **Ekle**' yi seçin.
> 1. Daha sonra kullanmak üzere **Istemci parolasının** **değerini** aklınızda yapın.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1. Adım: uygulamayı Azure portal yapılandırma
> Bu hızlı başlangıçta çalışması için kod örneği için, bir istemci parolası oluşturmanız ve bir yanıt URL 'SI eklemeniz gerekir **http://localhost:3000/redirect** .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişikliği benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-windows-desktop/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-project"></a>2. Adım: Projeyi indirme

> [!div renderon="docs"]
> Node.js kullanarak projeyi bir Web sunucusuyla çalıştırmak için, [temel proje dosyalarını indirin](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> Node.js kullanarak projeyi bir Web sunucusu ile çalıştırma

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>3. Adım: düğüm uygulamanızı yapılandırma
>
> Projeyi ayıklayın ve *MS-Identity-node-Main* klasörünü açın ve *index.js* dosyasını açın.
> `clientID` **Uygulamasını uygulama (ISTEMCI) kimliğiyle** ayarlayın.
> Öğesini `clientSecret` **Istemci parolasının** **değeri** ile ayarlayın.
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> `config`Bölümündeki değerleri burada açıklandığı gibi değiştirin:
>
> - `Enter_the_Application_Id_Here` , kaydettiğiniz uygulamanın **uygulama (istemci) kimliğidir** .
>
>    **Uygulama (istemci) kimliğinin** değerini bulmak için, Azure Portal uygulama kaydının **genel bakış** sayfasına gidin.
> - `Enter_the_Client_Secret_Here`, kaydettiğiniz uygulamanın **istemci gizli anahtarı** **değeridir** .
>
>    Yeni bir **istemci parolası** almak veya oluşturmak Için, **yönet** altında **Sertifikalar & parolaları**' nı seçin.
>
> Varsayılan `authority` değer, ana (genel) Azure bulutunu temsil eder:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. Adım: uygulamanız yapılandırıldı ve çalıştırılmaya hazırlanıyor
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>4. Adım: projeyi çalıştırma

Node.js kullanarak projeyi çalıştırın:

1. Sunucuyu başlatmak için, proje dizini içinden aşağıdaki komutları çalıştırın:
    ```console
    npm install
    npm start
    ```
1. `http://localhost:3000/` adresine gidin.

1. Oturum açma işlemini başlatmak için **oturum aç '** ı seçin.

    İlk kez oturum açtığınızda, uygulamanın profilinize erişmesine izin vermek için onayınızı sağlamanız ve oturumunuzu açmanız istenir. Başarıyla oturum açtıktan sonra komut satırında bir günlük iletisi görürsünüz.

## <a name="more-information"></a>Daha fazla bilgi

### <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı

Örnek, çalıştırıldığında, localhost üzerinde bir Web sunucusu barındırır, bağlantı noktası 3000. Bir Web tarayıcısı bu siteye eriştiğinde, örnek kullanıcıyı hemen bir Microsoft kimlik doğrulama sayfasına yönlendirir. Bu nedenle, örnek herhangi bir *HTML* veya görüntü öğesi içermiyor. Kimlik doğrulama başarısı "Tamam" iletisini görüntüler.

### <a name="msal-node"></a>MSAL düğümü

MSAL düğüm kitaplığı, kullanıcıları imzalar ve Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılan belirteçleri ister. Node.js paket yöneticisini (NPM) kullanarak en son sürümü indirebilirsiniz:

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Var olan bir Web uygulamasına kimlik doğrulama ekleme-GitHub kod örneği >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
