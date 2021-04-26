---
title: Azure 'da özel IP adresleri
titlesuffix: Azure Virtual Network
description: Azure 'da özel IP adresleri hakkında bilgi edinin.
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
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223270"
---
# <a name="private-ip-addresses"></a>Özel IP adresleri
Özel IP 'Ler, Azure 'daki kaynaklar arasında iletişime izin verir. 

Kaynaklar şu olabilir:
* Azure hizmetleri:
    * Sanal makine ağ arabirimleri
    * İç yük dengeleyiciler (ILB)
    * Uygulama ağ geçitleri
* Bir [Sanal ağda](virtual-networks-overview.md).
* VPN Gateway veya ExpressRoute devresi aracılığıyla şirket içi ağ.

Özel IP 'Ler, genel IP adresi kullanılmadan bu kaynaklarla iletişime izin verir.

## <a name="allocation-method"></a>Ayırma yöntemi

Azure, kaynağın bulunduğu sanal ağ alt ağının adres aralığından kaynaklara özel IP adresleri atar.

Azure her alt ağ adres aralığında ilk dört adresi ayırır. Adresler kaynaklara atanamaz. Örneğin, alt ağın adres aralığı 10.0.0.0/16 ise, 10.0.0.0-10.0.0.3 ve 10.0.255.255 adresleri kullanılamaz. Alt ağın adres aralığı aynı anda yalnızca bir kaynağa atanabilir. 

Özel IP adresi verilen iki yöntem vardır:

- **Dinamik**: Azure alt ağın adres aralığında sonraki kullanılabilir atanmamış veya ayrılmamış IP adresini atar. Örneğin Azure, 10.0.0.4-10.0.0.9 aralığındaki adresler diğer kaynaklara zaten atanmışsa 10.0.0.10 adresini yeni bir kaynağa atar. 

    Dinamik, varsayılan ayırma yöntemidir. Atandıktan sonra dinamik IP adresleri, bir ağ arabirimi şu şekilde olursa serbest bırakılır:
    
    * Silindi
    * Aynı sanal ağ içinde farklı bir alt ağa yeniden atandı.
    * Ayırma yöntemi statik olarak değişir ve farklı bir IP adresi belirtilir. 
    
    Varsayılan olarak, dinamik olan ayırma yöntemini statik olarak değiştirdiğinizde Azure dinamik olarak atanmış önceki adresi statik adres olarak atar.

- **Statik**: Alt ağın adres aralığında sonraki kullanılabilir atanmamış veya ayrılmamış IP adresini seçip atarsınız. 

    Örneğin, bir alt ağın adres aralığı 10.0.0.0/16 ve 10.0.0.4-10.0.0.9 adresleri diğer kaynaklara atanır. 10.0.0.10-10.0.255.254 arasında herhangi bir adres atayabilirsiniz. Statik adresler ancak ağ arabirimi silindiğinde serbest bırakılır. 
    
    Ayırma yöntemi değiştirildiğinde Azure, statik IP 'yi dinamik IP olarak atar. Adres, alt ağda kullanılabilir bir sonraki olmasa bile yeniden atama gerçekleşir. Ağ arabirimi farklı bir alt ağa atandığında adres değişir.
    
    Ağ arabirimini farklı bir alt ağa atamak için, ayırma yöntemini statik iken dinamik olarak değiştirirsiniz. Ağ arabirimini farklı bir alt ağa atayın ve ardından ayırma yöntemini yeniden statik olarak değiştirin. Yeni alt ağın adres aralığından bir IP adresi atayın.
    
## <a name="virtual-machines"></a>Sanal makineler

Bir veya daha fazla özel IP adresi bir veya daha fazla **ağ arabirimine** atandı. Ağ arabirimleri bir [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makinesine atanır. Her özel IP adresi için ayırma yöntemini dinamik veya statik olarak belirtebilirsiniz.

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>İç DNS ana bilgisayar adı çözümlemesi (sanal makineler için)

Azure sanal makineler, varsayılan olarak [Azure tarafından YÖNETILEN DNS sunucuları](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) ile yapılandırılır. Özel DNS sunucularını açık bir şekilde yapılandırabilirsiniz. Bu DNS sunucuları, aynı sanal ağ içindeki sanal makineler için iç ad çözümlemesi sağlar.

Ana bilgisayar adı için bir sanal makinenin özel IP adresine yönelik bir eşleme, Azure tarafından yönetilen DNS sunucularına eklenir. 

Bir sanal makine, bir VM olduğunda ana ağ arabiriminin birincil IP 'sine eşlenir:

* Birden çok ağ arabirimi
* Birden çok IP adresi
* Her ikisi

Azure tarafından yönetilen DNS ile yapılandırılmış VM 'Ler, aynı sanal ağ içindeki ana bilgisayar adlarını çözer. Bağlı sanal ağlardaki VM 'lerin ana bilgisayar adlarını çözümlemek için özel bir DNS sunucusu kullanın.

## <a name="internal-load-balancers-ilb--application-gateways"></a>İç yük dengeleyiciler (ILB) ve Uygulama ağ geçitleri

Bir için **ön uç** yapılandırmasına özel bir IP adresi atayabilirsiniz:

* [Azure iç yük dengeleyici](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Azure Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

Bu özel IP adresi bir iç uç nokta olarak görev yapar. İç uç noktaya yalnızca kendi sanal ağı ve kendisine bağlı uzak ağlar içindeki kaynaklara erişilebilir. Dinamik veya statik bir IP atanabilir.

## <a name="at-a-glance"></a>Bir bakışta
Aşağıdaki tabloda, özel bir IP 'nin bir kaynakla ilişkilendirilebilen özelliği gösterilmektedir. 

Kullanılabilecek olası ayırma yöntemleri de görüntülenir:

* Dinamik
* Statik

| En üst düzey kaynak | IP adresi ilişkilendirme | Dinamik | Statik |
| --- | --- | --- | --- |
| Sanal makine |Ağ arabirimi |Yes |Yes |
| Yük dengeleyici |Ön uç yapılandırması |Yes |Yes |
| Uygulama ağ geçidi |Ön uç yapılandırması |Yes |Yes |

## <a name="limits"></a>Sınırlar
IP adresleme sınırları, Azure 'da [ağ için tüm sınırlar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) kümesinde bulunur. Limitler bölge ve abonelik başınadır. Varsayılan limitleri iş ihtiyaçlarınıza göre maksimum sınırlara yükseltmek için [desteğe başvurun](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="next-steps"></a>Sonraki adımlar
[Azure 'da genel IP adresleri](public-ip-addresses.md) hakkında bilgi edinin
* [Azure portalını kullanarak statik özel IP adresli VM dağıtma](virtual-networks-static-private-ip-arm-pportal.md)