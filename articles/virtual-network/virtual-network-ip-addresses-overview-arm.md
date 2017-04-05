---
title: "Azure’da IP adresi türleri | Microsoft Docs"
description: "Azure’da genel ve özel IP adresleri hakkında bilgi edinin."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0506de98119576aae8f55cc6067f0bee95334c87
ms.lasthandoff: 03/31/2017


---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Azure’da IP adresi türleri ve ayırma yöntemleri
Diğer Azure kaynaklarıyla, şirket içi ağınızla ve İnternet’le iletişim kurmak için Azure kaynaklarına IP adresleri atayabilirsiniz. Azure'da kullanabileceğiniz iki tür IP adresi vardır:

* **Genel IP adresleri**: Genel kullanıma yönelik Azure hizmetleri dahil olmak üzere Internet ile iletişim için kullanılır
* **Özel IP adresleri**: Bir Azure sanal ağı (VNet) içinde ve şirket içi ağınızı Azure'a genişletmek için bir VPN ağ geçidi veya ExpressRoute bağlantı hattı kullanıyorsanız şirket içi ağınız içinde iletişim kurmak için kullanılır.

> [!NOTE]
> Azure’da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Resource Manager ve klasik](../resource-manager-deployment-model.md).  Bu makale, Microsoft’un çoğu yeni dağıtım için [klasik dağıtım modeli](virtual-network-ip-addresses-overview-classic.md) yerine önerdiği Resource Manager dağıtım modelini açıklamaktadır.
> 

Klasik dağıtım modeliyle ilgili bilginiz varsa [klasik ve Resource Manager IP adreslemesi arasındaki farkları](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) inceleyin.

## <a name="public-ip-addresses"></a>Genel IP adresleri
Genel IP adresleri, Azure kaynaklarının İnternet ile ve [Azure Redis Önbelleği](https://azure.microsoft.com/services/cache/), [Azure Olay Hub’ları](https://azure.microsoft.com/services/event-hubs/), [SQL veritabanları](../sql-database/sql-database-technical-overview.md) ve [Azure depolama alanı](../storage/storage-introduction.md) gibi genel kullanıma yönelik Azure hizmetleriyle iletişim kurmasını sağlar.

Azure Resource Manager’daki bir [genel IP](resource-groups-networking.md#public-ip-address) adresi, kendi özelliklerine sahip olan bir kaynaktır. Genel bir IP adresini aşağıdaki kaynakların herhangi biriyle ilişkilendirebilirsiniz:

* Sanal makineler (VM)
* İnternet'e yönelik yük dengeleyiciler
* VPN ağ geçitleri
* Uygulama ağ geçitleri

### <a name="allocation-method"></a>Ayırma yöntemi
Bir IP adresinin *genel* bir IP kaynağına ayrıldığı iki yöntem vardır: *Dinamik* veya *statik*. Varsayılan ayırma yöntemi, bir IP adresinin oluşturulduğu sırada **ayrılmadığı** yöntem olan *dinamik*tir. Bunun yerine, genel IP adresi ilişkili kaynağı (VM veya yük dengeleyici gibi) başlattığınızda (veya oluşturduğunuzda) ayrılır. Kaynağı durdurduğunuzda (veya sildiğinizde) IP adresi serbest kalır. Bu, bir kaynağı durdurduğunuzda veya başlattığınızda IP adresinin değişmesine yol açar.

İlişkili kaynağın IP adresinin aynı kalmasını sağlamak için ayırma yöntemini açıkça *statik* olarak ayarlayabilirsiniz. Bu durumda, bir IP adresi anında atanır. Adres, yalnızca kaynağı sildiğinizde veya ayırma yöntemini *dinamik* olarak değiştirdiğinizde serbest kalır.

> [!NOTE]
> Ayırma yöntemini *statik* olarak ayarladığınızda bile *genel IP kaynağına* atanan gerçek IP adresini belirtemezsiniz. Bunun yerine, kaynağın oluşturulduğu Azure konumundaki kullanılabilir IP adreslerinden oluşan bir havuzdan ayrılır.
>

Statik genel IP adresleri yaygın olarak aşağıdaki senaryolarda kullanılır:

* Son kullanıcıların Azure kaynaklarınızla iletişim kurabilmesi için güvenlik duvarı ayarlarını güncelleştirmesi gerekir.
* IP adresindeki bir değişikliğin A kayıtlarının güncelleştirilmesini gerektireceği DNS ad çözümlemesi.
* Azure kaynaklarınız, IP adresi tabanlı bir güvenlik yöntemi kullanan diğer uygulama ve hizmetlerle iletişim kurar.
* Bir IP adresine bağlı SSL sertifikalarınız.

> [!NOTE]
> Azure kaynaklarına ayrılan genel IP adreslerinin (dinamik/statik) ait olduğu IP aralıklarının listesi [Azure Veri Merkezi IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653) sayfasında yayımlanır.
>

### <a name="dns-hostname-resolution"></a>DNS ana bilgisayar adı çözümlemesi
Bir genel IP kaynağı için DNS etki alanı ad etiketi belirtebilirsiniz; bu durumda, Azure tarafından yönetilen DNS sunucularında genel IP adresine yönelik olarak *etkialanıadetiketi*.*konum*.cloudapp.azure.com için bir eşleme oluşturulur. Örneğin, **Batı ABD** Azure *konumunda* *etkialanıadetiketi* olarak **contoso** değerini içeren bir genel IP kaynağı oluşturursanız, **contoso.westus.cloudapp.azure.com** şeklindeki tam etki alanı adı (FQDN), kaynağın genel IP adresi olarak çözümlenir. Bu FQDN’yi kullanarak Azure’daki genel IP adresini işaret eden özel bir etki alanı CNAME kaydı oluşturabilirsiniz.

> [!IMPORTANT]
> Oluşturulan her bir etki alanı ad etiketi kendi Azure konumunda benzersiz olmalıdır.  
>

### <a name="virtual-machines"></a>Sanal makineler
Genel bir IP adresini bir [Windows](../virtual-machines/windows/about.md) veya [Linux](../virtual-machines/virtual-machines-linux-about.md) VM’nin **ağ arabirimine** giderek VM ile ilişkilendirebilirsiniz. Bir VM’nin birden çok ağ arabirimine sahip olması durumunda adresi yalnızca *birincil* ağ arabirimine atayabilirsiniz. Bir VM’ye dinamik veya statik bir genel IP adresi atayabilirsiniz.

### <a name="internet-facing-load-balancers"></a>İnternet'e yönelik yük dengeleyiciler
Genel bir IP adresini bir **Azure Load Balancer**’ın [ön uç](../load-balancer/load-balancer-overview.md) yapılandırmasına atayarak yük dengeleyiciyle ilişkilendirebilirsiniz. Bu genel IP adresi yükü dengelenmiş bir sanal IP adresi (VIP) olarak işlev görür. Bir yük dengeleyici ön ucuna dinamik veya statik bir genel IP adresi atayabilirsiniz. Ayrıca, SSL tabanlı web sitelerinde çok kiracılı bir ortam gibi [çok VIP’li](../load-balancer/load-balancer-multivip.md) senaryoları mümkün kılmak için bir yük dengeleyici ön ucuna birden çok genel IP adresi de atayabilirsiniz.

### <a name="vpn-gateways"></a>VPN ağ geçitleri
[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md), bir Azure sanal ağını (VNet) diğer Azure VNet’lerine veya bir şirket içi ağa bağlamak için kullanılır. Uzak ağla iletişim kurabilmesi için **IP yapılandırmasına** genel bir IP adresi atamanız gerekir. Şu anda VPN ağ geçitlerine yalnızca *dinamik* genel IP adresleri atayabilirsiniz.

### <a name="application-gateways"></a>Uygulama ağ geçitleri
Genel bir IP adresini bir Azure **Application Gateway**’in [ön uç](../application-gateway/application-gateway-introduction.md) yapılandırmasına atayarak ağ geçidiyle ilişkilendirebilirsiniz. Bu genel IP adresi yükü dengelenmiş bir VIP olarak işlev görür. Şu anda bir uygulama ağ geçidi ön uç yapılandırmasına yalnızca *dinamik* genel IP adresleri atayabilirsiniz.

### <a name="at-a-glance"></a>Bir bakışta
Aşağıdaki tabloda, genel bir IP adresinin en üst düzey bir kaynakla tam olarak hangi özellik üzerinden ilişkilendirilebileceği ve kullanılabilecek olası ayırma yöntemleri (dinamik veya statik) gösterilmiştir.

| En üst düzey kaynak | IP Adresi ilişkilendirme | Dinamik | Statik |
| --- | --- | --- | --- |
| Sanal makine |Ağ arabirimi |Evet |Evet |
| Yük dengeleyici |Ön uç yapılandırması |Evet |Evet |
| VPN ağ geçidi |Ağ geçidi IP yapılandırması |Evet |Hayır |
| Uygulama ağ geçidi |Ön uç yapılandırması |Evet |Hayır |

## <a name="private-ip-addresses"></a>Özel IP adresleri
Özel IP adresleri, Azure kaynaklarının İnternet’ten erişilebilen bir IP adresi gerekmeksizin bir [sanal ağdaki](virtual-networks-overview.md) diğer kaynaklarla veya bir VPN ağ geçidi ya da ExpressRoute bağlantı hattı üzerinden bir şirket içi ağ ile iletişim kurmasına imkan tanır.

Azure Resource Manager dağıtım modelinde, özel IP adresleri aşağıdaki Azure kaynağı türleriyle ilişkilendirilir:

* VM'ler
* İç yük dengeleyiciler (ILB)
* Uygulama ağ geçitleri

### <a name="allocation-method"></a>Ayırma yöntemi
Kaynağın bağlı olduğu alt ağın adres aralığından özel bir IP adresi ayrılır. Alt ağın adres aralığı VNet’in adres aralığının bir kısmıdır.

Özel IP adresi ayırmak için kullanılan iki yöntem vardır: *Dinamik* veya *statik*. Varsayılan ayırma yöntemi olan *dinamik* yönteminde, IP adresi kaynağın alt ağından otomatik olarak (DHCP kullanılarak) ayrılır. Kaynağı durdurup başlattığınızda bu IP adresi değişebilir.

IP adresinin aynı kalmasını sağlamak için ayırma yöntemini *statik* olarak ayarlayabilirsiniz. Bu durumda, kaynağın alt ağının bir parçası olan geçerli bir IP adresi de sağlamalısınız.

Statik özel IP adresleri yaygın olarak şunlar için kullanılır:

* Etki alanı denetleyicisi veya DNS sunucusu olarak çalışan VM’ler.
* IP adreslerini kullanan güvenlik duvarı kuralları gerektiren kaynaklar.
* Bir IP adresi üzerinden diğer uygulamalar/kaynaklar tarafından erişilen kaynaklar.

### <a name="virtual-machines"></a>Sanal makineler
Bir [Windows](../virtual-machines/windows/about.md) veya [Linux](../virtual-machines/virtual-machines-linux-about.md) VM’nin **ağ arabirimine** özel bir IP adresi atanır. Bir VM’nin birden çok ağ arabirimi olması durumunda her arabirime özel bir IP adresi atanır. Bir ağ arabirimi için ayırma yöntemini dinamik veya statik olarak belirtebilirsiniz.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>İç DNS ana bilgisayar adı çözümlemesi (VM’ler için)
Siz açıkça özel DNS sunucuları yapılandırmadığınız sürece, tüm Azure VM’leri varsayılan olarak [Azure tarafından yönetilen DNS sunucuları](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) ile yapılandırılmıştır. Bu DNS sunucuları aynı VNet’te bulunan VM’ler için iç ad çözümleme sağlar.

Bir VM oluşturduğunuzda, Azure tarafından yönetilen DNS sunucularına ana bilgisayar adı için özel IP adresine yönelik bir eşleme eklenir. Bir VM’nin birden çok ağ arabirimi olması durumunda, ana bilgisayar adı birincil ağ arabiriminin özel IP adresine eşlenir.

Azure tarafından yönetilen DNS sunucularıyla yapılandırılan VM’ler kendi VNet’indeki tüm VM’lerin ana bilgisayar adlarını bu VM’lerin özel IP adreslerine çözümleyebilir.

### <a name="internal-load-balancers-ilb--application-gateways"></a>İç yük dengeleyiciler (ILB) ve Uygulama ağ geçitleri
Bir [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md)’ın (ILB) veya [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)’in **ön uç** yapılandırmasına özel bir IP adresi atayabilirsiniz. Özel IP adresi, yalnızca kendi sanal ağı (VNet) ve VNet’e bağlı uzak ağlar tarafından erişilebilen bir iç uç nokta olarak çalışır. Ön uç yapılandırmasına dinamik veya statik bir özel IP adresi atayabilirsiniz.

### <a name="at-a-glance"></a>Bir bakışta
Aşağıdaki tabloda, özel bir IP adresinin en üst düzey bir kaynakla tam olarak hangi özellik üzerinden ilişkilendirilebileceği ve kullanılabilecek olası ayırma yöntemleri (dinamik veya statik) gösterilmiştir.

| En üst düzey kaynak | IP adresi ilişkilendirme | Dinamik | Statik |
| --- | --- | --- | --- |
| Sanal makine |Ağ arabirimi |Evet |Evet |
| Yük dengeleyici |Ön uç yapılandırması |Evet |Evet |
| Uygulama ağ geçidi |Ön uç yapılandırması |Evet |Evet |

## <a name="limits"></a>Sınırlar
IP adresleme için uygulanan limitler, Azure’daki tüm [ağ limitlerinin](../azure-subscription-service-limits.md#networking-limits) belirtildiği dizide bulunabilir. Bu kısıtlamalar bölge ve abonelik başınadır. [Destek ekibiyle iletişime geçerek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) varsayılan limitleri iş ihtiyaçlarınıza göre en üst düzeye çıkarabilirsiniz.

## <a name="pricing"></a>Fiyatlandırma
Genel IP adreslerinin nominal bir ücreti olabilir. Azure'da IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını inceleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure portalını kullanarak statik genel IP’li VM dağıtma](virtual-network-deploy-static-pip-arm-portal.md)
* [Şablon kullanarak statik genel IP’li VM dağıtma](virtual-network-deploy-static-pip-arm-template.md)
* [Azure portalını kullanarak statik özel IP adresli VM dağıtma](virtual-networks-static-private-ip-arm-pportal.md)

