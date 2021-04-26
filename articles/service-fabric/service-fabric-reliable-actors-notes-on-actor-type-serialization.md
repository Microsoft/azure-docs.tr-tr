---
title: Aktör türü serileştirme üzerinde Reliable Actors notları
description: Service Fabric Reliable Actors durumlarını ve arabirimlerini tanımlamak için kullanılabilecek, seri hale getirilebilir sınıfları tanımlamaya yönelik temel gereksinimleri açıklar
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a1118e394046b217a288663659a2c910098e992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576018"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Service Fabric Reliable Actors türü serileştirme notları
Tüm yöntemlerin bağımsız değişkenleri, bir aktör arabirimindeki her bir yöntem tarafından döndürülen görevlerin sonuç türleri ve aktörün durum Yöneticisi 'nde depolanan nesneler, [seri hale getirilebilir veri sözleşmesi](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)olmalıdır. Bu, [aktör olay arabirimlerinde](service-fabric-reliable-actors-events.md)tanımlanan yöntemlerin bağımsız değişkenleri için de geçerlidir. (Aktör olay arabirimi yöntemleri her zaman void döndürür.)

## <a name="custom-data-types"></a>Özel veri türleri
Bu örnekte, aşağıdaki aktör arabirimi adlı özel bir veri türü döndüren bir yöntemi tanımlar `VoicemailBox` :

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Arabirim, bir nesneyi depolamak için durum Yöneticisi 'ni kullanan bir aktör tarafından uygulanır `VoicemailBox` :

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

Bu örnekte, `VoicemailBox` nesne şu durumlarda serileştirilir:

* Nesne bir aktör örneği ve bir çağıran arasında iletilir.
* Nesne, disk üzerinde kalıcı olduğu ve diğer düğümlere çoğaltılan durum yöneticisine kaydedilir.

Güvenilir aktör çerçevesi, DataContract serileştirme kullanır. Bu nedenle, özel veri nesneleri ve üyeleri sırasıyla **DataContract** ve **DataMember** öznitelikleriyle açıklanmalıdır.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Sonraki adımlar
* [Aktör yaşam döngüsü ve çöp toplama](service-fabric-reliable-actors-lifecycle.md)
* [Aktör zamanlayıcılar ve anımsatıcıları](service-fabric-reliable-actors-timers-reminders.md)
* [Aktör olayları](service-fabric-reliable-actors-events.md)
* [Aktör yeniden girişi](service-fabric-reliable-actors-reentrancy.md)
* [Aktör çok biçimlilik ve nesne odaklı tasarım desenleri](service-fabric-reliable-actors-polymorphism.md)
* [Aktör tanılama ve performans izleme](service-fabric-reliable-actors-diagnostics.md)
