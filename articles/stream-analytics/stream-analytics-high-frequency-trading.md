---
title: Azure Stream Analytics kullanarak yüksek frekanslı ticaret
description: Azure Stream Analytics işinde doğrusal regresyon modeli eğitimi ve puanlamasını gerçekleştirme.
services: stream-analytics
author: zhongc
ms.author: zhongc
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 9d3c1a730c34632403669794bdd97f95e3b3662d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925521"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Stream Analytics ile yüksek frekanslı alım-satım simülasyonu
Azure Stream Analytics’te SQL dili ve JavaScript kullanıcı tanımlı işlevleri (UDF) ile kullanıcı tanımlı toplamların (UDAs) birleşimi, kullanıcıların gelişmiş analiz gerçekleştirmesine olanak tanır. Gelişmiş analizler, çevrimiçi makine öğrenimi eğitim ve puanlamasının yanı sıra durum bilgisi olan işlem simülasyonunu içerebilir. Bu makalede, yüksek frekanslı bir alım-satım senaryosunda sürekli çalıştıran ve puanlama yapan bir Azure Stream Analytics işinde nasıl doğrusal regresyon gerçekleştirileceği açıklanır.

## <a name="high-frequency-trading"></a>Yüksek frekanslı alım-satım
Yüksek frekanslı alım-satımın mantıksal akışı şununla ilgilidir:
1. Bir menkul kıymetler borsasından gerçek zamanlı teklifler alma.
2. Fiyat hareketlerini öngörebilmemiz için tekliflerle ilgili tahmine dayalı bir model derleme.
3. Fiyat hareketlerinin başarılı tahminlerinden gelir elde etmek için satın alma veya satış siparişleri verme. 

Sonuç olarak şunlar gerekir:
* Gerçek zamanlı bir teklif akışı.
* Gerçek zamanlı teklifler üzerinde çalışabilecek bir tahmine dayalı model.
* Alım satım algoritmasının kar veya zarar durumunu gösteren bir alım satım simülasyonu.

### <a name="real-time-quote-feed"></a>Gerçek zamanlı teklif akışı
IEX, socket.io kullanarak ücretsiz [gerçek zamanlı teklif ve soru teklifleri](https://iextrading.com/developer/docs/#websockets) sunar. Gerçek zamanlı teklifleri almak ve veri kaynağı olarak Azure Event Hubs’a göndermek için basit bir konsol programı yazılabilir. Aşağıdaki kod, programın iskeletidir. Kod, kısa tutmak için hata işlemeyi atlar. Projenize SocketIoClientDotNet ve WindowsAzure.ServiceBus NuGet paketlerini de eklemeniz gerekir.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

Oluşturulan bazı örnek olaylar aşağıda verilmiştir:

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>Olayın zaman damgası **lastUpdated** şeklindedir (dönem zamanı cinsinden).

### <a name="predictive-model-for-high-frequency-trading"></a>Yüksek frekanslı alım-satım için tahmine dayalı model
Gösterim amacıyla, bu çalışmada Darryl Shen tarafından [makalesinde](https://docplayer.net/23038840-Order-imbalance-based-strategy-in-high-frequency-trading.html) anlatılan bir doğrusal model kullandık.

Hacim sipariş dengesizliği (VOI) geçerli teklif/satış fiyatı ve hacim ile son fiyat dalgalanmasından gelen teklif/satış fiyatı ve hacmin bir fonksiyonudur. Makale, VOI ile gelecekteki fiyat hareketi arasındaki bağıntıyı tanımlar. Geçmiş 5 VOI değeri ile son 10 fiyat dalgalanmasındaki fiyat değişikliği arasında doğrusal bir model oluşturur. Model, doğrusal regresyonla önceki günün verileri kullanılarak çalıştırılır. 

Çalıştırılan model, geçerli alım-satım günündeki teklifler üzerinden gerçek zamanlı fiyat değişikliği tahminleri yapmak için kullanılır. Yeterince büyük bir fiyat değişikliği tahmini elde edildiğinde, alım-satım işlemi yürürlüğe konur. Eşik ayarına bağlı olarak, bir alım-satım gününde tek bir hisse senedi için binlerce alım-satım yapılması beklenebilir.

![Birim sırası dengesizliği tanımı](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

Şimdi, Azure Stream Analytics işindeki çalıştırma ve tahmin işlemlerini ortaya koyalım.

İlk olarak, girişler temizlenir. Dönem zamanı **DATEADD** kullanılarak tarih saate dönüştürülür. Sorgu başarısız olmadan veri türlerini zorlamak için **TRY_CAST** kullanılır. Giriş alanlarını beklenen veri türlerine yöneltmek her zaman iyi bir yöntemdir; böylelikle, alanların işlenmesi veya karşılaştırılması sırasında beklenmedik davranışlar görülmez.

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

Ardından, son fiyat dalgalanmasından değerleri almak için **LAG** işlevini kullanırız. Rastgele olarak bir saatlik bir **LIMIT DURATION** değeri seçilir. Teklif frekansı dikkate alındığında, bir saat geriye bakarak bir önceki fiyat dalgalanmasını bulabileceğinizi varsayabiliriz.  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

Ardından VOI değerini hesaplayabiliriz. Önceki fiyat dalgalanmasının mevcut olmaması durumunda, null değerleri filtrenin dışında bırakırız.

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

Şimdi, **LAG** işlevini yeniden kullanarak 2 ardışık VOI değeri ve bunları izleyen 10 ardışık orta fiyat değerleriyle bir dizi oluştururuz.

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

Sonra iki değişkenli doğrusal model için verileri girişler halinde yeniden şekillendiririz. Tüm verilere sahip olmadığımız olayları yine filtrenin dışında bırakırız.

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Azure Stream Analytics'in yerleşik bir doğrusal regresyon işlevi olmadığından, doğrusal modelin katsayılarını hesaplamak için **SUM** ve **AVG** toplama işlevlerini kullanırız.

![Doğrusal regresyon matematik formülü](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

Geçerli olayın puanlamasında önceki günün modelini kullanmak için, teklifleri modelle birleştirmek istiyoruz. Ancak, model olayları ve teklif olaylarında **JOIN** yerine **UNION** işlemini kullanırız. Sonra **LAG** işlemini kullanarak, olayları önceki günün modeliyle tek olarak bir eşleşme alabilecek şekilde eşleştiriyoruz. Hafta sonu nedeniyle, üç gün geriye bakmamız gerekiyor. Basit bir **JOIN** kullansaydık, her teklif olayı için üç model elde ederdik.

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

Şimdi, modeli temel alarak 0,02 eşik değeriyle tahminlerde bulunabilir ve alım/satım sinyalleri oluşturabiliriz. Alım-satım değerinin 10 olması satın almayı ifade eder. Alım-satım değerinin -10 olması satışı ifade eder.

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>Alım-satım benzetimi
Alım-satım sinyallerimiz olduğunda, gerçekten alım-satım yapmadan alım-satım stratejisinin ne kadar efektif olduğunu test etmek isteriz. 

Dakikada bir kez atlayan bir atlamalı pencere ile UDA kullanarak bu testi gerçekleştiririz. Tarih üzerinde ek gruplandırma ve Having yan tümcesi, pencerenin yalnızca aynı güne ait olayları hesaba katmasına olanak tanır. İki güne yayılan bir atlamalı pencere için, tarihe göre **GROUP BY** kullanmak gruplandırmayı önceki gün ve geçerli gün olarak ayırır. **HAVING** yan tümcesi geçerli günde biten aman önceki günde gruplandırma yapan pencereleri filtrenin dışında bırakır.

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

JavaScript UDA `init` işlevindeki tüm biriktiricileri başlatır, pencereye eklenen her olayla birlikte durum geçişini hesaplar ve pencerenin sonunda simülasyon sonuçlarını döndürür. Genel alım-satım işleminin amaçları şunlardır:

- Satın alma sinyali alındığında ve hissedarlık olmadığında stok satın almak.
- Satış sinyali alındığında ve hissedarlık olduğunda stok satmak.
- Hissedarlık yoksa satmak. 

Satış pozisyonu söz konusuysa ve satın alma sinyali alınırsa, açığı kapatmak için alım yaparız. Bu simülasyonda hiçbir zaman 10 hisse tutmayız veya satmayız. İşlem maliyet tam 8 USD’dir.

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

Son olarak, görselleştirme için çıkışları Power BI panosuna veririz.

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![Grafik görselini Power BI](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![PNL Power BI grafik görseli](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>Özet
Gerçekçi bir yüksek frekanslı alım-satım modelini Azure Stream Analytics’te ortalama karmaşıklıkta bir sorguyla uygulayabiliriz. Yerleşik doğrusal regresyon işlevinin olmamasından dolayı, beş giriş değişkenini ikiye düşürerek modeli basitleştirmemiz gerekti. Bununla birlikte, kararlı bir kullanıcı için daha büyük boyutlu ve gelişmiş algoritmaların JavaScript UDA olarak da gerçekleştirilmesi mümkündür. 

JavaScript UDA’dan farklı olarak sorgunun büyük bölümünün Visual Studio’nun içinden [Visual Studio için Azure Stream Analytics araçları](stream-analytics-tools-for-visual-studio-install.md) kullanılarak test edilebileceğini ve hatalarının ayıklanabileceğini belirtmekte yarar var. İlk sorgu yazıldıktan sonra, yazar Visual Studio'da 30 dakikadan kısa bir sürede sorguyu test edebilir ve hatalarını ayıklayabilir. 

Şu anda, Visual Studio’da UDA’nın hata ayıklaması yapılamaz. JavaScript kodunun üzerinden geçme becerisiyle bunu etkinleştirmek için çalışmalarımız sürüyor. Ayrıca, UDA’ya ulaşan alanların adlarının küçük harfle başladığını unutmayın. Sorgu testi sırasında bu açıkça görülen bir davranış değildi. Ancak Azure Stream Analytics uyumluluk düzeyi 1.1 ile, daha doğal bir davranış için alan adındaki büyük/küçük harf kullanımını koruruz.

Bu makalenin tüm Azure Stream Analytics kullanıcılarına, hizmetimizi kullanarak neredeyse gerçek zamanlı, sürekli gelişmiş analiz yapabilecek herkese ilham vermesini umuyorum. İleri düzey analitik senaryolarında sorguları gerçekleştirmeyi kolaylaştırmak için geri bildirimlerinizi almak isteriz.
