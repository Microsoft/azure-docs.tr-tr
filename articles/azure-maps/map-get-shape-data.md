---
title: Azure haritalar 'da çizim yöneticisinden şekil verileri alın | Microsoft Docs
description: Azure Haritalar Web SDK 'sını kullanarak şekil verilerini alma
author: walsehgal
ms.author: v-musehg
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e7706fba1efad1bd0ce7110e129dcf113689af9a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309837"
---
# <a name="get-shape-data"></a>Şekil verilerini al

Bu makalede, [Çizim Yöneticisi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **Drawingmanager. GetSource ()** işlevini kullanarak haritada çizilmiş şekil verilerini alma işlemi gösterilmektedir. Çizilmiş bir şeklin coğrafi JSON verilerini ayıklamak ve başka bir yerde kullanmak isteyebileceğiniz çeşitli senaryolar olabilir.  


## <a name="get-data-from-drawn-shape"></a>Çizilen şekilden verileri al

Aşağıdaki işlev, çizilen şeklin kaynak verilerini alır ve ekranda çıktı. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Aşağıda, işlevleri test etmek üzere bir şekil ekleyebileceğiniz, çalışan tüm kod örneği verilmiştir:

<br/>

<iframe height="686" title="Şekil verilerini al" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'><a href='https://codepen.io'>Codepen</a>üzerinde Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Şekil verileri alın</a> kalemine bakın.
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Harita](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Çizim Yöneticisi](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Çizim araç çubuğu](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
