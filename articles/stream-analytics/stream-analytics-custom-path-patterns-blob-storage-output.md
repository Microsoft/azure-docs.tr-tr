---
title: Özel blob çıkış bölümlendirme Azure Stream Analytics
description: Bu makalede, Azure Stream Analytics işlerden BLOB depolama çıktısı için özel tarih/saat yol desenleri ve özel alan veya öznitelikler özellikleri açıklanmaktadır.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: cb9d8edd24dcc8809f2b207a4db80653b0e140e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98014045"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Özel blob çıkış bölümlendirme Azure Stream Analytics

Azure Stream Analytics özel alanlar veya öznitelikler ve özel tarih saat yolu desenleriyle özel blob çıkış bölümlendirme destekler. 

## <a name="custom-field-or-attributes"></a>Özel alan veya öznitelikler

Özel alan veya giriş öznitelikleri, çıkış üzerinde daha fazla denetime izin vererek aşağı akış veri işleme ve raporlama iş akışlarını geliştirir.

### <a name="partition-key-options"></a>Bölüm anahtarı seçenekleri

Giriş verilerini bölümlemek için kullanılan bölüm anahtarı veya sütun adı, [BLOB adları](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)için kabul edilen herhangi bir karakteri içerebilir. Diğer adlarla birlikte kullanılmamışsa, iç içe geçmiş alanları bölüm anahtarı olarak kullanmak mümkün değildir, ancak bir dosya hiyerarşisi oluşturmak için belirli karakterleri kullanabilirsiniz. Örneğin, benzersiz bir bölüm anahtarı oluşturmak için iki diğer sütundaki verileri birleştiren bir sütun oluşturmak için aşağıdaki sorguyu kullanabilirsiniz.

```sql
SELECT name, id, CONCAT(name, "/", id) AS nameid
```

Bölüm anahtarı NVARCHAR (MAX), BIGINT, FLOAT veya BIT (1,2 uyumluluk düzeyi veya üzeri) olmalıdır. DateTime, dizi ve kayıt türleri desteklenmez, ancak dizelere dönüştürülürse bölüm anahtarları olarak kullanılabilir. Daha fazla bilgi için bkz. [Azure Stream Analytics veri türleri](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Örnek

Bir işin, oturumları belirlemek için bir sütun **client_id** içeren bir dış video oyun hizmetine bağlı canlı kullanıcı oturumlarından giriş verileri aldığını varsayalım. Verileri **client_id** göre bölümlemek Için blob yol deseninin alanını, bir iş oluştururken blob çıkış özelliklerine bir **{client_id}** bölüm belirteci içerecek şekilde ayarlayın. Çeşitli **client_id** değerleri olan veriler Stream Analytics iş aracılığıyla akar, çıkış verileri her klasör için tek bir **client_id** değerine göre ayrı klasörlere kaydedilir.

![İstemci kimlikli yol deseninin](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Benzer şekilde, iş girişi her bir sensör **sensor_id** sahip milyonlarca sensörden veri algılayıcısı ise, her algılayıcı verisini farklı klasörlere bölümlemek Için yol deseninin **{sensor_id}** olması gerekir.  


REST API kullanarak, bu istek için kullanılan JSON dosyasının çıkış bölümü aşağıdaki gibi görünebilir:  

![REST API çıkışı](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

İş çalışmaya başladıktan sonra, *istemciler* kapsayıcısı aşağıdaki gibi görünebilir:  

![İstemci kapsayıcısı](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Her bir dizin, her Blobun bir veya daha fazla kayıt içerdiği birden çok blob içerebilir. Yukarıdaki örnekte, "06000000" etiketli bir klasörde aşağıdaki içeriğe sahip tek bir blob vardır:

![Blob içeriği](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Çıkış yolundaki çıktıyı bölümlemek için kullanılan sütun **client_id** olduğundan, blobdaki her kaydın klasör adıyla eşleşen bir **client_id** sütunu olduğunu unutmayın.

### <a name="limitations"></a>Sınırlamalar

1. Yol deseninin blob output özelliğinde yalnızca bir özel bölüm anahtarına izin verilir. Aşağıdaki yol desenlerinin tümü geçerlidir:

   * Cluster1/{Date}/{Afielınvomydata}  
   * Cluster1/{Time}/{Afielınvomydata}  
   * Cluster1/{Afielınvomydata}  
   * Cluster1/{Date}/{Time}/{Afielınvomydata} 
   
2. Bölüm anahtarları büyük/küçük harfe duyarlıdır, bu nedenle "John" ve "John" gibi bölüm anahtarları eşdeğerdir. Ayrıca, ifadeler bölüm anahtarı olarak kullanılamaz. Örneğin, **{columnA + columnB}** çalışmıyor.  

3. Bir giriş akışı 8000 altında bölüm anahtarı kardinalitesi olan kayıtlardan oluşuyorsa, kayıtlar mevcut bloblara eklenir ve gerektiğinde yalnızca yeni blob 'lar oluşturulur. Kardinalite 8000 üzerinde ise, var olan Blobların yazılacağı garantisi yoktur ve aynı bölüm anahtarına sahip rastgele sayıda kayıt için yeni Bloblar oluşturulmaz.

4. Blob çıktısı [sabit olarak yapılandırılırsa](../storage/blobs/storage-blob-immutable-storage.md)Stream Analytics, veri her gönderilirken yeni bir blob oluşturur.

## <a name="custom-datetime-path-patterns"></a>Özel DateTime yol desenleri

Özel tarih saat yolu desenleri, Hive akış kuralları ile hizalanan bir çıkış biçimi belirtmenize olanak sağlar. bu sayede Azure HDInsight 'a veri gönderme ve aşağı akış işleme için Azure Databricks Azure Stream Analytics sağlarsınız. Özel tarih saat yolu desenleri, `datetime` BLOB çıktınızın yol ön eki alanındaki anahtar sözcüğü kullanılarak kolayca uygulanır, Biçim belirleyicisi ile birlikte. Örneğin, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Desteklenen belirteçler

Aşağıdaki biçim belirteci belirteçleri özel tarih saat biçimlerini elde etmek için tek başına veya birlikte kullanılabilir:

|Biçim belirteci   |Description   |Sonuç örnek zaman 2018-01-02T10:06:08|
|----------|-----------|------------|
|{DateTime: yyyy}|Dört basamaklı bir sayı olarak yıl|2018|
|{DateTime: dd}|01 ile 12 arasında bir ay|01|
|{DateTime: d}|Ay 1 ile 12 arasında|1|
|{DateTime: dd}|01 ile 31 arasında gün|02|
|{DateTime: d}|1 ile 31 arasında gün|2|
|{DateTime: HH}|00 ile 23 arasında 24 saat biçimini kullanan saat|10|
|{DateTime: dd}|00 ile 60 arasında dakika|06|
|{DateTime: d}|0 ile 60 arasında dakika|6|
|{DateTime: ss}|00 ile 60 arasında saniye|08|

Özel tarih saat desenleri kullanmak istemiyorsanız, yerleşik DateTime biçimleriyle bir açılan menü oluşturmak için yol ön ekine {Date} ve/veya {Time} belirtecini ekleyebilirsiniz.

![Eski DateTime biçimlerini Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Genişletilebilirlik ve kısıtlamalar

Yol `{datetime:<specifier>}` başına karakter sınırına ulaşana kadar, yol düzeninde istediğiniz sayıda belirteç kullanabilirsiniz. Biçim belirticileri, tarih ve saat açılan listeleri tarafından zaten listelenen kombinasyonlardan daha fazla tek bir belirteç içinde birleştirilemez. 

Bir yol bölümü için `logs/MM/dd` :

|Geçerli ifade   |Geçersiz ifade   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Yol önekinde aynı biçim belirticisini birden çok kez kullanabilirsiniz. Belirtecin her seferinde yinelenmesi gerekir.

### <a name="hive-streaming-conventions"></a>Hive akış kuralları

BLOB depolama için özel yol desenleri, klasör adında klasörlerin etiketlenmesi beklenen Hive akış kuralıyla birlikte kullanılabilir `column=` .

Örneğin, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Özel çıkış, tabloları değiştirme ve Azure Stream Analytics ile Hive arasında bağlantı noktası verilerine el ile bölüm ekleme sorunlarını ortadan kaldırır. Bunun yerine, pek çok klasör kullanılarak otomatik olarak eklenebilir:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Örnek

[Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) hızlı başlangıç kılavuzuna göre bir depolama hesabı, bir kaynak grubu, Stream Analytics işi ve bir giriş kaynağı oluşturun. [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)'da da bulunan hızlı başlangıç kılavuzunda kullanılan örnek verileri kullanın.

Aşağıdaki yapılandırmayla bir blob çıkış havuzu oluşturun:

![Stream Analytics blob çıkış Havuzu Oluştur](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Tam yol stili aşağıdaki gibidir:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


İşi başlattığınızda, blob kapsayıcıda yol düzenine dayalı bir klasör yapısı oluşturulur. Gün düzeyinde ayrıntıya gidebilirsiniz.

![Özel yol düzeniyle blob çıkışı Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream Analytics çıkışlarını anlayın](stream-analytics-define-outputs.md)
