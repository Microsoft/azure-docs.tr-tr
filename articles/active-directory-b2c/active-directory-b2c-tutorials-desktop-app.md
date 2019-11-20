---
title: Öğretici-yerel istemci uygulamasındaki kullanıcıların kimliğini doğrulama-Azure Active Directory B2C
description: .NET masaüstü uygulaması için Kullanıcı oturum açma bilgilerini sağlamak üzere Azure Active Directory B2C kullanma hakkında öğretici.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cd0fc90988048f98be46370d2c7836d9506cc44a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475296"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C kullanarak yerel masaüstü istemcisinde kullanıcıların kimliğini doğrulama

Bu öğreticide, bir Windows Presentation Foundation (WPF) masaüstü uygulamasında Kullanıcı oturumu açmak ve kullanıcılara kaydolmak için Azure Active Directory B2C (Azure AD B2C) nasıl kullanılacağı gösterilmektedir. Azure AD B2C, uygulamalarınızın, açık standart protokoller kullanarak sosyal hesaplar, kurumsal hesaplar ve Azure Active Directory hesaplar için kimlik doğrulaması yapmasına olanak sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yerel istemci uygulamasını ekleme
> * Uygulamayı kullanmak için örneği yapılandırma
> * Kullanıcı akışını kullanarak kaydolma

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- Uygulamanızdaki kullanıcı deneyimlerini etkinleştirmek için [Kullanıcı akışları oluşturun](tutorial-create-user-flows.md) .
- **.Net masaüstü geliştirme** ve **ASP.net ve Web geliştirme** Iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) ' ü yükler.

## <a name="add-the-native-client-application"></a>Yerel istemci uygulamasını ekleme

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

## <a name="configure-the-sample"></a>Örneği yapılandırma

Bu öğreticide, GitHub 'dan indirebileceğiniz bir örnek yapılandırırsınız. Örnek WPF Masaüstü uygulaması kaydolma, oturum açma ve Azure AD B2C ' de korumalı bir Web API 'SI çağırma gösterir. [Bir zip dosyası indirin](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip), [depoya göz atın](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) veya GitHub’dan örneği kopyalayın.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Uygulamayı Azure AD B2C kiracınızla çalışacak şekilde güncelleştirmek ve varsayılan demo kiracısında olanlar yerine Kullanıcı akışlarını çağırmak için:

1. Visual Studio 'da **Active-Directory-B2C-WPF** çözümünü (`active-directory-b2c-wpf.sln`) açın.
2. **Active-Directory-B2C-WPF** projesinde, *app.xaml.cs* dosyasını açın ve aşağıdaki değişken tanımlarını bulun. `{your-tenant-name}`, Azure AD B2C kiracı adınızla değiştirin ve daha önce kaydettiğiniz uygulama KIMLIĞIYLE `{application-ID}`.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. İlke adı değişkenlerini, önkoşulların bir parçası olarak oluşturduğunuz Kullanıcı akışlarının adlarıyla güncelleştirin. Örneğin:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>Örneği çalıştırma

Örneği derlemek ve çalıştırmak için **F5** tuşuna basın.

### <a name="sign-up-using-an-email-address"></a>E-posta adresi kullanarak kaydolma

1. Kullanıcı olarak kaydolmak için **oturum aç '** ı seçin. Bu, **B2C_1_signupsignin1** Kullanıcı akışını kullanır.
2. Azure AD B2C, **Şimdi kaydol** bağlantısı olan bir oturum açma sayfası sunar. Henüz bir hesabınız olmadığından **Şimdi kaydolun** bağlantısını seçin.
3. Kaydolma iş akışında, kullanıcı kimliğini bir e-posta adresi kullanarak toplamak ve doğrulamak için bir sayfa sunar. Kaydolma iş akışı, kullanıcının parolasını ve Kullanıcı akışında tanımlanan istenen öznitelikleri de toplar.

    Geçerli bir e-posta adresi kullanın ve doğrulama kodunu kullanarak doğrulamayı gerçekleştirin. Parola ayarlayın. İstenen öznitelikler için değerleri girin.

    ![Oturum açma/kaydolma iş akışının bir parçası olarak gösterilen kaydolma sayfası](media/active-directory-b2c-tutorials-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Azure AD B2C kiracısında yerel hesap oluşturmak için **Oluştur** ' u seçin.

Kullanıcı artık e-posta adreslerini kullanarak oturum açabilir ve masaüstü uygulamasını kullanabilir. Başarılı bir kaydolma veya oturum açma işleminden sonra, belirteç ayrıntıları WPF uygulamasının alt bölmesinde görüntülenir.

![WPF Masaüstü uygulamasının alt bölmesinde gösterilen belirteç ayrıntıları](media/active-directory-b2c-tutorials-desktop-app/desktop-app-01-post-signin.png)

**API çağır** düğmesini seçerseniz bir **hata iletisi** görüntülenir. Bu hatayla karşılaşırsınız, çünkü geçerli durumunda uygulama tanıtım kiracısı tarafından korunan bir API 'ye erişmeye çalışıyor, `fabrikamb2c.onmicrosoft.com`. Erişim belirteciniz yalnızca Azure AD B2C kiracınız için geçerli olduğundan, API çağrısı bu nedenle yetkilendirilmemiş.

Kendi kiracınızda korumalı bir Web API 'sini kaydetmek ve **API çağırma** işlevini etkinleştirmek için sonraki öğreticiye geçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Yerel istemci uygulamasını ekleme
> * Uygulamayı kullanmak için örneği yapılandırma
> * Kullanıcı akışını kullanarak kaydolma

Sonra, **API 'Yi çağır** düğme işlevselliğini ETKINLEŞTIRMEK için WPF Masaüstü uygulamasına kendi Azure AD B2C kiracınızda kayıtlı BIR Web API 'sine erişim izni verin:

> [!div class="nextstepaction"]
> [Öğretici: bir masaüstü uygulamasından Node. js web API 'sine erişim Izni verme >](active-directory-b2c-tutorials-desktop-app-webapi.md)
