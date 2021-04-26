---
title: Okuma çoğaltmalarını yönetme-Azure CLı, REST API-PostgreSQL için Azure veritabanı-tek sunucu
description: PostgreSQL için Azure veritabanı-Azure CLı ve REST API arasındaki okuma çoğaltmalarını yönetme hakkında bilgi edinin
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d13db238674cae62f528c3d730bf892a72b8f6c2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764702"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Azure CLı 'dan okuma çoğaltmaları oluşturun ve yönetin REST API

Bu makalede, Azure CLı ve REST API kullanarak PostgreSQL için Azure veritabanı 'nda okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz. Okuma çoğaltmaları hakkında daha fazla bilgi edinmek için bkz. [genel bakış](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Azure çoğaltma desteği
[Okuma çoğaltmaları](concepts-read-replicas.md) ve [mantıksal kod çözme](concepts-logical.md) , bilgi Için doğrudan Postgres yazma günlüğüne (Wal) bağlıdır. Bu iki özellik, Postgres 'den farklı günlük düzeylerine sahip olmalıdır. Mantıksal kod çözme, okuma Çoğaltmalarından daha yüksek bir günlüğe kaydetme düzeyine sahip olmalıdır.

Doğru günlük kaydını yapılandırmak için Azure çoğaltma desteği parametresini kullanın. Azure çoğaltma desteğinin üç ayar seçeneği vardır:

* **Kapalı** -en az bilgiyi Wal 'e yerleştirir. Bu ayar, çoğu PostgreSQL için Azure veritabanı sunucuları üzerinde kullanılamaz.  
* **Çoğaltma** -daha ayrıntılı bir **şekilde.** Bu, [okuma çoğaltmalarının](concepts-read-replicas.md) çalışması için gereken en düşük günlüğe kaydetme düzeyidir. Bu ayar, çoğu sunucuda varsayılandır.
* **Çoğaltmadan** daha ayrıntılı **mantıksal** . Bu, mantıksal kod çözmenin çalışması için en düşük günlük kayıt düzeyidir. Okuma çoğaltmaları bu ayarda de çalışır.


> [!NOTE]
> Kalıcı ağır yazma yoğunluklu birincil iş yükleri için okuma çoğaltmaları dağıtıldığında, çoğaltma gecikmesi büyümeye devam edebilir ve hiçbir zaman birincil ile yakalayamayabilir. Bu durum, çoğaltma sırasında alınana kadar WAL dosyaları silinmediği için birincil sırada depolama kullanımını da artırabilir.

## <a name="azure-cli"></a>Azure CLI
Azure CLı kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

### <a name="prerequisites"></a>Önkoşullar

- [Azure CLI 2.0’ı yükleme](/cli/azure/install-azure-cli)
- [PostgreSQL Için Azure veritabanı sunucusunun](quickstart-create-server-up-azure-cli.md) birincil sunucu olması.


### <a name="prepare-the-primary-server"></a>Birincil sunucuyu hazırlama

1. Birincil sunucunun `azure.replication_support` değerini denetleyin. Okuma çoğaltmalarının çalışması için en az çoğaltma olmalıdır.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. `azure.replication_support`En az çoğaltma değilse, ayarlayın. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Değişikliği uygulamak için sunucuyu yeniden başlatın.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

[Az Postgres Server Replication Create](/cli/azure/postgres/server/replica#az_postgres_server_replica_create) komutu aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Çoğaltma sunucusunun oluşturulacağı kaynak grubu.  |
| name | mydemoserver-çoğaltma | Oluşturulan yeni çoğaltma sunucusunun adı. |
| source-server | mydemoserver | Çoğaltılacak mevcut birincil sunucunun adı veya kaynak KIMLIĞI. Çoğaltmanın ve ana sunucunun kaynak gruplarının farklı olmasını istiyorsanız kaynak KIMLIĞI ' ni kullanın. |

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

`azure.replication_support`Parametreyi genel amaçlı veya bellek Için iyileştirilmiş birincil sunucuda **çoğaltma** olarak ayarlamadıysanız ve sunucuyu yeniden başlattıktan sonra bir hata alırsınız. Bir çoğaltma oluşturmadan önce bu iki adımı uygulayın.

> [!IMPORTANT]
> [Okuma çoğaltmasına genel bakış konusunun Konular bölümünü](concepts-read-replicas.md#considerations)gözden geçirin.
>
> Birincil sunucu ayarı yeni bir değere güncellenmesinden önce, çoğaltma ayarını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana üzerinde yapılan değişikliklerle devam etmesine yardımcı olur.

### <a name="list-replicas"></a>Çoğaltmaları Listele
[Az Postgres Server Replication List](/cli/azure/postgres/server/replica#az_postgres_server_replica_list) komutunu kullanarak bir birincil sunucunun çoğaltmalarının listesini görüntüleyebilirsiniz.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Çoğaltma sunucusuna çoğaltmayı durdur
[Az Postgres Server Replication stop](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) komutunu kullanarak birincil sunucu ile okuma çoğaltması arasında çoğaltmayı durdurabilirsiniz.

Birincil sunucuya çoğaltmayı ve bir okuma çoğaltmasını durdurduktan sonra geri alınamaz. Okuma çoğaltması, hem okuma hem de yazma işlemlerini destekleyen tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Birincil veya çoğaltma sunucusunu silme
Birincil veya çoğaltma sunucusunu silmek için [az Postgres Server DELETE](/cli/azure/postgres/server#az_postgres_server_delete) komutunu kullanın.

Birincil sunucuyu sildiğinizde, tüm okuma çoğaltmalarına çoğaltma durdurulur. Okuma çoğaltmaları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/)kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

### <a name="prepare-the-primary-server"></a>Birincil sunucuyu hazırlama

1. Birincil sunucunun `azure.replication_support` değerini denetleyin. Okuma çoğaltmalarının çalışması için en az çoğaltma olmalıdır.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. `azure.replication_support`En az çoğaltma değilse, ayarlayın.

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

### <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma
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

`azure.replication_support`Parametreyi genel amaçlı veya bellek Için iyileştirilmiş birincil sunucuda **çoğaltma** olarak ayarlamadıysanız ve sunucuyu yeniden başlattıktan sonra bir hata alırsınız. Bir çoğaltma oluşturmadan önce bu iki adımı uygulayın.

Bir çoğaltma, ana öğe ile aynı işlem ve depolama ayarları kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, bazı ayarlar birincil sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.


> [!IMPORTANT]
> Birincil sunucu ayarı yeni bir değere güncellenmesinden önce, çoğaltma ayarını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana üzerinde yapılan değişikliklerle devam etmesine yardımcı olur.

### <a name="list-replicas"></a>Çoğaltmaları Listele
[Çoğaltma LISTESI API](/rest/api/postgresql/replicas/listbyserver)'sini kullanarak bir birincil sunucunun çoğaltmalarının listesini görüntüleyebilirsiniz:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Çoğaltma sunucusuna çoğaltmayı durdur
[GÜNCELLEŞTIRME API](/rest/api/postgresql/servers/update)'sini kullanarak birincil sunucu ile okuma çoğaltması arasında çoğaltmayı durdurabilirsiniz.

Birincil sunucuya çoğaltmayı ve bir okuma çoğaltmasını durdurduktan sonra geri alınamaz. Okuma çoğaltması, hem okuma hem de yazma işlemlerini destekleyen tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Birincil veya çoğaltma sunucusunu silme
Birincil veya çoğaltma sunucusunu silmek için, [SILME API](/rest/api/postgresql/servers/delete)'sini kullanın:

Birincil sunucuyu sildiğinizde, tüm okuma çoğaltmalarına çoğaltma durdurulur. Okuma çoğaltmaları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Sonraki adımlar
* [PostgreSQL Için Azure veritabanı 'nda okuma çoğaltmaları](concepts-read-replicas.md)hakkında daha fazla bilgi edinin.
* [Azure Portal okuma çoğaltmaları oluşturmayı ve yönetmeyi](howto-read-replicas-portal.md)öğrenin.