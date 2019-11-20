---
title: Web SDK desteklenen tarayıcılar-Azure Maps | Microsoft Docs
description: Azure Maps web SDK için desteklenen tarayıcılar hakkında bilgi edinin
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: d867e9a1afcb495aee7e8b0e0b3b001104f48a4c
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844811"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK destekleyen tarayıcılar

Azure Haritalar Web SDK 'Sı, [Atlas. IsSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)adlı bir yardımcı işlev sağlar. Bu işlev, bir Web tarayıcısının harita denetimini yüklemeyi ve işlemeyi desteklemek için gereken en düşük WebGL özellikleri kümesine sahip olup olmadığını algılar. İşlevin nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir:

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>Masaüstü

Azure Haritalar Web SDK 'Sı, aşağıdaki masaüstü tarayıcılarını destekler:

- Microsoft Edge (geçerli ve önceki sürüm)
- Google Chrome (geçerli ve önceki sürüm)
- Mozilla Firefox (geçerli ve önceki sürüm)
- Apple Safari (Mac OS X) (geçerli ve önceki sürüm)

Ayrıca, bu makalenin ilerleyen kısımlarında bulunan [eski tarayıcıları de hedefleyin](#Target-Legacy-Browsers) .

## <a name="mobile"></a>Mobil

Azure Haritalar Web SDK 'Sı, aşağıdaki mobil tarayıcıları destekler:

- Android
  - Android 6,0 ve üzeri cihazlarda geçerli Chrome sürümü
  - Android 6,0 ve üzeri cihazlarda Chrome WebView
- iOS
  - İOS 'un geçerli ve önceki ana sürümünde Mobile Safari
  - İOS 'un geçerli ve önceki ana sürümündeki UIWebView ve WKWebView
  - İOS için Chrome 'un geçerli sürümü

> [!TIP]
> Bir Web görünümü kullanarak bir mobil uygulamanın içine harita ekliyorsanız, Azure Content Delivery Network 'de barındırılan SDK sürümüne başvurmak yerine [Azure Maps web SDK 'sının NPM paketini](https://www.npmjs.com/package/azure-maps-control) kullanmayı tercih edebilirsiniz. Bu yaklaşım yükleme süresini azaltır çünkü SDK zaten kullanıcının cihazında olduğundan ve çalışma zamanında indirilmesinin gerekli değildir.

## <a name="nodejs"></a>Node.js

Node. js ' de aşağıdaki Web SDK modülleri de desteklenir:

- Hizmetler modülü ([Belgeler](how-to-use-services-module.md) | [NPM modülü](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>Hedef eski tarayıcılar

WebGL 'yi desteklemeyen veya yalnızca sınırlı desteği olan eski tarayıcıları hedeflemek isteyebilirsiniz. Bu gibi durumlarda, Azure haritalar hizmetlerini, [leaflet](https://leafletjs.com/)gibi bir açık kaynaklı eşleme denetimiyle birlikte kullanmanızı öneririz. Bir örneği aşağıda verilmiştir:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure haritalar + leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>Codepen</a>'da Azure Maps <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>+ leaflet</a> 'e Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() tarafından bakın.
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Azure Haritalar Web SDK 'Sı hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita denetimi](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Hizmetler modülü](how-to-use-services-module.md)
