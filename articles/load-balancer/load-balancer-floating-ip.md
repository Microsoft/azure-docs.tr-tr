---
title: Kayan IP yapılandırmasını Azure Load Balancer
description: Kayan IP Azure Load Balancer genel bakış
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94699415"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Kayan IP yapılandırmasını Azure Load Balancer

Yük dengeleyici hem UDP hem de TCP uygulamaları için çeşitli yetenekler sağlar.

## <a name="floating-ip"></a>Kayan IP

Bazı uygulama senaryoları, arka uç havuzundaki tek bir VM 'de birden çok uygulama örneği tarafından kullanılacak bağlantı noktasını tercih eder veya gerektirir. Bağlantı noktası yeniden kullanımının yaygın örnekleri, yüksek kullanılabilirlik, ağ sanal cihazları için kümeleme ve yeniden şifrelemeden birden çok TLS uç noktası kullanıma sunma içerir. Arka uç bağlantı noktasını birden çok kural genelinde yeniden kullanmak istiyorsanız, kural tanımında kayan IP 'yi etkinleştirmeniz gerekir.

**Kayan IP** , doğrudan sunucu dönüşü (DSR) olarak bilinen bir kısmı için Azure 'un terminolojileridir. DSR iki bölümden oluşur:

- Akış topolojisi
- Bir IP adresi eşleme düzeni

Bir platform düzeyinde Azure Load Balancer, kayan IP 'nin etkin olup olmamasına bakılmaksızın her zaman DSR akış topolojisinde çalışır. Bu, akışın giden bölümünün her zaman doğrudan başlangıca akışa doğru şekilde yeniden yazılması anlamına gelir.
Kayan IP olmadan Azure, kullanım kolaylığı için geleneksel bir yük dengeleme IP adresi eşleme düzeni sunar (VM örnekleri ' IP). Kayan IP 'nin etkinleştirilmesi, ek esneklik sağlamak için IP adresi eşlemesini yük dengeleyicinin ön uç IP 'si olarak değiştirir. [Daha fazla bilgi edinin](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Sınırlamalar

- Kayan IP, Yük Dengeleme senaryoları için ikincil IP yapılandırmalarında Şu anda desteklenmiyor

## <a name="next-steps"></a>Sonraki adımlar

- Load Balancer kullanmaya başlamak için bkz. [genel standart Load Balancer oluşturma](quickstart-load-balancer-standard-public-portal.md) .
- [Giden Azure Load Balancer bağlantıları](load-balancer-outbound-connections.md)hakkında bilgi edinin.
- [Azure Load Balancer](load-balancer-overview.md)hakkında daha fazla bilgi edinin.
- [Sistem durumu araştırmaları](load-balancer-custom-probe-overview.md)hakkında bilgi edinin.
- [Standart Load Balancer tanılama](load-balancer-standard-diagnostics.md)hakkında bilgi edinin.
- [Ağ güvenlik grupları](../virtual-network/network-security-groups-overview.md)hakkında daha fazla bilgi edinin.