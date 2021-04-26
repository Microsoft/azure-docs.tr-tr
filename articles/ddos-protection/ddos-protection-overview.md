---
title: Azure DDoS koruması standardına genel bakış
description: Azure DDoS Koruması Standart'ın, uygulama tasarımı en iyi yöntemleriyle birleştirildiğinde DDoS saldırılarına karşı nasıl savunma sağladığını öğrenin.
services: virtual-network
documentationcenter: na
author: aletheatoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: c8b3b0227e293c42ac9db8f888bc863e073ff6f5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103343"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS koruması standardına genel bakış

Dağıtılmış hizmet engelleme (DDoS) saldırıları, uygulamalarını buluta taşıyan müşterilerin karşılaştığı en büyük kullanılabilirlik ve güvenlik sorunlarından biridir. DDoS saldırısı, uygulamanın kaynaklarını tüketmeye çalışır ve uygulamayı meşru kullanıcılar için kullanılamaz hale getirir. DDoS saldırıları internet üzerinden genel olarak erişilebilen herhangi bir uç noktasını hedefleyebilir.

Azure 'daki her özellik hiçbir ek ücret ödemeden Azure 'un altyapı DDoS (temel) koruması tarafından korunur. Küresel olarak dağıtılan Azure ağının ölçeği ve kapasitesi, her zaman açık trafik izleme ve gerçek zamanlı risk azaltma aracılığıyla ortak ağ katmanı saldırılarına karşı savunma sağlar. DDoS koruması temel, Kullanıcı Yapılandırması veya uygulama değişikliği gerektirmez. DDoS koruması temel, Azure DNS gibi PaaS hizmetleri de dahil olmak üzere tüm Azure hizmetlerini korumanıza yardımcı olur.

Azure DDoS koruma standardı, uygulama tasarımı en iyi uygulamaları ile birlikte, DDoS saldırılarına karşı savunmak için gelişmiş DDoS azaltma özellikleri sağlar. Bir sanal ağdaki belirli Azure kaynaklarınızı korumaya yardımcı olmak üzere otomatik olarak ayarlanır. Korumanın, yeni veya mevcut bir sanal ağda etkinleştirilmesi basittir ve uygulama veya kaynak değişikliği gerektirmez. Günlük, uyarı ve telemetri dahil olmak üzere temel hizmet üzerinde çeşitli avantajları vardır. 

![Azure DDoS Koruması hizmeti karşılaştırması](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS koruması, müşteri verilerini depolamaz.

## <a name="features"></a>Özellikler

- **Yerel platform tümleştirmesi:** Azure ile yerel olarak tümleşiktir. Azure portal aracılığıyla yapılandırmayı içerir. DDoS koruma standardı, kaynaklarınızı ve kaynak yapılandırmanızı anlamıştır.
- **Anahtar koruma:** Basitleştirilmiş yapılandırma, DDoS koruma standardı etkin olduğu anda bir sanal ağ üzerindeki tüm kaynakları hemen korur. Müdahale veya Kullanıcı tanımı gerekli değildir. 
- **Her zaman açık trafik izleme:** Uygulama trafik desenleriniz günde 24 saat, haftada 7 gün, DDoS saldırıları için göstergeler aranıyor. DDoS koruma standardı, algılandıktan sonra hızla ve otomatik olarak saldırıyı azaltır.
- **Uyarlamalı ayarlama:** Akıllı trafik profili oluşturma, uygulamanızın zaman içindeki trafiğini öğrenir ve hizmetiniz için en uygun profili seçer ve güncelleştirir. Profil zaman içinde trafik değişikliği olarak ayarlanır.
- **Çok katmanlı koruma:** Bir Web uygulaması güvenlik duvarı (WAF) ile birlikte dağıtıldığında, DDoS koruma standardı hem Ağ katmanında (katman 3 ve 4 ' te Azure DDoS koruma standardı tarafından sunulan) hem de uygulama katmanında (bir WAF tarafından sunulan) korunur. WAF teklifleri Azure [Application Gateway WAF SKU 'su](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)'nde bulunan üçüncü taraf Web uygulaması güvenlik duvarı tekliflerini içerir.
- **Kapsamlı risk azaltma ölçeği:** 60 üzerinde farklı saldırı türleri, en büyük bilinen DDoS saldırılarına karşı koruma sağlamak için küresel kapasiteyle birlikte azaltılabilir.
- **Saldırı Analizi:** Saldırı sırasında beş dakikalık artışlarla ayrıntılı raporlar alın ve saldırı bittikten sonra tam bir Özet yapın. Bir saldırı sırasında neredeyse gerçek zamanlı izleme için [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) 'e veya çevrimdışı güvenlik bilgilerine ve olay yönetimi (SIEM) sistemine yönelik günlük azaltma akışı.
- **Saldırı ölçümleri:** Her bir saldırıya ait özetlenen ölçümler Azure Izleyici aracılığıyla erişilebilir.
- **Saldırı uyarısı:** Uyarılar, yerleşik saldırı ölçümleri kullanılarak saldırı başlangıcında ve durdurulduğunda ve saldırının süresi boyunca yapılandırılabilir. Uyarılar, Microsoft Azure Izleme günlükleri, splunk, Azure depolama, e-posta ve Azure portal gibi işletimsel yazılımınızla tümleştirilir.
- **DDoS hızlı yanıt**: saldırı araştırması ve analizine yönelik yardım Için DDoS koruması hızlı yanıt (drr) ekibine katılın. Daha fazla bilgi için bkz. [DDoS hızlı yanıt](ddos-rapid-response.md).
- **Maliyet garantisi:** Veri aktarımı ve uygulama genişleme hizmeti kredisi, belgelenen DDoS saldırıları sonucunda oluşan kaynak maliyetleri için alma.

## <a name="pricing"></a>Fiyatlandırma

DDoS koruma planları, en fazla 100 genel IP adresini kapsayan ayda aylık $2.944 sabit ücretine sahiptir. Ek kaynaklara yönelik koruma, kaynak başına aylık olarak ek $30 ücret alınacaktır.

Bir kiracı altında, tek bir DDoS koruma planı birden çok abonelik genelinde kullanılabilir, bu nedenle birden fazla DDoS koruma planı oluşturmaya gerek yoktur.

Azure DDoS koruması standart fiyatlandırması hakkında bilgi edinmek için bkz. [Azure DDoS koruması standart fiyatlandırması](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="reference-architectures"></a>Başvuru mimarileri

DDoS koruma standardı, [sanal bir ağa dağıtılan hizmetler](../virtual-network/virtual-network-for-azure-services.md)için tasarlanmıştır. Diğer hizmetler için, varsayılan DDoS koruması temel hizmeti geçerlidir. Desteklenen mimariler hakkında daha fazla bilgi edinmek için bkz. [DDoS koruması başvuru mimarileri](./ddos-protection-reference-architectures.md). 

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [DDoS koruma planı oluşturma](manage-ddos-protection.md)