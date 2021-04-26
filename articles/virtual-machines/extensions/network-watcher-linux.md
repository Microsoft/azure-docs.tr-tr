---
title: Linux için Azure ağ Izleyicisi Aracısı sanal makine uzantısı
description: Ağ Izleyicisi aracısını, sanal makine uzantısı kullanarak Linux sanal makinesine dağıtın.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/14/2017
ms.openlocfilehash: bc252e560df782625d795b30c6688a34f5c2bd79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563600"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Linux için Ağ İzleyicisi Aracısı sanal makine uzantısı

## <a name="overview"></a>Genel Bakış

[Azure Ağ İzleyicisi](../../network-watcher/index.yml) , Azure ağları için izlemeye izin veren bir ağ performansı izleme, tanılama ve analiz hizmetidir. Ağ Izleyicisi Aracısı sanal makinesi (VM) uzantısı, Azure VM 'lerde, isteğe bağlı ağ trafiğini yakalama ve diğer gelişmiş işlevsellik gibi bazı ağ Izleyicisi özellikleri için bir gereksinimdir.

Bu makalede, Linux için ağ Izleyicisi Aracısı VM uzantısı için desteklenen platformlar ve dağıtım seçenekleri ayrıntılı olarak açıklanır. Aracının yüklenmesi, sanal makinenin yeniden başlatılmasını kesintiye uğramaz veya gerektirmez. Uzantıyı dağıttığınız sanal makinelere dağıtabilirsiniz. Sanal makine bir Azure hizmeti tarafından dağıtılırsa, sanal makinede uzantıların yüklenmesine izin verilip verilmeyeceğini öğrenmek için hizmetin belgelerini denetleyin.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Ağ Izleyicisi Aracısı uzantısı aşağıdaki Linux dağıtımları için yapılandırılabilir:

| Dağıtım | Sürüm |
|---|---|
| Ubuntu | 12 |
| Debian | 7 ve 8 |
| Red Hat | 6 ve 7 |
| Oracle Linux | 6.8 + ve 7 |
| SUSE Linux Enterprise Server | 11 ve 12 |
| OpenSUSE artık | 42.3 + |
| CentOS | 6.5 + ve 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>İnternet bağlantısı

Bazı ağ Izleyicisi aracı işlevleri, bir VM 'nin Internet 'e bağlanmasını gerektirir. Giden bağlantılar oluşturma özelliği olmadan, bazı ağ Izleyicisi Aracısı özellikleri hatalı olabilir veya kullanılamaz hale gelebilir. Aracı gerektiren ağ Izleyicisi işlevselliği hakkında daha fazla bilgi için, bkz.[Ağ İzleyicisi belgeleri](../../network-watcher/index.yml).

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

| Name | Değer/örnek |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| yayımcı | Microsoft. Azure. Networkizleyici |
| tür | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantılarını bir Azure Resource Manager şablonuyla dağıtabilirsiniz. Ağ Izleyicisi aracı uzantısını dağıtmak için, şablonunuzda önceki JSON şemasını kullanın.

## <a name="azure-classic-cli-deployment"></a>Azure klasik CLı dağıtımı

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Aşağıdaki örnek, ağ Izleyicisi Aracısı VM uzantısını klasik dağıtım modeliyle dağıtılan var olan bir sanal makineye dağıtır:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLı dağıtımı

Aşağıdaki örnek, Kaynak Yöneticisi aracılığıyla dağıtılan mevcut bir VM 'ye ağ Izleyicisi Aracısı VM uzantısını dağıtır:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Sorun giderme ve destek

### <a name="troubleshooting"></a>Sorun giderme

Azure portal veya Azure CLı kullanarak uzantı dağıtımlarının durumu hakkında veri alabilirsiniz.

Aşağıdaki örnek, Azure CLı kullanarak Kaynak Yöneticisi aracılığıyla dağıtılan bir VM için NetworkWatcherAgentLinux uzantısının dağıtım durumunu gösterir:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [Ağ İzleyicisi belgelerine](../../network-watcher/index.yml)başvurabilirsiniz veya [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için bkz. [destek sss Microsoft Azure](https://azure.microsoft.com/support/faq/).
