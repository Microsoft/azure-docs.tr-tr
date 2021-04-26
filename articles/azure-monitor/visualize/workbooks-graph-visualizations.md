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
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100625651"
---
# <a name="graph-visualizations"></a>Grafik görselleştirmeleri

Çalışma kitapları, izleme varlıkları arasındaki ilişkileri göstermek için günlüklerdeki verileri temel alarak rastgele grafikleri görselleştirmeyi destekler.

Aşağıdaki grafikte, dış bilgisayarlardan gelen/giden bağlantı noktaları aracılığıyla bir bilgisayarın içindeki/dışındaki veri akışı gösterilmektedir. Bu, türe (bilgisayar ile bağlantı noktası ve dış IP) göre renklendirilir ve kenar boyutları, içindeki veri akışı miktarına karşılık gelir. Temel veriler, KQL sorgu hedefleme VM bağlantılarından gelir.

[![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Grafik ekleme
1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analizinize yönelik KQL 'yi girmek için sorgu düzenleyicisini kullanın.

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

5. Görselleştirmeyi **grafiğe** ayarla
6. Ayarlar bölmesini açmak için **grafik ayarları** düğmesini seçin
7. Alttaki _Düzen alanlarında_ , şunu ayarlayın:
    * Düğüm kimliği: `Id`
    * Kaynak kimliği: `SourceId`
    * Hedef kimliği: `TargetId`
    * Kenar etiketi: `None`
    * Kenar boyutu: `Calls`
    * Düğüm boyutu: `None`
    * Renklendirme türü: `Categorical`
    * Düğüm renk alanı: `Kind`
    * Renk paleti: `Pastel`
8. En üstteki _düğüm biçimi ayarları_ ' nda, şunu ayarlayın:
    * _Popüler içerik_-sütunu kullanın: `Name` , sütun Oluşturucu: `Text`
    * _Center içeriği_-sütun kullanım: `Calls` , sütun Oluşturucu: `Big Number` , renk paleti: `None`
    * _Alt içerik_-sütunu kullanın: `Kind` , sütun Oluşturucu: `Text`
9. Bölmenin alt kısmındaki _Kaydet ve Kapat_ düğmesini seçin.

[![Yukarıdaki sorgu ve ayarlarla kutucuk Özeti görünümünün ekran görüntüsü.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Grafik ayarları

| Ayar         | Açıklama                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Grafikteki düğümlerin benzersiz KIMLIĞINI sağlayan bir sütun seçer. Sütunun değeri dize veya sayı olabilir. |
| `Source Id`     | Grafikteki kenarlar için kaynak düğümlerinin kimliklerini sağlayan bir sütun seçer. Değerler, _düğüm kimliği_ sütunundaki bir değere eşlenmelidir. |
| `Target Id`     | Grafikteki kenarlar için hedef düğümlerin kimliklerini sağlayan bir sütun seçer. Değerler, _düğüm kimliği_ sütunundaki bir değere eşlenmelidir. |
| `Edge Label`    | Grafikte kenar etiketleri sağlayan bir sütun seçer.                                                            |
| `Edge Size`     | Kenar genişliklerinin dayandığı ölçümü sağlayan bir sütun seçer.                                |
| `Node Size`     | Düğüm alanlarının temel alınacağı ölçümü sağlayan bir sütun seçer.                                 |
| `Coloring Type` | Düğüm renklendirme düzenini seçmek için kullanılır.                                                                            |

## <a name="node-coloring-types"></a>Düğüm renklendirme türleri

| Renklendirme türü | Açıklama |
|:------------- |:------------|
| `None`        | Tüm düğümler aynı renge sahip. |
| `Categorical` | Düğümlere, sonuç kümesindeki bir sütundan değere veya kategoriye göre renkler atanır. Yukarıdaki örnekte, renklendirme sonuç kümesinin sütun _türünü_ temel alır. Desteklenen paletler `Default` , `Pastel` ve `Cool tone` .  |
| `Field Based` | Bu türde, bir sütun, düğüm için kullanılmak üzere belirli bir RGB değeri sağlar. En çok esnekliği sağlar, ancak genellikle daha fazla iş gerektirir.  |

## <a name="node-format-settings"></a>Düğüm biçimi ayarları

Grafik yazarları, bir düğümün farklı bölümlerine hangi içeriğin gidebileceklerini belirtebilir: üst, sol, orta, sağ ve alt. Grafikler, işleyicilere çalışma kitaplarının hiçbirini destekler (metin, büyük sayı, Spark çizgileri, simge, vb.).

## <a name="field-based-node-coloring"></a>Alan tabanlı düğüm renklendirmesi

1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgusu denetimi eklemek için **Sorgu Ekle** bağlantısını kullanın.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analizinize yönelik KQL 'yi girmek için sorgu düzenleyicisini kullanın.

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
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Görselleştirmeyi *grafiğe* ayarla
6. Ayarlar bölmesini açmak için **grafik ayarları**  düğmesini seçin.
7. Alttaki *Düzen alanlarında* , şunu ayarlayın:
    * Düğüm Kimliği:`Id`
    * Kaynak kimliği: `SourceId`
    * Hedef kimliği: `TargetId`
    * Kenar etiketi: `None`
    * Kenar boyutu: `Calls`
    * Düğüm boyutu: `Node`
    * Renklendirme türü: `Field Based`
    * Düğüm renk alanı: `Color`
8. En üstteki *düğüm biçimi ayarları* ' nda, aşağıdakileri girin.
    * *En üstteki içerik*' de şunları ayarlayın:
        * Sütunu kullan: `Name` .
        * Sütun Oluşturucu: `Text` .
    * *Center içeriği*' nde şunları ayarlayın:
        * Sütunu kullan: `Calls`
        * Sütun Oluşturucu: `Big Number`
        * Renk paleti: `None`
    * *Alt içerikte*, şunu ayarlayın:
        * Sütunu kullan: `Kind`
        * Sütun Oluşturucu: `Text` .
9. Bölmenin alt kısmındaki **Kaydet ve Kapat düğmesini** seçin.

[![Alan temel düğümü renklendirmesi ile grafik görselleştirmesinin oluşturulmasını gösteren ekran görüntüsü.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

* Grafikler Ayrıca bileşik çubuk oluşturucuyu destekler. Daha fazla bilgi edinmek için [Birleşik çubuk belgelerini](workbooks-composite-bar.md)ziyaret edin.
* Çalışma kitaplarında kullanabileceğiniz [veri kaynakları](workbooks-data-sources.md) hakkında daha fazla bilgi edinin.
