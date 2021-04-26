---
title: Verileri yönetmek için Azure CLı kullanma (Azure Data Lake Storage 2.)
description: Hiyerarşik bir ad alanına sahip depolama hesaplarındaki dizinleri ve dosyaları yönetmek için Azure CLı 'yi kullanın.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650363"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure CLı kullanarak Azure Data Lake Storage 2. dizinleri ve dosyaları yönetme

Bu makalede, hiyerarşik bir ad alanına sahip depolama hesaplarında dizin ve dosya oluşturmak ve yönetmek için [Azure Command-Line arabirimi 'nin (CLI)](/cli/azure/) nasıl kullanılacağı gösterilmektedir.

Dizinlerin ve dosyaların erişim denetim listelerini (ACL) alma, ayarlama ve güncelleştirme hakkında bilgi edinmek için bkz. [Azure Data Lake Storage 2. ACL 'leri yönetmek Için Azure CLI kullanma](data-lake-storage-acl-cli.md).

[Örnekler](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Geri bildirimde](https://github.com/Azure/azure-cli-extensions/issues) bulunun

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).

- Hiyerarşik ad alanı etkin olan bir depolama hesabı. Bir tane oluşturmak için [Bu](create-data-lake-storage-account.md) yönergeleri izleyin.

- Azure CLı sürümü `2.6.0` veya üzeri.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Azure CLı 'nin doğru sürümünün yüklü olduğundan emin olun

1. [Azure Cloud Shell](../../cloud-shell/overview.md)açın veya Azure CLI 'yı yerel olarak [yüklediyseniz](/cli/azure/install-azure-cli) , Windows PowerShell gibi bir komut konsol uygulaması açın.

2. Yüklü olan Azure CLı sürümünün `2.6.0` aşağıdaki komutu kullanarak veya daha yüksek olduğunu doğrulayın.

   ```azurecli
    az --version
   ```

   Azure CLı sürümünüz daha düşükse `2.6.0` , daha sonra yeni bir sürüm yüklersiniz. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Hesaba Bağlan

1. Azure CLı 'yi yerel olarak kullanıyorsanız Login komutunu çalıştırın.

   ```azurecli
   az login
   ```

   CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.

   Aksi takdirde, konumunda bir tarayıcı sayfası açın [https://aka.ms/devicelogin](https://aka.ms/devicelogin) ve terminalinizde görünen yetkilendirme kodunu girin. Ardından, tarayıcıda hesap kimlik bilgilerinizle oturum açın.

   Farklı kimlik doğrulama yöntemleri hakkında daha fazla bilgi edinmek için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](./authorize-data-operations-cli.md).

2. Kimliğiniz birden fazla abonelikle ilişkiliyse, etkin aboneliğinizi statik Web sitenizi barındıracak depolama hesabının aboneliğine ayarlayın.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>`Yer tutucu değerini ABONELIĞINIZIN kimliğiyle değiştirin.

> [!NOTE]
> Bu makalede sunulan örnekte Azure Active Directory (Azure AD) yetkilendirmesi gösterilmektedir. Yetkilendirme yöntemleri hakkında daha fazla bilgi edinmek için bkz. [Azure CLI ile blob veya kuyruk verilerine erişim yetkisi verme](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Kapsayıcı oluşturma

Bir kapsayıcı dosyalarınız için bir dosya sistemi görevi görür. Komutunu kullanarak bir tane oluşturabilirsiniz `az storage fs create` . 

Bu örnek adlı bir kapsayıcı oluşturur `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Kapsayıcı özelliklerini göster

Komutunu kullanarak bir kapsayıcının özelliklerini konsola yazdırabilirsiniz `az storage fs show` .

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Kapsayıcı içeriklerini Listele

Komutunu kullanarak bir dizinin içeriğini listeleyin `az storage fs file list` .

Bu örnek, adlı bir kapsayıcının içeriğini listeler `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Kapsayıcı silme

Komutunu kullanarak bir kapsayıcıyı silin `az storage fs delete` .

Bu örnekte adlı bir kapsayıcı silinir `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Dizin oluşturma

Komutunu kullanarak bir dizin başvurusu oluşturun `az storage fs directory create` . 

Bu örnek, adlı bir `my-directory` hesapta bulunan adlı bir kapsayıcıya adlı bir dizin ekler `my-file-system` `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Dizin özelliklerini göster

Komutunu kullanarak bir dizinin özelliklerini konsola yazdırabilirsiniz `az storage fs directory show` .

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Bir dizini yeniden adlandırma veya taşıma

Komutunu kullanarak bir dizini yeniden adlandırın veya taşıyın `az storage fs directory move` .

Bu örnek, adından `my-directory` aynı kapsayıcıdaki adı bir dizini yeniden adlandırır `my-new-directory` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Bu örnek, adlı bir kapsayıcıya bir dizin taşır `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Bir dizini silme

Komutunu kullanarak bir dizini silin `az storage fs directory delete` .

Bu örnek adlı bir dizini siler `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Bir dizinin mevcut olup olmadığını denetle

Komutu kullanarak kapsayıcıda belirli bir dizinin mevcut olup olmadığını saptayın `az storage fs directory exists` .

Bu örnek, adlı bir dizinin kapsayıcıda mevcut olup olmadığını gösterir `my-directory` `my-file-system` . 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Bir dizinden indir

Komutunu kullanarak bir dizinden dosya indirin `az storage fs file download` .

Bu örnek, `upload.txt` adlı bir dizinden adlı bir dosyayı indirir `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Dizin içeriğini listeleme

Komutunu kullanarak bir dizinin içeriğini listeleyin `az storage fs file list` .

Bu örnek, adlı `my-directory` `my-file-system` bir depolama hesabının kapsayıcısında bulunan adlı bir dizinin içeriğini listeler `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Dizine dosya yükleme

Komutunu kullanarak bir dosyayı dizine yükleyin `az storage fs directory upload` .

Bu örnek adlı bir dizine adlı bir dosyayı yükler `upload.txt` `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Dosya özelliklerini göster

Bir dosyanın özelliklerini, komutunu kullanarak konsola yazdırabilirsiniz `az storage fs file show` .

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Bir dosyayı yeniden adlandırma veya taşıma

Komutunu kullanarak bir dosyayı yeniden adlandırın veya taşıyın `az storage fs file move` .

Bu örnek, adından adı olan bir dosyayı yeniden adlandırır `my-file.txt` `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Dosyayı silme

Komutunu kullanarak bir dosyayı silin `az storage fs file delete` .

Bu örnek, adlı dosyayı siler `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Ayrıca bkz.

- [Örnekler](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Görüş bildirin](https://github.com/Azure/azure-cli-extensions/issues)
- [Bilinen sorunlar](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure Data Lake Storage 2. ACL 'Leri yönetmek için Azure CLı kullanma](data-lake-storage-acl-cli.md)