---
title: include dosyası
description: include dosyası
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: a37280fddf34221a9d08501ef55c6b069cd877a4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "70242789"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Ağ sınırları-Azure Resource Manager aşağıdaki sınırlar yalnızca, her abonelik için bölge başına **Azure Resource Manager** üzerinden yönetilen ağ kaynakları için geçerlidir. [Geçerli kaynak kullanımınızı abonelik sınırlarınıza göre görüntülemeyi](../articles/networking/check-usage-against-limits.md)öğrenin.

> [!NOTE]
> Son zamanlarda tüm varsayılan limitleri en yüksek sınırlara yükseltireceğiz. En fazla limit sütunu yoksa, kaynak, ayarlanabilir sınırlara sahip değildir. Geçmişte desteklenen bu limitlere sahipseniz ve aşağıdaki tablolarda güncelleştirilmiş limitleri görmüyorsanız, [ücretsiz olarak çevrimiçi müşteri destek isteği açın](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | Varsayılan/en yüksek sınır | 
| --- | --- |
| Sanal ağlar |1000 |
| Sanal ağ başına alt ağ sayısı |3,000 |
| Sanal ağ başına sanal ağ eşlemeleri |500 |
| Sanal ağ başına DNS sunucuları |20 |
| Sanal ağ başına özel IP adresleri |65.536 |
| Ağ arabirimi başına özel IP adresleri |256 |
| Sanal makine başına özel IP adresleri |256 |
| Ağ arabirimi başına genel IP adresleri |256 |
| Sanal makine başına genel IP adresleri |256 |
| Bir sanal makinenin veya rol örneğinin her NIC 'ı için eşzamanlı TCP veya UDP akışı |500,000 |
| Ağ arabirim kartları |65.536 |
| Ağ Güvenlik Grupları |5,000 |
| NSG başına NSG kuralları |1000 |
| Bir güvenlik grubundaki kaynak veya hedef için belirtilen IP adresleri ve aralıklar |4,000 |
| Uygulama güvenlik grupları |3,000 |
| IP yapılandırması başına uygulama güvenlik grupları, NIC başına |20 |
| Uygulama güvenlik grubu başına IP yapılandırması |4,000 |
| Bir ağ güvenlik grubunun tüm güvenlik kuralları dahilinde belirtilenebilir uygulama güvenlik grupları |100 |
| Kullanıcı tanımlı yol tabloları |200 |
| Yol tablosu başına Kullanıcı tanımlı yollar |400 |
| Azure VPN Gateway başına Noktadan siteye kök sertifika |20 |
| Sanal ağ dokunmalar |100 |
| Ağ arabirimi sanal ağ başına yapılandırmalara dokunun |100 |

#### <a name="publicip-address"></a>Genel IP adresi sınırları
| Resource | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| Genel IP adresleri-dinamik | Temel için 1.000. |Desteğe başvurun. |
| Genel IP adresleri-statik | Temel için 1.000. |Desteğe başvurun. |
| Genel IP adresleri-statik | Standart için 1.000.|Desteğe başvurun. |
| Genel IP öneki uzunluğu | /28 | Desteğe başvurun. |

#### <a name="load-balancer"></a>Yük dengeleyici sınırları
Aşağıdaki sınırlar yalnızca abonelik başına bölgeye göre Azure Resource Manager ile yönetilen ağ kaynakları için geçerlidir. [Geçerli kaynak kullanımınızı abonelik sınırlarınıza göre görüntülemeyi](../articles/networking/check-usage-against-limits.md)öğrenin.

| Resource | Varsayılan/en yüksek sınır |
| --- | --- |
| Yük dengeleyiciler | 1000 | 
| Kaynak başına kurallar, temel | 250 |
| Kaynak başına kurallar, standart | 1\.500 | 
| IP yapılandırması başına kurallar | 299 |
| NIC başına kurallar | 300 |
| Ön uç IP yapılandırması, temel | 200 |
| Ön uç IP yapılandırması, standart | 600 |
| Arka uç havuzu, temel | 100, tek kullanılabilirlik kümesi |
| Arka uç havuzu, standart | 1\.000, tek sanal ağ |
| Yük dengeleyici başına arka uç kaynakları, standart<sup>1</sup> | 150 |
| Yüksek kullanılabilirlik bağlantı noktaları, standart | Her iç ön uç için 1 |

<sup>1</sup> Sınır, tek başına sanal makine kaynaklarının, kullanılabilirlik kümesi kaynaklarının ve sanal makine ölçek kümesi kaynaklarının herhangi bir kombinasyonuna göre 150 kaynağa kadar olur.

#### <a name="virtual-networking-limits-classic"></a>Aşağıdaki sınırlar yalnızca abonelik başına **Klasik** dağıtım modeliyle yönetilen ağ kaynakları için geçerlidir. [Geçerli kaynak kullanımınızı abonelik sınırlarınıza göre görüntülemeyi](../articles/networking/check-usage-against-limits.md)öğrenin.

| Resource | Varsayılan limit | Üst sınır |
| --- | --- | --- |
| Sanal ağlar |100 |100 |
| Yerel ağ siteleri |20 |50 |
| Sanal ağ başına DNS sunucuları |20 |20 |
| Sanal ağ başına özel IP adresleri |4\.096 |4\.096 |
| Bir sanal makinenin veya rol örneğinin her NIC 'ı için eşzamanlı TCP veya UDP akışı |500.000, iki veya daha fazla NIC için 1.000.000 ' e kadar. |500.000, iki veya daha fazla NIC için 1.000.000 ' e kadar. |
| Ağ güvenlik grupları (NSG 'ler) |200 |200 |
| NSG başına NSG kuralları |1000 |1000 |
| Kullanıcı tanımlı yol tabloları |200 |200 |
| Yol tablosu başına Kullanıcı tanımlı yollar |400 |400 |
| Genel IP adresleri (dinamik) |500 |500 |
| Ayrılmış genel IP adresleri |500 |500 |
| Dağıtım başına genel VIP |5 |Desteğe başvurun |
| Dağıtım başına özel VIP (İç Yük Dengeleme) |1\. |1\. |
| Uç nokta erişim denetim listeleri (ACL 'Ler) |50 |50 |
