---
title: Azure Cosmos DB yüksek kullanılabilirlik
description: Bu makalede, Azure Cosmos DB yüksek kullanılabilirlik sağladığını açıklanmaktadır
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ab6544e4535f2d2c2e88284f61251f177d457a84
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146656"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Azure Cosmos DB ile yüksek kullanılabilirlik

Azure Cosmos DB, verilerinizi Cosmos hesabınızla ilişkili tüm Azure bölgelerinde saydam olarak çoğaltır. Cosmos DB, aşağıdaki görüntüde gösterildiği gibi verileriniz için birden çok yedeklik katmanı kullanır:

![Fiziksel bölümlendirme](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos kapsayıcılarındaki veriler [yatay olarak bölümlenmiştir](partitioning-overview.md).

- Her bölge içinde, her bölüm çoğaltılan ve çoğalan çoğalma işlemi tarafından kaydedilmiş tüm yazmaları olan bir çoğaltma kümesi tarafından korunur. Çoğaltmalar, çok sayıda 10-20 hata etki alanı üzerinden dağıtılır.

- Tüm bölgelerde her bölüm çoğaltılır. Her bölge, Cosmos kapsayıcısının tüm veri bölümlerini içerir ve yazma işlemlerini kabul edebilir ve okumaları sunabilir.  

Cosmos hesabınız *N* Azure bölgelerinde dağıtılırsa, tüm verilerinizin en az *n* x 4 kopyası olacaktır. Cosmos hesabınızla ilişkili bölgelerde düşük gecikmeli veri erişimi sağlamaya ve yazma/okuma aktarım hızını Ölçeklendirmeye ek olarak, daha fazla bölgeye sahip olma (daha yüksek *N*) kullanılabilirliği artırır.  

## <a name="slas-for-availability"></a>Kullanılabilirlik için SLA 'Lar

Küresel olarak dağıtılmış bir veritabanı olarak, Cosmos DB aktarım hızını çevreleyen kapsamlı SLA 'Lar, en fazla 99 ' luk bir gecikme süresi, tutarlılık ve yüksek kullanılabilirlik sağlar. Aşağıdaki tabloda, tek ve çok bölgeli hesaplar için Cosmos DB tarafından sunulan yüksek kullanılabilirlik garantisi gösterilmektedir. Yüksek kullanılabilirlik için, Cosmos hesaplarınızı her zaman birden fazla yazma bölgesi olacak şekilde yapılandırın.

|İşlem türü  | Tek bölge |Çok bölgeli (tek bölge yazmaları)|Çok bölgeli (çok bölgeli yazma) |
|---------|---------|---------|-------|
|Yazar    | 99,99    |99,99   |99,999|
|Okur     | 99,99    |99,999  |99,999|

> [!NOTE]
> Uygulamada, sınırlı Eskime durumu, oturum, tutarlı ön ek ve nihai tutarlılık modelleriyle ilgili gerçek yazma kullanılabilirliği, yayımlanan SLA 'Lara göre önemli ölçüde daha yüksektir. Tüm tutarlılık seviyelerinin gerçek okuma kullanılabilirliği, yayımlanan SLA 'ların önemli ölçüde daha yüksektir.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Bölgesel kesintiler durumunda Cosmos DB ile yüksek kullanılabilirlik

Bölgesel kesintiler oldukça yaygındır ve Azure Cosmos DB veritabanınızın her zaman yüksek kullanılabilirliğe sahip olduğundan emin olur. Aşağıdaki ayrıntılar, Cosmos hesap yapılandırmanıza bağlı olarak kesinti sırasında Cosmos DB davranışını yakalar:

- Cosmos DB, istemciye bir yazma işlemi alınmadan önce, veriler yazma işlemlerini kabul eden bölge içindeki çoğaltmaların bir çekirdeği tarafından işlenir.

- Birden fazla yazma bölgesiyle yapılandırılan çok bölgeli hesaplar, yazma ve okuma işlemleri için yüksek oranda kullanılabilir olacaktır. Bölgesel yük devretme işlemleri anında yapılır ve uygulamadan herhangi bir değişiklik gerektirmez.

- **Tek bir yazma bölgesi olan çok bölgeli hesaplar (yazma bölgesi kesintisi):** 
  * Yazma bölgesi kesintisi sırasında, bu hesaplar okuma için yüksek oranda kullanılabilir olarak kalır. Yazma isteklerinin başarılı olması için Azure Cosmos hesabınızda **otomatik yük devretmeyi etkinleştir** seçeneğini etkinleştirmeniz gerekir. Bu seçeneğin etkinleştirilmesi, etkilenen bölgenin, belirlediğiniz bölge önceliği sırasına göre başka bir bölgeye yük devretmesini sağlar. 
  * Daha önce etkilenen bölge yeniden çevrimiçi olduğunda, bölge başarısız olduğunda çoğaltılmamış olan tüm yazma verileri, [Çakışma akışı](how-to-manage-conflicts.md#read-from-conflict-feed)aracılığıyla kullanılabilir hale getirilir. Uygulamalar, çakışmalar akışını okuyabilir, uygulamaya özgü mantığa göre çakışmaları çözümleyebilir ve güncelleştirilmiş verileri uygun şekilde Azure Cosmos kapsayıcısına geri yazabilir. 
  * Daha önce etkilenen yazma bölgesi kurtarıldıktan sonra otomatik olarak bir okuma bölgesi olarak kullanılabilir hale gelir. Kurtarılan bölgeye yazma bölgesi olarak dönebilirsiniz. [Azure CLI veya Azure Portal](how-to-manage-database-account.md#manual-failover)kullanarak bölgeleri geçirebilirsiniz. Bir **veri veya kullanılabilirlik kaybı** , yazma bölgesini değiştirmeden veya sonra, uygulamanız yüksek oranda kullanılabilir olmaya devam eder. 

- **Tek bir yazma bölgesi olan çok bölgeli hesaplar (okuma bölgesi kesintisi):** 
  * Okuma bölgesi kesintisi sırasında, bu hesaplar okuma ve yazma işlemleri için yüksek oranda kullanılabilir olarak kalır. 
  * Etkilenen bölgenin otomatik olarak yazma bölgesi bağlantısı kesilir ve çevrimdışı olarak işaretlenir. [Azure Cosmos DB SDK 'ları](sql-api-sdk-dotnet.md) , okuma çağrılarını tercih edilen bölge listesindeki bir sonraki kullanılabilir bölgeye yönlendirir. 
  * Tercih edilen bölge listesindeki bölgelerin hiçbiri kullanılabilir değilse, çağrılar otomatik olarak geçerli yazma bölgesine geri döner. 
  * Okuma bölgesi kesintisi 'nı işlemek için uygulama kodunuzda değişiklik yapılması gerekmez. Sonuç olarak, etkilenen bölge yeniden çevrimiçi olduğunda, daha önce etkilenen okuma bölgesi otomatik olarak geçerli yazma bölgesiyle eşitlenir ve okuma isteklerine sunulacak şekilde yeniden kullanılabilir. 
  * Sonraki okumalar, uygulama kodunuzda herhangi bir değişiklik gerektirmeden kurtarılan bölgeye yönlendirilir. Daha önce başarısız olan bir bölgenin yük devretmesi ve yeniden katılması sırasında, uyumluluk garantisi Cosmos DB tarafından kabul edilir.

- Tek bölgeli hesaplar, bölgesel kesintiden sonraki kullanılabilirliği kaybedebilir. Her zaman yüksek kullanılabilirlik sağlamak için Cosmos hesabınızla **en az iki bölge** (tercihen, en az iki yazma bölgesi) ayarlamanız önerilir.

- Azure bölgesinin kalıcı olarak kurtarılabilir olduğu nadir ve talihsiz olayında bile, çok bölgeli Cosmos hesabınız varsayılan tutarlılık düzeyiyle yapılandırılmışsa veri kaybı *olmaz.* Kalıcı olarak kurtarılabilir bir yazma bölgesi durumunda, sınırlı stalet tutarlılığı ile yapılandırılan çok bölgeli Cosmos hesapları için, olası veri kaybı penceresi, stalet penceresiyle (*K* veya *T*) kısıtlıdır; oturum, tutarlı ön ek ve nihai tutarlılık seviyeleri için, olası veri kaybı penceresi en fazla beş saniyeye kısıtlıdır. 

## <a name="availability-zone-support"></a>Kullanılabilirlik alanı desteği

Azure Cosmos DB, bölgesel kesintiler sırasında yüksek kullanılabilirlik ve dayanıklılık sağlayan, küresel olarak dağıtılmış, çok yöneticili bir veritabanı hizmetidir. Çapraz bölge dayanıklılığına ek olarak, Azure Cosmos veritabanınız ile ilişkilendirilecek bölge seçerken **bölge yedekliliği** de etkinleştirebilirsiniz. 

Kullanılabilirlik alanı desteğiyle Azure Cosmos DB, çoğaltmaların belirli bir bölgedeki birden çok bölgeye yerleştirildiğinden emin olur ve bu da, en fazla başarısızlık sırasında yüksek kullanılabilirlik ve esneklik sağlar. Bu yapılandırmadaki gecikme ve diğer SLA 'Lara yönelik bir değişiklik yoktur. Tek bir bölge hatası durumunda, bölge artıklığı RPO = 0 ve RTO = 0 ile kullanılabilirlik için tam veri dayanıklılığı sağlar. 

Bölge artıklığı, [çok yöneticili çoğaltma](how-to-multi-master.md) özelliği için *ek bir özelliktir* . Bölgesel dayanıklılık sağlamak için alan yedekliliği tek başına yeterli değildir. Örneğin, bölgeler genelinde bölgesel kesintiler veya düşük gecikme erişimi durumunda, bölge yedekliliğe ek olarak birden fazla yazma bölgesi olması önerilir. 

Azure Cosmos hesabınız için çok bölgeli yazma yapılandırırken, ek ücret ödemeden bölge yedekliliği seçebilirsiniz. Aksi takdirde, lütfen bölge artıklığı desteğinin fiyatlandırmasıyla ilgili olarak aşağıdaki nota bakın. Azure Cosmos hesabınızın mevcut bir bölgesini kaldırarak ve alan yedekliliği etkin durumdayken tekrar ekleyerek bu bölgede alan yedekliliğini etkinleştirebilirsiniz.

Bu özellik aşağıdaki Azure bölgelerinde kullanılabilir:

* Birleşik Krallık Güney
* Güneydoğu Asya 
* East US
* Doğu ABD 2 
* Orta ABD
* Batı Avrupa
* Batı ABD 2

> [!NOTE] 
> Tek bir bölge için Kullanılabilirlik Alanları etkinleştirme Azure Cosmos hesabı, hesabınıza ek bir bölge eklemeye eşdeğer olan ücretlere neden olur. Fiyatlandırma hakkında ayrıntılı bilgi için, Azure Cosmos DB makalelerinde [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/cosmos-db/) ve [çok bölgeli maliyete](optimize-cost-regions.md) bakın. 

Aşağıdaki tabloda çeşitli hesap yapılandırmalarının yüksek kullanılabilirlik özelliği özetlenmektedir: 

|KPI  |Kullanılabilirlik Alanları olmayan tek bölge (AZ değil)  |Kullanılabilirlik Alanları tek bölge (AZ)  |Kullanılabilirlik Alanları (AZ, 2 bölge) ile çok bölgeli yazma: en önerilen ayar |
|---------|---------|---------|---------|
|Kullanılabilirlik SLA 'Sı yaz     |   %99,99      |    %99,99     |  99.999%  |
|Kullanılabilirlik SLA 'sını oku   |   %99,99      |   %99,99      |  99.999%       |
|Fiyat  |  Tek bölge faturalandırma oranı |  Tek bölge kullanılabilirlik alanı faturalandırma oranı |  Çok bölgeli fatura ücreti       |
|Bölge arızaları – veri kaybı   |  Veri kaybı  |   Veri kaybı yok |   Veri kaybı yok  |
|Bölge arızaları – kullanılabilirlik |  Kullanılabilirlik kaybı  | Kullanılabilirlik kaybı yok  |  Kullanılabilirlik kaybı yok  |
|Okuma gecikmesi    |  Çapraz bölge    |   Çapraz bölge   |    Düşük  |
|Yazma gecikme süresi    |   Çapraz bölge   |  Çapraz bölge    |   Düşük   |
|Bölgesel kesinti – veri kaybı    |   Veri kaybı      |  Veri kaybı       |   Veri kaybı <br/><br/> Birden çok ana ve birden fazla bölge ile sınırlı stalet tutarlılığı kullanılırken, veri kaybı, hesabınızda yapılandırılan sınırlı stalet ile sınırlıdır. <br/><br/> Birden çok bölgeyle güçlü tutarlılık yapılandırılarak, bölgesel kesinti sırasında veri kaybı önlenebilir. Bu seçenek, kullanılabilirliği ve performansı etkileyen tüm dengelerle birlikte gelir.      |
|Bölgesel kesinti – kullanılabilirlik  |  Kullanılabilirlik kaybı       |  Kullanılabilirlik kaybı       |  Kullanılabilirlik kaybı yok  |
|Aktarım hızı    |  X RU/sn sağlanan aktarım hızı      |  X RU/sn sağlanan aktarım hızı       |  2X/sn sağlanan aktarım hızı <br/><br/> İki bölge olduğundan, bu yapılandırma modu Kullanılabilirlik Alanları tek bir bölgeyle karşılaştırıldığında üretilen iş miktarı sayısını iki kez gerektirir.   |

> [!NOTE] 
> Çok bölgeli bir Azure Cosmos hesabı için kullanılabilirlik alanı desteğini etkinleştirmek üzere, hesabın çoklu yöneticili yazmaları etkinleştirilmiş olması gerekir.


Yeni veya mevcut Azure Cosmos hesaplarına bölge eklerken bölge yedekliliği etkinleştirebilirsiniz. Azure Cosmos hesabınızda bölge yedekliliği etkinleştirmek için `isZoneRedundant` `true` bayrağını belirli bir konum için ayarlamanız gerekir. Bu bayrağı konumlar özelliği içinde ayarlayabilirsiniz. Örneğin, aşağıdaki PowerShell kod parçacığı "Güneydoğu Asya" bölgesi için bölge yedekliliği sunar:

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

Aşağıdaki komut, "EastUS" ve "WestUS2" bölgeleri için bölge artıklığını nasıl etkinleştireceğinizi göstermektedir:

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True \
  --enable-multiple-write-locations
```

Azure Cosmos hesabı oluştururken Azure portal kullanarak Kullanılabilirlik Alanları etkinleştirebilirsiniz. Bir hesap oluşturduğunuzda, **coğrafi yedeklilik**, **çok bölgeli yazmaları**etkinleştirdiğinizden emin olun ve kullanılabilirlik alanları desteklendiği bir bölge seçin: 

![Azure portal kullanarak Kullanılabilirlik Alanları etkinleştirme](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Yüksek oranda kullanılabilir uygulamalar oluşturma

- Yüksek yazma ve okuma kullanılabilirliği sağlamak için Cosmos hesabınızı, birden çok yazma bölgesi ile en az iki bölgeye yaymak üzere yapılandırın. Bu yapılandırma, SLA 'Lar tarafından desteklenen hem okuma hem de yazma işlemleri için en yüksek kullanılabilirlik, en düşük gecikme süresi ve en iyi ölçeklenebilirlik sağlar. Daha fazla bilgi edinmek için bkz. [Cosmos hesabınızı birden çok yazma bölgesi ile yapılandırma](tutorial-global-distribution-sql-api.md).

- Tek bir yazma bölgesiyle yapılandırılan çok bölgeli Cosmos hesapları için, [Azure CLI veya Azure Portal kullanarak otomatik yük devretmeyi etkinleştirin](how-to-manage-database-account.md#automatic-failover). Otomatik yük devretmeyi etkinleştirdikten sonra, bölgesel bir olağanüstü durum olduğunda Cosmos DB hesabınıza otomatik olarak yük devreder.  

- Cosmos hesabınız yüksek oranda kullanılabilir olsa bile, uygulamanız yüksek oranda kullanılabilir kalacak şekilde doğru şekilde tasarlanmayabilir. Uygulamanızın uçtan uca yüksek kullanılabilirliğini test etmek için, uygulama testinizin veya olağanüstü durum kurtarma (DR) detaylarının bir parçası olarak [Azure CLI veya Azure Portal kullanarak el ile yük devretmeyi](how-to-manage-database-account.md#manual-failover)çağırın.

- Küresel olarak dağıtılmış bir veritabanı ortamında, bölge genelinde bir kesinti olması durumunda tutarlılık düzeyi ve veri dayanıklılığı arasında doğrudan bir ilişki vardır. İş sürekliliği planınızı geliştirirken, kesintiye uğratan bir olaydan sonra uygulamanın tam olarak kurtarmadan önce kabul edilebilir en uzun süreyi anlamanız gerekir. Uygulamanın tam olarak kurtarılması için gereken süre, kurtarma zamanı hedefi (RTO) olarak bilinir. Ayrıca, uygulamanın, kesintiye uğratan bir olaydan sonra kurtarılırken kabul edebildiği en son veri güncelleştirme süresini de anlamanız gerekir. Kaybetmeyi göze alabileceğiniz güncelleştirme süresi kurtarma noktası hedefini (RPO) olarak bilinir. RPO ve RTO Azure Cosmos DB için bkz. [tutarlılık düzeyleri ve veri dayanıklılığı](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra aşağıdaki makaleleri okuyabilirsiniz:

* [Çeşitli tutarlılık düzeyleri için kullanılabilirlik ve performans seçenekleri](consistency-levels-tradeoffs.md)
* [Sağlanan aktarım hızı genel olarak ölçeklendiriliyor](scaling-throughput.md)
* [Küresel dağıtım-alt düzey](global-dist-under-the-hood.md)
* [Azure Cosmos DB 'deki tutarlılık düzeyleri](consistency-levels.md)
* [Cosmos hesabınızı birden çok yazma bölgesi ile yapılandırma](how-to-multi-master.md)
