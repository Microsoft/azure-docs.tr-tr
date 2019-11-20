---
title: Azure Veri Gezgini verilerini sorgulamak ve görselleştirmek için Power BI kullanmaya yönelik en iyi uygulamalar
description: Bu makalede, Azure Veri Gezgini verilerini sorgulamak ve görselleştirmek için Power BI kullanmaya yönelik en iyi yöntemleri öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024026"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Azure Veri Gezgini verilerini sorgulamak ve görselleştirmek için Power BI kullanmaya yönelik en iyi uygulamalar

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. [Power BI](https://docs.microsoft.com/power-bi/) , verilerinizi görselleştirmenizi ve sonuçları kuruluşunuz genelinde paylaşmanızı sağlayan bir iş analizi çözümüdür. Azure Veri Gezgini Power BI verilere bağlanmak için üç seçenek sunar. [Yerleşik bağlayıcıyı](power-bi-connector.md)kullanın, [Azure Veri Gezgini bir sorguyu Power BI içine aktarın](power-bi-imported-query.md)veya bir [SQL sorgusu](power-bi-sql-query.md)kullanın. Bu makale, Azure Veri Gezgini verilerinizi Power BI ile sorgulama ve görselleştirme için ipuçları sağlar. 

## <a name="best-practices-for-using-power-bi"></a>Power BI kullanmak için en iyi uygulamalar 

En yeni terabaytlarca ham veri ile çalışırken, Power BI panoları ve raporları Snappy ve güncel tutmak için aşağıdaki yönergeleri izleyin:

* **Seyahat ışığı** -Power BI için yalnızca raporlarınız için gereken verileri getirin. Ayrıntılı etkileşimli analizler için, kusto sorgu diliyle geçici araştırma için optimize edilmiş [Azure Veri Gezgini Web Kullanıcı arabirimini](web-query-data.md) kullanın.

* **Bileşik model** -en üst düzey panolar için toplanan verileri filtrelenmiş işlemsel ham verilerle birleştirmek için [bileşik model](https://docs.microsoft.com/power-bi/desktop-composite-models) kullanın. Ham verilerin ne zaman kullanılacağını ve toplu bir görünümün ne zaman kullanılacağını açıkça tanımlayabilirsiniz. 

* **Içeri aktarma modu, DirectQuery moduna karşı** -küçük veri kümelerinin etkileşimi Için **içeri aktarma** modunu kullanın. Büyük, sık güncellenen veri kümeleri için **DirectQuery** modunu kullanın. Örneğin, küçük olduklarından ve sık değişlemediğinden **Içeri aktarma** modunu kullanarak boyut tabloları oluşturun. Yenileme aralığını, beklenen veri güncelleştirmeleri hızına göre ayarlayın. Bu tablolar büyük olduğundan ve ham veriler içerdiğinden **DirectQuery** modunu kullanarak olgu tabloları oluşturun. Power BI [detaylandırma](https://docs.microsoft.com/power-bi/desktop-drillthrough)kullanarak filtrelenmiş verileri sunmak için bu tabloları kullanın.

* **Paralellik** – Azure Veri Gezgini, doğrusal bir şekilde ölçeklenebilir bir veri platformudur, bu nedenle, uçtan uca akışın paralelliğini aşağıdaki gibi artırarak Pano işlemenin performansını artırabilirsiniz:

   * [Power BI ' de DirectQuery içindeki eşzamanlı bağlantı](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)sayısını artırın.

   * [Paralellik kalitesini artırmak için zayıf tutarlılığı](/azure/kusto/concepts/queryconsistency)kullanın. Bu, verilerin yeniliği üzerinde bir etkiye sahip olabilir.

* **Etkin Dilimleyiciler** : raporların, kullanıma almadan verileri yüklemesini engellemek için [eşitleme Dilimleyicileri](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) kullanın. Veri kümesini yapılandırdıktan sonra tüm görselleri yerleştirip tüm Dilimleyicileri işaretledikten sonra yalnızca gerekli verileri yüklemek için eşitleme Dilimleyiciyi seçebilirsiniz.

* **Filtreleri kullanma** -Azure Veri Gezgini aramasını ilgili veri parçalarında odaklamak için olabildiğince çok Power BI filtresi kullanın.

* **Verimli görseller** : verileriniz için en iyi performansa sahip görselleri seçin.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Verileri sorgulamak için Power BI için Azure Veri Gezgini bağlayıcısını kullanmaya yönelik ipuçları

Aşağıdaki bölümde, Power BI ile kusto sorgu dilini kullanmaya yönelik ipuçları ve püf noktaları yer almaktadır. Verileri görselleştirmek için [Azure Veri Gezgini bağlayıcısını Power BI](power-bi-connector.md) kullanma

### <a name="complex-queries-in-power-bi"></a>Power BI 'de karmaşık sorgular

Karmaşık sorgular, kusto içinde Power Query kıyasla daha kolay bir şekilde ifade edilir. [Kusto işlevleri](/azure/kusto/query/functions)olarak uygulanmalıdır ve Power BI çağrılır. Kusto sorgunuzda **DirectQuery** `let` deyimleri kullanılırken bu yöntem gereklidir. Power BI iki sorguyu birleştiğinden ve `let` deyimleri `join` işleciyle birlikte kullanılamaz, söz dizimi hataları oluşabilir. Bu nedenle, birleştirmenin her bölümünü bir kusto işlevi olarak kaydedin ve Power BI bu iki işlevi birlikte katılmasına izin verin.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Göreli tarih-saat işlecinin benzetimini yapma

Power BI, `ago()`gibi *göreli* bir tarih-saat işleci içermez.
`ago()`benzetimini yapmak için `DateTime.FixedLocalNow()` ve `#duration` Power BI işlevlerinin birleşimini kullanın.

`ago()` işlecini kullanarak bu sorgu yerine:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Aşağıdaki eşdeğer sorguyu kullanın:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Kusto sorgu sınırlarına ulaşma 

Kusto sorguları, [sorgu sınırları](/azure/kusto/concepts/querylimits)bölümünde açıklandığı gibi varsayılan olarak en fazla 500.000 satıra veya 64 MB 'a döndürülür. **Azure Veri Gezgini (kusto)** bağlantı penceresinde **Gelişmiş seçenekleri** kullanarak bu Varsayılanları geçersiz kılabilirsiniz:

![Gelişmiş Seçenekler](media/power-bi-best-practices/advanced-options.png)

Bu seçenekler, varsayılan sorgu sınırlarını değiştirmek için Sorgunuzla birlikte [set deyimlerini](/azure/kusto/query/setstatement) sorun.

  * **Limit sorgu sonucu kayıt numarası** bir `set truncationmaxrecords` oluşturur
  * **Bayt cinsinden limit sorgu sonucu veri boyutu** bir `set truncationmaxsize` oluşturur
  * **Sonuç kümesi kesilmesini devre dışı bırakma** bir `set notruncation` oluşturur

### <a name="using-query-parameters"></a>Sorgu parametrelerini kullanma

Sorgunuzu dinamik olarak değiştirmek için [sorgu parametrelerini](/azure/kusto/query/queryparametersstatement) kullanabilirsiniz. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Bağlantı ayrıntılarında sorgu parametresi kullanma

Sorgudaki bilgileri filtrelemek ve sorgu performansını iyileştirmek için bir sorgu parametresi kullanın.
 
**Sorguları Düzenle** penceresinde, **giriş** > **Gelişmiş Düzenleyici**

1. Sorgunun aşağıdaki bölümünü bulun:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Örneğin:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Sorgunun ilgili bölümünü parametrenizle değiştirin. Sorguyu birden çok parçaya ayırın ve parametresi ile birlikte bir ve işareti (&) kullanarak tekrar birleştirin.

   Örneğin, Yukarıdaki sorguda `State == 'ALABAMA'` parçasını ele alacağız ve: `State == '` ve `'` `State` ' ye bölecektir:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Sorgunuz tırnak işaretleri içeriyorsa, bunları doğru kodlayın. Örneğin, aşağıdaki sorgu: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   **Gelişmiş Düzenleyici** iki tırnak işaretiyle aşağıdaki gibi görünür:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Üç tırnak işareti ile aşağıdaki sorguyla değiştirilmelidir:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Sorgu adımlarında bir sorgu parametresi kullanın

Sorgu parametresini, destekleyen herhangi bir sorgu adımında kullanabilirsiniz. Örneğin, sonuçları bir parametresinin değerine göre filtreleyin.

![bir parametre kullanarak Sonuçları filtrele](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Kusto 'e denetim komutları vermek için Power BI veri yenileme zamanlayıcısını kullanmayın

Power BI, düzenli aralıklarla bir veri kaynağına yönelik sorgular veren bir veri yenileme Zamanlayıcısı içerir. Power BI, tüm sorguların salt okunurdur olduğunu varsaydığından, denetim komutlarının kusto olarak zamanlanmasında bu mekanizma kullanılmamalıdır.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI, kusto 'e yalnızca kısa (&lt;2000 karakter) sorgular gönderebilir

Power BI ' de bir sorgu çalıştırırsanız, şu hata oluşur: _"DataSource. Error: Web. Contents öğesinden içerikleri alamadı..."_ sorgu muhtemelen 2000 karakterden daha uzun. Power BI, sorguyu alınmakta olan URI 'nin bir parçası olarak kodlayan bir HTTP GET isteği vererek kusto sorgulamak için **powerquery** 'yi kullanır. Bu nedenle, Power BI tarafından verilen kusto sorguları bir istek URI 'SI (2000 karakter, eksi küçük fark) uzunluk üst sınırı ile sınırlıdır. Geçici bir çözüm olarak, kusto içinde [saklı bir işlev](/azure/kusto/query/schema-entities/stored-functions) tanımlayabilir ve bu işlevi Sorguda Power BI kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Power BI için Azure Veri Gezgini bağlayıcısını kullanarak verileri görselleştirin](power-bi-connector.md)




