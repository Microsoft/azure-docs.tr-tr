---
title: Şablonları Cloud Shell dağıtma
description: Azure 'a kaynak dağıtmak için Azure Resource Manager ve Azure Cloud Shell kullanın. Kaynaklar bir Azure Resource Manager şablonunda tanımlanır (ARM şablonu).
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028616"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>ARM şablonlarını Azure Cloud Shell dağıtma

Azure Resource Manager şablonu dağıtmak için [Azure Cloud Shell](../../cloud-shell/overview.md) KULLANABILIRSINIZ (ARM şablonu). Uzaktan depolanan bir ARM şablonunu veya Cloud Shell için yerel depolama hesabında depolanan bir ARM şablonunu dağıtabilirsiniz.

Herhangi bir kapsama dağıtabilirsiniz. Bu makalede bir kaynak grubuna dağıtma gösterilmektedir.

## <a name="deploy-remote-template"></a>Uzak şablonu dağıtma

Dış şablon dağıtmak için, şablon URI 'sini tam olarak herhangi bir dış dağıtım için yaptığınız gibi sağlayın. Dış şablon bir GitHub deposunda veya bir dış depolama hesabında olabilir.

1. Cloud Shell istemi 'ni açın.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell’i açma":::

1. Şablonu dağıtmak için aşağıdaki komutları kullanın:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Yerel şablonu dağıtma

Yerel bir şablon dağıtmak için, önce şablonunuzu Cloud Shell oturumunuza bağlı depolama hesabına yüklemeniz gerekir.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Cloud Shell kaynak grubunuzu seçin. Ad deseni `cloud-shell-storage-<region>` şeklindedir.

   ![Kaynak grubunu seçme](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Cloud Shell için depolama hesabınızı seçin.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Depolama hesabı seçme":::

1. **Dosya paylaşımları**' nı seçin.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Dosya paylaşımlarını seçin":::

1. Cloud Shell için varsayılan dosya payını seçin. Dosya paylaşımının ad biçimi vardır `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Varsayılan dosya paylaşma":::

1. Şablonlarınızı barındırmak için yeni bir dizin ekleyin. Bu dizini seçin.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Dizin ekleme":::

1. **Karşıya Yükle**’yi seçin.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Şablonu karşıya yükle":::

1. Şablonunuzu bulup karşıya yükleyin.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Şablon seç":::

1. Cloud Shell istemi 'ni açın.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell’i açma":::

1. **CloudDrive** dizinine gidin. Şablonları tutmak için eklediğiniz dizine gidin.

1. Şablonu dağıtmak için aşağıdaki komutları kullanın:

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Sonraki adımlar

- Dağıtım komutları hakkında daha fazla bilgi için bkz. [ARM şablonları ve Azure CLI ile kaynak dağıtma](deploy-cli.md) ve [ARM şablonlarıyla kaynakları dağıtma ve Azure PowerShell](deploy-powershell.md).
- Bir şablonu dağıtımdan önce değişiklikleri önizlemek için bkz. [ARM şablonu dağıtımı ne-if işlemi](template-deploy-what-if.md).
