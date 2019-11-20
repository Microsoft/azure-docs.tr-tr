---
title: Esnek havuzları yönetme
description: Azure SQL elastik havuzları oluşturun ve yönetin.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 4ae2e84ab5f6108de4e8236c39371cf9658809d8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823839"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Azure SQL veritabanı 'nda elastik havuzları yönetme

Elastik havuz sayesinde, elastik havuzun veritabanlarının iş yükünü işlemek için gereken kaynak miktarını ve havuza alınan her veritabanı için kaynak miktarını belirlersiniz.

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure portal: elastik havuzları ve havuza alınmış veritabanlarını yönetme

Tüm havuz ayarları tek bir yerde bulunabilir: **Havuz yapılandırma** dikey penceresi. Buradan ulaşmak için portalda bir elastik havuz bulun ve dikey pencerenin en üstünden ya da soldaki kaynak menüsünden **havuzu Yapılandır** ' a tıklayın.

Buradan, aşağıdaki değişikliklerin herhangi bir birleşimini oluşturabilir ve tümünü tek bir toplu işte kaydedebilirsiniz:

1. Havuzun hizmet katmanını değiştirme
2. Performansı (DTU veya Vçekirdekler) ve depolamayı yukarı veya aşağı ölçeklendirin
3. Havuza/havuzdan veritabanları ekleme veya kaldırma
4. Havuzlardaki veritabanları için en az (garantili) ve en fazla performans sınırı ayarlayın
5. Yeni seçimlerinizin bir sonucu olarak faturanızda yapılan tüm değişiklikleri görüntülemek için maliyet özetini gözden geçirin

![Elastik havuz yapılandırma dikey penceresi](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: elastik havuzları ve havuza alınmış veritabanlarını yönetme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

SQL veritabanı elastik havuzları ve havuza alınmış veritabanlarını Azure PowerShell oluşturup yönetmek için aşağıdaki PowerShell cmdlet 'lerini kullanın. PowerShell 'i yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-az-ps). Elastik havuz için SQL veritabanı sunucularını oluşturmak ve yönetmek için bkz. [SQL veritabanı sunucuları oluşturma ve yönetme](sql-database-servers.md). Güvenlik duvarı kuralları oluşturmak ve yönetmek için bkz. [PowerShell kullanarak güvenlik duvarı kuralları oluşturma ve yönetme](sql-database-firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> PowerShell örnek betikleri için bkz. [Azure SQL veritabanında BIR SQL elastik havuzunu izlemek ve ölçeklendirmek için](scripts/sql-database-monitor-and-scale-pool-powershell.md)bkz. [elastik havuzlar oluşturma ve veritabanlarını havuzlar arasında ve havuza taşıma](scripts/sql-database-move-database-between-pools-powershell.md) ve PowerShell kullanma.
>

| Cmdlet | Açıklama |
| --- | --- |
|[New-Azsqtalaçıkartma havuzu](/powershell/module/az.sql/new-azsqlelasticpool)|Elastik havuz oluşturur.|
|[Get-Azsqtalaçıkartma havuzu](/powershell/module/az.sql/get-azsqlelasticpool)|Elastik havuzları ve bunların özellik değerlerini alır.|
|[Set-Azsqtalaçıkartma havuzu](/powershell/module/az.sql/set-azsqlelasticpool)|Elastik havuzun özelliklerini değiştirir Örneğin, bir elastik havuzun en büyük depolama alanını değiştirmek için **Storagemb** özelliğini kullanın.|
|[Remove-Azsqtalaçıkartma havuzu](/powershell/module/az.sql/remove-azsqlelasticpool)|Elastik havuzu siler.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Elastik havuzdaki işlemlerin durumunu alır|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Mevcut bir havuzda veya tek bir veritabanı olarak yeni bir veritabanı oluşturur. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Bir veya daha fazla veritabanını alır.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Bir veritabanının özelliklerini ayarlar veya mevcut bir veritabanını elastik havuzlar içine veya dışına taşıdıkça.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Bir veritabanını kaldırır.|

> [!TIP]
> Elastik havuzda birçok veritabanının oluşturulması, her seferinde yalnızca tek bir veritabanı oluşturan portal veya PowerShell cmdlet 'leri kullanılarak yapıldığında zaman alabilir. Bir elastik havuzda oluşturma işlemini otomatik hale getirmek için bkz. [Createorupdateelahapoolandpopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLı: elastik havuzları ve havuza alınmış veritabanlarını yönetme

[Azure CLI](/cli/azure)Ile SQL veritabanı elastik havuzları oluşturup yönetmek Için AŞAĞıDAKI [Azure CLI SQL veritabanı](/cli/azure/sql/db) komutlarını kullanın. CLI’yi tarayıcınızda çalıştırmak için [Cloud Shell](/azure/cloud-shell/overview) kullanın veya macOS, Linux ya da Windows’da [yükleyin](/cli/azure/install-azure-cli).

> [!TIP]
> Azure CLı örnek betikleri için bkz. [CLI kullanarak BIR SQL elastik havuzunda Azure SQL veritabanını taşıma](scripts/sql-database-move-database-between-pools-cli.md) ve Azure [SQL VERITABANı 'nda bir SQL elastik havuzunu ÖLÇEKLENDIRMEK Için Azure CLI kullanma](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Açıklama |
| --- | --- |
|[az SQL elastik havuz oluşturma](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Elastik havuz oluşturur.|
|[az SQL elastik havuz listesi](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Bir sunucudaki elastik havuzların listesini döndürür.|
|[az SQL elastik havuz listesi-DBS](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Elastik havuzdaki veritabanlarının listesini döndürür.|
|[az SQL elastik havuz listesi-sürümleri](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Ayrıca kullanılabilir havuz DTU ayarlarını, depolama sınırlarını ve veritabanı başına ayarları içerir. Ayrıntı düzeyini azaltmak için, ek depolama sınırları ve veritabanı başına ayarlar varsayılan olarak gizlidir.|
|[az SQL elastik havuz güncelleştirmesi](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Elastik havuzu güncelleştirir.|
|[az SQL elastik havuz silme](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Elastik havuzu siler.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: havuza alınmış veritabanlarını yönetme

Mevcut elastik havuzlarda veritabanları oluşturup taşımak veya Transact-SQL ile bir SQL veritabanı elastik havuzu hakkında bilgi döndürmek için aşağıdaki T-SQL komutlarını kullanın. Bu komutları, Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)veya bir Azure SQL veritabanı sunucusuna BAĞLANABILECEK ve Transact-SQL komutlarını geçirebilmeniz gereken herhangi bir programı kullanarak verebilirsiniz. T-SQL kullanarak güvenlik duvarı kuralları oluşturmak ve yönetmek için bkz. [Transact-SQL kullanarak güvenlik duvarı kurallarını yönetme](sql-database-firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Transact-SQL kullanarak Azure SQL veritabanı elastik havuzu oluşturamaz, güncelleştiremez veya silemezsiniz. Elastik bir havuzdan veritabanları ekleyebilir veya kaldırabilirsiniz ve var olan elastik havuzlarla ilgili bilgi döndürmek için DMVs 'yi kullanabilirsiniz.
>

| Komut | Açıklama |
| --- | --- |
|[VERITABANı oluşturma (Azure SQL veritabanı)](/sql/t-sql/statements/create-database-azure-sql-database)|Mevcut bir havuzda veya tek bir veritabanı olarak yeni bir veritabanı oluşturur. Yeni bir veritabanı oluşturmak için ana veritabanına bağlı olmanız gerekir.|
| [ALTER DATABASE (Azure SQL veritabanı)](/sql/t-sql/statements/alter-database-azure-sql-database) |Bir veritabanını elastik havuzlar içine veya dışına taşıyın.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Bir veritabanını siler.|
|[sys. elastic_pool_resource_stats (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Bir SQL veritabanı sunucusundaki tüm elastik havuzların kaynak kullanım istatistiklerini döndürür. Her elastik havuz için, her 15 saniyelik raporlama penceresi için bir satır vardır (dakikada dört satır). Buna CPU, GÇ, günlük, depolama alanı tüketimi ve havuzdaki tüm veritabanları tarafından eşzamanlı istek/oturum kullanımı dahildir.|
|[sys. database_service_objectives (Azure SQL veritabanı)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL veritabanı veya Azure SQL veri ambarı için sürüm (hizmet katmanı), hizmet hedefi (Fiyatlandırma Katmanı) ve elastik havuz adı döndürür. Azure SQL veritabanı sunucusunda ana veritabanında oturum açarsa, tüm veritabanlarına ilişkin bilgileri döndürür. Azure SQL veri ambarı için ana veritabanına bağlı olmanız gerekir.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: elastik havuzları ve havuza alınmış veritabanlarını yönetme

SQL veritabanı elastik havuzları ve havuza alınmış veritabanları oluşturup yönetmek için bu REST API isteklerini kullanın.

| Komut | Açıklama |
| --- | --- |
|[Elastik havuzlar-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|Yeni bir elastik havuz oluşturur veya mevcut bir elastik havuzu güncelleştirir.|
|[Elastik havuzlar-Sil](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|Elastik havuzu siler.|
|[Elastik havuzlar-al](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|Elastik havuz alır.|
|[Elastik havuzlar-sunucuya göre Listele](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Bir sunucudaki elastik havuzların listesini döndürür.|
|[Elastik havuzlar-Güncelleştir](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|Mevcut bir elastik havuzu güncelleştirir.|
|[Elastik havuz etkinlikleri](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|Elastik havuz etkinliklerini döndürür.|
|[Elastik havuz veritabanı etkinlikleri](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|Elastik havuzun içindeki veritabanları üzerinde etkinlik döndürür.|
|[Veritabanları-oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|Yeni bir veritabanı oluşturur veya var olan bir veritabanını güncelleştirir.|
|[Veritabanları-al](https://docs.microsoft.com/rest/api/sql/databases/get)|Bir veritabanını alır.|
|[Veritabanları-elastik havuza göre Listele](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|Elastik havuzdaki veritabanlarının listesini döndürür.|
|[Veritabanları-sunucuya göre listeleme](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|Bir sunucudaki veritabanlarının listesini döndürür.|
|[Veritabanları-Güncelleştir](https://docs.microsoft.com/rest/api/sql/databases/update)|Var olan bir veritabanını güncelleştirir.|

## <a name="next-steps"></a>Sonraki adımlar

* Elastik havuzları kullanan SaaS uygulamalarının tasarım desenleri hakkında daha fazla bilgi edinmek için bkz. [Azure SQL Veritabanı kullanan Çok Kiracılı SaaS Uygulamaları için Tasarım Desenleri](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Elastik havuzlar kullanan bir SaaS öğreticisi için bkz. [Wingtip SaaS uygulamasına giriş](sql-database-wtp-overview.md).
