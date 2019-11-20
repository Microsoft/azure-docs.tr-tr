---
title: Azure haritalar ile erişilebilir bir uygulama oluşturma | Microsoft Docs
description: Azure haritalar kullanarak erişilebilir bir uygulama oluşturma
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638780"
---
# <a name="building-an-accessible-application"></a>Erişilebilir bir uygulama oluşturma

Bu makalede, bir ekran okuyucu tarafından kullanılabilecek bir harita uygulamasının nasıl oluşturulacağı gösterilmektedir.

## <a name="understand-the-code"></a>Kodu anlama

<iframe height='500' scrolling='no' title='Erişilebilir bir uygulama oluşturma' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Bkz. Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) ile <a href='https://codepen.io'>codepen</a>'da <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>erişilebilir bir uygulama oluşturma</a> .
</iframe>

Eşleme, erişilebilirlik özellikleriyle önceden oluşturulmuş olarak sunulur. Kullanıcılar, klavyeyi kullanarak haritada gezinebilirler. Bir ekran okuyucu çalışıyorsa, eşleme kullanıcıya durumunda değişiklikleri bilgilendirir.
Örneğin, eşleme değiştirildiğinde veya yakınlaştırıldığında kullanıcılara eşleme değişiklikleri bildirilir. Temel haritaya yerleştirilmiş ek bilgiler, ekran okuyucu kullanıcıları için ilgili metin bilgilerine sahip olmalıdır.

[Açılır pencere](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) kullanımı, bunu elde etmenin bir yoludur. Yukarıdaki arama örneğinde, haritaya yerleştirilmiş her PIN için haritaya metin bilgisi içeren bir açılan pencere eklenir. Açılan menünün [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [Attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) yöntemini kullanmak, açılan pencerenin haritada açılan pencereyi görsel olarak görüntülemeden ekran okuyucu tarafından görülebilmesi için izin verir.

## <a name="next-steps"></a>Sonraki adımlar

Açılan pencere sınıfı ve bu makalede kullanılan yöntemleri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Kutu](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Daha fazla kod örneği inceleyin:

> [!div class="nextstepaction"]
> [Kod örnek sayfası](https://aka.ms/AzureMapsSamples)
