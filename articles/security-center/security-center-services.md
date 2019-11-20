---
title: Azure Güvenlik Merkezi 'nde sunulan desteklenen özellikler | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi tarafından desteklenen hizmetlerin bir listesini sağlar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 275598aa50c252512348f4a04543e1beaf538626
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529491"
---
# <a name="supported-features-available-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde sunulan desteklenen özellikler

> [!NOTE]
>Bazı özellikler yalnızca Standart katmanda kullanılabilir. Güvenlik Merkezi 'nin standart katmanına kaydolmadıysanız ücretsiz bir deneme süresi vardır. Daha fazla bilgi için [Güvenlik Merkezi fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/security-center/)bakın.

Aşağıdaki bölümlerde, [desteklenen platformları](security-center-os-coverage.md)Için kullanılabilen güvenlik merkezi özellikleri gösterilmektedir.

* [Sanal makineler/sunucular](#vm-server-features)
* [PaaS hizmetleri](#paas-services)


## Sanal makine/sunucu tarafından desteklenen özellikler<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

|Sunucu|Windows|||Linux|||Fiyatlandırma|
|----|----|----|----|----|----|----|----|
|**Ortam**|**Azure**||**Azure dışı**|**Azure**||**Azure dışı**||
||**Sanal Makine**|**Sanal makine ölçek kümesi**||**Sanal Makine**|**Sanal makine ölçek kümesi**|
|[Microsoft Defender ATP tümleştirmesi](https://docs.microsoft.com/azure/security-center/security-center-wdatp)|✔ (desteklenen sürümlerde)|✔ (desteklenen sürümlerde)|✔|X|X|X|Standart|
|[Sanal makine davranış analizi tehdit algılama uyarıları](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas)|✔|✔|✔|✔ (desteklenen sürümlerde)|✔ (desteklenen sürümlerde)|✔|Öneriler (ücretsiz) tehdit algılama (Standart)|
|[Dosya daha az tehdit algılama uyarıları](https://docs.microsoft.com/azure/security-center/security-center-alerts-iaas#fileless-attack-detection-)|✔|✔|✔|X|X|X|Standart|
|[Ağ tabanlı tehdit algılama uyarıları](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer#azure-network-layer)|✔|✔|X|✔|✔|X|Standart|
|[Tam zamanında VM erişimi](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|✔|X|X|✔|X|X|Standart|
|[Dosya bütünlüğünü Izleme](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)|✔|✔|✔|✔|✔|✔|Standart|
|[Uyarlamalı uygulama denetimleri](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)|✔|X|✔|✔|X|✔|Standart|
|[Ağ eşlemesi](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations#network-map)|✔|✔|X|✔|✔|X|Standart|
|[Uyarlamalı ağ sağlamlaştırma](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)|✔|X|X|✔|X|X|Standart|
|Uyarlamalı ağ denetimleri|✔|✔|X|✔|✔|X|Standart|
|[Mevzuat uyumluluk panosu & raporları](https://docs.microsoft.com/azure/security-center/security-center-compliance-dashboard)|✔|✔|✔|✔|✔|✔|Standart|
|Docker tarafından barındırılan IaaS kapsayıcılarında öneriler ve tehdit algılama|X|X|X|✔|✔|✔|Standart|
|Eksik işletim sistemi düzeltme ekleri değerlendirmesi|✔|✔|✔|✔|✔|✔|Ücretsiz|
|Güvenlik yapılandırması hataları değerlendirmesi|✔|✔|✔|✔|✔|✔|Ücretsiz|
|[Endpoint Protection değerlendirmesi](https://docs.microsoft.com/azure/security-center/security-center-services#supported-endpoint-protection-solutions-)|✔|✔|✔|X|X|X|Ücretsiz|
|Disk şifrelemesi değerlendirmesi|✔|✔|X|✔|✔|X|Ücretsiz|
|Üçüncü taraf güvenlik açığı değerlendirmesi|✔|X|X|✔|X|X|Ücretsiz|
|[Ağ güvenlik değerlendirmesi](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)|✔|✔|X|✔|✔|X|Ücretsiz|

### Desteklenen Endpoint Protection çözümleri<a name="endpoint-supported"></a>

Aşağıdaki tabloda bir matrisi verilmiştir:

 - Her çözümü sizin için yüklemek üzere Azure Güvenlik Merkezi 'ni kullanıp kullanmayacağınızı belirtir.
 - Güvenlik Merkezi 'nin hangi Endpoint Protection çözümlerini keşfedebileceği. Bu listeden bir uç nokta koruma çözümü bulunursa, güvenlik merkezi bir tane yüklemeyi önermez.

Bu korumaların her biri için önerilerin ne zaman oluşturulduğu hakkında bilgi için bkz. [Endpoint Protection değerlendirme ve öneriler](security-center-endpoint-protection.md).

| Endpoint Protection| Platformlar | Güvenlik Merkezi Yüklemesi | Güvenlik Merkezi Bulma |
|------|------|-----|-----|
| Windows Defender (Microsoft Kötü Amaçlı Yazılım Koruması)| Windows Server 2016| Hayır, işletim sisteminde yerleşik| Yes |
| System Center Endpoint Protection (Microsoft Kötü Amaçlı Yazılım Koruması) | Windows Server 2012 R2, 2012, 2008 R2 (aşağıdaki nota bakın) | Uzantı ile | Yes |
| Trend Micro – tüm sürümler * | Windows Server Ailesi  | Hayır | Yes |
| Symantec v12.1.1100+| Windows Server Ailesi  | Hayır | Yes |
| McAfee v10+ | Windows Server Ailesi  | Hayır | Yes |
| McAfee v10+ | Linux sunucu ailesi  | Hayır | Evet **\*** |
| Sophos v9 +| Linux sunucu ailesi  | Hayır | Evet **\***  |

 **\*** Kapsam durumu ve destekleyici veriler şu anda yalnızca korunan aboneliklerinizle ilişkili olan Log Analytics çalışma alanında kullanılabilir. Azure Güvenlik Merkezi portalında yansıtılmaz.

> [!NOTE]
>
> - Windows Server 2008 R2 sanal makinesi üzerinde System Center Endpoint Protection (SCEP) algılama, PowerShell 3,0 (veya bir üst sürüm) sonrasında SCEP 'in yüklenmesini gerektirir.
> - Derinlemesine güvenlik aracıları için Trend mikro koruma algılama desteklenir.  OfficeScan aracıları desteklenmez.


## PaaS hizmetleri desteklenen özellikler <a name="paas-services"></a>

Aşağıdaki PaaS kaynakları Azure Güvenlik Merkezi tarafından desteklenir:

|Hizmet|Öneriler (ücretsiz)|Tehdit algılama (Standart)|
|----|----|----|
|SQL|✔| ✔|
|PostgreSQL|✔| ✔|
|MySQL|✔| ✔|
|CosmosDB|X| ✔|
|Blob depolaması|✔| ✔|
|Depolama hesabı|✔| X|
|App Service|✔| ✔|
|İşlev|✔| X|
|Bulut Hizmeti|✔| X|
|Sanal ağ|✔| X|
|Alt ağ|✔| X|
|NIC|✔| X|
|NSG|✔| X|
|Abonelik|✔ * *| ✔|
|Batch hesabı|✔| X|
|Service Fabric hesabı|✔| X|
|Otomasyon hesabı|✔| X|
|Yük dengeleyici|✔| X|
|Arama|✔| X|
|Service Bus ad alanı|✔| X|
|Stream Analytics|✔| X|
|Olay hub’ı ad alanı|✔| X|
|Mantıksal uygulamalar|✔| X|
|Redis|✔| X|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Key vault|✔| X|

\* Bu özellikler Şu anda genel önizlemede destekleniyor.

\* \* Azure Active Directory (Azure AD) önerileri yalnızca standart abonelikler için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Güvenlik Merkezi 'nin verileri ve Log Analytics aracısını nasıl topladığı](security-center-enable-data-collection.md)hakkında bilgi edinin.
- [Güvenlik Merkezi 'nin verileri nasıl yönettiğini ve korumalarını](security-center-data-security.md)öğrenin.
- [Azure Güvenlik Merkezi 'ni benimsemek için tasarım konularını nasıl planlayacağınızı ve anlayacağınızı](security-center-planning-and-operations-guide.md)öğrenin.
- [Güvenlik Merkezi 'ni destekleyen platformları](security-center-os-coverage.md)gözden geçirin.
- [Azure Güvenlik Merkezi 'Nde VM 'ler & sunucular için tehdit algılama](security-center-alerts-iaas.md)hakkında daha fazla bilgi edinin.
- [Azure Güvenlik Merkezi 'ni kullanma hakkında sık sorulan soruları](security-center-faq.md)bulun.
- [Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini](https://blogs.msdn.com/b/azuresecurity/)bulun.
