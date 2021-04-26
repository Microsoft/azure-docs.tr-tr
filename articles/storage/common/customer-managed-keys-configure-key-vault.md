---
title: Azure Key Vault’ta depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma
titleSuffix: Azure Storage
description: Azure portal, PowerShell veya Azure CLı kullanarak Azure Key Vault ' de depolanan müşteri tarafından yönetilen anahtarlarla Azure depolama şifrelemesini nasıl yapılandıracağınızı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 77a01a270f47ddacb71962188e7fedd0a0a9f6d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790446"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Azure Key Vault’ta depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma

Azure depolama, bekleyen bir depolama hesabındaki tüm verileri şifreler. Varsayılan olarak, veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için kendi anahtarlarınızı yönetebilirsiniz. Müşteri tarafından yönetilen anahtarların Azure Key Vault veya Key Vault yönetilen donanım güvenlik modeli (HSM) (Önizleme) içinde depolanması gerekir.

Bu makalede, Azure portal, PowerShell veya Azure CLı kullanarak bir anahtar kasasında depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma gösterilmektedir. Yönetilen bir HSM 'de depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma hakkında bilgi edinmek için bkz. [Azure Key Vault Managed HSM 'de depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)](customer-managed-keys-configure-key-vault-hsm.md).

> [!NOTE]
> Azure Key Vault ve Azure Key Vault yönetilen HSM, yapılandırma için aynı API 'Leri ve yönetim arabirimlerini destekler.

## <a name="configure-a-key-vault"></a>Anahtar Kasası yapılandırma

Müşteri tarafından yönetilen anahtarları depolamak için yeni veya var olan bir Anahtar Kasası kullanabilirsiniz. Depolama hesabı ve Anahtar Kasası aynı bölgede olmalıdır, ancak farklı aboneliklerde olabilir.

Azure depolama şifrelemesi ile müşteri tarafından yönetilen anahtarların kullanılması, Anahtar Kasası için hem geçici silme hem de Temizleme korumasının etkinleştirilmesini gerektirir. Yeni bir Anahtar Kasası oluşturduğunuzda ve devre dışı bırakılamaz, geçici silme varsayılan olarak etkindir. Anahtar kasasını oluştururken veya oluşturulduktan sonra, temizleme korumasını etkinleştirebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir Anahtar Kasası oluşturmayı öğrenmek için bkz. [hızlı başlangıç: Azure Portal kullanarak bir Anahtar Kasası oluşturma](../../key-vault/general/quick-create-portal.md). Anahtar Kasası oluşturduğunuzda, aşağıdaki görüntüde gösterildiği gibi **Temizleme korumasını etkinleştir**' i seçin.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="Anahtar Kasası oluştururken Temizleme korumasının nasıl etkinleştirileceğini gösteren ekran görüntüsü":::

Mevcut bir anahtar kasasında Temizleme korumasını etkinleştirmek için şu adımları izleyin:

1. Azure portal, anahtar kasanıza gidin.
1. **Ayarlar** altında **Özellikler**' i seçin.
1. **Korumayı temizle** bölümünde, **Temizleme korumasını etkinleştir**' i seçin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile yeni bir Anahtar Kasası oluşturmak için, [az. keykasası](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell modülünün 2.0.0 veya sonraki bir sürümünü yüklemelisiniz. Sonra yeni bir Anahtar Kasası oluşturmak için [New-Azkeykasasını](/powershell/module/az.keyvault/new-azkeyvault) çağırın. Yeni bir Anahtar Kasası oluşturduğunuzda, az. Keykasa modülünün sürüm 2.0.0 ve üzeri sürümlerde geçici silme özelliği varsayılan olarak etkindir.

Aşağıdaki örnekte, hem geçici silme hem de Temizleme koruması etkin olan yeni bir Anahtar Kasası oluşturulur. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

PowerShell ile var olan bir anahtar kasası üzerinde Temizleme korumasını etkinleştirmeyi öğrenmek için bkz. [PowerShell ile geçici silme kullanma](../../key-vault/general/key-vault-recovery.md).

Ardından, depolama hesabınıza sistem tarafından atanan bir yönetilen kimlik atayın. Bu yönetilen kimliği, depolama hesabı izinlerini anahtar kasasına erişim vermek için kullanacaksınız. Sistem tarafından atanan Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../../active-directory/managed-identities-azure-resources/overview.md).

PowerShell kullanarak yönetilen bir kimlik atamak için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)komutunu çağırın:

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Son olarak, depolama hesabının bu hesaba erişim izinleri olması için Anahtar Kasası için erişim ilkesini yapılandırın. Bu adımda, daha önce depolama hesabına atadığınız yönetilen kimliği kullanacaksınız.

Anahtar Kasası erişim ilkesini ayarlamak için [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)komutunu çağırın:

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı kullanarak yeni bir Anahtar Kasası oluşturmak için [az keykasa Create komutunu](/cli/azure/keyvault#az_keyvault_create)çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Azure CLı ile mevcut bir anahtar kasası üzerinde Temizleme korumasını etkinleştirmeyi öğrenmek için bkz. [CLI ile geçici silme kullanma](../../key-vault/general/key-vault-recovery.md).

Ardından, depolama hesabına sistem tarafından atanan bir yönetilen kimlik atayın. Bu yönetilen kimliği, depolama hesabı izinlerini anahtar kasasına erişim vermek için kullanacaksınız. Sistem tarafından atanan Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../../active-directory/managed-identities-azure-resources/overview.md).

Azure CLı kullanarak yönetilen bir kimlik atamak için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update)' i çağırın:

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Son olarak, depolama hesabının bu hesaba erişim izinleri olması için Anahtar Kasası için erişim ilkesini yapılandırın. Bu adımda, daha önce depolama hesabına atadığınız yönetilen kimliği kullanacaksınız.

Anahtar Kasası için erişim ilkesini ayarlamak için [az keykasa Set-Policy](/cli/azure/keyvault#az_keyvault_set_policy)komutunu çağırın:

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>Anahtar ekleme

Ardından, anahtar kasasında bir anahtar ekleyin.

Azure depolama şifrelemesi, 2048, 3072 ve 4096 boyutlarının RSA ve RSA-HSM anahtarlarını destekler. Anahtarlar hakkında daha fazla bilgi için bkz. [anahtarlar hakkında](../../key-vault/keys/about-keys.md).

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir anahtar ekleme hakkında bilgi edinmek için bkz. [hızlı başlangıç: Azure Portal kullanarak Azure Key Vault bir anahtar ayarlama ve alma](../../key-vault/keys/quick-create-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir anahtar eklemek için [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)komutunu çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir anahtar eklemek için [az keykasa Key Create komutunu](/cli/azure/keyvault/key#az_keyvault_key_create)çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma

Sonra, Azure depolama hesabınızı Azure Key Vault ile müşteri tarafından yönetilen anahtarları kullanacak şekilde yapılandırın ve ardından depolama hesabıyla ilişkilendirilecek anahtarı belirtin.

Şifrelemeyi, müşteri tarafından yönetilen anahtarlarla yapılandırdığınızda, ilişkili anahtar kasasında yeni bir sürüm kullanılabilir olduğunda, Azure depolama şifrelemesi için kullanılan anahtar sürümünü otomatik olarak güncelleştirmeyi tercih edebilirsiniz. Alternatif olarak, anahtar sürümü el ile güncelleştirilene kadar şifreleme için kullanılacak bir anahtar sürümünü açıkça belirtebilirsiniz.

> [!NOTE]
> Bir anahtarı döndürmek için Azure Key Vault ' de anahtarın yeni bir sürümünü oluşturun. Azure depolama Azure Key Vault anahtarın döndürmesini işlemez, bu nedenle anahtarınızı el ile döndürmeniz veya bir zamanlamaya göre döndürmek için bir işlev oluşturmanız gerekecektir.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>Anahtar sürümlerinin otomatik güncelleştirilmesi için şifrelemeyi yapılandırma

Azure depolama, şifreleme için kullanılan müşteri tarafından yönetilen anahtarı, en son anahtar sürümünü kullanacak şekilde otomatik olarak güncelleştirebilir. Müşteri tarafından yönetilen anahtar Azure Key Vault döndürüldüğünde, Azure depolama, şifreleme için anahtarın en son sürümünü otomatik olarak kullanmaya başlayacaktır.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Müşteri tarafından yönetilen anahtarları Azure portal anahtar sürümünün otomatik güncelleştirilmesiyle birlikte yapılandırmak için aşağıdaki adımları izleyin:

1. Depolama hesabınıza gidin.
1. Depolama hesabının **Ayarlar** dikey penceresinde **şifreleme**' ye tıklayın. Varsayılan olarak, aşağıdaki görüntüde gösterildiği gibi, anahtar yönetimi **Microsoft tarafından yönetilen anahtarlar** olarak ayarlanır.

    ![Şifreleme seçeneğini gösteren Portal ekran görüntüsü](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. **Müşteri tarafından yönetilen anahtarlar** seçeneğini belirleyin.
1. **Key Vault seçin** seçeneğini belirleyin.
1. **Anahtar Kasası ve anahtar seçin '** i seçin.
1. Kullanmak istediğiniz anahtarı içeren anahtar kasasını seçin.
1. Anahtar kasasından anahtarı seçin.

   ![Anahtar Kasası ve anahtarın nasıl seçileceğini gösteren ekran görüntüsü](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. Yaptığınız değişiklikleri kaydedin.

Anahtarı belirttikten sonra Azure portal, anahtar sürümünün otomatik güncelleştirilme özelliğinin etkin olduğunu ve şifreleme için kullanılmakta olan anahtar sürümü görüntülediğini gösterir.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="Anahtar sürümünün etkinleştirildiği otomatik güncelleştirmeyi gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Müşteri tarafından yönetilen anahtarları PowerShell ile anahtar sürümünün otomatik olarak güncelleştirilmesiyle yapılandırmak için, [az. Storage](https://www.powershellgallery.com/packages/Az.Storage) Module, sürüm 2.0.0 veya üstünü yüklersiniz.

Müşteri tarafından yönetilen bir anahtarın anahtar sürümünü otomatik olarak güncelleştirmek için, depolama hesabı için müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırırken anahtar sürümünü atlayın. Aşağıdaki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını güncelleştirmek için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) öğesini çağırın ve depolama hesabı için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere **-keyvaultencryption** seçeneğini ekleyin.

Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Müşteri tarafından yönetilen anahtarları, Azure CLı ile anahtar sürümünün otomatik güncelleştirilmesiyle birlikte yapılandırmak için [Azure CLI sürüm 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) veya üstünü yükler. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli).

Müşteri tarafından yönetilen bir anahtarın anahtar sürümünü otomatik olarak güncelleştirmek için, depolama hesabı için müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırırken anahtar sürümünü atlayın. Aşağıdaki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını güncelleştirmek için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) ' i çağırın. Parametresi dahil edin `--encryption-key-source` ve `Microsoft.Keyvault` hesap için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere olarak ayarlayın.

Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Anahtar sürümlerinin el ile güncelleştirilmesi için şifrelemeyi yapılandırma

Anahtar sürümünü el ile güncelleştirmeyi tercih ediyorsanız, sürümü, müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırdığınız sırada açıkça belirtin. Bu durumda, anahtar kasasında yeni bir sürüm oluşturulduğunda Azure Storage anahtar sürümünü otomatik olarak güncelleştirmez. Yeni bir anahtar sürümü kullanmak için, Azure depolama şifrelemesi için kullanılan sürümü el ile güncelleştirmeniz gerekir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Müşteri tarafından yönetilen anahtarları Azure portal anahtar sürümünün el ile güncelleştirilmesiyle birlikte yapılandırmak için, sürüm dahil olmak üzere anahtar URI 'sini belirtin. Bir anahtarı URI olarak belirtmek için şu adımları izleyin:

1. Azure portal anahtar URI 'sini bulmak için, anahtar kasanıza gidin ve **anahtarlar** ayarını seçin. İstediğiniz anahtarı seçin ve ardından sürümlerini görüntülemek için anahtara tıklayın. Bu sürümün ayarlarını görüntülemek için bir anahtar sürüm seçin.
1. URI sağlayan **anahtar tanımlayıcı** alanının değerini kopyalayın.

    ![Anahtar Kasası anahtar URI 'sini gösteren ekran görüntüsü](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. Depolama hesabınız için **şifreleme anahtarı** ayarları ' nda **anahtar URI 'si girin** seçeneğini belirleyin.
1. Kopyaladığınız URI 'yi **anahtar URI** alanına yapıştırın. Anahtar sürümünün otomatik güncelleştirilmesini sağlamak için URI 'den anahtar sürümünü atlayın.

   ![Anahtar URI 'sini girmeyi gösteren ekran görüntüsü](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. Anahtar kasasını içeren aboneliği belirtin.
1. Yaptığınız değişiklikleri kaydedin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Müşteri tarafından yönetilen anahtarları, anahtar sürümünün el ile güncelleştirilmesiyle birlikte yapılandırmak için, depolama hesabı için şifrelemeyi yapılandırırken açık olarak anahtar sürümünü sağlayın. Aşağıdaki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını güncelleştirmek için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) öğesini çağırın ve depolama hesabı için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere **-keyvaultencryption** seçeneğini ekleyin.

Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Anahtar sürümünü el ile güncelleştirdiğinizde, yeni sürümü kullanmak için depolama hesabının şifreleme ayarlarını güncelleştirmeniz gerekir. İlk olarak, anahtarın en son sürümünü almak için [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) öğesini çağırın. Ardından, önceki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını anahtarın yeni sürümünü kullanacak şekilde güncelleştirmek için [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) komutunu çağırın.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Müşteri tarafından yönetilen anahtarları, anahtar sürümünün el ile güncelleştirilmesiyle birlikte yapılandırmak için, depolama hesabı için şifrelemeyi yapılandırırken açık olarak anahtar sürümünü sağlayın. Aşağıdaki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını güncelleştirmek için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) ' i çağırın. Parametresi dahil edin `--encryption-key-source` ve `Microsoft.Keyvault` hesap için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere olarak ayarlayın.

Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

Anahtar sürümünü el ile güncelleştirdiğinizde, yeni sürümü kullanmak için depolama hesabının şifreleme ayarlarını güncelleştirmeniz gerekir. İlk olarak, az keykasa [Show](/cli/azure/keyvault#az_keyvault_show)komutunu çağırarak Anahtar Kasası URI 'sini sorgulama, [az keykasa anahtar listesi-sürümlerini](/cli/azure/keyvault/key#az_keyvault_key_list-versions)çağırarak anahtar sürümü için. Ardından, önceki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını anahtarın yeni sürümünü kullanacak şekilde güncelleştirmek için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) ' i çağırın.

---

## <a name="change-the-key"></a>Anahtarı değiştirme

Azure depolama şifrelemesi için kullandığınız anahtarı dilediğiniz zaman değiştirebilirsiniz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Anahtarı Azure portal değiştirmek için şu adımları izleyin:

1. Depolama hesabınıza gidin ve **şifreleme** ayarlarını görüntüleyin.
1. Anahtar kasasını seçin ve yeni bir anahtar seçin.
1. Yaptığınız değişiklikleri kaydedin.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Anahtarı PowerShell ile değiştirmek için, [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) komutunu, [müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](#configure-encryption-with-customer-managed-keys) ve yeni anahtar adı ve sürümünü sağlama bölümünde gösterildiği gibi çağırın. Yeni anahtar farklı bir anahtar kasasında ise, Anahtar Kasası URI 'sini de güncelleştirmeniz gerekir.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Anahtarı Azure CLı ile değiştirmek için, [müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](#configure-encryption-with-customer-managed-keys) bölümünde gösterildiği gibi [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) çağrısı yapın ve yeni anahtar adını ve sürümünü sağlayın. Yeni anahtar farklı bir anahtar kasasında ise, Anahtar Kasası URI 'sini de güncelleştirmeniz gerekir.

---

## <a name="revoke-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları iptal et

Müşteri tarafından yönetilen bir anahtarı iptal etmek, depolama hesabı ile Anahtar Kasası arasındaki ilişkiyi kaldırır.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Müşteri tarafından yönetilen anahtarları Azure portal iptal etmek için, [müşteri tarafından yönetilen anahtarları devre dışı bırak](#disable-customer-managed-keys)bölümünde açıklandığı gibi anahtarı devre dışı bırakın.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Anahtar Kasası erişim ilkesini kaldırarak müşteri tarafından yönetilen anahtarları iptal edebilirsiniz. Müşteri tarafından yönetilen bir anahtarı PowerShell ile iptal etmek için, aşağıdaki örnekte gösterildiği gibi [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) komutunu çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Anahtar Kasası erişim ilkesini kaldırarak müşteri tarafından yönetilen anahtarları iptal edebilirsiniz. Azure CLı ile müşteri tarafından yönetilen bir anahtarı iptal etmek için, aşağıdaki örnekte gösterildiği gibi [az keykasa Delete-Policy](/cli/azure/keyvault#az_keyvault_delete_policy) komutunu çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları devre dışı bırak

Müşteri tarafından yönetilen anahtarları devre dışı bıraktığınızda, depolama hesabınız Microsoft tarafından yönetilen anahtarlarla yeniden şifrelenir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal müşteri tarafından yönetilen anahtarları devre dışı bırakmak için şu adımları izleyin:

1. Depolama hesabınıza gidin ve **şifreleme** ayarlarını görüntüleyin.
1. **Kendi anahtarınızı kullanın** ayarının yanındaki onay kutusunun işaretini kaldırın.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Müşteri tarafından yönetilen anahtarları PowerShell ile devre dışı bırakmak için, aşağıdaki örnekte gösterildiği gibi, seçeneğiyle [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) ' `-StorageEncryption` ı çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Müşteri tarafından yönetilen anahtarları Azure CLı ile devre dışı bırakmak için, aşağıdaki örnekte gösterildiği gibi [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) çağırın ve `--encryption-key-source parameter` öğesini olarak ayarlayın `Microsoft.Storage` . Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi ve önceki örneklerde tanımlanan değişkenleri kullanmayı unutmayın.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](storage-service-encryption.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](customer-managed-keys-overview.md)
- [Azure Key Vault yönetilen HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)](customer-managed-keys-configure-key-vault-hsm.md)
