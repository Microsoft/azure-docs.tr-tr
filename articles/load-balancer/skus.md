---
title: Azure Load Balancer SKU 'Ları
description: Azure Load Balancer SKU 'Larına genel bakış
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 874ecfc8c1c50816916fb0b04975477a1cbe0a71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94698096"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer SKU 'Ları

Azure Load Balancer iki SKU 'su vardır.

## <a name="sku-comparison"></a><a name="skus"></a> SKU karşılaştırması

Yük dengeleyici hem standart hem de temel SKU 'Ları destekler. Bu SKU 'Lar senaryo ölçeğinde, özelliklerde ve fiyatlandırmaya göre farklılık gösterir. Temel yük dengeleyici ile mümkün olan herhangi bir senaryo, standart yük dengeleyici ile oluşturulabilir.

Farkları karşılaştırmak ve anlamak için aşağıdaki tabloya bakın. Daha fazla bilgi için bkz. [Azure Standart Load Balancer genel bakış](./load-balancer-overview.md).

>[!NOTE]
> Microsoft, standart yük dengeleyiciyi öneriyor.
Tek başına VM'ler, kullanılabilirlik kümeleri ve sanal makine ölçek kümeleri yalnızca tek bir SKU'ya bağlanabilir, ikisine birden bağlanamaz. Yük dengeleyici ve genel IP adresi SKU 'SU ortak IP adresleriyle kullandığınızda aynı olmalıdır. Yük dengeleyici ve genel IP SKU 'Ları değişebilir değildir.

| | Standart Load Balancer | Temel Load Balancer |
| --- | --- | --- |
| **[Arka uç havuzu boyutu](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | En fazla 1000 örneği destekler. | En fazla 300 örneği destekler. |
| **Arka uç havuzu uç noktaları** | Tek bir sanal ağdaki herhangi bir sanal makine veya sanal makine ölçek kümesi. | Tek bir kullanılabilirlik kümesindeki veya sanal makine ölçek kümesindeki sanal makineler. |
| **[Durum araştırmaları](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Durum araştırma azaltma davranışı](./load-balancer-custom-probe-overview.md#probedown)** | TCP bağlantıları bir örnek araştırması üzerinde __ve__ tüm yoklamalar üzerinde etkin kalır. | TCP bağlantıları bir örnek araştırmasına etkin kalır. Tüm yoklamalar kapatıldığında tüm TCP bağlantıları sona erdir. |
| **Kullanılabilirlik Alanları** | Gelen ve giden trafik için bölge yedekli ve zikzak ön uçları. | Kullanılamaz |
| **Tanılama** | [Azure Izleyici çok boyutlu ölçümleri](./load-balancer-standard-diagnostics.md) | [Azure İzleyici günlükleri](./load-balancer-monitor-log.md) |
| **HA bağlantı noktaları** | [Iç Load Balancer için kullanılabilir](./load-balancer-ha-ports-overview.md) | Kullanılamaz |
| **Varsayılan olarak güvenli** | Ağ güvenlik grubu tarafından izin verilmediği takdirde gelen akışlar için kapatıldı. Sanal ağdan iç yük dengeleyiciye iç trafiğe izin verilir. | Varsayılan olarak açın. Ağ güvenlik grubu isteğe bağlı. |
| **Giden kuralları** | [Bildirim temelli giden NAT yapılandırması](./load-balancer-outbound-connections.md#outboundrules) | Kullanılamaz |
| **Boşta durumunda TCP sıfırlaması** | [Herhangi bir kuralda kullanılabilir](./load-balancer-tcp-reset.md) | Kullanılamaz |
| **[Birden çok ön uç](./load-balancer-multivip-overview.md)** | Gelen ve [giden](./load-balancer-outbound-connections.md) | Yalnızca gelen |
| **Yönetim İşlemleri** | Çoğu işlem 30 saniye < | 60-90 + saniye tipik |
| **SLA** | [% 99,99](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | Kullanılamaz | 

Daha fazla bilgi için bkz. [yük dengeleyici sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer). Standart Load Balancer hakkında ayrıntılı bilgi için bkz. [genel bakış](./load-balancer-overview.md), [fiyatlandırma](https://aka.ms/lbpricing) ve [SLA](https://aka.ms/lbsla).

## <a name="limitations"></a>Sınırlamalar

- SKU 'Lar değişebilir değildir. Mevcut bir kaynağın SKU 'sunu değiştiremezsiniz.
- Tek başına bir sanal makine kaynağı, kullanılabilirlik kümesi kaynağı veya sanal makine ölçek kümesi kaynağı, her ikisi de tek bir SKU 'ya başvurabilir.
- [Taşıma işlemleri](../azure-resource-manager/management/move-resource-group-and-subscription.md):
  - Kaynak grubu taşıma işlemleri (aynı abonelik içinde) Standart Load Balancer ve standart genel IP için **desteklenir** . 
  - Standart Load Balancer ve standart genel IP kaynakları için [abonelik grubu taşıma işlemleri](../azure-resource-manager/management/move-support-resources.md) **desteklenmez.**

## <a name="next-steps"></a>Sonraki adımlar

- Load Balancer kullanmaya başlamak için bkz. [genel standart Load Balancer oluşturma](quickstart-load-balancer-standard-public-portal.md) .
- [Standart Load Balancer ve kullanılabilirlik alanları](load-balancer-standard-availability-zones.md)kullanma hakkında bilgi edinin.
- [Sistem durumu araştırmaları](load-balancer-custom-probe-overview.md)hakkında bilgi edinin.
- [Giden bağlantılar için Load Balancer](load-balancer-outbound-connections.md)kullanma hakkında bilgi edinin.
- [Ha bağlantı noktaları Yük Dengeleme kurallarıyla standart Load Balancer](load-balancer-ha-ports-overview.md)hakkında bilgi edinin.
- [Ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md)hakkında daha fazla bilgi edinin.