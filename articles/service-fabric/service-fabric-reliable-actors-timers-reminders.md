---
title: Reliable Actors zamanlayıcılar ve anımsatıcılar
description: Her birinin ne zaman kullanılacağı hakkında rehberlik dahil olmak üzere Service Fabric Reliable Actors için zamanlayıcılar ve anımsatıcılara giriş.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: f77eb29c9146fe66d5d2b6073c33e30fbab649c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791803"
---
# <a name="actor-timers-and-reminders"></a>Aktör zamanlayıcılar ve anımsatıcıları
Aktör, zamanlayıcılar veya anımsatıcılar kaydederek kendi üzerinde düzenli işler zamanlayabilir. Bu makalede, zamanlayıcılar ve anımsatıcıların nasıl kullanılacağı gösterilir ve aralarındaki farklar açıklanmaktadır.

## <a name="actor-timers"></a>Aktör zamanlayıcılar
Aktör zamanlayıcılar, aktör çalışma zamanının sağladığı, geri çağırma yöntemlerinin, çift yönlü eşzamanlılık garantisi sağladığından emin olmak için bir .NET veya Java süreölçerinin etrafında basit bir sarmalayıcı sağlar.

Aktör, `RegisterTimer` `registerTimer` `UnregisterTimer` `unregisterTimer` zamanlayıcılarını kaydetmek ve kaydını silmek için temel sınıfında (c#) veya (Java) ve (c#) veya (Java) yöntemlerini kullanabilir. Aşağıdaki örnekte Zamanlayıcı API 'Lerinin kullanımı gösterilmektedir. API 'Ler .NET Zamanlayıcı veya Java zamanlayıcıya çok benzerdir. Bu örnekte, Zamanlayıcının süresi dolduğunda, aktör çalışma zamanı `MoveObject` (C#) veya `moveObject` (Java) yöntemini çağırır. Yöntemi, çift tabanlı eşzamanlılık açısından garanti edilir. Yani bu geri çağırma işlemi tamamlanana kadar başka bir aktör yöntemi veya zamanlayıcı/anımsatıcı geri çağırma işlemleri yapılmaz.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Süreölçerin bir sonraki dönemi, geri çağırma yürütmeyi tamamladıktan sonra başlar. Bu, geri çağırma yürütülürken zamanlayıcının durdurulduğu ve geri çağırma tamamlandığında başlatıldığı anlamına gelir.

Aktör çalışma zamanı, geri çağırma tamamlandığında aktörün durum yöneticisine yapılan değişiklikleri kaydeder. Durumu kaydederken bir hata oluşursa, bu aktör nesnesi devre dışı bırakılır ve yeni bir örnek etkinleştirilir.

[Anımsatıcılardan](#actor-reminders)farklı olarak, zamanlayıcılar güncelleştirilemiyor. `RegisterTimer`Yeniden çağrılırsa, yeni bir Zamanlayıcı kaydedilir.

Aktör çöp toplama işleminin parçası olarak devre dışı bırakıldığında tüm zamanlayıcılar durdurulur. Sonrasında hiçbir süreölçer geri çağırma çağrılmaz. Ayrıca, aktör çalışma zamanı devre dışı bırakmadan önce çalışmakta olan zamanlayıcılar hakkındaki bilgileri korumaz. Bu, gelecekte yeniden etkinleştirildiğinde ihtiyacı olan tüm zamanlayıcıları kaydetmek için aktöre kadar. Daha fazla bilgi için [aktör çöp toplamanın](service-fabric-reliable-actors-lifecycle.md)bölümüne bakın.

## <a name="actor-reminders"></a>Aktör anımsatıcıları
Anımsatıcılar, bir aktör üzerinde belirtilen zamanlarda kalıcı geri çağırmaları tetiklemeye yönelik bir mekanizmadır. İşlevleri zamanlayıcılar ile benzerdir. Ancak zamanlayıcılar aksine, aktör açıkça silinmediği veya aktör açıkça silinene kadar tüm koşullarda anımsatıcılar tetiklenir. Özellikle de, aktör çalışma zamanı aktör durumu sağlayıcısı kullanarak aktörün anımsatıcıları hakkında bilgi sağladığından, bir aktör ve yük devretmeler genelinde uyarı tetiklenir. Ayrıca, zamanlayıcılar aksine, var olan anımsatıcılar, kayıt yöntemi ( `RegisterReminderAsync` ), aynı uyarı *adı* kullanılarak tekrar çağırarak güncelleştirilemeyebilir.

> [!NOTE]
> Anımsatıcıların güvenilirliği, aktör durumu sağlayıcısı tarafından sunulan durum güvenilirliği garantisi ile bağlantılıdır. Bu, durum kalıcılığı *yok* olarak ayarlanmış aktörler için, bir yük devretmeden sonra, anımsatıcıların tetikleneceği anlamına gelir.

Bir anımsatıcıyı kaydetmek için bir aktör, [`RegisterReminderAsync`](/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync#remarks) Aşağıdaki örnekte gösterildiği gibi temel sınıfta verilen yöntemi çağırır:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

Bu örnekte, `"Pay cell phone bill"` Anımsatıcı adıdır. Bu, aktörün bir anımsatıcıyı benzersiz şekilde tanımlamak için kullandığı bir dizedir. `BitConverter.GetBytes(amountInDollars)`(C#), anımsatıcı ile ilişkili bağlamıdır. Anımsatıcı geri çağırması için bir bağımsız değişken olarak aktör 'e geçirilir, yani `IRemindable.ReceiveReminderAsync` (C#) veya `Remindable.receiveReminderAsync` (Java).

Anımsatıcıları kullanan aktörlerin, `IRemindable` Aşağıdaki örnekte gösterildiği gibi arabirimi uygulaması gerekir.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Bir anımsatıcı tetiklendiğinde, Reliable Actors çalışma zamanı  `ReceiveReminderAsync` aktör üzerinde (C#) veya `receiveReminderAsync` (Java) yöntemini çağırır. Aktör birden çok anımsatıcı kaydedebilir ve `ReceiveReminderAsync` (C#) veya `receiveReminderAsync` (Java) yöntemi bu anımsatıcıları tetiklendiğinde çağrılır. Aktör, `ReceiveReminderAsync` `receiveReminderAsync` hangi anımsatıcının tetiklendiğini anlamak için (C#) veya (Java) yöntemine geçirilen anımsatıcı adını kullanabilir.

Aktör çalışma zamanı, `ReceiveReminderAsync` (C#) veya `receiveReminderAsync` (Java) çağrısı tamamlandığında aktör durumunu kaydeder. Durumu kaydederken bir hata oluşursa, bu aktör nesnesi devre dışı bırakılır ve yeni bir örnek etkinleştirilir.

Bir anımsatıcının kaydını silmek için, bir aktör `UnregisterReminderAsync` `unregisterReminderAsync` Aşağıdaki örneklerde gösterildiği gibi (C#) veya (Java) yöntemini çağırır.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Yukarıda gösterildiği gibi, `UnregisterReminderAsync` (c#) veya ( `unregisterReminderAsync` Java) yöntemi bir `IActorReminder` (c#) veya `ActorReminder` (Java) arabirimini kabul eder. Aktör temel sınıfı, (c#) `GetReminder` `getReminder` veya ( `IActorReminder` `ActorReminder` Java) arabirimini anımsatıcı adına geçirerek almak için kullanılabilecek bir (c#) veya (Java) yöntemini destekler. Aktör (c#) `IActorReminder` `ActorReminder` `RegisterReminder` veya `registerReminder` (Java) yöntem çağrısından döndürülen (c#) veya (Java) arabirimini kalıcı hale getirmek zorunda olmadığından, bu kullanışlıdır.

## <a name="next-steps"></a>Sonraki Adımlar
Güvenilir aktör olayları ve yeniden giriş hakkında bilgi edinin:
* [Aktör olayları](service-fabric-reliable-actors-events.md)
* [Aktör yeniden girişi](service-fabric-reliable-actors-reentrancy.md)
