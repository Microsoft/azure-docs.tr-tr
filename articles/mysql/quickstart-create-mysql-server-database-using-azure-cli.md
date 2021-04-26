---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure CLı-MySQL için Azure veritabanı'
description: Bu hızlı başlangıçta, Azure CLI aracını kullanarak bir Azure kaynak grubunda nasıl MySQL için Azure Veritabanı sunucusu oluşturabileceğiniz açıklanır.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 63c7fe703a1fbf4cb46532085a33efd74e6a76ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875386"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak MySQL için Azure veritabanı sunucusu oluşturma

> [!TIP]
> Daha basit [az MySQL up](/cli/azure/mysql#az_mysql_up) Azure CLI komutunu kullanmayı düşünün (Şu anda önizleme aşamasındadır). [Hızlı](./quickstart-create-server-up-azure-cli.md)başlangıcı deneyin.

Bu hızlı başlangıçta, beş dakikada bir MySQL için Azure veritabanı sunucusu oluşturmak üzere [Azure Cloud Shell](https://shell.azure.com) ' de [Azure CLI](/cli/azure/get-started-with-azure-cli) komutlarının nasıl kullanılacağı gösterilmektedir. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Bu hızlı başlangıç, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

 - [Az Account set](/cli/azure/account) komutunu kullanarak hesabınız altındaki belirli bir aboneliği seçin. Komutta **abonelik** bağımsız değişkeninin değeri olarak kullanılacak **az Login** çıktısından **ID** değerini bir yere unutmayın. Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. Aboneliğinizi tamamen almak için [az Account List](/cli/azure/account#az_account_list)kullanın.

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>MySQL için Azure Veritabanı sunucusu oluşturma
[Az Group Create](/cli/azure/group) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun ve ardından bu kaynak grubunun içinde MySQL sunucunuzu oluşturun. Benzersiz bir ad sağlamanız gerekir. Aşağıdaki örnek `westus` konumunda `myresourcegroup` adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

[Az MySQL Server Create](/cli/azure/mysql/server#az_mysql_server_create) komutunu kullanarak MySQL Için Azure veritabanı sunucusu oluşturun. Bir sunucu birden çok veritabanı içerebilir.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Yukarıdaki bağımsız değişkenlerin ayrıntıları aşağıda verilmiştir: 

**Ayar** | **Örnek değer** | **Açıklama**
---|---|---
name | mydemoserver | MySQL için Azure veritabanı sunucunuz için benzersiz bir ad girin. Sunucu adı yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3 ila 63 karakter arası içermelidir.
resource-group | myresourcegroup | Azure kaynak grubunun adını sağlayın.
location | westus | Sunucu için Azure konumu.
admin-user | myadmin | Yöneticinin oturum açma kullanıcı adı. Şu değerler kullanılamaz: **azure_superuser**, **admin**, **administrator**, **root**, **guest** veya **public**.
admin-password | *güvenli parola* | Yönetici kullanıcının parolası. 8 ile 128 arasında karakter içermelidir. Parolanız şu üç kategoride yer alan karakterlerden oluşmalıdır: İngilizce büyük ve küçük harfler, sayılar ve alfasayısal olmayan karakterler.
sku-name|GP_Gen5_2|Fiyatlandırma katmanının adını ve işlem yapılandırmasını girin. Toplu olarak {fiyatlandırma katmanı}_{COMPUTE Generation}_{vçekirdekler} kuralını izler. Daha fazla bilgi için [fiyatlandırma katmanlarına](./concepts-pricing-tiers.md) bakın.

>[!IMPORTANT] 
>- Sunucunuzdaki varsayılan MySQL sürümü 5,7 ' dir. Şu anda 5,6 ve 8,0 sürümleri de mevcuttur.
>- **Az MySQL Server Create** komutunun tüm bağımsız değişkenlerini görüntülemek için bu [Başvuru belgesine](/cli/azure/mysql/server#az_mysql_server_create)bakın.
>- SSL, sunucunuzda varsayılan olarak etkinleştirilmiştir. SSL üzerinde daha fazla bilgi için bkz. [SSL bağlantısını yapılandırma](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Sunucu düzeyinde güvenlik duvarı kuralı oluşturma 
Oluşturulan yeni sunucu varsayılan olarak güvenlik duvarı kurallarıyla korunur ve güvenli şekilde erişilebilir değildir. [Az MySQL Server Firewall-Rule Create](/cli/azure/mysql/server/firewall-rule) komutunu kullanarak sunucunuzda güvenlik duvarı kuralını yapılandırabilirsiniz. Bu, sunucuya yerel olarak bağlanmanızı sağlar.

Aşağıdaki örnek `AllowMyIP` adında ve 192.168.0.1 IP adresinden gelen bağlantılara izin veren bir güvenlik duvarı kuralı oluşturur. Bağlanacağınız IP adresini değiştirin. Gerekirse bir IP adresi aralığı kullanabilirsiniz. IP 'nizi nasıl bakabileceğinizi bilmiyorsanız [https://whatismyipaddress.com/](https://whatismyipaddress.com/) IP adresinizi almak için adresine gidin.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> MySQL için Azure Veritabanı bağlantıları 3306 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, 3306 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 3306 numaralı bağlantı noktasını açmadığı sürece sunucunuza bağlanamazsınız.

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Sonuç JSON biçimindedir. **fullyQualifiedDomainName** ve **administratorLogin** bilgilerini not alın.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>MySQL komut satırı istemcisini kullanarak MySQL için Azure veritabanı sunucusuna bağlanma
[Azure Cloud Shell](../cloud-shell/overview.md)bir komut satırı aracı **[mysql.exe](https://dev.mysql.com/downloads/)** popüler bir istemci aracı kullanarak sunucunuza bağlanabilirsiniz. Alternatif olarak, yerel ortamınızda MySQL komut satırını kullanabilirsiniz.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu kaynaklara başka bir hızlı başlangıç/öğretici için gereksinim duymuyorsanız aşağıdaki komutu çalıştırarak kaynakları silebilirsiniz: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Yeni oluşturulan sunucuyu silmek istiyorsanız [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) komutunu kullanabilirsiniz.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
>[MySQL ile Windows üzerinde bir PHP uygulaması derleme](../app-service/tutorial-php-mysql-app.md)