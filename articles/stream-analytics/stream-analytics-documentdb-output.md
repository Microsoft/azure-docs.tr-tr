---
title: Cosmos DB için Azure Stream Analytics çıkışı
description: Bu makalede, çıkış veri arşivleme ve düşük gecikme süreli sorgular yapılandırılmamış JSON verileri için bir JSON çıkışı için Azure Cosmos DB'ye kaydetmek için Azure Stream Analytics kullanmayı açıklar.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062022"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB için Azure Stream Analytics çıkışı  
Stream Analytics hedefleyebilir [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) yapılandırılmamış JSON verileri üzerinde veri arşivleme ve düşük gecikme süreli sorgular için JSON çıkışında, etkinleştirme. Bu belge, bu yapılandırmayı uygulamak için bazı en iyi uygulamaları kapsar.

Kişiler için Cosmos DB ile bilginiz, göz atın [Azure Cosmos DB'nin öğrenme yolu](https://azure.microsoft.com/documentation/learning-paths/documentdb/) kullanmaya başlamak için. 

> [!Note]
> Şu anda Azure Stream Analytics'i kullanarak Azure Cosmos DB bağlantı yalnızca destekler **SQL API**.
> Diğer Azure Cosmos DB API henüz desteklenmiyor. Noktası Azure Stream Analytics Azure Cosmos DB hesaplarına diğer API'lerle oluşturduysanız, verileri düzgün bir şekilde depolanabilir değil. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Bir çıkış hedefi olarak Cosmos DB ile ilgili temel bilgiler
Stream Analytics Azure Cosmos DB çıktısı, akış işleme sonuçlarının Cosmos DB kapsayıcılarınıza JSON çıktısı olarak yazılmasına izin verebilir. Stream Analytics veritabanınızda kapsayıcı oluşturmaz, bunun yerine kendilerini daha önce oluşturmanızı gerektirir. Bu, Cosmos DB kapsayıcılarının faturalandırma maliyetlerinin sizin tarafınızdan denetlenmesi ve böylece [Cosmos DB API 'leri](https://msdn.microsoft.com/library/azure/dn781481.aspx)kullanarak kapsayıcılarınızın performansını, tutarlılığını ve kapasitesini doğrudan ayarlamanıza olanak sağlamak için kullanılır.

> [!Note]
> Azure Cosmos DB güvenlik duvarınızdan izin verilen IP 'Ler listesine 0.0.0.0 eklemeniz gerekir.

Cosmos DB kapsayıcı seçeneklerinden bazıları aşağıda ayrıntılı olarak verilmiştir.

## <a name="tune-consistency-availability-and-latency"></a>Tutarlılık, kullanılabilirlik ve gecikme süresini ayarlama
Uygulama gereksinimlerinizi karşılamak için, Azure Cosmos DB veritabanı ve kapsayıcıları ince ayarlamanıza ve tutarlılık, kullanılabilirlik, gecikme süresi ve aktarım hızı arasında denge sağlamanıza olanak tanır. Okuma tutarlılık düzeylerini bağlı olarak senaryo gereksinimlerinize yönelik Okuma ve yazma gecikme süresi, tutarlılık düzeyi veritabanı hesabınızı seçin. Aktarım hızı, kapsayıcıda Istek birimleri (ru) ölçeklenerek artırılabilir. Ayrıca, Azure Cosmos DB, kapsayıcılarınızın her bir CRUD işleminde zaman uyumlu dizin oluşturmayı sağlar. Azure Cosmos DB'de yazma/okuma performans denetlemek için kullanışlı başka bir seçenek budur. Daha fazla bilgi için gözden [, veritabanı ve sorgu tutarlılık düzeylerini değiştirme](../cosmos-db/consistency-levels.md) makalesi.

## <a name="upserts-from-stream-analytics"></a>Stream Analytics'ten alınan upsert eder
Azure Cosmos DB tümleştirmesi Stream Analytics, belirli bir belge KIMLIĞI sütununa göre kapsayıcınıza kayıt eklemenize veya güncelleştirmenize olanak tanır. Bu da verilir bir *Upsert*.

Stream Analytics ile belge kimliği çakışması ekleme başarısız olduğunda burada güncelleştirmeleri yalnızca işiniz bir iyimser upsert yaklaşımı kullanır. Uyumluluk düzeyi 1,0 ile, bu güncelleştirme bir düzeltme eki olarak gerçekleştirilir. bu nedenle belgede Kısmi güncelleştirme yapılmasını, diğer bir deyişle, yeni özelliklerin eklenmesi veya mevcut bir özelliğin değiştirilmesi artımlı olarak gerçekleştirilir. Ancak, değişiklikler, JSON belge sonucu üzerine tüm dizi diğer bir deyişle, dizi içinde dizi özelliklerin değerlerini değil birleştirilir. 1,2 ile, büyük bir davranış belgeyi eklemek veya değiştirmek için değiştirilir. Bu, aşağıdaki uyumluluk düzeyi 1,2 bölümünde daha ayrıntılı olarak açıklanmıştır.

Gelen JSON belgesini alan otomatik olarak Cosmos DB belge kimliği sütunu olarak kullanılır ve herhangi bir sonraki yazma, bu nedenle, bunlardan biri için önde gelen işlenir varolan bir kimliği alanı, varsa:
- eklemek için benzersiz bir kimlik sağlama
- Yinelenen kimlikleri ve 'ID' için ayarlanmış ' Belge Kimliği' upsert müşteri adayları
- Yinelenen kimlikleri ve 'Belge Kimliği' değil kümesi müşteri adayları hata için ilk belge sonra

Kaydetmek istiyorsanız <i>tüm</i> yinelenen bir Kimliğe sahip olanlar da dahil olmak üzere Belge Kimliği alanı sorgunuzda (AS anahtar sözcüğü ile) yeniden adlandırın ve Kimliği alanı oluşturun veya başka bir sütunun değeri ile Kimliğini değiştirin Cosmos DB sağlar (AS anahtar sözcüğü kullanılarak veya 'Belge Kimliği' ayarı kullanarak).

## <a name="data-partitioning-in-cosmos-db"></a>Cosmos DB'de bölümleme verileri
Azure Cosmos DB [sınırsız](../cosmos-db/partition-data.md) kapsayıcı, verilerinizi bölümlemek için önerilen yaklaşımdır ve Azure Cosmos DB iş yükünüze göre bölümleri otomatik olarak ölçeklendirirken. Sınırsız kapsayıcılara yazarken Stream Analytics önceki sorgu adımı veya giriş bölümleme düzeni olarak çok sayıda paralel yazıcı kullanır.
> [!NOTE]
> Şu anda Azure Stream Analytics yalnızca en üst düzeyde bölüm anahtarlarıyla sınırsız kapsayıcıları destekler. Örneğin, `/region` desteklenir. İç içe bölüm anahtarları (örneğin `/region/name`) desteklenmez. 

Bölüm anahtarınız seçiminize bağlı olarak şu _uyarıyı_alabilirsiniz:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Farklı değerlere sahip bir bölüm anahtarı özelliği seçmek ve iş yükünüzü bu değerler arasında eşit olarak dağıtmanızı sağlar. Doğal bir bölümlendirme yapıtı olarak, aynı bölüm anahtarını içeren istekler tek bir bölümün en yüksek aktarım hızı ile sınırlıdır. Ayrıca, aynı bölüm anahtarına ait belgelerin depolama boyutu 10 GB ile sınırlıdır. İdeal bölüm anahtarı, sorgularda filtre olarak sık görülen ve çözümünüzün ölçeklenebilir olduğundan emin olmak için yeterli kardinalite olan bir bölümdür.

Bir bölüm anahtarı, DocumentDB 'nin saklı yordamları ve tetikleyicilerinde bulunan işlemler için de sınır olur. İşlem içinde birlikte oluşan belgelerin aynı bölüm anahtarı değerini paylaşması için bölüm anahtarını seçmeniz gerekir. Cosmos DB bölümünde [bulunan](../cosmos-db/partitioning-overview.md) makale, bölüm anahtarı seçme hakkında daha fazla ayrıntı sağlar.

Sabit Azure Cosmos DB kapsayıcıları için Stream Analytics, tamamen ölçeklendirilmesi veya bu kadar bir kez daha fazla duruma getirilebilecek şekilde izin vermez. 10 GB ve 10.000 RU/sn aktarım hızı üst sınır sahiptirler.  Sınırsız bir kapsayıcıya (örneğin, bir en az 1.000 RU/sn ve bölüm anahtarı) için sabit bir kapsayıcıdan verileri geçirmek için kullanmanız gerekir [veri geçiş aracı](../cosmos-db/import-data.md) veya [değişiklik akışı Kitaplığı](../cosmos-db/change-feed.md).

Birden çok sabit kapsayıcıya yazma özelliği kullanımdan kalktı ve Stream Analytics işinizi ölçeklendirirken önerilmez.

## <a name="improved-throughput-with-compatibility-level-12"></a>Uyumluluk düzeyi 1,2 ile iyileştirilmiş performans
Uyumluluk düzeyi 1,2 ile, Stream Analytics Cosmos DB toplu olarak yazmak için yerel tümleştirmeyi destekler. Bu işlem, işleme hızını en üst düzeye çıkarmak ve azaltma isteklerini verimli bir şekilde işlemek Cosmos DB. Gelişmiş yazma mekanizması, büyük bir davranış farkı nedeniyle yeni bir uyumluluk düzeyinde kullanılabilir.  1,2 ' den önce, büyük bir davranış belgeyi eklemek veya birleştirmek olur. 1,2 ile, satır eklemek veya değiştirmek için yukarı serts davranışı değiştirilmiştir.

1,2 ' den önce, bir toplu işin işlem olarak yazıldığı Cosmos DB, bölüm anahtarı başına belge toplu olarak eklemek için özel bir saklı yordam kullanır. Tek bir kayıt geçici bir hatayı (daraltma) azaltsa bile, tüm toplu işin yeniden denenmesi gerekir. Bu senaryolar, hatta makul bir kısıtlama sayesinde oldukça yavaştır. Aşağıdaki karşılaştırma, bu işlerin 1,2 ile nasıl davrandığını gösterir.

Aşağıdaki örnekte aynı Olay Hub 'ı girişinden aynı Stream Analytics işlerin okuduğu iki özdeş gösterilmektedir. Stream Analytics işlerin her ikisi de bir PASSTHROUGH sorgusuyla [tamamen bölümlenir](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) ve özdeş cosmosdb kapsayıcılarına yazılır. Sol taraftaki ölçümler, uyumluluk düzeyi 1,0 ile yapılandırılan iş ve sağdaki 1,2 ile yapılandırılır. Cosmos DB kapsayıcısının bölüm anahtarı, giriş olayından gelen benzersiz bir GUID 'dir.

![Stream Analytics ölçümleri karşılaştırması](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Olay Hub 'ında gelen olay oranı, Cosmos DB kapsayıcılarından (20K ru) çok daha yüksek bir değer olan 1 ' den fazla. bu nedenle, azaltma işlemi Cosmos DB beklenir. Bununla birlikte, 1,2 ile iş, düzenli olarak daha yüksek bir işleme (çıkış olayları/dakika) ve daha düşük ortalama% kullanım kullanımı ile yazıyor. Ortamınızda bu fark, olay biçimi, giriş olayı/ileti boyutu, bölüm anahtarları, sorgu vb. seçimi gibi birkaç etkene bağlı olacaktır.

![Cosmos DB ölçümleri karşılaştırması](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1,2 ile Stream Analytics, azaltma/hız sınırlamalarından çok az sayıda yeniden gönderme ile Cosmos DB mevcut üretilen iş hızının% 100 ' i kullanarak daha akıllı bir işlem olur. Bu, kapsayıcıda aynı anda çalışan sorgular gibi diğer iş yükleri için daha iyi bir deneyim sağlar. Örneğin, 1 KB 'den 10.000 ileti/saniye için bir havuz olarak ASA 'nin Cosmos DB nasıl ölçeklendirebileceğiniz için, bunu yapmanızı sağlayan bir [Azure örnekleri projesi](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) aşağıda verilmiştir.
Cosmos DB çıktı üretilen işinin 1,0 ve 1,1 ile aynı olduğunu lütfen unutmayın. 1,2 Şu anda varsayılan olmadığından, Stream Analytics işi için, portalı kullanarak veya [iş oluşturma REST API çağrısını](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)kullanarak [Uyumluluk düzeyi ayarlayabilirsiniz](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) . Uyumluluk düzeyi 1,2 ' i Cosmos DB ile ASA içinde kullanmanız *önemle önerilir* .



## <a name="cosmos-db-settings-for-json-output"></a>Çıkış JSON için cosmos DB ayarları

Stream analytics'te bir çıkış olarak Cosmos DB oluşturma, aşağıda görüldüğü gibi bilgileri için bir istem oluşturur. Bu bölüm, özellikleri tanımının bir açıklama sağlar.

![documentdb stream analytics çıkış ekranı](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Alan           | Açıklama|
|-------------   | -------------|
|Çıktı diğer adı    | ASA sorgunuzda bu çıktıyı ifade eden bir diğer ad.|
|Subscription    | Azure aboneliğini seçin.|
|Hesap Kimliği      | Azure Cosmos DB hesabının adı veya uç nokta URI 'SI.|
|Hesap anahtarı     | Azure Cosmos DB hesabının paylaşılan erişim anahtarı.|
|Database        | Azure Cosmos DB veritabanı adı.|
|Kapsayıcı adı | Kullanılacak kapsayıcı adı. `MyContainer`örnek geçerli bir giriş; adlı `MyContainer` bir kapsayıcı var olmalıdır.  |
|Belge Kimliği     | İsteğe bağlı. Hangi ekleme veya güncelleştirme işlemleri dayanması benzersiz bir anahtar kullanılan çıkış olaylarındaki sütun adı. Boş bırakılırsa, tüm olayları güncelleştirme seçeneği ile eklenir.|

## <a name="error-handling-and-retries"></a>Hata Işleme ve yeniden denemeler

Geçici bir hata durumunda, Cosmos DB olayları gönderirken hizmetin kullanım dışı kalması veya azalmasını, işlemi başarıyla tamamlamaya yönelik olarak yeniden dener Stream Analytics. Ancak aşağıda belirtilen hatalarda yeniden deneme girişiminde bulunulmaz:

- Yetkisiz (http hata kodu 401)
- NotFound (http hata kodu 404)
- Yasak (http hata kodu 403)
- BadRequest (http hata kodu 400)
