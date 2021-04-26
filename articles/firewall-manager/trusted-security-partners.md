---
title: Azure Güvenlik Duvarı Yöneticisi güvenlik iş ortağı sağlayıcıları nelerdir?
description: Azure Güvenlik Duvarı Yöneticisi güvenlik iş ortağı sağlayıcıları hakkında bilgi edinin
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: victorh
ms.openlocfilehash: 622fde49a31105b2f66a678d3e55d48fabea9487
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105966920"
---
# <a name="what-are-security-partner-providers"></a>Güvenlik iş ortağı sağlayıcıları nedir?

Azure Güvenlik Duvarı Yöneticisi 'ndeki *güvenlik iş ortağı sağlayıcıları* , kullanıcılarınız için Internet erişimini korumak üzere tanıdık, en uygun, yerleşik, üçüncü taraf güvenlik hizmeti (SECaaS) tekliflerini kullanmanıza olanak sağlar.

Hızlı yapılandırmayla, desteklenen bir güvenlik ortağıyla bir hub 'ı güvenli hale getirebilirsiniz ve Internet trafiğini sanal ağlarınızdan (VNet) veya bir bölgedeki dal konumlarından yönlendirebilir ve filtreleyebilirsiniz. Bunu otomatik yönlendirme yönetimi ile, Kullanıcı tanımlı yollar (UDRs) ayarlamadan ve yönetmeden yapabilirsiniz.

Kullanıcılarınızın bu bölgelerdeki dünya genelinde her yerde bağlantı ve güvenlik sağlamak için birden fazla Azure bölgesinde tercih ettiğiniz güvenlik ortağıyla yapılandırılmış güvenli hub 'ları dağıtabilirsiniz. Güvenlik iş ortağının Internet/SaaS uygulama trafiği ve güvenli hub 'larda özel trafik için Azure Güvenlik Duvarı tekliflerini kullanma olanağı sayesinde, artık Azure 'da, genel olarak dağıtılmış kullanıcılarınıza ve uygulamalarınıza yakın olan güvenlik ucunu oluşturmaya başlayabilirsiniz.

Desteklenen güvenlik iş ortakları **Zscaler**, **[Check Point](check-point-overview.md)** ve **ıpatron**.

![Güvenlik iş ortağı sağlayıcıları](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Önemli senaryolar

Aşağıdaki senaryolarda Internet trafiğini filtrelemek için güvenlik iş ortaklarını kullanabilirsiniz:

- Sanal ağ (VNet)-Internet

   Azure üzerinde çalışan bulut iş yükleriniz için Gelişmiş Kullanıcı tarafından uyumlu Internet koruması kullanın.

- Daldan Internet

   Şube için üçüncü taraf NSaaS filtrelemesini Internet senaryolarına kolayca eklemek için Azure bağlantınızı ve küresel dağıtımınızı kullanın. Azure sanal WAN kullanarak küresel transit ağınızı ve güvenlik ucunu oluşturabilirsiniz.

Aşağıdaki senaryolar desteklenir:
- Hub 'da iki güvenlik sağlayıcısı

   Azure Güvenlik Duvarı aracılığıyla bir güvenlik iş ortağı sağlayıcısı ve diğer trafik (bağlı ağa bağlı, bağlı bileşen, Dalla Şube) aracılığıyla VNet/dalı-Internet.
- Hub 'da tek sağlayıcı

   - Azure Güvenlik Duvarı tarafından güvenli hale getirilen tüm trafik (bağlı bileşen, bağlı bileşen, şube ve sanal ağ, sanal ağ, VNet-Internet)<br>
      veya
   - Güvenlik iş ortağı sağlayıcısı aracılığıyla VNet/şube-Internet

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Güvenli sanal hub 'larda Internet trafiği Filtreleme için en iyi uygulamalar

Internet trafiği genellikle Web trafiğini içerir. Ayrıca, Azure depolama, Azure SQL gibi Microsoft 365 ve Azure genel PaaS hizmetleri gibi SaaS uygulamalarına giden trafiği de içerir. Aşağıda, bu hizmetlere giden trafiği işlemeye yönelik en iyi yöntem önerileri verilmiştir:

### <a name="handling-azure-paas-traffic"></a>Azure PaaS trafiğini işleme
 
- Trafiğiniz çoğunlukla Azure PaaS içeriyorsa ve uygulamalarınıza yönelik kaynak erişiminin IP adresleri, FQDN 'Ler, hizmet etiketleri veya FQDN etiketleri kullanılarak filtrelenebilir olması için Azure Güvenlik Duvarı 'Nı koruma için kullanın.

- Ortamınızdaki bir üçüncü taraf iş ortağı çözümünü, trafiğiniz SaaS uygulama erişimi 'nden oluşuyorsa veya Kullanıcı duyarlı filtrelemeye (örneğin, sanal masaüstü altyapısı (VDı) iş yükleriniz için) veya gelişmiş Internet filtreleme özelliklerine ihtiyacınız varsa kullanın.

![Azure Güvenlik Duvarı Yöneticisi için tüm senaryolar](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Microsoft 365 trafiği işleme

Küresel olarak dağıtılan şube konumu senaryolarında, Azure güvenli hub 'ınıza kalan Internet trafiğini göndermeden önce Microsoft 365 trafiği doğrudan dalda yeniden yönlendirmelisiniz.

Microsoft 365 için ağ gecikmesi ve performans, başarılı bir kullanıcı deneyimi için kritiktir. En iyi performans ve Kullanıcı deneyiminden bu hedeflere ulaşmak için müşterilerin Internet trafiğini Azure üzerinden yönlendirmeyi düşünmeden önce Microsoft 365 doğrudan ve yerel kaçış uygulaması gerekir.

[Ağ bağlantısı ilkeleri](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) , anahtar Microsoft 365 ağ bağlantıları için Kullanıcı dalından veya mobil cihazdan yerel olarak yönlendirilir ve doğrudan Internet üzerinden Microsoft 'un sahip olduğu en yakın Microsoft ağ iletişim noktasına yönlendirilir. Microsoft 365

Ayrıca, Microsoft 365 bağlantılar Gizlilik için şifrelenir ve performans nedenleriyle verimli, özel protokoller kullanır. Bu, bu bağlantıları geleneksel ağ düzeyi güvenlik çözümlerine tabi hale getirir. Bu nedenlerden dolayı, müşterilerin, trafiği Azure üzerinden göndermeden önce doğrudan dallardan Microsoft 365 trafiği göndermesini önemle öneririz. Microsoft, Azure ve Microsoft 365 tümleşen birçok SD-WAN çözüm sağlayıcısıyla işbirliği yaptı ve müşterilerin doğrudan ve yerel Internet kırılımı Microsoft 365 etkinleştirmesini kolaylaştırır. Ayrıntılar için bkz. [Azure sanal WAN nedir?](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>Sonraki adımlar

[Azure Güvenlik Duvarı Yöneticisi 'ni kullanarak güvenli bir hub 'da güvenlik ortağı teklifi dağıtın](deploy-trusted-security-partner.md).
