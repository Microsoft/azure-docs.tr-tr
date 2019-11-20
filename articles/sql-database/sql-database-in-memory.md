---
title: Bellek içi teknolojiler
description: Azure SQL veritabanı bellek Içi teknolojiler, işlem ve analiz iş yüklerinin performansını önemli ölçüde artırır.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: de60712451d4c2e8a7d931f7a09352f55be05694
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73810256"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>SQL veritabanında bellek Içi teknolojileri kullanarak performansı iyileştirin

Azure SQL veritabanı 'nda bellek içi teknolojiler uygulamanızın performansını iyileştirebilmeniz ve veritabanınızın maliyetini büyük olasılıkla azaltmanızı sağlar. 

## <a name="when-to-use-in-memory-technologies"></a>Bellek içi teknolojiler ne zaman kullanılır?

Azure SQL veritabanı 'nda bellek Içi teknolojileri kullanarak, çeşitli iş yükleriyle performans iyileştirmeleri elde edebilirsiniz:

- **İşlem** (çevrimiçi işlem Işleme (OLTP)) çoğu istek, daha küçük veri kümesini okur veya güncelleştirir (ÖRNEĞIN, CRUD işlemleri).
- Sorguların çoğunun raporlama amaçları için karmaşık hesaplamaları olduğu analiz (çevrimiçi analitik işleme (OLAP)), var olan tablolara veri yükleyen ve ekleyen belirli bir **sorgu (toplu** yükleme olarak adlandırılır) veya verileri silme tablolar. 
- Aynı veri kümesinde hem OLTP hem de OLAP sorgularının yürütüldüğü **karma** (karma işlem/analitik Işleme (htap)).

Bellek içi teknolojiler, bu iş yüklerinin performansını iyileştirebilirler, sorguların yerel derlemesini kullanarak veya toplu işleme ve yığın işleme ve SıMD yönergeleri gibi gelişmiş işleme, temel alınan donanım. 

## <a name="overview"></a>Genel Bakış

Azure SQL veritabanı, aşağıdaki bellek Içi teknolojilere sahiptir:
- *[Bellek ıçı OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)* , saniye başına işlem sayısını artırır ve işlem işleme gecikmesini azaltır. Bellek Içi OLTP 'dan faydalanabilir senaryolar: ticari ve oyun gibi yüksek performanslı işlem işleme, olaylardan veya IoT cihazlarından veri alımı, önbelleğe alma, veri yükleme ve geçici tablo ve tablo değişken senaryolarıdır.
- *Kümelenmiş columnstore dizinleri* , depolama alanı parmak izini (10 kez) azaltır ve raporlama ve analiz sorguları için performansı geliştirir. Veritabanınıza daha fazla veri sığdırmak ve performansı artırmak için veri reyonlarınızdaki olgu tabloları ile birlikte kullanabilirsiniz. Ayrıca, onu arşivlemek ve 10 kat daha fazla veri sorgulayabilmeniz için işletimsel veritabanınızdaki geçmiş verilerle birlikte kullanabilirsiniz.
- HTAP *kümelenmemiş columnstore dizinleri* , İşletimsel veritabanını doğrudan sorgulayarak, pahalı bir ayıklama, dönüştürme ve yükleme (ETL) işlemi çalıştırmaya ve verilerin beklenmesine gerek kalmadan, İşletimsel veritabanını doğrudan sorgulama yoluyla işinizle ilgili gerçek zamanlı Öngörüler elde etmenize yardımcı olur doldurulacak ambar. Kümelenmemiş columnstore dizinleri, OLTP veritabanındaki analiz sorgularının hızla yürütülmesine olanak sağlarken, işlemsel iş yükünde oluşan etkiyi azaltır.
- HTAP için *bellek için iyileştirilmiş kümelenmiş columnstore dizinleri* , hızlı işlem işlemleri yapmanızı ve analiz sorgularını aynı verilere göre çok hızlı bir *şekilde çalıştırmanızı sağlar* .

Columnstore dizinleri ve bellek Içi OLTP, sırasıyla 2012 ve 2014 ' den beri SQL Server ürünün bir parçasıdır. Azure SQL veritabanı ve SQL Server bellek içi teknolojilerin aynı uygulamasını paylaşır. Bu teknolojiler için yeni yetenekler, SQL Server ' de yayınlanmadan önce Azure SQL veritabanı 'nda ilk olarak yayımlanmıştır.

## <a name="benefits-of-in-memory-technology"></a>Bellek Içi teknolojinin avantajları

Daha verimli sorgu ve işlem işleme nedeniyle, bellek Içi teknolojiler de maliyeti azaltmanıza yardımcı olur. Performans kazancı elde etmek için genellikle veritabanının fiyatlandırma katmanını yükseltmeniz gerekmez. Bazı durumlarda, fiyatlandırma katmanını azaltabilir, ancak bellek içi teknolojilerle performans iyileştirmeleri görmeye devam edebilirsiniz.

Aşağıda, bellek Içi OLTP 'un performansı önemli ölçüde iyileştirmesine yardımcı olacak iki örnek verilmiştir:

- Bellek Içi OLTP kullanarak, [çekirdek Iş çözümleri, %70 oranında DTU 'ları geliştirirken iş yükünü ikiye katırdi](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).

  - DTU, *veritabanı işlem birimini*gösterir ve kaynak tüketiminin bir ölçüsünü içerir.
- Aşağıdaki videoda örnek bir iş yüküyle kaynak tüketimine ilişkin önemli bir geliştirme gösterilmektedir: [Azure SQL veritabanı videosunda bellek ıçı OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
  - Daha fazla bilgi için bkz. blog gönderisi: [Azure SQL veritabanı 'Nda bellek ıçı OLTP Web günlüğü gönderisi](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> Bellek içi teknolojiler Premium ve İş Açısından Kritik katmanı Azure SQL veritabanlarında ve Premium elastik havuzlarda mevcuttur.

Aşağıdaki videoda, Azure SQL veritabanı 'nda bellek Içi teknolojilerle ilgili potansiyel performans kazançları açıklanmaktadır. Gördüğünüz performans kazanmanın her zaman, iş yükünün ve verilerin doğası, veritabanının erişim deseninin ve benzeri birçok etkene bağlı olduğunu unutmayın.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

Bu makalede, Azure SQL veritabanı 'na özgü bellek Içi OLTP ve columnstore dizinlerinin yönleri açıklanmakta ve örnekler de yer almaktadır:

- Bu teknolojilerin depolama ve veri boyutu sınırları üzerinde etkisini görürsünüz.
- Farklı fiyatlandırma katmanları arasında bu teknolojileri kullanan veritabanlarının hareketini yönetme hakkında bilgi edineceksiniz.
- Azure SQL veritabanı 'nda Ayrıca, bellek Içi OLTP ve columnstore dizinlerinin kullanımını gösteren iki örnek görürsünüz.

Daha fazla bilgi için bkz.

- [Bellek ıçı OLTP genel bakış ve kullanım senaryoları](https://msdn.microsoft.com/library/mt774593.aspx) (müşteri örnek olay incelemeleri ve kullanmaya başlamak için bilgiler içerir)
- [Bellek Içi OLTP için belgeler](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore dizinleri Kılavuzu](https://msdn.microsoft.com/library/gg492088.aspx)
- [Gerçek zamanlı işlemsel analiz](https://msdn.microsoft.com/library/dn817827.aspx) olarak da bilinen karma işlem/analitik Işleme (htap)

## <a name="in-memory-oltp"></a>Bellek içi OLTP

Bellek içi OLTP teknolojisi, tüm verileri bellekte tutarak son derece hızlı veri erişim işlemleri sağlar. Ayrıca OLTP iş yükünün performansını artırmak için özel dizinleri, sorguların yerel derlemesini ve manuca veri erişimini de kullanır. Bellek Içi OLTP verilerinizi düzenlemenin iki yolu vardır:

- Her satırın ayrı bir bellek nesnesi olduğu **bellek için iyileştirilmiş rowstore** biçimi. Bu, yüksek performanslı OLTP iş yükleri için iyileştirilmiş, klasik bir bellek Içi OLTP biçimidir. Bellek için iyileştirilmiş rowstore biçiminde kullanılabilecek, bellek için iyileştirilmiş iki tür tablo vardır:
  - Bellekte bulunan satırların sunucu yeniden başlatıldıktan sonra korunduğu *dayanıklı tablolar* (SCHEMA_AND_DATA). Bu tür tablolar, bellek içi iyileştirmelerin ek avantajları ile geleneksel bir rowstore tablosu gibi davranır.
  - Yeniden başlatıldıktan sonra satırların korunmayan, *dayanıklı olmayan tablolar* (SCHEMA_ONLY). Bu tür bir tablo, geçici veriler (örneğin, geçici tabloları değiştirme) veya kalıcı bir tabloya taşımadan önce verileri hızlı bir şekilde yüklemeniz gereken tablolar (hazırlama tabloları olarak adlandırılır) için tasarlanmıştır.
- Verilerin sütunlu biçimde düzenlendiği, **bellek için iyileştirilmiş columnstore** biçimi. Bu yapı, OLTP iş yükünüzün çalıştığı veri yapısında analitik sorgular çalıştırmanız gereken, HTAP senaryoları için tasarlanmıştır.

> [!Note]
> Bellek içi OLTP teknolojisi, bellekte tam olarak yer alan veri yapıları için tasarlanmıştır. Bellek Içi veriler diske boşaltılamıyorsa, yeterli belleğe sahip olan veritabanını kullandığınızdan emin olun. Daha fazla ayrıntı için bkz. [bellek ıçı OLTP Için veri boyutu ve depolama üst sınırı](#data-size-and-storage-cap-for-in-memory-oltp) .

Bellek Içi OLTP üzerinde hızlı bir başlangıç: hızlı [Başlangıç 1: daha hızlı T-SQL performansı Için bellek ıçı OLTP teknolojileri](https://msdn.microsoft.com/library/mt694156.aspx) (başlamanıza yardımcı olacak başka bir makale)

Teknolojiler hakkında ayrıntılı Videolar:

- [Azure SQL veritabanı 'Nda bellek ıçı OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (performans avantajları ve bu sonuçları kendiniz yeniden oluşturma adımları içeren)
- [Bellek içi OLTP videoları: nedir ve ne zaman/nasıl kullanılır?](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)

Belirli bir veritabanının bellek Içi OLTP 'Yi destekleyip desteklemediğini anlamak için programlı bir yoldur. Aşağıdaki Transact-SQL sorgusunu çalıştırabilirsiniz:
```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```
Sorgu **1**döndürürse, bu veritabanında bellek içi OLTP desteklenir. Aşağıdaki sorgular bir veritabanının standart/temel 'e indirgenme öncesinde kaldırılması gereken tüm nesneleri belirler:
```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Bellek Içi OLTP için veri boyutu ve depolama alanı üst sınırı

Bellek içi OLTP, Kullanıcı verilerini depolamak için kullanılan bellek için iyileştirilmiş tablolar içerir. Bu tabloların belleğe sığması gerekir. Belleği doğrudan SQL veritabanı hizmetinde yönetirken, Kullanıcı verileri için bir kota kavramımız vardır. Bu fikir, *bellek ıçı OLTP depolaması*olarak adlandırılır.

Desteklenen her tek veritabanı fiyatlandırma katmanı ve her elastik havuz fiyatlandırma katmanı, belirli bir bellek Içi OLTP depolama miktarı içerir. Bkz. [DTU tabanlı kaynak limitleri-tek veritabanı](sql-database-dtu-resource-limits-single-databases.md), [DTU tabanlı kaynak limitleri-elastik havuzlar](sql-database-dtu-resource-limits-elastic-pools.md),[sanal çekirdek tabanlı kaynak limitleri-tek veritabanları](sql-database-vcore-resource-limits-single-databases.md) ve [sanal çekirdek tabanlı kaynak limitleri-elastik havuzlar](sql-database-vcore-resource-limits-elastic-pools.md).

Aşağıdaki öğeler, bellek Içi OLTP depolama üst sınırına doğru sayılır:

- Bellek için iyileştirilmiş tablolarda ve tablo değişkenlerinde etkin kullanıcı veri satırları. Eski satır sürümlerinin Cap 'e doğru sayılmadığını unutmayın.
- Bellek için iyileştirilmiş tablolardaki dizinler.
- ALTER TABLE işlemlerinde işletimsel ek yük.

Büyük bir süre vurmanız durumunda, bir kota dışı hata alırsınız ve artık veri ekleyemez veya güncelleştirme yapamazsınız. Bu hatayı azaltmak için, verileri silin veya veritabanının veya havuzun fiyatlandırma katmanını artırın.

Bellek içi OLTP depolama kullanımını izleme ve ekranı neredeyse vurmak istediğinizde uyarıları yapılandırma hakkında ayrıntılı bilgi için bkz. [bellek içi depolamayı izleme](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>Elastik havuzlar hakkında

Elastik havuzlarla, bellek Içi OLTP depolaması, havuzdaki tüm veritabanları arasında paylaşılır. Bu nedenle, bir veritabanındaki kullanım diğer veritabanlarını etkileyebilir. Bunun için iki azaltıcı etken şunlardır:

- Havuz için eDTU veya vCore sayısından daha düşük olan veritabanları için bir `Max-eDTU` veya `MaxvCore` yapılandırın. Bu en büyük bellek Içi OLTP depolama kullanımı, havuzdaki tüm veritabanları, eDTU sayısına karşılık gelen boyuta kadar büyük.
- 0 ' dan büyük bir `Min-eDTU` veya `MinvCore` yapılandırın. Bu en az, havuzdaki her bir veritabanının, yapılandırılan `Min-eDTU` veya `vCore`karşılık gelen bellek Içi OLTP depolama miktarına sahip olmasını güvence altına alır.

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>Bellek içi OLTP teknolojileri kullanan veritabanlarının hizmet katmanlarını değiştirme

Veritabanınızı veya örneğinizi İş Açısından Kritik Genel Amaçlı gibi daha yüksek bir katmana (veya standart olarak Premium 'a) yükseltebilirsiniz. Kullanılabilir işlev ve kaynaklar yalnızca artar.

Ancak katmanı daha eski sürüme dönüştürmek veritabanınızı olumsuz etkileyebilir. Veritabanı bellek içi OLTP nesneleri içerdiğinde İş Açısından Kritik ' den Genel Amaçlı (veya Premium ile standart ya da temel) sürümüne düşürülen etki oldukça görünür. Bellek için iyileştirilmiş tablolar, düşürme sonrasında (görünür kalsalar bile) kullanılamaz. Aynı noktalar, elastik bir havuzun fiyatlandırma katmanını azaltdığınızda veya bir veritabanını bellek Içi teknolojilerle bir standart veya temel elastik havuza taşırken geçerlidir.

> [!Important]
> Bellek içi OLTP Genel Amaçlı, standart veya temel katmanda desteklenmez. Bu nedenle, bellek Içi OLTP nesnelerine sahip bir veritabanını standart veya temel katmana taşımak mümkün değildir.

Veritabanını Standard/Basic 'e indirgediğinizde, tüm bellek için iyileştirilmiş tabloları ve tablo türlerini ve yerel koda derlenmiş T-SQL modüllerini kaldırın. 

*İş açısından kritik katmanındaki ölçeklendirme kaynakları*: bellek için iyileştirilmiş tablolardaki veriler, veritabanının veya yönetilen örneğin katmanıyla Ilişkili bellek içi OLTP depolama alanına sığmalıdır ya da elastik havuzda kullanılabilir. Katmanı ölçeklendirmeye veya veritabanını bellek Içi OLTP depolaması yeterli olmayan bir havuza taşımaya çalışırsanız, işlem başarısız olur.

## <a name="in-memory-columnstore"></a>Bellek içi columnstore

Bellek içi columnstore teknolojisi, tablolarda büyük miktarda veriyi depolamanızı ve sorgulamanızı sağlar. Columnstore teknolojisi, sütun tabanlı veri depolama biçimini ve toplu sorgu işlemeyi kullanarak, OLAP iş yüklerindeki geleneksel satır odaklı depolama üzerinden sorgu performansının en fazla 10 kata kadar elde etmelerini sağlar. Ayrıca, sıkıştırılmamış veri boyutu üzerinde veri sıkıştırmasının 10 kata kadar kazanç elde edebilirsiniz.
Verilerinizi düzenlemek için kullanabileceğiniz iki tür columnstore modeli vardır:

- Tablodaki tüm verilerin sütunlu biçimde düzenlendiği **kümelenmiş columnstore** . Bu modelde, tablodaki tüm satırlar, verileri yüksek ölçüde sıkıştıran ve tablo üzerinde hızlı analitik sorgular ve raporlar çalıştırmanızı sağlayan sütunlu biçimde yerleştirilir. Verilerinizin doğasına bağlı olarak, verilerinizin boyutu 10 x-100x olarak azalabilir. Kümelenmiş columnstore modeli, 100 ' den fazla veri kümesi diskte depolanmadan önce sıkıştırıldığından büyük miktarda verinin (toplu yük) hızlı bir şekilde içe alımını da mümkün tutar. Bu model, klasik veri ambarı senaryoları için iyi bir seçenektir. 
- Verilerin geleneksel rowstore tablosunda depolandığı **kümelenmemiş columnstore** ve analitik sorgular için kullanılan columnstore biçiminde bir dizin vardır. Bu model, karma Işlem analitik Işleme (HTAP) sağlar: işlemsel iş yükünde performanslı gerçek zamanlı analizler çalıştırma özelliği. OLTP sorguları, küçük bir satır kümesine erişmek için iyileştirilmiş rowstore tablosunda yürütülür, ancak OLAP sorguları, taramalar ve analizler için daha iyi seçim yapan columnstore dizininde yürütülür. Azure SQL veritabanı sorgu iyileştiricisi, sorguya göre rowstore veya columnstore biçimini dinamik olarak seçer. Kümelenmemiş columnstore dizinleri, özgün veri kümesi hiçbir değişiklik yapılmadan orijinal rowstore tablosunda tutulduğundan verilerin boyutunu azaltmayın. Ancak, ek columnstore dizininin boyutu, denk B-ağacı dizininden daha küçük bir büyüklük sırasıyla olmalıdır.

> [!Note]
> Bellek içi columnstore teknolojisi yalnızca bellekte işlenmek üzere gereken verileri tutar, ancak belleğe sığmayan veriler disk üzerinde depolanır. Bu nedenle, bellek içi columnstore yapılarında veri miktarı kullanılabilir bellek miktarını aşabilir. 

Teknoloji hakkında ayrıntılı video:

- [Columnstore dizini: Ignite 2016 'den bellek Içi analiz videoları](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Columnstore dizinleri için veri boyutu ve depolama

Columnstore dizinlerinin belleğe sığması gerekmez. Bu nedenle, Dizin boyutunun tek ucu, [DTU tabanlı satın alma modeli](sql-database-service-tiers-dtu.md) ve [sanal çekirdek tabanlı satın alma modeli](sql-database-service-tiers-vcore.md) makalelerinde belgelenen en büyük genel veritabanı boyutudur.

Kümelenmiş columnstore dizinleri kullandığınızda, temel tablo depolaması için sütunlu sıkıştırma kullanılır. Bu sıkıştırma, Kullanıcı verilerinizin depolama ayak izini önemli ölçüde azaltabilir, yani veritabanına daha fazla veri uydurabilecek anlamına gelir. Ve sıkıştırma, [sütunlu arşiv sıkıştırması](https://msdn.microsoft.com/library/cc280449.aspx#using-columnstore-and-columnstore-archive-compression)ile daha da artırılabilir. Elde ettiğiniz sıkıştırma miktarı verilerin doğasına bağlıdır, ancak sıkıştırma işlemi 10 kez daha yaygın değildir.

Örneğin, en fazla 1 terabaytlık (TB) boyutunda bir veritabanınız varsa ve columnstore dizinlerini kullanarak sıkıştırmaya 10 kez ulaşdıysanız, veritabanında toplam 10 TB Kullanıcı verisi kullanabilirsiniz.

Kümelenmemiş columnstore dizinleri kullandığınızda, temel tablo hala geleneksel rowstore biçiminde depolanır. Bu nedenle, depolama tasarrufu, kümelenmiş columnstore dizinleri ile büyük değildir. Ancak, bir dizi geleneksel kümelenmemiş dizini tek bir columnstore diziniyle değiştiriyorsanız, tablo için depolama ayak izine ilişkin genel tasarrufları görmeye devam edebilirsiniz.

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>Columnstore dizinleri içeren veritabanlarının hizmet katmanlarını değiştirme

Hedef katmanınız S3 altındaysa, *tek veritabanını temel veya Standart sürüme* düşürme olanağı mümkün olmayabilir. Columnstore dizinleri yalnızca İş Açısından Kritik/Premium fiyatlandırma katmanında ve temel katmanda değil, S3 ve yukarıdaki Standart katmanda desteklenir. Veritabanınızı desteklenmeyen bir katmana veya düzeye indirgemeniz durumunda, columnstore dizininiz kullanılamaz hale gelir. Sistem columnstore dizininizi korur, ancak dizinden hiç yararlanmayın. Daha sonra desteklenen bir katmana veya düzeye yeniden yükseltirseniz, columnstore dizininiz yeniden yararlanılabilir olmaya hemen başlamaya hazırız.

**Kümelenmiş** bir columnstore dizininiz varsa, tüm tablo düşürme sonrasında kullanılamaz hale gelir. Bu nedenle, veritabanınızı desteklenmeyen bir katmana veya düzeye indirgeyebilmeniz için tüm *kümelenmiş* columnstore dizinlerini düşürmenizi öneririz.

> [!Note]
> Yönetilen örnek, tüm katmanlarda ColumnStore dizinlerini destekler.

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>Sonraki adımlar

- [Hızlı Başlangıç 1: daha hızlı T-SQL performansı için bellek Içi OLTP teknolojileri](https://msdn.microsoft.com/library/mt694156.aspx)
- [Mevcut bir Azure SQL uygulamasında bellek Içi OLTP kullanın](sql-database-in-memory-oltp-migration.md)
- Bellek içi OLTP için [bellek ıçı OLTP depolama alanını izleme](sql-database-in-memory-oltp-monitoring.md)
- [Azure SQL veritabanı 'nda bellek Içi özellikleri deneyin](sql-database-in-memory-sample.md)

## <a name="additional-resources"></a>Ek kaynaklar

### <a name="deeper-information"></a>Daha derin bilgi

- [SQL veritabanı 'nda bellek Içi OLTP ile DTU 'yu %70 oranında düşürürken, çekirdeğin anahtar veritabanı iş yükünü nasıl çift katına kullandığını öğrenin](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [Azure SQL veritabanı blog gönderisine bellek içi OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [Bellek Içi OLTP hakkında bilgi edinin](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore dizinleri hakkında bilgi edinin](https://msdn.microsoft.com/library/gg492088.aspx)
- [Gerçek zamanlı operasyonel çözümlemeler hakkında bilgi edinin](https://msdn.microsoft.com/library/dn817827.aspx)
- Bkz. [ortak Iş yükü desenleri ve geçiş hususları](https://msdn.microsoft.com/library/dn673538.aspx) (bellek içi OLTP yaygın olarak önemli performans kazancı sağlayan iş yükü düzenlerini açıklar)

### <a name="application-design"></a>Uygulama tasarımı

- [Bellek içi OLTP (bellek Içi Iyileştirme)](https://msdn.microsoft.com/library/dn133186.aspx)
- [Mevcut bir Azure SQL uygulamasında bellek Içi OLTP kullanın](sql-database-in-memory-oltp-migration.md)

### <a name="tools"></a>Araçlar

- [Azure Portal](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [SQL Server Veri Araçları (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
