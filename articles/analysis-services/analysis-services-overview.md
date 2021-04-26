---
title: Azure Analysis Services nedir | Microsoft Docs
description: Bulutta kurumsal düzeyde veri modelleri sağlayan, tam olarak yönetilen bir hizmet olarak platform (PaaS) Azure Analysis Services hakkında bilgi edinin.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: overview
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: c3b95b92f2b76e669b4e207163f76f8cec2d3244
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731692"
---
# <a name="what-is-azure-analysis-services"></a>Azure Analysis Services nedir?

![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services, bulutta kurumsal düzeyde veri modelleri sağlayan, tam yönetilen bir hizmet olarak platformdur (PaaS). Birden fazla veri kaynağından verileri birleştirmek, ölçümleri tanımlamak ve tek, güvenilen bir tablo biçiminde anlam veri modelinde verilerinizin güvenliğini sağlamak için gelişmiş karma ve modelleme özelliklerini kullanın. Veri modeli, kullanıcıların Power BI ve Excel gibi araçları kullanarak geçici veri analizi gerçekleştirmenin daha kolay ve hızlı bir yolunu sağlar.

![Veri kaynakları](./media/analysis-services-overview/aas-overview-overall.png)

**Video:** Azure Analysis Services’in Microsoft’un genel BI özelliklerine ne kadar uygun olduğu hakkında bilgi almak için [Azure Analysis Services’e Genel Bakış](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) bölümüne göz atın.

## <a name="get-up-and-running-quickly"></a>Hızla çalışmaya başlayın

Azure portalında, birkaç dakikada [sunucu oluşturabilirsiniz](analysis-services-create-server.md). Ayrıca, Azure Resource Manager [şablonları](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) ve PowerShell'le, bildirim temelli bir şablon kullanarak sunucu oluşturabilirsiniz. Basit bir şablonla, sunucu kaynaklarını ve bunların yanında depolama hesapları ve Azure İşlevleri gibi diğer Azure bileşenlerini dağıtabilirsiniz. 

**Video:** Azure Otomasyonu’nu kullanarak sunucu oluşturmayı nasıl hızlandırabileceğiniz hakkında daha fazla bilgi almak için [Dağıtımı otomatik hale getirme](https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation) bölümüne göz atın.

Azure Analysis Services, birçok Azure hizmetiyle tümleştirildiğinden gelişmiş analiz çözümleri oluşturmanıza olanak tanır. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) ile tümleştirme, önemli verilerinize güvenli, rol tabanlı erişim sağlar. Verileri modele yükleyen bir etkinlik ekleyerek [Azure Data Factory](../data-factory/introduction.md) işlem hatlarıyla tümleştirin. Özel kodla modellerde basit düzenlemeler yapmak için [Azure Otomasyonu](../automation/automation-intro.md) ve [Azure İşlevleri](../azure-functions/functions-overview.md) kullanılabilir. 

## <a name="the-right-tier-when-you-need-it"></a>İhtiyacınız olduğunda doğru katman

Azure Analysis Services **Geliştirici**, **temel** ve **Standart** katmanlarda kullanılabilir. Her katmanda, plan maliyetleri, işleme gücüne, sorgu Işleme birimlerine (QPUs) ve bellek boyutuna göre farklılık gösterir. Sunucu oluşturduğunuzda, katman içinde bir plan seçersiniz. Aynı katman içinde planları yukarı veya aşağı doğru değişiklik yapabilir veya daha üst bir katmana yükseltebilirsiniz, ama üst katmandan daha alt bir katmana inemezsiniz.

### <a name="developer-tier"></a>Geliştirici katmanı

Bu katman değerlendirme, geliştirme ve test senaryoları için önerilir. Tek bir plan, standart katman ile sunulan aynı işlevleri içerir ancak işlemci gücü, QPU ve bellek boyutu bakımından sınırlıdır. Bu katmanda sorgu çoğaltma ölçeği genişletme *kullanılamıyor* . Bu katman bir SLA sunmaz.

|Planlama  |QPU’lar  |Bellek (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Temel katman

Bu katman, daha küçük tablo modelleriyle, sınırlı Kullanıcı eşzamanlılığa ve basit veri yenileme gereksinimlerine sahip üretim çözümleri için önerilir. Bu katmanda sorgu çoğaltma ölçeği genişletme *kullanılamıyor* . Perspektifler, çoklu bölümler ve DirectQuery tablolu model özellikleri bu katmanda *desteklenmez*.  

|Planlama  |QPU’lar  |Bellek (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    16     |

### <a name="standard-tier"></a>Standart katmanı

Bu katman, kullanıcı eşzamanlılığının elastik olmasını gerektiren ve hızla büyüyen veri modellerine sahip olan görev açısından kritik üretim uygulamalarına yöneliktir. Neredeyse gerçek zamanlı veri modeli güncelleştirmeleri için gelişmiş veri yenilemeyi ve tüm tablo modelleme özelliklerini destekler.

|Planlama  |QPU’lar  |Bellek (GB)  |
|---------|---------|---------|
|S0    |    40     |    10     |
|S1    |    100     |    25     |
|S2    |    200     |    50     |
|S4    |    400     |    100     |
|S8 <sup>[1](#naar)</sup>, <sup>[2](#rec)</sup>   |    320    |    200     |
|S9 <sup>[1](#naar)</sup>, <sup>[2](#rec)</sup>    |    640    |    400     |
|S8v2 <sup> [1](#naar)</sup>   |    640     |    200     |
|S9v2 <sup> [1](#naar)</sup>    |    1280    |    400     |

<a name="naar">1</a> -tüm bölgelerde kullanılamaz.   
<a name="rec">2</a> -S8 ve S9 [kullanım dışıdır](https://azure.microsoft.com/updates/azure-s8-and-s9-analysis-services-skus-retiring-on-31-august-2023/). v2 önerilir. 

## <a name="availability-by-region"></a>Bölgelere göre kullanılabilirlik

Azure Analysis Services, dünyanın dört bir yanındaki bölgelerde desteklenir. Desteklenen planların ve sorgu çoğaltmalarının kullanılabilirliği, seçtiğiniz bölgeye bağlıdır. Plan ve sorgu çoğaltması kullanılabilirliği ihtiyaca ve bölgelerde bulunan kaynaklara göre değişebilir. 

### <a name="americas"></a>Kuzey ve Güney Amerika

|Region  | Desteklenen planlar | Sorgu çoğaltmaları (yalnızca Standart planlar) |
|---------|---------|:---------:|
|Güney Brezilya     |    B1, B2, S0, S1, S2, S4, D1     |     1    |
|Orta Kanada    |     B1, B2, S0, S1, S2, S4, D1    |     1    |
|Orta Kanada     |     S8v2, S9v2   |    1    |
|Doğu ABD     |     B1, B2, S0, S1, S2, S4, D1    |    1     |
|Doğu ABD     |     S8v2, S9v2   |    1    |
|Doğu ABD 2     |     B1, B2, S0, S1, S2, S4, D1   |    7    |
|Doğu ABD 2     |     S8v2, S9v2   |    1    |
|Orta Kuzey ABD     |     B1, B2, S0, S1, S2, S4, D1     |    1     |
|Orta Kuzey ABD     |     S8v2, S9v2    |    1     |
|Central US     |    B1, B2, S0, S1, S2, S4, D1     |    1     |
|Central US     |    S8v2, S9v2     |    1     |
|Orta Güney ABD     |    B1, B2, S0, S1, S2, S4, D1     |    1     |
|Orta Güney ABD     |    S8v2, S9v2     |    1     |
|Orta Batı ABD   |     B1, B2, S0, S1, S2, S4, D1    |    3     |
|Batı ABD     |    B1, B2, S0, S1, S2, S4, D1    |    7   |
|Batı ABD     |    S8v2, S9v2   |    2  |
|Batı ABD 2    |    B1, B2, S0, S1, S2, S4, D1    |    3   |
|Batı ABD 2    |    S8v2, S9v2  |    1     |

### <a name="europe"></a>Europe

|Region  | Desteklenen planlar | Sorgu çoğaltmaları (yalnızca Standart planlar) |
|---------|---------|:---------:|
|Kuzey Avrupa     |    B1, B2, S0, S1, S2, S4, D1      |    7     |
|Kuzey Avrupa     |    S8v2, S9v2      |    3     |
|Güney Birleşik Krallık     |    B1, B2, S0, S1, S2, S4, D1      |     1    |
|West Europe     |    B1, B2, S0, S1, S2, S4, D1   |    7    |
|West Europe    |   S8v2, S9v2  |  1  |

### <a name="asia-pacific"></a>Asya Pasifik 

|Region  | Desteklenen planlar | Sorgu çoğaltmaları (yalnızca Standart planlar) |
|---------|---------|:---------:|
|Doğu Avustralya     |    B1, B2, S0, S1, S2, S4     |    3     |
|Doğu Avustralya     |    S8v2, S9v2    |    1     |
|Güneydoğu Avustralya     | B1, B2, S0, S1, S2, S4, D1       |    1     |
|Doğu Japonya     |   B1, B2, S0, S1, S2, S4, D1       |    1     |
|Doğu Japonya     |    S8v2, S9v2    |    1     |
|Güneydoğu Asya     |     B1, B2, S0, S1, S2, S4, D1     |   1      |
|Güneydoğu Asya     |     S8v2, S9v2     |   1      |
|Batı Hindistan     |    B1, B2, S0, S1, S2, S4, D1     |    1     |

## <a name="scale-to-your-needs"></a>Gereksinimlerinize göre ölçeklendirin

### <a name="scale-updown-pause-and-resume"></a>Ölçek artırma\azaltma, duraklatma ve sürdürme

Sunucunuzu yükseltin, indirin veya duraklatın. Azure portalını kullanın ve PowerShell'i kullanarak kolayca tam denetim sahibi olun. Sadece kullandığınız kadar ödersiniz.  

### <a name="scale-out-resources-for-fast-query-responses"></a>Hızlı sorgu yanıtları için genişletme kaynakları

Ölçek genişletme ile istemci sorguları bir sorgu havuzundaki birden çok *sorgu çoğaltması* arasında dağıtılır. Sorgu çoğaltmaları, tablo modellerinizin eşitlenmiş kopyalarını içerir. Sorgu iş yükü dağıtılarak, yüksek sorgu iş yükleri sırasında yanıt süreleri azaltılabilir. İstemci sorgularının işleme işlemlerinden olumsuz yönde etkilenmediğinden emin olmak için, model işleme işlemleri sorgu havuzundan ayrılabilir. 

En fazla yedi ek sorgu çoğaltması (sizin sunucunuzla birlikte toplam sekiz) ile bir sorgu havuzu oluşturabilirsiniz. Havuzunuzda sahip olabileceğiniz sorgu çoğaltmalarının sayısı, seçtiğiniz plana ve bölgeye bağlıdır. Sorgu çoğaltmaları, sunucunuzun bölgesi dışına yayılamaz. Sorgu çoğaltmaları, sunucunuzla aynı fiyattan faturalandırılır.

Katmanları değiştirirken yaptığınız gibi, sorgu çoğaltmalarını gereksinimlerinize göre genişletebilirsiniz. Genişletmeyi portalda veya REST API'leri kullanarak yapılandırın. Daha fazla bilgi için bkz. [Azure Analysis Services ölçeğini genişletme](analysis-services-scale-out.md).

## <a name="pricing"></a>Fiyatlandırma

Toplam maliyet, bir dizi etkene bağlıdır. Örneğin, seçtiğiniz bölge, katman, sorgu çoğaltmaları ve Duraklat/devam edin. Bölgenize yönelik tipik fiyatlandırmayı belirlemek için [Azure Analysis Services Fiyatlandırma](https://azure.microsoft.com/pricing/details/analysis-services/) hesaplayıcısını kullanın. Bu araç, tek bir bölge için tek sunuculu bir örneğin fiyatlandırmasını hesaplar. Sorgu çoğaltmalarının sunucunuzla aynı fiyattan faturalandırıldığını unutmayın. 

## <a name="built-on-sql-server-analysis-services"></a>SQL Server Analysis Services’ı temel alır

Azure Analysis Services, SQL Server Analysis Services Enterprise Edition’da bulunan harika özelliklerin çoğu ile uyumludur. Azure Analysis Services, 1200 veya üzeri [uyumluluk düzeylerinde](/analysis-services/tabular-models/compatibility-level-for-tabular-models-in-analysis-services) tablolu modelleri destekler. Tablolu modeller, Tablolu Model Betik Dili (TMSL) ve Tablolu Nesne Modeli (TOM) kodundaki tablolu meta veri nesne tanımları halinde geliştirilmiş ilişkisel modelleme yapılarıdır (model, tablolar, sütunlar). Bölümler, perspektifler, satır düzeyi güvenlik, çift yönlü ilişkiler ve çeviriler desteklenir \* . Çok boyutlu modeller ve *SharePoint için PowerPivot Azure Analysis Services desteklenmez.*

Hem bellek içi hem de DirectQuery modlarında tablolu modeller desteklenir. Bellek içi modda (varsayılan) tablolu modeller birden çok veri kaynağını destekler. Model verileri yüksek oranda sıkıştırılmış ve bellek içinde önbelleğe alınmış olduğundan, bu mod büyük miktarlarda veriler üzerinde en hızlı sorgu yanıtını sağlar. Ayrıca, karmaşık veri kümeleri ve sorgular için en fazla esnekliği sağlar. 

Bölümleme, artımlı yükleri etkinleştirir, paralelleştirmeyi artırır ve bellek tüketimini azaltır. Hesaplanmış tablolar gibi diğer gelişmiş veri modelleme özellikleri ve tüm DAX işlevleri desteklenir. Veri kaynaklarından önbelleğe alınmış verileri güncelleştirmek için bellek içi modeller yenilenmelidir (işlenmelidir). Azure hizmet sorumlusu desteği sayesinde, PowerShell, TOM, TMSL ve REST kullanan katılımsız yenileme işlemleri, model verilerinizin her zaman güncel olduğundan emin olmanızı sağlar. 

DirectQuery modu*, depolama ve sorgu yürütme için arka uç ilişkisel veritabanından yararlanır. Tek SQL Server çok büyük veri kümeleri, SQL Server veri ambarı, Azure SQL veritabanı, Azure SYNAPSE Analytics, Oracle ve Teradata veri kaynakları desteklenmektedir. Arka uç veri kümeleri, kullanılabilir sunucu kaynak belleğini aşabilir. Karmaşık veri modeli yenileme senaryoları gerekli değildir. Ayrıca, sınırlı veri kaynağı türleri, DAX formülü sınırlamaları gibi bazı kısıtlamalar vardır ve bazı gelişmiş veri modelleme özellikleri desteklenmez. Sizin için en iyi modu belirlemeden önce bkz. [Direct Query modu](/analysis-services/tabular-models/directquery-mode-ssas-tabular).

\* Özellik kullanılabilirliği katmana bağlıdır.

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları

Azure Analysis Services’te tablolu modeller, basit metin dosyalarından Azure Data Lake Store’daki Büyük Verilere varan çeşitlilikte veri kaynağını destekler. Daha fazla bilgi edinmek için bkz. [Azure Analysis Services'ta desteklenen veri kaynakları](analysis-services-datasource.md).

## <a name="compatibility-level"></a>Uyumluluk düzeyi

Uyumluluk düzeyi Analysis Services altyapıdaki yayına özgü davranışları ifade eder. Azure Analysis Services, 1200 veya üzeri uyumluluk düzeylerinde tablolu modelleri destekler. Daha fazla bilgi için bkz. [tablolu modeller Için uyumluluk düzeyi](/analysis-services/tabular-models/compatibility-level-for-tabular-models-in-analysis-services).


## <a name="your-data-is-secure"></a>Verileriniz güvende

Azure Analysis Services, birden fazla düzeyde hassas verileriniz için güvenlik sağlar. Azure hizmeti olarak Analysis Services, Azure platformunun bir parçası olarak otomatik olarak etkinleştirilmiş dağıtılmış hizmet reddi (DDoS) saldırılarının **temel** düzey korumasını sağlar. Daha fazla bilgi için bkz. [Azure DDoS koruması standardına genel bakış](../ddos-protection/ddos-protection-overview.md). 

Sunucu düzeyinde Analysis Services, güvenlik duvarı, Azure kimlik doğrulaması, sunucu yöneticisi rolleri ve Server-Side şifreleme sağlar. Veri modeli düzeyinde, Kullanıcı rolleri, satır düzeyi ve nesne düzeyinde güvenlik, verilerinizin güvende olduğundan ve yalnızca bunu gördüğü kullanıcılar tarafından görülendiğinden emin olmanızı sağlar.

### <a name="firewall"></a>Güvenlik Duvarı

Azure Analysis Services Güvenlik Duvarı, kurallarda belirtilen IP adresleri dışındaki tüm istemci bağlantılarını engeller. Varsayılan olarak, güvenlik duvarı koruması yeni sunucular için etkin değildir. Önerilen güvenlik duvarı koruması etkinleştirilir ve kurallar sunucu sağlama komut dosyasının bir parçası olarak veya sunucu oluşturulduktan hemen sonra portalda yapılandırılır. İstemci IP’lerine veya aralığa göre izin verilen IP adreslerini belirten kuralları yapılandırın. Power BI (hizmet) bağlantılarına da izin verilebilir veya bağlantılar engellenebilir. Güvenlik duvarı ve kuralları portaldan ya da PowerShell kullanarak yapılandırın. Daha fazla bilgi için bkz. [Sunucu güvenlik duvarı yapılandırma](analysis-services-qs-firewall.md).

### <a name="authentication"></a>Kimlik Doğrulaması

Kullanıcı kimlik doğrulaması, [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) tarafından işlenir. Kullanıcılar oturum açarken veritabanına rol tabanlı erişim ile bir kuruluş hesabı kimliği kullanır. Kullanıcı kimlikleri, sunucunun içinde bulunduğu abonelik için varsayılan Azure Active Directory’nin üyesi olmalıdır. Daha fazla bilgi için bkz. [Kimlik doğrulaması ve izinler](analysis-services-manage-users.md).

### <a name="data-security"></a>Veri güvenliği

Azure Analysis Services, Analysis Services veritabanları için depolamayı ve meta verileri kalıcı hale getirmek için Azure Blob depolamayı kullanır. Blob içindeki veri dosyaları [Azure Blob sunucu tarafı şifrelemesi (SSE)](../storage/common/storage-service-encryption.md)kullanılarak şifrelenir. Doğrudan Sorgu modu kullanılırken yalnızca meta veriler depolanır. Gerçek verilere veri kaynağından şifrelenmiş protokol aracılığıyla sorgu zamanında erişilir.

[Şirket içi veri ağ geçidi](analysis-services-gateway.md) yüklenip yapılandırılarak şirket içi veri kaynaklarınızda bulunan verilere güvenli erişim gerçekleştirilir. Ağ geçitleri hem DirectQuery hem de bellek içi modlar için veri erişimi sağlar.

### <a name="roles"></a>Roller

Analysis Services, sunucu ve model veritabanı işlemlerine, nesnelerine ve verilerine erişim izni veren [rol tabanlı yetkilendirme](/analysis-services/tabular-models/roles-ssas-tabular) kullanır. Bir sunucuya veya veritabanına erişimi olan tüm kullanıcılar, atanmış bir rol içinde Azure AD kullanıcı hesapları ile bunu yapar. Sunucu yöneticisi rolü, sunucu kaynağı düzeyindedir. Varsayılan olarak, bir sunucuyu oluştururken kullanılan hesap Sunucu Yöneticileri rolüne otomatik olarak eklenir. Portal, SSMS veya PowerShell kullanılarak başka kullanıcı ve grup hesapları eklenir.
  
Yönetici olmayan ve verileri sorgulayan son kullanıcılara, veritabanı rolleri aracılığıyla erişim izni verilir. Veritabanı rolü, veritabanında ayrı bir nesne olarak oluşturulur ve yalnızca bu rolün oluşturulduğu veritabanı için geçerli olur. Veritabanı rolleri, (veritabanı) Yönetici, Okuma ile Okuma ve İşleme izinleri tarafından tanımlanır. Kullanıcı ve grup hesapları, SSMS veya PowerShell kullanılarak eklenir.

### <a name="row-level-security"></a>Satır düzeyinde güvenlik

Tüm uyumluluk düzeylerindeki tablolu modeller, satır düzeyinde güvenliği destekler. Satır düzeyinde güvenlik, bir tablodaki satırları ve bir kullanıcının sorgulayabileceği ilişkili bir tablonun birçok yönündeki herhangi bir satırı tanımlayan DAX ifadeleri kullanılarak modelde yapılandırılır. DAX ifadeleri kullanan satır filtreleri, Okuma ile Okuma ve İşleme izinleri için tanımlanır. 

### <a name="object-level-security"></a>Nesne düzeyinde güvenlik 

1400 uyumluluk düzeyindeki tablolu modeller, tablo düzeyinde güvenlik ile sütun düzeyinde güvenlik içeren nesne düzeyinde güvenliği destekler. Nesne düzeyinde güvenlik, TMSL veya TOM kullanılarak Model.bim dosyasındaki JSON tabanlı meta verilerde ayarlanır. Daha fazla bilgi almak için bkz. [Nesne düzeyinde güvenlik](/analysis-services/tabular-models/object-level-security).

### <a name="automation-through-service-principals"></a>Hizmet sorumluları aracılığıyla Otomasyon

Hizmet sorumluları, katılımsız kaynak ve hizmet düzeyinde işlemler gerçekleştirmek için kiracınızın içinde oluşturduğunuz bir Azure Active Directory uygulama kaynağıdır. Hizmet sorumluları, veri yenileme, ölçek artırma/azaltma ve duraklatma/sürdürme gibi genel görevleri otomatik hale getirmek amacıyla Azure Otomasyonu, PowerShell katılımsız modu, özel istemci uygulamaları ve web uygulamaları ile birlikte kullanılır. Rol üyeliği aracılığıyla hizmet sorumlularına izinler atanır. Daha fazla bilgi almak için bkz. [Hizmet sorumlularıyla otomasyon](analysis-services-service-principal.md).

### <a name="azure-governance"></a>Azure idaresi

Azure Analysis Services, [Microsoft Online Services Koşullarına](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) ve [Microsoft Gizlilik Bildirimine](https://privacy.microsoft.com/privacystatement) tabidir.
Azure Güvenliği hakkında daha fazla bilgi edinmek için bkz. [Microsoft Güven Merkezi](https://www.microsoft.com/trustcenter).

## <a name="use-the-tools-you-already-know"></a>Bildiğiniz araçları kullanın

![BI geliştirici araçları](./media/analysis-services-overview/aas-overview-dev-tools.png)

### <a name="visual-studio"></a>Visual Studio

Analysis Services projeleriyle Visual Studio ile modeller geliştirin ve dağıtın. Analysis Services projeleri uzantısı, sizi hızla ve hızlı bir şekilde kullanmaya başlamanızı sağlayan şablonları ve sihirbazları içerir. Visual Studio 'da model yazma ortamı artık tablo 1400 ve daha yüksek modeller için modern veri kaynağı veri kaynağı sorgusu ve karma işlevselliği içerir. Power BI Masaüstü ve Excel 2016'daki Veri Al işlevini biliyorsanız, üst düzeyde özelleştirilmiş veri kaynağı sorguları oluşturmanın ne kadar kolay olduğunu zaten biliyor olmalısınız. 

Microsoft Analysis Services projeleri ücretsiz bir yüklenebilir VSıX paketi olarak sunulmaktadır. [Market'ten indirin](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects). Uzantı, ücretsiz topluluk sürümü de dahil olmak üzere herhangi bir Visual Studio 2017 ve üzeri sürümüyle birlikte çalışmaktadır.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)’yu (SSMS) kullanarak sunucularınızı ve model veritabanlarınızı yönetin. Sunucularınıza bulutta bağlayın. TMSL betiklerini, doğrudan XMLA sorgu penceresinden çalıştırın ve TMSL betikleri ve PowerShell kullanarak görevleri otomatik hale getirin. Yeni özellikler ve işlevler hızla kullanıma sunulur çünkü SSMS her ay güncelleştirilir.

### <a name="open-source-tools"></a>Açık kaynaklı Araçlar

Analysis Services, Araçlar oluşturan geliştiricilerin canlı bir topluluğuna sahiptir. Sezgisel, hafif bir düzenleyici kullanarak tablolu modeller oluşturmaya, korumaya ve yönetmeye yönelik açık kaynaklı bir araç olan [tablolu düzenleyiciyi](https://tabulareditor.github.io/)kontrol ettiğinizden emin olun. Dax [Studio](https://daxstudio.org/), DAX yazma, tanılama, performans ayarlama ve analize yönelik harika bir açık kaynaklı araçtır.

### <a name="powershell"></a>PowerShell

Sunucu kaynakları oluşturma, sunucu işlemlerini askıya alma veya sürdürme veya hizmet düzeyi (katman) Azure PowerShell cmdlet 'lerini değiştirme gibi sunucu kaynak yönetimi görevleri. Rol üyeleri ekleme, işleme veya TMSL betiklerini çalıştırma gibi veritabanlarını yönetmeye yönelik diğer görevlerde, SqlServer modülündeki cmdlet'ler kullanılır. Daha fazla bilgi almak için bkz. [Azure Analysis Services’i PowerShell ile yönetme](analysis-services-powershell.md).

### <a name="object-model-and-scripting"></a>Nesne modeli ve betik oluşturma

Tablosal modeller hızlı geliştirme sunar ve bunlar üst düzeyde özelleştirilebilir. Tablolu modeller model nesnelerini açıklamaya yönelik [Tablolu Nesne Modeli](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo)'ni (TOM) de içerir. TOM, [Tablosal Model Betik Dili (TMSL)](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) aracılığıyla JSON’da ve [Microsoft.AnalysisServices.Tabular](/dotnet/api/microsoft.analysisservices.tabular) ad alanı aracılığıyla AMO veri tanımlama dilinde kullanıma sunulur. 

## <a name="supports-the-latest-client-tools"></a>En son istemci araçlarını destekler

![Veri görselleştirmeleri](./media/analysis-services-overview/aas-overview-clients.png)

Power BI, Excel, Reporting Services ve üçüncü taraf araçlar gibi modern veri keşif ve görselleştirme araçlarının tümü desteklenir ve kullanıcılara modern verileriniz üzerinde üst düzeyde etkileşimli ve görsel açıdan zengin öngörüler sağlar. 

## <a name="monitoring-and-diagnostics"></a>İzleme ve tanılama

Azure Analysis Services, Azure Izleyici ölçümleriyle tümleşiktir ve sunucularınızın performansını ve sistem durumunu izlemenize yardımcı olacak çok sayıda kaynağa özgü ölçüm sağlar. Daha fazla bilgi için bkz. [Sunucu ölçümlerini izleme](analysis-services-monitor.md). [Kaynak platformu günlükleriyle](../azure-monitor/essentials/platform-logs-overview.md)ölçümleri kaydedin. Azure [depolama](https://azure.microsoft.com/services/storage/)'ya günlükleri izleyip gönderin, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)'a aktarın [ve Azure 'Da bir hizmet olan](https://www.microsoft.com/cloud-platform/operations-management-suite)Azure [izleyici günlüklerine](https://azure.microsoft.com/services/log-analytics/)dışarı aktarın. Daha fazla bilgi için bkz. [Tanılama günlüğüne kaydetmeyi ayarlama](analysis-services-logging.md).

Azure Analysis Services ayrıca [Dynamic Management Görünümleri (DMV)](/analysis-services/instances/use-dynamic-management-views-dmvs-to-monitor-analysis-services) kullanmayı destekler. SQL söz dizimine göre DMV’ler, sunucu örneği hakkında meta veri ve izleme bilgileri döndüren şema satır kümeleri ile arabirim oluşturur.

## <a name="documentation"></a>Belgeler

Azure Analysis Services’e özgü belgeler buraya eklenir. Makaleleri bulmak için tarayıcı ekranınızın sol tarafındaki içindekiler bölümünü kullanın. 

Azure Analysis Services 'daki tablo modelleri SQL Server Analysis Services ve Power BI Premium veri kümelerinde tablolu modellerle aynı olduğundan, [Analysis Services belgelerinde](/analysis-services/?view=azure-analysis-services-current&preserve-view=true)paylaşılan veri modelleme öğreticileri, kavramsal, yordamsal, geliştirici ve başvuru makaleleriyle ilgili kapsamlı bir kitaplık vardır. Paylaşılan Analysis Services belgelerindeki makaleler, başlığın altındaki bir başlık IÇIN GEÇERLI olan Azure Analysis Services için de geçerli olup olmadığını gösterir. Yalnızca kullandığınız platforma uygulanan makaleleri görmek için içindekiler tablosunun üzerindeki sürüm seçiciyi de kullanabilirsiniz.

![Paylaşılan belgeler](./media/analysis-services-overview/aas-overview-applies-to.png)

### <a name="contribute"></a>Katkıda bulunun!

Bu makale gibi Analysis Services belgeleri açık kaynaktır. Nasıl katkıda bulunabileceğiniz hakkında daha fazla bilgi edinmek için [docs katılımcısı Kılavuzu](/contribute/)' na bakın. 

Azure Analysis Services belgeleri ayrıca [GitHub Sorunları](/teamblog/a-new-feedback-system-is-coming-to-docs)’nı kullanır. Ürün veya belgeler hakkında geri bildirim sağlayabilirsiniz. Bir makalenin altındaki **Geri bildirim** seçeneğini kullanın. GitHub sorunları, paylaşılan Analysis Services belgeleri için etkin değildir. 

## <a name="blogs"></a>Bloglar

Her şey çok hızlı gelişiyor. [Power BI blogundan](https://powerbi.microsoft.com/blog/category/analysis-services/) ve [Azure blogundan](https://azure.microsoft.com/blog/)en son bilgileri alın.

## <a name="community"></a>Topluluk

Analysis Services’ın canlı bir kullanıcı topluluğu vardır. [Azure Analysis Services forumundaki](https://aka.ms/azureanalysisservicesforum) konuşmalara katılın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Ücretsiz Azure Denemesi için kaydolun](https://azure.microsoft.com/offers/ms-azr-0044p/)   

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Sunucu oluşturma - Portal](analysis-services-create-server.md)   

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Sunucu oluşturma - PowerShell](analysis-services-create-powershell.md)
