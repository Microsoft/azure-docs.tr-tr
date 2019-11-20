---
title: Azure Cosmos DB hizmet kotaları
description: Farklı kaynak türlerindeki hizmet kotalarını ve varsayılan limitleri Azure Cosmos DB.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 45cf7b7d9383de467f72769465a3ad382fe9d589
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064040"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB hizmet kotaları

Bu makalede, Azure Cosmos DB farklı kaynaklara sunulan varsayılan kotalarla bir genel bakış sunulmaktadır.

## <a name="storage-and-throughput"></a>Depolama ve aktarım hızı

Aboneliğiniz kapsamında bir Azure Cosmos hesabı oluşturduktan sonra [veritabanları, kapsayıcılar ve öğeler oluşturarak](databases-containers-items.md)hesabınızdaki verileri yönetebilirsiniz. [İstek birimi (ru/s veya Rus)](request-units.md)bakımından bir kapsayıcı düzeyinde veya veritabanı düzeyinde üretilen iş sağlayabilirsiniz. Aşağıdaki tabloda kapsayıcı/veritabanı başına depolama ve işleme sınırları listelenmektedir.

| Kaynak | Varsayılan limit |
| --- | --- |
| Kapsayıcı başına en fazla ru ([adanmış aktarım hızı sağlanmış mod](databases-containers-items.md#azure-cosmos-containers)) | Varsayılan olarak 1.000.000. [Azure destek bileti](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) kaydederek bunu artırabilirsiniz |
| Veritabanı başına en fazla ru ([paylaşılan verimlilik sağlanmış mod](databases-containers-items.md#azure-cosmos-containers)) | Varsayılan olarak 1.000.000. [Azure destek bileti](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) kaydederek bunu artırabilirsiniz |
| Maksimum ru/(mantıksal) bölüm anahtarı | 10,000 |
| Tüm öğelerin tamamında en fazla depolama alanı (mantıksal) bölüm anahtarı| 10 GB |
| En fazla farklı (mantıksal) bölüm anahtarı sayısı | İş çalışma zamanında |
| Kapsayıcı başına en fazla depolama alanı | İş çalışma zamanında |
| Veritabanı başına en fazla depolama alanı | İş çalışma zamanında |
| Hesap başına en fazla ek boyutu (ek özelliği amorti edilir) | 2 GB | 

> [!NOTE]
> Depolama veya işleme için daha fazla sınır gerektiren bölüm anahtarlarına sahip iş yüklerini yönetmeye yönelik en iyi yöntemler hakkında bilgi edinmek için bkz. [yapay bir bölüm anahtarı oluşturma](synthetic-partition-keys.md).
>

Cosmos kapsayıcısının (veya paylaşılan üretilen iş veritabanı) en az 400 ru işleme sahip olması gerekir. Kapsayıcı büyüdükçe, desteklenen en düşük aktarım hızı da aşağıdaki etkenlere bağlıdır:

* Bir kapsayıcıda ayarlayabileceğiniz en düşük aktarım hızı, kapsayıcıda sağlanan en yüksek aktarım hızına bağlıdır. Örneğin, üretilen iş hacmi 10000 ru olarak yükseldiğinde, olası en düşük sağlanan aktarım hızı 1000 RUs olur.
* Paylaşılan bir üretilen iş veritabanında en düşük aktarım hızı, her zaman bir paylaşılan verimlilik veritabanında oluşturduğunuz ve kapsayıcı başına 100 ru ile ölçülen toplam kapsayıcı sayısına bağlıdır. Örneğin, paylaşılan bir üretilen iş veritabanı içinde beş kapsayıcı oluşturduysanız verimlilik en az 500 RUs olmalıdır

Kapsayıcının veya bir veritabanının geçerli ve en düşük aktarım hızı Azure portal veya SDK 'lardan alınabilir. Daha fazla bilgi için bkz. [kapsayıcılar ve veritabanları üzerinde üretilen Iş sağlama](set-throughput.md). 

> [!NOTE]
> Bazı durumlarda, aktarım hızını %10 ' dan küçük bir süre düşürübiliyor olabilirsiniz. Kapsayıcı başına en az ru 'yı tam olarak almak için API 'YI kullanın.
>

Özet bölümünde, sağlanan en düşük RU sınırları aşağıda verilmiştir. 

| Kaynak | Varsayılan limit |
| --- | --- |
| Kapsayıcı başına en az ru ([özel üretilen iş işleme modu](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Veritabanı başına en az ru ([paylaşılan verimlilik sağlanmış mod](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Paylaşılan bir üretilen iş veritabanı içinde kapsayıcı başına en az ru | 100 |

Cosmos DB, SDK veya Portal aracılığıyla kapsayıcı veya veritabanı başına esnek işleme (ru) boyutunu destekler. Her kapsayıcı, en düşük ve en yüksek değerler arasında zaman uyumlu olarak ve 10 ila 100 kez bir ölçek aralığı içinde ölçeklendirebilir. İstenen üretilen iş değeri aralığın dışındaysa, ölçekleme zaman uyumsuz olarak gerçekleştirilir. Zaman uyumsuz ölçeklendirmenin, kapsayıcıda istenen işleme ve veri depolama boyutuna bağlı olarak tamamlanması dakika sürebilir.  

## <a name="control-plane-operations"></a>Denetim düzlemi işlemleri

Azure portal, Azure PowerShell, Azure CLı ve Azure Resource Manager şablonlarını kullanarak [Azure Cosmos hesabınızı temin edebilir ve yönetebilirsiniz](how-to-manage-database-account.md) . Aşağıdaki tabloda abonelik, hesap ve işlem sayısına göre sınırlar listelenmektedir.

| Kaynak | Varsayılan limit |
| --- | --- |
| Abonelik başına en fazla veritabanı hesabı | Varsayılan olarak 50. [Azure destek bileti](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) kaydederek bunu artırabilirsiniz|
| En fazla bölgesel yük devretme sayısı | Varsayılan olarak 1/saat. [Azure destek bileti](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) kaydederek bunu artırabilirsiniz|

> [!NOTE]
> Bölgesel yük devretme işlemleri yalnızca tek bölgeye yazma hesapları için geçerlidir. Çok bölgeli yazma hesapları, yazma bölgesinin değiştirilmesini gerektirmez veya hiçbir sınıra sahip olmaz.

Cosmos DB, düzenli aralıklarla verilerinizin yedeklerini otomatik olarak alır. Yedekleme bekletme aralıkları ve Windows hakkında daha fazla bilgi için, bkz. [Azure Cosmos DB çevrimiçi yedekleme ve isteğe bağlı veri yükleme](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Hesap başına sınırlar

| Kaynak | Varsayılan limit |
| --- | --- |
| En fazla veritabanı sayısı | İş çalışma zamanında |
| Veritabanı başına en fazla kapsayıcı sayısı (veya hesap) | İş çalışma zamanında |
| En fazla bölge sayısı | Sınır yok (tüm Azure bölgeleri) |

## <a name="per-container-limits"></a>Kapsayıcı başına sınırlar

Kullandığınız API 'ye bağlı olarak bir Azure Cosmos kapsayıcısı bir koleksiyon, tablo veya grafik temsil edebilir. Kapsayıcılar [benzersiz anahtar kısıtlamaları](unique-keys.md), [saklı yordamlar, Tetikleyiciler ve UDF](stored-procedures-triggers-udfs.md)'ler ve [Dizin oluşturma ilkesi](how-to-manage-indexing-policy.md)için konfigürasyonları destekler. Aşağıdaki tabloda bir kapsayıcı içindeki yapılandırmalara özgü sınırlar listelenmektedir. 

| Kaynak | Varsayılan limit |
| --- | --- |
| En fazla veritabanı veya kapsayıcı adı uzunluğu | 255 |
| Kapsayıcı başına en fazla saklı yordam | 100 <sup>*</sup>|
| Kapsayıcı başına en fazla UDF | 25 <sup>*</sup>|
| Dizin oluşturma ilkesindeki en fazla yol sayısı| 100 <sup>*</sup>|
| Kapsayıcı başına en fazla benzersiz anahtar sayısı|10 <sup>*</sup>|
| Benzersiz anahtar kısıtlaması başına en fazla yol sayısı|16 <sup>*</sup>|

<sup>*</sup> Azure desteği ile iletişime geçerek bu kapsayıcı başına limitlerin herhangi birini artırabilirsiniz.

## <a name="per-item-limits"></a>Öğe başına sınırlar

Kullandığınız API 'ye bağlı olarak, bir Azure Cosmos öğesi koleksiyondaki bir belgeyi, tablodaki bir satırı veya bir grafikteki bir düğümü ya da kenarı temsil edebilir. Aşağıdaki tabloda, Cosmos DB her öğe için sınır gösterilmektedir. 

| Kaynak | Varsayılan limit |
| --- | --- |
| En büyük öğe boyutu | 2 MB (UTF-8 JSON gösteriminin uzunluğu) |
| Bölüm anahtarı değerinin uzunluk üst sınırı | 2048 bayt |
| Maksimum kimlik değeri uzunluğu | 1024 bayt |
| Öğe başına en fazla özellik sayısı | Pratik sınır yok |
| En fazla iç içe geçme derinliği | Pratik sınır yok |
| Özellik adının uzunluk üst sınırı | Pratik sınır yok |
| En fazla özellik değeri uzunluğu | Pratik sınır yok |
| En fazla dize özelliği değeri uzunluğu | Pratik sınır yok |
| Maksimum sayısal Özellik değeri uzunluğu | IEEE754 çift duyarlıklı 64 bit |

Bölüm anahtarı ve kimlik değerleri için uzunluk kısıtlamaları ve 2 MB Toplam boyut kısıtlaması dışında, öğe yükleri üzerinde özellik sayısı ve iç içe geçme derinliği gibi kısıtlamalar yoktur. RU tüketimini azaltmak için büyük veya karmaşık öğe yapılarına sahip kapsayıcılar için dizin oluşturma İlkesi yapılandırmanız gerekebilir. Cosmos DB, gerçek dünyada bir örnek için bkz. [modelleme öğeleri](how-to-model-partition-example.md) ve büyük öğeleri yönetmek için desenler.

## <a name="per-request-limits"></a>İstek başına sınırlar

Cosmos DB kapsayıcılar, öğeler ve veritabanları gibi kaynaklara karşı [CRUD ve sorgu işlemlerini](https://docs.microsoft.com/rest/api/cosmos-db/) destekler.  

| Kaynak | Varsayılan limit |
| --- | --- |
| Tek bir işlem için en fazla yürütme süresi (saklı yordam yürütme veya tek bir sorgu sayfası alma gibi)| 5 sn |
| En fazla istek boyutu (saklı yordam, CRUD)| 2 MB |
| En büyük yanıt boyutu (örneğin, sayfalandırılmış sorgu) | 4 MB |

Sorgu gibi bir işlem, yürütme zaman aşımı veya Yanıt boyut sınırına ulaştığında, yürütmeyi sürdürmek için istemciye bir sonuç ve devamlılık belirteci döndürür. Tek bir sorgunun sayfalar/devamlılıklar arasında çalışması için pratik bir sınır yoktur.

Cosmos DB yetkilendirme için HMAC kullanır. Kapsayıcılar, bölüm anahtarları veya öğeler gibi kaynaklara yönelik ayrıntılı erişim denetimi için bir ana anahtar ya da [kaynak belirteçleri](secure-access-to-data.md) kullanabilirsiniz. Aşağıdaki tabloda Cosmos DB yetkilendirme belirteçleri için sınırlar listelenmektedir.

| Kaynak | Varsayılan limit |
| --- | --- |
| En büyük ana belirteç süre sonu zamanı | 15 dk  |
| En düşük kaynak belirteci süre sonu zamanı | 10 dakika  |
| En fazla kaynak belirteci süre sonu zamanı | Varsayılan olarak 24 saat. [Azure destek bileti](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) kaydederek bunu artırabilirsiniz|
| Belirteç yetkilendirme için maksimum saat eğriltme| 15 dk |

Cosmos DB, yazma işlemleri sırasında tetikleyicilerin yürütülmesini destekler. Hizmet, her yazma işlemi için en fazla bir ön tetikleyici ve bir tetikleme tetiklemeyi destekler. 

## <a name="sql-query-limits"></a>SQL sorgu sınırları

Cosmos DB, [SQL](how-to-sql-query.md)kullanılarak öğelerin sorgulanmasını destekler. Aşağıdaki tabloda sorgu deyimlerinde, örnek tümce sayısı veya sorgu uzunluğu gibi kısıtlamalar açıklanmaktadır.

| Kaynak | Varsayılan limit |
| --- | --- |
| Maksimum SQL sorgusu uzunluğu| 256 KB <sup>*</sup>|
| Sorgu başına en fazla birleşim| 5 <sup>*</sup>|
| Sorgu başına maksimum and| 2000 <sup>*</sup>|
| Sorgu başına en fazla ORs| 2000 <sup>*</sup>|
| Sorgu başına en fazla UDF| 10 <sup>*</sup>|
| İfade başına maksimum bağımsız değişken| 6000 <sup>*</sup>|
| Çokgen başına en fazla punto| 4096 <sup>*</sup>|

<sup>*</sup> Azure desteğiyle iletişim kurarak bu SQL sorgu limitlerinin herhangi birini artırabilirsiniz.

## <a name="mongodb-api-specific-limits"></a>MongoDB API 'sine özgü sınırlar

Cosmos DB, MongoDB 'ye karşı yazılan uygulamalar için MongoDB tel protokolünü destekler. Desteklenen komut ve protokol sürümlerini [desteklenen MongoDB özellikleri ve söz diziminde](mongodb-feature-support.md)bulabilirsiniz.

Aşağıdaki tabloda MongoDB Özellik desteğine özgü sınırlar listelenmektedir. SQL (çekirdek) API 'SI için bahsedilen diğer hizmet limitleri de MongoDB API 'SI için de geçerlidir.

| Kaynak | Varsayılan limit |
| --- | --- |
| Maksimum MongoDB sorgu belleği boyutu | 40 MB |
| MongoDB işlemleri için maksimum yürütme süresi| 30 saniye |

## <a name="try-cosmos-db-free-limits"></a>Cosmos DB ücretsiz sınırları deneyin

Aşağıdaki tabloda, [Try Azure Cosmos DB ücretsiz deneme için](https://azure.microsoft.com/try/cosmosdb/) sınırlar listelenmektedir.

| Kaynak | Varsayılan limit |
| --- | --- |
| Deneme süresi | 30 gün (herhangi bir sayıda yenilenebilir) |
| Abonelik başına en fazla kapsayıcı (SQL, Gremlin, Tablo API'si) | 1 |
| Abonelik başına en fazla kapsayıcı (MongoDB API) | 3 |
| Kapsayıcı başına en fazla aktarım hızı | 5000 |
| Paylaşılan işleme veritabanı başına en fazla aktarım hızı | 20000 |
| Hesap başına en fazla toplam depolama alanı | 10 GB |

Cosmos DB, genel dağıtımı yalnızca Orta ABD, Kuzey Avrupa ve Güneydoğu Asya bölgelerinde destekler. Deneme Azure Cosmos DB hesapları için Azure destek biletleri oluşturulamıyor. Ancak, mevcut destek planlarına sahip aboneler için destek sağlanır.

## <a name="next-steps"></a>Sonraki adımlar

Cosmos DB temel kavramları [genel dağıtım](distribute-data-globally.md) ve [bölümlendirme](partitioning-overview.md) ve [sağlanan aktarım hızı](request-units.md)hakkında daha fazla bilgi edinin.

Dört hızlı başlangıçtan biriyle Azure Cosmos DB kullanmaya başlayın:

* [Azure Cosmos DB SQL API’yi kullanmaya başlama](create-sql-api-dotnet.md)
* [MongoDB için Azure Cosmos DB API 'SI ile çalışmaya başlama](create-mongodb-nodejs.md)
* [Azure Cosmos DB Cassandra API’yi kullanmaya başlama](create-cassandra-dotnet.md)
* [Azure Cosmos DB Graph API’yi kullanmaya başlama](create-graph-dotnet.md)
* [Azure Cosmos DB Tablo API’yi kullanmaya başlama](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB’yi ücretsiz deneyin](https://azure.microsoft.com/try/cosmosdb/)
