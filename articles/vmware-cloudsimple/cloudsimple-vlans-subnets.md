---
title: CloudSimple tarafından Azure VMware çözümünde VLAN ve alt ağlar
description: CloudSimple özel bulutu 'nda VLAN 'Lar ve alt ağlar hakkında bilgi edinin
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7af191893d6b3cf1c38e5ff44a7a8a04509347a8
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543791"
---
# <a name="vlans-and-subnets-overview"></a>VLAN 'Lara ve alt ağlara genel bakış

CloudSimple, CloudSimple hizmetinizin dağıtıldığı bölge başına bir ağ sağlar.  Ağ, yönlendirme özelliği varsayılan olarak etkin olan tek bir TCP katman 3 adres alanıdır.  Bu bölgede oluşturulan tüm özel bulutlar ve alt ağlar, ek bir yapılandırma olmadan birbirleriyle iletişim kurabilir.  VLAN 'Lar kullanarak vCenter üzerinde dağıtılmış bağlantı noktası grupları oluşturabilirsiniz.

![CloudSimple ağ topolojisi](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

Her özel bulut için bir VLAN (katman 2 ağ) oluşturulur.  Katman 2 trafiği özel bulutun sınırında kalır ve özel buluttaki yerel trafiği yalıtmanızı sağlar.  Özel bulutta oluşturulan bir VLAN, yalnızca o özel bulutta dağıtılmış bağlantı noktası grupları oluşturmak için kullanılabilir.  Özel bir bulutta oluşturulan bir VLAN, özel bulutun konaklarına bağlı tüm anahtarlarda otomatik olarak yapılandırılır.

## <a name="subnets"></a>Alt ağlar

Alt ağın adres alanını tanımlayarak bir VLAN oluşturduğunuzda bir alt ağ oluşturabilirsiniz. Adres alanından bir IP adresi, alt ağ geçidi olarak atanır. Her müşteri ve bölge için tek bir özel katman 3 adres alanı atanır. Ağ bölgenizde, şirket içi ağınız veya Azure sanal ağınız ile herhangi bir RFC 1918 çakışmayan adres alanı yapılandırabilirsiniz.

Tüm alt ağlar varsayılan olarak birbirleriyle iletişim kurabilir ve özel bulutlar arasında yönlendirme için yapılandırma yükünü azaltır. Aynı bölgedeki bilgisayarlar arasında Doğu Batı verileri aynı katman 3 ağında kalır ve bölge içindeki yerel ağ altyapısına aktarır. Bir bölgedeki özel bulutlar arasındaki iletişim için çıkış gerekmez. Bu yaklaşım farklı özel bulutlarda farklı iş yüklerini dağıtmanın tüm WAN/çıkış performans cezalarını ortadan kaldırır.

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN alt ağları CıDR aralığı

Özel bulut, bir vCenter sunucusu tarafından yönetilen yalıtılmış bir VMware yığını (ESXi Konakları, vCenter, vSAN ve NSX) ortamı olarak oluşturulur.  Yönetim bileşenleri, vSphere/vSAN alt ağları CıDR için seçilen ağda dağıtılır.  Ağ CıDR aralığı, dağıtım sırasında farklı alt ağlara bölünmüştür.

* Minimum vSphere/vSAN alt ağları CıDR aralığı ön eki: **/24**
* Maksimum vSphere/vSAN alt ağları CıDR aralığı ön eki: **/21**

> [!IMPORTANT]
> VSphere/vSAN CıDR aralığındaki IP adresleri özel bulut altyapısı tarafından kullanılmak üzere ayrılmıştır.  Bu aralıkta IP adresini herhangi bir sanal makinede kullanmayın.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN alt ağları CıDR Aralık sınırları

VSphere/vSAN alt ağlarının CıDR Aralık boyutunun seçilmesi, özel bulutunuzun boyutunu etkiler.  Aşağıdaki tabloda, vSphere/vSAN alt ağlarının CıDR boyutunu temel alan en fazla düğüm sayısı gösterilmektedir.

| Belirtilen vSphere/vSAN alt ağları CıDR ön eki uzunluğu | En fazla düğüm sayısı |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Özel bir bulutta oluşturulan yönetim alt ağları

Özel bir bulut oluşturduğunuzda aşağıdaki yönetim alt ağları oluşturulur.

* **Sistem yönetimi**. ESXi ' Management Network, DNS Server, vCenter Server konakları için VLAN ve subnet.
* **VMotion**. ESXi konakları için VLAN ve subnet, ' vMotion Network.
* **VSAN**. ESXi konakları için VLAN ve subnet ' vSAN ağı.
* **NsxtEdgeUplink1**. Bir dış ağa VLAN bağlantıları için VLAN ve subnet.
* **NsxtEdgeUplink2**. Bir dış ağa VLAN bağlantıları için VLAN ve subnet.
* **Nsxtedgetransport**. Taşıma bölgeleri için VLAN ve subnet, NSX-T içindeki katman 2 ağlarının erişimini denetler.
* **Nsxthosttransport**. Konak aktarım bölgesi için VLAN ve subnet.

### <a name="management-network-cidr-range-breakdown"></a>Yönetim ağı CıDR aralığı dökümü

Belirtilen vSphere/vSAN alt ağları CıDR aralığı birden çok alt ağa bölünmüştür.  Aşağıdaki tabloda, izin verilen ön ekler için döküm örneği gösterilmektedir.  Örnek, CıDR aralığı olarak 192.168.0.0 kullanır.

Örnek:

| Belirtilen vSphere/vSAN alt ağları CıDR/ön eki | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Sistem Yönetimi | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T ana bilgisayar taşıması | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T Edge taşıması | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Sonraki adımlar

* [VLAN 'Ları ve alt ağları oluşturma ve yönetme](create-vlan-subnet.md)
