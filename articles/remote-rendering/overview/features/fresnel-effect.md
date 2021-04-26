---
title: Fresnel etkisi
description: Fresnel düşüşünü malzemesi efektinin Özellik açıklaması sayfası
author: jumeder
ms.author: jumeder
ms.date: 11/09/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f63cd3b50642c3cf531387b4446992b6f15116f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594121"
---
# <a name="fresnel-effect"></a>Fresnel etkisi

Fresnel düşüşünü etkisi malzeme özelliği, fiziksel olmayan doğru, geçici bir etkiye sahiptir. Özelliği, nesnelerin bu açılarda daha fazla yansıtıcı hale gelmesine yönelik fiziksel gözlemye dayanır. Fresnel düşüşünü yansıtılıance, Azure uzaktan Işlemede kullanılan [PBR malzeme modelinde](../../overview/features/pbr-materials.md) zaten fiziksel olarak eklenmiştir. Buna karşılık, Fresnel düşüşünü etkisi malzeme özelliği, [ışıklar](../../overview/features/lights.md) veya [gök ortamı](../../overview/features/sky.md)bağımlılığı olmadan yalnızca bir eklenebilir renk efektine sahiptir.

Fresnel düşüşünü efekti etkilenen nesnelere kenarları etrafında renkli bir renk ışığı verir. Efekt özelleştirme ve işleme sonuçlarının örnekleri hakkında bilgiler aşağıdaki bölümlerde bulunabilir.

## <a name="enabling-the-fresnel-effect"></a>Fresnel düşüşünü efektini etkinleştirme

Fresnel düşüşünü efekt özelliğini kullanmak için, söz konusu malzemelerde etkin olması gerekir. Bu özelliği, [PBR malzemelerde](../../overview/features/pbr-materials.md) [Pbrmaterialfeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures) FresnelEffect bitini ayarlayarak etkinleştirebilirsiniz. Aynı model [Colormaterialfeatures](/dotnet/api/microsoft.azure.remoterendering) ve [renk malzemesi](../../overview/features/color-materials.md)için de geçerlidir. Kullanım gösterimi için kod örnekleri bölümüne bakın.

Etkinleştirildikten sonra, Fresnel düşüşünü efekti hemen görünür olur. Varsayılan olarak, boşluk beyaz (1, 1, 1, 1) olur ve 1 üssün oluşur. Aşağıdaki parametre ayarlayıcıları ' nı kullanarak bu ayarları özelleştirebilirsiniz.

## <a name="customizing-the-effect-appearance"></a>Efekt görünümünü özelleştirme

Şu anda, Fresnel düşüşünü etkisi şu özellikler kullanılarak her malzeme için özelleştirilebilir:

| Malzeme özelliği | Tür | Açıklama |
|-------------------|------|-------------|
| FresnelEffectColor | Color4 | Fresnel düşüşünü ışığı olarak en fazla eklenen renk. Alfa kanalı şu anda yok sayıldı. |
| FresnelEffectExponent | float | Fresnel düşüşünü ışığı yayılıyor. 0,01 (tüm nesnenin üzerine yayılmış) sayısını 10 ' a (yalnızca en önemli açıların) göre değişir. |

Uygulamada, farklı renk ve üs ayarları şöyle görünür:

![Fresnel düşüşünü etkisi örnekleri](./media/fresnel-effect-examples.png)

Fresnel düşüşünü efektinin üs değeri, her renk satırı için 1 ile 10 arasında aşamalı olarak artar. Bu durumda, Fresnel düşüşünü görünü, görüntülenen nesnelerin kenarlarına aşamalı olarak çeker. Aşağıdaki örnekte görebileceğiniz gibi, Fresnel düşüşünü efekti de saydamlıkla etkilenmemiştir:

![Fresnel düşüşünü efekt saydamlığı örnekleri](./media/fresnel-effect-transparent-examples.png)

Gösterildiği gibi, köşegen üzerindeki nesneler tamamen saydamdır, ancak Fresnel düşüşünü görünler kalır. Bu efekt, bu ekran görüntülerinde de bulunan fiziksel olarak temel Fresnel düşüşünü taklit eder.

## <a name="code-samples"></a>Kod örnekleri

Aşağıdaki kod örnekleri, hem bir [PBR malzemesi](../../overview/features/pbr-materials.md) hem de bir [renk malzemesi](../../overview/features/color-materials.md)için Fresnel düşüşünü efektini etkinleştirme ve özelleştirmeyi göstermektedir:

```cs
    void SetFresnelEffect(RenderingSession session, Material material)
    {
        if (material.MaterialSubType == MaterialType.Pbr)
        {
            var pbrMaterial = material as PbrMaterial;
            pbrMaterial.PbrFlags |= PbrMaterialFeatures.FresnelEffect;
            pbrMaterial.FresnelEffectColor = new Color4(1.0f, 0.5f, 0.1f, 1.0f);
            pbrMaterial.FresnelEffectExponent = 3.141592f;
        }
        else if (material.MaterialSubType == MaterialType.Color)
        {
            var colorMaterial = material as ColorMaterial;
            colorMaterial.ColorFlags |= ColorMaterialFeatures.FresnelEffect;
            colorMaterial.FresnelEffectColor = new Color4(0.25f, 1.0f, 0.25f, 1.0f);
            colorMaterial.FresnelEffectExponent = 7.654321f;
        }
    }
```

```cpp
void SetFresnelEffect(ApiHandle<RenderingSession> session, ApiHandle<Material> material)
{
    if (material->GetMaterialSubType() == MaterialType::Pbr)
    {
        auto pbrMaterial = material.as<PbrMaterial>();
        auto featureFlags = PbrMaterialFeatures((int32_t)pbrMaterial->GetPbrFlags() | (int32_t)PbrMaterialFeatures::FresnelEffect);
        pbrMaterial->SetPbrFlags(featureFlags);
        pbrMaterial->SetFresnelEffectColor(Color4{ 1.f, 0.5f, 0.1f, 1.f });
        pbrMaterial->SetFresnelEffectExponent(3.141592f);

    }
    else if (material->GetMaterialSubType() == MaterialType::Color)
    {
        auto colorMaterial = material.as<ColorMaterial>();
        auto featureFlags = ColorMaterialFeatures((int32_t)colorMaterial->GetColorFlags() | (int32_t)ColorMaterialFeatures::FresnelEffect);
        colorMaterial->SetColorFlags(featureFlags);
        colorMaterial->SetFresnelEffectColor(Color4{ 0.25f, 1.f, 0.25f, 1.f });
        colorMaterial->SetFresnelEffectExponent(7.654321f);
    }
}
```

## <a name="api-documentation"></a>API belgeleri

* [C# PbrMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.pbrmaterialfeatures)
* [C++ PbrMaterialFeatures](/cpp/api/remote-rendering/pbrmaterialfeatures)
* [C# ColorMaterialFeatures](/dotnet/api/microsoft.azure.remoterendering.colormaterialfeatures)
* [C++ ColorMaterialFeatures](/cpp/api/remote-rendering/colormaterialfeatures)

## <a name="next-steps"></a>Sonraki adımlar

* [Malzemeler](../../concepts/materials.md)
* [PBR malzemeleri](../../overview/features/pbr-materials.md)
* [Renk malzemeleri](../../overview/features/color-materials.md)