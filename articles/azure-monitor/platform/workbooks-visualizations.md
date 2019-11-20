---
title: Azure Izleyici çalışma kitapları görselleştirmeleriyle etkileşimli raporlar oluşturma | Microsoft docs
description: Metin, grafikler, ızgaralar, ağaçlar ve grafikler dahil olmak üzere tüm Azure Izleyici çalışma kitabı görselleştirme bileşenleri hakkında bilgi edinin.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d878e8b5dd49ba517f85ebb74332bc6a245c33ca
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73165062"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Izleyici çalışma kitabı görselleştirmeleri

Azure Izleyici çalışma kitapları, raporlama ihtiyaçlarınızı karşılamak için birçok farklı görselleştirme stilini destekler. Bu makalede, her görselleştirme türünün örnekleri verilmiştir.

## <a name="text"></a>Metin

Çalışma kitapları yazarların çalışma kitaplarına metin blokları eklemesine izin verir. Metin, kullanıcıların verilerinizi yorumlamasını, Bölüm başlıklarınızı vb. sağlamak için Telemetriyi insan olarak analiz edebilir.

![Apdex metin tablosunun ekran görüntüsü](./media/workbooks-visualizations/apdex.png)

Metin, tam biçimlendirme denetimi sağlayan bir Markaşağı denetimi aracılığıyla eklenir.

![İşlenmiş tabloyu oluşturan ham Markup ekran görüntüsü](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Metin denetimi ekleme

1. **Düzenleme** araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir metin denetimi eklemek için **metin ekle** bağlantısını kullanın.
3. Denetime marka ekleyin.
4. Biçimli metni görmek için, **Düzenle** düğmesine tıklayın.

> [!TIP]
> Farklı biçimlendirme seçenekleri hakkında bilgi edinmek için bu [Markaşağı](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) ile bu sayfayı kullanın.

## <a name="charts"></a>grafik

Çalışma kitapları, izleme verilerinin grafik olarak sunulmasını sağlar. Desteklenen grafik türleri çizgi, çubuk, çubuk kategorik, alan, dağılım çizimleri, pasta ve saati içerir. Yazarlar, grafiğin yüksekliğini, genişliğini, renk paletini, göstergeyi, başlıkları, veri içermeyen iletiyi ve vb. özelleştirmeyi seçebilirler.

Çalışma kitapları hem Günlükler hem de ölçüm veri kaynakları için grafikleri destekler. 

### <a name="adding-a-log-chart"></a>Günlük grafik ekleme

1. **Düzenleme** araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analizinize yönelik [KQL](https://docs.microsoft.com/azure/kusto/query/) 'yi girmek için sorgu düzenleyicisini kullanın (örneğin, isteklerin eğilimi).
5. Görselleştirmeyi şunlardan birine ayarlayın: **alan**, **çubuk**, **çubuk (kategorik)** , **çizgi**, **pasta**, **dağılım**veya **zaman**.
6. Gerekirse daha fazla parametre belirleyin; örneğin, zaman aralığı, görselleştirme, boyut, renk paleti ve gösterge.

![Düzenleme modundaki günlük grafiğinin ekran görüntüsü](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Günlük grafiği parametreleri

| Parametre | Açıklama | Örnek |
| ------------- |:-------------|:-------------|
| `Query Type` | Kullanılacak sorgunun türü | Günlük, Azure Kaynak Grafiği vb. |
| `Resource Type` | Hedeflenecek kaynak türü | Application Insights, Log Analytics veya Azure-önce |
| `Resources` | Ölçüm değerinin alınacağı kaynak kümesi | MyApp1 |
| `Time Range` | Günlük grafiğinin görüntüleneceği zaman penceresi | Son saat, son 24 saat, vb. |
| `Visualization` | Kullanılacak görselleştirme | Alan, çubuk, çizgi, pasta, dağılım, saat, çubuk kategorik |
| `Size` | Denetimin dikey boyutu | Küçük, orta, büyük veya tam |
| `Color palette` | Grafikte kullanılacak renk paleti. Çok ölçümlü veya kesimli modda yoksayıldı. | Mavi, yeşil, kırmızı, vb. |
| `Legend` | Gösterge için kullanılacak toplama işlevi | Değerlerin toplamı veya ortalaması ya da Max, min, First, Last değeri |
| `Query` | Grafik görselleştirmesi tarafından beklenen biçimde veri döndüren herhangi bir KQL sorgusu | _\|-serisi istekleri = Count () varsayılan = 0 (1D) zaman damgasında şimdi (). Adım 1 sa_ |

### <a name="adding-a-metric-chart"></a>Ölçüm grafiği ekleme

1. **Düzenleme** araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına ölçüm denetimi eklemek için **ölçüm Ekle** bağlantısını kullanın.
3. Kaynak türünü (örneğin, depolama hesabı), hedeflenecek kaynakları, ölçüm ad alanını ve adı ve kullanılacak toplamayı seçin.
4. Gerekirse, daha fazla zaman aralığı, bölme, görselleştirme, boyut ve renk paleti gibi diğer parametreleri ayarlayın.

![Düzenleme modundaki ölçüm grafiğinin ekran görüntüsü](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Ölçüm grafiği parametreleri

| Parametre | Açıklama | Örnek |
| ------------- |:-------------|:-------------|
| `Resource Type` | Hedeflenecek kaynak türü | Depolama alanı veya sanal makine. |
| `Resources` | Ölçüm değerinin alınacağı kaynak kümesi | MyStorage1 |
| `Namespace` | Ölçüm ile ad alanı | Depolama > blobu |
| `Metric` | Görselleştirilecek ölçüm | Depolama > blob > Işlemleri |
| `Aggregation` | Ölçüm için uygulanacak toplama işlevi | Toplam, sayı, ortalama, vb. |
| `Time Range` | Ölçümün görüntüleneceği zaman penceresi | Son saat, son 24 saat, vb. |
| `Visualization` | Kullanılacak görselleştirme | Alan, çubuk, çizgi, dağılım, kılavuz |
| `Split By` | İsteğe bağlı olarak bir boyuttaki ölçüyü bölme | Coğrafi türe göre işlemler |
| `Size` | Denetimin dikey boyutu | Küçük, orta veya büyük |
| `Color palette` | Grafikte kullanılacak renk paleti. `Split by` parametresi kullanılırsa yoksayıldı | Mavi, yeşil, kırmızı, vb. |

## <a name="grids"></a>Larına

Kılavuzlar veya tablolar, verileri kullanıcılara sunmak için yaygın bir yoldur. Çalışma kitapları kullanıcıların, raporları için zengin bir kullanıcı arabirimi sağlamak üzere kılavuzun sütunlarını tek tek tarzlarına olanak tanır.

Aşağıdaki örnekte, simgeler, helemaps ve Spark-çubuklarını birleştiren ve karmaşık bilgileri sunan bir kılavuz gösterilmektedir. Çalışma kitabı ayrıca sıralama, arama kutusu ve Analize Git düğmesi de sağlar.

![Günlük tabanlı kılavuzun ekran görüntüsü](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Günlük tabanlı kılavuz ekleme

1. **Düzenleme** araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analizinize yönelik KQL 'yi (örneğin, bir eşiğin altında belleğe sahip VM 'Ler) girmek için sorgu düzenleyicisini kullanın
5. Görselleştirmeyi **kılavuza** ayarla
6. Gerekirse daha fazla parametre belirleyin, örneğin, zaman aralığı, boyut, renk paleti ve gösterge.

![Günlük tabanlı kılavuz sorgusunun ekran görüntüsü](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Parça

Kutucuklar, çalışma kitaplarında Özet verileri sunmak için çok faydalı bir yoldur. Aşağıdaki görüntüde, ayrıntılı bir kılavuzun en üstünde yer alan, uygulama düzeyi özetinin yaygın kullanım durumu gösterilmektedir.

![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-visualizations/tiles-summary.png)

Çalışma kitabı kutucukları; bir başlık, alt başlık, büyük metin, simge, ölçüm tabanlı degradeler, Spark çizgisi/çubukları, alt bilgi vb. gösteren

### <a name="adding-a-tile"></a>Kutucuk ekleme

1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın. 
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analiz etmek için KQL 'i girmek üzere sorgu düzenleyicisini kullanın
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Boyutu **tam** olarak ayarla
6. Görselleştirmeyi **döşemeler** olarak ayarlama
7. Ayarlar bölmesini açmak için **kutucuk ayarları** düğmesine tıklayın
8. **Döşeme alanları**' nda, şunu ayarlayın:
    * Başlık: `name`
    * Sol: `Requests`, Işleyici: `Big Number`, renk paleti: `Green to Red`, en düşük değer: `0`
    * Alt: `appName`
9. Bölmenin alt kısmındaki **Kaydet ve Kapat** düğmesine tıklayın.

![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-visualizations/tile-settings.png)

Bu, kutucukların okuma modunda nasıl görüneceğini aşağıda bulabilirsiniz:

![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Ağaçlarında

Çalışma kitapları ağaç kılavuzları aracılığıyla hiyerarşik görünümleri destekler. Ağaçlar, bir ayrıntıya gitme deneyimi için bazı satırların bir sonraki düzeye Genişletilebilir olmasını sağlar.

Aşağıdaki örnekte, ağaç ızgarası olarak görselleştirilen kapsayıcı sistem durumu ölçümleri (çalışma kümesi boyutu) gösterilmektedir. Burada en üst düzey düğümler Azure Kubernetes Service (aks) düğümlerdir, bir sonraki düzey Pod ve son düzeyi kapsayıcılardır. Yine de sütunlarınızı bir kılavuzda (heatmap, simgeler, bağlantı) biçimlendirme yapabildiğinize dikkat edin. Bu durumda temel alınan veri kaynağı AKS günlüklerine sahip bir Log Analytics çalışma alanıdır.

![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Ağaç ızgarası ekleme
1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın. 
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analiz etmek için KQL 'i girmek üzere sorgu düzenleyicisini kullanın
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Görselleştirmeyi **kılavuza** ayarla
6. Ayarlar bölmesini açmak için **sütun ayarları** düğmesine tıklayın
7. Alt kısımdaki **ağaç/grup ayarları** bölümünde, şunu ayarlayın:
    * Ağaç türü: `Parent/Child`
    * Kimlik alanı: `Id`
    * Üst kimlik alanı: `ParentId`
    * Genişleticiyi göster: `Name`
    * Ağacın en üst düzeyini genişletin: `checked`
8. Üstteki _sütunlar_ bölümünde, şunu ayarlayın:
    * _Kimlik_ sütunu oluşturucu: `Hidden`
    * _Üst kimlik_ -sütun oluşturucu: `Hidden`
    * _İstek_ sütunu işleyici: `Bar`, Color: `Blue`, minimum değer: `0`
9. Bölmenin alt kısmındaki _Kaydet ve Kapat_ düğmesine tıklayın.    

![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Ağaç ayarları

| Ayar | Açıklama |
|:------------- |:-------------|
| `Id Field` | Kılavuzdaki her satırın benzersiz kimliği |
| `Parent Id Field` | Geçerli satırın üst öğesinin kimliği |
| `Show the expander on` | Ağaç genişleticisindeki gösterileceği sütun. Ağaç kılavuzlarında çok okunabilir olmadıklarından kimlik ve üst kimlik alanlarını gizleyebilmek yaygındır. Bunun yerine, genişletici, varlığın adı gibi daha okunabilir bir değere sahip bir alanda görünür |
| `Expand the top level of the tree` | İşaretliyse, ağaç Kılavuzu en üst düzeyde genişletilir. Varsayılan olarak daha fazla bilgi göstermek istiyorsanız kullanışlıdır |

## <a name="graphs"></a>Performansının

Çalışma kitapları, izleme varlıkları arasındaki ilişkileri göstermek için günlüklerdeki verileri temel alarak rastgele grafikleri görselleştirmeyi destekler.

Aşağıdaki grafikte, dış bilgisayarlardan gelen/giden çeşitli bağlantı noktaları aracılığıyla bir bilgisayarın içindeki/dışındaki veri akışı gösterilmektedir. Bu, türe (bilgisayar ile bağlantı noktası ve dış IP) göre renklendirilir ve kenar boyutları, içindeki veri akışı miktarına karşılık gelir. Temel veriler, KQL sorgu hedefleme VM bağlantılarından gelir.

![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Grafik ekleme
1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın. 
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analiz etmek için KQL 'i girmek üzere sorgu düzenleyicisini kullanın
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Görselleştirmeyi **grafiğe** ayarla
8. Ayarlar bölmesini açmak için **grafik ayarları** düğmesine tıklayın
9. Alttaki _Düzen alanlarında_ , şunu ayarlayın:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. En üstteki _düğüm biçimi ayarları_ ' nda, şunu ayarlayın:
    * _Popüler içerik_-sütun kullanım: `Name`, sütun oluşturucu: `Text`
    * _Center Content_-kullanım sütunu: `Calls`, sütun oluşturucu: `Big Number`, renk paleti: `None`
    * _Alt içerik_-kullanılacak sütun: `Kind`, sütun oluşturucu: `Text`
10. Bölmenin alt kısmındaki _Kaydet ve Kapat_ düğmesine tıklayın.

![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Resource Manager çalışma kitaplarını [dağıtın](workbooks-automate.md) .
* Çalışma kitabı kaynaklarınıza erişimi [denetleme](workbooks-access-control.md) ve paylaşma.