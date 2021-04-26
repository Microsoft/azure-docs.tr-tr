---
title: Sık sorulan sorular-Azure Load Balancer
description: Azure Load Balancer hakkında sık sorulan soruların yanıtları.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3752a36d22f879b95b02bd49436be78212fe56a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576050"
---
# <a name="load-balancer-frequently-asked-questions"></a>Load Balancer sık sorulan sorular

## <a name="what-types-of-load-balancer-exist"></a>Ne tür Load Balancer var?
Bir sanal ağ ve dış yük dengeleyiciler içindeki trafiği dengelemeye yönelik iç yük dengeleyiciler, internet 'e bağlı bir uç noktadan gelen ve giden trafiği dengelemek Daha fazla bilgi için bkz. [Load Balancer türleri](components.md#frontend-ip-configurations). 

Her iki tür için de Azure, farklı işlevsel, performans, güvenlik ve sistem durumu izleme özelliklerine sahip temel bir SKU ve standart SKU sunmaktadır. Bu farklılıklar, [SKU karşılaştırma](skus.md) makalemiz bölümünde açıklanmaktadır.

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Bir temel bilgisayardan bir Standart Load Balancer nasıl yükseltebilirim?
Bir Load Balancer SKU yükseltmesi hakkında otomatik betik ve yönergeler için [temel ve standart sürümüne yükseltme](upgrade-basic-standard.md) makalesine bakın.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure 'daki farklı yük dengeleme seçenekleri nelerdir?
Kullanılabilir Yük Dengeleme Hizmetleri ve her biri için önerilen kullanımlar için [yük dengeleyici teknoloji kılavuzuna](/azure/architecture/guide/technology-choices/load-balancing-overview)  bakın.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Load Balancer ARM şablonlarını nerede bulabilirim?
Ortak dağıtımların ARM şablonları için [Azure Load Balancer hızlı başlangıç şablonları listesine](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) bakın.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Gelen NAT kuralları Yük Dengeleme kurallarından farklı mı?
NAT kuralları, trafiğin yönlendirileceği bir arka uç kaynağını belirtmek için kullanılır. Örneğin, belirli bir VM 'ye RDP trafiği göndermek için belirli bir yük dengeleyici bağlantı noktası yapılandırma. Yük Dengeleme kuralları, trafiği yönlendirmek için bir arka uç kaynakları havuzunu belirtmek için kullanılır ve her bir örnekteki yükün yükünü dengeleyin. Örneğin, bir yük dengeleyici kuralı, TCP paketlerini bir Web sunucuları havuzunda yük dengeleyicinin 80 numaralı bağlantı noktasında yönlendirebilir.

## <a name="what-is-ip-1686312916"></a>IP 168.63.129.16 nedir?
Azure altyapısı olarak etiketlenen konağın sanal IP adresi, Azure sistem durumu araştırmalarının gerçekleştiği Load Balancer. Arka uç örnekleri yapılandırılırken, bu IP adresinden gelen trafiğin sistem durumu araştırmasına başarıyla yanıt vermesi gerekir. Bu kural Load Balancer ön uç 'nize erişimle etkileşime girmiyor. Azure Load Balancer kullanmıyorsanız, bu kuralı geçersiz kılabilirsiniz. [Burada](../virtual-network/service-tags-overview.md#available-service-tags)Service Tags hakkında daha fazla bilgi edinebilirsiniz.

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Temel Load Balancer genel VNet eşlemesini kullanabilir miyim?
Hayır. Temel Load Balancer genel VNET eşlemesini desteklemez. Bunun yerine bir Standart Load Balancer kullanabilirsiniz. Sorunsuz yükseltme için [temel 'Ten standart sürümüne yükseltme](upgrade-basic-standard.md) makalesine bakın.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Azure VM 'nin kullandığı genel IP 'yi nasıl keşfedebilirim?

Giden bir bağlantının genel kaynak IP adresini belirlemenin birçok yolu vardır. OpenDNS, sanal makinenizin genel IP adresini gösterebilmeniz için bir hizmet sağlar.
Nslookup komutunu kullanarak, myip.opendns.com adı için OpenDNS çözümleyiciye bir DNS sorgusu gönderebilirsiniz. Hizmet, sorguyu göndermek için kullanılan kaynak IP adresini döndürür. Aşağıdaki sorguyu sanal makinenizde çalıştırdığınızda, yanıt o VM için kullanılan genel IP 'dir:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>Aynı Kullanılabilirlik kümesinden bir sanal makineyi Load Balancer farklı bir arka uç havuzuna ekleyebilir miyim?
Hayır, bu mümkün değildir.

## <a name="what-is-the-maximum-data-throughput-that-can-be-achieved-via-an-azure-load-balancer"></a>Azure Load Balancer aracılığıyla elde edilebilecekleri maksimum veri işleme nedir?
Azure LB bir geçişli ağ yük dengeleyicisi olduğundan, işleme sınırlamaları arka uç havuzunda kullanılan sanal makine türü tarafından dikte edilir. Diğer ağ aktarım hızı hakkında bilgi edinmek için [sanal makine ağ aktarım hızı](../virtual-network/virtual-machine-network-throughput.md)bölümüne bakın.

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Azure depolama ile aynı bölgedeki bağlantılar nasıl çalışır?
Yukarıdaki senaryolar aracılığıyla giden bağlantıların olması, VM ile aynı bölgedeki depolamaya bağlanmak için gerekli değildir. Bunu istemiyorsanız, yukarıda açıklandığı gibi ağ güvenlik gruplarını (NSG 'ler) kullanın. Diğer bölgelerdeki depolama bağlantısı için giden bağlantı gereklidir. Lütfen aynı bölgedeki bir VM 'den depolamaya bağlanırken, depolama tanılama günlüklerindeki kaynak IP adresinin, sanal makinenizin genel IP adresi değil, iç sağlayıcı adresi olacağını unutmayın. Depolama hesabınıza erişimi aynı bölgedeki bir veya daha fazla sanal ağ alt ağında bulunan VM 'Lerle kısıtlamak istiyorsanız, depolama hesabı güvenlik duvarını yapılandırırken ortak IP adresinizi değil [sanal ağ hizmet uç noktalarını](../virtual-network/virtual-network-service-endpoints-overview.md) kullanın. Hizmet uç noktaları yapılandırıldıktan sonra, sanal ağ özel IP adresinizi, iç sağlayıcı adresinde değil, depolama tanılama günlüklerinizin içinde görürsünüz.

## <a name="does-azure-load-balancer-support-tlsssl-termination"></a>, TLS/SSL sonlandırmayı Azure Load Balancer destekler mi?
Hayır, bir geçiş ağ yük dengeleyici olduğundan Azure Load Balancer Şu anda sonlandırmasını desteklememektedir. Application Gateway, uygulamanız için gerekliyse olası bir çözüm olabilir.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Giden bağlantı açısından en iyi uygulamalar nelerdir?
Standart Load Balancer ve standart genel IP, giden bağlantılara yönelik yetenekler ve farklı davranışları tanıtır. Bunlar temel SKU 'Lar ile aynı değildir. Standart SKU 'Lar ile çalışırken giden bağlantı isterseniz, standart genel IP adresleriyle veya standart ortak Load Balancer açıkça tanımlamanız gerekir. Bu, dahili bir Standart Load Balancer kullanırken giden bağlantı oluşturmayı içerir. Her zaman standart bir genel Load Balancer giden kuralları kullanmanızı öneririz. Bu, bir iç Standart Load Balancer kullanıldığında, giden bağlantı istenirse arka uç havuzundaki VM 'Ler için giden bağlantı oluşturmak üzere gerekli adımları uygulamanız gerekir. Giden bağlantı bağlamında, tek bir tek başına VM, tüm VM 'ler bir kullanılabilirlik kümesinde, bir VMSS içindeki tüm örnekler bir grup olarak davranır. Bu, bir kullanılabilirlik kümesindeki tek bir VM standart SKU ile ilişkili ise, bu kullanılabilirlik kümesindeki tüm sanal makine örnekleri, tek bir örnek doğrudan ilişkili olmasa bile standart SKU ile ilişkilendirildikleri kurallarla aynı kurallara göre davranır. Bu davranış, bir yük dengeleyiciye bağlı birden çok ağ arabirimi kartına sahip tek başına bir VM söz konusu olduğunda da gözlemlenmiştir. Tek başına bir NIC eklenirse aynı davranışa sahip olur. Genel kavramları anlamak için tüm belgeyi dikkatle gözden geçirin, SKU 'Lar arasındaki farklılıklar için [Standart Load Balancer](./load-balancer-overview.md) gözden geçirin ve [giden kuralları](load-balancer-outbound-connections.md#outboundrules)gözden geçirin.
Giden kuralları kullanmak, giden bağlantıların tüm yönleri üzerinde ayrıntılı denetim sağlar.
 
## <a name="next-steps"></a>Sonraki Adımlar
Sorunuz yukarıdaki listede yoksa, bu sayfayla ilgili geri bildirimde bulunmak için lütfen sorularınızı gönderin. Bu, tüm değerli müşteri sorularımızın cevaplanmasını sağlamak için ürün ekibine yönelik bir GitHub sorunu oluşturur.
