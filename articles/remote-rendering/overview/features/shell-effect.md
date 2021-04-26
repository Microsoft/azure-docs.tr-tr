---
title: Kabuk işleme
description: Kabuk işleme efektinin nasıl kullanılacağını açıklar
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594172"
---
# <a name="shell-rendering"></a>Kabuk işleme

[Hiyerarşik durum geçersiz kılma bileşeninin](../../overview/features/override-hierarchical-state.md) kabuk durumu bir saydamlık efektidir. İşleme özelliğinin aksine, nesnelerin yalnızca en önde gelen katmanı görünür ve [donuk işleme kaydedilir](../../overview/features/override-hierarchical-state.md) . Ayrıca, nesnelerin normal görünümü, kabuklar olarak işlendiğinde değiştirilebilir. Bu efekt, kullanıcının görsel olarak önemli olmayan bölümlerden, tüm sahnenin uzamsal tanımayı sürdürmeye devam etmesi gerektiği durumlarda kullanım örneklerine yöneliktir.

Kabuk tarafından işlenen nesnelerin görünümünü `ShellRenderingSettings` genel durum aracılığıyla yapılandırabilirsiniz. Kabuk oluşturma kullanan tüm nesneler aynı ayarı kullanacaktır. Nesne başına parametre yok.

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings parametreleri

Sınıf `ShellRenderingSettings` , genel kabuk işleme özellikleriyle ilgili ayarları tutar:

| Parametre      | Tür    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | 0 (yok doygunluğu yok) ile 1 (tam doygunluğa) aralığı içindeki olağan son nesne rengine uygulanacak doygunluğu kaldırma miktarı |
| `Opacity`      | float   | Kabukta işlenen nesnelerin, 0 (görünmez) ile 1 (tamamen donuk) aralığında geçirgenliği |

Ayrıca, tüm sahneye uygulandığında parametrelerin etkileri örnekleri için aşağıdaki tabloya bakın:

|                | 0 | 0.25 | 0.5 | 0,75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Doygunluğu kaldırma** | ![Doygunluğu kaldırma-0,0](./media/shell-desaturation-00.png) | ![Doygunluğu kaldırma-0,25](./media/shell-desaturation-025.png) | ![Doygunluğu kaldırma-0,5](./media/shell-desaturation-05.png) | ![Doygunluğu kaldırma-0,75](./media/shell-desaturation-075.png) | ![Doygunluğu kaldırma-1,0](./media/shell-desaturation-10.png) |
| **Opaklık**      | ![Opaklık-0,0](./media/shell-opacity-00.png) | ![Opaklık-0,25](./media/shell-opacity-025.png) | ![Opaklık-0,5](./media/shell-opacity-05.png) | ![Opaklık-0,75](./media/shell-opacity-075.png) | ![Opaklık-1,0](./media/shell-opacity-10.png) |

Kabuk etkisi, görünümün başka bir şekilde işlenebilmesi için son opak renge uygulanır. Bu, [renk tonu hiyerarşik durum geçersiz kılmayı](../../overview/features/override-hierarchical-state.md)içerir.

## <a name="example"></a>Örnek

Aşağıdaki kod, API aracılığıyla durumun örnek kullanımını gösterir `ShellRenderingSettings` :

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>Performans

Kabuk işleme özelliği, standart donuk işleme ile karşılaştırıldığında küçük bir sabit ek yük taşır. Nesneler üzerinde saydam malzemeler kullanmaktan veya görüntüleme [aşamasından](../../overview/features/override-hierarchical-state.md) daha hızlı bir şekilde daha hızlıdır. Sahnenin yalnızca bir bölümü kabukta işleme dönüştürüldüğünde performans daha kuvvetli düşebilir. Bu azalma, işleme gereken ek olarak ortaya çıkan nesneler nedeniyle ortaya çıkabilir. Bu şekilde, performans, [düzlemleri kes](../../overview/features/cut-planes.md) özelliğine benzer şekilde davranır.

## <a name="next-steps"></a>Sonraki adımlar

* [Hiyerarşik durum geçersiz kılma bileşeni](../../overview/features/override-hierarchical-state.md)