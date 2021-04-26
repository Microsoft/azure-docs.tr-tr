---
title: Azure Güvenlik Merkezi 'ndeki uyarılarınızı güvenlik bilgilerine ve olay yönetimi (SıEM) sistemlerine ve diğer izleme çözümlerine akışla
description: Güvenlik uyarılarınızı Azure Sentinel, üçüncü taraf Sıems, SOAR veya ıTSM çözümlerinde nasıl akışla kullanabileceğinizi öğrenin
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 3ddc385b9d489e0c2ab4abf35a6ade011970342b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572955"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Uyarıları bir SıEM, SOAR veya BT hizmet yönetimi çözümüne akış

Azure Güvenlik Merkezi, güvenlik uyarılarınızı en popüler güvenlik bilgileri ve olay yönetimi (SıEM), güvenlik Orchestration otomatik yanıtı (SOAR) ve BT hizmet yönetimi (ıTSM) çözümlerine akışa alabilir.

Uyarı verilerinizi bugün kullanımda olan en popüler çözümlerin tümünde görüntüleyebilmeniz için Azure Native araçlar vardır; örneğin:

- **Azure Sentinel**
- **Splunk Enterprise ve Splunk Cloud**
- **IBM 'in QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Uyarıları Azure Sentinel 'e akış 

Güvenlik Merkezi Azure Sentinel, Azure 'un Cloud-Native SıEM ve SOAR çözümü ile yerel olarak tümleştirilir. 

[Azure Sentinel hakkında daha fazla bilgi edinin](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Güvenlik Merkezi için Azure Sentinel bağlayıcıları

Azure Sentinel, abonelik ve kiracı düzeylerinde Azure Güvenlik Merkezi için yerleşik bağlayıcılar içerir:

- [Abonelik düzeyinde Azure Sentinel 'e uyarı akışı](../sentinel/connect-azure-security-center.md)
- [Kiracınızdaki tüm abonelikleri Azure Sentinel 'e bağlama](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Tüm denetim günlüklerinin Azure Sentinel 'e alımını yapılandırma 

Azure Sentinel 'de Güvenlik Merkezi uyarılarını araştırmaya yönelik başka bir alternatif de denetim günlüklerinizi Azure Sentinel 'e akışıdır:
    - [Windows güvenlik olaylarını bağlama](../sentinel/connect-windows-security-events.md)
    - [Syslog kullanarak Linux tabanlı kaynaklardan veri toplama](../sentinel/connect-syslog.md)
    - [Azure etkinlik günlüğünden veri bağlama](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel, Azure Sentinel 'de analizler ve Azure Izleyici Log Analytics çalışma alanında depolanan verilerin hacmi üzerinden faturalandırılır. Azure Sentinel, esnek ve öngörülebilir bir fiyatlandırma modeli sunar. [Azure Sentinel fiyatlandırma sayfasından daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Microsoft Graph güvenlik API 'SI ile akış uyarıları

Güvenlik Merkezi 'Nde Microsoft Graph güvenlik API 'SI ile kullanıma hazır tümleştirme vardır. Yapılandırma gerekli değildir ve ek maliyet yoktur. 

Bu API 'yi, **kiracınızdaki** (ve diğer birçok Microsoft Güvenlik ürününe ait verilerden), üçüncü taraf sıems ve diğer popüler platformlara olan uyarıları akışa almak için kullanabilirsiniz:

- **Splunk Enterprise ve splunk bulutu**  -  [Splunk için Microsoft Graph SECURITY apı Add-On kullanma](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  -  [Power BI Desktop Microsoft Graph GÜVENLIK API 'Sine bağlanın](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [ServiceNow mağazasından Microsoft Graph SECURITY API uygulamasını yüklemek ve yapılandırmak için yönergeleri izleyin](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [Azure Güvenlik Merkezi için MICROSOFT Graph API aracılığıyla IBM 'Nin cihaz destek modülü](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **anomali**, **gevlül**, **ıNSPARK** ve daha fazla [Microsoft Graph güvenlik API 'si](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Microsoft Graph güvenlik API 'si hakkında daha fazla bilgi edinin](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Azure Izleyici ile uyarı akışı 

Bildirimleri **arcgözetimi**, **splunk**, **SumoLogic**, syslog sunucuları, **Logrhythm**, **Logz.io Cloud Observability platform** ve diğer izleme çözümleriyle akışa almak için. Azure Event Hubs ile Güvenlik Merkezi 'ni Azure izleyici ile bağlama:

1. Güvenlik Merkezi uyarılarını, abonelik düzeyinde adanmış bir Azure Olay Hub 'ına akışa almak için [sürekli dışarı aktarmayı](continuous-export.md) etkinleştirin. 
    > [!TIP]
    > Bunu Azure Ilkesi kullanarak yönetim grubu düzeyinde yapmak için bkz. [ölçekteki sürekli dışarı aktarma Otomasyonu yapılandırması oluşturma](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. Azure [izleyici 'nin yerleşik bağlayıcılarını kullanarak Azure Olay Hub 'ını tercih ettiğiniz çözümünüze bağlayın](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. İsteğe bağlı olarak, ham günlükleri Azure Olay Hub 'ına akış ve tercih ettiğiniz çözüme bağlama. [Kullanılabilir izleme verileri](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available)hakkında daha fazla bilgi edinin.

> [!TIP]
> Aktarılmış veri türlerinin olay şemalarını görüntülemek için, [Olay Hub 'ı olay şemaları](https://aka.ms/ASCAutomationSchemas)' nı ziyaret edin.


## <a name="next-steps"></a>Sonraki adımlar

Bu sayfa, Azure Güvenlik Merkezi 'nin uyarı verilerinizin SıEM, SOAR veya ıTSM aracında kullanılabilir olduğunu nasıl güvence altına almak için açıklanmıştır. İlgili malzemeler için bkz.:

- [Azure Sentinel nedir?](../sentinel/overview.md)
- [Azure Güvenlik Merkezi 'Nde uyarı doğrulama](security-center-alert-validation.md) -uyarılarınızın doğru yapılandırıldığını doğrulama
- [Güvenlik Merkezi verilerini sürekli dışa aktarma](continuous-export.md)