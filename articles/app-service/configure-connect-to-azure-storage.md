---
title: Azure depolama (kapsayıcı) ekleme
description: Azure App Service ' de kapsayıcılı bir uygulamada özel ağ paylaşımının nasıl iliştirileyeceğinizi öğrenin. Uygulamalar arasında dosya paylaşma, statik içeriği uzaktan yönetme ve yerel olarak erişme, vb.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: f844b5b413cda2cb16f9701970857be65fe6d91d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777626"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>App Service'teki bir kapsayıcıdan ağ paylaşımı olarak Azure Depolama'ya (önizleme) erişme

::: zone pivot="container-windows"

Bu kılavuzda, Azure depolama dosyalarının App Service bir Windows kapsayıcısına ağ paylaşımının nasıl ekleneceği gösterilmektedir. Yalnızca [Azure dosya paylaşımları](../storage/files/storage-how-to-use-files-cli.md) ve [Premium dosya paylaşımları](../storage/files/storage-how-to-create-file-share.md) desteklenir. Avantajlar güvenli içerik, içerik taşınabilirlik, birden çok uygulamaya erişim ve birden çok aktarım yöntemi içerir.

> [!NOTE]
>App Service 'de Azure depolama, **Önizleme** aşamasındadır ve **Üretim senaryolarında** **desteklenmez** .

::: zone-end

::: zone pivot="container-linux"

Bu kılavuzda, Azure depolama 'nın bir Linux kapsayıcısına App Service nasıl ekleneceği gösterilmektedir. Avantajlar, güvenli içerik, içerik taşınabilirlik, kalıcı depolama, birden çok uygulamaya erişim ve birden fazla aktarım yöntemi içerir.

> [!NOTE]
>App Service 'de Azure depolama, Linux ve Kapsayıcılar için Web App App Service için **Önizleme** aşamasındadır. **Üretim senaryolarında** bu **desteklenmez** .

::: zone-end

## <a name="prerequisites"></a>Önkoşullar

::: zone pivot="container-windows"

- [Azure App Service var olan bir Windows kapsayıcı uygulaması](quickstart-custom-container.md)
- [Azure dosya paylaşma oluşturma](../storage/files/storage-how-to-use-files-cli.md)
- [Azure dosya paylaşımında dosya yükleme](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- [Linux uygulamasında](index.yml)mevcut bir App Service.
- Bir [Azure depolama hesabı](../storage/common/storage-account-create.md?tabs=azure-cli)
- Bir [Azure dosya paylaşma ve dizini](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Azure dosyaları varsayılan olmayan depolama ve Web uygulamasına dahil edilmeyen ayrı olarak faturalandırılır. Altyapı sınırlamaları nedeniyle güvenlik duvarı yapılandırması kullanımını desteklemez.
>

## <a name="limitations"></a>Sınırlamalar

::: zone pivot="container-windows"

- App Service 'de Azure depolama Şu anda kendi kod senaryolarınızı getir (kapsayıcısız Windows uygulamaları) için **desteklenmiyor** .
- App Service ' deki Azure depolama, altyapı sınırlamaları nedeniyle **depolama güvenlik duvarı** yapılandırmasını kullanmayı **desteklemez** .
- App Service ile Azure depolama, uygulama başına **en fazla beş** bağlama noktası belirtmenizi sağlar.
- Bir uygulamaya bağlı Azure Storage App Service FTP/FTPs uç noktaları aracılığıyla erişilebilir değildir. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)kullanın.

::: zone-end

::: zone pivot="container-linux"

- App Service Azure Storage, **Azure dosya kapsayıcıları** (okuma/yazma) ve **Azure Blob kapsayıcıları** (salt okuma) bağlamayı destekler
- App Service Azure depolama, uygulama başına **en fazla beş** bağlama noktası belirtmenizi sağlar.
- Bir uygulamaya bağlı Azure Storage App Service FTP/FTPs uç noktaları aracılığıyla erişilebilir değildir. [Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)kullanın.

::: zone-end

## <a name="link-storage-to-your-app"></a>Depolama alanını uygulamanıza bağlayın

::: zone pivot="container-windows"

[Azure depolama hesabınızı, dosya paylaşımınızı ve dizininizi](#prerequisites)oluşturduktan sonra uygulamanızı Azure Storage ile yapılandırabilirsiniz.

Azure dosya paylaşımından App Service uygulamanızdaki bir dizine bağlamak için [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) komutunu kullanın. Depolama türü AzureFiles olmalıdır.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Bunu, bir Azure dosya paylaşımıyla bağlantılı olmasını istediğiniz diğer dizinler için yapmanız gerekir.

::: zone-end

::: zone pivot="container-linux"

[Azure depolama hesabınızı, dosya paylaşımınızı ve dizininizi](#prerequisites)oluşturduktan sonra uygulamanızı Azure Storage ile yapılandırabilirsiniz.

Bir depolama hesabını App Service uygulamanızdaki bir dizine bağlamak için [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) komutunu kullanın. Depolama türü AzureBlob veya AzureFiles olabilir. Bu örnekte AzureFiles kullanılır. Bağlama yolu ayarı, Azure depolama 'ya bağlamak istediğiniz kapsayıcının içindeki klasöre karşılık gelir. '/' Olarak ayarlanması kapsayıcının tamamını Azure depolama 'ya bağlar.


> [!CAUTION]
> Web uygulamanızda bağlama yolu olarak belirtilen dizin boş olmalıdır. Dış bağlama eklendiğinde, bu dizinde depolanan içerikler silinir. Mevcut bir uygulama için dosyaları geçiriyorsanız, başlamadan önce uygulamanızın ve içeriğinin yedeğini alın.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Bunu, bir depolama hesabına bağlanmasını istediğiniz diğer dizinler için yapmanız gerekir.

::: zone-end

## <a name="verify-linked-storage"></a>Bağlı depolamayı doğrulama

Paylaşımın uygulamayla bağlantısı kurulduktan sonra, aşağıdaki komutu çalıştırarak bunu doğrulayabilirsiniz:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Sonraki adımlar

::: zone pivot="container-windows"

- Özel [bir kapsayıcı kullanarak Azure App Service özel yazılım geçirme](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Özel bir kapsayıcı yapılandırın](configure-custom-container.md?pivots=platform-linux).

::: zone-end