---
title: Azure SQL veritabanı ve Azure SYNAPSE Analytics 'te sunucu nedir?
titleSuffix: ''
description: Azure SQL veritabanı ve Azure SYNAPSE Analytics tarafından kullanılan mantıksal SQL sunucuları ve bunların nasıl yönetileceği hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c76d3ae78bf2b9b4a71d9520f7f1c6c2c322483b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784524"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Azure SQL veritabanı ve Azure SYNAPSE 'de mantıksal SQL Server nedir?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Azure SQL veritabanı ve Azure SYNAPSE Analytics 'te, bir sunucu, bir veritabanı koleksiyonu için merkezi bir yönetim noktası görevi gören mantıksal bir yapıdır. Sunucu düzeyinde, [oturum açma](logins-create-manage.md)bilgileri, [güvenlik duvarı kuralları](firewall-configure.md), [denetim kuralları](../../azure-sql/database/auditing-overview.md), [tehdit algılama ilkeleri](threat-detection-configure.md)ve [otomatik yük devretme grupları](auto-failover-group-overview.md)yönetebilirsiniz. Sunucu, kaynak grubundan farklı bir bölgede olabilir. Azure SQL veritabanı 'nda veya Azure SYNAPSE Analytics 'teki bir veri ambarı veritabanında bir veritabanı oluşturabilmeniz için önce sunucunun mevcut olması gerekir. Tek bir sunucu tarafından yönetilen tüm veritabanları, sunucusuyla aynı bölge içinde oluşturulur.

Bu sunucu, şirket içi dünyada bildiğiniz bir SQL Server örneğinden farklıdır. Özellikle, veritabanlarının veya veri ambarı veritabanının, kendilerini yöneten sunucuyla ilişkili olarak konumuyla ilgili garanti yoktur. Ayrıca, Azure SQL veritabanı ve Azure SYNAPSE herhangi bir örnek düzeyi erişim veya özellik sunmaz. Buna karşılık, yönetilen bir örnekteki örnek veritabanlarının hepsi, şirket içi veya sanal makine dünyasında SQL Server bildiğiniz gibi fiziksel olarak birlikte bulunur.

Sunucu oluşturduğunuzda, bu sunucu üzerindeki ana veritabanına ve bu sunucuda oluşturulan tüm veritabanlarına yönetici haklarına sahip bir sunucu oturum açma hesabı ve parolası sağlarsınız. Bu ilk hesap bir SQL oturum açma hesabıdır. Azure SQL veritabanı ve Azure SYNAPSE Analytics kimlik doğrulaması için SQL kimlik doğrulamasını ve Azure Active Directory kimlik doğrulamasını destekler. Oturum açma ve kimlik doğrulama hakkında bilgi için bkz. [Azure SQL veritabanı 'Nda veritabanlarını ve oturum açma Işlemlerini yönetme](logins-create-manage.md). Windows Kimlik Doğrulaması desteklenmez.

SQL veritabanı ve Azure 'da bir sunucu SYNAPSE:

- Bir Azure aboneliği içinde oluşturulur, ancak içerdiği kaynaklarla birlikte başka bir aboneliğe taşınabilir
- Veritabanları, elastik havuzlar ve veri ambarları için üst kaynaktır
- Veritabanları, elastik havuzlar ve veri ambarı veritabanı için bir ad alanı sağlar
- Güçlü yaşam süresi semantiğinin bulunduğu mantıksal bir kapsayıcıdır; bir sunucuyu silin ve veritabanlarını, elastik havuzları ve SQK havuzlarını siler
- [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md) -bir sunucu içindeki veritabanları, elastik havuzlar ve veri ambarı veritabanı, sunucudan erişim haklarını devralır
- , Azure Kaynak Yönetimi amaçları için veritabanları, elastik havuzlar ve veri ambarı veritabanı kimliğinin yüksek sıralı bir öğesidir (bkz. veritabanları ve havuzlar için URL şeması)
- Bir bölgedeki kaynakları birlikte bulundurur
- Veritabanı erişimi için bağlantı uç noktası sağlar (`<serverName>`.database.windows.net)
- Bir ana veritabanına bağlanarak DMV’ler aracılığıyla içerdiği kaynaklarla ilgili meta verilere erişim sağlar
- Veritabanları için, oturum açma bilgileri, güvenlik duvarı, denetim, tehdit algılama ve gibi yönetim ilkeleri için kapsam sağlar
- , Üst abonelik içindeki bir kota ile kısıtlanır (varsayılan olarak abonelik başına altı sunucu) ve [burada abonelik sınırlarına bakın](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- , İçerdiği kaynaklar için veritabanı kotası ve DTU veya sanal çekirdek kotası kapsamını sağlar (örneğin, 45.000 DTU)
- Kapsanan kaynaklarda etkinleştirilen yetenekler için sürüm kapsamıdır
- Sunucu düzeyinde asıl kullanıcı bilgileri bir sunucudaki tüm veritabanlarını yönetebilir
- , Sunucuda bir veya daha fazla veritabanına erişim izni verilen şirket içi ortamınızda SQL Server örneklerindekilerle benzer oturum açmaları içerebilir ve sınırlı yönetim hakları verilebilir. Daha fazla bilgi için bkz. [Kullanıcı Bilgileri](logins-create-manage.md).
- Sunucuda oluşturulan tüm veritabanları için varsayılan harmanlama, `SQL_LATIN1_GENERAL_CP1_CI_AS` burada `LATIN1_GENERAL` ingilizce (Birleşik Devletler), `CP1` kod sayfası 1252, `CI` büyük/küçük harfe duyarsız ve `AS` aksan duyarsızdır.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Azure portal kullanarak sunucuları, veritabanlarını ve güvenlik duvarlarını yönetme

Sunucu için kaynak grubunu zaman içinde veya sunucunun kendisini oluştururken oluşturabilirsiniz. Yeni bir SQL Server oluşturmak için veya yeni bir veritabanı oluşturmanın bir parçası olarak yeni bir SQL Server formu almak için birden çok yöntem vardır.

### <a name="create-a-blank-server"></a>Boş sunucu oluştur

Bir sunucu oluşturmak için (veritabanı, elastik havuz veya veri ambarı veritabanı olmadan) [Azure Portal](https://portal.azure.com)kullanarak boş bir SQL Server (mantıksal SQL Server) formuna gidin.

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda boş veya örnek veritabanı oluşturma

SQL veritabanında [Azure Portal](https://portal.azure.com)kullanarak veritabanı oluşturmak için boş bir SQL veritabanı formuna gidin ve istenen bilgileri sağlayın. Kaynak grubunu ve sunucuyu zaman içinde veya veritabanını oluştururken oluşturabilirsiniz. Boş bir veritabanı oluşturabilir veya Adventure Works LT ' y a göre örnek bir veritabanı oluşturabilirsiniz.

  ![create database-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Veritabanınızın fiyatlandırma katmanını seçme hakkında daha fazla bilgi için bkz. [DTU tabanlı satın alma modeli](service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md).

Yönetilen bir örnek oluşturmak için bkz. [yönetilen örnek oluşturma](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Var olan bir sunucuyu yönetme

Var olan bir sunucuyu yönetmek için, belirli bir veritabanı sayfası, **SQL Server** sayfası veya **tüm kaynaklar** sayfası gibi çeşitli yöntemler kullanarak sunucuya gidin.

Var olan bir veritabanını yönetmek için **SQL veritabanları** sayfasına gidin ve yönetmek istediğiniz veritabanına tıklayın. Aşağıdaki ekran görüntüsünde, bir veritabanının **genel bakış** sayfasından bir veritabanı için sunucu düzeyinde güvenlik duvarının ayarlanmasına nasıl başlayabileceğiniz gösterilmektedir.

   ![sunucu güvenlik duvarı kuralı](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Bir veritabanının performans özelliklerini yapılandırmak için bkz. [DTU tabanlı satın alma modeli](service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](service-tiers-vcore.md).
> [!TIP]
> Azure portal hızlı başlangıç için, bkz. [Azure Portal SQL veritabanı 'nda veritabanı oluşturma](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>PowerShell kullanarak sunucuları, veritabanlarını ve güvenlik duvarlarını yönetme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Azure PowerShell ile sunucu, veritabanları ve güvenlik duvarları oluşturup yönetmek için aşağıdaki PowerShell cmdlet 'lerini kullanın. PowerShell 'i yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps). Elastik havuzlar oluşturma ve yönetme için bkz. [elastik havuzlar](elastic-pool-overview.md).

| Cmdlet | Açıklama |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Bir veritabanı oluşturur |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Bir veya daha fazla veritabanını alır|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Bir veritabanının özelliklerini ayarlar veya var olan bir veritabanını esnek bir havuza taşıanlar|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Bir veritabanını kaldırır|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Bir kaynak grubu oluşturur|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Sunucu oluşturur|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Sunucular hakkında bilgi döndürür|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Bir sunucunun özelliklerini değiştirir|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Bir sunucuyu kaldırır|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturur |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Bir sunucu için güvenlik duvarı kurallarını alır|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Bir sunucudaki güvenlik duvarı kuralını değiştirir|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Bir güvenlik duvarı kuralını sunucudan siler.|
| New-AzSqlServerVirtualNetworkRule | Sanal ağ hizmeti uç noktası olan bir alt ağa bağlı olarak bir [*sanal ağ kuralı*](vnet-service-endpoint-rule-overview.md)oluşturur. |

> [!TIP]
> PowerShell hızlı başlangıcı için bkz. [PowerShell kullanarak Azure SQL veritabanı 'nda veritabanı oluşturma](single-database-create-quickstart.md). PowerShell örnek betikleri için bkz. PowerShell kullanarak [Azure SQL veritabanı 'nda veritabanı oluşturma ve bir güvenlik duvarı kuralı yapılandırma](scripts/create-and-configure-database-powershell.md) ve [POWERSHELL kullanarak Azure SQL veritabanı 'Nda bir veritabanını izleme ve ölçeklendirme](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Azure CLı kullanarak sunucuları, veritabanlarını ve güvenlik duvarlarını yönetme

[Azure CLI](/cli/azure)ile sunucu, veritabanları ve güvenlik duvarları oluşturup yönetmek Için AŞAĞıDAKI [Azure CLI SQL veritabanı](/cli/azure/sql/db) komutlarını kullanın. CLI’yi tarayıcınızda çalıştırmak için [Cloud Shell](../../cloud-shell/overview.md) kullanın veya macOS, Linux ya da Windows’da [yükleyin](/cli/azure/install-azure-cli). Elastik havuzlar oluşturma ve yönetme için bkz. [elastik havuzlar](elastic-pool-overview.md).

| Cmdlet | Açıklama |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Bir veritabanı oluşturur|
|[az SQL DB List](/cli/azure/sql/db#az_sql_db_list)|Bir sunucu tarafından yönetilen tüm veritabanlarını veya elastik havuzdaki tüm veritabanlarını listeler|
|[az SQL DB List-Editions](/cli/azure/sql/db#az_sql_db_list_editions)|Kullanılabilir hizmet hedeflerini ve depolama sınırlarını listeler|
|[az SQL DB List-kullanımlar](/cli/azure/sql/db#az_sql_db_list_usages)|Veritabanı kullanımlarını döndürür|
|[az SQL DB Show](/cli/azure/sql/db#az_sql_db_show)|Bir veritabanını alır
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Bir veritabanını güncelleştirir|
|[az SQL DB Delete](/cli/azure/sql/db#az_sql_db_delete)|Bir veritabanını kaldırır|
|[az group create](/cli/azure/group#az_group_create)|Bir kaynak grubu oluşturur|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Sunucu oluşturur|
|[az SQL Server List](/cli/azure/sql/server#az_sql_server_list)|Sunucuları listeler|
|[az SQL Server List-kullanımlar](/cli/azure/sql/server#az_sql_server_list-usages)|Sunucu kullanımlarını döndürür|
|[az SQL Server Show](/cli/azure/sql/server#az_sql_server_show)|Bir sunucu alır|
|[az SQL Server Update](/cli/azure/sql/server#az_sql_server_update)|Bir sunucuyu güncelleştirir|
|[az SQL Server DELETE](/cli/azure/sql/server#az_sql_server_delete)|Bir sunucuyu siler|
|[az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Sunucu güvenlik duvarı kuralı oluşturur|
|[az SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Bir sunucudaki güvenlik duvarı kurallarını listeler|
|[az SQL Server Firewall-Rule Show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Bir güvenlik duvarı kuralının ayrıntılarını gösterir|
|[az SQL Server Firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Bir güvenlik duvarı kuralını güncelleştirir|
|[az SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Bir güvenlik duvarı kuralını siler|

> [!TIP]
> Azure CLı hızlı başlangıcı için bkz. Azure [CLI kullanarak Azure SQL veritabanı 'nda veritabanı oluşturma](az-cli-script-samples-content-guide.md). Azure CLı örnek betikleri için bkz. [CLI kullanarak Azure SQL veritabanı 'nda veritabanı oluşturma ve bir güvenlik duvarı kuralı yapılandırma](scripts/create-and-configure-database-cli.md) ve [CLı kullanarak Azure SQL veritabanı 'nda bir veritabanını izleme ve ölçeklendirme](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Transact-SQL kullanarak sunucuları, veritabanlarını ve güvenlik duvarlarını yönetme

Transact-SQL ile sunucu, veritabanları ve güvenlik duvarları oluşturup yönetmek için aşağıdaki T-SQL komutlarını kullanın. Bu komutları, Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)ya da bir sunucuya BAĞLANABILECEK ve Transact-SQL komutlarını geçirebilmeniz gereken başka bir programı kullanarak verebilirsiniz. Elastik havuzları yönetmek için bkz. [elastik havuzlar](elastic-pool-overview.md).

> [!IMPORTANT]
> Transact-SQL kullanarak sunucu oluşturamaz veya silemezsiniz.

| Komut | Açıklama |
| --- | --- |
|[VERITABANı oluşturma (Azure SQL veritabanı)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Azure SQL veritabanı 'nda yeni bir veritabanı oluşturur. Yeni bir veritabanı oluşturmak için ana veritabanına bağlı olmanız gerekir.|
|[VERITABANı oluşturma (Azure SYNAPSE)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Azure SYNAPSE 'te yeni bir veri ambarı veritabanı oluşturur. Yeni bir veritabanı oluşturmak için ana veritabanına bağlı olmanız gerekir.|
| [ALTER DATABASE (Azure SQL veritabanı)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Veritabanını veya elastik havuzu değiştirir. |
|[ALTER DATABASE (Azure SYNAPSE Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Azure SYNAPSE 'de bir veri ambarı veritabanını değiştirir.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Bir veritabanını siler.|
|[sys.database_service_objectives (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Bir veritabanı için sürüm (hizmet katmanı), hizmet hedefi (Fiyatlandırma Katmanı) ve elastik havuz adı döndürür. Bir sunucu için ana veritabanında oturum açarsa, tüm veritabanlarına bilgi döndürür. Azure SYNAPSE için ana veritabanına bağlı olmanız gerekir.|
|[sys.dm_db_resource_stats (Azure SQL veritabanı)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Azure SQL veritabanı 'nda bir veritabanı için CPU, GÇ ve bellek tüketimi döndürür. Veritabanında etkinlik olmasa bile, her 15 saniyede bir satır vardır.|
|[sys.resource_stats (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Azure SQL veritabanı 'nda bir veritabanının CPU kullanımını ve depolama verilerini döndürür. Veriler, beş dakikalık aralıklar içinde toplanır ve toplanır.|
|[sys.database_connection_stats (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Azure SQL veritabanı için veritabanı bağlantı olaylarının istatistiklerini içerir. Bu, veritabanı bağlantısı başarılarına ve hatalara ilişkin bir genel bakış sağlar. |
|[sys.event_log (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Azure SQL veritabanı için başarılı Azure SQL veritabanı bağlantılarını, bağlantı başarısızlıklarını ve kilitlenmeleri döndürür. Bu bilgileri, veritabanı etkinliğinizi izlemek veya sorunlarını gidermek için kullanabilirsiniz.|
|[sp_set_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Sunucunuz için sunucu düzeyinde güvenlik duvarı ayarlarını oluşturur veya güncelleştirir. Bu saklı yordam yalnızca ana veritabanında sunucu düzeyinde asıl oturum açma için kullanılabilir. Sunucu düzeyi güvenlik duvarı kuralı, yalnızca ilk sunucu düzeyi güvenlik duvarı kuralı, Azure düzeyinde izinlerle bir kullanıcı tarafından oluşturulduktan sonra Transact-SQL kullanılarak oluşturulabilir|
|[sys.firewall_rules (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Bir sunucuyla ilişkili sunucu düzeyi güvenlik duvarı ayarları hakkında bilgi döndürür.|
|[sp_delete_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Sunucu düzeyi güvenlik duvarı ayarlarını bir sunucudan kaldırır. Bu saklı yordam yalnızca ana veritabanında sunucu düzeyinde asıl oturum açma için kullanılabilir.|
|[sp_set_database_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Azure SQL veritabanında bir veritabanı için veritabanı düzeyinde güvenlik duvarı kuralları oluşturur veya güncelleştirir. Veritabanı güvenlik duvarı kuralları, ana veritabanı ve SQL veritabanı 'ndaki Kullanıcı veritabanları için yapılandırılabilir. Kapsanan veritabanı kullanıcıları kullanılırken veritabanı güvenlik duvarı kuralları yararlı olur. Veritabanı güvenlik duvarı kuralları Azure SYNAPSE 'da desteklenmez.|
|[sys.database_firewall_rules (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Azure SQL veritabanı 'nda bir veritabanının veritabanı düzeyinde güvenlik duvarı ayarları hakkında bilgi döndürür. |
|[sp_delete_database_firewall_rule (Azure SQL veritabanı)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Azure SQL veritabanı 'nda kendi veritabanı için veritabanı düzeyinde güvenlik duvarı ayarını kaldırır. |

> [!TIP]
> Microsoft Windows üzerinde SQL Server Management Studio kullanan bir hızlı başlangıç için bkz. [Azure SQL veritabanı: bağlanmak ve veri sorgulamak için SQL Server Management Studio kullanma](connect-query-ssms.md). MacOS, Linux veya Windows üzerinde Visual Studio Code kullanan bir hızlı başlangıç için bkz. [Azure SQL veritabanı: bağlanmak ve veri sorgulamak için Visual Studio Code kullanma](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>REST API kullanarak sunucuları, veritabanlarını ve güvenlik duvarlarını yönetme

Sunucu, veritabanı ve güvenlik duvarı oluşturmak ve yönetmek için bu REST API isteklerini kullanın.

| Komut | Açıklama |
| --- | --- |
|[Sunucular-oluştur veya güncelleştir](/rest/api/sql/servers/createorupdate)|Yeni bir sunucu oluşturur veya güncelleştirir.|
|[Sunucular-Sil](/rest/api/sql/servers/delete)|Bir sunucuyu siler.|
|[Sunucular-al](/rest/api/sql/servers/get)|Bir sunucu alır.|
|[Sunucular-liste](/rest/api/sql/servers/list)|Sunucu listesini döndürür.|
|[Sunucular-kaynak grubuna göre Listele](/rest/api/sql/servers/listbyresourcegroup)|Bir kaynak grubundaki sunucuların listesini döndürür.|
|[Sunucular-Güncelleştir](/rest/api/sql/servers/update)|Var olan bir sunucuyu güncelleştirir.|
|[Veritabanları-oluştur veya güncelleştir](/rest/api/sql/databases/createorupdate)|Yeni bir veritabanı oluşturur veya var olan bir veritabanını güncelleştirir.|
|[Veritabanları-Sil](/rest/api/sql/databases/delete)|Bir veritabanını siler.|
|[Veritabanları-al](/rest/api/sql/databases/get)|Bir veritabanını alır.|
|[Veritabanları-elastik havuza göre Listele](/rest/api/sql/databases/listbyelasticpool)|Elastik havuzdaki veritabanlarının listesini döndürür.|
|[Veritabanları-sunucuya göre listeleme](/rest/api/sql/databases/listbyserver)|Bir sunucudaki veritabanlarının listesini döndürür.|
|[Veritabanları-Güncelleştir](/rest/api/sql/databases/update)|Var olan bir veritabanını güncelleştirir.|
|[Güvenlik duvarı kuralları-oluştur veya güncelleştir](/rest/api/sql/firewallrules/createorupdate)|Bir güvenlik duvarı kuralı oluşturur veya güncelleştirir.|
|[Güvenlik duvarı kuralları-Sil](/rest/api/sql/firewallrules/delete)|Bir güvenlik duvarı kuralını siler.|
|[Güvenlik duvarı kuralları-al](/rest/api/sql/firewallrules/get)|Bir güvenlik duvarı kuralı alır.|
|[Güvenlik duvarı kuralları-sunucuya göre Listele](/rest/api/sql/firewallrules/listbyserver)|Güvenlik Duvarı kurallarının bir listesini döndürür.|

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server bir veritabanını Azure SQL veritabanı 'na geçirme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı 'Na geçiş](migrate-to-database-from-sql-server.md).
- Desteklenen özellikler hakkında bilgi için bkz. [Özellikler](features-comparison.md).