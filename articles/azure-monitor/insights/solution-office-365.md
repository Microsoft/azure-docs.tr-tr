---
title: Azure 'da Office 365 yönetim çözümü | Microsoft Docs
description: Bu makalede, Azure 'da Office 365 çözümünün yapılandırması ve kullanımıyla ilgili ayrıntılar sağlanmaktadır.  Azure Izleyici 'de oluşturulan Office 365 kayıtlarının ayrıntılı açıklamasını içerir.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 84af0484ed9fb792bef6bbbe9c53395b569acb3c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793860"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Azure 'da Office 365 yönetim çözümü (Önizleme)

![Office 365 logosu](media/solution-office-365/icon.png)


> [!NOTE]
> Office 365 çözümünü yüklemek ve yapılandırmak için önerilen yöntem, bu makaledeki adımları kullanmak yerine [Azure Sentinel](../../sentinel/overview.md) 'de [Office 365 bağlayıcısını](../../sentinel/connect-office-365.md) etkinleştirir. Bu, geliştirilmiş bir yapılandırma deneyimiyle birlikte Office 365 çözümünün güncelleştirilmiş bir sürümüdür. Azure AD günlüklerini bağlamak için, [Azure Sentinel Azure AD bağlayıcısını](../../sentinel/connect-azure-active-directory.md) kullanabilir veya [Azure AD tanılama ayarlarını yapılandırarak](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)Office 365 Yönetim günlüklerinden daha zengin günlük verileri sağlayabilirsiniz. 
>
> [Azure Sentinel](../../sentinel/quickstart-onboard.md)'i eklediğinizde, Office 365 çözümünün yüklenmesini istediğiniz Log Analytics çalışma alanını belirtin. Bağlayıcıyı etkinleştirdikten sonra çözüm, çalışma alanında kullanılabilir olur ve yüklediğiniz diğer izleme çözümleriyle tamamen aynı şekilde kullanılır.
>
> Azure Sentinel 'in kamu bulutunda henüz kullanılamadığından, Azure Kamu Bulutu kullanıcıları bu makaledeki adımları kullanarak Office 365 ' i yüklemelidir.

Office 365 yönetimi çözümü, Azure Izleyici 'de Office 365 ortamınızı izlemenize olanak sağlar.

- Kullanım düzenlerini çözümlemek ve davranış eğilimlerini belirlemek için Office 365 hesaplarınızdaki Kullanıcı etkinliklerini izleyin. Örneğin, kuruluşunuz dışında paylaşılan dosyalar veya en popüler SharePoint siteleri gibi belirli kullanım senaryolarını ayıklayabilirsiniz.
- Yapılandırma değişikliklerini veya yüksek ayrıcalıklı işlemleri izlemek için yönetici etkinliklerini izleyin.
- Kurumsal gereksinimleriniz için özelleştirilebilen, istenmeyen kullanıcı davranışını algılayıp araştırın.
- Denetim ve uyumluluğu gösterir. Örneğin, gizli dosyalardaki dosya erişim işlemlerini, denetim ve uyumluluk sürecinde size yardımcı olabilecek şekilde izleyebilirsiniz.
- Kuruluşunuzun Office 365 etkinlik verilerinin en üstünde bulunan [günlük sorgularını](../log-query/log-query-overview.md) kullanarak işlem sorunlarını giderme işlemi gerçekleştirin.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu çözüm yüklenmeden ve yapılandırıldıktan önce aşağıdakiler gereklidir.

- Kurumsal Ofis 365 aboneliği.
- Genel yönetici olan bir kullanıcı hesabının kimlik bilgileri.
- Denetim verileri almak için Office 365 aboneliğinizdeki [denetimi yapılandırmanız](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) gerekir.  [Posta kutusu denetiminin](https://technet.microsoft.com/library/dn879651.aspx) ayrı olarak yapılandırıldığını unutmayın.  Hala çözümü yükleyebilir ve denetim yapılandırılmamışsa diğer verileri toplayabilirsiniz.
 

## <a name="management-packs"></a>Yönetim paketleri

Bu çözüm [bağlı yönetim gruplarına](../platform/om-agents.md)herhangi bir yönetim paketi yüklemez.
  

## <a name="install-and-configure"></a>Yükleme ve yapılandırma

[Aboneliğinize Office 365 çözümünü](solutions.md#install-a-monitoring-solution)ekleyerek başlayın. Eklendikten sonra, Office 365 aboneliğinize erişim sağlamak için bu bölümdeki yapılandırma adımlarını gerçekleştirmeniz gerekir.

### <a name="required-information"></a>Gerekli bilgiler

Bu yordama başlamadan önce, aşağıdaki bilgileri toplayın.

Log Analytics çalışma alanınızdan:

- Çalışma alanı adı: Office 365 verilerinin toplanacağı çalışma alanı.
- Kaynak grubu adı: çalışma alanını içeren kaynak grubu.
- Azure abonelik KIMLIĞI: çalışma alanını içeren abonelik.

Office 365 aboneliğinizden:

- Kullanıcı adı: yönetici hesabının e-posta adresi.
- Kiracı KIMLIĞI: Office 365 aboneliğinin benzersiz KIMLIĞI.

Azure Active Directory 'de Office 365 uygulamasının oluşturulması ve yapılandırılması sırasında aşağıdaki bilgiler toplanmalıdır:

- Uygulama (Istemci) KIMLIĞI: Office 365 istemcisini temsil eden 16 karakterlik dize.
- İstemci parolası: kimlik doğrulaması için şifreli dize gereklidir.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Azure Active Directory bir Office 365 uygulaması oluşturma

İlk adım, yönetim çözümünün Office 365 çözümünüze erişmek için kullanacağı Azure Active Directory bir uygulama oluşturmaktır.

1. [https://portal.azure.com](https://portal.azure.com/) adresinden Azure portalında oturum açın.
1. **Azure Active Directory** seçip **uygulama kayıtları**.
1. **Yeni kayıt**' ye tıklayın.

    ![Uygulama kaydı ekle](media/solution-office-365/add-app-registration.png)
1. Bir uygulama **adı**girin. **Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde (HERHANGI bir Azure ad dizini-Multitenant) hesaplar '** ı seçin.
    
    ![Uygulama oluştur](media/solution-office-365/create-application.png)
1. **Kaydet** ' e tıklayın ve uygulama bilgilerini doğrulayın.

    ![Kayıtlı uygulama](media/solution-office-365/registered-app.png)

1. Uygulama (istemci) KIMLIĞINI, daha önce toplanan bilgilerin geri kalanı ile birlikte kaydedin.


### <a name="configure-application-for-office-365"></a>Office 365 için uygulama yapılandırma

1. **Kimlik doğrulaması** ' nı seçin ve **Desteklenen hesap türleri**altında **herhangi bir kuruluş DIZININDEKI (herhangi bir Azure ad dizini-Multitenant) hesapların** seçildiğini doğrulayın.

    ![Ayarlar çok kiracılı](media/solution-office-365/settings-multitenant.png)

1. **API izinleri** ' ni seçin ve ardından **bir izin ekleyin**.
1. **Office 365 Yönetim API 'leri**' ne tıklayın. 

    ![API seçin](media/solution-office-365/select-api.png)

1. **Uygulamanız gereken izin türü altında?** uygulama **izinleri** ve **temsilci izinleri**için aşağıdaki seçenekleri belirtin:
   - Kuruluşunuzun hizmet durumu bilgilerini okuyun
   - Kuruluşunuzun etkinlik verilerini okuyun
   - Kuruluşunuz için etkinlik raporlarını okuyun

     ![API seçin](media/solution-office-365/select-permissions-01.png)![API seçin](media/solution-office-365/select-permissions-02.png)

1. **Izin Ekle**' ye tıklayın.
1. **Yönetici onayı ver** ' e tıklayın ve doğrulama istendiğinde **Evet** ' e tıklayın.


### <a name="add-a-secret-for-the-application"></a>Uygulama için gizli dizi ekleme

1. **Sertifikalar &** ve ardından **yeni istemci parolası**' nı seçin.

    ![Anahtarlar](media/solution-office-365/secret.png)
 
1. Yeni anahtar için bir **Açıklama** ve **süre** yazın.
1. **Ekle** ' ye tıklayın ve daha önce toplanan bilgilerin geri kalanı ile birlikte, istemci gizli dizisi olarak oluşturulan **değeri** kaydedin.

    ![Anahtarlar](media/solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Yönetici onayı Ekle

Yönetim hesabını ilk kez etkinleştirmek için, uygulama için yönetici onayı sağlamanız gerekir. Bunu bir PowerShell betiği ile yapabilirsiniz. 

1. Aşağıdaki betiği *office365_consent. ps1*olarak kaydedin.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us'; break}
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Betiği aşağıdaki komutla çalıştırın. Kimlik bilgileri için iki kez istemde bulunulacaktır. Önce Log Analytics çalışma alanınızın kimlik bilgilerini ve ardından Office 365 kiracınızın genel yönetici kimlik bilgilerini sağlayın.

    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```

    Örnek:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Aşağıda gösterilene benzer bir pencere sunulacaktır. **Kabul et**' e tıklayın.
    
    ![Yönetici onayı](media/solution-office-365/admin-consent.png)

> [!NOTE]
> Mevcut olmayan bir sayfaya yönlendiriliyorsunuz. Bunu başarılı olarak değerlendirin.

### <a name="subscribe-to-log-analytics-workspace"></a>Log Analytics çalışma alanına abone ol

Son adım, uygulamayı Log Analytics çalışma alanınıza Abone olunacak. Bunu bir PowerShell betiği ile de yapabilirsiniz.

Son adım, uygulamayı Log Analytics çalışma alanınıza Abone olunacak. Bunu bir PowerShell betiği ile de yapabilirsiniz.

1. Aşağıdaki betiği *office365_subscription. ps1*olarak kaydedin.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }

    Function RESTAPI-Auth { 
    $global:SubscriptionID = $Subscription.Subscription.Id
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()

    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory,Audit.SharePoint'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Betiği aşağıdaki komutla çalıştırın:

    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```

    Örnek:

    ```powershell
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troubleshooting"></a>Sorun giderme

Uygulamanız zaten bu çalışma alanına abone varsa veya bu kiracı başka bir çalışma alanına abone olduysa, aşağıdaki hatayı görebilirsiniz.

```Output
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Geçersiz parametre değerleri sağlanmışsa, aşağıdaki hatayı görebilirsiniz.

```Output
Select-AzSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzContextCommand

```

## <a name="uninstall"></a>Kaldırma

[Yönetim çözümünü kaldırma](solutions.md#remove-a-monitoring-solution)' daki Işlemi kullanarak Office 365 Yönetim çözümünü kaldırabilirsiniz. Bu, Office 365 ' den toplanan verileri de Azure Izleyici 'ye durdurmayacak. Office 365 aboneliğinizi kaldırmak ve veri toplamayı durdurmak için aşağıdaki yordamı izleyin.

1. Aşağıdaki betiği *office365_unsubscribe. ps1*olarak kaydedin.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Betiği aşağıdaki komutla çalıştırın:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Örnek:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Veri toplama

### <a name="supported-agents"></a>Desteklenen aracılar

Office 365 çözümü [Log Analytics aracılarından](../platform/agent-data-sources.md)herhangi birinden veri almaz.  Verileri doğrudan Office 365 'ten alır.

### <a name="collection-frequency"></a>Toplama sıklığı

Verilerin başlangıçta toplanması birkaç saat sürebilir. Toplamaya başladıktan sonra, Office 365 bir kayıt oluşturulduğunda ayrıntılı verilerle Azure Izleyici 'ye bir [Web kancası bildirimi](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) gönderir. Bu kayıt, Azure Izleyici 'de alındıktan sonra birkaç dakika içinde kullanılabilir.

## <a name="using-the-solution"></a>Çözümü kullanma

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Office 365 çözümünü Log Analytics çalışma alanınıza eklediğinizde panonuza **office 365** kutucuğu eklenecektir. Bu kutucukta, ortamınızdaki bilgisayarların sayısına ve güncelleştirme uyumluluğuna ilişkin bir sayı ve grafik gösterimi görüntülenir.<br><br>
![Office 365 Özet kutucuğu](media/solution-office-365/tile.png)  

Office **365** panosunu açmak için **Office 365** kutucuğuna tıklayın.

![Office 365 panosu](media/solution-office-365/dashboard.png)  

Pano aşağıdaki tabloda gösterilen sütunları içerir. Her sütunda, belirtilen kapsam ve zaman aralığı için bu sütun ölçütlerine uyan en üstteki on uyarı, sayıma göre listelenir. Listenin en altında bulunan tümünü görüntüle ' ye tıklayarak veya sütun başlığına tıklayarak, tüm listeyi sağlayan bir günlük araması çalıştırabilirsiniz.

| Sütun | Açıklama |
|:--|:--|
| Operations | Tüm izlenen Office 365 aboneliklerinizden etkin kullanıcılar hakkında bilgiler sağlar. Ayrıca, zaman içinde gerçekleşen etkinlik sayısını da görebileceksiniz.
| Değiştirin | Posta kutusu ekleme Izni veya Set-Mailbox gibi Exchange Server etkinliklerinin dökümünü gösterir. |
| SharePoint | Kullanıcıların SharePoint belgelerinde gerçekleştirdiği en iyi etkinlikleri gösterir. Bu kutucuktan ayrıntıya indığınızda arama sayfasında, bu etkinliklerin hedef belge ve bu etkinliğin konumu gibi ayrıntıları gösterilir. Örneğin, dosya erişimli bir olay için, erişilen belgeye, ilişkili hesap adına ve IP adresine bakabilirsiniz. |
| Azure Active Directory | Kullanıcı parolası ve oturum açma girişimlerini sıfırlama gibi ilk kullanıcı etkinliklerini içerir. Ayrıntıya gitmediğiniz zaman, bu etkinliklerin sonuç durumu gibi ayrıntılarını görebileceksiniz. Bu, genellikle Azure Active Directory şüpheli etkinlikleri izlemek istediğinizde yararlı olur. |




## <a name="azure-monitor-log-records"></a>Azure Izleyici günlük kayıtları

Office 365 çözümü tarafından Azure Izleyici 'de Log Analytics çalışma alanında oluşturulan tüm kayıtlar bir **tür** **officeetkinliğine**sahiptir.  **Officeworkload** özelliği, kaydın hangi Office 365 hizmetini (Exchange, AzureActiveDirectory, SharePoint veya OneDrive) başvurduğunu belirler.  **RecordType** özelliği işlem türünü belirtir.  Özellikler her işlem türü için farklılık gösterir ve aşağıdaki tablolarda gösterilir.

### <a name="common-properties"></a>Ortak özellikler

Aşağıdaki özellikler tüm Office 365 kayıtları için ortaktır.

| Özellik | Açıklama |
|:--- |:--- |
| Tür | *Officeetkinliği* |
| ClientIP | Etkinlik günlüğe kaydedildiğinde kullanılan cihazın IP adresi. IP adresi bir IPv4 veya IPv6 adresi biçiminde görüntülenir. |
| Officeiş yükü | Kaydın başvurduğu Office 365 hizmeti.<br><br>AzureActiveDirectory<br>Değiştirin<br>SharePoint|
| İşlem | Kullanıcı veya yönetici etkinliğinin adı.  |
| Kuruluş kimliği | Kuruluşunuzun Office 365 kiracısı için GUID. Bu değer, gerçekleştiği Office 365 hizmetine bakılmaksızın kuruluşunuzun her zaman aynı olacaktır. |
| RecordType | Gerçekleştirilen işlem türü. |
| ResultStatus | Eylemin (Işlem özelliğinde belirtilen) başarılı olup olmadığını gösterir. Olası değerler başarılı, PartiallySucceeded veya başarısız. Exchange yönetici etkinliği için değer true ya da false şeklindedir. |
| UserID | Günlüğe kaydedilen kayda neden olan eylemi gerçekleştiren kullanıcının UPN (Kullanıcı asıl adı); Örneğin, my_name@my_domain_name. Sistem hesapları tarafından gerçekleştirilen etkinlik kayıtlarının (örneğin, SHAREPOINT\system veya NTAUTHORITY\SYSTEM ADLı) da dahil edildiğini unutmayın. | 
| UserKey | UserID özelliğinde tanımlanan Kullanıcı için alternatif bir KIMLIK.  Örneğin, bu özellik SharePoint, OneDrive Iş ve Exchange kullanıcıları tarafından gerçekleştirilen olaylar için Passport benzersiz KIMLIĞI (PUıD) ile doldurulur. Bu özellik aynı zamanda diğer hizmetlerde gerçekleşen olaylar ve sistem hesapları tarafından gerçekleştirilen olaylar için UserID özelliği ile aynı değeri belirtebilir|
| userType | İşlemi gerçekleştiren kullanıcı türü.<br><br>Yöneticileri<br>Uygulama<br>DcAdmin<br>Aralıklarla<br>Ayrılmış<br>ServicePrincipal<br>Sistem |


### <a name="azure-active-directory-base"></a>Azure Active Directory taban

Aşağıdaki özellikler tüm Azure Active Directory kayıtları için ortaktır.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD olayının türü. |
| extendedProperties | Azure AD olayının genişletilmiş özellikleri. |


### <a name="azure-active-directory-account-logon"></a>Hesap oturum Azure Active Directory

Bu kayıtlar Active Directory bir Kullanıcı oturum açmayı denediğinde oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | Office 15 gibi hesap oturum açma olayını tetikleyen uygulama. |
| `Client` | Hesap oturum açma olayı için kullanılan istemci aygıtı, cihaz işletim sistemi ve cihaz tarayıcısı hakkında ayrıntılar. |
| `LoginStatus` | Bu özellik doğrudan OrgIdLogon. LoginStatus öğesinden. Çeşitli ilginç oturum açma hatalarının eşleştirmesi uyarı algoritmaları tarafından gerçekleştirilebilir. |
| `UserDomain` | Kiracı kimlik bilgileri (TIı). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory nesnelerinde değişiklik veya eklemeler yapıldığında bu kayıtlar oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Eylemin (Operation özelliği tarafından tanımlanan) üzerinde gerçekleştirildiği Kullanıcı. |
| Aktör | Eylemi gerçekleştiren kullanıcı veya hizmet sorumlusu. |
| Actorcontextıd | Aktörün ait olduğu kuruluşun GUID 'SI. |
| Actorpaddress | Aktör, ıPV4 veya ıPV6 adresi biçimindeki IP adresidir. |
| Intersystemsıd | Office 365 hizmeti içindeki bileşenler arasında eylemleri izleyen GUID. |
| Insystemıd |   Eylemi izlemek için Azure Active Directory tarafından oluşturulan GUID. |
| Supportticketıd | "Adına göre hareket etme" durumlarında eyleme ilişkin müşteri destek bileti KIMLIĞI. |
| Targetcontextıd | Hedeflenen kullanıcının ait olduğu kuruluşun GUID 'SI. |


### <a name="data-center-security"></a>Veri merkezi güvenliği

Bu kayıtlar, veri merkezi güvenlik denetim verilerinden oluşturulur.  

| Özellik | Açıklama |
|:--- |:--- |
| Efekt organizasyonu | Yükseltme/cmdlet 'in hedeflediği kiracının adı. |
| Yükseltme zamanı | Yükseltme onaylandığı zaman damgası. |
| Onaylayan yükseltme | Bir Microsoft yöneticisinin adı. |
| Yükseltme süresi | Yükseltme etkin olduğu süre. |
| Yükseltme RequestId |  Yükseltme isteği için benzersiz bir tanımlayıcı. |
| Yükseltme rolü | Yükseltme için istenen rol. |
| Yükseltme zamanı | Yükseltme başlangıç saati. |
| Start_Time | Cmdlet yürütmenin başlangıç saati. |


### <a name="exchange-admin"></a>Exchange Yöneticisi

Bu kayıtlar, Exchange yapılandırmasında değişiklik yapıldığında oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | Değiştirin |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Cmdlet 'inin kuruluşunuzdaki bir kullanıcı tarafından, Microsoft veri merkezi personeli veya bir veri merkezi hizmet hesabı tarafından mı yoksa yetkilendirilmiş bir yönetici tarafından mı çalıştırılacağını belirtir. False değeri, cmdlet 'inin kuruluşunuzdaki bir kişi tarafından çalıştırıldığını gösterir. True değeri, cmdlet 'in veri merkezi personeli, bir veri merkezi hizmet hesabı veya yönetici temsilcisi tarafından çalıştırıldığını belirtir. |
| ModifiedObjectResolvedName |  Bu, cmdlet tarafından değiştirilen nesnenin Kullanıcı dostu adıdır. Bu, yalnızca cmdlet nesneyi değiştirdiğinde günlüğe kaydedilir. |
| © | Kiracının adı. |
| OriginatingServer | Cmdlet 'in yürütüldüğü sunucunun adı. |
| Parametreler | Operations özelliğinde tanımlanan cmdlet ile kullanılan tüm parametrelerin adı ve değeri. |


### <a name="exchange-mailbox"></a>Exchange posta kutusu

Exchange posta kutularına değişiklik veya eklemeler yapıldığında bu kayıtlar oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | Değiştirin |
| RecordType     | Exchangeıtem |
| Clientınfostring | Bir tarayıcı sürümü, Outlook sürümü ve mobil cihaz bilgileri gibi, işlemi gerçekleştirmek için kullanılan e-posta istemcisiyle ilgili bilgiler. |
| Client_IPAddress | İşlem günlüğe kaydedildiğinde kullanılan cihazın IP adresi. IP adresi bir IPv4 veya IPv6 adresi biçiminde görüntülenir. |
| ClientMachineName | Outlook istemcisini barındıran makine adı. |
| ClientProcessName | Posta kutusuna erişmek için kullanılan e-posta istemcisi. |
| ClientVersion | E-posta istemcisinin sürümü. |
| Internallogontype | Dahili kullanım için ayrılmıştır. |
| Logon_Type | Posta kutusuna erişen ve günlüğe kaydedilen işlemi gerçekleştiren kullanıcı türünü gösterir. |
| LogonUserDisplayName |    İşlemi gerçekleştiren kullanıcının kolay adı. |
| LogonUserSid | İşlemi gerçekleştiren kullanıcının SID 'SI. |
| MailboxGuid | Erişilen posta kutusunun Exchange GUID 'ı. |
| MailboxOwnerMasterAccountSid | Posta kutusu sahibi hesabının ana hesap SID 'SI. |
| MailboxOwnerSid | Posta kutusu sahibinin SID 'SI. |
| MailboxOwnerUPN | Erişilen posta kutusunun sahibi olan kişinin e-posta adresi. |


### <a name="exchange-mailbox-audit"></a>Exchange posta kutusu denetimi

Bu kayıtlar, bir posta kutusu denetim girişi oluşturulduğunda oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | Değiştirin |
| RecordType     | Exchangeıtem |
| Öğe | İşlemin gerçekleştirildiği öğeyi temsil eder | 
| SendAsUserMailboxGuid 'Si | E-posta göndermek için erişilen posta kutusunun Exchange GUID 'ı. |
| SendAsUserSmtp | Kimliğine bürünülen kullanıcının SMTP adresi. |
| Sendonbenoktalı Ofusermailboxguid | Adına posta gönderilirken erişilen posta kutusunun Exchange GUID 'ı. |
| Sendonbenoktalı Ofusersmtp | Adına e-posta gönderilen kullanıcının SMTP adresi. |


### <a name="exchange-mailbox-audit-group"></a>Exchange posta kutusu denetim grubu

Bu kayıtlar, Exchange gruplarında değişiklik veya eklemeler yapıldığında oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | Değiştirin |
| Officeiş yükü | Exchangeıtemgroup |
| AffectedItems | Gruptaki her öğe hakkında bilgi. |
| CrossMailboxOperations | İşlemin birden fazla posta kutusu ile ilişkili olup olmadığını gösterir. |
| Destmailboxıd | Yalnızca CrossMailboxOperations parametresi true ise ayarlanır. Hedef posta kutusu GUID 'sini belirtir. |
| DestMailboxOwnerMasterAccountSid | Yalnızca CrossMailboxOperations parametresi true ise ayarlanır. Hedef posta kutusu sahibinin ana hesap SID 'sinin SID 'sini belirtir. |
| DestMailboxOwnerSid | Yalnızca CrossMailboxOperations parametresi true ise ayarlanır. Hedef posta kutusunun SID 'sini belirtir. |
| DestMailboxOwnerUPN | Yalnızca CrossMailboxOperations parametresi true ise ayarlanır. Hedef posta kutusunun sahibinin UPN 'sini belirtir. |
| DestFolder | Taşıma gibi işlemler için hedef klasör. |
| Klasör | Bir öğe grubunun bulunduğu klasör. |
| Klasörler |     Bir işlemde yer alan kaynak klasörleriyle ilgili bilgiler; Örneğin, klasörler seçildiyse ve sonra silinirse. |


### <a name="sharepoint-base"></a>SharePoint temeli

Bu özellikler tüm SharePoint kayıtları için ortaktır.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | SharePoint |
| Officeiş yükü | SharePoint |
| EventSource | SharePoint 'te bir olayın oluştuğunu tanımlar. Olası değerler SharePoint veya ObjectModel. |
| ItemType | Erişilen veya değiştirilen nesnenin türü. Nesne türleriyle ilgili ayrıntılar için bkz. ItemType tablosu. |
| Machinedomainınfo | Cihaz eşitleme işlemleri hakkında bilgi. Bu bilgiler yalnızca istekte mevcutsa bildirilir. |
| MachineID |   Cihaz eşitleme işlemleri hakkında bilgi. Bu bilgiler yalnızca istekte mevcutsa bildirilir. |
| Bölgesi | Kullanıcının eriştiği dosya veya klasörün bulunduğu sitenin GUID 'SI. |
| Source_Name | Denetlenen işlemi tetikleyen varlık. Olası değerler SharePoint veya ObjectModel. |
| Kullanıcı | Kullanıcının istemcisi veya tarayıcısı hakkında bilgi. Bu bilgiler istemci veya tarayıcı tarafından sağlanır. |


### <a name="sharepoint-schema"></a>SharePoint şeması

Bu kayıtlar, SharePoint 'te yapılandırma değişiklikleri yapıldığında oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | SharePoint |
| Officeiş yükü | SharePoint |
| CustomEvent | Özel olaylar için isteğe bağlı dize. |
| Event_Data |  Özel olaylar için isteğe bağlı yük. |
| ModifiedProperties | Özelliği, bir kullanıcının bir site veya site koleksiyonu yönetici grubuna üye olarak eklenmesi gibi yönetim olaylarına dahildir. Özelliği, değiştirilen özelliğin adını (örneğin, site yönetici grubu), değiştirilen özelliğin yeni değerini (site yöneticisi olarak eklenen Kullanıcı gibi) ve değiştirilen nesnenin önceki değerini içerir. |


### <a name="sharepoint-file-operations"></a>SharePoint dosya Işlemleri

Bu kayıtlar, SharePoint 'teki dosya işlemlerine yanıt olarak oluşturulur.

| Özellik | Açıklama |
|:--- |:--- |
| Officeiş yükü | SharePoint |
| Officeiş yükü | SharePointFileOperation |
| Hedef dosya uzantısı | Kopyalanmış veya taşınmış bir dosyanın dosya uzantısı. Bu özellik yalnızca dosya kopyalanmış ve dosya taşınan olayları için görüntülenir. |
| Hedef dosya adı | Kopyalanmış veya taşınan dosyanın adı. Bu özellik yalnızca dosya kopyalanmış ve dosya taşınan olayları için görüntülenir. |
| DestinationRelativeUrl 'Si | Bir dosyanın kopyalandığı veya taşındığı hedef klasörün URL 'SI. SiteURL, DestinationRelativeURL ve DestinationFileName parametrelerinin değerlerinin birleşimi, kopyalanmış dosyanın tam yol adı olan ObjectID özelliğinin değeri ile aynıdır. Bu özellik yalnızca dosya kopyalanmış ve dosya taşınan olayları için görüntülenir. |
| SharingType | Kaynağın paylaşıldığı kullanıcıya atanan paylaşım izinlerinin türü. Bu Kullanıcı, UserSharedWith parametresi tarafından tanımlanır. |
| Site_Url | Kullanıcının eriştiği dosya veya klasörün bulunduğu sitenin URL 'SI. |
| SourceFileExtension | Kullanıcı tarafından erişilen dosyanın dosya uzantısı. Erişilen nesne bir klasöriyorsa, bu özellik boştur. |
| SourceFileName |  Kullanıcı tarafından erişilen dosyanın veya klasörün adı. |
| SourceRelativeUrl 'Si | Kullanıcı tarafından erişilen dosyayı içeren klasörün URL 'SI. SiteURL, SourceRelativeURL ve SourceFileName parametrelerinin değerlerinin birleşimi, Kullanıcı tarafından erişilen dosyanın tam yol adı olan ObjectID özelliğinin değeri ile aynıdır. |
| UserSharedWith |  Bir kaynağın paylaşıldığı Kullanıcı. |




## <a name="sample-log-searches"></a>Örnek günlük aramaları

Aşağıdaki tabloda, bu çözüm tarafından toplanan güncelleştirme kayıtlarına ilişkin örnek günlük aramaları sunulmaktadır.

| Sorgu | Açıklama |
| --- | --- |
|Office 365 aboneliğinizdeki tüm işlemlerin sayısı |OfficeActivity &#124; , işleme göre Count () özetleme |
|SharePoint sitelerinin kullanımı|Officeiş &#124; yükü = ~ "SharePoint \|" &#124; olan officeactivity|
|Kullanıcı türüne göre dosya erişim işlemleri|içinde ara (OfficeActivity) Officeiş yükü = ~ "azureactivedirectory" ve "MyTest"|
|Belirli bir anahtar sözcükle arama|Tür = OfficeActivity Officeiş yükü = azureactivedirectory "MyTest"|
|Exchange 'de dış eylemleri izleme|Officeiş &#124; yükü = ~ "Exchange" ve ExternalAccess = = true olduğunda officeetkinliği|



## <a name="next-steps"></a>Sonraki adımlar

* Ayrıntılı güncelleştirme verilerini görüntülemek için [Azure izleyici 'de günlük sorguları '](../log-query/log-query-overview.md) nı kullanın.
* En sevdiğiniz Office 365 arama sorgularını göstermek için [kendi panolarınızı oluşturun](../learn/tutorial-logs-dashboards.md) .
* Önemli Office 365 etkinliklerinden haberdar olmak için [uyarılar oluşturun](../platform/alerts-overview.md) .  
