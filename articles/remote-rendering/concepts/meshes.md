---
title: Ağ yapıları
description: Azure uzaktan Işleme kapsamındaki kafeslerin tanımı
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: conceptual
ms.openlocfilehash: 33894e0d0b6f3ea50ffeac4f0c90c60f28e09f5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594527"
---
# <a name="meshes"></a>Ağ yapıları

## <a name="mesh-resource"></a>Kafes kaynağı

Kafesler yalnızca [model dönüştürme](../how-tos/conversion/model-conversion.md)ile oluşturulabilecek sabit bir [paylaşılan kaynaktır](../concepts/lifetime.md). Kafesler, [raycast sorguları](../overview/features/spatial-queries.md)için bir veya daha fazla *alt kafes* ve bir fizik gösterimiyle birlikte bulunur. Her alt ağ, varsayılan olarak işlenmesi gereken bir [malzemeye](materials.md) başvurur. 3B alana bir ağ yerleştirmek için bir [varlığa](entities.md)bir [meshcomponent](#meshcomponent) ekleyin.

### <a name="mesh-resource-properties"></a>Kafes kaynağı özellikleri

`Mesh`Sınıf özellikleri şunlardır:

* **Malzemeler:** Bir dizi malzeme. Her malzeme farklı bir alt ağ tarafından kullanılır. Dizide birden çok girdi aynı [malzemeye](materials.md)başvurabilir. Bu veriler çalışma zamanında değiştirilemez.

* **Sınırlar:** Kafes köşelerine ait yerel alan ekseni hizalanmış bir sınırlayıcı kutusu (AABB).

## <a name="meshcomponent"></a>MeshComponent

`MeshComponent`Sınıf, bir kafes kaynağı örneğini yerleştirmek için kullanılır. Her bir MeshComponent tek bir kafes 'e başvurur. Her bir alt ağı işlemek için kullanılan malzemeleri geçersiz kılabilir.

### <a name="meshcomponent-properties"></a>MeshComponent özellikleri

* **Ağ:** Bu bileşen tarafından kullanılan kafes kaynağı.

* **Malzemeler:** Kafes bileşeninin kendisinde belirtilen malzeme dizisi. Dizi, her zaman kafes kaynağındaki *malzemeler* dizisiyle aynı uzunluğa sahip olacaktır. Bu dizide varsayılan olarak geçersiz kılınamayan malzemeler, bu dizide *null* olarak ayarlanır.

* **Usedmalzemeleri:** Her alt ağ için gerçekten kullanılan malzemeler dizisi. Null olmayan değerler için, *malzemeler* dizisindeki verilerle aynı olacaktır. Aksi takdirde, kafes örneğindeki *malzemeler* dizisinden değeri içerir.

### <a name="sharing-of-meshes"></a>Kafeslerin paylaşılması

Bir `Mesh` kaynak, birden fazla kafes bileşeni örneği arasında paylaşılabilir. Ayrıca, `Mesh` bir ağ bileşenine atanan kaynak, programlama yoluyla dilediğiniz zaman değiştirilebilir. Aşağıdaki kod, bir ağı nasıl klonlanacağını göstermektedir:

```cs
Entity CloneEntityWithModel(RenderingConnection api, Entity sourceEntity)
{
    MeshComponent meshComp = sourceEntity.FindComponentOfType<MeshComponent>();
    if (meshComp != null)
    {
        Entity newEntity = api.CreateEntity();
        MeshComponent newMeshComp = api.CreateComponent(ObjectType.MeshComponent, newEntity) as MeshComponent;
        newMeshComp.Mesh = meshComp.Mesh; // share the mesh
        return newEntity;
    }
    return null;
}
```

```cpp
ApiHandle<Entity> CloneEntityWithModel(ApiHandle<RenderingConnection> api, ApiHandle<Entity> sourceEntity)
{
    if (ApiHandle<MeshComponent> meshComp = sourceEntity->FindComponentOfType<MeshComponent>())
    {
        ApiHandle<Entity> newEntity = *api->CreateEntity();
        ApiHandle<MeshComponent> newMeshComp = api->CreateComponent(ObjectType::MeshComponent, newEntity)->as<RemoteRendering::MeshComponent>();
        newMeshComp->SetMesh(meshComp->GetMesh()); // share the mesh
        return newEntity;
    }
    return nullptr;
}
```

## <a name="api-documentation"></a>API belgeleri

* [C# kafes sınıfı](/dotnet/api/microsoft.azure.remoterendering.mesh)
* [C# MeshComponent sınıfı](/dotnet/api/microsoft.azure.remoterendering.meshcomponent)
* [C++ kafes sınıfı](/cpp/api/remote-rendering/mesh)
* [C++ MeshComponent sınıfı](/cpp/api/remote-rendering/meshcomponent)


## <a name="next-steps"></a>Sonraki adımlar

* [Malzemeler](materials.md)