---
title: Linux 'ta Azure depolama 'dan içerik sunma-App Service
description: Linux üzerinde Azure App Service Azure Storage 'dan içerik yapılandırma ve hizmeti sunma.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 97c03ad294bba1f8a0285fff4595991ca0acc8b5
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018270"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Linux üzerinde App Service Azure Storage 'tan içerik sunma

Bu kılavuzda, [Azure Storage](/azure/storage/common/storage-introduction)kullanılarak Linux üzerinde App Service statik içerik sunma gösterilmektedir. Avantajlar, güvenli içerik, içerik taşınabilirlik, kalıcı depolama, birden çok uygulamaya erişim ve birden fazla aktarım yöntemi içerir.

## <a name="prerequisites"></a>Önkoşullar

- Mevcut bir Web uygulaması (Linux veya Kapsayıcılar için Web App üzerinde App Service).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 veya üzeri).

## <a name="create-azure-storage"></a>Azure depolama oluşturma

> [!NOTE]
> Azure depolama, varsayılan olmayan depolama ve Web uygulamasına dahil edilmeyen ayrı olarak faturalandırılır.
>
> Kendi depolama alanınızı getir, altyapı sınırlamaları nedeniyle depolama güvenlik duvarı yapılandırmasını kullanmayı desteklemez.
>

Azure [Azure depolama hesabı](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)oluşturun.

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Azure depolama 'ya dosya yükleme

Yerel bir dizini depolama hesabına yüklemek için aşağıdaki örnekte olduğu gibi [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) komutunu kullanın:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Depolama alanını Web uygulamanıza bağlama (Önizleme)

> [!CAUTION]
> Bir Web uygulamasında var olan bir dizinin bir depolama hesabına bağlanması, dizin içeriğini silecektir. Mevcut bir uygulama için dosyaları geçiriyorsanız, başlamadan önce uygulamanızın ve içeriğinin yedeğini alın.
>

Bir depolama hesabını App Service uygulamanızdaki bir dizine bağlamak için [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) komutunu kullanın. Depolama türü AzureBlob veya AzureFiles olabilir. Bu kapsayıcı için AzureBlob kullanırsınız.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Bunu, bir depolama hesabına bağlanmasını istediğiniz diğer dizinler için yapmanız gerekir.

## <a name="verify"></a>Doğrula

Bir depolama kapsayıcısı bir Web uygulamasına bağlandıktan sonra, aşağıdaki komutu çalıştırarak bunu doğrulayabilirsiniz:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Docker Compose özel depolama kullan

Azure depolama, özel kimlik kullanarak çok Kapsayıcılı uygulamalarla bağlanabilir. Özel kimlik adını görüntülemek için çalıştırın [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

*Docker-Compose. yıml* dosyanızda, `volumes` seçeneğini ile `custom-id`eşleyin. Örneğin:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure App Service Web uygulamalarını yapılandırın](../configure-common.md).
