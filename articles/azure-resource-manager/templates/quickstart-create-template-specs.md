---
title: Şablon belirtimini oluşturma ve dağıtma
description: ARM şablonundan bir şablon belirtimi oluşturmayı öğrenin. Ardından, şablon belirtimini aboneliğinizdeki bir kaynak grubuna dağıtın.
author: tfitzmac
ms.date: 12/14/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 28987486726f5a88d20efe9fe8a766e536062c2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889969"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>Hızlı başlangıç: şablon belirtimini oluşturma ve dağıtma (Önizleme)

Bu hızlı başlangıçta, bir Azure Resource Manager şablonu (ARM şablonu) bir [şablon](template-specs.md)belirtimine nasıl paketlenecek gösterilmektedir. Ardından, bu şablon belirtimini dağıtırsınız. Şablon spec, depolama hesabı dağıtan bir ARM şablonu içerir.

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Şablon özellikleri şu anda önizleme aşamasındadır. Azure PowerShell ile kullanmak için, [5.0.0 veya üzeri sürümünü](/powershell/azure/install-az-ps)yüklemelisiniz. Azure CLı ile kullanmak için, [Version 2.14.2 veya üstünü](/cli/azure/install-azure-cli)kullanın.

## <a name="create-template"></a>Şablon oluşturma

Yerel şablondan bir şablon belirtimi oluşturursunuz. Aşağıdaki şablonu kopyalayın ve **azuredeploy.js** adlı bir dosyaya yerel olarak kaydedin. Bu hızlı başlangıç, bir yola **c:\Templates\azuredeploy.js** kaydettiğiniz, ancak herhangi bir yolu kullanabileceğiniz varsayılmaktadır.

:::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

## <a name="create-template-spec"></a>Şablon belirtimi oluşturma

Şablon belirtimi adlı bir kaynak türüdür `Microsoft.Resources/templateSpecs` . Bir şablon belirtimi oluşturmak için PowerShell, Azure CLı, portal veya ARM şablonunu kullanın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Şablon belirtimini içeren yeni bir kaynak grubu oluşturun.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. Bu kaynak grubunda şablon belirtimini oluşturun. Yeni şablon belirtimini **storageSpec** adını verin.

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Şablon belirtimini içeren yeni bir kaynak grubu oluşturun.

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Bu kaynak grubunda şablon belirtimini oluşturun. Yeni şablon belirtimini **storageSpec** adını verin.

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. **Şablon özelliklerine** yönelik arama yapın. Kullanılabilir seçeneklerden **Şablon Özellikleri** ' ni seçin.

    :::image type="content" source="./media/quickstart-create-template-specs/search-template-spec.png" alt-text="arama şablonu özellikleri":::

1. **Şablonu Içeri aktar**' ı seçin.

    :::image type="content" source="./media/quickstart-create-template-specs/import-template.png" alt-text="şablonu içeri aktar":::

1. Klasör simgesini seçin.

    :::image type="content" source="./media/quickstart-create-template-specs/open-folder.png" alt-text="klasörü aç":::

1. Kaydettiğiniz yerel şablona gidip seçin. **Aç**’ı seçin.
1. **İçeri aktar**'ı seçin.

    :::image type="content" source="./media/quickstart-create-template-specs/select-import.png" alt-text="İçeri aktarma seçeneğini belirleyin":::

1. Aşağıdaki değerleri sağlayın:

    - **Ad**: şablon belirtimi için bir ad girin.  Örneğin, **storageSpec**
    - **Abonelik**: şablon belirtimini oluşturmak için kullanılan bir Azure aboneliği seçin.
    - **Kaynak grubu**: **Yeni oluştur**' u seçin ve ardından yeni bir kaynak grubu adı girin.  Örneğin, **Templatespecrg**.
    - **Konum**: kaynak grubu için bir konum seçin. Örneğin,  **Batı ABD 2**.
    - **Sürüm**: şablon belirtimi için bir sürüm girin. **1,0** kullanın.

1. **Gözden geçir + Oluştur**’u seçin.
1. **Oluştur**’u seçin.

# <a name="arm-template"></a>[ARM şablonu](#tab/azure-resource-manager)

> [!NOTE]
> Bir ARM şablonu kullanmak yerine, şablon belirtimini oluşturmak için PowerShell veya CLı kullanmanızı öneririz. Bu araçlar, bağlantılı şablonları ana şablonlarınıza bağlı yapıtlar otomatik olarak dönüştürür. Şablon belirtimini oluşturmak için ARM şablonu kullandığınızda, bu bağlantılı şablonları karmaşık olabilecek yapıtlar olarak el ile eklemeniz gerekir.

1. Şablon belirtimini oluşturmak için ARM şablonu kullandığınızda, şablon kaynak tanımına katıştırılır. Yerel şablonunuzda yapmanız gereken bazı değişiklikler vardır. Aşağıdaki şablonu kopyalayın ve **azuredeploy.js** olarak yerel olarak kaydedin.

    > [!NOTE]
    > Katıştırılmış şablonda, ikinci bir sol köşeli ayraç ile tüm [şablon ifadelerine](template-expressions.md) kaçmalıdır. `"[[`Yerine kullanın `"[` . JSON dizileri hala tek sol köşeli ayraç kullanır.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Yeni bir kaynak grubu oluşturmak için Azure CLı veya PowerShell kullanın.

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. Azure CLı veya PowerShell ile şablonunuzu dağıtın.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>Şablon belirtimini dağıt

Bir şablon belirtimini dağıtmak için, bir şablon dağıtmak için kullandığınız dağıtım komutlarının aynısını kullanın. Dağıtılacak şablon belirtiminin kaynak KIMLIĞINI geçirin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Yeni depolama hesabını içerecek bir kaynak grubu oluşturun.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. Şablon belirtiminin kaynak KIMLIĞINI alın.

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. Şablon belirtimini dağıtın.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. Bir ARM şablonunda yaptığınız gibi parametreleri tam olarak sağlarsınız. Şablon belirtimini depolama hesabı türü için bir parametre ile yeniden dağıtın.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Yeni depolama hesabını içerecek bir kaynak grubu oluşturun.

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Şablon belirtiminin kaynak KIMLIĞINI alın.

    ```azurecli
    id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > Şablon spec KIMLIĞI alma ve bunu Windows PowerShell 'de bir değişkene atama ile ilgili bilinen bir sorun vardır.

1. Şablon belirtimini dağıtın.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. Bir ARM şablonunda yaptığınız gibi parametreleri tam olarak sağlarsınız. Şablon belirtimini depolama hesabı türü için bir parametre ile yeniden dağıtın.

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Oluşturduğunuz şablon belirtimini seçin.

    :::image type="content" source="./media/quickstart-create-template-specs/select-template-spec.png" alt-text="şablon özelliklerini seçin":::

1. **Dağıt**'ı seçin.

    :::image type="content" source="./media/quickstart-create-template-specs/deploy-template-spec.png" alt-text="şablon özelliklerini dağıtma":::

1. Aşağıdaki değerleri sağlayın:

    - **Abonelik**: kaynağı oluşturmak Için bir Azure aboneliği seçin.
    - **Kaynak grubu**: **Yeni oluştur** ' u seçin ve ardından **storagerg** yazın.
    - **Depolama hesabı türü**: **Standard_GRS** seçin.

1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

# <a name="arm-template"></a>[ARM şablonu](#tab/azure-resource-manager)

1. Aşağıdaki şablonu kopyalayın ve **storage.js** adlı bir dosyaya yerel olarak kaydedin.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Depolama hesabı için yeni bir kaynak grubu oluşturmak üzere Azure CLı veya PowerShell 'i kullanın.

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. Azure CLı veya PowerShell ile şablonunuzu dağıtın.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>Erişim verme

Kuruluşunuzdaki diğer kullanıcıların şablon belirtimini dağıtmasını sağlamak istiyorsanız, bunlara okuma erişimi vermeniz gerekir. Okuyucu rolünü, paylaşmak istediğiniz şablon özelliklerini içeren kaynak grubu için bir Azure AD grubuna atayabilirsiniz. Daha fazla bilgi için bkz. [öğretici: Azure PowerShell kullanarak Azure kaynaklarına grup erişimi verme](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

## <a name="update-template"></a>Şablonu Güncelleştir

Şablon belirtimindeki şablonda yapmak istediğiniz bir değişikliği tanımladığınızı varsayalım. Aşağıdaki şablon, depolama hesabı adı için bir ön ek eklediğinden, önceki şablonunuz ile benzerdir. Aşağıdaki şablonu kopyalayın ve dosyadaki azuredeploy.jsgüncelleştirin.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/templatespecs/azuredeploy2.json":::

## <a name="update-template-spec-version"></a>Güncelleştirme şablonu belirtim sürümü

Düzeltilen şablon için yeni bir şablon belirtimi oluşturmak yerine, var olan şablon belirtimine adlı yeni bir sürüm ekleyin `2.0` . kullanıcılar dağıtılacak her iki sürümü de seçebilir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Şablon belirtimi için yeni bir sürüm oluşturun.

   ```powershell
   New-AzTemplateSpec `
     -Name storageSpec `
     -Version "2.0" `
     -ResourceGroupName templateSpecRG `
     -Location westus2 `
     -TemplateFile "c:\Templates\azuredeploy.json"
   ```

1. Yeni sürümü dağıtmak için sürümün kaynak KIMLIĞINI alın `2.0` .

   ```powershell
   $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "2.0").Versions.Id
   ```

1. Bu sürümü dağıtın. Depolama hesabı adı için bir ön ek belirtin.

   ```powershell
   New-AzResourceGroupDeployment `
     -TemplateSpecId $id `
     -ResourceGroupName storageRG `
     -namePrefix "demoaccount"
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. Şablon belirtimi için yeni bir sürüm oluşturun.

   ```azurecli
   az ts create \
     --name storageSpec \
     --version "2.0" \
     --resource-group templateSpecRG \
     --location "westus2" \
     --template-file "c:\Templates\azuredeploy.json"
   ```

1. Yeni sürümü dağıtmak için sürümün kaynak KIMLIĞINI alın `2.0` .

   ```azurecli
   id=$(az ts show --name storageSpec --resource-group templateSpecRG --version "2.0" --query "id")
   ```

1. Bu sürümü dağıtın. Depolama hesabı adı için bir ön ek belirtin.

   ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters namePrefix='demoaccount'
    ```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Şablon belirtimde **Yeni sürüm oluştur**' u seçin.

   :::image type="content" source="./media/quickstart-create-template-specs/select-versions.png" alt-text="Yeni sürüm oluştur":::

1. Yeni sürümü adlandırın `2.0` ve isteğe bağlı olarak not ekleyin. **Şablonu Düzenle**' yi seçin.

   :::image type="content" source="./media/quickstart-create-template-specs/add-version-name.png" alt-text="yeni sürümü Adlandır":::

1. Şablonun içeriğini güncelleştirilmiş şablonilerle değiştirin. **Gözden geçir + kaydet**' i seçin.
1. **Değişiklikleri kaydet**'i seçin.

1. Yeni sürümü dağıtmak için **sürümler** ' i seçin.

   :::image type="content" source="./media/quickstart-create-template-specs/see-versions.png" alt-text="sürümleri Listele":::

1. Dağıtmak istediğiniz sürüm için üç noktayı seçin ve **dağıtın**.

   :::image type="content" source="./media/quickstart-create-template-specs/deploy-version.png" alt-text="dağıtılacak sürümü seçin":::

1. Önceki sürümü dağıtmada yaptığınız gibi alanları girin.
1. **Gözden geçir ve oluştur**’u seçin.
1. **Oluştur**’u seçin.

# <a name="arm-template"></a>[ARM şablonu](#tab/azure-resource-manager)

1. Ayrıca, şablon özelliklerine sahip olmak için yerel şablonunuzda bazı değişiklikler yapmanız gerekir. Aşağıdaki şablonu kopyalayın ve azuredeploy.jsolarak yerel olarak kaydedin.

   ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "2.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    },
                    "namePrefix": {
                      "type": "string",
                      "maxLength": 11,
                      "defaultValue": "store",
                      "metadata": {
                        "description": "Prefix for storage account name"
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat(parameters('namePrefix'), uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. Şablon belirtime yeni sürümü eklemek için şablonunuzu Azure CLı veya PowerShell ile dağıtın.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

1. Aşağıdaki şablonu kopyalayın ve **storage.js** adlı bir dosyaya yerel olarak kaydedin.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-10-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '2.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Azure CLı veya PowerShell ile şablonunuzu dağıtın.

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta dağıttığınız kaynağı temizlemek için, oluşturduğunuz her iki kaynak grubunu silin.

1. Azure portalında sol menüden Kaynak grubu’nu seçin.

1. Ada göre filtrele alanına kaynak grubu adını (templateSpecRG ve storageRG) girin.

1. Kaynak grubu adını seçin.

1. Üstteki menüden Kaynak grubunu sil’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bağlantılı şablonlar içeren bir şablon belirtimi oluşturma hakkında bilgi edinmek için bkz. [bağlantılı şablonun şablon belirtimini oluşturma](template-specs-create-linked.md).
