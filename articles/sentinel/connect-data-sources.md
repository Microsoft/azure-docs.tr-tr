---
title: Veri kaynaklarını Azure Sentinel 'e bağlama | Microsoft Docs
description: Microsoft 365 Defender (eski adıyla Microsoft tehdit koruması), Microsoft 365 ve Office 365, Azure AD, ATP ve Cloud App Security gibi veri kaynaklarını Azure Sentinel 'e bağlamayı öğrenin.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 936789ef292ac6d06b9fd3ac1e9a81219f39e4ef
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307835"
---
# <a name="connect-data-sources"></a>Veri kaynaklarını bağlama

Azure Sentinel etkinleştirildikten sonra yapmanız gereken ilk şey, veri kaynaklarınızı bağlamak. Azure Sentinel, Microsoft çözümleri için çok sayıda bağlayıcı sunar ve Microsoft 365 Defender (eski adıyla Microsoft tehdit koruması) çözümleri, Microsoft 365 kaynakları (Office 365 dahil), Azure AD, kimlik için Microsoft Defender (eskiden Azure ATP), Microsoft Cloud App Security ve daha fazlasını içeren gerçek zamanlı tümleştirme sağlar. Ayrıca, Microsoft dışı çözümler için daha geniş güvenlik ekosistemine yerleşik bağlayıcılar vardır. Veri kaynaklarınızı Azure Sentinel 'e bağlamak için ortak olay biçimi (CEF), syslog veya REST API 'yi de kullanabilirsiniz.

1. Menüsünde, **veri bağlayıcıları**' nı seçin. Bu sayfa, Azure Sentinel 'in sağladığı bağlayıcıların tam listesini ve bunların durumlarını görmenizi sağlar. Bağlanmak istediğiniz bağlayıcıyı seçin ve **bağlayıcı sayfasını aç**' ı seçin. 

   ![Veri bağlayıcıları Galerisi](./media/collect-data/collect-data-page.png)

1. Belirli bağlayıcı sayfasında, tüm önkoşulları karşıladığınızdan emin olun ve verileri Azure Sentinel 'e bağlamak için yönergeleri izleyin. Günlüklerin Azure Sentinel ile eşitlenmeye başlaması biraz zaman alabilir. Bağlandıktan sonra, **alınan veri** grafiğindeki verilerin özetini ve veri türlerinin bağlantı durumunu görürsünüz.

   ![Veri bağlayıcıları yapılandırma](./media/collect-data/opened-connector-page.png)
  
1. Belirli veri türü için Azure Sentinel 'in sağladığı kullanıma hazır içeriğin bir listesini almak için **sonraki adımlar** sekmesine tıklayın.

   ![Bağlayıcılar için sonraki adımlar](./media/collect-data/data-insights.png)
 

## <a name="data-connection-methods"></a>Veri bağlantısı yöntemleri

Aşağıdaki veri bağlantısı yöntemleri Azure Sentinel tarafından desteklenir:

- **Hizmetten hizmete tümleştirme**:<br> Bazı hizmetler yerel olarak (AWS ve Microsoft Hizmetleri gibi) bağlı olduğundan, bu hizmetler, kullanıma hazır tümleştirme için Azure Foundation 'dan yararlanır ve aşağıdaki çözümler birkaç tıklamayla bağlanabilir:
    - [Amazon Web Services-Cloudtratem](connect-aws.md)
    - [Azure Active Directory](connect-azure-active-directory.md) -denetim günlükleri ve oturum açma günlükleri
    - [Azure etkinliği](connect-azure-activity.md)
    - [Azure AD Kimlik Koruması](connect-azure-ad-Identity-protection.md)
    - [Azure DDoS Koruması](connect-azure-ddos-protection.md)
    - [IoT Için Azure Defender](connect-asc-iot.md) (eski adıyla IoT Için Azure Güvenlik Merkezi)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Güvenlik Duvarı](connect-azure-firewall.md)
    - [Azure Güvenlik Merkezi](connect-azure-security-center.md) -Azure Defender çözümlerindeki uyarılar
    - [Azure Web uygulaması güvenlik duvarı (WAF)](connect-azure-waf.md) (eski adıyla Microsoft WAF)
    - [Cloud App Security](connect-cloud-app-security.md)
    - [Etki alanı adı sunucusu](connect-dns.md)
    - [Microsoft 365 Defender](connect-microsoft-365-defender.md) -M365D OLAYLARıNı ve MDE ham verileri içerir
    - [Uç nokta Için Microsoft Defender](connect-microsoft-defender-advanced-threat-protection.md) (eski adıyla Microsoft Defender Gelişmiş tehdit koruması)
    - [Kimlik Için Microsoft Defender](connect-azure-atp.md) (eski adıyla Azure Gelişmiş tehdit koruması)
    - [Office 365 Için Microsoft Defender](connect-office-365-advanced-threat-protection.md) (eski adıyla Office 365 Gelişmiş tehdit koruması)
    - [Office 365](connect-office-365.md) (artık ekiplerle!)
    - [Windows Güvenlik Duvarı](connect-windows-firewall.md)
    - [Windows güvenlik olayları](connect-windows-security-events.md)

- **API aracılığıyla dış çözümler**: bazı veri kaynakları, bağlı veri kaynağı tarafından sağlanmış API 'ler kullanılarak bağlanır. Genellikle, çoğu güvenlik teknolojisi, olay günlüklerinin alınabileceği bir API kümesi sağlar. API 'Ler Azure Sentinel 'e bağlanır ve belirli veri türlerini toplayıp Azure Log Analytics 'e gönderir. API aracılığıyla bağlı olan gereçler şunları içerir:
    
    - [Agari savunma ve marka koruması](connect-agari-phishing-defense.md)
    - [Alcide kAudit](connect-alcide-kaudit.md)
    - [Barracuda WAF](connect-barracuda.md)
    - [Barracuda CloudGen Firewall](connect-barracuda-cloudgen-firewall.md)
    - [BETTER Mobile Threat Defense](connect-better-mtd.md)
    - [Beyond Security beSECURE](connect-besecure.md)
    - [Cisco Umbrella](connect-cisco-umbrella.md)
    - [Citrix Analytics (Güvenlik)](connect-citrix-analytics.md)
    - [F5 BIG-IP](connect-f5-big-ip.md)
    - [Forcepoint DLP](connect-forcepoint-dlp.md)
    - [Google çalışma alanı (eski adıyla G Suite)](connect-google-workspace.md)
    - [NXLog (Windows) DNS günlükleri](connect-nxlog-dns.md)
    - [NXLog LinuxAudit](connect-nxlog-linuxaudit.md)
    - [Okta SSO](connect-okta-single-sign-on.md)
    - [Orca Security](connect-orca-security-alerts.md)
    - [Perimeter 81 günlükleri](connect-perimeter-81-logs.md)
    - [Istek üzerine düzeltme noktası (POD) e-posta güvenliği](connect-proofpoint-pod.md)
    - [Proofpoint TAP](connect-proofpoint-tap.md)
    - [Qualys VM](connect-qualys-vm.md)
    - [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
    - [Sophos Cloud OPX](connect-sophos-cloud-optix.md)
    - [Squadra Technologies secRMM](connect-squadra-secrmm.md)
    - [Symantec ICDX](connect-symantec.md)
    - [VMware Carbon Black Cloud Endpoint Standard](connect-vmware-carbon-black.md)
    - [Zimperium](connect-zimperium-mtd.md)


- **Aracı aracılığıyla dış çözümler**: Azure Sentinel, syslog protokolünü kullanarak gerçek zamanlı günlük akışı gerçekleştirebilen başka bir veri kaynağına aracı aracılığıyla bağlanabilir.

    Çoğu gereçleri, günlüğü kapsayan olay iletilerini ve günlük bilgilerini içeren olay iletilerini göndermek için Syslog protokolünü kullanır. Günlüklerin biçimi değişir, ancak çoğu gereçte günlük verileri için CEF tabanlı biçimlendirme desteklenir. 

    Aslında Log Analytics aracı olan Azure Sentinel Aracısı, CEF biçimli günlükleri Log Analytics tarafından kullanılabilecek bir biçime dönüştürür. Gereç türüne bağlı olarak, aracı doğrudan gereç üzerine ya da ayrılmış bir Linux tabanlı günlük ileticisine yüklenir. Linux Aracısı, syslog arka plan programından UDP üzerinden olay alır, ancak bir Linux makinenin yüksek hacimli bir Syslog olayları toplaması bekleniyorsa, bunlar Syslog arka plan programından aracıya ve Log Analytics için Buradan TCP üzerinden gönderilir.

    - **Güvenlik duvarları, proxy 'ler ve uç noktalar-CEF:**
        - [AI Vectra Detect](connect-ai-vectra-detect.md)
        - [Akamai güvenlik olayları](connect-akamai-security-events.md)
        - [Aruba ClearPass](connect-aruba-clearpass.md)
        - [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
        - [Denetim Noktası](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Citrix WAF](connect-citrix-waf.md)
        - [CyberArk Enterprise Password Vault](connect-cyberark.md)
        - [ExtraHop Reveal(x)](connect-extrahop.md)
        - [F5 ASM](connect-f5.md)
        - [Forcepoint ürünleri](connect-forcepoint-casb-ngfw.md)
        - [Fortinet](connect-fortinet.md)
        - [Illusive Networks AMS](connect-illusive-attack-management-system.md)
        - [Kusurda WAF ağ geçidi](connect-imperva-waf-gateway.md)
        - [One Identity Safeguard](connect-one-identity.md)
        - [Palo Alto Networks](connect-paloalto.md)
        - [Thycotic Secret Server](connect-thycotic-secret-server.md)
        - [Trend Micro Deep Security](connect-trend-micro.md)
        - [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
        - [Kablox ağ Forensics platformu](connect-wirex-systems.md)
        - [Zscaler](connect-zscaler.md)
        - [Diğer CEF tabanlı gereçler](connect-common-event-format.md)
    - **Güvenlik duvarları, proxy 'ler ve uç noktalar-Syslog:**
        - [Active Directory için alsıd](connect-alsid-active-directory.md)
        - [Cisco Meraki](connect-cisco-meraki.md)
        - [Cisco Birleşik Bilgi Işlem sistemi (UCS)](connect-cisco-ucs.md)
        - [Infoblox NIOS](connect-infoblox.md)
        - [Juniper SRX](connect-juniper-srx.md)
        - [Pulse Connect Secure](connect-pulse-connect-secure.md)
        - [Sophos XG](connect-sophos-xg-firewall.md)
        - [Squid Proxy](connect-squid-proxy.md)
        - [Symantec Proxy SG](connect-symantec-proxy-sg.md)
        - [Symantec VIP](connect-symantec-vip.md)
        - [VMware ESXi](connect-vmware-esxi.md)
        - [Diğer Syslog tabanlı gereçler](connect-syslog.md)
    - [Apache HTTP sunucusu](connect-apache-http-server.md)
    - DLP çözümleri
    - [Tehdit analizi sağlayıcıları](connect-threat-intelligence.md)
    - DNS [makineleri](connect-dns.md) -aracı doğrudan DNS makinesine yüklendi
    - [Azure Stack VM 'Ler](connect-azure-stack.md)
    - Linux sunucuları
    - Diğer bulutlar
    
## <a name="agent-connection-options"></a>Aracı bağlantı seçenekleri<a name="agent-options"></a>

Dış gerecinizi Azure Sentinel 'e bağlamak için aracının, Gereç ve Azure Sentinel arasındaki iletişimi desteklemesi için adanmış bir makineye (VM veya şirket içi) dağıtılması gerekir. Aracıyı otomatik olarak veya el ile dağıtabilirsiniz. Otomatik dağıtım yalnızca adanmış makineniz Azure 'da oluşturmakta olduğunuz yeni bir VM ise kullanılabilir. 

![Azure 'da CEF](./media/connect-cef/cef-syslog-azure.png)

Alternatif olarak, aracıyı mevcut bir Azure VM 'de, başka bir buluttaki VM 'de veya şirket içi bir makinede el ile dağıtabilirsiniz.

![Şirket içi CEF](./media/connect-cef/cef-syslog-onprem.png)

## <a name="map-data-types-with-azure-sentinel-connection-options"></a>Veri türlerini Azure Sentinel bağlantı seçenekleriyle eşleme


| **Veri türü** | **Bağlanma** | **Veri Bağlayıcısı?** | **Açıklamalar** |
|------|---------|-------------|------|
| Awscses Izi | [AWS’yi bağlama](connect-aws.md) | &#10003; | |
| AzureActivity | [Azure etkinliğine bağlanma](connect-azure-activity.md) ve [etkinlik günlüklerine genel bakış](../azure-monitor/essentials/platform-logs-overview.md)| &#10003; | |
| AuditLogs | [Azure AD’yi bağlama](connect-azure-active-directory.md)  | &#10003; | |
| Signınlogs | [Azure AD’yi bağlama](connect-azure-active-directory.md)  | &#10003; | |
| AzureFirewall |[Azure Tanılama](../firewall/firewall-diagnostics.md) | &#10003; | |
| InformationProtectionLogs_CL  | [Azure Information Protection raporları](/azure/information-protection/reports-aip)<br>[Azure Information Protection’ı bağlama](connect-azure-information-protection.md)  | &#10003; | Bu genellikle veri türüne ek olarak **ınformationprotectionevents** işlevini kullanır. Daha fazla bilgi için bkz [. raporları değiştirme ve özel sorgular oluşturma](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries)|
| AzureNetworkAnalytics_CL  | [Trafik analitik şeması](../network-watcher/traffic-analytics.md) [Trafik Analizi](../network-watcher/traffic-analytics.md)  | | |
| CommonSecurityLog  | [CEF 'yi bağlama](connect-common-event-format.md)  | &#10003; | |
| Officeetkinliği | [Office 365’i bağlama](connect-office-365.md) | &#10003; | |
| SecurityEvents | [Windows güvenlik olaylarını bağlama](connect-windows-security-events.md)  | &#10003; | Güvenli olmayan protokoller çalışma kitapları için bkz. [güvenli olmayan protokoller çalışma kitabı kurulumu](./quickstart-get-visibility.md#use-built-in-workbooks)  |
| Syslog | [Syslog’u bağlama](connect-syslog.md) | &#10003; | |
| Microsoft Web uygulaması güvenlik duvarı (WAF)-(AzureDiagnostics) |[Microsoft Web uygulaması güvenlik duvarını bağlama](./connect-azure-waf.md) | &#10003; | |
| SymantecICDx_CL | [Symantec 'e bağlanma](connect-symantec.md) | &#10003; | |
| Threatıntelligenceındicator  | [Tehdit zekasını bağlama](connect-threat-intelligence.md)  | &#10003; | |
| VMConnection <br> ServiceMapComputer_CL<br> ServiceMapProcess_CL|  [Azure Izleyici hizmeti Haritası](../azure-monitor/vm/service-map.md)<br>[Azure Izleyici VM öngörüleri ekleme](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Azure Izleyici VM öngörülerini etkinleştirin](../azure-monitor/vm/vminsights-enable-overview.md) <br> [Tek VM 'Yi görsel taslak kullanma](../azure-monitor/vm/vminsights-enable-portal.md)<br>  [Ilke aracılığıyla görsel taslak kullanma](../azure-monitor/vm/vminsights-enable-policy.md)| &#10007; | VM öngörüleri çalışma kitabı  |
| DnsEvents | [DNS bağlama](connect-dns.md) | &#10003; | |
| W3CIISLog | [IIS günlüklerini bağlama](../azure-monitor/agents/data-sources-iis-logs.md)  | &#10007; | |
| Iletilen veri | [Kablo verilerini bağlama](../azure-monitor/insights/wire-data.md) | &#10007; | |
| WindowsFirewall | [Windows güvenlik duvarını bağlama](connect-windows-firewall.md) | &#10003; | |
| AADıP SecurityAlert  | [Azure AD Kimlik Koruması bağlantısı kurma](connect-azure-ad-identity-protection.md)  | &#10003; | |
| AATP SecurityAlert  | [Kimlik Için Microsoft Defender 'ı bağlama](connect-azure-atp.md) (eski ADıYLA Azure ATP) | &#10003; | |
| ASC SecurityAlert  | Azure Güvenlik Merkezi 'nden [Azure Defender uyarılarını bağlama](connect-azure-security-center.md)  | &#10003; | |
| MCAS SecurityAlert  | [Microsoft Cloud App Security Bağlan](connect-cloud-app-security.md)  | &#10003; | |
| SecurityAlert | | | |
| Sysmon (olay) | [Sysmon bağlama](https://azure.microsoft.com/blog/detecting-in-memory-attacks-with-sysmon-and-azure-security-center)<br> [Windows olaylarını bağlama](../azure-monitor/agents/data-sources-windows-events.md) <br> [Sysmon ayrıştırıcısına ulaşın](https://github.com/Azure/Azure-Sentinel/blob/master/Parsers/Sysmon/Sysmon-v10.42-Parser.txt)| &#10007; | Sysmon koleksiyonu, sanal makinelerde varsayılan olarak yüklü değildir. Sysmon aracısının nasıl yükleneceği hakkında daha fazla bilgi için bkz. [Sysmon](/sysinternals/downloads/sysmon). |
| ConfigurationData  | [VM envanterini otomatikleştirme](../automation/change-tracking/overview.md)| &#10007; | |
| ConfigurationChange  | [VM izlemeyi otomatikleştirin](../automation/change-tracking/overview.md) | &#10007; | |
| F5 BIG-IP | [F5 BIG-IP'yi bağlama](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)  | &#10003; | |
| McasShadowItReporting  |  | &#10007; | |
| Barracuda_CL | [Barracuda’yı bağlama](connect-barracuda.md) | &#10003; | |


## <a name="next-steps"></a>Sonraki adımlar

- Azure Sentinel ile çalışmaya başlamak için Microsoft Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa [ücretsiz deneme sürümü](https://azure.microsoft.com/free/) için kaydolabilirsiniz.
- [Verilerinizi Azure Sentinel 'e](quickstart-onboard.md) [ekleme ve verilerinize ilişkin görünürlük alma ve olası tehditler](quickstart-get-visibility.md)hakkında bilgi edinin.
