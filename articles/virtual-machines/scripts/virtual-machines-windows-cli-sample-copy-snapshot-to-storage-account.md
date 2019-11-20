---
title: Azure CLı örneği-bir anlık görüntüyü başka bir bölgedeki depolama hesabına kopyalama
description: Azure CLI betik örneği - aynı veya farklı bölgedeki bir depolama hesabına VHD olarak anlık görüntü dışarı aktarma/kopyalama.
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 1e9ba52357703238c35d31823462d9ff3bd04c87
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040038"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>CLI ile farklı bölgedeki bir depolama hesabına bir anlık görüntü dışarı aktarma/kopyalama

Bu betik farklı bölgedeki bir depolama hesabına yönetilen bir anlık görüntü gönderir. İlk olarak anlık görüntünün SAS URI'sini oluşturur ve sonra onu farklı bölgede bulunan bir depolama hesabına kopyalamak için kullanır. Olağanüstü durum kurtarma amacıyla farklı bölgede bulunan yönetilen disklerinizin yedeğini tutmak için bu betiği kullanın.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir yönetilen anlık görüntünün SAS URI'sini oluşturmak için aşağıdaki komutları kullanır ve SAS URI'sini kullanarak anlık görüntüyü bir depolama hesabına kopyalar. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | Temel alınan VHD dosyasını bir depolama hesabına kopyalamak veya şirket içine indirmek üzere kullanılan salt okunur SAS oluşturur  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | Bir blobu bir depolama hesabından diğerine zaman uyumsuz olarak kopyalar |

## <a name="next-steps"></a>Sonraki adımlar

[VHD'den yönetilen disk oluşturma](virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek sanal makine ve yönetilen diskler CLı betiği örnekleri, [Azure WINDOWS VM belgelerinde](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bulunabilir.
