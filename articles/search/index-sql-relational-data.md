---
title: İçeri ve dizin oluşturma için SQL ilişkisel verilerini modelleyin
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de dizin oluşturma ve tam metin araması için ilişkisel verileri, sabit bir sonuç kümesine nasıl modelleyeceğinizi öğrenin.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790089"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Azure Bilişsel Arama içeri ve dışarı dizinlemeye yönelik ilişkisel SQL verilerini modelleme

Azure Bilişsel Arama, [Dizin oluşturma ardışık düzenine](search-what-is-an-index.md)giriş olarak düz bir satır kümesini kabul eder. Kaynak verileriniz SQL Server ilişkisel veritabanındaki birleştirilmiş tablolardan kaynaklanıyorsa, bu makalede sonuç kümesinin nasıl oluşturulacağı ve bir Azure Bilişsel Arama dizininde üst-alt ilişkisinin nasıl modelleneceği açıklanmaktadır.

Bir çizim olarak, [tanıtım verilerine](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)bağlı olarak bir kuramsal oteller veritabanına başvuracağız. Veritabanı, 50 oteller içeren bir oteller $ tablosundan ve toplam 750 Oda için değişen türler, oranlar ve değişiklik odaları içeren bir oda $ tablosu olduğunu varsayalım. Tablolar arasında bire çok ilişki vardır. Yaklaşımda bir görünüm, her bir satıra eklenen ilişkili Oda ayrıntısı ile, her otel için bir satır olmak üzere 50 satırı döndüren sorguyu sağlar.

   ![Oteller veritabanındaki tablolar ve görünüm](media/index-sql-relational-data/hotels-database-tables-view.png "Oteller veritabanındaki tablolar ve görünüm")


## <a name="the-problem-of-denormalized-data"></a>Yoğun verilerin sorunu

Bire çok ilişki ile çalışma konusundaki güçlüklerden biri, birleştirilmiş tablolar üzerinde oluşturulan standart sorguların, Azure Bilişsel Arama senaryosunda iyi çalışmayan çok fazla veri döndürmesini sağlar. Oteller ve odaları birleştiren aşağıdaki örneği göz önünde bulundurun.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Bu sorgunun sonuçları, tüm otel alanlarını ve ardından tüm oda alanlarını döndürür ve her oda değeri için ön otel bilgileri yinelenir.

   ![Sallaştırlanmış veriler, Oda alanları eklendiğinde yedekli otel verileri](media/index-sql-relational-data/denormalize-data-query.png "Sallaştırlanmış veriler, Oda alanları eklendiğinde yedekli otel verileri")


Bu sorgu, yüzeyde başarılı olsa da, beklenen arama deneyimi için doğru belge yapısını sunarken başarısız olur. Azure Bilişsel Arama, dizin oluşturma işlemi sırasında her satır için bir arama belgesi oluşturur. Arama belgeleriniz yukarıdaki sonuçlara benziyorsa, Ikizi Dome otelinin tek başına yedi ayrı belge olduğunu algılanan yinelemelerinizi görürsünüz. "Florida 'daki oteller" üzerinde bir sorgu, yalnızca Ikizi Dome otelinin yedi sonucunu döndürür ve diğer ilgili oteller, arama sonuçlarına göre daha derin bir hale döndürülür.

Her otel için bir belgenin beklenen deneyimini almak üzere, doğru ayrıntı düzeyinde bir satır kümesi sağlamanız gerekir, ancak bilgilerin tamamını görürsünüz. Neyse ki, bu makaledeki teknikleri benimseerek bunu kolayca yapabilirsiniz.

## <a name="define-a-query-that-returns-embedded-json"></a>Gömülü JSON döndüren bir sorgu tanımlayın

Beklenen arama deneyimini sunmak için, veri kümesi Azure Bilişsel Arama 'daki her arama belgesi için bir satırdan oluşmalıdır. Bizim örneğimizde, her otel için bir satır istiyoruz, ancak biz de kullanıcılarımızın gecelik oranı, boyut ve sayı ya da bir oda ayrıntısı olan bir plaj görünümü gibi, bu konuyla ilgili olarak ilgilendikleri diğer alanlara arama yapabilmesini istiyoruz.

Çözüm, iç içe geçmiş JSON olarak Oda Ayrıntısı yakalamak ve ikinci adımda gösterildiği gibi, bir görünümdeki alana JSON yapısını eklemedir. 

1. 50 oteller ve 750 odalarının ayrıntılarını içeren ve Hotelıd alanına katılmış iki birleştirilmiş tablonuz olan oteller $ ve odalar $ olduğunu varsayalım. Tek tek, bu tablolar 50 oteller ve 750 ile ilgili odalar içerir.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. İç tablodaki tüm alanlardan oluşan bir görünüm oluşturun (`SELECT * from dbo.Hotels$`), iç içe bir sorgunun çıkışını içeren yeni *Odalar* alanının eklenmesiyle birlikte. `SELECT * from dbo.Rooms$` **için bir for JSON Auto** yan tümcesi, çıktıyı JSON olarak yapılandırır. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Aşağıdaki ekran görüntüsünde, en altta yer alan *Odalar* nvarchar alanı ile elde edilen görünüm gösterilmektedir. *Odalar* alanı yalnızca Hoteloda görünümünde bulunur.

   ![Hoteloda görünümü](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms görünümü")

1. Satır kümesini almak için `SELECT * FROM dbo.HotelRooms` çalıştırın. Bu sorgu, her otel için bir tane olmak üzere, bir JSON koleksiyonu olarak ilişkili Oda bilgileriyle 50 satırı döndürür. 

   ![Hoteloda görünümündeki satır kümesi](media/index-sql-relational-data/hotelrooms-rowset.png "Hoteloda görünümündeki satır kümesi")

Bu satır kümesi artık Azure Bilişsel Arama 'ye aktarmaya hazırdır.

> [!NOTE]
> Bu yaklaşım gömülü JSON 'ın [SQL Server maksimum sütun boyutu limitlerinin](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server)altında olduğunu varsayar. Verileriniz sığmıyorsa, [örnek: Azure bilişsel arama AdventureWorks Inventory Database model](search-example-adventureworks-modeling.md)oluşturma gibi bir programlama yaklaşımı deneyebilirsiniz.

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Bire çok ilişkinin "çok" tarafı için karmaşık bir koleksiyon kullanın

Azure Bilişsel Arama tarafında, iç içe geçmiş JSON kullanarak bire çok ilişkiyi modelleyen bir dizin şeması oluşturun. Önceki bölümde oluşturduğunuz sonuç kümesi, genellikle aşağıda belirtilen dizin şemasına karşılık gelir (bazı alanları kısaltma için kestik).

Aşağıdaki örnek, [karmaşık veri türlerini Modelme](search-howto-complex-data-types.md#creating-complex-fields)içindeki örneğe benzerdir. Bu makalenin konusu olan *Odalar* yapısı, *oteller*adlı bir dizinin alanlar koleksiyonudur. Bu örnek ayrıca, bir koleksiyonda izin verilen çok sayıda öğenin aksine, sabit bir öğe kümesinden oluşan *odalardan* farklı olan *Adres*için karmaşık bir tür gösterir.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Önceki sonuç kümesi ve yukarıdaki Dizin şeması verildiğinde, başarılı bir dizin oluşturma işlemi için gerekli tüm bileşenlere sahipsiniz. Düzleştirilmiş veri kümesi dizin oluşturma gereksinimlerini karşılıyor ancak ayrıntı bilgilerini korur. Azure Bilişsel Arama dizininde, arama sonuçları, bireysel odalar ve onların özniteliklerinin bağlamını korurken, otel tabanlı varlıklara kolayca dönecektir.

## <a name="next-steps"></a>Sonraki adımlar

Kendi veri kümesini kullanarak, dizin oluşturmak ve yüklemek için [veri alma Sihirbazı 'nı](search-import-data-portal.md) kullanabilirsiniz. Sihirbaz, *odalarda*içerilen gıbı gömülü JSON koleksiyonunu algılar ve karmaşık bir tür koleksiyonu içeren bir dizin şemasını bulur. 

  ![Verileri Içeri aktarma Sihirbazı tarafından gösterilen Dizin](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Verileri Içeri aktarma Sihirbazı tarafından gösterilen Dizin")

Veri alma sihirbazının temel adımlarını öğrenmek için aşağıdaki hızlı başlangıcı deneyin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure portal kullanarak arama dizini oluşturma](search-get-started-portal.md)