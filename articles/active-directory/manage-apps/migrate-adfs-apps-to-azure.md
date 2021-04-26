---
title: AD FS uygulama kimlik doğrulamasını Azure Active Directory olarak taşıma
description: Active Directory Federasyon Hizmetleri (AD FS) (AD FS) değiştirmek için Azure Active Directory kullanarak kullanıcılara tüm uygulamalarına çoklu oturum açma olanağı verme hakkında bilgi edinin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: b0fd3dae2ff9c6de39462d19dc41a32ba51171e0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534863"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Uygulama kimlik doğrulamasını Active Directory Federasyon Hizmetleri’nden Azure Active Directory’ye taşıma

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) , kişilere, iş ortaklarınıza ve müşterilerinize uygulamalara erişmek ve herhangi bir platformdan ve cihazdan işbirliği yapmak için tek bir kimlik sağlayan bir evrensel kimlik platformu sunar. Azure AD ['nin eksiksiz bir kimlik yönetimi özellikleri paketi](../fundamentals/active-directory-whatis.md)vardır. Azure AD 'de uygulama kimlik doğrulaması ve yetkilendirmesinin standartlaştırarak bu avantajlar sağlanır.

> [!TIP]
> Bu makale, geliştirici hedef kitlesi için yazılmıştır. Bir uygulamayı Azure AD 'ye taşımayı planlayan proje yöneticileri ve Yöneticiler, [uygulama kimlik doğrulamasının Azure AD 'ye geçirilmesini](migrate-application-authentication-to-azure-active-directory.md)okumayı göz önünde bulundurmalıdır.

## <a name="azure-ad-benefits"></a>Azure AD avantajları

Kullanıcı hesapları içeren bir şirket içi dizininiz varsa, büyük olasılıkla kullanıcıların kimlik doğrulaması yapmış birçok uygulamanız vardır. Bu uygulamaların her biri kullanıcıların kimliklerini kullanarak erişmesi için yapılandırılır.

Kullanıcılar ayrıca, şirket içi Active Directory doğrudan kimlik doğrulaması yapabilir. Active Directory Federasyon Hizmetleri (AD FS) (AD FS), standartlara dayalı bir şirket içi kimlik hizmetidir. Kullanıcıların her bir uygulama için ayrı olarak oturum açması gerekmeden, güvenilen iş ortakları arasında çoklu oturum açma (SSO) işlevlerini kullanma özelliğini genişletir. Bu, federal kimlik olarak bilinir.

Birçok kuruluşun hizmet olarak yazılım (SaaS) veya özel iş kolu uygulamaları, Microsoft 365 ve Azure AD tabanlı uygulamalar ile birlikte AD FS doğrudan.

  ![Doğrudan şirket içine bağlı uygulamalar](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Uygulama güvenliğini artırmak için amacınız, şirket içi ve bulut ortamlarınızda tek bir erişim denetimi ve ilke kümesine sahip olmaktır.

  ![Azure AD ile bağlantılı uygulamalar](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Geçirilecek uygulama türleri

Kimlik ve erişim yönetimi için tek bir denetim düzlemi sunabildiğinden, tüm uygulama kimlik doğrulamalarınızın Azure AD 'ye geçirilmesi idealdir.

Uygulamalarınız, kimlik doğrulaması için modern veya eski protokolleri kullanabilir. Azure AD 'ye geçişinizi planlarken, önce modern kimlik doğrulama protokolleri (SAML ve açık KIMLIK Connect gibi) kullanan uygulamaları geçirmeyi göz önünde bulundurun. Bu uygulamalar Azure AD ile Azure Uygulama galerisinden bir yerleşik bağlayıcı aracılığıyla veya uygulamayı Azure AD 'ye kaydederek kimlik doğrulamak üzere yeniden yapılandırılabilir. Eski protokolleri kullanan uygulamalar, uygulama proxy 'Si kullanılarak tümleştirilebilir.

Daha fazla bilgi için bkz.

* [Uzak kullanıcılar için şirket içi uygulamalar yayımlamak üzere Azure AD uygulama ara sunucusu kullanma](what-is-application-proxy.md).
* [Uygulama yönetimi nedir?](what-is-application-management.md)
* [Uygulamaları Azure AD 'ye geçirmek için uygulama etkinliği raporunu AD FS](migrate-adfs-application-activity.md).
* [Azure AD Connect Health kullanarak AD FS izleyin](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Geçiş işlemi

Uygulama kimlik doğrulamasını Azure AD 'ye taşıma işlemi sırasında uygulamalarınızı ve yapılandırmanızı test edin. Üretim ortamına geçtiğinizde geçiş testi için mevcut test ortamlarını kullanmaya devam etmenizi öneririz. Bir test ortamı şu anda kullanılabilir değilse, uygulama mimarisine bağlı olarak [Azure App Service](https://azure.microsoft.com/services/app-service/) veya [Azure sanal makinelerini](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)kullanarak bir tane ayarlayabilirsiniz.

Uygulama yapılandırmalarının geliştirilmesi için ayrı bir test Azure AD kiracısı ayarlamayı tercih edebilirsiniz.

Geçiş işleminiz şöyle görünebilir:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>1. aşama – geçerli durum: üretim uygulaması AD FS kimliğini doğrular

  ![Geçiş aşaması 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>2. aşama – (Isteğe bağlı) Azure AD kiracısına uygulamanın bir test örneğini Işaret edin

Uygulamanın test örneğinizi bir Azure AD kiracısına işaret etmek için yapılandırmayı güncelleştirin ve gerekli değişiklikleri yapın. Uygulama, test Azure AD kiracısındaki kullanıcılarla test edilebilir. Geliştirme süreci sırasında, istekleri ve yanıtları karşılaştırmak ve doğrulamak için [Fiddler](https://www.telerik.com/fiddler) gibi araçları kullanabilirsiniz.

Ayrı bir test kiracısı ayarlamak uygun değilse, bu aşamayı atlayın ve uygulamanın bir test örneğini aşağıdaki 2. aşama bölümünde açıklandığı gibi üretim Azure AD kiracınıza getirin.

  ![Geçiş aşaması 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>3. aşama – uygulamanın bir test örneğini üretim Azure AD kiracısına Işaret etme

Uygulamanın test örneğinizi üretim Azure AD kiracınıza işaret etmek için yapılandırmayı güncelleştirin. Artık üretim kiracınızdaki kullanıcılarla test edebilirsiniz. Gerekirse, kullanıcıların geçişini yaparken bu makalenin bölümünü gözden geçirin.

  ![Geçiş aşaması 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>4. aşama – üretim uygulamasını üretim Azure AD kiracısına Işaret edin

Üretim uygulamanızın yapılandırmasını, üretim Azure AD kiracınızı işaret etmek üzere güncelleştirin.

  ![Geçiş aşaması 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 AD FS ile kimlik doğrulayan uygulamalar, izinler için Active Directory grupları kullanabilir. Geçişe başlamadan önce şirket içi ortamınız ile Azure AD arasında kimlik verilerini eşitlemek için [Azure AD Connect eşitleme](../hybrid/how-to-connect-sync-whatis.md) kullanın. Uygulama geçirildiğinde aynı kullanıcılara erişim sağlayabilmeniz için geçişten önce bu grupları ve üyeliği doğrulayın.

### <a name="line-of-business-apps"></a>İş kolu uygulamaları

İş kolu uygulamalarınız, kuruluşunuzun geliştirildiği veya standart bir paketlenmiş ürün olan olanlardır. Örnek olarak, Windows Identity Foundation ve SharePoint uygulamaları (SharePoint Online değil) üzerinde oluşturulmuş uygulamalar sayılabilir.

OAuth 2,0, OpenID Connect veya WS-Federation kullanan iş kolu uygulamaları Azure AD ile [uygulama kayıtları](../develop/quickstart-register-app.md)olarak tümleştirilebilir. SAML 2,0 veya WS-Federation kullanan özel uygulamaları [Azure Portal](https://portal.azure.com/)kurumsal uygulamalar sayfasında [Galeri dışı uygulamalar](add-application-portal.md) olarak tümleştirin.

## <a name="saml-based-single-sign-on"></a>SAML tabanlı çoklu oturum açma

Kimlik doğrulaması için SAML 2,0 kullanan uygulamalar, [SAML tabanlı çoklu oturum açma](what-is-single-sign-on.md) (SSO) için yapılandırılabilir. SAML tabanlı SSO ile, SAML talepleriniz içinde tanımladığınız kurallara göre kullanıcıları belirli uygulama rolleriyle eşleyebilirsiniz.

SAML tabanlı SSO için bir SaaS uygulaması yapılandırmak üzere bkz. [hızlı başlangıç: SAML tabanlı çoklu oturum açmayı ayarlama](add-application-portal-setup-sso.md).

  ![SSO SAML Kullanıcı ekran görüntüleri ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Birçok SaaS uygulamasında, SAML tabanlı SSO için yapılandırma adımlarında size kılavuzluk eden [uygulamaya özgü bir öğretici](../saas-apps/tutorial-list.md) vardır.

  ![Uygulama öğreticisi](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Bazı uygulamalar kolayca geçirilebilir. Özel talepler gibi daha karmaşık gereksinimleri olan uygulamalar Azure AD ve/veya Azure AD Connect ek yapılandırma gerektirebilir. Desteklenen talep eşlemeleri hakkında bilgi için bkz. [nasıl yapılır: bir Kiracıdaki belirli bir uygulama için belirteçlerde yayılan talepleri özelleştirme (Önizleme)](../develop/active-directory-claims-mapping.md).

Öznitelikleri eşlerken aşağıdaki sınırlamaları aklınızda bulundurun:

* AD FS ' de yayımlanmayan özniteliklerin hepsi, Azure AD 'de, bu öznitelikler eşitlense bile SAML belirteçlerine eklenecek öznitelikler olarak gösterilmez. Özniteliği düzenlediğinizde, **değer** açılan listesi, Azure AD 'de kullanılabilen farklı öznitelikleri gösterir. Gerekli bir özniteliğin (örneğin, **sAMAccountName**) Azure AD ile eşitlendiğinden emin olmak için [Azure AD Connect eşitleme konuları](../hybrid/how-to-connect-sync-whatis.md) yapılandırmasını denetleyin. Azure AD 'de standart Kullanıcı şemasının bir parçası olmayan herhangi bir talebi göstermek için uzantı özniteliklerini kullanabilirsiniz.
* En yaygın senaryolarda, uygulama için yalnızca **NameID** talebi ve diğer yaygın kullanıcı tanımlayıcısı talepleri gerekir. Herhangi bir ek talep gerekip gerekmediğini belirlemek için AD FS hangi talepleri yayınlıyoruz ' i inceleyin.
* Bazı talepler Azure AD 'de korunduğu için tüm talepler verilemez.
* Şifrelenmiş SAML belirteçleri kullanma özelliği artık önizlemededir. Bkz. [nasıl yapılır: kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md).

### <a name="software-as-a-service-saas-apps"></a>Hizmet olarak yazılım (SaaS) uygulamaları

Kullanıcılarınız Salesforce, ServiceNow veya Workday gibi SaaS uygulamalarında oturum açtığında ve AD FS ile tümleşikse, SaaS uygulamaları için Federe oturum açma kullanıyorsunuz demektir.

Birçok SaaS uygulaması, Azure AD 'de yapılandırılabilir. Microsoft,  [Azure AD uygulama galerisinde](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)SaaS uygulamalarına önceden yapılandırılmış birçok bağlantıya sahiptir ve bu da geçişinizi kolaylaştırır. SAML 2,0 uygulamaları Azure AD Uygulama Galerisi aracılığıyla veya [Galeri dışı uygulamalar](add-application-portal.md)olarak Azure AD ile tümleştirilebilir.

OAuth 2,0 veya OpenID Connect kullanan uygulamalar, Azure AD ile aynı şekilde [uygulama kayıtları](../develop/quickstart-register-app.md)olarak tümleştirilebilir. Eski protokolleri kullanan uygulamalar Azure AD kimlik doğrulaması için [azure ad uygulama ara sunucusu](application-proxy.md) kullanabilir.

SaaS uygulamalarınızı ekleme ile ilgili herhangi bir sorun için, [SaaS uygulaması tümleştirme destek diğer adına](mailto:SaaSApplicationIntegrations@service.microsoft.com)başvurabilirsiniz.

### <a name="saml-signing-certificates-for-sso"></a>SSO için SAML imzalama sertifikaları

İmzalama sertifikaları, herhangi bir SSO dağıtımının önemli bir parçasıdır. Azure AD, SaaS uygulamalarınıza SAML tabanlı Federasyon SSO 'SU kurmak için imzalama sertifikaları oluşturur. Galeri veya Galeri dışı uygulamalar eklediğinizde, eklenen uygulamayı Federasyon SSO seçeneğini kullanarak yapılandırırsınız. [Azure Active Directory için bkz. Federasyon çoklu oturum açma için sertifikaları yönetme](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>SAML belirteci şifrelemesi

Hem AD FS hem de Azure AD belirteç şifrelemeyi sağlar — uygulamalara gidecek SAML güvenlik onaylamalarını şifreleme özelliği. Onaylar ortak anahtarla şifrelenir ve eşleşen özel anahtarla alıcı uygulama tarafından şifresi çözülür. Belirteç şifrelemesini yapılandırırken, X. 509.952 sertifika dosyalarını ortak anahtarları sağlamak üzere karşıya yüklersiniz.

Azure AD SAML belirteci şifrelemesi ve nasıl yapılandırılacağı hakkında bilgi için bkz. [nasıl yapılır: Azure AD SAML belirteci şifrelemesini yapılandırma](howto-saml-token-encryption.md).  

> [!NOTE]
> Belirteç şifreleme bir Azure Active Directory (Azure AD) Premium özelliğidir. Azure AD sürümleri, Özellikler ve fiyatlandırma hakkında daha fazla bilgi edinmek için bkz. [Azure AD fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Bugün taşınabilecek uygulamalar ve Konfigürasyonlar

Günümüzde kolayca taşıyabileceğiniz uygulamalar, standart yapılandırma öğeleri ve talepler kümesini kullanan SAML 2,0 uygulamalarını içerir. Bu standart öğeler şunlardır:

* Kullanıcı Asıl Adı
* E-posta adresi
* Ad
* Soyadı
* Azure AD posta özniteliği, posta öneki, çalışan kimliği, 1-15 uzantı öznitelikleri veya şirket içi **SamAccountName** özniteliği dahil olmak üzere SAML **NameID** gibi alternatif öznitelik. Daha fazla bilgi için bkz. [NameIdentifier talebini düzenleme](../develop/active-directory-saml-claims-customization.md).
* Özel talepler.

Aşağıdakiler, Azure AD 'ye geçiş için ek yapılandırma adımları gerektirir:

* AD FS içindeki özel yetkilendirme veya Multi-Factor Authentication (MFA) kuralları. Bunları [Azure AD koşullu erişim](../conditional-access/overview.md) özelliğini kullanarak yapılandırırsınız.
* Birden çok yanıt URL uç noktası olan uygulamalar. Bunları Azure AD 'de PowerShell veya Azure portal arabirimini kullanarak yapılandırırsınız.
* SAML sürüm 1.1 belirteçlerini gerektiren SharePoint uygulamaları gibi WS-Federasyon uygulamaları. PowerShell kullanarak el ile yapılandırabilirsiniz. Galeriden SharePoint ve SAML 1,1 uygulamaları için önceden tümleştirilmiş bir genel şablon da ekleyebilirsiniz. SAML 2,0 protokolünü destekliyoruz.
* Karmaşık talepler verme, kuralları dönüştürür. Desteklenen talep eşlemeleri hakkında bilgi için bkz.
   *  [Azure Active Directory 'de talep eşlemesi](../develop/active-directory-claims-mapping.md).
   * [Azure Active Directory 'de kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Bugün Azure AD'de desteklenmeyen uygulamalar ve yapılandırmalar

Belirli özellikleri gerektiren uygulamalar bugün geçirilemez.

#### <a name="protocol-capabilities"></a>Protokol özellikleri

Aşağıdaki protokol özelliklerini gerektiren uygulamalar bugün geçirilemez:

* WS-Trust ActAs düzenine yönelik destek
* SAML yapı çözümlemesi
* İmzalı SAML isteklerinin imza doğrulaması
  > [!Note]
  > İmzalı istekler kabul edilir, ancak imza doğrulanmaz.

  Azure AD tarafından yalnızca uygulamada önceden yapılandırılmış uç noktalara belirteç döndürüldüğünden, çoğu durumda imza doğrulaması gerekmez.

#### <a name="claims-in-token-capabilities"></a>Belirteç özellikleri içindeki talepler

Belirteç özellikleri 'nde aşağıdaki talepler gerektiren uygulamalar bugün geçirilemez.

* Azure ad dizini dışındaki öznitelik depolarından gelen talepler, verilerin Azure AD ile eşitlenmediği durumlar değildir. Daha fazla bilgi için bkz. [Azure AD eşitleme API 'sine genel bakış](/graph/api/resources/synchronization-overview).
* Dizin çoklu değer özniteliklerinin verilmesi. Örneğin, şu anda proxy adresleri için çok değerli bir talep yayınlıyoruz.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>AD FS uygulama ayarlarını Azure AD 'ye eşleyin

Geçiş, uygulamanın şirket içinde nasıl yapılandırıldığını değerlendirmek ve sonra bu yapılandırmayı Azure AD ile eşlemeyi gerektirir. AD FS ile Azure AD'nin çalışmaları birbirine benzer; dolayısıyla güven yapılandırma, oturum açma ve oturum kapatma URL'leri ve tanımlayıcı gibi kavramlar her ikisi için de geçerlidir. Azure AD 'de kolayca yapılandırabilmeniz için uygulamalarınızın AD FS yapılandırma ayarlarını belgeleyin.

### <a name="map-app-configuration-settings"></a>Uygulama yapılandırma ayarlarını eşleme

Aşağıdaki tabloda, Azure AD kurumsal uygulamasına AD FS bağlı olan taraf güveni arasındaki ayarların en yaygın eşleştirmesinin bazıları açıklanmaktadır:

* AD FS — uygulama için AD FS bağlı olan taraf güveninde ayarı bulun. Bağlı olan tarafa sağ tıklayın ve Özellikler ' i seçin.
* Azure AD — bu ayar, her uygulamanın SSO özelliklerindeki [Azure Portal](https://portal.azure.com/) içinde yapılandırılır.

| Yapılandırma ayarı| AD FS| Azure AD 'de yapılandırma| SAML belirteci |
| - | - | - | - |
| **Uygulama oturum açma URL'si** <p>Bir hizmet sağlayıcısı (SP) tarafından başlatılan bir SAML akışında uygulamada oturum açmak için kullanıcının URL 'SI.| Yok| SAML tabanlı oturum açma işleminden temel SAML yapılandırması 'nı açın| Yok |
| **Uygulama yanıtı URL 'SI** <p>Kimlik sağlayıcısı (IDP) perspektifinden uygulamanın URL 'SI. IDP, Kullanıcı IDP 'de oturum açtıktan sonra kullanıcıyı ve belirteci buraya gönderir.  Bu, **SAML onaylama tüketici uç noktası** olarak da bilinir.| **Uç noktalar** sekmesini seçin| SAML tabanlı oturum açma işleminden temel SAML yapılandırması 'nı açın| SAML belirtecindeki hedef öğe. Örnek değer: `https://contoso.my.salesforce.com` |
| **Uygulama oturumu kapatma URL'si** <p>Bu, bir Kullanıcı bir uygulamadan oturumu kapattığında, oturum kapatma temizleme isteklerinin gönderildiği URL 'dir. IDP, kullanıcıyı diğer tüm uygulamalardan da oturumu kapatmak için isteği gönderir.| **Uç noktalar** sekmesini seçin| SAML tabanlı oturum açma işleminden temel SAML yapılandırması 'nı açın| Yok |
| **Uygulama tanımlayıcısı** <p>Bu, IDP 'nin perspektifinden uygulama tanımlayıcısıdır. Tanımlayıcı olarak çoğunlukla oturum açma URL değeri kullanılır (ama her zaman kullanılmaz).  Bazen uygulama bunu "varlık KIMLIĞI" olarak çağırır.| **Tanımlayıcılar** sekmesini seçin|SAML tabanlı oturum açma işleminden temel SAML yapılandırması 'nı açın| SAML belirtecindeki **hedef kitle** öğesiyle eşlenir. |
| **Uygulama Federasyon meta verileri** <p>Bu, uygulamanın Federasyon meta verilerinin konumudur. IdP bunu, uç noktalar veya şifreleme sertifikaları gibi belirli yapılandırma ayarlarını otomatik olarak güncelleştirmek için kullanır.| **İzleme** sekmesini seçin| Yok. Azure AD uygulama Federasyon meta verilerini doğrudan kullanmayı desteklemez. Federasyon meta verilerini el ile içeri aktarabilirsiniz.| Yok |
| **Kullanıcı tanımlayıcısı/ad KIMLIĞI** <p>Azure AD'den veya AD FS'den kullanıcı tanımlayıcınızı uygulamanıza benzersiz olarak göstermek için kullanılan öznitelik.  Bu öznitelik genellikle kullanıcının UPN 'si veya e-posta adresidir.| Talep kuralları. Çoğu durumda, talep kuralı **NameIdentifier** ile biten bir türle bir talep yayınlar.| Tanımlayıcıyı, **Kullanıcı öznitelikleri ve talepler** başlığı altında bulabilirsiniz. Varsayılan olarak UPN kullanılır| SAML belirtecindeki **NameID** öğesiyle eşlenir. |
| **Diğer talepler** <p>IDP 'den uygulamaya genellikle gönderilen diğer talep bilgilerine örnek olarak ad, soyadı, e-posta adresi ve grup üyeliği dahildir.| AD FS'de, bunu bağlı olan tarafta diğer talep kuralları olarak bulabilirsiniz.| Tanımlayıcıyı, **Kullanıcı öznitelikleri & talepler** altında bulabilirsiniz. **Görünüm**'ü seçin ve diğer tüm kullanıcı özniteliklerini düzenleyin.| Yok |

### <a name="map-identity-provider-idp-settings"></a>Eşleme kimlik sağlayıcısı (IDP) ayarları

Uygulamalarınızı, SSO için AD FS karşı Azure AD 'ye işaret etmek üzere yapılandırın. Burada SAML protokolünü kullanan SaaS uygulamalarına odaklanıyoruz. Ancak, bu kavram özel iş kolu uygulamalarına da genişletilir.

> [!NOTE]
> Azure AD 'nin yapılandırma değerleri, Azure kiracı KIMLIĞINIZIN {Tenant-ID} ' ın yerini aldığı ve uygulama KIMLIĞI {Application-id} ' nin yerini aldığı düzene uyar. Bu bilgileri [Azure portal](https://portal.azure.com/) **Azure Active Directory > Özellikler** altında bulabilirsiniz:

* Kiracı KIMLIĞINIZI görmek için dizin KIMLIĞI ' ni seçin.
* Uygulama kimliğinizi görmek için uygulama KIMLIĞI ' ni seçin.

 Yüksek düzeyde, aşağıdaki anahtar SaaS uygulamaları yapılandırma öğelerini Azure AD 'ye eşleyin.

| Öğe| Yapılandırma Değeri |
| - | - |
| Kimlik sağlayıcısı veren| https: \/ /STS.Windows.net/{Tenant-id}/ |
| Kimlik sağlayıcısı oturum açma URL 'SI| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Kimlik sağlayıcısı oturum kapatma URL 'SI| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Federasyon meta veri konumu| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>SaaS uygulamaları için SSO ayarlarını eşleme

SaaS uygulamalarının kimlik doğrulama isteklerinin nereden gönderileceğini ve alınan belirteçleri nasıl doğrulayacağını bilmeleri gerekir. Aşağıdaki tabloda, uygulamada SSO ayarlarını yapılandırma ve AD FS ve Azure AD içindeki değerleri ya da konumları açıklanmaktadır.

| Yapılandırma ayarı| AD FS| Azure AD 'de yapılandırma |
| - | - | - |
| **IDP oturum açma URL 'SI** <p>Uygulamanın perspektifinden IDP 'nin oturum açma URL 'SI (kullanıcının oturum açmak için yeniden yönlendirildiği konum).| AD FS oturum açma URL 'SI, AD FS Federasyon Hizmeti adı ve ardından "/adfs/ls/." <p>Örnek: `https://fs.contoso.com/adfs/ls/`| {Kiracı-kimliği} öğesini kiracı KIMLIĞINIZLE değiştirin. <p> SAML-P protokolünü kullanan uygulamalar için: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>WS-Federation protokolünü kullanan uygulamalar için: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IDP oturum kapatma URL 'SI**<p>Uygulamanın perspektifinden IDP 'nin oturum kapatma URL 'SI (uygulamanın oturumu kapatmak için seçerken kullanıcının yeniden yönlendirildiği).| Oturum kapatma URL 'SI, oturum açma URL 'si ile aynı URL ya da "WA = wsignout 1.0" eklenmiş URL. Örnek: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| {Kiracı-kimliği} öğesini kiracı KIMLIĞINIZLE değiştirin.<p>SAML-P protokolünü kullanan uygulamalar için:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> WS-Federation protokolünü kullanan uygulamalar için: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Belirteç imzalama sertifikası**<p>IDP, verilen belirteçleri imzalamak için sertifikanın özel anahtarını kullanır. Belirtecin, uygulamanın güvenmek üzere yapılandırıldığı IdP'den geldiğini doğrular.| AD FS belirteç imzalama sertifikası AD FS Yönetimi'nde **Sertifikalar**'ın altında bulabilirsiniz.| Uygulamanın **Çoklu oturum açma özelliklerinde** , **SAML imzalama sertifikası** başlığı altında Azure Portal bulun. Sertifikayı buradan indirip uygulamaya yükleyebilirsiniz.  <p>Uygulamanın birden fazla sertifikası varsa, Federasyon meta verileri XML dosyasındaki tüm sertifikaları bulabilirsiniz. |
| **Tanımlayıcı/"veren"**<p>Uygulamanın perspektifinden IDP 'nin tanımlayıcısı (bazen "verenin KIMLIĞI" olarak da adlandırılır).<p>SAML belirtecinde, değer veren öğesi olarak görünür.| AD FS için tanımlayıcı, genellikle **> Federasyon Hizmeti özelliklerini Düzenle**' nin altındaki AD FS yönetiminde Federasyon Hizmeti tanımlayıcısıdır. Örnek: `http://fs.contoso.com/adfs/services/trust`| {Kiracı-kimliği} öğesini kiracı KIMLIĞINIZLE değiştirin.<p>https: \/ /STS.Windows.net/{Tenant-id}/ |
| **IDP Federasyon meta verileri**<p>IDP 'nin genel kullanıma açık Federasyon meta verilerinin konumu. (Bazı uygulamalar federasyon meta verilerini yönetici yapılandırma URL'lerine, tanımlayıcıya ve bağımsız olarak belirteç imzalama sertifikasına alternatif olarak kullanılır)| **Hizmet > uç noktaları > meta veri > türü: Federasyon meta verileri** altında AD FS yönetiminde Federasyon meta verileri URL 'sini AD FS bulun. Örnek: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD için buna karşılık gelen değer, düzen izler [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . {TenantDomainName} öğesini kiracınızın adı "contoso.onmicrosoft.com" biçiminde değiştirin.   <p>Daha fazla bilgi için bkz. [Federasyon meta verileri](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Azure AD 'de AD FS güvenlik ilkelerini temsil etme

Uygulama kimlik doğrulamasını Azure AD 'ye taşırken, mevcut güvenlik ilkelerinden Azure AD 'de bulunan eşdeğer veya alternatif çeşitlere eşlemeler oluşturun. Uygulama sahipleriniz için gereken güvenlik standartları toplantılarından dolayı bu eşlemelerin yapılabilmesini sağlamak, uygulama geçişinin geri kalanının önemli ölçüde daha kolay olmasını sağlar.

Her kural örneği için, kuralın AD FS, AD FS kural dili denk kodu ve bunun Azure AD ile nasıl eşlendiğini gösteririz.

### <a name="map-authorization-rules"></a>Eşleme yetkilendirme kuralları

Aşağıda AD FS çeşitli yetkilendirme kuralı türlerinin örnekleri verilmiştir ve bunları Azure AD ile nasıl eşlersiniz.

#### <a name="example-1-permit-access-to-all-users"></a>Örnek 1: tüm kullanıcılara erişime Izin ver

AD FS tüm kullanıcılara erişime izin ver:

  ![Ekran görüntüsü, SAML ile tek Sign-On ayarla iletişim kutusunu gösterir.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Bu, Azure AD ile aşağıdaki yollarla eşlenir:

1. **Kullanıcı atamasını** **Hayır** olarak ayarlayın.

    ![SaaS uygulamaları için erişim denetimi ilkesini düzenleme ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > **Evet** Için **gereken Kullanıcı atamasını** ayarlamak, kullanıcıların erişim kazanmak için uygulamaya atanmasını gerektirir. **Hayır** olarak ayarlandığında, tüm kullanıcıların erişimi vardır. Bu anahtar, kullanıcıların **uygulamalarım** deneyiminde neleri görmediğini denetlemez.

1. **Kullanıcılar ve Gruplar sekmesinde**, uygulamanızı **tüm kullanıcılar** otomatik grubuna atayın. Varsayılan **tüm kullanıcılar** grubunun kullanılabilmesi IÇIN Azure AD kiracınızda [dinamik grupları etkinleştirmeniz](../enterprise-users/groups-create-rule.md) gerekir.

    ![Azure AD 'de SaaS uygulamalarım ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Örnek 2: bir gruba açıkça Izin verme

AD FS açık grup yetkilendirmesi:

  ![Ekran görüntüsü, Domain Admins talep kuralına Izin ver için kural Düzenle iletişim kutusunu gösterir.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Bu kuralı Azure AD 'ye eşlemek için:

1. [Azure Portal](https://portal.azure.com/), AD FS kullanıcı grubuna karşılık gelen [bir Kullanıcı grubu oluşturun](../fundamentals/active-directory-groups-create-azure-portal.md) .
1. Gruba uygulama izinleri atama:

    ![Atama Ekle ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Örnek 3: belirli bir kullanıcıyı yetkilendirme

AD FS açık kullanıcı yetkilendirmesi:

  ![Ekran görüntüsü, belirli bir Kullanıcı talebine Izin ver kuralı Düzenle iletişim kutusunu gösterir.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Bu kuralı Azure AD 'ye eşlemek için:

* [Azure Portal](https://portal.azure.com/), uygulamanın atama Ekle sekmesinden aşağıda gösterildiği gibi bir kullanıcı ekleyin:

    ![Azure 'da SaaS uygulamalarım ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Multi-Factor Authentication kurallarını eşleme

AD FS ile federe olduğunuz için [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) ve AD FS şirket içi dağıtımı, geçişten sonra hala işe yarar. Ancak Azure 'un Azure AD 'nin koşullu erişim iş akışlarına bağlı olan yerleşik MFA özelliklerine geçiş yapmayı düşünün.

Aşağıda AD FS MFA kuralları türlerinin örnekleri verilmiştir ve bunları farklı koşullara göre Azure AD ile nasıl eşleyebileceğiniz açıklanır.

AD FS MFA kuralı ayarları:

  ![Ekran görüntüsü Azure portal Azure A koşullarını gösterir.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Örnek 1: kullanıcılara/gruplara göre MFA 'yı zorlama

Kullanıcılar/Gruplar Seçicisi grup başına (Grup SID) veya Kullanıcı başına (birincil SID) temelinde MFA 'yı zorlamanıza olanak tanıyan bir kuraldır. Kullanıcılar/Gruplar atamalarından ayrı olarak, AD FS MFA yapılandırma kullanıcı arabirimi işlevindeki tüm ek onay kutuları, kullanıcılar/gruplar kuralı zorlandıktan sonra değerlendirilen ek kurallar olarak değerlendirilir.

Azure AD 'de bir kullanıcı veya grup için MFA kurallarını belirtin:

1. Yeni bir [koşullu erişim ilkesi](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)oluşturun.
1. **Atamalar**' ı seçin. MFA 'yı zorlamak istediğiniz kullanıcıları veya grupları ekleyin.
1. **Erişim denetimleri** seçeneklerini aşağıda gösterildiği gibi yapılandırın:

    ‎![Ekran görüntüsü, erişim sağlayabileceğiniz Izin bölmesini gösterir.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Örnek 2: kayıtlı olmayan cihazlar için MFA zorlama

Azure AD 'de kayıtlı olmayan cihazlar için MFA kurallarını belirtin:

1. Yeni bir [koşullu erişim ilkesi](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)oluşturun.
1. **Atamaları** **tüm kullanıcılara** ayarlayın.
1. **Erişim denetimleri** seçeneklerini aşağıda gösterildiği gibi yapılandırın:

    ![Ekran görüntüsü, erişim sağlayabileceğiniz ve diğer kısıtlamaları belirtebileceğiniz Izin bölmesini gösterir.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

**Seçilen denetimlerden birini gerektirmek** için **birden çok denetim için** seçeneğini belirlediğinizde, onay kutusu tarafından belirtilen koşullardan herhangi biri Kullanıcı tarafından karşılanıyorsa, kullanıcıya uygulamanıza erişim izni verilir demektir.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Örnek 3: MFA 'yı konuma göre zorla

Azure AD 'de bir kullanıcının konumuna göre MFA kurallarını belirtin:

1. Yeni bir [koşullu erişim ilkesi](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)oluşturun.
1. **Atamaları** **tüm kullanıcılara** ayarlayın.
1. [Azure AD 'de adlandırılmış konumları yapılandırın](../reports-monitoring/quickstart-configure-named-locations.md). Aksi takdirde, kurumsal ağınızın içinden Federasyon güvenilirdir.
1. MFA 'yı uygulamak istediğiniz konumları belirtmek için **koşullar kurallarını** yapılandırın.

    ![Ekran görüntüsü, koşullar kuralları için konumlar bölmesini gösterir.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. **Erişim denetimleri** seçeneklerini aşağıda gösterildiği gibi yapılandırın:

    ![Harita erişim denetim ilkeleri](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Yayma özniteliklerini talep kuralı olarak eşle

Öznitelikleri AD FS bir talep kuralı olarak göster:

  ![Ekran görüntüsü öznitelikleri talep olarak yayma için kural Düzenle iletişim kutusunu gösterir.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

Kuralı Azure AD 'ye eşlemek için:

1. [Azure Portal](https://portal.azure.com/), **Kurumsal uygulamalar** ' ı seçin ve ardından **Çoklu oturum açarak** SAML tabanlı oturum açma yapılandırmasını görüntüleyin:

    ![Ekran görüntüsü, kurumsal uygulamanız için çoklu oturum açma sayfasını gösterir.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Öznitelikleri değiştirmek için **Düzenle** (vurgulanmış) seçeneğini belirleyin:

    ![Bu, Kullanıcı özniteliklerini ve taleplerini düzenleme sayfasıdır](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Yerleşik erişim denetimi ilkelerini eşleme

AD FS 2016 ' de yerleşik erişim denetim ilkeleri:

  ![Azure AD yerleşik Access Control](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Azure AD 'de yerleşik ilkeleri uygulamak için, [Yeni bir koşullu erişim ilkesi](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) kullanın ve erişim denetimlerini yapılandırın veya AD FS 2016 ' de özel ilke tasarımcısını kullanarak erişim denetim ilkelerini yapılandırın. Kural Düzenleyicisi, her türlü permütasyon oluşturmanıza yardımcı olabilecek, Izin verme ve dışındaki seçeneklerin kapsamlı bir listesini içerir.

  ![Azure AD erişim denetimi ilkeleri](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

Bu tabloda bazı yararlı Izin ve seçenekler dışında, Azure AD ile nasıl eşleştireceğiz listelenmiştir.

| Seçenek | Azure AD 'de Izin verme seçeneği nasıl yapılandırılır?| Azure AD 'de except seçeneği nasıl yapılandırılır? |
| - | - | - |
| Belirli bir ağdan| Azure AD 'de [adlandırılmış konuma](../reports-monitoring/quickstart-configure-named-locations.md) eşlenir| [Güvenilen konumlar](../conditional-access/location-condition.md) için **Dışla** seçeneğini kullanın |
| Belirli gruplardan| [Kullanıcı/Grup ataması ayarlama](assign-user-or-group-access-portal.md)| Kullanıcılar ve gruplar 'daki **hariç tut** seçeneğini kullanın |
| Belirli güven düzeyine sahip cihazlardan| Bu ayarı, atamalar-> koşulları altındaki **cihaz durumu** denetiminden ayarlayın| Cihaz durumu koşulunun altındaki **Dışla** seçeneğini kullanın ve **tüm cihazları** ekleyin |
| Istekteki belirli taleplerle| Bu ayar geçirilemez| Bu ayar geçirilemez |

Azure portal güvenilir konumlar için exclude seçeneğinin nasıl yapılandırılacağı hakkında bir örnek aşağıda verilmiştir:

  ![Erişim denetimi ilkelerinin eşleme ekran görüntüsü](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Kullanıcıların AD FS 'den Azure AD 'ye geçiş

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD 'de AD FS gruplarını eşitleme

Yetkilendirme kurallarını eşlediğinizde AD FS ile kimlik doğrulayan uygulamalar izinler için Active Directory grupları kullanabilir. Böyle bir durumda, uygulamaları geçirmeden önce bu grupları Azure AD ile eşitlemek için [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) kullanın. Uygulama geçirildiğinde aynı kullanıcılara erişim sağlayabilmeniz için geçişten önce bu grupları ve üyeliği doğruladığınızdan emin olun.

Daha fazla bilgi için, [Active Directory 'ten eşitlenen grup özniteliklerini kullanma önkoşulları](../hybrid/how-to-connect-fed-group-claims.md)konusuna bakın.

### <a name="set-up-user-self-provisioning"></a>Kullanıcı kendi kendini sağlamayı ayarla

Bazı SaaS uygulamaları, kullanıcıların uygulamada ilk kez oturum açtıklarında kendi kendini sağlama yeteneğini destekler. Azure AD 'de uygulama sağlama, kullanıcıların erişmesi gereken bulut ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) uygulamalarında kullanıcı kimliklerinin ve rollerinin otomatik olarak oluşturulmasını ifade eder. Geçirilen kullanıcıların SaaS uygulamasında bir hesabı zaten vardır. Geçiş sağlandıktan sonra eklenen tüm yeni kullanıcılar. Uygulama geçirildikten sonra [SaaS uygulama sağlamasını](../app-provisioning/user-provisioning.md) test edin.

### <a name="sync-external-users-in-azure-ad"></a>Azure AD 'de dış kullanıcıları eşitleme

Mevcut dış kullanıcılarınız bu iki yolla AD FS ayarlanabilir:

* **Kuruluşunuzda yerel bir hesabı olan dış kullanıcılar**— bu hesapları, iç kullanıcı hesaplarınızla aynı şekilde kullanmaya devam edersiniz. Bu dış Kullanıcı hesaplarının, kuruluşunuzda bir ilke adı vardır, ancak hesabın e-postası dışarıdan işaret edebilir. Geçişiniz ile ilerleyerek, bu kullanıcıları bu tür bir kimlik kullanılabilir olduğunda kendi şirket kimliklerini kullanacak şekilde geçirerek [Azure AD B2B](../external-identities/what-is-b2b.md) 'nin sunduğu avantajlardan yararlanabilirsiniz. Bu, genellikle kendi kurumsal oturum açma bilgileriyle oturum açtığı için bu kullanıcılar için oturum açma sürecini kolaylaştırır. Kuruluşunuzun yönetimi, dış kullanıcılar için hesapları yönetmek zorunda kalmadan da daha kolay.
* **Federasyon dış kimlikleri**— Şu anda bir dış kuruluşla Federasyonunuzda yapmanız gereken birkaç yaklaşım vardır:
  * [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleyin](../external-identities/add-users-administrator.md). Bireysel üyelerin, kullanıldıkları uygulamaları ve varlıkları kullanmaya devam edebilmesi için Azure AD Yönetim portalından ortak kuruluşa B2B işbirliği davetlerini proaktif olarak gönderebilirsiniz.
  * B2B davetiyesi API 'sini kullanarak iş ortağı kuruluşunuzda bireysel kullanıcılar için bir istek üreten [self SERVIS B2B kaydolma iş akışı oluşturun](../external-identities/self-service-portal.md) .

Mevcut dış kullanıcılarınızın nasıl yapılandırıldığına bakılmaksızın, grup üyeliğinde veya belirli izinlerde kendi hesabıyla ilişkili izinleri olabilir. Bu izinlerin geçirilmesi veya temizlenmesi gerekip gerekmediğini değerlendirin. Bir dış kullanıcıyı temsil eden kuruluşunuzdaki hesapların, Kullanıcı bir dış kimliğe geçirildikten sonra devre dışı bırakılması gerekir. Kaynaklarınızın bağlanmasına yönelik bir kesinti olabileceğinden, geçiş işlemi iş ortaklarınızla birlikte ele alınmalıdır.

## <a name="migrate-and-test-your-apps"></a>Uygulamalarınızı geçirin ve test edin

Bu makalede ayrıntılı olarak açıklanan geçiş sürecini izleyin. Ardından, geçişin başarılı olup olmadığını test etmek için [Azure Portal](https://aad.portal.azure.com/) gidin.

Şu yönergeleri izleyin:

1. **Kurumsal uygulamalar**  >  **tüm uygulamalar** ' ı seçin ve listeden uygulamanızı bulun.
1.   >  Uygulamaya en az bir kullanıcı veya grup atamak için **kullanıcıları ve grupları yönet '** i seçin.
1.   >  **Koşullu erişimi** Yönet ' i seçin. İlke listenizi gözden geçirin ve [koşullu erişim ilkesiyle](../conditional-access/overview.md)uygulamaya erişimi engellemediğinizden emin olun.

Uygulamanızı nasıl yapılandırdığınıza bağlı olarak, SSO 'nun düzgün çalıştığını doğrulayın.

| Kimlik doğrulaması türü| Test Etme |
| :- | :- |
| OAuth/OpenID Connect| **Kurumsal uygulamalar > izinler** ' i seçin ve uygulamanız için Kullanıcı ayarlarındaki uygulamaya onay aldığınızdan emin olun.|
| SAML tabanlı SSO | **Çoklu oturum açma** altında bulunan [Test SAML ayarları](debug-saml-sso-issues.md) düğmesini kullanın. |
| Password-Based SSO 'SU |  [Uygulamaps güvenli oturum açma](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [uzantısını](../user-help/my-apps-portal-end-user-access.md)indirip yükleyin. Bu uzantı, bir SSO işlemi kullanmanızı gerektiren kuruluşunuzun bulut uygulamalarından herhangi birini başlatmanıza yardımcı olur. |
| Uygulama Ara Sunucusu | Bağlayıcının çalıştığından ve uygulamanıza atandığından emin olun. Daha fazla yardım için [uygulama proxy sorun giderme kılavuzunu](application-proxy-troubleshoot.md) ziyaret edin. |

> [!NOTE]
> Eski AD FS ortamından gelen tanımlama bilgileri kullanıcı makinelerinde kalır. Bu tanımlama bilgileri, kullanıcıların eski AD FS oturum açma ortamına yönlendirilebilmesi için yeni Azure AD oturumuna karşı geçiş ile ilgili sorunlara neden olabilir. Kullanıcı tarayıcısı tanımlama bilgilerini el ile veya bir komut dosyası kullanarak temizlemeniz gerekebilir. System Center Configuration Manager veya benzer bir platform da kullanabilirsiniz.

### <a name="troubleshoot"></a>Sorun giderme

Geçirilen uygulamaların testinizden herhangi bir hata varsa, sorun giderme, mevcut AD FS bağlı olan taraflara geri dönmeden önce ilk adım olabilir. [Azure Active Directory içindeki uygulamalarda SAML tabanlı çoklu oturum açma hatalarını ayıklama](debug-saml-sso-issues.md)bölümüne bakın.

### <a name="rollback-migration"></a>Geçişi geri alma

Geçiş başarısız olursa, mevcut bağlı olan taraflardan AD FS sunucularında ayrılmanız ve bağlı olan taraflara erişimi kaldırmanız önerilir. Bu, dağıtım sırasında gerektiğinde hızlı bir geri dönüş sağlar.

### <a name="employee-communication"></a>Çalışan iletişimi

Planlı kesinti penceresinin kendisi en düşük olabilmesine karşın, AD FS 'ten Azure AD 'ye geçiş yaparken bu zaman çerçevelerini çalışanlara karşı bir süre sonra da planlamanız gerekir. Uygulama deneyiminizin bir geri bildirim düğmesine veya sorunlar için yardım masasına yönelik işaretçilere sahip olduğundan emin olun.

Dağıtım tamamlandıktan sonra, başarılı bir dağıtım için kullanıcıları bilgilendirebilirler ve bunları yapması gereken adımların her birini hatırlatabilir.

* Kullanıcılara, geçirilen tüm uygulamalara erişmek için [uygulamalarımı](https://myapps.microsoft.com) kullanmalarını söyleyin.
* Kullanıcılara MFA ayarlarını güncelleştirmesi gerekebilecek kullanıcıları hatırlatın.
* Self-Service parola sıfırlama dağıtılırsa, kullanıcıların kimlik doğrulama yöntemlerini güncelleştirmesi veya doğrulaması gerekebilir. Bkz. [MFA](https://aka.ms/mfatemplates) ve [SSPR](https://aka.ms/ssprtemplates) Son Kullanıcı iletişim şablonları.

### <a name="external-user-communication"></a>Dış Kullanıcı iletişimi

Bu Kullanıcı grubu genellikle sorunlar durumunda en kritik düzeyde etkilendi. Bu özellikle, güvenlik sonrası, dış iş ortakları için farklı koşullu erişim kuralları veya risk profilleri kümesini belirlemesi durumunda geçerlidir. Dış iş ortaklarının, bulut geçiş zamanlamasının farkında olduğundan ve dış işbirliğine özgü tüm akışları test eden bir pilot dağıtıma katılması gereken bir zaman dilimi bulunduğundan emin olun. Son olarak, sorunlar söz konusu olduğunda yardım masasına erişmek için bir yol olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama kimlik doğrulamasını Azure AD 'ye geçirme](https://aka.ms/migrateapps/whitepaper)konusunu okuyun.
* [Koşullu erişim](../conditional-access/overview.md) ve [MFA](../authentication/concept-mfa-howitworks.md)'yı ayarlayın.
* Adım adım bir kod örneği deneyin:[geliştiriciler Için Azure AD uygulama geçiş PlayBook 'a AD FS](https://aka.ms/adfsplaybook).
