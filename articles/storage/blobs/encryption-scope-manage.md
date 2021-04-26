---
title: Şifreleme kapsamları oluşturma ve yönetme
description: Blob verilerini kapsayıcı veya blob düzeyinde yalıtmak için bir şifreleme kapsamı oluşturmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.date: 03/26/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 656443b0bc9d0e45f43634b1b4c21145de7a5bb5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792552"
---
# <a name="create-and-manage-encryption-scopes"></a>Şifreleme kapsamları oluşturma ve yönetme

Şifreleme kapsamları tek bir blob veya kapsayıcı düzeyinde şifrelemeyi yönetmenizi sağlar. Aynı depolama hesabında bulunan ancak farklı müşterilere ait olan veriler arasında güvenli sınırlar oluşturmak için şifreleme kapsamlarını kullanabilirsiniz. Şifreleme kapsamları hakkında daha fazla bilgi için bkz. [BLOB depolama Için şifreleme kapsamları](encryption-scope-overview.md).

Bu makalede, bir şifreleme kapsamının nasıl oluşturulacağı gösterilmektedir. Ayrıca bir blob veya kapsayıcı oluştururken bir şifreleme kapsamının nasıl gösterileceğini gösterir.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>Şifreleme kapsamı oluşturma

Microsoft tarafından yönetilen bir anahtarla veya bir Azure Key Vault depolanan ve Azure Key Vault yönetilen bir donanım güvenlik modelinde (HSM) (Önizleme) bulunan müşteri tarafından yönetilen bir anahtarla korunan bir şifreleme kapsamı oluşturabilirsiniz. Müşteri tarafından yönetilen anahtarla bir şifreleme kapsamı oluşturmak için, önce bir Anahtar Kasası veya yönetilen HSM oluşturmanız ve kapsam için kullanmayı düşündüğünüz anahtarı eklemeniz gerekir. Anahtar Kasası veya yönetilen HSM 'nin Temizleme koruması etkinleştirilmiş olması ve depolama hesabıyla aynı bölgede olması gerekir.

Bir şifreleme kapsamı, oluşturduğunuzda otomatik olarak etkinleştirilir. Şifreleme kapsamını oluşturduktan sonra, bir blob oluştururken belirtebilirsiniz. Ayrıca, kapsayıcıdaki tüm Bloblar için otomatik olarak uygulanan bir kapsayıcı oluşturduğunuzda varsayılan bir şifreleme kapsamı belirtebilirsiniz.

# <a name="portal"></a>[Portal](#tab/portal)

Azure portal bir şifreleme kapsamı oluşturmak için aşağıdaki adımları izleyin:

1. Azure portalda depolama hesabınıza gidin.
1. **Şifreleme** ayarını seçin.
1. **Şifreleme kapsamları** sekmesini seçin.
1. Yeni bir şifreleme kapsamı eklemek için **Ekle** düğmesine tıklayın.
1. **Şifreleme kapsamı oluştur** bölmesinde, yeni kapsam için bir ad girin.
1. **Microsoft tarafından yönetilen anahtarlar** veya **müşteri tarafından yönetilen anahtarlar** için istediğiniz şifreleme anahtarı desteği türünü seçin.
    - **Microsoft tarafından yönetilen anahtarlar**' ı seçtiyseniz, şifreleme kapsamını oluşturmak için **Oluştur** ' a tıklayın.
    - **Müşteri tarafından yönetilen anahtarlar**' ı seçtiyseniz bir abonelik seçin ve bir Anahtar Kasası veya YÖNETILEN bir HSM ve bu şifreleme kapsamı için kullanılacak bir anahtarı aşağıdaki görüntüde gösterildiği gibi belirtin.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Azure portal 'de şifreleme kapsamı oluşturmayı gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir şifreleme kapsamı oluşturmak için [az. Storage](https://www.powershellgallery.com/packages/Az.Storage) PowerShell modülünü, sürüm 3.4.0 veya üstünü yüklersiniz.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft tarafından yönetilen anahtarlarla korunan bir şifreleme kapsamı oluşturun

Microsoft tarafından yönetilen anahtarlarla korunan yeni bir şifreleme kapsamı oluşturmak için, parametresiyle **New-AzStorageEncryptionScope** komutunu çağırın `-StorageEncryption` .

Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla korunan bir şifreleme kapsamı oluşturun

Anahtar kasasında veya yönetilen HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla korunan yeni bir şifreleme kapsamı oluşturmak için önce depolama hesabı için müşteri tarafından yönetilen anahtarları yapılandırın. Depolama hesabına yönetilen bir kimlik atamanız ve ardından, depolama hesabının bu hesaba erişim izinleri olması için Anahtar Kasası veya yönetilen HSM için erişim ilkesini yapılandırmak üzere yönetilen kimliği kullanmanız gerekir.

Müşteri tarafından yönetilen anahtarları bir şifreleme kapsamıyla kullanılmak üzere yapılandırmak için, anahtar kasasında veya yönetilen HSM 'de Temizleme korumasının etkinleştirilmesi gerekir. Anahtar Kasası veya yönetilen HSM, depolama hesabı ile aynı bölgede olmalıdır.

Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Ardından, **New-AzStorageEncryptionScope** komutunu `-KeyvaultEncryption` parametresiyle ÇAĞıRıN ve anahtar URI 'sini belirtin. Anahtar URI 'sindeki anahtar sürümü de dahil olmak üzere isteğe bağlıdır. Anahtar sürümünü atlarsanız, şifreleme kapsamı en son anahtar sürümünü otomatik olarak kullanacaktır. Anahtar sürümünü eklerseniz, farklı bir sürümü kullanmak için anahtar sürümünü el ile güncelleştirmeniz gerekir.

Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLı ile bir şifreleme kapsamı oluşturmak için önce Azure CLı sürüm 2.20.0 veya üstünü yüklemeniz gerekir.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Microsoft tarafından yönetilen anahtarlarla korunan bir şifreleme kapsamı oluşturun

Microsoft tarafından yönetilen anahtarlarla korunan yeni bir şifreleme kapsamı oluşturmak için, parametresini olarak belirterek [az Storage Account ENCRYPTION Create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) komutunu çağırın `--key-source` `Microsoft.Storage` . Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla korunan bir şifreleme kapsamı oluşturun

Microsoft tarafından yönetilen anahtarlarla korunan yeni bir şifreleme kapsamı oluşturmak için, parametresini olarak belirterek [az Storage Account ENCRYPTION Create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) komutunu çağırın `--key-source` `Microsoft.Storage` . Yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

Bir anahtar kasasında veya yönetilen HSM 'de müşteri tarafından yönetilen anahtarlarla korunan yeni bir şifreleme kapsamı oluşturmak için önce depolama hesabı için müşteri tarafından yönetilen anahtarları yapılandırın. Depolama hesabına yönetilen bir kimlik atamanız ve ardından, depolama hesabının bu hesaba erişim izinleri olması için Anahtar Kasası için erişim ilkesini yapılandırmak üzere yönetilen kimliği kullanmanız gerekir. Daha fazla bilgi için bkz. [Azure depolama şifrelemesi Için müşteri tarafından yönetilen anahtarlar](../common/customer-managed-keys-overview.md).

Müşteri tarafından yönetilen anahtarları bir şifreleme kapsamıyla kullanılmak üzere yapılandırmak için, anahtar kasasında veya yönetilen HSM 'de Temizleme korumasının etkinleştirilmesi gerekir. Anahtar Kasası veya yönetilen HSM, depolama hesabı ile aynı bölgede olmalıdır.

Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Sonra, parametresini kullanarak **az Storage Account ENCRYPTION Create** komutunu çağırın `--key-uri` ve anahtar URI 'sini belirtin. Anahtar URI 'sindeki anahtar sürümü de dahil olmak üzere isteğe bağlıdır. Anahtar sürümünü atlarsanız, şifreleme kapsamı en son anahtar sürümünü otomatik olarak kullanacaktır. Anahtar sürümünü eklerseniz, farklı bir sürümü kullanmak için anahtar sürümünü el ile güncelleştirmeniz gerekir.

Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Azure depolama şifrelemesini bir anahtar kasasında veya yönetilen HSM 'de müşteri tarafından yönetilen anahtarlarla yapılandırma hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Azure Key Vault’ta depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](../common/customer-managed-keys-configure-key-vault.md)
- [Azure Key Vault YÖNETILEN HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)](../common/customer-managed-keys-configure-key-vault-hsm.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Depolama hesabı için şifreleme kapsamlarını Listele

# <a name="portal"></a>[Portal](#tab/portal)

Azure portal bir depolama hesabının şifreleme kapsamlarını görüntülemek için, depolama hesabı için **şifreleme kapsamları** ayarına gidin. Bu bölmeden, bir şifreleme kapsamını etkinleştirebilir veya devre dışı bırakabilir ya da bir şifreleme kapsamının anahtarını değiştirebilirsiniz.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Azure portal 'de şifreleme kapsamları listesini gösteren ekran görüntüsü":::

Anahtar URI 'SI ve sürümü de dahil olmak üzere, müşteri tarafından yönetilen bir anahtarın ayrıntılarını görüntülemek için **anahtar sütunundaki bağlantıyı** izleyin.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Şifreleme kapsamıyla kullanılan bir anahtarın ayrıntılarını gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir depolama hesabı için kullanılabilir şifreleme kapsamlarını listelemek için **Get-AzStorageEncryptionScope** komutunu çağırın. Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Depolama hesabına göre bir kaynak grubundaki tüm şifreleme kapsamlarını listelemek için ardışık düzen söz dizimini kullanın:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLı ile bir depolama hesabı için kullanılabilen şifreleme kapsamlarını listelemek için [az Storage Account ENCRYPTION List](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list) komutunu çağırın. Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Varsayılan şifreleme kapsamıyla kapsayıcı oluşturma

Bir kapsayıcı oluşturduğunuzda, varsayılan bir şifreleme kapsamı belirtebilirsiniz. Bu kapsayıcıdaki Bloblar, varsayılan olarak bu kapsamı kullanacaktır.

Kapsayıcı, tüm Blobların varsayılan kapsamı kullanmasını gerektirecek şekilde yapılandırılmadığı takdirde, kendi şifreleme kapsamı ile ayrı bir blob oluşturulabilir. Daha fazla bilgi için bkz. [kapsayıcılar ve Bloblar Için şifreleme kapsamları](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs).

# <a name="portal"></a>[Portal](#tab/portal)

Azure portal varsayılan şifreleme kapsamıyla bir kapsayıcı oluşturmak için, önce şifreleme kapsamı [oluşturma](#create-an-encryption-scope)bölümünde açıklandığı gibi şifreleme kapsamını oluşturun. Sonra, kapsayıcıyı oluşturmak için aşağıdaki adımları izleyin:

1. Depolama hesabınızdaki kapsayıcı listesine gidin ve yeni bir kapsayıcı oluşturmak için **Ekle** düğmesini seçin.
1. **Yeni kapsayıcı** bölmesinde **Gelişmiş** ayarlar ' ı genişletin.
1. **Şifreleme kapsamı** açılır penceresinde kapsayıcının varsayılan şifreleme kapsamını seçin.
1. Kapsayıcıdaki tüm Blobların varsayılan şifreleme kapsamını kullanmasını gerektirmek için, **Bu şifreleme kapsamını kapsayıcıdaki tüm Bloblar Için kullanmak** üzere onay kutusunu seçin. Bu onay kutusu işaretliyse, kapsayıcıdaki tek bir blob varsayılan şifreleme kapsamını geçersiz kılamaz.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Varsayılan şifreleme kapsamıyla kapsayıcıyı gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile varsayılan şifreleme kapsamıyla bir kapsayıcı oluşturmak için, parametresinin kapsamını belirterek [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) komutunu çağırın `-DefaultEncryptionScope` . Bir kapsayıcıdaki tüm Blobları kapsayıcının varsayılan kapsamını kullanacak şekilde zorlamak için `-PreventEncryptionScopeOverride` parametresini olarak ayarlayın `true` .

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLı ile varsayılan şifreleme kapsamıyla bir kapsayıcı oluşturmak için, parametresinin kapsamını belirterek [az Storage Container Create](/cli/azure/storage/container#az_storage_container_create) komutunu çağırın `--default-encryption-scope` . Bir kapsayıcıdaki tüm Blobları kapsayıcının varsayılan kapsamını kullanacak şekilde zorlamak için `--prevent-encryption-scope-override` parametresini olarak ayarlayın `true` .

Aşağıdaki örnek, kapsayıcıyı oluşturma işlemini yetkilendirmek için Azure AD hesabınızı kullanır. Hesap erişim anahtarını da kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](./authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Bir istemci, bir blobu varsayılan şifreleme kapsamına sahip bir kapsayıcıya yüklerken bir kapsam belirtmeyi denerse ve kapsayıcı, blob 'ların varsayılan kapsamı geçersiz kılmasını önleyecek şekilde yapılandırıldıysa, işlem, isteğin kapsayıcı şifreleme ilkesi tarafından yasaklanmış olduğunu belirten bir iletiyle başarısız olur.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Şifreleme kapsamına sahip bir blobu karşıya yükleme

Bir blobu karşıya yüklediğinizde, o blob için bir şifreleme kapsamı belirtebilir veya bir tane belirtilmişse kapsayıcı için varsayılan şifreleme kapsamını kullanabilirsiniz.

# <a name="portal"></a>[Portal](#tab/portal)

Bir blobu Azure portal aracılığıyla bir şifreleme kapsamıyla karşıya yüklemek için önce şifreleme kapsamı [oluşturma](#create-an-encryption-scope)bölümünde açıklandığı gibi şifreleme kapsamını oluşturun. Sonra, blobu oluşturmak için aşağıdaki adımları izleyin:

1. Blobu karşıya yüklemek istediğiniz kapsayıcıya gidin.
1. **Karşıya yükle** düğmesini seçin ve karşıya yüklenecek blobu bulun.
1. **Karşıya yükleme blobu** bölmesinde **Gelişmiş** ayarlar ' ı genişletin.
1. **Şifreleme kapsamı** açılan bölümünü bulun. Varsayılan olarak, blob belirtilmişse kapsayıcı için varsayılan şifreleme kapsamıyla oluşturulur. Kapsayıcı, Blobların varsayılan şifreleme kapsamını kullanmasını gerektiriyorsa, bu bölüm devre dışıdır.
1. Karşıya yüklediğiniz blob için farklı bir kapsam belirtmek üzere, **var olan bir kapsam** Seç ' i seçin ve ardından açılan listeden istediğiniz kapsamı seçin.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Bir blob 'u şifreleme kapsamıyla karşıya yüklemeyi gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell aracılığıyla bir şifreleme kapsamına sahip bir blobu karşıya yüklemek için [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) komutunu çağırın ve BLOB için şifreleme kapsamını belirtin.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLı aracılığıyla bir şifreleme kapsamına sahip bir blobu karşıya yüklemek için [az Storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) komutunu çağırın ve BLOB için şifreleme kapsamını belirtin.

Azure Cloud Shell kullanıyorsanız, kök dizinde bir dosya oluşturmak için [blobu karşıya yükleme](storage-quickstart-blobs-cli.md#upload-a-blob) bölümünde açıklanan adımları izleyin. Ardından, aşağıdaki örneği kullanarak bu dosyayı bir bloba yükleyebilirsiniz.

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Bir kapsamın şifreleme anahtarını değiştirme

Bir şifreleme kapsamını Microsoft tarafından yönetilen anahtardan müşteri tarafından yönetilen bir anahtarla koruyan anahtarı değiştirmek için, önce depolama hesabı için Azure Key Vault veya Key Vault HSM ile müşteri tarafından yönetilen anahtarları etkinleştirdiğinizden emin olun. Daha fazla bilgi için bkz. [Azure Key Vault depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](../common/customer-managed-keys-configure-key-vault.md) veya [Azure Key Vault depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portal](#tab/portal)

Azure portal bir kapsamı koruyan anahtarı değiştirmek için şu adımları izleyin:

1. Depolama hesabı için şifreleme kapsamları listesini görüntülemek için **şifreleme kapsamları** sekmesine gidin.
1. Değiştirmek istediğiniz kapsamın yanındaki **diğer** düğmesini seçin.
1. **Şifreleme kapsamını Düzenle** bölmesinde, şifreleme türünü Microsoft tarafından yönetilen anahtardan müşteri tarafından yönetilen anahtara veya bunun tersini değiştirebilirsiniz.
1. Müşteri tarafından yönetilen yeni bir anahtar seçmek için **Yeni bir anahtar kullan** ' ı seçin ve Anahtar Kasası, anahtar ve anahtar sürümünü belirtin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Bir şifreleme kapsamını müşteri tarafından yönetilen anahtardan PowerShell ile Microsoft tarafından yönetilen bir anahtara koruyan anahtarı değiştirmek için, **Update-AzStorageEncryptionScope** komutunu çağırın ve `-StorageEncryption` parametresini geçirin:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Ardından, **Update-AzStorageEncryptionScope** komutunu çağırın ve `-KeyUri` ve `-KeyvaultEncryption` parametrelerini geçirin:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Bir şifreleme kapsamını müşteri tarafından yönetilen anahtardan Microsoft tarafından yönetilen bir anahtara Azure CLı ile koruyan anahtarı değiştirmek için [az Storage Account ENCRYPTION-Scope Update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) komutunu çağırın ve `--key-source` parametreyi şu değerle geçirin `Microsoft.Storage` :

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Ardından, **az Storage Account ENCRYPTION-Scope Update** komutunu çağırın, `--key-uri` parametresini geçirin ve `--key-source` parametresini şu değerle geçirin `Microsoft.KeyVault` :

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Şifreleme kapsamını devre dışı bırakma

Bir şifreleme kapsamı devre dışı bırakıldığında, artık bu için faturalandırılırsınız. Gereksiz ücretlerden kaçınmak için gerekli olmayan tüm şifreleme kapsamlarını devre dışı bırakın. Daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portal](#tab/portal)

Azure portal bir şifreleme kapsamını devre dışı bırakmak için depolama hesabı için **şifreleme kapsamları** ayarına gidin, istenen şifreleme kapsamını seçin ve **devre dışı bırak**' ı seçin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Bir şifreleme kapsamını PowerShell ile devre dışı bırakmak için, `-State` `disabled` Aşağıdaki örnekte gösterildiği gibi Update-AzStorageEncryptionScope komutunu çağırın ve değeri ile parametresini ekleyin. Bir şifreleme kapsamını yeniden etkinleştirmek için, parametresiyle aynı komutu çağırın `-State` `enabled` . Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Bir şifreleme kapsamını Azure CLı ile devre dışı bırakmak için, [az Storage Account ENCRYPTION-Scope Update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) komutunu çağırın ve `--state` `Disabled` Aşağıdaki örnekte gösterildiği gibi, değerini içeren parametresini ekleyin. Bir şifreleme kapsamını yeniden etkinleştirmek için, parametresiyle aynı komutu çağırın `--state` `Enabled` . Örnekteki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Bir şifreleme kapsamının silinmesi mümkün değildir. Beklenmeyen maliyetleri önlemek için, şu anda ihtiyaç duymayan tüm şifreleme kapsamlarını devre dışı bıraktığınızdan emin olun.

---

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](../common/storage-service-encryption.md)
- [BLOB depolama için şifreleme kapsamları](encryption-scope-overview.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](../common/customer-managed-keys-overview.md)