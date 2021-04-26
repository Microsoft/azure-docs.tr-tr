---
title: IP güvenlik duvarı kuralları
description: Azure SQL veritabanı veya Azure SYNAPSE Analytics güvenlik duvarında bir veritabanı için sunucu düzeyi IP güvenlik duvarı kurallarını yapılandırın. Erişimi yönetin ve SQL veritabanı için veritabanı düzeyinde IP güvenlik duvarı kurallarını yapılandırın.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: 200df14e7d18c4bdfb903bef46c169f6f7bf5ca5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781784"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL veritabanı ve Azure SYNAPSE IP güvenlik duvarı kuralları
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL veritabanı veya *te yapılandırılmış MySQLServer* adlı Azure SYNAPSE Analytics 'te yeni bir sunucu oluşturduğunuzda, örneğin, sunucu düzeyinde bir güvenlik duvarı sunucu için genel uç noktaya ( *MySQLServer.Database.Windows.net* adresinden erişilebilen) tüm erişimi engeller. Basitlik için *SQL veritabanı* hem SQL veritabanı hem de Azure SYNAPSE Analytics 'e başvurmak için kullanılır.

> [!IMPORTANT]
> Bu *Makale,* *Azure SQL yönetilen örneği* için geçerlidir. Ağ yapılandırması hakkında daha fazla bilgi için bkz. [uygulamanızı Azure SQL yönetilen örneğine bağlama](../managed-instance/connect-application-instance.md).
>
> Azure SYNAPSE yalnızca sunucu düzeyinde IP güvenlik duvarı kurallarını destekler. Veritabanı düzeyinde IP güvenlik duvarı kurallarını desteklemez.

## <a name="how-the-firewall-works"></a>Güvenlik duvarının çalışma biçimi

Aşağıdaki diyagramda gösterildiği gibi, internet 'ten ve Azure 'dan gelen bağlantı girişimleri, sunucunuza veya veritabanınıza ulaşmadan önce güvenlik duvarından geçmelidir.

   ![Güvenlik Duvarı yapılandırma diyagramı][1]

### <a name="server-level-ip-firewall-rules"></a>Sunucu düzeyinde IP güvenlik duvarı kuralları

Bu kurallar, istemcilerin sunucunuzun tamamına, yani sunucu tarafından yönetilen tüm veritabanlarına erişmesini sağlar. Kurallar *ana* veritabanında depolanır. Bir sunucu için en fazla 128 sunucu düzeyinde IP güvenlik duvarı kuralınız olabilir. **Bu sunucuya erişmek Için Azure hizmetlerine ve kaynaklarına Izin ver** ayarını etkinleştirdiyseniz, bu sunucu için tek bir güvenlik duvarı kuralı olarak sayılır.
  
Azure portal, PowerShell veya Transact-SQL deyimlerini kullanarak sunucu düzeyi IP güvenlik duvarı kurallarını yapılandırabilirsiniz.

- Portalı veya PowerShell 'i kullanmak için abonelik sahibi veya abonelik katılımcısı olmanız gerekir.
- Transact-SQL kullanmak için, *ana* veritabanına sunucu düzeyinde asıl oturum açma veya Azure Active Directory yöneticisi olarak bağlanmanız gerekir. (Sunucu düzeyi IP güvenlik duvarı kuralı önce Azure düzeyinde izinlere sahip olan bir kullanıcı tarafından oluşturulmalıdır.)

> [!NOTE]
> Varsayılan olarak, Azure portal yeni bir mantıksal SQL Server oluşturulması sırasında, **Bu sunucuya erişmek Için Azure hizmetlerine ve kaynaklarına Izin ver** ayarı **Hayır** olarak ayarlanır.

### <a name="database-level-ip-firewall-rules"></a>Veritabanı düzeyinde IP güvenlik duvarı kuralları

Veritabanı düzeyinde IP güvenlik duvarı kuralları, istemcilerin belirli (güvenli) veritabanlarına erişmesini sağlar. Her veritabanı için ( *ana* veritabanı dahil) kuralları oluşturur ve tek veritabanında depolanır.
  
- Yalnızca Transact-SQL deyimlerini kullanarak ve yalnızca ilk sunucu düzeyinde güvenlik duvarını yapılandırdıktan sonra, ana ve kullanıcı veritabanları için veritabanı düzeyinde IP güvenlik duvarı kuralları oluşturabilir ve yönetebilirsiniz.
- Veritabanı düzeyi IP güvenlik duvarı kuralında sunucu düzeyi IP güvenlik duvarı kuralında aralığın dışında bir IP adresi aralığı belirtirseniz, yalnızca veritabanı düzeyinde IP adresi olan istemciler veritabanına erişebilir.
- Bir veritabanı için en fazla 128 veritabanı düzeyinde IP güvenlik duvarı kuralına sahip olabilirsiniz. Veritabanı düzeyinde IP güvenlik duvarı kurallarını yapılandırma hakkında daha fazla bilgi için, bu makalenin devamındaki örneğe bakın ve [sp_set_database_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)bölümüne bakın.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Güvenlik duvarı kuralları ayarlama önerileri

Mümkün olduğunda veritabanı düzeyinde IP güvenlik duvarı kuralları kullanmanızı öneririz. Bu uygulama güvenlik düzeyini artırır ve veritabanınızı daha taşınabilir hale getirir. Yöneticiler için sunucu düzeyi IP güvenlik duvarı kurallarını kullanın. Ayrıca, aynı erişim gereksinimlerine sahip birçok veritabanınız varsa ve her veritabanını ayrı ayrı yapılandırmak istemiyorsanız bunları kullanın.

> [!NOTE]
> İş sürekliliği bağlamında taşınabilir veritabanları hakkında bilgi edinmek için bkz. [Olağanüstü durum kurtarma için kimlik doğrulama gereksinimleri](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Sunucu düzeyi ile veritabanı düzeyi IP güvenlik duvarı kurallarını karşılaştırma

*Tek bir veritabanının kullanıcıları başka bir veritabanından tamamen yalıtılmalıdır mi?*

Yanıt *Evet* ise, erişim izni vermek için VERITABANı düzeyinde IP güvenlik duvarı kurallarını kullanın. Bu yöntem, tüm veritabanlarına güvenlik duvarı üzerinden erişime izin veren sunucu düzeyi IP güvenlik duvarı kurallarını kullanmaktan kaçınır. Bu, savunlarınızın derinliğini azaltır.

*IP adreslerindeki kullanıcıların tüm veritabanlarına erişmesi gerekiyor mu?*

Yanıt *Evet* Ise, IP güvenlik duvarı kurallarını yapılandırmak istediğiniz zaman sayısını azaltmak için sunucu düzeyi IP güvenlik duvarı kurallarını kullanın.

*IP güvenlik duvarı kurallarını yapılandıran kişi veya takım yalnızca Azure portal, PowerShell veya REST API erişim sahibi olabilir mi?*

Bu durumda, sunucu düzeyinde IP güvenlik duvarı kuralları kullanmanız gerekir. Veritabanı düzeyinde IP güvenlik duvarı kuralları yalnızca Transact-SQL üzerinden yapılandırılabilir.  

*Veritabanı düzeyinde yüksek düzeyde izni olmayan IP güvenlik duvarı kurallarını yapılandıran kişi veya ekip mu?*

Bu durumda, sunucu düzeyi IP güvenlik duvarı kurallarını kullanın. Veritabanı düzeyinde, veritabanı düzeyinde IP güvenlik duvarı kurallarını Transact-SQL aracılığıyla yapılandırmak için veritabanı düzeyinde en az *DENETIM veritabanı* iznine sahip olmanız gerekir.  

*IP güvenlik duvarı kurallarını yapılandıran veya denetleyen kişi veya ekip, çok sayıda (belki yüzlerce) veritabanı için IP güvenlik duvarı kurallarını merkezi olarak yönetebilir mi?*

Bu senaryoda, en iyi yöntemler gereksinimlerinize ve ortamınıza göre belirlenir. Sunucu düzeyi IP Güvenlik Duvarı kurallarının yapılandırılması daha kolay olabilir, ancak komut dosyası, kuralları veritabanı düzeyinde yapılandırabilir. Sunucu düzeyinde IP güvenlik duvarı kuralları kullanıyor olsanız bile, veritabanında *Denetim* iznine sahip kullanıcıların VERITABANı düzeyinde IP güvenlik duvarı kuralları oluşturma izni olup olmadığını görmek için VERITABANı düzeyinde IP güvenlik duvarı kurallarını denetlemeniz gerekebilir.

*Sunucu düzeyinde ve veritabanı düzeyinde IP Güvenlik Duvarı kurallarının bir karışımını kullanabilir miyim?*

Evet. Yöneticiler gibi bazı kullanıcıların sunucu düzeyinde IP güvenlik duvarı kuralları olması gerekebilir. Bir veritabanı uygulamasının kullanıcıları gibi diğer kullanıcıların veritabanı düzeyinde IP güvenlik duvarı kuralları olması gerekebilir.

### <a name="connections-from-the-internet"></a>İnternet bağlantısı

Bir bilgisayar Internet 'ten sunucunuza bağlanmaya çalıştığında, güvenlik duvarı ilk olarak isteğin kaynak IP adresini, bağlantının istek yaptığı veritabanının veritabanı düzeyinde IP güvenlik duvarı kurallarına karşı denetler.

- Adres, veritabanı düzeyinde IP güvenlik duvarı kurallarında belirtilen bir Aralık içindeyse, kuralı içeren veritabanına bağlantı verilir.
- Adres, veritabanı düzeyinde IP güvenlik duvarı kurallarında bir Aralık içinde değilse, güvenlik duvarı sunucu düzeyi IP güvenlik duvarı kurallarını denetler. Adres, sunucu düzeyinde IP güvenlik duvarı kurallarında bulunan bir Aralık içindeyse, bağlantı verilir. Sunucu düzeyinde IP güvenlik duvarı kuralları, sunucu tarafından yönetilen tüm veritabanları için geçerlidir.  
- Adres, veritabanı düzeyinde veya sunucu düzeyinde IP güvenlik duvarı kurallarındaki bir Aralık içinde değilse, bağlantı isteği başarısız olur.

> [!NOTE]
> Yerel bilgisayarınızdan Azure SQL veritabanına erişmek için, ağınızdaki ve yerel bilgisayarınızdaki güvenlik duvarının TCP bağlantı noktası 1433 ' de giden iletişime izin verildiğinden emin olun.

### <a name="connections-from-inside-azure"></a>Azure içinden bağlantılar

Azure içinde barındırılan uygulamaların SQL sunucunuza bağlanmasına izin vermek için Azure bağlantıları etkinleştirilmelidir. Azure bağlantıları 'nı etkinleştirmek için, başlangıç ve bitiş IP adreslerini 0.0.0.0 olarak ayarlanmış bir güvenlik duvarı kuralı olmalıdır.

Azure 'dan bir uygulama sunucusuna bağlanmaya çalıştığında, güvenlik duvarı kuralı var olduğunu doğrulayarak Azure bağlantılarına izin verildiğini kontrol eder. Bu, **güvenlik duvarları ve sanal ağlar** ayarlarındaki **Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine izin ver ' i** değiştirerek doğrudan Azure Portal dikey  penceresinden etkinleştirilebilir. ÜZERINDE olarak ayarlandığında, IP 0.0.0.0-0.0.0.0 adlı, **Allowallwindowsıp** adlı bir gelen güvenlik duvarı kuralı oluşturulur. Portalı kullanmıyorsanız başlangıç ve bitiş IP adresleri 0.0.0.0 olarak ayarlanan bir güvenlik duvarı kuralı oluşturmak için PowerShell veya Azure CLı kullanın. 

> [!IMPORTANT]
> Bu seçenek, güvenlik duvarını diğer müşterilerin aboneliklerinden gelen bağlantılar da dahil olmak üzere Azure 'daki tüm bağlantılara izin verecek şekilde yapılandırır. Bu seçeneği belirlerseniz, oturum açma ve kullanıcı izinlerinizin yalnızca yetkili kullanıcılara erişimi sınırlandırdığınızdan emin olun.

## <a name="permissions"></a>İzinler

Azure SQL Server için IP güvenlik duvarı kuralı oluşturmak ve yönetmek istiyorsanız şu rollerden birine sahip olmanız gerekir:

- [SQL Server katkıda](../../role-based-access-control/built-in-roles.md#sql-server-contributor) bulunan rolünde
- [SQL Güvenlik Yöneticisi](../../role-based-access-control/built-in-roles.md#sql-security-manager) rolünde
- Azure SQL Server içeren kaynağın sahibi

## <a name="create-and-manage-ip-firewall-rules"></a>IP güvenlik duvarı kurallarını oluşturma ve yönetme

[Azure Portal](https://portal.azure.com/) veya program aracılığıyla [Azure POWERSHELL](/powershell/module/az.sql), [azure CLI](/cli/azure/sql/server/firewall-rule)veya bir Azure [REST API](/rest/api/sql/firewallrules/createorupdate)kullanarak ilk sunucu düzeyi güvenlik duvarı ayarını oluşturursunuz. Bu yöntemleri veya Transact-SQL ' i kullanarak diğer sunucu düzeyi IP güvenlik duvarı kuralları oluşturur ve yönetirsiniz.

> [!IMPORTANT]
> Veritabanı düzeyi IP güvenlik duvarı kuralları yalnızca Transact-SQL kullanılarak oluşturulabilir ve yönetilebilir.

Performansı artırmak için sunucu düzeyinde IP güvenlik duvarı kuralları veritabanı düzeyinde geçici olarak önbelleğe alınır. Önbelleği yenilemek için bkz. [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı değişikliklerini denetlemek için [veritabanı denetimini](../../azure-sql/database/auditing-overview.md) kullanabilirsiniz.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyi IP güvenlik duvarı kurallarını yönetmek için Azure portal kullanma

Azure portal sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlamak için veritabanınızın veya sunucunuzun genel bakış sayfasına gidin.

> [!TIP]
> Öğretici için, bkz. [Azure Portal kullanarak veritabanı oluşturma](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Veritabanına genel bakış sayfasından

1. Veritabanı genel bakış sayfasından sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlamak için, aşağıdaki görüntüde gösterildiği gibi araç çubuğunda **sunucu güvenlik duvarını ayarla** ' yı seçin.

    ![Sunucu IP güvenlik duvarı kuralı](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Sunucu için **güvenlik duvarı ayarları** sayfası açılır.

2. Kullanmakta olduğunuz bilgisayarın IP adresini eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin ve ardından **Kaydet**' i seçin. Geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

    ![Sunucu düzeyi IP güvenlik duvarı kuralını ayarla](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Sunucuya genel bakış sayfasından

Sunucunuzun genel bakış sayfası açılır. Tam sunucu adını (örneğin, *mynewserver20170403.Database.Windows.net*) gösterir ve daha fazla yapılandırma seçeneği sunar.

1. Bu sayfadan bir sunucu düzeyi kuralı ayarlamak için, sol taraftaki **Ayarlar** menüsünden **güvenlik duvarı** ' nı seçin.

2. Kullanmakta olduğunuz bilgisayarın IP adresini eklemek için araç çubuğunda **istemci IP 'Si Ekle** ' yi seçin ve ardından **Kaydet**' i seçin. Geçerli IP adresiniz için sunucu düzeyinde bir IP güvenlik duvarı kuralı oluşturulur.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>IP güvenlik duvarı kurallarını yönetmek için Transact-SQL kullanma

| Katalog görünümü veya saklı yordam | Level | Description |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Sunucu |Geçerli sunucu düzeyi IP güvenlik duvarı kurallarını görüntüler |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kuralları oluşturur veya güncelleştirir |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kurallarını kaldırır |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Veritabanı |Geçerli veritabanı düzeyinde IP güvenlik duvarı kurallarını görüntüler |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Veritabanı |Veritabanı düzeyinde IP güvenlik duvarı kuralları oluşturur veya güncelleştirir |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Veritabanları |Veritabanı düzeyinde IP güvenlik duvarı kurallarını kaldırır |

Aşağıdaki örnek, mevcut kuralları gözden geçirir, *contoso* sunucusunda bir IP adresi aralığı sunar ve bir IP güvenlik duvarı kuralını siler:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Ardından, sunucu düzeyinde bir IP güvenlik duvarı kuralı ekleyin.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Sunucu düzeyinde bir IP güvenlik duvarı kuralını silmek için *sp_delete_firewall_rule* saklı yordamını yürütün. Aşağıdaki örnek *ContosoFirewallRule* kuralını siler:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyinde IP güvenlik duvarı kurallarını yönetmek için PowerShell kullanma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak tüm geliştirme artık az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az ve Azurerd modüllerindeki komutların bağımsız değişkenleri önemli ölçüde aynıdır.

| Cmdlet | Level | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde geçerli güvenlik duvarı kurallarını döndürür |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde yeni bir güvenlik duvarı kuralı oluşturur |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde mevcut güvenlik duvarı kuralının özelliklerini güncelleştirir |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Sunucu |Sunucu düzeyinde güvenlik duvarı kurallarını kaldırır |

Aşağıdaki örnek, PowerShell kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlar:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> $Servername için, tam DNS adını değil, sunucu adını belirtin, örneğin, **mysqldbserver.Database.Windows.net** yerine **mysqldbserver** belirtin
>
> Hızlı başlangıç bağlamındaki PowerShell örnekleri için, bkz. [DB-PowerShell oluşturma](powershell-script-content-guide.md) ve [tek bir veritabanı oluşturma ve PowerShell kullanarak sunucu düzeyinde bir IP güvenlik duvarı kuralı yapılandırma](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyinde IP güvenlik duvarı kurallarını yönetmek için CLı kullanma

| Cmdlet | Level | Description |
| --- | --- | --- |
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Sunucu|Sunucu IP güvenlik duvarı kuralı oluşturur|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Sunucu|Bir sunucudaki IP güvenlik duvarı kurallarını listeler|
|[az SQL Server Firewall-Rule Show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Sunucu|Bir IP güvenlik duvarı kuralının ayrıntılarını gösterir|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Sunucu|Bir IP güvenlik duvarı kuralını güncelleştirir|
|[az SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Sunucu|Bir IP güvenlik duvarı kuralını siler|

Aşağıdaki örnek, sunucu düzeyinde bir IP güvenlik duvarı kuralı ayarlamak için CLı kullanır:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> $Servername için, tam DNS adını değil, sunucu adını belirtin, örneğin, **mysqldbserver.Database.Windows.net** yerine **mysqldbserver** belirtin
>
> Hızlı başlangıç bağlamındaki bir CLı örneği için bkz. [Create DB-Azure CLI](az-cli-script-samples-content-guide.md) , [Azure CLI kullanarak tek bir veritabanı oluşturma ve sunucu düzeyinde IP güvenlik duvarı kuralı yapılandırma](scripts/create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Sunucu düzeyi IP güvenlik duvarı kurallarını yönetmek için REST API kullanma

| API | Level | Description |
| --- | --- | --- |
| [Güvenlik duvarı kurallarını Listele](/rest/api/sql/firewallrules/listbyserver) |Sunucu |Geçerli sunucu düzeyi IP güvenlik duvarı kurallarını görüntüler |
| [Güvenlik duvarı kuralları oluşturma veya güncelleştirme](/rest/api/sql/firewallrules/createorupdate) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kuralları oluşturur veya güncelleştirir |
| [Güvenlik duvarı kurallarını Sil](/rest/api/sql/firewallrules/delete) |Sunucu |Sunucu düzeyi IP güvenlik duvarı kurallarını kaldırır |
| [Güvenlik duvarı kuralları al](/rest/api/sql/firewallrules/get) | Sunucu | Sunucu düzeyi IP güvenlik duvarı kurallarını alır |

## <a name="troubleshoot-the-database-firewall"></a>Veritabanı güvenlik duvarı sorunlarını giderme

Azure SQL veritabanı erişimi, beklendiği gibi davranmazsa aşağıdaki noktaları göz önünde bulundurun.

- **Yerel güvenlik duvarı yapılandırması:**

  Bilgisayarınızın Azure SQL veritabanına erişebilmesi için bilgisayarınızda TCP bağlantı noktası 1433 için bir güvenlik duvarı özel durumu oluşturmanız gerekebilir. Azure bulut sınırları içinde bağlantı kurmak için ek bağlantı noktaları açmanız gerekebilir. Daha fazla bilgi için, [ADO.NET 4,5 ve Azure SQL veritabanı 1433 ' den sonraki bağlantı noktalarının](adonet-v12-develop-direct-route-ports.md)"SQL veritabanı: vs 'nin dışında" bölümüne bakın.

- **Ağ adresi çevirisi:**

  Ağ adresi çevirisi (NAT) nedeniyle, bilgisayarınız tarafından Azure SQL veritabanına bağlanmak için kullanılan IP adresi bilgisayarınızın IP yapılandırma ayarlarındaki IP adresinden farklı olabilir. Azure 'a bağlanmak için bilgisayarınızın kullandığı IP adresini görüntülemek için:
    1. Portalda oturum açın.
    1. Veritabanınızı barındıran sunucudaki **Yapılandır** sekmesine gidin.
    1. **Geçerli ISTEMCI IP adresi** , **Izin verilen IP adresleri** bölümünde görüntülenir. Bu bilgisayarın sunucuya erişmesine izin vermek için **Izin VERILEN IP adresleri** **Ekle** ' yi seçin.

- **İzin verilenler listesindeki değişiklikler henüz devreye alınmadı:**

  Azure SQL veritabanı güvenlik duvarı yapılandırmasındaki değişikliklerin etkili olabilmesi için beş dakikalık bir gecikme olabilir.

- **Oturum açma yetkisi yok veya yanlış parola kullanıldı:**

  Bir oturumun sunucuda izinleri yoksa veya parola yanlışsa, sunucu bağlantısı reddedilir. Bir güvenlik duvarı ayarı oluşturmak istemcilere yalnızca sunucunuza bağlanmayı denemeye yönelik bir *fırsat* sağlar. İstemci yine de gerekli güvenlik kimlik bilgilerini sağlamalıdır. Oturumları hazırlama hakkında daha fazla bilgi için bkz. [veritabanı erişimini denetleme ve verme](logins-create-manage.md).

- **Dinamik IP adresi:**

  Dinamik IP adresleme kullanan bir internet bağlantınız varsa ve güvenlik duvarıyla ilgili sorun yaşıyorsanız, aşağıdaki çözümlerden birini deneyin:
  
  - İnternet hizmet sağlayıcınızdan sunucuya erişen istemci bilgisayarlarınıza atanan IP adresi aralığını isteyin. Bu IP adresi aralığını bir IP güvenlik duvarı kuralı olarak ekleyin.
  - İstemci bilgisayarlarınız yerine statik IP adresleme alın. IP adreslerini IP güvenlik duvarı kuralları olarak ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

- Kurumsal ağ ortamınızın, Azure veri merkezleri tarafından kullanılan işlem IP adresi aralıklarından (SQL aralıkları dahil) gelen iletişime izin verdiğini doğrulayın. Bu IP adreslerini izin verilenler listesine eklemeniz gerekebilir. Bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653).  
- [Azure SQL veritabanı 'nda tek bir veritabanı oluşturma](single-database-create-quickstart.md)hakkında hızlı başlangıç deneyimimize bakın.
- Azure SQL veritabanı 'nda açık kaynaklı veya üçüncü taraf uygulamalardan bir veritabanına bağlanma konusunda yardım için bkz. [Azure SQL veritabanı 'Na istemci hızlı başlangıç kodu örnekleri](connect-query-content-reference-guide.md#libraries).
- Açmanız gerekebilecek ek bağlantı noktaları hakkında daha fazla bilgi için, [ADO.NET 4,5 ve SQL veritabanı 1433 ' den sonraki bağlantı noktaları için](adonet-v12-develop-direct-route-ports.md) "SQL veritabanı: vs 'nin dışında" bölümüne bakın
- Azure SQL veritabanı güvenliğine genel bakış için bkz. [veritabanınızın güvenliğini sağlama](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
