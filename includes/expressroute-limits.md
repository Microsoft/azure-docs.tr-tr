---
title: include dosyası
description: include dosyası
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 01/12/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 6f8ed3381f056238bdbb24fe52c5f859afef7d03
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98147330"
---
| Kaynak | Sınır |
| --- | --- |
| Abonelik başına ExpressRoute devreleri |10 |
| Abonelik başına her bölge için ExpressRoute devreleri, Azure Resource Manager ile |10 |
| ExpressRoute standardı ile Azure özel eşlemesine tanıtılan en fazla yol sayısı |4.000 |
| ExpressRoute Premium eklentisi ile Azure özel eşleme 'ye tanıtılan en fazla yol sayısı |10,000 |
| ExpressRoute bağlantısı için VNet adres alanından Azure özel eşlemeden tanıtılan en fazla yol sayısı |200 |
| ExpressRoute standardı ile Microsoft eşlemesine tanıtılan en fazla yol sayısı |200 |
| ExpressRoute Premium eklentisi ile Microsoft eşlemesi 'ne tanıtılan en fazla yol sayısı |200 |
| Aynı eşleme konumunda aynı sanal ağa bağlı olan maksimum ExpressRoute bağlantı hattı sayısı |4 |
| Farklı eşleme konumlarında aynı sanal ağa bağlı olan maksimum ExpressRoute bağlantı hattı sayısı |4 |
| ExpressRoute bağlantı hattı başına izin verilen sanal ağ bağlantısı sayısı |[ExpressRoute bağlantı hattı başına sanal ağ sayısını](#vnetpercircuit) görüntüleyin.  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> ExpressRoute bağlantı hattı başına sanal ağ sayısı
| **Devre boyutu** | **Standart için sanal ağ bağlantısı sayısı** | **Premium eklentisi olan sanal ağ bağlantısı sayısı** |
| --- | --- | --- |
| 50 Mb/sn |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mb/sn |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| 100 Gbps * |10 |100 |

**Yalnızca 100 Gbps ExpressRoute doğrudan*

> [!NOTE]
> Global Reach bağlantı sayısı ExpressRoute bağlantı hattı başına sanal ağ bağlantısı sınırına göre sayılır. Örneğin, 10 Gbps Premium bir bağlantı, ExpressRoute ağ geçitlerine veya 95 Global Reach bağlantı ve 95 bağlantısı ile Global Reach ExpressRoute ağ geçitlerine ya da devre için 100 bağlantı sınırına kadar olan 5 bağlantıya izin verir.
