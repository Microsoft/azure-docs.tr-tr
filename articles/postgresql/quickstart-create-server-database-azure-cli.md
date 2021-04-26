---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure CLı-PostgreSQL için Azure veritabanı-tek sunucu'
description: Bu hızlı başlangıç kılavuzunda, Azure CLı kullanarak PostgreSQL için Azure veritabanı sunucusu oluşturacaksınız.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d4d3b44b0ee878028df369de41451e3dc1d3c6de
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875242"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak PostgreSQL için Azure veritabanı sunucusu oluşturma

Bu hızlı başlangıçta, beş dakikada bir PostgreSQL için Azure veritabanı sunucusu oluşturmak üzere [Azure Cloud Shell](https://shell.azure.com) ' de [Azure CLI](/cli/azure/get-started-with-azure-cli) komutlarının nasıl kullanılacağı gösterilmektedir. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

    > [!TIP]
    >  Şu anda önizleme aşamasında olan daha basit [az Postgres up](/cli/azure/postgres#az_postgres_up) Azure CLI komutunu kullanmayı deneyin. [Hızlı](./quickstart-create-server-up-azure-cli.md)başlangıcı deneyin.

- [Az Account set](/cli/azure/account) komutunu kullanarak hesabınız altındaki belirli BIR abonelik kimliğini seçin.

    - Komutta **abonelik** bağımsız değişkeninin değeri olarak kullanılacak **az Login** çıktısından **ID** değerini bir yere unutmayın. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. Tüm aboneliklerinizi almak için [az Account List](/cli/azure/account#az_account_list)kullanın.

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure Veritabanı sunucusu oluşturma

[Az Group Create](/cli/azure/group#az_group_create) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun ve ardından bu kaynak grubunun Içinde PostgreSQL sunucunuzu oluşturun. Benzersiz bir ad sağlamanız gerekir. Aşağıdaki örnek `westus` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

[Az Postgres Server Create](/cli/azure/postgres/server) komutunu kullanarak [PostgreSQL için Azure veritabanı sunucusu](overview.md) oluşturun. Bir sunucu birden çok veritabanı içerebilir.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Yukarıdaki bağımsız değişkenlerin ayrıntıları aşağıda verilmiştir: 

**Ayar** | **Örnek değer** | **Açıklama**
---|---|---
name | mydemoserver | PostgreSQL için Azure veritabanı sunucunuzu tanımlayan benzersiz ad. Sunucu adı yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3 ile 63 arasında karakter içermelidir. Daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı adlandırma kuralları](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql).
resource-group | myresourcegroup | Azure Kaynak grubunun adı.
location | westus | Sunucu için Azure konumu.
admin-user | myadmin | Yönetici oturumu için Kullanıcı adı. **Azure_superuser** **, yönetici**, **yönetici**, **kök**, **Konuk** veya **ortak** olamaz.
admin-password | *güvenli parola* | Yönetici kullanıcısının parolası. Şu kategorilerden üçünden 8 ile 128 karakter içermesi gerekir: Ingilizce büyük harfler, Ingilizce küçük harfler, sayılar ve alfasayısal olmayan karakterler.
sku-name|GP_Gen5_2| Fiyatlandırma katmanının adı ve işlem yapılandırması. Toplu olarak {fiyatlandırma katmanı}_{COMPUTE Generation}_{vçekirdekler} kuralını izleyin. Daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı fiyatlandırması](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Sunucunuzdaki varsayılan PostgreSQL sürümü 9,6 ' dir. Desteklenen tüm sürümleri görmek için bkz. [desteklenen PostgreSQL ana sürümleri](./concepts-supported-versions.md).
>- **Az Postgres Server Create** komutuyla ilgili tüm bağımsız değişkenleri görüntülemek için, [Bu başvuru belgesine](/cli/azure/postgres/server#az_postgres_server_create)bakın.
>- SSL, sunucunuzda varsayılan olarak etkinleştirilmiştir. SSL hakkında daha fazla bilgi için bkz. [SSL bağlantısını yapılandırma](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Sunucu düzeyinde güvenlik duvarı kuralı oluşturma 
Varsayılan olarak, oluşturduğunuz sunucuya herkese açık olmaz ve güvenlik duvarı kurallarıyla korunur. Sunucunuzdaki güvenlik duvarı kurallarını, yerel ortamınıza sunucuya bağlanmak üzere erişim sağlamak için [az Postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) komutunu kullanarak yapılandırabilirsiniz. 

Aşağıdaki örnek `AllowMyIP` adında ve 192.168.0.1 IP adresinden gelen bağlantılara izin veren bir güvenlik duvarı kuralı oluşturur. Bağlanacağınız yere karşılık gelen IP adresini veya IP adresleri aralığını değiştirin. IP adresinizi bilmiyorsanız, almak için [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) adresine gidin.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Bağlantı sorunlarından kaçınmak için, ağınızın güvenlik duvarının bağlantı noktası 5432 ' e izin verdiğinden emin olun. PostgreSQL için Azure veritabanı sunucuları bu bağlantı noktasını kullanır. 

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlayın.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **Tınlogın** ve **Fullyıqualifieddomainname** değerlerini bir yere göz önünde koyun.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Psql kullanarak PostgreSQL için Azure veritabanı sunucusuna bağlanma
[Psql](https://www.postgresql.org/docs/current/static/app-psql.html) Client, PostgreSQL sunucularına bağlanmak için popüler bir seçenektir. [Azure Cloud Shell](../cloud-shell/overview.md)ile psql kullanarak sunucunuza bağlanabilirsiniz. Ayrıca psql istemcisini indirerek yerel ortamınızda da kullanabilirsiniz. Boş bir veritabanı, **Postgres**, yeni bir PostgreSQL sunucusuyla otomatik olarak oluşturulur. Aşağıdaki kodda gösterildiği gibi bu veritabanını psql ile bağlantı kurmak için kullanabilirsiniz. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Postgres 'e bağlanmak için bir URL yolu kullanmayı tercih ediyorsanız, URL, Kullanıcı adında @ işaretini ile kodlayın `%40` . Örneğin, psql için bağlantı dizesi şöyle olacaktır:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu kaynaklara başka bir hızlı başlangıç veya öğretici için ihtiyacınız yoksa, aşağıdaki komutu çalıştırarak bunları silebilirsiniz. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Yalnızca yeni oluşturulan bir sunucuyu silmek istiyorsanız [az Postgres Server DELETE](/cli/azure/postgres/server) komutunu çalıştırabilirsiniz.

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Dışarı aktarma ve içeri aktarma kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md)
