---
title: Haritadaki şekillerden veri al | Microsoft Azure haritaları
description: Bu makalede, Microsoft Azure haritaları Web SDK 'sını kullanarak bir haritada çizilmiş şekil verilerini alma hakkında bilgi edinin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ddb8009e544ede82d1c56d112950ff247a87380c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92890709"
---
# <a name="get-shape-data"></a>Şekil verilerini alma

Bu makalede, haritada çizilen şekillerin verilerini nasıl alacağınız gösterilmektedir. [Çizim Yöneticisi](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--)Içinde **Drawingmanager. GetSource ()** işlevini kullanıyoruz. Çizilmiş bir şeklin coğrafi JSON verilerini ayıklamak ve başka bir yerde kullanmak istediğinizde çeşitli senaryolar vardır.  


## <a name="get-data-from-drawn-shape"></a>Çizilen şekilden verileri al

Aşağıdaki işlev, çizilen şeklin kaynak verilerini alır ve ekrana verir. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Aşağıda, işlevleri test etmek üzere bir şekil ekleyebileceğiniz, çalışan tüm kod örneği verilmiştir:

<br/>

<iframe height="686" title="Şekil verilerini alma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Codepen üzerinde Azure Maps () ile <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Şekil verileri alın</a> kalemine bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'></a>.
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Çizim araçları modülünün ek özelliklerini kullanmayı öğrenin:

> [!div class="nextstepaction"]
> [Çizim olaylarına tepki verme](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Etkileşim türleri ve klavye kısayolları](drawing-tools-interactions-keyboard-shortcuts.md)

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Çizim Yöneticisi](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Çizim araç çubuğu](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)