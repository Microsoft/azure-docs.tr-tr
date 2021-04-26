---
title: Azure Event Hubs için ağ güvenliği
description: Bu makalede özel uç noktalardan erişimin nasıl yapılandırılacağı açıklanmaktadır
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 9503fc26c22d7dbff13c5754288f577b7bb3242f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011005"
---
# <a name="network-security-for-azure-event-hubs"></a>Azure Event Hubs için ağ güvenliği 
Bu makalede, Azure Event Hubs ile aşağıdaki güvenlik özelliklerinin nasıl kullanılacağı açıklanır: 

- Hizmet etiketleri
- IP güvenlik duvarı kuralları
- Ağ Hizmeti uç noktaları
- Özel uç noktalar


## <a name="service-tags"></a>Hizmet etiketleri
Hizmet etiketi, belirli bir Azure hizmetinden bir IP adresi önekleri grubunu temsil eder. Microsoft, hizmet etiketi ile çevrelenmiş adres öneklerini yönetir ve adres değişikliği olarak hizmet etiketini otomatik olarak güncelleştirir ve ağ güvenlik kuralları için sık sık güncelleştirmelerin karmaşıklığını en aza indirir. Hizmet etiketleri hakkında daha fazla bilgi için bkz. [hizmet etiketlerine genel bakış](../virtual-network/service-tags-overview.md).

[Ağ güvenlik gruplarında](../virtual-network/network-security-groups-overview.md#security-rules) veya [Azure Güvenlik duvarında](../firewall/service-tags.md)ağ erişim denetimleri tanımlamak için hizmet etiketlerini kullanabilirsiniz. Güvenlik kuralları oluştururken belirli IP adreslerinin yerine hizmet etiketleri kullanın. Bir kuralın uygun *kaynak* veya *hedef* alanındaki hizmet etiketi adını (örneğin, **EventHub**) belirterek, karşılık gelen hizmet için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz.

| Hizmet etiketi | Amaç | Gelen veya giden trafiği kullanabilir miyim? | Bölgesel olabilir mi? | Azure Güvenlik Duvarı ile kullanılabilir mi? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | Giden | Yes | Yes |


## <a name="ip-firewall"></a>IP güvenlik duvarı 
Varsayılan olarak, istek geçerli kimlik doğrulaması ve yetkilendirmeyle geldiği sürece, Event Hubs ad alanlarına internet 'ten erişilebilir. IP güvenlik duvarı ile bunu, [CIDR (sınıfsız Inter-Domain yönlendirme)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) gösteriminde yalnızca bir IPv4 adresi veya IPv4 adres aralıkları kümesiyle sınırlayabilirsiniz.

Bu özellik, Azure Event Hubs yalnızca belirli bilinen sitelerden erişilebilir olması gereken senaryolarda yardımcı olur. Güvenlik duvarı kuralları, belirli IPv4 adreslerinden kaynaklanan trafiği kabul etmek için kuralları yapılandırmanızı sağlar. Örneğin, [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)ile Event Hubs kullanıyorsanız, yalnızca şirket ıçı altyapı IP adreslerinden gelen trafiğe izin vermek için bir **güvenlik duvarı kuralı** oluşturabilirsiniz. 

IP güvenlik duvarı kuralları Event Hubs ad alanı düzeyinde uygulanır. Bu nedenle, kurallar desteklenen herhangi bir protokolü kullanarak istemcilerden gelen tüm bağlantılara uygulanır. Event Hubs ad alanındaki izin verilen bir IP kuralıyla eşleşmeyen bir IP adresinden gelen bağlantı girişimleri yetkisiz olarak reddedilir. Yanıt, IP kuralından bahsetmiyor. IP filtresi kuralları sırasıyla uygulanır ve IP adresiyle eşleşen ilk kural kabul etme veya reddetme eylemini belirler.

Daha fazla bilgi için bkz. [bir olay hub 'ı IÇIN IP güvenlik duvarını yapılandırma](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Ağ Hizmeti uç noktaları
[Sanal ağ (VNet) hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) ile Event Hubs tümleştirmesi, sanal ağlara bağlı sanal makineler gibi iş yüklerinden, her iki uçta da güvenli hale getirilen ağ trafiği yolu ile güvenli erişim sağlar.

En az bir sanal ağ alt ağ hizmeti uç noktasına bağlanacak şekilde yapılandırıldıktan sonra, ilgili Event Hubs ad alanı artık sanal ağlardaki her yerden trafiği kabul etmez. Sanal ağ perspektifinden bir Event Hubs ad alanını bir hizmet uç noktasına bağlamak, sanal ağ alt ağından mesajlaşma hizmetine yalıtılmış bir ağ tüneli yapılandırır. 

Sonuç olarak, alt ağa ve ilgili Event Hubs ad alanıyla ilişkili olan iş yükleri arasında özel ve yalıtılmış bir ilişki vardır. Bu, bir genel IP aralığında yer alan mesajlaşma hizmeti uç noktasının observable ağ adresi artma. Bu davranış için bir özel durum vardır. Hizmet uç noktasının etkinleştirilmesi, varsayılan olarak `denyall` sanal ağla Ilişkili [IP güvenlik duvarında](event-hubs-ip-filtering.md) kuralı etkinleştirir. Olay Hub 'ı genel uç noktasına erişimi etkinleştirmek için IP güvenlik duvarında belirli IP adresleri ekleyebilirsiniz. 

> [!IMPORTANT]
> Sanal ağlar, Event Hubs **Standart** ve **adanmış** katmanlarında desteklenir. **Temel** katmanda desteklenmez.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet tümleştirmesi tarafından etkinleştirilen gelişmiş güvenlik senaryoları 

Sıkı ve compartmenbir güvenlik gerektiren ve sanal ağ alt ağlarının, compartmençalıştıran hizmetler arasında segmentleme sağladığı çözümler, yine de bu bölmeleri bulunan hizmetler arasında iletişim yollarına gerek duyar.

TCP/IP üzerinden HTTPS 'yi yürüten bu dahil olmak üzere, bölmeleri arasındaki tüm anında IP rotası, üzerindeki ağ katmanından güvenlik açıklarına karşı yararlanma riskini taşır. Mesajlaşma Hizmetleri, iletilerin taraflar arasında geçiş yaparken bile diske yazıldığı, yalıtılmış iletişim yolları sağlar. Aynı Event Hubs örneğine bağlanan iki ayrı sanal ağdaki iş yükleri iletiler aracılığıyla verimli ve güvenilir bir şekilde iletişim kurabilir, ancak ilgili ağ yalıtımı sınır bütünlüğü korunur.
 
Bu, güvenlik duyarlı bulut çözümlerinizin yalnızca Azure sektör lideri güvenilir ve ölçeklenebilir zaman uyumsuz mesajlaşma özelliklerine erişim elde edemeyeceği anlamına gelir, ancak artık HTTPS ve diğer TLS güvenlikli yuva protokolleri de dahil olmak üzere herhangi bir eşler arası iletişim moduyla ulaşılabilir 'dan daha güvenli olan güvenli çözüm bölmeleri arasında iletişim yolları oluşturmak için mesajlaşma kullanabilir.

### <a name="bind-event-hubs-to-virtual-networks"></a>Olay Hub 'larını sanal ağlara bağlama

**Sanal ağ kuralları** , Azure Event Hubs ad uzayının belirli bir sanal ağ alt ağından gelen bağlantıları kabul edip etmediğini denetleyen güvenlik duvarı güvenlik özelliğidir.

Bir Event Hubs ad alanını bir sanal ağa bağlamak iki adımlı bir işlemdir. Önce bir sanal ağın alt ağında bir **sanal ağ hizmeti uç noktası** oluşturmanız ve bunu [hizmet uç noktası genel bakış](../virtual-network/virtual-network-service-endpoints-overview.md) makalesinde açıklandığı gibi **Microsoft. EventHub** için etkinleştirmeniz gerekir. Hizmet uç noktasını ekledikten sonra, Event Hubs ad alanını bir **sanal ağ kuralıyla** bağlayın.

Sanal ağ kuralı, bir sanal ağ alt ağıyla Event Hubs ad alanının bir ilişkidir. Kural var olsa da, alt ağa erişen tüm iş yükleri Event Hubs ad alanına erişim izni verilir. Event Hubs kendisi hiçbir şekilde giden bağlantı oluşturmaz, erişim elde etmek zorunda kalmaz ve bu nedenle bu kuralı etkinleştirerek alt ağınız için hiçbir şekilde erişim izni verilmemiş olur.

Daha fazla bilgi için bkz. [bir olay hub 'ı için sanal ağ hizmet uç noktalarını yapılandırma](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Özel uç noktalar

[Azure özel bağlantı hizmeti](../private-link/private-link-overview.md) , Azure hizmetlerine (örneğin, Azure Event Hubs, Azure depolama ve Azure Cosmos DB) ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine sanal ağınızdaki **özel bir uç nokta** üzerinden erişmenizi sağlar.

Özel uç nokta, Azure özel bağlantısı tarafından desteklenen bir hizmete özel ve güvenli bir şekilde bağlanan bir ağ arabirimidir. Özel uç nokta, sanal ağınızdan bir özel IP adresi kullanarak hizmeti sanal ağınıza etkin bir şekilde getiriyor. Hizmete giden tüm trafik özel uç nokta aracılığıyla yönlendirilebilir, bu nedenle ağ geçitleri, NAT cihazları, ExpressRoute veya VPN bağlantıları ya da genel IP adresleri gerekmez. Sanal ağınız ve hizmet arasındaki trafik, Microsoft omurga ağı üzerinden geçer ve genel İnternet’ten etkilenme olasılığı ortadan kaldırılır. Bir Azure kaynağı örneğine bağlanarak, erişim denetimi için en yüksek düzeyde ayrıntı düzeyi sağlayabilirsiniz.

> [!IMPORTANT]
> Bu özellik hem **Standart** hem de **adanmış** katmanlar için desteklenir. **Temel** katmanda desteklenmez.

Daha fazla bilgi için bkz. [bir olay hub 'ı için özel uç noktaları yapılandırma](private-link-service.md)


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Bir olay hub 'ı için IP güvenlik duvarını yapılandırma](event-hubs-ip-filtering.md)
- [Bir olay hub 'ı için sanal ağ hizmet uç noktalarını yapılandırma](event-hubs-service-endpoints.md)
- [Bir olay hub 'ı için özel uç noktaları yapılandırma](private-link-service.md)