---
title: Azure 'da izleme çözümlerinin envanterini görüntüleme | Microsoft Docs
description: Azure Izleyici 'de izleme çözümleri, belirli bir sorun alanı etrafında ölçüm sağlayan bir mantık, görselleştirme ve veri alma kuralları koleksiyonudur.  Bu makalede, Microsoft 'tan kullanılabilen izleme çözümlerinin bir listesi ve bunların yöntemi ve veri toplama sıklığı hakkındaki ayrıntılar verilmektedir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 6012a74c00b64c818434ea1744d86c6cf67dd463
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931325"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Azure 'da çözüm izleme için envanter ve veri toplama ayrıntıları
[İzleme çözümleri](solutions.md) , belirli bir uygulama veya hizmetin işlemine ek Öngörüler sağlamak için Azure 'daki hizmetlerden yararlanır. İzleme çözümleri genellikle günlük verilerini toplar ve toplanan verileri çözümlemek için sorgular ve görünümler sağlar. Azure Izleyici 'ye, kullandığınız tüm uygulama ve hizmetler için izleme çözümleri ekleyebilirsiniz. Bunlar genellikle ücretsiz olarak kullanılabilir ancak kullanım ücretlerini çağırabilecek veri toplar.

Bu makale, Microsoft 'tan sunulan ve ayrıntılı belgelerinin bağlantılarıyla birlikte bulunan çok sayıda [çözümlerin](solutions.md) bir listesini içerir.  Ayrıca, Azure Izleyici 'de veri toplama yöntemi ve sıklığı hakkında bilgi de sağlar.  Bu makaledeki bilgileri kullanarak, kullanılabilir farklı çözümleri tanımlayabilir ve farklı izleme çözümlerine yönelik veri akışını ve bağlantı gereksinimlerini anlayabilirsiniz.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>İzleme çözümlerinin listesi

Aşağıdaki tabloda, Microsoft tarafından sunulan Azure 'daki [izleme çözümleri](solutions.md) listelenmektedir. Sütunundaki bir giriş, çözümün bu yöntemi kullanarak Azure Izleyici 'ye veri topladığı anlamına gelir.  Bir çözümün seçili sütunu yoksa, başka bir Azure hizmetinden Azure Izleyici 'ye doğrudan yazar. Daha fazla bilgi için, her biri için ayrıntılı belgelere yönelik bağlantıyı izleyin.

Sütunların açıklamaları aşağıdaki gibidir:

- **Microsoft Monitoring Agent** -Windows ve Linux 'TA, SCOM 'den yönetim ve Izleme çözümlerini Azure 'dan çalıştırmak için kullanılan aracı. Bu yapılandırmada, aracı bir Operations Manager yönetim grubuna bağlı kalmadan doğrudan Azure Izleyici 'ye bağlanır. 
- **Operations Manager** -Microsoft Monitoring Agent ile aynı aracı. Bu yapılandırmada, Azure Izleyici 'ye bağlı [bir Operations Manager yönetim grubuna bağlanır](../platform/om-agents.md) . 
-  **Azure depolama** -çözüm, Azure Storage hesabından veri toplar. 
- **Operations Manager gerekli mi?** -İzleme çözümü tarafından veri toplama için bağlı bir Operations Manager yönetim grubu gerekir. 
- **Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager** -aracı [bir SCOM yönetim grubuna bağlıysa](../platform/om-agents.md), veriler yönetim sunucusundan Azure izleyici 'ye gönderilir. Bu durumda, aracının Azure Izleyici 'ye doğrudan bağlanması gerekmez. Bu kutu seçili değilse, aracı bir SCOM yönetim grubuna bağlı olsa bile, veriler aracıdan doğrudan Azure Izleyicisine gönderilir. [Log Analytics ağ geçidi](../platform/gateway.md)aracılığıyla Azure izleyici ile iletişim kurabilmesi gerekecektir.
- **Toplama sıklığı** -verilerin izleme çözümü tarafından toplandığı sıklığı belirtir. 



| **İzleme çözümü** | **Platformunun** | **Microsoft İzleme Aracısı** | **Operations Manager Aracısı** | **Azure depolama alanı** | **Operations Manager gerekli mi?** | **Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager** | **Toplama sıklığı** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Etkinlik günlüğü Analizi](../platform/activity-log-collect.md) | Azure | | | | | | bildirimde |
| [AD Değerlendirmesi](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 gün |
| [AD Çoğaltma Durumu](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 gün |
| [Aracı Durumu](solution-agenthealth.md) | Windows ve Linux | &#8226; | &#8226; | | | &#8226; | 1 dakika |
| [Uyarı yönetimi](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |varış noktasında |
| [Uyarı yönetimi](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 dakika |
| [Uyarı yönetimi](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 dakika |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | Yok |
| [Application Insights Bağlayıcısı (kullanım dışı)](../platform/app-insights-connector.md) | Azure | | | |  |  | bildirimde |
| [Otomasyon Karma Çalışanı](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | Yok |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | bildirimde |
| **İzleme çözümü** | **Platformunun** | **Microsoft İzleme Aracısı** | **Operations Manager Aracısı** | **Azure depolama alanı** | **Operations Manager gerekli mi?** | **Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager** | **Toplama sıklığı** |
| [Azure Ağ Güvenlik Grubu Analizi (kullanım dışı)](azure-networking-analytics.md) | Azure |  |  |  |  |  | bildirimde |
| [Azure SQL Analytics (Önizleme)](azure-sql.md) | Windows | | | | | | 1 dakika |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | bildirimde |
| [Kapasite ve Performans (Önizleme)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |varış noktasında |
| [Değişiklik İzleme](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[olmadığına](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Değişiklik İzleme](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[olmadığına](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Kapsayıcılar](containers.md) | Windows ve Linux | &#8226; | &#8226; |  |  |  | 3 dakika |
| [Anahtar Kasası Analizi](azure-key-vault.md) |Windows | | | | | |bildirimde |
| [Kötü Amaçlı Yazılım Değerlendirmesi](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |hourly |
| [Ağ Performansı İzleyicisi](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | Her 5 saniyede TCP el sıkışmaları, 3 dakikada bir gönderilen veriler |
| [Office 365 Analytics (Önizleme)](solution-office-365.md) |Windows | | | | | |bildirimde |
| **İzleme çözümü** | **Platformunun** | **Microsoft İzleme Aracısı** | **Operations Manager Aracısı** | **Azure depolama alanı** | **Operations Manager gerekli mi?** | **Yönetim grubu aracılığıyla gönderilen aracı verileri Operations Manager** | **Toplama sıklığı** |
| [Service Fabric Analizi](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 dakika |
| [Hizmet Eşlemesi](service-map.md) | Windows ve Linux | &#8226; | &#8226; |  |  |  | 15 saniye |
| [SQL Değerlendirmesi](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 gün |
| [Surçok yönlü hub](surface-hubs.md) |Windows |&#8226; | | | | |varış noktasında |
| [System Center Operations Manager Değerlendirmesi (Önizleme)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | yedi gün |
| [Güncelleştirme yönetimi](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |bir güncelleştirme yüklendikten sonra günde en az 2 kez ve 15 dakika |
| [Yükseltme Hazırlığı](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 gün |
| [VMware İzleme (kullanım dışı)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 dakika |
| [Wire Data 2.0 (Önizleme)](wire-data.md) |Windows (2012 R2/8,1 veya üzeri) |&#8226; |&#8226; | | | | 1 dakika |




## <a name="next-steps"></a>Sonraki adımlar
* [İzleme çözümlerini yüklemeyi ve kullanmayı](solutions.md)öğrenin.
* İzleme çözümleri tarafından toplanan verileri analiz etmek için [sorgular oluşturmayı](../log-query/log-query-overview.md) öğrenin.
