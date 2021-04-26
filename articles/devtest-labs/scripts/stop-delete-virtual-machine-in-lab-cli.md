---
title: Azure CLı-laboratuvardaki bir sanal makineyi durdurma ve silme
description: Bu makalede, Azure DevTest Labs bir laboratuvardaki bir sanal makineyi durduran ve silen bir Azure CLı betiği sunulmaktadır.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: e30ee47c4f34fe6a71e8c934a4f36cb7edbbe20e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777364"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs’deki bir sanal makineyi durdurmak ve silmek için Azure CLI’yı kullanma

Bu Azure CLI betiği, laboratuvardaki bir sanal makineyi (VM) durdurur ve siler. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır:

| Komut | Notlar |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az_lab_vm_stop) | Laboratuvardaki bir sanal makineyi (VM) durdurur. Bu işlemin tamamlanması biraz zaman alabilir. |
| [az lab vm delete](/cli/azure/lab/vm#az_lab_vm_delete) | Laboratuvardaki bir sanal makineyi (VM) siler Bu işlemin tamamlanması biraz zaman alabilir. |


## <a name="next-steps"></a>Sonraki adımlar

Azure CLI hakkında daha fazla bilgi için bkz. [Azure CLI belgeleri](/cli/azure).

Ek Azure Lab Services CLI betik örnekleri, [Azure Lab Services CLI örnekleri](../samples-cli.md) içinde bulunabilir.
