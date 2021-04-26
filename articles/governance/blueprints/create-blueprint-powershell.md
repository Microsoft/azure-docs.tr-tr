---
title: 'Hızlı başlangıç: PowerShell ile bir şema oluşturma'
description: Bu hızlı başlangıçta, PowerShell kullanarak yapıtlar oluşturmak, tanımlamak ve dağıtmak için Azure şemaları kullanırsınız.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-api
ms.openlocfilehash: 29c43da0c1467c74ddc85a447a0cf9addb574c7e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538898"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Hızlı başlangıç: PowerShell ile Azure Blueprint tanımlama ve atama

Şemaları oluşturmayı ve atamayı öğrenmek, ortak desenlerin tanımını Azure Resource Manager şablonları (ARM şablonları), ilke, güvenlik ve daha fazlasını temel alan yeniden kullanılabilir ve hızlı dağıtılabilir yapılandırma geliştirmeye olanak sağlar. Bu öğreticide kuruluşunuzda aşağıdakiler gibi şema oluşturma, yayımlama ve atama konusundaki yaygın görevlerin bazılarını yerine getirmek için Azure Blueprints'i kullanmayı öğreneceksiniz:

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.
- Zaten yüklü değilse, PowerShell Galerisi **az** . Blueprint modülünü yüklemek ve doğrulamak için [az. Blueprint modülünü ekleme](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) bölümündeki yönergeleri izleyin.
- Daha önce Azure şemaları kullanmadıysanız, ile Azure PowerShell aracılığıyla kaynak sağlayıcısını kaydedin `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint` .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Şema oluşturma

Uyumluluk için standart desen tanımlamanın ilk adımı kullanılabilir durumdaki kaynaklardan bir şema oluşturmaktır. Abonelik için rol ve ilke atamalarını yapılandırmak üzere 'MyBlueprint' adlı bir şema oluşturacağız. Daha sonra kaynak grubunda bir kaynak grubu, ARM şablonu ve rol ataması ekleyeceğiz.

> [!NOTE]
> PowerShell _kullanırken, öncelikle şema nesnesi oluşturulur_ . Eklenecek ve parametreye sahip olan her _yapıt_ için parametrelerin önceden ilk _şema_ içinde tanımlanması gerekir.

1. İlk _şema_ nesnesini oluşturun. **Blueprintfile** parametresi, şema, oluşturulacak kaynak grupları ve tüm şema düzeyi parametrelerinin özelliklerini içeren bir JSON dosyası alır. Parametreler atama sırasında ayarlanır ve sonraki adımlarda eklenecek yapıtlar tarafından kullanılır.

   - JSON dosyası-blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
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
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell komutu

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Şema tanımlarınızı programlı olarak oluştururken _blueprint.js_ dosya adı kullanın.
     > Bu dosya adı, [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact)çağrılırken kullanılır.

     Şema nesnesi varsayılan olarak varsayılan abonelikte oluşturulur. Yönetim grubunu belirtmek için, ' **ManagementGroupId** parametresini kullanın. Aboneliği belirtmek için, **SubscriptionID** parametresini kullanın.

1. Abonelikte rol ataması ekleyin. **Artifactfile** , yapıt _türünü_ tanımlar, Özellikler rol tanımı tanımlayıcısına hizalanır ve asıl kimlikler bir değer dizisi olarak geçirilir. Aşağıdaki örnekte belirtilen rolün verildiği sorumlu kimlikleri, şema ataması sırasında ayarlanan bir parametreyle yapılandırılmıştır. Bu örnek, bir GUID 'SI ile _katkıda_ bulunan yerleşik rolünü kullanır `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   - JSON dosyası-\artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell komutu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Abonelikte ilke ataması ekleyin. **Artifactfile** , yapıt _türünü_ , bir ilkeye veya girişim tanımına hizalamakta olan özellikleri tanımlar ve ilke atamasını, şema ataması sırasında yapılandırmak üzere tanımlanan şema parametrelerini kullanacak şekilde yapılandırır. Bu örnekte, GUID 'SI ile birlikte _Apply etiketi ve varsayılan değerini kaynak grupları_ yerleşik İlkesi kullanır `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON dosyası-\artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell komutu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Abonelikte Depolama etiketi için (_storageAccountType_ parametresini yeniden kullanarak) başka bir ilke ataması ekleyin. Bu ek ilke ataması yapıtı, şemada tanımlanan bir parametrenin birden fazla yapıt tarafından kullanılabileceğini gösterir. Örnekte kaynak grubunda etiket ayarlamak için **storageAccountType** kullanılmıştır. Bu değer, bir sonraki adımda oluşturulan depolama hesabıyla ilgili bilgi sağlar. Bu örnekte, GUID 'SI ile birlikte _Apply etiketi ve varsayılan değerini kaynak grupları_ yerleşik İlkesi kullanır `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - JSON dosyası-\artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell komutu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Kaynak grubuna şablon ekleyin. ARM şablonuna yönelik **TemplateFile** , ŞABLONUN normal JSON bileşenini içerir. Şablon aynı zamanda **storageAccountType**, **tagName** ve **tagValue** şema parametrelerini şablona geçirerek hepsini yeniden kullanır. Şema parametreleri parametre **Templateparameterfile** parametresi kullanılarak ve şablon içinde, değer eklemek için anahtar-değer ÇIFTININ kullanıldığı JSON içinde kullanılabilir. Şema ve Template parametre adları aynı olabilir.

   - JSON ARM şablon dosyası-\artifacts\templateStorage.json

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

   - JSON ARM şablon parametre dosyası-\artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
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
     }
     ```

   - PowerShell komutu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Rol atamasını kaynak grubuna ekleyin. Yukarıdaki rol ataması girişine benzer şekilde aşağıdaki örnekte de **Sahip** rolü için tanımlayıcı kullanılır ve şemadan farklı bir parametre sunulur. Bu örnek, GUID 'SI ile _sahip_ yerleşik rolünü kullanır `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   - JSON dosyası-\artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell komutu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Şemayı yayımlama

Yapıtları ekledikten sonra şemayı yayımlayabilirsiniz. Yayımladığınızda şema bir aboneliğe atanmaya hazır hale gelir.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

`{BlueprintVersion}` değeri en fazla 20 karakter olmak üzere harf, rakam ve kısa çizgilerden oluşan bir dizedir (boşluk veya özel karakter kullanılamaz). **v20180622-135541** gibi benzersiz ve bilgilendirici bir değer kullanın.

## <a name="assign-a-blueprint"></a>Şema atama

Şema, PowerShell kullanılarak yayımlandıktan sonra bir aboneliğe atanabilir. Oluşturduğunuz şemayı yönetim grubu hiyerarşinizdeki aboneliklerden birine atayın. Şema bir aboneliğe kaydedilirse, bu aboneliğe yalnızca atanabilir. **Şema** parametresi, atanacak şema öğesini belirtir. Ad, konum, kimlik, kilit ve şema parametreleri sağlamak için cmdlet 'inde eşleşen PowerShell parametrelerini kullanın `New-AzBlueprintAssignment` veya bunları **ATAMADOSYASı** parametresi json dosyasında belirtin.

1. Bir aboneliğe atayarak şema dağıtımını çalıştırın. **Katkıda bulunanlar** ve **sahipler** parametreleri, sorumluların bir dizi tanıtıcısından rol atanmasına izin verilmesini gerektirdiğinden, kendi kullanıcılarınızın, gruplarınızın veya hizmet sorumlularınızın **Atamadosyasında** kullanmak üzere objectıds 'yi toplamak için [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) kullanın.

   - JSON dosyası-blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
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
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell komutu

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Kullanıcı tarafından atanan yönetilen kimlik

     Bir şema ataması, [Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/overview.md)de kullanabilir.
     Bu durumda, JSON atama dosyasının **kimlik** kısmı aşağıdaki şekilde değişir. ,,, Ve ' ı `{tenantId}` `{subscriptionId}` `{yourRG}` `{userIdentity}` tenantıd, SubscriptionID, kaynak grubu adı ve Kullanıcı tarafından atanan yönetilen kimliğinizin adı ile değiştirin.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     **Kullanıcı tarafından atanan yönetilen kimlik** herhangi bir abonelik ve kaynak grubunda olabilir ve bu şema, şema ' i atayan kullanıcının izinlerine sahip olur.

     > [!IMPORTANT]
     > Azure şemaları, Kullanıcı tarafından atanan yönetilen kimliği yönetmez. Kullanıcılar yeterli rol ve izin atamaktan sorumludur ya da şema ataması başarısız olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

### <a name="unassign-a-blueprint"></a>Şema atamasını kaldırma

Bir şemayı abonelikten kaldırabilirsiniz. Kaldırma işlemi genellikle yapıt kaynaklarına ihtiyaç duyulmadığında gerçekleştirilir. Bir şema kaldırıldığında o şemanın bir parçası olarak atanan yapıtlar geride kalır. Bir şema atamasını kaldırmak için `Remove-AzBlueprintAssignment` cmdlet 'ini kullanın:

Atamamyblueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta PowerShell ile bir şema oluşturdunuz, atadınız ve kaldırdınız. Azure şemaları hakkında daha fazla bilgi edinmek için şema yaşam döngüsü makalesine ilerleyin.

> [!div class="nextstepaction"]
> [Şema yaşam döngüsü hakkında bilgi edinin](./concepts/lifecycle.md)
