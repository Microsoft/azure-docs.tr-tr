---
title: Bulut veritabanlarında dağıtılmış işlemler
description: Azure SQL veritabanı ile elastik veritabanı Işlemlerine genel bakış
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 3ca3e9074f28d66068d49b80915e98600759d9be
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568290"
---
# <a name="distributed-transactions-across-cloud-databases"></a>Bulut veritabanlarında dağıtılmış işlemler

Azure SQL veritabanı için elastik veritabanı işlemleri (SQL DB), SQL DB 'de çeşitli veritabanlarına yayılan işlemler çalıştırmanızı sağlar. SQL VERITABANı için elastik veritabanı işlemleri, ADO .NET kullanan .NET uygulamalarında kullanılabilir ve [System. Transaction](https://msdn.microsoft.com/library/system.transactions.aspx) sınıflarını kullanarak tanıdık programlama deneyimiyle tümleştirilir. Kitaplığı almak için, bkz. [.NET Framework 4.6.1 (Web Yükleyicisi)](https://www.microsoft.com/download/details.aspx?id=49981).

Şirket içinde, genellikle Microsoft Dağıtılmış İşlem Düzenleyicisi (MSDTC) çalıştırması gereken bir senaryo. MSDTC, Azure 'da hizmet olarak platform uygulaması için kullanılamadığından, dağıtılmış işlemleri koordine etme özelliği artık SQL DB ile doğrudan tümleşiktir. Uygulamalar dağıtılmış işlemleri başlatmak için herhangi bir SQL veritabanına bağlanabilir ve veritabanlarından biri, aşağıdaki şekilde gösterildiği gibi, dağıtılmış işlemi saydam şekilde koordine eder. 

  ![Elastik veritabanı işlemlerini kullanarak Azure SQL veritabanı ile dağıtılmış işlemler ][1]

## <a name="common-scenarios"></a>Genel senaryolar

SQL DB için elastik veritabanı işlemleri, uygulamaların birçok farklı SQL veritabanında depolanan verilerde atomik değişiklikler yapmasını sağlar. Önizleme, ve .NET içindeki C# istemci tarafı geliştirme deneyimlerine odaklanmaktadır. T-SQL kullanan sunucu tarafı deneyimi daha sonra planlanmaktadır.  
Elastik veritabanı işlemleri aşağıdaki senaryoları hedefler:

* Azure 'da çoklu veritabanı uygulamaları: Bu senaryoyla, veriler farklı veritabanlarında farklı türlerdeki verilerin bulunduğu gibi SQL DB 'deki çeşitli veritabanlarına dikey olarak bölümlenmiştir. Bazı işlemler, iki veya daha fazla veritabanında tutulan verilerde değişiklik yapılmasını gerektirir. Uygulama, değişiklikleri veritabanları genelinde koordine etmek ve Atomicity sağlamak için elastik veritabanı işlemlerini kullanır.
* Azure 'da parçalı veritabanı uygulamaları: Bu senaryoyla veri katmanı, verileri SQL VERITABANı 'ndaki birçok veritabanı arasında yatay olarak bölümlemek için [elastik veritabanı istemci kitaplığını](sql-database-elastic-database-client-library.md) veya kendi kendine parçalı veritabanını kullanır. Büyük bir kullanım durumu, kiracıların yayılmış bir çok kiracılı uygulama için atomik değişiklikler gerçekleştirme gereksinimdir. Tek bir kiracının, her ikisi de farklı veritabanlarında bulunan bir aktarım örneğini düşünün. İkinci bir durumda, genellikle bazı atomik işlemlerin aynı kiracı için kullanılan çeşitli veritabanlarını uzatması gerektiğini belirten büyük bir kiracı için kapasite gereksinimlerini karşılamak üzere ince ayar yapılır. Üçüncü bir durum, veritabanları arasında çoğaltılan verileri başvuru verilerine yönelik atomik güncelleştirmelerdir. Atomik, işlem temelli, bu satırlardaki işlemler artık önizleme kullanılarak birkaç veritabanı arasında koordine edilebilir.
  Elastik veritabanı işlemleri, veritabanları genelinde işlem kararlılığını sağlamak için iki aşamalı tamamlama kullanır. Tek bir işlem içinde 100 ' den az veritabanı içeren işlemlere uygun bir işlemdir. Bu sınırlar zorlanmaz, ancak bir tane, elastik veritabanı işlemleri için bu sınırları aşmadığında performans ve başarı oranları beklemelisiniz.

## <a name="installation-and-migration"></a>Yükleme ve geçiş

SQL DB 'de elastik veritabanı işlemlerine yönelik yetenekler, .NET kitaplıkları System. Data. dll ve System. Transactions. dll güncelleştirmeleri aracılığıyla sağlanır. Dll 'Ler, kararlılık sağlamak için gerektiğinde iki aşamalı yürütmenin kullanılmasını sağlar. Elastik veritabanı işlemlerini kullanarak uygulama geliştirmeye başlamak için [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) veya sonraki bir sürümü yüklemek. .NET Framework 'ün önceki bir sürümünde çalışırken, işlemler dağıtılmış bir işleme yükseltilmeyecektir ve bir özel durum oluşur.

Yükleme sonrasında, System. Transactions içindeki dağıtılmış işlem API 'Lerini SQL DB bağlantısı ile birlikte kullanabilirsiniz. Bu API 'Leri kullanarak mevcut MSDTC uygulamalarınız varsa, 4.6.1 çerçevesini yükledikten sonra, .NET 4,6 için mevcut uygulamalarınızı yeniden oluşturmanız yeterlidir. Projeleriniz .NET 4,6 ' i hedeflerse, yeni çerçeve sürümünden güncelleştirilmiş dll 'Leri otomatik olarak kullanır ve SQL DB bağlantıları ile birlikte dağıtılmış işlem API çağrıları artık başarılı olur.

Elastik veritabanı işlemlerinin MSDTC yüklemesi gerektirmediğini unutmayın. Bunun yerine, elastik veritabanı işlemleri SQL DB içinde ve tarafından doğrudan yönetilir. Bu, bir MSDTC dağıtımının SQL DB ile dağıtılmış işlemleri kullanması gerekmediğinden, bulut senaryolarını önemli ölçüde basitleştirir. Bölüm 4 ' te, elastik veritabanı işlemlerini ve gerekli .NET Framework 'ü bulut uygulamalarınızla birlikte Azure 'a dağıtma hakkında daha ayrıntılı bilgi verilmektedir.

## <a name="development-experience"></a>Geliştirme deneyimi

### <a name="multi-database-applications"></a>Çoklu veritabanı uygulamaları

Aşağıdaki örnek kod, .NET System. Transactions ile tanıdık programlama deneyimini kullanır. TransactionScope sınıfı .NET 'te bir ortam işlemi oluşturur. ("Çevresel işlem" geçerli iş parçacığında bulunan bir işlemdir.) TransactionScope içinde açılan tüm bağlantılar işleme katılır. Farklı veritabanları katılılırsa, işlem otomatik olarak dağıtılmış bir işleme yükseltilir. İşlemin sonucu, bir yürütmeyi göstermek için kapsamın tamamlanacağı şekilde ayarlanarak denetlenir.

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

### <a name="sharded-database-applications"></a>Parçalı veritabanı uygulamaları

SQL DB için elastik veritabanı işlemleri Ayrıca, Genişletilebilir veritabanı istemci kitaplığının OpenConnectionForKey yöntemini kullandığınız ve genişletilmiş bir veri katmanına yönelik bağlantıları açmak için kullandığınız dağıtılmış işlemleri koordine etme desteğini destekler. Birçok farklı parçalı anahtar değeri arasındaki değişiklikler için işlem tutarlılığını garanti etmeniz gereken durumları göz önünde bulundurun. Farklı parçalama anahtar değerlerini barındıran parçaların bağlantıları OpenConnectionForKey kullanılarak aracılı yapılır. Genel durumda, bağlantı, işlem garantilerinin dağıtılmış bir işlem gerektirdiğini sağlayan farklı parçalar olabilir. Aşağıdaki kod örneğinde bu yaklaşım gösterilmektedir. Bu, shardmap adlı bir değişkenin, elastik veritabanı istemci kitaplığından parça haritasını temsil etmek için kullanıldığını varsayar:

    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }


## <a name="net-installation-for-azure-cloud-services"></a>Azure Cloud Services için .NET yüklemesi

Azure, .NET uygulamalarını barındırmak için çeşitli teklifler sağlar. Farklı tekliflerin karşılaştırması [Azure App Service, Cloud Services ve sanal makine karşılaştırmasına](/azure/architecture/guide/technology-choices/compute-decision-tree)göre sunulmaktadır. Teklifin Konuk işletim sistemi elastik işlemler için gerekli .NET 4.6.1 'den küçükse Konuk işletim sistemini 4.6.1 'e yükseltmeniz gerekir. 

Azure Uygulama Hizmetleri için, Konuk işletim sistemine yükseltmeler Şu anda desteklenmiyor. Azure sanal makineler için, VM 'de oturum açıp en son .NET Framework için yükleyiciyi çalıştırmanız yeterlidir. Azure Cloud Services için, dağıtımınızın başlangıç görevlerine daha yeni bir .NET sürümünün yüklenmesini eklemeniz gerekir. Kavramlar ve adımlar, [.net 'i bir bulut hizmeti rolü üzerine yüklemiştir](../cloud-services/cloud-services-dotnet-install-dotnet.md).  

.NET 4.6.1 yükleyicisinin, .NET 4,6 için yükleyiciden farklı olarak Azure Cloud Services 'ta önyükleme işlemi sırasında daha fazla geçici depolama gerektirebileceğini unutmayın. Yüklemenin başarılı olmasını sağlamak için, aşağıdaki örnekte gösterildiği gibi, LocalResources bölümündeki ServiceDefinition. csdef dosyanızdaki Azure bulut hizmetinizin geçici depolama alanını ve başlangıç göreviniz ortam ayarlarını artırmanız gerekir:

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

## <a name="transactions-across-multiple-servers"></a>Birden çok sunucu arasında işlem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Esnek veritabanı işlemleri, Azure SQL veritabanı 'nda farklı SQL veritabanı sunucuları arasında desteklenir. SQL veritabanı sunucu sınırları arası işlemler olduğunda, katılım sunucularının ilk olarak karşılıklı iletişim ilişkisine girilmesi gerekir. İletişim ilişkisi kurulduktan sonra, iki sunucudan herhangi bir veritabanı diğer sunucudaki veritabanları ile elastik işlemlere katılabilir. İkiden fazla SQL veritabanı sunucusunu kapsayan işlemler sayesinde, her türlü SQL veritabanı sunucusu için bir iletişim ilişkisinin yerinde olması gerekir.

Elastik veritabanı işlemleri için çapraz sunucu iletişim ilişkilerini yönetmek üzere aşağıdaki PowerShell cmdlet 'lerini kullanın:

* **New-AzSqlServerCommunicationLink**: Azure SQL veritabanı 'nda iki SQL veritabanı sunucusu arasında yeni bir iletişim ilişkisi oluşturmak için bu cmdlet 'i kullanın. İlişki, her iki sunucunun da diğer sunucu ile işlem başlatabileceği anlamına gelir.
* **Get-AzSqlServerCommunicationLink**: Varolan iletişim ilişkilerini ve bunların özelliklerini almak için bu cmdlet 'i kullanın.
* **Remove-AzSqlServerCommunicationLink**: Var olan bir iletişim ilişkisini kaldırmak için bu cmdlet 'i kullanın. 

## <a name="monitoring-transaction-status"></a>İşlem durumunu izleme

Devam eden elastik veritabanı işlemlerinizin durumunu ve ilerlemesini izlemek için SQL DB 'de dinamik yönetim görünümlerini (DMVs) kullanın. İşlemler ile ilgili tüm DMVs 'ler SQL DB 'de dağıtılmış işlemler için geçerlidir. Bunlara karşılık gelen DMVs listesini buraya bulabilirsiniz: [Işlemle Ilgili dinamik yönetim görünümleri ve işlevleri (Transact-SQL)](https://msdn.microsoft.com/library/ms178621.aspx).

Bu DMVs 'ler özellikle yararlıdır:

* **sys.DM\_Tran\_etkinİşlemler\_** : Şu anda etkin olan işlemleri ve bunların durumlarını listeler. UOW (Iş birimi) sütunu, aynı dağıtılmış işleme ait farklı alt işlemleri tanımlayabilir. Aynı dağıtılmış işlem içindeki tüm işlemler aynı UOW değerini taşır. Daha fazla bilgi için bkz. [DMV belgeleri](https://msdn.microsoft.com/library/ms174302.aspx) .
* **sys.DM\_Tran\_veritabanıişlemleri\_** : İşlem hakkında, işlemin günlüğe yerleştirilmesi gibi ek bilgiler sağlar. Daha fazla bilgi için bkz. [DMV belgeleri](https://msdn.microsoft.com/library/ms186957.aspx) .
* **sys.DM\_Tran\_kilitleri**: Devam eden işlemler tarafından şu anda tutulan kilitler hakkında bilgi sağlar. Daha fazla bilgi için bkz. [DMV belgeleri](https://msdn.microsoft.com/library/ms190345.aspx) .

## <a name="limitations"></a>Sınırlamalar

Aşağıdaki sınırlamalar şu anda SQL DB 'de elastik veritabanı işlemleri için geçerlidir:

* Yalnızca SQL DB 'deki veritabanları arasındaki işlemler desteklenir. Diğer [X/açık XA](https://en.wikipedia.org/wiki/X/Open_XA) kaynak SAĞLAYıCıLARı ve SQL DB dışındaki veritabanları elastik veritabanı işlemlerine katılamaz. Diğer bir deyişle, elastik veritabanı işlemlerinin şirket içi SQL Server ve Azure SQL veritabanı arasında genişlemeyeceği anlamına gelir. Şirket içi dağıtılmış işlemler için MSDTC kullanmaya devam edin. 
* Yalnızca bir .NET uygulamasından alınan istemci ile eşgüdümlü işlemler desteklenir. BAŞLANGıÇ DAĞıTıM IŞLEMI gibi T-SQL için sunucu tarafı desteği planlanmaktadır, ancak henüz kullanılamamaktadır. 
* WCF hizmetleri arasında işlem desteklenmez. Örneğin, bir işlemi yürüten bir WCF hizmeti yönteminiz vardır. Çağrıyı bir işlem kapsamı içinde kapsayan bir [System. ServiceModel. ProtocolException](https://msdn.microsoft.com/library/system.servicemodel.protocolexception)olarak başarısız olur.

## <a name="next-steps"></a>Sonraki adımlar

Sorularınız için lütfen [SQL veritabanı forumundan](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) bize ulaşın ve özellik istekleri için lütfen bunları [SQL veritabanı geri bildirim forumuna](https://feedback.azure.com/forums/217321-sql-database/)ekleyin.

<!--Image references-->
[1]: ./media/sql-database-elastic-transactions-overview/distributed-transactions.png
