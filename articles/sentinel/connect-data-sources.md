---
title: Veri kaynaklarını Azure Sentinel 'e bağlama | Microsoft Docs
description: Veri kaynaklarını Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: rkarlin
manager: angrobe
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: rkarlin
ms.openlocfilehash: 4b21d6aa95a38df402cf7a2640467c7a060a7f49
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496379"
---
# <a name="connect-data-sources"></a>Veri kaynaklarını bağlama

Azure Sentinel 'e kadar, önce veri kaynaklarınıza bağlanmanız gerekir. Azure Sentinel, Microsoft çözümleri için çok sayıda bağlayıcı sunar ve Microsoft tehdit koruması çözümleri dahil gerçek zamanlı tümleştirme ve Office 365, Azure AD, Azure ATP ve dahil olmak üzere Microsoft 365 kaynakları sağlar. Microsoft Cloud App Security ve daha fazlası. Ayrıca, Microsoft dışı çözümler için daha geniş güvenlik ekosistemine yerleşik bağlayıcılar vardır. Ayrıca, veri kaynaklarınızı Azure Sentinel 'e bağlamak için ortak olay biçimi, syslog veya REST API de kullanabilirsiniz.  

1. Menüsünde, **veri bağlayıcıları**' nı seçin. Bu sayfa, Azure Sentinel 'in sağladığı bağlayıcıların tam listesini ve bunların durumlarını görmenizi sağlar. Bağlanmak istediğiniz bağlayıcıyı seçin ve **bağlayıcı sayfasını aç**' ı seçin. 

   ![Veri toplayıcılar](./media/collect-data/collect-data-page.png)

1. Belirli bağlayıcı sayfasında, tüm önkoşulları karşıladığınızdan emin olun ve verileri Azure Sentinel 'e bağlamak için yönergeleri izleyin. Günlüklerin Azure Sentinel ile eşitlenmeye başlaması biraz zaman alabilir. Bağlandıktan sonra, **alınan veri** grafiğindeki verilerin özetini ve veri türlerinin bağlantı durumunu görürsünüz.

   ![Toplayıcıların bağlantısını yapın](./media/collect-data/opened-connector-page.png)
  
1. Belirli veri türü için Azure Sentinel 'in sağladığı kullanıma hazır içeriğin bir listesini almak için **sonraki adımlar** sekmesine tıklayın.

   ![Veri toplayıcılar](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Veri bağlantısı yöntemleri

Aşağıdaki veri bağlantısı yöntemleri Azure Sentinel tarafından desteklenir:

- **Microsoft Hizmetleri**:<br> Microsoft Hizmetleri yerel olarak bağlanır ve kullanıma hazır tümleştirme için Azure Foundation 'ı kullanarak, aşağıdaki çözümler birkaç tıklamayla bağlanabilir:
    - [Office 365](connect-office-365.md)
    - [Azure AD denetim günlükleri ve oturum açmalar](connect-azure-active-directory.md)
    - [Azure etkinliği](connect-azure-activity.md)
    - [Azure AD Kimlik Koruması](connect-azure-ad-Identity-protection.md)
    - [Azure Güvenlik Merkezi](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Gelişmiş tehdit koruması](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Windows güvenlik olayları](connect-windows-security-events.md) 
    - [Windows Güvenlik Duvarı](connect-windows-firewall.md)

- **API aracılığıyla dış çözümler**: bazı veri kaynakları, bağlı veri kaynağı tarafından sağlanmış API 'ler kullanılarak bağlanır. Genellikle, çoğu güvenlik teknolojisi, olay günlüklerinin alınabileceği bir API kümesi sağlar. API 'Ler Azure Sentinel 'e bağlanır ve belirli veri türlerini toplayıp Azure Log Analytics 'e gönderir. API aracılığıyla bağlı olan gereçler şunları içerir:
    - [Barracuda](connect-barracuda.md)
    - ['E](connect-symantec.md)
    - [Citrix Analytics (güvenlik)](connect-citrix-analytics.md)

- **Aracı aracılığıyla dış çözümler**: Azure Sentinel, bir aracı aracılığıyla Syslog protokolünü kullanarak gerçek zamanlı günlük akışı gerçekleştirebilen diğer tüm veri kaynaklarına bağlanabilir. <br>Çoğu gereçleri, günlüğü kapsayan olay iletilerini ve günlük bilgilerini içeren olay iletilerini göndermek için Syslog protokolünü kullanır. Günlüklerin biçimi değişir, ancak çoğu gereçte, günlük verilerine yönelik ortak olay biçimi (CEF) tabanlı biçimlendirme desteklenir. <br>Log Analytics aracısına dayalı Azure Sentinel Aracısı, CEF biçimli günlükleri Log Analytics tarafından kullanılabilecek bir biçime dönüştürür. Gereç türüne bağlı olarak, aracı doğrudan gereç üzerine ya da adanmış bir Linux sunucusuna yüklenir. Linux Aracısı, syslog arka plan programından UDP üzerinden olay alır, ancak bir Linux makinenin yüksek hacimli bir Syslog olayları toplaması bekleniyorsa, bunlar Syslog arka plan programından aracıya ve Log Analytics için Buradan TCP üzerinden gönderilir.
    - Güvenlik duvarları, proxy 'ler ve uç noktalar:
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
        - [Diğer CEF cihazları](connect-common-event-format.md)
        - [Diğer Syslog cihazları](connect-syslog.md)
        - [Barbcuda CloudGen güvenlik duvarı](connect-barracuda-cloudgen-firewall.md)
        - [ExtraHop açığa çıkar (x)](connect-extrahop.md)
        - [Bir kimlik koruma](connect-one-identity.md)
        - [Trend mikro derin güvenlik](connect-trend-micro.md)
    - DLP çözümleri
    - [Tehdit bilgileri sağlayıcıları](connect-threat-intelligence.md)
    - DNS [makineleri](connect-dns.md) -aracı doğrudan DNS makinesine yüklendi
    - Linux sunucuları
    - Diğer bulutlar
    
## Aracı bağlantısı seçenekleri<a name="agent-options"></a>

Dış gerecinizi Azure Sentinel 'e bağlamak için aracının, Gereç ve Azure Sentinel arasındaki iletişimi desteklemesi için adanmış bir makineye (VM veya şirket içi) dağıtılması gerekir. Aracıyı otomatik olarak veya el ile dağıtabilirsiniz. Otomatik dağıtım yalnızca adanmış makineniz Azure 'da oluşturmakta olduğunuz yeni bir VM ise kullanılabilir. 


![Azure 'da CEF](./media/connect-cef/cef-syslog-azure.png)

Alternatif olarak, aracıyı mevcut bir Azure VM 'de, başka bir buluttaki VM 'de veya şirket içi bir makinede el ile dağıtabilirsiniz.

![Şirket içi CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Veri türlerini Azure Sentinel bağlantı seçenekleriyle eşleme


| **Veri türü** | **Bağlanma** | **Veri Bağlayıcısı?** | **Açıklamalar** |
|------|---------|-------------|------|
| Awscses Izi | [AWS 'yi bağlama](connect-aws.md) | Yönetim | |
| AzureActivity | [Azure etkinliğine bağlanma](connect-azure-activity.md) ve [etkinlik günlüklerine genel bakış](../azure-monitor/platform/activity-logs-overview.md)| Yönetim | |
| AuditLogs | [Azure AD 'ye bağlanma](connect-azure-active-directory.md)  | Yönetim | |
| Signınlogs | [Azure AD 'ye bağlanma](connect-azure-active-directory.md)  | Yönetim | |
| AzureFirewall |[Azure Tanılama](../firewall/tutorial-diagnostics.md) | Yönetim | |
| InformationProtectionLogs_CL  | [Azure Information Protection raporları](https://docs.microsoft.com/azure/information-protection/reports-aip)<br>[Azure Information Protection Bağlan](connect-azure-information-protection.md)  | Yönetim | Bu genellikle veri türüne ek olarak **ınformationprotectionevents** işlevini kullanır. Daha fazla bilgi için bkz [. raporları değiştirme ve özel sorgular oluşturma](https://docs.microsoft.com/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Trafik analitik şeması](../network-watcher/traffic-analytics.md) [Trafik Analizi](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [CEF 'yi bağlama](connect-common-event-format.md)  | Yönetim | |
| Officeetkinliği | [Office 365 'e bağlanma](connect-office-365.md) | Yönetim | |
| SecurityEvents | [Windows Güvenlik olaylarını bağlama](connect-windows-security-events.md)  | Yönetim | Güvenli olmayan protokoller çalışma kitapları için bkz. [güvenli olmayan protokoller çalışma kitabı kurulumu](https://blogs.technet.microsoft.com/jonsh/azure-sentinel-insecure-protocols-dashboard-setup/)  |
| Syslog | [Syslog 'ı bağla](connect-syslog.md) | Yönetim | |
| Microsoft Web uygulaması güvenlik duvarı (WAF)-(AzureDiagnostics) |[Microsoft Web uygulaması güvenlik duvarını bağlama](connect-microsoft-waf.md) | Yönetim | |
| SymantecICDx_CL | [Symantec 'e bağlanma](connect-symantec.md) | Yönetim | |
| Threatıntelligenceındicator  | [Tehdit zekası bağlama](connect-threat-intelligence.md)  | Yönetim | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Izleyici hizmeti Haritası](../azure-monitor/insights/service-map.md)<br>[Azure Izleyici VM öngörüleri ekleme](../azure-monitor/insights/vminsights-onboard.md) <br> [Azure Izleyici VM öngörülerini etkinleştirin](../azure-monitor/insights/vminsights-enable-overview.md) <br> [Tek VM 'Yi görsel taslak kullanma](../azure-monitor/insights/vminsights-enable-single-vm.md)<br>  [Ilke aracılığıyla görsel taslak kullanma](../azure-monitor/insights/vminsights-enable-at-scale-policy.md)| X | VM öngörüleri çalışma kitabı  |
| DnsEvents | [DNS bağlama](connect-dns.md) | Yönetim | |
| W3CIISLog | [IIS günlüklerini bağlama](../azure-monitor/platform/data-sources-iis-logs.md)  | X | |
| Iletilen veri | [Kablo verilerini bağlama](../azure-monitor/insights/wire-data.md) | X | |
| WindowsFirewall | [Windows güvenlik duvarını bağlama](connect-windows-firewall.md) | Yönetim | |
| AADıP SecurityAlert  | [Azure AD Kimlik Koruması bağlantısı kurma](connect-azure-ad-identity-protection.md)  | Yönetim | |
| AATP SecurityAlert  | [Azure ATP 'yi bağlama](connect-azure-atp.md) | Yönetim | |
| ASC SecurityAlert  | [Azure Güvenlik Merkezi 'Ne bağlanma](connect-azure-security-center.md)  | Yönetim | |
| MCAS SecurityAlert  | [Microsoft Cloud App Security Bağlan](connect-cloud-app-security.md)  | Yönetim | |
| SecurityAlert | | | |
| Sysmon (olay) | [Sysmon bağlama](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows olaylarını bağlama](../azure-monitor/platform/data-sources-windows-events.md) <br> [Sysmon ayrıştırıcısına ulaşın](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/SysmonParser.txt)| X | Sysmon koleksiyonu, sanal makinelerde varsayılan olarak yüklü değildir. Sysmon aracısının nasıl yükleneceği hakkında daha fazla bilgi için bkz. [Sysmon](https://docs.microsoft.com/sysinternals/downloads/sysmon). |
| ConfigurationData  | [VM envanterini otomatikleştirme](../automation/automation-vm-inventory.md)| X | |
| ConfigurationChange  | [VM izlemeyi otomatikleştirin](../automation/change-tracking.md) | X | |
| F5 BIG-ıP | [F5 BIG-IP bağlantısı](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel.md)  | X | |
| McasShadowItReporting  |  | X | |
| Barracuda_CL | [Barvacuda 'yı bağlama](connect-barracuda.md) | Yönetim | |


## <a name="next-steps"></a>Sonraki adımlar

- Azure Sentinel ile çalışmaya başlamak için Microsoft Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/free/) için kaydolabilirsiniz.
- [Verilerinizi Azure Sentinel 'e](quickstart-onboard.md) [ekleme ve verilerinize ilişkin görünürlük alma ve olası tehditler](quickstart-get-visibility.md)hakkında bilgi edinin.
