---
title: Unity için Remote Rendering’i ayarlama
description: Unity projesinde Azure uzaktan Işlemeyi başlatma
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594223"
---
# <a name="set-up-remote-rendering-for-unity"></a>Unity için Remote Rendering’i ayarlama

Unity 'de Azure uzaktan oluşturma 'yı (ARR) etkinleştirmek için, Unity 'ye özgü bazı yönlerden oluşan adanmış yöntemler sunuyoruz.

## <a name="startup-and-shutdown"></a>Başlatma ve kapatmadan

Uzaktan Işlemeyi başlatmak için kullanın `RemoteManagerUnity` . Bu sınıf genel ' i çağırır, `RenderingConnection` ancak sizin Için Unity 'ye özgü ayrıntıları zaten uygular. Örneğin, Unity belirli bir koordinat sistemi kullanır. Çağrılırken `RemoteManagerUnity.Initialize` , uygun kural ayarlanır. Çağrı Ayrıca, uzaktan işlenmiş içeriği görüntülemek için kullanılması gereken Unity kamerayı sağlamanızı gerektirir.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Uzaktan Işlemeyi kapatmak için çağrısı yapın `RemoteManagerStatic.ShutdownRemoteRendering()` .

Bir `RenderingSession` oluşturup, birincil işleme oturumu olarak seçildikten sonra, ile kaydolmalıdır `RemoteManagerUnity` :

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Tam örnek kod

Aşağıdaki kod, Unity 'de Azure uzaktan Işlemesini başlatmak için gereken tüm adımları göstermektedir:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Kullanışlı işlevler

### <a name="session-state-events"></a>Oturum durumu olayları

`RemoteManagerUnity.OnSessionUpdate` oturum durumu değiştiğinde olayları yayar, Ayrıntılar için kod belgelerine bakın.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity` , kurulum ve oturum yönetimini kolaylaştırmak için isteğe bağlı bir bileşendir. Uygulamanın çıkış sırasında veya yürütme modunun düzenleyicide çıkış yapıldığında oturumunu otomatik olarak durdurma ve gerektiğinde oturum kiralamasını otomatik olarak yenileme seçeneklerini içerir. Oturum özellikleri gibi verileri önbelleğe alır (kendi `LastProperties` değişkenine bakın) ve oturum durumu değişiklikleri ve oturum hataları için olayları ortaya koyar.

Tek seferde birden fazla örneği olamaz `ARRServiceUnity` . Bazı yaygın işlevleri uygulayarak daha hızlı bir şekilde başlamanıza yöneliktir. Daha büyük bir uygulama için, bu şeyleri sizin yapmanız tercih edilebilir, ancak.

Nasıl ayarlanacağı ve kullanılacağı bir örnek için `ARRServiceUnity` bkz. [öğretici: uzaktan Işlenmiş modelleri görüntüleme](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Unity için Remote Rendering paketini yükleme](install-remote-rendering-unity-package.md)
* [Öğretici: uzaktan işlenmiş modelleri görüntüleme](../../tutorials/unity/view-remote-models/view-remote-models.md)
