---
title: PowerShell (Önizleme) ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma-Azure depolama
description: PowerShell kullanarak Azure Storage 'da Azure Active Directory kimlik bilgilerini kullanarak paylaşılan erişim imzası (SAS) oluşturmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 0164c97adf720a618179908298223c54bf48824e
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673333"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>PowerShell ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma (Önizleme)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Bu makalede, Azure PowerShell (Önizleme) ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturmak üzere Azure Active Directory (Azure AD) kimlik bilgilerinin nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>Önizleme modülünü yükler

PowerShell kullanarak bir Kullanıcı temsili SAS oluşturmak için, önce az. Storage 1.3.1-Preview modülünü yüklemeniz gerekir. Modülünü yüklemek için şu adımları izleyin:

1. Azure PowerShell önceki tüm yüklemelerini kaldırın:

    - **Ayarlar**altındaki **uygulamalar & Özellikler** ayarını kullanarak Windows 'un önceki Azure PowerShell yüklemelerini kaldırın.
    - Tüm **Azure** modüllerini ' den `%Program Files%\WindowsPowerShell\Modules`kaldırın.

1. PowerShellGet 'in en son sürümüne sahip olduğunuzdan emin olun. Bir Windows PowerShell penceresi açın ve en son sürümü yüklemek için aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet yükledikten sonra PowerShell penceresini kapatın ve yeniden açın.

1. Azure PowerShell en son sürümünü yükler:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Kullanıcı temsili SAS 'yi destekleyen bir Azure depolama önizleme modülü yükler:

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. PowerShell penceresini kapatıp yeniden açın.

PowerShell en son az. Storage modülünü varsayılan olarak yüklediği için, konsolunu başlattığınızda 1.3.1-Preview modülünü açıkça yüklemeniz gerekebilir. Önizleme modülünü açıkça yüklemek için [Import-Module](/powershell/module/microsoft.powershell.core/import-module) komutunu çalıştırın:

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Azure PowerShell yükleme hakkında daha fazla bilgi için bkz. [PowerShellGet Ile yükleme Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Azure AD ile Azure PowerShell oturum açın

Azure AD hesabınızla oturum açmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu çağırın:

```powershell
Connect-AzAccount
```

PowerShell ile oturum açma hakkında daha fazla bilgi için bkz. [Azure PowerShell Ile oturum açma](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>RBAC ile izin atama

Azure PowerShell bir Kullanıcı temsili SAS oluşturmak için, PowerShell 'de oturum açmak üzere kullanılan Azure AD hesabına **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini içeren bir rol atanmalıdır. Bu izin, Azure AD hesabının *Kullanıcı temsili anahtarını*istemesine olanak sağlar. Kullanıcı temsili anahtarı, Kullanıcı temsili SAS imzalamak için kullanılır. Depolama hesabı, kaynak grubu veya abonelik düzeyinde **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini sağlayan rolün atanması gerekir. Kullanıcı temsilciliğini oluşturmaya yönelik RBAC izinleri hakkında daha fazla bilgi için, [Kullanıcı temsili oluşturma SAS oluşturma](/rest/api/storageservices/create-user-delegation-sas)konusunun **RBAC ile izin atama** bölümüne bakın.

Azure AD güvenlik sorumlusuna RBAC rolleri atamak için yeterli izniniz yoksa, hesap sahibine veya yöneticiden gerekli izinleri atamasını isteyebilirsiniz.

Aşağıdaki örnek, **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini Içeren **Depolama Blobu veri katılımcısı** rolünü atar. Rol, depolama hesabı düzeyinde kapsamlandırılır.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini içeren yerleşik roller hakkında daha fazla bilgi için bkz. [Azure kaynakları için yerleşik roller](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>SAS güvenliğini sağlamak için Azure AD kimlik bilgilerini kullanma

Azure PowerShell ile bir Kullanıcı temsili SAS oluşturduğunuzda, SAS imzalamak için kullanılan Kullanıcı temsili anahtarı sizin için örtülü olarak oluşturulur. SAS için belirttiğiniz başlangıç saati ve bitiş saati, Kullanıcı temsili anahtarı için başlangıç saati ve süre sonu zamanı olarak da kullanılır. 

Kullanıcı temsili anahtarının geçerli olduğu maksimum Aralık, başlangıç tarihinden itibaren 7 gün olduğundan, başlangıç zamanının 7 gün içinde olan SAS için bir süre sonu zamanı belirtmeniz gerekir. Kullanıcı temsili anahtarının süresi dolduktan sonra SAS geçersiz, bu nedenle süre sonu 7 günden daha fazla olan bir SAS yalnızca 7 gün için geçerli olacaktır.

Azure PowerShell olan bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturmak için, önce `-UseConnectedAccount` parametresini belirterek yeni bir Azure Storage bağlam nesnesi oluşturun. @No__t-0 parametresi, komutun oturum açtığınız Azure AD hesabı altında bağlam nesnesini oluşturduğunu belirtir.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Bir kapsayıcı için Kullanıcı temsili SAS oluşturma

Bir kapsayıcı için Kullanıcı temsili SAS belirteci döndürmek için, daha önce oluşturduğunuz Azure depolama bağlamı nesnesini geçirerek [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) komutunu çağırın.

Aşağıdaki örnek bir kapsayıcı için Kullanıcı temsili SAS belirteci döndürür. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Döndürülen Kullanıcı temsili SAS belirteci şuna benzer:

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Blob için Kullanıcı temsili SAS oluşturma

Blob için bir Kullanıcı temsili SAS belirteci döndürmek üzere, daha önce oluşturduğunuz Azure depolama bağlamı nesnesini geçirerek [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) komutunu çağırın.

Aşağıdaki sözdizimi bir blob için Kullanıcı temsili SAS döndürür. Örnek, SAS belirtecine eklenen blob URI 'sini döndüren `-FullUri` parametresini belirtir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Döndürülen Kullanıcı temsili SAS URI 'SI şuna benzer olacaktır:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Kullanıcı temsili SAS, depolanan erişim ilkesiyle izin tanımlamayı desteklemez.

## <a name="revoke-a-user-delegation-sas"></a>Kullanıcı temsilciliğini iptal etme SAS

Azure PowerShell bir kullanıcı temsilci SAS 'sini iptal etmek için, **Revoke-AzStorageAccountUserDelegationKeys** komutunu çağırın. Bu komut, belirtilen depolama hesabıyla ilişkili tüm Kullanıcı temsili anahtarlarını iptal eder. Bu anahtarlarla ilişkili tüm paylaşılan erişim imzaları geçersiz kılınır.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Hem Kullanıcı temsili anahtarı hem de RBAC rol atamaları Azure Storage tarafından önbelleğe alınır. bu nedenle, iptal işlemini başlattığınızda ve var olan bir Kullanıcı temsili SAS geçersiz hale geldiğinde bir gecikme olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Kullanıcı temsilciliğini oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Kullanıcı temsilciyi anahtar işlemi al](/rest/api/storageservices/get-user-delegation-key)
