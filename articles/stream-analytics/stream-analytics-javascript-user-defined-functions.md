---
title: JavaScript Kullanıcı tanımlı işlevleri Azure Stream Analytics
description: Bu öğreticide JavaScript kullanıcı tanımlı işlevleri ile gelişmiş sorgu mekanizmalarını uygulayacaksınız
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: 9ddf8a2a11cb863a0016726074c5279bfde96959
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990233"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Öğretici: Azure Stream Analytics JavaScript kullanıcı tanımlı işlevleri
 
Azure Stream Analytics, JavaScript dilinde yazılmış kullanıcı tanımlı işlevleri destekler. JavaScript’in sağladığı **String**, **RegExp**, **Math**, **Array** ve **Date** yöntemlerinden oluşan zengin küme sayesinde Stream Analytics işleriyle karmaşık veri dönüşümlerini oluşturmak daha kolay hale gelir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * JavaScript kullanıcı tanımlı işlevi tanımlama
> * İşlevi portala ekleme
> * İşlevi çalıştıran bir sorgu tanımlama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="javascript-user-defined-functions"></a>JavaScript kullanıcı tanımlı işlevleri
JavaScript kullanıcı tanımlı işlevleri, harici bağlantı gerektirmeyen durum bilgisiz, yalnızca işlem skaler değerli işlevleri destekler. Bir işlevin dönüş değeri yalnızca skaler (tek) değer olabilir. JavaScript kullanıcı tanımlı işlevini bir işe ekledikten sonra işlevi, yerleşik bir skaler değerli işlev gibi sorgunun herhangi bir yerinde kullanabilirsiniz.

JavaScript kullanıcı tanımlı işlevlerini yararlı bulabileceğiniz bazı senaryolar aşağıda verilmiştir:
* Normal ifade işlevlerine sahip dizeleri ayrıştırma ve düzenleme; örneğin, **Regexp_Replace()** and **Regexp_Extract()**
* İkili-onaltılık dönüşüm gibi verilerin kodunu çözme ve kodlama
* JavaScript **Math** işlevleriyle matematik hesaplamaları gerçekleştirme
* Sıralama, birleştirme, bulma ve doldurma gibi dizi işlemleri gerçekleştirme

Stream Analytics’te bir JavaScript kullanıcı tanımlı işlevi ile yapamayacağınız bazı işlemler aşağıda verilmiştir:
* Harici REST uç noktaları çağırma; örneğin, ters IP araması gerçekleştirme veya bir dış kaynaktan başvuru verilerini çekme
* Girdiler/çıktılar üzerinde özel durum serileştirme veya seri durumdan çıkarma işlemi gerçekleştirme
* Özel toplam değerler oluşturma

**Date.GetDate()** veya **Math.random()** gibi işlevler işlev tanımında engellenmese de bunları kullanmaktan kaçınmanız gerekir. Bu işlevler her çağırdığınızda aynı sonucu **vermezler** ve Azure Stream Analytics hizmeti, işlev çağrılarının ve döndürülen sonuçların kaydını tutmaz. Bir işlev aynı olaylar üzerinde farklı sonuçlar döndürürse, siz veya Stream Analytics hizmeti tarafından bir iş yeniden başlatıldığında tekrarlanabilirlik garanti edilmez.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Azure portalına JavaScript kullanıcı tanımlı işlevi ekleme
Mevcut bir Stream Analytics işi altında basit bir JavaScript Kullanıcı tanımlı işlevi oluşturmak için aşağıdaki adımları izleyin:

> [!NOTE]
> Bu adımlar, bulutta çalışmak üzere yapılandırılmış Stream Analytics işleri üzerinde çalışır. Stream Analytics işiniz Azure IoT Edge çalışacak şekilde yapılandırıldıysa, Visual Studio 'Yu kullanın ve [kullanarak C#Kullanıcı tanımlı işlevi yazın ](stream-analytics-edge-csharp-udf.md).

1.  Azure portalında Stream Analytics işinizi bulun.

2. **İş topolojisi** başlığı altında **işlevler**' i seçin. Boş bir işlevler listesi görüntülenir.

3.  Kullanıcı tanımlı yeni bir işlev oluşturmak için **+ Ekle**' yi seçin.

4.  **Yeni İşlev** dikey penceresinde **İşlev Türü** olarak **JavaScript**’i seçin. Düzenleyicide varsayılan bir işlev şablonu görüntülenir.

5.  **UDF diğer adı** için **hex2Int** girin ve işlev uygulamasını aşağıdaki gibi değiştirin:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  **Kaydet**’i seçin. İşleviniz işlevler listesinde görüntülenir.
7.  Yeni **hex2Int** işlevini seçin ve işlev tanımını denetleyin. Tüm işlevlerde işlev diğer adına bir **UDF** ön eki getirilmelidir. İşlevi Stream Analytics sorgunuzda çağırdığınızda *ön eki getirmeniz* gerekir. Bu durumda **UDF.hex2Int** adını çağırırsınız.

## <a name="testing-javascript-udfs"></a>JavaScript UDF 'Leri test etme 
JavaScript UDF mantığınızı herhangi bir tarayıcıda test edebilir ve hatalarını ayıklayabilirsiniz. Kullanıcı tanımlı bu işlevlerin mantığını hata ayıklama ve test etme işlemi şu anda Stream Analytics portalında desteklenmiyor. İşlev beklenen şekilde çalışırsa, yukarıda bahsedilen Stream Analytics işe ekleyebilirsiniz ve ardından doğrudan Sorgunuzla çağırabilirsiniz.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Bir sorguda JavaScript kullanıcı tanımlı işlevi çağırma

1. Sorgu Düzenleyicisi 'nde, **iş topolojisi** başlığı altında **sorgu**' yı seçin.
2.  Sorgunuzu düzenleyin ve sonra aşağıdaki gibi kullanıcı tanımlı işlevi çağırın:

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Örnek veri dosyasını karşıya yüklemek için iş girdisine sağ tıklayın.
4.  Sorgunuz test etmek için **Test**’i seçin.


## <a name="supported-javascript-objects"></a>Desteklenen JavaScript nesneleri
Azure Stream Analytics JavaScript kullanıcı tanımlı işlevleri standart, yerleşik JavaScript nesnelerini destekler. Bu nesnelerin bir listesi için bkz. [Genel Nesneler](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics ve JavaScript tür dönüşümü

Stream Analytics sorgu dili ile JavaScript’in desteklediği türler arasında farklılıklar vardır. Bu tabloda ikisi arasındaki dönüştürme eşlemeleri listelenmektedir:

Akış Analizi | JavaScript
--- | ---
bigint | Sayı (JavaScript yalnızca tam olarak 2^53’e kadar tamsayıları temsil edebilir)
Tarih Saat | Tarih (JavaScript yalnızca milisaniye birimini destekler)
double | Sayı
nvarchar(MAX) | Dize
Kayıt | Nesne
Dizi | Dizi
NULL | Null


JavaScript’ten Stream Analytics’e dönüşümler aşağıda verilmiştir:


JavaScript | Akış Analizi
--- | ---
Sayı | Bigint (sayı yuvarlak ve long.MinValue ile long.MaxValue arasındaysa; aksi takdirde iki katıdır)
Tarih | Tarih Saat
Dize | nvarchar(MAX)
Nesne | Kayıt
Dizi | Dizi
Null, Tanımsız | NULL
Başka bir tür (örneğin, bir işlev veya hata) | Desteklenmiyor (çalışma zamanı hatası ile sonuçlanır)

JavaScript dili, büyük/küçük harfe duyarlıdır ve JavaScript kodundaki nesne alanlarının büyük küçük harfe eşit olması gerekir. Uyumluluk düzeyi 1,0 olan işlerin, SQL SELECT deyimindeki alanları küçük harfe dönüştürdüğüne lütfen unutmayın. Uyumluluk düzeyi 1,1 ve üzeri sürümlerde, SELECT deyimindeki alanlar SQL sorgusunda belirtilen büyük küçük harflere sahip olacaktır.

## <a name="troubleshooting"></a>Sorun giderme
JavaScript çalışma zamanı hataları önemli kabul edilir ve Etkinlik günlüğünde öne çıkarılır. Günlüğü almak için Azure portalında işinize gidin ve **Etkinlik günlüğü**’nü seçin.

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
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, akış işini ve tüm ilgili kaynakları silin. İşin silinmesi, iş tarafından kullanılan akış birimlerinin faturalanmasını önler. İşi gelecekte kullanmayı planlıyorsanız, durdurup daha sonra gerektiğinde yeniden başlatabilirsiniz. Bu işi kullanmaya devam etmeyecekseniz aşağıdaki adımları kullanarak bu hızlı başlangıçla oluşturulan tüm kaynakları silin:

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**'na ve ardından oluşturduğunuz kaynağın adına tıklayın.  
2. Kaynak grubu sayfanızda, **Sil**'e tıklayın, metin kutusuna silinecek kaynağın adını yazın ve ardından **Sil**'e tıklayın.

## <a name="get-help"></a>Yardım alın
Ek yardım için [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) deneyin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide basit bir JavaScript kullanıcı tanımlı işlevi çalıştıran bir Stream Analytics işi oluşturdunuz. Stream Analytics hakkında daha fazla bilgi için gerçek zamanlı senaryo makalelerine geçin:

> [!div class="nextstepaction"]
> [Azure Stream Analytics’te gerçek zamanlı Twitter yaklaşım analizi](stream-analytics-twitter-sentiment-analysis-trends.md)
