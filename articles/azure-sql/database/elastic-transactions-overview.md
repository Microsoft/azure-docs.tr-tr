---
title: Bulut veritabanlarında dağıtılmış işlemler (önizleme)
description: Azure SQL veritabanı ve Azure SQL yönetilen örneği ile elastik veritabanı Işlemlerine genel bakış.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793356"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Bulut veritabanlarında dağıtılmış işlemler (önizleme)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL veritabanı ve Azure SQL yönetilen örneği için elastik veritabanı işlemleri, çeşitli veritabanlarına yayılan işlemleri çalıştırmanıza olanak sağlar. Esnek veritabanı işlemleri, ADO.NET kullanarak .NET uygulamaları için kullanılabilir ve [System. Transaction](/dotnet/api/system.transactions) sınıflarını kullanarak tanıdık programlama deneyimiyle tümleştirilebilir. Kitaplığı almak için, bkz. [.NET Framework 4.6.1 (Web Yükleyicisi)](https://www.microsoft.com/download/details.aspx?id=49981).
Ayrıca, yönetilen örnek için dağıtılmış işlemler [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql)' te kullanılabilir.

Şirket içinde bu tür bir senaryo genellikle Microsoft Dağıtılmış İşlem Düzenleyicisi (MSDTC) çalıştırmayı gerektirir. MSDTC, Azure 'da hizmet olarak platform uygulaması için kullanılamadığından, dağıtılmış işlemleri koordine etme özelliği artık doğrudan SQL veritabanı veya yönetilen örnekle tümleştirilmiştir. Uygulamalar dağıtılmış işlemleri başlatmak için herhangi bir veritabanına bağlanabilir ve veritabanlarından veya sunucularından biri, aşağıdaki şekilde gösterildiği gibi, dağıtılmış işlemi saydam şekilde koordine eder.

Bu belge koşullarında "dağıtılmış işlemler" ve "elastik veritabanı işlemleri", eş anlamlı kabul edilir ve birbirlerinin yerine kullanılır.

  ![Elastik veritabanı işlemlerini kullanarak Azure SQL veritabanı ile dağıtılmış işlemler ][1]

## <a name="common-scenarios"></a>Genel senaryolar

Elastik veritabanı işlemleri, uygulamaların birçok farklı veritabanında depolanan verilerde atomik değişiklikler yapmasını sağlar. Önizleme, C# ve .NET içindeki istemci tarafı geliştirme deneyimlerine odaklanmaktadır. [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) ' y i kullanarak sunucu tarafı deneyimi (saklı yordamlarda veya sunucu tarafı betiklerle yazılmış kod) yalnızca yönetilen örnek için kullanılabilir.
> [!IMPORTANT]
> Önizlemede, Azure SQL veritabanı ile Azure SQL yönetilen örneği arasında elastik veritabanı işlemlerini çalıştırmak şu anda desteklenmiyor. Elastik veritabanı işlemi, yalnızca SQL veritabanı veya yönetilen örnek kümesi arasında yayılabilir.

Elastik veritabanı işlemleri aşağıdaki senaryoları hedefler:

* Azure 'daki çok sunuculu uygulamalar: Bu senaryoyla, veriler SQL veritabanı 'nda veya yönetilen örnekte bulunan ve farklı veri türlerinin farklı veritabanlarında bulunduğu çeşitli veritabanlarına dikey olarak bölümlenmiştir. Bazı işlemler, iki veya daha fazla veritabanında tutulan verilerde değişiklik yapılmasını gerektirir. Uygulama, değişiklikleri veritabanları genelinde koordine etmek ve Atomicity sağlamak için elastik veritabanı işlemlerini kullanır.
* Azure 'da parçalı veritabanı uygulamaları: Bu senaryoyla veri katmanı, verileri SQL veritabanı veya yönetilen örnekteki birçok veritabanı genelinde yatay olarak bölümlemek için [elastik veritabanı istemci kitaplığını](elastic-database-client-library.md) veya kendi kendine parçalı veritabanını kullanır. Büyük bir kullanım durumu, kiracıların yayılmış bir çok kiracılı uygulama için atomik değişiklikler gerçekleştirme gereksinimdir. Tek bir kiracının, her ikisi de farklı veritabanlarında bulunan bir aktarım örneğini düşünün. İkinci bir durumda büyük bir kiracıya yönelik kapasite ihtiyaçlarını karşılamak için ince ayar yapılır, bu da genellikle bazı atomik işlemlerin aynı kiracı için kullanılan birkaç veritabanına uzaması gerektiğini gösterir. Üçüncü bir durum, veritabanları arasında çoğaltılan verileri başvuru verilerine yönelik atomik güncelleştirmelerdir. Atomik, işlem temelli, bu satırlardaki işlemler artık önizleme kullanılarak birkaç veritabanı arasında koordine edilebilir.
  Elastik veritabanı işlemleri, veritabanları genelinde işlem kararlılığını sağlamak için iki aşamalı tamamlama kullanır. Tek bir işlem içinde 100 'den daha az veritabanı içeren işlemlere uygun bir işlemdir. Bu sınırlar zorlanmaz, ancak bir tane, elastik veritabanı işlemleri için bu sınırları aşmadığında performans ve başarı oranları beklemelisiniz.

## <a name="installation-and-migration"></a>Yükleme ve geçiş

Elastik veritabanı işlemlerine yönelik yetenekler, .NET kitaplıkları System.Data.dll ve System.Transactions.dll güncelleştirmeleri aracılığıyla sağlanır. Dll 'Ler, kararlılık sağlamak için gerektiğinde iki aşamalı yürütmenin kullanılmasını sağlar. Elastik veritabanı işlemlerini kullanarak uygulama geliştirmeye başlamak için [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) veya sonraki bir sürümü yüklemek. .NET Framework 'ün önceki bir sürümünde çalışırken, işlemler dağıtılmış bir işleme yükseltilmeyecektir ve bir özel durum oluşur.

Yükleme sonrasında, System. Transactions içindeki dağıtılmış işlem API 'Lerini SQL veritabanı ve yönetilen örnek bağlantılarıyla kullanabilirsiniz. Bu API 'Leri kullanarak mevcut MSDTC uygulamalarınız varsa, 4.6.1 çerçevesini yükledikten sonra, .NET 4,6 için mevcut uygulamalarınızı yeniden derleyin. Projeleriniz .NET 4,6 ' i hedeflerse, yeni çerçeve sürümünden güncelleştirilmiş dll 'Leri ve SQL veritabanı bağlantıları ile birlikte dağıtılmış işlem API çağrılarını otomatik olarak kullanır veya yönetilen örnek artık başarılı olur.

Elastik veritabanı işlemlerinin MSDTC yüklemesi gerektirmediğinden emin olduğunu unutmayın. Bunun yerine, elastik veritabanı işlemleri tarafından doğrudan hizmet içinde yönetilir. Bu, bir MSDTC dağıtımı SQL veritabanı veya yönetilen örnek ile dağıtılmış işlemleri kullanmak gerekmediğinden, bulut senaryolarını önemli ölçüde basitleştirir. Bölüm 4 ' te, elastik veritabanı işlemlerini ve gerekli .NET Framework 'ü bulut uygulamalarınızla birlikte Azure 'a dağıtma hakkında daha ayrıntılı bilgi verilmektedir.

## <a name="net-installation-for-azure-cloud-services"></a>Azure Cloud Services için .NET yüklemesi

Azure, .NET uygulamalarını barındırmak için çeşitli teklifler sağlar. Farklı tekliflerin karşılaştırması [Azure App Service, Cloud Services ve sanal makine karşılaştırmasına](/azure/architecture/guide/technology-choices/compute-decision-tree)göre sunulmaktadır. Teklifin Konuk işletim sistemi elastik işlemler için gerekli .NET 4.6.1 'den küçükse Konuk işletim sistemini 4.6.1 'e yükseltmeniz gerekir.

Azure App Service için, Konuk işletim sistemi yükseltmeleri Şu anda desteklenmemektedir. Azure sanal makineler için, VM 'de oturum açıp en son .NET Framework için yükleyiciyi çalıştırmanız yeterlidir. Azure Cloud Services için, dağıtımınızın başlangıç görevlerine daha yeni bir .NET sürümünün yüklenmesini eklemeniz gerekir. Kavramlar ve adımlar, [.net 'i bir bulut hizmeti rolü üzerine yüklemiştir](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

.NET 4.6.1 yükleyicisinin, .NET 4,6 için yükleyiciden farklı olarak Azure Cloud Services 'ta önyükleme işlemi sırasında daha fazla geçici depolama gerektirebileceğini unutmayın. Yüklemenin başarılı olmasını sağlamak için, aşağıdaki örnekte gösterildiği gibi, LocalResources bölümündeki ServiceDefinition. csdef dosyanızdaki Azure bulut hizmetinizin geçici depolama alanını ve başlangıç göreviniz ortam ayarlarını artırmanız gerekir:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET geliştirme deneyimi

### <a name="multi-database-applications"></a>Çoklu veritabanı uygulamaları

Aşağıdaki örnek kod, .NET System. Transactions ile tanıdık programlama deneyimini kullanır. TransactionScope sınıfı .NET 'te bir ortam işlemi oluşturur. ("Çevresel işlem" geçerli iş parçacığında bulunan bir işlemdir.) TransactionScope içinde açılan tüm bağlantılar işleme katılır. Farklı veritabanları katılılırsa, işlem otomatik olarak dağıtılmış bir işleme yükseltilir. İşlemin sonucu, bir yürütmeyi göstermek için kapsamın tamamlanacağı şekilde ayarlanarak denetlenir.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Parçalı veritabanı uygulamaları

SQL veritabanı ve yönetilen örnek için elastik veritabanı işlemleri Ayrıca, genişletilmiş bir veri katmanının bağlantılarını açmak için elastik veritabanı istemci kitaplığının OpenConnectionForKey yöntemini kullandığınız dağıtılmış işlemleri koordine etme desteğini de destekler. Birçok farklı parçalı anahtar değeri arasındaki değişiklikler için işlem tutarlılığını garanti etmeniz gereken durumları göz önünde bulundurun. Farklı parçalama anahtar değerlerini barındıran parçaların bağlantıları OpenConnectionForKey kullanılarak aracılı yapılır. Genel durumda, bağlantı, işlem garantilerinin dağıtılmış bir işlem gerektirdiğini sağlayan farklı parçalar olabilir.
Aşağıdaki kod örneğinde bu yaklaşım gösterilmektedir. Bu, shardmap adlı bir değişkenin, elastik veritabanı istemci kitaplığından parça haritasını temsil etmek için kullanıldığını varsayar:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Transact-SQL geliştirme deneyimi

Transact-SQL kullanan sunucu tarafı dağıtılmış işlemler yalnızca Azure SQL yönetilen örneği için kullanılabilir. Dağıtılmış işlem yalnızca aynı [sunucu güven grubuna](../managed-instance/server-trust-group-overview.md)ait yönetilen örnekler arasında yürütülebilir. Bu senaryoda, yönetilen örneklerin birbirlerine başvurmak için [bağlı sunucu](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) kullanması gerekir.

Aşağıdaki örnek Transact-SQL kodu dağıtılmış işlemi başlatmak için [Dağıtılmış Işlem kullanmaya başla](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) ' yı kullanır.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>.NET ve Transact-SQL geliştirme deneyimini birleştirme

System. Transaction sınıfları kullanan .NET uygulamaları, TransactionScope sınıfını Transact-SQL ifadesiyle, dağıtılmış Işlem Başlat ile birleştirebilir. TransactionScope içinde, BEGIN DITRIBUTED IŞLEMINI yürüten iç işlem, açıkça dağıtılmış işleme yükseltilir. Ayrıca, ikinci SqlConnecton, TransactionScope içinde açıldığında, dağıtılmış işleme örtülü olarak yükseltilir. Dağıtılmış işlem başlatıldıktan sonra, sonraki tüm işlem istekleri .NET veya Transact-SQL ' den geliyor olsun, üst dağıtılmış işleme katılır. Sonuç olarak, BEGIN deyimi tarafından başlatılan tüm iç içe işlem kapsamları aynı işlem içinde sona erdirmek ve COMMıT/ROLLBACK deyimlerinin genel sonuç üzerinde şu etkiye sahip olacaktır:
 * COMMıT ifadesinin BEGIN ifadesiyle başlatılan işlem kapsamı üzerinde hiçbir etkisi olmayacaktır, diğer bir deyişle, Any () yöntemi TransactionScope nesnesinde çağrılmadan önce hiçbir sonuç uygulanmayacaktır. TransactionScope nesnesi tamamlanmadan önce yok edildiğinde, kapsam içinde yapılan tüm değişiklikler geri alınır.
 * ROLLBACK deyimleri tüm TransactionScope 'un geri alınmasına neden olur. TransactionScope içindeki yeni işlemlere yönelik tüm girişimler daha sonra başarısız olur ve bu da TransactionScope nesnesi üzerinde () öğesini çağırma girişiminde bulunur.

Bu, işlemin Transact-SQL ile dağıtılmış işleme açıkça yükseltilme örneğidir.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

Aşağıdaki örnek, TransactionScope içinde ikinci SqlConnecton başlatıldıktan sonra dağıtılmış işleme örtük olarak yükseltilen bir işlem gösterir.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Azure SQL veritabanı için birden çok sunucu arasında işlem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Esnek veritabanı işlemleri, Azure SQL veritabanı 'ndaki farklı sunucular arasında desteklenir. İşlemler çapraz sunucu sınırları sırasında, katılım sunucularının ilk olarak karşılıklı iletişim ilişkisine girilmesi gerekir. İletişim ilişkisi kurulduktan sonra, iki sunucudan herhangi bir veritabanı diğer sunucudaki veritabanları ile elastik işlemlere katılabilir. İkiden fazla sunucuyu kapsayan işlemler sayesinde, herhangi bir sunucu çifti için bir iletişim ilişkisinin olması gerekir.

Elastik veritabanı işlemleri için çapraz sunucu iletişim ilişkilerini yönetmek üzere aşağıdaki PowerShell cmdlet 'lerini kullanın:

* **New-AzSqlServerCommunicationLink**: Azure SQL veritabanı 'nda iki sunucu arasında yeni bir iletişim ilişkisi oluşturmak için bu cmdlet 'i kullanın. İlişki simetrik olduğundan, her iki sunucunun da diğer sunucu ile işlemleri başlatabileceği anlamına gelir.
* **Get-AzSqlServerCommunicationLink**: var olan iletişim ilişkilerini ve bunların özelliklerini almak için bu cmdlet 'i kullanın.
* **Remove-AzSqlServerCommunicationLink**: var olan bir iletişim ilişkisini kaldırmak için bu cmdlet 'i kullanın.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Azure SQL yönetilen örneği için birden çok sunucu arasında işlem

Dağıtılmış işlemler, Azure SQL yönetilen örneği 'nde farklı sunucular arasında desteklenir. İşlemler, yönetilen örnek sınırları sırasında, katılım örneklerinin ilk olarak bir karşılıklı güvenlik ve iletişim ilişkisine girilmesi gerekir. Bu işlem, Azure portal gerçekleştirilen bir [sunucu güven grubu](../managed-instance/server-trust-group-overview.md)oluşturularak yapılır. Yönetilen örnekler aynı sanal ağ üzerinde değilse, [sanal ağ](../../virtual-network/virtual-network-peering-overview.md) eşlemesinin ayarlanması gerekir ve ağ güvenlik grubu gelen ve giden kurallarının tüm katılan sanal ağlarda 5024 ve 11000-12000 bağlantı noktalarına izin verilmesi gerekir.

  ![Azure portalında sunucu güven grupları][3]

Aşağıdaki diyagramda, .NET veya Transact-SQL ile dağıtılmış işlemleri yürütebileceği yönetilen örneklere sahip sunucu güven grubu gösterilmektedir.

  ![Esnek işlemler kullanılarak Azure SQL yönetilen örneği ile dağıtılmış işlemler][2]

## <a name="monitoring-transaction-status"></a>İşlem durumunu izleme

Devam eden elastik veritabanı işlemlerinizin durumunu ve ilerlemesini izlemek için dinamik yönetim görünümlerini (DMVs) kullanın. İşlemler ile ilgili tüm DMVs 'ler, SQL veritabanı ve yönetilen örnekteki dağıtılmış işlemler için geçerlidir. Bunlara karşılık gelen DMVs listesini buradan bulabilirsiniz: [Işlemle Ilgili dinamik yönetim görünümleri ve işlevleri (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Bu DMVs 'ler özellikle yararlıdır:

* **sys.DM \_ Tran \_ etkin \_ işlemler**: etkin olan işlemleri ve bunların durumlarını listeler. UOW (Iş birimi) sütunu, aynı dağıtılmış işleme ait farklı alt işlemleri tanımlayabilir. Aynı dağıtılmış işlem içindeki tüm işlemler aynı UOW değerini taşır. Daha fazla bilgi için bkz. [DMV belgeleri](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql).
* **sys.DM \_ Tran \_ veritabanı \_ işlemleri**: işlemin günlüğe yerleştirilmesi gibi işlemler hakkında ek bilgiler sağlar. Daha fazla bilgi için bkz. [DMV belgeleri](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql).
* **sys.DM \_ Tran \_ kilitleri**: devam eden işlemler tarafından şu anda tutulan kilitler hakkında bilgi sağlar. Daha fazla bilgi için bkz. [DMV belgeleri](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

## <a name="limitations"></a>Sınırlamalar

Aşağıdaki sınırlamalar şu anda SQL veritabanındaki elastik veritabanı işlemleri için geçerlidir:

* Yalnızca SQL veritabanı 'ndaki veritabanları arasındaki işlemler desteklenir. Diğer [X/açık XA](https://en.wikipedia.org/wiki/X/Open_XA) kaynak SAĞLAYıCıLARı ve SQL veritabanı dışındaki veritabanları, elastik veritabanı işlemlerine katılamaz. Diğer bir deyişle, elastik veritabanı işlemlerinin şirket içi SQL Server ve Azure SQL veritabanı arasında genişlemeyeceği anlamına gelir. Şirket içi dağıtılmış işlemler için MSDTC kullanmaya devam edin.
* Yalnızca bir .NET uygulamasından alınan istemci ile eşgüdümlü işlemler desteklenir. BAŞLANGıÇ DAĞıTıM IŞLEMI gibi T-SQL için sunucu tarafı desteği planlanmaktadır, ancak henüz kullanılamamaktadır.
* WCF hizmetleri arasında işlem desteklenmez. Örneğin, bir işlemi yürüten bir WCF hizmeti yönteminiz vardır. Çağrıyı bir işlem kapsamı içinde kapsayan bir [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception)olarak başarısız olur.

Aşağıdaki sınırlamalar şu anda yönetilen örnekteki dağıtılmış işlemler için geçerlidir:

* Yalnızca yönetilen örnekteki veritabanları arasındaki işlemler desteklenir. Diğer [X/açık XA](https://en.wikipedia.org/wiki/X/Open_XA) kaynak sağlayıcıları ve Azure SQL yönetilen örneği dışındaki veritabanları dağıtılmış işlemlere katılamaz. Bu, dağıtılmış işlemlerin şirket içi SQL Server ve Azure SQL yönetilen örneği arasında genişlemeyeceği anlamına gelir. Şirket içi dağıtılmış işlemler için MSDTC kullanmaya devam edin.
* WCF hizmetleri arasında işlem desteklenmez. Örneğin, bir işlemi yürüten bir WCF hizmeti yönteminiz vardır. Çağrıyı bir işlem kapsamı içinde kapsayan bir [System. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception)olarak başarısız olur.
* Dağıtılmış işleme katılabilmek için Azure SQL yönetilen örneği bir [sunucu güven grubunun](../managed-instance/server-trust-group-overview.md) parçası olmalıdır.
* [Sunucu güven gruplarının](../managed-instance/server-trust-group-overview.md) sınırlamaları dağıtılmış işlemleri etkiler.
* Dağıtılmış işlemlere katılan yönetilen örneklerin özel uç noktalar üzerinden bağlantısı olması gerekir (dağıtıldığı sanal ağdan özel IP adresi kullanılarak) ve özel FQDN 'Ler kullanılarak karşılıklı olarak başvurulmaları gerekir. İstemci uygulamaları, Özel uç noktalarında dağıtılmış işlemleri kullanabilir. Ayrıca, Transact-SQL ' i özel uç noktalara başvuran bağlı sunuculardan yararlantığında, istemci uygulamaları da genel uç noktalarında dağıtılmış işlemleri kullanabilir. Bu sınırlama aşağıdaki diyagramda açıklanmaktadır.
  ![Özel uç nokta bağlantı sınırlaması][4]
## <a name="next-steps"></a>Sonraki adımlar

* Sorular için, [SQL veritabanı Için Microsoft Q&soru sayfasında](/answers/topics/azure-sql-database.html)bize ulaşın.
* Özellik istekleri için bunları [SQL veritabanı geri bildirim Forumu](https://feedback.azure.com/forums/217321-sql-database/) veya [yönetilen örnek forumuna](https://feedback.azure.com/forums/915676-sql-managed-instance)ekleyin.



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
