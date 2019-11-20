---
title: Belirli laboratuvar ilkelerine Kullanıcı izinleri verme | Microsoft Docs
description: Her kullanıcının ihtiyaçlarına bağlı olarak DevTest Labs 'deki belirli laboratuvar ilkelerine Kullanıcı izinleri verme hakkında bilgi edinin
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028443"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Belirli laboratuvar ilkelerine Kullanıcı izinleri verme
## <a name="overview"></a>Genel Bakış
Bu makalede, PowerShell kullanarak kullanıcılara belirli bir laboratuvar ilkesi için izinler verme konusu gösterilmektedir. Bu şekilde, izinler her kullanıcının ihtiyaçlarına göre uygulanabilir. Örneğin, belirli bir kullanıcıya VM ilkesi ayarlarını değiştirme yeteneği vermek isteyebilirsiniz, ancak maliyet ilkelerini etkilemez.

## <a name="policies-as-resources"></a>Kaynaklar olarak ilkeler
[Azure rol tabanlı Access Control](../role-based-access-control/role-assignments-portal.md) makalesinde ele alındığı gibi RBAC, Azure için kaynakların ayrıntılı erişim yönetimine izin verebilir. RBAC kullanarak, DevOps ekibinizdeki görevleri ayırabilirsiniz ve yalnızca işlerini gerçekleştirmesi için ihtiyaç duydukları kullanıcılara erişim miktarını verebilirsiniz.

DevTest Labs 'de, ilke **Microsoft. DevTestLab/Labs/policySets/policies/** RBAC eylemini sağlayan bir kaynak türüdür. Her laboratuvar ilkesi, Ilke kaynak türündeki bir kaynaktır ve bir RBAC rolüne kapsam olarak atanabilir.

Örneğin, **Izin VERILEN VM boyutları** ilkesine kullanıcılara okuma/yazma izni vermek Için, **Microsoft. devtestlab/Labs/policysets/policies/** Action ile çalışan özel bir rol oluşturursunuz ve ardından uygun kullanıcıları bu kullanıcılara atayabilirsiniz Bu özel rol **Microsoft. DevTestLab/Labs/policySets/policies/AllowedVmSizesInLab**kapsamında.

RBAC 'deki özel roller hakkında daha fazla bilgi edinmek için bkz. [özel roller erişim denetimi](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>PowerShell kullanarak laboratuvar özel rolü oluşturma
Başlamak için [Azure PowerShell yüklemeniz](/powershell/azure/install-az-ps)gerekir. 

Azure PowerShell cmdlet 'lerini ayarladıktan sonra, aşağıdaki görevleri gerçekleştirebilirsiniz:

* Bir kaynak sağlayıcısı için tüm işlemleri/eylemleri listeleme
* Belirli bir roldeki eylemleri listeleyin:
* Özel rol oluşturma

Aşağıdaki PowerShell betiği, bu görevlerin nasıl gerçekleştirileceğini gösteren örnekler gösterir:

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Özel rolleri kullanarak belirli bir ilke için kullanıcıya izin atama
Özel rollerinizi tanımladıktan sonra kullanıcılara atayabilirsiniz. Bir kullanıcıya özel bir rol atamak için, önce bu kullanıcıyı temsil eden **ObjectID** 'yi edinmeniz gerekir. Bunu yapmak için **Get-AzADUser** cmdlet 'ini kullanın.

Aşağıdaki örnekte, *Someuser* kullanıcısının **ObjectID** 17DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 ' dir.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Kullanıcı için **ObjectID** ve özel bir rol adı olduktan sonra, bu rolü **New-azroleatama** cmdlet 'i ile kullanıcıya atayabilirsiniz:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

Önceki örnekte, **AllowedVmSizesInLab** ilkesi kullanılır. Aşağıdaki ilkeleri kullanabilirsiniz:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Sonraki adımlar
Belirli laboratuvar ilkelerine Kullanıcı izinleri verdikten sonra, göz önünde bulundurmanız gereken birkaç adım aşağıda verilmiştir:

* [Laboratuvara erişimin güvenliğini sağlama](devtest-lab-add-devtest-user.md)
* [Laboratuvar ilkeleri belirleme](devtest-lab-set-lab-policy.md)
* [Laboratuvar şablonu oluşturma](devtest-lab-create-template.md)
* [VM'leriniz için özel yapıtlar oluşturma](devtest-lab-artifact-author.md)
* [VM'yi bir laboratuvara ekleme](devtest-lab-add-vm.md)

