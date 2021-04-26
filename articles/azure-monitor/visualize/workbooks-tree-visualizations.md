---
title: Azure Izleyici çalışma kitabı ağaç görselleştirmeleri
description: Tüm Azure Izleyici çalışma kitabı ağacı görselleştirmeleri hakkında bilgi edinin.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a815f4dcf7c76d4c11b4f2e227802b15f9c92a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100625232"
---
# <a name="tree-visualizations"></a>Ağaç görselleştirmeleri

Çalışma kitapları ağaç kılavuzları aracılığıyla hiyerarşik görünümleri destekler. Ağaçlar, bir ayrıntıya gitme deneyimi için bazı satırların bir sonraki düzeye Genişletilebilir olmasını sağlar.

Aşağıdaki örnekte, ağaç ızgarası olarak görselleştirilen kapsayıcı sistem durumu ölçümleri (çalışma kümesi boyutu) gösterilmektedir. Burada en üst düzey düğümler Azure Kubernetes Service (AKS) düğümlerdir, bir sonraki düzey pods ve son düzeyi kapsayıcılardır. Yine de sütunlarınızı bir kılavuzda (heatmap, simgeler, bağlantı) biçimlendirme yapabildiğinize dikkat edin. Bu durumda temel alınan veri kaynağı AKS günlüklerine sahip bir Log Analytics çalışma alanıdır.

[![Kutucuk Özet görünümünün ekran görüntüsü](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Ağaç ızgarası ekleme
1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgu denetimi eklemek için **Ekle** ' yi ve ardından *Sorgu Ekle* ' yi seçin.
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
6. Ayarlar bölmesini açmak için **sütun ayarları** düğmesini seçin
7. Alt kısımdaki **ağaç/grup ayarları** bölümünde, şunu ayarlayın:
    * Ağaç türü: `Parent/Child`
    * Kimlik alanı: `Id`
    * Üst kimlik alanı: `ParentId`
    * Genişleticiyi göster: `Name`
    * *Ağacın en üst düzeyini Genişlet* onay kutusunu seçin.
8. Üstteki _sütunlar_ bölümünde, şunu ayarlayın:
    * _Kimlik_ sütunu işleyici: `Hidden`
    * _Üst kimlik_ -sütun Oluşturucu: `Hidden`
    * _İstek_ sütunu işleyici: `Bar` , color: `Blue` , minimum değer: `0`
9. Bölmenin alt kısmındaki **Kaydet ve Kapat** düğmesini seçin.

[![Yukarıdaki sorgu ve ayarlarla kutucuk Özeti görünümünün ekran görüntüsü.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Ağaç ayarları

| Ayar | Açıklama |
|:------------- |:-------------|
| `Id Field` | Kılavuzdaki her satırın benzersiz KIMLIĞI. |
| `Parent Id Field` | Geçerli satırın üst öğesinin KIMLIĞI. |
| `Show the expander on` | Ağaç genişleticisindeki gösterileceği sütun. Ağaç kılavuzlarında çok okunabilir olmadıklarından KIMLIK ve üst KIMLIK alanlarını gizleyebilmek yaygındır. Bunun yerine, genişletici bir alanda varlık adı gibi daha okunabilir bir değere sahip görünür. |
| `Expand the top level of the tree` | İşaretliyse, ağaç Kılavuzu en üst düzeyde genişletilir. Varsayılan olarak daha fazla bilgi göstermek istiyorsanız yararlı olur. |

## <a name="grouping-in-a-grid"></a>Kılavuzda gruplandırma

Gruplandırma, önemli ölçüde daha basit sorgularla, yukarıdaki verilerle benzer hiyerarşik görünümler oluşturmanıza olanak sağlar. Ağacın iç düğümlerinde toplamayı kaybedersiniz, ancak bazı senaryolarda bu kabul edilebilir. Temel alınan sonuç kümesi uygun bir ücretsiz forma dönüştürülemese, örneğin uyarı, sistem durumu ve ölçüm verileri gibi ağaç görünümleri oluşturmak için *Group By* kullanın.

## <a name="adding-a-tree-using-grouping"></a>Gruplandırma kullanarak ağaç ekleme

1. _Düzenleme_ araç çubuğu öğesine tıklayarak çalışma kitabını düzenleme moduna geçirin.
2. Çalışma kitabına bir günlük sorgu denetimi eklemek için **Ekle** ' yi ve ardından *Sorgu Ekle* ' yi seçin.
3. Sorgu türünü **günlük**, kaynak türü (örneğin, Application Insights) ve hedeflenecek kaynakları seçin.
4. Analiz etmek için KQL 'i girmek üzere sorgu düzenleyicisini kullanın
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Görselleştirmeyi *kılavuza* ayarlayın.
2. Ayarlar bölmesini açmak için **sütun ayarları düğmesini** seçin.
3. Alt kısımdaki *ağaç/grup ayarları* bölümünde, şunu ayarlayın:
    * Ağaç türü: `Group By`
    * Gruplandırma ölçütü: `App`
    * Daha sonra: `None`
    * *Ağacın en üst düzeyini Genişlet* onay kutusunu seçin.
4. Üstteki *sütunlar* bölümünde, şunu ayarlayın:
    * *Uygulama* sütunu işleyici: `Hidden`
    * *İstek* sütunu işleyici: `Bar` , color: `Blue` , minimum değer: `0`
5. Bölmenin alt kısmındaki **Kaydet ve Kapat** düğmesini seçin.

[![Çalışma kitaplarında ağaç görselleştirmesinin oluşturulmasını gösteren ekran görüntüsü](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

* [Çalışma kitaplarında bir grafik](workbooks-graph-visualizations.md)oluşturmayı öğrenin.
* [Çalışma kitaplarında kutucuk](workbooks-tile-visualizations.md)oluşturmayı öğrenin.
