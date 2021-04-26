---
title: Denetim günlüklerine erişim ve gözden geçirme
titleSuffix: Azure AD B2C
description: Azure AD B2C denetim günlüklerine programlı olarak ve Azure portal erişme.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 04d5a989fcaf3cef3eea3b782842fb3255d4b317
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661111"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C denetim günlüklerine erişme

Azure Active Directory B2C (Azure AD B2C) B2C kaynakları, belirteçleri verilen belirteçler ve yönetici erişimi hakkındaki etkinlik bilgilerini içeren denetim günlüklerini yayar. Bu makalede, denetim günlüklerinde bulunan bilgilere kısa bir genel bakış ve Azure AD B2C kiracınız için bu verilere nasıl erişecağınız hakkında yönergeler sunulmaktadır.

Denetim günlüğü olayları yalnızca **yedi gün** boyunca tutulur. Daha uzun bir bekletme dönemi istiyorsanız, aşağıda gösterilen yöntemlerden birini kullanarak günlüklerinizi indirmeyi ve depolamayı planlayın.

> [!NOTE]
> Azure portal **Azure Active Directory** veya **Azure AD B2C** sayfaların **Kullanıcılar** bölümü altında bireysel Azure AD B2C uygulamaları için Kullanıcı oturum açma işlemlerini göremezsiniz. Oturum açma olayları Kullanıcı etkinliğini gösterir, ancak kullanıcının oturum açmadığı B2C uygulamasına geri alınamaz. Bu makalenin ilerleyen bölümlerinde açıklandığı gibi, Denetim günlüklerini bu şekilde kullanmanız gerekir.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Denetim günlüklerinin B2C kategorisinde bulunan etkinliklere genel bakış

Denetim günlüklerinde **B2C** kategorisi aşağıdaki etkinlik türlerini içerir:

|Etkinlik türü |Description  |
|---------|---------|
|Yetkilendirme |Bir kullanıcının B2C kaynaklarına (örneğin, B2C ilkeleri listesine erişen bir yönetici) erişmesine yönelik yetkilendirmeyle ilgili etkinlikler.         |
|Dizin |Bir yönetici Azure portal kullanarak oturum açtığında alınan dizin öznitelikleriyle ilgili etkinlikler. |
|Uygulama | B2C uygulamalarında oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri. |
|Anahtar |B2C anahtar kapsayıcısında depolanan anahtarlarla CRUD işlemleri. |
|Kaynak |B2C kaynaklarında CRUD işlemleri. Örneğin, ilkeler ve kimlik sağlayıcıları.
|Kimlik Doğrulaması |Kullanıcı kimlik bilgilerinin ve belirteç verme 'nin doğrulanması.|

Kullanıcı nesnesi CRUD etkinlikleri için **çekirdek Dizin** kategorisine bakın.

## <a name="example-activity"></a>Örnek etkinlik

Bu örnek Azure portal görüntüsü, bir Kullanıcı bir dış kimlik sağlayıcısıyla oturum açtığında yakalanan verileri gösterir, bu durumda Facebook:

![Azure portal içindeki denetim günlüğü etkinlik ayrıntıları sayfası örneği](./media/view-audit-logs/audit-logs-example.png)

Etkinlik ayrıntıları paneli aşağıdaki ilgili bilgileri içerir:

|Section|Alan|Açıklama|
|-------|-----|-----------|
| Etkinlik | Name | Hangi etkinlik gerçekleşti. Örneğin, uygulamayı gerçek Kullanıcı oturum açma sonucuna göre *bir id_token olarak verin*. |
| Başlatan (aktör) | ObjectId | Kullanıcının oturum açtığı B2C uygulamasının **nesne kimliği** . Bu tanımlayıcı Azure portal görünmez, ancak Microsoft Graph API 'SI aracılığıyla erişilebilir. |
| Başlatan (aktör) | SPN | Kullanıcının oturum açtığı B2C uygulamasının **uygulama kimliği** . |
| Hedefler | ObjectId | Oturum açan kullanıcının **nesne kimliği** . |
| Ek ayrıntılar | TenantId | Azure AD B2C kiracının **KIRACı kimliği** . |
| Ek ayrıntılar | PolicyId | Kullanıcının oturumu açmak için kullanılan Kullanıcı akışının (ilke) **Ilke kimliği** . |
| Ek ayrıntılar | ApplicationID | Kullanıcının oturum açtığı B2C uygulamasının **uygulama kimliği** . |

## <a name="view-audit-logs-in-the-azure-portal"></a>Azure portal denetim günlüklerini görüntüleme

Azure portal, Azure AD B2C kiracınızdaki denetim günlüğü olaylarına erişim sağlar.

1. [Azure portalda](https://portal.azure.com) oturum açma
1. Azure AD B2C kiracınızı içeren dizine geçin ve **Azure AD B2C** gidin.
1. Sol menüdeki **Etkinlikler** ' in altında, **Denetim günlükleri**' ni seçin.

Son yedi gün içinde günlüğe kaydedilen etkinlik olaylarının listesi görüntülenir.

![Azure portal iki etkinlik olayına sahip örnek filtre](./media/view-audit-logs/audit-logs-example-filter.png)

Aşağıdakiler dahil olmak üzere çeşitli filtreleme seçenekleri mevcuttur:

* **Etkinlik kaynak türü** - [kullanılabilir etkinlikler bölümüne genel bakış](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) bölümünde tabloda gösterilen etkinlik türlerine göre filtreleyin.
* **Tarih** -gösterilen etkinliklerin tarih aralığını filtreleyin.

Listeden bir satır seçerseniz, olay için etkinlik ayrıntıları görüntülenir.

Etkinlik olaylarının listesini bir virgülle ayrılmış değerler (CSV) dosyasında indirmek için **İndir**' i seçin.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Azure AD Raporlama API 'SI ile denetim günlüklerini alın

Denetim günlükleri, Azure Active Directory için diğer etkinliklerle aynı işlem hattına yayımlanır, bu nedenle [Azure Active Directory Raporlama API 'si](/graph/api/directoryaudit-list)üzerinden erişilebilirler. Daha fazla bilgi için bkz. [Azure Active Directory Raporlama API 'si ile çalışmaya başlama](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Raporlama API 'SI erişimini etkinleştir

Azure AD Raporlama API 'sine betik veya uygulama tabanlı erişime izin vermek için, aşağıdaki API izinleriyle Azure AD B2C kiracınızda kayıtlı bir uygulamaya ihtiyacınız vardır. Bu izinleri B2C kiracınızda var olan bir uygulama kaydında etkinleştirebilir veya bir özel olarak denetim günlüğü otomasyonu ile kullanmak için yeni bir tane oluşturabilirsiniz.

* Microsoft Graph > uygulama izinleri > AuditLog > Denetimgünlüğü. Read. All

Gerekli izinlere sahip bir uygulamayı kaydetmek için aşağıdaki makaledeki adımları izleyin:

[Microsoft Graph ile Azure AD B2C yönetme](microsoft-graph-get-started.md)

Uygun izinlerle bir uygulamayı kaydettikten sonra, bir komut dosyası ile etkinlik olaylarını nasıl alabileceğiniz hakkında bir örnek için bu makalenin ilerleyen kısımlarında bulunan PowerShell betiği bölümüne bakın.

### <a name="access-the-api"></a>API'ye erişme

API aracılığıyla Azure AD B2C denetim günlüğü olaylarını indirmek için, kategorideki günlüklere filtre uygulayın `B2C` . Kategoriye göre filtrelemek için, `filter` Azure AD Raporlama API uç noktasını çağırdığınızda sorgu dizesi parametresini kullanın.

```http
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell betiği

Aşağıdaki PowerShell betiği, Azure AD Raporlama API 'sinin nasıl sorgulanalınacağını gösteren bir örnek gösterir. API sorgulandıktan sonra, günlüğe kaydedilen olayları standart çıktıya yazdırır, ardından JSON çıkışını bir dosyaya yazar.

Bu betiği [Azure Cloud Shell](overview.md)deneyebilirsiniz. Bunu uygulama KIMLIĞINIZ, gizli anahtar ve Azure AD B2C kiracınızın adı ile güncelleştirdiğinizden emin olun.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

Makalenin önceki kısımlarında gösterilen örnek etkinlik olayının JSON temsili aşağıda verilmiştir:

```json
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Diğer yönetim görevlerini otomatikleştirebilir, örneğin, [Microsoft Graph Azure AD B2C Kullanıcı hesaplarını yönetebilirsiniz](microsoft-graph-operations.md).