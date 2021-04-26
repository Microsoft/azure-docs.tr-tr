---
title: Kullanıcı sağlamayı Azure Active Directory için bulut HR uygulaması planlayın
description: Bu makalede, Azure Active Directory ile Workday ve başarılı etkenler gibi bulut HR sistemlerini tümleştirme dağıtım süreci açıklanmaktadır. Azure AD 'yi bulut HR sistemiyle tümleştirmek, bir kimlik yaşam döngüsü yönetim sistemiyle sonuçlanır.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256720"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Kullanıcı sağlamayı Azure Active Directory için bulut HR uygulaması planlayın

Geçmişte, BT personeli çalışanları oluşturmak, güncelleştirmek ve silmek için el ile gerçekleştirilen yöntemlere güvendi. Çalışan verilerini eşitlemek için CSV dosyalarını veya özel betikleri karşıya yükleme gibi yöntemleri kullandık. Bu sağlama işlemlerinde hata açık, güvensiz ve yönetimi zor.

Çalışanların, satıcıların veya contentik çalışanların kimlik yaşam döngülerini yönetmek için [Azure Active Directory (Azure AD) Kullanıcı sağlama hizmeti](../app-provisioning/user-provisioning.md) , bulut tabanlı insan kaynakları (hr) uygulamalarıyla tümleştirme sunar. Uygulama örnekleri, Workday veya başarılı faktörleri içerir.

Azure AD, aşağıdaki bulut HR uygulaması (uygulama) iş akışlarını etkinleştirmek için bu tümleştirmeyi kullanır:

- **Kullanıcıları Active Directory sağla:** Bir bulut HR uygulamasındaki seçili kullanıcı kümelerini bir veya daha fazla Active Directory etki alanına sağlayın.
- **Azure AD 'ye yalnızca bulutta Kullanıcı sağlama:** Active Directory kullanılmayan senaryolarda, kullanıcıları doğrudan Cloud HR uygulamasından Azure AD 'ye sağlayın.
- **Cloud HR uygulamasına geri yaz:** Azure AD 'den e-posta adreslerini ve Kullanıcı adı özniteliklerini Cloud HR uygulamasına geri yazın.

> [!NOTE]
> Bu dağıtım planı, Azure AD Kullanıcı sağlama ile bulut HR uygulaması iş akışlarınızı nasıl dağıtacağınızı gösterir. Hizmet olarak yazılım (SaaS) uygulamalarına otomatik Kullanıcı sağlamayı dağıtma hakkında daha fazla bilgi için bkz. [Otomatik Kullanıcı sağlama dağıtımı planı](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Etkin HR senaryoları

Azure AD Kullanıcı sağlama hizmeti, aşağıdaki HR tabanlı kimlik yaşam döngüsü yönetimi senaryolarına yönelik Otomasyonu sunar:

- **Yeni çalışan işe alım:** Bulut HR uygulamasına yeni bir çalışan eklendiğinde, Active Directory ve Azure AD 'de otomatik olarak bir kullanıcı hesabı oluşturulur. Bu seçenek, e-posta adresi ve Kullanıcı adı özniteliklerini bulut HR uygulamasına geri yazma seçeneğiyle birlikte otomatik olarak oluşturulur.
- **Çalışan özniteliği ve profil güncelleştirmeleri:** Bulut HR uygulamasında ad, başlık veya yönetici gibi bir çalışan kaydı güncelleştirilirse, Kullanıcı hesapları Active Directory ve Azure AD 'de otomatik olarak güncelleştirilir.
- **Çalışan sonlandırmaları:** Bulut HR uygulamasında bir çalışan sonlandırıldığında, Kullanıcı hesapları Active Directory ve Azure AD 'de otomatik olarak devre dışı bırakılır.
- **Çalışan rehires:** Bulut HR uygulamasında bir çalışan yeniden işe alındığında, eski hesapları otomatik olarak yeniden etkinleştirilebilir veya Active Directory ve Azure AD 'ye yeniden sağlanabilir.

## <a name="who-is-this-integration-best-suited-for"></a>Bu tümleştirme kim için en uygun?

Azure AD Kullanıcı sağlama ile bulut HR uygulaması tümleştirmesi ideal olan kuruluşlar için idealdir:

- Bulut HR Kullanıcı sağlama için önceden oluşturulmuş, bulut tabanlı bir çözüm ister.
- Bulut HR uygulamasından Active Directory veya Azure AD 'ye doğrudan kullanıcı sağlamayı gerektir.
- Kullanıcılardan bulut HR uygulamasından elde edilen verileri kullanarak sağlanması gerekir.
- Yalnızca bulut HR uygulamasında algılanan değişiklik bilgilerini temel alan bir veya daha fazla Active Directory ormanlara, etki alanına ve OU 'Lara katılmayı zorunlu kılın.
- E-posta için Microsoft 365 kullanın.

## <a name="learn"></a>Learn

Kullanıcı hazırlama devam eden kimlik yönetimi için bir temel oluşturur. Yetkili kimlik verilerine dayanan iş işlemlerinin kalitesini geliştirir.

### <a name="terms"></a>Terimler

Bu makale aşağıdaki terimleri kullanır:

- **Kaynak sistem**: Azure AD tarafından sağlanan kullanıcıların Havuzu. Örnek olarak, Workday veya başarılı etmenler gibi bir bulut HR uygulamasıdır.
- **Hedef sistem**: Azure AD tarafından sağlanan kullanıcıların deposu. Örnekler şunlardır Active Directory, Azure AD, Microsoft 365 veya diğer SaaS uygulamaları.
- **Birleştiriciler-Taşımacılar**: BIR bulut HR uygulamasını bir kayıt sistemi olarak kullanarak yeni Hires, aktarımlar ve sonlandırma için kullanılan bir terim. Hizmet, hedef sisteme gerekli öznitelikleri başarıyla sağlarken işlem tamamlanır.

### <a name="key-benefits"></a>Önemli avantajlar

Bu HR-odaklı BT sağlama özelliği, aşağıdaki önemli iş avantajlarını sunmaktadır:

- **Üretkenliği artırın:** Artık Kullanıcı hesaplarının ve Microsoft 365 lisansların atanmasını otomatikleştirebilir ve anahtar gruplarına erişim sağlayabilirsiniz. Atamaları otomatik hale getirmek, iş araçlarına yeni bir anında erişim sağlar ve üretkenliği artırır.
- **Riski yönetin:** Bulut HR uygulamasından gelen verilerle akan çalışanların durum veya grup üyeliklerine göre değişiklikleri otomatikleştirerek güvenliği artırabilirsiniz. Değişikliklerin otomatikleştirilmesi, kullanıcılar kuruluştan geçiş yaparken veya kuruluştan ayrıldığınızda, anahtar uygulamalarına yönelik kullanıcı kimliklerinin ve erişimin otomatik olarak güncelleştirilmesini sağlar.
- **Adres uyumluluğu ve idare:** Azure AD, hem kaynak hem de hedef sistemlerin uygulamaları tarafından gerçekleştirilen Kullanıcı hazırlama istekleri için yerel denetim günlüklerini destekler. Denetimle, tek bir ekrandan uygulamalara kimlerin erişebileceğini izleyebilirsiniz.
- **Maliyeti yönetme:** Otomatik sağlama, el ile sağlama ile ilişkili verimsizlikleri ve insan hatasından kaçınarak maliyetleri azaltır. Eski ve süresi geçmiş platformlar kullanılarak zaman içinde oluşturulmuş özel geliştirmiş Kullanıcı sağlama çözümleri gereksinimini azaltır.

### <a name="licensing"></a>Lisanslama

Bulut HR uygulamasını Azure AD Kullanıcı sağlama tümleştirmesi olarak yapılandırmak için, geçerli bir [Azure AD Premium lisansına](https://azure.microsoft.com/pricing/details/active-directory/) ve iş günü veya başarılı faktörlerle bulut HR uygulaması için lisansa sahip olmanız gerekir.

Ayrıca, bulut HR uygulamasından kaynaklayacağı ve Active Directory ya da Azure AD 'ye sağlanan her kullanıcı için geçerli Azure AD Premium P1 veya daha yüksek bir abonelik lisansı gerekir. Bulut HR uygulamasına ait hatalı sayıda lisans, Kullanıcı sağlama sırasında hatalara yol açabilir.

### <a name="prerequisites"></a>Önkoşullar

- Azure AD Connect sağlama aracısını yapılandırmak için Azure AD [karma kimlik yöneticisi](../roles/permissions-reference.md#hybrid-identity-administrator)  .
- Azure portal sağlama uygulamasını yapılandırmak için Azure AD [Uygulama Yöneticisi](../roles/permissions-reference.md#application-administrator) rolü
- Bulut HR uygulamasının test ve üretim örneği.
- Bulut HR uygulamasındaki Yönetici izinleri bir sistem tümleştirme kullanıcısı oluşturur ve test amacıyla çalışan verilerini test etmek için değişiklikler yapar.
- Active Directory Kullanıcı sağlaması için, Azure AD Connect sağlama aracısını barındırmak üzere .NET 4.7.1 + Runtime ile Windows Server 2012 veya üstünü çalıştıran bir sunucu gerekir
- Kullanıcıları Active Directory ve Azure AD arasında eşitlemeye yönelik [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) .

### <a name="training-resources"></a>Eğitim kaynakları

| **Kaynaklar** | **Bağlantı ve açıklama** |
|:-|:-|
| Videolar | [Etkin Azure dizininde kullanıcı hazırlama nedir?](https://youtu.be/_ZjARPpI6NI) |
| | [Etkin Azure dizininde kullanıcı hazırlama dağıtımı](https://youtu.be/pKzyts6kfrw) |
| Öğreticiler | [SaaS uygulamalarını Azure AD ile tümleştirme hakkında öğreticiler listesi](../saas-apps/tutorial-list.md) |
| | [Öğretici: otomatik Kullanıcı sağlama için Workday yapılandırma](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| SSS | [Otomatik Kullanıcı sağlama](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Workday 'den Azure AD 'ye sağlama](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Çözüm mimarisi

Aşağıdaki örnek, genel karma ortamlar için uçtan uca Kullanıcı sağlama çözüm mimarisini açıklar ve şunları içerir:

- **Bulut HR uygulamasından Active Directory 'e kadar yetkili ık veri akışı.** Bu akışta, HR olayı (Joiners-broşür-Leavers işlemi), Cloud HR App kiracısında başlatılır. Azure AD sağlama hizmeti ve Azure AD Connect sağlama Aracısı, Kullanıcı verilerini Cloud ık uygulama kiracısından Active Directory olarak temin edin. Olaya bağlı olarak, Active Directory işlemleri oluşturma, güncelleştirme, etkinleştirme ve devre dışı bırakma işlemlerine neden olabilir.
- **Azure AD ile eşitleyin ve şirket içi Active Directory bulut HR uygulamasına geri e-posta ve Kullanıcı adı yazın.** Hesaplar Active Directory güncelleştirildikten sonra Azure AD ile Azure AD Connect arasında eşitlenir. E-posta adresleri ve Kullanıcı adı öznitelikleri, bulut HR uygulaması kiracıya geri yazılabilir.

![İş akışı diyagramı](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>İş akışının açıklaması

Diyagramda aşağıdaki anahtar adımları belirtilmiştir:  

1. **HR ekibi** , bulut HR uygulama kiracısında işlemleri gerçekleştirir.
2. **Azure AD sağlama hizmeti** , bulut HR App kiracısından zamanlanan döngüleri çalıştırır ve Active Directory eşitleme için işlenmesi gereken değişiklikleri belirler.
3. **Azure AD sağlama hizmeti** , Active Directory hesabı oluşturma, güncelleştirme, etkinleştirme ve devre dışı bırakma işlemlerini içeren bir istek yüküyle Azure AD Connect sağlama aracısını çağırır.
4. **Azure AD Connect sağlama aracısı** Active Directory hesabı verilerini yönetmek için bir hizmet hesabı kullanır.
5. **Azure AD Connect** , güncelleştirmeleri Active Directory çekmek için Delta [eşitlemesi](../hybrid/how-to-connect-sync-whatis.md) çalıştırır.
6. **Active Directory** GÜNCELLEŞTIRMELER Azure AD ile eşitlenir.
7. **Azure AD sağlama hizmeti** , Azure AD 'den e-posta özniteliği ve Kullanıcı adı ' nı Cloud HR App kiracısına yazar.

## <a name="plan-the-deployment-project"></a>Dağıtım projesini planlayın

Ortamınızda bu dağıtımın stratejisini belirlerken kurumsal ihtiyaçlarınızı göz önünde bulundurun.

### <a name="engage-the-right-stakeholders"></a>Doğru paydaşlara katılın

Teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri için bu işlem yapılır. Bu sınırları önlemek için, [doğru paydaşları ilgi çekici olduğunuzdan emin olun](../fundamentals/active-directory-deployment-plans.md). Ayrıca, projedeki paydaş rollerinin iyi anlaşıldığından emin olun. Paydaşları ve proje giriş ve accountikleri belgeleyin.

IK kuruluştan, mevcut ık iş süreçlerine ve çalışan kimliğine ve iş verileri işleme gereksinimlerine giriş sağlayabilen bir temsilci ekleyin.

### <a name="plan-communications"></a>İletişimi planlama

İletişim, her yeni hizmetin başarısı için önemlidir. Deneyimlerinizin ne zaman ve nasıl değiştirileceği hakkında kullanıcılarınız ile etkin bir şekilde iletişim kurun. Sorunlarla karşılaştıklarında nasıl destek elde edebileceklerini öğrenin.

### <a name="plan-a-pilot"></a>Pilot planı planlayın

Bulut HR uygulamasından hedef sistemlere ık iş süreçlerini ve kimlik iş akışlarını tümleştirmek, çözümü üretime dağıtabilmeniz için önemli miktarda veri doğrulaması, veri dönüştürme, veri temizleme ve uçtan uca test gerektirir.

İlk yapılandırmayı üretimdeki tüm kullanıcılara ölçeklendirmadan önce bir [pilot ortamda](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) çalıştırın.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Cloud HR sağlama bağlayıcı uygulamalarını seçin

Bulut HR uygulaması ve Active Directory arasında Azure AD sağlama iş akışlarını kolaylaştırmak için Azure AD uygulama galerisinden birden çok sağlama bağlayıcı uygulaması ekleyebilirsiniz:

- **Bulut HR uygulaması Kullanıcı sağlamasını Active Directory için**: Bu sağlama bağlayıcı uygulaması, bulut HR uygulamasından tek bir Active Directory etki alanına kullanıcı hesabı sağlamayı kolaylaştırır. Birden çok etki alanınız varsa, sağlamanız gereken her Active Directory etki alanı için Azure AD uygulama galerisinden bu uygulamanın bir örneğini ekleyebilirsiniz.
- **Bulut HR uygulaması Için Azure AD Kullanıcı sağlama**: Azure AD Connect Active Directory KULLANıCıLARı Azure AD 'ye eşitlemede kullanılması gereken bir araç olmakla kalmaz, bu sağlama bağlayıcı uygulaması yalnızca bulutta bulunan KULLANıCıLARıN bulut HR uygulamasından tek BIR Azure AD kiracısına sağlanması için kullanılabilir.
- **Cloud HR uygulaması geri yazma**: Bu sağlama bağlayıcı uygulaması, kullanıcının Azure AD 'den bulut HR uygulamasına olan e-posta adreslerini geri yazmasını kolaylaştırır.

Örneğin, aşağıdaki görüntüde Azure AD uygulama galerisinde bulunan Workday Bağlayıcısı uygulamaları listelenmektedir.

![Azure Active Directory Portal Uygulama Galerisi](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Karar akışı grafiği

Hangi bulut HR sağlama uygulamalarının senaryolarınızla ilgili olduğunu belirlemek için aşağıdaki karar akışı grafiğini kullanın.

![Karar akışı grafiği](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Azure AD Connect sağlama Aracısı dağıtım topolojisini tasarlama

Bulut HR uygulaması ve Active Directory arasındaki sağlama tümleştirmesi için dört bileşen gerekir:

- Cloud ık uygulama kiracısı
- Bağlayıcı uygulaması sağlanıyor
- Azure AD Connect sağlama Aracısı
- Active Directory etki alanı

Azure AD Connect sağlama Aracısı dağıtım topolojisi, tümleştirmeyi planladığınız bulut HR uygulama kiracılarının ve Active Directory alt etki alanlarının sayısına bağlıdır. Birden çok Active Directory etki alanı varsa, Active Directory etki alanlarının bitişik veya [ayrık](/windows-server/identity/ad-ds/plan/disjoint-namespace)olmasına bağlıdır.

Kararınız temelinde dağıtım senaryolarından birini seçin:

- Tek bulut HR App Tenant-> güvenilen bir ormandaki tek veya birden çok Active Directory alt etki alanını hedefleme
- Tek bulut HR App Tenant-> ayrık Active Directory ormanında birden çok alt etki alanını hedefleme

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Tek bulut HR App Tenant-> güvenilen bir ormandaki tek veya birden çok Active Directory alt etki alanını hedefleme

Aşağıdaki üretim yapılandırması önerilir:

|Gereksinim|Öneri|
|:-|:-|
|Dağıtılacak Azure AD Connect sağlama aracılarının sayısı|İki (yüksek kullanılabilirlik ve yük devretme için)
|Yapılandırılacak sağlama bağlayıcı uygulamalarının sayısı|Alt etki alanı başına bir uygulama|
|Azure AD Connect sağlama Aracısı için sunucu Konağı|Windows 2012 R2 + etki alanı denetleyicileri Active Directory</br>Azure AD Connect hizmetiyle birlikte çalışabilir|

![Şirket içi aracılara akış](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Tek bulut HR App Tenant-> ayrık Active Directory ormanında birden çok alt etki alanını hedefleme

Bu senaryo, bulut HR uygulamasından ayrık Active Directory ormanlarındaki etki alanlarına kullanıcı sağlamayı içerir.

Aşağıdaki üretim yapılandırması önerilir:

|Gereksinim|Öneri|
|:-|:-|
|Şirket içinde dağıtılacak Azure AD Connect sağlama aracılarının sayısı|Ayrık Active Directory orman başına iki|
|Yapılandırılacak sağlama bağlayıcı uygulamalarının sayısı|Alt etki alanı başına bir uygulama|
|Azure AD Connect sağlama Aracısı için sunucu Konağı|Windows 2012 R2 + etki alanı denetleyicileri Active Directory</br>Azure AD Connect hizmetiyle birlikte çalışabilir|

![Tek bulut ık uygulama kiracısı Active Directory ormanı kopuk](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect sağlama Aracısı gereksinimleri

Active Directory Kullanıcı sağlama çözümüne yönelik bulut HR uygulaması, Windows 2012 R2 veya üstünü çalıştıran sunuculara bir veya daha fazla Azure AD Connect sağlama Aracısı dağıtmanızı gerektirir. Sunucular en az 4 GB RAM ve .NET 4.7.1 + çalışma zamanına sahip olmalıdır. Konak sunucusunun hedef Active Directory etki alanına ağ erişimi olduğundan emin olun.

Şirket içi ortamı hazırlamak için Azure AD Connect sağlama Aracısı Yapılandırma Sihirbazı aracıyı Azure AD kiracınızla kaydeder, [bağlantı noktaları açar](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [URL 'lere erişim sağlar](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)ve [giden HTTPS proxy yapılandırmasını](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)destekler.

Sağlama Aracısı, Active Directory etki alanlarıyla iletişim kurmak için bir hizmet hesabı kullanır. Aracıyı yüklemeden önce, aşağıdaki gereksinimleri karşılayan Active Directory Kullanıcıları ve Bilgisayarları ' nda bir hizmet hesabı oluşturun:

- Süre sonu olmayan bir parola
- Kullanıcı hesaplarını okumak, oluşturmak, silmek ve yönetmek için atanan denetim izinleri

Sağlama isteklerini işlemesi gereken etki alanı denetleyicileri seçebilirsiniz. Coğrafi olarak dağıtılmış birkaç etki alanı denetleyiciniz varsa, sağlama aracısını tercih ettiğiniz etki alanı denetleyicileriniz ile aynı siteye yükleyebilirsiniz. Bu konumlandırma, uçtan uca çözümün güvenilirliğini ve performansını geliştirir.

Yüksek kullanılabilirlik için birden fazla Azure AD Connect sağlama Aracısı dağıtabilirsiniz. Aracıyı, aynı şirket içi Active Directory etki alanı kümesini işleyecek şekilde kaydedin.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Kapsam filtrelerini ve öznitelik eşlemesini planlayın

Bulut HR uygulamasından Active Directory veya Azure AD 'ye sağlamayı etkinleştirdiğinizde, Azure portal öznitelik değerlerini öznitelik eşlemesi aracılığıyla denetler.

### <a name="define-scoping-filters"></a>Kapsam filtrelerini tanımlama

Bulut HR uygulamasından Active Directory veya Azure AD 'ye hangi kullanıcıların sağlanması gerektiğini belirleyen öznitelik tabanlı kuralları tanımlamak için [kapsam filtrelerini](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) kullanın.

Birleştiriciler sürecini başlattığınızda, aşağıdaki gereksinimleri toplayın:

- Bulut HR uygulaması, çalışanları ve çalışanların çalışanları üzerinde getirmek için kullanılır mi?
- Hem çalışanları hem de çalışanların çalışanlarını yönetmek için bulut HR uygulamasını Azure AD Kullanıcı sağlaması için kullanmayı planlıyor musunuz?
- Bulut HR uygulamasını Azure AD Kullanıcı sağlaması için yalnızca bulut HR uygulaması kullanıcılarının bir alt kümesi için kullanıma almayı planlıyor musunuz? Örnek yalnızca çalışanlar olabilir.

Gereksinimlerinize bağlı olarak, öznitelik eşlemelerini yapılandırırken, bulut HR uygulamasındaki Kullanıcı kümelerinin Active Directory sağlama kapsamında olması gerektiğini seçmek için **kaynak nesne kapsamı** alanını ayarlayabilirsiniz. Daha fazla bilgi için bkz. bulut HR uygulama öğreticisi, yaygın olarak kullanılan kapsam filtreleri için.

### <a name="determine-matching-attributes"></a>Eşleşen öznitelikleri belirleme

Sağlama sayesinde, kaynak ve hedef sistem arasındaki mevcut hesapları eşleştirme imkanını alırsınız. Bulut HR uygulamasını Azure AD sağlama hizmeti ile tümleştirdiğinizde, bulut HR uygulamasından Active Directory veya Azure AD 'ye hangi kullanıcı verilerinin akaceğini belirlemek için [öznitelik eşlemesini yapılandırabilirsiniz](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) .

Birleştiriciler sürecini başlattığınızda, aşağıdaki gereksinimleri toplayın:

- Bu bulut HR uygulamasındaki her kullanıcıyı tanımlamak için kullanılan benzersiz KIMLIK nedir?
- Kimlik yaşam döngüsü perspektifinden, yeniden oluşturma işlemlerinin nasıl yapılacağını nasıl işleyeceğinizi? Eski çalışan kimliklerini saklayın mi?
- Gelecekteki ilerletlerinizi mi işletin, yoksa Active Directory hesapları mı oluşturdunuz?
- Bir kimlik yaşam döngüsü perspektifinden, çalışanı iş dönüşümünü veya başka bir şekilde nasıl işleyeceğinizi?
- Dönüştürülmüş kullanıcılar eski Active Directory hesaplarını saklar veya yenilerini alırlar mi?

Gereksinimlerinize bağlı olarak, Azure AD, öznitelik eşlemeleri için sabit değerler veya [yazma ifadeleri](../app-provisioning/functions-for-customizing-application-data.md)sağlayarak doğrudan öznitelik eşlemesini destekler. Bu esneklik, hedeflenen uygulama özniteliğinde doldurulduğuna ilişkin nihai denetim sağlar. Kullanıcı hazırlama öznitelik eşlemelerinizi ve şemanızı bir JSON dosyasına aktarmak ve yeniden Azure AD 'ye aktarmak için [MICROSOFT Graph API](../app-provisioning/export-import-provisioning-configuration.md) ve Graf Gezginini kullanabilirsiniz.

Varsayılan olarak, bulut HR uygulamasındaki benzersiz çalışan KIMLIĞINI temsil eden öznitelik, *Active Directory ' deki Unique özniteliğiyle eşlenen* eşleşen öznitelik olarak kullanılır. Örneğin, Workday App senaryosunda, **Workday** **workerıd** özniteliği Active Directory **ÇalışanNo** özniteliğiyle eşleştirilir.

Birden çok eşleşen öznitelik ayarlayabilir ve eşleşen önceliği atayabilirsiniz. Eşleşen önceliğe göre değerlendirilir. Bir eşleşme bulunur başlamaz, başka eşleşen öznitelikler değerlendirilir.

Ayrıca, varolan öznitelik eşlemelerini değiştirme veya silme gibi [varsayılan öznitelik eşlemelerini özelleştirebilirsiniz](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types). Ayrıca, iş gereksinimlerinize göre yeni öznitelik eşlemeleri de oluşturabilirsiniz. Daha fazla bilgi için, eşlenecek özel özniteliklerin bir listesi için Cloud HR App öğreticisi ( [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)gibi) konusuna bakın.

### <a name="determine-user-account-status"></a>Kullanıcı hesabı durumunu belirleme

Varsayılan olarak, sağlama bağlayıcı uygulaması, Kullanıcı hesabının etkinleştirilip etkinleştirilmeyeceğini veya devre dışı bırakılacağını öğrenmek için HR Kullanıcı profili durumunu Active Directory veya Azure AD 'deki Kullanıcı hesabı durumuyla eşler.

Joiners-Leavers sürecini başlattığınızda, aşağıdaki gereksinimleri toplayın.

| İşleme | Gereksinimler |
| - | - |
| **Birleştiriciler** | Kimlik yaşam döngüsü perspektifinden, yeniden oluşturma işlemlerinin nasıl yapılacağını nasıl işleyeceğinizi? Eski çalışan kimliklerini saklayın mi? |
| | Gelecekteki ilerletlerinizi mi işletin, yoksa Active Directory hesapları mı oluşturdunuz? Bu hesaplar etkin veya devre dışı durumda mı oluşturulmuş? |
| | Bir kimlik yaşam döngüsü perspektifinden, çalışanı iş dönüşümünü veya başka bir şekilde nasıl işleyeceğinizi? |
| | Dönüştürülmüş kullanıcılar eski Active Directory hesaplarını saklar veya yenilerini alırlar mi? |
| **Yük devretme** | Sonlandırmalar, Active Directory çalışanları ve çalışanların çalışanları için farklı şekilde işleniyorlar mı? |
| | Kullanıcı sonlandırmasını işlemek için hangi etkin tarihler dikkate alınır? |
| | Çalışan ve çalışanların çalışan dönüştürmeleri mevcut Active Directory hesaplarını nasıl etkiler? |
| | Active Directory de rescind işlemini nasıl işleirsiniz? Daha sonra Active Directory, birleştirici işlemin bir parçası olarak, bu işlemlerin oluşturulması gerekir. |

Gereksinimlerinize bağlı olarak, Active Directory hesabının bir veri noktalarının birleşimine göre etkinleştirilmesi veya devre dışı bırakılması için [Azure AD deyimlerini](../app-provisioning/functions-for-customizing-application-data.md) kullanarak eşleme mantığını özelleştirebilirsiniz.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Cloud ık uygulamasını Active Directory Kullanıcı özniteliklerine eşleyin

Her bir bulut HR uygulaması, eşlemeleri Active Directory için varsayılan bulut ık uygulamasıyla birlikte gelir.

Birleştiriciler-Taşımacılar sürecini başlattığınızda, aşağıdaki gereksinimleri toplayın.

| İşleme | Gereksinimler |
| - | - |
| **Birleştiriciler** | Active Directory hesabı oluşturma işlemi el ile, otomatikleştirilmiş veya kısmen otomatikleştirilebilir mi? |
| | Bulut HR uygulamasından Active Directory 'e özel öznitelikler yaymaya planlansın mı? |
| **Taşımacılar** | Bulut HR uygulamasında bir Movers işlemi gerçekleştiğinde hangi öznitelikleri işlemek istiyorsunuz? |
| | Kullanıcı güncelleştirmeleri sırasında belirli öznitelik doğrulamaları gerçekleştirsin mi? Yanıt Evet ise, ayrıntıları sağlayın. |
| **Yük devretme** | Sonlandırmalar, Active Directory çalışanları ve çalışanların çalışanları için farklı şekilde işleniyorlar mı? |
| | Kullanıcı sonlandırmasını işlemek için hangi etkin tarihler dikkate alınır? |
| | Çalışan ve iş çalışanı dönüştürmelerinde var olan Active Directory hesapları nasıl etkiler? |

Gereksinimlerinize bağlı olarak, eşlemeleri tümleştirme hedeflerinize uyacak şekilde değiştirebilirsiniz. Daha fazla bilgi için, eşlenecek özel özniteliklerin bir listesi için belirli bulut HR uygulama öğreticisine ( [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)gibi) bakın.

### <a name="generate-a-unique-attribute-value"></a>Benzersiz bir öznitelik değeri oluştur

Birleştiriciler sürecini başlattığınızda, CN, samAccountName ve benzersiz kısıtlamalara sahip UPN gibi öznitelikleri ayarladığınızda benzersiz öznitelik değerleri oluşturmanız gerekebilir.

Azure AD işlevi [Selectuniquevalues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) her kuralı değerlendirir ve hedef sistemde benzersizlik için oluşturulan değeri denetler. Bir örnek için bkz. [userPrincipalName (UPN) özniteliği için benzersiz değer oluşturma](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Bu işlev şu anda yalnızca Kullanıcı sağlama Active Directory için iş günü için desteklenir. Diğer sağlama uygulamalarıyla birlikte kullanılamaz.

### <a name="configure-active-directory-ou-container-assignment"></a>Active Directory OU kapsayıcı atamasını yapılandırma

Active Directory Kullanıcı hesaplarını iş birimlerine, konumlarına ve departmanlara göre kapsayıcılara yerleştirmek yaygın bir gereksinimdir. Bir geçiş işlemi başlattığınızda ve bir gözehirde kuruluş değişikliği varsa, kullanıcıyı Active Directory bir OU 'dan diğerine taşımanız gerekebilir.

OU atamasının iş mantığını yapılandırmak ve onu **parentDistinguishedName** Active Directory özniteliğiyle eşlemek için [Switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) işlevini kullanın.

Örneğin, kullanıcı OU 'da ık özniteliği **Municipsellik** temelinde oluşturmak istiyorsanız aşağıdaki ifadeyi kullanabilirsiniz:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Bu ifadeyle, Municipitesi değeri Dallas, Austin, Seattle veya Londra ise, Kullanıcı hesabı ilgili OU 'da oluşturulur. Eşleşme yoksa, hesap varsayılan OU 'da oluşturulur.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Yeni Kullanıcı hesaplarının parola teslimini planlayın

Birleştiriciler sürecini başlattığınızda, Yeni Kullanıcı hesaplarının geçici bir parolasını ayarlayıp teslim etmeniz gerekir. Azure AD Kullanıcı sağlama ile bulut HR ile Kullanıcı için Azure AD [self servis parola sıfırlama](../authentication/tutorial-enable-sspr.md) (SSPR) özelliğini bir gün boyunca alabilirsiniz.

SSPR, BT yöneticilerinin kullanıcıların parolalarını sıfırlamalarını veya hesaplarının kilidini açmalarına olanak tanımak için basit bir araçtır. Bulut HR uygulamasından **Mobil sayı** özniteliğini Active Directory ve Azure AD ile eşitlemek için sağlayabilirsiniz. **Cep telefonu numarası** ÖZNITELIĞI Azure AD 'de olduktan sonra, Kullanıcı hesabı için SSPR 'yi etkinleştirebilirsiniz. Ardından, bir gün sonra Yeni Kullanıcı, kimlik doğrulaması için kayıtlı ve doğrulanan mobil numarayı kullanabilir.

## <a name="plan-for-initial-cycle"></a>İlk döngüyü planlayın

Azure AD sağlama hizmeti ilk kez çalıştırıldığında, bulut HR uygulamasındaki tüm Kullanıcı nesnelerinin anlık görüntüsünü oluşturmak için bulut HR uygulamasına karşı bir [Başlangıç döngüsünü](../app-provisioning/how-provisioning-works.md#initial-cycle) gerçekleştirir. İlk Döngülerde geçen süre, kaynak sistemde kaç kullanıcının var olduğunu doğrudan bağlıdır. 100.000 ' den fazla kullanıcısı olan bazı bulut ık uygulama kiracılarının ilk döngüsünün süresi uzun sürebilir.

**Büyük bulut İK uygulama kiracılar (>30.000 kullanıcıları) için,** aşamalı aşamalar içinde ilk döngüyü çalıştırın. Artımlı güncelleştirmeleri yalnızca, farklı Kullanıcı hazırlama senaryoları için Active Directory doğru özniteliklerin ayarlandığını doğruladıktan sonra başlatın. Buradaki sırayı izleyin.

1. [Kapsam filtresini](#plan-scoping-filters-and-attribute-mapping)ayarlayarak yalnızca sınırlı bir kullanıcı kümesi için başlangıç döngüsünü çalıştırın.
2. İlk çalıştırma için seçilen kullanıcılar için ayarlanan Active Directory hesap sağlamayı ve öznitelik değerlerini doğrulayın. Sonuç beklentilerinizi karşılıyorsa, daha fazla kullanıcı eklemek ve ikinci çalıştırmanın sonuçlarını doğrulamak için kapsam filtresini genişletin.

Test kullanıcıları için başlangıç döngüsünün sonuçlarını tatmin ettikten sonra, [Artımlı güncelleştirmeleri](../app-provisioning/how-provisioning-works.md#incremental-cycles)başlatın.

## <a name="plan-testing-and-security"></a>Test ve güvenlik planlaması

İlk pilot bilgisayardan dağıtımınızın her aşamasında, Kullanıcı sağlamayı etkinleştirerek, sonuçların beklenen şekilde olduğunu ve sağlama döngülerini kontrol etmeyi olduğunuzdan emin olun.

### <a name="plan-testing"></a>Test planı

Bulut HR uygulamasını Azure AD Kullanıcı sağlaması için yapılandırdıktan sonra, bu çözümün kuruluşunuzun gereksinimlerini karşılayıp karşılamadığını doğrulamak için test çalışmalarını çalıştırın.

|Senaryolar|Beklenen sonuçlar|
|:-|:-|
|Yeni çalışan, bulut HR uygulamasında işe alındı.| -Kullanıcı hesabı Active Directory olarak sağlanır.</br>-Kullanıcı Active Directory etki alanı uygulamalarında oturum açabilir ve istenen eylemleri gerçekleştirebilir.</br>-Azure AD Connect eşitleme yapılandırılmışsa, Kullanıcı hesabı Azure AD 'de de oluşturulur.
|Kullanıcı, bulut HR uygulamasında sonlandırılır.|-Kullanıcı hesabı Active Directory devre dışı bırakıldı.</br>-Kullanıcı Active Directory tarafından korunan tüm kurumsal uygulamalarda oturum açamaz.
|Kullanıcı gözetim organizasyonu, bulut HR uygulamasında güncelleştirilir.|Öznitelik eşlemesine göre, Kullanıcı hesabı Active Directory bir OU 'dan diğerine gider.|
|HR, kullanıcının yöneticisini bulut HR uygulamasında güncelleştirir.|Active Directory içindeki yönetici alanı, yeni yöneticinin adını yansıtacak şekilde güncelleştirilir.|
|HR bir çalışanı yeni bir rol olarak yeniden oluşturur.|Davranış, bulut HR uygulamasının çalışan kimliklerini oluşturmak için nasıl yapılandırıldığına bağlıdır:</br>-Eski çalışan KIMLIĞI bir yeniden işe almak için yeniden kullanılırsa bağlayıcı, Kullanıcı için mevcut Active Directory hesabını izin vermez.</br>-Yeniden Kiralama yeni bir çalışan KIMLIĞI alırsa, bağlayıcı kullanıcı için yeni bir Active Directory hesabı oluşturur.|
|HR, çalışanı bir sözleşme çalışanına dönüştürür veya tam tersi de geçerlidir.|Yeni kişi için yeni bir Active Directory hesabı oluşturulur ve eski hesap dönüştürme geçerlilik tarihinde devre dışı bırakılır.|

Otomatik Kullanıcı hazırlama uygulamanızı, belirlenen zaman çizelgelerinizi temel alarak üretime nasıl geçireceğinizi öğrenmek için önceki sonuçları kullanın.

> [!TIP]
> Gizli kişisel verileri, gizlilik ve güvenlik standartlarıyla uyumlu olacak şekilde kaldırmak veya maskelemek için üretim verileriyle test ortamını yenilediğinizde veri azaltma ve veri temizleme gibi teknikler kullanın. 

### <a name="plan-security"></a>Plan güvenliği

Yeni bir hizmet dağıtımının bir parçası olarak bir güvenlik incelemesinin kullanılması yaygındır. Bir güvenlik incelemesi gerekiyorsa veya yürütülmemişse, bir hizmet olarak kimliğe genel bakış sağlayan birçok Azure AD [Teknik](https://www.microsoft.com/download/details.aspx?id=36391) incelemesini inceleyin.

### <a name="plan-rollback"></a>Planı geri alma

Bulut HR Kullanıcı sağlama uygulamasının üretim ortamında istendiği gibi çalışması başarısız olabilir. Bu durumda, aşağıdaki geri alma adımları önceki bilinen iyi duruma geri dönmede size yardımcı olabilir.

1. Etkilenen kullanıcılar veya gruplar üzerinde hatalı işlemlerin ne olduğunu belirlemek için [sağlama Özeti raporunu](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) ve [sağlama günlüklerini](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) gözden geçirin. Sağlama Özeti raporu ve günlükleri hakkında daha fazla bilgi için bkz. [Cloud İK uygulama kullanıcı sağlamayı yönetme](#manage-your-configuration).
2. Etkilenen kullanıcıların veya grupların bilinen son iyi durumu, sağlama denetim günlükleri aracılığıyla veya hedef sistemleri (Azure AD veya Active Directory) inceleyerek belirlenebilir.
3. En son bilinen iyi durum değerlerini kullanarak uygulamada doğrudan etkilenen kullanıcıları veya grupları güncelleştirmek için uygulama sahibiyle birlikte çalışın.

## <a name="deploy-the-cloud-hr-app"></a>Cloud HR uygulamasını dağıtma

Çözüm gereksinimlerinize göre hizalanan bulut HR uygulamasını seçin.

**Workday**: çalışan profillerini Workday 'den Active Directory ve Azure AD 'ye aktarmak için bkz. [öğretici: otomatik Kullanıcı sağlaması Için Workday 'yi yapılandırma](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). İsteğe bağlı olarak, e-posta adresini, Kullanıcı adını ve telefon numarasını Workday 'e geri yazabilirsiniz.

**SAP başarılı etmenleri**: çalışan profillerini başarılı bir şekilde Active Directory ve Azure AD 'ye aktarmak için bkz. [öğretici: otomatik Kullanıcı sağlama Için SAP başarılı faktörlerini yapılandırma](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). İsteğe bağlı olarak, e-posta adresini ve Kullanıcı adını başarılı etkenlere geri yazabilirsiniz.

## <a name="manage-your-configuration"></a>Yapılandırmanızı yönetin

Azure AD, kuruluşunuzun Kullanıcı sağlama kullanımı ve denetim günlükleri ve raporları aracılığıyla işletimsel sistem durumu hakkında ek öngörüler sağlayabilir.

### <a name="gain-insights-from-reports-and-logs"></a>Raporlardan ve günlüklerden öngörü elde edin

Başarılı bir [Başlangıç döngüsünden](../app-provisioning/how-provisioning-works.md#initial-cycle)sonra, Azure AD sağlama hizmeti, aşağıdaki olaylardan biri gerçekleşene kadar her bir uygulamaya özgü öğreticilerde tanımlanan aralıklarda sonsuza kadar sürekli artımlı güncelleştirmeler çalıştırmaya devam eder:

- Hizmet el ile durduruldu. [Azure Portal](https://portal.azure.com/) veya uygun [Microsoft Graph API](/graph/api/resources/synchronization-overview) komutu kullanılarak yeni bir başlangıç çevrimi tetiklenir.
- Yeni bir başlangıç çevrimi, öznitelik eşlemelerinde veya kapsam filtrelerinizdeki bir değişikliğe göre tetiklenir.
- Sağlama işlemi, yüksek hata oranı nedeniyle karantinaya alınır. Dört haftadan uzun bir süre içinde kalır ve bu süre otomatik olarak devre dışıdır.

Bu olayları ve sağlama hizmeti tarafından gerçekleştirilen diğer tüm etkinlikleri gözden geçirmek için, [günlükleri incelemeyi ve sağlama etkinliğine ilişkin raporları almayı öğrenin](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri

Sağlama hizmeti tarafından gerçekleştirilen tüm etkinlikler Azure AD denetim günlüklerine kaydedilir. Azure AD denetim günlüklerini daha fazla analiz için Azure Izleyici günlüklerine yönlendirebilirsiniz. Azure Izleyici günlükleri (Log Analytics çalışma alanı olarak da bilinir) sayesinde, olayları bulmak, eğilimleri çözümlemek ve çeşitli veri kaynakları arasında bağıntı gerçekleştirmek için verileri sorgulayabilirsiniz. Pratik Kullanıcı senaryolarında Azure AD günlükleri için Azure Izleyici günlüklerini kullanmanın avantajlarını öğrenmek için bu [videoyu](https://youtu.be/MP5IaCTwkQg) izleyin.

Ortamınızdaki sağlama olayları etrafında [önceden oluşturulmuş raporlara](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) erişim sağlamak IÇIN [Azure ad etkinlik günlükleri için Log Analytics görünümlerini](../reports-monitoring/howto-install-use-log-analytics-views.md) yükler.

Daha fazla bilgi için bkz. Azure [izleyici günlüklerinizi Azure ad etkinlik günlüklerini çözümleme](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Kişisel verileri yönetme

Windows Server 'da yüklü olan Azure AD Connect sağlama Aracısı, Windows olay günlüğünde, öznitelik eşlemelerini Active Directory için bulut HR uygulamanıza bağlı olarak kişisel veriler içerebilen Günlükler oluşturur. Kullanıcı gizlilik yükümlülükleriyle uyum sağlamak için, olay günlüğünü temizlemek üzere bir Windows zamanlanmış görevi ayarlayın ve hiçbir veri 48 saatten daha fazla tutulmayacak şekilde emin olun.

Azure AD sağlama hizmeti, raporlar oluşturmaz, analiz gerçekleştirmez veya 30 günden daha fazla veri depolamadığından, hiçbir veriyi 30 günden daha fazla saklayamadığından, bu verileri bir veya daha fazla öngörü sağlamadığı için

### <a name="troubleshoot"></a>Sorun giderme

Sağlama sırasında oluşabilecek sorunları gidermek için aşağıdaki makalelere bakın:

- [Azure AD Galeri uygulamasına kullanıcı sağlamayı yapılandırma sorunu](application-provisioning-config-problem.md)
- [Bir uygulamaya sağlamak için şirket içi Active Directory bir özniteliği Azure AD 'ye eşitleyin](user-provisioning-sync-attributes-for-mapping.md)
- [Azure Active Directory Galeri uygulamasına kullanıcı sağlamayı yapılandırırken yönetici kimlik bilgileri kaydedilirken sorun oluştu](./user-provisioning.md)
- [Azure AD Galeri uygulamasına hiçbir Kullanıcı sağlanmıyor](application-provisioning-config-problem-no-users-provisioned.md)
- [Azure AD Galeri uygulamasına yanlış kullanıcı kümesi sağlanıyor](../manage-apps/add-application-portal-assign-users.md)
- [Windows Olay Görüntüleyicisi aracı sorunlarını giderme için ayarlama](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Hizmet sorunlarını giderme için Azure portal denetim günlüklerini ayarlama](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [AD Kullanıcı hesabı oluşturma işlemleri için günlükleri anlama](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Yönetici güncelleştirme işlemlerine yönelik günlükleri anlama](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Yaygın olarak karşılaşılan hatalar çözümleniyor](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Sonraki adımlar

- [Öznitelik eşlemeleri için ifadeler yazma](functions-for-customizing-application-data.md)
- [Azure AD eşitleme API 'sine genel bakış](/graph/api/resources/synchronization-overview)
- [Kapsam dışına çıkan Kullanıcı hesaplarını silmeyi atlayın](skip-out-of-scope-deletions.md)
- [Azure AD Connect sağlama Aracısı: sürüm yayınlama geçmişi](provisioning-agent-release-version-history.md)