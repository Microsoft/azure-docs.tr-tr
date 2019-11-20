---
title: Çok modelli özellikler
description: Azure SQL veritabanı, aynı veritabanında birden çok veri modeli ile çalışmanıza olanak sağlar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802824"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Azure SQL veritabanı 'nın çok modelli özellikleri

Çok modelli veritabanları, ilişkisel veriler, grafikler, JSON/XML belgeleri, anahtar-değer çiftleri vb. gibi birden çok veri biçiminde temsil edilen verileri depolamanızı ve bunlarla çalışmanızı sağlar.

## <a name="when-to-use-multi-model-capabilities"></a>Çok modelli yetenekler ne zaman kullanılır?

Azure SQL veritabanı, çeşitli genel amaçlı uygulamalar için çoğu durumda en iyi performansı sağlayan ilişkisel modelle çalışacak şekilde tasarlanmıştır. Ancak, Azure SQL veritabanı yalnızca ilişkisel verilerle sınırlı değildir. Azure SQL veritabanı, ilişkisel modele sıkı bir şekilde tümleştirilmiş, ilişkisel olmayan çeşitli biçimleri kullanmanıza olanak sağlar.
Aşağıdaki durumlarda Azure SQL veritabanı 'nın birden çok modelli yeteneklerini kullanmayı göz önünde bulundurmanız gerekir:
- NoSQL modellerine daha iyi uyan bazı bilgi veya yapılar vardır ve ayrı NoSQL veritabanı kullanmak istemezsiniz.
- Verilerinizin çoğunluğu ilişkisel model için uygundur ve NoSQL stilinde verilerinizin bazı parçalarını modelleyebilirsiniz.
- Hem ilişkisel hem de NoSQL verilerini sorgulamak ve analiz etmek için zengin Transact-SQL dilinden yararlanmak ve bunu SQL dilini kullanan çeşitli araçlar ve uygulamalarla bütünleştirmek istiyorsunuz.
- NoSQL Veri yapılarınızın analitik veya işlemesinin performansını artırmak üzere [bellek içi teknolojiler](sql-database-in-memory.md) gibi veritabanı özelliklerini uygulamak, verilerinizin kopyasını oluşturmak için [İşlemsel çoğaltma](sql-database-managed-instance-transactional-replication.md) veya [okunabilir çoğaltmalar](sql-database-read-scale-out.md) kullanın diğer bir deyişle, birincil veritabanından bazı analitik iş yüklerinin yükünü devretmek.

## <a name="overview"></a>Genel Bakış

Azure SQL aşağıdaki çok modelli özellikleri sağlar:
- [Grafik özellikleri](#graph-features) , verilerinizi düğüm ve kenar kümesi olarak temsil etmeniz ve grafik verilerini sorgulamak için Graph `MATCH` işleciyle geliştirilmiş standart Transact-SQL sorguları kullanmanızı sağlar.
- [JSON özellikleri](#json-features) , JSON belgelerini tablolara yerleştirmeniz, ILIŞKISEL verileri JSON belgelerine dönüştürebilmeniz ve tam tersi de sağlar. Sorguları iyileştirmek için JSON işlevleriyle geliştirilmiş standart Transact-SQL dilini kullanabilir ve kümelenmemiş dizinler, columnstore dizinleri ya da bellek için iyileştirilmiş tablolar kullanabilirsiniz.
- [Uzamsal Özellikler](#spatial-features) , coğrafi ve geometrik verileri depolamanıza, uzamsal dizinleri kullanarak dizinlemenize ve uzamsal sorgular kullanarak verileri almanıza olanak sağlar.
- XML [özellikleri](#xml-features) , veritabanınıza XML verilerini depolayıp DIZINLERINIZI ve XML verileriyle çalışmak Için yerel XQuery/XPath işlemlerini kullanmanızı sağlar. Azure SQL veritabanı, XML verilerini işleyen özelleştirilmiş yerleşik XML sorgu motoruna sahiptir.
- Anahtar-değer çiftleri, iki sütunlu tablolar olarak yerel olarak modellendirildiğinden, [anahtar-değer çiftleri](#key-value-pairs) özel özellikler olarak açıkça desteklenmez.

  > [!Note]
  > Veritabanında depoladığınız tüm verilere erişmek için aynı Transact-SQL sorgusunda JSON yol ifadesini, XQuery/XPath ifadelerini, uzamsal işlevleri ve Graph-Query ifadelerini de kullanabilirsiniz. Ayrıca, Transact-SQL sorgularını yürüteerişebilen herhangi bir araç veya programlama dili, çok modelli verilere erişmek için bu sorgu arabirimini de kullanabilir. Bu, farklı veri modelleri için özelleştirilmiş API sağlayan [Azure Cosmos DB](/azure/cosmos-db/) gibi çok modelli veritabanlarıyla karşılaştırıldığında önemli farktır.

Aşağıdaki bölümlerde, SQL veritabanı 'nın en önemli çok modelli özellikleri hakkında bilgi edinebilirsiniz.

## <a name="graph-features"></a>Grafik özellikleri

Azure SQL veritabanı, veritabanında çok-çok ilişkilerini modelleyebilir grafik veritabanı özellikleri sunar. Grafik, düğümlerin (veya köşelerin) ve kenarlarının (ya da ilişkilerin) bir koleksiyonudur. Düğüm, bir varlığı (örneğin, bir kişi veya kuruluş) temsil eder ve bir kenar, bağlandığı iki düğüm (örneğin, beğeni veya arkadaşları) arasındaki ilişkiyi temsil eder. Grafik veritabanının benzersiz olmasını sağlayan bazı özellikler şunlardır:
- Kenarlar veya ilişkiler bir grafik veritabanındaki ilk sınıf varlıklardır ve bunlarla ilişkili özniteliklere veya özelliklere sahip olabilir.
- Tek bir kenar, bir grafik veritabanında birden çok düğümü esnek bir şekilde bağlanabilir.
- Model eşleştirme ve çok atlamalı gezinti sorgularını kolayca ifade edebilirsiniz.
- Geçişli kapanış ve Polimorfik sorguları kolayca ifade edebilirsiniz.

Grafik ilişkileri ve grafik sorgu özellikleri Transact-SQL ile tümleşiktir ve temel veritabanı yönetim sistemi olarak SQL Server kullanmanın avantajlarını alır.
[Grafik işleme](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) , çekirdek SQL Server veritabanı altyapısı özelliğidir, bu nedenle burada grafik işleme hakkında daha fazla bilgi edinebilirsiniz.

### <a name="when-to-use-a-graph-capability"></a>Grafik özelliği ne zaman kullanılır?

Bir grafik veritabanının elde edilebileceği, ilişkisel bir veritabanı kullanılarak ulaşılabilecek bir şey yok. Ancak, bir grafik veritabanı belirli sorguları hızlı bir şekilde ifade edebilir. Diğeri arasından birini seçme kararınız aşağıdaki etkenlere bağlı olabilir:

- Bir düğümün birden çok üst öğesi olabilecek hiyerarşik verileri modelleyin, bu nedenle HierarchyId kullanılamaz
- Modelde, uygulamanızın çok-çok ilişkileri karmaşık; Uygulama geliştikçe yeni ilişkiler eklenir.
- Bağlantılı verileri ve ilişkileri analiz etmeniz gerekir.

## <a name="json-features"></a>JSON özellikleri

Azure SQL veritabanı, JavaScript Nesne Gösterimi [(JSON)](https://www.json.org/) biçiminde temsil edilen verileri ayrıştırabilmenizi ve sorgulamanızı ve ILIŞKISEL verilerinizi JSON metni olarak dışarı aktarmanızı sağlar.

JSON, Modern Web ve mobil uygulamalarda veri alışverişi için kullanılan popüler bir veri biçimidir. JSON, yarı yapılandırılmış verileri günlük dosyalarında veya [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)gibi NoSQL veritabanlarında depolamak için de kullanılır. Birçok REST Web hizmeti, JSON metni olarak biçimlendirilen sonuçları döndürür veya JSON olarak biçimlendirilen verileri kabul eder. [Azure bilişsel arama](https://azure.microsoft.com/services/search/), [azure depolama](https://azure.microsoft.com/services/storage/)ve [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) gibi birçok Azure HIZMETI, JSON döndüren veya tüketen REST uç noktalarına sahiptir.

Azure SQL veritabanı, JSON verileriyle kolayca çalışmanıza ve veritabanınızı modern hizmetlerle tümleştirmenize imkan tanır. Azure SQL veritabanı, JSON verileriyle çalışmak için aşağıdaki işlevleri sağlar:

![JSON Işlevleri](./media/sql-database-json-features/image_1.png)

JSON metinleriniz varsa JSON 'dan veri ayıklayabilir veya [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)ve [ıSJSON](https://msdn.microsoft.com/library/dn921896.aspx)yerleşik işlevlerini kullanarak JSON 'ın düzgün biçimlendirildiğinden emin olabilirsiniz. [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) IşLEVI, JSON metni içindeki değeri güncelleştirmenizi sağlar. Daha gelişmiş sorgulama ve çözümleme için, [openjson](https://msdn.microsoft.com/library/dn921885.aspx) işlevi BIR dizi JSON nesnesini bir dizi satır içine dönüştürebilir. Döndürülen sonuç kümesinde herhangi bir SQL sorgusu çalıştırılabilir. Son olarak, ilişkisel Tablolarınızda depolanan verileri JSON metni olarak biçimlendirmenize imkan tanıyan bir [for JSON](https://msdn.microsoft.com/library/dn921882.aspx) yan tümcesi vardır.

Daha fazla bilgi için bkz. [Azure SQL veritabanı 'NDA JSON verileriyle çalışma](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) , bir veritabanı altyapısı özelliği SQL Server olduğundan JSON özelliği hakkında daha fazla bilgi edinebilirsiniz.

### <a name="when-to-use-a-json-capability"></a>JSON özelliği ne zaman kullanılır?

Belge modelleri, bazı belirli senaryolarda ilişkisel modeller yerine kullanılabilir:
- Tek seferde tüm nesne alanlarına erişirken veya nesnelerin normalleştirilmiş kısımlarını hiçbir zaman güncelleştirmeyeceğinden şemanın yüksek düzeyde normalleştirilmesi önemli avantajlar getirmez. Ancak, verileri almak için katılmanız gereken çok sayıda tablo olması nedeniyle, normalleştirilmiş model, sorgularınızın karmaşıklığını artırır.
- JSON belgelerini yerel olarak kullanan uygulamalarla çalışıyorsunuz iletişim veya veri modelleridir ve ilişkisel verileri JSON ve tam tersi dönüştüren ek katmanlar tanıtmak istemezsiniz.
- Alt tabloları veya varlık-nesne-değer düzenlerini yeniden normalleştirerek veri modelinizi basitleştirmeniz gerekir.
- Verileri çözümleyen ek bir araç olmadan JSON biçiminde depolanan verileri yüklemeniz veya dışarı aktarmanız gerekir.

## <a name="spatial-features"></a>Uzamsal Özellikler

Uzamsal veriler, geometrik nesnelerin fiziksel konumu ve şekli hakkındaki bilgileri temsil eder. Bu nesneler, nokta konumları veya ülkeler/bölgeler, yollar veya Lakes gibi daha karmaşık nesneler olabilir.

Azure SQL veritabanı iki uzamsal veri türünü destekler-geometri veri türü ve Coğrafya veri türü.
- Geometri türü, bir Euclidean (düz) koordinat sistemindeki verileri temsil eder.
- Coğrafya türü, bir yuvarlak dünya koordinat sistemindeki verileri temsil eder.

Azure SQL veritabanı 'nda [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Çokgen](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)gibi birçok uzamsal nesne vardır.

Azure SQL veritabanı, uzamsal Sorgularınızın performansını geliştirmek için kullanılabilecek özelleştirilmiş [uzamsal dizinler](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) de sağlar.

[Uzamsal destek](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) , temel SQL Server veritabanı altyapısı özelliğidir. bu sayede, burada uzamsal özelliği hakkında daha fazla bilgi edinebilirsiniz.

## <a name="xml-features"></a>XML özellikleri

SQL Server, yarı yapılandırılmış veri yönetimi için zengin uygulamalar geliştirmeye yönelik güçlü bir platform sağlar. For XML desteği SQL Server içindeki tüm bileşenlerle tümleşiktir ve şunları içerir:

- XML veri türü. Xml değerleri, bir XML şemaları koleksiyonuna göre yazılabilecek bir XML veri türü sütununda yerel olarak depolanabilir veya sola yazılmamış olabilir. XML sütununu dizinlemek için.
- XML veri türünde sütunlarda ve değişkenlerde depolanan XML verilerinde bir XQuery sorgusu belirtme özelliği. XQuery işlevleri, veritabanınızda kullandığınız herhangi bir veri modeline erişen herhangi bir Transact-SQL sorgusunda kullanılabilir.
- [BIRINCIL XML dizinini](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) kullanarak XML belgelerindeki tüm öğeleri otomatik olarak dizine alarak, [İkincil xml dizini](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)kullanılarak dizin oluşturulması gereken tam yolları belirtin.
- XML verilerinin toplu yüklenmesine izin veren OPENROWSET.
- İlişkisel verileri XML biçimine dönüştürün.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) , temel SQL Server veritabanı altyapısı özelliğidir, bu nedenle burada xml özelliği hakkında daha fazla bilgi edinebilirsiniz.

### <a name="when-to-use-an-xml-capability"></a>XML özelliği ne zaman kullanılır?

Belge modelleri, bazı belirli senaryolarda ilişkisel modeller yerine kullanılabilir:
- Tek seferde tüm nesne alanlarına erişirken veya nesnelerin normalleştirilmiş kısımlarını hiçbir zaman güncelleştirmeyeceğinden şemanın yüksek düzeyde normalleştirilmesi önemli avantajlar getirmez. Ancak, verileri almak için katılmanız gereken çok sayıda tablo olması nedeniyle, normalleştirilmiş model, sorgularınızın karmaşıklığını artırır.
- XML belgelerinin yerel olarak kullanıldığı uygulamalarla çalışıyorsunuz iletişim veya veri modelleridir ve ilişkisel verileri XML ve tam tersi dönüştüren ek katmanlar tanıtmak istemezsiniz.
- Alt tabloları veya varlık-nesne-değer düzenlerini yeniden normalleştirerek veri modelinizi basitleştirmeniz gerekir.
- Verileri çözümleyen ek bir araç olmadan XML biçiminde depolanan verileri yüklemeniz veya dışarı aktarmanız gerekir.

## <a name="key-value-pairs"></a>Anahtar-değer çiftleri

Anahtar-değer yapıları standart ilişkisel tablolar olarak yerel olarak temsil edilemediğinden, Azure SQL veritabanı 'nın anahtar-değer çiftlerini destekleyen özel türleri veya yapıları yoktur:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Bu anahtar-değer yapısını, herhangi bir kısıtlama olmadan gereksinimlerinize uyacak şekilde özelleştirebilirsiniz. Örnek olarak, değer JSON belgesi ise `nvarchar(max)` türü yerine XML belgesi olabilir, JSON içeriğinin geçerliliğini doğrulayan `CHECK` kısıtlaması koyabilirsiniz. Ek sütunlarda bir anahtarla ilgili herhangi bir sayıda değer koyabilirsiniz, veri erişimini basitleştirmek ve iyileştirmek için hesaplanmış sütunlar ve dizinler ekleyebilirsiniz, daha iyi performans sağlamak için tabloyu bellek/en iyileştirilmiş şema tablosu olarak tanımlayın, vb.

Bkz. [BWin 'Nın bellek ıçı OLTP kullanarak](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) , bir saniyede 1.200.000 toplu işi elde eden bir örnek olarak, ilişkisel modelin, içinde anahtar-değer çifti çözümü olarak nasıl etkili bir şekilde kullanılabileceğini gösteren ASP.NET önbelleğe alma çözümü Sonuç.

## <a name="next-steps"></a>Sonraki adımlar
Azure SQL veritabanlarında çok modelli özellikler ayrıca Azure SQL veritabanı ve SQL Server arasında paylaşılan temel SQL Server veritabanı altyapısı özellikleridir. Bu özellikler hakkında daha fazla bilgi edinmek için SQL Ilişkisel veritabanı belge sayfalarını ziyaret edin:

* [Grafik işleme](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON verileri](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Uzamsal destek](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML verileri](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
