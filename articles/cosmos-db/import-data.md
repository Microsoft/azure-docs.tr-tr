---
title: 'Öğretici: Azure Cosmos DB için veritabanı geçiş aracı'
description: 'Öğretici: MongoDB, SQL Server, tablo depolama, Amazon DynamoDB, CSV ve JSON dosyaları gibi çeşitli kaynaklardan Azure Cosmos DB verileri içeri aktarmak için açık kaynaklı Azure Cosmos DB veri geçiş araçlarını nasıl kullanacağınızı öğrenin. CSV’yi JSON’a dönüştürme.'
author: deborahc
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: dech
ms.openlocfilehash: 1d25a2c9a3fda48c2f7de01563e01dd0c7de7762
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721149"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>Öğretici: verilerinizi Azure Cosmos DB geçirmek için veri geçiş aracını kullanma

Bu öğretici, çeşitli kaynaklardan Azure Cosmos kapsayıcılarına ve tablolarına veri aktarabileceğiniz Azure Cosmos DB veri geçiş aracı 'nı kullanma hakkında yönergeler sağlar. JSON dosyalarından, CSV dosyalarından, SQL, MongoDB, Azure Tablo depolamadan, Amazon DynamoDB ve hatta Azure Cosmos DB SQL API koleksiyonlarından içeri aktarma işlemi yapabilirsiniz. Bu verileri Azure Cosmos DB birlikte kullanmak için koleksiyonlara ve tablolara geçirebilirsiniz. Veri Geçişi aracı, tek bir bölüm koleksiyonundan SQL API’si için çok bölümlü bir koleksiyona geçirme işlemi sırasında da kullanılabilir.

Azure Cosmos DB ile hangi API’yi kullanacaksınız?

* **[SQL API’si](documentdb-introduction.md)** : Verileri içeri aktarmak için Veri Geçişi aracında sağlanan kaynak seçeneklerinin herhangi birini kullanabilirsiniz.
* **[Tablo API’si](table-introduction.md)** : Verileri içeri aktarmak için Veri Geçişi aracını veya AzCopy’yi kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Cosmos DB Tablo API’si ile kullanılmak üzere verileri içeri aktarma](table-import.md).
* **[MongoDB IÇIN apı Azure Cosmos DB](mongodb-introduction.md)** -veri geçiş aracı şu anda mongodb için Azure Cosmos DB API 'sini kaynak veya hedef olarak desteklememektedir. Azure Cosmos DB içindeki koleksiyonların içindeki veya olmayan verileri geçirmek istiyorsanız, yönergeler için [MongoDB verilerini MongoDB için Azure Cosmos DB API 'Siyle nasıl geçirebileceğiniz](mongodb-migrate.md) konusuna bakın. SQL API’si ile kullanılmak üzere MongoDB’den Azure Cosmos DB SQL API koleksiyonlarına verileri dışarı aktarmak için Veri Geçişi aracını kullanmaya devam edebilirsiniz.
* **[GREMLIN API](graph-introduction.md)** -veri geçiş aracı şu anda Gremlin API hesapları için desteklenen bir içeri aktarma aracı değil.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Veri Geçişi aracını yükleme
> * Farklı veri kaynaklarından verileri içeri aktarma
> * Azure Cosmos DB’den JSON’a dışarı aktarma

## <a id="Prerequisites"></a>Önkoşullar

Bu makaledeki yönergeleri uygulamadan önce aşağıdaki adımları belirttiğinizden emin olun:

* [Microsoft .NET Framework 4,51](https://www.microsoft.com/download/developer-tools.aspx) veya üstünü yükler.

* **Aktarım hızını artırma:** Veri geçişinizin süresi, tek bir koleksiyon veya bir koleksiyon kümesi için ayarladığınız aktarım hızı miktarına bağlıdır. Büyük veri geçişleri için aktarım hızını artırdığınızdan emin olun. Geçişi tamamladıktan sonra maliyet tasarrufu sağlamak için aktarım hızını azaltın. Azure portal aktarım hızını artırma hakkında daha fazla bilgi için, bkz. Azure Cosmos DB [performans düzeyleri](performance-levels.md) ve [fiyatlandırma katmanları](https://azure.microsoft.com/pricing/details/cosmos-db/) .

* **Azure Cosmos DB kaynaklarını oluşturma:** Veri geçişini başlatmadan önce Azure portaldan tüm koleksiyonlarınızı oluşturun. Veritabanı düzeyinde aktarım hızı olan bir Azure Cosmos DB hesabına geçiş yapmak için, Azure Cosmos kapsayıcıları oluştururken bir bölüm anahtarı sağlayın.

## <a id="Overviewl"></a>Genel Bakış

Veri Geçişi aracı, aşağıda örnekleri verilen çeşitli kaynaklardan Azure Cosmos DB’ye veri içeri aktaran bir açık kaynak çözümüdür:

* JSON dosyaları
* MongoDB
* SQL Server
* CSV dosyaları
* Azure Tablo depolama
* Amazon DynamoDB
* HBase
* Azure Cosmos kapsayıcıları

İçeri aktarma aracı bir grafiksel kullanıcı arabirimi (dtui.exe) içerse de, komut satırından (dt.exe) da tetiklenebilir. Aslında, Kullanıcı arabirimi aracılığıyla bir içeri aktarma ayarladıktan sonra ilişkili komutun çıktısını almak için bir seçenek vardır. İçeri aktarma sırasında hiyerarşik ilişkiler (alt belgeler) oluşturmak için SQL Server veya CSV dosyaları gibi tablo kaynaklı verileri dönüştürebilirsiniz. Kaynak seçenekleri, her bir kaynaktan içeri aktarma için örnek komutlar, hedef seçenekler ve içeri aktarma sonuçlarını görüntüleme hakkında daha fazla bilgi edinmek için devamını okuyun.

## <a id="Install"></a>Yükleme

Geçiş aracı kaynak koduna GitHub’da [bu depodan](https://github.com/azure/azure-documentdb-datamigrationtool) erişilebilir. Çözümü yerel olarak indirip derleyebilir veya [önceden derlenmiş bir ikiliyi indirip](https://aka.ms/csdmtool) daha sonra aşağıdakilerden birini çalıştırabilirsiniz:

* **Dtui.exe**: Aracın grafiksel arabirim sürümü
* **Dt.exe**: Aracın komut satırı sürümü

## <a name="select-data-source"></a>Veri kaynağı seçme

Aracı yükledikten sonra verilerinizin içeri aktarılmasına sıra gelir. Ne tür verileri içeri aktarmak istiyorsunuz?

* [JSON dosyaları](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB Dışarı aktarma dosyaları](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV dosyaları](#CSV)
* [Azure Tablo Depolama](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos kapsayıcıları](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB toplu içeri aktarma](#SQLBulkTarget)
* [Azure Cosmos DB sıralı kayıt içeri aktarma](#SQLSeqTarget)

## <a id="JSON"></a>JSON dosyalarını içeri aktarma

JSON dosya kaynağı alma seçeneği, bir veya daha fazla tek belge JSON dosyasını veya her biri bir dizi JSON belgesi olan JSON dosyasını içeri aktarmanıza olanak tanır. İçeri aktarılacak JSON dosyaları olan klasörler eklenirken, alt klasörlerdeki dosyaları yinelemeli olarak arama seçeneğiniz vardır.

![JSON dosya kaynağı seçeneklerinin ekran görüntüsü: Veritabanı geçişi araçları](./media/import-data/jsonsource.png)

Bağlantı dizesi aşağıdaki biçimdedir:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* `<CosmosDB Endpoint>` uç nokta URI 'sidir. Bu değeri Azure portal alabilir. Azure Cosmos hesabınıza gidin. **Genel bakış** bölmesini açın ve **URI** değerini kopyalayın.
* `<AccountKey>`, "parola" veya **BIRINCIL anahtardır**. Bu değeri Azure portal alabilir. Azure Cosmos hesabınıza gidin. **Bağlantı dizelerini** veya **anahtarlar** bölmesini açın ve "parola" veya **birincil anahtar** değerini kopyalayın.
* `<CosmosDB Database>` CosmosDB veritabanı adıdır.

Örnek: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen Cosmos DB hesabına erişilebildiğinden emin olmak için Doğrula komutunu kullanın.

Aşağıda, JSON dosyalarını içeri aktarmak için bazı komut satırı örnekleri verilmiştir:

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a id="MongoDB"></a>MongoDB’den içeri aktarma

> [!IMPORTANT]
> MongoDB için Azure Cosmos DB API 'SI ile yapılandırılmış bir Cosmos hesabına aktarıyorsanız, bu [yönergeleri](mongodb-migrate.md)izleyin.

MongoDB kaynak alma seçeneği ile, tek bir MongoDB koleksiyonundan içeri aktarabilir, isteğe bağlı olarak belgeleri bir sorgu kullanarak filtreleyebilir ve bir projeksiyon kullanarak belge yapısını değiştirebilirsiniz.  

![MongoDB kaynağı seçeneklerinin ekran görüntüsü](./media/import-data/mongodbsource.png)

Bağlantı dizesi, standart MongoDB biçimindedir:

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen MongoDB örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

İçinden verilerin içeri aktarılacağı koleksiyonun adını girin. İsteğe bağlı olarak, `{pop: {$gt:5000}}`gibi bir sorgu için bir dosya veya `{loc:0}`gibi bir yansıtma ya da içeri aktardığınız verileri filtrelemek ve şekillendirmek için bir dosya belirtebilirsiniz.

Aşağıda, MongoDB’den içeri aktarılacak bazı komut satırı örnekleri verilmiştir:

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a id="MongoDBExport"></a>MongoDB dışarı aktarma dosyalarını içeri aktarma

> [!IMPORTANT]
> MongoDB desteğiyle Azure Cosmos DB bir hesaba aktarıyorsanız, bu [yönergeleri](mongodb-migrate.md)izleyin.

MongoDB dışarı aktarma JSON dosyası kaynak içeri aktarıcı seçeneği, mongoexport yardımcı programından üretilen bir veya daha fazla JSON dosyasını içeri aktarmanıza olanak sağlar.  

![MongoDB dışarı aktarma kaynağı seçeneklerinin ekran görüntüsü](./media/import-data/mongodbexportsource.png)

İçeri aktarma için MongoDB dışa aktarma JSON dosyalarını içeren klasörler eklenirken, alt klasörlerdeki dosyaları yinelemeli olarak arama seçeneğiniz vardır.

Aşağıda, MongoDB dışarı aktarma JSON dosyalarından içeri aktarma işlemi yapmak için bir komut satırı örneği verilmiştir:

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a id="SQL"></a>SQL Server’dan içeri aktarma

SQL kaynak içeri aktarıcı seçeneği, tek bir SQL Server veritabanından içeri aktarma işlemi yapmanıza ve isteğe bağlı şekilde bir sorgu kullanarak içeri aktarılacak kayıtları filtrelemenize olanak sağlar. Ayrıca bir iç içe geçirme ayırıcısı belirterek de belge yapısını değiştirebilirsiniz (kısa bir süre sonra bununla ilgili daha fazla bilgi verilecektir).  

![SQL kaynak seçeneklerinin ekran görüntüsü: veritabanı geçişi araçları](./media/import-data/sqlexportsource.png)

Bağlantı dizesinin biçimi, standart SQL bağlantı dizesi biçimindedir.

> [!NOTE]
> Bağlantı dizesi alanında belirtilen SQL Server örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

İçeri aktarma sırasında hiyerarşik ilişkiler (alt belgeler) oluşturmak için iç içe geçirme ayırıcısı özelliği kullanılır. Aşağıdaki SQL sorgusunu dikkate alın:

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

Aşağıdaki (kısmi) sonuçları döndürür:

![SQL sorgusu sonuçlarının ekran görüntüsü](./media/import-data/sqlqueryresults.png)

Address.AddressType ve Address.Location.StateProvinceName gibi diğer adlara dikkat edin. İç içe geçirme ayırıcısı olarak '.' öğesi belirtildiğinde içeri aktarma aracı içeri aktarma sırasında Address ve Address.Location alt belgelerini oluşturur. Aşağıda, Azure Cosmos DB’de elde edilen bir belge örneği verilmiştir:

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

Aşağıda, SQL Server’dan içeri aktarılacak bazı komut satırı örnekleri verilmiştir:

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a id="CSV"></a>CSV dosyalarını içeri aktarma ve CSV’yi JSON’a dönüştürme

CSV dosyası kaynak içeri aktarıcı seçeneği, bir veya daha fazla CSV dosyasını içeri aktarmanıza olanak sağlar. İçeri aktarma için CSV dosyaları olan klasörler eklenirken, alt klasörlerdeki dosyaları yinelemeli olarak arama seçeneğiniz vardır.

![CSV kaynak seçeneklerinin ekran görüntüsü: CSV’den JSON’a](media/import-data/csvsource.png)

SQL kaynağına benzer şekilde, içeri aktarma sırasında hiyerarşik ilişkiler (alt belgeler) oluşturmak için iç içe geçirme ayırıcısı özelliği kullanılabilir. Aşağıdaki CSV üst bilgisi satırını ve veri satırlarını dikkate alın:

![CSV örnek kayıtlarının ekran görüntüsü: CSV’den JSON’a](./media/import-data/csvsample.png)

DomainInfo.Domain_Name ve RedirectInfo.Redirecting gibi diğer adlara dikkat edin. İç içe geçirme ayırıcısı olarak '.' öğesi belirtildiğinde içeri aktarma aracı içeri aktarma sırasında DomainInfo ve RedirectInfo alt belgelerini oluşturur. Aşağıda, Azure Cosmos DB’de elde edilen bir belge örneği verilmiştir:

*{"DomainInfo": {"etki_alanı_adı": "ACUS.GOV", "Domain_Name_Address": "https:\//www.ACUS.GOV"}, "Federal kurum": "Birleşik Devletler Yönetim Konferansı", "Redirectınfo": {"yeniden yönlendirme": "0", "Redirect_Destination": ""}, "kimlik": "9cc565c5-EBCD-1c03-ebd3-cc3e2ecd814d"}*

İçeri aktarma aracı, CSV dosyalarındaki tırnak içine alınmış olmayan değerler için tür bilgilerini çıkarmaya çalışır (tırnak içinde değerler her zaman dize olarak değerlendirilir).  Türler şu sırayla tanımlanır: sayı, tarih saat, boole.  

CSV içeri aktarımı ile ilgili dikkat edilecek iki şey daha vardır:

1. Varsayılan olarak, tırnak içine alınmayan değerler her zaman sekmeler ve boşluklar için kırpılırken, tırnak içine alınan değerler olduğu gibi korunur. Tırnak içine alınan değerleri kırp onay kutusu veya /s.TrimQuoted komut satırı seçeneği ile bu davranış geçersiz kılınabilir.
2. Varsayılan olarak, tırnak içine alınmayan null değer, null değer olarak değerlendirilir. Tırnak içine alınmayan NULL değeri dize olarak değerlendir onay kutusu veya /s.NoUnquotedNulls komut satırı seçeneği ile bu davranış geçersiz kılınabilir (başka bir deyişle, tırnak içine alınmayan null değer, "null" dize olarak değerlendirilir).

Aşağıda, CSV içeri aktarımı için bir komut satırı örneği verilmiştir:

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a id="AzureTableSource"></a>Azure Tablo depolamadan içeri aktarma

Azure Tablo depolama kaynak içeri aktarıcı seçeneği, tek bir Azure Tablo depolama tablosundan içeri aktarma işlemi yapmanıza olanak sağlar. İsteğe bağlı olarak, içeri aktarılacak tablo varlıklarını filtreleyebilirsiniz.

Azure Tablo depolamadan içeri aktarılan verileri, Tablo API'si kullanım için Azure Cosmos DB tablo ve varlıklara aktarmak üzere çıktısını alabilirsiniz. İçeri aktarılan veriler, SQL API 'SI ile kullanılmak üzere koleksiyonlar ve belgeler için de çıktı olabilir. Ancak, Tablo API'si yalnızca komut satırı yardımcı programında hedef olarak kullanılabilir. Veri geçiş aracı Kullanıcı arabirimini kullanarak Tablo API'si dışarı aktarabilirsiniz. Daha fazla bilgi için bkz. [Azure Cosmos DB Tablo API’si ile kullanılmak üzere verileri içeri aktarma](table-import.md).

![Azure Tablo depolama kaynağı seçeneklerinin ekran görüntüsü](./media/import-data/azuretablesource.png)

Azure Tablo depolama bağlantı dizesinin biçimi şöyledir:

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen Azure Tablo depolama örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

İçinden içeri aktarma yapılacak Azure tablosunun adını girin. İsteğe bağlı olarak bir [filtre](../vs-azure-tools-table-designer-construct-filter-strings.md) belirtebilirsiniz.

Azure Tablo depolama kaynağı içeri aktarıcı seçeneği aşağıdaki ek seçenekleri içerir:

1. İç Alanları Dahil Et
   1. Tümü: Tüm iç alanları (PartitionKey, RowKey ve Timestamp) dahil et
   2. Hiçbiri: Tüm iç alanları dışla
   3. RowKey: Yalnızca RowKey alanını dahil et
2. Sütun Seç
   1. Azure Tablo depolama filtreleri projeksiyonları desteklemez. Yalnızca belirli Azure Tablo varlığı özelliklerini içeri aktarmak istiyorsanız bunları Sütun Seç listesine ekleyin. Diğer tüm varlık özellikleri yoksayılır.

Aşağıda, Azure Tablo depolamadan içeri aktarılacak bir komut satırı örneği verilmiştir:

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a id="DynamoDBSource"></a>Amazon DynamoDB’den içeri aktarma

Amazon DynamoDB kaynağı alma seçeneği, tek bir Amazon DynamoDB tablosundan içeri aktarmanızı sağlar. Bu, isteğe bağlı olarak içeri aktarılacak varlıkları filtreleyebilirler. İçeri aktarımın mümkün olduğunca kolay şekilde ayarlanması için birçok şablon sağlanır.

![Amazon DynamoDB kaynağı seçeneklerinin ekran görüntüsü: veritabanı geçişi araçları](./media/import-data/dynamodbsource1.png)

![Amazon DynamoDB kaynağı seçeneklerinin ekran görüntüsü: veritabanı geçişi araçları](./media/import-data/dynamodbsource2.png)

Amazon DynamoDB bağlantı dizesinin biçimi şöyledir:

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen Amazon DynamoDB örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

Aşağıda, Amazon DynamoDB’den içeri aktarım için bir komut satırı örneği verilmiştir:

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a id="BlobImport"></a>Azure Blob depolama alanından içeri aktarma

JSON dosyası, MongoDB dışarı aktarma dosyası ve CSV dosyası kaynak içeri aktarıcı seçenekleri, Azure Blob depolama alanından bir veya daha fazla dosyayı içeri aktarmanıza olanak sağlar. Bir Blob kapsayıcı URL’si ve Hesap Anahtarı belirttikten sonra, içeri aktarılacak dosyaları seçmek için bir normal ifade sağlayın.

![Blob dosya kaynağı seçeneklerinin ekran görüntüsü](./media/import-data/blobsource.png)

Aşağıda, Azure Blob depolama alanından JSON dosyalarını içeri aktarmak için komut satırı örneği verilmiştir:

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a id="SQLSource"></a>SQL API koleksiyonundan içeri aktarma

Azure Cosmos DB kaynak içeri aktarma seçeneği, bir veya daha fazla Azure Cosmos kapsayıcılarından verileri içeri aktarmanıza ve isteğe bağlı olarak belgeleri bir sorgu kullanarak filtrelemenize olanak tanır.  

![Azure Cosmos DB kaynağı seçeneklerinin ekran görüntüsü](./media/import-data/documentdbsource.png)

Azure Cosmos DB bağlantı dizesinin biçimi şöyledir:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

[Azure Cosmos DB hesabı yönetme](manage-account.md)bölümünde açıklandığı gibi, Azure Portal anahtarlar sayfasından Azure Cosmos DB hesabı bağlantı dizesini alabilirsiniz. Ancak, veritabanının adı bağlantı dizesinin aşağıdaki biçimde eklenmesi gerekir:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen Azure Cosmos DB örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

Tek bir Azure Cosmos kapsayıcısından içeri aktarmak için, verilerin içeri aktarılacağı koleksiyonun adını girin. Birden fazla Azure Cosmos kapsayıcısından içeri aktarmak için bir veya daha fazla koleksiyon adıyla eşleşecek bir normal ifade sağlayın (örneğin, collection01 | collection02 | collection03). İsteğe bağlı olarak, içeri aktardığınız verilerin filtreleneceği ve şekillendirileni için bir sorgu belirtebilir veya bir dosya sağlayabilirsiniz.

> [!NOTE]
> Koleksiyon alanı normal ifadeleri kabul ettiğinden, adının normal ifade karakterlerine sahip olduğu tek bir koleksiyondan içeri aktarıyorsanız, bu karakterlerin buna uygun şekilde kaçışılması gerekir.

Azure Cosmos DB kaynağı içeri aktarıcı seçeneği aşağıdaki gelişmiş seçenekleri içerir:

1. İç Alanları Dahil Et: Azure Cosmos DB belge sistemi özelliklerinin dışa aktarıma dahil edilip edilmeyeceğini belirtir (örneğin, _rid, _ts).
2. Hata Durumunda Yeniden Deneme Sayısı: Geçici hatalar (örneğin, ağ bağlantısı kesintisi) olması durumunda Azure Cosmos DB’ye bağlantı yeniden denemesi sayısını belirtir.
3. Yeniden Deneme Aralığı: Geçici hatalar (örneğin, ağ bağlantısı kesintisi) olması durumunda Azure Cosmos DB’ye yapılacak bağlantı yeniden denemeleri arasında beklenecek süreyi belirtir.
4. Bağlantı Modu: Azure Cosmos DB ile kullanılacak bağlantı modunu belirtir. Kullanılabilir seçenekler, DirectTcp, DirectHttps ve Gateway seçenekleridir. Doğrudan bağlantı modları daha hızlıyken, ağ geçidi modu ise yalnızca 443 numaralı bağlantı noktasını kullandığından güvenlik duvarıyla daha kolay kullanılabilir.

![Azure Cosmos DB kaynağı gelişmiş seçeneklerinin ekran görüntüsü](./media/import-data/documentdbsourceoptions.png)

> [!TIP]
> İçeri aktarma aracı, varsayılan olarak DirectTcp bağlantı moduna geçer. Güvenlik duvarıyla ilgili sorun yaşarsanız, Ağ Geçidi bağlantı moduna geçin; bu yalnızca 443 numaralı bağlantı noktasını gerektirir.

Aşağıda, Azure Cosmos DB’den içeri aktarılacak bazı komut satırı örnekleri verilmiştir:

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite /t.CollectionThroughput:2500
```

> [!TIP]
> Azure Cosmos DB Veri İçeri Aktarma Aracı, [Azure Cosmos DB Öykünücüsü](local-emulator.md)’nden verilerin içeri aktarımını da destekler. Yerel bir öykünücüden verilerini içeri aktarırken uç noktayı `https://localhost:<port>` olarak ayarlayın.

## <a id="HBaseSource"></a>HBase’den içeri aktarma

HBase kaynak içeri aktarıcı seçeneği, bir HBase tablosundan verileri içeri aktarmanıza ve isteğe bağlı olarak verileri filtrelemenize olanak sağlar. İçeri aktarımın mümkün olduğunca kolay şekilde ayarlanması için birçok şablon sağlanır.

![HBase kaynağı seçeneklerinin ekran görüntüsü](./media/import-data/hbasesource1.png)

![HBase kaynağı seçeneklerinin ekran görüntüsü](./media/import-data/hbasesource2.png)

HBase Stargate bağlantı dizesinin biçimi şöyledir:

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen HBase örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

Aşağıda, HBase’den içeri aktarım için bir komut satırı örneği verilmiştir:

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a id="SQLBulkTarget"></a>SQL API’sine içeri aktarma (Toplu İçeri Aktarma)

Azure Cosmos DB Toplu içeri aktarıcı, verimlilik için bir Azure Cosmos DB saklı yordamını kullanarak kullanılabilir kaynak seçeneklerden herhangi birinden içeri aktarma işlemi yapmanıza olanak sağlar. Araç, tek bölümlenmiş bir Azure Cosmos kapsayıcısına içeri aktarmayı destekler. Ayrıca, verilerin birden fazla tek bölümlenmiş Azure Cosmos kapsayıcısına bölünmesinin parçalı içeri aktarmayı da destekler. Verileri bölümleme hakkında daha fazla bilgi için bkz. [Azure Cosmos DB'de bölümleme ve ölçeklendirme](partition-data.md). Araç, saklı yordam oluşturur, yürütür ve sonra hedef koleksiyonlardan saklı yordamı siler.  

![Azure Cosmos DB toplu seçeneklerinin ekran görüntüsü](./media/import-data/documentdbbulk.png)

Azure Cosmos DB bağlantı dizesinin biçimi şöyledir:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

[Azure Cosmos DB hesabını yönetme](manage-account.md) bölümünde açıklandığı gibi, Azure portalının Anahtarlar sayfasından Azure Cosmos DB hesabı bağlantı dizesi alınabilse de, bağlantı dizesinin sonuna veritabanı adının şu biçimde eklenmesi gerekir:

`Database=<CosmosDB Database>;`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen Azure Cosmos DB örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

Tek bir koleksiyona içeri aktarmak için, içinden verilerin içeri aktarılacağı koleksiyonun adını girin ve Ekle düğmesine tıklayın. Birden fazla koleksiyona içeri aktarmak için, her bir koleksiyon adını ayrı ayrı girin veya birden fazla koleksiyon belirtmek için şu sözdizimini kullanın: *collection_prefix*[Başlangıç dizini-bitiş dizini]. Belirtilen sözdizimini kullanarak birden fazla koleksiyon belirtirken aşağıdaki yönergeleri aklınızda bulundurun:

1. Yalnızca tamsayı aralığı adı desenleri desteklenir. Örneğin, collection[0-3] belirtildiğinde şu koleksiyonlar oluşturulur: collection0, collection1, collection2, collection3.
2. Kısaltılmış bir sözdizimi kullanabilirsiniz: collection[3], 1. adımda belirtilen aynı koleksiyon kümesini oluşturur.
3. Birden fazla değiştirme sağlanabilir. Örneğin, collection[0-1] [0-9], başında sıfır olan 20 koleksiyon adı oluşturur (collection01, ..02, ..03).

Koleksiyon adları belirtildikten sonra, koleksiyonların istediğiniz aktarım hızını seçin (400 RU - 10.000 RU). En iyi içeri aktarma performansı için daha yüksek bir aktarım hızı seçin. Performans düzeyleri hakkında daha fazla bilgi için bkz. [Azure Cosmos DB’de performans düzeyleri](performance-levels.md).

> [!NOTE]
> Performans aktarım hızı ayarı yalnızca koleksiyon oluşturma için geçerlidir. Belirtilen koleksiyon zaten varsa, işlem hacmi değiştirilmez.

Birden fazla koleksiyona aktardığınızda, içeri aktarma aracı karma tabanlı parçalama destekler. Bu senaryoda, bölüm anahtarı olarak kullanmak istediğiniz belge özelliğini belirtin. (Bölüm anahtarı boş bırakılırsa, belgeler hedef koleksiyonlar arasında rastgele bir şekilde oluşturulur.)

İçeri aktarma işlemi sırasında içeri aktarma kaynağındaki hangi alanın Azure Cosmos DB belge KIMLIĞI özelliği olarak kullanılması gerektiğini isteğe bağlı olarak belirtebilirsiniz. Belgeler bu özelliğe sahip değilse, içeri aktarma aracı KIMLIK özelliği değeri olarak bir GUID oluşturur.

İçeri aktarma sırasında kullanılabilen birçok gelişmiş seçenek vardır. İlk olarak, araç varsayılan bir toplu içeri aktarma saklı yordamı (BulkInsert.js) içerse de, kendi içeri aktarma saklı yordamınızı belirtmeyi seçebilirsiniz:

 ![Azure Cosmos DB toplu ekleme sproc seçeneğinin ekran görüntüsü](./media/import-data/bulkinsertsp.png)

Ayrıca veri türlerini içeri aktarırken (örneğin, SQL Server veya MongoDB’den) üç içeri aktarma seçeneği arasından seçim yapabilirsiniz:

 ![Azure Cosmos DB tarih saat içeri aktarma seçeneklerinin ekran görüntüsü](./media/import-data/datetimeoptions.png)

* Dize: Dize değeri olarak kalıcı olur
* Dönem: Bir Dönem sayı değeri olarak kalıcı olur
* Her İkisi: Hem dize hem de Dönem sayı değeri olarak kalıcı olur. Bu seçenek bir alt belge oluşturur; örneğin: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB Toplu içeri aktarıcı aşağıdaki ek gelişmiş seçenekleri içerir:

1. Toplu İş Boyutu: Araç varsayılan olarak 50 toplu iş boyutunu belirler.  İçeri aktarılacak belgeler büyükse, toplu iş boyutunu küçültün. Aksine, içeri aktarılacak belgeler küçükse, toplu iş boyutunu büyütün.
2. Maksimum Betik Boyutu (bayt): Araç varsayılan olarak 512 KB maksimum betik boyutunu belirler.
3. Otomatik kimlik oluşturmayı devre dışı bırak: içeri aktarılacak her belge bir KIMLIK alanına sahipse, bu seçeneğin belirlenmesi performansı artırabilir. Benzersiz bir KIMLIK alanı eksik olan belgeler içeri aktarılmaz.
4. Mevcut belgeleri güncelleştir: araç, mevcut belgeleri KIMLIK çakışmaları ile değiştirmez. Bu seçeneğin belirlenmesi, eşleşen kimlikleri olan mevcut belgelerin üzerine yazılmasına izin verir. Bu özellik, mevcut belgeleri güncelleştiren zamanlanmış veri geçişleri için yararlıdır.
5. Hata durumunda yeniden deneme sayısı: geçici hatalar sırasında Azure Cosmos DB bağlantısının ne sıklıkta yeniden deneneceğini belirtir (örneğin, ağ bağlantısı kesintisi).
6. Yeniden Deneme Aralığı: Geçici hatalar (örneğin, ağ bağlantısı kesintisi) olması durumunda Azure Cosmos DB’ye yapılacak bağlantı yeniden denemeleri arasında beklenecek süreyi belirtir.
7. Bağlantı Modu: Azure Cosmos DB ile kullanılacak bağlantı modunu belirtir. Kullanılabilir seçenekler, DirectTcp, DirectHttps ve Gateway seçenekleridir. Doğrudan bağlantı modları daha hızlıyken, ağ geçidi modu ise yalnızca 443 numaralı bağlantı noktasını kullandığından güvenlik duvarıyla daha kolay kullanılabilir.

![Azure Cosmos DB toplu içeri aktarma gelişmiş seçeneklerinin ekran görüntüsü](./media/import-data/docdbbulkoptions.png)

> [!TIP]
> İçeri aktarma aracı, varsayılan olarak DirectTcp bağlantı moduna geçer. Güvenlik duvarıyla ilgili sorun yaşarsanız, Ağ Geçidi bağlantı moduna geçin; bu yalnızca 443 numaralı bağlantı noktasını gerektirir.

## <a id="SQLSeqTarget"></a>SQL API’sine içeri aktarma (Sıralı Kayıt İçeri Aktarma)

Azure Cosmos DB sıralı kayıt alma programı, kayıt temelinde bulunan kullanılabilir bir kaynak seçeneğinden içeri aktarmanızı sağlar. Saklı yordam kotasına ulaşmış olan mevcut bir koleksiyona içeri aktarma işlemi yapıyorsanız bu seçeneği belirleyebilirsiniz. Araç tek bir (tek bölümlü ve çok bölgeli) Azure Cosmos kapsayıcısına aktarmayı destekler. Ayrıca, verilerin birden fazla tek bölümlü veya çok bölgeli Azure Cosmos kapsayıcısına bölümlenmesi halinde bulunan parçalı içeri aktarmayı destekler. Verileri bölümleme hakkında daha fazla bilgi için bkz. [Azure Cosmos DB'de bölümleme ve ölçeklendirme](partition-data.md).

![Azure Cosmos DB sıralı kayıt içeri aktarma seçeneklerinin ekran görüntüsü](./media/import-data/documentdbsequential.png)

Azure Cosmos DB bağlantı dizesinin biçimi şöyledir:

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

[Azure Cosmos DB hesabını yönetme](manage-account.md)bölümünde açıklandığı gibi, Azure Portal anahtarlar sayfasından Azure Cosmos DB hesabının bağlantı dizesini alabilirsiniz. Ancak, veritabanının adı bağlantı dizesinin aşağıdaki biçimde eklenmesi gerekir:

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Bağlantı dizesi alanında belirtilen Azure Cosmos DB örneğinin erişilebilir olduğundan emin olmak için Doğrula komutunu kullanın.

Tek bir koleksiyona aktarmak için, verilerin içe aktarılacağı koleksiyonun adını girin ve ardından Ekle düğmesine tıklayın. Birden fazla koleksiyona içeri aktarmak için her koleksiyon adını tek tek girin. Birden fazla koleksiyon belirtmek için şu sözdizimini de kullanabilirsiniz: *collection_prefix*[Başlangıç dizini-bitiş dizini]. Belirtilen sözdizimi aracılığıyla birden fazla koleksiyon belirtirken aşağıdaki yönergeleri aklınızda bulundurun:

1. Yalnızca tamsayı aralığı adı desenleri desteklenir. Örneğin, collection[0-3] belirtildiğinde şu koleksiyonlar oluşturulur: collection0, collection1, collection2, collection3.
2. Kısaltılmış bir sözdizimi kullanabilirsiniz: collection[3], 1. adımda belirtilen aynı koleksiyon kümesini oluşturur.
3. Birden fazla değiştirme sağlanabilir. Örneğin, collection[0-1] [0-9], başında sıfır olan 20 koleksiyon adı oluşturur (collection01, ..02, ..03).

Koleksiyon adları belirtildikten sonra, koleksiyonların istediğiniz aktarım hızını seçin (400 RU - 250.000 RU). En iyi içeri aktarma performansı için daha yüksek bir aktarım hızı seçin. Performans düzeyleri hakkında daha fazla bilgi için bkz. [Azure Cosmos DB’de performans düzeyleri](performance-levels.md). Aktarım hızı 10.000 RU’da büyük olan koleksiyonlara içeri aktarma işlemi için bölüm anahtarı gerekir. 250.000’den fazla RU’nuz olmasını seçerseniz, hesabınızın artırılması için portalda bir istekte bulunmanız gerekir.

> [!NOTE]
> Aktarım hızı ayarı yalnızca koleksiyon veya veritabanı oluşturma için geçerlidir. Belirtilen koleksiyon zaten varsa, işlem hacmi değiştirilmez.

Birden fazla koleksiyona içeri aktarırken, içeri aktarma aracı karma tabanlı parçalama destekler. Bu senaryoda, bölüm anahtarı olarak kullanmak istediğiniz belge özelliğini belirtin. (Bölüm anahtarı boş bırakılırsa, belgeler hedef koleksiyonlar arasında rastgele bir şekilde oluşturulur.)

İçeri aktarma işlemi sırasında içeri aktarma kaynağındaki hangi alanın Azure Cosmos DB belge KIMLIĞI özelliği olarak kullanılması gerektiğini isteğe bağlı olarak belirtebilirsiniz. (Belgeler bu özelliğe sahip değilse, içeri aktarma aracı KIMLIK özelliği değeri olarak bir GUID oluşturur.)

İçeri aktarma sırasında kullanılabilen birçok gelişmiş seçenek vardır. İlk olarak, veri türlerini içeri aktarırken (örneğin, SQL Server veya MongoDB’den) üç içeri aktarma seçeneği arasından seçim yapabilirsiniz:

 ![Azure Cosmos DB tarih saat içeri aktarma seçeneklerinin ekran görüntüsü](./media/import-data/datetimeoptions.png)

* Dize: Dize değeri olarak kalıcı olur
* Dönem: Bir Dönem sayı değeri olarak kalıcı olur
* Her İkisi: Hem dize hem de Dönem sayı değeri olarak kalıcı olur. Bu seçenek bir alt belge oluşturur; örneğin: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB: Sıralı kayıt içeri aktarıcı aşağıdaki ek gelişmiş seçenekleri içerir:

1. Paralel İstek Sayısı: Araç varsayılan olarak iki paralel isteği belirler. İçeri aktarılacak belgeler küçükse, paralel istek sayısını yükseltin. Bu sayı çok fazla yükseltilirse içeri aktarma sırasında hız sınırlama oluşabilir.
2. Otomatik kimlik oluşturmayı devre dışı bırak: içeri aktarılacak her belge bir KIMLIK alanına sahipse, bu seçeneğin belirlenmesi performansı artırabilir. Benzersiz bir KIMLIK alanı eksik olan belgeler içeri aktarılmaz.
3. Mevcut belgeleri güncelleştir: araç, mevcut belgeleri KIMLIK çakışmaları ile değiştirmez. Bu seçeneğin belirlenmesi, eşleşen kimlikleri olan mevcut belgelerin üzerine yazılmasına izin verir. Bu özellik, mevcut belgeleri güncelleştiren zamanlanmış veri geçişleri için yararlıdır.
4. Hata durumunda yeniden deneme sayısı: geçici hatalar sırasında Azure Cosmos DB bağlantısının ne sıklıkta yeniden deneneceğini belirtir (örneğin, ağ bağlantısı kesintisi).
5. Yeniden deneme aralığı: geçici hatalarda Azure Cosmos DB bağlantının yeniden deneneceği (örneğin, ağ bağlantısı kesintisi) arasında ne kadar bekleneceğini belirtir.
6. Bağlantı Modu: Azure Cosmos DB ile kullanılacak bağlantı modunu belirtir. Kullanılabilir seçenekler, DirectTcp, DirectHttps ve Gateway seçenekleridir. Doğrudan bağlantı modları daha hızlıyken, ağ geçidi modu ise yalnızca 443 numaralı bağlantı noktasını kullandığından güvenlik duvarıyla daha kolay kullanılabilir.

![Azure Cosmos DB sıralı kayıt içeri aktarma gelişmiş seçeneklerinin ekran görüntüsü](./media/import-data/documentdbsequentialoptions.png)

> [!TIP]
> İçeri aktarma aracı, varsayılan olarak DirectTcp bağlantı moduna geçer. Güvenlik duvarıyla ilgili sorun yaşarsanız, Ağ Geçidi bağlantı moduna geçin; bu yalnızca 443 numaralı bağlantı noktasını gerektirir.

## <a id="IndexingPolicy"></a>Dizin oluşturma ilkesi belirtme

Geçiş aracının, içeri aktarma sırasında Azure Cosmos DB SQL API koleksiyonları oluşturmasına izin verdiğinizde koleksiyonların dizin oluşturma ilkesini belirtebilirsiniz. Azure Cosmos DB Sıralı kayıt seçenekleri ve Azure Cosmos DB Toplu içeri aktarmanın gelişmiş seçenekler bölümünde Dizin Oluşturma İlkesi bölümüne gidin.

![Azure Cosmos DB Dizin Oluşturma İlkesi gelişmiş seçeneklerinin ekran görüntüsü](./media/import-data/indexingpolicy1.png)

Dizin Oluşturma İlkesi gelişmiş seçeneğini kullanarak, bir dizin oluşturma ilkesi dosyası seçebilir, kendiniz bir dizin oluşturma ilkesi girebilir veya varsayılan şablonlar kümesi içinden seçim yapabilirsiniz (dizin oluşturma ilkesi metin kutusuna sağ tıklayarak).

Aracın sağladığı ilke şablonları şunlardır:

* Varsayılan. Bu ilke, dizelerde eşitlik sorguları gerçekleştirdiğinizde en iyi seçenektir. Ayrıca, sayılar için SıRALAMA ölçütü, Aralık ve eşitlik sorguları kullanıyorsanız da kullanılabilir. Bu ilkenin, Aralıktan daha düşük dizin depolama yükü vardır.
* Aralık. Bu ilke, hem sayılar hem de dizeler üzerinde SıRALAMA, Aralık ve eşitlik sorguları kullandığınızda en iyisidir. Bu ilkenin, Varsayılan veya Karmadan daha yüksek dizin depolama yükü vardır.

![Azure Cosmos DB Dizin Oluşturma İlkesi gelişmiş seçeneklerinin ekran görüntüsü](./media/import-data/indexingpolicy2.png)

> [!NOTE]
> Bir dizin oluşturma ilkesi belirtmezseniz, varsayılan ilke uygulanır. Dizin oluşturma ilkeleri hakkında daha fazla bilgi için bkz: [Azure Cosmos DB dizin oluşturma ilkeleri](index-policy.md).

## <a name="export-to-json-file"></a>JSON dosyasına dışarı aktarma

Azure Cosmos DB JSON dışarı aktarıcı, kullanılabilir kaynak seçeneklerinden herhangi birini JSON belgelerinin dizisine sahip bir JSON dosyasına vermenize olanak tanır. Araç, dışarı aktarmayı sizin için işler. Alternatif olarak, elde edilen geçiş komutunu görüntülemeyi ve komutu kendiniz çalıştırmayı tercih edebilirsiniz. Sonuçta elde edilen JSON dosyası, yerel olarak veya Azure Blob depolama alanında depolanabilir.

![Azure Cosmos DB JSON yerel dosya dışarı aktarma seçeneğinin ekran görüntüsü](./media/import-data/jsontarget.png)

![Azure Cosmos DB JSON Azure Blob depolama alanı dışarı aktarma seçeneğinin ekran görüntüsü](./media/import-data/jsontarget2.png)

İsteğe bağlı olarak, sonuçta elde edilen JSON 'ı önceden belirleyebilirsiniz. Bu eylem, içeriğin daha fazla okunabilir hale getirilmesi sırasında elde edilen belgenin boyutunu artırır.

* Standart JSON dışarı aktarma

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* Önceden bağlı JSON dışarı aktarma

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

Aşağıda, Azure Blob depolama alanından JSON dosyasını dışarı aktarmak için komut satırı örneği verilmiştir:

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>Gelişmiş yapılandırma

Gelişmiş yapılandırma ekranında, hataların yazılmasını istediğiniz günlük dosyasının konumunu belirtin. Bu sayfa için aşağıdaki kurallar geçerlidir:

1. Bir dosya adı sağlanmamışsa, tüm hatalar sonuçlar sayfasında döndürülür.
2. Bir dizin olmadan dosya adı sağlanırsa, geçerli ortam dizininde dosya oluşturulur (veya dosyanın üzerine yazılır).
3. Var olan bir dosyayı seçerseniz, dosyanın üzerine yazılır, hiçbir ekleme seçeneği yoktur.
4. Daha sonra tüm hata iletilerinin mi, kritik hata iletilerinin mi günlüğe kaydedileceğini yoksa hiçbir hata iletisinin günlüğe kaydedilmeyeceğini mi seçin. Son olarak, ekrandaki aktarım iletisinin ilerleme durumunun ne sıklıkla güncelleştirileceğine karar verin.

   ![Gelişmiş yapılandırma ekranının görüntüsü](./media/import-data/AdvancedConfiguration.png)

## <a name="confirm-import-settings-and-view-command-line"></a>İçeri aktarma ayarlarını Onayla ve komut satırını görüntüle

1. Kaynak bilgilerini, hedef bilgileri ve gelişmiş yapılandırmayı belirttikten sonra, geçiş özetini gözden geçirin ve isterseniz, elde edilen geçiş komutunu görüntüleyin veya kopyalayın. (Komutu kopyalamak, içeri aktarma işlemlerini otomatikleştirmek için yararlıdır.)

    ![Özet ekranının ekran görüntüsü](./media/import-data/summary.png)

    ![Özet ekranının ekran görüntüsü](./media/import-data/summarycommand.png)

2. Kaynak ve hedef seçeneklerden memnun kaldığınızda **İçeri Aktar**’a tıklayın. İçeri aktarma devam ettikçe, geçen süre, aktarılan sayı ve hata bilgileri (Gelişmiş yapılandırma bölümünde bir dosya adı sağlamadıysanız) güncelleştirilir. Tamamlandıktan sonra, sonuçları dışarı aktarabilirsiniz (örneğin, içeri aktarma hatalarıyla ilgilenmek için).

    ![Azure Cosmos DB JSON dışarı aktarma seçeneğinin ekran görüntüsü](./media/import-data/viewresults.png)

3. Ayrıca, tüm değerleri sıfırlayarak veya var olan ayarları tutarak yeni bir içeri aktarma işlemi başlatabilirsiniz. (Örneğin, bağlantı dizesi bilgilerini, kaynak ve hedef seçimi ve daha fazlasını tutmayı seçebilirsiniz.)

    ![Azure Cosmos DB JSON dışarı aktarma seçeneğinin ekran görüntüsü](./media/import-data/newimport.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdaki görevleri tamamladınız:

> [!div class="checklist"]
> * Veri Geçişi aracını yüklediniz
> * Farklı veri kaynaklarından verileri içeri aktardınız
> * Azure Cosmos DB’den JSON’a dışarı aktarma işlemi yaptınız

Şimdi, sonraki öğreticiye devam edebilir ve Azure Cosmos DB kullanarak verilerin nasıl sorgulanacağını öğrenebilirsiniz.

> [!div class="nextstepaction"]
>[Veriler nasıl sorgulanır?](../cosmos-db/tutorial-query-sql-api.md)
