---
title: Bir kimlik doğrulayıcı uygulamasına iş veya okul hesabı ekleme-Azure AD
description: İki öğeli doğrulama için iş veya okul hesabınızı Microsoft Authenticator uygulamasına ekleme.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c9fa41a1278f0e1e54a06c3930e3bbc3d3d3ae
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803730"
---
# <a name="add-your-work-or-school-account"></a>İş veya okul hesabınızı ekleyin

Kuruluşunuz iki öğeli doğrulama kullanıyorsa, iş veya okul hesabınızı doğrulama yöntemlerinden biri olarak Microsoft Authenticator uygulamayı kullanacak şekilde ayarlayabilirsiniz.

>[!Important]
>Hesabınızı ekleyebilmeniz için önce Microsoft Authenticator uygulamasını indirmeniz ve kurmanız gerekir. Henüz yapmadıysanız, [uygulamayı indirme ve yükleme](user-help-auth-app-download-install.md) makalesindeki adımları izleyin.

## <a name="add-your-work-or-school-account"></a>İş veya okul hesabınızı ekleyin

1. Bilgisayarınızda [ek güvenlik doğrulama](https://aka.ms/mfasetup) sayfasına gidin.

    >[!Note]
    >**Ek güvenlik doğrulama** sayfasını görmüyorsanız, yöneticiniz güvenlik bilgileri (Önizleme) deneyimini açmış olabilir. Bu durumda, [bir Authenticator uygulaması kullanmak için güvenlik bilgilerini ayarlama](security-info-setup-auth-app.md) bölümündeki yönergeleri izlemelisiniz. Böyle bir durum söz konusu değilse, yardım almak için kuruluşunuzun yardım masasına başvurmanız gerekir. Güvenlik bilgileri hakkında daha fazla bilgi için bkz. [güvenlik bilgileri (Önizleme) genel bakış](user-help-security-info-overview.md).

2. **Authenticator uygulaması**' nın yanındaki kutuyu işaretleyin ve ardından **Yapılandır**' ı seçin.

    **Mobil uygulamayı Yapılandır** sayfası görünür.

    ![QR kodu sağlayan ekran](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Microsoft Authenticator uygulamasını açın, sağ üst köşedeki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve sonra **iş veya okul hesabı**' nı seçin.

    >[!Note]
    >Microsoft Authenticator uygulamasını ilk kez ayarlıyorsanız, uygulamanın kameranıza erişmesine izin verip vermeyeceğinizi (iOS) veya uygulamanın resim almasına ve video (Android) kaydetmesine izin verip vermeyeceğinizi soran bir istem alabilirsiniz. Bir sonraki adımda QR kodunun bir resmini almak için, kimlik doğrulayıcı uygulamasının kameranıza erişebilmesi için **Izin ver** ' i seçmeniz gerekir. Kameraya izin vermezseniz, kimlik doğrulayıcı uygulamasını ayarlamaya devam edebilirsiniz, ancak kod bilgilerini el ile eklemeniz gerekir. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

4. Bilgisayarınızda **mobil uygulama yapılandırma** ekranından QR kodunu taramak için cihazınızın kamerayı kullanın ve ardından **bitti**' yi seçin.

    >[!Note]
    >Kameranız QR kodunu yakalayamaz, iki öğeli doğrulama için hesap bilgilerinizi Microsoft Authenticator uygulamasına el ile ekleyebilirsiniz. Daha fazla bilgi ve nasıl yapılacağı hakkında daha fazla bilgi için, bkz. [hesabınızı el ile ekleme](user-help-auth-app-add-account-manual.md).

5. Hesabınızın doğru olduğundan ve altı basamaklı bir doğrulama kodu olduğundan emin olmak için cihazınızdaki uygulamanın **hesaplar** ekranını gözden geçirin. Ek güvenlik için, doğrulama kodu her 30 saniyede bir kullanıcının kodu birden çok kez kullanmasını önler.

    ![Hesaplar ekranı](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Sonraki adımlar

- Hesaplarınızı uygulamaya ekledikten sonra, cihazınızda kimlik doğrulayıcı uygulamasını kullanarak oturum açabilirsiniz. Daha fazla bilgi için bkz. [uygulamayı kullanarak oturum açma](user-help-auth-app-sign-in.md).

- İOS çalıştıran cihazlarda, hesap kimlik bilgilerinizi ve hesaplarınızın sırası gibi ilgili uygulama ayarlarınızı buluta da yedekleyebilirsiniz. Daha fazla bilgi için bkz. [Microsoft Authenticator App Ile yedekleme ve kurtarma](user-help-auth-app-backup-recovery.md).
