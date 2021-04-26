---
title: Bellek içi teknolojiler
description: Bellek içi teknolojiler, Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde işlem ve analiz iş yüklerinin performansını önemli ölçüde artırır.
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 48b74a5507eb4a1d48b7bf70133e476a30fe8169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779960"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL veritabanı ve Azure SQL yönetilen örneği 'nde bellek içi teknolojileri kullanarak performansı iyileştirin
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Bellek içi teknolojiler uygulamanızın performansını iyileştirebilmeniz ve veritabanınızın maliyetini büyük olasılıkla azaltmanızı sağlar.

## <a name="when-to-use-in-memory-technologies"></a>Bellek içi teknolojiler ne zaman kullanılır?

Bellek içi teknolojileri kullanarak, çeşitli iş yükleriyle performans iyileştirmeleri elde edebilirsiniz:

- **İşlem** (çevrimiçi işlem Işleme (OLTP)) çoğu istek, daha küçük veri kümesini okur veya güncelleştirir (ÖRNEĞIN, CRUD işlemleri).
- Sorguların çoğunun raporlama amaçları için karmaşık hesaplamaları olduğu, mevcut tablolara veri yükleyen ve ekleyen (toplu yükleme olarak adlandırılır) veya tablolardaki verileri silen **analitik** (çevrimiçi analitik Işleme (OLAP)).
- Aynı veri kümesinde hem OLTP hem de OLAP sorgularının yürütüldüğü **karma** (karma işlem/analitik Işleme (htap)).

Bellek içi teknolojiler, bu iş yüklerinin performansını, sorguların yerel derlemesini kullanarak veya temel donanımda kullanılabilen toplu işleme ve SıMD yönergeleri gibi gelişmiş işlemleri kullanarak, belleğe işlenmesi gereken verileri koruyarak iyileştirebilirler.

## <a name="overview"></a>Genel Bakış

Azure SQL veritabanı ve Azure SQL yönetilen örneği aşağıdaki bellek içi teknolojilere sahiptir:

- *[Bellek ıçı OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* , saniye başına işlem sayısını artırır ve işlem işleme gecikmesini azaltır. In-Memory OLTP 'tan faydalanabilir senaryolar: ticari ve oyun gibi yüksek performanslı işlem işleme, olaylardan veya IoT cihazlarından veri alımı, önbelleğe alma, veri yükleme ve geçici tablo ve tablo değişken senaryolarıdır.
- *Kümelenmiş columnstore dizinleri* , depolama alanı parmak izini (10 kez) azaltır ve raporlama ve analiz sorguları için performansı geliştirir. Veritabanınıza daha fazla veri sığdırmak ve performansı artırmak için veri reyonlarınızdaki olgu tabloları ile birlikte kullanabilirsiniz. Ayrıca, onu arşivlemek ve 10 kat daha fazla veri sorgulayabilmeniz için işletimsel veritabanınızdaki geçmiş verilerle birlikte kullanabilirsiniz.
- ITAP *kümelenmemiş columnstore dizinleri* , İşletimsel veritabanını doğrudan sorgulayarak, pahalı bir ayıklama, dönüştürme ve yükleme (ETL) işlemini çalıştırmaya ve veri ambarının doldurulmasını beklemeniz gerekmeden, işinize gerçek zamanlı Öngörüler elde etmenize yardımcı olur. Kümelenmemiş columnstore dizinleri, OLTP veritabanındaki analiz sorgularının hızla yürütülmesine olanak sağlarken, işlemsel iş yükünde oluşan etkiyi azaltır.
- HTAP için *bellek için iyileştirilmiş kümelenmiş columnstore dizinleri* , hızlı işlem işlemleri yapmanızı ve analiz sorgularını aynı verilere göre çok hızlı bir *şekilde çalıştırmanızı sağlar* .

Hem columnstore dizinleri hem de In-Memory OLTP, sırasıyla 2012 ve 2014 ' den beri SQL Server ürünün bir parçasıdır. Azure SQL veritabanı, Azure SQL yönetilen örneği ve SQL Server bellek içi teknolojilerin aynı uygulamasını paylaşır.

## <a name="benefits-of-in-memory-technology"></a>Bellek içi teknolojinin avantajları

Daha verimli sorgu ve işlem işleme nedeniyle, bellek içi teknolojiler de maliyeti azaltmanıza yardımcı olur. Performans kazancı elde etmek için genellikle veritabanının fiyatlandırma katmanını yükseltmeniz gerekmez. Bazı durumlarda, fiyatlandırma katmanını azaltabilir, ancak bellek içi teknolojilerle performans iyileştirmeleri görmeye devam edebilirsiniz.

In-Memory OLTP 'nin performansı önemli ölçüde iyileştirmesine yardımcı olan iki örneği şunlardır:

- In-Memory OLTP kullanarak, [çekirdek Iş çözümleri, %70 oranında DTU 'ları geliştirirken iş yükünü ikiye katırdi](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu).
- Aşağıdaki videoda örnek bir iş yüküyle kaynak tüketimine ilişkin önemli bir geliştirme gösterilmektedir: [bellek ıçı OLTP videosu](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB). Daha fazla bilgi için bkz. blog gönderisi: [bellek ıçı OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Bellek içi teknolojiler Premium ve İş Açısından Kritik katmanlarında kullanılabilir.

Aşağıdaki videoda, bellek içi teknolojilerle ilgili potansiyel performans kazançları açıklanmaktadır. Gördüğünüz performans kazanmanın her zaman, iş yükünün ve verilerin doğası, veritabanının erişim deseninin ve benzeri birçok etkene bağlı olduğunu unutmayın.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

Bu makalede, Azure SQL veritabanı ve Azure SQL yönetilen örneği 'ne özgü In-Memory OLTP ve columnstore dizinlerinin yönleri açıklanmakta ve örnekler de yer almaktadır:

- Bu teknolojilerin depolama ve veri boyutu sınırları üzerinde etkisini görürsünüz.
- Farklı fiyatlandırma katmanları arasında bu teknolojileri kullanan veritabanlarının hareketini yönetme hakkında bilgi edineceksiniz.
- In-Memory OLTP 'ın yanı sıra columnstore dizinlerinin kullanımını gösteren iki örnek görürsünüz.

SQL Server bellek içi hakkında daha fazla bilgi için bkz.:

- [Bellek ıçı OLTP genel bakış ve kullanım senaryoları](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (müşteri örnek olay incelemeleri ve kullanmaya başlamak için bilgiler içerir)
- [In-Memory OLTP için belgeler](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Columnstore dizinleri Kılavuzu](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [Gerçek zamanlı işlemsel analiz](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics) olarak da bilinen karma işlem/analitik Işleme (htap)

## <a name="in-memory-oltp"></a>Bellek İçi OLTP

In-Memory OLTP teknolojisi, tüm verileri bellekte tutarak son derece hızlı veri erişim işlemleri sağlar. Ayrıca OLTP iş yükünün performansını artırmak için özel dizinleri, sorguların yerel derlemesini ve manuca veri erişimini de kullanır. In-Memory OLTP verilerinizi düzenlemenin iki yolu vardır:

- Her satırın ayrı bir bellek nesnesi olduğu **bellek için iyileştirilmiş rowstore** biçimi. Bu, yüksek performanslı OLTP iş yükleri için iyileştirilmiş, klasik bir In-Memory OLTP biçimidir. Bellek için iyileştirilmiş rowstore biçiminde kullanılabilecek, bellek için iyileştirilmiş iki tür tablo vardır:

  - Bellekte bulunan satırların sunucu yeniden başlatıldıktan sonra korunduğu *dayanıklı tablolar* (SCHEMA_AND_DATA). Bu tür tablolar, bellek içi iyileştirmelerin ek avantajları ile geleneksel bir rowstore tablosu gibi davranır.
  - Yeniden başlatıldıktan sonra satırların korunmayan, *dayanıklı olmayan tablolar* (SCHEMA_ONLY). Bu tür bir tablo, geçici veriler (örneğin, geçici tabloları değiştirme) veya kalıcı bir tabloya taşımadan önce verileri hızlı bir şekilde yüklemeniz gereken tablolar (hazırlama tabloları olarak adlandırılır) için tasarlanmıştır.

- Verilerin sütunlu biçimde düzenlendiği, **bellek için iyileştirilmiş columnstore** biçimi. Bu yapı, OLTP iş yükünüzün çalıştığı veri yapısında analitik sorgular çalıştırmanız gereken, HTAP senaryoları için tasarlanmıştır.

> [!Note]
> In-Memory OLTP teknolojisi, bellekte tam olarak yer alan veri yapıları için tasarlanmıştır. Bellek Içi veriler diske boşaltılamıyorsa, yeterli belleğe sahip olan veritabanını kullandığınızdan emin olun. Daha fazla ayrıntı için bkz. [In-Memory OLTP Için veri boyutu ve depolama üst sınırı](#data-size-and-storage-cap-for-in-memory-oltp) .

In-Memory OLTP: hızlı [Başlangıç 1: In-Memory OLTP teknolojilerini daha hızlı T-SQL performansı için](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) oluşturma (başlamanıza yardımcı olacak başka bir makale)

Teknolojiler hakkında ayrıntılı Videolar:

- [Bellek ıçı OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (performans avantajları ve bu sonuçları kendiniz yeniden oluşturma adımları içeren)
- [Bellek içi OLTP videoları: nedir ve ne zaman/nasıl kullanılır?](/archive/blogs/sqlserverstorageengine/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it)

Belirli bir veritabanının OLTP In-Memory destekleyip desteklemediğini anlamak için programlı bir yoldur. Aşağıdaki Transact-SQL sorgusunu çalıştırabilirsiniz:

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Sorgu **1** döndürürse, bu veritabanında OLTP In-Memory desteklenir. Aşağıdaki sorgular bir veritabanının Genel Amaçlı, standart veya temel bir şekilde indirgenemez önce kaldırılması gereken tüm nesneleri belirler:

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>In-Memory OLTP için veri boyutu ve depolama alanı üst sınırı

In-Memory OLTP, Kullanıcı verilerini depolamak için kullanılan bellek için iyileştirilmiş tablolar içerir. Bu tabloların belleğe sığması gerekir. Belleği doğrudan SQL veritabanı 'nda yönetirken, Kullanıcı verileri için bir kota kavramımız vardır. Bu fikir, *bellek ıçı OLTP depolaması* olarak adlandırılır.

Desteklenen her tek veritabanı fiyatlandırma katmanı ve her elastik havuz fiyatlandırma katmanı, In-Memory OLTP depolama alanı miktarını içerir.

- [DTU tabanlı kaynak limitleri-tek veritabanı](database/resource-limits-dtu-single-databases.md)
- [DTU tabanlı kaynak limitleri-elastik havuzlar](database/resource-limits-dtu-elastic-pools.md)
- [Sanal çekirdek tabanlı kaynak limitleri-tek veritabanları](database/resource-limits-vcore-single-databases.md)
- [Sanal çekirdek tabanlı kaynak limitleri-elastik havuzlar](database/resource-limits-vcore-elastic-pools.md)
- [Sanal çekirdek tabanlı kaynak limitleri-yönetilen örnek](managed-instance/resource-limits.md)

Aşağıdaki öğeler In-Memory OLTP depolama üst sınırına doğru sayılır:

- Bellek için iyileştirilmiş tablolarda ve tablo değişkenlerinde etkin kullanıcı veri satırları. Eski satır sürümlerinin Cap 'e doğru sayılmadığını unutmayın.
- Bellek için iyileştirilmiş tablolardaki dizinler.
- ALTER TABLE işlemlerinde işletimsel ek yük.

Büyük bir süre vurmanız durumunda, bir kota dışı hata alırsınız ve artık veri ekleyemez veya güncelleştirme yapamazsınız. Bu hatayı azaltmak için, verileri silin veya veritabanının veya havuzun fiyatlandırma katmanını artırın.

In-Memory OLTP depolama kullanımını izleme ve uyarı yapılandırma hakkında daha fazla bilgi için bkz. [bellek içi depolamayı izleme](in-memory-oltp-monitor-space.md).

#### <a name="about-elastic-pools"></a>Elastik havuzlar hakkında

Elastik havuzlarla, In-Memory OLTP depolaması havuzdaki tüm veritabanları arasında paylaşılır. Bu nedenle, bir veritabanındaki kullanım diğer veritabanlarını etkileyebilir. Bunun için iki azaltıcı etken şunlardır:

- Bir `Max-eDTU` `MaxvCore` bütün olarak havuz için EDTU veya sanal çekirdek sayısından düşük olan veritabanları için veya yapılandırın. Bu en büyük boyut, havuzdaki herhangi bir veritabanında bulunan OLTP depolama kullanımını In-Memory eDTU sayısına karşılık gelen boyuta kadar büyük.
- 0 ' `Min-eDTU` dan büyük bir veya yapılandırın `MinvCore` . Bu en az, havuzdaki her bir veritabanının, yapılandırılan veya ' a karşılık gelen In-Memory OLTP depolaması miktarına sahip olmasını güvence altına alır `Min-eDTU` `vCore` .

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>In-Memory OLTP teknolojileri kullanan veritabanlarının hizmet katmanlarını değiştirme

Veritabanınızı veya örneğinizi İş Açısından Kritik Genel Amaçlı gibi daha yüksek bir katmana (veya standart olarak Premium 'a) yükseltebilirsiniz. Kullanılabilir işlev ve kaynaklar yalnızca artar.

Ancak katmanı daha eski sürüme dönüştürmek veritabanınızı olumsuz etkileyebilir. Veritabanınız In-Memory OLTP nesneleri içerdiğinde İş Açısından Kritik, Genel Amaçlı (veya Premium ile standart ya da temel) ' dan düşürülen bir etkisi olur. Bellek için iyileştirilmiş tablolar, düşürme sonrasında (görünür kalsalar bile) kullanılamaz. Aynı noktalar, elastik bir havuzun fiyatlandırma katmanını azaltdığınızda veya bir veritabanını bellek içi teknolojilerle Genel Amaçlı, standart veya temel elastik havuza taşırken geçerlidir.

> [!Important]
> In-Memory OLTP Genel Amaçlı, standart veya temel katmanda desteklenmez. Bu nedenle, herhangi bir In-Memory OLTP nesnesine sahip bir veritabanını bu katmanlardan birine taşımak mümkün değildir.

Veritabanını Genel Amaçlı, standart veya temel sürümüne indirgediğinizde, tüm bellek için iyileştirilmiş tabloları ve tablo türlerini ve yerel koda derlenmiş T-SQL modüllerini kaldırın.

*İş açısından kritik katmanındaki ölçek azaltma kaynakları*: bellek için iyileştirilmiş tablolardaki veriler, veritabanının veya yönetilen örneğin katmanıyla ILIŞKILI In-Memory OLTP depolama alanına sığmalıdır ya da elastik havuzda kullanılabilir. Katmanı ölçeklendirmeye veya veritabanını daha fazla kullanılabilir In-Memory OLTP depolaması olmayan bir havuza taşımaya çalışırsanız, işlem başarısız olur.

## <a name="in-memory-columnstore"></a>Bellek içi columnstore

Bellek içi columnstore teknolojisi, tablolarda büyük miktarda veriyi depolamanızı ve sorgulamanızı sağlar. Columnstore teknolojisi, sütun tabanlı veri depolama biçimini ve toplu sorgu işlemeyi kullanarak, OLAP iş yüklerindeki geleneksel satır odaklı depolama üzerinden sorgu performansının en fazla 10 kata kadar elde etmelerini sağlar. Ayrıca, sıkıştırılmamış veri boyutu üzerinde veri sıkıştırmasının 10 kata kadar kazanç elde edebilirsiniz.
Verilerinizi düzenlemek için kullanabileceğiniz iki tür columnstore modeli vardır:

- Tablodaki tüm verilerin sütunlu biçimde düzenlendiği **kümelenmiş columnstore** . Bu modelde, tablodaki tüm satırlar, verileri yüksek ölçüde sıkıştıran ve tablo üzerinde hızlı analitik sorgular ve raporlar çalıştırmanızı sağlayan sütunlu biçimde yerleştirilir. Verilerinizin doğasına bağlı olarak, verilerinizin boyutu 10 x-100x olarak azalabilir. Kümelenmiş columnstore modeli, 100 ' den fazla veri kümesi diskte depolanmadan önce sıkıştırıldığından büyük miktarda verinin (toplu yük) hızlı bir şekilde içe alımını da mümkün tutar. Bu model, klasik veri ambarı senaryoları için iyi bir seçenektir.
- Verilerin geleneksel rowstore tablosunda depolandığı **kümelenmemiş columnstore** ve analitik sorgular için kullanılan columnstore biçiminde bir dizin vardır. Bu model hibrit Transactional-Analytic Işlemesini (HTAP) sağlar: işlemsel iş yükünde performans gerçek zamanlı analizini çalıştırma özelliği. OLTP sorguları, küçük bir satır kümesine erişmek için iyileştirilmiş rowstore tablosunda yürütülür, ancak OLAP sorguları, taramalar ve analizler için daha iyi seçim yapan columnstore dizininde yürütülür. Sorgu iyileştiricisi, sorguya göre rowstore veya columnstore biçimini dinamik olarak seçer. Kümelenmemiş columnstore dizinleri, özgün veri kümesi hiçbir değişiklik yapılmadan orijinal rowstore tablosunda tutulduğundan verilerin boyutunu azaltmayın. Ancak, ek columnstore dizininin boyutu, denk B-ağacı dizininden daha küçük bir büyüklük sırasıyla olmalıdır.

> [!Note]
> Bellek içi columnstore teknolojisi yalnızca bellekte işlenmek üzere gereken verileri tutar, ancak belleğe sığmayan veriler disk üzerinde depolanır. Bu nedenle, bellek içi columnstore yapılarında veri miktarı kullanılabilir bellek miktarını aşabilir.

Teknoloji hakkında ayrıntılı video:

- [Columnstore dizini: Ignite 2016 'den bellek Içi analiz videoları](/archive/blogs/sqlserverstorageengine/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Columnstore dizinleri için veri boyutu ve depolama

Columnstore dizinlerinin belleğe sığması gerekmez. Bu nedenle, Dizin boyutunun tek ucu, [DTU tabanlı satın alma modeli](database/service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](database/service-tiers-vcore.md) makalelerinde belgelenen en büyük genel veritabanı boyutudur.

Kümelenmiş columnstore dizinleri kullandığınızda, temel tablo depolaması için sütunlu sıkıştırma kullanılır. Bu sıkıştırma, Kullanıcı verilerinizin depolama ayak izini önemli ölçüde azaltabilir, yani veritabanına daha fazla veri uydurabilecek anlamına gelir. Ve sıkıştırma, [sütunlu arşiv sıkıştırması](/sql/relational-databases/data-compression/data-compression#using-columnstore-and-columnstore-archive-compression)ile daha da artırılabilir. Elde ettiğiniz sıkıştırma miktarı verilerin doğasına bağlıdır, ancak sıkıştırma işlemi 10 kez daha yaygın değildir.

Örneğin, en fazla 1 terabaytlık (TB) boyutunda bir veritabanınız varsa ve columnstore dizinlerini kullanarak sıkıştırmaya 10 kez ulaşdıysanız, veritabanında toplam 10 TB Kullanıcı verisi kullanabilirsiniz.

Kümelenmemiş columnstore dizinleri kullandığınızda, temel tablo hala geleneksel rowstore biçiminde depolanır. Bu nedenle, depolama tasarrufu, kümelenmiş columnstore dizinleri gibi önemli değildir. Ancak, bir dizi geleneksel kümelenmemiş dizini tek bir columnstore diziniyle değiştiriyorsanız, tablo için depolama ayak izine ilişkin genel tasarrufları görmeye devam edebilirsiniz.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Columnstore dizinleri içeren veritabanlarının hizmet katmanlarını değiştirme

Hedef katmanınız S3 altındaysa, *tek veritabanını temel veya Standart sürüme* düşürme olanağı mümkün olmayabilir. Columnstore dizinleri yalnızca İş Açısından Kritik/Premium fiyatlandırma katmanında ve temel katmanda değil, S3 ve yukarıdaki Standart katmanda desteklenir. Veritabanınızı desteklenmeyen bir katmana veya düzeye indirgemeniz durumunda, columnstore dizininiz kullanılamaz hale gelir. Sistem columnstore dizininizi korur, ancak dizinden hiç yararlanmayın. Daha sonra desteklenen bir katmana veya düzeye yeniden yükseltirseniz, columnstore dizininiz yeniden yararlanılabilir olmaya hemen başlamaya hazırız.

**Kümelenmiş** bir columnstore dizininiz varsa, tüm tablo düşürme sonrasında kullanılamaz hale gelir. Bu nedenle, veritabanınızı desteklenmeyen bir katmana veya düzeye indirgeyebilmeniz için tüm *kümelenmiş* columnstore dizinlerini düşürmenizi öneririz.

> [!Note]
> SQL yönetilen örneği, tüm katmanlarda columnstore dizinlerini destekler.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı Başlangıç 1: daha hızlı T-SQL performansı için OLTP teknolojilerini In-Memory](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)
- [Mevcut bir Azure SQL uygulamasında In-Memory OLTP kullanma](in-memory-oltp-configure.md)
- In-Memory OLTP için [OLTP depolamasını izleme In-Memory](in-memory-oltp-monitor-space.md)
- [Bellek içi özellikleri deneme](in-memory-sample.md)

## <a name="additional-resources"></a>Ek kaynaklar

### <a name="deeper-information"></a>Daha derin bilgi

- [SQL veritabanı 'nda In-Memory OLTP ile DTU 'yu %70 oranında düşürürken, çekirdeğin anahtar veritabanı iş yükünü nasıl çift katına kullandığını öğrenin](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Bellek içi OLTP blog gönderisi](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [In-Memory OLTP hakkında bilgi edinin](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Columnstore dizinleri hakkında bilgi edinin](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [Gerçek zamanlı operasyonel çözümlemeler hakkında bilgi edinin](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- Bkz. [yaygın Iş yükü desenleri ve geçiş konuları](/previous-versions/dn673538(v=msdn.10)) (In-Memory OLTP yaygın olarak önemli performans kazancı sağlayan iş yükü düzenlerini açıklar)

### <a name="application-design"></a>Uygulama tasarımı

- [Bellek içi OLTP (bellek içi iyileştirme)](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [Mevcut bir Azure SQL uygulamasında In-Memory OLTP kullanma](in-memory-oltp-configure.md)

### <a name="tools"></a>Araçlar

- [Azure portalı](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)
- [SQL Server Veri Araçları (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)