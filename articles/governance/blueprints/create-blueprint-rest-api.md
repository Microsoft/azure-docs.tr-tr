---
title: 'Hızlı başlangıç: REST API bir şema oluşturma'
description: Bu hızlı başlangıçta, REST API kullanarak yapıtlar oluşturmak, tanımlamak ve dağıtmak için Azure şemaları kullanırsınız.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: ceea54957e52f3b33d2c3fb2af190f15e6c94ec3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558984"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-rest-api"></a>Hızlı başlangıç: REST API Azure Blueprint tanımlama ve atama

Şemaları oluşturmayı ve atamayı öğrenmek, ortak desenlerin tanımını Azure Resource Manager şablonları (ARM şablonları), ilke, güvenlik ve daha fazlasını temel alan yeniden kullanılabilir ve hızlı dağıtılabilir yapılandırma geliştirmeye olanak sağlar. Bu öğreticide kuruluşunuzda aşağıdakiler gibi şema oluşturma, yayımlama ve atama konusundaki yaygın görevlerin bazılarını yerine getirmek için Azure Blueprints'i kullanmayı öğreneceksiniz:

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.
- `Microsoft.Blueprint`Kaynak sağlayıcısını kaydedin. Yönergeler için bkz. [kaynak sağlayıcıları ve türleri](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>REST API'sini kullanmaya başlama

REST API konusunda bilginiz yoksa özellikle istek URI'si ve istek gövdesi olmak üzere REST API hakkında genel bilgi edinmek için [Azure REST API Başvurusu](/rest/api/azure/) sayfasını inceleyerek başlayın. Bu makalede Azure Blueprints ile çalışmak üzere yönlendirme yapmak için bu kavramlar kullanılmakta ve bunları bildiğiniz kabul edilmektedir. [ARMClient](https://github.com/projectkudu/ARMClient) gibi araçlar yetkilendirme adımlarını otomatik olarak gerçekleştirebilir ve bu nedenle yeni başlayanlar için önerilir.

Azure şemaları özellikleri için bkz. [Azure şemaları REST API](/rest/api/blueprints/).

### <a name="rest-api-and-powershell"></a>REST API ve PowerShell

REST API çağrısı yapmak için bir aracınız yoksa bu yönergeleri PowerShell kullanarak gerçekleştirebilirsiniz. Aşağıda Azure kimlik doğrulaması için basit bir üst bilgi verilmiştir. Bir kimlik doğrulaması üst bilgisi (bazen **Taşıyıcı belirteç** olarak da adlandırılır) oluşturun ve bağlanılacak REST API URI'sini herhangi bir parametre ya da **İstek Gövdesi** ile sağlayın:

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Aboneliğiniz hakkında bilgi almak için yukarıdaki **$restUri** değişkeni içinde yer alan `{subscriptionId}` öğesini değiştirin. $response değişkeni `Invoke-RestMethod` cmdlet'inin sonucunu tutar ve [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) gibi cmdlet'ler ile ayrıştırılabilir. REST API hizmet uç noktası bir **İstek Gövdesi** bekliyorsa `Invoke-RestMethod` öğesinin `-Body` parametresine JSON biçiminde bir değişken sağlayın.

## <a name="create-a-blueprint"></a>Şema oluşturma

Uyumluluk için standart desen tanımlamanın ilk adımı kullanılabilir durumdaki kaynaklardan bir şema oluşturmaktır. Abonelik için rol ve ilke atamalarını yapılandırmak üzere 'MyBlueprint' adlı bir şema oluşturacağız. Daha sonra kaynak grubunda bir kaynak grubu, ARM şablonu ve rol ataması ekleyeceğiz.

> [!NOTE]
> REST API kullanıldığında ilk olarak _şema_ nesnesi oluşturulur. Eklenecek ve parametreye sahip olan her _yapıt_ için parametrelerin önceden ilk _şema_ içinde tanımlanması gerekir.

Her bir REST API URI'sinde kendi değerlerinizle değiştirmeniz gereken değişkenler bulunur:

- `{YourMG}` -Yönetim grubunuzun KIMLIĞIYLE değiştirin
- `{subscriptionId}` - Abonelik kimliğinizle değiştirin

> [!NOTE]
> Ayrıca, planlar abonelik düzeyinde oluşturulabilir. Bir örnek görmek için bkz. [abonelikte şema oluşturma örneği](/rest/api/blueprints/blueprints/createorupdate#subscriptionblueprint).

1. İlk _şema_ nesnesini oluşturun. **İstek Gövdesi** şemayla ilgili özellikleri, oluşturulacak kaynak gruplarını ve tüm şema düzeyi parametreleri içerir. Parametreler atama sırasında ayarlanır ve sonraki adımlarda eklenecek yapıtlar tarafından kullanılır.

   - REST API URI'si

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
     ```

   - İstek Gövdesi

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
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
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
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

1. Abonelikte rol ataması ekleyin. **İstek Gövdesi** yapıtın _türünü_ tanımlar, özellikler rol tanımı tanımlayıcısıyla eşlenir ve sorumlu kimlikleri değer dizisi olarak geçirilir. Aşağıdaki örnekte belirtilen rolün verildiği sorumlu kimlikleri, şema ataması sırasında ayarlanan bir parametreyle yapılandırılmıştır. Bu örnek, bir GUID 'SI ile _katkıda_ bulunan yerleşik rolünü kullanır `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   - REST API URI'si

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2018-11-01-preview
     ```

   - İstek Gövdesi

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Abonelikte ilke ataması ekleyin. **İstek Gövdesi** yapıtın _türü_ ile bir ilke veya girişim tanımıyla eşleşen özellikleri tanımlar ve ilke atamasını şema ataması sırasında yapılandırılacak tanımlı şema parametrelerini kullanacak şekilde yapılandırır. Bu örnekte, GUID 'SI ile birlikte _Apply etiketi ve varsayılan değerini kaynak grupları_ yerleşik İlkesi kullanır `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - REST API URI'si

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2018-11-01-preview
     ```

   - İstek Gövdesi

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Abonelikte Depolama etiketi için (_storageAccountType_ parametresini yeniden kullanarak) başka bir ilke ataması ekleyin. Bu ek ilke ataması yapıtı, şemada tanımlanan bir parametrenin birden fazla yapıt tarafından kullanılabileceğini gösterir. Örnekte kaynak grubunda etiket ayarlamak için **storageAccountType** kullanılmıştır. Bu değer, bir sonraki adımda oluşturulan depolama hesabıyla ilgili bilgi sağlar. Bu örnekte, GUID 'SI ile birlikte _Apply etiketi ve varsayılan değerini kaynak grupları_ yerleşik İlkesi kullanır `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - REST API URI'si

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
     ```

   - İstek Gövdesi

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
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

1. Kaynak grubuna şablon ekleyin. ARM şablonu için **Istek gövdesi** , ŞABLONUN normal JSON bileşenini içerir ve **Properties. resourceGroup** ile hedef kaynak grubunu tanımlar. Şablon aynı zamanda **storageAccountType**, **tagName** ve **tagValue** şema parametrelerini şablona geçirerek hepsini yeniden kullanır. Şema parametreleri **properties.parameters** tanımlanarak şemaya sunulur ve bu anahtar-değer çifti şema JSON nesnesinde kullanılarak değer eklenir. Şema ve şablon parametresi adları aynı olabilir ancak burada şemadan şablon yapıtına geçirme işleminin gösterilmesi için farklı olarak belirlenmiştir.

   - REST API URI'si

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2018-11-01-preview
     ```

   - İstek Gövdesi

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
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
                     "location": "[resourceGroups('storageRG').location]",
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
             },
             "resourceGroup": "storageRG",
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
     }
     ```

1. Rol atamasını kaynak grubuna ekleyin. Yukarıdaki rol ataması girişine benzer şekilde aşağıdaki örnekte de **Sahip** rolü için tanımlayıcı kullanılır ve şemadan farklı bir parametre sunulur. Bu örnek, GUID 'SI ile _sahip_ yerleşik rolünü kullanır `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   - REST API URI'si

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
     ```

   - İstek Gövdesi

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

## <a name="publish-a-blueprint"></a>Şemayı yayımlama

Yapıtları ekledikten sonra şemayı yayımlayabilirsiniz. Yayımladığınızda şema bir aboneliğe atanmaya hazır hale gelir.

- REST API URI'si

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2018-11-01-preview
  ```

`{BlueprintVersion}` değeri en fazla 20 karakter olmak üzere harf, rakam ve kısa çizgilerden oluşan bir dizedir (boşluk veya özel karakter kullanılamaz). **v20180622-135541** gibi benzersiz ve bilgilendirici bir değer kullanın.

## <a name="assign-a-blueprint"></a>Şema atama

REST API kullanarak yayımlanan şemaları bir aboneliğe atayabilirsiniz. Oluşturduğunuz şemayı yönetim grubu hiyerarşinizdeki aboneliklerden birine atayın. Şema bir aboneliğe kaydedilirse, bu aboneliğe yalnızca atanabilir. **İstek Gövdesi** atanacak şemayı belirtir, şema tanımındaki kaynak gruplarının adını ve konumunu sağlar ve şemada tanımlanıp ekli yapıtların biri veya daha fazlası tarafından kullanılan tüm parametreleri sağlar.

Her bir REST API URI'sinde kendi değerlerinizle değiştirmeniz gereken değişkenler bulunur:

- `{tenantId}` -Kiracı KIMLIĞINIZLE değiştirin
- `{YourMG}` -Yönetim grubunuzun KIMLIĞIYLE değiştirin
- `{subscriptionId}` - Abonelik kimliğinizle değiştirin

1. Azure Blueprints hizmet sorumlusuna hedef abonelikte **Sahip** rolünü atayın. AppID statiktir ( `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` ), ancak hizmet sorumlusu kimliği kiracıya göre değişir. Aşağıdaki REST API ile kiracınıza ait ayrıntılı bilgileri isteyebilirsiniz. Farklı yetkilere sahip [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist)kullanır.

   - REST API URI'si

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Bir aboneliğe atayarak şema dağıtımını çalıştırın. **contributors** ve **owners** parametreleri, rol ataması için sorumluların objectId değerlerinden oluşan bir diziye ihtiyaç duyduğu için [Azure Active Directory Graph API'sini](/graph/migrate-azure-ad-graph-planning-checklist) kullanarak kendi kullanıcılarınızın, gruplarınızın veya hizmet sorumlularınızın objectId değerlerini toplayıp **İstek Gövdesinde** kullanabilirsiniz.

   - REST API URI'si

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
     ```

   - İstek Gövdesi

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

   - Kullanıcı tarafından atanan yönetilen kimlik

     Bir şema ataması, [Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/overview.md)de kullanabilir.
     Bu durumda, istek gövdesinin **kimlik** kısmı aşağıdaki şekilde değişir. `{yourRG}`Ve `{userIdentity}` ' ı kaynak grubu adınızla ve Kullanıcı tarafından atanan yönetilen kimliğinizin adıyla değiştirin.

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

Bir şemayı abonelikten kaldırabilirsiniz. Kaldırma işlemi genellikle yapıt kaynaklarına ihtiyaç duyulmadığında gerçekleştirilir. Bir şema kaldırıldığında o şemanın bir parçası olarak atanan yapıtlar geride kalır. Bir şemanın atamasını kaldırmak için aşağıdaki REST API işlemini kullanın:

- REST API URI'si

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

### <a name="delete-a-blueprint"></a>Şema silme

Bir şemanın kendisini kaldırmak için aşağıdaki REST API işlemini kullanın:

- REST API URI'si

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, REST API ile bir şema oluşturdunuz, atadınız ve kaldırdınız. Azure şemaları hakkında daha fazla bilgi edinmek için şema yaşam döngüsü makalesine ilerleyin.

> [!div class="nextstepaction"]
> [Şema yaşam döngüsü hakkında bilgi edinin](./concepts/lifecycle.md)