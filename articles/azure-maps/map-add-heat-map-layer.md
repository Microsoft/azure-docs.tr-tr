---
title: Azure haritalar 'a bir ısı haritası katmanı ekleme | Microsoft Docs
description: Azure Maps web SDK 'sına bir ısı haritası katmanı ekleme.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e83b3c5f7f7cb6fa729a628f01f4103d44c19df8
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976195"
---
# <a name="add-a-heat-map-layer"></a>Isı haritası katmanı ekleme

Nokta yoğunluğu haritaları olarak da bilinen ısı haritaları, bir dizi renk kullanan verilerin yoğunluğunu temsil etmek için kullanılan bir veri görselleştirmesi türüdür. Genellikle bir haritada "etkin noktalar" verilerini göstermek için kullanılır ve büyük nokta veri kümelerini işlemek için harika bir yoldur.  Örneğin, harita görünümü içindeki binlerce noktayı sembol olarak işleme, harita alanının çoğunu kapsamakta ve verilerle örtüşen birçok sembol oluşmasına yol açar. Ancak, bu veri kümesinin bir ısı haritası olarak görselleştirilmesi, nokta verilerinin nerede olduğunu ve diğer alanlara göreli yoğunluğu görmenizi kolaylaştırır. Isı eşlemelerinin kullanıldığı birçok senaryo vardır. İşte birkaç örnek;

- Sıcaklık verileri genellikle iki veri noktası arasındaki sıcaklığın yaklaşık olması halinde ısı haritası olarak işlenir.
- Gürültü sensörlerine yönelik verileri bir ısı haritası olarak işleme yalnızca algılayıcının şiddetini göstermez, ancak aynı zamanda bir mesafe üzerinde de Öngörüler elde edebilirsiniz. Herhangi bir sitedeki gürültü düzeyi yüksek olmayabilir, ancak birden çok sensörden gürültü kapsam alanı çakışırsa, bu örtüşen alanın daha yüksek gürültü düzeylerine sahip olması ve bu nedenle ısı haritasında görünebilmesini mümkün olabilir.
- Her bir veri noktasının yoğunluğu hızlı bir şekilde hızlanışında, aracın hızlanma biçimini görmek için harika bir yol olan bir yığın izlemeyi görselleştirme.

> [!TIP]
> Isı haritası katmanları varsayılan olarak, bir veri kaynağındaki tüm geometrilerin koordinatlarını işler. Katmanı yalnızca nokta geometrisi özelliklerini gösterecek şekilde sınırlamak için `filter` `['==', ['geometry-type'], 'Point']` katmanın özelliğini olarak ayarlayın ve `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` MultiPoint özelliklerini de dahil etmek istiyorsanız.

## <a name="add-a-heat-map-layer"></a>Isı haritası katmanı ekleme

Bir veri kaynağını bir ısı haritası olarak işlemek için, veri kaynağınızı `HeatMapLayer` sınıfın bir örneğine geçirin ve burada gösterildiği gibi haritaya ekleyin.

Aşağıdaki kodda, her ısı noktasının tüm yakınlaştırma düzeylerinde 10 piksellik bir yarıçapı vardır. Bu örnek, bir haritaya ısı haritası katmanını eklerken daha iyi bir kullanıcı deneyimi oluşturmak için etiket katmanının altına ekler ve Etiketler, ısı haritası üzerinde açıkça görünür. Bu örnekteki veriler, [USGS deprem hazın programından](https://earthquake.usgs.gov/) kaynaklıdır ve son 30 gün içinde gerçekleşen önemli deprem 'yi temsil eder.

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Load a data set of points, in this case earthquake data from the USGS.
datasource.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson');

//Create a heatmap and add it to the map.
map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
  radius: 10,
  opacity: 0.8
}), 'labels');
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Basit ısı haritası katmanı' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile kalem <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>basit ısı haritası katmanını</a> inceleyin.
</iframe>

## <a name="customizing-the-heat-map-layer"></a>Isı haritası katmanını özelleştirme

Önceki örnek, yarıçap ve opaklık seçeneklerini ayarlayarak ısı haritasını özelleştirdi. Isı haritası katmanı, özelleştirme için çeşitli seçenekler sağlar;

* `radius`: Her bir veri noktasının işlenmesi için bir piksel yarıçapı tanımlar. Yarıçap sabit bir sayı veya bir ifade olarak ayarlanabilir. Bir ifade kullanarak yarıçapı, harita üzerinde tutarlı bir uzamsal alanı temsil eden (örneğin, 5-mil yarıçap), yakınlaştırma düzeyine göre ölçeklemek mümkündür.
* `color`: Isı haritasının renk oluşturma şeklini belirtir. Bir renk gradyanı genellikle ısı haritaları için kullanılır ve bir `interpolate` ifadeyle elde edilebilir. Isı haritasının `step` renklendirilebilmesini için bir ifade kullanılması, yoğunluğu, bir dağılım veya radar stil eşlemesine benzer aralıklara görsel aralıklarla ayırır. Bu renk paletleri, minimum değeri en yüksek yoğunluk değerine göre tanımlar. Isı haritaları için renk değerleri, `heatmap-density` değer üzerinde bir ifade olarak belirtilir. Bir ilişkilendirme ifadesinde dizin 0 ' daki veya bir adım ifadesinin varsayılan rengi olan renk, verilerin olmadığı alanın rengini tanımlar ve bir arka plan rengi tanımlamak için kullanılabilir. Birçok, bu değeri saydam veya yarı saydam bir siyah olarak ayarlamayı tercih eder. Renk ifadelerine örnekler aşağıda verilmiştir.

| Enterpolasyon rengi Ifadesi | Basamaklı renk Ifadesi | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' enterpolageç ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' doğrusal '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmap-yoğunluklu '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, ' saydam ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' mor ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, ' #fb00fb ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1, ' #00c3ff '<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' adım ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[' heatmap-yoğunluklu '\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;' saydam ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, ' Navy ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, ' yeşil ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, ' sarı ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, ' Red '<br/>\] | 

- `opacity`: Isı haritası katmanının donuk veya saydam şeklini belirtir.
- `intensity`: Isı haritasını 'in genel yoğunluğunu artırmak için her bir veri noktasının ağırlığına bir çarpan uygular ve veri noktalarının ağırlığının görselleştirilmesi daha kolay hale gelmesine yardımcı olur.
- `weight`: Varsayılan olarak, tüm veri noktalarında 1 ağırlığı bulunur, bu nedenle tüm veri noktaları eşit olarak ağırlıklı olur. Ağırlık seçeneği bir çarpan olarak davranır ve sayı veya bir ifade olarak ayarlanabilir. Bir sayı ağırlık olarak ayarlandıysa, her bir veri noktasının Haritada iki kez yerleştirilmesi, bu nedenle yoğunluğu ikiye katmasıdır. Weight seçeneğini bir sayı olarak ayarlamak, ısı haritasını, yoğunluk seçeneğini kullanmak için benzer bir şekilde işler. Ancak, bir ifade kullanılıyorsa her bir veri noktasının ağırlığı her bir veri noktasının özelliklerine bağlı olabilir. Örnek olarak deprem verisi alın, her veri noktası bir deprem temsil eder. Her deprem veri noktası için önemli bir ölçüm, bir büyüklük değeridir. Deprem her zaman gerçekleşir, ancak en az büyüklüğü düşüktür ve hatta keçeli değildir. Her bir veri noktasına ağırlığı atamak için bir ifadede büyüklük değerinin kullanılması, ısı haritası içinde daha önemli deprem daha iyi temsil edilebilir.
- Temel katman seçeneklerinin yanı sıra, en az/en fazla yakınlaştırma, görünür ve filtre, veri kaynağınız `source` bir vektör kutucuk kaynağı ise veri kaynağını ve `source-layer` seçeneğini güncelleştirmek isterseniz de bir seçenek vardır.

Farklı ısı haritası katman seçeneklerini test etmek için bir araç aşağıda verilmiştir.

<br/>

<iframe height='700' scrolling='no' title='Isı haritası katman seçenekleri' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>ısı haritası katman seçeneklerine</a> bakın.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Tutarlı Zoomable ısı haritası

Varsayılan olarak, ısı haritası katmanında işlenen veri noktalarının yarıçapının tüm yakınlaştırma düzeyleri için sabit bir piksel yarıçapı vardır. Eşleme yakınlaştırıldığında, veri toplar ve ısı haritası katmanı farklı görünüyor. Her `zoom` bir yakınlaştırma düzeyi için yarıçapı ölçeklendirmek için, her bir veri noktasının haritanın aynı fiziksel alanını kapsaması gibi bir ifade kullanılabilir. Bu, ısı haritası katmanının daha statik ve tutarlı görünmesini sağlayacak. Haritanın her yakınlaştırma düzeyinin, önceki yakınlaştırma düzeyiyle dikey ve yatay olarak iki piksel daha vardır. Her yakınlaştırma düzeyiyle birlikte bulunan yarıçapı ölçeklemek, tüm yakınlaştırma düzeylerinde tutarlı görünen bir ısı haritası oluşturur. Bu, `zoom` aşağıdaki örnekte gösterildiği gibi temel 2 `exponential interpolation` ifadesiyle kullanılarak gerçekleştirilebilir. Isı haritasının yakınlaştırma düzeyiyle nasıl ölçeklendirçalıştığını görmek için haritayı yakınlaştırın.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Tutarlı Zoomable ısı haritası" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile kalem <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>tutarlı Zoomable ısı haritasını</a> inceleyin.
</iframe>

> [!TIP]
> Veri kaynağında kümelendirmeyi etkinleştirerek birbirlerine yakın olan noktaları kümelenmiş bir nokta olarak birlikte gruplandırılır. Her kümenin nokta sayısı, ısı haritası için ağırlık ifadesi olarak kullanılabilir ve işlenmesi gereken noktaların sayısını önemli ölçüde azaltır. Bir kümenin nokta sayısı, aşağıda gösterildiği gibi Point özelliğinin `point_count` bir özelliğinde depolanır. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Kümeleme yarıçapı yalnızca birkaç pikselle, işleme göre önemli bir görsel fark olacaktır. Daha büyük bir yarıçap, her kümeye daha fazla işaret sağlar ve heatmap 'in performansını geliştirir, ancak farklılık gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Heatmaoyuncu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

Haritalarınıza eklenecek daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Veri kaynağı oluşturma](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Veri tabanlı stil ifadeleri kullanın](data-driven-style-expressions-web-sdk.md)