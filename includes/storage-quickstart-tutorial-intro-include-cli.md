---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 4ad977dc8cbaa85360092dbfd391a3c3b88f67bb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747935"
---
## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir Azure kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az group create \
    --name <resource-group-name> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

[az storage account create](/cli/azure/storage/account) komutuyla bir genel amaçlı depolama hesabı oluşturun. Genel amaçlı depolama hesabı; bloblar, dosyalar, tablolar ve kuyruklar olmak üzere dört hizmet için de kullanılabilir.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Depolama hesabının kimlik bilgilerini belirtme

Bu öğreticideki komutların çoğu için Azure CLI’sının depolama hesabı kimlik bilgilerinize ihtiyacı vardır. Bunu yapmak için birkaç seçenek olsa da bunları sağlamanın en kolay yollarından biri `AZURE_STORAGE_ACCOUNT` ve `AZURE_STORAGE_KEY` ortam değişkenlerini ayarlamaktır.

> [!NOTE]
> Bu makalede, Bash kullanarak ortam değişkenlerinin nasıl ayarlanacağı gösterilmektedir. Diğer ortamlar için sözdizimi değişiklikleri gerekebilir.

İlk olarak, [az Storage Account Keys List](/cli/azure/storage/account/keys) komutunu kullanarak depolama hesabı anahtarlarınızı görüntüleyin. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account keys list \
    --account-name <account-name> \
    --resource-group <resource-group-name> \
    --output table
```

Şimdi `AZURE_STORAGE_ACCOUNT` ve `AZURE_STORAGE_KEY` ortam değişkenlerini ayarlayın. Bunu bash kabuğu 'nda `export` komutunu kullanarak yapabilirsiniz. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```bash
export AZURE_STORAGE_ACCOUNT="<account-name>"
export AZURE_STORAGE_KEY="<account-key>"
```

Azure portal kullanarak hesap erişim anahtarlarını alma hakkında daha fazla bilgi için, [Azure Portal depolama hesabı ayarlarını yönetme](../articles/storage/common/storage-account-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#access-keys)bölümündeki **erişim anahtarlarına** bakın.