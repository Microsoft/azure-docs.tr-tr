---
title: Azure DDoS koruması standardına genel bakış | Microsoft Docs
description: Azure DDoS Koruması hizmeti hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: ef2319f18b6df15fd7f33e9344e8506f853f47e6
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532232"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS koruması standardına genel bakış

Dağıtılmış hizmet engelleme (DDoS) saldırıları, uygulamalarını buluta taşıyan müşterilerin karşılaştığı en büyük kullanılabilirlik ve güvenlik sorunlarından biridir. DDoS saldırıları, bir uygulamanın kaynaklarını tüketerek uygulamanın geçerli kullanıcılar için kullanılamaz duruma gelmesini amaçlar. DDoS saldırıları internet üzerinden genel olarak erişilebilen herhangi bir uç noktasını hedefleyebilir.

Azure DDoS koruması, uygulama tasarımı en iyi uygulamaları ile birlikte, DDoS saldırılarına karşı savunma sağlar. Azure DDoS koruması aşağıdaki hizmet katmanlarını sağlar:

- **Temel**: Azure platformunun bir parçası olarak otomatik olarak etkinleştirilir. Her zaman açık trafik izleme ve ortak ağ düzeyinde saldırıları gerçek zamanlı azaltma, Microsoft 'un çevrimiçi hizmetler tarafından kullanılan savunmaları sağlar. Azure 'un genel ağının tüm ölçeklendirilmesi, bölgeler arasında saldırı trafiğini dağıtmak ve azaltmak için kullanılabilir. IPv4 ve IPv6 Azure [genel IP adresleri](virtual-network-public-ip-address.md)için koruma sağlanır.
- **Standart**: Azure sanal ağ kaynakları için özel olarak ayarlanan temel hizmet katmanı üzerinden ek risk azaltma özellikleri sağlar. DDoS koruma standardının etkinleştirilmesi kolaydır ve uygulama değişikliği gerektirmez. Koruma ilkeleri adanmış trafik izleme ve makine öğrenimi algoritmalarıyla ayarlanır. İlkeler, Azure Load Balancer, Azure Application Gateway ve Azure Service Fabric örnekleri gibi sanal ağlarda dağıtılan kaynaklarla ilişkili genel IP adreslerine uygulanır, ancak bu koruma App Service ortamları için uygulanmaz. Gerçek zamanlı telemetri, bir saldırı sırasında Azure Izleyici görünümleri ve geçmiş için kullanılabilir. Tanılama ayarları aracılığıyla zengin saldırı risk azaltma analizlerine ulaşılabilir. Uygulama katmanı koruması, [azure Application Gateway Web uygulaması güvenlik duvarı](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aracılığıyla veya Azure Marketi 'nden üçüncü taraf güvenlik duvarı yükleyerek eklenebilir. IPv4 ve IPv6 Azure [genel IP adresleri](virtual-network-public-ip-address.md)için koruma sağlanır.

|Özellik                                         |DDoS koruması temel                 |DDoS koruma standardı                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Etkin trafik izleme & her zaman algılama |Yes                                   |Yes                                           |
|Otomatik saldırı azaltmaları                    |Yes                                   |Yes                                           |
|Kullanılabilirlik garantisi                          |Azure Bölgesi                          |Uygulama                                   |
|Risk azaltma ilkeleri                             |Azure trafik bölgesi birimi için ayarlandı |Uygulama trafiği birimi için ayarlanmış          |
|Ölçümler ve uyarılar                                |Hayır                                    |Azure izleyici ile gerçek zamanlı saldırı ölçümleri & tanılama günlükleri                                 |
|Risk azaltma raporları                              |Hayır                                    |Saldırı sonrası risk azaltma raporları                |
|Risk azaltma akış günlükleri                            |Hayır                                    |SıEM tümleştirmesi için NRT günlük akışı           |
|Geçiş ilkesi özelleştirmeleri                 |Hayır                                    |DDoS uzmanlarına katılın                           |
|Destek                                         |En iyi çaba                           |Etkin bir saldırı sırasında DDoS uzmanlarına erişim|
|SLA                                             |Azure Bölgesi                          |Uygulama garantisi & maliyet koruması       |
|Fiyatlandırma                                         |Ücretsiz                                  |Aylık & kullanımı tabanlı                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS koruması standart azaltmalarına sahip DDoS saldırıları türleri

DDoS koruma standardı aşağıdaki tür saldırıları hafifletmenize yardımcı olabilir:

- **Volumetric saldırıları**: saldırının hedefi, ağ katmanını önemli miktarda meşru trafik ile taşmaktır. Bu, UDP floods, yükseltme floods ve diğer sahte paket floods içerir. DDoS koruma standardı, bu potansiyel çok gigabaytlık saldırıları, Azure 'un küresel ağ ölçeklendirilmesi ve bunları otomatik olarak yaparak, bu olası çok kiracılı saldırıları azaltır.
- **Protokol saldırıları**: Bu saldırılar, katman 3 ve katman 4 protokol yığınındaki zayıf bir durumu kötüye vererek bir hedefi işlemez. Bu, SYN taşma saldırıları, yansıma saldırıları ve diğer protokol saldırıları içerir. DDoS koruması standardı, bu saldırıları azaltır, istemciyle etkileşime girerek ve kötü amaçlı trafiği engelleyerek kötü amaçlı ve meşru trafik arasında ayrım gerçekleştirebilir. 
- **Kaynak (uygulama) katmanı saldırıları**: Bu saldırılar, ana bilgisayarlar arasında veri aktarımını kesintiye uğratan Web uygulaması paketlerini hedeflemelidir. Saldırılara HTTP protokol ihlalleri, SQL ekleme, siteler arası komut dosyası oluşturma ve diğer katman 7 saldırıları dahildir. Bu saldırılara karşı savunma sağlamak için DDoS koruma standardı ile Azure [Application Gateway Web uygulaması güvenlik duvarı](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)'nı kullanın. Ayrıca, [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)'nde bulunan üçüncü taraf Web uygulaması güvenlik duvarı teklifleri de mevcuttur.

DDoS koruma standardı sanal makineler, yük dengeleyiciler ve uygulama ağ geçitleri ile ilişkili genel IP adresleri dahil bir sanal ağdaki kaynakları korur. DDoS koruma standardı, Application Gateway Web uygulaması güvenlik duvarı ile birlikte kullanıldığında, katman 7 risk azaltma özelliğine tam katman 3 sağlayabilir.

## <a name="ddos-protection-standard-features"></a>DDoS koruması standart özellikleri

![DDoS işlevselliği](./media/ddos-protection-overview/ddosfeatures.png)

DDoS koruması standart özellikleri şunlardır:

- **Yerel platform tümleştirmesi:** Azure ile yerel olarak tümleşiktir. Azure portal aracılığıyla yapılandırmayı içerir. DDoS koruma standardı, kaynaklarınızı ve kaynak yapılandırmanızı anlamıştır.
- **Anahtar korumasını aç:** Basitleştirilmiş yapılandırma, DDoS koruma standardı etkin olduğu anda bir sanal ağ üzerindeki tüm kaynakları hemen korur. Müdahale veya Kullanıcı tanımı gerekli değildir. DDoS koruma standardı, algılandıktan sonra hızla ve otomatik olarak saldırıyı azaltır.
- **Her zaman açık trafik izleme:** Uygulama trafik desenleriniz günde 24 saat, haftada 7 gün, DDoS saldırıları için göstergeler aranıyor. Koruma ilkeleri aşıldığında risk azaltma gerçekleştirilir.
- **Uyarlamalı ayarlama:** Akıllı trafik profili oluşturma, uygulamanızın zaman içindeki trafiğini öğrenir ve hizmetiniz için en uygun profili seçer ve güncelleştirir. Profil zaman içinde trafik değişikliği olarak ayarlanır.
- **Çok katmanlı koruma:** Bir Web uygulaması güvenlik duvarıyla birlikte kullanıldığında, tam yığın koruması sağlar.
- **Kapsamlı risk azaltma ölçeği:** 60 üzerinde farklı saldırı türleri, en büyük bilinen DDoS saldırılarına karşı koruma sağlamak için küresel kapasiteyle birlikte azaltılabilir.
- **Saldırı Analizi:** Saldırı sırasında beş dakikalık artışlarla ayrıntılı raporlar alın ve saldırı bittikten sonra tam bir Özet yapın. Bir saldırı sırasında neredeyse gerçek zamanlı izleme için, çevrimdışı bir güvenlik bilgileri ve olay yönetimi (SıEM) sistemine akış azaltma günlüğü.
- **Saldırı ölçümleri:** Her bir saldırıya ait özetlenen ölçümler Azure Izleyici aracılığıyla erişilebilir.
- **Saldırı uyarısı:** Uyarılar, yerleşik saldırı ölçümleri kullanılarak saldırı başlangıcında ve durdurulduğunda ve saldırının süresi boyunca yapılandırılabilir. Uyarılar, Microsoft Azure Izleme günlükleri, splunk, Azure depolama, e-posta ve Azure portal gibi işletimsel yazılımınızla tümleştirilir.
- **Maliyet garantisi:** Veri aktarımı ve belgelenmiş DDoS saldırıları için uygulama genişleme hizmeti kredileri.

## <a name="ddos-protection-standard-mitigation"></a>DDoS koruması standart risk azaltma

DDoS koruması standart gerçek trafik kullanımını izler ve DDoS Ilkesinde tanımlanan eşiklere göre sürekli olarak karşılaştırır. Trafik eşiği aşıldığında, DDoS risk azaltma otomatik olarak başlatılır. Trafik eşiğin altına döndüğünde, risk azaltma kaldırılır.

![Risk azaltma](./media/ddos-protection-overview/mitigation.png)

Risk azaltma sırasında, korunan kaynağa gönderilen trafik DDoS koruma hizmeti tarafından yeniden yönlendirilir ve aşağıdaki denetimler gibi çeşitli denetimler gerçekleştirilir:

- Paketlerin internet belirtimlerine uyduğundan ve hatalı biçimlendirilmiş olmadığından emin olun.
- Trafiğin potansiyel olarak sahte bir paket olup olmadığını (ör: SYN auth veya SYN tanımlama bilgisi) ve yeniden aktarmak için kaynak için bir paket bırakarak istemciyle etkileşime geçin.
- Başka bir zorlama yöntemi gerçekleştirilmeyen hız limiti paketleri.

DDoS koruması, saldırı trafiğini engeller ve kalan trafiği amaçlanan hedefine iletir. Birkaç dakika içinde saldırı algılama sırasında Azure Izleyici ölçümleri kullanılarak bildirim alırsınız. DDoS koruması standart telemetrisinde günlüğe kaydetmeyi yapılandırarak, gelecekteki analizler için günlükleri kullanılabilir seçeneklere yazabilirsiniz. DDoS koruma standardı için Azure Izleyici 'de ölçüm verileri 30 gün boyunca tutulur.

Microsoft, simülasyon için DDoS koruması etkinleştirilmiş genel IP adreslerine karşı trafik oluşturabileceğiniz bir arabirim oluşturmak üzere [BreakingPoint bulutu](https://www.ixiacom.com/products/breakingpoint-cloud) ile işbirliği yaptı. Kesme noktası bulutu benzetimi şunları yapmanıza olanak sağlar:

- DDoS koruması standardının Microsoft Azure DDoS saldırılarına karşı Azure kaynaklarınızı nasıl koruduğunu doğrulayın
- DDoS saldırısı kapsamındaki olay yanıt sürecinizi iyileştirin
- DDoS uyumluluğunu belgele
- Ağ Güvenlik ekiplerinizi eğitme

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruma standardını yapılandırma](manage-ddos-protection.md)
