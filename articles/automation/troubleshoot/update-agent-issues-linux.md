---
title: Azure Automation 'da Linux Güncelleştirme Aracısı sorunlarını giderme
description: Bu makalede, Güncelleştirme Yönetimi 'de Linux Windows Update Aracısı ile ilgili sorunların nasıl giderileceği ve çözüleceği açıklanır.
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: da7c0ea670b4c4201930ce5d0f01e7bd9d9835e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581037"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Linux Update aracı sorunlarını giderme

Güncelleştirme Yönetimi ' de makinenizin (sağlıklı) olarak görünmesinin pek çok nedeni olabilir. Temeldeki sorunu tespit etmek için bir Linux karma Runbook Worker aracısının sistem durumunu kontrol edebilirsiniz. Bir makineye yönelik üç hazırlık durumu aşağıda verilmiştir:

* Hazırlanıyor: karma Runbook Worker dağıtıldı ve en son bir saatten önce görüldü.
* Bağlantısı kesik: karma runbook çalışanı dağıtıldı ve en son bir saat önce görüldü.
* Yapılandırılmadı: karma Runbook Worker bulunamadı veya dağıtımı bitmedi.

> [!NOTE]
> Azure portal gösterdiği ve makinenin geçerli durumu arasında hafif bir gecikme olabilir.

Bu makalede, [çevrimdışı senaryodaki](#troubleshoot-offline)Azure Portal ve Azure dışı makinelerden Azure makinelerinde sorun gidericinin nasıl çalıştırılacağı açıklanmaktadır.

> [!NOTE]
> Sorun giderici betiği, bir proxy sunucusu yapılandırılmışsa trafiği Şu anda yönlendirmez.

## <a name="start-the-troubleshooter"></a>Sorun gidericiyi Başlat

Azure makineler için portalda **Güncelleştirme Aracısı hazırlığı** sütununda **sorun gider** bağlantısını seçin ve güncelleştirme Aracısı sorunlarını gider sayfasını açın. Azure dışı makineler için bağlantı sizi bu makaleye getirir. Azure olmayan bir makinede sorun gidermek için, "çevrimdışı sorun giderme" bölümündeki yönergelere bakın.

![VM listesi sayfası](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Denetimler VM 'nin çalışıyor olmasını gerektirir. VM çalışmıyorsa **VM 'Yi başlatın** .

Güncelleştirme Aracısı sorunlarını giderme sayfasında, sorun gidericiyi başlatmak için **denetimleri Çalıştır** ' ı seçin. Sorun giderici, bağımlılıkları doğrulamak üzere makinede bir betiği çalıştırmak için [Run komutunu](../../virtual-machines/linux/run-command.md) kullanır. Sorun giderici tamamlandığında, denetimlerin sonucunu döndürür.

![Sorun giderme sayfası](../media/update-agent-issues-linux/troubleshoot-page.png)

Denetimler tamamlandığında, sonuçlar pencerede döndürülür. Denetim bölümleri, her bir denetim için arama yapılan bilgiler sağlar.

![Aracı denetimleri sayfasını Güncelleştir](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Önkoşul denetimleri

### <a name="operating-system"></a>İşletim sistemi

İşletim sistemi denetimi, karma Runbook Worker 'ın aşağıdaki işletim sistemlerinden birini çalıştırıp çalıştırmadığını doğrular.

|İşletim sistemi  |Notlar  |
|---------|---------|
|CentOS 6 (x86/x64) ve 7 (x64)      | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır. Sınıflandırma tabanlı düzeltme eki uygulama, güvenlik verilerini döndürmek için ' yıum ' gerektirir ve bu da CentOS kutudan çıkar.         |
|Red Hat Enterprise 6 (x86/x64) ve 7 (x64)     | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.        |
|SUSE Linux Enterprise Server 11 (x86/x64) ve 12 (x64)     | Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.        |
|Ubuntu 14,04 LTS, 16,04 LTS ve 18,04 LTS (x86/x64)      |Linux aracılarının bir güncelleştirme havuzuna erişimi olmalıdır.         |

## <a name="monitoring-agent-service-health-checks"></a>İzleme Aracısı hizmeti durum denetimleri

### <a name="log-analytics-agent"></a>Log Analytics aracısı

Bu denetim, Linux için Log Analytics aracısının yüklü olmasını sağlar. Nasıl yükleneceğine ilişkin yönergeler için bkz. [Linux için aracıyı yüklemek](../../azure-monitor/vm/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Log Analytics Aracısı durumu

Bu denetim, Linux için Log Analytics aracısının çalışıyor olmasını sağlar. Aracı çalışmıyorsa, yeniden başlatmayı denemek için aşağıdaki komutu çalıştırabilirsiniz. Aracı sorunlarını giderme hakkında daha fazla bilgi için bkz. [Linux-karma Runbook Worker sorunlarını giderme](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Birden çok giriş

Bu denetim, aracının birden çok çalışma alanına raporlama olup olmadığını belirler. Güncelleştirme Yönetimi çoklu barındırılcıya desteklemez.

### <a name="hybrid-runbook-worker"></a>Karma Runbook Çalışanı

Bu denetim, Linux için Log Analytics aracısının karma Runbook Worker paketine sahip olup olmadığını doğrular. Güncelleştirme Yönetimi çalışması için bu paket gereklidir. Daha fazla bilgi edinmek için bkz. [Linux için Log Analytics Aracısı çalışmıyor](hybrid-runbook-worker.md#oms-agent-not-running).

Güncelleştirme Yönetimi, karma Runbook Worker paketlerini işlemler uç noktasından indirir. Bu nedenle, karma Runbook Worker çalışmıyorsa ve [işlemler uç noktası](#operations-endpoint) denetimi başarısız olursa, güncelleştirme başarısız olabilir.

### <a name="hybrid-runbook-worker-status"></a>Karma Runbook Worker durumu

Bu denetim, karma Runbook Worker 'ın makinede çalıştığından emin olmanızı sağlar. Karma Runbook Worker doğru çalışıyorsa aşağıdaki örnekteki işlemlerin mevcut olması gerekir.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Bağlantı denetimleri

### <a name="general-internet-connectivity"></a>Genel internet bağlantısı

Bu denetim, makinenin internet erişimi olduğundan emin olmanızı sağlar.

### <a name="registration-endpoint"></a>Kayıt uç noktası

Bu denetim, karma Runbook Worker 'ın Log Analytics çalışma alanında Azure Otomasyonu ile düzgün şekilde iletişim kurabildiğini belirler.

Proxy ve güvenlik duvarı yapılandırmalarının, karma Runbook Worker aracısının kayıt uç noktasıyla iletişim kurmasına izin verilmelidir. Açılacak adreslerin ve bağlantı noktalarının listesi için bkz. [ağ planlaması](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>İşlemler uç noktası

Bu denetim, Log Analytics aracısının Iş çalışma zamanı veri hizmetiyle düzgün şekilde iletişim kurabildiğini belirler.

Proxy ve güvenlik duvarı yapılandırmalarının, karma Runbook Worker aracısının Iş çalışma zamanı veri hizmetiyle iletişim kurmasına izin verilmelidir. Açılacak adreslerin ve bağlantı noktalarının listesi için bkz. [ağ planlaması](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Log Analytics uç noktası 1

Bu denetim, makinenizin Log Analytics Aracısı tarafından gereken uç noktalara erişimi olduğunu doğrular.

### <a name="log-analytics-endpoint-2"></a>Log Analytics uç noktası 2

Bu denetim, makinenizin Log Analytics Aracısı tarafından gereken uç noktalara erişimi olduğunu doğrular.

### <a name="log-analytics-endpoint-3"></a>Log Analytics uç noktası 3

Bu denetim, makinenizin Log Analytics Aracısı tarafından gereken uç noktalara erişimi olduğunu doğrular.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Çevrimdışı sorun giderme

Betiği yerel olarak çalıştırarak bir karma runbook çalışanında sorun gidericiyi çevrimdışı olarak kullanabilirsiniz. Python betiği [UM_Linux_Troubleshooter_Offline. Kopyala](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py), GitHub ' da bulunabilir. Aşağıdaki örnekte, bu betiğin çıktısının bir örneği gösterilmektedir:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Sonraki adımlar

[Karma Runbook Worker sorunlarını giderin](hybrid-runbook-worker.md).
