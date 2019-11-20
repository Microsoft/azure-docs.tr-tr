---
title: Azure Key Vault yönetilen depolama hesabı-PowerShell sürümü
description: Yönetilen depolama hesabı özelliği, Azure Key Vault ile Azure depolama hesabı arasında sorunsuz bir tümleştirme sağlar.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 225d9b715c56e4813a8e26d881c876e7bd498155
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204220"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Depolama hesabı anahtarlarını Key Vault ve Azure PowerShell yönetme

Bir Azure depolama hesabı, hesap adı ve anahtarı kapsayan kimlik bilgilerini kullanır. Anahtar otomatik olarak oluşturulur ve şifreleme anahtarı olarak değil, parola görevi görür. Key Vault, depolama hesabı anahtarlarını [Key Vault gizli](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)dizileri olarak depolayarak yönetir. 

Anahtarları bir Azure depolama hesabıyla listelemek (eşitlemek) ve anahtarları düzenli olarak yeniden oluşturmak (döndürmek) için Key Vault yönetilen depolama hesabı anahtarı özelliğini kullanabilirsiniz. Hem depolama hesapları hem de klasik depolama hesapları için anahtarları yönetebilirsiniz.

Yönetilen depolama hesabı anahtar özelliğini kullandığınızda, aşağıdaki noktaları göz önünde bulundurun:

- Anahtar değerleri, bir çağırana yanıt olarak hiçbir şekilde döndürülmez.
- Depolama hesabı anahtarlarınızı yalnızca Key Vault yönetmelidir. Anahtarları kendiniz yönetmeyin ve Key Vault süreçleriyle kesintiye uğramayın.
- Depolama hesabı anahtarlarını yalnızca tek bir Key Vault nesnesi yönetmelidir. Birden çok nesneden anahtar yönetimine izin verme.
- Depolama hesabınızı bir Kullanıcı sorumlusu ile yönetmek için Key Vault isteyebilirsiniz, ancak hizmet sorumlusu ile kullanamazsınız.
- Anahtarları yalnızca Key Vault kullanarak yeniden oluşturun. Depolama hesabı anahtarlarınızı el ile yeniden üretme.

Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmeti olan Azure Active Directory (Azure AD) ile Azure depolama tümleştirmesi kullanmanızı öneririz. Azure AD tümleştirmesi, Azure [Blobları ve kuyrukları](../storage/common/storage-auth-aad.md)için kullanılabilir ve Azure depolama 'ya OAuth2 belirteç tabanlı erişim sağlar (tıpkı Azure Key Vault gibi).

Azure AD, depolama hesabı kimlik bilgileri yerine bir uygulama veya Kullanıcı kimliği kullanarak istemci uygulamanızın kimliğini doğrulayabilmeniz için izin verir. Azure 'da çalıştırdığınızda [Azure ad yönetilen kimliğini](/azure/active-directory/managed-identities-azure-resources/) kullanabilirsiniz. Yönetilen kimlikler, istemci kimlik doğrulaması gereksinimini ortadan kaldırır ve uygulamanızda veya uygulamanızdaki kimlik bilgilerini depolar.

Azure AD, Key Vault tarafından da desteklenen yetkilendirmeyi yönetmek için rol tabanlı erişim denetimi 'ni (RBAC) kullanır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Hizmet sorumlusu uygulama KIMLIĞI

Bir Azure AD kiracısı, kayıtlı her uygulamayı bir [hizmet sorumlusu](/azure/active-directory/develop/developer-glossary#service-principal-object)ile sağlar. Hizmet sorumlusu, RBAC aracılığıyla diğer Azure kaynaklarına erişim için yetkilendirme kurulumu sırasında kullanılan uygulama KIMLIĞI olarak görev yapar.

Key Vault, tüm Azure AD kiracılarında önceden kaydedilmiş bir Microsoft uygulamasıdır. Key Vault, her bir Azure bulutu 'nda aynı uygulama KIMLIĞI altına kaydedilir.

| Kira | Bulut | Uygulama Kimliği |
| --- | --- | --- |
| Azure AD | Azure Kamu | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure genel | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Diğer  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzu gerçekleştirmek için, önce aşağıdakileri yapmanız gerekir:

- [Azure PowerShell modülünü yükler](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Anahtar Kasası oluşturma](quick-create-powershell.md)
- [Bir Azure depolama hesabı oluşturun](../storage/common/storage-quickstart-create-account.md?tabs=azure-powershell). Depolama hesabı adı yalnızca küçük harfler ve rakamlar kullanmalıdır. Adın uzunluğu 3 ile 24 karakter arasında olmalıdır.
      

## <a name="manage-storage-account-keys"></a>Depolama hesabı anahtarlarını yönetme

### <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) cmdlet 'Ini kullanarak PowerShell oturumunuzun kimliğini doğrulayın. 

```azurepowershell-interactive
Connect-AzAccount
```
Birden çok Azure aboneliğiniz varsa [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet 'ini kullanarak bunları listeleyebilir ve [set-azcontext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) cmdlet 'i ile kullanmak istediğiniz aboneliği belirtebilirsiniz. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Değişkenleri ayarla

İlk olarak, aşağıdaki adımlarda PowerShell cmdlet 'leri tarafından kullanılacak değişkenleri ayarlayın. <YourResourceGroupName> `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` , ,<YourStorageAccountName> Ve<YourKeyVaultName> yer tutucuları güncelleştirdiğinizden emin olun ve $keyVaultSpAppId (Yukarıdaki [hizmet sorumlusu uygulama kimliği](#service-principal-application-id)'nde belirtildiği gibi) olarak ayarlayın.

Ayrıca Azure PowerShell, Kullanıcı KIMLIĞINIZI ve Azure depolama hesabınızın bağlamını almak için [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) ve [Get-azstorageaccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) cmdlet 'lerini de kullanacağız.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Depolama hesabınıza Key Vault erişim izni verin

Key Vault depolama hesabı Anahtarlarınıza erişip yönetebilmeniz için depolama hesabınıza erişim yetkisi vermelisiniz. Key Vault uygulama, depolama hesabınıza yönelik anahtarları *listelemek* ve yeniden *oluşturmak* için izinler gerektirir. Bu izinler, yerleşik RBAC rol [depolama hesabı anahtar operatörü hizmeti rolü](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)aracılığıyla etkinleştirilir. 

Bu rolü, Azure PowerShell [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet 'ini kullanarak, kapsamı depolama hesabınızla sınırlayan Key Vault hizmet sorumlusuna atayın.

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Başarılı rol atamasından sonra, aşağıdaki örneğe benzer bir çıktı görmeniz gerekir:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Depolama hesabınızdaki role Key Vault zaten eklendiyse *"rol ataması zaten var."* hatayla. Rol atamasını, Azure portal depolama hesabı "erişim denetimi (ıAM)" sayfasını kullanarak da doğrulayabilirsiniz.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Yönetilen depolama hesaplarına kullanıcı hesabınıza izin verin

Key Vault erişim ilkesini güncelleştirmek ve Kullanıcı hesabınıza depolama hesabı izinleri vermek için Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) cmdlet 'ini kullanın.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Depolama hesapları için izinler, Azure portal depolama hesabı "erişim ilkeleri" sayfasında kullanılamaz.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Key Vault örneğinize yönetilen bir depolama hesabı ekleyin

Key Vault örneğiniz içinde yönetilen bir depolama hesabı oluşturmak için Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet 'ini kullanın. `-DisableAutoRegenerateKey` Anahtar, depolama hesabı anahtarlarını yeniden üretmemelidir.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Anahtar yeniden oluşturma olmadan depolama hesabının başarılı bir şekilde eklenmesiyle, aşağıdaki örneğe benzer bir çıktı görmeniz gerekir:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Anahtar yeniden oluşturmayı etkinleştir

Depolama hesabı anahtarlarınızı düzenli aralıklarla yeniden oluşturmak Key Vault isterseniz, yeniden oluşturma dönemi ayarlamak için Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) cmdlet 'ini kullanabilirsiniz. Bu örnekte, üç günün yeniden oluşturma dönemini ayarlayacağız. Üç gün sonra, Key Vault ' key2 ' öğesini yeniden oluşturacak ve etkin anahtarı ' key2 ' iken ' KEY1 ' olarak takas edecek.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Anahtar yeniden oluşturma ile depolama hesabının başarılı bir şekilde eklenmesiyle, aşağıdaki örneğe benzer bir çıktı görmeniz gerekir:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="shared-access-signature-tokens"></a>Paylaşılan erişim imza belirteçleri

Ayrıca, Key Vault paylaşılan erişim imzası belirteçleri oluşturmasını isteyebilirsiniz. Paylaşılan erişim imzası, depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. İstemci, hesap anahtarlarınızı paylaşmadan Depolama hesabınızdaki kaynaklara erişim izni verebilirsiniz. Paylaşılan erişim imzası, hesap anahtarlarınızla ödün vermeden depolama kaynaklarınızı paylaşmak için güvenli bir yol sağlar.

Bu bölümdeki komutlar aşağıdaki eylemleri tamamlar:

- Hesap paylaşılan erişim imzası tanımı ayarlayın. 
- Blob, dosya, tablo ve kuyruk Hizmetleri için bir hesap paylaşılan erişim imza belirteci oluşturun. Belirteç, kaynak türleri hizmeti, kapsayıcısı ve nesnesi için oluşturulur. Belirteç, https üzerinden ve belirtilen başlangıç ve bitiş tarihleriyle birlikte tüm izinlerle oluşturulur.
- Kasada Key Vault yönetilen bir depolama paylaşılan erişim imzası tanımı ayarlayın. Tanım, oluşturulan paylaşılan erişim imzası belirtecinin şablon URI 'sine sahiptir. Tanım, paylaşılan erişim imzası türüne `account` sahiptir ve N gün için geçerlidir.
- Paylaşılan erişim imzasının anahtar kasanıza gizli dizi olarak kaydedildiğini doğrulayın.
- 
### <a name="set-variables"></a>Değişkenleri ayarla

İlk olarak, aşağıdaki adımlarda PowerShell cmdlet 'leri tarafından kullanılacak değişkenleri ayarlayın. <YourStorageAccountName> Ve<YourKeyVaultName> yer tutucuları güncelleştirdiğinizden emin olun.

Ayrıca, Azure depolama hesabınızın bağlamını almak için Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlet 'lerini de kullanacağız.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Paylaşılan erişim imza belirteci oluşturma

Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlet 'lerini kullanarak bir paylaşılan erişim imzası tanımı oluşturun.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
$SasToken değeri şuna benzer olacaktır.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Paylaşılan erişim imzası tanımı oluştur

Paylaşılan erişim imzası tanımı oluşturmak için Azure PowerShell [set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) cmdlet 'ini kullanın.  `-Name` Parametresi için tercih ettiğiniz adı sağlayabilirsiniz.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Paylaşılan erişim imzası tanımını doğrulama

Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet 'ini kullanarak, paylaşılan erişim imzası tanımının anahtar kasasında depolandığını doğrulayabilirsiniz.

İlk olarak, Anahtar Kasanızda paylaşılan erişim imzası tanımını bulun.

```azurepowershell-interactive
Get-AzKeyVaultSecret -vault-name <YourKeyVaultName>
```

SAS tanımınıza karşılık gelen gizli dizi şu özelliklere sahip olacaktır:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Artık bu gizli dizi içeriğini görüntülemek için [Get-azkeyvaultsecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) cmdlet 'ini `Name` ve Secret özelliğini kullanabilirsiniz.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

Bu komutun çıktısı, SAS tanım dizenizi gösterecektir.


## <a name="next-steps"></a>Sonraki adımlar

- [Yönetilen depolama hesabı anahtar örnekleri](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-and-certificates.md)
- [PowerShell başvurusunu Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
