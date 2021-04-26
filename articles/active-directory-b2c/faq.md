---
title: Azure Active Directory B2C için sık sorulan sorular (SSS)
description: Azure Active Directory B2C hakkında sık sorulan soruların yanıtları.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d84c5fff41a7f00405c29ab4b4921ed2e28bdfde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580105"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: sık sorulan sorular (SSS)

Bu sayfa Azure Active Directory B2C (Azure AD B2C) hakkında sık sorulan soruları yanıtlar. Güncelleştirmeleri yeniden denetlemeye devam edin.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Azure portal Azure AD B2C uzantısına neden erişemiyorum?

Azure AD uzantısının neden çalışmadığına ilişkin iki yaygın neden vardır. Azure AD B2C, dizindeki kullanıcı rolünüzün genel yönetici olmasını gerektirir. Erişiminizin olması gerektiğini düşünüyorsanız lütfen yöneticinize başvurun. Genel yönetici ayrıcalıklarınız varsa, Azure Active Directory bir dizin değil Azure AD B2C dizinde olduğunuzdan emin olun. [Azure AD B2C kiracı oluşturma](tutorial-create-tenant.md)yönergelerini görebilirsiniz.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Mevcut, çalışan tabanlı Azure AD kiracısındaki Azure AD B2C özellikleri kullanabilir miyim?

Azure AD ve Azure AD B2C ayrı ürün tekliflerdir ve aynı kiracıda birlikte bulunamaz. Bir Azure AD kiracısı bir kuruluşu temsil eder. Azure AD B2C kiracısı, bağlı olan taraf uygulamalarıyla kullanılacak kimliklerin bir koleksiyonunu temsil eder. **Azure AD B2C > kimlik sağlayıcılarının** altına veya özel Ilkelerle **Yeni OpenID Connect sağlayıcısı** ekleyerek Azure AD B2C, bir kuruluştaki çalışanların kimlik DOĞRULAMASıNA izin veren Azure AD 'ye federe verebilir.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Azure AD B2C, sosyal oturum açma (Facebook ve Google +) Microsoft 365 için kullanabilir miyim?

Azure AD B2C, Microsoft 365 için kullanıcıların kimliğini doğrulamak için kullanılamaz. Azure AD, çalışanların SaaS uygulamalarına erişmesini yönetmeye yönelik bir çözümdür ve bu amaçla lisanslama ve koşullu erişim gibi tasarlanan özelliklere sahiptir. Azure AD B2C, Web uygulamaları ve mobil uygulamalar oluşturmak için bir kimlik ve erişim yönetimi platformu sağlar. Azure AD B2C bir Azure AD kiracısına federasyona yapılandırıldığında, Azure AD kiracısı, Azure AD B2C bağımlı uygulamalara çalışan erişimini yönetir.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C yerel hesaplar nelerdir? Azure AD 'de iş veya okul hesaplarından ne farklılık vardır?

Bir Azure AD kiracısında kiracıya ait olan kullanıcılar formun bir e-posta adresiyle oturum açın `<xyz>@<tenant domain>` . , `<tenant domain>` Kiracının veya ilk etki alanındaki doğrulanmış etki alanlarından biridir `<...>.onmicrosoft.com` . Bu hesap türü bir iş veya okul hesabıdır.

Azure AD B2C kiracısında çoğu uygulama, kullanıcının herhangi bir rastgele e-posta adresiyle oturum açmasını ister (örneğin,,, joe@comcast.net bob@gmail.com sarah@contoso.com veya jim@live.com ). Bu hesap türü yerel bir hesaptır. Yerel hesap olarak (örneğin, Ali, Bob, Sarah veya Jim) rastgele Kullanıcı adlarını da destekliyoruz. Azure portal Azure AD B2C için kimlik sağlayıcılarını yapılandırırken bu iki yerel hesap türünden birini seçebilirsiniz. Azure AD B2C kiracınızda **kimlik sağlayıcıları**' nı seçin, **yerel hesap**' ı seçin ve ardından **Kullanıcı adı**' nı seçin.

Uygulamalar için Kullanıcı hesapları, kaydolma Kullanıcı akışı, kaydolma veya oturum açma Kullanıcı akışı, Microsoft Graph API 'SI veya Azure portal aracılığıyla oluşturulabilir.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Şu anda hangi sosyal kimlik sağlayıcılarını destekliyoruz? Gelecekte hangi olanları desteklemeyi planlıyorsunuz?

Şu anda Amazon, Facebook, GitHub (Önizleme), Google, LinkedIn, Microsoft hesabı (MSA), QQ (Önizleme), Twitter, WeChat (Önizleme) ve Weibo (Önizleme) gibi birçok sosyal kimlik sağlayıcısını destekliyoruz. Müşteri talebine bağlı diğer popüler sosyal kimlik sağlayıcıları için destek eklemeyi değerlendiriyoruz.

Azure AD B2C Ayrıca [özel ilkeleri](custom-policy-overview.md)destekler. Özel ilkeler, [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) veya SAML 'yi destekleyen herhangi bir kimlik sağlayıcısı için kendi ilkenizi oluşturmanızı sağlar. [Özel ilke başlangıç paketimizi](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)inceleyerek özel ilkeleri kullanmaya başlayın.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Farklı sosyal kimlik sağlayıcılarından tüketiciler hakkında daha fazla bilgi toplamak için kapsamları yapılandırabilir miyim?

Hayır. Desteklenen sosyal kimlik sağlayıcıları kümesi için kullanılan varsayılan kapsamlar şunlardır:

* Facebook: e-posta
* Google +: e-posta
* Microsoft hesabı: OpenID e-posta profili
* Amazon: profili
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Uygulamamın Azure AD B2C ile çalışması için Azure 'da çalıştırılması gerekiyor mu?

Hayır, uygulamanızı dilediğiniz yerde (bulutta veya şirket içinde) barındırabilirsiniz. Tek yapmanız gereken Azure AD B2C, genel olarak erişilebilen uç noktalarda HTTP istekleri gönderme ve alma olanağıdır.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Birden çok Azure AD B2C kiracım var. Azure portal bunları nasıl yönetebilirim?

Azure portal sol taraftaki menüde ' Azure AD B2C ' öğesini açmadan önce, yönetmek istediğiniz dizine geçmeniz gerekir. Azure portal sağ üst köşesindeki kimliğinize tıklayarak Dizin değiştirin, ardından açılan kutuda bir dizin seçin.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Azure AD B2C tarafından gönderilen doğrulama e-postalarını (içerik ve "Kimden:" alanı) özelleştirmek Nasıl yaparım??

Kimlik doğrulama e-postalarının içeriğini özelleştirmek için [Şirket markası özelliğini](../active-directory/fundamentals/customize-branding.md) kullanabilirsiniz. Özellikle, e-postanın bu iki öğesi özelleştirilebilir:

* **Başlık logosu**: sağ alt tarafta gösterilir.
* **Arka plan rengi**: üst kısımda gösteriliyor.

    ![Özelleştirilmiş bir doğrulama e-postası ekran görüntüsü](./media/faq/company-branded-verification-email.png)

E-posta imzası, Azure AD B2C kiracıyı ilk oluşturduğunuzda verdiğiniz Azure AD B2C kiracının adını içerir. Bu yönergeleri kullanarak adı değiştirebilirsiniz:

1. [Azure Portal](https://portal.azure.com/) genel yönetici olarak oturum açın.
1. **Azure Active Directory** dikey penceresini açın.
1. **Özellikler** sekmesine tıklayın.
1. **Ad** alanını değiştirin.
1. Sayfanın üst kısmından **Kaydet**'e tıklayın.

Şu anda e-postadaki "Kimden:" alanını değiştiremezsiniz.

> [!TIP]
> Azure AD B2C [özel ilkeyle](custom-policy-overview.md), e-postanın "Kimden:" alanı da dahil olmak üzere Azure AD B2C e-postayı özelleştirebilirsiniz. Özel e-posta doğrulaması, [Mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md)veya [mini](https://sparkpost.com)posta gibi bir üçüncü taraf e-posta sağlayıcısının kullanılmasını gerektirir.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Var olan Kullanıcı adlarınızı, parolalarımı ve Profillerimi Veritabanım 'den Azure AD B2C 'a nasıl geçirebilirim?

Geçiş aracınızı yazmak için Microsoft Graph API 'sini kullanabilirsiniz. Ayrıntılar için [Kullanıcı geçiş kılavuzuna](user-migration.md) bakın.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C yerel hesaplar için hangi parola Kullanıcı akışı kullanılır?

Yerel hesaplar için Azure AD B2C parolası Kullanıcı akışı, Azure AD ilkesini temel alır. Azure AD B2C's kaydolma, kaydolma veya oturum açma ve parola sıfırlama Kullanıcı akışları "güçlü" parola gücünü kullanır ve herhangi bir parolayı sona ermez. Daha ayrıntılı bilgi için bkz. [Azure Active Directory parola ilkeleri ve kısıtlamaları](../active-directory/authentication/concept-sspr-policy.md).

Hesap kilitlenmelerini ve parolaları hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C tehditler ve veriler için tehditleri yönetme](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Şirket içi Active Directory depolanan tüketici kimliklerini Azure AD B2C 'e geçirmek için Azure AD Connect kullanabilir miyim?

Hayır, Azure AD Connect Azure AD B2C çalışmak üzere tasarlanmamıştır. Kullanıcı geçişi için [MICROSOFT Graph API](microsoft-graph-operations.md) 'sini kullanmayı düşünün. Ayrıntılar için [Kullanıcı geçiş kılavuzuna](user-migration.md) bakın.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Uygulamam iFrame içindeki Azure AD B2C sayfaları açabilir mi?

Bu özellik genel önizleme aşamasındadır. Ayrıntılar için bkz. [ekli oturum açma deneyimi](https://docs.microsoft.com/azure/active-directory-b2c/embedded-login).

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C, Microsoft Dynamics gibi CRM sistemleriyle birlikte çalışıyor mu?

Microsoft Dynamics 365 Portalı ile tümleştirme kullanılabilir. [Kimlik doğrulaması için Azure AD B2C kullanmak üzere Dynamics 365 portalını yapılandırma](/dynamics365/customer-engagement/portals/azure-ad-b2c)konusuna bakın.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C SharePoint şirket içi 2016 veya önceki bir sürümü ile çalışıyor mu?

Azure AD B2C, SharePoint dış iş ortağı-paylaşım senaryosu için tasarlanmamıştır; Bunun yerine bkz. [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Dış kimlikleri yönetmek için Azure AD B2C veya B2B kullanmalıdır mi?

Dış kimlik senaryolarınıza uygun özellikleri uygulama hakkında daha fazla bilgi edinmek için [Azure AD 'de B2B işbirliğinin ve B2C 'nin nasıl karşılaştırılacağını](../active-directory/external-identities/compare-with-b2c.md) okuyun.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C raporlama ve denetleme özellikleri ne sağlar? Azure AD Premium ile aynı mı?

Hayır, Azure AD B2C Azure AD Premium aynı rapor kümesini desteklemez. Ancak birçok ortak vardır:

* **Oturum açma raporları** , sınırlı ayrıntıların bulunduğu her oturum açma kaydını sağlar.
* **Denetim raporları** hem yönetici etkinliğini hem de uygulama etkinliğini içerir.
* **Kullanım raporları** , kullanıcı sayısını, oturum açma SAYıSıNı ve MFA hacmini içerir.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Azure AD B2C tarafından sunulan sayfaların Kullanıcı arabirimini yerelleştirebilirim miyim? Hangi diller destekleniyor?

Evet, bkz. [dil özelleştirmesi](language-customization.md). 36 dil için çeviriler sunuyoruz ve gereksinimlerinize uyacak şekilde herhangi bir dizeyi geçersiz kılabilirsiniz.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Azure AD B2C tarafından sunulan kaydolma ve oturum açma sayfalarınızda kendi URL 'lerimi kullanabilir miyim? Örneğin, contoso.b2clogin.com URL 'sini login.contoso.com olarak değiştirebilir miyim?

Bu özellik genel önizlemede kullanılabilir. Ayrıntılar için bkz. [özel etki alanları Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-user-flow).

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Azure AD B2C kiracımı Nasıl yaparım? silinsin mi?

Azure AD B2C kiracınızı silmek için aşağıdaki adımları izleyin.

Yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalar (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com/) *Abonelik Yöneticisi* olarak oturum açın. Azure 'a kaydolmak için kullandığınız iş veya okul hesabını ya da aynı Microsoft hesabı kullanın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. Azure AD B2C kiracınızdaki tüm **Kullanıcı akışlarını (ilkeleri)** silin.
1. Azure AD B2C kiracınızdaki tüm **kimlik sağlayıcılarını** silin.
1. **Uygulama kayıtları**' yi seçin ve ardından **tüm uygulamalar** sekmesini seçin.
1. Kaydolduysanız tüm uygulamaları silin.
1. **B2C-Extensions-App**' i silin.
1. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
1. Her bir kullanıcıyı sırasıyla seçin (Şu anda oturum açmış olduğunuz *abonelik yönetici* kullanıcısını hariç tut). Sayfanın alt kısmındaki **Sil** ' i seçin ve istendiğinde **Evet** ' i seçin.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Yönet** altında **Özellikler** ' i seçin.
1. **Azure kaynakları için erişim yönetimi**'nin altında **Evet**'i ve sonra da **Kaydet**'i seçin.
1. Azure portal oturumunuzu kapatıp erişiminizi yenilemek için yeniden oturum açın.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Genel bakış** sayfasında, **kiracı Sil**' i seçin. İşlemi gerçekleştirmek için ekrandaki yönergeleri izleyin.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com/) *Abonelik Yöneticisi* olarak oturum açın. Azure 'a kaydolmak için kullandığınız iş veya okul hesabını ya da aynı Microsoft hesabı kullanın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. Azure AD B2C kiracınızdaki tüm **Kullanıcı akışlarını (ilkeleri)** silin.
1. Azure AD B2C kiracınızda kayıtlı olan tüm **uygulamaları (eski)** silin.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Yönet** bölümünde **Kullanıcılar**'ı seçin.
1. Her bir kullanıcıyı sırasıyla seçin (Şu anda oturum açmış olduğunuz *abonelik yönetici* kullanıcısını hariç tut). Sayfanın alt kısmındaki **Sil** ' i seçin ve istendiğinde **Evet** ' i seçin.
1. **Yönet**'in altında **Uygulama kayıtları** nı seçin.
1. **Tüm uygulamaları görüntüle** seçeneğini belirleyin
1. **B2C-Extensions-App** adlı uygulamayı seçin, **Sil**' i seçin ve istendiğinde **Evet** ' i seçin.
1. **Yönet** altında **Kullanıcı ayarları**' nı seçin.
1. Varsa, **LinkedIn hesap bağlantıları** altında **Hayır**' ı seçin ve ardından **Kaydet**' i seçin.
1. **Yönet** altında **Özellikler** ' i seçin.
1. **Azure kaynakları için erişim yönetimi**'nin altında **Evet**'i ve sonra da **Kaydet**'i seçin.
1. Azure portal oturumunuzu kapatıp erişiminizi yenilemek için yeniden oturum açın.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Genel bakış** sayfasında, **dizini Sil**' i seçin. İşlemi gerçekleştirmek için ekrandaki yönergeleri izleyin.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Enterprise Mobility Suite 'in bir parçası olarak Azure AD B2C alabilir miyim?

Hayır, Azure AD B2C Kullandıkça Öde Azure hizmetidir ve Enterprise Mobility Suite 'in bir parçası değildir.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Azure AD B2C ile ilgili sorun rapor Nasıl yaparım??

[Azure Active Directory B2C Için dosya desteği isteklerini](support-options.md)görüntüleyin.
