---
title: Bir haritada trafiği göster | Microsoft Azure haritaları
description: Haritalar 'a trafik verileri ekleme hakkında bilgi edinin. Akış verileri hakkında bilgi edinin ve haritalara olay verileri ve akış verileri eklemek için Azure Maps web SDK 'sını kullanma konusuna bakın.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b6fcf39e05c4649503f0b7a80aadaaa16df24674
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98599556"
---
# <a name="show-traffic-on-the-map"></a>Haritada trafiği göster

Azure haritalar 'da kullanılabilen iki tür trafik verisi vardır:

- Olay verileri-oluşturma, yol kapanışları ve kazalardan dolayı gibi şeyler için nokta ve hat tabanlı verilerden oluşur.
- Akış verileri-yollar üzerindeki trafik akışı hakkında ölçümler sağlar. Genellikle trafik akışı verileri, yolların renklendirmek için kullanılır. Renkler, akışı yavaşlatan, hız sınırına veya başka bir ölçüye göre ne kadar trafik altına göre belirlenir. Haritanın trafik seçeneğine geçirilebilecek dört değer vardır `flow` .

    |Akış değeri | Description|
    | :-- | :-- |
    | `none` | Harita üzerinde trafik verilerini görüntülemiyor |
    | `relative` | Yolun serbest akış hızına göre trafik verilerini gösterir |
    | `relative-delay` | Beklenen ortalama gecikmeden daha yavaş olan bölgeleri görüntüler |
    | `absolute` | Yolda tüm taşıtın mutlak hızını gösterir |

Aşağıdaki kod, Haritada trafik verilerinin nasıl görüntüleneceğini gösterir.

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

Aşağıda, yukarıdaki işlevselliğin tamamen çalışan kod örneği verilmiştir.

<br/>

<iframe height='500' scrolling='no' title='Haritada trafiği gösterme' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bkz. kaleme Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>bir haritadaki trafiği gösterme</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="traffic-overlay-options"></a>Trafik kaplama seçenekleri

Aşağıdaki araç, işleme değişikliğini görmek için farklı trafik kaplama ayarları arasında geçiş yapmanızı sağlar. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Trafik kaplama seçenekleri" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>trafiği kaplama seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>


## <a name="add-traffic-controls"></a>Trafik denetimleri ekleme

Haritaya eklenebilen iki farklı trafik denetimi vardır. İlk denetim, `TrafficControl` trafiği açmak ve kapatmak için kullanılabilecek bir iki durumlu düğme ekler. Bu denetimin seçenekleri, trafiği gösterdiğinizde trafik ayarlarının ne zaman kullanılacağını belirtmenize izin verir. Bu denetim, varsayılan olarak göreli trafik akışını ve olay verilerini görüntüler, ancak bunu mutlak trafik akışını göstermek için ve isterseniz herhangi bir olay olmadan değiştirebilirsiniz. İkinci denetim, `TrafficLegendControl` kullanıcının renk kodu yolunda ne anlama geldiğini anlamalarına yardımcı olan haritaya bir trafik akışı göstergesi ekler. Bu denetim haritada yalnızca trafik akışı verileri haritada görüntülenirken ve diğer zamanlarda gizli olacak şekilde görünür.

Aşağıdaki kod, haritaya trafik denetimlerinin nasıl ekleneceğini gösterir.

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Trafik denetimleri" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>trafiği denetimlerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

Kullanıcı deneyimlerinizi geliştirin:

> [!div class="nextstepaction"]
> [Fare olayları ile harita etkileşimi](map-events.md)

> [!div class="nextstepaction"]
> [Erişilebilir eşleme oluşturma](map-accessibility.md)

> [!div class="nextstepaction"]
> [Kod örnek sayfası](https://aka.ms/AzureMapsSamples)