---
title: 'Öğretici: Azure CLI kullanarak MariaDB için Azure veritabanı tasarlama'
description: Bu öğreticide, oluşturma ve MariaDB server ve Azure CLI kullanarak komut satırından veritabanı için Azure veritabanı'nı yönetme açıklanmaktadır.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 11/10/2018
ms.custom: mvc
ms.openlocfilehash: 548f4f10758b2d69bf4fda00f8bf52d33d20306c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999152"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Öğretici: Azure CLI kullanarak MariaDB için Azure veritabanı tasarlama

MariaDB için Azure veritabanı Microsoft bulutunda MariaDB Community Edition veritabanı altyapısını temel alan ilişkisel veritabanı hizmetidir. Bu öğreticide, şunları nasıl yapacağınızı öğrenmek için Azure CLI (komut satırı arabirimi) ve diğer yardımcı programları kullanırsınız:

> [!div class="checklist"]
> * MariaDB için Azure Veritabanı oluşturma
> * Sunucu güvenlik duvarını yapılandırma
> * Veritabanı oluşturmak için [mysql komut satırı aracı](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) kullanma
> * Örnek verileri yükleme
> * Verileri sorgulama
> * Verileri güncelleştirme
> * Verileri geri yükleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

Bu öğreticide kod bloklarını çalıştırmak için tarayıcıda Azure Cloud Shell kullanabilir ya da kendi bilgisayarınıza [Azure CLI yükleyebilirsiniz]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale için Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli). 

Birden fazla aboneliğiniz varsa kaynağın mevcut olduğu ve faturalandırıldığı uygun aboneliği seçin. [az account set](/cli/azure/account#az-account-set) komutunu kullanarak hesabınız altındaki belirli bir abonelik kimliğini seçin.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
[az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) komutuyla bir [Azure kaynak grubu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek `westus` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>MariaDB için Azure Veritabanı sunucusu oluşturma
MariaDB sunucusu için Azure veritabanı oluşturma `az mariadb server create` komutu. Bir sunucu birden çok veritabanını yönetebilir. Genellikle her proje veya kullanıcı için farklı bir veritabanı kullanılır.

Aşağıdaki örnekte yer alan MariaDB server için Azure veritabanı oluşturulmaktadır `westus` kaynak grubundaki `myresourcegroup` adıyla `mydemoserver`. Sunucunun `myadmin` şeklinde bir oturum açma adı vardır. Bu, genel amaçlı, 5. nesil 2 sanal çekirdek sunucusuyla olur. `<server_admin_password>` değerini kendi değerinizle değiştirin.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
sku-name parametresi değeri aşağıdaki örneklerde gösterildiği gibi {fiyatlandırma katmanı}\_{işlem oluşturma}\_{sanal çekirdek} kuralını kullanır:
+ `--sku-name B_Gen5_4` Temel, 5. Nesil ve 4 sanal çekirdekle eşleşir.
+ `--sku-name GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
+ `--sku-name MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgeler ve katmanlar için geçerli olan değerleri anlamak için lütfen [fiyatlandırma katmanları](./concepts-pricing-tiers.md) belgesini inceleyin.

> [!IMPORTANT]
> Burada belirttiğiniz sunucu yöneticisi kullanıcı adı ve parolası, bu hızlı başlangıcın sonraki bölümlerinde sunucuda ve veritabanlarında oturum açmak için gereklidir. Bu bilgileri daha sonra kullanmak üzere aklınızda tutun veya kaydedin.


## <a name="configure-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma
Sunucu düzeyinde bir güvenlik duvarı kuralı MariaDB için Azure veritabanı oluşturma `az mariadb server firewall-rule create` komutu. Sunucu düzeyinde güvenlik duvarı kuralı gibi bir dış uygulamaya izin verir **mysql** komut satırı aracı veya MySQL Workbench'ın Azure MariaDB hizmetinin güvenlik duvarı üzerinden sunucunuza bağlanın.

Aşağıdaki örnek `AllowMyIP` adında ve 192.168.0.1 IP adresinden gelen bağlantılara izin veren bir güvenlik duvarı kuralı oluşturur. IP adresini veya IP adresi aralıklarını bağlandığınız adreslere göre değiştirin.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **fullyQualifiedDomainName** ve **administratorLogin** bilgilerini not alın.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql kullanarak sunucuya bağlanma
MariaDB için Azure Veritabanı sunucunuzla bağlantı kurmak üzere [mysql komut satırı aracını](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) kullanın. Bu örnekte, komut şöyledir:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Boş veritabanı oluşturma
Sunucuya bağlandıktan sonra, boş bir veritabanı oluşturun.
```sql
mysql> CREATE DATABASE mysampledb;
```

İstemde, bağlantıyı bu yeni oluşturulan veritabanına değiştirmek için şu komutu çalıştırın:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Veritabanında tablo oluşturma
MariaDB veritabanı için Azure veritabanına bağlanmak üzere nasıl artık bildiğinize göre bazı temel görevleri tamamlayın.

İlk olarak, bir tablo oluşturun ve bu tabloya bazı veriler yükleyin. Envanter bilgilerini depolayan bir tablo oluşturalım.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Tablolara veri yükleme
Bir tablonuz olduğuna göre içine bazı veriler ekleyin. Açık komut istemi penceresinde, birkaç veri satırı eklemek için şu sorguyu çalıştırın.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Daha önce oluşturduğunuz tabloda artık iki satırlık örnek verileriniz var.

## <a name="query-and-update-the-data-in-the-tables"></a>Tablolardaki verileri sorgulama ve güncelleştirme
Veritabanı tablosundan bilgi almak için şu sorguyu yürütün.
```sql
SELECT * FROM inventory;
```

Ayrıca tablolardaki verileri de güncelleştirebilirsiniz.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Siz veri aldıkça satır güncelleştirilir.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Bir veritabanını daha önceki bir noktaya geri yükleme
Bu tabloyu yanlışlıkla sildiğinizi düşünün. Bu işlemi kolayca geri alamazsınız. MariaDB için Azure veritabanı, son yukarı 35 günde zamanın herhangi bir noktasına geri dönmenize ve bu noktaya zaman yeni bir sunucuya geri yüklemenize olanak sağlar. Bu yeni sunucuyu silinen verilerinizi kurtarmak için kullanabilirsiniz. Şu adımlar, örnek sunucuyu tablo eklenmeden önceki bir noktaya geri yükler.

Geri yüklemek için şu bilgiler gereklidir:

- Geri yükleme noktası: Bir-sunucu değiştirilmeden önce gerçekleşen belirli bir noktaya seçin. Kaynak veritabanının En eski yedekleme değerinden daha büyük veya bu değere eşit olmalıdır.
- Hedef sunucu: Geri yüklemek istediğiniz yeni bir sunucu adı sağlayın
- Kaynak sunucu: Öğesinden geri yüklemek istediğiniz sunucunun adını belirtin
- Konum: Bölgeyi seçemezsiniz, varsayılan olarak, kaynak sunucuyla aynıdır

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

`az mariadb server restore` komutu için aşağıdaki parametreler gerekir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Kaynak sunucunun bulunduğu kaynak grubu.  |
| name | mydemoserver-restored | Geri yükleme komutu tarafından oluşturulan yeni sunucunun adı. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Zaman içinde geri yüklenecek bir nokta seçin. Bu tarih ve saat, kaynak sunucunun yedekleme saklama dönemi içinde olmalıdır. ISO8601 tarih ve saat biçimini kullanın. Örneğin, `2017-04-13T05:59:00-08:00` şeklinde kendi yerel saat diliminizi ya da `2017-04-13T13:59:00Z` şeklindeki UTC Zulu biçimini kullanabilirsiniz. |
| source-server | mydemoserver | Geri yükleme kaynağı olarak kullanılacak sunucunun adı veya kimliği. |

Bir sunucu zaman içinde bir noktaya geri yüklendiğinde, özgün sunucunun belirttiğiniz noktadaki durumunun kopyalanmasıyla yeni bir sunucu oluşturulur. Geri yüklenen sunucunun konum ve fiyatlandırma katmanı değerleri kaynak sunucu ile aynı olur.

Komut zaman uyumludur ve sunucu geri yüklendikten sonra döndürülür. Geri yükleme tamamlandığında, oluşturulan yeni sunucuyu bulun. Verilerin beklenen şekilde geri yüklendiğini doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * MariaDB için Azure Veritabanı sunucusu oluşturma
> * Sunucu güvenlik duvarını yapılandırma
> * Veritabanı oluşturmak için [mysql komut satırı aracı](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) kullanma
> * Örnek verileri yükleme
> * Verileri sorgulama
> * Verileri güncelleştirme
> * Verileri geri yükleme
