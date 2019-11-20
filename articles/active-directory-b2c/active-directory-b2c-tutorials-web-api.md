---
title: Öğretici-Azure Active Directory B2C bir ASP.NET Web API 'sine erişim Izni verme
description: Bir ASP.NET Web API 'sini korumak ve bir ASP.NET Web uygulamasından çağırmak için Active Directory B2C kullanma öğreticisi.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 0cd2eff7e3fafc1a6f3418a1f86629cfb1584e89
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641762"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C kullanarak bir ASP.NET Web API 'sine erişim Izni verme

Bu öğreticide, bir ASP.NET Web uygulamasından Azure Active Directory B2C (Azure AD B2C) ' de korumalı bir Web API kaynağını çağırma gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği yapılandırma

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

Öğreticideki adımları ve önkoşulları doldurun [: Azure Active Directory B2C kullanarak bir Web uygulamasında kimlik doğrulamasını etkinleştirme](active-directory-b2c-tutorials-web-app.md).

## <a name="add-a-web-api-application"></a>Web API'si uygulaması ekleme

Web API kaynakları, erişim belirteci sunan istemci uygulamalarına göre korunan kaynak isteklerini kabul etmeden ve bunlara yanıt verebilmeleri için kiracınızda kayıtlı olmalıdır.

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için, geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
3. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
4. **Uygulamalar**' ı seçin ve ardından **Ekle**' yi seçin.
5. Uygulama için bir ad girin. Örneğin, *webapi1*.
6. **İçerme Web uygulaması/Web API 'si**için **Evet**' i seçin.
7. **Yanıt URL 'si**için Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gereken bir uç nokta girin. Bu öğreticide, örnek yerel olarak çalışır ve `https://localhost:44332`' de dinler.
8. **Uygulama kimliği URI 'si**için, Web API 'niz için kullanılan tanımlayıcıyı girin. Tam etki alanı ile birlikte URI tanımlayıcısı sizin için oluşturulur. Örneğin, `https://contosotenant.onmicrosoft.com/api`.
9. **Oluştur**'a tıklayın.
10. Özellikler sayfasında, Web uygulamasını yapılandırırken kullanacağınız uygulama KIMLIĞINI kaydedin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *webapi1*.
1. **Yeniden yönlendirme URI 'si**altında **Web**' i seçin ve ardından Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gerektiği bir uç nokta girin. Bu öğreticide, örnek yerel olarak çalışır ve `https://localhost:44332`' de dinler.
1. **Kaydol**’u seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

* * *

## <a name="configure-scopes"></a>Kapsamları yapılandırma

Kapsamlar, korumalı kaynaklara erişimi yönetmek için bir yol sağlar. Kapsamlar web API’si tarafından kapsam tabanlı erişim denetimi uygulamak için kullanılır. Örneğin web API'sinin kullanıcıları hem okuma hem de yazma veya yalnızca okuma erişimine sahip olabilir. Bu öğreticide web API’si için okuma ve yazma izinlerini tanımlamak için kapsamları kullanacaksınız.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>İzinleri verme

Bir uygulamadan korumalı bir Web API 'SI çağırmak için uygulamanızın API 'sine izin vermeniz gerekir. Önkoşul öğreticisinde, Azure AD B2C adlı *WebApp1*adlı bir Web uygulaması oluşturdunuz. Web API 'sini çağırmak için bu uygulamayı kullanın.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uygulamanız, korumalı Web API 'sini çağırmak için kaydedilir. Kullanıcı, uygulamayı kullanmak için Azure AD B2C kimliğini doğrular. Uygulama, korumalı Web API 'sine erişmek için Azure AD B2C bir yetkilendirme izni alır.

## <a name="configure-the-sample"></a>Örneği yapılandırma

Web API 'SI kayıtlı olduğuna ve kapsamlarınızın tanımlandığından, Web API 'sini Azure AD B2C kiracınızı kullanacak şekilde yapılandırırsınız. Bu öğreticide örnek bir web API’sini yapılandıracaksınız. Örnek Web API 'SI, önkoşul öğreticisinde indirdiğiniz projeye dahil edilmiştir.

Örnek çözümde iki proje vardır:

* **Taskwebapp** -bir görev listesi oluşturun ve düzenleyin. Örnek, kullanıcıların kaydolması veya oturum açması için kaydolma **veya oturum açma** Kullanıcı akışını kullanır.
* **Taskservice** -oluşturma, okuma, güncelleştirme ve silme görev listesi işlevlerini destekler. API Azure AD B2C tarafından korunur ve TaskWebApp tarafından çağırılır.

### <a name="configure-the-web-application"></a>Web uygulamasını yapılandırma

1. **B2C-WebAPI-DotNet** çözümünü Visual Studio’da açın.
1. **Taskwebapp** projesinde **Web. config**dosyasını açın.
1. API’yi yerel olarak çalıştırmak üzere, **api:TaskServiceUrl** için localhost ayarını kullanın. Web.config’i aşağıdaki gibi değiştirin:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. API’nin URI’sini yapılandırın. Bu, Web uygulamasının API isteğini yapmak için kullandığı URI 'dir. Ayrıca istenen izinleri yapılandırın.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>Web API’sini yapılandırma

1. **Taskservice** projesinde **Web. config**dosyasını açın.
1. API’yi kiracınızı kullanmak için yapılandırın.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. İstemci KIMLIĞINI kayıtlı Web API uygulamanızın uygulama KIMLIĞI olan *webapi1*olarak ayarlayın.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Kullanıcı akışı ayarını, kaydolma ve oturum açma Kullanıcı akışlarınızın adıyla güncelleştirin, *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Kapsamlar ayarını portalda oluşturduğunuz olanlarla eşleşecek şekilde yapılandırın.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>Örneği çalıştırma

**TaskWebApp** ve **TaskService** projelerinin ikisini birden çalıştırmanız gerekir.

1. Çözüm Gezgini’nde, çözüme sağ tıklayıp **Başlangıç Projelerini Ayarla...** seçeneğini belirleyin.
1. **Birden çok başlangıç projesi**seçin.
1. İki proje için de **Eylem**’i **Başlat** olarak değiştirin.
1. Yapılandırmayı kaydetmek için **Tamam** ' ı tıklatın.
1. İki uygulamayı da çalıştırmak için **F5**'e basın. Her uygulama kendi tarayıcı penceresinde açılır.
    * `https://localhost:44316/` Web uygulamasıdır.
    * `https://localhost:44332/` web API’sidir.

1. Web uygulamasında, Web uygulamasında oturum açmak için **kaydolma/oturum aç** ' ı seçin. Daha önce oluşturduğunuz hesabı kullanın.
1. Oturum açtıktan sonra, yapılacaklar **listesi** ' ni seçin ve yapılacaklar listesi öğesi oluşturun.

Bir yapılacaklar listesi öğesi oluşturduğunuzda, Web uygulaması, yapılacaklar listesi öğesini oluşturmak için Web API 'sine bir istek yapar. Korumalı Web uygulamanız Azure AD B2C tarafından korunan Web API 'sini çağırıyor.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Web API'si uygulaması ekleme
> * Bir Web API 'SI için Kapsamları yapılandırma
> * Web API 'sine izin verme
> * Uygulamayı kullanmak için örneği yapılandırma

> [!div class="nextstepaction"]
> [Öğretici: Azure Active Directory B2C ' de uygulamalarınıza kimlik sağlayıcıları ekleme](tutorial-add-identity-providers.md)
