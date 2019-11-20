---
title: Hızlı başlangıç-Azure Active Directory B2C kullanarak tek sayfalı bir uygulama için oturum açma ayarlayın
description: Hesap oturum açma bilgileri sağlamak için Azure Active Directory B2C’yi kullanan örnek bir tek sayfalı uygulama çalıştırın.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 22cdfaf1d48c4e93e57f46482eacb66742a6c9d6
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515705"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Hızlı başlangıç: Azure Active Directory B2C'yi kullanarak tek sayfalı bir uygulama için oturum açma bilgilerini ayarlama

Azure Active Directory B2C (Azure AD B2C) uygulamanızın, işinizin ve müşterilerinin korunmasını sağlamak için bulut kimlik yönetimi sağlar. Azure AD B2C; uygulamalarınızın, açık standart protokolleri kullanarak sosyal hesaplarda ve kurumsal hesaplarda kimlik doğrulaması gerçekleştirmesine olanak tanır. Bu hızlı başlangıçta, sosyal kimlik sağlayıcısı kullanıp Azure AD B2C korumalı web API’si çağırarak oturum açmak için bir tek sayfalı uygulama kullanırsınız.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

- **ASP.net ve Web geliştirme** iş yüküyle [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [Node.js](https://nodejs.org/en/download/)
- Facebook, Google veya Microsoft 'tan sosyal hesap
- GitHub 'dan kod örneği: [Active-Directory-B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
    
    [ZIP arşivini indirebilir](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) veya depoyu klonlayabilirsiniz:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

1. Node.js komut isteminde şu komutları çalıştırarak sunucuyu başlatın:

    ```
    cd active-directory-b2c-javascript-msal-singlepageapp
    npm install && npm update
    node server.js
    ```

    Server.js, localhost’ta dinlediği bağlantı noktası numarasını çıkarır.

    ```
    Listening on port 6420...
    ```

2. Uygulamanın URL'sine göz atın. Örneğin, `http://localhost:6420`.

## <a name="sign-in-using-your-account"></a>Hesabınızı kullanarak oturum açın

1. İş akışını başlatmak için **Oturum aç**'a tıklayın.

    ![Tarayıcıda gösterilen tek sayfalı uygulama örnek uygulaması](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

    Örnek, sosyal kimlik sağlayıcısı kullanma veya bir e-posta adresiyle yerel bir hesap oluşturma da dahil olmak üzere çeşitli kaydolma seçeneklerini destekler. Bu hızlı başlangıç için Facebook, Google veya Microsoft 'dan bir sosyal kimlik sağlayıcısı hesabı kullanın.

2. Azure AD B2C, örnek Web uygulaması için fabrikam adlı kurgusal bir şirket için oturum açma sayfası sunar. Sosyal kimlik sağlayıcısı kullanarak kaydolmak için, kullanmak istediğiniz kimlik sağlayıcısının düğmesine tıklayın.

    ![Kimlik sağlayıcı düğmelerini gösteren oturum açma veya kaydolma sayfası](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

    Sosyal hesap kimlik bilgilerinizi kullanarak kimlik doğrulaması yapabilir (oturum açın) ve uygulamayı sosyal hesabınızdaki bilgileri okumak üzere yetkilendirirsiniz. Erişim izni verdiğinizde uygulama sosyal hesabınızdan adınız ve şehriniz gibi profil bilgilerini alabilir.

3. Kimlik sağlayıcısına ilişkin oturum açma işlemini tamamlayın.

## <a name="access-a-protected-api-resource"></a>Korumalı bir API kaynağına erişme

Görünen adınızın Web API’si çağrısından JSON nesnesi olarak döndürülmesi için **Web API’si Çağrısı**'na tıklayın.

![Web API yanıtını gösteren tarayıcıda örnek uygulama](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

Örnek tek sayfalı uygulama, korumalı web API’si kaynağına yönelik isteğe bir erişim belirteci ekler.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Azure AD B2C hızlı başlangıçlarını veya öğreticilerini denemeyi planlıyorsanız Azure AD B2C kiracınızı kullanabilirsiniz. Artık ihtiyaç duymuyorsanız [Azure AD B2C kiracınızı silebilirsiniz](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, aşağıdakileri yapmak için örnek bir tek sayfalı uygulama kullandınız:

* Özel bir oturum açma sayfasıyla oturum açın
* Sosyal kimlik sağlayıcısı ile oturum açın
* Azure AD B2C hesabı oluşturma
* Azure AD B2C tarafından korunan bir Web API 'sini çağırın

Kendi Azure AD B2C kiracınızı oluşturarak kullanmaya başlayın.

> [!div class="nextstepaction"]
> [Azure portalında Azure Active Directory B2C kiracısı oluşturma](tutorial-create-tenant.md)
