---
title: Parametreleri yapılandırma-PostgreSQL için Azure veritabanı-tek sunucu
description: Bu makalede, Azure CLı kullanarak PostgreSQL için Azure veritabanı-tek sunucu 'da Postgres parametrelerinin nasıl yapılandırılacağı açıklanır.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: cadf21423ac7eb997db5bb42005ee307d9813331
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604201"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Azure CLı kullanarak PostgreSQL için Azure veritabanı için sunucu yapılandırma parametrelerini özelleştirme-tek sunucu
Komut satırı arabirimi (Azure CLı) kullanarak bir Azure PostgreSQL sunucusu için yapılandırma parametrelerini listeleyebilir, gösterebilir ve güncelleştirebilirsiniz. Altyapı yapılandırmalarının bir alt kümesi sunucu düzeyinde sunulur ve değiştirilebilir. 

## <a name="prerequisites"></a>Önkoşullar
Bu nasıl yapılır kılavuzunda ilerlemek için şunlar gerekir:
- PostgreSQL için [Azure veritabanı oluşturma](quickstart-create-server-database-azure-cli.md) sunucusuna ve veritabanı için Azure veritabanı oluşturma
- Makinenizde [Azure CLI](/cli/azure/install-azure-cli) komut satırı arabirimi ' ni yükleyip tarayıcınızı kullanarak Azure Portal [Azure Cloud Shell](../cloud-shell/overview.md) kullanın.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>PostgreSQL için Azure veritabanı sunucusu için sunucu yapılandırma parametrelerini listeleyin
Bir sunucudaki tüm değiştirilebilir parametreleri ve değerlerini listelemek için [az Postgres Server Configuration List](/cli/azure/postgres/server/configuration) komutunu çalıştırın.

Sunucu **mydemoserver.Postgres.Database.Azure.com** için sunucu yapılandırma parametrelerini, **myresourcegroup** kaynak grubu altında listeleyebilirsiniz.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Sunucu yapılandırma parametresi ayrıntılarını göster
Bir sunucu için belirli bir yapılandırma parametresi ayrıntılarını göstermek üzere [az Postgres Server Configuration Show](/cli/azure/postgres/server/configuration)  komutunu çalıştırın.

Bu örnekte, **myresourcegroup** kaynak grubu altında sunucu **mydemoserver.Postgres.Database.Azure.com** için **günlük \_ Min \_ iletileri** sunucu yapılandırma parametresinin ayrıntıları gösterilmektedir.
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Sunucu yapılandırma parametre değerini değiştir
Ayrıca, PostgreSQL sunucu altyapısının temel yapılandırma değerini güncelleştiren belirli bir sunucu yapılandırma parametresinin değerini de değiştirebilirsiniz. Yapılandırmayı güncelleştirmek için [az Postgres Server Configuration set](/cli/azure/postgres/server/configuration) komutunu kullanın. 

Mydemoserver.postgres.database.azure.com kaynak grubu altında Server  'ın **günlük \_ Min \_ iletileri** sunucu yapılandırma parametresini güncelleştirmek için **.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Bir yapılandırma parametresinin değerini sıfırlamak istiyorsanız, isteğe bağlı parametreyi bırakmayı seçmeniz yeterlidir `--value` ve hizmet varsayılan değeri uygular. Yukarıdaki örnekte, şöyle görünür:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Bu komut **günlük \_ En düşük \_ ileti** yapılandırmasını varsayılan değer **uyarısı** olarak sıfırlar. Sunucu yapılandırması ve izin verilen değerler hakkında daha fazla bilgi için bkz. [sunucu yapılandırmasındaki](https://www.postgresql.org/docs/9.6/static/runtime-config.html)PostgreSQL belgeleri.

## <a name="next-steps"></a>Sonraki adımlar
- [Bir sunucuyu yeniden başlatmayı öğrenin](howto-restart-server-cli.md)
- Sunucu günlüklerini yapılandırmak ve erişmek için bkz. [PostgreSQL Için Azure veritabanı 'Nda sunucu günlükleri](concepts-server-logs.md)
