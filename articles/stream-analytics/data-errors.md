---
title: Kaynak günlüğü veri hatalarını Azure Stream Analytics
description: Bu makalede Azure Stream Analytics kullanılırken oluşabilecek farklı giriş ve çıkış verileri hataları açıklanmaktadır.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 2ca289cd52b9a406e486ee9c186be683e71f02d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020120"
---
# <a name="azure-stream-analytics-data-errors"></a>Azure Stream Analytics veri hataları

Veri hataları, verileri işlerken oluşan hatalardır.  Bu hatalar genellikle veri serileştirme, serileştirme ve yazma işlemleri sırasında oluşur.  Veri hataları oluştuğunda, Stream Analytics ayrıntılı bilgileri ve örnek olayları kaynak günlüklerine yazar. Bu ek ayrıntıları almak için işinizdeki tanılama günlüklerini etkinleştirin. Bazı durumlarda, bu bilgilerin Özeti Portal bildirimleri aracılığıyla da sağlanır.

Bu makalede, giriş ve çıkış verileri hatalarına yönelik farklı hata türleri, nedenler ve kaynak günlüğü ayrıntıları özetlenmektedir.

## <a name="resource-logs-schema"></a>Kaynak günlükleri şeması

Kaynak günlüklerinin şemasını görmek için [tanılama günlüklerini kullanarak Azure Stream Analytics sorun giderme](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) bölümüne bakın. Aşağıdaki JSON, bir veri hatası için kaynak günlüğünün **Özellikler** alanı için örnek bir değerdir.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Giriş verileri hataları

### <a name="inputdeserializererrorinvalidcompressiontype"></a>Inputdeserializererror. ınvalidcompressiontype

* Neden: seçilen giriş sıkıştırma türü verilerle eşleşmiyor.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: Geçersiz sıkıştırma türü de dahil olmak üzere herhangi bir seri kaldırma hatası içeren Iletiler girişten bırakılır.
* Günlük ayrıntıları
   * Giriş iletisi tanımlayıcısı. Olay Hub 'ı için, tanımlayıcı PartitionID, konum ve sıra numarasıdır.

**Hata iletisi**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>Inputdeserializererror. ınvalidheader

* Neden: giriş verilerinin üstbilgisi geçersiz. Örneğin, bir CSV yinelenen adlara sahip sütunlara sahiptir.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: geçersiz üst bilgi dahil olmak üzere herhangi bir seri kaldırma hatası içeren Iletiler girişten bırakılır.
* Günlük ayrıntıları
   * Giriş iletisi tanımlayıcısı. 
   * En fazla kilobayt olan gerçek yük.

**Hata iletisi**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>Inputdeserializererror. MissingColumns

* Neden: CREATE TABLE veya ile zaman DAMGASı aracılığıyla tanımlanan giriş sütunları yok.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: eksik sütunları olan olaylar girişten bırakılır.
* Günlük ayrıntıları
   * Giriş iletisi tanımlayıcısı. 
   * Eksik olan sütunların adları. 
   * En fazla bir kilobayt kadar gerçek yük.

**Hata iletileri**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>Inputdeserializererror. TypeConversionError

* Neden: giriş, CREATE TABLE ifadesinde belirtilen türe dönüştürülemedi.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: tür dönüştürme hatası olan olaylar girişten bırakılır.
* Günlük ayrıntıları
   * Giriş iletisi tanımlayıcısı. 
   * Sütunun adı ve beklenen tür.

**Hata iletileri**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>Inputdeserializererror. ınvaliddata

* Neden: giriş verileri doğru biçimde değil. Örneğin, giriş geçerli bir JSON değil.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: geçersiz bir veri hatası ile karşılaşıldıktan sonra iletideki tüm olaylara giriş işleminden bırakılır.
* Günlük ayrıntıları
   * Giriş iletisi tanımlayıcısı. 
   * En fazla kilobayt olan gerçek yük.

**Hata iletileri**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>Invalidınputtimestamp

* Neden: ifadeye göre zaman DAMGASı değeri DateTime değerine dönüştürülemez.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: geçersiz giriş zaman damgasına sahip olaylar girişten bırakılır.
* Günlük ayrıntıları
   * Giriş iletisi tanımlayıcısı. 
   * Hata iletisi. 
   * En fazla kilobayt olan gerçek yük.

**Hata iletisi**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>Invalidınputtimestamp anahtarı

* Neden: TIMESTAMP sütununun ÜZERINDE zaman damgası değeri NULL.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: geçersiz giriş zaman damgası anahtarına sahip olaylar girişten bırakılır.
* Günlük ayrıntıları
   * En fazla kilobayt olan gerçek yük.

**Hata iletisi**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>Lateınputevent

* Neden: uygulama süresi ve varış süresi arasındaki fark geç varış toleransı penceresinden daha büyüktür.
* Portal bildirimi belirtildi: Hayır
* Kaynak günlük düzeyi: bilgi
* Etki: geç giriş olayları, iş yapılandırmasının olay sıralaması bölümünde "diğer olayları Işleme" ayarına göre işlenir. Daha fazla bilgi için bkz. [zaman Işleme ilkeleri](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Günlük ayrıntıları
   * Uygulama süresi ve varış süresi. 
   * En fazla kilobayt olan gerçek yük.

**Hata iletisi**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>Earlyınputevent

* Neden: uygulama süresi ve varış süresi arasındaki fark 5 dakikadan fazla.
* Portal bildirimi belirtildi: Hayır
* Kaynak günlük düzeyi: bilgi
* Etki: erken giriş olayları, iş yapılandırmasının olay sıralaması bölümünde "diğer olayları Işleme" ayarına göre işlenir. Daha fazla bilgi için bkz. [zaman Işleme ilkeleri](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Günlük ayrıntıları
   * Uygulama süresi ve varış süresi. 
   * En fazla kilobayt olan gerçek yük.

**Hata iletisi**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Neden: olay, sıralama dışı tolerans penceresi tanımlı olarak kabul edilir.
* Portal bildirimi belirtildi: Hayır
* Kaynak günlük düzeyi: bilgi
* Etki: sıra dışı olaylar, iş yapılandırmasının olay sıralaması bölümünde "diğer olayları Işleme" ayarına göre işlenir. Daha fazla bilgi için bkz. [zaman Işleme ilkeleri](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Günlük ayrıntıları
   * En fazla kilobayt olan gerçek yük.

**Hata iletisi**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Çıkış verileri hataları

Azure Stream Analytics, yapılandırmaya bağlı olarak çıkış havuzunda g/ç isteği ile veya olmayan çıkış verileri hatalarını tanımlayabilir. Örneğin,  `PartitionKey` Azure Tablo çıkışı kullanılırken, bir g/ç isteği olmadan bir gerekli sütun eksik olabilir. Ancak, SQL çıktısında kısıtlama ihlalleri bir g/ç isteği gerektirir.

Yalnızca çıkış havuzuna bir çağrı yapıldıktan sonra algılanamayan ve işlemeyi yavaşlatabilecek birkaç veri hatası vardır. Bu sorunu çözmek için, işinizin yapılandırmasını veya veri hatasına neden olan sorguyu değiştirin.

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError. RequiredColumnMissing yok

* Neden: çıktı için gereken sütun yok. Örneğin, Azure Table PartitionKey olarak tanımlanan bir sütun yok.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: eksik gerekli sütununu da içeren tüm çıkış verileri dönüştürme hataları [çıkış verileri ilkesi](./stream-analytics-output-error-policy.md) ayarına göre işlenir.
* Günlük ayrıntıları
   * Sütunun adı ve kayıt tanımlayıcısı ya da kaydın bir parçası.

**Hata iletisi**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError. Columnnamegeçersiz

* Neden: sütun değeri çıkışa uymuyor. Örneğin, sütun adı geçerli bir Azure Tablo sütunu değil.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: geçersiz sütun adı da dahil olmak üzere tüm çıkış verileri dönüştürme hataları [çıkış verileri ilkesi](./stream-analytics-output-error-policy.md) ayarına göre işlenir.
* Günlük ayrıntıları
   * Sütunun adı ve kayıt tanımlayıcı ya da kaydın bir parçası.

**Hata iletisi**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError. TypeConversionError

* Neden: bir sütun çıktıda geçerli bir türe dönüştürülemez. Örneğin, sütununun değeri SQL tablosunda tanımlı kısıtlamalar veya türle uyumlu değil.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: tür dönüştürme hatası da dahil olmak üzere tüm çıkış verileri dönüştürme hataları [çıkış verileri ilkesi](./stream-analytics-output-error-policy.md) ayarına göre işlenir.
* Günlük ayrıntıları
   * Sütunun adı.
   * Kayıt tanımlayıcı ya da kaydın bir parçası.

**Hata iletisi**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError. RecordExceededSizeLimit

* Neden: iletinin değeri desteklenen çıkış boyutundan daha büyük. Örneğin, bir kayıt bir olay hub 'ı çıkışı için 1 MB 'den büyük.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: kayıt boyutu sınırı da dahil olmak üzere tüm çıkış verileri dönüştürme hataları, [çıkış verileri ilkesi](./stream-analytics-output-error-policy.md) ayarına göre işlenir.
* Günlük ayrıntıları
   * Kayıt tanımlayıcı ya da kaydın bir parçası.

**Hata iletisi**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError. DuplicateKey

* Neden: bir kayıt zaten sistem sütunuyla aynı ada sahip bir sütun içeriyor. Örneğin, ID sütunu farklı bir sütuna ayarlandığında ID adlı bir sütunla CosmosDB çıkışı.
* Portal bildirimi belirtildi: Evet
* Kaynak günlük düzeyi: uyarı
* Etki: yinelenen anahtar dahil tüm çıkış verileri dönüştürme hataları [çıkış verileri ilkesi](./stream-analytics-output-error-policy.md) ayarına göre işlenir.
* Günlük ayrıntıları
   * Sütunun adı.
   * Kayıt tanımlayıcı ya da kaydın bir parçası.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Sonraki adımlar

* [Tanılama günlüklerini kullanarak Azure Stream Analytics sorunlarını giderme](stream-analytics-job-diagnostic-logs.md)

* [Stream Analytics iş izlemeyi ve sorguların nasıl izleneceğini anlayın](stream-analytics-monitoring.md)