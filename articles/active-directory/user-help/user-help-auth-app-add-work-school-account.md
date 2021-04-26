---
title: Microsoft Authenticator uygulamasına iş veya okul hesabı ekleme-Azure AD
description: İki öğeli doğrulama kullanırken kimliğinizi doğrulamak için Microsoft Authenticator uygulamasına iş veya okul hesabınızı ekleyin.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 04c9bc429d9663f7ac36b6ba8f40abf225eb71c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359124"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamasına iş veya okul hesabınızı ekleyin

Kuruluşunuz iki öğeli doğrulama kullanıyorsa, iş veya okul hesabınızı doğrulama yöntemlerinden biri olarak Microsoft Authenticator uygulamayı kullanacak şekilde ayarlayabilirsiniz.

>[!Important]
>Hesabınızı ekleyebilmeniz için önce Microsoft Authenticator uygulamasını indirmeniz ve kurmanız gerekir. Henüz yapmadıysanız, [uygulamayı indirme ve yükleme](user-help-auth-app-download-install.md) makalesindeki adımları izleyin.

## <a name="add-your-work-or-school-account"></a>İş veya okul hesabınızı ekleyin

Aşağıdakilerden birini yaparak iş veya okul hesabınızı Microsoft Authenticator uygulamasına ekleyebilirsiniz:

- İş veya okul hesabınızla oturum açın kimlik bilgileri (Önizleme)
- QR kodunu tarama

### <a name="sign-in-with-your-credentials"></a>Kimlik bilgilerinizle oturum açın

>[!Note]
>Bu özellik yalnızca yöneticileri BT 'nin kimlik doğrulayıcı uygulamasını kullanarak telefonla oturum açmayı etkin olan kullanıcılar tarafından kullanılabilir.

Kimlik bilgilerinizi kullanarak iş veya okul hesabınızda oturum açarak bir hesap eklemek için:

1. Microsoft Authenticator uygulamasını açın ve **+** düğmeyi seçip **iş veya okul hesabı ekle**' ye dokunun. **Oturum aç**'ı seçin.

1. İş veya okul hesabınızın kimlik bilgilerini girin. Geçici bir erişim geçişinizdeki (dokunarak) oturum açmak için bunu kullanabilirsiniz. Bu noktada, muhtemelen aşağıdaki koşullardan biri ile devam etmeniz engellenebilir:

   - Hesabınız üzerinde güçlü bir kimlik doğrulama belirteci almak için yeterli kimlik doğrulama yöntemine sahip değilseniz, hesap ekleme işlemine devam edemezsiniz.

   - İletiyi alırsanız `You might be signing in from a location that is restricted by your admin` , engellenir ve [güvenlik bilgilerinde](https://mysignins.microsoft.com/security-info)sizi engellemeyi kaldırmanız gerekir.

   - Yönetici tarafından kimlik doğrulayıcı uygulamasını kullanarak telefon oturumu açma izniniz engellenmemişse, passwordless telefon oturum açma ve Azure Multi-Factor Authentication (MFA) için kurulum için cihaz kaydı ' na gidebilirsiniz.

1. Bu noktada, uygulamada şirket içi Multi-Factor Authentication hesabı ayarlamak için kuruluşunuz tarafından sunulan bir QR kodunu taramak isteyip istemediğiniz sorulabilir. Bunu yalnızca kuruluşunuz şirket içi MFA sunucusu kullanıyorsa yapmanız gerekir.

1. Cihazınızda hesaba dokunun ve tam ekran görünümünde hesabınızın doğru olduğunu ve altı basamaklı bir doğrulama kodu olduğunu doğrulayın. Ek güvenlik için, doğrulama kodu her 30 saniyede bir kullanıcının kodu birden çok kez kullanmasını önler.

## <a name="sign-in-with-a-qr-code"></a>QR kodu ile oturum açın

Bir QR kodunu tarayarak bir hesap eklemek için aşağıdakileri yapın:

1. Bilgisayarınızda **ek güvenlik doğrulama** sayfasına gidin.

   >[!Note]
   >**Ek güvenlik doğrulama** sayfasını görmüyorsanız, yöneticiniz güvenlik bilgileri (Önizleme) deneyimini açmış olabilir. Bu durumda, [bir Authenticator uygulaması kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-auth-app.md) bölümündeki yönergeleri izlemelisiniz. Böyle bir durum söz konusu değilse, yardım almak için kuruluşunuzun yardım masasına başvurmanız gerekir. Güvenlik bilgileri hakkında daha fazla bilgi için bkz. [oturum açma Isteminden güvenlik bilgilerinizi ayarlama](security-info-setup-signin.md).

1. Authenticator uygulaması ' nın yanındaki onay kutusunu işaretleyin ve ardından **Yapılandır**' ı seçin. **Mobil uygulamayı Yapılandır** sayfası görünür.

   ![QR kodu sağlayan ekran](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Microsoft Authenticator uygulamasını açın, artı simgesini seçin, ![ iOS veya Android cihazlarda artı simgesini seçin ](media/user-help-auth-app-add-work-school-account/plus-icon.png) ve **Hesap Ekle**' yi seçin, sonra da **iş veya okul hesabı '** nı seçin ve ardından **QR kodunu tarayın**.
   Kimlik doğrulayıcı uygulamasında ayarlanmış bir hesabınız yoksa **Hesap Ekle**' yi belirten büyük mavi bir düğme görürsünüz.

Bir QR kodunu taramak için kameranızı kullanmanız istenmezse, telefonunuzun ayarlarınızda, kimlik doğrulayıcı uygulamasının telefon kameraya erişimi olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- Hesaplarınızı uygulamaya ekledikten sonra, cihazınızda kimlik doğrulayıcı uygulamasını kullanarak oturum açabilirsiniz. Daha fazla bilgi için bkz. [uygulamayı kullanarak oturum açma](user-help-auth-app-sign-in.md).

- İOS çalıştıran cihazlarda, hesap kimlik bilgilerinizi ve hesaplarınızın sırası gibi ilgili uygulama ayarlarınızı buluta da yedekleyebilirsiniz. Daha fazla bilgi için bkz. [Microsoft Authenticator App Ile yedekleme ve kurtarma](user-help-auth-app-backup-recovery.md).
