---
title: Hızlı başlangıç-Azure CLı ile blob oluşturma
titleSuffix: Azure Storage
description: Bu hızlı başlangıçta, Azure CLı 'yı kullanarak bir blobu Azure depolama 'ya yükleme, blob indirme ve bir kapsayıcıdaki Blobları listeleme hakkında bilgi edineceksiniz.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 04f793f78cef938c31e7a30aad5569a54eb461a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104613116"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Hızlı başlangıç: Azure CLı ile Bloblar oluşturma, indirme ve listeleme

Azure CLI, Azure kaynaklarını yönetmek için Azure tarafından sunulan komut satırı deneyimidir. Azure Cloud Shell ile kendi tarayıcınızda da kullanabilirsiniz. Dilerseniz macOS, Linux veya Windows’a yükleyebilir ve komut satırından çalıştırabilirsiniz. Bu hızlı başlangıçta Azure Blob depolamaya veri yüklemek ve verileri indirmek için Azure CLI kullanma hakkında ayrıntılı bilgiler öğrenirsiniz.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.46 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="authorize-access-to-blob-storage"></a>Blob depolamaya erişim yetkisi verme

Azure CLı 'dan Azure AD kimlik bilgileriyle veya depolama hesabı erişim anahtarını kullanarak blob depolamaya erişim yetkisi verebilirsiniz. Azure AD kimlik bilgilerinin kullanılması önerilir. Bu makalede, Azure AD kullanarak BLOB depolama işlemlerini yetkilendirme işlemi gösterilmektedir.

Blob depolamaya yönelik veri işlemlerine yönelik Azure CLı komutları `--auth-mode` , belirli bir işlemin yetkilendirmesini belirtmenize olanak sağlayan parametresini destekler. `--auth-mode`Parametresini `login` Azure AD kimlik bilgileriyle yetkilendirmek için olarak ayarlayın. Daha fazla bilgi için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Yalnızca BLOB depolama veri işlemleri parametreyi destekler `--auth-mode` . Kaynak grubu veya depolama hesabı oluşturma gibi yönetim işlemleri, yetkilendirme için otomatik olarak Azure AD kimlik bilgilerini kullanır.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir Azure kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

[az storage account create](/cli/azure/storage/account) komutuyla bir genel amaçlı depolama hesabı oluşturun. Genel amaçlı depolama hesabı; bloblar, dosyalar, tablolar ve kuyruklar olmak üzere dört hizmet için de kullanılabilir.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bloblar her zaman bir kapsayıcıya yüklenir. Kapsayıcılardaki blob gruplarını, dosyalarınızı bilgisayarınızdaki dosyalarınıza düzenlemenize benzer şekilde düzenleyebilirsiniz. [az storage container create](/cli/azure/storage/container) komutunu kullanarak blobları depolamak için bir kapsayıcı oluşturun.

Aşağıdaki örnek, kapsayıcıyı oluşturma işlemini yetkilendirmek için Azure AD hesabınızı kullanır. Kapsayıcıyı oluşturmadan önce, [Depolama Blobu veri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rolünü kendinize atayın. Hesap sahibi olsanız bile, depolama hesabında veri işlemleri gerçekleştirmek için açık izinlere sahip olmanız gerekir. Azure rolleri atama hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak erişim için bir Azure rolü atama](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json).  

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> Azure rolü atamalarının yayılması birkaç dakika sürebilir.

Ayrıca, kapsayıcıyı oluşturmak için işlemi yetkilendirmek üzere depolama hesabı anahtarını da kullanabilirsiniz. Veri işlemlerini Azure CLı ile yetkilendirme hakkında daha fazla bilgi için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="upload-a-blob"></a>Blobu karşıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. Bu hızlı başlangıçtaki örneklerde, blok bloblarla nasıl çalışılacağı gösterilmektedir.

İlk olarak, bir blok blobuna yüklemek için bir dosya oluşturun. Azure Cloud Shell kullanıyorsanız, bir dosya oluşturmak için aşağıdaki komutu kullanın:

```bash
vi helloworld
```

Dosya açıldığında, **Ekle**' ye basın. *Merhaba Dünya* yazın ve ardından **ESC** tuşuna basın. Sonra, şunu yazın *: x* yazın ve **ENTER** tuşuna basın.

Bu örnekte, son adımda [az storage blob upload](/cli/azure/storage/blob) komutuyla oluşturduğunuz kapsayıcıya bir blob yükleyeceksiniz. Dosya kök dizininde oluşturulduğundan bu yana bir dosya yolu belirtmeniz gerekmez. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Bu işlemle, daha önce oluşturulmadıysa bir blob oluşturulur, aksi takdirde üzerine yazılacaktır. Devam etmeden önce istediğiniz sayıda dosyayı karşıya yükleyin.

Aynı anda birden fazla dosya yüklemek için [az storage blob upload-batch](/cli/azure/storage/blob) komutunu kullanabilirsiniz.

## <a name="list-the-blobs-in-a-container"></a>Kapsayıcıdaki blobları listeleme

[az storage blob list](/cli/azure/storage/blob) komutuyla kapsayıcıdaki blobları listeleyin. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Blobu indirme

Önceden karşıya yüklediğiniz bir blobu indirmek için [az storage blob download](/cli/azure/storage/blob) komutunu kullanın. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>AzCopy ile veri aktarımı

AzCopy komut satırı yardımcı programı, Azure depolama için yüksek performanslı ve komut dosyalı veri aktarımı sağlar. BLOB depolama ve Azure dosyalarına veri aktarmak için AzCopy kullanabilirsiniz. AzCopy 'in en son sürümü olan AzCopy ile v10 arasındaki hakkında daha fazla bilgi için bkz. [AzCopy ile çalışmaya başlama](../common/storage-use-azcopy-v10.md). AzCopy ile v10 arasındaki 'i blob Storage ile kullanma hakkında bilgi edinmek için bkz. [AzCopy ve BLOB Storage ile veri aktarma](../common/storage-use-azcopy-v10.md#transfer-data).

Aşağıdaki örnek, bir Blobun yerel bir dosyayı karşıya yüklemek için AzCopy kullanır. Örnek değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Depolama hesabı da dahil olmak üzere bu hızlı başlangıç kapsamında oluşturduğunuz kaynakları silmek istiyorsanız, [az Group Delete](/cli/azure/group) komutunu kullanarak kaynak grubunu silin. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, dosyaları yerel bir dosya sistemi ile Azure Blob depolama alanındaki bir kapsayıcı arasında aktarmayı öğrendiniz. Azure CLı kullanarak blob depolamayla çalışma hakkında daha fazla bilgi edinmek için, BLOB depolama için Azure CLı örnekleri ' ni gezin.

> [!div class="nextstepaction"]
> [BLOB depolama için Azure CLı örnekleri](./storage-samples-blobs-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
