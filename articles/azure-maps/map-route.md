---
title: Azure haritalar ile yönleri göster | Microsoft Docs
description: Azure Maps web SDK ile Haritada iki konum arasındaki yönleri görüntüleme.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: cf997d4ae120f3e9309892b112f9954bde97bc76
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976484"
---
# <a name="show-directions-from-a-to-b"></a>A'dan B'ye yönleri gösterme

Bu makalede yol isteği oluşturma ve Haritayı haritada gösterme gösterilmektedir.

Bunu iki şekilde yapabilirsiniz. İlk yöntem, [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) 'sini bir hizmet modülü aracılığıyla sorgulamanızı sağlar. İkinci yol, [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)'sine bir arama isteği yapmak IÇIN [getirme API](https://fetch.spec.whatwg.org/) 'sini kullanmaktır. Her iki yol da aşağıda ele alınmıştır.

## <a name="query-the-route-via-service-module"></a>Yönlendirme hizmetini hizmet modülü aracılığıyla sorgulama

<iframe height='500' scrolling='no' title='Bir haritadaki A 'dan B 'ye yönlendirmeler göster (hizmet modülü)' src='//codepen.io/azuremaps/embed/RBZbep/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/RBZbep/'>haritada (hizmet modülü) bir haritadaki a 'dan B 'ye yönlendirmeler göster</a> bölümüne bakın.
</iframe>

Yukarıdaki kodda, ilk kod bloğu bir harita nesnesi oluşturur ve kimlik doğrulama mekanizmasını erişim belirtecini kullanacak şekilde ayarlar. Yönergeler için [bir harita oluşturma](./map-create.md) ' ya bakabilirsiniz.

İkinci kod bloğu, erişim belirteciyle Azure `TokenCredential` Maps 'a http isteklerinin kimliğini doğrulamak için bir oluşturur. Ardından `atlas.service.MapsURL.newPipeline()` ' a geçirir `TokenCredential` ve bir işlem [hattı](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) örneği oluşturur. , `routeURL` Azure Maps [yönlendirme](https://docs.microsoft.com/rest/api/maps/route) işlemlerine yönelik bir URL 'yi temsil eder.

Üçüncü kod bloğu, haritaya bir [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) nesnesi oluşturur ve ekler.

Dördüncü kod bloğu başlangıç ve bitiş [noktaları](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) nesneleri oluşturur ve bunları DataSource nesnesine ekler.

Çizgi, LineString [özelliğidir](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) . Bir [Linelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) , [veri kaynağında](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) kaydırılan satır nesnelerini haritada satırlar olarak işler. Dördüncü kod bloğu oluşturur ve haritaya bir çizgi katmanı ekler. [Linestringlayeroptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)içindeki bir çizgi katmanının özelliklerine bakın.

Bir [sembol katmanı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) , [veri kaynağında](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) kaydırılan nokta tabanlı verileri haritada semboller olarak oluşturmak için metin veya simgeleri kullanır. Beşinci kod bloğu oluşturur ve haritaya bir sembol katmanı ekler.

Altıncı kod bloğu, [hizmet modülünün](how-to-use-services-module.md)bir parçası olan Azure Maps yönlendirme hizmeti 'ni sorgular. RouteURL 'nin [Hesaplayısınuyönleri](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest#methods) yöntemi başlangıç ve bitiş noktaları arasında bir yol almak için kullanılır. Yanıttan bir geojson Özellik koleksiyonu daha sonra `geojson.getFeatures()` yöntemi kullanılarak ayıklanır ve veri kaynağına eklenir. Ardından yanıtı haritada bir yol olarak işler. Haritaya satır ekleme hakkında daha fazla bilgi için bkz. [haritaya satır ekleme](map-add-line-layer.md).

Son kod bloğu haritanın [Setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) özelliğini kullanarak haritanın sınırlarını ayarlar.

Yol sorgusu, veri kaynağı, simge ve çizgi katmanları ve kamera sınırları, eşlemenin tam olarak yüklendikten sonra sonuçların görüntülenmesini sağlamak için haritanın [olay dinleyicisi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) içinde oluşturulur ve ayarlanır.

## <a name="query-the-route-via-fetch-api"></a>Fetch API aracılığıyla rotayı sorgulama

<iframe height='500' scrolling='no' title='A noktasından B noktasına yol tarifini haritada göster' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Kalem bkz <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Göster yönergeleri A'dan B'ye bir haritada</a> Azure haritalar tarafından (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) üzerinde <a href='https://codepen.io'>CodePen</a>.
</iframe>

Yukarıdaki kodda, ilk kod bloğu bir harita nesnesi oluşturur ve kimlik doğrulama mekanizmasını erişim belirtecini kullanacak şekilde ayarlar. Yönergeler için [bir harita oluşturma](./map-create.md) ' ya bakabilirsiniz.

İkinci kod bloğu oluşturur ve haritaya bir [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) nesnesi ekler.

Üçüncü kod bloğu, yol için başlangıç ve hedef noktalarını oluşturur ve bunları veri kaynağına ekler. [Addpın](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)'leri kullanma hakkında yönergeler için [haritada PIN ekleme '](map-add-pin.md) ye bakabilirsiniz.

Bir [Linelayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest) , [veri kaynağında](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) kaydırılan satır nesnelerini haritada satırlar olarak işler. Dördüncü kod bloğu oluşturur ve haritaya bir çizgi katmanı ekler. [Linelayeroptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)'da bir çizgi katmanının özelliklerine bakın.

Bir [sembol katmanı](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) , [veri kaynağında](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) kaydırılan nokta tabanlı verileri haritada semboller olarak oluşturmak için metin veya simgeleri kullanır. Beşinci kod bloğu oluşturur ve haritaya bir sembol katmanı ekler. Bkz. [Symbollayeroptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)'da bir sembol katmanının özelliklerine bakın.

Sonraki kod bloğu, Başlangıç `SouthWest` ve `NorthEast` hedef noktalarından oluşur ve bu noktaları oluşturur ve haritanın [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) özelliğini kullanarak haritanın sınırlarını ayarlar.

Son kod bloğu, [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)'sine bir arama isteği yapmak IÇIN [getirme API](https://fetch.spec.whatwg.org/) 'sini kullanır. Yanıt daha sonra ayrıştırılır. Yanıt başarılı olduysa, enlem ve boylam bilgileri, bu noktaları bağlayarak bir satırı dizi oluşturmak için kullanılır. Daha sonra, satır verileri haritada yolu işlemek için veri kaynağına eklenir. Yönergeler için [haritada satır ekleme '](map-add-line-layer.md) ye bakabilirsiniz.

Yol sorgusu, veri kaynağı, simge ve çizgi katmanları ve kamera sınırları, eşlemenin tam olarak yüklendikten sonra sonuçların görüntülenmesini sağlamak için haritanın [olay dinleyicisi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) içinde oluşturulur ve ayarlanır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Tam kod örnekleri için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Haritada trafiği göster](./map-show-traffic.md)

> [!div class="nextstepaction"]
> [Harita-fare olaylarıyla etkileşim kurma](./map-events.md)
