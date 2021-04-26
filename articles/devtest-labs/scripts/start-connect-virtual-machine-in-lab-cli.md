---
title: Azure CLI Betik Örneği - Laboratuvarda sanal makine başlatma | Microsoft Docs
description: Bu Azure CLI betiği, Azure DevTest Labs’deki bir laboratuvarda bir sanal makine başlatır.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f82d43fc212da034d978ee3d65236d806daeb008
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764990"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs’deki bir laboratuvarda bir sanal makine başlatmak için Azure CLI’yi kullanma

Bu Azure CLI betiği, laboratuvardaki bir sanal makineyi (VM) başlatır. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır:

| Komut | Notlar |
|---|---|
| [az Lab VM Start](/cli/azure/lab/vm#az_lab_vm_start) | Laboratuvardaki bir sanal makineyi (VM) başlatır. Bu işlemin tamamlanması biraz zaman alabilir. |

## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure Lab Services CLI betik örnekleri, [Azure Lab Services CLI örnekleri](../samples-cli.md) içinde bulunabilir.
