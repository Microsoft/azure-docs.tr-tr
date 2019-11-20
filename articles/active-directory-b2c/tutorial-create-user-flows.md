---
title: Öğretici-Kullanıcı akışları oluşturma-Azure Active Directory B2C
description: Kaydolma, oturum açma ve Azure Active Directory B2C uygulamalarınız için Kullanıcı profili düzenlemesini etkinleştirmek üzere Azure portal Kullanıcı akışları oluşturmayı öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3633ffb09d71bd166cb7366f860662d9b91c7f07
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063392"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C Kullanıcı akışları oluşturma

Uygulamalarınızda, kullanıcıların kaydolmalarına, oturum açmalarına veya profillerini yönetmesine olanak tanıyan [Kullanıcı akışlarına](active-directory-b2c-reference-policies.md) sahip olabilirsiniz. Azure Active Directory B2C (Azure AD B2C) kiracınızda farklı türlerde birden çok Kullanıcı akışı oluşturabilir ve bunları gerektiği şekilde uygulamalarınızda kullanabilirsiniz. Kullanıcı akışları, uygulamalar arasında yeniden kullanılabilir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Kaydolma ve oturum açma Kullanıcı akışı oluşturma
> * Profil düzenlemesi Kullanıcı akışı oluşturma
> * Parola sıfırlama Kullanıcı akışı oluşturma

Bu öğreticide, Azure portal kullanarak önerilen bazı Kullanıcı akışlarını nasıl oluşturacağınız gösterilmektedir. Uygulamanızda bir kaynak sahibi parola kimlik bilgileri (ROPC) akışının nasıl ayarlanacağı hakkında bilgi arıyorsanız, bkz. [Azure AD B2C kaynak sahibi parola kimlik bilgileri akışını yapılandırma](configure-ropc.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Oluşturmak istediğiniz kullanıcı akışlarının parçası olan [uygulamalarınızı kaydedin](tutorial-register-applications.md) .

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Kaydolma ve oturum açma Kullanıcı akışı oluşturma

Kaydolma ve oturum açma Kullanıcı akışı, hem kayıt hem de oturum açma deneyimlerini tek bir yapılandırmayla işler. Uygulamanızın kullanıcıları, bağlama göre doğru yolun altına alınır.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.

    ![B2C kiracısı seçiliyken dizin ve abonelik filtresi](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **İlkeler**altındaki sol menüde, **Kullanıcı akışları (ilkeler)** öğesini seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.

    ![Yeni Kullanıcı akışı düğmesi vurgulanmış şekilde portalda Kullanıcı akışları sayfası](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. **Önerilen** sekmede, **oturum aç ve oturum aç** Kullanıcı akışı ' nı seçin.

    ![Kaydolma ve oturum açma akışı vurgulanmış bir Kullanıcı akış sayfası seçin](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Kullanıcı akışı için bir **ad** girin. Örneğin, *signupsignin1*.
1. **Kimlik sağlayıcıları**Için **e-posta kaydı**' nı seçin.

    ![Azure portal ' de vurgulanan özelliklerle Kullanıcı akışı oluşturma sayfası](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. **Kullanıcı öznitelikleri ve talepler**için, kayıt sırasında kullanıcıdan toplamak ve göndermek istediğiniz talepleri ve öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve ardından **ülke/bölge**, **görünen ad**ve **posta kodu**için öznitelikler ve talepler ' i seçin. **Tamam**'ı tıklatın.

    ![Üç talep seçili olan öznitelikler ve talepler seçim sayfası](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. Bir *B2C_1* öneki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama**için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi `https://jwt.ms`gerekir.
1. **Kullanıcı akışını Çalıştır**' a tıklayın ve ardından **Şimdi kaydolun**' ı seçin.

    ![Kullanıcı akışını Çalıştır düğmesi vurgulanmış şekilde portalda Kullanıcı akış sayfasını Çalıştır](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Geçerli bir e-posta adresi girin, **doğrulama kodu gönder**' e tıklayın, aldığınız doğrulama kodunu girin ve **kodu doğrula**' yı seçin.
1. Yeni bir parola girin ve parolayı onaylayın.
1. Ülkenizi ve bölgenizi seçin, görüntülenmesini istediğiniz adı girin, bir posta kodu girin ve ardından **Oluştur**' a tıklayın. Belirteç öğesine `https://jwt.ms` döner ve size gösterilmesi gerekir.
1. Artık kullanıcı akışını yeniden çalıştırabilirsiniz ve oluşturduğunuz hesapla oturum açabilmelisiniz. Döndürülen belirteç, ülke/bölge, ad ve posta kodu ' nu seçtiğiniz talepleri içerir.

## <a name="create-a-profile-editing-user-flow"></a>Profil düzenlemesi Kullanıcı akışı oluşturma

Kullanıcıların uygulamanızdaki profilini düzenlemesini etkinleştirmek istiyorsanız, bir profil düzenleme Kullanıcı akışı kullanın.

1. Azure AD B2C kiracının Genel Bakış sayfasının sol menüsünde Kullanıcı akışları ' nı **(ilkeler)** seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.
1. Önerilen sekmede **profil düzenlemesi** Kullanıcı akışı ' nı seçin.
1. Kullanıcı akışı için bir **ad** girin. Örneğin, *profileediting1*.
1. **Kimlik sağlayıcıları**Için **yerel hesap oturumu açma**' yı seçin.
1. **Kullanıcı öznitelikleri**için, müşterinin profilinde düzenleyebilmesini istediğiniz öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve **görünen ad** ve **iş unvanı**için her iki özniteliği ve talebi seçin. **Tamam**'ı tıklatın.
1. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. Bir *B2C_1* öneki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama**için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi `https://jwt.ms`gerekir.
1. **Kullanıcı akışını Çalıştır**' a tıklayın ve daha önce oluşturduğunuz hesapla oturum açın.
1. Artık Kullanıcı için görünen adı ve iş başlığını değiştirme fırsatına sahipsiniz. **Devam**'a tıklayın. Belirteç öğesine `https://jwt.ms` döner ve size gösterilmesi gerekir.

## <a name="create-a-password-reset-user-flow"></a>Parola sıfırlama Kullanıcı akışı oluşturma

Uygulamanızın kullanıcılarının parolasını sıfırlamasına olanak tanımak için, parola sıfırlama kullanıcı akışını kullanırsınız.

1. Sol menüden **Kullanıcı akışları (ilkeler)** öğesini seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.
1. Önerilen sekmede **parola sıfırlama** Kullanıcı akışını seçin.
1. Kullanıcı akışı için bir **ad** girin. Örneğin, *passwordreset1*.
1. **Kimlik sağlayıcıları**için **e-posta adresini kullanarak parolayı sıfırlamayı**etkinleştirin.
1. Uygulama talepleri bölümünde **daha fazla göster** ' e tıklayın ve uygulamanıza geri gönderilen yetkilendirme belirteçlerinde döndürülmesini istediğiniz talepleri seçin. Örneğin, **Kullanıcının Nesne Kimliği**’ni seçin.
1. **Tamam**'ı tıklatın.
1. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. Bir *B2C_1* öneki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama**için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi `https://jwt.ms`gerekir.
1. **Kullanıcı akışını Çalıştır**' a tıklayın, daha önce oluşturduğunuz hesabın e-posta adresini doğrulayın ve **devam**' ı seçin.
1. Artık Kullanıcı parolasını değiştirme fırsatına sahipsiniz. Parolayı değiştirin ve **devam**' ı seçin. Belirteç öğesine `https://jwt.ms` döner ve size gösterilmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Kaydolma ve oturum açma Kullanıcı akışı oluşturma
> * Profil düzenlemesi Kullanıcı akışı oluşturma
> * Parola sıfırlama Kullanıcı akışı oluşturma

Daha sonra, Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft veya Twitter gibi sağlayıcılardan Kullanıcı oturum açmasını etkinleştirmek için uygulamalarınıza kimlik sağlayıcıları ekleme hakkında bilgi edinin.

> [!div class="nextstepaction"]
> [Uygulamalarınıza kimlik sağlayıcıları ekleyin >](tutorial-add-identity-providers.md)