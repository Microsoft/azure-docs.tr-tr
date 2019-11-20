---
title: include dosyası
description: include dosyası
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622129"
---
| Resource | Varsayılan/en yüksek sınır |
| --- | --- |
| Abonelik başına ExpressRoute devreleri |10 |
| Abonelik başına her bölge için ExpressRoute devreleri, Azure Resource Manager ile |10 |
| ExpressRoute standardı ile Azure özel eşlemesine tanıtılan en fazla yol sayısı |4,000 |
| ExpressRoute Premium eklentisi ile Azure özel eşleme 'ye tanıtılan en fazla yol sayısı |10,000 |
| ExpressRoute bağlantısı için VNet adres alanından Azure özel eşlemeden tanıtılan en fazla yol sayısı |200 |
| ExpressRoute standardı ile Microsoft eşlemesine tanıtılan en fazla yol sayısı |200 |
| ExpressRoute Premium eklentisi ile Microsoft eşlemesi 'ne tanıtılan en fazla yol sayısı |200 |
| Aynı eşleme konumunda aynı sanal ağa bağlı olan maksimum ExpressRoute bağlantı hattı sayısı |4 |
| Farklı eşleme konumlarında aynı sanal ağa bağlı olan maksimum ExpressRoute bağlantı hattı sayısı |4 |
| ExpressRoute bağlantı hattı başına izin verilen sanal ağ bağlantısı sayısı |[ExpressRoute bağlantı hattı başına sanal ağ sayısını](#vnetpercircuit) görüntüleyin.  |

#### <a name="vnetpercircuit"></a>ExpressRoute bağlantı hattı başına sanal ağ sayısı
| **Devre boyutu** | **Standart için sanal ağ bağlantısı sayısı** | **Premium eklentisi olan sanal ağ bağlantısı sayısı** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**Yalnızca 100 Gbps ExpressRoute doğrudan*
