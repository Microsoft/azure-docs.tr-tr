---
title: Azure Stream Analytics aramalar için başvuru verilerini kullanma
description: Bu makalede, bir Azure Stream Analytics işinin sorgu tasarımında verileri aramak veya ilişkilendirmek için başvuru verilerinin nasıl kullanılacağı açıklanır.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: e05a4cbbc5fefbfe8a92914ef480f32bdf43ca37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99560213"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Stream Analytics aramalar için başvuru verilerini kullanma

Başvuru verileri (arama tablosu olarak da bilinir), bir arama gerçekleştirmek veya veri akışlarınızı genişletmek için kullanılan, statik veya yavaş değişen, sınırlı bir veri kümesidir. Örneğin, bir IoT senaryosunda, sensörlerle ilgili meta verileri (sık değişmeyin) başvuru verilerinde saklayabilir ve gerçek zamanlı IoT veri akışları ile birleştirebilirsiniz. Azure Stream Analytics düşük gecikmeli akış işleme elde etmek için belleğe başvuru verilerini yükler. Azure Stream Analytics işinizdeki başvuru verilerini kullanmak için, genellikle sorgunuzda bir [başvuru verisi katılımı](/stream-analytics-query/reference-data-join-azure-stream-analytics) kullanacaksınız. 

## <a name="example"></a>Örnek  
Otomobiller bir stand geldiğinde gerçek zamanlı bir olay akışına sahip olabilirsiniz. Ücretli stand, lisans levhasını gerçek zamanlı olarak yakalayabilir ve süresi doldu lisans kalıplarını belirlemek için kayıt ayrıntıları olan statik bir veri kümesiyle katılabilir.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Stream Analytics, Azure Blob depolama ve Azure SQL veritabanını başvuru verileri için depolama katmanı olarak destekler. Ayrıca, [bulut tabanlı ve şirket içi veri mağazalarını](../data-factory/copy-activity-overview.md)kullanmak için Azure Data Factory ' den BLOB depolama alanına başvuru verileri dönüştürebilir ve/veya kopyalayabilirsiniz.

## <a name="azure-blob-storage"></a>Azure Blob depolama

Başvuru verileri, blob adında belirtilen tarih/saatin artan sırada bir blob dizisi (giriş yapılandırmasında tanımlanır) olarak modellenir. **Yalnızca** dizideki son blob tarafından belirtilenden **büyük** bir tarih/saat kullanarak sıranın sonuna eklenmesini destekler. Daha fazla bilgi için bkz. bir [Azure Stream Analytics işi Için blob depolamadan başvuru verilerini kullanma](data-protection.md).

### <a name="configure-blob-reference-data"></a>Blob başvuru verilerini yapılandırma

Başvuru verilerinizi yapılandırmak için önce **başvuru verileri** türünde bir giriş oluşturmanız gerekir. Aşağıdaki tabloda, başvuru veri girişini açıklaması ile oluştururken sağlamanız gereken her özellik açıklanmaktadır:

|**Özellik Adı**  |**Açıklama**  |
|---------|---------|
|Girdi Diğer Adı   | Bu girişe başvurmak için iş sorgusunda kullanılacak kolay bir ad.   |
|Depolama Hesabı   | Bloblarınızın bulunduğu depolama hesabının adı. Stream Analytics Işiniz ile aynı abonelikte yer alıyorsa, açılır listeden seçim yapabilirsiniz.   |
|Depolama hesabı anahtarı   | Depolama hesabıyla ilişkili gizli anahtar. Depolama hesabı Stream Analytics işle aynı abonelikte ise bu otomatik olarak doldurulur.   |
|Depolama kapsayıcısı   | Kapsayıcılar Microsoft Azure Blob hizmetinde depolanan Bloblar için mantıksal bir gruplama sağlar. Blob hizmetine bir blob yüklediğinizde, o blob için bir kapsayıcı belirtmeniz gerekir.   |
|Yol Deseni   | Bu, belirtilen kapsayıcı içindeki bloblarınızı bulmak için kullanılan gerekli bir özelliktir. Yol içinde, aşağıdaki 2 değişkenlerin bir veya daha fazla örneğini belirtmeyi seçebilirsiniz:<BR>{date}, {Time}<BR>Örnek 1: Ürünler/{Date}/{Time}/product-list.csv<BR>Örnek 2: Ürünler/{Date}/product-list.csv<BR>Örnek 3: product-list.csv<BR><br> Blob belirtilen yolda yoksa, blob 'un kullanılabilir olması için Stream Analytics işi süresiz olarak bekler.   |
|Tarih biçimi [isteğe bağlı]   | Belirttiğiniz yol deseninin içinde {Date} kullandıysanız, bloblarınızın desteklenen biçimlerin açılan listesinden düzenlendiği tarih biçimini seçebilirsiniz...<BR>Örnek: YYYY/AA/GG, AA/GG/YYYY vb.   |
|Saat biçimi [isteğe bağlı]   | Belirttiğiniz yol deseninin içinde {Time} kullandıysanız, bloblarınızın desteklenen biçimlerin açılan listesinden düzenlendiği zaman biçimini seçebilirsiniz...<BR>Örnek: HH, HH/mm veya HH-mm.  |
|Olay serileştirme biçimi   | Sorgularınızın istediğiniz şekilde çalıştığından emin olmak için, Stream Analytics gelen veri akışları için hangi serileştirme biçimini kullandığınızı bilmeniz gerekir. Başvuru verileri için desteklenen biçimler CSV ve JSON ' tır.  |
|Encoding   | Şu anda desteklenen tek kodlama biçimi UTF-8 ' i destekler.  |

### <a name="static-reference-data"></a>Statik başvuru verileri

Başvuru verilerinizin değiştirilmesi beklenmiyorsa, giriş yapılandırmasında statik bir yol belirtilerek statik başvuru verileri desteği etkinleştirilir. Azure Stream Analytics belirtilen yoldan blobu seçer. {Date} ve {Time} değiştirme belirteçleri gerekli değildir. Başvuru verileri Stream Analytics sabit olduğundan, statik başvuru veri blobunun üzerine yazılması önerilmez.

### <a name="generate-reference-data-on-a-schedule"></a>Bir zamanlamaya göre başvuru verileri oluşturma

Başvuru verileriniz yavaş değişen bir veri kümesi ise, başvuru verilerinin yenilenmesi desteği, giriş yapılandırmasında {Date} ve {Time} değiştirme belirteçlerini kullanarak bir yol kalıbı belirtilerek etkinleştirilir. Stream Analytics, bu yol düzenine göre güncelleştirilmiş başvuru verileri tanımlarını seçer. Örneğin, `sample/{date}/{time}/products.csv` Tarih biçimi **"yyyy-aa-gg"** ve saat biçimi **"hh-mm"** olan bir örüntü `sample/2015-04-16/17-30/products.csv` , 15 NISAN 2015 UTC saat diliminde 5:30 PM 'de güncelleştirilmiş blobu Stream Analytics.

Azure Stream Analytics, yenilenen başvuru verileri bloblarını bir dakikalık aralıklarla otomatik olarak tarar. Zaman damgası 10:30:00 olan bir blob küçük bir gecikmeyle karşıya yüklenirse (örneğin, 10:30:30), bu bloba başvuran Stream Analytics işinde küçük bir gecikme fark edeceksiniz. Bu senaryolara engel olmak için, blob 'u bellek içinde bulup yüklemek ve işlemleri gerçekleştirmek için yeterli zaman Stream Analytics izin vermek üzere hedef geçerlilik zamanından (Bu örnekteki 10:30:00) daha önce blob yüklemeniz önerilir. 

> [!NOTE]
> Şu anda Stream Analytics işleri yalnızca makine süresi blob adında kodlanan zamana ilerlediği zaman blob yenilemesini arar. Örneğin, iş `sample/2015-04-16/17-30/products.csv` mümkün olan en kısa sürede, 16 nisan 2015 UTC saat diliminde 5:30 PM 'tan önceki bir süre içinde görünür. Bir blob, bulunan son değerden daha önce kodlanmış bir saat ile *hiçbir zaman* aramaz.
> 
> Örneğin, iş blobu bulduktan sonra `sample/2015-04-16/17-30/products.csv` 2015, 12 nisan 5:30 ' den önceki kodlanmış bir tarih içeren tüm dosyaları yok sayacaktır. bu nedenle, bir geç gelen `sample/2015-04-16/17-25/products.csv` BLOB aynı kapsayıcıda oluşturulduysa iş onu kullanmaz.
> 
> Benzer şekilde `sample/2015-04-16/17-30/products.csv` , yalnızca 16 nisan 2015 ' de 10:03, ancak kapsayıcıda daha önceki bir tarih içeren bir blob yoksa, iş bu 10:03 dosyayı 11 Nisan, 2015 ve önceki başvuru verilerini kullanarak kullanır.
> 
> Bunun bir özel durumu, işin, verileri zamanında yeniden işlemesi veya işin ilk başlatılışında olması gerekir. Başlangıç zamanında iş, belirtilen iş başlangıç zamanından önce üretilen en son blobu arıyor. Bu, iş başladığında **boş olmayan** bir başvuru veri kümesi olduğundan emin olmak için yapılır. Bir tane bulunamazsa, iş aşağıdaki tanılamayı görüntüler: `Initializing input without a valid reference data blob for UTC time <start time>` .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) , başvuru veri tanımlarını güncelleştirmek için Stream Analytics gereken güncelleştirilmiş blob 'ların oluşturulması görevini düzenlemek üzere kullanılabilir. Data Factory, verilerin taşınmasını ve dönüştürülmesini düzenleyen ve otomatikleştiren bulut tabanlı bir veri tümleştirme hizmetidir. Data Factory, [çok sayıda bulut tabanlı ve şirket içi veri depolarına bağlanmayı](../data-factory/copy-activity-overview.md) ve verileri belirttiğiniz düzenli bir zamanlamaya göre kolayca taşımayı destekler. Daha fazla bilgi ve bir Data Factory işlem hattının, önceden tanımlanmış bir zamanlamaya göre yenileyen Stream Analytics için başvuru verileri oluşturmak üzere nasıl ayarlanacağı hakkında adım adım yönergeler için bu [GitHub örneğine](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs)göz atın.

### <a name="tips-on-refreshing-blob-reference-data"></a>Blob başvuru verilerini yenileme ipuçları

1. Başvuru verileri bloblarını sabit oldukları için üzerine yazmaz.
2. Başvuru verilerini yenilemek için önerilen yol şu şekilde yapılır:
    * Yol modelinde {Date}/{Time} kullanın
    * İş girişinde tanımlanan aynı kapsayıcıyı ve yol modelini kullanarak yeni bir blob ekleyin
    * Sıradaki son blob tarafından belirtilenden **büyük** bir tarih/saat kullanın.
3. Başvuru verileri Blobları, Blobun "son değiştirilme" zamanına **göre sıralanır,** ancak yalnızca {Date} ve {Time} değiştirmeler kullanılarak blob adında belirtilen saat ve Tarih ile belirtilir.
3. Çok sayıda blob listelemeyi önlemek için, işleme artık yapılmayacak olan çok eski blob 'ları silmeyi göz önünde bulundurun. Lütfen ASA 'nin yeniden başlatma gibi bazı senaryolarda küçük bir miktarı yeniden işlemesi gerekebileceğini unutmayın.

## <a name="azure-sql-database"></a>Azure SQL Veritabanı

Azure SQL veritabanı başvuru verileri Stream Analytics işiniz tarafından alınır ve işlenmek üzere bellekte bir anlık görüntü olarak depolanır. Başvuru verilerinizin anlık görüntüsü Ayrıca yapılandırma ayarlarında belirttiğiniz depolama hesabındaki bir kapsayıcıda depolanır. İş başladığında kapsayıcı otomatik olarak oluşturulur. İş durdurulmuşsa veya başarısız durumuna girerse, iş yeniden başlatıldığında otomatik oluşturulan kapsayıcılar silinir.  

Başvuru verileriniz yavaş değişen bir veri kümesi ise, işte kullanılan anlık görüntüyü düzenli olarak yenilemeniz gerekir. Stream Analytics, Azure SQL veritabanı giriş bağlantınızı yapılandırırken bir yenileme hızı ayarlamanıza olanak sağlar. Stream Analytics çalışma zamanı, Azure SQL veritabanınızı yenileme hızı tarafından belirtilen aralıkta sorgular. Desteklenen en hızlı yenileme hızı dakikada bir kez yapılır. Her yenileme için, Stream Analytics, belirtilen depolama hesabında yeni bir anlık görüntü depolar.

Stream Analytics, Azure SQL veritabanınızı sorgulamak için iki seçenek sunar. Bir anlık görüntü sorgusu zorunludur ve her işe eklenmelidir. Stream Analytics, yenileme aralığı temelinde anlık görüntü sorgusunu düzenli aralıklarla çalıştırır ve sorgu sonucunu (snapshot) başvuru veri kümesi olarak kullanır. Anlık görüntü sorgusunun çoğu senaryoya uyması gerekir, ancak büyük veri kümeleri ve hızlı yenileme ücretleri ile performans sorunlarıyla karşılaşırsanız, Delta sorgu seçeneğini kullanabilirsiniz. Başvuru veri kümesini döndürmek için 60 saniyeden daha fazla geçen sorgular zaman aşımı ile sonuçlanır.

Delta sorgu seçeneği ile, Stream Analytics taban çizgisi başvuru veri kümesi almak için başlangıçta anlık görüntü sorgusunu çalıştırır. Sonra, Stream Analytics artımlı değişiklikleri almak için yenileme aralığı temelinde düzenli aralıklarla Delta sorgusu çalıştırır. Bu artımlı değişiklikler, güncel tutmak için başvuru verileri kümesine sürekli olarak uygulanır. Delta sorgusunun kullanılması, depolama maliyetini ve ağ g/ç işlemlerini azaltmaya yardımcı olabilir.

### <a name="configure-sql-database-reference"></a>SQL veritabanı başvurusunu yapılandırma

SQL veritabanı başvuru verilerinizi yapılandırmak için önce **başvuru veri** girişi oluşturmanız gerekir. Aşağıdaki tabloda, başvuru verileri girişini açıklamasıyla oluştururken sağlamanız gereken her özellik açıklanmaktadır. Daha fazla bilgi için bkz. [Azure Stream Analytics bir iş IÇIN SQL veritabanından başvuru verileri kullanma](sql-reference-data.md).

[Azure SQL yönetilen örneğini](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) , başvuru veri girişi olarak kullanabilirsiniz. [SQL yönetilen örneği 'nde genel uç noktasını yapılandırmanız](../azure-sql/managed-instance/public-endpoint-configure.md) ve ardından Azure Stream Analytics aşağıdaki ayarları el ile yapılandırmanız gerekir. SQL Server çalıştıran bir veritabanı ile birlikte çalışan Azure sanal makinesi, aşağıdaki ayarları el ile yapılandırarak de desteklenir.

|**Özellik Adı**|**Açıklama**  |
|---------|---------|
|Girdi diğer adı|Bu girişe başvurmak için iş sorgusunda kullanılacak kolay bir ad.|
|Abonelik|Aboneliğinizi seçin|
|Veritabanı|Başvuru verilerinizi içeren Azure SQL veritabanı. SQL yönetilen örneği için 3342 numaralı bağlantı noktasını belirtmeniz gerekir. Örneğin, *sampleserver. public. Database. Windows. net, 3342*|
|Kullanıcı adı|Azure SQL veritabanınız ile ilişkilendirilen Kullanıcı adı.|
|Parola|Azure SQL veritabanınız ile ilişkilendirilen parola.|
|Düzenli aralıklarla Yenile|Bu seçenek, yenileme ücreti seçmenizi sağlar. "Açık" seçilirse yenileme hızını gg: ss: dd olarak belirtmenize izin verirsiniz.|
|Anlık görüntü sorgusu|Bu, SQL veritabanından başvuru verilerini alan varsayılan sorgu seçeneğidir.|
|Delta sorgusu|Büyük veri kümelerine ve kısa yenileme hızına sahip gelişmiş senaryolar için, bir Delta sorgusu eklemeyi seçin.|

## <a name="size-limitation"></a>Boyut sınırlaması

En iyi performansı elde etmek için 300 MB 'tan az olan başvuru veri kümelerini kullanmanız önerilir. Başvuru veri kümeleri 5 GB veya daha düşük olan işlerde 6 SUs veya daha fazlası desteklenir. Çok büyük bir başvuru verilerinin kullanılması, işinizin uçtan uca gecikme süresini etkileyebilir. Sorgu karmaşıklığı, pencereli toplamalar, zamana bağlı birleşimler ve zamana bağlı analitik işlevler gibi durum bilgisi içeren işleme dahil olmak üzere arttıkça, başvuru verilerinin en fazla desteklenen boyutunun azalttığı beklenmektedir. Azure Stream Analytics başvuru verilerini yükleyemez ve karmaşık işlemler gerçekleştirmeyebilir, iş belleği tükenme ve başarısız olur. Bu gibi durumlarda% SU kullanım ölçümü %100 ' ye ulaşacaktır.    

|**Akış birimi sayısı**  |**Önerilen boyut**  |
|---------|---------|
|1   |50 MB veya daha düşük   |
|3   |150 MB veya daha düşük   |
|6 ve daha fazla   |5 GB veya daha düşük.    |

Başvuru verileri için sıkıştırma desteği kullanılamaz.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Bir işte birden çok başvuru veri kümesini birleştirme
Sorgunuzun tek adımında tek bir başvuru veri girişi ile yalnızca bir akış girişine katılabilir. Ancak, sorgunuzu birden çok adıma ayırarak birden çok başvuru veri kümesine katılabilmeniz gerekir. Aşağıda bir örnek gösterilmiştir.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="iot-edge-jobs"></a>IoT Edge işleri

Stream Analytics Edge işleri için yalnızca yerel başvuru verileri desteklenir. IoT Edge cihaza bir iş dağıtıldığında, Kullanıcı tanımlı dosya yolundan başvuru verilerini yükler. Cihaza bir başvuru veri dosyası hazırlayın. Bir Windows kapsayıcısı için, başvuru veri dosyasını yerel sürücüye yerleştirin ve yerel sürücüyü Docker kapsayıcısı ile paylaşabilirsiniz. Bir Linux kapsayıcısı için, bir Docker birimi oluşturun ve veri dosyasını birime doldurun.

IoT Edge güncelleştirmedeki başvuru verileri bir dağıtım tarafından tetiklenir. Tetiklendikten sonra Stream Analytics modülü, çalışan işi durdurmadan güncelleştirilmiş verileri seçer.

Başvuru verilerini güncelleştirmenin iki yolu vardır:

* Azure portal Stream Analytics işinizdeki başvuru verileri yolunu güncelleştirin.

* IoT Edge dağıtımını güncelleştirin.

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure portalını kullanarak Stream Analytics işi oluşturma](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: ./stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
