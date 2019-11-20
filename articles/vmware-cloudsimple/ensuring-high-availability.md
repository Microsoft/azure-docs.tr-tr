---
title: Azure 'da VMware 'de çalışırken uygulamanın yüksek oranda kullanılabilir olmasını sağlayın
description: CloudSimple özel bulutu 'nda çalışan uygulamalar için ortak uygulama hatası senaryolarını ele almak üzere CloudSimple yüksek kullanılabilirlik özelliklerini açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5a48a75d70234b06942f5141402070c89c543f18
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903384"
---
# <a name="ensure-application-high-availability-when-running-in-vmware-on-azure"></a>Azure 'da VMware 'de çalışırken uygulamanın yüksek oranda kullanılabilir olmasını sağlayın

CloudSimple çözümü, Azure ortamında VMware üzerinde çalışan uygulamalarınız için yüksek kullanılabilirlik sağlar. Aşağıdaki tabloda hata senaryoları ve ilişkili yüksek kullanılabilirlik özellikleri listelenmektedir.

| Hata senaryosu | Uygulama korumalı mı? | Platform HA özelliği | VMware HA özelliği | Azure HA özelliği |
------------ | ------------- | ------------ | ------------ | ------------- |
| Disk hatası | EVET | Hatalı düğümü hızlı değiştirme | [VSAN varsayılan depolama Ilkesi hakkında](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-C228168F-6807-4C2A-9D74-E584CAF49A2A.html) |
| Fan hatası | EVET | Gereksiz fanlar, başarısız olan düğümün hızlı değiştirilmesi |  |  |
| NIC hatası | EVET | Yedekli NIC, başarısız olan düğümün hızlı değiştirilmesi
| Konak güç hatası | EVET | Yedekli güç kaynağı |  |  |
| ESXi ana bilgisayar arızası | EVET | Hatalı düğümü hızlı değiştirme | [Yüksek kullanılabilirlik VMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) |  |  |
| VM hatası | EVET | [Yük dengeleyiciler](load-balancers.md)  | [Yüksek kullanılabilirlik VMware vSphere](https://www.vmware.com/products/vsphere/high-availability.html) | Durum bilgisiz VMware VM 'Leri için Azure Load Balancer |
| Yaprak anahtar bağlantı noktası hatası | EVET | Yedekli NIC |  |  |
| Yaprak anahtar hatası | EVET | Yedekli yaprak anahtarları |  |  |
| Raf hatası | EVET | Yerleştirme grupları |  |  |
| Şirket içi DC 'ye ağ bağlantısı | EVET  | Yedekli ağ hizmetleri |  | Yedekli ER devreleri |
| Azure ile ağ bağlantısı | EVET | |  | Yedekli ER devreleri |
| Veri merkezi hatası | EVET |  |  | Kullanılabilirlik alanları |
| Bölgesel hata | EVET  |  |  | Azure bölgeleri |

CloudSimple tarafından sağlanan Azure VMware çözümü, aşağıdaki yüksek kullanılabilirlik özelliklerini sağlar.

## <a name="fast-replacement-of-failed-node"></a>Hatalı düğümü hızlı değiştirme

CloudSimple denetim düzlemi yazılımı, VMware kümelerinin sistem durumunu sürekli olarak izler ve bir ESXi düğümünün başarısız olduğunu algılar. Daha sonra, etkilenen VMware kümesine otomatik olarak kullanılabilir düğümler havuzundan yeni bir ESXi Konağı ekler ve başarısız olan düğümü kümeden alır. Bu işlevsellik, VMware kümesindeki yedek kapasitenin hızlı bir şekilde geri yüklenmesini sağlar, böylece küme, vSAN ve VMware HA tarafından sağlanmış olan dayanıklılık geri yüklenir.

## <a name="placement-groups"></a>Yerleştirme grupları

Özel bir bulut oluşturan bir Kullanıcı, seçili bölgede bir Azure bölgesi ve bir yerleştirme grubu seçebilir. Yerleştirme grubu, birden çok rafdaki, ancak aynı sırt ağı segmentinde yayılan bir düğüm kümesidir. Aynı yerleştirme grubu içindeki düğümler, en fazla iki ek anahtar atlaması ile birbirlerine erişebilir. Bir yerleştirme grubu her zaman tek bir Azure kullanılabilirlik bölgesi içinde bulunur ve birden çok raflara yayılır. CloudSimple denetim düzlemi, bir özel bulutun düğümlerini, en iyi çabalara göre birden çok raf arasında dağıtır. Farklı yerleştirme gruplarındaki düğümlerin farklı raflara yerleştirilmesi garanti edilir.

## <a name="availability-zones"></a>Kullanılabilirlik alanları

Kullanılabilirlik alanları, uygulamalarınızı ve verilerinizi veri merkezi hatalarından koruyan yüksek kullanılabilirliğe sahip bir tekliftir. Kullanılabilirlik alanları, bir Azure bölgesi içindeki özel fiziksel konumlardır. Her bölge, soğutma ve ağ bağımsız güç ile donatılmış bir veya daha fazla veri merkezlerinden oluşur. Her bölgenin bir kullanılabilirlik alanı vardır. Daha fazla bilgi için bkz. [Azure 'da kullanılabilirlik alanları nedir?](../availability-zones/az-overview.md).

## <a name="redundant-azure-expressroute-circuits"></a>Gereksiz Azure ExpressRoute devreleri

ExpressRoute kullanarak Azure vNet 'e veri merkezi bağlantısı, yüksek oranda kullanılabilir ağ bağlantısı bağlantısı sağlamak için yedekli devrelere sahiptir.

## <a name="redundant-networking-services"></a>Yedekli ağ hizmetleri

Özel bulut için tüm CloudSimple ağ hizmetleri (VLAN, güvenlik duvarı, genel IP adresleri, Internet ve VPN dahil), yüksek oranda kullanılabilir olacak şekilde tasarlanmıştır ve hizmet SLA 'sını destekleyebilir.

## <a name="azure-layer-7-load-balancer-for-stateless-vmware-vms"></a>Durum bilgisiz VMware VM 'Leri için Azure katman 7 Load Balancer

Kullanıcılar, Web katmanı için yüksek kullanılabilirlik elde etmek üzere VMware ortamında çalışan durum bilgisiz Web katmanı VM 'lerinin önüne bir Azure katman 7 Load Balancer yerleştirebilir.

## <a name="azure-regions"></a>Azure bölgeleri

Azure bölgesi, gecikme süresi belirlenmiş bir çevre içinde dağıtılan ve adanmış bölgesel düşük gecikmeli bir ağ ile bağlanan bir veri merkezleri kümesidir. Ayrıntılar için bkz. [Azure bölgeleri](https://azure.microsoft.com/global-infrastructure/regions).
