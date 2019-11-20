---
title: Azure Active Directory için otomatik Kullanıcı sağlama dağıtımı planlayın
description: Otomatik Kullanıcı sağlamayı planlama ve yürütmeye yönelik kılavuz
services: active-directory
author: martincoetzer
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: martinco
ms.reviewer: arvindha
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d1aec836f66ae2ebc007e920cf6ef8a4450919
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473333"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>Otomatik Kullanıcı sağlama dağıtımı planlayın

Birçok kuruluş, hizmet olarak yazılım (SaaS) uygulamalarını (ServiceNow, Zscaler ve son kullanıcı üretkenliği için bolluk) kullanır. Tarihsel BT personeli, CSV dosyalarını karşıya yükleme ya da her bir SaaS uygulamasındaki Kullanıcı kimliklerini güvenli bir şekilde yönetmek için özel betikler kullanma gibi el ile sağlama yöntemlerine güvendi. Bu süreçler hataya açık, güvensiz ve yönetimi zor bir hatadır.

Azure Active Directory (Azure AD) otomatik Kullanıcı sağlama, iş kurallarına göre SaaS uygulamalarındaki Kullanıcı kimliklerinin oluşturulmasını, bakımını ve kaldırılmasını güvenli bir şekilde otomatikleştirerek bu işlemi basitleştirir. Bu Otomasyon, bulut tabanlı çözümlere bağımlılarını genişlettikten sonra hem bulut hem de karma ortamlarda kimlik yönetimi sistemlerinizi etkili bir şekilde ölçeklendirmenize olanak tanır.

İşlevselliği daha iyi anlamak için, bkz. [SaaS uygulamalarına Kullanıcı hazırlama ve sağlamayı kaldırma Işlemlerini otomatik hale getirme Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

## <a name="learn"></a>Öğrenin

Kullanıcı hazırlama, sürekli kimlik yönetimi için bir temel oluşturur ve yetkili kimlik verilerine dayanan iş işlemlerinin kalitesini geliştirir.

### <a name="key-benefits"></a>Önemli avantajlar

Otomatik Kullanıcı sağlamayı etkinleştirmenin başlıca avantajları şunlardır:

* **Daha fazla üretkenlik**. Tek bir Kullanıcı sağlama yönetim arabirimiyle SaaS uygulamaları genelinde Kullanıcı kimliklerini yönetebilirsiniz. Bu arabirimin tek bir sağlama ilkeleri kümesi vardır.

* **Riski yönetin**. Rolleri ve/veya erişimi tanımlayan çalışan durumu veya grup üyelikleri temelinde değişiklikleri otomatikleştirerek güvenliği artırabilirsiniz.

* **Adres uyumluluğu ve idare**. Azure AD, her Kullanıcı sağlama isteği için yerel denetim günlüklerini destekler. İstekler hem kaynak hem de hedef sistemlerde yürütülür. Bu, tek bir ekrandan uygulamalara kimlerin erişebileceğini izlemenize olanak sağlar.

* **Maliyeti azaltın**. Otomatik Kullanıcı sağlama, el ile sağlama ile ilişkili verimsizlikleri ve insan hatasından kaçınarak maliyetleri azaltır. Özel olarak geliştirilmiş Kullanıcı sağlama çözümleri, betikler ve denetim günlükleri gereksinimini azaltır.

### <a name="licensing"></a>Lisanslama

Azure AD, Uygulama Galerisi menüsünde sağlanan şablonları kullanarak herhangi bir uygulamayla ilgili self servis tümleştirmesi sağlar. Lisans gereksinimlerinin tam listesi için bkz. [Azure AD lisanslama sayfası](https://azure.microsoft.com/pricing/details/active-directory/).

#### <a name="application-licensing"></a>Uygulama lisanslama

Otomatik olarak sağlamak istediğiniz uygulamalar için uygun lisanslara sahip olmanız gerekir. Uygulamaya atanan kullanıcıların uygulama rolleri için uygun lisanslara sahip olup olmadığını uygulama sahipleriyle tartışın. Azure AD, rollere göre otomatik sağlamayı yönetirse, Azure AD 'de atanan roller uygulama lisanslarına göre hizalanmalıdır. Uygulamaya ait yanlış lisanslar, bir kullanıcının sağlanması/güncelleştirilmesi sırasında hatalara neden olabilir.

### <a name="terms"></a>Koşullar

Bu makale aşağıdaki terimleri kullanır:

* CRUD işlemleri-Kullanıcı hesaplarında gerçekleştirilen eylemler: oluşturma, okuma, güncelleştirme, silme.

* Çoklu oturum açma (SSO)-bir kullanıcının bir kez oturum açması ve tüm SSO özellikli uygulamalara erişmesi özelliği. Kullanıcı hazırlama bağlamında, SSO otomatik Kullanıcı sağlama kullanan tüm sistemlere erişmek için tek bir hesaba sahip olan kullanıcıların bir sonucudur.

* Kaynak sistem-Azure AD tarafından sağlanan kullanıcıların Havuzu. Azure AD, önceden tümleştirilen sağlama bağlayıcıları için kaynak sistemidir. Ancak, SAP, Workday ve AWS gibi bulut uygulamaları için bazı özel durumlar vardır. Örneğin, [Workday 'den ad 'ye Kullanıcı hazırlama](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)bölümüne bakın.

* Hedef sistem-Azure AD tarafından sağlanan kullanıcı deposu. Hedef sistem genellikle ServiceNow, Zscaler ve bolluk gibi bir SaaS uygulamasıdır. Hedef sistem, AD gibi bir şirket içi sistem de olabilir.

* [Etki alanları arası kimlik yönetimi (SCıM) Için sistem](https://aka.ms/scimoverview) -Kullanıcı sağlama otomasyonuna izin veren bir açık standart. SCıM, Microsoft gibi kimlik sağlayıcıları ve Salesforce veya Kullanıcı kimlik bilgileri gerektiren diğer SaaS uygulamaları gibi hizmet sağlayıcıları arasında Kullanıcı kimlik verileri iletişim kurar.

### <a name="training-resources"></a>Eğitim kaynakları

| Kaynaklar| Bağlantı ve açıklama |
| - | - |
| İsteğe bağlı web seminerleri| [Kurumsal uygulamalarınızı Azure AD ile yönetme](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Azure AD 'nin, kurumsal SaaS uygulamalarınıza yönelik SSO ve erişim denetimi için en iyi yöntemleri almanıza nasıl yardımcı olabileceğini öğrenin. |
| Videolar| [Etkin Azure dizininde kullanıcı hazırlama nedir?](https://youtu.be/_ZjARPpI6NI) <br> [Active Directory 'de Kullanıcı hazırlama nasıl dağıtılır?](https://youtu.be/pKzyts6kfrw) <br> [Salesforce 'ı Azure AD ile tümleştirme: Kullanıcı sağlamasını otomatikleştirme](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| Çevrimiçi kurslar| SkillUp çevrimiçi: [kimlikleri yönetme](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Azure AD 'yi birçok SaaS uygulaması ile tümleştirmeyi ve bu uygulamalara Kullanıcı erişimini güvenli hale getirme hakkında bilgi edinin. |
| Kitaplar| [Web uygulamaları için Azure Active Directory (Geliştirici Başvurusu) 1. sürümde modern kimlik doğrulaması](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0).  <br> Bu yeni ortamlar için Active Directory kimlik doğrulama çözümleri oluşturmaya yönelik yetkili, derinlemesine bir kılavuzdur. |
| Öğreticiler| [SaaS uygulamalarını Azure AD ile tümleştirme hakkında öğreticilerin listesine](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)bakın. |
| SSS| Otomatik Kullanıcı sağlama hakkında [sık sorulan sorular](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) |

### <a name="solution-architectures"></a>Çözüm mimarileri

Azure AD sağlama hizmeti, her bir uygulama satıcısı tarafından sağlanan kullanıcı yönetimi API uç noktalarına bağlanarak kullanıcıları ve diğer sistemleri SaaS uygulamaları sağlar. Bu Kullanıcı yönetimi API uç noktaları, Azure AD 'nin Kullanıcı aracılığıyla kullanıcıları oluşturmasına, güncelleştirmesine ve kaldırmasına izin verir.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>Karma kuruluşlar için otomatik Kullanıcı hazırlama

Bu örnekte, kullanıcılar ve veya gruplar, şirket içi bir dizine bağlı bir ık veritabanında oluşturulur. Azure AD sağlama hizmeti, hedef SaaS uygulamalarına otomatik Kullanıcı sağlamayı yönetir.

 ![Kullanıcı hazırlama](media/auto-user-provision-dp/hybridprovisioning.png)

**İş akışının açıklaması:**

1. Kullanıcılar/Gruplar, SAP gibi bir şirket içi ık uygulaması/sisteminde oluşturulur. 

1. Azure AD Connect Aracısı, yerel AD 'den Azure AD 'ye olan kimlik (kullanıcılar ve gruplar) için zamanlanmış eşitlemeleri çalıştırır.

1. Azure AD sağlama hizmeti, kaynak sistem ve hedef sisteme karşı bir [Başlangıç döngüsüne](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) başlar. 

1. Azure AD sağlama hizmeti, ilk döngüden bu yana değiştirilen tüm kullanıcılar ve gruplar için kaynak sistemi sorgular ve [artımlı Döngülerde](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)değişiklikleri iter.

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>Yalnızca bulutta bulunan kuruluşlar için otomatik Kullanıcı hazırlama

Bu örnekte, Azure AD 'de Kullanıcı oluşturma oluşur ve Azure AD sağlama hizmeti, hedef (SaaS) uygulamalarına otomatik Kullanıcı sağlamasını yönetir:

![Resim 2](media/auto-user-provision-dp/cloudprovisioning.png)

**İş akışının açıklaması:**

1. Azure AD 'de Kullanıcı/grup oluşturulur.

1. Azure AD sağlama hizmeti, kaynak sistem ve hedef sisteme karşı bir [Başlangıç döngüsüne](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) başlar. 

1. Azure AD sağlama hizmeti, başlangıç döngüsünden bu yana güncelleştirilmiş tüm kullanıcılar ve gruplar için kaynak sistemi sorgular ve tüm [artımlı döngüleri](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)gerçekleştirir.

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>Cloud ık uygulamaları için otomatik Kullanıcı hazırlama 

Bu örnekte, kullanıcılar ve veya grupları Workday gibi bir bulut HR uygulamasında oluşturulur.

![Resim 2](media/auto-user-provision-dp/workdayprovisioning.png)

1. Bulut HR sisteminde oluşturulan hesaplar
1. Veriler, Azure AD sağlama hizmeti ve sağlama Aracısı aracılığıyla şirket içi AD 'ye akar.
1. Azure AD Connect verileri Azure AD 'ye eşitler
1. E-posta ve Kullanıcı adı özniteliği, bulut HR uygulamasına geri yazılabilir.

Çözüm mimarisi ve dağıtımı hakkında daha fazla bilgi için bkz. [öğretici: otomatik Kullanıcı sağlama Için Workday 'ı yapılandırma](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial).

## <a name="plan-the-deployment-project"></a>Dağıtım projesini planlayın

Ortamınızda Kullanıcı sağlamayı dağıtmaya yönelik stratejiyi öğrenmek için kuruluş ihtiyaçlarınızı göz önünde bulundurun.

### <a name="engage-the-right-stakeholders"></a>Doğru paydaşlara katılın

Teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri nedeniyle oluşur. Bu girişlerin oluşmasını önlemek için, [doğru paydaşları](https://aka.ms/deploymentplans) ve proje giriş ve accountatçlarını belgeleyerek projedeki paydaş rollerinin iyi anlaşıldığından emin olun.

### <a name="plan-communications"></a>İletişimleri planlayın

İletişim, her yeni hizmetin başarısı için önemlidir. Deneyimlerinizin nasıl değiştirileceği, ne zaman değiştirileceği ve sorunlarla karşılaştıkları durumlarda nasıl destek kazanabilecekleri hakkında daha etkin bir şekilde iletişim kurun.

### <a name="plan-a-pilot"></a>Pilot planı planlayın

Otomatik Kullanıcı sağlamanın ilk yapılandırmasının, üretim ortamındaki tüm kullanıcılara ölçeklendirmeden önce küçük bir kullanıcı alt kümesiyle bir test ortamında olmasını öneririz.

#### <a name="best-practices-for-a-pilot"></a>Pilot için en iyi uygulamalar  

Pilot, herkes için bir özelliği dağıtmaya başlamadan önce küçük bir grupla test etmenizi sağlar. Test etmenin bir parçası olarak, kuruluşunuzdaki her kullanım durumunun büyük bir şekilde sınandığından emin olun.

İlk dalga uygulamanızda BT 'yi, kullanılabilirliği ve geri bildirim sağlayabilen ve bunları sağlayabilecek diğer uygun kullanıcıları hedefleyebilirsiniz. Kullanıcılarınıza göndereceğiniz iletişim ve yönergeleri geliştirmek ve destek personelinizin görebileceği sorun türleri hakkında Öngörüler sağlamak için bu geri bildirimi kullanın.

Hedeflenmiş Grup kapsamını artırarak daha büyük Kullanıcı gruplarına dağıtımı genişletebilirsiniz. Bu, [dinamik grup üyeliği](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)aracılığıyla veya hedeflenen gruplara el ile Kullanıcı ekleyerek yapılabilir.

## <a name="plan-application-connections-and-administration"></a>Uygulama bağlantılarını ve yönetimini planlayın

Sağlamayı destekleyen tüm uygulamaları görüntülemek ve yönetmek için Azure AD portalını kullanın. Bkz. [portaldaki uygulamalarınızı bulma](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal).

### <a name="determine-the-type-of-connector-to-use"></a>Kullanılacak bağlayıcının türünü belirleme

Otomatik sağlamayı etkinleştirmek ve yapılandırmak için gereken gerçek adımlar uygulamaya bağlı olarak değişir. Otomatik olarak sağlamak istediğiniz uygulama [Azure AD SaaS uygulama galerisinde](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)listeleniyorsa, önceden tümleştirilmiş Kullanıcı sağlama bağlayıcısını yapılandırmak için [uygulamaya özgü tümleştirme öğreticisini](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) seçmeniz gerekir.

Aksi takdirde, aşağıdaki adımları izleyin:

1. Önceden tümleştirilmiş Kullanıcı sağlama Bağlayıcısı için [bir Istek oluşturun](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing) . Ekibimiz, SCıM destekliyorsa, uygulamanızı platformumuza eklemek için sizinle ve uygulama geliştiricimize çalışacağız.

1. Uygulama için [Byoa SCıM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) genel Kullanıcı sağlama desteğini kullanın. Bu, Azure AD 'nin uygulamayı önceden tümleşik sağlama Bağlayıcısı olmadan uygulamaya sağlaması için bir gereksinimdir.

1. Uygulama BYOA SCıM bağlayıcısını kullanabiliyor ise, uygulamanın BYOA SCIM bağlayıcısını yapılandırmak için [Byoa SCIM tümleştirme öğreticisine](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) bakın.

Daha fazla bilgi için bkz. [Azure AD otomatik Kullanıcı sağlama Ile hangi uygulamaları ve sistemleri kullanabilirim?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

### <a name="collect-information-to-authorize-application-access"></a>Uygulama erişimini yetkilendirmek için bilgi toplayın

Otomatik Kullanıcı sağlamayı ayarlama, uygulama başına bir işlemdir. Her uygulama için, hedef sistemin Kullanıcı yönetimi uç noktasına bağlanmak üzere [yönetici kimlik bilgilerini](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) sağlamanız gerekir.

Aşağıdaki görüntüde gerekli yönetici kimlik bilgilerinin bir sürümü gösterilmektedir:

![Kullanıcı hesabı sağlama ayarlarını yönetmek için sağlama ekranı](media/auto-user-provision-dp/userprovisioning-admincredentials.png)

Bazı uygulamalar Yönetici Kullanıcı adı ve parolası gerektirdiğinden, diğerleri bir taşıyıcı belirteç gerektirebilir.

## <a name="plan-user-and-group-provisioning"></a>Kullanıcı ve grup sağlamayı planlayın

Kurumsal uygulamalar için Kullanıcı sağlamayı etkinleştirirseniz [Azure Portal](https://portal.azure.com/) , öznitelik değerlerini öznitelik eşlemesi aracılığıyla denetler.

### <a name="determine-operations-for-each-saas-app"></a>Her bir SaaS uygulaması için işlemleri belirleme

Her uygulama, Azure AD 'deki özniteliklerle eşlenmesi gereken benzersiz kullanıcı veya grup özniteliklerine sahip olabilir. Uygulamanın yalnızca CRUD işlemleri kullanılabilir bir alt kümesi olabilir.

Her uygulama için aşağıdaki bilgileri belgeleyin:

* Hedef sistemler için Kullanıcı ve grup nesnelerinde gerçekleştirilecek CRUD sağlama işlemleri. Örneğin, her bir SaaS uygulaması işletme sahibi olası tüm işlemleri istemiyor olabilir.

* Kaynak sistemde kullanılabilir öznitelikler

* Hedef sistemde kullanılabilir öznitelikler

* Sistemler arasındaki özniteliklerin eşlemesi.

### <a name="choose-which-users-and-groups-to-provision"></a>Sağlanacak kullanıcıları ve grupları seçin

Otomatik Kullanıcı sağlamayı uygulamadan önce, uygulamanıza sağlanacak kullanıcıları ve grupları belirlemelisiniz.

* Bir uygulamaya hangi kullanıcıların sağlandığını belirleyen öznitelik tabanlı kurallar tanımlamak için [kapsam filtrelerini](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters) kullanın.

* Daha sonra, ek filtreleme için gerektiğinde [Kullanıcı ve grup atamalarını](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) kullanın.

### <a name="define-user-and-group-attribute-mapping"></a>Kullanıcı ve Grup özniteliği eşlemesini tanımla

Otomatik Kullanıcı sağlamayı uygulamak için, uygulama için gereken kullanıcı ve grup özniteliklerini tanımlamanız gerekir. Azure AD Kullanıcı nesneleri ve her bir SaaS uygulamasının Kullanıcı nesneleri arasında önceden yapılandırılmış bir öznitelikler ve [öznitelik eşlemeleri](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) kümesi vardır. Tüm SaaS uygulamaları grup özniteliklerini etkinleştirmez.

Azure AD, doğrudan öznitelik eşleme, sabit değerler sağlama veya [öznitelik eşlemeleri için ifadeler yazma](https://docs.microsoft.com/azure/active-directory/active-directory-saas-writing-expressions-for-attribute-mappings)tarafından desteklenir. Bu esneklik, hedeflenen sistemin özniteliğinde doldurulacak nelerin üzerinde ince denetim elde etmenizi sağlar. Kullanıcı hazırlama öznitelik eşlemelerinizi ve şemanızı bir JSON dosyasına aktarmak ve yeniden Azure AD 'ye aktarmak için [MICROSOFT Graph API](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration) ve Graf Gezginini kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure Active Directory Içindeki SaaS uygulamaları Için Kullanıcı hazırlama özniteliğini özelleştirme-eşlemeler](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

### <a name="special-considerations-for-user-provisioning"></a>Kullanıcı sağlamaya yönelik özel konular

Dağıtım sonrası sorunları azaltmak için aşağıdakileri göz önünde bulundurun:

* Kaynak ve hedef uygulamalar arasında Kullanıcı/Grup nesnelerini eşlemek için kullanılan özniteliklerin dayanıklı olduğundan emin olun. Öznitelikler değiştiğinde kullanıcıların/grupların hatalı sağlanması gerekmez (örneğin, bir Kullanıcı Şirketin farklı bir bölümüne taşınırsa).

* Uygulamalar, Kullanıcı sağlamanın doğru çalışması için sağlanması gereken belirli kısıtlamalara ve/veya gereksinimlere sahip olabilir. Örneğin, belirli özniteliklerin değerlerini atar. Her bir uygulamaya özgü [Otomatik Kullanıcı sağlama öğreticilerine](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) bakın.

* Kaynak ve hedef sistemler arasındaki şema tutarlılığını onaylayın. Genel sorunlar UPN veya posta eşleştirme gibi öznitelikleri içerir. Örneğin, Azure AD kümesindeki UPN *john_smith@contoso.com* olarak ve uygulamada, *jsmith@contoso.com* . Daha fazla bilgi için bkz. [Kullanıcı ve grup şeması başvurusu](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups).

## <a name="plan-testing-and-security"></a>Test ve güvenlik planlaması

Dağıtımınızın her aşamasında, sonuçların beklenildiği şekilde test olduğunuzdan ve sağlama döngülerini denetdiğinizden emin olun.

### <a name="plan-testing"></a>Test planı

Uygulama için otomatik Kullanıcı sağlamayı yapılandırdıktan sonra, bu çözümün kuruluşunuzun gereksinimlerini karşıladığından emin olmak için test çalışmalarını çalıştırırsınız.

| Senaryolar| Beklenen sonuçlar |
| - | - |
| Kullanıcı, hedef sisteme atanan bir gruba eklendi | Kullanıcı nesnesi hedef sistemde sağlanıyor. <br>Kullanıcı, hedef sistemde oturum açıp istenen eylemleri gerçekleştirebilir. |
| Kullanıcı, hedef sisteme atanan bir gruptan kaldırıldı | Kullanıcı nesnesi hedef sistemde hazırlandı.<br>Kullanıcı hedef sistemde oturum açamıyor. |
| Kullanıcı bilgileri, Azure AD 'de herhangi bir yöntemle güncelleştirilir | Güncelleştirilmiş Kullanıcı öznitelikleri, artımlı bir döngüden sonra hedef sisteme yansıtılır |
| Kullanıcı kapsam dışında | Kullanıcı nesnesi devre dışı veya silinmiş. <br>Note: Bu davranış, [Workday sağlaması](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)için geçersiz kılınır. |

### <a name="plan-security"></a>Plan güvenliği

Bir dağıtımın parçası olarak bir güvenlik incelemesinin gerekli olması yaygındır. Bir güvenlik incelemesi gerekiyorsa, hizmet olarak kimlik için bir genel bakış sağlayan birçok Azure AD [Teknik](https://www.microsoft.com/download/details.aspx?id=36391) incelemesine bakın.

### <a name="plan-rollback"></a>Planı geri alma

Otomatik Kullanıcı sağlama uygulamasının üretim ortamında istendiği şekilde çalışması başarısız olursa, aşağıdaki geri alma adımları önceki bilinen iyi duruma geri dönmede size yardımcı olabilir:

1. Etkilenen kullanıcılar ve/veya gruplarda hatalı işlemlerin ne olduğunu belirlemek için [sağlama Özeti raporunu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) ve [sağlama günlüklerini](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting) gözden geçirin.

1. Etkilenen Kullanıcı ve/veya grupların bilinen son iyi durumunu öğrenmek için sağlama denetim günlüklerini kullanın. Ayrıca, kaynak sistemleri (Azure AD veya AD) gözden geçirin.

1. En son bilinen iyi durum değerlerini kullanarak uygulamada doğrudan etkilenen kullanıcıları ve/veya grupları güncelleştirmek için uygulama sahibiyle birlikte çalışın.

## <a name="deploy-automatic-user-provisioning-service"></a>Otomatik Kullanıcı sağlama hizmeti 'ni dağıtma

Çözüm gereksinimlerinize göre hizalamaya yönelik adımları seçin.

### <a name="prepare-for-the-initial-cycle"></a>İlk döngüye hazırlanma

Azure AD sağlama hizmeti ilk kez çalıştırıldığında, kaynak sistem ve hedef sistemlere yönelik ilk geçiş, her hedef sistem için tüm Kullanıcı nesnelerinin anlık görüntüsünü oluşturur.

Bir uygulama için otomatik sağlamayı etkinleştirirken, ilk zaman 20 dakikadan birkaç saate kadar sürebilir. Süre, Azure AD dizininin boyutuna ve sağlama için kapsamdaki kullanıcı sayısına bağlıdır. Bkz. [sağlama performansını geliştirme](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish).

Sağlama Hizmeti, başlangıç döngüsünden sonra her iki sistemin durumunu depolar, sonraki artımlı döngülerin performansını geliştirir.

### <a name="configure-automatic-user-provisioning"></a>Otomatik kullanıcı sağlamayı yapılandırma

Otomatik Kullanıcı hesabı sağlamayı ve bunu destekleyen uygulamalar için ön sağlamayı yönetmek üzere [Azure Portal](https://portal.azure.com/) kullanın. [Bir uygulamaya otomatik sağlamayı ayarlama nasıl yaparım?](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) içindeki adımları izleyin.

Azure AD Kullanıcı sağlama hizmeti, [Microsoft Graph API 'si](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)kullanılarak da yapılandırılabilir ve yönetilebilir.

## <a name="manage-automatic-user-provisioning"></a>Otomatik Kullanıcı sağlamayı yönetme

Artık dağıtıldıktan sonra çözümü yönetmeniz gerekir.

### <a name="monitor-user-provisioning-operation-health"></a>Kullanıcı hazırlama işlemi sistem durumunu izleme

Başarılı bir [Başlangıç döngüsünden](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)sonra, Azure AD sağlama hizmeti, aşağıdaki olaylardan biri gerçekleşene kadar her bir uygulamaya özgü aralıklarla Artımlı güncelleştirmeleri süresiz olarak çalıştırır:

* Hizmet el ile durdurulur ve [Azure Portal](https://portal.azure.com/)kullanılarak veya uygun [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) komutu kullanılarak yeni bir başlangıç çevrimi tetiklenir.

* Yeni bir başlangıç çevrimi, öznitelik eşlemelerinde veya kapsam filtrelerinde değişiklik tarafından tetiklenir.

* Sağlama işlemi, yüksek hata oranı nedeniyle karantinaya alınır ve otomatik olarak devre dışı bırakıldığında dört haftadan uzun bir süre boyunca karantinaya alınır.

Bu olayları ve sağlama hizmeti tarafından gerçekleştirilen diğer tüm etkinlikleri gözden geçirmek için Azure AD [sağlama günlükleri](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/manage-apps/context/manage-apps-context)' ne bakın.

Sağlama döngüsü süresini anlamak ve sağlama işinin ilerlemesini izlemek için [Kullanıcı hazırlama durumunu kontrol](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)edebilirsiniz.

### <a name="gain-insights-from-reports"></a>Raporlardan Öngörüler elde edin

Azure AD, kuruluşunuzun Kullanıcı sağlama kullanımı ve denetim günlükleri ve raporları aracılığıyla işletimsel sistem durumu hakkında [ek Öngörüler](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) sağlayabilir.

Yöneticiler, sağlama işinin işletimsel durumunu izlemek için sağlama Özeti raporunu denetlemelidir. Sağlama hizmeti tarafından gerçekleştirilen tüm etkinlikler Azure AD denetim günlüklerine kaydedilir. Bkz. [öğretici: otomatik Kullanıcı hesabı sağlamayı bildirme](https://docs.microsoft.com/azure/active-directory/manage-apps/check-status-user-account-provisioning).

Kuruluşunuzun gereksinimlerini karşılayan bir temposunda üzerinde sahipliğini ve bu raporların kullanımını varsaymasını öneririz. Azure AD, en çok denetim verilerini 30 gün boyunca tutar.

### <a name="troubleshoot"></a>Sorun giderme

Sağlama sırasında oluşabilecek sorunları gidermek için aşağıdaki bağlantılara bakın:

* [Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma sorunu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)

* [Bir uygulamaya sağlamak için şirket içi Active Directory bir özniteliği Azure AD 'ye eşitleyin](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning-sync-attributes-for-mapping)

* [Azure AD Galeri uygulamasına Kullanıcı hazırlama saati veya daha fazlasını sürüyor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish)

* [Azure Active Directory Galeri uygulamasına kullanıcı sağlamayı yapılandırırken yönetici kimlik bilgileri kaydedilirken sorun oluştu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-storage-limit)

* [Azure AD Galeri uygulamasına hiçbir Kullanıcı sağlanmıyor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned)

* [Azure AD Galeri uygulamasına yanlış kullanıcı kümesi sağlanıyor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-wrong-users-provisioned)

### <a name="helpful-documentation"></a>Faydalı belgeler

* [Öznitelik eşlemeleri için ifadeler yazma](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)

* [Azure AD eşitleme API 'sine genel bakış](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [Kapsam dışına çıkan Kullanıcı hesaplarını silmeyi atlayın](https://docs.microsoft.com/azure/active-directory/manage-apps/skip-out-of-scope-deletions)

* [Azure AD Connect sağlama Aracısı: sürüm yayınlama geçmişi](https://docs.microsoft.com/azure/active-directory/manage-apps/provisioning-agent-release-version-history)

#### <a name="resources"></a>Kaynaklar

* [Ürün geri bildirimi sağlama](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Azure AD 'deki yenilikleri güncel tutun](https://azure.microsoft.com/updates/?product=active-directory)

* [Yığın taşması Azure AD Forumu](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>Sonraki adımlar
* [Otomatik Kullanıcı sağlamayı yapılandırma](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)

* [Microsoft Graph API kullanarak sağlama yapılandırmanızı dışarı aktarma veya içeri aktarma](https://docs.microsoft.com/azure/active-directory/manage-apps/export-import-provisioning-configuration)

* [Azure Active Directory 'de öznitelik eşlemeleri için ifadeler yazma](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)
