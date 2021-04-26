---
title: Haritada bir noktaya açılan pencere Ekle | Microsoft Azure haritaları
description: Azure haritalar 'da açılır pencere, açılan şablonlar ve açılan olaylar hakkında bilgi edinin. Bir harita üzerindeki noktaya açılan pencere ekleme ve açılır pencereleri yeniden kullanma ve özelleştirme bölümüne bakın.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 903574a8c8696f10d9903a685bab8d12b3e6395f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891045"
---
# <a name="add-a-popup-to-the-map"></a>Haritaya bir açılan pencere ekleyin

Bu makalede, haritada bir noktaya açılan pencerenin nasıl ekleneceği gösterilmektedir.

## <a name="understand-the-code"></a>Kodu anlama

Aşağıdaki kod, `name` `description` bir sembol katmanını kullanarak eşlemeye ve özelliklerine sahip bir nokta özelliği ekler. [Açılan sınıfın](/javascript/api/azure-maps-control/atlas.popup) bir örneği oluşturulur, ancak gösterilmez. Fare olayları, açılan menüyü açmak ve kapatmak için sembol katmanına eklenir. İşaret simgesi üzerine gelindiğinde, açılan menünün `position` özelliği işaret konumuyla güncellenir ve `content` seçenek,  `name` nokta özelliğinin ve özellıklerını kapsayan bir HTML ile güncelleştirilir `description` . Açılan pencere daha sonra kendi işlevini kullanarak haritada görüntülenir `open` .

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Azure haritalar 'ı kullanarak bir açılan pencere ekleme' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Azure haritaları kullanarak bir açılan pencere ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Birden çok noktayla açılan pencereyi yeniden kullanma

En iyi yaklaşım bir açılan pencere oluşturmak ve onu yeniden kullanmak için gereken durumlar vardır. Örneğin, çok sayıda noktanız olabilir ve tek seferde yalnızca bir açılan pencere göstermek isteyebilirsiniz. Açılan pencereyi yeniden kullanmaya göre, uygulama tarafından oluşturulan DOM öğelerinin sayısı büyük ölçüde azalır ve bu da daha iyi performans sağlayabilir. Aşağıdaki örnekte 3 puntoluk Özellikler oluşturulur. Bunlardan birine tıklarsanız, bu nokta özelliği için içerik ile bir açılan pencere görüntülenir.

<br/>

<iframe height='500' scrolling='no' title='Çoklu PIN ile açılan pencereyi yeniden kullanma' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>birden fazla PIN Ile açılan pencereyi yeniden kullanma</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="customizing-a-popup"></a>Açılan pencereyi özelleştirme

Varsayılan olarak, açılan pencerede beyaz bir arka plan, altta bir işaretçi ok ve sağ üst köşedeki bir Kapat düğmesi bulunur. Aşağıdaki örnek, açılan pencere seçeneğini kullanarak arka plan rengini siyah olarak değiştirir `fillColor` . Kapat düğmesi, `CloseButton` seçeneği false olarak ayarlanarak kaldırılır. Açılan menünün HTML içeriği, açılan pencerenin kenarlarından 10 piksel doldurulmuş kullanır. Metin beyaz yapılır, bu nedenle siyah arka planda düzgün şekilde görünür.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Özelleştirilmiş açılan pencere" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>özelleştirilmiş açılan pencere</a> başlığına bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Haritaya açılan şablonlar ekleme

Açılan şablonlar, açılan pencereler için veri odaklı düzenleri oluşturmayı kolaylaştırır. Aşağıdaki bölümlerde, özelliklerin özelliklerini kullanarak biçimlendirilen içerik oluşturmak için çeşitli açılan şablonların kullanımı gösterilmektedir.

> [!NOTE]
> Varsayılan olarak, oluşturulan tüm içerik açılan şablonu kullanır bir IFRAME içinde güvenlik özelliği olarak korumalı olur. Ancak, sınırlamalar vardır:
>
> - Tüm betikler, formlar, işaretçi kilidi ve üst gezinti işlevleri devre dışı bırakıldı. Bağlantıların tıklandığında yeni bir sekmede açık olmasına izin verilir. 
> - `srcdoc`İframe 'ler üzerinde parametresini desteklemeyen eski tarayıcılar, az miktarda içerik işlemeye sınırlandırılmıştır.
> 
> Açılana yüklenen verilere güveniyorsanız ve bu betiklerin açılan pencerede yüklenmesine izin vermek istiyorsanız, açılan şablonlar seçeneğini yanlış olarak ayarlayarak bunu devre dışı bırakabilirsiniz `sandboxContent` . 

### <a name="string-template"></a>Dize şablonu

Dize şablonu, yer tutucuları Özellik özelliklerinin değerleriyle değiştirir. Özelliğin özelliklerine dize türünde bir değer atanması gerekmez. Örneğin, `value1` bir tamsayı tutar. Bu değerler daha sonra öğesinin içerik özelliğine geçirilir `popupTemplate` . 

`numberFormat`Seçeneği, görüntülenecek sayının biçimini belirtir. `numberFormat`Belirtilmemişse, kod açılan şablonlar tarih biçimini kullanır. `numberFormat`Seçeneği [Number. toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) işlevini kullanarak sayıları biçimlendirir. Büyük sayıları biçimlendirmek için `numberFormat` [NumberFormat. Format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)' dan işlevlerle seçeneğini kullanmayı düşünün. Örneğin, aşağıdaki kod parçacığı, `maximumFractionDigits` kesir basamak sayısını ikiye sınırlamak için kullanır.

> [!Note]
> Dize şablonunun görüntüleri işleyebileceği tek bir yol vardır. İlk olarak, dize şablonunun içinde bir resim etiketi olması gerekir. Resim etiketine geçirilen değer bir görüntünün URL 'SI olmalıdır. Ardından, dize şablonunun `isImage` içinde true olarak ayarlanması gerekir `HyperLinkFormatOptions` . `isImage`Seçeneği köprünün bir görüntü için olduğunu ve köprünün bir resim etiketine yükleneceğini belirtir. Köprüye tıklandığında görüntü açılır.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>PropertyInfo şablonu

PropertyInfo şablonu, özelliğin kullanılabilir özelliklerini görüntüler. `label`Seçeneği, kullanıcıya görüntülenecek metni belirtir. `label`Belirtilmezse köprü görüntülenir. Köprü bir görüntü ise, "alt" etiketine atanan değer görüntülenir. `dateFormat`Tarih biçimini belirtir ve tarih biçimi belirtilmemişse, Tarih bir dize olarak işlenir. `hyperlinkFormat`Seçeneği, tıklatılabilir bağlantıları işler, benzer şekilde, `email` tıklatılabilir e-posta adreslerini işlemek için kullanılabilir.

PropertyInfo şablonu son kullanıcıya özellikleri görüntülemeden önce, özelliklerin gerçekten bu özellik için tanımlandığını denetler. Stil ve başlık özelliklerini görüntülemeyi de yoksayar. Örneğin,,,, `color` ve görüntülemez `size` `anchor` `strokeOpacity` `visibility` . Bu nedenle, arka uçta özellik yolu denetimi tamamlandıktan sonra PropertyInfo şablonu içeriği tablo biçiminde gösterir.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Birden çok içerik şablonu

Bir özellik ayrıca dize şablonu ve PropertyInfo şablonu birleşimini kullanarak içeriği görüntüleyebilir. Bu durumda, dize şablonu beyaz bir arka planda yer tutucular değerlerini işler.  Ve, PropertyInfo şablonu bir tablonun içinde tam genişlikli bir görüntü oluşturur. Bu örnekteki özellikler, önceki örneklerde açıklandığımız özelliklerle benzerdir.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Tanımlı şablon olmayan noktaları

Açılan şablon bir dize şablonu, bir PropertyInfo şablonu veya her ikisinin birleşimi olarak tanımlanmadığında varsayılan ayarları kullanır. `title`Ve `description` yalnızca atanan özellikler olduğunda, açılan şablonda sağ üst köşedeki bir kapatma düğmesi olan beyaz bir arka plan görüntülenir. Küçük ve orta ekranlarda, alt kısımdaki bir oku gösterir. Varsayılan ayarlar, ve dışındaki tüm özellikler için bir tablonun içinde gösterilir `title` `description` . Varsayılan ayarlara geri dönerek bile, açılan şablon yine de programlı bir şekilde yönetilebilir. Örneğin, kullanıcılar köprü algılamayı kapatabilir ve varsayılan ayarlar hala diğer özellikler için geçerlidir.

CodePen 'da haritadaki noktalara tıklayın. Şu açılan şablonların her biri için haritada bir nokta var: dize şablonu, PropertyInfo şablonu ve birden çok içerik şablonu. Şablonların varsayılan ayarları kullanarak nasıl işleneceğini göstermek için de üç işaret vardır.

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>Popuptemplates</a> 'e bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="reuse-popup-template"></a>Açılır şablonu yeniden kullan

Açılan pencereyi yeniden kullanmaya benzer şekilde, açılan şablonları kullanabilirsiniz. Bu yaklaşım, birden çok noktaya ait tek seferde yalnızca bir açılan şablon göstermek istediğinizde faydalıdır. Açılan şablonu yeniden kullanmak, uygulama tarafından oluşturulan DOM öğelerinin sayısı azaltılır ve bu da uygulama performansınızı geliştirir. Aşağıdaki örnek, üç noktaya ait aynı açılan şablonu kullanır. Bunlardan birine tıklarsanız, bu nokta özelliği için içerik ile bir açılan pencere görüntülenir.

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından bulunan kalemin <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>Reusepopuptemplate</a> bölümüne bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="popup-events"></a>Açılan olaylar

Açılanlar açılabilir, kapatılabilir ve sürüklenip kapatılabilir. Açılan sınıf, geliştiricilerin bu olaylara tepki vermesini sağlamak için olaylar sağlar. Aşağıdaki örnek, Kullanıcı açılan pencereyi açtığında, kapattığında veya sürüklediğinde hangi olayların tetiklediği vurgulanmıştır. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Açılan olaylar" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps () ile kalem <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>açılan olayları</a> ' na bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Açılan Pencere](/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](/javascript/api/azure-maps-control/atlas.popuptemplate)

Tam kod örnekleri için aşağıdaki harika makalelere göz atın:

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](./map-add-pin.md)

> [!div class="nextstepaction"]
> [HTML işaretleyicisi ekleme](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı ekleme](map-add-shape.md)