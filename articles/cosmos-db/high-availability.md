---
title: Azure Cosmos DB yüksek kullanılabilirlik
description: Bu makalede, Azure Cosmos DB yüksek kullanılabilirlik sağladığını açıklanmaktadır
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ac1e77d99707cdaa34ef42eb9b327a62f4e864c0
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365391"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Azure Cosmos DB yüksek kullanılabilirlik sağlama
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB, iki temel şekilde yüksek kullanılabilirlik sağlar. İlk olarak, Azure Cosmos DB verileri Cosmos hesabı içinde yapılandırılan bölgelerde çoğaltır. İkincisi, Azure Cosmos DB bir bölgedeki verilerin 4 çoğaltmasını korur.

Azure Cosmos DB, küresel olarak dağıtılmış bir veritabanı hizmetidir ve [Azure 'un kullanılabilir olduğu tüm bölgelerde](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)kullanılabilir bir temel hizmettir. Azure Cosmos hesabınızla istediğiniz sayıda Azure bölgesini ilişkilendirebilirsiniz ve verileriniz otomatik olarak ve saydam olarak çoğaltılır. İstediğiniz zaman Azure Cosmos hesabınıza bir bölge ekleyebilir veya kaldırabilirsiniz. Cosmos DB, müşteriler tarafından kullanılabilen beş farklı Azure bulut ortamında kullanılabilir:

* Genel olarak kullanılabilen **Azure genel** bulutu.

* **Azure Çin 21Vianet** , Microsoft ve 21Vianet arasındaki, ülkenin en büyük İnternet sağlayıcılarından biri Çin 'deki benzersiz bir iş ortaklığı aracılığıyla sunulmaktadır.

* **Azure Almanya** , bir veri güvenliği modeli altında hizmetler sağlar ve bu da, Almanya veri güvenliği olarak davranan, Deutsche Telekod 'nin bir yan kuruluşu olan T-Systems International GmbH denetimi altındaki Almanya 'da kalmaya devam eder.

* **Azure Kamu** , ABD kamu kurumlarına ve iş ortaklarına Birleşik Devletler dört bölgede kullanılabilir.

* **Savunma Bakanlığı (DOD) Için Azure Kamu** , ABD savunma Bakanlığı Birleşik Devletler iki bölgede sunulmaktadır.

Bir bölge içinde Azure Cosmos DB, aşağıdaki görüntüde gösterildiği gibi verilerinizin fiziksel bölümlere çoğaltma olarak dört kopyasını tutar:

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="Fiziksel bölümlendirme" border="false":::

* Azure Cosmos kapsayıcıları içindeki veriler [yatay olarak bölümlenmiştir](partitioning-overview.md).

* Bölüm kümesi, birden çok çoğaltma kümesi koleksiyonudur. Her bölge içinde, her bölüm çoğaltılan ve çoğalan çoğalma işlemi tarafından kaydedilmiş tüm yazmaları olan bir çoğaltma kümesi tarafından korunur. Çoğaltmalar, çok sayıda 10-20 hata etki alanı üzerinden dağıtılır.

* Tüm bölgelerde her bölüm çoğaltılır. Her bölge, bir Azure Cosmos kapsayıcısının tüm veri bölümlerini içerir ve çok bölgeli yazma özelliği etkinken yazma işlemleri yapmak için okuma ve yazma işlemleri sunabilir.  

Azure Cosmos hesabınız *N* Azure bölgelerinde dağıtılırsa, tüm verilerinizin en az *n* x 4 kopyası olacaktır. 2 ' den fazla bölgede bir Azure Cosmos hesabı olması, uygulamanızın kullanılabilirliğini iyileştirir ve ilişkili bölgeler arasında düşük gecikme süresi sağlar.

## <a name="slas-for-availability"></a>Kullanılabilirlik için SLA 'Lar

Azure Cosmos DB, üretilen iş miktarını, 99 ' luk yüzdede gecikme süresini, tutarlılığı ve yüksek kullanılabilirliği çevreleyen kapsamlı SLA 'Lar sağlar. Aşağıdaki tabloda, tek ve çok bölgeli hesaplar için Azure Cosmos DB tarafından sunulan yüksek kullanılabilirlik garantisi gösterilmektedir. Daha yüksek yazma kullanılabilirliği için Azure Cosmos hesabınızı birden fazla yazma bölgesi olacak şekilde yapılandırın.

|İşlem türü  | Tek bölge |Çok bölgeli (tek bölgeli yazma)|Çok bölgeli (çok bölgeli yazma) |
|---------|---------|---------|-------|
|Yazmalar    | 99,99    |99,99   |99,999|
|Okumalar     | 99,99    |99,999  |99,999|

> [!NOTE]
> Uygulamada, sınırlı Eskime durumu, oturum, tutarlı ön ek ve nihai tutarlılık modelleriyle ilgili gerçek yazma kullanılabilirliği, yayımlanan SLA 'Lara göre önemli ölçüde daha yüksektir. Tüm tutarlılık seviyelerinin gerçek okuma kullanılabilirliği, yayımlanan SLA 'ların önemli ölçüde daha yüksektir.

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>Bölgesel kesintiler durumunda Azure Cosmos DB ile yüksek kullanılabilirlik

Bölgesel kesintiden nadir durumlar için Azure Cosmos DB veritabanınızın her zaman yüksek oranda kullanılabilir olduğundan emin olur. Aşağıdaki ayrıntılar, Azure Cosmos hesap yapılandırmanıza bağlı olarak kesinti sırasında Azure Cosmos DB davranışını yakalar:

* Azure Cosmos DB, istemciye bir yazma işlemi alınmadan önce, veriler yazma işlemlerini kabul eden bölge içindeki çoğaltmaların bir çekirdeği tarafından işlenir. Daha ayrıntılı bilgi için bkz. [tutarlılık düzeyleri ve aktarım hızı](./consistency-levels.md#consistency-levels-and-throughput)

* Birden fazla yazma bölgesiyle yapılandırılan çok bölgeli hesaplar, yazma ve okuma işlemleri için yüksek oranda kullanılabilir olacaktır. Bölgesel yük devretme işlemleri Azure Cosmos DB istemcisinde algılanır ve işlenir. Bunlar da anlık ve uygulamadan herhangi bir değişiklik gerektirmez.

* Tek bölgeli hesaplar, bölgesel kesintiden sonraki kullanılabilirliği kaybedebilir. Her zaman yüksek kullanılabilirlik sağlamak için Azure Cosmos hesabınızla **en az iki bölge** (tercihen, en az iki yazma bölgesi) ayarlamanız önerilir.

> [!IMPORTANT]
> SQL API 'Lerini kullanırken, daha fazla kullanılabilirliği artırmak için Cosmos DB SDK 'sını belirtilen tüm okuma bölgelerini kullanacak şekilde yapılandırmak gerekir. Daha fazla bilgi için [Bu makaleye](troubleshoot-sdk-availability.md) bakın.

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>Tek bir yazma bölgesi olan çok bölgeli hesaplar (bölge kesintisi yazma)

* Yazma bölgesi kesintisi sırasında, Azure Cosmos hesabı, Azure Cosmos hesabında **otomatik yük devretmeyi etkinleştir** seçeneği yapılandırıldığında, ikincil bölgeyi otomatik olarak yeni birincil yazma bölgesi olacak şekilde yükseltir. Etkinleştirildiğinde, yük devretme, belirlediğiniz bölge önceliği sırasına göre başka bir bölgeye yapılır.

* El ile yük devretmenin tetiklenmeyeceğini ve kaynak veya hedef bölgenin bir kesinti olması durumunda başarısız olacağını unutmayın. Bu, bölgeler arasında bağlantı gerektiren yük devretme yordamının gerektirdiği bir tutarlılık denetimi nedeniyle oluşur.

* Daha önce etkilenen bölge yeniden çevrimiçi olduğunda, bölge başarısız olduğunda çoğaltılmamış olan tüm yazma verileri, [Çakışma akışı](how-to-manage-conflicts.md#read-from-conflict-feed)aracılığıyla kullanılabilir hale getirilir. Uygulamalar, çakışmalar akışını okuyabilir, uygulamaya özgü mantığa göre çakışmaları çözümleyebilir ve güncelleştirilmiş verileri uygun şekilde Azure Cosmos kapsayıcısına geri yazabilir.

* Daha önce etkilenen yazma bölgesi kurtarıldıktan sonra otomatik olarak bir okuma bölgesi olarak kullanılabilir hale gelir. Kurtarılan bölgeye yazma bölgesi olarak dönebilirsiniz. [PowerShell, Azure CLI veya Azure Portal](how-to-manage-database-account.md#manual-failover)kullanarak bölgeleri değiştirebilirsiniz. Bir **veri veya kullanılabilirlik kaybı** , yazma bölgesini değiştirmeden veya sonra, uygulamanız yüksek oranda kullanılabilir olmaya devam eder.

> [!IMPORTANT]
> **Otomatik yük devretmeyi etkinleştirmek** için üretim iş yükleri Için kullanılan Azure Cosmos hesaplarını yapılandırmanız önemle tavsiye edilir. Bu, Cosmos DB hesap veritabanlarının otomatik olarak availabile bölgelerine yük devretmesini sağlar. Bu yapılandırma yokluğunda, bölge bağlantısı olmaması nedeniyle el ile yük devretme başarısız olacağından, hesap, yazma bölgesi kesintisine ilişkin tüm süre için yazma kullanılabilirliği kaybıyla karşılaşacaktır.

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>Tek bir yazma bölgesi olan çok bölgeli hesaplar (okuma bölgesi kesintisi)

* Bir okuma bölgesi kesintisi sırasında, herhangi bir tutarlılık düzeyi veya üç veya daha fazla okuma bölgesi ile güçlü tutarlılık kullanan Azure Cosmos hesapları, okuma ve yazma işlemleri için yüksek oranda kullanılabilir olmaya devam edecektir.

* Üç bölgeyle (bir yazma, iki okuma) güçlü tutarlılık kullanan Azure Cosmos hesapları, okuma bölgesi kesintisi sırasında yazma kullanılabilirliğini korur. İki bölgeye ve otomatik yük devretmeye sahip hesaplarda, bölge başarısız olarak işaretlenene ve otomatik yük devretme gerçekleşene kadar, hesap yazmaları kabul etmeyi durdurur.

* Etkilenen bölgenin bağlantısı otomatik olarak kesilir ve çevrimdışı olarak işaretlenir. [Azure Cosmos DB SDK 'ları](sql-api-sdk-dotnet.md) , okuma çağrılarını tercih edilen bölge listesindeki bir sonraki kullanılabilir bölgeye yönlendirir.

* Tercih edilen bölge listesindeki bölgelerin hiçbiri kullanılabilir durumda değilse çağrılar otomatik olarak geçerli yazma bölgesine döner.

* Okuma bölgesi kesintisi 'nı işlemek için uygulama kodunuzda değişiklik yapılması gerekmez. Etkilenen okuma bölgesi yeniden çevrimiçi olduğunda, otomatik olarak geçerli yazma bölgesiyle eşitlenir ve okuma isteklerine hizmeti sağlamak için yeniden kullanılabilir hale gelir.

* Sonraki okumalar kurtarılan bölgeye yönlendirilir ve bunun için uygulamanızın kodunda değişiklik yapılması gerekmez. Daha önce başarısız olan bir bölgenin yük devretmesi ve yeniden katılması sırasında, uyumluluk garantisi Azure Cosmos DB tarafından kabul edilir.

* Azure bölgesinin kalıcı olarak kurtarılabilir olduğu nadir ve talihsiz olayında bile, çok bölgeli Azure Cosmos hesabınız *güçlü* tutarlılık ile yapılandırıldıysa veri kaybı olmaz. Kalıcı olarak kurtarılabilen bir yazma bölgesi olan, sınırlı hacme tutarlılığı ile yapılandırılmış çok bölgeli bir Azure Cosmos hesabı olan, olası veri kaybı penceresi, K = 100000 güncelleştirme veya T = 5 dakika olan (*k* veya *t*), ilk olarak gerçekleştikleri durumlar penceresiyle kısıtlıdır. Oturum, tutarlı ön ek ve nihai tutarlılık seviyeleri için, olası veri kaybı penceresi en fazla 15 dakika sınırlı olur. Azure Cosmos DB için RTO ve RPO hedefleri hakkında daha fazla bilgi için bkz. [tutarlılık düzeyleri ve veri dayanıklılığı](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>Kullanılabilirlik alanı desteği

Azure Cosmos DB çapraz bölge dayanıklılığına ek olarak, Azure Cosmos hesabınızla ilişkilendirilecek bölgeyi seçerken desteklenen bölgelerde **bölge yedekliliği** de destekler.

Kullanılabilirlik bölgesi (AZ) desteğiyle Azure Cosmos DB, çoğaltmaların belirli bir bölgedeki birden çok bölgeye yerleştirilmesi ve bu da hatalara karşı yüksek kullanılabilirlik ve dayanıklılık sağlanması gerekir. Kullanılabilirlik Alanları, gecikme süresine hiçbir değişiklik yapmadan% 99,995 kullanılabilirlik SLA 'Sı sağlar. Tek bir bölge hatası durumunda, bölge artıklığı RPO = 0 ve RTO = 0 ile kullanılabilirlik için tam veri dayanıklılığı sağlar. Bölge artıklığı, bölgesel çoğaltmaya ek bir özelliktir. Bölgesel dayanıklılık elde etmek için tek başına bölge artıklığı güvenlenemez.

Bölge artıklığı yalnızca, bir Azure Cosmos hesabına yeni bir bölge eklenirken yapılandırılabilir. Mevcut bölgelerde bölge yedekliliği, bölgeyi kaldırarak ve bölge yedekliği etkin olarak geri eklenerek etkinleştirilebilir. Tek bir bölge hesabında, bu, geçici olarak yük devretmeye, sonra da bölge yedekliği etkinken istenen bölgenin kaldırılmasına ve eklenmesine ek bir bölge eklenmesini gerektirir.

Azure Cosmos hesabınız için çok bölgeli yazma yapılandırırken, ek ücret ödemeden bölge yedekliliği seçebilirsiniz. Aksi takdirde, lütfen bölge artıklığı desteğinin fiyatlandırmasıyla ilgili olarak aşağıdaki tabloya bakın. Kullanılabilirlik bölgelerinin kullanılabildiği bölgelerin listesi için bkz. [kullanılabilirlik alanları](../availability-zones/az-region.md).

Aşağıdaki tabloda çeşitli hesap yapılandırmalarının yüksek kullanılabilirlik özelliği özetlenmektedir:

|KPI|AZs olmadan tek bölge|AZs ile tek bölge|AZs ile çok bölgeli, tek bölgeli yazma|AZs ile çok bölgeli, çok bölgeli yazma|
|---------|---------|---------|---------|---------|
|Kullanılabilirlik SLA 'Sı yaz | %99,99 | % 99,995 | % 99,995 | %99,999 |
|Kullanılabilirlik SLA 'sını oku  | %99,99 | % 99,995 | % 99,995 | %99,999 |
|Bölge arızaları – veri kaybı | Veri kaybı | Veri kaybı yok | Veri kaybı yok | Veri kaybı yok |
|Bölge arızaları – kullanılabilirlik | Kullanılabilirlik kaybı | Kullanılabilirlik kaybı yok | Kullanılabilirlik kaybı yok | Kullanılabilirlik kaybı yok |
|Bölgesel kesinti – veri kaybı | Veri kaybı |  Veri kaybı | Tutarlılık düzeyine bağımlıdır. Daha fazla bilgi için bkz. [tutarlılık, kullanılabilirlik ve performans avantajları](./consistency-levels.md) . | Tutarlılık düzeyine bağımlıdır. Daha fazla bilgi için bkz. [tutarlılık, kullanılabilirlik ve performans avantajları](./consistency-levels.md) .
|Bölgesel kesinti – kullanılabilirlik | Kullanılabilirlik kaybı | Kullanılabilirlik kaybı | Okuma bölgesi hatası için bir kullanılabilirlik kaybı yok, yazma bölgesi için geçici hata | Kullanılabilirlik kaybı yok |
|Fiyat (***1** _) | Yok | Sağlanan RU/s x 1,25 oranı | Sağlanan RU/s x 1,25 oranı (_ *_2_* *) | Çok bölgeli yazma oranı |

Sunucusuz hesaplar istek birimleri (RU) için ***1*** , 1,25 faktörüyle çarpılarak çarpılır.

***2*** 1,25 oran yalnızca az etkin olan bu bölgelere uygulanabilir.

Kullanılabilirlik Alanları şu şekilde etkinleştirilebilir:

* [Azure portalındaki](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager şablonları](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>Yüksek oranda kullanılabilir uygulamalar oluşturma

* Bu olaylar sırasında [Azure Cosmos SDK](troubleshoot-sdk-availability.md) 'larının beklenen davranışını gözden geçirin ve bunu etkileyen yapılandırma vardır.

* Yüksek yazma ve okuma kullanılabilirliği sağlamak için Azure Cosmos hesabınızı, birden çok yazma bölgesi ile en az iki bölgeye yaymak üzere yapılandırın. Bu yapılandırma, SLA 'Lar tarafından desteklenen hem okuma hem de yazma işlemleri için en yüksek kullanılabilirlik, en düşük gecikme süresi ve en iyi ölçeklenebilirlik sağlar. Daha fazla bilgi edinmek için bkz. [Azure Cosmos hesabınızı birden çok yazma bölgesi ile yapılandırma](tutorial-global-distribution-sql-api.md).

* Tek bir yazma bölgesiyle yapılandırılan çok bölgeli Azure Cosmos hesapları için, [Azure CLI veya Azure Portal kullanarak otomatik yük devretmeyi etkinleştirin](how-to-manage-database-account.md#automatic-failover). Otomatik yük devretmeyi etkinleştirdikten sonra, bölgesel bir olağanüstü durum olduğunda Cosmos DB hesabınıza otomatik olarak yük devreder.  

* Azure Cosmos hesabınız yüksek oranda kullanılabilir olsa bile, uygulamanız yüksek oranda kullanılabilir kalacak şekilde doğru şekilde tasarlanmayabilir. Uygulama test veya olağanüstü durum kurtarma (DR) detaylarının bir parçası olarak uygulamanızın uçtan uca yüksek kullanılabilirliğini test etmek için, hesapta otomatik yük devretmeyi geçici olarak devre dışı bırakın, [PowerShell, Azure CLI veya Azure Portal kullanarak el ile yük devretmeyi](how-to-manage-database-account.md#manual-failover)çağırın, sonra uygulamanızın yük devretmesini izleyin. Tamamlandıktan sonra, birincil bölgeye yeniden yük devreder ve hesap için otomatik yük devretmeyi geri yükleyebilirsiniz.

> [!IMPORTANT]
> Kaynak veya hedef bölgelerde Cosmos DB kesintisi sırasında el ile yük devretmeyi çağırmayın, çünkü veri tutarlılığını korumak için bölgeler bağlantısı gerekir ve başarılı olmaz.

* Küresel olarak dağıtılmış bir veritabanı ortamında, bölge genelinde bir kesinti olması durumunda tutarlılık düzeyi ve veri dayanıklılığı arasında doğrudan bir ilişki vardır. İş sürekliliği planınızı geliştirirken, kesintiye uğratan bir olaydan sonra uygulamanın tam olarak kurtarmadan önce kabul edilebilir en uzun süreyi anlamanız gerekir. Uygulamanın tam olarak kurtarılması için gereken süre, kurtarma zamanı hedefi (RTO) olarak bilinir. Ayrıca, uygulamanın, kesintiye uğratan bir olaydan sonra kurtarılırken kabul edebildiği en son veri güncelleştirme süresini de anlamanız gerekir. Kaybetmeyi göze alabileceğiniz güncelleştirme süresi kurtarma noktası hedefini (RPO) olarak bilinir. RPO ve RTO Azure Cosmos DB için bkz. [tutarlılık düzeyleri ve veri dayanıklılığı](./consistency-levels.md#rto)

## <a name="what-to-expect-during-a-cosmos-db-region-outage"></a>Cosmos DB bölge kesintisi sırasında beklenmeniz gerekenler

Tek bölgeli hesaplar için istemciler okuma ve yazma kullanılabilirliği kaybına neden olur.

Çok bölgeli hesaplar aşağıdaki tabloya bağlı olarak farklı davranışlarla karşılaşacaktır.

| Yazma bölgeleri | Otomatik yük devretme | Beklentiler | Ne yapılmalı |
| -- | -- | -- | -- |
| Tek bir yazma bölgesi | Etkin değil | Okuma bölgesinde kesinti olması durumunda tüm istemciler diğer bölgelere yeniden yönlendirilir. Okuma veya yazma kullanılabilirlik kaybı yok. Veri kaybı yok. <p/> Yazma bölgesinde bir kesinti olması durumunda, istemciler yazma kullanılabilirliği kaybıyla karşılaşacaktır. Güçlü tutarlılık düzeyi seçilmezse, bazı veriler kalan etkin bölgelere çoğaltılmayabilir. Bu, [Bu bölümde](consistency-levels.md#rto)açıklandığı gibi seçili olan tutarlılık düzeyine bağlıdır. Etkilenen bölge kalıcı veri kaybını içeriyorsa, çoğaltılan veriler kaybolabilir. <p/> Cosmos DB, kesinti sona erdiğinde otomatik olarak yazma kullanılabilirliği geri yüklenir. | Kesinti sırasında, kalan bölgelerde okuma trafiğini desteklemek için yeterli sayıda sağlanan RUs olduğundan emin olun. <p/> Kesinti sırasında el ile yük devretmeyi tetiklemeyin, çünkü *başarılı olmayacaktır.* <p/> Kesinti tamamlandığında, sağlanan RUs 'yi uygun şekilde yeniden ayarlayın. |
| Tek bir yazma bölgesi | Etkin | Okuma bölgesinde kesinti olması durumunda tüm istemciler diğer bölgelere yeniden yönlendirilir. Okuma veya yazma kullanılabilirlik kaybı yok. Veri kaybı yok. <p/> Yazma bölgesinde bir kesinti olması durumunda, istemciler yeni bir bölgeyi tercihlerinize göre yeni yazma bölgesi olarak otomatik olarak eleyenene kadar Cosmos DB, yazma kullanılabilirlik kaybına neden olur. Güçlü tutarlılık düzeyi seçilmezse, bazı veriler kalan etkin bölgelere çoğaltılmayabilir. Bu, [Bu bölümde](consistency-levels.md#rto)açıklandığı gibi seçili olan tutarlılık düzeyine bağlıdır. Etkilenen bölge kalıcı veri kaybını içeriyorsa, çoğaltılan veriler kaybolabilir. | Kesinti sırasında, kalan bölgelerde okuma trafiğini desteklemek için yeterli sayıda sağlanan RUs olduğundan emin olun. <p/> Kesinti sırasında el ile yük devretmeyi tetiklemeyin, çünkü *başarılı olmayacaktır.* <p/> Kesinti üzerindeyken, yazma bölgesini özgün bölgeye geri taşıyabilir ve sağlanan RUs 'yi uygun şekilde yeniden ayarlayabilirsiniz. SQL API 'Leri kullanan hesaplar, [Çakışma akışınızdan](how-to-manage-conflicts.md#read-from-conflict-feed)başarısız olan bölgedeki çoğaltılmayan verileri de kurtarabilir. |
| Birden çok yazma bölgesi | Uygulanamaz | Okuma veya yazma kullanılabilirlik kaybı yok. <p/> Hatalı bölgedeki son güncellenen veriler, kalan etkin bölgelerde korumasız olabilir. Nihai, tutarlı ön ek ve oturum tutarlılığı düzeyleri, <15dakikalık bir şekilde garanti garantisi. Sınırlanmış Eskime durumu, yapılandırmaya bağlı olarak, K güncelleştirme veya T saniyeden daha azını garanti eder. Etkilenen bölge kalıcı veri kaybını içeriyorsa, çoğaltılan veriler kaybolabilir. | Kesinti sırasında, ek trafiği desteklemek için kalan bölgelerde yeterli sayıda sağlanan RUs bulunduğundan emin olun. <p/> Kesinti tamamlandığında, sağlanan RUs 'yi uygun şekilde yeniden ayarlayabilirsiniz. Mümkünse Cosmos DB, SQL API hesapları için yapılandırılmış çakışma çözümü yöntemini kullanarak, başarısız olan bölgedeki çoğaltılmamış verileri otomatik olarak kurtarabilir ve diğer API 'Leri kullanarak hesaplar için WINS 'i son kez yazar. |

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra aşağıdaki makaleleri okuyabilirsiniz:

* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans avantajları](./consistency-levels.md)

* [Sağlanan aktarım hızını küresel olarak ölçeklendirme](./request-units.md)

* [Genel dağıtım - başlık altında](global-dist-under-the-hood.md)

* [Azure Cosmos DB'deki tutarlılık düzeyleri](consistency-levels.md)

* [Cosmos hesabınızı birden çok yazma bölgesi ile yapılandırma](how-to-multi-master.md)

* [Çoklu bölgesel ortamlarda SDK davranışı](troubleshoot-sdk-availability.md)