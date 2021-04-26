---
title: Yenilikler Sürüm notları-Azure Active Directory | Microsoft Docs
description: Azure Active Directory yenilikleri öğrenin; en son sürüm notları, bilinen sorunlar, hata düzeltmeleri, kullanım dışı işlevsellik ve yaklaşan değişiklikler gibi.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a909d946b55ee8b06d68aa8bee53bc50d2190e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532309"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory yenilikler nelerdir?

>Bu URL 'YI kopyalayarak ve yapıştırarak bu sayfanın ne zaman yeniden ziyaret ettikleridir hakkında bildirim alın: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` ![ RSS akışı okuyucu simgesi ](./media/whats-new/feed-icon-16x16.png) akış okuyucusuna.

Azure AD, iyileştirmeleri sürekli olarak alır. En son gelişmelerden haberdar olmak için, bu makalede hakkında bilgi verilmektedir:

- En son yayınlar
- Bilinen sorunlar
- Hata düzeltmeleri
- Kullanım dışı işlevsellik
- Değişiklik planları

Bu sayfa aylık olarak güncelleştirildiğinden düzenli olarak yeniden ziyaret edin. Altı aydan eski olan öğeleri arıyorsanız, [Azure Active Directory yenilikleri Için arşiv](whats-new-archive.md)halinde bulabilirsiniz.

---

## <a name="march-2021"></a>Mart 2021

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Yakında Azure AD TLS 1.0/1.1 kullanım dışı bırakma için hazırlanmaya yönelik ortamınızda TLS 1,2 desteğinin nasıl etkinleştirileceği hakkında rehberlik

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** yok  
**Ürün yeteneği:** Standartlarına

Azure Active Directory 30 Haziran 2021 ' den itibaren Azure Active Directory dünya çapındaki bölgelerde aşağıdaki protokolleri kullanımdan kaldırır:


- TLS 1.0
- TLS 1.1
- 3DES şifre paketi (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Etkilenen ortamlar şunlardır:

- Azure ticari bulut
- Office 365 GCC ve WW

Ek rehberlik için, [Azure AD TLS 1,1 ve 1,0 kullanım dışı için ORTAMıNıZDA TLS 1,2 desteğini etkinleştirme](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)bölümüne bakın.

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Genel Önizleme-Azure AD Yetkilendirme Yönetimi artık çok coğrafi coğrafi SharePoint Online 'ı destekliyor

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 
Çok coğrafi coğrafi SharePoint Online kullanan kuruluşlar için artık belirli çok coğrafi bilgisayarlı ortamları, Yetkilendirme Yönetimi erişim paketlerinize ekleyebilirsiniz. [Daha fazla bilgi edinin](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview).

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Genel Önizleme-Uygulama kayıtları silinen uygulamaları geri yükleme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Geliştirici deneyimi
 
Müşteriler artık Azure portal silinen uygulama kayıtlarını görüntüleyebilir, geri yükleyebilir ve kalıcı olarak kaldırabilir. Bu, yalnızca bir dizinle ilişkili uygulamalar için geçerlidir, kişisel bir Microsoft hesabı uygulamalar değildir. [Daha fazla bilgi edinin](../develop/quickstart-restore-app.md).
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Genel Önizleme-cihazları kaydetmek veya katılmak için koşullu erişimde yeni "Kullanıcı eylemi"

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
 Koşullu erişim bölümünde "cihazları Kaydet veya Birleştir" adlı yeni bir kullanıcı eylemi mevcuttur. Bu Kullanıcı eylemi, Azure AD cihaz kaydı için Multi-Factor Authentication (MFA) ilkelerini denetlemenize olanak tanır. 

Şu anda, bu kullanıcı eylemi yalnızca kullanıcılar cihazları kaydederken veya Azure AD 'ye birleştirmekte olan MFA 'yı bir denetim olarak etkinleştirmenizi sağlar. Azure AD cihaz kaydına bağımlı olan veya buna bağlı olan diğer denetimler bu kullanıcı eylemiyle devre dışı bırakıldı. [Daha fazla bilgi edinin](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Genel Önizleme-en yakın uygulama proxy 'Si bulut hizmetini kullanmak için bağlayıcı gruplarını Iyileştirme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control
 
Bu yeni özellik sayesinde bağlayıcı grupları, bir uygulamanın barındırıldığı en yakın bölgesel uygulama proxy hizmetine atanabilir. Bu, uygulamaların ana kiracının bölgesi dışındaki bölgelerde barındırıldığı senaryolarda uygulama performansını iyileştirebilir. [Daha fazla bilgi edinin](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Genel Önizleme-e-posta One-Time geçiş kodu hesapları kullanarak AAD 'de kaydolma Self-Service

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C

Dış kullanıcılar artık Azure AD 1. taraf ve LOB uygulamalarında oturum açmak için e-posta One-Time geçiş kodu hesaplarını kullanabiliyor. [Daha fazla bilgi edinin](../external-identities/one-time-passcode.md).

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Genel Önizleme-Azure AD 'de AD FS Sign-Ins kullanılabilirliği

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** & raporlamayı izleme
 
AD FS oturum açma etkinliği artık Azure AD etkinlik raporlama ile tümleştirilebilir, karma kimlik altyapısının birleştirilmiş bir görünümünü sağlar. Azure AD Sign-Ins raporu, Log Analytics ve Azure Izleyici çalışma kitaplarını kullanarak, hem AD FS AAD 'nin hem de AD FS hesabı kilitlenmelerinin, hatalı parola denemelerinde ve beklenmedik oturum açma denemelerinin olumsuz artışlarına yönelik derinlemesine analizler gerçekleştirmek mümkündür.

Daha fazla bilgi edinmek için, [Connect Health Ile Azure AD 'de oturum açma AD FS](../hybrid/how-to-connect-health-ad-fs-sign-in.md)ziyaret edin.

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Bulut kimlik doğrulaması için genel kullanılabilirlik-hazırlanmış dağıtım

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** AD Connect  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Bulut kimlik doğrulamasına yönelik hazırlanmış dağıtım artık genel kullanıma sunulmuştur. Hazırlanan dağıtım özelliği, Kullanıcı gruplarını, geçiş kimlik doğrulaması (PTA) veya parola karması eşitleme (PHS) gibi bulut kimlik doğrulama yöntemleriyle seçmeli olarak test etmenize olanak tanır. Diğer bir deyişle, Federasyon etki alanlarındaki diğer tüm kullanıcılar, kullanıcıların kimliğini doğrulamak için AD FS veya başka bir Federasyon hizmeti gibi Federasyon hizmetlerini kullanmaya devam eder. [Daha fazla bilgi edinin](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Genel kullanılabilirlik-Kullanıcı türü özniteliği artık Azure yönetim portalı 'nda güncelleştirilebilen olabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kullanıcı deneyimi ve yönetimi  
**Ürün yeteneği:** Kullanıcı Yönetimi
 
Müşteriler artık Azure yönetici portalından Kullanıcı profili bilgilerini güncelleştirdiklerinde Azure AD kullanıcılarının kullanıcı türünü güncelleştirebilir. Kullanıcı türü de Microsoft Graph güncelleştirilebilen olabilir. Daha fazla bilgi için bkz. [Kullanıcı profili bilgilerini ekleme veya güncelleştirme](active-directory-users-profile-azure-portal.md).
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Genel kullanılabilirlik-Azure Active Directory Domain Services için çoğaltma kümeleri

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services
 
Azure AD DS 'de çoğaltma kümelerinin yeteneği artık genel kullanıma sunulmuştur. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Genel kullanılabilirlik-Azure Kamu bulutunda e-posta One-Time geçiş kodu kullanarak iş ortaklarınızla Işbirliği yapın

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Microsoft Azure Kamu buluttaki kuruluşlar artık konuklarının e-posta One-Time geçiş kodu ile davetleri kullanmalarına olanak sağlayabilir. Bu, Azure Kamu bulutunda Azure AD, Microsoft veya Gmail hesabı olmayan tüm konuk kullanıcıların, paylaşılan kaynaklarda oturum açmak üzere geçici bir kod isteyerek ve girerek iş ortaklarıyla işbirliği yapabilmesini sağlar. [Daha fazla bilgi edinin](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Mart 2021

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Şu Mart 2021 ' de, federasyon desteğiyle App Gallery 'de aşağıdaki 37 yeni uygulamaları ekledik:

[Bambuser Live video alışverişi](https://lcx.bambuser.com/), [deepdyve Inc](https://www.deepdyve.com/azure-sso), [Moqups](../saas-apps/moqups-tutorial.md), [RICOH Spaces mobil](https://ricohspaces.app/welcome), [flipgrid](https://auth.flipgrid.com/), [Hcaptcha Enterprise](../saas-apps/hcaptcha-enterprise-tutorial.md), [SchoolStream asa](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [transkusursuz GlobalLink panosu](../saas-apps/transperfect-globallink-dashboard-tutorial.md) [SimplificaCI](https://app.simplificaci.com.br/), [Misyonumuz LXP](../saas-apps/thrive-lxp-tutorial.md), [lexonin TalentScape](../saas-apps/lexonis-talentscape-tutorial.md), [exium](../saas-apps/exium-tutorial.md), [sapient](../saas-apps/sapient-tutorial.md), [truechoice](../saas-apps/truechoice-tutorial.md), [RICOH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Acunetix 360](../saas-apps/acunetix-360-tutorial.md), [exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub kurumsal yönetilen kullanıcısı](../saas-apps/github-enterprise-managed-user-tutorial.md), [Outlook için Kurumsal Vault.Cloud](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [smartlook](../saas-apps/smartlook-tutorial.md), [Accenture akademik My](../saas-apps/accenture-academy-tutorial.md), [OnShape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [securitystudio](../saas-apps/securitystudio-tutorial.md), et [verileri](https://app.clicdata.com/), [etgreen](../saas-apps/evergreen-tutorial.md), [patchdestesi](https://patchdeck.com/ad_auth/authenticate/), [Faks. Ayrıca](../saas-apps/fax.plus-tutorial.md), [validsign](../saas-apps/validsign-tutorial.md), [AWS çoklu oturum açma](../saas-apps/aws-single-sign-on-tutorial.md), [Nura Space](https://dashboard.nuraspace.com/login), [Broadcom DX SaaS](../saas-apps/broadcom-dx-saas-tutorial.md), [Interplay Learning](https://skilledtrades.interplaylearning.com/#login), [sendpro Enterprise](../saas-apps/sendpro-enterprise-tutorial.md), [fortisase sıya](../saas-apps/fortisase-sia-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz: https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Mart 2021

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [AWS çoklu oturum açma](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub kurumsal yönetilen Kullanıcı](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Şirket markası için MS Graph API 'ye giriş

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** MS grafiği  
**Ürün yeteneği:** B2B/B2C

[Şirket markalaması Için MS Graph API](https://docs.microsoft.com/graph/api/resources/organizationalbrandingproperties)  , Azure AD veya Microsoft 365 oturum açma deneyiminde, marka parametrelerinin programlı olarak yönetimine izin vermek için kullanılabilir.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Uygulama proxy 'Si ile genel kullanılabilirlik-üst bilgi tabanlı kimlik doğrulama SSO 'SU

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control
 
Üst bilgi tabanlı kimlik doğrulaması için Azure AD Uygulama Ara Sunucusu yerel desteği genel kullanıma sunuldu. Bu özellikle, dağıtım için gerekli ek bileşenler olmadan uygulama için HTTP üstbilgileri olarak gereken kullanıcı özniteliklerini yapılandırabilirsiniz. [Daha fazla bilgi edinin](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>MFA sunucusu için iki yönlü SMS artık desteklenmiyor

**Şunu yazın:** Kullanım dışı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 

MFA sunucusu için iki yönlü SMS, başlangıçta 2018 ' de kullanımdan kaldırılmıştır ve 24 Şubat 2021 ' den sonra desteklenmeyecektir. Yöneticiler, hala iki yönlü SMS kullanan kullanıcılar için başka bir yöntemi etkinleştirmelidir.

E-posta bildirimleri ve Azure portalı hizmet durumu bildirimleri, 8 Aralık 2020 ve 28 Ocak 2021 tarihinde etkilenen yöneticilere gönderilmiştir. Uyarılar, aboneliğe bağlı olan sahip, Ikincil sahip, yönetici ve hizmet yöneticisi RBAC rollerine gitti. [Daha fazla bilgi edinin](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>Şubat 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Varsayılan olarak bir kerelik geçiş kodu kimlik doğrulamasını e-posta 2021 tarihinde başlayarak

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 

31 Ekim 2021 ' den itibaren Microsoft Azure Active Directory [e-posta bir kerelik geçiş kodu kimlik doğrulaması](../external-identities/one-time-passcode.md) , B2B işbirliği senaryolarına yönelik hesapları ve kiracılar için davet etme varsayılan yöntemi olur. Bu sırada, Microsoft yönetilmeyen Azure Active Directory hesapları kullanılarak davetlerin kullanımına izin vermez. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>İstekte bulunulmamış ancak onaylanan izinler, koşullu erişimi tetikleytiklerinde belirteçlere artık eklenmeyecektir

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Platformunun
 
Şu anda, [dinamik izinleri](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) kullanan uygulamalara erişim izni verilen tüm izinler verilir. Bu, istekte bulunulmamış ve koşullu erişimi tetiklerse bile uygulamaları içerir. Örneğin, bu, yalnızca onayı bulunan bir uygulamanın `user.read` `files.read` , Izin atanmış koşullu erişimi geçirmeye zorlanmasına neden olabilir `files.read` . 

Gereksiz koşullu erişim istemlerinin sayısını azaltmak için, Azure AD, istenmeyen kapsamların uygulamalara sağlandığı yöntemi değiştiriyor. Uygulamalar, yalnızca açıkça istediği izin için koşullu erişimi tetikler. Daha fazla bilgi için, [kimlik doğrulamadaki yenilikleri](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)okuyun.
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Genel Önizleme-passwordless kimlik bilgilerini kaydetmek için geçici bir erişim geçişi kullanın

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Geçici erişim geçişi, güçlü kimlik bilgileri görevi gören ve bir kullanıcı güçlü kimlik doğrulama faktörünü (örneğin, FIDO2 güvenlik anahtarı veya Microsoft Authenticator) kaybettiği veya unuttuğunda ve yeni güçlü kimlik doğrulama yöntemlerini kaydetmek için oturum açması gerektiğinde, daha az kimlik bilgileri ve kurtarmaya izin veren zaman sınırlı bir geçiş kodu olur. [Daha fazla bilgi edinin](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Genel Önizleme-yeni nesil Kullanıcı akışlarındaki Oturumumu Açık bırak (KMSı)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C

Yeni nesil B2C Kullanıcı akışları, müşterilerin kalıcı bir tanımlama bilgisi kullanarak Web ve yerel uygulamalarının kullanıcıları için oturum ömrünü genişletmelerine olanak sağlayan Oturumumu [açık tut (KMSı)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) işlevini desteklemektedir.  özelliği, kullanıcı tarayıcıyı kapatıp yeniden açtığında bile oturumu etkin tutar ve Kullanıcı oturumunu kapattığında iptal edilir.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Genel Önizleme-dış kimlikler Self-Service MSA hesaplarını kullanarak AAD 'de kaydolma

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Dış kullanıcılar artık Azure AD birinci taraf ve LOB uygulamalarında oturum açmak için Microsoft hesaplarını kullanabilir. [Daha fazla bilgi edinin](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Genel Önizleme-Konuk Kullanıcı için kullanım durumunu sıfırlama

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Müşteriler artık mevcut dış Konuk kullanıcıları, kullanım durumlarını sıfırlamak için yeniden davet edebilir ve bu da Konuk Kullanıcı hesabının herhangi bir erişimi kaybetmeden kalmasına izin verir. [Daha fazla bilgi edinin](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Genel Önizleme-/Synchronization (sağlama) API 'Leri artık uygulama izinlerini destekliyor

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Müşteriler artık, eşitleme API 'Lerini çağırmak için uygulama izni olarak Application. ReadWrite. ownedcan kullanabilir. Bu, yalnızca Azure AD 'den üçüncü taraf uygulamalara (örneğin, AWS, veri blokları, vb.) sağlanması için desteklenir. Bu, şu anda HR sağlama (Workday/başarılı faktörleri) veya bulut eşitleme (AD ile Azure AD) için desteklenmiyor. [Daha fazla bilgi edinin](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Genel kullanılabilirlik-kimlik doğrulama Ilkesi yönetici yerleşik rolü

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
Bu role sahip kullanıcılar, kimlik doğrulama yöntemleri ilkesini, kiracı genelinde MFA ayarlarını ve parola koruma ilkesini yapılandırabilir. Bu rol, parola koruma ayarlarını yönetme izni verir: akıllı kilitleme yapılandırması ve özel yasaklanmış parolalar listesi güncelleştiriliyor. [Daha fazla bilgi edinin](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Genel kullanılabilirlik-uygulamalarımın üzerindeki Kullanıcı koleksiyonları artık kullanılabilir!

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulamalarım  
**Ürün yeteneği:** Son Kullanıcı deneyimleri
 
Kullanıcılar artık uygulamalarım uygulama başlatıcısı üzerinde kendi uygulama gruplandırmaları oluşturabilir. Ayrıca, yöneticileri tarafından kendileriyle paylaşılan koleksiyonları yeniden sıralayabilir ve saklayabilir. [Daha fazla bilgi edinin](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Genel kullanılabilirlik-Authenticator içinde otomatik doldurma

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Microsoft Authenticator uygulaması  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Microsoft Authenticator, çok faktörlü kimlik doğrulaması (MFA) ve hesap yönetimi özellikleri sağlar ve artık, sitelerde ve uygulamalarda kullanıcıların mobil (iOS ve Android) üzerinde ziyaret ettiği parolaları otomatik olarak alır. 

Kullanıcı doğrulayıcısı üzerinde otomatik doldurma kullanmak için, kullanıcıların kendi kişisel Microsoft hesabı kimlik doğrulayıcısına eklemesi ve parolalarını eşitlemesini sağlamak için kullanması gerekir. Şu anda parolaları eşitlemek için iş veya okul hesapları kullanılamaz. [Daha fazla bilgi edinin](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Genel kullanılabilirlik-dahili kullanıcıları B2B işbirliğine davet etme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Müşteriler artık iç konukları, mevcut bir iç hesaba davetiye göndermek yerine B2B işbirliği kullanacak şekilde davet edebilir. Bu, müşterilerin bu kullanıcının nesne KIMLIĞI, UPN, grup üyelikleri ve uygulama atamalarını tutmasını sağlar. [Daha fazla bilgi edinin](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Genel kullanılabilirlik-etki alanı adı yöneticisi yerleşik rolü

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
Bu role sahip kullanıcılar, etki alanı adlarını yönetebilir (okuyabilir, ekleyebilir, doğrulayamaz, güncelleştirebilir ve silebilir). Kullanıcılar, gruplar ve uygulamalar hakkında, bu nesnelerin etki alanı bağımlılıkları olduğu için dizin bilgilerini de okuyabilir. 

Şirket içi ortamlarda, bu role sahip olan kullanıcılar, ilgili kullanıcıların her zaman şirket içinde kimlik doğrulamasından geçirilmeleri için, Federasyon etki alanı adlarını yapılandırabilir. Bu kullanıcılar daha sonra Azure AD tabanlı hizmetlerde çoklu oturum açma yoluyla şirket içi parolalarla oturum açabilirler. Federasyon ayarlarının Azure AD Connect aracılığıyla eşitlenmesi gerekir, böylece kullanıcıların Azure AD Connect yönetme izinleri de vardır. [Daha fazla bilgi edinin](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Şubat 2021

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Şubat 2021 ' de, federasyon desteğiyle App Gallery 'de aşağıdaki 37 yeni uygulamaları ekledik:

[Messenger uzantısı](https://loopworks.com/loop-flow-messenger/), [Silverfort Azure AD bağdaştırıcısı](http://www.silverfort.com/), [Karşılıklı öğrenme](https://skilledtrades.interplaylearning.com/#login), [Nura Space](https://dashboard.nuraspace.com/login), [Yoml AB](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPRESYA](https://uxpressia.com/users/sign-in), Pose [ön ve ekleme platformu, pobotd](http://app.introdus.dk/login), [hanaksıd](https://app.leaksid.com/) [,](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All) [kaydırıcı](http://www.shiftwizard.com/), [pingflow SSO](https://app.pingview.io/), [swiftlane](https://admin.swiftlane.com/login), [quasydoc SSO](https://www.quasydoc.eu/login), [Fenwick altın hesap](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [öğrennsoft LMS & TMS](http://www.learnsoft.com/), [P-TH +](https://p-th.jp/), [myviewboard](https://api.myviewboard.com/auth/microsoft/), [Tartabıt IoT Köprüsü](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot-otomobıl park yönetimi](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB düşünme noktası](../saas-apps/hsb-thoughtspot-tutorial.md), [ibmid](../saas-apps/ibmid-tutorial.md), [sharingcloud](../saas-apps/sharingcloud-tutorial.md), [poolparti anlam paketi](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung KNOX ve iş hizmetleri](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis-ölçeklendirme çevik platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [maptician](../saas-apps/maptician-tutorial.md), [olfeo SaaS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [cloudknox izinleri yönetim platformu](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md), [enablon](../saas-apps/enablon-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz: https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Şubat 2021

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [Merhaba kimliği](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Daha fazla bilgi için [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatik hale](../app-provisioning/user-provisioning.md)getirmeyi okuyun.

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Genel kullanılabilirlik-10 Azure Active Directory roller artık yeniden adlandırıldı

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
10 Azure AD yerleşik rolleri, [Microsoft 365 Yönetim Merkezi](/microsoft-365/admin/microsoft-365-admin-center-preview), [Azure AD portalı](https://portal.azure.com/)ve [Microsoft Graph](https://developer.microsoft.com/graph/)hizalanabilmesi için yeniden adlandırıldı. Yeni roller hakkında daha fazla bilgi edinmek için [Azure Active Directory Içindeki yönetici rolü izinleri](../roles/permissions-reference.md#all-roles)bölümüne bakın.

![MS Graph API rol adlarını ve Azure portal ve önerilen son adı API, Azure portal ve Mac arasında gösteren tablo.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>MFA/SSPR Birleşik kaydında yeni şirket markası

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kullanıcı deneyimi ve yönetimi  
**Ürün yeteneği:** Son Kullanıcı deneyimleri
 
Geçmişte, şirket logoları Azure Active Directory oturum açma sayfalarında kullanılmadı. Şirket markası, artık MFA/SSPR Birleşik kaydının sol üst kısmında bulunur. Şirket markası, Sign-Ins ve güvenlik bilgisi sayfasına da dahildir. [Daha fazla bilgi edinin](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Genel kullanılabilirlik-Ikinci düzey yönetici, alternatif onaylayan olarak ayarlanabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 
Onaylayanlar ' i seçtiğinizde, artık yetkilendirme yönetiminde ek bir seçenek mevcuttur. Ilk onaylayanın "onaylayan olarak yönetici" seçeneğini belirlerseniz, alternatif onaylayan alanında seçebileceğiniz başka bir "Ikinci düzey yönetici, alternatif onaylayan olarak" seçeneğine sahip olursunuz. Bu seçeneği belirlerseniz, sistemin ikinci düzey yöneticiyi bulamadığı durumlarda isteği iletmek için bir geri dönüş onaylayan eklemeniz gerekir. [Daha fazla bilgi edinin](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Kimlik doğrulama yöntemleri etkinlik panosu

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 

Yenilenen kimlik doğrulama yöntemleri etkinlik panosu, yöneticilere kiracısındaki kimlik doğrulama yöntemi kaydı ve kullanım etkinliğine ilişkin bir genel bakış sağlar. Rapor, her bir yöntem için kayıtlı kullanıcı sayısını ve oturum açma ve parola sıfırlama sırasında hangi yöntemlerin kullanılacağını özetler. [Daha fazla bilgi edinin](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Yenileme ve oturum belirteci yaşam süreleri yapılandırılabilir belirteç ömrü (CTL) ile kullanım dışı bırakıldı

**Şunu yazın:** Kullanım dışı  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Yenileme ve oturum belirteci yaşam süresi yapılandırması, CTL 'nin kullanım dışı bırakıldı. Azure Active Directory var olan ilkelerde artık yenileme ve oturum belirteci yapılandırması yok. [Daha fazla bilgi edinin](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>Ocak 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Sağlama yapılandırılırken gizli belirteç zorunlu bir alan olacaktır

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Geçmişte, özel/KCG uygulamasında sağlama ayarlanırken gizli dizi belirteci alanı boş tutulabilir. Bu işlev yalnızca test için kullanılmak üzere tasarlanmıştır. Alanı gerekli hale getirmek için Kullanıcı arabirimini güncelleştireceğiz. 

Müşteriler, tarayıcı URL 'sindeki bir özellik bayrağını kullanarak test amacıyla bu gereksinimi geçici olarak çözebilirler. [Daha fazla bilgi edinin](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Genel Önizleme-Android paylaşılan cihazlarını, en önde bulunan çalışanlar için ölçeklendirerek özelleştirin ve yapılandırın

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Cihaz kaydı ve yönetimi  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Azure AD ve Microsoft Uç Nokta Yöneticisi ekipleri, saflarda çalışan cihazlarınızı özelleştirme, ölçeklendirme ve güvenli hale getirmeye yönelik özelliği birleştirmelidir.

Aşağıdaki Önizleme özellikleri şunları yapmanıza olanak sağlayacak:
- Microsoft Endpoint Manager ile Android paylaşılan cihazlarını ölçekli olarak sağlama
- Cihaz tabanlı koşullu erişim kullanarak vardiya çalışanları için erişiminizi güvenli hale getirme
- Yönetilen giriş ekranı ile vardiya çalışanları için oturum açma deneyimlerini özelleştirin

Daha fazla bilgi edinmek için bkz. [saflarda çalışanları için paylaşılan cihazları ölçeklendirerek özelleştirme ve yapılandırma](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Genel Önizleme-sağlama günlükleri artık CSV veya JSON olarak indirilebilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Müşteriler, sağlama günlüklerini Kullanıcı arabirimi ve Graph API aracılığıyla bir CSV veya JSON dosyası olarak indirebilir. Daha fazla bilgi edinmek için [Azure Active Directory portalındaki sağlama raporları](../reports-monitoring/concept-provisioning-logs.md)bölümüne bakın.

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Genel Önizleme-bulut gruplarını Azure AD özel rollerine ve yönetici birimi kapsamlı rollere atayın

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
Müşteriler, bir bulut grubunu Azure AD özel rollerine veya bir yönetici birimi kapsamlı rolüne atayabilir. Bu özelliği nasıl kullanacağınızı öğrenmek için [Azure Active Directory ' de rol atamalarını yönetmek üzere bulut gruplarını kullanma](../roles/groups-concept.md)konusuna bakın.

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Genel kullanılabilirlik-Azure AD Connect bulut eşitlemesi (daha önce bulut sağlama olarak bilinirdi)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD Connect bulut eşitleme  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Azure AD Connect bulut eşitlemesi artık tüm müşteriler tarafından genel kullanıma sunulmuştur.

Azure AD Connect bulutu, dönüştürme mantığının büyük bir şekilde buluta taşınması ve şirket içi parmak izinizi azaltır. Ayrıca, daha fazla eşitleme kullanılabilirliği için birden çok hafif aracı dağıtımı vardır. [Daha fazla bilgi edinin](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Genel kullanılabilirlik-saldırı simülasyonu Yöneticisi ve saldırı yükü yazarı yerleşik roller

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
Role-Based Access Control içindeki iki yeni rol, kullanıcılara, saldırı simülasyonu yöneticisine ve saldırı yükü yazarına atamak için kullanılabilir. 

[Saldırı simülasyonu yönetici](../roles/permissions-reference.md#attack-simulation-administrator) rolündeki kullanıcıların Kiracıdaki tüm benzetimler için erişimi vardır ve şunları yapabilir:
- saldırı simülasyonu oluşturma 'nın tüm yönlerini oluşturun ve yönetin
- simülasyonu başlatma/zamanlama
-  simülasyon sonuçlarını gözden geçirin. 

[Saldırı yükü yazar](../roles/permissions-reference.md#attack-payload-author) rolündeki kullanıcılar, saldırı yükleri oluşturabilir, ancak bunu gerçekten başlatamaz veya zamanlayamaz. Böylece saldırı yükleri, kiracının benzetim oluşturmak için kullanabilecekleri tüm yöneticiler tarafından kullanılabilir.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Genel kullanılabilirlik-Kullanım Özeti raporları okuyucu yerleşik rolü

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
Kullanım Özeti raporları okuyucu rolüne sahip kullanıcılar, kullanım ve üretkenlik puanı için Microsoft 365 Yönetim Merkezi 'ndeki kiracı düzeyindeki toplu verilere ve ilişkili öngörülere erişebilir. Ancak, herhangi bir Kullanıcı düzeyi ayrıntılarına veya öngörülere erişemez. 

İki rapor için Microsoft 365 Yönetim merkezinde, kiracı düzeyi toplanmış veriler ve Kullanıcı düzeyi ayrıntıları arasında ayrım yaptık. Bu rol, bireysel kullanıcı tarafından tanımlanabilen verilere ek bir koruma katmanı ekler. [Daha fazla bilgi edinin](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Genel kullanılabilirlik-Azure AD koşullu erişim 'de uygulama koruma ilkesi izni ıste

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
"Uygulama koruma ilkesi gerektir" için Azure AD koşullu erişim izni artık GA. 

İlke aşağıdaki özellikleri sağlar:
- Yalnızca Intune uygulama korumasını destekleyen bir mobil uygulama kullanılırken erişime izin verir
- Yalnızca bir Kullanıcı mobil uygulamaya teslim edilen bir Intune uygulama koruma ilkesine sahip olduğunda erişime izin verir

[Burada](../conditional-access/app-protection-based-conditional-access.md)uygulama koruması için bir koşullu erişim ilkesi ayarlama hakkında daha fazla bilgi edinin.
 
---

### <a name="general-availability---email-one-time-passcode"></a>Genel kullanılabilirlik-e-posta One-Time geçiş kodu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
E-posta OTP, dünyanın dört bir yanındaki kuruluşların e-posta yoluyla bağlantı veya davetiye göndererek kişilerle işbirliği yapmasına olanak sağlar. Davet edilen kullanıcılar kendi iş ortaklarının kaynaklarına erişmek için e-postalarına gönderilen tek seferlik geçiş kodu ile kimliklerini doğrulayabilirler. [Daha fazla bilgi edinin](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Ocak 2021

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templamy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Daha fazla bilgi için bkz. [Azure AD 'de otomatik SaaS uygulaması Kullanıcı hazırlama nedir?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Ocak 2021

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Ocak 2021 ' de, federasyon desteğiyle uygulama galerimize aşağıdaki 29 yeni uygulamayı ekledik:

[Myscview](https://dev.myscview.com/), [talentech](https://talentech.com/contact/), [bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [IO](https://app.m.io/auth/install/microsoft?scopetype=hub), [sovelto Easy](https://login.soveltoeasy.fi/), [SUPPORTBENCH](https://account.supportbench.net/agent/login/),[biensin](https://formation.bienvenue.pro/login)işleme, [Aida sağlık SSO](https://aidaforparents.com/login/organizations), [Uluslararası SOS yardım ürünleri](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [lablog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [ePhoto Dam](../saas-apps/ephoto-dam-tutorial.md), [kavram](../saas-apps/notion-tutorial.md), [syndio](../saas-apps/syndio-tutorial.md), [sarılo Enterprise](../saas-apps/yello-enterprise-tutorial.md), [TimeClock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [NALCO E-Data](https://www.ecolab.com/), [boş konum filler](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), [Synerıse AI büyüme ekosistemi](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [zva Data Security](../saas-apps/imperva-data-security-tutorial.md), [Illusive Networks](../saas-apps/illusive-networks-tutorial.md), [proware](../saas-apps/proware-tutorial.md), [splan ziyaretçisi](../saas-apps/splan-visitor-tutorial.md), [Aruba Kullanıcı deneyimi öngörüleri](../saas-apps/aruba-user-experience-insight-tutorial.md), [contentsquare SSO](../saas-apps/contentsquare-sso-tutorial.md), [çevre 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz. https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Genel Önizleme-Ikinci düzey yönetici, alternatif onaylayan olarak ayarlanabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 
Onaylayanlar ' i seçtiğinizde, artık yetkilendirme yönetiminde ek bir seçenek mevcuttur. Ilk onaylayanın "onaylayan olarak yönetici" seçeneğini belirlerseniz, alternatif onaylayan alanında seçebileceğiniz başka bir "Ikinci düzey yönetici, alternatif onaylayan olarak" seçeneğine sahip olursunuz. Bu seçeneği belirlerseniz, sistemin ikinci düzey yöneticiyi bulamadığı durumlarda isteği iletmek için bir geri dönüş onaylayan eklemeniz gerekir. [Daha fazla bilgi edinin](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Genel kullanılabilirlik-doğrudan erişim portalından takımlara git

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 
Artık takımları doğrudan erişim portalından başlatabilirsiniz. 

Bunu yapmak için erişimimde oturum açın ( https://myaccess.microsoft.com/) "erişim paketlerine" gidin, daha sonra erişim sahibi olduğunuz tüm erişim paketlerini görmek için "etkin" sekmesine gidin. Seçili erişim paketini genişlettikten ve takımlar üzerine geldiğinizde, "Aç" düğmesine tıklayarak bunu başlatabilirsiniz. [Daha fazla bilgi edinin](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Riskli Konuk kullanıcılar için iyileştirilmiş günlük & End-User Istemleri

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 

Riskli Konuk kullanıcılar için günlüğe kaydetme ve End-User Istemleri güncelleştirildi. [Kimlik koruması ve B2B kullanıcıları](../identity-protection/concept-identity-protection-b2b.md)hakkında daha fazla bilgi edinin.
 
---
 
## <a name="december-2020"></a>Aralık 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Genel Önizleme-yerleşik Ilke kullanarak telefon kayıt ve oturum açma Azure AD B2C

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 
B2C telefon kayıt ve oturum açma, yerleşik Ilke kullanarak BT yöneticileri ve kuruluş geliştiricilerinin, son kullanıcılarınızın Kullanıcı akışlarında telefon numarası kullanarak oturum açmasını ve kaydolmasına izin vermesini sağlar. Daha fazla bilgi edinmek için [Kullanıcı akışları (Önizleme) için telefon kaydı ve oturum açma ayarlama](../../active-directory-b2c/phone-authentication-user-flows.md) makalesini okuyun.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Genel kullanılabilirlik-güvenlik Varsayılanları artık varsayılan olarak tüm yeni kiracılar için etkinleştirilmiştir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Kullanıcı hesaplarını korumak için, 12 Kasım 2020 tarihinde veya sonrasında oluşturulan tüm yeni kiracılar güvenlik Varsayılanları etkin olacak şekilde gelir. Güvenlik Varsayılanları aşağıdakiler dahil birden çok ilkeyi zorunlu tutar:
- Tüm kullanıcıların ve yöneticilerin Microsoft Authenticator uygulamasını kullanarak MFA 'ya kaydolmanızı gerektirir
- Her oturum açtıklarında MFA 'yı kullanmak için kritik yönetici rolleri gerektirir. Tüm diğer kullanıcılara her gerektiğinde MFA sorulur. 
- Eski kimlik doğrulama, kiracı genelinde engellenecek. 

Daha fazla bilgi için [güvenlik varsayılanlarını nelerdir?](../fundamentals/concept-fundamentals-security-defaults.md) bölümünü okuyun.

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Genel kullanılabilirlik-AADConnect 'te en fazla 250 k üye içeren gruplar için destek

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** AD Connect  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Microsoft, Azure Active Directory için eşitleme hizmeti işlemlerinin performansını geliştiren Azure AD Connect için yeni bir uç nokta (API) dağıttı. Yeni [v2 uç noktasını](../hybrid/how-to-connect-sync-endpoint-api-v2.md)kullandığınızda, Azure AD 'ye dışarı aktarma ve içeri aktarma sırasında dikkat çekici performans kazançları ile karşılaşırsınız. Bu yeni uç nokta aşağıdaki senaryoları destekler:

- En fazla 250 k üye içeren grupları eşitleme
- Azure AD 'ye verme ve içeri aktarma ile ilgili performans artışı

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Genel kullanılabilirlik-Azure Çin bulutundaki kiracılar için kullanılabilir Yetkilendirme Yönetimi

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 

Yetkilendirme yönetiminin özellikleri artık Azure Çin bulutundaki tüm kiracılar için kullanılabilir. Bilgi için [Identity idare belgeleri](https://docs.azure.cn/zh-cn/active-directory/governance/) sitemizi ziyaret edin.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Aralık 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Dijital Süreç Otomasyonu için Bizagi Studio](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Aralık 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
2020 Aralık 'ta, federasyon desteğiyle App Gallery 'de aşağıdaki 18 yeni uygulamayı ekledik:

[Awarego](../saas-apps/awarego-tutorial.md), [hownow SSO](https://gethownow.com/), [Zylab tek bir yasal saklama](https://www.zylab.com/en/product/legal-hold), [gulayis](http://www.guider-ai.com/), Softpais, [Pims 365](http://www.omega365.com/pims), bilgi [dönüştürme](../saas-apps/informacast-tutorial.md), elde edilen [her ne kadar](https://www.softcrisis.se/sv/) [,](../saas-apps/retrievermediadatabase-tutorial.md)bir [işlem içi Pano](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs Bilgi Bankası](../saas-apps/proprofs-knowledge-base-tutorial.md), [Rightcrowd iş gücü yönetimi](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL Trıriga](../saas-apps/jll-tririga-tutorial.md), [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web uygulaması güvenlik duvarı](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn tatatzı](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federasyon uygulaması](../saas-apps/equinix-federation-app-tutorial.md), [kfadvance](../saas-apps/kfadvance-tutorial.md) [](../saas-apps/vonage-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz. https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Takımlara doğrudan erişim portalından git

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kullanıcı erişimi Yönetimi **ürün yeteneği:** Yetkilendirme Yönetimi

Artık takımları doğrudan erişim portalından başlatabilirsiniz. Bunu yapmak için [erişimimde](https://myaccess.microsoft.com/)oturum açın, erişim **paketleri**' ne gidin ve daha önce erişiminiz olan tüm erişim paketlerini görmek için **etkin** sekmesine gidin. Erişim paketini genişlettikten ve takımlar üzerine geldiğinizde, **Aç** düğmesine tıklayarak uygulamayı başlatabilirsiniz. 

Erişim Portalı 'nı kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure AD Yetkilendirme Yönetimi 'nde erişim paketine erişim isteği](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Genel Önizleme-Ikinci düzey yönetici, alternatif onaylayan olarak ayarlanabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi

Artık yetkilendirme yönetiminde onay sürecinde fazladan bir seçenek mevcuttur. Ilk onaylayan için yöneticiyi onaylayan olarak seçerseniz, alternatif onaylayan alanında tercih ettiğiniz Ikinci düzey yönetici olan alternatif onaylayan olarak başka bir seçeneğe sahip olursunuz. Bu seçeneği belirlediğinizde, sistemin ikinci düzey yöneticiyi bulamadığı durumlarda isteği iletmek için bir geri dönüş onaylayan eklemeniz gerekir.

Daha fazla bilgi için [Azure AD yetkilendirme yönetiminde bir erişim paketinin onay ayarlarını değiştirin](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)bölümüne gidin.

--- 

## <a name="november-2020"></a>Kasım 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 ve 3DES kullanımdan kaldırıldı

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Tüm Azure AD uygulamaları  
**Ürün yeteneği:** Standartlarına

Azure Active Directory 30 Haziran 2021 ' den itibaren Azure Active Directory dünya çapındaki bölgelerde aşağıdaki protokolleri kullanımdan kaldırır:

- TLS 1.0
- TLS 1.1
- 3DES şifre paketi (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Etkilenen ortamlar:
- Azure ticari bulut
- Office 365 GCC ve WW

İlgili duyuru tüm istemci-sunucu ve tarayıcı-sunucu birleşimleri, Azure, Office 365 ve Microsoft 365 hizmetleri için Azure Active Directory güvenli bir bağlantı sağlamak üzere TLS 1,2 ve modern şifre paketleri kullanmalıdır. Bu değişiklik [Azure ACTIVE DIRECTORY TLS 1,0 & 1,1 ve 3Des şifre paketi US gov bulutuna kullanımdan kalkmasıyla](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud)ilgilidir.

Kullanım dışı protokol bağımlılıklarını kaldırma kılavuzu için lütfen [ORTAMıNıZDAKI tls 1,2 desteğini etkinleştirmek Için Azure AD TLS 1,1 ve 1,0](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)kullanım dışı bölümüne bakın.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Kasım 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

2020 Kasım ayında, federasyon desteğiyle App Gallery 'de aşağıdaki 52 yeni uygulamaları ekledik:

[Seyahat & gider yönetimi](https://app.expenseonce.com/Account/Login), [tribeloo](../saas-apps/tribeloo-tutorial.md), [itslearning dosya Seçicisi](https://pmteam.itslearning.com/), [crit denetimi](../saas-apps/crises-control-tutorial.md), [courtalert](https://www.courtalert.com/), [stealthmail](https://stealthmail.com/), [edmentum-araştırma Adası](https://app.studyisland.com/cfw/login/), [sanal risk yöneticisi](../saas-apps/virtual-risk-manager-tutorial.md), [timu](../saas-apps/timu-tutorial.md), [Looker analiz platformu](../saas-apps/looker-analytics-platform-tutorial.md) [talview-stait](https://recruit.talview.com/login), gerçek zamanlı çevirici, [Klaxoon](https://access.klaxoon.com/login), [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [netmini yönetim kurumsal](../saas-apps/netsparker-enterprise-tutorial.md), [en-Trak kiracı deneyimi platformu](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [builterra](https://portal.builterra.com/), [filiz iade](https://my.evacheckin.com/organization)etme, [hownow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), VISE Identity, [lucıd (tüm ürünler)](../saas-apps/lucid-tutorial.md), [Gobright](https://portal.brightbooking.eu/) [,](../saas-apps/coupa-risk-assess-tutorial.md) [sailpoint kimnow](../saas-apps/sailpoint-identitynow-tutorial.md),[Kaynak Merkezi](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [jetodoks](../saas-apps/jedox-tutorial.md), [cequence uygulama güvenliği](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [Trendminer](../saas-apps/trendminer-tutorial.md), [lexiyon](../saas-apps/lexion-tutorial.md), [workware](../saas-apps/workware-tutorial.md), [prodpad](../saas-apps/prodpad-tutorial.md), [AWS clientvpn](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [luum](../saas-apps/luum-tutorial.md), [navlun ölçümü](https://www.gpcsl.com/freight.html), [terrayform bulut](../saas-apps/terraform-cloud-tutorial.md) [,](https://gradle.com/) [doğa Research](../saas-apps/nature-research-tutorial.md), [Play Digital tabege](https://login.playsignage.com/login), [remotepc](../saas-apps/remotepc-tutorial.md), [prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise Software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [®](https://www.edriving.com/)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz. https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Genel Önizleme-kurumsal uygulamalar için özel roller

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
 [Temsilci kurumsal uygulama yönetimi Için özel RBAC rolleri](../roles/custom-available-permissions.md) artık genel önizlemede. Bu yeni izinler, uygulama kayıt yönetimi için özel rollerde derleme yapar ve bu, yöneticilerinize erişim için ayrıntılı denetim sağlar. Zaman içinde, Azure AD 'ye temsilci yönetiminin ek izinleri yayımlanacak.

Bazı yaygın temsili senaryolar:
- SAML tabanlı çoklu oturum açma uygulamalarına erişebilen kullanıcı ve grupların atanması
- Azure AD Galeri uygulamalarının oluşturulması
- SAML tabanlı çoklu oturum açma uygulamaları için temel SAML yapılandırmalarının güncelleştirilmesi ve okunması
- SAML tabanlı çoklu oturum açma uygulamaları için imza sertifikalarının yönetimi
- SAML tabanlı çoklu oturum açma uygulamaları için sertifikaların süresi dolan oturum açma bildirimi e-posta adresleri güncelleştirmesi
- SAML tabanlı çoklu oturum açma uygulamaları için SAML belirteci imzasını ve oturum açma algoritmasını güncelleştirme
- SAML tabanlı çoklu oturum açma uygulamaları için Kullanıcı özniteliklerini ve taleplerini oluşturma, silme ve güncelleştirme
- sağlama işlerini açma, kapatma ve yeniden başlatma özelliği
- öznitelik eşleme güncelleştirmeleri
- nesneyle ilişkili sağlama ayarlarını okuma özelliği
- hizmet sorumlusuyla ilişkili sağlama ayarlarını okuma özelliği
- sağlama için uygulama erişimini yetkilendirebilme

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Genel Önizleme-Azure AD Uygulama Ara Sunucusu, kimlik doğrulaması için üst bilgiler kullanan uygulamalara çoklu oturum açma erişimini yerel olarak destekler

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control
 
Azure Active Directory (Azure AD) uygulama proxy 'Si, kimlik doğrulaması için üst bilgileri kullanan uygulamalara çoklu oturum açma erişimini yerel olarak destekler. Azure AD 'de uygulamanız için gereken üst bilgi değerlerini yapılandırabilirsiniz. Üst bilgi değerleri uygulama proxy 'Si aracılığıyla uygulamaya gönderilir. Daha fazla bilgi edinmek için bkz. [Azure AD uygulaması proxy ile şirket içi uygulamalar Için üst bilgi tabanlı çoklu oturum açma](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Genel kullanılabilirlik-Azure AD B2C özel Ilke kullanarak telefon kayıt ve oturum açma

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C

Telefon numarası kaydolma ve oturum açma ile, geliştiriciler ve kuruluşlar müşterilerinin SMS aracılığıyla kullanıcının telefon numarasına gönderilen tek seferlik bir parola kullanarak kaydolmasına ve oturum açmalarına izin verebilir. Bu özellik ayrıca müşterinin telefonlarına erişimi Kayıplarsa telefon numaralarını değiştirmesine de olanak tanır. Özel ilkelerin gücüyle, geliştiricilerin ve kuruluşların markalarını sayfa özelleştirmesine iletişim kurmasına izin verin. [Azure AD B2C 'de özel ilkelerle telefon kaydı ve oturum açma ayarlamayı](../../active-directory-b2c/phone-authentication-user-flows.md)öğrenin.
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Kasım 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Adobe Kimlik Yönetimi](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic-Tac mobil](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Genel Önizleme-ProxyAddresses ile e-posta Sign-In artık hazırlanan dağıtım aracılığıyla dağıtılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Kiracı yöneticileri artık, ProxyAddresses ile belirli Azure AD gruplarına e-posta Sign-In dağıtmak için hazırlanan dağıtımı kullanabilir. Bu, özelliği, giriş bölgesi bulma ilkesi aracılığıyla kiracı tamamına dağıtmaya başlamadan önce yapmaya çalışırken yardımcı olabilir. Hazırlanmış dağıtım aracılığıyla ProxyAddresses ile e-posta Sign-In dağıtmaya yönelik yönergeler [belgelerde](../authentication/howto-authentication-use-email-signin.md)bulunur.
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Sınırlı Önizleme-oturum açma tanılaması

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 
Oturum açma tanılamasının ilk önizleme sürümü ile yöneticiler artık Kullanıcı oturum açma işlemlerini gözden geçirebilir. Yöneticiler bağlamsal, özel ve ilgili ayrıntıları ve oturum açma sırasında neler olduğunu ve sorunların nasıl düzeltileceğini gösteren kılavuzları alabilir. Tanılama, hem Azure AD düzeyinde hem de koşullu erişim 'i Tanıla ve çöz Blade ' de kullanılabilir. Bu sürümde ele alınan tanılama senaryoları, koşullu erişim, Multi-Factor Authentication ve başarılı oturum açma Erişimlerdir.

Daha fazla bilgi için bkz. [Azure AD 'de oturum açma tanılaması nedir?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Geliştirilmiş tanınmayan oturum açma özellikleri

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

  Bilinmeyen oturum açma özellikleri algılamaları güncelleştirildi. Müşteriler daha yüksek riskli, bilinmeyen oturum açma özellikleri algılamalarını fark edebilir. Daha fazla bilgi için bkz. [risk nedir?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Bulut sağlama aracısının genel önizleme yenilemesi kullanıma sunuldu (sürüm: 1.1.281.0)

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Azure AD bulutu sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Bulut sağlama Aracısı genel önizlemede yayımlanmıştır ve artık Portal üzerinden kullanılabilir. Bu sürümde, etki alanlarınız için GMSA desteği, daha iyi güvenlik, iyileştirilmiş ilk eşitleme döngüleri ve büyük gruplar için destek sağlayan çeşitli geliştirmeler bulunur. Daha fazla ayrıntı için yayın sürümü [geçmişine](../app-provisioning/provisioning-agent-release-version-history.md) göz atın. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker kurtarma anahtarı API uç noktası şu anda/ınformationprotection altında

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Cihaz erişim yönetimi  
**Ürün yeteneği:** Cihaz yaşam döngüsü yönetimi
 
Daha önce,/BitLocker uç noktası aracılığıyla BitLocker anahtarlarını kurtarabilirsiniz. Bu uç noktayı kullanımdan kaldıracağız ve müşteriler artık/ınformationprotectionaltında olan API 'YI kullanmaya başlamalıdır. 

Bu değişiklikleri yansıtmak için belgelerdeki güncelleştirmeler için bkz. [BitLocker kurtarma API 'si](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) .

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Uzak Masaüstü Hizmetleri HTML5 Web Istemcisi için uygulama ara sunucusu desteğinin genel kullanılabilirliği

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control
 
Uzak Masaüstü Hizmetleri (RDS) Web Istemcisi için Azure AD Uygulama Ara Sunucusu desteği genel kullanıma sunuldu. RDS Web istemcisi, kullanıcıların Microsoft Edge, Internet Explorer 11, Google Chrome gibi HTLM5 özellikli bir tarayıcıdan uzak masaüstü altyapısına erişmesini sağlar. Kullanıcılar, uzak uygulamalarla veya masaüstü bilgisayarlardan her yerden yerel bir cihazla etkileşim kurabilir. 

Azure AD Uygulama Ara Sunucusu kullanarak, tüm zengin istemci uygulamaları için ön kimlik doğrulama ve koşullu erişim ilkelerini zorunlu tutarak RDS dağıtımınızın güvenliğini artırabilirsiniz. Daha fazla bilgi için bkz. [Uzak Masaüstü 'Nü Azure Ile yayımlama ad uygulama ara sunucusu](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Yeni gelişmiş dinamik grup hizmeti genel önizlemede

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş
 
Gelişmiş dinamik grup hizmeti artık genel önizlemede. Kiracılarında dinamik gruplar oluşturan yeni müşteriler yeni hizmeti kullanacaktır. Dinamik bir grup oluşturmak için gereken süre, kiracının boyutu yerine oluşturulmakta olan grubun boyutuyla orantılıdır. Bu güncelleştirme, müşteriler daha küçük gruplar oluştururken büyük kiracıların performansını önemli ölçüde iyileştirir. 

Yeni hizmet aynı zamanda birkaç dakika içinde öznitelik değişiklikleri nedeniyle üye ekleme ve kaldırma işlemlerinin tamamlanmasını sağlar. Ayrıca, tek işleme arızaları kiracı işlemeyi engellemez. Dinamik grupları oluşturma hakkında daha fazla bilgi edinmek için [belgelerimize](../enterprise-users/groups-create-rule.md)bakın.
 
---
## <a name="october-2020"></a>Ekim 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Azure TLS sertifika değişikliklerinden etkilenen Azure AD şirket Içi karma aracıları

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** yok  
**Ürün yeteneği:** Platformunun

Microsoft, Azure hizmetlerini farklı bir kök sertifika yetkilisi (CA) kümesinden TLS sertifikaları kullanacak şekilde güncelleştiriyor. Bu güncelleştirme, geçerli CA sertifikalarının CA/tarayıcı Forum temel gereksinimlerinden biriyle uyumlu olmaması nedeniyle oluşur. Bu değişiklik, şirket içinde yüklü olan ve güvenilen bir kök sertifika listesi olan sıkı ortamları olan ve yeni sertifika verenler için güncelleştirilmeleri gereken Azure AD karma aracılarını etkiler.

Bu değişiklik, hemen işlem yapmazsanız hizmeti kesintiye neden olur. Bu aracılar, kullanıcılarınızın aynı parolaları kullanarak uygulamalara oturum açmasını sağlayan [geçiş kimlik doğrulama](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) aracılarına ve Azure AD EŞITLEME 'ye ad gerçekleştiren [bulut sağlama önizleme](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) aracılarına izin veren [uygulama proxy bağlayıcıları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) içerir. 

Yalnızca belirli bir sertifika Iptal listesi (CRL) indirmesi için giden çağrılara izin veren güvenlik duvarı kuralları ayarlanmış bir ortamınız varsa, aşağıdaki CRL ve OCSP URL 'Lerine izin vermeniz gerekir. Erişimi etkinleştirmek için değişiklik ve CRL ve OCSP URL 'Leri hakkında tam Ayrıntılar için bkz.  [Azure TLS sertifikası değişiklikleri](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Sağlama olayları, denetim günlüklerinden kaldırılacak ve yalnızca sağlama günlüklerine yayımlanacak

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 
SCıM [sağlama hizmeti](../app-provisioning/user-provisioning.md) tarafından gerçekleştirilen etkinlik hem denetim günlükleri hem de sağlama günlüklerinde günlüğe kaydedilir. Bu, ServiceNow içindeki bir kullanıcının oluşturulması, GSuite 'te grup veya AWS 'den bir rolün içeri aktarılması gibi etkinlikleri içerir. Gelecekte bu olaylar yalnızca sağlama günlüklerinde yayımlanacaktır. Günlüklerde yinelenen olayları önlemek için bu değişiklik uygulanıyor ve günlük Analizi 'nde Günlükler kullanan müşteriler tarafından tahakkuk eden ek maliyetler. 

Bir tarih tamamlandığında güncelleştirme sağlayacağız. Bu kullanımdan kaldırma, 2020 takvim yılı için planlanmıyor. 

> [!NOTE]
> Bu, sağlama hizmeti tarafından yayılan eşitleme olaylarının dışında denetim günlüklerinde olayları etkilemez. Bir uygulama oluşturma, koşullu erişim ilkesi, dizindeki bir Kullanıcı vb. gibi olaylar, denetim günlüklerinde yayınlanmeye devam edecektir. [Daha fazla bilgi edinin](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure Aktarım Katmanı Güvenliği (TLS) sertifika değişikliklerinden etkilenen Azure AD şirket Içi karma aracıları

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** yok  
**Ürün yeteneği:** Platformunun
 
Microsoft, Azure hizmetlerini farklı bir kök sertifika yetkilisi (CA) kümesinden TLS sertifikaları kullanacak şekilde güncelleştiriyor. CA/tarayıcı Forum temel gereksinimlerinden birini takip eden geçerli CA sertifikaları nedeniyle bir güncelleştirme olacaktır. Bu değişiklik, şirket içinde yüklü olan ve sabit bir kök sertifika listesi olan sıkı ortamları olan Azure AD karma aracılarını etkiler. Bu aracıların yeni sertifika verenler ile güveneceği şekilde güncellenmesi gerekir.

Bu değişiklik, hemen işlem yapmazsanız hizmeti kesintiye neden olur. Bu aracılar şunları içerir: 
- Şirket içi uzaktan erişim için [uygulama proxy bağlayıcıları](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) 
- Kullanıcılarınızın aynı parolaları kullanarak uygulamalarda oturum açmalarına olanak tanıyan [geçiş kimlik doğrulama](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) aracıları
- Azure AD eşitleme 'ye AD veren [bulut sağlama önizleme](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) aracıları. 

Yalnızca belirli bir sertifika Iptal listesi (CRL) indirmesi için giden çağrılara izin veren güvenlik duvarı kuralları ayarlanmış bir ortamınız varsa, CRL ve OCSP URL 'Lerine izin vermeniz gerekir. Erişimi etkinleştirmek için değişiklik ve CRL ve OCSP URL 'Leri hakkında tam Ayrıntılar için bkz.  [Azure TLS sertifikası değişiklikleri](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 ve 3DES US Gov bulutta kullanımdan kaldırıldı

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Tüm Azure AD uygulamaları  
**Ürün yeteneği:** Standartlarına
 
Azure Active Directory, 31 Mart 2021 ' den itibaren aşağıdaki protokolleri kullanımdan kaldırır:
- TLS 1.0
- TLS 1.1
- 3DES şifre paketi (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Tüm istemci-sunucu ve tarayıcı-sunucu birleşimleri, Azure, Office 365 ve Microsoft 365 hizmetleri için Azure Active Directory güvenli bir bağlantı sağlamak üzere TLS 1,2 ve modern şifre paketleri kullanmalıdır.

Etkilenen ortamlar:
- Azure US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Kullanım dışı protokol bağımlılıklarını kaldırma kılavuzu için lütfen [ORTAMıNıZDAKI tls 1,2 desteğini etkinleştirmek Için Azure AD TLS 1,1 ve 1,0](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)kullanım dışı bölümüne bakın.
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Yönetim birimi ve nesne kapsamındaki rollere uygulama atama

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
Bu özellik, yönetim birimi kapsamındaki bir yönetici rolüne uygulama (SPN) atama yeteneği sağlar. Daha fazla bilgi edinmek için [yönetim birimine kapsamlı roller atama](../roles/admin-units-assign-roles.md)bölümüne bakın.

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Artık, bir kaynağa erişim izni reddedildiğinde Konuk kullanıcıları devre dışı bırakabilir ve silebilirsiniz

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Erişim Incelemeleri  
**Ürün yeteneği:** Kimlik yönetimi
 
Kuruluşların gruplar ve uygulamalarda dış konukları daha iyi yönetmesine yardımcı olmak için Azure AD erişim Incelemelerinde devre dışı bırak ve Sil gelişmiş bir denetimdir. Bir erişim incelemesinin konukları engellenirse, **devre dışı bırak ve Sil** , bu otomatik olarak 30 gün boyunca oturum açmalarını engeller. 30 gün sonra kiracıdan tamamen kaldırılır.

Bu özellik hakkında daha fazla bilgi için bkz. [Azure AD erişim gözden geçirmeleri ile dış kimlikleri devre dışı bırakma ve silme](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Erişim gözden geçirmesi oluşturucuları, e-postalara gözden geçirenlere özel iletiler ekleyebilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Erişim Incelemeleri  
**Ürün yeteneği:** Kimlik yönetimi
 
Azure AD erişim gözden geçirmeleriyle, İncelemeleri oluşturan Yöneticiler artık gözden geçirenlere özel bir ileti yazabilir. Gözden geçirenler, aldıkları e-postadaki iletiyi inceleyerek incelemeyi tamamlamasını ister. Bu özelliği kullanma hakkında daha fazla bilgi edinmek için, [bir veya daha fazla erişim Incelemesi oluşturma](../governance/create-access-review.md#create-one-or-more-access-reviews) bölümünün 14. adımına bakın.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Ekim 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Apple İşletme Yönetimi](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C için tümleştirme Yardımcısı

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 
Tümleştirme Yardımcısı (Önizleme) deneyimi artık Azure AD B2C Uygulama kayıtları için kullanılabilir. Bu deneyim, uygulamanızı yaygın senaryolar için yapılandırmaya kılavuzluk eder. [Microsoft Identity platform en iyi uygulamaları ve önerileri](../develop/identity-platform-integration-checklist.md)hakkında daha fazla bilgi edinin.
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Azure portal Kullanıcı arabirimindeki rol şablonu KIMLIĞINI görüntüleme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure rolleri  
**Ürün yeteneği:** Access Control
 

Artık Azure portal her bir Azure AD rolünün şablon KIMLIĞINI görüntüleyebilirsiniz. Azure AD 'de seçili rolün  **açıklamasını** seçin. 

Müşterilerin, görünen ad yerine, PowerShell betiklerinde ve kodunda rol şablonu kimliklerini kullanması önerilir. Rol şablonu KIMLIĞI [Directoryroles](/graph/api/resources/directoryrole) ve [roledefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) nesnelerinde kullanılmak üzere desteklenir. Rol şablonu kimlikleri hakkında daha fazla bilgi için bkz. [Azure AD yerleşik rolleri](../roles/permissions-reference.md).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Azure AD B2C kaydolma Kullanıcı akışları için API bağlayıcıları artık genel önizlemede

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 

API bağlayıcıları artık Azure Active Directory B2C kullanılabilir. API bağlayıcıları, oturum açma Kullanıcı akışlarınızı özelleştirmek ve dış bulut sistemleriyle tümleştirilebilen Web API 'Lerini kullanmanıza imkan tanır. API bağlayıcılarını kullanarak şunları yapabilirsiniz:

- Özel onay iş akışlarıyla tümleştirin
- Kullanıcı giriş verilerini doğrulama
- Kullanıcı özniteliklerinin üzerine yaz 
- Özel iş mantığını Çalıştır 

 Daha fazla bilgi edinmek için bkz. [Kayıt belgelerini özelleştirmek ve genişletmek IÇIN API bağlayıcılarını kullanın](../../active-directory-b2c/api-connectors-overview.md) .

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Yetkilendirme yönetiminde bağlı kuruluşlar için durum özelliği

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Dizin Yönetimi **ürün yeteneği:** Yetkilendirme Yönetimi
 

 Tüm bağlı kuruluşların artık "State" adlı ek bir özelliği olacaktır. Durum, bağlı kuruluşun "tüm yapılandırılmış bağlı kuruluşlara" başvuran ilkelerde nasıl kullanılacağını kontrol eder. Değer "yapılandırılmış" olacaktır (kuruluşun "All" yan tümcesini kullanan ilkelerin kapsamında olduğu ve kuruluşun kapsamda olmadığı anlamına gelir).  

El ile oluşturulan bağlı kuruluşların varsayılan ayarı "yapılandırılmış" olacaktır. Bu arada otomatik olarak oluşturulan (internet 'ten herhangi bir kullanıcının erişim istemesine izin veren ilkeler aracılığıyla oluşturulur) varsayılan olarak "önerilir" olarak yapılır.  9 2020 Eylül 'tan önce oluşturulan bağlı kuruluşlar "yapılandırıldı" olarak ayarlanır. Yöneticiler, gerektiğinde bu özelliği güncelleştirebilir. [Daha fazla bilgi edinin](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory dış kimlikler artık B2C için Premium gelişmiş güvenlik ayarlarına sahiptir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 
Kimlik korumasının risk tabanlı koşullu erişim ve risk algılama özellikleri artık [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md)sunulmaktadır. Bu gelişmiş güvenlik özellikleriyle müşteriler artık şunları yapabilir:
- B2C uygulamaları ve son kullanıcı hesaplarıyla riskleri değerlendirmek için akıllı öngörülerden yararlanın. Algılamalar, genel seyahat, anonim IP adresleri, kötü amaçlı yazılım bağlantılı IP adresleri ve Azure AD tehdit bilgileri içerir. Portal ve API tabanlı raporlar da kullanılabilir.
- B2C kullanıcıları için uyarlamalı kimlik doğrulama ilkelerini yapılandırarak riskleri otomatik olarak çözün. Uygulama geliştiricileri ve Yöneticiler, çok faktörlü kimlik doğrulaması (MFA) gerektirerek veya Kullanıcı risk düzeyine bağlı olarak erişimi engelleyerek, konum, Grup ve uygulamaya dayalı ek denetimlerle, gerçek zamanlı riskleri azaltabilirler.
- Azure AD B2C Kullanıcı akışları ve özel ilkelerle tümleştirin. Koşullar Azure AD B2C yerleşik Kullanıcı akışlarından tetiklenebilir veya B2C özel ilkelerine eklenebilir. B2C Kullanıcı akışının diğer yönlerini de olduğu gibi, son kullanıcı deneyimi iletileri de özelleştirilebilir. Özelleştirme, kuruluşun ses, marka ve risk azaltma alternatiflerine göre belirlenir.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Ekim 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
2020 Ekim 'de, federasyon desteğiyle App Gallery 'de aşağıdaki 27 yeni uygulamayı ekledik:

[Sentry](../saas-apps/sentry-tutorial.md), [bıfrelebee-üretkenlik superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture bulutu](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [Eabesteci](../saas-apps/eacomposer-tutorial.md), [Genesys, Azure Için bir Azure için bulut tümleştirmesi](https://apps.mypurecloud.com/msteams-integration/), [bölge teknolojileri portalı](https://portail.zonetechnologie.com/signin) [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [datawiza erişim Aracısı](https://console.datawiza.com/), [Zokri](https://app.zokri.com/), [checkkanıt](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atışınsal](http://atspoke.com/login), [randevu anımsatıcısı](https://app.appointmentreminder.co.nz/account/login), [Cloud. Market](https://cloud.market/), [seyahatperk](../saas-apps/travelperk-tutorial.md) [, içiyilik,](https://app.greetly.com/) [orgvititesi SSO](../saas-apps/orgvitality-sso-tutorial.md), [Web Cargo hava](../saas-apps/web-cargo-air-tutorial.md), [döngü Flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [starakılda](../saas-apps/starmind-tutorial.md), [workstem](https://hrm.workstem.com/login), [Perakende Zipline](../saas-apps/retail-zipline-tutorial.md), [hoxhunt](../saas-apps/hoxhunt-tutorial.md), [mevisio](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure sanal Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz. https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Sağlama günlükleri artık Log Analytics 'e akışla eklenebilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 

Şunları yapmak için sağlama günlüklerinizi Log Analytics 'e yayımlayın:
- 30 günden uzun süre için sağlama günlüklerini depolayın
- Özel uyarıları ve bildirimleri tanımlama
- Günlükleri görselleştirmek için panolar oluşturun
- Günlükleri çözümlemek için karmaşık sorguları yürütün 

Özelliği nasıl kullanacağınızı öğrenmek için bkz. [sağlamanın Azure izleyici günlükleriyle nasıl tümleştirildiğini anlama](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Sağlama günlükleri artık uygulama sahipleri tarafından görüntülenebilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 
Artık uygulama sahiplerinin etkinlikleri sağlama hizmeti tarafından izlemesini sağlayabilir ve bunları ayrıcalıklı bir rol sağlamadan veya sorunu giderebilmeden sorunları giderebilirsiniz. [Daha fazla bilgi edinin](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10 Azure Active Directory rollerini yeniden adlandırma

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Azure rolleri  
**Ürün yeteneği:** Access Control
 
Bazı Azure Active Directory (AD) yerleşik rollerinin, Microsoft 365 Yönetim Merkezi, Azure AD portalı ve Microsoft Graph gibi görünenlerden farklı adları vardır. Bu tutarsızlık otomatik işlemlerde sorunlara neden olabilir. Bu güncelleştirmeyle, 10 rol adını tutarlı hale getirmek için yeniden adlandırıyoruz. Aşağıdaki tabloda yeni rol adları verilmiştir:

![M365 Yönetim Merkezi, Azure portal ve API 'sinde, MS Graph API ve Azure portal ve önerilen yeni rol adındaki rol adlarını gösteren tablo.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>MSAL JS 2. x kullanarak SPAs için auth Code Flow Azure AD B2C desteği

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 
MSAL.js sürüm 2. x artık tek sayfalı Web Apps (maça 'Lar) için yetkilendirme kodu akışı desteğini içerir. Azure AD B2C artık Azure portal üzerinde SPA uygulama türünün kullanımını ve tek sayfalı uygulamalar için PKCE ile MSAL.js yetkilendirme kodu akışının kullanımını destekleyecektir. Bu, Azure AD B2C kullanan maça 'Ları, daha yeni tarayıcılarla ve ABIDE ile daha yeni kimlik doğrulama protokolü önerilerini kullanarak SSO korumasını sağlar. Azure Active Directory B2C öğreticide [tek sayfalı uygulama (Spa) kaydetme](../../active-directory-b2c/tutorial-register-spa.md) ile çalışmaya başlayın.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Güvenilir bir cihaz ayarında Multi-Factor Authentication (MFA) anımsanacak güncelleştirmeler

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 

Son zamanlarda, kimlik doğrulamasını 365 güne kadar genişletmek için güvenilir bir cihaz özelliğindeki [hatırla Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) öğesini güncelleştirdik. Azure Active Directory (Azure AD) Premium lisansları, yeniden kimlik doğrulama ayarları için daha fazla esneklik sağlayan [koşullu erişim – oturum açma sıklığı ilkesini](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) de kullanabilir.

En iyi kullanıcı deneyimi için, güvenilir cihazlarda, konumlarda veya düşük riskli oturumlarda oturum ömrünü uzatmak için güvenilen bir cihaz ayarında MFA hatırlama konusuna alternatif olarak, koşullu erişim oturum açma sıklığını kullanmanızı öneririz. Başlamak için, yeniden [kimlik doğrulama deneyimini iyileştirmek üzere en son kılavuzumuzu](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)inceleyin.

---
