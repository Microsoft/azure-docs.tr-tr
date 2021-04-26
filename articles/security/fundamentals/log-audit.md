---
title: Azure Güvenlik günlüğü ve denetimi | Microsoft Docs
description: Azure 'da kullanılabilen Günlükler ve kazanabileceğinizi güvenlik öngörüleri hakkında bilgi edinin.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: 0d85cf6ae501a7d50f20e48543e361149f4b57d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580551"
---
# <a name="azure-security-logging-and-auditing"></a>Azure güvenliğini günlüğe kaydetme ve denetleme

Azure, güvenlik ilkeleriniz ve mekanizmalarda boşlukları belirlemenize yardımcı olmak için çok sayıda yapılandırılabilir güvenlik denetimi ve günlüğe kaydetme seçeneği sunar. Bu makalede, Azure 'da barındırılan hizmetlerden güvenlik günlüklerinin oluşturulması, toplanması ve çözümlenmesi anlatılmaktadır.

> [!Note]
> Bu makaledeki bazı öneriler, verilerin, ağın veya işlem kaynağı kullanımının artmasına ve lisans ya da abonelik maliyetlerinizi artırmaya yol açabilir.

## <a name="types-of-logs-in-azure"></a>Azure 'da günlüklerin türleri

Bulut uygulamaları birçok hareketli parça ile karmaşıktır. Günlüğe kaydetme verileri, uygulamalarınız hakkında öngörüler sağlayabilir ve şunları yapmanıza yardımcı olabilir:

- Geçmişteki sorunları giderin veya olası olanları engelleyin
- Uygulama performansını veya bakımlılığını geliştirme
- Aksi takdirde el ile müdahale gerektiren eylemleri otomatikleştirin

Azure günlükleri aşağıdaki türlerde kategorilere ayrılır:
* **Denetim/Yönetim günlükleri** Azure Resource Manager oluşturma, GÜNCELLEŞTIRME ve silme işlemleri hakkında bilgi sağlar. Daha fazla bilgi için bkz. [Azure etkinlik günlükleri](../../azure-monitor/essentials/platform-logs-overview.md).

* **Veri düzlemi günlükleri** , Azure Kaynak kullanımının bir parçası olarak oluşturulan olaylar hakkında bilgi sağlar. Bu tür bir günlüğe örnek olarak, bir sanal makinede (VM) Windows olay sistemi, güvenlik ve uygulama günlükleri ve Azure Izleyici aracılığıyla yapılandırılan [tanılama günlükleri](../../azure-monitor/essentials/platform-logs-overview.md) verilebilir.

* **İşlenen olaylar** , sizin adınıza işlenmiş olan çözümlenen olaylar/uyarılar hakkında bilgi sağlar. Bu tür örnekler, Azure [Güvenlik Merkezi](../../security-center/security-center-introduction.md) 'nin aboneliğinizi işlediği ve çözümleymekte olduğu ve kısa güvenlik uyarıları sağladığı [Azure Güvenlik Merkezi uyarılarından](../../security-center/security-center-managing-and-responding-alerts.md) oluşur.

Aşağıdaki tabloda, Azure 'da kullanılabilen en önemli günlük türleri listelenmektedir:

| Günlük kategorisi | Günlük türü | Kullanım | Tümleştirme |
| ------------ | -------- | ------ | ----------- |
|[Etkinlik günlükleri](../../azure-monitor/essentials/platform-logs-overview.md)|Azure Resource Manager kaynaklardaki denetim düzlemi olayları|    Aboneliğinizdeki kaynaklarda gerçekleştirilen işlemlere ilişkin öngörüler sağlar.|    REST API, [Azure izleyici](../../azure-monitor/essentials/platform-logs-overview.md)|
|[Azure Kaynak günlükleri](../../azure-monitor/essentials/platform-logs-overview.md)|Abonelikte Azure Resource Manager kaynakların çalışması hakkında sık kullanılan veriler| Kaynağınızın kendisi tarafından gerçekleştirilen işlemlere ilişkin öngörüler sağlar.| Azure İzleyici|
|[Azure Active Directory raporlama](../../active-directory/reports-monitoring/overview-reports.md)|Günlükler ve raporlar | Kullanıcılar ve grup yönetimiyle ilgili Kullanıcı oturum açma etkinliklerini ve sistem etkinliği bilgilerini raporlar.|[Graph API](../../active-directory/develop/microsoft-graph-intro.md)|
|[Sanal makineler ve bulut Hizmetleri](../../azure-monitor/vm/quick-collect-azurevm.md)|Windows olay günlüğü hizmeti ve Linux Syslog| , Sanal makinelerde sistem verilerini yakalar ve verileri günlüğe kaydeder ve bu verileri seçtiğiniz bir depolama hesabına aktarır.|   Azure Izleyici 'de Windows (Windows Azure Tanılama [[wad](../../azure-monitor/agents/diagnostics-extension-overview.md)] depolama) ve Linux 'u kullanma|
|[Azure Depolama Analizi](/rest/api/storageservices/fileservices/storage-analytics)|Depolama alanı günlüğü, bir depolama hesabı için ölçüm verileri sağlar|İzleme istekleri hakkında öngörüler sağlar, kullanım eğilimlerini analiz eder ve depolama hesabınızla ilgili sorunları tanılar.| REST API veya [istemci kitaplığı](/dotnet/api/overview/azure/storage)|
|[Ağ güvenlik grubu (NSG) akış günlükleri](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON biçimi, giden ve gelen akışları kural başına temelinde gösterir|Bir ağ güvenlik grubu üzerinden giriş ve çıkış IP trafiği hakkındaki bilgileri görüntüler.|[Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Uygulama öngörüleri](../../azure-monitor/app/app-insights-overview.md)|Günlükler, özel durumlar ve özel Tanılamalar|  Birden çok platformda Web geliştiricileri için bir uygulama performansı izleme (APM) hizmeti sağlar.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[İşlem verileri/güvenlik uyarıları](../../security-center/security-center-introduction.md)|   Azure Güvenlik Merkezi uyarıları, Azure Izleyici günlükleri uyarıları|    Güvenlik bilgileri ve uyarılar sağlar.|  REST API 'Leri, JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>Şirket içi SıEM sistemleriyle günlük tümleştirmesi
[Güvenlik Merkezi uyarılarını tümleştirmek](../../security-center/security-center-partner-integration.md) , güvenlik merkezi uyarılarını, Azure tanılama günlükleri tarafından toplanan sanal makine güvenlik olaylarını ve Azure izleyici günlüklerinizi veya SIEM çözümünüzle Azure denetim günlüklerini nasıl eşitleyeceğinizi tartışır.

## <a name="next-steps"></a>Sonraki adımlar

- [Denetleme ve günlüğe kaydetme](management-monitoring-overview.md): düzenli olarak güvenlik uyarılarına giderek verileri koruyun ve hızla yanıt verir.

- [Azure 'Da güvenlik günlüğü ve denetim günlüğü koleksiyonu](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Azure örneklerinizin doğru güvenlik ve denetim günlüklerini toplamasını sağlamak için bu ayarları uygulayın.

- [Site koleksiyonu için denetim ayarlarını yapılandırma](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): bir site koleksiyonu yöneticisiyseniz, bireysel kullanıcıların eylemlerinin geçmişini ve belirli bir tarih aralığında gerçekleştirilen eylemlerin geçmişini alın.

- [Microsoft 365 Güvenlik Merkezi 'nde denetim günlüğünde arama](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)yapın: Birleşik denetim günlüğünü aramak ve kuruluşunuzdaki Kullanıcı ve yönetici etkinliğini görüntülemek için Microsoft 365 güvenlik merkezini kullanın.