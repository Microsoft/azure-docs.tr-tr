---
title: Örnek-Linux VM 'lerde eksik uygulamaları denetleme
description: Bu örnek Ilke Konuk yapılandırma girişimi ve tanımları, belirtilen uygulamalar Linux sanal makineleri içinde yüklenmezse denetim girişimi ve tanımlar.
ms.date: 05/02/2019
ms.topic: sample
ms.openlocfilehash: 0789b7f408c1f3eea000bfb2fc21ddf5feff790c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076220"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Örnek-belirtilen uygulamalar Linux VM 'Ler içinde yüklü değilse denetleyin

Bu Ilke Konuk yapılandırması girişimi, belirtilen uygulamalar Linux sanal makineleri içinde yüklü olmadığında bir denetim olayı oluşturur. Bu yerleşik girişim KIMLIĞI `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Tüm konuk yapılandırma girişimleri **audit** ve **Deployifnotexists** ilke tanımlarından oluşur. İlke tanımlarından yalnızca birini atamak Konuk yapılandırmanın doğru şekilde çalışmamasına neden olur.

Bu örneği aşağıdaki kullanarak atayabilirsiniz:

- [Azure portalı](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Girişim bileşenleri

Bu [Konuk yapılandırması](../concepts/guest-configuration.md) girişimi aşağıdaki ilkelerden oluşur:

- [Denetim](#audit-definition) -uygulamalar Linux VM 'ler içinde yüklü olmadığında denetle
  - KIMLIK: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [Deployifnotexists](#deployIfNotExists-definition) -uygulamalar Linux sanal makineleri içinde yüklü OLMADıĞıNDA denetlenecek VM uzantısını dağıtın
  - KIMLIK: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Girişim tanımı

Girişim, **audit** ve **Deployifnotexists** tanımlarına birlikte ve [girişim parametrelerine](#initiative-parameters)katılarak oluşturulur. Bu, tanımın JSON ' dır.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Girişim parametreleri

|Ad |Tür |Açıklama |
|---|---|---|
|applicationName |Dize |Uygulama adları. Örnek: ' Python ', ' PowerShell ' veya ' Python, PowerShell ' gibi virgülle ayrılmış bir liste. ' Power\*' gibi joker karakter eşleştirme için \* kullanın. |

PowerShell veya Azure CLI ile atama oluştururken parametre verileri `-PolicyParameter` (PowerShell) veya `--params` (Azure CLI) kullanılarak dize ya da dosya şeklinde JSON biçiminde iletilebilir.
PowerShell aynı zamanda cmdlet'e bir Ad/Değer hashtable iletilmesini gereken `-PolicyParameterObject` parametresini de destekler. Burada **Ad** parametrenin adı, **Değer** ise atama sırasında iletilen tek bir değer veya değer dizisidir.

Bu örnek parametresinde, _Python_ ve _PowerShell_ uygulamalarının yüklenmesi denetlenir.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Yalnızca **Deployifnotexists** ilke tanımı girişim parametrelerini kullanır.

### <a name="audit-definition"></a>Denetim tanımı

**Denetim** ilkesi tanımının KURALLARıNı tanımlayan JSON.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists tanımı

**Deployifnotexists** ilke tanımının KURALLARıNı tanımlayan JSON.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

**Deployifnotexists** ilke tanımı, Ilkenin doğrulanan Azure görüntülerini tanımlar:

|Yayımcı |Sunduğu |SKU |
|-|-|-|
|OpenLogic |CentOS\* |6\* dışında tümü |
|RedHat |RHEL |6\* dışında tümü |
|RedHat |bitince | Tümü |
|credativ |Debian | 7\* dışında tümü |
|SuSE |SLES\* |11\* hariç tümü |
|Canonical| UbuntuServer |12\* hariç tümü |
|Microsoft-dsvm |Linux-Data-Science-VM-Ubuntu |Tümü |
|Microsoft-dsvm |azureml |Tümü |
|Cloudera |Cloudera-CentOS-OS |6\* dışında tümü |
|Cloudera |Cloudera-aly-CentOS-OS |Tümü |
|Microsoft-ads |Linux\* |Tümü |
|Microsoft-aks |Tümü |Tümü |
|AzureDatabricks |Tümü |Tümü |
|qubole-Inc |Tümü |Tümü |
|DataStax |Tümü |Tümü |
|Couchbase |Tümü |Tümü |
|scalegrid |Tümü |Tümü |
|mak |Tümü |Tümü |
|paloaltonetworks |Tümü |Tümü |

Kuralın **dağıtım** kısmı _ınstaltadadpplication_ parametresini sanal makinedeki konuk yapılandırma aracısına geçirir. Bu yapılandırma, aracının **Denetim** ilkesi tanımıyla doğrulamaları ve rapor uyumluluğunu geri gerçekleştirmesini sağlar.

## <a name="azure-portal"></a>Azure portalında

Portalda **audit** ve **Deployifnotexists** tanımları oluşturulduktan sonra, atamaları atama için bir [girişimde](../concepts/definition-structure.md#initiatives) gruplamak önerilir.

### <a name="create-copy-of-audit-definition"></a>Denetim tanımının kopyasını oluştur

[![](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json) ilke örneğini Azure
dağıtma [![Ilke örneğini Azure gov 'ye dağıtma](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Portal aracılığıyla dağıtmak için bu düğmelerin kullanılması, **Denetim** ilkesi tanımının bir kopyasını oluşturur.
Eşleştirilmiş **Deployifnotexists** ilke tanımı olmadan Konuk yapılandırması doğru çalışmaz.

### <a name="create-copy-of-deployifnotexists-definition"></a>DeployIfNotExists tanımının kopyasını oluştur

[![](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json) ilke örneğini Azure
dağıtma [![Ilke örneğini Azure gov 'ye dağıtma](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Portal aracılığıyla dağıtmak için bu düğmelerin kullanılması, **Deployifnotexists** ilke tanımının bir kopyasını oluşturur. Eşleştirilmiş **Denetim** ilkesi tanımı olmadan, Konuk yapılandırması doğru çalışmaz.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Azure PowerShell ile dağıtma

#### <a name="copy-and-assign-the-initiative"></a>Girişimi Kopyala ve ata

Bu adımlar, hem **Denetim** hem de **Deployifnotexists** için yerleşik ilkeleri içeren girişimin bir kopyasını oluşturur ve girişimi bir kaynak grubuna atar.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Önceki atamayı ve tanımını kaldırmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Denetim tanımını kopyalama ve atama

Bu adımlar, **Denetim** tanımının bir kopyasını oluşturur ve bir kaynak grubuna atar. Bu tanım, eşleştirilmiş **Deployifnotexists** tanımı da atanmaksızın düzgün çalışmayacak.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Önceki atamayı ve tanımını kaldırmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>DeployIfNotExists tanımını kopyalayın ve atayın

Bu adımlar, **Deployifnotexists** tanımının bir kopyasını oluşturur ve bir kaynak grubuna atar.
Bu tanım, eşleştirilmiş **Denetim** tanımı da atanmaksızın düzgün çalışmayacak.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Önceki atamayı ve tanımını kaldırmak için aşağıdaki komutu çalıştırın:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell açıklaması

Betikleri dağıtmak ve kaldırmak için aşağıdaki komutları kullanın. Aşağıdaki tabloda yer alan her komut, komuta özgü belgelere yönlendirir:

| Komut | Notlar |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Azure Ilke girişimi oluşturur. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Bir Azure Ilke tanımı oluşturur. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Tek bir kaynak grubunu alır. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Bir girişim veya tanım için yeni bir Azure Ilke ataması oluşturur. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Belirli bir sorumlu için var olan bir rol atamasını sağlar. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Var olan bir Azure İlkesi atamasını kaldırır. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Bir girişimi kaldırır. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Bir tanımı kaldırır. |

## <a name="next-steps"></a>Sonraki adımlar

- Ek [Azure ilkesi örneklerini](index.md)gözden geçirin.
- [Azure Ilkesi Konuk yapılandırması](../concepts/guest-configuration.md)hakkında daha fazla bilgi edinin.
- [Azure ilke tanımı yapısını](../concepts/definition-structure.md)gözden geçirin.
