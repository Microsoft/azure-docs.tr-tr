---
title: Elastik sorguya genel bakış
description: Elastik sorgu birden çok veritabanına yayılan bir Transact-SQL sorgusu çalıştırmanızı sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 07/01/2019
ms.openlocfilehash: 9566ac7169144d984f9200734c99eb10368b3142
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823753"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL veritabanı elastik sorguya genel bakış (Önizleme)

Elastik sorgu özelliği (önizlemede), Azure SQL veritabanı 'nda birden çok veritabanına yayılan bir Transact-SQL sorgusu çalıştırmanızı sağlar. Bu, uzak tablolara erişmek için veritabanları arası sorgular gerçekleştirmenize ve Microsoft ve üçüncü taraf araçlarına (Excel, Power BI, Tableau vb.) bağlanarak birden çok veritabanı içeren veri katmanlarında sorgulama yapmanıza olanak sağlar. Bu özelliği kullanarak, SQL veritabanında büyük veri katmanlarına yönelik sorguları ölçeklendirebilir ve iş zekası (BI) raporlarında sonuçları görselleştirebilirsiniz.

## <a name="why-use-elastic-queries"></a>Neden elastik sorgular kullanılmalıdır?

### <a name="azure-sql-database"></a>Azure SQL Veritabanı

T-SQL ' i Azure SQL veritabanları genelinde tamamen sorgulayın. Bu, uzak veritabanlarının salt okunurdur sorgulanmasını sağlar ve geçerli şirket içi SQL Server müşterilerin, üç ve dört parçalı ad veya bağlı sunucu ile SQL DB 'ye geçiş yapmasına olanak sağlar.

### <a name="available-on-standard-tier"></a>Standart katmanda kullanılabilir

Elastik sorgu hem standart hem de Premium hizmet katmanlarında desteklenir. Daha düşük hizmet katmanları için performans sınırlamalarındaki önizleme sınırlamalarıyla ilgili bölüme bakın.

### <a name="push-parameters-to-remote-databases"></a>Parametreleri uzak veritabanlarına gönder

Elastik sorgular artık SQL parametrelerini yürütme için uzak veritabanlarına gönderebilir.

### <a name="stored-procedure-execution"></a>Saklı yordam yürütme

Uzaktan saklı yordam çağrılarını veya uzak işlevleri, [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714)kullanarak yürütün.

### <a name="flexibility"></a>Esneklik

Elastik sorguya sahip dış tablolar, farklı bir şema veya tablo adına sahip uzak tablolara başvurabilir.

## <a name="elastic-query-scenarios"></a>Elastik sorgu senaryoları

Amaç, birden çok veritabanının satırları tek bir genel sonuca katkıda bulunan senaryoları sorgulamayı kolaylaştırmaktır. Sorgu, Kullanıcı veya uygulama tarafından doğrudan veya veritabanına bağlı olan araçlarla dolaylı olarak oluşturulabilir. Bu özellikle, ticari bı veya veri tümleştirme araçları veya değiştirilemeyen herhangi bir uygulamayı kullanarak rapor oluştururken kullanışlıdır. Elastik bir sorgu sayesinde Excel, Power BI, Tableau veya Cogno 'Lar gibi araçlarla tanıdık SQL Server bağlantı deneyimini kullanarak çeşitli veritabanları arasında sorgulama yapabilirsiniz.
Elastik bir sorgu, SQL Server Management Studio veya Visual Studio tarafından verilen sorgular aracılığıyla tüm veritabanı koleksiyonuna kolay erişim sağlar ve Entity Framework veya diğer ORM ortamlarından veritabanları arası sorgulama yapmayı kolaylaştırır. Şekil 1 ' de, var olan bir bulut uygulamasının ( [elastik veritabanı istemci kitaplığını](sql-database-elastic-database-client-library.md)kullanan) ölçekli bir veri katmanında derlemeleri ve veritabanları arası raporlama için esnek bir sorgu kullanıldığı bir senaryo gösterilmektedir.

**Şekil 1** Ölçekli veri katmanında kullanılan elastik sorgu

![Ölçekli veri katmanında kullanılan elastik sorgu][1]

Elastik sorgu için müşteri senaryoları aşağıdaki topolojilerle belirlenir:

* **Dikey bölümlendirme-veritabanları arası sorgular** (topoloji 1): veriler bir veri katmanındaki bir dizi veritabanı arasında dikey olarak bölümlenir. Genellikle, farklı tablo kümeleri farklı veritabanlarında bulunur. Diğer bir deyişle, şema farklı veritabanlarında farklı olur. Örneğin, tüm envanter tabloları, tüm muhasebe ile ilgili tablolar ikinci bir veritabanı üzerinde olduğunda tek bir veritabanıdır. Bu topolojideki yaygın kullanım örnekleri, bir veya birden çok veritabanında bulunan tablolarda rapor derlemek için bir tane gerektirir.
* **Yatay bölümlendirme-parçalı** oluşturma (topoloji 2): veriler, ölçeklendirilen bir veri katmanındaki satırları dağıtmak için yatay olarak bölümlenir. Bu yaklaşımda, şema tüm katılan veritabanlarında aynıdır. Bu yaklaşım, "parçalama" olarak da adlandırılır. Parçalama, (1) elastik veritabanı araçları kitaplıkları veya (2) kendi kendine parçalı kullanımı kullanılarak gerçekleştirilebilir ve yönetilebilir. Esnek sorgu, birçok parça genelinde raporları sorgulamak veya derlemek için kullanılır.

> [!NOTE]
> Elastik sorgu, çoğu işlemin (filtreleme, toplama) büyük bir kısmı dış kaynak tarafında gerçekleştirilebileceği raporlama senaryolarında en iyi şekilde kullanılır. Uzak veritabanından büyük miktarda verinin aktarıldığı ETL işlemleri için uygun değildir. Daha karmaşık sorgularla ağır raporlama iş yükleri veya veri ambarı senaryoları için [Azure SQL veri ambarı](https://azure.microsoft.com/services/sql-data-warehouse/)'nı kullanmayı da düşünün.
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Dikey bölümlendirme-veritabanları arası sorgular

Kodlamaya başlamak için bkz. [çapraz veritabanı sorgusuna Başlarken (dikey bölümlendirme)](sql-database-elastic-query-getting-started-vertical.md).

Elastik bir sorgu, SQL veritabanında bulunan verilerin diğer SQL veritabanları tarafından kullanılabilmesini sağlamak için kullanılabilir. Bu, bir veritabanından gelen sorguların diğer herhangi bir uzak SQL veritabanında bulunan tablolara başvurabileceği şekilde izin verir. İlk adım, her uzak veritabanı için bir dış veri kaynağı tanımlamaktır. Dış veri kaynağı, uzak veritabanında bulunan tablolara erişim kazanmak istediğiniz yerel veritabanında tanımlanır. Uzak veritabanında değişiklik yapılması gerekmez. Farklı veritabanlarının farklı şemaları olduğu tipik dikey bölümleme senaryolarında, başvuru verilerine erişim ve veritabanları arası sorgulama gibi yaygın kullanım durumlarını uygulamak için esnek sorgular kullanılabilir.

> [!IMPORTANT]
> Herhangi bir dış VERI kaynağı iznine sahip olmanız gerekir. Bu izin ALTER DATABASE iznine dahildir. Temel alınan veri kaynağına başvurmak için herhangi bir dış VERI kaynağı izinlerini DEĞIŞTIRME gerekir.
>

**Başvuru verileri**: topoloji, başvuru veri yönetimi için kullanılır. Aşağıdaki şekilde, başvuru verileriyle birlikte iki tablo (T1 ve T2) adanmış bir veritabanında tutulur. Esnek sorgu kullanarak artık T1 ve T2 tablolarına, şekilde gösterildiği gibi diğer veritabanlarından uzaktan erişebilirsiniz. Başvuru tablolarının küçük olması veya başvuru tablosuna uzak sorguların seçmeli koşullara sahip olması durumunda topoloji 1 ' i kullanın.

**Şekil 2** Dikey bölümleme-başvuru verilerini sorgulamak için elastik sorgu kullanma

![Dikey bölümleme-başvuru verilerini sorgulamak için elastik sorgu kullanma][3]

**Veritabanları arası sorgulama**: elastik sorgular, birkaç SQL veritabanı genelinde sorgu gerektiren kullanım örneklerini etkinleştirir. Şekil 3 ' te dört farklı veritabanı gösterilmektedir: CRM, envanter, HR ve ürünler. Veritabanlarından birinde gerçekleştirilen sorguların aynı zamanda diğer veritabanlarına bir veya diğer veritabanına erişmesi gerekir. Esnek sorgu kullanarak, dört veritabanının her birinde birkaç basit DDL deyimi çalıştırarak, bu durum için veritabanınızı yapılandırabilirsiniz. Bu tek seferlik yapılandırmadan sonra, uzak bir tabloya erişim, T-SQL sorgularından veya bı araçlarınızdaki yerel bir tabloya başvurmak kadar basittir. Bu yaklaşım, uzak sorgular büyük sonuçlar döndürmezse önerilir.

**Şekil 3** Dikey bölümleme-çeşitli veritabanları genelinde sorgulamak için elastik sorgu kullanma

![Dikey bölümleme-çeşitli veritabanları genelinde sorgulamak için elastik sorgu kullanma][4]

Aşağıdaki adımlar, aynı şemaya sahip uzak SQL veritabanlarında bulunan bir tabloya erişim gerektiren dikey bölümleme senaryolarına yönelik elastik veritabanı sorgularını yapılandırır:

* [Ana anahtar oluştur](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [VERITABANı KAPSAMLı KIMLIK bilgisi oluştur](https://msdn.microsoft.com/library/mt270260.aspx) myCredential
* Yalnızca **RDBMS** türünde [dış veri kaynağı oluştur/bırak](https://msdn.microsoft.com/library/dn935022.aspx) myDataSource
* [Dış tablo MyTable oluştur/bırak](https://msdn.microsoft.com/library/dn935021.aspx)

DDL deyimlerini çalıştırdıktan sonra, "MyTable" uzak tablosuna bir yerel tablo gibi erişebilirsiniz. Azure SQL veritabanı, uzak veritabanına yönelik bir bağlantıyı otomatik olarak açar, isteğinizi uzak veritabanında işler ve sonuçları döndürür.

## <a name="horizontal-partitioning---sharding"></a>Yatay bölümleme-parçalama

Veri katmanı, veri katmanının veritabanlarını temsil etmesi için [esnek bir veritabanı parça eşlemesi](sql-database-elastic-scale-shard-map-management.md) gerektiren bir parçalar üzerinde raporlama görevlerini gerçekleştirmek için elastik sorgu kullanma. Genellikle, bu senaryoda yalnızca tek bir parça eşlemesi kullanılır ve elastik sorgu özelliklerine (baş düğüm) sahip adanmış bir veritabanı, raporlama sorguları için giriş noktası görevi görür. Yalnızca bu adanmış veritabanının parça eşlemesine erişmesi gerekir. Şekil 4 ' te, elastik sorgu veritabanı ve parça haritası ile ilgili bu topoloji ve yapılandırma gösterilmektedir. Veri katmanındaki veritabanları herhangi bir Azure SQL veritabanı sürümü veya sürümü olabilir. Elastik veritabanı istemci kitaplığı ve parça haritaları oluşturma hakkında daha fazla bilgi için bkz. parça [eşleme yönetimi](sql-database-elastic-scale-shard-map-management.md).

**Şekil 4** Yatay bölümleme-parçalı veri katmanları üzerinde raporlama için elastik sorgu kullanma

![Yatay bölümleme-parçalı veri katmanları üzerinde raporlama için elastik sorgu kullanma][5]

> [!NOTE]
> Elastik sorgu veritabanı (baş düğüm) ayrı veritabanı olabilir veya parça haritasını barındıran veritabanı aynı olabilir.
> Seçtiğiniz yapılandırma ne olursa olsun, bu veritabanının hizmet katmanının ve işlem boyutunun beklenen oturum açma/sorgu isteği miktarını işleyecek kadar yüksek olduğundan emin olun.

Aşağıdaki adımlar, (genellikle) çeşitli uzak SQL veritabanlarında bulunan bir dizi tabloya erişim gerektiren yatay bölümleme senaryoları için elastik veritabanı sorguları yapılandırır:

* [Ana anahtar oluştur](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [VERITABANı KAPSAMLı KIMLIK bilgisi oluştur](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) myCredential
* Elastik veritabanı istemci kitaplığını kullanarak veri katmanınızı temsil eden bir parça [Haritası](sql-database-elastic-scale-shard-map-management.md) oluşturun.
* [Dış VERI kaynağı oluştur/bırak](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) **SHARD_MAP_MANAGER** türünde myDataSource
* [Dış tablo MyTable oluştur/bırak](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)

Bu adımları gerçekleştirdikten sonra, "MyTable" yatay bölümlenmiş tablosuna bir yerel tablo gibi erişebilirsiniz. Azure SQL veritabanı, tabloların fiziksel olarak depolandığı uzak veritabanlarına otomatik olarak birden çok paralel bağlantı açar, uzak veritabanlarındaki istekleri işler ve sonuçları döndürür.
Yatay bölümleme senaryosu için gereken adımlar hakkında daha fazla bilgi, [yatay bölümlendirme için elastik sorgu](sql-database-elastic-query-horizontal-partitioning.md)'da bulunabilir.

Kodlamaya başlamak için bkz. [Yatay bölümleme (parçalama) için elastik sorgu ile çalışmaya](sql-database-elastic-query-getting-started.md)başlama.

## <a name="t-sql-querying"></a>T-SQL sorgulama

Dış veri kaynaklarınızı ve dış tablolarınızı tanımladıktan sonra, dış tablolarınızı tanımladığınız veritabanlarına bağlanmak için normal SQL Server bağlantı dizelerini kullanabilirsiniz. Daha sonra, bu bağlantı üzerindeki dış tablolarınız üzerinde T-SQL deyimlerini aşağıda özetlenen sınırlamalara göre çalıştırabilirsiniz. Daha fazla bilgi ve T-SQL sorgularının örneklerini, [yatay bölümlendirme](sql-database-elastic-query-horizontal-partitioning.md) ve [Dikey bölümlendirme](sql-database-elastic-query-vertical-partitioning.md)için belge konularında bulabilirsiniz.

## <a name="connectivity-for-tools"></a>Araçlar için bağlantı

Uygulamalarınızı ve bı veya veri tümleştirme araçlarınızı, dış tabloları olan veritabanlarına bağlamak için normal SQL Server bağlantı dizelerini kullanabilirsiniz. SQL Server, aracınız için bir veri kaynağı olarak desteklendiğinden emin olun. Bağlandıktan sonra, bu veritabanındaki elastik sorgu veritabanına ve dış tablolara, aracsınız ile bağlandığınız diğer SQL Server veritabanları ile yaptığınız gibi bakın.

> [!IMPORTANT]
> Elastik sorgularla Azure Active Directory kullanan kimlik doğrulaması şu anda desteklenmiyor.

## <a name="cost"></a>Maliyet

Esnek sorgu, Azure SQL veritabanı veritabanlarının maliyetine dahildir. Uzak veritabanlarınızın, elastik sorgu uç noktasının desteklenenden farklı bir veri merkezinde olduğu, ancak uzak veritabanlarından gelen veri çıkışları düzenli olarak [Azure ücretleri](https://azure.microsoft.com/pricing/details/data-transfers/)üzerinden ücretlendirildiğini unutmayın.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

* İlk elastik sorgunuzu çalıştırmak standart hizmet katmanında birkaç dakika sürebilir. Bu süre, elastik sorgu işlevinin yüklenmesi için gereklidir; daha yüksek hizmet katmanları ve işlem boyutları ile yükleme performansı artar.
* SSMS veya SSDT 'den dış veri kaynaklarının veya dış tabloların betiği henüz desteklenmiyor.
* SQL DB için içeri/dışarı aktarma henüz dış veri kaynaklarını ve dış tabloları desteklemez. Içeri/dışarı aktarma kullanmanız gerekiyorsa, dışarı aktarmadan önce bu nesneleri bırakın ve içeri aktardıktan sonra yeniden oluşturun.
* Elastik sorgu şu anda yalnızca dış tablolara salt okuma erişimini desteklemektedir. Ancak, dış tablonun tanımlandığı veritabanında tam T-SQL işlevselliği kullanabilirsiniz. Bu, örneğin, kullanarak geçici sonuçları kalıcı hale getirmek için faydalı olabilir, örneğin, < column_list > < local_table > veya dış tablolara başvuran elastik sorgu veritabanında saklı yordamları tanımlamak için yararlı olabilir.
* Nvarchar (max) dışında, LOB türleri (uzamsal türler dahil) dış tablo tanımlarında desteklenmez. Geçici bir çözüm olarak, LOB türünü nvarchar (max) olarak veren uzak veritabanında bir görünüm oluşturabilir, dış tablonuzu temel tablo yerine görünüm üzerinden tanımlayabilir ve sonra Sorgularınızdaki özgün LOB türüne geri çevirebilirsiniz.
* Sonuç kümesi içindeki nvarchar (max) veri türü sütunları, elastik sorgu uygulamasında kullanılan gelişmiş toplu işlem Technics devre dışı bırakır ve hatta büyük miktarda bir büyüklük için sorgu performansını etkileyebilir toplanmayan veriler sorgu sonucu olarak aktarılır.
* Dış tablolar üzerinde sütun istatistikleri Şu anda desteklenmiyor. Tablo istatistikleri desteklenir, ancak el ile oluşturulması gerekir.
* Elastik sorgu yalnızca Azure SQL veritabanı ile birlikte kullanılabilir. Şirket içi SQL Server veya bir VM 'de SQL Server sorgulamak için kullanamazsınız.

## <a name="feedback"></a>Geri Bildirim

Aşağıda, MSDN forumlarında veya Stack Overflow üzerinde bizimle elastik sorgular ile deneyiminizle ilgili geri bildirim paylaşabilirsiniz. Hizmetle ilgili her türlü geri bildirimde bulunmak istiyoruz (kusur, kaba kenarlar, özellik boşlukları).

## <a name="next-steps"></a>Sonraki adımlar

* Dikey bölümleme öğreticisi için bkz. [çapraz veritabanı sorgusuna Başlarken (dikey bölümlendirme)](sql-database-elastic-query-getting-started-vertical.md).
* Dikey olarak bölümlenmiş verilere yönelik sözdizimi ve örnek sorgular için bkz. [dikey olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-vertical-partitioning.md)
* Yatay bölümleme (parçalama) öğreticisi için bkz. [Yatay bölümleme (parçalama) için elastik sorgu ile çalışmaya](sql-database-elastic-query-getting-started.md)başlama.
* Yatay olarak bölümlenmiş veriler için sözdizimi ve örnek sorgular için bkz. [yatay olarak bölümlenmiş verileri sorgulama)](sql-database-elastic-query-horizontal-partitioning.md)
* Tek bir uzak Azure SQL veritabanı üzerinde Transact-SQL ifadesini yürüten saklı yordam için bkz. [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) , yatay bölümleme düzeninde parçalar olarak hizmet veren veritabanları kümesi.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
