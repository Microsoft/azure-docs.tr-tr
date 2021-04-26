---
title: Azure Izleyici çalışma kitabı harita görselleştirmeleri
description: Azure Izleyici çalışma kitabı harita görselleştirmeleri hakkında bilgi edinin.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: lagayhar
ms.openlocfilehash: dd9bafab982923785898b7f135e5c0408aa19a14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100622849"
---
# <a name="map-visualization"></a>Harita görselleştirmesi

Harita görselleştirmesi belirli bölgelerde PIN işaret sorunlarını ve her bir konum/ülke/bölge ile eşlenen tüm verileri toplama yeteneği sağlayarak izleme verilerinin üst düzey toplanmış görünümlerini gösterir.

Aşağıdaki ekran görüntüsünde, farklı depolama hesapları için toplam işlemler ve uçtan uca gecikme süresi gösterilmektedir. Burada boyut toplam işlem sayısı ve haritanın altındaki renk ölçümleri uçtan uca gecikme süresini gösterir. İlk gözlemden sonra, **Batı ABD** bölgesindeki işlem sayısı **Doğu ABD** bölgeyle karşılaştırılır, ancak **Batı ABD** bölgenin uçtan uca gecikmesi **Doğu ABD** bölgesinden daha yüksektir. Bu, **Batı ABD** için bir şeyin isabetsizlik olduğunu gösteren ilk öngörüleri sağlar.

![Azure konum eşlemesinin ekran görüntüsü](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Harita ekleme

Temel alınan verilerde/ölçümlerde Latitude/boylam bilgileri, Azure Kaynak bilgileri, Azure konum bilgileri veya ülke/bölge, ad veya ülke/bölge kodu varsa eşleme görselleştirilir.

### <a name="using-azure-location"></a>Azure konumu kullanma

1. **Düzenleme** araç çubuğu öğesini seçerek çalışma kitabını düzenleme moduna geçirin.
2. **Ekle** ' yi ve ardından *Sorgu Ekle*' yi seçin
3. *Veri kaynağını* değiştirip `Azure Resource Graph` depolama hesabı olan herhangi bir aboneliği seçin.
4. Analizinizi ve Select **Run Query** için aşağıdaki sorguyu girin.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. *Boyut* ayarla `Large` .
6. *Görselleştirmeyi* olarak ayarlayın `Map` .
7. Tüm ayarlar tekrar doldurulur. Özel ayarlar için, **eşleme ayarları** düğmesini seçerek ayarlar bölmesini açın.
8. Aşağıda, seçili abonelik için her bir Azure bölgesinin depolama hesaplarını gösteren harita görselleştirmesinin ekran görüntüsü verilmiştir.

![Yukarıdaki sorguyla Azure location Map 'in ekran görüntüsü](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Azure kaynağı kullanma

1. **Düzenleme** araç çubuğu öğesini seçerek çalışma kitabını düzenleme moduna geçirin.
2. **Ekle** ' yi seçin ve *ölçüm ekleyin*.
3. Depolama hesapları olan bir abonelik kullanın.
4. Kaynak *türünü* olarak değiştirin `storage account` ve *kaynak* bölümünde birden çok depolama hesabı seçin.
5. **Ölçüm Ekle** ve işlem ölçümü Ekle ' yi seçin.
    1. Ad Alanı: `Account`
    2. Ölçüt `Transactions`
    3. Toplama `Sum`
    
    ![İşlem ölçüsünün ekran görüntüsü](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. **Ölçüm Ekle** ve başarı e2e gecikme ölçümü Ekle ' yi seçin.
    1. Ad Alanı: `Account`
    1. Ölçüt `Success E2E Latency`
    1. Toplama `Average`
    
    ![Başarılı uçtan uca gecikme ölçüsünün ekran görüntüsü](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. *Boyut* ayarla `Large` .
1. *Görselleştirme* boyutunu olarak ayarlayın `Map` .
1. **Eşleme ayarları** ' nda aşağıdaki ayarları ayarlayın:
    1. Şunu kullanarak konum bilgisi: `Azure Resource`
    1. Azure Kaynak alanı: `Name`
    1. Boyut ölçütü: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Konum için toplama: `Sum of values`
    1. Renklendirme türü: `Heatmap`
    1. Renk ölçütü: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Renk için toplama: `Sum of values`
    1. Renk paleti: `Green to Red`
    1. En küçük değer: `0`
    1. Ölçüm değeri: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Diğer ölçümleri toplama ölçütü: `Sum of values`
    1. **Özel biçimlendirme** kutusunu seçin
    1. Birim `Milliseconds`
    1. Biçim `Decimal`
    1. En fazla kesirli basamaklar: `2`

### <a name="using-countryregion"></a>Ülke/bölge kullanma

1. **Düzenleme** araç çubuğu öğesini seçerek çalışma kitabını düzenleme moduna geçirin.
2. *Ekle*' yi ve ardından *Sorgu Ekle*' yi seçin.
3. *Veri kaynağını* olarak değiştirin `Log` .
4. *Kaynak türünü* olarak seçin `Application Insights` ve ardından PageViews verilerine sahip tüm Application Insights kaynakları seçin.
5. Analiz etmek için KQL 'i girmek ve **Sorguyu Çalıştır**' ı seçmek için sorgu düzenleyicisini kullanın.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Boyut değerlerini olarak ayarlayın `Large` .
7. Görselleştirmeyi olarak ayarlayın `Map` .
8. Tüm ayarlar tekrar doldurulur. Özel ayarlar için **eşleme ayarları**' nı seçin.

### <a name="using-latitudelocation"></a>Enlem/konumu kullanma

1. **Düzenleme** araç çubuğu öğesini seçerek çalışma kitabını düzenleme moduna geçirin.
2. *Ekle*' yi ve ardından *Sorgu Ekle*' yi seçin.
3. *Veri kaynağını* olarak değiştirin `JSON` .
1. Sorgu Düzenleyicisi 'nde aşağıdaki JSON verilerini girin ve **Sorguyu Çalıştır**' ı seçin.
1. *Boyut* değerlerini olarak ayarlayın `Large` .
1. *Görselleştirmeyi* olarak ayarlayın `Map` .
1. "Ölçüm ayarları" altındaki **eşleme ayarları** ' nda, *ölçüm etiketini* ayarlayın `displayName` ve ardından **Kaydet ve Kapat**' ı seçin.

Aşağıdaki harita görselleştirmesinde, ölçümler için seçili etikete sahip her bir enlem ve boylam konumunun kullanıcıları gösterilmektedir.

![Ölçümler için seçili etikete sahip her bir enlem ve boylam konumu için kullanıcıları gösteren bir harita görselleştirmesinin ekran görüntüsü](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Harita ayarları

### <a name="layout-settings"></a>Düzen ayarları

| Ayar | Açıklama |
|:-------------|:-------------|
| `Location info using` | Haritada gösterilen öğelerin konumunu almak için bir yol seçin. <br> **Enlem/Boylam**: Enlem ve Boylam bilgilerine sahip sütunlar varsa bu seçeneği belirleyin. Enlem ve boylam verileri olan her satır, haritada ayrı bir öğe olarak gösterilir. <br> **Azure konumu**: Azure Location (eastus, westeurope, merkezileştirhindistan, vb.) bilgileri içeren bir sütun varsa bu seçeneği belirleyin. Bu sütunu belirtin ve her bir Azure konumu için karşılık gelen Enlem ve Boylam getirip, eşlemedeki konumları göstermek için aynı konum satırlarını (toplamaya göre) birlikte gruplandırın. <br> **Azure kaynağı**: Azure Kaynak bilgilerine (depolama hesabı, cosmosdb hesabı vb.) sahip bir sütun varsa bu seçeneği belirleyin. Bu sütunu belirtin ve her bir Azure kaynağı için karşılık gelen Enlem ve boylam, eşlemedeki konumları göstermek için aynı konum (Azure Location) satırlarını (belirtilen toplamaya göre) bir araya getirin. <br> **Ülke/** bölge: ülke/bölge adı/kodu (US, BIRLEŞIK Devletler, ın, HINDISTAN, CN, Çin) bilgilerine sahip bir sütun varsa bu seçeneği belirleyin. Bu sütunu, her ülke/bölge/kod için karşılık gelen Enlem ve Boylam getirip, eşlemedeki konumları göstermek için satırları aynı Country-Region kodu/Ülke Bölgesi adıyla birlikte gruplamak için belirtin. Ülke adı ve ülke kodu, haritada tek bir varlık olarak birlikte gruplandırılmaz.
| `Latitude/Longitude` | Bu iki seçenek konum bilgisi alan değeri: Enlem/Boylam ise görünür olur. Enlem alanında Enlem olan sütunu, sırasıyla Boylam alanındaki Boylam seçin. |
| `Azure location field` | Konum bilgisi alan değeri: Azure konumu ise bu seçenek görünür olur. Azure konum bilgilerinin sütununu seçin. |
| `Azure resource field` | Konum bilgisi alan değeri: Azure kaynağı ise bu seçenek görünür olur. Azure kaynak bilgilerinin sütununu seçin. |
| `Country/Region field` | Konum bilgisi alan değeri: ülke veya bölge ise bu seçenek görünür olur. Ülke/bölge bilgilerinin bulunduğu sütunu seçin. |
| `Size by` | Bu seçenek Haritada gösterilen öğelerin boyutunu denetler. Boyut, Kullanıcı tarafından belirtilen sütundaki değere bağlıdır. Şu anda dairenin yarıçapı, sütun değerinin karekökünü doğrudan doğru orantılıdır. ' None... ' ise seçilirse, tüm daireler varsayılan bölge boyutunu gösterir.|
| `Aggregation for location` | Bu alan, boyutun aynı Azure konumuna/Azure kaynağına/ülke bölgesine sahip olan sütunlara **göre** nasıl toplanacağını belirtir. |
| `Minimum region size` | Bu alan, Haritada gösterilen öğenin en düşük yarıçapını belirler. Bu, sütun değerlerine göre boyut arasında önemli bir fark olduğunda kullanılır, bu nedenle daha küçük öğeler haritada görünür. |
| `Maximum region size` | Bu alan, Haritada gösterilen öğenin en büyük yarıçapının ne olduğunu belirtir. Bu, sütuna göre boyut değerleri son derece büyükse ve haritanın çok büyük alanını kapsadıklarında kullanılır.|
| `Default region size` | Bu alan, Haritada gösterilen öğenin varsayılan yarıçapını belirler. Varsayılan yarıçap, sütuna göre boyut ' none... ' olduğunda kullanılır veya değer 0 ' dır.|
| `Minimum value` | Bölge boyutunu hesaplamak için kullanılan minimum değer. Belirtilmemişse, en küçük değer toplandıktan sonra en küçük değer olacaktır. |
| `Maximum value` | Bölge boyutunu hesaplamak için kullanılan en büyük değer. Belirtilmemişse, en büyük değer toplandıktan sonra en büyük değer olacaktır.|
| `Opacity of items on Map` | Bu alan, Haritada gösterilen öğelerin saydam olduğunu belirler. Opaklık 1, saydamlık yok, opaklık 0 anlamına gelir, öğelerin haritada görünmeyeceği anlamına gelir. Haritada çok fazla öğe varsa, tüm çakışan öğelerin görünür olması için opaklık düşük değere ayarlanabilir.|

### <a name="color-settings"></a>Renk ayarları

| Renklendirme türü | Açıklama |
|:------------- |:-------------|
| `None` | Tüm düğümler aynı renge sahip. |
| `Thresholds` | Bu türde, hücre renkleri eşik kurallarına göre ayarlanır (örneğin, _CPU > 90% => Red, 60% > cpu > 90% => sarı, cpu < 60% => yeşil_). <ul><li> **Color by**: Bu sütunun değeri, eşikler/heatmap mantığı tarafından kullanılır.</li> <li>**Renk Için toplama**: Bu alan, aynı Azure konumuna/Azure kaynağına/ülke bölgesine sahip olan sütunların **renk** toplama yöntemini belirtir. </li> <ul> |
| `Heatmap` | Bu türde, hücreler renk paleti ve renk alanına göre renklendirilir. Bu, **renk** seçeneklerinin eşikleriyle aynı **renge** sahip olacak ve toplamasının aynısını de içermelidir. |

### <a name="metric-settings"></a>Ölçüm ayarları
| Ayar | Açıklama |
|:------------- |:-------------|
| `Metric Label` | Konum bilgisi alan değeri: Enlem/Boylam ise bu seçenek görünür olur. Bu özelliği kullanarak, Kullanıcı haritanın altında gösterilen ölçümler için gösterilecek etiketi seçebilir. |
| `Metric Value` | Bu alan, haritanın altında gösterilecek ölçüm değerini belirtir. |
| `Create 'Others' group after` | Bu alan, "diğerleri" grubu oluşturulmadan önce sınırı belirtir. |
| `Aggregate 'Others' metrics by` | Bu alan, gösterilirse "diğerleri" grubu için kullanılan toplamayı belirtir. |
| `Custom formatting` | Sayı değerlerine yönelik birimleri, stili ve biçimlendirme seçeneklerini ayarlamak için bu alanı kullanın. Bu, [kılavuzun özel biçimiyle](../visualize/workbooks-grid-visualizations.md#custom-formatting)aynıdır.|

## <a name="next-steps"></a>Sonraki adımlar

- [Çalışma kitaplarında Honey çağrısıyla görselleştirmeleri](../visualize/workbooks-honey-comb.md)oluşturmayı öğrenin.