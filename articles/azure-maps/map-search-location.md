---
title: Arama sonuçlarını haritada göster | Microsoft Azure haritaları
description: Bu makalede, Microsoft Azure haritaları Web SDK 'sını kullanarak bir arama isteği gerçekleştirmeyi ve sonuçları haritada görüntülemeyi öğreneceksiniz.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 647a8fc25f27ef7f441ed7459ecd543d4f35581e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895282"
---
# <a name="show-search-results-on-the-map"></a>Haritada arama sonuçlarını göster

Bu makalede, ilgilendiğiniz konumun nasıl aranacağı ve arama sonuçlarının haritada gösterilmesi gösterilmektedir.

İlgi çekici bir konum aramak için iki yol vardır. Bir yol, bir arama isteği oluşturmak için hizmet modülünü kullanmaktır. Diğer bir yöntem de, [Fetch API](https://fetch.spec.whatwg.org/)aracılığıyla Azure 'da [benzer arama API 'si Maps](/rest/api/maps/search/getsearchfuzzy) için bir arama isteği yapmanın bir yoludur. Her iki yol da aşağıda ele alınmıştır.

## <a name="make-a-search-request-via-service-module"></a>Hizmet modülü aracılığıyla bir arama isteği oluşturma

<iframe height='500' scrolling='no' title='Arama sonuçlarını bir haritada göster (hizmet modülü)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bkz. kalemin Azure Maps () ile birlikte <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>arama sonuçlarını (hizmet modülü)</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>Codepen</a>ile gösterme.
</iframe>

Yukarıdaki kodda, ilk blok bir harita nesnesi oluşturur ve kimlik doğrulama mekanizmasını erişim belirtecini kullanacak şekilde ayarlar. Yönergeler için [bir harita oluşturma](./map-create.md) ' ya bakabilirsiniz.

İkinci kod bloğu, `TokenCredential` erişim belirteciyle Azure Maps 'A http isteklerinin kimliğini doğrulamak için bir oluşturur. Ardından `TokenCredential` `atlas.service.MapsURL.newPipeline()` ' a geçirir ve bir işlem [hattı](/javascript/api/azure-maps-rest/atlas.service.pipeline) örneği oluşturur. , `searchURL` Azure Maps [arama](/rest/api/maps/search) işlemlerine yönelik bir URL 'yi temsil eder.

Üçüncü kod bloğu, [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) sınıfını kullanarak bir veri kaynağı nesnesi oluşturur ve buna arama sonuçları ekler. Bir [sembol katmanı](/javascript/api/azure-maps-control/atlas.layer.symbollayer) , [veri kaynağında](/javascript/api/azure-maps-control/atlas.source.datasource) kaydırılan nokta tabanlı verileri haritada semboller olarak oluşturmak için metin veya simgeleri kullanır.  Daha sonra bir sembol katmanı oluşturulur. Veri kaynağı, daha sonra haritaya eklenen sembol katmanına eklenir.

Dördüncü kod bloğu, [hizmet modülündeki](how-to-use-services-module.md) [searchbulanık](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) yöntemi kullanır. Bu, ilgilendiğiniz noktayı aramak için [aramayı al benzer REST API 'si](/rest/api/maps/search/getsearchfuzzy) aracılığıyla ücretsiz form metin araması gerçekleştirmenize olanak tanır. Aranan bir API 'ye yönelik istekleri al, benzer girişlerin herhangi bir birleşimini işleyebilir. Yanıttan bir GeoJSON Özellik koleksiyonu daha sonra yöntemi kullanılarak ayıklanır `geojson.getFeatures()` ve veri kaynağına eklenir ve bu da otomatik olarak, sembol katmanı aracılığıyla haritada işlenen verilere neden olur.

Son kod bloğu haritanın [setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) özelliğini kullanarak haritanın kamera sınırlarını ayarlar.

Arama isteği, veri kaynağı, sembol katmanı ve kamera sınırları, haritanın [olay dinleyicisinin](/javascript/api/azure-maps-control/atlas.map#events) içindedir. Sonuçların haritanın tam olarak yüklenip yüklenmesiyle emin olmak istiyoruz.


## <a name="make-a-search-request-via-fetch-api"></a>Fetch API aracılığıyla bir arama isteği oluşturma

<iframe height='500' scrolling='no' title='Arama sonuçlarını haritada göster' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bodepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>harita üzerinde arama sonuçlarını gösterme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Yukarıdaki kodda, ilk kod bloğu bir harita nesnesi oluşturur. Kimlik doğrulama mekanizmasını erişim belirtecini kullanacak şekilde ayarlar. Yönergeler için [bir harita oluşturma](./map-create.md) ' ya bakabilirsiniz.

İkinci kod bloğu, bir arama isteği oluşturmak için bir URL oluşturur. Ayrıca, arama sonuçları için sınırları ve PIN 'leri depolamak üzere iki dizi oluşturur.

Üçüncü kod bloğu, [Fetch API](https://fetch.spec.whatwg.org/)'sini kullanır. [Fetch API](https://fetch.spec.whatwg.org/) 'Si, Azure Maps 'a bir istek oluşturmak Için, [benzer arama API](/rest/api/maps/search/getsearchfuzzy) 'sine, ilgilendiğiniz noktaları aramasını sağlamak için kullanılır. Benzer arama API 'SI, benzer girişlerin herhangi bir birleşimini işleyebilir. Ardından arama yanıtını işler ve ayrıştırır ve sonuç sabitleyicileri Searchpin dizisine ekler.

Dördüncü kod bloğu, [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource) sınıfını kullanarak bir veri kaynağı nesnesi oluşturur. Kodda, kaynak nesnesine arama sonuçları ekleyeceğiz. Bir [sembol katmanı](/javascript/api/azure-maps-control/atlas.layer.symbollayer) , [veri kaynağında](/javascript/api/azure-maps-control/atlas.source.datasource) kaydırılan nokta tabanlı verileri haritada semboller olarak oluşturmak için metin veya simgeleri kullanır. Daha sonra bir sembol katmanı oluşturulur. Veri kaynağı, daha sonra haritaya eklenen sembol katmanına eklenir.

Son kod bloğu bir [BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) nesnesi oluşturur. Sonuç dizisini kullanır ve sonra haritanın [Setcamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)' i kullanarak haritanın kamera sınırlarını ayarlar. Daha sonra sonuç PIN 'lerini işler.

Arama isteği, veri kaynağı, sembol katmanı ve kamera sınırları, eşlemenin tam olarak yüklendikten sonra sonuçların görüntülendiğinden emin olmak için haritanın [olay dinleyicisi](/javascript/api/azure-maps-control/atlas.map#events) içinde ayarlanır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Arama hizmetini kullanmaya yönelik en iyi uygulamalar](how-to-use-best-practices-for-search.md)

**Benzer arama** hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Maps benzer arama API 'SI](/rest/api/maps/search/getsearchfuzzy)

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](/javascript/api/azure-maps-control/atlas.map)

Tam kod örnekleri için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Bir koordinattan bilgi alma](map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [A'dan B'ye yönleri gösterme](map-route.md)