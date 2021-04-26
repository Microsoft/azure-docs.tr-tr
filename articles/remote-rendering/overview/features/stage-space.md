---
title: Aşama alanı
description: Aşama alanı ayarlarını ve kullanım örneklerini açıklar
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594019"
---
# <a name="stage-space"></a>Aşama alanı

HoloLens 2 gibi baş izleme verileri sağlayan bir cihazda ARR çalıştırırken, baş poz hem kullanıcı uygulamasına hem de sunucusuna gönderilir. Baş dönüştürmesinin tanımlandığı alana, *aşama alanı* denir.

Yerel ve uzak içeriği hizalamak için, aşama alanının ve dünya alanının hem istemci hem de sunucu üzerinde aynı olduğu varsayılır. Kullanıcı, kameranın üzerine ek bir dönüşüm eklemeye karar verirse, bu kullanıcının sunucuya gönderilmesi ve yerel ve uzak içeriği doğru şekilde hizalanması gerekir.

Aşama alanının taşınmasının tipik nedenleri [Dünya çapında kilitleme araçları](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) veya diğer gerçek dünya sabitleme teknikleridir ve bir sanal karakteri VR 'de taşımaktır.

## <a name="stage-space-settings"></a>Aşama alanı ayarları

> [!CAUTION]
> Deneysel: Bu özellik deneysel olur ve zaman içinde değişecektir. Bu nedenle, daha yeni istemci SDK sürümlerine güncelleştirme, kodu yükseltmek için ek iş gerektirebilir. Geçerli uygulama, aşama alanı başlangıcını değiştirirken kısa bir süre için yerel/uzak içerik hizalamasını keser.
Bu nedenle, şu anda yalnızca zaman içinde yalnızca çok küçük değişiklikler gösteren Çıpaları gibi dünya kilitleme amaçları için kullanılmak üzere tasarlanmıştır.

Sunucuya ek bir dönüşüm uygulandığını, bir konum tarafından tanımlanan kaynağı ve dünya alanındaki bir döndürme 'nin üzerinden gönderilmesi gerektiğini bildirmek için. Bu ayara, *aşama alanı ayarı* aracılığıyla erişilebilir.

> [!IMPORTANT]
> [Masaüstü benzetimi](../../concepts/graphics-bindings.md) ' nde, kameranın dünya alanı konumu Kullanıcı uygulaması tarafından sağlanır. Bu durumda, zaten kamera dönüşümüyle çarpıldığı için aşama alanı başlangıcını ayarlamak atlanmalıdır.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Unity aşama alanı betiği

Unity tümleştirmesi, kameranın üst oyun nesnesine eklenebilecek bir **Stagessize** adlı bir betik sağlar. Bu komut dosyası başlatıldıktan sonra, aşama alanı başlangıcını ayarlama konusunda dikkatli olunacaktır.

## <a name="next-steps"></a>Sonraki adımlar

* [Grafik bağlama](../../concepts/graphics-bindings.md)
* [Geç aşama yeniden projeksiyonu](late-stage-reprojection.md)
