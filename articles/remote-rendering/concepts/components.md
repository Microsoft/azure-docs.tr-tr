---
title: Bileşenler
description: Azure uzaktan Işleme kapsamındaki bileşenlerin tanımı
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 9679be03c69090a0c11d007cfc542bae70bd3cbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99592203"
---
# <a name="components"></a>Bileşenler

Azure uzaktan Işleme, [varlık bileşeni sistem](https://en.wikipedia.org/wiki/Entity_component_system) modelini kullanır. [Varlıklar](entities.md) , nesnelerin konumunu ve hiyerarşik birleşimini temsil ederken, davranış uygulamaktan bileşenler sorumludur.

En sık kullanılan bileşen türleri [:::no-loc text="mesh components":::](meshes.md) , işleme işlem hattına kafesler ekler. Benzer şekilde, açık kafesler eklemek için aydınlatma ve [kesilen düzlem bileşenleri](../overview/features/cut-planes.md) eklemek için de [hafif bileşenler](../overview/features/lights.md) kullanılır.

Tüm bu bileşenler, bağlı oldukları varlığın başvuru noktası olarak dönüşümünü (konum, döndürme, ölçek) kullanır.

## <a name="working-with-components"></a>Bileşenlerle çalışma

Programlı olarak bileşenleri kolayca ekleyebilir, kaldırabilir ve yönetebilirsiniz:

```cs
// create a point light component
RenderingSession session = GetCurrentlyConnectedSession();
PointLightComponent lightComponent = session.Connection.CreateComponent(ObjectType.PointLightComponent, ownerEntity) as PointLightComponent;

lightComponent.Color = new Color4Ub(255, 150, 20, 255);
lightComponent.Intensity = 11;

// ...

// destroy the component
lightComponent.Destroy();
lightComponent = null;
```

```cpp
// create a point light component
ApiHandle<RenderingSession> session = GetCurrentlyConnectedSession();

ApiHandle<PointLightComponent> lightComponent = session->Connection()->CreateComponent(ObjectType::PointLightComponent, ownerEntity)->as<PointLightComponent>();

// ...

// destroy the component
lightComponent->Destroy();
lightComponent = nullptr;
```

Bir bileşen, oluşturma zamanında bir varlığa eklenir. Daha sonra başka bir varlığa taşınamaz. Bileşenler `Component.Destroy()` , bileşenin sahip varlığı yok edildiğinde açıkça veya otomatik olarak silinir.

Her bir bileşen türünün yalnızca bir örneği bir varlığa bir seferde eklenebilir.

## <a name="unity-specific"></a>Unity 'ye özgü

Unity tümleştirmesinde bileşenlerle etkileşim kurmak için ek uzantı işlevleri bulunur. Bkz. [Unity oyun nesneleri ve bileşenleri](../how-tos/unity/objects-components.md).

## <a name="api-documentation"></a>API belgeleri

* [C# ComponentBase](/dotnet/api/microsoft.azure.remoterendering.componentbase)
* [C# RenderingConnection. CreateComponent ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.createcomponent)
* [C# Entity. Findbileşen Entoftype ()](/dotnet/api/microsoft.azure.remoterendering.entity.findcomponentoftype)
* [C++ ComponentBase](/cpp/api/remote-rendering/componentbase)
* [C++ RenderingConnection:: CreateComponent ()](/cpp/api/remote-rendering/renderingconnection#createcomponent)
* [C++ varlığı:: Findbileşen Entoftype ()](/cpp/api/remote-rendering/entity#findcomponentoftype)

## <a name="next-steps"></a>Sonraki adımlar

* [Nesne sınırları](object-bounds.md)
* [Ağ yapıları](meshes.md)