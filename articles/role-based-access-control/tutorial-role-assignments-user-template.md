---
title: Öğretici - bir kullanıcı, RBAC ve Resource Manager şablonu kullanarak Azure kaynaklarına erişim | Microsoft Docs
description: Rol tabanlı erişim denetimi (RBAC) Azure Resource Manager şablonu kullanarak Azure kaynaklarına kullanıcı erişimi öğrenin.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: edb20221862e6439b3bc574995f4037cbc95f8f9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668872"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Öğretici: RBAC ve Resource Manager şablonu kullanarak Azure kaynaklarına kullanıcı erişimi

[Rol tabanlı erişim denetimi (RBAC)](overview.md) Azure kaynaklarına erişimi yönetme yoludur. Bu öğreticide, bir kaynak grubu oluşturun ve oluşturmak ve kaynak grubunda sanal makineleri yönetmek için bir kullanıcı erişimi verin. Bu öğreticide, erişim vermek için bir Resource Manager şablonu dağıtma işlemi üzerinde odaklanır. Resource Manager şablonları geliştirme hakkında daha fazla bilgi için bkz. [Resource Manager belgeleri](/azure/azure-resource-manager/) ve [şablon başvurusu](/azure/templates/microsoft.authorization/allversions
).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir kaynak grubu kapsamındaki bir kullanıcı için erişim izni ver
> * Dağıtımı doğrulama
> * Temizleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Ekleme ve rol atamalarını kaldırmak için şunlara sahip olmalısınız:

* `Microsoft.Authorization/roleAssignments/write` ve `Microsoft.Authorization/roleAssignments/delete` izinleri gibi [kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) veya [sahibi](built-in-roles.md#owner)

## <a name="grant-access"></a>Erişim verme

Bu hızlı başlangıçta kullanılan şablon dandır [Azure hızlı başlangıç şablonları](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Daha fazla Azure yetkilendirme ilgili şablonları bulunabilir [burada](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Şablonu dağıtmak için seçebileceğiniz **deneyin** Azure Cloud Shell'i açın ve aşağıdaki PowerShell betiğini shell penceresine yapıştırın. Kod yapıştırmak için shell penceresine sağ tıklayın ve ardından **yapıştırın**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Son yordamda oluşturduğunuz kaynak grubunu açın. Varsayılan ad proje adıdır ile **rg** eklenir.
1. Soldaki menüden **Erişim denetimi (IAM)** öğesini seçin.
1. Seçin **rol atamaları**. 
1. İçinde **adı**, son yordamda girdiğiniz e-posta adresi girin. Kullanıcının e-posta adresiyle göreceksiniz **sanal makine Katılımcısı** rol.

## <a name="clean-up"></a>Temizleme

Son yordamda oluşturduğunuz kaynak grubunu kaldırmak için işaretleyin **deneyin** Azure Cloud Shell'i açın ve aşağıdaki PowerShell betiğini shell penceresine yapıştırın.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Öğretici: RBAC ve Azure PowerShell kullanarak Azure kaynaklarına kullanıcı erişimi](tutorial-role-assignments-user-powershell.md)