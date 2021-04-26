---
title: Azure Sentinel veri kaynağı şema başvurusu
description: Bu makalede, Azure Sentinel tarafından desteklenen Azure ve üçüncü taraf veri kaynağı şemaları, başvuru belgelerinin bağlantılarıyla birlikte listelenir.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 4601f2d6eddbbe8809dfd46a7e0cc5aa3c40c722
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209579"
---
# <a name="data-source-schema-reference"></a>Veri kaynağı şema başvurusu

Bu makalede, desteklenen Azure ve üçüncü taraf veri kaynağı şemaları, başvuru belgelerinin bağlantılarıyla birlikte listelenmektedir.

## <a name="azure-data-sources"></a>Azure veri kaynakları

| Tür                             | Veri kaynağı             | Log Analytics TableName | Şema başvurusu |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | Signınevents           | [Azure AD etkinlik raporları oturum açma özellikleri](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Azure Izleyici AuditLogs başvurusu](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Azure Izleyici AzureActivity başvurusu](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | Officeetkinliği         | Office 365 yönetim etkinliği API şemaları: <br>- [Ortak şema ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Exchange Yönetim şeması ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Exchange posta kutusu şeması](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [SharePoint taban şeması](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [SharePoint dosya işlemleri](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Azure Key Vault         | AzureDiagnostics       | [Azure Izleyici AzureDiagnostics başvurusu](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Konak**                             | Linux                  | Syslog                 | [Azure Izleyici Syslog başvurusu](/azure/azure-monitor/reference/tables/syslog) |
| **Ağ**                          | IIS Günlükleri               | W3CIISLog              | [Azure Izleyici W3CIISLog başvurusu](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Ağ**                          | VMinsights             | VMConnection           | [Azure Izleyici VMConnection başvurusu](/azure/azure-monitor/reference/tables/vmconnection) |
| **Ağ**                          | Tel veri çözümü     | Iletilen veri               | [Azure Izleyici kablolu veri başvurusu](/azure/azure-monitor/reference/tables/wiredata) |
| **Ağ**                          | NSG akış günlükleri          | AzureNetworkAnalytics  | [Trafik Analizi şema ve veri toplama](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> Daha fazla bilgi için [Azure izleyici veri başvurusunun](/azure/azure-monitor/reference/)tamamına bakın.
>
## <a name="3rd-party-vendor-data-sources"></a>üçüncü taraf satıcı veri kaynakları

Aşağıdaki tabloda, desteklenen üçüncü taraf satıcıları ve bunların Syslog veya ortak olay biçimi (CEF) eşleme belgelerinin yanı sıra CEF alan eşlemelerini ve her bir kategori türü için örnek günlükleri içeren çeşitli desteklenen günlük türleri listelenmektedir.

| Tür |    Satıcı |    Ürün | Log Analytics TableName | CEF alan eşleme başvurusu  |
| ----- | ----- | ----- | ----- |----- |
| **Ağ** | Palo Alto   | IŞLETIM SISTEMINI KAYDıR    | CommonSecurityLog |   [Pan-OS 9,0 ortak olay biçimi tümleştirme Kılavuzu](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) ( *CEF stili günlük biçimlerini* ara) |
| **Ağ** | Denetim Noktası  |ALL   | CommonSecurityLog | [Günlük alanları açıklaması](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Ağ** | Fortigate   | ALL   | CommonSecurityLog | [Günlük şeması yapısı](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Ağ** | Barracuda | Web Uygulaması Güvenlik Duvarı |  CommonSecurityLog   | [Syslog ve diğer günlükleri yapılandırma](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Ağ** | Cisco | ASA | CommonSecurityLog | [Cisco ASA serisi Syslog Iletileri](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Ağ** | Cisco | Firepower   | CommonSecurityLog | [Cisco Fireuzman tehdit savunma Syslog Iletileri](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.html)    |
| **Ağ** | Cisco   | Şemsiye  | Özel Günlükler tablosu  | [Günlük biçimleri ve sürüm oluşturma](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Ağ**   | Cisco | Meraki    | CommonSecurityLog |   [Syslog olay türleri ve günlük örnekleri](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Ağ**   | Zscaler | Nano akış hizmeti (NSS)|   CommonSecurityLog | [NSS akışlarını biçimlendirme](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (yalnızca Web, güvenlik DUVARı, DNS ve tünel günlükleri) |
| **Ağ**   |F5 | BigIP LTM|    CommonSecurityLog|  [Olay Iletileri ve saldırı türleri](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Ağ** | F5  | BigIP ASM|    CommonSecurityLog|  [Uygulama güvenliği olaylarını günlüğe kaydetme](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Ağ** | Citrix  |Web uygulaması güvenlik duvarı   | CommonSecurityLog|    [Uygulama güvenlik duvarında ortak olay biçimi (CEF) günlüğü desteği](https://support.citrix.com/article/CTX136146) <br>  [NetScaler 12,0 Syslog Ileti başvurusu](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Konak** |Symantec | Symantec Endpoint Protection Yöneticisi (SEPM) | CommonSecurityLog|[Endpoint Protection Yöneticisi için dış günlük kaydı ayarları ve günlük olayı önem düzeyleri](https://support.symantec.com/us/en/article.tech171741.html)|
|**Konak** |Eğilim mikro |Tümü |CommonSecurityLog | [Syslog Içerik eşleme-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

> [!NOTE]
> Daha fazla bilgi için bkz. [CEF ve CommonSecurityLog alan eşleme](cef-name-mapping.md).
> 
## <a name="next-steps"></a>Sonraki adımlar

CEF, syslog, Direct, Agent ve özel bağlayıcılar gibi desteklenen Azure Sentinel bağlayıcıları hakkında daha fazla bilgi edinin:

- [Veri kaynaklarını bağlama](connect-data-sources.md)

- [Azure Sentinel syslog, CEF ve diğer 3. taraf bağlayıcıları](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)
