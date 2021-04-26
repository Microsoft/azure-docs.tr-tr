---
title: Tüm uygulamalarınızı Azure AD ile tümleştirmek için beş adım
description: Bu kılavuzda, tüm uygulamalarınızın Azure AD ile nasıl tümleştirileceği açıklanmaktadır. Her adımda, değeri açıkladık ve teknik ayrıntıları açıklayacak kaynakların bağlantılarını sağlayacağız.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: 7fd9e504448d55b4a2ef8c10b4ba1176cb2e3402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96172644"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Tüm uygulamalarınızı Azure AD ile tümleştirmek için beş adım

Azure Active Directory (Azure AD), Microsoft bulut tabanlı kimlik ve erişim yönetimi hizmetidir. Azure AD, müşterilerin, iş ortaklarının ve çalışanların ihtiyaç duydukları uygulamalara erişebilmesi için güvenli kimlik doğrulama ve yetkilendirme çözümleri sağlar. Azure AD, [koşullu erişim](../conditional-access/overview.md), [çok faktörlü kimlik doğrulaması](../authentication/concept-mfa-howitworks.md), [Çoklu oturum açma](../hybrid/how-to-connect-sso.md)ve [Otomatik Kullanıcı sağlama](../app-provisioning/user-provisioning.md) sayesinde kimlik ve erişim yönetimini kolay ve güvenli hale getirin.

Şirketinizin Microsoft 365 aboneliği varsa, muhtemelen Azure AD 'yi [zaten kullanıyorsunuz](/office365/enterprise/about-office-365-identity) . Ancak, Azure AD tüm uygulamalarınız için kullanılabilir ve [uygulama yönetilebilmesini merkezileştirerek](../manage-apps/common-scenarios.md) tüm uygulama portföyünüzde aynı kimlik yönetimi özelliklerini, araçları ve ilkeleri kullanabilirsiniz. Bunun yapılması güvenliği artıran, maliyetleri azaltan, üretkenliği artıran ve uyumluluğu sağlamanıza olanak tanıyan birleştirilmiş bir çözüm sunar. Ve şirket içi uygulamalara uzaktan erişim alacaksınız.

Bu kılavuzda, tüm uygulamalarınızın Azure AD ile nasıl tümleştirileceği açıklanmaktadır. Her adımda, değeri açıkladık ve teknik ayrıntıları açıklayacak kaynakların bağlantılarını sağlayacağız. Bu adımları önerdiğimiz bir sırada sunuyoruz. Bununla birlikte, sizin için en fazla değeri ekleyen her şeyi kullanmaya başlamak için işlemin herhangi bir kısmına atlayabilirsiniz.

Bu konu başlığı altında, [uygulamalar Azure Active Directory sayfasına geçiş kaynaklarımızda](../manage-apps/migration-resources.md) bulunan ayrıntılı iş süreci Teknik İncelemeleri de dahil olmak üzere diğer kaynaklar.

## <a name="1-use-azure-ad-for-new-applications"></a>1. yeni uygulamalar için Azure AD kullanın

İlk olarak, yeni alınan uygulamalara odaklanın. İşiniz yeni bir uygulama kullanmaya başladığında [Azure AD kiracınıza hemen ekleyin](../manage-apps/add-application-portal.md) . Azure AD 'ye yeni uygulamalar eklemek için kuruluşunuzda standart uygulama olan bir şirket ilkesi ayarlayın. Bu, mevcut iş süreçlerine en az düzeyde düşüktür ve kullanıcıların ortamınızda iş yapmasına olanak vermeden uygulamalarınızı tümleştirmeden aldığınız değeri araştırıp kanıtlamanızı sağlar.

Azure Active Directory (Azure AD), kullanmaya başlamanızı kolaylaştırmak için binlerce önceden tümleştirilmiş uygulama içeren bir galersahiptir. Azure AD kuruluşunuza, şunun gibi popüler uygulamalarla tümleştirmeyle ilgili adım adım [öğreticiler](../saas-apps/tutorial-list.md) içeren [bir galeri uygulaması ekleyebilirsiniz](../manage-apps/add-application-portal.md) :

- [ServiceNow](../saas-apps/servicenow-tutorial.md)
- [Workday](../saas-apps/workday-tutorial.md)
- [Salesforce](../saas-apps/salesforce-tutorial.md)
- [AWS](../saas-apps/amazon-web-service-tutorial.md)
- [Slack](../saas-apps/slack-tutorial.md)

Ayrıca, kuruluşunuzda mevcut olan herhangi bir uygulama veya zaten Azure AD galerisinin parçası olmayan bir satıcıdan herhangi bir üçüncü taraf uygulaması dahil olmak üzere, [galeride olmayan uygulamaları tümleştirebilirsiniz](../manage-apps/view-applications-portal.md). Ayrıca, bu yoksa [uygulamanızı galeriye ekleyebilirsiniz](../develop/v2-howto-app-gallery-listing.md) .

Son olarak, şirket içinde Geliştirdiğiniz uygulamaları da tümleştirebilirsiniz. Bu, bu kılavuzun beş adımında ele alınmıştır.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. mevcut uygulama kullanımını belirleme ve işi önceliklendir

Ardından, çalışanların sıklıkla kullandığı uygulamaları bulabilir ve bunları Azure AD ile tümleştirmek için çalışmalarınızın önceliğini belirleyin.

Ağınızda "gölge" bulmak ve yönetmek için Microsoft Cloud App Security [bulut bulma araçları](/cloud-app-security/tutorial-shadow-it) 'nı kullanarak başlayabilirsiniz (yanı, BT departmanı tarafından yönetilmeyen uygulamalar). Bulma işlemini basitleştirmek ve genişletmek için [Microsoft Defender Gelişmiş tehdit koruması (ATP) kullanabilirsiniz](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) .

Ayrıca, kuruluşunuzdaki tüm AD FS uygulamalarını, bunlarda oturum açan benzersiz kullanıcıların sayısını ve bunları Azure AD ile tümleştirmek için uyumluluk için Azure portal [AD FS uygulama etkinliği raporunu](../manage-apps/migrate-adfs-application-activity.md) kullanabilirsiniz.

Mevcut Yatayı bulduktan sonra, [bir plan oluşturmanız](../manage-apps/migration-resources.md) ve tümleştirilecek en yüksek öncelikli uygulamaları önceliklendirmek isteyeceksiniz. Bu işleme kılavuzluk etmek için sorabileceğiniz bazı örnek sorular şunlardır:

- En çok kullanılan uygulamalar nelerdir?
- Riskide hangisi?
- Gelecekte kullanımdan kaldırılacak uygulamalar, bir taşımanın gereksiz yere getirilmesi
- Şirket içinde kalması gereken uygulamalar ve buluta taşınamaz mi?

Tüm uygulamalarınız tümleştirildikten ve artık birden fazla kimlik çözümünden yararlanmadan, en büyük avantajları ve maliyet tasarruflarını görürsünüz. Ancak, bu hedefe doğru geçiş yaparken daha kolay kimlik yönetimi ve güvenlik güvenliği de yaşarsınız. İşinizi önceliklendirmek ve durumunuza ne anlamlı olduğuna karar vermek için bu süreyi kullanmak istiyorsunuz.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. diğer kimlik sağlayıcılarına güvenen uygulamaları tümleştirin

Bulma işleminiz sırasında, BT departmanı tarafından izlenmeyen, verilerinizi ve kaynaklarınızı savunmasız bırakan uygulamalar bulunmuş olabilirsiniz. Active Directory Federasyon Hizmetleri (AD FS) (ADFS) veya diğer kimlik sağlayıcıları da dahil olmak üzere alternatif kimlik çözümlerini kullanan uygulamalarınız da olabilir. Para tasarrufu sağlamak ve güvenliği artırmak için kimlik ve erişim yönetimi 'ni nasıl birleştirebileceğiniz göz önünde bulundurun. Sahip olduğunuz kimlik çözümlerinin sayısını azaltmak şunları yapmanız gerekir:

- Şirket içi Kullanıcı sağlama ve kimlik doğrulaması gereksinimini ortadan kaldırarak ve aynı hizmet için diğer bulut kimlik sağlayıcılarına ödenen lisanslama ücretleri ile tasarruf edebilirsiniz.
- Kimlik ve erişim yönetimi sürecinizdeki yönetim yükünü azaltın ve daha az artıklıkları ile sıkı güvenliği etkinleştirin.
- Çalışanların, gereken tüm uygulamalara, [Uygulamaps portalı](../manage-apps/access-panel-collections.md)aracılığıyla güvenli çoklu oturum açma erişimi almasını sağlar.
- Uygulama kullanımınızdan aldığı veri miktarını artırarak ve avantajlarının yeni eklenen uygulamalara genişletilmesi yoluyla, koşullu erişim gibi Azure AD 'nin [kimlik koruması](../identity-protection/overview-identity-protection.md) ile ilgili yönetim bilgilerini artırın.

İş ve uygulama sahiplerini algılayan ve ilgilendiğiniz bir [poster](https://aka.ms/AppOnePager) ve [sunum](https://aka.ms/AppGuideline) dahil olmak üzere, uygulamaları Azure AD ile tümleştirme iş sürecini yönetmeye yönelik bir kılavuz yayınladık. Bu örnekleri kendi markanızla değiştirebilir ve bu işlemi tamamlamada kullandığınız gibi şirket portalı, bülteninizi veya diğer ortamınız aracılığıyla kuruluşunuzda yayımlayabilirsiniz.

Active Directory Federasyon Hizmetleri (AD FS) (ADFS) kullanımını değerlendirerek başlamak iyi bir yerdir. Birçok kuruluş SaaS uygulamaları, özel Iş kolu uygulamaları ve Microsoft 365 ve Azure AD tabanlı uygulamalar ile kimlik doğrulaması için ADFS kullanır:

![Diyagramda, şirket içi uygulamalar, iş kolu uygulamaları, SaaS uygulamaları ve Azure AD aracılığıyla Office 365, Active Directory ve AD FS noktalı çizgilerle bağlantı kurarak.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

Bu yapılandırmayı, ADFS 'yi kimlik yönetimi çözümünüzün [merkezi olarak Azure AD ile değiştirerek](../manage-apps/migrate-adfs-apps-to-azure.md) yükseltebilirsiniz. Bunun yapılması, çalışanlarınızın erişmek istedikleri her uygulama için oturum açma olanağı sağlar ve çalışanların, yukarıda bahsedilen diğer avantajlara ek olarak, [uygları portalı](../user-help/my-apps-portal-end-user-access.md)aracılığıyla ihtiyaç duydukları herhangi bir iş uygulamasını bulmasını kolaylaştırır.

![Diyagramda Active Directory ve AD FS, iş kolu uygulamaları, SaaS uygulamaları ve Office 365 ile Azure Active Directory noktalı çizgilerle bağlantı kurarak şirket içi uygulamalar gösterilir.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Azure AD merkezi kimlik sağlayıcısı olduktan sonra, Federe bir çözüm kullanmak yerine ADFS 'den tamamen geçiş yapabilirsiniz. Kimlik doğrulaması için önceden ADFS kullanan uygulamalar artık Azure AD 'yi kullanabilir.

![Diyagramda, şirket içi, iş kolu uygulamaları, SaaS uygulamaları ve Office 365 ' nin tüm noktalı çizgiler Azure Active Directory ' ye bağlanması gösterilmektedir. Active Directory ve AD FS yok.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

Ayrıca, bulut tabanlı farklı bir kimlik sağlayıcısı kullanan uygulamaları Azure AD 'ye geçirebilirsiniz. Kuruluşunuzun yerinde birden fazla kimlik erişim yönetimi (ıAM) çözümü olabilir. Tek bir Azure AD altyapısına geçiş yapmak, ıAM lisanslarındaki bağımlılıkları (Şirket içi veya bulutta) ve altyapı maliyetlerini azaltmaya yönelik bir fırsattır. Azure AD için M365 lisansları aracılığıyla zaten ücretli olabilecek durumlarda, başka bir ıAM çözümünün ek maliyetini ödemenizi gerektirmez.

## <a name="4-integrate-on-premises-applications"></a>4. Şirket içi uygulamaları tümleştirme

Geleneksel olarak, uygulamalar yalnızca şirket ağına bağlıyken erişime izin vererek güvende tutulmuştur. Bununla birlikte, giderek bağlı bir dünyada, dünyanın her yerinden bağımsız olarak müşteriler, iş ortakları ve/veya çalışanlar için uygulamalara erişime izin vermek istiyoruz. [Azure ad uygulama ara sunucusu](../manage-apps/what-is-application-proxy.md) (appproxy), mevcut şirket Içi UYGULAMALARıNıZı Azure AD 'ye bağlayan Azure AD 'nin bir özelliğidir ve bunu yapmak için kenar sunucularını veya diğer ek altyapıyı korumanıza gerek yoktur.

![Diyagram, uygulama ara sunucusu hizmetini işlem içinde gösterir. Bir Kullanıcı " https://sales.contoso.com " öğesine eriştiğinde ve istekleri "" https://sales-contoso.msappproxy.net Şirket içi adresine Azure Active Directory "" aracılığıyla yönlendirilir http://sales](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

Uygulama proxy 'Sini etkinleştirmek ve Azure AD kiracınıza şirket içi bir uygulama eklemek için [Azure Active Directory 'de uygulama ara sunucusu üzerinden uzaktan erişim için şirket içi uygulama ekleme öğreticisini](../manage-apps/application-proxy-add-on-premises-application.md) kullanabilirsiniz.

Ayrıca, F5 Big-IP APM veya Zscaler özel erişimi gibi uygulama teslim denetleyicilerini tümleştirebilirsiniz. Bunları Azure AD ile tümleştirerek, iş ortağı ürününün trafik yönetimi ve güvenlik özelliklerinin yanı sıra Azure AD 'nin modern kimlik doğrulama ve kimlik yönetimi özelliklerini alırsınız. Bu çözümü [güvenli karma erişim](../manage-apps/secure-hybrid-access.md)olarak çağırıyoruz. Şu hizmetlerden birini bugün kullanıyorsanız, bunları Azure AD ile tümleştirme konusunda size kılavuzluk edecek öğreticiler sunuyoruz.

- [Akamai kurumsal uygulama erişimi (EAA)](../saas-apps/akamai-tutorial.md)
- [Citrix uygulama teslim denetleyicisi (ADC)](../saas-apps/citrix-netscaler-tutorial.md) (eski adıyla Citrix NetScaler olarak biliniyordu)
- [F5 Big-IP APM](../saas-apps/headerf5-tutorial.md)
- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)

## <a name="5-integrate-apps-your-developers-build"></a>5. geliştiricilerin derlemenize yönelik uygulamaları tümleştirin

Şirketinizde yerleşik olan uygulamalar için, geliştiricileriniz kimlik doğrulama ve yetkilendirme uygulamak üzere [Microsoft Identity platformunu](../develop/index.yml) kullanabilir. [Azure AD 'ye kayıtlı](../develop/quickstart-register-app.md) ve yalnızca portföyünüzdeki diğer uygulamalar gibi yönetilen platformla tümleştirilmiş uygulamalar.

Geliştiriciler platformu hem iç kullanım uygulamaları hem de müşteriye yönelik uygulamalar için kullanabilir ve platformu kullanmayla birlikte gelen başka avantajlar da vardır. Platformun bir parçası olan [Microsoft kimlik doğrulama kitaplıkları (msal)](../develop/msal-overview.md), geliştiricilerin çok faktörlü kimlik doğrulaması gibi modern deneyimleri etkinleştirmesine ve güvenlik anahtarlarının kendi uygulamalarına gerek duymadan uygulamalara erişmesi için güvenlik anahtarlarının kullanılmasına olanak tanır. Ayrıca, Microsoft Identity platformu ile tümleştirilmiş uygulamalar, bir kuruluştaki üretkenlik, kimlik ve güvenlik düzenlerini açıklayan Microsoft 365 verileri sağlayan birleştirilmiş bir API uç noktası [Microsoft Graph](../develop/microsoft-graph-intro.md) erişebilir. Geliştiriciler, kullanıcılarınız için üretkenliği artıran özellikler uygulamak için bu bilgileri kullanabilir. Örneğin, kullanıcının yakın zamanda etkileşimde bulunduğu ve uygulamanın kullanıcı arabiriminde karşılaştığı kişileri tanımlayarak.

Desteklenen dillerde ve platformlarda platforma ve [birçok kod](../develop/sample-v2-code.md) örneğine kapsamlı bir giriş sağlayan bir [video serimiz](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) vardır.

## <a name="next-steps"></a>Sonraki adımlar

- [Uygulamaları Azure Active Directory’ye geçirmeye yönelik kaynaklar](../manage-apps/migration-resources.md)