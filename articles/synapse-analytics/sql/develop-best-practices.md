---
title: SYNAPSE SQL için geliştirme en iyi yöntemleri
description: SYNAPSE SQL için geliştirme yaparken bilmeniz gereken öneriler ve en iyi uygulamalar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 479b20bcb0803d5483d139939da627d53ceccb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667619"
---
# <a name="development-best-practices-for-synapse-sql"></a>SYNAPSE SQL için geliştirme en iyi yöntemleri

Bu makalede, veri ambarı Çözümünüzü geliştirirken rehberlik ve en iyi uygulamalar açıklanmaktadır. 

## <a name="dedicated-sql-pool-development-best-practices"></a>Adanmış SQL havuzu geliştirme en iyi uygulamaları

### <a name="reduce-cost-with-pause-and-scale"></a>Duraklatma ve ölçeklendirme ile maliyetleri azaltın

Duraklatma ve ölçeklendirme aracılığıyla maliyetleri azaltma hakkında daha fazla bilgi için bkz. [İşlem yönetme](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>İstatistiklerin bakımını yapın

İstatistiklerinizi günlük olarak veya her bir yük sonra güncelleştirdiğinizden emin olun.  İstatistiklerin sıfırdan oluşturulması veya mevcut olanların güncelleştirilmesi konusunda karar vermek için performans ve maliyet açısından değerlendirme yapmanız gerekir. Tüm istatistiklerinizi korumak için çok uzun sürüyor olduğunu fark ederseniz, hangi sütunların istatistikte olduğunu veya sık güncelleştirilmesi gereken sütunları daha seçmeli olarak seçebilirsiniz.  

Örneğin, yeni değerlerin her gün eklenebileceği Tarih sütunlarını güncelleştirmek isteyebilirsiniz. 

> [!NOTE]
> Birleşimlerde yer alan sütunlar, WHERE yan tümcesinde kullanılan sütunlar ve GROUP BY içinde bulunan sütunlar için istatistik bulundurarak en avantaja sahip olursunuz.

Ayrıca bkz. [tablo Istatistiklerini yönetme](develop-tables-statistics.md), [ISTATISTIK oluşturma](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true), [istatistikleri güncelleştirme](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Büyük tabloları karma olarak dağıtın

Tablolar varsayılan olarak Hepsini Bir Kez Deneme yöntemiyle dağıtılmıştır. Bu özellik, kullanıcıların tablolarının nasıl dağıtılacağına karar vermek zorunda kalmadan tablo oluşturmaya başlamasını kolaylaştırır.  Hepsini bir kez deneme tablosu, bazı iş yükleri için yeterince sürebilir. Ancak çoğu durumda, bir dağıtım sütununun seçilmesi çok daha iyi bir işlem yapar.  

Sütuna göre dağıtılmış bir tablonun Hepsini Bir Kez Deneme tablosundan daha iyi performans sunacağı bir örnek, iki büyük bilgi tablosunun birleştirilmesidir.  

Örneğin, order_id tarafından dağıtılan bir Siparişler tablonuz ve order_id tarafından dağıtılan bir işlem tablosu varsa, siparişler tablonuzu order_id işlem tablonuza eklediğinizde, bu sorgu doğrudan bir sorgu haline gelir. 

Bu, veri taşıma işlemlerini ortadan kaldırdığımız anlamına gelir.  Adım sayısı ne kadar az olursa sorgu o kadar hızlı işlenir.  Taşınan veri miktarı azaldıkça sorgunun hızı artar.

> [!TIP]
> Dağıtılmış bir tablo yüklenirken, yükleme işleminin yavaşlamaması için gelen verilerinizin dağıtım anahtarına göre sıralanmamış olduğundan emin olun.  

Dağıtım sütununun nasıl seçileceği hakkında daha fazla bilgi için aşağıdaki bağlantılara bakın ve CREATE TABLES deyiminiz WıTH yan tümcesinde dağıtılmış bir tablo tanımlayabilirsiniz.

Ayrıca bkz. [tabloya genel bakış](develop-tables-overview.md), [tablo dağıtımı](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [tablo dağıtımı](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)ve [Create Table](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)seçme.

### <a name="do-not-over-partition"></a>Aşırı bölümleme yapmayın
Verilerin bölümlenmesi, bölüm değiştirme veya taramayı en iyi duruma getirme ile, bölüm ile verileri korumak için etkili olsa da, çok fazla bölüm olması sorgularınızı yavaşlatabilir.  Genellikle SQL Server en iyi şekilde çalışan yüksek düzeyde parçalı bölümleme stratejisi, adanmış SQL havuzunda iyi çalışmayabilir.  

> [!NOTE]
> Genellikle SQL Server en iyi şekilde çalışan yüksek düzeyde parçalı bölümleme stratejisi, adanmış SQL havuzunda iyi çalışmayabilir.  

Bölüm sayısının çok fazla olması, her bir bölümdeki satır sayısının 1 milyondan az olması halinde kümelenmiş columnstore dizinlerinin verimini de düşürebilir. Adanmış SQL havuzu verilerinizi sizin için 60 veritabanlarına göre bölümlendirir. 

Bu nedenle, 100 bölümlü bir tablo oluşturursanız sonuç 6000 bölüm olacaktır.  Her iş yükü farklı olduğundan, en iyi yöntem deneme yanılma ile iş yükünüze en uygun bölümleme şeklini belirlemektir.  

Göz önünde bulundurulması gereken bir seçenek, SQL Server ' de sizin için çalışmış olabilecek değerden daha düşük bir ayrıntı düzeyi kullanmaktır.  Örneğin, günlük bölümler yerine haftalık veya aylık bölümler kullanın.

Ayrıca bkz. [tablo bölümleme](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>İşlem boyutları en aza indirin

Bir işlemde çalışan INSERT, UPDATE ve DELETE deyimleri başarısız olduğunda gerçekleştirilen adımların geri alınması gerekir.  Uzun sürecek bir geri alma işlemi olasılığını en aza indirmek için işlem boyutlarını mümkün oldukça küçültün.  Bunu yapmak için INSERT, UPDATE ve DELETE deyimlerini parçalara ayırabilirsiniz.  

Örneğin, 1 saat beklemeniz beklenen bir EKLEMEMENIZ varsa, EKLEMEYI dört parçaya bölebilir ve bu sayede her bir çalıştırmayı 15 dakika kadar kısaltabiliriz.

> [!TIP]
> Geri alma riskini azaltmak için CTAS, TRUNCATE, DROP TABLE veya INSERT gibi özel En Az Günlüğe Kaydetme durumlarında boş tablo kullanın.  

Geri alma işlemlerini ortadan kaldırmanın başka bir yöntemi de veri yönetimi için bölüm değiştirme gibi Yalnızca Meta Veri işlemlerini kullanmaktır.  

Örneğin, bir tablodaki order_date değerleri Ekim 2001 içinde olan tüm satırları silmek amacıyla bir DELETE deyimi çalıştırmak yerine verilerinizi aylık bölümlere ayırıp bölümü başka bir tablonun boş bölümüyle değiştirebilirsiniz (ALTER TABLE örneklerini inceleyin).  

Bölümlenmemiş tablolar için, SILME kullanmak yerine bir tabloda tutmak istediğiniz verileri yazmak üzere bir CTAS kullanmayı düşünün.  Bir CTAS aynı süre alırsa, en az işlem günlüğüne sahip olduğu için çalıştırılması çok daha güvenli bir işlemdir ve gerekirse hızlı bir şekilde iptal edilebilir.

Ayrıca bkz. [Işlemleri anlama](develop-transactions.md), [işlemleri iyileştirme](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [tablo bölümleme](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [truncate table](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [alter table](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)ve [Create Table as Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Mümkün olan en küçük sütun boyutunu kullanın

DDL 'nizi tanımlarken, verilerinizi destekleyecek en küçük veri türü kullanılarak sorgu performansı iyileştirecektir. Bu eylem, özellikle CHAR ve VARCHAR sütunları için önemlidir.  

Bir sütundaki en uzun değer 25 karakterse, sütununuzu VARCHAR(25) olarak tanımlayın.  Tüm karakter sütunları için varsayılan uzunluk değeri olarak yüksek bir değer kullanmaktan kaçının.  Ayrıca, NVARCHAR kullanmak yerine, sütunları VARCHAR olarak tanımlayın.

Ayrıca bkz. [tabloya genel bakış](develop-tables-overview.md), [tablo veri türleri](develop-tables-data-types.md)ve [Create Table](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Kümelenmiş columnstore tablolarını iyileştirin

Kümelenmiş columnstore dizinleri, verilerinizi adanmış SQL havuzunda depolayabilmeniz için en etkili yöntemlerle biridir.  Varsayılan olarak, adanmış SQL havuzundaki tablolar kümelenmiş ColumnStore olarak oluşturulur.  

Columnstore tablolarında yapılan sorgularda en iyi performansı elde etmek için segment kalitesinin yüksek olması önemlidir.  Satırlar columnstore tablolarına bellek baskısı altında yazıldığında, segment kalitesi düşebilir.  

Segment kalitesi, sıkıştırılmış Satır Grubu içindeki satır sayısıyla ölçülebilir.  Kümelenmiş columnstore tabloları için segment kalitesini algılamaya ve geliştirmeye yönelik adım adım yönergeler için, [zayıf columnstore dizin kalitesi](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) ve [tablo dizinleri](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) konusundaki nedenleri inceleyin.  

Yüksek kaliteli columnstore kesimleri önemli olduğundan, verileri yüklemek için orta veya büyük kaynak sınıfında bulunan Kullanıcı kimliklerini kullanmak iyi bir fikirdir. Daha düşük [veri ambarı birimlerinin](resource-consumption-models.md) kullanılması, yükleme kullanıcıya daha büyük bir kaynak sınıfı atamak istediğiniz anlamına gelir.

Columnstore tabloları genellikle tablo başına 1.000.000 ' den fazla satır olana kadar sıkıştırılmış bir columnstore segmentine veri gönderemeyeceği ve ayrılmış her SQL havuzu tablosu 60 tablo olarak bölümlendiğinden, tablo 60.000.000 ' den fazla satır içermiyorsa, columnstore tabloları bir sorgu avantajına sahip olmaz.  

> [!TIP]
> 60.000.000 ' den az satır içeren tablolar için bir columnstore dizinine sahip olmak en iyi çözüm olmayabilir.  

Ayrıca, verilerinizi bölümleyip, her bölümün bir kümelenmiş columnstore dizininden faydalanabilir 1.000.000 satıra sahip olması gerektiğini düşünmek isteyeceksiniz.  Bir tabloda 100 bölüm varsa, kümelenmiş bir sütun deposundan faydalanmak için en az 6.000.000.000 satıra sahip olması gerekir (60 dağıtımları *100 bölümleri* 1.000.000 satırları).  

Tablonuzun 6.000.000.000 satırı yoksa, bölüm sayısını azaltın veya bunun yerine bir yığın tablosu kullanmayı deneyin.  Ayrıca, bir columnstore tablosu yerine ikincil dizinlerle bir yığın tablosu kullanılarak daha iyi bir performans kazanılabilir.

Columnstore tablosunda çalıştırılan sorgular yalnızca ihtiyacınız olan sütunları seçmeniz halinde daha hızlı olacaktır.  

Ayrıca bkz. [tablo dizinleri](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [columnstore dizinleri Kılavuzu](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true), [columnstore dizinlerini yeniden oluşturma](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="serverless-sql-pool-development-best-practices"></a>Sunucusuz SQL havuzu geliştirme en iyi uygulamaları

### <a name="general-considerations"></a>Dikkat edilmesi gereken temel noktalar

Sunucusuz SQL havuzu, Azure depolama hesaplarınızdaki dosyaları sorgulamanızı sağlar. Yerel depolama veya alma özelliklerine sahip değildir, yani sorgunun hedeflediği tüm dosyalar sunucusuz SQL havuzunda harici olur. Bu nedenle, depolama alanından dosya okuma ile ilgili her şey sorgu performansının üzerinde bir etkiye sahip olabilir.

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Azure depolama hesabı ve sunucusuz SQL havuzunu birlikte bulundurma

Gecikme süresini en aza indirmek için, Azure depolama hesabınızı ve sunucusuz SQL havuzu uç noktanızı birlikte bulundurma. Çalışma alanı oluşturma sırasında sağlanan depolama hesapları ve uç noktaları aynı bölgede bulunur.

En iyi performans için, sunucusuz SQL havuzu ile diğer depolama hesaplarına eriştiğinizde aynı bölgede olduklarından emin olun. Aksi takdirde, verilerin uzak bölgeden uç noktanın bölgesine ağ aktarımı için gecikme süresi artacaktır.

### <a name="azure-storage-throttling"></a>Azure depolama alanı azaltma

Birden çok uygulama ve hizmet, depolama hesabınıza erişebilir. Uygulamalar, hizmetler ve sunucusuz SQL havuzu iş yükü tarafından oluşturulan Birleşik ıOPS veya üretilen iş, depolama hesabının sınırlarını aşarsa, depolama alanı azaltma oluşur. Depolama alanı azaltma gerçekleştiğinde, sorgu performansı üzerinde önemli ölçüde olumsuz bir etkiye sahip olur.

Daraltma algılandıktan sonra sunucusuz SQL havuzu bu senaryonun yerleşik olarak işlenmesini içerir. Sunucusuz SQL havuzu, azaltma çözümlenene kadar daha yavaş bir hızda depolama istekleri yapar. 

Ancak, en iyi sorgu yürütmesi için, sorgu yürütme sırasında diğer iş yükleriyle depolama hesabını vurgulamanızı öneririz.

### <a name="prepare-files-for-querying"></a>Dosyaları sorgulamaya hazırlama

Mümkünse, daha iyi performans için dosyaları hazırlayacaksınız:

- CSV 'yi Parquet – Parquet, sütunlu biçim olarak dönüştürür. Sıkıştırılmış olduğundan, aynı verilere sahip CSV dosyalarından daha küçük dosya boyutlarına sahiptir ve sunucusuz SQL havuzunun onu okumak için daha az zaman ve depolama istekleri olması gerekir.
- Bir sorgu tek bir büyük dosyayı hedefliyorsa, onu birden çok daha küçük dosyaya bölebilmeniz yararlı olacaktır.
- CSV dosyanızın boyutunu 10 GB 'tan daha düşük tutmaya çalışın.
- Tek bir OPENROWSET yolu veya dış tablo konumu için eşit boyutlu dosyalar olması tercih edilir.
- Bölümleri farklı klasörlere veya dosya adlarına depolayarak verilerinizi bölümleyin; [belirli bölümleri hedeflemek için dosya adı ve FilePath işlevlerini kullanın](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Belirli bölümleri hedeflemek için fileinfo ve filepath işlevlerini kullanın

Veriler genellikle bölümler halinde düzenlenir. Belirli klasörleri ve dosyaları sorgulamak için sunucusuz SQL havuzu söyleyebilirsiniz. Bunun yapılması sorgunun okuması ve işlemesi gereken dosya sayısını ve veri miktarını azaltır. 

Sonuç olarak, daha iyi performans elde edersiniz. Daha fazla bilgi için [dosya adı](query-data-storage.md#filename-function) ve [FilePath](query-data-storage.md#filepath-function) işlevlerini ve [belirli dosyaları sorgulama](query-specific-files.md)ile ilgili örnekleri denetleyin.

Depolamadaki verileriniz bölümlendirilmemişse, bu dosyaları hedefleyen sorguları iyileştirmek için bu işlevleri kullanabilmeniz için Bölümlendirmeyi düşünün.

Sunucusuz SQL havuzundan [Azure SYNAPSE dış tabloları için bölümlenmiş Apache Spark sorgulanırken](develop-storage-files-spark-tables.md) , sorgu yalnızca gerekli dosyaları hedefleyecek.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Sorgu performansını ve birleştirmeleri geliştirmek için CETAS kullanın

[Cetas](develop-tables-cetas.md) , SUNUCUSUZ SQL havuzunda bulunan en önemli özelliklerden biridir. CETAS, dış tablo meta verileri oluşturan ve SELECT sorgusunun sonucunu Depolama hesabınızdaki bir dosya kümesine dışarı aktaran paralel bir işlemdir.

Birleştirilmiş başvuru tabloları gibi sorguların sık kullanılan parçalarını yeni bir dosya kümesine depolamak için CETAS kullanabilirsiniz. Daha sonra, birden çok sorgu içinde ortak birleştirmeleri yinelemek yerine bu tek dış tabloya katabilirsiniz. 

CETAS, Parquet dosyalarını oluşturduğunda, ilk sorgu bu dış tabloyu hedeflediğinde istatistikler otomatik olarak oluşturulur ve daha iyi performans elde edersiniz.

### <a name="next-steps"></a>Sonraki adımlar

Bu makalede sağlanmayan bilgilere ihtiyacınız varsa, tüm SQL havuzu belgelerini aramak için bu sayfanın sol tarafındaki **Belge ara** işlevini kullanın.  [Azure SYNAPSE Analytics Için Microsoft Q&soru sayfası](/answers/topics/azure-synapse-analytics.html) , diğer kullanıcılara ve Azure SYNAPSE Analytics ürün grubuna yönelik sorular oluşturabilmeniz için bir yerdir. Sorularınızın diğer kullanıcılar veya ekibimiz tarafından yanıtlandığından emin olmak için bu forumu sürekli takip ediyoruz.  

Stack Overflow sorularınızı sormak isterseniz, [Azure SYNAPSE Analytics Stack Overflow Forumumuzu](https://stackoverflow.com/questions/tagged/azure-sqldw)da sunuyoruz.
