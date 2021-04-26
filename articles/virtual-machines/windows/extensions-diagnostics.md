---
title: Windows için Azure Tanılama uzantısı
description: Azure Tanılama uzantısını kullanarak Azure Windows sanal makinelerini izleme
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e703a8f91f18467660ef7e1b91fdb034feeb00b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549541"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Windows VM 'Leri için Azure Tanılama uzantısı

## <a name="overview"></a>Genel Bakış

Azure Tanılama VM uzantısı, Windows VM 'nizden performans sayaçları ve olay günlükleri gibi izleme verilerini toplamanıza olanak sağlar. Toplamak istediğiniz verileri ve bir Azure depolama hesabı ya da Azure Olay Hub 'ı gibi verilerin nereye gitmesini istediğinizi belirleyebilirsiniz. Bu verileri, Azure portal grafik oluşturmak veya ölçüm uyarıları oluşturmak için de kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Azure Tanılama uzantısı Windows 10 Istemcisi, Windows Server 2008 R2, 2012, 2012 R2 ve 2016 karşısında çalıştırılabilir.

### <a name="internet-connectivity"></a>İnternet bağlantısı

Azure Tanılama uzantısı, hedef sanal makinenin Internet 'e bağlı olmasını gerektirir. 

## <a name="extension-schema"></a>Uzantı şeması

[Azure Tanılama uzantısı şeması ve özellik değerleri bu belgede açıklanmaktadır.](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Önceki bölümde ayrıntılı JSON şeması, bir Azure Resource Manager şablon dağıtımı sırasında Azure Tanılama uzantısını çalıştırmak için bir Azure Resource Manager şablonunda kullanılabilir. Bkz. [WINDOWS VM ve Azure Resource Manager şablonlarıyla izleme ve tanılama kullanma](../extensions/diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

Azure CLı, Azure Tanılama uzantısını var olan bir sanal makineye dağıtmak için kullanılabilir. Korumalı ayarlar ve ayarlar özelliklerini yukarıdaki uzantı şemasından geçerli bir JSON ile değiştirin. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell dağıtımı

`Set-AzVMDiagnosticsExtension`Komut, Azure tanılama uzantısını var olan bir sanal makineye eklemek için kullanılabilir. Ayrıca bkz. [Windows çalıştıran bir sanal makinede Azure tanılama etkinleştirmek Için PowerShell 'ı kullanma](../extensions/diagnostics-windows.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal ve Azure CLı kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için, Azure CLı 'yı kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Azure Tanılama uzantısı için daha kapsamlı bir sorun giderme kılavuzu için [Bu makaleye bakın](../../azure-monitor/agents/diagnostics-extension-troubleshooting.md) .

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.

## <a name="next-steps"></a>Sonraki Adımlar
* [Azure Tanılama uzantısı hakkında daha fazla bilgi edinin](../../azure-monitor/agents/diagnostics-extension-overview.md)
* [Uzantı şemasını ve sürümlerini gözden geçirin](../../azure-monitor/agents/diagnostics-extension-schema-windows.md)
