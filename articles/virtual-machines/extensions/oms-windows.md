---
title: Windows için Azure Izleyici sanal makine uzantısı
description: Log Analytics aracısını bir sanal makine uzantısı kullanarak Windows sanal makinesine dağıtın.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2019
ms.author: akjosh
ms.openlocfilehash: c9fd62e57d131fb21e657c53914f9cd5349107ec
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073671"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>Windows için Azure Izleyici sanal makine uzantısı

Azure Izleyici günlükleri, bulut ve şirket içi varlıklar arasında izleme özellikleri sağlar. Windows için Log Analytics Aracısı sanal makine uzantısı Microsoft tarafından yayımlanır ve desteklenir. Uzantı, Azure sanal makinelerinde Log Analytics aracısını yükler ve sanal makinelerin mevcut bir Log Analytics çalışma alanına kaydeder. Bu belge, Windows için Azure Izleyici sanal makine uzantısı için Desteklenen platformların, yapılandırmaların ve dağıtım seçeneklerinin ayrıntılarını Izler.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Desteklenen Windows işletim sistemleri hakkında daha fazla bilgi için [Log Analytics aracısına genel bakış](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) makalesine bakın.

### <a name="agent-and-vm-extension-version"></a>Aracı ve VM uzantısı sürümü
Aşağıdaki tabloda, Microsoft Azure Izleyici VM uzantısının sürümü ve her sürüm için Log Analytics aracı paketi eşleştirmesi verilmiştir. 

| Log Analytics Windows Agent paketi sürümü | Azure Izleyici Windows VM Uzantısı sürümü | Yayınlanma Tarihi | Sürüm Notları |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18011 | 1.0.18011 | Temmuz 2019 | <ul><li> Küçük hata düzeltmeleri ve sabitleştirme iyileştirmeleri </li><li> Artan MaxExpressionDepth 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Haziran 2019 | <ul><li> Küçük hata düzeltmeleri ve sabitleştirme iyileştirmeleri </li><li> Ara sunucu bağlantısı kurulurken varsayılan kimlik bilgilerini devre dışı bırakma özelliği eklendi (WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH için destek) </li></ul>|
| 10.19.13515 | 1.0.13515 | Mart 2019 | <ul><li>Küçük sabitleştirme düzeltmeleri </li></ul> |
| 10.19.10006 | yok | Dec 2018 | <ul><li> Küçük sabitleştirme düzeltmeleri </li></ul> | 
| 8.0.11136 | yok | Eyl 2018 |  <ul><li> VM taşıma üzerinde kaynak KIMLIĞI değişikliğini algılama desteği eklendi </li><li> Uzantı dışı yüklemesi kullanılırken raporlama kaynak KIMLIĞI desteği eklendi </li></ul>| 
| 8.0.11103 | yok |  Nisan 2018 | |
| 8.0.11081 | 1.0.11081 | Kas 2017 | | 
| 8.0.11072 | 1.0.11072 | Eyl 2017 | |
| 8.0.11049 | 1.0.11049 | Şub 2017 | |

### <a name="azure-security-center"></a>Azure Güvenlik Merkezi

Azure Güvenlik Merkezi, Log Analytics aracısını otomatik olarak sağlar ve Azure aboneliğinin varsayılan Log Analytics çalışma alanına bağlar. Azure Güvenlik Merkezi kullanıyorsanız, bu belgedeki adımları çalıştırmayın. Bunun yapılması, yapılandırılan çalışma alanının üzerine yazar ve Azure Güvenlik Merkezi ile bağlantıyı keser.

### <a name="internet-connectivity"></a>İnternet bağlantısı
Windows için Log Analytics Aracısı uzantısı, hedef sanal makinenin Internet 'e bağlı olmasını gerektirir. 

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON Log Analytics aracı uzantısının şemasını gösterir. Uzantı, hedef Log Analytics çalışma alanından çalışma alanı KIMLIĞI ve çalışma alanı anahtarı gerektirir. Bunlar, Azure portal çalışma alanı ayarlarından bulunabilir. Çalışma alanı anahtarı hassas verileri olarak değerlendirilip olduğundan, bir korumalı ayarı yapılandırmasında depolanması gerekir. Azure VM uzantısının korumalı ayarı veriler şifrelenir ve yalnızca hedef sanal makinede şifresi. Unutmayın **Workspaceıd** ve **workspaceKey** büyük küçük harfe duyarlıdır.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (örn.)* | 6f680a37-00c6-41C7-a93f-1437e3462574 |
| workspaceKey (örn.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* çalışma alanı kimliği, Log Analytics API 'sindeki ConsumerID olarak adlandırılır.

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Önceki bölümde ayrıntılı JSON şeması, bir Azure Resource Manager şablon dağıtımı sırasında Log Analytics Aracısı uzantısını çalıştırmak için bir Azure Resource Manager şablonunda kullanılabilir. [Azure hızlı başlangıç galerisinde](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)log ANALYTICS Agent VM uzantısını içeren bir örnek şablon bulunabilir. 

>[!NOTE]
>Bu şablon, aracıyı birden çok çalışma alanına rapor verecek şekilde yapılandırmak istediğinizde birden fazla çalışma alanı KIMLIĞI ve çalışma alanı anahtarı belirtilmesini desteklemez. Aracıyı birden çok çalışma alanına rapor verecek şekilde yapılandırmak için, bkz. [çalışma alanı ekleme veya kaldırma](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

Bir sanal makine uzantısı için JSON, sanal makine kaynağının içinde iç içe veya Kaynak Yöneticisi JSON şablonunun kök veya üst düzeyine yerleştirilmiş olabilir. JSON yerleştirmesi, kaynak adının ve türün değerini etkiler. Daha fazla bilgi için [ayarlamak için alt kaynakları ad ve tür](../../azure-resource-manager/child-resource-name-type.md). 

Aşağıdaki örnek, Azure Izleyici uzantısının sanal makine kaynağının içinde iç içe olduğunu varsayar. İç içe uzantısı kaynak, JSON yerleştirildi `"resources": []` sanal makinenin nesne.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Uzantı JSON şablonu kökünde yerleştirilirken, kaynak adı üst sanal makineye bir başvuru içerir ve iç içe geçmiş yapılandırma türü yansıtır. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell dağıtımı

`Set-AzVMExtension` komutu, var olan bir sanal makineye Log Analytics Aracısı sanal makinesi uzantısını dağıtmak için kullanılabilir. Komutu çalıştırmadan önce, ortak ve özel yapılandırmaların bir PowerShell karma tablosunda depolanması gerekir. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal alabilir ve Azure PowerShell modülü kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için Azure PowerShell modülünü kullanarak aşağıdaki komutu çalıştırın.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Uzantı yürütme çıkışı aşağıdaki dizinde bulunan dosyalara kaydedilir:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).
