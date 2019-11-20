---
title: Azure CLı 'dan Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma
description: Azure CLı 'yı kullanarak Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları nasıl yapılandıracağınızı öğrenin. Müşteri tarafından yönetilen anahtarlar, erişim denetimleri oluşturmanıza, döndürmenize, devre dışı bırakmanızı ve iptal edebilmesini sağlar.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 18209816b5b73f58a8112efca0363b31dd47bd91
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374280"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Azure CLı 'dan Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları yapılandırma

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Bu makalede, Azure CLı kullanarak müşteri tarafından yönetilen anahtarlarla bir anahtar kasasının nasıl yapılandırılacağı gösterilmektedir.

> [!IMPORTANT]
> Azure depolama şifrelemesi ile müşteri tarafından yönetilen anahtarların kullanılması, anahtar kasasında iki özellik ayarlanmasını, **geçici silme** ve **Temizleme işlemi**yapılmasını gerektirir. Bu özellikler varsayılan olarak etkinleştirilmemiştir. Bu özellikleri etkinleştirmek için PowerShell veya Azure CLı kullanın.
> Yalnızca RSA anahtarları ve anahtar boyutu 2048 desteklenir.

## <a name="assign-an-identity-to-the-storage-account"></a>Depolama hesabına bir kimlik atayın

Depolama hesabınız için müşteri tarafından yönetilen anahtarları etkinleştirmek üzere öncelikle depolama hesabına sistem tarafından atanan bir yönetilen kimlik atayın. Bu yönetilen kimliği, depolama hesabı izinlerini anahtar kasasına erişim vermek için kullanacaksınız.

Azure CLı kullanarak yönetilen bir kimlik atamak için [az Storage Account Update](/cli/azure/storage/account#az-storage-account-update)' i çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Azure CLı ile sistem tarafından atanan yönetilen kimlikleri yapılandırma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Yeni bir Anahtar Kasası oluşturun

Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarları depolamak üzere kullandığınız anahtar kasasında iki anahtar koruma ayarı etkinleştirilmiş, **geçici silme** ve **Temizleme işlemi**yapılmalıdır. PowerShell veya Azure CLı kullanarak bu ayarların etkinleştirildiği yeni bir Anahtar Kasası oluşturmak için aşağıdaki komutları yürütün. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın. 

Azure CLı kullanarak yeni bir Anahtar Kasası oluşturmak için [az keykasa Create komutunu](/cli/azure/keyvault#az-keyvault-create)çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Anahtar Kasası erişim ilkesini yapılandırma

Daha sonra, depolama hesabının bu hesaba erişim izinleri olması için Anahtar Kasası için erişim ilkesini yapılandırın. Bu adımda, daha önce depolama hesabına atadığınız yönetilen kimliği kullanacaksınız.

Anahtar Kasası için erişim ilkesini ayarlamak için [az keykasa Set-Policy komutunu](/cli/azure/keyvault#az-keyvault-set-policy)çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

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
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Yeni anahtar oluştur

Ardından, anahtar kasasında bir anahtar oluşturun. Bir anahtar oluşturmak için [az keykasa Key Create komutunu](/cli/azure/keyvault/key#az-keyvault-key-create)çağırın. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma

Varsayılan olarak, Azure depolama şifrelemesi Microsoft tarafından yönetilen anahtarları kullanır. Azure depolama hesabınızı müşteri tarafından yönetilen anahtarlar için yapılandırın ve depolama hesabıyla ilişkilendirilecek anahtarı belirtin.

Depolama hesabının şifreleme ayarlarını güncelleştirmek için [az Storage Account Update](/cli/azure/storage/account#az-storage-account-update)' i çağırın. Bu örnek ayrıca anahtarı depolama hesabıyla ilişkilendirmek için gereken değerlerin her ikisi de Anahtar Kasası URI 'sini ve anahtar sürümünü sorgular. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Anahtar sürümünü güncelleştirme

Bir anahtarın yeni bir sürümünü oluşturduğunuzda, yeni sürümü kullanmak için depolama hesabını güncelleştirmeniz gerekir. İlk olarak, az keykasa [Show](/cli/azure/keyvault#az-keyvault-show)komutunu çağırarak Anahtar Kasası URI 'sini sorgulama, [az keykasa anahtar listesi-sürümlerini](/cli/azure/keyvault/key#az-keyvault-key-list-versions)çağırarak anahtar sürümü için. Ardından, önceki bölümde gösterildiği gibi, depolama hesabının şifreleme ayarlarını anahtarın yeni sürümünü kullanacak şekilde güncelleştirmek için [az Storage Account Update](/cli/azure/storage/account#az-storage-account-update) ' i çağırın.

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veriler için Azure depolama şifrelemesi](storage-service-encryption.md) 
- [Azure Key Vault nedir](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
