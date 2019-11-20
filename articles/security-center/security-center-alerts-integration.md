---
title: Azure Güvenlik Merkezi ile Azure güvenlik ürünlerini tümleştirme
description: Bu konu, Azure Güvenlik Merkezi ile tümleştirilmiş Azure güvenlik ürünlerini sunmaktadır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: dc1cb5cde06314e52ac886c1be1bb46b5f4bbd73
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686471"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde Azure güvenlik ürünlerini tümleştirme

Azure Güvenlik Merkezi, aşağıdaki güvenlik ürünleriyle çalışmak için ek Microsoft lisansları sağlar:

* [Azure WAF](#azure-waf)
* [Azure DDoS Koruması](#azure-ddos)

## Azure WAF<a name="azure-waf"></a>

Azure Application Gateway, web uygulamalarınız için açıklardan yararlanmaya ve güvenlik açıklarına karşı merkezi koruma sağlayan bir web uygulaması güvenlik duvarı (WAF) sunar.

Web uygulamaları, yaygın olarak bilinen güvenlik açıklarından yararlanan kötü amaçlı saldırılara karşı giderek daha da yöneliktir. WAF Application Gateway, açık Web uygulaması güvenlik projesinden 3,0 veya 2.2.9 temel kural kümesini temel alır. WAF, ek yapılandırma gerekmeden yeni güvenlik açıklarına karşı korunmak üzere otomatik olarak güncelleştirilir. WAF uyarıları Güvenlik Merkezi 'ne akışla kaydedilir. WAF tarafından oluşturulan uyarılar hakkında daha fazla bilgi için bkz. [Web uygulaması güvenlik duvarı Ayrıntılar kural grupları ve kuralları](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS koruması<a name="azure-ddos"></a>

Dağıtılmış hizmet reddi (DDoS) saldırılarına kolayca çalıştırılabilir. Özellikle uygulamalarınızı buluta taşıyorsanız harika bir güvenlik sorunu haline gelmiştir. 

DDoS saldırıları, bir uygulamanın kaynaklarını tüketerek uygulamanın geçerli kullanıcılar için kullanılamaz duruma gelmesini amaçlar. DDoS saldırıları Internet üzerinden erişilebilecek tüm uç noktaları hedefleyebilir.

Azure DDoS koruması, uygulama tasarımı en iyi uygulamaları ile birlikte, DDoS saldırılarına karşı bir savunma sağlar. DDoS koruması farklı hizmet katmanları sağlar. Daha fazla bilgi için bkz. [Azure DDoS korumasına genel bakış](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

DDoS koruma standardı aşağıdaki tür saldırıları hafifletmenize yardımcı olabilir:

> [!div class="mx-tableFixed"]

|Uyarı|Açıklama|
|---|---|
|**Volumetric saldırısı algılandı**|Bu saldırının amacı, ağ katmanını önemli miktarda meşru trafik ile taşmaktır. Bu, UDP floods, yükseltme floods ve diğer sahte paket floods içerir. DDoS koruması standardı, bu potansiyel çok gigabaytlık saldırıları, genel ağ ölçeklendirerek otomatik olarak, bu olası çok kiracılı saldırıları azaltır.|
|**Protokol saldırısı algılandı**|Bu saldırılar, katman 3 ve katman 4 protokol yığınlarındaki zayıf bir durumu kötüye vererek hedefe erişilemez. SYN taşma saldırıları, yansıma saldırıları ve diğer protokol saldırıları içerir. DDoS koruması standardı, bu saldırıları azaltır, istemciyle etkileşime girerek ve kötü amaçlı trafiği engelleyerek kötü amaçlı ve meşru trafik arasında ayrım gerçekleştirebilir.|
|**Kaynak (uygulama) katmanı saldırısı algılandı**|Bu saldırılar, ana bilgisayarlar arasında veri aktarımını kesintiye uğratan Web uygulaması paketlerini hedefleyebilir. Saldırılara HTTP protokol ihlalleri, SQL ekleme, siteler arası komut dosyası oluşturma ve diğer katman 7 saldırıları dahildir. Bu saldırılara karşı savunmak için DDoS koruma standardı ile Azure Application Gateway WAF 'yi kullanın. Ayrıca, Azure Marketi 'nde bulunan üçüncü taraf WAF teklifleri de mevcuttur.|
