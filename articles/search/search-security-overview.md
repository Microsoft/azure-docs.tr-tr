---
title: Güvenlik ve veri gizliliği
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama, SOC 2, HIPAA ve diğer sertifikalarla uyumludur. Filtre ifadelerinde Kullanıcı ve grup güvenlik tanımlayıcılarıyla bağlantı ve veri şifreleme, kimlik doğrulama ve kimlik erişimi.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2e509535473fa50fd3150965e1513e056ead18a6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794344"
---
# <a name="security-and-data-privacy-in-azure-cognitive-search"></a>Azure Bilişsel Arama güvenlik ve veri gizliliği

Kapsamlı güvenlik özellikleri ve erişim denetimleri, özel içeriğin bu şekilde kalmasını sağlamak için Azure Bilişsel Arama yerleşik olarak bulunur. Bu makalede, Azure Bilişsel Arama 'da yerleşik olarak bulunan güvenlik özellikleri ve standartlar uyumluluğu numaralandırılır.

Azure Bilişsel Arama güvenlik mimarisi fiziksel güvenlik, şifrelenmiş aktarımlar, şifrelenmiş depolama ve platform genelinde standartlar uyumluluğuna yayılmıştır. Azure Bilişsel Arama yalnızca kimliği doğrulanmış istekleri kabul eder. İsteğe bağlı olarak, güvenlik filtreleri aracılığıyla içeriğe göre Kullanıcı başına erişim denetimleri ekleyebilirsiniz. Bu makalede her katmandaki güvenliğe dokunduğunda, birincil olarak Azure Bilişsel Arama 'da verilerin ve işlemlerin güvenliğinin nasıl sağlandığı üzerine odaklanılmıştır.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Standartlar uyumluluğu: ISO 27001, SOC 2, HIPAA

Azure Bilişsel Arama, [haziran 2018 ' de duyurulduğu](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/)için aşağıdaki standartlara yönelik olarak sertifikalandırilmiştir:

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [SOC 2 tür 2 uyumluluğu](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Tam rapor için [Azure ve Azure Kamu SOC 2 tür II raporuna](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)gidin. 
+ [Sağlık sigortası taşınabilirlik ve Sorumluluk Yasası (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Part 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HıTRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI DSS düzeyi 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Avustralya ıRAP Unsınıflandırılmamış DLD](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Standart uyumluluk, genel olarak kullanılabilen özellikler için geçerlidir. Önizleme özellikleri, genel kullanılabilirliğe geçiş yaparken sertifikalıdır ve katı standartlar gereksinimlerine sahip çözümlerde kullanılmamalıdır. Uyumluluk sertifikası Microsoft Azure uyumluluğu ve [Güven Merkezi](https://www.microsoft.com/en-us/trustcenter) ['ne genel bakış](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) bölümünde belgelenmiştir. 

## <a name="encrypted-transmission-and-storage"></a>Şifrelenmiş iletim ve depolama

Şifreleme, tüm dizin oluşturma işlem hattının tamamında genişletiliyor: bağlantılardan, iletimden ve Azure Bilişsel Arama depolanan dizinli verilere kadar.

| Güvenlik katmanı | Açıklama |
|----------------|-------------|
| Aktarım sırasında şifreleme <br>(HTTPS/SSL/TLS) | Azure Bilişsel Arama, HTTPS bağlantı noktası 443 ' de dinler. Platform genelinde Azure Hizmetleri bağlantıları şifrelenir. <br/><br/>Tüm istemciden hizmete Azure Bilişsel Arama etkileşimleri SSL/TLS 1,2 özellikli bir hizmettir.  Hizmetinize SSL bağlantıları için TLSv 1.2 kullandığınızdan emin olun.|
| Bekleme sırasında şifreleme <br>Microsoft tarafından yönetilen anahtarlar | Şifreleme, dizin oluşturma işleminin tamamlanma süresi veya dizin boyutu üzerinde ölçülebilir bir etkisi olmadan dizin oluşturma işleminde tamamen internalized. Tam olarak şifrelenmemiş bir dizine yönelik artımlı güncelleştirmeler de dahil olmak üzere tüm dizin oluşturma işleminde otomatik olarak gerçekleşir (2018 Ocak 'tan önce oluşturulmuştur).<br><br>Dahili olarak, şifreleme, 256 bit [AES şifrelemesi](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)kullanılarak [Azure depolama hizmeti şifrelemesi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)tabanlıdır.<br><br> Şifreleme, Microsoft tarafından dahili olarak yönetilen sertifika ve şifreleme anahtarları ve evrensel olarak uygulanarak Azure Bilişsel Arama için dahili olarak kullanılır. Şifrelemeyi kapatamaz veya kapatamaz, kendi anahtarlarınızı yönetebilir veya kullanabilir ya da portalda veya program aracılığıyla şifreleme ayarlarını görüntüleyebilirsiniz.<br><br>Bekleyen şifreleme, 24 Ocak 2018 ' de duyuruldu ve tüm bölgelerde ücretsiz katman dahil tüm hizmet katmanlarına uygulanıyor. Tam şifreleme için, bu tarihten önce oluşturulan dizinlerin, şifrelemenin gerçekleşmesi için bırakılması ve yeniden oluşturulması gerekir. Aksi takdirde, yalnızca 24 Ocak 'tan sonra eklenen yeni veriler şifrelenir.|
| Bekleme sırasında şifreleme <br>Müşteri tarafından yönetilen anahtarlar | Müşteri tarafından yönetilen anahtarlarla şifreleme, ücretsiz hizmetler için kullanılamayan bir **Önizleme** özelliğidir. Ücretli hizmetler için, yalnızca en son Preview API sürümü (API-sürümü = 2019-05 -06-Preview) kullanılarak 2019 Ocak 'ta veya sonrasında oluşturulan arama hizmetleri için kullanılabilir.<br><br>Azure Bilişsel Arama dizinleri ve eş anlamlı haritalar artık Azure Key Vault ' de müşteri anahtarları yönetilen anahtarlarıyla geri alınabilir. Daha fazla bilgi için bkz. [Azure 'da şifreleme anahtarlarını yönetme bilişsel arama](search-security-manage-encryption-keys.md).<br>Bu özellik, bekleyen varsayılan şifrelemeyi değiştirmez, ancak buna ek olarak uygulanır.<br>Bu özelliğin etkinleştirilmesi, dizin boyutunu artırır ve sorgu performansını düşürür. Tarih gözlemlerini temel alarak sorgu süreleriyle %30 oranında %60 oranında bir artış görmeniz beklenir, ancak gerçek performans, Dizin tanımına ve sorgu türlerine göre değişir. Bu performans etkisi nedeniyle, bu özelliği yalnızca gerçekten gereken dizinlerde etkinleştirmenizi öneririz.

## <a name="azure-wide-user-access-controls"></a>Azure genelindeki Kullanıcı erişimi denetimleri

Birkaç güvenlik mekanizması Azure genelinde kullanılabilir ve bu nedenle otomatik olarak oluşturduğunuz Azure Bilişsel Arama kaynaklarında kullanılabilir.

+ [Silmeyi engellemek için abonelikte veya kaynak düzeyinde kilitler](../azure-resource-manager/resource-group-lock-resources.md)
+ [Bilgilere ve yönetim işlemlerine erişimi denetlemek için rol tabanlı Access Control (RBAC)](../role-based-access-control/overview.md)

Tüm Azure Hizmetleri, erişim düzeylerini her hizmet arasında tutarlı olarak ayarlamak için rol tabanlı erişim denetimlerini (RBAC) destekler. Örneğin, yönetici anahtarı gibi hassas verileri görüntüleme, sahip ve katkıda bulunan rolleriyle kısıtlıdır, ancak hizmet durumunu görüntülemek herhangi bir rolün üyeleri tarafından kullanılabilir. RBAC, sahip, katkıda bulunan ve okuyucu rolleri sağlar. Varsayılan olarak, tüm hizmet yöneticileri sahip rolünün üyeleridir.

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Hizmet erişimi ve kimlik doğrulaması

Azure Bilişsel Arama, Azure platformunun güvenlik korumalarını devralırken, kendi anahtar tabanlı kimlik doğrulamasını da sağlar. Bir API anahtarı rastgele oluşturulan rakamlardan ve harflerden oluşan bir dizedir. Anahtar türü (yönetici veya sorgu), erişim düzeyini belirler. Geçerli bir anahtarın gönderilmesi, isteğin güvenilir bir varlıktan kaynaklandığı kanıtları kabul edilir. 

Arama hizmetinize iki tür anahtara göre etkinleştirilen iki erişim düzeyi vardır:

* Yönetici erişimi (hizmette yapılan tüm okuma/yazma işlemleri için geçerli)
* Sorgu erişimi (bir dizinin belgeler koleksiyonuna karşı sorgular gibi salt okuma işlemleri için geçerli)

Hizmet sağlandığında *yönetici anahtarları* oluşturulur. *Birincil* ve *İkincil* olarak tasarlanan iki yönetici anahtarı vardır, ancak bunlar aslında değiştirilebilir. Her hizmette iki yönetici anahtarı bulunur, bu sayede hizmetinize erişimi kaybetmeksizin bir veya daha fazla dağıtım yapabilirsiniz. Azure Güvenlik en iyi uygulamaları başına [yönetici anahtarını](search-security-api-keys.md#regenerate-admin-keys) düzenli aralıklarla yeniden oluşturabilirsiniz, ancak toplam yönetici anahtar sayısına ekleyemezsiniz. Arama hizmeti başına en fazla iki yönetici anahtarı vardır.

*Sorgu anahtarları* gerekli olarak oluşturulur ve sorgular veren istemci uygulamalar için tasarlanmıştır. En çok 50 sorgu anahtarı oluşturabilirsiniz. Uygulama kodu ' nda, belirli bir dizinin belgeler koleksiyonuna salt okuma erişimi sağlamak için arama URL 'sini ve sorgu API 'si anahtarını belirtirsiniz. Bir arada, uç nokta, salt okuma erişimi için bir API anahtarı ve bir hedef dizin, istemci uygulamanızdan bağlantının kapsamını ve erişim düzeyini tanımlar.

Her istekte bir zorunlu anahtar, bir işlem ve bir nesneden oluşan her istekte kimlik doğrulaması gerekir. Birlikte zincirleme yaparken, iki izin düzeyi (tam veya salt okuma) ve bağlam (örneğin, dizin üzerinde bir sorgu işlemi), hizmet işlemlerinde tam SPI güvenliği sağlamak için yeterlidir. Anahtarlar hakkında daha fazla bilgi için bkz. [API anahtarları oluşturma ve yönetme](search-security-api-keys.md).

## <a name="index-access"></a>Dizin erişimi

Azure Bilişsel Arama, tek bir dizin, güvenli kılınabilir bir nesne değildir. Bunun yerine, bir dizin erişimi, hizmet katmanında (okuma veya yazma erişimi) bir işlemin içeriğiyle birlikte belirlenir.

Son Kullanıcı erişimi için, bir sorgu anahtarı kullanarak bağlanmak üzere sorgu istekleri yapılandırabilirsiniz, bu da tüm istekleri salt okunurdur ve uygulamanız tarafından kullanılan belirli bir dizini içerir. Bir sorgu isteğinde, Dizin bağlama veya birden çok dizine aynı anda erişme kavramı yoktur, bu nedenle tüm istekler tanım ile tek bir dizin hedefleyin. Bu nedenle, sorgu isteğinin kendisi (bir anahtar ve tek bir hedef dizin), güvenlik sınırını tanımlar.

Dizinlere yönetici ve geliştirici erişimi farklılaştırılabilir: her ikisi de hizmet tarafından yönetilen nesneleri oluşturmak, silmek ve güncelleştirmek için yazma erişimine ihtiyaç duyar. Hizmetinize yönetici anahtarına sahip olan herkes aynı hizmette bulunan herhangi bir dizini okuyabilir, değiştirebilir veya silebilir. Dizinlerin yanlışlıkla veya kötü amaçlı olarak silinmesine karşı koruma için, kod varlıkları için şirket içi kaynak denetiminiz, istenmeyen bir dizin silme veya değiştirme işlemi için bir çözüm oluşturur. Azure Bilişsel Arama, kullanılabilirliği sağlamak için küme içinde yük devretmeye sahiptir, ancak dizinleri oluşturmak veya yüklemek için kullanılan özel kodunuzu depolamaz veya yürütmez.

Dizin düzeyinde güvenlik sınırları gerektiren çok kiracılı çözümler için, bu tür çözümler genellikle müşterilerin Dizin yalıtımını işlemek için kullanacağı bir orta katman içerir. Çok kiracılı kullanım durumu hakkında daha fazla bilgi için bkz. [çok kiracılı SaaS uygulamaları ve Azure bilişsel arama Için tasarım desenleri](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Yönetici erişimi

[Rol tabanlı erişim (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) , hizmet ve içeriği üzerinde denetimlere erişip erişemeyeceğinizi belirler. Azure Bilişsel Arama hizmetinde bir sahibiniz veya katılımcısı varsa, hizmette nesne oluşturmak, güncelleştirmek veya silmek için portalı veya PowerShell **az. Search** modülünü kullanabilirsiniz. [Azure bilişsel arama Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)de kullanabilirsiniz.

## <a name="user-access"></a>Kullanıcı erişimi

Varsayılan olarak, bir dizine kullanıcı erişimi, sorgu isteğindeki erişim anahtarı tarafından belirlenir. Çoğu geliştirici, istemci tarafı arama istekleri için [*sorgu anahtarları*](search-security-api-keys.md) oluşturur ve atar. Sorgu anahtarı, Dizin içerisindeki tüm içeriğe okuma erişimi verir.

İçerik üzerinde ayrıntılı, Kullanıcı başına denetim istiyorsanız, sorgularda güvenlik filtreleri derleyebilir ve belirli bir güvenlik kimliğiyle ilişkili belgeleri getirebilirsiniz. Kimlik tabanlı erişim denetimi, önceden tanımlanmış roller ve rol atamaları yerine, belgelerin ve içeriğin arama sonuçlarını kimliklere göre kırpan bir *filtre* olarak uygulanır. Aşağıdaki tabloda yetkisiz içeriğin arama sonuçlarını kırpma için iki yaklaşım açıklanmaktadır.

| Uygulanabilecek | Açıklama |
|----------|-------------|
|[Kimlik filtrelerine göre güvenlik kırpması](search-security-trimming-for-azure-search.md)  | Kullanıcı kimliği erişim denetimi uygulamak için temel iş akışını belgeler. Bir dizine güvenlik tanımlayıcıları eklenmesini ve sonra yasaklanmış içeriğin sonuçlarını kırpmak için bu alana karşı filtrelemeyi açıklar. |
|[Azure Active Directory kimliklerine göre güvenlik kırpması](search-security-trimming-for-azure-search-with-aad.md)  | Bu makale, Azure bulut platformunda [ücretsiz hizmetlerden](https://azure.microsoft.com/free/) biri olan Azure ACTIVE DIRECTORY (AAD) ' den kimlik alma adımlarını sağlayan önceki makaleye genişletilir. |

## <a name="table-permissioned-operations"></a>Tablo: permissioned işlemler

Aşağıdaki tabloda, Azure Bilişsel Arama 'de izin verilen işlemler özetlenmektedir ve hangi anahtarın belirli bir işleme erişim kilidi vardır.

| İşlem | İzinler |
|-----------|-------------------------|
| Hizmet oluşturun | Azure abonelik sahibi|
| Hizmeti ölçeklendirme | Kaynak üzerinde yönetici anahtarı, RBAC sahibi veya katkıda bulunan  |
| Hizmet silme | Kaynak üzerinde yönetici anahtarı, RBAC sahibi veya katkıda bulunan |
| Hizmette nesne oluşturun, değiştirin, silin: <br>Dizinler ve bileşen bölümleri (çözümleyici tanımları, Puanlama profilleri, CORS seçenekleri dahil), Dizin oluşturucular, veri kaynakları, eş anlamlılar, öneri araçları. | Kaynak üzerinde yönetici anahtarı, RBAC sahibi veya katkıda bulunan  |
| Dizin sorgulama | Yönetici veya sorgu anahtarı (RBAC uygulanamaz) |
| İstatistik, sayı ve nesne listesi döndürme gibi sistem bilgilerini sorgulama. | Yönetici anahtarı, kaynak üzerinde RBAC (sahip, katkıda bulunan, okuyucu) |
| Yönetici anahtarlarını yönetme | Kaynak üzerinde yönetici anahtarı, RBAC sahibi veya katkıda bulunan. |
| Sorgu anahtarlarını Yönet |  Kaynak üzerinde yönetici anahtarı, RBAC sahibi veya katkıda bulunan.  |

## <a name="physical-security"></a>Fiziksel güvenlik

Microsoft veri merkezleri, sektör lideri fiziksel güvenlik sağlar ve kapsamlı standartlar ve yönetmeliklerle uyumludur. Daha fazla bilgi edinmek için [küresel veri merkezleri](https://www.microsoft.com/cloud-platform/global-datacenters) sayfasına gidin veya veri merkezi güvenliği hakkında kısa bir video izleyin.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Ayrıca bkz.

+ [Kullanmaya başlama .NET (bir dizin oluşturmak için bir yönetici anahtarı kullanmayı gösterir)](search-create-index-dotnet.md)
+ [Kullanmaya başlama REST (bir dizin oluşturmak için bir yönetici anahtarı kullanmayı gösterir)](search-create-index-rest-api.md)
+ [Azure Bilişsel Arama filtreleri kullanılarak kimlik tabanlı erişim denetimi](search-security-trimming-for-azure-search.md)
+ [Azure Bilişsel Arama filtrelerini kullanarak kimlik tabanlı erişim denetimi Active Directory](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure Bilişsel Arama filtreler](search-filters.md)