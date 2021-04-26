---
title: Veritabanı kopyalama
description: Azure SQL veritabanı 'nda aynı sunucuda veya farklı bir sunucuda, mevcut bir veritabanının işlemsel olarak tutarlı bir kopyasını oluşturun.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: b7084ef045d14b9715c41bb9ffa483d1f2f7bedf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865162"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Azure SQL veritabanı 'nda bir veritabanının işlemsel olarak tutarlı bir kopyasını kopyalama

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL veritabanı, aynı sunucuda veya farklı bir sunucuda var olan bir [veritabanının](single-database-overview.md) kopyasını oluşturmak için çeşitli yöntemler sağlar. Azure portal, PowerShell, Azure CLı veya T-SQL ' i kullanarak bir veritabanını kopyalayabilirsiniz.

## <a name="overview"></a>Genel Bakış

Veritabanı kopyası, kopyalama isteği başlatıldıktan sonra zaman içindeki bir noktaya göre kaynak veritabanının işlemsel olarak tutarlı bir anlık görüntüsüdür. Kopya için aynı sunucuyu veya farklı bir sunucuyu seçebilirsiniz. Ayrıca, yedek yedekliliği, hizmet katmanını ve kaynak veritabanının işlem boyutunu tutmayı veya aynı veya farklı bir hizmet katmanı içinde farklı bir yedek depolama yedekliği ve/veya işlem boyutunu kullanmayı seçebilirsiniz. Kopyalama işlemi tamamlandıktan sonra, tamamen işlevsel, bağımsız bir veritabanı haline gelir. Kopyalanmış veritabanındaki oturumlar, kullanıcılar ve izinler, kaynak veritabanından bağımsız olarak yönetilir. Kopya, coğrafi çoğaltma teknolojisi kullanılarak oluşturulur. Çoğaltma dengeli dağıtımı tamamlandıktan sonra, coğrafi çoğaltma bağlantısı otomatik olarak sonlandırılır. Coğrafi çoğaltmayı kullanmayla ilgili tüm gereksinimler veritabanı kopyalama işlemine de uygulanır. Ayrıntılar için bkz. [etkin coğrafi Çoğaltmaya genel bakış](active-geo-replication-overview.md) .

> [!NOTE]
> Azure SQL veritabanı yapılandırılabilir yedekleme depolama yedekliği Şu anda Brezilya Güney ' de genel önizlemede mevcuttur ve genel olarak yalnızca Güneydoğu Asya Azure bölgesinde kullanılabilir. Önizlemede, kaynak veritabanı yerel olarak yedekli veya bölgesel olarak yedekli yedek depolama yedeklemesiyle oluşturulduysa, farklı bir Azure bölgesindeki bir sunucuya veritabanı kopyalama desteklenmez. 

## <a name="logins-in-the-database-copy"></a>Veritabanı kopyasında oturum açma işlemleri

Bir veritabanını aynı sunucuya kopyaladığınızda, her iki veritabanında da aynı oturum açma işlemleri kullanılabilir. Veritabanını kopyalamak için kullandığınız güvenlik sorumlusu yeni veritabanında veritabanı sahibi olur.

Bir veritabanını farklı bir sunucuya kopyaladığınızda, hedef sunucuda kopyalama işlemini başlatan güvenlik sorumlusu yeni veritabanının sahibi olur.

Hedef sunucudan bağımsız olarak, tüm veritabanı kullanıcıları, izinleri ve güvenlik tanımlayıcıları (SID 'Ler) veritabanı kopyasına kopyalanır. Veri erişimi için [Kapsanan Veritabanı kullanıcılarının](logins-create-manage.md) kullanılması, kopyalanmış veritabanının aynı kullanıcı kimlik bilgilerine sahip olmasını sağlar, böylece kopyalama tamamlandıktan sonra aynı kimlik bilgileriyle hemen erişebilirsiniz.

Veri erişiminde sunucu düzeyi oturum açma bilgilerini kullanır ve veritabanını farklı bir sunucuya kopyalarsanız, oturum açma tabanlı erişim çalışmayabilir. Bu durumun oluşmasının nedeni, oturum açma bilgilerinin hedef sunucuda mevcut olmaması veya parolalarıyla güvenlik tanımlayıcılarının (SID) farklı olması olabilir. Farklı bir sunucuya bir veritabanını kopyaladığınızda, oturum açma işlemlerini yönetme hakkında bilgi edinmek için bkz. [Azure SQL veritabanı güvenliğini olağanüstü durum kurtarma sonrasında yönetme](active-geo-replication-security-configure.md). Farklı bir sunucuya kopyalama işlemi başarılı olduktan sonra ve diğer kullanıcılar yeniden eşlenmeden önce, yalnızca veritabanı sahibiyle ilişkili oturum açma veya Sunucu Yöneticisi kopyalanmış veritabanında oturum açabilir. Kopyalama işlemi tamamlandıktan sonra oturum açma işlemlerini çözümlemek ve veri erişimi oluşturmak için bkz. [oturum açma bilgilerini çözümleme](#resolve-logins).

## <a name="copy-using-the-azure-portal"></a>Azure portalını kullanarak kopyalama

Azure portal kullanarak bir veritabanını kopyalamak için veritabanınızın sayfasını açın ve ardından **Kopyala**' ya tıklayın.

   ![Veritabanı kopyalama](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>PowerShell veya Azure CLı kullanarak kopyalama

Bir veritabanını kopyalamak için aşağıdaki örnekleri kullanın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell için, [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet 'ini kullanın.

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. AzureRM modülü, en az Aralık 2020 ' e kadar hata düzeltmeleri almaya devam edecektir.  Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Uyumluluklarını hakkında daha fazla bilgi için bkz. [new Azure PowerShell konusuna giriş az Module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Veritabanı kopyası zaman uyumsuz bir işlemdir ancak hedef veritabanı, istek kabul edildikten hemen sonra oluşturulur. Hala devam ederken kopyalama işlemini iptal etmeniz gerekiyorsa, [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 'ini kullanarak hedef veritabanını bırakın.

Tüm örnek bir PowerShell betiği için bkz. [veritabanını yeni bir sunucuya kopyalama](scripts/copy-database-to-new-server-powershell.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Veritabanı kopyası zaman uyumsuz bir işlemdir ancak hedef veritabanı, istek kabul edildikten hemen sonra oluşturulur. Hala devam ederken kopyalama işlemini iptal etmeniz gerekirse, [az SQL DB Delete](/cli/azure/sql/db#az_sql_db_delete) komutunu kullanarak hedef veritabanını bırakın.

* * *

## <a name="copy-using-transact-sql"></a>Transact-SQL kullanarak kopyalama

Sunucu Yöneticisi oturum açma veya kopyalamak istediğiniz veritabanını oluşturan oturum açma ile ana veritabanında oturum açın. Veritabanı kopyalama işleminin başarılı olması için, sunucu yöneticisi olmayan oturum açma rollerinin üyesi olmalıdır `dbmanager` . Oturumlar ve sunucuya bağlanma hakkında daha fazla bilgi için bkz. [oturum açma bilgilerini yönetme](logins-create-manage.md).

Kaynak veritabanını CREATE DATABASE ile kopyalamaya başla [... Deyimin KOPYASı olarak](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) . T-SQL deyimleri, veritabanı kopyalama işlemi tamamlanana kadar çalışmaya devam eder.

> [!NOTE]
> T-SQL ifadesini sonlandırmak, veritabanı kopyalama işlemini sonlandırmaz. İşlemi sonlandırmak için hedef veritabanını bırakın.
>

> [!IMPORTANT]
> T-SQL CREATE DATABASE kullanılırken yedek depolama yedekliliği seçiliyor... Komutun KOPYASı henüz desteklenmiyor. 

### <a name="copy-to-the-same-server"></a>Aynı sunucuya Kopyala

Sunucu Yöneticisi oturum açma veya kopyalamak istediğiniz veritabanını oluşturan oturum açma ile ana veritabanında oturum açın. Veritabanı kopyalama işleminin başarılı olması için, sunucu yöneticisi olmayan oturum açma rollerinin üyesi olmalıdır `dbmanager` .

Bu komut, Veritabanı1 adlı yeni bir veritabanına aynı sunucuda Veritabanı2 kopyalar. Veritabanınızın boyutuna bağlı olarak kopyalama işleminin tamamlanması biraz zaman alabilir.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Elastik havuza Kopyala

Sunucu Yöneticisi oturum açma veya kopyalamak istediğiniz veritabanını oluşturan oturum açma ile ana veritabanında oturum açın. Veritabanı kopyalama işleminin başarılı olması için, sunucu yöneticisi olmayan oturum açma rollerinin üyesi olmalıdır `dbmanager` .

Bu komut, Pool1 adlı esnek havuzda Veritabanı1 adlı yeni bir veritabanına kopyalar. Veritabanınızın boyutuna bağlı olarak kopyalama işleminin tamamlanması biraz zaman alabilir.

Veritabanı1 tek veya havuza alınmış bir veritabanı olabilir. Farklı katman havuzları arasında kopyalama desteklenir, ancak bazı çapraz katman kopyaları başarılı olmayacaktır. Örneğin, tek veya elastik standart bir veritabanını genel amaçlı bir havuza kopyalayabilirsiniz, ancak standart bir elastik veritabanını bir Premium havuza kopyalayamazsınız. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Farklı bir sunucuya Kopyala

Yeni veritabanının oluşturulacağı hedef sunucunun ana veritabanında oturum açın. Kaynak sunucudaki kaynak veritabanının veritabanı sahibiyle aynı ada ve parolaya sahip bir oturum açma kullanın. Hedef sunucudaki oturum açma rolünün de rolün bir üyesi olması `dbmanager` veya Sunucu Yöneticisi oturum açması gerekir.

Bu komut Sunucu1 üzerindeki Veritabanı1 öğesini Sunucu2 üzerinde Veritabanı2 adlı yeni bir veritabanına kopyalar. Veritabanınızın boyutuna bağlı olarak kopyalama işleminin tamamlanması biraz zaman alabilir.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Her iki sunucu güvenlik duvarı da T-SQL CREATE VERITABANıNı veren istemcinin IP 'sinden gelen bağlantıya izin verecek şekilde yapılandırılmalıdır... Komutun KOPYASı olarak.

### <a name="copy-to-a-different-subscription"></a>Farklı bir aboneliğe kopyalama

Veritabanınızı, T-SQL kullanarak farklı bir abonelikte bulunan bir sunucuya kopyalamak için [SQL veritabanını farklı bir sunucuya kopyalama](#copy-to-a-different-server) bölümündeki adımları kullanabilirsiniz. Kaynak veritabanının veritabanı sahibiyle aynı ada ve parolaya sahip bir oturum açma kullandığınızdan emin olun. Ayrıca, oturum açma, `dbmanager` hem kaynak hem de hedef sunucularda rolün bir üyesi veya bir Sunucu Yöneticisi olmalıdır.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> [Azure Portal](https://portal.azure.com), PowerShell ve Azure CLI, farklı bir aboneliğe veritabanı kopyalamayı desteklemez.

> [!TIP]
> T-SQL kullanarak veritabanı kopyalama, bir veritabanının farklı bir Azure kiracısındaki abonelikten kopyalanmasını destekler. Bu yalnızca hedef sunucuda oturum açmak için bir SQL kimlik doğrulaması oturum açma işlemi kullanılırken desteklenir.

## <a name="monitor-the-progress-of-the-copying-operation"></a>Kopyalama işleminin ilerlemesini izleme

[Sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql), [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)ve [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) görünümlerini sorgulayarak kopyalama işlemini izleyin. Kopyalama işlemi devam ederken, yeni veritabanı için sys. databases görünümünün **state_desc** sütunu **kopyalama** olarak ayarlanır.

* Kopyalama başarısız olursa, yeni veritabanı için sys. databases görünümünün **state_desc** sütunu **şüpheli** olarak ayarlanır. DROP ifadesini yeni veritabanında yürütün ve daha sonra yeniden deneyin.
* Kopyalama başarılı olursa, yeni veritabanı için sys. databases görünümünün **state_desc** sütunu **çevrimiçi** olarak ayarlanır. Kopyalama tamamlanmıştır ve yeni veritabanı, kaynak veritabanından bağımsız olarak değiştirilebilen normal bir veritabanıdır.

> [!NOTE]
> Kopyalama işlemi devam ederken iptal etmeyi seçerseniz, yeni veritabanında [drop database](/sql/t-sql/statements/drop-database-transact-sql) ifadesini yürütün.

> [!IMPORTANT]
> Kaynaktan önemli ölçüde daha küçük bir hizmet hedefine sahip bir kopya oluşturmanız gerekiyorsa, hedef veritabanı dengeli işlem işlemini tamamlamaya yetecek kaynaklara sahip olmayabilir ve kopyalama işleminin başarısız olmasına neden olabilir. Bu senaryoda, farklı bir sunucuda ve/veya farklı bir bölgede bir kopya oluşturmak için bir coğrafi geri yükleme isteği kullanın. Daha fazla bilgi için bkz. [veritabanı yedeklerini kullanarak Azure SQL veritabanını kurtarma](recovery-using-backups.md#geo-restore) .

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Azure RBAC rolleri ve veritabanı kopyasını yönetme izinleri

Bir veritabanı kopyası oluşturmak için aşağıdaki rollerde olması gerekir

* Abonelik sahibi veya
* SQL Server katkıda bulunan rolü veya
* Kaynak ve hedef veritabanlarında aşağıdaki izinle özel rol:

   Microsoft. SQL/Servers/veritabanları/Microsoft. SQL/Servers/veritabanlarını/Write 'i okuyun

Bir veritabanı kopyasını iptal etmek için, aşağıdaki rollerde olması gerekir

* Abonelik sahibi veya
* SQL Server katkıda bulunan rolü veya
* Kaynak ve hedef veritabanlarında aşağıdaki izinle özel rol:

   Microsoft. SQL/Servers/veritabanları/Microsoft. SQL/Servers/veritabanlarını/Write 'i okuyun

Azure portal kullanarak veritabanı kopyasını yönetmek için aşağıdaki izinlere de ihtiyacınız olacaktır:

   Microsoft. resources/abonelikleri/kaynakları/Microsoft. resources/abonelikleri/kaynakları/yaz Microsoft. resources/dağıtımlar/okuma Microsoft. resources/dağıtımlar/Write Microsoft. resources/dağıtımlar/operationdurumlar/Read

Portalda kaynak grubunda bulunan ve SQL işlemleri de dahil olmak üzere birden çok kaynak sağlayıcılarındaki işlemler altında gerçekleştirilen işlemleri görmek isterseniz, bu ek izinlere ihtiyacınız olacaktır:

   Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/işlemler/okuma Microsoft. resources/abonelikler/ResourceGroups/dağıtımlar/operationdurumlarının/Read

## <a name="resolve-logins"></a>Oturum açma işlemlerini çözümle

Yeni veritabanı hedef sunucuda çevrimiçi olduktan sonra, kullanıcıları yeni veritabanından hedef sunucudaki oturum açmayla yeniden eşlemek için [alter User](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) deyimini kullanın. Yalnız bırakılmış kullanıcıları çözümlemek için bkz. [yalnız bırakılmış kullanıcılarda sorun giderme](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server). Ayrıca bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](active-geo-replication-security-configure.md).

Yeni veritabanındaki tüm kullanıcılar, kaynak veritabanında sahip oldukları izinleri korurlar. Veritabanı kopyasını Başlatan Kullanıcı, yeni veritabanının veritabanı sahibi olur. Kopyalama başarılı olduktan ve diğer kullanıcılar yeniden eşlendikten sonra, yalnızca veritabanı sahibi yeni veritabanında oturum açabilir.

Bir veritabanını başka bir sunucuya kopyaladığınızda kullanıcıları ve oturum açma işlemlerini yönetme hakkında bilgi edinmek için bkz. [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](active-geo-replication-security-configure.md).

## <a name="database-copy-errors"></a>Veritabanı kopyalama hataları

Azure SQL veritabanı 'nda bir veritabanı kopyalanırken aşağıdaki hatalarla karşılaşılabilir. Daha fazla bilgi için bkz. [Azure SQL Veritabanını kopyalama](database-copy.md).

| Hata kodu | Önem Derecesi | Açıklama |
| ---:| ---:|:--- |
| 40635 |16 |IP adresi '%. &#x2a;ls ' olan istemci geçici olarak devre dışı. |
| 40637 |16 |Veritabanı kopyası oluşturma şu anda devre dışı. |
| 40561 |16 |Veritabanı kopyalama başarısız oldu. Kaynak ya da hedef veritabanı yok. |
| 40562 |16 |Veritabanı kopyalama başarısız oldu. Kaynak veritabanı bırakılmış. |
| 40563 |16 |Veritabanı kopyalama başarısız oldu. Hedef veritabanı bırakılmış. |
| 40564 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40565 |16 |Veritabanı kopyalama başarısız oldu. Aynı kaynaktan 1 ' den fazla eşzamanlı veritabanı kopyası yapılmasına izin verilmez. Lütfen hedef veritabanını bırakın ve daha sonra yeniden deneyin. |
| 40566 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40567 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40568 |16 |Veritabanı kopyalama başarısız oldu. Kaynak veritabanı kullanılamaz duruma geldi. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40569 |16 |Veritabanı kopyalama başarısız oldu. Hedef veritabanı kullanılamaz duruma geldi. Lütfen hedef veritabanını bırakın ve yeniden deneyin. |
| 40570 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve daha sonra yeniden deneyin. |
| 40571 |16 |Veritabanı kopyalama bir iç hata nedeniyle başarısız oldu. Lütfen hedef veritabanını bırakın ve daha sonra yeniden deneyin. |

## <a name="next-steps"></a>Sonraki adımlar

* Oturumlar hakkında daha fazla bilgi için bkz. [oturum açma bilgilerini yönetme](logins-create-manage.md) ve [olağanüstü durum kurtarma sonrasında Azure SQL veritabanı güvenliğini yönetme](active-geo-replication-security-configure.md).
* Bir veritabanını dışarı aktarmak için bkz. [veritabanını BACPAC 'e aktarma](database-export.md).
