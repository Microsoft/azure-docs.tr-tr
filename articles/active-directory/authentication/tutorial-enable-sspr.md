---
title: Self servis parola sıfırlamayı Azure Active Directory etkinleştirme
description: Bu öğreticide, bir Kullanıcı grubu için self servis parola sıfırlamayı Azure Active Directory etkinleştirmeyi ve parola sıfırlama işlemini test yapmayı öğreneceksiniz.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18dd231a708030e3a454ab8708e3f0f11dbecf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861832"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Öğretici: kullanıcıların Self servis parola sıfırlama Azure Active Directory kullanarak hesaplarının kilidini açma veya parolaları sıfırlamalarını sağlama

Azure Active Directory (Azure AD) self servis parola sıfırlama (SSPR), kullanıcılara yönetici veya yardım masası katılımı olmadan parolasını değiştirme veya sıfırlama yeteneği sağlar. Azure AD bir kullanıcının hesabını kilitler veya parolalarını unutur, kendi kendilerini engellemeyi ve çalışmaya geri dönmek için istemleri izleyebilir. Bu özellik, bir Kullanıcı cihazlarındaki veya bir uygulamada oturum açarken yardım masası çağrılarını ve üretkenlik kaybını azaltır. [Azure AD 'de SSPR 'yi etkinleştirme ve yapılandırma](https://www.youtube.com/watch?v=rA8TvhNcCvQ)konusunda bu videoyu öneririz. Ayrıca [, SSPR ile en yaygın altı Son Kullanıcı hata iletilerini çözümlemeden](https://www.youtube.com/watch?v=9RPrNVLzT8I)BT yöneticileri için bir video sunuyoruz.

> [!IMPORTANT]
> Bu öğreticide, self servis parola sıfırlamayı etkinleştirme Yöneticisi gösterilmektedir. Self servis parola sıfırlama için zaten kayıtlı bir son kullanıcı varsa ve hesabınıza geri dönmek istiyorsanız [Microsoft Online parola sıfırlama](https://passwordreset.microsoftonline.com/) sayfasına gidin.
>
> BT ekibiniz kendi parolanızı sıfırlama özelliğini etkinleştirmediyseniz, ek yardım için yardım masasına ulaşın.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Bir Azure AD kullanıcısı grubu için self servis parola sıfırlamayı etkinleştirme
> * Kimlik doğrulama yöntemlerini ve kayıt seçeneklerini ayarlama
> * SSPR işlemini Kullanıcı olarak test etme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi bitirebilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* En az bir Azure AD ücretsiz veya deneme lisansı etkin olan çalışan bir Azure AD kiracısı. Ücretsiz katmanda SSPR yalnızca Azure AD 'deki bulut kullanıcıları için geçerlidir. Parola değişikliği ücretsiz katmanda desteklenir, ancak parola sıfırlama desteklenmez. 
    * Bu serideki sonraki öğreticiler için, şirket içi parola geri yazma için Azure AD Premium P1 veya deneme lisansı gerekir.
    * Gerekirse, [ücretsiz olarak bir Azure hesabı oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Genel yönetici* ayrıcalıklarına sahip bir hesap.
* Yönetici olmayan ve bir parolası olan, *testuser* gibi bir kullanıcı. Son Kullanıcı SSPR deneyimini Bu öğreticide bu hesabı kullanarak test edersiniz.
    * Bir kullanıcı oluşturmanız gerekiyorsa bkz. [hızlı başlangıç: Azure Active Directory yeni kullanıcı ekleme](../fundamentals/add-users-azure-active-directory.md).
* Yönetici olmayan kullanıcının üyesi olduğu bir grup, farklı bir *SSPR-test grubu*. Bu öğreticide bu grup için SSPR 'yi etkinleştireceksiniz.
    * Bir grup oluşturmanız gerekiyorsa bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Kendi kendine parola sıfırlamayı etkinleştirme

Azure AD, SSPR 'yi *hiçbiri*, *Seçili* veya *Tüm* kullanıcılar için etkinleştirmenizi sağlar. Bu ayrıntılı özellik, SSPR kayıt işlemini ve iş akışını test etmek için kullanıcıların bir alt kümesini seçmenize olanak sağlar. İşlemi rahat ve daha geniş bir Kullanıcı kümesiyle iletişim kurmak için zaman Haksa, SSPR için etkinleştirilecek kullanıcı grubunu seçebilirsiniz. Ya da, SSPR 'yi Azure AD kiracısındaki herkes için etkinleştirebilirsiniz.

> [!NOTE]
> Şu anda, Azure portal kullanarak SSPR için yalnızca bir Azure AD grubunu etkinleştirebilirsiniz. SSPR 'nin daha geniş bir dağıtımının bir parçası olarak, Azure AD iç içe grupları destekler. Seçtiğiniz gruptaki (ler) kullanıcılara uygun lisansların atandığından emin olun. Şu anda bu lisans gereksinimlerinden herhangi bir doğrulama işlemi yoktur.

Bu öğreticide, bir test grubundaki bir kullanıcı kümesi için SSPR 'yi ayarlayın. *SSPR-test-grubunu* kullanın ve gerektiğinde kendı Azure AD grubunuzu sağlayın:

1. *Genel yönetici* izinlerine sahip bir hesap kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **parola sıfırlama** ' yı seçin.
1. **Özellikler** sayfasında, *self servis parola sıfırlama etkin* seçeneği altında **Grup Seç** ' i seçin.
1. *SSPR-test-Group* gıbı Azure AD grubunuza gözatıp seçin ve ardından *Seç*' i seçin.

    [![Self servis parola sıfırlama için etkinleştirmek üzere Azure portal bir grup seçin](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Select kullanıcıları için SSPR 'yi etkinleştirmek üzere **Kaydet**' i seçin.

## <a name="select-authentication-methods-and-registration-options"></a>Kimlik doğrulama yöntemlerini ve kayıt seçeneklerini belirleyin

Kullanıcıların hesaplarının kilidini açmak veya parolasını sıfırlaması gerektiğinde, başka bir onay yöntemi istenir. Bu ek kimlik doğrulama faktörü, Azure AD 'nin yalnızca onaylanan SSPR olaylarını tamamladığından emin olmanızı sağlar. Kullanıcının sağladığı kayıt bilgilerine göre hangi kimlik doğrulama yöntemlerinin izin verdiğini seçebilirsiniz.

1. **Kimlik doğrulama yöntemleri** sayfasının sol tarafındaki menüden *1*' e **sıfırlamak için gereken yöntem sayısını** ayarlayın.

    Güvenliği artırmak için SSPR için gereken kimlik doğrulama yöntemlerinin sayısını artırabilirsiniz.

1. Kuruluşunuzun izin vermek istediği **kullanıcılara sunulan yöntemleri** seçin. Bu öğretici için aşağıdaki yöntemleri etkinleştirmek üzere kutulara göz atın:

    * *Mobil uygulama bildirimi*
    * *Mobil uygulama kodu*
    * *E-posta*
    * *Cep telefonu*

    *Office telefon* veya *güvenlik soruları* gibi diğer kimlik doğrulama yöntemlerini iş gereksinimlerinize uyacak şekilde etkinleştirebilirsiniz.

1. Kimlik doğrulama yöntemlerini uygulamak için **Kaydet**' i seçin.

Kullanıcıların hesaplarının kilidini açmak veya bir parolayı sıfırlayabilmeleri için, bunların iletişim bilgilerini kaydetmesi gerekir. Azure AD, önceki adımlarda ayarlanan farklı kimlik doğrulama yöntemleri için bu iletişim bilgilerini kullanır.

Bir yönetici bu iletişim bilgilerini el ile sağlayabilir veya kullanıcıların bilgileri sağlaması için bir kayıt portalına gidebilir. Bu öğreticide, Azure AD 'yi, kullanıcılardan bir sonraki oturum açışlarında kaydolmaları isteyecek şekilde ayarlayın.

1. **Kayıt** sayfasının sol tarafındaki menüden *Evet* ' i seçerek **kullanıcıların oturum açarken kaydolmasını gerektir**' i seçin.
1. **Kullanıcıların kimlik doğrulaması bilgilerini yeniden onaylamasını istemeden önce geçen gün sayısı** ayarını *180* olarak belirleyin.

    İletişim bilgilerinin güncel tutulması önemlidir. Bir SSPR olayı başlatıldığında, tarihi geçmiş iletişim bilgileri varsa, Kullanıcı hesabının kilidini açmayabilir veya parolalarını sıfırlayamayabilir.

1. Kayıt ayarlarını uygulamak için **Kaydet**' i seçin.

## <a name="set-up-notifications-and-customizations"></a>Bildirimleri ve özelleştirmeleri ayarlama

Kullanıcılara hesap etkinlikleri hakkında bilgi sahibi olmak için, bir SSPR olayı gerçekleştiğinde Azure AD 'yi e-posta bildirimleri gönderecek şekilde ayarlayabilirsiniz. Bu bildirimler, hem normal kullanıcı hesaplarını hem de yönetici hesaplarını kapsayabilir. Yönetici hesaplarında, bu bildirim, bir ayrıcalıklı yönetici hesabı parolası SSPR kullanılarak sıfırlandığında başka bir tanıma katmanı sağlar. Azure AD, bir yönetici hesabında SSPR kullandığında tüm genel yöneticilere bildirim gönderir.

1. **Bildirimler** sayfasının sol tarafındaki menüden aşağıdaki seçenekleri ayarlayın:

   * **Parola sıfırlamayı kullanıcılara bildirme** seçeneğini *Evet* olarak ayarlayın.
   * **Diğer yöneticiler parolalarını sıfırladığında tüm yöneticilere bildirme** seçeneğini *Evet* olarak ayarlayın.

1. Bildirim tercihlerini uygulamak için **Kaydet**' i seçin.

SSPR işleminde kullanıcıların daha fazla yardıma ihtiyacı varsa, "yöneticinize başvurun" bağlantısını özelleştirebilirsiniz. Kullanıcı, SSPR kayıt işleminde bu bağlantıyı seçebilir ve hesaplarının kilidini açıp parolalarını sıfırlarlar. Kullanıcılarınızın gerekli desteği almasını sağlamak için özel bir yardım masası e-postası veya URL sağlamanız önerilir.

1. **Özelleştirme** sayfasının sol tarafındaki menüden, **Özelleştir yardım masası bağlantısını** *Evet* olarak ayarlayın.
1. **Özel yardım masası e-postası veya URL 'si** alanında, kullanıcılarınızın kuruluşunuzdan daha fazla yardım almak için *https: \/ /support.contoso.com/* gibi bir e-posta adresi veya Web sayfası URL 'si sağlayın
1. Özel bağlantıyı uygulamak için **Kaydet**' i seçin.

## <a name="test-self-service-password-reset"></a>Self servis parola sıfırlamayı test etme

SSPR 'yi etkin ve ayarlanmış olarak, SSPR sürecini *Test-SSPR-Group* gibi önceki bölümde seçtiğiniz grubun parçası olan bir kullanıcıyla test edin. Aşağıdaki örnek, *testuser* hesabını kullanır. Kendi kullanıcı hesabınızı girin. Bu öğreticinin ilk bölümünde SSPR için etkinleştirdiğiniz grubun bir parçasıdır.

> [!NOTE]
> Self servis parola sıfırlamayı test ettiğinizde yönetici olmayan bir hesap kullanın. Varsayılan olarak, Azure AD, Yöneticiler için self servis parola sıfırlamasına izin vermez. Parolalarını sıfırlamak için iki kimlik doğrulama yöntemi kullanmaları gerekir. Daha fazla bilgi için bkz. [yönetici sıfırlama ilkesi farklılıkları](concept-sspr-policy.md#administrator-reset-policy-differences).

1. El ile kayıt işlemini görmek için, InPrivate veya ınbilito modunda yeni bir tarayıcı penceresi açın ve *https: \/ /aka.MS/ssprsetup* sayfasına gidin. Azure AD, kullanıcıları bir dahaki sefer oturum açtıklarında bu kayıt portalına yönlendirir.
1. *Testuser* gibi yönetici olmayan bir test kullanıcısı ile oturum açın ve kimlik doğrulama yöntemlerinin iletişim bilgilerini kaydedin.
1. Tamamlandıktan **sonra, işaretlenmiş düğmeyi** seçin ve tarayıcı penceresini kapatın.
1. InPrivate veya ınbilito modunda yeni bir tarayıcı penceresi açın ve *https: \/ /aka.MS/SSPR* konumuna gidin.
1. Yönetici olmayan test kullanıcılarınızın hesap bilgilerini ( *testuser* gibi), CAPTCHA ' dan karakterler ' i girin ve ardından **İleri**' yi seçin.

    ![Parolayı sıfırlamak için Kullanıcı hesabı bilgilerini girin](media/tutorial-enable-sspr/password-reset-page.png)

1. Parolanızı sıfırlamak için doğrulama adımlarını izleyin. İşiniz bittiğinde, parolanızın sıfırlandığını belirten bir e-posta bildirimi alırsınız.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu serinin sonraki bir öğreticide, parola geri yazma özelliğini ayarlayacaksınız. Bu özellik, Azure AD SSPR 'den şirket içi AD ortamına kadar olan parola değişikliklerini yazar. Parola geri yazma 'yı ayarlamak için bu öğretici serisine devam etmek istiyorsanız, SSPR 'yi şimdi devre dışı bırakın.

Bu öğreticinin bir parçası olarak ayarlamış olduğunuz SSPR işlevini artık kullanmak istemiyorsanız, aşağıdaki adımları uygulayarak SSPR durumunu **none** olarak ayarlayın:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Azure Active Directory** bulun ve seçin ve ardından sol taraftaki menüden **parola sıfırlama** ' yı seçin.
1. **Özellikler** sayfasında, *self servis parola sıfırlama etkin* seçeneği altında **hiçbiri**' ni seçin.
1. SSPR değişikliğini uygulamak için **Kaydet**' i seçin.

## <a name="faqs"></a>SSS

Bu bölümde, SSPR 'yi deneyen Yöneticiler ve son kullanıcılar hakkında sık sorulan sorular açıklanmaktadır:

- Şirket içinden eşitlenen parolaları kullanabilmeniz için, bir **parolanızın sıfırlandıktan** sonra, Federasyon kullanıcıları neden 2 dakikaya kadar bekler?

  Parolaları eşitlenen Federasyon kullanıcıları için, parolalar için yetki kaynağı şirket içinde olur. Sonuç olarak, SSPR yalnızca şirket içi parolaları güncelleştirir. Azure AD 'ye geri Parola karması eşitlemesi, her 2 dakikada bir zamanlanır.

- Telefon ve e-posta gibi SSPR verileriyle önceden doldurulan yeni oluşturulan bir Kullanıcı, SSPR kayıt sayfasını ziyaret ettiğinde **hesabınıza erişimi kaybetmeyin!** sayfanın başlığı olarak görünür. SSPR verileri önceden doldurulmadığı diğer kullanıcılar neden iletiyi görmez?

  ' İ görebilen Kullanıcı **hesabınıza erişimi kaybetmez!** , kiracı için yapılandırılmış SSPR/Birleşik kayıt gruplarının üyesidir. Görmediği kullanıcılar **hesabınıza erişimi kaybetmez!** SSPR/Birleşik kayıt gruplarının bir parçası değildi.

- Bazı kullanıcılar SSPR işlemini ilerlerse ve parolalarını sıfırlarsa, neden parola gücü göstergesi görmez?

  Zayıf/güçlü parola gücünü görmeyen kullanıcılar eşitlenmiş parola geri yazma özelliği etkinleştirilmiştir. SSPR, müşterinin Şirket içi ortamının parola ilkesini belirleyelemediğinden, parola gücünü veya zayıf düzeyini doğrulayamaz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, seçili bir Kullanıcı grubu için Azure AD self servis parola sıfırlamayı etkinleştirdiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Bir Azure AD kullanıcısı grubu için self servis parola sıfırlamayı etkinleştirme
> * Kimlik doğrulama yöntemlerini ve kayıt seçeneklerini ayarlama
> * SSPR işlemini Kullanıcı olarak test etme

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication’ı etkinleştirme](./tutorial-enable-azure-mfa.md)
