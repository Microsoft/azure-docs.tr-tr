---
title: Azure’da genel IP adresleri
titlesuffix: Azure Virtual Network
description: Azure’da genel IP adresleri hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: d1d412774dc68cba0c24709c7fc8a7999e0aeab8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094770"
---
# <a name="public-ip-addresses"></a>Genel IP adresleri

Genel IP adresleri, İnternet kaynaklarının Azure kaynakları ile gelen iletişimi kurmasına izin verir. Genel IP adresleri, Azure kaynaklarının Internet ve genel kullanıma yönelik Azure hizmetleriyle iletişim kurmasını sağlar. Adres, sizin tarafınızdan atanana kadar kaynağa ayrılmıştır. Genel IP atanmış olmayan bir kaynak giden iletişim kurabilir. Azure, kaynağa adanmış olmayan kullanılabilir bir IP adresini dinamik olarak atar. Azure’da giden bağlantılar hakkında daha fazla bilgi için bkz. [Giden bağlantıları anlama](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Azure Resource Manager’daki bir [genel IP](virtual-network-public-ip-address.md) adresi, kendi özelliklerine sahip olan bir kaynaktır. Genel IP adresi kaynağını ilişkilendirebileceğiniz bazı kaynaklar:

* Sanal makine ağ arabirimleri
* İnternet'e yönelik yük dengeleyiciler
* VPN ağ geçitleri
* Uygulama ağ geçitleri
* Azure Güvenlik Duvarı

## <a name="ip-address-version"></a>IP adresi sürümü

Genel IP adresleri IPv4 veya IPv6 adresiyle oluşturulur. 

## <a name="sku"></a>SKU

SKU yükseltmesi hakkında bilgi edinmek için [genel IP yükseltmesi](../virtual-network/virtual-network-public-ip-address-upgrade.md)' ne bakın.

Genel IP adresleri aşağıdaki SKU'lardan biriyle oluşturulur:

>[!IMPORTANT]
> Yük dengeleyici ve genel IP kaynakları için eşleşen SKU 'Lar gereklidir. Temel SKU ve standart SKU kaynaklarını bir arada kullanamazsınız. Tek başına sanal makineleri, bir kullanılabilirlik kümesi kaynağındaki sanal makineleri veya sanal makine ölçek kümesi kaynaklarını aynı anda iki SKU’ya iliştiremezsiniz.  Yeni tasarımlarda standart SKU kaynakları kullanmayı düşünmelisiniz.  Lütfen ayrıntılar için [Standart Yük Dengeleyici](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)’yi inceleyin.

### <a name="standard"></a>Standart

Standart SKU genel IP adresleri:

- Her zaman statik ayırma yöntemi kullanın.
- 4-30 dakikalık, varsayılan 4 dakikalık, geçersiz giden kaynaklı Flow zaman aşımı süresi 4 dakikadan oluşan, ayarlanabilir bir gelen akış boşta kalma zaman aşımı süresi.
- Varsayılan olarak güvenli hale getirin ve gelen trafiğe kapalıdır. [Ağ güvenlik grubuyla](./network-security-groups-overview.md#network-security-groups)gelen trafik listesine izin ver.
- Ağ arabirimlerine, standart genel yük dengeleyicileri veya uygulama ağ geçitlerine atanır. Standart yük dengeleyici hakkında daha fazla bilgi için bkz. [Azure Standart Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Bölgesel olarak yedekli (tüm 3 bölgeden tanıtılan), bölgesel (önceden seçilmiş belirli bir kullanılabilirlik bölgesinde garantili) veya bölge olmayan (belirli bir önceden seçilmiş kullanılabilirlik bölgesi ile ilişkilendirilmemiş) olabilir. Kullanılabilirlik alanları hakkında daha fazla bilgi için bkz. [Kullanılabilirlik alanlarına genel bakış](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Standart Yük Dengeleyici ve Kullanılabilirlik Alanları](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Bölgesel olarak yedekli IP 'Ler, yalnızca [3 kullanılabilirlik bölgesinin canlı olduğu bölgelerde](../availability-zones/az-region.md) oluşturulabilir.** Bölgeler etkin olmadan önce oluşturulan IP 'Ler bölge yedekli olmayacaktır.
- [Bölgeler arası yük dengeleyiciler](../load-balancer/cross-region-overview.md) için her noktaya yayın ön uç IP 'si olarak kullanılabilir (Önizleme işlevselliği).
 
> [!NOTE]
> Standart SKU kaynağıyla gelen iletişim, bir [ağ güvenlik grubu](./network-security-groups-overview.md#network-security-groups) oluşturup ilişkilendirene ve istenen gelen trafiğe açıkça izin verene kadar başarısız olur.

> [!NOTE]
> [Örnek meta veri hizmeti IDS](../virtual-machines/windows/instance-metadata-service.md)'leri kullanılırken yalnızca temel SKU 'Su olan genel IP adresleri kullanılabilir. Standart SKU desteklenmiyor.

> [!NOTE]
> Standart SKU genel IP adresi kullanılırken, Tanılama ayarları kaynak dikey penceresinin altında görünmez. Standart genel IP adresi kaynağınız üzerinde günlüğe kaydetmeyi etkinleştirmek için Azure Izleyici dikey penceresinde Tanılama Ayarları ' na gidin ve IP adresi kaynağınızı seçin.

### <a name="basic"></a>Temel

SKU'ların kullanıma sunulmasından önce oluşturulan tüm genel IP adresleri Temel SKU genel IP adresleridir. 

SKU 'ların kullanıma sunulmasıyla birlikte, genel IP adresinin hangi SKU 'nun olmasını istediğinizi belirtin. 

Temel SKU adresleri:

- Statik veya dinamik atama yöntemiyle atanmıştır.
- 4-30 dakikalık, varsayılan 4 dakikalık, geçersiz giden kaynaklı Flow zaman aşımı süresi 4 dakikadan oluşan, ayarlanabilir bir gelen akış boşta kalma zaman aşımı süresi.
- Varsayılan olarak açıktır.  Gelen veya giden trafiği kısıtlamak için ağ güvenlik grupları önerilir ama isteğe bağlıdır.
- Genel IP adresi atanabilen herhangi bir Azure kaynağına atanır, örneğin:
    * Ağ arabirimleri
    * VPN Ağ Geçitleri
    * Uygulama Ağ Geçitleri
    * Genel yük dengeleyiciler
- Kullanılabilirlik bölgesi senaryolarını desteklemez. Kullanılabilirlik bölgesi senaryolarında standart SKU genel IP 'yi kullanın. Kullanılabilirlik alanları hakkında daha fazla bilgi için bkz. [Kullanılabilirlik alanlarına genel bakış](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Standart Yük Dengeleyici ve Kullanılabilirlik Alanları](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Ayırma yöntemi

Temel ve standart genel IP 'Ler **statik** atamayı destekler.  Kaynağa, oluşturulduğu sırada bir IP adresi atanır. IP adresi, kaynak silindiğinde serbest bırakılır.

Temel SKU genel IP adresleri **dinamik** bir atamayı destekler. Dinamik, varsayılan atama yöntemidir. IP adresi, dinamik seçerken oluşturma sırasında **kaynağa verilmez.**

Genel IP adresi kaynağını bir ile ilişkilendirdiğinizde IP atanır:

* Sanal makine 
* İlk sanal makine, bir yük dengeleyicinin arka uç havuzuyla ilişkilendirilir.

Kaynağı durdurduğunuzda (veya sildiğinizde) IP adresi serbest kalır.  

Örneğin, **kaynak a** adlı bir kaynaktan ortak IP kaynağı serbest bırakılır. Genel IP kaynağı yeniden atandığında **kaynak a** , başlangıç sırasında farklı bir IP alır.

Ayırma yöntemi **statik** iken **dinamik** olarak değiştirildiğinde, IP adresi serbest bırakılır. İlişkili kaynağın IP adresinin aynı kalmasını sağlamak için ayırma yöntemini açıkça **statik** olarak ayarlayın. Statik IP adresi anında atanır.

> [!NOTE]
> Ayırma yöntemini **statik** olarak ayarladığınızda bile genel IP adresi kaynağına atanan gerçek IP adresini belirtemezsiniz. Azure IP adresini kaynağın oluşturulduğu Azure konumundaki kullanılabilir IP adreslerinden oluşan bir havuzdan atar.
>

Statik genel IP adresleri yaygın olarak aşağıdaki senaryolarda kullanılır:

* Azure kaynaklarınızla iletişim kurabilmesi için güvenlik duvarı ayarlarını güncelleştirmeniz gerektiğinde.
* IP adresindeki bir değişikliğin A kayıtlarının güncelleştirilmesini gerektireceği DNS ad çözümlemesi.
* Azure kaynaklarınız, IP adresi tabanlı bir güvenlik yöntemi kullanan diğer uygulama ve hizmetlerle iletişim kurar.
* Bir IP adresine bağlı olan TLS/SSL sertifikalarını kullanırsınız.

> [!NOTE]
> Azure genel IP adreslerini Azure bulutundaki her bir bölgeye atanmış olan benzersiz aralıktan atar. Azure [Genel](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [Çin](https://www.microsoft.com/download/details.aspx?id=57062) ve [Almanya](https://www.microsoft.com/download/details.aspx?id=57064) bulutları için bu aralıkların (ön ekler) listesini indirebilirsiniz.
>

## <a name="dns-hostname-resolution"></a>DNS ana bilgisayar adı çözümlemesi

Genel IP kaynağı için bir DNS etki alanı adı etiketi belirtme seçeneğini belirleyin. 

Bu seçim **etkialanıadetiketi** için bir eşleme oluşturur. Azure tarafından yönetilen DNS 'de **Location**. CLOUDAPP.Azure.com genel IP 'ye. 

Örneğin, ile genel IP oluşturma:

* **etkialanıadetiketi** olarak **contoso**
* **Batı ABD** Azure **konumu**

**Contoso.westus.cloudapp.Azure.com** tam etki alanı adı (FQDN), KAYNAĞıN genel IP adresi olarak çözümlenir.

> [!IMPORTANT]
> Oluşturulan her bir etki alanı ad etiketi kendi Azure konumunda benzersiz olmalıdır.  
>

## <a name="dns-recommendations"></a>DNS önerileri

Bir bölge taşıma gerekiyorsa, genel IP 'nizin FQDN 'sini geçiremezsiniz. Genel IP adresini işaret eden özel bir CNAME kaydı oluşturmak için FQDN 'yi kullanın. 

Farklı bir genel IP 'ye taşıma gerekiyorsa, FQDN 'yi güncelleştirmek yerine CNAME kaydını güncelleştirin.

DNS kaydınız için [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) veya HARICI bir DNS sağlayıcısı kullanabilirsiniz. 

## <a name="virtual-machines"></a>Sanal makineler

Genel bir IP adresini bir [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinenin **ağ arabirimine** giderek ilişkilendirebilirsiniz. 

Genel IP adresi için **dinamik** veya **statik** öğesini seçin. [IP adreslerini ağ arabirimlerine atama](virtual-network-network-interface-addresses.md) hakkında daha fazla bilgi edinin.

## <a name="internet-facing-load-balancers"></a>İnternet'e yönelik yük dengeleyiciler

[SKU](#sku) 'NUN genel IP adresini, yük dengeleyici **ön uç** yapılandırmasına atayarak bir [Azure Load Balancer](../load-balancer/load-balancer-overview.md)ile ilişkilendirebilirsiniz. Genel IP, yük dengeli bir IP işlevi görür. 

Bir yük dengeleyici ön ucuna dinamik veya statik bir genel IP adresi atayabilirsiniz. Yük dengeleyici ön ucuna birden çok genel IP adresi atayabilirsiniz. Bu yapılandırma, TLS tabanlı Web siteleriyle çok kiracılı bir ortam gibi [çok VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) senaryolarına izin vermez. 

Azure yük dengeleyici SKU'ları hakkında daha fazla bilgi edinmek için bkz. [Azure yük dengeleyici standart SKU'su](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="vpn-gateways"></a>VPN ağ geçitleri

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , bir Azure sanal ağını şu şekilde bağlar:

* Azure sanal ağları
* Şirket içi ağ (lar). 

Uzak ağla iletişimi etkinleştirmek için VPN Gateway bir genel IP adresi atanır. 

* Bir VPNGw 1-5 SKU ön uç yapılandırmasına **dinamik** temel genel IP atayın.
* VPNGwAZ 1-5 SKU ön uç yapılandırmasına **statik** bir standart genel IP adresi atayın.

## <a name="application-gateways"></a>Uygulama ağ geçitleri

Genel bir IP adresini bir Azure **Application Gateway**’in [ön uç](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) yapılandırmasına atayarak ağ geçidiyle ilişkilendirebilirsiniz. 

* Bir Application Gateway v1 ön uç yapılandırmasına **dinamik** temel genel IP atayın. 
* V2 ön uç yapılandırmasına **statik** standart genel IP adresi atayın.

## <a name="azure-firewall"></a>Azure Güvenlik Duvarı

[Azure Güvenlik Duvarı](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , abonelikler ve sanal ağlar arasında uygulama ve ağ bağlantısı ilkeleri oluşturmanıza, zorlamanıza ve günlüğe kaydetmenize olanak tanır.

Yalnızca **statik** standart genel IP adreslerini bir güvenlik duvarıyla ilişkilendirebilirsiniz. Bu, dışarıdaki güvenlik duvarlarının sanal ağınızdan kaynaklanan trafiği belirlemesine izin verir. 


## <a name="at-a-glance"></a>Bir bakışta

Aşağıdaki tabloda, genel bir IP 'nin en üst düzey bir kaynakla ve olası ayırma yöntemleriyle ilişkilendirilebilen özelliği gösterilmektedir.

| En üst düzey kaynak | IP Adresi ilişkilendirme | Dinamik | Statik |
| --- | --- | --- | --- |
| Sanal makine |Ağ arabirimi |Yes |Yes |
| İnternet'e yönelik yük dengeleyici |Ön uç yapılandırması |Yes |Yes |
| VPN ağ geçidi |Ağ geçidi IP yapılandırması |Yes |Hayır |
| Uygulama ağ geçidi |Ön uç yapılandırması |Evet (yalnızca v1) |Evet (yalnızca v2) |
| Azure Güvenlik Duvarı | Ön uç yapılandırması | Hayır | Yes|

## <a name="limits"></a>Sınırlar

IP adresleme sınırları, Azure 'da [ağ için tüm sınırlar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) kümesinde listelenmiştir. 

Limitler bölge ve abonelik başınadır. Varsayılan limitleri iş ihtiyaçlarınıza göre maksimum sınırlara yükseltmek için [desteğe başvurun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="pricing"></a>Fiyatlandırma

Genel IP adreslerinin nominal bir ücreti olabilir. Azure'da IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırması](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını inceleyin.

## <a name="next-steps"></a>Sonraki adımlar
* [Azure 'Da özel IP adresleri](private-ip-addresses.md) hakkında bilgi edinin
* [Azure portalını kullanarak statik genel IP ile VM dağıtma](virtual-network-deploy-static-pip-arm-portal.md)
