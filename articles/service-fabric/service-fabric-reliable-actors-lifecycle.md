---
title: Azure Service Fabric aktör yaşam döngüsüne genel bakış
description: Service Fabric güvenilir aktör yaşam döngüsü, çöp toplama ve aktörlerin ve bunların durumlarını el ile silme işlemini açıklar
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 88db4bb2376cbc418d6954e274a18a6c18a280d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576052"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Aktör yaşam döngüsü, otomatik atık toplama ve el ile silme
Bir aktör, metotlarından herhangi birine ilk kez çağrı yapıldığında etkinleştirilir. Bir aktör, yapılandırılabilir bir süre için kullanılmıyorsa devre dışı bırakılır (aktör çalışma zamanı tarafından atık olarak toplanır). Aktör ve durumu istediğiniz zaman el ile de silinebilir.

## <a name="actor-activation"></a>Aktör etkinleştirme
Aktör etkinleştirildiğinde aşağıdakiler gerçekleşir:

* Bir aktör için bir çağrı geldiğinde ve biri etkin değilse, yeni bir aktör oluşturulur.
* Aktörin durumu, durumu koruma ise yüklenir.
* `OnActivateAsync`(C#) veya `onActivateAsync` (aktör uygulamasında geçersiz kılınabilen) metodu çağrılır.
* Aktör artık etkin olarak kabul edilir.

## <a name="actor-deactivation"></a>Aktör devre dışı bırakma
Aktör devre dışı bırakıldığında aşağıdakiler gerçekleşir:

* Bir aktör bir süre boyunca kullanılmazsa, etkin aktörler tablosundan kaldırılır.
* `OnDeactivateAsync`(C#) veya `onDeactivateAsync` (aktör uygulamasında geçersiz kılınabilen) metodu çağrılır. Bu, aktör için tüm zamanlayıcıları temizler. Durum değişiklikleri gibi aktör işlemleri bu yöntemden çağrılmamalıdır.

> [!TIP]
> Yapı aktör çalışma zamanı, [aktör etkinleştirme ve devre dışı bırakma ile ilgili bazı olayları](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters)yayar. Tanılama ve performans izleme için faydalıdır.
>
>

### <a name="actor-garbage-collection"></a>Aktör çöp toplama
Aktör devre dışı bırakıldığında, aktör nesnesine yapılan başvurular yayımlanır ve normal olarak ortak dil çalışma zamanı (CLR) veya Java sanal makinesi (JVM) çöp toplayıcısı tarafından atık olarak toplanamaz. Çöp toplama yalnızca aktör nesnesini temizler; aktörün durum yöneticisinde depolanan **durumu kaldırmaz.** Aktörün bir sonraki etkinleştirililişinde yeni bir aktör nesnesi oluşturulur ve durumu geri yüklenir.

Devre dışı bırakma ve çöp toplama amacıyla "kullanılma" olarak ne sayılır?

* Çağrı alma
* `IRemindable.ReceiveReminderAsync` çağrılan Yöntem (yalnızca aktör anımsatıcıları kullanıyorsa geçerlidir)

> [!NOTE]
> aktör zamanlayıcılar kullanıyorsa ve zamanlayıcı geri araması çağrılırsa, "kullanılıyor" olarak **sayılmaz.**
>
>

Devre dışı bırakma ayrıntılarına geçmeden önce, aşağıdaki koşulları tanımlamak önemlidir:

* *Tarama aralığı*. Bu, aktör çalışma zamanının, devre dışı bırakılabilen ve atık toplanan aktörlerin etkin aktör tablosunu taradığı aralıktır. Bunun için varsayılan değer 1 dakikadır.
* *Boşta kalma zaman aşımı*. Bu, bir aktörün devre dışı bırakılabilmesi ve atık toplanabilmesi için kullanılmamış (boşta) kalması gereken süredir. Bunun için varsayılan değer 60 dakikadır.

Genellikle, bu Varsayılanları değiştirmeniz gerekmez. Ancak, gerekirse, `ActorServiceSettings` [aktör hizmetiniz](service-fabric-reliable-actors-platform.md)kaydedilirken bu aralıklar değiştirilebilir:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Her etkin aktör için aktör çalışma zamanı, boşta olan (kullanılmayan) süreyi izler. Aktör çalışma zamanı, `ScanIntervalInSeconds` atık olarak toplanabilecek ve boş kalırsa işaret eden her aktörlerin her birini denetler `IdleTimeoutInSeconds` .

Aktör her kullanıldığında, boşta kalma süresi 0 ' a sıfırlanır. Bundan sonra aktör yalnızca, için boşta kalırsa çöp toplama yapılabilir `IdleTimeoutInSeconds` . Aktör arabirimi yöntemi veya aktör anımsatıcı geri araması yürütüldüğünde bir aktörün kullanılmış kabul edileceğini unutmayın. Bir aktör, zamanlayıcı geri araması yürütülürse kullanılmış olarak kabul **edilmez** .

Aşağıdaki diyagramda, bu kavramları göstermek için tek bir aktörün yaşam döngüsü gösterilmektedir.

![Boşta kalma süresi örneği][1]

Örnek, aktör yöntemi çağrılarının, anımsatıcılarının ve zamanlayıcıları bu aktörün kullanım ömrü boyunca etkisini gösterir. Örnekle ilgili aşağıdaki noktalara değer verilmiştir:

* ScanInterval ve IdleTimeout sırasıyla 5 ve 10 olarak ayarlanmıştır. (Amacımız yalnızca kavramı göstermek olduğundan, bu şekilde birimler burada değildir.)
* Çöp toplama yapılacak aktörlerin taranması, 5 tarama aralığı ile tanımlanan T = 0, 5, 10, 15, 20, 25 ' te gerçekleşir.
* T = 4, 8, 12, 16, 20, 24 ve geri çağırma yürütüldüğünde düzenli bir süreölçer ateşlenir. Aktörün boşta kalma süresini etkilemez.
* T = 7 ' de bir aktör yöntemi çağrısı, boşta kalma süresini 0 olarak sıfırlar ve aktörin çöp toplama işlemini geciktirir.
* Aktör anımsatıcı geri araması T = 14 ' de yürütülür ve aktör atık koleksiyonuna daha fazla gecikmektedir.
* T = 25 ' te çöp toplama taraması sırasında, aktörün boşta kalma süresi 10 ' un boşta kalma süresini aştığında aktör atık olarak toplanır.

Bu yöntemin yürütülmesi ne kadar zaman harcandığına bakılmaksızın bir aktör, yöntemlerinden birini yürütürken hiçbir zaman atık olarak toplanmayacaktır. Daha önce belirtildiği gibi, aktör Arabirim yöntemlerinin ve anımsatıcı geri çağırmaların yürütülmesi aktörün boşta kalma süresini 0 olarak sıfırlayarak çöp toplamayı önler. Zamanlayıcı geri çağırmaları yürütme, boşta kalma süresini 0 olarak sıfırlamaz. Ancak, bir aktörün çöp toplama işlemi, zamanlayıcı geri çağrısının yürütülmesi tamamlanana kadar ertelenir.

## <a name="manually-deleting-actors-and-their-state"></a>Aktörleri ve durumlarını el ile silme
Devre dışı bırakılmış aktörlerin atık toplama işlemi yalnızca aktör nesnesini temizler, ancak aktörün durum yöneticisinde depolanan verileri kaldırmaz. Aktör yeniden etkinleştirildiğinde, verileri durum Yöneticisi aracılığıyla yeniden kullanılabilir hale getirilir. Aktörlerin verileri durum Yöneticisi 'nde depolaması ve devre dışı bırakılmaması, ancak hiçbir zaman etkinleştirilmemesi durumunda, verilerinin temizlenmesi gerekebilir.  Aktörlerin nasıl silineceği ile ilgili örnekler için, [aktörlerin ve bunların durumunu silin](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Aktör zamanlayıcılar ve anımsatıcıları](service-fabric-reliable-actors-timers-reminders.md)
* [Aktör olayları](service-fabric-reliable-actors-events.md)
* [Aktör yeniden girişi](service-fabric-reliable-actors-reentrancy.md)
* [Aktör tanılama ve performans izleme](service-fabric-reliable-actors-diagnostics.md)
* [Aktör API 'SI başvuru belgeleri](/previous-versions/azure/dn971626(v=azure.100))
* [C# örnek kodu](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java örnek kodu](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
