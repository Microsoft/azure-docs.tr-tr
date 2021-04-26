---
title: Olay Işleyicisi ana bilgisayarı 'nı kullanarak olayları alma-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Azure Event Hubs 'daki olay Işleyicisi ana bilgisayarı açıklanmaktadır. Bu, denetim noktası oluşturma, kiralama ve okuma olaylarının paralel olarak yönetilmesini basitleştirir.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d97b446993d3f0a280c1f4fadb237726ac09228a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313428"
---
# <a name="event-processor-host"></a>Olay işlemcisi konağı
> [!NOTE]
> Bu makale, Azure Event Hubs SDK 'sının eski sürümü için geçerlidir. SDK 'nın geçerli sürümü için bkz. [uygulamanızın birden çok örneğinde bölüm yükünü dengeleme](event-processor-balance-partition-load.md). Kodunuzu SDK 'nın daha yeni sürümüne nasıl geçirebileceğinizi öğrenmek için, bu geçiş kılavuzlarını inceleyin. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java betiği](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)

Azure Event Hubs, düşük maliyetli milyonlarca olayı akışa almak için kullanılabilen güçlü bir telemetri alma hizmetidir. Bu makalede, *olay Işlemcisi Konağı* (EPH) kullanılarak alınan olayların nasıl kullanılacağı açıklanır; checkişaret, kiralama ve paralel olay okuyucularının yönetimini kolaylaştıran akıllı bir tüketici Aracısı.  

Event Hubs Ölçeklendirilecek anahtar bölümlenmiş tüketicilerinin fikrine yöneliktir. [Rekabet tüketicilerinin](/previous-versions/msp-n-p/dn568101(v=pandp.10)) deseninin aksine, bölümlenmiş tüketici deseninin çekişme sorunlarını ortadan kaldırarak ve uçtan uca paralellik için kolaylaştırarak yüksek ölçeklenmesini sağlar.

## <a name="home-security-scenario"></a>Ana güvenlik senaryosu

Örnek senaryo olarak 100.000 evlerini izleyen bir ev güvenlik şirketi düşünün. Her dakika, bir hareket algılayıcısı, kapı/pencere açık algılayıcı, cam kesmesi algılayıcısı vb. gibi çeşitli sensörlerden, her girişe yüklenen verileri alır. Şirket, bir evin neredeyse gerçek zamanlı olarak kendi giriş etkinliklerini izlemesine yönelik bir Web sitesi sağlar.

Her algılayıcı, verileri bir olay hub 'ına iter. Olay Hub 'ı 16 bölüm ile yapılandırılır. Tüketim uçta, bu olayları okuyabilen, bunları birleştirebilen (filtre, toplama, vb.) bir mekanizmaya ve toplamanın dökümünü, daha sonra Kullanıcı dostu bir Web sayfasına yansıtan bir depolama blobuna dökmesi gerekir.

## <a name="write-the-consumer-application"></a>Tüketici uygulamasını yazma

Tüketici dağıtılmış bir ortamda tasarlarken, senaryonun aşağıdaki gereksinimleri işlemesi gerekir:

1. **Ölçek:** Her tüketiciye birkaç Event Hubs bölümden okuma sahipliği alan birden çok tüketici oluşturun.
2. **Yük Dengeleme:** Tüketicileri dinamik olarak artırın veya azaltın. Örneğin, her girişe yeni bir algılayıcı türü (örneğin, bir karbon MONOXIDE algılayıcısı) eklendiğinde, olayların sayısı artar. Bu durumda, işleç (insan), tüketici örneklerinin sayısını artırır. Daha sonra, tüketici havuzu, yükü yeni eklenen tüketicilerle paylaşmak için sahip oldukları bölüm sayısını yeniden dengeedebilir.
3. **Hatalarda sorunsuz bir şekilde sürdürülür:** Bir tüketici (**tüketici a**) başarısız olursa (örneğin, tüketiciyi barındıran sanal makine aniden kilitlenirse), diğer tüketiciler **tüketici a** 'nın sahip olduğu bölümleri çekebilmelidir ve devam edebilir. Ayrıca, *denetim noktası* ya da *konum* olarak adlandırılan devamlılık noktası, **tüketicisinin** başarısız olduğu veya bundan biraz önce olması gereken kesin bir noktada olmalıdır.
4. **Olayları tüketme:** Önceki üç işaret, tüketicinin yönetimiyle ilgilenirken, olayları tüketmek ve bununla ilgili bir şey yapmak için kod olmalıdır; Örneğin, bunu toplayın ve BLOB depolamaya yükleyin.

Bunun için kendi çözümünüzü oluşturmak yerine, Event Hubs bu işlevselliği [ıeventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) arabirimi ve [eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) sınıfı aracılığıyla sağlar.

## <a name="ieventprocessor-interface"></a>Ieventprocessor arabirimi

İlk olarak, uygulamaları kullanan  [ıeventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) arabirimini uygular ve bu dört yöntem Içerir: [openAsync, CloseAsync, Processerrorasync ve ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor#methods). Bu arabirim, Event Hubs gönderdiği olayları tüketmek için gerçek kodu içerir. Aşağıdaki kodda basit bir uygulama gösterilmektedir:

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

Sonra, bir [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneği örneği oluşturun. Yük yüküne bağlı olarak, oluşturucuda [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneği oluşturulurken aşağıdaki parametreler kullanılır:

- **ana bilgisayar adı:** her bir tüketici örneğinin adı. Her **Eventprocessorhost** örneğinin bir tüketici grubu içindeki bu değişken için benzersiz bir değeri olması gerekir, bu nedenle bu değeri sabit olarak kodlayın.
- **Eventhubpath:** Olay Hub 'ının adı.
- **Consumergroupname:** Event Hubs varsayılan tüketici grubunun adı olarak **$Default** kullanır, ancak belirli bir işlem yönüdür için bir tüketici grubu oluşturmak iyi bir uygulamadır.
- **Eventhubconnectionstring:** Azure portal alınabilecek Olay Hub 'ına yönelik bağlantı dizesi. Bu bağlantı dizesinin Olay Hub 'ında **dinleme** izinlerine sahip olması gerekir.
- **StorageConnectionString:** İç kaynak yönetimi için kullanılan depolama hesabı.

Son olarak, tüketiciler [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneğini Event Hubs hizmetiyle kaydeder. Bir olay işlemcisi sınıfını EventProcessorHost örneğiyle kaydetme olay işlemesini başlatır. Kayıt, Event Hubs hizmetine tüketici uygulamasının bazı bölümlerinden olayları tüketmesini ve kullanım olaylarını her geldiğinde [ıeventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) uygulama kodunu çağırmayı beklemesini ister. 

> [!NOTE]
> ConsumerGroupName büyük/küçük harfe duyarlıdır.  ConsumerGroupName üzerinde yapılan değişiklikler akışın başından itibaren tüm bölümlerin okunmasına neden olabilir.

### <a name="example"></a>Örnek

Örnek olarak, olayları tüketmek için ayrılmış 5 sanal makine (VM) ve her bir VM 'de, gerçek tüketim işini yapan basit bir konsol uygulaması olduğunu düşünün. Ardından, her bir konsol uygulaması bir [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) örneği oluşturur ve Event Hubs hizmetine kaydeder.

Bu örnek senaryoda, 5 **Eventprocessorhost** örneklerine 16 Bölüm ayrıldığınızı varsayalım. Bazı **Eventprocessorhost** örnekleri diğerlerinden daha fazla bölümden oluşabilir. Bir **Eventprocessorhost** örneğinin sahip olduğu her bölüm için, sınıfının bir örneğini oluşturur `SimpleEventProcessor` . Bu nedenle, `SimpleEventProcessor` her bir bölüme atanmış olan, genel olarak 16 örnek vardır.

Aşağıdaki listede bu örnek özetlenmektedir:

- 16 Event Hubs bölüm.
- Her VM 'de 5 VM, 1 tüketici uygulaması (örneğin, Consumer.exe).
- 5 EPH örneği kaydedilir, her VM 'de Consumer.exe tarafından 1.
- `SimpleEventProcessor`5 EPH örneği tarafından oluşturulan 16 nesne.
- 1 Consumer.exe uygulaması 4 nesne içerebilir `SimpleEventProcessor` , çünkü 1 EPH örneği 4 bölümden oluşabilir.

## <a name="partition-ownership-tracking"></a>Bölüm sahipliği izleme

Bir bölümün bir EPH örneğine (veya bir tüketiciye) sahipliği, izleme için belirtilen Azure depolama hesabı aracılığıyla izlenir. İzlemeyi aşağıdaki gibi basit bir tablo olarak görselleştirebilirsiniz. Belirtilen depolama hesabı altındaki Blobları inceleyerek gerçek uygulamayı görebilirsiniz:

| **Tüketici grubu adı** | **Bölüm Kimliği** | **Ana bilgisayar adı (sahip)** | **Kira (veya sahiplik) Alım Süresi** | **Bölümdeki konum (kontrol noktası)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Tüketici \_ VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Tüketici \_ VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Tüketici \_ VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Tüketici \_ VM3 | 2018-04-15T01:22:56 | 976 |

Burada, her ana bilgisayar belirli bir süre (kira süresi) için bir bölümün sahipliğini alır. Bir ana bilgisayar başarısız olursa (VM kapatılırsa) kira süresi dolar. Diğer konaklar bölümün sahipliğini almaya çalışır ve konaklardan biri başarılı olur. Bu işlem, bölüm üzerindeki kirayı yeni bir sahibe sıfırlar. Bu şekilde, bir seferde yalnızca tek bir okuyucu bir tüketici grubu içindeki belirli bir bölümden okuyabilir.

## <a name="receive-messages"></a>İleti alma

[Processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) öğesine yapılan her çağrı bir olay koleksiyonu sunar. Bu olayları işlemek sizin sorumluluğunuzdadır. İşlemci konağının her iletiyi en az bir kez işlediğinden emin olmak istiyorsanız, kendi yeniden deneme kodu yazmanız gerekir. Ancak kired iletiler hakkında dikkatli olun.

Şeyleri görece hızlı yapmanız önerilir; diğer bir deyişle, mümkün olduğunca az işlem yapın. Bunun yerine, tüketici gruplarını kullanın. Depolama alanına yazmanız ve bazı yönlendirme yapmanız gerekiyorsa, iki Tüketici grubu kullanmak ve ayrı olarak çalışan iki [ıeventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) uygulaması olması daha iyidir.

İşlem sırasında bir noktada, okuduğunuzu ve tamamlandığınızı izlemek isteyebilirsiniz. Okumayı yeniden başlatmanız gerekiyorsa, akışın başına dönmezseniz izlemenin kritik olması önemlidir. [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) , bu izlemeyi *kontrol noktaları* kullanarak basitleştirir. Bir denetim noktası, belirli bir tüketici grubundaki belirli bir bölüm için bir konum veya uzaklığa, bu noktada iletileri işletin karşılanmasını karşıladınız. **Eventprocessorhost** içindeki bir kontrol noktasının Işaretlenmesi, [Partitioncontext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) nesnesinde [checkpointasync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) yöntemi çağırarak gerçekleştirilir. Bu işlem [Processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) yöntemi içinde yapılır, ancak [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync)içinde de yapılabilir.

## <a name="checkpointing"></a>Denetim noktası oluşturma

[Checkpointasync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) yönteminin iki aşırı yüklemesi vardır: ilki parametre olmadan, [Processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)tarafından döndürülen koleksiyonda en yüksek olay kaydırmasına denetim noktası ekler. Bu konum "yüksek su" işaretidir; Bu, çağırdığınızda tüm son olayları işlediğinizi varsayar. Bu yöntemi bu şekilde kullanıyorsanız, diğer olay işleme kodunuz döndürülbaşladıktan sonra çağırmanız beklendiğine dikkat edin. İkinci aşırı yükleme, denetim noktası için bir [eventdata](/dotnet/api/microsoft.azure.eventhubs.eventdata) örneği belirtmenizi sağlar. Bu yöntem, denetim noktası için farklı bir filigran türü kullanmanıza olanak sağlar. Bu filigranla, "düşük su" işareti uygulayabilirsiniz: en düşük sıralı olay işlendiğinde. Bu aşırı yükleme, fark yönetiminde esnekliği sağlamak için sağlanır.

Denetim noktası gerçekleştirildiğinde bölüme özgü bilgileri olan bir JSON dosyası (özellikle de, fark), oluşturucuda [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)'a sağlanan depolama hesabına yazılır. Bu dosya sürekli olarak güncelleştirilir. Bağlam üzerine işaret etmeyi göz önünde bulundurmanız önemlidir. her ileti için denetim noktası oluşturma işlemi geri alınamaz. Checkişaret için kullanılan depolama hesabı, büyük olasılıkla bu yükü işlemez, ancak her bir olayın daha da önemlisi, bir Service Bus sırasının bir olay hub 'ından daha iyi bir seçenek olabileceği sıraya alınmış bir mesajlaşma deseninin göstergesi vardır. Event Hubs arkasındaki düşünce, harika ölçekte "en az bir kez" teslim almanızı öneririz. Aşağı akış sistemlerinizi ıdempotent yaparak, aynı olayların birden çok kez alınmasına neden olan hatalardan veya yeniden başlatmaları kurtarmak kolaydır.

## <a name="thread-safety-and-processor-instances"></a>İş parçacığı güvenliği ve işlemci örnekleri

Varsayılan olarak, [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) iş parçacığı güvenlidir ve [ıeventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)örneğine göre zaman uyumlu bir şekilde davranır. Bir bölüme yönelik olaylar geldiğinde, bu bölüm için **ıeventprocessor** örneğinde [processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) çağrılır ve bölüm Için **processeventsasync** 'e yönelik daha fazla çağrı engellenir. İleti göndericisi, arka planda diğer iş parçacıklarında çalışmaya devam ettiğinden, izleyen iletiler ve arka planda gerçekleştirilen **Processeventsasync** kuyruğuna yapılan çağrılar. Bu iş parçacığı güvenliği, iş parçacığı açısından güvenli koleksiyonlar gereksinimini ortadan kaldırır ve performansı önemli ölçüde artırır.

## <a name="shut-down-gracefully"></a>Düzgün bir şekilde kapatın

Son olarak, [eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) , tüm bölüm okuyucularının temiz bir şekilde kapatılmasını mümkün ve [eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)örneği kapatılırken her zaman çağrılmalıdır. Bunun yapılmaması, kira süre sonu ve dönem çakışmaları nedeniyle diğer **Eventprocessorhost** örnekleri başlatılırken gecikmelere neden olabilir. Dönem yönetimi, makalenin [Dönem](#epoch) bölümünde ayrıntılı olarak ele alınmıştır. 

## <a name="lease-management"></a>Kira yönetimi
Bir olay işlemcisi sınıfını EventProcessorHost örneğiyle kaydetme olay işlemesini başlatır. Konak örneği, büyük olasılıkla diğer ana bilgisayar örneklerinden bazıları yakalayıp, diğer konak örneklerinden bazı bölümler arasında bir dağıtım elde etmekle bir şekilde, Olay Hub 'ının bazı bölümlerine kiralamalar edinir. Her kiralanmış bölüm için konak örneği, belirtilen olay işlemcisi sınıfının bir örneğini oluşturur, ardından bu bölümden olayları alır ve bunları olay işlemcisi örneğine geçirir. Daha fazla örnek eklendikçe ve daha fazla kira olduğundan, EventProcessorHost sonuç olarak yükü tüm tüketiciler arasında dengeler.

Daha önce açıklandığı gibi, izleme tablosu [Eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync)öğesinin otomatik ölçeklendirme yapısını büyük ölçüde basitleştirir. Bir **Eventprocessorhost** örneği başlatıldıktan sonra, mümkün olduğunca fazla kira edinir ve olayları okumaya başlar. Süresi dolmak üzere olan kiralamalar, **Eventprocessorhost** , bir ayırma yerleştirerek bunları yenilemeye çalışır. Kira yenilemeye uygunsa, işlemci okumaya devam eder, ancak yoksa, okuyucu kapanır ve [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) çağırılır. **CloseAsync** , bu bölüm için son temizleme işlemini gerçekleştirmek için uygun bir zamandır.

**Eventprocessorhost** , bir [partitionmanageroptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) özelliği içerir. Bu özellik, kiralama yönetimi üzerinde denetimi sunar. [Ieventprocessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) uygulamanızı kaydetmeden önce bu seçenekleri ayarlayın.

## <a name="control-event-processor-host-options"></a>Denetim olayı Işlemcisi ana bilgisayar seçenekleri

Ayrıca, [Registereventprocessorasync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) 'in bir aşırı yüklemesi parametre olarak bir [eventprocessoroptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) nesnesi alır. [Eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) öğesinin davranışını denetlemek için bu parametreyi kullanın. [Eventprocessoroptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) dört özelliği ve bir olayı tanımlar:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): [Processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)çağrısında almak istediğiniz koleksiyonun en büyük boyutu. Bu boyut en az, yalnızca en büyük boyut değil. Alınacak daha az ileti varsa, **Processeventsasync** , kullanılabilir olduğu kadar çalışır.
- [Prefetchcount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): istemcinin kaç ileti alacağını öğrenmek için temel AMQP kanalı tarafından kullanılan bir değer. Bu değer [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize)değerinden büyük veya buna eşit olmalıdır.
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Bu parametre **true** ise, bir bölümdeki olayları almak için temeldeki çağrı zaman aşımına uğrarsa [processeventsasync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) çağırılır. Bu yöntem, bölüm üzerinde işlem yapılmayan işlemler sırasında zaman tabanlı eylemler gerçekleştirmek için yararlıdır.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): bir okuyucu bölüm okumaya başladığında ilk sapmayı sağlamak için çağrılan bir işlev işaretçisi veya lambda ifadesinin ayarlanmasını sağlar. Bu sapmayı belirtmeden, bir uzaklığa sahip bir JSON dosyası [Eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) oluşturucusuna sağlanan depolama hesabına zaten kaydedilmediği için okuyucu en eski olayda başlar. Bu yöntem, okuyucu başlatmanın davranışını değiştirmek istediğinizde yararlıdır. Bu yöntem çağrıldığında, nesne parametresi okuyucunun başlatıldığı bölüm KIMLIĞINI içerir.
- [Exceptionreceivedeventargs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): [eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)içinde oluşan temeldeki tüm özel durumlarla ilgili bildirim almanızı sağlar. İstediğiniz gibi şeyler çalışmadıysanız, bu olay aramaya başlamak için iyi bir yerdir.

## <a name="epoch"></a>Süresinin

Alma süresinin nasıl çalıştığı aşağıda verilmiştir:

### <a name="with-epoch"></a>Dönem ile
Dönem, hizmetin kullandığı, Bölüm/kira sahipliğini zorlamak için benzersiz bir tanımlayıcıdır (dönem değeri). [Createdönemler Chahize](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver) yöntemini kullanarak dönem tabanlı bir alıcı oluşturun. Bu yöntem, dönem tabanlı bir alıcı oluşturur. Alıcı, belirtilen tüketici grubundan belirli bir olay hub 'ı bölümü için oluşturulur.

Dönem özelliği, kullanıcılara, aşağıdaki kurallara göre herhangi bir zamanda bir tüketici grubunda yalnızca bir alıcı olmasını sağlar:

- Bir tüketici grubunda mevcut alıcı yoksa, Kullanıcı herhangi bir dönem değeri olan bir alıcı oluşturabilir.
- Bir dönem değeri olan bir alıcı varsa ve bir dönem değeri olan E2 (E1 <= E2) ile yeni bir alıcı oluşturulduysa, E1 ile alıcının bağlantısı otomatik olarak kesilir, E2 içeren alıcı başarıyla oluşturulur.
- Bir dönem değeri E1 olan bir alıcı varsa ve bir dönem değeri > E2 ile yeni bir alıcı oluşturulduysa, bu durumda E2 oluşturma işlemi şu hatayla başarısız olur: "dönemi olan bir alıcı zaten var.

### <a name="no-epoch"></a>Dönem yok
[Createreceiver](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver) yöntemini kullanarak dönem tabanlı olmayan bir alıcı oluşturursunuz. 

Akış işlemede, kullanıcıların tek bir tüketici grubunda birden çok alıcı oluşturmak istediğiniz bazı senaryolar vardır. Bu tür senaryoları desteklemek için, dönem olmadan bir alıcı oluşturabilir ve bu durumda tüketici grubunda 5 ' e kadar eşzamanlı alıcıya izin veririz.

### <a name="mixed-mode"></a>Karma mod
Dönemi olan bir alıcı oluşturduğunuz ve sonra aynı tüketici grubunda dönem olmayan veya bunun tersini alan uygulama kullanımını önermiyoruz. Ancak, bu davranış oluştuğunda hizmet aşağıdaki kuralları kullanarak bunu işler:

- Zaten dönem E1 ile oluşturulmuş bir alıcı varsa ve etkin bir şekilde olay alıyorsa ve yeni bir alıcı, dönem olmadan oluşturulursa, yeni alıcı oluşturulması başarısız olur. Dönem alıcıları, her zaman sistemde önceliğe sahip olur.
- Zaten bir dönem E1 ile oluşturulmuş ve bağlantısı kesilen bir alıcı varsa ve yeni bir MessagingFactory üzerinde dönem olmadan yeni bir alıcı oluşturulduysa yeni alıcı oluşturma işlemi başarılı olur. Burada sistemimizin, yaklaşık 10 dakika sonra "alıcı bağlantısının kesilmesi" algılayacağı bir desteklenmediği uyarısıyla vardır.
- Dönem olmadan oluşturulmuş bir veya daha fazla alıcı varsa ve dönem E1 ile yeni bir alıcı oluşturulduysa, tüm eski alıcıların bağlantısı kesilir.


> [!NOTE]
> Dönemleri kullanan uygulamalar için farklı tüketici grupları kullanmanızı ve hataları önlemek için dönemler kullanmayan dönemleri kullanmanızı öneririz. 


## <a name="next-steps"></a>Sonraki adımlar

Artık olay Işlemcisi konağını öğrenolduğunuza göre Event Hubs hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- Event Hubs kullanmaya başlayın
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Event Hubs programlama kılavuzu](event-hubs-programming-guide.md)
* [Event Hubs’da kullanılabilirlik ve tutarlılık](event-hubs-availability-and-consistency.md)
* [Event Hubs ile ilgili SSS](event-hubs-faq.yml)
* [GitHub 'daki Event Hubs örnekleri](https://github.com/Azure/azure-event-hubs/tree/master/samples)
