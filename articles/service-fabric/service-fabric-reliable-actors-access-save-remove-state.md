---
title: Azure Service Fabric durumunu yönetme
description: Azure Service Fabric güvenilir aktör için durum erişimi, kaydetme ve kaldırma hakkında bilgi edinin ve bir uygulama tasarlarken dikkat edilecek noktalar.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571326"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Reliable Actors duruma erişin, kaydedin ve kaldırın
[Reliable Actors](service-fabric-reliable-actors-introduction.md) hem mantığı hem de durumu kapsüllemek ve durumu güvenilir bir şekilde korumak için tek iş parçacıklı nesnelerdir. Her aktör örneğinin kendi [Durum Yöneticisi](service-fabric-reliable-actors-state-management.md)vardır: anahtar/değer çiftlerini güvenilir bir şekilde depolayan sözlük benzeri bir veri yapısı. Durum Yöneticisi, bir durum sağlayıcısının etrafındaki bir sarmalayıcıdır. Bu uygulamayı, hangi [Kalıcılık ayarının](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) kullanıldığı bağımsız olarak verileri depolamak için kullanabilirsiniz.

Durum Yöneticisi anahtarları dize olmalıdır. Değerler geneldir ve özel türler dahil olmak üzere herhangi bir tür olabilir. Durum Yöneticisi 'nde depolanan değerler, çoğaltma sırasında ağ üzerinden diğer düğümlere iletilebilecek ve bir aktörün durum kalıcılığı ayarına bağlı olarak diske yazılabileceği için veri anlaşması seri hale getirilebilir olmalıdır.

Durum Yöneticisi, güvenilir sözlükte bulunanlara benzer şekilde durum yönetimi için ortak Sözlük yöntemleri sunar.

Daha fazla bilgi için bkz. [aktör durumunu yönetirken en iyi uygulamalar](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Erişim durumu
Duruma, durum Yöneticisi ile anahtara göre erişilir. Aktör kalıcı duruma sahip olduğunda, durum Yöneticisi yöntemlerinin hepsi zaman uyumsuzdur. İlk erişimden sonra durum nesneleri bellekte önbelleğe alınır. Erişimi doğrudan bellekten ve disk g/ç veya zaman uyumsuz bağlam değiştirme ek yükü olmadan eşzamanlı olarak döndürülen erişim işlemlerini erişim nesnelerine yineleyin. Aşağıdaki durumlarda önbellekten bir durum nesnesi kaldırılır:

* Aktör yöntemi, durum yöneticisinden bir nesne aldıktan sonra işlenmeyen bir özel durum oluşturur.
* Aktör, devre dışı bırakıldıktan veya hatadan sonra yeniden etkinleştirilir.
* Durum sağlayıcısının durumu disk olarak. Bu davranış, durum sağlayıcısı uygulamasına bağlıdır. Ayarın varsayılan durum sağlayıcısı `Persisted` Bu davranışa sahiptir.

 `KeyNotFoundException` `NoSuchElementException` Anahtar için bir giriş yoksa, (C#) veya (Java) oluşturan bir standart get işlemi kullanarak durumu alabilirsiniz:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Ayrıca, bir anahtar için bir girdi yoksa, oluşturmayan bir *TryGet* yöntemi kullanarak durumu alabilirsiniz:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Durumu Kaydet
Durum Yöneticisi alma yöntemleri, yerel bellekteki bir nesneye bir başvuru döndürür. Bu nesnenin tek başına yerel bellekte değiştirilmesi, bu nesnenin durda kaydedilmesine neden olmaz. Bir nesne durum yöneticisinden alındığında ve değiştirildiğinde, bu, durda kaydedilmesi için durum yöneticisine yeniden eklenmelidir.

Sözdizimi eşdeğeri olan koşulsuz *küme* kullanarak durum ekleyebilirsiniz `dictionary["key"] = value` :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Bir *Add* yöntemi kullanarak State ekleyebilirsiniz. Bu yöntem `InvalidOperationException` `IllegalStateException` , zaten var olan bir anahtar eklenmeye çalışıldığında (C#) veya (Java) oluşturur.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Ayrıca, *TryAdd* yöntemi kullanarak durum ekleyebilirsiniz. Bu yöntem, zaten var olan bir anahtarı eklemeye çalıştığında oluşturmaz.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Aktör yönteminin sonunda, durum Yöneticisi ekleme veya güncelleştirme işlemi tarafından eklenmiş veya değiştirilmiş olan tüm değerleri otomatik olarak kaydeder. Kullanılan ayarlara bağlı olarak, "Kaydet", disk ve çoğaltmayı kalıcı olarak içerebilir. Değiştirilmeyen değerler kalıcı değil veya çoğaltılmaz. Hiçbir değer değiştirilmediyse, Kaydet işlemi hiçbir şey yapmaz. Kaydetme başarısız olursa, değiştirilen durum atılır ve özgün durum yeniden yüklenir.

Aktör tabanında yöntemini çağırarak durumu el ile de kaydedebilirsiniz `SaveStateAsync` :

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Durumu Kaldır
*Remove* metodunu çağırarak, aktörin durum yöneticisinden durumu kalıcı olarak kaldırabilirsiniz. Bu yöntem `KeyNotFoundException` `NoSuchElementException` , var olmayan bir anahtarı kaldırmaya çalıştığında (C#) veya (Java) oluşturur.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Ayrıca, *TryRemove* metodunu kullanarak durumu kalıcı olarak kaldırabilirsiniz. Bu yöntem, varolmayan bir anahtarı kaldırmaya çalıştığında oluşturmaz.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Reliable Actors depolanan durum, diske yazılmadan ve yüksek kullanılabilirlik için çoğaltılmadan önce serileştirilmelidir. [Aktör türü serileştirme](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)hakkında daha fazla bilgi edinin.

Ardından, [aktör tanılama ve performans izleme](service-fabric-reliable-actors-diagnostics.md)hakkında daha fazla bilgi edinin.
