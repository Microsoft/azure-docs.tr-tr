---
title: Azure CLI Betik Örneği - Anlık görüntüden yönetilen disk oluşturma
description: Azure CLI Betik Örneği - Anlık görüntüden yönetilen disk oluşturma
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 8a219b24c4d4b82363094ccba74db22e3b4018c4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74023309"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>CLI ile anlık görüntüden yönetilen disk oluşturma

Bu betik bir anlık görüntüden yönetilen disk oluşturur. İşletim sistemi anlık görüntülerinden ve veri disklerinden bir sanal makineyi geri yüklemek için bu betiği kullanın. İlgili anlık görüntülerden işletim sistemi ve veri diskleri oluşturun ve sonra yönetilen diskleri ekleyerek yeni bir sanal makine oluşturun. Ayrıca, anlık görüntülerden oluşturulan veri disklerini ekleyerek mevcut bir VM'nin veri disklerini geri yükleyebilirsiniz.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir anlık görüntüden yönetilen disk oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Anlık görüntünün kaynak ve grup özelliklerini kullanarak tüm özelliklerini alır. Yönetilen diski oluşturmak için kimlik özelliği kullanılır.  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk) | Yönetilen bir anlık görüntünün anlık görüntü kimliğini kullanarak yönetilen disk oluşturur |

## <a name="next-steps"></a>Sonraki adımlar

[Yönetilen diski işletim sistemi diski olarak ekleyerek sanal makine oluşturma](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek sanal makine ve yönetilen disk CLI betiği örnekleri, [Azure Linux VM belgeleri](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) içinde bulunabilir.
