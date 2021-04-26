---
title: Sunucuyu yönetme-Azure CLı-MySQL için Azure veritabanı esnek sunucu
description: MySQL için Azure veritabanı esnek sunucusunu Azure CLı 'dan yönetmeyi öğrenin.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4ef1408d5f7afc3b78ab021cdd25eedd75110849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776942"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı 'nı yönetme-esnek sunucu (Önizleme)

> [!IMPORTANT]
> MySQL için Azure veritabanı-esnek sunucu şu anda genel önizlemededir.

Bu makalede, Azure 'da dağıtılan esnek sunucunuzu (Önizleme) nasıl yöneteceğiniz gösterilmektedir. Yönetim görevleri işlem ve depolama ölçeklendirmesi, yönetici parola sıfırlama ve sunucu ayrıntılarını görüntüleme içerir.

## <a name="prerequisites"></a>Önkoşullar
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun. Bu makalede, Azure CLı sürüm 2,0 veya üstünü yerel olarak çalıştırıyor olmanız gerekir. Yüklü sürümü görmek için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

[Az Login](/cli/azure/reference-index#az_login) komutunu kullanarak hesabınızda oturum açmanız gerekir. Azure hesabınızın **ABONELIK kimliğini** ifade eden **ID** özelliğine göz önüne alın.

```azurecli-interactive
az login
```

[Az Account set](/cli/azure/account) komutunu kullanarak hesabınız altındaki belirli bir aboneliği seçin. Komutta **abonelik** bağımsız değişkeninin değeri olarak kullanılacak **az Login** çıktısından **ID** değerini bir yere unutmayın. Birden fazla aboneliğiniz varsa kaynağın faturalanacağı uygun aboneliği seçin. Aboneliğinizi tamamen almak için [az Account List](/cli/azure/account#az_account_list)kullanın.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Henüz esnek bir sunucu oluşturmadıysanız, bu nasıl yapılır Kılavuzu ile çalışmaya başlamak için lütfen bir tane oluşturun.

## <a name="scale-compute-and-storage"></a>İşlem ve depolamayı ölçeklendirme

Aşağıdaki komutu kullanarak işlem katmanınızı, sanal çekirdekleri ve depolamayı kolayca ölçeklendirebilirsiniz. Gerçekleştirebileceğiniz tüm sunucu işlemlerini görebilirsiniz [az MySQL esnek-Server Update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Yukarıdaki bağımsız değişkenlerin ayrıntıları aşağıda verilmiştir:

**Ayar** | **Örnek değer** | **Açıklama**
---|---|---
name | mydemoserver | MySQL için Azure veritabanı sunucunuz için benzersiz bir ad girin. Sunucu adı yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3 ila 63 karakter arası içermelidir.
resource-group | myresourcegroup | Azure kaynak grubunun adını sağlayın.
sku-name|Standard_D4ds_v4|İşlem katmanının adını ve boyutunu girin. Toplu Standard_ {VM size} kuralına uyar. Daha fazla bilgi için [fiyatlandırma katmanlarına](../concepts-pricing-tiers.md) bakın.
storage-size | 6144 | Sunucunun depolama kapasitesi (birim olan megabayt kullanılır). Minimum 5120, 1024 artışlarla artar.

> [!Important]
> - Depolama alanı yukarı ölçeklendirilebilir (ancak, depolamayı ölçeklendirmezsiniz)


## <a name="manage-mysql-databases-on-a-server"></a>MySQL veritabanlarını bir sunucuda yönetin.
Sunucunuzdaki bir veritabanının veritabanı özelliklerini oluşturmak, silmek, listelemek ve görüntülemek için aşağıdaki komutlardan herhangi birini kullanabilirsiniz

| Cmdlet | Kullanım| Description |
| --- | ---| --- |
|[az MySQL esnek-sunucu DB oluştur](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Bir veritabanı oluşturur|
|[az MySQL esnek-sunucu DB Delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Veritabanınızı sunucudan silin. Bu komut, sunucunuzu silmez. |
|[az MySQL esnek-sunucu DB listesi](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|sunucudaki tüm veritabanlarını listeler|
|[az MySQL esnek-sunucu DB Show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Veritabanının daha fazla ayrıntılarını gösterir|

## <a name="update-admin-password"></a>Yönetici parolasını Güncelleştir
Bu komutla yönetici rolü parolasını değiştirebilirsiniz
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Parolanın en az 8 karakter ve en fazla 128 karakter uzunluğunda olduğundan emin olun.
> Parola şu kategorilerden üçünden karakterler içermelidir: Ingilizce büyük harfler, Ingilizce küçük harfler, sayılar ve alfasayısal olmayan karakterler.

## <a name="delete-a-server"></a>Sunucu silme
MySQL esnek sunucusunu silmek istiyorsanız [az MySQL esnek-Server Server DELETE](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) komutunu çalıştırabilirsiniz.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar
- [Sunucu başlatmayı veya durdurmayı öğrenin](how-to-stop-start-server-portal.md)
- [Sanal ağı yönetmeyi öğrenin](how-to-manage-virtual-network-cli.md)
- [Bağlantı sorunlarını giderme](how-to-troubleshoot-common-connection-issues.md)
- [Güvenlik Duvarı oluşturma ve yönetme](how-to-manage-firewall-cli.md)