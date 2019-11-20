---
title: Azure Hızlı Başlangıç - Azure CLI kullanarak nesne depolamada blob oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, Azure CLı 'yı kullanarak bir blobu Azure depolama 'ya yükleme, blob indirme ve bir kapsayıcıdaki Blobları listeleme hakkında bilgi edineceksiniz.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747933"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Hızlı Başlangıç: Azure CLI’sini kullanarak blobları yükleme, indirme ve listeleme

Azure CLI, Azure kaynaklarını yönetmek için Azure tarafından sunulan komut satırı deneyimidir. Azure Cloud Shell ile kendi tarayıcınızda da kullanabilirsiniz. Dilerseniz macOS, Linux veya Windows’a yükleyebilir ve komut satırından çalıştırabilirsiniz. Bu hızlı başlangıçta Azure Blob depolamaya veri yüklemek ve verileri indirmek için Azure CLI kullanma hakkında ayrıntılı bilgiler öğrenirsiniz.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı seçerseniz bu hızlı başlangıç için Azure CLI 2.0.4 veya sonraki bir sürümünü kullanmanız gerekir. Sürümünüzü belirlemek için `az --version` çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

Bloblar her zaman bir kapsayıcıya yüklenir. Bu, blob gruplarını bilgisayarınızdaki dosyaları klasörler halinde düzenlediğiniz gibi düzenleyebilmenizi sağlar.

[az storage container create](/cli/azure/storage/container) komutunu kullanarak blobları depolamak için bir kapsayıcı oluşturun.

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Blobu karşıya yükleme

Blob depolama blok blobları, ekleme bloblarını ve sayfa bloblarını destekler. Bu hızlı başlangıçtaki örneklerde, blok bloblarla nasıl çalışılacağı gösterilmektedir.

İlk olarak, bir blok blobuna yüklemek için bir dosya oluşturun. Azure Cloud Shell kullanıyorsanız, bir dosya oluşturmak için aşağıdaki komutu kullanın:

```bash
vi helloworld
```

Dosya açıldığında, **Ekle**' ye basın. *Merhaba Dünya*yazın ve ardından **ESC**tuşuna basın. Sonra, şunu yazın *: x*yazın ve **ENTER**tuşuna basın.

Bu örnekte, son adımda [az storage blob upload](/cli/azure/storage/blob) komutuyla oluşturduğunuz kapsayıcıya bir blob yükleyeceksiniz. Dosya kök dizininde oluşturulduğundan bu yana bir dosya yolu belirtmek gerekli değildir:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Bu işlemle, daha önce oluşturulmadıysa bir blob oluşturulur, aksi takdirde üzerine yazılacaktır. Devam etmeden önce istediğiniz sayıda dosyayı karşıya yükleyin.

Aynı anda birden fazla dosya yüklemek için [az storage blob upload-batch](/cli/azure/storage/blob) komutunu kullanabilirsiniz.

## <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

[az storage blob list](/cli/azure/storage/blob) komutuyla kapsayıcıdaki blobları listeleyin.

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Blob indirme

Önceden karşıya yüklediğiniz bir blobu indirmek için [az storage blob download](/cli/azure/storage/blob) komutunu kullanın.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>AzCopy ile veri aktarımı

[AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) yardımcı programı, Azure Depolama’ya yüksek performanslı betik oluşturulabilir veri aktarımı için diğer bir seçenektir. Blob, Dosya ve Tablo depolamaları arasında veri aktarmak için AzCopy kullanabilirsiniz.

Aşağıdaki örnek, *örnek kapsayıcı* kapsayıcısına *Dosyam. txt* adlı bir dosyayı yüklemek için AzCopy kullanır. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturduğunuz depolama hesabı da dahil olmak üzere kaynak grubunuzdaki kaynakların hiçbirine artık ihtiyacınız yoksa [az Group Delete](/cli/azure/group) komutuyla kaynak grubunu silin. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, dosyaları yerel bir dosya sistemi ile Azure Blob depolama alanındaki bir kapsayıcı arasında aktarmayı öğrendiniz. Azure Depolama’da bloblarla çalışma hakkında daha fazla bilgi edinmek için, Azure Blob depolamayla çalışma hakkındaki öğretici ile devam edin.

> [!div class="nextstepaction"]
> [Nasıl yapılır: Azure CLI ile blob depolama işlemleri](storage-how-to-use-blobs-cli.md)
