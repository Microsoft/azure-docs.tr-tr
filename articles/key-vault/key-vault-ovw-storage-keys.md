---
title: Azure Key Vault ve Azure CLı ile depolama hesabı anahtarlarını yönetme
description: Depolama hesabı anahtarları, Azure depolama hesabına Azure Key Vault ve anahtar tabanlı erişim arasında sorunsuz bir tümleştirme sağlar.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 62faf33dc8b3690036407972e12633e741a85d78
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176743"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Key Vault ve Azure CLı ile depolama hesabı anahtarlarını yönetme

Bir Azure depolama hesabı, hesap adı ve anahtarı kapsayan kimlik bilgilerini kullanır. Anahtar otomatik olarak oluşturulur ve şifreleme anahtarı olarak değil, bir parola görevi görür. Key Vault, depolama hesabı anahtarlarını [Key Vault gizli](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)dizileri olarak depolayarak yönetir. 

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

## <a name="service-principal-application-id"></a>Hizmet sorumlusu uygulama KIMLIĞI

Bir Azure AD kiracısı, kayıtlı her uygulamayı bir [hizmet sorumlusu](/azure/active-directory/develop/developer-glossary#service-principal-object)ile sağlar. Hizmet sorumlusu, RBAC aracılığıyla diğer Azure kaynaklarına erişim için yetkilendirme kurulumu sırasında kullanılan uygulama KIMLIĞI olarak görev yapar.

Key Vault, tüm Azure AD kiracılarında önceden kaydedilmiş bir Microsoft uygulamasıdır. Key Vault, her bir Azure bulutu 'nda aynı uygulama KIMLIĞI altına kaydedilir.

| Kira | Una | Uygulama kimliği |
| --- | --- | --- |
| Azure AD | Azure Kamu | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure genel | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Farklı  | Kaydedilmemiş | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzu gerçekleştirmek için, önce aşağıdakileri yapmanız gerekir:

- [Azure CLI 'Yı yükler](/cli/azure/install-azure-cli).
- [Anahtar Kasası oluşturma](quick-create-cli.md)
- [Bir Azure depolama hesabı oluşturun](../storage/common/storage-quickstart-create-account.md?tabs=azure-cli). Depolama hesabı adı yalnızca küçük harfler ve rakamlar kullanmalıdır. Adın uzunluğu 3 ile 24 karakter arasında olmalıdır.
      
## <a name="manage-storage-account-keys"></a>Depolama hesabı anahtarlarını yönetme

### <a name="connect-to-your-azure-account"></a>Azure hesabınıza bağlanma

[Az Login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) komutunu kullanarak Azure CLI oturumunuzun kimliğini doğrulayın.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Depolama hesabınıza Key Vault erişim izni verin

Depolama hesabınıza erişmek Key Vault erişim sağlamak için Azure CLı [az role atama oluştur](/cli/azure/role/assignment?view=azure-cli-latest) komutunu kullanın. Komutu aşağıdaki parametre değerlerini belirtin:

- `--role`: "depolama hesabı anahtar operatörü hizmeti rolü" RBAC rolünü geçirin. Bu rol, erişim kapsamını depolama hesabınızla sınırlandırır. Klasik bir depolama hesabı için bunun yerine "klasik depolama hesabı anahtar Işletmeni hizmeti rolü" geçirin.
- `--assignee-object-id`: Azure genel bulutundaki Key Vault nesne KIMLIĞI olan "93c27d83-f79b-4CB2-8dd4-4aa716542e74" değerini geçirin. (Azure Kamu bulutundaki Key Vault nesne KIMLIĞINI almak için bkz. [hizmet sorumlusu uygulama kimliği](#service-principal-application-id).)
- `--scope`: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` biçiminde olan depolama hesabı kaynak KIMLIĞINIZI geçirin. Abonelik KIMLIĞINIZI bulmak için Azure CLı [az Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) komutunu kullanın; depolama hesabı adınızı ve depolama hesabı kaynak grubunuzu bulmak için Azure CLı [az Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) komutunu kullanın.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault yönetilen bir depolama hesabı oluşturma

 Azure CLı [az keykasa Storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) komutunu kullanarak Key Vault yönetilen bir depolama hesabı oluşturun. Yeniden oluşturma dönemini 90 gün olarak ayarlayın. 90 gün sonra, Key Vault `key1` ' ı yeniden üretir ve etkin anahtarı `key2` ' den `key1` ' ye değiştirir. `key1` daha sonra etkin anahtar olarak işaretlenir. Komutu aşağıdaki parametre değerlerini belirtin:

- `--vault-name`: anahtar kasanızın adını geçirin. Anahtar kasanızın adını bulmak için Azure CLı [az keykasası List](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) komutunu kullanın.
- `-n`: depolama hesabınızın adını geçirin. Depolama hesabınızın adını bulmak için Azure CLı [az Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) komutunu kullanın.
- `--resource-id`: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` biçiminde olan depolama hesabı kaynak KIMLIĞINIZI geçirin. Abonelik KIMLIĞINIZI bulmak için Azure CLı [az Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) komutunu kullanın; depolama hesabı adınızı ve depolama hesabı kaynak grubunuzu bulmak için Azure CLı [az Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) komutunu kullanın.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Paylaşılan erişim imza belirteçleri

Ayrıca, Key Vault paylaşılan erişim imzası belirteçleri oluşturmasını isteyebilirsiniz. Paylaşılan erişim imzası, Depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. İstemci, hesap anahtarlarınızı paylaşmadan Depolama hesabınızdaki kaynaklara erişim izni verebilirsiniz. Paylaşılan erişim imzası, hesap anahtarlarınızla ödün vermeden depolama kaynaklarınızı paylaşmak için güvenli bir yol sağlar.

Bu bölümdeki komutlar aşağıdaki eylemleri tamamlar:

- Hesap paylaşılan erişim imzası tanımını `<YourSASDefinitionName>` olarak ayarlayın. Tanım, anahtar kasanız `<YourKeyVaultName>` Key Vault yönetilen bir depolama hesabında `<YourStorageAccountName>` olarak ayarlanır.
- Blob, dosya, tablo ve kuyruk Hizmetleri için bir hesap paylaşılan erişim imza belirteci oluşturun. Belirteç, kaynak türleri hizmeti, kapsayıcısı ve nesnesi için oluşturulur. Belirteç, https üzerinden ve belirtilen başlangıç ve bitiş tarihleriyle birlikte tüm izinlerle oluşturulur.
- Kasada Key Vault yönetilen bir depolama paylaşılan erişim imzası tanımı ayarlayın. Tanım, oluşturulan paylaşılan erişim imzası belirtecinin şablon URI 'sine sahiptir. Tanım `account` paylaşılan erişim imzası türüne sahiptir ve N gün için geçerlidir.
- Paylaşılan erişim imzasının anahtar kasanıza gizli dizi olarak kaydedildiğini doğrulayın.

### <a name="create-a-shared-access-signature-token"></a>Paylaşılan erişim imza belirteci oluşturma

Azure CLı [az Storage Account Generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) komutunu kullanarak bir paylaşılan erişim imzası tanımı oluşturun. Bu işlem, `storage` ve `setsas` izinlerini gerektirir.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
İşlem başarıyla çalıştıktan sonra çıktıyı kopyalayın.

```console
"se=2020-01-01&sp=***"
```

Bu çıktı bir sonraki adımda `--template-id` parametresine geçirilecek.

### <a name="generate-a-shared-access-signature-definition"></a>Paylaşılan erişim imzası tanımı oluştur

Bir paylaşılan erişim imzası tanımı oluşturmak için, önceki adımdan gelen çıktıyı `--template-id` parametresine geçirerek Azure CLı [az keykasası Storage SAS-Definition Create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) komutunu kullanın.  Seçtiğiniz adı `-n` parametresine sağlayabilirsiniz.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Paylaşılan erişim imzası tanımını doğrulama

Paylaşılan erişim imzası tanımının Azure CLı [az keykasası gizli listesini](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) kullanarak anahtar kasasında depolandığını ve [az keykasasecret gizli gösterme](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) komutlarını doğrulayabilirsiniz.

İlk olarak, [az keykasasecret List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) komutunu kullanarak anahtar kasanızda paylaşılan erişim imzası tanımını bulun.

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

SAS tanımınıza karşılık gelen gizli dizi şu özelliklere sahip olacaktır:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Bundan böyle gizli dizi içeriğini görüntülemek için [az keykasası Secret Show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) komutunu ve `id` özelliğini kullanabilirsiniz.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

Bu komutun çıktısı, SAS tanım dizenizi @ no__t-0 olarak gösterir.


## <a name="next-steps"></a>Sonraki adımlar

- [Anahtarlar, gizli diziler ve sertifikalar](https://docs.microsoft.com/rest/api/keyvault/)hakkında daha fazla bilgi edinin.
- [Azure Key Vault ekip bloguna](https://blogs.technet.microsoft.com/kv/)ilişkin makaleleri gözden geçirin.
- Bkz. [keykasası depolama](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) başvurusu belgeleri.
