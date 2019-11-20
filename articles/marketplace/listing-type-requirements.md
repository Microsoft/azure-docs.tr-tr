---
title: Tür listeleme gereksinimleri | Mavisi
description: Bu makalede, uygulamaları Azure Marketi 'Nde nasıl yayımlayacağınızı anlamaya çalışan uygunluk ölçütleri ve yayımlama gereksinimleri iş ortakları açıklanır.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: b9a2e8b37f746eb86b2e0fe6a7304cd888a293e1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823026"
---
# <a name="requirements-by-listing-type"></a>Liste türüne göre gereksinimler  
Teknik ve pazarlama içeriği gereksinimleri storefront, teklif türü ve liste türüne göre farklılık gösterir. Uyumluluğunuzu doğrulamak için aşağıdaki belirtimleri gözden geçirin.  
1. Storefront gereksinimleri:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Market](#storefront-requirements-azure-marketplace)  
2. Liste türü ve teklif türü gereksinimleri:  
    *   Türleri ve teklif türlerini listeleme hakkında daha fazla bilgi için, [docs.Microsoft.com/Azure/Marketplace/determine-Your-Listing-Type](./determine-your-listing-type.md)adresinde bulunan çözümünüz Için liste türünü belirleme sayfasına gidin.  

## <a name="storefront-requirements-appsource"></a>Storefront gereksinimleri: AppSource  
Aşağıdaki tabloda AppSource 'ta yayımlama için önkoşul gereksinimleri açıklanmaktadır.  

| Gereksinim | Ayrıntılar | Gerekli ya da Önerilen |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Uygulamanız izin etkin olan Azure Active Directory Federasyon çoklu oturum açma (Azure AD Federasyon SSO) izni vermelidir.<ul> <li>Azure AD Federasyon SSO 'yu etkinleştirme hakkında daha fazla bilgi için, [docs.Microsoft.com/Azure/Active-Directory/Active-Directory-SaaS-Custom-Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)adresinde bulunan Azure Active Directory Uygulama Galerisi sayfasında olmayan uygulamalarda çoklu oturum açmayı yapılandırma sayfasını ziyaret edin.</li> </ul> | Gerekli |   
| ***Microsoft Bulut hizmetleriyle tümleştirme*** | Uygulamanız, Microsoft Power BI, Cortana Intelligence veya Microsoft Azure hizmetleri gibi diğer Microsoft Bulut hizmetlerle tümleşmelidir.<ul> <li>Microsoft Bulut bir hizmet örneği Nesnelerin İnterneti.</li> </ul> | Önerilen |  
| ***Grubu*** | Uygulamanız iş kolu kullanıcıları ve iş sahipleri için olmalıdır. | Gerekli | 
| ***İş için hizmet olarak yazılım (SaaS) uygulaması*** | Uygulamanızın aşağıdaki gereksinimleri karşılaması gerekir.<ul> <li>İş kolu SaaS uygulaması</li> <li>İş-işlem odaklı</li> <li>İş müşterilerine hedeflenmiş</li> <li>Kullanıcıların Kullanıcı adı ve parola gibi oturum açmasını sağlamak için iş kimlik bilgilerini kullanmasını sağlayın</li> </ul> | Gerekli |  
| ***Ücretsiz deneme süresi ve deneme deneyimi*** | Uygulamanız, bir müşterinin uygulamanızı sınırlı bir süre için ücretsiz olarak kullanabilmesi için aşağıdaki seçeneklerden birini içermelidir.<ul> <li>Müşteriler AppSource içinde uygulamanızın bir deneme sürümünü başlatabilmeleri için bir `try` yöntemi sağlayın</li> <li>AppSource 'ta bir `request trial` seçeneği belirtin, böylelikle müşteriler uygulamanızın deneme sürümünü isteyebilir</li> </ul>Sağladığınız ücretsiz deneme, müşteriye ek bir ücret ödemeden uygulamanızı denemek için önceden ayarlanmış bir süre süresi sunmalıdır. | Gerekli |  
| ***Kolayca yapılandırılabilir, kullanıma hazırlamış çözüm*** | Uygulamanız kolay ve hızlı bir şekilde yapılandırılması ve bir özelleştirme gerekmeden ayarlamanız gerekir. | Gerekli |  
| ***Müşteri adayı yönetimi*** | Storefront adresinden müşteri adaylarını almadan önce CRM 'nizin, müşteri adayı verilerini kabul etmesine izin vermek için etkinleştirin.<ul> <li>CRMs örnekleri Marketo, Microsoft Dynamics veya Salesforce</li> </ul> | Gerekli |  
| ***Gizlilik ilkesi ve kullanım koşulları*** | Uygulamanızın, genel bir URL kullanarak Gizlilik ilkesi sayfanız için bir bağlantı sağlaması gerekir. Metin olarak yayımlama sırasında kullanım koşullarınızın sağlanması gerekir. | Gerekli |  
| ***Destek*** | Uygulamanızın, genel bir URL kullanarak müşteri destek sayfanıza bir bağlantı sağlaması gerekir. Uygulamanız bir deneme sürümü ise, deneme süresi boyunca hiçbir ek ücret ödemeden desteketmeniz gerekir. | Gerekli |  

## <a name="storefront-requirements-azure-marketplace"></a>Storefront gereksinimleri: Azure Marketi  
Aşağıdakiler Azure Marketi 'nde türleri listelemek için önkoşul gereksinimleridir.  

| Gereksinim | Ayrıntılar | Liste türü |  
|:--- |:--- |:--- |  
| ***Katılım İlkeleri*** | Uygulamanız Azure Marketi Katılım ilkelerini izlemelidir.<ul> <li>Katılım ilkeleri hakkında daha fazla bilgi için, [Azure.Microsoft.com/support/legal/Marketplace/Participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies)adresinde bulunan Azure Marketi Katılım ilkeleri sayfasını ziyaret edin.</li></ul> | list<br />Transact<br />trial |  
| ***Microsoft ile tümleştirme*** | Teklifinizin işlem, ağ veya depolama gibi Microsoft Azure hizmet türlerini kullanması veya genişletmesi gerekir. Teklifiniz, veritabanları, güvenlik veya ağ gibi mevcut bir Azure Marketi kategorisine göre hizalanmalıdır.<ul> <li>Market teklifleri hakkında daha fazla bilgi için, [azuremarketplace.Microsoft.com/Marketplace/Apps](https://azuremarketplace.microsoft.com/marketplace/apps)adresinde bulunan Market uygulamaları sayfasını ziyaret edin.</li> </ul> | list<br />Transact<br />trial |  
| ***Grubu*** | Teklifinizin BT uzmanları, bulut geliştiricileri veya diğer teknik müşteri rolleri için olması gerekir. | list<br />Transact<br />trial |  
| ***Müşteri adayı yönetimi*** | Storefront 'ten müşteri adaylarını almadan önce CRM 'nizi (Marketo, Microsoft Dynamics veya Salesforce), müşteri adayı verilerini kabul edecek şekilde etkinleştirin. | list<br />Transact<br />trial |  
| ***Gizlilik ilkesi ve kullanım koşulları*** | Uygulamanızın, genel bir URL kullanarak Gizlilik ilkesi sayfanız için bir bağlantı sağlaması gerekir. Metin olarak yayımlama sırasında kullanım koşullarınızın sağlanması gerekir. | list<br />Transact<br />trial |  
| ***Destek*** | Teklifinizin, genel bir URL kullanarak müşteri destek sayfanıza bir bağlantı sağlaması gerekir. Teklifiniz bir deneme sürümü ise, deneme süresi boyunca hiçbir ek ücret ödemeden desteketmeniz gerekir. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Transact olmayan listeler  
Bu bölümde, Transact listeleme türünü kullanmayan tüm teklif türleri açıklanmaktadır. 

### <a name="list"></a>Liste  
Liste listesi türü, Market 'te bulunan storets 'ler üzerinde aşağıdaki teklif türlerini içerir.  

| Teklif türü | Vitrin | Ayrıntılar |  
|:---        |:---        |:---     |  
| Danışmanlık Hizmetleri | AppSource | Gereksinimler: AppSource: List: danışmanlık hizmetleri |  
| Danışmanlık Hizmetleri | Azure Market | Gereksinimler: Azure Marketi: liste: danışmanlık hizmetleri |  
| Benimle iletişim kurun | AppSource | [](#) |  
| Benimle iletişim kurun | Azure Market | Gereksinimler: AppSource: List: benimle Iletişim kurun |  

#### <a name="requirements-appsource-list-consulting-service"></a>Gereksinimler: AppSource: List: danışmanlık hizmeti  

| Gereksinimler | Ayrıntılar |  
|:--- |:--- |  
| Hizmet teklifi özellikleri | Danışmanlık hizmetinizin aşağıdaki ölçütleri karşılaması gerekir.<ul> <li>Sabit kapsam, sabit süreli, sabit fiyatlı (veya ücretsiz) bir katılım sunun.</li> <li>Öncelikli olarak ön satış için yönelim.</li> <li>Tek bir müşteriyle sınırlayın.</li> <li>Sitede gerçekleştirin.</li> </ul> |  
| Danışmanlık Hizmetleri için iş ortağı gereksinimleri | Hizmetiniz için ilgili alandaki ölçütleri karşılamanız gerekir.<table><tr><th>Çözüm alanı</th><th>Ölçütler</th></tr><tr><td>Müşteri Etkileşimi için Dynamics 365</td><td>Gümüş veya altın bulut müşteri Ilişkisi yönetimi uzmanlığına sahip olma.</td></tr><tr><td>Finans ve Işlemler için Dynamics 365, Enterprise Edition</td><td>$25.000 veya daha fazla 12 ayda, bulut işlemlerinizin sunduğu gümüş veya altın kurumsal kaynak planlama uzmanlığına ve gelirine sahip olma.</td></tr><tr><td>Finans ve Işlemler için Dynamics 365, Iş sürümü</td><td>Bir veya daha fazla müşteri için Cloud Services sağlayıcı (CSP) veya dijital kayıt ortağı (DPOR) olarak görev yapar.</td></tr><tr><td>Power BI</td><td>Çözüm Iş ortağı ölçütlerini karşılayın.</td></tr><tr><td>PowerApps</td><td>Iş ortağı gösterimi çözümüne sahip olun.</td></tr></table><ul> <li>Müşteri ilişkileri yönetimi hakkında daha fazla bilgi için, [partner.Microsoft.com/membership/Cloud-Customer-Relationship-Management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency)adresinde bulunan bulut müşterisi ilişki yönetimi sayfasını ziyaret edin.</li> <li>Kaynak planlama hakkında daha fazla bilgi için, [partner.Microsoft.com/membership/Enterprise-Resource-Planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency)adresinde bulunan kurumsal kaynak planlama sayfasını ziyaret edin.</li> <li>CSP hakkında daha fazla bilgi için [partner.Microsoft.com/Cloud-Solution-Provider](https://partner.microsoft.com/cloud-solution-provider)adresinde bulunan Cloud Services sağlayıcı sayfasını ziyaret edin.</li> <li>DPOR hakkında daha fazla bilgi için, [partner.Microsoft.com/membership/Digital-Partner-of-Record](https://partner.microsoft.com/membership/digital-partner-of-record)adresinde bulunan kayıt ve Iş ortağı Ilişkilendirmesi sayfasının dijital iş ortağını ziyaret edin.</li> <li>Çözüm iş ortağı ölçütleri hakkında daha fazla bilgi için, [www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.PDF](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf)adresinde bulunan çözüm ortağına genel bakış ve teşvikleri belgesini ziyaret edin.</li> <li>İş ortağı gösterimi hakkında daha fazla bilgi için, [PowerApps.Microsoft.com/Partner-Showcase](https://powerapps.microsoft.com/partner-showcase)adresinde bulunan Iş ortağı gösterimi sayfasını ziyaret edin.</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Gereksinimler: Azure Marketi: liste: danışmanlık hizmeti  

| Gereksinimler | Ayrıntılar |  
|:--- |:--- |  
| Hizmet teklifi özellikleri | Danışmanlık hizmetinizin aşağıdaki ölçütleri karşılaması gerekir.<ul> <li>Sabit kapsam, sabit süreli, sabit fiyatlı (veya ücretsiz) bir katılım sunun.</li> <li>Öncelikli olarak ön satış için yönelim.</li> <li>Tek bir müşteriyle sınırlayın.</li> <li>Sitede gerçekleştirin.</li> </ul> |  
| Danışmanlık Hizmetleri için iş ortağı gereksinimleri | Hizmetiniz için ilgili alanda aşağıdaki Uzmanlıklar üzerinde gümüş veya altın birine sahip olmanız gerekir. <table><tr><th>Çözüm alanı</th><th>Uzmanlığı</th></tr><td>Bulut platformu ve altyapısı</td><td>Bulut platformu<br />Veri Merkezi</td><tr><td>Uygulama geliştirme ve ISV</td><td>Uygulama Geliştirme<br />Uygulama Tümleştirme<br />DevOps</td></tr><tr><td>Veri Yönetimi ve analiz</td><td>Veri Analizi<br />Veri Platformu</td></tr></table><ul> <li>Uzmanlıklar hakkında daha fazla bilgi için, [partner.Microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies)adresinde bulunan Microsoft iş ortağı ağı sayfasındaki Uzmanlıklar ' ı ziyaret edin.</li> <li>Listeleme hakkında daha fazla bilgi için, [docs.Microsoft.com/Azure/Marketplace/Consulting-Services](https://docs.microsoft.com/azure/marketplace/consulting-services)adresinde bulunan Azure Marketi Danışmanlık Hizmetleri sayfasını ziyaret edin.</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Deneme  

| Teklif türü | Vitrin | Ayrıntılar |  
|:---        |:---        |:---     |  
| Ücretsiz/SaaS denemesi | AppSource | Liste türü gereksinimleri: deneme |  
| Ücretsiz/SaaS denemesi | Azure Market | Gereksinimler: Azure Marketi: deneme: ücretsiz deneme/SaaS deneme sürümü |  
| Etkileşimli tanıtım | AppSource | Liste türü gereksinimleri: deneme |  
| Etkileşimli tanıtım | Azure Market | [Gereksinimler: Azure Marketi: deneme: Etkileşimli Tanıtım](#requirements-azure-marketplace-trial-interactive-demo) |  
| Test sürüşü | AppSource | Liste türü gereksinimleri: deneme |  
| Test sürüşü | Azure Market | [Gereksinimler: Azure Marketi: deneme sürümü: test sürücüsü](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Gereksinimler: Azure Marketi: deneme  

| Gereksinim | Ayrıntılar |  
|:--- |:--- |  
| Ücretsiz deneme süresi ve deneme deneyimi | Müşteriniz, uygulamanızı sınırlı bir süre için ücretsiz olarak kullanabilir.<br /><br />Müşterinizin veya uygulamanızın lisans ya da abonelik ücretleri için ödeme yapması gerekli değildir. Müşterinizin, temel alınan Microsoft birinci taraf ürün veya hizmeti için ödeme yapması gerekmez. Tüm deneme seçenekleri Azure aboneliğinize dağıtılır. Maliyet iyileştirmesi ve yönetimi için tek denetim denemesine sahipsiniz.<br /><br />Ücretsiz bir deneme, etkileşimli tanıtım veya test sürücüsü seçebilirsiniz. Ne tercih ettiğinize bakılmaksızın ücretsiz deneme süreniz, müşteriyi ek bir ücret ödemeden uygulamayı denemek için önceden ayarlanmış bir süre için sunmalıdır.<ul> <li>Test sürücüsü oluşturma işlemine başlamak için [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)adresine bir e-posta gönderin.</li> </ul>Note: Azure Market SaaS deneme deneyimleri, müşterilerin oturum açmak için iş kimlik bilgilerini kullanmasına izin vermelidir.<ul> <li>Daha fazla bilgi için, [docs.Microsoft.com/Azure/Active-Directory/develop/Active-Directory-devhowto-appsource-Certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences)adresinde bulunan appsource deneme deneyimleri bölümünü ziyaret edin.</li> </ul> |  
| Kolayca yapılandırılabilir, kullanıma hazırlamış çözüm | Uygulamanızın yapılandırılması ve ayarlanması kolay ve hızlı olmalıdır. |  
| Kullanılabilirlik/çalışma süresi | SaaS uygulamanızın veya platformunuzun süresi en az% 99,9 olmalıdır. |  
| Azure Active Directory | Teklifinizin Azure Active Directory (Azure AD) Federal çoklu oturum açma (SSO) (Azure AD Federasyon SSO) onayı etkinleştirilmiş olarak izin vermelidir. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Gereksinimler: Azure Marketi: deneme: ücretsiz deneme/SaaS deneme sürümü  

| Avantaj | Gereksinim |  
|:--- |:--- |  
| Müşterinin ücretli kullanımı dönüştürmek üzere otomatikleştirilmiş bir yöntemle satın almadan önce ürününüzü denemesini sağlar. Ayrıca, Microsoft satış ekipleriyle müşteri ve Birleşik katılım için kavram provalarını da sunar. | Çözümünüz bir sanal makine veya çözüm şablonudur.<br /><br />Çözümünüz bir SaaS sunumudur ve çok kiracılı bir SaaS ürünü sunuyoruz.<br /><br />Bir müşteriyi hızla çalışır duruma getirmek için ilk çalıştırma deneyiminizin olması gerekir.<br /><br />Tek bir kiracınız var, ancak müşterileri Konuk Kullanıcı olarak ekliyoruz. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Gereksinimler: Azure Marketi: deneme: Etkileşimli Tanıtım  

| Avantaj | Gereksinim |  
|:--- |:--- |  
| Müşterilerinizin, kurulum karmaşıklığı olmadan çözümünüzü eylemde görmesini sağlar. | Çözümünüz, deneme süresi içinde elde etmek zor olabilecek karmaşık ayarlar gerektirir. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Gereksinimler: Azure Marketi: deneme sürümü: test sürücüsü  

| Avantaj | Gereksinim |  
|:--- |:--- |  
| Müşterinin satın alınmadan önce ürününüzü denemesini sağlar.<br /><br />Önceden yapılandırılmış ayarları kullanarak çözümünüz için Kılavuzlu bir deneyim sağlar.<br /><br />Test sürücüsü kullanılırken aşağıdakiler ek avantajlardır.<ul> <li>Market 'te Kullanıcı aramalarının %27 ' i, yalnızca test sürücüleriyle ilgili teklifleri göstermek için kullanıcılara göre iyileştiriliyor.</li> <li>Test sürücüleriyle birlikte sunulan teklifler, "%38 daha fazla müşteri adayı" üretir.</li> <li>Market 'teki yeni müşteri alma işlemlerine ait %36, test sürücüsü geçen müşterilerden gelir.</li> <li>Sınama sürücüleri Microsoft alan satıcılarını etkinleştirerek ürününüzü ortak satış çabalarıyla daha iyi anlamanıza yardımcı olur.</li> </ul> | Çözümünüz, tek bir kiracıya sahip bir sanal makine, çözüm şablonu veya SaaS uygulamasıdır ya da sağlanması karmaşıktır. <br /><br />Deneme sürümünüzü ücretli bir teklifle dönüştürmek için bir yönteminiz yok. |  

---

## <a name="transact-specific-listings"></a>Transact 'a özgü listeler

### <a name="transact"></a>İşlem  

| Teklif türü | Vitrin | Ayrıntılar |   
|:---        |:---        | :--- |  
| Azure uygulamaları: yönetilen uygulama | Azure Market | Gereksinimler: Azure Marketi: Transact: Azure uygulamaları: yönetilen uygulama |  
| Azure uygulamaları: çözüm şablonu | Azure Market | Gereksinimler: Azure Marketi: Transact: Azure uygulamaları: çözüm şablonu |  
| Kapsayıcılar | Azure Market | [Gereksinimler: Azure Marketi: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| SaaS uygulaması  | Azure Market | [Gereksinimler: Azure Marketi: Transact: SaaS uygulaması](#requirements-azure-marketplace-transact-saas-app) |  
| Sanal makine | Azure Market | [Gereksinimler: Azure Marketi: Transact: sanal makine](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Gereksinimler: Azure Marketi: Transact: Container  

| Gereksinim | Ayrıntılar |  
|:--- |:--- |  
| Faturalandırma ve ölçüm | Ücretsiz veya KLG faturalandırma modelini destekler. |  
| Docker tabanlı görüntü | Kapsayıcı resminiz Docker görüntü biçimine dayalı olmalıdır ve Azure Container Registry 'lerden çekilmelidir. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Gereksinimler: Azure Marketi: Transact: SaaS uygulaması  

| Gereksinim | Ayrıntılar |  
|:--- |:--- |  
| Faturalandırma ve ölçüm | Teklifiniz aylık sabit bir hızda fiyatlandırılır. Kullanım tabanlı fiyatlandırma ve kullanım tabanlı *gerçek zamanlı* seçenekler şu anda desteklenmiyor. |  
| İptal | Teklifiniz herhangi bir zamanda müşteri tarafından iptal edilir. |  
| İşlem giriş sayfası | Azure ortak markalı işlem giriş sayfasını barındırın. Giriş sayfanız, müşterilerinizin SaaS hizmet hesabınızı oluşturmalarına ve yönetmesine olanak sağlar. |  
| SaaS Abonelik API 'SI | Bir kullanıcı hesabı ve hizmet planı oluşturmak, güncelleştirmek ve silmek için SaaS aboneliğiyle etkileşim kuran bir hizmet sağlayın. Tüm kritik API değişikliklerinin 24 saat içinde desteklenmesi gerekir. Kritik olmayan tüm API değişiklikleri düzenli olarak güncelleştirilir. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Gereksinimler: Azure Marketi: Transact: sanal makine  

| Gereksinim | Ayrıntılar |  
|:--- |:--- | 
| Faturalandırma ve ölçüm | SANAL makinenizin KLG veya Kullandıkça Öde aylık faturalandırmasını desteklemesi gerekir. |  
| Azure ile uyumlu sanal sabit disk (VHD) | VM 'Ler Windows veya Linux üzerinde oluşturulmalıdır.<ul> <li>Linux VHD oluşturma hakkında daha fazla bilgi için bkz. [Azure 'da desteklenen Linux dağıtımları](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Windows VHD oluşturma hakkında daha fazla bilgi için bkz. [Azure ile uyumlu bır VHD oluşturma](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

## <a name="next-steps"></a>Sonraki adımlar
*   [Azure Marketi ve AppSource yayımcı Kılavuzu](./marketplace-publishers-guide.md) sayfasını ziyaret edin.  

