---
title: Windows sanal masaüstü (klasik) hizmet sorumlusu rol ataması-Azure
description: Windows sanal masaüstü 'nde (klasik) PowerShell kullanarak hizmet sorumluları oluşturma ve rol atama.
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 535ee2dace742a57877c9673e77bee64fd81c456
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445049"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Öğretici: Windows sanal masaüstü 'nde PowerShell ile hizmet sorumluları ve rol atamaları oluşturma (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir.

Hizmet sorumluları, belirli bir amaçla roller ve izinler atamak için Azure Active Directory oluşturabileceğiniz kimliklerdir. Windows sanal masaüstü 'nde, aşağıdakileri yapmak için bir hizmet sorumlusu oluşturabilirsiniz:

- Belirli Windows sanal masaüstü yönetim görevlerini otomatikleştirin.
- Windows sanal masaüstü için herhangi bir Azure Resource Manager şablonu çalıştırırken, MFA 'ya gerekli kullanıcıların yerine kimlik bilgilerini kullanın.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Azure Active Directory bir hizmet sorumlusu oluşturun.
> * Windows sanal masaüstü 'nde bir rol ataması oluşturun.
> * Hizmet sorumlusunu kullanarak Windows sanal masaüstü 'nde oturum açın.

## <a name="prerequisites"></a>Önkoşullar

Hizmet sorumluları ve rol atamaları oluşturabilmeniz için üç şey yapmanız gerekir:

1. AzureAD modülünü yükler. Modülünü yüklemek için PowerShell 'i yönetici olarak çalıştırın ve aşağıdaki cmdlet 'i çalıştırın:

    ```powershell
    Install-Module AzureAD
    ```

2. [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/).

3. Bu makaledeki tüm yönergeleri aynı PowerShell oturumunda izleyin. Pencereyi kapatarak ve daha sonra yeniden açarak PowerShell oturumunuzu keserseniz işlem çalışmayabilir.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Azure Active Directory'de hizmet sorumlusu oluşturma

PowerShell oturumunuzda önkoşulları karşıladıktan sonra, Azure 'da çok kiracılı hizmet sorumlusu oluşturmak için aşağıdaki PowerShell cmdlet 'lerini çalıştırın.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>PowerShell 'de kimlik bilgilerinizi görüntüleme

Hizmet sorumlusu için rol atamasını oluşturmadan önce, kimlik bilgilerinizi görüntüleyin ve daha sonra başvurmak üzere bunları aşağı yazın. Bu PowerShell oturumunu kapattıktan sonra bu parola özellikle önemlidir.

Aşağıda, yazmanız gereken üç kimlik bilgileri ve bunları almak için çalıştırmanız gereken cmdlet 'ler verilmiştir:

- Parola:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Kiracı Kimliği:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Uygulama Kimliği:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Windows sanal masaüstünde rol ataması oluşturma

Ardından, hizmet sorumlusunun Windows sanal masaüstü 'nde oturum açmasını sağlamak için bir rol ataması oluşturmanız gerekir. Rol atamaları oluşturma izinlerine sahip bir hesapla oturum açmanız emin olun.

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/) .

Windows sanal masaüstüne bağlanmak ve Kiracılarınızı göstermek için aşağıdaki PowerShell cmdlet 'lerini çalıştırın.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

İçin bir rol ataması oluşturmak istediğiniz kiracının kiracı adını bulduğunuzda, bu adı aşağıdaki cmdlet içinde kullanın:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Hizmet sorumlusu ile oturum açma

Hizmet sorumlusu için bir rol ataması oluşturduktan sonra, hizmet sorumlusunun aşağıdaki cmdlet 'i çalıştırarak Windows sanal masaüstü 'nde oturum açmasını sağlayın:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Oturum açtıktan sonra, hizmet sorumlusu ile birkaç Windows sanal masaüstü PowerShell cmdlet 'ini test ederek her şeyin çalıştığından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Hizmet sorumlusunu oluşturup Windows sanal masaüstü kiracınızda bir rol atadıktan sonra, bir konak havuzu oluşturmak için bu hizmeti kullanabilirsiniz. Konak havuzları hakkında daha fazla bilgi edinmek için Windows sanal masaüstü 'nde bir konak havuzu oluşturma öğreticisine geçin.

 > [!div class="nextstepaction"]
 > [Azure Market ile ana bilgisayar havuzu oluşturma](create-host-pools-azure-marketplace-2019.md)
