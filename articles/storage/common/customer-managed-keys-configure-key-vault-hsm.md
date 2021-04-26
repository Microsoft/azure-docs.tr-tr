---
title: Azure Key Vault yönetilen HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)
titleSuffix: Azure Storage
description: Azure CLı kullanarak Azure Key Vault yönetilen HSM (Önizleme) içinde depolanan müşteri tarafından yönetilen anahtarlarla Azure depolama şifrelemesini yapılandırma hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9b40c934cb428a31a3feb77195518d5351818d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785370"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Azure Key Vault yönetilen HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)

Azure depolama, bekleyen bir depolama hesabındaki tüm verileri şifreler. Varsayılan olarak, veriler Microsoft tarafından yönetilen anahtarlarla şifrelenir. Şifreleme anahtarları üzerinde ek denetim için kendi anahtarlarınızı yönetebilirsiniz. Müşteri tarafından yönetilen anahtarların Azure Key Vault veya Key Vault yönetilen donanım güvenlik modeli (HSM) (Önizleme) içinde depolanması gerekir. Azure Key Vault yönetilen HSM, FIPS 140-2 düzey 3 tarafından doğrulanan bir HSM 'dir.

Bu makalede, Azure CLı kullanarak yönetilen bir HSM 'de depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma gösterilmektedir. Bir anahtar kasasında depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma hakkında bilgi edinmek için bkz. [Azure Key Vault 'de depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Azure Key Vault yönetilen HSM 'de depolanan, müşteri tarafından yönetilen anahtarlarla şifreleme Şu anda **önizlemededir**. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan yasal koşullara yönelik [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.
>
> Azure Key Vault ve Azure Key Vault yönetilen HSM, yapılandırma için aynı API 'Leri ve yönetim arabirimlerini destekler.

## <a name="assign-an-identity-to-the-storage-account"></a>Depolama hesabına bir kimlik atayın

İlk olarak, depolama hesabına sistem tarafından atanan bir yönetilen kimlik atayın. Bu yönetilen kimliği, depolama hesabına yönetilen HSM 'ye erişim izni vermek için kullanacaksınız. Sistem tarafından atanan Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../../active-directory/managed-identities-azure-resources/overview.md).

Azure CLı kullanarak yönetilen bir kimlik atamak için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update)' i çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Yönetilen HSM 'ye erişim için depolama hesabına bir rol atama

Daha sonra, depolama hesabının yönetilen HSM 'ye izinleri olması için, **YÖNETILEN HSM şifreleme hizmeti şifreleme** rolünü depolama hesabının yönetilen kimliğine atayın. Microsoft, yönetilen kimliğe en az olası ayrıcalıklara izin vermek için rol atamasını ayrı anahtar düzeyine göre kapsammanızı önerir.

Depolama hesabı için rol ataması oluşturmak için [az Key kasa rolü atama oluştur](/cli/azure/role/assignment#az_role_assignment_create)' u çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Yönetilen HSM 'de bir anahtarla şifrelemeyi yapılandırma

Son olarak, yönetilen HSM 'de depolanan bir anahtarı kullanmak için, müşteri tarafından yönetilen anahtarlarla Azure depolama şifrelemesini yapılandırın. Desteklenen anahtar türleri 2048, 3072 ve 4096 boyutlarının RSA-HSM anahtarlarını içerir. Yönetilen bir HSM 'de anahtar oluşturmayı öğrenmek için bkz. [HSM anahtarı oluşturma](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key).

Şifrelemeyi yönetilen bir HSM 'de müşteri tarafından yönetilen anahtar kullanacak şekilde yapılandırmak için Azure CLı 2.12.0 veya üstünü kullanın. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli).

Müşteri tarafından yönetilen bir anahtarın anahtar sürümünü otomatik olarak güncelleştirmek için, depolama hesabı için müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırırken anahtar sürümünü atlayın. Aşağıdaki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını güncelleştirmek için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) ' i çağırın. `--encryption-key-source parameter` `Microsoft.Keyvault` Hesap için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere öğesini dahil edin ve olarak ayarlayın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Müşteri tarafından yönetilen bir anahtarın sürümünü el ile güncelleştirmek için, depolama hesabı için şifrelemeyi yapılandırırken anahtar sürümünü dahil edin:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Anahtar sürümünü el ile güncelleştirdiğinizde, yeni sürümü kullanmak için depolama hesabının şifreleme ayarlarını güncelleştirmeniz gerekir. İlk olarak, az keykasa [Show](/cli/azure/keyvault#az_keyvault_show)komutunu çağırarak Anahtar Kasası URI 'sini sorgulama, [az keykasa anahtar listesi-sürümlerini](/cli/azure/keyvault/key#az_keyvault_key_list_versions)çağırarak anahtar sürümü için. Ardından, önceki örnekte gösterildiği gibi, depolama hesabının şifreleme ayarlarını anahtarın yeni sürümünü kullanacak şekilde güncelleştirmek için [az Storage Account Update](/cli/azure/storage/account#az_storage_account_update) ' i çağırın.

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](storage-service-encryption.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](customer-managed-keys-overview.md)
