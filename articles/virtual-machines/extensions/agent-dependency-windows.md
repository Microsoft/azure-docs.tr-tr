---
title: Windows için Azure Izleyici bağımlılığı sanal makine uzantısı
description: Azure Izleyici bağımlılık aracısını bir sanal makine uzantısı kullanarak Windows sanal makinesine dağıtın.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 858aba2a45a6b8cf133e6f94bac45e721535ab25
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073259"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Windows için Azure Izleyici bağımlılığı sanal makine uzantısı

Vm'leri Haritası özelliği için Azure İzleyici verilerini Microsoft Dependency Aracıdan alır. Windows için Azure VM bağımlılık Aracısı sanal makine uzantısı, Microsoft tarafından yayımlanır ve desteklenir. Uzantı, Azure sanal makinelerine bağımlılık Aracısı 'nı yüklüyor. Bu belgede, Windows için Azure VM bağımlılık Aracısı sanal makine uzantısı için desteklenen platformlar, konfigürasyonlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır.

## <a name="operating-system"></a>İşletim sistemi

Windows için Azure VM bağımlılık Aracısı uzantısı, VM'ler için Azure İzleyici dağıtım makalesinin [desteklenen işletim sistemleri](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) bölümünde listelenen desteklenen işletim sistemlerine karşı çalıştırılabilir.

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON, Azure Windows VM 'de Azure VM bağımlılık Aracısı uzantısı için şemayı gösterir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer/örnek |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantılarını Azure Resource Manager şablonlarıyla dağıtabilirsiniz. Bir Azure Resource Manager şablon dağıtımı sırasında Azure VM bağımlılık Aracısı uzantısı 'nı çalıştırmak için bir Azure Resource Manager şablonunun önceki bölümünde ayrıntılı JSON şemasını kullanabilirsiniz.

Bir sanal makine uzantısı için JSON, sanal makine kaynağının içinde iç içe olabilir. Ya da, bunu bir Kaynak Yöneticisi JSON şablonunun kök veya üst düzeyine yerleştirebilirsiniz. JSON yerleştirmesi, kaynak adının ve türün değerini etkiler. Daha fazla bilgi için [ayarlamak için alt kaynakları ad ve tür](../../azure-resource-manager/child-resource-name-type.md).

Aşağıdaki örnek, bağımlılık Aracısı uzantısının sanal makine kaynağının içinde iç içe olduğunu varsayar. Uzantı kaynağını iç içe aktardığınızda JSON, sanal makinenin `"resources": []` nesnesine yerleştirilir.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

JSON uzantısını şablon köküne yerleştirdiğinizde, kaynak adı üst sanal makineye bir başvuru içerir. Tür, iç içe geçmiş yapılandırmayı yansıtır.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell dağıtımı

Bağımlılık Aracısı sanal makine uzantısını var olan bir sanal makineye dağıtmak için `Set-AzVMExtension` komutunu kullanabilirsiniz. Komutu çalıştırmadan önce, ortak ve özel yapılandırmaların bir PowerShell karma tablosunda depolanması gerekir.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal alabilir ve Azure PowerShell modülü kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için Azure PowerShell modülünü kullanarak aşağıdaki komutu çalıştırın:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Uzantı yürütme çıkışı aşağıdaki dizinde bulunan dosyalara kaydedilir:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Ya da bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin. Azure desteğinin nasıl kullanılacağı hakkında bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
