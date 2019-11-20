---
title: PostgreSQL için Azure veritabanı için okuma çoğaltmalarını yönetme-Azure CLı 'dan tek sunuculu sunucu REST API
description: PostgreSQL için Azure veritabanı-Azure CLı ve REST API arasındaki okuma çoğaltmalarını yönetme hakkında bilgi edinin
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e146a0f42b6487545321ebfbc9ec523da5e78c8a
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995348"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Azure CLı 'dan okuma çoğaltmaları oluşturun ve yönetin REST API

Bu makalede, Azure CLı ve REST API kullanarak PostgreSQL için Azure veritabanı 'nda okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz. Okuma çoğaltmaları hakkında daha fazla bilgi edinmek için bkz. [genel bakış](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI
Azure CLı kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

### <a name="prerequisites"></a>Önkoşullar

- [Azure CLI 2.0’ı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [PostgreSQL Için Azure veritabanı sunucusunun](quickstart-create-server-up-azure-cli.md) ana sunucu olması.


### <a name="prepare-the-master-server"></a>Ana sunucuyu hazırlama
Bu adımların Genel Amaçlı veya bellek için Iyileştirilmiş katmanlarda bir ana sunucu hazırlamak için kullanılması gerekir.

Parametrenin ana sunucuda çoğaltma olarak ayarlanması gerekir. `azure.replication_support` Bu statik parametre değiştirildiğinde, değişikliğin etkili olması için sunucu yeniden başlatması gerekir.

1. Çoğaltma `azure.replication_support` olarak ayarlayın.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Değişikliği uygulamak için sunucuyu yeniden başlatın.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Salt okunur bir çoğaltma oluşturma

[Az Postgres Server Replication Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) komutu aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Açıklama  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Çoğaltma sunucusunun oluşturulacağı kaynak grubu.  |
| name | mydemoserver-çoğaltma | Oluşturulan yeni çoğaltma sunucusunun adı. |
| source-server | mydemoserver | Çoğaltılacak var olan ana sunucunun adı veya kaynak KIMLIĞI. |

Aşağıdaki CLı örneğinde, çoğaltma, ana öğe ile aynı bölgede oluşturulur.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Bir çapraz bölge okuma çoğaltması oluşturmak için `--location` parametresini kullanın. Aşağıdaki CLı örneği Batı ABD içinde çoğaltmayı oluşturur.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin. 

`azure.replication_support` Parametreyi genel amaçlı veya bellek için iyileştirilmiş ana sunucuda **çoğaltma** olarak ayarlamadıysanız ve sunucuyu yeniden başlattıktan sonra bir hata alırsınız. Bir çoğaltma oluşturmadan önce bu iki adımı uygulayın.

Bir çoğaltma, ana öğe ile aynı işlem ve depolama ayarları kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, birden fazla ayar ana sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.

> [!IMPORTANT]
> Ana sunucu ayarı yeni bir değere güncellenmesinden önce, çoğaltma ayarını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana üzerinde yapılan değişikliklerle devam etmesine yardımcı olur.

### <a name="list-replicas"></a>Çoğaltmaları Listele
[Az Postgres Server Replica List](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) komutunu kullanarak bir ana sunucunun çoğaltmalarının listesini görüntüleyebilirsiniz.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Bir çoğaltma sunucusu için çoğaltma durdurma
[Az Postgres Server Replication stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) komutunu kullanarak, bir ana sunucu ve okuma çoğaltması arasındaki çoğaltmayı durdurabilirsiniz.

Bir ana sunucu ve bir okuma çoğaltması için çoğaltmayı durdurduktan sonra geri alınamaz. Okuma çoğaltması, hem okuma hem de yazma işlemlerini destekleyen tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Ana veya çoğaltma sunucusunu silme
Ana veya çoğaltma sunucusunu silmek için [az Postgres Server DELETE](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) komutunu kullanın.

Bir ana sunucuyu sildiğinizde, tüm okuma çoğaltmalarına çoğaltma durdurulur. Okuma çoğaltmaları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/)kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

### <a name="prepare-the-master-server"></a>Ana sunucuyu hazırlama
Bu adımların Genel Amaçlı veya bellek için Iyileştirilmiş katmanlarda bir ana sunucu hazırlamak için kullanılması gerekir.

Parametrenin ana sunucuda çoğaltma olarak ayarlanması gerekir. `azure.replication_support` Bu statik parametre değiştirildiğinde, değişikliğin etkili olması için sunucu yeniden başlatması gerekir.

1. Çoğaltma `azure.replication_support` olarak ayarlayın.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. Değişikliği uygulamak için [sunucuyu yeniden başlatın](/rest/api/postgresql/servers/restart) .

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Salt okunur bir çoğaltma oluşturma
[Oluşturma API](/rest/api/postgresql/servers/create)'sini kullanarak bir okuma çoğaltması oluşturabilirsiniz:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin. 

`azure.replication_support` Parametreyi genel amaçlı veya bellek için iyileştirilmiş ana sunucuda **çoğaltma** olarak ayarlamadıysanız ve sunucuyu yeniden başlattıktan sonra bir hata alırsınız. Bir çoğaltma oluşturmadan önce bu iki adımı uygulayın.

Bir çoğaltma, ana öğe ile aynı işlem ve depolama ayarları kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, birden fazla ayar ana sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.


> [!IMPORTANT]
> Ana sunucu ayarı yeni bir değere güncellenmesinden önce, çoğaltma ayarını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana üzerinde yapılan değişikliklerle devam etmesine yardımcı olur.

### <a name="list-replicas"></a>Çoğaltmaları Listele
[Çoğaltma LISTESI API](/rest/api/postgresql/replicas/listbyserver)'sini kullanarak bir ana sunucunun çoğaltmalarının listesini görüntüleyebilirsiniz:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Bir çoğaltma sunucusu için çoğaltma durdurma
[GÜNCELLEŞTIRME API](/rest/api/postgresql/servers/update)'sini kullanarak bir ana sunucu ve okuma çoğaltması arasındaki çoğaltmayı durdurabilirsiniz.

Bir ana sunucu ve bir okuma çoğaltması için çoğaltmayı durdurduktan sonra geri alınamaz. Okuma çoğaltması, hem okuma hem de yazma işlemlerini destekleyen tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Ana veya çoğaltma sunucusunu silme
Ana veya çoğaltma sunucusunu silmek için, [SILME API](/rest/api/postgresql/servers/delete)'sini kullanın:

Bir ana sunucuyu sildiğinizde, tüm okuma çoğaltmalarına çoğaltma durdurulur. Okuma çoğaltmaları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Sonraki adımlar
* [PostgreSQL Için Azure veritabanı 'nda okuma çoğaltmaları](concepts-read-replicas.md)hakkında daha fazla bilgi edinin.
* [Azure Portal okuma çoğaltmaları oluşturmayı ve yönetmeyi](howto-read-replicas-portal.md)öğrenin.