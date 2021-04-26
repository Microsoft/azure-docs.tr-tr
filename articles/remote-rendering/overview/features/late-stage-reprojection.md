---
title: Geç aşama yeniden projeksiyonu
description: Geç aşama yeniden projeksiyonu ve nasıl kullanılacağı hakkında bilgi.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: f0951415bba22a226dadb7f2a115cede451399bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205651"
---
# <a name="late-stage-reprojection"></a>Geç aşama yeniden projeksiyonu

*Geç aşama yeniden projeksiyonu* (LSR), Kullanıcı taşırken hologragram sabitlemeye yardımcı olan bir donanım özelliğidir.

Statik modellerin, çevresinde taşırken konumlarını görsel olarak korumaları beklenir. Bu davranış kararsız şekilde görünüyorsa, LSR sorunları hakkında ipucu olabilir. Animasyonlar veya açılım görünümleri gibi ek dinamik dönüşümlerinin bu davranışı maskeleyebilir olduğunu unutmayın.

İki farklı LSR modu arasından seçim yapabilirsiniz, yani **planar LSR** veya **derinlik LSR**. Etkin olanı, istemci uygulamanın bir derinlik arabelleği göndermediği tarafından belirlenir.

Her iki LSR modu, farklı sınırlamalara sahip olsa da hologram kararlılığını geliştirir. Çoğu durumda daha iyi sonuçlar elde etmek için derinlik LSR 'ı deneyerek başlayın.

## <a name="choose-lsr-mode-in-unity"></a>Unity 'de LSR modunu seçme

Unity düzenleyicisinde öğesine gidin *:::no-loc text="File > Build Settings":::* . *:::no-loc text="Player Settings":::* Sol alt köşedeki ' ı seçin, sonra da *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* işaretli olup olmadığını kontrol edin **:::no-loc text="Enable Depth Buffer Sharing":::** :

![Derinlik arabelleği paylaşımı etkin bayrağı](./media/unity-depth-buffer-sharing-enabled.png)

Bu durumda, uygulamanız derinlik LSR kullanır, aksi takdirde planar LSR kullanır.

## <a name="depth-lsr"></a>Derinlik LSR

Derinlik LSR 'nin çalışması için, istemci uygulamanın LSR sırasında göz önünde bulundurulması gereken tüm geometriyi içeren geçerli bir derinlik arabelleği sağlaması gerekir.

Derinlik LSR, sağlanan derinlik arabelleğinin içeriğine göre video çerçevesini sabitkurmaya çalışır. Sonuç olarak, saydam nesneler gibi, kendisine işlenen içerik LSR tarafından ayarlanamaz ve kararsızlık ve yeniden projeksiyon yapıtları gösterilebilir. 

Saydam nesneler için yeniden projeksiyonu kararlaşmasını azaltmak için derinlik arabelleği yazmayı zorlayabilirsiniz. [Renk](color-materials.md) ve [PBR](pbr-materials.md) malzemeleri için malzeme bayrağına *TransparencyWritesDepth* bakın. Ancak, bu bayrak etkinleştirildiğinde saydam/donuk nesne etkileşiminin görsel kalitesi düşebilir.

## <a name="planar-lsr"></a>Planar LSR

Planar LSR, derinlik LSR gibi piksel başına derinlik bilgisine sahip değildir. Bunun yerine, her bir çerçeveyi sağlamanız gereken bir düzlemi temel alarak tüm içeriği yeniden projelerini sağlar.

Planar LSR, sağlanan düzleme yakın olan nesneleri en iyi şekilde yeniden projeler. Bir nesne daha fazla olduğunda, daha fazla kararsız hale gelir. Derinlik LSR, farklı derinlikler üzerinde yeniden yansıtılarken daha iyi olsa da, Planar LSR, içerik hizalamak için bir düzlemde daha iyi çalışabilir.

### <a name="configure-planar-lsr-in-unity"></a>Unity 'de planar LSR yapılandırma

Düzlem parametreleri, her kareyi ile sağlamanız gereken *odak noktası* olarak adlandırılan bir öğesinden türetilir `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . Ayrıntılar için bkz. [Unity Focus POINT API](/windows/mixed-reality/focus-point-in-unity) . Bir odak noktası ayarlamazsanız, sizin için bir geri dönüş seçilir. Ancak otomatik geri dönüş, genellikle sonuçları daha iyi halleyen sonuçlara yol açar.

Odak noktasını kendiniz hesaplayabilirsiniz, ancak bu işlem, uzak Işleme ana bilgisayarı tarafından hesaplanabilecek şekilde mantıklı olabilir. Bunu `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` elde etmek için çağırın. Odak noktasını ifade etmek için bir koordinat çerçevesi sağlamanız istenir. Çoğu durumda, sonucu yalnızca buradan sağlamak isteyeceksiniz `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` .

Genellikle hem istemci hem de konak oluşturma içeriği diğer tarafın, istemci üzerindeki UI öğeleri gibi farkında değildir. Bu nedenle, uzak odak noktasını yerel olarak hesaplanan bir şekilde birleştirmek mantıklı olabilir.

Art arda iki karede hesaplanan odak noktaları oldukça farklı olabilir. Bunları olduğu gibi kullanmak, bu yana atlama gibi görünen hologramlar için yol açabilir. Bu davranışı engellemek için, önceki ve geçerli odak noktaları arasına enterpolasyon önerilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Sunucu tarafı performans sorguları](performance-queries.md)