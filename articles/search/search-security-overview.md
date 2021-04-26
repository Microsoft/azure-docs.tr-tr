---
title: Güvenliğe genel bakış
titleSuffix: Azure Cognitive Search
description: Uç noktaları, içerik ve işlemleri korumak için Azure Bilişsel Arama 'daki güvenlik özellikleri hakkında bilgi edinin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097645"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Azure Bilişsel Arama için güvenliğe genel bakış

Bu makalede, Azure Bilişsel Arama içeriğini ve işlemlerini koruyan güvenlik özellikleri açıklanmaktadır.

Bir arama hizmetine yapılan gelen istekler için, arama hizmeti uç noktasını koruyan güvenlik önlemlerinin bir ilerlemesi vardır: istekteki API anahtarlarından, güvenlik duvarındaki gelen kurallara, genel İnternet 'ten hizmetinizi tamamen koruyan özel uç noktalara kadar bir ilerleme vardır.

Diğer hizmetlere yapılan giden istekler için, önceden baskın istek, dış kaynaklardan içerik okuyan Dizin oluşturucular tarafından yapılır. Bağlantı dizesinde kimlik bilgileri sağlayabilirsiniz. Ya da Azure depolama, Azure SQL, Cosmos DB veya diğer Azure veri kaynaklarından verilere erişirken bir güvenilen hizmeti aramaya yönelik yönetilen bir kimlik ayarlayabilirsiniz. Yönetilen kimlik, bağlantıda kimlik bilgilerinin veya erişim anahtarlarının yerini alır. Bu yetenek hakkında daha fazla bilgi için bkz. [yönetilen kimlik kullanarak bir veri kaynağına bağlanma](search-howto-managed-identities-data-sources.md).

Dış hizmetlere yazma işlemleri çok az: bir arama hizmeti günlük dosyalarına yazar ve bilgi depoları oluştururken, kalıcı önbelleğe alınmış bir zenginleştirme ve hata ayıklama oturumlarını kalıcı hale getiren Azure depolama 'ya yazılır. Bilişsel hizmetler gibi diğer hizmetten hizmete çağrılar, iç ağda yapılır.

Güvenlik mimarisine ve her özellik kategorisine genel bir bakış için bu hızla ilerleyebileceğiniz videoyu izleyin.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Ağ güvenliği

<a name="service-access-and-authentication"></a>

Gelen güvenlik özellikleri, güvenlik ve karmaşıklık düzeylerini artırarak arama hizmeti uç noktasını korur. İlk olarak, tüm istekler kimliği doğrulanmış erişim için bir API anahtarı gerektirir. İkinci olarak, belirli IP adreslerine erişimi sınırlayan güvenlik duvarı kuralları belirleyebilirsiniz. Gelişmiş koruma için, üçüncü bir seçenek Azure özel bağlantısının, hizmet uç noktanızı tüm internet trafiğinden korunmasını sağlar.

### <a name="public-access-using-api-keys"></a>API anahtarları kullanarak genel erişim

Varsayılan olarak, bir arama hizmetine yönetim için anahtar tabanlı kimlik doğrulaması kullanılarak veya arama hizmeti uç noktasına sorgu erişimi kullanılarak genel bulut üzerinden erişilir. Geçerli bir anahtarın gönderilmesi, isteğin güvenilir bir varlıktan kaynaklandığı kanıtları kabul edilir. Anahtar tabanlı kimlik doğrulaması, sonraki bölümde ele alınmıştır.

### <a name="configure-ip-firewalls"></a>IP güvenlik duvarlarını yapılandırma

Arama hizmetinize erişimi daha fazla denetlemek için, belirli IP adreslerine veya bir IP adresi aralığına erişime izin veren gelen güvenlik duvarı kuralları oluşturabilirsiniz. Tüm istemci bağlantıları izin verilen bir IP adresi üzerinden yapılmalıdır veya bağlantı reddedildi.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="IP kısıtlı erişimi için örnek mimari diyagramı":::

[Gelen erişimi yapılandırmak](service-configure-firewall.md)için portalı kullanabilirsiniz.

Alternatif olarak, yönetim REST API 'Lerini de kullanabilirsiniz. API sürüm 2020-03-13 ' den başlayarak, [ıprule](/rest/api/searchmanagement/services/createorupdate#iprule) parametresi ile, arama hizmetinize erişim vermek istediğiniz tek tek veya BIR aralıktaki IP adreslerini tanımlayarak hizmetinize erişimi kısıtlayabilirsiniz.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Özel bir uç nokta (Internet trafiği olmadan) üzerinden ağ yalıtımı

Azure Bilişsel Arama özel bir [uç noktası](../private-link/private-endpoint-overview.md) oluşturabilirsiniz. bir [sanal ağ](../virtual-network/virtual-networks-overview.md) üzerindeki istemcinin bir [özel bağlantı](../private-link/private-link-overview.md)üzerinden bir arama dizinindeki verilere güvenli bir şekilde erişmesini sağlar.

Özel uç nokta, arama hizmetinize bağlantı için sanal ağ adres alanından bir IP adresi kullanır. İstemci ile arama hizmeti arasındaki ağ trafiği, sanal ağın ve Microsoft omurga ağındaki özel bir bağlantının üzerinde geçiş yaparken, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır. VNET, kaynaklar arasında, şirket içi ağınızdan ve Internet 'Ten güvenli iletişim sağlar.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Özel uç nokta erişimi için örnek mimari diyagramı":::

Bu çözüm en güvenli hale geldiğinden, ek hizmetlerin kullanılması, ek bir maliyettir. bu nedenle, ' de kullanmadan önce avantajların net bir şekilde anlaşıldığından emin olun. veya maliyetler hakkında daha fazla bilgi için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/private-link/). Bu bileşenlerin birlikte nasıl çalıştığı hakkında daha fazla bilgi için bu makalenin en üstündeki videoyu izleyin. Özel uç nokta seçeneğinin kapsamı, video 5:48 ' de başlar. Uç noktanın nasıl ayarlanacağı hakkında yönergeler için bkz. [Azure bilişsel arama Için özel uç nokta oluşturma](service-create-private-endpoint.md).

## <a name="authentication"></a>Kimlik Doğrulaması

Arama hizmetine gelen istekler için, kimlik doğrulaması [zorunlu BIR API anahtarı](search-security-api-keys.md) (rastgele oluşturulan sayılardan ve mektuplardan oluşan bir dize) aracılığıyla isteği güvenilir bir kaynaktan olduğunu kanıtlar. Bilişsel Arama, şu anda gelen istekler için Azure Active Directory kimlik doğrulamasını desteklememektedir.

Bir Dizin Oluşturucu tarafından yapılan giden istekler, dış hizmet tarafından kimlik doğrulamaya tabidir. Bilişsel Arama Dizin Oluşturucu alt hizmeti, Azure 'da, yönetilen bir kimlik kullanılarak diğer hizmetlere bağlanarak güvenilen bir hizmet verebilir. Daha fazla bilgi için bkz. [yönetilen kimlik kullanarak bir veri kaynağına Dizin Oluşturucu bağlantısı](search-howto-managed-identities-data-sources.md)kurma.

## <a name="authorization"></a>Yetkilendirme

Bilişsel Arama, içerik yönetimi ve hizmet yönetimi için farklı yetkilendirme modelleri sağlar. 

### <a name="authorization-for-content-management"></a>İçerik yönetimi için yetkilendirme

İçerik yetkilendirmesi ve içerikle ilgili işlemler, istekte sağlanmış [API anahtarı](search-security-api-keys.md) aracılığıyla basıp basmadığını değerlendirerek olarak yazma erişimğidir. API anahtarı bir kimlik doğrulama mekanizmasıdır, ancak API anahtarının türüne göre erişim yetkisi verir.

+ Yönetici anahtarı (arama hizmetinde oluşturma-okuma-güncelleştirme-silme işlemleri için okuma-yazma erişimine izin verir), hizmet sağlandığında oluşturulur

+ Sorgu anahtarı (bir dizinin belge koleksiyonuna salt okuma erişimi sağlar), gerekli olarak oluşturulur ve sorgu veren istemci uygulamalar için tasarlanmıştır

Uygulama kodu ' nda, içerik ve seçeneklere erişime izin vermek için uç noktayı ve bir API anahtarını belirtirsiniz. Bir uç nokta hizmetin kendisi, dizinler koleksiyonu, belirli bir dizin, belge koleksiyonu veya belirli bir belge olabilir. Birlikte zincirleme yaparken, uç nokta, işlem (örneğin, bir oluşturma veya güncelleştirme isteği) ve izin düzeyi (anahtara göre tam veya salt okuma hakları), içerik ve işlemleri koruyan güvenlik formülünü oluşturur.

### <a name="controlling-access-to-indexes"></a>Dizinlere erişimi denetleme

Azure Bilişsel Arama, tek bir dizin, güvenli kılınabilir bir nesne değildir. Bunun yerine, bir dizin erişimi, hizmet katmanında (sağladığınız API anahtarını temel alan okuma veya yazma erişimi) bir işlemin içeriğiyle birlikte belirlenir.

Salt okuma erişimi için, bir [sorgu anahtarı](search-security-rbac.md)kullanarak bağlanmak üzere sorgu istekleri oluşturabilir ve uygulamanız tarafından kullanılan belirli dizini ekleyebilirsiniz. Bir sorgu isteğinde, Dizin bağlama veya birden çok dizine aynı anda erişme kavramı yoktur, bu nedenle tüm istekler tanım ile tek bir dizin hedefleyin. Bu nedenle, sorgu isteğinin kendisi (bir anahtar ve tek bir hedef dizin), güvenlik sınırını tanımlar.

Dizinlere yönetici ve geliştirici erişimi farklılaştırılabilir: her ikisi de hizmet tarafından yönetilen nesneleri oluşturmak, silmek ve güncelleştirmek için yazma erişimine ihtiyaç duyar. Hizmetinize [yönetici anahtarına](search-security-rbac.md) sahip olan herkes aynı hizmette bulunan herhangi bir dizini okuyabilir, değiştirebilir veya silebilir. Dizinlerin yanlışlıkla veya kötü amaçlı olarak silinmesine karşı koruma için, kod varlıkları için şirket içi kaynak denetiminiz, istenmeyen bir dizin silme veya değiştirme işlemi için bir çözüm oluşturur. Azure Bilişsel Arama, kullanılabilirliği sağlamak için küme içinde yük devretmeye sahiptir, ancak dizinleri oluşturmak veya yüklemek için kullanılan özel kodunuzu depolamaz veya yürütmez.

Dizin düzeyinde güvenlik sınırları gerektiren çok kiracılı çözümler için, bu tür çözümler genellikle müşterilerin Dizin yalıtımını işlemek için kullanacağı bir orta katman içerir. Çok kiracılı kullanım durumu hakkında daha fazla bilgi için bkz. [çok kiracılı SaaS uygulamaları ve Azure bilişsel arama Için tasarım desenleri](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Belgelere erişimi denetleme

Arama sonuçları üzerinde ayrıntılı, Kullanıcı başına denetim istiyorsanız, sorgularda güvenlik filtreleri oluşturabilir, bu, belirli bir güvenlik kimliğiyle ilişkili belgeleri döndürür. 

Kavramsal olarak "satır düzeyi güvenlik" ile eşdeğer, dizin içindeki içeriğe yönelik yetkilendirme, önceden tanımlanmış roller veya Azure Active Directory içindeki varlıklarla eşlenen rol atamaları kullanılarak yerel olarak desteklenmez. Cosmos DB gibi dış sistemlerdeki veriler üzerinde bulunan tüm Kullanıcı izinleri, Bilişsel Arama tarafından dizinlendiği gibi bu verilerle aktarılmaz.

"Satır düzeyi güvenlik" gerektiren çözümler için geçici çözümler, bir güvenlik grubunu veya Kullanıcı kimliğini temsil eden veri kaynağında bir alan oluşturmayı ve sonra, belge ve içerik arama sonuçlarını kimliklere göre seçmeli olarak kırpmak için Bilişsel Arama filtreler kullanmayı içerir. Aşağıdaki tabloda yetkisiz içeriğin arama sonuçlarını kırpma için iki yaklaşım açıklanmaktadır.

| Yaklaşım | Description |
|----------|-------------|
|[Kimlik filtrelerine göre güvenlik kırpması](search-security-trimming-for-azure-search.md)  | Kullanıcı kimliği erişim denetimi uygulamak için temel iş akışını belgeler. Bir dizine güvenlik tanımlayıcıları eklenmesini ve sonra yasaklanmış içeriğin sonuçlarını kırpmak için bu alana karşı filtrelemeyi açıklar. |
|[Azure Active Directory kimliklerine göre güvenlik kırpması](search-security-trimming-for-azure-search-with-aad.md)  | Bu makalede, Azure bulut platformunda [ücretsiz hizmetlerden](https://azure.microsoft.com/free/) biri olan Azure Active Directory (Azure AD) ' den kimlik alma adımları sağlayan önceki makalede genişletilir. |

### <a name="authorization-for-service-management"></a>Hizmet yönetimi için yetkilendirme

Hizmet yönetimi işlemlerine [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/overview.md)aracılığıyla yetki verilir. Azure RBAC, Azure kaynaklarını sağlamaya yönelik [Azure Resource Manager](../azure-resource-manager/management/overview.md) yerleşik bir yetkilendirme sistemidir. 

Azure Bilişsel Arama 'de, hizmeti oluşturmak veya silmek, API anahtarlarını yönetmek ve hizmeti ölçeklendirmek için Kaynak Yöneticisi kullanılır. Bu nedenle, Azure rol atamaları, [Portal](search-manage.md), [POWERSHELL](search-manage-powershell.md)veya [Yönetim REST API 'lerini](/rest/api/searchmanagement/search-howto-management-rest-api)kullanıp kullanmadıklarından bağımsız olarak kimlerin bu görevleri gerçekleştirebileceğini tespit eder.

Arama hizmeti yönetimi için [üç temel rol](search-security-rbac.md#management-tasks-by-role) tanımlanmıştır. Rol atamaları, desteklenen herhangi bir metodolojide (portal, PowerShell, vb.) kullanılarak yapılabilir ve hizmet genelinde kabul edilebilir. Sahip ve katkıda bulunan rolleri çeşitli yönetim işlevleri gerçekleştirebilir. Okuyucu rolünü yalnızca önemli bilgileri görüntüleyen kullanıcılara atayabilirsiniz.

> [!Note]
> Azure genelinde mekanizmalar kullanarak, yönetici haklarına sahip kullanıcılar tarafından, arama hizmetinizin yanlışlıkla veya izinsiz olarak silinmesini engellemek için bir aboneliği veya kaynağı kilitleyebilir. Daha fazla bilgi için bkz. [beklenmeyen silme işlemini engellemek için kaynakları kilitleme](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Veri koruma

Depolama katmanında, dizinler, eş anlamlı eşlemeler ve Dizin oluşturucular, veri kaynakları ve becerileri tanımları dahil olmak üzere diske kaydedilen tüm hizmet tarafından yönetilen içerikler için veri şifreleme yerleşiktir. İsteğe bağlı olarak, dizine alınmış içeriğin ek şifrelenmesi için müşteri tarafından yönetilen anahtarlar (CMK) ekleyebilirsiniz. 1 2020 Ağustos 'Tan sonra oluşturulan hizmetler için CMK şifrelemesi, dizini oluşturulmuş içeriğin tam "çift şifrelemesi" için geçici disklerde verileri genişletir.

### <a name="data-in-transit"></a>Aktarım durumundaki veriler

Azure Bilişsel Arama 'de, şifreleme bağlantılarla ve iletimlerle başlar ve diskte depolanan içeriğe genişletir. Genel internet üzerindeki arama hizmetleri için Azure Bilişsel Arama, HTTPS bağlantı noktası 443 ' i dinler. Tüm istemciden hizmete bağlantılar TLS 1,2 şifrelemesini kullanır. Önceki sürümler (1,0 veya 1,1) desteklenmez.

### <a name="encrypted-data-at-rest"></a>Bekleyen şifrelenmiş veriler

Arama hizmeti tarafından dahili olarak işlenen veriler için aşağıdaki tabloda [veri şifreleme modelleri](../security/fundamentals/encryption-models.md)açıklanmaktadır. Bilgi deposu, artımlı zenginleştirme ve Dizin Oluşturucu tabanlı dizin oluşturma, diğer Azure hizmetlerinde veri yapılarına okuma veya yazma gibi bazı özellikler. Bu hizmetlerin Azure Bilişsel Arama ayrı ayrı şifreleme desteği düzeyleri vardır.

| Modelleme | Belirlenmesine&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Gereklilik&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Kısıtlamalar | Şunlara uygulanır |
|------------------|-------|-------------|--------------|------------|
| sunucu tarafı şifreleme | Microsoft tarafından yönetilen anahtarlar | Hiçbiri (yerleşik) | Hiçbiri, tüm bölgelerde, tüm bölgelerde bulunan ve 24 2018 Ocak 'tan sonra oluşturulan içerikler için kullanılabilir. | İçerik (dizinler ve eş anlamlı haritalar) ve tanımlar (Dizin oluşturucular, veri kaynakları, becerileri) |
| sunucu tarafı şifreleme | Müşteri tarafından yönetilen anahtarlar | Azure Key Vault | Faturalandırılabilir katmanda, tüm bölgelerde, Ocak 2019 ' den sonra oluşturulan içerikler için kullanılabilir. | Veri disklerinde içerik (dizinler ve eş anlamlılar haritaları) |
| sunucu tarafı çift şifreleme | Müşteri tarafından yönetilen anahtarlar | Azure Key Vault | 1 2020 Ağustos 'Tan sonra Arama hizmetlerinde, seçili bölgelerde faturalanabilir katmanlarda kullanılabilir. | Veri disklerinde ve geçici disklerde içerik (dizinler ve eş anlamlılar haritaları) |

### <a name="service-managed-keys"></a>Hizmet tarafından yönetilen anahtarlar

Hizmet tarafından yönetilen şifreleme, 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanılarak [Azure depolama hizmeti şifrelemesi](../storage/common/storage-service-encryption.md)temel alan Microsoft iç işlemidir. Tam olarak şifrelenmeyen dizinlerin Artımlı güncelleştirmeleri de dahil olmak üzere tüm dizin oluşturma işleminde otomatik olarak gerçekleşir (2018 Ocak 'tan önce oluşturulmuştur).

### <a name="customer-managed-keys-cmk"></a>Müşteri tarafından yönetilen anahtarlar (CMK)

Müşteri tarafından yönetilen anahtarlar, farklı bir bölgede, ancak Azure Bilişsel Arama olarak aynı abonelik altında olabilen ek bir faturalanabilir hizmet Azure Key Vault gerektirir. CMK şifrelemesini etkinleştirmek, dizin boyutunu artırır ve sorgu performansını düşürür. Tarih gözlemlerini temel alarak sorgu süreleriyle %30 oranında %60 oranında bir artış görmeniz beklenir, ancak gerçek performans, Dizin tanımına ve sorgu türlerine göre değişir. Bu performans etkisi nedeniyle, bu özelliği yalnızca gerçekten gereken dizinlerde etkinleştirmenizi öneririz. Daha fazla bilgi için bkz. [Azure bilişsel arama müşteri tarafından yönetilen şifreleme anahtarlarını yapılandırma](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Çift şifreleme

Azure Bilişsel Arama 'de, Çift şifreleme CMK 'nin bir uzantısıdır. İki katlı şifreleme (CMK tarafından bir kez ve hizmet tarafından yönetilen anahtarlar tarafından) ve kapsamdaki kapsam, bir veri diskine yazılan uzun süreli depolamayı ve geçici disklere yazılan kısa vadeli depolamayı ele almak için kullanılır. CMK ile 1 2020 Ağustos 'Tan önce ve sonrasında CMK arasındaki fark ve Azure Bilişsel Arama 'de CMK 'nin Çift şifreleme özelliği ne kadar bir kez yapılır, geçici disklerde bekleyen verilerin ek şifrelemesi.

Çift şifreleme Şu anda bu bölgelerde oluşturulan yeni hizmetlerde 1 Ağustos 'Tan sonra kullanılabilir:

+ Batı ABD 2
+ Doğu ABD
+ Orta Güney ABD
+ US Gov Virginia
+ US Gov Arizona

## <a name="security-management"></a>Güvenlik yönetimi

### <a name="api-keys"></a>API anahtarları

API anahtar tabanlı kimlik doğrulamasına güvenilme, Azure Güvenlik en iyi uygulamalarına göre, düzenli aralıklarla yönetici anahtarını yeniden oluşturma planına sahip olmanız gerektiği anlamına gelir. Arama hizmeti başına en fazla iki yönetici anahtarı vardır. API anahtarlarının güvenliğini sağlama ve yönetme hakkında daha fazla bilgi için bkz. [API anahtarları oluşturma ve yönetme](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Etkinlik ve tanılama günlükleri

Bilişsel Arama, belirli bir kullanıcı hakkında bilgi için günlüklere başvurabilmeniz için Kullanıcı kimliklerini günlüğe kaydetmez. Ancak hizmet, belirli eylemlerin acentelerini anlamak için diğer günlüklerle ilişkilendirebilmeniz gerekebilecek oluşturma-okuma-güncelleştirme silme işlemlerini günlüğe kaydeder.

Azure 'da uyarıları ve günlük altyapısını kullanarak, sorgu hacmi artışlarını veya beklenen iş yüklerinden sapacak diğer eylemleri seçebilirsiniz. Günlükleri ayarlama hakkında daha fazla bilgi için bkz. [günlük verilerini toplama ve analiz etme](search-monitor-logs.md) ve [sorgu isteklerini izleme](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Sertifikalamaları ve uyumluluk

Azure Bilişsel Arama düzenli denetimleri içerir ve hem genel bulut hem de Azure Kamu için çeşitli küresel, bölgesel ve sektöre özgü standartlara karşı sertifikalandırilmiştir. Tüm liste için, resmi denetim raporları sayfasından [ **Microsoft Azure uyumluluk teklifleri** teknik](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) incelemesini indirin.

Uyumluluk için Azure [ilkesi](../governance/policy/overview.md) 'Ni kullanarak [Azure Güvenlik kıyaslaması](../security/benchmarks/introduction.md)için yüksek güvenlikli en iyi yöntemleri uygulayabilirsiniz. Azure Güvenlik kıyaslaması, hizmet ve veri tehditlerini azaltmak için gerçekleştirmeniz gereken önemli eylemlerle eşlenen güvenlik denetimlerine yönelik güvenlik önerileri koleksiyonudur. [Ağ güvenliği](../security/benchmarks/security-control-network-security.md), [günlüğe kaydetme ve izleme](../security/benchmarks/security-control-logging-monitoring.md)dahil olmak üzere 11 güvenlik denetimi ve birkaç ad vermek için [veri koruması](../security/benchmarks/security-control-data-protection.md) vardır.

Azure Ilkesi, Azure 'da yerleşik olarak bulunan ve Azure Güvenlik kıyaslaması dahil olmak üzere birden çok standart için uyumluluğu yönetmenize yardımcı olan bir özelliktir. İyi bilinen kıyaslamalar için Azure Ilkesi, hem ölçütü hem de uyumsuz bir yanıt sağlayan yerleşik tanımları, uyumsuz olmayan bir yanıt sağlar.

Azure Bilişsel Arama için, şu anda bir yerleşik tanım vardır. Bu, tanılama günlüğe kaydetme içindir. Bu yerleşik ile, tanılama günlüğü eksik olan herhangi bir arama hizmetini tanımlayan bir ilke atayabilir ve sonra onu açabilir. Daha fazla bilgi için bkz. [Azure Ilke mevzuatı uyumluluk denetimleri bilişsel arama](security-controls-policy.md).

## <a name="see-also"></a>Ayrıca bkz.

+ [Azure güvenliğinin temelleri](../security/fundamentals/index.yml)
+ [Azure Güvenlik](https://azure.microsoft.com/overview/security)
+ [Azure Güvenlik Merkezi](../security-center/index.yml)