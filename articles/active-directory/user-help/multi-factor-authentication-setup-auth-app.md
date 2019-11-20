---
title: İki öğeli doğrulama yöntemi olarak bir Authenticator uygulaması ayarlama-Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator uygulamasını iki öğeli doğrulama yöntemi olarak ayarlamayı öğrenin.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3a09ccdc867cb6d0e84a6e0f85e532839604f5e
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619442"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>İki öğeli doğrulama yöntemi olarak bir Authenticator uygulaması ayarlama

Bir kimlik doğrulayıcı uygulamasını, mobil cihazınıza bildirim göndermek veya güvenlik doğrulama yönteminiz olarak bir doğrulama kodu göndermek için ayarlayabilirsiniz. Microsoft Authenticator uygulamasını kullanmanız gerekmez ve ayarlama işlemi sırasında farklı bir uygulama seçebilirsiniz. Ancak, bu makale Microsoft Authenticator uygulamasını kullanır.

>[!Important]
>Hesabınızı ekleyebilmeniz için önce Microsoft Authenticator uygulamasını indirmeniz ve kurmanız gerekir. Henüz yapmadıysanız, [uygulamayı indirme ve yükleme](user-help-auth-app-download-install.md) makalesindeki adımları izleyin.

>[!Note]
> Mobil uygulama seçeneği gri ise, kuruluşunuz doğrulama için bir kimlik doğrulama uygulaması kullanmanıza izin vermiyor olabilir. Bu durumda, daha fazla yardım için başka bir yöntem seçmeniz veya yöneticinize başvurmanız gerekir.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Bildirimleri göndermek için Microsoft Authenticator uygulamasını ayarlama

1. **Ek güvenlik doğrulaması** sayfasında, **adım 1 ' den **mobil uygulama** ' yı seçin: Sizinle** sizinle iletişim kuruyoruz.

2. **Mobil uygulama alanını nasıl kullanmak istediğinizi** **doğrulamak için bildirimleri al** ' ı seçin ve ardından **Ayarla**' yı seçin.

    ![Ek güvenlik doğrulama sayfası, mobil uygulama ve bildirimler seçeneği](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    **Mobil uygulamayı Yapılandır** sayfası görünür.

    ![QR kodu sağlayan ekran](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Microsoft Authenticator uygulamasını açın, sağ üst köşedeki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve sonra **iş veya okul hesabı**' nı seçin.

    >[!Note]
    >Microsoft Authenticator uygulamasını ilk kez ayarlıyorsanız, uygulamanın kameranıza erişmesine izin verip vermeyeceğinizi (iOS) veya uygulamanın resim almasına ve video (Android) kaydetmesine izin verip vermeyeceğinizi soran bir istem alabilirsiniz. Bir sonraki adımda QR kodunun bir resmini almak için, kimlik doğrulayıcı uygulamasının kameranıza erişebilmesi için **Izin ver** ' i seçmeniz gerekir. Kameraya izin vermezseniz, kimlik doğrulayıcı uygulamasını ayarlamaya devam edebilirsiniz, ancak kod bilgilerini el ile eklemeniz gerekir. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

4. Bilgisayarınızda **mobil uygulama yapılandırma** ekranından QR kodunu taramak için cihazınızın kamerayı kullanın ve ardından **İleri**' yi seçin.

5. Bilgisayarınıza ve **ek güvenlik doğrulama** sayfasına dönüp yapılandırmanızın başarılı olduğunu belirten iletiyi aldığınızdan emin olun ve ardından **İleri**' yi seçin.

    ![Ek güvenlik doğrulama sayfası, başarılı ileti](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    Kimlik doğrulayıcı uygulaması, mobil cihazınıza test olarak bir bildirim gönderir.

6. Mobil cihazınızda **Onayla**' yı seçin.

7. Bilgisayarınızda, cep telefonu numaranızı **3. adıma ekleyin: Mobil uygulama** alanına erişiminizi kaybetmeniz durumunda **İleri**' yi seçin.

    Mobil cihaz telefon numaranızı, herhangi bir nedenden dolayı bir mobil uygulamaya erişemeyecek veya bu uygulama için kullanabileceğiniz bir yedekleme görevi görecek şekilde eklemeyi kesinlikle öneririz.

8. **Adım 4: Mevcut uygulamalar** alanınızı kullanmaya devam edin, belirtilen uygulama parolasını kopyalayın ve güvenli bir yere yapıştırın.

    ![Ek güvenlik doğrulama sayfasının uygulama parolaları alanı](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Uygulama parolasının eski uygulamalarınızla nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [uygulama parolalarını yönetme](multi-factor-authentication-end-user-app-passwords.md). Yalnızca iki öğeli doğrulamayı desteklemeyen eski uygulamaları kullanmaya devam ediyorsanız uygulama parolalarını kullanmanız gerekir.

9. **Done** (Bitti) öğesini seçin.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Microsoft Authenticator uygulamasını doğrulama kodlarını kullanacak şekilde ayarlama

1. **Ek güvenlik doğrulaması** sayfasında, **adım 1 ' den **mobil uygulama** ' yı seçin: Sizinle** sizinle iletişim kuruyoruz.

2. **Mobil uygulama alanını nasıl kullanmak** istiyorsunuz listesinden **doğrulama kodunu kullan** ' ı seçin ve ardından **Ayarla**' yı seçin.

    ![Ek güvenlik doğrulama sayfası, mobil uygulama ve bildirimler seçeneği](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    **Mobil uygulamayı Yapılandır** sayfası görünür.

    ![QR kodu sağlayan ekran](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Microsoft Authenticator uygulamasını açın, sağ üst köşedeki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve sonra **iş veya okul hesabı**' nı seçin.

    >[!Note]
    >Microsoft Authenticator uygulamasını ilk kez ayarlıyorsanız, uygulamanın kameranıza erişmesine izin verip vermeyeceğinizi (iOS) veya uygulamanın resim almasına ve video (Android) kaydetmesine izin verip vermeyeceğinizi soran bir istem alabilirsiniz. Bir sonraki adımda QR kodunun bir resmini almak için, kimlik doğrulayıcı uygulamasının kameranıza erişebilmesi için **Izin ver** ' i seçmeniz gerekir. Kameraya izin vermezseniz, kimlik doğrulayıcı uygulamasını ayarlamaya devam edebilirsiniz, ancak kod bilgilerini el ile eklemeniz gerekir. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

4. Bilgisayarınızda **mobil uygulama yapılandırma** ekranından QR kodunu taramak için cihazınızın kamerayı kullanın ve ardından **İleri**' yi seçin.

5. Bilgisayarınıza ve **ek güvenlik doğrulama** sayfasına dönüp yapılandırmanızın başarılı olduğunu belirten iletiyi aldığınızdan emin olun ve ardından **İleri**' yi seçin.

    ![Ek güvenlik doğrulama sayfası, başarılı ileti](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    Kimlik doğrulayıcı uygulaması, test olarak bir doğrulama kodu ister.

6. Microsoft Authenticator uygulamadan iş veya okul hesabınıza gidin, 6 basamaklı kodu **kopyalayın ve 2. adıma yapıştırın: Bilgisayarınızdaki mobil uygulama** kutusundan doğrulama kodunu girin ve ardından **Doğrula**' yı seçin.

    ![Doğrulama kodu testi ile ek güvenlik doğrulama sayfası](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. Bilgisayarınızda, cep telefonu numaranızı **3. adıma ekleyin: Mobil uygulama** alanına erişiminizi kaybetmeniz durumunda **İleri**' yi seçin.

    Mobil cihaz telefon numaranızı, herhangi bir nedenden dolayı bir mobil uygulamaya erişemeyecek veya bu uygulama için kullanabileceğiniz bir yedekleme görevi görecek şekilde eklemeyi kesinlikle öneririz.

8. **Adım 4: Mevcut uygulamalar** alanınızı kullanmaya devam edin, belirtilen uygulama parolasını kopyalayın ve güvenli bir yere yapıştırın.

    ![Ek güvenlik doğrulama sayfasının uygulama parolaları alanı](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >Uygulama parolasının eski uygulamalarınızla nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [uygulama parolalarını yönetme](multi-factor-authentication-end-user-app-passwords.md). Yalnızca iki öğeli doğrulamayı desteklemeyen eski uygulamaları kullanmaya devam ediyorsanız uygulama parolalarını kullanmanız gerekir.

9. **Done** (Bitti) öğesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

İki öğeli doğrulama yönteminizi ayarladıktan sonra, başka yöntemler ekleyebilir, ayarlarınızı ve uygulama parolalarınızı yönetebilir, oturum açabilir veya bazı yaygın iki öğeli doğrulamayla ilgili bazı sorunlarla ilgili yardım alabilirsiniz.

- [İki öğeli doğrulama yöntemi ayarlarınızı yönetin](multi-factor-authentication-end-user-manage-settings.md)

- [Uygulama parolalarını yönetme](multi-factor-authentication-end-user-app-passwords.md)

- [İki öğeli doğrulama kullanarak oturum açın](multi-factor-authentication-end-user-signin.md)

- [İki öğeli doğrulamayla ilgili yardım alın](multi-factor-authentication-end-user-troubleshoot.md)
