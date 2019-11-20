---
title: Sanal ağ için Azure CLI örnekleri
description: Sanal ağ için Azure CLI örnekleri.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: ff1017c8a6bc3c2806da6ea2a53b931aaf28644f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092088"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Sanal ağ için Azure CLI örnekleri

Aşağıdaki tablo, Azure CLI komutları ile bash betiklerine yönelik bağlantıları içerir:

| | |
|----|----|
| [Çok katmanlı uygulamalar için sanal ağ oluşturma](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Ön uç ve arka uç alt ağları ile sanal ağ oluşturur. 3306 numaralı bağlantı noktası için, ön uç alt ağına giden trafik HTTP ve SSH ile sınırlıyken, arka uç alt ağına giden trafik MySQL ile sınırlıdır. |
| [İki sanal ağı eşleme](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Aynı bölgede iki sanal ağ oluşturur ve bunları bağlar. |
| [Bir ağ sanal gereci yoluyla trafiği yönlendirme](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | İki alt ağ arasında trafiği yönlendirebilen bir sanal makine ve ön uç ve arka uç alt ağları içeren bir sanal ağ oluşturur. |
| [Gelen ve giden sanal makine ağ trafiğini filtreleme](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Ön uç ve arka uç alt ağları ile sanal ağ oluşturur. Ön uç alt ağına gelen ağ trafiği, HTTP, HTTPS ve SSH ile sınırlıdır. Arka uç alt ağından İnternet’e giden trafiğe izin verilmez. |
|[Temel Load Balancer IPv4 + IPv6 çift yığın sanal ağını yapılandırma](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|İki VM ile çift yığın (IPv4 + IPv6) sanal ağını ve IPv4 ve IPv6 genel IP adreslerine sahip bir Azure temel Load Balancer dağıtır. |
|[IPv4 + IPv6 çift yığın sanal ağını Standart Load Balancer ile yapılandırma](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|İki VM ile çift yığın (IPv4 + IPv6) sanal ağını ve IPv4 ve IPv6 genel IP adreslerine sahip bir Azure Standart Load Balancer dağıtır. |