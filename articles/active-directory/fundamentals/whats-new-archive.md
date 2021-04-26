---
title: Azure Active Directory yenilikler için arşiv var mı? | Microsoft Belgeleri
description: Bu içerik kümesinin Genel Bakış bölümündeki yenilikler sürüm notları, 6 aylık etkinlik içerir. 6 ay sonra, öğeler ana makaleden kaldırılır ve bu arşiv makalesine yerleştirilir.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0500c8535c6230bad85c97c23787a405edfbfee
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588216"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Azure Active Directory yenilikler için arşiv var mı?

[Azure Active Directory 'deki yenilikler nelerdir? sürüm notları](whats-new.md) makalesi, son altı aya yönelik güncelleştirmeleri içerir, ancak bu makalede daha eski bilgiler yer alır.

Azure Active Directory yenilikler nelerdir? sürüm notları hakkında bilgi sağlar:

- En son yayınlar
- Bilinen sorunlar
- Hata düzeltmeleri
- Kullanım dışı işlevsellik
- Değişiklik planları

---

## <a name="september-2020"></a>Eylül 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Eylül 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [CODA](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Alıcı Eşitleme](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot güvenlik tanıma](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Bulut sağlama genel önizlemeyi yenileme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD bulut sağlama **ürün yeteneği:** kimlik yaşam döngüsü yönetimi
 
Azure AD Connect bulut sağlama genel önizleme yenileme özellikleri müşteri geri bildirimlerinden geliştirilen iki önemli geliştirmelere sahiptir: 

- Azure portal aracılığıyla öznitelik eşleme deneyimi

    Bu özellikle, BT yöneticileri Kullanıcı, Grup veya iletişim özniteliklerini AD 'den Azure AD 'ye eşleyerek bugün mevcut çeşitli eşleme türlerini kullanarak eşleyebilirsiniz. Öznitelik eşleme, Active Directory ' den Azure Active Directory akan özniteliklerin değerlerini standartlaştırarak kullanılan bir özelliktir. Bunlardan biri, AD 'den Azure AD 'ye ait olan öznitelik değerinin doğrudan eşlenip eşlenmeyeceğini belirleyebilir veya kullanıcıları sağlarken öznitelik değerlerini dönüştürmek için ifadeler kullanın. [Daha fazla bilgi edinin](../cloud-sync/how-to-attribute-mapping.md)

- İsteğe bağlı sağlama veya test Kullanıcı deneyimi

    Yapılandırmanızı ayarladıktan sonra, Kullanıcı dönüşümünün kapsamdaki tüm kullanıcılarınıza uygulamadan önce beklendiği gibi çalışıp çalışmadığını görmek için test etmek isteyebilirsiniz. İsteğe bağlı sağlama sayesinde, BT yöneticileri bir AD kullanıcısının ayırt edici adını (DN) girebilir ve beklenen şekilde eşitlenip eşitlenmediğini görebilirler. İsteğe bağlı sağlama, daha önce beklenen şekilde çalışan öznitelik eşleştirmelerinin sağlanması için harika bir yol sağlar. [Daha Fazla Bilgi](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Azure AD 'de denetlenen BitLocker kurtarma-genel önizleme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Cihaz erişim yönetimi  
**Ürün yeteneği:** Cihaz yaşam döngüsü yönetimi
 
BT yöneticileri veya son kullanıcılar erişimi olan BitLocker kurtarma anahtarlarını okuduklarında, Azure Active Directory artık kurtarma anahtarına kimin eriştiğini yakalayan bir denetim günlüğü oluşturur. Aynı denetim, BitLocker anahtarının ilişkilendirildiği cihazın ayrıntılarını sağlar.

Son kullanıcılar [, kendi Kurtarma anahtarlarına Hesabım aracılığıyla erişebilirler](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). BT yöneticileri, [Beta 'Daki BitLocker kurtarma anahtarı API 'si](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) veya Azure AD portalı aracılığıyla Kurtarma anahtarlarına erişebilir. Daha fazla bilgi için bkz. [Azure AD portalındaki BitLocker anahtarlarını görüntüleme veya kopyalama](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Ekip cihazları yönetici yerleşik rolü

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** RAPORDAKI  
**Ürün yeteneği:** Access Control
 
[Takımlar cihazları yönetici](../roles/permissions-reference.md#teams-devices-administrator) rolüne sahip kullanıcılar takımlar yönetim merkezinden [ekipten sertifikalı cihazları](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) yönetebilir. 

Bu rol, kullanıcının tüm cihazları tek bakışta görüntülemesine olanak tanır ve bu sayede cihazları arayabilir ve filtreleyebilirsiniz. Kullanıcı oturum açma hesabı ve cihazın marka ve modeli dahil olmak üzere her bir cihazın ayrıntılarını da denetleyebilir. Kullanıcı cihazdaki ayarları değiştirebilir ve yazılım sürümlerini güncelleştirebilir. Bu rol, ekip etkinliklerini denetlemek ve cihazın kalitesini çağırmak için izinler vermez.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Dizin nesneleri için Gelişmiş sorgu özellikleri

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** MS grafiği  
**Ürün yeteneği:** Geliştirici deneyimi
 
Azure AD API 'Lerinde dizin nesneleri için sunulan tüm yeni sorgu özellikleri artık v 1.0 uç noktasında ve üretime hazır olarak sunulmaktadır. Geliştiriciler, Standart OData işleçlerini kullanarak Dizin nesnelerini ve ilgili bağlantıları sayabilir, arayabilir, filtreleyip sıralayabilir.

Daha fazla bilgi edinmek için [buradaki](https://aka.ms/BlogPostMezzoGA)belgelere bakın ve bu [kısa anketle](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)de geri bildirim gönderebilirsiniz.
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Genel Önizleme: koşullu erişim ilkelerini yapılandıran kiracılar için sürekli erişim değerlendirmesi

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Artık sürekli erişim değerlendirmesi (CAE), koşullu erişim ilkelerine sahip Azure AD kiracılar için genel önizlemede kullanıma sunulmuştur. CAE ile kritik güvenlik olayları ve ilkeleri gerçek zamanlı olarak değerlendirilir. Buna hesap devre dışı bırakma, parola sıfırlama ve konum değişikliği dahildir. Daha fazla bilgi için bkz. [sürekli erişim değerlendirmesi](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Genel Önizleme: kullanıcılardan bir erişim paketi talep etmesini isteyin onay kararlarını artırmak için ek sorular

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 
Yöneticiler artık bir erişim paketi isteyen kullanıcıların, Azure AD Yetkilendirme Yönetimi 'nin erişim portalından yalnızca iş gerekçesinin ötesinde ek sorulara yanıt yanıtlamasını gerektirebilir. Daha sonra bu kullanıcıların yanıtları daha doğru bir erişim onayı kararı vermek için onaylayanlara gösterilir. Daha fazla bilgi edinmek için bkz. [onay için ek istek sahibi bilgileri toplama (Önizleme)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Genel Önizleme: Gelişmiş Kullanıcı Yönetimi

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kullanıcı Yönetimi  
**Ürün yeteneği:** Kullanıcı Yönetimi
 

Azure AD portalı, tüm kullanıcılar ve silinen kullanıcılar sayfalarındaki kullanıcıları bulmayı kolaylaştıracak şekilde güncelleştirilmiştir. Önizlemedeki değişiklikler şunları içerir: 
- Nesne KIMLIĞI, Dizin eşitleme durumu, oluşturma türü ve kimlik veren gibi daha görünür Kullanıcı özellikleri.
- Arama şimdi ad, e-posta ve nesne kimliklerinin birleştirilmiş aramasına izin veriyor.
- Kullanıcı türüne göre Gelişmiş filtreleme (üye, konuk ve yok), Dizin eşitleme durumu, oluşturma türü, şirket adı ve etki alanı adı.
- Ad, Kullanıcı asıl adı ve silinme tarihi gibi özelliklerde yeni sıralama özellikleri.
- Yeni bir Toplam Kullanıcı sayısı, tüm aramalarla veya filtrelerle güncelleştirilir.

Daha fazla bilgi için lütfen [Azure Active Directory Içindeki Kullanıcı yönetimi geliştirmeleri (Önizleme)](../enterprise-users/users-search-enhanced.md)bölümüne bakın.

---

### <a name="new-notes-field-for-enterprise-applications"></a>Kurumsal uygulamalar için yeni notlar alanı

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar **ürün yeteneği:** SSO

Kurumsal uygulamalara ücretsiz metin notları ekleyebilirsiniz. Kurumsal uygulamalar altındaki uygulamaları yönetici yapmanıza yardımcı olacak ilgili bilgileri ekleyebilirsiniz. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure Active Directory (Azure AD) kiracınızdaki bir uygulamanın özelliklerini yapılandırma](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Eylül 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Eylül 2020 ' de, federasyon desteğiyle App Gallery 'de aşağıdaki 34 yeni uygulamaları ekledik:

[VMware ufuk-birleştirilmiş erişim ağ geçidi](), [Pulse Secure PC](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [innetleştirilmiş](https://services.enteksystems.de/sso/microsoft/signup), [bookpencere](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [Iş için HashData](https://hashdata.app/login.xhtml), [securelogin](https://securelogin.securelogin.nu/sso/azure/login), [cybersolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [sibsolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [limbpacmms](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), MB [Yükseklik](../saas-apps/zeroheight-tutorial.md), [cinsiyet uygunluk](https://app.genderfitness.com/), [COEO portalı](https://my.coeo.com/), [Grammarly](../saas-apps/grammarly-tutorial.md), [fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA dever Suite](../saas-apps/rsa-archer-suite-tutorial.md), [teamzbeceri](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BIZMERLINHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobil dolap](../saas-apps/mobile-locker-tutorial.md), [zengine](../saas-apps/zengine-tutorial.md), [CLOUDCADı](https://app.cloudcadi.com/login), [sıtem](https://simfonianalytics.com/accounts/microsoft/login/), [bir kimlik & erişim yönetimi](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [eventfinity](../saas-apps/eventfinity-tutorial.md), [fexa](../saas-apps/fexa-tutorial.md), [güvenli imzalama Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [güvenli imzalama Enterprise Portal AAD kurulumu](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [wınfc çevrimiçi](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft tarafından korunan, F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz: https://aka.ms/AppsTutorial .

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Azure AD Yetkilendirme Yönetimi 'nde yeni temsili rolü: paket atama Yöneticisi 'ne erişim

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 
Atamaları yönetmek için ayrıntılı izinler sağlamak üzere Azure AD Yetkilendirme Yönetimi 'ne yeni bir erişim paketi atama Yöneticisi rolü eklenmiştir. Artık bu roldeki bir kullanıcıya görevler temsilcisinden, bir erişim paketinin atama yönetimini iş sahibine devredebilir. Ancak, Access Package atama Yöneticisi, erişim paketi ilkelerini veya yöneticiler tarafından ayarlanan diğer özellikleri değiştiremez. 

Bu yeni rolle, atamaların yönetimini devretmek ve diğer tüm erişim paketi yapılandırmalarında yönetim denetimini sürdürmek için gereken en az ayrıcalıklardan yararlanabilirsiniz. Daha fazla bilgi için bkz. [Yetkilendirme Yönetimi rolleri](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management ekleme akışında yapılan değişiklikler

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Privileged Identity Management  
**Ürün yeteneği:** Privileged Identity Management
 
Daha önce, Privileged Identity Management (PıM) gerekli Kullanıcı onayını ve Azure AD MFA 'ya kayıt içeren PıM 'nin dikey penceresindeki bir ekleme akışını ekleme. Azure AD rolleri ve yöneticiler dikey penceresinde PıM deneyiminin son tümleştirmesi ile bu deneyimi kaldırdık. Geçerli P2 lisansına sahip herhangi bir kiracı, PıM 'ye otomatik olarak eklendi.

PıM 'ye ekleme, kiracınızda herhangi bir dolaysız olumsuz etkiye sahip değildir. Aşağıdaki değişiklikleri bekleyebilir:
- Etkin vs. gibi ek atama seçenekleri, PıM veya Azure AD rolleri ve yöneticiler dikey penceresinde atama yaptığınızda başlangıç ve bitiş zamanına uygundur. 
- Doğrudan atama deneyimine getirilen yönetim birimleri ve özel roller gibi ek kapsam mekanizmaları. 
- Genel yönetici veya ayrıcalıklı rol yöneticisiyseniz, PıM haftalık Özeti gibi birkaç ek e-posta almaya başlayabilirsiniz. 
- Ayrıca, rol atamasıyla ilgili denetim günlüğünde MS-PIM hizmet sorumlusu ' nı da görebilirsiniz. Bu beklenen değişikliğin normal iş akışınızı etkilememesi gerekir.

 Daha fazla bilgi için bkz. [Privileged Identity Management kullanmaya başlama](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD Yetkilendirme Yönetimi: erişim paketi kaynaklarının seçme bölmesi artık varsayılan olarak seçili katalogdaki kaynakları gösterir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kullanıcı erişim yönetimi  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 

Erişim paketi oluşturma akışında, kaynak rolleri sekmesinde, seçim bölmesi davranışı değişiyor. Şu anda varsayılan davranış, seçilen kataloğa eklenen kullanıcının ve kaynakların sahip olduğu tüm kaynakları göstermek olur. 

Bu deneyim, kullanıcıların katalogdan kolayca kaynak seçebilmek için varsayılan olarak katalogda zaten eklenen kaynakları görüntüleyecek şekilde değiştirilecektir. Güncelleştirme, erişim paketlerine eklenecek kaynakların keşfediliğine yardımcı olur ve kataloğun parçası olmayan kullanıcıya ait kaynakları yanlışlıkla ekleme riskini azaltır. Daha fazla bilgi edinmek için bkz. [Azure AD yetkilendirme yönetiminde yeni bir erişim paketi oluşturma](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Ağustos 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication Sunucusu Güvenlik Duvarı gereksinimlerine yönelik güncelleştirmeler

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
1 Ekim 2020 ' den itibaren Azure MFA sunucusu güvenlik duvarı gereksinimleri ek IP aralıkları gerektirecektir.

Kuruluşunuzda giden güvenlik duvarı kurallarınız varsa, MFA sunucularınızın tüm gerekli IP aralıklarıyla iletişim kurabilmesi için kuralları güncelleştirin. IP aralıkları [Azure Multi-Factor Authentication sunucusu güvenlik duvarı gereksinimleriyle](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)belgelenmiştir.

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Kimlik güvenli puanı 'nda Kullanıcı deneyimine yaklaşan değişiklikler

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Microsoft güvenli puanınızın [yeni sürümünde](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)tanıtılan değişikliklerle uyum sağlamak Için kimlik güvenli puan portalı 'nı güncelleştiriyoruz. 

Değişikliklerle birlikte önizleme sürümü, Eylül ayının başlangıcında kullanılabilir olacaktır. Önizleme sürümündeki değişiklikler şunları içerir:
- Microsoft güvenli puanı ile marka hizalaması için "kimlik güvenli puanı", "kimlik için güvenli skor" olarak yeniden adlandırıldı
- Standart ölçeğe normalleştirilmiş ve işaret yerine yüzde cinsinden bildirilen noktaları

Bu önizlemede, müşteriler mevcut deneyim ve yeni deneyim arasında geçiş yapabilir. Bu önizleme, Kasım 2020 sonuna kadar son olacak. Önizleme sonrasında, müşteriler otomatik olarak yeni UX deneyimine yönlendirilir.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD 'de yeni kısıtlı Konuk erişim Izinleri-genel önizleme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Access Control   
**Ürün yeteneği:** Kullanıcı Yönetimi

Konuk kullanıcılar için dizin düzeyindeki izinleri güncelleştirdik. Bu izinler, yöneticilerin dış Konuk Kullanıcı erişimi üzerinde ek kısıtlamalar ve denetimler gerektirmesini sağlar. Yöneticiler artık dış konukların Kullanıcı ve grupların profiline ve üyelik bilgilerine erişimi için ek kısıtlamalar ekleyebilir. Bu genel önizleme özelliğiyle, müşteriler, konuk kullanıcıların içinde bulundukları grup üyeliklerini görmesini kısıtlama dahil olmak üzere grup üyeliklerini kararlayarak, dış Kullanıcı erişimini ölçekte yönetebilir.

Daha fazla bilgi için bkz. [kısıtlı Konuk erişim izinleri](../enterprise-users/users-restrict-guest-permissions.md) ve [Kullanıcıların varsayılan izinleri](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Hizmet sorumluları için Delta sorgularının genel kullanılabilirliği

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** MS grafiği  
**Ürün yeteneği:** Geliştirici deneyimi
 
Microsoft Graph Delta sorgusu artık v 1.0 'da kaynak türünü destekliyor:
- Hizmet Sorumlusu

Artık istemciler bu kaynaklardaki değişiklikleri etkili bir şekilde izleyebilir ve değişiklikleri yerel bir veri deposuyla eşitlemeye yönelik en iyi çözümü sağlar. Bu kaynakları bir sorguda nasıl yapılandıracağınızı öğrenmek için bkz. [Microsoft Graph verilerdeki değişiklikleri izlemek için Delta sorgu kullanma](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>OAuth2PermissionGrant için Delta sorgularının genel kullanılabilirliği

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** MS grafiği  
**Ürün yeteneği:** Geliştirici deneyimi

Microsoft Graph Delta sorgusu artık v 1.0 'da kaynak türünü destekliyor:
- OAuth2PermissionGrant

İstemciler artık bu kaynaklardaki değişiklikleri etkili bir şekilde izleyebilir ve değişiklikleri yerel bir veri deposuyla eşitlemeye yönelik en iyi çözümü sağlar. Bu kaynakları bir sorguda nasıl yapılandıracağınızı öğrenmek için bkz. [Microsoft Graph verilerdeki değişiklikleri izlemek için Delta sorgu kullanma](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Ağustos 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Ağustos 2020 ' de, federasyon desteğiyle App Gallery 'de aşağıdaki 25 yeni uygulamayı ekledik:

[Backup365](https://portal.backup365.io/login), [Soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [alma SIS](https://almau.getalma.com/), [enlyft Dynamics 365 Bağlayıcısı](http://enlyft.com/), [serseview Space kullanımı yazılım çözümleri](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniq](https://web.uniq.app/), [Visi,](../saas-apps/visibly-tutorial.md) [Zylo](../saas-apps/zylo-tutorial.md), [edmentum-Courseware değerlendirmesi tam yolu](https://auth.edmentum.com/elf/login), [Sivlab](https://cyberlab.evolvesecurity.com/#/welcome), [Altamıra HRM](../saas-apps/altamira-hrm-tutorial.md), [kablotekler](../saas-apps/wirewheel-tutorial.md), [ZX uyumluluk ve yakalama](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), Doğa, cıklü [kurumsal iş denetimleri platformu](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec payını](https://www.clearance.network/), [ISAMs](../saas-apps/isams-tutorial.md), [verasmart](../saas-apps/verasmart-tutorial.md), [AMIKO](https://amiko.web.rivero.app/), [twingate](https://auth.twingate.com/signup), [huni Kiralama](https://nestiolistings.com/sso/oidc/azure/authorize/), [scalefyayılması](https://scalefusion.com/users/sign_in/), [bpanda](https://goto.bpanda.com/login), [vivun takvim bağlantısı](https://app.vivun.com/dashboard/calendar/connect), [Fortigate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md), [wandera Son Kullanıcı](https://www.wandera.com/)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz. https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için buradaki ayrıntıları okuyun https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Kaynak ormanları artık Azure AD DS için kullanılabilir 

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD Domain Services   
**Ürün yeteneği:** Azure AD Domain Services
 
Azure AD Domain Services içindeki kaynak ormanların özelliği artık genel kullanıma sunulmuştur. Artık, akıllı kart yetkilendirmesi de dahil olmak üzere Azure AD Domain Services kullanmak için Parola karması eşitlemesi olmadan yetkilendirmeyi etkinleştirebilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure Active Directory Domain Services (Önizleme) Için çoğaltma kümesi kavramları ve özellikleri](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Azure AD DS yönetilen etki alanları için bölgesel çoğaltma desteği artık kullanılabilir

**Şunu yazın:** Yeni Özellik   
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services
 
Yönetilen bir etki alanını, Azure AD kiracısı başına birden fazla çoğaltma kümesine sahip olacak şekilde genişletebilirsiniz. Çoğaltma kümeleri, Azure AD Domain Services destekleyen herhangi bir Azure bölgesindeki eşlenen herhangi bir sanal ağa eklenebilir. Farklı Azure bölgelerindeki ek çoğaltma kümeleri, bir Azure bölgesi çevrimdışı kalırsa eski uygulamalar için coğrafi olağanüstü durum kurtarma sağlar. Daha fazla bilgi edinmek için bkz. [Azure Active Directory Domain Services (Önizleme) Için çoğaltma kümesi kavramları ve özellikleri](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD My Sign-Ins genel kullanılabilirliği

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Son Kullanıcı deneyimleri
 
Azure AD My Sign-Ins, kurumsal kullanıcıların olağan dışı etkinlikleri denetlemek için oturum açma geçmişini gözden geçirmesine olanak sağlayan yeni bir özelliktir. Ayrıca, bu özellik, son kullanıcıların şüpheli etkinlikler üzerinde "Bu ben değilme" veya "Bu was Me" olarak raporistemelerine izin verir. Bu özelliği kullanma hakkında daha fazla bilgi edinmek için bkz. [My Sign-Ins sayfasından en son oturum açma etkinliğinizi görüntüleme ve arama](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Azure AD 'de SAP başarılı bir şekilde yönetilen Kullanıcı hazırlama, genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Artık Azure AD ile SAP başarılı çarpanlarını yetkili kimlik kaynağı olarak tümleştirebilir ve Azure AD 'de hesapların sağlanması ve ön sağlamasını yapmak için yeni işe alımlar ve sonlandırmalar gibi HR olaylarını kullanarak uçtan uca kimlik yaşam döngüsünü otomatik hale getirebilirsiniz. 

SAP başarılı bir şekilde Azure AD 'nin sağlamasını yapılandırma hakkında daha fazla bilgi edinmek için bkz. [SAP başarılı faktörleri Active Directory için Kullanıcı sağlamayı yapılandırma](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Özel açık KIMLIK Azure AD B2C için MS Graph API desteğini bağlama

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 
Daha önce özel açık KIMLIK bağlama sağlayıcıları yalnızca Azure portal aracılığıyla eklenebilir veya yönetilebilir. Azure AD B2C müşteriler artık Microsoft Graph API beta sürümü aracılığıyla bunları ekleyebilir ve yönetebilir. Bu kaynağı API 'lerle yapılandırma hakkında bilgi edinmek için bkz. [IdentityProvider kaynak türü](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Azure AD yerleşik rollerini bulut gruplarına atama

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD rolleri  
**Ürün yeteneği:** Access Control

Artık bu yeni özelliği kullanarak, Azure AD yerleşik rollerini bulut gruplarına atayabilirsiniz. Örneğin, SharePoint Yöneticisi rolünü Contoso_SharePoint_Admins grubuna atayabilirsiniz. Ayrıca, bir gruba bir rolün uygun bir üyesini sağlamak için PıM 'yi kullanarak, bekleyen erişim izni verebilirsiniz. Bu özelliği nasıl yapılandıracağınızı öğrenmek için bkz. [Azure Active Directory (Önizleme) içinde rol atamalarını yönetmek için bulut gruplarını kullanma](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Öngörüler Iş lideri yerleşik rolü artık kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD rolleri  
**Ürün yeteneği:** Access Control
 
Öngörüler Iş lideri rolündeki kullanıcılar, [M365 Insights uygulaması](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)aracılığıyla bir panolar ve Öngörüler kümesine erişebilir. Bu, tüm panolara ve sunulan Öngörüler ve veri araştırma işlevlerine tam erişim içerir. Ancak, bu roldeki kullanıcılar, Öngörüler Yöneticisi rolünün sorumluluğu olan ürün yapılandırma ayarlarına erişemez. Bu rol hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rolü izinleri](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Öngörüler Yöneticisi yerleşik rolü artık kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD rolleri  
**Ürün yeteneği:** Access Control
 
Öngörüler yönetici rolündeki kullanıcılar, [M365 Insights uygulamasındaki](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)tüm yönetim özelliklerine erişebilir. Bu roldeki bir Kullanıcı dizin bilgilerini okuyabilir, hizmet durumunu izleyebilir, dosya destek biletlerini izleyebilir ve Öngörüler yönetici ayarları yönlerini erişebilir. Bu rol hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rolü izinleri](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Uygulama Yöneticisi ve bulut uygulaması Yöneticisi, uygulamaların uzantı özelliklerini yönetebilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Azure AD rolleri  
**Ürün yeteneği:** Access Control
 
Daha önce, [uzantı özelliğini](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true)yalnızca genel yönetici yönetebilir. Artık uygulama Yöneticisi ve bulut uygulaması Yöneticisi için de bu özelliği etkinleştiriyoruz.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MıM 2016 SP2 düzeltme 4.6.263.0 ve bağlayıcılar 1.1.1301.0

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Microsoft Identity Manager  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Microsoft Identity Manager (MıM) 2016 Service Pack 2 (SP2) için bir [düzeltme paketi paketi (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) kullanılabilir. Bu toplu paket MIM CM, MıM Eşitleme Yöneticisi ve PAM bileşenleri için güncelleştirmeleri içerir. Ayrıca, MıM genel bağlayıcıları derleme 1.1.1301.0, Graph Bağlayıcısı için güncelleştirmeleri içerir.

---

## <a name="july-2020"></a>Temmuz 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>BT Yöneticisi olarak, koşullu erişim kullanarak istemci uygulamalarını hedeflemek istiyorum

**Şunu yazın:** Değişiklik planı   
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Koşullu erişimde istemci uygulamaları koşulunun GA sürümü ile, yeni ilkeler artık varsayılan olarak tüm istemci uygulamalarına uygulanır. Buna eski kimlik doğrulama istemcileri dahildir. Mevcut ilkeler değişmeden kalır, ancak ilke tarafından hangi istemci uygulamalarının uygulanacağını kolayca görmek için, var olan ilkelerden *yapılandırma Evet/Hayır* geçişi kaldırılır. 

Yeni bir ilke oluştururken, hala eski kimlik doğrulaması kullanan kullanıcıları ve hizmet hesaplarını dışlediğinizden emin olun; Bunu yapmazsanız, engellenecektir. [Daha fazla bilgi edinin](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Yaklaşan SCıM uyumluluk düzeltmeleri

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Azure AD sağlama hizmeti, uygulamalarla tümleştirme için SCıM standardını kullanır. SCıM standardı uygulamamız gelişiyor ve bir kaynaktaki "etkin" özelliğini ayarlama ve düzeltme eki gerçekleştirme davranışımızın bir üzerinde değişiklik yapmayı bekledik. [Daha fazla bilgi edinin](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure yönetim portalı 'nda Grup sahibi ayarı değiştirilecek

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Gruplar genel ayar sayfasında sahip ayarları, Azure yönetim portalı ve erişim panelindeki sınırlı bir kullanıcı grubuyla sahip atama ayrıcalıklarını kısıtlayacak şekilde yapılandırılabilir. Yakında, bu iki UX portalında değil de grup sahibi ayrıcalığı atama imkanına sahip olmakla kalmaz, uç noktalar arasında PowerShell ve Microsoft Graph gibi tutarlı davranışlar sağlamak için de arka uçta ilke uygulayabilir. 

Kullanmayan müşteriler için geçerli ayarı devre dışı bırakacağız ve bir sonraki birkaç ayda Grup sahibi ayrıcalığı için kullanıcıları kapsam seçeneğiyle bir seçenek sunacak. Grup ayarlarını güncelleştirme hakkında yönergeler için bkz. [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)kullanarak Grup bilgilerinizi düzenleme.

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory kayıt hizmeti, TLS 1,0 ve 1,1 için destek bitidir

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Cihaz kaydı ve yönetimi  
**Ürün yeteneği:** Platformunun

Aktarım Katmanı Güvenliği (TLS) 1,2 ve güncelleştirme sunucuları ve istemcileri yakında Azure Active Directory Cihaz Kaydı hizmetiyle iletişim kuracak. Azure AD cihaz kayıt hizmeti ile iletişim için TLS 1,0 ve 1,1 desteği devre dışı bırakılacak:
- 31 Ağustos 2020 ' de tüm bağımsız bulutlarda (GCC High, DoD vb.)
- 30 Ekim 2020 ' de tüm ticari bulutlarda

Azure AD kayıt hizmeti için TLS 1,2 hakkında [daha fazla bilgi edinin](../devices/reference-device-registration-tls-1-2.md) .

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD oturum açma günlüklerinde görünen Iş için Windows Hello

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 
Iş için Windows Hello, son kullanıcıların Windows makinelerde bir hareket (PIN veya biyometri gibi) oturum açmasına olanak tanır. Azure AD yöneticileri, şirket içi oturum açma işlemleri için Windows Hello 'Yu, bir kuruluşun yolculuğunun bir parçası olarak passwordless kimlik doğrulamasına ayırmak isteyebilir. 

Yöneticiler artık, Azure portal Azure AD Sign-Ins dikey penceresinde bir Windows oturum açma olayı için kimlik doğrulama ayrıntıları sekmesini denetleyerek Windows kimlik doğrulamasının Iş için Windows Hello kullanıp kullanmadığını görebilir. Iş için Windows Hello kimlik doğrulamaları, kimlik doğrulama yöntemi alanına "WindowsHelloForBusiness" içerecektir. Sign-In günlüklerini yorumlama hakkında daha fazla bilgi için lütfen [oturum açma günlükleri belgelerine](../reports-monitoring/concept-sign-ins.md)bakın.
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Grup silme davranışı ve performans iyileştirmeleri için düzeltmeler

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Daha önce, bir grup "kapsam içi" iken "kapsam dışı" olarak değiştirildiğinde ve değişiklik tamamlanmadan önce bir yöneticinin tıklamış olması durumunda, Grup nesnesi silinmeyecektir. Artık grup nesnesi kapsam dışına geçtiğinde (devre dışı, silinmiş, atanmamış veya kapsam filtresi geçirmediği) hedef uygulamadan silinecek. [Daha fazla bilgi edinin](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Genel Önizleme: Yöneticiler artık erişim incelemesi oluştururken gözden geçirenlere e-postaya özel içerik ekleyebilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Erişim Incelemeleri  
**Ürün yeteneği:** Kimlik yönetimi
 
Yeni bir erişim incelemesi oluşturulduğunda, gözden geçiren, erişim incelemesini tamamlaması için isteyen bir e-posta alır. Müşterilerimizin birçoğu, e-postaya iletişim bilgileri gibi özel içerik ekleme ve gözden geçiriciye kılavuzluk etmek için diğer ek destekleyici içerik gibi bir özellik istiyor. 

Artık genel önizlemede sunulan Yöneticiler, Azure AD erişim Incelemelerinin "Gelişmiş" bölümünde içerik ekleyerek gözden geçirenlere gönderilen e-postada özel içerik belirtebilir. Erişim gözden geçirmeleri oluşturma hakkında yönergeler için bkz. [Azure AD erişim gözden geçirmeleri içindeki gruplar ve uygulamalar için erişim gözden geçirmesi oluşturma](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Tek sayfalı uygulamalar için yetkilendirme kodu akışı kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Geliştirici deneyimi
 
Safari ıOP gibi modern tarayıcı tanımlama bilgisi kısıtlamaları nedeniyle, maça 'ın SSO 'yu korumak için örtük akış yerine yetkilendirme kodu akışını kullanması ve MSAL.js v 2. x de yetkilendirme kodu akışını desteklemeye devam edecektir. 

SPA 'nizi "Spa" türüne ve kimlik doğrulama kod akışını kullanacak şekilde güncelleştirebilmeniz için Azure portal ilgili güncelleştirmeler vardır. Daha fazla bilgi için bkz. [oturum açma kullanıcıları ve kimlik doğrulama kodu akışını kullanarak bir JAVASCRIPT Spa 'da erişim belirteci edinme](../develop/quickstart-v2-javascript-auth-code.md) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD Uygulama Ara Sunucusu artık Uzak Masaüstü Hizmetleri Web Istemcisini destekliyor

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama proxy 'Si  
**Ürün yeteneği:** Access Control

Azure AD Uygulama Ara Sunucusu artık Uzak Masaüstü Hizmetleri (RDS) Web Istemcisini desteklemektedir. RDS Web istemcisi, kullanıcıların Microsoft Edge, Internet Explorer 11, Google Chrome vb. gibi HTLM5 özellikli bir tarayıcıdan uzak masaüstü altyapısına erişmesini sağlar. Kullanıcılar, uzak uygulamalarla veya masaüstü bilgisayarlardan her yerden yerel bir cihazla etkileşim kurabilir. Azure AD Uygulama Ara Sunucusu kullanarak, tüm zengin istemci uygulamaları için ön kimlik doğrulama ve koşullu erişim ilkelerini zorunlu tutarak RDS dağıtımınızın güvenliğini artırabilirsiniz. Rehberlik için bkz. [Azure AD uygulama ara sunucusu Ile uzak masaüstü yayımlama](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Yeni nesil Azure AD B2C Kullanıcı akışları genel önizlemede

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 
Basitleştirilmiş Kullanıcı akış deneyimi, önizleme özellikleriyle Özellik eşliği sağlar ve tüm yeni özelliklerin ana adresidir. Kullanıcılar aynı kullanıcı akışındaki yeni özellikleri etkinleştirerek, her yeni özellik sürümüyle birden çok sürüm oluşturma gereksinimini azaltır. Son olarak, yeni, Kullanıcı dostu UX, Kullanıcı akışlarının seçimini ve oluşturulmasını basitleştirir. Şimdi [bir Kullanıcı akışı oluşturarak](../../active-directory-b2c/tutorial-create-user-flows.md)deneyin. 

Kullanıcılar akışları hakkında daha fazla bilgi için, [Azure Active Directory B2C Içindeki Kullanıcı akışı sürümleri](../../active-directory-b2c/user-flow-versions.md)bölümüne bakın.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Temmuz 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Haziran 2020 ' de, Federasyon desteği ile App Gallery 'de aşağıdaki 55 yeni uygulamaları ekledik:

[Clap, uygulamalı](http://www.rmit.com.ar/), [appreiz](https://microsoftteams.appreiz.com/), [inextor Kasası](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [templamy OpenID Connect](https://app.templafy.com/), [peterconnect Alım](https://msteams.peterconnects.com/), [alogüm](https://appfusions.alohacloud.com/auth), güetler, [DENETIM Kulesi](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [Coins oluşturma bulutu](https://sso.coinsconstructioncloud.com/#login/), [medxnote MT](https://task.teamsmain.medx.im/authorization), [yansıma kt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [mycompanyarchive](https://login.mycompanyarchive.com/), [greminders](https://app.greminders.com/o365-oauth), [titandosya](../saas-apps/titanfile-tutorial.md), [WOOTRIC](../saas-apps/wootric-tutorial.md), [Solarwinds cumon](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Dizin Hizmetleri](../saas-apps/opentext-directory-services-tutorial.md), [Datasite](../saas-apps/datasite-tutorial.md), [Blogin](../saas-apps/blogin-tutorial.md), [intsıghts](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), [TextLine](../saas-apps/textline-tutorial.md), [bulut akademik My-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [topluluk Spark](../saas-apps/community-spark-tutorial.md), [ChatWork](../saas-apps/chatwork-tutorial.md), [cloudsign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [smarthr](https://smarthr.jp/), [Numlyengage™](../saas-apps/numlyengage-tutorial.md), [Michigan Data Hub çoklu oturum açma](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [Çıkış](../saas-apps/egress-tutorial.md), [sendgüvenle](../saas-apps/sendsafely-tutorial.md), [eletive](https://app.eletive.com/), [sağ taraftaki siber güvenlik adı](https://right-hand.ai/), [EYC kurumsal kimlik doğrulaması](https://enterprise.fyde.com/), verme [,](../saas-apps/verme-tutorial.md) [lenses.io](../saas-apps/lensesio-tutorial.md), [momenta](../saas-apps/momenta-tutorial.md), [Uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [cloudcords](../saas-apps/cloudcords-tutorial.md), [Tellme bot](https://tellme365liteweb.azurewebsites.net/), [feire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Bağlayıcısı](https://www.strata.io/identity-fabric/), [smartokul (okul yönetim sistemi)](https://smartschoolz.com/login), [Zepto-Intelligent timesaklanması](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [skedda](../saas-apps/skedda-tutorial.md), [whosonlocation](../saas-apps/whos-on-location-tutorial.md), [Coire](../saas-apps/coggle-tutorial.md), [Kemp Loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack çoklu oturum açma](../saas-apps/browserstack-single-sign-on-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz. https://aka.ms/AppsTutorial

Uygulamanızı Azure AD uygulama galerisinde listelemek için lütfen buradaki ayrıntıları okuyun https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Tüm kapsamlar genelinde rol atamalarını görüntüleme ve bunları bir CSV dosyasına indirme yeteneği

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Azure AD rolleri  
**Ürün yeteneği:** Access Control
 
Artık Azure AD portalındaki "roller ve Yöneticiler" sekmesinde bir rolün tüm kapsamları genelinde rol atamalarını görüntüleyebilirsiniz. Ayrıca, her rol için bu rol atamalarını bir CSV dosyasına indirebilirsiniz. Rol atamalarını görüntüleme ve ekleme hakkında yönergeler için, bkz. [Azure Active Directory yönetici rollerini görüntüleme ve atama](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication yazılım geliştirme (Azure MFA SDK) kullanımdan kaldırılıyor

**Şunu yazın:** Kullanım dışı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Azure Multi-Factor Authentication yazılım geliştirme (Azure MFA SDK), 14 Kasım 2018 ' 2017 de ilk kez duyuruldu. Microsoft, 30 Eylül 2020 tarihinde geçerli olan SDK hizmetini kapatacak. SDK 'ya yapılan çağrılar başarısız olur.

Kuruluşunuz Azure MFA SDK 'sını kullanıyorsa, 30 Eylül 2020 ' e kadar geçiş yapmanız gerekir:
- MıM için Azure MFA SDK: SDK 'Yı MıM ile kullanıyorsanız, bu [yönergeleri](/microsoft-identity-manager/working-with-mfaserver-for-mim)IZLEYEREK Azure MFA sunucusuna geçiş yapmanız ve PRIVILEGED Access Management (Pam) etkinleştirmeniz gerekir.   
- Özelleştirilmiş uygulamalar için Azure MFA SDK 'Sı: uygulamanızı Azure AD 'ye tümleştirmeyi düşünün ve MFA 'yı zorlamak için koşullu erişim kullanın. Başlamak için bu [sayfayı](../manage-apps/plan-an-application-integration.md)gözden geçirin. 

---

## <a name="june-2020"></a>Haziran 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Koşullu erişim ilkesinde Kullanıcı risk koşulu

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 

Azure AD koşullu erişim ilkesinde Kullanıcı risk desteği, birden çok Kullanıcı risk tabanlı ilke oluşturmanıza olanak sağlar. Farklı kullanıcılar ve uygulamalar için farklı en düşük Kullanıcı risk düzeyleri gerekli olabilir. Kullanıcı riskine bağlı olarak, erişimi engellemek, Multi-Factor Authentication, güvenli parola değişikliği yapmak veya ek denetim gibi oturum ilkesini zorlamak için Microsoft Cloud App Security yeniden yönlendirmek üzere ilkeler oluşturabilirsiniz.

Kullanıcı risk koşulu, bir P2 teklifi olan Azure kimlik koruması kullandığından Azure AD Premium P2 gerektirir. Koşullu erişim hakkında daha fazla bilgi için [Azure AD koşullu erişim belgelerine](../conditional-access/index.yml)bakın.

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO artık istendiğinde, SPNameQualifier 'nin ayarlanması gereken uygulamaları destekliyor

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO
 
Bazı SAML uygulamaları, istendiğinde onay konusunun döndürülmesini gerektirir. İstek NameID ilkesinde bir SPNameQualifier istendiğinde Azure AD doğru şekilde yanıt veriyor. Bu, SP tarafından başlatılan oturum açma için de çalışır ve IDP tarafından başlatılan oturum açma işlemi uygulanır.  Azure Active Directory 'de SAML protokolü hakkında daha fazla bilgi edinmek için bkz. [tek Sign-On SAML Protokolü](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B Işbirliği, Azure Kamu kiracılarında MSA ve Google kullanıcılarını davet etme desteği

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 

B2B işbirliği özelliklerini kullanan Azure Kamu kiracılar artık Microsoft veya Google hesabı olan kullanıcıları davet edebilir. Kiracınızın bu özellikleri kullanıp kullanmızdan emin olmak için, [Azure ABD kamu kiracımda B2B işbirliğinin kullanılabilir olup olmadığını nasıl anlayabilirim?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant) konusundaki yönergeleri izleyin.

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 'deki Kullanıcı nesnesi artık externalUserState ve externalUserStateChangedDateTime özelliklerini içerir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 

ExternalUserState ve externalUserStateChangedDateTime özellikleri, davetlerini henüz kabul etmemiş olan davet edilen B2B konuklarının yanı sıra bazı gün sayısından sonra davetlerini kabul edilmeyen kullanıcıları silme gibi derleme Otomasyonu 'nu bulmak için de kullanılabilir. Bu özellikler artık MS Graph v1 'de kullanılabilir. Bu özellikleri kullanma hakkında rehberlik için [Kullanıcı kaynak türü](/graph/api/resources/user)' ne bakın.
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD koşullu erişim 'de kimlik doğrulama oturumlarını yönetme genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Kimlik doğrulama oturumu yönetimi özellikleri, kullanıcılarınızın ortamınızda daha fazla güvenlik ve esneklik sunmak için oturum açma kimlik bilgilerini ve tarayıcıları kapatıp yeniden açtıktan sonra kimlik bilgileri sağlamaları gerekip gerekmediğini yapılandırmanıza olanak tanır.
 
Ayrıca, kimlik doğrulama oturumu yönetimi yalnızca Azure AD 'ye katılmış, hibrit Azure AD 'ye katılmış ve Azure AD 'ye kayıtlı cihazlarda yalnızca birinci faktör kimlik doğrulamasına uygulamak için kullanılır. Artık, kimlik doğrulama oturumu yönetimi MFA için de geçerlidir. Daha fazla bilgi için bkz. [koşullu erişimle kimlik doğrulama oturumu yönetimini yapılandırma](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Haziran 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Haziran 2020 ' de, federasyon desteğiyle App Gallery 'de aşağıdaki 29 yeni uygulamayı ekledik:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [finketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI denetleyicisi](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/), [Merkez planlayıcısı](../saas-apps/hub-planner-tutorial.md), [ansıra-iş ortağı go-to-market](https://ansira.com/technology/channel-engagement), [bulutta IBM dijital iş Otomasyonu](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [kişi fiziksel güvenliği](../saas-apps/kisi-physical-security-tutorial.md), [viewpointon](https://team.viewpoint.com/), [ıntelligencebank](../saas-apps/intelligencebank-tutorial.md), [pyölçümler](../saas-apps/pymetrics-tutorial.md), [sıfır](https://www.teamzero.com/), [ınstation](https://instation.invillia.com/), [EDX for Business SAML 2,0 tümleştirmesi](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [pkııgmpplatform](https://platform.pkisigning.nl/), [siteıntel](../saas-apps/siteintel-tutorial.md), [alan kimliği](../saas-apps/field-id-tutorial.md), [Curricula SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [myuyumluluk bulutu](https://cloud.metacompliance.com/), [smallstep SSH](https://smallstep.com/sso-ssh/)  

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz https://aka.ms/AppsTutorial . Uygulamanızı Azure AD uygulama galerisinde listelemek için lütfen buradaki ayrıntıları okuyun: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Dış kimlikler self servis kaydolma için API bağlayıcıları artık genel önizlemede

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Dış kimlikler API bağlayıcıları, self servis kaydolma 'yı dış bulut sistemleriyle tümleştirmenizi sağlayan Web API 'Lerinden yararlanmanızı sağlar. Bu, artık bulut tabanlı özel iş akışlarını tetiklemek için bir kayıt akışında özel adımlar olarak Web API 'Leri çağırabileceğiniz anlamına gelir. Örneğin, API bağlayıcılarını kullanarak şunları yapabilirsiniz:

- Özel bir onay iş akışlarıyla tümleştirin.
- Kimlik doğrulama gerçekleştir
- Kullanıcı giriş verilerini doğrulama
- Kullanıcı özniteliklerinin üzerine yaz
- Özel iş mantığını Çalıştır

API bağlayıcılarıyla ilgili tüm deneyimler hakkında daha fazla bilgi için bkz. [API bağlayıcılarını özelleştirmek ve genişletmek için API bağlayıcıları kullanma](../external-identities/api-connectors-overview.md)veya [Web API 'si tümleştirmelerine sahip dış kimlikler self servis kaydolma](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>İsteğe bağlı sağlayın ve uygulamaları saniyeler içinde alın

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Azure AD sağlama hizmeti şu anda döngüsel olarak çalışır. Hizmet, her 40 dakikada bir çalışır. [İsteğe bağlı sağlama özelliği](https://aka.ms/provisionondemand) , bir Kullanıcı seçmenizi ve saniyeler içinde sağlamanızı sağlar. Bu özellik, sağlama döngüsünün yeniden başlatılmasına zorlamak için yeniden başlatma işlemi yapmanıza gerek kalmadan sağlama sorunlarını hızla giderebilmenizi sağlar. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Graph 'ta Azure AD Yetkilendirme Yönetimi 'ni kullanmaya yönelik yeni izin

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Yetkilendirme Yönetimi
 
Yeni bir Temsilcili izin EntitlementManagement. Read. All artık Microsoft Graph Beta 'da yetkilendirme yönetimi API 'SI ile kullanılabilir. Kullanılabilir API 'Ler hakkında daha fazla bilgi edinmek için bkz. [Azure AD yetkilendirme yönetimi API 'si Ile çalışma](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true).

---

### <a name="identity-protection-apis-available-in-v10"></a>V 1.0 'da kimlik koruması API 'Leri kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
RiskyUsers ve riskDetections Microsoft Graph API 'Leri artık genel kullanıma sunulmuştur. Artık v 1.0 uç noktasında kullanılabilir olduğuna göre, bunları üretimde kullanmak için davet ediyoruz. Daha fazla bilgi için lütfen [Microsoft Graph belgelerine](/graph/api/resources/identityprotectionroot)göz atın.
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Microsoft 365 gruplara ilke uygulamak için duyarlılık etiketleri genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş
 

Artık duyarlılık etiketleri oluşturabilir ve Gizlilik (genel veya özel) ve dış Kullanıcı erişimi ilkesi gibi Microsoft 365 gruplarına ilke uygulamak için etiket ayarlarını kullanabilirsiniz. Gizli olması için Gizlilik ilkesiyle birlikte bir etiket ve Konuk kullanıcılar eklemeye izin vermek için ise harici Kullanıcı erişim ilkesi oluşturabilirsiniz. Bir Kullanıcı bu etiketi bir gruba uygularsa, Grup özel olur ve gruba hiçbir Konuk kullanıcının eklenmesine izin verilmez. 

Duyarlılık etiketleri, iş açısından kritik verilerinizi korumak ve grupları uyumlu ve güvenli bir biçimde yönetmenize olanak tanımak için önemlidir. Duyarlılık etiketlerini kullanma hakkında yönergeler için, [Azure Active Directory (Önizleme) Microsoft 365 gruplara duyarlılık etiketleri atama](../enterprise-users/groups-assign-sensitivity-labels.md)bölümüne bakın.
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Azure AD Premium müşterileri için Microsoft Identity Manager Destek güncelleştirmeleri

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Microsoft Identity Manager  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Azure desteği, Microsoft Identity Manager 2016 için genişletilmiş destek sonuna kadar Microsoft Identity Manager 2016 ' nin Azure AD tümleştirme bileşenleri ile sunulmaktadır. [Microsoft Identity Manager kullanarak Azure AD Premium müşterileri Için destek güncelleştirmesinde](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)daha fazla bilgi edinin.

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>SSO talep yapılandırmasındaki grup üyeliği koşullarının kullanımı artırıldı

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO
 
Daha önce, tek bir uygulama yapılandırması içindeki grup üyeliğine göre talepleri koşullu olarak değiştirdiğinizde kullanabileceğiniz grupların sayısı 10 ile sınırlıdır. SSO talep yapılandırmasındaki grup üyeliği koşullarının kullanılması artık maksimum 50 gruba yükselmiştir. Talepleri yapılandırma hakkında daha fazla bilgi için bkz. [Kurumsal uygulamalar SSO talep yapılandırması](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Şirket markalaması 'nda oturum açma sayfası metin bileşeninde temel biçimlendirmeyi etkinleştirme.

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Azure AD/Microsoft 365 oturum açma deneyiminde Şirket markası işlevselliği, müşterinin kalın yazı tipi, altı çizili ve italik gibi köprü ve basit biçimlendirme eklemesine izin verecek şekilde güncelleştirilmiştir. Bu işlevi kullanma hakkında yönergeler için bkz. [kuruluşunuzun Azure Active Directory oturum açma sayfasına marka ekleme](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Performans iyileştirmeleri sağlama

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Sağlama Hizmeti, [artımlı bir döngüyü](../app-provisioning/how-provisioning-works.md#incremental-cycles) tamamlamaya yönelik süreyi azaltacak şekilde güncelleştirilmiştir. Bu, kullanıcıların ve grupların uygulamalarına daha hızlı sağlandığı anlamına gelir. 6/10/2020 sonra oluşturulan tüm yeni sağlama işleri, performans iyileştirmelerinden otomatik olarak faydalanır. Performans iyileştirmelerinden faydalanmak için 6/10/2020 ' den önce sağlama için yapılandırılmış tüm uygulamaların, 6/10/2020 ' den sonra yeniden başlatılması gerekir. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>ADAL ve MS Graf eşliği kullanımdan kaldırılması duyurusu

**Şunu yazın:** Kullanım dışı  
**Hizmet kategorisi:** yok  
**Ürün yeteneği:** Cihaz yaşam döngüsü yönetimi

Artık Microsoft kimlik doğrulama kitaplıkları (MSAL) kullanılabilir olduğuna göre, artık Azure Active Directory kimlik doğrulama kitaplıklarına (ADAL) yeni özellikler ekleyeceğiz ve 30 Haziran 2022 ' de güvenlik düzeltme eklerini sona erdirmek için. MSAL ' ye geçiş hakkında daha fazla bilgi için bkz. [uygulamaları Microsoft kimlik doğrulama kitaplığı 'Na geçirme (msal)](../develop/msal-migration.md).

Ayrıca, tüm Azure AD Graph işlevlerini MS Graph aracılığıyla kullanılabilir hale getirmek için çalışmayı tamamladık. Bu nedenle, Azure AD Graph API 'Leri 30 Haziran 2022 ' e kadar yalnızca bugdüzeltmesini ve güvenlik düzeltmelerini alacak. Daha fazla bilgi için bkz. [Microsoft kimlik doğrulama kitaplığı ve apı Microsoft Graph kullanmak için uygulamalarınızı güncelleştirme](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
## <a name="may-2020"></a>Mayıs 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>SignIns, riskyUsers ve riskDetections API 'Lerinde özelliklerin kullanımdan kaldırılması

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Şu anda numaralandırılmış türler, riskType özelliğini hem riskDetections API hem de Riskyuseriztem (önizlemede) içinde temsil etmek için kullanılır. Numaralandırılmış türler Ayrıca signIns API 'sindeki riskEventTypes özelliği için de kullanılır. İleri giderek bu özellikleri dizeler olarak temsil edeceğiz. 

Müşteriler Beta riskDetections ve Riskyuseriztem API 'sindeki riskEventType özelliğine ve 9 Eylül 2020 ' e kadar Beta signIns API 'sindeki riskEventTypes_v2 özelliğine geçiş yapılmalıdır. Bu tarihte, geçerli riskType ve riskEventTypes özelliklerini devre dışı bırakılacağız. Daha fazla bilgi için [Microsoft Graph risk olayı özelliklerine ve kimlik koruması API 'lerine yönelik değişikliklere](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)bakın.

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>SignIns v 1.0 API 'sindeki riskEventTypes özelliğinin kullanımdan kaldırılması Microsoft Graph

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Numaralandırılmış türler Microsoft Graph Eylül 2020 ' deki risk olayı özelliklerini temsil edildiğinde dize türlerine geçiş yapar. Bu değişiklik, önizleme API 'Lerini etkilemenin yanı sıra üretim içi signIns API 'sini de etkileyecektir.

SignIns v 1.0 API 'sine yeni bir riskEventsTypes_v2 (String) özelliği ekledik. Geçerli riskEventTypes (enum) özelliğini 11 Haziran 2022 ' de kullanımdan kaldıracağız ve Microsoft Graph kullanım dışı bırakma ilkenize uygun şekilde devre dışı bırakacağız. Müşteriler, 11 Haziran 2022 ' e kadar v 1.0 signIns API 'sindeki riskEventTypes_v2 özelliğine geçiş yapılmalıdır. Daha fazla bilgi için [Microsoft Graph signIns v 1.0 API 'Sindeki riskEventTypes özelliğinin kullanımdan kaldırılması](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)bölümüne bakın.

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA e-posta bildirimlerinde yaklaşan değişiklikler

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 

Cloud MFA için e-posta bildirimlerinde aşağıdaki değişiklikleri yapıyoruz:

E-posta bildirimleri şu adresten gönderilir: azure-noreply@microsoft.com ve msonlineservicesteam@microsoftonline.com . Dolandırıcıların engellemesini kaldırmak için gerekli adımları daha iyi göstermek üzere sahtekarlık uyarı e-postalarının içeriğini güncelleştiriyoruz.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Azure Active Directory eşitlenmediği için, Microsoft ekiplerine erişemeyen Federasyon etki alanlarında bulunan kullanıcılar için yeni self servis kaydolun.

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 

Şu anda, Azure AD 'de Federasyon etki alanlarında bulunan ancak kiracı ile eşitlenmemiş olan kullanıcılar takımlara erişemez. Haziran 'un sonundan itibaren, bu yeni özellik, var olan e-posta doğrulanmış kaydolma özelliğini genişleterek bunu yapabilmesini sağlayacak. Bu, Federasyon IDP 'de oturum açabilen ancak henüz Azure KIMLIĞINDE bir kullanıcı nesnesine sahip olmayan ve takımlar için otomatik olarak oluşturulmuş bir kullanıcı nesnesine sahip olmayan kullanıcılara izin verir. Kullanıcı nesneleri, "self servis kaydolma" olarak işaretlenir. Bu, yönetilen etki alanındaki kullanıcıların yapabileceği ve aynı bayrak kullanılarak denetlenebileceği, var olan bir e-posta doğrulama özelliğinin bir uzantısıdır. Bu değişiklik, aşağıdaki iki ay boyunca kullanıma alınır. Belge güncelleştirmelerini [buradan](../enterprise-users/directory-self-service-signup.md)izleyin.
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Yaklaşan Düzeltme: Azure Kamu Bulutu için OıDC keşif belgesi, doğru grafik uç noktalarına başvuracak şekilde güncelleştiriliyor.

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Bağımsız bulutlar  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Haziran 'dan itibaren, [Azure Kamu Bulutu](../develop/authentication-national-cloud.md) uç noktasındaki (login.microsoftonline.us) OIDC keşif belgesi [Microsoft Identity platform ve OpenID Connect Protokolü](../develop/v2-protocols-oidc.md) , doğru [National Cloud Graph](/graph/deployments) uç noktasını ( https://graph.microsoft.us veya https://dod-graph.microsoft.us) belirtilen kiracıya göre) döndürmeye başlayacaktır.  Şu anda hatalı grafik uç noktası (graph.microsoft.com) "msgraph_host" alanı sağlar.  

Bu hata düzeltmesinin süresi yaklaşık 2 aydan fazla olacak şekilde alınacaktır.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Kamu kullanıcıları artık login.microsoftonline.com üzerinde oturum açamaz

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Bağımsız bulutlar  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
1 Haziran 2018 ' de, Azure Kamu için resmi Azure Active Directory (Azure AD) yetkilisi ' den ' a değiştirilir https://login-us.microsoftonline.com https://login.microsoftonline.us . Bir Azure Kamu kiracısı içindeki bir uygulamaya sahipseniz, uygulamanızı. us uç noktasındaki kullanıcıları imzalamak için güncelleştirmeniz gerekir.

Azure AD, Mayıs 5 ' ten itibaren, Azure Kamu kullanıcılarının genel uç nokta (microsoftonline.com) kullanılarak Azure Kamu kiracılarında barındırılan uygulamalarda oturum açmasını engelleyen bitiş noktası değişikliğini zorlamaya başlayacak. Etkilenen uygulamalar, AADSTS900439-USGClientNotSupportedOnPublicEndpoint hatasını görmeye başlayacaktır. 

Bu değişikliğin, Haziran 2020 ' de tüm uygulamalarda tamamlanamamış olması beklendiğinden aşamalı olarak bir dağıtım olması gerekir. Daha fazla ayrıntı için lütfen [Azure Kamu blog gönderisine](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)bakın.

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML çoklu oturum kapatma isteği artık NameID 'yi doğru biçimde gönderiyor

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Kullanıcı, oturum kapatma (örneğin, Uygps portalındaki) oturumunu tıklattığında, Azure AD kullanıcı oturumunda etkin olan ve oturum kapatma URL 'SI yapılandırılmış her uygulamaya bir SAML Single Logout iletisi gönderir. Bu iletiler kalıcı biçimde bir NameID içerir.

Özgün SAML oturum açma belirteci, NameID (örn. e-posta/UPN) için farklı bir biçim kullanıyorsa, SAML uygulaması oturum kapatma iletisindeki NameID 'yi mevcut bir oturumla ilişkilendiremiyor (her iki iletide kullanılan Nameıds farklı olduğu için), bu da oturum kapatma iletisinin SAML uygulaması ve Kullanıcı oturum açmış durumda kalmasını sağlar. Bu onarım, oturum kapatma iletisinin uygulama için yapılandırılan NameID ile tutarlı olmasını sağlar.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Karma kimlik yöneticisi rolü artık bulut sağlaması ile kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD bulutu sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
BT yöneticileri yeni "karma yönetici" rolünü, Azure AD Connect bulut sağlaması kurmak için en az ayrıcalıklı rol olarak kullanmaya başlayabilir. Bu yeni rolle, artık bulut sağlamasını ayarlamak ve yapılandırmak için genel yönetici rolünü kullanmanız gerekmez. [Daha fazla bilgi edinin](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Mayıs 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
2020 Mayıs 'ta, federasyon desteğiyle uygulama galerimize aşağıdaki 36 yeni uygulamaları ekledik:

[Moula](https://moula.com.au/pay/merchants), [gözetimi ypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [tacklebox](http://www.tacklebox.app/), [Powell ekipleri](https://powell-software.com/en/powell-teams-en/), [talentsoft Yardımcısı](https://msteams.talent-soft.com/), [ASC kayıt öngörüleri](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-bağlı](https://b-engaged.se/), [Competella Contact Center çalışma grubu](http://www.competella.com/), [asite](http://www.asite.com/), [ımavals kimliği](https://identity.imagesoftinc.com/), [ibısworld](https://identity.imagesoftinc.com/), [ınsuite](../saas-apps/insuite-tutorial.md), [değişiklik işlemi yönetimi](../saas-apps/change-process-management-tutorial.md), [Cyara CX güvence platformu](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart Global idare](../saas-apps/smart-global-governance-tutorial.md), [PREZI](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [datava Enterprise Service platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [trelica](../saas-apps/trelica-tutorial.md), [Tsysso](../saas-apps/easysso-for-confluence-tutorial.md)for the [Bitbucket](../saas-apps/easysso-for-bitbucket-tutorial.md)için easysso, [Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii](../saas-apps/torii-tutorial.md), [axiad bulutu](../saas-apps/axiad-cloud-tutorial.md), [humanage](../saas-apps/humanage-tutorial.md), [colortokens ztna](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [sharekasa,](../saas-apps/sharevault-tutorial.md) [Vyond](../saas-apps/vyond-tutorial.md), [textexpander](../saas-apps/textexpander-tutorial.md), bir [Ev CRM](../saas-apps/anyone-home-crm-tutorial.md) [,](../saas-apps/ice-contact-center-tutorial.md) [askbağlı](../saas-apps/askspoke-tutorial.md)

Ayrıca, tüm uygulamaların belgelerini buradan bulabilirsiniz https://aka.ms/AppsTutorial .

Uygulamanızı Azure AD uygulama galerisinde listelemek için lütfen buradaki ayrıntıları okuyun https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Koşullu erişim için yalnızca rapor modu genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması

[Azure AD koşullu erişim için yalnızca rapor modu](../conditional-access/concept-conditional-access-report-only.md) , erişim denetimlerini zorlamadan bir ilkenin sonucunu değerlendirmenizi sağlar. Kuruluşunuzda yalnızca rapor ilkelerini test edebilir ve etkinleştirilmeden önce etkilerini anlayabilmeniz, dağıtımın daha güvenli ve kolay olmasını sağlayabilirsiniz. Son birkaç aya göre yalnızca rapor modunun güçlü şekilde benimsenmesini gördük. 26 ' dan fazla kullanıcı zaten rapor ilkesi kapsamında. Günümüzde duyuru sayesinde, yeni Azure AD koşullu erişim ilkeleri varsayılan olarak yalnızca rapor modunda oluşturulur. Bu, ilkelerinizin oluşturuldukları andan itibaren etkilerini izleyebilmeniz anlamına gelir. Ayrıca, MS Graph API 'Lerini kullananlar için [yalnızca rapor ilkelerini](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) de daha fazla yönetebilirsiniz. 

---

### <a name="self-service-sign-up-for-guest-users"></a>Konuk kullanıcılar için self servis kaydolma

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Azure AD 'de dış kimliklerle, kuruluşunuzun dışındaki kişilerin, tercih ettikleri kimliği kullanarak oturum açmalarına izin vererek uygulamalarınıza ve kaynaklarınıza erişmesini sağlayabilirsiniz. Bir uygulamayı dış kullanıcılarla paylaşırken, uygulamaya erişmesi gereken her zaman önceden bilmiyor olabilirsiniz. [Self servis kaydolma](../external-identities/self-service-sign-up-overview.md)sayesinde, konuk kullanıcıların kaydolup iş kolu (LOB) Uygulamalarınız için bir Konuk hesabı elde etmesine olanak sağlayabilirsiniz. Kaydolma akışı, Azure AD ve sosyal kimlik kimliklerini destekleyecek şekilde oluşturulabilir ve özelleştirilebilir. Kayıt sırasında Kullanıcı hakkında ek bilgiler de toplayabilirsiniz.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Koşullu erişim öngörüleri ve raporlama çalışma kitabı genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması

[Öngörüler ve raporlama çalışma kitabı](../conditional-access/howto-conditional-access-insights-reporting.md) , yöneticilere kiracınızda Azure AD koşullu erişiminin Özet görünümünü sağlar. Tek bir ilke seçme özelliği sayesinde, yöneticiler her bir ilkenin ne yaptığını daha iyi anlayabilir ve değişiklikleri gerçek zamanlı olarak izleyebilir. Çalışma kitabı, Azure Izleyici 'de depolanan verileri akışlar ve [Bu yönergeleri izleyerek](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)birkaç dakika içinde ayarlayabilirsiniz. Panoyu daha fazla bulunabilir hale getirmek için Azure AD koşullu erişim menüsündeki yeni Öngörüler ve Raporlama sekmesine taşıdık.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Koşullu erişimin ilke ayrıntıları dikey penceresi genel önizlemede

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Yeni [ilke ayrıntıları dikey](../conditional-access/troubleshoot-conditional-access.md) penceresi, koşullu erişim ilkesi değerlendirmesi sırasında bulunan atamaları, koşulları ve denetimleri görüntüler. Oturum açma ayrıntılarının koşullu erişim veya yalnızca rapor sekmelerinde bulunan bir satırı seçerek dikey pencereye erişebilirsiniz.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph dizin nesneleri için yeni sorgu özellikleri genel önizlemede

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** MS Graph **ürün yeteneği:** geliştirici deneyimi

Microsoft Graph dizin nesneleri API 'Leri için yeni yetenekler tanıtılmakta, sayı, arama, filtre ve sıralama işlemleri etkinleştiriliyor. Bu, geliştiricilere bellek içi filtreleme ve sıralama gibi geçici çözümler olmadan dizin nesnelerinizi hızlı bir şekilde sorgulama olanağı sunar. Bu [blog gönderisine](https://aka.ms/CountFilterMSGraphAAD)daha fazla bilgi edinin.

Şu anda genel önizleme aşamasındadır ve geri bildirim aranıyor. Lütfen bu [kısa anketle](https://aka.ms/MsGraphAADSurveyDocs)yorumlarınızı gönderin.

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Microsoft Graph API (Beta) kullanarak SAML tabanlı çoklu oturum açmayı yapılandırma

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO
 
Beta 'daki MS Graph API 'Leri kullanılarak Azure AD galerisinden bir uygulama oluşturma ve yapılandırma desteği artık kullanılabilir. Bir uygulamanın birden çok örneği için SAML tabanlı çoklu oturum açmayı ayarlamanız gerekiyorsa, [SAML tabanlı çoklu oturum açma yapılandırmasını otomatikleştirmek](/graph/application-saml-sso-configure-api)Için Microsoft Graph API 'lerini kullanarak zamandan tasarruf edin.
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Mayıs 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [Kuruluşa göre New Relic](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML belirteci şifrelemesi genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO
 
[SAML belirteci şifrelemesi](../manage-apps/howto-saml-token-encryption.md) , UYGULAMALARıN şifreli SAML onayları alacak şekilde yapılandırılmasını sağlar. Özelliği artık tüm bulutlarda genel kullanıma sunulmuştur.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Uygulama belirteçlerinde grup adı talepleri genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO
 
Bir belirteçte verilen grup talepleri artık yalnızca uygulamaya atanan gruplarla sınırlı olabilir.  Bu özellikle, kullanıcılar çok sayıda grubun üyesi olduğunda ve belirteç boyutu sınırlarını aşmamak açısından önemlidir. Bu yeni özellik hazır olduğunda, [belirteçlere grup adları ekleme](../hybrid/how-to-connect-fed-group-claims.md) özelliği genel kullanıma sunulmuştur.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday geri yazma özelliği artık iş telefonu numarası özniteliklerinin ayarlanmasını destekliyor

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
İş günü geri yazma sağlama uygulamasının artık iş telefonu numarası ve mobil numara özniteliklerinin geri yazma işlemlerini desteklemesi için geliştirilmiştir. E-posta ve Kullanıcı adının yanı sıra, artık Workday geri yazma sağlama uygulamasını Azure AD 'den Workday 'e telefon numarası değerlerini Flow şekilde yapılandırabilirsiniz. Telefon numarası geri yazma özelliğinin nasıl yapılandırılacağı hakkında daha fazla bilgi için, [Workday geri yazma](../saas-apps/workday-writeback-tutorial.md) uygulaması öğreticisine bakın. 

---

### <a name="publisher-verification-preview"></a>Yayımcı doğrulaması (Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Geliştirici deneyimi
 
Yayımcı doğrulaması (Önizleme), yöneticilerin ve son kullanıcıların Microsoft Identity platformu ile tümleştirerek uygulama geliştiricilerinin orijinalliğini anlamalarına yardımcı olur. Ayrıntılar için [Yayımcı doğrulama (Önizleme)](../develop/publisher-verification-overview.md)bölümüne bakın.
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Tek sayfalı uygulamalar için yetkilendirme kodu akışı

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik doğrulama **ürün yeteneği:** geliştirici deneyimi

[Safari IOP gibi modern tarayıcı 3 taraf tanımlama bilgisi kısıtlamaları](../develop/reference-third-party-cookies-spas.md)nedeniyle, maça 'ın SSO 'yu korumak için örtük akış yerine yetkilendirme kodu akışını kullanması gerekecektir; MSAL.js v 2. x artık yetkilendirme kodu akışını destekleyecektir. Azure portal, SPA 'nizi "Spa" türüne ve kimlik doğrulama kod akışını kullanacak şekilde güncelleştirebilmeniz için bunlara karşılık gelen güncelleştirmeler de vardır. Rehberlik için [hızlı başlangıç: Kullanıcı oturum açma ve kimlik doğrulama kod akışını kullanarak bir JAVASCRIPT Spa 'da erişim belirteci edinme](../develop/quickstart-v2-javascript-auth-code.md)bölümüne bakın.

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Cihazlar için geliştirilmiş filtreleme genel önizlemede

**Şunu yazın:** Değiştirilen özellik   
**Hizmet kategorisi:** Cihaz Yönetimi **ürün yeteneği:** cihaz yaşam döngüsü yönetimi
 
Daha önce, kullanabileceğiniz tek filtreler "etkinleştirildi" ve "etkinlik tarihi" olarak değiştirildi. Artık, işletim sistemi türü, JOIN türü, uyumluluk ve daha fazlası dahil olmak üzere [daha fazla özellik için cihaz listenizi filtreleyebilirsiniz](../devices/device-management-azure-portal.md#device-list-filtering-preview). Bu eklemeler belirli bir cihazı konumlandırmayı basitleştirir.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Azure AD B2C için yeni Uygulama kayıtları deneyimi artık genel kullanıma sunuldu

**Şunu yazın:** Değiştirilen özellik   
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Azure AD B2C için yeni Uygulama kayıtları deneyimi artık genel kullanıma sunulmuştur. 

Daha önce, B2C tüketicisine yönelik uygulamalarınızı eski ' uygulamalar ' deneyimini kullanarak uygulamalarınızın geri kalanından ayrı olarak yönetmeniz gerekiyordu. Bu, Azure 'daki farklı yerlerde farklı uygulama oluşturma deneyimleri sunmaktır.

Yeni deneyim tüm B2C uygulama kayıtlarını ve Azure AD uygulama kayıtlarını tek bir yerde gösterir ve bunları yönetmek için tutarlı bir yol sağlar. Azure AD B2C kaynaklarını programlı bir şekilde yönetmek için Microsoft Graph erişimi olan bir uygulamayı veya bir uygulamayı yönetmeniz gerekip gerekmediğini öğrenmek için yalnızca bir yol yapmanız gerekir.

Azure AD B2C hizmetinde gezinerek ve Uygulama kayıtları dikey penceresini seçerek yeni deneyimle ulaşabilirsiniz. Deneyim Azure Active Directory hizmetinden de erişilebilir.

Azure AD B2C Uygulama kayıtları deneyimi, Azure AD kiracılarına yönelik genel [uygulama kayıt deneyimini](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) temel alır, ancak Azure AD B2C için tasarlanmıştır. Eski "uygulamalar" deneyimi gelecekte kullanım dışı olacaktır.

Daha fazla bilgi için [Azure AD B2C Için yeni uygulama kaydı deneyimini](../../active-directory-b2c/app-registrations-training-guide.md)ziyaret edin.

---
## <a name="april-2020"></a>Nisan 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Birleşik güvenlik bilgileri kayıt deneyimi artık genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)

**Ürün yeteneği:** Kimlik güvenliği & koruması

Multi-Factor Authentication (MFA) ve Self-Service parola sıfırlama (SSPR) için Birleşik kayıt deneyimi artık genel kullanıma sunulmuştur. Bu yeni kayıt deneyimi, kullanıcıların MFA ve SSPR 'ye tek bir adım adım işlem halinde kaydolmalarını sağlar. Kuruluşunuz için yeni deneyimi dağıttığınızda, kullanıcılar daha az zaman ve daha az kurtulur kaydedebilir. Blog gönderisine [buradan](https://bit.ly/3etiRyQ)göz atın.

---

### <a name="continuous-access-evaluation"></a>Sürekli erişim değerlendirmesi

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)

**Ürün yeteneği:** Kimlik güvenliği & koruması

Sürekli erişim değerlendirmesi, Azure AD 'de (Kullanıcı hesabı silme gibi) olaylar gerçekleştiğinde Azure AD erişim belirteçlerini kullanan bağlı olan taraflar üzerinde neredeyse gerçek zamanlı ilke uygulanmasını sağlayan yeni bir güvenlik özelliğidir. Bu özelliği ilk olarak takımlar ve Outlook istemcileri için kullanıma sunuyoruz. Daha fazla ayrıntı için lütfen [blogumuzu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) ve  [belgelerimizi](../conditional-access/concept-continuous-access-evaluation.md)okuyun.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS oturum açma: Firstline çalışanları, telefon numarası ve parola olmadan Azure AD ile desteklenen uygulamalarda oturum açabilir

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)

**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Office, geleneksel olmayan kuruluşlara ve birincil iletişim yöntemi olarak e-posta kullanmayan büyük kuruluşlardaki çalışanlara yönelik bir dizi mobil ilk iş uygulaması başlatıyor. Bu uygulamalar, kendi işverenlerinden bir e-posta adresi alamaz, bir bilgisayara veya bu uygulamalara erişim sahibi olmayan çalışanlar, deskuz çalışanları, alan aracılarını veya perakende çalışanları hedefleyebilir. Bu proje, bu çalışanların bir telefon numarası girerek ve bir kodu aşağı girerken iş uygulamalarında oturum açmalarına olanak tanır. Daha fazla ayrıntı için lütfen [yönetici belgelerimize](../authentication/howto-authentication-sms-signin.md) ve [Son Kullanıcı belgelerinize](../user-help/sms-sign-in-explainer.md)bakın.

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>B2B işbirliği kullanan dahili kullanıcıları davet etme

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** EAı

**Ürün yeteneği:**

Mevcut iç hesapların, daha sonra B2B işbirliği kimlik bilgilerini kullanacak şekilde davet edilmesine izin vermek için B2B davetiyesi özelliğini genişlettik. Bu, davet edilen e-posta adresi gibi tipik parametrelere ek olarak Kullanıcı nesnesi davet API 'sine geçirilerek yapılır. Kullanıcının nesne KIMLIĞI, UPN, Grup üyeliği, uygulama ataması, vb. değişmeden kalır, ancak iletme, bir yandan, Davetten önce kullandıkları iç kimlik bilgileri yerine kendi giriş Kiracı kimlik bilgileriyle kimlik doğrulaması yapmak için B2B kullanır. Ayrıntılar için [belgelerine](../external-identities/invite-internal-users.md)bakın.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Koşullu erişim için yalnızca rapor modu genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Koşullu erişim

**Ürün yeteneği:** Kimlik güvenliği & koruması

[Azure AD koşullu erişim için yalnızca rapor modu](../conditional-access/concept-conditional-access-report-only.md) , erişim denetimlerini zorlamadan bir ilkenin sonucunu değerlendirmenizi sağlar. Kuruluşunuzda yalnızca rapor ilkelerini test edebilir ve etkinleştirilmeden önce etkilerini anlayabilmeniz, dağıtımın daha güvenli ve kolay olmasını sağlayabilirsiniz. Son birkaç ay içinde, yalnızca rapor ilkesi kapsamında, 26 ' dan fazla Kullanıcı önceden rapor modunun güçlü şekilde benimsenmesini gördük. Bu duyuru ile yeni Azure AD koşullu erişim ilkeleri varsayılan olarak yalnızca rapor modunda oluşturulur. Bu, ilkelerinizin oluşturuldukları andan itibaren etkilerini izleyebilmeniz anlamına gelir. Ayrıca, MS Graph API 'Lerini kullananlar için, [yalnızca rapor ilkelerini programlı](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true)bir şekilde yönetebilirsiniz. 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Koşullu erişim öngörüleri ve raporlama çalışma kitabı genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Koşullu erişim

**Ürün yeteneği:** Kimlik güvenliği & koruması

Koşullu erişim [öngörüleri ve raporlama çalışma kitabı](../conditional-access/howto-conditional-access-insights-reporting.md) , yöneticilere kiracınızda Azure AD koşullu erişiminin Özet görünümünü sağlar. Tek bir ilke seçme özelliği sayesinde, yöneticiler her bir ilkenin ne yaptığını daha iyi anlayabilir ve değişiklikleri gerçek zamanlı olarak izleyebilir. Çalışma kitabı, Azure Izleyici 'de depolanan verileri akışlar ve [Bu yönergeleri izleyerek](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)birkaç dakika içinde ayarlayabilirsiniz. Panoyu daha fazla bulunabilir hale getirmek için Azure AD koşullu erişim menüsündeki yeni Öngörüler ve Raporlama sekmesine taşıdık.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Koşullu erişimin ilke ayrıntıları dikey penceresi genel önizlemede

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Koşullu erişim

**Ürün yeteneği:** Kimlik güvenliği & koruması

Yeni [ilke ayrıntıları dikey](../conditional-access/troubleshoot-conditional-access.md) penceresi, koşullu erişim ilkesi değerlendirmesi sırasında hangi atamaların, koşulların ve denetimlerin karşılanmasını görüntüler. Oturum açma ayrıntılarının **koşullu erişim** veya **yalnızca rapor** sekmelerinde bulunan bir satırı seçerek dikey pencereye erişebilirsiniz.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Nisan 2020

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Kurumsal uygulamalar

**Ürün yeteneği:** üçüncü taraf tümleştirmesi

2020 Nisan 'da, uygulama galerisine federasyon desteği olan bu 31 yeni uygulamayı ekledik: 

Bol [Ropool Apps](https://www.sincropool.com/), [smartdb](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT tedarik Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [lunnı](https://lunni.fi/), [easysso, jira](../saas-apps/easysso-for-jira-tutorial.md), [sanal eğitim akademik My](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki panosu](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 taşıyıcısı](https://app.mover.io/login), [Konuşmacı](https://speakerengage.com/login.php)katılım, [his](../saas-apps/honestly-tutorial.md), [önemli](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [Alertmedia](../saas-apps/alertmedia-tutorial.md), [gr8 kişileri](../saas-apps/gr8-people-tutorial.md), [pendo](../saas-apps/pendo-tutorial.md), [üst](../saas-apps/highground-tutorial.md)düzey, [uyum](../saas-apps/harmony-tutorial.md), [timetabling çözümleri](../saas-apps/timetabling-solutions-tutorial.md), [Synchronet tıklama](../saas-apps/synchronet-click-tutorial.md), [Empower](https://www.made-in-office.com/en/), [ktes değişiklik bulutu](../saas-apps/fortes-change-cloud-tutorial.md), [Litmus](../saas-apps/litmus-tutorial.md), [grouptalk](https://recorder.grouptalk.com/), [mintify](../saas-apps/frontify-tutorial.md), [MongoDB Cloud](../saas-apps/mongodb-cloud-tutorial.md), [tickitlms öğreni](../saas-apps/tickitlms-learn-tutorial.md), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro üretkenlik Suite](../saas-apps/nitro-productivity-suite-tutorial.md), [Trend Micro Web Security (tmws)](../saas-apps/trend-micro-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>OAuth2PermissionGrant için Delta sorgu desteği genel önizlemeye sunuldu Microsoft Graph

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** MS grafiği

**Ürün yeteneği:** Geliştirici deneyimi

OAuth2PermissionGrant için Delta sorgusu genel önizleme için kullanılabilir! Artık Microsoft Graph sürekli olarak yoklama yapmadan değişiklikleri izleyebilirsiniz. [Daha fazla bilgi edinin.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Kurumsal iletişim için Microsoft Graph Delta sorgu desteği genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** MS grafiği

**Ürün yeteneği:** Geliştirici deneyimi

Kurumsal kişiler için Delta sorgusu genel kullanıma sunulmuştur! Artık, Microsoft Graph sürekli olarak yoklama yapmadan üretim uygulamalarındaki değişiklikleri izleyebilirsiniz. Performansı önemli ölçüde artırmak için, orgContact verilerini Delta sorgusuna sürekli yoklayan mevcut herhangi bir kodu değiştirin. [Daha fazla bilgi edinin.](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Uygulama için Microsoft Graph Delta sorgu desteği genel kullanıma sunuldu

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** MS grafiği

**Ürün yeteneği:** Geliştirici deneyimi

Uygulamalar için Delta sorgusu genel kullanıma sunulmuştur! Artık, Microsoft Graph sürekli olarak yoklama yapmadan üretim uygulamalarındaki değişiklikleri izleyebilirsiniz. Performansı önemli ölçüde artırmak için uygulama verilerini Delta sorgusuna göre sürekli yoklayan mevcut herhangi bir kodu değiştirin. [Daha fazla bilgi edinin.](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Genel önizleme için kullanılabilen yönetim birimlerine yönelik Delta sorgu desteğini Microsoft Graph

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** MS grafiği

**Ürün yeteneği:** Yönetim birimleri için geliştirici deneyimi Delta sorgusu genel önizleme için kullanılabilir! Artık Microsoft Graph sürekli olarak yoklama yapmadan değişiklikleri izleyebilirsiniz. [Daha fazla bilgi edinin.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Yeni Microsoft Graph Beta API 'Lerinde kimlik doğrulama telefon numaralarını ve daha fazlasını yönetme

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** MS grafiği

**Ürün yeteneği:** Geliştirici deneyimi

Bu API 'Ler, kullanıcılarınızın kimlik doğrulama yöntemlerini yönetmeye yönelik bir temel araçtır. Artık MFA ve self servis parola sıfırlama (SSPR) için kullanılan kimlik doğrulayıcılar aracılığıyla programlı olarak kayıt yapabilir ve yönetebilirsiniz. Bu, Azure AD MFA, SSPR ve Microsoft Graph alanlarında en çok istenen özelliklerden biridir. Bu dalgaya yayımladığımız yeni API 'Ler şunları yapabilmenizi sağlar:

- Kullanıcının kimlik doğrulama telefonlarını okuma, ekleme, güncelleştirme ve kaldırma
- Kullanıcının parolasını sıfırlama
- SMS 'yi açma ve kapatma-oturum açma

Daha fazla bilgi için bkz. [Azure AD kimlik doğrulama yöntemleri API 'sine genel bakış](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

---

### <a name="administrative-units-public-preview"></a>Yönetim birimleri genel önizleme

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Azure AD rolleri

**Ürün yeteneği:** Access Control

Yönetim birimleri bir departman, bölge veya tanımladığınız kuruluşunuzun diğer kesimiyle kısıtlanan yönetici izinleri vermenizi sağlar. Bölgesel yöneticilerle izinleri devretmek veya ayrıntılı bir düzeyde ilke ayarlamak için yönetim birimlerini kullanabilirsiniz. Örneğin, bir kullanıcı hesabı Yöneticisi profil bilgilerini güncelleştirebilir, parolaları sıfırlayabilir ve yalnızca kendi yönetim biriminde kullanıcılar için lisans atayabilir.

Yönetim birimlerini kullanarak bir merkezi yönetici şunları sağlayabilir:

- Kaynakların merkezileşmemiş yönetimi için yönetim birimi oluşturma
- Yönetici izinlerine sahip bir rolü yalnızca Azure AD kullanıcıları üzerinde bir yönetim biriminde atama
- Yönetim birimlerini Kullanıcı ve gruplarla gerektiği şekilde doldurma

Daha fazla bilgi için bkz. [Azure Active Directory (Önizleme) Içinde yönetim birimleri yönetimi](../roles/administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Yazıcı Yöneticisi ve yazıcı teknisyeni yerleşik rolleri

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Azure AD rolleri

**Ürün yeteneği:** Access Control

**Yazıcı Yöneticisi**: Bu role sahip kullanıcılar, Evrensel Yazdırma Bağlayıcısı ayarları dahil olmak üzere Microsoft Evrensel Yazdırma çözümünde tüm yazıcı yapılandırmalarının yazıcılarını kaydedebilir ve tüm yönlerini yönetebilir. Tüm temsilci atanmış yazdırma izni isteklerini kabul edebilirler. Yazıcı yöneticilerinin Ayrıca raporları yazdırma erişimi de vardır. 

**Yazıcı teknisyeni**: Bu role sahip kullanıcılar Microsoft Evrensel Yazdırma çözümünde yazıcı kaydedebilir ve yazıcı durumunu yönetebilir. Ayrıca, tüm bağlayıcı bilgilerini okuyabilir. Ana görevler bir yazıcı teknisyeni, yazıcılar ve paylaşım yazıcılarında Kullanıcı izinlerini ayarlar. [Daha fazla bilgi edinin.](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Karma kimlik yöneticisi yerleşik rolü

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Azure AD rolleri

**Ürün yeteneği:** Access Control

Bu roldeki kullanıcılar, Azure AD 'de karma kimliği etkinleştirme ile ilgili hizmetleri ve ayarları etkinleştirebilir, yapılandırabilir ve yönetebilir. Bu rol, Azure AD 'yi desteklenen üç kimlik doğrulama yönteminden birine&#8212;, Parola karması eşitlemesi (PHS), geçişli kimlik doğrulaması (PTA) veya Federasyon (AD FS ya da 3. taraf Federasyon sağlayıcısı) &#8212;ve bunları etkinleştirmek üzere ilgili şirket içi altyapıyı dağıtmak için yapılandırma olanağı verir. Şirket içi altyapı, sağlama ve PTA aracılarını içerir. Bu rol, Windows 10 olmayan cihazlarda veya Windows Server 2016 olmayan bilgisayarlarda sorunsuz kimlik doğrulamayı etkinleştirmek için sorunsuz tek Sign-On (S-SSO) etkinleştirebilme olanağı verir. Ayrıca, bu rol, oturum açma günlüklerini görüntüleme ve izleme ve sorun giderme amacıyla sistem durumuna ve analizine erişme olanağı verir. [Daha fazla bilgi edinin.](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ağ Yöneticisi yerleşik rolü

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Azure AD rolleri

**Ürün yeteneği:** Access Control

Bu role sahip olan kullanıcılar, Microsoft 'tan gelen ve Kullanıcı konumlarından ağ telemetrisini temel alan ağ çevre mimarisi önerilerini gözden geçirebilir. Microsoft 365 ağ performansı, genellikle kullanıcı konumlarına özgü olan dikkatli kurumsal müşteri ağ çevre mimarisine dayanır. Bu rol, geliştirilmiş telemetri ölçümlerini ve tasarım önerilerini kolaylaştırmak amacıyla, bulunan Kullanıcı konumlarının düzenlenmesine ve bu konumların ağ parametrelerinin yapılandırılmasını sağlar. [Daha fazla bilgi edinin.](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD Yönetici portalı deneyiminde toplu etkinlik ve İndirmeler

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Kullanıcı Yönetimi

**Ürün yeteneği:** Dizinden

Artık Azure AD Yönetim Portalı deneyimine bir CSV dosyası yükleyerek Azure AD 'de kullanıcılar ve gruplar üzerinde toplu etkinlikler gerçekleştirebilirsiniz. Kullanıcılar oluşturabilir, kullanıcıları silebilir ve konuk kullanıcıları davet edebilirsiniz. Ve bir gruba üye ekleyebilir ve kaldırabilirsiniz.

Azure AD kaynakları listesini Azure AD Yönetici portalı deneyiminden de indirebilirsiniz. Dizindeki Kullanıcı listesini, dizindeki grupların listesini ve belirli bir grubun üyelerini indirebilirsiniz.

Daha fazla bilgi için aşağıdakileri gözden geçirin:

- [Kullanıcı oluşturma](../enterprise-users/users-bulk-add.md) veya [Konuk kullanıcıları davet etme](../external-identities/tutorial-bulk-invite.md)
- [Kullanıcıları silme](../enterprise-users/users-bulk-delete.md) veya [silinen kullanıcıları geri yükleme](../enterprise-users/users-bulk-restore.md)
- [Kullanıcı listesini indir](../enterprise-users/users-bulk-download.md) veya [Grup listesini indir](../enterprise-users/groups-bulk-download.md)
- Üye [ekleme (içeri aktarma)](../enterprise-users/groups-bulk-import-members.md) veya üye [kaldırma](../enterprise-users/groups-bulk-remove-members.md) ya da bir grup için [üye listesini indirme](../enterprise-users/groups-bulk-download-members.md)

---

### <a name="my-staff-delegated-user-management"></a>Personelin Temsilcili Kullanıcı Yönetimi

**Şunu yazın:** Yeni Özellik

**Hizmet kategorisi:** Kullanıcı Yönetimi

**Ürün yeteneği:**

Personeliniz, personel üyelerinin Azure AD hesaplarına erişebildiğinden emin olmak için mağaza yöneticisi gibi Firstline yöneticileri sağlar. Kuruluşlar, merkezi bir yardım masasına güvenmek yerine, parolaları sıfırlama veya telefon numaralarını değiştirme gibi genel görevleri bir Firstline Manager 'a devredebilir. Personeli sayesinde, hesaplarına erişemesiz bir Kullanıcı, yardım masası veya BT personeli gerekmeden yalnızca birkaç tıklamayla erişim elde edebilir. Daha fazla bilgi için bkz. [kullanıcılarımızla kullanıcılarınızı yönetme (Önizleme)](../roles/my-staff-configure.md) ve [Kullanıcılarım Ile Kullanıcı yönetimi temsilcisi seçme (Önizleme)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Erişim incelemeleriyle yükseltilen Son Kullanıcı deneyimi

**Şunu yazın:** Değiştirilen özellik

**Hizmet kategorisi:** Erişim Incelemeleri

**Ürün yeteneği:** Kimlik yönetimi

My Apps portalındaki Azure AD erişim gözden geçirmeleriyle ilgili gözden geçiren deneyimini güncelleştirdik. Nisan 'un sonunda, Azure AD erişim gözden geçirmeleri gözden geçirmesinde oturum açmış olan gözden geçirenler, erişimimde güncelleştirilmiş deneyimi denemeye imkan tanıyan bir başlık görülecektir. Lütfen güncelleştirilmiş erişim İncelemeleri deneyiminin geçerli deneyimle aynı işlevleri sunduğunu, ancak kullanıcılarınızın üretken olmalarını sağlamak için yeni yetenekler üzerinde geliştirilmiş bir kullanıcı arabirimi olduğunu unutmayın. [Burada güncelleştirilmiş deneyim hakkında daha fazla bilgi](../governance/perform-access-review.md)edinebilirsiniz. Bu genel önizleme, 2020 Temmuz 'un sonuna kadar son olacak. Temmuz 'un sonunda, önizleme deneyimine karar verilmeyen gözden geçirenler, erişim gözden geçirmeleri gerçekleştirmek için erişimime otomatik olarak yönlendirilir. Gözden geçirenlerden şimdi erişimimde önizleme deneyimine geçiş yapmasını istiyorsanız, [lütfen buradan bir istek yapın](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>İşgünü gelen Kullanıcı hazırlama ve geri yazma uygulamaları artık Workday Web Hizmetleri API 'sinin en son sürümlerini desteklemektedir

**Şunu yazın:** Değiştirilen özellik

**Hizmet kategorisi:** Uygulama sağlama

**Ürün yeteneği:** 

Müşteri geri bildirimlerine bağlı olarak, Workday Web Hizmetleri (WWS) API 'sinin en son sürümlerini desteklemek üzere kurumsal uygulama galerisinde iş günü gelen Kullanıcı hazırlama ve geri yazma uygulamalarını güncelleştirdik. Bu değişiklik ile müşteriler, bağlantı dizesinde kullanmak istedikleri WWS API sürümünü belirtebilir. Bu, müşterilere Workday 'in sürümlerindeki daha fazla ık özniteliği alma olanağı sağlar. Workday geri yazma uygulaması artık Maintain_Contact_Info sınırlamalarını aşmak için önerilen Change_Work_Contact_Info Workday Web hizmetini kullanmaktadır.

Bağlantı dizesinde sürüm belirtilmemişse, varsayılan olarak, Workday gelen sağlama uygulamaları, gelen Kullanıcı sağlaması için en son Workday API 'Lerine geçiş yapmak üzere WWS v 21.1 kullanmaya devam eder, müşterilerin bağlantı dizesini [öğreticide](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) belgelendiği şekilde güncelleştirmesi ve ayrıca Workday [özniteliği başvuru kılavuzu](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)'Nda belgelendiği gibi Workday öznitelikleri için kullanılan XPaths 'ı güncelleştirmesi gerekir. 

Yeni API 'yi geri yazma için kullanmak üzere, Workday geri yazma sağlama uygulamasında hiçbir değişiklik yapılması gerekmez. Workday tarafında, Workday Integration System User (ıSU) hesabının öğretici bölümünde belgelendiği gibi Change_Work_Contact iş sürecini çağırma izinlerine sahip olduğundan emin olun, [iş süreci güvenlik ilkesi Izinlerini yapılandırın](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

[Öğretici kılavuzumuzu](../saas-apps/workday-inbound-tutorial.md) yeni API sürümü desteğini yansıtacak şekilde güncelleştirdik.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Varsayılan erişim rolüne sahip kullanıcılar artık sağlama için kapsamdadır

**Şunu yazın:** Değiştirilen özellik

**Hizmet kategorisi:** Uygulama sağlama

**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Geçmişte, varsayılan erişim rolüne sahip olan kullanıcılar sağlama için kapsam dışındaydı. Müşterilerin bu rolü olan kullanıcıların sağlama kapsamında olmasını istediğini belirten geri bildirimde bulunduk. 16 Nisan 2020 itibariyle, tüm yeni sağlama yapılandırmalarının varsayılan erişim rolüne sahip kullanıcıların sağlanması sağlanır. Kademeli olarak, mevcut sağlama yapılandırmalarının davranışını bu rolle kullanıcıları sağlamayı destekleyecek şekilde değiştirecek. [Daha fazla bilgi edinin.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Hazırlama Kullanıcı arabirimi güncelleştirildi

**Şunu yazın:** Değiştirilen özellik

**Hizmet kategorisi:** Uygulama sağlama

**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Daha odaklanmış bir yönetim görünümü oluşturmak için sağlama deneyimimizi yeniledik. Zaten yapılandırılmış bir kurumsal uygulama için sağlama dikey penceresine gittiğinizde, sağlama, durdurma ve yeniden başlatma gibi eylemleri sağlama ve yönetme sürecini kolayca izleyebilirsiniz. [Daha fazla bilgi edinin.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Dinamik grup kuralı doğrulaması artık genel önizleme için kullanılabilir

**Şunu yazın:** Değiştirilen özellik

**Hizmet kategorisi:** Grup Yönetimi

**Ürün yeteneği:** İş

Azure Active Directory (Azure AD), artık dinamik grup kurallarını doğrulama araçlarını sağlıyor. Kuralların beklenen şekilde çalıştığını onaylamak için **kuralları doğrula** sekmesinde, dinamik kuralınızı örnek grup üyelerine doğrulayabilirsiniz. Yöneticiler, dinamik grup kuralları oluştururken veya güncelleştirirken, bir kullanıcının veya cihazın grubun üyesi olup olmayacağını bilmesini ister. Bu, bir kullanıcının veya cihazın, üyelik beklenmediği zaman sorun gidermede kural ölçütlerini ve yardımları karşılayıp karşılamadığını değerlendirmenize yardımcı olur.

Daha fazla bilgi için bkz. [dinamik grup üyeliği kuralını doğrulama (Önizleme)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Kimlik güvenli puanı-güvenlik Varsayılanları ve MFA geliştirme eylem güncelleştirmeleri

**Şunu yazın:** Değiştirilen özellik

**Hizmet kategorisi:** yok

**Ürün yeteneği:** Kimlik güvenliği & koruması

**Azure AD geliştirme eylemleri için güvenlik varsayılanlarını destekleme:** Microsoft güvenli puanı, [Azure AD 'deki güvenlik varsayılanlarını](./concept-fundamentals-security-defaults.md)desteklemek için geliştirme eylemlerini güncelleyecektir. Bu, kuruluşunuzun yaygın saldırılar için önceden yapılandırılmış güvenlik ayarlarıyla korunmasını kolaylaştırır. Bu, aşağıdaki geliştirme eylemlerini etkiler:

- Tüm kullanıcıların güvenli erişim için Multi-Factor Authentication 'ı tamamlayabilmesi için
- Yönetim rolleri için MFA gerektir
- Eski kimlik doğrulamasını engellemek için ilkeyi etkinleştir
 
**MFA geliştirme eylemi güncelleştirmeleri:** İşletmelerin, işletmelerle çalışan ilkeleri uygularken en çok güvenliği güvence altına aldığından emin olmak için, Microsoft güvenli puanı, Multi-Factor Authentication ' ın çevresinde ortalanmış üç geliştirme eylemini kaldırmış ve iki eklenmiştir.

Geliştirme eylemleri kaldırıldı:

- Multi-Factor Authentication için tüm kullanıcıları kaydet
- Tüm kullanıcılar için MFA gerektirme
- Azure AD ayrıcalıklı rolleri için MFA gerektir

Geliştirme eylemleri eklendi:

- Tüm kullanıcıların güvenli erişim için Multi-Factor Authentication 'ı tamamlayabilmesi için
- Yönetim rolleri için MFA gerektir

Bu yeni geliştirme eylemleri, kullanıcılarınızı veya yöneticilerinizi, dizininiz genelinde Multi-Factor Authentication (MFA) için kaydetmeyi ve kuruluşunuzun gereksinimlerine uygun olan doğru ilke kümesini oluşturmayı gerektirir. Ana amaç, tüm kullanıcılarınızın ve yöneticilerinin birden çok faktörle veya risk tabanlı kimlik doğrulama istemlerinde kimlik doğrulaması yapabilmesini sağlarken esneklik sağlamaktır. Bu, kapsamlı kararlar uygulayan veya güvenlik varsayılanlarını (16 Mart itibariyle) ayarlayarak, Microsoft 'un MFA için kullanıcılara ne zaman zorluk cağına karar vermesine olanak tanıyan birden çok ilke olmasını sağlayabilir. [Microsoft güvenli puandaki yenilikler hakkında daha fazla bilgi edinin](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Mart 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>2021 Mart için B2B güncelleştirmesinde yönetilmeyen Azure Active Directory hesapları

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Microsoft, **31 mart 2021**' den ıtıbaren, B2B işbirliği senaryolarına yönelik yönetilmeyen Azure Active Directory (Azure AD) hesapları ve kiracılar oluşturarak davetlerin kullanımını desteklememektedir. Buna hazırlık olarak, [bir kerelik geçiş kodu kimlik doğrulamasını e-posta](../external-identities/one-time-passcode.md)ile yapmayı tercih etmeniz önerilir.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Varsayılan erişim rolüne sahip kullanıcılar sağlama kapsamında olacaktır

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Geçmişte, varsayılan erişim rolüne sahip olan kullanıcılar sağlama için kapsam dışındaydı. Müşterilerin bu rolü olan kullanıcıların sağlama kapsamında olmasını istediğini belirten geri bildirimde bulunduk. Tüm yeni sağlama yapılandırmalarının, varsayılan erişim rolüne sahip kullanıcıların sağlanmasına olanak tanımak için bir değişikliği dağıtmaya çalışıyoruz. Kademeli olarak, mevcut sağlama yapılandırmalarının davranışını bu rolle kullanıcıları sağlamayı destekleyecek şekilde değiştireceksiniz. Hiçbir müşteri eylemi gerekli değildir. Bu değişiklik gerçekleştikten sonra [belgelerimize](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) bir güncelleştirme göndereceğiz.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B işbirliği, 21Vianet tarafından çalıştırılan Microsoft Azure (Azure Çin 21Vianet) kiracılarında kullanıma sunulacaktır

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Azure AD B2B işbirliği özellikleri, 21Vianet tarafından çalıştırılan Microsoft Azure (Azure Çin 21Vianet) kiracılarında kullanıma sunulacaktır ve bir Azure Çin 21Vianet kiracısındaki kullanıcıların diğer Azure Çin 21Vianet kiracılarındaki kullanıcılarla sorunsuz bir şekilde işbirliği yapmasına olanak tanır. [Azure AD B2B işbirliği hakkında daha fazla bilgi edinin](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B Işbirliği daveti e-postası tasarımı

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Kullanıcıları dizine davet etmek için Azure AD B2B işbirliği daveti hizmeti tarafından gönderilen [e-postalar](../external-identities/invitation-email-elements.md) , davet bilgilerini ve kullanıcının sonraki adımları daha net hale getirmek için yeniden tasarlanmıştır.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealmDiscovery ilkesi değişiklikleri denetim günlüklerinde görüntülenir

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Denetlenmesini  
**Ürün yeteneği:** & raporlamayı izleme
 
[Homerealmdiscovery ilkesinde](../manage-apps/configure-authentication-for-federated-users-portal.md) yapılan değişikliklerin denetim günlüklerine dahil edilmediğinden oluşan bir hata düzeltildi. Artık, ilkenin ne zaman ve nasıl değiştiğini ve kim tarafından nasıl değiştirildiğini görebileceksiniz. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları mevcuttur-Mart 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
2020 Mart 'da, uygulama galerisine federasyon desteğiyle bu 51 yeni uygulamalar ekledik: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One Çin](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML App](../saas-apps/profitco-saml-app-tutorial.md), [IPoint Service Provider](../saas-apps/ipoint-service-provider-tutorial.md), [Contexxt.ai Sphere](https://contexxt-sphere.com/login), [ınkıs tarafından Wıkıx](../saas-apps/wisdom-by-invictus-tutorial.md), [Mercek Parlaması, açılan dijital imza](https://spark-dev.pixelnebula.com/login), [LOGZ.IO-Cloud Observability for mühendisler](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [SpectrumU](../saas-apps/spectrumu-tutorial.md), [Bizzcontact](https://www.bizzcontact.app/), [elqano SSO](../saas-apps/elqano-sso-tutorial.md), [Pazar Signshare](http://www.signshare.com/), [crossknowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [NetVision Compas](../saas-apps/netvision-compas-tutorial.md), [fcm hub](../saas-apps/fcm-hub-tutorial.md), [Rib A/S byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [golmürekkepler](../saas-apps/golinks-tutorial.md), [Dataköpek](../saas-apps/datadog-tutorial.md), [Zscaler B2B Kullanıcı Portalı](../saas-apps/zscaler-b2b-user-portal-tutorial.md), [yükseltme,](../saas-apps/lift-tutorial.md) [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [yetenekler iş akışı](../saas-apps/skills-workflow-tutorial.md), [düğüm öngörüleri](https://admin.nodeinsight.com/AADLogin.aspx) [IP platformu](../saas-apps/ip-platform-tutorial.md), [Invision](../saas-apps/invision-tutorial.md), [Pipedrive](../saas-apps/pipedrive-tutorial.md), gösterim [Atölyesi](https://app.showcaseworkshop.com/), [melight Integration platformu](../saas-apps/greenlight-integration-platform-tutorial.md), [doğa Light uyumlu erişim yönetimi](../saas-apps/greenlight-compliant-access-management-tutorial.md), [grok Learning](../saas-apps/grok-learning-tutorial.md), [Mıradore çevrimiçi](https://login.online.miradore.com/), [Khoros Hizmetleri](../saas-apps/khoros-care-tutorial.md), [askyourteam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [BizAgi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [yalıtılmış Tex](https://www.insuite.jp/), [Sybo](https://www.systexsoftware.com.tw/), [britive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-Days](../saas-apps/e-days-tutorial.md), [kollesel Sdn](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [playtı](https://my.playvox.com/login), [Korn Fraz 360](../saas-apps/korn-ferry-360-tutorial.md), [kampüs Café](../saas-apps/campus-cafe-tutorial.md), [catch noktası](../saas-apps/catchpoint-tutorial.md), [Code42](../saas-apps/code42-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B Işbirliği Azure Kamu kiracılarında kullanılabilir

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** B2B/B2C
 
Azure AD B2B işbirliği özellikleri artık bazı Azure Kamu kiracılar arasında kullanılabilir.  Kiracınızın bu özellikleri kullanıp kullanabileceklerini öğrenmek için, [B2B işbirliğinin Azure ABD kamu kiracısında kullanılabilir olup olmadığını nasıl anlayabilirim?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)konusundaki yönergeleri izleyin.

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure günlükleri için Azure Izleyici tümleştirmesi artık Azure Kamu 'da kullanıma sunuldu

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 
Azure AD günlükleriyle Azure Izleyici tümleştirmesi artık Azure Kamu 'da kullanıma sunulmuştur. Azure AD günlüklerini (denetim ve oturum açma günlükleri) bir depolama hesabına, Olay Hub 'ına ve Log Analytics yönlendirebilirsiniz. Lütfen [ayrıntılı belgelere](../reports-monitoring/concept-activity-logs-azure-monitor.md) ve Azure AD senaryolarına yönelik [Raporlama ve izleme için dağıtım planlarına](../reports-monitoring/plan-monitoring-and-reporting.md) göz atın.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Kamu 'da kimlik koruması yenileme

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması

Artık [](../identity-protection/overview-identity-protection.md)   , [Microsoft Azure Kamu portalında](https://portal.azure.us/)yenilenen Azure AD kimlik koruması deneyimini kullanıma sunduğumuz için paylaştık. Daha fazla bilgi için bkz. [duyuru blog gönderimiz](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Olağanüstü durum kurtarma: sağlama yapılandırmanızı Indirin ve depolayın

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi
 
Azure AD sağlama hizmeti, zengin bir yapılandırma özellikleri kümesi sağlar. Müşterilerin, daha sonra başvurabilmeleri veya bilinen iyi bir sürüme geri dönebilmeleri için yapılandırmalarını kaydedebilmeleri gerekir. Sağlama yapılandırmanızı bir JSON dosyası olarak indirme ve ihtiyacınız olduğunda karşıya yükleme özelliği ekledik. [Daha fazla bilgi edinin](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (self servis parola sıfırlama) artık Microsoft Azure 21Vianet tarafından çalıştırılan Yöneticiler için iki ağ geçidi gerektirir (Azure Çin 21Vianet) 

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Self-Service parola sıfırlama  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Daha önce 21Vianet tarafından çalıştırılan Microsoft Azure (Azure Çin 21Vianet), kendi parolalarını sıfırlamak için self servis parola sıfırlama (SSPR) kullanan yöneticiler, kimliklerini kanıtlamak için yalnızca bir "Gate" (zorluk) gerekir. Genel ve diğer ulusal bulutlarda Yöneticiler, SSPR kullanırken kimliklerini kanıtlamak için genellikle iki kapı kullanmalıdır. Ancak, Azure Çin 21Vianet 'de SMS veya telefon çağrılarını desteklemediğimiz için, yöneticiler tarafından tek bir geçit parola sıfırlamasına izin verdik.

Azure Çin 21Vianet ve genel bulut arasında SSPR özelliği eşliği oluşturacağız. Bundan sonra, Yöneticiler SSPR kullanırken iki ağ geçidi kullanmalıdır. SMS, telefon aramaları ve Authenticator uygulama bildirimleri ve kodları desteklenecektir. [Daha fazla bilgi edinin](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Parola uzunluğu 256 karakterle sınırlıdır

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Azure AD hizmeti 'nin güvenilirliğini güvence altına almak için kullanıcı parolalarının uzunluğu 256 karakterle sınırlıdır. Bundan daha uzun parolalara sahip olan kullanıcılar, yöneticilerle iletişim kurarak veya self servis parola sıfırlama özelliğini kullanarak, sonraki oturum açmada parolasını değiştirmeyecektir.

Bu değişiklik, 13 Mart, 2020, saat 10:00:00 PST (18:00 UTC) tarihinde etkinleştirildi ve hata AADSTS 50052, ınvalidpasswordexceedsmaxlength. Daha fazla ayrıntı için bkz. [değişiklik bildirimi](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD oturum açma günlükleri artık Azure portal aracılığıyla tüm ücretsiz kiracılar için kullanılabilir

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Rapor  
**Ürün yeteneği:** & raporlamayı izleme
 
Şimdi başlayarak, ücretsiz kiracılar olan müşteriler [Azure Portal Azure AD oturum açma günlüklerine](../reports-monitoring/concept-sign-ins.md) en fazla 7 güne kadar erişebilir. Daha önce, oturum açma günlükleri yalnızca Azure Active Directory Premium lisansları olan müşteriler için kullanılabilir. Bu değişiklik ile tüm kiracılar bu günlüklere Portal üzerinden erişebilir.

> [!NOTE]
> Müşterilerin, Microsoft Graph API ve Azure Izleyici aracılığıyla oturum açma günlüklerine erişmesi için Premium lisansına (Azure Active Directory Premium P1 veya P2) ihtiyacı vardır.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Grupların genel ayarlarından Dizin genelinde gruplar seçeneğinin kullanımdan kaldırılması Azure portal

**Şunu yazın:** Kullanım dışı  
**Hizmet kategorisi:** Grup Yönetimi  
**Ürün yeteneği:** İş

Müşterilerin ihtiyaçlarını en iyi şekilde karşılayan Dizin genelinde gruplar oluşturması için daha esnek bir yol sağlamak üzere, **Dizin genelinde** gruplar seçeneğini, Azure Portal **gruplar**  >  **genel** ayarlarından [dinamik grup belgelerinin](../enterprise-users/groups-dynamic-membership.md)bir bağlantısıyla değiştirdik. Yöneticiler, Konuk kullanıcıları dahil eden veya hariç tutmaları için daha fazla yönerge eklemek üzere belgelerimizi geliştirdik.

---

## <a name="february-2020"></a>Şubat 2020

### <a name="upcoming-changes-to-custom-controls"></a>Özel denetimlerde yaklaşan değişiklikler

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
İş ortağı tarafından sunulan kimlik doğrulama yeteneklerinin Azure Active Directory yönetici ve son kullanıcı deneyimleri ile sorunsuz bir şekilde çalışmasını sağlayan bir yaklaşım ile geçerli özel denetim önizlemesini değiştirmeyi planlıyoruz. Günümüzde, iş ortağı MFA çözümleri şu sınırlamalara sahiptir: yalnızca bir parola girildikten sonra çalışır; diğer anahtar senaryolarında adım adım kimlik doğrulaması için MFA olarak işlev görürler; Ayrıca, son kullanıcı veya yönetim kimlik bilgileri yönetim işlevleriyle tümleştirilemiyor. Yeni uygulama, iş ortağı tarafından belirtilen kimlik doğrulama faktörlerinin, kayıt, kullanım, MFA talepleri, adım kimlik doğrulama, raporlama ve günlüğe kaydetme gibi önemli senaryolar için yerleşik faktörlerle birlikte çalışmasına izin verir. 

Özel denetimler, genel kullanıma ulaşıncaya kadar yeni tasarımın yanı sıra önizlemede desteklenmeye devam edecektir. Bu noktada, müşterilere yeni tasarıma geçiş süresi vereceğiz. Geçerli yaklaşımın sınırlamaları nedeniyle, yeni tasarım kullanılabilir olana kadar yeni sağlayıcılar sunmayacağız. Müşteriler ve sağlayıcılar ile yakından çalışıyoruz ve zaman çizelgesini daha yakından ilettiğimiz için iletişim kuracak. [Daha fazla bilgi edinin](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Kimlik güvenli puanı-MFA geliştirme eylem güncelleştirmeleri

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** MFA  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
İşletmelerin, işletmelerle çalışan ilkeleri uygularken en fazla güvenliği güvence altına aldığından emin olmak için, çok faktörlü kimlik doğrulaması (MFA) etrafında ortalanmış üç geliştirme eylemini kaldırır ve iki ekleme işlemi yapılır.

Aşağıdaki geliştirme eylemleri kaldırılacak:

- MFA için tüm kullanıcıları kaydet
- Tüm kullanıcılar için MFA gerektirme
- Azure AD ayrıcalıklı rolleri için MFA gerektir

Aşağıdaki geliştirme eylemleri eklenecek:

- Tüm kullanıcıların, güvenli erişim için MFA 'yı tamambağlanabildiğinden emin olun
- Yönetim rolleri için MFA gerektir

Bu yeni geliştirme eylemleri, kullanıcılarınızın veya yöneticilerinin dizininiz genelinde MFA için kaydedilmesini ve kuruluşunuzun gereksinimlerine uygun olan doğru ilke kümesini oluşturmayı gerektirir. Ana amaç, tüm kullanıcılarınızın ve yöneticilerinin birden çok faktörle veya risk tabanlı kimlik doğrulama istemlerinde kimlik doğrulaması yapabilmesini sağlarken esneklik sağlamaktır. Bu, Microsoft 'un MFA için kullanıcıları ne zaman sınayacağına karar vermesine veya kapsamlı kararlar uygulayan birden çok ilkeye sahip olmasına izin veren güvenlik varsayılanlarını ayarlama biçimini alabilir. Bu geliştirme eylemi güncelleştirmelerinin bir parçası olarak, temel koruma ilkeleri artık Puanlama hesaplamalarına dahil olmayacaktır. [Microsoft güvenli puanına nelerin geldiği hakkında daha fazla bilgi edinin](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU seçimi

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Azure AD Domain Services  
**Ürün yeteneği:** Azure AD Domain Services
 
Azure AD Domain Services müşterilerin, örnekleri için performans düzeylerini seçerken daha fazla esneklik istediğini belirten geri bildirimde bulunduk. 1 Şubat 2020 ' den itibaren, dinamik bir modelden (Azure AD, nesne sayısına göre performans ve fiyatlandırma katmanını belirler) bir kendi kendine seçim modeline geçtik. Artık müşteriler, ortamıyla eşleşen bir performans katmanını seçebilirler. Bu değişiklik Ayrıca, kaynak ormanları gibi yeni senaryoları ve günlük yedeklemeler gibi Premium özellikleri etkinleştirebileceğimizi de sağlar. Nesne sayısı tüm SKU 'Lar için artık sınırsız, ancak her katman için nesne sayısı önerileri sunmaya devam edeceğiz.

**Acil bir müşteri eylemi gerekli değildir.** Mevcut müşteriler için 1 Şubat 2020 ' de kullanılan dinamik katman, yeni varsayılan katmanı belirler. Bu değişikliğin sonucu olarak bir fiyatlandırma veya performans etkisi yoktur. Azure AD DS müşterilerinin, dizin boyutu ve iş yükü özellikleri değiştikçe performans gereksinimlerini değerlendirmesi gerekecektir. Hizmet katmanları arasında geçiş kesinti olmaması durumunda olmaya devam eder, ancak artık kendi dizin artışına göre müşterileri otomatik olarak yeni katmanlara taşıyamayacaktır. Ayrıca, hiçbir ücret artmayacak ve yeni fiyatlandırma geçerli faturalandırma modelimize göre hizalanacaktır. Daha fazla bilgi için bkz. [Azure AD DS SKU belgeleri](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) ve [Azure AD Domain Services fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Şubat 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Şubat 2020 ' de, uygulama galerisine federasyon desteğiyle bu 31 yeni uygulamayı ekledik: 

[Iamıp patent platformu](../saas-apps/iamip-patent-platform-tutorial.md), [deneyim bulutu](../saas-apps/experience-cloud-tutorial.md), [Azure için ns1 SSO](../saas-apps/ns1-sso-azure-tutorial.md), [Barkcuda e-posta güvenlik hizmeti](https://ess.barracudanetworks.com/sso/azure), [aba raporlaması](https://myaba.co.uk/client-access/signin/auth/msad), [Çapraz çevrimiçi portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), [Bic bulut tasarımı](../saas-apps/bic-cloud-design-tutorial.md), [](../saas-apps/splashtop-tutorial.md) [Azure ad veri Bağlayıcısı için beekeeto](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md) [](../saas-apps/verkada-command-tutorial.md) [Yeni relik (sınırlı sürüm)](../saas-apps/new-relic-limited-release-tutorial.md), [thulium](https://admin.thulium.com/login/instance), [bilet Yöneticisi](../saas-apps/ticketmanager-tutorial.md), [](../saas-apps/syxsense-tutorial.md) [takımlar için şablon Seçicisi](https://links.officeatwork.com/templatechooser-download-teams), [beesy](https://www.beesy.me/index.php/site/login), [sağlık desteği sistemi](../saas-apps/health-support-system-tutorial.md), [mural](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [firmex VDR](../saas-apps/firmex-vdr-tutorial.md), [öğretmenler ve okullar için ThingLink](https://www.thinglink.com/), [Coda](../saas-apps/coda-tutorial.md), beklenen [podapp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [Wedo](../saas-apps/wedo-tutorial.md), [](https://www.kornferry.com/solutions/kf-digital/kf-assess) [](../saas-apps/eab-navigate-tutorial.md) [davetli](https://invitepeople.com/login) [,](../saas-apps/teamviewer-tutorial.md) [](../saas-apps/reprints-desk-article-galaxy-tutorial.md)

 
Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Şubat 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Karma ortamlarda FIDO2 güvenlik anahtarları için Azure AD desteği

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
Karma ortamlarda FIDO2 güvenlik anahtarlarına yönelik Azure AD desteğinin genel önizlemesini duyuruyoruz. Kullanıcılar, karma Azure AD 'ye katılmış Windows 10 cihazlarında oturum açmak ve şirket içi ve bulut kaynaklarında sorunsuz oturum açmak için FIDO2 güvenlik anahtarlarını kullanabilir. Azure AD 'ye katılmış cihazlarda FIDO2 desteği için genel önizlemeyi başlatdığımız için, karma ortamlar için destek, parolalarımızın en çok istenen özelliğidir. Biyometri ve genel/özel anahtar şifrelemesi gibi gelişmiş teknolojiler kullanılarak passwordless kimlik doğrulaması, güvenli hale getirmeye yönelik kolaylık ve kullanım kolaylığını sağlar. Bu genel önizleme ile, artık geleneksel Active Directory kaynaklarına erişmek için FIDO2 güvenlik anahtarları gibi modern kimlik doğrulamasını kullanabilirsiniz. Daha fazla bilgi için bkz. Şirket [içi kaynaklara SSO](../authentication/howto-authentication-passwordless-security-key-on-premises.md)'ya gidin. 

Başlamak için, adım adım yönergeler için [kiracınız IÇIN FIDO2 güvenlik anahtarlarını etkinleştir](../authentication/howto-authentication-passwordless-security-key.md) ' i ziyaret edin. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Yeni Hesabım deneyimi artık genel kullanıma sunuldu

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Profilim/Hesabım  
**Ürün yeteneği:** Son Kullanıcı deneyimleri
 
Tüm Son Kullanıcı hesabı yönetimi ihtiyaçları için tek bir durdurma için Hesabım, artık genel kullanıma sunuldu! Son kullanıcılar bu yeni siteye URL aracılığıyla veya yeni uygulamalarım deneyiminin üst bilgisinde erişebilir. Tüm self servis özellikleri hakkında daha fazla bilgi edinmek için yeni deneyim [Hesap portalından genel bakış ' a](../user-help/my-account-portal-overview.md)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Hesabım sitesi URL 'SI myaccount.microsoft.com olarak güncelleştiriliyor

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Profilim/Hesabım  
**Ürün yeteneği:** Son Kullanıcı deneyimleri
 
Yeni Hesabım Son Kullanıcı deneyimi, URL 'sini bir sonraki ay içinde olacak şekilde güncelleştirilecektir `https://myaccount.microsoft.com` . [Hesabım portalı yardımım](../user-help/my-account-portal-overview.md)'de son kullanıcılara sunduğu deneyim ve tüm hesap self servis özellikleri hakkında daha fazla bilgi edinin.

---

## <a name="january-2020"></a>Ocak 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Yeni uygulamalarım portalı genel kullanıma sunuldu

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Uygulamalarım  
**Ürün yeteneği:** Son Kullanıcı deneyimleri
 
Kuruluşunuzu, genel kullanıma sunulan yeni uygulamalarım portalına yükseltin! Yeni Portal ve Koleksiyonlar hakkında daha fazla bilgi için [bkz. uygulamalarım portalındaki koleksiyonlar oluştur](../manage-apps/access-panel-collections.md).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD 'deki çalışma alanları koleksiyonlara yeniden adlandırıldı

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Uygulamalarım   
**Ürün yeteneği:** Son Kullanıcı deneyimleri
 
Yöneticiler kullanıcıların uygulamalarını düzenlemek üzere yapılandırabilen çalışma alanları, artık koleksiyonlar olarak anılacaktır. [Uygulamalarım portalındaki koleksiyonları oluşturma](../manage-apps/access-panel-collections.md)sırasında bunları yapılandırma hakkında daha fazla bilgi edinin.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefon kayıt ve özel ilke kullanarak oturum açma (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C
 
Telefon numarası kaydolma ve oturum açma ile, geliştiriciler ve kuruluşlar müşterilerinin SMS aracılığıyla kullanıcının telefon numarasına gönderilen tek seferlik bir parola kullanarak kaydolmasına ve oturum açmalarına izin verebilir. Bu özellik ayrıca müşterinin telefonlarına erişimi Kayıplarsa telefon numaralarını değiştirmesine de olanak tanır. Özel ilkelerin ve telefon kayıt ve oturum açma gücü sayesinde, geliştiricilerin ve kuruluşların markalarını sayfa özelleştirmesine iletişim kurmasına izin verir. [Azure AD B2C 'de özel ilkelerle telefon kaydı ve oturum açma ayarlamayı](../../active-directory-b2c/phone-authentication-user-flows.md)öğrenin.
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD uygulama galerisinde yeni sağlama bağlayıcıları-Ocak 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Ocak 2020

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi
 
2020 Ocak 'ta, uygulama galerisine federasyon desteğiyle bu 33 yeni uygulamalar ekledik: 

[Josa](../saas-apps/josa-tutorial.md), [fastly Edge bulutu](../saas-apps/fastly-edge-cloud-tutorial.md), [terraform Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [ufuk](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)anında, [](../saas-apps/upshotly-tutorial.md) [upvebot](https://appsource.microsoft.com/en-us/product/office/WA200001175), [Datacamp](../saas-apps/datacamp-tutorial.md), [tripactions](../saas-apps/tripactions-tutorial.md), [smartwork](https://www.intumit.com/teams-smartwork/), [Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md), [Ssogen-Azure AD SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft ve JDE](../saas-apps/ssogen-tutorial.md), [barındırılan Mycirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [yuhu özelliği YÖNETIM platformu](../saas-apps/yuhu-property-management-platform-tutorial.md), [Lumapps](https://sites.lumapps.com/login), [upwork Enterprise](../saas-apps/upwork-enterprise-tutorial.md), [talentsoft](../saas-apps/talentsoft-tutorial.md), [Smartdb for Microsoft ekipleri](http://teams.smartdb.jp/login/), [presspage](../saas-apps/presspage-tutorial.md), [contractsafe SAML2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [maxdentitydeploy Manager yazılımı](../saas-apps/maxient-conduct-manager-software-tutorial.md), [helpshıft](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/), [coreview](https://portal.coreview.com/), Squelch Cloud Office365 bağlayıcı, [pingflow kimlik doğrulaması](https://app-staging.pingview.io/), [printerlogic SaaS](../saas-apps/printerlogic-saas-tutorial.md), [taskize Connect](../saas-apps/taskize-connect-tutorial.md), [sandwai](https://app.sandwai.com/), [EZRentOut](../saas-apps/ezrentout-tutorial.md), [assetsonar](../saas-apps/assetsonar-tutorial.md), [akarı Sanal Yardımcısı](https://akari.io/akari-virtual-assistant/)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="two-new-identity-protection-detections"></a>İki yeni kimlik koruması algılamaları

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kimlik koruması  
**Ürün yeteneği:** Kimlik güvenliği & koruması
 
Kimlik korumasına iki yeni oturum açma bağlantılı algılama türü ekledik: şüpheli gelen kutusu düzenleme kuralları ve olanaksız seyahat. Bu çevrimdışı algılamalar Microsoft Cloud App Security (MCAS) tarafından keşfedilir ve kimlik koruması sırasında kullanıcıyı ve oturum açma riskini etkiler. Bu algılamalar hakkında daha fazla bilgi için bkz. [oturum açma risk türleri](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Son değişiklik: URI parçaları, oturum açma yeniden yönlendirme aracılığıyla taşınmaz

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması
 
8 Şubat 2020 ' den itibaren, bir kullanıcının oturum açması için login.microsoftonline.com 'e bir istek gönderildiğinde, hizmet isteğe boş bir parça ekler.  Bu, tarayıcının istekteki mevcut bir parçayı temizler olarak yeniden yönlendirme saldırılarına karşı bir sınıf önler. Hiçbir uygulamanın bu davranışa bağımlılığı olmamalıdır. Daha fazla bilgi için bkz. Microsoft Identity platform belgelerindeki [son değişiklikler](../develop/reference-breaking-changes.md#february-2020) .

---

## <a name="december-2019"></a>Aralık 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP başarılı faktörleri sağlamasını Azure AD 'ye ve şirket içi AD 'ye tümleştirin (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulama sağlama  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Artık SAP başarılı faktörleri, Azure AD 'de yetkili kimlik kaynağı olarak tümleştirebilirsiniz. Bu tümleştirme, Azure AD hesaplarının sağlanmasını denetlemek için yeni işe alımlar veya sonlandırmalar gibi İK tabanlı olayları kullanma dahil olmak üzere uçtan uca kimlik yaşam döngüsünü otomatikleştirmenize yardımcı olur.

SAP 'nin Azure AD 'ye gelen sağlama için başarılı bir şekilde nasıl ayarlanacağı hakkında daha fazla bilgi için bkz. [SAP başarılı etmenleri yapılandırma otomatik sağlama](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) öğreticisi.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C 'de özelleştirilmiş e-postalar için destek (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** B2C-tüketici kimlik yönetimi  
**Ürün yeteneği:** B2B/B2C

Artık kullanıcılarınız uygulamalarınızı kullanmak üzere kaydolduğunuzda özelleştirilmiş e-postalar oluşturmak için Azure AD B2C kullanabilirsiniz. DisplayControls (Şu anda önizleme aşamasında) ve bir üçüncü taraf e-posta sağlayıcısı (örneğin, [SendGrid](https://sendgrid.com/), [mini](https://sparkpost.com/)posta veya özel bir REST API) kullanarak kendi e-posta şablonunuzu **, adres ve** konu metinlerinizi kullanabilir ve yerelleştirme ve özel bir kerelik parola (OTP) ayarlarını destekleyebilirsiniz.

Daha fazla bilgi için bkz. [Azure Active Directory B2C özel e-posta doğrulaması](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Ana hat ilkelerinin güvenlik varsayılanlarını değiştirme

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Kimlik güvenliği ve koruması

Kimlik doğrulaması için güvenli bir varsayılan model kapsamında, tüm kiracılardan var olan temel koruma ilkelerini kaldırdık. Bu kaldırma işlemi Şubat 'ın sonunda tamamlanmasına yöneliktir. Bu temel koruma ilkelerinin yerini değiştirme, güvenlik varsayılanlardır. Temel koruma ilkeleri kullanıyorsanız, yeni güvenlik Varsayılanları ilkesine veya koşullu erişime taşımayı planlamanız gerekir. Bu ilkeleri kullanmadıysanız, yapmanız gereken bir işlem yoktur.

Yeni güvenlik Varsayılanları hakkında daha fazla bilgi için bkz. [güvenlik Varsayılanları nelerdir?](./concept-fundamentals-security-defaults.md) Koşullu erişim ilkeleri hakkında daha fazla bilgi için bkz. [Genel koşullu erişim ilkeleri](../conditional-access/concept-conditional-access-policy-common.md).

---

## <a name="november-2019"></a>Kasım 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>SameSite özniteliği ve Chrome 80 için destek

**Şunu yazın:** Değişiklik planı  
**Hizmet kategorisi:** Kimlik doğrulamaları (oturum açma)  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Tanımlama bilgileri için güvenli bir varsayılan model modelinin parçası olarak, Chrome 80 tarayıcısı özniteliği olmadan tanımlama bilgilerine nasıl davrandığını değiştiriyor `SameSite` . Özniteliği belirtmeyen tüm tanımlama bilgileri `SameSite` , olarak ayarlanmış gibi değerlendirilir, bu da `SameSite=Lax` uygulamanızın bağlı olabileceği bazı etki alanları arası tanımlama bilgisi paylaşma senaryolarını engeller. Daha eski Chrome davranışını sürdürmek için, `SameSite=None` özniteliğini kullanabilir ve ek bir öznitelik ekleyebilirsiniz. bu `Secure` nedenle, siteler arası tanımlama BILGILERINE yalnızca HTTPS bağlantıları üzerinden erişilebilir. Chrome, 4 Şubat 2020 ' de bu değişikliği tamamlamaya zamanlandı.

Tüm geliştiricilerimizin bu kılavuzu kullanarak uygulamalarını test etmenizi öneririz:

- **Güvenli tanımlama bilgisi kullan** ayarının varsayılan değerini **Evet** olarak ayarlayın.

- **SameSite** özniteliği için varsayılan değeri **none** olarak ayarlayın.

- Ek bir `SameSite` **güvenli** öznitelik ekleyin.

Daha fazla bilgi için bkz. [ASP.net ve ASP.NET Core ' de yakında bulunan SameSite tanımlama bilgisi değişiklikleri](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) ve [Chrome sürüm 79 ve sonraki sürümlerinde müşteri web siteleri ve Microsoft ürün ve hizmetlerine yönelik olası](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)kesinti.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager (MıM) 2016 Service Pack 2 (SP2) için yeni düzeltme

**Şunu yazın:** Düzenle  
**Hizmet kategorisi:** Microsoft Identity Manager  
**Ürün yeteneği:** Kimlik yaşam döngüsü yönetimi

Microsoft Identity Manager (MıM) 2016 Service Pack 2 (SP2) için bir düzeltme paketi paketi (Build 4.6.34.0) kullanılabilir. Bu toplu paket sorunları çözer ve "Bu güncelleştirmede eklenen sorunlar ve iyileştirmeler" bölümünde açıklanan iyileştirmeler ekler.

Daha fazla bilgi edinmek ve düzeltme paketini indirmek için, bkz. [Microsoft Identity Manager 2016 Service Pack 2 (derleme 4.6.34.0) güncelleştirme paketi kullanılabilir](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Azure AD 'ye uygulama geçirmeye yardımcı olmak için yeni AD FS App etkinlik raporu (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO

Uygulamalarınızın Azure AD 'ye geçirilme yeteneğine sahip olduğunu belirlemek için, Azure portal yeni Active Directory Federasyon Hizmetleri (AD FS) (AD FS) uygulama etkinliği raporunu kullanın. Rapor tüm AD FS uygulamaları Azure AD ile uyumluluk için değerlendirir, herhangi bir sorun olup olmadığını denetler ve tek tek uygulamaları geçişe hazırlamaya yönelik rehberlik sağlar.

Daha fazla bilgi için bkz. [uygulamaları Azure AD 'ye geçirmek için AD FS uygulama etkinliği raporunu kullanma](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Kullanıcıların yönetici onayı istemesi için yeni iş akışı (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** Access Control

Yeni yönetici onayı iş akışı yöneticilere yönetici onayı gerektiren uygulamalara erişim izni vermek için bir yol sağlar. Bir Kullanıcı bir uygulamaya erişmeyi dener, ancak izin sağlayamadığında, artık yönetici onayı için bir istek gönderebilirler. İstek, e-posta ile gönderilir ve Azure portal erişilebilen bir sıraya, gözden geçirenler olarak belirlenmiş tüm yöneticilere yerleştirilir. Gözden geçiren, bekleyen bir istek üzerinde işlem yaptıktan sonra, istenen kullanıcılara bu eylem bildirilir.

Daha fazla bilgi için bkz. [yönetici onayı iş akışını yapılandırma (Önizleme)](../manage-apps/configure-admin-consent-workflow.md).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>İsteğe bağlı talepleri yönetmek için yeni Azure AD Uygulaması kayıt belirteci yapılandırma deneyimi (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Geliştirici deneyimi

Azure portal yeni **Azure AD uygulaması kayıt belirteci yapılandırma** dikey penceresi artık uygulama geliştiricilerine uygulamalarına yönelik isteğe bağlı taleplerin dinamik bir listesini gösterir. Bu yeni deneyim, Azure AD uygulama geçişlerini kolaylaştırmaya ve isteğe bağlı talepler yapılandırmalarını en aza indirmeye yardımcı olur.

Daha fazla bilgi için bkz. [Azure AD uygulamanıza isteğe bağlı talepler sağlama](../develop/active-directory-optional-claims.md).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD Yetkilendirme Yönetimi 'nde yeni iki aşamalı onay iş akışı (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Yetkilendirme Yönetimi

Bir kullanıcının erişim paketine yönelik isteğini onaylaması için iki onaylayan zorunlu olmanızı sağlayan yeni bir iki aşamalı onay iş akışı sunuyoruz. Örneğin, bunu, istenen Kullanıcı yöneticisinin önce onaylaması gerekir, sonra da onaylanacak kaynak sahibi olmasını sağlayabilirsiniz. Onaylayanlardan biri onaylanmazsa erişim izni verilmez.

Daha fazla bilgi için bkz. [Azure AD yetkilendirme yönetiminde erişim paketi Için değişiklik isteği ve onay ayarları](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Yeni çalışma alanlarıyla birlikte uygulamalarım sayfası güncelleştirmeleri (Genel Önizleme)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Uygulamalarım  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

Artık kuruluşunuzun kullanıcılarının yenilenen uygulamalarımı görüntüleme ve bu deneyimle erişme yönteminizi özelleştirebilirsiniz. Bu yeni deneyim Ayrıca kullanıcılarınızın uygulamaları bulmasını ve düzenlemesini kolaylaştıran yeni çalışma alanları özelliğini de içerir.

Yeni uygulama deneyimi hakkında daha fazla bilgi ve çalışma alanı oluşturma hakkında daha fazla bilgi için bkz. [My Apps Portal 'da çalışma alanları oluşturma](../manage-apps/access-panel-collections.md).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B işbirliği için Google sosyal KIMLIK desteği (genel kullanılabilirlik)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** EAı  
**Ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD 'de Google sosyal kimliklerini (Gmail hesapları) kullanmaya yönelik yeni destek, kullanıcılarınız ve iş ortaklarınız için işbirliğinin daha basit olmasına yardımcı olur. Artık iş ortaklarınızın Microsoft 'a özgü yeni bir hesap oluşturması ve yönetmesi gerekmez. Microsoft ekipleri artık tüm istemcilerde ve Kiracıdaki ortak ve kiracı ile ilgili kimlik doğrulama uç noktalarında Google kullanıcılarını tam olarak desteklemektedir.

Daha fazla bilgi için bkz. [B2B Konuk kullanıcıları için kimlik sağlayıcısı olarak Google ekleme](../external-identities/google-federation.md).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Koşullu erişim ve çoklu oturum açma için Microsoft Edge mobil desteği (genel kullanılabilirlik)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Koşullu erişim  
**Ürün yeteneği:** Kimlik güvenliği & koruması

İOS ve Android 'de Microsoft Edge için Azure AD artık Azure AD tek Sign-On ve koşullu erişimi desteklemektedir:

- **Microsoft Edge çoklu oturum açma (SSO):** Çoklu oturum açma artık Azure AD 'ye bağlı tüm uygulamalar için yerel istemcilerde (Microsoft Outlook ve Microsoft Edge gibi) kullanılabilir.

- **Microsoft Edge koşullu erişimi:** Uygulama tabanlı koşullu erişim ilkeleri sayesinde, kullanıcılarınız Microsoft Edge gibi Microsoft Intune korumalı tarayıcıları kullanmalıdır.

Microsoft Edge ile koşullu erişim ve SSO hakkında daha fazla bilgi için bkz. [koşullu erişim Için Microsoft Edge mobil desteği ve çoklu oturum açma, genel kullanıma açık](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blog gönderisi. [Uygulama tabanlı koşullu erişim](../conditional-access/app-based-conditional-access.md) veya [cihaz tabanlı koşullu erişim](../conditional-access/require-managed-devices.md)kullanarak istemci uygulamalarınızı ayarlama hakkında daha fazla bilgi için, bkz. [Microsoft Intune İlkeyle korunan tarayıcı kullanarak Web erişimini yönetme](/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD Yetkilendirme Yönetimi (genel kullanılabilirlik)

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Farklı  
**Ürün yeteneği:** Yetkilendirme Yönetimi

Azure AD Yetkilendirme Yönetimi, kuruluşların kimlik ve erişim yaşam döngüsünü ölçekli olarak yönetmesine yardımcı olan yeni bir kimlik idare özelliğidir. Bu yeni özellik, erişim isteği iş akışlarını otomatikleştirerek, gruplar, uygulamalar ve SharePoint Online siteleri arasında atamaları, İncelemeleri ve kullanım süresinin tamamlanmasına yardımcı olur.

Azure AD yetkilendirme yönetimi sayesinde, hem çalışanlar hem de kuruluşunuzun dışındaki kullanıcılar için erişimi daha verimli bir şekilde yönetebilirsiniz.

Daha fazla bilgi için bkz. [Azure AD yetkilendirme yönetimi nedir?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Bu yeni desteklenen SaaS uygulamaları için Kullanıcı hesabı sağlamayı otomatikleştirin

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi  

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

[SAP Cloud Platform kimliği kimlik doğrulama hizmeti](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [spaceiq](../saas-apps/spaceiq-provisioning-tutorial.md), [mıro](../saas-apps/miro-provisioning-tutorial.md), [cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [Infor cloudsuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [officesi yazılımı](../saas-apps/officespace-software-provisioning-tutorial.md), [Öncelik matrisi](../saas-apps/priority-matrix-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Kasım 2019

**Şunu yazın:** Yeni Özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** üçüncü taraf tümleştirmesi

2019 Kasım 'da, uygulama galerisine federasyon desteğiyle bu 21 yeni uygulama ekledik:

[Airtable](../saas-apps/airtable-tutorial.md), [HootSuite](../saas-apps/hootsuite-tutorial.md), [Üyeler için mavi erişim (BAI)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [reslife portalı](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal çoklu oturum açma (SSO)](../saas-apps/negometrixportal-tutorial.md), [teamschamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [motlar](../saas-apps/motus-tutorial.md), [myaryaka](../saas-apps/myaryaka-tutorial.md), [Mave-posta](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md) [](../saas-apps/claromentis-tutorial.md), [onedesk](../saas-apps/onedesk-tutorial.md), [Foko perakende](../saas-apps/foko-retail-tutorial.md), [gpazarlar fikir & yenilik yönetimi](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [netüse Kullanıcı kimliği](../saas-apps/netskope-user-authentication-tutorial.md) [](../saas-apps/uniflow-online-tutorial.md) [](../saas-apps/jisc-student-voter-registration-tutorial.md) [](https://portal.e4enable.com/)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Yeni ve geliştirilmiş Azure AD Uygulama Galerisi

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO

Azure Active Directory kiracınızda sağlamayı, OpenID Connect ve SAML 'yi destekleyen önceden tümleştirilmiş uygulamaları bulmanızı kolaylaştırmak için Azure AD uygulama galerisini güncelleştirdik.

Daha fazla bilgi için bkz. [Azure Active Directory kiracınıza uygulama ekleme](../manage-apps/add-application-portal.md).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>120 karakterden 240 karaktere daha fazla uygulama rolü tanımı uzunluğu sınırı

**Şunu yazın:** Değiştirilen özellik  
**Hizmet kategorisi:** Kurumsal uygulamalar  
**Ürün yeteneği:** SSO

Müşterilerin bazı uygulama ve hizmetlerdeki uygulama rolü tanımı değeri için uzunluk sınırının 120 karakterden fazla kısa olduğunu duyduk. Yanıt olarak, rol değeri tanımının en büyük uzunluğunu 240 karakter olarak artırdık.

Uygulamaya özel rol tanımlarını kullanma hakkında daha fazla bilgi için bkz. [uygulamanıza uygulama rolleri ekleme ve bunları belirtece alma](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Ekim 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD Kimlik Koruması risk algılamaları için ıdentityriskevent API 'sinin kullanımdan kaldırılması

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Geliştirici geri bildirimlerine yanıt olarak Azure AD Premium P2 aboneleri artık Microsoft Graph için yeni riskDetection API 'sini kullanarak Azure AD Kimlik Koruması riskli algılama verilerinde karmaşık sorgular gerçekleştirebilir. Mevcut [ıdentityriskevent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API beta sürümü **10 Ocak 2020 '** den itibaren veri döndürmeyi durdurur. Kuruluşunuz ıdentityriskevent API 'sini kullanıyorsa, yeni riskDetection API 'sine geçiş yapmanız gerekir.

Yeni riskDetection API 'SI hakkında daha fazla bilgi için [risk algılama API 'si başvuru belgelerine](/graph/api/resources/riskdetection)bakın.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>SameSite özniteliği ve Chrome 80 için uygulama ara sunucusu desteği

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** uygulama proxy 'si **ürün yeteneği:** Access Control

Chrome 80 tarayıcı sürümünden önceki birkaç hafta önce, uygulama proxy tanımlama bilgilerinin **SameSite** özniteliğini nasıl değerlendirdiğinin güncelleştirilmesini planlıyoruz. Chrome 80 sürümü ile, **SameSite** özniteliğini belirtmeyen tüm tanımlama bilgileri, olarak ayarlanmış olsa da olarak kabul edilir `SameSite=Lax` .

Bu değişiklik nedeniyle olumsuz olumsuz etkileri önlemeye yardımcı olmak için uygulama proxy 'Si erişimi ve oturum tanımlama bilgilerini şu şekilde güncelleştiriyoruz:

- **Güvenli tanımlama bilgisi kullan** ayarı için varsayılan değer **Evet** olarak ayarlanıyor.

- **SameSite** özniteliği için varsayılan değer **none** olarak ayarlanıyor.

    >[!NOTE]
    > Uygulama proxy 'Si erişimi tanımlama bilgileri her zaman güvenli kanallar üzerinden iletilir. Bu değişiklikler yalnızca oturum tanımlama bilgileri için geçerlidir.

Uygulama proxy 'Si tanımlama bilgisi ayarları hakkında daha fazla bilgi için bkz. [Azure Active Directory ' de şirket içi uygulamalara erişmek Için tanımlama bilgisi ayarları](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Uygulama kayıt portalındaki (apps.dev.microsoft.com) Uygulama kayıtları (eski) ve uygulama yönetimi artık kullanılamıyor

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** yok **ürün yeteneği:** geliştirici deneyimi

Azure AD hesapları olan kullanıcılar artık uygulama kayıt portalı 'nı (apps.dev.microsoft.com) kullanarak uygulamaları kaydedemez veya yönetemez veya Azure portal Uygulama kayıtları (eski) deneyimdeki uygulamaları kaydedebilir ve yönetebilir.

Yeni Uygulama kayıtları deneyimi hakkında daha fazla bilgi edinmek için [Azure Portal eğitim kılavuzundaki uygulama kayıtları](../develop/quickstart-register-app.md)bakın.

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Kullanıcı başına MFA 'dan koşullu erişim tabanlı MFA 'ya geçiş sırasında kullanıcıların artık yeniden kaydedilmesi gerekmez

**Şunu yazın:** Sabit **hizmet kategorisi:** MFA **ürün yeteneği:** kimlik güvenliği & koruması

Kullanıcıların Kullanıcı başına Multi-Factor Authentication (MFA) için devre dışı bırakıldıklarında ve sonra koşullu erişim ilkesi aracılığıyla MFA için etkinleştirildiklerinde yeniden kaydolmaları gereken bilinen bir sorunu düzelttik.

Kullanıcıların yeniden kaydolmesini gerektirmek için, Azure AD portalındaki kullanıcının kimlik doğrulama yöntemlerinden **gereken MFA 'yı yeniden kaydet** seçeneğini belirleyebilirsiniz. Kullanıcı başına MFA 'dan koşullu erişim tabanlı MFA 'ya Kullanıcı geçirme hakkında daha fazla bilgi için bkz. [Kullanıcı BAŞıNA MFA 'Dan koşullu erişim tabanlı MFA 'ya Kullanıcı dönüştürme](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>SAML belirtecinizdeki talepleri dönüştürmek ve göndermek için yeni yetenekler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

SAML belirteçinizdeki talepleri özelleştirmenize ve göndermenize yardımcı olacak ek yetenekler ekledik. Bu yeni yetenekler şunlardır:

- Ek talep dönüştürme işlevleri, talepte göndereceğiniz değeri değiştirmenize yardımcı olur.

- Tek bir talebe birden çok dönüşüm uygulama özelliği.

- Kullanıcı türüne ve kullanıcının ait olduğu gruba göre talep kaynağını belirtme özelliği.

Bu yeni yetenekler hakkında daha fazla bilgi için, bkz. [Kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 'de son kullanıcılar için yeni oturum açma görevlerim sayfası

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** izleme & raporlama

Yeni **oturum açma** işlemleri sayfası ekledik ( https://mysignins.microsoft.com) kuruluşunuzun kullanıcılarına olağan dışı etkinlikleri denetlemek için en son oturum açma geçmişini görüntülemesine olanak tanır. Bu yeni sayfa, kullanıcılarınızın şunları görmesini sağlar:

- Herkes parolasını tahmin etmeye çalışıyorsa.

- Bir saldırgan hesabında ve bu konumdan başarıyla oturum açtı.

- Saldırganın erişmeye çalıştığı uygulamalar.

Daha fazla bilgi için, bkz. [Kullanıcılar artık olağan dışı etkinlik bloguna ait oturum açma geçmişini denetleyebilir](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD Domain Services (Azure AD DS) klasik 'ten Azure Resource Manager sanal ağlara geçirme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD Domain Services **ürün özelliği:** Azure AD Domain Services

Klasik sanal ağlarda takılı olan müşterilerimiz için çok güzel bir haberimiz var! Artık, klasik bir sanal ağdan mevcut bir Kaynak Yöneticisi sanal ağa tek seferlik bir geçiş gerçekleştirebilirsiniz. Kaynak Yöneticisi sanal ağa taşıdıktan sonra, hassas parola ilkeleri, e-posta bildirimleri ve denetim günlükleri gibi ek ve yükseltilmiş özelliklerden yararlanabilirsiniz.

Daha fazla bilgi için bkz. [Klasik sanal ağ modelinden Kaynak Yöneticisi Için önizleme-geçirme Azure AD Domain Services](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C sayfa sözleşmesi düzenine yönelik güncelleştirmeler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Azure AD B2C için sayfa sözleşmesinin sürüm 1.2.0 için bazı yeni değişiklikler ekledik. Bu güncelleştirilmiş sürümde, artık öğeleriniz için yükleme sırasını denetleyebilir ve bu da, stil sayfası (CSS) yüklenirken gerçekleşen titreşimi durdurmaya da yardımcı olabilir.

Sayfa sözleşmesinde yapılan değişikliklerin tam listesi için bkz. [sürüm değişiklik günlüğü](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Yeni çalışma alanlarıyla birlikte uygulamalarım sayfasına güncelleştir (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulamalarım **ürün yeteneği:** Access Control

Artık, kuruluşunuzun kullanıcılarının, uygulamaları bulmasını kolaylaştırmak için yeni çalışma alanları özelliğini kullanma dahil olmak üzere yepyeni uygulamalara görüntüleme ve bu deneyimle erişme biçimini özelleştirebilirsiniz. Yeni çalışma alanları işlevselliği, kuruluşunuzun kullanıcılarının zaten erişimi olan uygulamalar için bir filtre işlevi görür.

Yeni uygulamalarım deneyimini kullanıma alma ve çalışma alanları oluşturma hakkında daha fazla bilgi için bkz. [My Apps (Önizleme) portalında çalışma alanları oluşturma](../manage-apps/access-panel-collections.md).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Aylık etkin kullanıcı tabanlı faturalandırma modeli için destek (genel kullanılabilirlik)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Azure AD B2C artık aylık etkin kullanıcıların (MAU) faturalandırmasını desteklemektedir. MAU faturalandırma, bir takvim ayı sırasında kimlik doğrulama etkinliği olan benzersiz kullanıcı sayısını temel alır. Mevcut müşteriler, bu yeni faturalandırma yöntemine dilediğiniz zaman geçebilir.

1 Kasım 2019 ' den itibaren, tüm yeni müşteriler otomatik olarak bu yöntem kullanılarak faturalandırılacaktır. Bu Faturalandırma yöntemi, müşterilerin maliyet avantajları ve ilerleyebilme aracılığıyla faydalanır.

Daha fazla bilgi için bkz. [aylık etkin kullanıcıları faturalama modeline yükseltme](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Ekim 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

2019 Ekim 'de, uygulama galerisine federasyon desteğiyle bu 35 yeni uygulamalar ekledik:

[Çapraz-mobil](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno ile yolculuğa](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [kişi](https://www.tact.ai/products/tact-assistant), [opuscapita nakit yönetimi](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [salestim](https://www.salestim.com/), [learnster](../saas-apps/learnster-tutorial.md), [dynaTrace](../saas-apps/dynatrace-tutorial.md), [hunchbuzz](https://login.hunchbuzz.com/integrations/azure/process), en uygun [çalışma](../saas-apps/freshworks-tutorial.md), [ECornell](../saas-apps/ecornell-tutorial.md), [Shiphazı](../saas-apps/shiphazmat-tutorial.md), [NETÜSE Bulut güvenliği](../saas-apps/netskope-cloud-security-tutorial.md), [çekişme](../saas-apps/contentful-tutorial.md), [bindtuning](https://bindtuning.com/login), [HireVue koordinatı – Avrupa](https://www.hirevue.com/), [HireVue koordinatı-usonly](https://www.hirevue.com/), [HireVue koordinatı-US](https://www.hirevue.com/), [WittyParrot BILGI kutusu](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](../saas-apps/paylocity-tutorial.md), [posta şanslar!](../saas-apps/mail-luck-tutorial.md), [teamie](https://theteamie.com/), [takımlar için hız](https://velocity.peakup.org/teams/login), [sıgnl4](https://account.signl4.com/manage), [EAB gidilecek Impl](../saas-apps/eab-navigate-impl-tutorial.md), [ekran karşılamaları](https://console.screenmeet.com/), [Omega noktası](https://pi.ompnt.com/), [konuşma](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)için konuşma e-postası, [for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](../saas-apps/exactcare-sso-tutorial.md), [ıhealthhome bakım gezinti sistemi](https://ihealthnav.com/account/signin), [qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD portalındaki birleştirilmiş güvenlik menü öğesi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Artık, mevcut Azure AD güvenlik özelliklerinin tümüne yeni **güvenlik** menü öğesinden ve **arama** çubuğundan Azure Portal erişebilirsiniz. Ayrıca, **güvenlik-Başlarken** adlı yeni **güvenlik** giriş sayfası, ortak belgelerimize, güvenlik kılavuzumuza ve dağıtım kılavuzlarımıza bağlantılar sağlar.

Yeni **güvenlik** menüsü şunları içerir:

- Koşullu Erişim
- Kimlik Koruması
- Güvenlik Merkezi
- Kimlik güvenli puanı
- Kimlik doğrulama yöntemleri
- Çok faktörlü kimlik doğrulaması
- Risk raporları-riskli kullanıcılar, riskli oturum açmalar, risk algılamaları
- Ve daha fazlası...

Daha fazla bilgi için bkz. [güvenlik-Başlarken](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Otomatik yenileme ile geliştirilmiş Office 365 grupları süre sonu ilkesi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

Office 365 grupları süre sonu ilkesi, üyeleri tarafından etkin olarak kullanılan grupları otomatik olarak yenilemek üzere geliştirilmiştir. Gruplar, Outlook, SharePoint ve takımlar dahil olmak üzere tüm Office 365 uygulamaları genelinde Kullanıcı etkinliğine göre autorenewed olacaktır.

Bu geliştirme, Grup süre sonu bildirimlerinizi azaltmaya yardımcı olur ve etkin grupların kullanılabilir olmaya devam etmesine yardımcı olur. Office 365 gruplarınız için zaten etkin bir süre sonu ilkeniz varsa, bu yeni işlevselliği açmak için herhangi bir şey yapmanız gerekmez.

Daha fazla bilgi için bkz. [Office 365 grupları için süre sonu Ilkesini yapılandırma](../enterprise-users/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Azure AD Domain Services (Azure AD DS) oluşturma deneyimi güncelleştirildi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Azure AD Domain Services **ürün yeteneği:** Azure AD Domain Services

Azure AD Domain Services (Azure AD DS) yeni ve geliştirilmiş bir oluşturma deneyimi içerecek şekilde güncelleştirdik ve yalnızca üç tıklamayla yönetilen bir etki alanı oluşturmanıza yardımcı olur! Ayrıca, artık Azure AD DS bir şablondan karşıya yükleyebilir ve dağıtabilirsiniz.

Daha fazla bilgi için bkz. [öğretici: Azure Active Directory Domain Services örneği oluşturma ve yapılandırma](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>Eylül 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Değişiklik planı: Power BI içerik paketlerinin kullanımdan kaldırılması

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** raporlama **ürün yeteneği:** izleme & raporlama

1 Ekim 2019 ' den başlayarak Power BI, Azure AD Power BI içerik paketi de dahil olmak üzere tüm içerik paketlerini kullanımdan kaldırmaya başlayacaktır. Bu içerik paketine alternatif olarak Azure AD çalışma kitaplarını kullanarak Azure AD ile ilgili hizmetleriniz hakkında öngörüler elde edebilirsiniz. Yalnızca rapor modundaki koşullu erişim ilkeleriyle ilgili çalışma kitapları, uygulama onayı tabanlı Öngörüler ve daha fazlası dahil olmak üzere ek çalışma kitapları geliyor.

Çalışma kitapları hakkında daha fazla bilgi için bkz. [Azure izleyici çalışma kitaplarını Azure Active Directory raporları için kullanma](../reports-monitoring/howto-use-azure-monitor-workbooks.md). İçerik paketlerinin kullanımdan kaldırılması hakkında daha fazla bilgi için bkz. [Power BI Template Apps genel kullanılabilirliği](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) blog gönderisi.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Profilimi Microsoft Office hesabı sayfasıyla yeniden adlandırma ve Tümleştirme

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** profil/hesap **ürün yeteneği:** işbirliği

Ekim 'den itibaren profilim deneyimi Hesabım olur. Bu değişikliğin bir parçası olarak, şu anda şöyle ki, **Profilim** **Hesabım** olarak değişecektir. Adlandırma değişikliğinin en üstünde ve bazı tasarım geliştirmelerinden, güncelleştirilmiş deneyim Microsoft Office hesabı sayfasıyla ek tümleştirme sunacak. Özellikle, **genel bakış hesabı** sayfasından Office yüklemelerine ve aboneliklerine, **Gizlilik** sayfasından Office ile ilgili iletişim tercihlerini de erişebileceksiniz.

Profilim (Önizleme) deneyimi hakkında daha fazla bilgi için bkz. [Profilim (Önizleme) portalına genel bakış](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Azure AD portalındaki CSV dosyalarını kullanarak grupları ve üyeleri toplu olarak yönetme (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Azure AD portalındaki toplu Grup Yönetimi deneyimlerinin genel önizleme kullanılabilirliğine duyurmaktan mutluluk duyuyoruz. Artık aşağıdakiler dahil olmak üzere grupları ve üye listelerini yönetmek için bir CSV dosyası ve Azure AD portalını kullanabilirsiniz:

- Gruptan üye ekleme veya kaldırma.

- Dizindeki grupların listesi indiriliyor.

- Belirli bir grup için Grup üyeleri listesi indiriliyor.

Daha fazla bilgi için bkz. [toplu üye ekleme](../enterprise-users/groups-bulk-import-members.md), [üyeleri toplu kaldırma](../enterprise-users/groups-bulk-remove-members.md), [üyeleri toplu indirme listesi](../enterprise-users/groups-bulk-download-members.md)ve [Toplu indirme grupları listesi](../enterprise-users/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dinamik izin artık yeni bir yönetici onay uç noktası aracılığıyla destekleniyor

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Microsoft Identity platformunda dinamik izin modelini kullanmak isteyen uygulamalar için yararlı olan dinamik izni desteklemek için yeni bir yönetici onay uç noktası oluşturduk.

Bu yeni uç noktanın nasıl kullanılacağı hakkında daha fazla bilgi için, bkz. [Yönetici onay uç noktası kullanma](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları mevcuttur-Eylül 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Eylül 2019 ' de, uygulama galerisine federasyon desteğiyle bu 29 yeni uygulamaları ekledik:

[Schedulelook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO erişimi, etidex uyumluluk ofisi &trade; -Çoklu oturum açma](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Iver portalı](../saas-apps/iserver-portal-tutorial.md), [ufuk sitesi](../saas-apps/skysite-tutorial.md), [yarışmayı ve harcama](../saas-apps/concur-travel-and-expense-tutorial.md), [çalışma panosu](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/` [yay tesislerini](../saas-apps/arc-facilities-tutorial.md), [luware strazı ekibi](https://stratus.emea.luware.cloud/login), [geniş fikirler](https://wideideas.online/wideideas/), [przma bulutu](../saas-apps/prisma-cloud-tutorial.md), [jdlt Client hub](https://clients.jdlt.co.uk/login), [renpaku](../saas-apps/renraku-tutorial.md), [sealpath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [przma bulutu](../saas-apps/prisma-cloud-tutorial.md), `https://app.penneo.com/` ,, işlem `https://app.testhtm.com/settings/email-integration` [çok fazla bulut](https://aec.cintoo.com/login), [beyaz kaynak](../saas-apps/whitesource-tutorial.md), [barındırılan herials online SSO](../saas-apps/hosted-heritage-online-sso-tutorial.md), [IDC](../saas-apps/idc-tutorial.md), [cakehr](../saas-apps/cakehr-tutorial.md) [,](../saas-apps/bis-tutorial.md)b, [COO Kai ekip derlemesi](https://ms-contacts.coo-kai.jp/), [sonarquin](../saas-apps/sonarqube-tutorial.md), [Adobe Identity Management](../saas-apps/tutorial-list.md), [Discovery avantajları SSO](../saas-apps/discovery-benefits-sso-tutorial.md), [Amelio](https://app.amelio.co/), `https://itask.yipinapp.com/`

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-azure-ad-global-reader-role"></a>Yeni Azure AD Genel okuyucu rolü

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD rolleri **ürün yeteneği:** Access Control

24 Eylül 2019 ' den itibaren, genel okuyucu adlı yeni bir Azure Active Directory (AD) rolü kullanıma sunacağız. Bu piyasaya çıkma üretim ve küresel bulut müşterileri (GCC) ile başlar ve Ekim 'de dünya çapında bir işlem görür.

Genel okuyucu rolü, genel yöneticiye salt okunurdur. Bu roldeki kullanıcılar Microsoft 365 hizmetleri genelinde ayarları ve yönetim bilgilerini okuyabilir, ancak yönetim eylemleri alamaz. Kuruluşunuzdaki genel yönetici sayısını azaltmaya yardımcı olmak için genel okuyucu rolünü oluşturduk. Genel yönetici hesapları güçlü ve saldırılara karşı savunmasız olduğundan, beşten az genel yönetici olmasını öneririz. Planlama, denetim veya araştırmalar için genel okuyucu rolünü kullanmanızı öneririz. Ayrıca, küresel bir yönetici rolü gerekmeden çalışmanın yapılmasına yardımcı olması için Exchange Yöneticisi gibi diğer sınırlı yönetici rolleriyle birlikte genel okuyucu rolünü kullanmanızı öneririz.

Genel okuyucu rolü, yeni Microsoft 365 Yönetim Merkezi, Exchange Yönetim Merkezi, takımlar Yönetim Merkezi, Güvenlik Merkezi, Uyumluluk Merkezi, Azure AD Yönetim Merkezi ve cihaz yönetimi Yönetim Merkezi ile birlikte kullanılabilir.

>[!NOTE]
> Genel Önizleme 'nin başlangıcında, genel okuyucu rolü şu şekilde çalışmaz: SharePoint, Privileged Access Management, Müşteri Kasası, duyarlılık etiketleri, takımlar yaşam döngüsü, takımlar raporlama & çağrı analizi, takımlar IP telefon cihazı yönetimi ve takımlar uygulama kataloğu.

Daha fazla bilgi için [Azure Active Directory Içindeki yönetici rolü izinleri](../roles/permissions-reference.md)bölümüne bakın.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Azure Active Directory Uygulama Ara Sunucusu kullanarak Power BI Mobil uygulamanızdan şirket içi rapor sunucusuna erişin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** Access Control

Power BI Mobile App ile Azure AD Uygulama Ara Sunucusu arasında yeni tümleştirme, Power BI mobil uygulamada güvenli bir şekilde oturum açmanızı ve şirket içi Power BI Rapor Sunucusu barındırılan kuruluşunuzun tüm raporlarını görüntülemenize olanak sağlar.

Uygulamanın indirileceği dahil Power BI Mobil uygulama hakkında daha fazla bilgi için [Power BI sitesine](https://powerbi.microsoft.com/mobile/)bakın. Power BI mobil uygulamayı Azure AD Uygulama Ara Sunucusu ile ayarlama hakkında daha fazla bilgi için bkz. [azure ad uygulama ara sunucusu ile Power BI mobil uzaktan erişimi etkinleştirme](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell modülünün yeni sürümü kullanılabilir

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** Dizin

Yeni cmdlet 'ler, Azure AD 'de aşağıdakiler de dahil olmak üzere özel rolleri tanımlamaya ve atamaya yardımcı olmak için AzureADPreview modülüne eklenmiştir:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect yeni sürümü

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** Dizin

Otomatik yükseltme müşterileri için Azure AD Connect güncelleştirilmiş bir sürümünü yayımladık. Bu yeni sürüm bazı yeni özellikler, geliştirmeler ve hata düzeltmeleri içerir. Bu yeni sürüm hakkında daha fazla bilgi için bkz. [Azure AD Connect: sürüm yayınlama geçmişi](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication (MFA) sunucusu, sürüm 8.0.2 artık kullanılabilir

**Şunu yazın:** Sabit **hizmet kategorisi:** MFA **ürün yeteneği:** kimlik güvenliği & koruması

MFA sunucusunu 1 Temmuz 2019 ' den önce etkinleştiren mevcut bir müşteriyseniz, artık MFA sunucusu 'nun en son sürümünü (sürüm 8.0.2) indirebilirsiniz. Bu yeni sürümde şunları yaptık:

- Bir sorun düzeltildi böylece Azure AD eşitleme bir kullanıcıyı devre dışı durumundan etkin olarak değiştirdiğinde kullanıcıya bir e-posta gönderilir.

- Etiketler işlevini kullanmaya devam ederken müşterilerin başarılı bir şekilde yükseltebilmesi için bir sorun düzeltildi.

- Kosovo (+ 383) ülke kodu eklendi.

- MultiFactorAuthSvc. log dosyasına bir kerelik atlama denetim günlüğü eklendi.

- Web hizmeti SDK 'sının performansı geliştirildi.

- Diğer küçük hatalar düzeltildi.

Microsoft, 1 Temmuz 2019 ' den itibaren Yeni dağıtımlar için MFA sunucusunu sunmayı durdurdu. Multi-Factor Authentication gerektiren yeni müşterilerin bulut tabanlı Azure AD Multi-Factor Authentication kullanması gerekir. Daha fazla bilgi için bkz. [bulut tabanlı Azure AD Multi-Factor Authentication dağıtımı planlama](../authentication/howto-mfa-getstarted.md).

---

## <a name="august-2019"></a>Ağustos 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Azure AD portalında (Genel Önizleme), gruplar için gelişmiş arama, filtreleme ve sıralama kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Azure AD portalındaki gelişmiş gruplar ile ilgili deneyimlerin genel önizleme kullanılabilirliğine duyurmaktan mutluluk duyuyoruz. Bu geliştirmeler, aşağıdakileri sağlayarak grupları ve üye listelerini daha iyi yönetmenize yardımcı olur:

- Gruplar listesinde alt dize arama gibi gelişmiş arama özellikleri.
- Üye ve sahip listelerindeki Gelişmiş filtreleme ve sıralama seçenekleri.
- Üye ve sahip listeleri için yeni arama özellikleri.
- Büyük gruplar için daha doğru grup sayısı.

Daha fazla bilgi için [Azure Portal grupları yönetme](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)bölümüne bakın.

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Yeni özel roller uygulama kayıt yönetimi için kullanılabilir (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD rolleri **ürün yeteneği:** Access Control

Özel Roller (bir Azure AD P1 veya P2 aboneliği ile kullanılabilir) artık, belirli izinlerle rol tanımları oluşturmanıza ve ardından bu rolleri belirli kaynaklara atamaya izin vererek size ayrıntılı erişim sağlamanıza yardımcı olabilir. Şu anda, uygulama kayıtlarını yönetmek ve ardından rolü belirli bir uygulamaya atamak için izinleri kullanarak özel roller oluşturursunuz. Özel roller hakkında daha fazla bilgi için, bkz. [Azure Active Directory (Önizleme) Içinde özel yönetici rolleri](../roles/custom-overview.md).

Şu anda görmezseniz ek izinlere veya kaynaklara ihtiyacınız varsa, [Azure geri bildirim sitenize](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) geri bildirimde bulunabilir ve isteğinizi güncelleştirme yol haritemiz olarak ekleyeceğiz.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Yeni sağlama günlükleri, uygulama sağlama dağıtımınızı izlemenize ve sorunlarını gidermenize yardımcı olabilir (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

Kullanıcı ve grup sağlama dağıtımını izlemenize ve sorunlarını gidermenize yardımcı olacak yeni sağlama günlükleri mevcuttur. Bu yeni günlük dosyaları aşağıdakiler hakkında bilgiler içerir:

- [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) 'da başarıyla oluşturulan gruplar
- [AWS Single-Account erişimi](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access) 'nden hangi roller içeri aktarıldı
- [Workday](../saas-apps/workday-inbound-tutorial.md) 'den hangi çalışanların içeri aktarılmadığı

Daha fazla bilgi için, [Azure Active Directory portalında (Önizleme) raporları sağlama](../reports-monitoring/concept-provisioning-logs.md)konusuna bakın.

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Tüm Azure AD yöneticileri için yeni güvenlik raporları (genel kullanılabilirlik)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Varsayılan olarak, Azure AD yöneticileri, Azure AD içindeki modern güvenlik raporlarına yakında erişebilecektir. Eylül sonuna kadar, eski raporlara geri dönmek için modern güvenlik raporlarının en üstündeki başlığı kullanabileceksiniz.

Modern güvenlik raporları, aşağıdakiler de dahil olmak üzere eski sürümlerden ek yetenekler sağlayacaktır:

- Gelişmiş filtreleme ve sıralama
- Eksik Kullanıcı riski gibi toplu eylemler
- Güvenliği aşılmış veya güvenli varlıkların onayı
- Risk durumu, kapsayan: risk, kapatılan, düzeltilen ve onaylanan güvenliği aşılmış
- Risk ile ilgili yeni algılamalar (Azure AD Premium aboneler için kullanılabilir)

Daha fazla bilgi için bkz. [riskli kullanıcılar](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [riskli oturum açma](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)işlemleri ve [risk algılamaları](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Kullanıcı tarafından atanan yönetilen kimlik, sanal makineler ve sanal makine ölçek kümeleri (genel kullanılabilirlik) için kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure kaynakları için Yönetilen kimlikler **ürün yeteneği:** geliştirici deneyimi

Kullanıcı tarafından atanan Yönetilen kimlikler artık sanal makineler ve sanal makine ölçek kümeleri için genel kullanıma sunulmuştur. Bunun bir parçası olarak Azure, Azure AD kiracısında kullanımda olan aboneliğin güvendiği ve bir veya daha fazla Azure hizmet örneğine atanabilecek bir kimlik oluşturabilir. Kullanıcı tarafından atanan Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Kullanıcılar bir mobil uygulama veya donanım belirteci (genel kullanım) kullanarak parolalarını sıfırlayabilir

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** self servis parola sıfırlama **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Kuruluşunuz ile bir mobil uygulama kaydeden kullanıcılar artık Microsoft Authenticator uygulamasından bir bildirim seçerek veya mobil uygulamasından veya donanım belirtecinden bir kod girerek kendi parolalarını sıfırlayabilir.

Daha fazla bilgi için bkz. [nasıl çalıştığını öğrenin: Azure AD self servis parola sıfırlama](../authentication/concept-sspr-howitworks.md). Kullanıcı deneyimi hakkında daha fazla bilgi için bkz. [kendi iş veya okul parolanızı sıfırlama genel bakış](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET, yerinde senaryolar için MSAL.NET paylaşılan önbelleğini yoksayar

**Şunu yazın:** Sabit **hizmet kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD kimlik doğrulama kitaplığı (ADAL.NET) sürüm 5.0.0-Preview sürümünden itibaren, uygulama geliştiricileri [Web uygulamaları ve Web API 'leri için hesap başına bir önbellek serileştirmelidir](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Aksi takdirde, bazı belirli kullanım durumları ile birlikte Java için [Şirket adına yönelik akış](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) kullanan bazı senaryolar `UserAssertion` ayrıcalık yükselmesine neden olabilir. Bu güvenlik açığından kaçınmak için, ADAL.NET artık, için Microsoft kimlik doğrulama kitaplığı için DotNet (MSAL.NET) paylaşılan önbellek için Microsoft kimlik doğrulama kitaplığını yok sayar.

Bu sorun hakkında daha fazla bilgi için bkz. [Azure Active Directory kimlik doğrulaması kitaplığı ayrıcalık yükselmesi güvenlik açığı](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Ağustos 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Ağustos 2019 ' de, uygulama galerisine federasyon desteğiyle bu 26 yeni uygulamalar ekledik:

[Cıvic platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [bilitodoks](../saas-apps/cognidox-tutorial.md), [viareport 'ın Inativ Portal (Avrupa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [deneme](../saas-apps/robin-tutorial.md), akademik katılım, [Öncelik matrisi](https://sync.appfluence.com/pmwebng/), [cousto MySpace](https://cousto.platformers.be/account/login), [uploadilgilenme](https://uploadcare.com/accounts/signup/), [Carbonite uç nokta yedeklemesi](../saas-apps/carbonite-endpoint-backup-tutorial.md),, [Incom ile cpqsync](../saas-apps/cpqsync-by-cincom-tutorial.md), [chargebee](../saas-apps/chargebee-tutorial.md), [sunma. Media &trade; Portal](https://portal.deliver.media), [Frontline eğitim](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Connect](https://www.stashcat.com), [yanıp sönme](../saas-apps/blink-tutorial.md), [vocoli](../saas-apps/vocoli-tutorial.md), [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [sigstr](../saas-apps/sigstr-tutorial.md), [darwinbox](../saas-apps/darwinbox-tutorial.md), [renklerin göre izleme](../saas-apps/watch-by-colors-tutorial.md) [, ana,](../saas-apps/harness-tutorial.md) [EAB stratejik bakım](../saas-apps/eab-navigate-strategic-care-tutorial.md) [](../saas-apps/academy-attendance-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>AzureAD PowerShell ve AzureADPreview PowerShell modüllerinin yeni sürümleri mevcuttur

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** Dizin

AzureAD ve AzureAD Preview PowerShell modüllerine yönelik yeni güncelleştirmeler mevcuttur:

- `-Filter` `Get-AzureADDirectoryRole` Azuread modülündeki parametreye yeni bir parametre eklendi. Bu parametre, cmdlet tarafından döndürülen dizin rollerini filtrelemenize yardımcı olur.
- Yeni cmdlet 'ler, Azure AD 'de aşağıdakiler de dahil olmak üzere özel rolleri tanımlamaya ve atamaya yardımcı olmak için AzureADPreview modülüne eklenmiştir:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure portal dinamik grup kuralı oluşturucusunun Kullanıcı arabirimine yönelik iyileştirmeler

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Yeni bir kuralı daha kolay ayarlamanıza veya mevcut kuralları değiştirmenize yardımcı olması için Azure portal kullanılabilir olan dinamik grup kuralı Oluşturucusu 'nda bazı Kullanıcı arabirimi geliştirmeleri yaptık. Bu tasarım geliştirmesi, yalnızca bir tane yerine en fazla beş ifadeye sahip kurallar oluşturmanıza olanak sağlar. Ayrıca, kullanım dışı bırakılan cihaz özelliklerini kaldırmak için cihaz özelliği listesini güncelleştirdik.

Daha fazla bilgi için bkz. [dinamik üyelik kurallarını yönetme](../enterprise-users/groups-dynamic-membership.md).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Yeni Microsoft Graph uygulama izni, erişim gözden geçirmeleri ile kullanıma sunuldu

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** erişim gözden geçirmeleri **ürün yeteneği:** Identity idare

`AccessReview.ReadWrite.Membership`Uygulamaların grup üyelikleri ve uygulama atamaları için otomatik olarak erişim İncelemeleri oluşturmasına ve almasına izin veren yeni bir Microsoft Graph App izni sunuyoruz. Bu izin, zamanlanmış işleriniz veya Otomasyon kapsamında oturum açmış bir kullanıcı bağlamı gerekmeden kullanılabilir.

Daha fazla bilgi için bkz. [PowerShell blogu ile Microsoft Graph uygulama izinleri kullanarak Azure AD erişim incelemeleri oluşturma örneği](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD etkinlik günlükleri artık Azure Izleyici 'de kamu bulutu örnekleri için kullanılabilir

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure AD etkinlik günlüklerinin artık Azure Izleyici 'deki kamu bulutu örnekleri için kullanılabilir olduğunu duyurmaktan mutluluk duyuyoruz. Artık [SumoLogic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)ve [arctıma](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md)gibi SIEM araçlarınızla tümleştirebilmek için depolama hesabınıza veya bir olay hub 'ına Azure AD günlükleri gönderebilirsiniz.

Azure Izleyici 'yi ayarlama hakkında daha fazla bilgi için bkz. Azure [izleyici 'de Azure ad etkinlik günlükleri](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Kullanıcılarınızı yeni, geliştirilmiş güvenlik bilgileri deneyimiyle güncelleştirin

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:**  kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

25 Eylül 2019 ' de, Kullanıcı güvenlik bilgilerini kaydetmek ve yönetmek ve yalnızca yeni ve [geliştirilmiş sürümü](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)açmak için eski, gelişmiş olmayan güvenlik bilgileri deneyimini kapatıyoruz. Bu, kullanıcılarınızın artık eski deneyimi kullanamayacağı anlamına gelir.

Gelişmiş güvenlik bilgileri deneyimi hakkında daha fazla bilgi için [yönetici belgelerimize](../authentication/concept-registration-mfa-sspr-combined.md) ve [Kullanıcı belgelerimize](../user-help/security-info-setup-signin.md)bakın.

#### <a name="to-turn-on-this-new-experience-you-must"></a>Bu yeni deneyimi etkinleştirmek için şunları yapmanız gerekir:

1. Azure portal genel yönetici veya Kullanıcı Yöneticisi olarak oturum açın.

2. **Azure Active Directory > Kullanıcı ayarları ' na gidin > erişim paneli Önizleme özellikleri ayarlarını yönetin**.

3. Kullanıcılar, **güvenlik bilgilerini kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilir-gelişmiş** alanı, **Seçili**' i seçin ve ardından bir Kullanıcı grubu seçebilir ya da bu özelliği Kiracıdaki tüm kullanıcılar için açmak üzere **Tümünü** seçebilirsiniz.

4. * * Kullanıcılar, güvenliği kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilir * * bilgi * * * * alanı, **hiçbiri**' ni seçin.

5. Ayarlarınızı kaydedin.

    Ayarlarınızı kaydettikten sonra eski güvenlik bilgileri deneyimine artık erişebileceksiniz.

>[!Important]
>25 Eylül 2019 tarihinden önce bu adımları tamamlamazsanız, Azure Active Directory kiracınız gelişmiş deneyim için otomatik olarak etkinleştirilir. Sorularınız varsa lütfen adresinden bizimle iletişime geçin registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Oturum açma işlemleri kullanan kimlik doğrulama istekleri daha kesin olarak doğrulanacak

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** standartlar

2 Eylül 2019 ' den itibaren POST yöntemini kullanan kimlik doğrulama istekleri HTTP standartlarına karşı daha kesin olarak onaylanır. Özellikle, boşluklar ve çift tırnak işaretleri (") artık istek formu değerlerinden kaldırılmayacak. Bu değişikliklerin mevcut istemcileri bozmak beklenmez ve Azure AD 'ye gönderilen isteklerin her seferinde güvenilir bir şekilde işlendiğinden emin olmaya yardımcı olur.

Daha fazla bilgi için bkz. [Azure AD bölünmesi değişiklikleri bildirimleri](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Temmuz 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Değişiklik planı: yalnızca TLS 1,2 ' i destekleyecek uygulama proxy hizmeti güncelleştirmesi

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** uygulama proxy 'si **ürün yeteneği:** Access Control

En güçlü şifreimizi size sağlamaya yardımcı olmak için, uygulama proxy hizmeti erişimini yalnızca TLS 1,2 protokollerine sınırlandırmaya başlayacağız. Bu sınırlama, başlangıçta zaten TLS 1,2 protokollerini kullanan müşterilere dağıtılır, bu nedenle etkiyi görmezsiniz. TLS 1,0 ve TLS 1,1 protokollerinin kullanım dışı bırakılması, 31 Ağustos 2019 tarihinde tamamlanacaktır. Hala TLS 1,0 ve TLS 1,1 kullanan müşteriler, bu değişikliğe hazırlanmak için gelişmiş bir bildirim alır.

Bu değişiklik boyunca uygulama ara sunucusu hizmetiyle bağlantıyı sürdürmek için, istemci-sunucu ve tarayıcı-sunucu birleşimlerinizin TLS 1,2 kullanacak şekilde güncelleştirildiğinden emin olmanızı öneririz. Ayrıca, uygulama proxy 'Si hizmeti aracılığıyla yayınlanan uygulamalara erişmek için çalışanlarınızın kullandığı tüm istemci sistemlerini dahil ettiğinizden emin olmanızı öneririz.

Daha fazla bilgi için bkz. [Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Değişiklik planı: uygulama galerisine yönelik tasarım güncelleştirmeleri geliyor

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** kurumsal uygulamalar **ürün yeteneği:** SSO

Yeni Kullanıcı arabirimi değişiklikleri, **Uygulama Ekle** dikey penceresinin Galeri alanından **Ekle** ' nin tasarımına geliyor. Bu değişiklikler, otomatik sağlamayı, OpenID Connect, Security Assertion Markup Language (SAML) ve parola çoklu oturum açmayı (SSO) destekleyen uygulamalarınızı daha kolay bir şekilde bulmanıza yardımcı olur.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Değişiklik planı: MFA sunucusu IP adresinin Office 365 IP adresinden kaldırılması

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** MFA **ürün yeteneği:** kimlik güvenliği & koruması

MFA sunucusu IP adresini [Office 365 IP adresi ve URL Web hizmetinden](/office365/enterprise/office-365-ip-web-service)kaldırıyoruz. Şu anda güvenlik duvarı ayarlarınızı güncelleştirmek için bu sayfalara sahipseniz [azure Multi-Factor Authentication sunucusu](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) kullanmaya başlama konusunun **Azure Multi-Factor Authentication sunucusu güvenlik duvarı GEREKSINIMLERI** bölümünde belgelenen IP adreslerinin listesini de dahil ettiğinizden emin olmanız gerekir.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Yalnızca uygulama belirteçleri, artık istemci uygulamasının kaynak kiracısında var olmasını gerektiriyor

**Şunu yazın:** Sabit **hizmet kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

26 Temmuz 2019 ' de, [istemci kimlik bilgileri verme](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)yoluyla yalnızca uygulama belirteçleri sağlıyoruz. Daha önce, uygulamalar, istemci uygulamanın kiracıda olup olmamasına bakılmaksızın diğer uygulamaları çağırmak için belirteçleri alabilir. Bu davranışı, bazen Web API 'Leri olarak adlandırılan tek kiracılı kaynakların yalnızca kaynak kiracısında bulunan istemci uygulamaları tarafından çağrılabilir şekilde güncelleştirdik.

Uygulamanız kaynak kiracısında değilse, bu sorunu çözmek için, bir hata iletisi alırsınız `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` . Bu sorunu gidermek için, [Yönetici onay uç noktasını](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) veya [PowerShell](../develop/howto-authenticate-service-principal-powershell.md)'i kullanarak kiracıda istemci uygulama hizmeti sorumlusunu oluşturmanız gerektiğini, kiracınızın kiracı içinde çalışmaya yönelik uygulama iznini vermiş olmasını sağlar.

Daha fazla bilgi için bkz. [kimlik doğrulaması yenilikleri nelerdir?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> İstemci ve API arasındaki mevcut onay gerekli olmaya devam eder. Uygulamalar yine de kendi yetkilendirme denetimlerini yapmakta olmalıdır.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>FIDO2 güvenlik anahtarları kullanılarak Azure AD 'de yeni parolasız oturum açma

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD müşterileri artık, kuruluşlarının kullanıcıları ve grupları için FIDO2 güvenlik anahtarlarını yönetmek üzere ilkeler ayarlayabilir. Son kullanıcılar ayrıca güvenlik anahtarlarını kendi kendine kaydedebilir, FIDO özellikli cihazlarda Microsoft hesaplarında oturum açmak için anahtarları kullanabilir ve Azure AD 'ye katılmış Windows 10 cihazlarında oturum açabilirler.

Daha fazla bilgi için bkz. yönetici ile ilgili bilgiler için [Azure AD 'de passwordless oturum açma 'Yı etkinleştirme (Önizleme)](../authentication/concept-authentication-passwordless.md) ve son kullanıcı ile ilgili bilgiler için Güvenlik [anahtarı (Önizleme) kullanmak üzere güvenlik bilgilerini ayarlama](../user-help/security-info-setup-security-key.md) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD Uygulaması galerisinde yeni Federasyon uygulamaları kullanılabilir-Temmuz 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Haziran 2019 ' de, uygulama galerisine federasyon desteği olan bu 18 yeni uygulamayı ekledik:

[Ungerboeck yazılımı](../saas-apps/ungerboeck-software-tutorial.md), [parlak model omnichannel iletişim merkezi](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [Clever Nelly](../saas-apps/clever-nelly-tutorial.md), [acquireio](../saas-apps/acquireio-tutorial.md), [gevop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO, etiıdex uyumluluk ofisi &trade; ](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [hype](../saas-apps/hype-tutorial.md), [soyut](../saas-apps/abstract-tutorial.md), [ascentis](../saas-apps/ascentis-tutorial.md), [flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [wandera](../saas-apps/wandera-tutorial.md), [TwineSocial](https://twinesocial.com/), [kallifer](../saas-apps/kallidus-tutorial.md), [hyperanna](../saas-apps/hyperanna-tutorial.md) [,](../saas-apps/jfrog-artifactory-tutorial.md) [ilaç](https://pharmid.com/) [](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Bu yeni desteklenen SaaS uygulamaları için Kullanıcı hesabı sağlamayı otomatikleştirin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** izleme & raporlama

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federasyon Dizini](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Ağ güvenlik grubu için yeni Azure AD Domain Services hizmet etiketi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD Domain Services **ürün özelliği:** Azure AD Domain Services

IP adresi ve aralıklarının uzun listesini yönetiyorsanız, Azure AD Domain Services sanal ağ alt ağına gelen trafiğin güvenliğini sağlamaya yardımcı olması için Azure ağ güvenlik grubunuzdaki yeni **AzureActiveDirectoryDomainServices** Network Service etiketini kullanabilirsiniz.

Bu yeni hizmet etiketi hakkında daha fazla bilgi için bkz. [Azure AD Domain Services Için ağ güvenlik grupları](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services için yeni güvenlik denetimleri (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD Domain Services **ürün özelliği:** Azure AD Domain Services

Azure AD etki alanı hizmeti güvenlik denetimi 'nin genel önizlemeye sunulduğunu duyurmaktan mutluluk duyuyoruz. Güvenlik denetimi, Azure depolama, Azure Log Analytics çalışma alanları ve Azure Olay Hub 'ı dahil olmak üzere Azure AD etki alanı hizmet portalı 'nı kullanarak, hedeflenen kaynaklara güvenlik denetim olayları aktararak kimlik doğrulama hizmetlerinize yönelik kritik Öngörüler sağlamanıza yardımcı olur.

Daha fazla bilgi için bkz. [Azure AD Domain Services Için güvenlik denetimlerini etkinleştirme (Önizleme)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Yeni kimlik doğrulama yöntemleri kullanım & öngörüleri (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** self servis parola sıfırlama **ürün yeteneği:** izleme & raporlama

Yeni kimlik doğrulama yöntemleri kullanım & öngörüleri raporları, Azure AD Multi-Factor Authentication ve self servis parola sıfırlama gibi özelliklerin kuruluşunuzda nasıl kaydedildiğini ve kullanıldığını, her bir özellik için kayıtlı Kullanıcı sayısı, parolaları sıfırlamak için kendi kendine parola sıfırlamasının ne sıklıkta kullanıldığını ve sıfırlamanın ne yaptığını anlamanıza yardımcı olabilir.

Daha fazla bilgi için bkz. [kimlik doğrulama yöntemleri kullanımı & Öngörüler (Önizleme)](../authentication/howto-authentication-methods-activity.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Tüm Azure AD yöneticileri için yeni güvenlik raporları kullanılabilir (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Tüm Azure AD yöneticileri artık riskli **Kullanıcılar** ve **riskli oturum açma** raporlarında gösterildiği gibi yeni güvenlik deneyimini kullanmaya başlamak için, **risk için işaretlenmiş kullanıcılar** raporu gibi mevcut güvenlik raporlarının en üstündeki başlığı seçebilirler. Zaman içinde, tüm güvenlik raporları eski sürümlerden yeni sürümlere taşınır ve yeni raporlar aşağıdaki ek olanakları sağlar:

- Gelişmiş filtreleme ve sıralama

- Eksik Kullanıcı riski gibi toplu eylemler

- Güvenliği aşılmış veya güvenli varlıkların onayı

- Risk durumu, kapsayan: risk, kapatılan, düzeltilen ve onaylanan güvenliği aşılmış

Daha fazla bilgi için bkz. [riskli kullanıcılar raporu](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) ve [riskli oturum açma bileşenleri raporu](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD Domain Services için yeni güvenlik denetimleri (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD Domain Services **ürün özelliği:** Azure AD Domain Services

Azure AD etki alanı hizmeti güvenlik denetimi 'nin genel önizlemeye sunulduğunu duyurmaktan mutluluk duyuyoruz. Güvenlik denetimi, Azure depolama, Azure Log Analytics çalışma alanları ve Azure Olay Hub 'ı dahil olmak üzere Azure AD etki alanı hizmet portalı 'nı kullanarak, hedeflenen kaynaklara güvenlik denetim olayları aktararak kimlik doğrulama hizmetlerinize yönelik kritik Öngörüler sağlamanıza yardımcı olur.

Daha fazla bilgi için bkz. [Azure AD Domain Services Için güvenlik denetimlerini etkinleştirme (Önizleme)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>SAML/WS-beslemesiyle yeni B2B Direct Federation (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2B **ürün yeteneği:** B2B/B2C

Doğrudan Federasyon, SAML veya WS-Fed standartlarını destekleyen kimlik sistemleriyle çalışarak, BT tarafından yönetilen kimlik çözümü Azure AD olmayan iş ortaklarıyla çalışmanızı kolaylaştırır. Bir iş ortağıyla doğrudan Federasyon ilişkisi ayarladıktan sonra, bu etki alanından davet ettiğiniz tüm yeni Konuk kullanıcıları mevcut kurumsal hesaplarını kullanarak sizinle işbirliği yapabilir ve konuklarınız için Kullanıcı deneyimini daha sorunsuz hale getirir.

Daha fazla bilgi için bkz. [Konuk kullanıcılar için AD FS ve üçüncü taraf sağlayıcılarla doğrudan Federasyon (Önizleme)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Bu yeni desteklenen SaaS uygulamaları için Kullanıcı hesabı sağlamayı otomatikleştirin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** izleme & raporlama

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federasyon Dizini](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Azure AD portalındaki yinelenen grup adları için yeni denetim

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Artık Azure AD portalından bir grup adı oluşturduğunuzda veya güncelleştirdiğinizde, kaynakta mevcut bir grup adını çoğaltdığınıza ilişkin bir denetim yapacağız. Adın zaten başka bir grup tarafından kullanımda olduğunu tespit ettik, adınızı değiştirmeniz istenir.

Daha fazla bilgi için bkz. [Azure AD portalındaki grupları yönetme](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD artık yanıt (yeniden yönlendirme) URI 'Lerinde statik sorgu parametrelerini desteklemektedir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD Apps artık, `https://contoso.com/oauth2?idp=microsoft` OAuth 2,0 istekleri için statik sorgu parametreleriyle (örneğin,) Yanıtla (yeniden yönlendirme) URI 'leri kaydedebilir ve kullanabilir. Statik sorgu parametresi, yanıt URI 'sinin diğer herhangi bir bölümünde olduğu gibi, yanıt URI 'Leri için dize eşlemeye tabidir. URL kodu çözülmüş yeniden yönlendirme URI 'si ile eşleşen kayıtlı bir dize yoksa, istek reddedilir. Yanıt URI 'SI bulunursa, statik sorgu parametresi dahil olmak üzere, kullanıcının yeniden yönlendirileceği tüm dize kullanılır.

Bir güvenlik riskini temsil ettiğinden dinamik yanıt URI 'Leri hala yasaktır ve bir kimlik doğrulama isteği genelinde durum bilgilerini sürdürmek için kullanılamaz. Bu amaçla `state` parametresini kullanın.

Şu anda Azure portal uygulama kayıt ekranları hala sorgu parametrelerini engelliyor. Ancak, uygulamanıza sorgu parametrelerini eklemek ve test etmek için uygulama bildirimini el ile düzenleyebilirsiniz. Daha fazla bilgi için bkz. [kimlik doğrulaması yenilikleri nelerdir?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Azure AD için etkinlik günlükleri (MS Graph API 'Leri) artık PowerShell cmdlet 'Leri aracılığıyla kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure AD etkinlik günlüklerinin (denetim ve oturum açma raporları) artık Azure AD PowerShell modülü aracılığıyla kullanılabildiğini duyurmak için heyecanlıyız. Daha önce, MS Graph API uç noktaları kullanarak kendi betiklerinizi oluşturabilir ve şimdi bu özelliği PowerShell cmdlet 'lerine genişlettik.

Bu cmdlet 'leri kullanma hakkında daha fazla bilgi için bkz. [Raporlama Için Azure AD PowerShell cmdlet 'leri](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Azure AD 'de denetim ve oturum açma günlükleri için filtre denetimleri güncelleştirildi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Denetim ve oturum açma günlüğü raporlarını, artık rapor ekranlarında sütun olarak eklemek zorunda kalmadan çeşitli filtreler uygulayabilmeniz için güncelleştirdik. Ayrıca, ekranda kaç filtre göstermek istediğinizi de seçebilirsiniz. Bu güncelleştirmeler, raporların daha kolay okunmasını ve gereksinimlerinize göre daha fazla kapsamlanmasını kolaylaştırmak için birlikte çalışır.

Bu güncelleştirmeler hakkında daha fazla bilgi için bkz. [Denetim günlüklerini filtreleme](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) ve [oturum açma etkinliklerini filtreleme](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Haziran 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Microsoft Graph için yeni riskDetections API (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Microsoft Graph için yeni riskDetections API 'sini duyurmaktan mutluluk duyuyoruz, artık genel önizlemede. Bu yeni API 'YI, kuruluşunuzun kimlik korumasıyla ilgili Kullanıcı ve oturum açma riski algılamalarının bir listesini görüntülemek için kullanabilirsiniz. Bu API 'yi, algılama türü, durumu, düzeyi ve daha fazlası hakkındaki ayrıntılar dahil, risk algılamalarınızı daha verimli bir şekilde sorgulamak için de kullanabilirsiniz.

Daha fazla bilgi için [risk algılama API 'si başvuru belgelerine](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)bakın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Haziran 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Haziran 2019 ' de, uygulama galerisine federasyon desteğiyle bu 22 yeni uygulamaları ekledik:

[Azure AD SAML araç seti](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN istemcisi](https://portal.azure.com/), [expenseın](../saas-apps/expensein-tutorial.md), [Helper Yardımcısı](../saas-apps/helper-helper-tutorial.md), [costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [SIVARK SAML kimlik doğrulaması](../saas-apps/cyberark-saml-authentication-tutorial.md), IServiceProvider [,](https://www.scrible.com/sign-in/#/create-account) [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [proptıbol OS](https://proptimise.co.uk/), [vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Retail Merchandising için Oracle Erişim Yöneticisi, Oracle e-iş paketi için Oracle Erişim Yöneticisi, c-Business Suite için Oracle IDCs, JD Edler için Oracle IDCs

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Bu yeni desteklenen SaaS uygulamaları için Kullanıcı hesabı sağlamayı otomatikleştirin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** izleme & raporlama

Artık yeni tümleştirilmiş uygulamalar için Kullanıcı hesapları oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirebilirsiniz:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlamayı kullanarak kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Azure AD sağlama hizmeti 'nin gerçek zamanlı ilerlemesini görüntüleme

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

Azure AD sağlama deneyimini, Kullanıcı sağlama sürecinde ne kadar uzakta olduğunu gösteren yeni bir ilerleme çubuğu içerecek şekilde güncelleştirdik. Bu güncelleştirilmiş deneyim Ayrıca, geçerli döngüyle sağlanan kullanıcı sayısı ve ne kadar Kullanıcı sağlandığını gösteren bilgileri de sağlar.

Daha fazla bilgi için bkz. [Kullanıcı hazırlama durumunu denetleme](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Şirket markası artık oturum kapatma ve hata ekranları üzerinde görünüyor

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD 'yi, şirket markasının artık oturum açma ve hata ekranlarında, oturum açma sayfasından da görünmesini sağlayacak şekilde güncelleştirdik. Bu özelliği açmak için herhangi bir şey yapmanız gerekmez, Azure AD yalnızca Azure portal **Şirket markası** alanında ayarlamış olduğunuz varlıkları kullanır.

Şirket markanızı ayarlama hakkında daha fazla bilgi için bkz. [kuruluşunuzun Azure Active Directory sayfalarına marka ekleme](./customize-branding.md).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication (MFA) sunucusu artık Yeni dağıtımlar için kullanılamaz

**Şunu yazın:** Kullanım dışı **hizmet kategorisi:** MFA **ürün yeteneği:** kimlik güvenliği & koruması

1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sunmaz. Kuruluşunuzda Multi-Factor Authentication istemek isteyen yeni müşterilerin artık bulut tabanlı Azure AD Multi-Factor Authentication kullanması gerekir. MFA sunucusunu 1 Temmuz 'dan önce etkinleştiren müşteriler bir değişiklik görmez. Hala en son sürümü indirebilir, gelecekteki güncelleştirmeleri alabilir ve etkinleştirme kimlik bilgileri oluşturabilirsiniz.

Daha fazla bilgi için bkz. [Azure Multi-Factor Authentication sunucusu](../authentication/howto-mfaserver-deploy.md)kullanmaya başlama. Bulut tabanlı Azure AD Multi-Factor Authentication hakkında daha fazla bilgi için bkz. [bulut tabanlı Azure ad Multi-Factor Authentication dağıtımı planlama](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Mayıs 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Hizmet değişikliği: uygulama proxy 'Si hizmetinde yalnızca TLS 1,2 protokolleri için gelecekteki destek

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** uygulama proxy 'si **ürün yeteneği:** Access Control

Müşterilerimiz için en iyi sınıf şifrelemeyi sağlamaya yardımcı olmak için, yalnızca uygulama proxy 'Si hizmetindeki TLS 1,2 protokollerine erişimi sınırlıyoruz. Bu değişiklik, yalnızca TLS 1,2 protokollerini kullanan müşterilere yavaş bir şekilde dağıtılır, bu nedenle herhangi bir değişiklik göremezsiniz.

TLS 1,0 ve TLS 1,1 ' nin kullanımdan kaldırılması 31 Ağustos 2019 tarihinde yapılır, ancak bu değişikliğe hazırlanmak için zaman duyarsınız. Bu değişikliğe hazırlanmak için, kullanıcılarınızın uygulama proxy 'Si aracılığıyla yayınlanan uygulamalara erişmek için kullanacağı istemciler dahil olmak üzere, istemci-sunucu ve tarayıcı-sunucu birleşimlerinizin, uygulama proxy 'Si hizmetiyle bağlantıyı sürdürmek için TLS 1,2 protokolünü kullanacak şekilde güncelleştirildiğinden emin olun. Daha fazla bilgi için bkz. [Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Uygulamayla ilgili oturum açma verilerinizi görüntülemek için kullanım ve Öngörüler raporunu kullanın

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** izleme & raporlama

Artık aşağıdakiler hakkında bilgiler de dahil olmak üzere oturum açma verilerinizin uygulama merkezli bir görünümünü almak için Azure portal **Kurumsal uygulamalar** alanında bulunan kullanım ve Öngörüler raporunu kullanabilirsiniz:

- Kuruluşunuz için kullanılan en popüler uygulamalar

- En fazla başarısız oturum açma işlemleri içeren uygulamalar

- Her uygulama için ilk oturum açma hataları

Bu özellik hakkında daha fazla bilgi için [Azure Active Directory portalındaki Kullanım ve Öngörüler raporuna](../reports-monitoring/concept-usage-insights-report.md) bakın

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Azure AD kullanarak bulut uygulamalarına Kullanıcı hazırlığını otomatikleştirin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** izleme & raporlama

Aşağıdaki bulut tabanlı uygulamalar için Kullanıcı hesaplarının oluşturulmasını, silinmesini ve güncelleştirilmesini otomatik hale getirmek için Azure AD sağlama hizmetini kullanmak üzere bu yeni öğreticilerini izleyin:

- [Birlikte karşılayın](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [Permi güvenliği](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Ayrıca, Grup nesneleri sağlama hakkında bilgi sağlayan bu yeni [Dropbox öğreticisini](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)de izleyebilirsiniz.

Otomatik Kullanıcı hesabı sağlama aracılığıyla kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Kimlik güvenli puanı artık Azure AD 'de kullanılabilir (genel kullanılabilirlik)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** yok **ürün yeteneği:** kimlik güvenliği & koruması

Artık Azure AD 'de kimlik güvenli puanı özelliğini kullanarak kimlik güvenliğini izleyip geliştirebilirsiniz. Kimlik güvenli puanı özelliği, size yardımcı olması için tek bir pano kullanır:

- NesneKimliği, 1 ile 223 arasında bir puan temelinde kimlik güvenliği önlemidir.

- Kimlik güvenlik geliştirmelerinizi planlayın

- Güvenlik geliştirmelerinizin başarısını inceleyin

Kimlik güvenliği puanı özelliği hakkında daha fazla bilgi için bkz. [Azure Active Directory kimlik güvenli puanı nedir?](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Yeni Uygulama kayıtları deneyimi artık kullanıma sunuldu (genel kullanılabilirlik)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** geliştirici deneyimi

Yeni [uygulama kayıtları](https://aka.ms/appregistrations) deneyimi artık genel kullanıma sunulmuştur. Bu yeni deneyim, Azure portal ve uygulama kayıt Portalı ' ndan bildiğiniz tüm önemli özellikleri içerir ve bunları aracılığıyla geliştirir:

- **Daha iyi uygulama yönetimi.** Uygulamalarınızı farklı portallarda görmek yerine, artık tüm uygulamalarınızı tek bir konumda görebilirsiniz.

- **Basitleştirilmiş uygulama kaydı.** Geliştirilmiş gezinti deneyiminden, yeniden kullanılabilir izin seçimi deneyimine yönelik olarak, uygulamalarınızı kaydetmek ve yönetmek artık daha kolay.

- **Daha ayrıntılı bilgi.** Hızlı başlangıç kılavuzlarınız ve daha fazlası dahil olmak üzere uygulamanız hakkında daha fazla ayrıntı bulabilirsiniz.

Daha fazla bilgi için bkz. [Microsoft Identity platform](../develop/index.yml) ve [uygulama kayıtları deneyimi artık genel kullanıma sunuldu!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) Blog duyurusu.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Kimlik koruması için riskli kullanıcılar API 'sinde sunulan yeni yetenekler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Kullanıcıların risk geçmişini almak, riskli kullanıcıları kapatmak ve kullanıcıları tehlikeye atılmak üzere doğrulamak için riskli kullanıcılar API 'sini artık kullanacağınızı duyurmaktan memnuniyet duyuyoruz. Bu değişiklik, kullanıcılarınızın risk durumunu daha verimli bir şekilde güncelleştirmenize ve risk geçmişini anlamanıza yardımcı olur.

Daha fazla bilgi için [riskli KULLANıCıLAR API başvuru belgelerine](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)bakın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Mayıs 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Mayıs 2019 ' de, uygulama galerisine federasyon desteğiyle bu 21 yeni uygulama ekledik:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [gerçek bağlantılar](../saas-apps/real-links-tutorial.md), [Kida](https://app.kianda.com/sso/OpenID/AzureAD/), [basit imza](../saas-apps/simple-sign-tutorial.md), [BRAZE](../saas-apps/braze-tutorial.md), [displayr](../saas-apps/displayr-tutorial.md), [templamy](../saas-apps/templafy-tutorial.md), [Marketo Sales](https://toutapp.com/login)katılım, [aclp](../saas-apps/aclp-tutorial.md), [OutSystems](../saas-apps/outsystems-tutorial.md), [META4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), [hisse çalışma alanı](../saas-apps/quantum-workplace-tutorial.md), [Cobalt](../saas-apps/cobalt-tutorial.md), [WebMethods API bulutu](../saas-apps/webmethods-integration-cloud-tutorial.md), [redflag](https://pocketstop.com/redflag/), [whatdüzeltmesini](../saas-apps/whatfix-tutorial.md), [denetimi](../saas-apps/control-tutorial.md), [jobhub](../saas-apps/jobhub-tutorial.md), [neogov](../saas-apps/neogov-tutorial.md), [pdee](../saas-apps/foodee-tutorial.md), [myvr](../saas-apps/myvr-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Azure AD portalında geliştirilmiş Grup oluşturma ve yönetim deneyimleri

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Azure AD portalındaki gruplarla ilgili deneyimler için geliştirmeler yaptık. Bu geliştirmeler, yöneticilerin grup listelerini, üye listelerini daha iyi yönetmesine ve ek oluşturma seçenekleri sağlamasına imkan tanır.

Geliştirmeler şunları içerir:

- Üyelik türüne ve grup türüne göre temel filtreleme.

- Kaynak ve e-posta adresi gibi yeni sütunların eklenmesi.

- Kolay bir şekilde silinmek üzere grupları, üyeleri ve sahip listelerini çoklu seçme özelliği.

- Grup oluşturma sırasında e-posta adresi seçme ve sahipler ekleme özelliği.

Daha fazla bilgi için bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Azure AD portalındaki Office 365 grupları için adlandırma ilkesi yapılandırma (genel kullanılabilirlik)

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Yöneticiler artık Azure AD portalını kullanarak Office 365 grupları için bir adlandırma ilkesi yapılandırabilir. Bu değişiklik, kuruluşunuzdaki kullanıcılar tarafından oluşturulan veya düzenlenen Office 365 grupları için tutarlı adlandırma kuralları uygulanmasını sağlar.

Office 365 grupları için adlandırma ilkesini iki farklı yolla yapılandırabilirsiniz:

- Otomatik olarak bir grup adına eklenen ön ekleri veya sonekleri tanımlayın.

- Kuruluşunuz için Grup adlarında izin verilmeyen özelleştirilmiş bir engellenen sözcük kümesini karşıya yükleyin (örneğin, "CEO, bordro, HR").

Daha fazla bilgi için bkz. [Office 365 grupları Için adlandırma Ilkesi zorlama](../enterprise-users/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API uç noktaları artık Azure AD etkinlik günlükleri (genel kullanılabilirlik) için kullanılabilir

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure AD etkinlik günlükleri için Microsoft Graph API uç noktası desteğinin genel kullanıma sunulduğunu duyurmaktan mutluluk duyuyoruz. Bu sürümle birlikte artık hem Azure AD denetim günlüklerinin hem de oturum açma günlüğü API 'Lerinin 1,0 sürümünü kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure AD denetim günlüğü API 'sine genel bakış](/graph/api/resources/azure-ad-auditlog-overview).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Yöneticiler artık Birleşik kayıt işlemi (Genel Önizleme) için koşullu erişimi kullanabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği & koruması

Yöneticiler artık Birleşik kayıt sayfası tarafından kullanılmak üzere koşullu erişim ilkeleri oluşturabilir. Bu, şu durumlarda kayda izin vermek için ilkeleri uygulamayı içerir:

- Kullanıcılar güvenilir bir ağ üzerinde.

- Kullanıcılar, düşük bir oturum açma riskidir.

- Kullanıcılar yönetilen bir cihazlardır.

- Kullanıcılar kuruluşun kullanım koşulları 'nı (TOU) kabul etmiş olursunuz.

Koşullu erişim ve parola sıfırlama hakkında daha fazla bilgi için [Azure AD BIRLEŞIK MFA ve parola sıfırlama kayıt deneyimi blog gönderisine yönelik koşullu erişimi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)görebilirsiniz. Birleşik kayıt işlemi için koşullu erişim ilkeleri hakkında daha fazla bilgi için bkz. [Birleşik kayıt Için koşullu erişim ilkeleri](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration). Azure AD kullanım koşulları özelliği hakkında daha fazla bilgi için bkz. [kullanım koşulları özelliği Azure Active Directory](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>Nisan 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Yeni Azure AD tehdit zekası algılaması artık Azure AD Kimlik Koruması bir parçası olarak sağlanıyor

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Azure AD Threat Intelligence algılama artık güncelleştirilmiş Azure AD Kimlik Koruması özelliğinin bir parçası olarak sunulmaktadır. Bu yeni işlevsellik, Microsoft 'un dahili ve dış tehdit bilgileri kaynaklarına dayanan bilinen saldırı desenleriyle tutarlı olan belirli bir kullanıcı veya etkinliğin olağan dışı Kullanıcı etkinliklerini belirtmenize yardımcı olur.

Azure AD Kimlik Koruması yenilenen sürümü hakkında daha fazla bilgi için, bkz. [dört ana Azure AD kimlik koruması geliştirmesi artık genel önizleme](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) bloguna ve [ne Azure Active Directory kimlik koruması (YENİLENDİ)?](../identity-protection/overview-identity-protection.md) bakın. Azure AD Threat Intelligence algılaması hakkında daha fazla bilgi için [risk algılamalarını Azure Active Directory kimlik koruması](../identity-protection/concept-identity-protection-risks.md) makalesine bakın.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD Yetkilendirme Yönetimi artık kullanıma sunuldu (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Identity idare **ürün yeteneği:** kimlik yönetimi

Artık genel önizlemede olan Azure AD Yetkilendirme Yönetimi, müşterilerin, çalışanların ve iş ortaklarının erişim isteme, ne kadar süreceğine ve ne kadar erişebileceğini tanımlayan erişim paketleri yönetimini temsilcmalarına yardımcı olur. Erişim paketleri Azure AD ve Office 365 gruplarındaki üyeliği, kurumsal uygulamalardaki rol atamalarını ve SharePoint Online siteleri için rol atamalarını yönetebilir. Yetkilendirme Yönetimi hakkında daha fazla bilgi için [bkz. Azure AD Yetkilendirme Yönetimi](../governance/entitlement-management-overview.md). Privileged Identity Management, erişim gözden geçirmeleri ve kullanım koşulları dahil Azure AD Identity Governance özellikleri hakkında daha fazla bilgi edinmek için bkz. [Azure AD Identity Governance nedir?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Azure AD portalındaki Office 365 grupları için adlandırma ilkesi yapılandırma (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Yöneticiler artık Azure AD portalını kullanarak Office 365 grupları için bir adlandırma ilkesi yapılandırabilir. Bu değişiklik, kuruluşunuzdaki kullanıcılar tarafından oluşturulan veya düzenlenen Office 365 grupları için tutarlı adlandırma kuralları uygulanmasını sağlar.

Office 365 grupları için adlandırma ilkesini iki farklı yolla yapılandırabilirsiniz:

- Otomatik olarak bir grup adına eklenen ön ekleri veya sonekleri tanımlayın.

- Kuruluşunuz için Grup adlarında izin verilmeyen özelleştirilmiş bir engellenen sözcük kümesini karşıya yükleyin (örneğin, "CEO, bordro, HR").

Daha fazla bilgi için bkz. [Office 365 grupları Için adlandırma Ilkesi zorlama](../enterprise-users/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD etkinlik günlükleri artık Azure Izleyici 'de kullanılabilir (genel kullanılabilirlik)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure AD etkinlik günlükleri ile görselleştirmelerle ilgili geri bildirimlerinizi ele almak için, Log Analytics yeni bir Öngörüler özelliği sunuyoruz. Bu özellik, çalışma kitapları adlı etkileşimli şablonlarımızı kullanarak Azure AD kaynaklarınız hakkında öngörüler elde etmenize yardımcı olur. Önceden oluşturulmuş bu çalışma kitapları, uygulamalar veya kullanıcılar için Ayrıntılar sağlayabilir ve şunları içerir:

- **Oturum açma işlemleri.** Uygulama ve kullanıcıların oturum açma konumu, kullanımda olan işletim sistemi veya tarayıcı istemcisi ve sürümü ve başarılı ya da başarısız oturum açma sayısı dahil olmak üzere ayrıntıları sağlar.

- **Eski kimlik doğrulama ve koşullu erişim.** Koşullu erişim ilkeleri tarafından tetiklenen Multi-Factor Authentication kullanımı, koşullu erişim ilkelerini kullanan uygulamalar vb. dahil olmak üzere eski kimlik doğrulaması kullanan uygulamalar ve kullanıcılar için ayrıntılar sağlar.

- **Oturum açma hatası analizi.** Kullanıcı eylemi, ilke sorunları veya altyapınız nedeniyle oturum açma hatalarınızın oluşup oluşmadığını belirlemenize yardımcı olur.

- **Özel raporlar.** Kuruluşunuz için Öngörüler özelliğini özelleştirmeye yardımcı olmak üzere yeni veya mevcut çalışma kitaplarını düzenleyebilirsiniz.

Daha fazla bilgi için bkz. [Azure izleyici çalışma kitaplarını Azure Active Directory raporları için kullanma](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Nisan 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

2019 Nisan 'da, uygulama galerisine federasyon desteğiyle bu 21 yeni uygulama ekledik:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [Hrçalışmalar çoklu oturum açma](../saas-apps/hrworks-single-sign-on-tutorial.md), [percogeç](../saas-apps/percolate-tutorial.md), [Mobıoncontrol](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [shibumi](../saas-apps/shibumi-tutorial.md), [benchling](../saas-apps/benchling-tutorial.md), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](../saas-apps/pagedna-tutorial.md), [eğitibrite LMS](../saas-apps/edubrite-lms-tutorial.md), [rstudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [Mitel Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (rol tabanlı SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [cerkatlanmış hisse senedi yönetimi](../saas-apps/certent-equity-management-tutorial.md), [sectigo Sertifika Yöneticisi](../saas-apps/sectigo-certificate-manager-tutorial.md), [tarzorbit](../saas-apps/greenorbit-tutorial.md), [workgrid](../saas-apps/workgrid-tutorial.md), [Monday.com](../saas-apps/mondaycom-tutorial.md), [gözcemaymun Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [ındiggo](https://indiggolead.com/)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Yeni erişim İncelemeleri sıklık seçeneği ve birden çok rol seçimi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** erişim gözden geçirmeleri **ürün yeteneği:** Identity idare

Azure AD erişim gözden geçirmeleriyle yeni güncelleştirmeler şunları yapmanıza olanak sağlar:

- Daha önce haftalık, aylık, üç aylık ve yıllık olan mevcut seçeneklere ek olarak, erişim incelemelerinizin sıklığını **yarı yıllık** olarak değiştirin.

- Tek bir erişim incelemesi oluştururken birden çok Azure AD ve Azure Kaynak rolü seçin. Bu durumda, tüm roller aynı ayarlarla ayarlanır ve tüm gözden geçirenler aynı anda bilgilendirilir.

Erişim incelemesi oluşturma hakkında daha fazla bilgi için bkz. [Azure AD erişim gözden geçirmeleriyle gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-posta uyarı sistemleri geçiyor, bazı müşteriler için yeni e-posta gönderici bilgileri gönderiliyor

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** ad eşitleme **ürün yeteneği:** platform

Azure AD Connect, e-posta uyarı sistemlerimizi geçiş sürecinde olduğundan, bazı müşterilere yeni bir e-posta gönderici gösteriliyor olabilir. Bunu çözmek için `azure-noreply@microsoft.com` kuruluşunuzun izin öğesine eklemeniz gerekir veya Office 365, Azure veya eşitleme hizmetinizden önemli uyarıları almaya devam edemeyeceksiniz.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN son ek değişiklikleri artık Azure AD Connect Federasyon etki alanları arasında başarılı oluyor

**Şunu yazın:** Sabit **hizmet kategorisi:** ad eşitleme **ürün yeteneği:** platform

Artık bir kullanıcının UPN sonekini, Azure AD Connect bir Federasyon etki alanından başka bir Federasyon etki alanına başarıyla değiştirebilirsiniz. Bu çözüm, eşitleme çevrimi sırasında FederatedDomainChangeError hata iletisini artık deneymemelisiniz veya "[FederatedUser. UserPrincipalName] özniteliği geçerli olmadığından," Bu nesne Azure Active Directory güncelleştirilemiyor, "bir bildirim e-postası alacaksınız. Yerel dizin hizmetinizdeki değeri güncelleştirin.

Daha fazla bilgi için bkz. [eşitleme sırasında sorun giderme hataları](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Azure AD 'de uygulama koruma tabanlı koşullu erişim ilkesini kullanarak artırılmış güvenlik (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği & koruması

Uygulama koruma tabanlı koşullu erişim artık **Uygulama koruma Ilkesi gerektir** ilkesi kullanılarak kullanılabilir. Bu yeni ilke, aşağıdakileri önlemeye yardımcı olarak kuruluşunuzun güvenliğini artırmaya yardımcı olur:

- Kullanıcılar Microsoft Intune lisansı olmadan uygulamalara erişim hakkı elde edebilirler.

- Kullanıcılar Microsoft Intune uygulama koruma ilkesi alamıyor.

- Kullanıcılar, yapılandırılmış bir Microsoft Intune uygulama koruma ilkesi olmadan uygulamalara erişim hakkı elde edebilirler.

Daha fazla bilgi için bkz. [koşullu erişim ile Cloud App erişimi için uygulama koruma Ilkesi gerektirme](../conditional-access/app-protection-based-conditional-access.md).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 'de Azure AD çoklu oturum açma ve koşullu erişim için yeni destek (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği & koruması

Azure AD çoklu oturum açma ve koşullu erişim için yeni destek sağlama dahil olmak üzere Microsoft Edge için Azure AD desteğimizi geliştirdik. Daha önce Microsoft Intune Managed Browser kullandıysanız, artık bunun yerine Microsoft Edge 'i kullanabilirsiniz.

Koşullu erişim kullanarak cihazlarınızı ve uygulamalarınızı ayarlama ve yönetme hakkında daha fazla bilgi için bkz. [koşullu erişim ile Cloud App erişimi için yönetilen cihazlar gerektir](../conditional-access/require-managed-devices.md) ve [koşullu erişim ile Cloud App erişimi için onaylanan istemci uygulamaları gerektir](../conditional-access/app-based-conditional-access.md). Microsoft Edge 'i Microsoft Intune ilkeleriyle kullanarak erişimin nasıl yönetileceği hakkında daha fazla bilgi için, bkz. [Microsoft Intune İlkeyle korunan tarayıcı kullanarak Internet erişimini yönetme](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Mart 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C 'da kimlik deneyimi çerçevesi ve özel ilke desteği artık kullanılabilir (GA)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Artık Azure AD B2C, ölçeklendirerek ve Azure SLA 'umuz kapsamında desteklenen aşağıdaki görevler de dahil olmak üzere özel ilkeler oluşturabilirsiniz:

- Özel ilkeler kullanarak özel kimlik doğrulama Kullanıcı yolculukları oluşturun ve karşıya yükleyin.

- Talep sağlayıcıları arasındaki değişimleri adım adım kabul eden kullanıcı hakkındaki adımları açıklama.

- Kullanıcı yolculukları 'nda koşullu dallanma tanımlayın.

- Gerçek zamanlı kararlar ve iletişimlerde kullanım için talepleri dönüştürün ve eşleyin.

- Özel kimlik doğrulama Kullanıcı yolculuğa REST API özellikli Hizmetleri kullanın. Örneğin, e-posta sağlayıcıları, CRMs ve özel yetkilendirme sistemleri.

- Openıdconnect protokolüyle uyumlu kimlik sağlayıcılarıyla federasyona bağlayın. Örneğin, çok kiracılı Azure AD, sosyal hesap sağlayıcıları veya iki öğeli doğrulama sağlayıcıları ile.

Özel ilkeler oluşturma hakkında daha fazla bilgi için, bkz. [Azure Active Directory B2C özel ilkeler Için geliştirici notları](../../active-directory-b2c/custom-policy-developer-notes.md) ve [örnek olay incelemeleri de dahil, Alex SION blog gönderisini](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)okuyun.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Mart 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

2019 Mart 'da, uygulama galerisine federasyon desteği olan bu 14 yeni uygulamaları ekledik:

[ISEC7 mobil Exchange temsilcisi](https://www.isec7.com/english/), [mediusflow](https://office365.cloudapp.mediusflow.com/), [eplatform](../saas-apps/eplatform-tutorial.md), [fulcrud](../saas-apps/fulcrum-tutorial.md), [excelityglobal](../saas-apps/excelityglobal-tutorial.md), [Açıklama tabanlı denetim sistemi](../saas-apps/explanation-based-auditing-system-tutorial.md), [yalın](../saas-apps/lean-tutorial.md), [powerokul performansı önemli](../saas-apps/powerschool-performance-matters-tutorial.md), [cincode](https://cinode.com/), [Iris intraneti](../saas-apps/iris-intranet-tutorial.md), [empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md), [tas](../saas-apps/tas-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD galerisinde yeni Zscaler ve Atlasser sağlama bağlayıcıları-Mart 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği:** üçüncü taraf tümleştirmesi

Aşağıdaki uygulamalar için Kullanıcı hesaplarını oluşturmayı, güncellemeyi ve silmeyi otomatik hale getirin:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler iki](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler üç](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler zscsesli](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [atlasser bulutu](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Otomatik Kullanıcı hesabı sağlama aracılığıyla kuruluşunuzun daha iyi güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure AD Ile SaaS uygulamalarına Kullanıcı sağlamayı otomatikleştirme](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Azure AD portalındaki silinen Office 365 gruplarınızı geri yükleme ve yönetme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Artık silinen Office 365 gruplarınızı Azure AD portalından görüntüleyebilir ve yönetebilirsiniz. Bu değişiklik, geri yükleme için hangi grupların kullanılabilir olduğunu görmenizi sağlar ve kuruluşunuzun ihtiyaç duymayan grupları kalıcı olarak silmenizi sağlar.

Daha fazla bilgi için bkz. [geri yükleme süre aşımına uğradı veya silinmiş gruplar](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Çoklu oturum açma artık Azure AD SAML ile güvenli şirket içi uygulamalar için uygulama ara sunucusu aracılığıyla kullanılabilir (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** Access Control

Artık şirket içi, SAML kimliği doğrulanmış uygulamalar için çoklu oturum açma (SSO) deneyimi ve uygulama proxy 'Si aracılığıyla bu uygulamalara uzaktan erişim sağlayabilirsiniz. Şirket içi uygulamalarınızla SAML SSO 'yu ayarlama hakkında daha fazla bilgi için bkz. [uygulama proxy 'si (Önizleme) ile şirket içi uygulamalar Için SAML çoklu oturum açma](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>İstek döngülerine yönelik istemci uygulamaları, güvenilirliği ve Kullanıcı deneyimini geliştirmek için kesintiye uğrar

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

İstemci uygulamaları kısa bir süre boyunca aynı oturum açma isteklerini yanlışlıkla verebilir. Bu istekler, başarılı olsun veya etmeksizin, tüm kullanıcılar için zayıf bir kullanıcı deneyimine ve ıDP 'nin bu iş yüklerini artırarak, tüm kullanıcılar için gecikme süresini artırarak ve ıDP kullanılabilirliğini azalttığından.

Bu güncelleştirme bir `invalid_grant` hata gönderir: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` normal işlem kapsamının ötesinde, kısa bir süre içinde birden çok kez yinelenen istekler veren istemci uygulamalarına. Bu sorunla karşılaşan istemci uygulamalarında, kullanıcının yeniden oturum açmasını gerektiren bir etkileşimli istem gösterilmelidir. Bu değişiklik ve bu hatayla karşılaştığında uygulamanızı nasıl gidereceğiniz hakkında daha fazla bilgi için bkz. [kimlik doğrulaması yenilikleri nelerdir?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Yeni denetim günlükleri Kullanıcı deneyimi artık kullanılabilir

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Okunabilirliği ve bilgilerinizi aramanızı artırmaya yardımcı olmak için yeni bir Azure AD **Denetim günlükleri** sayfası oluşturduk. Yeni **Denetim günlükleri** sayfasını görmek IÇIN Azure AD 'nin **etkinlik** bölümündeki **Denetim günlükleri** ' ni seçin.

![Yeni denetim günlükleri sayfası, örnek bilgi](media/whats-new/audit-logs-page.png)

Yeni **Denetim günlükleri** sayfası hakkında daha fazla bilgi için, bkz. [Azure Active Directory portalındaki denetim etkinliği raporları](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Yanlış yapılandırılmış koşullu erişim ilkeleriyle yanlışlıkla yöneticinin kilitlenmesini önlemeye yardımcı olacak yeni uyarılar ve kılavuzluk

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği & koruması

Yöneticilerin, yanlış yapılandırılmış koşullu erişim ilkeleri aracılığıyla kendi kiracılarından kendilerini yanlışlıkla kilitlemesini önlemeye yardımcı olmak için Azure portal yeni uyarılar ve güncelleştirilmiş rehberlik oluşturduk. Yeni rehberlik hakkında daha fazla bilgi için bkz. [koşullu erişim Azure Active Directory hizmet bağımlılıkları nelerdir](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Mobil cihazlarda geliştirilmiş son kullanıcı kullanım koşulları deneyimi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** idare

Mevcut kullanım koşullarımızı, bir mobil cihazda kullanım koşullarını nasıl gözden geçitireceğiniz ve kabul ettiğiniz hakkında daha fazla yardımcı olacak şekilde güncelleştirdik. Artık yakınlaştırıp uzaklaştırabilir, geri dönüp bilgileri indirebilir ve köprüler ' i seçebilirsiniz. Güncelleştirilmiş kullanım koşulları hakkında daha fazla bilgi için bkz. [kullanım koşulları özelliği Azure Active Directory](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Yeni Azure AD etkinlik günlükleri indirme deneyimi kullanılabilir

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Artık Azure portal çok miktarda etkinlik günlüğünü doğrudan indirebilirsiniz. Bu güncelleştirme şunları yapmanızı sağlar:

- En fazla 250.000 satır indirin.

- İndirme tamamlandıktan sonra bildirim alın.

- Dosya adınızı özelleştirin.

- JSON veya CSV çıkış biçiminizi belirleme.

Bu özellik hakkında daha fazla bilgi için bkz [. hızlı başlangıç: Azure Portal kullanarak bir denetim raporu yükleme](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Son değişiklik: Exchange ActiveSync (EAS) tarafından koşul değerlendirmesine yönelik güncelleştirmeler

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** koşullu erişim **ürün yeteneği:** Access Control

Exchange ActiveSync 'in (EAS) aşağıdaki koşulları nasıl değerlendirdiği hakkında güncelleştirme sürecimiz vardır:

- Ülke, bölge veya IP adresine göre Kullanıcı konumu

- Oturum açma riski

- Cihaz platformu

Bu koşulları daha önce koşullu erişim ilkeleriniz üzerinde kullandıysanız, koşul davranışının değişebilir olduğunu unutmayın. Örneğin, daha önce bir ilkede Kullanıcı konumu koşulunu kullandıysanız, ilkeyi artık kullanıcının konumuna göre atlandığını görebilirsiniz.

---

## <a name="february-2019"></a>Şubat 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Yapılandırılabilir Azure AD SAML belirteci şifrelemesi (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

Artık, desteklenen herhangi bir SAML uygulamasını şifrelenmiş SAML belirteçleri alacak şekilde yapılandırabilirsiniz. Azure AD, bir uygulamayla yapılandırılıp kullanıldığında, Azure AD 'de depolanan bir sertifikadan alınan bir ortak anahtar kullanarak, verilmiş SAML onaylamalarını şifreler.

SAML belirteci şifrelemeyi yapılandırma hakkında daha fazla bilgi için bkz. [Azure AD SAML belirteci şifrelemesini yapılandırma](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleri kullanarak gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** erişim gözden geçirmeleri **ürün yeteneği:** idare

Artık grup üyeliği veya uygulama ataması için tek bir Azure AD erişim gözden geçirmesine birden çok grup veya uygulama ekleyebilirsiniz. Birden çok grup veya uygulama içeren erişim gözden geçirmeleri aynı ayarlarla ayarlanır ve tüm dahil olan gözden geçirenler aynı anda bildirilir.

Azure AD erişim gözden geçirmeleri kullanarak erişim incelemesi oluşturma hakkında daha fazla bilgi için bkz. [Azure AD erişim gözden geçirmeleriyle grup veya uygulama erişimi Incelemesi oluşturma](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Şubat 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Şubat 2019 ' de, uygulama galerisine federasyon desteği olan bu 27 yeni uygulamayı ekledik:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [Mindticyon](../saas-apps/mindtickle-tutorial.md), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [airstack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [iDrive](../saas-apps/idrive-tutorial.md), [ufuk ve qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [Parlatidea](../saas-apps/brightidea-tutorial.md), [alerla](../saas-apps/alertops-tutorial.md), [Soloinsight-cloudgate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md), izin tıklama, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [storegatesmartfile](../saas-apps/smartfile-tutorial.md), [Pexıp](../saas-apps/pexip-tutorial.md), [stormboard](../saas-apps/stormboard-tutorial.md), [seismik](../saas-apps/seismic-tutorial.md), [bir rüt](https://www.shareadream.org/how-it-works), [Bugsnag](../saas-apps/bugsnag-tutorial.md), [WebMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [her yerde LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU kampüs](../saas-apps/ou-campus-tutorial.md), [Periscope verileri](../saas-apps/periscope-data-tutorial.md), [NetOp Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [purecg, Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), İK [üretkenlik platformu](../saas-apps/clickup-productivity-platform-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Geliştirilmiş Birleşik MFA/SSPR kaydı

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** self servis parola sıfırlama **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Müşteri geri bildirimlerine yanıt olarak, birleştirilmiş MFA/SSPR kayıt önizleme deneyimini geliştirdik. böylece kullanıcılarınız hem MFA hem de SSPR için güvenlik bilgilerini daha hızlı kaydetmeye yardımcı olur.

**Kullanıcılarınızın gelişmiş deneyimini bugün açmak için şu adımları izleyin:**

1. Genel yönetici veya Kullanıcı Yöneticisi olarak Azure portal oturum açın ve **> Kullanıcı ayarları ' na Azure Active Directory gidin > erişim paneli Önizleme özellikleri ayarlarını yönetin**.

2. **Güvenlik bilgilerini kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilecek kullanıcılar – Yenile** seçeneği için **Seçili bir Kullanıcı grubu** veya **tüm kullanıcılar** için özellikleri açmayı seçin.

Önümüzdeki birkaç hafta içinde, zaten açık olmayan kiracılar için eski Birleşik MFA/SSPR kayıt önizleme deneyimini açma imkanını kaldıracağız.

**Kiracınız için denetimin kaldırılıp kaldırılmadığını görmek için şu adımları izleyin:**

1. Genel yönetici veya Kullanıcı Yöneticisi olarak Azure portal oturum açın ve **> Kullanıcı ayarları ' na Azure Active Directory gidin > erişim paneli Önizleme özellikleri ayarlarını yönetin**.

2. **Güvenlik bilgilerini kaydetmek ve yönetmek için Önizleme özelliklerini kullanabilecek kullanıcılar** **hiçbiri** olarak ayarlanırsa, bu seçenek kiracınızdan kaldırılır.

Daha önce, kullanıcılar için eski Birleşik MFA/SSPR kayıt önizleme deneyimini önceden açtığınızdan bağımsız olarak, eski deneyim gelecekteki bir tarihte kapatılacak. Bu nedenle, yeni ve gelişmiş deneyimle mümkün olan en kısa sürede geçmeniz önemle tavsiye ederiz.

Gelişmiş kayıt deneyimi hakkında daha fazla bilgi için bkz. [Azure AD BIRLEŞIK MFA ve parola sıfırlama kayıt deneyimi için seyrek geliştirmeler](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Kullanıcı akışları için ilke yönetimi deneyimi güncelleştirildi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Kullanıcı akışları (daha önce, yerleşik ilkeler) için ilke oluşturma ve yönetim işlemini daha kolay bir şekilde güncelleştirdik. Bu yeni deneyim artık tüm Azure AD kiracılarınız için varsayılandır.

Portal ekranının üst kısmındaki **bize geri bildirim gönder** alanındaki gülümseme veya kaş simgeleri kullanarak ek geri bildirim ve öneriler sağlayabilirsiniz.

Yeni ilke yönetimi deneyimi hakkında daha fazla bilgi için, [şimdi Azure AD B2C JavaScript özelleştirmesine ve birçok yeni özellik](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) bloguna sahiptir.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Azure AD B2C tarafından sunulan belirli sayfa öğesi sürümlerini seçin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Artık, Azure AD B2C tarafından sunulan sayfa öğelerinin belirli bir sürümünü seçebilirsiniz. Belirli bir sürümü seçerek, güncelleştirmelerinizi bir sayfada görüntülenmeden önce test edebilir ve öngörülebilir bir davranış sağlayabilirsiniz. Ayrıca, artık JavaScript özelleştirmeleriyle ilgili belirli sayfa sürümlerini zorunlu kılabilirsiniz. Bu özelliği açmak için Kullanıcı akışlarınızda **Özellikler** sayfasına gidin.

Sayfa öğelerinin belirli sürümlerini seçme hakkında daha fazla bilgi için, [artık Azure AD B2C JavaScript özelleştirmesine ve birçok yeni özellik](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) bloguna sahiptir.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C için yapılandırılabilir Son Kullanıcı parolası gereksinimleri (GA)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Artık kuruluşunuzun parola karmaşıklığını, yerel Azure AD parola ilkenizi kullanmak yerine son kullanıcılarınız için ayarlayabilirsiniz. Kullanıcı akışlarınızın **Özellikler** dikey penceresinde (önceki adıyla yerleşik ilkeleriniz), **basit** veya **güçlü** bir parola karmaşıklığı seçebilir veya **özel** bir gereksinim kümesi oluşturabilirsiniz.

Parola karmaşıklığı gereksinim yapılandırması hakkında daha fazla bilgi için bkz. [Azure Active Directory B2C parolalar için karmaşıklık gereksinimlerini yapılandırma](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Özel markalı kimlik doğrulama deneyimleri için yeni varsayılan şablonlar

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Kullanıcılarınız için özel bir markalı kimlik doğrulama deneyimi oluşturmak üzere Kullanıcı akışlarınızın **sayfa düzenleri** dikey penceresinde (daha önce yerleşik ilkeler olarak bilinir) bulunan yeni varsayılan şablonlarımızı kullanabilirsiniz.

Şablonları kullanma hakkında daha fazla bilgi için bkz. [Azure AD B2C artık JavaScript özelleştirmesi ve birçok yeni özellik içeriyor](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Ocak 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Tek seferlik geçiş kodu kimlik doğrulaması kullanarak B2B işbirliği Active Directory (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2B **ürün yeteneği:** B2B/B2C

Azure AD, Microsoft hesabı (MSA) veya Google Federasyonu gibi diğer yollarla kimlik doğrulamasından geçmediğiniz B2B Konuk kullanıcıları için bir kerelik geçiş kodu kimlik doğrulaması (OTP) sunuyoruz. Bu yeni kimlik doğrulama yöntemi, konuk kullanıcıların yeni bir Microsoft hesabı oluşturması gerekmediği anlamına gelir. Bunun yerine, bir daveti benimseme veya paylaşılan bir kaynağa erişirken, Konuk Kullanıcı bir e-posta adresine gönderilmek üzere geçici bir kod isteyebilir. Bu geçici kodu kullanarak Konuk Kullanıcı oturum açmaya devam edebilir.

Daha fazla bilgi için bkz. [e-posta bir kerelik geçiş kodu kimlik doğrulaması (Önizleme)](../external-identities/one-time-passcode.md) ve blogda [Azure AD, herhangi bir hesabı kullanarak tüm kullanıcılar için paylaşım ve işbirliği yapma olanağı sunar](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Yeni Azure AD Uygulama Ara Sunucusu tanımlama bilgisi ayarları

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** Access Control

Uygulama proxy 'Si aracılığıyla yayınlanan uygulamalarınız için kullanılabilen üç yeni tanımlama bilgisi ayarı ekledik:

- **HTTP-Only tanımlama bilgisini kullanın.** Uygulama proxy 'Si erişiminizin ve oturum tanımlama bilgilerinizdeki **HttpOnly** bayrağını ayarlar. Bu ayarı açmak, istemci tarafı komut dosyası aracılığıyla tanımlama bilgilerinin kopyalanmasını veya değiştirilmesini önlemeye yardımcı olmak gibi ek güvenlik avantajları sağlar. Bu bayrağı açmanızı öneririz ( **Evet**' i seçin).

- **Güvenli tanımlama bilgisini kullanın.** Uygulama proxy 'Si erişiminizin ve oturum tanımlama bilgilerinde **güvenli** bayrağını ayarlar. Bu ayarı açmak, tanımlama bilgilerinin yalnızca HTTPS gibi TLS güvenli kanallarından aktarılmasını sağlamak için ek güvenlik avantajları sağlar. Bu bayrağı açmanızı öneririz ( **Evet**' i seçin).

- **Kalıcı tanımlama bilgisini kullanın.** Web tarayıcısı kapalıyken erişim tanımlama bilgilerinin süresinin dolmasını önler. Bu tanımlama bilgileri, erişim belirtecinin ömrü için en son. Ancak, süre sonu zamanına ulaşıldığında veya Kullanıcı tanımlama bilgisini el ile silerse tanımlama bilgileri sıfırlanır. Varsayılan ayar olarak, yalnızca süreçler arasında tanımlama bilgilerini paylaşmayan eski uygulamalar **için ayarı açarak** yapmanız önerilir.

Yeni tanımlama bilgileri hakkında daha fazla bilgi için bkz. [Azure Active Directory ' de şirket içi uygulamalara erişmek Için tanımlama bilgisi ayarları](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Ocak 2019

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

2019 Ocak 'ta, uygulama galerisine federasyon desteğiyle bu 35 yeni uygulamalar ekledik:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Fize](../saas-apps/folloze-tutorial.md), [Tatatci paleti](../saas-apps/talent-palette-tutorial.md),, [cloudsuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco şemsiye](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Erişim Yöneticisi](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [süre sonu anımsatıcısı](../saas-apps/expiration-reminder-tutorial.md), [EVR Görüntüleyicisi](../saas-apps/instavr-viewer-tutorial.md), [Corptax](../saas-apps/corptax-tutorial.md), [Verb](https://app.verb.net/login), [OPENLATLET](https://openlattice.com/agora), [theorgwiki](https://www.theorgwiki.com/signup), [pavaso DIJITAL kapatma](../saas-apps/pavaso-digital-close-tutorial.md), [Goodalıştırma araç seti](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service Picco](../saas-apps/cloud-service-picco-tutorial.md), [denetimpanosu](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [workable](../saas-apps/workable-tutorial.md), [Callbir](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO sistemi](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE serviceınsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [coralogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [Ares](../saas-apps/ares-for-enterprise-tutorial.md), [for Enterprise, for Office 365](https://www.k2.com/O365), [xledger](https://www.xledger.net/), [IDID Manager](../saas-apps/idid-manager-tutorial.md), [highdişli](../saas-apps/highgear-tutorial.md), [K2](../saas-apps/visitly-tutorial.md), [Korn Fraz alp](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [Visitly csaas platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Yeni Azure AD Kimlik Koruması geliştirmeleri (Genel Önizleme)

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Aşağıda aşağıdakiler dahil olmak üzere Azure AD Kimlik Koruması genel önizleme sunumuna aşağıdaki geliştirmeleri eklediğimiz duyurumuz ediyoruz.

- Güncelleştirilmiş ve daha tümleşik bir kullanıcı arabirimi

- Ek API’ler

- Machine Learning ile iyileştirilmiş risk değerlendirmesi

- Riskli kullanıcılar ve riskli oturum açma işlemleri arasında ürün genelinde hizalama

Geliştirmeler hakkında daha fazla bilgi için bkz. [ne Azure Active Directory kimlik koruması (YENİLENDİ)?](../identity-protection/overview-identity-protection.md) daha fazla bilgi edinmek ve fikirlerinizi ürün içi istemler aracılığıyla paylaşmak için.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>İOS ve Android cihazlarda Microsoft Authenticator uygulaması için yeni uygulama kilidi özelliği

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Microsoft Authenticator uygulama **ürün yeteneği:** kimlik güvenliği & koruması

Tek seferlik geçiş kodlarınızın, uygulama bilgilerinizin ve uygulama ayarlarının daha güvenli olmasını sağlamak için Microsoft Authenticator uygulamasındaki uygulama kilidi özelliğini açabilirsiniz. Uygulama kilidi 'ni açmak, Microsoft Authenticator uygulamasını her açışınızda PIN 'inizi veya biyometri kullanarak kimlik doğrulaması yapmanız istenecektir.

Daha fazla bilgi için bkz. [uygulama sss Microsoft Authenticator](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Gelişmiş Azure AD Privileged Identity Management (PıM) dışa aktarma özellikleri

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Privileged Identity Management **ürün özelliği:** Privileged Identity Management

Privileged Identity Management (PıM) yöneticileri artık tüm alt kaynaklar için rol atamaları içeren belirli bir kaynak için tüm etkin ve uygun rol atamalarını dışarı aktarabilir. Daha önce, yöneticilerin bir abonelik için rol atamalarının tamamen bir listesini alması ve her belirli kaynak için rol atamalarını dışarı aktarmaları gerekiyordu.

Daha fazla bilgi için bkz. [PIM 'de Azure Kaynak rolleri için etkinliği ve denetim geçmişini görüntüleme](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>Kasım/Aralık 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Eşitleme kapsamından kaldırılan kullanıcılar artık yalnızca bulut hesaplarına geçmesiz

**Şunu yazın:** Sabit **hizmet kategorisi:** Kullanıcı Yönetimi **ürün yeteneği:** Dizin

>[!Important]
>Bu çözüm nedeniyle rahatsız etmemizi duyduk ve anladık. Bu nedenle, bu değişikliği kuruluşunuzda uygulamanızı daha kolay hale getirmek için bu süre kadar geri döndürüyoruz.

Active Directory Domain Services (AD DS) nesnesi eşitleme kapsamından dışlandıysa ve sonra aşağıdaki eşitleme döngüsündeki Azure AD 'ye geri dönüşüm kutusu 'na taşındıktan sonra bir kullanıcının DirSyncEnabled bayrağının **hatalı olarak yanlış** olarak geçtiğimiz bir hata düzeltildi. Bu düzeltmeyle ilgili olarak, Kullanıcı eşitleme kapsamından dışlandığından ve daha sonra Azure AD geri dönüşüm kutusu 'ndan geri yüklenirse, Kullanıcı hesabı şirket içi AD 'den eşitlenmiş olarak kalır ve bu nedenle, yetki kaynağı (SoA) Şirket içi AD olarak kaldığı için bulutta yönetilemez.

Bu düzeltmeyle önce, DirSyncEnabled bayrağı false olarak geçildiğinde bir sorun oluştu. Bu hesaplar, bu hesapların yalnızca bulut nesnelerine dönüştürüldüğüne ve hesapların bulutta yönetilebileceğinden yanlış izlenimi verdi. Ancak, hesaplar hala SoA 'yi şirket içinde ve şirket içi AD 'den gelen tüm eşitlenmiş Özellikler (gölge öznitelikleri) olarak bekletilir. Bu durum, Azure AD 'de ve diğer bulut iş yüklerinde (Exchange Online gibi), bu hesapların AD 'den eşitlenmiş olduğunu ancak artık yalnızca bulut hesapları gibi davranmasının beklendiği bir şekilde daha fazla soruna neden oldu.

Şu anda, eşitlenmiş bir AD hesabını gerçekten yalnızca bulut hesabına dönüştürmenin tek yolu, bir arka uç işlemini SoA aktarmaya tetikleyen kiracı düzeyinde DirSync 'i devre dışı bırakmalıdır. Bu tür SoA değişikliği, şirket içi ilgili tüm öznitelikleri (LastDirSyncTime ve Shadow öznitelikleri gibi) temizlemek ve ilgili nesnenin kendisini yalnızca bulutta bir hesaba dönüştürmek üzere diğer bulut iş yüklerine bir sinyal göndermek için gereklidir (ancak bunlarla sınırlı değildir).

Bu, sonuçta, geçmişteki bazı senaryolarda, AD 'den eşitlenen bir kullanıcının ImmutableID özniteliğinde doğrudan güncelleştirmelerin yapılmasını önler. Tasarım yaparak, adın gösterdiği gibi Azure AD 'deki bir nesnenin ImmutableID, sabit olması anlamına gelir. Azure AD Connect Health ve Azure AD Connect eşitleme istemcisinde uygulanan yeni özellikler, bu senaryolara yönelik olarak kullanılabilir:

- **Aşamalı bir yaklaşımda çok sayıda kullanıcı için büyük ölçekli ImmutableID güncelleştirmesi**

  Örneğin, uzun AD DS ormanlar arası geçiş yapmanız gerekir. Çözüm: **kaynak bağlantısını yapılandırmak** Için Azure AD Connect kullanın ve Kullanıcı geçirildiğinde, mevcut ImmutableID değerlerini Azure AD DS ad 'den yeni ormanın ms-DS-tutarlılık-GUID özniteliğine kopyalayın. Daha fazla bilgi için bkz. [MS-DS-ımıbu GUID 'ı Sourcetutturucu olarak kullanma](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Tek bir görüntüsündeki birçok kullanıcı için büyük ölçekli ImmutableID güncelleştirmeleri**

  Örneğin, Azure AD Connect uygulanırken bir hata yaparsınız ve şimdi Sourcetutturucu özniteliğini değiştirmeniz gerekir. Çözüm: kiracı düzeyinde DirSync 'i devre dışı bırakın ve tüm geçersiz ImmutableID değerlerini temizleyin. Daha fazla bilgi için bkz. [Office 365 için dizin eşitlemeyi devre dışı bırakma](/office365/enterprise/turn-off-directory-synchronization).

- **Azure AD 'de mevcut bir kullanıcıyla şirket içi Kullanıcı Ile yeniden eşleştir** Örneğin, AD DS ' de yeniden oluşturulmuş bir kullanıcı var olan bir Azure AD hesabıyla (yalnız bırakılmış nesne) yeniden eşleştirmek yerine Azure AD hesabında yinelenen bir öğe oluşturur. Çözüm: kaynak bağlayıcısını/ImmutableID yeniden eşlemek için Azure portal Azure AD Connect Health kullanın. Daha fazla bilgi için bkz. [yalnız bırakılmış nesne senaryosu](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Son değişiklik: Azure Izleyici aracılığıyla denetim ve oturum açma günlüğü şemasında yapılan güncelleştirmeler

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure Izleyici aracılığıyla hem denetim hem de oturum açma günlüğü akışlarını yayımlıyoruz, böylece günlük dosyalarını SıEM araçlarınızla veya Log Analytics ile sorunsuzca tümleştirebilirsiniz. Geri bildirimlerinize göre ve bu özelliğin genel kullanılabilirlik duyurusunu hazırlıyoruz, şemanızda aşağıdaki değişiklikleri yapıyoruz. Bu şema değişiklikleri ve ilgili belge güncelleştirmeleri, Ocak 'un ilk haftası tarafından gerçekleşir.

#### <a name="new-fields-in-the-audit-schema"></a>Denetim şemasındaki yeni alanlar
Kaynak üzerinde gerçekleştirilen işlem türünü sağlamak için yeni bir **Işlem türü** alanı ekliyoruz. Örneğin, **ekleme**, **güncelleştirme** veya **silme**.

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
|appliedConditionalAccessPolicies|Bu **Conditionalaccesspolicies** alanıdır. Artık **appliedConditionalAccessPolicies** alanıdır.|düzeltme sınıfı,|düzeltme sınıfı,|
|conditionalAccessStatus|Oturum açma sırasında koşullu erişim Ilkesi durumunun sonucunu sağlar. Bu, daha önce numaralandırılmıştı, ancak şimdi gerçek değeri gösteriyoruz.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Başarılı</li><li>Hata</li><li>Uygulanmadı</li><li>Devre dışı</li></ul>|
|appliedConditionalAccessPolicies: sonuç|Oturum açma sırasında bireysel koşullu erişim Ilkesi durumunun sonucunu sağlar. Bu, daha önce numaralandırılmıştı, ancak şimdi gerçek değeri gösteriyoruz.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Başarılı</li><li>Hata</li><li>Uygulanmadı</li><li>Devre dışı</li></ul>|

Şema hakkında daha fazla bilgi için bkz. [Azure izleyici 'de Azure AD denetim günlükleri şemasını yorumlama (Önizleme)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Denetimli makine öğrenimi modeli ve risk puanı altyapısında kimlik koruması geliştirmeleri

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** risk puanları

Kimlik koruması ile ilgili Kullanıcı ve oturum açma risk değerlendirmesi altyapısı geliştirmeleri, Kullanıcı risk doğruluğu ve kapsamını geliştirmeye yardımcı olabilir. Yöneticiler, Kullanıcı risk düzeyinin artık belirli algılamaların risk düzeyine doğrudan bağlı olmadığını ve riskli oturum açma olaylarının sayısı ve düzeyinde bir artış olduğunu fark edebilir.

Risk algılamaları artık kullanıcının oturum açma işlemlerinin ek özelliklerini ve bir algılama düzenlerini kullanarak Kullanıcı riskini hesaplayan, denetimli makine öğrenimi modeli tarafından değerlendirilir. Yönetici, bu modele bağlı olarak, bu kullanıcıyla ilişkili algılamalar düşük veya orta riskli olsa bile, yüksek riskli puanları olan kullanıcılar tarafından bulunabilir.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Yöneticiler, Microsoft Authenticator uygulamasını kullanarak kendi parolalarını sıfırlayabilir (Genel Önizleme)

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** self servis parola sıfırlama **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD yöneticileri artık Microsoft Authenticator uygulama bildirimlerini veya herhangi bir mobil kimlik doğrulayıcı uygulamasının veya donanım belirtecinden bir kodu kullanarak kendi parolasını sıfırlayabilir. Yöneticiler, kendi parolasını sıfırlamak için şu yöntemlerden ikisini de kullanabilir:

- Microsoft Authenticator uygulama bildirimi

- Diğer mobil kimlik doğrulayıcı uygulaması/donanım belirteci kodu

- E-posta

- Telefon görüşmesi

- Kısa mesaj

Parolaları sıfırlamak için Microsoft Authenticator uygulamasını kullanma hakkında daha fazla bilgi için bkz. [Azure AD self servis parola sıfırlama-mobil uygulama ve SSPR (Önizleme)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Yeni Azure AD bulut aygıtı yönetici rolü (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** cihaz kaydı ve Yönetim **ürün yeteneği:** erişim denetimi

Yöneticiler, bulut aygıtı yönetici görevlerini gerçekleştirmek için kullanıcıları yeni bulut cihaz yöneticisi rolüne atayabilir. Bulut cihaz yöneticileri rolüne atanan kullanıcılar, Azure AD 'de cihazları etkinleştirebilir, devre dışı bırakabilir ve silebilir ve Azure portal Windows 10 BitLocker anahtarlarını (varsa) okuyabilir.

Roller ve izinler hakkında daha fazla bilgi için bkz. [Azure Active Directory yönetici rolleri atama](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Azure AD 'de yeni etkinlik zaman damgasını kullanarak cihazlarınızı yönetin (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** cihaz kaydı ve Yönetim **ürün yeteneği:** cihaz yaşam döngüsü yönetimi

Ortamınızda eski cihazları kullanmaktan kaçınmak için, Azure AD 'de kuruluşların cihazlarını yenilemeniz ve devre dışı bırakmanız gerektiğini fark ettik. Azure AD, bu işleme yardımcı olmak için artık cihazlarınızı yeni bir etkinlik zaman damgasıyla güncelleştirir ve cihaz yaşam döngüsünü yönetmenize yardımcı olur.

Bu zaman damgasını alma ve kullanma hakkında daha fazla bilgi için bkz [. nasıl yapılır: Azure AD 'de eski cihazları yönetme](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Yöneticiler, kullanıcıların her cihazda kullanım koşullarını kabul etmesini gerektirebilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** idare

Yöneticiler artık kullanıcılarınızın kiracınızda kullandıkları her cihazda kullanım koşullarınızı kabul etmesini gerektirmek için **kullanıcıların her cihazda izin vermesini iste** seçeneğini açabilir.

Daha fazla bilgi için [Azure Active Directory kullanım koşulları özelliğinin cihaz başına kullanım koşulları bölümüne](../conditional-access/terms-of-use.md#per-device-terms-of-use)bakın.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Yöneticiler, yinelenen bir zamanlamaya göre kullanım koşulları 'nı zaman dolacak şekilde yapılandırabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** idare


Yöneticiler artık, belirtilen yinelenen zamanlamanıza bağlı olarak tüm kullanıcılarınız için kullanım süresini dolacak şekilde **süre sonu onayları** seçeneğini açabilir. Zamanlama yıllık, iki yıllık, üç aylık veya aylık olabilir. Kullanım koşulları sona erdiğinde, kullanıcıların yeniden kabul etmesi gerekir.

Daha fazla bilgi için [Azure Active Directory kullanım koşulları özelliğinin kullanım koşulları ekleme bölümüne](../conditional-access/terms-of-use.md#add-terms-of-use)bakın.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Yöneticiler, her bir kullanıcının zamanlamasını temel alarak kullanım koşullarını yapılandırabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** idare

Yöneticiler artık kullanıcının kullanım koşullarını yeniden kabul etmesi için bir süre belirtebilir. Örneğin, Yöneticiler kullanıcıların her 90 günde bir kullanım koşulları 'nı yeniden kabul etmesi gerektiğini belirtebilir.

Daha fazla bilgi için [Azure Active Directory kullanım koşulları özelliğinin kullanım koşulları ekleme bölümüne](../conditional-access/terms-of-use.md#add-terms-of-use)bakın.

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory rolleri için yeni Azure AD Privileged Identity Management (PıM) e-postaları

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Privileged Identity Management **ürün özelliği:** Privileged Identity Management

Azure AD Privileged Identity Management (PıM) kullanan müşteriler artık son yedi güne ait aşağıdaki bilgiler dahil olmak üzere haftalık bir Özet e-postası alabilir:

- En uygun ve kalıcı rol atamalarına genel bakış

- Rolleri etkinleştiren kullanıcı sayısı

- PıM 'de rollere atanan kullanıcı sayısı

- PıM dışında rollere atanan kullanıcı sayısı

- PıM 'de "kalıcı hale getirilir" Kullanıcı sayısı

PıM ve kullanılabilir e-posta bildirimleri hakkında daha fazla bilgi için bkz. [PIM 'de e-posta bildirimleri](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Grup tabanlı lisanslama genel kullanıma sunuldu

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** Dizin

Grup tabanlı lisanslama genel önizleme aşamasındadır ve genel kullanıma sunulmuştur. Bu genel yayının bir parçası olarak, bu özelliği daha ölçeklenebilir hale aldık ve tek bir kullanıcı için grup tabanlı lisanslama atamalarını yeniden işleme özelliği ve Office 365 E3/a3 lisanslarıyla grup tabanlı lisanslama kullanma özelliği ekledik.

Grup tabanlı lisanslama hakkında daha fazla bilgi için bkz. [Azure Active Directory grup tabanlı lisanslama nedir?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Kasım 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

2018 Kasım 'da, uygulama galerisine federasyon desteğiyle bu 26 yeni uygulama ekledik:

[Corestack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [Getno](../saas-apps/getthere-tutorial.md), [GRA-PE](../saas-apps/grape-tutorial.md), [ehour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [appınux](../saas-apps/appinux-tutorial.md), [drivedolar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [useall](../saas-apps/useall-tutorial.md), [sonsuz kampüs](../saas-apps/infinitecampus-tutorial.md), [alaya](https://alayagood.com), [heyıarkadaş](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [drift](../saas-apps/drift-tutorial.md), [zenegy for Business Central 365](https://accounting.zenegy.com/), [yaprak Köprüsü üye portalı](../saas-apps/everbridge-tutorial.md), [IDEO](https://profile.ideo.com/users/sign_up), [ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [allbağlanacak SSO](../saas-apps/allbound-sso-tutorial.md), [parçalı uygulamalar-klasik test](https://test.plexonline.com/signon), [parçalı uygulamalar](https://www.plexonline.com/signon)– bir, parçalı uygulamalar [-UX testi](https://test.cloud.plex.com/sso), [parçalı uygulamalar – UX](https://cloud.plex.com/sso), [parçalı uygulamalar – IAM](https://accounts.plex.com/), [öğelerini-childilgilenme kayıtları, katılım, & finansal izleme sistemi](https://getcrafts.ca/craftsregistration)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

## <a name="october-2018"></a>Ekim 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD günlükleri artık Azure Log Analytics ile çalışıyor (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure AD günlüklerinizi artık Azure Log Analytics ilettiğini duyurmaktan mutluluk duyuyoruz! Bu en iyi istenen özellik, iş, işlemler ve güvenlik için Analize daha iyi erişim sağlamanıza yardımcı olur ve altyapınızı izlemeye yardımcı olmak için bir yoldur. Daha fazla bilgi için bkz. [Azure 'Daki etkinlik günlüklerine Azure Active Directory artık blog Log Analytics](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Ekim 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

2018 Ekim 'de, uygulama galerisine federasyon desteği olan bu 14 yeni uygulamaları ekledik:

[Ikramiye noktalarım](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), Ambyint, [myworkdrive](../saas-apps/myworkdrive-tutorial.md), [ödünç kutusu](../saas-apps/borrowbox-tutorial.md), dialpad, [ON24 sanal ortamı](../saas-apps/on24-tutorial.md), [halka orta](../saas-apps/ringcentral-tutorial.md), [Zscaler üç](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [workspot denetimi](../saas-apps/workspotcontrol-tutorial.md), [shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [glassfrog](../saas-apps/glassfrog-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-posta bildirimlerini Azure AD Domain Services

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD Domain Services **ürün özelliği:** Azure AD Domain Services

Azure AD Domain Services, yanlış yapılandırma veya yönetilen etki alanı ile ilgili sorunlar hakkında Azure portal uyarılar sağlar. Bu uyarılar, destek ile iletişime geçerek sorunları gidermeyi deneyebilmeniz için adım adım kılavuzlar içerir.

Ekim 'den başlayarak, yönetilen etki alanınız için bildirim ayarlarını özelleştirerek yeni uyarılar gerçekleştiğinde, belirlenen bir kişi grubuna e-posta gönderilir ve portalda güncelleştirmeler için sürekli olarak denetlenmesi gereğini ortadan kaldırır.

Daha fazla bilgi için [Azure AD Domain Services bildirim ayarları](../../active-directory-domain-services/notifications.md)bölümüne bakın.

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD Portal, özel etki alanlarını silmek için ForceDelete etki alanı API 'sini kullanmayı destekler

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Dizin Yönetimi **ürün yeteneği:** Dizin

Özel etki alanı adınızdan (contoso.com) bulunan kullanıcılar, gruplar ve uygulamalar gibi başvuruları, ilk varsayılan etki alanı adına (contoso.onmicrosoft.com) geri yükleyerek özel etki alanı adlarınızı silmek için artık ForceDelete etki alanı API 'sini kullanabilirsiniz.

Bu değişiklik, kuruluşunuz artık adı kullanmadıysa veya başka bir Azure AD ile etki alanı adını kullanmanız gerekiyorsa, özel etki alanı adlarınızı daha hızlı bir şekilde silmenizi sağlar.

Daha fazla bilgi için bkz. [özel etki alanı adını silme](../enterprise-users/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Eylül 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Dinamik Gruplar için yönetici rolü izinleri güncelleştirildi

**Şunu yazın:** Sabit **hizmet kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Belirli Yönetici rollerinin artık grubun sahibi olması gerekmeden dinamik üyelik kuralları oluşturup güncelleştirebilmesi için bir sorunu düzelttik.

Roller şunlardır:

- Genel yönetici

- Intune yöneticisi

- Kullanıcı yöneticisi

Daha fazla bilgi için bkz. [dinamik grup oluşturma ve durumu denetleme](../enterprise-users/groups-create-rule.md)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Bazı üçüncü taraf uygulamalar için Basitleştirilmiş tek Sign-On (SSO) yapılandırma ayarları

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

Hizmet olarak yazılım (SaaS) uygulamaları için tek Sign-On (SSO) ayarlamanın, her bir uygulama yapılandırmasının benzersiz doğası nedeniyle zorlayıcı bir şekilde yapabileceğini fark ettik. Aşağıdaki üçüncü taraf SaaS uygulamalarına yönelik SSO yapılandırma ayarlarını otomatik olarak doldurmak için basitleştirilmiş bir yapılandırma deneyimi geliştirdik:

- Zendesk

- Çevrimiçi olan ArcGIS

- Jamf Pro

Bu tek tıklamayla deneyim 'i kullanmaya başlamak için, uygulamanın **Azure Portal**  >  **SSO yapılandırma** sayfasına gidin. Daha fazla bilgi için bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory-verileriniz nerede bulunuyor? sayfasında

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** altın ocal

Tüm Azure AD Hizmetleri için Azure AD verilerinizi bekleyen Azure veri merkezini görüntülemek için **Azure Active Directory** şirketinizin bölgesini seçin. Bilgileri şirketinizin bölgesi için belirli Azure AD hizmetlerine göre filtreleyebilirsiniz.

Bu özelliğe erişmek ve daha fazla bilgi için, bkz. [Azure Active Directory-verileriniz nerede bulunur](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Uygulamalarım erişim paneli için yeni dağıtım planı kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulamalarım **ürün yeteneği:** SSO

Uygulamalarım erişim paneli için kullanılabilen yeni dağıtım planına göz atın ( https://aka.ms/deploymentplans) .
Uygulamalarım erişim paneli kullanıcılara uygulamaları bulmak ve bunlara erişmek için tek bir yer sağlar. Bu portal Ayrıca kullanıcılara, uygulamalar ve gruplar için erişim isteme ya da diğer kişilerin adına bu kaynaklara erişimi yönetme gibi self servis fırsatları sağlar.

Daha fazla bilgi için bkz. [uygulamalarım portalı nedir?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure portal oturum açma günlükleri sayfasında yeni sorun giderme ve destek sekmesi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure portal **oturum açma** Işlemlerinin yeni **sorun giderme ve destek** sekmesi, yöneticilerin ve Destek mühendislerinin Azure AD oturum açma konularıyla ilgili sorunları gidermelerine yardımcı olmak için tasarlanmıştır. Bu yeni sekme, sorunu çözmeye yardımcı olmak için hata kodu, hata iletisi ve düzeltme önerilerini (varsa) sağlar. Sorunu çözemezse, bir destek bileti **oluşturmak için,** destek biletinizdeki günlük dosyası IÇIN **Istek kimliği** ve **Tarih (UTC)** alanlarını dolduran bir destek bileti oluşturmaya yönelik yeni bir yol da sunuyoruz.

![Yeni sekmeyi gösteren oturum açma günlükleri](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Dinamik üyelik kuralları oluşturmak için kullanılan özel uzantı özellikleri için gelişmiş destek

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Bu güncelleştirmeyle, artık dinamik Kullanıcı grubu kuralı oluşturucusunun **özel uzantı özellikleri al** bağlantısına tıklayabilir, BENZERSIZ uygulama kimliğinizi girebilir ve kullanıcılar için dinamik üyelik kuralı oluştururken kullanmak üzere özel uzantı özelliklerinin tam listesini alabilirsiniz. Bu liste ayrıca, bu uygulama için yeni özel uzantı özellikleri almak üzere yenilenebilir.

Dinamik üyelik kuralları için özel uzantı özellikleri kullanma hakkında daha fazla bilgi için bkz. [uzantı özellikleri ve özel uzantı özellikleri](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim için yeni onaylanan istemci uygulamaları

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** koşullu erişim **ürün yeteneği:** kimlik güvenliği ve koruma

Aşağıdaki uygulamalar [onaylanan istemci uygulamaları](../conditional-access/concept-conditional-access-conditions.md#client-apps)listesidir:

- Microsoft To-Do

- Microsoft Stream

Daha fazla bilgi için bkz.

- [Azure AD uygulama tabanlı koşullu erişim](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 kilit ekranından Self-Service parola sıfırlama için yeni destek

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** SSPR **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Bu yeni özelliği ayarladıktan sonra kullanıcılarınız, Windows 7, Windows 8 veya Windows 8.1 çalıştıran bir cihazın **kilit** ekranından parolalarını sıfırlama bağlantısını görür. Bu bağlantıya tıkladığınızda, Kullanıcı Web tarayıcısı aracılığıyla aynı parola sıfırlama akışında gezinerek yapılır.

Daha fazla bilgi için bkz. [Windows 7, 8 ve 8,1 ' de parola sıfırlamayı etkinleştirme](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Değişiklik bildirimi: yetkilendirme kodları artık yeniden kullanım için kullanılamayacak

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** kimlik doğrulamaları (oturum açma) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

15 Kasım 2018 tarihinden itibaren Azure AD, uygulamalar için önceden kullanılan kimlik doğrulama kodlarını kabul etmeyi durduracak. Bu güvenlik değişikliği, Azure AD 'yi OAuth belirtimine göre satıra getirmeye yardımcı olur ve hem v1 hem de v2 uç noktalarında zorunlu kılınır.

Uygulamanız birden fazla kaynağa belirteç almak için yetkilendirme kodlarını yeniden kullanıyorsa, bir yenileme belirteci almak için kodu kullanmanızı ve ardından bu yenileme belirtecini kullanarak diğer kaynaklar için ek belirteçler elde etmenizi öneririz. Yetkilendirme kodları yalnızca bir kez kullanılabilir, ancak yenileme belirteçleri birden çok kaynak arasında birden çok kez kullanılabilir. OAuth kod akışı sırasında bir kimlik doğrulama kodunu yeniden kullanmayı deneyen bir uygulama invalid_grant bir hata alır.

Bu ve diğer protokollerle ilgili değişiklikler için, [kimlik doğrulaması yenilikleri hakkında tam listeye](../develop/reference-breaking-changes.md)bakın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Eylül 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Eylül 2018 ' de, uygulama galerisine federasyon desteğiyle bu 16 yeni uygulamayı ekledik:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Cokarşılayan işe alma yazılımı](../saas-apps/comeetrecruitingsoftware-tutorial.md), [workteam](../saas-apps/workteam-tutorial.md), ArcGIS [Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [nuclino](../saas-apps/nuclino-tutorial.md), [jda Cloud](../saas-apps/jdacloud-tutorial.md), [kar ke](../saas-apps/snowflake-tutorial.md), NavigoCloud, [figma](../saas-apps/figma-tutorial.md), Join.me, [Zephyrsso](../saas-apps/zephyrsso-tutorial.md), [SILVERBACK](../saas-apps/silverback-tutorial.md), riveryatak Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Azure için enlyft SSO, [Palatino,](../saas-apps/convene-tutorial.md) [dmarcıa](../saas-apps/dmarcian-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="support-for-additional-claims-transformations-methods"></a>Ek talep dönüştürmeleri yöntemleri için destek

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

SAML tabanlı **tek Sign-On yapılandırma** sayfasından SAML belirteçlerine uygulanabilen yeni talep dönüştürme yöntemleri ToLower () ve ToUpper () sunuyoruz.

Daha fazla bilgi için bkz [. Azure AD 'de kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>SAML tabanlı uygulama yapılandırma kullanıcı arabirimi (Önizleme) güncelleştirildi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

Güncelleştirilmiş SAML tabanlı uygulama yapılandırma Kullanıcı arabirimimizin bir parçası olarak şunları alacaksınız:

- SAML tabanlı uygulamalarınızı yapılandırmaya yönelik güncelleştirilmiş bir gözden geçirme deneyimi.

- Yapılandırmanızda nelerin eksik olduğu veya yanlış olduğu hakkında daha fazla görünürlük.

- Süre sonu sertifikası bildirimi için birden çok e-posta adresi ekleme özelliği.

- Yeni talep dönüştürme yöntemleri, ToLower () ve ToUpper () ve daha fazlası.

- Kurumsal uygulamalarınız için kendi belirteç imzalama sertifikanızı karşıya yüklemenin bir yolu.

- SAML uygulamaları için NameID biçimini ayarlamak için bir yol ve NameID değerini Dizin uzantıları olarak ayarlamak için bir yol.

Bu güncelleştirilmiş görünümü açmak için **Çoklu oturum açma** sayfasının en üstündeki **Yeni deneyimimizi deneyin** bağlantısına tıklayın. Daha fazla bilgi için bkz. [öğretici: Azure Active Directory olan bir uygulama IÇIN SAML tabanlı çoklu oturum açmayı yapılandırma](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Ağustos 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory IP adresi aralıklarına yapılan değişiklikler

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** diğer **ürün yeteneği:** platform

Azure AD 'ye daha büyük IP aralıkları sunuyoruz, yani güvenlik duvarları, yönlendiricileriniz veya ağ güvenlik grupları için Azure AD IP adresi aralıklarını yapılandırdıysanız, bunları güncelleştirmeniz gerekir. Bu güncelleştirmeyi yaptığımız için, Azure AD yeni uç noktalar eklediğinde güvenlik duvarı, yönlendirici veya ağ güvenlik grupları IP aralığı yapılandırmalarının yeniden değiştirilmesini istemezsiniz.

Ağ trafiği, sonraki iki ay içinde bu yeni aralığa taşınıyor. Kesintisiz hizmet ile devam etmek için, bu güncelleştirilmiş değerleri 10 Eylül 2018 ' dan önce IP adreslerinize eklemeniz gerekir:

- 20.190.128.0/18

- 40.126.0.0/18

Tüm ağ trafiğiniz yeni aralıklara taşınana kadar eski IP adresi aralıklarını kaldırmamak kesinlikle önerilir. Taşıma hakkındaki güncelleştirmeler ve eski aralıkları ne zaman kaldırabileceğinizi öğrenmek için bkz. [Office 365 URL 'leri ve IP adresi aralıkları](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Değişiklik bildirimi: yetkilendirme kodları artık yeniden kullanım için kullanılamayacak

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** kimlik doğrulamaları (oturum açma) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

15 Kasım 2018 tarihinden itibaren Azure AD, uygulamalar için önceden kullanılan kimlik doğrulama kodlarını kabul etmeyi durduracak. Bu güvenlik değişikliği, Azure AD 'yi OAuth belirtimine göre satıra getirmeye yardımcı olur ve hem v1 hem de v2 uç noktalarında zorunlu kılınır.

Uygulamanız birden fazla kaynağa belirteç almak için yetkilendirme kodlarını yeniden kullanıyorsa, bir yenileme belirteci almak için kodu kullanmanızı ve ardından bu yenileme belirtecini kullanarak diğer kaynaklar için ek belirteçler elde etmenizi öneririz. Yetkilendirme kodları yalnızca bir kez kullanılabilir, ancak yenileme belirteçleri birden çok kaynak arasında birden çok kez kullanılabilir. OAuth kod akışı sırasında bir kimlik doğrulama kodunu yeniden kullanmayı deneyen bir uygulama invalid_grant bir hata alır.

Bu ve diğer protokollerle ilgili değişiklikler için, [kimlik doğrulaması yenilikleri hakkında tam listeye](../develop/reference-breaking-changes.md)bakın.

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Self servis parola (SSPR) ve Multi-Factor Authentication (MFA) için yakınsanmış güvenlik bilgi yönetimi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** SSPR **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Bu yeni özellik, kullanıcıların güvenlik bilgilerini (örneğin, telefon numarası, mobil uygulama vb.) SSPR ve MFA için tek bir konum ve deneyimle yönetmesine yardımcı olur; daha önce olduğu gibi, iki farklı konumda yapıldığı gibi.

Bu yakınsama deneyimi Ayrıca SSPR veya MFA kullanan kişiler için de geçerlidir. Ayrıca, kuruluşunuz MFA veya SSPR kaydını zorlayamazsa, kullanıcılar uygulama tarafından izin verilen tüm MFA veya SSPR güvenlik bilgileri yöntemlerini uygulamalarım portalından kaydedebilir.

Bu, isteğe bağlı bir genel önizlemedir. Yöneticiler seçili bir grup veya bir Kiracıdaki tüm kullanıcılar için yeni deneyimi (isteniyorsa) açabilir. Yakınsama deneyimi hakkında daha fazla bilgi için, bkz. [Yakınsanan deneyim blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD uygulama ara sunucusu uygulamalarında yeni HTTP-Only tanımlama bilgileri ayarı

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** Access Control

Uygulama ara sunucusu uygulamalarınızda **yalnızca http tanımlama bilgileri** adlı yeni bir ayar vardır. Bu ayar, hem uygulama proxy 'Si erişimi hem de oturum tanımlama bilgileri için HTTP yanıt üst bilgisine HTTPOnly bayrağını ekleyerek ek güvenlik sağlanmasına yardımcı olur, istemci tarafı betikten tanımlama bilgisine erişimi durduruyor ve tanımlama bilgisini kopyalama ya da değiştirme gibi eylemleri daha da engelliyor. Bu bayrak daha önce kullanılmasa da, tanımlama bilgileriniz her zaman hatalı değişikliklere karşı korumaya yardımcı olmak için bir TLS bağlantısı kullanılarak şifrelenir ve iletilir.

Bu ayar, Uzak Masaüstü gibi ActiveX denetimlerini kullanan uygulamalarla uyumlu değildir. Bu durumda, bu ayarı kapatmanız önerilir.

HTTP-Only tanımlama bilgileri ayarı hakkında daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure kaynakları için Privileged Identity Management (PıM), yönetim grubu kaynak türlerini destekler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Privileged Identity Management **ürün özelliği:** Privileged Identity Management

Tam zamanında etkinleştirme ve atama ayarları artık abonelikler, kaynak grupları ve kaynaklar (VM 'Ler, uygulama hizmetleri ve daha fazlası gibi) için zaten yaptığınız gibi yönetim grubu kaynak türlerine uygulanabilir. Ayrıca, bir yönetim grubu için yönetici erişimi sağlayan role sahip olan herkes bu kaynağı PıM 'de bulabilir ve yönetebilir.

PıM ve Azure kaynakları hakkında daha fazla bilgi için bkz. [Privileged Identity Management kullanarak Azure kaynaklarını bulma ve yönetme](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Uygulama erişimi (Önizleme) Azure AD portalına daha hızlı erişim sağlar

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Privileged Identity Management **ürün özelliği:** Privileged Identity Management

Bugün, PıM kullanarak bir rolü etkinleştirirken, izinlerin etkili olması 10 dakika sürebilir. Şu anda genel önizleme aşamasında olan uygulama erişimini kullanmayı seçerseniz, Yöneticiler, etkinleştirme isteği tamamlandıktan hemen sonra Azure AD portalına erişebilir.

Şu anda uygulama erişimi yalnızca Azure AD Portal deneyimini ve Azure kaynaklarını destekler. PıM ve uygulama erişimi hakkında daha fazla bilgi için bkz. [Azure AD Privileged Identity Management nedir?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Ağustos 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Ağustos 2018 ' de, uygulama galerisine federasyon desteğiyle bu 16 yeni uygulamayı ekledik:

[Hornbill](../saas-apps/hornbill-tutorial.md), [bridgeline bağlanmamış](../saas-apps/bridgelineunbound-tutorial.md), [Sauce Labs-mobil ve Web testi](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [meta ağlar Bağlayıcısı](../saas-apps/metanetworksconnector-tutorial.md), [yaptığımız](../saas-apps/waywedo-tutorial.md), [spotinst](../saas-apps/spotinst-tutorial.md), [Promaster (ınlogik tarafından)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [dossıer](../saas-apps/dossier-tutorial.md), [N2F-harcama raporları](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 canlı sohbet](../saas-apps/comm100livechat-tutorial.md), [safeconnect](../saas-apps/safeconnect-tutorial.md), [zenqms](../saas-apps/zenqms-tutorial.md), [eluminate](../saas-apps/eluminate-tutorial.md), [dovetale](../saas-apps/dovetale-tutorial.md).

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Yerel Tableau desteği artık Azure AD Uygulama Ara Sunucusu kullanılabilir

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** Access Control

OpenID 'deki güncelleştirmemizden, ön kimlik doğrulama protokoliz için OAuth 2,0 kod verme protokolüne bağlanın, artık uygulama proxy 'Si ile Tableau kullanmak için ek yapılandırma yapmanız gerekmez. Bu protokol değişikliği Ayrıca, JavaScript ve HTML etiketlerinde yaygın olarak desteklenen yalnızca HTTP yeniden yönlendirmeleri kullanarak, uygulama proxy 'Sinin daha modern uygulamaları daha iyi desteklemesini sağlar.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Azure Active Directory (Önizleme) içinde B2B Konuk kullanıcıları için bir kimlik sağlayıcısı olarak Google eklemek üzere yeni destek

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2B **ürün yeteneği:** B2B/B2C

Kuruluşunuzda Google ile Federasyon ayarlayarak, bir kişisel Microsoft hesabı (MSAs) veya bir Azure AD hesabı oluşturmaya gerek kalmadan, Gmail kullanıcılarının mevcut Google hesabını kullanarak paylaşılan uygulamalarınızda ve kaynaklarda oturum açmasını sağlayabilirsiniz.

Bu, isteğe bağlı bir genel önizlemedir. Google Federasyonu hakkında daha fazla bilgi için bkz. [B2B Konuk kullanıcıları için kimlik sağlayıcısı olarak Google ekleme](../external-identities/google-federation.md).

---

## <a name="july-2018"></a>Temmuz 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory e-posta bildirimlerine yönelik iyileştirmeler

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

Azure Active Directory (Azure AD) e-postaları artık güncelleştirilmiş bir tasarımın yanı sıra, aşağıdaki hizmetlerden gönderildiğinde gönderenin e-posta adresi ve gönderen görünen adı üzerinde değişiklik gösterir:

- Azure AD erişim Incelemeleri
- Azure AD Connect Health
- Azure AD Kimlik Koruması
- Azure AD Privileged Identity Management
- Kurumsal uygulamanın süresi dolan sertifika bildirimleri
- Kurumsal uygulama sağlama hizmeti bildirimleri

E-posta bildirimleri şu e-posta adresinden ve görünen adla gönderilir:

- E-posta adresi: azure-noreply@microsoft.com
- Görünen ad: Microsoft Azure

Yeni e-posta tasarımlarının bir örneği ve daha fazla bilgi için bkz. [Azure AD PIM 'de e-posta bildirimleri](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD etkinlik günlükleri artık Azure Izleyici aracılığıyla kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Azure AD etkinlik günlükleri artık Azure Izleyici (Azure 'un platform genelinde izleme hizmeti) için genel önizlemede kullanıma sunulmuştur. Azure Izleyici, bu geliştirmelere ek olarak uzun süreli saklama ve sorunsuz tümleştirme sağlar:

- Günlük dosyalarınızı kendi Azure depolama hesabınıza yönlendirerek uzun süreli saklama.

- Sorunsuz SıEM tümleştirmesi, Özel betikler yazmanızı veya bakımını gerektirmenize gerek kalmadan.

- Kendi özel çözümlerinizle, analiz araçlarınızla veya olay yönetimi çözümleriyle sorunsuz tümleştirme.

Bu yeni yetenekler hakkında daha fazla bilgi için bkz. Azure [izleyici tanılama 'daki blog Azure ad etkinlik günlükleri artık genel önizlemede](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) ve belgelerimizde, [Azure izleyici 'de etkinlik günlükleri Azure Active Directory (Önizleme)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Azure AD oturum açma raporuna koşullu erişim bilgileri eklendi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** kimlik güvenliği & koruması

Bu güncelleştirme, bir kullanıcı ilke sonucuyla birlikte oturum açtığında hangi ilkelerin değerlendirildiğini görmenizi sağlar. Ayrıca, rapor artık Kullanıcı tarafından kullanılan istemci uygulaması türünü içerir, böylece eski protokol trafiğini belirleyebilirsiniz. Rapor girdileri Ayrıca, kullanıcıya yönelik hata iletisinde bulunan bir bağıntı KIMLIĞI için aranabilir ve eşleşen oturum açma isteğini tanımlamak ve sorunlarını gidermek için kullanılabilir.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Oturum açma işlemleri etkinlik günlükleri aracılığıyla eski kimlik doğrulamaları görüntüleme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Oturum açma etkinliği günlüklerindeki **Istemci uygulaması** alanının tanıtılmasıyla, müşteriler artık eski kimlik doğrulamaları kullanan kullanıcıları görebilirler. Müşteriler, oturum açma Microsoft Graph API 'sini kullanarak veya Azure AD portalındaki oturum açma etkinlik günlükleri aracılığıyla, eski kimlik doğrulamaları için filtre uygulamak üzere **Istemci uygulaması** denetimini kullanabileceğiniz oturum açma etkinliği günlükleri aracılığıyla bu bilgilere erişebilecektir. Daha fazla bilgi için belgelere göz atın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Temmuz 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Haziran 2018 ' de, uygulama galerisine federasyon desteğiyle bu 16 yeni uygulamayı ekledik:

[Yenilik hub](../saas-apps/innovationhub-tutorial.md), [leapsome](../saas-apps/leapsome-tutorial.md), [belirli yönetici SSO](../saas-apps/certainadminsso-tutorial.md), Psuc hazırlama, [IPASS SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [ekran kaydı-O-arada](../saas-apps/screencast-tutorial.md), powerokul Birleşik sınıf, [eli ekleme](../saas-apps/elionboarding-tutorial.md), [bomgar remote support](../saas-apps/bomgarremotesupport-tutorial.md), [Nimblex](../saas-apps/nimblex-tutorial.md), [Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 joinnow Connector](../saas-apps/securejoinnow-tutorial.md), [kanbanize](../saas-apps/kanbanize-tutorial.md), [smartlpa](../saas-apps/smartlpa-tutorial.md), [yetenekler tabanı](../saas-apps/skillsbase-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Yeni Kullanıcı hazırlama SaaS uygulaması tümleştirmeleri-2018 Temmuz

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği:** üçüncü taraf tümleştirmesi

Azure AD, Dropbox, Salesforce, ServiceNow gibi SaaS uygulamalarında kullanıcı kimliklerinin oluşturulmasını, bakımını ve kaldırılmasını otomatik hale getirmenizi sağlar. 2018 Temmuz için, Azure AD uygulama galerisinde aşağıdaki uygulamalar için Kullanıcı hazırlama desteği ekledik:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Azure AD galerisinde Kullanıcı sağlamayı destekleyen tüm uygulamaların bir listesi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Eşitleme için Connect Health-yalnız bırakılmış ve yinelenen öznitelik eşitleme hatalarını gidermenin daha kolay bir yolu

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** AD Connect **ürün yeteneği:** izleme & raporlama

Azure AD Connect Health, eşitleme hatalarını vurgulamanıza ve düzeltmemize yardımcı olmak için self servis düzeltmesini tanıtır. Bu özellik yinelenen öznitelik eşitleme hatalarını giderir ve Azure AD 'den yalnız bırakılmış nesneleri düzeltir. Bu tanılamada aşağıdaki avantajlar bulunur:

- Yinelenen öznitelik eşitleme hatalarını daraltır ve belirli düzeltmeleri sağlar

- Özel Azure AD senaryoları için bir çözüm uygular, tek bir adımda hataları çözer

- Bu özelliği açmak ve kullanmak için yükseltme veya yapılandırma gerekmez

Daha fazla bilgi için bkz. [yinelenen öznitelik eşitleme hatalarını tanılama ve](../hybrid/how-to-connect-health-diagnose-sync-errors.md) Düzeltme

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Azure AD ve MSA oturum açma deneyimlerine yönelik görsel güncelleştirmeler

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Azure AD **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Microsoft 'un Office 365 ve Azure gibi çevrimiçi hizmetler oturum açma deneyimi için Kullanıcı arabirimini güncelleştirdik. Bu değişiklik ekranları daha az karışık ve daha basit hale getirir. Bu değişiklik hakkında daha fazla bilgi için bkz. [Azure AD oturum açma deneyimi bloguna yönelik yakında iyileştirmeler](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect yeni sürümü-Temmuz 2018

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

Azure AD Connect en son sürümü şunları içerir:

- Hata düzeltmeleri ve desteklenebilirlik Updates

- Ping-Federate tümleştirmesinin genel kullanılabilirliği

- En son SQL 2012 istemcisine yönelik güncelleştirmeler

Bu güncelleştirme hakkında daha fazla bilgi için bkz [. Azure AD Connect: sürüm yayınlama geçmişi](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Son Kullanıcı Kullanıcı arabirimi kullanım koşullarına göre güncelleştirmeler

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** idare

TOU Son Kullanıcı Kullanıcı arabirimindeki kabul dizesini güncelleştiriyoruz.

**Geçerli metin.** [TenantName] kaynaklarına erişmek için kullanım koşullarını kabul etmeniz gerekir.<br>**Yeni metin.** [TenantName] kaynağına erişmek için kullanım koşullarını okumanız gerekir.

**Geçerli metin:** Kabul etmek için seçilmesi, yukarıdaki kullanım koşullarının tümünü kabul ettiğiniz anlamına gelir.<br>**Yeni metin:** Kullanım koşullarını okuduğunuzu ve anladığınızı onaylamak için lütfen kabul et ' e tıklayın.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Doğrudan kimlik doğrulaması eski protokolleri ve uygulamaları destekler

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Doğrudan kimlik doğrulaması artık eski protokolleri ve uygulamaları desteklemektedir. Aşağıdaki sınırlamalar artık tam olarak desteklenmektedir:

- Modern kimlik doğrulaması gerekmeden eski Office istemci uygulamalarına, Office 2010 ve Office 2013 Kullanıcı oturum açma işlemleri.

- Yalnızca Office 2010 ' de Exchange karma ortamlarında Takvim paylaşımına ve ücretsiz/meşgul bilgilere erişim.

- Modern kimlik doğrulaması gerekmeden Skype Kurumsal istemci uygulamalarına Kullanıcı oturum açma işlemleri.

- PowerShell sürüm 1,0 'de Kullanıcı oturum açma işlemleri.

- Apple Aygıt Kayıt Programı (Apple DEP), iOS Kurulum Yardımcısı kullanılarak.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Self servis parola sıfırlama ve Multi-Factor Authentication için yakınsama güvenlik bilgileri yönetimi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** SSPR **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Bu yeni özellik, kullanıcıların güvenlik bilgilerini (örneğin, telefon numarası, e-posta adresi, mobil uygulama vb.) self servis parola sıfırlama (SSPR) ve Multi-Factor Authentication (MFA) için tek bir deneyimde yönetmesine olanak tanır. Kullanıcılar artık SSPR ve MFA için aynı güvenlik bilgilerini iki farklı deneyimle kaydetmeyecektir. Bu yeni deneyim Ayrıca SSPR ya da MFA 'ya sahip kullanıcılar için de geçerlidir.

Bir kuruluş MFA veya SSPR kaydını zormıyorsa, kullanıcılar kendi güvenlik bilgilerini **uygulamalarım** portalı aracılığıyla kaydedebilir. Buradan, kullanıcılar MFA veya SSPR için etkinleştirilen tüm yöntemleri kaydedebilir.

Bu, isteğe bağlı bir genel önizlemedir. Yöneticiler seçili bir Kullanıcı grubu veya bir Kiracıdaki tüm kullanıcılar için yeni deneyimi (isteniyorsa) açabilir.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Parolanızı sıfırladıktan sonra kimliğinizi doğrulamak için Microsoft Authenticator uygulamasını kullanın

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** SSPR **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Bu özellik, Microsoft Authenticator (veya başka bir Authenticator uygulamadan) bir bildirim veya kod kullanarak bir parolayı sıfırlarken yönetici olmayan kimlik doğrulamasını sağlar. Yöneticiler bu Self servis parola sıfırlama yöntemini etkinleştirdikten sonra, aka.ms/mfasetup veya aka.ms/setupsecurityinfo aracılığıyla bir mobil uygulama kaydeden kullanıcılar, parolalarını sıfırlarken kendi mobil uygulamalarını doğrulama yöntemi olarak kullanabilir.

Mobil uygulama bildirimi, yalnızca parolanızı sıfırlamak için iki yöntem gerektiren bir ilkenin parçası olarak açılabilir.

---

## <a name="june-2018"></a>Haziran 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Değişiklik bildirimi: Azure AD etkinlik günlükleri API 'sini kullanarak uygulamalar için yetkilendirilmiş yetkilendirme akışına güvenlik düzeltme

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** raporlama **ürün yeteneği:** izleme & raporlama

Daha güçlü güvenlik uygulamamız nedeniyle, [Azure ad etkinlik günlükleri API 'lerine](../reports-monitoring/concept-reporting-api.md)erişmek için yetkilendirilmiş yetkilendirme akışı kullanan uygulamalar için izinlerde değişiklik yapmamız gerekiyordu. Bu değişiklik **26 haziran 2018 '** de gerçekleşir.

Uygulamalarınızdan herhangi biri Azure AD etkinlik günlüğü API 'Lerini kullanıyorsa, değişikliğin gerçekleşmesinden sonra uygulamanın kesilmesini olmamasını sağlamak için aşağıdaki adımları izleyin.

**Uygulama izinlerinizi güncelleştirmek için**

1. Azure portal oturum açın, **Azure Active Directory**' i seçin ve ardından **uygulama kayıtları**' nı seçin.
2. Azure AD etkinlik günlükleri API 'sini kullanan uygulamanızı seçin, **Ayarlar**' ı seçin, **gerekli izinler**' i seçin ve ardından **Windows Azure Active Directory** API 'sini seçin.
3. **Erişimi etkinleştir** dikey penceresinin **temsilci izinleri** alanında, **Dizin verilerini oku** ' nın yanındaki kutuyu işaretleyin ve ardından **Kaydet**' i seçin.
4. **Izin ver**' i seçin ve ardından **Evet**' i seçin.

    >[!Note]
    >Uygulamaya izin vermek için bir genel yönetici olmanız gerekir.

Daha fazla bilgi için Azure AD Raporlama API 'SI makalesine erişim için önkoşulların [Izin verme](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) bölümüne bakın.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>PCI DSS uyumluluğu için Azure AD hizmetlerine bağlanmak üzere TLS ayarlarını yapılandırma

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** yok **ürün yeteneği:** platform

Aktarım Katmanı Güvenliği (TLS), iki iletişim uygulaması arasında gizlilik ve veri bütünlüğü sağlayan bir protokoldür ve günümüzde kullanılan en yaygın olarak dağıtılan güvenlik protokolüdür.

[PCI güvenlik standartları Council](https://www.pcisecuritystandards.org/) , uyumluluk **30 Haziran 2018**' den itibaren, yeni ve daha güvenli uygulama PROTOKOLLERININ etkinleştirilmesi için eski TLS ve Güvenli Yuva Katmanı (SSL) sürümlerinin devre dışı bırakılması gerektiğini belirledi. Bu değişiklik, Azure AD hizmetlerine bağlanıp PCI DSS uyumluluğunu gerektirdiğinde TLS 1,0 ' i devre dışı bırakmanız anlamına gelir. Birden çok TLS sürümü mevcuttur, ancak TLS 1,2 Azure Active Directory Hizmetleri için kullanılabilen en son sürümdür. Hem istemci/sunucu hem de tarayıcı/sunucu birleşimleri için doğrudan TLS 1,2 ' e geçmeyi öneririz.

Güncel olmayan tarayıcılar TLS 1,2 gibi daha yeni TLS sürümlerini desteklemeyebilir. Tarayıcı tarafından hangi TLS sürümlerinin desteklendiğini görmek için [Qualys SSL Labs](https://www.ssllabs.com/) sitesine gidin ve **tarayıcınızı test et**' e tıklayın. Web tarayıcınızın en son sürümüne yükseltmenizi ve tercihen yalnızca TLS 1,2 ' i etkinleştirmenizi öneririz.

**TLS 1,2 'yi tarayıcıda etkinleştirmek için**

- **Microsoft Edge ve Internet Explorer (her ikisi de Internet Explorer kullanılarak ayarlanır)**

    1. Internet Explorer 'ı açın, **Araçlar**  >  **Internet seçenekleri**  >  **Gelişmiş**' i seçin.
    2. **Güvenlik** alanında, **TLS 1,2 kullan**' ı seçin ve ardından **Tamam**' ı seçin.
    3. Tüm tarayıcı pencerelerini kapatın ve Internet Explorer 'ı yeniden başlatın.

- **Google Chrome**

    1. Google Chrome ' ı açın, adres çubuğuna *Chrome://Settings/* yazın ve **ENTER** tuşuna basın.
    2. **Gelişmiş** seçenekleri genişletin, **sistem** alanına gidin ve **proxy ayarlarını aç**' ı seçin.
    3. **Internet özellikleri** kutusunda **Gelişmiş** sekmesini seçin, **güvenlik** alanı ' na gidin, **TLS 1,2 kullan**' ı seçin ve ardından **Tamam**' ı seçin.
    4. Tüm tarayıcı pencerelerini kapatın ve Google Chrome 'u yeniden başlatın.

- **Mozilla Firefox**

    1. Firefox ' u açın, adres çubuğuna *about: config* yazın ve **ENTER** tuşuna basın.
    2. *TLS* terimini arayın ve ardından **Security. TLS. Version. Max** girişini seçin.
    3. Tarayıcıyı TLS 1,2 sürümüne kadar kullanmaya zorlamak için değeri **3** olarak ayarlayın ve ardından **Tamam**' ı seçin.

        >[!NOTE]
        >Firefox sürüm 60,0, TLS 1,3 ' yi desteklediğinden, Security. TLS. Version. Max değerini **4** olarak da ayarlayabilirsiniz.

    4. Tüm tarayıcı pencerelerini kapatın ve Mozilla Firefox 'u yeniden başlatın.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Haziran 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Haziran 2018 ' de, uygulama galerisine federasyon desteğiyle bu 15 yeni uygulamayı ekledik:

[Ufuk dokunma](../saas-apps/skytap-tutorial.md), [müzik kapatma](../saas-apps/settlingmusic-tutorial.md), [SAML 1,1 belirteci etkin lob uygulaması](../saas-apps/saml-tutorial.md), [süper ruh](../saas-apps/supermood-tutorial.md), [otomatik görev](../saas-apps/autotaskendpointbackup-tutorial.md), [uç nokta yedekleme](../saas-apps/autotaskendpointbackup-tutorial.md), [ufuk yüksek ağlar](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [Tonicdm](../saas-apps/tonicdm-tutorial.md), [moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint şirket içi](../saas-apps/sharepoint-on-premises-tutorial.md), bir adet [CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [yükselkx](../saas-apps/vidyard-tutorial.md) [](../saas-apps/chronicx-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md). Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD parola koruması genel önizlemede kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Ortamınızdaki kolayca tahmin edilen parolaları ortadan kaldırmaya yardımcı olması için Azure AD parola korumasını kullanın. Bu parolaların ortadan kaldırılması, parola spreyi saldırılarına karşı riskli olma riskini azaltmaya yardımcı olur.

Azure AD parola koruması özellikle şunları yapmanıza yardımcı olur:

- Kuruluşunuzun hesaplarını hem Azure AD hem de Windows Server Active Directory (AD) içinde koruyun.
- Kullanıcılarınızın, en sık kullanılan parolaların 500 ' inden daha fazla bir listede parola kullanmasını ve bu parolaların 1.000.000 karakterlik değiştirme çeşitlemelerini engeller.
- Azure AD parola korumasını Azure AD portalındaki tek bir konumdan, hem Azure AD hem de şirket içi Windows Server AD için yönetin.

Azure AD parola koruması hakkında daha fazla bilgi için bkz. [kuruluşunuzdaki kötü parolaları kaldırma](../authentication/concept-password-ban-bad.md).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Kullanım koşulları oluşturma sırasında yeni "tüm konuklar" koşullu erişim ilkesi şablonu oluşturuldu

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** idare

Kullanım koşullarınızın oluşturulması sırasında, "tüm konuklar" ve "tüm uygulamalar" için de yeni bir koşullu erişim ilkesi şablonu oluşturulur. Bu yeni ilke şablonu, yeni oluşturulan ToU 'yi uygular ve konuklar için oluşturma ve zorlama sürecini hızlandırma.

Daha fazla bilgi için bkz. [Azure Active Directory kullanım koşulları özelliği](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Kullanım koşulları oluşturma sırasında yeni "özel" koşullu erişim ilkesi şablonu oluşturuldu

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** idare

Kullanım koşullarınızın oluşturulması sırasında yeni bir "özel" koşullu erişim ilkesi şablonu da oluşturulur. Bu yeni ilke şablonu, ToU 'yi oluşturmanızı ve ardından doğrudan portalda gezinmenize gerek kalmadan koşullu erişim ilkesi oluşturma dikey penceresine gitmenizi sağlar.

Daha fazla bilgi için bkz. [Azure Active Directory kullanım koşulları özelliği](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication dağıtma hakkında yeni ve kapsamlı kılavuz

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** kimlik güvenliği & koruması

Kuruluşunuzda Azure AD Multi-Factor Authentication (MFA) dağıtma hakkında yeni adım adım yönergeler yayımladık.

MFA dağıtım kılavuzunu görüntülemek için GitHub 'daki [kimlik dağıtım kılavuzlarını](./active-directory-deployment-plans.md) depoya gidin. Dağıtım kılavuzlarıyla ilgili geri bildirim sağlamak için [dağıtım planı geri bildirim formunu](https://aka.ms/deploymentplanfeedback)kullanın. Dağıtım kılavuzlarıyla ilgili sorularınız varsa, [IDGitDeploy](mailto:idgitdeploy@microsoft.com)adresinden bizimle iletişime geçin.

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD tarafından atanan uygulama yönetimi rolleri genel önizlemede

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** Access Control

Yöneticiler artık genel yönetici rolünü atamadan uygulama yönetim görevleri için temsilci verebilir. Yeni roller ve yetenekler şunlardır:

- **Yeni standart Azure AD yönetici rolleri:**

    - **Uygulama Yöneticisi.** Kayıt, SSO ayarları, uygulama atamaları ve lisanslama, uygulama ara sunucusu ayarları ve onay (Azure AD kaynakları hariç) dahil olmak üzere tüm uygulamaların tüm yönlerini yönetme olanağı verir.

    - **Bulut uygulaması Yöneticisi.** Uygulama proxy 'si dışında, şirket içi erişim sağlamadığı için tüm uygulama yöneticisi yeteneklerini verir.

    - **Uygulama geliştiricisi.** **Kullanıcıların uygulama kaydetmesine izin ver** seçeneği devre dışı olsa bile, uygulama kayıtları oluşturma özelliğini verir.

- **Sahiplik (Grup sahipliği işlemine benzer şekilde, uygulama başına kayıt ve kurumsal başına uygulama ayarlayın:**

    - **Uygulama kayıt sahibi.** Uygulama bildirimi ve ek sahipler ekleme dahil olmak üzere sahip olan uygulama kaydı 'nın tüm yönlerini yönetme olanağı verir.

    - **Kurumsal uygulama sahibi.** SSO ayarları, uygulama atamaları ve onay (Azure AD kaynakları hariç) dahil olmak üzere sahip olunan kurumsal uygulamaların birçok yönlerini yönetme olanağı verir.

Genel önizleme hakkında daha fazla bilgi için bkz. [Azure AD temsilci uygulama yönetimi rolleri genel önizlemede](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) . lenemeyen. Roller ve izinler hakkında daha fazla bilgi için bkz. [Azure Active Directory yönetici rolleri atama](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Mayıs 2018

### <a name="expressroute-support-changes"></a>ExpressRoute destek değişiklikleri

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** kimlik doğrulamaları (oturum açma) **ürün yeteneği:** platform

Azure Active Directory (Azure AD) gibi bir hizmet sunumu, ExpressRoute veya diğer özel VPN tünellerine gerek duymadan doğrudan Internet üzerinden çalışacak şekilde tasarlanmıştır. Bu nedenle, **1 ağustos 2018**' de Azure genel eşleme ve Microsoft eşleme 'de Azure toplulukları KULLANıLARAK Azure AD Hizmetleri Için ExpressRoute desteğini durduracağız. Bu değişiklikten etkilenen tüm hizmetler, Azure AD trafiğinin yavaş şekilde ExpressRoute 'a Internet 'e kaydırdığına bildirimde bulunabilir.

Desteğimizi değiştirirken, kimlik doğrulama trafiğiniz için adanmış bir devre kümesi kullanmanız gerekebilecek durumlar olduğunu da biliyoruz. Bu nedenle, Azure AD "diğer Office 365 Online Services" topluluğuyla Microsoft eşliğinde zaten ExpressRoute ve hizmetleri kullanan kiracı başına IP aralığı kısıtlamalarını desteklemeye devam edecektir. Hizmetleriniz etkilenirse, ancak ExpressRoute gerekliyse, aşağıdakileri yapmanız gerekir:

- **Azure genel eşleme kullanıyorsanız.** Microsoft eşlemesi ' ne gidin ve **diğer Office 365 Online Services (12076:5100)** topluluğuna kaydolun. Azure genel eşlemeden Microsoft eşlemesine nasıl geçeceğimiz hakkında daha fazla bilgi için bkz. [genel eşlemeyi Microsoft eşlemesi 'Ne taşıma](../../expressroute/how-to-move-peering.md) makalesi.

- **Microsoft eşleme kullanıyorsanız.** **Diğer Office 365 çevrimiçi hizmet (12076:5100)** topluluğu için kaydolun. Yönlendirme gereksinimleri hakkında daha fazla bilgi için, ExpressRoute yönlendirme gereksinimleri makalesinin [BGP toplulukları Için destek bölümüne](../../expressroute/expressroute-routing.md#bgp) bakın.

Adanmış devreleri kullanmaya devam etmeniz gerekiyorsa, **diğer Office 365 çevrimiçi hizmet (12076:5100)** Community 'yi kullanma yetkisi alma hakkında Microsoft hesabı ekibinizle konuşmanız gerekir. MS Office tarafından yönetilen gözden geçirme panosu, bu devrelere ihtiyacınız olup olmadığını doğrular ve bunları tutmanın teknik etkilerini anladığınızdan emin olmanızı sağlar. Office 365 için yol filtreleri oluşturmaya çalışan yetkisiz abonelikler, bir hata iletisi alır.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>TOU için yönetim senaryoları için Microsoft Graph API 'Leri

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** geliştirici deneyimi

Azure AD kullanım koşulları 'nın yönetim işlemi için Microsoft Graph API 'Leri ekledik. Kullanım koşulları nesnesi oluşturabilir, güncelleştirebilir, silebilirsiniz.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD B2C bir kimlik sağlayıcısı olarak Azure AD çok kiracılı uç noktası ekleme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Özel ilkeleri kullanarak artık Azure AD ortak uç noktasını Azure AD B2C bir kimlik sağlayıcısı olarak ekleyebilirsiniz. Bu, uygulamalarınızda oturum açan tüm Azure AD kullanıcıları için tek bir giriş noktasına sahip etmenize olanak tanır. Daha fazla bilgi için bkz. [Azure Active Directory B2C: kullanıcıların özel ilkeleri kullanarak çok kiracılı bir Azure AD kimlik sağlayıcısında oturum açmalarına Izin verin](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Uygulamalarım oturum açma uzantımız ve Azure AD Uygulama Ara Sunucusu her yerden uygulamalara erişmek için Iç URL 'Leri kullanın

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulamalarım **ürün yeteneği:** SSO

Kullanıcılar, Azure AD için uygulamalarım güvenli oturum açma uzantısını kullanarak kurumsal ağınızın dışında bile uygulamalara iç URL 'Ler aracılığıyla erişebilir. Bu, aynı zamanda erişim paneli tarayıcı uzantısının yüklü olduğu herhangi bir tarayıcıda Azure AD Uygulama Ara Sunucusu kullanarak yayımladığınız herhangi bir uygulamayla birlikte çalışacaktır. Kullanıcı uzantıya oturum açtıktan sonra URL yeniden yönlendirme işlevi otomatik olarak etkinleştirilir. Uzantı [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)ve [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)'ta indirilebilir.

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory-Avrupa müşterileri için Avrupa 'daki veriler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** altın ocal

Avrupa 'daki müşteriler, verilerin Avrupa 'da kalmasını ve müşterilerin gizlilik ve Avrupa yasaları için Avrupa veri merkezleri dışında çoğaltılmaması gerekir. Bu [makalede](./active-directory-data-storage-eu.md) , Avrupa 'da hangi kimlik bilgilerinin depolanacağı ve ayrıca Avrupa veri merkezleri dışında depolanacak bilgiler hakkındaki ayrıntıları sağlayan belirli ayrıntılar sağlanmaktadır.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Yeni Kullanıcı hazırlama SaaS uygulaması tümleştirmeleri-Mayıs 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği:** üçüncü taraf tümleştirmesi

Azure AD, Dropbox, Salesforce, ServiceNow gibi SaaS uygulamalarında kullanıcı kimliklerinin oluşturulmasını, bakımını ve kaldırılmasını otomatik hale getirmenizi sağlar. Mayıs 2018 için, Azure AD uygulama galerisinde aşağıdaki uygulamalar için Kullanıcı hazırlama desteği ekledik:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Azure AD galerisinde Kullanıcı sağlamayı destekleyen tüm uygulamaların listesi için bkz [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) ..

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Grupların ve uygulama erişiminin Azure AD erişim gözden geçirmeleri artık yinelenen incelemeler sağlıyor

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** erişim gözden geçirmeleri **ürün yeteneği:** idare

Grupların ve uygulamaların erişim incelemesi artık Azure AD Premium P2 bir parçası olarak genel kullanıma sunulmuştur.  Yöneticiler, aylık veya üç aylık gibi düzenli aralıklarla otomatik olarak yinelenecek grup üyelikleri ve uygulama atamalarının erişim incelemelerini yapılandırabilecektir.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Azure AD etkinlik günlükleri (oturum açma ve denetim) artık MS Graph aracılığıyla kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Oturum açma işlemlerini ve denetim günlüklerini içeren Azure AD etkinlik günlükleri artık Microsoft Graph API aracılığıyla kullanılabilir. Bu günlüklere erişmek için Microsoft Graph API 'SI aracılığıyla iki uç noktası kullanıma sunduk. Başlamak için Azure AD Raporlama API 'Lerine programlı erişim için [belgelerimizi](../reports-monitoring/concept-reporting-api.md) inceleyin.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>B2B kullanım deneyimine yönelik geliştirmeler ve bir kuruluş bırakma

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2B **ürün yeteneği:** B2B/B2C

**Tam zamanında kullanım:** B2B API 'sini kullanarak bir kaynağı Konuk kullanıcıyla paylaştığınızda, özel bir davet e-postası göndermeniz gerekmez. Çoğu durumda, Konuk Kullanıcı kaynağa erişebilir ve tam zamanında kullanım deneyiminden alınacaktır. Eksik e-postalar nedeniyle daha fazla etki yok. Konuk kullanıcılarınızı "Bu kullanım bağlantısına tıkladı", sistem sizi gönderdi. Bu, SPO davet yöneticisini kullandığında-bulutlu ekleri tüm kullanıcılar için aynı kurallı URL 'yi (iç ve dış), herhangi bir kullanım durumunda sahip olabileceği anlamına gelir.

**Modern kullanım deneyimi:** Daha fazla bölünmüş ekran ödeme giriş sayfası yok. Kullanıcılar, üçüncü taraf uygulamalar için olduğu gibi, kuruluşun gizlilik bildirimiyle ilgili modern bir onay deneyimi görür.

**Konuk kullanıcılar kuruluş 'dan bırakabilir:** Bir kullanıcının bir org ile ilişkisi bittikten sonra, kuruluştan ayrılmaları için kendi kendine servis sağlayabilirsiniz. Kuruluşun yöneticisinin "kaldırılması" için davet eden başka hiçbir çağrı yok, daha fazla destek bileti yok.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir-Mayıs 2018

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Mayıs 2018 ' de, uygulama galerimize federasyon desteği olan bu 18 yeni uygulamayı ekledik:

[Awardspring](../saas-apps/awardspring-tutorial.md), ınfogix Data3Sixty yönetme [, yodestesi](../saas-apps/infogix-tutorial.md), [JAMF Pro](../saas-apps/jamfprosamlconnector-tutorial.md), [knowledgeowl](../saas-apps/knowledgeowl-tutorial.md), [Envi Mmis](../saas-apps/envimmis-tutorial.md), [launchkaranlık](../saas-apps/launchdarkly-tutorial.md), [Adobe Capze ana](../saas-apps/adobecaptivateprime-tutorial.md), [monsat çevrimiçi](../saas-apps/montageonline-tutorial.md), [まなびポケット](../saas-apps/manabipocket-tutorial.md), OpenReel, [yay yayımlama-SSO](../saas-apps/arc-tutorial.md), [plangrid](../saas-apps/plangrid-tutorial.md), [ıwellneskar](../saas-apps/iwellnessnow-tutorial.md), [proxyclick](../saas-apps/proxyclick-tutorial.md), [Riskware](../saas-apps/riskware-tutorial.md), [Flock](../saas-apps/flock-tutorial.md), [Gözden](../saas-apps/reviewsnap-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md).

Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory için yeni adım adım dağıtım kılavuzu

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** Dizin

Self servis parola sıfırlama (SSPR), çoklu oturum açma (SSO), koşullu erişim, uygulama proxy 'si, Kullanıcı sağlama, Active Directory Federasyon Hizmetleri (AD FS) (ADFS) ve parola karması eşitleme (PHS) gibi Azure Active Directory (Azure AD) dağıtma hakkında yeni ve adım adım yönergeler.

Dağıtım kılavuzlarını görüntülemek için GitHub 'daki [kimlik dağıtım kılavuzlarını](./active-directory-deployment-plans.md) depoya gidin. Dağıtım kılavuzlarıyla ilgili geri bildirim sağlamak için [dağıtım planı geri bildirim formunu](https://aka.ms/deploymentplanfeedback)kullanın. Dağıtım kılavuzlarıyla ilgili sorularınız varsa, [IDGitDeploy](mailto:idgitdeploy@microsoft.com)adresinden bizimle iletişime geçin.

---

### <a name="enterprise-applications-search---load-more-apps"></a>Kurumsal uygulamalar arama-daha fazla uygulama yükleme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

Uygulamalarınızın/hizmet sorumlularını bulmada sorun mu yaşıyorsunuz? Kurumsal uygulamalarınızın tüm uygulamalar listesine daha fazla uygulama yükleme özelliği ekledik. Varsayılan olarak 20 uygulama gösterilmektedir. Artık, ek uygulamaları görüntülemek için **daha fazla yükle** ' ye tıklayabilirsiniz.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>AADConnect 'in Mayıs yayını, PingFederate ile tümleştirmenin genel önizlemesini, önemli güvenlik güncelleştirmelerini, birçok hata düzeltmesi ve yeni harika yeni sorun giderme araçlarını içerir.

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** AD Connect **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

AADConnect 'in Mayıs yayını, PingFederate ile tümleştirmenin genel önizlemesini, önemli güvenlik güncelleştirmelerini, birçok hata düzeltmesi ve yeni harika yeni sorun giderme araçlarını içerir. Sürüm notlarını [buradan](../hybrid/reference-connect-version-history.md)bulabilirsiniz.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD erişim gözden geçirmeleri: otomatik uygulama

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** erişim gözden geçirmeleri **ürün yeteneği:** idare

Grupların ve uygulamaların erişim gözden geçirmeleri artık Azure AD Premium P2 bir parçası olarak genel kullanıma sunulmuştur. Yönetici, gözden geçirenin değişikliklerini bu grup veya uygulamaya otomatik olarak uygulamak için erişim gözden geçirmesi tamamlanana kadar yapılandırabilir. Yönetici ayrıca, gözden geçirenler yanıt vermezse, erişimi kaldırmadıysa, erişimi devam ettirdiğinde veya sistem önerilerini almak için kullanıcının devam erişiminin ne olacağını da belirtebilir.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>KIMLIK belirteçleri artık yeni uygulamalar için sorgu response_mode kullanılarak döndürülemez.

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

25 Nisan 2018 ' de veya sonrasında oluşturulan uygulamalar artık **sorgu** response_mode kullanarak **id_token** isteyemeyecektir.  Bu, Azure AD 'yi OıDC belirtimleriyle satır içi olarak getirir ve uygulamalarınızın saldırı yüzeyini azaltmaya yardımcı olur.  25 Nisan 2018 tarihinden önce oluşturulan uygulamalar, **id_token** response_type **sorgu** response_mode kullanılmasını engellenmemiş.  Azure AD 'den bir id_token istenirken döndürülen hata, **AADSTS70007: ' Query ', belirteç istenirken ' response_mode ' için desteklenen bir değer değil**.

**Parça** ve **form_post** response_modes çalışmaya devam eder-yeni uygulama nesneleri oluştururken (örneğin, uygulama proxy 'si kullanımı için), yeni bir uygulama oluşturmadan önce bu response_modes birini kullandığınızdan emin olun.

---

## <a name="april-2018"></a>Nisan 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C erişim belirteci GA

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Artık erişim belirteçlerini kullanarak Azure AD B2C tarafından güvenliği sağlanmış Web API 'Lerine erişebilirsiniz. Bu özellik genel önizlemeden GA 'ye taşınıyor. Azure AD B2C uygulamaları ve Web API 'Lerini yapılandırmak için Kullanıcı arabirimi deneyimi geliştirilmiştir ve diğer küçük geliştirmeler yapılmıştır.

Daha fazla bilgi için bkz. [Azure AD B2C: erişim belirteçleri isteme](../../active-directory-b2c/access-tokens.md).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>SAML tabanlı uygulamalar için çoklu oturum açma yapılandırmasını test etme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

SAML tabanlı SSO uygulamalarını yapılandırırken yapılandırma sayfasında tümleştirmeyi test edebilirsiniz. Oturum açma sırasında bir hatayla karşılaşırsanız, testi deneymenizde hata verebilir ve Azure AD, belirli sorunu çözmek için çözüm adımları sağlar.

Daha fazla bilgi için bkz.

- [Azure Active Directory uygulama galerisinde bulunmayan uygulamalar için çoklu oturum açmayı yapılandırma](../manage-apps/view-applications-portal.md)
- [Azure Active Directory içindeki uygulamalarda SAML tabanlı çoklu oturum açma hatalarını ayıklama](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD kullanım koşulları artık Kullanıcı başına raporlamaya sahiptir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** uyumluluk

Yöneticiler artık belirli bir ToU seçebilir ve bu ToU 'ya onaylanan tüm kullanıcıları ve ne zaman/saat sürdüğünü görebilir.

Daha fazla bilgi için bkz. [Azure AD kullanım koşulları özelliği](../conditional-access/terms-of-use.md).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: AD FS Extranet kilitleme koruması için riskli IP

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** izleme & raporlama

Connect Health artık, saat başına veya günlük olarak başarısız U/P oturum açma eşiklerini aşan IP adreslerini algılama özelliğini desteklemektedir. Bu özellik tarafından sunulan yetenekler şunlardır:

- Özelleştirilebilir eşikle, IP adresini ve saatlik/günlük temelinde oluşturulan başarısız oturum açma sayısını gösteren kapsamlı rapor.
- Belirli bir IP adresinin saat/gün başına, başarısız U/P oturum açma eşiğini ne zaman aşmadığını gösteren e-posta tabanlı uyarılar.
- Verilerin ayrıntılı analizini yapmak için bir indirme seçeneği

Daha fazla bilgi için bkz. [RISKLI IP raporu](../hybrid/how-to-connect-health-adfs.md).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Meta veri dosyası veya URL 'SI olan kolay uygulama yapılandırması

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

Kurumsal uygulamalar sayfasında, Yöneticiler Azure AD Galerisi ve Galeri dışı uygulama için SAML tabanlı oturum açmayı yapılandırmak üzere bir SAML meta veri dosyasını karşıya yükleyebilir.

Ayrıca, hedef uygulamayla SSO 'yu yapılandırmak için Azure AD uygulama Federasyon meta verileri URL 'sini de kullanabilirsiniz.

Daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde olmayan uygulamalarda çoklu oturum açmayı yapılandırma](../manage-apps/view-applications-portal.md)konusuna bakın.

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD Kullanım koşulları genel kullanıma sunuldu

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** uyumluluk


Azure AD kullanım koşulları, genel önizlemeden genel kullanıma açık olarak taşınmıştır.

Daha fazla bilgi için bkz. [Azure AD kullanım koşulları özelliği](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Belirli kuruluşlardan B2B kullanıcılarına gönderilen davetlere izin verme veya engelleme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2B **ürün yeteneği:** B2B/B2C


Artık Azure AD B2B Işbirliğinde paylaşmak ve işbirliği yapmak istediğiniz iş ortağı kuruluşlarını belirtebilirsiniz. Bunu yapmak için, belirli izin verme veya reddetme etki alanlarının listesini oluşturmayı seçebilirsiniz. Bu özellikleri kullanarak bir etki alanı engellendiğinde, çalışanlar artık bu etki alanındaki kişilere davetiye gönderemezler.

Bu, onaylanan kullanıcılar için sorunsuz bir deneyim etkinleştirirken kaynaklarınıza erişimi denetlemenize yardımcı olur.

Bu B2B Işbirliği özelliği tüm Azure Active Directory müşterileri tarafından kullanılabilir ve dış iş kullanıcılarının oturum açma ve erişim kazanmasıyla ilgili daha ayrıntılı denetim sağlamak için koşullu erişim ve kimlik koruması gibi Azure AD Premium özelliklerle birlikte kullanılabilir.

Daha fazla bilgi için, bkz. [belirli kuruluşlardan B2B kullanıcılarına Izin verme veya bu kullanıcıların davetlerini engelleme](../external-identities/allow-deny-list.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

2018 Nisan 'da, uygulama galerimize federasyon desteği olan bu 13 yeni uygulamayı ekledik:

Ölçüt HCM, [Fcalnote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (on-premises)](../saas-apps/secretserver-on-premises-tutorial.md), [dinamik sinyal](../saas-apps/dynamicsignal-tutorial.md), [mindwireless](../saas-apps/mindwireless-tutorial.md), [kuruluş şeması artık](../saas-apps/orgchartnow-tutorial.md), [ziflow](../saas-apps/ziflow-tutorial.md), [appneta performans izleyicisi](../saas-apps/appneta-tutorial.md), [elium](../saas-apps/elium-tutorial.md), [floxx Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), rafı, [SafetyNET](../saas-apps/safetynet-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md).

Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Azure AD 'de B2B kullanıcılarına şirket içi uygulamalarınıza erişim izni verme (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2B **ürün yeteneği:** B2B/B2C

Konuk kullanıcıları iş ortağı kuruluşlarınızdan Azure AD 'ye davet etmek için Azure Active Directory (Azure AD) B2B işbirliği özelliklerini kullanan bir kuruluş olarak, bu B2B kullanıcılarına şirket içi uygulamalara erişimi sağlayabilirsiniz. Bu şirket içi uygulamalar, SAML tabanlı kimlik doğrulaması veya tümleşik Windows kimlik doğrulaması (ıWA) kullanarak Kerberos kısıtlanmış temsili (KCD) kullanabilir.

Daha fazla bilgi için bkz. Şirket [içi uygulamalarınıza Azure AD erişimi IÇIN B2B kullanıcıları verme](../external-identities/hybrid-cloud-to-on-premises.md).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Azure Marketi 'nden SSO tümleştirme öğreticileri alın

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** diğer **ürün yeteneği:** üçüncü taraf tümleştirmesi

[Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) 'nde listelenen BIR uygulama SAML tabanlı çoklu oturum açmayı destekliyorsa, **Şimdi al** ' a tıklamak bu uygulamayla ilişkili tümleştirme öğreticisini sağlar.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>SaaS uygulamalarına Azure AD otomatik Kullanıcı sağlamasının daha hızlı performansı

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği: 3.** taraf tümleştirmesi

Daha önce, SaaS uygulamaları için Azure Active Directory Kullanıcı sağlama bağlayıcılarını kullanan müşteriler (örneğin, Salesforce, ServiceNow ve Box), Azure AD kiracılar 100.000 Birleşik Kullanıcı ve grup üzerinde yer alıyorsa ve hangi kullanıcıların sağlanması gerektiğini belirlemekte Kullanıcı ve Grup atamaları kullanıyorsa, yavaş performansla karşılaşabilir.

2 Nisan 2018 ' de, Azure AD sağlama hizmeti 'ne Azure Active Directory ve hedef SaaS uygulamaları arasında ilk eşitlemeleri gerçekleştirmek için gereken süreyi büyük ölçüde azaltan önemli performans geliştirmeleri dağıtıldı.

Sonuç olarak, çok sayıda gün veya hiç tamamlanmamış uygulamalar için ilk eşitlemeleri olan birçok müşteri artık birkaç dakika veya saat içinde tamamlanır.

Daha fazla bilgi için bkz. [sağlama sırasında ne olur?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Karma Azure AD 'ye katılmış makineler için Windows 10 kilit ekranından self servis parola sıfırlama

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** self servis parola sıfırlama **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Windows 10 SSPR özelliğini, karma Azure AD 'ye katılmış makineler için destek içerecek şekilde güncelleştirdik. Bu özellik Windows 10 ' da kullanılabilir RS4, kullanıcıların parolalarını bir Windows 10 makinesinin kilit ekranından sıfırlamasına izin verir. Self servis parola sıfırlama için etkin ve kayıtlı kullanıcılar bu özelliği kullanabilir.

Daha fazla bilgi için bkz. [oturum açma ekranından Azure AD parola sıfırlama](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>Mart 2018

### <a name="certificate-expire-notification"></a>Sertifika süre sonu bildirimi

**Şunu yazın:** Sabit **hizmet kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

Bir galeri veya Galeri dışı bir uygulama için bir sertifika sona ermek üzereyken Azure AD bir bildirim gönderir.

Bazı kullanıcılar SAML tabanlı çoklu oturum açma için yapılandırılmış kurumsal uygulamalar için bildirim almadı. Bu sorun çözüldü. Azure AD, 7, 30 ve 60 gün içinde süresi dolan sertifikaların bildirimini gönderir. Bu olayı denetim günlüklerinde görebilirsiniz.

Daha fazla bilgi için bkz.

- [Azure Active Directory 'da federe çoklu oturum açma için sertifikaları yönetme](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Azure Active Directory portalındaki denetim etkinliği raporları](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C Twitter ve GitHub kimlik sağlayıcıları

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** B2C-tüketici kimlik yönetimi **ürün yeteneği:** B2B/B2C

Artık, Azure AD B2C bir kimlik sağlayıcısı olarak Twitter veya GitHub ekleyebilirsiniz. Twitter, genel önizlemeden GA 'ye taşınıyor. GitHub, genel önizlemede kullanıma sunuldu.

Daha fazla bilgi için bkz. [Azure AD B2B işbirliği nedir?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>İOS ve Android için Azure AD uygulama tabanlı koşullu erişim ile Intune Managed Browser kullanarak tarayıcı erişimini kısıtlama

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği & koruması

**Şimdi genel önizlemede!**

**INTUNE Managed Browser SSO:** Çalışanlarınız yerel istemcilerde (Microsoft Outlook gibi) çoklu oturum açma ve tüm Azure AD ile bağlantılı uygulamalar için Intune Managed Browser kullanabilir.

**Intune Managed Browser koşullu erişim desteği:** Artık çalışanların, uygulama tabanlı koşullu erişim ilkeleri kullanarak Intune Managed Browser 'ı kullanmasını zorunlu kılabilirsiniz.

[Blog gönderimizde](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)bunun hakkında daha fazla bilgi edinin.

Daha fazla bilgi için bkz.

- [Uygulama tabanlı koşullu erişim kurulumu](../conditional-access/app-based-conditional-access.md)

- [Yönetilen tarayıcı ilkelerini yapılandırma](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>PowerShell GA modülünde uygulama proxy cmdlet 'Leri

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** Access Control

Uygulama proxy cmdlet 'leri için destek artık PowerShell GA modülünde! Bu, PowerShell modüllerinde güncelleşmenizi gerektirir; arkasında bir yıldan daha fazla olduğunuzda bazı cmdlet 'ler çalışmayı durdurabilir.

Daha fazla bilgi için bkz. [Azuread](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Office 365 yerel istemcileri etkileşimli olmayan bir protokol kullanılarak sorunsuz SSO tarafından desteklenir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Office 365 Native istemcileri kullanan kullanıcı (sürüm 16.0.8730. xxxx ve üzeri) sorunsuz SSO kullanarak sessiz bir oturum açma deneyimi alın. Bu destek, Azure AD 'ye etkileşimli olmayan bir protokol (WS-Trust) eklenerek sağlanır.

Daha fazla bilgi için bkz. [sorunsuz SSO ile yerel bir istemcide oturum açma nasıl çalışır?](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Uygulamalar, Azure AD 'nin kiracı uç noktalarına oturum açma istekleri gönderirse sorunsuz SSO ile sessiz oturum açma deneyimi alırlar.

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Kullanıcılar, sorunsuz SSO ile sessiz bir oturum açma deneyimi alırlar. bir uygulama (örneğin, `https://contoso.sharepoint.com` ) Azure AD 'nin kiracı uç noktalarına oturum açma istekleri gönderirse (örneğin, `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` Azure AD 'nin ortak uç noktası () yerine) `https://login.microsoftonline.com/common/<...>` .

Daha fazla bilgi için bkz. [kesintisiz çoklu oturum açma Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Sorunsuz SSO 'yu kullanıma almak için kullanıcıların Intranet bölgesi ayarlarına daha önce iki URL yerine yalnızca bir Azure AD URL 'SI eklemeniz gerekir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Kullanıcılarınıza sorunsuz SSO eklemek için, Active Directory ' de Grup İlkesi kullanarak kullanıcıların Intranet bölgesi ayarlarına yalnızca bir Azure AD URL 'SI eklemeniz gerekir: `https://autologon.microsoftazuread-sso.com` . Daha önce müşterilerin iki URL eklemesi gerekiyordu.

Daha fazla bilgi için bkz. [kesintisiz çoklu oturum açma Azure Active Directory](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Bu 15 Mart 2018 ' de, uygulama galerimize federasyon desteği olan bu 15 yeni uygulamayı ekledik:

[BoxCryptor](../saas-apps/boxcryptor-tutorial.md), [cylanceprotect](../saas-apps/cylanceprotect-tutorial.md), Wrike, [Signalfx](../saas-apps/signalfx-tutorial.md), Asstagenda, [yardione](../saas-apps/yardione-tutorial.md), vtiger CRM, ınkırpması, [genlik](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [Contractçalışma,](../saas-apps/contractworks-tutorial.md) [bersin](../saas-apps/bersin-tutorial.md), [mercell](../saas-apps/mercell-tutorial.md), [trısotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md).

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md).

Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Azure kaynakları için PıM genel kullanıma sunuldu

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Privileged Identity Management **ürün özelliği:** Privileged Identity Management

Dizin rolleri için Azure AD Privileged Identity Management kullanıyorsanız, artık, abonelik, kaynak grupları, sanal makineler ve Azure Resource Manager tarafından desteklenen diğer kaynaklar gibi Azure Kaynak rolleri için PıM 'nin saat bağlantılı erişim ve atama yeteneklerini kullanabilirsiniz. Tek seferde rolleri etkinleştirirken Multi-Factor Authentication uygulayın ve etkinleştirmeleri, onaylanan değişiklik pencereleri ile koordinasyon olarak zamanlayın. Buna ek olarak, bu sürüm, güncelleştirilmiş bir kullanıcı arabirimi, onay iş akışları ve rolleri yakında süresi dolacak şekilde genişletme ve süresi dolan rolleri yenileme özelliği dahil olmak üzere genel önizleme sırasında kullanılamayan geliştirmeler ekler.

Daha fazla bilgi için bkz. [Azure kaynakları Için PIM (Önizleme)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Uygulama belirteçlerinize Isteğe bağlı talepler ekleme (Genel Önizleme)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD uygulamanız artık JWTs veya SAML belirteçlerinde özel veya isteğe bağlı talepler talep edebilir.  Bunlar, boyut veya uygulanabilirlik kısıtlamaları nedeniyle, belirteçte varsayılan olarak bulunmayan Kullanıcı veya kiracı hakkında taleplerdir.  Bu, şu anda v 1.0 ve v 2.0 uç noktalarında Azure AD uygulamaları için genel önizlemededir.  Hangi taleplerin eklenebileceklerini ve bunları istemek için uygulama bildiriminizi nasıl düzenleyeceğini öğrenmek için belgelere bakın.

Daha fazla bilgi için bkz. [Azure AD 'de Isteğe bağlı talepler](../develop/active-directory-optional-claims.md).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD, daha güvenli OAuth akışları için PKI CE 'yi destekler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD docs, OAuth 2,0 yetkilendirme kodu verme akışı sırasında daha güvenli iletişim sağlayan PCE için destek olarak güncelleştirilmiştir.  V 1.0 ve v 2.0 uç noktalarında hem S256 hem de düz metin code_challenges desteklenir.

Daha fazla bilgi için bkz. [yetkilendirme kodu isteme](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Workday Get_Workers API 'sinde bulunan tüm kullanıcı özniteliği değerlerini sağlama desteği

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama sağlama **ürün yeteneği:** üçüncü taraf tümleştirmesi

Workday 'den Active Directory ve Azure AD 'ye gelen kaynak sağlamanın genel önizlemesi artık Workday Get_Workers API 'sinde bulunan tüm öznitelik değerlerini ayıklama ve sağlama özelliğini desteklemektedir. Bu, iş günü gelen sağlama bağlayıcısının ilk sürümü ile sevk edilen yüzlerce ek standart ve özel öznitelik için destek sağlar.

Daha fazla bilgi için bkz. [Workday kullanıcı özniteliklerinin listesini özelleştirme](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Grup üyeliğini dinamikten statik olarak değiştirme ve tam tersi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Grup Yönetimi **ürün yeteneği:** işbirliği

Bir grupta üyeliğin yönetilme şeklini değiştirmek mümkündür. Bu, sistemde aynı grup adını ve KIMLIĞINI tutmak istediğinizde yararlıdır, bu nedenle gruba yönelik varolan başvurular hala geçerlidir; Yeni bir grup oluşturmak bu başvuruların güncelleştirilmesini gerektirir.
Azure AD Yönetim merkezini bu işlevselliği destekleyecek şekilde güncelleştirdik. Artık müşteriler, mevcut grupları dinamik üyeliğinden atanan üyeliğe veya tam tersi yönde dönüştürebilir. Mevcut PowerShell cmdlet 'leri yine de kullanılabilir.

Daha fazla bilgi için bkz. [Azure Active Directory gruplar Için dinamik üyelik kuralları](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Sorunsuz SSO ile iyileştirilmiş oturum kapatma davranışı

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Daha önce, kullanıcılar Azure AD tarafından güvenli hale getirilmiş bir uygulamanın oturumunu kapatsa bile, bir Azure AD uygulamasına, etki alanına katılmış cihazlarından Corpnet dahilinde yeniden erişmeye çalıştıkları durumlarda sorunsuz SSO kullanılarak otomatik olarak oturum açabilirler. Bu değişiklik ile oturumu kapatma desteklenir.  Böylece kullanıcılar, sorunsuz SSO kullanılarak otomatik olarak oturum açmak yerine, kullanıcıların ile yeniden oturum açmasını sağlamak için aynı veya farklı Azure AD hesabını seçmesine olanak sağlar.

Daha fazla bilgi için bkz. [Azure Active Directory kesintisiz çoklu oturum açma](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>Uygulama proxy Bağlayıcısı Sürüm 1.5.402.0 yayınlandı

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** kimlik güvenliği & koruması

Bu bağlayıcı sürümü, Kasım ile kademeli olarak dışarı aktarılır. Bu yeni bağlayıcı sürümü aşağıdaki değişiklikleri içerir:

- Bağlayıcı artık alt etki alanı düzeyi yerine etki alanı düzeyi tanımlama bilgilerini ayarlıyor. Bu, daha yumuşak bir SSO deneyimi sağlar ve gereksiz kimlik doğrulama istemlerini önler.
- Yığın halinde kodlama istekleri desteği
- Geliştirilmiş bağlayıcı sistem durumu izleme
- Çeşitli hata düzeltmeleri ve kararlılık geliştirmeleri

Daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu bağlayıcıları anlama](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Şubat 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Kullanıcıları ve grupları yönetmek için geliştirilmiş gezinti

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** Dizin Yönetimi **ürün yeteneği:** Dizin

Kullanıcıları ve grupları yönetmeye yönelik gezinti deneyimi kolaylaştırılmıştır. Artık, silinen kullanıcılar listesine daha kolay erişim sağlamak için doğrudan dizine genel bakış ' ı tüm kullanıcılar listesine gidebilirsiniz. Ayrıca, Grup yönetimi ayarlarına daha kolay erişim sayesinde dizine genel bakış ' a doğrudan tüm grupların listesine gidebilirsiniz. Ayrıca, dizine genel bakış sayfasından bir Kullanıcı, Grup, kurumsal uygulama veya uygulama kaydı için arama yapabilirsiniz.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>21Vianet tarafından çalıştırılan Microsoft Azure oturum açma ve denetim raporlarının kullanılabilirliği (Azure Çin 21Vianet)

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure Stack **ürün yeteneği:** izleme & raporlama

Azure AD etkinlik günlüğü raporları artık 21Vianet (Azure Çin 21Vianet) örnekleri tarafından işletilen Microsoft Azure sunulmaktadır. Aşağıdaki günlükler dahil edilmiştir:

- **Oturum açma etkinliği günlükleri**  -kiracınızla ilişkili tüm oturum açma günlüklerini içerir.

- **Self servis parola denetim günlükleri** -tüm SSPR denetim günlüklerini içerir.

- **Dizin Yönetimi denetim günlükleri** -Kullanıcı yönetimi, uygulama yönetimi ve diğerleri gibi dizin yönetimiyle ilgili tüm denetim günlüklerini içerir.

Bu günlüklerle ortamınızın nasıl yaptığına ilişkin Öngörüler elde edebilirsiniz. Sağlanan verilerle:

- Uygulamalarınızın ve hizmetlerinizin kullanıcılarınız tarafından nasıl kullanıldığını saptayın.

- Kullanıcılarınızın işlerini halledmesini önlemeye yönelik sorunları giderin.

Bu raporların nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Active Directory raporlama](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Azure AD etkinlik raporlarını görüntülemek için "rapor okuyucu" rolünü (yönetici olmayan rol) kullanın

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** raporlama **ürün yeteneği:** izleme & raporlama

Yönetici olmayan rollerin Azure AD etkinlik günlüklerine erişimine sahip olmasını sağlamak için müşterilerin geri bildirimlerinden bir parçası olarak, "rapor okuyucu" rolünde olan kullanıcıların, Azure portal içinde oturum açma ve denetim etkinliğine ve Microsoft Graph API 'sini kullanmaya erişme yeteneğini etkinleştirdik.

Bu raporların nasıl kullanılacağı hakkında daha fazla bilgi için bkz. [Azure Active Directory raporlama](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Kullanıcı özniteliği ve Kullanıcı tanımlayıcısı olarak kullanılabilir EmployeeID talebi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** SSO

**ÇalışanNo** 'ı, kurumsal uygulama kullanıcı arabiriminden SAML tabanlı oturum açma uygulamalarında üye KULLANıCıLAR ve B2B konukları için Kullanıcı tanımlayıcısı ve kullanıcı özniteliği olarak yapılandırabilirsiniz.

Daha fazla bilgi için bkz. [Azure Active Directory 'de kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu joker karakter kullanarak Basitleştirilmiş uygulama yönetimi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Uygulama dağıtımını daha kolay hale getirmek ve yönetim yükünüzü azaltmak için artık joker karakterler kullanarak uygulama yayımlama özelliğini destekliyoruz. Bir joker uygulama yayımlamak için standart uygulama yayımlama akışını takip edebilir, ancak iç ve dış URL 'lerde bir joker karakter kullanabilirsiniz.

Daha fazla bilgi için [Azure Active Directory uygulama proxy 'Sinde joker uygulamalar](../manage-apps/application-proxy-wildcard.md) bölümüne bakın

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Uygulama proxy 'sinin yapılandırmasını desteklemeye yönelik yeni cmdlet 'ler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** platform

AzureAD PowerShell önizleme modülünün en son sürümü, müşterilerin PowerShell kullanarak uygulama proxy uygulamalarını yapılandırmasına izin veren yeni cmdlet 'ler içerir.

Yeni cmdlet 'ler şunlardır:

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

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Grupların yapılandırılmasını desteklemeye yönelik yeni cmdlet 'ler

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulama proxy 'si **ürün yeteneği:** platform

AzureAD PowerShell modülünün en son sürümü, Azure AD 'de grupları yönetmek için cmdlet 'ler içerir. Bu cmdlet 'ler daha önce AzureADPreview modülünde kullanılabilir ve artık AzureAD modülüne eklendi

Genel kullanılabilirlik için şimdi serbest olan grup cmdlet 'leri şunlardır:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Azure AD Connect yeni bir sürümü var

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** ad eşitleme **ürün yeteneği:** platform

Azure AD Connect, Windows Server Active Directory ve LDAP dahil olmak üzere Azure AD ile şirket içi veri kaynakları arasında veri eşitlemesi için tercih edilen bir araçtır.

>[!Important]
>Bu derleme şema ve eşitleme kuralı değişikliklerini tanıtır. Azure AD Connect eşitleme hizmeti bir yükseltmeden sonra tam Içeri aktarma ve tam eşitleme adımlarını tetikler. Bu davranışı değiştirme hakkında daha fazla bilgi için, [yükseltmeden sonra tam eşitlemenin ertelenmesi](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)bölümüne bakın.

Bu sürümde aşağıdaki güncelleştirmeler ve değişiklikler bulunur:

**Sorun düzeltildi**

- Sonraki sayfaya geçiş yaparken bölüm filtreleme sayfasına yönelik arka plan görevlerinde zamanlama penceresini düzeltir.

- ConfigDB özel eylemi sırasında erişim ihlaline neden olan bir hata düzeltildi.

- SQL bağlantı zaman aşımından kurtarmak için bir hata düzeltildi.

- SAN joker karakterlerinden oluşan sertifikaların ön REQ denetimi başarısız olduğu bir hata düzeltildi.

- Azure AD Bağlayıcısı dışarı aktarma sırasında miiserver.exe kilitlenmeye neden olan bir hata düzeltildi.

- Çalışırken DC 'de hatalı parola denemesinin oturum açtığı bir hata düzeltildi, bu da Azure AD Connect sihirbazının yapılandırmayı değiştirmesine neden oldu

**Yeni özellikler ve geliştirmeler**

- Uygulama telemetrisi-Yöneticiler bu veri sınıfını açabilir/kapatabilir.

- Azure AD sistem durumu verileri-Yöneticiler, sistem durumu ayarlarını denetlemek için sistem durumu portalını ziyaret etmelidir. Hizmet ilkesi değiştirildikten sonra aracılar bunu okur ve uygular.

- Sayfa başlatma için cihaz geri yazma yapılandırma eylemleri ve ilerleme çubuğu eklendi.

- ZIP-Text/HTML raporundaki HTML raporu ve tam veri koleksiyonuyla gelişmiş genel Tanılamalar.

- Otomatik yükseltmenin güvenilirliği geliştirildi ve sunucunun sistem durumunun belirlenebildiğinden emin olmak için ek telemetri eklendi.

- AD bağlayıcı hesabında ayrıcalıklı hesaplar için kullanılabilir izinleri kısıtla. Yeni yüklemeler için, sihirbaz, MSOL hesabını oluşturduktan sonra ayrıcalıklı hesapların MSOL hesabında sahip olduğu izinleri kısıtlar. Değişiklikler, otomatik oluşturma hesabıyla hızlı yüklemeleri ve özel yüklemeleri etkiler.

- Yükleyiciyi, AADConnect 'in temiz yüklemesi sırasında SA ayrıcalığı gerektirecek şekilde değiştirdi.

- Belirli bir nesne için eşitleme sorunlarını gidermeye yönelik yeni yardımcı program. Şu anda yardımcı program aşağıdaki şeyleri denetler:

    - Azure AD kiracısındaki eşitlenmiş Kullanıcı nesnesi ile Kullanıcı hesabı arasında UserPrincipalName uyumsuzluğu.

    - Nesne, etki alanı filtreleme nedeniyle eşitlemeden filtrelendiğinde

    - Nesne, kuruluş birimi (OU) filtrelemesi nedeniyle eşitlemeden filtrelendiğinde

- Belirli bir kullanıcı hesabı için şirket içi Active Directory depolanan geçerli parola karmasını eşitlemeye yönelik yeni yardımcı program. Yardımcı program parola değişikliği gerektirmez.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim ile kullanılmak üzere eklenen Intune Uygulama Koruması ilkelerini destekleyen uygulamalar

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği & koruması

Uygulama tabanlı koşullu erişimi destekleyen daha fazla uygulama ekledik. Artık, bu onaylanan istemci uygulamalarını kullanarak Office 365 ve diğer Azure AD bağlı bulut uygulamalarına erişebilirsiniz.

Aşağıdaki uygulamalar Şubat sonuna kadar eklenecektir:

- Microsoft Power BI

- Microsoft başlatıcısı

- Microsoft faturalandırma

Daha fazla bilgi için bkz.

- [Onaylanan istemci uygulaması gereksinimi](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD uygulama tabanlı koşullu erişim](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Mobil deneyimle Kullanım koşulları güncelleştirme

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** uyumluluk

Kullanım koşulları görüntülendiğinde artık sorun yaşamadan ' a tıklayabilirsiniz. **Buraya tıklayın**. Bu bağlantıya tıkladığınızda cihazınızdaki kullanım koşulları yerel olarak açılır. Belgedeki yazı tipi boyutundan veya cihazın ekran boyutundan bağımsız olarak, belgeyi gerektiği gibi yakınlaştırıp okuyabilirsiniz.

---

## <a name="january-2018"></a>Ocak 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları kullanılabilir

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Ocak 2018 ' de, uygulama galerisine federasyon desteği olan aşağıdaki yeni uygulamalar eklenmiştir:

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md), [Onetrust gizlilik yönetimi yazılımı](../saas-apps/onetrust-tutorial.md), [Satıcıyolu](../saas-apps/dealpath-tutorial.md), [ıriusrisfedermi federe dizini ve [uygunlukta netavantajlar](../saas-apps/fidelitynetbenefits-tutorial.md).

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md).

Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="sign-in-with-additional-risk-detected"></a>Algılanan ek risk ile oturum açın

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği & koruması

Algılanan bir risk algılaması için aldığınız Öngörüler, Azure AD aboneliğinize bağlıdır. Azure AD Premium P2 sürümüyle, temeldeki Tüm algılamalar hakkında en ayrıntılı bilgileri alırsınız.

Azure AD Premium P1 sürümü sayesinde, lisansınız kapsamında olmayan algılamalar, risk algılama tarafından algılanan ek risklerle birlikte oturum açma olarak görünür.

Daha fazla bilgi için bkz. [risk algılamalarını Azure Active Directory](../identity-protection/overview-identity-protection.md).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365 uygulamalarını son kullanıcının erişim panellerinden gizle

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulamalarım **ürün yeteneği:** SSO

Artık, Office 365 uygulamalarının yeni bir Kullanıcı ayarı aracılığıyla kullanıcının erişim panellerinde nasıl gösterileceğini daha iyi yönetebilirsiniz. Bu seçenek, yalnızca Office portalında Office uygulamalarını göstermek istiyorsanız kullanıcının erişim panellerindeki uygulama sayısını azaltmak için yararlıdır. Bu ayar **Kullanıcı ayarlarında** bulunur ve etiketlidir, **kullanıcılar yalnızca Office 365 portalında Office 365 uygulamalarını görebilirler**.

Daha fazla bilgi için bkz. [Azure Active Directory kullanıcının deneyiminden bir uygulamayı gizleme](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Parola SSO 'SU doğrudan uygulamanın URL 'sinden etkin olan uygulamalarda sorunsuz oturum açma

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulamalarım **ürün yeteneği:** SSO

Uygulamalarım tarayıcı uzantısı, artık uygulamalarımın tek oturum açma özelliğini tarayıcınızda bir kısayol olarak sunan kullanışlı bir araç aracılığıyla kullanıma sunulmuştur. ' İ yükledikten sonra, kullanıcıların tarayıcılarında uygulamalara hızlı erişim sağlayan bir waffle simgesi görürsünüz. Kullanıcılar artık şu özelliklerden yararlanabilir:

- Uygulamanın oturum açma sayfasından parola SSO tabanlı uygulamalarda doğrudan oturum açma özelliği
- Hızlı arama özelliğini kullanarak herhangi bir uygulamayı başlatma
- Uzantıdan son kullanılan uygulamalar için kısayollar
- Uzantı, Microsoft Edge, Chrome ve Firefox için kullanılabilir.

Daha fazla bilgi için bkz. [uygulamalarımın güvenli oturum açma uzantısı](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Klasik Azure Portalı Azure AD yönetim deneyimi kullanımdan kaldırılmıştır

**Şunu yazın:** Kullanım dışı **hizmet kategorisi:** Azure AD **ürün yeteneği:** Dizin

8 Ocak 2018 itibariyle, klasik Azure portalındaki Azure AD yönetim deneyimi kullanımdan kaldırılmıştır. Bu, klasik Azure portalının kullanımdan kaldırılması ile birlikte gerçekleşir. Gelecekte Azure AD 'nin tüm portal tabanlı yönetimi için [Azure AD Yönetim merkezini](https://aad.portal.azure.com) kullanmanız gerekir.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor Web portalı kullanımdan kaldırılmıştır

**Şunu yazın:** Kullanım dışı **hizmet kategorisi:** Azure AD **ürün yeteneği:** Dizin

8 Ocak 2018 itibariyle PhoneFactor Web portalı kullanımdan kaldırılmıştır. Bu portal MFA sunucusu 'nun yönetimi için kullanılmıştır, ancak bu işlevler portal.azure.com adresinde Azure portal taşınmıştır.

MFA yapılandırması şurada bulunur: **Azure Active Directory \> MFA sunucusu**

---

### <a name="deprecate-azure-ad-reports"></a>Azure AD raporlarını kullanımdan kaldırma

**Şunu yazın:** Kullanım dışı **hizmet kategorisi:** raporlama **ürün yeteneği:** kimlik yaşam döngüsü yönetimi


Yeni Azure Active Directory Yönetim konsolunun genel kullanıma sunulduğuna ve yeni API 'lerin hem etkinlik hem de güvenlik raporlarında kullanılabilir olması sayesinde, "/Reports" uç noktasındaki rapor API 'Leri 31 Aralık 2017 ' in sonunda kullanımdan kaldırılmıştır.

**Neler mevcuttur?**

Yeni Yönetici konsoluna geçişin bir parçası olarak, Azure AD etkinlik günlüklerini almak için 2 yeni API 'si yaptık. Yeni API 'Ler kümesi, daha zengin denetim ve oturum açma etkinlikleri sağlamaya ek olarak daha zengin filtreleme ve sıralama işlevselliği sağlar. Daha önce güvenlik raporları üzerinden kullanılabilen verilere artık Microsoft Graph kimlik koruması risk algılamaları API 'SI aracılığıyla erişilebilir.

Daha fazla bilgi için bkz.

- [Azure Active Directory Reporting API 'SI ile çalışmaya başlama](../reports-monitoring/concept-reporting-api.md)

- [Azure Active Directory Kimlik Koruması ve Microsoft Graph kullanmaya başlama](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Aralık 2017

### <a name="terms-of-use-in-the-access-panel"></a>Erişim panelinde Kullanım koşulları

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** uyumluluk

Artık erişim paneline gidebilir ve önceden kabul ettiğiniz kullanım koşullarını görüntüleyebilirsiniz.

Şu adımları izleyin:

1. [Uygulamaps portalına](https://myapps.microsoft.com)gidin ve oturum açın.

2. Sağ üst köşede adınızı seçin ve ardından listeden **profil** ' i seçin.

3. **Profilinizde** **kullanım koşullarını gözden geçir**' i seçin.

4. Artık kabul ettiğiniz kullanım koşullarını gözden geçirebilirsiniz.

Daha fazla bilgi için bkz. [Azure AD kullanım koşulları özelliği (Önizleme)](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Yeni Azure AD oturum açma deneyimi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD ve Microsoft hesabı Identity System U, tutarlı bir görünüme sahip olacak şekilde yeniden tasarlanmıştır. Ayrıca, Azure AD oturum açma sayfası önce Kullanıcı adını, ardından ikinci ekranda kimlik bilgisini toplar.

Daha fazla bilgi için bkz. [yeni Azure AD oturum açma deneyimi artık genel önizlemede](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Daha az oturum açma istemi: Azure AD oturum açma için yeni bir "Oturumumu Açık tut" deneyimi

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD oturum açma sayfasındaki Oturumumu Açık **bırak** onay kutusu, başarıyla kimlik doğrulamasından geçtikten sonra görüntülenen yeni bir istem ile değiştirilmiştir.

Bu istem için **Evet** 'e yanıt verirseniz, hizmet sizi kalıcı yenileme belirteci sağlar. Bu davranış, eski deneyimdeki Oturumumu **açık tut** onay kutusunu seçtiğiniz durumla aynıdır. Federal kiracılar için bu istem, Federasyon Hizmeti ile başarıyla kimlik doğrulamasından geçtikten sonra görüntülenir.

Daha fazla bilgi için bkz. daha [az oturum açma istemi: Azure AD için yeni "Oturumumu Açık tut" deneyimi önizlemededir](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Kullanım koşullarının kabul edilmeden önce genişletilmesi için yapılandırma ekleyin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** uyumluluk

Yöneticiler için bir seçenek, kullanıcıların koşulları kabul etmeden önce kullanım koşullarını genişletmelerini gerektirir.

Kullanıcıların kullanım koşullarını genişletmesini gerektirmek için **Açık** veya **kapalı** seçeneklerinden birini belirleyin. **Açık** ayarı, kullanıcıların kabul etmeden önce kullanım koşullarını görüntülemesini gerektirir.

Daha fazla bilgi için bkz. [Azure AD kullanım koşulları özelliği (Önizleme)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Uygun rol atamaları için kapsamlı etkinleştirme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Privileged Identity Management **ürün özelliği:** Privileged Identity Management

Varsayılan olarak orijinal atama varsayılanlarından daha az bağımsız çalışma sınırı sahip olan uygun Azure Kaynak rolü atamalarını etkinleştirmek için kapsamlı etkinleştirme kullanabilirsiniz. Kiracınızda aboneliğin sahibi olarak atandıysanız örnek bir örnektir. Kapsamlı etkinleştirme sayesinde, abonelik içinde (kaynak grupları ve sanal makineler gibi) bulunan en fazla beş kaynak için sahip rolünü etkinleştirebilirsiniz. Etkinleştirmenin kapsamını belirlemek, önemli Azure kaynaklarında istenmeyen değişiklikler yürütme olasılığını azaltabilir.

Daha fazla bilgi için bkz. [Azure AD Privileged Identity Management nedir?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD uygulama galerisinde yeni Federasyon uygulamaları

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kurumsal uygulamalar **ürün yeteneği:** üçüncü taraf tümleştirmesi

Bu yeni uygulamaları, Aralık 2017 ' de, uygulama galerimize federasyon desteğiyle ekledik:

[Accanble](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital storefront](../saas-apps/efidigitalstorefront-tutorial.md), [communifire](../saas-apps/communifire-tutorial.md) cybsafe, [factu](../saas-apps/factset-tutorial.md), [Image çalışma,](../saas-apps/imageworks-tutorial.md) [mobi](../saas-apps/mobi-tutorial.md), [Mobileiron Azure AD tümleştirmesi](../saas-apps/mobileiron-tutorial.md), yansıma [ktive](../saas-apps/reflektive-tutorial.md), [tarafından Bamboo için](../saas-apps/bamboo-tutorial.md) [SAML SSO](../saas-apps/bitbucket-tutorial.md) [](../saas-apps/vodeclic-tutorial.md)

Uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Active Directory SaaS uygulama tümleştirmesi](../saas-apps/tutorial-list.md).

Uygulamanızı Azure AD uygulama galerisinde listeleme hakkında daha fazla bilgi için, [Azure Active Directory Uygulama galerisinde uygulamanızı](../develop/v2-howto-app-gallery-listing.md)listeleme bölümüne bakın.

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD dizin rolleri için onay iş akışları

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Privileged Identity Management **ürün yeteneği:** Privileged Identity Management

Azure AD dizin rolleri için onay iş akışı genel kullanıma sunulmuştur.

Onay iş akışı ile ayrıcalıklı rol yöneticileri, ayrıcalıklı rolü kullanabilmeniz için, uygun rol üyelerinin rol etkinleştirme istemesi için gerekli olabilir. Birden çok kullanıcı ve gruba, onay sorumlulukları atanabilir. Uygun rol üyeleri onay tamamlandığında ve rolleri etkin olduğunda bildirim alır.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Geçişli kimlik doğrulaması: Skype Kurumsal desteği

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik doğrulamaları (oturum açmalar) **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Doğrudan kimlik doğrulaması artık, çevrimiçi ve karma topolojilerini içeren modern kimlik doğrulamayı destekleyen Skype Kurumsal istemci uygulamalarına yönelik kullanıcı oturum açma işlemlerini destekler.

Daha fazla bilgi için bkz. [modern kimlik doğrulamasıyla desteklenen Skype Kurumsal topolojileri](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC için Azure AD Privileged Identity Management güncelleştirmeleri (Önizleme)

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Privileged Identity Management **ürün yeteneği:** Privileged Identity Management

Azure rol tabanlı erişim denetimi (Azure RBAC) için Azure AD Privileged Identity Management (PıM) genel önizleme yenilemesinde, artık şunları yapabilirsiniz:

* Yalnızca yeterli yönetimi kullanın.
* Kaynak rollerini etkinleştirmek için onay gerektir.
* Hem Azure AD hem de Azure rolleri için onay gerektiren bir rolün daha sonra etkinleştirilmesini zamanlayın.

Daha fazla bilgi için bkz. [Azure kaynakları için Privileged Identity Management (Önizleme)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>Kasım 2017

### <a name="access-control-service-retirement"></a>Access Control hizmeti kullanımdan kaldırma

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** Access Control hizmeti **ürün özelliği:** Access Control hizmet

Azure Active Directory Access Control (Access Control hizmeti olarak da bilinir), geç 2018 ' de kullanımdan kaldırılacaktır. Sonraki birkaç hafta içinde ayrıntılı bir zamanlamayı ve üst düzey geçiş kılavuzunu içeren daha fazla bilgi sağlanacaktır. Bu sayfadaki açıklamaları Access Control hizmetiyle ilgili herhangi bir soru ile bırakabilir ve bir takım üyesi onları yanıtlayabilir.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Intune Managed Browser tarayıcı erişimini kısıtlama

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** koşullu erişim **ürün yeteneği:** kimlik güvenliği ve koruma

Onaylanan bir uygulama olarak Intune Managed Browser kullanarak Office 365 ve diğer Azure AD bağlı bulut uygulamalarına tarayıcı erişimini kısıtlayabilirsiniz.

Artık uygulama tabanlı koşullu erişim için aşağıdaki koşulu yapılandırabilirsiniz:

**İstemci uygulamaları:** Tarayıcı

**Değişikliğin etkisi nedir?**

Bugün, bu durumu kullandığınızda erişim engellenir. Önizleme kullanılabilir olduğunda, tüm erişimlerle yönetilen tarayıcı uygulamasının kullanılması gerekir.

Yaklaşan blogların ve sürüm notlarındaki bu özelliği ve daha fazla bilgiyi bulun.

Daha fazla bilgi için bkz. [Azure AD 'de koşullu erişim](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim için yeni onaylanan istemci uygulamaları

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** koşullu erişim **ürün yeteneği:** kimlik güvenliği ve koruma

Aşağıdaki uygulamalar [onaylanan istemci uygulamaları](../conditional-access/concept-conditional-access-conditions.md#client-apps)listesidir:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Daha fazla bilgi için bkz.

- [Onaylanan istemci uygulaması gereksinimi](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD uygulama tabanlı koşullu erişim](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Birden çok dil için kullanım koşulları desteği

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** uyumluluk

Yöneticiler, artık birden çok PDF belgesi içeren yeni kullanım koşulları oluşturabilir. Bu PDF belgelerini karşılık gelen bir dille etiketlendirebilirsiniz. Kullanıcılar, tercihleri temelinde, eşleşen dille PDF gösterilir. Eşleşme yoksa, varsayılan dil gösterilir.

---

### <a name="real-time-password-writeback-client-status"></a>Gerçek zamanlı parola geri yazma istemci durumu

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** self servis parola sıfırlama **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Artık şirket içi parola geri yazma istemcinizdeki durumu gözden geçirebilirsiniz. Bu seçenek, [parola sıfırlama](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) sayfasının **Şirket içi tümleştirme** bölümünde bulunur.

Şirket içi geri yazma istemcinizdeki bağlantınız ile ilgili sorunlar varsa şunları sağlayan bir hata iletisi görürsünüz:

- Şirket içi geri yazma istemcinizle ilgili olarak neden bağlanamadığına ilişkin bilgiler.
- Sorunu çözmenize yardımcı olacak belge bağlantısı.

Daha fazla bilgi için bkz. [Şirket içi tümleştirme](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Azure AD **ürün yeteneği:** kimlik güvenliği ve koruması

Artık Office 365 ve diğer Azure AD bağlı bulut uygulamalarına erişimi, [Azure AD uygulama tabanlı koşullu erişim](../conditional-access/app-based-conditional-access.md)kullanarak Intune uygulama koruma ilkelerini destekleyen [onaylanan istemci uygulamalarına](../conditional-access/concept-conditional-access-conditions.md#client-apps) kısıtlayabilirsiniz. Intune uygulama koruma ilkeleri, bu istemci uygulamalarında şirket verilerini yapılandırmak ve korumak için kullanılır.

[Uygulama tabanlı](../conditional-access/app-based-conditional-access.md) [cihaz tabanlı](../conditional-access/require-managed-devices.md) koşullu erişim ilkeleriyle, kişisel ve şirket cihazlarındaki verileri koruma esnekliğine sahip olursunuz.

Aşağıdaki koşullar ve denetimler artık uygulama tabanlı koşullu erişim ile birlikte kullanılabilir:

**Desteklenen platform koşulu**

- iOS
- Android

**İstemci uygulamaları koşulu**

- Mobil uygulamalar ve Masaüstü istemcileri

**Erişim denetimi**

- Onaylanan istemci uygulaması gerektir

Daha fazla bilgi için bkz. [Azure AD uygulama tabanlı koşullu erişim](../conditional-access/app-based-conditional-access.md).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure portal Azure AD cihazlarını yönetme

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** cihaz kaydı ve Yönetim **ürün yeteneği:** kimlik güvenliği ve koruması

Artık Azure AD 'ye bağlı tüm cihazlarınızı ve cihazla ilgili etkinlikleri tek bir yerde bulabilirsiniz. Azure portal tüm cihaz kimliklerinizi ve ayarlarını yönetmek için yeni bir yönetim deneyimi vardır. Bu sürümde şunları yapabilirsiniz:

- Azure AD 'de koşullu erişim için kullanılabilen tüm cihazlarınızı görüntüleyin.
- Karma Azure AD 'ye katılmış cihazlarınızı içeren özellikleri görüntüleyin.
- Azure AD 'ye katılmış cihazlarınız için BitLocker anahtarları bulun, cihazınızı Intune ile yönetin ve daha fazlasını yapın.
- Azure AD cihazla ilgili ayarları yönetin.

Daha fazla bilgi için [Azure Portal kullanarak cihazları yönetme](../devices/device-management-azure-portal.md)konusuna bakın.

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD koşullu erişim için bir cihaz platformu olarak macOS desteği

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği ve koruması

Artık macOS 'ı Azure AD koşullu erişim ilkenize cihaz platformu olarak dahil edebilir (veya dışlayabilirsiniz). MacOS 'un desteklenen cihaz platformlarına eklenmesi sayesinde şunları yapabilirsiniz:

- **Intune kullanarak macOS cihazlarını kaydedin ve yönetin.** İOS ve Android gibi diğer platformlara benzer şekilde, macOS 'un birleştirilmiş kayıtları yapması için bir şirket portalı uygulaması vardır. MacOS için yeni şirket portalı uygulamasını kullanarak bir cihazı Intune 'a kaydedebilir ve Azure AD 'ye kaydedebilirsiniz.
- **MacOS cihazlarının, kuruluşunuzun Intune 'da tanımlanan uyumluluk ilkelerine bağlı olduğundan emin olun.** Azure portal Intune 'da, artık macOS cihazları için uyumluluk ilkeleri ayarlayabilirsiniz.
- **Azure AD 'deki uygulamalara erişimi yalnızca uyumlu macOS cihazlarına kısıtlayın.** Koşullu erişim ilkesi yazma, macOS öğesini ayrı bir cihaz platformu seçeneği olarak içerir. Artık Azure 'da ayarlanan hedeflenen uygulama için macOS 'a özgü koşullu erişim ilkelerini yazabilirsiniz.

Daha fazla bilgi için bkz.

- [Intune ile macOS cihazları için cihaz uyumluluğu ilkesi oluşturma](/mem/intune/protect/compliance-policy-create-mac-os)
- [Azure AD 'de koşullu erişim](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication için ağ Ilkesi sunucusu uzantısı

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:**  Multi-Factor Authentication **ürün yeteneği:** Kullanıcı kimlik doğrulaması

Azure AD Multi-Factor Authentication için ağ Ilkesi sunucusu uzantısı, mevcut sunucularınızı kullanarak kimlik doğrulama altyapınıza bulut tabanlı Multi-Factor Authentication özellikleri ekler. Ağ Ilkesi sunucusu uzantısıyla, mevcut kimlik doğrulama akışınıza telefon araması, SMS mesajı veya telefon uygulaması doğrulaması ekleyebilirsiniz. Yeni sunucuları yüklemek, yapılandırmak ve bakımını yapmak zorunda değilsiniz.

Bu uzantı, Azure Multi-Factor Authentication Sunucusu dağıtımı yapmadan sanal özel ağ bağlantılarını korumak isteyen kuruluşlar için oluşturulmuştur. Ağ Ilkesi sunucu uzantısı, federal veya eşitlenmiş kullanıcılar için ikinci bir kimlik doğrulama faktörü sağlamak üzere RADIUS ve bulut tabanlı Azure AD Multi-Factor Authentication arasında bir bağdaştırıcı görevi görür.

Daha fazla bilgi için bkz. [mevcut ağ Ilkesi sunucusu altyapınızı Azure AD Multi-Factor Authentication tümleştirme](../authentication/howto-mfa-nps-extension.md).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Silinen kullanıcıları geri yükleme veya kalıcı olarak kaldırma

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Kullanıcı Yönetimi **ürün yeteneği:** Dizin

Azure AD Yönetim merkezinde artık şunları yapabilirsiniz:

- Silinen bir kullanıcıyı geri yükleyin.
- Bir kullanıcıyı kalıcı olarak silme.

**Denemek için:**

1. Azure AD Yönetim merkezinde **Yönet** bölümündeki [tüm kullanıcılar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) ' ı seçin.

2. **Göster** listesinden, **son silinen kullanıcılar**' ı seçin.

3. Son silinen bir veya daha fazla kullanıcı seçin ve ardından onları geri yükleyin veya kalıcı olarak silin.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD uygulama tabanlı koşullu erişim için yeni onaylanan istemci uygulamaları

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği ve koruma

[Onaylanan istemci uygulamaları](../conditional-access/concept-conditional-access-conditions.md#client-apps)listesine aşağıdaki uygulamalar eklenmiştir:

- Microsoft Planner
- Azure Information Protection

Daha fazla bilgi için bkz.

- [Onaylanan istemci uygulaması gereksinimi](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD uygulama tabanlı koşullu erişim](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Koşullu erişim ilkesindeki denetimler arasında "OR" kullanın

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** koşullu erişim **ürün yeteneği:** kimlik güvenliği ve koruma

Koşullu erişim denetimleri için artık "veya" (seçili denetimlerden birini gerektir) kullanabilirsiniz. Bu özelliği, erişim denetimleri arasında "veya" ile ilke oluşturmak için kullanabilirsiniz. Örneğin, bir kullanıcının uyumlu bir cihazda olması için Multi-Factor Authentication "veya" kullanarak oturum açmasını gerektiren bir ilke oluşturmak için bu özelliği kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure AD koşullu erişim 'Deki denetimler](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Gerçek zamanlı risk algılamalarını toplama

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** kimlik koruması **ürün yeteneği:** kimlik güvenliği ve koruması

Azure AD Kimlik Koruması, belirli bir gündeki aynı IP adresinden kaynaklanan tüm gerçek zamanlı risk algılamaları artık her risk algılama türü için toplanır. Bu değişiklik, Kullanıcı güvenliğine hiçbir değişiklik yapılmadan gösterilen risk algılamaları hacminin sınırlarını sınırlar.

Temeldeki gerçek zamanlı algılama, kullanıcının her oturum açışında işe yarar. Multi-Factor Authentication veya erişimi engellemek üzere ayarlanmış bir oturum açma riski güvenlik ilkesi varsa, bu, her riskli oturum açma işlemi sırasında tetiklenir.

---

## <a name="october-2017"></a>Ekim 2017

### <a name="deprecate-azure-ad-reports"></a>Azure AD raporlarını kullanımdan kaldırma

**Şunu yazın:** Değişiklik **hizmeti kategorisini planlayın:** raporlama **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

Azure portal şunları sağlar:

- Yeni bir Azure AD Yönetim Konsolu.
- Etkinlik ve güvenlik raporları için yeni API 'Ler.

Bu yeni yetenekler nedeniyle,/Reports uç noktasındaki rapor API 'Leri 10 Aralık 2017 ' de kullanımdan kalkmıştı.

---

### <a name="automatic-sign-in-field-detection"></a>Otomatik oturum açma alanı algılama

**Şunu yazın:** Sabit **hizmet kategorisi:** uygulamalarım **ürün yeteneği:** çoklu oturum açma

Azure AD, bir HTML Kullanıcı adı ve parola alanı işleyen uygulamalar için otomatik oturum açma alanı algılamayı destekler. Bu adımlar, [bir uygulama için oturum açma alanlarını otomatik olarak yakalama](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app)bölümünde belgelenmiştir. Bu özelliği, [Azure Portal](https://aad.portal.azure.com) **Enterprise Applications** sayfasında *Galeri dışı* bir uygulama ekleyerek bulabilirsiniz. Ayrıca, bu yeni uygulamadaki **Çoklu oturum** açma modunu **parola tabanlı çoklu oturum açma** için yapılandırabilir, bir Web URL 'si girebilir ve sonra sayfayı kaydedebilirsiniz.

Bir hizmet sorunu nedeniyle, bu işlev geçici olarak devre dışı bırakıldı. Sorun çözüldü ve otomatik oturum açma alanı algılaması yeniden kullanılabilir.

---

### <a name="new-multi-factor-authentication-features"></a>Yeni Multi-Factor Authentication özellikleri

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Multi-Factor Authentication **ürün yeteneği:** kimlik güvenliği ve koruması

Multi-Factor Authentication (MFA), kuruluşunuzu korumanın önemli bir parçasıdır. Kimlik bilgilerini daha uyumlu hale getirmek ve deneyimi daha sorunsuz hale getirmek için aşağıdaki özellikler eklenmiştir:

- Multi-Factor Challenge sonuçları doğrudan Azure AD oturum açma raporuyla tümleşiktir ve bu da MFA sonuçlarına programlı erişim içerir.
- MFA yapılandırması, Azure portal Azure AD yapılandırma deneyimiyle daha ayrıntılı bir şekilde tümleştirilir.

Bu genel önizleme sayesinde MFA yönetimi ve raporlama, çekirdek Azure AD yapılandırma deneyiminin tümleşik bir parçasıdır. Artık MFA yönetim portalı işlevini Azure AD deneyiminden yönetebilirsiniz.

Daha fazla bilgi için bkz. [Azure Portal MFA raporlaması Için başvuru](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Kullanım koşulları

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** kullanım koşulları **ürün yeteneği:** uyumluluk

Kullanıcılara yasal veya uyumluluk gereksinimleri için ilgili bildirimler gibi bilgileri sunmak için Azure AD kullanım koşulları 'nı kullanabilirsiniz.

Azure AD kullanım koşulları 'nı aşağıdaki senaryolarda kullanabilirsiniz:

- Kuruluşunuzdaki tüm kullanıcılar için genel kullanım koşulları
- Kullanıcının özniteliklerine dayalı belirli kullanım koşulları (örneğin, doktorlarla veya yerel ve uluslararası çalışanlar, dinamik gruplar tarafından yapılır)
- Salesforce gibi yüksek etkili iş uygulamalarına erişmek için belirli kullanım koşulları

Daha fazla bilgi için bkz. [Azure AD kullanım koşulları](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management geliştirmeleri

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** Privileged Identity Management **ürün özelliği:** Privileged Identity Management

Azure AD Privileged Identity Management, kuruluşunuzda Azure kaynaklarına (Önizleme) erişimi yönetebilir, denetleyebilir ve izleyebilirsiniz:

- Abonelikler
- Kaynak grupları
- Sanal makineler

Azure RBAC işlevselliğini kullanan Azure portal tüm kaynaklar, Azure AD Privileged Identity Management sunabileceği tüm güvenlik ve yaşam döngüsü yönetimi yetilerinden yararlanabilir.

Daha fazla bilgi için bkz. [Azure kaynakları için Privileged Identity Management](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="access-reviews"></a>Erişim gözden geçirmeleri

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** erişim gözden geçirmeleri **ürün yeteneği:** uyumluluk

Kuruluşlar, grup üyeliklerini verimli bir şekilde yönetmek ve kurumsal uygulamalara erişmek için erişim gözden geçirmeleri (Önizleme) kullanabilir:

- Konuk kullanıcıların uygulamalara ve grup üyeliklerine erişimlerine ait erişim gözden geçirmelerini kullanarak bu kullanıcıların erişimini yeniden onaylayabilirsiniz. Gözden geçirenler, erişim gözden geçirmeleri tarafından belirtilen Öngörüler temelinde konuklara erişime izin verip vermeyeceğine karar verebilir.
- Erişim gözden geçirmeleri ile çalışanların uygulamalara erişimini ve grup üyeliklerini yeniden onaylayabilirsiniz.

Erişim gözden geçirmesi denetimlerini kuruluşunuza uygun programlarda toplayarak, uyumluluk veya riske duyarlı uygulamalar için gözden geçirmeleri takip edebilirsiniz.

Daha fazla bilgi için bkz. [Azure AD erişim İncelemeleri](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Uygulamalarım ve Office 365 uygulama başlatıcısı 'ndan üçüncü taraf uygulamaları gizleyin

**Şunu yazın:** Yeni özellik **hizmeti kategorisi:** uygulamalarım **ürün yeteneği:** çoklu oturum açma

Şimdi, yeni bir **uygulama Gizle** özelliği aracılığıyla kullanıcılarınızın portallarında görüntülenen uygulamaları daha iyi yönetebilirsiniz. Uygulama kutucuklarının arka uç hizmetleri veya yinelenen kutucuklar ve en yüksek kullanıcıların uygulama başlatanlar için göstereceği durumlarda yardımcı olması için uygulamaları gizleyebilirsiniz. Geçiş, üçüncü taraf uygulamanın **Özellikler** bölümüdür ve **Kullanıcı tarafından görülebilir** olarak etiketlenir. Ayrıca, bir uygulamayı PowerShell aracılığıyla programlı bir şekilde gizleyebilirsiniz.

Daha fazla bilgi için bkz. [Azure AD 'de kullanıcının deneyiminden üçüncü taraf bir uygulamayı gizleme](../manage-apps/hide-application-from-user-portal.md).


**Neler mevcuttur?**

 Yeni Yönetici konsoluna geçişin bir parçası olarak, Azure AD etkinlik günlüklerini almak için iki yeni API kullanılabilir. Yeni API 'Ler kümesi, daha zengin denetim ve oturum açma etkinlikleri sağlamaya ek olarak daha zengin filtreleme ve sıralama işlevselliği sağlar. Daha önce güvenlik raporları üzerinden kullanılabilen verilere artık Microsoft Graph kimlik koruması risk algılamaları API 'SI aracılığıyla erişilebilir.


## <a name="september-2017"></a>Eylül 2017

### <a name="hotfix-for-identity-manager"></a>Identity Manager düzeltmesi

**Şunu yazın:** Değiştirilen özellik **hizmeti kategorisi:** Identity Manager **ürün yeteneği:** kimlik yaşam döngüsü yönetimi

Kimlik Yöneticisi 2016 Service Pack 1 ' den itibaren, 25 Eylül 2017 itibariyle bir düzeltme toplaması paketi (Build 4.4.1642.0) kullanılabilir. Bu toplama paketi:

- Sorunları çözer ve iyileştirmeler ekler.
- , Identity Manager 2016 için 4.4.1459.0 derlemesi için tüm Identity Manager 2016 hizmet paketi 1 güncelleştirmelerini değiştiren bir toplu güncelleştirmedir.
- Identity Manager 2016 derleme 4.4.1302.0 sahip olmanızı gerektirir.

Daha fazla bilgi için bkz. [Identity Manager 2016 Service Pack 1 Için düzeltme paketi paketi (Build 4.4.1642.0) kullanılabilir](https://support.microsoft.com/help/4021562).

---
