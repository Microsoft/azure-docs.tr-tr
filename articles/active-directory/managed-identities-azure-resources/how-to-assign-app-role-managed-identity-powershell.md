---
title: PowerShell kullanarak bir uygulama rolüne yönetilen kimlik atama-Azure AD
description: PowerShell kullanarak, başka bir uygulamanın rolüne yönetilen kimlik erişimi atamaya yönelik adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5150c26d67b77008c11764cc6e51ca7085ffcb31
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784920"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>PowerShell kullanarak bir uygulama rolüne yönetilen kimlik erişimi atama

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory bir kimlik ile Azure hizmetleri sağlar. Kodunuzda kimlik bilgileri gerekmeden çalışır. Azure Hizmetleri, Azure AD kimlik doğrulamasını destekleyen hizmetlere kimlik doğrulaması yapmak için bu kimliği kullanır. Uygulama rolleri rol tabanlı erişim denetimi için bir form sağlar ve bir hizmetin yetkilendirme kuralları uygulamasına izin verir.

Bu makalede, Azure AD PowerShell kullanarak başka bir uygulama tarafından kullanıma sunulan bir uygulama rolüne yönetilen bir kimlik atamayı öğreneceksiniz.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Örnek betikleri çalıştırmak için iki seçeneğiniz vardır:
    - Kod bloklarının sağ üst köşesindeki **It TRY** düğmesini kullanarak açabileceğiniz [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.
    - [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)'in en son sürümünü yükleyerek betikleri yerel olarak çalıştırın.

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Başka bir uygulamanın uygulama rolüne yönetilen kimlik erişimi atama

1. [Azure VM](qs-configure-powershell-windows-vm.md)gibi bir Azure kaynağında yönetilen kimliği etkinleştirin.

1. Yönetilen kimliğin hizmet sorumlusunun nesne KIMLIĞINI bulun.

   **Sistem tarafından atanan bir yönetilen kimlik için**, kaynağın **kimliği SAYFASıNDA Azure Portal nesne kimliğini bulabilirsiniz** . Nesne KIMLIĞINI bulmak için aşağıdaki PowerShell betiğini de kullanabilirsiniz. Kaynak **özellikleri** sayfasında Azure Portal bulunan 1. adımda oluşturduğunuz KAYNAĞıN kaynak kimliği gerekir...

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Kullanıcı tarafından atanan yönetilen kimlik için**, kaynağın **genel bakış** sayfasındaki Azure Portal yönetilen kimliğin nesne kimliğini bulabilirsiniz. Nesne KIMLIĞINI bulmak için aşağıdaki PowerShell betiğini de kullanabilirsiniz. Kullanıcı tarafından atanan yönetilen kimliğin kaynak KIMLIĞI gerekir.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Yönetilen kimliğinizin bir istek göndereceği hizmeti temsil edecek yeni bir uygulama kaydı oluşturun. Uygulama rolünü ortaya çıkaran API veya hizmetin Azure AD kiracınızda zaten bir hizmet sorumlusu varsa, bu adımı atlayın. Örneğin, Microsoft Graph API 'sine yönetilen kimlik erişimi vermek istiyorsanız, bu adımı atlayabilirsiniz.

1. Hizmet uygulamasının hizmet sorumlusunun nesne KIMLIĞINI bulun. Azure portal kullanarak bunu bulabilirsiniz. Azure Active Directory gidin ve **Kurumsal uygulamalar** sayfasını açın, ardından uygulamayı bulun ve **nesne kimliğini** arayın. Ayrıca, aşağıdaki PowerShell betiğini kullanarak hizmet sorumlusunun nesne KIMLIĞINI görünen adına göre de bulabilirsiniz:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Uygulamaların görünen adları benzersiz değildir, bu nedenle doğru uygulamanın hizmet sorumlusunu elde ettiğini doğrulamanız gerekir.

1. Adım 3 ' te oluşturduğunuz uygulamaya bir [uygulama rolü](../develop/howto-add-app-roles-in-azure-ad-apps.md) ekleyin. Azure portal veya Microsoft Graph kullanarak rolü oluşturabilirsiniz. Örneğin, aşağıdaki gibi bir uygulama rolü ekleyebilirsiniz:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Yönetilen kimliğe uygulama rolünü atayın. Uygulama rolünü atamak için aşağıdaki bilgilere ihtiyacınız vardır:
    * `managedIdentityObjectId`: adım 2 ' de bulduğunuz yönetilen kimliğin hizmet sorumlusunun nesne KIMLIĞI.
    * `serverServicePrincipalObjectId`: adım 4 ' te bulduğunuz sunucu uygulamasının hizmet sorumlusunun nesne KIMLIĞI.
    * `appRoleId`: 5. adımda oluşturduğunuz sunucu uygulaması tarafından kullanıma sunulan uygulama rolü KIMLIĞI, örneğin, uygulama rolü KIMLIĞI `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Rol atamasını eklemek için aşağıdaki PowerShell betiğini yürütün:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Betiğin tamamı

Bu örnek betik, bir Azure Web uygulamasının yönetilen kimliğini bir uygulama rolüne nasıl atayacağınızı gösterir.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynaklarına genel bakış için yönetilen kimlik](overview.md)
- Azure VM 'de yönetilen kimliği etkinleştirmek için bkz. [PowerShell kullanarak Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-powershell-windows-vm.md).
