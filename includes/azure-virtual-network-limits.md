---
title: include dosyası
description: include dosyası
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 2262290486047c225cec8e8f6bb73242df7258fa
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106271656"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Ağ sınırları-Azure Resource Manager
Aşağıdaki sınırlar yalnızca, her abonelik için bölge başına **Azure Resource Manager** ile yönetilen ağ kaynakları için geçerlidir. [Geçerli kaynak kullanımınızı abonelik sınırlarınıza göre görüntülemeyi](../articles/networking/check-usage-against-limits.md)öğrenin.

> [!NOTE]
> Son zamanlarda tüm varsayılan limitleri en yüksek sınırlara yükseltireceğiz. En fazla limit sütunu yoksa, kaynak, ayarlanabilir sınırlara sahip değildir. Geçmişte desteklenen bu limitlere sahipseniz ve aşağıdaki tablolarda güncelleştirilmiş limitleri görmüyorsanız, [ücretsiz olarak çevrimiçi müşteri destek isteği açın](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Kaynak | Sınır | 
| --- | --- |
| Sanal ağlar |1.000 |
| Sanal ağ başına alt ağ |3.000 |
| Sanal ağ başına sanal ağ eşlemesi |500 |
| [Sanal ağ başına sanal ağ geçitleri (VPN ağ geçitleri)](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Sanal ağ başına sanal ağ geçitleri (ExpressRoute ağ geçitleri)](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Sanal ağ başına DNS sunucuları |20 |
| Sanal ağ başına özel IP adresi |65.536 |
| Ağ arabirimi başına özel IP adresleri |256 |
| Sanal makine başına özel IP adresleri |256 |
| Ağ arabirimi başına genel IP adresleri |256 |
| Sanal makine başına genel IP adresleri |256 |
| [Bir sanal makinenin veya rol örneğinin her NIC 'ı için eşzamanlı TCP veya UDP akışı](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) |500.000 |
| Ağ arabirim kartları |65.536 |
| Ağ Güvenlik Grupları |5.000 |
| NSG başına NSG kuralları |1.000 |
| Bir güvenlik grubundaki kaynak veya hedef için belirtilen IP adresleri ve aralıklar |4.000 |
| Uygulama güvenliği grupları |3.000 |
| IP yapılandırması başına uygulama güvenlik grupları, NIC başına |20 |
| Uygulama güvenlik grubu başına IP yapılandırması |4.000 |
| Bir ağ güvenlik grubunun tüm güvenlik kuralları dahilinde belirtilenebilir uygulama güvenlik grupları |100 |
| Kullanıcı tanımlı yol tabloları |200 |
| Yol tablosu başına Kullanıcı tanımlı yollar |400 |
| Azure VPN Gateway başına Noktadan siteye kök sertifika |20 |
| Sanal ağ dokunmalar |100 |
| Ağ arabirimi sanal ağ başına yapılandırmalara dokunun |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Genel IP adresi sınırları
| Kaynak | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| Genel IP adresleri<sup>1</sup> | Temel için 10. | Desteğe başvurun. |
| Statik genel IP adresleri<sup>1</sup> | Temel için 10. | Desteğe başvurun. |
| Standart genel IP adresleri<sup>1</sup> | 10 | Desteğe başvurun. |
| [Kaynak grubu başına genel IP adresleri](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | Desteğe başvurun. | 
| Genel IP Ön Ekleri | bir abonelikte standart genel IP sayısıyla sınırlı | Desteğe başvurun. |
| Genel IP öneki uzunluğu | /28 | Desteğe başvurun. |

<sup>1</sup> Genel IP adresleri için varsayılan sınırlar, ücretsiz deneme, Kullandıkça öde, CSP gibi teklif kategorisi türüne göre farklılık gösterir. Örneğin, Kurumsal Anlaşma abonelikler için varsayılan değer 1000 ' dir.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Yük dengeleyici sınırları
Aşağıdaki sınırlar yalnızca abonelik başına bölgeye göre Azure Resource Manager ile yönetilen ağ kaynakları için geçerlidir. [Geçerli kaynak kullanımınızı abonelik sınırlarınıza göre görüntülemeyi](../articles/networking/check-usage-against-limits.md)öğrenin.

**Standart Load Balancer**

| Kaynak                                | Sınır         |
|-----------------------------------------|-------------------------------|
| Yük dengeleyiciler                          | 1.000                         |
| Kaynak başına kurallar (Load Balancer + gelen NAT)                      | 1.500                         |
| NIC başına kural (bir NIC 'deki tüm IP 'lerde) | 300                           |
| Ön uç IP yapılandırması              | 600                           |
| Arka uç havuzu boyutu                       | 1.000 IP yapılandırması, tek bir sanal ağ |
| Load Balancer 1 başına arka uç kaynakları <sup><sup> | 1.200                   |
| Yüksek kullanılabilirlik bağlantı noktaları                 | iç ön uç başına 1       |
| Load Balancer başına giden kuralları        | 600                           |
| VM başına yük dengeleyiciler                   | 2 (1 genel ve 1 iç)   |

<sup>1</sup> sınır, tek başına sanal makine kaynaklarının, kullanılabilirlik kümesi kaynaklarının ve sanal makine ölçek kümesi yerleştirme gruplarının herhangi bir birleşiminde 1.200 kaynağa kadar olur.

**Temel Load Balancer**

| Kaynak                                | Sınır        |
|-----------------------------------------|------------------------------|
| Yük dengeleyiciler                          | 1.000                        |
| Kaynak başına kurallar                      | 250                          |
| NIC başına kural (bir NIC 'deki tüm IP 'lerde) | 300                          |
| Ön uç IP yapılandırması <sup> 2<sup>  | 200                          |
| Arka uç havuzu boyutu                       | 300 IP yapılandırması, tek kullanılabilirlik kümesi |
| Load Balancer başına kullanılabilirlik kümeleri     | 1                            |
| VM başına yük dengeleyiciler                   | 2 (1 genel ve 1 iç)  |

<sup>2</sup> arka uç havuzundaki tek bir ayrı kaynağın sınırı (tek başına sanal makine, kullanılabilirlik kümesi veya sanal makine ölçek kümesi yerleştirme grubu), tek bir temel genel Load Balancer ve temel iç Load Balancer en fazla 250 ön uç IP yapılandırmasına sahip olur.

<a name="virtual-networking-limits-classic"></a>Aşağıdaki sınırlar yalnızca abonelik başına **Klasik** dağıtım modeliyle yönetilen ağ kaynakları için geçerlidir. [Geçerli kaynak kullanımınızı abonelik sınırlarınıza göre görüntülemeyi](../articles/networking/check-usage-against-limits.md)öğrenin.

| Kaynak | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| Sanal ağlar |100 |100 |
| Yerel ağ siteleri |20 |50 |
| Sanal ağ başına DNS sunucuları |20 |20 |
| Sanal ağ başına özel IP adresi |4.096 |4.096 |
| Bir sanal makinenin veya rol örneğinin her NIC 'ı için eşzamanlı TCP veya UDP akışı |500.000, iki veya daha fazla NIC için 1.000.000 ' e kadar. |500.000, iki veya daha fazla NIC için 1.000.000 ' e kadar. |
| Ağ güvenlik grupları (NSG 'ler) |200 |200 |
| NSG başına NSG kuralları |200 |1.000 |
| Kullanıcı tanımlı yol tabloları |200 |200 |
| Yol tablosu başına Kullanıcı tanımlı yollar |400 |400 |
| Genel IP adresleri (dinamik) |500 |500 |
| Ayrılmış genel IP adresleri |500 |500 |
| Dağıtım başına genel IP |5 |Desteğe başvurun |
| Dağıtım başına özel IP (İç Yük Dengeleme) |1 |1 |
| Uç nokta erişim denetim listeleri (ACL 'Ler) |50 |50 |
