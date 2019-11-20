---
title: Örnek-Azure AD yöneticisi olmayan denetim
description: Bu örnek ilke tanımı, SQL Server 'a atanan Azure Active Directory yönetici olmadığında denetler.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: fa4a9b5512f0f10a801cf8566febddea0fc4df99
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076384"
---
# <a name="sample---audit-no-azure-active-directory-administrator"></a>Örnek-denetim Azure Active Directory yönetici yok

SQL sunucusuna atanmış bir Azure Active Directory yöneticisi olmadığında denetler.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Örnek şablon

[!code-json[main](../../../../policy-templates/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.json "Audit SQL DB Level Audit Setting")]

[Azure portalı](#deploy-with-the-portal) kullanarak, [PowerShell](#deploy-with-powershell) ile veya [Azure CLI](#deploy-with-azure-cli) ile bu şablonu dağıtabilirsiniz.

## <a name="deploy-with-the-portal"></a>Portal ile dağıtma

[![Ilke örneğini Azure 'a dağıtma](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FSQL%2Faudit-if-no-sql-active-directory-admin%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell ile dağıtma

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "audit-if-no-sql-active-directory-admin" -DisplayName "Audit If no AAD Admin" -description "Aduit If there is no AAD Admin assigned to this server" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell dağıtımını temizleme

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Azure CLI ile dağıtma

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'audit-if-no-sql-active-directory-admin' --display-name 'Audit If no AAD Admin' --description 'Aduit If there is no AAD Admin assigned to this server' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/SQL/audit-if-no-sql-active-directory-admin/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "audit-if-no-sql-active-directory-admin" 
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI dağıtımını temizleme

Kaynak grubunu, VM’yi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure İlkesi örnekleri](index.md) sayfasındaki diğer örnekleri inceleyin