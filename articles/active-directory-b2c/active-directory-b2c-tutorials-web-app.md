---
title: Öğretici-bir Web uygulamasında kimlik doğrulamasını etkinleştirme-Azure Active Directory B2C
description: Bir ASP.NET web uygulamasında kullanıcının oturum açmasını sağlamak için Azure Active Directory B2C’nin nasıl kullanılacağını gösteren öğretici.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 10/14/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: d88ad13c1326ce62b73dbffa0c79904c6f81e6f4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641791"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C kullanarak bir Web uygulamasında kimlik doğrulamasını etkinleştirme

Bu öğreticide, bir ASP.NET Web uygulamasındaki kullanıcıları oturum açmak ve kaydolmak için Azure Active Directory B2C (Azure AD B2C) nasıl kullanılacağı gösterilmektedir. Azure AD B2C, uygulamalarınızın, açık standart protokoller kullanarak sosyal hesaplar, kurumsal hesaplar ve Azure Active Directory hesaplar için kimlik doğrulaması yapmasına olanak sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure AD B2C içinde uygulamayı güncelleştirme
> * Uygulamayı kullanmak için örneği yapılandırma
> * Kullanıcı akışını kullanarak kaydolma

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Ön koşullar

* Uygulamanızdaki kullanıcı deneyimlerini etkinleştirmek için [Kullanıcı akışları oluşturun](tutorial-create-user-flows.md) .
* **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) ' i yükledikten sonra.

## <a name="update-the-application-registration"></a>Uygulama kaydını güncelleştirme

Önkoşulların bir parçası olarak tamamladığınız öğreticide, Azure AD B2C bir Web uygulaması kaydettiniz. Bu öğreticideki örnekle iletişimi etkinleştirmek için bir yeniden yönlendirme URI 'SI eklemeniz ve kayıtlı uygulama için bir istemci gizli anahtarı (anahtar) oluşturmanız gerekir.

### <a name="add-a-redirect-uri-reply-url"></a>Yeniden yönlendirme URI 'SI (yanıt URL 'SI) ekleme

Uygulamayı güncelleştirmek için geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Uygulamalar**' ı seçin ve ardından *WebApp1* uygulamasını seçin.
1. **Yanıt URL 'si**altında `https://localhost:44316`ekleyin.
1. **Kaydet**’i seçin.
1. Özellikler sayfasında, Web uygulamasını yapılandırırken daha sonraki bir adımda kullanmak üzere uygulama KIMLIĞINI kaydedin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulama kayıtları (Önizleme)** öğesini seçin, **sahip olunan uygulamalar** sekmesini seçin ve ardından *WebApp1* uygulamasını seçin.
1. **Kimlik doğrulaması**' nı seçin ve ardından **Yeni deneyimi deneyin** (gösteriliyorsa) seçeneğini belirleyin.
1. **Web**altında **URI Ekle** bağlantısını seçin, `https://localhost:44316`girin ve ardından **Kaydet**' i seçin.
1. **Genel Bakış**’ı seçin.
1. Web uygulamasını yapılandırırken daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

* * *

### <a name="create-a-client-secret"></a>İstemci parolası oluşturma

Sonra, kayıtlı Web uygulaması için bir istemci gizli anahtarı oluşturun. Web uygulaması kod örneği, belirteç istenirken kimliğini kanıtlamak için bunu kullanır.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Örneği yapılandırma

Bu öğreticide, GitHub 'dan indirebileceğiniz bir örnek yapılandırırsınız. Örnek, basit bir yapılacaklar listesi sağlamak için ASP.NET kullanır. Örnek, [Microsoft OWIN ara yazılım bileşenlerini](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/)kullanır. GitHub’dan [zip dosyasını indirin](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) veya örneği kopyalayın. Örnek dosyayı, yolunun toplam karakter uzunluğu 260'tan az olan bir klasöre ayıkladığınızdan emin olun.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Aşağıdaki iki proje örnek çözümde bulunur:

* **Taskwebapp** -bir görev listesi oluşturun ve düzenleyin. Örnek, kullanıcıların kaydolması ve oturum açması için kaydolma **veya oturum açma** Kullanıcı akışını kullanır.
* **Taskservice** -oluşturma, okuma, güncelleştirme ve silme görev listesi işlevlerini destekler. API Azure AD B2C tarafından korunur ve TaskWebApp tarafından çağırılır.

Örnek, kiracınızda kayıtlı olan uygulamayı kullanacak şekilde, uygulama KIMLIĞI ve daha önce kaydettiğiniz anahtar dahil olmak üzere değiştirirsiniz. Ayrıca, oluşturduğunuz Kullanıcı akışlarını da yapılandırırsınız. Örnek, yapılandırma değerlerini *Web. config* dosyasında ayarlar olarak tanımlar.

Web. config dosyasındaki ayarları Kullanıcı akışınız ile çalışacak şekilde güncelleştirin:

1. **B2C-WebAPI-DotNet** çözümünü Visual Studio’da açın.
1. **Taskwebapp** projesinde **Web. config** dosyasını açın.
    1. `ida:Tenant` ve `ida:AadInstance` değerini, oluşturduğunuz Azure AD B2C kiracının adıyla güncelleştirin. Örneğin `fabrikamb2c` `contoso`ile değiştirin.
    1. `ida:ClientId` değerini, kaydettiğiniz uygulama KIMLIĞIYLE değiştirin.
    1. `ida:ClientSecret` değerini kaydettiğiniz anahtarla değiştirin. Web. config dosyasına eklemeden önce, istemci gizliliğini XML olarak kodlamanız gerekir.
    1. `ida:SignUpSignInPolicyId` değerini `b2c_1_signupsignin1`ile değiştirin.
    1. `ida:EditProfilePolicyId` değerini `b2c_1_profileediting1`ile değiştirin.
    1. `ida:ResetPasswordPolicyId` değerini `b2c_1_passwordreset1`ile değiştirin.

## <a name="run-the-sample"></a>Örneği çalıştırma

1. Çözüm Gezgini, **Taskwebapp** projesine sağ tıklayın ve ardından **Başlangıç projesi olarak ayarla**' ya tıklayın.
1. **F5**tuşuna basın. Varsayılan tarayıcı, `https://localhost:44316/` olan yerel web sitesi adresini açar.

### <a name="sign-up-using-an-email-address"></a>E-posta adresi kullanarak kaydolma

1. Uygulamanın bir kullanıcısı olarak kaydolmak için **kaydolun/oturum aç '** ı seçin. **B2c_1_signupsignin1** Kullanıcı akışı kullanılır.
1. Azure AD B2C, kayıt bağlantısı içeren bir oturum açma sayfası sunar. Henüz bir hesabınız olmadığından **Şimdi kaydolun**' ı seçin. Kaydolma iş akışında, kullanıcı kimliğini bir e-posta adresi kullanarak toplamak ve doğrulamak için bir sayfa sunar. Kaydolma iş akışı, kullanıcının parolasını ve Kullanıcı akışında tanımlanan istenen öznitelikleri de toplar.
1. Geçerli bir e-posta adresi kullanın ve doğrulama kodunu kullanarak doğrulamayı gerçekleştirin. Parola ayarlayın. İstenen öznitelikler için değerleri girin.

    ![Oturum açma/kaydolma iş akışının bir parçası olarak gösterilen kaydolma sayfası](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Azure AD B2C kiracısında yerel hesap oluşturmak için **Oluştur** ' u seçin.

Uygulama kullanıcısı artık kendi e-posta adreslerini kullanarak oturum açabilir ve Web uygulamasını kullanabilir.

Ancak, bu yapılacaklar **listesi** özelliği, serideki bir sonraki öğreticiyi tamamlayana kadar çalışmaz, [öğretici: bir ASP.NET Web API 'sini korumak için Azure AD B2C kullanın](active-directory-b2c-tutorials-web-api.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure AD B2C içinde uygulamayı güncelleştirme
> * Uygulamayı kullanmak için örneği yapılandırma
> * Kullanıcı akışını kullanarak kaydolma

Şimdi, Web uygulamasının Yapılacaklar **listesi** özelliğini etkinleştirmek için sonraki öğreticiye geçin. Bu durumda, bir Web API uygulamasını kendi Azure AD B2C kiracınıza kaydeder ve sonra API kimlik doğrulaması için kiracınızı kullanacak şekilde kod örneğini değiştirirsiniz.

> [!div class="nextstepaction"]
> [Öğretici: bir ASP.NET Web API 'sini korumak için Azure Active Directory B2C kullanın >](active-directory-b2c-tutorials-web-api.md)
