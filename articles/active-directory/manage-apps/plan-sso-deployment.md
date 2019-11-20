---
title: Azure Active Directory çoklu oturum açma dağıtımı planlayın
description: Kuruluşunuzda SSO 'yu planlayıp, dağıtmanıza ve yönetmenize yardımcı olacak kılavuz.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733b0d7650d68bddae60cf524947590c2b689968
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779373"
---
# <a name="plan-a-single-sign-on-deployment"></a>Çoklu oturum açma dağıtımını planlama

Çoklu oturum açma (SSO), tek bir kullanıcı hesabı kullanarak yalnızca bir kez oturum açarak Kullanıcı ihtiyaçlarına sahip tüm uygulamalara ve kaynaklara erişim anlamına gelir. SSO ile, kullanıcılar ikinci bir kez kimlik doğrulaması yapmak zorunda kalmadan gerekli tüm uygulamalara erişebilirler.

## <a name="benefits-of-sso"></a>SSO avantajları

Çoklu oturum açma (SSO), kullanıcılar Azure Active Directory (Azure AD) içinde uygulamalarda oturum açtığında güvenlik ve kolaylık sağlar. 

Birçok kuruluş, son kullanıcı üretkenliği için Office 365, Box ve Salesforce gibi hizmet olarak yazılım (SaaS) uygulamalarını kullanır. Geçmişte, BT personeli her bir SaaS uygulamasında kullanıcı hesaplarını tek tek oluşturmak ve güncelleştirmek için gereklidir ve kullanıcıların her biri için bir parolayı anımsamasını gerekiyordu.

Azure Marketi, önceden tümleştirilmiş SSO bağlantıları olan 3000 uygulamasına sahiptir ve bunları kiracınızda tümleştirmeyi kolaylaştırır.

## <a name="licensing"></a>Lisanslama

- **Azure AD lisanslama** -önceden tümleştirilmiş SaaS uygulamaları için SSO ücretsizdir. Ancak, dizininizdeki nesne sayısı ve dağıtmak istediğiniz özellikler ek lisanslar gerektirebilir. Lisans gereksinimlerinin tam listesi için bkz. [Azure Active Directory fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/).
- **Uygulama lisanslama** -iş gereksinimlerinizi karşılamak için SaaS uygulamalarınız için uygun lisanslara ihtiyacınız vardır. Uygulamaya atanan kullanıcıların, uygulama içindeki rolleri için uygun lisanslara sahip olup olmadığını öğrenmek için uygulama sahibiyle birlikte çalışın. Azure AD, rollere göre otomatik sağlamayı yönettiğinde, Azure AD 'de atanan roller, uygulamanın sahip olduğu lisansların sayısıyla birlikte hizalanmalıdır. Uygulamaya ait hatalı sayıda lisans, Kullanıcı sağlama/güncelleştirme sırasında hatalara neden olabilir.

## <a name="plan-your-sso-team"></a>SSO takımınızı planlayın

- **Doğru paydaşlara** katılım-teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri nedeniyle olur. Bu sınırları önlemek için, [doğru hissedarları](https://aka.ms/deploymentplans) ve paydaşların rollerini anlamasına emin olun.
- **Iletişim planı** , her yeni hizmetin başarısı için önemlidir. Deneyimlerinizin nasıl değiştirileceği, ne zaman değiştirileceği ve sorunlarla karşılaştıkları durumlarda nasıl destek kazanabilecekleri hakkında kullanıcılarınız için etkin bir şekilde iletişim kurun. [Son KULLANıCıLARıN SSO etkin uygulamalarına nasıl erişeceğini](end-user-experiences.md)ve iletişimlerinizi seçiminizle eşleşecek şekilde oluşturma seçeneklerini gözden geçirin. 

## <a name="plan-your-sso-protocol"></a>SSO protokolünü planlayın

Federasyon protokollerini temel alan SSO uygulaması güvenlik, güvenilirlik ve son kullanıcı deneyimlerini geliştirir ve daha kolay uygulanır. Birçok uygulama, Azure AD 'de [adım](../saas-apps/tutorial-list.md)adım kılavuzlarla önceden tümleştirilir. Bunları [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/)'nde bulabilirsiniz. Her SSO yöntemiyle ilgili ayrıntılı bilgi, [Azure Active Directory içindeki uygulamalarda çoklu oturum açma](what-is-single-sign-on.md)makalesinde bulunabilir.

Kullanıcılarınızın uygulamalarınıza çoklu oturum açma imkanı sağlamanın iki temel yolu vardır:

- **Federasyon çoklu oturum açma ile** Azure AD, Azure AD hesabını kullanarak kullanıcının kimliğini doğrular. Bu yöntem SAML 2,0, WS-Federation veya OpenID Connect gibi protokolleri destekleyen uygulamalar için desteklenir ve çoklu oturum açma 'nın zenginleştirme modudur. Parola tabanlı SSO ve ADFS yerine bir uygulama destekliyorsa Azure AD ile Federasyon SSO kullanmanızı öneririz.

- **Parola tabanlı çoklu oturum açma** kullanıcıları, uygulama ilk kez Kullanıcı adı ve parola ile oturum açtıklarında bu uygulamaya erişir. İlk oturum açma işleminden sonra Azure AD, uygulamaya Kullanıcı adı ve parola sağlar. Parola tabanlı çoklu oturum açma güvenli uygulama parola depolama ve bir web tarayıcısı uzantısı veya mobil uygulama kullanarak yeniden yürütme sağlar. Bu seçenek, uygulama tarafından sunulan mevcut oturum açma sürecini kullanır, yöneticinin parolaları yönetmesine olanak sağlar ve kullanıcının parolayı bilmesini gerektirmez.

### <a name="considerations-for-federation-based-sso"></a>Federasyon tabanlı SSO konuları

- **OpenID Connect ve OAuth kullanarak** , bağlanmakta olduğunuz uygulama onu destekliyorsa, bu uygulama için SSO 'yu etkinleştirmek üzere OIDC/OAuth 2,0 yöntemini kullanın. Bu yöntem daha az yapılandırma gerektirir ve daha zengin bir kullanıcı deneyimi sunar. Daha fazla bilgi için bkz. [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [openıd Connect 1,0](../develop/v2-protocols-oidc.md)ve [Azure Active Directory Geliştirici Kılavuzu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **SAML tabanlı SSO Için uç nokta yapılandırması** -SAML kullanıyorsanız, geliştiricilerinizin uygulamayı yapılandırmadan önce belirli bilgilere ihtiyacı olacaktır. Daha fazla bilgi için bkz. [temel SAML yapılandırmasını düzenleme](configure-single-sign-on-non-gallery-applications.md).
- **SAML tabanlı SSO Için sertifika yönetimi** -UYGULAMANıZıN Federasyon SSO 'yu etkinleştirdiğinizde, Azure AD üç yıl için varsayılan olarak geçerli olan bir sertifika oluşturur. Gerekirse, bu sertifika için süre sonu tarihini özelleştirebilirsiniz. Sertifikaları, süresi dolduktan önce yenilediğinizden emin olun. Daha fazla bilgi için bkz. [Azure AD sertifikaları yönetme](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Parola tabanlı SSO konuları

Parola tabanlı SSO için Azure AD 'nin kullanılması, kimlik bilgilerini güvenli bir şekilde alacak ve oturum açma formlarını dolduran bir tarayıcı uzantısının dağıtılmasını gerektirir. Uzantıyı [desteklenen tarayıcılarla](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)bir ölçekte dağıtmak için bir mekanizma tanımlayın. Şu seçenekler mevcuttur:

- [Internet Explorer için grup ilkesi](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet Explorer için System Center Configuration Manager (SCCM)](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Chrome, Firefox, Microsoft Edge veya IE için Kullanıcı odaklı indirme ve yapılandırma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Daha fazla bilgi için bkz. [parola çoklu oturum açmayı yapılandırma](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Galeride olmayan uygulamalar için oturum açma formları meta verilerini yakalama

Microsoft, parola oluşturma (Kullanıcı adı ve parola alanlarını yakalama) için bir Web uygulamasında meta verilerin yakalanmasını destekler. Form meta verilerini yakalamak üzere uygulamayı yapılandırma işlemi sırasında oturum açma URL 'sine gidin. Uygulamanın sahibine tam oturum açma URL 'SI için sorun. Bu bilgiler oturum açma işlemi sırasında, oturum açma sırasında Azure AD kimlik bilgilerini uygulamaya eşlemek için kullanılır.

Daha fazla bilgi edinmek için bkz. [Azure AD ile uygulama erişimi ve SSO nedir? – parola tabanlı SSO](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Formlardaki meta verilerin yeniden yakalanmasının gerektiği konusunda göstergeler

Uygulamalar HTML düzenlerini değiştirdiğinde, değişiklikleri ayarlamak için meta verileri yeniden yakalamanız gerekebilir. HTML düzeninin değişiklik olduğunu gösteren yaygın belirtiler şunlardır:

- Uygulamaya tıklayan Kullanıcı bildirimi, oturum açma sayfasında "takılı" alır
- Kullanıcı adı veya parolanın doldurulmadığını bildiren kullanıcılar

#### <a name="shared-accounts"></a>Paylaşılan hesaplar

Oturum açma perspektifinden, paylaşılan hesaplara sahip uygulamalar, bireysel kullanıcılar için parola SSO 'SU kullanan bir Galeri uygulamasından farklı değildir. Ancak, paylaşılan hesapları kullanmak üzere bir uygulamayı planlarken ve yapılandırırken gereken bazı ek adımlar vardır:

1. Aşağıdakileri belgelemek için uygulama iş kullanıcılarıyla çalışın:
   1. Kuruluştaki uygulamayı kullanacak kullanıcı kümesi
   1. Kullanıcı kümesiyle ilişkili uygulamadaki mevcut kimlik bilgileri kümesi 
1. Kullanıcı kümesi ve kimlik bilgilerinin her birleşimi için, gereksinimlerinize göre bulutta veya şirket içinde bir güvenlik grubu oluşturun.
1. Paylaşılan kimlik bilgilerini sıfırlayın. Azure AD 'de uygulama dağıtıldıktan sonra, kişiler paylaşılan hesabın parolasına gerek kalmaz. Azure AD parolayı depolayabileceği için, bunu çok uzun ve karmaşık olarak ayarlamayı göz önünde bulundurun. 
1. Uygulamanın desteklediği durumlarda parolanın otomatik olarak başa geçişine yapılandırma. Bu şekilde, ilk kurulumu yapan yönetici de paylaşılan hesabın parolasını bilir. 

## <a name="plan-your-authentication-method"></a>Kimlik doğrulama yönteminizi planlayın

Doğru kimlik doğrulama yönteminin seçilmesi, bir Azure AD karma kimlik çözümü ayarlamaya yönelik önemli bir ilk karardır. Bulut içindeki kullanıcıları da sağlayan Azure AD Connect kullanılarak yapılandırılan kimlik doğrulama yöntemini uygulayın.

Bir kimlik doğrulama yöntemi seçmek için, seçiminizi uygulayan süreyi, var olan altyapıyı, karmaşıklığı ve maliyeti göz önünde bulundurmanız gerekir. Bu faktörler her kuruluş için farklılık gösterebilir ve zaman içinde değişebilir. Belirli senaryolarınız en yakından eşleşen olanı seçmeniz gerekir. Daha fazla bilgi için bkz. [Azure Active Directory karma kimlik çözümünüz için doğru kimlik doğrulama yöntemini seçme](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).

## <a name="plan-your-security-and-governance"></a>Güvenlik ve idare planınızı planlayın 

Ağ duvarlar, KCG cihazlarının ve bulut uygulamalarının açılımına göre giderek daha az etkin hale geldiği için kimlik, güvenlik konusunda dikkat çekici ve yatırımların yeni birincil özettir. 

### <a name="plan-access-reviews"></a>Erişim incelemelerini planlayın

[Erişim gözden geçirmeleri](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) , kuruluşların grup üyeliklerini verimli bir şekilde yönetmesine, kurumsal uygulamalara erişime ve rol atamalarına olanak tanır. Yalnızca doğru kişilerin erişmeye devam ettiğinden emin olmak için Kullanıcı erişimini düzenli olarak gözden geçirmeyi planlamalısınız.

Erişim incelemelerini ayarlarken planlama için plan yapılacak bazı önemli konular şunları içerir:

1. İş ihtiyaçlarınıza uyan erişim gözden geçirmeleri için bir temposunda tanımlama. Bu, haftada bir, aylık, yıllık veya isteğe bağlı bir alıştırma olarak sıklıkla bir kez olabilir.

1. Uygulama erişim raporlarının gözden geçirenleri temsil eden gruplar oluşturun. Uygulama ve hedef kullanıcıları ve kullanım durumları için en tanıdık olan paydaşların erişim incelemelerinizin katılımcıları olduğundan emin olmanız gerekir

1. Erişim incelemesini tamamlamak, artık erişmesi gerekmeyen kullanıcılara uygulama erişim izinleri almayı içerir. Reddedilen kullanıcılardan gelen olası destek isteklerini işlemeyi planlayın. Silinen bir Kullanıcı, gerektiğinde bir yönetici tarafından geri yüklenebilecekleri 30 gün boyunca Azure AD 'de silinmiş olarak kalır. 30 gün sonra bu kullanıcı kalıcı olarak silinir. Azure Active Directory portalını kullanarak, bir genel yönetici, son zamanlarda silinen bir kullanıcıyı bu döneme ulaşılmadan önce tamamen kalıcı olarak silebilir.

### <a name="plan-auditing"></a>Denetim planı

Azure AD, [Teknik ve iş öngörüleri içeren raporlar](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)sağlar. 

Güvenlik ve etkinlik raporlarının her ikisi de mevcuttur. Güvenlik raporları, risk için işaretlenen kullanıcıları ve riskli oturum açma işlemlerini gösterir. Etkinlik raporları, oturum açma etkinliğini ayrıntılandıran ve tüm oturum açma işlemleri için denetim izleri sağlayan kuruluşunuzdaki kullanıcıların davranışını anlamanıza yardımcı olur. Riskleri yönetmek, üretkenliği artırmak ve uyumluluğu izlemek için raporları kullanabilirsiniz.

| Rapor türü | Erişim gözden geçirmesi | Güvenlik raporları | Oturum açma raporu |
|-------------|---------------|------------------|----------------|
| Gözden geçirmek için kullanın | Uygulama izinleri ve kullanımı. | Tehlikede riskli hesaplar | Uygulamalara erişen |
| Olası eylemler | Denetim erişimi; izinleri iptal et | Erişimi iptal et; Güvenlik sıfırlamayı zorla | Erişimi iptal et |

Azure AD, en fazla 30 gün boyunca denetim verilerini korur ve verileri Azure yönetim portalı üzerinden veya çözümleme sistemlerinize indirmeniz için bir API aracılığıyla kullanılabilir hale getirir.

### <a name="consider-using-microsoft-cloud-application-security"></a>Microsoft Bulut uygulama güvenliği kullanmayı düşünün

Microsoft Cloud App Security (MCAS), bir bulut erişim güvenlik Aracısı (CASB) çözümüdür. Bulut Uygulamalarınız ve hizmetleriniz için görünürlük sağlar, siber tehditleri belirleyip tespit etmek için gelişmiş çözümlemeler sağlar ve verilerinizin nasıl hareket etmesini denetlemenizi sağlar.

MCAS dağıtımı şunları yapmanızı sağlar:

- Bulut ortamınızı ve kuruluşunuzun kullandığı bulut uygulamalarını eşlemek ve tanımlamak için Cloud Discovery kullanın.
- Bulutunuzdaki tasdiksiz ve tasdiksiz uygulamalar
- Bağlandığınız uygulamaların görünürlüğü ve idaresi için sağlayıcı API 'Lerinden yararlanan, dağıtımı kolay uygulama bağlayıcılarını kullanın
- Bulut uygulamalarınızda erişim ve etkinlikler üzerinde gerçek zamanlı görünürlük ve denetim sağlamak için Koşullu Erişim Uygulama Denetimi koruma kullanın
- Ayarlayarak sürekli denetime sahip olmanıza ve sürekli olarak ince ayar yapmanıza yardımcı olur.

Microsoft Bulut uygulama güvenliği (MCAS) oturum denetimi, herhangi bir işletim sisteminde herhangi bir büyük platformda herhangi bir tarayıcı için kullanılabilir. Mobil uygulamalar ve Masaüstü uygulamaları da engellenen izin veya. Azure AD ile yerel olarak tümleştirerek, SAML ile yapılandırılmış tüm uygulamalar veya Azure AD 'de çoklu oturum açma ile açık KIMLIK Connect uygulamaları, [çeşitli öne çıkan uygulamalar](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)dahil olmak üzere desteklenebilir.

MCAS hakkında daha fazla bilgi için [Microsoft Cloud App Security genel bakış](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)bölümüne bakın. MCAS, Kullanıcı tabanlı bir abonelik hizmetidir. [MCAS lisanslama veri sayfasında](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)lisanslama ayrıntılarını gözden geçirebilirsiniz.

### <a name="use-conditional-access"></a>Koşullu erişim kullan

Koşullu erişimle, bulut uygulamalarınız için ölçüt tabanlı erişim denetimi kararlarını otomatik hale getirebilirsiniz.

Koşullu erişim ilkeleri, ilk faktör kimlik doğrulaması tamamlandıktan sonra zorlanır. Bu nedenle, koşullu erişim hizmet reddi (DoS) saldırıları gibi senaryolar için birinci hat savunma olarak tasarlanmamıştır, ancak erişimi anlamak için bu olaylardan gelen sinyalleri kullanabilir. Örneğin, oturum açma risk düzeyi, isteğin konumu vb. kullanılabilir. Koşullu erişim hakkında daha fazla bilgi için bkz. [genel bakış](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) ve [dağıtım planı](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Azure SSO teknik gereksinimleri

Aşağıdaki bölümde, gerekli ortamlar, uç noktalar, talep eşleme, gerekli öznitelikler, sertifikalar ve kullanılan protokoller dahil olmak üzere belirli uygulamanızı yapılandırma gereksinimlerinin ayrıntıları verilmiştir. [Azure AD PORTALıNDA](https://portal.azure.com/)SSO 'yu yapılandırmak için bu bilgilere ihtiyacınız olacaktır.

### <a name="authentication-mechanism-details"></a>Kimlik doğrulama mekanizması ayrıntıları

Önceden tümleştirilmiş tüm SaaS uygulamaları için Microsoft bir öğretici sağlar ve bu bilgilere gerek kalmaz. Uygulama Market marketi/galerimizde yoksa, aşağıdaki veri parçalarını toplamanız gerekebilir:

- **Geçerli kimlik sağlayıcısı uygulama, uygunsa SSO için kullanılır** ; örneğin: AD FS, PingFederate, okta
- **Hedef uygulama tarafından desteklenen protokoller** -ÖRNEĞIN, SAML 2,0, OpenID Connect, OAuth, form tabanlı kimlik doğrulaması, WS-BESLENIR, WS-Trust
- **Azure AD ile yapılandırılan protokol** -ÖRNEĞIN, SAML 2,0 veya 1,1, OpenID Connect, OAuth, Forms tabanlı, WS-beslenir

### <a name="attribute-requirements"></a>Öznitelik gereksinimleri

Azure AD Kullanıcı nesneleri ve her SaaS uygulamasının Kullanıcı nesneleri arasında önceden yapılandırılmış bir öznitelikler ve öznitelik eşlemeleri kümesi vardır. Bazı uygulamalar, gruplar gibi diğer nesne türlerini yönetir. Azure AD 'den uygulamanıza kullanıcı özniteliklerinin eşlemesini planlayın ve [varsayılan öznitelik eşlemelerini](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) iş gereksinimlerinize göre özelleştirin.

### <a name="certificate-requirements"></a>Sertifika gereksinimleri

Uygulamanın sertifikası güncel olmalıdır veya kullanıcıların uygulamaya erişebilmediğinden ilgili bir risk vardır. Birçok SaaS uygulaması sertifikası 36 ay boyunca iyidir. Bu sertifika süresini uygulama dikey penceresinde değiştirirsiniz. Süre sonunu belgelediğinizden emin olun ve sertifika yenilemeyi nasıl yöneteceğini öğrenin. 

Sertifikalarınızı yönetmenin iki yolu vardır. 

- **Otomatik Sertifika geçişi** -Microsoft [, Azure AD 'de anahtar geçişi imzalamayı](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)destekler. Bu, sertifikaları yönetmek için tercih ettiğiniz yöntemdir, ancak ISV 'nin bu senaryoyu desteklemesi desteklenmez.

- **El ile güncelleştirme** -her uygulamanın, nasıl tanımlandığınıza göre süresi dolan kendi sertifikası vardır. Uygulamanın sertifikasının süresi dolmadan önce yeni bir sertifika oluşturun ve bunu ISV 'ye gönderin. Bu bilgiler, Federasyon meta verilerinden çeklenebilir. [Federasyon meta verileri hakkında buradan daha fazla bilgi edinin.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>SSO uygulama

Çözümünüzü planlamak ve kuruluşunuzda dağıtmak için aşağıdaki aşamaları kullanın:

### <a name="user-configuration-for-sso"></a>SSO için Kullanıcı Yapılandırması

- **Test kullanıcılarınızı tanımla**

   Uygulama sahibine başvurun ve uygulamanın içinde en az üç test kullanıcısı oluşturmasını isteyin. Birincil tanımlayıcı olarak kullanacağınız bilgilerin doğru doldurulduğundan ve Azure AD 'de kullanılabilen bir öznitelikle eşleştiğinden emin olun. Çoğu durumda bu, SAML tabanlı uygulamalar için "NameID" ile eşlenir. JWT belirteçleri için, "preferred_username" olur.
   
   Kullanıcıyı Azure AD 'de bulut tabanlı bir kullanıcı olarak el ile oluşturun veya Azure AD Connect eşitleme altyapısını kullanarak kullanıcıyı Şirket içinden eşitleyin. Bilgilerin uygulamaya gönderilen taleplerle eşleştiğinden emin olun.

- **SSO 'yu yapılandırma**

   Uygulama [listesinden](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)uygulamanızın SSO öğreticisini bulup açın ve SaaS uygulamanızı başarıyla yapılandırmak için öğreticinin adımlarını izleyin.

   Uygulamanızı bulamıyorsanız bkz. [özel uygulama belgeleri](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Bu, Azure AD galerisinde bulunmayan bir uygulama ekleme konusunda size kılavuzluk eder.

   İsteğe bağlı olarak, [Microsoft 'un rehberlik belgelerini](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)kullanarak kurumsal uygulama için SAML belirtecinde verilen talepleri kullanabilirsiniz. Bu uygulamanın, uygulamanız için SAML yanıtında almak istediğiniz şekilde eşlendiğinden emin olun. Yapılandırma sırasında sorunlarla karşılaşırsanız, [SSO tümleştirmesinde hata ayıklama ile](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)ilgili kılavuzumuzu kullanın.

Özel uygulama ekleme bir Azure AD Premium P1 veya P2 lisansları özelliğidir.

### <a name="provide-sso-change-communications-to-end-users"></a>Son kullanıcılara SSO değişiklik iletişimleri sağlama

İletişim planınızı uygulayın. Son kullanıcılarınıza bir değişikliğin geldiğini, ne zaman geldiğini, ne yapmanız gerektiğini ve yardım arama hakkında bilgi sahibi olduğunuzdan emin olun.

### <a name="verify-end-user-scenarios-for-sso"></a>SSO için Son Kullanıcı senaryolarını doğrulama

Aşağıdaki test çalışmalarını, SSO yapılandırmalarının beklendiği gibi çalıştığından emin olmak için şirkete ait ve kişisel cihazlarda testler yapmak üzere kullanabilirsiniz. Aşağıdaki senaryolar, bir kullanıcının uygulama URL 'sine gezinmekte olduğunu ve hizmet sağlayıcısı tarafından başlatılan bir kimlik doğrulaması akışından (SP tarafından başlatılan kimlik doğrulama akışı) başlatıldığını varsayar.

| Senaryo | Kullanıcıya göre SP tarafından başlatılan kimlik doğrulama akışında beklenen sonuç |
|----------|---------------------------------------------------|
| Corpnet sırasında IE ile uygulamada oturum açın. | Tümleşik Windows kimlik doğrulaması (ıWA), ek istem olmadan oluşur. |
| Yeni oturum açma girişimiyle Corpnet çalışırken IE ile uygulamada oturum açın. | AD FS sunucuda form tabanlı istem. Kullanıcı, MFA için başarıyla oturum açar ve tarayıcı istemlerini ister. |
| Geçerli bir oturumla Corpnet çalışırken IE ile uygulamada oturum açın ve MFA 'yı hiç gerçekleştirmeyin. | Kullanıcı ilk faktör için istem almaz. Kullanıcı MFA için istem alır. |
| Geçerli bir oturumla Corpnet çalışırken IE ile uygulama oturumu açın ve bu oturumda MFA 'yı zaten gerçekleştirdi. | Kullanıcı ilk faktör için istem almaz. Kullanıcı MFA almaz. Kullanıcı uygulamaya takılır. |
| Geçerli bir oturumla Corpnet/Firefox/Safari ile uygulamada oturum açın ve bu oturumda MFA 'yı zaten gerçekleştirdi. | Kullanıcı ilk faktör için istem almaz. Kullanıcı MFA almaz. Kullanıcı SSO 'SU uygulama. |
| Yeni oturum açma girişimi ile Corpnet 'i kapatırken Chrome/Firefox/Safari ile uygulamada oturum açın. | AD FS sunucuda form tabanlı istem. Kullanıcı, MFA için başarıyla oturum açar ve tarayıcı istemlerini ister. |
| Geçerli bir oturumla şirket ağı sırasında Chrome/Firefox ile uygulamada oturum açın. | Kullanıcı ilk faktör için istem almaz. Kullanıcı MFA almaz. Kullanıcı SSO 'SU uygulama. |
| Uygulama mobil uygulamasıyla yeni bir oturum açma girişimi ile uygulamada oturum açın. | AD FS sunucuda form tabanlı istem. Kullanıcı, MFA için ve ADAL istemci istemlerine başarıyla oturum açar. |
| Yetkisiz Kullanıcı, oturum açma URL 'SI ile uygulama oturumunu açmaya çalışır. | AD FS sunucuda form tabanlı istem. Kullanıcı ilk faktörle oturum açamazsa. |
| Yetkili Kullanıcı oturum açmaya çalışır, ancak yanlış bir parola girer. | Kullanıcı uygulama URL 'sine gider ve hatalı Kullanıcı adı/parola hatası alır. |
| Yetkili Kullanıcı bir e-postadaki bağlantıyı tıklatır ve zaten kimlik doğrulamasından geçer. | Kullanıcı URL 'yi tıklatır ve ek istem olmadan uygulamada oturum açmış olur. |
| Yetkili kullanıcı e-postadaki bağlantıyı tıklatır ve henüz doğrulanmadı. | Kullanıcı URL 'yi tıklatır ve ilk faktörle kimlik doğrulaması yapmak için istemde bulunur. |
| Yetkili Kullanıcı, yeni bir oturum açma girişimi ile uygulama mobil uygulaması (SP-başlatma) ile uygulamada oturum açar. | AD FS sunucuda form tabanlı istem. Kullanıcı, MFA için ve ADAL istemci istemlerine başarıyla oturum açar. |
| Yetkisiz kullanıcı oturum açma URL 'SI (SP-başlatma) ile uygulamada oturum açmaya çalışır. | AD FS sunucuda form tabanlı istem. Kullanıcı ilk faktörle oturum açamazsa. |
| Yetkili Kullanıcı oturum açmaya çalışır, ancak yanlış bir parola girer.| Kullanıcı uygulama URL 'sine gider ve hatalı Kullanıcı adı/parola hatası alır. |
| Yetkili Kullanıcı oturumu kapatıp yeniden oturum açar. | Oturum kapatma URL 'SI yapılandırılmışsa, Kullanıcı tüm hizmetlerde oturumu kapatıp kimlik doğrulaması yapmasını ister. |
| Yetkili Kullanıcı oturumu kapatıp yeniden oturum açar. | Oturum kapatma URL 'SI yapılandırılmamışsa, Kullanıcı mevcut Azure AD tarayıcı oturumunda mevcut belirteç kullanılarak otomatik olarak yeniden oturum açar. |
| Yetkili Kullanıcı bir e-postadaki bağlantıyı tıklatır ve zaten kimlik doğrulamasından geçer. | Kullanıcı URL 'yi tıklatır ve ek istem olmadan uygulamada oturum açmış olur. |
| Yetkili kullanıcı e-postadaki bağlantıyı tıklatır ve henüz doğrulanmadı. | Kullanıcı URL 'yi tıklatır ve ilk faktörle kimlik doğrulaması yapmak için istemde bulunur. |

## <a name="manage-sso"></a>SSO 'yu Yönet

Bu bölüm, SSO 'yu başarıyla yönetmeye yönelik gereksinimleri ve önerileri özetler.

### <a name="required-administrative-roles"></a>Gerekli yönetim rolleri

Rolü her zaman Azure Active Directory içinde gerekli görevi gerçekleştirmek için en az izinlerle kullanın. Microsoft, [kullanılabilir farklı rollerin gözden geçirilmesini](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) önerir ve bu uygulamaya yönelik her kişi için ihtiyaçlarınızı çözümlemek üzere doğru olanı seçer. Dağıtım tamamlandıktan sonra bazı rollerin geçici olarak uygulanması ve kaldırılması gerekebilir.

| Bilgisini| Roller | Azure AD rolü (gerekirse) |
|--------|-------|-----------------------------|
| Yardım Masası Yöneticisi | Katman 1 desteği | None |
| Kimlik Yöneticisi | Sorunları Azure AD ' i etkileyen yapılandırma ve hata ayıklama | Genel yönetici |
| Uygulama Yöneticisi | Uygulamada Kullanıcı kanıtlaması, izinleri olan kullanıcılar üzerinde yapılandırma | None |
| Altyapı Yöneticileri | Sertifika aktarma sahibi | Genel yönetici |
| İşletme sahibi/paydaş | Uygulamada Kullanıcı kanıtlaması, izinleri olan kullanıcılar üzerinde yapılandırma | None |

Dizin izinleri olan kullanıcılar için ek denetim, denetim ve erişim incelemesi sağlamak üzere rollerinizi yönetmek üzere [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) kullanmanızı öneririz.

### <a name="sso-certificate-lifecycle-management"></a>SSO sertifika yaşam döngüsü yönetimi

Azure AD ile çoklu oturum açma ile yapılandırılmakta olan uygulama arasındaki imza sertifikası yaşam döngüsünü yönetmeye yönelik doğru roller ve e-posta dağıtım listelerinin belirlenmesi önemlidir. Aşağıda, yerinde olmasını önerdiğimiz bazı anahtar roller verilmiştir:

- Uygulamadaki kullanıcı özelliklerini güncelleştirmek için sahip
- Uygulama kesmesi/düzeltilmesi desteği için sahip çağrısı
- Sertifikayla ilgili değişiklik bildirimleri için yakından izlenen e-posta dağıtım listesi

Bir sertifikanın en uzun yaşam süresi üç yıldır. Azure AD ile uygulamanız arasında bir sertifika değişikliğini nasıl işleyeceğinizi bir işlem yapmanızı öneririz. Bu, bir sertifikanın süresinin dolmasını veya sertifika geçişine izin zorlaması nedeniyle kesintiden kaçınmak veya en aza indirmenize yardımcı olabilir

### <a name="rollback-process"></a>Geri alma işlemi

Test çalışmalarınızı temel alarak testi tamamladıktan sonra, uygulamanızla birlikte üretime geçme süresi de vardır. Üretime taşınma, planlı ve test edilmiş yapılandırmalardan üretim kiracınızda uygulamanız ve bunu kullanıcılarınıza sunmaktır. Ancak, dağıtımınızın planlanmaması durumunda ne yapılacağını planlamanız önemlidir. Dağıtım sırasında SSO yapılandırması başarısız olursa, herhangi bir kesinti etkisini nasıl azaltacağınızı ve kullanıcılarınıza etkisini azaltmanızı anlamanız gerekir.

Uygulama içindeki kimlik doğrulama yöntemlerinin kullanılabilirliği, en iyi stratejinizi tespit eder. Dağıtımınızın sorunlar halinde çalışması durumunda, özgün oturum açma yapılandırması durumuna tam olarak nasıl geri gidebileceğini her zaman uygulama sahipleri için ayrıntılı belgelerinize sahip olduğunuzdan emin olun.

- **Uygulamanız birden çok kimlik sağlayıcısını destekliyorsa**(ÖRNEĞIN, LDAP ve AD FS ve ping), dağıtım SıRASıNDA mevcut SSO yapılandırmasını silmeyin. Bunun yerine, daha sonra geri geçmeniz gerektiğinde geçiş sırasında devre dışı bırakın. 

- **Uygulamanız birden çok IDP 'yi desteklemiyorsa** , ancak kullanıcıların form tabanlı kimlik doğrulaması (Kullanıcı adı/parola) kullanarak oturum açmasına izin veriyorsa, yeni SSO yapılandırması dağıtımı başarısız olduğunda kullanıcıların bu yaklaşıma dönebilmesine emin olun.

### <a name="access-management"></a>Erişim yönetimi

Kaynaklara erişimi yönetirken ölçeklendirilen bir yaklaşım seçmeyi öneririz. Yaygın yaklaşımlar, Azure AD Connect aracılığıyla eşitleyerek şirket içi grupların kullanılmasıyla, [Kullanıcı özniteliklerine göre Azure AD 'de dinamik gruplar oluşturmaya](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)veya bir kaynak sahibi tarafından YÖNETILEN Azure AD 'de [self servis grupları](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) oluşturulmasına dahildir.

### <a name="monitor-security"></a>Güvenliği izle

SaaS uygulama güvenliğinin farklı yönlerini gözden geçitireceğiz ve gerekli tüm düzeltme eylemlerini gerçekleştirirken düzenli bir temposunda ayarlamayı öneririz.

### <a name="troubleshooting"></a>Sorun giderme

Aşağıdaki bağlantılar sorun giderme senaryolarını sunar. Destek personeliniz için bu senaryoları ve bunları gidermeye yönelik adımları içeren belirli bir kılavuz oluşturmak isteyebilirsiniz.

#### <a name="consent-issues"></a>Onay sorunları

- [Beklenmeyen izin hatası](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Kullanıcı onayı hatası](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Oturum açma sorunları

- [Özel portaldan oturum açma sorunları](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Özel bölmeden oturum açma sorunları](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Uygulama oturum açma sayfasında hata](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Microsoft uygulamasında oturum açma sorunu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure Uygulama galerisinde listelenen uygulamalar için SSO sorunları

- [Azure Uygulama galerisinde listelenen uygulamalar için parola SSO 'SU ile ilgili sorun](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure Uygulama galerisinde listelenen uygulamalar için Federasyon SSO 'SU ile ilgili sorun](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure Uygulama galerisinde listelenmeyen uygulamalar için SSO sorunları

- [Azure Uygulama galerisinde listelenmeyen uygulamalar için parola SSO 'SU ile ilgili sorun](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure Uygulama galerisinde listelenmeyen uygulamalar için Federasyon SSO 'SU ile ilgili sorun](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Sonraki adımlar

[SAML tabanlı SSO hata ayıklama](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[PowerShell aracılığıyla uygulamalar için talep eşlemesi](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[SAML belirtecinde verilen talepleri özelleştirme](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Çoklu oturum açma SAML Protokolü](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Çoklu Oturum Kapatma SAML protokolü](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (iş ortakları ve satıcılar gibi dış kullanıcılar için)

[Azure AD koşullu erişim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure kimlik koruması](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO erişimi](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Uygulama SSO öğreticisi](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
