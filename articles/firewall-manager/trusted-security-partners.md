---
title: Azure Güvenlik Duvarı Yöneticisi güvenilen güvenlik iş ortakları (Önizleme) nedir?
description: Azure Güvenlik Duvarı Yöneticisi güvenilen güvenlik iş ortakları hakkında bilgi edinin
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: cb8a2fdd14cfa7d361e3d78a64f3aaf60ea7676d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468573"
---
# <a name="what-are-trusted-security-partners-preview"></a>Güvenilen güvenlik iş ortakları (Önizleme) nedir?

> [!IMPORTANT]
> Bu genel önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Güvenlik Duvarı Yöneticisi 'nde *güvenilir güvenlik iş ortakları (Önizleme)* , kullanıcılarınız için Internet erişimini korumak üzere tanıdık, en uygun, yerleşik, üçüncü taraf güvenlik hizmeti (SECaaS) tekliflerini kullanmanıza olanak sağlar.

Hızlı yapılandırmayla, desteklenen bir güvenlik ortağıyla bir hub 'ı güvenli hale getirebilirsiniz ve Internet trafiğini sanal ağlarınızdan (VNet) veya bir bölgedeki dal konumlarından yönlendirebilir ve filtreleyebilirsiniz. Bu işlem, Kullanıcı tanımlı yollar (UDRs) ayarlamadan ve yönetilmeden otomatik rota Yönetimi kullanılarak yapılır.

Kullanıcılarınızın bu bölgelerdeki dünya genelinde her yerde bağlantı ve güvenlik sağlamak için birden fazla Azure bölgesinde tercih ettiğiniz güvenlik ortağıyla yapılandırılmış güvenli hub 'ları dağıtabilirsiniz. Güvenlik iş ortağının Internet/SaaS uygulama trafiği için teklifini ve güvenli hub 'larda özel trafik için Azure Güvenlik duvarını kullanma olanağı sayesinde, artık küresel olarak dağıtılan kullanıcılarınıza yakın olan Azure 'da güvenlik ucunu oluşturmaya başlayabilirsiniz ve uygulamaları.

Bu önizleme için, desteklenen güvenlik iş ortakları **Zscaler** ve **ıpatron**' dir. Desteklenen bölgeler şunlardır WestCentralUS, kuzeydoğu, WestUS, WestUS2 ve EastUS.

![Güvenilen güvenlik iş ortakları](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Önemli senaryolar

Aşağıdaki senaryolarda Internet trafiğini filtrelemek için güvenlik iş ortaklarını kullanabilirsiniz:

- Sanal ağ (VNet) ile Internet arasında

   Azure üzerinde çalışan bulut iş yükleriniz için Gelişmiş Kullanıcı tarafından uyumlu Internet korumasından yararlanın.

- Internet 'e dallan

   Şube için üçüncü taraf NSaaS filtrelemesini kolayca Internet senaryolarına eklemek için Azure bağlantınız ve küresel dağıtım özelliğinden yararlanın. Azure sanal WAN kullanarak küresel transit ağınızı ve güvenlik ucunu oluşturabilirsiniz.

Aşağıdaki senaryolar desteklenir:
-   Üçüncü taraf iş ortağı sunumu aracılığıyla VNet 'Ten Internet 'e.
-   Üçüncü taraf iş ortağı sunumu aracılığıyla Internet 'e dallan.
-   Bir üçüncü taraf iş ortağı teklifi aracılığıyla Internet 'e dallan, Özel trafiğin geri kalanı (bağlı olan tarafa bağlı bileşen, bağlı bileşen, Dalla dallar) Azure Güvenlik Duvarı aracılığıyla.

Aşağıdaki senaryo desteklenmez:

- Bir iş ortağı sunumu aracılığıyla Internet 'e VNet, özel trafik için Azure Güvenlik Duvarı ile birleştirilemez. Aşağıdaki sınırlamalara bakın.

## <a name="current-limitations"></a>Geçerli sınırlamalar

- VNet 'Ten Internet 'e yönelik olarak, özel trafik için Azure Güvenlik Duvarı ve Internet trafiği için bir iş ortağı teklifi ekleme karıştıramazsınız. Güvenli sanal hub 'da Azure Güvenlik Duvarı 'na veya bir üçüncü taraf güvenlik iş ortağı teklifine Internet trafiği gönderebilirsiniz, ancak ikisini birden kullanamazsınız. 
- Her sanal hub için en çok bir güvenlik ortağı dağıtabilirsiniz. Sağlayıcıyı değiştirmeniz gerekiyorsa, mevcut ortağı kaldırmalı ve yeni bir tane eklemeniz gerekir.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Güvenli sanal hub 'larda Internet trafiği Filtreleme için en iyi uygulamalar

Internet trafiği genellikle Web trafiğini içerir. Ayrıca, Office 365 (O365) gibi SaaS uygulamalarına ve Azure depolama, Azure SQL gibi Azure genel PaaS hizmetlerine giden trafiği de içerir. Aşağıda, bu hizmetlere giden trafiği işlemeye yönelik en iyi yöntem önerileri verilmiştir:

### <a name="handling-azure-paas-traffic"></a>Azure PaaS trafiğini işleme
 
- Trafiğiniz çoğunlukla Azure PaaS içeriyorsa ve uygulamalarınıza yönelik kaynak erişiminin IP adresleri, FQDN 'Ler, hizmet etiketleri veya FQDN etiketleri kullanılarak filtrelenebilir olması için Azure Güvenlik Duvarı 'Nı koruma için kullanın.

- Ortamınızdaki bir üçüncü taraf iş ortağı çözümünü, trafiğiniz SaaS uygulama erişimi 'nden oluşuyorsa veya Kullanıcı duyarlı filtrelemeye (örneğin, sanal masaüstü altyapısı (VDı) iş yükleriniz için) veya gelişmiş Internet filtreleme özelliklerine ihtiyacınız varsa kullanın.

![Azure Güvenlik Duvarı Yöneticisi için tüm senaryolar](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Office 365 (O365) trafiğini işleme

Küresel olarak dağıtılan dal konumu senaryolarında, Azure güvenli hub 'ınıza kalan Internet trafiğini göndermeden önce Office 365 trafiğini doğrudan dalda yeniden yönlendirmelisiniz.

Office 365 için, başarılı bir kullanıcı deneyimi için ağ gecikme süresi ve performansı kritik öneme sahiptir. En iyi performans ve Kullanıcı deneyiminden bu hedeflere ulaşmak için müşteriler, Internet trafiğinin geri kalanını Azure üzerinden yönlendirmeyi düşünmeden önce Office 365 Direct ve yerel kaçış işlemlerini gerçekleştirmelidir.

[Office 365 ağ bağlantısı ilkeleri](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) , anahtar Office 365 ağ bağlantılarının Kullanıcı dalından veya mobil cihazdan yerel olarak yönlendirilmesi için çağrı ve doğrudan Internet üzerinden Microsoft 'un sahip olduğu en yakın Microsoft ağ iletişim noktası.

Ayrıca, Office 365 bağlantılarının gizlilik açısından güçlü şekilde şifrelenmesi ve performans nedenleriyle verimli, özel protokoller kullanılması gerekir. Bu, bu bağlantıları geleneksel ağ düzeyi güvenlik çözümlerine tabi hale getirir. Bu nedenlerden dolayı, müşterilerin Azure üzerinden trafiği geri göndermeden önce doğrudan dallardan Office 365 trafiği göndermesini öneririz. Microsoft, Azure ve Office 365 ile tümleşen birçok SD-WAN çözümü sağlayıcısıyla işbirliği yaptı ve müşterilerin Office 365 Direct ve yerel Internet kırılımı etkinleştirmesini kolaylaştırır. Ayrıntılar için bkz. [sanal WAN aracılığıyla O365 ilkelermi ayarlamak nasıl yaparım??](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview#how-do-i-set-my-o365-policies-via-virtual-wan)


## <a name="next-steps"></a>Sonraki adımlar

[Azure Güvenlik Duvarı Yöneticisi 'ni kullanarak güvenli bir hub 'da güvenilir bir güvenlik teklifi dağıtın](deploy-trusted-security-partner.md).
