---
title: Azure CLI Betik Örneği - İşletim sistemi diskini bağlama
description: Azure CLI Betik Örneği - İşletim sistemi diskini bağlama
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 292d67dafa768c82041a2cae8e6d888ee5d9050b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037603"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>VM işletim sistemi diski ile ilgili sorun giderme

Bu betik, hatalı veya sorunlu bir sanal makinenin işletim sistemi diskini ikinci bir sanal makineye veri diski olarak bağlar. Disk sorunlarını giderirken veya verileri kurtarırken bu yöntem kullanışlı olabilir.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir kaynak grubu, sanal makine ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm) | Sanal makinelerin listesini döndürür. Bu örnekte sanal makine işletim sistemi diskini döndürmek için sorgu seçeneği kullanılır. Bu değer daha sonra 'uri' değişken adına eklenir. |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm) | Bir sanal makineyi siler. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | Bir sanal makine oluşturur.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | Bir diski sanal makineye ekler. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm) | Bir sanal makinenin IP adreslerini döndürür. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek sanal makine CLI betiği örnekleri, [Azure Linux VM belgeleri](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) içinde bulunabilir.
