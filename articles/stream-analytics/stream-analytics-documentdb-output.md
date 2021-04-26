---
title: Çıktıyı Azure Cosmos DB Azure Stream Analytics
description: Bu makalede, veri arşivleme ve yapılandırılmamış JSON verilerinde düşük gecikme süreli sorgular için JSON çıktısı için Azure Cosmos DB çıktıyı kaydetmek üzere Azure Stream Analytics nasıl kullanılacağı açıklanır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 2d00d489ff248ecf5599d78e0a351c93248cf8ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018109"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Çıktıyı Azure Cosmos DB Azure Stream Analytics  
Azure Stream Analytics, veri arşivlemeyi ve yapılandırılmamış JSON verilerinde düşük gecikme süreli sorguları etkinleştirerek JSON çıkışı için [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) hedefleyebilir. Bu belge, bu yapılandırmayı uygulamak için bazı en iyi yöntemleri içerir. Azure Cosmos db çıktı olarak kullanırken işinizi uyumluluk Level 1,2 olarak ayarlamanızı öneririz.

Azure Cosmos DB hakkında bilgi sahibi değilseniz, kullanmaya başlamak için [Azure Cosmos DB belgelerine](../cosmos-db/index.yml) bakın. 

> [!Note]
> Şu anda Stream Analytics, yalnızca *SQL API 'si* aracılığıyla Azure Cosmos DB bağlantısını destekler.
> Diğer Azure Cosmos DB API 'Leri henüz desteklenmiyor. Diğer API 'lerle oluşturulan Azure Cosmos DB hesaplara Stream Analytics işaret ediyorsanız, veriler düzgün şekilde depolanmayabilir. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Çıkış hedefi olarak Azure Cosmos DB temelleri
Stream Analytics Azure Cosmos DB çıktısı, akış işleme sonuçlarınızı Azure Cosmos DB kapsayıcılarınızın JSON çıktısı olarak yazılmasına izin verebilir. 

Stream Analytics, veritabanınızda kapsayıcı oluşturmaz. Bunun yerine, bunları baştan oluşturmanız gerekir. Daha sonra Azure Cosmos DB kapsayıcıların faturalandırma maliyetlerini kontrol edebilirsiniz. Ayrıca, [Azure Cosmos DB API 'lerini](/rest/api/cosmos-db/)kullanarak doğrudan kapsayıcılarınızın performans, tutarlılık ve kapasitesini ayarlayabilirsiniz.

> [!Note]
> Azure Cosmos DB güvenlik duvarınızdan izin verilen IP 'Ler listesine 0.0.0.0 eklemeniz gerekir.

Aşağıdaki bölümler Azure Cosmos DB kapsayıcı seçeneklerinden bazılarını ayrıntılandırır.

## <a name="tuning-consistency-availability-and-latency"></a>Tutarlılık, kullanılabilirlik ve gecikme ayarlama
Uygulama gereksinimlerinizi karşılamak için, Azure Cosmos DB veritabanı ve kapsayıcıları ince ayarlamanıza ve tutarlılık, kullanılabilirlik, gecikme süresi ve aktarım hızı arasında denge sağlamanıza olanak tanır. 

Senaryolarınızın okuma ve yazma gecikme süresine karşı ne kadar uygun olduğuna bağlı olarak, veritabanı hesabınızda bir tutarlılık düzeyi seçebilirsiniz. Kapsayıcıda Istek birimleri (ru) ölçeğini ölçeklendirerek aktarım hızını artırabilirsiniz. 

Ayrıca, Azure Cosmos DB, kapsayıcılarınızın her bir CRUD işleminde zaman uyumlu dizin oluşturmayı sağlar. Bu, Azure Cosmos DB 'de yazma/okuma performansını denetlemek için başka bir kullanışlı seçenektir. 

Daha fazla bilgi için, [veritabanınızı değiştirme ve tutarlılık düzeylerini sorgulama](../cosmos-db/consistency-levels.md) makalesini inceleyin.

## <a name="upserts-from-stream-analytics"></a>Stream Analytics yukarı
Azure Cosmos DB tümleştirmesi Stream Analytics, belirli bir **belge kimliği** sütununa göre kapsayıcınıza kayıt eklemenize veya güncelleştirmenize olanak tanır. Bu da *upsert* olarak adlandırılır.

Stream Analytics iyimser bir yaklaşım kullanır. Güncelleştirmeler yalnızca bir ekleme bir belge KIMLIĞI çakışmasıyla başarısız olduğunda gerçekleşir. 

Uyumluluk düzeyi 1,0 ile, Stream Analytics bu güncelleştirmeyi bir düzeltme eki işlemi olarak gerçekleştirir, bu nedenle belgede Kısmi güncelleştirme yapılmasını sağlar. Stream Analytics yeni özellikler ekler veya var olan bir özelliği artımlı olarak değiştirir. Ancak, JSON belgenizdeki dizi özelliklerinin değerlerinde yapılan değişiklikler tüm dizinin üzerine yazılmasına neden olacak. Diğer bir deyişle, dizi birleştirilmez. 

1,2 ile, büyük bir davranış belgeyi eklemek veya değiştirmek için değiştirilir. Uyumluluk düzeyi 1,2 ile ilgili sonraki bölümde bu davranış açıklanmaktadır.

Gelen JSON belgesinde mevcut bir KIMLIK alanı varsa, bu alan Azure Cosmos DB **belge kimliği** sütunu olarak otomatik olarak kullanılır. Sonraki tüm yazmalar, bu durumlardan birine göre önde işlenir:

- Benzersiz kimlikler ekleme ile sonuçlanabilmektedir.
- Yinelenen kimlikler ve **belge kimliği** , **kimlik** lideri olarak upsert olarak ayarlanmıştır.
- Yinelenen kimlikler ve **belge kimliği** , ilk belgeden sonra hataya neden olarak ayarlanmadı.

Yinelenen KIMLIĞE sahip olanlar da dahil olmak üzere *Tüm* belgeleri kaydetmek ISTIYORSANıZ sorgunuzun ID alanını ( **as** anahtar sözcüğünü kullanarak) yeniden adlandırın. KIMLIK alanı Azure Cosmos DB oluşturma veya KIMLIĞI başka bir sütunun değeriyle değiştirme ( **as** anahtar sözcüğünü kullanarak veya **belge kimliği** ayarını kullanarak).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB veri bölümleme
Azure Cosmos DB, iş yükünüze göre bölümleri otomatik olarak ölçeklendirir. Bu nedenle, verilerinizi bölümlemeye yönelik yaklaşım olarak [sınırsız](../cosmos-db/partitioning-overview.md) kapsayıcı önerilir. Stream Analytics sınırsız kapsayıcılara yazdığında, önceki sorgu adımı veya giriş bölümleme düzeni olarak çok sayıda paralel yazıcı kullanır.

> [!NOTE]
> Azure Stream Analytics en üst düzeyde yalnızca bölüm anahtarları olan sınırsız kapsayıcıları destekler. Örneğin, `/region` desteklenir. İç içe geçmiş bölüm anahtarları (örneğin, `/region/name` ) desteklenmez. 

Bölüm anahtarınız seçiminize bağlı olarak şu _uyarıyı_ alabilirsiniz:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Farklı değerlere sahip bir bölüm anahtarı özelliği seçmeniz ve iş yükünüzü bu değerler arasında eşit bir şekilde dağıtmanıza olanak sağlamak önemlidir. Doğal bir bölümlendirme yapıtı olarak, aynı bölüm anahtarını içeren istekler tek bir bölümün en yüksek aktarım hızı ile sınırlıdır. 

Aynı bölüm anahtarı değerine ait olan belgelerin depolama boyutu 20 GB ile sınırlıdır ( [fiziksel bölüm boyutu sınırı](../cosmos-db/partitioning-overview.md) 50 GB 'tır). [İdeal bölüm anahtarı](../cosmos-db/partitioning-overview.md#choose-partitionkey) , sorgularda filtre olarak sık görülen ve çözümünüzün ölçeklenebilir olduğundan emin olmak için yeterli kardinalite olan bir bölümdür.

Stream Analytics sorguları için kullanılan bölüm anahtarları ve Cosmos DB aynı olması gerekmez. Tam paralel topolojiler, Stream Analytics sorgusunun bölüm anahtarı olarak *giriş bölüm anahtarının* kullanılmasını önerir, `PartitionId` ancak Cosmos DB kapsayıcısının bölüm anahtarı için önerilen seçenek olmayabilir.

Bölüm anahtarı Ayrıca, saklı yordamlar ve Azure Cosmos DB tetikleyicilerinde bulunan işlemler için de sınır olur. İşlem içinde birlikte oluşan belgelerin aynı bölüm anahtarı değerini paylaşması için bölüm anahtarını seçmeniz gerekir. Azure Cosmos DB bölümünde [bulunan](../cosmos-db/partitioning-overview.md) makale, bölüm anahtarı seçme hakkında daha fazla ayrıntı sağlar.

Sabit Azure Cosmos DB kapsayıcıları için, Stream Analytics, tamamen ölçeklendirilmesi veya bu kadar bir yönteme izin vermez. 10 GB ve 10.000 RU/sn 'lik bir üst sınıra sahiptir. Verileri sabit bir kapsayıcıdan sınırsız bir kapsayıcıya (örneğin, en az 1.000 RU/s ve bölüm anahtarıyla) geçirmek için [veri geçiş aracı](../cosmos-db/import-data.md) 'nı veya [değişiklik akışı kitaplığını](../cosmos-db/change-feed.md)kullanın.

Birden çok sabit kapsayıcıya yazma özelliği kullanım dışı bırakılıyor. Stream Analytics işinizi ölçeklendirmek için bunu önermiyoruz.

## <a name="improved-throughput-with-compatibility-level-12"></a>Uyumluluk düzeyi 1,2 ile iyileştirilmiş performans
Uyumluluk düzeyi 1,2 ile, Stream Analytics Azure Cosmos DB toplu olarak yazmak için yerel tümleştirmeyi destekler. Bu tümleştirme, işleme hızını en üst düzeye çıkararak ve daraltma isteklerini verimli bir şekilde işlerken Azure Cosmos DB için yazma özelliği sağlar 

Gelişmiş yazma mekanizması, büyük bir davranıştaki farklılık nedeniyle yeni bir uyumluluk düzeyi altında bulunabilir. 1,2 ' dan önceki düzeyler ile, büyük bir davranış belgeyi eklemek veya birleştirmek olur. 1,2 ile, büyük bir davranış belgeyi eklemek veya değiştirmek için değiştirilir.

1,2 ' dan önceki düzeyler sayesinde Stream Analytics, bölüm anahtarı başına belgeleri Azure Cosmos DB içine toplu olarak eklemek için özel bir saklı yordam kullanır. Burada bir Batch işlem olarak yazılır. Tek bir kayıt geçici bir hatayı (daraltma) azaltsa bile, tüm toplu işin yeniden denenmesi gerekir. Bu, senaryoları nispeten daha da düşük bir şekilde yavaş azaltarak kolaylaştırır.

Aşağıdaki örnekte aynı Azure Event Hubs girişinin okuduğu iki özdeş Stream Analytics işi gösterilmektedir. Stream Analytics işlerin her ikisi de bir PASSTHROUGH sorgusuyla [tamamen bölümlenir](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) ve aynı Azure Cosmos DB kapsayıcılarına yazılır. Sol taraftaki ölçümler uyumluluk düzeyi 1,0 ile yapılandırılmış işten alınır. Sağdaki ölçümler 1,2 ile yapılandırılır. Azure Cosmos DB kapsayıcısının bölüm anahtarı, giriş olayından gelen benzersiz bir GUID 'dir.

![Stream Analytics ölçümlerinin karşılaştırması](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Event Hubs gelen olay hızı Azure Cosmos DB kapsayıcılardan (20.000 ru) en fazla iki kat daha yüksektir ve bu nedenle Azure Cosmos DB kısıtlama beklenir. Bununla birlikte, 1,2 ile iş düzenli olarak daha yüksek bir işleme (dakika başına çıktı olayları) ve düşük ortalama SU kullanımı ile bir yazma işlemi olur. Ortamınızda bu fark, daha fazla etkene göre farklılık gösterir. Bu faktörler, olay biçimi, giriş olayı/ileti boyutu, bölüm anahtarları ve sorgu seçimi içerir.

![Azure Cosmos DB ölçümleri karşılaştırması](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1,2 ile Stream Analytics, azaltma veya hız sınırlandırmasından çok az sayıda yeniden gönderme işlemi ile Azure Cosmos DB, kullanılabilir üretilen iş hızının yüzde 100 ' ü kullanarak daha akıllı bir işlem olur. Bu, kapsayıcıda aynı anda çalışan sorgular gibi diğer iş yükleri için daha iyi bir deneyim sağlar. Stream Analytics, saniyede 1.000 ile 10.000 ileti için bir havuz olarak Azure Cosmos DB nasıl ölçeklendirebileceğiniz hakkında bilgi edinmek istiyorsanız,  [Bu Azure örnek projesini](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb)deneyin.

Azure Cosmos DB çıkışının üretilen işi 1,0 ve 1,1 ile aynıdır. Uyumluluk düzeyi 1,2 ' i Azure Cosmos DB Stream Analytics kullanmanızı *kesinlikle öneririz* .

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON çıkışı için Azure Cosmos DB ayarları

Stream Analytics bir çıkış olarak Azure Cosmos DB kullanmak için aşağıdaki istem bilgileri oluşturulur.

![Azure Cosmos DB çıkış akışı için bilgi alanları](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Alan           | Açıklama|
|-------------   | -------------|
|Çıktı diğer adı    | Stream Analytics sorgunuzda Bu çıktıya başvurmak için bir diğer ad.|
|Abonelik    | Azure aboneliği.|
|Hesap Kimliği      | Azure Cosmos DB hesabının adı veya uç nokta URI 'SI.|
|Hesap anahtarı     | Azure Cosmos DB hesabının paylaşılan erişim anahtarı.|
|Veritabanı        | Azure Cosmos DB veritabanı adı.|
|Kapsayıcı adı | Kapsayıcı adı, örneğin `MyContainer` . Adlı bir kapsayıcı `MyContainer` var olmalıdır.  |
|Belge KIMLIĞI     | İsteğe bağlı. Ekleme veya güncelleştirme işlemlerinin temel olması gereken benzersiz anahtar olarak kullanılan çıkış olaylardaki sütun adı. Boş bırakırsanız, tüm olaylar hiçbir güncelleştirme seçeneği olmadan eklenir.|

Azure Cosmos DB çıkışını yapılandırdıktan sonra, bunu sorgusunda bir [Into ifadesinin](/stream-analytics-query/into-azure-stream-analytics)hedefi olarak kullanabilirsiniz. Bu şekilde bir Azure Cosmos DB çıkışı kullanırken, [bölüm anahtarının açıkça ayarlanması gerekir](./stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). 

Çıkış kaydı, Azure Cosmos DB bölüm anahtarından sonra adlı büyük küçük harfe duyarlı bir sütun içermelidir. Daha büyük paralelleştirme elde etmek için, deyimi aynı sütunu kullanan bir [bölüm by yan tümcesi](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) gerektirebilir.

Örnek bir sorgu aşağıda verilmiştir:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Hata işleme ve yeniden deneme

Stream Analytics geçici bir hata, hizmet kullanılamaz veya daraltma işlemi, Azure Cosmos DB olay gönderirken, işlemi başarıyla tamamlamayı Stream Analytics. Ancak, aşağıdaki hatalarda yeniden deneme girişiminde yoktur:

- Yetkisiz (HTTP hata kodu 401)
- NotFound (HTTP hata kodu 404)
- Yasak (HTTP hata kodu 403)
- BadRequest (HTTP hata kodu 400)

## <a name="common-issues"></a>Genel sorunlar

1. Koleksiyona benzersiz bir dizin kısıtlaması eklenir ve Stream Analytics çıkış verileri bu kısıtlamayı ihlal ediyor. Stream Analytics çıkış verilerinin benzersiz kısıtlamaları ihlal etmemesini veya kısıtlamaları kaldırmasını sağlayın. Daha fazla bilgi için bkz. [Azure Cosmos DB Içindeki benzersiz anahtar kısıtlamaları](../cosmos-db/unique-keys.md).

2. `PartitionKey`Sütun yok.

3. `Id`Sütun yok.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics çıkışlarını anlayın](stream-analytics-define-outputs.md) 
* [Azure SQL veritabanı 'na Azure Stream Analytics çıkışı](stream-analytics-sql-output-perf.md)
* [Özel blob çıkış bölümlendirme Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md)