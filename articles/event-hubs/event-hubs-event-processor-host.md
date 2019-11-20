---
title: Olay işlemcisi konağı - Azure Event Hubs kullanarak olay alma | Microsoft Docs
description: Bu makalede, Azure Event denetim noktası yönetimini basitleştiren, kiralama ve olayları eçimi paralel okumaya hubs'da, olay işlemcisi konağı açıklanır.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 07/16/2019
ms.author: shvija
ms.openlocfilehash: 312800482405530d57ce7b0b1e77b91c2ad069ce
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772152"
---
# <a name="event-processor-host"></a>Olay işlemcisi konağı

Azure Event Hubs, milyonlarca olayı düşük bir maliyet karşılığında kullanılabilir güçlü telemetri alma hizmetidir. Bu makalede kullanarak içe alınan etkinlikleri kullanma *Event Processor Host* (EPH); akıllı bir tüketici aracıyı denetim noktası oluşturma, kiralama ve paralel olay okuyucuları yönetimini basitleştirir.  

Event Hubs için ölçeklendirmek için anahtar bölümlenmiş tüketici olur. Tersine [rakip tüketiciler](https://msdn.microsoft.com/library/dn568101.aspx) düzeni, bölünmüş bir tüketici modeli sağlar büyük ölçekli Çekişme performans sorunu kaldırma ve uçtan uca paralellik etkinleştirme.

## <a name="home-security-scenario"></a>Giriş güvenlik senaryosu

Örnek bir senaryo 100.000 havaalanlarından izleyen bir giriş güvenliği şirketi düşünün. Her dakika hareket algılayıcısı, kapı/penceresi açık algılayıcı, cam sonu algılayıcısı, her giriş sayfasında yüklü vb. gibi çeşitli sensörlerden alınan verileri alır. Şirket, kendi giriş neredeyse gerçek zamanlı etkinliğini izlemek vatandaşlar için bir web sitesi sağlar.

Her algılayıcı verileri olay hub'ına gönderir. Olay hub'ı 16 bölümleri ile yapılandırılır. Alıcı uçta ise bu etkinlikleri, birleştirmek (filtreleme, toplama, vb.) ve ardından kullanıcı dostu bir web sayfasına gsyih bir depolama blobu için toplama dökümünü bir mekanizma gerekir.

## <a name="write-the-consumer-application"></a>Tüketici uygulaması yazma

Dağıtılmış bir ortamda tüketici tasarlarken, senaryo aşağıdaki gereksinimleri ele almalısınız.

1. **Ölçek** Her tüketiciye birkaç Event Hubs bölümden okuma sahipliği alan birden çok tüketici oluşturun.
2. **Yük Dengeleme:** Tüketicileri dinamik olarak artırın veya azaltın. Örneğin, her giriş için yeni bir algılayıcı türü (örneğin, gizli monoxide algılayıcısı) eklendiğinde, olay sayısını artırır. Bu durumda, ' % s'işleci (insan) tüketici örneği sayısını artırır. Tüketici havuzu oldukları, bölüm sayısını dengeleyebilir sonra yeni eklenen tüketicileriyle yük paylaşma.
3. **Hatalarda sorunsuz bir şekilde sürdürülür:** Bir tüketici (**tüketici a**) başarısız olursa (örneğin, tüketiciyi barındıran sanal makine aniden kilitlenirse), diğer tüketiciler **tüketici a** 'nın sahip olduğu bölümleri çekebilmelidir ve devam edebilir. Ayrıca, devamlılık noktası, adlı bir *denetim noktası* veya *uzaklığı*, tam noktada olmalıdır **tüketici A** hatalı veya biraz daha önceki.
4. **Olayları tüketme:** Önceki üç işaret, tüketicinin yönetimiyle ilgilenirken, olayları tüketmek ve bununla ilgili bir şey yapmak için kod olmalıdır; Örneğin, bunu toplayın ve BLOB depolamaya yükleyin.

Kendi çözümünüzü bu oluşturmak yerine, olay hub'ları aracılığıyla bu işlevselliği sağlar [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) arabirimi ve [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) sınıfı.

## <a name="ieventprocessor-interface"></a>Ieventprocessor arabirimini

İlk olarak, uygulamaları kullanan [ıeventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) arabirimini uygular ve bu da dört yöntem içerir: [OpenAsync, CloseAsync, ProcessErrorAsync ve ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Bu arabirim, Event Hubs gönderdiği olayları kullanma gerçek kodu içerir. Aşağıdaki kod, basit bir uygulama gösterilmektedir:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Ardından, örneği bir [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneği. Oluştururken aşırı yükleme, bağlı olarak [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneği oluşturucusunun içinde aşağıdaki parametreler kullanılır:

- **ana bilgisayar adı:** her tüketici örneğinin adı. Her **Eventprocessorhost** örneğinin bir tüketici grubu içindeki bu değişken için benzersiz bir değeri olması gerekir, bu nedenle bu değeri sabit olarak kodlayın.
- **eventHubPath:** Olay Hub 'ının adı.
- **consumerGroupName:** Event Hubs varsayılan tüketici grubunun adı olarak **$Default** kullanır, ancak belirli bir işlem yönüdür için bir tüketici grubu oluşturmak iyi bir uygulamadır.
- **eventHubConnectionString:** Azure portal alınabilecek Olay Hub 'ına yönelik bağlantı dizesi. Bu bağlantı dizesi olmalıdır **dinleme** olay hub'ında izinleri.
- **StorageConnectionString** İç kaynak yönetimi için kullanılan depolama hesabı.

Son olarak, tüketicilerin kaydetme [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneği ile Event Hubs hizmeti. Bir olay işlemcisi sınıfı EventProcessorHost örneğini kaydetme, olay işleme başlar. Kayıt talimatı verir tüketici uygulama bazı bölümleri olaylardan tükettiğini beklenir ve çağırmak için Event Hubs hizmeti [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) kullanmak için olaylar gönderir her uygulama kodu. 


### <a name="example"></a>Örnek

Örneğin, 5 sanal makineleri (VM'ler) Imagine olayları ve her VM'de bir basit bir konsol uygulaması kullanan için ayrılmış, hangi gerçek tüketim çalışır. Aşağıdakilerden her konsol uygulaması oluşturur [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneği ve Event Hubs hizmetine kaydeder.

Bu örnek senaryoda varsayalım 16 bölüm 5'e ayrıldığını algıladı **EventProcessorHost** örnekleri. Bazı **EventProcessorHost** örnekleri birkaç daha fazla bölüm diğerlerinden sahip. Her biri için bölüm bir **EventProcessorHost** kopyasına sahip, bir örneğini oluşturur `SimpleEventProcessor` sınıfı. Bu nedenle, 16 örnekleri vardır `SimpleEventProcessor` her bölüm için atanan bir genel.

Aşağıdaki listede, bu örnek özetlenmektedir:

- 16 event Hubs bölümler.
- 5 VM, her VM'deki 1 tüketici uygulama (örneğin, Consumer.exe).
- 5 EPH örneklerin kayıtlı, her VM Consumer.exe tarafından 1.
- 16 `SimpleEventProcessor` 5 EPH örnekleri tarafından oluşturulmuş nesneleri.
- 1 Consumer.exe uygulama 4 içerebilir `SimpleEventProcessor` nesneleri bu yana 1 EPH örnek 4 bölüm sahip.

## <a name="partition-ownership-tracking"></a>İzleme bölümü sahipliği

Bir bölüm EPH örneği (veya bir tüketici) sahipliğini, izleme için belirtilen Azure depolama hesabıyla izlenir. Basit bir tablo olarak izleme gibi görselleştirebilirsiniz. Sağlanan depolama hesabı altındaki BLOB'ları inceleyerek, gerçek uygulama görebilirsiniz:

| **Tüketici grubu adı** | **Bölüm kimliği** | **Ana bilgisayar adı (sahibi)** | **Alınan kira (veya sahipliği)** | **Uzaklık içinde bölümü (Denetim)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Tüketici\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Tüketici\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Tüketici\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Tüketici\_VM3 | 2018-04-15T01:22:56 | 976 |

Burada, her konağın belirli bir süre için (kiralama süresi) bir bölüm sahipliğini alır. Ardından konak başarısız olursa (VM kapatıldığında) kira süresi dolar. Diğer ana bölüm sahipliğini almayı denemek ve konaklardan birine başarılı olur. Bu işlem yeni bir sahip olan bölüm kirayı sıfırlar. Bu şekilde, aynı anda yalnızca tek bir okuyucu herhangi belirli bir bölümünün bir tüketici grubundaki okuma yapabilirsiniz.

## <a name="receive-messages"></a>İleti alma

Her çağrı [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) olaylar koleksiyonu sunar. Bu olayları işlemek için sizin sorumluluğunuzdur. İşlemci konağının her iletiyi en az bir kez işlediğinden emin olmak istiyorsanız, kendi yeniden deneme kodu yazmanız gerekir. Ancak kired iletiler hakkında dikkatli olun.

Görece şeyler yapmanız önerilir. diğer bir deyişle, mümkün olduğunca az miktarda işleme olarak yapın. Bunun yerine, tüketici gruplarını kullanın. Depolama alanına yazmanız ve bazı yönlendirme yapmanız gerekiyorsa, iki Tüketici grubu kullanmak ve ayrı olarak çalışan iki [ıeventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) uygulaması olması daha iyidir.

İşleme sırasında belirli bir noktada ne artık okuma tamamlandı ve izlemek isteyebilirsiniz. Akış başlangıcına döndürmeyin için okuma, yeniden başlatırsanız izleme tutmak önemlidir. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) kullanarak bu izleme basitleştirir *kontrol noktaları*. Bir konum veya uzaklık, belirli bir tüketici grubundaki belirli bir bölüm için bir denetim noktası, işlenen olduğunuz bu noktaya memnun iletileri. Bir denetim noktasında işaretleme **EventProcessorHost** çağrılarak gerçekleştirilir [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metodunda [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) nesne. Bu işlem gerçekleştirilir [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) yöntemi de gitmeniz gerekir, ancak [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Denetim noktası oluşturma

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) yönteminin iki aşırı yüklemesi vardır: birincisi, parametre olmadan, en yüksek olay uzaklığı tarafından döndürülen koleksiyon içinde denetim noktaları [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Bu uzaklık, bir "yüksek su" işaretidir; Bu, çağırdığınızda, tüm son olayları işleyip varsayar. Bu şekilde bu yöntemi kullanırsanız, bir olay işleme kodunuzu döndürdü sonra çağrısından beklenir unutmayın. İkinci aşırı yükleme belirtmenizi sağlar bir [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) denetim noktası örneği. Bu yöntem, farklı türde bir filigran kontrol noktasına kullanmanıza olanak sağlar. Bu eşik ile bir "Düşük" Filigran uygulayabilir: olduğunuz belirli en düşük sıralı olay işlendi. Bu aşırı yükleme uzaklık yönetim esneklik olanağı sağlanır.

Denetim noktası işlemi yapıldığında, bir JSON dosyası (özellikle uzaklık) bölüm özgü bilgilerle yazılır oluşturucu içinde sağlanan depolama hesabına [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Bu dosya sürekli olarak güncelleştirilir. Denetim noktası oluşturma bağlamda göz önünde bulundurmanız önemlidir - kontrol noktası için uygun her ileti. Denetim noktası oluşturma için büyük olasılıkla kullanılan depolama hesabı, bu yükle başa değil, ancak denetim noktası her olay bir Service Bus kuyruğuna bir olay hub'den daha iyi bir seçenek olabilir bir kuyruğa alınan Mesajlaşma modeli simulatorda daha da önemlisi. Event Hubs arkasında büyük ölçekte "en az bir kez" teslim alma olur. Aşağı Akış sistemlerine ıdempotent yaparak bu hatalardan kurtarma kolayca veya sonucunda aynı olayları birden çok kez alınan yeniden başlatır.

## <a name="thread-safety-and-processor-instances"></a>İş parçacığı güvenliği ve işlemci örnekleri

Varsayılan olarak, [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) iş parçacığı güvenlidir ve zaman uyumlu bir şekilde örneğini göre davranışını [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Olayları bir bölüm için geldiğinde [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) üzerinde çağrılır **Ieventprocessor** bölüm ve sonraki çağrılar için engeller örnek **ProcessEventsAsync**bölümü için. Sonraki iletiler ve çağrıları **ProcessEventsAsync** kuyrukta arka planda ileti pompası diğer iş parçacıkları üzerinde arka planda çalışmaya devam ettikçe. Bu iş parçacığı güvenliği, iş parçacığı güvenli koleksiyonları gereksinimini ortadan kaldırır ve performansı önemli ölçüde artırır.

## <a name="shut-down-gracefully"></a>Düzgün biçimde kapatılamadı

Son olarak, [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) tüm bölüm okuyucular, temiz kapatma sağlar ve her zaman örneğini kapatılırken çağrılmalıdır [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Bunun yapılmaması diğer örneğini başlatırken gecikmelere neden olabilir **EventProcessorHost** kira sona erme ve dönem çakışmaları nedeniyle. Dönem yönetimi, makalenin [Dönem](#epoch) bölümünde ayrıntılı olarak ele alınmıştır. 

## <a name="lease-management"></a>Kiralama Yönetimi
Bir olay işlemcisi sınıfı EventProcessorHost örneğini kaydetme, olay işleme başlar. Ana bilgisayar örneği, büyük olasılıkla tüm konak örneklerinde bölümlerin eşit dağıtımı üzerinde uygun sonuç verir şekilde diğer konak örneklerden bazıları kapmasını bazı bölümlerini bir olay hub'ı üzerinde kiraları alır. Kiralanmış her bölüm için ana bilgisayar örneği sağlanan olay işlemcisi sınıfının bir örneğini oluşturur, ardından bu bölümün dışında olaylarını alır ve bunları olay işlemcisi örneğine geçirir. Daha fazla örnek eklenir ve daha fazla kira yakaladı EventProcessorHost sonunda tüm tüketicileri arasında yük dengeleyen.

Daha önce açıklandığı şekilde, otomatik ölçeklendirme yapısını izleme tablosu büyük ölçüde basitleştirir. [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Bir örneği olarak **EventProcessorHost** başlatır, mümkün olduğunca çok kiraları alır ve olayları okumaya başlar. Kiralar, süresi dolmak üzere olarak **EventProcessorHost** rezervasyon yerleştirerek yenilemek çalışır. Kira yenileme için kullanılabilir, işlemci okumaya devam eder, ancak değilse, okuyucu kapatılana ve [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) çağrılır. **CloseAsync** için o bölümün son tüm temizleme işlemlerini gerçekleştirmek için iyi bir zamandır.

**EventProcessorHost** içeren bir [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) özelliği. Bu özellik, kiralama yönetimine üzerinde denetim sağlar. Kaydetmeden önce bu seçenekleri ayarlamak, [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) uygulaması.

## <a name="control-event-processor-host-options"></a>Olay işlemcisi konağı seçeneklerini denetleme

Ayrıca, bir aşırı yüklemesini [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) götüren bir [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) bir parametre olarak nesne. Davranışını denetlemek için bu parametreyi kullanın [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) kendisi. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) dört özellikleri ve bir olay tanımlar:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): [Processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)çağrısında almak istediğiniz koleksiyonun en büyük boyutu. Bu boyut en düşük, yalnızca en büyük boyutu değil. Alınabilmesi için daha az sayıda ileti olduğunda **ProcessEventsAsync** ile kadar kullanılabilir olarak yürütür.
- [Prefetchcount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): İstemcinin kaç ileti alacağını öğrenmek için temel AMQP kanalı tarafından kullanılan bir değer. Bu değer, büyük veya buna eşit olmalıdır [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Bu parametre **true**ise, bir bölümdeki olayları almak için temeldeki çağrı zaman aşımına uğrarsa [processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) çağırılır. Bu yöntem, etkinlik olmadan geçen bölüm dönemlerde zamana bağlı eylemleri almak için kullanışlıdır.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Bir okuyucu bölüm okumaya başladığında ilk sapmayı sağlamak için çağrılan bir işlev işaretçisi veya lambda ifadesinin ayarlanmasını sağlar. Bir uzaklık içeren bir JSON dosyası için sağlanan depolama hesabında zaten kaydedildi sürece bu uzaklığı belirtmeden okuyucu eski etkinlikte başlatır [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Oluşturucusu. Bu yöntem, okuyucu başlangıç davranışını değiştirmek istediğinizde kullanışlıdır. Bu yöntem çağrıldığında, nesne parametresi okuyucu başlatıldığına bölüm Kimliğini içerir.
- [Exceptionreceivedeventargs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)içinde oluşan temeldeki tüm özel durumlarla ilgili bildirim almanızı sağlar. Beklediğiniz gibi şeyler çalışmıyorsanız, bu olay bakmaya başlamak için iyi bir yerdir.

## <a name="epoch"></a>Süresinin

Alma süresinin nasıl çalıştığı aşağıda verilmiştir:

### <a name="with-epoch"></a>Dönem ile
Dönem, hizmetin kullandığı, Bölüm/kira sahipliğini zorlamak için benzersiz bir tanımlayıcıdır (dönem değeri). [Createdönemler Chahize](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) yöntemini kullanarak dönem tabanlı bir alıcı oluşturun. Bu yöntem, dönem tabanlı bir alıcı oluşturur. Alıcı, belirtilen tüketici grubundan belirli bir olay hub 'ı bölümü için oluşturulur.

Dönem özelliği, kullanıcılara, aşağıdaki kurallara göre herhangi bir zamanda bir tüketici grubunda yalnızca bir alıcı olmasını sağlar:

- Bir tüketici grubunda mevcut alıcı yoksa, Kullanıcı herhangi bir dönem değeri olan bir alıcı oluşturabilir.
- Bir dönem değeri olan bir alıcı varsa ve bir dönem değeri olan E2 (E1 < = E2) ile yeni bir alıcı oluşturulduysa, E1 ile alıcının bağlantısı otomatik olarak kesilir, E2 içeren alıcı başarıyla oluşturulur.
- Bir dönem değeri E1 olan bir alıcı varsa ve bir dönem değeri > E2 ile yeni bir alıcı oluşturulduysa, bu durumda E2 oluşturma işlemi şu hatayla başarısız olur: Süresi E1 olan bir alıcı zaten var.

### <a name="no-epoch"></a>Dönem yok
[Createreceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) yöntemini kullanarak dönem tabanlı olmayan bir alıcı oluşturursunuz. 

Akış işlemede, kullanıcıların tek bir tüketici grubunda birden çok alıcı oluşturmak istediğiniz bazı senaryolar vardır. Bu tür senaryoları desteklemek için, dönem olmadan bir alıcı oluşturabilir ve bu durumda tüketici grubunda 5 ' e kadar eşzamanlı alıcıya izin veririz.

### <a name="mixed-mode"></a>Karma mod
Dönemi olan bir alıcı oluşturduğunuz ve sonra aynı tüketici grubunda dönem olmayan veya bunun tersini alan uygulama kullanımını önermiyoruz. Ancak, bu davranış oluştuğunda hizmet aşağıdaki kuralları kullanarak bunu işler:

- Zaten dönem E1 ile oluşturulmuş bir alıcı varsa ve etkin bir şekilde olay alıyorsa ve yeni bir alıcı, dönem olmadan oluşturulursa, yeni alıcı oluşturulması başarısız olur. Dönem alıcıları, her zaman sistemde önceliğe sahip olur.
- Zaten bir dönem E1 ile oluşturulmuş ve bağlantısı kesilen bir alıcı varsa ve yeni bir MessagingFactory üzerinde dönem olmadan yeni bir alıcı oluşturulduysa yeni alıcı oluşturma işlemi başarılı olur. Burada sistemimizin, yaklaşık 10 dakika sonra "alıcı bağlantısının kesilmesi" algılayacağı bir desteklenmediği uyarısıyla vardır.
- Dönem olmadan oluşturulmuş bir veya daha fazla alıcı varsa ve dönem E1 ile yeni bir alıcı oluşturulduysa, tüm eski alıcıların bağlantısı kesilir.


> [!NOTE]
> Dönemleri kullanan uygulamalar için farklı tüketici grupları kullanmanızı ve hataları önlemek için dönemler kullanmayan dönemleri kullanmanızı öneririz. 


## <a name="next-steps"></a>Sonraki adımlar

Artık Event Processor Host ile ilgili bilgi sahibi olduğunuza göre Event Hubs hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* [Event Hubs öğreticisi](event-hubs-dotnet-standard-getstarted-send.md) ile çalışmaya başlayın
* [Event Hubs programlama kılavuzu](event-hubs-programming-guide.md)
* [Event Hubs’da kullanılabilirlik ve tutarlılık](event-hubs-availability-and-consistency.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.md)
* [Github'da Event Hubs örnekleri](https://github.com/Azure/azure-event-hubs/tree/master/samples)
