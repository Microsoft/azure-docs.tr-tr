---
title: IP güvenlik duvarı kuralları
description: Bir SQL veritabanı veya SQL veri ambarı güvenlik duvarı için sunucu düzeyi IP güvenlik duvarı kurallarını yapılandırın. Tek veya havuza alınmış bir veritabanı için erişimi yönetin ve veritabanı düzeyinde IP güvenlik duvarı kurallarını yapılandırın.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 668744121c41a6e4797bc335b2736c8b31d87a41
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807941"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL veritabanı ve Azure SQL veri ambarı IP güvenlik duvarı kuralları

> [!NOTE]
> Bu makale, Azure SQL sunucuları ve Azure SQL veritabanı ve Azure SQL veri ambarı veritabanları için Azure SQL Server 'da geçerlidir. Basitlik için SQL *veritabanı* hem SQL veritabanı hem de SQL veri ambarı 'na başvurmak için kullanılır.

> [!IMPORTANT]
> Bu *Makale,* *Azure SQL veritabanı yönetilen örneği*için geçerlidir. Ağ yapılandırması hakkında daha fazla bilgi için bkz. [uygulamanızı Azure SQL veritabanı yönetilen örneğine bağlama](sql-database-managed-instance-connect-app.md).

Örneğin, *te yapılandırılmış MySQLServer*adlı yeni bir Azure SQL Server oluşturduğunuzda, SQL veritabanı güvenlik duvarı sunucu için genel uç noktaya ( *MySQLServer.Database.Windows.net*adresinden erişilebilen) tüm erişimi engeller.

> [!IMPORTANT]
> SQL veri ambarı yalnızca sunucu düzeyinde IP güvenlik duvarı kurallarını destekler. Veritabanı düzeyinde IP güvenlik duvarı kurallarını desteklemez.

## <a name="how-the-firewall-works"></a>Güvenlik duvarının çalışma biçimi
Aşağıdaki diyagramda gösterildiği gibi, internet 'ten ve Azure 'dan gelen bağlantı girişimleri, SQL Server veya SQL veritabanınıza erişmeden önce güvenlik duvarından geçmelidir.

   ![Güvenlik Duvarı yapılandırma diyagramı][1]

### <a name="server-level-ip-firewall-rules"></a>Sunucu düzeyinde IP güvenlik duvarı kuralları

  Bu kurallar, istemcilerin tüm Azure SQL Server 'a, yani aynı SQL veritabanı sunucusundaki tüm veritabanlarına erişmesini sağlar. Kurallar *ana* veritabanında depolanır. Azure SQL Server için en fazla 128 sunucu düzeyi IP güvenlik duvarı kuralına sahip olabilirsiniz.
  
  Azure portal, PowerShell veya Transact-SQL deyimlerini kullanarak sunucu düzeyi IP güvenlik duvarı kurallarını yapılandırabilirsiniz.
  - Portalı veya PowerShell 'i kullanmak için abonelik sahibi veya abonelik katılımcısı olmanız gerekir.
  - Transact-SQL kullanmak için SQL veritabanı örneğine sunucu düzeyi asıl oturum açma veya Azure Active Directory yöneticisi olarak bağlanmanız gerekir. (Sunucu düzeyi IP güvenlik duvarı kuralı önce Azure düzeyinde izinlere sahip olan bir kullanıcı tarafından oluşturulmalıdır.)

### <a name="database-level-ip-firewall-rules"></a>Veritabanı düzeyinde IP güvenlik duvarı kuralları

  Bu kurallar istemcilerin aynı SQL veritabanı sunucusu içindeki belirli (güvenli) veritabanlarına erişmesini sağlar. Her veritabanı için ( *ana* veritabanı dahil) kuralları oluşturur ve tek veritabanında depolanır.
  
  Yalnızca Transact-SQL deyimlerini kullanarak ve yalnızca ilk sunucu düzeyinde güvenlik duvarını yapılandırdıktan sonra, ana ve kullanıcı veritabanları için veritabanı düzeyinde IP güvenlik duvarı kuralları oluşturabilir ve yönetebilirsiniz.
  
  Veritabanı düzeyi IP güvenlik duvarı kuralında sunucu düzeyi IP güvenlik duvarı kuralında aralığın dışında bir IP adresi aralığı belirtirseniz, yalnızca veritabanı düzeyinde IP adresi olan istemciler veritabanına erişebilir.
  
  Bir veritabanı için en fazla 128 veritabanı düzeyinde IP güvenlik duvarı kuralına sahip olabilirsiniz. Veritabanı düzeyinde IP güvenlik duvarı kurallarını yapılandırma hakkında daha fazla bilgi için, bu makalenin devamındaki örneğe bakın ve [sp_set_database_firewall_rule (Azure SQL veritabanı)](https://msdn.microsoft.com/library/dn270010.aspx)bölümüne bakın.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Güvenlik duvarı kuralları ayarlama önerileri

Mümkün olduğunda veritabanı düzeyinde IP güvenlik duvarı kuralları kullanmanızı öneririz. Bu uygulama güvenliği geliştirir ve veritabanınızı daha taşınabilir hale getirir. Yöneticiler için sunucu düzeyi IP güvenlik duvarı kurallarını kullanın. Ayrıca, aynı erişim gereksinimlerine sahip birçok veritabanınız varsa ve her veritabanını ayrı ayrı yapılandırmak istemiyorsanız bunları kullanın.

> [!NOTE]
> İş sürekliliği bağlamında taşınabilir veritabanları hakkında bilgi edinmek için bkz. [Olağanüstü durum kurtarma için kimlik doğrulama gereksinimleri](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Sunucu düzeyinde ve veritabanı düzeyinde IP güvenlik duvarı kurallarına karşılık

*Tek bir veritabanının kullanıcıları başka bir veritabanından tamamen yalıtılmalıdır mi?*

Yanıt *Evet*ise, erişim izni vermek için VERITABANı düzeyinde IP güvenlik duvarı kurallarını kullanın. Bu yöntem, tüm veritabanlarına güvenlik duvarı üzerinden erişime izin veren sunucu düzeyi IP güvenlik duvarı kurallarını kullanmaktan kaçınır. Bu, savunlarınızın derinliğini azaltır.

*IP adreslerindeki kullanıcıların tüm veritabanlarına erişmesi gerekiyor mu?*

Yanıt *Evet*Ise, IP güvenlik duvarı kurallarını yapılandırmak istediğiniz zaman sayısını azaltmak için sunucu düzeyi IP güvenlik duvarı kurallarını kullanın.

*IP güvenlik duvarı kurallarını yapılandıran kişi veya takım yalnızca Azure portal, PowerShell veya REST API erişim sahibi olabilir mi?*

Bu durumda, sunucu düzeyinde IP güvenlik duvarı kuralları kullanmanız gerekir. Veritabanı düzeyinde IP güvenlik duvarı kuralları yalnızca Transact-SQL üzerinden yapılandırılabilir.  

*Veritabanı düzeyinde yüksek düzeyde izni olmayan IP güvenlik duvarı kurallarını yapılandıran kişi veya ekip mu?*

Bu durumda, sunucu düzeyi IP güvenlik duvarı kurallarını kullanın. Veritabanı düzeyinde, veritabanı düzeyinde IP güvenlik duvarı kurallarını Transact-SQL aracılığıyla yapılandırmak için veritabanı düzeyinde en az *DENETIM veritabanı* iznine sahip olmanız gerekir.  

*IP güvenlik duvarı kurallarını yapılandıran veya denetleyen kişi veya ekip, çok sayıda (belki yüzlerce) veritabanı için IP güvenlik duvarı kurallarını merkezi olarak yönetebilir mi?*

Bu senaryoda, en iyi yöntemler gereksinimlerinize ve ortamınıza göre belirlenir. Sunucu düzeyi IP Güvenlik Duvarı kurallarının yapılandırılması daha kolay olabilir, ancak komut dosyası, kuralları veritabanı düzeyinde yapılandırabilir. Sunucu düzeyinde IP güvenlik duvarı kuralları kullanıyor olsanız bile, veritabanında *Denetim* iznine sahip kullanıcıların VERITABANı düzeyinde IP güvenlik duvarı kuralları oluşturma izni olup olmadığını görmek için VERITABANı düzeyinde IP güvenlik duvarı kurallarını denetlemeniz gerekebilir.

*Sunucu düzeyinde ve veritabanı düzeyinde IP Güvenlik Duvarı kurallarının bir karışımını kullanabilir miyim?*

Evet. Yöneticiler gibi bazı kullanıcıların sunucu düzeyinde IP güvenlik duvarı kuralları olması gerekebilir. Bir veritabanı uygulamasının kullanıcıları gibi diğer kullanıcıların veritabanı düzeyinde IP güvenlik duvarı kuralları olması gerekebilir.

### <a name="connections-from-the-internet"></a>İnternet bağlantısı

Bir bilgisayar Internet 'ten veritabanı sunucunuza bağlanmaya çalıştığında, güvenlik duvarı ilk olarak isteğin kaynak IP adresini, bağlantının istek yaptığı veritabanının veritabanı düzeyinde IP güvenlik duvarı kurallarına karşı denetler.

- Adres, veritabanı düzeyinde IP güvenlik duvarı kurallarında belirtilen bir Aralık içindeyse, kuralı içeren SQL veritabanına bağlantı verilir.
- Adres, veritabanı düzeyinde IP güvenlik duvarı kurallarında bir Aralık içinde değilse, güvenlik duvarı sunucu düzeyi IP güvenlik duvarı kurallarını denetler. Adres, sunucu düzeyinde IP güvenlik duvarı kurallarında bulunan bir Aralık içindeyse, bağlantı verilir. Sunucu düzeyinde IP güvenlik duvarı kuralları, Azure SQL Server 'daki tüm SQL veritabanları için geçerlidir.  
- Adres, veritabanı düzeyinde veya sunucu düzeyinde IP güvenlik duvarı kurallarındaki bir Aralık içinde değilse, bağlantı isteği başarısız olur.

> [!NOTE]
> Yerel bilgisayarınızdan SQL veritabanına erişmek için, ağınızdaki ve yerel bilgisayarınızdaki güvenlik duvarının TCP bağlantı noktası 1433 ' de giden iletişime izin verildiğinden emin olun.

### <a name="connections-from-inside-azure"></a>Azure içinden bağlantılar

Azure içinde barındırılan uygulamaların SQL sunucunuza bağlanmasına izin vermek için Azure bağlantıları etkinleştirilmelidir. Azure 'dan bir uygulama, veritabanı sunucunuza bağlanmaya çalıştığında, güvenlik duvarı Azure bağlantılarına izin verildiğini doğrular. *0.0.0.0* DEĞERINE eşit IP adreslerini başlatma ve bitiş bir güvenlik duvarı ayarı, Azure bağlantılarına izin verildiğini gösterir. Bağlantıya izin verilmiyorsa, istek SQL veritabanı sunucusuna erişmez.

> [!IMPORTANT]
> Bu seçenek, güvenlik duvarını diğer müşterilerin aboneliklerinden gelen bağlantılar da dahil olmak üzere Azure 'daki tüm bağlantılara izin verecek şekilde yapılandırır. Bu seçeneği belirlerseniz, oturum açma ve kullanıcı izinlerinizin yalnızca yetkili kullanıcılara erişimi sınırlandırdığınızdan emin olun.

## <a name="create-and-manage-ip-firewall-rules"></a>IP güvenlik duvarı kuralları oluşturma ve yönetme

[Azure Portal](https://portal.azure.com/) veya program aracılığıyla [Azure POWERSHELL](https://docs.microsoft.com/powershell/module/az.sql), [azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)veya bir Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)kullanarak ilk sunucu düzeyi güvenlik duvarı ayarını oluşturursunuz. Bu yöntemleri veya Transact-SQL ' i kullanarak diğer sunucu düzeyi IP güvenlik duvarı kuralları oluşturur ve yönetirsiniz.

> [!IMPORTANT]
> Veritabanı düzeyi IP güvenlik duvarı kuralları yalnızca Transact-SQL kullanılarak oluşturulabilir ve yönetilebilir.

Performansı artırmak için sunucu düzeyi IP güvenlik duvarı kuralları veritabanı düzeyinde geçici olarak önbelleğe alınır. Önbelleği yenilemek için bkz. [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı değişikliklerini denetlemek için [SQL veritabanı denetimini](sql-database-auditing.md) kullanabilirsiniz.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyi IP güvenlik duvarı kurallarını yönetmek için Azure portal kullanma

Azure portal sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlamak için Azure SQL veritabanınızın veya SQL veritabanı sunucunuzun genel bakış sayfasına gidin.

> [!TIP]
> Öğretici için, bkz. [Azure Portal kullanarak veritabanı oluşturma](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>Veritabanına genel bakış sayfasından

1. Veritabanı genel bakış sayfasından sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlamak için, aşağıdaki görüntüde gösterildiği gibi araç çubuğunda **sunucu güvenlik duvarını ayarla** ' yı seçin. SQL Veritabanı sunucusu için **Güvenlik duvarı ayarları** sayfası açılır.

      ![Sunucu IP güvenlik duvarı kuralı](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Kullanmakta olduğunuz bilgisayarın IP adresini eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin ve ardından **Kaydet**' i seçin. Geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

      ![Sunucu düzeyi IP güvenlik duvarı kuralını ayarla](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

#### <a name="from-the-server-overview-page"></a>Sunucuya genel bakış sayfasından

Sunucunuzun genel bakış sayfası açılır. Tam sunucu adını (örneğin, *mynewserver20170403.Database.Windows.net*) gösterir ve daha fazla yapılandırma seçeneği sunar.

1. Bu sayfadan bir sunucu düzeyi kuralı ayarlamak için, sol taraftaki **Ayarlar** menüsünden **güvenlik duvarı** ' nı seçin.

2. Kullanmakta olduğunuz bilgisayarın IP adresini eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin ve ardından **Kaydet**' i seçin. Geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>IP güvenlik duvarı kurallarını yönetmek için Transact-SQL kullanma

| Katalog görünümü veya saklı yordam | Düzey | Açıklama |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Sunucu |Geçerli sunucu düzeyi IP güvenlik duvarı kurallarını görüntüler |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kuralları oluşturur veya güncelleştirir |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kurallarını kaldırır |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |Geçerli veritabanı düzeyinde IP güvenlik duvarı kurallarını görüntüler |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |Veritabanı düzeyinde IP güvenlik duvarı kuralları oluşturur veya güncelleştirir |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Veritabanları |Veritabanı düzeyinde IP güvenlik duvarı kurallarını kaldırır |

Aşağıdaki örnek, mevcut kuralları gözden geçirir, *contoso*sunucusunda bir IP adresi aralığı sunar ve bir IP güvenlik duvarı kuralını siler:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ardından, sunucu düzeyinde bir IP güvenlik duvarı kuralı ekleyin.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Sunucu düzeyinde bir IP güvenlik duvarı kuralını silmek için *sp_delete_firewall_rule* saklı yordamını yürütün. Aşağıdaki örnek *ContosoFirewallRule*kuralını siler:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyinde IP güvenlik duvarı kurallarını yönetmek için PowerShell kullanma 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak tüm geliştirme artık az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az ve Azurerd modüllerindeki komutların bağımsız değişkenleri önemli ölçüde aynıdır.

| Cmdlet | Düzey | Açıklama |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde geçerli güvenlik duvarı kurallarını döndürür |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde yeni bir güvenlik duvarı kuralı oluşturur |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde mevcut güvenlik duvarı kuralının özelliklerini güncelleştirir |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde güvenlik duvarı kurallarını kaldırır |

Aşağıdaki örnek, PowerShell kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlar:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Hızlı başlangıç bağlamındaki PowerShell örnekleri için, bkz. [DB-PowerShell oluşturma](sql-database-powershell-samples.md) ve [tek bir veritabanı oluşturma ve PowerShell kullanarak SQL VERITABANı sunucu düzeyi IP güvenlik duvarı kuralı yapılandırma](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyinde IP güvenlik duvarı kurallarını yönetmek için CLı kullanma

| Cmdlet | Düzey | Açıklama |
| --- | --- | --- |
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Sunucu|Sunucu IP güvenlik duvarı kuralı oluşturur|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Sunucu|Bir sunucudaki IP güvenlik duvarı kurallarını listeler|
|[az SQL Server Firewall-Rule Show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Sunucu|Bir IP güvenlik duvarı kuralının ayrıntılarını gösterir|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Sunucu|Bir IP güvenlik duvarı kuralını güncelleştirir|
|[az SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Sunucu|Bir IP güvenlik duvarı kuralını siler|

Aşağıdaki örnek, sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlamak için CLı kullanır:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Hızlı başlangıç bağlamındaki bir CLı örneği için bkz. [Create DB-Azure CLI](sql-database-cli-samples.md) , [Azure CLI kullanarak tek bir VERITABANı oluşturma ve SQL veritabanı IP güvenlik duvarı kuralı yapılandırma](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyi IP güvenlik duvarı kurallarını yönetmek için REST API kullanma

| API | Düzey | Açıklama |
| --- | --- | --- |
| [Güvenlik duvarı kurallarını Listele](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Sunucu |Geçerli sunucu düzeyi IP güvenlik duvarı kurallarını görüntüler |
| [Güvenlik duvarı kuralları oluşturma veya güncelleştirme](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kuralları oluşturur veya güncelleştirir |
| [Güvenlik duvarı kurallarını Sil](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kurallarını kaldırır |
| [Güvenlik duvarı kuralları al](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Sunucu | Sunucu düzeyi IP güvenlik duvarı kurallarını alır |

## <a name="troubleshoot-the-database-firewall"></a>Veritabanı güvenlik duvarında sorun giderme

SQL veritabanı hizmetine erişim, beklendiği gibi davranmıyorsa aşağıdaki noktaları göz önünde bulundurun.

- **Yerel güvenlik duvarı yapılandırması:**

  Bilgisayarınızın SQL veritabanına erişebilmesi için bilgisayarınızda TCP bağlantı noktası 1433 için bir güvenlik duvarı özel durumu oluşturmanız gerekebilir. Azure bulut sınırları içinde bağlantı kurmak için ek bağlantı noktaları açmanız gerekebilir. Daha fazla bilgi için, [ADO.NET 4,5 ve SQL veritabanı 1433 ' den sonraki bağlantı noktalarının](sql-database-develop-direct-route-ports-adonet-v12.md)"SQL veritabanı: vs 'nin dışında" bölümüne bakın.

- **Ağ adresi çevirisi:**

  Ağ adresi çevirisi (NAT) nedeniyle, bilgisayarınız tarafından SQL veritabanına bağlanmak için kullanılan IP adresi bilgisayarınızın IP yapılandırma ayarlarındaki IP adresinden farklı olabilir. Azure 'a bağlanmak için bilgisayarınızın kullandığı IP adresini görüntülemek için:
    1. Portalda oturum açın.
    1. Veritabanınızı barındıran sunucudaki **Yapılandır** sekmesine gidin.
    1. **Geçerli ISTEMCI IP adresi** , **Izin verilen IP adresleri** bölümünde görüntülenir. Bu bilgisayarın sunucuya erişmesine izin vermek için **Izin VERILEN IP adresleri** **Ekle** ' yi seçin.

- **İzin verilenler listesindeki değişiklikler henüz devreye alınmadı:**

  SQL veritabanı güvenlik duvarı yapılandırmasındaki değişikliklerin etkili olabilmesi için beş dakikalık bir gecikme olabilir.

- **Oturum açma yetkisi yok veya yanlış parola kullanıldı:**

  Bir oturumun SQL veritabanı sunucusunda izinleri yoksa veya parola yanlışsa, sunucu bağlantısı reddedilir. Bir güvenlik duvarı ayarı oluşturmak istemcilere yalnızca sunucunuza bağlanmayı denemeye yönelik bir *fırsat* sağlar. İstemci yine de gerekli güvenlik kimlik bilgilerini sağlamalıdır. Oturumları hazırlama hakkında daha fazla bilgi için bkz. [SQL veritabanı ve SQL veri ambarı 'na veritabanı erişimini denetleme ve verme](sql-database-manage-logins.md).

- **Dinamik IP adresi:**

  Dinamik IP adresleme kullanan bir internet bağlantınız varsa ve güvenlik duvarıyla ilgili sorun yaşıyorsanız, aşağıdaki çözümlerden birini deneyin:
  
  - Internet servis sağlayıcınızdan SQL veritabanı sunucusuna erişen istemci bilgisayarlarınıza atanan IP adresi aralığını sorun. Bu IP adresi aralığını bir IP güvenlik duvarı kuralı olarak ekleyin.
  - İstemci bilgisayarlarınız yerine statik IP adresleme alın. IP adreslerini IP güvenlik duvarı kuralları olarak ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

- Kurumsal ağ ortamınızın, Azure veri merkezleri tarafından kullanılan işlem IP adresi aralıklarından (SQL aralıkları dahil) gelen iletişime izin verdiğini doğrulayın. Bu IP adreslerini izin verilenler listesine eklemeniz gerekebilir. Bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653).  
- Sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturma hakkında hızlı başlangıç için bkz. [Azure SQL veritabanı oluşturma](sql-database-single-database-get-started.md).
- Açık kaynaklı veya üçüncü taraf uygulamalardan bir Azure SQL veritabanına bağlanma konusunda yardım için bkz. [SQL veritabanı Için istemci hızlı başlangıç kodu örnekleri](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Açmanız gerekebilecek ek bağlantı noktaları hakkında daha fazla bilgi için, [ADO.NET 4,5 ve SQL veritabanı 1433 ' den sonraki bağlantı noktaları için](sql-database-develop-direct-route-ports-adonet-v12.md) "SQL veritabanı: vs 'nin dışında" bölümüne bakın
- Azure SQL veritabanı güvenliğine genel bakış için bkz. [veritabanınızın güvenliğini sağlama](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
