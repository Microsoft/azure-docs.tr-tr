---
title: Uygulama performansını artırmak için toplu işlem kullanma
description: Bu konuda, veritabanı işlemlerinin toplu olarak, Azure SQL veritabanı uygulamalarınızın hızını ve ölçeklenebilirliğini büyük ölçüde geliştirip gelişmediğini belirten bir kanıt sunulmaktadır. Bu toplu işlem teknikleri tüm SQL Server veritabanları için çalışabilse de makalenin konusu Azure 'da yer alır.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 175ba6b4e65b4a6e276dbfb586e210027a6cd9b3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822411"
---
# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>SQL veritabanı uygulama performansını artırmak için toplu işlem kullanma

Azure SQL veritabanı 'na toplu işlem işlemleri, uygulamalarınızın performansını ve ölçeklenebilirliğini önemli ölçüde geliştirir. Avantajları anlamak için, bu makalenin ilk kısmı sıralı ve toplu istekleri bir SQL veritabanı ile karşılaştıran bazı örnek test sonuçlarını ele almaktadır. Makalenin geri kalanında Azure uygulamalarınızda toplu işlem oluşturmayı başarıyla kullanmanıza yardımcı olacak teknikler, senaryolar ve önemli noktalar gösterilmektedir.

## <a name="why-is-batching-important-for-sql-database"></a>SQL veritabanı için neden toplu işlem önemlidir?

Uzak bir hizmete yapılan toplu çağrılar, performansı ve ölçeklenebilirliği artırmak için iyi bilinen bir stratejidir. Serileştirme, ağ aktarımı ve seri durumundan çıkarma gibi uzak bir hizmetle her türlü etkileşimlere yönelik sabit işleme maliyetleri vardır. Tek bir toplu işte çok sayıda ayrı işlem paketleme bu maliyetleri en aza indirir.

Bu yazıda, çeşitli SQL veritabanı toplu işlem stratejilerini ve senaryolarını incelemek istiyoruz. Bu stratejiler SQL Server kullanan şirket içi uygulamalar için de önemli olsa da SQL veritabanı için toplu işlem kullanımını vurgulamanın çeşitli nedenleri vardır:

* SQL veritabanına erişirken, özellikle de aynı Microsoft Azure veri merkezi dışından SQL veritabanı 'na erişirken büyük olasılıkla daha fazla ağ gecikmesi vardır.
* SQL veritabanı 'nın çoklu kiracı özellikleri, veri erişim katmanının verimliliğinin veritabanının genel ölçeklenebilirliğiyle ilişkili olduğu anlamına gelir. SQL veritabanı, tek bir kiracının/kullanıcının veritabanı kaynaklarını diğer kiracıların dekeline karşı kullanmasını önlemektir. Önceden tanımlanmış kotaların daha fazla olması halinde SQL veritabanı, performansı azaltabilir veya azaltma özel durumları ile yanıt verebilir. Toplu işlem gibi verimlilik, bu sınırlara ulaşmadan önce SQL veritabanı üzerinde daha fazla çalışma olanağı sağlar. 
* Toplu işlem, birden çok veritabanı kullanan mimariler için de geçerlidir (parçalama). Her veritabanı birimi ile etkileşimin verimliliği, genel ölçeklenebilirlik için hala önemli bir faktördür. 

SQL veritabanı kullanmanın avantajlarından biri, veritabanını barındıran sunucuları yönetmeniz gerekmez. Ancak, bu yönetilen altyapı ayrıca veritabanı iyileştirmeleri hakkında farklı düşünmeniz gereken anlamına gelir. Artık veritabanı donanımını veya ağ altyapısını geliştirmek için göz atabilirsiniz. Microsoft Azure bu ortamları denetler. Denetleyebilmeniz gereken ana alan, uygulamanızın SQL veritabanı ile nasıl etkileşime gidir. Toplu işleme, bu iyileştirmelerin biridir. 

Kağıdın ilk bölümü, SQL veritabanı kullanan .NET uygulamalarına yönelik çeşitli toplu işlem tekniklerini inceler. Son iki bölüm toplu işlem kılavuzlarını ve senaryolarını kapsar.

## <a name="batching-strategies"></a>Toplu işleme stratejileri

### <a name="note-about-timing-results-in-this-article"></a>Bu makaledeki zamanlama sonuçları hakkında bilgi alın

> [!NOTE]
> Sonuçlar kıyaslamalar değildir ancak **göreli performansı**göstermek için tasarlanmıştır. Zamanlamalar, en az 10 test çalıştırmalarının ortalamasını temel alır. İşlemler boş bir tabloya eklenir. Bu sınamalar önceden V12 ölçülmüş ve yeni [DTU hizmeti katmanlarını](sql-database-service-tiers-dtu.md) veya [Vcore hizmeti katmanlarını](sql-database-service-tiers-vcore.md)kullanarak bir V12 veritabanında karşılaşabileceğiniz aktarım hızına karşılık gelmez. Toplu işleme tekniğinin göreli avantajı benzer olmalıdır.

### <a name="transactions"></a>İşlemler

İşlemleri tartışarak toplu işi gözden geçirmeyi başlatmak garip bir şekilde görünür. Ancak, istemci tarafı işlemlerin kullanımı performansı artıran hafif bir sunucu tarafı toplu işlem etkisine sahiptir. Ve işlemler yalnızca birkaç satır kodla eklenebilir, bu nedenle sıralı işlemlerin performansını artırmanın hızlı bir yolunu sağlar.

Basit bir tabloda C# INSERT ve Update işlemleri dizisini içeren aşağıdaki kodu göz önünde bulundurun.

```csharp
List<string> dbOperations = new List<string>();
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
dbOperations.Add("insert MyTable values ('new value',1)");
dbOperations.Add("insert MyTable values ('new value',2)");
dbOperations.Add("insert MyTable values ('new value',3)");
```
Aşağıdaki ADO.NET kodu ardışık olarak bu işlemleri gerçekleştirir.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();

    foreach(string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn);
        cmd.ExecuteNonQuery();
    }
}
```

Bu kodu iyileştirmenin en iyi yolu, bu çağrıların bazı istemci tarafı toplu işlem biçimini uygulamaktır. Ancak, bu kodun performansını artırmanın basit bir yolu vardır ve yalnızca bir işlemdeki çağrı dizisini sarmalayarak. Bir işlem kullanan aynı kod aşağıda verilmiştir.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    conn.Open();
    SqlTransaction transaction = conn.BeginTransaction();

    foreach (string commandString in dbOperations)
    {
        SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
        cmd.ExecuteNonQuery();
    }

    transaction.Commit();
}
```

İşlemler aslında bu örneklerin her ikisinde de kullanılır. İlk örnekte, her bir çağrı örtük bir işlemdir. İkinci örnekte, açık bir işlem tüm çağrıları sarmalar. [Yazma öncesi işlem günlüğü](https://msdn.microsoft.com/library/ms186259.aspx)için belge başına, işlem tamamlandığında günlük kayıtları diske silinir. Bu nedenle, bir işleme daha fazla çağrı dahil ederek işlem günlüğüne yazma işlemi, işlem kaydedilene kadar geciktirebilirler. Aslında, sunucunun işlem günlüğüne yazma işlemleri için toplu işleme etkinleştiriliyor.

Aşağıdaki tabloda bazı geçici test sonuçları gösterilmektedir. Testler, ve işlemleri olmadan aynı sıralı eklemeleri gerçekleştirdi. Daha fazla bakış için, ilk test kümesi bir dizüstü bilgisayardan Microsoft Azure içindeki veritabanına uzaktan çalışır. İkinci test kümesi, her ikisi de aynı Microsoft Azure veri merkezi (Batı ABD) içinde yer alan bir bulut hizmetinden ve veritabanından çalışır. Aşağıdaki tabloda, işlemleri ile ve olmayan sıralı ekleme süresinin milisaniye cinsinden gösterilmektedir.

**Şirket Içinden Azure 'a**:

| İşlemler | Işlem yok (MS) | İşlem (MS) |
| --- | --- | --- |
| 1 |130 |402 |
| 10 |1208 |1226 |
| 100 |12662 |10395 |
| 1000 |128852 |102917 |

**Azure 'Dan Azure 'a (aynı veri merkezi)** :

| İşlemler | Işlem yok (MS) | İşlem (MS) |
| --- | --- | --- |
| 1 |21 |26 |
| 10 |220 |56 |
| 100 |2145 |341 |
| 1000 |21479 |2756 |

> [!NOTE]
> Sonuçlar kıyaslamalar değildir. [Bu makaledeki zamanlama sonuçları hakkında nota](#note-about-timing-results-in-this-article)bakın.

Önceki test sonuçlarına bağlı olarak, bir işlemdeki tek bir işlemin sarmalanması performansı düşürür. Ancak, tek bir işlem içindeki işlem sayısını artırdıkça, performans iyileştirmesi daha fazla işaretlenmiş olur. Performans farkı, Microsoft Azure veri merkezinde tüm işlemler gerçekleştiğinde da daha belirgin bir şekilde görülür. Microsoft Azure veri merkezi dışından SQL veritabanı kullanmanın artmasıyla gecikme süresi, işlemleri kullanmanın performans kazanmasına neden olacak.

İşlemlerin kullanımı performansı artırabilir, ancak [işlemler ve bağlantılar için en iyi yöntemleri gözlemlemeye](https://msdn.microsoft.com/library/ms187484.aspx)devam edin. İşlemi mümkün olduğunca kısa tutun ve iş tamamlandıktan sonra veritabanı bağlantısını kapatın. Önceki örnekteki using deyimleri, sonraki kod bloğu tamamlandığında bağlantının kapalı olmasını sağlar.

Önceki örnekte, iki satırlık herhangi bir ADO.NET koduna yerel bir işlem ekleyebileceğinizi gösterir. İşlemler sıralı ekleme, güncelleştirme ve silme işlemleri yapan kodun performansını artırmanın hızlı bir yolunu sunar. Bununla birlikte, en hızlı performans için, tablo değerli parametreler gibi istemci tarafı toplu işlem avantajlarından faydalanmak için kodu daha fazla değiştirmeyi göz önünde bulundurun.

ADO.NET içindeki işlemler hakkında daha fazla bilgi için bkz. [ADO.net Içindeki yerel işlemler](https://docs.microsoft.com/dotnet/framework/data/adonet/local-transactions).

### <a name="table-valued-parameters"></a>tablo değerli parametreler

Tablo değerli parametreler, Kullanıcı tanımlı tablo türlerini Transact-SQL deyimleriyle, saklı yordamlarda ve işlevlerde parametre olarak destekler. Bu istemci tarafı toplu işleme tekniği, tablo değerli parametre içinde birden fazla veri satırı göndermenizi sağlar. Tablo değerli parametreleri kullanmak için önce bir tablo türü tanımlayın. Aşağıdaki Transact-SQL beyanı, **Mytabletype**adlı bir tablo türü oluşturur.

```sql
    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
```

Kodda, tablo türünün tam olarak aynı adlarıyla ve türleriyle bir **DataTable** oluşturacaksınız. Bu **DataTable** 'ı bir metin sorgusundaki veya saklı yordam çağrısındaki bir parametreye geçirin. Aşağıdaki örnek bu tekniği göstermektedir:

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    DataTable table = new DataTable();
    // Add columns and rows. The following is a simple example.
    table.Columns.Add("mytext", typeof(string));
    table.Columns.Add("num", typeof(int));
    for (var i = 0; i < 10; i++)
    {
        table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
    }

    SqlCommand cmd = new SqlCommand(
        "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
        connection);

    cmd.Parameters.Add(
        new SqlParameter()
        {
            ParameterName = "@TestTvp",
            SqlDbType = SqlDbType.Structured,
            TypeName = "MyTableType",
            Value = table,
        });

    cmd.ExecuteNonQuery();
}
```

Önceki örnekte, **SqlCommand** nesnesi tablo değerli bir parametreden, **testtvp\@** satır ekler. Önceden oluşturulan **DataTable** nesnesi, **SqlCommand. Parameters. Add** yöntemiyle bu parametreye atanır. Eklemeleri tek bir çağrıda toplu olarak işleme, sıralı eklemelerin performansını önemli ölçüde artırır.

Önceki örneği daha da geliştirmek için metin tabanlı komut yerine bir saklı yordam kullanın. Aşağıdaki Transact-SQL komutu, **Simpletesttabletype** tablo değerli parametresini alan bir saklı yordam oluşturur.

```sql
CREATE PROCEDURE [dbo].[sp_InsertRows] 
@TestTvp as MyTableType READONLY
AS
BEGIN
INSERT INTO MyTable(mytext, num) 
SELECT mytext, num FROM @TestTvp
END
GO
```

Ardından, önceki kod örneğinde **SqlCommand** nesne bildirimini aşağıdaki şekilde değiştirin.

```csharp
SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
cmd.CommandType = CommandType.StoredProcedure;
```

Çoğu durumda, tablo değerli parametrelerin diğer toplu işlem tekniklerinden eşdeğer veya daha iyi bir performansı vardır. Tablo değerli parametreler genellikle diğer seçeneklerden daha esnek olduklarından tercih edilir. Örneğin, SQL toplu kopyalama gibi diğer teknikler yalnızca yeni satırların eklenmesine izin verir. Ancak tablo değerli parametrelerle, hangi satırların hangilerinin ekleneceğini ve hangilerinin ekleneceğini tespit etmek için saklı yordamdaki mantığı kullanabilirsiniz. Tablo türü Ayrıca, belirtilen satırın eklenip eklenmeyeceğini, güncelleştirileceğini veya silinip silinmeyeceğini belirten bir "Işlem" sütunu içerecek şekilde değiştirilebilir.

Aşağıdaki tabloda, tablo değerli parametrelerin kullanım için milisaniye cinsinden geçici test sonuçları gösterilmektedir.

| İşlemler | Şirket Içinden Azure 'a (MS) | Azure aynı veri merkezi (MS) |
| --- | --- | --- |
| 1 |124 |32 |
| 10 |131 |25 |
| 100 |338 |51 |
| 1000 |2615 |382 |
| 10000 |23830 |3586 |

> [!NOTE]
> Sonuçlar kıyaslamalar değildir. [Bu makaledeki zamanlama sonuçları hakkında nota](#note-about-timing-results-in-this-article)bakın.
> 
> 

Toplu işleme ait performans kazancı hemen görünür. Önceki sıralı testte, 1000 işlemleri veri merkezinde 129 saniye ve veri merkezi içinde 21 saniye sürdü. Ancak tablo değerli parametrelerle 1000 işlem, veri merkezi içindeki veri merkezi ve 0,4 saniye dışında yalnızca 2,6 saniye sürer.

Tablo değerli parametreler hakkında daha fazla bilgi için bkz. [tablo değerli parametreler](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>SQL toplu kopyalama

SQL toplu kopyalama, bir hedef veritabanına büyük miktarlarda veri eklemenin başka bir yoludur. .NET uygulamaları toplu ekleme işlemleri gerçekleştirmek için **SqlBulkCopy** sınıfını kullanabilir. **SqlBulkCopy** , işlevinde komut satırı aracına, **bcp. exe**' ye veya Transact-SQL bildirimine benzer **bulk INSERT**. Aşağıdaki kod örneği, kaynak **DataTable**, tablosundaki satırların SQL Server, myTable içindeki hedef tabloya nasıl toplu şekilde kopyalanacağını gösterir.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
    {
        bulkCopy.DestinationTableName = "MyTable";
        bulkCopy.ColumnMappings.Add("mytext", "mytext");
        bulkCopy.ColumnMappings.Add("num", "num");
        bulkCopy.WriteToServer(table);
    }
}
```

Toplu kopyalamanın tablo değerli parametreler üzerinden tercih edildiği bazı durumlar vardır. Tablo değerli parametrelerin karşılaştırma tablosuna ve [tablo değerli parametrelerde](https://msdn.microsoft.com/library/bb510489.aspx)bulk INSERT işlemlerine bakın.

Aşağıdaki geçici test sonuçları, her saniye içinde **SqlBulkCopy** ile toplu işleme performansını gösterir.

| İşlemler | Şirket Içinden Azure 'a (MS) | Azure aynı veri merkezi (MS) |
| --- | --- | --- |
| 1 |433 |57 |
| 10 |441 |32 |
| 100 |636 |53 |
| 1000 |2535 |341 |
| 10000 |21605 |2737 |

> [!NOTE]
> Sonuçlar kıyaslamalar değildir. [Bu makaledeki zamanlama sonuçları hakkında nota](#note-about-timing-results-in-this-article)bakın.
> 
> 

Daha küçük toplu iş boyutlarında, tablo değerli parametreleri kullan parametresi **SqlBulkCopy sınıfını başarıyla** gerçekleştirdi. Ancak, **SqlBulkCopy** , 1.000 ve 10.000 satırları testleri için tablo değerli parametrelerden% 12-31 daha hızlı gerçekleştirdi. Tablo değerli parametreler gibi, **SqlBulkCopy** , özellikle toplu olmayan işlemlerin performansına kıyasla, toplu ekleme için iyi bir seçenektir.

ADO.NET ' de toplu kopyalama hakkında daha fazla bilgi için [SQL Server Içindeki toplu kopyalama işlemleri](https://msdn.microsoft.com/library/7ek5da1a.aspx)konusuna bakın.

### <a name="multiple-row-parameterized-insert-statements"></a>Birden çok satır parametreli INSERT deyimleri

Küçük toplu işlemlere yönelik bir alternatif, birden çok satır ekleyen büyük parametreli INSERT deyimlerinin oluşturulması içindir. Aşağıdaki kod örneği bu tekniği gösterir.

```csharp
using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
{
    connection.Open();

    string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
        "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";

    SqlCommand cmd = new SqlCommand(insertCommand, connection);

    for (int i = 1; i <= 10; i += 2)
    {
        cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
        cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
    }

    cmd.ExecuteNonQuery();
}
```

Bu örnek, temel kavramı göstermek için tasarlanmıştır. Daha gerçekçi bir senaryo, sorgu dizesini ve komut parametrelerini aynı anda oluşturmak için gereken varlıklarda döngü sağlar. Toplam 2100 sorgu parametresi ile sınırlı olursunuz, bu nedenle bu şekilde işlenebilecek toplam satır sayısını kısıtlar.

Aşağıdaki geçici test sonuçları, bu tür INSERT deyimlerinin performansını milisaniye cinsinden gösterir.

| İşlemler | Tablo değerli parametreler (MS) | Tek deyimli Insert (MS) |
| --- | --- | --- |
| 1 |32 |20 |
| 10 |30 |25 |
| 100 |33 |51 |

> [!NOTE]
> Sonuçlar kıyaslamalar değildir. [Bu makaledeki zamanlama sonuçları hakkında nota](#note-about-timing-results-in-this-article)bakın.
> 
> 

Bu yaklaşım 100 satırdan az olan toplu işlemler için biraz daha hızlı olabilir. Geliştirme küçük olsa da, bu teknik, özel uygulama senaryonuzda uygun olabilecek başka bir seçenektir.

### <a name="dataadapter"></a>✔

**DataAdapter** sınıfı, bir **veri kümesi** nesnesini DEĞIŞTIRMENIZE ve sonra değişiklikleri INSERT, Update ve DELETE işlemleri olarak göndermenize olanak tanır. Bu şekilde **DataAdapter** kullanıyorsanız, her farklı işlem için ayrı çağrıların yapıldığını unutmayın. Performansı artırmak için, **UpdateBatchSize** özelliğini tek seferde toplu olarak oluşturulacak işlem sayısına göre kullanın. Daha fazla bilgi için bkz. [DataAdapter kullanarak Batch Işlemleri gerçekleştirme](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Varlık çerçevesi

Entity Framework, şu anda toplu işlemeyi desteklemiyor. Topluluktaki farklı geliştiriciler, **SaveChanges** metodunu geçersiz kılma gibi geçici çözümler göstermeye çalıştı. Ancak çözümler genellikle karmaşıktır ve uygulama ve veri modeli için özelleştirilir. Entity Framework CodePlex projesi şu anda bu özellik isteğinde bir tartışma sayfasına sahip. Bu tartışmayı görüntülemek için bkz. [Toplantı notlarını tasarlama-2 ağustos 2012](https://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>'SINI

Bir toplu işlem stratejisi olarak XML hakkında konuşmak için önemli olduğunu biliyoruz. Ancak, XML kullanımı diğer yöntemlere ve birçok dezavantaja yönelik avantaja sahip değildir. Yaklaşım tablo değerli parametrelere benzerdir, ancak bir XML dosyası veya dize, Kullanıcı tanımlı bir tablo yerine bir saklı yordama geçirilir. Saklı yordam saklı yordamdaki komutları ayrıştırır.

Bu yaklaşımın çeşitli dezavantajları vardır:

* XML ile çalışma, sıkıcı ve hataya açık olabilir.
* Veritabanında XML ayrıştırma CPU kullanımı yoğun olabilir.
* Çoğu durumda, bu yöntem tablo değerli parametrelerden daha yavaştır.

Bu nedenlerden dolayı, Batch sorguları için XML kullanılması önerilmez.

## <a name="batching-considerations"></a>Toplu işleme konuları

Aşağıdaki bölümler SQL veritabanı uygulamalarında toplu işlem kullanımı için daha fazla rehberlik sağlar.

### <a name="tradeoffs"></a>Bileşim

Mimarinize bağlı olarak, toplu işleme performans ve dayanıklılık arasında bir zorunluluğunu getirir içerebilir. Örneğin, rolünüzün beklenmedik bir şekilde sona uğradığında senaryoyu göz önünde bulundurun. Bir veri satırını kaybederseniz, etki çok büyük bir toplu yığın olmayan satırları kaybetme etkisinden daha küçüktür. Belirli bir zaman penceresinde veritabanına göndermeden önce satırları arabelleğe aldığınızda daha fazla risk vardır.

Bu zorunluluğunu getirir nedeniyle, toplu işlem yaptığınız işlemlerin türünü değerlendirin. Daha az kritik verilerle toplu olarak daha fazla kararlılık (daha büyük toplu işler ve daha uzun zaman pencereleri).

### <a name="batch-size"></a>Toplu iş boyutu

Testlerimizde, genellikle büyük toplu işleri küçük parçalara ayırma avantajı yoktur. Aslında, bu alt bölüm genellikle tek bir büyük toplu işlem gönderilmeden daha yavaş performansa neden olur. Örneğin, 1000 satır eklemek istediğiniz bir senaryoyu düşünün. Aşağıdaki tabloda, daha küçük toplu işlemlere ayrıldığınızda 1000 satır eklemek için tablo değerli parametrelerin ne kadar süreceği gösterilmektedir.

| Toplu iş boyutu | Tekrarları | Tablo değerli parametreler (MS) |
| --- | --- | --- |
| 1000 |1 |347 |
| 500 |2 |355 |
| 100 |10 |465 |
| 50 |20 |630 |

> [!NOTE]
> Sonuçlar kıyaslamalar değildir. [Bu makaledeki zamanlama sonuçları hakkında nota](#note-about-timing-results-in-this-article)bakın.
> 
> 

1000 satırı için en iyi performansın her seferinde bir kez göndermesi gerektiğini görebilirsiniz. Diğer testlerde (burada gösterilmez), 10000 satır toplu işini iki farklı 5000 toplu işe bölmek için küçük bir performans kazancı vardı. Ancak bu testlerin tablo şeması nispeten basittir. bu nedenle, bu bulguları doğrulamak için belirli verileriniz ve toplu iş boyutlarınız üzerinde testler gerçekleştirmeniz gerekir.

Göz önünde bulundurulması gereken başka bir faktör ise, toplam toplu işlem çok büyük hale gelirse SQL veritabanı, toplu işi gerçekleştirmeyi ve reddedebilir. En iyi sonuçlar için, ideal bir toplu iş boyutu olup olmadığını öğrenmek üzere belirli senaryonuzu test edin. Performans veya hatalara göre hızlı ayarlamaları etkinleştirmek için toplu iş boyutunu çalışma zamanında yapılandırılabilir hale getirin.

Son olarak, toplu işlemin boyutunu toplu işleme ile ilişkili riskler ile dengeleyin. Geçici hatalar varsa veya rol başarısız olursa, işlemi yeniden deneme veya toplu işteki verileri kaybetme sonuçlarını göz önünde bulundurun.

### <a name="parallel-processing"></a>Paralel işleme

Toplu iş boyutunu azaltmanın yaklaşımı gerçekleştirmeniz ancak çalışmayı yürütmek için birden çok iş parçacığı kullandıysanız ne olacak? Bundan sonra, sınamalarımız birkaç küçük çok iş parçacıklı toplu işlemin tipik olarak tek bir büyük toplu iş sürümünden daha kötü bir Aşağıdaki test bir veya daha fazla paralel toplu işlem içine 1000 satır eklemeye çalışır. Bu test, daha fazla eşzamanlı toplu işlerin performansı gerçekten azaltdığını gösterir.

| Toplu iş boyutu [yinelemeler] | İki iş parçacığı (MS) | Dört iş parçacığı (MS) | Altı iş parçacığı (MS) |
| --- | --- | --- | --- |
| 1000 [1] |277 |315 |266 |
| 500 [2] |548 |278 |256 |
| 250 [4] |405 |329 |265 |
| 100 [10] |488 |439 |391 |

> [!NOTE]
> Sonuçlar kıyaslamalar değildir. [Bu makaledeki zamanlama sonuçları hakkında nota](#note-about-timing-results-in-this-article)bakın.
> 
> 

Paralellik nedeniyle performans düşüşünün birkaç olası nedeni vardır:

* Bunun yerine birden çok eş zamanlı ağ çağrısı vardır.
* Tek bir tabloya karşı birden çok işlem çekişmeye ve engellemeye yol açabilir.
* Çoklu iş parçacıklı ile ilişkili fazla kafa vardır.
* Birden çok bağlantı açma gideri, paralel işlemenin avantajına göre daha ağır.

Farklı tabloları veya veritabanlarını hedefliyorsanız, bu stratejiyle ilgili bazı performans kazanımı görmeniz mümkündür. Veritabanı parçaları veya Federasyonları bu yaklaşım için bir senaryo olabilir. Parçalama birden çok veritabanını kullanır ve her bir veritabanına farklı verileri yönlendirir. Her küçük toplu iş farklı bir veritabanına gitecekse, işlemleri paralel olarak gerçekleştirmek daha verimli olabilir. Ancak, performans kazancı, çözümünüzde veritabanı parçalama kullanma kararının temeli olarak kullanılmak üzere yeterince önemli değildir.

Bazı tasarımlarda, daha küçük toplu işlerin paralel yürütmesi yük altında bir sistemdeki isteklerin artmasında daha fazla verim oluşmasına neden olabilir. Bu durumda, tek bir büyük toplu işi işlemek daha hızlı olsa da, paralel olarak birden çok toplu işin işlenmesi daha verimli olabilir.

Paralel yürütme kullanırsanız, en fazla çalışan iş parçacığı sayısını denetlemeyi göz önünde bulundurun. Daha küçük bir sayı, daha az çekişme ve daha hızlı bir yürütme süresine neden olur. Ayrıca, bu, hedef veritabanında hem bağlantılar hem de işlemlerde yer alan ek yükü göz önünde bulundurun.

### <a name="related-performance-factors"></a>İlgili performans faktörleri

Veritabanı performansına ilişkin tipik yönergeler de toplu işlemeyi etkiler. Örneğin, büyük bir birincil anahtara veya çok sayıda kümelenmemiş dizine sahip tablolar için ekleme performansı azalır.

Tablo değerli parametreler saklı yordam kullanıyorsa, yordamın başlangıcında **NOCOUNT komut kümesini** kullanabilirsiniz. Bu ifade, yordamda etkilenen satırların sayısının döndürülmesini bastırır. Ancak, testlerimizde, **SET NOCOUNT** kullanımı hiçbir etkisi veya azaltıldı. Test saklı yordamı tablo değerli parametresindeki tek bir **Insert** komutuyla basittir. Daha karmaşık saklı yordamların bu deyimden faydalanabilir olması mümkündür. Ancak, saklı yordamınıza bir **NOCOUNT kümesi** eklemenin performansı otomatik olarak artırmadığını varsaymayın. Etkiyi anlamak için, saklı yordamınız ile ve **SET NOCOUNT on** ifadesiyle test edin.

## <a name="batching-scenarios"></a>Toplu işlem senaryoları

Aşağıdaki bölümlerde, tablo değerli parametrelerin üç uygulama senaryosunda nasıl kullanılacağı açıklanır. İlk senaryo, arabelleğe alma ve toplu işleme 'nin birlikte nasıl çalıştığını gösterir. İkinci senaryo, tek bir saklı yordam çağrısında ana ayrıntı işlemlerini gerçekleştirerek performansı geliştirir. Son senaryo, tablo değerli parametrelerin bir "UPSERT" işleminde nasıl kullanılacağını gösterir.

### <a name="buffering"></a>Ara

Toplu işleme için belirgin aday olan bazı senaryolar olsa da, Gecikmeli işleme tarafından toplu işlemin avantajlarından yararlanan birçok senaryo vardır. Ancak gecikmeli işleme, verilerin beklenmeyen bir hata durumunda kaybolduğu daha büyük bir risk taşır. Bu riski anlamak ve sonuçları göz önünde bulundurmanız önemlidir.

Örneğin, her kullanıcının gezinti geçmişini izleyen bir Web uygulaması düşünün. Her sayfa isteğinde, uygulama, kullanıcının sayfa görünümünü kaydetmek için bir veritabanı çağrısı yapabilir. Ancak daha yüksek performans ve ölçeklenebilirlik, kullanıcıların gezinti etkinliklerinin arabelleğe alınması ve sonra bu verilerin veritabanına toplu işlem halinde gönderilmesi ile elde edilebilir. Veritabanı güncelleştirmesini geçen süre ve/veya arabellek boyutuna göre tetikleyebilirsiniz. Örneğin, bir kural, toplu işin 20 saniye sonra veya arabelleğin 1000 öğeye ulaştığında işlenmesi gerektiğini belirtebilir.

Aşağıdaki kod örneği, bir izleme sınıfı tarafından oluşturulan arabelleğe alınmış olayları işlemek için [reaktif uzantıları-RX](https://msdn.microsoft.com/data/gg577609) kullanır. Arabellek dolguları veya zaman aşımı ulaşıldığında, Kullanıcı verisi toplu işi veritabanına tablo değerli bir parametre ile gönderilir.

Aşağıdaki Navgeçmişini veri sınıfı kullanıcı Gezinti ayrıntılarını modeller. Kullanıcı tanımlayıcısı, erişilen URL ve erişim zamanı gibi temel bilgileri içerir.

```csharp
public class NavHistoryData
{
    public NavHistoryData(int userId, string url, DateTime accessTime)
    { UserId = userId; URL = url; AccessTime = accessTime; }
    public int UserId { get; set; }
    public string URL { get; set; }
    public DateTime AccessTime { get; set; }
}
```

Navizdatamonitor sınıfı, Kullanıcı gezintisi verilerinin veritabanına arabelleğini sağlamaktan sorumludur. **OnAdded** olayını yükselterek yanıt veren bir yöntem, RecordUserNavigationEntry içerir. Aşağıdaki kod, olaya dayalı bir observable koleksiyonu oluşturmak için RX kullanan Oluşturucu mantığını gösterir. Daha sonra bu observable koleksiyonuna buffer yöntemiyle abone olur. Aşırı yükleme, arabelleğin 20 saniyede bir veya 1000 girişe gönderilmesi gerektiğini belirtir.

```csharp
public NavHistoryDataMonitor()
{
    var observableData =
        Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

    observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
}
```

İşleyici, tüm arabelleğe alınmış öğeleri tablo değerli bir türe dönüştürür ve sonra bu türü toplu işi işleyen bir saklı yordama geçirir. Aşağıdaki kod, hem navizdataeventargs hem de Navizdatamonitor sınıfları için tüm tanımlamayı gösterir.

```csharp
public class NavHistoryDataEventArgs : System.EventArgs
{
    public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
    public NavHistoryData Data { get; set; }
}

public class NavHistoryDataMonitor
{
    public event EventHandler<NavHistoryDataEventArgs> OnAdded;

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");

        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);
    }
```

İşleyici, tüm arabelleğe alınmış öğeleri tablo değerli bir türe dönüştürür ve sonra bu türü toplu işi işleyen bir saklı yordama geçirir. Aşağıdaki kod, hem navizdataeventargs hem de Navizdatamonitor sınıfları için tüm tanımlamayı gösterir.

```csharp
    public class NavHistoryDataEventArgs : System.EventArgs
    {
        if (OnAdded != null)
            OnAdded(this, new NavHistoryDataEventArgs(data));
    }

    protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
    {
        DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
        navHistoryBatch.Columns.Add("UserId", typeof(int));
        navHistoryBatch.Columns.Add("URL", typeof(string));
        navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
        foreach (EventPattern<NavHistoryDataEventArgs> item in items)
        {
            NavHistoryData data = item.EventArgs.Data;
            navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
        }

        using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
        {
            connection.Open();

            SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
            cmd.CommandType = CommandType.StoredProcedure;

            cmd.Parameters.Add(
                new SqlParameter()
                {
                    ParameterName = "@NavHistoryBatch",
                    SqlDbType = SqlDbType.Structured,
                    TypeName = "NavigationHistoryTableType",
                    Value = navHistoryBatch,
                });

            cmd.ExecuteNonQuery();
        }
    }
}
```

Bu arabelleğe alma sınıfını kullanmak için, uygulama bir statik Navıizdatamonitor nesnesi oluşturur. Bir Kullanıcı bir sayfaya her eriştiğinde, uygulama Navizdatamonitor. RecordUserNavigationEntry yöntemini çağırır. Arabelleğe alma mantığı, bu girişlerin veritabanına toplu olarak gönderilmesi için işlem gerçekleştirir.

### <a name="master-detail"></a>Ana ayrıntı

Tablo değerli parametreler basit ekleme senaryoları için faydalıdır. Ancak, birden fazla tablo içeren toplu ekleme eklemeleri daha zor olabilir. "Ana/ayrıntı" senaryosu iyi bir örnektir. Ana tablo, birincil varlığı tanımlar. Bir veya daha fazla ayrıntı tablosu varlık hakkında daha fazla veri depolar. Bu senaryoda, yabancı anahtar ilişkileri, Ayrıntılar için benzersiz bir ana varlıkla ilişki uygular. Bir PurchaseOrder tablosunun basitleştirilmiş bir sürümünü ve ilişkili OrderDetail tablosunu göz önünde bulundurun. Aşağıdaki Transact-SQL, dört sütunlu bir PurchaseOrder tablosu oluşturuyor: OrderID, OrderDate, CustomerID ve Status.

```sql
CREATE TABLE [dbo].[PurchaseOrder](
[OrderID] [int] IDENTITY(1,1) NOT NULL,
[OrderDate] [datetime] NOT NULL,
[CustomerID] [int] NOT NULL,
[Status] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_PurchaseOrder] 
PRIMARY KEY CLUSTERED ( [OrderID] ASC ))
```

Her sipariş bir veya daha fazla ürün satın alma içerir. Bu bilgiler PurchaseOrderDetail tablosunda yakalanır. Aşağıdaki Transact-SQL, beş sütunlu PurchaseOrderDetail tablosunu oluşturur: OrderID, Orderdetailıd, ProductID, UnitPrice ve Ordermik.

```sql
CREATE TABLE [dbo].[PurchaseOrderDetail](
[OrderID] [int] NOT NULL,
[OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
[ProductID] [int] NOT NULL,
[UnitPrice] [money] NULL,
[OrderQty] [smallint] NULL,
CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
( [OrderID] ASC, [OrderDetailID] ASC ))
```

PurchaseOrderDetail tablosundaki OrderID sütunu, PurchaseOrder tablosundan bir sıralama başvurmalıdır. Yabancı anahtarın aşağıdaki tanımı bu kısıtlamayı zorlar.

```sql
ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
REFERENCES [dbo].[PurchaseOrder] ([OrderID])
```

Tablo değerli parametreleri kullanabilmeniz için, her bir hedef tablo için Kullanıcı tanımlı bir tablo türüne sahip olmanız gerekir.

```sql
CREATE TYPE PurchaseOrderTableType AS TABLE 
( OrderID INT,
    OrderDate DATETIME,
    CustomerID INT,
    Status NVARCHAR(50) );
GO

CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
( OrderID INT,
    ProductID INT,
    UnitPrice MONEY,
    OrderQty SMALLINT );
GO
```

Sonra bu türlerin tablolarını kabul eden bir saklı yordam tanımlayın. Bu yordam, bir uygulamanın bir dizi siparişi ve siparişi ayrıntılarını tek bir çağrıda yerel olarak toplu olarak toplu olarak toplu olarak toplu olarak Aşağıdaki Transact-SQL, bu satın alma siparişi örneği için tam saklı yordam bildirimini sağlar.

```sql
CREATE PROCEDURE sp_InsertOrdersBatch (
@orders as PurchaseOrderTableType READONLY,
@details as PurchaseOrderDetailTableType READONLY )
AS
SET NOCOUNT ON;

-- Table that connects the order identifiers in the @orders
-- table with the actual order identifiers in the PurchaseOrder table
DECLARE @IdentityLink AS TABLE ( 
SubmittedKey int, 
ActualKey int, 
RowNumber int identity(1,1)
);

-- Add new orders to the PurchaseOrder table, storing the actual
-- order identifiers in the @IdentityLink table   
INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;

-- Match the passed-in order identifiers with the actual identifiers
-- and complete the @IdentityLink table for use with inserting the details
WITH OrderedRows As (
SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
FROM @orders
)
UPDATE @IdentityLink SET SubmittedKey = M.OrderID
FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;

-- Insert the order details into the PurchaseOrderDetail table, 
-- using the actual order identifiers of the master table, PurchaseOrder
INSERT INTO PurchaseOrderDetail (
[OrderID],
[ProductID],
[UnitPrice],
[OrderQty] )
SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
FROM @details D
JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
GO
```

Bu örnekte, yerel olarak tanımlanan @IdentityLink tablosu, yeni yerleştirilen satırlardan gerçek OrderID değerlerini depolar. Bu sıra tanımlayıcıları, @orders ve tablo değerli parametrelerde @details geçici SiparişNo değerlerinden farklıdır. Bu nedenle @IdentityLink tablosu, @orders parametresindeki OrderID değerlerini PurchaseOrder tablosundaki yeni satırların gerçek OrderID değerlerine bağlar. Bu adımdan sonra, @IdentityLink tablosu, yabancı anahtar kısıtlamasına uyan gerçek OrderID ile sipariş ayrıntılarının eklenmesi işlemini kolaylaştırabilir.

Bu saklı yordam, koddan veya diğer Transact-SQL çağrılarından kullanılabilir. Kod örneği için bu kağıdın tablo değerli parametreler bölümüne bakın. Aşağıdaki Transact-SQL sp_InsertOrdersBatch nasıl çağrılacağını gösterir.

```sql
declare @orders as PurchaseOrderTableType
declare @details as PurchaseOrderDetailTableType

INSERT @orders 
([OrderID], [OrderDate], [CustomerID], [Status])
VALUES(1, '1/1/2013', 1125, 'Complete'),
(2, '1/13/2013', 348, 'Processing'),
(3, '1/12/2013', 2504, 'Shipped')

INSERT @details
([OrderID], [ProductID], [UnitPrice], [OrderQty])
VALUES(1, 10, $11.50, 1),
(1, 12, $1.58, 1),
(2, 23, $2.57, 2),
(3, 4, $10.00, 1)

exec sp_InsertOrdersBatch @orders, @details
```

Bu çözüm, her toplu işin 1 ile başlayan bir OrderID değeri kümesini kullanmasına izin verir. Bu geçici OrderID değerleri toplu işteki ilişkileri tanımlar, ancak gerçek OrderID değerleri ekleme işlemi sırasında belirlenir. Önceki örnekteki aynı deyimleri tekrar tekrar çalıştırabilirsiniz ve veritabanında benzersiz siparişler oluşturabilirsiniz. Bu nedenle, bu toplu işlem tekniğinin kullanıldığı sırada yinelenen siparişlerin önlediği daha fazla kod veya veritabanı mantığı eklemeyi göz önünde bulundurun.

Bu örnek, ana ayrıntı işlemleri gibi daha karmaşık veritabanı işlemlerinin tablo değerli parametreler kullanılarak toplu olarak kullanılabileceğini gösterir.

### <a name="upsert"></a>UPSERT

Başka bir toplu işlem senaryosu aynı anda mevcut satırları güncellemeyi ve yeni satırlar eklemeyi içerir. Bu işlem bazen "UPSERT" (güncelleştirme + ekleme) işlemi olarak adlandırılır. INSERT ve UPDATE için ayrı çağrılar yapmak yerine MERGE deyimleri bu göreve en uygun seçenektir. MERGE deyimleri tek bir çağrıda hem INSERT hem de Update işlemleri gerçekleştirebilir.

Tablo değerli parametreler, güncelleştirme ve ekleme işlemleri için MERGE ifadesiyle birlikte kullanılabilir. Örneğin, şu sütunları içeren basitleştirilmiş bir çalışan tablosu düşünün: EmployeeID, FirstName, LastName, SocialSecurityNumber:

```sql
CREATE TABLE [dbo].[Employee](
[EmployeeID] [int] IDENTITY(1,1) NOT NULL,
[FirstName] [nvarchar](50) NOT NULL,
[LastName] [nvarchar](50) NOT NULL,
[SocialSecurityNumber] [nvarchar](50) NOT NULL,
CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
([EmployeeID] ASC ))
```

Bu örnekte, SocialSecurityNumber ' ın birden çok çalışanın BIRLEŞTIRME işlemini gerçekleştirmek için benzersiz olduğu gerçeğinden yararlanabilirsiniz. İlk olarak, Kullanıcı tanımlı tablo türünü oluşturun:

```sql
CREATE TYPE EmployeeTableType AS TABLE 
( Employee_ID INT,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    SocialSecurityNumber NVARCHAR(50) );
GO
```

Sonra, bir saklı yordam oluşturun veya Update ve INSERT işlemini gerçekleştirmek için MERGE ifadesini kullanan kodu yazın. Aşağıdaki örnek, EmployeeTableType türünde @employeestablo değerli bir parametre üzerinde MERGE ifadesini kullanır. @employees tablosunun içerikleri burada gösterilmez.

```sql
MERGE Employee AS target
USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
AS source ([FirstName], [LastName], [SocialSecurityNumber])
ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
WHEN MATCHED THEN 
UPDATE SET
target.FirstName = source.FirstName, 
target.LastName = source.LastName
WHEN NOT MATCHED THEN
    INSERT ([FirstName], [LastName], [SocialSecurityNumber])
    VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);
```

Daha fazla bilgi için MERGE deyimin belgelerine ve örneklerine bakın. Aynı iş, ayrı ekleme ve GÜNCELLEŞTIRME işlemleriyle birden çok adımlı bir saklı yordam çağrısında gerçekleştirilebileceği halde MERGE deyimleri daha etkilidir. Veritabanı kodu, INSERT ve UPDATE için iki veritabanı çağrısı gerektirmeden doğrudan MERGE ifadesini kullanan Transact-SQL çağrıları da oluşturabilir.

## <a name="recommendation-summary"></a>Öneri Özeti

Aşağıdaki listede, bu makalede ele alınan toplu işlem önerilerinin özeti verilmiştir:

* SQL veritabanı uygulamalarının performansını ve ölçeklenebilirliğini artırmak için arabelleğe alma ve toplu işlem kullanma.
* Toplu işlem/arabelleğe alma ve dayanıklılık arasındaki avantajları anlayın. Bir rol hatası sırasında, işlenmemiş iş açısından kritik verileri kaybetme riski, toplu işlemin performans avantajını engelleyebilir.
* Gecikme süresini azaltmak için tek bir veri merkezi içindeki veritabanına yapılan tüm çağrıları tutmayı deneyin.
* Tek bir toplu işleme tekniği seçerseniz tablo değerli parametreler en iyi performansı ve esnekliği sunar.
* En hızlı ekleme performansı için aşağıdaki genel yönergeleri izleyin, ancak senaryonuzu test edin:
  * < 100 satırları için tek parametreli bir INSERT komutu kullanın.
  * < 1000 satırları için tablo değerli parametreleri kullanın.
  * > = 1000 satırları için SqlBulkCopy kullanın.
* Güncelleştirme ve silme işlemleri için tablo parametresi içindeki her satırda doğru işlemi belirleyen saklı yordam mantığı ile tablo değerli parametreleri kullanın.
* Toplu işlem boyut yönergeleri:
  * Uygulamanız ve iş gereksinimleriniz için anlamlı olan en büyük toplu iş boyutlarını kullanın.
  * Büyük toplu işlerin performans kazancını, geçici veya çok önemli arızaların riskleri ile dengeleyin. İşlemdeki verilerin ne kadar yeniden denenmesi veya kaybedilmesi ne olur? 
  * SQL veritabanının bu dosyayı reddetmediğinden emin olmak için en büyük toplu iş boyutunu test edin.
  * Toplu iş boyutu veya arabelleğe alma zaman penceresi gibi toplu işlemeyi denetleyen yapılandırma ayarları oluşturun. Bu ayarlar esneklik sağlar. Üretimde toplu işlem davranışını bulut hizmetini yeniden dağıtmaya gerek kalmadan değiştirebilirsiniz.
* Tek bir veritabanında tek bir tabloda çalışan toplu işlerin paralel yürütmesini önleyin. Tek bir toplu işi birden çok çalışan iş parçacığı arasında bölmek istiyorsanız, ideal iş parçacığı sayısını belirlemek için testler çalıştırın. Belirtilmeyen eşikten sonra, daha fazla iş parçacığı, performansı artırmak yerine performansı düşürür.
* Daha fazla senaryo için toplu işleme uygulama yöntemi olarak boyut ve zaman arabelleğini göz önünde bulundurun.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, toplu işleme ile ilgili veritabanı tasarımı ve kodlama teknikleri, uygulama performansınızı ve ölçeklenebilirliğini nasıl iyileştirebileceğinizi yoğunlaşmaktadır. Ancak bu, genel stratejinizde yalnızca bir faktördür. Performansı ve ölçeklenebilirliği artırmanın daha fazla yolu için bkz. [tek veritabanları Için Azure SQL veritabanı performans Kılavuzu](sql-database-performance-guidance.md) ve [elastik havuz için fiyat ve performans konuları](sql-database-elastic-pool-guidance.md).

