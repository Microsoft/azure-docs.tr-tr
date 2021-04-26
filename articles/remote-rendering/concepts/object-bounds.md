---
title: Nesne sınırları
description: Uzamsal nesne sınırlarının nasıl sorgulanabilecek açıklanmaktadır
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: df04b767035dffb62fde89d1e74b808d62fcc943
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594493"
---
# <a name="object-bounds"></a>Nesne sınırları

Nesne sınırları, bir [varlığın](entities.md) ve alt öğelerinin kaplayacağı birimi temsil eder. Azure uzaktan Işlemede, nesne sınırları her zaman *eksen hizalanmış sınırlayıcı kutular* (AABB) olarak verilir. Nesne sınırları, *yerel alanda* veya *Dünya* alanında olabilir. Her iki durumda da, her zaman eksen hizalı olur, bu da kapsamlar ve birim yerel ve dünya alanı gösterimi arasında farklılık gösterebilir.

## <a name="querying-object-bounds"></a>Nesne sınırlarını sorgulama

Bir [kafesin](meshes.md) yerel eksen hizalanmış sınırlayıcı kutusu doğrudan kafes kaynağından sorgulanabilir. Bu sınırlar, varlığın dönüşümünü kullanarak bir varlığın yerel alanına veya dünya alanına dönüştürülebilir.

Bu şekilde tüm nesne hiyerarşisinin sınırlarını hesaplamak mümkündür, ancak hiyerarşide geçiş yapmak, her bir kafesin sınırlarını sorgulamak ve bunları el ile birleştirmek gerekir. Bu işlem hem sıkıcı hem de verimsiz.

Daha iyi bir yol, `QueryLocalBoundsAsync` bir varlığı çağırmak ya da `QueryWorldBoundsAsync` bir varlığı kullanmaktır. Hesaplama daha sonra sunucuda boşaltılır ve en az gecikmeyle döndürülür.

```cs
public async void GetBounds(Entity entity)
{
    try
    {
        Task<Bounds> boundsQuery = entity.QueryWorldBoundsAsync();
        Bounds result = await boundsQuery;
    
        Double3 aabbMin = result.Min;
        Double3 aabbMax = result.Max;
        // ...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    entity->QueryWorldBoundsAsync(
        // completion callback:
        [](Status status, Bounds bounds)
        {
           if (status == Status::OK)
            {
                Double3 aabbMin = bounds.Min;
                Double3 aabbMax = bounds.Max;
                // ...
            }
        }
    );
}
```

## <a name="api-documentation"></a>API belgeleri

* [C# Entity. QueryLocalBoundsAsync](/dotnet/api/microsoft.azure.remoterendering.entity.querylocalboundsasync)
* [C++ varlığı:: QueryLocalBoundsAsync](/cpp/api/remote-rendering/entity#querylocalboundsasync)

## <a name="next-steps"></a>Sonraki adımlar

* [Uzamsal sorgular](../overview/features/spatial-queries.md)