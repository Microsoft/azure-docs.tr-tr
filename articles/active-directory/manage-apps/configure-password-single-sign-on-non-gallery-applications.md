---
title: Azure AD uygulamaları için parola çoklu oturum açmayı yapılandırma | Microsoft Docs
description: Microsoft Identity platform (Azure AD) içinde Azure AD kurumsal uygulamalarınıza parola çoklu oturum açma (SSO) yapılandırma
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: d191abafbaad123ed47f8eaae6cdd4e48478da7a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422612"
---
# <a name="configure-password-single-sign-on"></a>Parola çoklu oturum açmayı yapılandırma

Azure AD kurumsal uygulamalarınıza [bir galeri uygulaması](add-gallery-app.md) veya [Galeri olmayan bir Web uygulaması](add-non-gallery-app.md) eklediğinizde, kullanabileceğiniz çoklu oturum açma seçeneklerinden biri, [parola tabanlı çoklu oturum](what-is-single-sign-on.md#password-based-sso)açma seçenekleriniz vardır. Bu seçenek, HTML oturum açma sayfası olan herhangi bir Web için kullanılabilir. Parola oluşturma olarak da bilinen parola tabanlı SSO, Kimlik Federasyonu desteklemeyen Web uygulamalarına Kullanıcı erişimini ve parolalarını yönetmenizi sağlar. Ayrıca, çeşitli kullanıcıların, kuruluşunuzun sosyal medya uygulaması hesapları gibi tek bir hesabı paylaşması gereken senaryolar için de kullanışlıdır. 

Parola tabanlı SSO, uygulamaları Azure AD 'ye hızlı bir şekilde tümleştirmenize başlamak için harika bir yoldur ve şunları yapmanızı sağlar:

-   Azure AD ile tümleştirmiş olduğunuz uygulama için Kullanıcı adlarını ve parolaları güvenli bir şekilde depolayıp kaydederek **kullanıcılarınız Için çoklu oturum açmayı** etkinleştirin

-   Yalnızca Kullanıcı adı ve parola alanlarının oturum açmasını gerektiren uygulamalar için **Çoklu oturum açma alanları gerektiren uygulamalar desteklenir**

-   Kullanıcılarınızın kimlik bilgilerini girerken [uygulama erişimi panelinde](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) göreceği Kullanıcı adı ve parola giriş alanlarının **etiketlerini özelleştirin**

-   **Kullanıcılarınızın** , [uygulama erişimi paneline](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) el ile yazdıkları mevcut uygulama hesapları için kendi kullanıcı adlarını ve parolalarını sağlamasına izin verin

-   **İş grubunun bir üyesinin** , [self servis uygulama erişimi](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) özelliğini kullanarak bir kullanıcıya atanan kullanıcı adlarını ve parolaları belirtmesini sağlar

-   Bir **yöneticinin** kimlik bilgilerini güncelleştir özelliğini kullanarak uygulamada oturum açarken bireyler veya gruplar tarafından kullanılacak kullanıcı adını ve parolayı belirtmesini sağlar 

## <a name="before-you-begin"></a>Başlamadan önce

Uygulama Azure AD kiracınıza eklenmemişse, bkz. [Galeri uygulaması ekleme](add-gallery-app.md) veya [Galeri dışı bir uygulama ekleme](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Uygulamayı açın ve parola çoklu oturum açma seçeneğini belirleyin

1. [Azure Portal](https://portal.azure.com) bir bulut uygulaması Yöneticisi veya Azure AD kiracınız için bir uygulama Yöneticisi olarak oturum açın.

2. **Azure Active Directory** > **kurumsal uygulamalara**gidin. Azure AD kiracınızdaki uygulamaların rastgele bir örneği görüntülenir. 

3. **Uygulama türü** menüsünde, **tüm uygulamalar**' ı seçin ve ardından **Uygula**' yı seçin.

4. Arama kutusuna uygulamanın adını girin ve sonra sonuçlardan uygulamayı seçin.

5. **Yönet** bölümünde **Çoklu oturum açma**' yı seçin. 

6. **Parola tabanlı**' yı seçin.

7. Uygulamanın Web tabanlı oturum açma sayfasının URL 'sini girin. Bu dize, Kullanıcı adı giriş alanını içeren sayfa olmalıdır.

   ![Parola tabanlı çoklu oturum açma](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. **Kaydet**’i seçin. Azure AD, Kullanıcı adı girişi ve parola girişi için oturum açma sayfasını ayrıştırmaya çalışır. Deneme başarılı olursa işiniz bitti demektir. 
 
> [!NOTE]
> Sonraki adımınız, [uygulamaya Kullanıcı veya grup atamak](methods-for-assigning-users-and-groups.md)olur. Kullanıcılar ve gruplar atadıktan sonra, uygulamada oturum açtıklarında Kullanıcı adına kullanılacak kimlik bilgilerini sağlayabilirsiniz. **Kullanıcılar ve gruplar**' ı seçin, kullanıcının veya grubun satırı için onay kutusunu işaretleyin ve ardından **kimlik bilgilerini güncelleştir**' e tıklayın. Ardından Kullanıcı veya Grup adına kullanılacak kullanıcı adını ve parolayı girin. Aksi takdirde, kullanıcılardan başlatma sırasında kimlik bilgilerini girmesi istenir.
 

## <a name="manual-configuration"></a>El ile yapılandırma

Azure AD 'nin Ayrıştırma girişimi başarısız olursa, oturum açmayı el ile yapılandırabilirsiniz.

1. **\<Uygulama adı > yapılandırma**altında, **oturum açma yapılandırma** sayfasını göstermek için **uygulama adı > parola çoklu oturum açma ayarları Yapılandır \<** ' ı seçin. 

2. **Oturum açma alanlarını el ile Algıla**' yı seçin. Oturum açma alanlarının el ile algılanmasını açıklayan ek yönergeler görüntülenir.

   ![Parola tabanlı çoklu oturum açma için el ile yapılandırma](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. **Kayıt açma alanlarını yakala '** yı seçin. **Şu anda devam eden ileti meta veri yakalama olduğunu**gösteren yeni bir sekmede yakalama durumu sayfası açılır.

4. **Erişim paneli uzantısı gerekli** kutusu yeni bir sekmede görünürse **uygulamalarım güvenli oturum açma uzantısı** tarayıcı uzantısını yüklemek için **Şimdi yüklensin** ' i seçin. (Tarayıcı uzantısı Microsoft Edge, Chrome veya Firefox gerektirir.) Sonra uzantıyı yükleyip etkinleştirin, sonra da yakalama durumu sayfasını yenileyin.

   Tarayıcı uzantısı daha sonra, girilen URL 'YI görüntüleyen bir sekme açar.
5. Girilen URL 'nin bulunduğu sekmede, oturum açma işlemine gidin. Kullanıcı adı ve parola alanlarını doldurup oturum açmayı deneyin. (Doğru parolayı sağlamanız gerekmez.)

   Bir istem, yakalanan oturum açma alanlarını kaydetmenizi ister.
6. **Tamam**’ı seçin. Tarayıcı uzantısı, **uygulama için Ileti meta verileri güncelleştirildiğinde**yakalama durumu sayfasını güncelleştirir. Tarayıcı sekmesi kapanır.

7. Azure AD **oturum açma yapılandırma** sayfasında **Tamam ' ı seçin, uygulamada başarıyla oturum açabildim**.

8. **Tamam**’ı seçin.

Oturum açma sayfasının yakalandıktan sonra, kullanıcıları ve grupları atayabilir ve yalnızca normal [parola SSO uygulamaları](what-is-single-sign-on.md)gibi kimlik bilgileri ilkeleri ayarlayabilirsiniz.

> [!NOTE]
> Uygulamanın **yapılandırma** sekmesindeki **logoyu karşıya yükle** düğmesini kullanarak uygulama için bir kutucuk logosu yükleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaya Kullanıcı veya Grup atama](methods-for-assigning-users-and-groups.md)
- [Otomatik Kullanıcı hesabı sağlamayı yapılandırma](configure-automatic-user-provisioning-portal.md)
