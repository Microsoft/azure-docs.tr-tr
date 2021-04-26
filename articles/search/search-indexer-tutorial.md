---
title: C# öğreticisi Azure SQL verilerini dizine alma
titleSuffix: Azure Cognitive Search
description: Bu C# öğreticisinde Azure SQL veritabanı 'na bağlanın, aranabilir verileri ayıklayın ve Azure Bilişsel Arama dizinine yükleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: e2ca5f42120661b887d07e697596f41cb7a7fce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745775"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Öğretici: .NET SDK kullanarak Azure SQL verilerinin dizinini oluştur

Azure Bilişsel Arama 'te arama dizinine göndererek Azure SQL veritabanı 'ndan aranabilir verileri ayıklamak için bir [Dizin Oluşturucu](search-indexer-overview.md) yapılandırın. 

Bu öğretici aşağıdaki görevleri gerçekleştirmek için C# ve [.NET SDK](/dotnet/api/overview/azure/search) kullanır:

> [!div class="checklist"]
> * Azure SQL veritabanı 'na bağlanan bir veri kaynağı oluşturma
> * Dizin oluşturucu oluşturma
> * Dizine veri yüklemek için Dizin Oluşturucu çalıştırma
> * Bir dizini doğrulama adımı olarak sorgulama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

+ [Azure SQL Veritabanı](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Mevcut bir arama hizmeti](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [oluşturun](search-create-service-portal.md) veya bulun 

> [!Note]
> Bu öğretici için ücretsiz hizmeti kullanabilirsiniz. Ücretsiz arama hizmeti, sizi üç Dizin, üç Dizin Oluşturucu ve üç veri kaynağı ile sınırlandırır. Bu öğreticide hepsinden birer tane oluşturulur. Başlamadan önce, hizmetinize yeni kaynakları kabul etmek için yeriniz olduğundan emin olun.

## <a name="download-files"></a>Dosyaları indirme

Bu öğreticinin kaynak kodu, [Azure-Samples/Search-DotNet-Başlarken](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub deposundaki [Dotnethowtoındexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) klasöründedir.

## <a name="1---create-services"></a>1-hizmet oluşturma

Bu öğretici, dizin oluşturma ve sorgular için Azure Bilişsel Arama ve dış veri kaynağı olarak Azure SQL veritabanı 'nı kullanır. Mümkünse, yakınlık ve yönetilebilirlik için aynı bölgede ve kaynak grubunda her iki hizmeti de oluşturun. Uygulamada, Azure SQL veritabanı herhangi bir bölgede olabilir.

### <a name="start-with-azure-sql-database"></a>Azure SQL veritabanı 'nı kullanmaya başlama

Bu adımda, bir dizin oluşturucunun gezinebileceği Azure SQL veritabanı 'nda bir dış veri kaynağı oluşturun. Azure SQL veritabanı 'nda veri kümesini oluşturmak için örnek indirBir Azure portal ve *oteller. SQL* dosyasını kullanabilirsiniz. Azure Bilişsel Arama, bir görünümden veya sorgudan oluşturulan bir gibi düzleştirilmiş satır kümeleri kullanır. Örnek çözümdeki SQL dosyası tek bir tablo oluşturur ve doldurur.

Mevcut bir Azure SQL veritabanı kaynağınız varsa, bu, 4. adımdan başlayarak bu tabloya oteller tablosunu ekleyebilirsiniz.

1. [Azure Portal oturum açın](https://portal.azure.com/).

1. Bir **SQL veritabanı** bulun veya oluşturun. Varsayılan değerleri ve en düşük düzey fiyatlandırma katmanını kullanabilirsiniz. Sunucu oluşturmanın bir avantajı, yönetici kullanıcı adı ve parolası belirtebilmenizdir; çünkü sonraki adımda tabloları oluşturmak ve yüklemek için bunlar gerekecektir.

   :::image type="content" source="media/search-indexer-tutorial/indexer-new-sqldb.png" alt-text="Yeni veritabanı sayfası" border="false":::

1. Yeni sunucuyu ve veritabanını dağıtmak için **gözden geçir + oluştur** ' a tıklayın. Sunucu ve veritabanının dağıtılmasını bekleyin.

1. Gezinti bölmesinde **sorgu Düzenleyicisi (Önizleme)** öğesine tıklayın ve sunucu yöneticisinin Kullanıcı adını ve parolasını girin. 

   Erişim reddedilirse, istemci IP adresini hata iletisinden kopyalayın ve ardından, istemci bilgisayarınızdan aralığa erişim sağlayan bir kural eklemek için **sunucu güvenlik duvarı ayarla** bağlantısına tıklayın. Kuralın etkili olması birkaç dakika sürebilir.

1. Sorgu Düzenleyicisi 'nde **sorgu aç** ' a tıklayın ve yerel bilgisayarınızdaki *oteller. SQL* dosyasının konumuna gidin. 

1. Dosyayı seçin ve **Aç**'a tıklayın. Betik aşağıdaki ekran görüntüsüne benzer görünmelidir:

   :::image type="content" source="media/search-indexer-tutorial/sql-script.png" alt-text="SQL betiği" border="false":::

1. Sorguyu yürütmek için **Çalıştır**'a tıklayın. Sonuçlar bölmesinde, 3 satır için sorgu başarılı oldu iletisini görmeniz gerekir.

1. Bu tablodan bir satır kümesi döndürmek için, doğrulama adımı olarak aşağıdaki sorguyu yürütebilirsiniz:

    ```sql
    SELECT * FROM Hotels
    ```

1. Veritabanı için ADO.NET bağlantı dizesini kopyalayın. **Ayarlar**  >  **bağlantı dizeleri** altında, aşağıdaki örneğe benzer şekilde ADO.NET bağlantı dizesini kopyalayın.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Bu bağlantı dizesine, ortamınızı ayarlarken bir sonraki alıştırmada ihtiyacınız olacak.

### <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

Sonraki bileşen, [portalda oluşturabileceğiniz](search-create-service-portal.md)Azure bilişsel arama. Bu izlenecek yolu tamamlamak için ücretsiz katmanı kullanabilirsiniz. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Bilişsel Arama yönelik bir yönetici API anahtarı ve URL 'SI alın

API çağrıları, hizmet URL 'SI ve erişim anahtarı gerektirir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar**  >  **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP uç noktası ve erişim anahtarı al" border="false":::

## <a name="2---set-up-your-environment"></a>2-ortamınızı ayarlama

1. Visual Studio 'Yu başlatın ve **Dotnethowtoındexers. sln**'yi açın.

1. Çözüm Gezgini, bağlantı bilgilerini sağlamak için **appsettings.js** açın.

1. İçin `SearchServiceEndPoint` , hizmete Genel Bakış sayfasındaki tam URL " https://my-demo-service.search.windows.net " ise, sağlanacak değer URL 'dir.

1. İçin `AzureSqlConnectionString` dize biçimi şuna benzerdir: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceEndPoint": "<placeholder-search-url>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-search-service>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Bağlantı dizesinde bağlantı dizesinin geçerli bir parola içerdiğinden emin olun. Veritabanı ve Kullanıcı adları üzerine kopyalanacak olsa da, parolanın el ile girilmesi gerekir.

## <a name="3---create-the-pipeline"></a>3-işlem hattını oluşturma

Dizin oluşturucular bir veri kaynağı nesnesi ve bir dizin gerektirir. İlgili kod iki dosyada bulunur:

  + dizini tanımlayan bir şema içeren **otel. cs**
  + Hizmetinizdeki yapıları oluşturma ve yönetme işlevlerini içeren **program. cs**

### <a name="in-hotelcs"></a>Hotel.cs dosyasında

Dizin şeması, aşağıdaki HotelName alan tanımında gösterildiği gibi alanın tam metin araması yapılabilir, filtrelenebilir veya sıralanabilir olup olmadığı gibi izin verilen işlemleri belirten öznitelikler de dahil olmak üzere alan koleksiyonunu tanımlar. Bir [Searchablefield](/dotnet/api/azure.search.documents.indexes.models.searchablefield) , tanım ile aranabilir tam metin. Diğer öznitelikler açıkça atanır.

```csharp
. . . 
[SearchableField(IsFilterable = true, IsSortable = true)]
[JsonPropertyName("hotelName")]
public string HotelName { get; set; }
. . .
```

Şema başka öğeler, örneğin arama puanını artırmak için puanlama profilleri, özel çözümleyiciler ve başka yapılar içerebilir. Öte yandan, bizim amaçlarımıza uygun olarak şema yalnızca örnek veri kümelerinde bulunan alanlarla seyrek bir şekilde tanımlanmıştır.

### <a name="in-programcs"></a>Program.cs dosyasında

Ana program dizin [Oluşturucu istemci](/dotnet/api/azure.search.documents.indexes.models.searchindexer), dizin, veri kaynağı ve Dizin Oluşturucu oluşturma mantığını içerir. Kod, bu programı birden çok kez çalıştırabileceğiniz varsayımıyla, aynı adı taşıyan kaynakları denetler ve siler.

Veri kaynağı nesnesi, Azure SQL 'in yerleşik [değişiklik algılama özelliklerinden](/sql/relational-databases/track-changes/about-change-tracking-sql-server) yararlanmak için [kısmi veya artımlı dizin oluşturma](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) da dahIl olmak üzere Azure SQL veritabanı kaynaklarına özgü ayarlarla yapılandırılır. Azure SQL 'deki kaynak tanıtım oteller veritabanının **IsDeleted** adlı bir "geçici silme" sütunu vardır. Bu sütun veritabanında true olarak ayarlandığında, Dizin Oluşturucu ilgili belgeyi Azure Bilişsel Arama dizininden kaldırır.

```csharp
Console.WriteLine("Creating data source...");

var dataSource =
      new SearchIndexerDataSourceConnection(
         "hotels-sql-ds",
         SearchIndexerDataSourceType.AzureSql,
         configuration["AzureSQLConnectionString"],
         new SearchIndexerDataContainer("hotels"));

indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
```

Dizin Oluşturucu nesnesi, yapılandırma, zamanlama ve çağrının kaynağa bakılmaksızın aynı olduğu platformdan bağımsız bir platformdur. Bu örnek Dizin Oluşturucu bir zamanlama, Dizin Oluşturucu geçmişini temizleyen bir sıfırlama seçeneği içerir ve Dizin oluşturucuyu hemen oluşturmak ve çalıştırmak için bir yöntem çağırır. Bir dizin oluşturucu oluşturmak veya güncelleştirmek için [Createorupdateındexsilinebilir Sync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.createorupdateindexerasync)kullanın.

```csharp
Console.WriteLine("Creating Azure SQL indexer...");

var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
      StartTime = DateTimeOffset.Now
};

var parameters = new IndexingParameters()
{
      BatchSize = 100,
      MaxFailedItems = 0,
      MaxFailedItemsPerBatch = 0
};

// Indexer declarations require a data source and search index.
// Common optional properties include a schedule, parameters, and field mappings
// The field mappings below are redundant due to how the Hotel class is defined, but 
// we included them anyway to show the syntax 
var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
      Description = "Data indexer",
      Schedule = schedule,
      Parameters = parameters,
      FieldMappings =
      {
         new FieldMapping("_id") {TargetFieldName = "HotelId"},
         new FieldMapping("Amenities") {TargetFieldName = "Tags"}
      }
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

Dizin Oluşturucu çalıştırmaları genellikle zamanlanır, ancak geliştirme sırasında [Runındexsilinebilir eşitlemesini](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync)kullanarak Dizin oluşturucuyu hemen çalıştırmak isteyebilirsiniz.

```csharp
Console.WriteLine("Running Azure SQL indexer...");

try
{
      await indexerClient.RunIndexerAsync(indexer.Name);
}
catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
{
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
}
```

## <a name="4---build-the-solution"></a>4-çözümü oluşturma

Çözümü derlemek ve çalıştırmak için F5 tuşuna basın. Program hata ayıklama modunda yürütülür. Konsol penceresinde her işlemin durumu bildirilir.

   :::image type="content" source="media/search-indexer-tutorial/console-output.png" alt-text="Konsol çıktısı" border="false":::

Kodunuz Visual Studio 'da yerel olarak çalışarak Azure 'da arama hizmetinize bağlanarak Azure SQL veritabanına bağlanır ve veri kümesini alır. Bu çok işlem sayesinde birkaç olası hata noktası vardır. Bir hata alırsanız, önce aşağıdaki koşulları kontrol edin:

+ Sağladığınız arama hizmeti bağlantı bilgileri tam URL 'dir. Yalnızca hizmet adını girdiyseniz, işlemler dizin oluşturma sırasında durdurulur ve hata bağlantısı başarısız olacaktır.

+ **Appsettings.json** dosyasındaki veritabanı bağlantı bilgileri. Bu, portaldan alınmış ve veritabanınız için geçerli kullanıcı adı ve parolayı içerecek şekilde değiştirilmiş ADO.NET bağlantı dizesi olmalıdır. Kullanıcı hesabının verileri alma izni olmalıdır. Yerel istemci IP adresiniz güvenlik duvarı üzerinden gelen erişime izin verilmelidir.

+ Kaynak sınırları. Ücretsiz katmanın 3 dizin, Dizin Oluşturucu ve veri kaynağı sınırlarına sahip olduğunu hatırlayın. Sınırına dayanmış bir hizmet yeni nesneler oluşturamaz.

## <a name="5---search"></a>5-arama

Nesne oluşturmayı doğrulamak için Azure portal kullanın ve ardından dizini sorgulamak için **Arama Gezgini** 'ni kullanın.

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında, nesnenin oluşturulduğunu doğrulamak için her listeyi sırayla açın. **Dizinler**, **Dizin oluşturucular** ve **veri kaynakları** sırasıyla "oteller", "Azure-SQL-Indexer" ve "Azure-SQL" olacaktır.

   :::image type="content" source="media/search-indexer-tutorial/tiles-portal.png" alt-text="Dizin oluşturucu ve veri kaynağı kutucukları" border="false":::

1. Oteller dizinini seçin. Oteller sayfasında, **Arama Gezgini** ilk sekmedir. 

1. Boş bir sorgu vermek için **Ara** ' ya tıklayın. 

   Dizininizdeki üç girdi, JSON belgeleri olarak döndürülür. Yapının tamamını görebilmeniz için arama gezgini belgeleri JSON olarak döndürür.

   :::image type="content" source="media/search-indexer-tutorial/portal-search.png" alt-text="Dizin sorgulama" border="false":::
   
1. Ardından, bir arama dizesi girin: `search=river&$count=true`. 

   Bu sorgu, `river` terimi için tam metin aramasını çağırır ve sonuç eşleşen belgelerin sayısını içerir. Binlerce veya milyonlarca belge içeren çok büyük bir dizininiz olduğunda, test senaryolarında eşleşen belge sayısının döndürülmesi yararlı olur. Bizim örneğimizde, sorguyla eşleşen tek bir belge vardır.

1. Son olarak, JSON çıkışını ilgilendiğiniz alanlarla sınırlandıran bir arama dizesi girin: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Sorgu yanıtı seçili alanlara daraltılır ve sonuçta daha kısa bir çıkış elde edilir.

## <a name="reset-and-rerun"></a>Sıfırlama ve yeniden çalıştırma

Geliştirmede erken deneysel aşamalarda, tasarım yinelemesi için en pratik yaklaşım, nesneleri Azure Bilişsel Arama silmek ve kodunuzun bunları yeniden oluşturması için izin verir. Kaynak adları benzersizdir. Bir nesneyi sildiğinizde, aynı adı kullanarak nesneyi yeniden oluşturabilirsiniz.

Bu öğreticinin örnek kodu mevcut nesneleri denetler ve kodunuzu yeniden çalıştırabilmeniz için onları siler.

Ayrıca, dizinleri, Dizin oluşturucuyu ve veri kaynaklarını silmek için portalını de kullanabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir projenin sonunda kendi aboneliğinizde çalışırken, artık ihtiyaç duyulmadığınızda kaynakları kaldırmak iyi bir fikirdir. Çalışır durumda bırakılan kaynaklar maliyetlerin artmasına neden olabilir. Kaynakları teker teker silebilir veya tüm kaynak grubunu silerek kaynak kümesinin tamamını kaldırabilirsiniz.

Sol gezinti bölmesindeki tüm kaynaklar veya kaynak grupları bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Artık SQL veritabanı dizin oluşturma hakkında bilgi sahibi olduğunuza göre, Dizin Oluşturucu yapılandırmasına daha yakından bakalım.

> [!div class="nextstepaction"]
> [SQL veritabanı dizin oluşturucuyu yapılandırma](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)