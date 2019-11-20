---
title: Azure Web uygulaması güvenlik duvarı-sık sorulan sorular
description: Bu makalede, Azure ön kapısı hizmetinde Web uygulaması güvenlik duvarı hakkında sık sorulan sorulara yanıtlar verilmektedir
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: 263f929fa010450fcfb35b8db74f94cd6fd7dc84
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517182"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Azure ön kapı hizmeti 'nde Azure Web uygulaması güvenlik duvarı hakkında sık sorulan sorular

Bu makalede, Azure Web uygulaması güvenlik duvarı (WAF) özellikleri ve işlevleri hakkında sık sorulan sorular yanıtlanmaktadır. 

## <a name="what-is-azure-waf"></a>Azure WAF nedir?

Azure WAF, Web uygulamalarınızı SQL ekleme, siteler arası komut dosyası oluşturma ve diğer web siteleri gibi yaygın tehditlere karşı korumanıza yardımcı olan bir Web uygulaması güvenlik duvarıdır. Web uygulamalarınıza erişimi denetlemek için özel ve yönetilen kuralların birleşiminden oluşan bir WAF ilkesi tanımlayabilirsiniz.

Azure WAF ilkesi, Application Gateway veya Azure ön kapılarında barındırılan Web uygulamalarına uygulanabilir.

## <a name="what-is-waf-on-azure-front-door-service"></a>Azure ön kapı hizmeti 'nde WAF nedir? 

Azure ön kapısı, yüksek düzeyde ölçeklenebilir, global olarak dağıtılmış bir uygulama ve içerik teslim ağı. Azure WAF, ön kapıya tümleştirildiğinde hizmet reddi ve hedeflenen uygulama saldırılarını, Sanal ağınızı girmeden önce, saldırı kaynaklarına yakın bir şekilde kapatır ve performanstan ödün vermeden koruma sunar.

## <a name="does-azure-waf-support-https"></a>Azure WAF, HTTPS 'yi destekliyor mu?

Ön kapı hizmeti, SSL boşaltma sağlar. WAF, ön kapıya yerel olarak tümleşiktir ve şifresi çözüledikten sonra bir isteği inceleyebilir.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF, IPv6 'Yı destekliyor mu?

Evet. IPv4 ve IPv6 için IP kısıtlaması yapılandırabilirsiniz.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Yönetilen kural kümeleri güncel değil mi?

Tehdit Yatayı değiştirmekle en iyi şekilde haberdar ediyoruz. Yeni bir kural güncelleştirildikten sonra, varsayılan kural kümesine yeni bir sürüm numarasıyla eklenir.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>WAF ilkenizde bir değişiklik yaptım zaman yayma süresi nedir?

Bir WAF ilkesini küresel olarak dağıtmak yaklaşık 5 dakika sürer ve genellikle daha erken tamamlanır.

## <a name="can-waf-policies-be-different-for-different-regions"></a>WAF ilkeleri farklı bölgeler için farklı olabilir mi?

Ön kapı hizmeti ile tümleştirildiğinde WAF genel bir kaynaktır. Tüm ön kapı konumlarında aynı yapılandırma geçerlidir.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Nasıl yaparım? arka uca erişimi yalnızca ön kapıdan olacak şekilde sınırlayın mi?

Arka uçınızdan IP Access Control listesini, yalnızca ön kapı giden IP adresi aralıklarına izin vermek ve Internet 'ten doğrudan erişimi reddetmek için yapılandırabilirsiniz. Sanal ağınızda kullanabilmeniz için hizmet etiketleri desteklenir. Ayrıca, X ile Iletilen ana bilgisayar HTTP üst bilgisi alanının Web uygulamanız için geçerli olduğunu doğrulayabilirsiniz.

## <a name="which-azure-waf-options-should-i-choose"></a>Hangi Azure WAF seçeneklerini seçmem gerekir?

Azure 'da WAF İlkeleri uygulanırken iki seçenek vardır. Azure ön kapısına sahip WAF, küresel olarak dağıtılmış bir uç güvenlik çözümüdür. Application Gateway ile WAF, bölgesel ve adanmış bir çözümdür. Genel performans ve güvenlik gereksinimlerinize göre bir çözüm seçmenizi öneririz. Daha fazla bilgi için bkz. [Azure 'un uygulama teslim paketiyle Yük Dengeleme](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Tümleştirilen tüm platformlarda aynı WAF özelliklerini destekliyor musunuz?

Şu anda ModSec sp_configure 2.2.9, sp_configure 3,0 ve yukarı 3,1 kuralları yalnızca Application Gateway WAF ile desteklenir. Hız sınırlaması, coğrafi filtreleme ve Azure tarafından yönetilen varsayılan kural kümesi kuralları yalnızca Azure ön kapıda WAF ile desteklenir.

## <a name="is-ddos-protection-integrated-with-front-door"></a>DDoS koruması ön kapıla tümleşiktir mı? 

Azure ağ kenarlarında küresel olarak dağıtılan Azure ön kapısı, büyük hacimli saldırıları artışlarını devralarak ve coğrafi olarak yalıtabilir. Bilinen imzaları olan http (ler) saldırılarını otomatik olarak engellemek ve derecelendirmek için özel WAF ilkesi oluşturabilirsiniz. Daha fazla bilgi için arka uçlarınızın dağıtıldığı VNet üzerinde DDoS koruma standardını etkinleştirebilirsiniz. Azure DDoS koruması standart müşterileri, bir saldırı sırasında anında yardım almak için maliyet koruması, SLA garantisi ve DDoS hızlı yanıt ekibinden uzmanlara erişim gibi ek avantajlar sağlar.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Hız sınırı kuralı için yapılandırılan eşiğin üzerinde neden ek istekler, arka uç sunucuma geçirilir?

Bir hız sınırı kuralı, anormal yüksek trafiği herhangi bir istemci IP adresinden sınırlayabilir. Bir dakikalık veya beş dakikalık bir süre boyunca istemci IP adresinden izin verilen Web isteği sayısında bir eşik yapılandırabilirsiniz. Ayrıntılı hız denetimi için, hız sınırlaması, HTTP (S) parametre eşleşmesi gibi ek eşleşme koşullarıyla birleştirilebilir. 

Aynı istemciden gelen istekler genellikle aynı ön kapı sunucusuna ulaşır. Bu durumda, eşiğin üzerindeki ek isteklerin hemen engellendiğini görürsünüz. 

Ancak, aynı istemciden gelen istekler, henüz hız limiti sayacını yenilemeyen farklı bir ön kapıya sahip sunucuya gelebilir. Örneğin, istemci her istek için yeni bir bağlantı açabilir ve eşik düşüktür. Bu durumda, yeni ön kapı sunucusuna yapılan ilk istek hız limiti denetimini geçiyordu. Bir hız sınırı eşiği, genellikle herhangi bir istemci IP adresinden hizmet reddi saldırılarına karşı savunacak şekilde ayarlanır. Çok düşük bir eşik için, eşiğin üzerindeki ek istekleri daha fazla bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Web uygulaması güvenlik duvarı](../overview.md)hakkında bilgi edinin.
- [Azure ön kapısı](../../frontdoor/front-door-overview.md)hakkında daha fazla bilgi edinin.
