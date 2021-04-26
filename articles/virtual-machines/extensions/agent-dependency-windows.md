---
title: Windows için Azure Izleyici bağımlılığı sanal makine uzantısı
description: Azure Izleyici bağımlılık aracısını bir sanal makine uzantısı kullanarak Windows sanal makinesine dağıtın.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: windows
ms.date: 03/29/2019
ms.openlocfilehash: 429cc01f466c55283985729c3395bb2137e38fa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566320"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Windows için Azure Izleyici bağımlılığı sanal makine uzantısı

VM'ler için Azure İzleyici Map özelliği, verileri Microsoft bağımlılık Aracısı 'ndan alır. Windows için Azure VM bağımlılık Aracısı sanal makine uzantısı, Microsoft tarafından yayımlanır ve desteklenir. Uzantı, Azure sanal makinelerine bağımlılık Aracısı 'nı yüklüyor. Bu belgede, Windows için Azure VM bağımlılık Aracısı sanal makine uzantısı için desteklenen platformlar, konfigürasyonlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır.

## <a name="operating-system"></a>İşletim sistemi

Windows için Azure VM bağımlılık Aracısı uzantısı, VM'ler için Azure İzleyici dağıtım makalesinin [desteklenen işletim sistemleri](../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) bölümünde listelenen desteklenen işletim sistemlerine karşı çalıştırılabilir.

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

| Name | Değer/örnek |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| yayımcı | Microsoft. Azure. Monitoring. DependencyAgent |
| tür | DependencyAgentWindows |
| typeHandlerVersion | 9,5 |

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantılarını Azure Resource Manager şablonlarıyla dağıtabilirsiniz. Bir Azure Resource Manager şablon dağıtımı sırasında Azure VM bağımlılık Aracısı uzantısı 'nı çalıştırmak için bir Azure Resource Manager şablonunun önceki bölümünde ayrıntılı JSON şemasını kullanabilirsiniz.

Bir sanal makine uzantısı için JSON, sanal makine kaynağının içinde iç içe olabilir. Ya da, bunu bir Kaynak Yöneticisi JSON şablonunun kök veya üst düzeyine yerleştirebilirsiniz. JSON yerleştirmesi, kaynak adının ve türün değerini etkiler. Daha fazla bilgi için bkz. [alt kaynaklar için ad ve tür ayarlama](../../azure-resource-manager/templates/child-resource-name-type.md).

Aşağıdaki örnek, bağımlılık Aracısı uzantısının sanal makine kaynağının içinde iç içe olduğunu varsayar. Uzantı kaynağını iç içe aktardığınızda JSON, `"resources": []` sanal makinenin nesnesine yerleştirilir.


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

`Set-AzVMExtension`Bağımlılık Aracısı sanal makine uzantısını var olan bir sanal makineye dağıtmak için komutunu kullanabilirsiniz. Komutu çalıştırmadan önce, ortak ve özel yapılandırmaların bir PowerShell karma tablosunda depolanması gerekir.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

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

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Ya da bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin. Azure desteğinin nasıl kullanılacağı hakkında bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
