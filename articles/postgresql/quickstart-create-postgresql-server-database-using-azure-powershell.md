---
title: 'Hızlı başlangıç: sunucu oluşturma-Azure PowerShell-PostgreSQL için Azure veritabanı-tek sunucu'
description: Azure PowerShell kullanarak bir PostgreSQL için Azure veritabanı oluşturmak için hızlı başlangıç kılavuzu-tek sunucu.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 79a070dbd49ed6ee00dd790c8157057aebde30df
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606749"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Hızlı başlangıç: PowerShell kullanarak tek sunuculu bir PostgreSQL için Azure veritabanı oluşturma

Bu hızlı başlangıçta, Azure Kaynak grubundaki PostgreSQL için Azure veritabanı sunucusu oluşturmak üzere PowerShell 'in nasıl kullanılacağı açıklanmaktadır. PowerShell 'i kullanarak Azure kaynaklarını etkileşimli olarak veya betikte oluşturabilir ve yönetebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az. PostgreSql PowerShell modülü önizlemedeyken, aşağıdaki komutu kullanarak az PowerShell modülünden ayrı olarak yüklemelisiniz: `Install-Module -Name Az.PostgreSql -AllowPrerelease` .
> Az. PostgreSql PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

İlk kez PostgreSQL için Azure veritabanı hizmetini kullanıyorsanız, **Microsoft. DBforPostgreSQL** kaynak sağlayıcısını kaydetmeniz gerekir.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
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

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL için Azure Veritabanı sunucusu oluşturma

Cmdlet 'i ile PostgreSQL için Azure veritabanı sunucusu oluşturun `New-AzPostgreSqlServer` . Bir sunucu birden çok veritabanını yönetebilir. Genellikle her proje veya kullanıcı için farklı bir veritabanı kullanılır.

Aşağıdaki tabloda, cmdlet 'inin yaygın olarak kullanılan parametrelerinin ve örnek değerlerinin bir listesi yer almaktadır `New-AzPostgreSqlServer` .

|        **Ayar**         | **Örnek değer** |                                                                                                                                                             **Açıklama**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Azure 'da PostgreSQL için Azure veritabanınızı tanımlayan genel olarak benzersiz bir ad seçin. Sunucu adı yalnızca harf, sayı ve kısa çizgi (-) karakterini içerebilir. Belirtilen tüm büyük karakterler, oluşturma işlemi sırasında otomatik olarak küçük harfe dönüştürülür. 3 ila 63 karakter arası içermelidir. |
| ResourceGroupName          | myresourcegroup  | Azure kaynak grubunun adını sağlayın.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | SKU'nun adı. Toplu olarak **fiyatlandırma katmanı \_ işlem oluşturma \_ sanal çekirdekleri** kuralına uyar. SKU parametresi hakkında daha fazla bilgi için bu tablodan sonraki bilgilere bakın.                                                                                                                                           |
| BackupRetentionDay         | 7                | Yedeklemenin ne kadar süreyle tutulacağı. Birim olarak gün kullanılır. 7-35 aralığındadır.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Etkin          | Coğrafi olarak yedekli yedeklemelerin bu sunucu için etkinleştirilip etkinleştirilmeyeceği. Bu değer, temel fiyatlandırma katmanındaki sunucular için etkinleştirilemez ve sunucu oluşturulduktan sonra değiştirilemez. İzin verilen değerler: Etkin, Devre Dışı.                                                                                                      |
| Konum                   | westus           | Sunucu için Azure bölgesi.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Etkin          | Bu sunucu için SSL 'nin etkinleştirilmesi gerekip gerekmediğini belirtir. İzin verilen değerler: Etkin, Devre Dışı.                                                                                                                                                                                                                                                 |
| Storageınmb                | 51200            | Sunucunun depolama kapasitesi (birim olan megabayt kullanılır). Geçerli Storageınmb değeri en az 5120 MB 'dir ve 1024 MB 'lik artışlarla artar. Depolama boyutu sınırları hakkında daha fazla bilgi için bkz. [PostgreSQL Için Azure veritabanı fiyatlandırma katmanları](./concepts-pricing-tiers.md).                                                                               |
| Sürüm                    | 9.6              | PostgreSQL ana sürümü.                                                                                                                                                                                                                                                                                                                 |
| Yönetici Kullanıcı adı      | myadmin          | Yöneticinin oturum açma kullanıcı adı. Şu değerler kullanılamaz: **azure_superuser**, **admin**, **administrator**, **root**, **guest** veya **public**.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Güvenli bir dize biçimindeki yönetici kullanıcının parolası. 8 ile 128 arasında karakter içermelidir. Parolanız şu üç kategoride yer alan karakterlerden oluşmalıdır: İngilizce büyük ve küçük harfler, sayılar ve alfasayısal olmayan karakterler.                                       |

**SKU** parametresi değeri, aşağıdaki örneklerde gösterildiği gibi **fiyatlandırma katmanı \_ işlem oluşturma \_ sanal çekirdeklerini** izler.

- `-Sku B_Gen5_1` Temel, Gen 5 ve 1 sanal çekirdekle eşlenir. Bu seçenek, kullanılabilen en küçük SKU ' dır.
- `-Sku GP_Gen5_32` Genel Amaçlı, Gen 5 ve 32 sanal çekirdekle eşleşir.
- `-Sku MO_Gen5_2` Bellek için iyileştirilmiş, Gen 5 ve 2 sanal çekirdekle eşleşir.

Bölgeye ve katmanlara göre geçerli **SKU** değerleri hakkında daha fazla bilgi için bkz. [PostgreSQL için Azure veritabanı fiyatlandırma katmanları](./concepts-pricing-tiers.md).

Aşağıdaki örnek, **myresourcegroup** kaynak grubundaki **demosunucum** adlı **Batı ABD** bölgesinde **myadmin** Sunucu Yöneticisi oturum açma bilgilerini içeren bir PostgreSQL sunucusu oluşturur. 2 sanal çekirdek ve coğrafi olarak yedekli yedeklemeler etkin olan genel amaçlı fiyatlandırma katmanında bir gen 5 sunucusudur. Örnek, PostgreSQL Sunucu Yöneticisi hesabının parolası olduğundan, örneğin ilk satırında kullanılan parolayı belgeleyin.

> [!TIP]
> Sunucu adı bir DNS adıyla eşleşir ve bunun Azure'da benzersiz olması gerekir.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Hafif işlem ve g/ç iş yükünüz için yeterli ise temel fiyatlandırma katmanını kullanmayı düşünün.

> [!IMPORTANT]
> Temel fiyatlandırma katmanında oluşturulan sunucular daha sonra genel amaçlı veya bellek için iyileştirilmiş olarak ölçeklendirilemez ve coğrafi olarak çoğaltılamaz.

## <a name="configure-a-firewall-rule"></a>Güvenlik duvarı kuralını yapılandırma

Cmdlet 'ini kullanarak PostgreSQL için Azure veritabanı sunucu düzeyinde güvenlik duvarı kuralı oluşturun `New-AzPostgreSqlFirewallRule` . Sunucu düzeyinde bir güvenlik duvarı kuralı, `psql` komut satırı aracı veya PostgreSQL çalışma ekranı gibi bir dış uygulamanın PostgreSQL Için Azure veritabanı hizmet güvenlik duvarı aracılığıyla sunucunuza bağlanmasını sağlar.

Aşağıdaki örnek, belirli bir IP adresinden (192.168.0.1) gelen bağlantılara izin veren **Allowmyıp** adlı bir güvenlik duvarı kuralı oluşturur. Bağlanmakta olduğunuz konuma karşılık gelen bir IP adresini veya IP adresleri aralığını değiştirin.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> PostgreSQL için Azure veritabanı bağlantıları 5432 numaralı bağlantı noktası üzerinden iletişim kurar. Bir kurumsal ağ içinden bağlanmaya çalışırsanız, 5432 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu senaryoda, yalnızca BT departmanınız 5432 numaralı bağlantı noktasını açarsa sunucuya bağlanabilirsiniz.

## <a name="get-the-connection-information"></a>Bağlantı bilgilerini alma

Sunucunuza bağlanmak için ana bilgisayar bilgilerini ve erişim kimlik bilgilerini sağlamanız gerekir. Bağlantı bilgilerini öğrenmek için aşağıdaki örneği kullanın. **Fullyıqualifieddomainname** ve **tınlogin** değerlerini bir yere göz önünde koyun.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgres.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>psql’yi kullanarak PostgreSQL veritabanına bağlanma

İstemci bilgisayarınızda PostgreSQL yüklüyse, [psql](https://www.postgresql.org/docs/current/static/app-psql.html)’nin yerel bir örneğini kullanarak Azure PostgreSQL sunucusuna bağlanabilirsiniz. Ayrıca, `psql` Bu makaledeki bir kod örneğinde **deneyin** düğmesini seçerek Azure Cloud Shell komut satırı aracının önceden yüklenmiş bir sürümüne erişebilirsiniz. Azure Cloud Shell erişmek için diğer yollar Azure portal sağ üst araç çubuğunda veya [Shell.Azure.com](https://shell.azure.com/)ziyaret ederek **>_** düğmesini seçmenizi sağlar.

1. Komut satırı yardımcı programını kullanarak Azure PostgreSQL sunucunuza bağlanın `psql` .

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Örneğin, aşağıdaki komut, erişim kimlik bilgilerini kullanarak PostgreSQL sunucunuzda **Postgres** adlı varsayılan veritabanına bağlanır `mydemoserver.postgres.database.azure.com` . Parola istendiğinde seçtiğiniz `<server_admin_password>` değerini girin.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Postgres 'e bağlanmak için bir URL yolu kullanmayı tercih ediyorsanız, URL, Kullanıcı adında @ işaretini ile kodlayın `%40` . Örneğin, psql için bağlantı dizesi, `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`

1. Sunucuya bağlandıktan sonra, istemde boş bir veritabanı oluşturun.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. Komut isteminde, bağlantıyı yeni oluşturulan **mypgsqldb** veritabanına geçirmek için aşağıdaki komutu yürütün:

   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>pgAdmin’i kullanarak PostgreSQL Sunucusu’na bağlanma

pgAdmin, PostgreSQL ile kullanılan açık kaynak bir araçtır. pgAdmin'i [pgAdmin web sitesinden](https://www.pgadmin.org/) yükleyebilirsiniz. Kullandığınız pgAdmin sürümü bu Hızlı Başlangıçta kullanılan sürümden farklı olabilir. Ek yönergeler gerekiyorsa pgAdmin belgelerini okuyun.

1. İstemci bilgisayarınızda pgAdmin uygulamasını açın.

1. Araç çubuğundan **Nesne**’ye gidin, **Oluştur** seçeneğinin üzerine gelip **Sunucu**’yu seçin.

1. **Oluştur - Sunucu** iletişim kutusunun **Genel** sekmesinde, sunucu için **mydemoserver** gibi benzersiz ve kolay bir ad girin.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="Genel sekmesi":::

1. **Oluştur - Sunucu** iletişim kutusunun **Bağlantı** sekmesinde ayarlar tablosunu doldurun.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="Bağlantı sekmesi":::

    pgAdmin parametresi |Değer|Açıklama
    ---|---|---
    Konak adı/adresi | Sunucu adı | PostgreSQL için Azure Veritabanı sunucusunu oluştururken kullandığınız sunucu adı değeri. Örnek sunucumuz: **mydemoserver.postgres.database.azure.com.** Örnekte gösterildiği gibi tam etki alanı adını (**\* . Postgres.Database.Azure.com**) kullanın. Sunucu adınızı anımsamıyorsanız bağlantı bilgilerini almak için bir önceki bölümdeki adımları izleyin.
    Bağlantı noktası | 5432 | PostgreSQL için Azure Veritabanı sunucusuna bağlanırken kullanılacak bağlantı noktası.
    Bakım veritabanı | *Postgres* | Sistem tarafından oluşturulan varsayılan veritabanı adı.
    Kullanıcı adı | Sunucu yöneticisi oturum açma adı | PostgreSQL için Azure Veritabanı sunucusunu oluştururken girdiğiniz sunucu yöneticisi oturum açma kullanıcı adı. Kullanıcı adını anımsamıyorsanız bağlantı bilgilerini almak için bir önceki bölümdeki adımları izleyin. Biçim *kullanıcıadı \@ sunucuadı*' dir.
    Parola | Yönetici parolanız | Bu Hızlı Başlangıçta daha önce sunucuyu oluştururken seçtiğiniz parola.
    Rol | Boş bırakın | Bu noktada bir rol adı sağlamanız gerekmez. Alanı boş bırakın.
    SSL modu | *Gerektirme* | PgAdmin 'in SSL sekmesinde TLS/SSL modunu ayarlayabilirsiniz. Varsayılan olarak, tüm PostgreSQL sunucuları için Azure veritabanı sunucuları, açık olan TLS ile oluşturulur. TLS zorlamayı devre dışı bırakmak için bkz. [TLS zorlamayı yapılandırma](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. **Kaydet**’i seçin.

1. Sol taraftaki **Tarayıcı** bölmesinde **Sunucular** düğümünü genişletin. Sunucunuzu (örneğin, **mydemoserver**) seçin. Bu sunucuya bağlanmak için tıklayın.

1. Sunucu düğümünü ve ardından onun altındaki **Veritabanları**’nı genişletin. Liste mevcut *postgres* veritabanınızı ve oluşturduğunuz diğer veritabanlarını içermelidir. PostgreSQL için Azure Veritabanı ile sunucu başına birden çok veritabanı oluşturabilirsiniz.

1. **Veritabanları**’na sağ tıklayın, **Oluştur** menüsünü seçin ve sonra da **Veritabanı**’nı seçin.

1. **Veritabanı** alanına, **mypgsqldb2** gibi tercih ettiğiniz veritabanı adını yazın.

1. Liste kutusundan veritabanı için **Sahip**’i seçin. Sunucu yöneticinizin oturum açma adını (örnekteki **my admin** gibi) seçin.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="PgAdmin 'te veritabanı oluşturma":::

1. Yeni ve boş bir veritabanı oluşturmak için **Kaydet**’i seçin.

1. **Tarayıcı** bölmesinde, oluşturduğunuz veritabanını sunucu adınızın altındaki veritabanları listesinde görebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta oluşturulan kaynaklar başka bir hızlı başlangıç veya öğretici için gerekmiyorsa, aşağıdaki örneği çalıştırarak bunları silebilirsiniz.

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu hızlı başlangıç kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Kaynak grubunu silmeden yalnızca bu hızlı başlangıçta oluşturulan sunucuyu silmek için `Remove-AzPostgreSqlServer` cmdlet 'ini kullanın.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [PowerShell kullanarak PostgreSQL için Azure veritabanı tasarlama](tutorial-design-database-using-powershell.md)