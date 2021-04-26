---
title: Okuma çoğaltmalarını yönetme-Azure CLı, REST API-MySQL için Azure veritabanı
description: Azure CLı veya REST API kullanarak MySQL için Azure veritabanı 'nda okuma çoğaltmaları ayarlamayı ve yönetmeyi öğrenin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1a5bc9638e2e6eeff8f2176247f579b64beede90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94540221"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>Azure CLı ve REST API kullanarak MySQL için Azure veritabanı 'nda okuma çoğaltmaları oluşturma ve yönetme

Bu makalede, Azure CLı ve REST API kullanarak MySQL için Azure veritabanı hizmetinde okuma çoğaltmaları oluşturmayı ve yönetmeyi öğreneceksiniz. Okuma çoğaltmaları hakkında daha fazla bilgi edinmek için bkz. [genel bakış](concepts-read-replicas.md).

## <a name="azure-cli"></a>Azure CLI’si
Azure CLı kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

### <a name="prerequisites"></a>Önkoşullar

- [Azure CLI 2.0’ı yükleme](/cli/azure/install-azure-cli)
- Kaynak sunucu olarak kullanılacak [MySQL Için Azure veritabanı sunucusu](quickstart-create-mysql-server-database-using-azure-portal.md) . 

> [!IMPORTANT]
> Çoğaltma oku özelliği yalnızca Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanlarında MySQL için Azure veritabanı sunucuları için kullanılabilir. Kaynak sunucunun bu fiyatlandırma katmanlarından birinde olduğundan emin olun.

### <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma

> [!IMPORTANT]
> Mevcut çoğaltmaları olmayan bir kaynak için çoğaltma oluşturduğunuzda, kaynak ilk olarak çoğaltma için hazırlamak üzere yeniden başlatılır. Bunu dikkate alın ve yoğun bir süre boyunca bu işlemleri gerçekleştirin.

Aşağıdaki komut kullanılarak bir okuma çoğaltması sunucusu oluşturulabilir:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Çoğaltma sunucusunun oluşturulacağı kaynak grubu.  |
| name | mydemoreplicaserver | Oluşturulan yeni çoğaltma sunucusunun adı. |
| source-server | mydemoserver | Çoğaltma yapılacak mevcut kaynak sunucunun adı veya KIMLIĞI. |

Bir çapraz bölge okuma çoğaltması oluşturmak için `--location` parametresini kullanın. Aşağıdaki CLı örneği Batı ABD içinde çoğaltmayı oluşturur.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin. 

> [!NOTE]
> Okuma çoğaltmaları, ana sunucuyla aynı sunucu yapılandırmasıyla oluşturulur. Çoğaltma sunucusu yapılandırması oluşturulduktan sonra değiştirilebilir. Çoğaltmanın ana öğe ile devam edebileceğinden emin olmak için çoğaltma sunucusunun yapılandırmasının kaynaktan eşit veya daha büyük bir değer tutulması önerilir.


### <a name="list-replicas-for-a-source-server"></a>Kaynak sunucu için çoğaltmaları listeleme

Belirli bir kaynak sunucu için tüm çoğaltmaları görüntülemek için aşağıdaki komutu çalıştırın: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Çoğaltma sunucusunun oluşturulacağı kaynak grubu.  |
| server-name | mydemoserver | Kaynak sunucunun adı veya KIMLIĞI. |

### <a name="stop-replication-to-a-replica-server"></a>Çoğaltma sunucusuna çoğaltmayı durdur

> [!IMPORTANT]
> Bir sunucuya çoğaltma durdurulduğunda geri alınamaz. Bir kaynak ve çoğaltma arasında çoğaltma durdurulduktan sonra geri alınamaz. Çoğaltma sunucusu daha sonra tek başına bir sunucu olur ve artık hem okuma hem de yazma işlemlerini destekler. Bu sunucu tekrar bir çoğaltmaya yapılamaz.

Bir okuma çoğaltması sunucusuna çoğaltma, aşağıdaki komut kullanılarak durdurulabilir:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Örnek değer | Description  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Çoğaltma sunucusunun bulunduğu kaynak grubu.  |
| name | mydemoreplicaserver | Çoğaltmayı durdurulacak çoğaltma sunucusunun adı. |

### <a name="delete-a-replica-server"></a>Çoğaltma sunucusunu silme

**[Az MySQL Server DELETE](/cli/azure/mysql/server)** komutu çalıştırılarak bir okuma çoğaltması sunucusunu silme işlemi gerçekleştirilebilir.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Kaynak sunucuyu silme

> [!IMPORTANT]
> Bir kaynak sunucu durdurulduğunda, tüm çoğaltma sunucularına çoğaltma durdurulur ve kaynak sunucu silinir. Çoğaltma sunucuları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

Bir kaynak sunucuyu silmek için **[az MySQL Server DELETE](/cli/azure/mysql/server)** komutunu çalıştırabilirsiniz.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
[Azure REST API](/rest/api/azure/)kullanarak okuma çoğaltmaları oluşturabilir ve yönetebilirsiniz.

### <a name="create-a-read-replica"></a>Okuma amaçlı çoğaltma oluşturma
[Oluşturma API](/rest/api/mysql/servers/create)'sini kullanarak bir okuma çoğaltması oluşturabilirsiniz:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> İçinde bir çoğaltma oluşturabileceğiniz bölgeler hakkında daha fazla bilgi edinmek için [çoğaltma kavramlarını oku makalesini](concepts-read-replicas.md)ziyaret edin. 

`azure.replication_support`Parametreyi genel amaçlı veya bellek Için iyileştirilmiş kaynak sunucuda **çoğaltma** olarak ayarlamadıysanız ve sunucuyu yeniden başlattıktan sonra bir hata alırsınız. Bir çoğaltma oluşturmadan önce bu iki adımı uygulayın.

Bir çoğaltma, ana öğe ile aynı işlem ve depolama ayarları kullanılarak oluşturulur. Bir çoğaltma oluşturulduktan sonra, çeşitli ayarlar kaynak sunucudan bağımsız olarak değiştirilebilir: işlem oluşturma, sanal çekirdek, depolama ve yedekleme saklama süresi. Fiyatlandırma Katmanı, temel katmandan veya dışında bağımsız olarak da değiştirilebilir.


> [!IMPORTANT]
> Kaynak sunucu ayarı yeni bir değere güncellenmesinden önce, çoğaltma ayarını eşit veya daha büyük bir değere güncelleştirin. Bu eylem, çoğaltmanın ana üzerinde yapılan değişikliklerle devam etmesine yardımcı olur.

### <a name="list-replicas"></a>Çoğaltmaları Listele
[Çoğaltma LISTESI API](/rest/api/mysql/replicas/listbyserver)'sini kullanarak bir kaynak sunucunun çoğaltmalarının listesini görebilirsiniz:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Çoğaltma sunucusuna çoğaltmayı durdur
[GÜNCELLEŞTIRME API](/rest/api/mysql/servers/update)'sini kullanarak bir kaynak sunucu ile okuma çoğaltması arasındaki çoğaltmayı durdurabilirsiniz.

Kaynak sunucuya çoğaltmayı ve bir okuma çoğaltmasını durdurduktan sonra geri alınamaz. Okuma çoğaltması, hem okuma hem de yazma işlemlerini destekleyen tek başına bir sunucu haline gelir. Tek başına sunucu tekrar bir çoğaltmaya yapılamaz.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>Kaynak veya çoğaltma sunucusunu silme
Bir kaynak veya çoğaltma sunucusunu silmek için [SILME API](/rest/api/mysql/servers/delete)'sini kullanın:

Bir kaynak sunucuyu sildiğinizde, tüm okuma çoğaltmalarına çoğaltma durdurulur. Okuma çoğaltmaları artık hem okuma hem de yazma işlemlerini destekleyen tek başına sunucular haline gelir.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Sonraki adımlar

- [Okuma çoğaltmaları](concepts-read-replicas.md) hakkında daha fazla bilgi edinin