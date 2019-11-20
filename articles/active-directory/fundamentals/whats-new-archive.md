---
title: Azure Active Directory yenilikler için arşiv var mı? | Microsoft Belgeleri
description: Etkinliğin 6 ay bu içerik kümesinin bölümünü içeren yeni sürüm notları genel bakışta nedir? 6 ay sonra öğeleri ana makalesinden kaldırılır ve Arşiv makalede yerleştirin.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9aa129978201017c628da00e90190d93900d2de
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131913"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Azure Active Directory yenilikler için arşiv var mı?

[Azure Active Directory 'deki yenilikler nelerdir? sürüm notları](whats-new.md) makalesi, son altı aya yönelik güncelleştirmeleri içerir, ancak bu makalede daha eski bilgiler yer alır.

Azure Active Directory yenilikler nelerdir? sürüm notları hakkında bilgi sağlar:

- En son sürümleri
- Bilinen sorunlar
- Hata düzeltmeleri
- Kullanım dışı işlev
- Değişiklikleri planları

---

## <a name="april-2019"></a>Nisan 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Yeni Azure AD tehdit zekası algılaması artık Azure AD Kimlik Koruması bir parçası olarak sağlanıyor

**Türü:** yeni özellik  
**Hizmet kategorisi:** Azure AD Kimlik Koruması  
**Ürün özelliği:** kimlik güvenliği ve koruması

Azure AD Threat Intelligence algılama artık güncelleştirilmiş Azure AD Kimlik Koruması özelliğinin bir parçası olarak sunulmaktadır. Bu yeni işlevsellik, Microsoft 'un dahili ve dış tehdit bilgileri kaynaklarına dayanan bilinen saldırı desenleriyle tutarlı olan belirli bir kullanıcı veya etkinliğin olağan dışı Kullanıcı etkinliklerini belirtmenize yardımcı olur.

Azure AD Kimlik Koruması yenilenen sürümü hakkında daha fazla bilgi için, bkz. [dört ana Azure AD kimlik koruması geliştirmesi artık genel önizleme](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) bloguna ve [ne Azure Active Directory kimlik koruması (YENİLENDİ)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) makalenin. Azure AD Threat Intelligence algılaması hakkında daha fazla bilgi için [risk algılamalarını Azure Active Directory kimlik koruması](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) makalesine bakın.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD Yetkilendirme Yönetimi artık kullanıma sunuldu (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** Kimlik yönetimi  
**Ürün yeteneği:** Kimlik yönetimi

Artık genel önizlemede olan Azure AD Yetkilendirme Yönetimi, müşterilerin, çalışanların ve iş ortaklarının erişim isteme, ne kadar süreceğine ve ne kadar erişebileceğini tanımlayan erişim paketleri yönetimini temsilcmalarına yardımcı olur. Erişim paketleri Azure AD ve Office 365 gruplarındaki üyeliği, kurumsal uygulamalardaki rol atamalarını ve SharePoint Online siteleri için rol atamalarını yönetebilir. Yetkilendirme Yönetimi hakkında daha fazla bilgi için [bkz. Azure AD Yetkilendirme Yönetimi](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Privileged Identity Management, erişim gözden geçirmeleri ve kullanım koşulları dahil Azure AD Identity Governance özellikleri hakkında daha fazla bilgi edinmek için bkz. [Azure AD Identity Governance nedir?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Azure AD portalındaki Office 365 grupları için adlandırma ilkesi yapılandırma (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün özelliği:** işbirliği

Yöneticiler artık Azure AD portalını kullanarak Office 365 grupları için bir adlandırma ilkesi yapılandırabilir. Bu değişiklik, kuruluşunuzdaki kullanıcılar tarafından oluşturulan veya düzenlenen Office 365 grupları için tutarlı adlandırma kuralları uygulanmasını sağlar.

Office 365 grupları için adlandırma ilkesini iki farklı yolla yapılandırabilirsiniz:

- Otomatik olarak bir grup adına eklenen ön ekleri veya sonekleri tanımlayın.

- Kuruluşunuz için Grup adlarında izin verilmeyen özelleştirilmiş bir engellenen sözcük kümesini karşıya yükleyin (örneğin, "CEO, bordro, HR").

Daha fazla bilgi için bkz. [Office 365 grupları Için adlandırma Ilkesi zorlama](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD etkinlik günlükleri artık Azure Izleyici 'de kullanılabilir (genel kullanılabilirlik)

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Azure AD etkinlik günlükleri ile görselleştirmelerle ilgili geri bildirimlerinizi ele almak için, Log Analytics yeni bir Öngörüler özelliği sunuyoruz. Bu özellik, çalışma kitapları adlı etkileşimli şablonlarımızı kullanarak Azure AD kaynaklarınız hakkında öngörüler elde etmenize yardımcı olur. Önceden oluşturulmuş bu çalışma kitapları, uygulamalar veya kullanıcılar için Ayrıntılar sağlayabilir ve şunları içerir:

- **Oturum açma işlemleri.** Uygulama ve kullanıcıların oturum açma konumu, kullanımda olan işletim sistemi veya tarayıcı istemcisi ve sürümü ve başarılı ya da başarısız oturum açma sayısı dahil olmak üzere ayrıntıları sağlar.

- **Eski kimlik doğrulama ve koşullu erişim.** Koşullu erişim ilkeleri tarafından tetiklenen Multi-Factor Authentication kullanımı, koşullu erişim ilkelerini kullanan uygulamalar vb. dahil olmak üzere eski kimlik doğrulaması kullanan uygulamalar ve kullanıcılar için ayrıntılar sağlar.

- **Oturum açma hatası analizi.** Kullanıcı eylemi, ilke sorunları veya altyapınız nedeniyle oturum açma hatalarınızın oluşup oluşmadığını belirlemenize yardımcı olur.

- **Özel raporlar.** Kuruluşunuz için Öngörüler özelliğini özelleştirmeye yardımcı olmak üzere yeni veya mevcut çalışma kitaplarını düzenleyebilirsiniz.

Daha fazla bilgi için bkz. [Azure izleyici çalışma kitaplarını Azure Active Directory raporları için kullanma](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Nisan 2019

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme

2019 Nisan 'da, uygulama galerisine federasyon desteğiyle bu 21 yeni uygulama ekledik:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [Hrçalışmalar çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [percogeç](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [Mobıoncontrol](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Riling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [eğitibrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [rstudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (rol tabanlı SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [cerkatlanmış varlık yönetimi](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [sectigo Sertifika Yöneticisi](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [tarzorbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ Gözetimi kurumsal](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [ındiggo](https://indiggolead.com/)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Yeni erişim İncelemeleri sıklık seçeneği ve birden çok rol seçimi

**Türü:** yeni özellik  
**Hizmet kategorisi:** erişim gözden geçirmeleri  
**Ürün yeteneği:** Kimlik yönetimi

Azure AD erişim gözden geçirmeleriyle yeni güncelleştirmeler şunları yapmanıza olanak sağlar:

- Daha önce haftalık, aylık, üç aylık ve yıllık olan mevcut seçeneklere ek olarak, erişim incelemelerinizin sıklığını **yarı yıllık**olarak değiştirin.

- Tek bir erişim incelemesi oluştururken birden çok Azure AD ve Azure Kaynak rolü seçin. Bu durumda, tüm roller aynı ayarlarla ayarlanır ve tüm gözden geçirenler aynı anda bilgilendirilir.

Erişim incelemesi oluşturma hakkında daha fazla bilgi için bkz. [Azure AD erişim gözden geçirmeleriyle gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-posta uyarı sistemleri geçiyor, bazı müşteriler için yeni e-posta gönderici bilgileri gönderiliyor

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** AD eşitleme  
**Ürün özelliği:** platformu

Azure AD Connect, e-posta uyarı sistemlerimizi geçiş sürecinde olduğundan, bazı müşterilere yeni bir e-posta gönderici gösteriliyor olabilir. Bunu çözmek için kuruluşunuzun izin verilenler listesine `azure-noreply@microsoft.com` eklemeniz gerekir veya Office 365, Azure veya eşitleme hizmetinizden önemli uyarıları almaya devam edemeyeceksiniz.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN son ek değişiklikleri artık Azure AD Connect Federasyon etki alanları arasında başarılı oluyor

**Türü:** düzeltildi  
**Hizmet kategorisi:** AD eşitleme  
**Ürün özelliği:** platformu

Artık bir kullanıcının UPN sonekini, Azure AD Connect bir Federasyon etki alanından başka bir Federasyon etki alanına başarıyla değiştirebilirsiniz. Bu düzeltilme, eşitleme çevrimi sırasında FederatedDomainChangeError hata iletisine artık karşılaşmayabilmeniz veya bir bildirim e-postası almanız, "özniteliği nedeniyle bu nesne Azure Active Directory güncelleştirilemiyor. FederatedUser. UserPrincipalName] geçerli değil. Yerel dizin hizmetinizdeki değeri güncelleştirin.

Daha fazla bilgi için bkz. [eşitleme sırasında sorun giderme hataları](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Azure AD 'de uygulama koruma tabanlı koşullu erişim ilkesini kullanarak artırılmış güvenlik (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Uygulama koruma tabanlı koşullu erişim artık **Uygulama koruma Ilkesi gerektir** ilkesi kullanılarak kullanılabilir. Bu yeni ilke, aşağıdakileri önlemeye yardımcı olarak kuruluşunuzun güvenliğini artırmaya yardımcı olur:

- Kullanıcılar Microsoft Intune lisansı olmadan uygulamalara erişim hakkı elde edebilirler.

- Kullanıcılar Microsoft Intune uygulama koruma ilkesi alamıyor.

- Kullanıcılar, yapılandırılmış bir Microsoft Intune uygulama koruma ilkesi olmadan uygulamalara erişim hakkı elde edebilirler.

Daha fazla bilgi için bkz. [koşullu erişim ile Cloud App erişimi için uygulama koruma Ilkesi gerektirme](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 'de Azure AD çoklu oturum açma ve koşullu erişim için yeni destek (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Azure AD çoklu oturum açma ve koşullu erişim için yeni destek sağlama dahil olmak üzere Microsoft Edge için Azure AD desteğimizi geliştirdik. Daha önce Microsoft Intune Managed Browser kullandıysanız, artık bunun yerine Microsoft Edge 'i kullanabilirsiniz.

Koşullu erişim kullanarak cihazlarınızı ve uygulamalarınızı ayarlama ve yönetme hakkında daha fazla bilgi için bkz. [koşullu erişim ile Cloud App erişimi için yönetilen cihazlar gerektir](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) ve [koşullu erişim ile Cloud App erişimi için onaylanan istemci uygulamaları gerektir](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Microsoft Edge 'i Microsoft Intune ilkeleriyle kullanarak erişimin nasıl yönetileceği hakkında daha fazla bilgi için, bkz. [Microsoft Intune İlkeyle korunan tarayıcı kullanarak Internet erişimini yönetme](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mart 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C 'da kimlik deneyimi çerçevesi ve özel ilke desteği artık kullanılabilir (GA)

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C

Artık Azure AD B2C, ölçeklendirerek ve Azure SLA 'umuz kapsamında desteklenen aşağıdaki görevler de dahil olmak üzere özel ilkeler oluşturabilirsiniz:

- Özel ilkeler kullanarak özel kimlik doğrulama Kullanıcı yolculukları oluşturun ve karşıya yükleyin.

- Talep sağlayıcıları arasındaki değişimleri adım adım kabul eden kullanıcı hakkındaki adımları açıklama.

- Kullanıcı yolculukları 'nda koşullu dallanma tanımlayın.

- Gerçek zamanlı kararlar ve iletişimlerde kullanım için talepleri dönüştürün ve eşleyin.

- Özel kimlik doğrulama Kullanıcı yolculuğa REST API özellikli Hizmetleri kullanın. Örneğin, e-posta sağlayıcıları, CRMs ve özel yetkilendirme sistemleri.

- Openıdconnect protokolüyle uyumlu kimlik sağlayıcılarıyla federasyona bağlayın. Örneğin, çok kiracılı Azure AD, sosyal hesap sağlayıcıları veya iki öğeli doğrulama sağlayıcıları ile.

Özel ilkeler oluşturma hakkında daha fazla bilgi için, bkz. [Azure Active Directory B2C özel ilkeler Için geliştirici notları](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) ve [örnek olay incelemeleri de dahil, Alex SION blog gönderisini](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)okuyun.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Mart 2019

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme

2019 Mart 'da, uygulama galerisine federasyon desteği olan bu 14 yeni uygulamaları ekledik:

[ISEC7 mobil Exchange temsilcisi](https://www.isec7.com/english/), [mediusflow](https://office365.cloudapp.mediusflow.com/), [eplatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [fulcrud](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [excelityglobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Açıklama tabanlı denetim sistemi](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [yalın](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [powerokul performansı önemli](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [cincode](https://cinode.com/), [Iris intraneti](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [tas](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD galerisinde yeni Zscaler ve Atlasser sağlama bağlayıcıları-Mart 2019

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama sağlama  
**Ürün özelliği:** 3. taraf tümleştirme

Aşağıdaki uygulamalar için Kullanıcı hesaplarını oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirin:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler iki](https://aka.ms/ZscalerTwoProvisioning), [Zscaler üç](https://aka.ms/ZscalerThreeProvisioning), [Zscaler zscsesli](https://aka.ms/ZscalerZSCloudProvisioning), [atlasser bulutu](https://aka.ms/atlassianCloudProvisioning)

Otomatik Kullanıcı hesabı sağlama aracılığıyla kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Azure AD portalındaki silinen Office 365 gruplarınızı geri yükleme ve yönetme

**Türü:** yeni özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün özelliği:** işbirliği

Artık silinen Office 365 gruplarınızı Azure AD portalından görüntüleyebilir ve yönetebilirsiniz. Bu değişiklik, geri yükleme için hangi grupların kullanılabilir olduğunu görmenizi sağlar ve kuruluşunuzun ihtiyaç duymayan grupları kalıcı olarak silmenizi sağlar.

Daha fazla bilgi için bkz. [geri yükleme süre aşımına uğradı veya silinmiş gruplar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Çoklu oturum açma artık Azure AD SAML ile güvenli şirket içi uygulamalar için uygulama ara sunucusu aracılığıyla kullanılabilir (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** erişim denetimi

Artık şirket içi, SAML kimliği doğrulanmış uygulamalar için çoklu oturum açma (SSO) deneyimi ve uygulama proxy 'Si aracılığıyla bu uygulamalara uzaktan erişim sağlayabilirsiniz. Şirket içi uygulamalarınızla SAML SSO 'yu ayarlama hakkında daha fazla bilgi için bkz. [uygulama proxy 'si (Önizleme) ile şirket içi uygulamalar Için SAML çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>İstek döngülerine yönelik istemci uygulamaları, güvenilirliği ve Kullanıcı deneyimini geliştirmek için kesintiye uğrar

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

İstemci uygulamaları kısa bir süre boyunca aynı oturum açma isteklerini yanlışlıkla verebilir. Bu istekler, başarılı olsun veya etmeksizin, tüm kullanıcılar için zayıf bir kullanıcı deneyimine ve ıDP 'nin bu iş yüklerini artırarak, tüm kullanıcılar için gecikme süresini artırarak ve ıDP kullanılabilirliğini azalttığından.

Bu güncelleştirme bir `invalid_grant` hatası gönderir: kısa bir süre içinde birden çok kez yinelenen istekler veren istemci uygulamalarına `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`, normal işlem kapsamının ötesinde. Bu sorunla karşılaşan istemci uygulamalarında, kullanıcının yeniden oturum açmasını gerektiren bir etkileşimli istem gösterilmelidir. Bu değişiklik ve bu hatayla karşılaştığında uygulamanızı nasıl gidereceğiniz hakkında daha fazla bilgi için bkz. [kimlik doğrulaması yenilikleri nelerdir?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Yeni denetim günlükleri Kullanıcı deneyimi artık kullanılabilir

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Okunabilirliği ve bilgilerinizi aramanızı artırmaya yardımcı olmak için yeni bir Azure AD **Denetim günlükleri** sayfası oluşturduk. Yeni **Denetim günlükleri** sayfasını görmek IÇIN Azure AD 'nin **etkinlik** bölümündeki **Denetim günlükleri** ' ni seçin.

![Yeni denetim günlükleri sayfası, örnek bilgi](media/whats-new/audit-logs-page.png)

Yeni **Denetim günlükleri** sayfası hakkında daha fazla bilgi için, bkz. [Azure Active Directory portalındaki denetim etkinliği raporları](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Yanlış yapılandırılmış koşullu erişim ilkeleriyle yanlışlıkla yöneticinin kilitlenmesini önlemeye yardımcı olacak yeni uyarılar ve kılavuzluk

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Yöneticilerin, yanlış yapılandırılmış koşullu erişim ilkeleri aracılığıyla kendi kiracılarından kendilerini yanlışlıkla kilitlemesini önlemeye yardımcı olmak için Azure portal yeni uyarılar ve güncelleştirilmiş rehberlik oluşturduk. Yeni rehberlik hakkında daha fazla bilgi için bkz. [koşullu erişim Azure Active Directory hizmet bağımlılıkları nelerdir](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Mobil cihazlarda geliştirilmiş son kullanıcı kullanım koşulları deneyimi

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** idare

Mevcut kullanım koşullarımızı, bir mobil cihazda kullanım koşullarını nasıl gözden geçitireceğiniz ve kabul ettiğiniz hakkında daha fazla yardımcı olacak şekilde güncelleştirdik. Artık yakınlaştırıp uzaklaştırabilir, geri dönüp bilgileri indirebilir ve köprüler ' i seçebilirsiniz. Güncelleştirilmiş kullanım koşulları hakkında daha fazla bilgi için bkz. [kullanım koşulları özelliği Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Yeni Azure AD etkinlik günlükleri indirme deneyimi kullanılabilir

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Artık Azure portal çok miktarda etkinlik günlüğünü doğrudan indirebilirsiniz. Bu güncelleştirme şunları yapmanızı sağlar:

- En fazla 250.000 satır indirin.

- İndirme tamamlandıktan sonra bildirim alın.

- Dosya adınızı özelleştirin.

- JSON veya CSV çıkış biçiminizi belirleme.

Bu özellik hakkında daha fazla bilgi için bkz [. hızlı başlangıç: Azure Portal kullanarak bir denetim raporu yükleme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Son değişiklik: Exchange ActiveSync (EAS) tarafından koşul değerlendirmesine yönelik güncelleştirmeler

**Türü:** değişiklik planı  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** erişim denetimi

Exchange ActiveSync 'in (EAS) aşağıdaki koşulları nasıl değerlendirdiği hakkında güncelleştirme sürecimiz vardır:

- Ülke, bölge veya IP adresine göre Kullanıcı konumu

- Oturum açma riski

- Cihaz platformu

Bu koşulları daha önce koşullu erişim ilkeleriniz üzerinde kullandıysanız, koşul davranışının değişebilir olduğunu unutmayın. Örneğin, daha önce bir ilkede Kullanıcı konumu koşulunu kullandıysanız, ilkeyi artık kullanıcının konumuna göre atlandığını görebilirsiniz.

---

## <a name="february-2019"></a>Şubat 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Yapılandırılabilir Azure AD SAML belirteci şifrelemesi (Genel Önizleme) 

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO

Artık, desteklenen herhangi bir SAML uygulamasını şifrelenmiş SAML belirteçleri alacak şekilde yapılandırabilirsiniz. Azure AD, bir uygulamayla yapılandırılıp kullanıldığında, Azure AD 'de depolanan bir sertifikadan alınan bir ortak anahtar kullanarak, verilmiş SAML onaylamalarını şifreler.

SAML belirteci şifrelemeyi yapılandırma hakkında daha fazla bilgi için bkz. [Azure AD SAML belirteci şifrelemesini yapılandırma](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleri kullanarak gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma

**Türü:** yeni özellik  
**Hizmet kategorisi:** erişim gözden geçirmeleri  
**Ürün özelliği:** idare

Artık grup üyeliği veya uygulama ataması için tek bir Azure AD erişim gözden geçirmesine birden çok grup veya uygulama ekleyebilirsiniz. Birden çok grup veya uygulama içeren erişim gözden geçirmeleri aynı ayarlarla ayarlanır ve tüm dahil olan gözden geçirenler aynı anda bildirilir.

Azure AD erişim gözden geçirmeleri kullanarak erişim incelemesi oluşturma hakkında daha fazla bilgi için bkz. [Azure AD erişim gözden geçirmeleriyle grup veya uygulama erişimi Incelemesi oluşturma](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Şubat 2019

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
Şubat 2019 ' de, uygulama galerisine federasyon desteği olan bu 27 yeni uygulamayı ekledik:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [mindtickalıbı](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [airstack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [ufuk ve qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Parlatıdea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [alerar](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-cloudgate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), izin tıklama, [brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [storegatesmartfile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [pexıp](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismik](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial) [](https://www.shareadream.org/how-it-works) [](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial) [ Her yerde LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU kampüs](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope verileri](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [NetOp Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [Purecyüksek, Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [tıklama üretkenlik platformu](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Geliştirilmiş Birleşik MFA/SSPR kaydı

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Self Servis parola sıfırlama  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Müşteri geri bildirimlerine yanıt olarak, birleştirilmiş MFA/SSPR kayıt önizleme deneyimini geliştirdik. böylece kullanıcılarınız hem MFA hem de SSPR için güvenlik bilgilerini daha hızlı kaydetmeye yardımcı olur. 

**Kullanıcılarınızın gelişmiş deneyimini bugün açmak için şu adımları izleyin:**

1. Genel yönetici veya Kullanıcı Yöneticisi olarak Azure portal oturum açın ve **> Kullanıcı ayarları ' na Azure Active Directory gidin > erişim paneli Önizleme özellikleri ayarlarını yönetin**. 

2. **Güvenlik bilgilerini kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilecek kullanıcılar – Yenile** seçeneği için **Seçili bir Kullanıcı grubu** veya **tüm kullanıcılar**için özellikleri açmayı seçin.

Önümüzdeki birkaç hafta içinde, zaten açık olmayan kiracılar için eski Birleşik MFA/SSPR kayıt önizleme deneyimini açma imkanını kaldıracağız.

**Kiracınız için denetimin kaldırılıp kaldırılmadığını görmek için şu adımları izleyin:**

1. Genel yönetici veya Kullanıcı Yöneticisi olarak Azure portal oturum açın ve **> Kullanıcı ayarları ' na Azure Active Directory gidin > erişim paneli Önizleme özellikleri ayarlarını yönetin**.  

2. **Güvenlik bilgilerini kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilecek kullanıcılar** **hiçbiri**olarak ayarlanırsa, bu seçenek kiracınızdan kaldırılır.

Daha önce, kullanıcılar için eski Birleşik MFA/SSPR kayıt önizleme deneyimini önceden açtığınızdan bağımsız olarak, eski deneyim gelecekteki bir tarihte kapatılacak. Bu nedenle, yeni ve gelişmiş deneyimle mümkün olan en kısa sürede geçmeniz önemle tavsiye ederiz.

Gelişmiş kayıt deneyimi hakkında daha fazla bilgi için bkz. [Azure AD BIRLEŞIK MFA ve parola sıfırlama kayıt deneyimi için seyrek geliştirmeler](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Kullanıcı akışları için ilke yönetimi deneyimi güncelleştirildi

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C

Kullanıcı akışları (daha önce, yerleşik ilkeler) için ilke oluşturma ve yönetim işlemini daha kolay bir şekilde güncelleştirdik. Bu yeni deneyim artık tüm Azure AD kiracılarınız için varsayılandır.

Portal ekranının üst kısmındaki **bize geri bildirim gönder** alanındaki gülümseme veya kaş simgeleri kullanarak ek geri bildirim ve öneriler sağlayabilirsiniz.

Yeni ilke yönetimi deneyimi hakkında daha fazla bilgi için, [şimdi Azure AD B2C JavaScript özelleştirmesine ve birçok yeni özellik](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) bloguna sahiptir.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Azure AD B2C tarafından sunulan belirli sayfa öğesi sürümlerini seçin

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C

Artık, Azure AD B2C tarafından sunulan sayfa öğelerinin belirli bir sürümünü seçebilirsiniz. Belirli bir sürümü seçerek, güncelleştirmelerinizi bir sayfada görüntülenmeden önce test edebilir ve öngörülebilir bir davranış sağlayabilirsiniz. Ayrıca, artık JavaScript özelleştirmeleriyle ilgili belirli sayfa sürümlerini zorunlu kılabilirsiniz. Bu özelliği açmak için Kullanıcı akışlarınızda **Özellikler** sayfasına gidin.

Sayfa öğelerinin belirli sürümlerini seçme hakkında daha fazla bilgi için, [artık Azure AD B2C JavaScript özelleştirmesine ve birçok yeni özellik](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) bloguna sahiptir.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C için yapılandırılabilir Son Kullanıcı parolası gereksinimleri (GA)

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C

Artık kuruluşunuzun parola karmaşıklığını, yerel Azure AD parola ilkenizi kullanmak yerine son kullanıcılarınız için ayarlayabilirsiniz. Kullanıcı akışlarınızın **Özellikler** dikey penceresinde (önceki adıyla yerleşik ilkeleriniz), **basit** veya **güçlü**bir parola karmaşıklığı seçebilir veya **özel** bir gereksinim kümesi oluşturabilirsiniz.

Parola karmaşıklığı gereksinim yapılandırması hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C parolalar için karmaşıklık gereksinimlerini yapılandırma](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Özel markalı kimlik doğrulama deneyimleri için yeni varsayılan şablonlar

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C

Kullanıcılarınız için özel bir markalı kimlik doğrulama deneyimi oluşturmak üzere Kullanıcı akışlarınızın **sayfa düzenleri** dikey penceresinde (daha önce yerleşik ilkeler olarak bilinir) bulunan yeni varsayılan şablonlarımızı kullanabilirsiniz.

Şablonları kullanma hakkında daha fazla bilgi için bkz. [Azure AD B2C artık JavaScript özelleştirmesi ve birçok yeni özellik içeriyor](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Ocak 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Tek seferlik geçiş kodu kimlik doğrulaması kullanarak B2B işbirliği Active Directory (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2B  
**Ürün özelliği:** B2B/B2C

Azure AD, Microsoft hesabı (MSA) veya Google Federasyonu gibi diğer yollarla kimlik doğrulamasından geçmediğiniz B2B Konuk kullanıcıları için bir kerelik geçiş kodu kimlik doğrulaması (OTP) sunuyoruz. Bu yeni kimlik doğrulama yöntemi, konuk kullanıcıların yeni bir Microsoft hesabı oluşturması gerekmediği anlamına gelir. Bunun yerine, bir daveti benimseme veya paylaşılan bir kaynağa erişirken, Konuk Kullanıcı bir e-posta adresine gönderilmek üzere geçici bir kod isteyebilir. Bu geçici kodu kullanarak Konuk Kullanıcı oturum açmaya devam edebilir.

Daha fazla bilgi için bkz. [e-posta bir kerelik geçiş kodu kimlik doğrulaması (Önizleme)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) ve blogda [Azure AD, herhangi bir hesabı kullanarak tüm kullanıcılar için paylaşım ve işbirliği yapma olanağı sunar](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Yeni Azure AD Uygulama Ara Sunucusu tanımlama bilgisi ayarları

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** erişim denetimi

Uygulama proxy 'Si aracılığıyla yayınlanan uygulamalarınız için kullanılabilen üç yeni tanımlama bilgisi ayarı ekledik:

- **Yalnızca HTTP tanımlama bilgisini kullanın.** Uygulama proxy 'Si erişiminizin ve oturum tanımlama bilgilerinizdeki **HttpOnly** bayrağını ayarlar. Bu ayarı açmak, istemci tarafı komut dosyası aracılığıyla tanımlama bilgilerinin kopyalanmasını veya değiştirilmesini önlemeye yardımcı olmak gibi ek güvenlik avantajları sağlar. Bu bayrağı açmanızı öneririz ( **Evet**' i seçin).

- **Güvenli tanımlama bilgisini kullanın.** Uygulama proxy 'Si erişiminizin ve oturum tanımlama bilgilerinde **güvenli** bayrağını ayarlar. Bu ayarı açmak, tanımlama bilgilerinin yalnızca HTTPS gibi TLS güvenli kanallarından aktarılmasını sağlamak için ek güvenlik avantajları sağlar. Bu bayrağı açmanızı öneririz ( **Evet**' i seçin).

- **Kalıcı tanımlama bilgisini kullanın.** Web tarayıcısı kapalıyken erişim tanımlama bilgilerinin süresinin dolmasını önler. Bu tanımlama bilgileri, erişim belirtecinin ömrü için en son. Ancak, süre sonu zamanına ulaşıldığında veya Kullanıcı tanımlama bilgisini el ile silerse tanımlama bilgileri sıfırlanır. Varsayılan ayar olarak, yalnızca süreçler arasında tanımlama bilgilerini paylaşmayan eski uygulamalar **için ayarı açarak**yapmanız önerilir.

Yeni tanımlama bilgileri hakkında daha fazla bilgi için bkz. [Azure Active Directory ' de şirket içi uygulamalara erişmek Için tanımlama bilgisi ayarları](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Ocak 2019

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
2019 Ocak 'ta, uygulama galerisine federasyon desteğiyle bu 35 yeni uygulamalar ekledik:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Fize](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [tatatci paleti](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [ın, cloudsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco şemsiye](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Erişim Yöneticisi](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [süre sonu anımsatıcısı](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [EVR Görüntüleyicisi](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [Corptax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [fiil](https://app.verb.net/login), [OPENLATCE](https://openlattice.com/agora), [theorgwiki](https://www.theorgwiki.com/signup), [pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [goodalıştırma araç seti](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [auditboard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [Call](https://webapp.callplease.com/create-account/create-account.html) [GTNEXUS SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE serviceınsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Ares for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [for Office 365](https://www.k2.com/O365), [XLEDGER](https://www.xledger.net/), [idıd Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [highdişli](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Fraz alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle csaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Yeni Azure AD Kimlik Koruması geliştirmeleri (Genel Önizleme)

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kimlik koruması  
**Ürün özelliği:** kimlik güvenliği ve koruması

Aşağıda aşağıdakiler dahil olmak üzere Azure AD Kimlik Koruması genel önizleme sunumuna aşağıdaki geliştirmeleri eklediğimiz duyurumuz ediyoruz.

- Güncelleştirilmiş ve daha tümleşik bir kullanıcı arabirimi

- Diğer API’ler

- Machine Learning ile iyileştirilmiş risk değerlendirmesi

- Riskli kullanıcılar ve riskli oturum açma işlemleri arasında ürün genelinde hizalama

Geliştirmeler hakkında daha fazla bilgi için bkz. [ne Azure Active Directory kimlik koruması (YENİLENDİ)?](https://aka.ms/IdentityProtectionDocs) daha fazla bilgi edinmek ve fikirlerinizi ürün içi istemler aracılığıyla paylaşmak için.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>İOS ve Android cihazlarda Microsoft Authenticator uygulaması için yeni uygulama kilidi özelliği

**Türü:** yeni özellik  
**Hizmet kategorisi:** Microsoft Authenticator uygulaması  
**Ürün özelliği:** kimlik güvenliği ve koruması

Tek seferlik geçiş kodlarınızın, uygulama bilgilerinizin ve uygulama ayarlarının daha güvenli olmasını sağlamak için Microsoft Authenticator uygulamasındaki uygulama kilidi özelliğini açabilirsiniz. Uygulama kilidi 'ni açmak, Microsoft Authenticator uygulamasını her açışınızda PIN 'inizi veya biyometri kullanarak kimlik doğrulaması yapmanız istenecektir.

Daha fazla bilgi için bkz. [uygulama sss Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Gelişmiş Azure AD Privileged Identity Management (PıM) dışa aktarma özellikleri

**Türü:** yeni özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management

Privileged Identity Management (PıM) yöneticileri artık tüm alt kaynaklar için rol atamaları içeren belirli bir kaynak için tüm etkin ve uygun rol atamalarını dışarı aktarabilir. Daha önce, yöneticilerin bir abonelik için rol atamalarının tamamen bir listesini alması ve her belirli kaynak için rol atamalarını dışarı aktarmaları gerekiyordu.

Daha fazla bilgi için bkz. [PIM 'de Azure Kaynak rolleri için etkinliği ve denetim geçmişini görüntüleme](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Kasım/Aralık 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Eşitleme kapsamından kaldırılan kullanıcılar artık yalnızca bulut hesaplarına geçmesiz

**Türü:** düzeltildi  
**Hizmet kategorisi:** Kullanıcı Yönetimi  
**Ürün özelliği:** dizini

>[!Important]
>Bu çözüm nedeniyle rahatsız etmemizi duyduk ve anladık. Bu nedenle, bu değişikliği kuruluşunuzda uygulamanızı daha kolay hale getirmek için bu süre kadar geri döndürüyoruz.

Active Directory Domain Services (AD DS) nesnesi eşitleme kapsamından dışlandıysa ve sonra aşağıdaki eşitleme döngüsündeki Azure AD 'ye geri dönüşüm kutusu 'na taşındıktan sonra bir kullanıcının DirSyncEnabled bayrağının **hatalı olarak yanlış** olarak geçtiğimiz bir hata düzeltildi. Bu düzeltmeyle ilgili bir sonuç olarak, Kullanıcı eşitleme kapsamından dışlandığından ve daha sonra Azure AD geri dönüşüm kutusu 'ndan geri yüklenirse, Kullanıcı hesabı şirket içi AD 'den eşitlenmiş olarak kalır ve bu nedenle, yetki kaynağı (SoA) olarak kaldığı için bulutta yönetilemez. Şirket içi AD.

Bu düzeltmeyle önce, DirSyncEnabled bayrağı false olarak geçildiğinde bir sorun oluştu. Bu hesaplar, bu hesapların yalnızca bulut nesnelerine dönüştürüldüğüne ve hesapların bulutta yönetilebileceğinden yanlış izlenimi verdi. Ancak, hesaplar hala SoA 'yi şirket içinde ve şirket içi AD 'den gelen tüm eşitlenmiş Özellikler (gölge öznitelikleri) olarak bekletilir. Bu durum, Azure AD 'de ve diğer bulut iş yüklerinde (Exchange Online gibi), bu hesapların AD 'den eşitlenmiş olduğunu ancak artık yalnızca bulut hesapları gibi davranmasının beklendiği bir şekilde daha fazla soruna neden oldu.

Şu anda, eşitlenmiş bir AD hesabını gerçekten yalnızca bulut hesabına dönüştürmenin tek yolu, bir arka uç işlemini SoA aktarmaya tetikleyen kiracı düzeyinde DirSync 'i devre dışı bırakmalıdır. Bu tür SoA değişikliği, şirket içi ilgili tüm öznitelikleri (LastDirSyncTime ve Shadow öznitelikleri gibi) temizlemek ve diğer bulut iş yüklerine bir sinyal göndermek için, ilgili nesnenin de yalnızca bulut hesabına dönüştürülmesi için bir sinyal göndermesi gerekir .

Bu, sonuçta, geçmişteki bazı senaryolarda, AD 'den eşitlenen bir kullanıcının ImmutableID özniteliğinde doğrudan güncelleştirmelerin yapılmasını önler. Tasarım yaparak, adın gösterdiği gibi Azure AD 'deki bir nesnenin ImmutableID, sabit olması anlamına gelir. Azure AD Connect Health ve Azure AD Connect eşitleme istemcisinde uygulanan yeni özellikler, bu senaryolara yönelik olarak kullanılabilir:

- **Aşamalı bir yaklaşımda çok sayıda kullanıcı için büyük ölçekli ImmutableID güncelleştirmesi**
  
  Örneğin, uzun AD DS ormanlar arası geçiş yapmanız gerekir. Çözüm: **kaynak bağlantısını yapılandırmak** Için Azure AD Connect kullanın ve Kullanıcı geçirildiğinde, mevcut ImmutableID değerlerini Azure AD DS ad 'den yeni ormanın ms-DS-tutarlılık-GUID özniteliğine kopyalayın. Daha fazla bilgi için bkz. [MS-DS-ımıbu GUID 'ı Sourcetutturucu olarak kullanma](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Tek bir görüntüsündeki birçok kullanıcı için büyük ölçekli ImmutableID güncelleştirmeleri**

  Örneğin, Azure AD Connect uygulanırken bir hata yaparsınız ve şimdi Sourcetutturucu özniteliğini değiştirmeniz gerekir. Çözüm: kiracı düzeyinde DirSync 'i devre dışı bırakın ve tüm geçersiz ImmutableID değerlerini temizleyin. Daha fazla bilgi için bkz. [Office 365 için dizin eşitlemeyi devre dışı bırakma](/office365/enterprise/turn-off-directory-synchronization).

- **Azure AD 'de mevcut bir kullanıcıyla şirket içi Kullanıcı Ile yeniden eşleştir** Örneğin, AD DS ' de yeniden oluşturulmuş bir kullanıcı var olan bir Azure AD hesabıyla (yalnız bırakılmış nesne) yeniden eşleştirmek yerine Azure AD hesabında yinelenen bir öğe oluşturur. Çözüm: kaynak bağlayıcısını/ImmutableID yeniden eşlemek için Azure portal Azure AD Connect Health kullanın. Daha fazla bilgi için bkz. [yalnız bırakılmış nesne senaryosu](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Son değişiklik: Azure Izleyici aracılığıyla denetim ve oturum açma günlüğü şemasında yapılan güncelleştirmeler

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Azure Izleyici aracılığıyla hem denetim hem de oturum açma günlüğü akışlarını yayımlıyoruz, böylece günlük dosyalarını SıEM araçlarınızla veya Log Analytics ile sorunsuzca tümleştirebilirsiniz. Geri bildirimlerinize göre ve bu özelliğin genel kullanılabilirlik duyurusunu hazırlıyoruz, şemanızda aşağıdaki değişiklikleri yapıyoruz. Bu şema değişiklikleri ve ilgili belge güncelleştirmeleri, Ocak 'un ilk haftası tarafından gerçekleşir.

#### <a name="new-fields-in-the-audit-schema"></a>Denetim şemasındaki yeni alanlar
Kaynak üzerinde gerçekleştirilen işlem türünü sağlamak için yeni bir **Işlem türü** alanı ekliyoruz. Örneğin, **ekleme**, **güncelleştirme**veya **silme**.

#### <a name="changed-fields-in-the-audit-schema"></a>Denetim şemasındaki alanları değiştirme
Aşağıdaki alanlar denetim şemasında değişiyor:

|Alan adı|Değişen|Eski değerler|Yeni değerler|
|----------|------------|----------|----------|
|Kategori|Bu **hizmet adı** alanıdır. Artık **denetim kategorileri** alanıdır. **Hizmet adı** **Loggedbyservice** alanı olarak yeniden adlandırıldı.|<ul><li>Hesap Sağlama</li><li>Çekirdek Dizin</li><li>Self servis parola sıfırlama</li></ul>|<ul><li>Kullanıcı Yönetimi</li><li>Grup Yönetimi</li><li>Uygulama yönetimi</li></ul>|
|targetResources|En üst düzeyde **Targetresourcetype** içerir.|&nbsp;|<ul><li>İlke</li><li>Uygulama</li><li>Kullanıcı</li><li>Grup</li></ul>|
|loggedByService|Denetim günlüğünü oluşturan hizmetin adını sağlar.|Null|<ul><li>Hesap Sağlama</li><li>Çekirdek Dizin</li><li>Self servis parola sıfırlama</li></ul>|
|Sonuç|Denetim günlüklerinin sonucunu sağlar. Bu, daha önce numaralandırılmıştı, ancak şimdi gerçek değeri gösteriyoruz.|<ul><li>0</li><li>1</li></ul>|<ul><li>Başarılı</li><li>Hata</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Oturum açma şemasında değiştirilen alanlar
Aşağıdaki alanlar, oturum açma şemasında değişiyor:

|Alan adı|Değişen|Eski değerler|Yeni değerler|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Bu **Conditionalaccesspolicies** alanıdır. Artık **appliedConditionalAccessPolicies** alanıdır.|Değişiklik yok|Değişiklik yok|
|conditionalAccessStatus|Oturum açma sırasında koşullu erişim Ilkesi durumunun sonucunu sağlar. Bu, daha önce numaralandırılmıştı, ancak şimdi gerçek değeri gösteriyoruz.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Başarılı</li><li>Hata</li><li>Uygulanmadı</li><li>Devre dışı</li></ul>|
|appliedConditionalAccessPolicies: sonuç|Oturum açma sırasında bireysel koşullu erişim Ilkesi durumunun sonucunu sağlar. Bu, daha önce numaralandırılmıştı, ancak şimdi gerçek değeri gösteriyoruz.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Başarılı</li><li>Hata</li><li>Uygulanmadı</li><li>Devre dışı</li></ul>|

Şema hakkında daha fazla bilgi için bkz. [Azure izleyici 'de Azure AD denetim günlükleri şemasını yorumlama (Önizleme)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Denetimli makine öğrenimi modeli ve risk puanı altyapısında kimlik koruması geliştirmeleri

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kimlik koruması  
**Ürün yeteneği:** Risk puanları

Kimlik koruması ile ilgili Kullanıcı ve oturum açma risk değerlendirmesi altyapısı geliştirmeleri, Kullanıcı risk doğruluğu ve kapsamını geliştirmeye yardımcı olabilir. Yöneticiler, Kullanıcı risk düzeyinin artık belirli algılamaların risk düzeyine doğrudan bağlı olmadığını ve riskli oturum açma olaylarının sayısı ve düzeyinde bir artış olduğunu fark edebilir.

Risk algılamaları artık kullanıcının oturum açma işlemlerinin ek özelliklerini ve bir algılama düzenlerini kullanarak Kullanıcı riskini hesaplayan, denetimli makine öğrenimi modeli tarafından değerlendirilir. Yönetici, bu modele bağlı olarak, bu kullanıcıyla ilişkili algılamalar düşük veya orta riskli olsa bile, yüksek riskli puanları olan kullanıcılar tarafından bulunabilir. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Yöneticiler, Microsoft Authenticator uygulamasını kullanarak kendi parolalarını sıfırlayabilir (Genel Önizleme)

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Self Servis parola sıfırlama  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Azure AD yöneticileri artık Microsoft Authenticator uygulama bildirimlerini veya herhangi bir mobil kimlik doğrulayıcı uygulamasının veya donanım belirtecinden bir kodu kullanarak kendi parolasını sıfırlayabilir. Yöneticiler, kendi parolasını sıfırlamak için şu yöntemlerden ikisini de kullanabilir:

- Microsoft Authenticator uygulama bildirimi

- Diğer mobil kimlik doğrulayıcı uygulaması/donanım belirteci kodu

- Email

- Telefon araması

- Kısa mesaj

Parolaları sıfırlamak için Microsoft Authenticator uygulamasını kullanma hakkında daha fazla bilgi için bkz. [Azure AD self servis parola sıfırlama-mobil uygulama ve SSPR (Önizleme)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Yeni Azure AD bulut aygıtı yönetici rolü (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** Cihaz kaydı ve yönetimi  
**Ürün yeteneği:** Erişim denetimi

Yöneticiler, bulut aygıtı yönetici görevlerini gerçekleştirmek için kullanıcıları yeni bulut cihaz yöneticisi rolüne atayabilir. Bulut cihaz yöneticileri rolüne atanan kullanıcılar, Azure AD 'de cihazları etkinleştirebilir, devre dışı bırakabilir ve silebilir ve Azure portal Windows 10 BitLocker anahtarlarını (varsa) okuyabilir.

Roller ve izinler hakkında daha fazla bilgi için bkz. [Azure Active Directory yönetici rolleri atama](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Azure AD 'de yeni etkinlik zaman damgasını kullanarak cihazlarınızı yönetin (Genel Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** Cihaz kaydı ve yönetimi  
**Ürün yeteneği:** Cihaz yaşam döngüsü yönetimi

Ortamınızda eski cihazları kullanmaktan kaçınmak için, Azure AD 'de kuruluşların cihazlarını yenilemeniz ve devre dışı bırakmanız gerektiğini fark ettik. Azure AD, bu işleme yardımcı olmak için artık cihazlarınızı yeni bir etkinlik zaman damgasıyla güncelleştirir ve cihaz yaşam döngüsünü yönetmenize yardımcı olur.

Bu zaman damgasını alma ve kullanma hakkında daha fazla bilgi için bkz [. nasıl yapılır: Azure AD 'de eski cihazları yönetme](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Yöneticiler, kullanıcıların her cihazda kullanım koşullarını kabul etmesini gerektirebilir

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** idare
 
Yöneticiler artık kullanıcılarınızın kiracınızda kullandıkları her cihazda kullanım koşullarınızı kabul etmesini gerektirmek için **kullanıcıların her cihazda izin vermesini iste** seçeneğini açabilir.

Daha fazla bilgi için [Azure Active Directory kullanım koşulları özelliğinin cihaz başına kullanım koşulları bölümüne](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)bakın.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Yöneticiler, yinelenen bir zamanlamaya göre kullanım koşulları 'nı zaman dolacak şekilde yapılandırabilir

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** idare
 

Yöneticiler artık, belirtilen yinelenen zamanlamanıza bağlı olarak tüm kullanıcılarınız için kullanım süresini dolacak şekilde **süre sonu onayları** seçeneğini açabilir. Zamanlama yıllık, iki yıllık, üç aylık veya aylık olabilir. Kullanım koşulları sona erdiğinde, kullanıcıların yeniden kabul etmesi gerekir.

Daha fazla bilgi için [Azure Active Directory kullanım koşulları özelliğinin kullanım koşulları ekleme bölümüne](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)bakın.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Yöneticiler, her bir kullanıcının zamanlamasını temel alarak kullanım koşullarını yapılandırabilir

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** idare

Yöneticiler artık kullanıcının kullanım koşullarını yeniden kabul etmesi için bir süre belirtebilir. Örneğin, Yöneticiler kullanıcıların her 90 günde bir kullanım koşulları 'nı yeniden kabul etmesi gerektiğini belirtebilir.

Daha fazla bilgi için [Azure Active Directory kullanım koşulları özelliğinin kullanım koşulları ekleme bölümüne](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)bakın.
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory rolleri için yeni Azure AD Privileged Identity Management (PıM) e-postaları

**Türü:** yeni özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management
 
Azure AD Privileged Identity Management (PıM) kullanan müşteriler artık son yedi güne ait aşağıdaki bilgiler dahil olmak üzere haftalık bir Özet e-postası alabilir:

- En uygun ve kalıcı rol atamalarına genel bakış

- Rolleri etkinleştiren kullanıcı sayısı

- PıM 'de rollere atanan kullanıcı sayısı

- PıM dışında rollere atanan kullanıcı sayısı

- PıM 'de "kalıcı hale getirilir" Kullanıcı sayısı

PıM ve kullanılabilir e-posta bildirimleri hakkında daha fazla bilgi için bkz. [PIM 'de e-posta bildirimleri](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Grup tabanlı lisanslama genel kullanıma sunuldu

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** dizini

Grup tabanlı lisanslama genel önizleme aşamasındadır ve genel kullanıma sunulmuştur. Bu genel yayının bir parçası olarak, bu özelliği daha ölçeklenebilir hale aldık ve tek bir kullanıcı için grup tabanlı lisanslama atamalarını yeniden işleme özelliği ve Office 365 E3/a3 lisanslarıyla grup tabanlı lisanslama kullanma özelliği ekledik.

Grup tabanlı lisanslama hakkında daha fazla bilgi için bkz. [Azure Active Directory grup tabanlı lisanslama nedir?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Kasım 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
2018 Kasım 'da, uygulama galerisine federasyon desteğiyle bu 26 yeni uygulama ekledik:

[Corestack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetConsent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [ehour](https://getehour.com/try-now), [](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [appınux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [drivedolar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [sonsuz kampüs](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [alaya](https://alayagood.com/en/demo/), [heyarkadaş](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [zenegy, Iş Merkezi 365](https://accounting.zenegy.com/), tek [köprü üye portalı](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [ıvanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [allbağlanacak SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [parçalı uygulamalar-klasik test ](https://test.plexonline.com/signon), [Parçalı uygulamalar – klasik](https://www.plexonline.com/signon), [parçalı uygulamalar-UX testi](https://test.cloud.plex.com/sso), [parçalı uygulamalar – UX](https://cloud.plex.com/sso), [parçalı uygulamalar – IAM](https://accounts.plex.com/), [öğelerini-childbakım kayıtları, katılım, & finansal izleme sistemi](https://getcrafts.ca/craftsregistration) 

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Ekim 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD günlükleri artık Azure Log Analytics (genel Önizleme) ile çalışma

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Azure Log Analytics için artık Azure AD günlüklerinizi iletebilir duyurmaktan Mutluluk duyuyoruz! Bu en çok istenen özellik analytics'e işletmenizi, işlemleri ve güvenlik yanı sıra altyapınızı izlemeye yardımcı olmak için bir yol için daha iyi erişmesini yardımcı olur. Daha fazla bilgi için [Azure Active Directory etkinlik günlükleri artık Azure Log Analytics'te](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Yeni Federasyon uygulamaları kullanılabilir Azure AD uygulama galerisinde - Ekim 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme

Ekim 2018'de Federasyon ile 14 Bu yeni uygulamalar için uygulama Galerisi desteği ekledik:

[Ödül Puanlarım](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), tuş takımı, [ON24 sanal ortam](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler üç](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot denetimi](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho kayıt](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-posta bildirimleri Azure AD etki alanı Hizmetleri

**Türü:** yeni özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün özelliği:** Azure AD Domain Services

Azure AD Domain Services yönetilen etki alanınız ile ilgili sorunları veya yanlış yapılandırmalar hakkında Azure portalındaki uyarılar sağlar. Bu uyarılar, desteğe başvurun gerek kalmadan sorunları düzeltmek deneyebilmek adım adım kılavuzlar içerir.

Ekim ayında başlayarak, yönetilen etki alanınız için bildirim ayarlarını özelleştirmek mümkün olacaktır için yeni uyarılar ortaya çıktığında, insanların sürekli güncelleştirmeler için portalı denetleyin gereğini ortadan kaldırır, atanmış bir gruba e-posta gönderilir.

Daha fazla bilgi için [bildirim ayarları Azure AD Etki Alanı Hizmetleri'nde](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Özel etki alanları silinecek ForceDelete etki alanı API kullanarak azure AD portalı destekler 

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** dizin yönetimi  
**Ürün özelliği:** dizini

Artık ForceDelete etki alanı API başvuruları, kullanıcılar, gruplar ve uygulamalar gibi özel bir etki alanı adınızla (contoso.com) öğesinden geri ilk varsayılan etki alanı adı (zaman uyumsuz olarak yeniden adlandırarak özel etki alanı silmek için kullanabileceğiniz duyurmaktan Mutluluk duyuyoruz contoso.onmicrosoft.com).

Bu değişiklik, özel etki alanı adı kuruluşunuz artık kullanıyorsa veya başka bir Azure AD ile etki alanı adı kullanmanız gerekiyorsa daha hızlı bir şekilde silmek için yardımcı olur.

Daha fazla bilgi için [bir özel etki alanı adını silme](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Eylül 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Dinamik gruplar için güncelleştirilmiş yönetici rolü izinleri

**Türü:** düzeltildi  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün özelliği:** işbirliği

Özel yönetici rolleri oluşturabilir ve grubun sahibi olmanıza gerek kalmadan dinamik Üyelik kuralları güncelleştirmek için bir sorunu düzelttik.

Rolü şunlardır:

- Genel yönetici

- Intune Yöneticisi

- Kullanıcı Yöneticisi

Daha fazla bilgi için [dinamik bir grup oluşturun ve durumunu denetle](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Bazı üçüncü taraf uygulamalar için Basitleştirilmiş Çoklu Oturum Açma (SSO) yapılandırma ayarları

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO

Biz yukarı çoklu oturum açma (SSO) için yazılım (SaaS) hizmet olarak ayarlama uygulamalar her apps yapılandırması benzersiz niteliği nedeniyle zor olabileceğini unutmayın. Aşağıdaki üçüncü taraf SaaS uygulamaları için SSO yapılandırma ayarlarını otomatik olarak doldurmak için Basitleştirilmiş yapılandırma deneyimi oluşturduk:

- Zendesk

- Arcgıs Online

- Jamf Pro

Bu tek tıklamayla deneyimi kullanmaya başlamak için Git **Azure portalında** > **SSO yapılandırma** sayfasını. Daha fazla bilgi için [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Verilerinizin bulunduğu azure Active Directory -? Sayfa

**Türü:** yeni özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** GoLocal

Şirketinizin bölgeyi seçin **Azure etkin verilerinizin bulunduğu dizin -** sayfasına hangi Azure veri merkezinde kalan tüm Azure AD Hizmetleri, Azure AD verileri barındırıldığı görünümü. Tarafından özel bilgileri filtreleyebilirsiniz şirketinizin bölge için Azure AD Hizmetleri.

Daha fazla bilgi için bu özelliğe erişmek için [Azure etkin verilerinizin bulunduğu dizin -](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Uygulamalarım Erişim paneli için yeni dağıtım planı kullanılabilir

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulamalarım  
**Ürün özelliği:** SSO

My Apps erişim panelinde kullanılabilen yeni dağıtım planını göz atın (https://aka.ms/deploymentplans).
My Apps erişim panelinde bulmak ve uygulamalarını erişmek için tek bir yerde kullanıcılara sağlar. Bu portal, uygulamaları ve gruplara erişim isteği veya diğerleri adına bu kaynaklara erişimi yönetme gibi fırsatlar Self Servis kullanıcılarla da sağlar.

Daha fazla bilgi için [uygulamalarım portalı nedir?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure portal Oturum Açma Günlükleri sayfasında yeni Sorun Giderme ve Destek sekmesi

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Azure portal **oturum açma** Işlemlerinin yeni **sorun giderme ve destek** sekmesi, yöneticilerin ve Destek mühendislerinin Azure AD oturum açma konularıyla ilgili sorunları gidermelerine yardımcı olmak için tasarlanmıştır. Bu yeni sekme, sorunu çözmeye yardımcı olmak için hata kodu, hata iletisi ve düzeltme önerilerini (varsa) sağlar. Sorunu çözmek yapamıyorsanız, ayrıca, kullanarak bir destek bileti oluşturmak için yeni bir yolunu sunuyoruz **Panoya Kopyala** deneyimi, hangi doldurur **istek kimliği** ve **Tarih(UTC)** destek biletiniz günlük dosyasında alanları.  

![Yeni sekmeyi gösteren oturum açma günlükleri](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Dinamik üyelik kuralları oluşturmak için kullanılan özel uzantı özellikleri için gelişmiş destek

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün özelliği:** işbirliği

Bu güncelleştirmeyle, artık tıklayabilirsiniz **özel uzantı özelliklerini alma** bağlantı dinamik kullanıcı grubu kuralı Oluşturucusu'ndan, benzersiz uygulama Kimliğinizi girin ve dinamik oluşturulurken kullanılacak özel uzantı özelliklerinin tam listesini al kullanıcılar için üyelik kuralı. Bu liste, bu uygulama için tüm yeni özel uzantı özellikleri almak için aynı zamanda yenilenebilir.

Özel uzantı özellikleri için dinamik üyelik kurallarını kullanma hakkında daha fazla bilgi için bkz. [uzantı özellikleri ve özel uzantı özellikleri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim için yeni onaylanan istemci uygulamaları

**Türü:** değişiklik planı  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Aşağıdaki uygulamalar listede yer [onaylı istemci uygulamalar](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Daha fazla bilgi için bkz.

- [Azure AD uygulama tabanlı koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 Kilit ekranında Self Servis Parola Sıfırlama için yeni destek

**Türü:** yeni özellik  
**Hizmet kategorisi:** SSPR  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Bu yeni özelliği ayarlandıktan sonra kullanıcılarınız parolalarını sıfırlamak için bir bağlantı göreceksiniz **kilit** Windows 7, Windows 8 veya Windows 8.1 çalıştıran bir cihaz ekranı. Bu bağlantıya tıklayarak, kullanıcı, web tarayıcısı gibi aynı parola sıfırlama işlem akışında yönlendirilir.

Daha fazla bilgi için [parola Windows 7, 8 ve 8.1 sıfırlama olanağı tanıma](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Değişiklik bildirimi: Yetkilendirme kodları artık yeniden kullanılamayacak 

**Türü:** değişiklik planı  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

15 Kasım 2018'de başlayarak, Azure AD uygulamaları için daha önce kullanılan kimlik doğrulama kodlarını kabul durdurur. Bu güvenlik değişiklik v1 ve v2 Uç noktalara zorlanmasını sağlar ve Azure AD OAuth belirtimi ayarlarına uygun olarak çıkarmak yardımcı olur.

Uygulamanız için birden fazla kaynak belirteçlerini almak için yetkilendirme kodları yeniden kullanır, bir yenileme belirteci almak için kodu kullanın ve ardından diğer kaynaklar için ek belirteçlerini almak için yenileme belirtecini kullanmak öneririz. Yetkilendirme kodları yalnızca bir kez kullanılabilir, ancak yenileme belirteçleri birden fazla kaynak arasında birden çok kez kullanılabilir. Kimlik doğrulaması kodu OAuth kod akışı sırasında yeniden başlatmayı deneyen uygulama invalid_grant hata alırsınız.

Bu ve diğer protokolleri güvenlikle ilgili değişiklikler için bkz. [kimlik doğrulaması için Yenilikler tam listesini](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD uygulama galerisinde yeni Federasyon Uygulamaları kullanılabilir - Eylül 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
Eylül 2018'de Federasyon ile 16 Bu yeni uygulamalar için uygulama Galerisi desteği ekledik:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet yazılım işe](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [Arcgıs Kurumsal](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ JDA bulut](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), ı, Azure için Enlyft SSO [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Ek talep dönüşümü yöntemleri için destek

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO

ToLower() ve SAML belirteçlerini SAML tabanlı uygulanabilir ToUpper(), yeni talep dönüştürme yöntemleri tanıttık **çoklu oturum açma yapılandırması** sayfası.

Daha fazla bilgi için [Azure AD'de kurumsal uygulamalar için SAML belirtecinde verilen talepleri özelleştirme](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Güncelleştirilmiş SAML tabanlı uygulama yapılandırması kullanıcı arabirimi (önizleme)

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO

Bizim güncelleştirilmiş SAML tabanlı uygulama yapılandırması kullanıcı Arabirimi bir parçası olarak, elde edecekleriniz:

- SAML tabanlı uygulamalarınızı yapılandırmak için bir güncelleştirilmiş izlenecek deneyimi.

- Eksik veya yanlış yapılandırmanızda sunulanlar hakkında daha fazla görünürlük sağlar.

- Birden çok e-posta adresleri için sona erme sertifika bildirim ekleme yeteneği.

- Yeni Talep dönüştürme yöntemleri ToLower() ve ToUpper() ve daha fazlası.

- Kendi belirteç imzalama sertifikası, Kurumsal uygulamalarınız için karşıya yüklemek için bir yol.

- SAML uygulamaları için Nameıd biçimi yolunu ve dizin uzantılarını olarak Nameıd değeri ayarlamak için bir yol.

Bu güncelleştirilmiş bir görünüm üzerinde etkinleştirmek için tıklayın **yeni deneyimimizi deneyin** üst öğesinden bağlantısı **çoklu oturum açma** sayfası. Daha fazla bilgi için [Öğreticisi: yapılandırma SAML tabanlı çoklu oturum açma için Azure Active Directory ile bir uygulama](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>Ağustos 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory IP adresi aralıklarındaki değişiklikler

**Türü:** değişiklik planı  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** platformu

Daha büyük IP aralıklarını, güvenlik duvarları, yönlendiriciler veya ağ güvenlik grupları, Azure AD IP adresi aralıklarını yapılandırdığınız anlamına güncelleştirmeniz gerekecektir Azure AD'ye kullanıma sunduğumuz. Azure AD yeni uç nokta eklediğinde, güvenlik duvarı, yönlendirici veya ağ güvenlik grupları IP aralığı yapılandırmaları yeniden değiştirmek zorunda kalmamanız için bu güncelleştirmeyi yapıyoruz. 

Ağ trafiği için bu yeni aralıklar sonraki iki ay içinde taşınıyor. Kesintisiz hizmet ile devam etmek için bu güncelleştirilmiş değerleri 10 Eylül 2018'den önce IP adreslerinizi eklemeniz gerekir:

- 20.190.128.0/18 

- 40.126.0.0/18 

Tüm ağ trafiğinizin taşındı kadar yeni aralıklar için eski IP adres aralıklarını kaldırılmıyor kesinlikle öneririz. Güncelleştirmeler taşıma hakkında ve ne zaman eski aralıkları kaldırabilirsiniz öğrenmek için bkz [Office 365 URL'leri ve IP adresi aralıkları](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Değişiklik bildirimi: Yetkilendirme kodları artık yeniden kullanılamayacak 

**Türü:** değişiklik planı  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

15 Kasım 2018'de başlayarak, Azure AD uygulamaları için daha önce kullanılan kimlik doğrulama kodlarını kabul durdurur. Bu güvenlik değişiklik v1 ve v2 Uç noktalara zorlanmasını sağlar ve Azure AD OAuth belirtimi ayarlarına uygun olarak çıkarmak yardımcı olur.

Uygulamanız için birden fazla kaynak belirteçlerini almak için yetkilendirme kodları yeniden kullanır, bir yenileme belirteci almak için kodu kullanın ve ardından diğer kaynaklar için ek belirteçlerini almak için yenileme belirtecini kullanmak öneririz. Yetkilendirme kodları yalnızca bir kez kullanılabilir, ancak yenileme belirteçleri birden fazla kaynak arasında birden çok kez kullanılabilir. Kimlik doğrulaması kodu OAuth kod akışı sırasında yeniden başlatmayı deneyen uygulama invalid_grant hata alırsınız.

Bu ve diğer protokolleri güvenlikle ilgili değişiklikler için bkz. [kimlik doğrulaması için Yenilikler tam listesini](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Self servis parola (SSPR) ve Multi-Factor Authentication (MFA) için yakınsanmış güvenlik bilgileri yönetimi

**Türü:** yeni özellik  
**Hizmet kategorisi:** SSPR  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Bu yeni özellik SSPR ve mfa'yı tek bir konum ve deneyimi için güvenlik bilgilerini (örneğin, telefon numarası, mobil uygulama ve benzeri) yönetme kişiler yardımcı olur; kıyasla daha önce burada iki farklı konumlarda bitti dedik.

Bu yakınsanmış deneyimi de SSPR'ı veya mfa'yı kullanan kişiler için çalışır. Ayrıca, kuruluşunuz MFA veya SSPR kayıt zorunlu değil, kişiler uygulamalarım portalında kuruluşunuz tarafından izin verilen tüm MFA veya SSPR güvenlik bilgisi yöntemleri yine de kaydedebilirsiniz.

Bu bir katılım genel önizlemesidir. Yöneticilerin yeni deneyimi (istenirse) bir kiracıdaki tüm kullanıcılar veya seçili bir grup için kapatabilirsiniz. Yakınsanmış deneyimi hakkında daha fazla bilgi için bkz. [Yakınsanan experience blog'a](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD Uygulama Ara Sunucusu uygulamalarında yeni Yalnızca HTTP tanımlama bilgileri ayarı

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** erişim denetimi

Çağrılan, bir yeni ayar yoktur **azaltmaya** uygulamalarınızdaki uygulama proxy'si. Bu ayar, her iki uygulama ara sunucusu erişim ve oturum tanımlama bilgileri HTTP yanıt üst bilgisinde HTTPOnly bayrağı dahil olmak üzere, erişim tanımlama bilgisine istemci tarafı komut dosyasından durduruluyor ve daha da kopyalama gibi eylemleri engelleyen ek güvenlik sağlamaya yardımcı olur veya tanımlama bilgisi değiştiriliyor. Bu bayrak daha önce kullanılmamış olsa da, tanımlama bilgilerinizi her zaman silinmiş şifrelenir ve aktarılan yanlış değişikliklere karşı korumaya yardımcı olmak için bir SSL bağlantısı kullanma.

Bu ayarı, Uzak Masaüstü gibi ActiveX denetimlerini kullanan uygulamalar ile uyumlu değildir. Bu durumda kullanıyorsanız, bu ayarı devre dışı bırakmak öneririz.

Azaltmaya ayarı hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure kaynakları için Privileged Identity Management (PIM), Yönetim Grubu kaynak türlerini destekler

**Türü:** yeni özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management
 
Just-In-Time etkinleştirme ve atama ayarları şimdi zaten abonelik, kaynak grupları ve kaynakları (örneğin, VM'ler, uygulama hizmetleri ve diğer) için yaptığınız gibi yönetim grubu kaynak türleri için de uygulanabilir. Ayrıca, bir yönetim grubu için yönetici erişimi sağlayan bir rolü olan herkes bulabilir ve bu kaynağın PIM yönetme.

PIM ve Azure kaynakları hakkında daha fazla bilgi için bkz. [bulma ve Privileged Identity Management'ı kullanarak Azure kaynaklarını yönetme](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Uygulama erişimi (önizleme) Azure AD portalına daha hızlı erişim sağlar

**Türü:** yeni özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management
 
Bugün, PIM kullanarak rol etkinleştirme yapılırken, üzerinde etkili olması izinler için 10 dakika sürebilir. Şu anda genel Önizleme aşamasında olan uygulama erişimi kullanmayı tercih ederseniz Yöneticiler etkinleştirme isteği tamamlandıktan hemen sonra Azure AD portalına erişebilir.

Şu anda uygulama erişimi yalnızca Azure kaynakları ve Azure AD portalı deneyiminin destekler. PIM ve uygulama erişimi hakkında daha fazla bilgi için bkz: [Azure AD Privileged Identity Management nedir?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD uygulama galerisinde yeni Federasyon Uygulamaları kullanılabilir - Ağustos 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
Ağustos 2018'de Federasyon ile 16 Bu yeni uygulamalar için uygulama Galerisi desteği ekledik:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [ilişkisiz Bridgeline](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Tariflerinizden Labs - mobil ve Web testi](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta ağları bağlayıcı](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [yaptığımız şekilde](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [(tarafından Inlogik) ProMaster](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - gider raporları](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 canlı sohbet](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Yerel Tableau desteği artık Azure AD Uygulama Ara Sunucusu içinde kullanılabilir

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** erişim denetimi

Bizim güncelleştirmesi ile Openıd Connect bizim ön kimlik doğrulama protokolü için OAuth 2.0 kodu verme protokolü, artık Tableau uygulaması Ara sunucusu ile kullanılacak tüm ek yapılandırma adımları gerekir. Bu protokol değişiklik uygulama proxy'si, JavaScript ve HTML etiketleri yaygın olarak desteklenen HTTP yönlendirmeleri kullanarak daha modern uygulamalar daha iyi destek de yardımcı olur.

Tableau için yerel destek hakkında daha fazla bilgi için bkz: [artık yerel Tableau desteği ile Azure AD uygulama proxy'si](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Azure Active Directory'de B2B konuk kullanıcıları için kimlik sağlayıcısı olarak Google eklemek için yeni destek (Önizleme)

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2B  
**Ürün özelliği:** B2B/B2C

Google ile bir Federasyon kuruluşunuzdaki ayarlayarak, davet edilen Gmail kullanıcılar oturum paylaşılan uygulamaları ve kişisel bir Microsoft Account (MSA) veya bir Azure AD hesabı oluşturmak zorunda kalmadan, var olan Google hesaplarını kullanarak kaynakları yapmanız gerekiyor.

Bu bir katılım genel önizlemesidir. Google Federasyon hakkında daha fazla bilgi için bkz: [B2B Konuk kullanıcılar için kimlik sağlayıcısı ekle Google](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Temmuz 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory e-posta bildirimleri iyileştirmeleri

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** kimlik yaşam döngüsü yönetimi
 
Azure Active Directory (Azure AD) e-postaları artık gönderen e-posta adresi ve gönderen görünen adı, değişiklikleri yanı sıra, güncelleştirilmiş bir tasarım aşağıdaki hizmetlerinden gönderilen zaman özellik:
 
- Azure AD erişim gözden geçirmeleri
- Azure AD Connect Health 
- Azure AD Kimlik Koruması 
- Azure AD Privileged Identity Management
- Kurumsal uygulama süresi dolan sertifika bildirimleri
- Kurumsal uygulama sağlama hizmet bildirimleri
 
E-posta bildirimleri şu e-posta adresinden gönderilir ve görünen ad:

- E-posta adresi: azure-noreply@microsoft.com
- Görünen ad: Microsoft Azure
 
Bir örnek, bazı yeni e-posta tasarımları ve daha fazla bilgi için bkz [e-posta bildirimleri Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD Etkinlik Günlükleri artık Azure İzleyici'de kullanılabilir

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Azure AD etkinlik günlüklerini Azure İzleyicisi'ni (Azure'nın platform genelinde izleme hizmeti) için genel önizlemede kullanıma sunulmuştur. Azure İzleyici, uzun süreli saklama ve bu geliştirmeler yanı sıra sorunsuz tümleştirme sunar:

- Günlük dosyalarınızın kendi Azure depolama hesabına yönlendirme ile uzun süreli saklama için.

- Yazma veya özel komut dosyaları korumak gerektirmeden sorunsuz SIEM tümleştirmesi.

- Kendi özel çözümler, analiz araçları ve Olay yönetimi çözümleri ile sorunsuz tümleştirme sağlar.

Blogumuzu bu yeni özellikler hakkında daha fazla bilgi için bkz. [Azure AD etkinlik günlükleri Tanılama, artık genel önizlemede Azure İzleyicisi'nde](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) ve Belgelerimizi, [Azure'da Azure Active Directory etkinlik günlükleri İzleyici (Önizleme)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Azure AD oturum açma raporuna koşullu erişim bilgileri eklendi

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** kimlik güvenliği ve koruması
 
Bu güncelleştirme, hangi ilkelerin ilke sonucu ile birlikte bir kullanıcı oturum açtığı zaman değerlendirilen görmenize olanak tanır. Ayrıca, rapor artık eski protokol trafiğini belirleyebilmeniz kullanıcı tarafından kullanılan istemci uygulaması türünü içerir. Rapor girişleri kullanıcıya yönelik hata iletisinde bulunabilir ve belirleme ve giderme eşleşen oturum açma isteği için kullanılan bir bağıntı kimliği için artık aranabilir.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Oturum açma etkinlik günlükleri ile eski kimlik doğrulamalarını görüntüleyin

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama
 
Sunulmasıyla birlikte **istemci uygulaması** alan oturum açma etkinlik günlükleri, eski kimlik doğrulama kullanarak müşterilerin can artık bkz: kullanıcılar. Müşteriler, oturum açma MS Graph API'sini kullanarak bu bilgilere erişmek mümkün olacaktır veya oturum açma kullanabileceğiniz Azure AD'ye portalda etkinlik günlükleri **istemci uygulaması** üzerinde eski kimlik doğrulamaları filtrelemek için denetimi. Daha fazla ayrıntı için belgeleri gözden geçirin.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD uygulama galerisinde yeni Federasyon Uygulamaları kullanılabilir - Temmuz 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
Temmuz 2018'de Federasyon ile 16 Bu yeni uygulamalar için uygulama Galerisi desteği ekledik:

[Yenilik Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [belirli yönetici SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC hazırlama [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [yayını-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial) , PowerSchool sınıf, birleşik [Eli ekleme](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Uzaktan Destek](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial) , [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow bağlayıcı](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [temel becerileri](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Yeni kullanıcı sağlama SaaS uygulama tümleştirmeleri - Temmuz 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama sağlama  
**Ürün özelliği:** 3. taraf tümleştirme
 
Azure AD, oluşturma, Bakım ve Dropbox, Salesforce, ServiceNow ve diğer SaaS uygulamalarına kullanıcı kimliklerini kaldırılmasını otomatik hale getirmenizi sağlar. Kullanıcı Azure AD uygulama galerisinde şu uygulamalar için destek sağlama Temmuz 2018 tarihinden itibaren ekledik:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Azure AD Galerisi'nde kullanıcı sağlamayı destekleyen tüm uygulamalar listesi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Eşitleme için Connect Health - Yalnız bırakılmış ve çoğaltışmış öznitelik eşitleme hatalarını kolaylıkla düzeltme

**Türü:** yeni özellik  
**Hizmet kategorisi:** AD Bağlan  
**Ürün özelliği:** izleme ve Raporlama
 
Azure AD Connect Health kendi kendine düzeltme vurgulayın ve eşitleme hatalarını düzeltmeyi yardımcı olması için ortaya çıkarır. Bu özellik, yinelenen öznitelik Eşitleme hataları giderir ve yalnız bırakılmış nesneler, Azure AD'den giderir. Bu tanılama aşağıdaki faydaları sağlar:

- Belirli düzeltmeler sağlama yinelenen öznitelik Eşitleme hataları daraltır

- Azure AD senaryoları, tek bir adımda hatalarını çözümlemek için bir düzeltme uygular

- Yükseltme ya da yapılandırma'yı açmak ve bu özelliği kullanmak için gereklidir

Daha fazla bilgi için [Tanıla ve yinelenen öznitelik eşitleme hatalarını Düzelt](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Azure AD ve MSA oturum açma deneyimleri için görsel güncelleştirmeler

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Azure AD  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Microsoft online services oturum açma deneyimi için kullanıcı Arabirimi gibi Office 365 ve Azure için güncelleştirdik. Bu değişiklik ekranları, daha az anlaşılamayacak ve daha basit hale getirir. Bu değişiklik hakkında daha fazla bilgi için bkz. [yaklaşan geliştirmeler Azure AD oturum açma deneyimini](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) blogu.

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Yeni Azure AD Connect sürümü - Temmuz 2018

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** uygulama sağlama  
**Ürün özelliği:** kimlik yaşam döngüsü yönetimi

Azure AD Connect'in en son sürümünü içerir: 

- Hata düzeltmeleri ve desteklenebilirliği güncelleştirmeleri 

- Genel kullanılabilirlik Ping federasyona tümleştirme

- En son SQL 2012 istemci güncelleştirmeleri 

Bu güncelleştirme hakkında daha fazla bilgi için bkz. [Azure AD Connect: sürüm yayımlama geçmişi](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Son Kullanıcı Kullanıcı arabirimi kullanım koşullarına göre güncelleştirmeler

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** idare

Kabul dize TOU son kullanıcı arabiriminde güncelleştiriyoruz.

**Geçerli metin.** [Kiracıadı] kaynaklara erişmek için kullanım koşullarını kabul etmeniz gerekir.<br>**Yeni metin.** [Kiracıadı] kaynağa erişebilmeniz için kullanım koşullarını okuyun gerekir.

**Geçerli metin:** tüm yukarıdaki kullanım koşullarını kabul etmiş olursunuz kabul etmek seçme.<br>**Yeni metin:** Lütfen okudum ve anladım kullanım koşullarını onaylamak üzere kabul Et'E tıklayın.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Doğrudan kimlik doğrulama eski protokolleri ve uygulamaları destekliyor

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Geçişli kimlik doğrulaması artık eski protokolleri ve uygulamaları destekler. Aşağıdaki sınırlamalar artık tam olarak desteklenir:

- Modern kimlik doğrulaması gerektirmeden eski Office istemci uygulamaları, Office 2010 ve Office 2013, kullanıcı oturum açma işlemleri.

- Takvim Paylaşımı ve ücretsiz/meşgul bilgileri yalnızca karma ortamlarda Office 2010 Exchange erişim.

- Kullanıcı oturum açma işlemleri Skype için modern kimlik doğrulaması gerektirmeden iş istemci uygulamaları için.

- PowerShell sürüm 1.0 için kullanıcı oturum açma işlemleri.

- Apple cihaz kaydı iOS Kurulum Yardımcısı'nı kullanarak programı (Apple DEP). 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Self servis parola sıfırlama ve Multi-Factor Authentication için yakınsanmış güvenlik bilgileri yönetimi

**Türü:** yeni özellik  
**Hizmet kategorisi:** SSPR  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Bu yeni özellik için güvenlik bilgilerini (örneğin, telefon numaranız, e-posta adresi, mobil uygulama ve benzeri) Self Servis parola sıfırlama (SSPR) ve multi-Factor Authentication (MFA) tek bir deneyimle yönetme olanağı sunar. Kullanıcıların SSPR ve MFA için iki farklı deneyimlerinde aynı güvenlik bilgilerini kaydetmek artık gerekir. Bu yeni deneyim, SSPR'ı veya MFA sahip kullanıcılar için de geçerlidir.

Bir kuruluş, MFA veya SSPR kayıt zorunlu değil, kullanıcılar aracılığıyla güvenlik bilgileri kaydedebilirsiniz **uygulamalarım** portalı. Burada, kullanıcılar için mfa'yı veya SSPR etkin herhangi bir yöntem kaydedebilirsiniz. 

Bu bir katılım genel önizlemesidir. Yöneticilerin yeni deneyimi (istenirse) seçili bir grup kullanıcı ya da bir kiracıdaki tüm kullanıcılar için etkinleştirebilirsiniz.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Parolanızı sıfırlarken kimliğinizi doğrulamak için Microsoft Authenticator uygulamasını kullanın

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** SSPR  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Bu özellik, bir bildirim veya koddan Microsoft Authenticator'ı (ya da herhangi bir kimlik doğrulayıcı uygulama) kullanarak bir parolayı sıfırlarken kimliklerini doğrulama yönetici olmayanlar olanak sağlar. Yöneticileri açtıktan sonra yöntemi bu Self Servis parola sıfırlama, bir mobil uygulama aka.ms/mfasetup veya aka.ms/setupsecurityinfo aracılığıyla kaydolan kullanıcılar kendi mobil uygulama doğrulama yöntemi olarak, parola sıfırlama sırasında kullanabilirsiniz.

Mobil uygulama bildirimi, parolanızı sıfırlamak için iki yöntem gerektiren bir ilke bir parçası olarak yalnızca açılabilir.

---

## <a name="june-2018"></a>Haziran 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Değişiklik bildirimi: Azure AD etkinlik günlüklerini API'sini kullanarak uygulamalar için Temsilcili yetkilendirme akışı güvenlik düzeltme

**Türü:** değişiklik planı  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Bizim daha güçlü güvenlik zorlama nedeniyle, şu izinleri erişmek için Temsilcili yetkilendirme akışı kullanan uygulamalar için değişiklik vardı [Azure AD etkinlik günlüklerini API'lerini](https://aka.ms/aadreportsapi). Bu değişiklik tarafından ortaya çıkar **26 Haziran 2018'e**.

Tüm uygulamalarınızın Azure AD etkinlik günlüğü API'lerini kullanırsanız, değişiklik yapıldıktan sonra app kesmeyecektir emin olmak için aşağıdaki adımları izleyin.

**Uygulama izinleri güncelleştirmek için**

1. Azure portalında oturum, select **Azure Active Directory**ve ardından **uygulama kayıtları**.
2. Azure AD etkinlik günlüklerini API'si, select kullanan uygulamanızı seçin **ayarları**seçin **gerekli izinler**ve ardından **Windows Azure Active Directory** API.
3. İçinde **temsilci izinleri** alanının **erişimi etkinleştirme** dikey penceresinde yanındaki kutuyu işaretleyin **okuma directory** veri tıklayın ve ardından **Kaydet**.
4. Seçin **izinleri verin**ve ardından **Evet**.
    
    >[!Note]
    >Uygulama izinlerini vermek için genel yönetici olması gerekir.

Daha fazla bilgi için [izinleri verin](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) alan Azure AD raporlama API'si makaleye erişmek için Önkoşullar.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>PCI DSS uyumluluğu için Azure AD Hizmetleri bağlanmak için TLS ayarlarını yapılandırma

**Türü:** yeni özellik  
**Hizmet kategorisi:** yok  
**Ürün özelliği:** platformu

Aktarım Katmanı Güvenliği (TLS) arasında iki iletişim kuran uygulamaları gizlilik ve veri bütünlüğü sağlar ve Günümüzde kullanılan en yaygın olarak dağıtılan güvenlik protokolü bir protokoldür.

[PCI güvenlik standartları Council](https://www.pcisecuritystandards.org/) TLS Güvenli Yuva Katmanı (SSL) ve önceki sürümlerinde uyumluluk başlangıç ile yeni ve daha güvenli uygulama protokolleri etkinleştirme ile değiştiriliyor devre dışı bırakılmalıdır olduğunu belirledi **30 Haziran 2018**. Bu değişiklik, Azure AD hizmetlerine bağlanmak ve PCI DSS uyumluluğu gerektir, TLS 1.0 mamtelemetrydisabled anlamına gelir. TLS birden çok sürümü kullanılabilir ancak TLS 1.2 en son sürümü Azure Active Directory Hizmetleri için kullanılabilir. Hem istemci/sunucu hem de tarayıcı/sunucu birleşimleri için doğrudan TLS 1.2 taşınması önerilir.

Güncel olmayan tarayıcılar, TLS 1.2 gibi yeni TLS sürümlerini desteklemiyor olabilir. TLS hangi sürümlerinin tarayıcınız tarafından desteklendiğini görmek için Git [Qualys SSL Labs](https://www.ssllabs.com/) tıklayın ve site **tarayıcınızı Test**. Öneririz web tarayıcınızın en son sürümüne yükseltin ve tercihen yalnızca TLS 1.2 etkinleştirin.

**TLS 1.2, tarayıcı tarafından etkinleştirmek için**

- **Microsoft Edge ve Internet Explorer'ın (hem de Internet Explorer'ı kullanarak ayarlanır)**

    1. Internet Explorer'ı açın, **Araçları** > **Internet Seçenekleri** > **Gelişmiş**.
    2. İçinde **güvenlik** alanında **TLS 1.2 kullanmak**ve ardından **Tamam**.
    3. Tüm tarayıcı pencerelerini kapatın ve Internet Explorer'ı yeniden başlatın. 

- **Google Chrome**

    1. Açık Google Chrome, türü *chrome://settings/* tuşuna basın ve adres çubuğuna **Enter**.
    2. Genişletin **Gelişmiş** seçenekleri, Git **sistem** alan ve seçim **proxy ayarları**.
    3. İçinde **Internet Özellikleri** kutusunda **Gelişmiş** sekmesine gidin **güvenlik** alanında **TLS 1.2 kullanmak**ve ardından seçin **Tamam**.
    4. Tüm tarayıcı pencerelerini kapatın ve Google Chrome'u yeniden başlatın.

- **Mozilla Firefox**

    1. Açık Firefox, türü *hakkında: yapılandırma* ENTER tuşuna basın ve adres çubuğuna **Enter**.
    2. Arama terimi için *TLS*ve ardından **security.tls.version.max** girişi.
    3. Değerine **3** TLS 1.2 sürümü için kullanır ve ardından için tarayıcıyı zorlamak için **Tamam**.

        >[!NOTE]
        >Firefox sürümü 60,0 TLS 1.3 destekler böylece de security.tls.version.max değeri ayarlayabilirsiniz **4**.

    4. Tüm tarayıcı pencerelerini kapatın ve Mozilla Firefox yeniden başlatın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Yeni Federasyon uygulamaları kullanılabilir Azure AD uygulama galerisinde - Haziran 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
Haziran 2018'de Federasyon ile 15 bu yeni uygulamalar için uygulama Galerisi desteği ekledik:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [müzik sonlandırma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 belirteci etkin LOB uygulaması](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [ Uç nokta yedekleme](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh ağları](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho bir](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [ SharePoint şirket içi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [CX Suite Öngörüyor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial). Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD parola koruması genel önizlemeye sunuldu

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik koruması  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Ortamınızdaki parolalar kolayca tahmin gidermeye yardımcı olması için Azure AD parola koruması kullanın. Bu parolalar ortadan güvenliğinin aşılması riskine karşı bir saldırı parola ilaç türü riskini azaltmak için yardımcı olur.

Özellikle, Azure AD parola koruması, yardımcı olur:

- Hem Azure AD'de kuruluşunuzun hesapları korumak ve Windows Server Active Directory (AD). 
- Kullanıcılarınızın parolaları listesi en sık kullanılan parolaların 500'den fazla ve bu parolaları 1 milyon karakter değiştirme çözümlenmeyebileceği kullanarak durdurur.
- Her iki Azure AD için Azure AD parola koruması, Azure AD portalında tek bir konumdan yönetmek ve şirket içi Windows Server AD.

Azure AD parola koruması hakkında daha fazla bilgi için bkz. [yanlış parolalar, kuruluşunuzdaki ortadan](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Kullanım koşulları oluşturma sırasında yeni "tüm konuklar" koşullu erişim ilkesi şablonu oluşturuldu

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** idare

Kullanım koşullarınızın oluşturulması sırasında, "tüm konuklar" ve "tüm uygulamalar" için de yeni bir koşullu erişim ilkesi şablonu oluşturulur. Bu yeni bir ilke şablonu oluşturma ve zorlama işlemi konuklar için hızlandırma, yeni oluşturulan ToU geçerlidir.

Daha fazla bilgi için [kullanım özelliği, Azure Active Directory koşulları](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Kullanım koşulları oluşturma sırasında yeni "özel" koşullu erişim ilkesi şablonu oluşturuldu

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** idare

Kullanım koşullarınızın oluşturulması sırasında yeni bir "özel" koşullu erişim ilkesi şablonu da oluşturulur. Bu yeni ilke şablonu, ToU 'yi oluşturmanızı ve ardından doğrudan portalda gezinmenize gerek kalmadan koşullu erişim ilkesi oluşturma dikey penceresine gitmenizi sağlar.

Daha fazla bilgi için [kullanım özelliği, Azure Active Directory koşulları](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Azure multi-Factor Authentication'ı dağıtma hakkında daha fazla yeni ve kapsamlı Kılavuzu

**Türü:** yeni özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** kimlik güvenliği ve koruması
 
Yeni bir Azure multi-Factor Authentication (MFA) kuruluşunuzda dağıtma hakkında adım adım kılavuz yayımladık.

MFA Dağıtım Kılavuzu'nu görüntülemek için Git [kimlik dağıtım kılavuzları](https://aka.ms/DeploymentPlans) github deposu. Dağıtım kılavuzları hakkında geri bildirim sağlamak için kullanın [dağıtım planı geri bildirim formu](https://aka.ms/deploymentplanfeedback). Dağıtım kılavuzları hakkında sorularınız varsa, adresinden bize başvurun [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD rolleri genel Önizleme aşamasındadır uygulama yönetimi temsilcisi

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** erişim denetimi

Yöneticiler, artık genel yönetici rolü atama olmadan uygulama yönetim görevlerini devredebilirsiniz. Yeni rol ve özellikleri şunlardır:

- **Yeni bir standart Azure AD yönetim rolleri:**

    - **Uygulama Yöneticisi.** Kayıt, SSO ayarlarını, uygulama atamalarını ve lisanslama, uygulama proxy'si ayarları ve onay dahil tüm uygulamalar'ın tüm yönlerini yönetme olanağı veren (Azure AD kaynaklarını hariç).

    - **Bulut uygulaması Yöneticisi.** Şirket içi erişim sağlamaz çünkü uygulama Yöneticisi becerileri, uygulama ara sunucusu hariç tüm verir.

    - **Uygulama geliştiricisi.** Uygulama kayıtları oluşturma yeteneği verir bile **uygulamaları kaydetmesine izin verin** seçeneği devre dışı bırakılmış durumdadır.

- **Sahiplik (uygulama başına kayıt ve kurumsal uygulama, Grup sahipliği işleme benzer ayarlayın:**
 
    - **Uygulama kaydı sahibi.** Sahip olunan bir uygulama kaydı, ek sahipleri ekleme ve uygulama bildirimi dahil olmak üzere tüm yönlerini yönetmek için yeteneği verir.

    - **Kurumsal uygulama sahibi.** Sahip olunan Kurumsal uygulamaları, SSO ayarlarını, uygulama atamalarını ve onay dahil olmak üzere birçok yönden yönetme olanağı veren (Azure AD kaynaklarını hariç).

Genel önizleme hakkında daha fazla bilgi için bkz: [Azure AD rolleri, genel Önizleme aşamasında olan uygulama yönetimi temsilcisi!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Rolleri ve izinleri hakkında daha fazla bilgi için bkz: [Azure Active Directory'de yönetici rolleri atama](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Mayıs 2018

### <a name="expressroute-support-changes"></a>ExpressRoute destek değişiklikleri

**Türü:** değişiklik planı  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** platformu  

Doğrudan Internet gidip ExpressRoute veya diğer özel VPN tünelleri gerek kalmadan iyi çalışacak şekilde tasarlanan Azure Active Directory (Azure AD) gibi bir hizmet olarak yazılım. Bu nedenle, üzerinde **1 Ağustos 2018**, size Azure AD Hizmetleri kullanarak Azure ortak eşleme ve Microsoft eşlemesi içinde Azure topluluklar için ExpressRoute destekleme durdurur. Bu değişiklikten etkilenen tüm hizmetler Azure fark edebilirsiniz AD trafik ExpressRoute Internet'e giderek kaydırma.

Ayrıca var. biliyoruz ki, destek değiştirmiyorsanız olan gerek duyduğunuz senaryolara hala durumlarda kullanırken devreler adanmış bir dizi, kimlik doğrulama trafiği için. Bu nedenle, Azure AD Kiracı başına ExpressRoute ve Hizmetleri zaten "Diğer Office 365 Çevrimiçi Hizmetleri" toplulukla Microsoft eşlemesi üzerinde kullanarak IP aralığı kısıtlamalarını desteklemeye devam edecektir. Hizmetlerinizi etkilenir, ancak ExpressRoute gerektirir, aşağıdakileri yapmanız gerekir:

- **Azure ortak eşleme üzerinde kullanıyorsanız.** Microsoft eşlemesi için taşıma ve kaydolun **diğer Office 365 Çevrimiçi Hizmetleri (12076:5100)** topluluğu. Azure genel Microsoft eşlemesi için eşlemeden taşıma hakkında daha fazla bilgi için bkz. [Microsoft eşlemesi için genel eşleme taşıma](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) makalesi.

- **Microsoft eşlemesi üzerinde kullanıyorsanız.** Kaydolun **diğer Office 365 çevrimiçi hizmet (12076:5100)** topluluğu. Yönlendirme gereksinimleri hakkında daha fazla bilgi için bkz. [BGP toplulukları bölümü için destek](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) ExpressRoute yönlendirme gereksinimleri makalesi.

Adanmış devreler kullanmaya devam etmeniz gerekiyorsa Microsoft Account ekibinize kullanılacak yetkilendirme edinme hakkında konuşmak gerekecektir **diğer Office 365 çevrimiçi hizmet (12076:5100)** topluluğu. MS Office yönetilen gözden geçirme panonun bu devreler gerekir ve başvurularınızı teknik sonuçlarını anladığınızdan emin olup olmadığını doğrular. Yetkisiz abonelikleri Office 365 için rota filtreleri oluşturulmaya çalışılırken bir hata iletisi alırsınız. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API'leri için TOU yönetim senaryoları için

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** geliştirici deneyimi
 
Azure AD kullanım koşulları 'nın yönetim işlemi için Microsoft Graph API 'Leri ekledik. Kullanım koşulları nesnesi oluşturabilir, güncelleştirebilir, silebilirsiniz.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Bir Azure AD B2C kimlik sağlayıcısı olarak Azure AD çok kiracılı uç noktası ekleme

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C
 
Özel ilkeleri kullanarak, Azure AD genel uç noktası kimlik sağlayıcısı olarak Azure AD B2C artık ekleyebilirsiniz. Bu, tek bir uygulamalarınızda oturum açtığınız tüm Azure AD kullanıcıları için giriş noktası olmasını sağlar. Daha fazla bilgi için [Azure Active Directory B2C: özel ilkeleri kullanarak çok kiracılı Azure AD kimlik sağlayıcısı için oturum açmasına imkan tanıyın](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Her yerden uygulamalara erişmek için iç URL'lerini kullanan My Apps oturum açma Uzantımızı ve Azure AD uygulama proxy'si ile

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulamalarım  
**Ürün özelliği:** SSO
 
Kullanıcılar artık uygulamalar üzerinden şirket ağınıza olduğunda bile dışında İç URL My Apps güvenli oturum açma uzantısı için Azure AD'yi kullanarak erişebilirsiniz. Bu, Azure AD uygulama ara sunucusu, ayrıca erişim paneli tarayıcı uzantısının yüklü olan herhangi bir tarayıcı üzerinde kullanarak yayımladığınız herhangi bir uygulama ile çalışır. URL yeniden yönlendirme işlevi, uzantısı'na bir kullanıcı oturumu sonra otomatik olarak etkinleştirilir. Uzantı, ücretsiz olarak kullanılabilir [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367), ve [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Avrupa müşterilerin Avrupa'daki veri

**Türü:** yeni özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** GoLocal

Avrupa'da bulunan müşteriler verilerine Avrupa'da kalmasını gerektirir ve toplantı gizliliği ve Avrupa yasaları Avrupa veri merkezleri dışına çoğaltılmamış. Bu [makale](https://go.microsoft.com/fwlink/?linkid=872328) hangi kimlik bilgilerini belirli ayrıntıları Avrupa içinde depolanır ve ayrıca depolanan dış Avrupa veri merkezlerinde olacak bilgileri hakkında ayrıntılı bilgi sağlar. sağlar. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>SaaS uygulama tümleştirmeleri - Mayıs 2018 sağlama yeni kullanıcı

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama sağlama  
**Ürün özelliği:** 3. taraf tümleştirme
 
Azure AD, oluşturma, Bakım ve Dropbox, Salesforce, ServiceNow ve diğer SaaS uygulamalarına kullanıcı kimliklerini kaldırılmasını otomatik hale getirmenizi sağlar. Mayıs 2018 için kullanıcı sağlama desteği aşağıdaki uygulamalar Azure AD uygulama galerisinde ekledik:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Temel taşıdır OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Azure AD Galerisi'nde kullanıcı sağlamayı destekleyen tüm uygulamalar listesi için bkz. [ https://aka.ms/appstutorial ](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Uygulama erişim artık yinelenen incelemeleri sağlayan ve grupları Azure AD erişim gözden geçirmeleri

**Türü:** yeni özellik  
**Hizmet kategorisi:** erişim gözden geçirmeleri  
**Ürün özelliği:** idare
 
Erişim gözden geçirmesi grupları ve uygulamaları şu anda genel kullanımdadır Azure AD Premium P2 bir parçası olarak.  Yöneticiler, erişim gözden geçirmeleri grup üyeliklerini ve uygulama atamaları aylık veya üç aylık gibi düzenli aralıklarla otomatik olarak yinelenmesini yapılandırmanız mümkün olacaktır.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>MS Graph ile Azure AD etkinlik günlüklerini (oturum açma işlemleri ve Denetim) artık kullanılabilir

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama
 
Oturum açma işlemleri ve Denetim günlükleri'nı içeren azure AD etkinlik günlüklerini MS Graph üzerinden kullanıma sunulmuştur. Biz bu günlüklerine erişmek için MS Graph ile iki bitiş noktası kullanıma sunması. Kullanıma sunduğumuz [belgeleri](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) programlı erişim için Azure AD raporlama kullanmaya başlamak için API'leri. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>B2B kullanım geliştirmeleri deneyimi ve bir kuruluştan ayrılmak

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2B  
**Ürün özelliği:** B2B/B2C

**Kullanım zaman içinde yalnızca:** sonra bir kaynak B2B API'sini kullanarak bir konuk kullanıcıyla paylaştığınız – özel davet e-posta gönderir gerekmez. Çoğu durumda, Konuk kullanıcı kaynaklara erişebilir ve zamanında kullanım deneyimi üzerinden gerçekleştirilmez. Daha fazla etkisi nedeniyle eksik e-postaları. "Gönderdiğiniz sistem, kullanım bağlantıya tıklayın muydunuz?", Konuk kullanıcılar artık sorma. Bunun anlamı sonra SPO davet manager'ın kullandığı – cloudy ekleri – iç ve dış – faydalarınız, herhangi bir durumdaki tüm kullanıcılar için kurallı aynı URL'ye sahip olabilir.

**Modern bir kullanım deneyimi:** artık giriş sayfası ekran kullanım bölün. Kullanıcıların modern görürsünüz üçüncü taraf uygulamaları için yaptıkları gibi davet eden kuruluşunuzun gizlilik bildirimini deneyimle onayı.

**Konuk kullanıcılar, kuruluş bırakabilirsiniz:** bir kuruluş ile bir kullanıcının ilişki bittikten sonra bunlar kuruluştan ayrılmadan Self Servis. Artık ", daha fazla yükseltmeyi destek biletlerini kaldırılacak" davet edildikleri kuruluştan 's yöneticinin çağrılıyor.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Yeni Federasyon uygulamaları kullanılabilir Azure AD uygulama galerisinde - Mayıs 2018

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme
 
Mayıs 2018'de uygulama galerimizden Federasyon ile 18 Bu yeni uygulamaları desteği ekledik:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty yöneten, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe büyüleyecektir asal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [çevrimiçi montaj](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [yay yayımlama - SSO ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial).

Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory için yeni bir adım adım dağıtım kılavuzları

**Türü:** yeni özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** dizini
 
Self servis parola sıfırlama (SSPR), çoklu oturum açma (SSO), koşullu erişim (CA), uygulama proxy, Kullanıcı sağlama, Active Directory Federasyon Hizmetleri (AD FS) (ADFS) dahil olmak üzere Azure Active Directory (Azure AD) dağıtma hakkında yeni, adım adım yönergeler Geçişli kimlik doğrulaması (PTA) ve ADFS 'den Parola karması eşitleme (PHS).

Dağıtım kılavuzlarını görüntülemek için Git [kimlik dağıtım kılavuzları](https://aka.ms/DeploymentPlans) github deposu. Dağıtım kılavuzları hakkında geri bildirim sağlamak için kullanın [dağıtım planı geri bildirim formu](https://aka.ms/deploymentplanfeedback). Dağıtım kılavuzları hakkında sorularınız varsa, adresinden bize başvurun [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Kurumsal uygulamaları arayın: daha fazla uygulama yükleme

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO
 
Hizmet sorumluları / uygulamalarınızı bulma konusunda sorun mu yaşıyorsunuz? Tüm uygulamalar listesini, Kurumsal uygulamalarınızda daha fazla uygulama yükleme olanağı ekledik. Varsayılan olarak 20 uygulamaları göstereceğiz. Artık tıklatabilir, **daha fazla Yükle** ek uygulamaları görüntülemek için. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Genel Önizleme aşamasında, PingFederate ile tümleştirme AADConnect sürümünü içeren Mayıs önemli güvenlik güncelleştirmeleri, birçok hata düzeltmeleri ve yeni harika yeni sorun giderme araçları. 

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** AD Bağlan  
**Ürün özelliği:** kimlik yaşam döngüsü yönetimi
 
Genel Önizleme aşamasında, PingFederate ile tümleştirme AADConnect sürümünü içeren Mayıs önemli güvenlik güncelleştirmeleri, birçok hata düzeltmeleri ve yeni harika yeni sorun giderme araçları. Sürüm notlarında bulabilirsiniz [burada](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD erişim gözden geçirmeleri: otomatik olarak Uygula

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** erişim gözden geçirmeleri  
**Ürün özelliği:** idare

Erişim gözden geçirmeleri, grupları ve uygulamaları sunulmuştur Azure AD Premium P2 bir parçası olarak. Bir yönetici, erişim gözden geçirmesi tamamlandığında otomatik olarak gözden geçirenin değişiklikler, Grup veya uygulama uygulamak üzere yapılandırabilirsiniz. Yönetici kullanıcının sürekli erişim gözden geçirenler olmadı yanıt, erişimini kaldırmak, erişim tutun veya sistem önerileri ele ne de belirtebilirsiniz. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Kimlik belirteçlerini, artık yeni uygulamalar için sorgu response_mode kullanarak döndürülebilir. 

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Ya da 25 Nisan 2018'den sonra oluşturulan uygulamaların artık oluşturabileceksiniz istemek bir **id_token** kullanarak **sorgu** response_mode.  Yardımcı uygulama saldırı yüzeyinizi azaltmak ve bu Azure AD satır içi OIDC belirtimleri ile getirir.  25 Nisan 2018 tarihinden önce oluşturulan uygulamaların, kullanımından değil engellenir **sorgu** ile bir response_type response_mode **id_token**.  Döndürülen bir id_token, AAD'den istenirken hata **AADSTS70007: 'query' değil 'response_mode' ın desteklenen bir değer bir belirteç isterken**.

**Parça** ve **form_post** response_modes - yeni uygulama nesneleri oluşturma (örneğin, uygulama proxy'si kullanım için) emin olun, bu response_modes birini kullanmak bunlar oluşturmadan önce çalışmaya devam bir Yeni uygulama.  

---
 
## <a name="april-2018"></a>Nisan 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C erişim belirteci olan genel kullanım

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C 

Azure AD B2C ile güvenliği sağlanan Web API'leri artık erişebilirsiniz erişim belirteçleri kullanarak. Özellik genel Önizleme için büyüyecek taşınıyor Azure AD B2C uygulamaları ve web API'leri yapılandırmak için kullanıcı Arabirimi deneyimi İyileştirildi ve başka küçük geliştirmeler yapıldı.
 
Daha fazla bilgi için [Azure AD B2C: istenen erişim belirteçleri](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>SAML tabanlı uygulamalar için çoklu oturum açma yapılandırması test

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO

SAML tabanlı SSO uygulamaları yapılandırırken, yapılandırma sayfasında tümleştirme test edebilirsiniz. Oturum açma sırasında bir hatayla karşılaşırsanız, hata test deneyiminde sağlayabilir ve Azure AD ile çözüm adımları belirli sorunu çözmek için sağlar.

Daha fazla bilgi için bkz.

- [Azure Active Directory uygulama galerisinde bulunmayan uygulamalar için çoklu oturum açmayı yapılandırma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Azure Active Directory'de uygulamalar için SAML tabanlı çoklu oturum açma hata ayıklama](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD kullanım koşulları artık Kullanıcı başına raporlamaya sahiptir

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** uyumluluk
 
Yöneticiler artık belirli bir ToU'ı seçin ve ToU ve hangi, tarih yer aldığı onay tüm kullanıcılar görür.

Daha fazla bilgi için [Azure AD kullanım koşulları özelliği](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health'i: AD FS extranet kilitleme koruması riskli IP 

**Türü:** yeni özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** izleme ve Raporlama

Bir saatlik veya günlük olarak hatalı U/P oturum açma eşiğini aşan IP algılama özelliğine destekler adresleri artık connect Health. Bu özellik tarafından sağlanan özellikler şunlardır:

- IP adresi ve bir saatlik/günlük olarak özelleştirilebilir eşik ile oluşturulan başarısız oturum açma sayısını gösteren kapsamlı bir rapor.
- E-posta tabanlı uyarılar belirli bir IP adresi, gösteren saatlik/günlük olarak hatalı U/P oturum açma eşiğini aştı.
- Verilerin ayrıntılı bir analiz yapmak için bir yükleme seçeneği

Daha fazla bilgi için [riskli IP raporu](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Meta veri dosyası veya URL ile kolay uygulama yapılandırması

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO

Kurumsal uygulamalar sayfasında Yöneticiler SAML tabanlı oturum açma AAD galeri ve galeri dışı bir uygulama için yapılandırmak için bir SAML meta veri dosyasını karşıya yükleyebilirsiniz.

Ayrıca, Azure AD uygulama Federasyon meta veri URL'si ile hedeflenen uygulamaya SSO yapılandırmak için kullanabilirsiniz.

Daha fazla bilgi için [Azure Active Directory Uygulama galerisinde bulunmayan uygulamalar için çoklu oturum açma yapılandırma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD kullanım koşulları artık kullanıma sunuldu

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** uyumluluk
 

Azure AD kullanım koşulları, genel önizlemeden genel kullanıma açık olarak taşınmıştır.

Daha fazla bilgi için [Azure AD kullanım koşulları özelliği](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>İzin verme veya davetleri B2B kullanıcıları belirli kuruluşlardan engelleme

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2B  
**Ürün özelliği:** B2B/B2C
 

Paylaşın ve Azure AD B2B işbirliği birlikte çalışmak istediğiniz hangi iş ortağı kuruluşlar artık belirtebilirsiniz. Bunu yapmak için özel bir listesini oluşturmak seçebileceğiniz izin ver veya Reddet etki alanları. Bu özellikleri kullanılarak bir etki alanı engellendiğinde, çalışanlar artık bu etki alanında davetleri kişilere gönderebilirsiniz.

Bu, onaylanan kullanıcılar için sorunsuz bir deneyim etkinleştirirken, kaynaklarınıza erişimi denetlemenize yardımcı olur.

Bu B2B Işbirliği özelliği tüm Azure Active Directory müşterileri tarafından kullanılabilir ve dış iş kullanıcılarının ne zaman ve nasıl imzalandığını daha ayrıntılı bir şekilde denetlemek için koşullu erişim ve kimlik koruması gibi Azure AD Premium özelliklerle birlikte kullanılabilir. ve ' de erişim elde edin.

Daha fazla bilgi için [B2B kullanıcıları için izin verilenler veya Engellenenler davetleri belirli kuruluşlardan](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Yeni Federasyon uygulamaları kullanılabilir Azure AD uygulama galerisinde

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme

Nisan 2018'de uygulama galerimizden Federasyon ile 13 bu yeni uygulamaları desteği ekledik:

Ölçüt HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [gizli sunucusu (şirket içi)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dinamik sinyal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [Kuruluş Şeması Artık](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performans İzleyicisi'ni](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco bulut](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), raf, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial).

Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Şirket içi uygulamalarınıza (genel Önizleme) Azure AD erişim verme B2B kullanıcıları

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2B  
**Ürün özelliği:** B2B/B2C

Azure ad iş ortağı kuruluşlardan Konuk kullanıcıları davet etmek için Azure Active Directory (Azure AD) B2B işbirliği özelliklerini kullanan bir kuruluş, artık bu B2B kullanıcıları şirket içi uygulamalara erişim sağlayabilir. Bu şirket içi uygulamaları, Kerberos Kısıtlı temsilci (KCD) ile SAML tabanlı kimlik doğrulaması veya tümleşik Windows kimlik doğrulaması (IWA) kullanabilirsiniz.

Daha fazla bilgi için [verme B2B kullanıcıları Azure AD'de, şirket içi uygulamalarınıza erişim](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Azure Market'ten SSO tümleştirme öğreticilerine erişin

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** diğer  
**Ürün özelliği:** 3. taraf tümleştirme

Listelenen bir uygulama bildirimi [Azure Market](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) destekleyen SAML tabanlı çoklu tıklayarak oturum, **şimdi edinin** bu uygulamayla ilişkili tümleştirme öğreticisiyle sağlar. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Azure AD SaaS uygulamaları için otomatik kullanıcı hazırlama, daha hızlı performans

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** uygulama sağlama  
**Ürün özelliği:** 3. taraf tümleştirme
 
Daha önce Azure AD kiracıları 100. 000'den birleşik kullanıcılar içeriyorsa sağlama bağlayıcılar (örneğin Salesforce, ServiceNow ve kutusu) SaaS uygulamaları için Azure Active Directory kullanıcı kullanan müşteriler yavaş performans karşılaşması ve Gruplar ve hangi kullanıcıların sağlanan belirlemek için kullanıcı ve Grup atamaları kullanıyordu.

2 Nisan 2018'de, Azure Active Directory ve hedef SaaS uygulamaları arasında ilk eşitlemeler gerçekleştirmek için gereken süreyi büyük ölçüde azaltmak Azure AD sağlama hizmeti için önemli performans geliştirmeleri dağıtıldı.

Sonuç olarak, birçok müşteri, ilk eşitleme geçen gün sayısı veya hiçbir zaman tamamlandı, artık yalnızca birkaç dakika veya saat içinde tamamlıyorsanız uygulamalara vardı.

Daha fazla bilgi için [sağlama sırasında ne olur?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Self Servis parola sıfırlama için hibrit Azure AD'ye Windows 10 kilit ekranından makineler katılmış

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Self Servis parola sıfırlama  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Hibrit Azure AD'ye katılmış olan makineler için destek eklemek için Windows 10 SSPR özelliğini güncelleştirdik. Bu özellik kullanılabilir Windows 10 RS4 bir Windows 10 makinenin kilit ekranında parolalarını sıfırlamalarına olanak tanır. Etkin ve Self Servis parola sıfırlama için kaydolan kullanıcılar, bu özelliği kullanabilir.

Daha fazla bilgi için [Azure AD parola sıfırlama oturum açma ekranından](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Mart 2018
 
### <a name="certificate-expire-notification"></a>Sertifika sona bildirimi

**Türü:** düzeltildi  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO
 
Galeri dışı uygulama dolmak üzere olduğu ya da Azure AD galeri için bir sertifika olduğunda bir bildirim gönderir. 

Bazı kullanıcılar, SAML tabanlı çoklu oturum açma için yapılandırılmış kurumsal uygulamalar için bildirim almadı. Bu sorun çözüldü. Azure AD 7, 30 ve 60 gün içinde sona erecek sertifikaları için bildirim gönderir. Bu olay denetim günlüklerinde görebilirsiniz. 

Daha fazla bilgi için bkz.

- [Federasyon çoklu oturum açma için Azure Active Directory'de sertifikaları yönetme](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Azure Active Directory portalındaki denetim etkinlik raporları](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C, twitter ve GitHub kimlik sağlayıcıları

**Türü:** yeni özellik  
**Hizmet kategorisi:** B2C - tüketici Kimlik Yönetimi  
**Ürün özelliği:** B2B/B2C
 
Artık, bir Azure AD B2C kimlik sağlayıcısı olarak Twitter veya GitHub ekleyebilirsiniz. Twitter büyüyecek için genel preview'dan taşınıyor GitHub genel önizlemede yayınlanır.

Daha fazla bilgi için [Azure AD B2B işbirliği nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>İOS ve Android için Azure AD uygulama tabanlı koşullu erişim ile Intune Managed Browser kullanarak tarayıcı erişimini kısıtlama

**Türü:** yeni özellik  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması
 
**Artık genel önizlemede!**

**Intune Managed Browser'da SSO:** çalışanlarınızın çoklu oturum açma (Microsoft Outlook gibi) için yerel istemcileri ve Intune Managed Browser tüm Azure AD bağlantılı uygulamalar için kullanabilirsiniz.

**Intune Managed Browser koşullu erişim desteği:** Artık çalışanların, uygulama tabanlı koşullu erişim ilkeleri kullanarak Intune Managed Browser 'ı kullanmasını zorunlu kılabilirsiniz.

Bu konuda hakkında daha fazla bilgiyi bizim [blog gönderisi](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Daha fazla bilgi için bkz.

- [Uygulama tabanlı koşullu erişim kurulumu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Yönetilen tarayıcı ilkelerini yapılandırma](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Uygulama proxy'si GA Powershell modülündeki cmdlet'leri

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** erişim denetimi
 
Uygulama proxy'si cmdlet'leri için desteği Powershell GA modülünde sunuldu! Bu Powershell modülleri - daha fazla bir yıl, bazı cmdlet'ler hale gelirse çalışmayı durdurabilir güncel gerektirir. 

Daha fazla bilgi için [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 için yerel istemcileri tarafından etkileşimli olmayan bir protokol kullanarak sorunsuz SSO desteklenir

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Office 365 yerel istemci kullanarak kullanıcı (sürüm 16.0.8730.xxxx ve üstü) sorunsuz çoklu oturum açma kullanarak sessiz oturum açma deneyimi alın. Bu destek, Azure AD'ye etkileşimli olmayan bir protokol (WS-Trust) ekleyerek sağlanır.

Daha fazla bilgi için [nasıl sorunsuz çoklu oturum açma çalışma ile yerel bir istemcide oturum?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Bir uygulamayı Azure AD'nin Kiracı uç noktalar için oturum açma isteği gönderirse bir sessiz oturum açma deneyimini sorunsuz SSO ile kullanıcılar Al

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Kullanıcılar bir sessiz oturum açma deneyimini sorunsuz SSO ile bir uygulama bildirimi alır (örneğin, `https://contoso.sharepoint.com`) oturum açma istekleri için Azure AD'nin Kiracı uç noktası; diğer bir deyişle, gönderir `https://login.microsoftonline.com/contoso.com/<..>` veya `https://login.microsoftonline.com/<tenant_ID>/<..>` - Azure AD'nin ortak uç nokta yerine (`https://login.microsoftonline.com/common/<...>`).

Daha fazla bilgi için [Azure Active Directory sorunsuz çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Sorunsuz çoklu oturum açma almak için kullanıcıların Intranet bölgesi ayarlarını daha önce iki URL yerine yalnızca bir Azure AD URL'sini eklemeniz gerekir

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Kullanıcılarınız için sorunsuz SSO alma için yalnızca bir Azure AD URL kullanıcıların intranet bölgesi ayarlarını Active Directory'de Grup İlkesi'ni kullanarak eklemeniz gerekir: `https://autologon.microsoftazuread-sso.com`. Daha önce müşterilerin iki URL'si eklemek için zorunluluğundaydı.

Daha fazla bilgi için [Azure Active Directory sorunsuz çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Yeni Federasyon uygulamaları kullanılabilir Azure AD uygulama Galerisi

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme

Mart 2018'de bu 15 yeni Federasyon uygulamalarla uygulama galerimizden desteği ekledik:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Yardımcısı tarafından FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Genliğe](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech dijital Kuruluş Sunucusu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu bulut](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial).

Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Azure kaynakları için PIM kullanıma sunuldu

**Türü:** yeni özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management
 
Dizin rolleri için Azure AD Privileged Identity Management kullanıyorsanız, artık PIM'ın süresi sınırlı erişim ve atama özellikleri abonelik, kaynak grupları, sanal makineler ve desteklenen herhangi bir kaynak gibi Azure kaynak rolleri için kullanabilirsiniz Azure Resource Manager tarafından. Just-ın-Time rolleri etkinleştirirken multi-Factor Authentication yürürlüğe ve onaylanan değişiklik windows ile koordinasyon halinde etkinleştirmeleri zamanlayabilirsiniz. Ayrıca, bu sürüm geliştirmeleri güncelleştirilmiş bir kullanıcı Arabirimi, onay iş akışları ve özelliği yakında sona erecek rolleri genişletmek ve süresi dolan rolleri yenileme dahil olmak üzere genel Önizleme sırasında kullanılabilir değil ekler.

Daha fazla bilgi için [(Önizleme) Azure kaynakları için PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Uygulamaları belirteçlerinizi (genel Önizleme) için isteğe bağlı bir talep ekleme

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Özel veya isteğe bağlı isteği Jwt'ler veya SAML belirteçlerini talep artık Azure AD uygulamanızı geçirebilirsiniz.  Bu, varsayılan boyutu veya Uygulanabilirlik kısıtlamaları nedeniyle bir belirteç olarak bulunmayan bir kullanıcı ya da Kiracı hakkında taleplerdir.  Şu anda genel önizlemede Azure AD uygulamaları v1.0 ve v2.0 uç noktalarda budur.  Bilgi için bkz. hangi talepleri temel eklenebilir ve bunları istemek için uygulama bildirimini düzenleme.  

Daha fazla bilgi için [isteğe bağlı Azure AD'de talep](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD PKCE daha güvenli OAuth akışlar için destekler.

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Azure AD belgeleri, OAuth 2.0 yetkilendirme kodu verme akışı sırasında daha güvenli iletişime izin veren PKCE desteği Not güncelleştirildi.  S256 hem düz metin code_challenges v1.0 ve v2.0 Uç noktalara desteklenir. 

Daha fazla bilgi için [bir yetkilendirme kodu istek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Tüm kullanıcı öznitelik değerleri Workday Get_Workers API'SİNDE kullanılabilir sağlama desteği

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama sağlama  
**Ürün özelliği:** 3. taraf tümleştirme
 
Gelen Workday'den Active Directory artık Azure AD için sağlama ve genel Önizleme çıkarma ve sağlama Workday Get_Workers API'SİNDE kullanılabilir tüm öznitelik değerleri destekler. Bu ek standart yüzlerce destekler ekler ve ilk sürümü Workday ile birlikte gelen olanlar dışında özel öznitelikler gelen bağlayıcı sağlama.

Daha fazla bilgi için bkz: [Workday kullanıcı özniteliklerinin listesi özelleştirme](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Grup üyeliğini dinamik olan statik ve tersi değiştirme

**Türü:** yeni özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün özelliği:** işbirliği
 
Üyelik bir gruba nasıl yönetilir değiştirmek mümkündür. Bu gruba varolan herhangi bir başvuruyu hala geçerli olduğundan sistemde aynı grubu adını ve Kimliğini tutmak istediğinizde kullanışlıdır. Yeni grup oluşturma, bu başvuruları güncelleştirilmesi gerekir.
Bu işlevselliği desteklemek için Azure AD yönetim merkezini güncelleştirdik. Şimdi, müşterilerin var olan grupları dinamik üyelik atanan üyelik ve tersi dönüştürebilirsiniz. Mevcut PowerShell cmdlet'leri de yine kullanılabilir durumdadır.

Daha fazla bilgi için bkz. [Azure Active Directory gruplar Için dinamik üyelik kuralları](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Sorunsuz çoklu oturum açma ile geliştirilmiş oturum kapatma davranışı

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Kullanıcılar, Azure AD tarafından güvenli hale getirilmiş bir uygulamaya dışında açıkça imzalanmış olsa bile, daha önce bunlar otomatik olarak etki alanına katılmış cihazlarından bir Azure AD uygulaması, corpnet içinde yeniden erişmeye çalışıyorsanız geri sorunsuz SSO kullanarak oturum açmış olmanız. Bu değişiklik, oturumunuzu desteklenir.  Bu aynı veya farklı Azure seçmelerini sağlar geri, otomatik olarak sorunsuz SSO kullanarak oturum açmış yerine oturum açmak için bir AD hesabı.

Daha fazla bilgi için [Azure Active Directory sorunsuz çoklu oturum açma](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Uygulama Proxy Bağlayıcısı sürümü yayımlanan 1.5.402.0

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** kimlik güvenliği ve koruması
 
Bu bağlayıcı sürüm kademeli olarak Kasım kullanıma sunulacaktır. Bu yeni bağlayıcı sürüm, aşağıdaki değişiklikleri içerir:

- Bağlayıcı artık etki alanı düzeyi tanımlama bilgilerini bunun yerine alt etki alanı düzeyini ayarlar. Bu daha sorunsuz SSO bir deneyim sağlar ve yedek kimlik doğrulama istemleri önler.
- Öbekli kodlama istekleri için destek
- Geliştirilmiş bağlayıcı sistem durumu izleme 
- Birçok hata düzeltmesi ve kararlılık geliştirmeleri

Daha fazla bilgi için [anlamak Azure AD uygulama ara sunucusu bağlayıcıları](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Şubat 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Kullanıcılar ve grupları yönetmek için Gezinti geliştirildi

**Türü:** değişiklik planı  
**Hizmet kategorisi:** dizin yönetimi  
**Ürün özelliği:** dizini

Kullanıcılar ve grupları yönetmek için gezinti deneyimini basitleştirilmiştir. Artık dizine genel bakış'tan doğrudan silinen kullanıcıların listesine daha kolay erişimi olan tüm kullanıcıların listesini gidebilirsiniz. Grup Yönetimi ayarlarına daha kolay erişim ile tüm grupların listesine doğrudan dizin genel bakış'tan da gidebilirsiniz. Ve ayrıca dizine genel bakış sayfasından bir kullanıcı, Grup, Kurumsal uygulama veya uygulama kaydını arayabilirsiniz. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Oturum açma işlemleri ve denetim kullanılabilirliğini raporlar 21Vianet tarafından işletilen Microsoft azure'da (Azure China 21Vianet)

**Türü:** yeni özellik  
**Hizmet kategorisi:** Azure Stack  
**Ürün özelliği:** izleme ve Raporlama

Azure AD etkinlik günlük raporlar tarafından (Azure China 21Vianet) örnekleri 21Vianet tarafından işletilen Microsoft azure'da kullanıma sunulmuştur. Aşağıdaki günlüklere dahil edilir:

- **Oturum açma işlemleri etkinlik günlüklerini** -tüm oturum açma işlemleri içerir, Kiracı ile ilişkilendirilen günlükleri.

- **Self Servis parola denetim günlükleri** -SSPR denetim günlükleri içerir.

- **Dizin Yönetimi denetim günlüklerini** -kullanıcı yönetimi, uygulama yönetimi ve diğerleri gibi tüm dizin yönetimiyle ilgili denetim günlükleri içerir.

Bu günlükleri ile ortamınızın nasıl çalıştığını içine öngörü sahibi olabilir. Sağlanan verilerle:

- Uygulama ve hizmetlerinizin kullanıcılarınız tarafından nasıl kullanıldığını saptayabilirsiniz.

- Kullanıcılarınızın işlerini yapmalarını engelleyen sorunları giderin.

Bu raporların nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Active Directory raporlama](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Azure AD Etkinlik raporlarını görüntülemek için "Rapor okuyucu" rolünü (yönetici olmayan rol) kullanın

**Türü:** yeni özellik  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** izleme ve Raporlama

Yönetici olmayan rollerin Azure AD etkinlik erişmesini etkinleştirmek için müşterilerin geri bildirim parçası günlükleri gibi erişim oturum açma ve Azure portalı yanı sıra Graph Apı'lerimizi kullanarak içinde denetim etkinliği için "Rapor okuyucu" rolündeki kullanıcılar için özelliğini etkinleştirdik. 

Daha fazla bilgi için bu raporları kullanmak üzere nasıl [Azure Active Directory raporlama](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Kullanıcı özniteliği ve kullanıcı tanımlayıcısı olarak kullanılabilir EmployeeID talebi

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** SSO
 
Yapılandırabileceğiniz **EmployeeID** üye kullanıcılar ve B2B Konukları uygulamalarda Kurumsal Uygulama UI'dan SAML tabanlı oturum açma için kullanıcı özniteliği ve kullanıcı tanımlayıcısı olarak.

Daha fazla bilgi için [Azure Active Directory'de kurumsal uygulamalar için SAML belirtecinde verilen talepleri özelleştirme](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Azure AD uygulama ara Sunucusu'nda joker kullanarak Basitleştirilmiş uygulama yönetimi

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Uygulama dağıtımını kolaylaştırın ve yönetim yükünüzü azaltmak için artık joker karakter kullanan uygulamalar yayımlama olanağı desteklenmektedir. Bir joker uygulama yayımlamak için standart bir uygulama yayımlama akışını takip edin, ancak iç ve dış URL'lerinde bir joker karakter kullanın.

Daha fazla bilgi için [joker karakteri uygulamalarında Azure Active Directory Uygulama proxy'si](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Uygulama Ara Sunucusu'nun yapılandırılmasını desteklemeye yönelik yeni cmdlet'ler

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** platformu

AzureAD PowerShell Önizleme modülünün en son sürümü, müşterilerin PowerShell kullanarak uygulama Proxy uygulamaları yapılandırmanıza imkan tanıyan yeni cmdlet'ler içerir.

Yeni cmdlet'ler şunlardır: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Grupların yapılandırılmasını desteklemeye yönelik yeni cmdlet'ler

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulama ara sunucusu  
**Ürün özelliği:** platformu

AzureAD PowerShell modülünün en son sürümü Azure AD'de grupları yönetmek için cmdlet'leri içerir. Bu cmdlet'ler, daha önce AzureADPreview modülünde kullanılabilen ve şimdi AzureAD modülüne eklendi

Şimdi genel kullanılabilirlik sürümü olan Grup cmdlet'ler şunlardır: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- Yeni-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Ekle-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Azure AD Connect'in yeni bir sürüm kullanılabilir

**Türü:** yeni özellik  
**Hizmet kategorisi:** AD eşitleme  
**Ürün özelliği:** platformu
 
Azure AD Connect, Azure AD arasında ve Windows Server Active Directory ve LDAP dahil olmak üzere şirket içi veri kaynakları verileri eşitlemek için tercih edilen bir araçtır.

>[!Important]
>Bu derleme şema ve eşitleme tanıtır kural değişiklikler. Azure AD Connect eşitleme hizmeti bir yükseltmeden sonra bir tam içeri aktarma ve tam eşitleme adımları tetikler. Bu davranışı değiştirmek konusunda daha fazla bilgi için bkz: [yükseltmeden sonra tam eşitleme erteleneceği nasıl](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Bu sürüm aşağıdaki güncelleştirmeleri ve değişiklikleri sahiptir:

**Giderilen sorunlar**

- Sonraki sayfaya geçiş yaparken bölüm filtreleme sayfası için arka plan görevleri zamanlama penceresi düzeltmesi.

- Yapılandırmaveritabanı özel eylemi sırasında erişim ihlaline neden bir hata düzeltildi.

- SQL bağlantı zaman aşımı kurtarmak için bir hata düzeltildi.

- Burada SAN joker karakterler sertifikalarla Önkoşul denetimi başarısız bir hata düzeltildi.

- AAD bağlayıcı dışarı aktarma sırasında miiserver.exe kilitlenmeye neden olan bir hata düzeltildi.

- DC üzerinde çalışan AAD neden olduğunda günlüğe bir hatalı parola denemesi yapılandırmasını değiştirmek için Sihirbazı eriştikleri bir hata düzeltildi

**Yeni özellikler ve geliştirmeler**
 
- Uygulama telemetrisini - Yöneticiler, bu sınıf Aç/Kapat verilerinin geçiş yapabilirsiniz.

- Azure AD sistem durumu verilerini - yöneticiler kendi sistem durumu ayarlarını denetlemek için sistem durumu portalı ziyaret etmeniz gerekir. Hizmet İlkesi değiştirildi sonra aracılar okuyun ve onu zorla.

- Cihaz geri yazmayı yapılandırma eylemleri ve sayfa başlatma için bir ilerleme çubuğu ekledik.

- Geliştirilmiş genel tanılama HTML rapor ve tam veri toplama ZIP metin / HTML raporu.

- Geliştirilmiş otomatik güvenilirliğini yükseltin ve sunucusunun sistem durumunu belirlenebilir emin olmak için ek telemetri eklendi.

- Kullanılabilir AD Bağlayıcısı hesabındaki ayrıcalıklı hesaplara kısıtlayın. Yeni yüklemeler için sihirbazın ayrıcalıklı hesapları izinleri kısıtlar MSOL hesabı oluşturduktan sonra MSOL hesabına sahip. Değişiklikler express yüklemeleri ve otomatik olarak oluşturma hesabıyla özel yüklemeleri etkiler.

- Yükleyici SA ayrıcalık AADConnect temiz yüklemesini zorunlu tutulmayacak şekilde değiştirildi.

- Belirli bir nesnesi için eşitleme sorunlarını gidermek için yeni yardımcı program'ı seçin. Şu anda aşağıdakiler için yardımcı program denetler:

    - Azure AD kiracısında kullanıcı hesabı ile eşitlenmiş kullanıcı nesnesi arasındaki UserPrincipalName uyuşmazlığı.
  
    - Nesne filtreleme etki alanı nedeniyle eşitleme filtrelediyseniz
  
    - Nesne eşitleme filtreleme kuruluş birimi (OU) nedeniyle filtrelediyseniz

- Belirli bir kullanıcı hesabı için şirket içi Active Directory içinde depolanan geçerli parola karması eşitleme için yeni yardımcı program'ı seçin. Yardımcı programı, parola değişikliği gerektirmez. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim ile kullanılmak üzere eklenen Intune Uygulama Koruması ilkelerini destekleyen uygulamalar

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Uygulama tabanlı koşullu erişimi destekleyen daha fazla uygulama ekledik. Artık, Office 365 ve bu onaylı istemci uygulamalarını kullanarak diğer Azure AD bağlantılı bulut uygulamalarınıza erişimi alabilirsiniz.

Aşağıdaki uygulamalar Şubat sonuna eklenecek:

- Microsoft Power BI

- Microsoft Başlatıcısı

- Microsoft Invoicing

Daha fazla bilgi için bkz.

- [Onaylı istemci uygulama gereksinimi](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD uygulama tabanlı koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Mobil deneyimle Kullanım koşulları güncelleştirme 

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** uyumluluk

Kullanım koşulları görüntülendiğinde artık sorun yaşamadan ' a tıklayabilirsiniz.  **Buraya tıklayın**. Bu bağlantıya tıkladığınızda kullanım Cihazınızda yerel olarak açılır. Belgenin yazı tipi boyutu veya cihazın ekran boyutu ne olursa olsun yakınlaştırma ve gerektiğinde belgeyi okuyalım. 

---
 
## <a name="january-2018"></a>Ocak 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Yeni Federasyon uygulamaları kullanılabilir Azure AD uygulama Galerisi 

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme

Ocak 2018'de app Galerisi'nde aşağıdaki yeni uygulamaları ile Federasyonu Desteği eklendi:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust gizlilik yönetim yazılımı](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federasyon dizin ve [uygunluk NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial).

Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Ek risk algılandı bilgilerinizle oturum açın

**Türü:** yeni özellik  
**Hizmet kategorisi:** kimlik koruması  
**Ürün özelliği:** kimlik güvenliği ve koruması

Algılanan bir risk algılaması için aldığınız Öngörüler, Azure AD aboneliğinize bağlıdır. Azure AD Premium P2 sürümü ile temel alınan tüm algılamalar hakkında en ayrıntılı bilgileri alın.

Azure AD Premium P1 sürümü sayesinde, lisansınız kapsamında olmayan algılamalar, risk algılama tarafından algılanan ek risklerle birlikte oturum açma olarak görünür.

Daha fazla bilgi için bkz. [risk algılamalarını Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Son kullanıcı erişim panellerinde Office 365 uygulamalarından gizleme

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulamalarım  
**Ürün özelliği:** SSO

Artık, nasıl Office 365 uygulamaları üzerinde yeni bir kullanıcı ayarı ile kullanıcı erişim panellerinde görünmesi daha iyi yönetebilirsiniz. Bu seçenek Office uygulamalarını yalnızca Office Portalı'nda gösterilecek tercih ederseniz bir kullanıcının erişim panellerinde uygulamaları sayısını azaltmak için yararlıdır. Ayar bulunan **kullanıcı ayarları** ve olarak etiketlenen **kullanıcılar yalnızca Office 365 uygulamaları Office 365 portalında görebilir**.

Daha fazla bilgi için [bir uygulamadan Azure Active Directory'de kullanıcı deneyimini Gizle](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Uygulamanın URL'sini doğrudan parola SSO için etkinleştirildiği uygulamalar sorunsuz oturum 

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulamalarım  
**Ürün özelliği:** SSO

Uygulamalarım tarayıcı uzantısı aracılığıyla uygulamalarım çoklu oturum tarayıcınızda bir kısayol olarak özellik hakkında size uygun bir aracı kullanıma sunuldu. Kullanıcının yükledikten sonra tarayıcılarında bunları uygulamalarına hızlı erişim sağlayan bir waffle menüsü simgesi görürsünüz. Kullanıcılar artık yararlanabilirsiniz:

- Özelliği doğrudan uygulamanın oturum açma sayfasında parola tabanlı SSO uygulamaları için oturum açın
- Hızlı arama özelliğini kullanarak herhangi bir uygulama başlatın
- Uzantı son kullanılan uygulamalar için kısayollar
- Uzantı, Microsoft Edge, Chrome ve Firefox için kullanılabilir.
 
Daha fazla bilgi için [My Apps güvenli oturum açma uzantısı](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD yönetim Klasik Azure portalı deneyiminde Çekildi

**Türü:** kullanım dışı   
**Hizmet kategorisi:** Azure AD  
**Ürün özelliği:** dizini

8 Ocak 2018'den itibaren Azure AD Yönetim'den itibaren Klasik Azure portalı deneyiminde Çekildi. Bu, Azure Klasik Portalı'nın kendisini kullanımdan kaldırma ile birlikte yapıldı. Gelecekte, kullanmanız gereken [Azure AD yönetim merkezini](https://aad.portal.azure.com) tüm, portal tabanlı yönetim için Azure ad.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor web portalı kullanımdan kaldırıldı

**Türü:** kullanım dışı  
**Hizmet kategorisi:** Azure AD  
**Ürün özelliği:** dizini
 
8 Ocak 2018'den itibaren PhoneFactor web portalı kullanımdan kaldırıldı. Bu portalı MFA sunucusunun yönetimi için kullanılan, ancak bu işlevleri portal.azure.com adresindeki Azure portalında taşınmıştır. 

MFA yapılandırma şu konumdadır: **Azure Active Directory \> MFA sunucusu**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Azure AD raporlar kullanımdan kaldırma

**Türü:** kullanım dışı  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** kimlik yaşam döngüsü yönetimi  


Genel kullanılabilirlik yeni API'ler hem etkinlik ve güvenlik raporları için rapor API'leri kullanıma sunuldu ve yeni Azure Active Directory Yönetim Konsolu ile altında "/ reports" uç nokta son 31 Aralık 2017 itibariyle devre dışı bırakılmış.

**Kullanılabilir nedir?**

Yeni Yönetici Konsolu geçiş işleminin bir parçası olarak 2 yeni API'ler Azure AD etkinlik günlüklerini almak için kullanılabilir gerçekleştirdik. Yeni API'leri daha zengin filtreleme ve sıralama daha zengin denetim ve oturum açma etkinlikleri sağlamaya ek olarak işlevselliği sunmaktadır. Daha önce güvenlik raporları üzerinden kullanılabilen verilere artık Microsoft Graph kimlik koruması risk algılamaları API 'SI aracılığıyla erişilebilir.

Daha fazla bilgi için bkz.

- [Azure Active Directory raporlama API'SİYLE çalışmaya başlama](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Microsoft Graph ve Azure Active Directory kimlik koruması ile çalışmaya başlama](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Aralık 2017

### <a name="terms-of-use-in-the-access-panel"></a>Erişim panelinde kullanım koşulları

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** uyumluluk
 
Artık erişim Masası'na gidin ve daha önce kabul ettiğiniz kullanım koşullarını görüntüleyin.

Şu adımları uygulayın:

1. Git [MyApps portalında](https://myapps.microsoft.com)ve oturum açın.

2. Sağ üst köşede, adınızı seçerek ve ardından **profili** listeden. 

3. Üzerinde **profili**seçin **kullanım koşullarını gözden geçir**. 

4. Kullanım koşullarını gözden geçirebilir, kabul edildi. 

Daha fazla bilgi için [kullanım özelliği (Önizleme) Azure AD kullanım koşulları](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Yeni Azure AD oturum açma deneyimi

**Türü:** yeni özellik  
**Hizmet kategorisi:** Azure AD  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
Azure AD ve Microsoft hesabı kimlik sistemi kullanıcı arabirimleri, tutarlı bir görünüm olduğuna şekilde tasarlanmıştır. Ayrıca, Azure AD oturum açma sayfasının kullanıcı adının ilk olarak, ikinci bir ekranda kimlik bilgisi tarafından izlenen toplar.

Daha fazla bilgi için [yeni Azure AD oturum açma deneyiminin genel önizlemeye sunuldu](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Daha az oturum açma istemi: yeni "Oturumumu açık bırak" bir deneyimi için Azure AD oturum açma

**Türü:** yeni özellik  
**Hizmet kategorisi:** Azure AD  
**Ürün özelliği:** kullanıcı kimlik doğrulaması
 
**Oturumumu açık bırak** Azure AD oturum açma sayfasında onay kutusunu başarıyla kimlik doğrulama işleminden sonra yedekleme gösteren yeni bir İstemi ile değiştirildi. 

Yanıt, **Evet** bu istemi için hizmeti, bir sürekli yenileme belirteci sağlar. Bu seçili olduğunda aynı, davranıştır **Oturumumu açık bırak** eski deneyimi onay kutusu. Federasyon Hizmeti ile başarıyla kimlik doğrulama işleminden sonra Federasyon kiracıları için bu istemi gösterir.

Daha fazla bilgi için [daha az oturum açma istemi: yeni "Oturumumu açık bırak" deneyimi için Azure AD Önizleme aşamasındadır](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Kabul etmeden önce genişletilecek kullanım koşullarını gerektirecek şekilde yapılandırması Ekle

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** uyumluluk
 
Yöneticiler için bir seçenek, kullanıcıların koşulları kabul etmeden önce kullanım koşullarını genişletmesini gerektirir.

Şunlardan birini seçin **üzerinde** veya **kapalı** kullanıcıların kullanım koşullarını genişletmesini gerekli kıl için. **Üzerinde** ayarı kabul etmeden önce kullanım koşullarını görüntülemek kullanıcıların gerektirir.

Daha fazla bilgi için [kullanım özelliği (Önizleme) Azure AD kullanım koşulları](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Uygun rol atamaları için kapsamlı etkinleştirme

**Türü:** yeni özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management
 
Kapsamlı etkinleştirme, özgün atama varsayılan değerinden daha az bağımsız çalışma sınırı ile uygun bir Azure kaynak rol atamalarını etkinleştirmek için kullanabilirsiniz. Kiracınızda atadığınız bir abonelik sahibi olarak bir örnektir. Kapsamlı etkinleştirme'yle (örneğin, kaynak grupları ve sanal makineler) abonelik kapsamında yer alan en fazla beş kaynakları için sahip rolü etkinleştirebilirsiniz. Aktivasyon kapsamı, kritik Azure kaynakları için istenmeyen değişiklikleri yürütülmeden olasılığını düşürebilir.

Daha fazla bilgi için [Azure AD Privileged Identity Management nedir?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD uygulama Galerisi'nde yeni Federasyon uygulamaları

**Türü:** yeni özellik  
**Hizmet kategorisi:** kurumsal uygulamalar  
**Ürün özelliği:** 3. taraf tümleştirme

Aralık 2017'de, bu yeni Federasyon uygulamalarla uygulama galerimizden desteği ekledik:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager [EFI dijital mağaza](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [görüntü WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD tümleştirme](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [GmbH çözünürlüğün Bamboo için SAML SSO](https://go.microsoft.com/fwlink/?linkid=863520), [GmbH çözünürlüğün Bitbucket için SAML SSO](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD tümleştirmesi.

Uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory ile SaaS uygulama tümleştirmesi](https://aka.ms/appstutorial).

Azure AD uygulama galerisinde uygulamanızı listeleme hakkında daha fazla bilgi için bkz. [uygulamanızı Azure Active Directory Uygulama galerisinde listeleyin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD Dizin rolleri için onay iş akışları

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management
 
Azure AD Dizin rolleri için onay iş akışı genel kullanıma sunulmuştur.

Ayrıcalıklı rol kullanabilmeniz için önce onay iş akışı ile ayrıcalıklı rol yöneticileri rol etkinleştirme isteği uygun rolü üyeleri gerektirebilir. Birden çok kullanıcı ve grupları temsilcisi onay sorumlulukları olabilir. Uygun Rol üyeleri onayı tamamlandı ve rollerine etkin olduğunda bildirim alırsınız.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Geçişli kimlik doğrulaması: Skype for Business desteği

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** kimlik doğrulamaları (oturum açma bilgileri)  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Geçişli kimlik doğrulaması artık kullanıcı oturum açma işlemleri Skype Kurumsal çevrimiçi içeren modern kimlik doğrulamasını destekleyen iş istemci uygulamalarını ve karma topolojiler için destekler. 

Daha fazla bilgi için [Skype için modern kimlik doğrulaması ile desteklenen iş topolojiler](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC (Önizleme) için Azure AD Privileged Identity Management güncelleştirmeleri

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management
 
Azure AD Privileged Identity Management (PIM) Azure rol tabanlı Access Control (RBAC) için genel Önizleme yenileme ile şunları yapabilirsiniz:

* Yeterli yönetim kullanın.
* Kaynak rolleri etkinleştirmek için onay gerektirir.
* Azure RBAC rolleri ile hem Azure AD için onay gerektiren bir rolü ve gelecekteki bir etkinleştirme zamanlayın.
 
Daha fazla bilgi için [(Önizleme) Azure kaynakları için Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>Kasım 2017
 
### <a name="access-control-service-retirement"></a>Erişim denetimi hizmeti devre dışı bırakma

**Türü:** değişiklik planı  
**Hizmet kategorisi:** erişim denetimi hizmeti  
**Ürün özelliği:** erişim denetimi hizmeti 

Azure Active Directory erişim denetimi (erişim denetimi hizmeti olarak da bilinir) geç 2018'de kullanımdan kaldırılacaktır. Önümüzdeki birkaç hafta içinde ayrıntılı zamanlama ve üst düzey geçiş kılavuzunu içeren daha fazla bilgi sağlanacaktır. Erişim denetimi hizmeti hakkında sorular ile bu sayfada açıklamaları bırakabilirsiniz ve bir takım üyesi yanıt.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Intune Managed Browser için tarayıcı erişimi kısıtlama 

**Türü:** değişiklik planı  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Onaylanmış bir uygulama Intune Managed Browser'ı kullanarak, Office 365 ve diğer Azure AD bağlantılı bulut uygulamaları için tarayıcı erişimini kısıtlayabilirsiniz. 

Artık uygulama tabanlı koşullu erişim için aşağıdaki koşulu yapılandırabilirsiniz:

**İstemci uygulamaları:** tarayıcı

**Değişikliğin etkilerini nedir?**

Günümüzde bu durum kullandığınızda erişim engellenir. Önizleme mevcut değil, tüm erişim yönetilen tarayıcı uygulaması kullanımını gerektirir. 

Bu özellik ve yaklaşan blogları ve sürüm notları hakkında daha fazla bilgi arayın. 

Daha fazla bilgi için bkz. [Azure AD 'de koşullu erişim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim için yeni onaylanan istemci uygulamaları

**Türü:** değişiklik planı  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Aşağıdaki uygulamalar listede yer [onaylı istemci uygulamalar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Daha fazla bilgi için bkz.

- [Onaylı istemci uygulama gereksinimi](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD uygulama tabanlı koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Kullanım koşulları birden çok dil desteği

**Türü:** yeni özellik    
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** uyumluluk

Yöneticiler artık birden çok PDF belgeleri içeren yeni kullanım koşulları oluşturabilirsiniz. Bu PDF belgeleri karşılık gelen bir dil ile etiketleyebilirsiniz. Kullanıcılara, PDF, eşleşen dil tercihlerine göre ile gösterilir. Eşleşme yoksa varsayılan dilde gösterilir.

---
 
### <a name="real-time-password-writeback-client-status"></a>Gerçek zamanlı parola geri yazma istemcinizin durumunu

**Türü:** yeni özellik  
**Hizmet kategorisi:** Self Servis parola sıfırlama  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Artık, şirket içi parola geri yazma istemcinizin durumunu gözden geçirebilirsiniz. Bu seçenek kullanılabilir **şirket içi tümleştirme** bölümünü [parola sıfırlama](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) sayfası. 

Şirket içi geri yazma istemciniz için bağlantınızda bir sorun varsa, size sağlayan bir hata iletisi görürsünüz:

- Şirket içi geri yazma istemcinize neden bağlanamıyorsunuz bilgi.
- Sorunun çözümlenmesinde yardımcı belgeleri bağlantısı. 

Daha fazla bilgi için [şirket tümleştirme](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim 
 
**Türü:** yeni özellik  
**Hizmet kategorisi:** Azure AD  
**Ürün özelliği:** kimlik güvenliği ve koruması

Artık Office 365 ve diğer Azure AD bağlı bulut uygulamalarına erişimi, [Azure AD uygulama tabanlı koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)kullanarak Intune uygulama koruma ilkelerini destekleyen [onaylanan istemci uygulamalarına](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) kısıtlayabilirsiniz. Intune uygulama koruma ilkelerini yapılandırma ve bu istemci uygulamalarını üzerindeki şirket verilerini korumak için kullanılır.

[Uygulama tabanlı](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) [cihaz tabanlı](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) koşullu erişim ilkeleriyle, kişisel ve şirket cihazlarındaki verileri koruma esnekliğine sahip olursunuz.

Aşağıdaki koşullar ve denetimler artık uygulama tabanlı koşullu erişim ile birlikte kullanılabilir:

**Desteklenen platform koşulu**

- iOS
- Android

**İstemci uygulamaları koşulu**

- Mobil uygulamalar ve masaüstü istemcileri

**Erişim denetimi**

- Onaylı istemci uygulaması gerektir

Daha fazla bilgi için bkz. [Azure AD uygulama tabanlı koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure portalında Azure AD cihazları yönetme

**Türü:** yeni özellik  
**Hizmet kategorisi:** cihaz kaydı ve Yönetimi  
**Ürün özelliği:** kimlik güvenliği ve koruması

Artık tüm cihazlarınızı Azure AD'ye bağlı bulabilirsiniz ve cihaz ile ilgili etkinlikleri tek bir yerde. Tüm cihaz kimliklerini ve Azure Portalı'ndaki ayarları yönetmek için yeni bir yönetim deneyimi vardır. Bu sürümde, şunları yapabilirsiniz:

- Azure AD 'de koşullu erişim için kullanılabilen tüm cihazlarınızı görüntüleyin.
- Azure hibrit içeren özelliklerini görüntüleme AD alanına katılmış cihazlar.
- Azure AD'ye katılmış cihazlar için BitLocker anahtarlarını bulmak, Cihazınızı ve Intune ile yönetme.
- Azure AD cihaz ile ilgili ayarları yönetin.

Daha fazla bilgi için [Azure portalını kullanarak cihazları yönetme](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD koşullu erişim için bir cihaz platformu olarak macOS desteği 

**Türü:** yeni özellik    
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması 

Artık macOS 'ı Azure AD koşullu erişim ilkenize cihaz platformu olarak dahil edebilir (veya dışlayabilirsiniz). Desteklenen cihaz platformları için macOS'ın eklenmesiyle, şunları yapabilirsiniz:

- **Kaydetme ve Intune kullanarak macOS cihazlarını yönetin.** Benzer şekilde, iOS ve Android gibi diğer platformlarda, Şirket portalı uygulamasını birleşik kayıtları yapmak için macOS için kullanılabilir. Yeni Şirket portalı uygulaması macOS için Intune ile cihaz kaydetme ve Azure AD ile kaydetmek için kullanabilirsiniz.
- **MacOS cihazları Intune'da tanımlanan kuruluşunuzun uyumluluk ilkelerine uymaları emin olun.** Azure portalında Intune'da artık macOS cihazları için Uyumluluk ilkelerini ayarlayabilirsiniz. 
- **Yalnızca uyumlu macOS cihazlar için Azure AD'de, uygulamalara erişimi kısıtlayın.** Koşullu erişim ilkesi yazma, macOS öğesini ayrı bir cihaz platformu seçeneği olarak içerir. Artık Azure 'da ayarlanan hedeflenen uygulama için macOS 'a özgü koşullu erişim ilkelerini yazabilirsiniz.

Daha fazla bilgi için bkz.

- [Intune ile macOS cihazları için cihaz uyumluluğu ilkesi oluşturma](https://aka.ms/macoscompliancepolicy)
- [Azure AD 'de koşullu erişim](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Azure multi-Factor Authentication için ağ ilkesi sunucusu uzantısı 

**Türü:** yeni özellik    
**Hizmet kategorisi:** çok faktörlü kimlik doğrulaması  
**Ürün özelliği:** kullanıcı kimlik doğrulaması

Azure multi-Factor Authentication için ağ ilkesi sunucusu uzantısı, mevcut sunucularınızda kullanarak bulut tabanlı çok faktörlü kimlik doğrulama özellikleri kimlik doğrulama altyapınızı ekler. Ağ İlkesi Sunucusu uzantısı ile mevcut kimlik doğrulama akışınıza telefon araması, SMS mesajı ve telefon uygulaması doğrulama ekleyebilirsiniz. Yükleme, yapılandırma ve yeni sunuculara bakım gerekmez. 

Bu uzantı, sanal özel ağ bağlantılarıyla Azure multi-Factor Authentication Sunucusu'nu dağıtmadan korumak istediğiniz kuruluşlar için oluşturuldu. Ağ ilkesi sunucusu arasında RADIUS ve Azure multi-Factor Authentication'ı bulut tabanlı bir ikinci faktör kimlik doğrulaması sağlamak için bir bağdaştırıcı uzantısı görür federasyona eklenenler veya eşitlenmiş kullanıcıları.

Daha fazla bilgi için [mevcut ağ ilkesi sunucusu altyapınızı Azure multi-Factor Authentication ile tümleştirme](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Geri yükleme ya da kalıcı olarak silinen Kullanıcıları Kaldır

**Türü:** yeni özellik    
**Hizmet kategorisi:** kullanıcı yönetimi  
**Ürün özelliği:** dizini 

Azure AD Yönetim merkezinde artık şunları yapabilirsiniz:

- Silinen bir kullanıcıyı geri yükleyin. 
- Kullanıcı kalıcı olarak siler.

**Denemek için:**

1. Azure AD Yönetim merkezinde seçin [tüm kullanıcılar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) içinde **Yönet** bölümü. 

2. Gelen **Göster** listesinden **kullanıcılar'yakın zamanda silinip**. 

3. Yakın zamanda silinen kullanıcılar, bir veya daha fazla seçin ve ardından ya da geri yükleyebilir veya kalıcı olarak silebilirsiniz.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim için yeni onaylanan istemci uygulamaları
 
**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması

Aşağıdaki uygulamalar listesine eklenmiş olmasından [onaylı istemci uygulamalar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 

Daha fazla bilgi için bkz.

- [Onaylı istemci uygulama gereksinimi](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD uygulama tabanlı koşullu erişim](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Koşullu erişim ilkesindeki denetimler arasında "OR" kullanın 

**Türü:** değiştirilen özellik    
**Hizmet kategorisi:** koşullu erişim  
**Ürün özelliği:** kimlik güvenliği ve koruması
 
Koşullu erişim denetimleri için artık "veya" (seçili denetimlerden birini gerektir) kullanabilirsiniz. "Veya" arasında erişim denetim ilkeleri oluşturmak için bu özelliği kullanabilirsiniz. Örneğin, bir kullanıcı çok faktörlü kimlik doğrulaması kullanarak oturum açın "veya" uyumlu bir cihaz üzerinde olmasını gerektiren bir ilke oluşturmak için bu özelliği kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure AD koşullu erişim 'Deki denetimler](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Gerçek zamanlı risk algılamalarını toplama

**Türü:** değiştirilen özellik    
**Hizmet kategorisi:** kimlik koruması  
**Ürün özelliği:** kimlik güvenliği ve koruması

Azure AD Kimlik Koruması, belirli bir gündeki aynı IP adresinden kaynaklanan tüm gerçek zamanlı risk algılamaları artık her risk algılama türü için toplanır. Bu değişiklik, Kullanıcı güvenliğine hiçbir değişiklik yapılmadan gösterilen risk algılamaları hacminin sınırlarını sınırlar.

Temel alınan gerçek zamanlı algılama, kullanıcı oturum açtığı her zaman çalışır. Çok faktörlü kimlik doğrulaması veya erişimi engelleme için ayarlanmış bir oturum açma riski ilkesi varsa, yine de her riskli oturum açma sırasında tetiklenir.
 
---
 
## <a name="october-2017"></a>Ekim 2017

### <a name="deprecate-azure-ad-reports"></a>Azure AD raporlar kullanımdan kaldırma

**Türü:** değişiklik planı  
**Hizmet kategorisi:** raporlama  
**Ürün özelliği:** kimlik yaşam döngüsü yönetimi  

Azure portalı ile sağlar:

- Yeni bir Azure AD Yönetim Konsolu.
- Yeni API'ler için etkinlik ve güvenlik raporları.
 
Bu yeni özellikler nedeniyle/Reports uç nokta altında API'ler rapor kullanımdan kaldırılmıştır 10 Aralık 2017'de. 

---

### <a name="automatic-sign-in-field-detection"></a>Otomatik oturum açma alanı algılaması

**Türü:** düzeltildi   
**Hizmet kategorisi:** uygulamalarım  
**Ürün özelliği:** çoklu oturum açma  

Azure AD, bir HTML kullanıcı adı ve parola alanı işlemek uygulamalar için otomatik oturum açma alanı algılaması destekler. Bu adımları bölümünde belgelendirilen [nasıl otomatik olarak bir uygulama için oturum açma alanlarını Yakala](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Bu özellik ekleyerek bulabilirsiniz bir *galeri dışı* uygulaması **kurumsal uygulamalar** sayfasını [Azure portalında](https://aad.portal.azure.com). Buna ek olarak, yapılandırabileceğiniz **çoklu oturum açma** bu yeni uygulama modu **parola tabanlı çoklu oturum açma**web URL'si girin ve ardından sayfanın kaydedin.
 
Bir hizmet sorunu nedeniyle bu işlevselliği geçici olarak devre dışı bırakıldı. Sorunun çözülüp çözülmediğini ve otomatik oturum açma alanı algılaması yeniden kullanılabilir.

---

### <a name="new-multi-factor-authentication-features"></a>Yeni çok faktörlü kimlik doğrulaması özellikleri

**Türü:** yeni özellik  
**Hizmet kategorisi:** çok faktörlü kimlik doğrulaması  
**Ürün özelliği:** kimlik güvenliği ve koruması  

Çok faktörlü kimlik doğrulaması (MFA), kuruluşunuzun koruma önemli bir parçasıdır. Kimlik bilgileri daha Uyarlamalı ve deneyimi daha sorunsuz hale getirmek için aşağıdaki özellikleri eklendi: 

- Çok faktörlü sınama sonuçları, MFA sonuçları programlı erişim içeren Azure AD oturum açma raporu doğrudan bütünleştirilmiştir.
- MFA yapılandırmanın daha derin bir şekilde Azure AD'ye yapılandırmaya tümleşik deneyimi Azure Portalı'nda.

Bu genel Önizleme ile MFA yönetim ve raporlama çekirdek Azure AD'ye yapılandırma deneyimi tümleşik bir parçası olan. Artık Azure AD deneyimi içinde MFA Yönetim Portalı işlevlerini yönetebilirsiniz.

Daha fazla bilgi için [MFA Azure Portalı'nda raporlama için başvuru](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Kullanım koşulları

**Türü:** yeni özellik  
**Hizmet kategorisi:** kullanım koşulları  
**Ürün özelliği:** uyumluluk  

Azure AD kullanım koşulları yasal gereksinimler veya uyumluluk gereksinimleriyle ilgili bildirimleri gibi bilgiler sunmak için kullanabilirsiniz.

Azure AD kullanım koşulları aşağıdaki senaryolarda kullanabilirsiniz:

- Genel, kuruluşunuzdaki tüm kullanıcılar için kullanım koşulları
- Belirli kullanıcı özniteliklerine (doktorlarla nurses karşılaştırması gibi) ya da yurtiçi ve uluslararası çalışanlara, dinamik gruplar tarafından yapılan temel kullanım koşulları
- Salesforce gibi yüksek etkili iş uygulamaları erişmek için belirli kullanım koşulları

Daha fazla bilgi için [Azure AD kullanım koşulları](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management geliştirmeleri

**Türü:** yeni özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün özelliği:** Privileged Identity Management  

Azure AD Privileged Identity Management ile yönetebilir, denetleyebilir ve kuruluşunuz içinde (Önizleme) Azure kaynaklarına erişimi izleyin:

- Abonelikler
- Kaynak grupları
- Sanal makineler 

Azure RBAC işlevselliği kullandığı tüm kaynakları Azure portalındaki güvenlik ve Azure AD Privileged Identity Management sunduğu yaşam döngüsü yönetim özellikleri yararlanabilirsiniz.

Daha fazla bilgi için [Azure kaynakları için Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Erişim gözden geçirmeleri

**Türü:** yeni özellik  
**Hizmet kategorisi:** erişim gözden geçirmeleri  
**Ürün özelliği:** uyumluluk  

Kuruluşların grup üyeliklerini ve kurumsal uygulamalara erişimi etkili bir şekilde yönetmek için erişim gözden geçirmeleri (Önizleme) kullanabilirsiniz: 

- Konuk kullanıcıların uygulamalara ve grup üyeliklerine erişimlerine ait erişim gözden geçirmelerini kullanarak bu kullanıcıların erişimini yeniden onaylayabilirsiniz. Gözden geçirenler, verimli bir şekilde erişim gözden geçirmeleri tarafından sağlanan bilgiler dayalı olarak erişimi Konukları izin vermek isteyip devam karar verebilirsiniz.
- Erişim gözden geçirmeleri ile çalışanların uygulamalara erişimini ve grup üyeliklerini yeniden onaylayabilirsiniz.

Erişim gözden geçirmesi denetimlerini kuruluşunuza uygun programlarda toplayarak, uyumluluk veya riske duyarlı uygulamalar için gözden geçirmeleri takip edebilirsiniz.

Daha fazla bilgi için [Azure AD erişim gözden geçirmeleri](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Üçüncü taraf uygulamalardan uygulamalarım ve Office 365 uygulama başlatıcısında Gizle

**Türü:** yeni özellik  
**Hizmet kategorisi:** uygulamalarım  
**Ürün özelliği:** çoklu oturum açma  

Şimdi yeni bir aracılığıyla kullanıcılarınızın portalı üzerinde görünen uygulamalar daha iyi yönetebilirsiniz **uygulama Gizle** özelliği. Arka uç hizmetlerine veya yinelenen kutucukları ve dağınıklık kullanıcıların uygulama launchers uygulama kutucuklarına burada görünmesi durumlarda yardımcı olması için uygulamanızı gizleyebilirsiniz. İki durumlu düğme bulunduğu **özellikleri** üçüncü taraf uygulama bölümünü ve etiketli **kullanıcıya görünür?** Ayrıca PowerShell üzerinden program aracılığıyla uygulama gizleyebilirsiniz. 

Daha fazla bilgi için [üçüncü taraf bir uygulamanın Azure AD'de kullanıcı deneyiminden gizleme](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Kullanılabilir nedir?**

 Yeni Yönetici Konsolu, iki yeni API geçişi kapsamında günlüklerine Azure AD etkinlik almak için kullanılabilir. Yeni API'leri daha zengin filtreleme ve sıralama daha zengin denetim ve oturum açma etkinlikleri sağlamaya ek olarak işlevselliği sunmaktadır. Daha önce güvenlik raporları üzerinden kullanılabilen verilere artık Microsoft Graph kimlik koruması risk algılamaları API 'SI aracılığıyla erişilebilir.


## <a name="september-2017"></a>Eylül 2017

### <a name="hotfix-for-identity-manager"></a>Identity Manager için düzeltme

**Türü:** değiştirilen özellik  
**Hizmet kategorisi:** Identity Manager  
**Ürün özelliği:** kimlik yaşam döngüsü yönetimi  

Bir düzeltme dökümü paket (derleme 4.4.1642.0), 25 Eylül 2017 Identity Manager 2016 Service Pack 1'den itibaren kullanılabilir. Bu döküm paketi:

- Sorunları giderir ve geliştirmeleri içerir.
- Identity Manager 2016 için derleme 4.4.1459.0 kadar tüm Identity Manager 2016 Service Pack 1 güncelleştirmeleri değiştirir toplu bir güncelleştirmesidir. 
- Identity Manager 2016 4.4.1302.0 derleme olmasını gerektirir. 

Daha fazla bilgi için [Identity Manager 2016 Service Pack 1 için düzeltme paketi (derleme 4.4.1642.0) kullanılabilir](https://support.microsoft.com/help/4021562). 

---
