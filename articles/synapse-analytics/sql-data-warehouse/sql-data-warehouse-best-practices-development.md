---
title: Geliştirme için en iyi yöntemler
description: SYNAPSE SQL havuzu için çözümler geliştirdikçe bilmeniz gereken öneriler ve en iyi uygulamalar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bc101e6bb743b9290593937a4d1bac656430b438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685319"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>SYNAPSE SQL havuzu için geliştirme en iyi yöntemleri

Bu makalede, SQL havuzu Çözümünüzü geliştirirken rehberlik ve en iyi uygulamalar açıklanmaktadır.

## <a name="tune-query-performance-with-new-product-enhancements"></a>Yeni ürün geliştirmeleriyle sorgu performansını ayarlama

- [Gerçekleştirilmiş görünümler ile performans ayarlama](performance-tuning-materialized-views.md)
- [Sıralı kümelenmiş columnstore dizini ile performans ayarlama](performance-tuning-ordered-cci.md)
- [Sonuç kümesini önbelleğe ile performans ayarlama](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Duraklatma ve ölçeklendirme ile maliyetleri azaltın

Duraklatma ve ölçeklendirme ile maliyetleri azaltma hakkında daha fazla bilgi için, [Işlem yönetme](sql-data-warehouse-manage-compute-overview.md) makalesine bakın.

## <a name="maintain-statistics"></a>İstatistiklerin bakımını yapın

SQL havuzu, sütunlarda istatistikleri otomatik olarak algılayacak ve oluşturulacak şekilde yapılandırılabilir.  İyileştirici tarafından oluşturulan sorgu planları yalnızca kullanılabilir istatistiklerle iyidir.  

Sorgularınızda kullanılan sütunlardaki istatistiklerin her zaman güncel olduğundan emin olmak için, veritabanlarınızda AUTO_CREATE_STATISTICS etkinleştirmenizi ve istatistiklerin her bir yükün her gün veya sonrasında güncelleştirilmesini öneririz.

Tüm istatistiklerinizin güncelleştirilmesi çok uzun sürüyor olduğunu fark ederseniz, hangi sütunların sık kullanılan istatistik güncelleştirmelerine ihtiyacı olduğunu daha seçmeli bir şekilde denemek isteyebilirsiniz. Örneğin, yeni değer eklenme ihtimali olan tarih sütunlarını her gün güncelleştirmeyi tercih edebilirsiniz.

> [!TIP]
> Birleşimler, WHERE yan tümcesinde kullanılan sütunlar ve GROUP BY içinde bulunan sütunlar üzerinde bulunan sütunlarda güncel istatistikler bulundurarak en avantaja sahip olursunuz.

Ayrıca bkz. [tablo Istatistiklerini yönetme](sql-data-warehouse-tables-statistics.md), [Istatistik oluşturma](sql-data-warehouse-tables-statistics.md)ve [güncelleştirme istatistikleri](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Büyük tabloları karma olarak dağıtın

Tablolar varsayılan olarak Hepsini Bir Kez Deneme yöntemiyle dağıtılmıştır.  Bu tasarım, kullanıcıların tablolarının nasıl dağıtılması gerektiğini belirlemek zorunda kalmadan tabloları oluşturmaya başlamanızı kolaylaştırır.  

Hepsini Bir Kez Deneme tabloları, belirli iş yükleri için yeterli performans sunabilir ancak birçok durumda dağıtım sütunu seçilmesi daha iyi sonuç verecektir.  Sütuna göre dağıtılmış bir tablonun Hepsini Bir Kez Deneme tablosundan daha iyi performans sunacağı bir örnek, iki büyük bilgi tablosunun birleştirilmesidir.  

Örneğin, order_id ile dağıtılmış bir sipariş tablonuz ve yine order_id ile dağıtılmış bir işlem tablonuz varsa, sipariş tablonuzla işlem tablonuzu order_id üzerinden birleştirdiğinizde, ilgili sorgu bir geçiş sorgusu olur ve veri taşıma işlemleri atlanır.  Adım sayısı ne kadar az olursa sorgu o kadar hızlı işlenir.  Taşınan veri miktarı azaldıkça sorgunun hızı artar.  

Dağıtılmış bir tablo yüklenirken, yükleme işleminin yavaşlamaması için gelen verilerinizin dağıtım anahtarına göre sıralanmamış olduğundan emin olun.  Aşağıdaki makaleler, bir dağıtım sütunu seçerek ve CREATE TABLES deyiminiz WıTH yan tümcesinde dağıtılmış bir tablo tanımlama hakkında daha fazla ayrıntı sağlar.

Ayrıca bkz. [tabloya genel bakış](sql-data-warehouse-tables-overview.md), [tablo dağıtımı](sql-data-warehouse-tables-distribute.md), [tablo dağıtımı](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [Create Table](sql-data-warehouse-tables-overview.md)ve [Create Table](sql-data-warehouse-develop-ctas.md) seçme

## <a name="do-not-over-partition"></a>Aşırı bölümleme yapmayın

Verilerin bölümlenmesi, bölüm değiştirme veya taramayı en iyi duruma getirme ile, bölüm ile verileri korumak için etkili olsa da, çok fazla bölüm olması sorgularınızı yavaşlatabilir.  

Genellikle, SQL Server iyi şekilde çalışan yüksek düzeyde ayrıntı veren bir bölümlendirme stratejisi SQL havuzunda iyi çalışmayabilir.  Bölüm sayısının çok fazla olması, her bir bölümdeki satır sayısının 1 milyondan az olması halinde kümelenmiş columnstore dizinlerinin verimini de düşürebilir.  

Arka planda SQL havuzunun verilerinizi 60 veritabanlarına bölümleyip, 100 bölümden oluşan bir tablo oluşturursanız bu durum, 6000 bölümlerinde sonuçlanır.  Her iş yükü farklı olduğundan, en iyi yöntem deneme yanılma ile iş yükünüze en uygun bölümleme şeklini belirlemektir.  

> [!TIP]
> SQL Server ' de, daha düşük bir ayrıntı düzeyi kullanmayı tercih edebilirsiniz.  Örneğin, günlük bölümler yerine haftalık veya aylık bölümler kullanın.

Ayrıca bkz. [tablo bölümleme](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>İşlem boyutları en aza indirin

Bir işlemde çalışan INSERT, UPDATE ve DELETE deyimleri başarısız olduğunda gerçekleştirilen adımların geri alınması gerekir.  Uzun sürecek bir geri alma işlemi olasılığını en aza indirmek için işlem boyutlarını mümkün oldukça küçültün.  Bunu yapmak için INSERT, UPDATE ve DELETE deyimlerini parçalara ayırabilirsiniz.  

Örneğin, 1 saat beklemeniz beklenen bir INSERT uygulamanız varsa, her biri 15 dakika içinde çalışacak şekilde, EKLEMEYI dört parçaya bölün.  Geri alma riskini azaltmak için CTAS, TRUNCATE, DROP TABLE veya boş tablolara ekleme gibi çok az sayıda günlüğe kaydetme durumu özelliğinden yararlanın.  

Geri alma işlemlerini ortadan kaldırmanın başka bir yöntemi de veri yönetimi için bölüm değiştirme gibi Yalnızca Meta Veri işlemlerini kullanmaktır.  Örneğin, bir tablodaki order_date değerleri Ekim 2001 içinde olan tüm satırları silmek amacıyla bir DELETE deyimi çalıştırmak yerine verilerinizi aylık bölümlere ayırıp bölümü başka bir tablonun boş bölümüyle değiştirebilirsiniz (ALTER TABLE örneklerini inceleyin).  

Bölümlenmemiş tablolar için, SILME kullanmak yerine bir tabloda tutmak istediğiniz verileri yazmak üzere bir CTAS kullanmayı düşünün.  Bir CTAS aynı süre alırsa, en az işlem günlüğüne sahip olduğu için çalıştırılması çok daha güvenli bir işlemdir ve gerekirse hızlı bir şekilde iptal edilebilir.

Ayrıca bkz. [Işlemleri anlama](sql-data-warehouse-develop-transactions.md), [işlemleri iyileştirme](sql-data-warehouse-develop-best-practices-transactions.md), [tablo bölümleme](sql-data-warehouse-tables-partition.md), [truncate table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [alter table](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)ve [Create Table as Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>Mümkün olan en küçük sütun boyutunu kullanın

DDL 'nizi tanımlarken, verilerinizi destekleyecek en küçük veri türü kullanılarak sorgu performansı iyileştirecektir.  Bu yaklaşım özellikle CHAR ve VARCHAR sütunları için önemlidir.  

Bir sütundaki en uzun değer 25 karakterse, sütununuzu VARCHAR(25) olarak tanımlayın.  Tüm karakter sütunları için varsayılan uzunluk değeri olarak yüksek bir değer kullanmaktan kaçının.  Ayrıca, NVARCHAR şart değilse sütunları VARCHAR olarak tanımlayın.

Ayrıca bkz. [tabloya genel bakış](sql-data-warehouse-tables-overview.md), [tablo veri türleri](sql-data-warehouse-tables-data-types.md)ve [Create Table](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Kümelenmiş columnstore tablolarını iyileştirin

Kümelenmiş columnstore dizinleri, verilerinizi SQL havuzunda depolayabilmeniz için en etkili yöntemlerle biridir.  Varsayılan olarak, SQL havuzundaki tablolar kümelenmiş ColumnStore olarak oluşturulur.  

> [!NOTE]
> Columnstore tablolarındaki sorgularda en iyi performansı elde etmek için, iyi segment kalitesine sahip olmak önemlidir.  

Satırlar columnstore tablolarına bellek baskısı altında yazıldığında, segment kalitesi düşebilir.  Segment kalitesi, sıkıştırılmış Satır Grubu içindeki satır sayısıyla ölçülebilir.  

Kümelenmiş columnstore tabloları için segment kalitesini algılamaya ve geliştirmeye yönelik adım adım yönergeler için [tablo dizinleri](sql-data-warehouse-tables-index.md) makalesindeki [kötü columnstore dizin kalitesinin nedenleri](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) bölümüne bakın.  

Yüksek kaliteli columnstore kesimleri önemli olduğundan, verileri yüklemek için orta veya büyük kaynak sınıfında bulunan Kullanıcı kimliklerini kullanmak iyi bir fikirdir. Daha düşük [veri ambarı birimlerinin](what-is-a-data-warehouse-unit-dwu-cdwu.md) kullanılması, yükleme kullanıcıya daha büyük bir kaynak sınıfı atamak istediğiniz anlamına gelir.

Columnstore tabloları, tablo başına 1.000.000 ' den fazla satır olmadığı ve her bir SQL havuzu tablosu 60 tablo olarak bölümlenene kadar verileri sıkıştırılmış bir columnstore kesimine getirmediği için, genellikle tablo 60.000.000 ' den fazla satır içermiyorsa, columnstore tabloları bir sorgu avantajına sahip olmaz.  

60.000.000 ' den az satır içeren bir tablo için, bir columnstore dizinine sahip olmak mantıklı bir anlamlı olmayabilir.  Kullanmanın da bir zararı olmayacaktır.  

Ayrıca, verilerinizi bölümlemeniz halinde her bir bölümün kümelenmiş columnstore dizini kullanabilmesi için en az 1 milyon satıra ihtiyaç duyacağını unutmayın.  Bir tabloda 100 bölüm varsa, kümelenmiş bir sütun deposundan faydalanmak için en az 6.000.000.000 satıra sahip olması gerekir (60 dağıtımları *100 bölümleri* 1.000.000 satırları).  

Bu örnekte tablonuzda 6 milyar satır yoksa, bölüm sayısını azaltabilir veya yığın tablo kullanabilirsiniz.  Deneme yaparak columnstore tablosu yerine ikincil dizine sahip yığın tablo ile daha iyi performans elde edip etmeyeceğinizi görebilirsiniz.

> [!TIP]
> Columnstore tablosunda çalıştırılan sorgular yalnızca ihtiyacınız olan sütunları seçmeniz halinde daha hızlı olacaktır.  

Ayrıca bkz. [tablo dizinleri](sql-data-warehouse-tables-index.md), [columnstore dizinleri Kılavuzu](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)ve [columnstore dizinlerini yeniden oluşturma](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede aradığınızı bulamazsanız, tüm Azure SYNAPSE belgelerini aramak için bu sayfanın sol tarafındaki "belgeleri ara" seçeneğini kullanmayı deneyin.  

[Azure SYNAPSE Için Microsoft Q&soru sayfası](/answers/topics/azure-synapse-analytics.html) , diğer kullanıcılara ve Azure SYNAPSE ürün grubuna soru göndermek için kullanabileceğiniz bir yerdir.  Sorularınızın diğer kullanıcılar veya ekibimiz tarafından yanıtlandığından emin olmak için bu forumu sürekli takip ediyoruz.  

Stack Overflow sorularınızı sormak isterseniz, [Azure SYNAPSE Analytics Stack Overflow Forumumuzu](https://stackoverflow.com/questions/tagged/azure-sqldw)da sunuyoruz.

Özellik istekleri yapmak için [Azure SYNAPSE geri bildirim](https://feedback.azure.com/forums/307516-sql-data-warehouse) sayfasını kullanın.  İsteklerinizi eklemeniz veya diğer istekleri oylamanız, özellikleri önceliklendirme konusunda bize yardımcı olmaktadır.