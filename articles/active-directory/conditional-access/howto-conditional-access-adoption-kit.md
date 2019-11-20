---
title: Koşullu erişim benimseme seti-Azure Active Directory
description: Kaynaklara erişim için Azure AD koşullu erişimini benimseme
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: daaced0aae595e839f2b2476562fc5caffb63640
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473939"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Azure AD koşullu erişimini benimseme

Mobil ve bulut öncelikli bir dünyada, kullanıcılar farklı cihaz ve uygulama türlerini kullanarak kuruluşunuzun Kaynaklarına her yerden erişebilir. Sonuç olarak, bir kaynağa yalnızca kimlerin erişedükecek kadar çok daha fazla olmaz. Kimlerin erişebileceğini ve kullanıcının nerede olduğunu ve hangi cihazın kullanıldığını ve çok daha fazlasını belirleyebilirsiniz.

Bu denetimi sağlamak için, **Azure Active Directory (ad) koşullu erişim** , bir kullanıcının bir uygulamaya erişmek için karşılaması gereken koşulları (örneğin, MULTI-Factor AUTHENTICATION (MFA) belirtmenize olanak tanır. Koşullu erişim ilkelerinin kullanılması, yetkili kullanıcıların (bir bulut uygulamasına erişim izni verilen kullanıcılar), bulut uygulamalarına belirli koşullar altında erişme şeklini denetler. Daha fazla bilgi için [Azure Active Directory Koşullu erişim nedir](overview.md) bölümüne bakın.

## <a name="key-benefits"></a>Önemli avantajlar

Azure AD koşullu erişim kullanmanın başlıca avantajları şunlardır:

* **Üretkenliği artırın:** Koşullu erişim (CA) ilkeleri, kullanıcıların MFA 'yı kullanması, erişimin engellenmesi veya güvenilir bir cihaz kullanması için gerekli olan noktayı hedeflemesini sağlar. Örneğin, yalnızca kullanıcıların şirket ağı kapalıyken bir uygulamaya MFA kullanmasını gerektiren ilkeler belirleyebilirsiniz. MFA isteklerini azaltmak, kullanıcıların her oturum açtıklarında MFA 'ya göre daha üretken olmalarını sağlar. Ayrıca, Azure AD koşullu erişim, her kullanıcı için ilke belirtmenize ve ayrıca uygulamaya özgü ilkeler oluşturmanıza olanak sağlar.
* **Riski yönetin:** Koşullu erişim ilkelerinin etkinleştirilmesi, size bulut ölçeğinde kimlik koruması, risk tabanlı erişim denetimi özellikleri ve yerel Multi-Factor Authentication desteği sağlar. Kimlik koruması ile, koşullu erişim, bir uygulamaya erişimin ne zaman engellendiğini veya Gated i tanımlamanızı sağlar.
* **Adres uyumluluğu ve idare:** Uygulama için erişim isteklerini ve onaylarını denetleme ve genel uygulama kullanımını anlama, gerçekleştirilen her uygulama erişimi isteği için yerel denetim günlüklerini desteklediğinden Azure AD ile daha kolay. Denetim, istek sahibi kimliği, istenen tarih, iş doğrulama, onay durumu ve onaylayan kimliğini içerir. Bu veriler bir API 'den de bulunur, bu da bu verilerin içe aktarılması işlemini bir güvenlik olayı ve olay Izleme (SıEM) sistemi olarak kullanılabilmesini sağlar.
* **Maliyeti yönetme:** Erişim ilkelerini Azure AD 'ye taşımak, koşullu erişim için Active Directory Federasyon Hizmetleri (AD FS) (ADFS) gibi özel veya şirket içi çözümlere karşı, bu altyapıyı çalıştırmanın maliyetini azaltarak azaltır.

## <a name="customer-case-studies"></a>Müşteri örnek olay incelemeleri

Kuruluşların, koşullara göre bulut uygulamalarına erişmek için otomatik erişim denetimi kararları tanımlamak ve uygulamak üzere Azure AD koşullu erişimini nasıl kullandığını öğrenin. Aşağıdaki öne çıkan hikayeler, bu müşteri ihtiyaçlarını nasıl karşıladığını gösterir.

* [**Wipro** , müşteri görevlendirmeleri geliştirmek için Microsoft bulut güvenlik araçlarıyla Mobil üretkenlik sağlar.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Azure AD 'deki koşullu erişim ilkeleri, şirketin, kendi şirket verileri üzerinde denetimi sürdürirken---kendi kimlik bilgilerini kullanabilen---, şirket dışındaki güvenilen belgeleri, kaynakları ve uygulamaları paylaşmasını sağlar.
* [**Accenture** Microsoft bulut App Security Accenture ile buluta geçme](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Cloud App Security, ağ geçidi için koşullu erişim Azure Active Directory kullanan [koşullu erişim uygulama denetimi](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) özelliğini değerlendiriyor. belirli koşullara göre uygulama erişimi. Mapenske, bu özelliğin İndirmeleri yasaklayarak salt okuma dosya erişimini olanaklı hale getirecek şekilde yararlı olduğunu söyler.
* [ **Aramex** teslimi sınırlı-küresel lojistik ve taşımacılık şirketi, kimlik ve erişim yönetimi çözümü ile buluta bağlı Office oluşturur](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Aramex 'in uzak çalışanları ile güvenli erişimin daha zor olduğundan emin olma. Şirket artık bu uzak çalışanların SaaS uygulamalarına Ağ dışından erişmesini sağlamak için koşullu erişim uyguluyor. Koşullu erişim kuralı, Multi-Factor Authentication uygulanıp zorlamayacağına ve yalnızca doğru kişilerin doğru erişime izin vermesini sağlar.

Azure AD koşullu erişim 'deki müşteri ve iş ortağı deneyimleri hakkında daha fazla bilgi edinmek için, [bkz. kişilerin Azure ile yaptığına yönelik harika şeyler](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Duyurular

Azure AD, iyileştirmeleri sürekli olarak alır. En son gelişmeleri güncel kalmak için bkz. [Azure Active Directory yenilikler nelerdir?](../fundamentals/whats-new.md)

Teknoloji Topluluğu ve Microsoft Identity bölümünün son blogları:

* 24 Eylül 2018, [Azure Databricks Azure Active Directory Koşullu erişim](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 Eylül 2018, [Azure AD koşullu erişim özel denetimleri genel önizlemede](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 Eylül 2018, [Microsoft Cloud App Security ile sınırlı erişim Için Azure AD koşullu erişim desteği artık kullanılabilir](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 Eylül 2018, [Azure AD koşullu erişim: iOS/Android platformları Için Managed Browser desteği şimdi önizlemede](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 Eylül 2018 ' de, [Ülke kodları Için Azure AD koşullu erişimi genel önizlemede](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 Eylül 2018, [Azure AD kullanım koşulları artık kullanıma sunuldu](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Öğrenme kaynakları

Azure AD koşullu erişim işlevlerinin nasıl yapılacağını öğrenmek için aşağıdaki bağlantıları izleyin.

* "[Azure Active Directory Koşullu erişim nedir?](overview.md)" hakkında bilgi edinin
* "[Koşullu erişim Azure Active Directory koşullar nelerdir?" olarak](conditions.md)bilinir.
* "[Koşullu erişim Azure Active Directory konum koşulu nedir?" olarak](location-condition.md)bilinir.
* "[Koşullu erişim Azure Active Directory erişim denetimleri nelerdir?" olarak](controls.md)bilinir.
* "[Koşullu erişim Azure Active Directory ne tür bir aracı nedir?"](what-if-tool.md) öğesini bulun.
* [Azure Active Directory Koşullu erişim Için en iyi uygulamaları](best-practices.md) izleyin

Ayrıca, Azure Active Directory ile tümleştirilmiş tüm hizmetlere erişimi korumaya yönelik rehberlik için aşağıdaki bağlantılara başvurun.

* [Kimlik ve cihaz erişim yapılandırması](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Önceden tanımlanmış koşullu erişim ilkeleri ve ilgili yetenekler dahil olmak üzere önerilen bir ortamı ve yapılandırmayı uygulayarak Enterprise Mobility + Security ürünleri aracılığıyla bulut hizmetlerine güvenli erişimin nasıl yapılandırılacağını açıklar.
* [Koşullu erişim ayarları başvurusunu Azure Active Directory](technical-reference.md). Oluşturacağınızı
   * Hangi uygulamalar koşullu erişimi kullanır?
   * Koşullu erişimle hangi hizmetler etkinleştirilir?
* [Güvenli Kullanıcı erişimi Için koşullu erişimi Azure Active Directory etkinleştirin](https://www.youtube.com/watch?v=eLAYBwjCGoA). Koşullu erişimin diğer kurumsal ve Mobility Suite iş yüklerindeki bir rolü nasıl yürüttüğünde bilgi edinmek için bu videoyu izleyin.

### <a name="training-videos"></a>Eğitim videoları

**Enterprise Mobility + Security koşullu erişim**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Cihaz tabanlı koşullu erişim**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Güvenli Kullanıcı erişimi için koşullu erişim için Azure Active Directory etkinleştirme**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Çevrimiçi kurslar

[Pluralsight.com](https://www.pluralsight.com/)üzerinde aşağıdaki koşullu erişim kurslarına ve daha fazlasına başvurun:

* Pluralsight.com: [Microsoft Azure 'Da kimlik yönetimini tasarlama](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Bu kurs, Azure AD ile kimlik yönetimi çözümünüzü tasarlamak için bilmeniz gereken önemli öğelerde size kılavuzluk eder." Azure AD koşullu erişimi, "Azure AD ile roller ve Access Control kullanımı" modülünden alınmıştır.

* Pluralsight.com: [Microsoft Azure Için tasarım kimlik doğrulaması](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Bu kurs, tüm bulut kimlik doğrulama gereksinimlerinizi çözümlemek için Azure AD 'nin nasıl kullanılacağını açıklar." Azure AD koşullu erişimi, "farklı senaryolar için kimlik doğrulama gereksinimleri" modülünün kapsamına alınmıştır.

* Pluralsight.com: [Microsoft Azure Için tasarım yetkilendirmesi](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Bu kurs, Azure ve Azure AD ile kullanılabilir yetkilendirme seçeneklerini öğretir." Azure AD koşullu erişimi, "Azure Resource Manager ve Azure AD ile yetkilendirme" modülü kapsamında ele alınmıştır.

### <a name="books"></a>Kitaplar

* Ilk olarak, [Azure çözümlerini uygulama-Ikinci sürüm.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Azure hizmetlerini kullanmaya başlayın ve bunları kuruluşunuzda nasıl uygulayacağınızı öğrenin. Azure AD koşullu erişimi, [Azure Active Directory dağıtımı ve eşitlenmesi](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)bölümünde ele alınmıştır. "

* [Mimarlar için Wiley-Microsoft Azure altyapı hizmetleri: bulut çözümlerini tasarlama](https://www.wiley.com/Microsoft+Azure+Infrastructure+Services+for+Architects%3A+Designing+Cloud+Solutions-p-9781119596547)
   * "Microsoft Azure kullanan ortamları anlamak, değerlendirmek, dağıtmak ve sürdürmek için ihtiyacınız olan her şey."

## <a name="white-papers"></a>Teknik incelemeler

* 18 Aralık 2018 ' de yayımlandı, [Azure Active Directory ile dayanıklı bir erişim denetimi yönetim stratejisi oluşturma](../authentication/concept-resilient-controls.md)
   * Bu belge, bir kuruluşun öngörülemeyen kesintiler sırasında kilitleme riskini azaltmak için esnekliği sağlamak üzere benimseyebileceği stratejiler hakkında rehberlik sağlar.

* 18 Eylül 2018 ' de yayınlanan, [uygulamaları Azure Active Directory geçirme kaynakları](../manage-apps/migration-resources.md)
   * Bu Teknik İnceleme, uygulama erişimini ve kimlik doğrulamasını Azure Active Directory (Azure AD) ile geçirmenize yardımcı olacak kaynakların bir listesini içerir.

* 12 Temmuz 2018 ' den yayımlandı [Azure Güvenlik ve uyumluluk şeması: Birleşik KRALLıK resmi Iş yükleri Için PaaS Web uygulaması barındırma](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure şemaları, acize veya uyumluluk gereksinimlerine sahip senaryolara çözüm sunmak için bulut tabanlı mimariler dağıtan rehberlik belgelerinden ve otomasyon şablonlarından oluşur.

## <a name="guidance-for-it-administrators"></a>BT yöneticileri için rehberlik

[Azure Portal](https://portal.azure.com/) genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın. [Azure Active Directory Içindeki yönetici rolü izinleri](../users-groups-roles/directory-assign-admin-roles.md) bölümüne bakın.

BT Yöneticisi olarak, kullanıcıların Azure Multi-Factor Authentication kullanarak kimlik doğrulaması yapmasını, güvenilen bir ağdan veya güvenilir bir cihazdan oturum açmasını gerektirmek için [Azure AD koşullu erişim](overview.md) ' i kullanın.

Başlamanıza yardımcı olacak yararlı bağlantılar aşağıda verilmiştir:

* [Azure Active Directory 'de koşullu erişim için en iyi yöntemler](best-practices.md)
* [Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetmek için Azure AD erişim incelemelerini kullanın](../governance/conditional-access-exclusion.md)
* [Nasıl yapılır: Azure Active Directory 'de koşullu erişim dağıtımınızı planlayın](plan-conditional-access.md)
* [Hızlı başlangıç: koşullu erişim Azure Active Directory belirli uygulamalar için MFA gerektirme](app-based-mfa.md)
* [Hızlı başlangıç: bulut uygulamalarına erişmeden önce kabul edilecek kullanım koşullarını gerektir](require-tou.md)
* [Hızlı başlangıç: koşullu erişim Azure Active Directory bir oturum riski algılandığında erişimi engelleyin](app-sign-in-risk.md)
* [Azure AD koşullu erişim SSS](faqs.md)
   * Diğer sorular için [MSDN forumunu](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)da görüntüleyebilirsiniz.
   * Bir sorunun yanıtını bulamazsanız, destek ekiplerimiz size daha fazla yardımcı olmak için her zaman kullanılabilir. [Microsoft desteğine başvurun](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)' i kullanın.

### <a name="tutorials"></a>Öğreticiler

* [**Hızlı başlangıç: koşullu erişim Azure Active Directory belirli uygulamalar için MFA gerektirme**](app-based-mfa.md)
   * Bu hızlı başlangıçta, ortamınızdaki seçili bir bulut uygulaması için çok faktörlü kimlik doğrulaması gerektiren bir Azure AD koşullu erişim ilkesinin nasıl yapılandırılacağı gösterilmektedir.

* [**Hızlı başlangıç: bulut uygulamalarına erişmeden önce kabul edilecek kullanım koşullarını gerektir**](require-tou.md)
   * Bu hızlı başlangıçta, ortamınızdaki seçili bir bulut uygulaması için ToU 'nın kabul edilmesi gereken bir Azure AD koşullu erişim ilkesinin nasıl yapılandırılacağı gösterilmektedir.

* [**Hızlı başlangıç: koşullu erişim Azure Active Directory bir oturum riski algılandığında erişimi engelleyin**](app-sign-in-risk.md)
   * Bu hızlı başlangıçta, yapılandırılmış bir oturum açma risk düzeyi algılandığında oturum açmayı engelleyen bir koşullu erişim ilkesinin nasıl yapılandırılacağı gösterilmektedir.

* [Öğretici: **Azure Portal çok faktörlü kimlik doğrulaması gerektiren klasik bir Ilkeyi geçirme**](policy-migration-mfa.md)
   * Bu öğreticide, bir bulut uygulaması için Multi-Factor Authentication (MFA) gerektiren klasik bir ilkenin nasıl geçirileceği gösterilmektedir.

## <a name="end-user-readiness-and-communication"></a>Son Kullanıcı hazırlığı ve iletişim

Koşullu erişim, son kullanıcı deneyimini etkileyebilecek diğer Azure AD yeteneklerini kullanır. Örneğin, kullanıcılar için güçlü kimlik doğrulamasını etkinleştirmek üzere Azure Multi-Factor Authentication 'ı kullanabilirsiniz. Bu durumda, Azure MFA 'nın Son Kullanıcı şablonlarını kullanacaksınız.

## <a name="next-steps"></a>Sonraki adımlar

* [Koşullu erişim dağıtım planlama belgeleriyle](plan-conditional-access.md)dağıtımınızı başlatın.
