---
title: Azure Izleyici çalışma kitabı grafik görselleştirmeleri
description: Tüm Azure Izleyici çalışma kitabı grafik görselleştirmeleri hakkında bilgi edinin.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100624596"
---
# <a name="chart-visualizations"></a>Grafik görselleştirmeleri

Çalışma kitapları, izleme verilerinin grafik olarak sunulmasını sağlar. Desteklenen grafik türleri çizgi, çubuk, çubuk kategorik, alan, dağılım çizimleri, pasta ve saati içerir. Yazarlar, grafiğin yükseklik, genişlik, renk paleti, açıklama, başlık, veri içermeyen mesaj vb. ve grafik ayarlarını kullanarak eksen türlerini ve seri renklerini özelleştirmek için seçim yapabilir.

Çalışma kitapları hem Günlükler hem de ölçüm veri kaynakları için grafikleri destekler.

## <a name="log-charts"></a>Günlük grafikleri

Azure Izleyici günlükleri, kaynak sahiplerini uygulama ve altyapısının işleyişi hakkında ayrıntılı bilgi sağlar. Ölçümlerin aksine, günlük bilgileri varsayılan olarak toplanmaz ve bazı tür koleksiyon oluşturma gerektirir. Ancak, mevcut Günlükler, kaynak durumu ve Tanılama için yararlı veri hakkında çok fazla bilgi sağlar. Çalışma kitapları, Kullanıcı analizine yönelik görsel grafikler olarak günlük verileri sunmayı sağlar.

### <a name="adding-a-log-chart"></a>Günlük grafik ekleme

Aşağıdaki örnekte, bir uygulamaya yönelik isteklerin önceki günler boyunca eğilimi gösterilmektedir.

1. **Düzenleme** araç çubuğu öğesini seçerek çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analizinize yönelik [KQL](/azure/kusto/query/) 'yi girmek için sorgu düzenleyicisini kullanın (örneğin, isteklerin eğilimi).
5. Görselleştirmeyi şunlardan birine ayarlayın: **alan**, **çubuk**, **çubuk (kategorik)**, **çizgi**, **pasta**, **dağılım** veya **zaman**.
6. Gerekirse daha fazla parametre belirleyin; örneğin, zaman aralığı, görselleştirme, boyut, renk paleti ve gösterge.

[![Düzenleme modundaki günlük grafiğinin ekran görüntüsü](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Günlük grafiği parametreleri

| Parametre | Açıklama | Örnek |
| ------------- |:-------------|:-------------|
| `Query Type` | Kullanılacak sorgunun türü. | Günlük, Azure Kaynak Grafiği vb. |
| `Resource Type` | Hedeflenecek kaynak türü. | Application Insights, Log Analytics veya Azure-önce |
| `Resources` | Ölçüm değerinin alınacağı kaynak kümesi. | MyApp1 |
| `Time Range` | Günlük grafiğinin görüntüleneceği zaman penceresi. | Son saat, son 24 saat, vb. |
| `Visualization` | Kullanılacak görselleştirme. | Alan, çubuk, çizgi, pasta, dağılım, saat, çubuk kategorik |
| `Size` | Denetimin dikey boyutu. | Küçük, orta, büyük veya tam |
| `Color palette` | Grafikte kullanılacak renk paleti. Çok ölçümlü veya kesimli modda yoksayıldı. | Mavi, yeşil, kırmızı, vb. |
| `Legend` | Gösterge için kullanılacak toplama işlevi. | Değerlerin toplamı veya ortalaması ya da Max, min, First, Last değeri |
| `Query` | Grafik görselleştirmesi tarafından beklenen biçimde veri döndüren herhangi bir KQL sorgusu. | _\|-Series istek sayısı = count () varsayılan = 0, zaman damgasında önce (1D), şimdi (). Adım 1 sa_ |

### <a name="time-series-charts"></a>Zaman serisi grafikleri

Alan, çubuk, çizgi, dağılım ve saat gibi zaman serisi grafikleri, çalışma kitaplarında sorgu denetimi kullanılarak kolayca oluşturulabilir. Anahtar, sonuç kümesinde zaman ve ölçüm bilgilerine sahip olur.

#### <a name="simple-time-series"></a>Basit zaman serisi

Aşağıdaki sorgu iki sütunlu bir tablo döndürüyor: *zaman damgası* ve *istekler*. Sorgu denetimi X ekseni ve Y ekseni *istekleri* için *zaman damgası* kullanır.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Basit bir zaman serisi günlük satırı grafiğinin ekran görüntüsü.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Birden çok ölçümle zaman serisi

Aşağıdaki sorgu üç sütunlu bir tablo döndürür: *zaman damgası*, *istek* ve *Kullanıcı*. Sorgu denetimi X ekseni için *zaman damgasını* kullanır ve   &  *kullanıcıları* Y ekseninde ayrı seriler olarak ister.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Birden çok ölçüm günlük satırı grafiğine sahip bir zaman serisinin ekran görüntüsü.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Kesimli süre serisi

Aşağıdaki sorgu, üç sütun içeren bir tablo döndürür: *RequestName* , istek adlarının bulunduğu kategorik bir sütun olduğu *zaman damgası*, *istekler* ve *RequestName* . Buradaki sorgu denetimi X ekseni için *zaman damgasını* kullanır ve *RequestName* değeri başına bir seri ekler.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Bölümlenmiş bir zaman serisi günlük satırı grafiğinin ekran görüntüsü.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Özet-oluştur-serisini özetleme

Önceki bölümde yer aldığı örnekler, `summarize` daha kolay anlaşılması için işlecini kullanır. Ancak, demette hiçbir öğe yoksa, özetleme, sonuçlar satırını atladığından önemli bir sınırlamaya sahiptir. Boş demetlerin, zaman aralığının ön veya arkatarafında olup olmadığına bağlı olarak, grafik zaman penceresini kaydırma etkisi olabilir.

`make-series`Boş demetler için varsayılan değerler sağlama seçeneğine sahip olan zaman serisi verilerini oluşturmak için işlecini kullanmak genellikle daha iyidir.

Aşağıdaki sorgu `make-series` işlecini kullanır.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

Aşağıdaki sorguda, işleciyle benzer bir grafik gösterilmektedir `summarize`

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Temel alınan sonuç kümesi farklı olsa da. Bir kullanıcının yapması gereken, görselleştirmeyi alan, çizgi, çubuk veya saat ve çalışma kitapları olarak ayarlamak, Rest 'in ilgilenmesini sağlar.

[![Bir oluşturma serisi sorgusundan oluşturulan günlük çizgisi grafiğinin ekran görüntüsü](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Kategorik çubuk grafik veya histogram

Kategorik grafikler, kullanıcıların bir grafiğin X ekseninde bir boyut veya sütunu göstermesini sağlar, bu özellikle histogramlar için kullanışlıdır. Aşağıdaki örnekte, isteklerin sonuç koduna göre dağılımı gösterilmektedir.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Sorgu iki sütun döndürür: *istek* ölçümü ve *sonuç* kategorisi. *Sonuç* sütununun her değeri,, *istek ölçüsüne* göre yükseklik orantılı şekilde grafikte kendi çubuğunu alır.

[![Sonuç koduna göre istekler için kategorik bir çubuk grafik görüntüsü](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Pasta grafikleri

Pasta grafikler, sayısal oranın görselleştirmesine izin verir. Aşağıdaki örnekte, istek koduna göre isteklerin oranı gösterilmektedir.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Sorgu iki sütun döndürür: *istek* ölçümü ve *sonuç* kategorisi. *Sonuç* sütununun her bir değeri, bir pastadaki kendi dilimini, *istek* ölçüsüne orantılı olacak şekilde alır.

[![Sonuç kodunu temsil eden dilimlerle bir pasta grafiğinin ekran görüntüsü](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Ölçüm grafikleri

Çoğu Azure kaynağı, durum ve sistem durumu hakkında ölçüm verileri (örneğin CPU kullanımı, depolama kullanılabilirliği, veritabanı işlemlerinin sayısı, başarısız uygulama istekleri vb.) yayar. Çalışma kitapları bu verilerin görselleştirmesine zaman serisi grafikleri olarak izin verir.)

### <a name="adding-a-metric-chart"></a>Ölçüm grafiği ekleme

Aşağıdaki örnek, bir depolama hesabındaki işlem sayısını önceki bir saat içinde gösterir. Bu, depolama sahibinin işlem eğilimi görmesini ve davranışlara yönelik davranışı belirlemesine izin verir.

1. **Düzenleme** araç çubuğu öğesini seçerek çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına ölçüm denetimi eklemek için **ölçüm Ekle** bağlantısını kullanın.
3. Kaynak türünü (örneğin, depolama hesabı), hedeflenecek kaynakları, ölçüm ad alanını ve adı ve kullanılacak toplamayı seçin.
4. Gerekirse, daha fazla zaman aralığı, bölme, görselleştirme, boyut ve renk paleti gibi diğer parametreleri ayarlayın.

[![Düzenleme modundaki ölçüm grafiğinin ekran görüntüsü](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Ölçüm grafiği parametreleri

| Parametre | Açıklama | Örnek |
| ------------- |:-------------|:-------------|
| `Resource Type` | Hedeflenecek kaynak türü. | Depolama alanı veya sanal makine. |
| `Resources` | Ölçüm değerinin alınacağı kaynak kümesi. | MyStorage1 |
| `Namespace` | Ölçüm ile ad alanı. | Depolama > blobu |
| `Metric` | Görselleştirilecek ölçüm. | Depolama > blob > Işlemleri |
| `Aggregation` | Ölçüm için uygulanacak toplama işlevi. | Toplam, sayı, ortalama, vb. |
| `Time Range` | Ölçümün içinde görüntüleneceği zaman penceresi. | Son saat, son 24 saat, vb. |
| `Visualization` | Kullanılacak görselleştirme. | Alan, çubuk, çizgi, dağılım, kılavuz |
| `Split By` | İsteğe bağlı olarak ölçüm boyutunu bir boyuta ayırın. | Coğrafi türe göre işlemler |
| `Size` | Denetimin dikey boyutu. | Küçük, orta veya büyük |
| `Color palette` | Grafikte kullanılacak renk paleti. `Split by`Parametre kullanılıyorsa yoksayıldı. | Mavi, yeşil, kırmızı, vb. |

### <a name="examples"></a>Örnekler

Bir çizgi grafik olarak API adına göre bölünen işlemler:

[![API adına göre bölünmüş depolama işlemleri için ölçüm çizgisi grafiğinin ekran görüntüsü](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Yanıt türüne göre büyük bir çubuk grafik olarak bölünen işlemler:

[![Yanıt türüne göre bölünmüş depolama işlemleri için büyük bir ölçüm çubuğu grafiğinin ekran görüntüsü](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Dağılım grafiği olarak ortalama gecikme süresi:

[![Depolama gecikmesi için ölçüm dağılım grafiğinin ekran görüntüsü](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Grafik ayarları

Yazarlar, Grafik eksenleri, eksen birimleri, özel biçimlendirme, aralıklar, gruplandırma davranışları, göstergeler ve seri renkler içinde hangi alanların kullanıldığını özelleştirmek için grafik ayarlarını kullanabilir.

### <a name="the-settings-tab"></a>Ayarlar sekmesi

Ayarlar sekmesi denetimleri:

- Hangi alanları içeren eksen ayarları, kullanıcıların sayı biçimlendirmesini eksen değerlerine ve özel aralıklara ayarlamasına izin veren özel biçimlendirme.
- Grup ayarları, hangi alanı, "diğerleri" grubu oluşturulmadan önceki limitleri de içerecek şekilde gruplandırma.
- Alt kısımdaki ölçümleri (seri adı, renkler ve rakamlar) ve/veya gösterge (seri adları ve renkler) gibi gösterge ayarları.

![Grafik ayarlarının ekran görüntüsü.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Özel biçimlendirme

Sayı biçimlendirme seçenekleri şunlardır:

| Biçimlendirme seçeneği             | Açıklama                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | Sütun için birimler-yüzde, sayı, saat, bayt, sayı/saat, bayt/saat vb. için çeşitli seçenekler. Örneğin, 1234 değerinin birimi milisaniyeye ayarlanabilir ve yuvarlayın s olarak işlenir.                                  |
| `Style`                      | Ondalık, para birimi, yüzde olarak işlenecek biçim.                                               |
| `Show group separator`       | Grup ayırıcılarını gösterme onay kutusu. ABD 'de 1.234 olarak 1234 işler.                                    |
| `Minimum integer digits`     | Kullanılacak en az tamsayı basamak sayısı (varsayılan 1).                                                   |
| `Minimum fractional digits`  | Kullanılacak en az kesirli basamak sayısı (varsayılan 0).                                                |
| `Maximum fractional digits`  | Kullanılacak en fazla kesirli basamak sayısı.                                                            |
| `Minimum significant digits` | En az kullanılan önemli basamak sayısı (varsayılan 1).                                               |
| `Maximum significant digits` | En fazla kullanılacak önemli basamak sayısı.                                                           |

![X ekseni ayarlarının ekran görüntüsü.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Seriler sekmesi

Seriler ayarı sekmesi grafikteki seriler için gösterilen etiketleri ve renkleri ayarlamanıza olanak sağlar.

- `Series name`Alan, verilerdeki bir seriyi eşleştirmek için kullanılır ve eşleşirse, görüntüleme etiketi ve renk görüntülenir.
- `Comment`Bu yorum, görüntü etiketlerini yerelleştirmek için çevirmenler tarafından kullanılabilecek şekilde şablon yazarları için faydalıdır.

![Seri ayarlarının ekran görüntüsü.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Çalışma kitaplarında kutucuk](workbooks-tile-visualizations.md)oluşturmayı öğrenin.
- [Etkileşimli çalışma kitapları](workbooks-interactive.md)oluşturmayı öğrenin.