---
title: DDoS yanıt stratejisinin bileşenleri
description: DDoS saldırılarına yanıt vermek için Azure DDoS koruma standardı 'nı nasıl kullanacağınızı öğrenin.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: c028185a0753bc4da966ebc56bb792e57cac1f77
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103054"
---
# <a name="components-of-a-ddos-response-strategy"></a>DDoS yanıt stratejisinin bileşenleri

Azure kaynaklarını hedefleyen bir DDoS saldırısı genellikle Kullanıcı açısından çok az müdahale gerektirir. Yine de, bir olay yanıt stratejisinin parçası olarak DDoS azaltma 'yı eklemek, iş sürekliliği etkisini en aza indirmenize yardımcı olur.

## <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

Microsoft 'un kapsamlı bir tehdit zekası ağı vardır. Bu ağ, Internet güvenlik topluluğundaki Microsoft çevrimiçi hizmetler, Microsoft iş ortakları ve ilişkileri destekleyen bir genişletilmiş güvenlik topluluğunun toplu bilgisini kullanır. 

Kritik bir altyapı sağlayıcısı olarak, Microsoft tehditler hakkında erken uyarılar alır. Microsoft, çevrimiçi hizmetler tehdit zekasını ve küresel müşteri temelini toplar. Microsoft bu tehdit zekasını Azure DDoS koruma ürünlerine geri ekler.

Ayrıca, Microsoft Digital Crimes birimi (DCU) botları için rahatsız edici stratejiler uygular. Botlar, DDoS saldırıları için ortak bir komut ve denetim kaynağıdır.

## <a name="risk-evaluation-of-your-azure-resources"></a>Azure kaynaklarınızın risk değerlendirmesi

DDoS saldırılarına karşı risk kapsamını sürekli olarak anlamanız zorunludur. Düzenli olarak kendinize sorun:

- Hangi yeni genel kullanıma açık Azure kaynakları koruması gerekir?

- Hizmette tek hata noktası var mı? 

- Hizmetler geçerli müşteriler için kullanılabilir olmaya devam ederken, bir saldırının etkilerini sınırlamak için hizmetler nasıl yalıtılarak?

- DDoS koruma standardının etkinleştirilmesi gereken ancak olmaması gereken sanal ağlar var mı? 

- Hizmetlerim, birden çok bölgede yük devretmeyle etkin/etkin mi?

Bir uygulamanın normal davranışını anlamanız ve uygulama DDoS saldırısı sırasında beklendiği gibi davranmıyorsa, uygulamaya hazırlanmanız önemlidir. , İstemci davranışını taklit eden, iş açısından kritik uygulamalarınız için yapılandırılmış izleyicilere sahiptir ve ilgili anomali algılandığında size bildirimde bulunur. Uygulamanızın sistem durumu hakkında öngörüler elde etmek için [izleme ve tanılama en iyi uygulamaları](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) bölümüne bakın.

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) , birden çok platformda Web geliştiricileri için genişletilebilir bir uygulama performans YÖNETIMI (APM) hizmetidir. Canlı Web uygulamanızı izlemek için Application Insights kullanın. Performans bozuklulıkları otomatik olarak algılar. Sorunları tanılamanıza ve kullanıcıların uygulamanızla ne yaptığını anlamanıza yardımcı olacak analiz araçları içerir. Performansı ve kullanılabilirliği sürekli geliştirmenize yardımcı olmak amacıyla tasarlanmıştır.

## <a name="customer-ddos-response-team"></a>Müşteri DDoS yanıt ekibi

DDoS yanıt ekibi oluşturmak, bir saldırının hızla ve etkili bir şekilde yanıt vermemesine neden olan önemli bir adımdır. Kuruluşunuzda hem planlama hem de yürütmeyi alacak kişileri belirler. Bu DDoS yanıt ekibi, Azure DDoS koruması standart hizmetini iyice anlamalıdır. Ekibin, Microsoft destek ekibi de dahil olmak üzere dahili ve harici müşterilerle koordine ederek bir saldırıyı belirleyebilmesi ve hafifletmek için emin olun. 

Simülasyon alıştırmalarını, hizmet kullanılabilirliği ve süreklilik planlamanızın normal bir parçası olarak kullanmanızı ve bu alýþtýrmalar ölçek testi içermelidir. Azure genel uç noktalarınıza karşı DDoS test trafiğinin benzetimini yapmayı öğrenmek için bkz. [benzetimler üzerinden test](test-through-simulations.md) etme.

## <a name="alerts-during-an-attack"></a>Saldırı sırasında uyarılar

Azure DDoS koruması standardı, herhangi bir kullanıcı müdahalesi olmadan DDoS saldırılarını tanımlar ve azaltır. Korunan genel IP için etkin bir risk azaltma olduğunda bildirim almak için [uyarıları yapılandırabilirsiniz](alerts.md).

### <a name="when-to-contact-microsoft-support"></a>Microsoft desteği 'ne ne zaman iletişim kura

Azure DDoS koruması standart müşterileri, bir saldırı sırasında saldırı sonrası analizler ve saldırı sırasında saldırı konusunda yardımcı olabilecek DDoS hızlı yanıt (DRR) ekibine erişebilir. DRR ekibine ne zaman dahil edilmesi gerektiği hakkında daha fazla bilgi için bkz. [DDoS hızlı yanıt](ddos-rapid-response.md) .

## <a name="post-attack-steps"></a>Saldırı sonrası adımlar

Bir saldırıya uğradıktan sonra her zaman iyi bir stratejidir ve DDoS yanıt stratejisini gereken şekilde ayarlayın. Göz önüne almanız gerekenler:

- Ölçeklenebilir mimarinin olmaması nedeniyle hizmet veya Kullanıcı deneyimine bir kesinti vardı mi?

- En çok hangi uygulama veya hizmet var?

- DDoS yanıt stratejisi ne kadar etkili oldu ve nasıl geliştirilebilir?

DDoS saldırısının altında olduğunuzdan şüpheleniyorsanız, normal Azure destek kanallarınız aracılığıyla ilerletin.

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruma planı oluşturma](manage-ddos-protection.md)hakkında bilgi edinin.