---
title: Azure Active Directory B2C için sık sorulan sorular (SSS)
description: Azure Active Directory B2C hakkında sık sorulan soruların yanıtları.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 577dc6d4344cd6d7a38c4d538218c9dd89e883aa
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643636"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: sık sorulan sorular (SSS)

Bu sayfa Azure Active Directory B2C (Azure AD B2C) hakkında sık sorulan soruları yanıtlar. Güncelleştirmeleri yeniden denetlemeye devam edin.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Azure portal Azure AD B2C uzantısına neden erişemiyorum?

Azure AD uzantısının neden çalışmadığına ilişkin iki yaygın neden vardır. Azure AD B2C, dizindeki kullanıcı rolünüzün genel yönetici olmasını gerektirir. Erişiminizin olması gerektiğini düşünüyorsanız lütfen yöneticinize başvurun. Genel yönetici ayrıcalıklarınız varsa, Azure Active Directory bir dizin değil Azure AD B2C dizinde olduğunuzdan emin olun. [Azure AD B2C kiracı oluşturma](tutorial-create-tenant.md)yönergelerini görebilirsiniz.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Mevcut, çalışan tabanlı Azure AD kiracısındaki Azure AD B2C özellikleri kullanabilir miyim?

Azure AD ve Azure AD B2C ayrı ürün tekliflerdir ve aynı kiracıda birlikte bulunamaz. Bir Azure AD kiracısı bir kuruluşu temsil eder. Azure AD B2C kiracısı, bağlı olan taraf uygulamalarıyla kullanılacak kimliklerin bir koleksiyonunu temsil eder. Özel ilkelerle Azure AD B2C, bir kuruluştaki çalışanların kimlik doğrulamasına izin veren Azure AD 'ye federe verebilir.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Office 365 'ye sosyal oturum açma (Facebook ve Google +) sağlamak için Azure AD B2C kullanabilir miyim?

Azure AD B2C, Microsoft Office 365 için kullanıcıların kimliğini doğrulamak için kullanılamaz. Azure AD, çalışanların SaaS uygulamalarına erişmesini yönetmeye yönelik bir çözümdür ve bu amaçla lisanslama ve koşullu erişim gibi tasarlanan özelliklere sahiptir. Azure AD B2C, Web uygulamaları ve mobil uygulamalar oluşturmak için bir kimlik ve erişim yönetimi platformu sağlar. Azure AD B2C bir Azure AD kiracısına federasyona yapılandırıldığında, Azure AD kiracısı, Azure AD B2C bağımlı uygulamalara çalışan erişimini yönetir.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Azure AD B2C yerel hesaplar nelerdir? Azure AD 'de iş veya okul hesaplarından ne farklılık vardır?

Bir Azure AD kiracısında kiracıya ait olan kullanıcılar `<xyz>@<tenant domain>`bir e-posta adresiyle oturum açın. `<tenant domain>`, kiracının veya ilk `<...>.onmicrosoft.com` etki alanındaki doğrulanmış etki alanlarından biridir. Bu hesap türü bir iş veya okul hesabıdır.

Azure AD B2C kiracısında çoğu uygulama, kullanıcının rastgele herhangi bir e-posta adresiyle oturum açmasını ister (örneğin, joe@comcast.net, bob@gmail.com, sarah@contoso.comveya jim@live.com). Bu hesap türü yerel bir hesaptır. Yerel hesap olarak (örneğin, Ali, Bob, Sarah veya Jim) rastgele Kullanıcı adlarını da destekliyoruz. Azure portal Azure AD B2C için kimlik sağlayıcılarını yapılandırırken bu iki yerel hesap türünden birini seçebilirsiniz. Azure AD B2C kiracınızda **kimlik sağlayıcıları**' nı seçin, **yerel hesap**' ı seçin ve ardından **Kullanıcı adı**' nı seçin.

Uygulamalar için Kullanıcı hesaplarının her zaman bir kaydolma Kullanıcı akışı, kaydolma veya oturum açma Kullanıcı akışından veya Azure AD Graph API kullanılarak oluşturulması gerekir. Azure portal oluşturulan kullanıcı hesapları yalnızca kiracıyı yönetmek için kullanılır.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Şu anda hangi sosyal kimlik sağlayıcılarını destekliyoruz? Gelecekte hangi olanları desteklemeyi planlıyorsunuz?

Şu anda Amazon, Facebook, GitHub (Önizleme), Google, LinkedIn, Microsoft hesabı (MSA), QQ (Önizleme), Twitter, WeChat (Önizleme) ve Weibo (Önizleme) gibi birçok sosyal kimlik sağlayıcısını destekliyoruz. Müşteri talebine bağlı diğer popüler sosyal kimlik sağlayıcıları için destek eklemeyi değerlendiriyoruz.

Azure AD B2C Ayrıca [özel ilkeleri](active-directory-b2c-overview-custom.md)destekler. Özel ilkeler, [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) veya SAML 'yi destekleyen herhangi bir kimlik sağlayıcısı için kendi ilkenizi oluşturmanızı sağlar. [Özel ilke başlangıç paketimizi](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)inceleyerek özel ilkeleri kullanmaya başlayın.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Farklı sosyal kimlik sağlayıcılarından tüketiciler hakkında daha fazla bilgi toplamak için kapsamları yapılandırabilir miyim?

Hayır. Desteklenen sosyal kimlik sağlayıcıları kümesi için kullanılan varsayılan kapsamlar şunlardır:

* Facebook: e-posta
* Google +: e-posta
* Microsoft hesabı: OpenID e-posta profili
* Amazon: profili
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Uygulamamın Azure AD B2C ile çalışması için Azure 'da çalıştırılması gerekiyor mu?

Hayır, uygulamanızı dilediğiniz yerde (bulutta veya şirket içinde) barındırabilirsiniz. Uygulamanın Azure AD B2C ile etkileşime geçmesi için yalnızca genel olarak erişilebilir uç noktalarında HTTP isteği gönderip alabilmesi yeterlidir.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Birden çok Azure AD B2C kiracım var. Azure portal bunları nasıl yönetebilirim?

Azure portal sol taraftaki menüde ' Azure AD B2C ' öğesini açmadan önce, yönetmek istediğiniz dizine geçmeniz gerekir. Azure portal sağ üst köşesindeki kimliğinize tıklayarak Dizin değiştirin, ardından açılan kutuda bir dizin seçin.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Azure AD B2C tarafından gönderilen doğrulama e-postalarını (içerik ve "Kimden:" alanı) özelleştirmek Nasıl yaparım??

Kimlik doğrulama e-postalarının içeriğini özelleştirmek için [Şirket markası özelliğini](../active-directory/fundamentals/customize-branding.md) kullanabilirsiniz. Özellikle, e-postanın bu iki öğesi özelleştirilebilir:

* **Başlık logosu**: sağ alt tarafta gösterilir.
* **Arka plan rengi**: üst kısımda gösteriliyor.

    ![Özelleştirilmiş bir doğrulama e-postası ekran görüntüsü](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

E-posta imzası, Azure AD B2C kiracıyı ilk oluşturduğunuzda verdiğiniz Azure AD B2C kiracının adını içerir. Bu yönergeleri kullanarak adı değiştirebilirsiniz:

1. [Azure Portal](https://portal.azure.com/) genel yönetici olarak oturum açın.
1. **Azure Active Directory** dikey penceresini açın.
1. **Özellikler** sekmesine tıklayın.
1. **Ad** alanını değiştirin.
1. Sayfanın üst kısmından **Kaydet**'e tıklayın.

Şu anda e-postadaki "Kimden:" alanını değiştirme yöntemi yoktur.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Var olan Kullanıcı adlarınızı, parolalarımı ve Profillerimi Veritabanım 'den Azure AD B2C 'a nasıl geçirebilirim?

Geçiş aracınızı yazmak için Azure AD Graph API kullanabilirsiniz. Ayrıntılar için [Kullanıcı geçiş kılavuzuna](active-directory-b2c-user-migration.md) bakın.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Azure AD B2C yerel hesaplar için hangi parola Kullanıcı akışı kullanılır?

Yerel hesaplar için Azure AD B2C parolası Kullanıcı akışı, Azure AD ilkesini temel alır. Azure AD B2C's kaydolma, kaydolma veya oturum açma ve parola sıfırlama Kullanıcı akışları "güçlü" parola gücünü kullanır ve herhangi bir parolayı sona ermez. Daha fazla bilgi için [Azure AD parola ilkesini](/previous-versions/azure/jj943764(v=azure.100)) okuyun. Hesap kilitlenmelerini ve parolaları hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C tehditler ve veriler için tehditleri yönetme](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Şirket içi Active Directory depolanan tüketici kimliklerini Azure AD B2C 'e geçirmek için Azure AD Connect kullanabilir miyim?

Hayır, Azure AD Connect Azure AD B2C çalışmak üzere tasarlanmamıştır. Kullanıcı geçişi için [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) kullanmayı göz önünde bulundurun. Ayrıntılar için [Kullanıcı geçiş kılavuzuna](active-directory-b2c-user-migration.md) bakın.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Uygulamam iFrame içindeki Azure AD B2C sayfaları açabilir mi?

Hayır, güvenlik nedenleriyle Azure AD B2C sayfalar iFrame içinde açılamaz. Hizmetimiz, IFRAME 'leri engellemek için tarayıcıyla iletişim kurar. Genel ve OAUTH2 belirtiminde güvenlik topluluğu, tıklama-Jacking riski nedeniyle kimlik deneyimleri için IFRAME 'Ler kullanmayı öneririz.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C, Microsoft Dynamics gibi CRM sistemleriyle birlikte çalışıyor mu?

Microsoft Dynamics 365 Portalı ile tümleştirme kullanılabilir. [Kimlik doğrulaması için Azure AD B2C kullanmak üzere Dynamics 365 portalını yapılandırma](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c)konusuna bakın.

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C SharePoint şirket içi 2016 veya önceki bir sürümü ile çalışıyor mu?

Azure AD B2C, SharePoint dış iş ortağı-paylaşım senaryosu için tasarlanmamıştır; Bunun yerine bkz. [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Dış kimlikleri yönetmek için Azure AD B2C veya B2B kullanmalıdır mi?

Dış kimlik senaryolarınıza uygun özellikleri uygulama hakkında daha fazla bilgi edinmek için [dış kimlikler](../active-directory/active-directory-b2b-compare-external-identities.md) hakkında bu makaleyi okuyun.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Azure AD B2C raporlama ve denetleme özellikleri ne sağlar? Azure AD Premium ile aynı mı?

Hayır, Azure AD B2C Azure AD Premium aynı rapor kümesini desteklemez. Ancak birçok ortak vardır:

* **Oturum açma raporları** , sınırlı ayrıntıların bulunduğu her oturum açma kaydını sağlar.
* **Denetim raporları** hem yönetici etkinliğini hem de uygulama etkinliğini içerir.
* **Kullanım raporları** , kullanıcı sayısını, oturum açma SAYıSıNı ve MFA hacmini içerir.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Azure AD B2C tarafından sunulan sayfaların Kullanıcı arabirimini yerelleştirebilirim miyim? Hangi diller desteklenir?

Evet, bkz. [dil özelleştirmesi](active-directory-b2c-reference-language-customization.md). 36 dil için çeviriler sunuyoruz ve gereksinimlerinize uyacak şekilde herhangi bir dizeyi geçersiz kılabilirsiniz.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Azure AD B2C tarafından sunulan kaydolma ve oturum açma sayfalarınızda kendi URL 'lerimi kullanabilir miyim? Örneğin, contoso.b2clogin.com URL 'sini login.contoso.com olarak değiştirebilir miyim?

Şu anda hayır. Bu özellik yol haritasında bulunur. Azure portal **etki alanı sekmesinde etki** alanınızı doğrulamak, bu hedefin yerine getirmiyor. Bununla birlikte, b2clogin.com ile [bağımsız bir en üst düzey etki alanı](b2clogin.md)sunuyoruz ve bu nedenle dış görünüm Microsoft 'un bahsetmeden uygulanabilir.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Azure AD B2C kiracımı Nasıl yaparım? silinsin mi?

Azure AD B2C kiracınızı silmek için aşağıdaki adımları izleyin.

Geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. [Azure Portal](https://portal.azure.com/) *Abonelik Yöneticisi*olarak oturum açın. Azure 'a kaydolmak için kullandığınız iş veya okul hesabını ya da aynı Microsoft hesabı kullanın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. Azure AD B2C kiracınızdaki tüm **Kullanıcı akışlarını (ilkeleri)** silin.
1. Azure AD B2C kiracınızda kayıtlı olan tüm **uygulamaları** silin.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Yönet** bölümünde **Kullanıcılar**’ı seçin.
1. Her bir kullanıcıyı sırasıyla seçin (Şu anda oturum açmış olduğunuz *abonelik yönetici* kullanıcısını hariç tut). Sayfanın alt kısmındaki **Sil** ' i seçin ve istendiğinde **Evet** ' i seçin.
1. **Yönet**altında **Uygulama kayıtları** (veya **uygulama kayıtları (eski)** ) öğesini seçin.
1. **Tüm uygulamaları görüntüle** seçeneğini belirleyin
1. **B2C-Extensions-App**adlı uygulamayı seçin, **Sil**' i seçin ve istendiğinde **Evet** ' i seçin.
1. **Yönet**altında **Kullanıcı ayarları**' nı seçin.
1. Varsa, **LinkedIn hesap bağlantıları**altında **Hayır**' ı seçin ve ardından **Kaydet**' i seçin.
1. **Yönet**altında **Özellikler** ' i seçin.
1. **Azure kaynakları Için erişim yönetimi**altında **Evet**' i seçin ve ardından **Kaydet**' i seçin.
1. Azure portal oturumunuzu kapatıp erişiminizi yenilemek için yeniden oturum açın.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Genel bakış** sayfasında, **dizini Sil**' i seçin. İşlemi gerçekleştirmek için ekrandaki yönergeleri izleyin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. [Azure Portal](https://portal.azure.com/) *Abonelik Yöneticisi*olarak oturum açın. Azure 'a kaydolmak için kullandığınız iş veya okul hesabını ya da aynı Microsoft hesabı kullanın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. Azure AD B2C kiracınızdaki tüm **Kullanıcı akışlarını (ilkeleri)** silin.
1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **tüm uygulamalar** sekmesini seçin.
1. Kaydolduysanız tüm uygulamaları silin.
1. **B2C-Extensions-App**' i silin.
1. **Yönet** bölümünde **Kullanıcılar**’ı seçin.
1. Her bir kullanıcıyı sırasıyla seçin (Şu anda oturum açmış olduğunuz *abonelik yönetici* kullanıcısını hariç tut). Sayfanın alt kısmındaki **Sil** ' i seçin ve istendiğinde **Evet** ' i seçin.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Yönet**altında **Kullanıcı ayarları**' nı seçin.
1. Varsa, **LinkedIn hesap bağlantıları**altında **Hayır**' ı seçin ve ardından **Kaydet**' i seçin.
1. **Yönet**altında **Özellikler** ' i seçin.
1. **Azure kaynakları Için erişim yönetimi**altında **Evet**' i seçin ve ardından **Kaydet**' i seçin.
1. Azure portal oturumunuzu kapatıp erişiminizi yenilemek için yeniden oturum açın.
1. Sol taraftaki menüden **Azure Active Directory** ' yi seçin.
1. **Genel bakış** sayfasında, **dizini Sil**' i seçin. İşlemi gerçekleştirmek için ekrandaki yönergeleri izleyin.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Enterprise Mobility Suite 'in bir parçası olarak Azure AD B2C alabilir miyim?

Hayır, Azure AD B2C Kullandıkça Öde Azure hizmetidir ve Enterprise Mobility Suite 'in bir parçası değildir.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Azure AD B2C ile ilgili sorun rapor Nasıl yaparım??

[Azure Active Directory B2C Için dosya desteği isteklerini](active-directory-b2c-support.md)görüntüleyin.
