---
title: MariaDB için Azure veritabanı 'nda sunucu yedekleme ve geri yükleme
description: Azure CLı kullanarak MariaDB için Azure veritabanı 'nda bir sunucuyu yedeklemeyi ve geri yüklemeyi öğrenin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: ae2e8049c58be312eed380fe2197985e61d28a26
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965221"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Azure CLı kullanarak MariaDB için Azure veritabanı 'nda bir sunucuyu yedekleme ve geri yükleme

MariaDB sunucuları için Azure veritabanı, geri yükleme özelliklerini etkinleştirmek üzere düzenli aralıklarla yedeklenir. Bu özelliği kullanarak, sunucuyu ve tüm veritabanlarını yeni bir sunucuda daha önceki bir zaman noktasına geri yükleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

- [MariaDB sunucusu ve veritabanı Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Bu nasıl yapılır Kılavuzu, Azure CLı sürüm 2,0 veya üstünü kullanmanızı gerektirir. Sürümü onaylamak için, Azure CLı komut isteminde `az --version`girin. Yüklemek veya yükseltmek için bkz. [Azure CLI 'Yı yüklemek]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Yedekleme yapılandırmasını ayarla

Sunucu oluşturma sırasında sunucunuzu yerel olarak yedekli yedeklemeler veya coğrafi olarak yedekli yedeklemeler için yapılandırma arasında seçim yaparsınız.

> [!NOTE]
> Bir sunucu oluşturulduktan sonra, coğrafi olarak yedekli ve yerel olarak yedekli olan artıklık türü değiştirilemez.
>

`az mariadb server create` komutu aracılığıyla bir sunucu oluştururken, `--geo-redundant-backup` parametresi, yedekleme yedeklemenizin seçeneğine karar verir. `Enabled`, coğrafi olarak yedekli yedeklemeler alınır. Veya `Disabled` yerel olarak yedekli yedeklemeler alınırsa.

Yedekleme saklama süresi `--backup-retention`parametresi tarafından ayarlanır.

Oluşturma sırasında bu değerleri ayarlama hakkında daha fazla bilgi için, bkz. [MariaDB Server CLI hızlı başlangıç Için Azure veritabanı](quickstart-create-mariadb-server-database-using-azure-cli.md).

Bir sunucunun yedekleme saklama süresi şu şekilde değiştirilebilir:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Yukarıdaki örnek, demosunucum yedekleme bekletme süresini 10 gün olarak değiştirir.

Yedekleme bekletme süresi, kullanılabilir yedeklemeler temel aldığı için zaman içinde bir nokta geri yüklemesi alma süresinin ne kadar geri alınacağını yönetir. Zaman içinde geri yükleme sonraki bölümde daha ayrıntılı olarak açıklanmıştır.

## <a name="server-point-in-time-restore"></a>Sunucu zaman içinde geri yükleme

Sunucuyu zaman içinde önceki bir noktaya geri yükleyebilirsiniz. Geri yüklenen veriler yeni bir sunucuya kopyalanır ve var olan sunucu olduğu gibi bırakılır. Örneğin, bir tablo yanlışlıkla öğleden sonra bırakılırsa, gece ' e kadar olan saate geri yükleyebilirsiniz. Daha sonra, eksik tablo ve verileri, sunucunun geri yüklenen kopyasından elde edebilirsiniz.

Sunucuyu geri yüklemek için Azure CLı [az MariaDB Server restore](/cli/azure/mariadb/server#az-mariadb-server-restore) komutunu kullanın.

### <a name="run-the-restore-command"></a>Restore komutunu çalıştırın

Sunucuyu geri yüklemek için, Azure CLı komut isteminde aşağıdaki komutu girin:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mariadb server restore` komutu aşağıdaki parametreleri gerektirir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Kaynak sunucunun varolduğu kaynak grubu.  |
| ad | mydemoserver-restored | Geri yükleme komutu tarafından oluşturulan yeni sunucunun adı. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Geri yüklenecek bir zaman noktası seçin. Bu tarih ve saat, kaynak sunucunun yedekleme saklama dönemi içinde olmalıdır. ISO8601 tarih ve saat biçimini kullanın. Örneğin, `2018-03-13T05:59:00-08:00`gibi kendi yerel saat diliminizi kullanabilirsiniz. UTC Zulu dili biçimini de kullanabilirsiniz; Örneğin, `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Geri yükleme kaynağı olarak kullanılacak sunucunun adı veya kimliği. |

Wbir sunucuyu zaman içinde önceki bir noktaya geri yüklediğinizde yeni bir sunucu oluşturulur. Özgün sunucu ve belirtilen zaman içindeki veritabanları yeni sunucuya kopyalanır.

Geri yüklenen sunucu için konum ve fiyatlandırma katmanı değerleri, özgün sunucu ile aynı kalır. 

Geri yükleme işlemi tamamlandıktan sonra, yeni sunucuyu bulun ve verilerin beklendiği gibi geri yüklendiğini doğrulayın. Yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda bulunan güvenlik duvarı kurallarına veya VNet hizmeti uç noktalarına sahip değildir. Bu kuralların bu yeni sunucu için ayrı olarak ayarlanması gerekir.

## <a name="geo-restore"></a>Coğrafi geri yükleme

Sunucunuzu coğrafi olarak yedekli yedeklemeler için yapılandırdıysanız, var olan sunucunun yedeklemesinden yeni bir sunucu oluşturulabilir. Bu yeni sunucu, MariaDB için Azure veritabanı 'nın kullanılabildiği herhangi bir bölgede oluşturulabilir.  

Coğrafi olarak yedekli bir yedekleme kullanarak bir sunucu oluşturmak için Azure CLı `az mariadb server georestore` komutunu kullanın.

> [!NOTE]
> Sunucu ilk oluşturulduğunda coğrafi geri yükleme için hemen kullanılamayabilir. Gerekli meta verilerin doldurulması birkaç saat sürebilir.
>

Sunucuyu coğrafi olarak geri yüklemek için, Azure CLı komut isteminde aşağıdaki komutu girin:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Bu komut, *myresourcegroup*öğesine ait olacak Doğu ABD *mydemoserver-geogeri yüklenen* adlı yeni bir sunucu oluşturur. 8 sanal çekirdeğe sahip bir Genel Amaçlı, Gen 5 sunucusudur. Sunucu, *myresourcegroup* kaynak grubunda de olan, coğrafi olarak yedekli olan *demosunucum*yedeğinden oluşturulur.

Yeni sunucuyu mevcut sunucudan farklı bir kaynak grubunda oluşturmak istiyorsanız, `--source-server` parametresinde sunucu adını aşağıdaki örnekte olduğu gibi niteleyebilirsiniz:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

`az mariadb server georestore` komutu aşağıdaki parametreleri gerektirir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
|resource-group| myresourcegroup | Yeni sunucunun ait olacağı kaynak grubunun adı.|
|ad | mydemoserver-geogeri yüklendi | Yeni sunucunun adı. |
|source-server | mydemoserver | Coğrafi olarak yedekli yedeklemeleri kullanılan mevcut sunucunun adı. |
|location | eastus | Yeni sunucunun konumu. |
|sku-name| GP_Gen5_8 | Bu parametre, yeni sunucunun fiyatlandırma katmanını, işlem üretimini ve sanal çekirdek sayısını ayarlar. GP_Gen5_8, 8 sanal çekirdeğe sahip bir Genel Amaçlı, Gen 5 sunucusuyla eşlenir.|

Coğrafi geri yükleme ile yeni bir sunucu oluştururken, kaynak sunucuyla aynı depolama boyutunu ve fiyatlandırma katmanını devralır. Bu değerler oluşturma sırasında değiştirilemez. Yeni sunucu oluşturulduktan sonra, depolama boyutu yukarı ölçeklendirilebilir.

Geri yükleme işlemi tamamlandıktan sonra, yeni sunucuyu bulun ve verilerin beklendiği gibi geri yüklendiğini doğrulayın. Yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda bulunan güvenlik duvarı kurallarına veya VNet hizmeti uç noktalarına sahip değildir. Bu kuralların bu yeni sunucu için ayrı olarak ayarlanması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Hizmetin [yedeklemeleri](concepts-backup.md) hakkında daha fazla bilgi edinin
- [Çoğaltmalar](concepts-read-replicas.md) hakkında bilgi edinin
- [İş sürekliliği](concepts-business-continuity.md) seçenekleri hakkında daha fazla bilgi edinin
