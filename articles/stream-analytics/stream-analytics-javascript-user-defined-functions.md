---
title: JavaScript Kullanıcı tanımlı işlevleri Azure Stream Analytics
description: Bu makale, Stream Analytics içindeki JavaScript Kullanıcı tanımlı işlevlere giriş niteliğindedir.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 12/15/2020
ms.openlocfilehash: 70015ef24039694789ce96a6c4853221fe2377c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020392"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics 'de Kullanıcı tanımlı JavaScript işlevleri
 
Azure Stream Analytics, JavaScript dilinde yazılmış kullanıcı tanımlı işlevleri destekler. JavaScript’in sağladığı **String**, **RegExp**, **Math**, **Array** ve **Date** yöntemlerinden oluşan zengin küme sayesinde Stream Analytics işleriyle karmaşık veri dönüşümlerini oluşturmak daha kolay hale gelir.

## <a name="overview"></a>Genel Bakış

JavaScript Kullanıcı tanımlı işlevleri, dış bağlantı gerektirmeyen durum bilgisiz, yalnızca işlem olmayan skalar işlevleri destekler. Bir işlevin dönüş değeri yalnızca skaler (tek) değer olabilir. JavaScript kullanıcı tanımlı işlevini bir işe ekledikten sonra işlevi, yerleşik bir skaler değerli işlev gibi sorgunun herhangi bir yerinde kullanabilirsiniz.

JavaScript kullanıcı tanımlı işlevlerini yararlı bulabileceğiniz bazı senaryolar aşağıda verilmiştir:
* Normal ifade işlevlerine sahip dizeleri ayrıştırma ve düzenleme; örneğin, **Regexp_Replace()** and **Regexp_Extract()**
* İkili-onaltılık dönüşüm gibi verilerin kodunu çözme ve kodlama
* JavaScript **matematik** işlevleriyle matematik ve hesaplamalar yapma
* Sort, JOIN, Find ve Fill gibi dizi işlemleri yapma

Stream Analytics içinde JavaScript Kullanıcı tanımlı işleviyle yapaamıyoruz.
* Dış REST uç noktalarını çağırma, örneğin ters IP araması yapma veya bir dış kaynaktan başvuru verileri çekme
* Girdiler/çıktılar üzerinde özel durum serileştirme veya seri durumdan çıkarma işlemi gerçekleştirme
* Özel toplam değerler oluşturma

**Date. GetDate ()** veya **Math. random ()** gibi işlevler işlevler tanımında engellenmese de, bunları kullanmaktan kaçının. Bu işlevler, her çağırdığınızda aynı **sonucu döndürmez ve** Azure Stream Analytics hizmeti, işlev etkinleştirmeleri ve döndürülen sonuçları günlüğe kaydetmez. Bir işlev aynı olaylar üzerinde farklı sonuçlar döndürürse, siz veya Stream Analytics hizmeti tarafından bir iş yeniden başlatıldığında yinelenebilirlik garanti edilmez.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>İşinize Kullanıcı tanımlı JavaScript işlevi ekleme

> [!NOTE]
> Bu adımlar, bulutta çalışmak üzere yapılandırılmış Stream Analytics işleri üzerinde çalışır. Stream Analytics işiniz Azure IoT Edge çalışacak şekilde yapılandırıldıysa, Visual Studio 'Yu kullanın ve [C# kullanarak Kullanıcı tanımlı işlevi yazın](stream-analytics-edge-csharp-udf.md).

Stream Analytics işte JavaScript Kullanıcı tanımlı bir işlev oluşturmak için **Iş topolojisi** altında **işlevler** ' i seçin. Ardından + açılan menü **Ekle** menüsünden **JavaScript UDF** ' ı seçin. 

![JavaScript UDF ekleme](./media/javascript/stream-analytics-jsudf-add.png)

Ardından aşağıdaki özellikleri sağlamanız ve **Kaydet**' i seçmeniz gerekir.

|Özellik|Açıklama|
|--------|-----------|
|İşlev diğer adı|Sorgunuzun işlevini çağırmak için bir ad girin.|
|Çıkış türü|JavaScript Kullanıcı tanımlı işleviniz tarafından Stream Analytics sorgunuza döndürülecek tür.|
|İşlev tanımı|UDF 'inizin Sorgunuzla çağrılması her seferinde yürütülecek JavaScript işlevinizin uygulanması.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript UDF 'Leri test etme ve sorunlarını giderme 

JavaScript UDF mantığınızı herhangi bir tarayıcıda test edebilir ve hatalarını ayıklayabilirsiniz. Kullanıcı tanımlı bu işlevlerin mantığını hata ayıklama ve test etme işlemi şu anda Stream Analytics portalında desteklenmiyor. İşlev beklenen şekilde çalışırsa, yukarıda bahsedilen Stream Analytics işe ekleyebilirsiniz ve ardından doğrudan Sorgunuzla çağırabilirsiniz. [Visual Studio için Stream Analytics araçları](./stream-analytics-tools-for-visual-studio-install.md)'nı kullanarak sorgu mantığınızı JavaScript UDF ile test edebilirsiniz.

JavaScript çalışma zamanı hataları önemli kabul edilir ve Etkinlik günlüğünde öne çıkarılır. Günlüğü almak için Azure portalında işinize gidin ve **Etkinlik günlüğü**’nü seçin.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Bir sorguda JavaScript kullanıcı tanımlı işlevi çağırma

**Udf** ile önekli işlev diğer adını kullanarak sorgunuzda JavaScript işlevinizi kolayca çağırabilirsiniz. Onaltılık değerleri bir Stream Analytics sorgusunda Çağrılmakta olan tamsayıya dönüştüren bir JavaScript UDF örneği aşağıda verilmiştir.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Desteklenen JavaScript nesneleri

Azure Stream Analytics JavaScript kullanıcı tanımlı işlevleri standart, yerleşik JavaScript nesnelerini destekler. Bu nesnelerin bir listesi için bkz. [Genel Nesneler](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics ve JavaScript tür dönüşümü

Stream Analytics sorgu dili ile JavaScript’in desteklediği türler arasında farklılıklar vardır. Bu tabloda ikisi arasındaki dönüştürme eşlemeleri listelenmektedir:

Stream Analytics | JavaScript
--- | ---
bigint | Sayı (JavaScript yalnızca tam olarak 2^53’e kadar tamsayıları temsil edebilir)
DateTime | Tarih (JavaScript yalnızca milisaniye birimini destekler)
double | Sayı
nvarchar(MAX) | Dize
Kayıt | Nesne
Dizi | Dizi
NULL | Null

JavaScript’ten Stream Analytics’e dönüşümler aşağıda verilmiştir:

JavaScript | Stream Analytics
--- | ---
Sayı | Bigint (sayı yuvarlak ve long.MinValue ile long.MaxValue arasındaysa; aksi takdirde iki katıdır)
Tarih | DateTime
Dize | nvarchar(MAX)
Nesne | Kayıt
Dizi | Dizi
Null, Tanımsız | NULL
Başka bir tür (örneğin, bir işlev veya hata) | Desteklenmiyor (çalışma zamanı hatası ile sonuçlanır)

JavaScript dili, büyük/küçük harfe duyarlıdır ve JavaScript kodundaki nesne alanlarının büyük küçük harf ayrımına uymalıdır. Uyumluluk düzeyi 1,0 olan işler, SQL SELECT deyimindeki alanları küçük harfe dönüştürür. Uyumluluk düzeyi 1,1 ve üzeri sürümlerde, SELECT deyimindeki alanlar SQL sorgusunda belirtilen büyük küçük harflere sahip olacaktır.

## <a name="other-javascript-user-defined-function-patterns"></a>Diğer JavaScript kullanıcı tanımlı işlev desenleri

### <a name="write-nested-json-to-output"></a>Çıktıya iç içe geçmiş JSON yazma

Girdi olarak bir Stream Analytics iş çıktısını kullanan bir takip işleme adımınız varsa ve bir JSON biçimi gerektiriyorsa, çıktıya bir JSON dizesi yazabilirsiniz. Aşağıdaki örnekte girdinin tüm ad/değer çiftlerini paketlemek ve sonra bunları çıktıda tek bir dize olarak yazmak için **JSON.stringify()** işlevi çağrılmaktadır.

**JavaScript kullanıcı tanımlı işlev tanımı:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Örnek sorgu:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>Dizeyi işlenecek JSON nesnesine yayınla

JSON olan bir dize alanınız varsa ve bunu bir JavaScript UDF 'de işlenmek üzere bir JSON nesnesine dönüştürmek istiyorsanız, daha sonra kullanılabilecek bir JSON nesnesi oluşturmak için **JSON. Parse ()** işlevini kullanabilirsiniz.

**JavaScript kullanıcı tanımlı işlev tanımı:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Örnek sorgu:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Hata işleme için try/catch kullanın

Try/catch blokları, JavaScript UDF 'e geçirilen hatalı biçimlendirilmiş giriş verileriyle ilgili sorunları belirlemenize yardımcı olabilir.

**JavaScript kullanıcı tanımlı işlev tanımı:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Örnek sorgu: bir hata olduğunda dönebilmesi için kaydın tamamını ilk parametre olarak geçirin.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString ()
JavaScript 'teki **toLocaleString** yöntemi, bu yöntemin çağrıldığı tarih saat verilerini temsil eden dile duyarlı bir dize döndürmek için kullanılabilir.
Azure Stream Analtycs yalnızca UTC Tarih saatini sistem zaman damgası olarak kabul etse de, bu yöntem sistem zaman damgasını başka bir yerel ayara ve saat dilimine birlikte almak için kullanılabilir.
Bu yöntem, Internet Explorer 'da bulunan ile aynı uygulama davranışını izler.

**JavaScript kullanıcı tanımlı işlev tanımı:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**Örnek sorgu: tarih/saati giriş değeri olarak geçirme**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

Bu sorgunun çıktısı, belirtilen seçeneklerle birlikte de giriş tarih/saat **olarak da kullanılır** .
```
Samstag, 28. Dezember 2019
```

## <a name="user-logging"></a>Kullanıcı günlüğü
Günlüğe kaydetme mekanizması, bir iş çalışırken özel bilgileri yakalamanızı sağlar. Hata ayıklamak veya gerçek zamanlı olarak özel kodun doğruluğunu değerlendirmek için günlük verilerini kullanabilirsiniz. Bu mekanizma, Console. log () yöntemi aracılığıyla kullanılabilir.

```javascript
console.log('my error message');
```

Günlük iletilerine [tanılama günlükleri](data-errors.md)aracılığıyla erişebilirsiniz.
## <a name="next-steps"></a>Sonraki adımlar

* [Machine Learning UDF](./machine-learning-udf.md)
* [C# UDF](./stream-analytics-edge-csharp-udf-methods.md)
