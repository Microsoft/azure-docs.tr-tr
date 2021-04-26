---
title: Uygulamanızı Amazon DynamoDB konumundan Azure Cosmos DB geçirin
description: .NET uygulamanızı Amazon 'ın DynamoDB 'den Azure Cosmos DB 'e geçirmeyi öğrenin
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: 9b4b5fca8017a906fa44b02edcf5f0bdcf6166b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334234"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Uygulamanızı Amazon DynamoDB konumundan Azure Cosmos DB geçirin
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, ölçeklenebilir, global olarak dağıtılmış, tam olarak yönetilen bir veritabanıdır. Verilerinize garantili düşük gecikme süresi erişimi sağlar. Azure Cosmos DB hakkında daha fazla bilgi edinmek için [genel bakış](introduction.md) makalesine bakın. Bu makalede, .NET uygulamanızın en az kod değişiklikleriyle Azure Cosmos DB için DynamoDB 'den nasıl geçirileceği açıklanır.

## <a name="conceptual-differences"></a>Kavramsal farklılıklar

Azure Cosmos DB ve DynamoDB arasındaki önemli kavramsal farklılıklar aşağıda verilmiştir:

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|Uygulanamaz|  Veritabanı |
|Tablo      |  Koleksiyon |
|  Öğe |  Belge |
|Öznitelik|Alan|
|İkincil dizin|İkincil dizin|
|Birincil anahtar – bölüm anahtarı|Bölüm Anahtarı|
|Birincil anahtar – sıralama anahtarı| Gerekli değil |
|Akış|Değişiklik akışı|
|Işlem birimini yaz|İstek birimi (esnek, okuma veya yazma işlemleri için kullanılabilir)|
|Işlem birimini oku    |İstek birimi (esnek, okuma veya yazma işlemleri için kullanılabilir)|
|Genel tablolar| Gerekli değildir. Azure Cosmos hesabını sağlarken bölgeyi doğrudan seçebilirsiniz (bölgeyi daha sonra değiştirebilirsiniz)|

## <a name="structural-differences"></a>Yapısal farklılıklar

Azure Cosmos DB, DynamoDB ile karşılaştırıldığında daha basit bir JSON yapısına sahiptir. Aşağıdaki örnekte farklar gösterilmektedir

**Dynamodb**:

Aşağıdaki JSON nesnesi, DynamoDB 'deki veri biçimini temsil eder

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

Aşağıdaki JSON nesnesi Azure Cosmos DB veri biçimini temsil eder

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>Verilerinizi geçirme

Verilerinizi Azure Cosmos DB geçirmek için kullanabileceğiniz çeşitli seçenekler vardır. Daha fazla bilgi edinmek için şirket [içi veya bulut verilerinizi Azure Cosmos DB makalesine geçirmeye yönelik seçeneklere](cosmosdb-migrationchoices.md) bakın.

## <a name="migrate-your-code"></a>Kodunuzu geçirme

Bu makale, veritabanı geçişinin önemli bir yönü olan Azure Cosmos DB bir uygulamanın kodunu geçirmek için kapsamlandırılır. Öğrenme eğrisini azaltmanıza yardımcı olması için aşağıdaki bölümlerde, Amazon DynamoDB ile Azure Cosmos DB denk kod parçacığı arasında bir yan yana kod karşılaştırması bulunur.

Kaynak kodu indirmek için aşağıdaki depoyu kopyalayın:

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>Ön koşullar

-  .NET Framework 4.7.2
- Visual Studio 2019
- Azure Cosmos DB SQL API hesabına erişim
- Amazon DynamoDB yerel yüklemesi
- Java 8
- 8000 numaralı bağlantı noktasında Amazon DynamoDB 'nin indirilebilir sürümünü çalıştırın (kodu değiştirebilir ve yapılandırabilirsiniz)

### <a name="set-up-your-code"></a>Kodunuzu ayarlama

Aşağıdaki "NuGet paketini" projenize ekleyin:

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>Bağlantı kurma

**Dynamodb**:

Amazon DynamoDB 'de, bağlanmak için aşağıdaki kod kullanılır:

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Azure Cosmos DB bağlanmak için kodunuzu şu şekilde güncelleştirin:

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Azure Cosmos DB bağlantıyı en iyileştirme**

Azure Cosmos DB, bağlantınızı iyileştirmek için aşağıdaki seçenekleri kullanabilirsiniz:

* **Connectionmode** -Azure Cosmos DB hizmetindeki veri düğümlerine bağlanmak için doğrudan bağlantı modunu kullanın. Ağ Geçidi modunu yalnızca mantıksal adresleri başlatmak ve önbelleğe almak ve güncelleştirmelerde yenilemek için kullanın. Daha fazla ayrıntı için [bağlantı modları](sql-sdk-connection-modes.md) makalesine bakın.

* **ApplicationRegion** -Bu seçenek, Azure Cosmos DB etkileşimde bulunmak için kullanılan tercih edilen coğrafi olarak çoğaltılan bölgeyi ayarlamak için kullanılır. Daha fazla bilgi edinmek için bkz. [genel dağıtım](distribute-data-globally.md) makalesi.

* Bu seçenek, varsayılan **tutarlılık düzeyini geçersiz** kılmak için kullanılır. Daha fazla bilgi edinmek için [tutarlılık düzeyleri](consistency-levels.md) makalesine bakın.

* **Bulkexecutionmode** -Bu seçenek, *allowbulkexecution* özelliği true olarak ayarlanarak toplu işlemleri yürütmek için kullanılır. Daha fazla bilgi edinmek için bkz. [toplu içeri aktarma](tutorial-sql-api-dotnet-bulk-import.md) makalesi.

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>Kapsayıcıyı sağlama

**Dynamodb**:

Verileri Amazon DynamoDB 'ye depolamak için önce tabloyu oluşturmanız gerekir. Bu işlemde, aşağıdaki kodda gösterildiği gibi şemayı, anahtar türünü ve öznitelikleri tanımlarsınız:

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

Amazon DynamoDB 'de, okuma işlem birimleri & yazma işlem birimleri sağlamanız gerekir. Azure Cosmos DB ' de, dinamik olarak herhangi bir işlem için kullanılabilen [Istek birimleri (ru/s)](request-units.md)olarak üretilen işi belirtirsiniz. Veriler veritabanı olarak düzenlenir--> kapsayıcı--> öğe. Aktarım hızını veritabanı düzeyinde veya koleksiyon düzeyinde ya da her ikisi de belirtebilirsiniz.

Veritabanı oluşturmak için:

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

Kapsayıcıyı oluşturmak için:

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>Verileri yükleme

**Dynamodb**:

Aşağıdaki kod, Amazon DynamoDB içindeki verilerin nasıl yükleneceğini gösterir. MoviesArray, JSON belgesi listesinden oluşur ve JSON belgesini Amazon DynamoDB içine yüklemeniz gerekir:

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

Azure Cosmos DB, akış ve yazma için tercih edebilirsiniz `moviesContainer.CreateItemStreamAsync()` . Ancak, bu örnekte, tür atama özelliğini göstermek için JSON, *Moviemodel* türünde seri durumdan çıkarılacak. Kod çok iş parçacıklı olduğundan Azure Cosmos DB dağıtılmış mimarisini kullanır ve yüklemeyi hızlandıracak:

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>Belge oluşturma

**Dynamodb**:

Amazon DynamoDB 'de yeni bir belge yazmak tür kullanımı güvenli değildir, aşağıdaki örnek belge türü olarak NewItem kullanır:

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB, veri modeli aracılığıyla bir tür güvenliği sağlar. ' MovieModel ' adlı veri modelini kullanıyoruz:

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

Azure Cosmos DB NewItem, MovieModel olacaktır:

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>Belge okuma

**Dynamodb**:

Amazon DynamoDB içinde okumak için, temelleri tanımlamanız gerekir:

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

Ancak, Azure Cosmos DB sorgu doğal (LINQ) ile birlikte:

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

Yukarıdaki örnekteki belgeler koleksiyonu şu şekilde olacaktır:

- tür kullanımı güvenli
- doğal bir sorgu seçeneği belirtin.

### <a name="update-an-item"></a>Öğeyi güncelleştirme

**Dynamodb**: Amazon dynamodb içindeki öğeyi güncelleştirmek için:

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

Azure Cosmos DB, güncelleştirme, mevcut değilse belgeyi yerleştir olarak kabul edilecek.

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>Bir belgeyi silme

**Dynamodb**:

Amazon DynamoDB içindeki bir öğeyi silmek için, temel elemanlarına düşecek şekilde yeniden ihtiyacınız vardır:

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

Azure Cosmos DB, belgeyi alabilir ve zaman uyumsuz olarak silebilirsiniz:

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>Belgeleri sorgulama

**Dynamodb**:

Amazon DynamoDB 'de, API işlevleri verileri sorgulamak için gereklidir:

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

Azure Cosmos DB, bir basit SQL sorgusu içinde projeksiyon ve filtreleme yapabilirsiniz:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

Aralık işlemleri için örneğin, ' between ', Amazon DynamoDB içinde bir tarama yapmanız gerekir:

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

Azure Cosmos DB, SQL sorgusu ve tek satırlık bir ifade kullanabilirsiniz:

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>Kapsayıcı silme

**Dynamodb**:

Amazon DynamoDB içindeki tabloyu silmek için şunları belirtebilirsiniz:

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Koleksiyonu Azure Cosmos DB silmek için şunları belirtebilirsiniz:

```csharp
await moviesContainer.DeleteContainerAsync();
```
Ardından, gerekirse veritabanını silin.

```csharp
await cosmosDatabase.DeleteAsync();
```

Görebileceğiniz gibi, Azure Cosmos DB doğal sorguları (SQL) destekler, işlemler zaman uyumsuzdur ve çok daha kolaydır. Karmaşık kodunuzu Azure Cosmos DB 'e kolayca geçirebilirsiniz. Bu, geçişten sonra daha kolay hale gelir.

### <a name="next-steps"></a>Sonraki Adımlar

- [Performans iyileştirmesi](performance-tips.md)hakkında bilgi edinin.
- [Okuma ve yazma işlemlerini iyileştirme](key-value-store-cost.md) hakkında bilgi edinin
- [Cosmos DB izleme](monitor-cosmos-db.md) hakkında bilgi edinin

