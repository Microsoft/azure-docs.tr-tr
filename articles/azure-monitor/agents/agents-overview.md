---
title: Azure izleme aracılarına genel bakış | Microsoft Docs
description: Bu makalede, Azure 'da veya karma ortamda barındırılan sanal makineleri izlemeyi destekleyen Azure aracılarına yönelik ayrıntılı bir genel bakış sunulmaktadır.
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 4d1dd358c03d051be4be5733d9e729d1d7ef5b0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026181"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Izleyici aracılarına genel bakış

Sanal makineler ve diğer işlem kaynakları, Konuk işletim sistemi ve iş yüklerinin performansını ve kullanılabilirliğini ölçmek için gereken izleme verilerini toplamak üzere bir aracı gerektirir. Bu makalede, Azure Izleyici tarafından kullanılan aracılar açıklanmakta ve belirli ortamınızın gereksinimlerini karşılamanız gerektiğini belirlemenize yardımcı olur.

> [!NOTE]
> Azure Izleyici 'nin şu anda birden çok Aracısı vardır çünkü Azure Izleyici 'nin son birleştirme ve Log Analytics. Özelliklerinde örtüşme olabilir, ancak her birinin benzersiz özellikleri vardır. Gereksinimlerinize bağlı olarak, makinelerinizdeki bir veya daha fazla aracıya ihtiyacınız olabilir. 

Belirli bir makine için tek bir aracıda tamamen karşılanmayan belirli bir gereksinim kümesine sahip olabilirsiniz. Örneğin, Azure tanılama uzantısı gerektiren ölçüm uyarılarını kullanmak isteyebilir, ancak Ayrıca Log Analytics Aracısı ve bağımlılık Aracısı gerektiren VM öngörülerinin işlevselliğinden yararlanmak isteyebilirsiniz. Bu gibi durumlarda, birden çok aracı kullanabilirsiniz ve bu, her birinden işlevselliği gerektiren müşteriler için yaygın bir senaryodur.

## <a name="summary-of-agents"></a>Aracıların Özeti

Aşağıdaki tablolarda, Windows ve Linux için Azure Izleyici aracılarıyla ilgili hızlı bir karşılaştırma sağlanmaktadır. Aşağıdaki bölümde, her biri hakkında daha fazla ayrıntı sağlanır.

> [!NOTE]
> Azure Izleyici Aracısı Şu anda sınırlı özellikleri olan önizlemededir. Bu tablo güncelleştirilecektir 

### <a name="windows-agents"></a>Windows aracıları

| | Azure Izleyici Aracısı (Önizleme) | Tanılama<br>Uzantı (WAD) | Log Analytics<br>aracı | Bağımlılık<br>aracı |
|:---|:---|:---|:---|:---|
| **Desteklenen ortamlar** | Azure<br>Diğer bulut (Azure ARC)<br>Şirket içi (Azure ARC)  | Azure | Azure<br>Diğer bulut<br>Şirket içi | Azure<br>Diğer bulut<br>Şirket içi | 
| **Aracı gereksinimleri**  | Yok | Yok | Yok | Log Analytics Aracısı gerektirir |
| **Toplanan veriler** | Olay Günlükleri<br>Performans | Olay Günlükleri<br>ETW olayları<br>Performans<br>Dosya tabanlı Günlükler<br>IIS günlükleri<br>.NET uygulama günlükleri<br>Kilitlenme bilgi dökümleri<br>Aracı tanılama günlükleri | Olay Günlükleri<br>Performans<br>Dosya tabanlı Günlükler<br>IIS günlükleri<br>Öngörüler ve çözümler<br>Diğer hizmetler | İşlem bağımlılıkları<br>Ağ bağlantısı ölçümleri |
| **Gönderilen veriler** | Azure İzleyici Günlükleri<br>Azure İzleyici Ölçümleri | Azure Depolama<br>Azure İzleyici Ölçümleri<br>Olay Hub'ı | Azure İzleyici Günlükleri | Azure İzleyici Günlükleri<br>(Log Analytics Aracısı aracılığıyla) |
| **Hizmetler ve**<br>**özelliklerinde**<br>**Destek** | Log Analytics<br>Ölçüm gezgini | Ölçüm gezgini | VM öngörüleri<br>Log Analytics<br>Azure Otomasyonu<br>Azure Güvenlik Merkezi<br>Azure Sentinel | VM öngörüleri<br>Hizmet Eşlemesi |

### <a name="linux-agents"></a>Linux aracıları

| | Azure Izleyici Aracısı (Önizleme) | Tanılama<br>Uzantı (LAD) | Telegraf<br>aracı | Log Analytics<br>aracı | Bağımlılık<br>aracı |
|:---|:---|:---|:---|:---|:---|
| **Desteklenen ortamlar** | Azure<br>Diğer bulut (Azure ARC)<br>Şirket içi (Azure ARC) | Azure | Azure<br>Diğer bulut<br>Şirket içi | Azure<br>Diğer bulut<br>Şirket içi | Azure<br>Diğer bulut<br>Şirket içi |
| **Aracı gereksinimleri**  | Yok | Yok | Yok | Yok | Log Analytics Aracısı gerektirir |
| **Toplanan veriler** | Syslog<br>Performans | Syslog<br>Performans | Performans | Syslog<br>Performans| İşlem bağımlılıkları<br>Ağ bağlantısı ölçümleri |
| **Gönderilen veriler** | Azure İzleyici Günlükleri<br>Azure İzleyici Ölçümleri | Azure Depolama<br>Olay Hub'ı | Azure İzleyici Ölçümleri | Azure İzleyici Günlükleri | Azure İzleyici Günlükleri<br>(Log Analytics Aracısı aracılığıyla) |
| **Hizmetler ve**<br>**özelliklerinde**<br>**Destek** | Log Analytics<br>Ölçüm gezgini | | Ölçüm gezgini | VM öngörüleri<br>Log Analytics<br>Azure Otomasyonu<br>Azure Güvenlik Merkezi<br>Azure Sentinel | VM öngörüleri<br>Hizmet Eşlemesi |


## <a name="azure-monitor-agent-preview"></a>Azure Izleyici Aracısı (Önizleme)

[Azure izleyici Aracısı](azure-monitor-agent-overview.md) Şu anda önizleme aşamasındadır ve hem Windows hem de Linux makineleri için Log Analytics Aracısı ve telegraf aracısının yerini alacak. Hem Azure Izleyici günlüklerine hem de Azure Izleyici ölçümlerine veri gönderebilir ve her bir aracı için veri toplamayı ve hedeflerini yapılandırmaya yönelik daha ölçeklenebilir bir yöntem sağlayan [veri toplama kuralları 'nı (DCR)](data-collection-rule-overview.md) kullanır.

Şunları yapmanız gerekirse Azure Izleyici aracısını kullanın:

- Azure 'daki herhangi bir makineden, diğer bulutlarda veya şirket içinde Konuk günlükleri ve ölçümleri toplayın. (Azure 'un dışındaki makineler için gereken[Azure Arc etkin sunucuları](../../azure-arc/servers/overview.md) .) 
- Azure izleyici günlüklerine veri gönderme ve Azure izleyici ile analiz için Azure izleyici ölçümleri. 
- Arşivlenmek üzere verileri Azure depolama 'ya gönderin.
- [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md)kullanarak üçüncü taraf araçlara veri gönderme.
- [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md) veya [Azure Sentinel](../../sentinel/overview.md)kullanarak makinelerinizin güvenliğini yönetin. (Önizlemede kullanılamaz.)

Azure Izleyici aracısının sınırlamaları şunlardır:

- Şu anda genel önizlemede. Genel önizleme sırasındaki sınırlamaların listesi için bkz. [geçerli sınırlamalar](azure-monitor-agent-overview.md#current-limitations) .

## <a name="log-analytics-agent"></a>Log Analytics aracısı

[Log Analytics Aracısı](./log-analytics-agent.md) , Azure 'da, diğer bulut sağlayıcılarında ve şirket içi makinelerde bulunan konuk işletim sisteminden ve sanal makinelerin iş yüklerinden izleme verilerini toplar. Log Analytics çalışma alanına veri gönderir. Log Analytics Aracısı System Center Operations Manager tarafından kullanılan aracıdır ve aynı anda yönetim grubunuz ve Azure Izleyiciyle iletişim kurmak için çok giriş aracı bilgisayarları kullanabilirsiniz. Bu aracı, Azure Izleyici 'deki belirli Öngörüler ve Azure 'daki diğer hizmetler için de gereklidir.

> [!NOTE]
> Windows için Log Analytics Aracısı genellikle Microsoft Monitoring Agent (MMA) olarak adlandırılır. Linux için Log Analytics Aracısı genellikle OMS Aracısı olarak adlandırılır.

Şunları yapmanız gerekirse Log Analytics aracısını kullanın:

* Azure sanal makinelerinden veya Azure dışında barındırılan karma makinelerde bulunan günlükleri ve performans verilerini toplayın.
* [Günlük sorguları](../logs/log-query-overview.md)gibi [Azure izleyici günlükleri](../logs/data-platform-logs.md) tarafından desteklenen özelliklerden yararlanmak için bir Log Analytics çalışma alanına veri gönderin.
* Makinelerinizi ölçekli olarak izlemenizi ve diğer kaynaklardaki ve dış süreçlerdeki işlemleri ve bağımlılıklarını izlemenizi sağlayan [VM öngörülerini](../vm/vminsights-overview.md) kullanın.  
* [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md) veya [Azure Sentinel](../../sentinel/overview.md)kullanarak makinelerinizin güvenliğini yönetin.
* Azure ve Azure dışı makinelerinizde kapsamlı yönetim sağlamak için [Azure otomasyonu güncelleştirme yönetimi](../../automation/update-management/overview.md), [Azure Otomasyonu durum yapılandırması](../../automation/automation-dsc-overview.md)veya [Azure Otomasyonu değişiklik izleme ve envanterini](../../automation/change-tracking/overview.md) kullanın.
* Belirli bir hizmeti veya uygulamayı izlemek için farklı [çözümler](../monitor-reference.md#insights-and-core-solutions) kullanın.

Log Analytics aracısının sınırlamaları şunlardır:

- Azure Izleyici ölçümleri, Azure depolama veya Azure Event Hubs veri gönderilemez.
- Bireysel aracılar için benzersiz izleme tanımları yapılandırmaya zorlaştırıyor.
- Her bir sanal makinenin benzersiz bir yapılandırması olduğundan ölçeğe göre yönetimi zor.

## <a name="azure-diagnostics-extension"></a>Azure tanılama uzantısı

[Azure tanılama uzantısı](./diagnostics-extension-overview.md) , Konuk işletim sisteminden ve Azure sanal makinelerinin ve diğer işlem kaynaklarının iş yüklerinden izleme verilerini toplar. Birincil olarak Azure Storage 'da veri toplar, ancak Azure Izleyici ölçümleri ve Azure Event Hubs gibi diğer hedeflere veri göndermek için de veri havuzları tanımlamanızı sağlar.

Şunları yapmanız gerekirse Azure tanılama uzantısı 'nı kullanın:

- Arşivleme için verileri Azure depolama 'ya gönderin veya [Azure Depolama Gezgini](../../vs-azure-tools-storage-manage-with-storage-explorer.md)gibi araçlarla çözümleyin.
- Verileri, [Ölçüm Gezgini](../essentials/metrics-getting-started.md) ile analiz etmek ve neredeyse gerçek zamanlı [ölçüm uyarıları](../alerts/alerts-metric-overview.md) ve [Otomatik ölçeklendirme](../autoscale/autoscale-overview.md) (yalnızca Windows) gibi özelliklerden yararlanmak için [Azure izleyici ölçümlerine](../essentials/data-platform-metrics.md) veri gönderme.
- [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md)kullanarak üçüncü taraf araçlara veri gönderme.
- VM önyükleme sorunlarını araştırmak için [önyükleme tanılamayı](/troubleshoot/azure/virtual-machines/boot-diagnostics) toplayın.

Azure tanılama uzantısı sınırlamaları şunlardır:

- Yalnızca Azure kaynaklarıyla kullanılabilir.
- Azure Izleyici günlüklerine veri gönderme yeteneği sınırlıdır.

## <a name="telegraf-agent"></a>Telegraf Aracısı

[Etkileyen Azure Data telegraf Aracısı](../essentials/collect-custom-metrics-linux-telegraf.md) , Linux bilgisayarlardan Azure Izleyici ölçümlerine performans verileri toplamak için kullanılır.

Şunları yapmanız gerekirse telegraf aracısını kullanın:

* Verileri, [Ölçüm Gezgini](../essentials/metrics-getting-started.md) ile analiz etmek ve neredeyse gerçek zamanlı [ölçüm uyarıları](../alerts/alerts-metric-overview.md) ve [Otomatik ölçeklendirme](../autoscale/autoscale-overview.md) (yalnızca Linux) gibi özelliklerden yararlanmak için [Azure izleyici ölçümlerine](../essentials/data-platform-metrics.md) veri gönderme.

## <a name="dependency-agent"></a>Bağımlılık aracısı

Bağımlılık Aracısı, makine ve dış işlem bağımlılıkları üzerinde çalışan işlemler hakkında bulunan verileri toplar. 

Şunları yapmanız gerekirse bağımlılık aracısını kullanın:

* Harita özelliği [VM öngörülerini](../vm/vminsights-overview.md) veya [hizmet eşlemesi](../vm/service-map.md) çözümünü kullanın.

Bağımlılık aracısını kullanırken aşağıdakileri göz önünde bulundurun:

- Bağımlılık Aracısı, Log Analytics aracısının aynı makinede yüklü olmasını gerektirir.
- Linux bilgisayarlarda, Azure tanılama uzantısından önce Log Analytics aracısının yüklenmesi gerekir.

## <a name="virtual-machine-extensions"></a>Sanal makine uzantıları

[Windows](../../virtual-machines/extensions/oms-windows.md) ve [Linux](../../virtual-machines/extensions/oms-linux.md) Için Log Analytics uzantısı, Azure sanal makinelerine Log Analytics Aracısı 'nı yükler. [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) ve [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) için Azure Izleyici bağımlılığı uzantısı, Azure sanal makinelerine bağımlılık aracısını yükler. Bunlar yukarıda açıklanan aracılardır, ancak bunları [sanal makine uzantıları](../../virtual-machines/extensions/overview.md)aracılığıyla yönetmenizi sağlar. Mümkün olan her durumda aracıları yüklemek ve yönetmek için uzantıları kullanmanız gerekir.

Karma makinelerde, Log Analytics ve Azure Izleyici bağımlılığı VM uzantılarını dağıtmak için [Azure Arc etkin sunucularını](../../azure-arc/servers/manage-vm-extensions.md) kullanın.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki tablolarda, Azure Izleyici aracıları tarafından desteklenen işletim sistemleri listelenmektedir. Her bir aracının, benzersiz hususlar ve yükleme işlemi için belgelerine bakın. Desteklenen işletim sistemleri için bkz. telegraf belgeleri. Tüm işletim sistemleri x64 olarak kabul edilir. x86, herhangi bir işletim sistemi için desteklenmez.

### <a name="windows"></a>Windows

| İşletim sistemi | Azure İzleyici aracısı | Log Analytics aracısı | Bağımlılık aracısı | Tanılama uzantısı | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(çoklu oturum dahil) ve Pro<br>(Yalnızca sunucu senaryoları)  | X | X | X | X |
| Windows 8 Enterprise ve Pro<br>(Yalnızca sunucu senaryoları)  |   | X | X |   |
| Windows 7 SP1<br>(Yalnızca sunucu senaryoları)                 |   | X | X |   |

### <a name="linux"></a>Linux

| İşletim sistemi | Azure Izleyici Aracısı <sup>1</sup> | Log Analytics Aracısı <sup>1</sup> | Bağımlılık aracısı | Tanılama uzantısı <sup>2</sup>| 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017,09                                        |   | X |   |   |
| CentOS Linux 8                                              | X <sup>3</sup> | X | X |   |
| CentOS Linux 7                                              | X | X | X | X |
| CentOS Linux 6                                              |   | X |   |   |
| CentOS Linux 6.5 +                                           |   | X | X | X |
| De, 10 <sup>1</sup>                                      | X |   |   |   |
| Debian 9                                                    | X | X | x | X |
| Debian 8                                                    |   | X | X |   |
| Deyi 7                                                    |   |   |   | X |
| OpenSUSE 13.1 +                                              |   |   |   | X |
| Oracle Linux 8                                              | X <sup>3</sup> | X |   |   |
| Oracle Linux 7                                              | X | X |   | X |
| Oracle Linux 6                                              |   | X |   |   |
| Oracle Linux 6.4 +                                           |   | X |   | X |
| Red Hat Enterprise Linux Server 8                           | X <sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 7                           | X | X | X | X |
| Red Hat Enterprise Linux Server 6                           |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7 +                        |   | X | X | X |
| SUSE Linux Enterprise Server 15,2                           | X <sup>3</sup> |   |   |   |
| SUSE Linux Enterprise Server 15,1                           | X <sup>3</sup> | X |   |   |
| SUSE Linux Enterprise Server 15                             | X | X | X |   |
| SUSE Linux Enterprise Server 12                             | X | X | X | X |
| Ubuntu 20,04 LTS                                            | X | X | X |   |
| Ubuntu 18.04 LTS                                            | X | X | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X | X |
| Ubuntu 14,04 LTS                                            |   | X |   | X |

<sup>1</sup> , bir Python (2 veya 3) makinede yüklü olmasını gerektirir.

<sup>2</sup> makinede Python 2 ' nin yüklü olmasını gerektirir.

<sup>3</sup> bilinen bir sorun Syslog olaylarını topluyor. Şu anda yalnızca performans verileri destekleniyor.
#### <a name="dependency-agent-linux-kernel-support"></a>Bağımlılık Aracısı Linux çekirdek desteği

Bağımlılık Aracısı çekirdek düzeyinde çalıştığından, destek de çekirdek sürümüne bağımlıdır. Aşağıdaki tabloda, bağımlılık aracısına yönelik birincil ve küçük Linux işletim sistemi sürümü ve desteklenen çekirdek sürümleri listelenmektedir.

| Dağıtım | İşletim sistemi sürümü | Çekirdek sürümü |
|:---|:---|:---|
|  Red Hat Linux 8   | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* EL8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7,5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6,10    | 2.6.32-754 |
|                    | 6,9     | 2.6.32-696  |
| CentOS Linux 8     | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* EL8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
| CentOS Linux 6     | 6,10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6,9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 20,04   | 5.4\* |
|                    | 18,04   | 5.3.0-1020<br>5,0 (Azure tarafından ayarlanmış çekirdek dahil)<br>4,18 *<br> 4,15* |
|                    | 16.04.3 | 4,15.\* |
|                    | 16,04   | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |
| SUSE Linux 12 Enterprise Server | 15     | 4.12.14-150\*
|                                 | 12 SP4 | 4,12. * (Azure tarafından ayarlanmış çekirdek dahil) |
|                                 | 12 SP3 | 4,4. * |
|                                 | 12 SP2 | 4,4. * |
| Debian                          | 9      | 4,9  | 

## <a name="next-steps"></a>Sonraki adımlar

Aracılardan her biri hakkında daha fazla ayrıntı için aşağıdaki adresten yararlanın:

- [Log Analytics aracısına genel bakış](./log-analytics-agent.md)
- [Azure Tanılama uzantısına genel bakış](./diagnostics-extension-overview.md)
- [Etkileyen bir Linux VM için özel ölçümler toplama telegraf Aracısı](../essentials/collect-custom-metrics-linux-telegraf.md)