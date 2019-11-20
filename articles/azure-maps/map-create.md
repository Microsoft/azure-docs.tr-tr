---
title: Azure haritalar ile harita oluşturma | Microsoft Docs
description: Azure Maps web SDK ile harita oluşturma.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 932c244ff41e757413a05cde019ee7ee1a82232d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976114"
---
# <a name="create-a-map"></a>Harita oluşturma

Bu makalede harita oluşturma ve Haritayı canlandırma yolları gösterilmektedir.  

## <a name="loading-a-map"></a>Harita yükleme

Eşleme yüklemek için, [Map sınıfının](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)yeni bir örneğini oluşturun. Harita başlatılırken, Haritayı işlemek için bir DIV öğe KIMLIĞI ve eşleme yüklenirken kullanılacak bir seçenek kümesi geçirilir. `atlas` Ad alanında varsayılan kimlik doğrulama bilgileri belirtilmemişse, eşleme yüklenirken bu bilgilerin eşleme seçeneklerinde belirtilmesi gerekir. Eşleme, performans için birkaç kaynağı zaman uyumsuz olarak yükler. Bu nedenle, eşleme örneğini oluşturduktan sonra haritaya bir `ready` veya `load` olayı ekleyin ve ardından bu olay işleyicisindeki eşlemele etkileşim kuran ek kodu ekleyin. Bu `ready` olay, haritada programlı olarak bir şekilde etkileşim altına almak için yeterli miktarda kaynak yüklendiği anda ateşlenir. İlk harita görünümü tamamen yüklemeyi tamamladıktan sonra olayateşlenir.`load` 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Temel harita yüklemesi" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>temel haritasını</a> inceleyin.
</iframe>

> [!TIP]
> Aynı sayfada birden çok eşleme yüklenebilir ve her biri aynı veya farklı kimlik doğrulama ve dil ayarlarını kullanabilir.

## <a name="show-a-single-copy-of-the-world"></a>Dünyanın tek bir kopyasını göster

Harita geniş bir ekranda yakınlaştırıldığında dünyanın birden fazla kopyası yatay olarak görünür. Bu çoğu senaryo için idealdir, ancak bazı uygulamalarda yalnızca dünyanın tek bir kopyasını görmek istenebilir. Bu, haritalar `renderWorldCopies` seçeneği olarak `false`ayarlanarak yapılabilir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Renderworldkopyaları = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) için bkz. <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderworldkopyaların = false</a> .
</iframe>

## <a name="controlling-the-map-camera"></a>Harita kamerasını denetleme

Kamerayı kullanarak haritanın görüntülenen alanını ayarlayabileceğiniz iki yol vardır. Haritayı yüklerken merkez ve yakınlaştırma gibi kamera seçeneklerini ayarlayabilir veya eşleme görünümünü programlı bir şekilde güncelleştirmek için Map yüklendikten sonra `setCamera` istediğiniz zaman çağrı yapabilirsiniz.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Kamerayı ayarlama

Aşağıdaki kodda bir [harita nesnesi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) oluşturulur ve Ortala ve Yakınlaştır seçenekleri ayarlanır. Merkez ve yakınlaştırma düzeyi gibi harita özellikleri [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)'in bir parçasıdır.

<br/>

<iframe height='500' scrolling='no' title='CameraOptions aracılığıyla harita oluşturma' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure konum tabanlı hizmetler (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>bir harita `CameraOptions` oluşturma </a>kalemine bakın.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Kamera sınırlarını ayarlama

Aşağıdaki kodda, ile `new atlas.Map()`bir [Map nesnesi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) oluşturulur. Gibi eşleme özellikleri `CameraBoundsOptions` , Map sınıfının [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) işlevi aracılığıyla tanımlanabilir. Sınırlar ve doldurma özellikleri kullanılarak `setCamera`ayarlanır.

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions aracılığıyla harita oluşturma' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>bir harita `CameraBoundsOptions` oluşturma </a>kalemine bakın.
</iframe>

### <a name="animate-map-view"></a>Harita görünümüne animasyon ekleme

Aşağıdaki kodda, ilk kod bloğu bir harita oluşturur ve harita stilini, merkezi ve yakınlaştırma değerlerini ayarlar. İkinci kod bloğunda, animasyon düğmesi için bir tıklama olayı işleyicisi oluşturulur. Bu düğmeye tıklandığında, setCamera işlevi [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [animationoptions](/javascript/api/azure-maps-control/atlas.animationoptions)için bazı rastgele değerlerle çağırılır.

<br/>

<iframe height='500' scrolling='no' title='Harita görünümüne animasyon ekleme' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>Codepen</a>'da Azure haritalar (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile ilgili <a href='https://codepen.io/azuremaps/pen/WayvbO/'>harita görünümü</a> kalemine bakın.
</iframe>

## <a name="try-out-the-code"></a>Kodu deneyin

Yukarıdaki örnek koda göz atın. Sol taraftaki **js sekmesinde** JavaScript kodunu düzenleyebilir ve sağ taraftaki **sonuç sekmesinde** harita görünümü değişiklikleri ' ne bakabilirsiniz. Ayrıca, **birlikte Düzenle** düğmesine tıklayıp kodu CodePen ' de düzenleyebilirsiniz.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Uygulamanıza işlevsellik eklemek için bkz. kod örnekleri:

> [!div class="nextstepaction"]
> [Haritanın stilini değiştir](choose-map-style.md)

> [!div class="nextstepaction"]
> [Haritaya denetim ekleme](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](https://docs.microsoft.com/samples/browse/?products=azure-maps)