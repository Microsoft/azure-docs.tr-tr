---
title: Depolamayı otomatik Büyüt-Azure CLı-MariaDB için Azure veritabanı
description: Bu makalede, MariaDB için Azure veritabanı 'nda Azure CLı kullanarak otomatik büyüme depolamayı nasıl etkinleştirebileceğinizi açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f2ae7a890fc1dab29b6cc99e4cc88087dbc6e052
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366190"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Azure CLı kullanarak MariaDB depolama için Azure veritabanı 'nı otomatik büyütme
Bu makalede, bir MariaDB sunucu depolaması için Azure veritabanı 'nı, iş yükünü etkilemeden büyümeden nasıl yapılandırabileceğiniz açıklanmaktadır.

[Depolama sınırına ulaşan](concepts-pricing-tiers.md#reaching-the-storage-limit)sunucu, salt okunurdur olarak ayarlanmıştır. Depolama otomatik büyüme, 100 GB 'tan az kullanılabilir depolama alanı olan sunucular için etkinleştirildiyse, boş depolama alanı sağlanan depolamanın en fazla 1 GB veya %10 ' u altındaysa, sağlanan depolama boyutu 5 GB artar. 100 GB 'tan fazla kullanılabilir depolama alanı olan sunucularda, boş depolama alanı sağlanan depolama boyutunun 10 GB 'ı altındaysa, sağlanan depolama boyutu %5 oranında artar. [Burada](concepts-pricing-tiers.md#storage) belirtilen en fazla depolama sınırı geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzu gerçekleştirmek için:

- [MariaDB sunucusu Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-cli.md)gerekir.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="enable-mariadb-server-storage-auto-grow"></a>MariaDB Server Storage otomatik büyümesini etkinleştir

Sunucu, var olan bir sunucuda depolamayı otomatik olarak büyüyerek aşağıdaki komutla etkinleştirin:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aşağıdaki komutla yeni bir sunucu oluştururken sunucu otomatik büyüyerek depolamayı etkinleştirin:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Sonraki adımlar

[Ölçümler üzerinde uyarılar oluşturma](howto-alert-metric.md)hakkında bilgi edinin.
