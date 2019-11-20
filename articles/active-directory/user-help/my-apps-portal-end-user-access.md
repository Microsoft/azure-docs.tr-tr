---
title: Uygulamalarım portalındaki uygulamalara erişme ve uygulama kullanma-Azure Active Directory | Microsoft Docs
description: Uygulamalarım portalına nasıl erişebileceğinizi öğrenin.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 901067e198616dba390e0e1162ec26a0b11768f1
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "68383146"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>Uygulamalarım portalındaki uygulamalara erişin ve bunları kullanın

İş veya okul hesabınızı Web tabanlı **My Apps** portalından kullanarak kuruluşunuzun bulut tabanlı uygulamalarının çoğunu görüntüleyip başlatabilir, profil ve hesap bilgilerinizin bazılarını güncelleştirebilir, **gruplarınızın** bilgilerini görebilir ve şunları yapabilirsinizUygulamalarınız ve gruplarınız için erişim gözden geçirmeleri. **Uygulamalarım** portalına erişiminiz yoksa, Izin Için yardım masasına başvurmanız gerekir.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Bu içerik kullanıcılara yöneliktir. Yöneticiyseniz, [uygulama yönetimi belgelerinde](https://docs.microsoft.com/azure/active-directory/manage-apps)bulut tabanlı uygulamalarınızın nasıl ayarlanacağı ve yönetileceği hakkında daha fazla bilgi edinebilirsiniz.

## <a name="supported-browsers"></a>Desteklenen tarayıcılar

Aşağıdaki Web tarayıcılarının herhangi birinden **uygulamalar** portalına ulaşabilirsiniz:

- Google Chrome

- Mozilla Firefox, sürüm 26,0 veya üzeri

- Microsoft Edge

- Internet Explorer, sürüm 11 (sınırlı destek)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>Uygulamalarım güvenli oturum açma uzantısını indirin ve yükleyin

İstenirse uygulamalarım güvenli oturum açma uzantısını indirip yükleyin. Bu uzantı, kuruluşunuzda çoklu oturum açma işlemi kullanmanızı gerektiren bulut uygulamalarından herhangi birini başlatmanıza yardımcı olur. Kuruluşunuz zaten çoklu oturum açma için ayarlandıysa, uzantı otomatik olarak yüklenir ve bu bölümü atlayabilirsiniz.

Bu uzantı şunları yapmanıza yardımcı olur:

- Oturum açma sayfasından doğrudan uygulamalarda oturum açın.

- **Hızlı arama** özelliğini kullanarak tüm uygulamaları başlatın.

- Son **kullanılan** bölümde kullandığınız son uygulamalara bakın.

- [Uygulama proxy 'si](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)ile uzak sırada Iç şirket URL 'lerini kullanın.

### <a name="to-download-and-install-the-extension"></a>Uzantıyı indirmek ve yüklemek için

Uzantıyı, kullanmakta olduğunuz tarayıcıya göre indirin ve yükleyin:

- **Google Chrome.** Chrome Web mağazasından, [uygulamalarım güvenli oturum açma uzantısı](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) özelliğine gidin ve ardından **Chrome 'a Ekle**' yi seçin.

- **Mozilla Firefox** **Firefox eklentileri** sayfasından [uygulamalarım güvenli oturum açma uzantısı](https://addons.mozilla.org/firefox/addon/access-panel-extension/) özelliğine gidin ve ardından **Firefox 'a Ekle**' yi seçin.

- **Microsoft Edge** Microsoft Store, [uygulamalarım güvenli oturum açma uzantısı](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) özelliğine gidin ve **Al**' ı seçin.

**Adres** çubuğunuzun sağına bir simge eklenir, bu da oturum açıp uzantıyı özelleştirmenize olanak tanır.

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>Uzantıyı kullanarak My Apps portalınızı değiştirme
**Son kullanılan** bölümde kaç uygulama görüntüleneceğini seçebilir ve kuruluşunuzun Iç URL 'lerinin yeniden yönlendirmesine izin verip vermeyeceğine karar verebilirsiniz.

1. Yeni **uygulamalarım güvenli oturum açma uzantısı** simgesini seçin ![Extension simgesi **adres** çubuğunuzun sağında ](media/my-apps-portal/my-apps-portal-extension-icon.png) ve ardından **oturum aç**' ı seçerek başlayın.

2. **Ayarlar** simgesine ![Settings simge ](media/my-apps-portal/my-apps-portal-extension-settings-icon.png) sağ tıklayın ve ardından **Ayarlar**' ı seçin.

3. **Ayarlar** kutusunda, portalda görmek istediğiniz son uygulama sayısını ve kuruluşunuzun Iç URL 'lerinin uzaktan kullanabilmeniz için yeniden yönlendirmesine izin verip vermeyeceğinizi seçin.

    ![Uzantının, kullanılabilir özelleştirmeleri gösteren ayarlar sayfası](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>Cihaza göre uygulamalar portalına erişme ve bunları kullanma
Bilgisayarınızda, Intune tarafından yönetilen bir tarayıcıdan veya bir iOS veya Android mobil cihazınızdan, My Apps portalına erişebilir ve bunları kullanabilirsiniz.

![Uygulamalarım portalındaki uygulamalar sayfası](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>Bilgisayarınızdaki My Apps portalına erişin ve bunları kullanın
Kuruluşunuzun bulut tabanlı uygulamalarına erişme ve bunları kullanma izniniz varsa, bu uygulamalara, **uygulamalar** portalından ulaşabilirsiniz.

1. İş veya okul hesabınızda oturum açın.

2. Web tarayıcınızı açın ve https://myapps.microsoft.com gidin veya kuruluşunuz tarafından sunulan bağlantıyı kullanın. Örneğin, kuruluşunuz için https://myapps.microsoft.com/contoso.com gibi özelleştirilmiş bir sayfaya yönlendirilmeyebilirsiniz.

    Kuruluşunuzun sahip olduğu ve kullanabileceğiniz bulut tabanlı uygulamaların tümünü gösteren **uygulamalar** sayfası görüntülenir.

3. **Uygulamalar** sayfasında, kullanmaya başlamak istediğiniz uygulamayı seçin.

    Uygulama için (gerekirse) oturum açmak veya uygulamayı kullanmaya başlamak için yeni bir sayfa açılır.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Intune ile yönetilen bir tarayıcıda, My Apps portalına erişin ve bunları kullanın

İOS ve Android cihazlarınızdaki bir Intune Managed Browser kuruluşunuzun uygulamalarını görüntüleyin ve kullanın.

1. Mobil cihazınızda Intune Managed Browser uygulamasını [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) 'dan ve [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)indirin ve yükleyin.

2. Intune Managed Browser uygulamasını açın, https://myapps.microsoft.com ' e gidin veya kuruluşunuz tarafından sunulan bağlantıyı kullanın. Örneğin, kuruluşunuz için https://myapps.microsoft.com/contoso.com gibi özelleştirilmiş bir sayfaya yönlendirilmeyebilirsiniz.

    Kuruluşunuzun sahip olduğu ve kullanabileceğiniz bulut tabanlı uygulamaların tümünü gösteren **uygulamalar** sayfası görüntülenir.

3. **Uygulamalar** sayfasında, kullanmaya başlamak istediğiniz uygulamayı seçin.

    Uygulama için (gerekirse) oturum açmak veya uygulamayı kullanmaya başlamak için yeni bir sayfa açılır.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>İOS cihazında My Apps portalına erişme ve bunları kullanma

İOS sürüm 7 veya üzerini çalıştıran bir iPhone veya iPad cihazından **My Apps** portalını görüntüleyin ve kullanın. İOS cihazlarınızdaki Kuruluşunuzun uygulamalarına erişmek için [My Apps Mobil uygulamasını](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8) da yükleyebilirsiniz. 

1. Mobil cihazınızda Safari gibi bir Web tarayıcısı uygulaması başlatın.

2. @No__t_0 gidin veya kuruluşunuz tarafından sunulan bağlantıyı kullanın. Örneğin, kuruluşunuz için https://myapps.microsoft.com/contoso.com gibi özelleştirilmiş bir sayfaya yönlendirilmeyebilirsiniz.

    Kuruluşunuzun sahip olduğu ve kullanabileceğiniz bulut tabanlı uygulamaların tümünü gösteren **uygulamalar** sayfası görüntülenir.

3. **Uygulamalar** sayfasında, kullanmaya başlamak istediğiniz uygulamayı seçin.

    Uygulama için (gerekirse) oturum açmak veya uygulamayı kullanmaya başlamak için yeni bir sayfa açılır.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Android cihazında My Apps portalına erişme ve bunları kullanma

Android cihazında **My Apps** portalını görüntüleyin ve kullanın.

1. Mobil cihazınızda, Google Chrome gibi bir Web tarayıcısı uygulaması başlatın.

2. @No__t_0 gidin veya kuruluşunuz tarafından sunulan bağlantıyı kullanın. Örneğin, kuruluşunuz için https://myapps.microsoft.com/contoso.com gibi özelleştirilmiş bir sayfaya yönlendirilmeyebilirsiniz.

    Kuruluşunuzun sahip olduğu ve kullanabileceğiniz bulut tabanlı uygulamaların tümünü gösteren **uygulamalar** sayfası görüntülenir.

3. **Uygulamalar** sayfasında, kullanmaya başlamak istediğiniz uygulamayı seçin.

    Uygulama için (gerekirse) oturum açmak veya uygulamayı kullanmaya başlamak için yeni bir sayfa açılır.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>Uygulamalarım portalına yeni bir uygulama ekleme

Yöneticiniz size izin verdiyse, **uygulamalar** sayfasına yeni bir uygulama ekleyebilirsiniz.

1. **Uygulamalar** sayfasında, **Uygulama Ekle**' yi seçin.

    **Uygulama Ekle** sayfası görüntülenir.

    ![Uygulamalar ekleme sayfası, My Apps portalında](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. Belirtilen listeden eklemek istediğiniz uygulamayı seçin ve ardından **Ekle**' yi seçin.

3. Uygulama, **uygulamalar** sayfasındaki listeye eklenir.

    Bazı uygulamaların eklenebilmesi için önce yönetici onayı gerekebilir. Bu durumda, uygulama, yönetici tarafından onaylayana kadar **uygulamalar** sayfasına eklenmez.

## <a name="next-steps"></a>Sonraki adımlar

**Uygulamalar** sayfasına geldiğinizde şunları yapabilirsiniz:

- [Profil bilgilerinizi değiştirin](my-apps-portal-end-user-update-profile.md)

- [Gruplarla ilgili bilgilerinizi görüntüleyin ve güncelleştirin](my-apps-portal-end-user-groups.md)

- [Kendi erişim gözden geçirmelerinizi gerçekleştirme](my-apps-portal-end-user-access-reviews.md)
