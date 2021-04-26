---
title: İleti aktarımları, kilitler ve kapatma Azure Service Bus
description: Bu makalede Azure Service Bus ileti aktarımları, kilitler ve kapatma işlemlerine ilişkin bir genel bakış sunulmaktadır.
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fbbcbf4a1920ee0e66a956443dcfb8a6a17af43
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306781"
---
# <a name="message-transfers-locks-and-settlement"></a>İleti aktarımları, kilitler ve kapatma

Service Bus gibi bir ileti aracısının merkezi özelliği, iletileri bir kuyruğa veya konuya kabul etmek ve daha sonra alınabilmeleri için kullanılabilir tutmaktır. *Send* iletisi, ileti aracısına ileti aktarımı için yaygın olarak kullanılan terimdir. *Alma* , bir iletinin alma istemcisine aktarılması için yaygın olarak kullanılan terimdir.

Bir istemci bir ileti gönderdiğinde, genellikle iletinin aracı tarafından düzgün şekilde aktarıldığını ve kabul edilip edilmediğini veya bir hata oluştuğunu bilmek ister. Bu olumlu veya negatif onay, istemcinin ve aracının aktarım durumu hakkında bilgi sahibi olduğunu istemci ve aracı tarafından yapılır. Bu nedenle, ' *kapatma* olarak adlandırılır.

Benzer şekilde, aracı bir istemciye bir ileti aktarırken, aracı ve istemci, iletinin başarıyla işlenip işlenmeyeceğini ve bu nedenle kaldırılıp kaldırılmadığını ya da ileti tesliminin ya da işlemenin başarısız olup olmadığını anlamak ister ve bu nedenle ileti yeniden teslim edilmesi gerekebilir.

## <a name="settling-send-operations"></a>Gönderme işlemlerini kapatma

Desteklenen Service Bus API istemcilerinden herhangi birini kullanarak, işlemleri Service Bus içine gönder, API işleminin Service Bus gelmesi için bir kabul sonucu bekleyeceğini ve sonra Gönder işlemini tamamlayarak, her zaman açıkça kapatılır.

İleti Service Bus tarafından reddedilirse, reddetme bir hata göstergesi ve içinde **İzleme kimliği** olan metin içerir. Reddetme, işlemin başarılı olma beklentisiyle yeniden denenip denenmeyeceğini belirten bilgileri de içerir. İstemcide, bu bilgiler özel bir duruma getirilir ve gönderme işlemi çağıranına yükseltilir. İleti kabul edildiyse, işlem sessizce tamamlanır.

.NET Standard, Java, JavaScript, Python ve go istemcileri için özel protokol olan AMQP protokolünü kullanırken ve [.NET Framework istemcisi için bir seçenek](service-bus-amqp-dotnet.md), ileti aktarımları ve kapatmalar ardışık düzen ve zaman uyumsuz olarak yapılır. Zaman uyumsuz programlama modeli API türevlerini kullanmanızı öneririz.

Bir gönderici, SBMP protokolünde veya HTTP 1,1 ile büyük bir süre olacağı gibi, her bir iletinin onaylanmasına gerek kalmadan, her iletiyi almak için beklemek zorunda kalmadan, hatta her ileti için bir araya daha fazla ileti yerleştirebilir. İlgili iletiler kabul edildiği ve depolandığı, bölümlenmiş varlıklarda veya farklı varlıklara gönderme işlemi çakıştığında, bu zaman uyumsuz gönderme işlemleri tamamlanır. Tamamlamalar, özgün gönderme siparişinden da oluşabilir.

Gönderme işlemlerinin sonucunu işleme stratejisi, uygulamanız için anında ve önemli performans etkisine sahip olabilir. Bu bölümdeki örnekler C# dilinde yazılmıştır ve Java futures, Java Monos, JavaScript 'e ve diğer dillerdeki eşdeğer kavramlara uygulanır.

Uygulama bir sonraki ileti, zaman uyumlu veya zaman uyumsuz API şekillerini göndermeden önce, burada düz bir döngüyle gösterilen ve her gönderme işleminin tamamlanmasını beklemek zorunda olsaydı, 10 ileti gönderildiğinde yalnızca 10 sıralı tam gidiş dönüş sonrasında tamamlanır.

Bir şirket içi siteden Service Bus ve her iletiyi kabul etmesi ve depolaması için yalnızca 10 Service Bus MS sağlayan 70 milisaniyelik bir TCP gidiş dönüş gecikme süresi ile, aşağıdaki döngü en az 8 saniye sürer, yük aktarma süresini veya olası yol tıkanıklık efektlerini saymaz:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Uygulama, arka arkaya 10 zaman uyumsuz gönderme işlemini başlatır ve ilgili tamamlamalarını ayrı olarak bekleliyorsa, bu 10 gönderme işlemlerine ilişkin gidiş dönüş süresi örtüşüyor. 10 ileti hemen art arda aktarılır, hatta TCP çerçevelerini paylaşıma alabilir ve genel aktarım süresi büyük ölçüde, aracıya aktarılan iletilerin alması için gereken ağla ilgili zamana bağlıdır.

Önceki döngü için aynı varsayımlar yapılıyor, aşağıdaki döngü için toplam çakışan yürütme süresi bir saniye içinde iyi kalabilirler:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Tüm zaman uyumsuz programlama modellerinin, bekleyen işlemleri tutan bir dizi bellek tabanlı, gizli iş kuyruğunu kullandığından emin olmak önemlidir. Send API 'SI döndüğünde, Gönder görevi o iş kuyruğunda sıraya alınır ancak protokol hareketi, görevin çalışmaya dönüşü bir kez gönderilir. İletilerin ani ve güvenilir bir sorun olduğu konusunda eğilimi gösteren kod için, gönderilen tüm iletiler factually olana kadar bellek çektiğinden, her seferinde çok fazla ileti "uçuşmaya" koyduğundan dikkatli olunmalıdır.

Aşağıdaki kod parçacığında C# dilinde gösterildiği gibi Semaforlar, gerektiğinde uygulama düzeyi azaltmayı etkinleştiren eşitleme nesneleridir. Semaforun bu kullanımı, en fazla 10 iletinin bir kez Uçuşta olmasını sağlar. 10 kullanılabilir semafor kilitlerinin biri göndermeden önce alınır ve gönderme işlemi olarak serbest bırakılır. Döngü boyunca geçen 11, önceki gönderiden en az biri tamamlanana kadar bekler ve ardından kilidini kullanılabilir hale getirir:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Uygulamalar **hiçbir zaman** zaman uyumsuz gönderme işlemini işlemin sonucunu almadan bir "ateş ve unutmadan" bir biçimde başlatmamalıdır. Bunun yapılması, iç ve görünmeyen görev kuyruğunu bellek tükenmesi için yükleyebilir ve uygulamanın gönderme hatalarını algılamasını önler:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Alt düzey AMQP istemcisiyle, Service Bus Ayrıca "önceden kapatılan" aktarımları kabul eder. Önceden kapatılan bir aktarım, sonucun istemciye geri bildirilmediği ve iletinin gönderildiğinde kapatılan kabul edildiği bir ateş ve-unut işlemidir. İstemciye geri bildirimde bulunamaması Ayrıca, Tanılama için kullanılabilir bir kullanılabilir veri yoktur, bu da bu modun Azure desteği aracılığıyla yardım için uygun olmadığı anlamına gelir.

## <a name="settling-receive-operations"></a>Alma işlemlerini kapatma

Alma işlemleri için Service Bus API istemcileri iki farklı açık modu etkinleştirir: *al-ve-DELETE* ve *Peek-kilitle*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

**Alma ve silme** modu, aracıya gönderildiği sırada, alıcı istemciye gönderdiği tüm iletileri kabul etmeyi söyler. Diğer bir deyişle, aracı bunu kabloya yerleştirdiğinden ileti tüketilen kabul edilir. İleti aktarımı başarısız olursa ileti kaybedilir.

Bu modun üstü, alıcının ileti üzerinde daha fazla işlem yapması gerekmez ve ayrıca kapatmanın sonucunu beklerken yavaşlamaz. Tek tek iletilerde içerilen verilerin değeri düşük ve/veya yalnızca çok kısa bir süre için anlamlı ise, bu mod makul bir seçimdir.

### <a name="peeklock"></a>PeekLock

**Peek-kilit** modu, alıcı istemcinin alınan iletileri açıkça kapatmak istediğini aracıya söyler. İleti, alıcının işlemesi için kullanılabilir hale getirilir, böylece diğer, rakip alıcıların bunu görebilmesi için hizmette özel bir kilit altında tutulur. Kilit süresi başlangıçta sırada veya abonelik düzeyinde tanımlanır ve kilidin sahibi olan istemci tarafından genişletilebilir. Kilitleri yenileme hakkında daha fazla bilgi için bu makaledeki [kilitleri yenileme](#renew-locks) bölümüne bakın. 

Bir ileti kilitliyken, aynı kuyruktan veya abonelikten alınan diğer istemciler kilitleri alabilir ve etkin kilit altında olmayan bir sonraki kullanılabilir iletiyi alabilir. Bir iletideki kilit açıkça serbest bırakıldığında veya kilidin süresi dolarsa, ileti yeniden teslim edilmek üzere alma sırasının önüne veya yanına doğru bir şekilde yeniden açılır.

İleti alıcılar tarafından tekrararda serbest bırakıldığında veya kilit, tanımlanan sayıda ([en fazla teslimat sayısı](service-bus-dead-letter-queues.md#maximum-delivery-count)) için kilitlenme zamanına izin verdiği zaman, ileti kuyruktan veya abonelikten otomatik olarak kaldırılır ve ilişkili atılacak ileti kuyruğuna yerleştirilir.

Alıcı istemci `Complete` ileti için API 'yi çağırdığında pozitif bir bildirim içeren alındı iletisini kapatmayı başlatır. Aracının iletinin başarıyla işlendiğini ve iletinin kuyruktan veya abonelikten kaldırıldığını gösterir. Aracı, kapatmanın gerçekleştirilip gerçekleştirilmeyeceğini belirten bir Yanıt ile alıcının kapatma amacını yanıtlar.

Alıcı istemci bir iletiyi işleyemediğinde ancak iletinin yeniden teslim edilmesini istiyorsa ileti için API 'yi çağırarak iletiyi anında serbest bırakmaya ve kilidinin doğrudan yönelmesini isteyebilir `Abandon` ya da hiçbir şey yapmaz ve kilidin geçmesini sağlayabilirsiniz.

Alıcı istemci bir iletiyi işleyemezse ve iletiyi yeniden teslim etmeyi ve işlemi yeniden denemeyi bilmezse iletiyi reddedebilir ve bu ileti, ileti üzerinde API 'yi çağırarak iletiyi geri alabilir ve bu da ileti, teslim `DeadLetter` edilemeyen ileti sırasından alınmış bir neden kodu da dahil olmak üzere özel bir özellik ayarlamaya olanak tanır.

Bir kapanış özel durumu ertelenmiştir ve [ayrı bir makalede](message-deferral.md)ele alınmıştır.

`Complete`, `Deadletter` , Veya `RenewLock` işlemleri ağ sorunları nedeniyle başarısız olabilir, tutulan kilidin süresi dolmuşsa veya kapatmayı önleyen başka hizmet tarafı koşulları vardır. İkinci durumlardan birinde, hizmet API istemcilerinde özel durum olarak yüzey sağlayan negatif bir bildirim gönderir. Nedeni bozuk bir ağ bağlantııysa, farklı bir bağlantıda var olan AMQP bağlantılarının kurtarılmasını desteklemediği Service Bus kilit bırakılır.

`Complete`İşlem başarısız olursa, genellikle ileti işlemenin çok sonunda ve bazı durumlarda çalışma işleminin süresi dolduktan sonra ortaya çıkan uygulama, işin durumunu korur ve ikinci bir kez teslim edildiğinde aynı iletiyi yok sayar ya da ileti yeniden teslim edildiğinde yeniden denemeler yapıp denemeyeceğine karar verebilir.

Yinelenen ileti teslimlerini tanımlamaya yönelik tipik mekanizma ileti kimliğini denetleyerek, büyük olasılıkla kaynak işlemden tanımlayıcı ile hizalanmış bir şekilde, gönderen tarafından benzersiz bir değere ayarlanmalıdır. Bir iş Zamanlayıcı büyük olasılıkla ileti Kimliğini verilen çalışan bir çalışana atamaya çalıştığı işin tanımlayıcısına ayarlayabilir ve bu iş zaten yapıldıysa çalışan, iş atamasının ikinci oluşumunu yoksayar.

> [!IMPORTANT]
> PeekLock 'in ileti üzerinde elde edilen kilidinin geçici olduğunu ve aşağıdaki koşullarda kaybolabileceğini unutmayın
>   * Hizmet güncelleştirmesi
>   * İşletim sistemi güncelleştirmesi
>   * Kilidi tutarken varlıktaki özellikleri değiştirme (kuyruk, konu, abonelik).
>
> Kilit kaybedildiği zaman, Azure Service Bus istemci uygulama kodu üzerinde ortaya çıkacak bir LockLostException oluşturur. Bu durumda, istemcinin varsayılan yeniden deneme mantığı otomatik olarak başlamalıdır ve işlemi yeniden dener.

## <a name="renew-locks"></a>Kilitleri Yenile
Kilit süresi için varsayılan değer **30 saniyedir**. Kuyruk veya abonelik düzeyinde kilit süresi için farklı bir değer belirtebilirsiniz. Kilidin sahibi olan istemci, alıcı nesnesindeki yöntemleri kullanarak ileti kilidini yenileyebilirler. Bunun yerine, otomatik kilit yenileme özelliğini kullanarak kilidi yenilemek istediğiniz zaman süresini belirleyebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar
- Bir kapanış özel durumu erteleme olur. Ayrıntılar için bkz. [ileti erteleme](message-deferral.md) . 
- Atılacak ileti alma hakkında bilgi edinmek için bkz. [atılacak ileti sıraları](service-bus-dead-letter-queues.md).
- Genel olarak Service Bus mesajlaşma hakkında daha fazla bilgi edinmek için bkz. [Service Bus kuyruklar, konular ve abonelikler](service-bus-queues-topics-subscriptions.md)