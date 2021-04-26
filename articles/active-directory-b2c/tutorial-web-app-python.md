---
title: 'Öğretici: bir Python web uygulamasında kimlik doğrulamasını etkinleştirme'
titleSuffix: Azure AD B2C
description: Bu öğreticide, Python Flask Web uygulaması için Kullanıcı oturumu açma sağlamak üzere Azure Active Directory B2C nasıl kullanacağınızı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555681"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Öğretici: Azure AD B2C bir Python web uygulamasında kimlik doğrulamasını etkinleştirme

Bu öğreticide, bir Python Flask Web uygulamasındaki kullanıcılara kaydolmak ve oturum açmak için Azure Active Directory B2C (Azure AD B2C) nasıl kullanılacağı gösterilmektedir.

Bu öğreticide:

> [!div class="checklist"]
> * Azure AD B2C kiracınızda kayıtlı bir uygulamaya yanıt URL 'SI ekleme
> * GitHub 'dan bir kod örneği indirin
> * Örnek uygulamanın kodunu kiracınızla çalışacak şekilde değiştirin
> * Kaydolma/oturum açma Kullanıcı akışınızı kullanarak kaydolun

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımlara devam etmeden önce aşağıdaki Azure AD B2C kaynaklara sahip olmanız gerekir:

* [Azure AD B2C kiracı](tutorial-create-tenant.md)
* Kiracınızda [kayıtlı olan uygulama](tutorial-register-applications.md) ve *uygulamanın (istemci) kimliği* ve *istemci gizli anahtarı*
* Kiracınızda [oluşturulan kullanıcı akışları](tutorial-create-user-flows.md)

Ayrıca, yerel geliştirme ortamınızda aşağıdakiler gereklidir:

* [Visual Studio Code](https://code.visualstudio.com/) veya başka bir kod Düzenleyicisi
* [Python](https://nodejs.org/en/download/) 2.7 + veya 3 +

## <a name="add-a-redirect-uri"></a>Yeniden yönlendirme URI 'SI Ekle

Önkoşulların bir parçası olarak tamamladığınız ikinci öğreticide, Azure AD B2C bir Web uygulaması kaydettiniz. Bu öğreticide kod örneğiyle iletişimi etkinleştirmek için uygulama kaydına bir yanıt URL 'SI (yeniden yönlendirme URI 'SI olarak da bilinir) ekleyin.

Azure AD B2C kiracınızdaki bir uygulamayı güncelleştirmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin, **sahip olunan uygulamalar** sekmesini seçin ve ardından *WebApp1* uygulamasını seçin.
1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Web** altında **URI Ekle** bağlantısını seçin ve `http://localhost:5000/getAToken` metin kutusuna girin.
1. **Kaydet**’i seçin.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve sonra **Azure AD B2C**' i arayıp seçin.
1. **Uygulamalar (eski)** öğesini seçin ve ardından *WebApp1* uygulamasını seçin.
1. **Yanıt URL 'si** altında, ekleyin `http://localhost:5000/getAToken` .
1. **Kaydet**’i seçin.
* * *

## <a name="get-the-sample-code"></a>Örnek kodunu alma

Bu öğreticide, GitHub 'dan yüklediğiniz bir kod örneğini B2C kiracınızla çalışacak şekilde yapılandırırsınız. Örnek, bir Python Flask Web uygulamasının Kullanıcı kaydı ve oturum açma için Azure AD B2C nasıl kullanabileceğinizi gösterir.

[İndirin. ZIP arşivi](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) veya [kod örneği deposunu](https://github.com/Azure-Samples/ms-identity-python-webapp) GitHub 'dan klonlayın.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Örneği güncelleştirme

Örneği aldıktan sonra, uygulamayı Azure AD B2C kiracınızı, uygulama kaydınızı ve Kullanıcı akışlarını kullanacak şekilde yapılandırın.

Projenin kök dizininde:

1. *App_config. Kopyala* dosyasını *app_config. Kopyala. old* olarak yeniden adlandırın
1. *App_config_b2c. Kopyala* ' yı *app_config. Kopyala* olarak yeniden adlandırın

Yeni yeniden adlandırılmış *app_config. Kopyala* ' yı Azure AD B2C kiracınız ve önkoşulların bir parçası olarak oluşturduğunuz uygulama kaydınız için değerlerle güncelleştirin.

1. *App_config. Kopyala* dosyasını Düzenleyicinizde açın.
1. `b2c_tenant`Değeri, Azure AD B2C kiracınızın adıyla güncelleştirin, örneğin *contosob2c*.
1. Değerlerin her birini, `*_user_flow` önkoşulların bir parçası olarak oluşturduğunuz Kullanıcı akışlarının adlarıyla eşleşecek şekilde güncelleştirin.
1. Değeri, `CLIENT_ID` önkoşulların bir parçası olarak kaydettiğiniz Web uygulamasının **uygulama (ISTEMCI) kimliğiyle** güncelleştirin.
1. Değeri, `CLIENT_SECRET` önkoşullarda oluşturduğunuz **istemci parolasının** değeriyle güncelleştirin. Daha yüksek güvenlik için, bir **ortam değişkeninde** bunun yerine açıklamaları içinde önerilen şekilde depolamayı ele.

*App_config. Kopyala* 'nın üst bölümü artık aşağıdaki kod parçacığına benzer şekilde görünmelidir:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Kod parçacığı açıklamalarında belirtildiği gibi, uygulama kodunuzda gizli dizileri **düz metin olarak saklamadığımızda** önerilir. Sabit kodlanmış değişken *yalnızca kolaylık sağlamak* için kod örneğinde kullanılır. Bir ortam değişkeni veya Azure Key Vault gibi bir gizli depo kullanmayı deneyin.

## <a name="run-the-sample"></a>Örneği çalıştırma

1. Konsolunuzun veya terminalinizde, örneği içeren dizine geçin. Örnek:

    ```console
    cd ms-identity-python-webapp
    ```
1. Pypı 'den gerekli paketleri yüklemek ve Web uygulamasını yerel makinenizde çalıştırmak için aşağıdaki komutları çalıştırın:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    Konsol penceresinde yerel olarak çalışan uygulamanın bağlantı noktası numarası görüntülenir:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. `http://localhost:5000`Yerel makinenizde çalışan Web uygulamasını görüntülemek için öğesine gidin.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Yerel olarak çalışan Python Flask Web uygulamasını gösteren Web tarayıcısı":::

### <a name="sign-up-using-an-email-address"></a>E-posta adresi kullanarak kaydolma

Bu örnek uygulama kaydolma, oturum açma ve parola sıfırlama 'yı destekler. Bu öğreticide, bir e-posta adresi kullanarak kaydolabilirsiniz.

1. Daha önceki bir adımda belirttiğiniz *B2C_1_signupsignin1* Kullanıcı akışını başlatmak Için **oturum aç '** ı seçin.
1. Azure AD B2C, kaydolma bağlantısı içeren bir oturum açma sayfası sunar. Henüz bir hesabınız olmadığından **Şimdi kaydolun** bağlantısını seçin.
1. Kaydolma iş akışı, bir e-posta adresi kullanarak kullanıcının kimliğini toplayıp doğrulamak için bir sayfa sunar. Kaydolma iş akışı, kullanıcının parolasını ve Kullanıcı akışında tanımlanan istenen öznitelikleri de toplar.

    Geçerli bir e-posta adresi kullanın ve doğrulama kodunu kullanarak doğrulamayı gerçekleştirin. Parola ayarlayın. İstenen öznitelikler için değerleri girin.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Azure AD B2C Kullanıcı akışı tarafından görünen kaydolma sayfası":::

1. Azure AD B2C dizininde yerel bir hesap oluşturmak için **Oluştur** ' u seçin.

**Oluştur**' u seçtiğinizde, uygulama, oturum açmış kullanıcının adını gösterir.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Oturum açmış kullanıcıyla Python Flask Web uygulamasını gösteren Web tarayıcısı":::

Oturum açma sınamasını yapmak isterseniz, oturum **kapatma** bağlantısını seçin ve **oturum açın** ' ı seçin ve kaydolduğunuzda girdiğiniz e-posta adresi ve parolayla oturum açın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, kaydolma ve oturum açma özelliği sağlamak üzere Azure AD B2C kiracınızdaki bir Kullanıcı akışıyla çalışacak bir Python Flask Web uygulaması yapılandırdınız. Şu adımları tamamladınız:

> [!div class="checklist"]
> * Azure AD B2C kiracınızda kayıtlı bir uygulamaya yanıt URL 'SI eklendi
> * GitHub 'dan bir kod örneği indirildi
> * Örnek uygulamanın kodunu kiracınızla çalışacak şekilde değiştirdi
> * Kaydolma/oturum açma Kullanıcı akışınız kullanılarak kaydolup

Daha sonra, Azure AD B2C göre kullanıcılarınıza gösterilecek Kullanıcı akış sayfalarının Kullanıcı arabirimini özelleştirmeyi öğrenin:

> [!div class="nextstepaction"]
> [Azure AD B2C >Kullanıcı deneyimlerinin arabirimini özelleştirme ](customize-ui.md)