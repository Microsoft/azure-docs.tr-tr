---
title: Azure CLI Betik Örneği - CLI ile bir yönetilen diskin anlık görüntüsünü aynı veya farklı aboneliğe kopyalama (taşıma)
description: Azure CLI Betik Örneği - CLI ile bir yönetilen diskin anlık görüntüsünü aynı veya farklı aboneliğe kopyalama (taşıma)
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
ms.custom: mvc
ms.openlocfilehash: 5c83ce8d65be01156d50061d7f0a8287ddbfa1eb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039993"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>CLI ile bir yönetilen diskin anlık görüntüsünü aynı veya farklı aboneliğe kopyalama

Bu betik bir yönetilen diskin anlık görüntüsünü aynı veya farklı bir aboneliğe kopyalar. Aşağıdaki senaryolar için bu betiği kullanın:

1. Maliyetinizi azaltmak için Premium depolamada (Premium_LRS) bir anlık görüntüyü standart depolamaya (Standard_LRS veya Standard_ZRS) geçirin.
1. ZRS depolamanın daha yüksek güvenilirliğine faydalanmak için yerel olarak yedekli depolama alanından (Premium_LRS, Standard_LRS) bir anlık görüntüyü bölge yedekli depolama alanına (Standard_ZRS) geçirin.
1. Daha uzun bekletme için bir anlık görüntüyü aynı bölgedeki farklı bir aboneliğe taşıyın.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, kaynak anlık görüntünün kimliğini kullanarak hedef abonelikte bir anlık görüntü oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | Anlık görüntünün kaynak ve grup özelliklerini kullanarak tüm özelliklerini alır. Anlık görüntüyü farklı aboneliğe kopyalamak için kimlik özelliği kullanılır.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | Üst anlık görüntünün kimlik ve adı ile farklı bir abonelikte anlık görüntü oluşturarak anlık görüntüyü kopyalar.  |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure).

Ek sanal makine ve yönetilen diskler CLı betiği örnekleri, [Azure WINDOWS VM belgelerinde](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bulunabilir.
