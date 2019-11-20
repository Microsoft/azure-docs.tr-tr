---
title: Azure Cosmos DB .NET SDK kullanırken sorunları tanılama ve giderme
description: .NET SDK kullanırken Azure Cosmos DB sorunları tanımlamak, tanılamak ve sorunlarını gidermek için istemci tarafı günlüğe kaydetme gibi özellikleri ve diğer üçüncü taraf araçları kullanın.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 51b37c43b94ad59090f32af0d57bbefaa57f30fa
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932549"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK kullanırken sorunları tanılama ve giderme
Bu makalede, Azure Cosmos DB SQL API hesaplarıyla [.NET SDK](sql-api-sdk-dotnet.md) kullandığınızda yaygın sorunlar, geçici çözümler, Tanılama adımları ve araçlar ele alınmaktadır.
.NET SDK, Azure Cosmos DB SQL API 'sine erişmek için istemci tarafı mantıksal temsili sağlar. Bu makalede, herhangi bir sorunla karşılaşırsanız size yardımcı olacak araçlar ve yaklaşımlar açıklanmaktadır.

## <a name="checklist-for-troubleshooting-issues"></a>Sorun giderme sorunları için denetim listesi:
Uygulamanızı üretime taşımadan önce aşağıdaki denetim listesini göz önünde bulundurun. Denetim listesinin kullanılması, görebileceğiniz bazı yaygın sorunları engeller. Ayrıca bir sorun oluştuğunda hızlı bir şekilde tanılama yapabilirsiniz:

*   En son [SDK 'yı](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)kullanın. Önizleme SDK 'Ları üretim için kullanılmamalıdır. Bu, zaten düzeltilen bilinen sorunların vurmasını engeller.
*   [Performans ipuçlarını](performance-tips.md)gözden geçirin ve önerilen uygulamaları izleyin. Bu, ölçeklendirmeyi, gecikmeyi ve diğer performans sorunlarını önlemeye yardımcı olur.
*   Bir sorunu gidermenize yardımcı olması için SDK günlüğünü etkinleştirin. Günlüğe kaydetme işleminin etkinleştirilmesi performansı etkileyebilir, bu sayede yalnızca sorun giderme sırasında etkinleştirmek en iyisidir. Aşağıdaki günlükleri etkinleştirebilirsiniz:
    *   Azure portal kullanarak [ölçümleri günlüğe kaydedin](monitor-accounts.md) . Portal ölçümleri, sorunun Azure Cosmos DB veya istemci tarafından olup olmadığını belirlemede faydalı olan Azure Cosmos DB Telemetriyi gösterir.
    *   [Tanılama dizesini](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) nokta işlem yanıtlarından günlüğe kaydedin.
    *   Tüm Sorgu yanıtlarından [SQL sorgu ölçümlerini](sql-api-query-metrics.md) günlüğe kaydet 
    *   [SDK günlüğü]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) kurulumunu izleyin

Bu makaledeki [yaygın sorunlar ve geçici çözümler](#common-issues-workarounds) bölümüne göz atın.

Etkin olarak izlenen [GitHub sorunları bölümüne](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) bakın. Geçici bir çözüm ile benzer bir sorun olup olmadığını denetleyin. Bir çözüm bulamıyorsanız bir GitHub sorunu verirsiniz. Acil sorunlar için bir destek çizgisi açabilirsiniz.


## <a name="common-issues-workarounds"></a>Yaygın sorunlar ve geçici çözümler

### <a name="general-suggestions"></a>Genel öneriler
* Uygulamanızı, mümkün olduğunda Azure Cosmos DB hesabınızla aynı Azure bölgesinde çalıştırın. 
* İstemci makinenizde kaynak olmaması nedeniyle bağlantı/kullanılabilirlik sorunlarıyla karşılaşabilirsiniz. Azure Cosmos DB istemcisini çalıştıran düğümlerde CPU kullanımınızı izlemenizi ve yüksek yük üzerinde çalışıyorsa ölçeği büyütmeyi/dışarı izlemeyi öneririz.

### <a name="check-the-portal-metrics"></a>Portal ölçümlerini denetleyin
Denetimi [portal ölçümleri](monitor-accounts.md) sorun istemci tarafından ise veya hizmeti ile bir sorun olduğunda belirlemenize yardımcı olur. Örneğin ölçümleri oranı sınırlı istekleri (HTTP durum kodu 429) yüksek oranda içerip içermediğini istek kısıtlandı anlamına sonra iade [istek oranı çok büyük] bölümü. 

### <a name="request-timeouts"></a>İstek zaman aşımı
RequestTimeout genellikle doğrudan/TCP kullanılırken gerçekleşir, ancak ağ geçidi modunda gerçekleşebilir. Bunlar yaygın olarak bilinen nedenlerdir ve sorunun nasıl düzeltileceğini gösteren önerilerdir.

* CPU kullanımı yüksek, gecikme süresi ve/veya istek zaman aşımına neden olur. Müşteri, daha fazla kaynak vermek için konak makinenin ölçeğini değiştirebilir veya yük daha fazla makineye dağıtılabilir.
* Yuva/bağlantı noktası kullanılabilirliği düşük olabilir. Azure 'da çalışırken, .NET SDK kullanan istemciler Azure SNAT (PAT) bağlantı noktası tükenmesi ile aynı olabilir. Bu sorunu verme olasılığını azaltmak için .NET SDK 'sının en son sürüm 2. x veya 3. x sürümünü kullanın. Bu, her zaman en son SDK sürümünü çalıştırmanın nasıl önerildığına bir örnektir.
* Birden çok DocumentClient örneği oluşturmak bağlantı çekişmesine ve zaman aşımı sorunlarına yol açabilir. [Performans ipuçlarını](performance-tips.md)izleyin ve bir işlemin tamamında tek bir documentclient örneği kullanın.
* Koleksiyonlar her zaman zaman aşımına uğruyor, arka uç istekleri ve istemci yeniden denemeleri bu çağrı yapana bağlı olmadan bir kez daha yeniden denemeler yaptığından, yükseltilmiş gecikme süresi veya istek zaman aşımları görebilirler. [Portal ölçümlerini](monitor-accounts.md)denetleyin.
* Azure Cosmos DB, genel olarak sağlanan üretilen işi fiziksel bölümler arasında eşit olarak dağıtır. İş yükünün bir sıcak [bölüm anahtarıyla](partition-data.md)karşılaşmaya yönelik olup olmadığını görmek için Portal ölçümlerini denetleyin. Bu, toplam tüketilen üretilen iş üretiminin (RU/s), sağlanan RUs kapsamında görünmesine neden olur, ancak tek bölümlü tüketilen üretilen iş hacmi (RU/s), sağlanan aktarım hızını aşacak. 
* Ayrıca, 2,0 SDK, kanal semantiğini doğrudan/TCP bağlantılarına ekler. Aynı anda birden çok istek için bir TCP bağlantısı kullanılır. Bu, belirli durumlarda iki soruna neden olabilir:
    * Yüksek derecede eşzamanlılık, kanalda çekişmeye neden olabilir.
    * Büyük istekler veya yanıtlar, görece düşük eşzamanlılık derecesine sahip olan kanal ve exacerbate çekişmesi üzerinde satır başı engellemeye yol açabilir.
    * Örnek, bu iki kategorinin herhangi birine denk geliyorsa (veya yüksek CPU kullanımı şüpheli ise), bunlar olası azaltmalar olur:
        * Uygulamayı ölçeklendirmeye/dışarı ölçeklendirmeye çalışın.
        * Ayrıca, daha fazla ayrıntı edinmek için SDK günlükleri [Izleme dinleyicisi](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) aracılığıyla yakalanabilir.

### <a name="connection-throttling"></a>Bağlantı azaltma
Bir ana makinedeki bağlantı sınırı nedeniyle bağlantı azaltma meydana gelebilir. 2,0 ' den önceki Azure 'da çalışan istemciler, [Azure SNAT (Pat) bağlantı noktası tükenmesi]' ne ulaşgeçirebilir.

### <a name="snat"></a>Azure SNAT (PAT) bağlantı noktası tükenmesi

Uygulamanız Azure sanal makinelerinde genel IP adresi olmadan dağıtılmışsa, varsayılan olarak [Azure SNAT bağlantı noktaları](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) , sanal makinenizin dışındaki herhangi bir uç noktaya bağlantı kurar. VM 'den Azure Cosmos DB uç noktasına izin verilen bağlantı sayısı [Azure SNAT yapılandırması](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)ile sınırlıdır.

 Azure SNAT bağlantı noktaları yalnızca sanal makinenizin özel bir IP adresi varsa ve VM 'den bir işlem ortak IP adresine bağlanmaya çalıştığında kullanılır. Azure SNAT sınırlamasından kaçınmak için iki geçici çözüm vardır:

* Azure Cosmos DB hizmeti uç noktanızı Azure sanal makineler sanal ağınızın alt ağına ekleyin. Daha fazla bilgi için bkz. [Azure sanal ağ hizmeti uç noktaları](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Hizmet uç noktası etkinleştirildiğinde, istekler artık genel bir IP Azure Cosmos DB ' e gönderilmez. Bunun yerine, sanal ağ ve alt ağ kimliği gönderilir. Yalnızca genel IP 'Lere izin veriliyorsa bu değişiklik güvenlik duvarı düşceye neden olabilir. Bir güvenlik duvarı kullanıyorsanız, hizmet uç noktasını etkinleştirdiğinizde, [sanal ağ ACL 'lerini](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl)kullanarak güvenlik duvarına bir alt ağ ekleyin.
* Azure sanal makinenize genel IP atayın.

### <a name="http-proxy"></a>HTTP proxy 'si
Bir HTTP proxy kullanıyorsanız, SDK `ConnectionPolicy`'da yapılandırılan bağlantı sayısını destekleyediğinden emin olun.
Aksi halde bağlantı sorunlarıyla karşılaşın.

### İstek hızı çok büyük<a name="request-rate-too-large"></a>
' İstek hızı çok büyük ' veya 429 hata kodu hatası, tüketilen üretilen iş (RU/s) sağlanan aktarım hızını aştığından isteklerin kısıtlandığını gösterir. SDK, belirtilen [yeniden deneme ilkesine](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)göre istekleri otomatik olarak yeniden dener. Bu hatayla sık sık karşılaşırsanız, koleksiyondaki aktarım hızını artırmayı düşünün. 429 hatası olup olmadığınızı görmek için [portalın ölçümlerini](use-metrics.md) denetleyin. Depolama ve istek hacminin eşit bir şekilde dağıtımına neden olduğundan emin olmak için [bölüm anahtarınızı](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) gözden geçirin. 

### <a name="slow-query-performance"></a>Yavaş sorgu performansı
Sorgu [ölçümleri](sql-api-query-metrics.md) , sorgunun en fazla zaman harcamanın nerede olduğunu belirlemenize yardımcı olur. Sorgu ölçümlerinde, istemcinin arka ucunda ne kadarının harcandığını görebilirsiniz.
* Arka uç sorgusu hızlı bir şekilde döndürürse ve istemci üzerinde büyük bir zaman harcadıysanız makinedeki yükü kontrol edin. Yeterli miktarda kaynak olmaması ve SDK 'nın yanıtı işlemek için kaynakların kullanılabilir olmasını bekliyor olması olasıdır.
* Arka uç sorgusu yavaşsa, [sorguyu iyileştirmeyi](optimize-cost-queries.md) ve geçerli [Dizin oluşturma ilkesine](index-overview.md) bakmaya çalışın 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[istek oranı çok büyük]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) bağlantı noktası tükenmesi]: #snat
[Production check list]: #production-check-list


