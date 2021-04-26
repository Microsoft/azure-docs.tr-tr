---
title: 'Hızlı başlangıç: Node.js Web uygulamasına kullanıcı oturumu ekleme | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, OpenID Connect kullanarak Node.js bir Web uygulamasında kimlik doğrulamanın nasıl uygulanacağını öğreneceksiniz.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: f7f14b91dc69eeba4ac06f6608f6151634dc38d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103507"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Hızlı başlangıç: Node.js Web uygulamasına OpenID Connect ile oturum açma ekleme

Bu hızlı başlangıçta, Express ile Node.js kullanılarak oluşturulmuş bir Web uygulamasında OpenID Connect kimlik doğrulamasının nasıl ayarlanacağını gösteren bir kod örneği indirip çalıştırırsınız. Örnek, herhangi bir platformda çalışmak üzere tasarlanmıştır.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Uygulamanızı kaydetme

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin (örneğin,) `MyWebApp` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Desteklenen hesap türleri** bölümünde, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında (ör. Skype, Xbox, Outlook.com) hesaplar**' ı seçin.

    Birden fazla yeniden yönlendirme URI 'si varsa, bunları daha sonra uygulama başarıyla oluşturulduktan sonra **kimlik doğrulama** sekmesinden ekleyin.

1. Uygulamayı oluşturmak için **Kaydet** ' i seçin.
1. Uygulamanın **genel bakış** sayfasında, **uygulama (istemci) kimlik** değerini bulun ve daha sonra için kaydedin. Uygulamayı bu projede daha sonra yapılandırmak için bu değere ihtiyacınız olacak.
1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Platform Web ekle**' yi seçin  >  . 
1. **Yeniden yönlendirme URI 'leri** bölümünde, girin `http://localhost:3000/auth/openid/return` .
1. **Ön kanal oturum kapatma URL 'si** girin `https://localhost:3000` .
1. **Örtük verme ve karma akışlar** bölümünde **Kimlik belirteçleri** ' ni seçin. Bu örnek, kullanıcının oturum açması için [örtük verme akışının](./v2-oauth2-implicit-grant-flow.md) etkinleştirilmesini gerektirir.
1. **Yapılandır**'ı seçin.
1. **Yönet**' in altında **Sertifikalar & gizli**  >  **anahtar istemci parolası**' nı seçin.
1. Bir anahtar açıklaması girin (örneğin, uygulama gizli anahtarı).
1. **1 yılda, 2 yıl içinde** bir anahtar süresi seçin veya **hiçbir zaman sona ermez**.
1. **Add (Ekle)** seçeneğini belirleyin. Anahtar değeri görüntülenecektir. Anahtar değerini kopyalayın ve daha sonra kullanmak üzere güvenli bir konuma kaydedin.


## <a name="download-the-sample-application-and-modules"></a>Örnek uygulamayı ve modülleri indirin

Sonra, örnek depoyu kopyalayın ve NPM modüllerini yükler.

Kabuk veya komut satırınızdan:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

veya

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Proje kök dizininden komutunu çalıştırın:

`$ npm install`

## <a name="configure-the-application"></a>Uygulamayı yapılandırma

config.js içindeki parametreleri `exports.creds` belirtildiği gibi sağlayın.

* `<tenant_name>`' Da `exports.identityMetadata` Azure AD kiracı adı \* . onmicrosoft.com biçiminde güncelleştirin.
* Uygulama `exports.clientID` kaydından belirtilen uygulama kimliğiyle güncelleştirin.
* `exports.clientSecret`Uygulama kaydınızdan belirtilen uygulama gizli anahtarı ile güncelleştirin.
* `exports.redirectUrl`Uygulama kaydından belirtilen yeniden YÖNLENDIRME URI 'siyle güncelleştirin.

**Üretim uygulamaları için isteğe bağlı yapılandırma:**

* `exports.destroySessionUrl`Farklı bir kullanmak istiyorsanız config.js güncelleştirin `post_logout_redirect_uri` .

* `exports.useMongoDBSessionStore` [MongoDB](https://www.mongodb.com) veya diğer [uyumlu oturum depolarını](https://github.com/expressjs/session#compatible-session-stores)kullanmak istiyorsanız, config.js true olarak ayarlayın.
Bu örnekteki varsayılan oturum depolama alanı `express-session` . Varsayılan oturum deposu üretime uygun değil.

* `exports.databaseUri`MongoDB oturum deposunu ve farklı bir veritabanı URI 'sini kullanmak istiyorsanız güncelleştirin.

* Güncelleştirin `exports.mongoDBSessionMaxAge` . Burada, mongoDB 'de bir oturumu ne kadar süreyle saklamak istediğinizi belirtebilirsiniz. Birim saniye (sn).

## <a name="build-and-run-the-application"></a>Uygulamayı derleme ve çalıştırma

MongoDB hizmetini başlatın. Bu uygulamada mongoDB oturum deposu kullanıyorsanız, [mongoDB 'yi yüklemeniz](http://www.mongodb.org/) ve önce hizmeti başlatmanız gerekir. Varsayılan oturum deposunu kullanıyorsanız, bu adımı atlayabilirsiniz.

Komut satırınızdan aşağıdaki komutu kullanarak uygulamayı çalıştırın.

```
$ node app.js
```

**Sunucu çıkışının anlaşılması zor mi?:** `bunyan` Bu örnekte günlüğe kaydetmek için kullanıyoruz. Ayrıca, Bunyan ' i yüklemediğiniz ve sunucuyu yukarıdaki gibi çalıştırarak, Bunyan ikilisini kullanarak, konsolu sizin için çok anlamlı hale getirir:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>İşiniz bitti!

Sunucusunda başarıyla çalışan bir sunucu olacak `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Microsoft Identity platformunun desteklediği Web uygulaması senaryosu hakkında daha fazla bilgi edinin:
> [!div class="nextstepaction"]
> [Kullanıcılar senaryosunda oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md)