---
title: Yedekleme ve geri yükleme-Azure PowerShell-PostgreSQL için Azure veritabanı
description: Azure PowerShell kullanarak PostgreSQL için Azure veritabanı 'nda bir sunucuyu nasıl yedekleyeceğinizi ve geri yükleyeceğinizi öğrenin.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63fffb5998b0b6a245db3f1c8fcf16f2d576936e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92489770"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-postgresql-server-using-powershell"></a>PowerShell kullanarak bir PostgreSQL için Azure veritabanı sunucusunu yedekleme ve geri yükleme

PostgreSQL için Azure veritabanı sunucuları, geri yükleme özelliklerini etkinleştirmek üzere düzenli aralıklarla yedeklenir. Bu özelliği kullanarak, sunucuyu ve tüm veritabanlarını yeni bir sunucuda daha önceki bir zaman noktasına geri yükleyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu nasıl yapılır kılavuzunu tamamlayabilmeniz için şunlar gerekir:

- Yerel olarak yüklenen veya tarayıcıda [Azure Cloud Shell](https://shell.azure.com/) [az PowerShell modülü](/powershell/azure/install-az-ps)
- [PostgreSQL Için Azure veritabanı sunucusu](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az. PostgreSql PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Az. PostgreSql PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

PowerShell 'i yerel olarak kullanmayı seçerseniz, [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanın.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Yedekleme yapılandırmasını ayarla

Sunucu oluşturma sırasında, sunucunuzu yerel olarak yedekli veya coğrafi olarak yedekli yedeklemeler için yapılandırma arasında seçim yaparsınız.

> [!NOTE]
> Bir sunucu oluşturulduktan sonra, coğrafi olarak yedekli ve yerel olarak yedekli olan artıklık türü değiştirilemez.

Komutu aracılığıyla bir sunucu oluştururken `New-AzPostgreSqlServer` , **GeoRedundantBackup** parametresi yedekleme yedekliliğe karar verir. **Etkinleştirilirse**, coğrafi olarak yedekli yedeklemeler alınır. Ya da **devre dışı** bırakılmışsa yerel olarak yedekli yedeklemeler alınır.

Yedekleme saklama süresi **BackupRetentionDay** parametresine göre ayarlanır.

Bu değerleri sunucu oluşturma sırasında ayarlama hakkında daha fazla bilgi için bkz. [PowerShell kullanarak PostgreSQL Için Azure veritabanı sunucusu oluşturma](quickstart-create-postgresql-server-database-using-azure-powershell.md).

Bir sunucunun yedekleme saklama süresi şu şekilde değiştirilebilir:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

Yukarıdaki örnek, demosunucum yedekleme bekletme süresini 10 gün olarak değiştirir.

Yedekleme bekletme süresi, kullanılabilir yedeklemeleri temel aldığı için bir noktadan sonra geri yüklemenin ne kadar geri alınacağını yönetir. Zaman içinde geri yükleme sonraki bölümde daha ayrıntılı olarak açıklanmıştır.

## <a name="server-point-in-time-restore"></a>Sunucu zaman içinde geri yükleme

Sunucuyu önceki bir zaman noktasına geri yükleyebilirsiniz. Geri yüklenen veriler yeni bir sunucuya kopyalanır ve var olan sunucu değişmeden bırakılır. Örneğin, bir tablo yanlışlıkla bırakıldıysanız, yalnızca bırakma gerçekleştiği zamana geri yükleyebilirsiniz. Daha sonra, eksik tablo ve verileri, sunucunun geri yüklenen kopyasından elde edebilirsiniz.

Sunucuyu geri yüklemek için `Restore-AzPostgreSqlServer` PowerShell cmdlet 'ini kullanın.

### <a name="run-the-restore-command"></a>Restore komutunu çalıştırın

Sunucuyu geri yüklemek için PowerShell 'den aşağıdaki örneği çalıştırın.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Cmdlet 'in **Pointintimeresıtransaction** parametre kümesi `Restore-AzPostgreSqlServer` aşağıdaki parametreleri gerektirir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Kaynak sunucunun varolduğu kaynak grubu.  |
| Name | mydemoserver-restored | Geri yükleme komutu tarafından oluşturulan yeni sunucunun adı. |
| Restorepoinıntime | 2020-03-13T13:59:00Z | Geri yüklenecek bir zaman noktası seçin. Bu tarih ve saat, kaynak sunucunun yedekleme saklama dönemi içinde olmalıdır. ISO8601 tarih ve saat biçimini kullanın. Örneğin, **2020-03-13T05:59:00-08:00** gibi kendi yerel saat diliminizi kullanabilirsiniz. UTC Zulu dili biçimini de kullanabilirsiniz; Örneğin, **2018-03-13T13:59:00Z**. |
| Usepointintimerestınic | `<SwitchParameter>` | Geri yüklemek için zaman noktası modunu kullanın. |

Bir sunucuyu önceki bir zaman noktasına geri yüklediğinizde yeni bir sunucu oluşturulur. Özgün sunucu ve veritabanları belirtilen zaman noktasından yeni sunucuya kopyalanır.

Geri yüklenen sunucu için konum ve fiyatlandırma katmanı değerleri, özgün sunucu ile aynı kalır.

Geri yükleme işlemi tamamlandıktan sonra, yeni sunucuyu bulun ve verilerin beklendiği gibi geri yüklendiğini doğrulayın. Yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan VNet hizmeti uç noktalarına sahip değildir. Bu kuralların yeni sunucu için ayrı olarak ayarlanması gerekir. Özgün sunucudan gelen güvenlik duvarı kuralları geri yüklendi.

## <a name="geo-restore"></a>Coğrafi geri yükleme

Sunucunuzu coğrafi olarak yedekli yedeklemeler için yapılandırdıysanız, mevcut sunucunun yedeklemesinden yeni bir sunucu oluşturulabilir. Bu yeni sunucu, PostgreSQL için Azure veritabanı 'nın kullanılabildiği herhangi bir bölgede oluşturulabilir.

Coğrafi olarak yedekli bir yedekleme kullanarak bir sunucu oluşturmak için, `Restore-AzPostgreSqlServer` komutunu **Usegeorestore** parametresiyle birlikte kullanın.

> [!NOTE]
> Sunucu ilk oluşturulduğunda coğrafi geri yükleme için hemen kullanılamayabilir. Gerekli meta verilerin doldurulması birkaç saat sürebilir.

Sunucuyu coğrafi olarak geri yüklemek için PowerShell 'den aşağıdaki örneği çalıştırın:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Bu örnek, **myresourcegroup** öğesine ait Doğu ABD bölgesinde **mydemoserver-geogeri yüklenen** adlı yeni bir sunucu oluşturur. 8 sanal çekirdeğe sahip bir Genel Amaçlı, Gen 5 sunucusudur. Sunucu, **myresourcegroup** kaynak grubundaki **demosunucum**'ın coğrafi olarak yedekli yedeğinden oluşturulur.

Yeni sunucuyu mevcut sunucudan farklı bir kaynak grubunda oluşturmak için, aşağıdaki örnekte gösterildiği gibi **Resourcegroupname** parametresini kullanarak yeni kaynak grubu adını belirtin:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzPostgreSqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Cmdlet 'in **Georestore** parametre kümesi `Restore-AzPostgreSqlServer` aşağıdaki parametreleri gerektirir:

| Ayar | Önerilen değer | Açıklama  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | Yeni sunucunun ait olduğu kaynak grubunun adı.|
|Name | mydemoserver-geogeri yüklendi | Yeni sunucunun adı. |
|Konum | eastus | Yeni sunucunun konumu. |
|UseGeoRestore | `<SwitchParameter>` | Geri yüklemek için coğrafi mod kullanın. |

Coğrafi geri yükleme kullanarak yeni bir sunucu oluştururken, **SKU** parametresi belirtilmediği sürece kaynak sunucuyla aynı depolama boyutunu ve fiyatlandırma katmanını devralır.

Geri yükleme işlemi tamamlandıktan sonra, yeni sunucuyu bulun ve verilerin beklendiği gibi geri yüklendiğini doğrulayın. Yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan VNet hizmeti uç noktalarına sahip değildir. Bu kurallar, bu yeni sunucu için ayrı olarak ayarlanmalıdır. Özgün sunucudan gelen güvenlik duvarı kuralları geri yüklendi.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell ile PostgreSQL için Azure veritabanı bağlantı dizesi oluşturma](howto-connection-string-powershell.md)