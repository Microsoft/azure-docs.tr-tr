---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "97665126"
---
|  | **Noktadan siteye** | **Siteden siteye** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure desteklenen hizmetler** |Cloud Services ve Virtual Machines |Cloud Services ve Virtual Machines |[Hizmetler listesi](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Tipik Bant Genişlikleri** |Ağ geçidi SKU’sunu temel alanlar: |Tipik olarak < 1 Gb/sn toplu |50 Mb/sn, 100 Mb/sn, 200 Mb/sn, 500 Mb/sn, 1 Gb/sn, 2 Gb/sn, 5 Gb/sn, 10 Gb/sn |
| **Desteklenen protokoller** |Güvenli Yuva Tünel Protokolü (SSTP), OpenVPN ve IPSec |IPsec |VLAN, NSP'nin VPN’si teknolojileri (MPLS, VPLS,...) üzerinden doğrudan bağlantı |
| **Yönlendirme** |RouteBased (dinamik) |PolicyBased (statik yönlendirme) ve RouteBased’i (dinamik yönlendirme VPN) destekliyoruz |BGP |
| **Bağlantı dayanıklılığı** |aktif/pasif |aktif-pasif veya aktif-aktif |aktif/aktif |
| **Tipik kullanım örneği** |Uzak kullanıcılar için Azure sanal ağlarına güvenli erişim |Bulut hizmetleri ve sanal makineler için geliştirme/test/laboratuvar senaryoları ve küçük ve orta ölçekli üretim iş yükleri |Tüm Azure hizmetlerine erişim (doğrulanmış liste), Kurumsal sınıfta ve görev açısından kritik iş yükleri, Yedekleme, Büyük Veri, DR sitesi olarak Azure |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **Fiyatlandırma** |[Fiyatlandırma](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Fiyatlandırma](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Fiyatlandırma](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Teknik belgeler** |[VPN Gateway belgeleri](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[VPN Gateway belgeleri](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[ExpressRoute belgeleri](https://azure.microsoft.com/documentation/services/expressroute/) |
| **SSS** |[VPN Gateway SSS](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN Gateway SSS](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute SSS](../articles/expressroute/expressroute-faqs.md) |
