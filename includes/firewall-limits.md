---
title: include dosyası
description: include dosyası
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: c4c36c0e099ed7474a5d27f6edcbd4b3ac435f4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030837"
---
| Kaynak | Sınır |
| --- | --- |
| Veri aktarım hızı |30 Gbps<sup>1</sup> |
|Kural sınırları|Ağ kurallarında 10.000 benzersiz kaynak/hedef|
|Maksimum DNAT kuralları|tek bir genel IP adresi için 298.<br>Ek genel IP adresleri kullanılabilir SNAT bağlantı noktalarına katkıda bulunur, ancak kullanılabilir DNAT kurallarının sayısını azaltır. Örneğin, iki genel IP adresi 297 DNAT kuralları için izin verir. Bir kuralın protokolü TCP ve UDP için yapılandırılırsa, iki kural olarak sayılır.|
|Minimum AzureFirewallSubnet boyutu |/26|
|Ağ ve uygulama kurallarında bağlantı noktası aralığı|1 - 65535|
|Genel IP adresleri|250 en fazla. Tüm genel IP adresleri DNAT kurallarında kullanılabilir ve hepsi, kullanılabilir SNAT bağlantı noktalarına katkıda bulunur.|
|IP gruplarındaki IP adresleri|Güvenlik Duvarı başına en fazla 100 IP grubu.<br>Her IP grubu başına en fazla 5000 ayrı IP adresi veya IP öneki.
|Yol tablosu|Varsayılan olarak, AzureFirewallSubnet, NextHopType değeri **Internet** olarak ayarlanmış bir 0.0.0.0/0 yolu içerir.<br><br>Azure Güvenlik Duvarı internete bağlı olmalıdır. AzureFirewallSubnet, BGP aracılığıyla şirket içi ağınıza varsayılan bir yol öğrenirse, doğrudan Internet bağlantısını sürdürmek için **Nexthoptype** değeri **Internet** olarak ayarlanmış bir 0.0.0.0/0 UDR ile geçersiz kılmalısınız. Azure Güvenlik Duvarı, varsayılan olarak şirket içi bir ağa Zorlamalı tünel oluşturmayı desteklemez.<br><br>Ancak, yapılandırmanız şirket içi bir ağa Zorlamalı tünel gerektiriyorsa, Microsoft bu servis talebi büyük bir durum temelinde destekleyecektir. Büyük/küçük harf bilgilerinizi gözden geçirebilmemiz için desteğe başvurun. Kabul edilirse, aboneliğinize izin vereceğiz ve gerekli güvenlik duvarı Internet bağlantısının korunduğundan emin olacaksınız.|
|Ağ kurallarında FQDN 'Ler|İyi performans için, güvenlik duvarı başına tüm ağ kurallarında 1000 ' den fazla FQDN 'yi aşmayın.|

<sup>1</sup> Bu sınırları artırmanız gerekiyorsa, Azure desteği 'ne başvurun.
