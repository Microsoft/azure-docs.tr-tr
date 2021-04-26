---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure PowerShell-MySQL için Azure veritabanı'
description: Bu hızlı başlangıç, PowerShell kullanarak bir Azure Kaynak grubunda MySQL için Azure veritabanı sunucusu oluşturma işlemini açıklar.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 04/28/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: d12d447acb3b6bf2b6f84e9768e9f063a9a36b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94542329"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak MySQL için Azure veritabanı sunucusu oluşturma

Bu hızlı başlangıç, PowerShell kullanarak bir Azure Kaynak grubunda MySQL için Azure veritabanı sunucusu oluşturma işlemini açıklar. PowerShell 'i kullanarak Azure kaynaklarını etkileşimli olarak veya betikte oluşturabilir ve yönetebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az. MySql PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.MySql -AllowPrerelease` .
> Az. MySql PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

MySQL için Azure veritabanı hizmetini ilk kez kullanıyorsanız, **Microsoft. Dbformyısql** kaynak sağlayıcısını kaydetmeniz gerekir.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMySQL
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

## <a name="create-an-azure-database-for-mysql-server"></a>MySQL için Azure Veritabanı sunucusu oluşturma

Cmdlet 'ini kullanarak MySQL için Azure veritabanı sunucusu oluşturun `New-AzMySqlServer` . Bir sunucu birden çok veritabanını yönetebilir. Genellikle her proje veya kullanıcı için farklı bir veritabanı kullanılır.

Aşağıdaki tabloda, cmdlet 'inin yaygın olarak kullanılan parametrelerinin ve örnek değerlerinin bir listesi yer almaktadır `New-AzMySqlServer` .

|        **Ayar**         | **Örnek değer** |                                                                                                                                                             **Açıklama**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Azure 'da MySQL sunucusu için Azure veritabanınızı tanımlayan genel olarak benzersiz bir ad seçin. Sunucu adı yalnızca harf, sayı ve kısa çizgi (-) karakterini içerebilir. Belirtilen tüm büyük karakterler, oluşturma işlemi sırasında otomatik olarak küçük harfe dönüştürülür. 3 ila 63 karakter arası içermelidir. |
| ResourceGroupName          | myresourcegroup  | Azure kaynak grubunun adını sağlayın.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | SKU'nun adı. Toplu olarak **fiyatlandırma katmanı \_ işlem oluşturma \_ sanal çekirdekleri** kuralına uyar. SKU parametresi hakkında daha fazla bilgi için bu tablodan sonraki bilgilere bakın.                                                                                                                                           |
| BackupRetentionDay         | 7                | Yedeklemenin ne kadar süreyle tutulacağı. Birim olarak gün kullanılır. 7-35 aralığındadır.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Etkin          | Coğrafi olarak yedekli yedeklemelerin bu sunucu için etkinleştirilip etkinleştirilmeyeceği. Bu değer, temel fiyatlandırma katmanındaki sunucular için etkinleştirilemez ve sunucu oluşturulduktan sonra değiştirilemez. İzin verilen değerler: Etkin, Devre Dışı.                                                                                                      |
| Konum                   | westus           | Sunucu için Azure bölgesi.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Etkin          | Bu sunucu için SSL 'nin etkinleştirilmesi gerekip gerekmediğini belirtir. İzin verilen değerler: Etkin, Devre Dışı.                                                                                                                                                                                                                                                 |
| Storageınmb                | 51200            | Sunucunun depolama kapasitesi (birim olan megabayt kullanılır). Geçerli Storageınmb değeri en az 5120 MB 'dir ve 1024 MB 'lik artışlarla artar. Depolama boyutu sınırları hakkında daha fazla bilgi için bkz. [MySQL Için Azure veritabanı fiyatlandırma katmanları](./concepts-pricing-tiers.md).                                                                               |
| Sürüm                    | 5.7              | MySQL ana sürümü.                                                                                                                                                                                                                                                                                                                 |
| Yönetici Kullanıcı adı      | myadmin          | Yöneticinin oturum açma kullanıcı adı. Şu değerler kullanılamaz: **azure_superuser**, **admin**, **administrator**, **root**, **guest** veya **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Güvenli bir dize biçimindeki yönetici kullanıcının parolası. 8 ile 128 arasında karakter içermelidir. Parolanız şu üç kategoride yer alan karakterlerden oluşmalıdır: İngilizce büyük ve küçük harfler, sayılar ve alfasayısal olmayan karakterler.                                       |

**SKU** parametresi değeri, aşağıdaki örneklerde gösterildiği gibi **fiyatlandırma katmanı \_ işlem oluşturma \_ sanal çekirdeklerini** izler.

- `-Sku B_Gen5_1` Temel, Gen 5 ve 1 sanal çekirdekle eşlenir. Bu seçenek, kullanılabilen en küçük SKU ' dır.
- `-Sku GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
- `-Sku MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgeye ve katmanlara göre geçerli **SKU** değerleri hakkında daha fazla bilgi için bkz. [MySQL için Azure veritabanı fiyatlandırma katmanları](./concepts-pricing-tiers.md).

Aşağıdaki örnek, **myresourcegroup** kaynak grubundaki **demosunucum** adlı **Batı ABD** bölgesinde **myadmin** Sunucu Yöneticisi oturum açma bilgilerini içeren bir MySQL sunucusu oluşturur. 2 sanal çekirdek ve coğrafi olarak yedekli yedeklemeler etkin olan genel amaçlı fiyatlandırma katmanında bir gen 5 sunucusudur. Örnek, MySQL Server yönetici hesabının parolası olduğundan, örneğin ilk satırında kullanılan parolayı belgeleyin.

> [!TIP]
> Sunucu adı bir DNS adıyla eşleşir ve bunun Azure'da benzersiz olması gerekir.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Hafif işlem ve g/ç iş yükünüz için yeterli ise temel fiyatlandırma katmanını kullanmayı düşünün.

> [!IMPORTANT]
> Temel fiyatlandırma katmanında oluşturulan sunucular daha sonra genel amaçlı veya bellek için iyileştirilmiş olarak ölçeklendirilemez ve coğrafi olarak çoğaltılamaz.

## <a name="configure-a-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma

Cmdlet 'ini kullanarak MySQL için Azure veritabanı sunucu düzeyinde güvenlik duvarı kuralı oluşturun `New-AzMySqlFirewallRule` . Sunucu düzeyinde bir güvenlik duvarı kuralı, `mysql` komut satırı aracı veya MySQL çalışma ekranı gibi bir dış uygulamanın, MySQL Için Azure veritabanı hizmeti güvenlik duvarı aracılığıyla sunucunuza bağlanmasını sağlar.

Aşağıdaki örnek, belirli bir IP adresinden (192.168.0.1) gelen bağlantılara izin veren **Allowmyıp** adlı bir güvenlik duvarı kuralı oluşturur. Bağlanmakta olduğunuz konuma karşılık gelen bir IP adresini veya IP adresleri aralığını değiştirin.

```azurepowershell-interactive
New-AzMySqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> MySQL için Azure Veritabanı bağlantıları 3306 bağlantı noktası üzerinden iletişim kurar. Kurumsal ağ içinden bağlanmaya çalışıyorsanız, 3306 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu senaryoda, yalnızca BT departmanınız 3306 numaralı bağlantı noktasını açarsa sunucuya bağlanabilirsiniz.

## <a name="configure-ssl-settings"></a>SSL ayarlarını yapılandırma

Varsayılan olarak sunucunuz ile istemci uygulamaları arasında SSL bağlantıları zorunlu tutulur. Bu varsayılan değer, Internet üzerinden veri akışını şifreleyerek _hareket içi_ verilerin güvenliğini sağlar. Bu hızlı başlangıç için sunucunuzda SSL bağlantılarını devre dışı bırakın. Daha ayrıntılı bilgi için bkz. [MySQL için Azure Veritabanı'na güvenli bir şekilde bağlanmak üzere uygulamanızda SSL bağlantısını yapılandırma](./howto-configure-ssl.md).

> [!WARNING]
> Üretim sunucuları için SSL’in devre dışı bırakılması önerilmez.

Aşağıdaki örnek, MySQL için Azure veritabanı sunucunuzda SSL 'yi devre dışı bırakır.

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -SslEnforcement Disabled
```

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir. Bağlantı bilgilerini öğrenmek için aşağıdaki örneği kullanın. **Fullyıqualifieddomainname** ve **tınlogin** değerlerini bir yere göz önünde koyun.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mysql.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>MySQL komut satırı aracını kullanarak sunucuya bağlanma

Komut satırı aracını kullanarak sunucunuza bağlanın `mysql` . Komut satırı aracını indirip yüklemek için bkz. [MySQL Community İndirmeleri](https://dev.mysql.com/downloads/shell/). Ayrıca, `mysql` Bu makaledeki bir kod örneğinde **deneyin** düğmesini seçerek Azure Cloud Shell komut satırı aracının önceden yüklenmiş bir sürümüne erişebilirsiniz. Azure Cloud Shell erişmek için diğer yollar Azure portal sağ üst araç çubuğunda veya [Shell.Azure.com](https://shell.azure.com/)ziyaret ederek **>_** düğmesini seçmenizi sağlar.

1. Komut satırı aracını kullanarak sunucuya bağlanın `mysql` .

   ```azurepowershell-interactive
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

1. Sunucu durumunu görüntüleyin.

   ```sql
   mysql> status
   ```

    ```Output
    C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
    Enter password: *************
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65512
    Server version: 5.6.42.0 MySQL Community Server (GPL)

    Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> status
    --------------
    mysql  Ver 14.14 Distrib 5.7.29, for Win64 (x86_64)

    Connection id:          65512
    Current database:
    Current user:           myadmin@myipaddress
    SSL:                    Not in use
    Using delimiter:        ;
    Server version:         5.6.42.0 MySQL Community Server (GPL)
    Protocol version:       10
    Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
    Server characterset:    latin1
    Db     characterset:    latin1
    Client characterset:    utf8
    Conn.  characterset:    utf8
    TCP port:               3306
    Uptime:                 1 hour 2 min 12 sec

    Threads: 7  Questions: 952  Slow queries: 0  Opens: 66  Flush tables: 3  Open tables: 16  Queries per second avg: 0.255
    --------------

    mysql>
    ```

Ek komutlar için bkz. [MySQL 5.7 Başvuru Kılavuzu - Bölüm 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-mysql-workbench"></a>MySQL çalışma ekranı kullanarak sunucuya bağlanma

1. İstemci bilgisayarınızda MySQL Workbench uygulamasını başlatın. MySQL çalışma ekranı 'nı indirip yüklemek için bkz. [MySQL çalışma ekranı 'Nı indirme](https://dev.mysql.com/downloads/workbench/).

1. **Setup New Connection** (Yeni Bağlantı Oluştur) iletişim kutusundaki **Parameters** (Parametreler) sekmesine aşağıdaki bilgileri girin:

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-powershell/setup-new-connection.png" alt-text="yeni bağlantı oluştur":::

    |    **Ayar**    |           **Önerilen değer**           |                      **Açıklama**                       |
    | ----------------- | --------------------------------------- | ---------------------------------------------------------- |
    | Bağlantı Adı   | Bağlantım                           | Bu bağlantı için bir etiket belirtin                        |
    | Bağlantı Yöntemi | Standart (TCP/IP)                       | MySQL için Azure veritabanı 'na bağlanmak için TCP/IP protokolünü kullanma |
    | Konak adı          | `mydemoserver.mysql.database.azure.com` | Daha önce not ettiğiniz sunucu adı                           |
    | Bağlantı noktası              | 3306                                    | MySQL için varsayılan bağlantı noktası                                 |
    | Kullanıcı adı          | myadmin@mydemoserver                    | Daha önce not ettiğiniz Sunucu Yöneticisi oturum açma bilgileri                |
    | Parola          | *************                           | Daha önce yapılandırdığınız Yönetici hesabı parolasını kullanın      |

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

Kaynak grubunu silmeden yalnızca bu hızlı başlangıçta oluşturulan sunucuyu silmek için `Remove-AzMySqlServer` cmdlet 'ini kullanın.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak MySQL için Azure veritabanı tasarlama](tutorial-design-database-using-powershell.md)