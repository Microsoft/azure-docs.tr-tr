---
title: Azure Stream Analytics’te JavaScript kullanıcı tanımlı toplamları
description: Bu makalede, Azure Stream Analytics içinde JavaScript Kullanıcı tanımlı toplamalarda Gelişmiş sorgu mekanizması gerçekleştirme işlemi açıklanır.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 6c590ae62e080a6681e49c87264089f9a5f4ce2f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489527"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>JavaScript Kullanıcı tanımlı toplamaları Azure Stream Analytics
 
Azure Stream Analytics, JavaScript 'te yazılmış Kullanıcı tanımlı toplamaları (UDA) destekler, bu, karmaşık durum bilgisi olmayan iş mantığını uygulamanıza olanak sağlar. UDA içinde durum veri yapısı, durum birikmesi, durum ayırma ve Toplam Sonuç hesaplaması üzerinde tam denetime sahip olursunuz. Makale, iki farklı JavaScript UDA arabirimini, UDA oluşturma adımlarını ve Stream Analytics sorgusundaki pencere tabanlı işlemlerle UDA 'yı kullanmayı tanıtır.

## <a name="javascript-user-defined-aggregates"></a>JavaScript Kullanıcı tanımlı toplamalar

Kullanıcı tanımlı bir toplama, Bu penceredeki olayları toplamak ve tek bir sonuç değeri üretmek için zaman penceresi belirtiminin üstünde kullanılır. Stream Analytics bugün, AccumulateOnly ve AccumulateDeaccumulate tarafından desteklenen iki tür UDA arabirimi vardır. Her iki tür UDA, atlayan, atlamalı, kaydırma ve oturum penceresi tarafından kullanılabilir. AccumulateDeaccumulate UDA, hopping, kaydırma ve oturum penceresiyle birlikte kullanıldığında AccumulateOnly UDA 'dan daha iyi çalışır. Kullandığınız algoritmayı temel alan iki türden birini seçersiniz.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly toplamaları

AccumulateOnly toplamaları yalnızca durumunda yeni olayları biriktirilemez, algoritma değerlerin sıradan çıkmasına izin vermez. Durum değerinden bir olay bilgisinin çıkarılması mümkün olmadığında bu toplama türünü seçin. Aşağıda AccumulatOnly toplamaları için JavaScript şablonu verilmiştir:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate toplamaları

AccumulateDeaccumulate toplamaları, bir önceki birikmiş değerin durumundan birikmesine izin verir, örneğin, bir olay değerleri listesinden anahtar-değer çiftini kaldırır ya da toplam toplama durumundan bir değeri çıkarır. Aşağıda AccumulateDeaccumulate toplamaları için JavaScript şablonu verilmiştir:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA-JavaScript işlev bildirimi

Her JavaScript UDA bir Işlev nesnesi bildirimi tarafından tanımlanır. Aşağıda bir UDA tanımındaki ana öğeler verilmiştir.

### <a name="function-alias"></a>İşlev diğer adı

İşlev diğer adı UDA tanımlayıcısıdır. Stream Analytics sorgusunda çağrıldığında, her zaman "uda" ile birlikte UDA diğer adını kullanın. koy.

### <a name="function-type"></a>İşlev türü

UDA için işlev türü **JAVASCRIPT uda**olmalıdır.

### <a name="output-type"></a>Çıkış türü

Sorgudaki türü işlemek istiyorsanız, Stream Analytics işin desteklediği belirli bir tür veya "Any".

### <a name="function-name"></a>İşlev adı

Bu Işlev nesnesinin adı. İşlev adı UDA diğer adıyla eşleşmelidir.

### <a name="method---init"></a>Method-init ()

İnit () yöntemi toplamanın durumunu başlatır. Bu yöntem, pencere başlatıldığında çağrılır.

### <a name="method--accumulate"></a>Yöntem – topla ()

Birikme () yöntemi, önceki duruma ve geçerli olay değerlerine göre UDA durumunu hesaplar. Bu yöntem bir olay bir zaman penceresine girdiğinde çağrılır (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW veya SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Yöntem – debiriktir ()

Debirikme () yöntemi önceki duruma ve geçerli olay değerlerine göre durumu yeniden hesaplar. Bu yöntem, bir olay bir SLIDINGWINDOW veya SESSIONWINDOW 'tan ayrıldığında çağrılır.

### <a name="method--deaccumulatestate"></a>Yöntem – deaccumulateState ()

DeaccumulateState () yöntemi önceki duruma ve bir atlama durumuna göre durumu yeniden hesaplar. Bu yöntem, bir olay kümesi bir HOPPINGWINDOW 'tan ayrıldığında çağrılır.

### <a name="method--computeresult"></a>Yöntem – computeResult ()

ComputeResult () yöntemi, geçerli duruma göre toplam sonuç döndürür. Bu yöntem bir zaman penceresinin sonunda çağrılır (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW veya SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA desteklenen giriş ve çıkış veri türleri
JavaScript UDA veri türleri için, [JavaScript UDF 'Leri tümleştirme](stream-analytics-javascript-user-defined-functions.md)' ın bölüm **Stream Analytics ve JavaScript tür dönüştürmesi** ' ne bakın.

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Azure portal JavaScript UDA ekleme

Aşağıda, portaldan UDA oluşturma işlemini yürüyoruz. Burada kullandığımız örnek, zaman ağırlıklı ortalamalar hesaplanıyor.

Şimdi adımları izleyerek mevcut bir ASA işi altında bir JavaScript UDA oluşturalım.

1. Azure portal oturum açın ve mevcut Stream Analytics işinizi bulun.
1. Sonra **Iş topolojisi**altında işlevler bağlantısına tıklayın.
1. Yeni bir işlev eklemek için **Ekle** simgesine tıklayın.
1. Yeni Işlev görünümünde, Işlev türü olarak **JAVASCRIPT uda** ' yı seçin, ardından düzenleyicide görüntülenecek varsayılan uda şablonunu görürsünüz.
1. "TWA" öğesini UDA diğer adı olarak girin ve işlev uygulamasını aşağıdaki şekilde değiştirin:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. "Kaydet" düğmesine tıkladığınızda, UDA 'nın işlev listesinde görünür.

1. Yeni "TWA" işlevine tıklayın, işlev tanımını kontrol edebilirsiniz.

## <a name="calling-javascript-uda-in-asa-query"></a>ASA sorgusunda JavaScript UDA çağırma

Azure portal ve işinizi açın, sorguyu düzenleyin ve "uda." mantarih önekiyle birlikte TWA () işlevini çağırın. Örneğin:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>UDA ile sorgu test etme

Aşağıdaki içeriğe sahip yerel bir JSON dosyası oluşturun, dosyayı Stream Analytics işe yükleyin ve yukarıdaki sorguyu test edin.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Yardım alın

Ek yardım için [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics yönetim REST API başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
