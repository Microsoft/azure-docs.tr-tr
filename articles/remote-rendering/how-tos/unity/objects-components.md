---
title: Unity oyun nesneleri ve bileşenleri
description: Uzaktan Işleme varlıkları ve bileşenleriyle çalışmak için Unity 'ye özgü yöntemleri açıklar.
author: jakrams
ms.author: jakras
ms.date: 02/28/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 15822c357db63db81e6c1efda2467279a98d7c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594155"
---
# <a name="interact-with-unity-game-objects-and-components"></a>Unity oyun nesneleri ve bileşenleriyle etkileşim kurma

Azure uzaktan Işleme (ARR) çok sayıda nesne için iyileştirilmiştir (bkz. [sınırlamalar](../../reference/limits.md)). Konakta büyük ve karmaşık hiyerarşileri yönetmek mümkün olsa da, düşük güç kullanan cihazlarda Unity 'de tümünü çoğaltmak uygun değildir.

Bu nedenle, konakta bir model yüklendiğinde, Azure uzaktan Işleme, istemci cihazdaki model yapısıyla ilgili bilgileri yansıtır (ağ trafiği olur), ancak Unity 'deki nesneleri ve bileşenleri çoğaltmaz. Bunun yerine, gerekli Unity oyun nesneleri ve bileşenlerini el ile istemeniz gerekir, bu nedenle ek yükü gerçekten gereken şekilde sınırlayabilirsiniz. Bu şekilde, istemci tarafı performans üzerinde daha fazla denetime sahip olursunuz.

Sonuç olarak, Azure uzaktan Işlemenin Unity tümleştirmesi, uzaktan Işleme yapısını isteğe bağlı olarak çoğaltmak için ek işlevlerle birlikte gelir.

## <a name="load-a-model-in-unity"></a>Unity 'de model yükleme

Bir model yüklediğinizde, yüklenen modelin kök nesnesine bir başvuru alırsınız. Bu başvuru bir Unity oyun nesnesi değildir, ancak genişletme yöntemini kullanarak bir tane içine dönüştürebilirsiniz `Entity.GetOrCreateGameObject()` . Bu işlev türünde bir bağımsız değişken bekliyor `UnityCreationMode` . Geçirirseniz `CreateUnityComponents` , yeni oluşturulan Unity oyun nesnesi, konakta bulunan tüm uzaktan işleme bileşenleri için ara sunucu bileşenleri ile doldurulur. Bunun yapılması önerilir, ancak bunu tercih etmek için `DoNotCreateUnityComponents` En düşük düzeyde tutar.

### <a name="load-model-with-unity-coroutines"></a>Unity bağıntıları ile model yükleme

```cs
IEnumerator LoadModelWithCoroutine(RenderingSession session)
{
    float currentProgress = 0.0f;
    var task = session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"),
        (float progress) =>
        {
            currentProgress = progress;
        });

    while (!task.IsCompleted && !task.IsFaulted)
    {
        int percentage = (int)(currentProgress * 100.0f);
        yield return null;
    }

    if (!task.IsFaulted)
    {
        var gameObject = task.Result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
    }
}
```

### <a name="load-model-with-await-pattern"></a>Bekleme düzenine sahip modeli yükle

```cs
async void LoadModelWithAwait(RenderingSession session)
{
    var result = await session.Connection.LoadModelFromSasAsync(new LoadModelFromSasOptions("builtin://Engine"), null);
    var gameObject = result.Root?.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
}
```

Yukarıdaki kod örnekleri, yerleşik model yüklendiği için SAS aracılığıyla model yükleme yolunu kullandı. (Ve kullanarak), blob kapsayıcıları aracılığıyla modelin adreslenmesi `LoadModelAsync` `LoadModelOptions` tamamen analoya.

## <a name="remoteentitysyncobject"></a>RemoteEntitySyncObject

Unity oyun nesnesi oluşturma `RemoteEntitySyncObject` , oyun nesnesine örtülü olarak bir bileşen ekler. Bu bileşen, varlık dönüşümünü sunucuya eşleştirmek için kullanılır. Varsayılan olarak `RemoteEntitySyncObject` , kullanıcının `SyncToRemote()` Yerel Unity durumunu sunucuya eşitlemeyi açıkça çağırması gerekir. Etkinleştirme `SyncEveryFrame` nesneyi otomatik olarak eşitler.

' A sahip nesneler, `RemoteEntitySyncObject` uzak alt öğelerinin örneklendiği ve bu düğme aracılığıyla Unity düzenleyicisinde gösterildiği olabilir **:::no-loc text="Show children":::** .

![RemoteEntitySyncObject](media/remote-entity-sync-object.png)

## <a name="wrapper-components"></a>Sarmalayıcı bileşenleri

Uzaktan Işleme varlıklarına eklenen [Bileşenler](../../concepts/components.md) , ara sunucu 'Lar aracılığıyla Unity 'ye sunulur `MonoBehavior` . Bu proxy 'ler Unity 'deki uzak bileşeni temsil eder ve tüm değişiklikleri konağa iletir.

Proxy uzaktan Işleme bileşenleri oluşturmak için, genişletme yöntemini kullanın `GetOrCreateArrComponent` :

```cs
var cutplane = gameObject.GetOrCreateArrComponent<ARRCutPlaneComponent>(RemoteManagerUnity.CurrentSession);
```

## <a name="coupled-lifetimes"></a>Bağlanmış yaşam süreleri

Bir uzak [varlığın](../../concepts/entities.md) ve Unity oyun nesnesinin yaşam süresi bir ile bağlı oldukları sırada bağlanır `RemoteEntitySyncObject` . `UnityEngine.Object.Destroy(...)`Bu tür bir oyun nesnesiyle birlikte çağırırsanız, uzak varlık da kaldırılır.

Unity oyun nesnesini, uzak varlığı etkilemeden yok etmek için öncelikle üzerinde çağırmanız gerekir `Unbind()` `RemoteEntitySyncObject` .

Bu, tüm proxy bileşenleri için de geçerlidir. Yalnızca istemci tarafı gösterimini yok etmek için `Unbind()` önce proxy bileşenini çağırmanız gerekir:

```cs
var cutplane = gameObject.GetComponent<ARRCutPlaneComponent>();
if (cutplane != null)
{
    cutplane.Unbind();
    UnityEngine.Object.Destroy(cutplane);
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Unity için Remote Rendering’i ayarlama](unity-setup.md)
* [Öğretici: Unity 'de uzak varlıkları Işleme](../../tutorials/unity/manipulate-models/manipulate-models.md)
