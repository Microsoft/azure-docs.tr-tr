---
title: Uyumluluk düzeylerini Azure Stream Analytics
description: Azure Stream Analytics işi için bir uyumluluk düzeyi ayarlamayı ve en son uyumluluk düzeyindeki önemli değişiklikleri nasıl ayarlayacağınızı öğrenin
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 888c1f0bb38a5317cc27790ea47917c182d49593
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925636"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics işleri için uyumluluk düzeyi

Bu makalede, Azure Stream Analytics uyumluluk düzeyi seçeneği açıklanmaktadır. Stream Analytics, düzenli özellik güncelleştirmeleri ve performans iyileştirmeleri içeren bir yönetilen hizmettir. Hizmetin çalışma zamanları güncelleştirmelerinin çoğu, son kullanıcılar için otomatik olarak kullanılabilir hale getirilir. 

Ancak, hizmette bulunan bazı yeni işlevler, var olan bir işin davranışında değişiklik veya çalışan işlerde verilerin tüketilme biçiminde bir değişiklik gibi önemli bir değişikliğe neden olabilir. Uyumluluk düzeyi ayarını düşürülemez ve bu durumda, büyük değişiklikler olmadan çalışan mevcut Stream Analytics işlerinizi koruyabilirsiniz. En son çalışma zamanı davranışları için hazırsanız, uyumluluk düzeyini yükselterek kabul edebilirsiniz. 

## <a name="choose-a-compatibility-level"></a>Uyumluluk düzeyi seçin

Uyumluluk düzeyi bir Stream Analytics işinin çalışma zamanı davranışını denetler. 

Azure Stream Analytics Şu anda üç uyumluluk düzeyini desteklemektedir:

* 1,0-önceki davranış
* 1,1-varsayılan davranış
* 1,2 (Önizleme)-değerlendirmede en son iyileştirmeler ile en yeni davranış

Özgün 1,0 uyumluluk düzeyi, birkaç yıl önce Azure Stream Analytics genel kullanıma sunulmasıyla tanıtılmıştır.

Yeni bir Stream Analytics iş oluşturduğunuzda, en son uyumluluk düzeyini kullanarak oluşturmak en iyi uygulamadır. Daha sonra eklenmiş değişiklik ve karmaşıklığın önüne geçmek için en son davranışlardan sonra iş tasarımınızı başlatın.

## <a name="set-the-compatibility-level"></a>Uyumluluk düzeyini ayarlama

Stream Analytics işi için uyumluluk düzeyini Azure portal veya [iş oluştur REST API çağrısını](/rest/api/streamanalytics/stream-analytics-job)kullanarak ayarlayabilirsiniz.

Azure portal işin uyumluluk düzeyini güncelleştirmek için:

1. Stream Analytics işinizi bulmak için [Azure Portal](https://portal.azure.com) kullanın.
2. Uyumluluk düzeyini güncelleştirmeden önce işi **durdurun** . İşiniz çalışır durumdaysa uyumluluk düzeyini güncelleştiremezsiniz.
3. **Yapılandır** başlığı altında, **Uyumluluk düzeyi**' ni seçin.
4. İstediğiniz uyumluluk düzeyi değerini seçin.
5. Sayfanın alt kısmındaki **Kaydet** ' i seçin.

![Azure portal Stream Analytics uyumluluk düzeyi](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Uyumluluk düzeyini güncelleştirdiğinizde T-SQL derleyicisi, işi seçilen uyumluluk düzeyine karşılık gelen sözdizimiyle doğrular.

## <a name="compatibility-level-12-preview"></a>Uyumluluk düzeyi 1,2 (Önizleme)

Aşağıdaki büyük değişiklikler uyumluluk düzeyi 1,2 ' de kullanıma sunulmuştur:

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

**1,2 düzeyi:** CosmosDB çıkışıyla yerel toplu API Tümleştirmesi aktarım hızını en üst düzeye çıkarır ve azaltma isteklerini verimli bir şekilde işler. Daha fazla bilgi için [Azure Cosmos DB çıktıyı Azure Stream Analytics sayfasına](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)bakın.

Upsert davranış *INSERT veya Replace*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>SQL çıktısına yazarken DateTimeOffset

**Önceki düzeyler:** [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) türleri UTC olarak ayarlandı.

**1,2 düzeyi:** DateTimeOffset artık ayarlanmamış.

### <a name="long-when-writing-to-sql-output"></a>SQL çıktısına yazarken uzun

**Önceki düzeyler:** Değerler hedef türüne göre kesildi.

**1,2 düzeyi:** Hedef türüne sığmayan değerler, çıkış hata ilkesine göre işlenir.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>SQL çıktısına yazarken kayıt ve dizi serileştirme

**Önceki düzeyler:** Kayıtlar "kayıt" olarak yazıldı ve diziler "dizi" olarak yazılmıştır.

**1,2 düzeyi:** Kayıtlar ve diziler JSON biçiminde serileştirilir.

### <a name="strict-validation-of-prefix-of-functions"></a>İşlevlerin ön ekinin katı doğrulaması

**Önceki düzeyler:** İşlev ön eklerinin kesin doğrulaması yoktu.

**1,2 düzeyi:** Azure Stream Analytics, işlev öneklerinden oluşan katı bir doğrulamaya sahiptir. Yerleşik bir işleve ön ek eklemek hataya neden olur. Örneğin`myprefix.ABS(…)` desteklenmez.

Yerleşik toplamalar için bir ön ek eklemek de hataya neden olur. Örneğin `myprefix.SUM(…)` desteklenmez.

Kullanıcı tanımlı herhangi bir işlev için "sistem" önekini kullanmak hataya neden olur.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Cosmos DB çıkış bağdaştırıcısında anahtar özellikler olarak dizi ve nesneye izin verme

**Önceki düzeyler:** Dizi ve nesne türleri anahtar özelliği olarak desteklendi.

**1,2 düzeyi:** Dizi ve nesne türleri artık anahtar özelliği olarak desteklenmiyor.

## <a name="compatibility-level-11"></a>Uyumluluk düzeyi 1,1

Aşağıdaki büyük değişiklikler uyumluluk düzeyi 1,1 ' de kullanıma sunulmuştur:

### <a name="service-bus-xml-format"></a>Service Bus XML biçimi

**1,0 düzeyi:** İleti içeriği XML etiketleri dahil olmak üzere Azure Stream Analytics DataContractSerializer kullanılır. Örnek:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1,1 düzeyi:** İleti içeriği doğrudan ek etiket olmadan akışı içerir. Örneğin, `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Alan adları için büyük/küçük harf duyarlılığı

**1,0 düzeyi:** Alan adları, Azure Stream Analytics altyapısı tarafından işlendiğinde küçük harf olarak değiştirilmiştir.

**1,1 düzeyi:** büyük/küçük harf duyarlılığı, Azure Stream Analytics altyapısı tarafından işlendiğinde alan adları için kalıcıdır.

> [!NOTE]
> Edge ortamı kullanılarak barındırılan akış analitik işleri için kalıcı büyük/küçük harf duyarlılığı henüz kullanılamaz. Sonuç olarak, işiniz Edge üzerinde barındırılıyorsa tüm alan adları küçük harfe dönüştürülür.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1,0 düzeyi:** CREATE TABLE komut, olayları NaN (a-number değil) ile filtremedi. Örneğin, sonsuz,-sonsuzluk bir sütun türünde, bu sayılar için belgelenmiş aralığın dışında.

**1,1 düzeyi:** CREATE TABLE güçlü bir şema belirtmenize olanak tanır. Stream Analytics altyapısı, verilerin bu şemaya uygun olduğunu doğrular. Bu modelde, komut, olayları NaN değerleriyle filtreleyebilir.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>JSON 'da DateTime dizeleri için otomatik yukarı dönüştürmeyi devre dışı bırak

**1,0 düzeyi:** JSON ayrıştırıcısı tarih/saat/bölge bilgileri ile dize değerlerini otomatik olarak tarih/saat/bölge bilgileriyle yukarı aktarır ve UTC 'ye dönüştürür. Bu davranış, saat dilimi bilgilerini kaybetme ile sonuçlandı.

**1,1 düzeyi:** Tarih/saat/bölge bilgileriyle DateTime türüne sahip dize değerleri için otomatik olarak başka bir dönüştürme yoktur. Sonuç olarak, saat dilimi bilgileri tutulur.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics giriş sorunlarını giderme](stream-analytics-troubleshoot-input.md)
* [Stream Analytics kaynak durumu](stream-analytics-resource-health.md)
