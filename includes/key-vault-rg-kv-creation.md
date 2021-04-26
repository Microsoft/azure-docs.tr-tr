---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6d6c4a019e9b0b9bd4ed52990fa8b59e939026f1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073537"
---
Bu hızlı başlangıçta önceden oluşturulmuş bir Azure Anahtar Kasası kullanılmaktadır. [Azure CLI hızlı başlangıç](../articles/key-vault/general/quick-create-cli.md), [Azure PowerShell hızlı](../articles/key-vault/general/quick-create-powershell.md)başlangıç veya [Azure Portal Hızlı Başlangıç](../articles/key-vault/general/quick-create-portal.md)adımlarını izleyerek bir Anahtar Kasası oluşturabilirsiniz. 

Alternatif olarak, yalnızca Azure CLı veya Azure PowerShell komutlarını çalıştırabilirsiniz.

> [!Important]
> Her Anahtar Kasası benzersiz bir ada sahip olmalıdır. -Unique-keykasa-adı> <aşağıdaki örneklerde anahtar kasanızın adıyla değiştirin.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---