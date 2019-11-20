---
title: Azure SYNAPSE Analytics 'te SQL Analytics için en iyi uygulamalar (eski adıyla SQL DW)
description: Azure SYNAPSE Analytics 'te (eski adıyla SQL DW) SQL Analytics çözümleri geliştirmeye yönelik öneriler ve en iyi uygulamalar.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646167"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Azure SYNAPSE Analytics 'te SQL Analytics için en iyi uygulamalar (eski adıyla SQL DW)

Bu makale, [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) dağıtımından en iyi performansı elde etmenize yardımcı olan en iyi yöntemler koleksiyonudur.  Bu makalenin amacı, size bazı temel rehberlik sunmak ve odağın önemli bölümlerini vurgulayasağlamaktır.  Her bölüm sizi kavram halinde tanıtarak kavramı daha ayrıntılı bir şekilde kapsayan daha ayrıntılı makalelere yönlendirir. Konuların sırası önem sırasına göre belirlenir. 

## <a name="reduce-cost-with-pause-and-scale"></a>Duraklatma ve ölçeklendirme ile maliyetleri azaltın

Duraklatma ve ölçeklendirme aracılığıyla maliyetleri azaltma hakkında daha fazla bilgi için bkz. [İşlem yönetme](sql-data-warehouse-manage-compute-overview.md). 

## <a name="maintain-statistics"></a>İstatistiklerin bakımını yapın
Azure SQL veri ambarı, sütunlarda istatistikleri otomatik olarak algılayacak ve oluşturulacak şekilde yapılandırılabilir.  İyileştirici tarafından oluşturulan sorgu planları yalnızca kullanılabilir istatistiklerle iyidir.  Sorgularınızda kullanılan sütunlardaki istatistiklerin her zaman güncel olduğundan emin olmak için, veritabanlarınız için AUTO_CREATE_STATISTICS etkinleştirmenizi ve istatistiklerin her bir yükün her gün veya sonrasında güncelleştirilmesini öneririz. 

Tüm istatistiklerinizin güncelleştirilmesi çok uzun sürüyor olduğunu fark ederseniz, hangi sütunların sık kullanılan istatistik güncelleştirmelerine ihtiyacı olduğunu daha seçmeli bir şekilde denemek isteyebilirsiniz. Örneğin, yeni değer eklenme ihtimali olan tarih sütunlarını her gün güncelleştirmeyi tercih edebilirsiniz. **Birleşimlerde yer alan sütunlarda, grup tarafından bulunan WHERE yan tümcesinde ve sütunlarda kullanılan sütunlarda güncelleştirilmiş istatistikler bulundurarak en avantaja sahip olursunuz.**

Ayrıca bkz. [Tablo istatistiklerini yönetme][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Sorgularınızı izlemek ve iyileştirmek için DMV’leri kullanın
SQL Analytics 'in, sorgu yürütmeyi izlemek için kullanılabilecek çeşitli DMVs 'ler vardır.  Aşağıdaki izleme makalesinde çalıştırılan bir sorgunun ayrıntılarını incelemeyle ilgili adım adım talimatlar yer almaktadır.  Bu DMV’lerdeki sorguları hızlıca bulmak için sorgularınızla LABEL seçeneğini kullanabilirsiniz.

Ayrıca bkz. [DMV’leri kullanarak iş yükünüzü izleme][Monitor your workload using DMVs], [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="tune-query-performance-with-new-product-enhancements"></a>Yeni ürün geliştirmeleriyle sorgu performansını ayarlama
- [Gerçekleştirilmiş görünümlerle performans ayarı](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Sıralı kümelenmiş columnstore diziniyle performans ayarı](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Sonuç kümesi önbelleğe alma performans ayarı](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>INSERT deyimlerini gruplayın
Küçük bir tabloya bir INSERT deyimiyle tek seferlik yükleme yapmak veya `INSERT INTO MyLookup VALUES (1, 'Type 1')` gibi bir deyimle bir aramanın düzenli aralıklarla yeniden yüklenmesi işinizi fazlasıyla görebilir.  Ancak gün içinde binlerce veya milyonlarca satır yüklemeniz gerekiyorsa, ayrı ayrı INSERT deyimleri gerekli performansı göstermeyebilir.  Bunun yerine işlemlerinizi bir dosyaya yazılacağı ve başka bir işlemin belirli aralıklarla bu dosyayı yükleyeceği şekilde geliştirin.

Ayrıca bkz. [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Verileri hızlıca yüklemek ve dışarı aktarmak için PolyBase kullanın

 SQL Analytics Azure Data Factory, PolyBase ve BCP gibi çeşitli araçlarla veri yüklemeyi ve dışarı aktarmayı destekler.  Performansın yüksek öneme sahip olmadığı küçük miktarlardaki veriler için bu araçlardan herhangi birini kullanabilirsiniz.  Ancak, büyük hacimli verileri yüklerken veya dışarı aktarırken ya da yüksek performansa ihtiyaç duyduğunuzda, PolyBase en iyi çözüm olacaktır.  
 
 PolyBase, MPP (büyük ölçüde paralel Işleme) mimarisinden yararlanmak için tasarlanmıştır ve veri magnitudes diğer araçlardan daha hızlı yüklenip dışarı aktarır.  PolyBase yükleri CTAS veya INSERT INTO ile çalıştırılabilir.  **CTAS, işlem günlüğünü en aza indiren ve verilerinizi en hızlı yükleyen seçenektir.** 
 
  Azure Data Factory, PolyBase yüklerini da destekler ve CTAS olarak benzer performans elde edebilir.  PolyBase, Gzip de dahil olmak üzere birçok dosya biçimi için destek sunar.  **Gzip metin dosyalarını kullanırken üretilen işi en üst düzeye çıkarmak için, yüklerinizin paralelliğini en üst düzeye çıkarmak üzere dosyaları 60 veya daha fazla dosyaya bölün.**  Toplam hızı artırmak için verilerinizi aynı anda yükleyin.

Ayrıca bkz. [veri yükleme][Load data], [PolyBase kullanma kılavuzu][Guide for using PolyBase], [SQL havuzu yükleme desenleri ve stratejileri][Azure SQL Data Warehouse loading patterns and strategies], [Azure Data Factory ile VERI yükleme][Load Data with Azure Data Factory], [Azure Data Factory verileri taşıma][Move data with Azure Data Factory], [dış dosya biçimi oluşturma][CREATE EXTERNAL FILE FORMAT], [oluşturma Select (CTAS) olarak tablo][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Dış tabloları önce yükleyip sonra sorgu çalıştırın
Dış tablolar olarak da bilinen Polybase, veri yüklemenin en hızlı yolu olsa da sorgular için en iyi çözüm değildir. PolyBase tabloları Şu anda yalnızca Azure Blob dosyalarını ve Azure Data Lake depolamayı destekler. Bu dosyaları destekleyen herhangi bir işlem kaynağı yoktur.  

Sonuç olarak, SQL Analytics bu çalışmayı boşaltamaz ve bu nedenle verileri okumak için tempdb 'ye yükleyerek dosyanın tamamını okumalı olmalıdır.  Sonuç olarak bu veriler için birden fazla sorgunuz varsa, verileri bir kez yükleyip sorguların yerel tabloyu kullanmalarını sağlamak daha iyi olacaktır.

Ayrıca bkz. [PolyBase kullanma kılavuzu][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Büyük tabloları karma olarak dağıtın
Tablolar varsayılan olarak Hepsini Bir Kez Deneme yöntemiyle dağıtılmıştır.  Bu durum, kullanıcıların tabloların dağıtma şekli hakkında düşünmelerine gerek kalmadan tablo oluşturmaya başlamalarını sağlar.  Hepsini Bir Kez Deneme tabloları, belirli iş yükleri için yeterli performans sunabilir ancak birçok durumda dağıtım sütunu seçilmesi daha iyi sonuç verecektir.  Sütuna göre dağıtılmış bir tablonun Hepsini Bir Kez Deneme tablosundan daha iyi performans sunacağı bir örnek, iki büyük bilgi tablosunun birleştirilmesidir.  

Örneğin, order_id ile dağıtılmış bir sipariş tablonuz ve yine order_id ile dağıtılmış bir işlem tablonuz varsa, sipariş tablonuzla işlem tablonuzu order_id üzerinden birleştirdiğinizde, ilgili sorgu bir geçiş sorgusu olur ve veri taşıma işlemleri atlanır.  Adım sayısı ne kadar az olursa sorgu o kadar hızlı işlenir.  Taşınan veri miktarı azaldıkça sorgunun hızı artar.  

Dağıtılmış bir tablo yüklenirken, yükleme işleminin yavaşlamaması için gelen verilerinizin dağıtım anahtarına göre sıralanmamış olduğundan emin olun.  Dağıtım sütununun nasıl seçileceği hakkında daha fazla ayrıntı için aşağıdaki bağlantılara bakın ve CREATE TABLE deyimlerinizin WıTH yan tümcesinde dağıtılmış bir tablo tanımlayabilirsiniz.

Ayrıca bkz. [Tabloya genel bakış][Table overview], [Tablo dağıtımı][Table distribution], [Tablo dağıtımına genel bakış][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Aşırı bölümleme yapmayın
Verilerin bölümlenmesi, bölüm değiştirme veya taramayı en iyi duruma getirme ile, bölüm ile verileri korumak için etkili olsa da, çok fazla bölüm olması sorgularınızı yavaşlatabilir.  Genellikle SQL Server en iyi şekilde çalışan yüksek düzeyde ayrıntı veren bir bölümlendirme stratejisi, SQL Analytics 'te iyi çalışmayabilir.  

Bölüm sayısının çok fazla olması, her bir bölümdeki satır sayısının 1 milyondan az olması halinde kümelenmiş columnstore dizinlerinin verimini de düşürebilir.  Arka planda SQL Analytics 'in verilerinizi 60 veritabanı olarak bölümleyip, 100 bölümlü bir tablo oluşturursanız bu durum, 6000 bölümlerine neden olur.  

Her iş yükü farklı olduğundan, en iyi yöntem deneme yanılma ile iş yükünüze en uygun bölümleme şeklini belirlemektir.  SQL Server’da kullandığınızdan daha az parça kullanmayı deneyin.  Örneğin, günlük bölümler yerine haftalık veya aylık bölümler kullanın.

Ayrıca bkz. [Tablo bölümleme][Table partitioning]

## <a name="minimize-transaction-sizes"></a>İşlem boyutları en aza indirin
Bir işlemde çalışan INSERT, UPDATE ve DELETE deyimleri başarısız olduğunda gerçekleştirilen adımların geri alınması gerekir.  Uzun sürecek bir geri alma işlemi olasılığını en aza indirmek için işlem boyutlarını mümkün oldukça küçültün.  Bunu yapmak için INSERT, UPDATE ve DELETE deyimlerini parçalara ayırabilirsiniz.  

Örneğin, 1 saat beklemeniz beklenen bir EKLEMEMENIZ varsa, her biri 15 dakika içinde çalışacak şekilde, EKLEMEYI dört parçaya bölün.  Geri alma riskini azaltmak için CTAS, TRUNCATE, DROP TABLE veya INSERT gibi özel En Az Günlüğe Kaydetme durumlarında boş tablo kullanın.  

Geri alma işlemlerini ortadan kaldırmanın başka bir yöntemi de veri yönetimi için bölüm değiştirme gibi Yalnızca Meta Veri işlemlerini kullanmaktır.  Örneğin, bir tablodaki order_date değerleri Ekim 2001 içinde olan tüm satırları silmek amacıyla bir DELETE deyimi çalıştırmak yerine verilerinizi aylık bölümlere ayırıp bölümü başka bir tablonun boş bölümüyle değiştirebilirsiniz (ALTER TABLE örneklerini inceleyin).  

Bölümlenmemiş tablolar için, SILME kullanmak yerine bir tabloda tutmak istediğiniz verileri yazmak üzere bir CTAS kullanmayı düşünün.  Bir CTAS aynı süre alırsa, en az işlem günlüğüne sahip olduğu için çalıştırılması çok daha güvenli bir işlemdir ve gerekirse hızlı bir şekilde iptal edilebilir.

Ayrıca bkz. [İşlemleri anlama][Understanding transactions], [İşlemleri iyileştirme][Optimizing transactions], [Tablo bölümleme][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="reduce-query-result-sizes"></a>Sorgu sonuç boyutlarını azalt  
Bu adım büyük sorgu sonucundan kaynaklanan istemci tarafı sorunlarından kaçınmanıza yardımcı olur.  Döndürülen satır sayısını azaltmak için sorgunuzu düzenleyebilirsiniz. Bazı sorgu oluşturma araçları, her sorguya "ilk N" söz dizimini eklemenize olanak tanır.  Ayrıca sorgu sonucunu geçici bir tabloya CETAS ve sonra alt düzey işleme için PolyBase dışarı aktarmayı kullanabilirsiniz.

## <a name="use-the-smallest-possible-column-size"></a>Mümkün olan en küçük sütun boyutunu kullanın
DDL 'nizi tanımlarken, verilerinizi destekleyecek en küçük veri türü kullanılarak sorgu performansı iyileştirecektir.  Bu durum özellikle CHAR ve VARCHAR sütunları için önemlidir.  Bir sütundaki en uzun değer 25 karakterse, sütununuzu VARCHAR(25) olarak tanımlayın.  Tüm karakter sütunları için varsayılan uzunluk değeri olarak yüksek bir değer kullanmaktan kaçının.  Ayrıca, NVARCHAR şart değilse sütunları VARCHAR olarak tanımlayın.

Ayrıca bkz. [Tabloya genel bakış][Table overview], [Tablo veri türleri][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Geçiş verileri için geçici yığın tabloları kullanın
Geçici olarak veri giriş yaptığınızda, yığın tablosu kullanmanın genel işlemi daha hızlı hale getirir.  Verileri yalnızca daha fazla dönüştürme gerçekleştirmeden önce hazırlamak için yüklüyorsanız, tabloyu yığın tablosuna yüklemek verileri kümelenmiş bir columnstore tablosuna yüklemekten çok daha hızlı olacaktır.  

Ayrıca verileri geçici bir tabloya yüklemek, tabloyu kalıcı bir depolama alanına yüklemekten çok daha hızlı gerçekleştirilecektir.  Geçici tablolar bir "#" ile başlar ve yalnızca onu oluşturan oturum tarafından erişilebilir, bu nedenle yalnızca sınırlı senaryolarda çalışabilir.   Yığın tabloları, CREATE TABLE deyiminin WITH yan tümcesinde tanımlanır.  Geçici tablo kullanıyorsanız, onun için de istatistik oluşturmayı unutmayın.

Ayrıca bkz. [Geçici tablolar][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Kümelenmiş columnstore tablolarını iyileştirin
Kümelenmiş columnstore dizinleri, verilerinizi SQL Analytics 'te depolayabilmeniz için en verimli yöntemlerle biridir.  Varsayılan olarak, SQL Analytics 'teki tablolar kümelenmiş ColumnStore olarak oluşturulur.  Columnstore tablolarında yapılan sorgularda en iyi performansı elde etmek için segment kalitesinin yüksek olması önemlidir.  

Satırlar columnstore tablolarına bellek baskısı altında yazıldığında, segment kalitesi düşebilir.  Segment kalitesi, sıkıştırılmış Satır Grubu içindeki satır sayısıyla ölçülebilir.  Kümelenmiş columnstore tablolarının segment kalitesini tespit etme ve iyileştirme talimatları için [Tablo dizinleri][Causes of poor columnstore index quality] makalesindeki [Columnstore dizin kalitesinin düşük olmasının nedenleri][Table indexes] bölümüne bakın.  

Yüksek kaliteli columnstore kesimleri önemli olduğundan, verileri yüklemek için orta veya büyük kaynak sınıfında bulunan Kullanıcı kimliklerini kullanmak iyi bir fikirdir. Daha düşük [veri ambarı birimlerinin](what-is-a-data-warehouse-unit-dwu-cdwu.md) kullanılması, yükleme kullanıcıya daha büyük bir kaynak sınıfı atamak istediğiniz anlamına gelir.

Columnstore tabloları genellikle tablo başına 1.000.000 ' den fazla satır oluncaya kadar sıkıştırılmış bir columnstore segmentine veri gönderemeyeceği ve her bir SQL Analytics tablosu, bir Thumb kuralı olarak 60 tablo olarak bölümlendiğinden, columnstore tabloları bir sorgu için, tabloda 60.000.000 'den fazla satır vardır.  60 milyondan az satıra sahip tablolarda columnstore dizini kullanmaya gerek olmayabilir.  Kullanmanın da bir zararı olmayacaktır.  

Ayrıca, verilerinizi bölümlemeniz halinde her bir bölümün kümelenmiş columnstore dizini kullanabilmesi için en az 1 milyon satıra ihtiyaç duyacağını unutmayın.  100 bölüme sahip bir tablonun kümelenmiş columnstore kullanabilmesi için en az 6 milyar satıra sahip olması gerekir (60 dağıtım * 100 bölüm * 1 milyon satır).  

Bu örnekte tablonuzda 6 milyar satır yoksa, bölüm sayısını azaltabilir veya yığın tablo kullanabilirsiniz.  Deneme yaparak columnstore tablosu yerine ikincil dizine sahip yığın tablo ile daha iyi performans elde edip etmeyeceğinizi görebilirsiniz.

Columnstore tablosunda çalıştırılan sorgular yalnızca ihtiyacınız olan sütunları seçmeniz halinde daha hızlı olacaktır.  

Ayrıca bkz. [Tablo dizinleri][Table indexes], [Columnstore dizinleri kılavuzu][Columnstore indexes guide], [Columnstore dizinlerini yeniden oluşturma][Rebuilding columnstore indexes]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Sorgu performansını artırmak için daha büyük kaynak sınıfı kullanın
SQL Analytics, kaynak gruplarını sorgulara bellek ayırmak için bir yol olarak kullanır.  Kullanıma hazır olan tüm kullanıcılar, dağıtım başına 100 MB bellek veren küçük kaynak sınıfına atanır.  Her zaman 60 dağıtım olduğundan ve her dağıtıma en az 100 MB verildiğinden, sistem genelinde ayrılan bellek toplam 6000 MB (yaklaşık 6 GB) olur.  

Büyük birleştirmeler veya kümelenmiş columnstore tablolarına yapılan yüklemeler gibi belirli sorgulara daha fazla bellek atanır.  Yalnızca tarama gibi bazı sorgulara ek atama yapılmaz.  Ters çevir tarafında, daha büyük kaynak sınıflarının kullanılmasıyla eşzamanlılık azalır, bu nedenle tüm kullanıcılarınızı büyük bir kaynak sınıfına taşımadan önce bu etkiyi dikkate almanız gerekir.

Ayrıca bkz. [İş yükü yönetimi için kaynak sınıfları](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Eşzamanlılığı artırmak için daha küçük kaynak sınıfı kullanın
Kullanıcı sorgularının uzun bir gecikme olduğunu düşünüyorsanız, kullanıcılarınızın daha büyük kaynak sınıflarında çalışıyor olması ve diğer sorguların sıraya çıkmasına neden olan çok sayıda eşzamanlılık yuvası kullanıyor olması olabilir.  Kullanıcı sorgularının kuyrukta olup olmadığını görmek için `SELECT * FROM sys.dm_pdw_waits` çalıştırıp dönen satırlara bakın.

Ayrıca bkz. [iş yükü yönetimi Için kaynak sınıfları](resource-classes-for-workload-management.md), [sys. DM _pdw_wait][sys.dm_pdw_waits]

## <a name="other-resources"></a>Diğer kaynaklar
Genel sorunlar ve çözümleri için [Sorun giderme][Troubleshooting] makalemizi de inceleyin.

Bu makalede aradığınızı bulamazsanız, tüm Azure SYNAPSE belgelerini aramak için bu sayfanın sol tarafındaki "belgeleri ara" seçeneğini kullanmayı deneyin.  [Azure SYNAPSE Forumu][Azure SQL Data Warehouse MSDN Forum] , diğer kullanıcılara ve Azure SYNAPSE ürün grubu 'na soru sormanız için bir yerdir. 

Sorularınızın diğer kullanıcılar veya ekibimiz tarafından yanıtlandığından emin olmak için bu forumu sürekli takip ediyoruz.  Stack Overflow sorularınızı sormak isterseniz, [Azure Synapse Stack Overflow Forumumuzu][Azure SQL Data Warehouse Stack Overflow Forum]da sunuyoruz.

Son olarak, özellik isteklerini yapmak için lütfen [Azure SYNAPSE geri bildirim][Azure SQL Data Warehouse Feedback] sayfasını kullanın.  İsteklerinizi eklemeniz veya diğer istekleri oylamanız, özellikleri önceliklendirme konusunda bize yardımcı olmaktadır.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
