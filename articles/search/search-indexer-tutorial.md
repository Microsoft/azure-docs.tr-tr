---
title: 'C#Öğretici: Azure SQL veritabanlarındaki verileri diz'
titleSuffix: Azure Cognitive Search
description: C#Azure SQL veritabanı 'na bağlanmayı, aranabilir verileri ayıklamayı ve Azure Bilişsel Arama dizinine yüklemeyi gösteren kod örneği.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 4e8097eeb07420bee4ba30eb0fedbe5d4db2db9d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113316"
---
# <a name="c-tutorial-import-azure-sql-database-using-azure-cognitive-search-indexers"></a>C#Öğretici: Azure Bilişsel Arama Dizinleyicileri kullanarak Azure SQL veritabanını Içeri aktarma

Örnek bir Azure SQL veritabanından aranabilir verileri ayıklamak için dizin oluşturucunun nasıl yapılandırılacağını öğrenin. [Dizin oluşturucular](search-indexer-overview.md) , dış veri kaynaklarını gösteren ve bir [Arama dizinini](search-what-is-an-index.md) içerikle dolduran Azure bilişsel arama bileşenidir. Tüm Dizin oluşturucular, Azure SQL veritabanı için Dizin Oluşturucu en yaygın kullanılan veritabanıdır. 

Dizin oluşturucu yapılandırmasında yeterlilik kazanmak yararlı olur çünkü yazmanız ve korumanız gereken kod miktarını azaltır. Şemayla uyumlu bir JSON veri kümesi hazırlayıp göndermek yerine, veri kaynağına bir dizin oluşturucu ekleyebilir, bu dizin oluşturucunun verileri ayıklamasını ve dizine eklemesini sağlayabilir ve isteğe bağlı olarak temel kaynaktaki değişiklikleri almak için dizin oluşturucuyu yinelenen bir zamanlamada çalıştırabilirsiniz.

Bu öğreticide, aşağıdaki görevleri gerçekleştirmek için [Azure bilişsel arama .NET istemci kitaplıklarını](https://aka.ms/search-sdk) ve bir .NET Core konsol uygulamasını kullanın:

> [!div class="checklist"]
> * Uygulama ayarlarına arama hizmeti bilgilerini ekleme
> * Azure SQL veritabanında bir dış veri kümesi hazırlama 
> * Örnek kodda dizinin ve dizin oluşturucunun tanımlarını gözden geçirme
> * Dizin oluşturucu kodunu çalıştırarak verileri içeri aktarma
> * Dizinde arama yapma
> * Portalda dizin oluşturucu yapılandırmasını görüntüleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki hizmetler, Araçlar ve veriler bu hızlı başlangıçta kullanılır. 

Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu öğretici için ücretsiz bir hizmet kullanabilirsiniz.

[Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) , bir Dizin Oluşturucu tarafından kullanılan dış veri kaynağını depolar. Örnek çözümde, tablo oluşturmak için bir SQL veri dosyası sağlanır. Hizmeti ve veritabanını oluşturma adımları Bu öğreticide sunulmaktadır.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), herhangi bir sürüm, örnek çözümü çalıştırmak için kullanılabilir. Örnek kod ve yönergeler ücretsiz topluluk sürümünde sınanmıştır.

[Azure-Samples/Search-DotNet-Başlarken](https://github.com/Azure-Samples/search-dotnet-getting-started) , Azure örnekleri GitHub deposunda bulunan örnek çözümü sağlar. Çözümü indirip ayıklayın. Varsayılan olarak, çözümler salt okunurdur. Çözüme sağ tıklayın ve dosyaları değiştirebilmeniz için salt okunurdur özniteliğini temizleyin.

> [!Note]
> Ücretsiz Azure Bilişsel Arama hizmeti kullanıyorsanız, üç Dizin, üç Dizin Oluşturucu ve üç veri kaynağı ile sınırlı olursunuz. Bu öğreticide hepsinden birer tane oluşturulur. Hizmetinizde yeni kaynakları kabul edecek kadar yer olduğundan emin olun.

## <a name="get-a-key-and-url"></a>Anahtar ve URL al

REST çağrıları için her istekte hizmet URL'sinin ve bir erişim anahtarının iletilmesi gerekir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetine **genel bakış** sayfasında URL 'yi alın. Örnek uç nokta `https://mydemo.search.windows.net` şeklinde görünebilir.

1. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

![HTTP uç noktası ve erişim anahtarı al](media/search-get-started-postman/get-url-key.png "HTTP uç noktası ve erişim anahtarı al")

Tüm istekler hizmetinize gönderilen her istekte bir API anahtarı gerektirir. İstek başına geçerli bir anahtara sahip olmak, isteği gönderen uygulama ve bunu işleyen hizmet arasında güven oluşturur.

## <a name="set-up-connections"></a>Bağlantıları ayarlama
Gerekli hizmetlere bağlantı bilgileri, çözümdeki **appsettings.json** dosyasında belirtilir. 

1. Visual Studio 'da, **Dotnethowtoındexers. sln** dosyasını açın.

1. Çözüm Gezgini, her bir ayarı doldurmanız için **appSettings. JSON** ' u açın.  

Azure Bilişsel Arama hizmetinize yönelik URL ve yönetici anahtarlarını kullanarak şu anda yalnızca ilk iki giriş doldurabilirsiniz. `https://mydemo.search.windows.net`uç noktası verildiğinde, sağlanacak hizmet adı `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

Son giriş için mevcut bir veritabanı gerekir. Bunu bir sonraki adımda oluşturacaksınız.

## <a name="prepare-sample-data"></a>Örnek verileri hazırlama

Bu adımda, dizin oluşturucunun gezinebileceği bir dış veri kaynağı oluşturun. Azure SQL Veritabanı'nda veri kümesini oluşturmak için Azure Portal'ı ve örnekteki *hotels.sql* dosyasını kullanabilirsiniz. Azure Bilişsel Arama, bir görünümden veya sorgudan oluşturulan bir gibi düzleştirilmiş satır kümeleri kullanır. Örnek çözümdeki SQL dosyası tek bir tablo oluşturur ve doldurur.

Aşağıdaki alıştırmada, mevcut sunucu veya veritabanı olmadığı varsayılır ve 2. adımda size her ikisini de oluşturma yönergeleri verilir. İsteğe bağlı olarak, kaynağınızın mevcut olması durumunda, oteller tablosunu buna ekleyebilir ve 4. adımdan başlayabilirsiniz.

1. [Azure Portal oturum açın](https://portal.azure.com/). 

2. Bir veritabanı, sunucu ve kaynak grubu oluşturmak için bir **Azure SQL veritabanı** bulun veya oluşturun. Varsayılan değerleri ve en düşük düzey fiyatlandırma katmanını kullanabilirsiniz. Sunucu oluşturmanın bir avantajı, yönetici kullanıcı adı ve parolası belirtebilmenizdir; çünkü sonraki adımda tabloları oluşturmak ve yüklemek için bunlar gerekecektir.

   ![Yeni veritabanı sayfası](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Yeni sunucuyu ve veritabanını dağıtmak için **Oluştur**'a tıklayın. Sunucu ve veritabanının dağıtılmasını bekleyin.

4. Yeni veritabanınızın SQL Veritabanı sayfasını (zaten açık değilse) açın. Kaynak adı *SQL Server* değil *SQL veritabanı* olmalıdır.

   ![SQL veritabanı sayfası](./media/search-indexer-tutorial/hotels-db.png)

4. Gezinti bölmesinde **sorgu Düzenleyicisi (Önizleme)** öğesine tıklayın.

5. **Oturum Aç**'a tıklayın ve sunucu yöneticisinin kullanıcı adıyla parolasını girin.

6. **Sorgu aç**'a tıklayın ve *hotels.sql* dosyasının konumuna gidin. 

7. Dosyayı seçin ve **Aç**'a tıklayın. Betik aşağıdaki ekran görüntüsüne benzer görünmelidir:

   ![SQL betiği](./media/search-indexer-tutorial/sql-script.png)

8. Sorguyu yürütmek için **Çalıştır**'a tıklayın. Sonuçlar bölmesinde, 3 satır için sorgu başarılı oldu iletisini görmeniz gerekir.

9. Bu tablodan bir satır kümesi döndürmek için, doğrulama adımı olarak aşağıdaki sorguyu yürütebilirsiniz:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    `SELECT * FROM Hotels` sorgu prototipi Sorgu Düzenleyicisi'nde çalışmaz. Örnek verilerde, Konum alanında coğrafi koordinatlar vardır ve bunlar şu anda düzenleyicide işlenmemektedir. Sorgulanacak diğer sütunların listesi için, şu deyimi yürütebilirsiniz: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Artık bir dış veri kümeniz olduğuna göre, veritabanının ADO.NET bağlantı dizesini kopyalayın. Veritabanınızın SQL Veritabanı sayfasında **Ayarlar** > **Bağlantı Dizeleri**'ne gidin ve ADO.NET bağlantı dizesini kopyalayın.
 
    ADO.NET bağlantı dizesi aşağıdaki örneğe benzer; geçerli bir veritabanı adı, kullanıcı adı ve parola kullanacak şekilde değiştirilmiştir.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Bağlantı dizesini, Visual Studio'da **appsettings.json** dosyasındaki üçüncü girdi olarak "AzureSqlConnectionString" içine yapıştırın.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Kodu anlama

Veri ve yapılandırma ayarları olduktan sonra, **Dotnethowtoındexers. sln** dosyasındaki örnek program derlemek ve çalıştırmak için hazırlayın. Bunu yapmadan önce, birkaç dakikanızı ayırıp bu örnekteki dizin ve dizin oluşturucu tanımlarını öğrenin. İlgili kod iki dosyada yer alır:

  + **hotel.cs**, dizini tanımlayan şemayı içerir
  + **Program.cs**, hizmetinizdeki yapıları oluşturmaya ve yönetmeye yönelik işlevleri içerir

### <a name="in-hotelcs"></a>Hotel.cs dosyasında

Dizin şeması, aşağıdaki HotelName alan tanımında gösterildiği gibi alanın tam metin araması yapılabilir, filtrelenebilir veya sıralanabilir olup olmadığı gibi izin verilen işlemleri belirten öznitelikler de dahil olmak üzere alan koleksiyonunu tanımlar. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Şema başka öğeler, örneğin arama puanını artırmak için puanlama profilleri, özel çözümleyiciler ve başka yapılar içerebilir. Öte yandan, bizim amaçlarımıza uygun olarak şema yalnızca örnek veri kümelerinde bulunan alanlarla seyrek bir şekilde tanımlanmıştır.

Bu öğreticide, dizin oluşturucu verileri tek bir veri kaynağından çeker. Uygulamada, birden fazla veri kaynağından birleştirilmiş bir aranabilir dizin oluşturarak aynı dizine birden çok Dizin Oluşturucu ekleyebilirsiniz. Nerede esnekliğe ihtiyacınız olduğuna bağlı olarak, yalnızca veri kaynaklarında değişiklik yapıp aynı dizin oluşturucu-dizin çiftini veya çeşitli dizin oluşturucu ve veri kaynağı bileşimleriyle tek bir dizini kullanabilirsiniz.

### <a name="in-programcs"></a>Program.cs dosyasında

Ana program, bir istemci, dizin, veri kaynağı ve Dizin Oluşturucu oluşturma mantığını içerir. Kod, bu programı birden çok kez çalıştırabileceğiniz varsayımıyla, aynı adı taşıyan kaynakları denetler ve siler.

Veri kaynağı nesnesi, Azure SQL 'in yerleşik [değişiklik algılama özelliklerinden](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) yararlanmak için [artımlı dizin oluşturma](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) da dahIl olmak üzere Azure SQL veritabanı kaynaklarına özgü ayarlarla yapılandırılır. Azure SQL 'deki demo oteller veritabanının **IsDeleted**adlı "geçici silme" sütunu vardır. Bu sütun veritabanında true olarak ayarlandığında, Dizin Oluşturucu ilgili belgeyi Azure Bilişsel Arama dizininden kaldırır.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Dizin Oluşturucu nesnesi, yapılandırma, zamanlama ve çağrının kaynağa bakılmaksızın aynı olduğu platformdan bağımsız bir platformdur. Bu örnek Dizin Oluşturucu bir zamanlama, Dizin Oluşturucu geçmişini temizleyen bir sıfırlama seçeneği içerir ve Dizin oluşturucuyu hemen oluşturmak ve çalıştırmak için bir yöntem çağırır.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>Dizin oluşturucuyu çalıştırma

Bu adımda, programı derleyin ve çalıştırın. 

1. Çözüm Gezgini'nde **DotNetHowToIndexers** öğesine sağ tıklayın ve **Oluştur**'u seçin.
2. Bir kez daha **DotNetHowToIndexers** öğesine sağ tıklayın ve ardından **Hata Ayıkla** > **Yeni örnek başlat**'a tıklayın.

Program hata ayıklama modunda yürütülür. Konsol penceresinde her işlemin durumu bildirilir.

  ![SQL betiği](./media/search-indexer-tutorial/console-output.png)

Kodunuz, Azure'da arama hizmetinize bağlanarak Visual Studio'da yerel olarak çalıştırılır. Arama hizmeti de bağlantı dizesini kullanarak Azure SQL Veritabanı'na bağlanır ve veri kümesini alır. Bu kadar çok işlem olduğunda, bazı olası hata noktaları vardır ama hata alırsanız, önce aşağıdaki koşulları denetleyin:

+ Sağladığınız arama hizmeti bağlantı bilgileri, bu öğreticide hizmet adıyla sınırlıdır. Tam URL girdiyseniz, dizin oluşturma aşamasında bağlantı hatasıyla işlemler durdurulur.

+ **Appsettings.json** dosyasındaki veritabanı bağlantı bilgileri. Bu, portaldan alınmış ve veritabanınız için geçerli kullanıcı adı ve parolayı içerecek şekilde değiştirilmiş ADO.NET bağlantı dizesi olmalıdır. Kullanıcı hesabının verileri alma izni olmalıdır.

+ Kaynak sınırları. Ücretsiz katmanın 3 dizin, Dizin Oluşturucu ve veri kaynağı sınırlarına sahip olduğunu hatırlayın. Sınırına dayanmış bir hizmet yeni nesneler oluşturamaz.

## <a name="search-the-index"></a>Dizinde arama yapma 

Azure Portal'da, arama hizmetinin Genel Bakış sayfasında üst kısımdaki **Arama gezgini**'ne tıklayarak yeni dizine birkaç sorgu gönderin.

1. Üst kısımdaki **Dizini değiştir**'e tıklayarak *hotels* dizinini seçin.

2. Boş bir arama göndermek için **Ara** düğmesine tıklayın. 

   Dizininizdeki üç girdi, JSON belgeleri olarak döndürülür. Yapının tamamını görebilmeniz için arama gezgini belgeleri JSON olarak döndürür.

3. Ardından, bir arama dizesi girin: `search=river&$count=true`. 

   Bu sorgu, `river` terimi için tam metin aramasını çağırır ve sonuç eşleşen belgelerin sayısını içerir. Binlerce veya milyonlarca belge içeren çok büyük bir dizininiz olduğunda, test senaryolarında eşleşen belge sayısının döndürülmesi yararlı olur. Bizim örneğimizde, sorguyla eşleşen tek bir belge vardır.

4. Son olarak, JSON çıkışını ilgilendiğiniz alanlarla sınırlandıran bir arama dizesi girin: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   Sorgu yanıtı seçili alanlara daraltılır ve sonuçta daha kısa bir çıkış elde edilir.

## <a name="view-indexer-configuration"></a>Dizin oluşturucu yapılandırmasını görüntüleme

Az önce programlama yoluyla oluşturduğunuz dizin oluşturucu da dahil olmak üzere tüm dizin oluşturucular portalda listelenir. Dizin oluşturucu tanımını açabilir ve veri kaynağını görüntüleyebilir veya yeni ve değiştirilmiş satırları seçmek için bir yenileme zamanlaması yapılandırabilirsiniz.

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmeti **genel bakış** sayfasında **dizinler**, **Dizin oluşturucular**ve **veri kaynakları**için bağlantılara tıklayın.
3. Yapılandırma ayarlarını görüntülemek veya değiştirmek için ayrı nesneleri seçin.

   ![Dizin oluşturucu ve veri kaynağı kutucukları](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğreticiden sonra temizlemenin en hızlı yolu, Azure Bilişsel Arama hizmetini içeren kaynak grubunu silmelidir. Kaynak grubunu silerek içindeki her şeyi kalıcı olarak silebilirsiniz. Portalda, kaynak grubu adı Azure Bilişsel Arama hizmeti 'nin genel bakış sayfaalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Bir Dizin Oluşturucu ardışık düzenine AI zenginleştirme algoritmaları ekleyebilirsiniz. Sonraki adım olarak, aşağıdaki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Azure Blob Depolama’da Belgelerin Dizinini Oluşturma](search-howto-indexing-azure-blob-storage.md)