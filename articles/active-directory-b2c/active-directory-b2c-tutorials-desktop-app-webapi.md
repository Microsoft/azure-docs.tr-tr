---
title: Öğretici-bir masaüstü uygulamasından Node. js web API 'sine erişim Izni verme-Azure Active Directory B2C | Microsoft Docs
description: Bir Node.js web API’sini korumak ve bir .NET masaüstü uygulamasından çağırmak için Active Directory B2C kullanmaya yönelik öğretici.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0cf9a0a13a1c16f5be6d82528849e6e2cc3d466d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641834"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Öğretici - Azure Active Directory B2C kullanarak bir masaüstü uygulamasından Node.js web API'sine erişim izni verme

Bu öğreticide, ayrıca Azure AD B2C tarafından korunan bir Windows Presentation Foundation (WPF) masaüstü uygulamasından Azure Active Directory B2C (Azure AD B2C) tarafından korunan bir Node. js web API 'sinin nasıl çağrılacağını gösterir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği güncelleştirme

## <a name="prerequisites"></a>Ön koşullar

Öğreticideki adımları ve önkoşulları doldurun [: yerel bir masaüstü istemcisinde kullanıcıların kimliğini doğrulama](active-directory-b2c-tutorials-desktop-app.md).

## <a name="add-a-web-api-application"></a>Web API'si uygulaması ekleme

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Kapsamları yapılandırma

Kapsamlar, korumalı kaynaklara erişimi yönetmek için bir yol sağlar. Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin, bazı kullanıcılar hem okuma hem de yazma erişimine sahipken diğer kullanıcıların salt okunur izinleri olabilir. Bu öğreticide web API’si için okuma ve yazma izinlerini tanımlayacaksınız.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Masaüstü uygulamasını yapılandırırken daha sonraki bir adımda kullanmak üzere `demo.read` kapsamı için **kapsamlar** altındaki değeri kaydedin. Tam kapsam değeri `https://contosob2c.onmicrosoft.com/api/demo.read`benzerdir.

## <a name="grant-permissions"></a>İzinleri verme

Yerel bir istemci uygulamasından korumalı bir Web API 'SI çağırmak için, kayıtlı yerel istemci uygulama izinlerini Azure AD B2C kaydettiğiniz Web API 'sine vermeniz gerekir.

Önkoşul öğreticisinde, *nativeapp1*adlı bir yerel istemci uygulaması kaydettiniz. Aşağıdaki adımlarda, yerel uygulama kaydı, önceki bölümde *webapi1* için kullanıma sunulacak API kapsamları ile yapılandırılır. Bu, masaüstü uygulamasının, Web API 'sinin kaynakları doğrulamak ve kaynaklarına kapsamlı erişimi sağlamak için kullanabileceği Azure AD B2C bir erişim belirteci almasına olanak tanır. Hem masaüstü uygulamasını hem de Web API kodu örneklerini öğreticide daha sonra yapılandırıp çalıştırırsınız.

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. **Uygulamalar**' ı seçin ve ardından *nativeapp1*' ı seçin.
1. **API erişimi**' ni seçin ve ardından **Ekle**' yi seçin.
1. **API Seç** açılan menüsünde *webapi1*' yi seçin.
1. **Kapsamları Seç** açılan menüsünde, daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Tamam**’ı seçin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından API 'ye erişmesi gereken yerel istemci uygulamasını seçin. Örneğin, *nativeapp1*.
1. **Yönet**altında **API izinleri**' ni seçin.
1. **Yapılandırılan izinler**altında **izin Ekle**' yi seçin.
1. **API 'Lerim** sekmesini seçin.
1. Yerel istemci uygulamasına erişim verilmesi gereken API 'yi seçin. Örneğin, *webapi1*.
1. **İzin**altında **demo**' i genişletin ve daha önce tanımladığınız kapsamları seçin. Örneğin, *demo. Read* ve *demo. Write*.
1. **Izin Ekle**' yi seçin. Yönlendirildiğinden, bir sonraki adıma geçmeden önce birkaç dakika bekleyin.
1. **Yönetici onayı ver ' i (kiracı adınız)** seçin.
1. Şu anda oturum açmış olan yönetici hesabınızı seçin veya Azure AD B2C kiracınızda, en azından *bulut uygulama Yöneticisi* rolüne atanan bir hesapla oturum açın.
1. **Kabul Et**’i seçin.
1. **Yenile**' yi seçin ve ardından "verilen..." öğesini doğrulayın Her iki kapsam için **durum** altında görünür. İzinlerin yayılması birkaç dakika sürebilir.

* * *

Bir kullanıcı WPF Masaüstü uygulamasını kullanmak için Azure AD B2C kimliğini doğrular. Masaüstü uygulaması, korunan Web API 'sine erişmek için Azure AD B2C bir yetkilendirme izni alır.

## <a name="configure-the-samples"></a>Örnekleri yapılandırın

Web API 'SI kayıtlı olduğuna ve kapsamlarınızın ve izinlerinizin yapılandırıldığına göre, masaüstü uygulamasını ve Web API örneklerini Azure AD B2C kiracınızı kullanacak şekilde yapılandırırsınız.

### <a name="update-the-desktop-application"></a>Masaüstü uygulamasını güncelleştirme

Bu makalenin bir önkoşulu içinde, Azure AD B2C kiracınızdaki bir Kullanıcı akışı ile oturum açmayı etkinleştirmek için bir [WPF Masaüstü uygulamasını](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) değiştirdiniz. Bu bölümde, *webapi1*daha önce kaydettiğiniz Web API 'sine başvurmak için aynı uygulamayı güncelleşolursunuz.

1. Visual Studio 'da **Active-Directory-B2C-WPF** çözümünü (`active-directory-b2c-wpf.sln`) açın.
1. **Active-Directory-B2C-WPF** projesinde, *app.xaml.cs* dosyasını açın ve aşağıdaki değişken tanımlarını bulun.
    1. `ApiScopes` değişkeninin değerini, **demo. Read** kapsamını tanımladığınızda daha önce kaydettiğiniz değerle değiştirin.
    1. `ApiEndpoint` değişkeninin değerini, kiracınızda Web API 'sini (örneğin, *webapi1*) kaydettiğinizde daha önce kaydettiğiniz **yeniden yönlendirme URI** 'siyle değiştirin.

    Bir örneği aşağıda verilmiştir:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Node. js API örneğini edinme ve güncelleştirme

Daha sonra, GitHub 'dan Node. js web API kodu örneğini alın ve Azure AD B2C kiracınızda kayıtlı olan Web API 'sini kullanmak üzere yapılandırın.

GitHub’dan [zip dosyasını indirin](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) veya örnek web uygulamasını kopyalayın.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Node.js web API’si örneği API’ye yapılan çağrıları korumak için Azure AD B2C’yi etkinleştirmek üzere Passport.js kitaplığını kullanır.

1. `index.js` dosyasını açın.
1. Bu değişken tanımlarını aşağıdaki değerlerle güncelleştirin. Daha önce kaydettiğiniz Web API 'sinin **uygulama (istemci) kimliğine** `<web-API-application-ID>` değiştirin (*webapi1*). `<your-b2c-tenant>` Azure AD B2C kiracınızın adıyla değiştirin.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. API 'YI yerel olarak çalıştırdığınız için, GET yöntemi yolundaki yolu tanıtım uygulamasının `/hello`konumu yerine `/` güncelleştirin:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>Örnekleri çalıştırma

### <a name="run-the-nodejs-web-api"></a>Node. js web API 'sini çalıştırma

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
3. [Bir masaüstü uygulamasında Azure Active Directory B2C ile kullanıcılar için kimlik doğrulaması gerçekleştirme öğreticisinde](active-directory-b2c-tutorials-desktop-app.md) kullanılan e-posta adresi ve parolayı kullanarak oturum açın.
4. **API çağrısı** düğmesini seçin.

Masaüstü uygulaması yerel olarak çalışan Web API 'sine bir istek yapar ve geçerli bir erişim belirtecinin doğrulanması sırasında, oturum açan kullanıcının görünen adını gösterir.

![WPF Masaüstü uygulamasının üst bölmesinde gösterilen görünen ad](media/active-directory-b2c-tutorials-desktop-app-webapi/desktop-app-01-post-api-call.png)

Azure AD B2C tarafından korunan masaüstü uygulamanız, Azure AD B2C tarafından da korunan yerel olarak çalışan Web API 'sini çağırıyor.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği güncelleştirme

> [!div class="nextstepaction"]
> [Öğretici: Azure Active Directory B2C ' de uygulamalarınıza kimlik sağlayıcıları ekleme](tutorial-add-identity-providers.md)
