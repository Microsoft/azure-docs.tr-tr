---
title: include dosyası
description: include dosyası
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cba54c65a314919b5509e1de029ad05f8722baa2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075455"
---
|**VPN<br>ağ geçidi<br>oluşturma** |**SKU**   | **S2S/VNet-VNet<br>Tünelleri** | **P2S<br> SSTP Bağlantıları** | **P2S<br> Ikev2/OpenVPN bağlantıları** | **Toplam<br>Aktarım Hızı Kıyaslaması** | **BGP** | **Bölge yedekli** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Temel**   | En çok, 10    | En çok, 128  | Desteklenmiyor  | 100 Mb/sn  | Desteklenmiyor| Hayır |
|**Generation1**|**VpnGw1**  | En çok, 30*   | En çok, 128  | En çok, 250       | 650 Mbps  | Destekleniyor | Hayır |
|**Generation1**|**VpnGw2**  | En çok, 30*   | En çok, 128  | En çok, 500       | 1 Gbps    | Destekleniyor | Hayır |
|**Generation1**|**VpnGw3**  | En çok, 30*   | En çok, 128  | En çok, 1000      | 1,25 Gb/sn | Destekleniyor | Hayır |
|**Generation1**|**VpnGw1AZ**| En çok, 30*   | En çok, 128  | En çok, 250       | 650 Mbps  | Destekleniyor | Yes |
|**Generation1**|**VpnGw2AZ**| En çok, 30*   | En çok, 128  | En çok, 500       | 1 Gbps    | Destekleniyor | Yes |
|**Generation1**|**VpnGw3AZ**| En çok, 30*   | En çok, 128  | En çok, 1000      | 1,25 Gb/sn | Destekleniyor | Yes |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | En çok, 30*   | En çok, 128  | En çok, 500       | 1,25 Gb/sn | Destekleniyor | Hayır |
|**Generation2**|**VpnGw3**  | En çok, 30*   | En çok, 128  | En çok, 1000      | 2,5 Gbps  | Destekleniyor | Hayır |
|**Generation2**|**VpnGw4**  | En çok, 30*   | En çok, 128  | En çok, 1000      | 5 Gbps    | Destekleniyor | Hayır |
|**Generation2**|**VpnGw5**  | En çok, 30*   | En çok, 128  | En çok, 1000      | 10 Gbps   | Destekleniyor | Hayır |
|**Generation2**|**VpnGw2AZ**| En çok, 30*   | En çok, 128  | En çok, 500       | 1,25 Gb/sn | Destekleniyor | Yes |
|**Generation2**|**VpnGw3AZ**| En çok, 30*   | En çok, 128  | En çok, 1000      | 2,5 Gbps  | Destekleniyor | Yes |
|**Generation2**|**VpnGw4AZ**| En çok, 30*   | En çok, 128  | En çok, 1000      | 5 Gbps    | Destekleniyor | Yes |
|**Generation2**|**VpnGw5AZ**| En çok, 30*   | En çok, 128  | En çok, 1000      | 10 Gbps   | Destekleniyor | Yes |

(*) 30'dan fazla S2S VPN tüneline ihtiyacınız varsa [Sanal WAN](../articles/virtual-wan/virtual-wan-about.md) kullanın.

* VpnGw SKU 'Larının yeniden boyutlandırılması, temel SKU 'nun yeniden boyutlandırılması dışında, aynı oluşturma içinde kullanılabilir. Temel SKU 'su eski bir SKU 'SU ve özellik sınırlamaları vardır. Temel ' den başka bir VpnGw SKU 'suna geçiş yapmak için, temel SKU VPN ağ geçidini silip istenen nesil ve SKU boyut birleşimine sahip yeni bir ağ geçidi oluşturmanız gerekir.

* Bu bağlantı sınırları ayrıdır. Örneğin, bir VpnGw1 SKU’sunda 128 SSTP bağlantısına ek olarak 250 IKEv2 bağlantısına sahip olabilirsiniz.

* Fiyatlandırma bilgileri [Fiyatlandırma](https://azure.microsoft.com/pricing/details/vpn-gateway) sayfasında bulunabilir.

* SLA (Hizmet Düzeyi Sözleşmesi) bilgilerine [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) sayfasından ulaşılabilir.

* Tek bir tünelde en fazla 1 Gbps aktarım elde edilebilir. Yukarıdaki tabloda yer alan toplu Işleme kıyaslamalar, tek bir ağ geçidi aracılığıyla toplanan birden çok tünelin ölçümlerine bağlıdır. Bir VPN Gateway’e yönelik Toplam Aktarım Hızı Karşılaştırması S2S ve P2S’nin birleşimidir. **Çok sayıda P2S bağlantısına sahipseniz S2S bağlantınız aktarım hızı sınırlamalarından dolayı olumsuz etkilenebilir.** Toplam Verimlilik kıyaslaması, Internet trafiği koşulları ve uygulama davranışlarınız nedeniyle garantili bir aktarım hızı değildir.

Müşterilerinizin farklı algoritmaları kullanarak SKU 'ların göreli performansını anlamalarına yardımcı olmak için, performansı ölçmek üzere genel kullanıma açık Iperf ve CTSTraffic araçları kullandık. Aşağıdaki tabloda, 1. nesil, VpnGw SKU 'Larının performans testlerinin sonuçları listelenmiştir. Gördüğünüz gibi, her iki IPSec şifrelemesi ve bütünlüğü için GCMAES256 algoritması kullandığımızda en iyi performans elde edilir. IPSec şifrelemesi için AES256 ve bütünlüğü için SHA256 kullanılırken ortalama performans aldık. IPSec şifrelemesi için DES3 kullandığımızda ve bütünlüğü için SHA256 en düşük performansa sahip olduğumuz zaman.

|**Mesinden**|**SKU**   | **Kullanılan algoritmalar<br>** | **Aktarım hızı<br>gözlemlenen** | **Saniyedeki paket sayısı<br>gözlemlenen** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mb/sn<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mb/sn<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/sn<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60,000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mb/sn<br>120 Mbps   | 58.000<br>50.000<br>50.000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mb/sn<br>120 Mbps | 90,000<br>80,000<br>55.000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/sn<br>550 Mbps<br>120 Mbps | 105.000<br>90,000<br>60,000|
