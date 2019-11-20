---
title: Hızlı başlangıç-Azure Active Directory B2C kullanarak bir masaüstü uygulaması için oturum açma ayarlama
description: Hesap oturum açma sağlamak için Azure Active Directory B2C kullanan bir örnek WPF Masaüstü uygulaması çalıştırın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e11fd2c8ff250ff505f312507a577d67ff9c1454
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065836"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Hızlı Başlangıç: Azure Active Directory B2C kullanarak bir masaüstü uygulaması için oturum açma ayarlama

Azure Active Directory B2C (Azure AD B2C) uygulamanızın, işinizin ve müşterilerinin korunmasını sağlamak için bulut kimlik yönetimi sağlar. Azure AD B2C; uygulamalarınızın, açık standart protokolleri kullanarak sosyal hesaplarda ve kurumsal hesaplarda kimlik doğrulaması gerçekleştirmesine olanak tanır. Bu hızlı başlangıçta bir sosyal kimlik sağlayıcısı kullanarak oturum açmak ve Azure AD B2C korumalı bir web API’sini çağırmak için bir Windows Presentation Foundation (WPF) masaüstü uygulaması kullanacaksınız.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) .
- Facebook, Google veya Microsoft 'dan bir sosyal hesap.
- GitHub’dan [zip dosyasını indirin](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/master.zip) veya örnek web uygulamasını kopyalayın.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>Uygulamayı Visual Studio'da çalıştırma

1. Örnek uygulama proje klasöründeki **active-directory-b2c-wpf.sln** çözümünü Visual Studio'da açın.
2. Uygulamada hata ayıklamak için **F5**’e basın.

## <a name="sign-in-using-your-account"></a>Hesabınızı kullanarak oturum açın

1. **Kaydolma veya Oturum Açma** iş akışını başlatmak için **Oturum aç**’a tıklayın.

    ![Örnek WPF uygulamasının ekran görüntüsü](media/active-directory-b2c-quickstarts-desktop-app/wpf-sample-application.png)

    Örnek, çeşitli kaydolma seçeneklerini destekler. Bu seçenekler, bir sosyal kimlik sağlayıcısı kullanmayı veya bir e-posta adresi kullanarak yerel bir hesap oluşturmayı içerir. Bu hızlı başlangıç için Facebook, Google veya Microsoft 'dan bir sosyal kimlik sağlayıcısı hesabı kullanın.


2. Azure AD B2C, örnek Web uygulaması için fabrikam adlı kurgusal bir şirket için oturum açma sayfası sunar. Sosyal kimlik sağlayıcısı kullanarak kaydolmak için, kullanmak istediğiniz kimlik sağlayıcısının düğmesine tıklayın.

    ![Kimlik sağlayıcılarını gösteren oturum açma veya kaydolma sayfası](media/active-directory-b2c-quickstarts-desktop-app/sign-in-or-sign-up-wpf.png)

    Sosyal hesap kimlik bilgilerinizi kullanarak kimlik doğrulaması yapabilir (oturum açın) ve uygulamayı sosyal hesabınızdaki bilgileri okumak üzere yetkilendirirsiniz. Erişim izni verdiğinizde uygulama sosyal hesabınızdan adınız ve şehriniz gibi profil bilgilerini alabilir.

2. Kimlik sağlayıcısına ilişkin oturum açma işlemini tamamlayın.

    Yeni hesap profili ayrıntılarınız, sosyal hesabınızdaki bilgilerle önceden doldurulur.

## <a name="edit-your-profile"></a>Profilinizi düzenleme

Azure AD B2C, kullanıcılara profillerini güncelleme olanağı tanıyan bir işlev sunar. Örnek Web uygulaması, iş akışı için bir Azure AD B2C düzenleme profili Kullanıcı akışı kullanır.

1. Oluşturduğunuz profili düzenlemek için uygulamanın menü çubuğunda **Profili düzenle**’ye tıklayın.

    ![WPF örnek uygulamasında profil Düzenle düğmesi vurgulanmış](media/active-directory-b2c-quickstarts-desktop-app/edit-profile-wpf.png)

2. Oluşturduğunuz hesapla ilişkili kimlik sağlayıcısını seçin. Örneğin, hesabınızı oluştururken kimlik sağlayıcısı olarak Facebook kullandıysanız, ilişkili profil ayrıntılarını değiştirmek için Facebook ' u seçin.

3. **Görünen adınızı** veya **Şehrinizi** değiştirip **Devam**’a tıklayın.

    *Belirteç bilgileri* metin kutusunda yeni bir erişim belirteci görüntülenir. Profilinizdeki değişiklikleri doğrulamak istiyorsanız erişim belirtecini kopyalayıp https://jwt.ms belirteç kod çözücüsüne yapıştırın.

## <a name="access-a-protected-api-resource"></a>Korumalı bir API kaynağına erişme

Korumalı kaynaktan istekte bulunmak için **API’yi çağır**’a tıklayın.

    ![Call API](media/active-directory-b2c-quickstarts-desktop-app/call-api-wpf.png)

    The application includes the Azure AD access token in the request to the protected web API resource. The web API sends back the display name contained in the access token.

Azure AD B2C korumalı bir web API’si için yetkili bir çağrıda bulunmak üzere Azure AD B2C kullanıcı hesabınızı başarıyla kullandınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Azure AD B2C hızlı başlangıçlarını veya öğreticilerini denemeyi planlıyorsanız Azure AD B2C kiracınızı kullanabilirsiniz. Artık ihtiyaç duymuyorsanız [Azure AD B2C kiracınızı silebilirsiniz](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, aşağıdakileri yapmak için örnek bir masaüstü uygulaması kullandınız:

* Özel bir oturum açma sayfasıyla oturum açın
* Sosyal kimlik sağlayıcısı ile oturum açın
* Azure AD B2C hesabı oluşturma
* Azure AD B2C tarafından korunan bir Web API 'sini çağırın

Kendi Azure AD B2C kiracınızı oluşturarak kullanmaya başlayın.

> [!div class="nextstepaction"]
> [Azure portalında Azure Active Directory B2C kiracısı oluşturma](tutorial-create-tenant.md)
