---
title: Tek taraflı işleme
description: Tek taraflı işleme ayarlarını ve kullanım örneklerini açıklar
author: florianborn71
ms.author: flborn
ms.date: 02/06/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: fea9deae3948b36732b5ea5203fceea6bec07fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594087"
---
# <a name="no-loc-textsingle-sided-rendering"></a>:::no-loc text="Single-sided"::: çizmeye

Çoğu işleyicilere, performansı artırmak için [arka yüz kaldırma](https://en.wikipedia.org/wiki/Back-face_culling) kullanır. Ancak, kafesler [kesme düzlemleri](cut-planes.md)açık bir şekilde kesiliyorsa, kullanıcılar genellikle üçgenlerin arka tarafına bakar. Bu üçgenler ortaya koyulur olursa sonuç, ikna edici değildir.

Bu sorunu güvenilir bir şekilde önlemenin yolu, üçgenler *çift taraflı* işlenmelidir. Arka yüz yüzey kaldırma işlemi, performans etkilerine karşı, varsayılan olarak Azure uzaktan Işleme yalnızca Kesme düzlemiyle kesişen kafesler için çift taraflı işlemeye geçiş yapar.

*:::no-loc text="single-sided"::: İşleme* ayarı bu davranışı özelleştirmenize olanak sağlar.

> [!CAUTION]
> :::no-loc text="single-sided":::İşleme ayarı deneysel bir özelliktir. Gelecekte yeniden kaldırılabileceği. Uygulamanızda önemli bir sorunu gerçekten çözmediğiniz müddetçe lütfen varsayılan ayarı değiştirmeyin.

## <a name="prerequisites"></a>Önkoşullar

:::no-loc text="single-sided":::İşleme ayarının yalnızca [](../../how-tos/conversion/configure-model-conversion.md) `opaqueMaterialDefaultSidedness` seçeneği olarak ayarlanmış olan kafesler için bir etkisi vardır `SingleSided` . Varsayılan olarak, bu seçenek olarak ayarlanır `DoubleSided` .

## <a name="no-loc-textsingle-sided-rendering-setting"></a>:::no-loc text="Single-sided"::: işleme ayarı

Üç farklı mod vardır:

**Normal:** Bu modda, kafesler dönüştürülürken her zaman oluşturulur. Bu, ayarlanmış olan kafeslerin `opaqueMaterialDefaultSidedness` `SingleSided` , kesilen bir düzlemi kesişdiklerinde bile, her zaman arka yüz yüzey kaldırma özelliği etkinleştirilmiş olarak işlendiğine yol açacaktır.

**Dynamicdoublesıding:** Bu modda, kesilen bir düzlem bir kafesden kesişdiğinde, otomatik olarak çift taraflı işlemeye geçiş yapılır. Bu mod varsayılan moddur.

**Alwaysdoubleyüzlü:** Tüm tek taraflı geometriyi her zaman çift taraflı olarak işlenecek şekilde zorlar. Bu mod, ve işleme arasındaki performans etkisini kolayca karşılaştırabilmeniz için çoğunlukla kullanıma :::no-loc text="single-sided"::: sunulur :::no-loc text="double-sided"::: .

:::no-loc text="single-sided":::Oluşturma ayarlarının değiştirilmesi aşağıdaki şekilde yapılabilir:

```cs
void ChangeSingleSidedRendering(RenderingSession session)
{
    SingleSidedSettings settings = session.Connection.SingleSidedSettings;

    // Single-sided geometry is rendered as is
    settings.Mode = SingleSidedMode.Normal;

    // Single-sided geometry is always rendered double-sided
    settings.Mode = SingleSidedMode.AlwaysDoubleSided;
}
```

```cpp
void ChangeSingleSidedRendering(ApiHandle<RenderingSession> session)
{
    ApiHandle<SingleSidedSettings> settings = session->Connection()->GetSingleSidedSettings();

    // Single-sided geometry is rendered as is
    settings->SetMode(SingleSidedMode::Normal);

    // Single-sided geometry is always rendered double-sided
    settings->SetMode(SingleSidedMode::AlwaysDoubleSided);
}
```

## <a name="api-documentation"></a>API belgeleri

* [C# RenderingConnection. SingleSidedSettings özelliği](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.singlesidedsettings)
* [C++ RenderingConnection:: SingleSidedSettings ()](/cpp/api/remote-rendering/renderingconnection#singlesidedsettings)

## <a name="next-steps"></a>Sonraki adımlar

* [Düzlemleri kesme](cut-planes.md)
* [Model dönüştürmeyi yapılandırma](../../how-tos/conversion/configure-model-conversion.md)