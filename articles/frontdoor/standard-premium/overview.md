---
title: Azure ön kapısının Standart/Premium | Microsoft Docs
description: Bu makalede, Azure ön kapısının Standart/Premium 'a genel bakış sunulmaktadır.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: overview
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 5c3ae5c7b1c45d170548f6fa00481094117e1737
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937371"
---
# <a name="what-is-azure-front-door-standardpremium-preview"></a>Azure ön kapısının Standart/Premium nedir (Önizleme)?

> [!IMPORTANT]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Azure ön kapı belgelerini](../front-door-overview.md)görüntüleyin.

Azure ön kapı Standart/Premium, Microsoft Global Edge ağını kullanan ve akıllı tehdit koruması ile tümleşen hızlı, güvenilir ve güvenli bir modern bulut CDN 'dir. Azure ön kapısı, Azure Content Delivery Network (CDN) standardı ve Azure Web uygulaması güvenlik duvarı (WAF) yeteneklerini tek bir güvenli bulut CDN platformunda birleştirir.

Azure ön kapısının Standart/Premium sayesinde, genel tüketicisini ve kurumsal uygulamalarınızı, ağ ucunda kullanıcıya yakın olan küresel bir hedef kitleye ulaşan içerikle güvenli ve yüksek performanslı, kişiselleştirilmiş modern uygulamalarla dönüştürebilirsiniz. Ayrıca, anlık yük devretmeyle genel HTTP yük dengelemesinden faydalanmasını sırasında, uygulamanızın ısınma olmadan ölçeği ölçeklendirmesini sağlar.

   :::image type="content" source="../media/overview/front-door-overview.png" alt-text="Azure ön kapısının Standart/Premium mimarisi" lightbox="../media/overview/front-door-overview-expanded.png":::

Azure ön kapı Standart/Premium, genel bağlantıyı geliştirmek için bölünmüş TCP ve Microsoft 'un küresel ağı ile her noktaya yayın protokolünü kullanarak katman 7 ' de (HTTP/HTTPS katmanı) çalışmaktadır. Kurallar kümesi kullanılarak özelleştirilmiş yönlendirme yönteminiz temelinde, Azure ön kapısının istemci isteklerinizi en hızlı ve en yüksek kaynağa yönlendirdiğinden emin olabilirsiniz. Uygulama kaynağı, Azure içinde veya dışında barındırılan Internet 'e yönelik herhangi bir hizmettir. Azure ön kapı Standart/Premium, farklı uygulama ihtiyaçlarına ve otomatik yük devretme senaryolarına uyacak şekilde çeşitli trafik yönlendirme yöntemleri ve kaynak sistem durumu izleme seçenekleri sağlar. Traffic Manager benzer şekilde, ön kapı, Azure bölgesinin tamamına yönelik hatalarla birlikte hatalara karşı dayanıklıdır.

Azure ön kapısının yanı sıra tümleşik Web uygulaması güvenlik duvarı koruması, Bot koruması ve yerleşik katman 3/katman 4 dağıtılmış hizmet reddi (DDoS) koruması ile uygulamanızı koruyabilirsiniz. Özel arka uçlarınızın özel bağlantı hizmetiyle de güvenliğini sağlar. Azure ön kapısının küresel ölçekte Microsoft 'un en iyi yöntem güvenliğini sağlar.  

>[!NOTE]
> Azure, senaryolarınız için tam olarak yönetilen yük dengeleme çözümleri sunar.
>
> * DNS tabanlı küresel yönlendirme yapmak istiyorsanız ve Aktarım Katmanı Güvenliği (TLS) protokolü sonlandırma ("SSL yük boşaltma"), HTTP/HTTPS isteği veya uygulama katmanı işleme gereksinimlerine sahip **değilseniz** [Traffic Manager](../../traffic-manager/traffic-manager-overview.md)gözden geçirin.
> * Uygulama katmanındaki bir bölgedeki sunucularınız arasında yük dengelemesi yapmak istiyorsanız, [Application Gateway](../../application-gateway/overview.md) gözden geçirin
> * Ağ katmanı yük dengelemesi yapmak için [Load Balancer](../../load-balancer/load-balancer-overview.md)gözden geçirin.
>
> Uçtan uca senaryolarınız, bu çözümlerin gerektiğinde birleştirilmesinin avantajlarından yararlanabilir.
> Azure yük dengeleme seçenekleri karşılaştırması için bkz. [Azure 'da Yük Dengeleme seçeneklerine genel bakış](/azure/architecture/guide/technology-choices/load-balancing-overview).

> [!IMPORTANT]
> Azure ön kapısının Standart/Premium Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-azure-front-door-standardpremium-preview"></a>Azure ön kapısı Standart/Premium (Önizleme) neden kullanılmalıdır?

Azure ön kapısının Standart/Premium, yerleşik anahtar güvenlik tümleştirmesiyle hem dinamik hem de statik hızlandırmaya ve basit ve öngörülebilir bir fiyatlandırma modeline sahip olan tek bir Birleşik platform sağlar. Ön kapı aynı zamanda uygulamanız için küresel yönlendirmeyi tanımlamanıza, yönetmenize ve izlemenize olanak sağlar.

Azure ön kapısı Standart/Premium (Önizleme) ile birlikte gelen temel özellikler:

- **[Bölünmüş TCP tabanlı](../front-door-routing-architecture.md#splittcp)** her noktaya yayın protokolünü kullanarak hızlandırılmış uygulama performansı.

- Kaynaklar **[arasında akıllı](concept-origin.md)** **[durum araştırma](concept-health-probes.md)** izleme ve yük dengeleme.

- Esnek etki alanı doğrulama ile kendi **[özel etki](how-to-add-custom-domain.md)** alanınızı tanımlayın.

- Tümleşik **[Web uygulaması güvenlik duvarı (WAF)](../../web-application-firewall/afds/afds-overview.md)** ile uygulama güvenliği.

- SSL yük boşaltma ve tümleşik **[sertifika yönetimi](how-to-configure-https-custom-domain.md)**.

- **[Özel bağlantıyla](concept-private-link.md)** kaynakları güvenli hale getirin.  

- **[Kural kümesi](concept-rule-set.md)** aracılığıyla özelleştirilebilir trafik yönlendirme ve iyileştirmeler.

- Hem ön kapı hem de güvenlik desenleri için hepsi bir arada Pano içeren **[yerleşik raporlar](how-to-reports.md)** .

- Azure Izleme ile tümleştirilen **[gerçek zamanlı izleme](how-to-monitor-metrics.md)** ve uyarılar.

- Her bir ön kapı isteği ve başarısız durum **[araştırmalarının günlüğü](how-to-logs.md)** .

- Uçtan uca IPv6 bağlantısı ve HTTP/2 protokolünün yerel desteği.

## <a name="pricing"></a>Fiyatlandırma

Azure ön kapısının Standart/Premium iki SKU 'su, standart ve Premium. Bkz. [Katman karşılaştırması](tier-comparison.md). Fiyatlandırma bilgileri için bkz. [Front Door Fiyatlandırması](https://azure.microsoft.com/pricing/details/frontdoor/). 

## <a name="whats-new"></a>Yenilikler

RSS akışına abone olun ve [Azure Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) sayfasında en son Azure ön kapılı Özellik güncelleştirmelerini görüntüleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Front Door oluşturmayı](create-front-door-portal.md) öğrenin.
