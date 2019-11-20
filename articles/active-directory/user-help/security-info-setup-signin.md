---
title: Oturum açma isteminden güvenlik bilgilerini ayarlama (Önizleme)-Azure AD
description: Kuruluşunuzun oturum açma sayfasından istenirse iş veya okul hesabınız için güvenlik bilgilerini ayarlama.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: overview
ms.date: 08/05/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ae1856c7934c658bd244445306b4bb78c666e38
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820295"
---
# <a name="set-up-your-security-info-preview-from-the-sign-in-page-prompt"></a>Oturum açma sayfası isteminden güvenlik bilgilerinizi (Önizleme) ayarlama

İş veya okul hesabınızda oturum açtıktan hemen sonra güvenlik bilgilerinizi ayarlamanız istenirse, bu adımları izleyebilirsiniz.

Bu istemi yalnızca kuruluşunuz için gereken güvenlik bilgilerini ayarlamadıysanız görürsünüz. Daha önce güvenlik bilgilerinizi ayarladıysanız ancak değişiklik yapmak istiyorsanız, çeşitli Yöntem tabanlı nasıl yapılır makalelerindeki adımları izleyebilirsiniz. Daha fazla bilgi için bkz. [güvenlik bilgilerine genel bakış ekleme veya güncelleştirme](security-info-add-update-methods-overview.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="sign-in-to-your-work-or-school-account"></a>İş veya okul hesabınızda oturum açın

İş veya okul hesabınızda oturum açtıktan sonra hesabınıza erişmenize izin vermeden önce daha fazla bilgi sağlamanızı isteyen bir istem görürsünüz.

![Daha fazla bilgi istesin](media/security-info/securityinfo-prompt.png)

## <a name="set-up-your-security-info-using-the-wizard"></a>Sihirbazı kullanarak güvenlik bilgilerinizi ayarlama

İş veya okul hesabınızla ilgili güvenlik bilgilerinizi istemden ayarlamak için bu adımları izleyin.

>[!Important]
>Bu işlem yalnızca bir örnektir. Kuruluşunuzun gereksinimlerine bağlı olarak, yöneticiniz bu işlem sırasında ayarlamanız gereken farklı doğrulama yöntemleri ayarlamış olabilir. Bu örnekte, Microsoft Authenticator uygulaması ve doğrulama çağrıları veya SMS iletileri için bir cep telefonu numarası olmak üzere iki yöntem gerekli.

1. İstemde **İleri** ' yi seçtikten sonra, yönetici ve kuruluşunuzun ayarlamanız gereken ilk yöntemi gösteren **Hesabınızı güvenli tut Sihirbazı** görüntülenir. Bu örnek için Microsoft Authenticator uygulamasıdır.

   > [!Note]
   > Microsoft Authenticator uygulaması dışında bir Authenticator uygulaması kullanmak istiyorsanız, **farklı bir Authenticator uygulama bağlantısı kullanmak istiyorum** bağlantısını seçin.
   >
   > Kuruluşunuz, doğrulayıcı uygulamasının yanı sıra farklı bir yöntem seçmenizi sağlar, **farklı bir yöntem bağlantısı kurmak istiyorum**' u seçebilirsiniz.

    ![Kimlik doğrulama uygulaması indirme sayfasını gösteren hesap güvenli Sihirbazı 'nı koruyun](media/security-info/securityinfo-prompt-get-auth-app.png)

2. Mobil cihazınıza Microsoft Authenticator uygulamayı indirip yüklemek için **Şimdi İndir** ' i seçin ve ardından **İleri**' yi seçin. Uygulamanın nasıl indirileceği ve yükleneceği hakkında daha fazla bilgi için, bkz. [Microsoft Authenticator uygulamasını indirme ve yükleme](user-help-auth-app-download-install.md).

    ![Hesap sayfanızı ayarlama kimlik doğrulamasını gösteren hesap güvenli Sihirbazı 'nı koruyun](media/security-info/securityinfo-prompt-auth-app-setup-acct.png)

3. Mobil cihazınızda Microsoft Authenticator uygulamayı ayarlarken **hesabınızı ayarlama** sayfasında kalır.

4. Microsoft Authenticator uygulamasını açın, bildirimlere izin ver ' i seçin (istenirse), sağ üst köşedeki **Özelleştir ve denetim** simgesinden **Hesap Ekle** ' yi seçin ve sonra **iş veya okul hesabı**' nı seçin.

    >[!Note]
    >Microsoft Authenticator uygulamasını ilk kez ayarlıyorsanız, uygulamanın kameranıza erişmesine izin verip vermeyeceğinizi (iOS) veya uygulamanın resim almasına ve video (Android) kaydetmesine izin verip vermeyeceğinizi soran bir istem alabilirsiniz. Bir sonraki adımda QR kodunun bir resmini almak için, kimlik doğrulayıcı uygulamasının kameranıza erişebilmesi için **Izin ver** ' i seçmeniz gerekir. Kameraya izin vermezseniz, kimlik doğrulayıcı uygulamasını ayarlamaya devam edebilirsiniz, ancak kod bilgilerini el ile eklemeniz gerekir. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

5. Bilgisayarınızda **hesabınızı ayarlama** sayfasına dönün ve sonra **İleri**' yi seçin.

    **QR kodunu Tara** sayfası görüntülenir.

    ![Doğrulayıcı uygulamasını kullanarak QR kodunu tarama](media/security-info/securityinfo-prompt-auth-app-qrcode.png)

6. 5\. adımda iş veya okul hesabınızı oluşturduktan sonra mobil cihazınızda görünen Microsoft Authenticator App QR kod okuyucusu ile belirtilen kodu tarayın.

    Kimlik doğrulayıcı uygulaması, size herhangi bir ek bilgi gerekmeden iş veya okul hesabınızı başarıyla eklememelidir. Ancak, QR kod okuyucusu kodu okuyaamazsa, **QR görüntüsünü tarayamaz** ve Microsoft Authenticator uygulamasına kodu ve URL 'yi el ile girebilirsiniz. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

7. Bilgisayarınızdaki **QR kodunu Tara** sayfasında **İleri ' yi** seçin.

    Hesabınızı test etmek için mobil cihazınızdaki Microsoft Authenticator uygulamasına bir bildirim gönderilir.

    ![Kimlik doğrulayıcı uygulamasıyla hesabınızı test etme](media/security-info/securityinfo-prompt-test-app.png)

8. Microsoft Authenticator uygulamasındaki bildirimi onaylayın ve ardından **İleri**' yi seçin.

    ![Başarı bildirimi, uygulamayı ve hesabınızı bağlama](media/security-info/securityinfo-prompt-auth-app-success.png)

    Güvenlik bilgileriniz, iki adımlı doğrulama veya parola sıfırlama kullanırken kimliğinizi doğrulamak için varsayılan olarak Microsoft Authenticator uygulamasını kullanacak şekilde güncelleştirilir.

9. **Telefon** kurulum sayfasında, kısa mesaj veya telefon araması almak isteyip istemediğinizi seçin ve ardından **İleri**' yi seçin. Bu örneğin, metin iletilerini kullanacağız, bu nedenle metin iletilerini kabul edebilecek bir cihaz için telefon numarası kullanmanız gerekir.

    ![SMS mesajlaşması için telefon numaranızı ayarlamaya başlayın](media/security-info/securityinfo-prompt-text-msg.png)

    Telefon numaranızı bir SMS mesajı gönderilir. Telefon araması yapmayı tercih ediyorsanız, işlem aynıdır. Bununla birlikte, kısa mesaj yerine yönergeler içeren bir telefon araması alırsınız.

10. Mobil cihazınıza gönderilen SMS iletisinin verdiği kodu girin ve ardından **İleri**' yi seçin.

    ![Kısa mesajla hesabınızı test edin](media/security-info/securityinfo-prompt-text-msg-enter-code.png)

11. Başarı bildirimini gözden geçirin ve **bitti**' yi seçin.

    ![Başarı bildirimi](media/security-info/securityinfo-prompt-call-answered-success.png)

    Güvenlik bilgileriniz, iki adımlı doğrulama veya parola sıfırlama kullanılırken kimliğinizi doğrulamak için bir yedekleme yöntemi olarak metin iletilerini kullanacak şekilde güncelleştirilir.

12. Güvenlik bilgileriniz için hem Microsoft Authenticator uygulamasını hem de telefon (kısa mesaj veya telefon araması) yöntemini başarıyla ayarladığınızı doğrulamak için **başarı** sayfasını gözden geçirin ve **bitti**' yi seçin.

    ![Sihirbaz başarıyla tamamlandı sayfası](media/security-info/securityinfo-prompt-setup-success.png)

    >[!Note]
    >Kuruluşunuz uygulama parolalarını kullanmanızı gerektiriyorsa, bu sihirbazda ayarlayabileceğiniz ek bir bölüm görürsünüz. **Uygulama parolaları**olarak adlandırılan üçüncü bir bölüm görürseniz, Sihirbazı tamamlayabilmeniz için önce onu doldurmanız gerekir. Uygulama parolası ekleme hakkında adımlar için bu makalenin [uygulama parolalarını yönetme](#manage-your-app-passwords) bölümüne bakın.

### <a name="manage-your-app-passwords"></a>Uygulama parolalarınızı yönetin

Outlook 2010 gibi bazı uygulamalar iki aşamalı doğrulamayı desteklemez. Bu destek eksikliği, kuruluşunuzda iki aşamalı doğrulama kullanıyorsanız uygulamanın çalışmamasıdır. Bu sorunu çözmek için, normal parolanınızdan ayrı olarak, her tarayıcı olmayan uygulamayla birlikte kullanmak üzere otomatik olarak kullanılan bir parola oluşturabilirsiniz.

>[!Note]
>Sihirbazda bu seçeneği görmüyorsanız, yöneticinizin bunu ayarlamadığı anlamına gelir. Bu ayarlanmamışsa, ancak uygulama parolalarını kullanmanız gerektiğini biliyorsanız, [güvenlik bilgileri (Önizleme) sayfasından uygulama parolalarını ayarlama](security-info-app-passwords.md)bölümündeki adımları izleyebilirsiniz.

Uygulama parolalarını kullanırken, dikkat edilmesi önemlidir:

- Uygulama parolaları otomatik olarak oluşturulur ve uygulama başına yalnızca bir kez girilir.

- Kullanıcı başına 40 parola sınırı vardır. Bu sınırdan sonra bir tane oluşturmayı denerseniz, yenisini oluşturmaya izin verilmeden önce mevcut bir parolayı silmeniz istenir.

- Her uygulama için değil, cihaz başına bir uygulama parolası kullanın. Örneğin, dizüstü bilgisayarınızdaki tüm uygulamalar için tek bir parola ve masaüstünüzdeki tüm uygulamalar için tek bir parola oluşturun.

#### <a name="to-add-app-passwords-in-the-sign-in-wizard"></a>Uygulama parolalarını oturum açma sihirbazına eklemek için

1. Sihirbazın önceki bölümlerini tamamladıktan sonra, **İleri** ' yi seçin ve **uygulama parolası** bölümünü doldurun.

2. Parola gerektiren uygulamanın adını yazın, örneğin `Outlook 2010`ve ardından **İleri**' yi seçin.

    ![Sihirbazda uygulama parolası adını ekleme](media/security-info/app-password-app-password.png)

3. Parola kodunu **uygulama parolası** ekranından kopyalayın ve uygulamanın **parola** alanına yapıştırın (bu örnekte, Outlook 2010).

    ![Kopyalama parolası olan uygulama parolası sayfası](media/security-info/app-password-copy-password.png)

4. Parolayı kopyaladıktan ve uygulamaya yapıştırdıktan sonra, tüm oturum açma yöntemi bilgilerinizin doğru olduğundan emin olmak için Bu sihirbaza dönün ve **bitti**' yi seçin.

    ![Uygulama parolası sayfası, tamamlanma bildirimi](media/security-info/app-password-complete.png)

## <a name="next-steps"></a>Sonraki adımlar

- Varsayılan güvenlik bilgisi yöntemlerini değiştirmek, silmek veya güncelleştirmek için bkz.:

    - [Bir doğrulayıcı uygulaması için güvenlik bilgilerini ayarlayın](security-info-setup-auth-app.md).

    - [SMS mesajlaşma için güvenlik bilgilerini ayarlayın](security-info-setup-text-msg.md).

    - [Telefon aramalarını kullanmak için güvenlik bilgilerini ayarlayın](security-info-setup-phone-number.md).

    - [E-posta kullanmak için güvenlik bilgilerini ayarlayın](security-info-setup-email.md).

    - [Önceden tanımlanmış güvenlik sorularını kullanmak için güvenlik bilgilerini ayarlayın](security-info-setup-questions.md).

- Belirtilen yöntemi kullanarak oturum açma hakkında daha fazla bilgi için bkz. [oturum açma](user-help-sign-in.md).

- Parola [sıfırlama portalından](https://passwordreset.microsoftonline.com/) kayıp veya unuttuysanız parolanızı sıfırlayın veya [iş veya okul parolanızı sıfırlama](active-directory-passwords-update-your-own-password.md) makalesindeki adımları izleyin.

- [Microsoft hesabı makalesinde oturum açma](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) sorunları için sorun giderme ipuçları ve yardım alın.
