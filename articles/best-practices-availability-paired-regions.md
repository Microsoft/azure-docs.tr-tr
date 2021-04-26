---
title: Azure eşleştirilmiş bölgeleri kullanarak olağanüstü durum kurtarma & iş sürekliliği sağlama
description: Azure bölgesel eşleştirme kullanarak uygulama dayanıklılığı sağlayın
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 9fda6f913fcb5325c811671cd6476dcbf2413766
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058026"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>İş sürekliliği ve olağanüstü durum kurtarma (BCDR): Eşleştirilmiş Azure Bölgeleri

## <a name="what-are-paired-regions"></a>Eşleştirilmiş bölgeler nelerdir?

Bir Azure bölgesi, gecikme süresi belirlenmiş bir çevre içinde dağıtılan ve adanmış bir düşük gecikmeli ağ üzerinden bağlanan bir veri merkezleri kümesinden oluşur.  Bu, bir Azure bölgesindeki Azure hizmetlerinin olası en iyi performansı ve güvenliği sunmasını sağlar.  

Bir Azure coğrafya, en az bir Azure bölgesi içeren bir dünya alanını tanımlar. Geographies, genellikle iki veya daha fazla bölge içeren ve veri fazlalığını ve uyumluluk sınırlarını koruyan ayrı bir pazar tanımlar.  Azure 'un genel altyapısı hakkında [buradan](https://azure.microsoft.com/global-infrastructure/regions/) daha fazla bilgi edinin

Bölgesel bir çift aynı coğrafya içindeki iki bölgeden oluşur. Azure, her bir çiftin tek seferde yalnızca bir bölgenin güncelleştirmesinin ardından, bölgesel çiftler genelinde platform güncelleştirmelerini (planlı bakım) seri hale getirir. Bir kesinti birden çok bölgeyi etkiliyorsa, her bir çiftin en az bir bölgesine kurtarma için öncelik uygulanır.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Bazı Azure Hizmetleri, iş sürekliliği sağlamak ve veri kaybına karşı korumak için eşleştirilmiş bölgelerden daha fazla avantaj sağlar.  Azure, veri kullanılabilirliği sağlamak için eşleştirilmiş bölgelerin avantajlarından yararlanan birkaç [depolama çözümü](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) sağlar. Örneğin, [Azure coğrafi olarak yedekli depolama](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS), verileri bir ikincil bölgeye otomatik olarak çoğaltır ve verilerin, birincil bölgenin kurtarılabilir olmaması durumunda bile dayanıklı olmasını sağlar. 

Tüm Azure hizmetlerinin verileri otomatik olarak çoğaltmadığını ve tüm Azure hizmetlerinin otomatik olarak başarısız bir bölgeden çift geri dönüş yapamadığını unutmayın.  Bu gibi durumlarda, kurtarma ve çoğaltmanın müşteri tarafından yapılandırılması gerekir.

## <a name="can-i-select-my-regional-pairs"></a>Bölgesel çiftlerimi seçebilir miyim?

Hayır. Bazı Azure Hizmetleri, Azure 'un [yedekli depolama alanı](./storage/common/storage-redundancy.md)gibi bölgesel çiftler üzerinde yararlanır. Bu hizmetler Yeni bölgesel eşleştirmelerinin oluşturmanıza izin vermez.  Benzer şekilde, Azure bölgesel çiftler için planlı bakım ve kurtarma önceliklendirmesini denetletiğinden, bu hizmetlerden yararlanmak için kendi bölgesel çiftlerinizi tanımlayamazsınız. Ancak, herhangi bir sayıda bölgede hizmet oluşturarak ve bunları eşleştirmek için Azure hizmetlerinden yararlanarak kendi olağanüstü durum kurtarma çözümünüzü oluşturabilirsiniz. 

Örneğin, [AzCopy](./storage/common/storage-use-azcopy-v10.md) gibi Azure hizmetlerini kullanarak farklı bir bölgedeki bir depolama hesabına veri yedeklemeleri zamanlayabilirsiniz.  Müşteriler, [Azure DNS ve Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)kullanarak, uygulamaları için birincil bölge kaybını sürdüren dayanıklı bir mimari tasarlayabilirler.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Bölgesel çiftlerim içinde Hizmetleri kullanmaya sınırlı mıyım?

Hayır. Belirli bir Azure hizmeti bölgesel bir çifte bağlı olsa da, diğer hizmetlerinizi iş gereksinimlerinizi karşılayan herhangi bir bölgede barındırabilirsiniz.  Azure GRS depolama çözümü, Doğu ABD 'de bulunan Işlem kaynaklarını kullanırken Kanada Doğu bir eş ile Kanada Orta veri eşleyebilir.  

## <a name="must-i-use-azure-regional-pairs"></a>Azure bölgesel çiftlerinin kullanılması gerekir mi?

Hayır. Müşteriler, Azure 'un bölgesel çiftlerine bağlı kalmadan dayanıklı bir hizmeti mimarileri artırmak için Azure hizmetlerinden yararlanabilir.  Bununla birlikte, [yalıtımdan](./security/fundamentals/isolation-choices.md) faydalanmak ve [kullanılabilirliği](./availability-zones/az-overview.md)geliştirmek için iş sürekliliği olağanüstü durum kurtarma (BCDR) ' i bölgesel çiftler arasında yapılandırmanızı öneririz. Birden çok etkin bölgeyi destekleyen uygulamalarda mümkün olduğunca bölge çiftindeki her iki bölgenin de kullanılmasını öneririz. Bu, uygulamalar için en iyi kullanılabilirliği ve olağanüstü durum durumunda en düşük kurtarma süresini sağlar. Mümkün olduğunda, uygulamanızı [en yüksek dayanıklılık](/azure/architecture/framework/resiliency/overview) ve [olağanüstü durum kurtarma](/azure/architecture/framework/resiliency/backup-and-recovery)kolaylığı için tasarlayın.

## <a name="azure-regional-pairs"></a>Azure bölgesel çiftleri

| Coğrafya | Bölgesel çift A | Bölgesel çift B  |
|:--- |:--- |:--- |
| Asia-Pacific |Doğu Asya (Hong Kong) | Güneydoğu Asya (Singapur) |
| Avustralya |Doğu Avustralya |Güneydoğu Avustralya |
| Avustralya |Orta Avustralya |Avustralya Orta 2 * |
| Brezilya |Güney Brezilya |Orta Güney ABD |
| Brezilya |Brezilya Güneydoğu * |Güney Brezilya |
| Kanada |Orta Kanada |Doğu Kanada |
| Çin |Kuzey Çin |Doğu Çin|
| Çin |Çin Kuzey 2 |Çin Doğu 2|
| Avrupa |Kuzey Avrupa (İrlanda) |Batı Avrupa (Hollanda) |
| Fransa |Orta Fransa|Fransa Güney *|
| Almanya |Almanya Orta Batı |Almanya Kuzey * |
| Hindistan |Orta Hindistan |Güney Hindistan |
| Hindistan |Batı Hindistan |Güney Hindistan |
| Japonya |Doğu Japonya |Batı Japonya |
| Güney Kore |Güney Kore - Orta |Güney Kore - Güney |
| Kuzey Amerika |Doğu ABD |Batı ABD |
| Kuzey Amerika |Doğu ABD 2 |Central US |
| Kuzey Amerika |Orta Kuzey ABD |Orta Güney ABD |
| Kuzey Amerika |Batı ABD 2 |Orta Batı ABD |
| Norveç | Norveç Doğu | Norveç Batı * |
| Güney Afrika | Güney Afrika - Kuzey |Güney Afrika Batı * |
| İsviçre | İsviçre Kuzey |İsviçre Batı * |
| Birleşik Krallık |Batı Birleşik Krallık |Güney Birleşik Krallık |
| Birleşik Arap Emirlikleri | BAE Kuzey | BAE Orta * |
| ABD Savunma Bakanlığı |US DoD Doğu * |US DoD Orta * |
| ABD Kamu |US Gov Arizona * |US Gov Teksas * |
| ABD Kamu |US Gov Iowa * |US Gov Virginia * |
| ABD Kamu |US Gov Virginia * |US Gov Teksas * |

(*) Belirli bölgeler, belirli müşteri senaryolarını desteklemek için kısıtlanır (örneğin, ülke içi olağanüstü durum kurtarma). Bu bölgeler yalnızca [Azure Portal yeni bir destek isteği oluşturularak](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)istek üzerine kullanılabilir.

> [!Important]
> - Batı Hindistan yalnızca bir yönde eşleştirilmiş. Batı Hindistan ikincil bölgesi Güney Hindistan, ancak Güney Hindistan ikincil bölgesi Orta Hindistan.
> - Brezilya Güney, Coğrafya dışında bir bölgeyle eşleştirildiği için benzersizdir. Brezilya Güney ikincil bölgesi Orta Güney ABD. Orta Güney ABD ikincil bölgesi Brezilya Güney değil.


## <a name="an-example-of-paired-regions"></a>Eşleştirilmiş bölge örneği
Aşağıdaki görüntüde olağanüstü durum kurtarma için bölgesel çift kullanan bir kuramsal uygulama gösterilmektedir. Yeşil sayılar, üç Azure hizmetinin (Azure işlem, depolama ve veritabanı) çapraz bölge etkinliklerini ve bölgeler arasında çoğaltma için nasıl yapılandırıldığını vurgular. Eşleştirilmiş bölgeler arasında dağıtmanın benzersiz avantajları turuncu sayılarla vurgulanır.

![Eşleştirilmiş bölge avantajlarına genel bakış](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Şekil 2 – kuramsal Azure bölgesel çifti

## <a name="cross-region-activities"></a>Çapraz bölge etkinlikleri
Şekil 2 ' de adlandırılmaktadır.

1. **Azure işlem (IaaS)** – bir olağanüstü durum sırasında kaynakların başka bir bölgede kullanılabilmesini sağlamak için daha önce ek işlem kaynakları sağlamalısınız. Daha fazla bilgi için bkz. [Azure dayanıklılık Teknik Kılavuzu](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure depolama** -yönetilen diskler kullanıyorsanız, Azure Backup [çapraz bölge yedeklemeleri](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) hakkında bilgi edinin ve Azure Site Recovery ile [sanal makineleri](./site-recovery/azure-to-azure-tutorial-enable-replication.md) bir bölgeden diğerine çoğaltma. Depolama hesapları kullanıyorsanız, coğrafi olarak yedekli depolama (GRS), bir Azure depolama hesabı oluşturulduğunda varsayılan olarak yapılandırılır. GRS ile, verileriniz birincil bölgede otomatik olarak üç kez ve eşleştirilmiş bölgede üç kez çoğaltılır. Daha fazla bilgi için bkz. [Azure Storage artıklık seçenekleri](storage/common/storage-redundancy.md).

3. **Azure SQL veritabanı** – Azure SQL veritabanı coğrafi çoğaltma ile, işlemlerin zaman uyumsuz çoğaltmasını dünyanın herhangi bir bölgesine yapılandırabilirsiniz; Ancak, bu kaynakları birçok olağanüstü durum kurtarma senaryosunda eşleştirilmiş bir bölgeye dağıtmanızı öneririz. Daha fazla bilgi için bkz. [Azure SQL veritabanı 'Nda coğrafi çoğaltma](./azure-sql/database/auto-failover-group-overview.md).

4. **Azure Resource Manager**: Resource Manager doğal olarak bölgeler arasında bileşenlerin mantıksal yalıtımını sağlar. Bu, bir bölgedeki mantıksal hataların diğer etkileri daha az olabilir.

## <a name="benefits-of-paired-regions"></a>Eşleştirilmiş bölgelerin avantajları

5. **Fiziksel yalıtım** – mümkün olduğunda Azure, bölgesel çiftteki veri merkezleri arasında en az 300 mil ayırmayı tercih eder, ancak tüm coğrafi bölgelerde pratik veya mümkün değildir. Fiziksel veri merkezi ayrımı, her iki bölgeyi aynı anda etkileyen doğal felaketler, hukuki geri kalan, güç kesintileri veya fiziksel ağ kesintileri olasılığını azaltır. Yalıtım, coğrafya içindeki kısıtlamalara tabidir (Coğrafya boyutu, güç/ağ altyapısı kullanılabilirliği, yönetmelikler vb.).  

6. **Platform tarafından sağlanmış çoğaltma** -Geo-Redundant depolama gibi bazı hizmetler, eşleştirilmiş bölgeye otomatik çoğaltma sağlar.

7. **Bölge kurtarma sırası** – geniş bir kesinti durumunda, bir bölgenin kurtarılması her çiftin dışında bir önceliğe sahiptir. Eşleştirilmiş bölgelere dağıtılan uygulamalarda bölgelerden birinin öncelikli olarak kurtarılacağı garanti edilir. Bir uygulama eşlenmez bölgeler arasında dağıtılırsa kurtarma geciktirilebilir. en kötü durumda, seçilen bölgeler kurtarılacak son iki durumda olabilir.

8. **Sıralı güncelleştirmeler** – planlı Azure sistem güncelleştirmeleri, kapalı kalma süresini en aza indirmek için (aynı anda değil) sıralı olarak (aynı zamanda değil), hatalı bir güncelleştirmenin nadir olayında hata oluşması ve mantıksal hatalar için sırayla yapılır

9. **Veri** yerleşimi – bir bölge, çift ile aynı coğrafya içinde bulunur (Brezilya Güney hariç) ve vergi ve yasalar zorlama amaçları için veri fazlalığını karşılamak üzere.