---
title: Azure DDoS koruma standardı ile ortaklık
description: Azure DDoS koruma standardı tarafından etkinleştirilen ortaklık fırsatlarını anlayın.
ms.service: ddos-protection
documentationcenter: na
author: aletheatoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: 625f77a6d9dbeaf612df884434ab39b943587791
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103207"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Azure DDoS koruma standardı ile ortaklık
Bu makalede, Azure DDoS koruma standardı tarafından etkinleştirilen iş ortaklığı olanakları açıklanmaktadır. Bu makale, ürün yöneticilerinin ve iş geliştirme rollerinin yatırım yollarını anlamasına yardımcı olmak ve ortaklık değeri propositions hakkında Öngörüler sağlaması için tasarlanmıştır.

## <a name="background"></a>Arka Plan
Dağıtılmış hizmet reddi (DDoS) saldırıları, uygulamalarını buluta taşıyan müşterilere göre önemli kullanılabilirlik ve güvenlik sorunlarının biridir. DDoS saldırılarına maruz geçen yaygın savunma ve hacktivisk sayesinde, bu uygulamalar görece kolay ve yüksek EAP gibi tür, ölçek ve oluşma sıklığında sürekli olarak arttı.

Azure DDoS koruması, Azure ağ oluşturma küresel ölçeğinden yararlanarak en gelişmiş DDoS tehditlerine karşı önlemler sağlar. Hizmet, sanal ağlarda dağıtılan uygulamalar ve kaynaklar için gelişmiş DDoS azaltma özellikleri sağlar.

Teknoloji ortakları, DDoS saldırıları nedeniyle kullanılabilirlik ve güvenilirlik sorunlarını gidermek için müşterilerinin kaynaklarını Azure DDoS koruma standardı ile yerel olarak koruyabilir.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS koruması standardına giriş
Azure DDoS koruması standardı, katman 3 ve katman 4 DDoS saldırılarına karşı gelişmiş DDoS azaltma özellikleri sağlar. DDoS koruması standart hizmetinin önemli özellikleri aşağıda verilmiştir.

### <a name="adaptive-real-time-tuning"></a>Uyarlamalı gerçek zamanlı ayarlama
Her korumalı uygulama için, Azure DDoS koruma standardı, uygulamanın trafik profili desenlerine bağlı olarak DDoS Azaltma ilkesi eşiklerini otomatik olarak Tunus. Hizmet, bu özelleştirmeyi iki öngörü kullanarak gerçekleştirir:

- Katman 3 ve 4 için müşteri başına (IP başına) trafik desenlerini otomatik öğrenme.
- Azure 'un ölçeğinin önemli miktarda trafiğe artışlarını devralarak izin verdiğinden emin olmak için hatalı pozitif sonuçları en aza indirir.

![Uyarlamalı gerçek zamanlı ayarlama](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Saldırı Analizi, telemetri, izleme ve uyarı
Azure DDoS koruması, herhangi bir kullanıcı müdahalesi olmadan DDoS saldırılarını tanımlar ve azaltır.

- Korunan kaynak Azure Güvenlik Merkezi kapsamında yer alıyorsa, DDoS koruma standardı otomatik olarak güvenlik merkezi 'ne bir uyarı gönderir.
- Alternatif olarak, korumalı bir genel IP için etkin bir risk azaltma olduğunda bildirim almak için DDoS saldırısı kapsamındaki ölçüm üzerinde [bir uyarı yapılandırabilirsiniz](alerts.md) .
- Ayrıca, diğer DDoS ölçümleri için uyarı oluşturmayı ve saldırı ölçeğini, bırakılan trafiği, saldırı vektörlerini, en çok katılımcıları ve diğer ayrıntıları anlamak üzere [saldırı telemetrisini yapılandırmayı](telemetry.md) seçebilirsiniz.

![DDoS ölçümleri](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS hızlı yanıt (DRR)
DDoS koruması standart müşterilerinin etkin bir saldırı sırasında [hızlı yanıt takımına](ddos-rapid-response.md) erişimi vardır. DRR, saldırı sırasında saldırı sonrası analizler ve saldırı sırasında saldırı konusunda yardımcı olabilir.

### <a name="sla-guarantee-and-cost-protection"></a>SLA garantisi ve maliyet koruması
DDoS koruması standart hizmeti% 99,99 SLA kapsamında ve maliyet koruması belgelenmiş bir saldırı sırasında ölçeği genişletme için kaynak kredileri sağlar. Daha fazla bilgi için bkz. [Azure DDoS koruması Için SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Öne çıkan iş ortağı senaryoları
Aşağıda, Azure DDoS koruma standardı ile tümleştirerek türettiğiniz önemli avantajlar verilmiştir:

- İş ortaklarının müşterilerine sunulan hizmetler (yük dengeleyici, Web uygulaması güvenlik duvarı, güvenlik duvarı vb.) arka uçta Azure DDoS koruma standardı tarafından otomatik olarak korunur (beyaz etiketlidir).
- İş ortakları, Azure DDoS koruması standart saldırı analizlerine ve telemetrisine, birleştirilmiş bir müşteri deneyimi sunarak kendi ürünleriyle tümleştirebilecekleri bir erişime sahiptir.  
- İş ortaklarının, DDoS ile ilgili sorunlar için Azure hızlı yanıt yokluğunda bile DDoS hızlı yanıt desteğine erişimi vardır.
- İş ortaklarının korumalı uygulamaları, DDoS saldırıları nedeniyle DDoS SLA garantisi ve maliyet koruması tarafından desteklenir.

## <a name="technical-integration-overview"></a>Teknik tümleştirmeye genel bakış
Azure DDoS koruması Standart ortaklık fırsatları Azure portal, API 'Ler ve CLı/PS aracılığıyla kullanılabilir hale getirilir.

### <a name="integrate-with-ddos-protection-standard"></a>DDoS koruma standardı ile tümleştirme
İş ortaklarının Azure DDoS koruma standardı ile tümleştirmeyi yapılandırması için aşağıdaki adımlar gereklidir:
1. İstediğiniz (iş ortağı) aboneliğinizde bir DDoS koruma planı oluşturun. Adım adım yönergeler için bkz. [DDoS standart koruma planı oluşturma](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Belirli bir kiracı için yalnızca 1 DDoS koruma planının oluşturulması gerekir. 
2. Yük dengeleyici, güvenlik duvarları ve Web uygulaması güvenlik duvarı gibi (iş ortağı) aboneliklerinizde ortak uç nokta ile bir hizmet dağıtın. 
3. İlk adımda oluşturulan DDoS koruma planını kullanan ortak uç noktalara sahip hizmetin sanal ağında Azure DDoS koruma standardını etkinleştirin. Stpe adım yönergeler için bkz. [DDoS standart koruma planını etkinleştirme](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > Azure DDoS koruması standardı bir sanal ağ üzerinde etkinleştirildikten sonra, bu sanal ağ içindeki tüm genel IP 'Ler otomatik olarak korunur. Bu genel IP 'lerin kaynağı Azure 'da (istemci aboneliği) veya Azure dışında olabilir. 
4. İsteğe bağlı olarak, uygulamaya özgü müşterilere yönelik panonuzda Azure DDoS koruması standart telemetri ve saldırı analizlerini tümleştirin. Telemetriyi kullanma hakkında daha fazla bilgi için bkz. [DDoS koruması telemetrisini görüntüleme ve yapılandırma](telemetry.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Ekleme kılavuzlarını ve teknik belgeler

- [Azure DDoS koruması ürün sayfası](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS koruması belgeleri](ddos-protection-overview.md)
- [Azure DDoS koruması API başvurusu](/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure sanal ağ API başvurusu](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Yardım alın

- Azure DDoS koruma standardı ile uygulama, hizmet veya ürün tümleştirmeleri hakkında sorularınız varsa, [Azure Güvenlik topluluğuna](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)ulaşın.
- [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/)tartışmaları izleyin.

### <a name="get-to-market"></a>Pazara al

- Microsoft ile ortaklık için birincil program [Microsoft iş ortağı ağı](https://partner.microsoft.com/). – Microsoft Graph güvenlik tümleştirmeleri [MPN bağımsız yazılım satıcısı (ISV)](https://partner.microsoft.com/saas-solution-guide) izlemesine girer.
- Microsoft [akıllı güvenlik ilişkilendirmesi](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) , Microsoft güvenlik iş ortaklarının özel olarak, güvenlik ürünlerinizi zenginleştirilmesine yardımcı olmak ve Microsoft güvenlik ürünleriyle tümleştirmelerinizin müşteri bulunabilirliği için sunduğu programdır.

## <a name="next-steps"></a>Sonraki adımlar
Mevcut iş ortağı tümleştirmelerini görüntüleyin:

- [Barçcuda WAF-hizmet olarak](https://www.barracuda.com/waf-as-a-service)
- [Radware 'den Azure bulutu WAF](https://www.radware.com/resources/microsoft-azure/)
