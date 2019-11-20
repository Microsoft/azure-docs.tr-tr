---
title: Azure 'da IP adresi türleri (klasik)
titlesuffix: Azure Virtual Network
description: Azure 'da genel ve özel IP adresleri (klasik) hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 207e728d25df9192f8a600b13d86330af8311700
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058920"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Azure 'da IP adresi türleri ve ayırma yöntemleri (klasik)
Diğer Azure kaynaklarıyla, şirket içi ağınızla ve İnternet’le iletişim kurmak için Azure kaynaklarına IP adresleri atayabilirsiniz. Azure 'da kullanabileceğiniz iki tür IP adresi vardır: genel ve özel.

Genel IP adresleri, Azure genel kullanıma yönelik hizmetler de dahil olmak üzere Internet ile iletişim için kullanılır.

Özel IP adresleri, ağınızı Azure 'a genişletmek için bir VPN ağ geçidi veya ExpressRoute bağlantı hattı kullandığınızda bir Azure sanal ağı (VNet), bir bulut hizmeti ve şirket içi ağınız içinde iletişim kurmak için kullanılır.

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Kaynak Yöneticisi ve klasik](../resource-manager-deployment-model.md).  Bu makale klasik dağıtım modelini incelemektedir. Microsoft, en yeni dağıtımların Kaynak Yöneticisi kullanmasını önerir. [IP adresleri](virtual-network-ip-addresses-overview-arm.md) makalesini okuyarak kaynak YÖNETICISI içindeki IP adresleri hakkında bilgi edinin.

## <a name="public-ip-addresses"></a>Genel IP adresleri
Genel IP adresleri, Azure kaynaklarının, [Redu](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL veritabanları](../sql-database/sql-database-technical-overview.md)ve [Azure depolama](../storage/common/storage-introduction.md)gibi Azure önbelleği gibi Internet ve Azure genel kullanıma yönelik hizmetleriyle iletişim kurmasına olanak tanır.

Genel bir IP adresi aşağıdaki kaynak türleriyle ilişkilendirilir:

* Bulut hizmetleri
* IaaS sanal makineleri (VM 'Ler)
* PaaS rol örnekleri
* VPN ağ geçitleri
* Uygulama ağ geçitleri

### <a name="allocation-method"></a>Ayırma yöntemi
Genel bir IP adresinin bir Azure kaynağına atanması gerektiğinde, kaynak oluşturulduğu konumdaki kullanılabilir genel IP adresi havuzundan *dinamik olarak* ayrılır. Bu IP adresi, kaynak durdurulduğunda serbest bırakılır. Bulut hizmeti ile, bu durum, tüm rol örneklerinin durdurulduğu ve *statik* (ayrılmış) bir IP adresi kullanılarak önlenebilir (bkz. [Cloud Services](#cloud-services)).

> [!NOTE]
> Genel IP adreslerinin Azure kaynaklarına ayrıldığı IP aralıklarının listesi, [Azure veri MERKEZI IP aralıklarında](https://www.microsoft.com/download/details.aspx?id=41653)yayımlanır.
> 
> 

### <a name="dns-hostname-resolution"></a>DNS ana bilgisayar adı çözümlemesi
Bir bulut hizmeti veya IaaS VM oluşturduğunuzda, Azure 'daki tüm kaynaklarda benzersiz olan bir bulut hizmeti DNS adı sağlamanız gerekir. Bu, Azure tarafından yönetilen DNS sunucularında, *DnsName*. cloudapp.NET için KAYNAĞıN genel IP adresi olarak bir eşleme oluşturur. Örneğin, bir bulut hizmeti DNS adı **contoso**olan bir bulut hizmeti oluşturduğunuzda, **contoso.cloudapp.net** tam etkı alanı adı (FQDN), bulut hizmetinin genel IP adresine (VIP) çözümlenir. Bu FQDN’yi kullanarak Azure’daki genel IP adresini işaret eden özel bir etki alanı CNAME kaydı oluşturabilirsiniz.

### <a name="cloud-services"></a>Bulut hizmetleri
Bulut hizmeti her zaman bir sanal IP adresi (VIP) olarak adlandırılan bir genel IP adresine sahiptir. VIP 'lerdeki farklı bağlantı noktalarını sanal makinelerde ve bulut hizmetindeki rol örneklerinde iç bağlantı noktalarına ilişkilendirmek için bir bulut hizmetinde uç noktalar oluşturabilirsiniz. 

Bulut hizmeti, hepsi aynı bulut hizmeti VIP aracılığıyla kullanıma sunulan birden çok IaaS VM veya PaaS rol örneği içerebilir. Ayrıca, SSL tabanlı Web siteleri ile çok kiracılı bir ortam gibi çok VIP senaryolarına olanak sağlayan [bir bulut hizmetine birden çok VIP](../load-balancer/load-balancer-multivip.md)atayabilirsiniz.

Bulut hizmeti genel IP adresinin, tüm rol örnekleri durdurulduğunda bile, [ayrılmış IP](virtual-networks-reserved-public-ip.md)olarak adlandırılan *STATIK* bir genel IP adresi kullanılarak aynı kalmasını sağlayabilirsiniz. Belirli bir konumda statik (ayrılmış) bir IP kaynağı oluşturabilir ve bu konumdaki herhangi bir bulut hizmetine atayabilirsiniz. Ayrılmış IP için gerçek IP adresini belirtemezsiniz, oluşturulan konumdaki kullanılabilir IP adresleri havuzundan ayrılır. Bu IP adresi, açıkça silininceye kadar serbest bırakılır.

Statik (ayrılmış) genel IP adresleri genellikle bir bulut hizmetinin bulunduğu senaryolarda kullanılır:

* Güvenlik Duvarı kurallarının son kullanıcılar tarafından ayarlanmasını gerektirir.
* dış DNS adı çözümlemesine ve dinamik bir IP 'nin bir kaydın güncelleştirilmesini gerektirmesine bağlıdır.
* IP tabanlı güvenlik modeli kullanan dış Web hizmetlerini kullanır.
* bir IP adresine bağlı SSL sertifikalarını kullanır.

> [!NOTE]
> Klasik bir VM oluşturduğunuzda, Azure tarafından bir sanal IP adresine (VIP) sahip bir kapsayıcı *bulut hizmeti* oluşturulur. Oluşturma Portal üzerinden yapıldığında, bulut hizmeti VIP aracılığıyla VM 'ye bağlanabilmeniz için Portal tarafından varsayılan bir RDP veya SSH *uç noktası* yapılandırılır. Bu bulut hizmeti VIP, sanal makineye bağlanmak için ayrılmış IP adresi etkin bir şekilde ayrılmış olabilir. Daha fazla uç nokta yapılandırarak ek bağlantı noktaları açabilirsiniz.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VM 'Leri ve PaaS rol örnekleri
Bir bulut hizmeti içindeki bir IaaS [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya PaaS rol örneğine doğrudan genel bir IP adresi atayabilirsiniz. Bu, örnek düzeyi genel IP adresi ([ılpıp](virtual-networks-instance-level-public-ip.md)) olarak adlandırılır. Bu genel IP adresi yalnızca dinamik olabilir.

> [!NOTE]
> Bu, bir bulut hizmeti birden çok IaaS VM veya PaaS rol örneği içerebildiği için, hepsi aynı bulut hizmeti VIP 'si aracılığıyla kullanıma sunulan bulut hizmetinin VIP 'sinden farklıdır.
> 
> 

### <a name="vpn-gateways"></a>VPN ağ geçitleri
Bir [VPN ağ geçidi](../vpn-gateway/vpn-gateway-about-vpngateways.md) , Azure VNET 'ı diğer Azure sanal ağlarına veya şirket içi ağlara bağlamak için kullanılabilir. VPN ağ geçidine, uzak ağla iletişim sağlayan, *dinamik olarak*genel bir IP adresi atanır.

### <a name="application-gateways"></a>Uygulama ağ geçitleri
Azure [Application Gateway](../application-gateway/application-gateway-introduction.md) , HTTP tabanlı ağ trafiğini yönlendirmek için Layer7 yük dengeleme için kullanılabilir. Uygulama ağ geçidine, yük dengeli VIP görevi gören *dinamik*olarak genel bir IP adresi atanır.

### <a name="at-a-glance"></a>Bir bakışta
Aşağıdaki tabloda, olası ayırma yöntemlerine (dinamik/statik) sahip her kaynak türü ve birden çok genel IP adresi atama yeteneği gösterilmektedir.

| Resource | Dinamik | Statik | Birden çok IP adresi |
| --- | --- | --- | --- |
| Bulut hizmeti |Evet |Evet |Evet |
| IaaS VM veya PaaS rol örneği |Evet |Hayır |Hayır |
| VPN ağ geçidi |Evet |Hayır |Hayır |
| Uygulama ağ geçidi |Evet |Hayır |Hayır |

## <a name="private-ip-addresses"></a>Özel IP adresleri
Özel IP adresleri, Azure kaynaklarının bir bulut hizmetindeki diğer kaynaklarla veya bir [sanal ağ](virtual-networks-overview.md)(VNet) veya şirket içi ağa (VPN ağ geçidi veya ExpressRoute bağlantı hattı aracılığıyla) Internet 'ten ERIŞILEBILEN bir IP adresi kullanmadan iletişim kurmasına olanak tanır.

Azure klasik dağıtım modelinde, aşağıdaki Azure kaynaklarına özel bir IP adresi atanabilir:

* IaaS VM 'Leri ve PaaS rol örnekleri
* İç yük dengeleyici
* Uygulama ağ geçidi

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS VM 'Leri ve PaaS rol örnekleri
Klasik dağıtım modeliyle oluşturulan sanal makineler (VM 'Ler), PaaS rol örneklerine benzer şekilde her zaman bir bulut hizmetine yerleştirilir. Bu nedenle, özel IP adreslerinin davranışı bu kaynaklar için benzerdir.

Bulut hizmetinin iki şekilde dağıtılabileceğini unutmayın:

* Bir sanal ağ içinde olmayan *tek başına* bir bulut hizmeti olarak.
* Bir sanal ağın parçası olarak.

#### <a name="allocation-method"></a>Ayırma yöntemi
*Tek başına* bulut hizmeti söz konusu olduğunda, kaynaklar Azure veri MERKEZI özel IP adresi aralığından *dinamik olarak* ayrılan özel bir IP adresi alır. Yalnızca aynı bulut hizmeti içindeki diğer VM 'lerle iletişim için kullanılabilir. Bu IP adresi, kaynak durdurulduğunda ve başlatıldığında değişebilir.

Bir sanal ağ içinde dağıtılan bir bulut hizmeti söz konusu olduğunda, kaynaklar ilişkili alt ağın (ağ yapılandırmasında belirtildiği gibi) adres aralığından ayrılan özel IP adreslerini alır. Bu özel IP adresleri, VNet içindeki tüm VM 'Ler arasında iletişim için kullanılabilir.

Ayrıca, bir sanal ağ içindeki bulut Hizmetleri söz konusu olduğunda, varsayılan olarak özel bir IP adresi *dinamik olarak* (DHCP kullanılarak) ayrılır. Kaynak durdurulduğunda ve başlatıldığında değişiklik yapabilir. IP adresinin aynı kalmasını sağlamak için ayırma yöntemini *statik*olarak ayarlamanız ve ilgili adres aralığı içinde GEÇERLI bir IP adresi sağlamanız gerekir.

Statik özel IP adresleri yaygın olarak şunlar için kullanılır:

* Etki alanı denetleyicisi veya DNS sunucusu olarak çalışan VM’ler.
* IP adreslerini kullanan güvenlik duvarı kuralları gerektiren VM 'Ler.
* Diğer uygulamalar tarafından bir IP adresi aracılığıyla erişilen Hizmetleri çalıştıran VM 'Ler.

#### <a name="internal-dns-hostname-resolution"></a>İç DNS ana bilgisayar adı çözümlemesi
Özel DNS sunucularını açıkça yapılandırmadığınız sürece, tüm Azure VM 'Leri ve PaaS rol örnekleri, varsayılan olarak [Azure tarafından YÖNETILEN DNS sunucuları](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) ile yapılandırılır. Bu DNS sunucuları, aynı VNet veya bulut hizmeti içinde bulunan VM 'Ler ve rol örnekleri için iç ad çözümlemesi sağlar.

Bir VM oluşturduğunuzda, Azure tarafından yönetilen DNS sunucularına ana bilgisayar adı için özel IP adresine yönelik bir eşleme eklenir. Çoklu NIC VM ile, ana bilgisayar adı birincil NIC 'in özel IP adresi ile eşleştirilir. Ancak, bu eşleme bilgileri aynı bulut hizmeti veya VNet içindeki kaynaklarla kısıtlıdır.

*Tek başına* bulut hizmeti söz konusu olduğunda, yalnızca aynı bulut hizmetindeki tüm VM 'ler/rol örneklerinin ana bilgisayar adlarını çözümleyebilirsiniz. VNet içindeki bir bulut hizmeti söz konusu olduğunda, VNet içindeki tüm VM 'Ler/rol örneklerinin ana bilgisayar adlarını çözümleyebilirsiniz.

### <a name="internal-load-balancers-ilb--application-gateways"></a>İç yük dengeleyiciler (ILB) ve Uygulama ağ geçitleri
Bir [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md)’ın (ILB) veya [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)’in **ön uç** yapılandırmasına özel bir IP adresi atayabilirsiniz. Özel IP adresi, yalnızca kendi sanal ağı (VNet) ve VNet’e bağlı uzak ağlar tarafından erişilebilen bir iç uç nokta olarak çalışır. Ön uç yapılandırmasına dinamik veya statik bir özel IP adresi atayabilirsiniz. Çoklu VIP senaryolarına olanak tanımak için birden çok özel IP adresi de atayabilirsiniz.

### <a name="at-a-glance"></a>Bir bakışta
Aşağıdaki tabloda, olası ayırma yöntemleri (dinamik/statik) ve birden çok özel IP adresi atama yeteneği ile her kaynak türü gösterilmektedir.

| Resource | Dinamik | Statik | Birden çok IP adresi |
| --- | --- | --- | --- |
| VM ( *tek başına* bulut hizmeti veya VNET 'te) |Evet |Evet |Evet |
| PaaS rol örneği ( *tek başına* bulut hizmeti veya VNET 'te) |Evet |Hayır |Hayır |
| İç yük dengeleyici ön ucu |Evet |Evet |Evet |
| Application Gateway ön ucu |Evet |Evet |Evet |

## <a name="limits"></a>Sınırlar
Aşağıdaki tabloda, abonelik başına Azure 'da IP adreslemeye uygulanan sınırlar gösterilmektedir. [Destek ekibiyle iletişime geçerek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) varsayılan limitleri iş ihtiyaçlarınıza göre en üst düzeye çıkarabilirsiniz.

|  | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| Genel IP adresleri (dinamik) |5 |Desteğe Başvurun |
| Ayrılmış genel IP adresleri |20 |Desteğe Başvurun |
| Dağıtım başına genel VIP (bulut hizmeti) |5 |Desteğe Başvurun |
| Dağıtım başına özel VIP (ıLB) (bulut hizmeti) |1\. |1\. |

Azure 'da [ağ için tüm sınırları](../azure-subscription-service-limits.md#networking-limits) okuduğunuzdan emin olun.

## <a name="pricing"></a>Fiyatlandırma
Çoğu durumda, genel IP adresleri ücretsizdir. Ek ve/veya statik genel IP adresleri kullanmak için kabul edilen bir ücret vardır. [Genel IP 'lerin fiyatlandırma yapısını](https://azure.microsoft.com/pricing/details/ip-addresses/)anladığınızdan emin olun.

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Kaynak Yöneticisi ve klasik dağıtımlar arasındaki farklılıklar
Kaynak Yöneticisi ve klasik dağıtım modelindeki IP adresleme özelliklerinin bir karşılaştırması aşağıda verilmiştir.

|  | Resource | Klasik | Resource Manager |
| --- | --- | --- | --- |
| **Genel IP adresi** |***'NIN*** |ILPıP (yalnızca dinamik) olarak adlandırılır |Genel IP olarak adlandırılır (dinamik veya statik) |
|  ||Bir IaaS VM 'sine veya bir PaaS rol örneğine atandı |VM 'nin NIC 'i ile ilişkili |
|  |***Internet 'e yönelik yük dengeleyici*** |VIP (dinamik) veya Ayrılmış IP (statik) olarak adlandırılır |Genel IP olarak adlandırılır (dinamik veya statik) |
|  ||Bir bulut hizmetine atandı |Yük dengeleyicinin ön uç yapılandırması ile ilişkili |
|  | | | |
| **Özel IP adresi** |***'NIN*** |DIP olarak adlandırılır |Özel IP adresi olarak adlandırılır |
|  ||Bir IaaS VM 'sine veya bir PaaS rol örneğine atandı |VM 'nin NIC 'sine atandı |
|  |***İç yük dengeleyici (ıLB)*** |ILB 'ye atandı (dinamik veya statik) |ILB 'nin ön uç yapılandırmasına atandı (dinamik veya statik) |

## <a name="next-steps"></a>Sonraki adımlar
* Azure portal kullanarak [statik bir özel IP adresi Ile VM dağıtın](virtual-networks-static-private-ip-classic-pportal.md) .

