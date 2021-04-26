---
title: Azure haritalar ile harita oluşturma | Microsoft Azure haritaları
description: Azure Haritalar Web SDK 'sını kullanarak Web sayfalarına haritalar ekleme hakkında bilgi edinin. Animasyon, stil, kamera, hizmetler ve kullanıcı etkileşimleri için seçenekler hakkında bilgi edinin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 833b6413cc5dfde1129075a286e5fe93a06e159f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890930"
---
# <a name="create-a-map"></a>Harita oluşturma

Bu makalede harita oluşturma ve Haritayı canlandırma yolları gösterilmektedir.  

## <a name="loading-a-map"></a>Harita yükleme

Eşleme yüklemek için, [Map sınıfının](/javascript/api/azure-maps-control/atlas.map)yeni bir örneğini oluşturun. Haritayı başlatırken, Haritayı işlemek için bir DIV öğe KIMLIĞI geçirin ve eşleme yüklenirken kullanılacak bir seçenek kümesi geçirin. Ad alanında varsayılan kimlik doğrulama bilgileri belirtilmemişse `atlas` , eşleme yüklenirken bu bilgilerin eşleme seçeneklerinde belirtilmesi gerekir. Eşleme, performans için birkaç kaynağı zaman uyumsuz olarak yükler. Bu nedenle, eşleme örneğini oluşturduktan sonra `ready` haritaya bir veya olayı ekleyin `load` ve ardından eşleme ile etkileşimde bulunan ek kodu olay işleyicisine ekleyin. Bu `ready` olay, haritada programlı olarak bir şekilde etkileşim altına almak için yeterli miktarda kaynak yüklendiği anda ateşlenir. `load`İlk harita görünümü tamamen yüklemeyi tamamladıktan sonra olay ateşlenir. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Temel harita yüklemesi" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>temel haritasını</a> inceleyin <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

> [!TIP]
> Aynı sayfada birden çok eşleme yükleyebilirsiniz. Aynı sayfada birden çok eşleme aynı veya farklı kimlik doğrulama ve dil ayarlarını kullanabilir.

## <a name="show-a-single-copy-of-the-world"></a>Dünyanın tek bir kopyasını göster

Harita geniş bir ekranda yakınlaştırıldığında dünyanın birden fazla kopyası yatay olarak görünür. Bu seçenek bazı senaryolar için idealdir, ancak diğer uygulamalar için dünyanın tek bir kopyasını görmeniz istenebilir. Bu davranış, haritalar seçeneği olarak ayarlanarak uygulanır `renderWorldCopies` `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Renderworldkopyaları = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () için bkz. <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>Renderworldkopyaların = false</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>


## <a name="map-options"></a>Eşleme seçenekleri

Bir harita oluştururken, eşlemenin aşağıda listelenen şekilde nasıl çalıştığını özelleştirmek için geçirilebilecek birkaç farklı seçenek türü vardır.

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) ve [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) , haritanın görüntülemesi gereken alanı belirtmek için kullanılır.
- [Serviceoptions](/javascript/api/azure-maps-control/atlas.serviceoptions) , eşlemenin Haritayı destekleyen hizmetlerle nasıl etkileşime gireceğini belirtmek için kullanılır.
- Stil [seçenekleri](/javascript/api/azure-maps-control/atlas.styleoptions) , haritanın biçimlendirilmiş ve işlenmiş olması gerektiğini belirtmek için kullanılır.
- [Userınteractionoptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) , kullanıcının haritada etkileşim kurarken eşlemenin nasıl ulaşması gerektiğini belirtmek için kullanılır. 

Bu seçenekler, eşleme yüklendikten sonra,,, ve işlevleri kullanılarak da güncelleştirilir `setCamera` `setServiceOptions` `setStyle` `setUserInteraction` . 

## <a name="controlling-the-map-camera"></a>Harita kamerasını denetleme

Harita kamerasını kullanarak haritanın görüntülenen alanını ayarlamak için iki yol vardır. Haritayı yüklerken kamera seçeneklerini ayarlayabilirsiniz. Veya, eşleme `setCamera` görünümünü program aracılığıyla güncelleştirmek için eşleme yüklendikten sonra istediğiniz zaman çağırabilirsiniz.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Kamerayı ayarlama

Harita Kamerası, harita tuvalinin görünüm penceresinde neyin görüntülendiğini denetler. Kamera seçenekleri, başlatılan veya haritalar işlevine geçirildiğinde harita seçeneklerine geçirilebilir `setCamera` .

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

Aşağıdaki kodda bir [harita nesnesi](/javascript/api/azure-maps-control/atlas.map) oluşturulur ve Ortala ve Yakınlaştır seçenekleri ayarlanır. Orta ve yakınlaştırma düzeyi gibi harita özellikleri [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)'in bir parçasıdır.

<br/>

<iframe height='500' scrolling='no' title='CameraOptions aracılığıyla harita oluşturma' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure konum tabanlı hizmetler () ile <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>bir harita `CameraOptions` oluşturma </a>kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Kamera sınırlarını ayarlama

Harita kamerasını güncelleştirmek için bir sınırlayıcı kutu kullanılabilir. Sınırlama kutusu, noktadan itibaren hesaplanmışsa, simge boyutunu hesaba eklemek için kamera seçeneklerinde bir piksel doldurma değeri de belirtmeniz genellikle yararlı olur. Bu, noktaların harita Görünüm penceresinin kenarına düşmemesini sağlamaya yardımcı olur.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

Aşağıdaki kodda, ile bir [Map nesnesi](/javascript/api/azure-maps-control/atlas.map) oluşturulur `new atlas.Map()` . Gibi eşleme özellikleri `CameraBoundsOptions` , Map sınıfının [setcamera](/javascript/api/azure-maps-control/atlas.map) işlevi aracılığıyla tanımlanabilir. Sınırlar ve doldurma özellikleri kullanılarak ayarlanır `setCamera` .

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions aracılığıyla harita oluşturma' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>bir harita `CameraBoundsOptions` oluşturma </a>kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

### <a name="animate-map-view"></a>Harita görünümüne animasyon ekleme

Haritanın kamera seçeneklerini ayarlarken [animasyon seçenekleri](/javascript/api/azure-maps-control/atlas.animationoptions) de ayarlanabilir. Bu seçenekler, kameranın taşınması için gereken animasyon türünü ve süreyi belirtir.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

Aşağıdaki kodda, ilk kod bloğu bir harita oluşturur ve ENTER ve zoom harita stillerini ayarlar. İkinci kod bloğunda, animasyon düğmesi için bir tıklama olayı işleyicisi oluşturulur. Bu düğme tıklandığında, `setCamera` Işlev [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) ve [animationoptions](/javascript/api/azure-maps-control/atlas.animationoptions)için bazı rastgele değerlerle çağırılır.

<br/>

<iframe height='500' scrolling='no' title='Harita görünümüne animasyon ekleme' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure haritalar () ile ilgili <a href='https://codepen.io/azuremaps/pen/WayvbO/'>harita görünümü</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="request-transforms"></a>İstek dönüşümleri

Bazen harita denetimi tarafından yapılan HTTP isteklerini değiştirmek faydalı olabilir. Örnek:

- Kutucuk isteklerine ek üstbilgiler ekleyin. Bu, genellikle parola korumalı hizmetler için yapılır.
- Proxy hizmeti üzerinden istekleri çalıştırmak için URL 'Leri değiştirin.

Eşlemenin [hizmet seçenekleri](/javascript/api/azure-maps-control/atlas.serviceoptions) , `transformRequest` eşleme tarafından yapılan tüm istekleri yapılmadan önce değiştirmek için kullanılabilecek bir öğesine sahip. `transformRequest`Bu seçenek, iki parametre alan bir işlevdir; bir dize URL 'si ve isteğin ne için kullanıldığını gösteren bir kaynak türü dizesi. Bu işlev [requestParameters](/javascript/api/azure-maps-control/atlas.requestparameters) sonucunu döndürmelidir.

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

Aşağıdaki örnek, `https://example.com` isteğe üstbilgi olarak bir Kullanıcı adı ve parola ekleyerek tüm istekleri boyuta göre değiştirmek için nasıl kullanılacağını gösterir.

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>Kodu deneyin

Kod örneklerine bakın. **Js sekmesinin** içindeki JavaScript kodunu düzenleyebilir ve **sonuç sekmesinde** harita görünümü değişikliklerini görebilirsiniz. Ayrıca, sağ üst köşede yer **alan CodePen 'Da Düzenle**' yi tıklatabilir ve kodu CodePen ' da değiştirebilirsiniz.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Uygulamanıza işlevsellik eklemek için bkz. kod örnekleri:

> [!div class="nextstepaction"]
> [Haritanın stilini değiştirme](choose-map-style.md)

> [!div class="nextstepaction"]
> [Haritaya denetim ekleme](map-add-controls.md)

> [!div class="nextstepaction"]
> [Kod örnekleri](/samples/browse/?products=azure-maps)