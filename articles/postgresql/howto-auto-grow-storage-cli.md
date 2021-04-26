---
title: Depolamayı otomatik büyütme-Azure CLı-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, PostgreSQL için Azure veritabanı-tek sunucu 'da Azure CLı kullanarak depolama otomatik büyümenin nasıl yapılandırılacağı açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d16fe5ef6654ee29c3e345ff0532ed91206d86d3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366180"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Azure CLı kullanarak PostgreSQL için Azure veritabanı depolama-tek sunucu için otomatik büyüme
Bu makalede, bir PostgreSQL için Azure veritabanı sunucu depolaması, iş yükünü etkilemeden nasıl büyütüleceği açıklanır.

[Depolama sınırına ulaşan](./concepts-pricing-tiers.md#reaching-the-storage-limit)sunucu, salt okunurdur olarak ayarlanmıştır. Depolama otomatik büyüme, 100 GB 'tan az kullanılabilir depolama alanı olan sunucular için etkinleştirildiyse, boş depolama alanı sağlanan depolamanın en fazla 1 GB veya %10 ' u altındaysa, sağlanan depolama boyutu 5 GB artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun 10 GB 'ı altındaysa, sağlanan depolama boyutu %5 oranında artar. [Burada](./concepts-pricing-tiers.md#storage) belirtilen en fazla depolama sınırı geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

- [PostgreSQL Için Azure veritabanı sunucusuna](quickstart-create-server-database-azure-cli.md)ihtiyacınız vardır.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="enable-postgresql-server-storage-auto-grow"></a>PostgreSQL Server depolama alanını otomatik büyümeye etkinleştir

Sunucu, var olan bir sunucuda depolamayı otomatik olarak büyüyerek aşağıdaki komutla etkinleştirin:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aşağıdaki komutla yeni bir sunucu oluştururken sunucu otomatik büyüyerek depolamayı etkinleştirin:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-on-metric.md)hakkında bilgi edinin.
