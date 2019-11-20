---
title: Azure haritalar 'da kümeleme noktası verileri | Microsoft Docs
description: Web SDK 'da nokta verileri kümesi oluşturma
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 5f51c1166364a3470a1cc943e66d429c32cdc49b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839484"
---
# <a name="clustering-point-data"></a>Kümeleme noktası verileri

Haritadaki çok sayıda veri noktasını görselleştirirken, eşleme bozulur ve kullanımı zor olur. Nokta verisi Kümelemesi, bu kullanıcı deneyimini geliştirmek için kullanılabilir. Kümeleme noktası verileri, birbirine yakın olan nokta verisini birleştirme ve bunları haritada tek bir kümelenmiş veri noktası olarak temsil etme işlemidir. Kullanıcı haritada yakınlaştırdıktan sonra, kümeler kendi bireysel veri noktalarıyla kesilir.

## <a name="enabling-clustering-on-a-data-source"></a>Veri kaynağında kümeleme etkinleştiriliyor

Küme, `DataSource` `cluster` seçeneği doğru olarak ayarlanarak sınıfında kolayca etkinleştirilebilir. Ayrıca, bir kümede birleştirilecek olan yakın noktaları seçmek için piksel yarıçapı, `clusterRadius` kullanılarak ayarlanabilir ve `clusterMaxZoom` seçeneği kullanılarak kümeleme mantığının devre dışı bırakılması için bir yakınlaştırma düzeyi belirtilebilir. Veri kaynağında kümelemenin nasıl etkinleştirileceği hakkında bir örnek aşağıda verilmiştir.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,

    //The radius in pixels to cluster points together.
    clusterRadius: 45,

    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> İki veri noktası arka planda yakınsa, kullanıcının ne kadar yakın yakınlaştırılmadığına bakılmaksızın kümenin hiçbir şekilde hiçbir şekilde ayrılamamaları mümkündür. Bunu çözmek için, küme mantığını devre dışı `clusterMaxZoom` bırakmak ve yalnızca her şeyi göstermek için yakınlaştırma düzeyinde belirten veri kaynağı seçeneğini ayarlayabilirsiniz.

Sınıfı `DataSource` , kümeleme ile ilgili aşağıdaki yöntemlere de sahiptir:

| Yöntem | Dönüş türü | Açıklama |
|--------|-------------|-------------|
| getClusterChildren (Clusterıd: Number) | Promise&lt;dizi&lt;özelliğigeometrisi&lt;, herhangi&gt; bir şekil\|&gt;&gt; | Sonraki yakınlaştırma düzeyinde verilen kümenin alt öğelerini alır. Bu alt öğeler şekil ve alt kümelerin bir birleşimi olabilir. Alt kümeler, ClusteredProperties ile eşleşen özelliklerle özellik olacaktır. |
| getClusterExpansionZoom (Clusterıd: Number) | Promise&lt;numarası&gt; | Kümenin genişlemekte veya parçalanmasına başlayacağı yakınlaştırma düzeyini hesaplar. |
| Getclusteryaprakları (Clusterıd: Number, limit: Number, kayması: Number) | Promise&lt;dizi&lt;özelliğigeometrisi&lt;, herhangi&gt; bir şekil\|&gt;&gt; | Kümedeki tüm noktaları alır. ' İ `limit` , noktaların bir alt kümesini döndürecek şekilde ayarlayın ve ' ı `offset` noktaları üzerinden kullanın. |

## <a name="display-clusters-using-a-bubble-layer"></a>Balon bir katman kullanarak kümeleri görüntüleme

Kabarcık katmanı, yarıçapı kolayca ölçeklendirebilmeniz ve bir ifade kullanarak kümedeki noktaların sayısına göre renkleri değiştirebilmeniz için, kümelenmiş noktaları işlemek için harika bir yoldur. Bir kabarcık katmanı kullanarak kümeler görüntülenirken, kümelenmemiş veri noktalarını işlemek için ayrı bir katman da kullanmanız gerekir. Ayrıca, kabarcıkların üst kısmında kümenin boyutunu görüntüleyebilmek de iyi bir hale gelir. Bu davranışa ulaşmak için, metin ve simge içeren bir sembol katmanı kullanılabilir. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Temel kabarcık katmanı Kümelemesi" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>temel balon katmanı kümelemesine</a> bakın.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Sembol katmanı kullanarak kümeleri görüntüleme

Sembol katmanını kullanarak nokta verilerini görselleştirirken, varsayılan olarak, temizleyici bir deneyim oluşturmak için birbirleriyle örtüşen sembolleri otomatik olarak gizler, ancak haritadaki veri noktalarının yoğunluğunu görmek isterseniz bu işlem istenen deneyim olmayabilir. `true` Sembol katmanları `iconOptions` özelliğinin seçeneği bu deneyimi devre dışı bırakmak için ayarlandığında, ancak tüm simgelerin görüntülenmesine neden olur. `allowOverlap` Kümeleme kullanmak, iyi bir temiz Kullanıcı deneyimi oluştururken tüm verilerin yoğunluğunu görmenizi sağlar. Bu örnekte, kümeleri ve bireysel veri noktalarını göstermek için özel semboller kullanılacaktır.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Kümelenmiş sembol katmanı" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından kullanılan kalem <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>kümelenmiş sembol katmanını</a> görüntüleyin.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Kümeleme ve ısı haritaları katmanı

Isı haritaları haritadaki verilerin yoğunluğunu görüntülemenin harika bir yoludur. Bu görselleştirme, kendi başına çok sayıda veri noktasını işleyebilir, ancak veri noktaları kümelenmişse ve küme boyutu ısı haritasının ağırlığı olarak kullanılıyorsa daha da fazla veri işleyebilir. Bunu başarmak için ısı haritası katmanının `['get', 'point_count']` seçeneğiniolarakayarlayın.`weight` Küme yarıçapı küçük olduğunda, ısı haritası kümelenmemiş veri noktalarını kullanarak bir ısı haritası ile neredeyse özdeş olur ancak çok daha iyi işlem yapar. Ancak, küme yarıçapı ne kadar küçükse, ısı haritası daha doğru olur ancak daha az performans avantajı vardır.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Küme ağırlıklı ısı haritası" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>kümesi ağırlıklı ısı haritasını</a> inceleyin.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Kümelenmiş veri noktalarında fare olayları

Kümelenmiş veri noktaları içeren bir katmanda fare olayları gerçekleştiğinde, kümelenmiş veri noktası olaya GeoJSON Point özellik nesnesi olarak döndürülür. Bu nokta özelliği aşağıdaki özelliklere sahip olacaktır:

| Özellik adı | Type | Açıklama |
|---------------|------|-------------|
| küme | boolean | Özelliğin bir kümeyi temsil ettiğini belirtir. |
| cluster_id | dize | Küme için, veri kaynağı `getClusterExpansionZoom`, `getClusterChildren`ve `getClusterLeaves` yöntemleriyle kullanılabilecek benzersiz bir kimlik. |
| point_count | numarası | Kümenin içerdiği noktaların sayısı. |
| point_count_abbreviated | dize | Long ise `point_count` değeri abbreviates bir dize. (örneğin, 4.000 4K olur) |

Bu örnek, küme noktalarını işleyen bir kabarcık katmanı alır ve bir sonraki yakınlaştırma düzeyi `getClusterExpansionZoom` `DataSource` içinHaritayıtetiklendiğinde,hesapladığınızdaveyakınlaştırdığındabirtıklamaolayıekler.`cluster_id` tıklanan kümelenmiş veri noktasının özelliği. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Küme getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile ilgili <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getclusterexpansionzoom kalem kümesine</a> bakın.
</iframe>

## <a name="display-cluster-area"></a>Küme alanını görüntüle 

Bir kümenin temsil ettiği nokta verileri bir alanın üzerine yayılır. Bu örnekte, fare bir kümenin üzerine geldiğinde, içerdiği tek tek veri noktaları (yaprakları) bir convex kabuk hesaplamak için kullanılır ve alanı göstermek için haritada görüntülenir. Bir kümede yer alan tüm noktaları, `getClusterLeaves` yöntemi kullanılarak veri kaynağından alınabilir. Bir convex kabuk, esnek bant gibi bir dizi noktayı sarmalayan ve `atlas.math.getConvexHull` yöntemi kullanılarak hesaplanabilecek bir çokgen.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Küme alanı convex kabuk" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convex kabuk olan kalem kümesi alanı</a> ' na bakın.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Kümelerdeki verileri toplama

Genellikle kümeler, küme içindeki noktaların sayısıyla birlikte bir sembol kullanılarak temsil edilir, ancak bazen küme içindeki tüm noktaların toplam geliri gibi bazı bir ölçüme göre kümelerin stilini daha da özelleştirmek istenebilir. Küme toplamaları ile özel özellikler, bir [toplama ifadesi](data-driven-style-expressions-web-sdk.md#aggregate-expression) hesaplaması kullanılarak oluşturulup doldurulabilir.  Küme toplamaları, `clusterProperties` `DataSource`seçeneğinde tanımlanabilir.

Aşağıdaki örnek, bir kümedeki her bir veri noktasının varlık türü özelliğine dayalı olarak bir sayıyı hesaplamak için bir toplama ifadesi kullanır.

<iframe height="500" style="width: 100%;" scrolling="no" title="Küme toplamaları" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile ilgili kalem <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>kümesi toplamlarını</a> inceleyin.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [DataSource sınıfı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [DataSourceOptions nesnesi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.datasourceoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas. Math ad alanı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)

Uygulamanıza işlevsellik eklemek için bkz. kod örnekleri:

> [!div class="nextstepaction"]
> [Kabarcık katmanı ekleme](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](map-add-pin.md)

> [!div class="nextstepaction"]
> [Isı haritası katmanı ekleme](map-add-heat-map-layer.md)
