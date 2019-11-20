---
title: Azure Cosmos DB'deki farklı API'ler hakkında sık sorulan sorular
description: Genel olarak dağıtılmış, çok modelli bir veritabanı hizmeti olan Azure Cosmos DB hakkında sık sorulan soruların yanıtlarını alın. Kapasite, performans düzeyleri ve ölçeklendirme hakkında bilgi edinin.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 15e4fc3ce412807908c75fe25acecac0fe86d261
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262804"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Azure Cosmos DB'deki farklı API'ler hakkında sık sorulan sorular

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB için tipik kullanım örnekleri nelerdir?

Azure Cosmos DB, otomatik ölçeklendirme, tahmin edilebilir performans, milisaniyelik yanıt sürelerinin hızlı sıralaması ve şema içermeyen verileri sorgulama yeteneği önemli olan yeni Web, mobil, oyun ve IoT uygulamaları için iyi bir seçimdir. Hızlı geliştirme ve uygulama veri modellerinin sürekli yinelemesini destekleme için kendisini Azure Cosmos DB. Kullanıcı tarafından oluşturulan içeriği ve verileri yöneten uygulamalar [Azure Cosmos DB için yaygın kullanım durumlardır](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB öngörülebilir performans sunar?

[İstek birimi](request-units.md) (ru), Azure Cosmos DB aktarım hızı ölçümüdür. 1 ru aktarım hızı, 1 KB 'lık bir belge ALMANıN aktarım hızına karşılık gelir. Okuma, yazma, SQL sorguları ve saklı yordam yürütmeleri dahil Azure Cosmos DB her işlem, işlemi gerçekleştirmek için gereken aktarım hızını temel alan belirleyici bir RU değeri içerir. CPU, GÇ ve bellek hakkında düşünce ve bunların her birinin uygulama aktarım hızını nasıl etkilediği hakkında düşünmek yerine, tek RU ölçüsünün koşullarını düşünebilirsiniz.

Her bir Azure Cosmos kapsayıcısını, saniye başına iş hacmi temelinde sağlanan aktarım hızı ile yapılandırabilirsiniz. Herhangi bir ölçekte uygulama için, tek tek istekleri kendi RU değerlerini ölçecek şekilde kıyasleyebilir ve tüm isteklerde toplam istek birimi sayısını işlemek üzere bir kapsayıcı sağlayabilirsiniz. Uygulamanızın ihtiyaçları geliştikçe kapsayıcının aktarım hızını da ölçeklendirebilir veya azaltabilirsiniz. İstek birimleri hakkında daha fazla bilgi edinmek ve kapsayıcı gereksinimlerinizi belirlemeye yönelik yardım almak için [üretilen iş Hesaplayıcısı](https://www.documentdb.com/capacityplanner)' nı deneyin.

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB, anahtar/değer, sütunlu, belge ve grafik gibi çeşitli veri modellerini nasıl destekler?

Anahtar/değer (tablo), sütunlu, belge ve grafik veri modelleri, Azure Cosmos DB oluşturulduğu ARS (alar, kayıtlar ve diziler) tasarımı nedeniyle yerel olarak desteklenir. Alar, kayıtlar ve diziler kolayca eşleştirilebilir ve çeşitli veri modelleriyle görüntülenebilir. Modellerin bir alt kümesine yönelik API 'Ler Şu anda kullanılabilir (SQL, MongoDB, tablo ve Gremlin) ve ek veri modellerine özgü olan diğerleri gelecekte kullanılabilir olacaktır.

Azure Cosmos DB, geliştiriciden herhangi bir şema veya ikincil dizin gerektirmeden tüm verileri otomatik olarak dizinleyen, şema belirsiz bir dizin oluşturma motoruna sahiptir. Motor, dizin ve sorgu işleme alt sistemlerine ait depolama düzenini kapsayan mantıksal Dizin düzenleri (ters, sütunlu, ağaç) kümesini temel alır. Cosmos DB Ayrıca, bir dizi hat protokolünü ve API 'yi genişletilebilir bir şekilde destekleyebilir ve bunları temel veri modeline (1) ve mantıksal Dizin düzenlerine (2), yerel olarak birden fazla veri modelini desteklemeye olanak sağlamak için verimli bir şekilde çevirebilir.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Verilerinize erişmek için birden çok API kullanabilir miyim?

Azure Cosmos DB, Microsoft 'un genel olarak dağıtılmış, çok modelli veritabanı hizmetidir. Çoklu modelin birden çok API 'yi ve birden çok veri modelini desteklediği Azure Cosmos DB, farklı API 'Ler depolama ve tel protokolü için farklı veri biçimleri kullanır. Örneğin, SQL JSON kullanıyorsa MongoDB, EDM kullanır, Cassandra, CQL kullanır ve Gremlin, boson kullanır. Sonuç olarak, belirli bir hesaptaki verilere tüm erişim için aynı API 'YI kullanmanızı öneririz.

Her API, birlikte çalışabilen Gremlin ve SQL API 'SI dışında bağımsız olarak çalışır.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>HIPAA Azure Cosmos DB uyumlu mı?

Evet, Azure Cosmos DB HIPAA uyumludur. HIPAA, bağımsız olarak tanımlanabilen sağlık bilgilerinin kullanımı, açıklanması ve korunması için gereksinimler belirler. Daha fazla bilgi için bkz. [Microsoft Güven Merkezi](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB depolama sınırları nelerdir?

Bir kapsayıcının Azure Cosmos DB içinde depolayabileceği toplam veri miktarına yönelik bir sınır yoktur.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB üretilen iş sınırları nelerdir?

Bir kapsayıcının Azure Cosmos DB ' de destekleyebileceğiniz toplam aktarım hızı miktarına yönelik bir sınır yoktur. Önemli fikir, çok fazla sayıda bölüm anahtarı arasında iş yükünüzü kabaca eşit olarak dağıtmaktır.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Doğrudan ve ağ geçidi bağlantı modları şifrelendi mi?

Evet her iki mod her zaman tamamen şifrelenir.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB maliyeti ne kadar sürer?

Ayrıntılar için [Azure Cosmos DB fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/cosmos-db/) sayfasına bakın. Azure Cosmos DB kullanım ücretleri, sağlanan kapsayıcıların sayısına, kapsayıcıların çevrimiçi olduğu saat sayısına ve her bir kapsayıcı için sağlanan aktarım hızına göre belirlenir.

### <a name="is-a-free-account-available"></a>Ücretsiz bir hesap var mı?

Evet, herhangi bir taahhütte hiçbir ücret ödemeden bir zaman sınırlı hesap için kaydolabilirsiniz. Kaydolmak için Azure Cosmos DB deneme [Azure Cosmos DB SSS](#try-cosmos-db)' de [ücretsiz deneyin](https://azure.microsoft.com/try/cosmosdb/) veya daha fazla bilgi edinin makalesini ziyaret edin.

Azure 'u yeni kullanıyorsanız, tüm Azure hizmetlerini denemek için size 30 gün ve kredi sağlayan [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/)için kaydolabilirsiniz. Visual Studio aboneliğiniz varsa, herhangi bir Azure hizmetinde kullanmak üzere [ücretsiz Azure kredileri](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) için de uygunsunuz demektir.

Ayrıca, Azure aboneliği oluşturmadan uygulamanızı ücretsiz olarak geliştirmek ve test etmek için [Azure Cosmos DB öykünücüsü](local-emulator.md) de kullanabilirsiniz. Uygulamanızın Azure Cosmos DB Öykünücüsü’ndeki performansından memnun olduğunuzda bulut üzerinde Azure Cosmos DB hesabı kullanmaya başlayabilirsiniz.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Azure Cosmos DB nasıl ek yardım alabilirim?

Teknik bir soru sormak için, bu iki sorudan birine ve yanıt forumlarından birine gönderebilirsiniz:

* [MSDN forumu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow, programlama sorularına en iyisidir. Sorunuzun [konuyla ilgili](https://stackoverflow.com/help/on-topic) olduğundan emin olun ve mümkün olduğunca [fazla ayrıntı sağlayın ve bu soruyu açık ve answerable](https://stackoverflow.com/help/how-to-ask)yapın.

Yeni özellikler istemek için [Kullanıcı sessiyle](https://feedback.azure.com/forums/263030-azure-cosmos-db)yeni bir istek oluşturun.

Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oluşturun.

Diğer sorular [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)' de ekibe gönderilebilir; Ancak bu bir teknik destek diğer adı değildir.

## <a id="try-cosmos-db"></a>Azure Cosmos DB abonelikleri deneyin

Artık abonelik olmadan, ücretsiz ve taahhütlere sahip bir zaman sınırlı Azure Cosmos DB deneyiminden yararlanabilirsiniz. Deneme Azure Cosmos DB aboneliğine kaydolmak için, [ücretsiz Azure Cosmos DB dene](https://azure.microsoft.com/try/cosmosdb/)' ye gidin. Bu abonelik [Azure Ücretsiz deneme](https://azure.microsoft.com/free/)sürümünden ayrıdır ve Azure Ücretsiz deneme sürümü veya Azure ücretli abonelikle birlikte kullanılabilir.

Deneme Azure Cosmos DB abonelikler, Kullanıcı KIMLIĞINIZLE ilişkili diğer diğer aboneliklerde Azure portal görüntülenir.

Azure Cosmos DB aboneliklerini denemek için aşağıdaki koşullar geçerlidir:

* SQL, Gremlin API ve tablo hesapları için abonelik başına bir [işleme sağlanan kapsayıcı](./set-throughput.md#set-throughput-on-a-container) .
* MongoDB hesapları için abonelik başına en fazla üç [işleme sağlanmış koleksiyon](./set-throughput.md#set-throughput-on-a-container) .
* Abonelik başına bir [üretilen iş veritabanı sağlandı](./set-throughput.md#set-throughput-on-a-database) . Üretilen iş veritabanları, içinde herhangi bir sayıda kapsayıcı içerebilir.
* 10 GB depolama kapasitesi.
* Genel çoğaltma şu [Azure bölgelerinde](https://azure.microsoft.com/regions/)kullanılabilir: Orta ABD, Kuzey Avrupa ve Güneydoğu Asya
* Kapsayıcı düzeyinde sağlandığınızda, en fazla 5 K RU/sn aktarım hızı.
* Veritabanı düzeyinde sağlanmak üzere en fazla 20 K RU/sn aktarım hızı.
* Abonelikler 30 gün sonra sona erer ve en fazla 31 günlük toplam olarak genişletilebilir.
* Deneme Azure Cosmos DB hesapları için Azure destek biletleri oluşturulamıyor; Ancak, mevcut destek planlarına sahip aboneler için destek sağlanır.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB ayarlama

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Azure Cosmos DB kaydolmak Nasıl yaparım??

Azure Cosmos DB Azure portal kullanılabilir. İlk olarak, bir Azure aboneliği için kaydolun. Kaydolduktan sonra Azure aboneliğinize bir Azure Cosmos DB hesabı ekleyebilirsiniz.

### <a name="what-is-a-master-key"></a>Ana anahtar nedir?

Ana anahtar, bir hesaptaki tüm kaynaklara erişmeyi sağlayan bir güvenlik belirtecidir. Anahtara sahip kişiler, veritabanı hesabındaki tüm kaynaklara okuma ve yazma erişimine sahiptir. Ana anahtarları dağıtırken dikkatli olun. Birincil ana anahtar ve ikincil ana anahtar [Azure Portal][azure-portal] **anahtarlar** dikey penceresinde kullanılabilir. Anahtarlar hakkında daha fazla bilgi için bkz. [Erişim tuşlarını görüntüleme, kopyalama ve yeniden oluşturma](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>PreferredLocations 'un ayarlayabilecekleri bölgeler nelerdir?

PreferredLocations değeri, Cosmos DB kullanılabildiği Azure bölgelerinden herhangi birine ayarlanabilir. Kullanılabilir bölgelerin listesi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Verileri dünya genelinde Azure veri merkezleri aracılığıyla dağıtırken dikkat etmeniz gereken bir şey var mı?

Azure Cosmos DB, [Azure bölgeleri](https://azure.microsoft.com/regions/) sayfasında belirtilen tüm Azure bölgelerinde bulunur. Çekirdek hizmet olduğundan, her yeni veri merkezinde Azure Cosmos DB bir varlık bulunur.

Bir bölge belirlediğinizde, Azure Cosmos DB bağımsız ve kamu bulutları olduğunu unutmayın. Diğer bir deyişle, bir [bağımsız bölgesinde](https://azure.microsoft.com/global-infrastructure/)bir hesap oluşturursanız, bu [evereign bölgesinin](https://azure.microsoft.com/global-infrastructure/)dışına çoğaltılamaz. Benzer şekilde, bir dış hesaptan diğer bağımsız konumlara çoğaltmayı etkinleştiremezsiniz.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Kapsayıcı düzeyi işleme sağlamasından veritabanı düzeyi işleme sağlama ' ya geçiş yapılabilir mi? Ya da tam tersi

Kapsayıcı ve veritabanı düzeyinde üretilen iş sağlama, ayrı tekliflerdir ve bunlardan biri arasında geçiş yaparak verilerin kaynaktan hedefe geçirilmesi gerekir. Bu, yeni bir veritabanı veya yeni bir kapsayıcı oluşturmanız ve ardından [toplu yürütücü kitaplığı](bulk-executor-overview.md) veya [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)kullanarak veri geçirmeniz gerektiği anlamına gelir.

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB, zaman serisi analizini destekliyor mu?

Evet Azure CosmosDB, zaman serisi analizini destekler, burada [zaman serisi deseninin](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns)bir örneği verilmiştir. Bu örnek, zaman serisi verileri üzerine toplanan görünümler oluşturmak için değişiklik akışını nasıl kullanacağınızı gösterir. Spark akışı veya başka bir akış verisi işlemcisi kullanarak bu yaklaşımı genişletebilirsiniz.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Azure Cosmos DB hizmet kotaları ve verimlilik limitleri nelerdir?

Daha fazla bilgi için bkz. [kapsayıcı ve veritabanı](set-throughput.md#comparison-of-models) makaleleri Azure Cosmos DB [hizmet kotaları](concepts-limits.md) ve sınırları genelinde.

## <a name="sql-api"></a>SQL API’si

### <a name="how-do-i-start-developing-against-the-sql-api"></a>SQL API 'sine karşı geliştirmeye Nasıl yaparım? misiniz?

Önce bir Azure aboneliğine kaydolmanız gerekir. Bir Azure aboneliğine kaydolduktan sonra Azure aboneliğinize bir SQL API kapsayıcısı ekleyebilirsiniz. Azure Cosmos DB hesap ekleme hakkında yönergeler için bkz. [Azure Cosmos veritabanı hesabı oluşturma](create-sql-api-dotnet.md#create-account).

.NET, Python, Node.js, JavaScript ve Java için [SDK'lar](sql-api-sdk-dotnet.md) kullanılabilir. Geliştiriciler ayrıca, çeşitli platformlardan ve dillerden Azure Cosmos DB kaynaklarla etkileşim kurmak için [Restilahttp API 'lerini](/rest/api/cosmos-db/) de kullanabilir.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Baş bir başlangıç yapmak için bazı kullanıma hazırlanmış örneklere erişebilir miyim?

GitHub 'da SQL API [.net](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node. js](sql-api-nodejs-samples.md)ve [Python](sql-api-python-samples.md) SDK 'ları örnekleri mevcuttur.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API veritabanı şemaya ücretsiz verileri destekliyor mu?

Evet, SQL API 'SI, uygulamaların şema tanımları veya ipuçları olmadan rastgele JSON belgelerini depolamasına izin verir. Veriler, Azure Cosmos DB SQL sorgu arabirimi aracılığıyla sorgu için hemen kullanılabilir.

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API 'SI ACID işlemlerini destekliyor mu?

Evet, SQL API JavaScript saklı yordamları ve Tetikleyicileri ifade eden belgeler arası işlemleri destekler. İşlemler, her kapsayıcıda tek bir bölümün kapsamına alınır ve ACID semantiğinin "tümü veya hiçbir şey" olarak, diğer eşzamanlı kod ve Kullanıcı isteklerinden yalıtılmış olarak yürütülmesi sağlanır. JavaScript uygulama kodunun sunucu tarafında yürütülmesi aracılığıyla özel durumlar oluşturulursa, tüm işlem geri alınır. 

### <a name="what-is-a-container"></a>Kapsayıcı nedir?

Kapsayıcı, bir dizi belge ve ilişkili JavaScript uygulama mantığının bir grubudur. Bir kapsayıcı, [maliyetin](performance-levels.md) verimlilik ve kullanılan depolama tarafından belirlendiği faturalandırılabilir bir varlıktır. Kapsayıcılar bir veya daha fazla bölüme veya sunucuya yayılabilir ve bu işlem, neredeyse sınırsız miktarda depolama alanı veya aktarım hızı işleyecek şekilde ölçeklendirebilir.

* SQL API 'SI için bir kapsayıcı bir kapsayıcıya eşlenir.
* MongoDB hesapları için Cosmos DB API 'SI için bir kapsayıcı bir koleksiyonla eşlenir.
* Cassandra ve Tablo API'si hesapları için bir kapsayıcı bir tabloyla eşlenir.
* Gremlin API hesapları için bir kapsayıcı bir grafikle eşlenir.

Kapsayıcılar Azure Cosmos DB için de faturalandırma varlıklarıdır. Her kapsayıcı, sağlanan aktarım hızı ve kullanılan depolama alanı temelinde saatlik olarak faturalandırılır. Daha fazla bilgi için bkz. [Azure Cosmos DB fiyatlandırması](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Veritabanı nasıl oluşturulur?

[Bir kapsayıcı ekleme](create-sql-api-java.md#add-a-container), [Azure Cosmos DB SDK 'Lardan](sql-api-sdk-dotnet.md)biri veya [REST API 'lerinden](/rest/api/cosmos-db/)açıklandığı gibi [Azure Portal](https://portal.azure.com)kullanarak veritabanları oluşturabilirsiniz.

### <a name="how-do-i-set-up-users-and-permissions"></a>Kullanıcıları ve izinleri nasıl ayarlarım?

[Cosmos DB API SDK 'lardan](sql-api-sdk-dotnet.md) birini veya [REST API 'lerini](/rest/api/cosmos-db/)kullanarak kullanıcılar ve izinler oluşturabilirsiniz.

### <a name="does-the-sql-api-support-sql"></a>SQL API 'SI SQL destekliyor mu?

SQL API hesapları tarafından desteklenen SQL sorgu dili, SQL Server tarafından desteklenen sorgu işlevselliğinin gelişmiş bir alt kümesidir. Azure Cosmos DB SQL sorgu dili, JavaScript tabanlı, Kullanıcı tanımlı işlevler (UDF 'ler) aracılığıyla zengin hiyerarşik ve ilişkisel işleçler ve genişletilebilirlik sağlar. JSON dilbilgisi, JSON belgelerinin, hem Azure Cosmos DB otomatik dizin oluşturma teknikleri hem de Azure Cosmos DB SQL sorgu diyalekti tarafından kullanılan etiketli düğümleri olan ağaç olarak modellenmesini sağlar. SQL dilbilgisi kullanımı hakkında bilgi için bkz. [SQL sorgu][query] makalesi.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API 'SI SQL toplama işlevlerini destekliyor mu?

SQL API 'SI, SQL dilbilgisi aracılığıyla `COUNT`, `MIN`, `MAX`, `AVG` ve `SUM` toplama işlevleri aracılığıyla herhangi bir ölçekte düşük gecikmeli toplamayı destekler. Daha fazla bilgi için bkz. [toplama işlevleri](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API 'SI eşzamanlılık nasıl sağlar?

SQL API 'SI, HTTP varlık etiketleri veya ETags aracılığıyla iyimser eşzamanlılık denetimini (OCC) destekler. Her SQL API kaynağı bir ETag öğesine sahiptir ve bir belge her güncelleştirildiği zaman sunucu üzerinde ETag ayarlanır. ETag üst bilgisi ve geçerli değer tüm yanıt iletilerine dahildir. ETags, sunucunun bir kaynağın güncelleştirilip güncelleştirilmediğini meyeceğine karar vermesini sağlamak için IF-Match üst bilgisiyle birlikte kullanılabilir. IF-Match değeri, denetlenecek ETag değeridir. ETag değeri sunucu ETag değeriyle eşleşiyorsa, kaynak güncellenir. ETag artık geçerli değilse, sunucu işlemi "HTTP 412 Önkoşul hatası" Yanıt koduyla reddeder. İstemci daha sonra kaynak için geçerli ETag değerini almak üzere kaynağı yeniden ekler. Ayrıca, ETags bir kaynağın tekrar al gerekip gerekmediğini belirleyebilmek için If-None-Match üst bilgisiyle birlikte kullanılabilir.

.NET 'te iyimser eşzamanlılık kullanmak için [Accesscondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) sınıfını kullanın. Bir .NET örneği için bkz. GitHub 'daki DocumentManagement örneğindeki [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) .

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>SQL API 'de işlem Nasıl yaparım? mı?

SQL API 'SI, JavaScript saklı yordamları ve tetikleyicileri aracılığıyla dil ile tümleşik işlemleri destekler. Betiklerin içindeki tüm veritabanı işlemleri anlık görüntü yalıtımı altında yürütülür. Tek bölümlü bir kapsayıcı ise, yürütme kapsayıcının kapsamına alınır. Kapsayıcı bölümlense, yürütme, kapsayıcıda aynı bölüm anahtarı değerine sahip olan belgelerle kapsamlandırılır. Belge sürümlerinin anlık görüntüsü (ETag'ler) ise işlem başlangıcında alınır ve yalnızca betik başarılı olursa uygulanır. JavaScript bir hata oluşturursa işlem geri alınır. Daha fazla bilgi için bkz. [Azure Cosmos DB Için sunucu tarafı JavaScript programlama](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Cosmos DB belgeleri toplu olarak nasıl ekleyebilirim?

Aşağıdaki yollarla Azure Cosmos DB belgeleri toplu olarak ekleyebilirsiniz:

* Toplu yürütücü [.NET kitaplığı kullanma](bulk-executor-dot-net.md) ve [toplu yürütücü Java Kitaplığı kullanma](bulk-executor-java.md) bölümünde açıklandığı gibi toplu yürütücü aracı
* [Azure Cosmos DB Için veritabanı geçiş aracında](import-data.md)açıklandığı gibi veri geçiş aracı.
* [Azure Cosmos DB Için sunucu tarafı JavaScript programlamasında](stored-procedures-triggers-udfs.md)açıklandığı gibi saklı yordamlar.

### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API 'SI kaynak bağlantısını önbelleğe alma işlemini destekliyor mu?

Evet, Azure Cosmos DB yeniden kullanılabilir bir hizmet olduğundan kaynak bağlantıları sabittir ve önbelleğe alınabilir. SQL API istemcileri, herhangi bir kaynak benzeri belge veya kapsayıcıya yönelik okuma için "If-None-Match" üst bilgisini belirtebilir ve ardından sunucu sürümü değiştirildikten sonra yerel kopyalarını güncelleştirebilir.

### <a name="is-a-local-instance-of-sql-api-available"></a>Yerel bir SQL API örneği mi var?

Evet. [Azure Cosmos DB öykünücüsü](local-emulator.md) Cosmos DB hizmeti için yüksek uygunlukta bir öykünme sağlar. JSON belgelerinin oluşturulması ve sorgulanmasına, koleksiyonları sağlamanıza ve ölçeklendirmeye, saklı yordamları ve Tetikleyicileri yürütmeye yönelik destek de dahil olmak üzere Azure Cosmos DB ile aynı işlevselliği destekler. Azure Cosmos DB öykünücüsünü kullanarak uygulamalar geliştirebilir ve test edebilir ve Azure Cosmos DB için bağlantı uç noktasında tek bir yapılandırma değişikliği yaparak bunları küresel ölçekte Azure 'a dağıtabilirsiniz.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Portalda veri Gezgini 'nden görüntülendiğinde bir belgedeki uzun kayan nokta değerleri neden yuvarlanır.

Bu, JavaScript 'in sınırlamasıdır. JavaScript, IEEE 754 ' de belirtildiği gibi çift duyarlıklı kayan nokta biçim numaraları kullanır ve-(2<sup>53</sup> -1) ile 2<sup>53</sup>-1 (yani, 9007199254740991) arasında tam olarak sayı tutabilir.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Nesne hiyerarşisinde izinlere izin veriliyor?

Kaynak belirteçlerine, kaynak belirteçleri ve alt öğeleri (belgeler, ekler gibi) kullanarak izinler oluşturulmasına izin verilir. Bu, veritabanında bir izin oluşturulmaya veya bir hesap düzeyinde şu anda izin verilmediği anlamına gelir.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API'si

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API nedir?

Azure Cosmos DB MongoDB için API 'SI, uygulamaların mevcut, topluluk tarafından desteklenen SDK 'Ları ve MongoDB için sürücüleri kullanarak yerel Azure Cosmos veritabanı altyapısına kolayca ve şeffaf bir şekilde iletişim kurmasına olanak tanıyan bir tel protokol uyumluluk katmanıdır. Geliştiriciler artık Azure Cosmos DB faydalanan uygulamalar oluşturmak için mevcut MongoDB araç zincirlerini ve yeteneklerini kullanabilir. Geliştiriciler, çok yöneticili çoğaltma, Otomatik Dizin oluşturma, yedekleme bakımı, mali olarak desteklenen hizmet düzeyi sözleşmeleri (SLA 'Lar) vb. genel dağıtımı içeren Azure Cosmos DB benzersiz yetilerinden yararlanır.

### <a name="how-do-i-connect-to-my-database"></a>Nasıl yaparım? veritabanıma bağlansın mı?

MongoDB için Azure Cosmos DB API 'SI ile Cosmos veritabanına bağlanmak için en hızlı yol, [Azure Portal](https://portal.azure.com). Hesabınıza gidin ve ardından sol gezinti menüsünde **hızlı başlangıç**' ye tıklayın. Hızlı başlangıç, veritabanınıza bağlanmak için kod parçacıkları almanın en iyi yoludur.

Azure Cosmos DB katı güvenlik gereksinimleri ve standartları uygular. Azure Cosmos DB hesapların SSL aracılığıyla kimlik doğrulaması ve güvenli iletişim olması gerekir, bu nedenle TLSv 1.2 kullandığınızdan emin olun.

Daha fazla bilgi için bkz. [MongoDB için Azure Cosmos DB API 'Siyle Cosmos veritabanınıza bağlanma](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Azure Cosmos DB API 'sini kullanırken ilgilenmem gereken ek hata kodları var mı?

Ortak MongoDB hata kodlarıyla birlikte Azure Cosmos DB MongoDB için API 'sinin kendi özel hata kodları vardır:

| Hata               | Kodlayın  | Açıklama  | Çözüm  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Tüketilen istek birimlerinin toplam sayısı, kapsayıcının sağlanan istek birimi oranından daha fazla ve kısıtlanmış. | Azure portal bir kapsayıcıya veya bir kapsayıcı kümesine atanmış üretilen işi ölçeklendirmeye veya yeniden denemeye dikkat edin. |
| ExceededMemoryLimit | 16501 | Çok kiracılı bir hizmet olarak, işlem istemcinin bellek işlem biriminden çıktı. | Daha kısıtlayıcı sorgu ölçütleri aracılığıyla işlemin kapsamını azaltın veya [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)destek ekibiyle iletişime geçin. <br><br>Örnek: <em> @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4DB. getCollection (' kullanıcılar '). Aggregate ([<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$match: {Name: "Andy"}}, <br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$sort: {Age:-1}}<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3]) </em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB için Simba sürücüsü Azure Cosmos DB MongoDB için API 'SI ile kullanılmak üzere destekleniyor mu?

Evet, MongoDB için Azure Cosmos DB API 'SI ile SIBA 'nın Mongo ODBC sürücüsünü kullanabilirsiniz

## <a id="table"></a>Tablo API’si

### <a name="how-can-i-use-the-table-api-offering"></a>Tablo API'si teklifini nasıl kullanabilirim?

Azure Cosmos DB Tablo API'si [Azure Portal][azure-portal]kullanılabilir. Önce bir Azure aboneliğine kaydolmanız gerekir. Kaydolduktan sonra Azure aboneliğinize bir Azure Cosmos DB Tablo API'si hesabı ekleyebilir ve sonra hesabınıza tablolar ekleyebilirsiniz.

[Azure Cosmos DB tablo API'si giriş](table-introduction.md)bölümünde desteklenen dilleri ve ilişkili hızlı başlangıç bilgilerini bulabilirsiniz.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Tablo API'si kullanmak için yeni bir SDK almam gerekiyor mu?

Hayır, mevcut depolama SDK 'Ları hala çalışıyor olmalıdır. Ancak, her zaman en iyi destek için en son SDK 'Ları ve birçok durumda üstün performans elde etmek önerilir. [Azure Cosmos DB giriş tablo API'si](table-introduction.md)kullanılabilir dillerin listesine bakın.

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Azure Tablo depolama davranışında Tablo API'si nerede aynı değil?

Kullanıcıların Azure Cosmos DB Tablo API'si tabloları oluşturmak isteyen Azure Tablo depolamadan gelen bazı davranış farklılıkları vardır:

* Azure Cosmos DB Tablo API'si, garantili bir performans sağlamak için ayrılmış bir kapasite modeli kullanır, ancak bu, kapasite kullanılmasa bile, bu, bir kapasite için bir süre için ödeme yaptığı anlamına gelir. Azure Tablo depolama ile yalnızca kullanılan kapasite için ödeme yapılır. Bu, Azure Tablo depolama alanı 10 saniyelik bir SLA sağlarken, Tablo API'si neden 99 ' luk bir 10.000 ve 15 MS yazma SLA 'Sı sunabileceği açıklanmasına yardımcı olur. Sonuç olarak, Tablo API'si tabloları, hatta herhangi bir istek olmadan boş tablolar bile, kapasiteyi, Azure Cosmos DB tarafından sunulan SLA 'da bunlara yönelik istekleri işlemek için kullanılabilir durumda olmasını sağlamak için maliyet paranız ile ücretlendirilir.
* Tablo API'si tarafından döndürülen sorgu sonuçları, Azure Tablo depolamada olduklarından bölüm anahtarı/satır anahtarı sırasında sıralanmaz.
* Satır anahtarları yalnızca 255 bayta kadar olabilir
* Toplu işlemler yalnızca 2 MB 'a kadar olabilir
* CORS Şu anda desteklenmiyor
* Azure Tablo depolama alanındaki tablo adları büyük/küçük harfe duyarlı değildir, ancak bunlar Azure Cosmos DB Tablo API'si
* İkili alanlar gibi Azure Cosmos DB kodlama bilgileri için bazı iç biçimler, şu anda benzer bir şekilde verimli değildir. Bu nedenle bu, veri boyutuyla ilgili beklenmeyen sınırlamalara neden olabilir. Örneğin, şu anda, kodlama verilerin boyutunu arttığı için, bir tablo varlığının tam bir Meg 'sini, ikili verileri depolamak için kullandık.
* ' ID ' varlık özelliği adı şu anda desteklenmiyor
* TableQuery TakeCount, 1000 ile sınırlı değildir

REST API, Azure Cosmos DB Tablo API'si tarafından desteklenmeyen birkaç uç nokta/sorgu seçeneği vardır:

| Rest yöntemleri | REST uç noktası/sorgu seçeneği | Belge URL 'Leri | Açıklama |
| ------------| ------------- | ---------- | ----------- |
| AL, KOY | /? ResType = service@comp = Özellikler| [Tablo hizmeti özelliklerini ayarlama](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) ve [Tablo hizmeti özelliklerini al](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Bu uç nokta CORS kuralları, depolama Analizi yapılandırması ve günlüğe kaydetme ayarlarını ayarlamak için kullanılır. CORS Şu anda desteklenmiyor ve analiz ve günlüğe kaydetme, Azure depolama tablolarından Azure Cosmos DB farklı şekilde işlendi |
| Seçenekler | / @ no__t-1tablo-kaynak-adı > | [Uçuş öncesi CORS tablo isteği](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Bu, Azure Cosmos DB Şu anda desteklemediği CORS 'nin bir parçasıdır. |
| GET | /? ResType = service@comp = stats | [Tablo hizmeti Istatistiklerini al](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Birincil ve ikincil sunucular arasında verilerin ne kadar hızlı çoğaltılmasının bilgisini sağlar. Çoğaltma, yazma işlemlerinin bir parçası olduğundan, bu Cosmos DB gerekli değildir. |
| AL, KOY | /MyTable? comp = ACL | [Tablo ACL 'Sini al](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) ve [tablo ACL 'sini ayarla](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Bu, paylaşılan erişim Imzalarını (SAS) yönetmek için kullanılan saklı erişim ilkelerini alır ve ayarlar. SAS desteklense de farklı şekilde ayarlanır ve yönetilir. |

Ayrıca, Azure Cosmos DB Tablo API'si yalnızca, ATOM değil JSON biçimini destekler.

Azure Cosmos DB, paylaşılan erişim Imzalarını (SAS) desteklese de, özellikle de yeni tablo oluşturma hakkı gibi yönetim işlemleriyle ilgili olan bazı ilkeler desteklenmez.

Özellikle .NET SDK için, Azure Cosmos DB Şu anda desteklemediği bazı sınıflar ve yöntemler vardır.

| Sınıf | Desteklenmeyen Yöntem |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | SetPermissions * |
|            | GetPermissions * |
| TableServiceContext | * (Bu sınıf kullanım dışı) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Bu farklılıklardan herhangi birinde projeniz için bir sorun varsa, [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) ' i arayın ve bize izin verin.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Nasıl yaparım? SDK veya hatalar hakkında geri bildirimde bulunmak mı istiyorsunuz?

Görüşlerinizi aşağıdaki yollarla paylaşabilirsiniz:

* [Kullanıcı sesi](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN forumu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow, programlama sorularına en iyisidir. Sorunuzun [konuyla ilgili](https://stackoverflow.com/help/on-topic) olduğundan emin olun ve mümkün olduğunca [fazla ayrıntı sağlayın ve bu soruyu açık ve answerable](https://stackoverflow.com/help/how-to-ask)yapın.

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Tablo API'si bağlanmak için kullanmam gereken bağlantı dizesi nedir?

Bağlantı dizesi:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Bağlantı dizesini Azure portal bağlantı dizesi sayfasından edinebilirsiniz.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Tablo API'si için .NET SDK 'daki istek seçeneklerinin yapılandırma ayarlarını geçersiz Nasıl yaparım?.

Bazı ayarlar CreateCloudTableClient yönteminde ve diğer bir deyişle istemci uygulamasındaki appSettings bölümündeki App. config aracılığıyla işlenir. Yapılandırma ayarları hakkında bilgi için bkz. [Azure Cosmos DB özellikleri](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Mevcut Azure Tablo depolama SDK 'larını kullanan müşteriler için herhangi bir değişiklik var mı?

Hiçbiri. Mevcut Azure Tablo depolama SDK 'larını kullanan mevcut veya yeni müşteriler için herhangi bir değişiklik yoktur.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Nasıl yaparım?, Tablo API'si birlikte kullanmak için Azure Cosmos DB depolanan tablo verilerini görüntüleyebilir mi?

Verilere gözatabilmeniz için Azure portal kullanabilirsiniz. Tablo API'si kodunu veya sonraki cevap içinde bahsedilen araçları da kullanabilirsiniz.

### <a name="which-tools-work-with-the-table-api"></a>Tablo API'si hangi araçlar çalışıyor?

[Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)kullanabilirsiniz.

Daha önce belirtilen biçimde bir bağlantı dizesi alma esnekliğine sahip araçlar yeni Tablo API'si destekleyebilir. [Azure Storage Istemci araçları](../storage/common/storage-explorers.md) sayfasında tablo araçları listesi sunulmaktadır.

### <a name="is-the-concurrency-on-operations-controlled"></a>Eşzamanlılık işlemleri denetleniyor mu?

Evet, iyimser eşzamanlılık ETag mekanizması kullanılarak sağlanır.

### <a name="is-the-odata-query-model-supported-for-entities"></a>OData sorgu modeli varlıklar için destekleniyor mu?

Evet, Tablo API'si OData sorgusunu ve LINQ sorgusunu destekler.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Azure Tablo depolama 'ya bağlanıp Azure Cosmos DB Tablo API'si aynı uygulamada yan yana bağlanabilir miyim?

Evet, her biri bağlantı dizesi aracılığıyla kendi URI 'sine işaret eden CloudTableClient 'ın iki ayrı örneğini oluşturarak bağlanabilirsiniz.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Mevcut bir Azure Tablo Depolama uygulamasını bu teklife geçirmek Nasıl yaparım? mı?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) ve [Azure Cosmos DB veri geçiş aracı](import-data.md) desteklenir.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Bu hizmet için depolama boyutunun nasıl genişlediği, örneğin *n* GB veri ile başladım ve verilerim zaman IÇINDE 1 TB 'a büyüyecektir?

Azure Cosmos DB, yatay ölçeklendirme kullanılarak sınırsız depolama sağlamak üzere tasarlanmıştır. Hizmet, depolama alanınızı izleyebilir ve etkili bir şekilde artırabilir.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Tablo API'si teklif Nasıl yaparım? İzmi?

İstekleri ve depolama kullanımını izlemek için Tablo API'si **ölçümleri** bölmesini kullanabilirsiniz.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Gerekli olan üretilen iş üretimini Nasıl yaparım? hesaplansın mı?

İşlemler için gereken tablo verimini hesaplamak için kapasite tahmin Aracı ' i kullanabilirsiniz. Daha fazla bilgi için bkz. [Tahmini Istek birimleri ve veri depolama](https://www.documentdb.com/capacityplanner). Genel olarak, varlığınızı JSON olarak gösterebilir ve işlemlerinizin numaralarını sağlayabilirsiniz.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Tablo API'si SDK 'sını öykünücü ile yerel olarak kullanabilir miyim?

Şimdilik hayır.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Mevcut Uygulamam Tablo API'si çalışabilir mi?

Evet, aynı API desteklenir.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Tablo API'si özelliklerini kullanmak istemiyorum, mevcut Azure Tablo depolama uygulamalarımı SDK 'ya geçirmem gerekir mi?

Hayır, mevcut Azure Tablo depolama varlıklarını herhangi bir kesinti olmadan oluşturabilir ve kullanabilirsiniz. Ancak Tablo API'si kullanmıyorsanız, Otomatik Dizin, ek tutarlılık seçeneği veya küresel dağıtım avantajlarından faydalanabilirsiniz.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Tablo API'si verileri Azure 'un birden fazla bölgesine çoğaltması Nasıl yaparım? eklensin mi?

Uygulamanız için uygun olan bölgeleri eklemek için Azure Cosmos DB portalının [Genel çoğaltma ayarlarını](tutorial-global-distribution-sql-api.md#portal) kullanabilirsiniz. Küresel olarak dağıtılmış bir uygulama geliştirmek için, düşük okuma gecikmesi sağlamak üzere, uygulamanızı PreferredLocation bilgilerini yerel bölgeye de eklemeniz gerekir.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Nasıl yaparım? Tablo API'si hesabın birincil yazma bölgesi değiştirilsin mi?

Bir bölge eklemek ve ardından gerekli bölgeye yük devretmek için Azure Cosmos DB genel çoğaltma portalı bölmesini kullanabilirsiniz. Yönergeler için bkz. [Multi-region Azure Cosmos DB hesaplarıyla geliştirme](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Verilerimi dağıtırken tercih edilen okuma bölgelerini düşük gecikme süresine göre yapılandırma Nasıl yaparım??

Yerel konumdan okumaya yardımcı olması için App. config dosyasındaki PreferredLocation anahtarını kullanın. Mevcut uygulamalar için, LocationMode değeri ayarlandıysa Tablo API'si bir hata oluşturur. Bu kodu kaldırın çünkü Tablo API'si App. config dosyasından bu bilgileri alır. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Tablo API'si tutarlılık düzeylerini nasıl düşünmem gerekir?

Azure Cosmos DB tutarlılık, kullanılabilirlik ve gecikme süresi arasında iyi bir fikir verir. Azure Cosmos DB, geliştiricilerin Tablo API'si beş tutarlılık düzeyi sunarak, tablo düzeyinde doğru tutarlılık modelini seçebilir ve verileri sorgularken tek tek istekler yapabilirsiniz. İstemci bağlandığı zaman, tutarlılık düzeyi belirtebilir. Bu düzeyi CreateCloudTableClient ' ın ' me düzeyi bağımsız değişkeni aracılığıyla değiştirebilirsiniz.

Tablo API'si, varsayılan olarak sınırlı bir tutarlılık tutarlılığı olan "kendi yazmalarınızı okuma" ile düşük gecikmeli okumalar sağlar. Daha fazla bilgi için bkz. [tutarlılık düzeyleri](consistency-levels.md).

Varsayılan olarak, Azure Tablo Depolaması bir bölge içinde güçlü tutarlılık ve ikincil konumlarda nihai tutarlılık sağlar.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Azure Cosmos DB Tablo API'si Azure Tablo depolamadan daha fazla tutarlılık düzeyi sunuyor mu?

Evet, Azure Cosmos DB dağıtılmış doğası hakkında daha fazla bilgi için bkz. [tutarlılık düzeyleri](consistency-levels.md). Tutarlılık düzeyleri için garantiler sağlandığından, bunları güvenle kullanabilirsiniz.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Genel dağıtım etkinleştirildiğinde verileri çoğaltmak ne kadar sürer?

Azure Cosmos DB, verileri yerel bölgede dursun olarak kaydeder ve verileri birkaç milisaniyeye göre hemen diğer bölgelere gönderir. Bu çoğaltma yalnızca veri merkezinin gidiş dönüş zamanına (RTT) bağımlıdır. Azure Cosmos DB küresel dağıtım özelliği hakkında daha fazla bilgi edinmek için bkz. [Azure Cosmos DB: Azure 'da genel olarak dağıtılmış bir veritabanı hizmeti](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Okuma isteği tutarlılığı düzeyi değiştirilebilir mi?

Azure Cosmos DB, tutarlılık düzeyini kapsayıcı düzeyinde (tabloda) ayarlayabilirsiniz. .NET SDK 'yı kullanarak, App. config dosyasında Tableme düzeyi anahtarı için değer sağlayarak düzeyi değiştirebilirsiniz. Olası değerler şunlardır: güçlü, sınırlanmış Eskime durumu, oturum, tutarlı ön ek ve nihai. Daha fazla bilgi için bkz. [Azure Cosmos dB ayarlanabilir veri tutarlılığı düzeyleri](consistency-levels.md). Önemli fikir, istek tutarlılığı düzeyini tablonun ayarından daha fazla ayarlayamıyoruz. Örneğin, son olarak tablo için tutarlılık düzeyini ve istek tutarlılığı düzeyini güçlü olarak ayarlayamazsınız.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Bir bölge aşağı gittiğinde Tablo API'si yük devretmeyi nasıl işler?

Tablo API'si, Azure Cosmos DB küresel olarak dağıtılmış platformundan yararlanır. Uygulamanızın veri merkezi kapalı kalma süresine yol açabildiğinden emin olmak için, Azure Cosmos DB portalında hesap için [çok bölgeli Azure Cosmos DB hesaplarıyla](high-availability.md)en az bir bölge daha etkinleştirin. Portalın önceliğini, [çok bölgeli Azure Cosmos DB hesapları Ile geliştirmeyi](high-availability.md)kullanarak belirleyebilirsiniz.

Hesap için istediğiniz kadar bölge ekleyebilir ve yük devretme önceliği sağlayarak hesaba yük devredebileceği yeri kontrol edebilirsiniz. Veritabanını kullanmak için, çok fazla bir uygulama sağlamanız gerekir. Bunu yaptığınızda, müşterileriniz kesinti yaşar. [En son .NET istemci SDK 'sı](table-sdk-dotnet.md) otomatik olarak barındırıldır, ancak diğer SDK 'lar değildir. Diğer bir deyişle, alt bölgeyi algılayabilir ve yeni bölgeye otomatik olarak yük devreder.

### <a name="is-the-table-api-enabled-for-backups"></a>Tablo API'si yedeklemeler için etkinleştirildi mi?

Evet, Tablo API'si yedeklemeler için Azure Cosmos DB platformundan yararlanır. Yedeklemeler otomatik olarak yapılır. Daha fazla bilgi için bkz. [Azure Cosmos DB çevrimiçi yedekleme ve geri yükleme](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Tablo API'si, varsayılan olarak bir varlığın tüm özniteliklerini dizinlidir mi?

Evet, bir varlığın tüm öznitelikleri varsayılan olarak dizinlenir. Daha fazla bilgi için bkz. [Azure Cosmos DB: Dizin oluşturma ilkeleri](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Bu, sorguları karşılamak için birden fazla dizin oluşturmak zorunda olmam anlamına geliyor mu?

Evet, Azure Cosmos DB Tablo API'si tüm özniteliklerin şema tanımı olmadan otomatik olarak dizinlemesini sağlar. Bu Otomasyon, geliştiricilerin dizin oluşturma ve yönetimi yerine uygulamaya odaklanmasını boşaltır. Daha fazla bilgi için bkz. [Azure Cosmos DB: Dizin oluşturma ilkeleri](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Dizin oluşturma ilkesini değiştirebilir miyim?

Evet, Dizin tanımını sağlayarak dizin oluşturma ilkesini değiştirebilirsiniz. Ayarları doğru bir şekilde kodlayıp kaçış yapmanız gerekir.

Non-.NET SDK 'Ları için, dizin oluşturma ilkesi yalnızca **Veri Gezgini**' de portalda ayarlanabilir, değiştirmek istediğiniz belirli tabloya gidebilir ve sonra **Ölçek & ayarları**-> Dizin oluşturma ilkesi ' ne gidebilir, istediğiniz değişikliği yapıp, sonra **tasarruf** edebilirsiniz .

.NET SDK 'dan App. config dosyasında gönderilebilir:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Platform olarak Azure Cosmos DB sıralama, toplamalar, hiyerarşi ve diğer işlevler gibi birçok özelliğe sahip olabilir. Bu özellikleri Tablo API'si ekleysin mi?

Tablo API'si, Azure Tablo depolama ile aynı sorgu işlevlerini sağlar. Azure Cosmos DB ayrıca sıralama, toplamalar, jeo-uzamsal sorgu, hiyerarşi ve çok çeşitli yerleşik işlevleri de destekler. Daha fazla bilgi için bkz. [SQL sorguları](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>Tablo API'si için tablo verimini ne zaman değiştirmem gerekir?

Aşağıdaki koşullardan biri geçerli olduğunda, Tableverimini değiştirmelisiniz:

* Veri ayıklama, dönüştürme ve yükleme (ETL) işlemi gerçekleştiriyor veya kısa sürede çok fazla veri yüklemek istiyorsunuz.
* Kapsayıcıda veya arka uçtaki bir kapsayıcı kümesinden daha fazla işleme ihtiyacınız vardır. Örneğin, kullanılan üretilen iş üretiminin, sağlanan aktarım hızına göre daha fazla olduğunu ve kısıtlamadığınızı görürsünüz. Daha fazla bilgi için bkz. [Azure Cosmos kapsayıcıları için aktarım hızını ayarlama](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Tablo API'si tablomın verimini büyütme veya azaltma yapabilir miyim?

Evet, üretilen işi ölçeklendirmek için Azure Cosmos DB portalının ölçek bölmesini kullanabilirsiniz. Daha fazla bilgi için bkz. [aktarım hızını ayarlama](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Yeni sağlanan tablolar için ayarlanan varsayılan bir Tableüretilen Iş mi?

Evet, App. config aracılığıyla Tableverimini geçersiz kılamazsınız ve Azure Cosmos DB önceden oluşturulmuş bir kapsayıcı kullanmıyorsanız, hizmet 400 verimini içeren bir tablo oluşturur.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Azure Tablo depolama hizmeti 'nin mevcut müşterileri için fiyatlandırma değişikliği var mı?

Hiçbiri. Mevcut Azure Tablo depolama müşterileri için fiyat değişikliği yoktur.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Tablo API'si fiyat nasıl hesaplanır?

Fiyat, ayrılan tablo Işleme göre değişir.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Tablo API'si teklifteki tablolarda sınırlama Nasıl yaparım? mi?

İstek oranı, temel alınan kapsayıcı veya bir kapsayıcı kümesi için sağlanan aktarım hızı kapasitesinden daha büyükse bir hata alırsınız ve SDK yeniden deneme ilkesini uygulayarak çağrıyı yeniden dener.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Azure Cosmos DB teklifinizin Tablo API'si avantajlarından yararlanmak için neden PartitionKey ve RowKey 'tan ayrı bir aktarım hızı seçmem gerekir?

Azure Cosmos DB, App. config dosyasında veya Portal aracılığıyla bir tane sağlamazsanız, kapsayıcınıza yönelik varsayılan bir aktarım hızı belirler.

Azure Cosmos DB, performans ve gecikme için, işlem üzerinde üst sınırlarla garanti sağlar. Bu güvence, altyapının kiracının işlemleri üzerinde idare zorlaması mümkün olduğunda mümkündür. Tablo aktarım hızını ayarlamak, platform bu kapasiteyi ayırdığından ve işletimsel başarıyı garanti ettiğinden garantili aktarım hızı ve gecikme süresi almanızı sağlar.

Verimlilik belirtimini kullanarak, uygulamanızın mevsimselliği avantajlarından yararlanabilir, verimlilik ihtiyaçlarını karşılayabilir ve maliyetleri tasarruf edebilirsiniz.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Yalnızca verileri depolamak için ödeme yapma ve nadiren sorgu oluşturmam için Azure Tablo depolama, benim için ucuzdur. Azure Cosmos DB Tablo API'si teklif, tek bir işlem gerçekleştirmeme ya da herhangi bir şeyi depoladığım halde bile bana ücretlendirme yapıyor gibi görünüyor. Şunları anlayabileceğiniz?

Azure Cosmos DB, kullanılabilirlik, gecikme süresi ve aktarım hızı garantisi olan küresel olarak dağıtılmış, SLA tabanlı bir sistem olacak şekilde tasarlanmıştır. Azure Cosmos DB aktarım hızını ayırdığınızda, diğer sistemlerin aktarım hızını farklı şekilde garanti edilir. Azure Cosmos DB, müşterilerin istediği ikincil dizinler ve küresel dağıtım gibi ek yetenekler sağlar.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Azure Tablo depolama alanına veri aldığımda, hiçbir zaman kota tam "bildirimi (bir bölümün dolu olduğunu gösterir). Tablo API'si, bu iletiyi alıyorum. Bu teklif beni sınırlıyor ve var olan uygulamamı değiştirmem zorunlu midir?

Azure Cosmos DB gecikme süresi, aktarım hızı, kullanılabilirlik ve tutarlılık garantisi sayesinde sınırsız ölçek sağlayan SLA tabanlı bir sistemdir. Garantili Premium performans sağlamak için, veri boyutlarınızın ve dizininizin yönetilebilir ve ölçeklenebilir olduğundan emin olun. Bölüm anahtarı başına varlık veya öğe sayısı için 10 GB 'lik sınır, harika arama ve sorgu performansı sağlamamız sağlamaktır. Azure depolama için bile uygulamanızın iyi ölçeklendirdiğinden emin olmak için, tüm bilgileri tek bir bölümde depolayarak ve sorgulayarak bir sıcak bölüm *oluşturmamalıdır* .

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Bu nedenle PartitionKey ve RowKey Tablo API'si hala gereklidir?

Evet. Tablo API'si yüzey alanı Azure Tablo depolama SDK 'sına benzer olduğundan, bölüm anahtarı verileri dağıtmak için etkili bir yol sağlar. Satır anahtarı bu bölüm içinde benzersizdir. Satır anahtarının mevcut olması gerekir ve standart SDK 'da olduğu gibi null olamaz. RowKey uzunluğu 255 bayttır ve PartitionKey uzunluğu 1 KB 'tır.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Tablo API'si yönelik hata iletileri nelerdir?

Azure Tablo depolama ve Azure Cosmos DB Tablo API'si aynı SDK 'Ları kullanarak hataların çoğu aynı olacaktır.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Tablo API'si bir diğerinden sonra çok sayıda tablo oluşturmaya çalıştığımda neden kısıtlıyorum?

Azure Cosmos DB gecikme süresi, aktarım hızı, kullanılabilirlik ve tutarlılık garantisi sağlayan SLA tabanlı bir sistemdir. Sağlanan bir sistem olduğundan, bu gereksinimleri güvence altına almak için kaynakları ayırır. Tablo oluşturma işlemi hızlı bir şekilde algılanır ve kısıtlanacaktır. Tablo oluşturma hızına bakmanız ve dakika başına 5 ' ten az bir değer vermek tavsiye ederiz. Tablo API'si sağlanan bir sistem olduğunu unutmayın. Bunu sağladığınız süre için ödeme yapmaya başlayabilirsiniz.

## <a name="gremlin-api"></a>Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>/.Net geliştirmesi için C#Microsoft. Azure. Graf paketini veya Gremlin.net mi kullanmalıyım?

Azure Cosmos DB Gremlin API 'SI, açık kaynaklı sürücülerden hizmete yönelik ana bağlayıcılar olarak yararlanır. Bu nedenle önerilen seçenek [Apache Tinkerpop tarafından desteklenen sürücüleri](https://tinkerpop.apache.org/)kullanmaktır.

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Grafik veritabanında sorgular çalıştırılırken RU/s nasıl ücretlendirilir?

Tüm Graph nesneleri, köşeleri ve kenarları, arka uçta JSON belgeleri olarak gösterilir. Bir Gremlin sorgusu tek seferde bir veya birden çok grafik nesnesini değiştirebileceğinizden, onunla ilişkili maliyet, sorgu tarafından işlenen nesnelerle, kenarlarla doğrudan ilgilidir. Bu, Azure Cosmos DB diğer tüm API 'Ler için kullanılan işlemdir. Daha fazla bilgi için [Azure Cosmos DB Istek birimleri](request-units.md)bölümüne bakın.

RU ücreti, sonuç kümesi değil, geçiş geçişinin çalışma verileri kümesine bağlıdır. Örneğin, bir sorgu bir sonuç olarak tek bir köşe elde etmek, ancak aynı şekilde birden fazla başka nesne arasında geçiş yapması gerekiyorsa, maliyet, bir sonuç izdüşümünü hesaplamak için gereken tüm grafik nesnelerine göre yapılır.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Grafik veritabanının Azure Cosmos DB Gremlin API 'de sahip olduğu en büyük ölçek nedir?

Azure Cosmos DB, depolama ve aktarım hızı gereksinimlerinde artışı otomatik olarak çözmek için [Yatay bölümleme](partition-data.md) kullanımını sağlar. Bir iş yükünün en yüksek aktarım hızı ve depolama kapasitesi, belirli bir kapsayıcı ile ilişkili bölüm sayısına göre belirlenir. Ancak, bir Gremlin API kapsayıcısı, uygun bir performans deneyimini ölçeklendirmek için belirli bir kılavuz kümesine sahiptir. Bölümlendirme ve en iyi uygulamalar hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB makalesinde bölümlendirme](partition-data.md) .

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Gremlin sürücülerini kullanarak ekleme saldırılarına karşı nasıl koruyabilirim?

Çoğu yerel Apache Tinkerpop Gremlin sürücüleri, sorgu yürütme için bir parametre sözlüğü sağlama seçeneğine izin verir. Bu, [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) Içinde ve [Gremlin-JavaScript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js)içinde nasıl yapılacağını gösteren bir örnektir.

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Neden "Gremlin sorgu derleme hatası: herhangi bir yöntem bulunamıyor" hatası alıyorum?

Gremlin API Azure Cosmos DB Gremlin Surface alanında tanımlanan işlevselliğin bir alt kümesini uygular. Desteklenen adımlar ve daha fazla bilgi için bkz. [Gremlin destek](gremlin-support.md) makalesi.

En iyi geçici çözüm, gerekli Gremlin adımlarını desteklenen işlevlerle yeniden yazmaktır, çünkü tüm Essential Gremlin adımları Azure Cosmos DB tarafından desteklenir.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Neden "WebSocketException: sunucu, ' 101 ' durum kodu beklenirken ' 200 ' durum kodunu döndürdü" hatasını alıyorum?

Yanlış uç nokta kullanılırken bu hata büyük olasılıkla oluşur. Bu hatayı üreten uç nokta aşağıdaki düzene sahiptir:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Bu, grafik veritabanınızın belge uç noktasıdır.  Kullanılacak doğru uç nokta, aşağıdaki biçime sahip Gremlin uç noktasıdır:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>"RequestRateIsTooLarge" hatasını neden alıyorum?

Bu hata, saniye başına ayrılan Istek birimlerinin sorguya sunacak kadar yeterli olmadığı anlamına gelir. Bu hata genellikle tüm köşeleri elde eden bir sorgu çalıştırdığınızda görülür:

```
// Query example:
g.V()
```

Bu sorgu, grafikteki tüm köşeleri almayı deneyecek. Bu nedenle, bu sorgunun ücreti, ru 'daki en az köşe sayısına eşit olacaktır. RU/s ayarı bu sorguyu ele almak için ayarlanmalıdır.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Gremlin sürücü bağlantılarım neden sonunda kesiliyor?

Bir WebSocket bağlantısı aracılığıyla Gremlin bağlantısı yapılır. WebSocket bağlantılarında gerçek zamanlı bir zaman yoksa, Azure Cosmos DB Gremlin API, boşta bağlantıları 30 dakika sonra işlem yapılmadan sonlandırır.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Yerel Gremlin sürücülerinde neden Fluent API çağrısı kullanmıyorum?

Akıcı API çağrıları henüz Azure Cosmos DB Gremlin API 'SI tarafından desteklenmiyor. Akıcı API çağrıları, şu anda Azure Cosmos DB Gremlin API tarafından desteklenmeyen, bytecode desteği olarak bilinen bir iç biçimlendirme özelliği gerektirir. Aynı nedenden dolayı, en son Gremlin-JavaScript sürücüsü de şu anda desteklenmiyor.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Gremlin sorgularının verimliliğini nasıl değerlendirebilirim?

**Executionprofile ()** önizleme adımı, sorgu yürütme planının analizini sağlamak için kullanılabilir. Bu adımın, aşağıdaki örnekte gösterildiği gibi herhangi bir Gremlin sorgusunun sonuna eklenmesi gerekir:

**Sorgu örneği**

```
g.V('mary').out('knows').executionProfile()
```

**Örnek çıkış**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Yukarıdaki profilin çıktısı, köşe nesneleri, uç nesneler ve çalışma verileri kümesinin boyutunu almak için ne kadar zaman harcandığını gösterir. Bu, Azure Cosmos DB sorguları için standart maliyet ölçümlerle ilgilidir.

## <a id="cassandra"></a>Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Azure Cosmso DB Cassandra API tarafından desteklenen protokol sürümü nedir? Diğer protokolleri desteklemeye yönelik bir plan var mı?

Azure Cosmos DB için Apache Cassandra API, bugün CQL sürüm 4 ' ü destekler. Diğer protokolleri destekleme hakkında geri bildiriminiz varsa, [Kullanıcı sesli geri bildirimleri](https://feedback.azure.com/forums/263030-azure-cosmos-db) aracılığıyla bize bilgi verin veya [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)' ye e-posta gönderin.

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Bir tablo gereksinimi için neden bir verimlilik seçiliyor?

Azure Cosmos DB,-portal veya CQL tablosunu oluşturduğunuz yere bağlı olarak Kapsayıcınız için varsayılan aktarım hızını belirler.
Azure Cosmos DB, performans ve gecikme için, işlem üzerinde üst sınırlarla garanti sağlar. Bu güvence, altyapının kiracının işlemleri üzerinde idare zorlaması mümkün olduğunda mümkündür. Verimlilik ayarı, platform bu kapasiteyi ayırdığından ve işlem başarısını garanti ettiğinden garantili aktarım hızı ve gecikme süresi almanızı sağlar.
Değişiklik verimini, uygulamanızın mevsimlerinden faydalanabilir şekilde esnek ve maliyetleri tasarrufu sağlayabilirsiniz.

Verimlilik kavramı [Azure Cosmos DB makalesinde Istek birimleri](request-units.md) bölümünde açıklanmaktadır. Bir tablo için üretilen iş, temel alınan fiziksel bölümlere eşit olarak dağıtılır.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>CQL aracılığıyla oluşturulduğu sırada tablo için varsayılan RU/sn nedir? What If değiştirmem gerekiyor mu?

Azure Cosmos DB, işleme sağlamak için bir para birimi olarak saniye başına istek birimi (RU/sn) kullanır. CQL aracılığıyla oluşturulan tablolarda 400 RU vardır. RU 'yi portaldan değiştirebilirsiniz.

CQL

```
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Aktarım hızı kullanıldığında ne olur?

Azure Cosmos DB, performans ve gecikme için, işlem üzerinde üst sınırlarla garanti sağlar. Bu güvence, altyapının kiracının işlemleri üzerinde idare zorlaması mümkün olduğunda mümkündür. Bu, platform bu kapasiteyi ayırdığından ve işlem başarısını güvence altına alarak, garantili aktarım hızını ve gecikme süresini almanızı sağlayan üretilen işi ayarlamaya göre mümkündür.
Bu kapasitenin üzerine gittiğinizde, kapasitenizin kullanıldığını belirten aşırı yüklenmiş hata iletisi alırsınız.
0x1001 aşırı yüklü: "Istek oranı büyük olduğu için istek işlenemiyor. Bu juncture 'de, hangi işlemlerin ve hacminin bu soruna neden olduğunu görmek önemlidir. Portalın ölçümleriyle sağlanan kapasiteyi kullanarak tüketilen kapasite hakkında bir fikir edinebilirsiniz. Daha sonra, tüm temeldeki bölümlerde kapasitenin neredeyse eşit olarak tüketildiğinden emin olmanız gerekir. Üretilen iş yükünün çoğunu tek bir bölüm kullanıyorsa, iş yükünü eğtiğini görürsünüz.

Verimlilik, bölümler arasında veya toplu olarak, iş hızının saat, gün ve yedi gün boyunca nasıl kullanıldığını gösterir. Daha fazla bilgi için bkz. [Azure Cosmos DB ölçümlerle izleme ve hata ayıklama](use-metrics.md).

Tanılama günlükleri [Azure Cosmos DB tanılama günlüğü](logging.md) makalesinde açıklanmaktadır.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Birincil anahtar Azure Cosmos DB bölüm anahtarı kavramıyla mi eşlenir?

Evet, bölüm anahtarı varlığı doğru konuma yerleştirmek için kullanılır. Azure Cosmos DB, fiziksel bir bölümde depolanan sağ mantıksal bölümü bulmak için kullanılır. Bölümleme kavramı, [bölüm ve ölçek Azure Cosmos DB](partition-data.md) makalesinde de açıklanacaktır. Burada önemli bir bölüm, bir mantıksal bölümün bugün 10 GB 'lik sınırın üzerinde geçmemelidir.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Bir bölümün dolu olduğunu belirten bir kota tam bildirimi aldığımda ne olur?

Azure Cosmos DB gecikme süresi, aktarım hızı, kullanılabilirlik ve tutarlılık garantisi sayesinde sınırsız ölçek sağlayan SLA tabanlı bir sistemdir. Bu sınırsız depolama, anahtar kavram olarak bölümlendirme kullanılarak verilerin yatay ölçeklenmesini temel alır. Bölümleme kavramı, [bölüm ve ölçek Azure Cosmos DB](partition-data.md) makalesinde de açıklanacaktır.

Her mantıksal bölüm için, bağlı olmanız gereken varlık sayısı veya öğe sayısı için 10 GB sınırı. Uygulamanızın iyi ölçeklendirdiğinden emin olmak için, tüm bilgileri tek bir bölümde depolayarak ve sorgulayarak bir sıcak bölüm *oluşturmamalıdır* . Bu hata yalnızca verilerinizin eğriltilmiş olması halinde gelebilir: Yani, bir bölüm anahtarı için çok fazla veriniz vardır (10 @ no__t-GB 'den fazla). Depolama portalını kullanarak verilerin dağıtımını bulabilirsiniz. Bu hatayı gidermenin yolu, tabloyu yeniden oluşturmak ve verilerin daha iyi dağıtımına izin veren parçalı bir birincil (bölüm anahtarı) seçmek.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Cassandra API, bağımsız bölüm anahtarları milyonlarca veya milyarlarca olan anahtar değer deposu olarak kullanmak mümkün midir?

Azure Cosmos DB, depolama alanını ölçeklendirerek sınırsız sayıda veri depolayabilirler. Bu işlem, aktarım hızını birbirinden bağımsızdır. Evet yalnızca Cassandra API, doğru birincil/bölüm anahtarını belirterek anahtar/değer depolamak ve almak için her zaman kullanabilirsiniz. Bu tek anahtarlar kendi mantıksal bölümlerini alır ve sorun olmadan en üst fiziksel bölüme sahip.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB Apache Cassandra API birden fazla tablo oluşturmak mümkün mü?

Evet, Apache Cassandra API birden fazla tablo oluşturmak mümkündür. Bu tabloların her biri, üretilen iş ve depolama için birim olarak değerlendirilir.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Art arda birden fazla tablo oluşturmak mümkün mü?

Azure Cosmos DB hem veri hem de denetim düzlemi etkinlikleri için kaynak tarafından yönetilen sistemdir. Koleksiyonlar, tablolar gibi kapsayıcılar, belirtilen üretilen iş kapasitesi için sağlanan çalışma zamanı varlıklarıdır. Bu kapsayıcıların hızlı bir şekilde art arda oluşturulması beklenen etkinlik ve kısıtlanıyor. Tabloları hemen bırakan/oluşturan testleriniz varsa, bunları boş bırakmayı deneyin.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Oluşturulabilecek en fazla tablo sayısı nedir?

Tablo sayısı üzerinde fiziksel sınır yoktur, çok sayıda tablonuz varsa [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ' e bir e-posta gönderin (Toplam sabit boyutun boyutu 10 TB 'den fazla olursa).

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Oluşturabileceğiniz anahtar alanı sayısı üst sınırı nedir?

Anahtar alanları sayısında, meta veri kapsayıcıları gibi fiziksel sınır yoktur, bazı nedenlerle çok sayıda keyspaces varsa [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ' e bir e-posta gönderin.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Normal tablodan başladıktan sonra çok fazla veri getirmek mümkün midir?

Depolama kapasitesi, daha fazla veri gönderme sırasında otomatik olarak yönetilir ve artar. Böylece, düğümleri yönetmeniz ve sağlamaktan çok daha fazla veri içe aktarabilir ve daha fazlasını yapabilirsiniz.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Azure Cosmos DB davranışının Apache Casssandra API 'sini yapılandırmak için YAML dosya ayarlarını sağlamak mümkün mü?

Azure Cosmos DB Apache Cassandra API bir platform hizmetidir. İşlemleri yürütmek için protokol düzeyinde uyumluluk sağlar. Yönetim, izleme ve yapılandırma karmaşıklığını ortadan kaldırır. Bir geliştirici/Kullanıcı olarak kullanılabilirlik, silinmiş pullar, anahtar önbelleği, satır önbelleği, Bloom filtresi ve diğer ayarlar hakkında endişelenmeniz gerekmez. Azure Cosmos DB Apache Cassandra API, yapılandırma ve yönetim ek yükü olmadan ihtiyaç duyduğunuz okuma ve yazma performansını sağlamaya odaklanır.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>, Düğüm ekleme/küme durumu/düğüm durumu komutlarının Azure Cosmos DB destek Apache Cassandra API.

Apache Cassandra API, kapasite planlaması yapan ve depolama bir Breeze & verimlilik için esneklik taleplerini yanıtlayan bir platform hizmetidir. Azure Cosmos DB, iş üretimini temin etmeniz gerekir. Daha sonra, düğümleri ekleme/silme veya yönetme hakkında endişelenmeden, gün içinde herhangi bir sayıda zaman kadar ölçeklendirebilir ve azaltabilirsiniz. Bu, düğüm, küme yönetim aracı 'nı da kullanmanız gerekmediğini gösterir.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Basit/ağ gibi anahtar uzayı oluşturma için çeşitli yapılandırma ayarlarına göre ne olur?

Azure Cosmos DB, kullanılabilirlik ve düşük gecikme süreleri açısından genel dağıtım sağlar. Çoğaltmaları veya diğer şeyleri ayarlamanıza gerek yoktur. Tüm yazma işlemleri, performans garantisi sağlarken yazdığınız her bölgede her zaman durmadan bir çekirdekte işlenir.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Bloom filtresi, önbelleğe alma, okuma onarma değişikliği, gc_grace, Compression memtable_flush_period ve daha fazlası gibi tablo meta verileri için çeşitli ayarlara göre ne olur?

Azure Cosmos DB, yapılandırma ayarlarından herhangi birine dokunmaya ve yanlışlıkla onları değiştirmeye gerek olmadan okuma/yazma ve işleme için performans sağlar.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Cassandra tablolarında yaşam süresi (TTL) destekleniyor mu?

Evet, TTL destekleniyor.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Düğüm durumunu, çoğaltma durumunu, GC 'yi ve işletim sistemi parametrelerini daha önce çeşitli araçlarla izlemek mümkün midir? Şu anda izlenmesi gereken şeyler nelerdir?

Azure Cosmos DB, altyapıyı yönetme ve izleme konusunda endişelenmenize yardımcı olan bir platform hizmetidir. Yalnızca Portal ölçümlerinde bulunan ve bu aktarım hızını artırma veya azaltma konusunda bilgi edinmek için mevcut olan aktarım hızına sahip olmanız gerekir.
[SLA 'ları](monitor-accounts.md)izleyin.
[Ölçümleri](use-metrics.md) kullanın [tanılama günlükleri](logging.md)kullanın.

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Azure Cosmos DB Apache Cassandra API hangi istemci SDK 'Ları çalışabilir?

Apache Cassandra SDK 'sının istemci programları için CQLv3 kullanan istemci sürücüleri kullanılmıştır. Kullandığınız başka sürücüleriniz varsa veya sorun yaşıyorsanız, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)' e posta gönderin.

### <a name="is-composite-partition-key-supported"></a>Bileşik bölüm anahtarı destekleniyor mu?

Evet, bileşik bölüm anahtarı oluşturmak için normal sözdizimini kullanabilirsiniz.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Veri yüklemesi için sstableloader kullanabilir miyim?

Hayır, sstableloader desteklenmez.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Şirket içi Apache Cassandra kümesi, Azure Cosmos DB Cassandra API ile eşleştirilmelidir mi?

Mevcut Azure Cosmos DB, işlem ek yükü olmadan bulut ortamı için iyileştirilmiş bir deneyim içerir. Eşleştirmeye ihtiyacınız varsa, senaryonuzun bir açıklama ile [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ' e posta gönderin. Şirket içi/farklı bulut Cassandra kümesini Cosomos DB 'nin Cassandra API eşleştirmeye yardımcı olmaya yönelik sunum konusunda çalışıyoruz.

### <a name="does-cassandra-api-provide-full-backups"></a>Cassandra API tam yedeklemeler sağlar mi?

Azure Cosmos DB, tüm API 'lerde bugün dört saat aralığında gerçekleştirilen iki ücretsiz tam yedekleme sağlar. Bu, bir yedekleme zamanlaması ve diğer şeyleri ayarlamanıza gerek kalmaz.
Bekletme ve sıklığı değiştirmek istiyorsanız, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) ' e bir e-posta gönderin veya bir destek talebi yükseltin. Yedekleme özelliği hakkında bilgi, [Azure Cosmos DB makalesinde otomatik çevrimiçi yedekleme ve geri yükleme](online-backup-and-restore.md) bölümünde verilmiştir.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Bir bölge aşağı gittiğinde Cassandra API hesabı yük devretmeyi nasıl işler?

Azure Cosmos DB, Azure Cosmos DB genel olarak dağıtılmış platformundan bulunamadığında Cassandra API. Uygulamanızın veri merkezi kapalı kalma süresine yol açabildiğinden emin olmak için, Azure Cosmos DB portalında hesap için [çok bölgeli Azure Cosmos DB hesaplarıyla](high-availability.md)en az bir bölge daha etkinleştirin. Portalın önceliğini, [çok bölgeli Azure Cosmos DB hesapları Ile geliştirmeyi](high-availability.md)kullanarak belirleyebilirsiniz.

Hesap için istediğiniz kadar bölge ekleyebilir ve yük devretme önceliği sağlayarak hesaba yük devredebileceği yeri kontrol edebilirsiniz. Veritabanını kullanmak için, çok fazla bir uygulama sağlamanız gerekir. Bunu yaptığınızda, müşterileriniz kesinti yaşar.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API, varsayılan olarak bir varlığın tüm özniteliklerini dizinlidir mi?

Cassandra API, belirli özniteliklerde seçmeli dizin oluşturmaya yardımcı olmak için Ikincil Dizin oluşturmayı desteklemeyi planlıyor. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Yeni Cassandra API SDK 'sını öykünücü ile yerel olarak kullanabilir miyim?

Evet bu desteklenir.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Platform olarak Azure Cosmos DB, değişiklik akışı ve diğer işlevleri gibi birçok özelliğe sahip olabilir. Bu yetenekler Cassandra API eklenecektir mi?

Apache Cassandra API, Apache Cassandra ile aynı CQL işlevlerini sağlar. Gelecekte çeşitli özellikleri desteklemeye yönelik bir göz atmak için planlıyoruz.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Normal Cassandra API özelliği bugün olarak çalışmıyor ve geri bildirim sağlanmıyor?

[Kullanıcı sesli geri bildirimi](https://feedback.azure.com/forums/263030-azure-cosmos-db)aracılığıyla geri bildirim sağlayın.

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
