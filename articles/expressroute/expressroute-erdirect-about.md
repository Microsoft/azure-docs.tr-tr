---
title: Azure ExpressRoute Direct hakkında
description: Bu sayfada ExpressRoute Direct 'e genel bakış sunulmaktadır
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083598"
---
# <a name="about-expressroute-direct"></a>ExpressRoute Direct hakkında

ExpressRoute doğrudan, Microsoft'un küresel ağı dünya genelindeki stratejik dağıtılmış eşleme konumlarda doğrudan bağlanma özelliği sağlar. ExpressRoute Direct, ölçek sırasında etkin/etkin bağlantıyı destekleyen çift 100 Gbps veya 10 Gbps bağlantı sağlar.

ExpressRoute doğrudan sağlayan önemli özellikler dahil ancak bunlarla sınırlı değildir:

* Depolama ve Cosmos DB gibi hizmetler için Büyük Veri Alımı özelliği
* Fiziksel yalıtım düzenlenen ve gerektiren sektörler için adanmış ve yalıtılmış gibi bağlantı: bankacılık, kamu ve perakende
* Bağlantı hattı dağıtımının iş birimine dayalı detaylı denetimi

## <a name="onboard-to-expressroute-direct"></a>ExpressRoute Direct 'e ekleme

ExpressRoute Direct kullanmadan önce aboneliğinizi kaydetmeniz gerekir. Kaydetmek için bir e-posta Gönder <ExpressRouteDirect@microsoft.com> abonelik Kimliğinizi, aşağıdaki ayrıntılar dahil olmak üzere:

* Senaryo ile gerçekleştirmek için aradığınız **ExpressRoute doğrudan**
* Konum bkz - [iş ortakları ve eşleme konumları](expressroute-locations-providers.md) tüm konumlara tam listesi için
* Uygulama için zaman çizelgesi
* Diğer sorular

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute hizmet sağlayıcısı ve ExpressRoute doğrudan kullanma

| **ExpressRoute kullanarak bir hizmet sağlayıcısı** | **ExpressRoute doğrudan** | 
| --- | --- |
| Hızlı ekleme ve mevcut altyapısıyla bağlantısını etkinleştirmek için hizmet sağlayıcıları kullanır. | 100 Gbps/10 Gbps altyapısı ve tüm katmanların tam yönetimi gerekir
| Sağlayıcı Ethernet ve MPLS gibi yüzlerce ile tümleşir | Düzenlenen sektör ve büyük veri alımı için doğrudan/adanmış kapasite |
| 50 MB/sn devreler SKU'lardan 10 GB/sn | Müşteri, 100 Gbps ExpressRoute Direct üzerinde aşağıdaki devre SKU 'Larının birleşimini seçebilir: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Müşteri, 10 Gbps ExpressRoute Direct üzerinde aşağıdaki devre SKU 'Larının birleşimini seçebilir:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Tek bir kiracı için en iyi duruma getirilmiş | Birden çok iş birimi ve birden çok iş ortamı ile tek bir kiracı için iyileştirildi

## <a name="expressroute-direct-circuits"></a>Doğrudan ExpressRoute bağlantı hatları

Microsoft Azure ExpressRoute, bağlantı sağlayıcı tarafından kolaylaştırılan özel bağlantı üzerinden şirket içi ağlarınızı Microsoft bulutuna genişletmenizi sağlar. ExpressRoute ile Microsoft Azure ve Office 365 gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz.

Her eşleme konumunda, Microsoft'un genel ağ erişimi olan ve varsayılan olarak herhangi bir bölgede coğrafi bölge erişebilir ve tüm küresel bölgeye sahip premium devresi erişebilir.  

Çoğu senaryoda işlevselliğini çalışması için bir ExpressRoute hizmet sağlayıcısı'nı kullanan bağlantı hatlarına eşdeğerdir. Daha fazla ayrıntı düzeyi ve ExpressRoute doğrudan kullanarak sunulan yeni özellikleri desteklemek için doğrudan ExpressRoute bağlantı hatları üzerinde mevcut bazı temel özellikleri vardır.

## <a name="circuit-skus"></a>Bağlantı hattı SKU'ları

ExpressRoute doğrudan Azure depolama ve diğer büyük veri hizmetlerle büyük veri alma senaryolarını destekler. 100 Gbps ExpressRoute doğrudan 'daki ExpressRoute devreleri, **40 Gbps** ve **100 Gbps** devre SKU 'larını da desteklemektedir. Fiziksel bağlantı noktası çiftleri yalnızca **100 veya 10 GB/sn** olabilir ve birden çok sanal Devreme sahip olabilir. Devre boyutları:

| **100 Gbps ExpressRoute Direct** | **10 Gbps ExpressRoute Direct** | 
| --- | --- |
| **Abone olunan bant genişliği**: 200 Gbps | **Abone olunan bant genişliği**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Teknik Gereksinimler

* Microsoft Kurumsal kenar yönlendirici (MSEE) arabirimleri:
    * Yalnızca yönlendirici çiftinde çift 10 veya 100 Gigabit Ethernet bağlantı noktası
    * Tek modlu LR fiber bağlantı
    * IPv4 ve IPv6
    * IP MTU 1500 bayt

* Anahtar/yönlendirici katman 2/katman 3 bağlantısı:
    * 1 802.1 Q (Dot1Q) etiketi veya iki etiket 802.1 Q (QinQ) etiketi kapsüllemeyi desteklemelidir
    * EtherType = 0x8100
    * Microsoft tarafından belirtilen VLAN KIMLIğINE bağlı olarak dış VLAN etiketi (STAG) eklenmelidir ve *yalnızca QinQ üzerinde geçerlidir*
    * Bağlantı noktası ve cihaz başına birden çok BGP oturumu (VLAN) desteklemelidir
    * IPv4 ve IPv6 bağlantısı. *IPv6 için ek bir alt arabirim oluşturulmaz. IPv6 adresi var olan alt arabirime eklenecektir*. 
    * İsteğe bağlı: ExpressRoute devrelerinde tüm özel eşlemelerde varsayılan olarak yapılandırılan [çift yönlü Iletme algılaması (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) desteği

## <a name="vlan-tagging"></a>VLAN etiketleme

ExpressRoute doğrudan QinQ hem Dot1Q VLAN etiketleme destekler.

* **QinQ VLAN etiketleme** üzerinde yalıtılmış Yönlendirme etki alanları için sağlayan bir ExpressRoute bağlantı hattı temelinde. Azure, dinamik olarak bağlantı hattı oluşturma sırasında bir S etiketi ayırır ve değiştirilemez. Her eşleme (özel ve Microsoft) devreye benzersiz bir C-Tag VLAN yararlanacaktır. C-Tag devreler ExpressRoute doğrudan bağlantı noktalarında arasında benzersiz olması gerekli değildir.

* **Dot1Q VLAN etiketleme** tek bir VLAN etiketli için sağlayan bir ExpressRoute doğrudan bağlantı noktası çifti temelinde. Tüm devreler ve ExpressRoute doğrudan bağlantı noktası çifti üzerinde eşlemeler arasında bir eşleme kullanılan C-Tag benzersiz olması gerekir.

## <a name="workflow"></a>İş akışı

[![iş akışı](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

ExpressRoute doğrudan aynı kurumsal sınıf SLA ile küresel Microsoft ağına aktif/aktif yedekli bağlantılar sağlar. ExpressRoute altyapı gereksizdir ve küresel Microsoft ağına bağlantısı yedekli ve çeşitli ve uygun şekilde ölçeklenen müşteri gereksinimlerine sahip. 

## <a name="next-steps"></a>Sonraki adımlar

[ExpressRoute doğrudan yapılandırın](expressroute-howto-erdirect.md)
