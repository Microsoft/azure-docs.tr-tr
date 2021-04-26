---
title: 'Azure ön kapısı: DDoS koruması'
description: Bu sayfa, Azure ön kapısının Standart/Premium 'ın DDoS saldırılarına karşı korumaya nasıl yardımcı olduğuna ilişkin bilgiler sağlar
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100554"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Azure ön kapısı Standart/Premium 'da DDoS koruması (Önizleme)

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Azure ön kapısında dağıtılmış hizmet reddi (DDoS) saldırılarını önlemeye yardımcı olabilecek çeşitli özellikler ve özellikler vardır. Bu özellikler, saldırganların uygulamanıza ulaşmasını ve uygulamanızın kullanılabilirliğini ve performansını etkilemesini engelleyebilir.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Azure DDoS Protection temel ile tümleştirme

Ön kapı, Azure DDoS Protection temel tarafından korunmaktadır. Özelliği, ön kapı platformuna varsayılan olarak ve ek bir maliyet olmadan tümleştirilir. Ön kapı küresel olarak dağıtılan ağın tam ölçeği ve kapasitesi, her zaman açık trafik izleme ve gerçek zamanlı risk azaltma aracılığıyla ortak ağ katmanı saldırılarına karşı savunma sağlar. Temel DDoS koruması Ayrıca, genel uç noktaları hedefleyen en yaygın, sık görülen katman 7 DNS sorgu (sel ve katman 3 ve 4 Volumetric saldırılarına karşı savunma sergiler. Bu hizmette ayrıca Microsoft 'un kurumsal ve tüketici hizmetlerini büyük ölçekli saldırılara karşı koruyan kanıtlanmış bir izleme kaydı vardır. Daha fazla bilgi için bkz. [Azure DDoS koruması](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Protokol engelleme

Ön kapı yalnızca HTTP ve HTTPS protokollerinin trafiği kabul eder ve yalnızca bilinen bir üst bilgiyle geçerli istekleri işler `Host` . Bu davranış, bir dizi protokol ve bağlantı noktası, DNS yükseltme saldırısı ve TCP kirletme saldırılarına yayılan Volumetric saldırıları dahil bazı yaygın DDoS saldırı türlerini azaltmaya yardımcı olur.

## <a name="capacity-absorption"></a>Kapasite açıklaması

Ön kapı, genel olarak dağıtılmış, genel olarak dağıtılan bir hizmettir. Her saniyede yüzlerce binlerce istek alan Microsoft 'un büyük ölçekli bulut ürünleri dahil olmak üzere birçok müşteriniz vardır. Ön kapı, Azure 'un ağı, ansorbing ve coğrafi olarak büyük hacimli saldırıları birbirinden ayırarak bulunur. Bu, kötü amaçlı trafiğin Azure ağının kenarından daha fazla ilerlemesine engel olabilir.

## <a name="caching"></a>Önbelleğe Alma

[Ön kapıların önbelleğe alma özellikleri](concept-caching.md) , bir saldırı tarafından oluşturulan büyük trafik birimlerinden arka uçları korumak için kullanılabilir. Önbelleğe alınan kaynaklar, arka ucunuza iletilmemesi için ön kapı kenar düğümlerinden döndürülür. Dinamik yanıtlara yönelik kısa önbellek süre sonu süreleri (saniye veya dakika), arka uç hizmetlerindeki yükü büyük ölçüde azaltabilir. Kavramları ve desenleri önbelleğe alma hakkında daha fazla bilgi için bkz. [önbelleğe alma konuları](/azure/architecture/best-practices/caching) ve [önbelleğe alma düzeni](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>Web Uygulaması Güvenlik Duvarı (WAF)

[Ön kapı Web uygulaması güvenlik duvarı (WAF)](../../web-application-firewall/afds/afds-overview.md) , birçok farklı saldırı türünü azaltmak için kullanılabilir:

* Yönetilen kural kümesini kullanmak birçok yaygın saldırılara karşı koruma sağlar.
* Tanımlı bir coğrafi bölge veya tanımlı bölge içindeki trafik, Engellenen veya statik bir Web sayfasına yeniden yönlendirilebilir. Daha fazla bilgi için bkz. [coğrafi filtreleme](../../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* Kötü amaçlı olarak tanımlayabilen IP adresleri ve aralıkları engellenebilir.
* IP adreslerinin hizmetinizi çok sık aramasını engellemek için hız sınırlaması uygulanabilir.
* Bilinen imzaları olan HTTP veya HTTPS saldırılarını otomatik olarak engellemek ve derecelendirmek için [özel WAF kuralları](../../web-application-firewall/afds/waf-front-door-custom-rules.md) oluşturabilirsiniz.

## <a name="for-further-protection"></a>Daha fazla koruma için

Daha fazla korumaya ihtiyacınız varsa arka uçlarınızın dağıtıldığı VNet üzerinde [Azure DDoS koruma standardı](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) 'nı etkinleştirebilirsiniz. DDoS koruması standart müşterileri aşağıdakiler dahil daha fazla avantaj alır:

* Maliyet koruması
* SLA garantisi
* Bir saldırı sırasında anında yardım için DDoS hızlı yanıt ekibinden uzmanlara erişin.

## <a name="next-steps"></a>Sonraki adımlar

[Ön kapı Standart/Premium oluşturmayı](create-front-door-portal.md)öğrenin.
