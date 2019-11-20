---
title: Azure 'da Linux Sanal Makineleri yeniden dağıtma | Microsoft Docs
description: SSH bağlantı sorunlarını azaltmak için Azure 'da Linux sanal makinelerini yeniden dağıtma.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: d8096a14bf2abc0b06b7ab7c3d340a313b1cd24c
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057292"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Linux sanal makinesini yeni Azure düğümüne yeniden dağıtın
Azure 'da bir Linux sanal makinesine (VM) SSH veya uygulama erişiminde sorun giderme sorunları yaşıyorsanız, sanal makinenin yeniden dağıtılması yardımcı olabilir. Bir VM 'yi yeniden dağıtırken, VM 'yi Azure altyapısı içindeki yeni bir düğüme taşıdığından, sonra yeniden güçlendirir. Tüm yapılandırma seçenekleriniz ve ilişkili kaynaklarınız korunur. Bu makalede, Azure CLı veya Azure portal kullanarak bir VM 'yi yeniden dağıtma gösterilmektedir.

> [!NOTE]
> Bir VM 'yi yeniden dağıttığdıktan sonra, geçici disk kaybedilir ve sanal ağ arabirimiyle ilişkili dinamik IP adresleri güncellenir. 


## <a name="use-the-azure-cli"></a>Azure CLI kullanma
En son [Azure CLI](/cli/azure/install-az-cli2) 'yı yükleyip [az Login](/cli/azure/reference-index)kullanarak Azure hesabınızda oturum açın.

[Az VM yeniden dağıtma](/cli/azure/vm)ile sanal makineyi yeniden dağıtın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *MYVM* adlı VM 'yi yeniden dağıtır:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Klasik Azure CLı 'yı kullanma
[En son Azure klasık CLI](../../cli-install-nodejs.md) 'Yı yükleyip Azure hesabınızda oturum açın. Kaynak Yöneticisi modunda olduğunuzdan emin olun (`azure config mode arm`).

Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *MYVM* adlı VM 'yi yeniden dağıtır:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Sonraki adımlar
Sanal makinenize bağlanma sorunları yaşıyorsanız, [SSH bağlantılarında sorun giderme](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) hakkında belirli yardım veya [ayrıntılı SSH sorun giderme adımları](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)bulabilirsiniz. VM 'niz üzerinde çalışan bir uygulamaya erişemiyorsanız, [uygulama sorunlarını giderme sorunlarını](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)da okuyabilirsiniz.


