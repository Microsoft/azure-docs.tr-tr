---
title: Yedekleme ve geri yükleme-Azure CLı-MySQL için Azure veritabanı
description: Azure CLı kullanarak MySQL için Azure veritabanı 'nda bir sunucuyu yedeklemeyi ve geri yüklemeyi öğrenin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8c8b0f37729ea20a62838d736dbed59f05c584c6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780434"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Azure CLı kullanarak MySQL için Azure veritabanı 'nda bir sunucuyu yedekleme ve geri yükleme

MySQL için Azure veritabanı sunucuları, geri yükleme özelliklerini etkinleştirmek üzere düzenli aralıklarla yedeklenir. Bu özelliği kullanarak, sunucuyu ve tüm veritabanlarını yeni bir sunucuda daha önceki bir zaman noktasına geri yükleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için:

- [MySQL Için Azure veritabanı sunucusu ve veritabanı](quickstart-create-mysql-server-database-using-azure-cli.md)gerekir.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin 2,0 veya sonraki bir sürümünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="set-backup-configuration"></a>Yedekleme yapılandırmasını ayarla

Sunucu oluşturma sırasında sunucunuzu yerel olarak yedekli yedeklemeler veya coğrafi olarak yedekli yedeklemeler için yapılandırma arasında seçim yaparsınız. 

> [!NOTE]
> Bir sunucu oluşturulduktan sonra, coğrafi olarak yedekli ve yerel olarak yedekli olan artıklık türü değiştirilemez.
>

Komutu aracılığıyla bir sunucu oluştururken `az mysql server create` , `--geo-redundant-backup` parametresi yedekleme yedeklilik seçeneğine karar verir. İse `Enabled` , coğrafi olarak yedekli yedeklemeler alınır. Ya da `Disabled` yerel olarak yedekli yedeklemeler alınırsa. 

Yedekleme saklama süresi parametresi tarafından ayarlanır `--backup-retention` . 

Oluşturma sırasında bu değerleri ayarlama hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı sunucu CLI hızlı başlangıç](quickstart-create-mysql-server-database-using-azure-cli.md).

Bir sunucunun yedekleme saklama süresi şu şekilde değiştirilebilir:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Yukarıdaki örnek, demosunucum yedekleme bekletme süresini 10 gün olarak değiştirir.

Yedekleme bekletme süresi, kullanılabilir yedeklemeler temel aldığı için zaman içinde bir nokta geri yüklemesi alma süresinin ne kadar geri alınacağını yönetir. Zaman içinde geri yükleme sonraki bölümde daha ayrıntılı olarak açıklanmıştır.

## <a name="server-point-in-time-restore"></a>Sunucu zaman içinde geri yükleme
Sunucuyu zaman içinde önceki bir noktaya geri yükleyebilirsiniz. Geri yüklenen veriler yeni bir sunucuya kopyalanır ve var olan sunucu olduğu gibi bırakılır. Örneğin, bir tablo yanlışlıkla öğleden sonra bırakılırsa, gece ' e kadar olan saate geri yükleyebilirsiniz. Daha sonra, eksik tablo ve verileri, sunucunun geri yüklenen kopyasından elde edebilirsiniz. 

Sunucuyu geri yüklemek için Azure CLı [az MySQL Server restore](/cli/azure/mysql/server#az_mysql_server_restore) komutunu kullanın.

### <a name="run-the-restore-command"></a>Restore komutunu çalıştırın

Sunucuyu geri yüklemek için, Azure CLı komut isteminde aşağıdaki komutu girin:

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Kaynak sunucunun varolduğu kaynak grubu.  |
| name | mydemoserver-restored | Geri yükleme komutu tarafından oluşturulan yeni sunucunun adı. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Geri yüklenecek bir zaman noktası seçin. Bu tarih ve saat, kaynak sunucunun yedekleme saklama dönemi içinde olmalıdır. ISO8601 tarih ve saat biçimini kullanın. Örneğin, kendi yerel saat diliminizi (gibi) kullanabilirsiniz `2018-03-13T05:59:00-08:00` . UTC Zulu dili biçimini de kullanabilirsiniz. Örneğin, `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Geri yükleme kaynağı olarak kullanılacak sunucunun adı veya kimliği. |

Bir sunucuyu daha önceki bir zaman noktasına geri yüklediğinizde yeni bir sunucu oluşturulur. Özgün sunucu ve belirtilen zaman içindeki veritabanları yeni sunucuya kopyalanır.

Geri yüklenen sunucu için konum ve fiyatlandırma katmanı değerleri, özgün sunucu ile aynı kalır. 

Geri yükleme işlemi tamamlandıktan sonra, yeni sunucuyu bulun ve verilerin beklendiği gibi geri yüklendiğini doğrulayın. Yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Ayrıca, geri yükleme işlemi tamamlandıktan sonra, geri yükleme işleminden sonra varsayılan değerlere sıfırlanan (ve birincil sunucudan kopyalanmayan) iki sunucu parametresi vardır
*   time_zone-bu değer varsayılan değer **sistemine** ayarlanır
*   event_scheduler-event_scheduler, geri yüklenen sunucuda **kapalı** olarak ayarlanmıştır

Birincil sunucudan değeri kopyalamanız ve [sunucu parametresini](howto-server-parameters.md) yeniden yapılandırarak geri yüklenen sunucuda ayarlamanız gerekir

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan VNet hizmeti uç noktalarına sahip değildir. Bu kuralların bu yeni sunucu için ayrıca ayarlanması gerekir. Özgün sunucudan gelen güvenlik duvarı kuralları geri yüklendi.

## <a name="geo-restore"></a>Coğrafi geri yükleme
Sunucunuzu coğrafi olarak yedekli yedeklemeler için yapılandırdıysanız, var olan sunucunun yedeklemesinden yeni bir sunucu oluşturulabilir. Bu yeni sunucu, MySQL için Azure veritabanı 'nın kullanılabildiği herhangi bir bölgede oluşturulabilir.  

Coğrafi olarak yedekli bir yedekleme kullanarak bir sunucu oluşturmak için Azure CLı komutunu kullanın `az mysql server georestore` .

> [!NOTE]
> Sunucu ilk oluşturulduğunda coğrafi geri yükleme için hemen kullanılamayabilir. Gerekli meta verilerin doldurulması birkaç saat sürebilir.
>

Sunucuyu coğrafi olarak geri yüklemek için, Azure CLı komut isteminde aşağıdaki komutu girin:

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8 
```
Bu komut, *myresourcegroup* öğesine ait olacak Doğu ABD *mydemoserver-geogeri yüklenen* adlı yeni bir sunucu oluşturur. 8 sanal çekirdeğe sahip bir Genel Amaçlı, Gen 5 sunucusudur. Sunucu, *myresourcegroup* kaynak grubunda de olan, coğrafi olarak yedekli olan *demosunucum* yedeğinden oluşturulur.

Yeni sunucuyu var olan sunucudan farklı bir kaynak grubunda oluşturmak isterseniz, `--source-server` Aşağıdaki örnekte olduğu gibi parametresi için sunucu adını niteleyebilirsiniz:

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

`az mysql server georestore`Komut aşağıdaki parametreleri gerektirir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
|resource-group| myresourcegroup | Yeni sunucunun ait olacağı kaynak grubunun adı.|
|name | mydemoserver-geogeri yüklendi | Yeni sunucunun adı. |
|source-server | mydemoserver | Coğrafi olarak yedekli yedeklemeleri kullanılan mevcut sunucunun adı. |
|location | eastus | Yeni sunucunun konumu. |
|sku-name| GP_Gen5_8 | Bu parametre, yeni sunucunun fiyatlandırma katmanını, işlem üretimini ve sanal çekirdek sayısını ayarlar. GP_Gen5_8, 8 sanal çekirdekte bir Genel Amaçlı, Gen 5 sunucusuyla eşlenir.|

Coğrafi geri yükleme ile yeni bir sunucu oluştururken, kaynak sunucuyla aynı depolama boyutunu ve fiyatlandırma katmanını devralır. Bu değerler oluşturma sırasında değiştirilemez. Yeni sunucu oluşturulduktan sonra, depolama boyutu yukarı ölçeklendirilebilir.

Geri yükleme işlemi tamamlandıktan sonra, yeni sunucuyu bulun ve verilerin beklendiği gibi geri yüklendiğini doğrulayın. Yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan VNet hizmeti uç noktalarına sahip değildir. Bu kuralların bu yeni sunucu için ayrıca ayarlanması gerekir. Özgün sunucudan gelen güvenlik duvarı kuralları geri yüklendi.

## <a name="next-steps"></a>Sonraki adımlar
- Hizmetin [yedeklemeleri](concepts-backup.md) hakkında daha fazla bilgi edinin
- [Çoğaltmalar](concepts-read-replicas.md) hakkında bilgi edinin
- [İş sürekliliği](concepts-business-continuity.md) seçenekleri hakkında daha fazla bilgi edinin
