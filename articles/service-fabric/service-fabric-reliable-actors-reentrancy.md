---
title: Azure Service Fabric aktörleri için yeniden giriş
description: Çağrı bağlamına göre engellemeyi mantıksal olarak önlemenin bir yolu olan Service Fabric Reliable Actors için yeniden giriş yapın.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 90a8e9146d18d628fbf124307f1b79b912c9cef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789389"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors yeniden giriş
Reliable Actors çalışma zamanı, varsayılan olarak mantıksal çağrı bağlamı tabanlı yeniden giriş yapılmasına izin verir. Bu, aktörlerin aynı çağrı bağlamı zincirinde olmaları durumunda yer almasına izin verir. Örneğin, oyuncu aktör C 'ye bir ileti gönderen aktör B 'ye bir ileti gönderir. İleti işlemenin bir parçası olarak, aktör C aktör A 'yı çağırırsa ileti yeniden alınır, bu nedenle izin verilir. Farklı bir çağrı bağlamının parçası olan diğer tüm iletiler, işleme bitene kadar aktör A 'da engellenir.

Sabit listesinde bir aktör yeniden girişi için kullanılabilen iki seçenek vardır `ActorReentrancyMode` :

* `LogicalCallContext` (varsayılan davranış)
* `Disallowed` -yeniden girişi devre dışı bırakır

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Yeniden giriş, kayıt sırasında bir ' ın `ActorService` ayarlarından yapılandırılabilir. Ayar, aktör hizmetinde oluşturulan tüm aktör örneklerine uygulanır.

Aşağıdaki örnek, yeniden giriş modunu olarak ayarlayan bir aktör hizmetini gösterir `ActorReentrancyMode.Disallowed` . Bu durumda, bir aktör başka bir aktöre bir yeniden gelen iletisi gönderirse, türünde bir özel durum `FabricException` oluşturulur.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Sonraki adımlar
* [Aktör API 'si başvuru belgelerinde](/dotnet/api/microsoft.servicefabric.actors)yeniden giriş hakkında daha fazla bilgi edinin
