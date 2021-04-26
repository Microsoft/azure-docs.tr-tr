---
title: Uyumluluk düzeylerini Azure Stream Analytics
description: Azure Stream Analytics işi için bir uyumluluk düzeyi ayarlamayı ve en son uyumluluk düzeyindeki önemli değişiklikleri nasıl ayarlayacağınızı öğrenin
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: a040aecbdee40832bd21256e26a140a986b65e39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606251"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics işleri için uyumluluk düzeyi

Bu makalede, Azure Stream Analytics uyumluluk düzeyi seçeneği açıklanmaktadır.

Stream Analytics, [düzenli özellik güncelleştirmeleri ve sabit performans iyileştirmeleri](https://azure.microsoft.com/updates/?product=stream-analytics)içeren bir yönetilen hizmettir. Hizmetin çalışma zamanları güncelleştirmelerinin çoğu, uyumluluk düzeyinden bağımsız olarak son kullanıcılar için otomatik olarak kullanılabilir hale getirilir. Ancak, yeni bir işlev var olan işlerin davranışındaki bir değişikliği veya çalışan iş işlerinde verilerin tüketildiği bir değişikliği ortaya çıkardık, bu değişikliği yeni bir uyumluluk düzeyi altında tanıttık. Uyumluluk düzeyi ayarını düşürülemez ve bu durumda, büyük değişiklikler olmadan çalışan mevcut Stream Analytics işlerinizi koruyabilirsiniz. En son çalışma zamanı davranışları için hazırsanız, uyumluluk düzeyini yükselterek kabul edebilirsiniz.


## <a name="choose-a-compatibility-level"></a>Uyumluluk düzeyi seçin

Uyumluluk düzeyi bir Stream Analytics işinin çalışma zamanı davranışını denetler.

Azure Stream Analytics Şu anda üç uyumluluk düzeyini desteklemektedir:

* 1,2-en son iyileştirmeler ile en yeni davranış
* 1,1-önceki davranış
* 1,0-birkaç yıl önce Azure Stream Analytics genel kullanıma sunulmasıyla tanıtılan orijinal uyumluluk düzeyi. 

Yeni bir Stream Analytics iş oluşturduğunuzda, en son uyumluluk düzeyini kullanarak oluşturmak en iyi uygulamadır. Daha sonra eklenmiş değişiklik ve karmaşıklığın önüne geçmek için en son davranışlardan sonra iş tasarımınızı başlatın.

## <a name="set-the-compatibility-level"></a>Uyumluluk düzeyini ayarlama

Stream Analytics işi için uyumluluk düzeyini Azure portal veya [iş oluştur REST API çağrısını](/rest/api/streamanalytics/2016-03-01/streamingjobs/createorreplace#compatibilitylevel)kullanarak ayarlayabilirsiniz.

Azure portal işin uyumluluk düzeyini güncelleştirmek için:

1. Stream Analytics işinizi bulmak için [Azure Portal](https://portal.azure.com) kullanın.
2. Uyumluluk düzeyini güncelleştirmeden önce işi **durdurun** . İşiniz çalışır durumdaysa uyumluluk düzeyini güncelleştiremezsiniz.
3. **Yapılandır** başlığı altında, **Uyumluluk düzeyi**' ni seçin.
4. İstediğiniz uyumluluk düzeyi değerini seçin.
5. Sayfanın alt kısmındaki **Kaydet** ' i seçin.

![Azure portal Stream Analytics uyumluluk düzeyi](media/stream-analytics-compatibility-level/stream-analytics-compat-level-1-2.png)

Uyumluluk düzeyini güncelleştirdiğinizde T-derleyicisi, seçili uyumluluk düzeyine karşılık gelen söz dizimi ile işi doğrular.

## <a name="compatibility-level-12"></a>Uyumluluk düzeyi 1,2

Aşağıdaki büyük değişiklikler uyumluluk düzeyi 1,2 ' de kullanıma sunulmuştur:

###  <a name="amqp-messaging-protocol"></a>AMQP mesajlaşma Protokolü

**1,2 düzeyi**: Azure Stream Analytics Service Bus kuyruklara ve konulara yazmak Için [Gelişmiş ileti sırası Protokolü (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) mesajlaşma protokolünü kullanır. AMQP, açık bir standart protokol kullanarak platformlar arası karma uygulamalar oluşturmanıza olanak sağlar.

### <a name="geospatial-functions"></a>Jeo-uzamsal işlevler

**Önceki düzeyler:** Azure Stream Analytics Coğrafya hesaplamaları kullanıldı.

**1,2 düzeyi:** Azure Stream Analytics, geometrik olarak tasarlanan coğrafi koordinatları hesaplamanıza olanak sağlar. Jeo-uzamsal işlevlerin imzasında değişiklik yoktur. Ancak semantiklerinden biraz farklıdır, daha önce daha kesin hesaplama yapılmasına izin verir.

Azure Stream Analytics Jeo-uzamsal başvuru verileri dizinlemeyi destekler. Jeo-uzamsal öğeleri içeren başvuru verileri daha hızlı bir JOIN hesaplaması için dizinlenebilir.

Güncelleştirilmiş Jeo-uzamsal işlevleri, tanınmış metin (WKT) Jeo-uzamsal biçiminin tam ifade düzeyini getirir. Daha önce GeoJson ile desteklenmeyen diğer Jeo-uzamsal bileşenleri belirtebilirsiniz.

Daha fazla bilgi için bkz. [bulut ve IoT Edge Azure Stream Analytics Jeo-uzamsal özelliklerine güncelleştirmeler](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Birden çok bölümlü giriş kaynakları için paralel sorgu yürütme

**Önceki düzeyler:** Azure Stream Analytics sorguları, giriş kaynak bölümleri arasında sorgu işleme paralel hale getirmek için bölüm BY yan tümcesinin kullanımını gerektirdi.

**1,2 düzeyi:** Sorgu mantığı giriş kaynak bölümleri arasında paralelleştirilmesine Azure Stream Analytics, ayrı sorgu örnekleri oluşturur ve hesaplamaları paralel olarak çalıştırır.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>CosmosDB çıkışıyla yerel toplu API Tümleştirmesi

**Önceki düzeyler:** Upsert davranış *INSERT veya Merge*.

**1,2 düzeyi:** CosmosDB çıkışıyla yerel toplu API Tümleştirmesi aktarım hızını en üst düzeye çıkarır ve azaltma isteklerini verimli bir şekilde işler. Daha fazla bilgi için [Azure Cosmos DB çıktıyı Azure Stream Analytics sayfasına](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)bakın.

Upsert davranış *INSERT veya Replace*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>SQL çıktısına yazarken DateTimeOffset

**Önceki düzeyler:** [DateTimeOffset](/sql/t-sql/data-types/datetimeoffset-transact-sql) türleri UTC olarak ayarlandı.

**1,2 düzeyi:** DateTimeOffset artık ayarlanmamış.

### <a name="long-when-writing-to-sql-output"></a>SQL çıktısına yazarken uzun

**Önceki düzeyler:** Değerler hedef türüne göre kesildi.

**1,2 düzeyi:** Hedef türüne sığmayan değerler, çıkış hata ilkesine göre işlenir.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>SQL çıktısına yazarken kayıt ve dizi serileştirme

**Önceki düzeyler:** Kayıtlar "kayıt" olarak yazıldı ve diziler "dizi" olarak yazılmıştır.

**1,2 düzeyi:** Kayıtlar ve diziler JSON biçiminde serileştirilir.

### <a name="strict-validation-of-prefix-of-functions"></a>İşlevlerin ön ekinin katı doğrulaması

**Önceki düzeyler:** İşlev ön eklerinin kesin doğrulaması yoktu.

**1,2 düzeyi:** Azure Stream Analytics, işlev öneklerinden oluşan katı bir doğrulamaya sahiptir. Yerleşik bir işleve ön ek eklemek hataya neden olur. Örneğin, `myprefix.ABS(…)` desteklenmez.

Yerleşik toplamalar için bir ön ek eklemek de hataya neden olur. Örneğin, `myprefix.SUM(…)` desteklenmez.

Kullanıcı tanımlı herhangi bir işlev için "sistem" önekini kullanmak hataya neden olur.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Cosmos DB çıkış bağdaştırıcısında anahtar özellikler olarak dizi ve nesneye izin verme

**Önceki düzeyler:** Dizi ve nesne türleri anahtar özelliği olarak desteklendi.

**1,2 düzeyi:** Dizi ve nesne türleri artık anahtar özelliği olarak desteklenmiyor.

## <a name="compatibility-level-11"></a>Uyumluluk düzeyi 1,1

Aşağıdaki büyük değişiklikler uyumluluk düzeyi 1,1 ' de kullanıma sunulmuştur:

### <a name="service-bus-xml-format"></a>Service Bus XML biçimi

**1,0 düzeyi:** İleti içeriği XML etiketleri dahil olmak üzere Azure Stream Analytics DataContractSerializer kullanılır. Örnek:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1,1 düzeyi:** İleti içeriği doğrudan ek etiket olmadan akışı içerir. Örnek: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Alan adları için büyük/küçük harf duyarlılığı

**1,0 düzeyi:** Alan adları, Azure Stream Analytics altyapısı tarafından işlendiğinde küçük harf olarak değiştirilmiştir.

**1,1 düzeyi:** büyük/küçük harf duyarlılığı, Azure Stream Analytics altyapısı tarafından işlendiğinde alan adları için kalıcıdır.

> [!NOTE]
> Edge ortamı kullanılarak barındırılan akış analitik işleri için kalıcı büyük/küçük harf duyarlılığı henüz kullanılamaz. Sonuç olarak, işiniz Edge üzerinde barındırılıyorsa tüm alan adları küçük harfe dönüştürülür.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1,0 düzeyi:** CREATE TABLE komut, olayları NaN (a-number değil) ile filtremedi. Örneğin, sonsuz,-sonsuzluk bir sütun türünde, bu sayılar için belgelenmiş aralığın dışında.

**1,1 düzeyi:** CREATE TABLE güçlü bir şema belirtmenize olanak tanır. Stream Analytics altyapısı, verilerin bu şemaya uygun olduğunu doğrular. Bu modelde, komut, olayları NaN değerleriyle filtreleyebilir.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>DateTime dizelerinin JSON için giriş konumundaki DateTime türüne otomatik dönüştürülmesini devre dışı bırak

**1,0 düzeyi:** JSON ayrıştırıcısı tarih/saat/bölge bilgileri ile dize değerlerini otomatik olarak tarih/saat/bölge bilgileriyle dönüştürür. böylece değer, özgün biçimlendirmesini ve saat dilimi bilgilerini hemen kaybeder. Bu, bu alan sorguda kullanılmasa bile, giriş sırasında yapıldığından UTC Tarih/saat biçimine dönüştürülür.

**1,1 düzeyi:** Tarih/saat/bölge bilgileri ile DATETIME türüne sahip dize değerleri otomatik olarak dönüştürülmez. Sonuç olarak, saat dilimi bilgileri ve özgün biçimlendirme tutulur. Ancak, NVARCHAR (MAX) alanı sorguda bir TARIH saat ifadesinin (örneğin, DATEADD işlevi) bir parçası olarak kullanılıyorsa, hesaplamayı gerçekleştirmek için DATETIME türüne dönüştürülür ve özgün formunu kaybeder.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş sorunlarını giderme](stream-analytics-troubleshoot-input.md)
* [Stream Analytics kaynak durumu](./stream-analytics-troubleshoot-query.md)
