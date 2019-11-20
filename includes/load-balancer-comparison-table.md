---
title: include dosyası
description: include dosyası
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 8b08e0ced0c7094890a80c37452c7f1b001fe511
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888680"
---
| | Standart SKU | Temel SKU |
| --- | --- | --- |
| Arka uç havuzu boyutu | En fazla 1000 örneği destekler. | En fazla 100 örneği destekler. |
| Arka uç havuzu uç noktaları | Sanal makinelerin karışımlar, kullanılabilirlik kümeleri ve sanal makine ölçek kümeleri dahil olmak üzere tek bir sanal ağdaki tüm sanal makineler. | Tek bir kullanılabilirlik kümesindeki veya sanal makine ölçek kümesindeki sanal makineler. |
| [Sistem durumu yoklamaları](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Durum araştırma azaltma davranışı](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP bağlantıları bir örnek araştırması üzerinde __ve__ tüm yoklamalar üzerinde etkin kalır. | TCP bağlantıları bir örnek araştırmasına etkin kalır. Tüm yoklamalar kapatıldığında tüm TCP bağlantıları sonlandırılır. |
| Kullanılabilirlik Alanları | Gelen ve giden trafik için bölge yedekli ve bölgesel ön uçları. Giden akışlar eşleştirmeleri alan hatası. Bölgeler arası yük dengeleme. | Kullanılamaz |
| Tanılama | Azure Izleyici. Bayt ve paket sayaçları dahil olmak üzere çok boyutlu ölçümler. Durum araştırma durumu. Bağlantı denemeleri (TCP SYN). Giden bağlantı durumu (SNAT başarılı ve başarısız akışlar). Etkin veri düzlemi ölçümleri. | Yalnızca ortak Load Balancer için Azure Log Analytics. SNAT tükenme uyarısı. Arka uç havuzu sistem durumu sayısı. |
| HA bağlantı noktaları | İç Yük Dengeleyici | Kullanılamaz |
| Varsayılan olarak güvenli | Genel IP, genel Load Balancer uç noktaları ve iç Load Balancer uç noktaları, bir ağ güvenlik grubu tarafından izin verilmediği takdirde gelen akışlara kapalıdır. | Varsayılan olarak açın. Ağ güvenlik grubu isteğe bağlı. |
| [Giden bağlantılar](../articles/load-balancer/load-balancer-outbound-connections.md) | Havuz tabanlı giden NAT 'ı [giden kurallarla](../articles/load-balancer/load-balancer-outbound-rules-overview.md)açık bir şekilde tanımlayabilirsiniz. Yük Dengeleme kuralı için geri çevirme başına birden çok ön uç kullanabilirsiniz. Giden bağlantı kullanmak için sanal makine, kullanılabilirlik kümesi veya sanal makine ölçek kümesi için bir giden senaryonun açıkça oluşturulması _gerekir_ . Sanal ağ hizmeti uç noktalarına giden bağlantı tanımlanmadan ulaşılmış ve verilerin işlendiği doğru sayılmaz. Sanal ağ hizmeti uç noktaları olarak kullanılamayan Azure PaaS hizmetleri de dahil olmak üzere tüm genel IP adreslerine giden bağlantı kullanılarak ulaşılmalıdır ve verilerin işlendiği doğru sayılır. Yalnızca bir iç Load Balancer sanal makineye, kullanılabilirlik kümesine veya sanal makine ölçek kümesine hizmet veriyorsa, varsayılan SNAT üzerinden giden bağlantılar kullanılamaz. Bunun yerine [giden kurallarını](../articles/load-balancer/load-balancer-outbound-rules-overview.md) kullanın. Giden SNAT programlama, gelen yük dengeleme kuralının aktarım protokolüne bağlıdır. | Tek ön uç, birden çok ön uç bulunduğunda rastgele seçilir. Yalnızca dahili Load Balancer sanal makineye, kullanılabilirlik kümesine veya sanal makine ölçek kümesine hizmet veriyorsa, varsayılan SNAT kullanılır. |
| [Giden kuralları](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Genel IP adreslerini veya genel IP öneklerini veya her ikisini kullanarak bildirim temelli NAT yapılandırması. Yapılandırılabilir giden boşta kalma zaman aşımı (4-120 dakika). Özel SNAT bağlantı noktası ayırma | Kullanılamaz |
| [Boşta durumunda TCP sıfırlaması](../articles/load-balancer/load-balancer-tcp-reset.md) | Herhangi bir kuralda boşta kalma zaman aşımı durumunda TCP sıfırlamayı (TCP RST) etkinleştir | Kullanılamaz |
| [Birden çok ön uç](../articles/load-balancer/load-balancer-multivip-overview.md) | Gelen ve [giden](../articles/load-balancer/load-balancer-outbound-connections.md) | Yalnızca gelen |
| Yönetim Işlemleri | Çoğu işlem 30 saniye < | 60-90 + saniye tipik |
| SLA | iki sağlıklı sanal makineyle veri yolu için% 99,99. | Uygulanamaz | 
| Fiyatlandırma | Kural sayısına ve kaynakla ilişkili gelen ve giden dışarı alınan verilere göre ücretlendirilir. | Ücretsiz |
|  |  |  |
