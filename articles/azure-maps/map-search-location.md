---
title: Azure haritalar ile arama sonuçlarını gösterme | Microsoft Docs
description: Azure haritalar ile bir arama isteği gerçekleştirme ve sonuçları Azure Maps web SDK 'sında görüntüleme.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7c4c78e1d21754d42391a3762e9f7ed199a7376b
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975973"
---
# <a name="show-search-results-on-the-map"></a>Haritada arama sonuçlarını göster

Bu makalede, ilgilendiğiniz konumun nasıl aranacağı ve arama sonuçlarının haritada gösterilmesi gösterilmektedir.

İlgi çekici bir konum aramak için iki yol vardır. Bir yol, bir arama isteği oluşturmak için hizmet modülünü kullanmaktır. Diğer bir yöntem de, [Fetch API](https://fetch.spec.whatwg.org/)aracılığıyla Azure 'da [benzer arama API 'si Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) için bir arama isteği yapmanın bir yoludur. Her iki yol da aşağıda ele alınmıştır.

## <a name="make-a-search-request-via-service-module"></a>Hizmet modülü aracılığıyla bir arama isteği oluşturma

<iframe height='500' scrolling='no' title='Arama sonuçlarını bir haritada göster (hizmet modülü)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bkz. kalemin Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile birlikte <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>arama sonuçlarını (hizmet modülü)</a> <a href='https://codepen.io'>codepen</a>ile gösterme.
</iframe>

Yukarıdaki kodda, ilk kod bloğu bir harita nesnesi oluşturur ve kimlik doğrulama mekanizmasını erişim belirtecini kullanacak şekilde ayarlar. Yönergeler için [bir harita oluşturma](./map-create.md) ' ya bakabilirsiniz.

İkinci kod bloğu, erişim belirteciyle Azure `TokenCredential` Maps 'a http isteklerinin kimliğini doğrulamak için bir oluşturur. Ardından `atlas.service.MapsURL.newPipeline()` ' a geçirir `TokenCredential` ve bir işlem [hattı](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) örneği oluşturur. , `searchURL` Azure Maps [arama](https://docs.microsoft.com/rest/api/maps/search) işlemlerine yönelik bir URL 'yi temsil eder.

Üçüncü kod bloğu, [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sınıfını kullanarak bir veri kaynağı nesnesi oluşturur ve buna arama sonuçları ekler. Bir [sembol katmanı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) , [veri kaynağında](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) kaydırılan nokta tabanlı verileri haritada semboller olarak oluşturmak için metin veya simgeleri kullanır.  Daha sonra bir sembol katmanı oluşturulur ve veri kaynağı sembol katmanına eklenir ve bu daha sonra haritaya eklenir.

Dördüncü kod bloğu, [hizmet modülündeki](how-to-use-services-module.md) [searchbulanık](/javascript/api/azure-maps-rest/atlas.service.models.searchgetsearchfuzzyoptionalparams) yöntemi kullanır. Bu, ilgilendiğiniz noktayı aramak için [aramayı al benzer REST API 'si](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) aracılığıyla ücretsiz form metin araması gerçekleştirmenize olanak tanır. Arama belirsiz API 'leri, benzer girişlerin herhangi bir birleşimini işleyebilir. Yanıttan bir geojson Özellik koleksiyonu daha sonra `geojson.getFeatures()` yöntemi kullanılarak ayıklanır ve veri kaynağına eklenir ve bu da otomatik olarak, sembol katmanı aracılığıyla haritada işlenen verilere neden olur.

Son kod bloğu haritanın [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) özelliğini kullanarak haritanın kamera sınırlarını ayarlar.

Arama isteği, veri kaynağı ve sembol katmanı ve kamera sınırları oluşturulur ve haritanın tam olarak yüklendikten sonra sonuçların görüntülenmesini sağlamak için haritanın Ready [olay dinleyicisi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) içinde ayarlanır.


## <a name="make-a-search-request-via-fetch-api"></a>Fetch API aracılığıyla bir arama isteği oluşturma

<iframe height='500' scrolling='no' title='Arama sonuçlarını haritada göster' src='//codepen.io/azuremaps/embed/KQbaeM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Bodepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>harita üzerinde arama sonuçlarını gösterme</a> kalemine bakın.
</iframe>

Yukarıdaki kodda, ilk kod bloğu bir harita nesnesi oluşturur ve kimlik doğrulama mekanizmasını erişim belirtecini kullanacak şekilde ayarlar. Yönergeler için [bir harita oluşturma](./map-create.md) ' ya bakabilirsiniz.

İkinci kod bloğu, bir arama isteği oluşturmak için bir URL oluşturur. Ayrıca, arama sonuçları için sınırları ve PIN 'leri depolamak üzere iki dizi oluşturur.

Üçüncü kod bloğu, [Fetch API](https://fetch.spec.whatwg.org/) 'Sini kullanarak Azure Maps 'e bir istek oluşturmak Için, [benzer arama API 'si](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) ile ilgi çekici noktaları arar. Benzer arama API 'SI, benzer girişlerin herhangi bir birleşimini işleyebilir. Ardından arama yanıtını işler ve ayrıştırır ve sonuç sabitleyicileri Searchpin dizisine ekler.

Dördüncü kod bloğu, [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) sınıfını kullanarak bir veri kaynağı nesnesi oluşturur ve buna arama sonuçları ekler. Bir [sembol katmanı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) , [veri kaynağında](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) kaydırılan nokta tabanlı verileri haritada semboller olarak oluşturmak için metin veya simgeleri kullanır. Daha sonra bir sembol katmanı oluşturulur ve veri kaynağı sembol katmanına eklenir ve bu daha sonra haritaya eklenir.

Son kod bloğu, sonuçların dizisini kullanarak bir [sıçrama Dingbox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) nesnesi oluşturur ve haritanın [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-)öğesini kullanarak haritanın kamera sınırlarını ayarlar. Daha sonra sonuç PIN 'lerini işler.

Arama isteği, veri kaynağı ve sembol düzeyi ve kamera sınırları, eşlemenin tam olarak yüklendikten sonra sonuçların görüntülendiğinden emin olmak için haritanın [olay dinleyicisi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) içinde ayarlanır.

## <a name="next-steps"></a>Sonraki adımlar

**Benzer arama**hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Azure Maps benzer arama API 'SI](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Tam kod örnekleri için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Bir koordinatdan bilgi al](./map-get-information-from-coordinate.md)
<!-- Comment added to suppress false positive warning -->
> [!div class="nextstepaction"]
> [A 'dan B 'ye yönlendirmeler göster](./map-route.md)
