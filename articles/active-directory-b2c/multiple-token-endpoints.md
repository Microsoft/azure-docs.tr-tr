---
title: OWıN tabanlı Web API 'Lerini b2clogin.com-Azure Active Directory B2C 'e geçirme
description: Uygulamalarınızı b2clogin.com 'e geçirirken birden çok belirteç verenler tarafından verilen belirteçleri desteklemek için bir .NET Web API 'sini nasıl etkinleştirebileceğinizi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8a6b4f90fe3f1e60341cc59e7d81870c82e843b
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533771"
---
# <a name="migrate-an-owin-based-web-api-to-b2clogincom"></a>OWıN tabanlı bir Web API 'sini b2clogin.com 'e geçirme

Bu makalede, [.net Için açık Web arabirimi (OWıN)](http://owin.org/)uygulayan Web API 'lerinde birden çok belirteç verenler desteğini etkinleştirme tekniği açıklanmaktadır. Birden çok belirteç uç noktasını desteklemek, Azure Active Directory B2C (Azure AD B2C) API 'Lerini ve uygulamalarını *login.microsoftonline.com* ' den *b2clogin.com*' ye geçirirken yararlıdır.

Hem b2clogin.com hem de login.microsoftonline.com tarafından verilen belirteçleri kabul etmek için API 'nize destek ekleyerek, API 'den login.microsoftonline.com tarafından verilen belirteçler desteğini kaldırmadan önce Web uygulamalarınızı aşamalı bir şekilde geçirebilirsiniz.

Aşağıdaki bölümler, [Microsoft OWIN][katana] ara yazılım bileşenleri (Katana) kullanan BIR Web API 'sinde birden çok veren 'in nasıl etkinleşeceği hakkında bir örnek sunar. Kod örnekleri Microsoft OWıN ara yazılımı 'na özgü olsa da, genel teknik diğer OWIN kitaplıkları için geçerli olmalıdır.

> [!NOTE]
> Bu makale, şu anda dağıtılmış olan API 'ler ve uygulama `login.microsoftonline.com` ve önerilen `b2clogin.com` uç noktaya geçirmek isteyen uygulamalarla Azure AD B2C müşterilere yöneliktir. Yeni bir uygulama ayarlıyorsanız, [b2clogin.com](b2clogin.md) kullanın.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki adımlarla devam etmeden önce aşağıdaki Azure AD B2C kaynaklara sahip olmanız gerekir:

* Kiracınızda oluşturulan [Kullanıcı akışları](tutorial-create-user-flows.md) veya [özel ilkeler](active-directory-b2c-get-started-custom.md)

## <a name="get-token-issuer-endpoints"></a>Belirteç Verenin uç noktalarını al

İlk olarak, API 'niz içinde desteklemek istediğiniz her veren için belirteç verenin uç noktası URI 'Lerini almanız gerekir. Azure AD B2C kiracınız tarafından desteklenen *b2clogin.com* ve *login.microsoftonline.com* uç noktalarını almak için Azure Portal aşağıdaki yordamı kullanın.

Mevcut Kullanıcı akışlarınızdan birini seçerek başlayın:

1. [Azure portal](https://portal.azure.com) Azure AD B2C kiracınıza gidin
1. **İlkeler**altında **Kullanıcı akışları ' nı (ilkeler)** seçin
1. Var olan bir ilkeyi seçin (örneğin *B2C_1_signupsignin1*) ve ardından **Kullanıcı akışını Çalıştır** ' ı seçin.
1. Sayfanın üst kısmındaki **Kullanıcı akış başlığını Çalıştır** bölümünde, bu kullanıcı akışı Için OpenID Connect bulma uç noktasına gitmek üzere köprüyü seçin.

    ![Azure portal Şimdi Çalıştır sayfasında iyi bilinen URI Köprüsü](media/multi-token-endpoints/portal-01-policy-link.png)

1. Tarayıcınızda açılan sayfada `issuer` değeri kaydedin, örneğin:

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

1. **Etki alanı seç** açılan öğesini kullanarak diğer etki alanını seçin, sonra önceki iki adımı bir kez daha gerçekleştirin ve `issuer` değerini kaydedin.

Artık şuna benzer iki Uri kayıtlı olmalıdır:

```
https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/
```

### <a name="custom-policies"></a>Özel ilkeler

Kullanıcı akışları yerine özel ilkeleriniz varsa, veren URI 'Leri almak için benzer bir işlem kullanabilirsiniz.

1. Azure AD B2C kiracınıza gidin
1. **Kimlik deneyimi çerçevesini** seçin
1. Bağlı olan taraf ilkelerinizin birini seçin, örneğin, *B2C_1A_signup_signin*
1. Bir etki alanı seçmek için **etki alanı seç** açılan listesini kullanın, örneğin *yourtenant.b2clogin.com*
1. **OpenID Connect bulma uç noktası** altında görünen köprüyü seçin
1. `issuer` Değeri kaydet
1. Diğer etki alanı için 4-6 adımlarını gerçekleştirin, örneğin *login.microsoftonline.com*

## <a name="get-the-sample-code"></a>Örnek kodunu alma

Her iki Token Endpoint URI 'si olduğuna göre, her iki uç noktanın de geçerli verenler olduğunu belirtmek için kodunuzu güncelleştirmeniz gerekir. Örnek olarak, örnek uygulamayı indirin veya kopyalayın, ardından örneği her iki bitiş noktasını da geçerli verenler olarak destekleyecek şekilde güncelleştirin.

Arşivi indirin: [Active-Directory-B2C-DotNet-WebApp-and-WebApi-Master. zip][sample-archive]

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

## <a name="enable-multiple-issuers-in-web-api"></a>Web API 'de birden çok veren etkinleştirme

Bu bölümde, her iki belirteç verenin bitiş noktalarının geçerli olduğunu belirtmek için kodu güncelleştirin.

1. Visual Studio 'da **B2C-WebAPI-DotNet. sln** çözümünü açın
1. **Taskservice** projesinde, Düzenleyicinizde *taskservice\\\\App_Start **Startup.auth.cs**.* dosyasını açın
1. Aşağıdaki `using` yönergeyi dosyanın en üstüne ekleyin:

    `using System.Collections.Generic;`
1. [`ValidIssuers`][validissuers] [Özelliği`TokenValidationParameters`][tokenvalidationparameters] tanımına ekleyin ve önceki bölümde kaydettiğiniz URI 'leri belirtin:

    ```csharp
    TokenValidationParameters tvps = new TokenValidationParameters
    {
        // Accept only those tokens where the audience of the token is equal to the client ID of this app
        ValidAudience = ClientId,
        AuthenticationType = Startup.DefaultPolicy,
        ValidIssuers = new List<string> {
            "https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/",
            "https://{your-b2c-tenant}.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/"
        }
    };
    ```

`TokenValidationParameters`, MSAL.NET tarafından sağlanır ve *Startup.auth.cs*içindeki kodun sonraki bölümünde owın ara yazılımı tarafından kullanılır. Birden çok geçerli veren belirtildiğinde, OWıN uygulama işlem hattı, her iki belirteç uç hattının de geçerli verenler olduğunu fark eder.

```csharp
app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
{
    // This SecurityTokenProvider fetches the Azure AD B2C metadata &  from the OpenID Connect metadata endpoint
    AccessTokenFormat = new JwtFormat(tvps, new tCachingSecurityTokenProvider(String.Format(AadInstance, ultPolicy)))
});
```

Daha önce belirtildiği gibi, diğer OWIN kitaplıkları genellikle birden çok veren desteklemek için benzer bir özellik sağlar. Her kitaplık için örneklerin sağlanması bu makalenin kapsamı dışındadır, ancak çoğu kitaplık için benzer bir teknik kullanabilirsiniz.

## <a name="switch-endpoints-in-web-app"></a>Web uygulamasındaki uç noktaları değiştirme

Artık Web API 'niz tarafından desteklenen URI 'Ler sayesinde, Web uygulamanızı b2clogin.com uç noktasından belirteçleri alması için güncelleştirmeniz gerekir.

Örneğin, `ida:AadInstance` **taskwebapp** projesinin *taskwebapp\\**Web. config**.* dosyasındaki değeri değiştirerek, örnek Web uygulamasını yeni uç noktayı kullanacak şekilde yapılandırabilirsiniz.

Taskwebapp 'in *Web. config* dosyasındaki `{your-b2c-tenant-name}.b2clogin.com` `login.microsoftonline.com` `ida:AadInstance` değeri, yerine başvuru olacak şekilde değiştirin.

Önce:

```xml
<!-- Old value -->
<add key="ida:AadInstance" value="https://login.microsoftonline.com/tfp/{0}/{1}" />
```

Sonra (B2C `{your-b2c-tenant}` kiracınızın adıyla değiştirin):

```xml
<!-- New value -->
<add key="ida:AadInstance" value="https://{your-b2c-tenant}.b2clogin.com/tfp/{0}/{1}" />
```

Uç nokta dizeleri Web uygulamasının yürütülmesi sırasında oluşturulduğunda, b2clogin.com tabanlı uç noktalar belirteçleri istediğinde kullanılır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, birden çok veren uç noktasından belirteçleri kabul etmek için Microsoft OWıN ara yazılımı (Katana) uygulayan bir Web API 'SI yapılandırma yöntemi sunulmuştur. Fark edebilirsiniz, ancak bu projeleri kendi kiracınızda derlemek ve çalıştırmak istiyorsanız, değiştirilmesi gereken hem TaskService hem de TaskWebApp projelerinin *Web. config* dosyalarında bulunan birkaç başka dize vardır. Projeleri eylemde görmek istiyorsanız uygun şekilde değiştirmek için hoş geldiniz, ancak bunu yaparak tam bir adım adım bu makalenin kapsamı dışındadır.

Azure AD B2C tarafından yayılan farklı güvenlik belirteçleri türleri hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C belirteçlere genel bakış](active-directory-b2c-reference-tokens.md).

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip
[sample-repo]: https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi

<!-- LINKS - Internal -->
[katana]: https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/
[validissuers]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters.validissuers
[tokenvalidationparameters]: https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters
