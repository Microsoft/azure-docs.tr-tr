---
title: Kiracı KIMLIĞINIZI bulma-Azure Active Directory
description: Mevcut bir Azure aboneliğine kiracı KIMLIĞI bulma ve Azure Active Directory hakkında yönergeler.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764360"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Azure Active Directory kiracı KIMLIĞINIZI bulma

Azure aboneliklerinin Azure Active Directory (Azure AD) ile bir güven ilişkisi vardır. Azure AD, abonelik için kullanıcıların, hizmetlerin ve cihazların kimliğini doğrulamak üzere güvenilirdir. Her aboneliğin kendisiyle ilişkili bir kiracı KIMLIĞI vardır ve aboneliğiniz için kiracı KIMLIĞINI bulmanın birkaç yolu vardır.

## <a name="find-tenant-id-through-the-azure-portal"></a>Azure portal aracılığıyla kiracı KIMLIĞINI bulun

1. [Azure portalında](https://portal.azure.com) oturum açın.
 
1. **Azure Active Directory** seçin.

1. **Özellikler**’i seçin.

1. Ardından, **KIRACı kimliği** alanına aşağı kaydırın. Kiracı KIMLIĞINIZ kutuda olacaktır.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-Özellikler-kiracı KIMLIĞI-kiracı KIMLIĞI alanı":::

## <a name="find-tenant-id-with-powershell"></a>PowerShell ile kiracı KIMLIĞINI bulma

Ayrıca, kiracıyı programlı bir şekilde bulabilirsiniz. Azure PowerShell kiracı KIMLIĞINI bulmak için cmdlet 'ini kullanın `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Daha fazla bilgi için [Get-AzTenant](/powershell/module/az.accounts/get-aztenant)için bu Azure PowerShell cmdlet başvurusuna bakın.


## <a name="find-tenant-id-with-cli"></a>CLı ile kiracı KIMLIĞINI bulma
Kiracı KIMLIĞINI bulmak için bir komut satırı arabirimi kullanmak istiyorsanız, bunu [Azure CLI](/cli/azure/install-azure-cli) veya [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/)ile yapabilirsiniz. 

Azure CLı için, aşağıdaki örnekte gösterildiği gibi, **az Login**, **az Account List** veya **az Account Tenant List** komutlarından birini kullanın. Her bir komutun çıkışında bulunan aboneliklerinizin her biri için **Tenantıd** özelliğine dikkat edin.

```azurecli-interactive
az login
az account list
az account tenant list
```

Daha fazla bilgi için, bkz. [az Login](/cli/azure/reference-index#az_login) komut Reference, [az Account](/cli/azure/ext/account/account) komut Reference veya [az Account Tenant](/cli/azure/ext/account/account/tenant) komut Reference.


Microsoft 365 CLı için aşağıdaki örnekte gösterildiği gibi cmdlet **Kiracı kimliğini** kullanın:
 
```cli
m365 tenant id get
```

Daha fazla bilgi için Microsoft 365 [Kiracı kimliği Al](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) komut başvurusu ' na bakın.


## <a name="next-steps"></a>Sonraki adımlar

- Yeni bir Azure AD kiracısı oluşturmak için bkz. [hızlı başlangıç: Azure Active Directory yeni kiracı oluşturma](active-directory-access-create-new-tenant.md).

- Bir kiracıya nasıl ilişki veya bir abonelik ekleneceğini öğrenmek için bkz. [Azure Active Directory Kiracınıza Azure aboneliği ilişkilendirme veya ekleme](active-directory-how-subscriptions-associated-directory.md).

- Nesne KIMLIĞINI bulma hakkında bilgi edinmek için bkz. [Kullanıcı nesne kimliğini bulma](/partner-center/find-ids-and-domain-names#find-the-user-object-id).
