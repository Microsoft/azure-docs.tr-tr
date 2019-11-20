---
title: Azure dosyalarını kullanarak depolamayı yapılandırma
description: App Service Windows kapsayıcısında Azure dosyalarını yapılandırma ve bu dosyalara bağlanma.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 2c12bf45c033fea185d976f1e9d644183407b5ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297220"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>App Service bir Windows kapsayıcısında Azure dosyalarını yapılandırma

> [!NOTE]
> Bu makale özel Windows kapsayıcıları için geçerlidir. _Linux_üzerinde App Service dağıtmak için bkz. [Azure Storage 'dan içerik](./containers/how-to-serve-content-from-azure-storage.md)sunma.
>

Bu kılavuzda, Windows kapsayıcılarında Azure depolama 'ya nasıl erişebileceğiniz gösterilmektedir. Yalnızca [Azure dosya paylaşımları](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) ve [Premium dosya paylaşımları](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) desteklenir. Azure dosya paylaşımlarını bu nasıl yapılır ile kullanıyorsunuz. Avantajlar güvenli içerik, içerik taşınabilirlik, birden çok uygulamaya erişim ve birden çok aktarım yöntemi içerir.

## <a name="prerequisites"></a>Önkoşullar

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 veya üzeri).
- [Azure App Service var olan bir Windows kapsayıcı uygulaması](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure dosya paylaşma oluşturma](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Azure dosya paylaşımında dosya yükleme](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure dosyaları varsayılan olmayan depolama ve Web uygulamasına dahil edilmeyen ayrı olarak faturalandırılır. Altyapı sınırlamaları nedeniyle güvenlik duvarı yapılandırması kullanımını desteklemez.
>

## <a name="link-storage-to-your-web-app-preview"></a>Depolama alanını Web uygulamanıza bağlama (Önizleme)

 Azure dosya paylaşımından App Service uygulamanızdaki bir dizine bağlamak için [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) komutunu kullanın. Depolama türü AzureFiles olmalıdır.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Bunu, bir Azure dosya paylaşımıyla bağlantılı olmasını istediğiniz diğer dizinler için yapmanız gerekir.

## <a name="verify"></a>Doğrulama

Bir Azure dosya paylaşımının bir Web uygulamasıyla bağlantısı kurulduktan sonra, aşağıdaki komutu çalıştırarak bunu doğrulayabilirsiniz:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Sonraki adımlar

- [Windows kapsayıcısı (Önizleme) kullanarak bir ASP.NET uygulamasını Azure App Service geçirme](app-service-web-tutorial-windows-containers-custom-fonts.md).
