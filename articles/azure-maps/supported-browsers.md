---
title: Web SDK desteklenen tarayıcılar | Microsoft Azure haritaları
description: Azure Haritalar Web SDK 'sının bir tarayıcıyı destekleyip desteklemediğini denetleme hakkında bilgi edinin. Desteklenen tarayıcıların listesini görüntüleyin. Harita hizmetlerini eski tarayıcılarla nasıl kullanacağınızı öğrenin.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6321b96cb3db570102f138dcfd949d9c32daedbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384478"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK destekleyen tarayıcılar

Azure Haritalar Web SDK 'Sı, [Atlas. IsSupported](/javascript/api/azure-maps-control/atlas#issupported-boolean-)adlı bir yardımcı işlev sağlar. Bu işlev, bir Web tarayıcısının harita denetimini yüklemeyi ve işlemeyi desteklemek için gereken en düşük WebGL özellikleri kümesine sahip olup olmadığını algılar. İşlevin nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir:

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
- Apple Safari (macOS X) (geçerli ve önceki sürüm)

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

Aşağıdaki Web SDK modülleri de Node.js desteklenir:

- Hizmetler modülü ([Belgeler](how-to-use-services-module.md)  |  [NPM modülü](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>Hedef eski tarayıcılar

WebGL 'yi desteklemeyen veya yalnızca sınırlı desteği olan eski tarayıcıları hedeflemek isteyebilirsiniz. Bu gibi durumlarda, Azure haritalar hizmetlerini, [leaflet](https://leafletjs.com/)gibi bir açık kaynaklı eşleme denetimiyle birlikte kullanmanızı öneririz. Açık kaynaklı [Azure Maps leaflet eklentisini](https://github.com/azure-samples/azure-maps-leaflet)kullanan bir örnek aşağıda verilmiştir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure haritalar + leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>+ leaflet</a> 'e Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) <a href='https://codepen.io'></a>tarafından bakın.
</iframe>

Azure haritalar 'ın leaflet 'de kullanıldığı ek kod örnekleri [burada](https://azuremapscodesamples.azurewebsites.net/?search=leaflet)bulunabilir.

Azure haritalar ekibinin eklenti oluşturduğu bazı popüler açık kaynaklı harita denetimleri [aşağıda](open-source-projects.md#third-part-map-control-plugins) verilmiştir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Haritalar Web SDK 'Sı hakkında daha fazla bilgi edinin:

[Harita denetimi](how-to-use-map-control.md)

[Hizmetler modülü](how-to-use-services-module.md)
