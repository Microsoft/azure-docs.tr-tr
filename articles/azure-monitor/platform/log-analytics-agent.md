---
title: Azure Log Analytics Agent ile günlük verilerini toplama | Microsoft Docs
description: Bu konu, verileri nasıl toplayacağınızı ve Azure 'da, şirket içinde veya başka bir bulut ortamında barındırılan bilgisayarların Log Analytics nasıl izleneceğini anlamanıza yardımcı olur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 8070abad675acc69f5b1da232b60179078adbc57
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932236"
---
# <a name="collect-log-data-with-the-log-analytics-agent"></a>Log Analytics aracısıyla günlük verilerini toplama

Daha önce Microsoft Monitoring Agent (MMA) veya OMS Linux Aracısı olarak adlandırılan Azure Log Analytics Aracısı, şirket içi makinelerde kapsamlı yönetim için geliştirilmiştir, [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/)tarafından izlenen bilgisayarlar ve herhangi bir buluttaki sanal makineler. Windows ve Linux aracıları bir Azure Izleyicisine ekler ve Log Analytics çalışma alanınızdaki farklı kaynaklardan toplanan günlük verilerini, ayrıca bir izleme çözümünde tanımlanan benzersiz Günlükler veya ölçümleri depolar. 

Bu makalede Aracı, sistem ve ağ gereksinimleri ve farklı dağıtım yöntemlerine ilişkin ayrıntılı bir genel bakış sunulmaktadır.

## <a name="overview"></a>Genel Bakış

![Log Analytics Aracısı iletişim diyagramı](./media/log-analytics-agent/log-analytics-agent-01.png)

Toplanan verileri analiz etmek ve üzerinde işlem yapmadan önce, Azure Izleyici hizmetine veri göndermek istediğiniz tüm makineler için aracıları yüklemeniz ve bağlamanız gerekir. Windows ve Linux için Azure Log Analytics VM uzantısını ve Azure Otomasyonu 'nda kurulum, komut satırı veya Istenen durum yapılandırması (DSC) kullanarak bir karma ortamdaki makineler için Azure sanal makinelerinize aracılar yükleyebilirsiniz. 

Linux ve Windows için aracı, TCP bağlantı noktası 443 üzerinden Azure Izleyici hizmetine giden iletişim kurar ve makine Internet üzerinden iletişim kurmak için bir güvenlik duvarı veya ara sunucu üzerinden bağlanıyorsa, ağ yapılandırmasını anlamak için aşağıdaki gereksinimleri gözden geçirin Gerekli. BT güvenlik ilkeleriniz ağdaki bilgisayarların Internet 'e bağlanmasına izin vermediğinden, bir [Log Analytics ağ geçidi](gateway.md) ayarlayabilir ve ardından aracıyı ağ geçidinden Azure izleyici günlüklerine bağlanacak şekilde yapılandırabilirsiniz. Aracı daha sonra yapılandırma bilgilerini alabilir ve çalışma alanınızda etkinleştirdiğiniz veri toplama kurallarına ve izleme çözümlerine bağlı olarak toplanan verileri gönderebilir. 

Veri toplamak için Log Analytics aracılarını kullanırken, aracı dağıtımınızı planlamak için aşağıdakileri anlamanız gerekir:

* Windows aracılarından veri toplamak için, [her aracıyı bir veya daha fazla çalışma alanına rapor verecek şekilde yapılandırabilirsiniz](agent-windows.md), bu da bir System Center Operations Manager yönetim grubuna bildirimde bulunur. Windows Aracısı en fazla dört çalışma alanı rapor edebilir.
* Linux Aracısı çoklu barındırıltı desteklemez ve yalnızca tek bir çalışma alanına rapor verebilir.
* Windows Aracısı [fıps 140 standardını](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation)destekler, ancak Linux Aracısı tarafından desteklenmez.  

System Center Operations Manager 2012 R2 veya sonraki bir sürümünü kullanıyorsanız:

* Her bir Operations Manager yönetim grubu [yalnızca bir çalışma alanına bağlanabilir](om-agents.md).
* Bir yönetim grubuna rapor veren Linux bilgisayarların doğrudan bir Log Analytics çalışma alanına rapor verecek şekilde yapılandırılması gerekir. Linux bilgisayarlarınız zaten bir çalışma alanına doğrudan raporlama yaptıysanız ve bunları Operations Manager izlemek istiyorsanız, [bir Operations Manager yönetim grubuna raporlamak](agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group)için aşağıdaki adımları izleyin.
* Log Analytics Windows aracısını Windows bilgisayarına yükleyebilir ve hem bir çalışma alanıyla hem de bir çalışma alanı ile tümleşik Operations Manager, farklı bir çalışma alanı için rapor verebilirsiniz.

Linux ve Windows için aracı yalnızca Azure Izleyici 'ye bağlanmıyor, karma Runbook Worker rolünü ve [değişiklik izleme](../../automation/change-tracking.md), [güncelleştirme yönetimi](../../automation/automation-update-management.md)ve [Azure Güvenlik Merkezi gibi diğer hizmetleri barındırmak için Azure Otomasyonu 'nu da destekler ](../../security-center/security-center-intro.md). Karma Runbook Worker rolü hakkında daha fazla bilgi için bkz. [Azure Otomasyonu karma Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="supported-windows-operating-systems"></a>Desteklenen Windows işletim sistemleri

Windows işletim sisteminin aşağıdaki sürümleri resmi olarak Windows Agent için desteklenir:

* Windows Server 2019
* Windows Server 2008 SP2 (x64), 2008 R2, 2012, 2012 R2, 2016, sürüm 1709 ve 1803
* Windows 7 SP1, Windows 8 Enterprise ve Pro ve Windows 10 Enterprise ve Pro

>[!NOTE]
>Windows için Log Analytics Aracısı, sunucu izleme senaryolarını destekleyecek şekilde tasarlandıkça, sunucu işletim sistemi için yapılandırılmış ve iyileştirilmiş iş yüklerini desteklemek üzere Windows istemcisini çalıştırabiliriz. Aracı Windows istemcisini destekler, ancak izleme çözümlerimiz açıkça belirtilmedikçe istemci izleme senaryolarına odaklanmaz.

## <a name="supported-linux-operating-systems"></a>Desteklenen Linux işletim sistemleri

Bu bölüm desteklenen Linux dağıtımları hakkında ayrıntılı bilgi sağlar.

2018 Ağustos 'Tan sonra yayınlanan sürümlerle başlayarak, destek modelimiz için aşağıdaki değişiklikleri yapıyoruz:  

* İstemci değil yalnızca sunucu sürümleri desteklenir.  
* Azure Linux tarafından onaylanan yeni [kaldırmalar](../../virtual-machines/linux/endorsed-distros.md) sürümleri her zaman desteklenmektedir.  
* Listelenen her ana sürüm için tüm küçük yayınlar desteklenir.
* Üreticisinin destek sonu tarihini geçen sürümler desteklenmez.  
* Yeni bir AMı sürümü desteklenmez.  
* Yalnızca SSL 1. x çalıştıran sürümler varsayılan olarak desteklenir.

>[!NOTE]
>Şu anda desteklenmeyen ve destek modelimize hizalanmayan bir veya daha fazla sürümü kullanıyorsanız, bu depoyu çatalla, Microsoft destek 'in aracılı aracı sürümleriyle ilgili yardım sağlamayamayacak olduğunu bildiren bu depoyu çatallandırmanızı öneririz.

* Amazon Linux 2017,09 (x64)
* CentOS Linux 6 (x86/x64) ve 7 (x64)  
* Oracle Linux 6 ve 7 (x86/x64) 
* Red Hat Enterprise Linux Server 6 (x86/x64) ve 7 (x64)
* De, GNU/Linux 8 ve 9 (x86/x64)
* Ubuntu 14,04 LTS (x86/x64), 16,04 LTS (x86/x64) ve 18,04 LTS (x64)
* SUSE Linux Enterprise Server 12 (x64) ve 15 (x64)

>[!NOTE]
>OpenSSL 1.1.0 yalnızca x86_x64 platformlarında desteklenir (64-bit) ve 1. x ' ten önceki OpenSSL, hiçbir platformda desteklenmez.
>

### <a name="agent-prerequisites"></a>Aracı önkoşulları

Aşağıdaki tabloda, aracının yükleneceği desteklenen Linux destekleri için gereken paketler vurgulanmıştır.

|Gerekli paket |Açıklama |En düşük sürüm |
|-----------------|------------|----------------|
|GLIBC |    GNU C Kitaplığı | 2,5-12 
|OpenSSL    | OpenSSL kitaplıkları | 1.0. x veya 1.1. x |
|Kıvr | Web istemcisini kıvır | 7.15.5 |
|Python-ctypes | | 
|KULLANıCıSı | Takılabilir kimlik doğrulama modülleri | | 

>[!NOTE]
>Syslog iletilerini toplamak için rsyslog veya Syslog-ng gerekir. Red Hat Enterprise Linux, CentOS ve Oracle Linux sürümünün (sysklog) sürüm 5 ' te bulunan varsayılan Syslog Daemon, Syslog olay koleksiyonu için desteklenmez. Bu dağıtımların bu sürümünden Syslog verileri toplamak için rsyslog arka plan programı yüklenmeli ve sysklog ' ı değiştirecek şekilde yapılandırılmalıdır.

## <a name="tls-12-protocol"></a>TLS 1,2 Protokolü

Azure Izleyici günlüklerine geçiş sırasındaki verilerin güvenliğini sağlamak için aracıyı en az Aktarım Katmanı Güvenliği (TLS) 1,2 kullanacak şekilde yapılandırmanızı önemle öneririz. TLS/Güvenli Yuva Katmanı (SSL) uygulamasının güvenlik açığı olduğu ve geriye dönük uyumlulukla hala çalışmaya devam eden daha eski sürümlerinin **kullanılması önerilmez**.  Daha fazla bilgi için [TLS 1,2 kullanarak verileri güvenli bir şekilde göndermeyi](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)inceleyin. 

## <a name="network-firewall-requirements"></a>Ağ güvenlik duvarı gereksinimleri

Aşağıdaki bilgiler, Linux ve Windows aracısının Azure Izleyici günlükleriyle iletişim kurması için gereken proxy ve güvenlik duvarı yapılandırma bilgilerini listeler.  

|Aracı Kaynağı|Bağlantı Noktaları |Yön |HTTPS denetlemesini atlama|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Bağlantı noktası 443 |Giden|Yes |  
|*.oms.opinsights.azure.com |Bağlantı noktası 443 |Giden|Yes |  
|*.blob.core.windows.net |Bağlantı noktası 443 |Giden|Yes |  
|*.azure-automation.net |Bağlantı noktası 443 |Giden|Yes |  

Azure Kamu için gereken güvenlik duvarı bilgileri için bkz. [Azure Kamu Yönetimi](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Ortamınızdaki runbook 'ları kullanmak üzere otomasyon hizmetine bağlanmak ve bu hizmete kaydolmak için Azure Otomasyonu karma Runbook Worker kullanmayı planlıyorsanız, bağlantı noktası numarasına ve [ağınızı karma runbook Için yapılandırma bölümünde açıklanan URL 'lere erişimi olmalıdır Çalışan](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Windows ve Linux Aracısı, HTTPS protokolünü kullanarak bir proxy sunucu aracılığıyla veya Azure Izleyici 'ye Log Analytics ağ geçidiyle iletişim kurmasını destekler.  Hem anonim hem de temel kimlik doğrulaması (Kullanıcı adı/parola) desteklenir.  Doğrudan hizmete bağlı olan Windows Aracısı için, proxy yapılandırması yükleme sırasında veya denetim masasından ya da PowerShell ile [dağıtımdan sonra](agent-manage.md#update-proxy-settings) belirtilir.  

Linux Aracısı için ara sunucu, yükleme sırasında veya proxy. conf yapılandırma dosyası değiştirilerek [yüklendikten sonra](agent-manage.md#update-proxy-settings) belirtilir.  Linux Aracısı proxy yapılandırma değeri aşağıdaki sözdizimine sahiptir:

`[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
> Proxy sunucunuz kimlik doğrulaması yapmanızı gerektirmiyorsa, Linux Aracısı yine de sahte bir kullanıcı/parola sağlanması gerekir. Bu, herhangi bir Kullanıcı adı veya parola olabilir.

|Özellik| Açıklama |
|--------|-------------|
|Protokol | https |
|Kullanıcısını | Proxy kimlik doğrulaması için isteğe bağlı Kullanıcı adı |
|password | Proxy kimlik doğrulaması için isteğe bağlı parola |
|proxyhost | Ara sunucu/Log Analytics ağ geçidinin adresi veya FQDN 'si |
|port | Ara sunucu/Log Analytics ağ geçidi için isteğe bağlı bağlantı noktası numarası |

Örneğin, `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Parolandaki "\@" gibi özel karakterler kullanırsanız, değer yanlış ayrıştırılacağından bir ara sunucu bağlantı hatası alırsınız.  Bu sorunu geçici olarak çözmek için URL 'deki parolayı [Urlşifre çözme](https://www.urldecoder.org/)gibi bir araç kullanarak kodlayın.  

## <a name="install-and-configure-agent"></a>Aracıyı yükleyip yapılandırın

Azure aboneliğinizdeki veya karma ortamınızdaki makineleri doğrudan Azure Izleyici günlükleriyle bağlamak, gereksinimlerinize bağlı olarak farklı yöntemler kullanılarak gerçekleştirilebilir. Aşağıdaki tabloda, her bir yöntemi, kuruluşunuzda en iyi şekilde çalışacak şekilde belirlenir.

|Kaynak | Yöntem | Açıklama|
|-------|-------------|-------------|
|Azure VM| -Azure CLı kullanarak veya bir Azure Resource Manager şablonuyla [Windows](../../virtual-machines/extensions/oms-windows.md) veya [LINUX](../../virtual-machines/extensions/oms-linux.md) için Log Analytics VM Uzantısı<br>- [Azure Portal el ile](../../azure-monitor/learn/quick-collect-azurevm.md?toc=/azure/azure-monitor/toc.json)<br>- [Azure Güvenlik Merkezi otomatik sağlama](../../security-center/security-center-enable-data-collection.md)| -Uzantı Log Analytics aracısını Azure sanal makinelerine yükleyip mevcut bir Azure Izleyici çalışma alanına kaydeder.<br>-Azure Güvenlik Merkezi, desteklenen tüm Azure VM 'lerinde Log Analytics aracısını ve güvenlik açıklarını ve tehditleri izlemek için bu uygulamayı etkinleştirdiğinizde oluşturulan tüm yeni olanları temin edebilir. Etkinleştirilirse, yüklü bir aracı olmayan yeni veya mevcut VM 'ler temin edilir.|
| Hibrit Windows bilgisayarı|- [el ile yüklemesi](agent-windows.md)<br>- [Azure Automation DSC](agent-windows.md#install-the-agent-using-dsc-in-azure-automation)<br>- [Kaynak Yöneticisi şablon Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) |Microsoft Monitoring Agent 'ı komut satırından veya Azure Automation DSC, [System Center Configuration Manager](https://docs.microsoft.com/sccm/apps/deploy-use/deploy-applications)gibi otomatikleştirilmiş bir yöntem ya da Microsoft Azure Stack dağıttıysanız Azure Resource Manager şablonuyla birlikte kullanarak yükleyebilirsiniz. Zi.| 
| Hibrit Linux bilgisayarı| [El ile yüklemesi](../../azure-monitor/learn/quick-collect-linux-computer.md)|GitHub 'da barındırılan bir sarmalayıcı betiği çağıran Linux için aracıyı yükler. | 
| System Center Operations Manager|[Operations Manager Log Analytics ile tümleştirin](../../azure-monitor/platform/om-agents.md) | Toplanan verileri Windows bilgisayarlardan bir yönetim grubuna iletmek için Operations Manager ile Azure Izleyici günlükleri arasındaki tümleştirmeyi yapılandırın.|  

## <a name="next-steps"></a>Sonraki adımlar

* Windows veya Linux sisteminizden veri toplamak için kullanılabilir veri kaynaklarını anlamak üzere [veri kaynaklarını](../../azure-monitor/platform/agent-data-sources.md) gözden geçirin. 

* Veri kaynaklarından ve çözümlerinden toplanan verileri analiz etmek için [günlük sorguları](../../azure-monitor/log-query/log-query-overview.md) hakkında bilgi edinin. 

* Azure Izleyici 'ye işlevsellik ekleyen ve ayrıca Log Analytics çalışma alanına veri toplayacağınız [çözümleri izleme](../../azure-monitor/insights/solutions.md) hakkında bilgi edinin.
