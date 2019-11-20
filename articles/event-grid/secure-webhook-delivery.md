---
title: Azure Event Grid 'de Azure AD ile güvenli Web kancası teslimi
description: Azure Event Grid kullanılarak Azure Active Directory korunan HTTPS uç noktalarına olayların nasıl teslim edileceğini açıklar
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: babanisa
ms.openlocfilehash: 9ee1dd74384725b3fffbfea7144c3a536b50c531
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174557"
---
# <a name="publish-events-to-azure-active-directory-protected-endpoints"></a>Korunan uç noktalara Azure Active Directory olayları yayımlayın

Bu makalede, olay aboneliğiniz ve Web kancası uç noktanız arasındaki bağlantıyı güvenli hale getirmek için Azure Active Directory avantajlarından nasıl yararlanabilmeniz açıklanmaktadır. Azure AD uygulamalarına ve hizmet sorumlularına genel bakış için bkz. [Microsoft Identity platform (v 2.0) genel bakış](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

Bu makale, tanıtım için Azure portal kullanır, ancak özellik CLı, PowerShell veya SDK 'lar kullanılarak da etkinleştirilebilir.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-and-azure-ad-application"></a>Azure AD uygulaması oluşturma

Korumalı uç noktanız için bir Azure AD uygulaması oluşturarak başlayın. Bkz. https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Korumalı API 'nizi bir Daemon uygulaması tarafından çağrılacak şekilde yapılandırın.
    
## <a name="enable-event-grid-to-use-your-azure-ad-application"></a>Azure AD uygulamanızı kullanmak için Event Grid etkinleştirme

Azure AD uygulamanızda bir rol ve hizmet ilkesi oluşturmak için aşağıdaki PowerShell betiğini kullanın. Azure AD uygulamanızdan kiracı KIMLIĞI ve nesne KIMLIĞI gerekir:

    > [!NOTE]
    > You must be a member of the [Azure AD Application Administrator role](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) to execute this script.
    
1. Azure AD kiracı KIMLIĞINIZI kullanmak için PowerShell betiğinin $myTenantId değiştirin.
1. PowerShell betiğinin $myAzureADApplicationObjectId Azure AD uygulamanızın nesne KIMLIĞINI kullanacak şekilde değiştirin
1. Değiştirilen betiği çalıştırın.

```PowerShell
# This is your Tenant Id. 
$myTenantId = "<the Tenant Id of your Azure AD Application>"

Connect-AzureAD -TenantId $myTenantId
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the "Azure Event Grid" Azure Active Directory AppId
$eventGridAppId = "4962773b-9cdb-44cf-a8bf-237846a00ab7"
    
# This is the name of the new role we will add to your Azure AD Application
$eventGridRoleName = "AzureEventGridSecureWebhook"
    
# Create an application role of given name and description
Function CreateAppRole([string] $Name, [string] $Description)
{
    $appRole = New-Object Microsoft.Open.AzureAD.Model.AppRole
    $appRole.AllowedMemberTypes = New-Object System.Collections.Generic.List[string]
    $appRole.AllowedMemberTypes.Add("Application");
    $appRole.DisplayName = $Name
    $appRole.Id = New-Guid
    $appRole.IsEnabled = $true
    $appRole.Description = $Description
    $appRole.Value = $Name;
    return $appRole
}
    
# Get my Azure AD Application, it's roles and service principal
$myApp = Get-AzureADApplication -ObjectId $myAzureADApplicationObjectId
$myAppRoles = $myApp.AppRoles
$eventGridSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $eventGridAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match $eventGridRoleName)
{
    Write-Host "The Azure Event Grid role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $eventGridRoleName -Description "Azure Event Grid Role"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($eventGridSP -match "Microsoft.EventGrid")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the "Azure Event Grid" Azure AD Application and add it to the role
    $eventGridSP = New-AzureADServicePrincipal -AppId $eventGridAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $eventGridSP.ObjectId -PrincipalId $eventGridSP.ObjectId
    
Write-Host "My Azure AD Tenant Id" + $myTenantId
Write-Host "My Azure AD Application Id" + $myAzureADApplicationObjectId
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```
    
## <a name="configure-the-event-subscription"></a>Olay aboneliğini yapılandırma

Olay aboneliğiniz için oluşturma akışında, ' Web kancası ' uç nokta türünü seçin. Uç nokta URI 'nizi aldıktan sonra, olay abonelikleri oluştur dikey penceresinin en üstündeki ek özellikler sekmesine tıklayın.

![Uç nokta türü Web kancasını seçin](./media/secure-webhook-delivery/select-webhook.png)

Ek Özellikler sekmesinde, ' AAD kimlik doğrulaması kullan ' kutusunu işaretleyin ve kiracı KIMLIĞINI ve uygulama KIMLIĞINI yapılandırın:

* Azure AD kiracı KIMLIĞINI betiğin çıktısından kopyalayın ve AAD kiracı KIMLIĞI alanına girin.
* Azure AD uygulama KIMLIĞI 'ni betiğin çıktısından kopyalayın ve AAD uygulama KIMLIĞI alanına girin.

    ![Güvenli Web kancası eylemi](./media/secure-webhook-delivery/aad-configuration.png)

## <a name="next-steps"></a>Sonraki adımlar

* Olay teslimatlarını izleme hakkında bilgi için bkz. [izleyici Event Grid ileti teslimi](monitor-event-delivery.md).
* Kimlik doğrulama anahtarı hakkında daha fazla bilgi için bkz. [Event Grid Security and Authentication](security-authentication.md).
* Azure Event Grid aboneliği oluşturma hakkında daha fazla bilgi için bkz. [Event Grid abonelik şeması](subscription-creation-schema.md).