---
title: Azure Cosmos DB 'de farklı API 'Ler hakkında sık sorulan sorular
description: Genel olarak dağıtılmış, çok modelli bir veritabanı hizmeti olan Azure Cosmos DB hakkında sık sorulan soruların yanıtlarını alın. Kapasite, performans düzeyleri ve ölçeklendirme hakkında bilgi edinin.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 81c959467c4bb3700120917cf150773fb18e866d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359838"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Azure Cosmos DB'deki farklı API'ler hakkında sık sorulan sorular
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB için tipik kullanım örnekleri nelerdir?

Azure Cosmos DB, otomatik ölçeklendirme, tahmin edilebilir performans, milisaniyelik yanıt sürelerinin hızlı sıralaması ve şema içermeyen verileri sorgulama yeteneği önemli olan yeni Web, mobil, oyun ve IoT uygulamaları için iyi bir seçimdir. Hızlı geliştirme ve uygulama veri modellerinin sürekli yinelemesini destekleme için kendisini Azure Cosmos DB. Kullanıcı tarafından oluşturulan içeriği ve verileri yöneten uygulamalar [Azure Cosmos DB için yaygın kullanım durumlardır](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB öngörülebilir performans sunar?

[İstek birimi](request-units.md) (ru), Azure Cosmos DB aktarım hızı ölçümüdür. 1 ru aktarım hızı, 1 KB 'lık bir belge ALMANıN aktarım hızına karşılık gelir. Okuma, yazma, SQL sorguları ve saklı yordam yürütmeleri dahil Azure Cosmos DB her işlem, işlemi gerçekleştirmek için gereken aktarım hızını temel alan belirleyici bir RU değeri içerir. CPU, GÇ ve bellek hakkında düşünce ve bunların her birinin uygulama aktarım hızını nasıl etkilediği hakkında düşünmek yerine, tek RU ölçüsünün koşullarını düşünebilirsiniz.

Her bir Azure Cosmos kapsayıcısını, saniye başına iş hacmi temelinde sağlanan aktarım hızı ile yapılandırabilirsiniz. Herhangi bir ölçekte uygulama için, tek tek istekleri kendi RU değerlerini ölçecek şekilde kıyasleyebilir ve tüm isteklerde toplam istek birimi sayısını işlemek üzere bir kapsayıcı sağlayabilirsiniz. Uygulamanızın ihtiyaçları geliştikçe kapsayıcının aktarım hızını da ölçeklendirebilir veya azaltabilirsiniz. İstek birimleri hakkında daha fazla bilgi edinmek ve kapsayıcı gereksinimlerinizi belirlemeye yönelik yardım almak için [üretilen iş Hesaplayıcısı](https://www.documentdb.com/capacityplanner)' nı deneyin.

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB, anahtar/değer, sütunlu, belge ve grafik gibi çeşitli veri modellerini nasıl destekler?

Anahtar/değer (tablo), sütunlu, belge ve grafik veri modelleri, Azure Cosmos DB oluşturulduğu ARS (alar, kayıtlar ve diziler) tasarımı nedeniyle yerel olarak desteklenir. Alar, kayıtlar ve diziler kolayca eşleştirilebilir ve çeşitli veri modelleriyle görüntülenebilir. Modellerin bir alt kümesine yönelik API 'Ler Şu anda kullanılabilir (SQL, MongoDB, tablo ve Gremlin) ve ek veri modellerine özgü olan diğerleri gelecekte kullanılabilir olacaktır.

Azure Cosmos DB, geliştiriciden herhangi bir şema veya ikincil dizin gerektirmeden tüm verileri otomatik olarak dizinleyen, şema belirsiz bir dizin oluşturma motoruna sahiptir. Motor, dizin ve sorgu işleme alt sistemlerine ait depolama düzenini kapsayan mantıksal Dizin düzenleri (ters, sütunlu, ağaç) kümesini temel alır. Cosmos DB Ayrıca, bir dizi hat protokolünü ve API 'yi genişletilebilir bir şekilde destekleyebilir ve bunları temel veri modeline (1) ve mantıksal Dizin düzenlerine (2), yerel olarak birden fazla veri modelini desteklemeye olanak sağlamak için verimli bir şekilde çevirebilir.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Verilerinize erişmek için birden çok API kullanabilir miyim?

Azure Cosmos DB, Microsoft'un sunduğu genel olarak dağıtılmış çok modelli bir veritabanı hizmetidir. Çoklu modelin birden çok API 'yi ve birden çok veri modelini desteklediği Azure Cosmos DB, farklı API 'Ler depolama ve tel protokolü için farklı veri biçimleri kullanır. Örneğin, SQL JSON kullanıyorsa MongoDB, EDM kullanır, Cassandra, CQL kullanır ve Gremlin JSON biçimini kullanır. Sonuç olarak, belirli bir hesaptaki verilere tüm erişim için aynı API 'YI kullanmanızı öneririz.

Her API, birlikte çalışabilen Gremlin ve SQL API 'SI dışında bağımsız olarak çalışır.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>HIPAA Azure Cosmos DB uyumlu mı?

Evet, Azure Cosmos DB HIPAA uyumludur. HIPAA, bağımsız olarak tanımlanabilen sağlık bilgilerinin kullanımı, açıklanması ve korunması için gereksinimler belirler. Daha fazla bilgi için bkz. [Microsoft Güven Merkezi](/compliance/regulatory/offering-hipaa-hitech).

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

* [Soru sayfası Microsoft Q&](/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow, programlama sorularına en iyisidir. Sorunuzun [konuyla ilgili](https://stackoverflow.com/help/on-topic) olduğundan emin olun ve mümkün olduğunca [fazla ayrıntı sağlayın ve bu soruyu açık ve answerable](https://stackoverflow.com/help/how-to-ask)yapın.

Yeni özellikler istemek için [Kullanıcı sessiyle](https://feedback.azure.com/forums/263030-azure-cosmos-db)yeni bir istek oluşturun.

Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oluşturun.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Azure Cosmos DB abonelikleri deneyin

Artık abonelik olmadan, ücretsiz ve taahhütlere sahip bir zaman sınırlı Azure Cosmos DB deneyiminden yararlanabilirsiniz. Deneme Azure Cosmos DB aboneliğine kaydolmak için, [Azure Cosmos DB dene](https://azure.microsoft.com/try/cosmosdb/) ' ye gidin ve kişisel MICROSOFT HESABı (MSA) kullanın. Bu abonelik [Azure Ücretsiz deneme](https://azure.microsoft.com/free/)sürümünden ayrıdır ve Azure Ücretsiz deneme sürümü veya Azure ücretli abonelikle birlikte kullanılabilir.

Deneme Azure Cosmos DB abonelikler, Kullanıcı KIMLIĞINIZLE ilişkili diğer diğer aboneliklerde Azure portal görüntülenir.

Azure Cosmos DB aboneliklerini denemek için aşağıdaki koşullar geçerlidir:

* Hesap erişimi, kişisel Microsoft hesaplarına (MSA) verilebilir. Şirket Azure AD kiracılarına ait Azure Active Directory (Azure AD) hesaplarını veya hesaplarını kullanmaktan kaçının, erişim izni engelleyebilecek kısıtlamalar olabilir.
* SQL, Gremlin API ve tablo hesapları için abonelik başına bir [işleme sağlanan kapsayıcı](./set-throughput.md#set-throughput-on-a-container) .
* MongoDB hesapları için abonelik başına en fazla üç [işleme sağlanmış koleksiyon](./set-throughput.md#set-throughput-on-a-container) .
* Abonelik başına bir [üretilen iş veritabanı sağlandı](./set-throughput.md#set-throughput-on-a-database) . Üretilen iş veritabanları, içinde herhangi bir sayıda kapsayıcı içerebilir.
* 10 GB depolama kapasitesi.
* Genel çoğaltma şu [Azure bölgelerinde](https://azure.microsoft.com/regions/)kullanılabilir: Orta ABD, Kuzey Avrupa ve Güneydoğu Asya
* Kapsayıcı düzeyinde sağlandığınızda, en fazla 5 K RU/sn aktarım hızı.
* Veritabanı düzeyinde sağlanmak üzere en fazla 20 K RU/sn aktarım hızı.
* Abonelikler 30 gün sonra sona erer ve en fazla 31 günlük toplam olarak genişletilebilir. Süre dolduktan sonra, içerilen bilgiler silinir.
* Deneme Azure Cosmos DB hesapları için Azure destek biletleri oluşturulamıyor; Ancak, mevcut destek planlarına sahip aboneler için destek sağlanır.

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB ayarlama

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Azure Cosmos DB kaydolmak Nasıl yaparım??

Azure Cosmos DB Azure portal kullanılabilir. İlk olarak, bir Azure aboneliği için kaydolun. Kaydolduktan sonra Azure aboneliğinize bir Azure Cosmos DB hesabı ekleyebilirsiniz.

### <a name="what-is-a-primary-key"></a>Birincil anahtar nedir?

Birincil anahtar, bir hesabın tüm kaynaklarına erişmek için bir güvenlik belirtecidir. Anahtara sahip kişiler, veritabanı hesabındaki tüm kaynaklara okuma ve yazma erişimine sahiptir. Birincil anahtarları dağıtırken dikkatli olun. Birincil birincil anahtar ve ikincil birincil anahtar [Azure Portal][azure-portal] **anahtarlar** dikey penceresinde kullanılabilir. Anahtarlar hakkında daha fazla bilgi için bkz. [Erişim tuşlarını görüntüleme, kopyalama ve yeniden oluşturma](manage-with-cli.md#list-account-keys).

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

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>SQL API hakkında sık sorulan sorular

### <a name="how-do-i-start-developing-against-the-sql-api"></a>SQL API 'sine karşı geliştirmeye Nasıl yaparım? misiniz?

Önce bir Azure aboneliğine kaydolmanız gerekir. Bir Azure aboneliğine kaydolduktan sonra Azure aboneliğinize bir SQL API kapsayıcısı ekleyebilirsiniz. Azure Cosmos DB hesap ekleme hakkında yönergeler için bkz. [Azure Cosmos veritabanı hesabı oluşturma](create-sql-api-dotnet.md#create-account).

.NET, Python, Node.js, JavaScript ve Java için [SDK'lar](sql-api-sdk-dotnet.md) kullanılabilir. Geliştiriciler ayrıca, çeşitli platformlardan ve dillerden Azure Cosmos DB kaynaklarla etkileşim kurmak için [Restilahttp API 'lerini](/rest/api/cosmos-db/) de kullanabilir.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Baş bir başlangıç yapmak için bazı kullanıma hazırlanmış örneklere erişebilir miyim?

SQL API [.net](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md)ve [Python](sql-api-python-samples.md) SDK örnekleri GitHub ' da kullanılabilir.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API veritabanı şemaya ücretsiz verileri destekliyor mu?

Evet, SQL API 'SI, uygulamaların şema tanımları veya ipuçları olmadan rastgele JSON belgelerini depolamasına izin verir. Veriler, Azure Cosmos DB SQL sorgu arabirimi aracılığıyla sorgu için hemen kullanılabilir.

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API 'SI ACID işlemlerini destekliyor mu?

Evet, SQL API JavaScript saklı yordamları ve Tetikleyicileri ifade eden belgeler arası işlemleri destekler. İşlemler, her kapsayıcıda tek bir bölümün kapsamına alınır ve ACID semantiğinin "tümü veya hiçbir şey" olarak, diğer eşzamanlı kod ve Kullanıcı isteklerinden yalıtılmış olarak yürütülmesi sağlanır. JavaScript uygulama kodunun sunucu tarafında yürütülmesi aracılığıyla özel durumlar oluşturulursa, tüm işlem geri alınır. 

### <a name="what-is-a-container"></a>Kapsayıcı nedir?

Kapsayıcı, bir dizi belge ve ilişkili JavaScript uygulama mantığının bir grubudur. Bir kapsayıcı, [maliyetin](performance-levels.md) verimlilik ve kullanılan depolama tarafından belirlendiği faturalandırılabilir bir varlıktır. Kapsayıcılar bir veya daha fazla bölüme veya sunucuya yayılabilir ve bu işlem, neredeyse sınırsız miktarda depolama alanı veya aktarım hızı işleyecek şekilde ölçeklendirebilir.

* SQL API 'SI için kaynağa kapsayıcı adı verilir.
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

SQL API,,,,, `COUNT` `MIN` `MAX` `AVG` ve `SUM` SQL dilbilgisi aracılığıyla herhangi bir ölçekte düşük gecikmeli toplamayı destekler. Daha fazla bilgi için bkz. [toplama işlevleri](sql-query-aggregate-functions.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API 'SI eşzamanlılık nasıl sağlar?

SQL API 'SI, HTTP varlık etiketleri veya ETags aracılığıyla iyimser eşzamanlılık denetimini (OCC) destekler. Her SQL API kaynağı bir ETag öğesine sahiptir ve bir belge her güncelleştirildiği zaman sunucu üzerinde ETag ayarlanır. ETag üst bilgisi ve geçerli değer tüm yanıt iletilerine dahildir. ETags, sunucunun bir kaynağın güncelleştirilip güncelleştirilmediğini meyeceğine karar vermesini sağlamak için If-Match üstbilgisiyle birlikte kullanılabilir. If-Match değeri, denetlenecek ETag değeridir. ETag değeri sunucu ETag değeriyle eşleşiyorsa, kaynak güncellenir. ETag artık geçerli değilse, sunucu işlemi "HTTP 412 Önkoşul hatası" Yanıt koduyla reddeder. İstemci daha sonra kaynak için geçerli ETag değerini almak üzere kaynağı yeniden ekler. Ayrıca, ETags bir kaynağın tekrar al gerekip gerekmediğini belirleyebilmek için If-None-Match üst bilgisiyle birlikte kullanılabilir.

.NET 'te iyimser eşzamanlılık kullanmak için [Accesscondition](/dotnet/api/microsoft.azure.documents.client.accesscondition) sınıfını kullanın. .NET örneği için GitHub 'daki DocumentManagement örneğinde [program. cs](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/DocumentManagement/Program.cs) bölümüne bakın.

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

Bu, JavaScript ile ilgili bir sınırlamadır. JavaScript, IEEE 754 ' de belirtildiği gibi çift duyarlıklı kayan nokta biçim numaraları kullanır ve-(2<sup>53</sup> -1) ile 2<sup>53</sup>-1 (yani, 9007199254740991) arasında tam olarak sayı tutabilir.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Nesne hiyerarşisinde izinlere izin veriliyor?

Kaynak belirteçlerine, kaynak belirteçleri ve alt öğeleri (belgeler, ekler gibi) kullanarak izinler oluşturulmasına izin verilir. Bu, veritabanında bir izin oluşturulmaya veya bir hesap düzeyinde şu anda izin verilmediği anlamına gelir.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Sonraki adımlar

Diğer API 'lerde sıkça sorulan sorular hakkında bilgi edinmek için bkz.:

* [MongoDB için Azure Cosmos DB API 'si](mongodb-api-faq.md) hakkında sık sorulan sorular
* [Azure Cosmos DB 'de Gremlin API](gremlin-api-faq.md) hakkında sık sorulan sorular
* [Azure Cosmos DB Cassandra API](cassandra-faq.md) hakkında sık sorulan sorular
* [Azure Cosmos DB tablo API'si](table-api-faq.md) hakkında sık sorulan sorular