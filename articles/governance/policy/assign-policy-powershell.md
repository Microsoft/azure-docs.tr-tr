---
title: 'Hızlı başlangıç: PowerShell ile yeni ilke ataması'
description: Bu hızlı başlangıçta, uyumlu olmayan kaynakları belirlemek üzere bir Azure Ilkesi ataması oluşturmak için Azure PowerShell kullanırsınız.
ms.date: 03/31/2021
ms.topic: quickstart
ms.openlocfilehash: 1043bd53fa2c494d34b0007a851b95afd937d991
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091438"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir. Bu hızlı başlangıçta, yönetilen disk kullanmayan sanal makineleri belirlemek için bir ilke ataması oluşturacaksınız. Bu tamamlandığında, _uyumlu olmayan_ sanal makineleri tanımlayacaksınız.

Azure PowerShell modülü, Azure kaynaklarını komut satırından veya betiklerden yönetmek için kullanılır.
Bu kılavuzda, bir ilke ataması oluşturmak için az Module kullanma açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

- Başlamadan önce, Azure PowerShell en son sürümünün yüklü olduğundan emin olun. Ayrıntılı bilgi için bkz. [Azure PowerShell modülünü Install](/powershell/azure/install-az-ps) .

- Azure PowerShell kullanarak Azure Policy Insights kaynak sağlayıcısını kaydedin. Kaynak sağlayıcısı kaydedildiğinde, aboneliğinizin bununla çalıştığından emin olunur. Kaynak sağlayıcısını kaydetmek için, kaynak sağlayıcısı kaydetme işlemini kaydettirme izninizin olması gerekir. Bu işlem, Katkıda Bulunan ve Sahip rolleriyle birlikte sunulur. Aşağıdaki komutu çalıştırarak kaynak sağlayıcısını kaydedin:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Kaynak sağlayıcılarını kaydetme ve görüntüleme hakkında daha fazla bilgi için bkz. [kaynak sağlayıcıları ve türleri](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, _yönetilen diskler olmadan VM 'Leri denetle_ tanımı için bir ilke ataması oluşturacaksınız. Bu ilke tanımı, yönetilen diskleri kullanmayan sanal makineleri tanımlar.

Yeni ilke ataması oluşturmak için aşağıdaki komutları çalıştırın:

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Yukarıdaki komutlarda aşağıdaki bilgiler kullanılmaktadır:

- **Ad** - Atamanın gerçek adı. Bu örnekte _audit-vm-manageddisks_ kullanıldı.
- **Görünen Ad** - Bu ilke atamasının görünen adı. Bu durumda, _yönetilen disk ataması olmadan VM 'Leri denetleme_ kullanıyorsunuz demektir.
- **Tanım** - Bu, atamayı oluşturmak için kullandığınız ilke tanımıdır. Bu durumda, _yönetilen diskleri kullanmayan ilke tanımı denetim VM_'lerinin kimliğidir.
- **Kapsam** - Kapsam, ilke atamasının hangi kaynaklarda veya kaynak gruplarında uygulanacağını belirler. Bir abonelikten kaynak gruplarına kadar değişiklik gösterebilir. &lt;Kapsam&gt; yerine kaynak grubunuzun adını yazdığınızdan emin olun.

Artık ortamınızın uyumluluk durumunu anlamak için uyumlu olmayan kaynakları belirlemek için hazırsınız demektir.

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

Oluşturduğunuz ilke atamasıyla uyumlu olmayan kaynakları belirlemek için aşağıdaki bilgileri kullanın. Aşağıdaki komutları çalıştırın:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

İlke durumunu alma hakkında daha fazla bilgi için bkz. [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Sonuçlarınız aşağıdaki örneğe benzer:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Sonuçlar, Azure portal görünümündeki bir ilke atamasının **kaynak uyumluluğu** sekmesinde gördüklerinize göre eşleşir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturulan atamayı kaldırmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure ortamınızda uyumlu olmayan kaynakları belirlemek üzere bir ilke tanımı atadınız.

Yeni kaynakların uyumlu olduğunu doğrulamak üzere ilke atama hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)