---
title: Bölge yedekli yüksek kullanılabilirliği yönetme-Azure CLı-MySQL için Azure veritabanı esnek sunucu
description: Bu makalede, Azure CLı ile MySQL esnek sunucusu için Azure veritabanı 'nda bölge yedekli yüksek kullanılabilirliğinin nasıl yapılandırılacağı açıklanır.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509171"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Azure CLı ile MySQL için Azure veritabanı esnek sunucusu 'nda bölge yedekli yüksek kullanılabilirliği yönetme

> [!NOTE]
> MySQL için Azure veritabanı esnek sunucusu genel önizlemede. 

Makalede, esnek sunucunuzda sunucu oluşturma sırasında bölge yedekli yüksek kullanılabilirlik yapılandırmasını etkinleştirebilir veya devre dışı bırakabileceğinizi açıklar. Sunucu oluşturulduktan sonra bölge yedekli yüksek kullanılabilirliğini devre dışı bırakabilirsiniz. Sunucu oluşturulduktan sonra bölge yedekli yüksek kullanılabilirliği etkinleştirme desteklenmez.

Yüksek kullanılabilirlik özelliği, farklı bölgelerde fiziksel ve bekleme çoğaltmasını fiziksel olarak ayırır. Daha fazla bilgi için bkz. [yüksek kullanılabilirlik kavramları belgeleri](./concepts/../concepts-high-availability.md). Yüksek kullanılabilirliğin etkinleştirilmesi veya devre dışı bırakılması, VNET yapılandırması, güvenlik duvarı ayarları ve yedekleme saklama gibi diğer ayarlarınızı değiştirmez. Yüksek kullanılabilirliği devre dışı bırakmak, uygulamanızın bağlantı ve işlemlerini etkilemez.

> [!IMPORTANT]
> Bölgesel olarak yedekli yüksek kullanılabilirlik, sınırlı sayıda bölgede kullanılabilir. Lütfen [burada](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions)desteklenen bölgeleri gözden geçirin. 

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

## <a name="enable-high-availability-during-server-creation"></a>Sunucu oluşturma sırasında yüksek kullanılabilirliği etkinleştir
Yalnızca yüksek kullanılabilirliğe sahip genel amaçlı veya bellek için iyileştirilmiş fiyatlandırma katmanlarını kullanarak sunucu oluşturabilirsiniz. Yalnızca oluşturma zamanı sırasında bir sunucu için yüksek kullanılabilirlik sağlayabilirsiniz.

**Kullanımıyla**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Örnek:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Yüksek kullanılabilirliği devre dışı bırak

[Az MySQL esnek-sunucu Update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) komutunu kullanarak yüksek kullanılabilirliği devre dışı bırakabilirsiniz. Yüksek kullanılabilirliği devre dışı bırakmanın yalnızca sunucu yüksek kullanılabilirlik ile oluşturulduysa desteklenir. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Örnek:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Sonraki adımlar

-   [İş sürekliliği](./concepts-business-continuity.md) hakkında bilgi edinin
-   [Bölge yedekli yüksek kullanılabilirlik](./concepts-high-availability.md) hakkında bilgi edinin
