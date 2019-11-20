---
title: SQL Server yönetilen örneğe geçir
description: Bir veritabanını SQL Server örneğinden Azure SQL veritabanı yönetilen örneği 'ne geçirmeyi öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 802dfa7e3b2d0b9deac957662ac1e7604d085fd9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828085"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Azure SQL veritabanı yönetilen örneğine örnek geçişi SQL Server

Bu makalede, SQL Server 2005 veya sonraki bir sürüm örneğini [Azure SQL veritabanı yönetilen örneği](sql-database-managed-instance.md)'ne geçirmeye yönelik yöntemler hakkında bilgi edineceksiniz. Tek bir veritabanına veya elastik havuza geçiş hakkında daha fazla bilgi için bkz. [tek veya havuza alınmış bir veritabanına geçme](sql-database-cloud-migrate.md). Diğer platformlardan geçiş hakkında geçiş bilgileri için bkz. [Azure veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/).

> [!NOTE]
> Yönetilen örneği hızlı bir şekilde başlatmak ve denemek istiyorsanız, Bu sayfa yerine [hızlı başlangıç kılavuzuna](sql-database-managed-instance-quickstart-guide.md) gitmek isteyebilirsiniz. 

Yüksek düzeyde, veritabanı geçiş işlemi şöyle görünür:

![geçiş işlemi](./media/sql-database-managed-instance-migration/migration-process.png)

- Geçişlerinizi engelleyebilen engelleyici bir sorun olmadığından emin olmanız gereken [yönetilen örnek uyumluluğunu değerlendirin](#assess-managed-instance-compatibility) .
  - Bu adım, kaynak SQL Server Örneğiniz üzerinde kaynak kullanımını belirlemede [performans temeli](#create-performance-baseline) oluşturmayı da içerir. O işlem, o dağıtım için uygun boyutta yönetilen örnek kullanmak istiyorsanız ve geçişten sonra performansınces 'in etkilenmemesi durumunda gereklidir.
- [Uygulama bağlantısı seçeneklerini belirleyin](sql-database-managed-instance-connect-app.md)
- Yönetilen örneğinizin teknik özelliklerini (sanal çekirdek sayısı, bellek miktarı) ve performans katmanını (İş Açısından Kritik Genel Amaçlı) seçeceğiniz, en uygun [Boyut yönetimli bir örneğe dağıtın](#deploy-to-an-optimally-sized-managed-instance) .
- [Geçiş yöntemi ' ni seçin ve](#select-migration-method-and-migrate) çevrimdışı geçiş (yerel yedekleme/geri yükleme, veritabanı ımporte/dışarı aktarma) veya çevrimiçi geçiş (veri geçiş hizmeti, işlemsel çoğaltma) kullanarak veritabanlarınızı geçireceğiniz yere geçiş yapın.
- Performansın beklendiğinden emin olmak için [uygulamaları izleyin](#monitor-applications) .

> [!NOTE]
> Tek bir veritabanını tek bir veritabanı veya elastik havuza geçirmek için, bkz. [SQL Server veritabanını Azure SQL veritabanı 'Na geçirme](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Yönetilen örnek uyumluluğunu değerlendir

İlk olarak, yönetilen Örneğin uygulamanızın veritabanı gereksinimleriyle uyumlu olup olmadığını saptayın. Yönetilen örnek dağıtım seçeneği, şirket içinde veya sanal makinelerde SQL Server kullanan mevcut uygulamaların çoğunluğu için kolay yükseltme ve kaydırma geçişi sağlamak üzere tasarlanmıştır. Ancak bazen henüz desteklenmeyen özellikler veya yetenekler gerektirebilir, geçici çözüm uygulama maliyeti de çok yüksek olabilir.

Azure SQL veritabanında veritabanı işlevselliğini etkileyen olası uyumluluk sorunlarını algılamak için [Data Migration Yardımcısı (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) kullanın. DMA, yönetilen örneği henüz geçiş hedefi olarak desteklemez, ancak Azure SQL veritabanı 'na karşı değerlendirme çalıştırmak ve bildirilen özellik eşliği ve ürün belgelerine karşı uyumluluk sorunları listesini dikkatle gözden geçirmeniz önerilir. Azure SQL veritabanı 'na geçişi engelleyen engelleyici sorunların çoğu yönetilen örnekle kaldırıldığından, bu nedenle [Azure SQL veritabanı özellikleri](sql-database-features.md) 'ne göz atın. Örneğin, çapraz veritabanı sorguları, aynı örnekteki veritabanları arası işlemler, bağlantılı sunucu diğer SQL kaynakları, CLR, genel geçici tablolar, örnek düzeyi görünümler, Hizmet Aracısı ve benzer özellikler yönetilen örneklerde mevcuttur.

Yönetilen örnek dağıtımı seçeneği ile kaldırılmayan bazı raporlanan engelleyici sorunlar varsa, [Azure sanal makinelerinde SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)gibi alternatif bir seçeneği göz önünde bulundurmanız gerekebilir. İşte bazı örnekler:

- İşletim sistemine veya dosya sistemine doğrudan erişim istiyorsanız, örneğin, SQL Server ile aynı sanal makineye üçüncü taraf veya özel aracılar yüklemek için.
- FILESTREAM/FileTable, PolyBase ve platformlar arası işlemler gibi hala desteklenmeyen özelliklerde kesin bağımlılığı varsa.
- Kesinlikle SQL Server belirli bir sürümünde (örneğin, 2012) kalması gerekiyorsa.
- İşlem gereksinimleriniz, yönetilen örnek tekliflerinin çok daha düşükse (örneğin, bir sanal çekirdek, örnek için) ve veritabanı birleştirme kabul edilebilir seçenek değildir.

Belirlenen tüm geçiş engelleyicilerini çözümledikten ve yönetilen örneğe geçişe devam ederseniz, bazı değişikliklerin iş yükünüzün performansını etkileyebileceğini unutmayın:
- Zorunlu tam kurtarma modeli ve düzenli otomatik yedekleme zamanlaması, düzenli aralıklarla basit/toplu olarak günlüğe kaydedilmiş bir model kullandıysanız veya yedeklemeleri istek üzerine durdurduktan sonra iş yükünüzün veya bakım/ETL eylemlerinin performansını etkileyebilir.
- İzleme bayrakları veya uyumluluk düzeyleri gibi farklı sunucu veya veritabanı düzeyi yapılandırma
- Saydam veritabanı şifreleme (TDE) veya otomatik yük devretme grupları gibi kullandığınız yeni özellikler CPU ve GÇ kullanımını etkileyebilir.

Yönetilen örnek, kritik senaryolarda bile% 99,99 kullanılabilirlik garantisi, bu nedenle bu özelliklerden kaynaklanan ek yük devre dışı bırakılamaz. Daha fazla bilgi için, [SQL Server ve yönetilen örnek üzerinde farklı performansa neden olabilecek kök nedenleri](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)bölümüne bakın.

### <a name="create-performance-baseline"></a>Performans temeli oluştur

Yönetilen örnekteki iş yükünüzün performansını SQL Server üzerinde çalışan orijinal iş yükünüze göre karşılaştırmak istiyorsanız, karşılaştırma için kullanılacak bir performans temeli oluşturmanız gerekir. 

Performans temeli, ortalama/en fazla CPU kullanımı, Ortalama/maksimum disk GÇ gecikme süresi, aktarım hızı, ıOPS, ortalama/en fazla sayfa ömrü erkeklerin, tempdb 'nin ortalama en büyük boyutu gibi parametrelerin bir kümesidir. Geçişten sonra benzer veya hatta daha iyi parametrelere sahip olmak istiyorsunuz. bu nedenle, bu parametrelerin taban çizgisi değerlerini ölçmek ve kaydetmek önemlidir. Sistem parametrelerine ek olarak, iş yükünüzün temsili sorguları veya en önemli sorgular kümesini seçmeniz ve seçili sorgular için min/Ortalama/maksimum süreyi ölçmenize gerek duyarsınız. Bu değerler, yönetilen örnek üzerinde çalışan iş yükünün performansını, kaynak SQL Server özgün değerlerle karşılaştırmanızı sağlar.

SQL Server Örneğinizde ölçmeye ihtiyacınız olan parametrelerden bazıları şunlardır: 
- [SQL Server Örneğiniz ÜZERINDEKI CPU kullanımını izleyin](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) ve ortalama ve en yoğun CPU kullanımını kaydedin.
- [SQL Server Örneğiniz üzerinde bellek kullanımını izleyin](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) ve arabellek havuzu, plan önbelleği, sütun deposu havuzu, [bellek içi OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)vb. gibi farklı bileşenler tarafından kullanılan bellek miktarını saptayın. Ayrıca, sayfa ömrü erkeklerin bellek performans sayacının ortalama ve en yüksek değerlerini de bulmanız gerekir.
- [Sys. dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) View veya [Performance sayaçlarını](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)kullanarak kaynak SQL Server örneğinde disk GÇ kullanımını izleyin.
- SQL Server 2016 + sürümünden geçiş yapıyorsanız dinamik yönetim görünümlerini veya sorgu deposunu inceleyerek iş yükünü ve sorgu performansını veya SQL Server örneğinizi izleyin. Yönetilen örnek üzerinde çalışan sorgularla karşılaştırmak için iş yükünüzün en önemli sorgularının ortalama süresini ve CPU kullanımını belirler.

> [!Note]
> Yüksek CPU kullanımı, sabit bellek baskısı, tempdb veya parametrization sorunları gibi SQL Server iş yükünüz ile ilgili herhangi bir sorun olduğunu fark ederseniz, taban çizgisini ve geçişi gerçekleştirmeden önce bunları kaynak SQL Server Örneğinizde çözmeyi denemelisiniz. Herhangi bir yeni sistem ile ilgili sorunları bildirmek, beklenmeyen sonuçlara neden olur ve tüm performans karşılaştırmasını geçersiz kılar.

Bu etkinliğin bir sonucu olarak, kaynak sisteminizdeki CPU, bellek ve GÇ kullanımı için Ortalama ve en yüksek değerler, ayrıca iş yükünüzün en çok baskın ve en kritik sorguların ortalama ve en fazla süresi ve CPU kullanımı için belgelenmiş bir değer elde etmeniz gerekir. Yönetilen örnekteki iş yükünüzün performansını, kaynak SQL Server iş yükünün temel performansına göre karşılaştırmak için bu değerleri daha sonra kullanmanız gerekir.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>En iyi boyuta sahip yönetilen örneğe dağıtın

Yönetilen örnek, buluta geçiş yapmayı planlayan şirket içi iş yükleri için tasarlanmıştır. İş yükleriniz için doğru kaynak düzeyini seçerken daha fazla esneklik sağlayan [Yeni bir satın alma modeli](sql-database-service-tiers-vcore.md) sunar. Şirket içi dünyada, fiziksel çekirdekler ve GÇ bant genişliği kullanarak bu iş yüklerini boyutlandırmayı merak ediyor olabilirsiniz. Yönetilen örnek için satın alma modeli, sanal çekirdekleri veya ek depolama ve ıO 'ları ayrı olarak kullanılabilir olan "Vçekirdekler" temelinde temel alır. Sanal çekirdek modeli, buluttaki işlem gereksinimlerinizi ve şirket içi olarak kullandığınız şekilde anlamanız için daha basit bir yoldur. Bu yeni model, bulutta hedef ortamınızı doğru bir şekilde boyutlandırmanızı sağlar. Doğru hizmet katmanını ve özelliklerini seçmenize yardımcı olabilecek bazı genel yönergeler aşağıda açıklanmıştır:
- Temel CPU kullanımına bağlı olarak, SQL Server üzerinde kullandığınız çekirdek sayısıyla eşleşen bir yönetilen örnek sağlayabilirsiniz, bu durumda CPU özelliklerinin [yönetilen örnek yüklendiği VM özellikleriyle eşleşecek şekilde ölçeklendirilmesi gerekebilir ](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- Temel bellek kullanımı temel alınarak [, eşleşen belleği olan hizmet katmanını](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)seçin. Bellek miktarı doğrudan seçilemez, bu nedenle, eşleşen belleği olan sanal çekirdek miktarıyla yönetilen örneği seçmeniz gerekir (örneğin, 5. nesil içinde 5,1 GB/sanal çekirdek). 
- Dosya alt sisteminin temel GÇ gecikme süresine bağlı olarak Genel Amaçlı (5 MS 'den büyük gecikme) ve hizmet katmanlarını İş Açısından Kritik (3 MS 'den az gecikme) arasında seçim yapın.
- Beklenen GÇ performansını almak için, temel verimlilik temelinde verilerin veya günlük dosyalarının boyutunu önceden ayırır.

Dağıtım zamanında işlem ve depolama kaynaklarını seçebilir ve ardından [Azure Portal](sql-database-scale-resources.md)kullanarak uygulamanız için kapalı kalma süresine bildirmeden daha sonra değiştirebilirsiniz:

![Yönetilen örnek boyutlandırma](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

VNet altyapısı ve yönetilen bir örnek oluşturma hakkında bilgi edinmek için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> [Yönetilen örnek VNET gereksinimlerine](sql-database-managed-instance-connectivity-architecture.md#network-requirements)göre hedef VNET ve alt ağınızın her zaman uyumlu tutulması önemlidir. Herhangi bir uyumsuzluk, yeni örnekler oluşturmanızı veya önceden oluşturduğunuz olanları kullanmanızı engelleyebilir. [Yeni oluşturma](sql-database-managed-instance-create-vnet-subnet.md) ve [Mevcut ağları yapılandırma](sql-database-managed-instance-configure-vnet-subnet.md) hakkında daha fazla bilgi edinin.

## <a name="select-migration-method-and-migrate"></a>Geçiş yöntemini seçin ve geçirin

Yönetilen örnek dağıtım seçeneği, şirket içi veya IaaS veritabanı uygulamalarından toplu veritabanı geçişi gerektiren Kullanıcı senaryolarını hedefler. Düzenli olarak örnek düzeyi ve/veya veritabanları arası işlevleri kullanan uygulamaların arka ucu üzerinde geçiş ve kaydırma yapmanız gerektiğinde bunlar en iyi seçenektir. Bu senaryonuz söz konusu olduğunda, uygulamalarınızı yeniden mimarmadan Azure 'da ilgili bir ortama bir örnek taşıyabilirsiniz.

SQL örneklerini taşımak için dikkatle planlamanız gerekir:

- Birlikte bulunması gereken tüm veritabanlarının geçişi (aynı örnek üzerinde çalışıyor)
- Oturum açma, kimlik bilgileri, SQL Aracısı Işleri ve Işleçler ve sunucu düzeyi Tetikleyicileri dahil olmak üzere uygulamanızın bağımlı olduğu örnek düzeyi nesnelerin geçişi.

Yönetilen örnek, düzenli bir DBA etkinliklerinin bazılarını yerleşik oldukları gibi platforma atamanıza olanak tanıyan bir yönetilen hizmettir. Bu nedenle, [yüksek kullanılabilirlik](sql-database-high-availability.md) yerleşik olarak oluşturulduğu için düzenli yedeklemeler veya her zaman yapılandırma için bakım işleri gibi bazı örnek düzeyi verilerin geçirilmesi gerekmez.

Yönetilen örnek, aşağıdaki veritabanı geçiş seçeneklerini destekler (Şu anda desteklenen tek geçiş yöntemleridir):

- Azure veritabanı geçiş hizmeti-neredeyse sıfır kapalı kalma süresi ile geçiş
- Yerel `RESTORE DATABASE FROM URL`-SQL Server yerel yedeklemeleri kullanır ve bazı kapalı kalma süresi gerektirir.

### <a name="azure-database-migration-service"></a>Azure Veritabanı Geçiş Hizmeti

[Azure veritabanı geçiş hizmeti (DMS)](../dms/dms-overview.md) , birden çok veritabanı kaynağından Azure veri platformları arasında kesintisiz geçiş sağlamak için tasarlanan, tam olarak yönetilen bir hizmettir. Bu hizmet, var olan üçüncü taraf ve SQL Server veritabanlarını Azure 'a taşımak için gereken görevleri basitleştirir. Genel önizlemede dağıtım seçenekleri Azure SQL veritabanı 'ndaki veritabanlarını ve bir Azure sanal makinesinde SQL Server veritabanlarını içerir. DMS, kurumsal iş yükleriniz için önerilen geçiş yöntemidir.

Şirket içinde SQL Server SQL Server Integration Services (SSIS) kullanıyorsanız, DMS, SSIS paketlerini depolayan SSIS kataloğunu (SSSıSDB) geçirmeyi henüz desteklememektedir, Azure-SSIS Integration Runtime ancak Azure Data Factory (ADF) içinde, yönetilen bir örnekte yeni bir SSıSDB oluşturun ve ardından paketlerinizi buna yeniden dağıtabilirsiniz, bkz. [ADF 'de Azure-SSIS IR oluşturma](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

DMS için bu senaryo ve yapılandırma adımları hakkında daha fazla bilgi edinmek için bkz. Şirket [içi VERITABANıNıZı DMS kullanarak yönetilen örneğe geçirme](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>URL 'den yerel GERI yükleme

[Azure Storage](https://azure.microsoft.com/services/storage/)'da bulunan SQL Server şirket içi veya [sanal makinelerde SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)tarafından gerçekleştirilen yerel yedeklemelerin (. bak dosyaları) geri yüklenmesi, yönetilen örnek dağıtım seçeneğinin hızlı ve kolay bir şekilde çevrimdışı olmasını sağlayan temel yeteneklerden biridir Veritabanı geçişi.

Aşağıdaki diyagramda, işleme ilişkin üst düzey bir genel bakış sunulmaktadır:

![geçiş-akış](./media/sql-database-managed-instance-migration/migration-flow.png)

Aşağıdaki tabloda, çalıştırdığınız kaynak SQL Server sürümüne bağlı olarak kullanabileceğiniz yöntemlerle ilgili daha fazla bilgi verilmektedir:

|Adım|SQL altyapısı ve sürümü|Yedekleme/geri yükleme yöntemi|
|---|---|---|
|Azure depolama 'ya yedeklemeyi yerleştirme|Önceki SQL 2012 SP1 CU2 UYGULAMAZSANıZ|. Bak dosyasını doğrudan Azure Storage 'a yükleme|
||2012 SP1 CU2 UYGULAMAZSANıZ-2016|[KIMLIK bilgisi sözdizimi ile](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) kullanım dışı kullanarak doğrudan yedekleme|
||2016 ve üzeri|[SAS KIMLIK bilgisiyle](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) kullanarak doğrudan yedekleme|
|Azure depolama 'dan yönetilen örneğe geri yükleme|[SAS KIMLIK BILGISIYLE URL 'den GERI yükleme](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - [Saydam veri şifrelemesi](transparent-data-encryption-azure-sql.md) tarafından korunan bir veritabanını yerel geri yükleme seçeneği kullanılarak yönetilen bir örneğe geçirirken, şirket Içi veya ıaas SQL Server karşılık gelen sertifikanın veritabanı geri yüklemeden önce geçirilmesi gerekir. Ayrıntılı adımlar için bkz. [TDE sertifikayı yönetilen örneğe geçirme](sql-database-managed-instance-migrate-tde-certificate.md)
> - Sistem veritabanlarının geri yüklenmesi desteklenmiyor. Örnek düzeyi nesneleri (ana veya msdb veritabanlarında depolanır) geçirmek için, bunları, hedef örnekte betiğe ve T-SQL betiklerini çalıştırmaya önerilir.

Bir veritabanı yedeklemesinin SAS kimlik bilgilerini kullanarak yönetilen örneğe nasıl geri yükleneceğini gösteren bir hızlı başlangıç için bkz. [yedeklemeden yönetilen bir örneğe geri yükleme](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Uygulamaları izleme

Yönetilen örneğe geçişi tamamladıktan sonra, iş yükünüzün uygulama davranışını ve performansını izlemeniz gerekir. Bu işlem aşağıdaki etkinlikleri içerir:
- [Yönetilen örnekte çalışan iş yükünün performansını](#compare-performance-with-the-baseline) [, kaynak SQL Server oluşturduğunuz performans temeliyle](#create-performance-baseline)karşılaştırın.
- Olası sorunları ve gelişmeleri belirlemek için [iş yükünüzün performansını sürekli izleyin](#monitor-performance) .

### <a name="compare-performance-with-the-baseline"></a>Taban çizgisiyle performansı karşılaştırın

Başarılı geçişten hemen sonra yapmanız gereken ilk etkinlik, iş yükünün performansını temel iş yükü performansıyla karşılaştırmaktır. Bu etkinliğin amacı, yönetilen örnekteki iş yükü performansının gereksinimlerinizi karşıladığından emin olmaktır. 

Yönetilen örneğe veritabanı geçişi, çoğu durumda veritabanı ayarlarını ve özgün uyumluluk düzeyini korur. Özgün ayarlar, kaynak SQL Server karşılaştırıldığında bazı performans azalmaları riskini azaltmak için mümkün olduğunda korunur. Bir kullanıcı veritabanının uyumluluk düzeyi, geçişten önce 100 veya daha yüksek bir değere sahip ise geçişten sonra aynı kalır. Bir kullanıcı veritabanının uyumluluk düzeyi geçişten önce 90 ise, yükseltilen veritabanında, uyumluluk düzeyi 100 olarak ayarlanır ve bu, yönetilen örnekteki en düşük desteklenen uyumluluk düzeyidir. Sistem veritabanlarının uyumluluk düzeyi 140 ' dir. Yönetilen örneğe geçiş gerçekten SQL Server veritabanı altyapısının en son sürümüne geçiş yaptığından, bazı yüksek performans sorunlarından kaçınmak için iş yükünüzün performansını yeniden test etmeniz gerektiğini unutmayın.

Bir önkoşul olarak, aşağıdaki etkinlikleri tamamladığınızdan emin olun:
- Çeşitli örnek, veritabanı, temdb ayarlarını ve konfigürasyonları inceleyerek, yönetilen örnekteki ayarlarınızı kaynak SQL Server örneğindeki ayarlarla hizalayın. İlk performans karşılaştırmayı çalıştırmadan önce uyumluluk düzeyleri veya şifreleme gibi ayarları değiştirdiğinizden emin olun veya etkinleştirdiğiniz yeni özelliklerden bazılarının bazı sorguları etkileyebileceğini riski kabul edin. Geçiş risklerini azaltmak için, veritabanı uyumluluk düzeyini yalnızca performans izleme sonrasında değiştirin.
- Daha iyi performans elde etmek için dosyaların boyutunu önceden ayırma gibi [genel amaçlı için en iyi depolama uygulama kılavuzunu](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) uygulayın.
- [Yönetilen örnek ve SQL Server arasındaki performans farklılıklarına neden olabilecek]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) ve performansı etkileyebilecek riskleri tanımlayan temel ortam farklılıkları hakkında bilgi edinin.
- Yönetilen örneğiniz üzerinde etkinleştirilmiş sorgu deposunu ve otomatik ayarlamayı sakladığınızdan emin olun. Bu özellikler iş yükü performansını ölçmenize ve olası performans sorunlarını otomatik olarak düzeltmenize olanak sağlar. [Daha yeni SQL Server sürümüne yükseltme sırasında performans kararlılığını koruyun](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)bölümünde açıklandığı gibi, veritabanı uyumluluk düzeyi değişikliğinden önce ve sonra iş yükü performansı hakkında bilgi almak Için Query Store 'un en iyi aracı olarak nasıl kullanılacağını öğrenin.
Şirket içi ortamınızdan mümkün olduğunca uygun olan ortamı hazırladıktan sonra, iş yükünüzü çalıştırmaya başlayabilir ve performansı ölçebilir. Ölçüm süreci, [kaynak SQL Server iş yükü ölçülerinizin temel performansını oluştururken](#create-performance-baseline)ölçülmüş parametrelerin aynısını içermelidir.
Sonuç olarak, performans parametrelerini taban çizgisiyle karşılaştırmalı ve kritik farklılıkları belirlemeniz gerekir.

> [!NOTE]
> Çoğu durumda, yönetilen örnek ve SQL Server tam olarak eşleşen performansı sağlayamaabileceksiniz. Yönetilen örnek bir SQL Server veritabanı altyapısıdır, ancak yönetilen örnekteki altyapı ve yüksek kullanılabilirlik yapılandırması bazı farklılıklara neden olabilir. Bazı sorguların daha hızlı olabildiğini beklemeniz gerekebilir. Karşılaştırma hedefi, yönetilen örnekteki iş yükü performansının SQL Server (Ortalama) performans ile eşleştiğini doğrulamak ve performansı, özgün performansınınkiyle eşleşmeyen herhangi bir kritik sorgu olduğunu belirlemektir.

Performans karşılaştırmasının sonucu şu olabilir:
- Yönetilen örnekteki iş yükü performansı, SQL Server iş yükü performansının hizalanması veya daha iyi bir seçenektir. Bu durumda, geçişin başarılı olduğunu başarıyla onaylamışdı.
- Performans parametrelerinin ve iş yükündeki sorguların büyük çoğunluğu, performans düşüklüğü olan bazı özel durumlarla birlikte ince çalışır. Bu durumda, farkları ve bunların önemini belirlemeniz gerekir. Performansı düşürülmüş bazı önemli sorgular varsa, temel alınan SQL planlarının değiştiği veya sorguların bazı kaynak limitlerine ulaşmanız gerekir. Bu durumda hafifletme, kritik sorgulara (örneğin, değiştirilen uyumluluk düzeyi, eski kardinalite Estimator), doğrudan veya plan kılavuzlarını kullanarak bir ipucu uygulayabilir, planları etkileyebilecek istatistikleri ve dizinleri yeniden oluşturabilir ya da oluşturabilirsiniz. 
- Sorguların çoğu, kaynak SQL Server kıyasla yönetilen örnekte daha yavaştır. Bu durumda, örneğin GÇ sınırları, bellek sınırı, örnek günlük hızı sınırı vb. gibi [bazı kaynak sınırlarına ulaşmak]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) gibi farkın kök nedenlerini belirlemeyi deneyin. Farka neden olabilecek kaynak sınırları yoksa, veritabanının uyumluluk düzeyini değiştirmeyi deneyin veya eski kardinalite tahmini gibi veritabanı ayarlarını değiştirip testi yeniden başlatın. Performansı gerileyen sorguları belirlemek için yönetilen örnek veya sorgu deposu görünümleri tarafından sunulan önerileri gözden geçirin.

> [!IMPORTANT]
> Yönetilen örnek, varsayılan olarak etkinleştirilen yerleşik otomatik plan düzeltme özelliğine sahiptir. Bu özellik, yapıştırırken düzgün çalışan sorguların gelecekte azalmamasını sağlar. Temel performans ve planlar hakkında bilgi edinmek için, yönetilen örneği etkinleştirmek üzere yeni ayarları değiştirmeden önce, bu özelliğin etkinleştirildiğinden ve eski ayarlarla iş yüküyle yeterince uzun süredir yürütülmekte olduğunuzdan emin olun.

Gereksinimlerinize uygun iş yükü performansını elde edene kadar parametreleri değiştirin veya hizmet katmanlarını en iyi yapılandırmayla yakınsama olarak yükseltin.

### <a name="monitor-performance"></a>Performansı izleme

Yönetilen örnek, izleme ve sorun giderme için çok sayıda gelişmiş araç sağlar ve bunları örneğinizin performansını izlemek için kullanmanız gerekir. İzlemeniz gereken parametrelerden bazıları şunlardır:
- Örneğin CPU kullanımı, sağladığınız sanal çekirdek sayısının iş yükünüz için doğru eşleşme olduğunu belirlemektir.
- Daha [fazla bellek](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)gerekip gerekmediğini öğrenmek için yönetilen Örneğinizde sayfa ömrü erkeklerin.
- Daha iyi GÇ performansı almak için dosyaları önceden ayırmanız gerekebilecek Genel Amaçlı katmanda depolama GÇ sorunları olduğunu belirten `INSTANCE_LOG_GOVERNOR` veya `PAGEIOLATCH` gibi bekleme istatistikleri.

## <a name="leverage-advanced-paas-features"></a>Gelişmiş PaaS özelliklerinden yararlanın

Tam olarak yönetilen bir platformda olduğunuzda ve iş yükü performanslarını SQL Server iş yükü performansından eşleştirdiğini doğruladıktan sonra, SQL veritabanı hizmetinin bir parçası olarak otomatik olarak sunulan avantajlardan yararlanın. 

Geçiş sırasında yönetilen örnekte bazı değişiklikler yapmasanız bile, en son veritabanı altyapısı geliştirmelerinden faydalanmak için örneğinizi çalıştırırken yeni özelliklerden bazılarını açmanıza çok daha yüksek bir şansınız vardır. Bazı değişiklikler yalnızca [veritabanı uyumluluk düzeyi değiştirildikten](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)sonra etkinleştirilir.


Örneğin, yönetilen örnek üzerinde yedeklemeler oluşturmanız gerekmez; hizmet yedeklemeleri sizin için otomatik olarak gerçekleştirir. Yedeklemeleri zamanlama, alma ve yönetme konusunda artık endişelenmeniz gerekmez. Yönetilen örnek, bu bekletme döneminde zaman içindeki herhangi bir noktaya geri yükleme olanağını, zaman içindeki bir [noktaya kurtarma (sür)](sql-database-recovery-using-backups.md#point-in-time-restore)kullanarak sağlar. Ayrıca, [yüksek kullanılabilirlik yerleşik olarak ayarlanması](sql-database-high-availability.md) konusunda endişelenmeniz gerekmez.

Güvenliği güçlendirin, [Azure Active Directory kimlik doğrulaması](sql-database-security-overview.md), [Denetim](sql-database-managed-instance-auditing.md), [tehdit algılama](sql-database-advanced-data-security.md), [satır düzeyi güvenlik](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)ve [dinamik veri maskeleme](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) kullanmayı düşünün.

Gelişmiş Yönetim ve güvenlik özelliklerinin yanı sıra, yönetilen örnek, [iş yükünüzü izlemenize ve ayarlamanıza](sql-database-monitor-tune-overview.md)yardımcı olabilecek bir dizi gelişmiş araç sağlar. [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) , büyük bir yönetilen örnek kümesini izlemenizi ve çok sayıda örnek ve veritabanının izlenmesini merkezileştirmenizi sağlar. Yönetilen örnekteki [otomatik ayarlama](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) , SQL planı yürütme istatistiklerinizin performansını sürekli olarak izler ve belirlenen performans sorunlarını otomatik olarak düzeltir.

## <a name="next-steps"></a>Sonraki adımlar

- Yönetilen örnekler hakkında daha fazla bilgi için bkz. [yönetilen örnek nedir?](sql-database-managed-instance.md).
- Yedekten geri yükleme içeren bir öğretici için bkz. [yönetilen örnek oluşturma](sql-database-managed-instance-get-started.md).
- DMS kullanarak geçişi gösteren öğretici için, bkz. Şirket [içi VERITABANıNıZı DMS kullanarak yönetilen örneğe geçirme](../dms/tutorial-sql-server-to-managed-instance.md).  
