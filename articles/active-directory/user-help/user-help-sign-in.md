---
title: Kimlik doğrulama bilgilerinizi kullanarak oturum açın-Azure AD
description: Güvenlik bilgilerinde çeşitli kimlik doğrulama yöntemlerini kullanarak oturum açma hakkında bilgi edinin.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.custom: user-help
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c79e82680ae81137ee4164e88e62235f934565a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74028518"
---
# <a name="sign-in-using-two-step-verification-or-security-info"></a>İki adımlı doğrulama veya güvenlik bilgilerini kullanarak oturum açın

İki aşamalı doğrulamayı veya güvenlik bilgilerini ayarladıktan sonra, hesabınızda belirtilen kimlik doğrulama yönteminizi kullanarak oturum açabilirsiniz.

> [!Note]
> İki adımlı doğrulama deneyimini kullanmaya devam ediyorsanız, [iki adımlı doğrulama için hesabımı ayarlama](multi-factor-authentication-end-user-first-time.md) makalesindeki yönergeleri izleyerek kimlik doğrulama yöntemlerinizi ayarlamanız gerekir.
>
> Yöneticiniz güvenlik bilgileri deneyimini etkinleştirmiştir, bu adım adım makaleleri kullanarak kimlik doğrulama yöntemlerinizi ayarlamanız gerekir:<ul><li>[Kimlik doğrulama uygulaması kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-auth-app.md)</li><li>[Güvenlik bilgilerini SMS kullanacak şekilde ayarlama](security-info-setup-text-msg.md)</li><li>[Telefon araması kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-phone-number.md)</li><li>[Güvenlik anahtarı kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-security-key.md)</li></ul>

## <a name="sign-in-using-an-authenticator-app-notification-on-your-mobile-device"></a>Mobil cihazınızda bir Authenticator uygulama bildirimi kullanarak oturum açın

1. Kullanıcı adınızı ve parolanızı kullanarak hesabınızda oturum açın.

2. Mobil cihazınıza gönderilen onay bildiriminden **Onayla** ' yı seçin.

## <a name="sign-in-using-an-authenticator-app-code-on-your-mobile-device"></a>Mobil cihazınızda bir Authenticator uygulama kodu kullanarak oturum açın

1. Kullanıcı adınızı ve parolanızı kullanarak hesabınızda oturum açın.

2. Kimlik doğrulayıcı uygulamanızı açın ve hesap için rastgele oluşturulan kodu, **kodu gir** kutusuna yazın.

## <a name="sign-in-using-your-phone-number"></a>Telefon numaranızı kullanarak oturum açın

1. Kullanıcı adınızı ve parolanızı kullanarak hesabınızda oturum açın.

2. Telefonunuzu yanıtlayın ve yönergeleri izleyin.

## <a name="sign-in-using-a-text-message"></a>Kısa mesaj kullanarak oturum açın

1. Kullanıcı adınızı ve parolanızı kullanarak hesabınızda oturum açın.

2. Metin iletisini açın ve **kod girin** kutusuna metin iletinizde kodu yazın.

## <a name="sign-in-using-a-security-key-at-the-lock-screen"></a>Kilit ekranında bir güvenlik anahtarı kullanarak oturum açın

1. Güvenlik anahtarınızı kaydettikten sonra, Windows 10 kilit ekranından güvenlik anahtarı görüntüsünü seçin.

2. Cihazınızın USB bağlantı noktasına güvenlik anahtarınızı ekleyin ve güvenlik anahtarı PIN 'inizi kullanarak Windows 'da oturum açın.

    ![Windows 10 kilit ekranında güvenlik anahtarı oturumu açma](./media/security-info/security-info-windows-10-lock-screen-security-key.png)

## <a name="sign-in-using-a-security-key-and-the-microsoft-edge-browser"></a>Güvenlik anahtarı ve Microsoft Edge tarayıcısı kullanarak oturum açın

1. Güvenlik anahtarınızı kaydettikten sonra Microsoft Edge tarayıcısını açın.

2. Oturum açmanız istendiğinde, cihazınızın USB bağlantı noktasına güvenlik anahtarınızı ekleyin ve güvenlik anahtarı PIN 'inizi kullanarak Windows 'da oturum açın.

    ![Microsoft Edge tarayıcısını kullanarak güvenlik anahtarı oturumu açma](./media/security-info/security-info-edge-security-key.png)

    >[!NOTE]
    >Microsoft Authenticator uygulamasını kullanarak oturum açma hakkında daha fazla bilgi için, [Microsoft Authenticator uygulamasını kullanarak hesaplarınızda oturum açın](user-help-auth-app-sign-in.md)makalesini inceleyin.

## <a name="sign-in-using-another-verification-method"></a>Başka bir doğrulama yöntemi kullanarak oturum açın

Birincil oturum açma yönteminizi kullanmanızın bir nedeni varsa, daha önce bir daha önceden doğrulama yöntemi kullanabilirsiniz.

1. Hesabınızda normal olarak oturum açın ve ardından **iki adımlı doğrulama** sayfasında **başka bir şekilde oturum aç** bağlantısını seçin.

    ![Doğrulama yönteminde oturum açma değiştirme yöntemi](media/security-info/two-factor-auth-signin-another-way.png)

    >[!Note]
    >**Başka bir yolla oturum açma** bağlantısını görmüyorsanız, başka bir doğrulama yöntemi ayarlamadıysanız ve hesabınızda oturum açmak için yöneticinize başvurmanız gerekir. Yöneticiniz oturum açmanıza yardımcı olduktan sonra, ek doğrulama yöntemleri eklediğinizden emin olun. Doğrulama yöntemleri ekleme hakkında daha fazla bilgi için, [iki adımlı doğrulama için ayarlarınızı yönetme](multi-factor-authentication-end-user-manage-settings.md) makalesine bakın.
    >
    >**Oturum açmayı başka bir şekilde** görürseniz, ancak yine de başka bir doğrulama yöntemi görmüyorsanız, hesabınızda oturum açmaya yardım etmek için yöneticinize başvurmanız gerekir.

2. Alternatif doğrulama yönteminizi seçin ve iki adımlı doğrulama işlemiyle devam edin.

3. Hesabınıza geri döndüğünüzde doğrulama yöntemlerinizi güncelleştirebilirsiniz (gerekirse). Yöntemlerinizi ekleme veya değiştirme hakkında daha fazla bilgi için, [iki adımlı doğrulama için ayarlarınızı yönetme](multi-factor-authentication-end-user-manage-settings.md) makalesine bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik [bilgileri (Önizleme) genel bakış](user-help-security-info-overview.md) makalesindeki güvenlik bilgileri hakkında bilgi edinin.

- İki [adımlı doğrulamaya genel bakış](user-help-two-step-verification-overview.md) makalesinde iki adımlı doğrulama hakkında bilgi edinin.

- Parola [sıfırlama portalından](https://passwordreset.microsoftonline.com/) , kaybedildiyseniz veya unuttuysanız parolanızı sıfırlayın

- [Microsoft hesabı makalesinde oturum açma](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) sorunları için sorun giderme ipuçları ve yardım alın.
