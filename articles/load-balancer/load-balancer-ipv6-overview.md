---
title: IPv6-Azure Load Balancer genel bakış
description: Bu öğrenme yoluyla, Azure Load Balancer ve yük dengeli VM 'Ler için IPv6 desteği ile çalışmaya başlayın.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, azure yük dengeleyici, ikili yığın, genel IP, yerel IPv6, mobil veya IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 07dd3ee507f31099e32f18143d2beedf76e83c36
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077017"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Azure Load Balancer için IPv6 'ya genel bakış


>[!NOTE] 
>Azure Load Balancer iki farklı türü destekler: Temel ve Standart. Bu makalede Temel Yük Dengeleyici anlatılmaktadır. Standard Load Balancer hakkında daha fazla bilgi için bkz. [Standard Load Balancer’a genel bakış](load-balancer-standard-overview.md).

Temel SKU Internet 'e yönelik yük dengeleyiciler bir IPv6 adresi ile dağıtılabilir. IPv4 bağlantısına ek olarak, bu özellik aşağıdaki özellikleri sağlar:

* Yük dengeleyici aracılığıyla genel Internet istemcileri ve Azure sanal makineleri (VM 'Ler) arasındaki yerel uçtan uca IPv6 bağlantısı.
* VM 'Ler ve genel Internet IPv6 özellikli istemciler arasındaki yerel uçtan uca IPv6 giden bağlantısı.

Aşağıdaki resimde Azure Load Balancer IPv6 işlevselliği gösterilmektedir.

![IPv6 ile Azure Load Balancer](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Bir IPv4 veya IPv6 etkin Internet istemcisi dağıtıldıktan sonra, Azure Internet 'e yönelik Load Balancer genel IPv4 veya IPv6 adresleriyle (veya ana bilgisayar adları) iletişim kurabilir. Yük dengeleyici, IPv6 paketlerini ağ adresi çevirisi (NAT) kullanarak VM 'lerin özel IPv6 adreslerine yönlendirir. IPv6 Internet istemcisi, VM 'lerin IPv6 adresiyle doğrudan iletişim kuramaz.

## <a name="features"></a>Özellikler

Azure Resource Manager aracılığıyla dağıtılan VM 'Ler için yerel IPv6 desteği şunları sağlar:

1. Internet 'te IPv6 istemcileri için yük dengeli IPv6 Hizmetleri
2. VM 'lerde yerel IPv6 ve IPv4 uç noktaları ("çift yığılmış")
3. Gelen ve giden tarafından başlatılan yerel IPv6 bağlantıları
4. TCP, UDP ve HTTP gibi desteklenen protokoller, eksiksiz bir hizmet mimarisi yelpazesi sağlar

## <a name="benefits"></a>Avantajlar

Bu işlevsellik aşağıdaki önemli avantajları sağlar:

* Yeni uygulamalara yalnızca IPv6 istemcileri tarafından erişilebilmesini gerektiren kamu düzenlemelerine uygun
* Mobil ve nesnelerin Interneti (ıOT) geliştiricilerinin büyüyen mobil & ıOT pazarlarına yönelik olarak çift yığılmış (IPv4 + IPv6) Azure sanal makinelerini kullanmasını sağlama

## <a name="details-and-limitations"></a>Ayrıntılar ve sınırlamalar

Ayrıntılar

* Azure DNS hizmeti hem IPv4 A hem de IPv6 AAAA ad kayıtlarını içerir ve yük dengeleyici için her iki kayıtla da yanıt verir. İstemci, iletişim kurmak için kullanılacak adresi (IPv4 veya IPv6) seçer.
* Bir VM, genel Internet IPv6 bağlantılı bir cihazla bağlantı başlattığında, sanal makinenin kaynak IPv6 adresi, yük dengeleyicinin genel IPv6 adresine çevrilen ağ adresidir (NAT).
* Linux işletim sistemini çalıştıran VM 'Ler DHCP aracılığıyla bir IPv6 IP adresi alacak şekilde yapılandırılmalıdır. Azure galerisinde Linux görüntülerinin birçoğu, değişiklik yapılmadan IPv6 'Yı destekleyecek şekilde zaten yapılandırılmış. Daha fazla bilgi için bkz. [Linux VM 'leri Için DHCPv6 yapılandırma](load-balancer-ipv6-for-linux.md)
* Yük dengeleyicinizle bir sistem durumu araştırması kullanmayı tercih ederseniz, IPv4 araştırması oluşturun ve hem IPv4 hem de IPv6 uç noktalarıyla kullanın. VM 'nizin hizmeti kapalıysa, hem IPv4 hem de IPv6 uç noktaları, döndürme dışında alınır.

Sınırlamalar

* Azure portal IPv6 Yük Dengeleme kuralları ekleyemezsiniz. Kurallar yalnızca, CLı, PowerShell şablonu aracılığıyla oluşturulabilir.
* Mevcut VM 'Leri IPv6 adreslerini kullanacak şekilde yükseltmeyebilirsiniz. Yeni VM 'Leri dağıtmanız gerekir.
* Tek bir IPv6 adresi her VM 'de tek bir ağ arabirimine atanabilir.
* Genel IPv6 adresleri bir VM 'ye atanamaz. Yalnızca bir yük dengeleyiciye atanabilir.
* Genel IPv6 adresiniz için ters DNS aramasını yapılandıramazsınız.
* IPv6 adreslerine sahip VM 'Ler bir Azure bulut hizmeti 'nin üyesi olamaz. Bunlar, bir Azure sanal ağına (VNet) bağlanabilir ve IPv4 adreslerinin üzerinden birbirleriyle iletişim kurabilir.
* Özel IPv6 adresleri bir kaynak grubundaki ayrı sanal makinelere dağıtılabilir, ancak ölçek kümeleri aracılığıyla bir kaynak grubuna dağıtılamaz.
* Azure VM 'Leri, IPv6 üzerinden diğer VM 'lere, diğer Azure hizmetlerine veya şirket içi cihazlara bağlanamaz. Yalnızca IPv6 üzerinden Azure yük dengeleyicisiyle iletişim kurabilir. Ancak, IPv4 kullanarak bu diğer kaynaklarla iletişim kurabilir.
* IPv4 için ağ güvenlik grubu (NSG) koruması, çift yığın (IPv4 + IPv6) dağıtımlarında desteklenir. NSG 'ler IPv6 uç noktaları için geçerlidir.
* VM 'deki IPv6 uç noktası doğrudan internet 'e gösterilmez. Bir yük dengeleyicinin arkasında. Yalnızca yük dengeleyici kurallarında belirtilen bağlantı noktalarına IPv6 üzerinden erişilebilir.
* IPv6 için IdleTimeout parametresini değiştirme **Şu anda desteklenmiyor**. Varsayılan değer dört dakikadır.
* IPv6 için loadDistributionMethod parametresini değiştirme **Şu anda desteklenmiyor**.
* Ayrılmış IPv6 IP 'Leri (ıpallocationmethod = static) **Şu anda desteklenmiyor**.
* NAT64 (IPv6 ile IPv4 çevirisi) desteklenmez.
* Standart SKU yük dengeleyiciler Şu anda IPv6 adreslerini desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

IPv6 ile yük dengeleyici dağıtmayı öğrenin.

* [Bölgeye göre IPv6 kullanılabilirliği](https://go.microsoft.com/fwlink/?linkid=828357)
* [Şablon kullanarak IPv6 ile yük dengeleyici dağıtma](load-balancer-ipv6-internet-template.md)
* [Azure PowerShell kullanarak IPv6 ile yük dengeleyici dağıtma](load-balancer-ipv6-internet-ps.md)
* [Azure CLı kullanarak IPv6 ile yük dengeleyici dağıtma](load-balancer-ipv6-internet-cli.md)
