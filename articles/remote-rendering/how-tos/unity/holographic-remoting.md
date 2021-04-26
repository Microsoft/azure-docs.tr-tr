---
title: Unity’de Holographic Remoting’i ve Remote Rendering’i kullanma
description: Holographic Remoting Preview 'ın Azure uzaktan Işleme ile birlikte nasıl kullanılabileceği
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: bd69710b6e4404a76d3cca385b6c07ea7c1f3f5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "92201826"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Unity’de Holographic Remoting’i ve Remote Rendering’i kullanma

[Holographic Remoting](/windows/mixed-reality/holographic-remoting-player) ve Azure uzaktan işleme, bir uygulama içinde birbirini dışlıyor. Bu nedenle, [Unity oynatma modu](/windows/mixed-reality/unity-play-mode) da kullanılamaz.

Unity düzenleyicisinin her çalışması için, bunlardan yalnızca biri kullanılabilir. Diğerini kullanmak için önce Unity 'yi yeniden başlatın.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>HoloLens 2 ' de önizleme yapmak için Unity Play modunu kullanma

 Unity oynatma modu, örneğin uygulamanın kullanıcı arabirimini test etmek için hala kullanılabilir. Ancak, ARR 'nin hiç başlatılmaması çok önemlidir. Aksi takdirde kilitlenme olur.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Masaüstünde önizleme yapmak için bir WMR VR kulaklık kullanın

Windows Mixed Reality VR kulaklığı varsa, Unity içinde önizleme yapmak için kullanılabilir. Bu durumda, ARR 'yi başlatmak iyi bir durumdur, ancak WMR kulaklık kullanıldığı sırada bir oturuma bağlanabilmek mümkün olmayacaktır.