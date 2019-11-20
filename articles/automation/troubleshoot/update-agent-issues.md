---
title: Azure Güncelleştirme Yönetimi Windows Agent denetim sonuçlarını anlayın
description: Güncelleştirme Yönetimi aracısında sorunları nasıl giderebileceğinizi öğrenin.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: d3099498c3abea428e04d94ca0fcd553e6a0fec6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886409"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>Güncelleştirme Yönetimi Windows Agent denetim sonuçlarını anlayın

Güncelleştirme Yönetimi ' de makinenizin **Ready** olarak görünmemesinin pek çok nedeni olabilir. Güncelleştirme Yönetimi, temeldeki sorunu tespit etmek için karma çalışan aracısının sistem durumunu kontrol edebilirsiniz. Bu makalede, [çevrimdışı senaryodaki](#troubleshoot-offline)Azure Portal ve Azure dışı makinelerden Azure makinelerinde sorun gidericinin nasıl çalıştırılacağı açıklanmaktadır.

Aşağıdaki liste, bir makinenin içinde bulunabileceği üç hazırlık durumlarından oluşan bir durum olabilir:

* **Ready** -Güncelleştirme Aracısı dağıtıldı ve en son 1 saatten daha önce görüldü.
* **Bağlantısı kesik** -Güncelleştirme Aracısı dağıtıldı ve en son 1 saat önce görüldü.
* **Yapılandırılmadı** -Güncelleştirme Aracısı bulunamadı veya ekleme bitmedi.

> [!NOTE]
> Azure portal gösterdiği ve makinenin geçerli durumu arasında hafif bir gecikme olabilir.

## <a name="start-the-troubleshooter"></a>Sorun gidericiyi Başlat

Azure makinelerinde, portalda **Güncelleştirme Aracısı hazırlığı** sütununda **sorun gider** bağlantısına tıkladığınızda **Güncelleştirme Aracısı sorunlarını gider** sayfası başlatılır. Azure dışı makineler için bağlantı sizi bu makaleye getirir. Azure olmayan bir makinede sorun gidermeye yönelik [çevrimdışı yönergelere](#troubleshoot-offline) bakın.

![Sanal makinelerin yönetim listesini güncelleştir](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Bir aracının durumunu denetlemek için VM 'nin çalışıyor olması gerekir. VM çalışmıyorsa **VM 'Yi Başlat** düğmesi görüntülenir.

**Güncelleştirme Aracısı sorunlarını giderme** sayfasında, sorun gidericiyi başlatmak Için **denetimleri Çalıştır** ' ı seçin. Sorun giderici, aracı bağımlılıklarını doğrulamak üzere makinede bir betik çalıştırmak için [Run komutunu](../../virtual-machines/windows/run-command.md) kullanır. Sorun giderici tamamlandığında, denetimlerin sonucunu döndürür.

![Güncelleştirme Aracısı sayfasında sorun giderme](../media/update-agent-issues/troubleshoot-page.png)

Sonuçlar, varsa sayfada gösterilir. Denetimler bölümünde her bir denetimin içerdiği özellikler gösterilir.

![Güncelleştirme Aracısı denetimlerinde sorun giderme](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Önkoşul denetimleri

### <a name="operating-system"></a>İşletim sistemi

İşletim sistemi denetimi, karma Runbook Worker 'ın şu işletim sistemlerinden birini çalıştırıp çalıştırmadığını doğrular:

|İşletim sistemi  |Notlar  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | Yalnızca güncelleştirme değerlendirmelerini destekler.         |
|Windows Server 2008 R2 SP1 ve üzeri |.NET Framework 4.6.1 veya üzeri gereklidir. ([.NET Framework indirin](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 5,1 gereklidir.  ([Windows Management Framework 5,1 'Yi indirin](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-461"></a>.NET 4.6.1 +

.NET Framework denetimi sistemin en az [.NET Framework 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981) yüklü olduğunu doğrular.

### <a name="wmf-51"></a>WMF 5,1

WMF Check, sistemin gerekli Windows Management Framework (WMF) sürümüne sahip olduğunu doğrular- [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1,2

Bu denetim, iletişimlerinizi şifrelemek için TLS 1,2 kullanıp kullanmayacağınızı belirler. TLS 1,0 artık platform tarafından desteklenmiyor. İstemcilerin Güncelleştirme Yönetimi ile iletişim kurmak için TLS 1,2 kullanmasını öneririz.

## <a name="connectivity-checks"></a>Bağlantı denetimleri

### <a name="registration-endpoint"></a>Kayıt uç noktası

Bu denetim aracının Aracı hizmetiyle düzgün şekilde iletişim kurup kuramayacağını belirler.

Proxy ve güvenlik duvarı yapılandırmalarının, karma Runbook Worker aracısının kayıt uç noktasıyla iletişim kurmasına izin verilmelidir. Açılacak adreslerin ve bağlantı noktalarının listesi için bkz. [karma çalışanlar Için ağ planlaması](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>İşlemler uç noktası

Bu denetim, aracının Iş çalışma zamanı veri hizmetiyle düzgün şekilde iletişim kurup kuramayacağını belirler.

Proxy ve güvenlik duvarı yapılandırmalarının, karma Runbook Worker aracısının Iş çalışma zamanı veri hizmetiyle iletişim kurmasına izin verilmelidir. Açılacak adreslerin ve bağlantı noktalarının listesi için bkz. [karma çalışanlar Için ağ planlaması](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>VM hizmeti durum denetimleri

### <a name="monitoring-agent-service-status"></a>İzleme Aracısı hizmet durumu

Bu denetim makinede `HealthService`, Microsoft Monitoring Agent çalışıp çalışmadığını belirler.

Hizmette sorun giderme hakkında daha fazla bilgi edinmek için [Microsoft Monitoring Agent çalışmıyor](hybrid-runbook-worker.md#mma-not-running)' a bakın.

Microsoft Monitoring Agent yeniden yüklemek için, bkz. [Microsoft Monitoring Agent yükleme ve yapılandırma](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Aracı hizmeti olaylarını izleme

Bu denetim, son 24 saat içinde makinede bulunan Azure Operations Manager günlüğünde herhangi bir `4502` olayının görünüp görünmeyeceğini belirler.

Bu olay hakkında daha fazla bilgi edinmek için bu olayla ilgili [sorun giderme kılavuzuna](hybrid-runbook-worker.md#event-4502) bakın.

## <a name="access-permissions-checks"></a>Erişim izinleri denetimleri

### <a name="machinekeys-folder-access"></a>MachineKeys klasörü erişimi

Şifreleme klasörü erişim denetimi, yerel sistem hesabının C:\programdata\microsoft\crypto\rsaa erişimi olup olmadığını belirler.

## <a name="troubleshoot-offline"></a>Çevrimdışı sorun giderme

Betiği yerel olarak çalıştırarak karma Runbook Worker 'daki sorun gidericiyi çevrimdışı olarak kullanabilirsiniz. PowerShell Galerisi, [sorun giderme-WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)komut dosyasına ulaşabilirsiniz. Betiği çalıştırmak için WMF 4,0 veya sonraki bir sürümü yüklemiş olmanız gerekir. PowerShell 'in en son sürümünü indirmek için bkz. [PowerShell 'in çeşitli sürümlerini yükleme](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

Bu Betiğin çıktısı aşağıdaki örneğe benzer şekilde görünür:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Sonraki adımlar

Karma runbook çalışanlarınız hakkında daha fazla sorunu gidermek için bkz. [karma runbook çalışanları sorunlarını giderme](hybrid-runbook-worker.md).

