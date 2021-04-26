---
title: Application Gateway Azure Web uygulaması güvenlik duvarı-sık sorulan sorular
description: Bu makalede, Application Gateway üzerinde Web uygulaması güvenlik duvarı hakkında sık sorulan sorulara yanıtlar verilmektedir
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 890688dba70a7fa654e97652b3e474b919f9a077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585392"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Application Gateway 'de Azure Web uygulaması güvenlik duvarı hakkında sık sorulan sorular

Bu makalede, Application Gateway özellikleri ve işlevleri üzerinde Azure Web uygulaması güvenlik duvarı (WAF) hakkında sık sorulan sorular yanıtlanmaktadır. 

## <a name="what-is-azure-waf"></a>Azure WAF nedir?

Azure WAF, Web uygulamalarınızı SQL ekleme, siteler arası komut dosyası oluşturma ve diğer web siteleri gibi yaygın tehditlere karşı korumanıza yardımcı olan bir Web uygulaması güvenlik duvarıdır. Web uygulamalarınıza erişimi denetlemek için özel ve yönetilen kuralların birleşiminden oluşan bir WAF ilkesi tanımlayabilirsiniz.

Azure WAF ilkesi, Application Gateway veya Azure ön kapılarında barındırılan Web uygulamalarına uygulanabilir.

## <a name="what-features-does-the-waf-sku-support"></a>WAF SKU 'SU hangi özellikleri destekler?

WAF SKU 'SU standart SKU 'da bulunan tüm özellikleri destekler.

## <a name="how-do-i-monitor-waf"></a>Nasıl yaparım? İzleyicisi WAF mi?

Tanılama günlük kaydı ile WAF 'yi izleyin. Daha fazla bilgi için bkz. [Application Gateway Için tanılama günlüğü ve ölçümleri](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>Algılama modu trafiği engelliyor mu?

Hayır. Algılama modu yalnızca bir WAF kuralını tetikleyen trafiği günlüğe kaydeder.

## <a name="can-i-customize-waf-rules"></a>WAF kurallarını özelleştirebilir miyim?

Evet. Daha fazla bilgi için bkz. [WAF kural gruplarını ve kurallarını özelleştirme](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>WAF için şu anda hangi kurallar kullanılabilir?

WAF Şu anda sp_configure [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)ve [3,1](application-gateway-crs-rulegroups-rules.md#owasp31)' i desteklemektedir. Bu kurallar, Web uygulaması güvenlik projesini (OWASP) açan en yüksek 10 güvenlik açığına karşı temel güvenlik sağlar: 

* SQL ekleme koruması
* Siteler arası betik koruması
* Komut ekleme, HTTP isteği, HTTP yanıtı bölme ve uzak dosya ekleme saldırısı gibi yaygın web saldırılarına karşı koruma
* HTTP protokolü ihlallerine karşı koruma
* Eksik konak kullanıcısı-aracısı ve kabul üst bilgileri gibi HTTP protokolü anormalliklerine karşı koruma
* Robotlar, gezginler ve tarayıcıları önleme
* Yaygın uygulama yapılandırmalarını algılama (yani, Apache, IIS vb.)

Daha fazla bilgi için bkz. [OWASP Top-10 güvenlik açıkları](https://owasp.org/www-project-top-ten/).

## <a name="what-content-types-does-waf-support"></a>WAF hangi içerik türlerini destekler?

Application Gateway WAF, yönetilen kurallar için aşağıdaki içerik türlerini destekler:

* uygulama/json
* Uygulama/XML
* Application/x-www-form-urlencoded
* multipart/form-Data

Ve özel kurallar için:

* Application/x-www-form-urlencoded
* multipart/form-Data

## <a name="does-waf-support-ddos-protection"></a>WAF, DDoS korumasını destekliyor mu?

Evet. DDoS korumasını, uygulama ağ geçidinin dağıtıldığı sanal ağ üzerinde etkinleştirebilirsiniz. Bu ayar, Azure DDoS Koruması hizmetinin Application Gateway sanal IP 'yi (VIP) de korumasını sağlar.

### <a name="does-waf-store-customer-data"></a>Müşteri verilerini WAF olarak depolıyor mu?

Hayır, WAF müşteri verilerini depolamaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Web uygulaması güvenlik duvarı](../overview.md)hakkında bilgi edinin.
- [Azure ön kapısı](../../frontdoor/front-door-overview.md)hakkında daha fazla bilgi edinin.
