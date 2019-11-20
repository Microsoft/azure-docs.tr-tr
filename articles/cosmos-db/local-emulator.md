---
title: Azure Cosmos öykünücüsü ile yerel olarak geliştirme
description: Azure Cosmos öykünücüsünü kullanarak, Azure aboneliği oluşturmadan uygulamanızı ücretsiz olarak ücretsiz olarak geliştirebilir ve test edebilirsiniz.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/26/2019
ms.openlocfilehash: 773e55bd1908c04e1c73d998348d36b685524715
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075653"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Yerel geliştirme ve test için Azure Cosmos öykünücüsünü kullanma

Azure Cosmos öykünücüsü, Azure Cosmos DB hizmetini geliştirme amacıyla taklit eden bir yerel ortam sağlar. Azure Cosmos öykünücüsünü kullanarak, Azure aboneliği oluşturmadan veya herhangi bir ücret ödemeden uygulamanızı yerel olarak geliştirebilir ve test edebilirsiniz. Uygulamanızın Azure Cosmos öykünücüsünde nasıl çalıştığı konusunda memnun kaldığınızda, bulutta bir Azure Cosmos hesabı kullanarak geçiş yapabilirsiniz.

Azure Cosmos öykünücüsü 'nü [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api)ve [tablo](local-emulator.md#table-api) API hesaplarıyla kullanarak geliştirebilirsiniz. Ancak, öykünücüdeki Veri Gezgini görünümü yalnızca SQL API için istemcileri tamamen destekler. 

## <a name="how-the-emulator-works"></a>Öykünücü nasıl çalışır?

Azure Cosmos öykünücüsü Azure Cosmos DB hizmeti için yüksek uygunlukta bir öykünme sağlar. Veri oluşturma ve sorgulama, kapsayıcıları sağlama ve ölçeklendirme ve saklı yordamları ve Tetikleyicileri yürütme desteği dahil Azure Cosmos DB benzer işlevleri destekler. Azure Cosmos öykünücüsünü kullanarak uygulamalar geliştirebilir ve test edebilir ve yalnızca Azure Cosmos DB bağlantı uç noktasında tek bir yapılandırma değişikliği yaparak Azure 'a genel ölçekte dağıtabilirsiniz.

Azure Cosmos DB hizmetinin öykünmesi aslına sadık olsa da, öykünücünün uygulaması hizmetten farklıdır. Örneğin öykünücü, kalıcılık için yerel dosya sistemi gibi standart işletim sistemi bileşenlerini ve bağlantı için HTTPS protokolü yığınını kullanır. Genel çoğaltma, okuma/yazma işlemleri için tek haneli milisaniyelik gecikme süresi ve ayarlanabilir tutarlılık düzeyleri gibi Azure altyapısına dayanan işlevler geçerli değildir.

[Azure Cosmos DB veri geçiş aracını](https://github.com/azure/azure-documentdb-datamigrationtool)kullanarak Azure Cosmos öykünücüsü ile Azure Cosmos DB hizmeti arasında veri geçirebilirsiniz.

Azure Cosmos öykünücüsü 'nü Windows Docker kapsayıcısında çalıştırabilirsiniz. docker pull komutu için [Docker Hub 'ına](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) ve öykünücü kaynak kodu için [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) ' a bakın.

## <a name="differences-between-the-emulator-and-the-service"></a>Öykünücü ile hizmet arasındaki farklar

Azure Cosmos öykünücüsü yerel geliştirici iş istasyonunda çalışan bir Öykünülmüş ortam sağladığından, öykünücü ve buluttaki bir Azure Cosmos hesabı arasındaki işlevlerde bazı farklılıklar vardır:

* Öykünücü Veri Gezgini Şu anda SQL API istemcilerini destekler. MongoDB, Table, Graph ve Cassandra API 'Leri gibi Azure Cosmos DB API 'Ler için Veri Gezgini görünümü ve işlemleri tam olarak desteklenmez.
* Azure Cosmos öykünücüsü yalnızca tek bir sabit hesabı ve iyi bilinen bir ana anahtarı destekler. Azure Cosmos öykünücüsünde anahtar yeniden oluşturma mümkün değildir, ancak varsayılan anahtar komut satırı seçeneği kullanılarak değiştirilebilir.
* Azure Cosmos öykünücüsü ölçeklenebilir bir hizmet değildir ve çok sayıda kapsayıcıyı desteklemez.
* Azure Cosmos öykünücüsü farklı [Azure Cosmos DB tutarlılık düzeyleri](consistency-levels.md)sunmaz.
* Azure Cosmos öykünücüsü [çok bölgeli çoğaltma](distribute-data-globally.md)sunmaz.
* Azure Cosmos öykünücüsünün kopyası her zaman Azure Cosmos DB hizmetindeki en son değişikliklerle güncel olmayabilir, uygulamanızın üretim verimlilik (ru) ihtiyaçlarını doğru şekilde tahmin etmek için [Azure Cosmos DB kapasite planlayıcıya](https://www.documentdb.com/capacityplanner) başvurmalısınız.
* Azure Cosmos öykünücüsü 'nü kullanırken, varsayılan olarak, en fazla 25 sabit boyut kapsayıcısı (yalnızca Azure Cosmos DB SDK 'Ları kullanarak desteklenir) veya Azure Cosmos öykünücüsünü kullanarak 5 sınırsız kapsayıcı oluşturabilirsiniz. Bu değeri değiştirme hakkında daha fazla bilgi için bkz. [PartitionCount değerini ayarlama](#set-partitioncount).

## <a name="system-requirements"></a>Sistem gereksinimleri

Azure Cosmos öykünücüsü, aşağıdaki donanım ve yazılım gereksinimlerine sahiptir:

* Yazılım gereksinimleri
  * Windows Server 2012 R2, Windows Server 2016 veya Windows 10
  * 64-bit işletim sistemi
* Minimum Donanım gereksinimleri
  * 2-GB RAM
  * 10 GB kullanılabilir sabit disk alanı

## <a name="installation"></a>Yükleme

Azure Cosmos öykünücüsünü [Microsoft Indirme merkezi](https://aka.ms/cosmosdb-emulator) ' nden indirip yükleyebilir veya Docker for Windows Emulator 'ı çalıştırabilirsiniz. Öykünücüyü Docker for Windows'da kullanmaya ilişkin yönergeler için bkz. [Docker üzerinde çalıştırma](#running-on-docker).

> [!NOTE]
> Azure Cosmos öykünücüsü 'nü yüklemek, yapılandırmak ve çalıştırmak için bilgisayarda yönetici ayrıcalıklarına sahip olmanız gerekir. Öykünücü, bir sertifika oluşturur/ekler ve ayrıca hizmetlerini çalıştırmak için güvenlik duvarı kurallarını ayarlar; Bu nedenle, öykünücü bu tür işlemleri yürütebilmesi için gereklidir.

## <a name="running-on-windows"></a>Windows’da çalıştırma

Azure Cosmos öykünücüsü 'nü başlatmak için Başlat düğmesini seçin veya Windows tuşuna basın. **Azure Cosmos öykünücüsü**yazmaya başlayın ve uygulama listesinden öykünücü ' ı seçin.

![Başlat düğmesini seçin veya Windows tuşuna basın, * * Azure Cosmos öykünücüsü * * yazmaya başlayın ve uygulama listesinden öykünücü ' ı seçin](./media/local-emulator/database-local-emulator-start.png)

Öykünücü çalıştırıldığında, Windows görev çubuğu bildirim alanında bir simge görürsünüz. ![Yerel öykünücü Azure Cosmos DB görev çubuğu bildirimi](./media/local-emulator/database-local-emulator-taskbar.png)

Azure Cosmos öykünücüsü, bağlantı noktası 8081 ' ü dinleyen yerel makinede ("localhost") varsayılan olarak çalışır.

Azure Cosmos öykünücüsü, varsayılan olarak `C:\Program Files\Azure Cosmos DB Emulator` yüklenir. Komut satırından da öykünücüyü başlatabilir ve durdurabilirsiniz. Daha fazla bilgi için bkz. [komut satırı aracı başvurusu](#command-line).

## <a name="start-data-explorer"></a>Veri Gezgini’ni Başlat

Azure Cosmos öykünücüsü başlatıldığında Azure Cosmos Veri Gezgini tarayıcınızda otomatik olarak açar. Adres `https://localhost:8081/_explorer/index.html`olarak görünür. Gezgin 'i kapatır ve daha sonra yeniden açmak isterseniz, URL 'YI tarayıcınızda açabilir veya aşağıda gösterildiği gibi Windows tepsi simgesindeki Azure Cosmos öykünücüsünde başlatabilirsiniz.

![Azure Cosmos yerel öykünücü Veri Gezgini başlatıcısı](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Güncelleştirmeleri denetleme

Veri Gezgini, indirilebilir yeni bir güncelleştirme olup olmadığını belirtir.

> [!NOTE]
> Azure Cosmos öykünücüsünün bir sürümünde oluşturulan veriler (bkz:%LOCALAPPDATA%\CosmosDBEmulator veya veri yolu isteğe bağlı ayarları) farklı bir sürüm kullanılırken erişilebilir değildir. Verilerinizi uzun vadede kalıcı hale getirmeniz gerekiyorsa, bu verileri Azure Cosmos öykünücüsü yerine bir Azure Cosmos hesabında depolamanız önerilir.

## <a name="authenticating-requests"></a>İsteklerin kimliğini doğrulama

Bulutta Azure Cosmos DB olduğu gibi, Azure Cosmos öykünücüsünde karşı yaptığınız her isteğin kimliği doğrulanmalıdır. Azure Cosmos öykünücüsü, ana anahtar kimlik doğrulaması için tek bir sabit hesabı ve iyi bilinen bir kimlik doğrulama anahtarını destekler. Bu hesap ve anahtar, Azure Cosmos öykünücüsü ile kullanım için izin verilen tek kimlik bilgileridir. Bunlar:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos öykünücüsü tarafından desteklenen ana anahtarın yalnızca öykünücü ile kullanılması amaçlanmıştır. Üretim Azure Cosmos DB hesabınızı ve anahtarınızı Azure Cosmos öykünücüsü ile kullanamazsınız.

> [!NOTE]
> Emulator 'u/Key seçeneğiyle başlattıysanız, `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`yerine oluşturulan anahtarı kullanın. /Key seçeneği hakkında daha fazla bilgi için bkz [. komut satırı araç başvurusu.](#command-line)

Azure Cosmos DB itibariyle Azure Cosmos öykünücüsü yalnızca SSL aracılığıyla güvenli iletişimi destekler.

## <a name="running-on-a-local-network"></a>Yerel ağ üzerinde çalışma

Yerel bir ağ üzerinde öykünücüyü çalıştırabilirsiniz. Ağ erişimini etkinleştirmek için, [komut satırında](#command-line-syntax)`/AllowNetworkAccess` seçeneğini belirtin, bu da `/Key=key_string` veya `/KeyFile=file_name`belirtmenizi gerektirir. Rastgele anahtar önündeki bir dosyayı oluşturmak için `/GenKeyFile=file_name` kullanabilirsiniz. Daha sonra bunu `/KeyFile=file_name` veya `/Key=contents_of_file`geçirebilirsiniz.

Kullanıcı ilk kez öykünücüyü kapatması ve öykünücü veri dizinini (%LOCALAPPDATA%\CosmosDBEmulator) silmek için ağ erişimini etkinleştirmek için.

## <a name="developing-with-the-emulator"></a>Öykünücü ile geliştirme

### <a name="sql-api"></a>SQL API’si

Azure Cosmos öykünücüsünü masaüstünüzde çalışır olduktan sonra, öykünücüle etkileşim kurmak için desteklenen [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) veya [Azure Cosmos DB REST API](/rest/api/cosmos-db/) kullanabilirsiniz. Azure Cosmos öykünücüsü Ayrıca, SQL API Cosmos DB veya Mongo DB API 'SI için kapsayıcılar oluşturmanıza olanak sağlayan yerleşik bir Veri Gezgini içerir ve herhangi bir kod yazmadan öğeleri görüntüleyebilir ve düzenleyebilir.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API'si

Azure Cosmos öykünücüsünü masaüstünüzde çalışır olduktan sonra, öykünücüle etkileşimde bulunmak için [Azure Cosmos DB MongoDB IÇIN API](mongodb-introduction.md) 'sini kullanabilirsiniz. "/EnableMongoDbEndpoint" ile yönetici olarak komut isteminden öykünücü başlatın. Daha sonra MongoDB API hesabına bağlanmak için aşağıdaki bağlantı dizesini kullanın:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Tablo API’si

Azure Cosmos öykünücüsünü masaüstünüzde çalışan bir kez etkinleştirdikten sonra, öykünücüle etkileşim kurmak için [Azure Cosmos DB tablo API'si SDK 'sını](table-storage-how-to-use-dotnet.md) kullanabilirsiniz. "/EnableTableEndpoint" ile yönetici olarak komut isteminden öykünücü başlatın. Daha sonra tablo API hesabına bağlanmak için aşağıdaki kodu çalıştırın:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API’si

"/EnableCassandraEndpoint" ile yönetici komut isteminden öykünücü başlatın. Alternatif olarak, ortam değişkenini de `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`ayarlayabilirsiniz.

* [Python 2,7 'yi yükler](https://www.python.org/downloads/release/python-2716/)

* [Cassandra CLı/CSQLSH 'i yükler](https://cassandra.apache.org/download/)

* Normal bir komut istemi penceresinde aşağıdaki komutları çalıştırın:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* CSQLSH kabuğu 'nda Cassandra uç noktasına bağlanmak için aşağıdaki komutları çalıştırın:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>Gremlin API

"/EnableGremlinEndpoint" ile yönetici komut isteminden öykünücü başlatın. Alternatif olarak, ortam değişkenini de ayarlayabilirsiniz `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Apache-tinkerpop-Gremlin-Console-3.3.4 ' i yükler](https://tinkerpop.apache.org/downloads.html)

* Öykünücü Veri Gezgini, "DB1" veritabanını ve "coll1" koleksiyonunu oluşturun; bölüm anahtarı için "/Name" öğesini seçin

* Normal bir komut istemi penceresinde aşağıdaki komutları çalıştırın:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* Gremlin kabuğu 'nda Gremlin uç noktasına bağlanmak için aşağıdaki komutları çalıştırın:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>SSL sertifikasını dışarı aktarma

.NET dilleri ve çalışma zamanı, Azure Cosmos DB yerel öykünücüsüne güvenli şekilde bağlanmak için Windows Sertifika Deposunu kullanır. Diğer dillerin kendi sertifikaları yönetme ve kullanma yöntemi vardır. Java kendi [sertifika deposunu](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) kullanırken Python ise [yuva sarmalayıcılarını](https://docs.python.org/2/library/ssl.html) kullanır.

Windows Sertifika Deposu ile tümleştirilmeyen çalışma zamanları ve dillerle kullanmak üzere bir sertifika edinmek için Windows Sertifika Yöneticisi’ni kullanarak bunu dışarı aktarmanız gerekir. Bunu, Certlm. msc dosyasını çalıştırarak başlatabilir veya [Azure Cosmos öykünücü sertifikalarını dışarı aktarma](./local-emulator-export-ssl-certificates.md)konusundaki adım adım yönergeleri izleyebilirsiniz. Sertifika yöneticisi çalıştırıldıktan sonra aşağıda gösterildiği gibi Kişisel Sertifikaları açın ve sertifikayı BASE-64 kodlu X.509 (.cer) dosyası olarak "DocumentDBEmulatorCertificate" kolay adıyla dışarı aktarın.

![Azure Cosmos DB yerel öykünücüsü SSL sertifikası](./media/local-emulator/database-local-emulator-ssl_certificate.png)

X.509 sertifikası, [Java CA Sertifika Deposuna Sertifika Ekleme](https://docs.microsoft.com/azure/java-add-certificate-ca-store) bölümündeki yönergeler izlenerek Java sertifika deposuna içeri aktarılabilir. Sertifika sertifika deposuna alındıktan sonra, SQL ve Azure Cosmos DB 's API istemcilerinin MongoDB için olan istemcileri Azure Cosmos öykünücüsünde bağlantı kurabiliyor.

Python ve Node.js SDK’larından öykünücüye bağlanırken SSL doğrulaması devre dışı bırakılır.

## <a id="command-line"></a>Komut satırı aracı başvurusu
Yükleme konumundan, öykünücü başlatmak ve durdurmak, seçenekleri yapılandırmak ve diğer işlemleri gerçekleştirmek için komut satırını kullanabilirsiniz.

### <a name="command-line-syntax"></a>Komut satırı sözdizimi

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Seçenek listesini görüntülemek için komut satırına `CosmosDB.Emulator.exe /?` yazın.

|**Seçenek** | **Açıklama** | **Komut**| **Bağımsız Değişkenler**|
|---|---|---|---|
|[Bağımsız değişken yok] | Azure Cosmos öykünücüsünü varsayılan ayarlarla başlatır. |CosmosDB.Emulator.exe| |
|[Yardım] |Desteklenen komut satırı bağımsız değişkenleri listesini görüntüler.|CosmosDB.Emulator.exe /? | |
| GetStatus |Azure Cosmos öykünücüsünün durumunu alır. Durum, çıkış kodu tarafından belirtilir: 1 = Başlatılıyor, 2 = Çalıştırılıyor, 3 = Durduruldu. Negatif çıkış kodu, bir hata oluştuğunu gösterir. Başka bir çıktı üretilmez. | CosmosDB.Emulator.exe /GetStatus| |
| Kapat| Azure Cosmos öykünücüsünü kapatır.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Veri dosyalarının depolanacağı yolu belirtir. Varsayılan değer%LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: Erişilebilir bir yol |
|Bağlantı noktası | Öykünücü için kullanılacak bağlantı noktası numarasını belirtir. Varsayılan değer 8081 ' dir. |CosmosDB.Emulator.exe /Port=\<port\> | \<port\>: Tek bir bağlantı noktası numarası |
| ComputePort | Compute Interop Gateway hizmeti için kullanılacak bağlantı noktası numarası belirtildi. Ağ geçidinin HTTP uç nokta araştırması bağlantı noktası ComputePort + 79 olarak hesaplanır. Bu nedenle, ComputePort ve ComputePort + 79 açık ve kullanılabilir olmalıdır. Varsayılan değerler 8900, 8979 ' dir. | CosmosDB. Emulator. exe/ComputePort = \<ComputePort\> | \<computeport\>: tek bağlantı noktası numarası |
| EnableMongoDbEndpoint | MongoDB API 'sini sunar | CosmosDB. Emulator. exe/EnableMongoDbEndpoint | |
| MongoPort | MongoDB uyumluluk API’si için kullanılacak bağlantı noktası numarasını belirtir. Varsayılan değer 10255 ' dir. |CosmosDB. Emulator. exe/MongoPort = \<mongoport\>|\<mongoport\>: Tek bir bağlantı noktası numarası|
| EnableCassandraEndpoint | Cassandra API etkinleştirilir | CosmosDB. öykünücü. exe/EnableCassandraEndpoint | |
| CassandraPort | Cassandra uç noktası için kullanılacak bağlantı noktası numarasını belirtir. Varsayılan değer 10350 ' dir. | CosmosDB. Emulator. exe/CassandraPort = \<cassandraport\> | cassandraport\>\<: tek bağlantı noktası numarası |
| EnableGremlinEndpoint | Gremlin API 'sini sunar | CosmosDB. öykünücü. exe/EnableGremlinEndpoint | |
| GremlinPort | Gremlin uç noktası için kullanılacak bağlantı noktası numarası. Varsayılan değer 8901 ' dir. | CosmosDB. Emulator. exe/GremlinPort =\<bağlantı noktası\> | \<port\>: Tek bir bağlantı noktası numarası |
|EnableTableEndpoint | Azure Tablo API'si etkinleştirilir | CosmosDB. öykünücü. exe/EnableTableEndpoint | |
|TablePort | Azure Tablo uç noktası için kullanılacak bağlantı noktası numarası. Varsayılan değer 8902 ' dir. | CosmosDB. Emulator. exe/TablePort =\<bağlantı noktası\> | \<port\>: Tek bir bağlantı noktası numarası|
| Dosyasına | Belirtilen dosyadan yetkilendirme anahtarını okuyun. Bir keyfile oluşturmak için/GenKeyFile seçeneğini kullanın | CosmosDB. Emulator. exe/KeyFile =\<file_name\> | \<file_name\>: dosyanın yolu |
| ResetDataPath | Belirtilen yoldaki tüm dosyaları yinelemeli olarak kaldırır. Bir yol belirtmezseniz, varsayılan olarak%LOCALAPPDATA%\CosmosDbEmulator olur | CosmosDB. Emulator. exe/ResetDataPath =\<yol > | \<yolu\>: dosya yolu  |
| Başlangıçizler  |  Hata ayıklama izleme günlüklerini toplamaya başla. | CosmosDB. Emulator. exe/Startizlemeler | |
| Durma Izlemeleri     | Hata ayıklama izleme günlüklerini toplamayı durdur. | CosmosDB. Emulator. exe/Stopizlemeleri  | |
|Failonsslcertificatenameuyuşmazlığı | Varsayılan olarak, sertifika SAN 'ı öykünücü konağın etki alanı adını, yerel IPv4 adresini, ' localhost ' ve ' 127.0.0.1 ' içermiyorsa, öykünücü otomatik olarak imzalanan SSL sertifikasını yeniden oluşturur. Bu seçenekle öykünücü, bunun yerine başlangıçta başarısız olur. Ardından, yeni bir otomatik olarak imzalanan SSL sertifikası oluşturmak ve yüklemek için/GenCert seçeneğini kullanmanız gerekir. | CosmosDB. Emulator. exe/Failonsslcertificatenameuyuşmazlığı  | |
| GenCert | Otomatik olarak imzalanan yeni bir SSL sertifikası oluşturun ve yükler. isteğe bağlı olarak, ağ üzerinden öykünücüye erişmek için ek DNS adlarının virgülle ayrılmış listesini de içeren bir liste. | CosmosDB. Emulator. exe/GenCert =\<DNS-adları\> |\<DNS adları\>: ek DNS adlarının Isteğe bağlı virgülle ayrılmış listesi  |
| DirectPorts |Doğrudan bağlantı için kullanılacak bağlantı noktalarını belirtir. Varsayılan değerler: 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: 4 bağlantı noktasından oluşan virgülle ayrılmış liste |
| Anahtar |Öykünücü için yetkilendirme anahtarı. Anahtar, 64 bayt vektörün base 64 kodlaması olmalıdır. | CosmosDB.Emulator.exe /Key:\<key\> | \<key\>: Anahtar, 64 bayt vektörün base 64 kodlaması olmalıdır|
| EnableRateLimiting | İstek oranını sınırlama davranışının etkinleştirildiğini belirtir. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |İstek oranını sınırlama davranışının devre dışı bırakıldığını belirtir. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | Öykünücü kullanıcı arabirimini gösterme. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | Başlangıçta veri gezginini gösterme. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | En fazla bölümlenmiş kapsayıcı sayısını belirtir. Daha fazla bilgi için bkz. [kapsayıcı sayısını değiştirme](#set-partitioncount) . | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitionCount\>: izin verilen en fazla sayıda tek bölüm kapsayıcısı. Varsayılan değer 25’tir. Maksimum izin verilen: 250.|
| DefaultPartitionCount| Bölümlenmiş bir kapsayıcı için varsayılan bölüm sayısını belirtir. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> varsayılan değer 25 ' tir.|
| AllowNetworkAccess | Bir ağ üzerinden öykünücüye erişilmesini sağlar. Ağ erişimini etkinleştirmek için /Key=\<key_string\> veya /KeyFile=\<file_name\> öğesini de geçirmeniz gerekir. | CosmosDB. Emulator. exe/AllowNetworkAccess/Key =\<key_string\> veya CosmosDB. Emulator. exe/AllowNetworkAccess/KeyFile =\<file_name\>| |
| NoFirewall | /AllowNetworkAccess seçeneği kullanıldığında güvenlik duvarı kurallarını ayarlamadığınızda. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Yeni bir yetkilendirme anahtarı oluşturun ve belirtilen dosyaya kaydedin. Oluşturulan anahtar, /Key veya /KeyFile seçenekleri ile kullanılabilir. | CosmosDB. Emulator. exe/GenKeyFile =\<anahtar dosyasının yolu\> | |
| Tutarlılık | Hesap için varsayılan tutarlılık düzeyini ayarlayın. | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<consistency\>: Değer, şu [tutarlılık düzeylerinden](consistency-levels.md) biri olmalıdır: Oturum, Güçlü, Son veya Sınırlanmış Eskime Durumu. Varsayılan değer: Oturum. |
| ? | Yardım iletisini gösterin.| | |

## <a id="set-partitioncount"></a>Kapsayıcıların sayısını değiştirme

Varsayılan olarak, en fazla 25 sabit boyut kapsayıcısı (yalnızca Azure Cosmos DB SDK 'Ları kullanılarak desteklenir) veya Azure Cosmos öykünücüsünü kullanarak 5 sınırsız kapsayıcı oluşturabilirsiniz. **PartitionCount** değerini değiştirerek, en fazla 250 sabit boyut kapsayıcısı veya 50 sınırsız kapsayıcı oluşturabilir ya da iki değerin herhangi bir bileşimini (sınırsız kapsayıcı = 5 sabit boyut kapsayıcısı) 250 aşamaz. Ancak, öykünücü 200 ' den fazla sabit boyutlu kapsayıcıyla çalışacak şekilde ayarlanmalıdır. Disk GÇ işlemlerine eklediği ek yük nedeniyle, uç nokta API 'Leri kullanılırken öngörülemeyen zaman aşımları ile sonuçlanır.

Geçerli bölüm sayısı aşıldıktan sonra bir kapsayıcı oluşturmaya çalışırsanız, öykünücü aşağıdaki iletiyle bir ServiceUnavailable özel durumu atar.

"Üzgünüz, şu anda bu bölgede yüksek taleple karşılaşıyoruz ve isteğinizi Şu anda gerçekleştiremiyor. Daha fazla ve daha fazla kapasiteyi çevrimiçi hale getirmek için sürekli olarak çalıştık ve yeniden denemeniz önerilir.
Lütfen e-posta askcosmosdb@microsoft.com istediğiniz zaman veya herhangi bir nedenle girmeyin.
ActivityId: 12345678-1234-1234-1234-123456789ABC "

Azure Cosmos öykünücüsünde bulunan kapsayıcıların sayısını değiştirmek için aşağıdaki adımları çalıştırın:

1. Sistem tepsisindeki **Azure Cosmos DB öykünücü** simgesine sağ tıklayıp ardından **verileri Sıfırla...** öğesine tıklayarak tüm yerel Azure Cosmos öykünücü verilerini silin.
2. Bu klasördeki tüm öykünücü verilerini `%LOCALAPPDATA%\CosmosDBEmulator`silin.
3. Sistem tepsisindeki **Azure Cosmos DB Öykünücüsü** simgesine sağ tıklayıp **Çıkış**’a tıklayarak tüm açık örneklerden çıkın. Tüm örneklerin çıkması bir dakika sürebilir.
4. [Azure Cosmos öykünücüsünün](https://aka.ms/cosmosdb-emulator)en son sürümünü yükler.
5. 250 veya daha düşük bir değer ayarlayarak PartitionCount bayrağı ile öykünücüyü başlatın. Örneğin: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Öykünücüyü denetleme

Öykünücü, hizmetin durumunu başlatmak, durdurmak, kaldırmak ve almak için bir PowerShell modülü ile birlikte gelir. PowerShell modülünü kullanmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

veya `PSModules` dizinini `PSModulesPath` yerleştirip aşağıdaki komutta gösterildiği gibi içeri aktarın:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Aşağıda, PowerShell’den öykünücüyü denetlemeye ilişkin komutların özeti verilmiştir:

### `Get-CosmosDbEmulatorStatus`

**Söz dizimi**

`Get-CosmosDbEmulatorStatus`

**Açıklamalar**

Şu ServiceControllerStatus değerlerinden birini döndürür: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running veya ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Söz dizimi**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Açıklamalar**

Öykünücüyü başlatır. Varsayılan olarak komut, öykünücünün istekleri kabul etmeye hazır olmasını bekler. Cmdlet’in öykünücüyü başlattığı anda geri dönmesini istiyorsanız -NoWait seçeneğini kullanın.

### `Stop-CosmosDbEmulator`

**Söz dizimi**

 `Stop-CosmosDbEmulator [-NoWait]`

**Açıklamalar**

Öykünücüyü durdurur. Varsayılan olarak bu komut, öykünücünün tamamen kapanmasını bekler. Cmdlet’in öykünücü kapanmaya başladığı anda geri dönmesini istiyorsanız -NoWait seçeneğini kullanın.

### `Uninstall-CosmosDbEmulator`

**Söz dizimi**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Açıklamalar**

Öykünücüyü kaldırır ve isteğe bağlı olarak $env:LOCALAPPDATA\CosmosDbEmulator içeriklerinin tamamını kaldırır.
Cmdlet, öykünücüyü kaldırmadan önce öykünücünün tamamen durdurulduğundan emin olur.

## <a name="running-on-docker"></a>Docker’da çalıştırma

Azure Cosmos öykünücüsü Docker for Windows üzerinde çalıştırılabilir. Öykünücü Docker for Oracle Linux üzerinde çalışmaz.

[Docker for Windows](https://www.docker.com/docker-windows) yüklendikten sonra, araç çubuğundaki Docker simgesine sağ tıklayıp **Windows kapsayıcılarına geç** seçeneğini belirleyerek Windows kapsayıcılarına geçin.

Daha sonra sık kullandığınız kabuktan aşağıdaki komutu çalıştırarak Docker Hub'dan Öykünücü görüntüsünü çekin.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Görüntüyü başlatmak için aşağıdaki komutları çalıştırın.

Komut satırından:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```

> [!NOTE]
> Docker Run komutunu çalıştırdığınızda bir bağlantı noktası çakışması hatası görürseniz (belirtilen bağlantı noktası zaten kullanımda), bağlantı noktası numaralarını değiştirerek özel bir bağlantı noktası geçirebilirsiniz. Örneğin, "-p 8081:8081" öğesini "-p 443:8081" olarak değiştirebilirsiniz

PowerShell’den:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

Yanıt şuna benzer:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Şimdi istemcinizdeki yanıttan uç noktayı ve ana anahtar girişini kullanın ve SSL sertifikasını ana bilgisayarınıza içeri aktarın. SSL sertifikasını içeri aktarmak için, yönetici komut isteminden aşağıdakileri yapın:

Komut satırından:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

PowerShell’den:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Öykünücü başlatıldıktan sonra etkileşimli kabuğu kapatmak öykünücünün kapsayıcısını kapatır.

Veri Gezgini’ni açmak için tarayıcınızda aşağıdaki URL’ye gidin. Yukarıda gösterilen yanıt iletisinde öykünücü uç noktası sağlanır.

    https://<emulator endpoint provided in response>/_explorer/index.html

Linux Docker kapsayıcısında çalışan bir .NET istemci uygulamanız varsa ve bir konak makinesinde Azure Cosmos öykünücüsü çalıştırıyorsanız, bu durumda öykünücünden Azure Cosmos hesabına bağlanamazsınız. Uygulama konak makinede çalışmadığından, öykünücü uç noktasıyla eşleşen Linux kapsayıcısına kayıtlı sertifika eklenemiyor. 

Geçici bir çözüm olarak, aşağıdaki .NET kod örneğinde gösterildiği gibi bir `HttpClientHandler` örneğini geçirerek, sunucunun SSL sertifika doğrulamasını istemci uygulamanızdan devre dışı bırakabilirsiniz. Bu geçici çözüm yalnızca `Microsoft.Azure.DocumentDB` NuGet paketini kullanıyorsanız geçerlidir; bu, `Microsoft.Azure.Cosmos` NuGet paketiyle desteklenmez:
 
 ```csharp
var httpHandler = new HttpClientHandler()
{
    ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
};
 
using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
{
    RunDatabaseDemo(client).GetAwaiter().GetResult();
}
```

SSL sertifika doğrulamasını devre dışı bırakmanın yanı sıra, öykünücüyü `/allownetworkaccess` seçeneğiyle başlatmanız ve öykünücü uç noktasına `host.docker.internal` DNS yerine ana bilgisayar IP adresinden erişilebilir olması önemlidir.

## Mac veya Linux üzerinde çalışıyor<a id="mac"></a>

Şu anda Cosmos öykünücüsü yalnızca Windows üzerinde çalıştırılabilir. Mac veya Linux çalıştıran kullanıcılar, öykünücüyü Parallels veya VirtualBox gibi bir hiper yönetici olarak barındırılan bir Windows sanal makinesinde çalıştırabilir. Bunu etkinleştirme adımları aşağıda verilmiştir.

Windows VM içinde aşağıdaki komutu çalıştırın ve IPv4 adresini unutmayın.

```cmd
ipconfig.exe
```

Uygulamanızın içinde, DocumentClient nesnesinin URI 'sini `ipconfig.exe`tarafından döndürülen IPv4 adresini kullanacak şekilde değiştirmeniz gerekir. Sonraki adım, DocumentClient nesnesini oluştururken CA doğrulamasının etrafında çalışmadır. Bunun için, The DocumentClient oluşturucusuna bir HttpClientHandler sağlamanız gerekir ve bu, ServerCertificateCustomValidationCallback için kendi uygulamasına sahip olur.

Aşağıda kodun nasıl görüneceğine ilişkin bir örnek verilmiştir.

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

Son olarak, Windows VM 'nin içinden aşağıdaki seçenekleri kullanarak Cosmos öykünücüsünü komut satırından başlatın.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>Sorun giderme

Azure Cosmos öykünücüsü ile karşılaştığınız sorunları gidermeye yardımcı olması için aşağıdaki ipuçlarını kullanın:

- Öykünücünün yeni bir sürümünü yüklediyseniz ve hatalarla karşılaşıyorsanız, verilerinizi sıfırladığınızdan emin olun. Sistem tepsisinde Azure Cosmos öykünücü simgesine sağ tıklayıp verileri Sıfırla... öğesine tıklayarak verilerinizi sıfırlayabilirsiniz. Bu, hataları gidermezse, öykünücü ve Emulator 'un eski sürümlerini kaldırabilir, "C:\Program files\Azure Cosmos DB Emulator" dizinini kaldırabilir ve öykünücüyü yeniden yükleyebilirsiniz. Yönergeler için bkz. [Yerel öykünücüden kaldırma](#uninstall).

- Azure Cosmos öykünücüsü kilitlenirse, '%LocalAppData%\crashdökümler ' klasöründen döküm dosyalarını toplayın, bunları sıkıştırarak ve [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)için bir e-postaya ekleyin.

- `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`kilitlenmelerden karşılaşırsanız, bu durum performans sayaçlarının bozulmuş durumda olduğu bir belirti olabilir. Genellikle bir yönetici komut isteminden aşağıdaki komutu çalıştırmak sorunu düzeltir:

  ```cmd
  lodctr /R
   ```

- Bir bağlantı sorunu yaşarsanız [izleme dosyalarını toplayın](#trace-files), sıkıştırın ve bir e-postaya ekleyip [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) adresine gönderin.

- **Hizmet Kullanılamıyor** iletisi alırsanız öykünücü, ağ yığınını başlatamıyor olabilir. Pulse secure istemcisinin veya Juniper networks istemcisinin yüklü olup olmadığını denetleyin; bunların ağ filtresi sürücüleri soruna yol açıyor olabilir. Genellikle üçüncü taraf ağ filtresi sürücüleri kaldırıldığında sorun düzeltilir. Alternatif olarak, öykünücü ağ iletişimini normal Winsock 'a geçecek olan/Disablerıo ile öykünücüyü başlatın. 

- Öykünücü çalışırken bilgisayarınız uyku moduna geçer veya herhangi bir işletim sistemi güncelleştirmesi çalıştırırsa, bir **Service is currently unavailable** (Hizmet şu anda kullanılamıyor) iletisi alabilirsiniz. Windows bildirim tepsisinde görüntülenen simgeye sağ tıklayıp **verileri Sıfırla**' yı seçerek öykünücü verilerini sıfırlayın.

### <a id="trace-files"></a>İzleme dosyalarını toplama

Hata ayıklama izlemelerini toplamak için bir yönetici komut isteminden aşağıdaki komutları çalıştırın:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Programın kapatıldığından emin olmak için sistem tepsisine bakın; bu bir dakika sürebilir. Azure Cosmos öykünücü Kullanıcı arabiriminde de **Çıkış** ' a tıklayabilirsiniz.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Sorunu yeniden oluşturun. Veri Gezgini çalışmıyorsa yalnızca hatayı yakalamak için tarayıcının birkaç saniye boyunca açılmasını beklemeniz gerekir.
5. `CosmosDB.Emulator.exe /stoptraces`
6. `%ProgramFiles%\Azure Cosmos DB Emulator` konumuna gidin ve docdbemulator_000001.etl dosyasını bulun.
7. Hata ayıklama için .etl dosyasını yeniden üretme adımlarıyla birlikte [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) adresine gönderin.

### <a id="uninstall"></a>Yerel öykünücüyü kaldırma

1. Sistem tepsisinde Azure Cosmos öykünücü simgesine sağ tıklayıp Çıkış ' a tıklayarak Yerel öykünücüsünün tüm açık örneklerinden çıkın. Tüm örneklerin çıkması bir dakika sürebilir.
2. Windows arama kutusuna **Uygulamalar ve özellikler** yazın ve **Uygulamalar ve özellikler (Sistem ayarları)** sonucuna tıklayın.
3. Uygulamalar listesinde **Azure Cosmos DB Öykünücüsü**’ne gidip bunu seçin, **Kaldır**’a tıklayın, daha sonra onaylayıp yeniden **Kaldır**’a tıklayın.
4. Uygulama kaldırıldığında `%LOCALAPPDATA%\CosmosDBEmulator` klasörüne gidip klasörü silin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide ücretsiz yerel geliştirme için yerel öykünücünün nasıl kullanılacağını öğrendiniz. Artık sonraki öğreticiye devam edebilir ve öykünücü SSL sertifikalarının nasıl dışarı aktarılacağını öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [Azure Cosmos öykünücü sertifikalarını dışarı aktarma](local-emulator-export-ssl-certificates.md)
