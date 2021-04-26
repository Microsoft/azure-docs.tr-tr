---
title: PowerShell ile atamaları yönetme
description: Resmi Azure şemaları PowerShell modülü az. şema ile şema atamalarını yönetmeyi öğrenin.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: d60fb887e07b4697b8e86a4e2fd74a735ac0bb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919385"
---
# <a name="how-to-manage-assignments-with-powershell"></a>PowerShell ile atamaları yönetme

Şema atama **az. şema** Azure PowerShell modülü kullanılarak yönetilebilir. Modül, atamaları getirmeyi, oluşturmayı, güncelleştirmeyi ve kaldırmayı destekler. Modül, var olan şema tanımlarında ayrıntılar da getirebilir. Bu makalede, modülün nasıl yükleneceği ve nasıl kullanılacağı ele alınmaktadır.

## <a name="add-the-azblueprint-module"></a>Az. Blueprint modülünü ekleyin

Şema atamalarını yönetmek için Azure PowerShell etkinleştirmek üzere modülün eklenmesi gerekir. Bu modül, [Azure Cloud Shell](https://shell.azure.com)ile veya [Azure PowerShell Docker görüntüsüyle](https://hub.docker.com/r/azuresdk/azure-powershell/)yerel olarak yüklü PowerShell ile kullanılabilir.

### <a name="base-requirements"></a>Temel gereksinimler

Azure şemaları modülü için aşağıdaki yazılımlar gereklidir:

- Azure PowerShell 1.5.0 veya üzeri. Henüz yüklenmiş değilse, [bu yönergeleri](/powershell/azure/install-az-ps) izleyin.
- PowerShellGet 2.0.1 veya üzeri. Henüz yüklenmiş ve güncellenmiş değilse, [bu yönergeleri](/powershell/scripting/gallery/installing-psget) izleyin.

### <a name="install-the-module"></a>Modülü yükler

PowerShell için Azure şemaları modülü **az. Blueprint** olur.

1. Bir **Yönetim** PowerShell isteminde aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > **Az. Accounts** zaten yüklüyse, `-AllowClobber` yüklemeyi zorlamak için kullanılması gerekebilir.

1. Modülün içeri aktarıldığını ve doğru sürüm (0.2.6) olduğunu doğrulayın:

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Şema tanımlarını al

Atama ile çalışmanın ilk adımı, genellikle bir şema tanımına başvuru almaktır.
`Get-AzBlueprint`Cmdlet 'i bir veya daha fazla şema tanımı alır. Cmdlet 'i ile olan bir yönetim grubundan şema tanımlarını `-ManagementGroupId {mgId}` veya ile aboneliği alabilir `-SubscriptionId {subId}` . **Name** parametresi bir şema tanımı alır, ancak **ManagementGroupId** veya **SubscriptionID** ile birlikte kullanılmalıdır. **Sürüm** , **ad** ile birlikte kullanılabilir ve bu, hangi şema tanımının döndürüldüğünden daha açık olacaktır. **Sürüm** yerine, anahtar `-LatestPublished` en son yayımlanan sürümü de dönüştürür.

Aşağıdaki örnek, `Get-AzBlueprint` ' 101-planlar-Definition-Subscription ' adlı bir şema tanımının tüm sürümlerini şu şekilde gösterilen belirli bir abonelikten almak için kullanır `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Birden çok sürümü olan bir şema tanımının örnek çıktısı şuna benzer:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

Şema tanımındaki [şema parametreleri](../concepts/parameters.md#blueprint-parameters) daha fazla bilgi sağlamak için genişletilebilir.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Şema atamalarını al

Şema ataması zaten varsa cmdlet 'ini kullanarak buna bir başvuru alabilirsiniz `Get-AzBlueprintAssignment` . Cmdlet 'i, **SubscriptionID** ve **adı** isteğe bağlı parametreler olarak alır. **SubscriptionID** belirtilmemişse, geçerli abonelik bağlamı kullanılır.

Aşağıdaki örnek, `Get-AzBlueprintAssignment` olarak belirtilen belirli bir aboneliğin ' atama-kilitleme-kaynak-gruplar ' adlı tek bir şema atamasını almak için kullanır `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Bir şema atamasının örnek çıktısı şöyle görünür:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Şema atamaları oluşturma

Şema ataması henüz yoksa `New-AzBlueprintAssignment` cmdlet 'ini kullanarak oluşturabilirsiniz. Bu cmdlet şu parametreleri kullanır:

- **Ad** [gerekli]
  - Şema atamasının adını belirtir
  - Benzersiz olmalı ve **abonelik kimliği** zaten mevcut değil
- **Blueprint** [gerekli]
  - Atanacak şema tanımını belirtir
  - `Get-AzBlueprint`Başvuru nesnesini almak için kullanın
- **Konum** [gerekli]
  - Sistem tarafından atanan yönetilen kimlik ve abonelik dağıtım nesnesinin oluşturulacağı bölgeyi belirtir
- **Abonelik** (isteğe bağlı)
  - Atamanın dağıtıldığı aboneliği belirtir
  - Sağlanmazsa, varsayılan olarak geçerli abonelik bağlamına
- **Kilitle** (isteğe bağlı)
  - Dağıtılan kaynaklar için kullanılacak [şema kaynak kilitlemeyi](../concepts/resource-locking.md) tanımlar
  - Desteklenen seçenekler: _none_, _allresourcesreadonly_, _allresourcesdonotdelete_
  - Sağlanmazsa varsayılan değer _none_ olur
- **Systemassignedıdentity** (isteğe bağlı)
  - Atama için sistem tarafından atanan yönetilen kimlik oluşturmak ve kaynakları dağıtmak için seçin
  - "Identity" parametre kümesi için varsayılan
  - **Useratandıdentity** ile kullanılamaz
- **Useratandıdentity** (isteğe bağlı)
  - Atama ve kaynakları dağıtma için kullanılacak kullanıcı tarafından atanan yönetilen kimliği belirtir
  - "Identity" parametre kümesinin bir parçası
  - **Systemassignedıdentity** ile kullanılamaz
- **Parametre** (isteğe bağlı)
  - Şema atamasında [dinamik parametreleri](../concepts/parameters.md#dynamic-parameters) ayarlamak için anahtar/değer çiftlerinin [karma tablosu](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Bir dinamik parametre için varsayılan değer, tanımdaki **DefaultValue** 'dir
  - Bir parametre sağlanmazsa ve **DefaultValue** yoksa, parametre isteğe bağlı değildir

    > [!NOTE]
    > **Parametre** securestrings 'i desteklemiyor.

- **Resourcegroupparameter** (isteğe bağlı)
  - Kaynak grubu yapıtlarının [karma tablosu](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Her kaynak grubu yapıt yer tutucusu, bu kaynak grubu yapıtı üzerinde dinamik ayar **adı** ve **konumu** için anahtar/değer çiftlerine sahiptir
  - Bir kaynak grubu parametresi sağlanmazsa ve **DefaultValue** yoksa, kaynak grubu parametresi isteğe bağlı değildir
- **Atamadosyası** (isteğe bağlı)
  - Bir şema atamasının JSON dosyası gösteriminin yolu
  - Bu parametre, yalnızca **Name**, **Blueprint** ve **SubscriptionID** ve ortak parametreleri içeren bir PowerShell parametre kümesinin bir parçasıdır.

### <a name="example-1-provide-parameters"></a>Örnek 1: parametre sağlama

Aşağıdaki örnek ile birlikte getirilen ' My-şema ' şema tanımının ' 1,1 ' sürümünün yeni bir atamasını oluşturur `Get-AzBlueprint` , yönetilen kimliği ve atama nesnesi konumunu ' westus2 ' olarak ayarlar, kaynakları _allresourcesreadonly_ ile kilitler ve aşağıdaki gibi belirli bir abonelikte hem **parametre** hem de **resourcegroupparameter** için karma tabloları ayarlar `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Bir şema ataması oluşturmak için örnek çıktı şöyle görünür:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Örnek 2: JSON atama tanım dosyası kullanma

Aşağıdaki örnekte, [Örnek 1](#example-1-provide-parameters)ile neredeyse aynı atama oluşturulur. Cmdlet 'e parametre geçirmek yerine, örnek bir JSON atama tanımı dosyası ve **Atamadosyası** parametresinin kullanımını gösterir. Ayrıca, **Excludedsorumlularını** özelliği **kilitlerin** bir parçası olarak yapılandırılır. **Excludedsorumlularını** Için bir PowerShell parametresi yoktur ve ÖZELLIK yalnızca JSON atama tanımı dosyası aracılığıyla ayarlanarak yapılandırılabilir.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Kullanıcı tarafından atanan yönetilen kimliğin JSON atama tanım dosyasının bir örneği için, [örnek: REST API için Kullanıcı tarafından atanan yönetilen kimlik Ile atama](/rest/api/blueprints/assignments/createorupdate#examples) .

## <a name="update-blueprint-assignments"></a>Şema atamalarını Güncelleştir

Bazen zaten oluşturulmuş bir şema atamasının güncelleştirilmesi gerekir. `Set-AzBlueprintAssignment`Cmdlet 'i bu eylemi işler. Cmdlet 'i cmdlet 'in kullandığı parametrelerin çoğunu alır `New-AzBlueprintAssignment` ve atamada ayarlanan her şeyin güncelleştirilmesine izin verir. Özel durumlar _ad_, _Blueprint_ ve _SubscriptionID_' dir. Yalnızca belirtilen değerler güncellenir.

Bir şema atamasını güncelleştirirken ne olduğunu anlamak için bkz. [atamaları güncelleştirme kuralları](./update-existing-assignments.md#rules-for-updating-assignments).

- **Ad** [gerekli]
  - Güncelleştirilecek BLUEPRINT atamasının adını belirtir
  - Güncelleştirme atamasını bulmak için kullanılır, atamayı değiştirmez
- **Blueprint** [gerekli]
  - Şema atamasının şema tanımını belirtir
  - `Get-AzBlueprint`Başvuru nesnesini almak için kullanın
  - Güncelleştirme atamasını bulmak için kullanılır, atamayı değiştirmez
- **Konum** (isteğe bağlı)
  - Sistem tarafından atanan yönetilen kimlik ve abonelik dağıtım nesnesinin oluşturulacağı bölgeyi belirtir
- **Abonelik** (isteğe bağlı)
  - Atamanın dağıtıldığı aboneliği belirtir
  - Sağlanmazsa, varsayılan olarak geçerli abonelik bağlamına
  - Güncelleştirme atamasını bulmak için kullanılır, atamayı değiştirmez
- **Kilitle** (isteğe bağlı)
  - Dağıtılan kaynaklar için kullanılacak [şema kaynak kilitlemeyi](../concepts/resource-locking.md) tanımlar
  - Desteklenen seçenekler: _none_, _allresourcesreadonly_, _allresourcesdonotdelete_
- **Systemassignedıdentity** (isteğe bağlı)
  - Atama için sistem tarafından atanan yönetilen kimlik oluşturmak ve kaynakları dağıtmak için seçin
  - "Identity" parametre kümesi için varsayılan
  - **Useratandıdentity** ile kullanılamaz
- **Useratandıdentity** (isteğe bağlı)
  - Atama ve kaynakları dağıtma için kullanılacak kullanıcı tarafından atanan yönetilen kimliği belirtir
  - "Identity" parametre kümesinin bir parçası
  - **Systemassignedıdentity** ile kullanılamaz
- **Parametre** (isteğe bağlı)
  - Şema atamasında [dinamik parametreleri](../concepts/parameters.md#dynamic-parameters) ayarlamak için anahtar/değer çiftlerinin [karma tablosu](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Bir dinamik parametre için varsayılan değer, tanımdaki **DefaultValue** 'dir
  - Bir parametre sağlanmazsa ve **DefaultValue** yoksa, parametre isteğe bağlı değildir

    > [!NOTE]
    > **Parametre** securestrings 'i desteklemiyor.

- **Resourcegroupparameter** (isteğe bağlı)
  - Kaynak grubu yapıtlarının [karma tablosu](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Her kaynak grubu yapıt yer tutucusu, bu kaynak grubu yapıtı üzerinde dinamik ayar **adı** ve **konumu** için anahtar/değer çiftlerine sahiptir
  - Bir kaynak grubu parametresi sağlanmazsa ve **DefaultValue** yoksa, kaynak grubu parametresi isteğe bağlı değildir

Aşağıdaki örnek, kilit modunu değiştirerek ' My-şema ' şema tanımının ' 1,1 ' sürümünün atamasını güncelleştirir `Get-AzBlueprint` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Bir şema ataması oluşturmak için örnek çıktı şöyle görünür:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Şema atamalarını kaldır

Bir şema atamasının kaldırılması sırasında, `Remove-AzBlueprintAssignment` cmdlet bu eylemi işler. Cmdlet 'i hangi şema atamasının kaldırılacağını belirtmek için **Name** ya da **InputObject** değerini alır. **SubscriptionID** _gereklidir_ ve her durumda belirtilmelidir.

Aşağıdaki örnek, ile var olan bir şema atamasını getirir `Get-AzBlueprintAssignment` ve bu atamayı şöyle temsil eden belirli bir abonelikten kaldırır `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>Kod örneği

Tüm adımları bir araya getiren aşağıdaki örnek, şema tanımını alır, ardından belirli bir abonelikte şu şekilde gösterilen bir şema atamasını oluşturur, güncelleştirir ve kaldırır `{subId}` :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Sonraki adımlar

- [Şema yaşam döngüsü](../concepts/lifecycle.md) hakkında bilgi edinin.
- [Statik ve dinamik parametrelerin](../concepts/parameters.md) kullanımını anlayın.
- [Şema sıralama düzenini](../concepts/sequencing-order.md) özelleştirmeyi öğrenin.
- [Şema kaynak kilitleme](../concepts/resource-locking.md) özelliğini kullanmayı öğrenin.
- [Genel sorun giderme](../troubleshoot/general.md) adımlarıyla şema atama sorunlarını giderin.