---
title: Microsoft Identity platform OıDC Node. js web uygulaması hızlı başlangıç | Mavisi
description: OpenID Connect kullanarak bir Node. js web uygulamasında kimlik doğrulamanın nasıl uygulanacağını öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ac3344fe16d24ae116a5fde289421998f11f18
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73178049"
---
# <a name="quickstart-add-sign-in-using-oidc-to-a-nodejs-web-app"></a>Hızlı başlangıç: node. js web uygulamasına OıDC kullanarak oturum açma ekleme

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Bu hızlı başlangıçta, Express ile Node. js kullanılarak oluşturulan bir Web uygulamasında OpenID Connect kimlik doğrulamasını ayarlamayı öğreneceksiniz. Örnek, herhangi bir platformda çalışmak üzere tasarlanmıştır.

## <a name="prerequisites"></a>Önkoşullar

Bu örneği çalıştırmak için şunlar gerekir:

* http://nodejs.org/ 'dan Node. js ' ye Install

* [Microsoft hesabı](https://www.outlook.com) veya [Office 365 Geliştirici Programı](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Uygulamanızı kaydetme 
1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.
1. Hesabınız birden çok Azure AD kiracısında varsa:
    - Sayfanın sağ üst köşesindeki menüden profilinizi seçin ve ardından dizin ' i **değiştirin**.
    - Uygulamanızı oluşturmak istediğiniz Azure AD kiracısı için oturumunuzu değiştirin.

1. Uygulamanızı kaydetmek için [Azure Active Directory > uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) gidin.

1. **Yeni kayıt** seçeneğini belirleyin.

1. **Bir uygulamayı kaydet** sayfası göründüğünde, uygulamanızın kayıt bilgilerini girin:
    - **Ad** bölümünde, uygulamanın kullanıcılarına gösterilecek anlamlı bir ad girin. Örneğin: MyWebApp
    - **Desteklenen hesap türleri** bölümünde, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında (ör. Skype, Xbox, Outlook.com) hesaplar**' ı seçin.

    Birden fazla yeniden yönlendirme URI 'si varsa, bunları daha sonra uygulama başarıyla oluşturulduktan sonra **kimlik doğrulama** sekmesinden eklemeniz gerekir.

1. Uygulamayı oluşturmak için **Kaydet** ' i seçin.

1. Uygulamanın **genel bakış** sayfasında, **uygulama (istemci) kimlik** değerini bulun ve daha sonra için kaydedin. Uygulamayı bu projede daha sonra yapılandırmak için bu değere ihtiyacınız olacak.

1. Uygulama sayfa listesinde **Kimlik doğrulaması**'nı seçin.
    - **Yeniden yönlendirme URI 'leri** bölümünde, açılan kutudan **Web** ' i seçin ve aşağıdaki yeniden yönlendirme urı 'sini girin: `http://localhost:3000/auth/openid/return`
    - **Gelişmiş ayarlar** bölümünde, **oturum kapatma URL 'sini** `http://localhost:3000`olarak ayarlayın.
    - **Gelişmiş ayarlar > örtük verme** bölümünde, **kimlik belirteçlerini** denetleyin, bu örnek, kullanıcının oturum açması için [örtük verme akışının](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) etkinleştirilmesini gerektirir.

1. **Kaydet**’i seçin.

1. **Sertifikalar & gizlilikler** sayfasında, **istemci gizli** dizileri bölümünde **yeni istemci parolası**' nı seçin.
    - Bir anahtar açıklaması girin (örneğin, uygulama gizli anahtarı).
    - **1 yılda, 2 yıl içinde** bir anahtar süresi seçin veya **hiçbir zaman sona ermez**.
    - **Ekle** düğmesine tıkladığınızda, anahtar değeri görüntülenecektir. Anahtar değerini kopyalayın ve güvenli bir konuma kaydedin.

    Uygulamayı yapılandırmak için bu anahtara daha sonra ihtiyacınız olacak. Bu anahtar değeri bir daha görüntülenmez veya başka yollarla alınabilir, bu nedenle Azure portal görünür hale geldiğinde onu kaydedin.

## <a name="download-the-sample-application-and-modules"></a>Örnek uygulamayı ve modülleri indirin

Sonra, örnek depoyu kopyalayın ve NPM modüllerini yükler.

Kabuk veya komut satırınızdan:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

or

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Proje kök dizininden komutunu çalıştırın:

`$ npm install`  

## <a name="configure-the-application"></a>Uygulamayı yapılandırma

Parametreleri `exports.creds` ' de config. js ' de belirtildiği gibi sağlayın.

* `exports.identityMetadata` `<tenant_name>` \*. onmicrosoft.com biçiminin Azure AD kiracı adıyla güncelleştirin.
* `exports.clientID`, uygulama kaydından belirtilen uygulama KIMLIĞIYLE güncelleştirin.
* Uygulama kaydından belirtilen uygulama gizli anahtarı ile `exports.clientSecret` güncelleştirin.
* Uygulama kaydından belirtilen yeniden yönlendirme URI 'siyle `exports.redirectUrl` güncelleştirin.

**Üretim uygulamaları için isteğe bağlı yapılandırma:**

* Farklı bir `post_logout_redirect_uri`kullanmak istiyorsanız config. js ' de `exports.destroySessionUrl` güncelleştirin.

* [MongoDB](https://www.mongodb.com) veya diğer [uyumlu oturum depolarını](https://github.com/expressjs/session#compatible-session-stores)kullanmak istiyorsanız config. js içinde `exports.useMongoDBSessionStore` değerini true olarak ayarlayın.
Bu örnekteki varsayılan oturum deposu `express-session`. Varsayılan oturum deposu üretime uygun değil.

* MongoDB oturum deposunu ve farklı bir veritabanı URI 'sini kullanmak istiyorsanız `exports.databaseUri`güncelleştirin.

* Güncelleştirme `exports.mongoDBSessionMaxAge`. Burada, mongoDB 'de bir oturumu ne kadar süreyle saklamak istediğinizi belirtebilirsiniz. Birim saniye (sn).

## <a name="build-and-run-the-application"></a>Uygulamayı derleme ve çalıştırma

MongoDB hizmetini başlatın. Bu uygulamada mongoDB oturum deposu kullanıyorsanız, [mongoDB 'yi yüklemeniz](http://www.mongodb.org/) ve önce hizmeti başlatmanız gerekir. Varsayılan oturum deposunu kullanıyorsanız, bu adımı atlayabilirsiniz.

Komut satırınızdan aşağıdaki komutu kullanarak uygulamayı çalıştırın.

```
$ node app.js
```

**Sunucu çıkışının anlaşılması zor mi?:** Bu örnekte günlüğe kaydetmek için `bunyan` kullanırız. Ayrıca, Bunyan ' i yüklemediğiniz ve sunucuyu yukarıdaki gibi çalıştırarak, Bunyan ikilisini kullanarak, konsolu sizin için çok anlamlı hale getirir:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>İşiniz bitti!

`http://localhost:3000`sunucuda başarıyla çalışan bir sunucu olacak.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Microsoft Identity platformunun desteklediği Web uygulaması senaryosu hakkında daha fazla bilgi edinin:
> [!div class="nextstepaction"]
> [Kullanıcılar senaryosunda oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md)
