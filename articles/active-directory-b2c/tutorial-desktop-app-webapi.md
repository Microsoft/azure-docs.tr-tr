---
title: "Öğretici: masaüstü uygulamasından Node.js Web API 'sine erişim Izni verme"
description: Node.js Web API 'sini korumak ve bir .NET masaüstü uygulamasından çağırmak için Active Directory B2C kullanma hakkında öğretici.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 12951f25feb6f3710b8d35fbc682caeeb480e788
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555610"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Öğretici - Azure Active Directory B2C kullanarak bir masaüstü uygulamasından Node.js web API'sine erişim izni verme

Bu öğreticide, Azure AD B2C tarafından da korunan bir Windows Presentation Foundation (WPF) masaüstü uygulamasından Azure Active Directory B2C (Azure AD B2C) tarafından korunan Node.js Web API 'sinin nasıl çağrılacağını gösterir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

Öğreticideki adımları ve önkoşulları doldurun [: yerel bir masaüstü istemcisinde kullanıcıların kimliğini doğrulama](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Web API'si uygulaması ekleme

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Kapsamları yapılandırma

Kapsamlar korumalı kaynaklara erişimi yönetmenin bir yolunu sunar. Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin, bazı kullanıcılar hem okuma hem de yazma erişimine sahipken diğer kullanıcıların salt okunur izinleri olabilir. Bu öğreticide web API’si için okuma ve yazma izinlerini tanımlayacaksınız.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

 `demo.read` Masaüstü uygulamasını yapılandırırken daha sonraki bir adımda kullanılacak kapsamın kapsamları altındaki değeri kaydedin. Tam kapsam değeri öğesine benzerdir `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>İzinleri verme

Yerel bir istemci uygulamasından korumalı bir Web API 'SI çağırmak için, kayıtlı yerel istemci uygulama izinlerini Azure AD B2C kaydettiğiniz Web API 'sine vermeniz gerekir.

Önkoşul öğreticisinde, *nativeapp1* adlı bir yerel istemci uygulaması kaydettiniz. Aşağıdaki adımlarda, yerel uygulama kaydı, önceki bölümde *webapi1* için kullanıma sunulacak API kapsamları ile yapılandırılır. Bu, masaüstü uygulamasının, Web API 'sinin kaynakları doğrulamak ve kaynaklarına kapsamlı erişimi sağlamak için kullanabileceği Azure AD B2C bir erişim belirteci almasına olanak tanır. Hem masaüstü uygulamasını hem de Web API kodu örneklerini öğreticide daha sonra yapılandırıp çalıştırırsınız.

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. **Uygulama kayıtları**' yi seçin ve ardından API 'ye erişmesi gereken yerel istemci uygulamasını seçin. Örneğin, *nativeapp1*.
1. **Yönet** altında **API izinleri**' ni seçin.
1. **Yapılandırılan izinler** altında **izin Ekle**' yi seçin.
1. **API 'Lerim** sekmesini seçin.
1. Yerel istemci uygulamasına erişim verilmesi gereken API 'yi seçin. Örneğin, *webapi1*.
1. **İzin** altında **demo**' i genişletin ve daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Izin Ekle**' yi seçin. Yönlendirildiğinden, bir sonraki adıma geçmeden önce birkaç dakika bekleyin.
1. **Yönetici onayı ver ' i (kiracı adınız)** seçin.
1. Şu anda oturum açmış olan yönetici hesabınızı seçin veya Azure AD B2C kiracınızda, en azından *bulut uygulama Yöneticisi* rolüne atanan bir hesapla oturum açın.
1. **Kabul Et**’i seçin.
1. **Yenile**' yi seçin ve ardından "verilen..." öğesini doğrulayın Her iki kapsam için **durum** altında görünür. İzinlerin yayılması birkaç dakika sürebilir.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. Uygulamalar ' ı **(eski)** seçin ve ardından *nativeapp1*' ı seçin.
1. **API erişimi**' ni seçin ve ardından **Ekle**' yi seçin.
1. **API Seç** açılan menüsünde *webapi1*' yi seçin.
1. **Kapsamları Seç** açılan menüsünde, daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Tamam**’ı seçin.

* * *

Kullanıcılar WPF masaüstü uygulamasını kullanmak için Azure AD B2C ile kimlik doğrulaması yapar. Masaüstü uygulaması, korumalı web API’sine erişmek için Azure AD B2C’den bir yetkilendirme izni alır.

## <a name="configure-the-samples"></a>Örnekleri yapılandırın

Web API 'SI kayıtlı olduğuna ve kapsamlarınızın ve izinlerinizin yapılandırıldığına göre, masaüstü uygulamasını ve Web API örneklerini Azure AD B2C kiracınızı kullanacak şekilde yapılandırırsınız.

### <a name="update-the-desktop-application"></a>Masaüstü uygulamasını güncelleştirme

Bu makalenin bir önkoşulu içinde, Azure AD B2C kiracınızdaki bir Kullanıcı akışı ile oturum açmayı etkinleştirmek için bir [WPF Masaüstü uygulamasını](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) değiştirdiniz. Bu bölümde, *webapi1* daha önce kaydettiğiniz Web API 'sine başvurmak için aynı uygulamayı güncelleşolursunuz.

1. Visual Studio 'da **Active-Directory-B2C-WPF** çözümünü ( `active-directory-b2c-wpf.sln` ) açın.
1. **Active-Directory-B2C-WPF** projesinde *app. xaml. cs* dosyasını açın ve aşağıdaki değişken tanımlarını bulun.
    1. Değişkenin değerini, `ApiScopes` **demo. Read** kapsamını tanımladığınızda daha önce kaydettiğiniz değerle değiştirin.
    1. Değişkenin değerini, `ApiEndpoint` kiracınızdaki Web API 'sini (örneğin, *webapi1*) kaydettiğinizde daha önce KAYDETTIĞINIZ **yeniden yönlendirme URI** 'siyle değiştirin.

    Aşağıda bir örnek verilmiştir:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Node.js API örneğini edinme ve güncelleştirme

Sonra, GitHub 'dan Node.js Web API kodu örneğini alın ve Azure AD B2C kiracınızda kaydettiğiniz Web API 'sini kullanmak üzere yapılandırın.

GitHub’dan [zip dosyasını indirin](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) veya örnek web uygulamasını kopyalayın.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Node.js web API’si örneği API’ye yapılan çağrıları korumak için Azure AD B2C’yi etkinleştirmek üzere Passport.js kitaplığını kullanır.

1. `index.js` dosyasını açın.
1. Bu değişken tanımlarını aşağıdaki değerlerle güncelleştirin. `<web-API-application-ID>`Daha önce kaydettiğiniz Web API 'sinin (*Webapi1*) **uygulama (istemci) kimliğine** geçin. `<your-b2c-tenant>`Azure AD B2C kiracınızın adına geçin.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. API 'YI yerel olarak çalıştırdığınız için, yol içindeki yolu, `/` tanıtım uygulamasının konumu yerıne Get yöntemine güncelleştirin `/hello` :

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Örnekleri çalıştırma

### <a name="run-the-nodejs-web-api"></a>Node.js Web API 'sini çalıştırma

1. Bir Node.js komut istemi başlatın.
2. Node.js örneğini içeren dizine değiştirin. Örneğin `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Aşağıdaki komutları çalıştırın:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Masaüstü uygulamasını çalıştırma

1. Visual Studio 'da **Active-Directory-B2C-WPF** çözümünü açın.
2. Masaüstü uygulamasını çalıştırmak için **F5**'e basın.
3. [Bir masaüstü uygulamasında Azure Active Directory B2C ile kullanıcılar için kimlik doğrulaması gerçekleştirme öğreticisinde](tutorial-desktop-app.md) kullanılan e-posta adresi ve parolayı kullanarak oturum açın.
4. **API çağrısı** düğmesini seçin.

Masaüstü uygulaması yerel olarak çalışan Web API 'sine bir istek yapar ve geçerli bir erişim belirtecinin doğrulanması sırasında, oturum açan kullanıcının görünen adını gösterir.

![WPF Masaüstü uygulamasının üst bölmesinde gösterilen görünen ad](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Azure AD B2C tarafından korunan masaüstü uygulamanız, Azure AD B2C tarafından da korunan yerel olarak çalışan Web API 'sini çağırıyor.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği güncelleştirme

> [!div class="nextstepaction"]
> [Azure Active Directory B2C ' deki uygulamalarınıza kimlik sağlayıcıları ekleyin](add-identity-provider.md)