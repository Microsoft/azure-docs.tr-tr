---
title: Uzamsal sorgular
description: Sahnede uzamsal sorgular yapma
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c664df586c260b3e16f64c071190055dbaeccd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594053"
---
# <a name="spatial-queries"></a>Uzamsal sorgular

Uzamsal sorgular, uzaktan işleme hizmetine hangi nesnelerin bir alanda konumlandırılabilir olduğunu sorabileceğiniz işlemlerdir. Uzamsal sorgular sıklıkla bir kullanıcının işaret ettiği nesneyi bulmak gibi etkileşimleri uygulamak için kullanılır.

Tüm uzamsal sorgular sunucuda değerlendirilir. Sonuç olarak, zaman uyumsuz işlemlerdir ve sonuçlar ağ gecikme süresine bağlı bir gecikmeyle gönderilir. Her uzamsal sorgu ağ trafiği oluşturduğundan, aynı anda çok fazla yapamamaya dikkat edin.

## <a name="collision-meshes"></a>Çakışma kafesleri

Uzamsal sorgular [Havok fizik](https://www.havok.com/products/havok-physics) altyapısı tarafından desteklenir ve özel bir çakışma ağı bulunmasını gerektirir. Varsayılan olarak, [model dönüştürme](../../how-tos/conversion/model-conversion.md) çakışma kafesleri oluşturur. Karmaşık bir modelde uzamsal sorgular gerekmiyorsa, [dönüştürme seçeneklerinde](../../how-tos/conversion/configure-model-conversion.md)çakışma kafesi oluşturmayı devre dışı bırakmayı göz önünde bulundurun, çünkü bu birden çok şekilde etkiler:

* [Model dönüştürme](../../how-tos/conversion/model-conversion.md) oldukça uzun sürer.
* Dönüştürülen model dosya boyutları, önemli ölçüde daha büyük olduğundan indirme hızını etkiler.
* Çalışma zamanı yükleme süreleri daha uzun.
* Çalışma zamanı CPU bellek tüketimi daha yüksektir.
* Her model örneği için hafif bir çalışma zamanı performans yükü vardır.

## <a name="ray-casts"></a>Işın yayınları

Bir *Ray cast* , çalışma zamanının, belirli bir konumdan başlayıp belirli bir yöne işaret eden bir ışın tarafından hangi nesnelerin kesişen olduğunu denetlediğinde uzamsal bir sorgudur. Bir iyileştirme olarak, çok uzakta olan nesneleri aramak için bir en yüksek ışın uzaklığı de verilir.

```cs
async void CastRay(RenderingSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastQueryResult result = await session.Connection.RayCastQueryAsync(rayCast);
    RayCastHit[] hits = result.Hits;
    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitObject;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
```

```cpp
void CastRay(ApiHandle<RenderingSession> session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast;
    rayCast.StartPos = {0, 0, 0};
    rayCast.EndPos = {0, 0, 1};
    rayCast.MaxHits = 10;

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy::ClosestHit;

    session->Connection()->RayCastQueryAsync(rayCast, [](Status status, ApiHandle<RayCastQueryResult> result)
    {
        if (status == Status::OK)
        {
            std::vector<RayCastHit> hits;
            result->GetHits(hits);

            if (hits.size() > 0)
            {
                auto hitObject = hits[0].HitObject;
                auto hitPosition = hits[0].HitPosition;
                auto hitNormal = hits[0].HitNormal;

                // do something with the hit information
            }
        }
    });
}
```


Üç isabet toplama modu vardır:

* **`Closest` :** Bu modda yalnızca en yakın isabet raporlanır.
* **`Any` :** Bir ışın her şey için ne zaman isabet eteceğine, ancak tam olarak ne olduğunu *önemsemediğini* bildirmek istiyorsanız bu modu tercih edin. Bu sorgu, değerlendirmek için önemli ölçüde olabilir, ancak aynı zamanda yalnızca birkaç uygulama içerir.
* **`All` :** Bu modda, ışın üzerindeki tüm isabetlerin bildirilmesi, uzaklığına göre sıralanır. Gerçekten ilk isabetden daha fazlasına ihtiyaç duymadığınız takdirde bu modu kullanmayın. Bildirilen isabetlerin sayısını `MaxHits` seçeneğiyle sınırlayın.

Nesneleri seçmeli bir şekilde kabul edilmeden dışlamak için, [HierarchicalStateOverrideComponent](override-hierarchical-state.md) bileşeni kullanılabilir.

<!--
The CollisionMask allows the query to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>İsabet sonucu

Bir Ray cast sorgusunun sonucu bir isabet dizisidir. Bir nesne isabet ettirilse dizi boştur.

Bir Isabet aşağıdaki özelliklere sahiptir:

* **`HitEntity` :** Hangi [varlığa](../../concepts/entities.md) ulaşıldı.
* **`SubPartId` :** Bir [meshcomponent](../../concepts/meshes.md)'ta hangi *alt kafesde* ulaşıldı. , İçinde dizin kurmak `MeshComponent.UsedMaterials` ve bu noktada [malzemenin](../../concepts/materials.md) aramak için kullanılabilir.
* **`HitPosition` :** Ray 'un nesnenin kesişen bulunduğu dünya alanı konumu.
* **`HitNormal` :** Dünya alanı, kesişme konumundaki kafesin normal yüzeyi.
* **`DistanceToHit` :** Ray başlangıç konumundan hit 'e kadar olan uzaklık.

## <a name="api-documentation"></a>API belgeleri

* [C# RenderingConnection. Rayroqueryasync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.raycastqueryasync)
* [C++ RenderingConnection:: Rayroqueryasync ()](/cpp/api/remote-rendering/renderingconnection#raycastqueryasync)

## <a name="next-steps"></a>Sonraki adımlar

* [Nesne sınırları](../../concepts/object-bounds.md)
* [Hiyerarşik durumları geçersiz kılma](override-hierarchical-state.md)