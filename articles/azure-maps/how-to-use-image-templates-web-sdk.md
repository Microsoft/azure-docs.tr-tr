---
title: Azure Haritalar Web SDK 'sindeki görüntü şablonları | Microsoft Azure haritaları
description: Azure Haritalar Web SDK 'sını kullanarak haritalara görüntü simgeleri ve kalıp dolgulu çokgenler ekleme hakkında bilgi edinin. Kullanılabilir görüntü ve Fill model şablonlarını görüntüleyin.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895690"
---
# <a name="how-to-use-image-templates"></a>Görüntü şablonlarını kullanma

Görüntüler, HTML işaretçileri ve Azure Maps web SDK içindeki çeşitli katmanlarla kullanılabilir:

 - Sembol katmanları, haritada bir görüntü simgesiyle noktaları işleyebilir. Semboller ayrıca bir çizgiler yolu üzerinde de oluşturulabilir.
 - Çokgen katmanları, bir doldur örüntüsünün görüntüsü ile oluşturulabilir. 
 - HTML işaretçileri, görüntüleri ve diğer HTML öğelerini kullanarak noktaları işleyebilir.

Katmanlarla iyi bir performans sağlamak için görüntüleri, işlemeden önce harita görüntüsü Sprite kaynağına yükleyin. SymbolLayer [Iconoptions](/javascript/api/azure-maps-control/atlas.iconoptions), varsayılan olarak harita görüntüsü Sprite öğesine, el ile birkaç işaret görüntüsünü önceden yükler. Bu işaret görüntüleri ve daha fazlası SVG şablonları olarak kullanılabilir. Özel ölçeklendirmeleri olan veya müşteri birincil ve ikincil renk olarak kullanılan görüntüler oluşturmak için kullanılabilir. Toplamda 42 görüntü şablonu sağlanır: 27 sembol simgeleri ve 15 Çokgen dolgusu desenleri.

Görüntü şablonları, işlevi kullanılarak harita görüntüsü Sprite kaynaklarına eklenebilir `map.imageSprite.createFromTemplate` . Bu işlev, en fazla beş parametrenin geçirilmesine izin verir;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

, `id` Oluşturduğunuz benzersiz bir tanımlayıcıdır. , `id` Haritalar görüntü Sprite öğesine eklendiğinde görüntüye atanır. Hangi görüntü kaynağının işleneceğini belirtmek için katmanlarda bu tanımlayıcıyı kullanın. `templateName`Kullanılacak görüntü şablonunu belirtir. `color`Seçeneği görüntünün birincil rengini ayarlar ve `secondaryColor` Seçenekler görüntünün ikincil rengini ayarlar. `scale`Seçeneği resim Sprite öğesine uygulamadan önce görüntü şablonunu ölçeklendirir. Görüntü, görüntü Sprite öğesine uygulandığında, PNG 'ye dönüştürülür. Net işleme sağlamak için, görüntü şablonunu bir katmanda ölçeklendirmeye kıyasla Sprite öğesine eklemeden önce ölçeklendirmeniz daha iyidir.

Bu işlev, görüntüyü zaman uyumsuz olarak görüntü hareketli görüntüsüne yükler. Bu nedenle, bu işlevin tamamlanmasını beklerseniz bir Promise döndürür.

Aşağıdaki kod, yerleşik şablonlardan birinden bir görüntünün nasıl oluşturulacağını ve bir sembol katmanıyla birlikte nasıl kullanılacağını gösterir.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Sembol katmanı ile görüntü şablonu kullanma

Harita görüntüsü hareketli görüntüsüne bir görüntü şablonu yüklendikten sonra, ' ın seçeneğinde görüntü kaynak KIMLIĞINE başvurarak sembol katmanında bir sembol olarak oluşturulabilir `image` `iconOptions` .

Aşağıdaki örnek, bir sembol katmanını, `marker-flat` deniz mavisi birincil rengine ve beyaz bir ikincil renge sahip görüntü şablonunu kullanarak işler. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Yerleşik simge şablonuyla sembol katmanı" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>birlikte yerleşik simge şablonuyla kalem sembolü katmanına</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Bir çizgi yolu üzerinde görüntü şablonu kullanma

Bir görüntü şablonu harita görüntüsü Sprite öğesine yüklendikten sonra, bir veri kaynağına bir LineString eklenerek ve bir seçenek ile sembol katmanını kullanarak `lineSpacing` ve ' nin seçeneğinde görüntü KAYNAĞıNıN kimliğine başvurarak bir satırın yolu üzerinde oluşturulabilir `image` `iconOptions` . 

Aşağıdaki örnek, haritada pembe bir çizgi oluşturur ve bir sembol katmanını kullanarak bir `car` Dodger mavi birincil rengi ve bir beyaz ikincil renge sahip görüntü şablonunu kullanır. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Yerleşik simge şablonuyla çizgi katmanı" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>yerleşik simge şablonuyla kalem çizgisi katmanına</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

> [!TIP]
> Görüntü şablonu işaret ediyorsa, `rotation` çizgi ile aynı yöne işaret etmek istiyorsanız sembol katmanının simge seçeneğini 90 olarak ayarlayın.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Çokgen katmanı ile görüntü şablonu kullanma

Harita görüntüsü hareketli görüntüsüne bir görüntü şablonu yüklendikten sonra, katman seçeneğinde görüntü kaynak KIMLIĞINE başvurarak çokgen katmanında bir doldur stili olarak oluşturulabilir `fillPattern` .

Aşağıdaki örnek, `dot` kırmızı bir birincil renkle görüntü şablonunu kullanarak bir çokgen katmanı ve saydam bir ikincil renk oluşturur.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Yerleşik simge şablonuyla Çokgen doldur" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>yerleşik simge şablonuyla kalem dolgusu Çokgen</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> ' ne <a href='https://codepen.io'></a>bakın.
</iframe>

> [!TIP]
> Daha sonra, Fill desenlerinin ikincil rengini ayarlamak, temel alınan haritanın hala birincil deseni sağladığını daha kolay görmenizi sağlar. 

## <a name="use-an-image-template-with-an-html-marker"></a>HTML işaretleyicisi ile bir görüntü şablonu kullanma

Bir resim şablonu, işlevi kullanılarak alınabilir `altas.getImageTemplate` ve bır HTML işaretçisinin içeriği olarak kullanılır. Şablon, `htmlContent` işaret seçeneğine geçirilebilir ve ardından `color` , `secondaryColor` ve seçenekleri kullanılarak özelleştirilebilir `text` .

Aşağıdaki örnek, `marker-arrow` şablonu kırmızı bir birincil renkle, bir pembe ikincil renkle ve "00" metin değeri ile kullanır.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Yerleşik simge şablonuyla HTML Işaretçisi" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>yerleşik simge şablonuyla kalem HTML işaretine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>


> [!TIP]
> Görüntü şablonları eşleme dışında da kullanılabilir. Getımagetemplate funcıton, yer tutucuları olan bir SVG dizesi döndürüyor; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Geçerli bir SVG dizesi oluşturmak için bu yer tutucu değerlerini değiştirin. Daha sonra SVG dizesini doğrudan HTML DOM 'a ekleyebilir veya bir veri URI 'sine dönüştürebilir ve bunu bir resim etiketine ekleyebilirsiniz. Örnek:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Özel yeniden kullanılabilir şablonlar oluşturma

Uygulamanız farklı simgelerle aynı simgeyi kullanıyorsa veya ek görüntü şablonları ekleyen bir modül oluşturuyorsanız, bu simgeleri Azure Maps web SDK 'sına kolayca ekleyebilir ve alabilirsiniz. Ad alanı üzerinde aşağıdaki statik işlevleri kullanın `atlas` .

| Name | Dönüş Türü | Description | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Atlas ad alanına özel bir SVG resim şablonu ekler. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Bir SVG şablonunu ada göre alır. |
| `getAllImageTemplateNames()` | String [] |  Bir SVG şablonunu ada göre alır. |

SVG resim şablonları aşağıdaki yer tutucu değerlerini destekler:

| Yer tutucu | Description |
|-|-|
| `{color}` | Birincil renk. | 
| `{secondaryColor}` | İkincil renk. | 
| `{scale}` | SVG görüntüsü, harita görüntüsü Sprite öğesine eklendiğinde bir PNG görüntüsüne dönüştürülür. Bu yer tutucu, açıkça oluşturulduğundan emin olmak için, bir şablonu dönüştürüldükten önce ölçeklendirmek üzere kullanılabilir. | 
| `{text}` | HTML Işaretleyicisi ile kullanıldığında metnin işlenmesi için konum. |

Aşağıdaki örnek, bir SVG şablonunun nasıl alınacağını ve Azure Maps web SDK 'sına yeniden kullanılabilir bir simge şablonu olarak nasıl ekleneceğini gösterir. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Atlas ad alanına özel simge şablonu ekleme" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () tarafından bulunan <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Atlas ad alanına özel simge şablonu ekleme</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="list-of-image-templates"></a>Görüntü şablonlarının listesi

Bu tablo, Azure Maps web SDK 'sının Şu anda kullanılabilir olan tüm görüntü şablonlarını listeler. Şablon adı her bir görüntünün üzerinde. Varsayılan olarak, birincil renk mavidir ve ikincil renk beyazdır. İkincil rengin beyaz bir arka planda daha kolay görülmesini sağlamak için aşağıdaki görüntülerde ikincil renk siyah olarak ayarlanmıştır.

**Sembol simgesi şablonları**

:::row:::
   :::column span="":::
      im
   :::column-end:::
   :::column span="":::
      işaretleyici-kalın
   :::column-end:::
   :::column span="":::
      işaretleyici daire
   :::column-end:::
   :::column span="":::
      işaretleyici-düz
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![işaret simgesi](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![işaretleyici-kalın simgesi](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![işaret-daire simgesi](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![işaret-düz simgesi](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      işaretleyici-kare
   :::column-end:::
   :::column span="":::
      işaretleyici-kare-küme
   :::column-end:::
   :::column span="":::
      işaretleyici-ok
   :::column-end:::
   :::column span="":::
      işaretleyici-bol-pin
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![işaret-kare simgesi](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![işaretleyici-kare-küme simgesi](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![işaret-ok simgesi](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![işaretleyici-bol-pin simgesi](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      işaretleyici-kare yuvarlatılmış
   :::column-end:::
   :::column span="":::
      işaretleyici-kare yuvarlatılmış-küme
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      bayrak-üçgen
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![işaretleyici-kare yuvarlatılmış simgesi](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![işaretleyici-kare yuvarlatılmış-küme simgesi](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![bayrak simgesi](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![bayrak-üçgen simgesi](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      üçgen
   :::column-end:::
   :::column span="":::
      üçgen-kalın
   :::column-end:::
   :::column span="":::
      üçgen-yukarı ok
   :::column-end:::
   :::column span="":::
      üçgen-sol ok
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![üçgen simgesi](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![üçgen-kalın simgesi](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![Üçgen-aşağı ok simgesi](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![üçgen-sol ok simgesi](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      altıgeni
   :::column-end:::
   :::column span="":::
      altıgen-kalın
   :::column-end:::
   :::column span="":::
      altıgen yuvarlatılmış
   :::column-end:::
   :::column span="":::
      altıgen-yuvarlatılmış-kalın
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![altıgen simgesi](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![altıgen-kalın simgesi](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![altıgen yuvarlatılmış simgesi](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![altıgen-yuvarlatılmış-kalın simgesi](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      sabitleme
   :::column-end:::
   :::column span="":::
      PIN-yuvarlak
   :::column-end:::
   :::column span="":::
      yuvarlak kare
   :::column-end:::
   :::column span="":::
      yuvarlak köşeli-kalın
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![raptiye simgesi](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![PIN-Round simgesi](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![Yuvarlatılmış-kare simgesi](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![yuvarlak köşeli-kalın simgesi](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      yukarı ok
   :::column-end:::
   :::column span="":::
      ok-yukarı-ince
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![yukarı ok simgesi](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![ok-yukarı-ince simgesi](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![Araba simgesi](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Çokgen dolgusu kalıp şablonları**

:::row:::
   :::column span="":::
      denetimc
   :::column-end:::
   :::column span="":::
      denetleyici-döndürülmüş
   :::column-end:::
   :::column span="":::
      daireleri
   :::column-end:::
   :::column span="":::
      Daire-aralıklı
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![denetleyici simgesi](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![denetleyici-döndürülmüş simgesi](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![daireler simgesi](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![Daire içinde aralıklı simge](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      köşegen-çizgiler
   :::column-end:::
   :::column span="":::
      Köşegen-aşağı satırlar
   :::column-end:::
   :::column span="":::
      Köşegen-Yukarı şeritler
   :::column-end:::
   :::column span="":::
      Köşegen-aşağı şeritler
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![köşegen-çizgiler-yukarı simgesi](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![köşegen-çizgiler-aşağı simgesi](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![çapraz şeritler-yukarı simgesi](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![Köşegen-aşağı açılan simgesi](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      kılavuz çizgileri
   :::column-end:::
   :::column span="":::
      Döndürülmüş-kılavuz çizgileri
   :::column-end:::
   :::column span="":::
      Döndürülmüş-ızgara-şeritler
   :::column-end:::
   :::column span="":::
      x dolgusu
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![kılavuz çizgileri simgesi](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![Döndürülmüş-kılavuz çizgileri simgesi](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![Döndürülmüş-Grid-şeritler simgesi](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![x dolgusu simgesi](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      zg-Zak
   :::column-end:::
   :::column span="":::
      Zig-Zak-dikey
   :::column-end:::
   :::column span="":::
      noktaları
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![Zig-Zak simgesi](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![Zig-Zak-dikey simgesi](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![noktalar simgesi](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

**Önceden yüklenmiş görüntü simgeleri**

Eşleme,, ve şablonlarını kullanarak haritalar görüntüsü Sprite öğesine bir simge kümesini önceden yükler `marker` `pin` `pin-round` . Bu simge adları ve renk değerleri aşağıdaki tabloda listelenmiştir.

| simge adı | color | secondaryColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Şimdi deneyin Aracı

Aşağıdaki araçla farklı yerleşik görüntü şablonlarını çeşitli şekillerde işleyebilir ve birincil ve ikincil renkleri ve ölçeği özelleştirebilirsiniz.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Simge şablonu seçenekleri" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>simgesi şablon seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Imabir Pritemanager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Atlas ad alanı](/javascript/api/azure-maps-control/atlas#functions
)

Görüntü şablonlarının kullanılabileceği daha fazla kod örneği için aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [Sembol katmanı ekleme](map-add-pin.md)

> [!div class="nextstepaction"]
> [Çizgi katmanı ekleme](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Çokgen katmanı ekleme](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML üreticileri ekleme](map-add-bubble-layer.md)