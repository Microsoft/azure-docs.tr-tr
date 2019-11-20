---
title: Azure 'da IP adresi türleri
titlesuffix: Azure Virtual Network
description: Azure’da genel ve özel IP adresleri hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: kumud
ms.openlocfilehash: 40797c1b46bc88ecdaab6e28ef64f05a73e3ba8d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495909"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Azure’da IP adresi türleri ve ayırma yöntemleri

Diğer Azure kaynaklarıyla, şirket içi ağınızla ve İnternet’le iletişim kurmak için Azure kaynaklarına IP adresleri atayabilirsiniz. Azure'da kullanabileceğiniz iki tür IP adresi vardır:

* **Genel IP adresleri**: Genel kullanıma yönelik Azure hizmetleri dahil olmak üzere Internet ile iletişim için kullanılır.
* **Özel IP adresleri**: Bir Azure sanal ağı (VNet) içinde ve şirket içi ağınızı Azure'a genişletmek için bir VPN ağ geçidi veya ExpressRoute bağlantı hattı kullanıyorsanız şirket içi ağınız içinde iletişim kurmak için kullanılır.

Ayrıca bir genel IP ön eki aracılığıyla statik genel IP adreslerinden oluşan bitişik bir aralık oluşturabilirsiniz. [Genel IP ön eki hakkında bilgi edinin.](public-ip-address-prefix.md)

> [!NOTE]
> Azure’da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Resource Manager ve klasik](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Bu makale, Microsoft’un çoğu yeni dağıtım için [klasik dağıtım modeli](virtual-network-ip-addresses-overview-classic.md) yerine önerdiği Resource Manager dağıtım modelini açıklamaktadır.
> 

Klasik dağıtım modeliyle ilgili bilginiz varsa [klasik ve Resource Manager IP adreslemesi arasındaki farkları](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) inceleyin.

## <a name="public-ip-addresses"></a>Genel IP adresleri

Genel IP adresleri, İnternet kaynaklarının Azure kaynakları ile gelen iletişimi kurmasına izin verir. Genel IP adresleri ayrıca Azure kaynaklarının İnternet ve kaynağa atanmış bir IP adresi ile genel kullanıma yönelik Azure hizmetleri ile giden iletişimi kurmasını sağlar. Adres sizin atamayı kaldırmanıza kadar kaynağa ayrılmıştır. Bir genel IP adresi herhangi bir kaynağa atanmamışsa, kaynak yine de İnternet ile giden iletişim kurabilir, ancak Azure kaynağa ayrılmamış kullanılabilir bir IP adresini dinamik olarak atar. Azure’da giden bağlantılar hakkında daha fazla bilgi için bkz. [Giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Azure Resource Manager’daki bir [genel IP](virtual-network-public-ip-address.md) adresi, kendi özelliklerine sahip olan bir kaynaktır. Genel bir IP adresini ilişkilendirebileceğiniz kaynakların bazıları şunlardır:

* Sanal makine ağ arabirimleri
* İnternet'e yönelik yük dengeleyiciler
* VPN ağ geçitleri
* Uygulama ağ geçitleri

### <a name="ip-address-version"></a>IP adresi sürümü

Genel IP adresleri IPv4 veya IPv6 adresiyle oluşturulur. 

### <a name="sku"></a>SKU

Genel IP adresleri aşağıdaki SKU'lardan biriyle oluşturulur:

>[!IMPORTANT]
> Yük dengeleyici ve genel IP kaynakları için eşleşen SKU'lar kullanılmalıdır. Temel SKU ve standart SKU kaynaklarını bir arada kullanamazsınız. Tek başına sanal makineleri, bir kullanılabilirlik kümesi kaynağındaki sanal makineleri veya sanal makine ölçek kümesi kaynaklarını aynı anda iki SKU’ya iliştiremezsiniz.  Yeni tasarımlarda standart SKU kaynakları kullanmayı düşünmelisiniz.  Lütfen ayrıntılar için [Standart Yük Dengeleyici](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)’yi inceleyin.

#### <a name="basic"></a>Temel

SKU'ların kullanıma sunulmasından önce oluşturulan tüm genel IP adresleri Temel SKU genel IP adresleridir. SKU'ların kullanıma sunulması genel IP adresinin ait olmasını istediğiniz SKU'yu belirleme seçeneğini sunmuştur. Temel SKU adresleri:

- Statik veya dinamik atama yöntemiyle atanmıştır.
- 4-30 dakikalık, varsayılan 4 dakikalık, geçersiz giden kaynaklı Flow zaman aşımı süresi 4 dakikadan oluşan, ayarlanabilir bir gelen akış boşta kalma zaman aşımı süresi.
- Varsayılan olarak açıktır.  Ağ güvenlik grupları önerilir ancak gelen veya giden trafiği kısıtlamak için isteğe bağlıdır.
- Ağ arabirimleri, VPN ağ geçitleri, uygulama ağ geçitleri ve İnternet'e yönelik yük dengeleyiciler gibi genel IP adresi atanabilecek bir Azure kaynağına atanmıştır.
- Kullanılabilirlik bölgesi senaryolarını desteklemez.  Kullanılabilirlik bölgesi senaryolarında standart SKU genel IP 'yi kullanmanız gerekir. Kullanılabilirlik alanları hakkında daha fazla bilgi için bkz. [Kullanılabilirlik alanlarına genel bakış](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Standart Yük Dengeleyici ve Kullanılabilirlik Alanları](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standart

Standart SKU genel IP adresleri:

- Her zaman statik ayırma yöntemi kullanın.
- 4-30 dakikalık, varsayılan 4 dakikalık, geçersiz giden kaynaklı Flow zaman aşımı süresi 4 dakikadan oluşan, ayarlanabilir bir gelen akış boşta kalma zaman aşımı süresi.
- Varsayılan olarak güvenlidir ve gelen trafiğe kapalıdır. İzin verilen trafiği bir [ağ güvenlik grubu](security-overview.md#network-security-groups) ile özellikle beyaz listeye almanız gerekir.
- Ağ arabirimlerine, standart genel yük dengeleyicileri, uygulama ağ geçitlerine veya VPN ağ geçitlerine atanır. Standart Load Balancer hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Bölge varsayılan olarak yedekli ve isteğe bağlı olarak, belirli bir kullanılabilirlik alanında daha fazla ve garanti edilebilir. Kullanılabilirlik alanları hakkında daha fazla bilgi için bkz. [Kullanılabilirlik alanlarına genel bakış](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Standart Yük Dengeleyici ve Kullanılabilirlik Alanları](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Standart SKU kaynağıyla gelen iletişim, bir [ağ güvenlik grubu](security-overview.md#network-security-groups) oluşturup ilişkilendirene ve istenen gelen trafiğe açıkça izin verene kadar başarısız olur.

> [!NOTE]
> [Örnek meta veri hizmeti IDS](../virtual-machines/windows/instance-metadata-service.md)'leri kullanılırken yalnızca temel SKU 'Su olan genel IP adresleri kullanılabilir. Standart SKU desteklenmiyor.

### <a name="allocation-method"></a>Ayırma yöntemi

Temel ve standart SKU genel IP adresleri *statik* ayırma yöntemini destekler.  Kaynak oluşturulduğunda kaynağa bir IP adresi atanır ve kaynak silindiğinde bu IP adresi serbest bırakılır.

Temel SKU genel IP adresleri, ayırma yöntemi belirtilmediğinde varsayılan değer olan *dinamik* ayırma yöntemini de destekler.  Genel bir ortak IP adresi kaynağı için *Dinamik* ayırma yönteminin seçilmesi, IP adresinin kaynak oluşturma sırasında **ayrılmadığı** anlamına gelir.  Genel IP adresi, genel IP adresini bir sanal makine ile ilişkilendirdiğinizde veya temel yük dengeleyici arka uç havuzuna ilk sanal makine örneğini yerleştirdiğinizde tahsis edilir.   Kaynağı durdurduğunuzda (veya sildiğinizde) IP adresi serbest kalır.  Örneğin IP adres kaynak A tarafından serbest bırakıldıktan sonra farklı bir kaynağa atanabilir. Kaynak A durdurulmuş haldeyken IP adresi farklı bir kaynağa atanmışsa kaynak A yeniden başlatıldığında farklı bir IP adresi atanır. Temel bir genel IP adresi kaynağının ayırma yöntemini *statik* yerine *dinamik* olacak şekilde değiştirirseniz adres serbest bırakılır. İlişkili kaynağın IP adresinin aynı kalmasını sağlamak için ayırma yöntemini açıkça *statik* olarak ayarlayabilirsiniz. Statik IP adresi anında atanır.

> [!NOTE]
> Ayırma yöntemini *statik* olarak ayarladığınızda bile genel IP adresi kaynağına atanan gerçek IP adresini belirtemezsiniz. Azure IP adresini kaynağın oluşturulduğu Azure konumundaki kullanılabilir IP adreslerinden oluşan bir havuzdan atar.
>

Statik genel IP adresleri yaygın olarak aşağıdaki senaryolarda kullanılır:

* Azure kaynaklarınızla iletişim kurabilmesi için güvenlik duvarı ayarlarını güncelleştirmeniz gerektiğinde.
* IP adresindeki bir değişikliğin A kayıtlarının güncelleştirilmesini gerektireceği DNS ad çözümlemesi.
* Azure kaynaklarınız, IP adresi tabanlı bir güvenlik yöntemi kullanan diğer uygulama ve hizmetlerle iletişim kurar.
* Bir IP adresine bağlı SSL sertifikalarınız.

> [!NOTE]
> Azure genel IP adreslerini Azure bulutundaki her bir bölgeye atanmış olan benzersiz aralıktan atar. Azure [Genel](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [Çin](https://www.microsoft.com/download/details.aspx?id=57062) ve [Almanya](https://www.microsoft.com/download/details.aspx?id=57064) bulutları için bu aralıkların (ön ekler) listesini indirebilirsiniz.
>

### <a name="dns-hostname-resolution"></a>DNS ana bilgisayar adı çözümlemesi
Bir genel IP kaynağı için DNS etki alanı ad etiketi belirtebilirsiniz; bu durumda, Azure tarafından yönetilen DNS sunucularında genel IP adresine yönelik olarak *etkialanıadetiketi*.*konum*.cloudapp.azure.com için bir eşleme oluşturulur. Örneğin, **Batı ABD** Azure *konumunda* **etkialanıadetiketi** olarak *contoso* değerini içeren bir genel IP kaynağı oluşturursanız, **contoso.westus.cloudapp.azure.com** şeklindeki tam etki alanı adı (FQDN), kaynağın genel IP adresi olarak çözümlenir.

> [!IMPORTANT]
> Oluşturulan her bir etki alanı ad etiketi kendi Azure konumunda benzersiz olmalıdır.  
>

### <a name="dns-best-practices"></a>DNS En Iyi yöntemleri
Farklı bir bölgeye geçiş yapmanız gerekiyorsa, genel IP adresinizin FQDN 'sini geçiremezsiniz. En iyi uygulama olarak, Azure 'da genel IP adresini işaret eden özel bir etki alanı CNAME kaydı oluşturmak için FQDN 'yi kullanabilirsiniz. Farklı bir genel IP 'ye taşımanız gerekiyorsa, FQDN 'yi yeni adresle el ile güncelleştirmek zorunda kalmak yerine CNAME kaydına yönelik bir güncelleştirme gerekir. DNS kaydınız için [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) veya HARICI bir DNS sağlayıcısı kullanabilirsiniz. 

### <a name="virtual-machines"></a>Sanal makineler

Genel bir IP adresini bir [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinenin **ağ arabirimine** giderek ilişkilendirebilirsiniz. Bir sanal makineye dinamik veya statik bir genel IP adresi atayabilirsiniz. [IP adreslerini ağ arabirimlerine atama](virtual-network-network-interface-addresses.md) hakkında daha fazla bilgi edinin.

### <a name="internet-facing-load-balancers"></a>İnternet'e yönelik yük dengeleyiciler

Herhangi bir [SKU](#sku) ile oluşturulmuş genel bir IP adresini bir [Azure Load Balancer](../load-balancer/load-balancer-overview.md)'ın **ön uç** yapılandırmasına atayarak yük dengeleyiciyle ilişkilendirebilirsiniz. Genel IP adresi yükü dengelenmiş bir sanal IP adresi (VIP) olarak işlev görür. Bir yük dengeleyici ön ucuna dinamik veya statik bir genel IP adresi atayabilirsiniz. Ayrıca, SSL tabanlı web sitelerinde çok kiracılı bir ortam gibi [çok VIP’li](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) senaryoları mümkün kılmak için bir yük dengeleyici ön ucuna birden çok genel IP adresi de atayabilirsiniz. Azure yük dengeleyici SKU'ları hakkında daha fazla bilgi edinmek için bkz. [Azure yük dengeleyici standart SKU'su](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="vpn-gateways"></a>VPN ağ geçitleri

[Azure VPN Ağ Geçidi](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json), bir Azure sanal ağını diğer Azure sanal ağlarına veya bir şirket içi ağa bağlar. VPN Ağ Geçidinin uzak ağ ile iletişim kurmasını sağlamak için genel IP adresi atanır. *Dinamik* genel IP adreslerini yalnızca VPN ağ geçitlerine atayabilirsiniz.

### <a name="application-gateways"></a>Uygulama ağ geçitleri

Genel bir IP adresini bir Azure [Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)’in **ön uç** yapılandırmasına atayarak ağ geçidiyle ilişkilendirebilirsiniz. Bu genel IP adresi yükü dengelenmiş bir VIP olarak işlev görür. Yalnızca *bir uygulama* ağ geçidi v1 ön uç yapılandırmasına ve yalnızca *statik* standart SKU adresini bir v2 ön uç yapılandırmasına atayabilirsiniz.

### <a name="at-a-glance"></a>Bir bakışta
Aşağıdaki tabloda, genel bir IP adresinin en üst düzey bir kaynakla tam olarak hangi özellik üzerinden ilişkilendirilebileceği ve kullanılabilecek olası ayırma yöntemleri (dinamik veya statik) gösterilmiştir.

| En üst düzey kaynak | IP Adresi ilişkilendirme | Dinamik | Statik |
| --- | --- | --- | --- |
| Sanal makine |Ağ arabirimi |Evet |Evet |
| İnternet'e yönelik yük dengeleyici |Ön uç yapılandırması |Evet |Evet |
| VPN ağ geçidi |Ağ geçidi IP yapılandırması |Evet |Hayır |
| Uygulama ağ geçidi |Ön uç yapılandırması |Evet (yalnızca v1) |Evet (yalnızca v2) |

## <a name="private-ip-addresses"></a>Özel IP adresleri
Özel IP adresleri, Azure kaynaklarının İnternet’ten erişilebilen bir IP adresi gerekmeksizin bir [sanal ağdaki](virtual-networks-overview.md) diğer kaynaklarla veya bir VPN ağ geçidi ya da ExpressRoute bağlantı hattı üzerinden bir şirket içi ağ ile iletişim kurmasına imkan tanır.

Azure Resource Manager dağıtım modelinde, özel IP adresleri aşağıdaki Azure kaynağı türleriyle ilişkilendirilir:

* Sanal makine ağ arabirimleri
* İç yük dengeleyiciler (ILB)
* Uygulama ağ geçitleri

### <a name="allocation-method"></a>Ayırma yöntemi

Kaynağın dağıtıldığı sana alt ağın adres aralığından özel bir IP adresi ayrılır. Azure her alt ağ adres aralığında ilk dört adresi ayırır, bu nedenle adresler kaynaklara atanamaz. Örneğin, alt ağın adres aralığı 10.0.0.0/16 ise, 10.0.0.0-10.0.0.3 ve 10.0.255.255 adresleri kaynaklara atanamaz. Alt ağın adres aralığı aynı anda yalnızca bir kaynağa atanabilir. 

Özel IP adresi ayırmak için kullanılan iki yöntem vardır:

- **Dinamik**: Azure alt ağın adres aralığında sonraki kullanılabilir atanmamış veya ayrılmamış IP adresini atar. Örneğin Azure, 10.0.0.4-10.0.0.9 aralığındaki adresler diğer kaynaklara zaten atanmışsa 10.0.0.10 adresini yeni bir kaynağa atar. Dinamik, varsayılan ayırma yöntemidir. Dinamik IP adresleri bir kez atandıktan sonra, ancak ağ arabirimi silinirse, aynı sanal ağ içinde farklı bir alt ağa atanırsa veya ayırma yöntemi statik olarak değiştirilip farklı bir IP adresi belirtilirse serbest bırakılır. Varsayılan olarak, dinamik olan ayırma yöntemini statik olarak değiştirdiğinizde Azure dinamik olarak atanmış önceki adresi statik adres olarak atar.
- **Statik**: Alt ağın adres aralığında sonraki kullanılabilir atanmamış veya ayrılmamış IP adresini seçip atarsınız. Örneğin, bir alt ağın adres aralığı 10.0.0.0/16 ise ve 10.0.0.4-10.0.0.9 adresleri diğer kaynaklara zaten atanmışsa, 10.0.0.10 - 10.0.255.254 aralığındaki herhangi bir adresi atayabilirsiniz. Statik adresler ancak ağ arabirimi silindiğinde serbest bırakılır. Ayırma yöntemini dinamik olarak değiştirirseniz, Azure daha önce statik IP adresi olarak atanmış olan adresi dinamik adres olarak atar. Bu adres, alt ağ adres aralığında bir sonraki kullanılabilir adres olmayabilir. Ağ arabirimi aynı sanal ağ içinde farklı bir alt ağa atandığında da adres değişir; ama ağ arabirimini farklı bir alt ağa atamak için, önce statik ayırma yöntemini dinamik olarak değiştirmeniz gerekir. Ağ arabirimini farklı bir alt ağa atadıktan sonra, ayırma yöntemini yine statik yapabilir ve yeni alt ağ adres aralığından bir IP adresi atayabilirsiniz.

### <a name="virtual-machines"></a>Sanal makineler

**Windows** veya [Linux](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesinin bir veya birden çok [ağ arabirimine](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bir veya birden çok özel IP adresi atanır. Her özel IP adresi için ayırma yöntemini dinamik veya statik olarak belirtebilirsiniz.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>İç DNS ana bilgisayar adı çözümlemesi (sanal makineler için)

Siz açıkça özel DNS sunucuları yapılandırmadığınız sürece, tüm Azure sanal makineleri varsayılan olarak [Azure tarafından yönetilen DNS sunucuları](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) ile yapılandırılmıştır. Bu DNS sunucuları aynı sanal ağda bulunan sanal makineler için iç ad çözümleme sağlar.

Bir sanal makine oluşturduğunuzda, Azure tarafından yönetilen DNS sunucularına ana bilgisayar adı için özel IP adresine yönelik bir eşleme eklenir. Sanal makinenin birden çok ağ arabirimi veya ağ arabirimi için birden çok IP yapılandırması olması durumunda, ana bilgisayar adı birincil ağ arabirimine ilişkin birincil IP yapılandırmasının özel IP adresiyle eşlenir.

Azure tarafından yönetilen DNS sunucularıyla yapılandırılan sanal makineler kendi sanal ağındaki tüm sanal makinelerin ana bilgisayar adlarını bu sanal makinelerin özel IP adreslerine çözümleyebilir. Bağlı sanal ağlarda sanal makinelerin ana bilgisayar adlarını çözümlemek için, özel DNS sunucusu kullanmalısınız.

### <a name="internal-load-balancers-ilb--application-gateways"></a>İç yük dengeleyiciler (ILB) ve Uygulama ağ geçitleri

Bir **Azure Internal Load Balancer**’ın (ILB) veya [Azure Application Gateway](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)’in [ön uç](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) yapılandırmasına özel bir IP adresi atayabilirsiniz. Özel IP adresi, yalnızca kendi sanal ağı ve sanal ağa bağlı uzak ağlar tarafından erişilebilen bir iç uç nokta olarak çalışır. Ön uç yapılandırmasına dinamik veya statik bir özel IP adresi atayabilirsiniz.

### <a name="at-a-glance"></a>Bir bakışta
Aşağıdaki tabloda, özel bir IP adresinin en üst düzey bir kaynakla tam olarak hangi özellik üzerinden ilişkilendirilebileceği ve kullanılabilecek olası ayırma yöntemleri (dinamik veya statik) gösterilmiştir.

| En üst düzey kaynak | IP adresi ilişkilendirme | Dinamik | Statik |
| --- | --- | --- | --- |
| Sanal makine |Ağ arabirimi |Evet |Evet |
| Yük dengeleyici |Ön uç yapılandırması |Evet |Evet |
| Uygulama ağ geçidi |Ön uç yapılandırması |Evet |Evet |

## <a name="limits"></a>Sınırlar
IP adresleme için uygulanan limitler, Azure’daki tüm [ağ limitlerinin](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) belirtildiği dizide bulunabilir. Limitler bölge ve abonelik başınadır. [Destek ekibiyle iletişime geçerek](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) varsayılan limitleri iş ihtiyaçlarınıza göre en üst düzeye çıkarabilirsiniz.

## <a name="pricing"></a>Fiyatlandırma
Genel IP adreslerinin nominal bir ücreti olabilir. Azure'da IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını inceleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure portalını kullanarak statik genel IP’li VM dağıtma](virtual-network-deploy-static-pip-arm-portal.md)
* [Azure portalını kullanarak statik özel IP adresli VM dağıtma](virtual-networks-static-private-ip-arm-pportal.md)
