---
title: Linux için Azure ağ Izleyicisi Aracısı sanal makine uzantısı
description: Ağ Izleyicisi aracısını, sanal makine uzantısı kullanarak Linux sanal makinesine dağıtın.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: c04b27ab4a8ea53e09ca3a133d6aef6457fe1526
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073043"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Linux için ağ Izleyicisi Aracısı sanal makine uzantısı

## <a name="overview"></a>Genel Bakış

[Azure Ağ İzleyicisi](/azure/network-watcher/) , Azure ağları için izlemeye izin veren bir ağ performansı izleme, tanılama ve analiz hizmetidir. Ağ Izleyicisi Aracısı sanal makinesi (VM) uzantısı, Azure VM 'lerde, isteğe bağlı ağ trafiğini yakalama ve diğer gelişmiş işlevsellik gibi bazı ağ Izleyicisi özellikleri için bir gereksinimdir.

Bu makalede, Linux için ağ Izleyicisi Aracısı VM uzantısı için desteklenen platformlar ve dağıtım seçenekleri ayrıntılı olarak açıklanır. Aracının yüklenmesi, sanal makinenin yeniden başlatılmasını kesintiye uğramaz veya gerektirmez. Uzantıyı dağıttığınız sanal makinelere dağıtabilirsiniz. Sanal makine bir Azure hizmeti tarafından dağıtılırsa, sanal makinede uzantıların yüklenmesine izin verilip verilmeyeceğini öğrenmek için hizmetin belgelerini denetleyin.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Ağ Izleyicisi Aracısı uzantısı aşağıdaki Linux dağıtımları için yapılandırılabilir:

| Dağıtım | Sürüm |
|---|---|
| Ubuntu | 12 + |
| Debian | 7 ve 8 |
| Red Hat | 6 ve 7 |
| Oracle Linux | 6.8+ ve 7 |
| SUSE Linux Enterprise Server | 11 ve 12 |
| OpenSUSE Leap | 42.3 + |
| CentOS | 6.5+ ve 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>İnternet bağlantısı

Bazı ağ Izleyicisi aracı işlevleri, bir VM 'nin Internet 'e bağlanmasını gerektirir. Giden bağlantılar oluşturma özelliği olmadan, bazı ağ Izleyicisi Aracısı özellikleri hatalı olabilir veya kullanılamaz hale gelebilir. Aracı gerektiren ağ Izleyicisi işlevselliği hakkında daha fazla bilgi için, bkz.[Ağ İzleyicisi belgeleri](/azure/network-watcher/).

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON ağ Izleyicisi Aracısı uzantısı için şemayı gösterir. Uzantı, Kullanıcı tarafından sağlanan tüm ayarları gerektirmez veya desteklemez. Uzantı, varsayılan yapılandırmasını temel alır.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantılarını bir Azure Resource Manager şablonuyla dağıtabilirsiniz. Ağ Izleyicisi aracı uzantısını dağıtmak için, şablonunuzda önceki JSON şemasını kullanın.

## <a name="azure-classic-cli-deployment"></a>Azure klasik CLı dağıtımı

Aşağıdaki örnek, ağ Izleyicisi Aracısı VM uzantısını klasik dağıtım modeliyle dağıtılan var olan bir sanal makineye dağıtır:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI dağıtım

Aşağıdaki örnek, Kaynak Yöneticisi aracılığıyla dağıtılan mevcut bir VM 'ye ağ Izleyicisi Aracısı VM uzantısını dağıtır:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Sorun giderme ve destek

### <a name="troubleshooting"></a>Sorun giderme

Azure portal veya Azure CLı kullanarak uzantı dağıtımlarının durumu hakkında veri alabilirsiniz.

Aşağıdaki örnek, klasik Azure CLı kullanılarak klasik dağıtım modeliyle dağıtılan bir VM için uzantıların dağıtım durumunu gösterir:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Uzantı yürütme çıkışı aşağıdaki dizinde bulunan dosyalara kaydedilir:

```
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
```

Aşağıdaki örnek, Azure CLı kullanarak Kaynak Yöneticisi aracılığıyla dağıtılan bir VM için NetworkWatcherAgentLinux uzantısının dağıtım durumunu gösterir:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [Ağ İzleyicisi belgelerine](/azure/network-watcher/)başvurabilirsiniz veya [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için bkz. [destek sss Microsoft Azure](https://azure.microsoft.com/support/faq/).
