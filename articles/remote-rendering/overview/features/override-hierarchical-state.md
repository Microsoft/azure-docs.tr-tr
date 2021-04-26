---
title: Hiyerarşik durumu geçersiz kılma
description: Hiyerarşik durum geçersiz kılma bileşenleri kavramını açıklar.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 851a87885ac765c829e8c2be9fd1205e22906ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94445163"
---
# <a name="hierarchical-state-override"></a>Hiyerarşik durumu geçersiz kılma

Çoğu durumda, bir [modelin](../../concepts/models.md)bölümlerinin görünümünü dinamik olarak değiştirmek, örneğin alt grafikleri gizlemek veya bölümleri saydam işlemeye geçirmek gerekir. Dahil edilen her bir bölümün materyalini değiştirmek, tüm sahne grafiğinin üzerinde yineleme yapmak ve her düğümde malzeme kopyalamayı ve atamayı yönetmek gerektirdiğinden pratik değildir.

En az olası ek yük ile bu kullanım örneğini başarmak için, kullanın `HierarchicalStateOverrideComponent` . Bu bileşen, sahne grafiğinin rastgele dallarında hiyerarşik durum güncelleştirmelerini uygular. Yani, bir durum sahne grafiğinde herhangi bir düzeyde tanımlanabilir ve yeni bir durum tarafından geçersiz kılınana ya da bir yaprak nesneye uygulanana kadar hiyerarşiyi aşağı doğru ayırır.

Örnek olarak, bir otomobil modelini göz önünde bulundurun ve iç altyapı bölümü dışında tüm arabayı saydam olacak şekilde değiştirmek isteyebilirsiniz. Bu kullanım örneği, bileşenin yalnızca iki örneğini içerir:

* İlk bileşen modelin kök düğümüne atanır ve tüm otomobil için saydam işlemeyi etkinleştirir.
* İkinci bileşen, altyapının kök düğümüne atanır ve doğrudan göster modunu devre dışı bırakarak durumu yeniden geçersiz kılar.

## <a name="features"></a>Özellikler

Geçersiz kılınabilen sabit durumlar kümesi şunlardır:

* **`Hidden`**: Sahne grafiğinde ilgili kafesler gizlenir veya gösterilir.
* **`Tint color`**: İşlenmiş bir nesne, tek bir renk tonu rengi ve renk tonu ağırlığı ile renk tonlu olabilir. Aşağıdaki görüntüde, bir tekerleğin kıyısı renk gösterilmektedir.
  
  ![Bir nesneyi açmak için kullanılan renk tonu rengi](./media/color-tint.png)

* **`See-through`**: Geometri yarı saydam olarak işlenir, örneğin bir nesnenin iç parçalarını açığa çıkarmak için. Aşağıdaki görüntüde, kırmızı hızınızı düşüren Caliper dışında, bkz.-Through modunda işlenen tüm otomobil gösterilmektedir:

  ![Seçili nesneleri saydam hale getirmek için kullanılan modu göster](./media/see-through.png)

  > [!IMPORTANT]
  > Yalnızca *Tilebasedcomposition* [işleme modu](../../concepts/rendering-modes.md) kullanıldığında, diğer adım etkisi geçerlidir.

* **`Shell`**: Geometri, saydam, doygun bir kabuk olarak işlenir. Bu mod, bir sahnenin önemli olmayan bölümlerinin soluklaşulurken bir şekil ve göreli konumlandırma hissi olmaya devam ediyor. Kabuk işleme görünümünü değiştirmek için, [Shellrenderingsettings](shell-effect.md) durumunu kullanın. Araba modelinin, mavi sprinler dışında tamamen kabuk tarafından işlenmiş olması için aşağıdaki resme bakın:

  ![Belirli nesneleri soluklaştırmak için kullanılan kabuk modu](./media/shell.png)

  > [!IMPORTANT]
  > Kabuk etkisi yalnızca *Tilebasedcomposition* [işleme modu](../../concepts/rendering-modes.md) kullanıldığında işe yarar.

* **`Selected`**: Geometri bir [seçim ana hattı](outlines.md)ile işlenir.

  ![Seçili parçayı vurgulamak için kullanılan ana hat seçeneği](./media/selection-outline.png)

* **`DisableCollision`**: Geometri, [uzamsal sorgulardan](spatial-queries.md)muaf tutulur. **`Hidden`** Bayrak, çakışma durumu bayrağını etkilemez, bu nedenle bu iki bayrak genellikle birlikte ayarlanır.

* **`UseCutPlaneFilterMask`**: Kesme düzlemi seçimini denetlemek için tek bir filtre bit maskesi kullanın. Bu bayrak, tek tek filtre maskesinin kullanılması veya üst öğesinden devralınıp alınmayacağını belirler. Filtre bit maskesinin kendisi özelliği aracılığıyla ayarlanır `CutPlaneFilterMask` . Filtrelemenin nasıl çalıştığı hakkında ayrıntılı bilgi için, [Seçmeli kesme düzlemleri paragrafına](cut-planes.md#selective-cut-planes)bakın. Sahnenin geri kalanı etkilenirken yalnızca tire ve RIM kesildiğinde aşağıdaki örneğe bakın.
![Seçmeli kesme düzlemleri](./media/selective-cut-planes-hierarchical-override.png)


> [!TIP]
> Tam bir alt grafiğin görünürlük ve uzamsal sorgularını kapatmaya alternatif olarak, `enabled` bir oyun nesnesinin durumu değiştirilebilir. Bir hiyerarşi devre dışıysa, bunun üzerinde tercih vardır `HierarchicalStateOverrideComponent` .

## <a name="hierarchical-overrides"></a>Hiyerarşik geçersiz kılmalar

, `HierarchicalStateOverrideComponent` Bir nesne hiyerarşisinin birden çok düzeyine eklenebilir. Bir varlıktaki her bir türün yalnızca bir bileşeni olabilir, her biri `HierarchicalStateOverrideComponent` gizli, bkz.-Through, seçilen, renk tonu ve çarpışma için durumları yönetir.

Bu nedenle, her durum şunlardan birine ayarlanabilir:

* `ForceOn` -Bu düğüm üzerindeki ve altındaki tüm kafesde durum etkindir
* `ForceOff` -Bu düğüm üzerindeki ve altındaki tüm kafesler için durum devre dışı bırakıldı
* `InheritFromParent` -durum bu geçersiz kılma bileşeninden etkilenmemiştir

Durumları doğrudan veya işlev aracılığıyla değiştirebilirsiniz `SetState` :

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

```cpp
ApiHandle<HierarchicalStateOverrideComponent> component = ...;

// set one state directly
component->SetHiddenState(HierarchicalEnableState::ForceOn);

// or: set a state with the SetState function
component->SetState(HierarchicalStates::SeeThrough, HierarchicalEnableState::InheritFromParent);

// set multiple states at once with the SetState function
component->SetState(
    (HierarchicalStates)((int32_t)HierarchicalStates::Hidden | (int32_t)HierarchicalStates::DisableCollision), HierarchicalEnableState::ForceOff);

```

### <a name="tint-color"></a>Renk tonu rengi

`tint color`Geçersiz kılma, hem bir açık/kapalı/devralma durumu hem de bir renk tonu rengi özelliği olduğundan biraz özeldir. Renk tonu renginin alfa kısmı, renk azaltma efektinin kalınlığını tanımlar: 0,0 olarak ayarlanırsa, renk tonu rengi görünür olmaz ve 1,0 olarak ayarlandıysa nesne saf renk tonu rengiyle işlenir. -Between değerleri için, son renk, renk tonu rengiyle karışacaktır. Renkli bir animasyon elde etmek için renk tonu rengi çerçeve başına temelinde değiştirilebilir.

## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Bir örneği, `HierarchicalStateOverrideComponent` çok çalışma zamanı ek yükü eklemez. Ancak, etkin bileşenlerin sayısını düşük tutmak her zaman iyi bir uygulamadır. Örneğin, çekilen nesneyi vurgulayan bir seçim sistemi uygularken, vurgu kaldırıldığında bileşeni silmeniz önerilir. Bileşenlerin nötr özelliklerle birlikte tutulması hızlı bir şekilde eklenebilir.

Saydam işleme, sunucunun GPU 'lara standart işleme göre daha fazla iş yükü koyar. Sahne grafiğinin büyük *bölümleri, çok* sayıda geometrinin görünür olması halinde, görünen bir performans sorunu haline gelebilir. Aynı [seçim anahatları](../../overview/features/outlines.md#performance) olan nesneler ve [kabuk işleme](../../overview/features/shell-effect.md#performance) için de geçerlidir. 

## <a name="api-documentation"></a>API belgeleri

* [C# HierarchicalStateOverrideComponent sınıfı](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent)
* [C++ HierarchicalStateOverrideComponent sınıfı](/cpp/api/remote-rendering/hierarchicalstateoverridecomponent)

## <a name="next-steps"></a>Sonraki adımlar

* [Özetler](../../overview/features/outlines.md)
* [İşleme modları](../../concepts/rendering-modes.md)
* [Uzamsal sorgular](../../overview/features/spatial-queries.md)