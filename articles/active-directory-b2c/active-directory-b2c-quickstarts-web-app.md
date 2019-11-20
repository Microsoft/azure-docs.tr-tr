---
title: Hızlı başlangıç-Azure Active Directory B2C kullanarak bir ASP.NET uygulaması için oturum açma ayarlama
description: Hesap oturum açma bilgileri sağlamak için Azure Active Directory B2C’yi kullanan örnek bir ASP.NET web uygulamasını çalıştırın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 446f891538f926342e1a4e3e5f5bca0cc791b27e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065812"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>Hızlı Başlangıç: Azure Active Directory B2C kullanarak bir ASP.NET uygulaması için oturum açma ayarlama

Azure Active Directory B2C (Azure AD B2C) uygulamanızın, işinizin ve müşterilerinin korunmasını sağlamak için bulut kimlik yönetimi sağlar. Azure AD B2C; uygulamalarınızın, açık standart protokolleri kullanarak sosyal hesaplarda ve kurumsal hesaplarda kimlik doğrulaması gerçekleştirmesine olanak tanır. Bu hızlı başlangıçta, sosyal kimlik sağlayıcısı kullanıp Azure AD B2C korumalı web API’si çağırarak oturum açmak için bir ASP.NET uygulaması kullanacaksınız.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) .
- Facebook, Google veya Microsoft 'dan bir sosyal hesap.
- GitHub’dan [zip dosyasını indirin](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) veya örnek web uygulamasını kopyalayın.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    Örnek çözümde iki proje vardır:

    - **TaskWebApp**: Görev listesi oluşturan ve düzenleyen bir web uygulamasıdır. Web uygulaması, kullanıcıların kaydolması veya oturum açması için kaydolma **veya oturum açma** Kullanıcı akışını kullanır.
    - **TaskService**: Görev listesi oluşturma, okuma, güncelleştirme ve silme işlevlerini destekleyen web API’sidir. Web API’si Azure AD B2C tarafından korunur ve web uygulaması tarafından çağrılır.

## <a name="run-the-application-in-visual-studio"></a>Uygulamayı Visual Studio'da çalıştırma

1. Örnek uygulama proje klasöründen, Visual Studio’da **B2C-WebAPI-DotNet.sln** çözümünü açın.
2. Bu hızlı başlangıç için aynı anda hem **TaskWebApp** hem de **TaskService** projesini çalıştırıyorsunuz. Çözüm Gezgini'nde **B2C-WebAPI-DotNet** çözümüne sağ tıklayıp **Başlangıç Projelerini Ayarla**'yı seçin.
3. **Çoklu başlangıç projeleri**'ni seçin ve iki projenin de **Eylem** alanını **Başlat** olarak değiştirin.
4. **Tamam**'ı tıklatın.
5. Her iki uygulamada da hata ayıklama gerçekleştirmek için **F5**’e basın. Her uygulama kendi tarayıcı sekmesinde açılır:

    - `https://localhost:44316/` - ASP.NET web uygulaması. Hızlı başlangıçta doğrudan bu uygulamayla etkileşim kurarsınız.
    - `https://localhost:44332/` - ASP.NET web uygulaması tarafından çağrılan web API’si.

## <a name="sign-in-using-your-account"></a>Hesabınızı kullanarak oturum açın

1. İş akışını başlatmak için ASP.NET web uygulamasında **Kaydol/Oturum aç**'a tıklayın.

    ![Kaydolma/imzalama bağlantısı vurgulanmış şekilde tarayıcıda örnek ASP.NET Web uygulaması](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    Örnek, sosyal kimlik sağlayıcısı kullanma veya bir e-posta adresiyle yerel bir hesap oluşturma da dahil olmak üzere çeşitli kaydolma seçeneklerini destekler. Bu hızlı başlangıç için Facebook, Google veya Microsoft 'dan bir sosyal kimlik sağlayıcısı hesabı kullanın.

2. Azure AD B2C, örnek Web uygulaması için fabrikam adlı kurgusal bir şirket için oturum açma sayfası sunar. Sosyal kimlik sağlayıcısı kullanarak kaydolmak için, kullanmak istediğiniz kimlik sağlayıcısının düğmesine tıklayın.

    ![Kimlik sağlayıcı düğmelerini gösteren oturum açma veya kaydolma sayfası](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    Sosyal hesap kimlik bilgilerinizi kullanarak kimlik doğrulaması yapabilir (oturum açın) ve uygulamayı sosyal hesabınızdaki bilgileri okumak üzere yetkilendirirsiniz. Erişim izni verdiğinizde uygulama sosyal hesabınızdan adınız ve şehriniz gibi profil bilgilerini alabilir.

3. Kimlik sağlayıcısına ilişkin oturum açma işlemini tamamlayın.

## <a name="edit-your-profile"></a>Profilinizi düzenleme

Azure Active Directory B2C, kullanıcılara profillerini güncelleme olanağı tanıyan bir işlev sunar. Örnek Web uygulaması, iş akışı için bir Azure AD B2C düzenleme profili Kullanıcı akışı kullanır.

1. Uygulamanın menü çubuğunda profil adınıza tıklayın ve oluşturduğunuz profili düzenlemek için **Profili düzenle**’yi seçin.

    ![Profil Düzenle bağlantısı vurgulanmış şekilde tarayıcıda örnek Web uygulaması](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. **Görünen adınızı** veya **Şehrinizi** değiştirin ve profilinizi güncelleştirmek için **Devam**’a tıklayın.

    Değişiklik, web uygulamasının giriş sayfasının sağ üst kısmında görüntülenir.

## <a name="access-a-protected-api-resource"></a>Korumalı bir API kaynağına erişme

1. Yapılacaklar listesi öğelerinizi girmek ve değiştirmek için **Yapılacaklar Listesi**’ne tıklayın.

2. Metni **Yeni Öğe** metin kutusuna girin. Yapılacaklar listesi öğesi ekleyen Azure AD B2C korumalı web API’sini çağırmak için **Ekle**’ye tıklayın.

    ![Yapılacaklar listesi öğesi Ekle ile tarayıcıda örnek Web uygulaması](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    ASP.NET web uygulaması, kullanıcının yapılacaklar listesi öğelerinde işlem gerçekleştirmek için korumalı web API’si kaynağına gönderilen isteğe bir Azure AD erişim belirteci ekler.

Azure AD B2C korumalı bir web API’si için yetkili bir çağrıda bulunmak üzere Azure AD B2C kullanıcı hesabınızı başarıyla kullandınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Azure AD B2C hızlı başlangıçlarını veya öğreticilerini denemeyi planlıyorsanız Azure AD B2C kiracınızı kullanabilirsiniz. Artık ihtiyaç duymuyorsanız [Azure AD B2C kiracınızı silebilirsiniz](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şu şekilde bir örnek ASP.NET uygulaması kullandınız:

* Özel bir oturum açma sayfasıyla oturum açın
* Sosyal kimlik sağlayıcısı ile oturum açın
* Azure AD B2C hesabı oluşturma
* Azure AD B2C tarafından korunan bir Web API 'sini çağırın

Kendi Azure AD B2C kiracınızı oluşturarak kullanmaya başlayın.

> [!div class="nextstepaction"]
> [Azure portalında Azure Active Directory B2C kiracısı oluşturma](tutorial-create-tenant.md)
