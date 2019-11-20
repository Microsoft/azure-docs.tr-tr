---
title: Azure CLı betik örneği-VHD ile VM oluşturma
description: Azure CLI Betik Örneği - Sanal sabit disk kullanarak VM oluşturma.
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
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 16f4e3e153cbc02f8626199d168d069add48e4b6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037663"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Bir sanal sabit disk ile VM oluşturma

Bu örnek, VHD kullanarak bir sanal makine oluşturur.
Bir kaynak grubu, depolama hesabı ve kapsayıcı oluşturur, ardından VHD'yi kapsayıcıya yükleyerek bir VM oluşturur.
VM’ye erişebilmeniz için ssh ortak anahtarını sizin ortak anahtarınızla değiştirir.

Önyüklenebilir bir VHD gerekir. Betik `~/sample.vhd` öğesini arar.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir kaynak grubu, sanal makine, kullanılabilirlik kümesi, yük dengeleyici ve tüm ilgili kaynakları oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account) | Depolama hesaplarını listeler |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account) | Depolama hesabı adının geçerli olduğunu ve önceden var olmadığını doğrular |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys) | Depolama hesaplarının anahtarlarını listeler |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob) | Blobun mevcut olup olmadığını denetler |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container) | Depolama hesabında bir kapsayıcı oluşturur. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob) | VHD’yi karşıya yükleyerek kapsayıcıda bir blob oluşturur. |
| [az vm list](https://docs.microsoft.com/cli/azure/vm) | VM adının kullanımda olup olmadığını denetlemek için `--query` ile birlikte kullanılır. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Sanal makineleri oluşturur. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az-vm-list-ip-addresses) | Oluşturulan VM’nin IP adresini alır. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek sanal makine CLI betiği örnekleri, [Azure Linux VM belgeleri](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) içinde bulunabilir.
