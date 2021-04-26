---
title: Azure Izleyici çalışma kitabı kılavuz görselleştirmeleri
description: Tüm Azure Izleyici çalışma kitabı kılavuz görselleştirmeleri hakkında bilgi edinin.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100622622"
---
# <a name="grid-visualizations"></a>Kılavuz görselleştirmeleri

Kılavuzlar veya tablolar, verileri kullanıcılara sunmak için yaygın bir yoldur. Çalışma kitapları kullanıcıların, raporları için zengin bir kullanıcı arabirimi sağlamak üzere kılavuzun sütunlarını tek tek tarzlarına olanak tanır.

Aşağıdaki örnekte, simgeler, helemaps ve Spark-çubuklarını birleştiren ve karmaşık bilgileri sunan bir kılavuz gösterilmektedir. Çalışma kitabı ayrıca sıralama, arama kutusu ve Analize Git düğmesi de sağlar.

[![Günlük tabanlı kılavuzun ekran görüntüsü](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Günlük tabanlı kılavuz ekleme

1. **Düzenleme** araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analizinize yönelik KQL 'yi (örneğin, bir eşiğin altında belleğe sahip VM 'Ler) girmek için sorgu düzenleyicisini kullanın
5. Görselleştirmeyi **kılavuza** ayarla
6. Gerekirse daha fazla parametre belirleyin, örneğin, zaman aralığı, boyut, renk paleti ve gösterge.

[![Günlük tabanlı kılavuz sorgusunun ekran görüntüsü](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Günlük grafiği parametreleri

| Parametre | Açıklama | Örnek |
| ------------- |:-------------|:-------------|
| `Query Type` | Kullanılacak sorgunun türü. | Günlük, Azure Kaynak Grafiği vb. |
| `Resource Type` | Hedeflenecek kaynak türü. | Application Insights, Log Analytics veya Azure-önce |
| `Resources` | Ölçüm değerinin alınacağı kaynak kümesi. | MyApp1 |
| `Time Range` | Günlük grafiğinin görüntüleneceği zaman penceresi. | Son saat, son 24 saat, vb. |
| `Visualization` | Kullanılacak görselleştirme. | Kılavuz |
| `Size` | Denetimin dikey boyutu. | Küçük, orta, büyük veya tam |
| `Query` | Grafik görselleştirmesi tarafından beklenen biçimde veri döndüren herhangi bir KQL sorgusu. | _istekleri \| özetleme istekleri = Count () adına göre_ |

## <a name="simple-grid"></a>Basit kılavuz

Çalışma kitapları, KQL sonuçlarını basit bir tablo olarak işleyebilir. Aşağıdaki kılavuzda, bir uygulamadaki istek sayısı ve istek başına benzersiz kullanıcı sayısı gösterilmektedir.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Düzenleme modundaki bir günlük tabanlı kılavuzun ekran görüntüsü](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Kılavuz stili

Düz bir tablo verileri gösterirken, okunması zor olur ve Öngörüler her zaman görünür olmayacaktır. Kılavuzun stillendirilmesini, verilerin okunmasını ve yorumlanmasına yardımcı olabilir.

Aşağıda, heatmaps olarak stillendirilmiş bir önceki bölümden aynı kılavuz vardır.

[![Isı haritalarını olarak stillendirilmiş sütunlara sahip günlük tabanlı kılavuzun ekran görüntüsü](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Bu kılavuz, çubuklar olarak stillendirilmiş [ ![ bir günlük tabanlı kılavuzun ekran görüntüsü](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox) olarak gösterilmiştir.

### <a name="styling-a-grid-column"></a>Kılavuz sütununu Stillendirme

1. Sorgu denetimi araç çubuğunda **sütun ayarı** düğmesini seçin.
2. *Sütun düzenleme ayarları*' nda, stil olarak kullanılacak sütunu seçin.
3. Sütununuzu stil eklemek için bir sütun Oluşturucu (örneğin, heatmap, çubuk, çubuk altında, vb.) ve ilgili ayarları seçin.

*İstek* sütununu çubuk olarak stileden bir örnek aşağıda verilmiştir:

[![Bir çubuk olarak stillendirilmiş istek sütunuyla bir günlük tabanlı kılavuzun ekran görüntüsü.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Sütun Oluşturucu

| Sütun Oluşturucu | Açıklama | Ek Seçenekler |
|:------------- |:-------------|:-------------|
| `Automatic` | Varsayılan değer, sütun türüne göre en uygun oluşturucuyu kullanır.  |  |
| `Text` | Sütun değerlerini metin olarak işler. | |
| `Right Aligned` | Metne benzer, sağa hizalanmış olması dışında. | |
| `Date/Time` | Okunabilir bir tarih saat dizesi işler. | |
| `Heatmap` | Hücrenin değerine göre kılavuz hücrelerini renklerinde yapın. | Ölçeklendirme için kullanılan Color paleti ve min/max değeri. |
| `Bar` | Hücrenin değerine göre hücrenin yanına bir çubuk oluşturur. | Ölçeklendirme için kullanılan Color paleti ve min/max değeri. |
| `Bar underneath` | Hücrenin değerine göre hücrenin alt kısmına yakın bir çubuk çizer. | Ölçeklendirme için kullanılan Color paleti ve min/max değeri. |
| `Composite bar` | Bu satırdaki belirtilen sütunları kullanarak bileşik bir çubuk oluşturur. Ayrıntılar için [Birleşik çubuğa](workbooks-composite-bar.md) başvurun. | Çubuğu ve çubuğun üstünde görüntülenecek bir etiketi işlemek için karşılık gelen renklerin bulunduğu sütunlar. |
| `Spark bars` | Hücredeki bir dinamik dizinin değerlerine göre hücrede Spark çubuğu oluşturur. Örneğin, üstteki ekran görüntüsünden eğilim sütunu. | Ölçeklendirme için kullanılan Color paleti ve min/max değeri. |
| `Spark lines` | Hücredeki bir dinamik dizinin değerlerine göre hücrede Spark satırını işler. | Ölçeklendirme için kullanılan Color paleti ve min/max değeri. |
| `Icon` | Hücredeki metin değerlerine göre simgeleri işler. Desteklenen değerler şunlardır: `cancelled` , `critical` , `disabled` , `error` , `failed` , `info` , `none` , `pending` , `stopped` , `question` , `success` ,, `unknown` `warning` `uninitialized` , `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,,,,,,, ve.|  |
| `Link` | Tıklatıldığında veya yapılandırılabilir bir eylem yaptığında bir bağlantı oluşturur. *Yalnızca* öğenin bir bağlantı olmasını istiyorsanız bunu kullanın.  Diğer türlerden herhangi biri, ayarı kullanılarak *da* bir bağlantı olabilir `Make this item a link` . Daha fazla bilgi için aşağıdaki [bağlantı eylemlerini](#link-actions) inceleyin. |  |
| `Location` | Bir bölge kimliği temelinde kolay bir Azure bölge adı oluşturur. |  |
| `Resource type` | Kaynak türü kimliği temel alınarak kolay bir kaynak türü dizesi işler  |  |
| `Resource` | Bir kaynak kimliğine göre kolay bir kaynak adı ve bağlantı oluşturur  | Kaynak türü simgesini gösterme seçeneği  |
| `Resource group` | Bir kaynak grubu kimliğini temel alan kolay bir kaynak grubu adı ve bağlantısı oluşturur. Hücrenin değeri bir kaynak grubu değilse, buna dönüştürülür.  | Kaynak grubu simgesini gösterme seçeneği  |
| `Subscription` | Abonelik kimliğini temel alan kolay bir abonelik adı ve bağlantı oluşturur.  hücrenin değeri bir abonelik değilse, buna dönüştürülür.  | Abonelik simgesini gösterme seçeneği.  |
| `Hidden` | Kılavuzdaki sütunu gizler. Varsayılan sorgu gerekenden daha fazla sütun döndürdüğünde, ancak bir proje uzakta istenmiyorsa yararlı olur |  |

### <a name="link-actions"></a>Bağlantı eylemleri

`Link`Oluşturucu seçiliyse veya *Bu öğeyi bir bağlantı yap* onay kutusu işaretliyse, yazar hücreyi seçerken gerçekleşecek bir bağlantı eylemi yapılandırabilir. Bu genellikle kullanıcıyı hücreden gelen bağlamla başka bir görünüme veya bir URL 'yi açabilir.

### <a name="custom-formatting"></a>Özel biçimlendirme

Çalışma kitapları, kullanıcıların hücre değerlerinin sayı biçimlendirmesini ayarlamasına de olanak tanır. Bu, kullanılabilir olduğunda *özel biçimlendirme* onay kutusuna tıklayarak bunu yapabilir.

| Biçimlendirme seçeneği | Açıklama |
|:------------- |:-------------|
| `Units` | Sütun için birimler-yüzde, sayı, saat, bayt, sayı/saat, bayt/saat vb. için çeşitli seçenekler. Örneğin, 1234 değerinin birimi milisaniyeye ayarlanabilir ve 1,234 s olarak işlenir. |
| `Style` | Ondalık, para birimi, yüzde olarak işlenecek biçim. |
| `Show group separator` | Grup ayırıcılarını gösterme onay kutusu. ABD 'de 1.234 olarak 1234 işler. |
| `Minimum integer digits` | Kullanılacak en az tamsayı basamak sayısı (varsayılan 1). |
| `Minimum fractional digits` | Kullanılacak en az kesirli basamak sayısı (varsayılan 0).  |
| `Maximum fractional digits` | Kullanılacak en fazla kesirli basamak sayısı. |
| `Minimum significant digits` | En az kullanılan önemli basamak sayısı (varsayılan 1). |
| `Maximum significant digits` | En fazla kullanılacak önemli basamak sayısı. |
| `Custom text for missing values` | Bir veri noktasının bir değeri olmadığında, bu özel metni boş yerine gösterin. |

### <a name="custom-date-formatting"></a>Özel Tarih biçimlendirmesi

Yazar bir sütunun tarih/saat işleyicisine ayarlandığını belirtmişse, yazar *özel tarih* biçimlendirme onay kutusunu kullanarak özel tarih biçimlendirme seçeneklerini belirtebilir.

| Biçimlendirme seçeneği | Açıklama |
|:------------- |:-------------|
| `Style` | Bir tarihi kısa, uzun, tam biçimler veya kısa veya uzun saat biçimleri olarak işleyen biçim. |
| `Show time as` | Yazarın saati yerel saat (varsayılan) veya UTC olarak gösterme arasında karar vermesini sağlar. Tarih biçim stiline bağlı olarak, UTC/saat dilimi bilgileri görüntülenmeyebilir. |

## <a name="custom-column-width-setting"></a>Özel sütun genişliği ayarı

Yazar, *sütun ayarlarındaki* *özel sütun genişliği* alanını kullanarak kılavuzdaki herhangi bir sütunun genişliğini özelleştirebilir.

![Kırmızı kutuda belirtilen özel sütun genişliği alanıyla sütun ayarlarının ekran görüntüsü](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Alan siyah bırakılırsa genişlik, sütundaki karakter sayısına ve görünür sütun sayısına göre otomatik olarak belirlenir. Varsayılan Birim "CH" (karakter).

Etikette mavi **(geçerli genişlik)** düğmesi seçildiğinde metin alanı seçili sütunun geçerli genişliğiyle doldurulur. Özel genişlik alanında ölçü birimi olmayan bir değer varsa, varsayılan olarak kullanılacaktır.

Kullanılabilir ölçüm birimleri şunlardır:

| Ölçü birimi | Tanım           |
|:--------------------|:---------------------|
| ch                  | karakterler (varsayılan) |
| px                  | pikseller               |
| kesir                  | kesir birimleri     |
| %                   | percentage           |

Giriş doğrulaması-doğrulama başarısız olursa, alanın altında kırmızı bir kılavuz iletisi açılır. ancak kullanıcı yine de genişliği uygulayabilir. Girişte bir değer varsa, ayrıştırılacaktır. Geçerli bir ölçü birimi bulunmazsa, varsayılan olarak kullanılacaktır.

Bu, yazarın kararına ayrılana kadar en düşük/en yüksek genişlik yoktur. Özel sütun genişliği alanı gizli sütunlar için devre dışıdır.

## <a name="examples"></a>Örnekler

### <a name="spark-lines-and-bar-underneath"></a>Spark çizgileri ve altındaki çubuk

Aşağıdaki örnekte, istek adına göre istek sayısı ve eğilimi gösterilmektedir.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Alttaki bir çubuk ve Spark çizgisi içeren bir günlük tabanlı kılavuz ekran görüntüsü](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Paylaşılan ölçeklendirmelere ve özel biçimlendirmeye sahip heatmap

Bu örnek, çeşitli istek süresi ölçümlerini ve sayısını gösterir. Isı haritasını işleyicisi, Ayarlar ' da ayarlanan en düşük değerleri kullanır veya sütun için en düşük ve en büyük değeri hesaplar ve sütunun en küçük ve en büyük değerine göre hücrenin değerine göre seçilen paletten bir arka plan rengi atar.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Sütunlar arasında paylaşılan ölçeğe sahip bir ısı haritasını ile günlük tabanlı kılavuzun ekran görüntüsü](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

Yukarıdaki örnekte, bir paylaşılan palet (yeşil veya kırmızı) ve ölçek sütunları renklendirmek için kullanılır (ortalama, ortanca, P80, P95 ve P99). İstek sütunu için kullanılan ayrı bir palet (mavi).

#### <a name="shared-scale"></a>Paylaşılan ölçek

Paylaşılan ölçek almak için:

1. Bir ayarı uygulamak için birden fazla sütun seçmek için normal ifadeler kullanın. Örneğin, sütun adını `Mean|Median|p80|p95|p99` Tümünü seçmek için olarak ayarlayın.
2. Ayrı sütunlar için varsayılan ayarları silin.

Bu, yeni çok sütunlu ayarın, paylaşılan bir ölçek içerecek şekilde ayarlarını uygulamasına neden olur.

[![Sütunlar arasında paylaşılan ölçek almak için günlük tabanlı kılavuz ayarının ekran görüntüsü](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Durumu temsil eden simgeler

Aşağıdaki örnekte, P95 süresine göre isteklerin özel durumu gösterilmektedir.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Yukarıdaki sorguyu kullanarak sütunlar arasında paylaşılan ölçeğe sahip bir ısı haritasını ile günlük tabanlı bir kılavuzun ekran görüntüsü.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Desteklenen simge adları şunlardır: `cancelled` , `critical` , `disabled` , `error` , `failed` , `info` , `none` , `pending` , `stopped` , `question` , `success` ,, `unknown` `warning` `uninitialized` , `resource` , `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,, ve.

### <a name="using-thresholds-with-links"></a>Bağlantılarla eşikleri kullanma

Aşağıdaki yönergeler, simgeleri atamak ve farklı çalışma kitaplarını açmak için bağlantılarla birlikte eşikleri nasıl kullanacağınızı gösterir. Kılavuzdaki her bağlantı, bu Application Insights kaynağı için farklı bir çalışma kitabı şablonu açar.

1. Araç çubuğu öğesini *Düzenle* seçeneğini belirleyerek çalışma kitabını düzenleme moduna geçirin.
2. **Ekle** ' yi ve ardından *Sorgu Ekle*' yi seçin
3. *Veri kaynağını* "JSON" ve *görselleştirme* olarak "Grid" olarak değiştirin.
4. Aşağıdaki sorguyu girin.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Sorguyu çalıştırın.
6. Ayarları açmak için **sütun ayarları** ' nı seçin.
7. *Sütunlardan*"ad" seçeneğini belirleyin.
8. *Sütun Oluşturucu* altında "eşikler" i seçin.
9.  Aşağıdaki *eşik ayarlarını* girin ve seçin.
   
    | İşleç | Değer   | Simgeler   |
    |----------|---------|---------|
    | ==       | warning | Uyarı |
    | ==       | error   | Başarısız  |

    ![Yukarıdaki ayarlarla sütun ayarlarını Düzenle sekmesinin ekran görüntüsü.](./media/workbooks-grid-visualizations/column-settings.png)

    Varsayılan satırı olduğu gibi tutun. Dilediğiniz metni girebilirsiniz. Metin sütunu, girdi olarak bir dize biçimi alır ve belirtilmişse sütun değeri ve birimle doldurur. Örneğin, uyarı değeri " {0} {1} Link!" olarak ayarlanırsa, "uyarı bağlantısı!" olarak görüntülenir.
10. *Bu öğeyi bir bağlantı yap* kutusunu seçin.
    1. *Açmak Için görünüm*' ün altında "çalışma kitabı (şablon)" öğesini seçin.
    2. *Bağlantı değeri*' nin altında, "bağla" yı seçin.
    3. *Bağlam aç dikey penceresinde bağlantıyı aç '* ı seçin.
    4. *Çalışma kitabı bağlantı ayarları* 'nda aşağıdaki ayarları seçin
        1. *Şablon kimliği*' nin altında, "sütun" öğesini seçin.
        2. *Sütun* altında "bağla" yı seçin.

    ![Yukarıdaki ayarlarla bağlantı ayarlarının ekran görüntüsü.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. *Sütunlardan*"bağla" yı seçin. *Sütun işleyicisinin* yanındaki Ayarlar ' ın altında **(sütunu gizle)** seçeneğini belirleyin.
1. "Ad" sütununun görünen adını değiştirmek için **Etiketler** sekmesini seçin. *Sütun kimliği* olarak "ad" olan satırda, * sütun etiketi altında görüntülenmesini istediğiniz adı girin.
2. **Uygula** ' yı seçin

![Yukarıdaki ayarlarla kılavuzdaki eşiklerin ekran görüntüsü](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Kesirli birim yüzdeleri

Kesirli birim (fr), çeşitli kılavuz türlerinde yaygın olarak kullanılan bir dinamik ölçü birimidir. Pencere boyutu/çözünürlüğü değiştikçe fr Width de değişir.

Aşağıdaki ekran görüntüsünde, her biri 1FR Width ve tüm eşit genişliklerde sekiz sütunlu bir tablo gösterilmektedir. Pencere boyutu değiştikçe, her sütunun genişliği orantılı olarak değişir.

[![Izgaradaki sütun genişliği değeri 1FR her bulunan sütunların ekran görüntüsü](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

Aşağıdaki görüntüde, ilk sütunun %50 genişliğine ayarlandığı durumlar dışında aynı tablo gösterilmektedir. Bu, sütunu dinamik olarak toplam kılavuz genişliğinin yarısını olacak şekilde ayarlar. Pencere boyutu çok küçük olmadığı için pencerenin yeniden boyutlandırılması %50 genişliğini tutmaya devam edecektir. Bu dinamik sütunlarda içeriğine göre en düşük genişlik vardır. Kılavuzun kalan %50 ' ü toplam sekiz kesirli birim tarafından bölünür. Aşağıdaki "Kind" sütunu 2fr olarak ayarlanmıştır, bu nedenle kalan alanın dörtte birini kaplar. Diğer sütunlar 1FR, her biri kılavuzun sağ yarısını alırlar.

[![Izgaradaki sütunların ekran görüntüsü, 1 sütun genişliği değeri %50 ve REST as 1FR her](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

Fr,%, px ve ch genişliklerini birleştirmek mümkündür ve önceki örneklerde benzer şekilde çalışır. Statik birimler (CH ve px) tarafından ayarlanan genişlikler, pencere/çözüm değiştirilse bile değişmeyecek sabit sabitlerdir. % Tarafından ayarlanan sütunlar toplam kılavuz genişliğine bağlı olarak yüzde değerini alır (daha önce en düşük genişlikler nedeniyle tam olarak olmayabilir). Fr ile ayarlanan sütunlar, ayrılan kesirli birim sayısına göre yalnızca kalan ızgara alanını bölecektir.

[![Farklı genişlik birimi sınıflı kılavuzda kullanılan sütunların ekran görüntüsü](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

* [Çalışma kitaplarında ağaç](workbooks-tree-visualizations.md)oluşturmayı öğrenin.
* [Çalışma kitabı metin parametreleri](workbooks-text.md)oluşturmayı öğrenin.