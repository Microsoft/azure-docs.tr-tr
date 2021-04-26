---
title: İşleme modları
description: Farklı sunucu tarafı işleme modlarını açıklar
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594510"
---
# <a name="rendering-modes"></a>İşleme modları

Uzaktan Işleme iki ana işlem modunu, **Tilebasedcomposition** modunu ve **depthbasedcomposition** modunu sunar. Bu modlar, iş yükünün sunucuda birden çok GPU arasında nasıl dağıtıldığını tespit edilir. Mod bağlantı zamanında belirtilmelidir ve çalışma zamanı sırasında değiştirilemez.

Her iki mod da avantajlarla ve ayrıca devralınan özellik sınırlamaları ile birlikte gelir; bu nedenle en uygun mod seçilmesi, büyük/küçük harfe özgüdür.

## <a name="modes"></a>Mod

Bu iki mod artık daha ayrıntılı bir şekilde ele alınmıştır.

### <a name="tilebasedcomposition-mode"></a>' TileBasedComposition ' modu

**Tilebasedcomposition** modunda, her ilgili GPU ekranda belirli alt dikdörtgenler (kutucuklar) işler. Ana GPU, bir video çerçevesi olarak istemciye gönderilmeden önce kutucukların son görüntüsünü oluşturur. Buna uygun olarak, tüm GPU 'Ların işleme için aynı kaynak kümesine sahip olması gerekir, bu nedenle yüklenen varlıkların tek bir GPU belleğine uyması gerekir.

Bu modda işleme kalitesi,, MSAA 'nın her GPU için tam geometri kümesi üzerinde çalışabilmesi nedeniyle **Depthbasedcomposition** modundan biraz daha iyidir. Aşağıdaki ekran görüntüsünde, düzgünleştirmenin her iki kenar da benzer şekilde düzgün şekilde çalıştığından gösterilmektedir:

![TileBasedComposition içinde MSAA](./media/service-render-mode-quality.png)

Ayrıca, bu modda her parça saydam bir malzemeye geçiş yapılabilir veya [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md) aracılığıyla **görmek** için

### <a name="depthbasedcomposition-mode"></a>' DepthBasedComposition ' modu

**Depthbasedcomposition** modunda, her ilgili GPU tam ekran çözünürlüğünde ve yalnızca kafeslerin bir alt kümesiyle işlenir. Ana GPU 'daki son görüntü kompozisyonu, parçaların, derinlemesine, derinliğine göre doğru şekilde birleştirildiğini dikkate alır. Doğal olarak, bellek yükü GPU 'larda dağıtılır ve bu sayede tek bir GPU belleğine sığmayan modellerin oluşturulmasına izin verilir.

Her tek GPU, MSAA 'yi AntiAlias yerel içeriğine kullanır. Ancak, farklı GPU 'Ların kenarları arasında devralınmış bir diğer ad olabilir. Bu efekt, son görüntünün işlenmesine karşı azalmakta, ancak MSAA kalitesi, **Tilebasedcomposition** modundan daha kötüleşmektedir.

MSAA yapıtları şu görüntüde gösterilmiştir: ![ MSAA 'da, DepthBasedComposition](./media/service-render-mode-balanced.png)

Her iki bölüm de aynı GPU 'da işlendiği için, düzgünleştirme, Sculpture ve curma arasında düzgün şekilde çalışmaktadır. Diğer taraftan, bu iki bölüm ayrı GPU 'Larda oluşturulduğundan, curle ve duvardaki kenar bazı diğer adlar gösterir.

Bu modun en büyük sınırlaması, geometri parçalarının dinamik olarak saydam malzemelere geçiş yapmayacağı veya [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)için de **geçişli** modunun çalışmamasıdır. Diğer durum geçersiz kılma özellikleri (ana hat, renk tonu,...) de çalışır, ancak. Ayrıca, dönüştürme sırasında saydam olarak işaretlenen malzemeler bu modda düzgün şekilde çalışır.

### <a name="performance"></a>Performans

Her iki mod için performans özellikleri, kullanım örneğine göre farklılık gösterir ve bu durum, genel öneriler sağlar. Yukarıda bahsedilen sınırlamalara (bellek veya saydamlık/bkz.) karşı kısıtlanmamışsa, her iki modu da denemeniz ve çeşitli kamera konumlarını kullanarak performansı izlemeniz önerilir.

## <a name="setting-the-render-mode"></a>Oluşturma modunu ayarlama

Bir uzaktan Işleme sunucusunda kullanılan işleme modu `RenderingSession.ConnectAsync` aracılığıyla belirtilir `RendererInitOptions` .

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>API belgeleri

* [C# RenderingSession. ConnectAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [C# RendererInitOptions yapısı](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession:: ConnectToConnectAsyncRuntime ()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [C++ RendererInitOptions yapısı](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>Sonraki adımlar

* [Oturumlar](../concepts/sessions.md)
* [Hiyerarşik durum geçersiz kılma bileşeni](../overview/features/override-hierarchical-state.md)