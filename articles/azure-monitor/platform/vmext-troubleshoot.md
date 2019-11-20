---
title: Azure Izleyici 'de Azure Log Analytics VM Uzantısı sorunlarını giderme | Microsoft Docs
description: Windows ve Linux Azure VM 'Leri için Log Analytics VM uzantısıyla ilgili en yaygın sorunların belirtilerini, nedenlerini ve çözümlemesini tanıtın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 06/06/2019
ms.openlocfilehash: 9ec0d5036632c575415a7de19b9ea35eb2a28118
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931923"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics VM Uzantısı sorunlarını giderme
Bu makale, Microsoft Azure üzerinde çalışan Windows ve Linux sanal makineleri için Log Analytics VM uzantısıyla karşılaşabileceğiniz sorun giderme konusunda yardım sağlar ve bunları çözmek için olası çözümler önerir.

Uzantının durumunu doğrulamak için Azure portal aşağıdaki adımları gerçekleştirin.

1. [Azure portal](https://portal.azure.com) oturum açın.
2. Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **sanal makineler**yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Sanal makineleri**seçin.
3. Sanal makineler listenizde, bulun ve seçin.
3. Sanal makinede, **Uzantılar**' a tıklayın.
4. Listeden Log Analytics uzantısının etkin olup olmadığını denetleyin.  Linux için aracı, **Omsagentforlinux** ve Windows için listelenir. Aracı, **microsoftmonitoringagent**olarak listelenir.

   ![VM uzantısı görünümü](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Ayrıntıları görüntülemek için uzantıya tıklayın. 

   ![VM Uzantısı ayrıntıları](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM Uzantısı sorunlarını giderme

*Microsoft Monitoring Agent* VM Uzantısı yükleme veya raporlama değilse, sorunu gidermek için aşağıdaki adımları uygulayabilirsiniz.

1. [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)' deki adımları kullanarak Azure VM Aracısı 'nın yüklenip düzgün çalışıp çalışmadığını denetleyin.
   * VM Aracısı günlük dosyasını da gözden geçirebilirsiniz `C:\WindowsAzure\logs\WaAppAgent.log`
   * Günlük yoksa, VM Aracısı yüklenmez.
   * [Azure VM aracısını yükler](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` Microsoft Monitoring Agent VM Uzantısı günlük dosyalarını gözden geçirin
3. Sanal makinenin PowerShell betikleri çalıştırabağlanabildiğinden emin olun
4. C:\Windows\temp üzerinde izinlerin değişmediğinden emin olun
5. Microsoft Monitoring Agent durumunu, sanal makinede yükseltilmiş bir PowerShell penceresine aşağıdakini yazarak görüntüleyin `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` Microsoft Monitoring Agent kurulum günlük dosyalarını gözden geçirin

Daha fazla bilgi için bkz. [Windows uzantılarında sorun giderme](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Linux VM Uzantısı sorunlarını giderme
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Linux VM Uzantısı *için Log Analytics Aracısı* yükleme veya raporlama değilse, sorunu gidermek için aşağıdaki adımları uygulayabilirsiniz.

1. Uzantı durumu *bilinmiyor* Ise, VM Aracısı günlük dosyasını Inceleyerek Azure VM aracısının yüklenip düzgün çalışıp çalışmadığını denetleyin `/var/log/waagent.log`
   * Günlük yoksa, VM Aracısı yüklenmez.
   * [Linux VM 'lerine Azure VM Aracısı 'nı yükler](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Diğer sağlıksız durumlar için, `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` ve `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log` Linux sanal makine uzantısı günlük dosyaları için Log Analytics aracısını gözden geçirin.
3. Uzantı durumu sağlıklı ise, ancak veriler karşıya yüklenemediğinden, `/var/opt/microsoft/omsagent/log/omsagent.log` içindeki Linux günlük dosyaları için Log Analytics aracısını gözden geçirin

Daha fazla bilgi için bkz. [Linux uzantılarında sorun giderme](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure dışındaki bilgisayarlarda barındırılan Linux için Log Analytics aracısıyla ilgili ek sorun giderme kılavuzu için bkz. [azure Log Analytics Linux Aracısı sorunlarını giderme](agent-linux-troubleshoot.md).  
