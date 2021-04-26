---
title: 'Öğretici: sunucu tasarlama-Azure PowerShell-MariaDB için Azure veritabanı'
description: Bu öğreticide, PowerShell kullanarak MariaDB sunucusu ve veritabanı için Azure veritabanı 'nın nasıl oluşturulacağı ve yönetileceği açıklanmaktadır.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664510"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>Öğretici: PowerShell kullanarak MariaDB için Azure veritabanı tasarlama

MariaDB için Azure veritabanı, Microsoft bulutundaki MariaDB Community Edition veritabanı altyapısını temel alan ilişkisel bir veritabanı hizmetidir. Bu öğreticide, aşağıdakileri nasıl yapacağınızı öğrenmek için PowerShell ve diğer yardımcı programları kullanacaksınız:

> [!div class="checklist"]
> - MariaDB için Azure Veritabanı oluşturma
> - Sunucu güvenlik duvarını yapılandırma
> - Veritabanı oluşturmak için [mysql komut satırı aracı](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) kullanma
> - Örnek verileri yükleme
> - Verileri sorgulama
> - Verileri güncelleştirme
> - Verileri geri yükleme

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az. MariaDb PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> Az. MariaDb PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

MariaDB hizmeti için Azure veritabanı 'nı ilk kez kullanıyorsanız, **Microsoft. Dbformarıdb** kaynak sağlayıcısını kaydetmeniz gerekir.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli BIR abonelik kimliği seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, **Batı ABD** bölgesinde **myresourcegroup** adlı bir kaynak grubu oluşturur.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>MariaDB için Azure Veritabanı sunucusu oluşturma

Cmdlet 'ini kullanarak bir MariaDB sunucusu için Azure veritabanı oluşturun `New-AzMariaDbServer` . Bir sunucu birden çok veritabanını yönetebilir. Genellikle her proje veya kullanıcı için farklı bir veritabanı kullanılır.

Aşağıdaki örnek, **myresourcegroup** kaynak grubundaki **demosunucum** adlı **Batı ABD** bölgesinde **myadmin** Sunucu Yöneticisi oturum açma bilgilerini içeren bir MariaDB sunucusu oluşturur. 2 sanal çekirdek ve coğrafi olarak yedekli yedeklemeler etkin olan genel amaçlı fiyatlandırma katmanında bir gen 5 sunucusudur. Örneğin, MariaDB Sunucu Yöneticisi hesabının parolası olduğundan, örneğin ilk satırında kullanılan parolayı belgeleyin.

> [!TIP]
> Sunucu adı bir DNS adıyla eşleşir ve bunun Azure'da benzersiz olması gerekir.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

**SKU** parametresi değeri, aşağıdaki örneklerde gösterildiği gibi **fiyatlandırma katmanı \_ işlem oluşturma \_ sanal çekirdeklerini** izler.

- `-Sku B_Gen5_1` Temel, Gen 5 ve 1 sanal çekirdekle eşlenir. Bu seçenek, kullanılabilen en küçük SKU ' dır.
- `-Sku GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
- `-Sku MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgeye ve katmanlara göre geçerli **SKU** değerleri hakkında daha fazla bilgi için bkz. [MariaDB için Azure veritabanı fiyatlandırma katmanları](./concepts-pricing-tiers.md).

Hafif işlem ve g/ç iş yükünüz için yeterli ise temel fiyatlandırma katmanını kullanmayı düşünün.

> [!IMPORTANT]
> Temel fiyatlandırma katmanında oluşturulan sunucular daha sonra genel amaçlı veya bellek için iyileştirilmiş olarak ölçeklendirilemez ve coğrafi olarak çoğaltılamaz.

## <a name="configure-a-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma

Cmdlet 'ini kullanarak MariaDB için Azure veritabanı sunucu düzeyinde güvenlik duvarı kuralı oluşturun `New-AzMariaDbFirewallRule` . Sunucu düzeyinde bir güvenlik duvarı kuralı, `mysql` komut satırı aracı veya MariaDB çalışma ekranı gibi bir dış uygulamanın, MariaDB hizmet güvenlik duvarı Için Azure veritabanı aracılığıyla sunucunuza bağlanmasını sağlar.

Aşağıdaki örnek, belirli bir IP adresinden (192.168.0.1) gelen bağlantılara izin veren **Allowmyıp** adlı bir güvenlik duvarı kuralı oluşturur. Bağlanmakta olduğunuz konuma karşılık gelen bir IP adresini veya IP adresleri aralığını değiştirin.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> MariaDB için Azure Veritabanı bağlantıları, 3306 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, 3306 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu senaryoda, yalnızca BT departmanınız 3306 numaralı bağlantı noktasını açarsa sunucuya bağlanabilirsiniz.

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir. Bağlantı bilgilerini öğrenmek için aşağıdaki örneği kullanın. **Fullyıqualifieddomainname** ve **tınlogin** değerlerini bir yere göz önünde koyun.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>MySQL komut satırı aracını kullanarak sunucuya bağlanma

Komut satırı aracını kullanarak sunucunuza bağlanın `mysql` . Komut satırı aracını indirip yüklemek için bkz. [MariaDB Community İndirmeleri](https://dev.mysql.com/downloads/shell/). Ayrıca, `mysql` Bu makaledeki bir kod örneğinde **deneyin** düğmesini seçerek Azure Cloud Shell komut satırı aracının önceden yüklenmiş bir sürümüne erişebilirsiniz. Azure Cloud Shell erişmek için diğer yollar Azure portal sağ üst araç çubuğunda veya [Shell.Azure.com](https://shell.azure.com/)ziyaret ederek **>_** düğmesini seçmenizi sağlar.

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Veritabanı oluşturma

Sunucuya bağlandıktan sonra, boş bir veritabanı oluşturun.

```sql
mysql> CREATE DATABASE mysampledb;
```

İstemde, bağlantıyı bu yeni oluşturulan veritabanına değiştirmek için şu komutu çalıştırın:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Veritabanında tablo oluşturma

Artık MariaDB veritabanı için Azure veritabanı 'na nasıl bağlanabildiğinizi öğreneceğimize göre bazı temel görevleri tamamlayabilirsiniz.

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

Sunucuyu önceki bir zaman noktasına geri yükleyebilirsiniz. Geri yüklenen veriler yeni bir sunucuya kopyalanır ve var olan sunucu değişmeden bırakılır. Örneğin, bir tablo yanlışlıkla bırakıldıysanız, yalnızca bırakma gerçekleştiği zamana geri yükleyebilirsiniz. Daha sonra, eksik tablo ve verileri, sunucunun geri yüklenen kopyasından elde edebilirsiniz.

Sunucuyu geri yüklemek için `Restore-AzMariaDbServer` PowerShell cmdlet 'ini kullanın.

### <a name="run-the-restore-command"></a>Restore komutunu çalıştırın

Sunucuyu geri yüklemek için PowerShell 'den aşağıdaki örneği çalıştırın.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Bir sunucuyu önceki bir zaman noktasına geri yüklediğinizde yeni bir sunucu oluşturulur. Özgün sunucu ve veritabanları belirtilen zaman noktasından yeni sunucuya kopyalanır.

Geri yüklenen sunucu için konum ve fiyatlandırma katmanı değerleri, özgün sunucu ile aynı kalır.

Geri yükleme işlemi tamamlandıktan sonra, yeni sunucuyu bulun ve verilerin beklendiği gibi geri yüklendiğini doğrulayın. Yeni sunucu, geri yükleme başlatıldığı sırada mevcut sunucu için geçerli olan Sunucu Yöneticisi oturum açma adı ve parolaya sahiptir. Parola, yeni sunucunun **genel bakış** sayfasından değiştirilebilir.

Geri yükleme sırasında oluşturulan yeni sunucu, özgün sunucuda var olan VNet hizmeti uç noktalarına sahip değildir. Bu kuralların yeni sunucu için ayrı olarak ayarlanması gerekir. Özgün sunucudan gelen güvenlik duvarı kuralları geri yüklendi.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak bir MariaDB sunucusu için Azure veritabanı 'nı yedekleme ve geri yükleme](howto-restore-server-powershell.md)