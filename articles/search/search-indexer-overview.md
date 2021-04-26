---
title: Dizin Oluşturucu’ya genel bakış
titleSuffix: Azure Cognitive Search
description: Aranabilir verileri ayıklamak ve bir Azure Bilişsel Arama dizinini doldurmak için Azure SQL veritabanı, SQL yönetilen örneği, Azure Cosmos DB veya Azure Storage 'ı gezin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6cce37a7c719c6a0c183e166fa28967ea926a221
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581649"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Bilişsel Arama'daki Dizin Oluşturucular

Azure Bilişsel Arama 'de bir *Dizin Oluşturucu* , bir dış Azure veri kaynağından aranabilir metin ve meta verileri çıkaran ve kaynak verilerle dizininizdeki alan eşlemeleri kullanarak bir arama dizinini dolduran bir gezgin. Bu yaklaşım bazen ' çekme modeli ' olarak adlandırılır, çünkü hizmet verileri bir dizine ekleyen herhangi bir kod yazmak zorunda kalmadan içine veri çeker. Dizin oluşturucular Ayrıca Bilişsel Arama [AI zenginleştirme](cognitive-search-concept-intro.md) yeteneklerini de sağlar ve bu da içeriğin dış işlemesini bir dizine tümleştirmenize olanak sağlar.

Dizin oluşturucular, Azure [SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Tablo depolama](search-howto-indexing-azure-tables.md) ve [BLOB depolama](search-howto-indexing-azure-blob-storage.md)için bireysel dizin oluşturucular ile yalnızca Azure ' dir. Bir dizin oluşturucuyu yapılandırırken bir veri kaynağı (Origin) ve bir dizin (hedef) belirtirsiniz. BLOB depolama gibi birkaç kaynak, bu içerik türüne özgü ek yapılandırma özelliklerine sahiptir.

Dizin oluşturucularını isteğe bağlı olarak veya her beş dakikada bir çalışan yinelenen bir veri yenileme zamanlaması üzerinde çalıştırabilirsiniz. Daha sık güncellemeler, hem Azure Bilişsel Arama hem de dış veri kaynağınızdaki verileri eşzamanlı olarak güncelleştiren bir [' Push model '](search-what-is-data-import.md) gerektirir.

## <a name="usage-scenarios"></a>Kullanım senaryoları

Bir dizin oluşturucuyu, veri alımı için tek yol olarak veya içerik yükleme ve isteğe bağlı olarak içerik dönüştürme ya da zenginleştirme teknikleri birleşiminin bir parçası olarak kullanabilirsiniz. Aşağıdaki tabloda ana senaryolar özetlenmektedir.

| Senaryo |Strateji |
|----------|---------|
| Tek veri kaynağı | Bu model en basit: bir veri kaynağı, bir arama dizini için tek içerik sağlayıcıdır. Kaynaktan, arama dizininde belge anahtarı olarak kullanılacak benzersiz değerler içeren bir alan tanımlayacaksınız. Benzersiz değer bir tanımlayıcı olarak kullanılacaktır. Diğer tüm kaynak alanları, bir dizindeki ilgili alanlarla örtük olarak veya açıkça eşleştirilir. </br></br>Önemli bir kalkış, bir belge anahtarı değerinin kaynak verilerden kaynaklanabilme örneğidir. Arama hizmeti anahtar değerleri oluşturmaz. Sonraki çalışmalarda, yeni anahtarlara sahip gelen belgeler eklenir, ancak mevcut anahtarlara sahip gelen belgeler, Dizin alanlarının null ya da doldurulmuş olmasına bağlı olarak birleştirilir veya üzerine yazılır. |
| Birden çok veri kaynağı | Bir dizin, her çalıştırmanın farklı bir kaynaktan yeni içerik aldığı birden çok kaynaktan içerik kabul edebilir. </br></br>Tek bir sonuç, her bir Dizin Oluşturucu çalıştıktan sonra belge elde eden bir dizin olabilir ve tüm belgeler her kaynaktan tam olarak oluşturulur. Örneğin, belgeler 1-100, BLOB depolama 'dan, 101-200 belgelerinin Azure SQL 'den ve bu şekilde devam eder. Bu senaryonun çekişmesi, tüm gelen veriler için uygun bir dizin şeması ve arama dizininde tek biçimli bir belge anahtar yapısı tasarlamada yer alır. Yerel olarak, bir belgeyi benzersiz bir şekilde tanımlayan değerler bir blob kapsayıcısında ve bir SQL tablosundaki birincil anahtar metadata_storage_path. Bir veya iki kaynağın, içerik kaynağından bağımsız olarak ortak bir biçimde anahtar değerleri sağlaması için düzeltmeniz gerektiğini düşünün. Bu senaryoda, verileri tek bir dizine çekilecek şekilde eklemek için bir miktar ön işleme düzeyi gerçekleştirmeyi beklemeniz gerekir. </br></br>Alternatif bir sonuç, ilk çalıştırmada kısmen doldurulmuş ve ardından sonraki çalışmalarla daha sonra diğer kaynaklardan değer getirmek için daha sonra doldurulmuş belgelerde arama yapmak olabilir. Örneğin, 1-10 alanları BLOB depolama, Azure SQL 'den 11-20 ve benzeri. Bu düzenin çekişmesi, her bir dizinleme çalıştırmasının aynı belgeyi hedeflediğinden emin olmanızı sağlamak. Alanların mevcut bir belgede birleştirilmesi için belge anahtarında bir eşleşme olması gerekir. Bu senaryonun bir gösterimi için bkz. [öğretici: birden çok veri kaynağından Dizin](tutorial-multiple-data-sources.md). |
| Çoklu Dizin oluşturucular | Birden çok veri kaynağı kullanıyorsanız, çalıştırma zamanı parametrelerini, zamanlamayı veya alan eşlemelerini değiştirmeniz gerekiyorsa birden çok Dizin Oluşturucu da gerekebilir. Birden çok Indexer-Data-Source kümesi aynı dizini hedefleyebilir, ancak dizindeki mevcut değerlerin üzerine yazabilecek Dizin Oluşturucu çalıştırmalarının dikkatli olun. İkinci bir dizin oluşturucu-veri kaynağı aynı belge ve alanları hedefliyorsa, ilk çalıştırdaki tüm değerlerin üzerine yazılır. Alan değerleri tam olarak değişir; bir Dizin Oluşturucu birden çok çalıştırdaki değerleri aynı alana birleştiremez.</br></br>Başka bir çoklu Dizin Oluşturucu kullanım örneği [bilişsel arama çapraz bölge ölçeklendirmektir](search-performance-optimization.md#data-sync). Farklı bölgelerde aynı arama dizininin kopyalarına sahip olabilirsiniz. Arama dizini içeriğini eşzamanlı hale getirmek için, her bir dizin oluşturucunun farklı bir arama dizinini hedeflediği aynı veri kaynağından birden çok dizinleyici çekmesini sağlayabilirsiniz.</br></br>Çok büyük veri kümelerinin [paralel dizinlemesi](search-howto-large-index.md#parallel-indexing) de bir çoklu Dizin Oluşturucu stratejisi gerektirir. Her Dizin Oluşturucu verilerin bir alt kümesini hedefler. |
| İçerik dönüştürme | Bilişsel Arama, yeni aranabilir içerik ve yapı oluşturmak için görüntü analizi ve doğal dil işleme ekleyen isteğe bağlı [AI zenginleştirme](cognitive-search-concept-intro.md) davranışlarını destekler. AI zenginleştirme, eklenen bir [beceri](cognitive-search-working-with-skillsets.md)aracılığıyla Dizin Oluşturucu temelli bir. AI zenginleştirme işlemini gerçekleştirmek için, dizin oluşturucunun yine bir dizin ve Azure veri kaynağı olması gerekir, ancak bu senaryoda Dizin Oluşturucu yürütmeye beceri işlem ekler. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Desteklenen veri kaynakları

Dizin oluşturucular Azure 'da veri depolarında gezinme.

+ [Azure Blob Depolama](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage 2.](search-howto-index-azure-data-lake-storage.md) (önizlemede)
+ [Azure Tablo Depolama](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Veritabanı](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Yönetilen Örnek](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Azure Sanal Makineler'de SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

İstemci uygulamaları için Azure sanal ağlarını kullandığınızda, uzak veri kaynaklarına yönelik Dizin Oluşturucu bağlantıları standart Internet bağlantıları (genel) veya şifrelenmiş özel bağlantılar kullanılarak yapılabilir. Ayrıca, güvenilen bir hizmet kimliği kullanarak kimlik doğrulaması için bağlantılar da ayarlayabilirsiniz. Güvenli bağlantılar hakkında daha fazla bilgi için bkz. [Özel uç noktalar aracılığıyla erişim verme](search-indexer-securing-resources.md#granting-access-via-private-endpoints) ve [yönetilen kimlik kullanarak bir veri kaynağına bağlanma](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Dizin oluşturma aşamaları

Bir başlangıç çalıştırmasında, dizin boş olduğunda bir Dizin Oluşturucu, tabloda veya kapsayıcıda belirtilen tüm verilerde okunacaktır. Sonraki çalışmalarda, Dizin Oluşturucu genellikle değişmiş olan verileri algılayabilir ve alabilir. Blob verileri için değişiklik algılama otomatik olarak belirlenir. Azure SQL veya Cosmos DB gibi diğer veri kaynakları için değişiklik algılama özelliğinin etkinleştirilmesi gerekir.

Bir Dizin Oluşturucu, aldığı her belge için, belge almayla, dizin oluşturma için "iletime" nihai arama motoruna birden çok adımı uygular veya düzenler. İsteğe bağlı olarak, bir Dizin Oluşturucu, bir beceri tanımlandığından beceri yürütme ve çıkışları çalıştırmaya da benzer.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Dizin Oluşturucu aşamaları" border="false":::

### <a name="stage-1-document-cracking"></a>1. Aşama: belge çözme

Belge çözme, dosyaları açma ve içeriği ayıklama işlemidir. Veri kaynağının türüne bağlı olarak, Dizin Oluşturucu, Dizin oluşturamayacak olabilecek içeriği ayıklamak için farklı işlemler gerçekleştirmeye çalışır.  

Örnekler:  

+ Belge, [Azure SQL veri kaynağındaki](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)bir kayıt olduğunda, Dizin Oluşturucu kayıt için her bir alanı ayıklar.
+ Belge, [Azure Blob depolama veri kaynağındaki](search-howto-indexing-azure-blob-storage.md)bir PDF dosyası olduğunda, Dizin Oluşturucu metin, resim ve meta verileri ayıklar.
+ Belge, [Cosmos DB veri kaynağındaki](search-howto-index-cosmosdb.md)bir kayıt olduğunda, Dizin Oluşturucu alanları ve alt alanları Cosmos DB belgesinden ayıklar.

### <a name="stage-2-field-mappings"></a>2. Aşama: alan eşlemeleri 

Dizin Oluşturucu bir kaynak alanından metin ayıklar ve bir dizin veya bilgi deposundaki hedef alana gönderir. Alan adları ve türleri kesişyolduğunda yol net olur. Ancak, çıktıda farklı adlar veya türler isteyebilirsiniz. Bu durumda, dizin oluşturucuya alanı nasıl eşleyeceğinizi bildirmeniz gerekir. 

Bu adım, belge çözme işleminden sonra, ancak Dizin Oluşturucu kaynak belgelerden okurken dönüşümlerden önce oluşur. Bir [alan eşlemesi](search-indexer-field-mappings.md)tanımladığınızda, kaynak alanın değeri, hedef alana değişiklik olmadan olarak gönderilir. 

### <a name="stage-3-skillset-execution"></a>3. Aşama: beceri yürütme

Beceri yürütme, yerleşik veya özel AI işlemesini çağıran isteğe bağlı bir adımdır. Kaynak veriler ikili bir görüntüdür ve içerik farklı dillerde ise dil çevirisine ihtiyacınız varsa, görüntü analizi biçiminde optik karakter tanıma (OCR) için buna ihtiyaç duyabilirsiniz. 

Dönüştürme ne olursa olsun, Beceri yürütmesi nerede meydana gelir. Bir Dizin Oluşturucu bir işlem hattıyla, bir [beceri](cognitive-search-defining-skillset.md) "ardışık düzen içinde işlem hattı" olarak düşünebilirsiniz.

### <a name="stage-4-output-field-mappings"></a>4. Aşama: çıkış alanı eşlemeleri

Bir beceri eklerseniz, büyük olasılıkla çıkış alanı eşlemelerini eklemeniz gerekir. Bir beceri çıkışı aslında *zenginleştirilmiş belge* olarak adlandırılan bir bilgi ağacıdır. Çıkış alanı eşlemeleri, bu ağacın hangi bölümlerinin dizininizdeki alanlarla eşlendiğini seçmenizi sağlar. [Çıkış alanı eşlemelerini nasıl tanımlayacağınızı](cognitive-search-output-field-mapping.md)öğrenin.

Alan eşlemeleri, veri kaynağından tam değerleri hedef alanlarla ilişkilendirirken, çıkış alanı eşlemeleri Dizin oluşturucudaki dönüştürülmüş değerleri dizindeki hedef alanlarla nasıl ilişkilendirmiş olduğunu söyler. İsteğe bağlı olarak kabul edilen alan eşlemelerinin aksine, her zaman bir dizinde bulunması gereken dönüştürülmüş içerikler için bir çıkış alanı eşlemesi tanımlamanız gerekir.

Sonraki görüntüde, Dizin Oluşturucu aşamaları için bir örnek Dizin Oluşturucu [hata ayıklama oturumu](cognitive-search-debug-session.md) temsili gösterilmektedir: belge çözme, alan eşlemeleri, Beceri yürütme ve çıkış alanı eşlemeleri.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="örnek hata ayıklama oturumu" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Temel iş akışı

Dizin oluşturucular veri kaynağına özgü özellikler sunabilir. Bu bakımdan, dizin oluşturucu veya veri kaynağı yapılandırmasının bazı boyutları dizin oluşturucu türüne göre farklılık gösterir. Bununla birlikte, tüm dizin oluşturucuların temel birleşimi ve gereksinimleri aynıdır. Tüm dizin oluşturucularda ortak olan adımlar aşağıda ele alınmıştır.

### <a name="step-1-create-a-data-source"></a>1. Adım: Veri kaynağı oluşturma

Dizin oluşturucular, bağlantı dizesi ve muhtemelen kimlik bilgileri sağlayan bir *veri kaynağı* nesnesi gerektirir. Kaynağı oluşturmak için [veri kaynağı oluşturma (REST)](/rest/api/searchservice/create-data-source) veya [SearchIndexerDataSourceConnection sınıfını](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) çağırın.

Veri kaynakları, bunları kullanan dizin oluşturuculardan bağımsız olarak yapılandırılır ve yönetilir. Bu da bir veri kaynağının, bir seferde birden çok dizin yüklemek amacıyla birden çok dizin oluşturucu tarafından kullanılabileceği anlamına gelir.

### <a name="step-2-create-an-index"></a>2. Adım: Dizin oluşturma

Dizin oluşturucu veri alımıyla ilgili bazı görevleri otomatikleştirir, ancak dizin oluşturma genellikle bu görevlerden biri değildir. Bir önkoşul olarak dış veri kaynağınızdaki alanlarla eşleşen alanlara sahip önceden tanımlı bir dizininiz olmalıdır. Alanların ad ve veri türüyle eşleşmesi gerekir. Aksi takdirde, ilişkilendirmeyi oluşturmak için [alan eşlemelerini tanımlayabilirsiniz](search-indexer-field-mappings.md) . Dizini yapılandırma hakkında daha fazla bilgi için bkz. [Dizin oluşturma (REST)](/rest/api/searchservice/Create-Index) veya [searchındex sınıfı](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Dizin oluşturucular sizin için dizin oluşturamasa da, portaldaki **Verileri içeri aktarma** sihirbazı bu işlem için size yardımcı olabilir. Çoğu durumda, sihirbaz, kaynaktaki mevcut meta verilerden dizin şeması çıkarsayarak, sihirbaz etkin olduğunda satır içinde düzenleyebileceğiniz geçici bir dizin şeması sunar. Hizmet için sihirbaz oluşturulduğunda, portalda yapılabilecek ayrıntılı düzenlemeler, genellikle yeni alanlar eklemeyle sınırlıdır. Sihirbaz dizin oluşturmak için uygun olsa da, düzenlemek için uygun değildir. Uygulama yaparak öğrenmek için, [portal kılavuzundaki](search-get-started-portal.md) adımları izleyin.

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>3. Adım: Dizin oluşturucuyu oluşturma ve çalıştırma (veya zamanlama)

Bir Dizin Oluşturucu, arama hizmetinde [bir Dizin Oluşturucu oluşturduğunuzda](/rest/api/searchservice/Create-Indexer) çalışır. Yalnızca veri kaynağına erişilebilir veya beceri geçerli olduğunda bulacağınız Dizin oluşturucuyu oluşturduğunuzda veya çalıştırdığınızda. İlk çalıştırıldıktan sonra, [çalıştırma Dizin oluşturucuyu](/rest/api/searchservice/run-indexer)kullanarak isteğe bağlı olarak yeniden çalıştırabilirsiniz veya [yinelenen bir zamanlama tanımlayabilirsiniz](search-howto-schedule-indexers.md). 

Dizin Oluşturucu durumunu portalda veya [Dizin Oluşturucu durumunu Al API 'si](/rest/api/searchservice/get-indexer-status)aracılığıyla izleyebilirsiniz. Ayrıca, sonucu beklediğiniz şeydir olduğunu doğrulamak için [dizin üzerinde sorgular çalıştırmalısınız](search-query-create.md) .

## <a name="next-steps"></a>Sonraki adımlar

Artık tanıtıldığınıza göre, bir sonraki adım, Dizin Oluşturucu özelliklerini ve parametrelerini, zamanlamayı ve Dizin oluşturucuyu izlemeyi gözden geçirmektedir. Alternatif olarak, belirli bir kaynak hakkında daha fazla bilgi için [desteklenen veri kaynakları](#supported-data-sources) listesine geri dönebilirsiniz.

+ [Dizin oluşturucular oluştur](search-howto-create-indexers.md)
+ [Dizin oluşturucuyu sıfırlama ve çalıştırma](search-howto-run-reset-indexers.md)
+ [Zamanlama dizin oluşturucuları](search-howto-schedule-indexers.md)
+ [Alan eşlemelerini tanımlama](search-indexer-field-mappings.md)
+ [Dizin Oluşturucu durumunu izleme](search-howto-monitor-indexers.md)