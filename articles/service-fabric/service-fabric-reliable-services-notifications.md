---
title: Reliable Services bildirimleri
description: Güvenilir durum Yöneticisi ve güvenilir sözlük için Service Fabric Reliable Services bildirimlerine yönelik kavramsal belgeler
author: mcoskun
ms.topic: conceptual
ms.date: 6/29/2017
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: f5b48cc6cca2e143c48ed7bdfc99de936be2a227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784588"
---
# <a name="reliable-services-notifications"></a>Reliable Services bildirimleri
Bildirimler, istemcilerin ilgilendikleri bir nesne üzerinde yapılan değişiklikleri izlemesine olanak sağlar. İki tür nesne bildirimleri destekler: *güvenilir durum Yöneticisi* ve *güvenilir sözlük*.

Bildirimleri kullanmanın yaygın nedenleri şunlardır:

* İkincil dizinler veya çoğaltmanın durumunun toplanmış Filtrelenmiş görünümleri gibi gerçekleştirilmiş görünümler oluşturma. Bu örnek, güvenilir Sözlükteki tüm anahtarların sıralanmış bir dizinidir.
* Son saate eklenen Kullanıcı sayısı gibi izleme verileri gönderme.

İşlemler uygulamanın bir parçası olarak bildirimler tetiklenir. Bu nedenle, bildirimler mümkün olduğunca hızlı işlenmeli ve zaman uyumlu olaylar pahalı işlemler içermemelidir.

## <a name="reliable-state-manager-notifications"></a>Güvenilir durum Yöneticisi bildirimleri
Güvenilir durum Yöneticisi aşağıdaki olaylar için bildirim sağlar:

* İşlem
  * İşleme
* Durum Yöneticisi
  * Yeniden derleme
  * Güvenilir bir durum ekleme
  * Güvenilir bir durumu kaldırma

Güvenilir durum Yöneticisi, geçerli esnek işlemleri izler. İşlem durumundaki tek değişiklik, bir bildirimin tetikedilmesine neden olan bir işlemdir.

Güvenilir durum Yöneticisi güvenilir sözlük ve güvenilir sıra gibi güvenilir durumların bir koleksiyonunu tutar. Bu koleksiyon değiştiğinde güvenilir durum Yöneticisi bildirimleri tetikleyen: güvenilir bir durum eklendiğinde veya kaldırıldığında ya da tüm koleksiyon yeniden oluşturulur.
Güvenilir durum Yöneticisi koleksiyonu üç durumda yeniden oluşturulur:

* Kurtarma: bir çoğaltma başladığında, önceki durumunu diskten kurtarır. Kurtarma sonunda, kurtarılan güvenilir durumlar kümesini içeren bir olay harekete geçmek için **Notifystatemanagerchangedeventargs** ' ı kullanır.
* Tam kopya: bir çoğaltmanın yapılandırma kümesine katılabilmesi için, oluşturulması gerekir. Bazen, bu durum, güvenilir durum yöneticisinin birincil çoğaltmadan boşta kalan ikincil çoğaltmaya uygulanacak durumunun tam bir kopyasını gerektirir. İkincil çoğaltma üzerinde güvenilir durum Yöneticisi, birincil çoğaltmadan elde edilen güvenilir durumlar kümesini içeren bir olay tetiklemesi için **Notifystatemanagerchangedeventargs** kullanır.
* Geri yükleme: olağanüstü durum kurtarma senaryolarında çoğaltmanın durumu **Restoreasync** aracılığıyla bir yedekten geri yüklenebilir. Bu gibi durumlarda, birincil çoğaltmadaki güvenilir durum Yöneticisi, yedekten geri yüklendiği güvenilir durumlar kümesini içeren bir olay harekete geçmek için **Notifystatemanagerchangedeventargs** kullanır.

İşlem bildirimleri ve/veya durum Yöneticisi bildirimlerine kaydolmak için, güvenilir durum Yöneticisi 'nde **Işlem değiştirilmiş** veya **statemanagerchanged** olaylarıyla kayıt yapmanız gerekir. Bu olay işleyicileriyle kaydolmak için ortak bir yer, durum bilgisi olan hizmetinizin oluşturucusudur. Oluşturucuya kaydettiğinizde, **ıreliablestatemanager** ömrü boyunca bir değişikliğin neden olduğu bir bildirimi kaçırmaz.

```csharp
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**Transactionchanged** olay işleyicisi olay hakkında ayrıntı sağlamak Için **Notifytransactionchangedeventargs** ' i kullanır. Değişiklik türünü belirten Action özelliğini (örneğin, **Notifytransactionchangedadction. Commit**) içerir. Ayrıca, değiştirilen işleme bir başvuru sağlayan Transaction özelliğini de içerir.

> [!NOTE]
> Bugün, **Transactionchanged** olayları yalnızca işlem taahhütse tetiklenir. Eylem daha sonra **Notifytransactionchangedadction. Commit**' e eşittir. Ancak gelecekte, diğer işlem durum değişikliği türleri için olaylar ortaya çıkabilir. Eylemi kontrol etmenizi ve yalnızca beklediğiniz bir ise olayı işlemenizi öneririz.
> 
> 

Aşağıda örnek bir **Transactionchanged** olay işleyicisi verilmiştir.

```csharp
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

**Statemanagerchanged** olay işleyicisi olay hakkında ayrıntı sağlamak Için **Notifystatemanagerchangedeventargs** kullanır.
**Notifystatemanagerchangedeventargs** iki alt sınıflara sahiptir: **Notifystatemanagerrebuildeventargs** ve **notifystatemanagersingleentitychangedeventargs**.
Notifystatemanagerchangedeventargs içindeki  Action özelliğini doğru alt **sınıfa atamak için** kullanın:

* **Notifystatemanagerchangedadction. Rebuild**: **notifystatemanagerrebuildeventargs**
* **Notifystatemanagerchangedadction. Add** ve **Notifystatemanagerchangedadction. Remove**: **notifystatemanagersingleentitychangedeventargs**

Örnek bir **Statemanagerchanged** bildirim işleyicisi aşağıda verilmiştir.

```csharp
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStateManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>Güvenilir sözlük bildirimleri
Güvenilir sözlük aşağıdaki olaylar için bildirimler sağlar:

* Yeniden oluştur: **Reliabledictionary** durumu kurtarılan veya kopyalanmış bir yerel durumdan veya yedekten kurtarıldığı zaman çağrılır.
* Clear: **Reliabledictionary** 'ın durumu **clearasync** yöntemi aracılığıyla Temizlenmişken, çağırılır.
* Add: bir öğe **Reliabledictionary** öğesine eklendiğinde çağırılır.
* Update: **ıreliabledictionary** içindeki bir öğe güncellendiyse çağırılır.
* Remove: **ıreliabledictionary** içindeki bir öğe silindiğinde çağırılır.

Güvenilir sözlük bildirimleri almak için **ıreliabledictionary** üzerinde **dictionarychanged** olay işleyicisine kaydolmanız gerekir. Bu olay işleyicileriyle kaydolmak için ortak bir yer, **Reliablestatemanager. StateManagerChanged** bildirim Ekle ' dir.
**Ireliabledictionary** , **ıreliablestatemanager** 'a eklendiğinde, tüm bildirimleri kaçırmamasını sağlar.

```csharp
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
        }
    }
}
```

> [!NOTE]
> **Processstatemanagersingleentitynotification** , önceki **Onstatemanagerchangedhandler** örneğinin çağırdığı örnek bir yöntemdir.
> 
> 

Yukarıdaki kod, **ıreliablenotificationasynccallback** arabirimini ve **dictionarychanged** ile birlikte ayarlar. **Notifydictionaryrebuildeventargs** , zaman uyumsuz olarak numaralandırılması gereken bir **ıasyncenumerable** arabirimi içerdiğinden--yeniden derleme bildirimleri **Ondictionarychangedhandler** yerine **rebuildnotificationasynccallback** aracılığıyla tetiklenir.

```csharp
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

> [!NOTE]
> Önceki kodda, yeniden oluşturma bildirimini işlemenin bir parçası olarak, önce tutulan toplanmış durum temizlenir. Güvenilir koleksiyon yeni bir durumla yeniden oluşturulduğundan, önceki tüm bildirimler ilgisiz olur.
> 
> 

**Dictionarychanged** olay işleyicisi olay hakkında ayrıntı sağlamak Için **Notifydictionarychangedeventargs** kullanır.
**Notifydictionarychangedeventargs** beş alt sınıflara sahiptir. **Notifydictionarychangedeventargs içindeki Action** özelliğini doğru alt sınıfa dönüştürmek  için kullanın:

* **Notifydictionarychangedadction. Rebuild**: **notifydictionaryrebuildeventargs**
* **Notifydictionarychangedadction. Clear**: **notifydictionarycleareventargs**
* **Notifydictionarychangedadction. Add**: **NotifyDictionaryItemAddedEventArgs**
* **Notifydictionarychangedadction. Update**: **notifydictionaryıtemupdatedeventargs**
* **Notifydictionarychangedadction. Remove**: **notifydictionaryıtemremovedeventargs**

```csharp
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>Öneriler
* Bildirim olaylarının tamamlanmasını mümkün olduğunca hızlı *yapın* .
* Zaman uyumlu olayların bir parçası olarak pahalı işlemler (örneğin, g/ç işlemleri *) yürütmeyin* .
* Olay işlemini gerçekleştirmeden önce eylem *türünü denetleyin.* Yeni eylem türleri gelecekte eklenebilir.

Göz önünde bulundurmanız gereken bazı şeyler aşağıda verilmiştir:

* Bildirimler bir işlemin yürütülmesi kapsamında tetiklenir. Örneğin, geri yükleme işleminin son adımı olarak bir geri yükleme bildirimi tetiklenir. Bildirim olayı işlenene kadar geri yükleme tamamlanmaz.
* Bildirimler uygulama işlemlerinin bir parçası olarak tetiklentiğinden, istemciler yalnızca yerel olarak kaydedilmiş işlemler için bildirimleri görür. Ayrıca, işlemler yalnızca yerel olarak kararlıdır (diğer bir deyişle, günlüğe kaydedilir) garanti edildiğinden, gelecekte geri alınmayabilir.
* Yineleme yolunda, uygulanan her işlem için tek bir bildirim tetiklenir. Bu, Transaction T1 'un Create (X), Delete (X) ve Create (X) ' i içermesi durumunda, bir tane olmak üzere X oluşturulması için bir bildirim alacağınız ve bu sırada oluşturma işlemi için bir bildirim alacağınız anlamına gelir.
* Birden çok işlem içeren işlemler için, işlemler kullanıcının birincil çoğaltmasında alındıkları sırada uygulanır.
* Hatalı ilerlemeyi işlemenin bir parçası olarak bazı işlemler geri alınabilir. Bu geri alma işlemleri için bildirimler tetiklenir ve çoğaltmanın durumu kararlı bir noktaya geri alınır. Geri alma bildirimlerinin önemli bir farkı, yinelenen anahtarlara sahip olayların toplanmaktadır. Örneğin, işlem T1 geri alınmışsa, (X) öğesini silmek için tek bir bildirim görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar
* [Güvenilir Koleksiyonlar](service-fabric-work-with-reliable-collections.md)
* [Reliable Services hızlı başlangıç](service-fabric-reliable-services-quick-start.md)
* [Reliable Services yedekleme ve geri yükleme (olağanüstü durum kurtarma)](service-fabric-reliable-services-backup-restore.md)
* [Güvenilir koleksiyonlar için geliştirici başvurusu](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
