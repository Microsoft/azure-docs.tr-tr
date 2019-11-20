---
title: Azure portal eylem grupları oluşturma ve yönetme
description: Azure portal eylem grupları oluşturmayı ve yönetmeyi öğrenin.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 8/19/2019
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: a0b0df9110f062b5f9c23840cb21308b634c9c81
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69898175"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Azure portal eylem grupları oluşturma ve yönetme
Bir eylem grubu, bir Azure aboneliğinin sahibi tarafından tanımlanan bildirim tercihleri koleksiyonudur. Azure Izleyici ve hizmet durumu uyarıları, kullanıcılara bir uyarının tetiklendiğini bildirmek için eylem gruplarını kullanır. Çeşitli uyarılar, kullanıcının gereksinimlerine bağlı olarak aynı eylem grubunu veya farklı eylem gruplarını kullanabilir. Bir abonelikte en fazla 2.000 eylem grubu yapılandırabilirsiniz.

E-posta veya SMS ile bir kişiye bildirimde bulunan bir eylem yapılandırdığınızda, bunlar eylem grubuna eklendiğini belirten bir onay alırlar.

Bu makalede Azure portal eylem gruplarının nasıl oluşturulacağı ve yönetileceği gösterilmektedir.

Her eylem aşağıdaki özelliklerden oluşur:

* **Ad**: Eylem grubu içindeki benzersiz bir tanımlayıcı.  
* **Eylem türü**: Gerçekleştirilen eylem. Bir sesli çağrı, SMS, e-posta gönderme örnekleri aşağıda verilmiştir. veya çeşitli otomatikleştirilmiş eylem türlerini tetikleyerek. Bu makalenin ilerleyen kısımlarında bulunan türlere bakın.
* **Ayrıntılar**: *Eylem türüne*göre farklılık gösteren ilgili ayrıntılar.

Eylem gruplarını yapılandırmak için Azure Resource Manager şablonlarını kullanma hakkında daha fazla bilgi için bkz. [eylem grubu Kaynak Yöneticisi şablonları](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Azure portal kullanarak bir eylem grubu oluşturun

1. [Azure Portal](https://portal.azure.com), **İzle**' yi seçin. **İzleyici** bölmesi tüm izleme ayarlarınızı ve verilerinizi tek bir görünümde birleştirir.

    !["Izleyici" hizmeti](./media/action-groups/home-monitor.png)
    
1. **Uyarıları** seçin ve ardından **eylemleri Yönet**' i seçin.

    ![Eylemleri Yönet düğmesi](./media/action-groups/manage-action-groups.png)
    
1. **Eylem grubu Ekle**' yi seçin ve alanları girin.

    !["Eylem grubu Ekle" komutu](./media/action-groups/add-action-group.png)
    
1. **Eylem grubu adı** kutusuna bir ad girin ve **kısa ad** kutusuna bir ad girin. Bu eylem grubu kullanılarak bildirim gönderildiğinde tam grup adı yerine kısa ad kullanılır.

      ![Eylem grubu Ekle "iletişim kutusu](./media/action-groups/action-group-define.png)

1. **Abonelik** kutusu, geçerli aboneliğiniz ile oto doldurur. Bu abonelik, eylem grubunun kaydedildiği bir işlemdir.

1. Eylem grubunun kaydedildiği **kaynak grubunu** seçin.

1. Eylemlerin bir listesini tanımlayın. Her eylem için aşağıdakileri sağlayın:

    1. **Ad**: Bu eylem için benzersiz bir tanımlayıcı girin.

    1. **Eylem türü**: E-posta/SMS/Push/Voice, Logic App, Web kancası, ıSM veya Automation runbook 'U seçin.

    1. **Ayrıntılar**: Eylem türüne bağlı olarak bir telefon numarası, e-posta adresi, Web kancası URI 'SI, Azure uygulaması, ıTSM bağlantısı veya Otomasyon Runbook 'u girin. ITSM eylemi için, ayrıca ıTSM araclarınızın gerektirdiği **Iş öğesini** ve diğer alanları belirtin.
    
    1. **Ortak uyarı şeması**: Azure Izleyici 'deki tüm uyarı hizmetlerinde tek bir Genişletilebilir ve birleştirilmiş uyarı yüküne sahip olmanın avantajını sağlayan [ortak uyarı şemasını](https://aka.ms/commonAlertSchemaDocs)etkinleştirmeyi seçebilirsiniz.

1. Eylem grubunu oluşturmak için **Tamam ' ı** seçin.

## <a name="manage-your-action-groups"></a>Eylem gruplarınızı yönetin

Bir eylem grubu oluşturduktan sonra, **izleyici** bölmesinin **eylem grupları** bölümünde görünür. Yönetmek istediğiniz eylem grubunu seçin:

* Eylemleri ekleyin, düzenleyin veya kaldırın.
* Eylem grubunu silin.

## <a name="action-specific-information"></a>Eyleme özgü bilgiler

> [!NOTE]
> Aşağıdaki öğelerin her birinde sayısal limitleri [izlemek Için abonelik hizmeti sınırlarına](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-monitor-limits) bakın.  

### <a name="automation-runbook"></a>Otomasyon Runbook'u
Runbook yüklerinin sınırları için [Azure abonelik hizmeti sınırlarına](../../azure-subscription-service-limits.md) bakın.

Bir eylem grubunda sınırlı sayıda runbook eylemi olabilir. 

### <a name="azure-app-push-notifications"></a>Azure uygulaması Anında İletme Bildirimleri
Bir eylem grubunda sınırlı sayıda Azure uygulama eylemi olabilir.

### <a name="email"></a>Email
E-postalar aşağıdaki e-posta adreslerinden gönderilir. E-posta filtrelemesinin uygun şekilde yapılandırıldığından emin olun
- azure-noreply@microsoft.com
- azureemail-noreply@microsoft.com
- alerts-noreply@mail.windowsazure.com

Bir eylem grubunda sınırlı sayıda e-posta eylemi olabilir. Bkz. [hız sınırlandırma bilgileri](./../../azure-monitor/platform/alerts-rate-limiting.md) makalesi.

### <a name="email-azure-resource-manager-role"></a>E-posta Azure Resource Manager rolü
Abonelik rolü üyelerine e-posta gönderin.

Bir eylem grubunda sınırlı sayıda e-posta eylemi olabilir. Bkz. [hız sınırlandırma bilgileri](./../../azure-monitor/platform/alerts-rate-limiting.md) makalesi.

### <a name="function"></a>İşlev
Eylem olarak yapılandırılan Işlev uygulamalarının işlev anahtarları, "AzureWebJobsSecretStorageType" uygulama ayarını "dosyalar" olarak yapılandırmak için şu anda v2 işlev uygulamaları gerektiren Işlevler API 'SI aracılığıyla okunurdur. Daha fazla bilgi için bkz. [Işlevler v2 'de anahtar yönetimi değişiklikleri]( https://aka.ms/funcsecrets).

Bir eylem grubunda sınırlı sayıda Işlev eylemine sahip olabilirsiniz.

### <a name="itsm"></a>ITSM
ITSM eylemi bir ıTSM bağlantısı gerektiriyor. ITSM [bağlantısı](../../azure-monitor/platform/itsmc-overview.md)oluşturmayı öğrenin.

Bir eylem grubunda sınırlı sayıda ıSM eylemi olabilir. 

### <a name="logic-app"></a>Mantıksal Uygulama
Bir eylem grubunda sınırlı sayıda mantıksal uygulama eylemi olabilir.

### <a name="secure-webhook"></a>Güvenli Web kancası
**Güvenli Web kancası işlevselliği Şu anda önizleme aşamasındadır.**

Eylem grupları Web kancası eylemi, eylem grubunuz ve korumalı Web API 'niz (Web kancası uç noktası) arasındaki bağlantıyı güvenli hale getirmek için Azure Active Directory avantajlarından yararlanmanızı sağlar. Bu işlevden yararlanmak için genel iş akışı aşağıda açıklanmıştır. Azure AD uygulamalarına ve hizmet sorumlularına genel bakış için bkz. [Microsoft Identity platform (v 2.0) genel bakış](https://docs.microsoft.com/azure/active-directory/develop/v2-overview).

1. Korumalı Web API 'niz için bir Azure AD uygulaması oluşturun. Bkz. https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-overview.
    - Korumalı API 'nizi bir Daemon uygulaması tarafından çağrılacak şekilde yapılandırın.
    
1. Azure AD uygulamanızı kullanmak için eylem gruplarını etkinleştirin.

    > [!NOTE]
    > Bu betiği yürütmek için [Azure AD uygulama Yöneticisi rolünün](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#available-roles) bir üyesi olmanız gerekir.
    
    - PowerShell betiğinin Connect-AzureAD çağrısını Azure AD kiracı KIMLIĞINIZI kullanacak şekilde değiştirin.
    - PowerShell betiğinin değişken $myAzureADApplicationObjectId Azure AD uygulamanızın nesne KIMLIĞINI kullanacak şekilde değiştirin
    - Değiştirilen betiği çalıştırın.
    
1. Eylem grubu güvenli Web kancası eylemini yapılandırın.
    - Betikten $myApp. ObjectID değerini kopyalayın ve Web kancası eylem tanımındaki uygulama nesne KIMLIĞI alanına girin.
    
    ![Güvenli Web kancası eylemi](./media/action-groups/action-groups-secure-webhook.png)

#### <a name="secure-webhook-powershell-script"></a>Güvenli Web kancası PowerShell betiği

```PowerShell
Connect-AzureAD -TenantId "<provide your Azure AD tenant ID here>"
    
# This is your Azure AD Application's ObjectId. 
$myAzureADApplicationObjectId = "<the Object Id of your Azure AD Application>"
    
# This is the Action Groups Azure AD AppId
$actionGroupsAppId = "461e8683-5575-4561-ac7f-899cc907d62a"
    
# This is the name of the new role we will add to your Azure AD Application
$actionGroupRoleName = "ActionGroupsSecureWebhook"
    
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
$actionGroupsSP = Get-AzureADServicePrincipal -Filter ("appId eq '" + $actionGroupsAppId + "'")

Write-Host "App Roles before addition of new role.."
Write-Host $myAppRoles
    
# Create the role if it doesn't exist
if ($myAppRoles -match "ActionGroupsSecureWebhook")
{
    Write-Host "The Action Groups role is already defined.`n"
}
else
{
    $myServicePrincipal = Get-AzureADServicePrincipal -Filter ("appId eq '" + $myApp.AppId + "'")
    
    # Add our new role to the Azure AD Application
    $newRole = CreateAppRole -Name $actionGroupRoleName -Description "This is a role for Action Groups to join"
    $myAppRoles.Add($newRole)
    Set-AzureADApplication -ObjectId $myApp.ObjectId -AppRoles $myAppRoles
}
    
# Create the service principal if it doesn't exist
if ($actionGroupsSP -match "AzNS AAD Webhook")
{
    Write-Host "The Service principal is already defined.`n"
}
else
{
    # Create a service principal for the Action Groups Azure AD Application and add it to the role
    $actionGroupsSP = New-AzureADServicePrincipal -AppId $actionGroupsAppId
}
    
New-AzureADServiceAppRoleAssignment -Id $myApp.AppRoles[0].Id -ResourceId $myServicePrincipal.ObjectId -ObjectId $actionGroupsSP.ObjectId -PrincipalId $actionGroupsSP.ObjectId
    
Write-Host "My Azure AD Application ($myApp.ObjectId): " + $myApp.ObjectId
Write-Host "My Azure AD Application's Roles"
Write-Host $myApp.AppRoles
```

### <a name="sms"></a>SMS
Ek önemli bilgiler için bkz. bilgi ve [SMS uyarı davranışını](../../azure-monitor/platform/alerts-sms-behavior.md) [sınırlandırma](./../../azure-monitor/platform/alerts-rate-limiting.md) .

Bir eylem grubunda sınırlı sayıda SMS eylemi olabilir.  

### <a name="voice"></a>Ses
Bkz. [hız sınırlandırma bilgileri](./../../azure-monitor/platform/alerts-rate-limiting.md) makalesi.

Bir eylem grubunda sınırlı sayıda ses eylemi olabilir.

### <a name="webhook"></a>Web Kancası
Web kancaları aşağıdaki kurallar kullanılarak yeniden denenir. Aşağıdaki HTTP durum kodları döndürüldüğünde Web kancası çağrısı en fazla 2 kez yeniden denenir: 408, 429, 503, 504 veya HTTP uç noktası yanıt vermez. İlk yeniden deneme 10 saniye sonra yapılır. İkinci yeniden deneme 100 saniye sonra gerçekleşir. İki hatadan sonra, herhangi bir eylem grubu 30 dakika için uç noktayı çağırmaz. 

Kaynak IP adresi aralıkları
 - 13.72.19.232
 - 13.106.57.181
 - 13.106.54.3
 - 13.106.54.19
 - 13.106.38.142
 - 13.106.38.148
 - 13.106.57.196
 - 13.106.57.197
 - 52.244.68.117
 - 52.244.65.137
 - 52.183.31.0
 - 52.184.145.166
 - 51.4.138.199
 - 51.5.148.86
 - 51.5.149.19

Bu IP adreslerindeki değişikliklerle ilgili güncelleştirmeleri almak için, eylem grupları hizmeti hakkında bilgilendirici bildirimleri izleyen bir hizmet sistem durumu uyarısı yapılandırmanız önerilir.

Bir eylem grubunda sınırlı sayıda Web kancası eylemi olabilir.



## <a name="next-steps"></a>Sonraki adımlar
* [SMS uyarı davranışı](../../azure-monitor/platform/alerts-sms-behavior.md)hakkında daha fazla bilgi edinin.  
* [Etkinlik günlüğü uyarısı Web kancası şemasının anlaşılmasını](../../azure-monitor/platform/activity-log-alerts-webhook.md)elde edin.  
* [ITSM Bağlayıcısı](../../azure-monitor/platform/itsmc-overview.md) hakkında daha fazla bilgi edinin
* Uyarıların [hız sınırlaması](../../azure-monitor/platform/alerts-rate-limiting.md) hakkında daha fazla bilgi edinin.
* [Etkinlik günlüğü uyarılarına genel bir bakış](../../azure-monitor/platform/alerts-overview.md)elde edin ve uyarıları alma hakkında bilgi edinin.  
* [Bir hizmet durumu bildirimi gönderildiğinde uyarıların nasıl yapılandırılacağını](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)öğrenin.
