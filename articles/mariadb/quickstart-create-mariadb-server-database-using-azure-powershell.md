---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure PowerShell-MariaDB için Azure veritabanı'
description: Bu hızlı başlangıçta PowerShell kullanarak bir Azure Kaynak grubunda MariaDB sunucusu için Azure veritabanı oluşturma işlemini açıklanmaktadır.
author: savjani
ms.author: pariks
ms.date: 05/26/2020
ms.topic: quickstart
ms.service: mariadb
ms.devlang: azurepowershell
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 7bfbd223cb337ff7d908032c769afbbf5cafa38f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531387"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak MariaDB sunucusu için Azure veritabanı oluşturma

Bu hızlı başlangıçta PowerShell kullanarak bir Azure Kaynak grubunda MariaDB sunucusu için Azure veritabanı oluşturma işlemini açıklanmaktadır. PowerShell 'i kullanarak Azure kaynaklarını etkileşimli olarak veya betikte oluşturabilir ve yönetebilirsiniz.

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

Aşağıdaki tabloda, cmdlet 'inin yaygın olarak kullanılan parametrelerinin ve örnek değerlerinin bir listesi yer almaktadır `New-AzMariaDbServer` .

|        **Ayar**         | **Örnek değer** |                                                                                                                                                             **Açıklama**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Azure 'da MariaDB sunucusu için Azure veritabanınızı tanımlayan genel olarak benzersiz bir ad seçin. Sunucu adı yalnızca harf, sayı ve kısa çizgi (-) karakterini içerebilir. Belirtilen tüm büyük karakterler, oluşturma işlemi sırasında otomatik olarak küçük harfe dönüştürülür. 3 ila 63 karakter arası içermelidir. |
| ResourceGroupName          | myresourcegroup  | Azure kaynak grubunun adını sağlayın.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | SKU'nun adı. Toplu olarak **fiyatlandırma katmanı \_ işlem oluşturma \_ sanal çekirdekleri** kuralına uyar. SKU parametresi hakkında daha fazla bilgi için bu tablodan sonraki bilgilere bakın.                                                                                                                                           |
| BackupRetentionDay         | 7                | Yedeklemenin ne kadar süreyle tutulacağı. Birim olarak gün kullanılır. 7-35 aralığındadır.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Etkin          | Coğrafi olarak yedekli yedeklemelerin bu sunucu için etkinleştirilip etkinleştirilmeyeceği. Bu değer, temel fiyatlandırma katmanındaki sunucular için etkinleştirilemez ve sunucu oluşturulduktan sonra değiştirilemez. İzin verilen değerler: Etkin, Devre Dışı.                                                                                                      |
| Konum                   | westus           | Sunucu için Azure bölgesi.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Etkin          | Bu sunucu için SSL 'nin etkinleştirilmesi gerekip gerekmediğini belirtir. İzin verilen değerler: Etkin, Devre Dışı.                                                                                                                                                                                                                                                 |
| Storageınmb                | 51200            | Sunucunun depolama kapasitesi (birim olan megabayt kullanılır). Geçerli Storageınmb değeri en az 5120 MB 'dir ve 1024 MB 'lik artışlarla artar. Depolama boyutu sınırları hakkında daha fazla bilgi için bkz. [MariaDB Için Azure veritabanı fiyatlandırma katmanları](./concepts-pricing-tiers.md).                                                                               |
| Sürüm                    | 5.7              | MariaDB ana sürümü.                                                                                                                                                                                                                                                                                                                 |
| Yönetici Kullanıcı adı      | myadmin          | Yöneticinin oturum açma kullanıcı adı. Şu değerler kullanılamaz: **azure_superuser**, **admin**, **administrator**, **root**, **guest** veya **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Güvenli bir dize biçimindeki yönetici kullanıcının parolası. 8 ile 128 arasında karakter içermelidir. Parolanız şu üç kategoride yer alan karakterlerden oluşmalıdır: İngilizce büyük ve küçük harfler, sayılar ve alfasayısal olmayan karakterler.                                       |

**SKU** parametresi değeri, aşağıdaki örneklerde gösterildiği gibi **fiyatlandırma katmanı \_ işlem oluşturma \_ sanal çekirdeklerini** izler.

- `-Sku B_Gen5_1` Temel, Gen 5 ve 1 sanal çekirdekle eşlenir. Bu seçenek, kullanılabilen en küçük SKU ' dır.
- `-Sku GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
- `-Sku MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgeye ve katmanlara göre geçerli **SKU** değerleri hakkında daha fazla bilgi için bkz. [MariaDB için Azure veritabanı fiyatlandırma katmanları](./concepts-pricing-tiers.md).

Aşağıdaki örnek, **myresourcegroup** kaynak grubundaki **demosunucum** adlı **Batı ABD** bölgesinde **myadmin** Sunucu Yöneticisi oturum açma bilgilerini içeren bir MariaDB sunucusu oluşturur. 2 sanal çekirdek ve coğrafi olarak yedekli yedeklemeler etkin olan genel amaçlı fiyatlandırma katmanında bir gen 5 sunucusudur. Örneğin, MariaDB Sunucu Yöneticisi hesabının parolası olduğundan, örneğin ilk satırında kullanılan parolayı belgeleyin.

> [!TIP]
> Sunucu adı bir DNS adıyla eşleşir ve bunun Azure'da benzersiz olması gerekir.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

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

## <a name="configure-ssl-settings"></a>SSL ayarlarını yapılandırma

Varsayılan olarak sunucunuz ile istemci uygulamaları arasında SSL bağlantıları zorunlu tutulur. Bu varsayılan değer, Internet üzerinden veri akışını şifreleyerek _hareket içi_ verilerin güvenliğini sağlar. Bu hızlı başlangıç için sunucunuzda SSL bağlantılarını devre dışı bırakın. Daha ayrıntılı bilgi için bkz. [MariaDB için Azure Veritabanı'na güvenli bir şekilde bağlanmak üzere uygulamanızda SSL bağlantısını yapılandırma](./howto-configure-ssl.md).

> [!WARNING]
> Üretim sunucuları için SSL’in devre dışı bırakılması önerilmez.

Aşağıdaki örnek, MariaDB sunucusu için Azure veritabanınızda SSL 'yi devre dışı bırakır.

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

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

Komut satırı aracını kullanarak sunucunuza bağlanın `mysql` . Komut satırı aracını indirip yüklemek için bkz. [MySQL Community İndirmeleri](https://dev.mysql.com/downloads/shell/). Ayrıca, `mysql` Bu makaledeki bir kod örneğinde **deneyin** düğmesini seçerek Azure Cloud Shell komut satırı aracının önceden yüklenmiş bir sürümüne erişebilirsiniz. Azure Cloud Shell erişmek için diğer yollar Azure portal sağ üst araç çubuğunda veya [Shell.Azure.com](https://shell.azure.com/)ziyaret ederek **>_** düğmesini seçmenizi sağlar.

1. Komut satırı aracını kullanarak sunucuya bağlanın `mysql` .

   ```azurepowershell-interactive
   mysql -h <servername>.mariadb.database.azure.com -u myadmin@<servername> -p
   ```

1. Sunucu durumunu görüntüleyin.

   ```sql
   mysql> status
   ```

   ```Output
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: *************
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 64793
   Server version: 5.6.42.0 MariaDB Server

   Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   /usr/bin/mysql  Ver 14.14 Distrib 5.7.29, for Linux (x86_64) using  EditLine wrapper

   Connection id:          64793
   Current database:
   Current user:           myadmin@myipaddress
   SSL:                    Cipher in use is ECDHE-RSA-AES256-GCM-SHA384
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.42.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 17 min 4 sec

   Threads: 19  Questions: 482  Slow queries: 0  Opens: 50  Flush tables: 3  Open tables: 12  Queries per second avg: 0.470
   --------------

   mysql>
   ```

Ek komutlar için bkz. [MySQL 5.7 Başvuru Kılavuzu - Bölüm 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mariadb-workbench"></a>MariaDB çalışma ekranı kullanarak sunucuya bağlanma

1. İstemci bilgisayarınızda MySQL Workbench uygulamasını başlatın. MySQL çalışma ekranı 'nı indirip yüklemek için bkz. [MySQL çalışma ekranı 'Nı indirme](https://dev.mysql.com/downloads/workbench/).

1. **Setup New Connection** (Yeni Bağlantı Oluştur) iletişim kutusundaki **Parameters** (Parametreler) sekmesine aşağıdaki bilgileri girin:

   ![yeni bağlantı oluştur](./media/quickstart-create-mariadb-server-database-using-azure-powershell/setup-new-connection.png)

    |    **Ayar**    |            **Önerilen değer**            |                      **Açıklama**                       |
    | ----------------- | ----------------------------------------- | ---------------------------------------------------------- |
    | Bağlantı Adı   | Bağlantım                             | Bu bağlantı için bir etiket belirtin                        |
    | Bağlantı Yöntemi | Standart (TCP/IP)                         | MariaDB için Azure Veritabanı'na bağlanmak için TCP/IP protokolünü kullanın |
    | Konak adı          | `mydemoserver.mariadb.database.azure.com` | Daha önce not ettiğiniz sunucu adı                           |
    | Bağlantı noktası              | 3306                                      | MariaDB için varsayılan bağlantı noktası                                 |
    | Kullanıcı adı          | myadmin@mydemoserver                      | Daha önce not ettiğiniz Sunucu Yöneticisi oturum açma bilgileri                |
    | Parola          | *************                             | Daha önce yapılandırdığınız Yönetici hesabı parolasını kullanın      |

1. Parametrelerin doğru yapılandırılıp yapılandırılmadığını test etmek için **Bağlantıyı Sına** düğmesine tıklayın.

1. Sunucuya bağlanmak için bağlantıyı seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturulan kaynaklar başka bir hızlı başlangıç veya öğretici için gerekmiyorsa, aşağıdaki örneği çalıştırarak bunları silebilirsiniz.

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu hızlı başlangıç kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Kaynak grubunu silmeden yalnızca bu hızlı başlangıçta oluşturulan sunucuyu silmek için `Remove-AzMariaDbServer` cmdlet 'ini kullanın.

```azurepowershell-interactive
Remove-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak MariaDB için Azure veritabanı tasarlama](tutorial-design-database-using-powershell.md)
