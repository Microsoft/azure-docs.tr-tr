---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500075"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>İşleviniz için destekleyici Azure kaynakları oluşturma

İşlev kodunuzu Azure 'a dağıtabilmeniz için önce üç kaynak oluşturmanız gerekir:

- İlgili kaynaklar için mantıksal kapsayıcı olan bir [kaynak grubu](../articles/azure-resource-manager/management/overview.md).
- İşlevleriniz hakkında durum ve diğer bilgileri korumak için kullanılan bir [depolama hesabı](../articles/storage/common/storage-account-create.md).
- İşlev kodunuzu yürütmek için ortam sağlayan bir işlev uygulaması. Bir işlev uygulaması yerel işlev projenize eşlenir ve kaynakların daha kolay yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanızı sağlar.

Bu öğeleri oluşturmak için aşağıdaki komutları kullanın. Hem Azure CLı hem de PowerShell desteklenir.

1. Şimdiye kadar yapmadıysanız Azure 'da oturum açın:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [Az Login](/cli/azure/reference-index#az_login) komutu sizi Azure hesabınızda oturum açar.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'i Azure hesabınızda oturum açar.

    ---

1. Bölgede adlı bir kaynak grubu oluşturun `AzureFunctionsQuickstart-rg` `westeurope` :

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    [Az Group Create](/cli/azure/group#az_group_create) komutu bir kaynak grubu oluşturur. Genellikle kaynak grubunuzu ve kaynaklarınızı, komuttan döndürülen kullanılabilir bir bölgeyi kullanarak size yakın bir bölgede oluşturursunuz `az account list-locations` .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutu bir kaynak grubu oluşturur. Genellikle kaynak grubunuzu ve kaynaklarınızı size yakın bir bölgede, [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet 'inden döndürülen kullanılabilir bir bölgeyi kullanarak oluşturursunuz.

    ---

1. Kaynak grubunuzda ve bölgenizde genel amaçlı bir depolama hesabı oluşturun:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [Az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutu depolama hesabı oluşturur. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet 'i depolama hesabı oluşturur.

    ---

    Önceki örnekte, `<STORAGE_NAME>` Azure depolama 'da size uygun ve benzersiz olan bir adla değiştirin. Adlar yalnızca üç ile 24 karakter arasında ve küçük harflerden oluşmalıdır. `Standard_LRS`[işlevleri tarafından desteklenen](../articles/azure-functions/storage-considerations.md#storage-account-requirements), genel amaçlı bir hesabı belirtir.
