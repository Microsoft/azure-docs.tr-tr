---
title: 'Hızlı başlangıç: Azure CLI ile bir şema oluşturma'
description: Bu hızlı başlangıçta, Azure CLı kullanarak yapıtlar oluşturmak, tanımlamak ve dağıtmak için Azure şemaları kullanırsınız.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 506877eddd78ce54681bd4870e1d9040b4738c27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877417"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Hızlı başlangıç: Azure CLı ile Azure Blueprint tanımlama ve atama

Şemaları oluşturmayı ve atamayı öğrenmek, ortak desenlerin tanımını Azure Resource Manager şablonları (ARM şablonları), ilke, güvenlik ve daha fazlasını temel alan yeniden kullanılabilir ve hızlı dağıtılabilir yapılandırma geliştirmeye olanak sağlar. Bu öğreticide kuruluşunuzda aşağıdakiler gibi şema oluşturma, yayımlama ve atama konusundaki yaygın görevlerin bazılarını yerine getirmek için Azure Blueprints'i kullanmayı öğreneceksiniz:

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.
- Daha önce Azure şemaları kullanmadıysanız, ile Azure CLı aracılığıyla kaynak sağlayıcısını kaydedin `az provider register --namespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Blueprint uzantısını ekleme

Şema tanımlarını ve atamalarını yönetmek için Azure CLı 'yı etkinleştirmek üzere uzantının eklenmesi gerekir.
Bu uzantı; [Windows 10 üzerinde bash](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (ikisi de tek başına ve portalın içinde), [Azure CLI Docker resmi](https://hub.docker.com/_/microsoft-azure-cli) dahil olmak üzere Azure CLI’sinin kullanılabildiği her yerde çalışır veya yerel olarak yüklenir.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.0.76**). Henüz yüklenmiş değilse, [bu yönergeleri](/cli/azure/install-azure-cli-windows) izleyin.

1. Seçtiğiniz Azure CLI ortamınızda, aşağıdaki komutu kullanarak içeri aktarın:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Uzantının yüklendiğini ve beklenen sürüm (en az **0.1.0**) olduğunu doğrulayın:

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Şema oluşturma

Uyumluluk için standart desen tanımlamanın ilk adımı kullanılabilir durumdaki kaynaklardan bir şema oluşturmaktır. Abonelik için rol ve ilke atamalarını yapılandırmak üzere 'MyBlueprint' adlı bir şema oluşturacağız. Daha sonra kaynak grubunda bir kaynak grubu, ARM şablonu ve rol ataması ekleyeceğiz.

> [!NOTE]
> Azure CLI kullanırken, öncelikle _şema_ nesnesi oluşturulur. Eklenecek ve parametreye sahip olan her _yapıt_ için parametrelerin önceden ilk _şema_ içinde tanımlanması gerekir.

1. İlk _şema_ nesnesini oluşturun. **Parameters** parametresi, şema düzeyindeki tüm parametreleri IÇEREN bir JSON dosyası alır. Parametreler atama sırasında ayarlanır ve sonraki adımlarda eklenecek yapıtlar tarafından kullanılır.

   - JSON dosyası-blueprintparms.json

     ```json
     {
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
                "displayName": "storage account type.",
                "description": null
            }
        },
        "tagName": {
            "type": "string",
            "metadata": {
                "displayName": "The name of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "tagValue": {
            "type": "string",
            "metadata": {
                "displayName": "The value of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "contributors": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                "strongType": "PrincipalId"
            }
        },
        "owners": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                "strongType": "PrincipalId"
            }
        }
     }
     ```

   - Azure CLı komutu

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Şema tanımlarınızı içeri aktarırken _blueprint.js_ dosya adını kullanın.
     > Bu dosya adı [az şema Import](/cli/azure/blueprint#az_blueprint_import)çağrılırken kullanılır.

     Şema nesnesi varsayılan olarak varsayılan abonelikte oluşturulur. Yönetim grubunu belirtmek için, **ManagementGroup** parametresini kullanın. Aboneliği belirtmek için, parametre **aboneliğini** kullanın.

1. Depolama yapıtları için kaynak grubunu tanımına ekleyin.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Abonelikte rol ataması ekleyin. Aşağıdaki örnekte belirtilen rolün verildiği sorumlu kimlikleri, şema ataması sırasında ayarlanan bir parametreyle yapılandırılmıştır. Bu örnek, bir GUID 'SI ile _katkıda_ bulunan yerleşik rolünü kullanır `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Abonelikte ilke ataması ekleyin. Bu örnekte, GUID 'SI ile birlikte _Apply etiketi ve varsayılan değerini kaynak grupları_ yerleşik İlkesi kullanır `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON dosyası-artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLı komutu

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

     > [!NOTE]
     > `az blueprint`Mac üzerinde kullanırken, `\` yolunu içeren `/` parametre değerleri için ile değiştirin. Bu durumda, **parametrelerin** değeri olur `artifacts/policyTags.json` .

1. Abonelikte Depolama etiketi için (_storageAccountType_ parametresini yeniden kullanarak) başka bir ilke ataması ekleyin. Bu ek ilke ataması yapıtı, şemada tanımlanan bir parametrenin birden fazla yapıt tarafından kullanılabileceğini gösterir. Örnekte kaynak grubunda etiket ayarlamak için **storageAccountType** kullanılmıştır. Bu değer, bir sonraki adımda oluşturulan depolama hesabıyla ilgili bilgi sağlar. Bu örnekte, GUID 'SI ile birlikte _Apply etiketi ve varsayılan değerini kaynak grupları_ yerleşik İlkesi kullanır `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON dosyası-artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLı komutu

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

     > [!NOTE]
     > `az blueprint`Mac üzerinde kullanırken, `\` yolunu içeren `/` parametre değerleri için ile değiştirin. Bu durumda, **parametrelerin** değeri olur `artifacts/policyStorageTags.json` .

1. Kaynak grubuna şablon ekleyin. ARM şablonunun **şablon** parametresi, ŞABLONUN normal JSON bileşenlerini içerir. Şablon aynı zamanda **storageAccountType**, **tagName** ve **tagValue** şema parametrelerini şablona geçirerek hepsini yeniden kullanır. Şema parametreleri parametre **parametreleri** kullanılarak ve şablon içinde, değer eklemek için anahtar-değer ÇIFTININ kullanıldığı JSON içinde kullanılabilir. Şema ve Template parametre adları aynı olabilir.

   - JSON ARM şablon dosyası-artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON ARM şablon parametre dosyası-artifacts\templateStorageParams.json

     ```json
     {
        "storageAccountTypeFromBP": {
           "value": "[parameters('storageAccountType')]"
        },
        "tagNameFromBP": {
           "value": "[parameters('tagName')]"
        },
        "tagValueFromBP": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLı komutu

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

     > [!NOTE]
     > `az blueprint`Mac üzerinde kullanırken, `\` yolunu içeren `/` parametre değerleri için ile değiştirin. Bu durumda, **şablon** değeri `artifacts/templateStorage.json` ve **parametreleri** haline gelir `artifacts/templateStorageParams.json` .

1. Rol atamasını kaynak grubuna ekleyin. Yukarıdaki rol ataması girişine benzer şekilde aşağıdaki örnekte de **Sahip** rolü için tanımlayıcı kullanılır ve şemadan farklı bir parametre sunulur. Bu örnek, GUID 'SI ile _sahip_ yerleşik rolünü kullanır `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Şemayı yayımlama

Yapıtları ekledikten sonra şemayı yayımlayabilirsiniz. Yayımladığınızda şema bir aboneliğe atanmaya hazır hale gelir.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

`{BlueprintVersion}` değeri en fazla 20 karakter olmak üzere harf, rakam ve kısa çizgilerden oluşan bir dizedir (boşluk veya özel karakter kullanılamaz). **V20200605-135541** gibi benzersiz ve bilgilendirici bir öğe kullanın.

## <a name="assign-a-blueprint"></a>Şema atama

Şema, Azure CLI kullanılarak yayımlandıktan sonra bir aboneliğe atanabilir. Oluşturduğunuz şemayı yönetim grubu hiyerarşinizdeki aboneliklerden birine atayın. Şema bir aboneliğe kaydedilirse, bu aboneliğe yalnızca atanabilir. **Şema-Name** parametresi, atanacak şema öğesini belirtir. Ad, konum, kimlik, kilit ve şema parametreleri sağlamak için, komutta eşleşen Azure CLı parametrelerini kullanın `az blueprint assignment create` veya **parametreleri** json dosyasında belirtin.

1. Bir aboneliğe atayarak şema dağıtımını çalıştırın. **Katkıda bulunanlar** ve **sahipler** parametreleri, sorumluların bir dizi tanıtıcısından rol ataması verilmesini gerektirdiğinden, kendi kullanıcılarınızın, gruplarınız veya hizmet sorumluları Için **parametrelerde** kullanmak üzere objectıds 'yi toplamak için [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) kullanın.

   - JSON dosyası-blueprintAssignment.json

     ```json
     {
        "storageAccountType": {
            "value": "Standard_GRS"
        },
        "tagName": {
            "value": "CostCenter"
        },
        "tagValue": {
            "value": "ContosoIT"
        },
        "contributors": {
            "value": [
                "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                "38833b56-194d-420b-90ce-cff578296714"
            ]
        },
        "owners": {
            "value": [
                "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                "316deb5f-7187-4512-9dd4-21e7798b0ef9"
            ]
        }
     }
     ```

   - Azure CLı komutu

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Kullanıcı tarafından atanan yönetilen kimlik

     Bir şema ataması, [Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/overview.md)de kullanabilir.
     Bu durumda, **Identity-Type** parametresi _userassigned_ olarak ayarlanır ve **Kullanıcı tarafından atanan-kimlikler** parametresi kimliği belirtir. `{userIdentity}`Kullanıcı tarafından atanan yönetilen kimliğinizin adıyla değiştirin.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     **Kullanıcı tarafından atanan yönetilen kimlik** herhangi bir abonelik ve kaynak grubunda olabilir ve bu şema, şema ' i atayan kullanıcının izinlerine sahip olur.

     > [!IMPORTANT]
     > Azure şemaları, Kullanıcı tarafından atanan yönetilen kimliği yönetmez. Kullanıcılar yeterli rol ve izin atamaktan sorumludur ya da şema ataması başarısız olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

### <a name="unassign-a-blueprint"></a>Şema atamasını kaldırma

Bir şemayı abonelikten kaldırabilirsiniz. Kaldırma işlemi genellikle yapıt kaynaklarına ihtiyaç duyulmadığında gerçekleştirilir. Bir şema kaldırıldığında o şemanın bir parçası olarak atanan yapıtlar geride kalır. Şema atamasını kaldırmak için şu `az blueprint assignment delete` komutu kullanın:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta Azure CLI ile bir şema oluşturdunuz, atadınız ve kaldırdınız. Azure şemaları hakkında daha fazla bilgi edinmek için şema yaşam döngüsü makalesine ilerleyin.

> [!div class="nextstepaction"]
> [Şema yaşam döngüsü hakkında bilgi edinin](./concepts/lifecycle.md)