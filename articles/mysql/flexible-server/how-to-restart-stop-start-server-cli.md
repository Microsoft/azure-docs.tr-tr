---
title: Yeniden Başlat/Durdur/Başlat-Azure portal-MySQL için Azure veritabanı esnek sunucu
description: Bu makalede, Azure CLı aracılığıyla MySQL için Azure veritabanı 'nda işlemleri yeniden başlatma/durdurma/başlatma işlemlerinin nasıl yapılacağı açıklanır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509161"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>MySQL için Azure veritabanını yeniden başlatma/durdurma/başlatma-esnek sunucu (Önizleme)

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu makalede, Azure CLı kullanarak nasıl yeniden başlatma, esnek sunucu başlatma ve durdurma işlemleri gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
- Azure CLı 'yı en son sürüme yükler veya yükseltin. Bkz. [Azure CLI 'Yi yüklemeyi](/cli/azure/install-azure-cli).
-  [Az Login](/cli/azure/reference-index#az-login) komutunu kullanarak Azure hesabında oturum açın. Azure hesabınızın **ABONELIK kimliğini** ifade eden **ID** özelliğine göz önüne alın.

    ```azurecli-interactive
    az login
    ````

- Birden çok aboneliğiniz varsa, komutunu kullanarak sunucuyu oluşturmak istediğiniz uygun aboneliği seçin ```az account set``` .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Komutu kullanarak henüz bir tane oluşturmadıysanız, MySQL esnek sunucusu oluşturun ```az mysql flexible-server create``` .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>Çalışan bir sunucuyu durdur
Bir sunucuyu durdurmak için komutunu çalıştırın  ```az mysql flexible-server stop``` . [Yerel bağlam](/cli/azure/config/param-persist)kullanıyorsanız, herhangi bir bağımsız değişken sağlamanız gerekmez.

**Kullanımıyla**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Yerel bağlam olmadan örnek:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Yerel bağlamla örnek:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Durdurulmuş bir sunucu başlatma
Bir sunucu başlatmak için komutunu çalıştırın  ```az mysql flexible-server start``` . [Yerel bağlam](/cli/azure/config/param-persist)kullanıyorsanız, herhangi bir bağımsız değişken sağlamanız gerekmez.

**Kullanımıyla**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Yerel bağlam olmadan örnek:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Yerel bağlamla örnek:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> Sunucu başarıyla yeniden başlatıldıktan sonra, tüm yönetim işlemleri artık esnek sunucu için kullanılabilir.

## <a name="restart-a-server"></a>Sunucuyu yeniden başlatma
Bir sunucuyu yeniden başlatmak için  ```az mysql flexible-server restart``` komutunu çalıştırın. [Yerel bağlam](/cli/azure/config/param-persist)kullanıyorsanız, herhangi bir bağımsız değişken sağlamanız gerekmez.

**Kullanımıyla**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Yerel bağlam olmadan örnek:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Yerel bağlamla örnek:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> Sunucu başarıyla yeniden başlatıldıktan sonra, tüm yönetim işlemleri artık esnek sunucu için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar
- [MySQL Için Azure veritabanı esnek sunucusu 'nda ağ](./concepts-networking.md) hakkında daha fazla bilgi edinin
- [Azure Portal kullanarak MySQL Için Azure veritabanı esnek sunucu sanal ağını oluşturun ve yönetin](./how-to-manage-virtual-network-portal.md).

