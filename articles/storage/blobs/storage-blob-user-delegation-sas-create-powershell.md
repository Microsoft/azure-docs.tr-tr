---
title: Bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturmak için PowerShell kullanma
titleSuffix: Azure Storage
description: PowerShell kullanarak Azure Active Directory kimlik bilgileriyle Kullanıcı temsili SAS oluşturmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714523"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>PowerShell ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Bu makalede, Azure PowerShell sahip bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturmak üzere Azure Active Directory (Azure AD) kimlik bilgilerinin nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>PowerShell modülünü yükleme

PowerShell ile bir Kullanıcı temsili SAS oluşturmak için az. Storage modülünün Version 1.10.0 veya üzeri sürümünü yüklemelisiniz. Modülün en son sürümünü yüklemek için aşağıdaki adımları izleyin:

1. Azure PowerShell önceki tüm yüklemelerini kaldırın:

    - **Ayarlar** altındaki **uygulamalar & Özellikler** ayarını kullanarak Windows 'un önceki Azure PowerShell yüklemelerini kaldırın.
    - Tüm **Azure** modüllerini ' den kaldırın `%Program Files%\WindowsPowerShell\Modules` .

1. PowerShellGet 'in en son sürümüne sahip olduğunuzdan emin olun. Bir Windows PowerShell penceresi açın ve en son sürümü yüklemek için aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet yükledikten sonra PowerShell penceresini kapatın ve yeniden açın.

1. Azure PowerShell en son sürümünü yükler:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Azure PowerShell sürüm 3.2.0 veya sonraki bir sürümü yüklediğinizden emin olun. Azure Storage PowerShell modülünün en son sürümünü yüklemek için aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. PowerShell penceresini kapatıp yeniden açın.

Az. Storage modülünün hangi sürümünün yüklü olduğunu denetlemek için şu komutu çalıştırın:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Azure PowerShell yükleme hakkında daha fazla bilgi için bkz. [PowerShellGet Ile yükleme Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Azure AD ile Azure PowerShell oturum açın

Azure AD hesabınızla oturum açmak için [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu çağırın:

```powershell
Connect-AzAccount
```

PowerShell ile oturum açma hakkında daha fazla bilgi için bkz. [Azure PowerShell Ile oturum açma](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Azure RBAC ile izin atama

Azure PowerShell bir Kullanıcı temsili SAS oluşturmak için, PowerShell 'de oturum açmak üzere kullanılan Azure AD hesabına **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini içeren bir rol atanmalıdır. Bu izin, Azure AD hesabının *Kullanıcı temsili anahtarını* istemesine olanak sağlar. Kullanıcı temsili anahtarı, Kullanıcı temsili SAS imzalamak için kullanılır. Depolama hesabı, kaynak grubu veya abonelik düzeyinde **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini sağlayan rolün atanması gerekir. Kullanıcı temsilciliğini oluşturmaya yönelik Azure RBAC izinleri hakkında daha fazla bilgi için, [Kullanıcı temsili oluşturma SAS oluşturma](/rest/api/storageservices/create-user-delegation-sas)konusunun **Azure RBAC ile izin atama** bölümüne bakın.

Azure AD güvenlik sorumlusuna Azure rolleri atamak için yeterli izniniz yoksa, hesap sahibine veya yöneticiden gerekli izinleri atamasını isteyebilirsiniz.

Aşağıdaki örnek, **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini Içeren **Depolama Blobu veri katılımcısı** rolünü atar. Rol, depolama hesabı düzeyinde kapsamlandırılır.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

**Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** eylemini içeren yerleşik roller hakkında daha fazla bilgi için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>SAS güvenliğini sağlamak için Azure AD kimlik bilgilerini kullanma

Azure PowerShell ile bir Kullanıcı temsili SAS oluşturduğunuzda, SAS imzalamak için kullanılan Kullanıcı temsili anahtarı sizin için örtülü olarak oluşturulur. SAS için belirttiğiniz başlangıç saati ve bitiş saati, Kullanıcı temsili anahtarı için başlangıç saati ve süre sonu zamanı olarak da kullanılır. 

Kullanıcı temsili anahtarının geçerli olduğu maksimum Aralık, başlangıç tarihinden itibaren 7 gün olduğundan, başlangıç zamanının 7 gün içinde olan SAS için bir süre sonu zamanı belirtmeniz gerekir. Kullanıcı temsili anahtarının süresi dolduktan sonra SAS geçersiz, bu nedenle süre sonu 7 günden daha fazla olan bir SAS yalnızca 7 gün için geçerli olacaktır.

Azure PowerShell olan bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturmak için, önce parametreyi belirterek yeni bir Azure Storage bağlam nesnesi oluşturun `-UseConnectedAccount` . `-UseConnectedAccount`Parametresi, komutun oturum açtığınız Azure AD hesabı altında bağlam nesnesini oluşturduğunu belirtir.

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

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Blob için Kullanıcı temsili SAS oluşturma

Blob için bir Kullanıcı temsili SAS belirteci döndürmek üzere, daha önce oluşturduğunuz Azure depolama bağlamı nesnesini geçirerek [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) komutunu çağırın.

Aşağıdaki sözdizimi bir blob için Kullanıcı temsili SAS döndürür. Örnek, alt `-FullUri` SAS belirtecine eklenen blob URI 'sini döndüren parametresini belirtir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Döndürülen Kullanıcı temsili SAS URI 'SI şuna benzer olacaktır:

```output
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
> Hem Kullanıcı temsili anahtarı hem de Azure rolü atamaları Azure Storage tarafından önbelleğe alınır, bu nedenle iptal işlemini başlattığınızda ve mevcut bir Kullanıcı temsili SAS geçersiz hale geldiğinde bir gecikme olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Kullanıcı temsilciliğini oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Kullanıcı temsilciyi anahtar işlemi al](/rest/api/storageservices/get-user-delegation-key)
