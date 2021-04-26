---
title: Sunucuyu yönetme-Azure CLı-PostgreSQL için Azure veritabanı-esnek sunucu
description: Azure CLı 'dan bir PostgreSQL için Azure veritabanı-esnek sunucu yönetme hakkında bilgi edinin.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9a7e16bf85293a412baf5015af825377438ebb7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778508"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Azure CLı kullanarak PostgreSQL için Azure veritabanı 'nı yönetme-esnek sunucu

> [!IMPORTANT]
> PostgreSQL için Azure veritabanı-esnek sunucu önizlemededir.

Bu makalede, Azure 'da dağıtılan esnek sunucunuzu nasıl yöneteceğiniz gösterilmektedir. Yönetim görevleri işlem ve depolama ölçeklendirmesi, yönetici parola sıfırlama ve sunucu ayrıntılarını görüntüleme içerir.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun. 

Yerel olarak, Azure CLı sürüm 2,0 veya üstünü çalıştırıyor olmanız gerekir. Yüklü sürümü görmek için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

[Az Login](/cli/azure/reference-index#az_login) komutunu kullanarak hesabınızda oturum açın. 

```azurecli-interactive
az login
```

[Az Account set](/cli/azure/account) komutunu kullanarak aboneliğinizi seçin. Aşağıdaki komutta, **abonelik** bağımsız değişkeninin değeri olarak kullanılacak **az Login** çıktısından **ID** değerini bir yere unutmayın. Birden çok aboneliğiniz varsa, kaynağın faturalandırılması gereken aboneliği seçin. Tüm aboneliklerinizi belirlemek için [az Account List](/cli/azure/account#az_account_list) komutunu kullanın.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Henüz esnek bir sunucu oluşturmadıysanız, bu nasıl yapılır kılavuzunu izlemek için bunu yapmanız gerekir.

## <a name="scale-compute-and-storage"></a>İşlem ve depolamayı ölçeklendirme

Aşağıdaki komutu kullanarak işlem katmanınızı, sanal çekirdekleri ve depolamayı kolayca ölçekleyebilirsiniz. Çalıştırabileceğiniz tüm sunucu işlemlerinin bir listesi için, [az Postgres esnek-sunucuya](/cli/azure/postgres/flexible-server) genel bakış bölümüne bakın.

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Yukarıdaki koddaki bağımsız değişkenlerin ayrıntıları aşağıda verilmiştir:

**Ayar** | **Örnek değer** | **Açıklama**
---|---|---
name | mydemoserver | Sunucunuz için benzersiz bir ad girin. Sunucu adı yalnızca küçük harf, sayı ve kısa çizgi (-) karakterini içerebilir. 3 ile 63 arasında karakter içermelidir.
resource-group | myresourcegroup | Azure kaynak grubunun adını sağlayın.
sku-name|Standard_D4ds_v3|İşlem katmanının adını ve boyutunu girin. Değer, toplu *Standard_ {VM size}* kuralına uyar. Daha fazla bilgi için [fiyatlandırma katmanlarına](../concepts-pricing-tiers.md) bakın.
storage-size | 6144 | Sunucunun depolama kapasitesini megabayt cinsinden girin. Minimum değer 5120 ' dir ve 1024 ' lik artışlarla artar.

> [!IMPORTANT]
> Depolamayı ölçeklendiremezsiniz. 

## <a name="manage-postgresql-databases-on-a-server"></a>Sunucu üzerinde PostgreSQL veritabanlarını yönetme

PostgreSQL sunucusu için Azure veritabanınıza çeşitli uygulamalar kullanarak bağlanabilirsiniz. İstemci bilgisayarınızda PostgreSQL yüklüyse, yerel bir [psql](https://www.postgresql.org/docs/current/static/app-psql.html)örneği kullanabilirsiniz. Şimdi PostgreSQL için Azure veritabanı sunucusuna bağlanmak üzere psql komut satırı aracını kullanalım.

1. Aşağıdaki **psql** komutunu çalıştırın:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Örneğin, aşağıdaki komut, erişim kimlik bilgileriniz aracılığıyla PostgreSQL sunucunuzdaki **mydemoserver.Postgres.Database.Azure.com** **Postgres** adlı varsayılan veritabanına bağlanır. İstendiğinde, seçtiğiniz öğesini girin `<server_admin_password>` .
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Bağlandıktan sonra, psql aracı SQL komutları girebileceğiniz bir **Postgres** istemi görüntüler. İlk bağlantı çıkışında, kullanmakta olduğunuz psql sürümü PostgreSQL için Azure veritabanı sunucusu üzerindeki sürümden farklıysa bir uyarı görüntülenir.

   Örnek psql çıktısı:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Güvenlik duvarı istemcinizin IP adreslerine izin verecek biçimde yapılandırılmamışsa aşağıdaki hata oluşur:
   >
   > "psql: ÖNEMLI: Ana bilgisayar `<IP address>` ," myadmin ", veritabanı" Postgres "için bir pg_hba. conf girişi, önemli olan SSL: SSL bağlantısı gerekiyor. SSL seçeneklerini belirtin ve yeniden deneyin. "
   >
   > Güvenlik duvarı kurallarında istemcinizin IP adresine izin verildiğini doğrulayın.

2. Komut istemine aşağıdaki komutu yazarak **postgresdb** adlı boş bir veritabanı oluşturun:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Komut isteminde, bağlantıları yeni oluşturulan veritabanı **postgresdb** ile değiştirmek için aşağıdaki komutu çalıştırın:

    ```bash
    \c postgresdb
    ```

4. `\q`Psql 'den çıkmak için yazın ve ENTER 'ı seçin.

Bu bölümde, psql ile PostgreSQL için Azure veritabanı sunucusuna bağlandınız ve boş bir kullanıcı veritabanı oluşturdunuz.

## <a name="reset-the-admin-password"></a>Yönetici parolasını sıfırlama

Aşağıdaki komutla yönetici rolü parolasını değiştirebilirsiniz:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> En az 8 karakter ve en fazla 128 karakter içeren bir parola seçin. Parolanın aşağıdaki kategorilerden üçünden karakterler içermesi gerekir: 
> - İngilizce alfabesinin büyük harfleri
> - İngilizce alfabesinin küçük harfleri
> - Sayılar
> - Alfasayısal olmayan karakterler

## <a name="delete-a-server"></a>Sunucu silme

PostgreSQL için Azure veritabanı esnek sunucusunu silmek için [az Postgres esnek-sunucu Sil](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete) komutunu çalıştırın.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Sonraki adımlar

- [Yedekleme ve geri yükleme kavramlarını anlama](concepts-backup-restore.md)
- [Sunucuyu ayarlama ve izleme](concepts-monitoring.md)