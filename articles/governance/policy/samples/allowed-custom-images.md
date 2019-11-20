---
title: Örnek onaylı VM görüntüleri
description: Bu örnek ilke tanımı, ortamınızda yalnızca onaylanan özel görüntülerin dağıtılmasını gerektirir.
ms.date: 01/26/2019
ms.topic: sample
ms.openlocfilehash: f8b5911cfa7700ef14dfac610ab4533c57f22e76
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076468"
---
# <a name="sample---approved-virtual-machine-images"></a>Örnek tarafından onaylanan sanal makine görüntüleri

Bu ilke, ortamınızda yalnızca onaylı özel görüntülerin dağıtılmasını gerektirir. Onaylanan bir görüntü kimliği dizisi belirtirsiniz.

Bu örnek ilkeyi aşağıdakileri kullanarak dağıtabilirsiniz:

- [Azure portalı](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Örnek ilke

### <a name="policy-definition"></a>İlke tanımı

REST API, 'Azure'a Dağıt' düğmeleri ve portalda el ile kullanılan tam JSON ilkesi tanımı.

[!code-json[full](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> Portalda el ile ilke oluşturuyorsanız yukarıdaki girişin **properties.parameters** ve **properties.policyRule** bölümlerini kullanın. Geçerli bir JSON kodu haline getirmek için iki bölümü küme ayraçları `{}` arasına alın.

### <a name="policy-rules"></a>İlke kuralları

Azure CLI ve Azure PowerShell tarafından kullanılan, ilke kurallarını tanımlayan JSON.

[!code-json[rule](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>İlke parametreleri

Azure CLI ve Azure PowerShell tarafından kullanılan, ilke parametrelerini tanımlayan JSON.

[!code-json[parameters](../../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Parametreler

|Ad |Tür |Alan |Açıklama |
|---|---|---|---|
|imageIds |Dizi |Microsoft.Compute/imageIds |Onaylı VM görüntülerinin listesi|

PowerShell veya Azure CLI ile atama oluştururken parametre verileri `-PolicyParameter` (PowerShell) veya `--params` (Azure CLI) kullanılarak dize ya da dosya şeklinde JSON biçiminde iletilebilir.
PowerShell aynı zamanda cmdlet'e bir Ad/Değer hashtable iletilmesini gereken `-PolicyParameterObject` parametresini de destekler. Burada **Ad** parametrenin adı, **Değer** ise atama sırasında iletilen tek bir değer veya değer dizisidir.

Bu örnek parametrede yalnızca _YourResourceGroup_ içindeki _ContosoStdImage_ girişine veya 'Orta ABD' konumunda bulunan Windows Server 2016 Datacenter Mayıs 2018 görüntüsüne izin verilir.

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>Azure portalında

[![](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json) ilke örneğini Azure
dağıtma [![Ilke örneğini Azure gov 'ye dağıtma](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Azure PowerShell ile dağıtma

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Azure PowerShell ile kaldırma

Önceki atamayı ve tanımını kaldırmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell açıklaması

Betikleri dağıtmak ve kaldırmak için aşağıdaki komutları kullanın. Aşağıdaki tabloda yer alan her komut, komuta özgü belgelere yönlendirir:

| Komut | Notlar |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | Yeni bir Azure İlkesi tanımı oluşturur. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | Tek bir kaynak grubunu alır. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | Yeni bir Azure İlkesi ataması oluşturur. Bu örnekte bir tanım sağlıyoruz ancak girişim de kullanılabilir. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | Var olan bir Azure İlkesi atamasını kaldırır. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | Var olan bir Azure İlkesi tanımını kaldırır. |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Azure CLI ile dağıtma

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a resource, subscription, or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Azure CLI ile kaldırma

Önceki atamayı ve tanımını kaldırmak için aşağıdaki komutu çalıştırın:

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI açıklaması

| Komut | Notlar |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Yeni bir Azure İlkesi tanımı oluşturur. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Tek bir kaynak grubunu alır. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Yeni bir Azure İlkesi ataması oluşturur. Bu örnekte bir tanım sağlıyoruz ancak girişim de kullanılabilir. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Var olan bir Azure İlkesi atamasını kaldırır. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Var olan bir Azure İlkesi tanımını kaldırır. |

## <a name="rest-api"></a>REST API

[ARMClient](https://github.com/projectkudu/ARMClient) veya PowerShell gibi Resource Manager REST API'si ile etkileşim kurmak için kullanılabilecek birçok araç vardır. PowerShell'den REST API'sini çağırma örneği, **İlke tanımı yapısı** bölümünün [Diğer Adlar](../concepts/definition-structure.md#aliases) kısmında bulunabilir.

### <a name="deploy-with-rest-api"></a>REST API'si ile dağıtma

- İlke Tanımını (Abonelik kapsamı) oluşturun. İstek Gövdesi için [ilke tanımı](#policy-definition) JSON kodunu kullanın.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- İlke Atamasını (Kaynak Grubu kapsamı) oluşturma

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  İstek Gövdesi için aşağıdaki JSON örneğini kullanın:

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>REST API'si ile kaldırma

- İlke Atamasını kaldırma

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- İlke Tanımını kaldırma

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>REST API'si açıklaması

| Hizmet | Grup | İşlem | Notlar |
|---|---|---|---|
| Kaynak Yönetimi | İlke Tanımları | [Oluşturma](/rest/api/resources/policydefinitions/createorupdate) | Abonelikte yeni bir Azure İlkesi tanımı oluşturur. Alternatif: [Yönetim grubunda oluşturma](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Kaynak Yönetimi | İlke Atamaları | [Oluşturma](/rest/api/resources/policyassignments/create) | Yeni bir Azure İlkesi ataması oluşturur. Bu örnekte bir tanım sağlıyoruz ancak girişim de kullanılabilir. |
| Kaynak Yönetimi | İlke Atamaları | [Silme](/rest/api/resources/policyassignments/delete) | Var olan bir Azure İlkesi atamasını kaldırır. |
| Kaynak Yönetimi | İlke Tanımları | [Silme](/rest/api/resources/policydefinitions/delete) | Var olan bir Azure İlkesi tanımını kaldırır. Alternatif: [Yönetim grubunda silme](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Sonraki adımlar

- Ek [Azure İlkesi örneklerini](index.md) gözden geçirme
- [Azure İlkesi tanımı yapısını](../concepts/definition-structure.md) gözden geçirme
- Sanal Makineler için diğer Azure İlkesi örnekleri [Windows VM’lere ilke uygulama](../../../virtual-machines/windows/policy.md) içinde bulunabilir