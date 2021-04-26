---
title: Azure AD dağıtımı denetim listesi
description: Azure Active Directory özellik dağıtımı denetim listesi
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d1e5b8ac6492b0b1d819431e4616d32a092cc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836929"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Azure Active Directory özellik dağıtım kılavuzu

Kuruluşunuz için Azure Active Directory (Azure AD) dağıtımı yapabilir ve güvenli bir şekilde koruyabilirsiniz. Bu makalede, müşterilerin güvenlik duruşunu iyileştirmek için 30, 60, 90 gün veya daha fazla aşamada, müşterilerin evrelerde tamamlaması faydalı olan ortak görevleri tanımlanmaktadır. Azure AD 'yi zaten dağıtmış olan kuruluşlar bile bu kılavuzu kullanarak yatırımlarından en iyi şekilde yararlanmanızı sağlar.

İyi planlanmış ve yürütülen bir kimlik altyapısı, yalnızca bilinen kullanıcılar ve cihazlar tarafından üretkenlik iş yüklerinize ve verilerinize güvenli erişim olanağı sağlar.

Ayrıca müşteriler, Microsoft 'un en iyi uygulamalarına nasıl hizalanacağını öğrenmek için kendi [kimlik güvenli Puanını](identity-secure-score.md) denetleyebilir. Sektörünüz ve boyutlarınızın diğer kuruluşlarındaki diğer kuruluşlara kıyasla ne kadar iyi işlem yaptığınızı görmek için bu önerileri uyguladıktan önce ve sonra güvenli puanınızı kontrol edin.

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzdaki önerilerin birçoğu Azure AD Ücretsiz veya hiçbir lisans olmadan uygulanabilir. Lisansların gerekli olduğu durumlar, görevi gerçekleştirmek için en düşük düzeyde lisansın gerekli olduğu durumdur.

Lisanslama hakkında ek bilgiler aşağıdaki sayfalarda bulunabilir:

* [Azure AD lisanslama](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Kurumsal](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Azure AD dış kimlikleri fiyatlandırması](../external-identities/external-identities-pricing.md)

## <a name="phase-1-build-a-foundation-of-security"></a>1. Aşama: güvenlik temeli oluşturma

Bu aşamada Yöneticiler, normal kullanıcı hesaplarını içeri aktarmadan veya oluşturmadan önce Azure AD 'de daha güvenli ve kolay bir temel oluşturmak için temel güvenlik özelliklerini etkinleştirir. Bu temel aşama, başlangıçtan itibaren daha güvenli bir durumda olmanızı ve son kullanıcılarınızın yalnızca yeni kavramlara bir kez tanıtılmasını sağlar.

| Görev | Ayrıntı | Gerekli lisans |
| ---- | ------ | ---------------- |
| [Birden fazla genel yönetici belirleyin](../roles/security-emergency-access.md) | Acil durumda kullanılmak üzere en az iki bulut kalıcı genel yönetici hesabı atayın. Bu hesaplar günlük olarak kullanılmaz ve uzun ve karmaşık parolalara sahip olmalıdır. | Azure AD Ücretsiz |
| [Mümkün olduğunda genel olmayan yönetim rolleri kullanın](../roles/permissions-reference.md) | Yöneticilerinize yalnızca erişmesi gereken alanlara yalnızca ihtiyaç duydukları erişimi verin. Tüm yöneticilerin genel yönetici olması gerekmez. | Azure AD Ücretsiz |
| [Yönetici rolü kullanımını izlemek için Privileged Identity Management etkinleştir](../privileged-identity-management/pim-getting-started.md) | Yönetim rolü kullanımını izlemeye başlamak için Privileged Identity Management etkinleştirin. | Azure AD Premium P2 |
| [Self servis parola sıfırlamayı dağıtma](../authentication/howto-sspr-deployment.md) | Personelin yönetici denetimi olarak sizin oluşturduğunuz ilkeleri kullanarak kendi parolalarını sıfırlamasına izin vererek parola sıfırlama için Yardım Masası çağrılarını azaltın. | |
| [Kuruluşa özgü özel yasaklanmış parola listesi oluşturma](../authentication/tutorial-configure-custom-password-protection.md) | Kullanıcıların, kuruluşunuzun veya bölgenizdeki ortak sözcükleri veya tümceleri içeren parolalar oluşturmasını engelleyin. | |
| [Azure AD parola koruması ile şirket içi tümleştirmeyi etkinleştirme](../authentication/concept-password-ban-bad-on-premises.md) | Yasaklanmış parola listesini şirket içi dizininize genişletin ve şirket içinde ayarlanan parolaların Ayrıca genel ve kiracıya özgü yasaklanmış parola listeleriyle uyumlu olduğundan emin olun. | Azure AD Premium P1 |
| [Microsoft 'un parola kılavuzunu etkinleştir](https://www.microsoft.com/research/publication/password-guidance/) | Kullanıcıların bir küme zamanlaması üzerinde parolalarını değiştirmesini, karmaşıklık gereksinimlerini devre dışı bırakmayı ve kullanıcılarınızın parolalarını anımsamasını ve bunları güvenli bir şekilde tutmasını sağlama gereğini durdur. | Azure AD Ücretsiz |
| [Bulut tabanlı kullanıcı hesapları için düzenli parola sıfırlamaları devre dışı bırak](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Düzenli parola sıfırlama, kullanıcılarınızın mevcut parolalarını artırmasını teşvik eder. Microsoft 'un parola Kılavuzu belgesi ' nde yer alan yönergeleri kullanın ve şirket içi ilkenizi yalnızca bulutta bulunan kullanıcılara yansıtın. | Azure AD Ücretsiz |
| [Akıllı kilitleme Azure Active Directory özelleştirme](../authentication/howto-password-smart-lockout.md) | Bulut tabanlı kullanıcılardan gelen kilitlenmelerini, şirket içi Active Directory kullanıcılarına çoğaltılmalarını durdurun | |
| [AD FS için extranet akıllı kilitlemeyi etkinleştir](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS Extranet kilitleme, deneme yanılma, parola tahmin saldırılarına karşı koruma sağlar, ancak geçerli AD FS kullanıcıların hesaplarını kullanmaya devam etmesine izin verir. | |
| [Koşullu erişimle Azure AD 'de eski kimlik doğrulamasını engelleyin](../conditional-access/block-legacy-authentication.md) | Multi-Factor Authentication zorlayamayan POP, SMTP, IMAP ve MAPI gibi eski kimlik doğrulama protokollerini engelleyin ve bunları reklam işlemleri için tercih edilen bir giriş noktası haline getirin. | Azure AD Premium P1 |
| [Koşullu erişim ilkelerini kullanarak Azure AD Multi-Factor Authentication dağıtma](../authentication/howto-mfa-getstarted.md) | Kullanıcıların koşullu erişim ilkelerini kullanarak gizli uygulamalara erişirken iki adımlı doğrulama gerçekleştirmesini gerektir. | Azure AD Premium P1 |
| [Azure Active Directory Kimlik Koruması etkinleştir](../identity-protection/overview-identity-protection.md) | Kuruluşunuzdaki kullanıcılar için riskli oturum açma işlemlerinin ve güvenliği aşılmış kimlik bilgilerinin izlenmesini etkinleştirin. | Azure AD Premium P2 |
| [Çok faktörlü kimlik doğrulaması ve parola değişikliklerini tetiklemek için risk algılamalarını kullanma](../authentication/tutorial-risk-based-sspr-mfa.md) | Multi-Factor Authentication, parola sıfırlama ve oturum açma işlemlerini riske göre engelleme gibi olayları tetikleyemeyen Otomasyonu etkinleştirin. | Azure AD Premium P2 |
| [Self servis parola sıfırlama ve Azure AD Multi-Factor Authentication için Birleşik kaydı etkinleştirme](../authentication/concept-registration-mfa-sspr-combined.md) | Kullanıcılarınızın hem Azure AD Multi-Factor Authentication hem de Self servis parola sıfırlama için bir ortak deneyimden kaydolmalarına izin verin. | Azure AD Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>2. Aşama: kullanıcıları Içeri aktarma, eşitlemeyi etkinleştirme ve cihazları yönetme

Daha sonra, 1. Aşama ' de bulunan, kullanıcılarınızı içeri aktararak ve eşitlemeyi etkinleştirerek, konuk erişimi planlayarak ve ek işlevselliği desteklemeye hazırlanarak temel bir özellik ekleyeceğiz.

| Görev | Ayrıntı | Gerekli lisans |
| ---- | ------ | ---------------- |
| [Azure AD Connect'i yükleme](../hybrid/how-to-connect-install-select-installation.md) | Mevcut şirket içi dizininizdeki kullanıcıları buluta eşitlemeye hazırlanın. | Azure AD Ücretsiz |
| [Parola karması eşitlemesini Uygula](../hybrid/how-to-connect-password-hash-synchronization.md) | Parola değişikliklerinin çoğaltılmasını, hatalı parola algılamayı ve düzeltmesini ve sızdırılan kimlik bilgisi raporlamasını sağlamak için parola karmalarını eşitler. | Azure AD Ücretsiz |
| [Parola geri yazmayı Uygula](../authentication/tutorial-enable-sspr-writeback.md) | Bulutta parola değişikliklerinin şirket içi Windows Server Active Directory ortamına geri yazılmasına izin verin. | Azure AD Premium P1 |
| [Azure AD Connect Health Uygula](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Azure AD Connect sunucularınız, AD FS sunucularınız ve etki alanı denetleyicileriniz için anahtar sistem durumu istatistiklerinin izlenmesini etkinleştirin. | Azure AD Premium P1 |
| [Azure Active Directory ' de grup üyeliğine göre kullanıcılara lisans atama](../enterprise-users/licensing-groups-assign.md) | Kullanıcı başına ayar yerine gruba göre özellikleri etkinleştiren veya devre dışı bırakan lisanslama grupları oluşturarak zaman ve çaba tasarrufu yapın. | |
| [Konuk Kullanıcı erişimi için bir plan oluşturun](../external-identities/what-is-b2b.md) | Kendi iş, okul veya sosyal kimliklerini kullanarak uygulamalarınızda ve hizmetlerinizde oturum açmalarına izin vererek Konuk kullanıcılarla işbirliği yapın. | [Azure AD dış kimlikleri fiyatlandırması](../external-identities/external-identities-pricing.md) |
| [Cihaz yönetimi stratejisine karar verme](../devices/overview.md) | Kuruluşunuzun cihazlara yönelik olarak izin vermesini belirleyin. Vs katılımını kaydetme, kendi cihazını ve şirket tarafından sağlanmış olan cihazları alın. | |
| [Kuruluşunuzda Iş için Windows Hello 'Yu dağıtma](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Windows Hello kullanarak passwordless kimlik doğrulaması için hazırlanma | |
| [Kullanıcılarınız için passwordless kimlik doğrulama yöntemlerini dağıtın](../authentication/concept-authentication-passwordless.md) | Kullanıcılarınıza güvenli parolasız kimlik doğrulama yöntemleri sağlayın | Azure AD Premium P1 |

## <a name="phase-3-manage-applications"></a>3. Aşama: uygulamaları yönetme

Önceki aşamalarda oluşturmaya devam ettiğimiz için, aday uygulamaları Azure AD ile geçiş ve tümleştirme için tanımladık ve bu uygulamaların kurulumunu tamamlayacağız.

| Görev | Ayrıntı | Gerekli lisans |
| ---- | ------ | ---------------- |
| Uygulamalarınızı tanımla | Kuruluşunuzda kullanımdaki uygulamaları tanımla: şirket içinde, bulutta SaaS uygulamaları ve diğer iş kolu uygulamaları. Bu uygulamaların Azure AD ile yönetilip yönetilmediğini ve bunların yönetilmesini saptayın. | Lisans gerekli değil |
| [Galerideki desteklenen SaaS uygulamalarını tümleştirme](../manage-apps/add-application-portal.md) | Azure AD 'nin binlerce önceden tümleştirilmiş uygulama içeren bir Galerisi vardır. Kuruluşunuzun kullandığı uygulamalardan bazıları büyük olasılıkla galeride doğrudan Azure portal erişilebilir. | Azure AD Ücretsiz |
| [Şirket içi uygulamaları bütünleştirmek için uygulama proxy 'Si kullanma](../manage-apps/application-proxy-add-on-premises-application.md) | Uygulama proxy 'Si, kullanıcıların Azure AD hesabıyla oturum açarak şirket içi uygulamalara erişmesine olanak sağlar. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>4. Aşama: ayrıcalıklı kimlikleri denetleme, erişim incelemesi tamamlamayı ve Kullanıcı yaşam döngüsünü yönetme

4. aşama, yöneticilere yönetim için en az ayrıcalık ilkeleri uygulayan, ilk erişim incelemelerini tamamlayan ve ortak Kullanıcı yaşam döngüsü görevlerinin otomatikleştirilmesini sağlayan yöneticileri görür.

| Görev | Ayrıntı | Gerekli lisans |
| ---- | ------ | ---------------- |
| [Privileged Identity Management kullanımını zorunlu kıl](../privileged-identity-management/pim-security-wizard.md) | Yönetim rollerini normal günden günlük Kullanıcı hesaplarından kaldırın. Yönetici kullanıcıları, bir Multi-Factor Authentication denetimini tamamladıktan sonra, bir iş gerekçe sağlayarak veya belirlenen onaylayanlardan onay isteyerek, rollerinin kullanımını uygun hale getirin. | Azure AD Premium P2 |
| [PıM 'de Azure AD dizin rolleri için erişim gözden geçirmeyi tamamlar](../privileged-identity-management/pim-how-to-start-security-review.md) | Kuruluşunuzun ilkelerine bağlı olarak yönetim erişimini gözden geçirmek için bir erişim gözden geçirme ilkesi oluşturmak üzere güvenlik ve liderlik ekipleriyle birlikte çalışın. | Azure AD Premium P2 |
| [Dinamik grup üyeliği ilkeleri uygulama](../enterprise-users/groups-dynamic-membership.md) | Kullanıcıları, kendi özniteliklerine (veya gerçeği kaynağına) göre otomatik olarak gruplara atamak için dinamik grupları kullanın (örneğin, departman, başlık, bölge ve diğer öznitelikler). |  |
| [Grup tabanlı uygulama sağlamayı Uygula](../manage-apps/what-is-access-management.md) | Kullanıcıları SaaS uygulamalarına otomatik olarak sağlamak için grup tabanlı erişim yönetimi sağlamayı kullanın. |  |
| [Kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatikleştirme](../app-provisioning/user-provisioning.md) | Yetkisiz erişimi engellemek için, çalışan hesabı yaşam döngünüzün el ile yapılan adımları kaldırın. Truth (IK sistem) kaynağınızdan Azure AD 'ye kimlikler eşitler. |  |

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD lisanslama ve fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/active-directory/)

[Kimlik ve cihaz erişim yapılandırmaları](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Sık önerilen kimlik ve cihaz erişim ilkeleri](/microsoft-365/enterprise/identity-access-policies)