---
title: Internet eşlemesi ve Eşleme Hizmeti
titleSuffix: Azure
description: Internet eşlemesi ve Eşleme Hizmeti
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: overview
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: 717a8c87c6a1c22d3f75cd4e3054bfdf0c4b5f9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95023454"
---
# <a name="internet-peering-vs-peering-service"></a>Internet eşlemesi ve Eşleme Hizmeti

Internet eşlemesi, Microsoft 'un genel ağı (AS8075) ve taşıyıcılar veya hizmet sağlayıcıları ağı arasındaki herhangi bir bağlantı anlamına gelir. Bir hizmet sağlayıcı, müşterinin Microsoft ağı ile en iyi şekilde yönlendirmeyle güvenilir ve yüksek performanslı genel bağlantı sağlamak için aşağıda açıklanan eşleme hizmeti ortaklığı gereksinimleri uygulayarak bir eşleme hizmeti ortağı olabilir.

## <a name="about-peering-service"></a>Eşleme Hizmeti hakkında
Eşleme hizmeti, kurumsal kullanıcılarına sınıfının en iyi genel Internet bağlantısını sağlayan temel hizmet sağlayıcılarının bulunduğu bir ortaklık programıdır. Programın parçası olan iş ortakları doğrudan, yüksek oranda kullanılabilir, coğrafi olarak yedekli bağlantılar ve Microsoft 'a iyileştirilmiş yönlendirmeye sahip olur. Eşleme hizmeti, Microsoft bağlantı portföyüne bir ektir:
*   IaaS veya PaaS kaynaklarına özel bağlantı için ExpressRoute (özel IP alanı için destek)
    *   İş ortağı tabanlı bağlantı
    *   Microsoft 'a doğrudan 100G bağlantısı
*   Buluta VPN bağlantısı için Internet üzerinden ıPSEC
*   SD-sanal WAN aracılığıyla Azure ile WAN bağlantısı

Eşleme hizmeti için hedef segment SaaS bağlantısı, SD-WAN müşterileri, dala ve çift strateji MPLS 'e ve kurumsal düzeyde Internet 'e sahip olan müşteriler için internet 'te bağlantı kurabilmek isteyen bir müşteri.

Microsoft Bulut bağlanırken birincil hedef, Microsoft 'un tüm veri merkezlerini ve birden çok bulut uygulama giriş noktasını birbirine bağlayan Microsoft 'un ortak ağ omurgası olan bir Kullanıcı sitesinden Microsoft küresel ağı 'na gidiş dönüş süresini (RTT) azaltarak gecikme süresini en aza indirmektir. Bkz. [Office 365 ' de en iyi bağlantı ve performansı alma](https://techcommunity.microsoft.com/t5/Office-365-Blog/Getting-the-best-connectivity-and-performance-in-Office-365/ba-p/124694).

> [!div class="mx-imgBorder"]
> ![Dağıtılmış erişimin görüntüsü](./media/distributed-access.png)

Küresel bir kuruluşun her bir şubesi yukarıdaki şekilde, iş ortağının ağı aracılığıyla mümkün olan en yakın Microsoft Edge konumuna bağlanır.

**Eşleme hizmeti müşteri avantajları:**
* En iyi performans ve güvenilirlik için Microsoft Bulut Hizmetleri Internet üzerinden en iyi genel yönlendirme.
* Microsoft Bulut bağlanmak için tercih edilen hizmet sağlayıcısını seçme özelliği.
* Gecikme süresi raporlama ve önek izleme gibi trafik öngörüleri.
* Microsoft Bulut 'den en uygun ağ atlaması (atlama olarak).
* Rota Analizi ve istatistikleri-Sınır Ağ Geçidi Protokolü ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) yol bozukluileri (sızıntı/hijak Detection) ve alt öğe yönlendirme yönlendirmesi için olaylar.

## <a name="peering-service-partnership-requirements"></a>Eşleme hizmeti ortaklığı gereksinimleri
* Müşteriye yakın bir konumda Microsoft Bulut bağlantı. Bir iş ortağı hizmeti sağlayıcısı, Kullanıcı trafiğini kullanıcıya en yakın Microsoft Edge 'e yönlendirir. Benzer şekilde, kullanıcıya doğru olan trafik üzerinde, Microsoft trafiği (BGP etiketi kullanılarak) kullanıcıya en yakın kenar konumuna yönlendirir ve hizmet sağlayıcısına trafiği kullanıcıya teslim eder.
* İş ortağı, Microsoft Global Network ile yüksek oranda kullanılabilir, yüksek aktarım hızı ve coğrafi olarak yedekli bağlantıyı koruyacaktır.
* İş ortağı, gereksinimi karşılıyorsa eşleme hizmetini desteklemek için mevcut eşlerinden yararlanabilir.

## <a name="faq"></a>SSS
Sık sorulan sorular için bkz. [eşleme hizmeti-SSS](service-faqs.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Eşleme hizmeti](../peering-service/index.yml)ile ilgili müşteri avantajları hakkında daha fazla bilgi edinin.
* [Eşleme hizmeti iş ortağı](walkthrough-peering-service-all.md)Kılavuzu 'Ndaki eşleme hizmeti için doğrudan eşlemeyi etkinleştirme adımları hakkında bilgi edinin.