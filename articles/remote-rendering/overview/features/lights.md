---
title: Sahne aydınlatması
description: Hafif kaynak açıklaması ve özellikleri
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92206858"
---
# <a name="scene-lighting"></a>Sahne aydınlatması

Varsayılan olarak, uzaktan işlenen nesneler bir [gök ışığı](sky.md)kullanılarak aydınlatılır. Çoğu uygulama için bu zaten yeterlidir, ancak sahneye daha açık kaynaklar ekleyebilirsiniz.

> [!IMPORTANT]
> Yalnızca [PBR malzemeleri](pbr-materials.md) hafif kaynaklardan etkilenir. [Renk malzemeleri](color-materials.md) her zaman tamamen parlak şekilde görünür.

> [!NOTE]
> Atama gölgeleri Şu anda desteklenmiyor. Azure uzaktan Işleme, çok büyük miktarlarda geometriyi işlemek için en iyi duruma getirilmiştir ve gerekirse birden fazla GPU kullanılır. Gölge çevrim için geleneksel yaklaşımlar, bu senaryolarda iyi çalışmaz.

## <a name="common-light-component-properties"></a>Ortak açık bileşen özellikleri

Tüm ışık türleri soyut temel sınıftan türetilir `LightComponent` ve bu özellikleri paylaşır:

* **Renk:** [Gama](https://en.wikipedia.org/wiki/SRGB)alanındaki ışığın rengi. Alfa yok sayılır.

* **Yoğunluk:** Işığın parlaklığı. Nokta ve spot ışıklar için yoğunluk, ışığın ne kadar uzakta olduğunu da tanımlar.

## <a name="point-light"></a>Nokta ışığı

Azure uzaktan Işlemede, `PointLightComponent` Softer ışık kaynaklarının benzetimini yapmak için yalnızca tek bir noktadan (aynı zamanda küçük bir kürenin veya küçük bir tüp) ışık olamaz.

### <a name="pointlightcomponent-properties"></a>PointLightComponent özellikleri

* **Yarıçap:** Varsayılan yarıçap sıfırdır ve bu durumda ışığın nokta ışığı olarak hareket eder. Yarıçap sıfırdan büyükse, Yansımalı vurguların görünümünü değiştiren küresel bir açık kaynak olarak davranır.

* **Uzunluk:** Her ikisi `Length` de `Radius` sıfır değilse, ışık bir boru ışığı işlevi görür. Bu, Neon boruları benzetimini yapmak için kullanılabilir.

* **Zayıflauationkesme:** (0, 0) ise, ışığın zayıflatılaması yalnızca öğesine bağlıdır `Intensity` . Ancak, ışığın şiddette doğrusal olarak ölçeği sıfıra ölçeklendiği özel min/maksimum uzaklıklar sağlayabilirsiniz. Bu özellik, belirli bir ışığın daha küçük bir etkisi aralığını zorlamak için kullanılabilir.

* **Projectedcubemap:** Geçerli bir [küp harita](../../concepts/textures.md)olarak ayarlandıysa, doku ışığın çevresindeki geometri üzerine yansıtıldır. Cubemap 'in rengi ışığın rengi ile modüle edilir.

## <a name="spot-light"></a>Spot ışığı

, `SpotLightComponent` Öğesine benzerdir `PointLightComponent` ancak ışık bir koni şekli ile sınırlıdır. Koni yönü, *sahip varlığın negatif z ekseni* tarafından tanımlanır.

### <a name="spotlightcomponent-properties"></a>SpotLightComponent özellikleri

* **Yarıçap:** İle aynı `PointLightComponent` .

* **Spotangleder:** Bu Aralık, derece cinsinden ölçülen koni iç ve dış açısını tanımlar. İç açıdaki her şey tam parlaklık ile aydınlatılır. Bir azalma, bir penra benzeri bir efekt üreten dış açıya doğru uygulanır.

* **Falloffüs:** İç ve dış koni açısı arasındaki dönüşlerin ne kadar keskin olduğunu tanımlar. Daha yüksek bir değer daha net bir geçişe neden olur. Varsayılan 1,0, doğrusal bir geçişe neden olur.

* **Zayıflauationkesme:** İle aynı `PointLightComponent` .

* **Projected2dTexture:** Geçerli bir [2B dokusunu](../../concepts/textures.md)ayarlandıysa, görüntü, ışığın bulunduğu geometriye yansıtılır. Dokunun rengi ışığın rengine göre modüle edilir.

## <a name="directional-light"></a>Yönlü ışık

, `DirectionalLightComponent` Sonsuza kadar uzakta olan bir hafif kaynağın benzetimini yapar. Hafif, *sahip varlığının negatif z ekseninin* yönlerine sahiptir. Varlığın konumu yoksayıldı.

Ek özellik yok.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Hafif kaynakların işleme performansı üzerinde önemli bir etkisi vardır. Bunları dikkatle ve yalnızca uygulama için gerekliyse kullanın. Statik yönlü bir bileşen dahil olmak üzere herhangi bir statik küresel aydınlatma koşulu, ek işleme maliyeti olmadan [özel bir gök dokusuyla](sky.md)elde edilebilir.

## <a name="api-documentation"></a>API belgeleri

* [C# LightComponentBase sınıfı](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [C# PointLightComponent sınıfı](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [C# SpotLightComponent sınıfı](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [C# DirectionalLightComponent sınıfı](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [C++ LightComponentBase sınıfı](/cpp/api/remote-rendering/lightcomponentbase)
* [C++ PointLightComponent sınıfı](/cpp/api/remote-rendering/pointlightcomponent)
* [C++ SpotLightComponent sınıfı](/cpp/api/remote-rendering/spotlightcomponent)
* [C++ DirectionalLightComponent sınıfı](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Sonraki adımlar

* [Malzemeler](../../concepts/materials.md)
* [Çat](sky.md)