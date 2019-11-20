---
title: Azure haritalar ile trafiği gösterme | Microsoft Docs
description: Azure Haritalar Web SDK 'sında trafik verilerini görüntüleme.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b765f6f35cecfdd3183b4432954fcfb2548de046
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874874"
---
# <a name="show-traffic-on-the-map"></a>Haritada trafiği göster

Azure haritalar 'da kullanılabilen iki tür trafik verisi vardır:

- Olay verileri-oluşturma, yol kapanışları ve kazalardan dolayı gibi şeyler için nokta ve hat tabanlı verilerden oluşur.
- Akış verileri-yollar üzerindeki trafik akışı hakkında ölçümler sağlar. Trafik akışı verileri genellikle, trafiğin hız sınırına veya başka bir ölçüme göre ne kadar trafik yavaşlatıyor olduğunu temel alarak yollar için kullanılır. Azure haritalar 'daki trafik akışı verileri üç farklı ölçüm ölçümüne sahiptir:
    - `relative`-yolun ücretsiz akış hızına göre değişir.
    - `absolute`-yolda tüm taşıtlardan oluşan mutlak hızdır.
    - `relative-delay`-beklenen ortalama gecikmeden daha yavaş olan bölgeleri görüntüler.

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

<iframe height='500' scrolling='no' title='Haritada trafiği gösterme' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bkz. kaleme Azure Maps ( <a href='https://codepen.io'></a><a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/WMLRPw/'>bir haritadaki trafiği gösterme</a> .
</iframe>

## <a name="traffic-overlay-options"></a>Trafik kaplama seçenekleri

Aşağıdaki araç, işleme değişikliğini görmek için farklı trafik kaplama ayarları arasında geçiş yapmanızı sağlar. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Trafik kaplama seçenekleri" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>trafiği kaplama seçeneklerine</a> bakın.
</iframe>

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

Kullanıcı deneyimlerinizi geliştirin:

> [!div class="nextstepaction"]
> [Fare olayları ile harita etkileşimi](map-events.md)

> [!div class="nextstepaction"]
> [Erişilebilir eşleme oluşturma](map-accessibility.md)

> [!div class="nextstepaction"]
> [Kod örnek sayfası](https://aka.ms/AzureMapsSamples)
