---
title: Bir Authenticator uygulamasına Microsoft hesabı olmayan bir uygulamaya ekleme-Azure AD
description: Google, Facebook veya GitHub gibi Microsoft dışı hesaplarınızı iki öğeli doğrulama için Microsoft Authenticator uygulamasına ekleme.
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
ms.openlocfilehash: 62d866e9bf543577887ee99173a52202fcfec632
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73803674"
---
# <a name="add-your-non-microsoft-accounts"></a>Microsoft dışı hesaplarınızı ekleyin

Google, Facebook veya GitHub gibi Microsoft dışı hesaplarınızı iki öğeli doğrulama için Microsoft Authenticator uygulamasına ekleyin. Microsoft Authenticator uygulaması, iki öğeli doğrulama kullanan tüm uygulamalar ve zaman tabanlı bir kerelik parola (TOTP) standartlarını destekleyen herhangi bir hesap ile birlikte kullanılabilir.

>[!Important]
>Hesabınızı ekleyebilmeniz için önce Microsoft Authenticator uygulamasını indirmeniz ve kurmanız gerekir. Henüz yapmadıysanız, [uygulamayı indirme ve yükleme](user-help-auth-app-download-install.md) makalesindeki adımları izleyin.

## <a name="add-personal-accounts"></a>Kişisel hesaplar ekleme

Genellikle, tüm kişisel hesaplarınız için şunları yapmanız gerekir:

1. Hesabınızda oturum açın ve cihazınızı ya da bilgisayarınızı kullanarak iki öğeli doğrulamayı açın.

2. Microsoft Authenticator uygulamasına hesabı ekleyin. Bu işlemin bir parçası olarak bir QR kodu taramak isteyip istemediğiniz sorulabilir.

    >[!Note]
    >Microsoft Authenticator uygulamasını ilk kez ayarlıyorsanız, uygulamanın kameranıza erişmesine izin verip vermeyeceğinizi (iOS) veya uygulamanın resim almasına ve video (Android) kaydetmesine izin verip vermeyeceğinizi soran bir istem alabilirsiniz. Bir sonraki adımda QR kodunun bir resmini almak için, kimlik doğrulayıcı uygulamasının kameranıza erişebilmesi için **Izin ver** ' i seçmeniz gerekir. Kameraya izin vermezseniz, kimlik doğrulayıcı uygulamasını ayarlamaya devam edebilirsiniz, ancak kod bilgilerini el ile eklemeniz gerekir. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

Facebook, Google, GitHub ve Amazon hesaplarınız için bu işlemi burada sunuyoruz, ancak bu işlem, sistem durumu, Netflix veya Adobe gibi diğer uygulamalar için de aynıdır.

## <a name="add-your-google-account"></a>Google hesabınızı ekleyin

İki öğeli doğrulamayı açıp daha sonra hesabı uygulamaya ekleyerek Google hesabınızı ekleyin.

### <a name="turn-on-two-factor-verification"></a>İki öğeli doğrulamayı açma

1. Bilgisayarınızda https://myaccount.google.com/signinoptions/two-step-verification/enroll-welcome' a gidin, **kullanmaya**başlayın ' ı seçin ve sonra kimliğinizi doğrulayın.

2. Kişisel Google hesabınız için iki aşamalı doğrulamayı açmak üzere sayfa üzerindeki adımları izleyin.

### <a name="add-your-google-account-to-the-app"></a>Google hesabınızı uygulamaya ekleyin

1. Bilgisayarınızdaki Google sayfasında, **Alternatif ikinci ikinci adımı ayarlama** bölümüne gidin, **Authenticator uygulaması** bölümünden **Ayarla** ' yı seçin.

2. **Doğrulayıcı uygulamasındaki kodları al** sayfasında, telefon türüne göre **Android** veya **iPhone** ' ı seçin ve ardından **İleri**' yi seçin.

    Hesabınızı Microsoft Authenticator uygulamayla otomatik olarak ilişkilendirmek için kullanabileceğiniz bir QR kodu vermiş olursunuz. Bu pencereyi kapatmayın.

3. Microsoft Authenticator uygulamasını açın, sağ üstteki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve ardından **diğer hesap (Google, Facebook vb.)** seçeneğini belirleyin.

4. Bilgisayarınızdaki **kimlik doğrulayıcı ayarla** sayfasından QR kodunu taramak için cihazınızın kamerasını kullanın.

    >[!Note]
    >Kameranız düzgün çalışmıyorsa, QR kodunu ve URL 'YI el ile girebilirsiniz.

5. Hesap bilgilerinizin doğru olduğundan ve altı basamaklı bir doğrulama kodu olduğundan emin olmak için cihazınızdaki Microsoft Authenticator uygulamasının **hesaplar** sayfasını gözden geçirin.

    Ek güvenlik için, doğrulama kodu her 30 saniyede bir kullanıcının kodu birden çok kez kullanmasını önler.

6. Bilgisayarınızdaki **kimlik doğrulayıcı 'Yı ayarla** sayfasında **İleri** ' yi seçin, Google hesabınız için uygulamada belirtilen altı basamaklı doğrulama kodunu yazın ve ardından **Doğrula**' yı seçin.

7. Hesabınız doğrulanır ve **kimlik doğrulayıcı 'Yı ayarla** sayfasını kapatmak için **bitti** ' yi seçebilirsiniz.

    >[!NOTE]
    >İki öğeli doğrulama ve Google hesabınız hakkında daha fazla bilgi için bkz. [2 basamaklı doğrulamayı etkinleştirme](https://support.google.com/accounts/answer/185839) ve [2 adımlı doğrulama hakkında daha fazla bilgi edinin](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Facebook hesabınızı ekleyin

İki öğeli doğrulamayı açıp daha sonra hesabı uygulamaya ekleyerek Facebook hesabınızı ekleyin.

### <a name="turn-on-two-factor-verification"></a>İki öğeli doğrulamayı açma

1. Bilgisayarınızda Facebook ' u açın, sağ üst köşedeki açılan menüyü seçin ve ardından **ayarlar** > **güvenlik ve oturum açma**' ya gidin.

    **Güvenlik ve oturum açma** sayfası görüntülenir.

2. **Iki öğeli kimlik doğrulama** bölümünde **iki öğeli kimlik doğrulaması kullan** seçeneğine gidin ve ardından **Düzenle**' yi seçin.

    **Iki öğeli kimlik doğrulama** sayfası görüntülenir.

3. **Aç '** ı seçin.

### <a name="add-your-facebook-account-to-the-app"></a>Facebook hesabınızı uygulamaya ekleyin

1. Bilgisayarınızdaki Facebook sayfasında, **yedekleme ekleme** bölümüne gidin ve ardından **kimlik doğrulama uygulaması** alanından **Kurulum** ' u seçin.

    Hesabınızı Microsoft Authenticator uygulamayla otomatik olarak ilişkilendirmek için kullanabileceğiniz bir QR kodu vermiş olursunuz. Bu pencereyi kapatmayın.

2. Microsoft Authenticator uygulamasını açın, sağ üstteki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve ardından **diğer hesap (Google, Facebook vb.)** seçeneğini belirleyin.

3. Bilgisayarınızdaki **iki öğeli kimlik doğrulama** sayfasından QR kodunu taramak için cihazınızın kamerasını kullanın.

    >[!Note]
    >Kameranız düzgün çalışmıyorsa, QR kodunu ve URL 'YI el ile girebilirsiniz.

4. Hesap bilgilerinizin doğru olduğundan ve altı basamaklı bir doğrulama kodu olduğundan emin olmak için cihazınızdaki Microsoft Authenticator uygulamasının **hesaplar** sayfasını gözden geçirin.

    Ek güvenlik için, doğrulama kodu her 30 saniyede bir kullanıcının kodu birden çok kez kullanmasını önler.

5. Bilgisayarınızda **iki öğeli kimlik doğrulama** sayfasında **İleri** ' yi seçin ve ardından Facebook hesabınız için uygulamada belirtilen altı basamaklı doğrulama kodunu yazın.

    Hesabınız doğrulanır ve artık hesabınızı doğrulamak için uygulamayı kullanabilirsiniz.

    >[!NOTE]
    >İki öğeli doğrulama ve Facebook hesabınız hakkında daha fazla bilgi için bkz. [iki öğeli kimlik doğrulaması nedir ve nasıl çalışır?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-github-account"></a>GitHub hesabınızı ekleyin

İki öğeli doğrulamayı açıp hesabı uygulamaya ekleyerek GitHub hesabınızı ekleyin.

### <a name="turn-on-two-factor-verification"></a>İki öğeli doğrulamayı açma

1. Bilgisayarınızda GitHub ' ı açın, sağ üst köşeden görüntünüzü seçin ve ardından **Ayarlar**' ı seçin.

    **İki öğeli kimlik doğrulama** sayfası görüntülenir.

2. **Kişisel ayarlar** kenar çubuğundan **güvenlik** ' i seçin ve **iki öğeli kimlik** doğrulama alanından **iki öğeli kimlik doğrulamayı etkinleştir** ' i seçin.

### <a name="add-your-github-account-to-the-app"></a>GitHub hesabınızı uygulamaya ekleme

1. Bilgisayarınızdaki **iki öğeli kimlik doğrulama** sayfasında, **uygulama kullanarak ayarla**' yı seçin.

2. Erişiminizi kaybederseniz ve sonra **İleri**' yi seçerek hesabınıza geri dönmek için kurtarma kodlarınızı kaydedin. 

    Kodlarınızı cihazınıza indirerek, sabit bir kopya yazdırarak veya bir parola Yöneticisi aracına kopyalayarak kaydedebilirsiniz.

3. **İki öğeli kimlik doğrulama** sayfasında, **uygulama kullanarak ayarla**' yı seçin.

    Sayfa, bir QR kodu gösterecek şekilde değişir. Bu sayfayı kapatmayın.

4. Microsoft Authenticator uygulamasını açın, sağ üst köşedeki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin, **diğer hesap (Google, Facebook vb.)** öğesini seçin ve ardından şu **metin kodunu** sayfa.

    Microsoft Authenticator uygulama QR kodunu tarayamıyor, bu yüzden kodu el ile girmeniz gerekir.

5. Bir **Hesap adı** girin (örneğin, GitHub) ve 4. adımda **gizli anahtarı** yazın ve **son**' u seçin.

6. Bilgisayarınızdaki **iki öğeli kimlik doğrulayıcı** sayfasında, Github hesabınız için uygulamada belirtilen altı basamaklı doğrulama kodunu yazın ve **Etkinleştir**' i seçin.

    Uygulamanın **hesaplar** sayfasında hesap adınızı ve altı basamaklı bir doğrulama kodu görüntülenir. Ek güvenlik için, doğrulama kodu her 30 saniyede bir kullanıcının kodu birden çok kez kullanmasını önler.

    >[!NOTE]
    >İki öğeli doğrulama ve GitHub hesabınız hakkında daha fazla bilgi için bkz. [iki öğeli kimlik doğrulama hakkında](https://help.github.com/articles/about-two-factor-authentication/).

## <a name="add-your-amazon-account"></a>Amazon hesabınızı ekleyin

İki öğeli doğrulamayı açıp daha sonra hesabı uygulamaya ekleyerek Amazon hesabınızı ekleyin.

### <a name="turn-on-two-factor-verification"></a>İki öğeli doğrulamayı açma

1. Bilgisayarınızda, Amazon ' i açın, **hesap & listeleri** açılır menüsünü seçin ve ardından **Hesabınızı**seçin.

2. **Oturum aç & güvenlik**' i seçin, Amazon hesabınızda oturum açın ve ardından **Gelişmiş güvenlik ayarları** alanında **Düzenle** ' yi seçin.

    **Gelişmiş güvenlik ayarları** sayfası görüntülenir.

3. **Kullanmaya**başlayın ' ı seçin.

4. **Kodu nasıl alacağınızı** seçin sayfasında **Authenticator uygulaması** ' nı seçin.

    Sayfa, bir QR kodu gösterecek şekilde değişir. Bu sayfayı kapatmayın.

5. Microsoft Authenticator uygulamasını açın, sağ üstteki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve ardından **diğer hesap (Google, Facebook vb.)** seçeneğini belirleyin.

6. Bilgisayarınızda kod **alma yöntemini seçin** sayfasında QR kodunu taramak için cihazınızın kamerasını kullanın.

    >[!Note]
    >Kameranız düzgün çalışmıyorsa, QR kodunu ve URL 'YI el ile girebilirsiniz.

7. Hesap bilgilerinizin doğru olduğundan ve altı basamaklı bir doğrulama kodu olduğundan emin olmak için cihazınızdaki Microsoft Authenticator uygulamasının **hesaplar** sayfasını gözden geçirin.

    Ek güvenlik için, doğrulama kodu her 30 saniyede bir kullanıcının kodu birden çok kez kullanmasını önler.

8. Bilgisayarınızda **nasıl kod alacağınızı seçin** sayfasında, Amazon hesabınız için uygulamada belirtilen altı basamaklı doğrulama kodunu yazın ve ardından **kodu doğrula ve devam et**' i seçin.

9. Metin iletisi gibi bir yedekleme doğrulama yöntemi ekleme ve ardından **kodu gönder**' i içeren kaydolma işleminin geri kalanını tamamlayın.

10. Bilgisayarınızda **bir yedekleme doğrulama yöntemi ekleme** sayfasında, Amazon hesabınız için yedekleme doğrulama yönteminiz tarafından belirtilen altı basamaklı doğrulama kodunu yazın ve ardından **kodu doğrula ve devam et**' i seçin.

11. **Neredeyse bitti** sayfasında, bilgisayarınızı güvenilir bir cihaz yapıp yapmayacağınıza karar verin ve ardından alındı ' ı seçin **. Iki aşamalı doğrulamayı açın**.

    Güncelleştirilmiş iki öğeli doğrulama ayrıntılarınızın gösterildiği **Gelişmiş güvenlik ayarları** sayfası görüntülenir.

    >[!NOTE]
    >İki öğeli doğrulama ve Amazon hesabınız hakkında daha fazla bilgi için bkz. iki adımlı doğrulama hakkında ve [Iki adımlı doğrulamayla oturum açma](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440) [hakkında](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) .

## <a name="next-steps"></a>Sonraki adımlar

- Hesaplarınızı uygulamaya ekledikten sonra, cihazınızda kimlik doğrulayıcı uygulamasını kullanarak oturum açabilirsiniz. Daha fazla bilgi için bkz. [uygulamayı kullanarak oturum açma](user-help-auth-app-sign-in.md).

- İOS çalıştıran cihazlarda, hesap kimlik bilgilerinizi ve hesaplarınızın sırası gibi ilgili uygulama ayarlarınızı buluta da yedekleyebilirsiniz. Daha fazla bilgi için bkz. [Microsoft Authenticator App Ile yedekleme ve kurtarma](user-help-auth-app-backup-recovery.md).
