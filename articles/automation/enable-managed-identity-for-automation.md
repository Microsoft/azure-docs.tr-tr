---
title: Azure Otomasyonu hesabınız için yönetilen bir kimliği etkinleştirme (Önizleme)
description: Bu makalede, Azure Otomasyonu hesapları için yönetilen kimliğin nasıl ayarlanacağı açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0416ce25ed03be35c56e7fa2ed7175ad65ae6555
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831555"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Azure Otomasyonu hesabınız için yönetilen bir kimliği etkinleştirme (Önizleme)

Bu konu başlığı altında, bir Azure Otomasyonu hesabı için yönetilen kimlik oluşturma ve diğer kaynaklara erişmek için nasıl kullanılacağı gösterilmektedir. Yönetilen kimliğin Azure Otomasyonu ile nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Yönetilen kimlikler](automation-security-overview.md#managed-identities-preview).

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure hesabı ve aboneliği Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. Hem yönetilen kimlik hem de runbook 'un bu kimliği kullanarak yönettiği hedef Azure kaynakları aynı Azure aboneliğinde olmalıdır.

- Azure Otomasyonu hesap modüllerinin en son sürümü. Şu anda 1.6.0. (Bu sürüm hakkındaki ayrıntılar için bkz. [az. Automation 1.6.0](https://www.powershellgallery.com/packages/Az.Automation/1.6.0) .)

- Otomasyon Runbook 'ınızdan erişmek istediğiniz bir Azure kaynağı. Bu kaynağın yönetilen kimlik için tanımlanmış bir rolü olması gerekir. Otomasyon Runbook 'unun kaynağa erişimi kimlik doğrulamasını sağlar. Rol eklemek için, ilgili Azure AD kiracısında kaynak için sahip olmanız gerekir.

- Karma işleri yönetilen bir kimlik kullanarak yürütmek istiyorsanız, karma runbook çalışanını en son sürüme güncelleştirin. Gerekli en düşük sürümler şunlardır:

   - Windows karma Runbook Worker: sürüm 7.3.1125.0
   - Linux karma Runbook Worker: sürüm 1.7.4.0

## <a name="enable-system-assigned-identity"></a>Sistem tarafından atanan kimliği etkinleştir

>[!IMPORTANT]
>Yeni Otomasyon hesabı düzeyi kimliği, önceki tüm VM düzeyinde sistem tarafından atanan kimlikleri geçersiz kılar ( [yönetilen kimliklerle runbook kimlik doğrulamasını kullanma](/automation-hrw-run-runbooks#runbook-auth-managed-identities)bölümünde açıklanmıştır). Runbook kaynaklarına erişmek için bir VM 'nin sistem tarafından atanan kimliğini kullanan Azure VM 'lerinde karma işler çalıştırıyorsanız, karma işler için Otomasyon hesabı kimliği kullanılacaktır. Bu, Otomasyon hesabınızın müşteri tarafından yönetilen anahtarlar (CMK) özelliğini kullanıyorsanız mevcut iş yürütmesinin etkilenebileceği anlamına gelir.<br/><br/>VM 'nin yönetilen kimliğini kullanmaya devam etmek istiyorsanız Otomasyon hesabı düzeyi kimliğini etkinleştirmezseniz. Zaten etkinleştirdiyseniz Otomasyon hesabı yönetilen kimliğini devre dışı bırakabilirsiniz. Bkz. [Azure Otomasyonu hesabı yönetilen kimliğinizi devre dışı bırakma](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation).

Azure Otomasyonu için sistem tarafından atanan kimliklerin ayarlanması iki farklı şekilde yapılabilir. Azure portal ya da Azure REST API kullanabilirsiniz.

>[!NOTE]
>Kullanıcı tarafından atanan kimlikler henüz desteklenmiyor.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure portal 'de sistem tarafından atanan kimliği etkinleştirme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Otomasyon hesabınıza gidin ve **Hesap ayarları** altında **kimlik** ' i seçin.

1. Sistem tarafından **atanan** seçeneğini **Açık** olarak ayarlayın ve **Kaydet**' e basın. Onaylamanız istendiğinde **Evet**' i seçin.

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Azure portal 'de sistem tarafından atanan kimlik etkinleştiriliyor.":::

Otomasyon hesabınız artık Azure Active Directory (Azure AD) ile kaydedilen ve bir nesne KIMLIĞIYLE temsil edilen sistem tarafından atanan kimliği kullanabilir.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="Yönetilen kimlik nesnesi KIMLIĞI.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>REST API aracılığıyla sistem tarafından atanan kimliği etkinleştirme

Aşağıdaki REST API çağrısını kullanarak, sistem tarafından atanan bir yönetilen kimliği Otomasyon hesabına yapılandırabilirsiniz.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

İstek gövdesi
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|Özellik (JSON) | Değer | Açıklama|
|----------|-----------|------------|
| PrincipalId | \<principal-ID\> | Azure AD kiracısında Otomasyon hesabınızı temsil eden yönetilen kimliğin hizmet sorumlusu nesnesinin genel benzersiz tanıtıcısı (GUID). Bu GUID bazen "nesne KIMLIĞI" veya ObjectID olarak görünür. |
| değerine | \<Azure-AD-tenant-ID\> | Otomasyon hesabının artık bir üyesi olduğu Azure AD kiracısını temsil eden genel benzersiz tanımlayıcı (GUID). Azure AD kiracısı içinde, hizmet sorumlusu Otomasyon hesabı ile aynı ada sahiptir. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>Belirteç alarak Azure kaynaklarına kimlik erişimi verme

Otomasyon hesabı, Azure Key Vault gibi Azure AD tarafından korunan diğer kaynaklara erişmek için belirteçleri almak üzere yönetilen kimliğini kullanabilir. Bu belirteçler uygulamanın belirli bir kullanıcısını temsil etmez. Bunun yerine, kaynağa erişen uygulamayı temsil eder. Bu durumda, örneğin, belirteç bir Otomasyon hesabını temsil eder.

Kimlik doğrulaması için sistem tarafından atanan yönetilen kimliğinizi kullanabilmeniz için, kimliği kullanmayı planladığınız Azure kaynağında bu kimlik için erişim ayarlayın. Bu görevi gerçekleştirmek için, hedef Azure kaynağında ilgili kimliğe uygun rolü atayın.

Bu örnek, abonelikte hedef Azure kaynağına katkıda bulunan rolünün nasıl atanacağını göstermek için Azure PowerShell kullanır. Katkıda bulunan rolü bir örnek olarak kullanılır ve bu durumda gerekli olmayabilir veya gerekli olmayabilir.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>Yönetilen kimlikle erişimin kimliğini doğrulama

Otomasyon hesabınız için yönetilen kimliği etkinleştirdikten ve hedef kaynağa bir kimlik erişimi verdiğinizde, runbook 'larda yönetilen kimliği destekleyen kaynaklara karşı bu kimliği belirtebilirsiniz. Kimlik desteği için az cmdlet `Connect-AzAccount` cmdlet 'ini kullanın. Bkz. PowerShell başvurusunda [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>Kuruluşunuz hala kullanım dışı Azurerd cmdlet 'lerini kullanıyorsa, kullanabilirsiniz `Connect-AzureRMAccount -Identity` .

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Azure cmdlet 'lerini kullanmadan bir erişim belirteci oluşturma

HTTP uç noktaları için aşağıdakilerden emin olun.
- Meta veri üst bilgisi mevcut olmalı ve "true" olarak ayarlanmalıdır.
- Bir kaynak, GET isteği için sorgu parametresi olarak ve POST isteği için form verileri olarak istekle birlikte geçirilmelidir.
- X-ıDENTITY-HEADER, karma runbook çalışanları için IDENTITY_HEADER ortam değişkeni değerine ayarlanmalıdır. 
- POST isteğinin içerik türü ' Application/x-www-form-urlencoded ' olmalıdır. 

### <a name="sample-get-request"></a>Örnek GET isteği

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>Örnek POST isteği
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>Yönetilen kimlik kullanan örnek runbook 'lar

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Azure cmdlet 'lerini kullanmadan bir SQL veritabanına erişmek için örnek runbook

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Azure cmdlet 'lerini kullanarak bir anahtar kasasına erişmek için örnek runbook

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>Belirteç almak için örnek Python runbook 'u
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>Sonraki adımlar

- Yönetilen bir kimliği devre dışı bırakmanız gerekirse, bkz. [Azure Otomasyonu hesabı yönetilen kimliğinizi devre dışı bırakma (Önizleme)](disable-managed-identity-for-automation.md).

- Azure Otomasyonu hesap güvenliğine genel bakış için bkz. [Otomasyon hesabı kimlik doğrulamasına genel bakış](automation-security-overview.md).
