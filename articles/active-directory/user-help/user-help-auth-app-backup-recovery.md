---
title: Microsoft Authenticator App ile yedekleme ve kurtarma-Azure AD
description: Microsoft Authenticator uygulamasını kullanarak hesap kimlik bilgilerinizi nasıl yedekleyeceğinizi ve kurtaracağınızı öğrenin.
services: active-directory
author: eross-msft
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2404dd443c1e3ede1d3ad5a81888e9f805edb810
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820252"
---
# <a name="backup-and-recover-account-credentials-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator uygulamayla hesap kimlik bilgilerini yedekleme ve kurtarma

**Şunlara uygulanır:**

- iOS cihazları, sürüm 5.7.0 ve üzeri çalışıyor

- 6\.6.0 ve üzeri sürümleri çalıştıran Android cihazları

Microsoft Authenticator uygulaması, hesap kimlik bilgilerinizi ve hesaplarınızın sırası gibi ilgili uygulama ayarlarını buluta yedekler. Yedeklemeden sonra, verilerinizi yeni bir cihazda kurtarmak için de kullanabilirsiniz; bu da, kilitlenmelerden veya hesapların yeniden oluşturulması gereğini ortadan kaldırmanıza imkan sağlar.

Her yedekleme depolama konumu için tek bir kişisel Microsoft hesabı olması gerekir, ancak iOS Ayrıca bir iCloud hesabına sahip olmanızı gerektirir. Bu tek konumda depolanan birden çok hesabınız olabilir. Örneğin, kişisel bir hesabınız, bir iş veya okul hesabınız ve Facebook, Google vb. gibi kişisel, Microsoft hesabı olmayan bir hesap olabilir.

> [!IMPORTANT]
> Yalnızca kişisel ve üçüncü taraf hesap kimlik bilgileriniz depolanır; bu, Kullanıcı adınızı ve Kimliğinizi kanıtlamak için gereken hesap doğrulama kodunu içerir. E-postalar veya dosyalar dahil, hesaplarınızla ilişkili diğer bilgileri depolamayız. Ayrıca, hesaplarınızı herhangi bir şekilde veya başka bir ürün ya da hizmetle ilişkilendirmez veya paylaşmayın. Son olarak, BT yöneticiniz bu hesaplardan herhangi biri hakkında herhangi bir bilgi almaz.

## <a name="back-up-your-account-credentials"></a>Hesap kimlik bilgilerinizi yedekleyin

Kimlik bilgilerinizi yedekleyebilmeniz için önce şunları yapmanız gerekir:

- Kurtarma hesabınız olarak davranacak kişisel bir [Microsoft hesabı](https://account.microsoft.com/account) .

- **Yalnızca IOS için,** gerçek depolama konumu Için bir [iCloud hesabınız](https://www.icloud.com/) olmalıdır.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>İOS cihazları için bulut yedeklemesini açmak için

- İOS cihazınızda **Ayarlar**' ı seçin, **Yedekle**' yi seçin ve ardından **iCloud yedeklemesi**' ni açın.

    Hesap kimlik bilgileriniz iCloud hesabınıza yedeklenir.

    ![iCloud yedekleme ayarlarının konumunu gösteren iOS ayarları ekranı](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Android cihazlar için bulut yedeklemesini açmak için

- Android cihazınızda **Ayarlar**' ı seçin, **Yedekle**' yi seçin ve ardından **bulut yedeklemesi**' ni açın.

    Hesap kimlik bilgileriniz bulut hesabınıza yedeklenir.

    ![Yedekleme ayarlarının konumunu gösteren Android ayarları ekranı](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Hesap kimlik bilgilerinizi yeni cihazınızda kurtarın

Hesap kimlik bilgilerinizi bulut hesabınızdan kurtarabilir, ancak önce kurtardığınız hesabın Microsoft Authenticator uygulamasında bulunmadığından emin olmanız gerekir. Örneğin, kişisel Microsoft hesabı kurtarıyorsanız, kimlik doğrulayıcı uygulamasında zaten bir kişisel Microsoft hesabı ayarlamış olduğunuzdan emin olmanız gerekir. Bu denetim önemli olduğundan, var olan bir hesabı yanlışlıkla yazarken veya sildiğimiz için emin olabiliriz.

### <a name="to-recover-your-information"></a>Bilgilerinizi kurtarmak için

1. Mobil cihazınızda Microsoft Authenticator uygulamasını açın ve ekranın altından **kurtarmaya başla** ' yı seçin.

    ![Kurtarma Başlat ' ın nereye tıklamasını gösteren Microsoft Authenticator uygulaması](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Kurtarma hesabınızda, yedekleme işlemi sırasında kullandığınız kişisel Microsoft hesabı ile oturum açın.

    Hesap kimlik bilgileriniz yeni cihaza kurtarıldı.

Kurtarmayı tamamladıktan sonra, Microsoft Authenticator uygulamasındaki kişisel Microsoft hesabı doğrulama kodlarınızın eski ve yeni telefonlarınız arasında farklı olduğunu fark edebilirsiniz. Her bir cihazın kendine ait benzersiz bir kimlik bilgisi olduğundan, her ikisi de geçerli ve ilişkili telefonda oturum açarken çalıştığı için kodlar farklıdır.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Daha fazla doğrulama gerektiren ek hesapları kurtar

Kişisel veya iş veya okul hesaplarınızla anında iletme bildirimleri kullanıyorsanız, bilgilerinizi kurtarabilmeniz için daha fazla doğrulama sağlamanız gerektiğini belirten bir ekran uyarısı alırsınız. Anında iletme bildirimleri, belirli cihazınıza bağlı olan ve ağ üzerinden gönderilmemiş bir kimlik bilgisi kullanılmasını gerektirdiğinden, cihazınızda kimlik bilgileri oluşturulmadan önce kimliğinizi kanıtlamanız gerekir.

Kişisel Microsoft hesapları için parolanızı, alternatif bir e-posta veya telefon numarasıyla girerek kimliğinizi kanıtlayabilirsiniz. İş veya okul hesaplarında, size hesap sağlayıcınız tarafından verilen bir QR kodunu taramalısınız.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Kişisel hesaplara ek doğrulama sağlamak için

1. Microsoft Authenticator uygulamasının **hesaplar** ekranında, kurtarmak istediğiniz hesabın yanındaki açılan oku seçin.

    ![İlgili açılan okları olan kullanılabilir hesapları gösteren Microsoft Authenticator uygulaması](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

2. **Kurtarmak Için oturum aç**' ı seçin, parolanızı yazın ve ardından e-posta adresinizi veya telefon numaranızı ek doğrulama olarak onaylayın.

    ![Microsoft Authenticator uygulama, oturum açma bilgilerinizi girmenize olanak sağlar](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>İş veya okul hesapları için ek doğrulama sağlamak üzere

1. Microsoft Authenticator uygulamasının **hesaplar** ekranında, kurtarmak istediğiniz hesabın yanındaki açılan oku seçin.

    ![İlgili açılan okları olan kullanılabilir hesapları gösteren Microsoft Authenticator uygulaması](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

2. **Kurtarmak IÇIN QR kodunu Tara**' yı seçin ve ardından QR kodunu tarayın.

    ![Microsoft Authenticator, QR kodunuzu taramanızı sağlar](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    >[!NOTE]
    >QR kodları ve nasıl alınacağı hakkında daha fazla bilgi için, yöneticinizin güvenlik bilgilerine sahip olup olmadığına bağlı olarak, [Microsoft Authenticator uygulamayı kullanmaya başlama](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) veya [güvenlik bilgilerini bir kimlik doğrulayıcı uygulaması kullanacak şekilde ayarlama](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)bölümüne bakın.
    >
    >Microsoft Authenticator uygulamasını ilk kez ayarlıyorsanız, uygulamanın kameranıza erişmesine izin verip vermeyeceğinizi (iOS) veya uygulamanın resim almasına ve video (Android) kaydetmesine izin verip vermeyeceğinizi soran bir istem alabilirsiniz. Bir sonraki adımda QR kodunun bir resmini almak için, kimlik doğrulayıcı uygulamasının kameranıza erişebilmesi için **Izin ver** ' i seçmeniz gerekir. Kameraya izin vermezseniz, kimlik doğrulayıcı uygulamasını ayarlamaya devam edebilirsiniz, ancak kod bilgilerini el ile eklemeniz gerekir. Kodu el ile ekleme hakkında daha fazla bilgi için bkz. [uygulamaya el ile hesap ekleme](user-help-auth-app-add-account-manual.md).

## <a name="troubleshoot-backup-and-recovery-problems"></a>Yedekleme ve kurtarma sorunlarını giderme

Yedeğinizin kullanılabilir olmasının birkaç nedeni vardır:

- **İşletim sistemlerini değiştirme.** Yedeklemeniz iOS için iCloud ve Microsoft 'un Android için bulut depolama sağlayıcısı 'nda depolanır. Bu, Android ve iOS cihazları arasında geçiş yaparsanız yedeklemenin kullanılamadığı anlamına gelir. Anahtarı yaparsanız, hesaplarınızı Microsoft Authenticator uygulamasında el ile yeniden oluşturmanız gerekir.

- **Ağ sorunları.** Ağla ilgili sorunlar yaşıyorsanız, ağa bağlı olduğunuzdan ve hesabınızda düzgün şekilde oturum açtığınızdan emin olun.

- **Hesap sorunları.** Hesapla ilgili sorunlar yaşıyorsanız hesabınızda doğru şekilde oturum açtığınızdan emin olun. İOS için bu, iPhone 'nizle aynı AppleID hesabını kullanarak iCloud 'da oturum açmanız gerektiği anlamına gelir.

- **Yanlışlıkla silme.** Yedekleme hesabınızı önceki cihazdan silmiş veya bulut depolama hesabınızı yönetirken mümkündür. Bu durumda, hesabınızı uygulama içinde el ile yeniden oluşturmanız gerekir.

- **Mevcut Microsoft Authenticator hesapları.** Microsoft Authenticator uygulamasında hesapları zaten ayarladıysanız, uygulama yedeklenmiş hesaplarınızı kurtaramaz. Kurtarmanın önlenmesi, güncel olmayan bilgilerle hesap ayrıntılarınızın üzerine yazılmadığından emin olmanıza yardımcı olur. Bu durumda, yedeklemenizi kurtarabilmeniz için, kimlik doğrulayıcı uygulamanızda ayarlanmış mevcut hesaplardan mevcut hesap bilgilerini kaldırmanız gerekir.

- **Yedekleme güncel değil.** Yedekleme bilgileriniz güncel değilse, Microsoft kurtarma hesabınızda yeniden oturum açarak bilgileri yenilemeniz istenebilir. Kurtarma hesabınız, ilk olarak yedeklemenizi depolamak için kullandığınız kişisel Microsoft hesabı. Bir oturum açma gerekliyse, menüde veya eylem çubuğunda kırmızı bir nokta görürsünüz. Kırmızı noktayı seçtikten sonra bilgilerinizi güncelleştirmek için yeniden oturum açmanız istenir.

## <a name="next-steps"></a>Sonraki adımlar

Hesap kimlik bilgilerinizi yeni cihazınıza yedeklediğinize ve kurtardığınıza göre kimliğinizi doğrulamak için Microsoft Authenticator uygulamasını kullanmaya devam edebilirsiniz. Daha fazla bilgi için bkz. [Microsoft Authenticator uygulamasını kullanarak hesaplarınızda oturum açma](user-help-sign-in.md).

## <a name="related-articles"></a>İlgili makaleler:

- [Microsoft Authenticator uygulaması nedir?](user-help-auth-app-overview.md)

- [Microsoft Authenticator uygulaması hakkında SSS](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
