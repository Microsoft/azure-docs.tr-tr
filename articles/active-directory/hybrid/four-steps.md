---
title: Azure Active Directory ile güçlü bir kimlik temeli için dört adım
description: Bu konuda, müşterilerin güçlü bir kimlik Foundation oluşturmak için uygulayabileceğiniz dört adım karma kimliği açıklanmaktadır.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2cd080602ec879affb3ece978f14d5afc33ef9d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173095"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Azure Active Directory ile güçlü bir kimlik temeli için dört adım

Uygulamalara ve verilere erişimin yönetilmesi, uygulamaların buluta hızlı bir şekilde taşınması nedeniyle çevre ağları ve güvenlik duvarları gibi geleneksel ağ güvenlik sınırı stratejilerine artık güvenmemelidir. Artık kuruluşların, kuruluşun uygulamalarına ve verilerine kimlerin ve ne erişimi olduğunu denetlemek için kimlik çözümüne güvenmesi gerekir. Daha fazla kuruluş, çalışanların kendi cihazlarını iş ve cihazlarını Internet 'e bağlanabildiği her yerden kullanmasını sağlar. Bu cihazların uyumlu ve güvenli olmasını sağlamak, bir kuruluşun uygulamayı seçtiği kimlik çözümünde önemli bir konudur. Bugünün dijital çalışma alanında, kimlik, buluta taşınan herhangi bir kuruluşun [birincil denetim düzledir](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) .

Azure Active Directory (Azure AD) karma kimlik çözümünü benimseme bölümünde kuruluşlar, otomasyon, yetkilendirme, self servis ve çoklu oturum açma özellikleri aracılığıyla üretkenliği artıran Premium özelliklere erişim elde edebilir. Çalışanlarınızın şirket kaynaklarına, işlerini yapması gereken her yerde erişmelerini sağlar, böylece BT takımınızın, doğru kişilerin güvenli üretkenlik sağlamak üzere doğru kaynaklara doğru erişime sahip olmasını sağlayarak erişimi yönetebilmesini sağlar.

Bu en iyi uygulama denetim listesi, dersleri temel alınarak, kuruluşunuzda *güçlü* bir kimlik temeli oluşturmak için önerilen eylemleri hızlı bir şekilde dağıtmanıza yardımcı olacaktır:

* Uygulamalara kolayca bağlanın
* Her Kullanıcı için otomatik olarak bir kimlik oluşturun
* Kullanıcılarınıza güvenli bir şekilde güç katın
* Öngörülerinizi çalıştırın

## <a name="step-1---connect-to-apps-easily"></a>1\. adım-uygulamalara kolayca bağlanma

Uygulamalarınızı Azure AD 'ye bağlayarak, çoklu oturum açma (SSO) ve Kullanıcı hazırlama işlemlerini etkinleştirerek Son Kullanıcı üretkenliğini ve güvenliğini geliştirebilirsiniz. Uygulamalarınızı tek bir yerde (Azure AD) yönetirken, yönetim yükünü en aza indirebilir ve güvenlik ve uyumluluk ilkeleriniz için tek bir denetim noktası elde edebilirsiniz.

Bu bölüm, uygulamalara Kullanıcı erişimini yönetme, iç uygulamalara güvenli uzaktan erişim sağlama ve uygulamalarınızı Azure AD 'ye geçirme avantajlarına yönelik seçeneklerinizi anlatmaktadır.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Uygulamalarınızı kullanıcılarınız için sorunsuz bir şekilde kullanılabilir hale getirin

Azure AD, yöneticilerin [Azure Portal](https://portal.azure.com/)kurumsal uygulamalar galerisine [uygulama eklemesini](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) sağlar. Kurumsal uygulamalar galerisine uygulamalar eklemek, uygulamaları kimlik sağlayıcınız olarak Azure AD 'yi kullanacak şekilde yapılandırmanızı kolaylaştırır. Ayrıca, koşullu erişim ilkeleriyle uygulamaya yönelik Kullanıcı erişimini yönetmenizi ve kullanıcıların parolalarını tekrar tekrar girmesi ve hem şirket içinde hem de otomatik olarak oturum açmasını sağlamak için uygulamalara çoklu oturum açma (SSO) yapılandırma olanağı sağlar. bulut tabanlı uygulamalar.

Uygulamalar Azure AD galerisine eklendikten sonra, kullanıcılar bunlara atanan uygulamaları görebilir ve gerektiğinde diğer uygulamaları arayabilir ve talep edebilir. Azure AD, kullanıcıların uygulamalarına erişmesi için [çeşitli yöntemler](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) sağlar:

* Erişim paneli/uygulamalarım
* Office 365 uygulama başlatıcısı
* Birleştirilmiş uygulamalarda doğrudan oturum açma
* Doğrudan oturum açma bağlantıları

Uygulamalara yönelik kullanıcı erişimi hakkında daha fazla bilgi edinmek için bkz. **3. adım--bu makaledeki kullanıcılarınıza güç katın** .

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Uygulamaları Active Directory Federasyon Hizmetleri (AD FS) 'den Azure AD 'ye geçirme

Active Directory Federasyon Hizmetleri (AD FS) (ADFS) ' dan Azure AD 'ye çoklu oturum açma yapılandırmasını geçirme güvenlik, daha tutarlı yönetilebilirlik ve işbirliği özelliklerine ek yetenekler sağlar. En iyi sonuçlar için uygulamalarınızı AD FS 'den Azure AD 'ye geçirmeniz önerilir. Uygulama kimlik doğrulaması ve yetkilendirmesinin Azure AD 'ye getirilmesi aşağıdaki avantajları sağlar:

* Maliyeti yönetme
* Risk yönetimi
* Üretkenliği artırma
* Uyumluluk ve idare hakkında adresleme

Daha fazla bilgi edinmek için [uygulamalarınızı Azure Active Directory](https://aka.ms/migrateapps/whitepaper) teknik incelemeye geçirme konusuna bakın.

### <a name="enable-secure-remote-access-to-apps"></a>Uygulamalara güvenli uzaktan erişimi etkinleştir

[Azure AD uygulama ara sunucusu](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) , kuruluşların şirket içi uygulamaları, iç uygulamalara güvenli bir şekilde erişmesi gereken uzak kullanıcılar için buluta yayımlamasına yönelik basit bir çözüm sunar. Azure AD 'de çoklu oturum açma işleminden sonra, kullanıcılar dış URL 'Ler veya dahili uygulama portalı aracılığıyla hem buluta hem de şirket içi uygulamalara erişebilir.

Azure AD Uygulama Ara Sunucusu aşağıdaki avantajları sunmaktadır:

* Azure AD 'yi şirket içi kaynaklara genişletme
  * Bulut ölçeğinde güvenlik ve koruma
  * Koşullu erişim ve etkinleştirilmesi kolay Multi-Factor Authentication gibi özellikler
* Çevre ağında VPN ve geleneksel ters proxy çözümleri gibi bileşenler yok
* Gelen bağlantı gerekli değil
* Bulutta ve şirket içinde cihazlar, kaynaklar ve uygulamalar arasında çoklu oturum açma (SSO)
* Son kullanıcıları her zaman ve her yerde üretken olmaya güçler

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Microsoft Cloud App Security ile gölge BT 'yi bulma

Modern kuruluşlarda, BT departmanları genellikle kullanıcıların işlerini yapması için kullanılan tüm bulut uygulamalarının farkında değildir. BT yöneticileri, çalışanlarının ne kadar olduğunu düşündüklerini yaklaşık olarak 30 veya 40 söyledikleri hakkında bir şekilde görür. Gerçekte, ortalama, kuruluşunuzda çalışanlar tarafından kullanılan 1.000 ayrı uygulama üzerinde bulunur. çalışanların %80 ' ü incelemeyen ve güvenlik ve uyumluluk ilkelerinizle uyumlu olmayan, tasdiksiz uygulamalar kullanıyor.

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS), KULLANıCıLARıN, SSO ve koşullu erişim gibi yeteneklerden yararlanması için, Kullanıcı tarafından tasdikli ve kurumsal uygulamalar galerisine ekleyebileceği yararlı uygulamaları belirlemenize yardımcı olabilir.

<em>"**Cloud App Security** , Accenture korumaya yardımcı olan temel güvenlik ilkelerini destekleyecek şekilde, insanlarımızın bulut ve SaaS uygulamalarınızı doğru şekilde kullandığından emin olmamıza yardımcı olur."</em> --- [John Blasi, Direktörü yönetme, bilgi güvenliği, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

MCAS Ayrıca, gölge BT 'nin risk düzeyini tespit edebilir, kurumsal verilere yetkisiz erişimi, olası veri sızıntısını ve uygulamalarda bulunan diğer güvenlik risklerini de belirleyebilir.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2\. adım-her kullanıcı için otomatik olarak bir kimlik oluşturun

Şirket içi ve bulut tabanlı dizinlerin bir Azure AD karma kimlik çözümünde birlikte getirilmesi, bulutta mevcut kimliklerinizi sağlayarak mevcut şirket içi Active Directory yatırımınızı yeniden kullanmanıza olanak tanır. Çözüm, şirket içi kimlikleri Azure AD ile eşitler, ancak şirket içi Active Directory, kimlikler için birincil kaynak olarak mevcut idare çözümleriyle birlikte çalışır. Microsoft 'un Azure AD hibrit kimlik çözümü, şirket içi ve bulut tabanlı yeteneklere yayıldığından, konumlarından bağımsız olarak tüm kaynaklarda kimlik doğrulaması ve yetkilendirme için ortak bir kullanıcı kimliği oluşturur.

Şirket içi dizinlerinizi Azure AD ile tümleştirmek, kullanıcılarınızın hem bulut kaynaklarına hem de şirket içi kaynaklara erişmek için ortak bir kimlik sağlayarak uygulamalar ve hizmetler arasında birden çok hesap kullanmalarını önler. Birden çok hesap kullanmak, son kullanıcılar ve buna benzer bir noktasıdır. Bir son kullanıcı perspektifinden, birden çok hesaba sahip olmak birden çok parolayı hatırlamaları gerektiği anlamına gelir. Bunu önlemek için, birçok kullanıcı her bir hesap için aynı parolayı yeniden kullanır ve bu da bir güvenlik perspektifinden hatalı olur. BT perspektifinden, Son Kullanıcı şikayetleriyle birlikte daha fazla parola sıfırlamaları ve yardım masası maliyetlerine daha fazla yeniden kullanım sağlar.

Azure AD Connect, şirket içi kimliklerinizi Azure AD ile eşitlemek için kullanılan, daha sonra bulut uygulamalarına erişmek için kullanılan araçtır. Kimlikler Azure AD 'de olduktan sonra Salesforce veya Concur gibi SaaS uygulamalarına temin edebilirler.

Bu bölümde, bulut için yüksek kullanılabilirlik, modern kimlik doğrulama ve şirket içi parmak izini azaltma önerilerini listeliyoruz.

> [!NOTE]
> Azure AD Connect hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Azure AD Connect eşitleme nedir?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Azure AD Connect için bir hazırlama sunucusu kurun ve güncel tutun

Azure AD Connect, sağlama sürecinde bir anahtar rol oynar. Eşitleme sunucusu herhangi bir nedenle çevrimdışı kalırsa, şirket içi değişiklikler bulutta güncellenmez ve kullanıcılara erişim sorunlarına neden olur. Eşitleme sunucusu çevrimdışı olduktan sonra yöneticilerin eşitlemeyi hızlı bir şekilde sürdürmesini sağlayan bir yük devretme stratejisi tanımlamanız önemlidir.

Birincil Azure AD Connect sunucunuzun çevrimdışı olması durumunda yüksek kullanılabilirlik sağlamak için, Azure AD Connect için ayrı bir [hazırlama sunucusu](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) dağıtmanız önerilir. Sunucu dağıtmak, yöneticinin hazırlama sunucusunu basit bir yapılandırma anahtarıyla üretime "yükselmesini" sağlar. Hazırlama modunda yapılandırılmış bir bekleme sunucusuna sahip olmak, yeni yapılandırma değişikliklerini test etmenize ve dağıtmanıza ve eskisini açığa çıkarırsam yeni bir sunucu tanıtmaya de olanak tanır.

> [!TIP]
> Azure AD Connect düzenli olarak güncelleştirilir. Bu nedenle, performans geliştirmelerinden, hata düzeltmelerinden ve her yeni sürümün sağladığı yeni özelliklerden yararlanmak için hazırlama sunucusunu güncel tutmanız önemle tavsiye edilir.

### <a name="enable-cloud-authentication"></a>Bulut kimlik doğrulamasını etkinleştir

Şirket içi Active Directory kuruluşların, Azure AD Connect kullanarak dizinini Azure AD 'ye genişletmesi ve uygun kimlik doğrulama yöntemini yapılandırması gerekir. Kuruluşunuz için [doğru kimlik doğrulama yöntemini seçmek](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) , uygulamaları buluta taşıma sürecinizdeki ilk adımdır. Tüm bulut verilerine ve kaynaklarına erişimi denetlemediğinden bu kritik bir bileşendir.

Azure AD 'de şirket içi dizin nesnelerinde bulut kimlik doğrulamasını etkinleştirmek için en basit ve önerilen yöntem, [Parola karması eşitlemesini](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) etkinleştirmektir. Alternatif olarak, bazı kuruluşlar [doğrudan kimlik doğrulamayı](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) etkinleştirmeyi düşünebileceğiniz bir işlem olabilir.

PHS veya PTA seçeneklerinden birini tercih etmeksizin, kullanıcıların kurumsal ağınızda Windows 7 ve 8 cihazları kullanırken kullanıcı adını ve parolasını sürekli olarak girmeden buluta erişmelerini sağlamak için [sorunsuz çoklu oturum açmayı](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) unutmayın. Çoklu oturum açma olmadan, kullanıcıların uygulamaya özgü parolaları hatırlamaları ve her uygulamada oturum açması gerekir. Benzer şekilde, BT personelinin Office 365, Box ve Salesforce gibi her uygulama için Kullanıcı hesapları oluşturması ve güncelleştirmesi gerekir. Kullanıcıların parolalarını hatırlamaları ve her uygulamada oturum açma süresini harcaması gerekir. Tüm kuruluş için standartlaştırılmış bir çoklu oturum açma mekanizması sağlamak, en iyi kullanıcı deneyimi, risk azaltma, rapor verme ve idare açısından önemlidir.

Zaten AD FS veya başka bir şirket içi kimlik doğrulama sağlayıcısı kullanan kuruluşlar için, kimlik sağlayıcınız olarak Azure AD 'ye geçilmesi karmaşıklığı azaltabilir ve kullanılabilirliği iyileştirebilir. Federasyonu kullanmaya yönelik özel kullanım çalışmalarınız yoksa, daha az şirket içi bir kaplama ve bulut tekliflerinin avantajlarından yararlanmak için federal kimlik doğrulamasından PHS ve sorunsuz SSO ya da PTA ve sorunsuz SSO 'lara geçiş yapmanızı öneririz. Geliştirilmiş kullanıcı deneyimleri. Daha fazla bilgi için bkz. [Azure Active Directory için Federasyondan Parola karması eşitlemesine geçiş](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Hesapların otomatik olarak sağlamasını etkinleştir

Uygulamalarınızda otomatik sağlama ve sağlamayı kaldırma özelliğinin etkinleştirilmesi, birden fazla sistem genelinde kimliklerin yaşam döngüsünü yönetmek için en iyi stratejidir. Azure AD, ServiceNow ve Salesforce gibi çeşitli popüler SaaS uygulamalarına ve [SCIM 2,0 protokolünü](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)uygulayan diğerlerine yönelik [Otomatik, ilke tabanlı sağlama ve](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) Kullanıcı hesaplarını sağlamayı destekler. Özel kod veya CSV dosyalarının el ile karşıya yüklenmesi gereken geleneksel sağlama çözümlerinin aksine, sağlama hizmeti bulutta barındırılır ve Azure portal kullanılarak ayarlanabilir ve yönetilebilecek önceden tümleştirilmiş bağlayıcılar Özellikler. Otomatik sağlamayı kaldırma 'nın önemli bir avantajı, kuruluştan ayrıldıklarında kullanıcıların kimliklerini anahtar SaaS uygulamalarından anında kaldırarak kuruluşunuzun güvenliğini sağlamaya yardımcı olur.

Otomatik Kullanıcı hesabı sağlama ve nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Ile SaaS uygulamalarına Kullanıcı sağlamayı ve sağlamayı kaldırmayı otomatikleştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

## <a name="step-3---empower-your-users-securely"></a>3\. adım-kullanıcılarınıza güvenli bir şekilde güç katın

Bugünün dijital çalışma alanında, güvenliği üretkenlik ile dengelemek önemlidir. Ancak, son kullanıcılar genellikle üretkenliğini ve bulut uygulamalarına erişimini yavaşlatan güvenlik ölçümlerine geri gönderim sağlar. Bu konuda yardım almak için Azure AD, kullanıcıların yönetim yükünü en aza indirerek üretken kalmasını sağlayan self servis özellikleri sağlar.

Bu bölümde, geri kalan dikkatli sırasında kullanıcılarınızı güçleyerek kuruluşunuzdaki uçuşleştirme kaldırma önerileri listelenmiştir.

### <a name="enable-self-service-password-reset-for-all-users"></a>Tüm kullanıcılar için self servis parola sıfırlamayı etkinleştir

Azure [self servis parola sıfırlama](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) (SSPR), BT yöneticilerinin kullanıcıların yönetici müdahalesi olmadan parolalarını veya hesaplarını sıfırlamasına ve bunların kilidini açmalarına izin veren basit bir yol sunar. Sistem, kullanıcıların sisteme erişimini izleyen ayrıntılı raporlama içerir, ayrıca kötüye kullanım veya uygunsuz kullanım konusunda uyaran bildirimler sağlar.

Varsayılan olarak, Azure AD, parola sıfırlama gerçekleştirdiğinde hesapların kilidini açar. Ancak, [Şirket içinde Azure AD Connect tümleştirmeyi](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)etkinleştirdiğinizde, bu iki işlemi ayrı ayrı ayırın ve bu da kullanıcıların, parolayı sıfırlamak zorunda kalmadan hesabının kilidini açmalarına olanak sağlar.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>MFA ve SSPR için tüm kullanıcıların kayıtlı olduğundan emin olun

Azure, kullanıcıların MFA ve SSPR için Kaydolmakta olduğundan emin olmak için siz ve kuruluşunuz tarafından kullanılabilen raporlar sağlar. Kaydolmamış kullanıcıların süreç üzerinde eğitime yapması gerekebilir.

MFA [oturum açma işlemleri raporu](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) , MFA kullanımı hakkında bilgiler IÇERIR ve MFA 'nın kuruluşunuzda nasıl çalıştığı hakkında öngörüler sunar. Azure AD 'de oturum açma etkinliğine erişim (ve denetim ve risk algılamaları), sorun giderme, Kullanım Analizi ve adli araştırmalar için önemlidir.

Benzer şekilde, [self servis parola yönetimi raporu](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) , SSPR 'ye kimin sahip olduğunu (veya ayrılmadığını) belirlemede kullanılabilir.

### <a name="self-service-app-management"></a>Self Servis uygulama yönetimi

Kullanıcılarınızın erişim panelinden uygulamaları kendi kendine bulabilmesi için, kullanıcıların kendi kendine bulmasını ve erişim istemesini sağlamak istediğiniz uygulamalara [self servis uygulama erişimini](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) etkinleştirmeniz gerekir. Self Servis uygulama erişimi, kullanıcıların uygulamaları kendi kendine bulmasına ve isteğe bağlı olarak iş grubunun bu uygulamalara erişimi onaylamasını sağlayan harika bir yoldur. İş grubunun, erişim panellerinden, [parola çoklu oturum açma uygulamalarında](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) bu kullanıcılara atanan kimlik bilgilerini yönetmesine izin verebilirsiniz.

### <a name="self-service-group-management"></a>Self servis grup yönetimi

Kullanıcıların uygulamalara atanması en iyi şekilde, gruplar kullanılırken en iyi şekilde eşlenir, çünkü ölçeği büyük ölçüde esneklik ve yönetebilme olanağı sağlar:

* Dinamik grup üyeliği kullanılarak öznitelik tabanlı
* Uygulama sahiplerine yetkilendirme

Azure AD, güvenlik gruplarını ve Office 365 gruplarını kullanarak kaynaklara erişimi yönetme olanağı sağlar. Bu gruplar, üyelik isteklerini onaylayabilecek veya reddedebilen ve grup üyeliği denetimini atayan bir grup sahibi tarafından yönetilebilir. [Self servis grup yönetimi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)olarak bilindiğinde, bu özellik, yönetim rolü atanmamış Grup sahiplerinin, isteklerini işlemek üzere yöneticilere güvenmeksizin grupları oluşturup yönetmesine izin vererek zamandan tasarruf sağlar.

## <a name="step-4---operationalize-your-insights"></a>4\. adım-öngörülerinizi çalıştırma

Güvenlikle ilgili olayları ve ilgili uyarıları denetleme ve günlüğe kaydetme, kullanıcıların üretken kalmasını sağlamak ve kuruluşunuzun güvende kalması için etkili bir stratejinin önemli bileşenleridir. Güvenlik günlükleri ve raporları, şu gibi bir sorunun yanıtlanmasına yardımcı olabilir:

* Ne için ödeme yapabileceğinizi kullanıyorsunuz?
* Kiracımda şüpheli veya kötü amaçlı olan bir şey var mı?
* Güvenlik olayı sırasında kim etkilendi?

Güvenlik günlükleri ve raporları, size bir elektronik şüpheli etkinlik kaydı sağlar ve ağın denendiği veya başarılı bir şekilde dışarıdan sızma ve iç saldırılar olduğunu gösterebilen desenleri tespit etmenize yardımcı olur. Kullanıcı etkinliğini izlemek, mevzuat uyumluluğu, analiz yapmak ve daha fazlasını yapmak için denetimi kullanabilirsiniz. Uyarılar güvenlik olaylarının bildirimleri sağlar.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>İşlemler için en az ayrıcalıklı yönetici rolleri atama

İşlemlere yaklaşımınızı düşünürken göz önünde bulundurmanız gereken birkaç yönetim düzeyi vardır. İlk düzey yönetim yükünü genel yöneticinize koyar. Her zaman genel yönetici rolünü kullanarak, daha küçük şirketler için uygun olabilir. Ancak, yardım masası personeli ve yöneticiler için belirli görevlerden sorumlu olan büyük kuruluşlar için, genel yönetici rolünü atamak, bu bireyler üzerinde ve sonrasındaki görevleri yönetme olanağı sağladığından bir güvenlik riski oluşturabilir yapabilmeleri gereken özellikler.

Bu durumda, bir sonraki yönetim düzeyini göz önünde bulundurmanız gerekir. Azure AD 'yi kullanarak son kullanıcıları daha az ayrıcalıklı rollerdeki görevleri yönetebilen "sınırlı yöneticiler" olarak belirleyebilirsiniz. Örneğin, yardım masası personelinizi [güvenlik okuyucusu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) rolüne atayabilir ve bunlara salt okuma erişimiyle güvenlikle ilgili özellikleri yönetme olanağı sağlayabilirsiniz. Ya da, parola olmayan kimlik bilgilerini sıfırlama veya Azure hizmet durumunu okuma ve yapılandırma olanağı sağlamak için bireylere [kimlik doğrulama Yöneticisi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) rolünü atamak mantıklı bir fikir verir.

Daha fazla bilgi için [Azure Active Directory Içindeki yönetici rolü izinleri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)bölümüne bakın.

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Azure AD Connect Health kullanarak karma bileşenleri (Azure AD Connect eşitleme, AD FS) izleme

Azure AD Connect ve AD FS, yaşam döngüsü yönetimi ve kimlik doğrulaması olabilecek önemli bileşenlerdir ve sonuç olarak kesintiler kesintiye neden olabilir. Bu nedenle, bu bileşenlerin izlenmesi ve raporlanması için Azure AD Connect Health dağıtmanız gerekir.

Daha fazla bilgi edinmek için [Azure AD Connect Health kullanarak AD FS İzleyicisi](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)'ni okuyun.

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Analiz için veri günlüklerini toplamak üzere Azure Izleyici 'yi kullanma

[Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/overview) , ayrıntılı Öngörüler, gelişmiş analiz ve akıllı makine öğrenimi sağlayan tüm Azure AD günlükleri için birleştirilmiş bir izleme portalıdır. Azure Izleyici ile, kaynaklarınızın durumu ve performansına ilişkin daha fazla görünürlük elde etmek için Portal içindeki ölçümleri ve günlükleri kullanabilir ve API 'Ler aracılığıyla kullanabilirsiniz. Bu,, geleneksel üçüncü taraf SıEM sistemlerini destekleyen API 'Ler ve veri dışa aktarma seçenekleri aracılığıyla çok çeşitli ürün tümleştirmelerini etkinleştirerek portalda tek bir cam deneyim bölmesini etkinleştirir. Azure Izleyici aynı zamanda, kaynaklarınızı etkileyen sorunlar hakkında bildirim almak veya Otomatik eylemler gerçekleştirmek üzere uyarı kuralları yapılandırma olanağı da sağlar.

![Azure İzleyici](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Liderliğiniz ve güne ait gün için özel panolar oluşturun

SıEM çözümü olmayan kuruluşlar, Azure AD için [Power BI Içerik paketini](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) indirebilir. Power BI içerik paketi, kullanıcılarınızın Azure AD özelliklerini benimseme ve kullanma şeklini anlamanıza yardımcı olmak için önceden oluşturulmuş raporlar içerir. bu sayede, dizininizdeki tüm etkinliklere ilişkin Öngörüler elde etmenizi sağlayabilirsiniz. Ayrıca, kendi [özel panonuzu](https://docs.microsoft.com/power-bi/service-dashboards) oluşturabilir ve günlük etkinlikleri raporlamak için liderlik ekibinizle paylaşabilirsiniz. Panolar, işinizi izlemenin ve en önemli ölçümlerinizin tümünü bir bakışta görmenize yönelik harika bir yoldur. Bir panodaki görselleştirmeler, temel alınan bir veri kümesinden veya çok fazla, temel alınan bir rapordan veya çok sayıda olabilir. Bir Pano, şirket içi ve bulut verilerini birleştirerek verilerin nerede yaşına bakılmaksızın birleştirilmiş bir görünüm sağlar.

![Özel panoyu Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>Destek çağrı sürücülerinizi anlayın

Bu makalede özetlenen bir karma kimlik çözümü uyguladığınızda, destek aramalarınızın sonunda bir azalma olduğunu fark etmelisiniz. Unutulan parolalar ve hesap kilitlenmelerini gibi yaygın sorunlar, Azure 'un self servis parola sıfırlama uygulaması tarafından azaltıldığından, self servis uygulama erişiminin etkinleştirilmesi, kullanıcıların bağlı olmadan uygulamalara kendi kendine keşif ve erişim istemesine izin verir. BT personeliniz üzerinde.

Destek çağrılarında bir azalmayla karşılaşmıyorsanız, SSPR veya self servis uygulama erişiminin doğru şekilde yapılandırılıp yapılandırılmadığını veya sistematik olabilecek başka yeni sorunlar olup olmadığını onaylama girişiminde destek çağrı sürücülerinizi çözümlemeniz önerilir Belirtilen.

*"Dijital dönüşümde yolculuğunda, etkili bir ekosistem için ABD, iş ortakları ve bulut hizmeti sağlayıcıları arasında sorunsuz bir şekilde tümleştirmeyi kolaylaştırmak amacıyla güvenilir bir kimlik ve erişim yönetim sağlayıcısı gerekiyordu. Azure AD, riskleri algılamamızı ve yanıt verebilmesini sağlayan gerekli özellikleri ve görünürlüğü bize sunan en iyi seçenektir. "* --- [yazan almasri, küresel bilgi güvenlik Direktörü, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Öngörüleri yönlendirmek için uygulama kullanımınızı izleyin

Gölge BT 'yi bulmaya ek olarak, [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) kullanarak kuruluşunuzda uygulama kullanımını izlemek, bulut uygulamalarının taahhüdünü tamamen avantajlarından yararlanmak için kuruluşunuz hareket etmenize yardımcı olabilir. Etkinlik üzerinde geliştirilmiş görünürlük ve bulut uygulamaları genelinde kritik verilerin korunmasını artırmak için varlıklarınızı denetlemenize yardımcı olabilir. MCAS kullanarak kuruluşunuzda uygulama kullanımını izlemek aşağıdaki soruları cevaplamanıza yardımcı olabilir:

* Tasdikli olmayan uygulamalar, verileri depolamak için kullanılan çalışanlar mi?
* Gizli verilerin nerede ve ne zaman bulutta depolanıyor?
* Buluttaki hassas verilere kimler erişiyor?

*"Cloud App Security sayesinde, anormallikleri hızlı bir şekilde belirleyebiliyoruz ve işlem yapabilirsiniz."* --- [Eric Tapenske, üst düzey yönetici, bilgi güvenliği, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Özet

Karma kimlik çözümü uygulamak için birçok önemli nokta vardır, ancak bu dört adımlı denetim listesi, kullanıcıların daha üretken ve güvenli olmasını sağlayacak bir kimlik altyapısını hızlıca gerçekleştirmenize yardımcı olur.

* Uygulamalara kolayca bağlanın
* Her Kullanıcı için otomatik olarak bir kimlik oluşturun
* Kullanıcılarınıza güvenli bir şekilde güç katın
* Öngörülerinizi çalıştırın

Bu belgede, kuruluşunuz için güçlü bir kimlik temeli oluşturmaya yönelik yararlı bir yol haritası sunuyoruz.

## <a name="identity-checklist"></a>Kimlik denetim listesi

BT yolculuğuna kuruluşunuzda daha katı bir kimlik temele başladığınızda, başvuru için aşağıdaki denetim listesini yazdırmanızı öneririz.

### <a name="today"></a>Adresten

|Açtınız mı?|Öğe|
|:-|:-|
||Bir grup için pilot self servis parola sıfırlama (SSPR)|
||Azure AD Connect Health kullanarak karma bileşenleri izleme|
||İşlem için en az ayrıcalıklı yönetici rolleri atama|
||Microsoft Cloud App Security ile gölge BT 'yi bulma|
||Analiz için veri günlüklerini toplamak üzere Azure Izleyici 'yi kullanma|

### <a name="next-two-weeks"></a>Sonraki iki hafta

|Açtınız mı?|Öğe|
|:-|:-|
||Kullanıcılarınız için bir uygulamayı kullanılabilir hale getirme|
||Tercih ettiğiniz SaaS uygulaması için pilot Azure AD sağlama|
||Azure AD Connect için bir hazırlama sunucusu kurun ve güncel tutun|
||ADFS 'den Azure AD 'ye uygulama geçirmeye başlayın|
||Liderliğiniz ve güne ait gün için özel panolar oluşturun|

### <a name="next-month"></a>Sonraki ay

|Açtınız mı?|Öğe|
|:-|:-|
||Öngörüleri yönlendirmek için uygulama kullanımınızı izleyin|
||Uygulamalara yönelik pilot güvenli uzaktan erişim|
||MFA ve SSPR için tüm kullanıcıların kayıtlı olduğundan emin olun|
||Bulut kimlik doğrulamasını etkinleştir|

### <a name="next-three-months"></a>Sonraki üç ay

|Açtınız mı?|Öğe|
|:-|:-|
||Self Servis uygulama yönetimini etkinleştirme|
||Self Servis Grup yönetimini etkinleştir|
||Öngörüleri yönlendirmek için uygulama kullanımınızı izleyin|
||Destek çağrı sürücülerinizi anlayın|

## <a name="next-steps"></a>Sonraki adımlar

Azure Active Directory yeteneklerini ve bu beş adımlı denetim listesini kullanarak, [kimlik altyapınızın güvenliğini sağlamaya yönelik beş](https://aka.ms/securitysteps)adım aracılığıyla güvenli durunuzu nasıl artırabileceğinizi öğrenin.

Azure AD 'deki kimlik özelliklerinin, kuruluşların kimlik yönetimini geleneksel olarak hızlı bir şekilde benimsemesini ve taşımasını sağlayan çözümler ve yetenekler sağlayarak bulut yönetilen yönetimine geçişinizi hızlandırmanıza nasıl yardımcı olabileceğini öğrenin Azure AD 'de şirket içi sistemler için Azure AD, Şirket [Içi Iş yükleri Için bulut tarafından yönetilen yönetim sağlar](https://aka.ms/cloudgoverned).
