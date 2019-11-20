---
title: Azure Access Control Service geçir | Microsoft Docs
description: Uygulamaları ve Hizmetleri Azure Access Control Service (ACS) taşıma seçenekleri hakkında bilgi edinin.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59a2cc971fbc1df967bc2655c672ab8f419eef71
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835525"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Nasıl yapılır: Azure Access Control Service'ten geçiş yapma

Microsoft Azure Access Control Service (ACS), bir Azure Active Directory (Azure AD) hizmeti 7 Kasım 2018 tarihinde kullanımdan kaldırılacaktır. Şu anda Access Control kullanan uygulamalar ve hizmetler, daha sonra farklı bir kimlik doğrulama mekanizmasına tamamen geçirilmesi gerekir. Bu makalede, Access Control kullanımını kullanımdan kaldırmayı planladığınız için geçerli müşterilere yönelik öneriler açıklanır. Şu anda Access Control kullanmıyorsanız, herhangi bir işlem yapmanız gerekmez.

## <a name="overview"></a>Genel Bakış

Access Control, Web uygulamalarınıza ve hizmetlerinize erişim için kullanıcıların kimliğini doğrulamak ve yetkilendirmek için kolay bir yol sunan bir bulut kimlik doğrulama hizmetidir. Kimlik doğrulaması ve yetkilendirmenin pek çok özelliğinin kodunuzla bir şekilde oluşturulmasına izin verir. Access Control, öncelikle Microsoft .NET istemcilerinin, ASP.NET Web uygulamalarının ve Windows Communication Foundation (WCF) Web hizmetlerinin geliştiriciler ve mimarları tarafından kullanılır.

Access Control için kullanım örnekleri, üç ana kategoriye ayrılabilir:

- Azure Service Bus ve Dynamics CRM dahil olmak üzere belirli Microsoft bulut hizmetlerinde kimlik doğrulaması. İstemci uygulamaları, çeşitli eylemler gerçekleştirmek için bu hizmetlerde kimlik doğrulaması yapmak üzere Access Control belirteçleri elde eder.
- Hem özel hem de önceden paketlenmiş (SharePoint gibi) Web uygulamalarına kimlik doğrulaması ekleme. Web uygulamaları Access Control "pasif" kimlik doğrulamasını kullanarak, Microsoft hesabı (eski adıyla canlı KIMLIK) ve Google, Facebook, Yahoo, Azure AD ve Active Directory Federasyon Hizmetleri (AD FS) (AD FS) hesaplarından oturum açma desteği sağlayabilir.
- Access Control tarafından verilen belirteçlerle özel Web hizmetlerini güvenli hale getirme. Web Hizmetleri, "etkin" kimlik doğrulamasını kullanarak yalnızca Access Control ile kimliği doğrulanmış bilinen istemcilere erişime izin verdiklerinden emin olabilir.

Bu kullanım örneklerinin ve önerilen geçiş stratejilerinin her biri, aşağıdaki bölümlerde ele alınmıştır.

> [!WARNING]
> Çoğu durumda, mevcut uygulamaları ve hizmetleri daha yeni teknolojilere geçirmek için önemli kod değişiklikleri gerekir. Olası kesintileri veya kapalı kalma süresini önlemek için geçişinizi planlamaya ve çalıştırmaya hemen başlamanız önerilir.

Access Control aşağıdaki bileşenlere sahiptir:

- Kimlik doğrulama isteklerini alan ve döndürülen güvenlik belirteçlerini veren bir güvenli belirteç hizmeti (STS).
- Access Control ad alanlarını oluşturduğunuz, sildiğiniz ve etkinleştirdiğiniz ve devre dışı bırakabileceğiniz klasik Azure portalı.
- Access Control ad alanlarını özelleştirdiğiniz ve yapılandırdığınız ayrı bir Access Control yönetim portalı.
- Portalların işlevlerini otomatikleştirebilmek için kullanabileceğiniz bir yönetim hizmeti.
- Sorunları Access Control belirteçlerin çıkış biçimini işlemek için karmaşık mantığı tanımlamak üzere kullanabileceğiniz bir belirteç dönüştürme kuralı altyapısı.

Bu bileşenleri kullanmak için bir veya daha fazla Access Control ad alanı oluşturmanız gerekir. *Ad alanı* , uygulama ve hizmetlerinizin iletişim kurduğu Access Control adanmış bir örneğidir. Ad alanı diğer tüm Access Control müşterilerinden yalıtılmıştır. Diğer Access Control müşteriler kendi ad alanlarını kullanır. Access Control bir ad alanı, şuna benzer bir özel URL 'ye sahiptir:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

STS ve yönetim işlemleriyle tüm iletişimler bu URL 'de yapılır. Farklı amaçlar için farklı yollar kullanırsınız. Uygulamalarınızın veya hizmetlerinizin Access Control kullanıp kullanmadığını öğrenmek için, https://&lt;Namespace&gt;. AccessControl.Windows.net öğesine giden trafiği izleyin. Bu URL 'ye giden tüm trafik Access Control tarafından işlenir ve üretimi devam etmek gerekir. 

Bunun özel durumu, için `https://accounts.accesscontrol.windows.net`tüm trafiktir. Bu URL 'ye giden trafik zaten farklı bir hizmet tarafından işlenmiştir ve Access Control kullanımdan kalkarak etkilenmemektedir. 

Access Control hakkında daha fazla bilgi için bkz. [Access Control Service 2,0 (arşivlenmiş)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Uygulamalarınızdan hangilerinin etkileneceği hakkında bilgi edinin

Bu bölümdeki adımları izleyerek, hangi uygulamalarınızdan ACS kullanımdan hangilerinin etkilendiğini öğrenin.

### <a name="download-and-install-acs-powershell"></a>ACS PowerShell 'i indirme ve yükleme

1. PowerShell Galerisi gidin ve [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)' i indirin.
1. Modülünü çalıştırarak

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. Şunu çalıştırarak tüm olası komutların bir listesini alın

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Belirli bir komut hakkında yardım almak için şunu çalıştırın:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    Burada `[Command-Name]` ACS komutunun adıdır.

### <a name="list-your-acs-namespaces"></a>ACS ad alanlarınızı listeleme

1. **Connect-AcsAccount** cmdlet 'INI kullanarak ACS 'ye bağlanın.
  
    Komutları yürütebilmeniz ve komutları `Set-ExecutionPolicy -ExecutionPolicy Bypass` yürütmek için bu aboneliklerin yöneticisi olmanız gerekir.

1. **Get-AcsSubscription** cmdlet 'ini kullanarak kullanılabilir Azure aboneliklerinizi listeleyin.
1. **Get-AcsNamespace** cmdlet 'INI kullanarak ACS ad alanlarınızı listeleyin.

### <a name="check-which-applications-will-be-impacted"></a>Hangi uygulamaların etkilendiğini denetleyin

1. Önceki adımda bulunan ad alanını kullanın ve şuraya gidin`https://<namespace>.accesscontrol.windows.net`

    Örneğin, ad alanlarından biri contoso-test ise şuraya gidin`https://contoso-test.accesscontrol.windows.net`

1. **Güven ilişkileri**altında, ACS kullanımdan kaldırma tarafından etkilenecek uygulamaların listesini görmek Için **bağlı olan taraf uygulamaları** ' nı seçin.
1. Sahip olduğunuz diğer tüm ACS ad alanı (ler) için 1-2 adımlarını yineleyin.

## <a name="retirement-schedule"></a>Emeklilik zamanlaması

2017 Kasım itibariyle tüm Access Control bileşenleri tam olarak desteklenir ve çalışır. Tek kısıtlama, [Klasik Azure portalı aracılığıyla yeni Access Control ad alanları oluşturamıyoruz](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Access Control bileşenleri kullanımdan kaldırmaya yönelik zamanlama aşağıda verilmiştir:

- **2017 Kasım**:  Klasik Azure portalında Azure AD yönetici deneyimi [kullanımdan kaldırıldı](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). Bu noktada, Access Control için ad alanı yönetimi yeni ve adanmış bir URL 'de kullanılabilir: `https://manage.windowsazure.com?restoreClassic=true`. Bu URl 'yi kullanarak mevcut ad alanlarınızı görüntüleyin, ad alanlarını etkinleştirin ve devre dışı bırakın ve seçeneğini belirlerseniz ad alanlarını silin.
- **2 nisan 2018**: Klasik Azure portalı tamamen devre dışı bırakıldı, anlamı Access Control ad alanı yönetimi artık herhangi bir URL aracılığıyla kullanılamaz. Bu noktada, Access Control ad alanlarınızı devre dışı bırakıp etkinleştiremez, silemez veya numaralandırabilirsiniz. Ancak, Access Control yönetim portalı tam olarak çalışır ve konumunda `https://\<namespace\>.accesscontrol.windows.net`bulunur. Tüm Access Control diğer bileşenleri normal şekilde çalışmaya devam eder.
- **7 kasım 2018**: Tüm Access Control bileşenleri kalıcı olarak kapatılır. Buna Access Control yönetim portalı, yönetim hizmeti, STS ve Token dönüşüm kuralı altyapısı dahildir. Bu noktada, Access Control gönderilen tüm istekler (Namespace \<\>. AccessControl.Windows.net konumunda bulunur) başarısız olur. Var olan tüm uygulamaları ve Hizmetleri, bu süreden daha önce geçen diğer teknolojilere geçirmelisiniz.

> [!NOTE]
> İlke bir süre belirteci istememiş olan ad alanlarını devre dışı bırakır. 2018 Eylül ayının başlarında bu süre, şu anda en fazla 14 gün boyunca etkinlik dışı kalır, ancak önümüzdeki haftalarda 7 gün boyunca işlem yapılmadan kısaltıldı. Şu anda devre dışı bırakılmış Access Control ad alanlarınız varsa, ad alanlarını yeniden etkinleştirmek için [ACS PowerShell 'i indirip yükleyebilirsiniz](#download-and-install-acs-powershell) .

## <a name="migration-strategies"></a>Geçiş stratejileri

Aşağıdaki bölümlerde, Access Control diğer Microsoft teknolojilerine geçiş için üst düzey öneriler açıklanır.

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft Cloud Services istemcileri

Access Control tarafından verilen belirteçleri kabul eden her bir Microsoft bulut hizmeti artık en az bir farklı kimlik doğrulama biçimini desteklemektedir. Doğru kimlik doğrulama mekanizması her hizmet için farklılık gösterir. Resmi rehberlik için her bir hizmet için belirli belgelere başvurmanız önerilir. Kolaylık sağlaması için her belge kümesi şurada sunulmaktadır:

| Hizmet | Rehber |
| ------- | -------- |
| Azure Service Bus | [Paylaşılan erişim imzalarına geçiş](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus geçişi | [Paylaşılan erişim imzalarına geçiş](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure yönetilen önbelleği | [Redsıs için Azure önbelleğine geçiş](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Bilişsel Hizmetler API'si geçirin](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Azure App Service Logic Apps özelliğine geçiş yapın](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Azure AD kimlik doğrulamasına geçiş](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Azure Backup aracısını yükseltme](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint müşterileri

SharePoint 2013, 2016 ve SharePoint Online müşterileri, bulutta, şirket içinde ve hibrit senaryolarında kimlik doğrulama amacıyla ACS 'yi uzun süredir kullandı. Bazı SharePoint özellikleri ve kullanım durumları, ACS kullanımdan kaldırılırken etkilenir, diğerleri bu durumdan etkilenmez. Aşağıdaki tabloda, ACS 'den yararlanan en popüler SharePoint özelliklerinden bazıları için geçiş kılavuzu özetlenmektedir:

| Özellik | Rehber |
| ------- | -------- |
| Azure AD 'de kullanıcıların kimliğini doğrulama | Daha önce Azure AD, kimlik doğrulaması için SharePoint için gereken SAML 1,1 belirteçlerini desteklemediğinden ACS, SharePoint 'in Azure AD belirteç biçimleriyle uyumlu hale getirilen bir aracı olarak kullanılmıştı. Artık [SharePoint 'i şirket içi uygulama Azure AD uygulaması Galeri kullanarak doğrudan Azure AD 'ye bağlayabilirsiniz](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [Şirket içi SharePoint 'te sunucudan sunucuya kimlik doğrulaması & uygulama kimlik doğrulaması](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | ACS emekliliğinizi etkilemez; değişiklik gerekmiyor. | 
| [SharePoint eklentileri için düşük güven yetkilendirmesi (sağlayıcı barındırılan ve SharePoint 'te barındırılan)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | ACS emekliliğinizi etkilemez; değişiklik gerekmiyor. |
| [SharePoint bulutu karma arama](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | ACS emekliliğinizi etkilemez; değişiklik gerekmiyor. |

### <a name="web-applications-that-use-passive-authentication"></a>Pasif kimlik doğrulaması kullanan Web uygulamaları

Kullanıcı kimlik doğrulaması için Access Control kullanan Web uygulamaları için, Access Control Web uygulaması geliştiricileri ve mimarları için aşağıdaki özellikleri ve yetenekleri sağlar:

- Windows Identity Foundation (WıF) ile derin tümleştirme.
- Google, Facebook, Yahoo, Azure Active Directory ve AD FS hesapları ve Microsoft hesaplarıyla Federasyon.
- Aşağıdaki kimlik doğrulama protokolleri için destek: OAuth 2,0 taslak 13, WS-Trust ve Web Hizmetleri Federasyonu (WS-Federation).
- Aşağıdaki belirteç biçimleri için destek: JSON Web Token (JWT), SAML 1,1, SAML 2,0 ve basit Web belirteci (SWT).
- WıF ile tümleştirilmiş, kullanıcıların oturum açmak için kullandıkları hesap türünü seçmesini sağlayan bir giriş bölgesi bulma deneyimi. Bu deneyim, Web uygulaması tarafından barındırılır ve tamamen özelleştirilebilir.
- Web uygulaması tarafından alınan taleplerin Access Control, aşağıdakiler de dahil olmak üzere zengin özelleştirmeye izin veren belirteç dönüşümü:
    - Kimlik sağlayıcılarından gelen talepleri geçirin.
    - Ek özel talepler ekleme.
    - Belirli koşullarda talepler vermek için basit if-then mantığı.

Ne yazık ki, bu eşdeğer yeteneklerin tümünü sunan bir hizmet yok. Hangi Access Control özellikleri değerlendirmelisiniz ve sonra [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) veya başka bir bulut kimlik doğrulama hizmeti arasında seçim yapmanız gerekir.

#### <a name="migrate-to-azure-active-directory"></a>Azure Active Directory geçir

Göz önünde bulundurulması gereken bir yol, uygulamalarınızı ve hizmetlerinizi doğrudan Azure AD ile tümleştirmenizi sağlar. Azure AD, Microsoft iş veya okul hesapları için bulut tabanlı kimlik sağlayıcıdır. Azure AD, Office 365, Azure ve çok daha fazlası için kimlik sağlayıcıdır. Access Control için benzer federe kimlik doğrulama özellikleri sağlar, ancak tüm Access Control özelliklerini desteklemez. 

Birincil örnek, Facebook, Google ve Yahoo gibi sosyal kimlik sağlayıcılarının bulunduğu bir federasyondır. Kullanıcılarınız bu kimlik bilgileri türleriyle oturum açtığında, Azure AD sizin için çözüm değildir. 

Azure AD aynı zamanda Access Control ile aynı kimlik doğrulama protokollerini desteklemeyede gerek yoktur. Örneğin, hem Access Control hem de Azure AD OAuth desteklese de, her uygulama arasında hafif farklar vardır. Farklı uygulamalar, bir geçişin parçası olarak kodu değiştirmenizi gerektirir.

Ancak, Azure AD, Access Control müşterilere çeşitli olası avantajlar sağlar. Bulutta barındırılan Microsoft iş veya okul hesaplarını yerel olarak destekler, bu da genellikle Access Control müşterileri tarafından kullanılır. 

Azure AD kiracısı, AD FS aracılığıyla bir veya daha fazla şirket içi Active Directory örneğine de federe olabilir. Bu şekilde, uygulamanız şirket içinde barındırılan bulut tabanlı kullanıcıların ve kullanıcıların kimliğini doğrulayabilir. Ayrıca, WıF kullanarak bir Web uygulamasıyla tümleştirmeyi oldukça basit hale getiren WS-Federation protokolünü de destekler.

Aşağıdaki tabloda, Azure AD 'de bulunan özelliklerle Web uygulamalarıyla ilgili Access Control özellikleri karşılaştırılır. 

*Kullanıcıların yalnızca Microsoft iş veya okul hesaplarıyla oturum açmalarına izin verirseniz, büyük bir düzeyde Azure Active Directory geçişiniz için en iyi seçenektir*.

| Özellik | Access Control desteği | Azure AD desteği |
| ---------- | ----------- | ---------------- |
| **Hesap türleri** | | |
| Microsoft iş veya okul hesapları | Desteklenen | Desteklenen |
| Windows Server Active Directory ve AD FS hesapları |-Azure AD kiracısı ile Federasyon aracılığıyla desteklenir <br />-AD FS ile doğrudan Federasyon aracılığıyla desteklenir | Yalnızca bir Azure AD kiracısı ile Federasyon aracılığıyla desteklenir | 
| Diğer kurumsal kimlik yönetimi sistemlerinden hesaplar |-Azure AD kiracısı ile Federasyon aracılığıyla mümkündür <br />-Doğrudan Federasyon aracılığıyla desteklenir | Azure AD kiracısı ile Federasyon aracılığıyla mümkün |
| Kişisel kullanım için Microsoft hesapları | Desteklenen | Azure AD v 2.0 OAuth protokolü aracılığıyla desteklenir, ancak başka protokoller üzerinden desteklenmez | 
| Facebook, Google, Yahoo hesapları | Desteklenen | Desteklenmeyen bir şekilde desteklenmiyor |
| **Protokoller ve SDK uyumluluğu** | | |
| WIF | Desteklenen | Desteklenir, ancak sınırlı yönergeler mevcuttur |
| WS-Federation | Desteklenen | Desteklenen |
| OAuth 2.0 | Taslak 13 desteği | En modern belirtim olan RFC 6749 için destek |
| WS-Trust | Desteklenen | Desteklenmiyor |
| **Belirteç biçimleri** | | |
| JWT | Beta sürümünde destekleniyor | Desteklenen |
| SAML 1.1 | Desteklenen | Önizleme |
| SAML 2.0 | Desteklenen | Desteklenen |
| SWT | Desteklenen | Desteklenmiyor |
| **Melere** | | |
| Özelleştirilebilir giriş bölgesi bulma/hesap-seçme Kullanıcı arabirimi | Uygulamalara dahil edilebilir indirilebilir kod | Desteklenmiyor |
| Özel belirteç imzalama sertifikalarını karşıya yükle | Desteklenen | Desteklenen |
| Belirteçlerde talepleri özelleştirme |-Kimlik sağlayıcılarından gelen giriş taleplerini geçirme<br />-Kimlik sağlayıcısından bir talep olarak erişim belirteci alın<br />-Giriş taleplerinin değerlerine göre çıkış taleplerini verme<br />-Sabit değerlerle çıkış talepleri verme |-Federal Kimlik sağlayıcılarından talepler geçirilemez<br />-Kimlik sağlayıcısından bir talep olarak erişim belirteci alınamıyor<br />-Giriş taleplerinin değerlerine göre çıkış talepleri veremez<br />-Sabit değerlerle çıkış talepleri verebilir<br />-Azure AD ile eşitlenen kullanıcıların özelliklerine göre çıkış talepleri verebilir |
| **Otomasyon** | | |
| Yapılandırma ve yönetim görevlerini otomatikleştirin | Access Control Management Service aracılığıyla desteklenir | Microsoft Graph ve Azure AD Graph API aracılığıyla desteklenir |

Azure AD 'nin Uygulamalarınız ve hizmetleriniz için en iyi geçiş yolu olduğuna karar verirseniz, uygulamanızı Azure AD ile tümleştirmenin iki yolunu bilmelisiniz.

WS-Federation veya WıF 'yi Azure AD ile tümleştirmede kullanmak için, [Galeri dışı bir uygulama için Federasyon çoklu oturum açmayı yapılandırma](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)bölümünde açıklanan yaklaşımı takip etmenizi öneririz. Bu makale, Azure AD 'yi SAML tabanlı çoklu oturum açma için yapılandırma ve ayrıca WS-Federation yapılandırması için de kullanılır. Bu yaklaşımın ardından Azure AD Premium lisansı gerekir. Bu yaklaşımın iki avantajı vardır:

- Azure AD belirteç özelleştirmesi 'nın tam esnekliğini elde edersiniz. Azure AD tarafından verilen talepleri, Access Control tarafından verilen taleplerle eşleşecek şekilde özelleştirebilirsiniz. Bu özellikle Kullanıcı KIMLIĞI veya ad tanımlayıcı talebi içerir. Teknolojileri değiştirdikten sonra kullanıcılarınız için tutarlı kullanıcı tanımlayıcıları almaya devam etmek için Azure AD tarafından verilen kullanıcı kimliklerinin Access Control tarafından verilen olanlarla eşleştiğinden emin olun.
- Uygulamanıza özel bir belirteç imzalama sertifikası ve denetlediğiniz bir yaşam süresi yapılandırabilirsiniz.

> [!NOTE]
> Bu yaklaşım için Azure AD Premium lisansı gerekir. Bir Access Control müşterisiyseniz ve bir uygulama için çoklu oturum açma ayarı yapmak üzere Premium lisansa ihtiyacınız varsa bizimle iletişime geçin. Sizin için geliştirici lisansları sağlamamız gerekir.

Alternatif bir yaklaşım, WS-Federation kurulumu için biraz farklı yönergeler sunan [Bu kod örneğini](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)izlemelidir. Bu kod örneği, ASP.NET 4,5 OWıN ara yazılımı yerine WıF 'yi kullanmaz. Ancak, uygulama kaydı için yönergeler WıF kullanan uygulamalar için geçerlidir ve Azure AD Premium lisansı gerektirmez. 

Bu yaklaşımı seçerseniz, [Azure AD 'de imzalama anahtarı geçişi](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)' ni anlamanız gerekir. Bu yaklaşım, belirteçleri vermek için Azure AD Genel imzalama anahtarını kullanır. Varsayılan olarak, WıF imzalama anahtarlarını otomatik olarak yenilemez. Azure AD, genel imza anahtarlarını döndürdüğünde, değişiklikleri kabul etmek için WıF uygulamanızın hazırlanması gerekir. Daha fazla bilgi için bkz. [Azure AD 'de anahtar geçişi imzalama hakkında önemli bilgiler](https://msdn.microsoft.com/library/azure/dn641920.aspx).

OpenID Connect veya OAuth protokolleri aracılığıyla Azure AD ile tümleştirilebilir, bunu yapmanızı öneririz. Azure AD [Geliştirici](https://aka.ms/aaddev)kılavuzumuzdan Azure AD 'yi Web uygulamanızla tümleştirme hakkında kapsamlı belge ve yönergeler sunuyoruz.

#### <a name="migrate-to-azure-active-directory-b2c"></a>Azure Active Directory B2C geçir

Dikkate alınması gereken diğer geçiş yolu Azure AD B2C. Azure AD B2C, Access Control gibi geliştiricilerin kimlik doğrulama ve kimlik yönetimi mantığının bir bulut hizmetine dış oluşturulmasına olanak tanıyan bir bulut kimlik doğrulama hizmetidir. Milyonlarca etkin kullanıcıya sahip olabilecek, tüketiciye yönelik uygulamalar için tasarlanan ücretli bir hizmettir (ücretsiz ve Premium katmanlarıyla).

Access Control gibi, Azure AD B2C en çekici özelliklerinden biri de birçok farklı hesap türünü destekledir. Azure AD B2C, kullanıcıların Microsoft hesabı veya Facebook, Google, LinkedIn, GitHub veya Yahoo hesaplarını ve daha fazlasını kullanarak oturum açabilirsiniz. Azure AD B2C Ayrıca, kullanıcıların özel olarak uygulamanız için oluşturdıkları "yerel hesaplar" veya Kullanıcı adı ve parolaları da destekler. Azure AD B2C Ayrıca, oturum açma akışlarınızı özelleştirmek için kullanabileceğiniz zengin genişletilebilirlik sağlar. 

Ancak, Azure AD B2C, müşterilerin ihtiyaç duyduğu Access Control kimlik doğrulama protokollerinin ve belirteç biçimlerinin kapsamını desteklemez. Ayrıca, kimlik sağlayıcısından Microsoft veya başka bir şekilde Kullanıcı hakkında ek bilgiler için belirteçleri almak ve Azure AD B2C kullanamazsınız.

Aşağıdaki tabloda, Web uygulamalarıyla ilgili Access Control özellikleri, Azure AD B2C mevcut olanlarla karşılaştırılır. Büyük bir düzeyde, *uygulamanız tüketici ile sunulursa veya birçok farklı hesap türünü destekliyorsa, Azure AD B2C geçişinizin doğru seçimidir.*

| Özellik | Access Control desteği | Azure AD B2C desteği |
| ---------- | ----------- | ---------------- |
| **Hesap türleri** | | |
| Microsoft iş veya okul hesapları | Desteklenen | Özel ilkeler aracılığıyla desteklenir  |
| Windows Server Active Directory ve AD FS hesapları | AD FS ile doğrudan Federasyon aracılığıyla desteklenir | Özel ilkeler kullanılarak SAML Federasyonu aracılığıyla desteklenir |
| Diğer kurumsal kimlik yönetimi sistemlerinden hesaplar | WS-Federation aracılığıyla doğrudan Federasyon aracılığıyla desteklenir | Özel ilkeler kullanılarak SAML Federasyonu aracılığıyla desteklenir |
| Kişisel kullanım için Microsoft hesapları | Desteklenen | Desteklenen | 
| Facebook, Google, Yahoo hesapları | Desteklenen | Facebook ve Google desteklenen yerel olarak, özel ilkeler kullanılarak OpenID Connect Federasyonu aracılığıyla desteklenir |
| **Protokoller ve SDK uyumluluğu** | | |
| Windows Identity Foundation (WıF) | Desteklenen | Desteklenmiyor |
| WS-Federation | Desteklenen | Desteklenmiyor |
| OAuth 2.0 | Taslak 13 desteği | En modern belirtim olan RFC 6749 için destek |
| WS-Trust | Desteklenen | Desteklenmiyor |
| **Belirteç biçimleri** | | |
| JWT | Beta sürümünde destekleniyor | Desteklenen |
| SAML 1.1 | Desteklenen | Desteklenmiyor |
| SAML 2.0 | Desteklenen | Desteklenmiyor |
| SWT | Desteklenen | Desteklenmiyor |
| **Melere** | | |
| Özelleştirilebilir giriş bölgesi bulma/hesap-seçme Kullanıcı arabirimi | Uygulamalara dahil edilebilir indirilebilir kod | Özel CSS aracılığıyla tamamen özelleştirilebilir kullanıcı arabirimi |
| Özel belirteç imzalama sertifikalarını karşıya yükle | Desteklenen | Özel ilkeler aracılığıyla desteklenen, sertifikalar değil özel İmzalama anahtarları |
| Belirteçlerde talepleri özelleştirme |-Kimlik sağlayıcılarından gelen giriş taleplerini geçirme<br />-Kimlik sağlayıcısından bir talep olarak erişim belirteci alın<br />-Giriş taleplerinin değerlerine göre çıkış taleplerini verme<br />-Sabit değerlerle çıkış talepleri verme |-Kimlik sağlayıcılarından talepler geçirebilir; Bazı talepler için gerekli özel ilkeler<br />-Kimlik sağlayıcısından bir talep olarak erişim belirteci alınamıyor<br />-Özel ilkeler aracılığıyla giriş taleplerinin değerlerine göre çıkış talepleri verebilir<br />-Özel ilkeler aracılığıyla sabit değerlerle çıkış talepleri verebilir |
| **Otomasyon** | | |
| Yapılandırma ve yönetim görevlerini otomatikleştirin | Access Control Management Service aracılığıyla desteklenir |-Azure AD Graph API aracılığıyla izin verilen Kullanıcı oluşturma<br />-Program aracılığıyla B2C kiracılar, uygulamalar veya ilkeler oluşturulamıyor |

Azure AD B2C, uygulamalarınız ve hizmetleriniz için en iyi geçiş yolu olduğuna karar verirseniz, aşağıdaki kaynaklarla başlayın:

- [Azure AD B2C belgeleri](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Özel ilke Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Ping kimliğine veya Auth0 geçiş

Bazı durumlarda, Azure AD ve Azure AD B2C, büyük bir kod değişikliği yapmadan Web uygulamalarınızda Access Control değiştirmek için yeterli olmamasını fark edebilirsiniz. Bazı yaygın örneklerde şunlar bulunabilir:

- Google veya Facebook gibi sosyal kimlik sağlayıcıları ile oturum açmak için WıF veya WS-Federation kullanan Web uygulamaları.
- WS-Federation protokolü üzerinden kurumsal kimlik sağlayıcısına doğrudan Federasyon gerçekleştiren Web uygulamaları.
- Access Control tarafından verilen belirteçlerde bir talep olarak sosyal kimlik sağlayıcısı tarafından (Google veya Facebook gibi) verilen erişim belirtecini gerektiren Web uygulamaları.
- Azure AD veya Azure AD B2C tarafından yeniden oluşturulabilmesi için karmaşık belirteç dönüştürme kurallarına sahip Web uygulamaları.
- Federasyonı merkezi olarak birçok farklı kimlik sağlayıcısına merkezi olarak yönetmek için ACS kullanan çok kiracılı web uygulamaları

Bu durumlarda, Web uygulamanızı başka bir bulut kimlik doğrulama hizmetine geçirmeyi düşünmek isteyebilirsiniz. Aşağıdaki seçenekleri araştırmayı öneririz. Aşağıdaki seçeneklerden her biri, Access Control benzer yetenekler sunar:

|     |     |
| --- | --- |
| ![Bu görüntüde Auth0 logosu gösterilmektedir](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) , [Access Control müşterileri için üst düzey geçiş kılavuzu](https://auth0.com/acs)oluşturmuş ve ACS 'nin oluşturduğu neredeyse her özelliği desteklediği esnek bir bulut kimlik hizmetidir. |
| ![Bu görüntüde ping kimlik logosu gösterilmektedir](./media/active-directory-acs-migration/rsz_ping.png) | [Ping kimliği](https://www.pingidentity.com) , ACS 'ye benzer iki çözüm sunar. PingOne, ACS ile aynı özelliklerin birçoğunu destekleyen bir bulut kimlik hizmetidir ve PingFederate, daha fazla esneklik sağlayan benzer bir şirket içi kimlik ürünüdür. Bu ürünleri kullanma hakkında daha fazla bilgi için [ping 'un ACS kullanımdan kaldırma kılavuzuna](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) bakın. |

Ping kimliği ve Auth0 ile çalışmamız, tüm Access Control müşterilerinin uygulama ve hizmetleri için Access Control geçiş için gereken iş miktarını en aza indiren bir geçiş yoluna sahip olmasını sağlamaktır.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Etkin kimlik doğrulaması kullanan Web Hizmetleri

Access Control tarafından verilen belirteçlerle güvenliği sağlanmış Web Hizmetleri için, Access Control aşağıdaki özellikleri ve özellikleri sunar:

- Access Control ad alanına bir veya daha fazla *hizmet kimliği* kaydetme yeteneği. Hizmet kimlikleri, belirteç istemek için kullanılabilir.
- Aşağıdaki kimlik bilgileri türlerini kullanarak, belirteç istemek için OAuth WRAP ve OAuth 2,0 taslak 13 protokolleri için destek:
    - Hizmet kimliği için oluşturulmuş basit bir parola
    - Simetrik anahtar veya x509 sertifikası kullanarak imzalı bir SWT
    - Güvenilir bir kimlik sağlayıcısı tarafından verilen SAML belirteci (genellikle bir AD FS örneği)
- Aşağıdaki belirteç biçimleri için destek: JWT, SAML 1,1, SAML 2,0 ve SWT.
- Basit belirteç dönüştürme kuralları.

Access Control içindeki hizmet kimlikleri genellikle sunucudan sunucuya kimlik doğrulaması uygulamak için kullanılır. 

#### <a name="migrate-to-azure-active-directory"></a>Azure Active Directory geçir

Bu tür bir kimlik doğrulama akışına yönelik önerimiz [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/)geçirilecek. Azure AD, Microsoft iş veya okul hesapları için bulut tabanlı kimlik sağlayıcıdır. Azure AD, Office 365, Azure ve çok daha fazlası için kimlik sağlayıcıdır. 

Ayrıca, Azure AD 'yi, OAuth istemci kimlik bilgileri verme 'nin Azure AD uygulamasını kullanarak sunucudan sunucuya kimlik doğrulaması için de kullanabilirsiniz. Aşağıdaki tabloda, sunucu-sunucu kimlik doğrulamasında Access Control özellikleri Azure AD 'de kullanılabilir olanlarla karşılaştırılır.

| Özellik | Access Control desteği | Azure AD desteği |
| ---------- | ----------- | ---------------- |
| Web hizmeti kaydetme | Access Control yönetim portalında bağlı olan taraf oluşturma | Azure portal bir Azure AD Web uygulaması oluşturma |
| Bir istemciyi kaydetme | Access Control yönetim portalında hizmet kimliği oluşturma | Azure portal başka bir Azure AD Web uygulaması oluşturma |
| Kullanılan protokol |-OAuth WRAP Protokolü<br />-OAuth 2,0 taslak 13 istemci kimlik bilgileri verme | OAuth 2.0 istemci kimlik bilgileri verme |
| İstemci kimlik doğrulaması yöntemleri |-Basit parola<br />-İmzalı SWT<br />-Federasyon kimliği sağlayıcısından SAML belirteci |-Basit parola<br />-İmzalı JWT |
| Belirteç biçimleri |-JWT<br />-SAML 1,1<br />-SAML 2,0<br />-SWT<br /> | Yalnızca JWT |
| Belirteç dönüştürmesi |-Özel talepler ekleme<br />-Basit if-then talep verme mantığı | Özel talepler Ekle | 
| Yapılandırma ve yönetim görevlerini otomatikleştirin | Access Control Management Service aracılığıyla desteklenir | Microsoft Graph ve Azure AD Graph API aracılığıyla desteklenir |

Sunucudan sunucuya senaryolar uygulama hakkında rehberlik için aşağıdaki kaynaklara bakın:

- [Azure AD Geliştirici Kılavuzu](https://aka.ms/aaddev) 'Nun hizmetten hizmete bölümü
- [Basit parola istemci kimlik bilgilerini kullanarak Daemon kod örneği](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Sertifika istemci kimlik bilgilerini kullanarak Daemon kod örneği](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Ping kimliğine veya Auth0 geçiş

Bazı durumlarda, Azure AD istemci kimlik bilgilerinin ve OAuth verme uygulamasının, mimarinizdeki Access Control büyük kod değişiklikleri olmadan değiştirmek için yeterli olmadığı fark edebilirsiniz. Bazı yaygın örneklerde şunlar bulunabilir:

- JWTs dışındaki belirteç biçimlerini kullanarak sunucudan sunucuya kimlik doğrulaması.
- Dış kimlik sağlayıcısı tarafından sunulan bir giriş belirtecini kullanarak sunucudan sunucuya kimlik doğrulaması.
- Azure AD 'nin yeniden üretebileceği belirteç dönüştürme kuralları ile sunucudan sunucuya kimlik doğrulaması.

Bu durumlarda, Web uygulamanızı başka bir bulut kimlik doğrulama hizmetine geçirmeyi düşünebilirsiniz. Aşağıdaki seçenekleri araştırmayı öneririz. Aşağıdaki seçeneklerden her biri, Access Control benzer yetenekler sunar:

|     |     |
| --- | --- |
| ![Bu görüntüde Auth0 logosu gösterilmektedir](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) , [Access Control müşterileri için üst düzey geçiş kılavuzu](https://auth0.com/acs)oluşturmuş ve ACS 'nin oluşturduğu neredeyse her özelliği desteklediği esnek bir bulut kimlik hizmetidir. |
| ![Bu görüntüde ping kimlik logosu gösterilmektedir](./media/active-directory-acs-migration/rsz_ping.png) | [Ping kimliği](https://www.pingidentity.com) , ACS 'ye benzer iki çözüm sunar. PingOne, ACS ile aynı özelliklerin birçoğunu destekleyen bir bulut kimlik hizmetidir ve PingFederate, daha fazla esneklik sağlayan benzer bir şirket içi kimlik ürünüdür. Bu ürünleri kullanma hakkında daha fazla bilgi için [ping 'un ACS kullanımdan kaldırma kılavuzuna](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) bakın. |

Ping kimliği ve Auth0 ile çalışmamız, tüm Access Control müşterilerinin uygulama ve hizmetleri için Access Control geçiş için gereken iş miktarını en aza indiren bir geçiş yoluna sahip olmasını sağlamaktır.

#### <a name="passthrough-authentication"></a>Geçiş kimlik doğrulaması

Rastgele belirteç dönüşümünde geçiş kimlik doğrulaması için, ACS 'ye eşdeğer bir Microsoft teknolojisi yoktur. Müşterilerinizin ihtiyacı varsa, Auth0 en yakın çözümü sağlayan bir çözüm olabilir.

## <a name="questions-concerns-and-feedback"></a>Sorular, sorunlar ve geri bildirim

Birçok Access Control müşterinin bu makaleyi okuduktan sonra açık geçiş yolu bulmayabileceğini anladık. Doğru planı belirlemede bazı yardım veya rehberlik gerekebilir. Geçiş senaryolarınızı ve sorularınızı tartışmak isterseniz lütfen bu sayfada bir yorum bırakın.
