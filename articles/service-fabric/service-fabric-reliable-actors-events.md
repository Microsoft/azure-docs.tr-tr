---
title: Aktör tabanlı Azure Service Fabric aktörleri içindeki olaylar
description: Aktör ve istemci arasında iletişim kurmanın etkili bir yolu olan Service Fabric Reliable Actors olayları hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 591e3539715ba0873e44f56a93d05df6552bb1ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571377"
---
# <a name="actor-events"></a>Aktör olayları
Aktör olayları, aktörden istemcilere en iyi çaba bildirimleri göndermenin bir yolunu sağlar. Aktör olayları, aktörden istemciye iletişim için tasarlanmıştır ve aktör ile aktör arasındaki iletişim için kullanılmamalıdır.

Aşağıdaki kod parçacıkları, uygulamanızda aktör olaylarının nasıl kullanılacağını gösterir.

Aktör tarafından yayınlanan olayları açıklayan bir arabirim tanımlayın. Bu arabirimin arabiriminden türetilmesi gerekir `IActorEvents` . Yöntemlerin bağımsız değişkenlerinin [seri hale getirilebilir veri anlaşması](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)olması gerekir. Olay bildirimleri tek yönlü ve en iyi çaba olduğundan Yöntemler void döndürmelidir.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Aktör arabirimindeki aktör tarafından yayınlanan olayları bildirin.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
İstemci tarafında olay işleyicisini uygulayın.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

İstemcide, olayı yayımlayan ve olaylarına abone olan aktör için bir ara sunucu oluşturun.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Yük devretme durumunda aktör, farklı bir işlem veya düğüme yük devretmeyebilir. Aktör proxy 'si, etkin abonelikleri yönetir ve otomatik olarak yeniden abone olur. API aracılığıyla yeniden abonelik aralığını kontrol edebilirsiniz `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . Aboneliğinizi kaldırmak için API 'yi kullanın `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` .

Aktör üzerinde olayları olduğu gibi yayımlayın. Olayda abone varsa, aktör çalışma zamanı bu bildirimleri gönderir.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Sonraki adımlar
* [Aktör yeniden girişi](service-fabric-reliable-actors-reentrancy.md)
* [Aktör tanılama ve performans izleme](service-fabric-reliable-actors-diagnostics.md)
* [Aktör API 'SI başvuru belgeleri](/previous-versions/azure/dn971626(v=azure.100))
* [C# örnek kodu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core örnek kodu](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java örnek kodu](https://github.com/Azure-Samples/service-fabric-java-getting-started)
