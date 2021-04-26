---
title: 'Öğretici: Azure Resource Manager erişmek için yönetilen bir kimlik kullanma-Windows-Azure AD'
description: Windows VM üzerinde bir kullanıcı tarafından atanan yönetilen kimliği kullanarak Azure Resource Manager'a erişme işleminde size yol gösteren bir öğretici.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 37b7b6332bdd000968dc136b946d61ebe82d87ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776384"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Öğretici: Azure Resource Manager erişmek için Windows VM üzerinde kullanıcı tarafından atanan yönetilen kimlik kullanma

Bu öğreticide, kullanıcı tarafından atanan kimliği oluşturma, bunu Windows Sanal Makinesine (VM) atama ve bu kimliği Azure Resource Manager API’sine erişmek için kullanma işlemleri açıklanır. Yönetilen Hizmet Kimlikleri Azure tarafından otomatik olarak yönetilir. Bunlar, kodunuza kimlik bilgileri girmenize gerek kalmadan Azure AD kimlik doğrulamasını destekleyen hizmetlerde kimlik doğrulaması yapmaya olanak tanır. 

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Kullanıcı tarafından atanan yönetilen kimlik oluşturma
> * Kullanıcı tarafından atanan kimliğinizi Windows VM’nize atama
> * Azure Resource Manager’da Kaynak Grubuna kullanıcı tarafından atanan kimlik için erişim verme 
> * Kullanıcı tarafından atanan kimliği kullanarak erişim belirteci alma ve Azure Resource Manager çağrısı yapmak için bunu kullanma 
> * Kaynak Grubunun özelliklerini okuma

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Azure portal oturum açın](https://portal.azure.com)

- [Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-portal.md)

- Bu öğreticideki gerekli kaynak oluşturma ve rol yönetimini adımlarını gerçekleştirmek için hesabınız uygun kapsamda (aboneliğiniz veya kaynak grubunuz) "Sahip" izinlerini gerektiriyor. Rol atama ile ilgili yardıma ihtiyacınız varsa, [Azure abonelik kaynaklarınıza erişimi yönetmek Için Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)konusuna bakın.

- Örnek betikleri çalıştırmak için iki seçeneğiniz vardır:
    - Kod bloklarının sağ üst köşesindeki **It TRY** düğmesini kullanarak açabileceğiniz [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.
    - Komut dosyalarını, sonraki bölümde açıklandığı gibi Azure PowerShell yerel olarak çalıştırın.

### <a name="configure-azure-powershell-locally"></a>Azure PowerShell yerel olarak yapılandırma

Bu makale için Azure PowerShell yerel olarak kullanmak için (Cloud Shell kullanmak yerine), aşağıdaki adımları izleyin:

1. Henüz yapmadıysanız [Azure PowerShell en son sürümünü](/powershell/azure/install-az-ps) yükleyebilirsiniz.

1. Azure 'da oturum açın:

    ```azurepowershell
    Connect-AzAccount
    ```

1. [PowerShellGet'in en son sürümünü](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget) yükleyin.

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    `Exit`Sonraki adımda bu komutu çalıştırdıktan sonra geçerli PowerShell oturumunun olması gerekebilir.

1. `Az.ManagedServiceIdentity`Bu makalede Kullanıcı tarafından atanan yönetilen kimlik işlemlerini gerçekleştirmek için modülün ön sürümü sürümünü yükler:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>Etkinleştir

Kullanıcı tarafından atanan bir kimliğe dayalı bir senaryo için aşağıdaki adımları gerçekleştirmeniz gerekir:

- Kimlik oluşturma
- Yeni oluşturulan kimliği ata

### <a name="create-identity"></a>Kimlik oluştur

Bu bölümde, Kullanıcı tarafından atanan bir kimliğin nasıl oluşturulacağı gösterilmektedir. Kullanıcı tarafından atanan kimlik, tek başına bir Azure kaynağı olarak oluşturulur. [New-Azuseratandıdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity)kullanılarak Azure, Azure AD kiracınızda bir veya daha fazla Azure hizmet örneğine atanabilecek bir kimlik oluşturur.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Yanıt, aşağıdaki örneğe benzer biçimde, oluşturulmuş kullanıcı tarafından atanan kimliğin ayrıntılarını içerir. Sonraki adımlarda kullanılacağından kullanıcı tarafından atanan kimliğin `Id` ve `ClientId` değerlerini not edin:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>Kimlik ata

Bu bölümde, Kullanıcı tarafından atanan kimliğin bir Windows sanal makinesine nasıl atanacağı gösterilmektedir. Kullanıcı tarafından atanan kimlik, istemciler tarafından birden çok Azure kaynağında kullanılabilir. Aşağıdaki komutları kullanarak kullanıcı tarafından atanan kimliği tek bir VM'ye atayın. `-IdentityID` parametresi için önceki adımda döndürülen `Id` özelliğini kullanın.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Erişim verme 

Bu bölümde, Azure Resource Manager ' deki bir kaynak grubuna kullanıcı tarafından atanan kimlik erişimi verme yöntemi gösterilmektedir. Azure kaynakları için yönetilen kimlikler, kodunuzun Azure AD kimlik doğrulamasını destekleyen kaynak API'lerinde kimlik doğrulaması yapmak amacıyla erişim belirteçleri istemek için kullanabileceği kimlikleri sağlar. Bu öğreticide, kodunuz Azure Resource Manager API’sine erişir. 

Kodunuzun API'ye erişebilmesi için önce Azure Resource Manager'da kaynağa kimlik erişimi vermeniz gerekir. Bu durumda, içinde VM'nin yer aldığı Kaynak Grubudur. `<SUBSCRIPTION ID>` değerini ortamınıza uyacak şekilde güncelleştirin.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Yanıt, aşağıdaki örneğe benzer biçimde, oluşturulmuş atamasının ayrıntılarını içerir:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>Verilere erişme

### <a name="get-an-access-token"></a>Bir erişim belirteci alma 

Bu öğreticinin kalan bölümünde, daha önce oluşturmuş olduğumuz VM'den çalışacaksınız.

1. Azure portal şurada oturum açın: [https://portal.azure.com](https://portal.azure.com)

2. Portalda, **Sanal Makineler**'e ve Windows sanal makinesine gidin, ardından **Genel Bakış**'ta **Bağlan**'a tıklayın.

3. Windows VM'sini oluştururken kullandığınız **Kullanıcı adı** ve **Parola**’yı girin.

4. Sanal makineyle bir **Uzak Masaüstü bağlantısı** oluşturduğunuza göre, uzak oturumda **PowerShell** ' i açın.

5. PowerShell’in `Invoke-WebRequest` komutunu kullanarak, Azure kaynakları için yönetilen kimliği uç noktasına Azure Resource Manager için erişim belirteci alma isteğinde bulunun.  `client_id`Değer, Kullanıcı tarafından atanan yönetilen kimliği oluşturduğunuzda döndürülen değerdir.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Okuma özellikleri

Azure Resource Manager’a erişmek için önceki adımda alınan erişim belirtecini kullanın ve kullanıcı tarafından atanan kimliğiniz için erişim verdiğiniz Kaynak Grubunun özelliklerini okuyun. `<SUBSCRIPTION ID>` öğesini ortamınızın abonelik kimliğiyle değiştirin.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Yanıtta, aşağıdaki örneğe benzer belirli Kaynak Grubu bilgileri yer alır:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Kullanıcı tarafından atanan bir kimlik oluşturmayı ve Azure Resource Manager API 'sine erişmek için Azure sanal makinesine eklemeyi öğrendiniz.  Azure Resource Manager hakkında daha fazla bilgi edinmek için bkz:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
