---
title: Düzlemleri kesme
description: Kesilen düzlemleri ve bunların nasıl kullanılacağını açıklar.
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: b3348e5a999b507aa0d286528970beb0e03f26cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594381"
---
# <a name="cut-planes"></a>Düzlemleri kesme

*Kesilen düzlem* , sanal düzlemin bir tarafında bulunan ve [kafeslerin](../../concepts/meshes.md)içini gösteren bir görsel özelliktir.
Aşağıdaki görüntüde etkisi gösterilmektedir. Sol, sağ taraftaki orijinal kafesi gösterdiği gibi, kafesin içinde görünür:

![Düzlemi kes](./media/cutplane-1.png)

## <a name="cutplanecomponent"></a>CutPlaneComponent

Bir *CutPlaneComponent* oluşturarak sahneye kesme düzlemi eklersiniz. Düzlemin konumu ve yönü, bileşenin sahip [varlığına](../../concepts/entities.md)göre belirlenir.

```cs
void CreateCutPlane(RenderingSession session, Entity ownerEntity)
{
    CutPlaneComponent cutPlane = (CutPlaneComponent)session.Connection.CreateComponent(ObjectType.CutPlaneComponent, ownerEntity);
    cutPlane.Normal = Axis.X; // normal points along the positive x-axis of the owner object's orientation
    cutPlane.FadeColor = new Color4Ub(255, 0, 0, 128); // fade to 50% red
    cutPlane.FadeLength = 0.05f; // gradient width: 5cm
}
```

```cpp
void CreateCutPlane(ApiHandle<RenderingSession> session, ApiHandle<Entity> ownerEntity)
{
    ApiHandle<CutPlaneComponent> cutPlane = session->Connection()->CreateComponent(ObjectType::CutPlaneComponent, ownerEntity)->as<CutPlaneComponent>();;
    cutPlane->SetNormal(Axis::X); // normal points along the positive x-axis of the owner object's orientation
    Color4Ub fadeColor;
    fadeColor.channels = { 255, 0, 0, 128 }; // fade to 50% red
    cutPlane->SetFadeColor(fadeColor);
    cutPlane->SetFadeLength(0.05f); // gradient width: 5cm
}
```

### <a name="cutplanecomponent-properties"></a>CutPlaneComponent özellikleri

Aşağıdaki özellikler kesilmiş bir düzlem bileşeni üzerinde kullanıma sunuldu:

* `Enabled`: Bileşeni devre dışı bırakarak, geçici olarak kesme düzlemleri kapatabilirsiniz. Devre dışı bırakılan kesme düzlemleri işleme ek yüküne neden olmaz ve ayrıca genel kesme düzeyi sınırına göre sayılmaz.

* `Normal`: Düzlem için normal olarak kullanılacak yönü (+ X,-X, + Y,-Y, + Z,-Z) belirtir. Bu yön, sahip varlığın yönüne göredir. Tam yerleştirme için sahip varlığını taşıyın ve döndürün.

* `FadeColor` ve `FadeLength` :

  *FadeColor* Alpha değeri sıfır değilse, kesme düzlemine yakın olan pikseller FADECOLOR 'in RGB bölümüne doğru bir şekilde görünür. Alfa kanalının kuvveti, silinme rengine tamamen veya kısmen doğru bir şekilde mi solacağını belirler. *FadeLength* , bu belirme mesafesinin ne kadar olacağını tanımlar.

* `ObjectFilterMask`: Kesme düzleminden etkilenen geometriyi belirleyen bir filtre bit maskesi. Ayrıntılı bilgi için bkz. sonraki paragraf.

### <a name="selective-cut-planes"></a>Seçmeli kesme düzlemleri

Yalnızca belirli geometriyi etkileyecek şekilde ayrı ayrı düzlemleri yapılandırmak mümkündür. Aşağıdaki resimde bu kurulumun uygulamada nasıl görünebileceği gösterilmektedir:

![Seçmeli kesme düzlemleri](./media/selective-cut-planes.png)

Filtreleme, kesilmiş düzlem tarafındaki bir bit maskesi ve geometri üzerinde ayarlanan ikinci bir bit maskesi arasında **mantıksal bit maskesi karşılaştırması** aracılığıyla işe yarar. Maskeler arasındaki mantıksal bir işlemin sonucu `AND` sıfır değilse, kesilen düzlem geometriyi etkiler.

* Kesim düzeyi bileşenindeki bit maskesi, özelliği ile ayarlanır `ObjectFilterMask`
* Geometri alt hiyerarşisindeki bit maskesi [HierarchicalStateOverrideComponent](override-hierarchical-state.md#features) aracılığıyla ayarlanır.

Örnekler:

| Düzlem filtresi maskesini kes | Geometri filtresi maskesi  | Mantıksal bir sonuç `AND` | Kesme düzlemi geometriyi etkiler mi?  |
|--------------------|-------------------|-------------------|:----------------------------:|
| (0000 0001) = = 1   | (0000 0001) = = 1  | (0000 0001) = = 1  | Yes |
| (1111 0000) = = 240 | (0001 0001) = = 17 | (0001 0000) = = 16 | Yes |
| (0000 0001) = = 1   | (0000 0010) = = 2  | (0000 0000) = = 0  | No |
| (0000 0011) = = 3   | (0000 1000) = = 8  | (0000 0000) = = 0  | No |

>[!TIP]
> Kesme düzlemi 0 ' a ayarlandığında `ObjectFilterMask` , mantıksal değer hiçbir şekilde null olmadığı için hiçbir geometriyi etkilemez `AND` . İşleme sistemi bu düzlemleri ilk yerinde kabul etmeyecek, bu yüzden tek tek kesme düzlemleri devre dışı bırakmak için basit bir yöntemdir. Bu kesilen düzlemler da 8 etkin düzlemler sınırına göre sayılmaz.

## <a name="limitations"></a>Sınırlamalar

* Azure uzaktan Işleme **, en fazla sekiz etkin kesme düzlemleri** destekler. Daha fazla kesme bileşeni oluşturabilirsiniz, ancak aynı anda daha fazlasını etkinleştirmeyi denerseniz, etkinleştirme yok sayılacak. Hangi bileşenlerin sahneyi etkileyeceğini değiştirmek istiyorsanız, önce diğer düzlemleri devre dışı bırakın.
* Kesin olmayan düzler tamamen görsel bir özelliktir, [uzamsal sorguların](spatial-queries.md)sonucunu etkilemez. Bir kes açık bir ağ içine ışın dönüştürmek istiyorsanız, ışın başlangıç noktasını kes düzlede olacak şekilde ayarlayabilirsiniz. Bu şekilde, ışın yalnızca görünür bölümler alabilir.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Her etkin kesilen düzlem, işleme sırasında küçük bir maliyet doğurur. Gerekli olmadığında kesilen düzlemleri devre dışı bırakın veya silin.

## <a name="api-documentation"></a>API belgeleri

* [C# CutPlaneComponent sınıfı](/dotnet/api/microsoft.azure.remoterendering.cutplanecomponent)
* [C++ CutPlaneComponent sınıfı](/cpp/api/remote-rendering/cutplanecomponent)

## <a name="next-steps"></a>Sonraki adımlar

* [Tek taraflı işleme](single-sided-rendering.md)
* [Uzamsal sorgular](spatial-queries.md)