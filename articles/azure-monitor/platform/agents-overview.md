---
title: Azure izleme aracılarına genel bakış | Microsoft Docs
description: Bu makalede, Azure 'da veya karma ortamda barındırılan sanal makineleri izlemeyi destekleyen Azure aracılarına yönelik ayrıntılı bir genel bakış sunulmaktadır.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150036"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Azure Izleyici aracılarına genel bakış 
Sanal makineler gibi işlem kaynakları, performansını ve kullanılabilirliğini yalnızca [diğer bulut kaynakları](../insights/monitor-azure-resource.md)gibi izlemek için veri oluşturur. İşlem kaynaklarında Ayrıca, izlenmesi gereken bir konuk işletim sistemi ve iş yükleri de vardır. Bu izleme verilerinin kaynağın içinden toplanması bir aracı gerektirir. Bu makalede, Azure Izleyici tarafından kullanılan aracılar açıklanmakta ve belirli ortamınızın gereksinimlerini karşılamanız gerektiğini belirlemenize yardımcı olur.

## <a name="summary-of-agents"></a>Aracıların Özeti

> [!NOTE]
> Azure Izleyici 'nin şu anda birden çok Aracısı vardır çünkü Azure Izleyici 'nin son birleştirme ve Log Analytics. Her iki aracı da bazı özellikleri paylaşır, ancak diğer özellikler belirli bir aracıya özeldir. Gereksinimlerinize bağlı olarak, aracılardan biri veya her ikisi de gerekebilir. 

Azure Izleyici, her birinin belirli bir işlevi sağladığı üç aracı vardır. Gereksinimlerinize bağlı olarak, sanal makinelerinize ve diğer işlem kaynaklarına tek bir aracı veya birden çok yükleyebilirsiniz.

* [Azure Tanılama uzantısı](#azure-diagnostic-extension)
* [Log Analytics Aracısı](#log-analytics-agent)
* [Bağımlılık Aracısı](#dependency-agent)

Aşağıdaki tabloda, farklı aracıların hızlı karşılaştırması sağlanmıştır. Bu makalenin geri kalanında Ayrıntılar için bkz.

| | Azure tanılama uzantısı | Log Analytics Aracısı | Bağımlılık aracısı |
|:---|:---|:---|:---|
| Desteklenen ortamlar | Azure | Azure<br>Diğer bulut<br>Şirket içi | Azure<br>Diğer bulut<br>Şirket içi |
| İşletim sistemleri | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Aracı bağımlılıkları  | None | None | Log Analytics Aracısı gerektirir |
| Toplanan veriler | Olay Günlükleri<br>ETW olayları<br>Syslog<br>Performans<br>IIS günlükleri<br>.NET uygulama izleme çıkış günlükleri<br>Kilitlenme bilgi dökümleri | Olay Günlükleri<br>Syslog<br>Performans<br>IIS günlükleri<br>Özel günlükler<br>Çözümlerdeki veriler | İşlem ayrıntıları ve bağımlılıklar<br>Ağ bağlantısı ölçümleri |
| Gönderilen veriler | Azure Storage<br>Azure Izleyici ölçümleri<br>Olay Hub'ı | Azure İzleyici Günlükleri | Azure İzleyici Günlükleri |



## <a name="azure-diagnostic-extension"></a>Azure tanılama uzantısı
[Azure tanılama uzantısı](../../azure-monitor/platform/diagnostics-extension-overview.md) , Konuk işletim sisteminden ve Azure işlem kaynaklarının iş yüklerinden izleme verilerini toplar. Birincil olarak Azure Storage 'da veri toplar. Azure Izleyici 'yi, verileri depolamadan bir Log Analytics çalışma alanına kopyalayacak şekilde yapılandırabilirsiniz. Ayrıca, Azure Izleyici ölçümlerine konuk performans verileri toplayabilirsiniz.

Azure tanılama uzantısı, genellikle Microsoft Azure tanılama (WAD) veya Linux Azure tanılama (LAD) uzantısı olarak adlandırılır.


### <a name="scenarios-supported"></a>Desteklenen senaryolar

Azure Tanılama uzantısı tarafından desteklenen senaryolar şunlardır:

* Azure işlem kaynaklarından günlükleri ve performans verilerini toplayın.
* Konuk işletim sisteminden Azure depolama 'ya Günlükler ve performans verileri arşivleyin.
* [Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md)gibi bir araç kullanarak izleme verilerini depolamada görüntüleyin.
* Neredeyse gerçek zamanlı [ölçüm uyarıları](../../azure-monitor/platform/alerts-metric-overview.md) ve [Otomatik ölçeklendirme](autoscale-overview.md)gibi [Azure izleyici ölçümleri](data-platform-metrics.md) tarafından desteklenen özelliklerden yararlanmak için bir ölçüm veritabanında performans verilerini toplayın. 
* [Günlük sorguları](../log-query/log-query-overview.md)gibi [Azure izleyici günlükleri](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) tarafından desteklenen özelliklerden yararlanmak için [depolama 'dan bir Log Analytics çalışma alanına](azure-storage-iis-table.md) izleme verileri toplayın.
* [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)kullanarak, üçüncü taraf araçlara izleme verileri gönderin.
* [Önyükleme tanılamaları](../../virtual-machines/troubleshooting/boot-diagnostics.md)ile VM önyüklemesi sorunlarını araştırın.
* Diğer uygulama izlemeyle tümleştirilecek [APPLICATION INSIGHTS](diagnostics-extension-to-application-insights.md) sanal makinenizde çalışan uygulamalardan verileri kopyalayın.

## <a name="log-analytics-agent"></a>Log Analytics Aracısı
[Log Analytics Aracısı](log-analytics-agent.md) , Azure 'da, diğer bulut sağlayıcılarında ve Şirket içindeki sanal makinelerin Konuk işletim sisteminden ve iş yüklerinden izleme verilerini toplar. Log Analytics çalışma alanına veri toplar.

Log Analytics Aracısı, System Center Operations Manager tarafından kullanılan aracıdır ve yönetim grubunuz ve Azure Izleyici ile aynı anda iletişim kurmak üzere çok ana aracı bilgisayarlardır. Bu aracı, Azure Izleyici 'deki belirli çözümler için de gereklidir.

Windows için Log Analytics Aracısı genellikle Microsoft Yönetim Aracısı (MMA) olarak adlandırılır. Linux için Log Analytics Aracısı genellikle OMS Aracısı olarak adlandırılır.


### <a name="scenarios-supported"></a>Desteklenen senaryolar

Log Analytics Aracısı tarafından desteklenen senaryolar şunlardır:

* Azure, diğer bulut sağlayıcıları ve şirket içi sanal makinelerden gelen günlükleri ve performans verilerini toplayın. 
* [Günlük sorguları](../log-query/log-query-overview.md)gibi [Azure izleyici günlükleri](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) tarafından desteklenen özelliklerden yararlanmak için bir Log Analytics çalışma alanına izleme verileri toplayın.
* [VM'ler için Azure izleyici](../insights/vminsights-overview.md), [kapsayıcılar için Azure izleyici](../insights/container-insights-overview.md)vb. gibi Azure izleyici izleme çözümlerini kullanın.  
* Log Analytics aracısını gerektiren [Azure Sentinel](../../sentinel/overview.md) 'i kullanarak sanal makinelerinizin güvenliğini yönetin.
* Log Analytics aracısını gerektiren [Azure Güvenlik Merkezi](../../security-center/security-center-intro.md) 'ni kullanarak sanal makinelerinizin güvenliğini yönetin.
* Azure [Otomasyonu](../../automation/automation-intro.md) özelliklerini kullanarak Azure VM 'lerinizi yaşam döngülerinde kapsamlı bir şekilde yönetimini sunun.  Log Analytics aracısını gerektiren bu özelliklerin örnekleri şunlardır:
  * İşletim sistemi güncelleştirmelerinin [Azure Otomasyonu güncelleştirme yönetimi](../../automation/automation-update-management.md) .
  * Tutarlı yapılandırma durumunu korumak için [Azure Otomasyonu durum yapılandırması](../../automation/automation-dsc-overview.md) .
  * [Azure otomasyonu değişiklik izleme ve envanteriyle](../../automation/change-tracking.md)yapılandırma değişikliklerini izleyin.

## <a name="dependency-agent"></a>Bağımlılık aracısı
Bağımlılık Aracısı, sanal makine ve dış işlem bağımlılıkları üzerinde çalışan işlemler hakkında bulunan verileri toplar. Bu aracı [hizmet eşlemesi](../insights/service-map.md) ve eşleme özelliği [VM'ler için Azure izleyici](../insights/vminsights-overview.md)için gereklidir. Bağımlılık Aracısı Log Analytics aracısına gerek duyar ve verileri Azure Izleyici 'de bir Log Analytics çalışma alanına yazar.


## <a name="using-multiple-agents"></a>Birden çok aracı kullanma
Belirli bir sanal makine için Azure tanılama uzantısı 'nı veya Log Analytics aracısını kullanmak üzere belirli gereksinimlere sahip olabilirsiniz. Örneğin, Azure tanılama uzantısı gerektiren ölçüm uyarılarını kullanmak isteyebilirsiniz. Ancak, bağımlılık Aracısı ve Log Analytics Aracısı gerektiren VM'ler için Azure İzleyici Map özelliğini de kullanmak isteyebilirsiniz. Bu durumda, birden çok aracı kullanabilirsiniz ve bu, her birinden işlevselliği gerektiren müşteriler için yaygın bir senaryodur.

### <a name="considerations"></a>Dikkat edilmesi gerekenler

- Bağımlılık Aracısı, Log Analytics aracısının aynı sanal makinede yüklü olmasını gerektirir.
- Linux VM 'lerinde, Azure tanılama uzantısından önce Log Analytics aracısının yüklenmesi gerekir.


## <a name="next-steps"></a>Sonraki adımlar

- Aracıyı Azure 'da, veri merkezinizde veya diğer bulut ortamınızda barındırılan makinelere dağıtmak için gereksinimleri ve desteklenen yöntemleri gözden geçirmek üzere [Log Analytics aracısına genel bakış](../../azure-monitor/platform/log-analytics-agent.md) konusuna bakın.

