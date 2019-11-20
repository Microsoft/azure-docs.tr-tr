---
title: PowerShell ile kaynakları denetlemek için ilke oluşturma
description: Uyumlu olmayan kaynakları belirlemek üzere bir Azure Ilkesi ataması oluşturmak için Azure PowerShell kullanın.
ms.date: 03/11/2019
ms.topic: quickstart
ms.openlocfilehash: 796606e1e34e753e49a9083fca5a441cacf6d03c
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960120"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir. Bu hızlı başlangıçta, yönetilen disk kullanmayan sanal makineleri belirlemek üzere bir ilke ataması oluşturun. Bu tamamlandığında, *uyumlu olmayan*sanal makineleri tanımlayacaksınız.

Azure PowerShell modülü, Azure kaynaklarını komut satırından veya betiklerden yönetmek için kullanılır.
Bu kılavuzda, bir ilke ataması oluşturmak için az Module kullanma açıklanmaktadır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- Başlamadan önce, Azure PowerShell en son sürümünün yüklü olduğundan emin olun. Ayrıntılı bilgi için bkz. [Azure PowerShell modülünü Install](/powershell/azure/install-az-ps) .
- Azure PowerShell kullanarak Azure Policy Insights kaynak sağlayıcısını kaydedin. Kaynak sağlayıcısı kaydedildiğinde, aboneliğinizin bununla çalıştığından emin olunur. Bir kaynak sağlayıcısını kaydetmek için kayıt kaynak sağlayıcısı işlemi izni olmalıdır. Bu işlem, Katkıda Bulunan ve Sahip rolleriyle birlikte sunulur. Aşağıdaki komutu çalıştırarak kaynak sağlayıcısını kaydedin:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Kaynak sağlayıcıları kaydetme ve görüntülemeyle ilgili daha fazla bilgi için bkz. [Kaynak Sağlayıcıları ve Türleri](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, *yönetilen diskler olmadan VM 'Leri denetle* tanımı için bir ilke ataması oluşturacaksınız. Bu ilke tanımı, yönetilen diskleri kullanmayan sanal makineleri tanımlar.

Yeni ilke ataması oluşturmak için aşağıdaki komutları çalıştırın:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Yukarıdaki komutlarda aşağıdaki bilgiler kullanılmaktadır:

- **Ad** - Atamanın gerçek adı. Bu örnekte *audit-vm-manageddisks* kullanıldı.
- **Görünen Ad** - Bu ilke atamasının görünen adı. Bu durumda, kullanmakta olduğunuz *yönetilen disk ataması olmayan denetim VM'ler*.
- **Tanım** - Bu, atamayı oluşturmak için kullandığınız ilke tanımıdır. Bu durumda, ilke tanımı kimliğidir *denetim yönetilen diskleri kullanmayan Vm'leri*.
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

Oluşturduğunuz atamayı kaldırmak için aşağıdaki komutu kullanın:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure ortamınızda uyumlu olmayan kaynakları belirlemek üzere bir ilke tanımı atadınız.

Yeni kaynakların uyumlu olduğunu doğrulamak için ilkeleri atama hakkında daha fazla bilgi için öğreticisiyle devam edin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)