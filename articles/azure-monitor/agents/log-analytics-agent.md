---
title: Log Analytics aracısına genel bakış
description: Bu konu, verileri nasıl toplayacağınızı ve Azure 'da, şirket içinde veya başka bir bulut ortamında barındırılan bilgisayarların Log Analytics nasıl izleneceğini anlamanıza yardımcı olur.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 8b3b1d39766d556b820f21c1775210611b995d92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199896"
---
# <a name="log-analytics-agent-overview"></a>Log Analytics aracısına genel bakış

Azure Log Analytics Aracısı tüm bulutta, şirket içi makinelerde ve [System Center Operations Manager](/system-center/scom/) tarafından izlenen Windows ve Linux sanal makinelerinden telemetri toplar ve toplanan verileri Azure izleyici 'de Log Analytics çalışma alanınıza gönderir. Log Analytics Aracısı Ayrıca Azure Izleyici 'de [VM öngörüleri](../vm/vminsights-enable-overview.md), [Azure Güvenlik Merkezi](../../security-center/index.yml)ve [Azure Otomasyonu](../../automation/automation-intro.md)gibi diğer hizmetleri de destekler. Bu makalede Aracı, sistem ve ağ gereksinimleri ve dağıtım yöntemlerine ilişkin ayrıntılı bir genel bakış sunulmaktadır.

> [!NOTE]
> Ayrıca, Microsoft Monitoring Agent (MMA) olarak adlandırılan Log Analytics aracısını da görebilirsiniz.

## <a name="comparison-to-azure-diagnostics-extension"></a>Azure tanılama uzantısına kıyasla
Azure Izleyici 'de Azure [Tanılama uzantısı](./diagnostics-extension-overview.md) , Azure sanal makinelerinin Konuk işletim sisteminden izleme verilerini toplamak için de kullanılabilir. Gereksinimlerinize bağlı olarak ya da her ikisini de kullanmayı seçebilirsiniz. Azure izleyici aracılarının ayrıntılı bir karşılaştırması için bkz. [Azure izleyici aracılarına genel bakış](../agents/agents-overview.md) . 

Dikkate alınması gereken önemli farklılıklar şunlardır:

- Azure Tanılama uzantısı yalnızca Azure sanal makineler ile kullanılabilir. Log Analytics Aracısı, Azure 'da, diğer bulutlarda ve Şirket içindeki sanal makinelerle birlikte kullanılabilir.
- Azure Tanılama uzantısı, verileri Azure depolama 'ya, [Azure Izleme ölçümlerine](../essentials/data-platform-metrics.md) (yalnızca Windows) ve Event Hubs gönderir. Log Analytics Aracısı verileri [Azure Izleyici günlüklerine](../logs/data-platform-logs.md)gönderir.
- Log Analytics Aracısı, [çözümler](../monitor-reference.md#insights-and-core-solutions), [VM öngörüleri](../vm/vminsights-overview.md)ve [Azure Güvenlik Merkezi](../../security-center/index.yml)gibi diğer hizmetler için gereklidir.

## <a name="costs"></a>Maliyetler

Log Analytics Aracısı için maliyet yoktur, ancak alınan veriler için ücret ödemeniz gerekebilir. Log Analytics çalışma alanında toplanan verilerin fiyatlandırmasıyla ilgili ayrıntılı bilgi için [Azure Izleyici günlükleriyle kullanımı ve maliyetleri yönetin ' i](../logs/manage-cost-storage.md) işaretleyin.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

 Log Analytics Aracısı tarafından desteklenen Windows ve Linux işletim sistemi sürümlerinin bir listesi için bkz. [desteklenen işletim sistemleri](../agents/agents-overview.md#supported-operating-systems) . 

## <a name="data-collected"></a>Toplanan veriler

Aşağıdaki tabloda, tüm bağlı aracılardan toplanacak bir Log Analytics çalışma alanını yapılandırabileceğiniz veri türleri listelenmektedir. Diğer veri türlerini toplamak için Log Analytics aracısını kullanan Öngörüler, çözümler ve diğer çözümlerin listesi için bkz. [Azure izleyici tarafından Izlenen nedir?](../monitor-reference.md) .

| Veri Kaynağı | Description |
| --- | --- |
| [Windows olay günlükleri](../agents/data-sources-windows-events.md) | Windows olay günlüğü sistemine gönderilen bilgiler. |
| [Syslog](../agents/data-sources-syslog.md)                     | Linux olay günlüğü sistemine gönderilen bilgiler. |
| [Performans](../agents/data-sources-performance-counters.md)  | Sayısal değerler, işletim sistemi ve iş yüklerinin farklı yönlerinin performansını ölçerek. |
| [IIS günlükleri](../agents/data-sources-iis-logs.md)                 | Konuk işletim sisteminde çalışan IIS Web siteleri için kullanım bilgileri. |
| [Özel günlükler](../agents/data-sources-custom-logs.md)           | Hem Windows hem de Linux bilgisayarlardaki metin dosyalarından gelen olaylar. |

## <a name="data-destinations"></a>Veri hedefleri

Log Analytics Aracısı verileri Azure Izleyici 'de bir Log Analytics çalışma alanına gönderir. Birden çok çalışma alanına ve System Center Operations Manager yönetim grubuna veri göndermek için Windows aracısının birden çok bağlantısı olabilir. Linux Aracısı, bir çalışma alanı veya yönetim grubu olmak üzere yalnızca tek bir hedefe gönderebilir.

## <a name="other-services"></a>Diğer hizmetler

Linux ve Windows için aracı yalnızca Azure Izleyici 'ye bağlanmıyor. Azure Güvenlik Merkezi ve Azure Sentinel gibi diğer hizmetler aracıya ve bağlı Log Analytics çalışma alanına bağlıdır. Aracı Ayrıca karma Runbook Worker rolünü ve [değişiklik izleme](../../automation/change-tracking/overview.md), [güncelleştirme yönetimi](../../automation/update-management/overview.md)ve [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)gibi diğer hizmetleri barındırmak için Azure Otomasyonu 'nu destekler. Karma Runbook Worker rolü hakkında daha fazla bilgi için bkz. [Azure Otomasyonu karma Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Çalışma alanı ve yönetim grubu sınırlamaları

Bir aracıyı Operations Manager yönetim grubuna bağlama hakkında ayrıntılı bilgi için bkz. [Aracıyı yapılandırma Operations Manager yönetim grubuna rapor vermek](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) için.

* Windows aracıları, System Center Operations Manager bir yönetim grubuna bağlansalar bile, en fazla dört çalışma alanına bağlanabilir.
* Linux Aracısı çoklu barındırıltı desteklemez ve yalnızca tek bir çalışma alanına veya yönetim grubuna bağlanabilir.

## <a name="security-limitations"></a>Güvenlik sınırlamaları

* Windows ve Linux aracıları [fıps 140 standardını](/windows/security/threat-protection/fips-140-validation)destekler, ancak [diğer sağlamlaştırma türleri de desteklenmiyor olabilir](../agents/agent-linux.md#supported-linux-hardening).

## <a name="installation-options"></a>Yükleme seçenekleri

Log Analytics aracısını yüklemeye ve gereksinimlerinize bağlı olarak makinenizi Azure Izleyici 'ye bağlamaya yönelik birden çok yöntem vardır. Aşağıdaki bölümlerde, farklı türlerde sanal makineler için olası Yöntemler listelenmektedir.

> [!NOTE]
> Zaten yapılandırılmış Log Analytics aracısına sahip bir makine klonlamak desteklenmez. Aracı zaten bir çalışma alanıyla ilişkilendirilmişse bu, ' altın görüntüler ' için çalışmaz.

### <a name="azure-virtual-machine"></a>Azure sanal makine

- [VM öngörüleri](../vm/vminsights-enable-overview.md) , uygun ölçekte aracıları etkinleştiren birden çok yöntem sağlar. Bu, Log Analytics Aracısı ve bağımlılık aracısının yüklenmesini içerir. 
- [Azure Güvenlik Merkezi,](../../security-center/security-center-enable-data-collection.md) desteklenen tüm Azure vm 'lerinde Log Analytics aracısını ve güvenlik açıklarını ve tehditleri izlemek için bunu etkinleştirdiğinizde oluşturulan tüm yeni olanları temin edebilir.
- [Windows](../../virtual-machines/extensions/oms-windows.md) veya [LINUX](../../virtual-machines/extensions/oms-linux.md) için Log Analytics VM uzantısı, Azure Portal, Azure clı, Azure PowerShell veya Azure Resource Manager şablonuyla yüklenebilir.
- Tek tek Azure sanal makineleri için [Azure Portal el ile](../vm/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)yükleyebilirsiniz.

### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Şirket içinde veya başka bir bulutta Windows sanal makinesi

- Log Analytics VM uzantısını dağıtmak ve yönetmek için [Azure Arc etkin sunucularını](../../azure-arc/servers/overview.md) kullanın.
- Aracıyı komut satırından [el ile yükleyebilirsiniz](../agents/agent-windows.md) .
- Yüklemeyi [Azure Automation DSC](../agents/agent-windows.md#install-agent-using-dsc-in-azure-automation)ile otomatikleştirin.
- [Azure Stack ile kaynak yöneticisi şablonu](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) kullanma

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Şirket içinde veya başka bir bulutta Linux sanal makinesi

- Log Analytics VM uzantısını dağıtmak ve yönetmek için [Azure Arc etkin sunucularını](../../azure-arc/servers/overview.md) kullanın.
- GitHub 'da barındırılan bir sarmalayıcı betiği çağıran aracıyı [el ile yükleyebilirsiniz](../vm/quick-collect-linux-computer.md) .
- Toplanan verileri Windows bilgisayarları raporlama 'dan bir yönetim grubuna iletmek için [System Center Operations Manager](./om-agents.md) Azure izleyici ile tümleştirin.

## <a name="workspace-id-and-key"></a>Çalışma alanı KIMLIĞI ve anahtarı

Kullanılan yükleme yönteminden bağımsız olarak, aracının bağlanacağı Log Analytics çalışma alanı için çalışma alanı KIMLIĞI ve anahtarı gerekir. Azure portal **Log Analytics çalışma alanları** menüsünden çalışma alanını seçin. Ardından **Ayarlar** bölümünde **aracılar yönetimi** ' ni seçin. 

[![Çalışma alanı ayrıntıları](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1,2 Protokolü

Azure Izleyici günlüklerine aktarılan verilerin güvenliğini sağlamak için, aracıyı en az Aktarım Katmanı Güvenliği (TLS) 1,2 kullanacak şekilde yapılandırmanızı kesinlikle öneririz. TLS/Güvenli Yuva Katmanı (SSL) uygulamasının güvenlik açığı olduğu ve geriye dönük uyumlulukla hala çalışmaya devam eden daha eski sürümlerinin **kullanılması önerilmez**.  Daha fazla bilgi için [TLS 1,2 kullanarak verileri güvenli bir şekilde göndermeyi](../logs/data-security.md#sending-data-securely-using-tls-12)inceleyin. 

## <a name="network-requirements"></a>Ağ gereksinimleri

Linux ve Windows için aracı, TCP bağlantı noktası 443 üzerinden Azure Izleyici hizmetine giden iletişim kurar. Makine Internet üzerinden iletişim kurmak için bir güvenlik duvarı veya ara sunucu üzerinden bağlanıyorsa, gereken ağ yapılandırmasını anlamak için aşağıdaki gereksinimleri gözden geçirin. BT güvenlik ilkeleriniz ağdaki bilgisayarların Internet 'e bağlanmasına izin vermediğinden, bir [Log Analytics ağ geçidi](gateway.md) ayarlayabilir ve ardından aracıyı Azure izleyici 'ye ağ geçidine bağlanacak şekilde yapılandırabilirsiniz. Aracı daha sonra yapılandırma bilgilerini alabilir ve toplanan verileri gönderebilir.

![Log Analytics Aracısı iletişim diyagramı](./media/log-analytics-agent/log-analytics-agent-01.png)

Aşağıdaki tabloda, Linux ve Windows aracılarının Azure Izleyici günlükleriyle iletişim kurması için gereken proxy ve güvenlik duvarı yapılandırma bilgileri listelenmektedir.

### <a name="firewall-requirements"></a>Güvenlik duvarı gereksinimleri

|Aracı Kaynağı|Bağlantı noktaları |Yön |HTTPS denetlemesini atlama|
|------|---------|--------|--------|
|*.ods.opinsights.azure.com |Bağlantı noktası 443 |Giden|Yes |  
|*.oms.opinsights.azure.com |Bağlantı noktası 443 |Giden|Yes |  
|*.blob.core.windows.net |Bağlantı noktası 443 |Giden|Yes |
|*.azure-automation.net |Bağlantı noktası 443 |Giden|Yes |

Azure Kamu için gereken güvenlik duvarı bilgileri için bkz. [Azure Kamu Yönetimi](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Ortamınızdaki runbook 'ları veya yönetim özelliklerini kullanmak üzere otomasyon hizmetine bağlanmak ve kaydolmak için Azure Otomasyonu karma Runbook Worker kullanmayı planlıyorsanız, bağlantı noktası numarasına ve [ağınızı karma Runbook Worker Için yapılandırma](../../automation/automation-hybrid-runbook-worker.md#network-planning)bölümünde açıklanan URL 'lere erişimi olmalıdır.

### <a name="proxy-configuration"></a>Ara sunucu yapılandırması

Windows ve Linux Aracısı, HTTPS protokolünü kullanarak bir proxy sunucu aracılığıyla veya Azure Izleyici 'ye Log Analytics ağ geçidiyle iletişim kurmasını destekler.  Hem anonim hem de temel kimlik doğrulaması (Kullanıcı adı/parola) desteklenir.  Doğrudan hizmete bağlı olan Windows Aracısı için, proxy yapılandırması yükleme sırasında veya denetim masasından ya da PowerShell ile [dağıtımdan sonra](../agents/agent-manage.md#update-proxy-settings) belirtilir.  

Linux Aracısı için ara sunucu, yükleme sırasında veya proxy. conf yapılandırma dosyası değiştirilerek [yüklendikten sonra](../agents/agent-manage.md#update-proxy-settings) belirtilir. Linux Aracısı proxy yapılandırma değeri aşağıdaki sözdizimine sahiptir:

`[protocol://][user:password@]proxyhost[:port]`

|Özellik| Açıklama |
|--------|-------------|
|Protokol | https |
|kullanıcı | Proxy kimlik doğrulaması için isteğe bağlı Kullanıcı adı |
|password | Proxy kimlik doğrulaması için isteğe bağlı parola |
|proxyhost | Ara sunucu/Log Analytics ağ geçidinin adresi veya FQDN 'si |
|port | Ara sunucu/Log Analytics ağ geçidi için isteğe bağlı bağlantı noktası numarası |

Örnek: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Parolandaki "" gibi özel karakterler kullanırsanız \@ , değer yanlış ayrıştırılacağından bir ara sunucu bağlantı hatası alırsınız.  Bu sorunu geçici olarak çözmek için URL 'deki parolayı [Urlşifre çözme](https://www.urldecoder.org/)gibi bir araç kullanarak kodlayın.  

## <a name="next-steps"></a>Sonraki adımlar

* Windows veya Linux sisteminizden veri toplamak için kullanılabilir veri kaynaklarını anlamak üzere [veri kaynaklarını](../agents/agent-data-sources.md) gözden geçirin. 
* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../logs/log-query-overview.md) hakkında bilgi edinin. 
* Azure Izleyici 'ye işlevsellik ekleyen ve ayrıca Log Analytics çalışma alanına veri toplayacağınız [çözümleri izleme](../insights/solutions.md) hakkında bilgi edinin.
