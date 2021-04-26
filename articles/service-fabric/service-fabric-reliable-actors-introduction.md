---
title: Service Fabric Reliable Actors genel bakış
description: Sanal aktör düzenine göre Service Fabric Reliable Actors programlama modeline giriş.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a8a7003a69deaf6b74d6fbb8a3cf84b0a78eecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576392"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors giriş
Reliable Actors, [sanal aktör](https://research.microsoft.com/en-us/projects/orleans/) düzenine göre Service Fabric bir uygulama çerçevesidir. Reliable Actors API 'SI, Service Fabric tarafından sağlanan ölçeklenebilirlik ve güvenilirlik garantisi üzerine inşa olan tek iş parçacıklı bir programlama modeli sağlar.

## <a name="what-are-actors"></a>Aktör nedir?
Aktör, tek iş parçacıklı yürütme ile yalıtılmış, bağımsız bir işlem ve durum birimidir. [Aktör](https://en.wikipedia.org/wiki/Actor_model) modeli, bu aktörlerin çok sayıda aynı anda ve birbirinden bağımsız olarak yürütebileceği eşzamanlı veya dağıtılmış sistemler için bir hesaplama modelidir. Aktörler birbirleriyle iletişim kurabilir ve daha fazla aktör oluşturabilir.

### <a name="when-to-use-reliable-actors"></a>Ne zaman kullanılacağı Reliable Actors
Service Fabric Reliable Actors aktör tasarım deseninin bir uygulamasıdır. Herhangi bir yazılım tasarımı modelinde olduğu gibi, belirli bir model kullanılıp kullanılmayacağını kararla, yazılım tasarımı sorununun modele uygun olup olmadığına göre yapılır.

Aktör tasarım deseninin bir dizi dağıtılmış sistem sorunu ve senaryosuna uygun olmasına rağmen, düzenin kısıtlamaları ve bunu uygulayan çerçevenin yapılması gerekir. Genel kılavuz olarak, sorun veya senaryonuzu modellemek için aktör modelini göz önünde bulundurun:

* Sorun alanınız, büyük bir sayı (binlerce veya daha fazla), küçük, bağımsız ve yalıtılmış birim ve mantık birimleri içerir.
* Dış bileşenlerden önemli bir etkileşim gerektirmeyen tek iş parçacıklı nesnelerle çalışmak istiyorsunuz, bu da durumu bir aktör kümesi genelinde sorgulama dahil.
* Aktör örneklerinizin, g/ç işlemleri vererek öngörülemeyen gecikmelerle arayanlara engel olmaz.

## <a name="actors-in-service-fabric"></a>Service Fabric aktörler
Service Fabric, aktörler Reliable Actors Framework 'te uygulanır: [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)en üstünde oluşturulan aktör temelli bir uygulama çerçevesi. Yazdığınız her güvenilir aktör hizmeti, aslında bölümlenmiş ve durum bilgisi olan güvenilir bir hizmettir.

Her aktör bir aktör türünün örneği olarak tanımlanır, .NET nesnesinin bir .NET türünün örneği olduğu şekilde aynıdır. Örneğin, bir Hesaplayıcı işlevselliğini uygulayan bir aktör türü olabilir ve bir küme genelinde çeşitli düğümlerde dağıtılan bu türden birçok aktör olabilir. Her bir aktör bir aktör KIMLIĞI tarafından benzersiz şekilde tanımlanır.

## <a name="actor-lifetime"></a>Aktör ömrü
Service Fabric aktörler sanal, yani yaşam sürelerinin bellek içi gösterimine bağlı olmadığı anlamına gelir. Sonuç olarak, açıkça oluşturulması veya yok edilmesi gerekmez. Reliable Actors çalışma zamanı, aktör KIMLIĞI için bir istek aldığında bir aktöri otomatik olarak etkinleştirir. Aktör bir süre boyunca kullanılmıyorsa, Reliable Actors çalışma zamanı atık-bellek içi nesneyi toplar. Ayrıca, aktörin varlığına daha sonra yeniden etkinleştirilmesi gerekir. Daha ayrıntılı bilgi için bkz. [aktör yaşam döngüsü ve çöp toplama](service-fabric-reliable-actors-lifecycle.md).

Bu sanal aktör yaşam süresi soyutlama, sanal aktör modelinin bir sonucu olarak bazı uyarılar taşır ve Reliable Actors uygulama bu modelden zaman içinde farklılık gösterir.

* Aktör KIMLIĞI bir ileti ilk kez gönderildiğinde aktör otomatik olarak etkinleştirilir (aktör nesnesi oluşturulmasını sağlar). Bir süre sonra aktör nesnesi atık olarak toplanır. Gelecekte aktör KIMLIĞINI yeniden kullanmak yeni bir aktör nesnesinin oluşturulmasına neden olur. Aktörün durumu, durum Yöneticisi 'nde depolanırken nesnenin ömrünü aşacak.
* Aktör KIMLIĞI için herhangi bir aktör yöntemi çağrılması, bu aktörü etkinleştirir. Bu nedenle, aktör türlerinin Oluşturucusu çalışma zamanı tarafından örtük olarak çağırılır. Bu nedenle, istemci kodu aktör türünün oluşturucusuna parametreleri geçirebilse de, parametrelerin hizmetin kendisi tarafından oluşturucusuna geçirilmesi istenebilir. Bunun sonucunda, aktör kısmen başlatılmış bir durumda, diğer yöntemlerin çağrıldığı zamana göre oluşturulabilir ve aktör istemciden başlatma parametreleri gerektiriyorsa. İstemciden bir aktörün etkinleştirilmesi için tek bir giriş noktası yoktur.
* Reliable Actors, aktör nesneleri örtülü olarak oluştursa da, aktör ve durumunu açıkça silme olanağınız vardır.

## <a name="distribution-and-failover"></a>Dağıtım ve yük devretme
Ölçeklenebilirlik ve güvenilirlik sağlamak için Service Fabric aktörleri küme genelinde dağıtır ve bunları otomatik olarak başarısız düğümlerden, gerektiği gibi sağlıklı bir şekilde geçirir. Bu, [bölümlenmiş ve durum bilgisi olan güvenilir bir hizmet](service-fabric-concepts-partitioning.md)üzerinde soyutlamadır. Dağıtım, ölçeklenebilirlik, güvenilirlik ve otomatik yük devretme, aktörlerin *aktör hizmeti* olarak adlandırılan durum bilgisi olan güvenilir bir hizmetin içinde çalıştığı gerçeğini sağlamıştır.

Aktör, aktör hizmetinin bölümlerine dağıtılır ve bu bölümler bir Service Fabric kümesindeki düğümlere dağıtılır. Her hizmet bölümü bir aktör kümesi içerir. Service Fabric, hizmet bölümlerinin dağıtımını ve yük devretmesini yönetir.

Örneğin, varsayılan aktör bölümü yerleştirmesi kullanılarak üç düğüme dağıtılan dokuz bölümlü bir aktör hizmeti şu şekilde dağıtılır:

![Reliable Actors dağıtım][2]

Aktör çerçevesi, bölüm düzenini ve anahtar aralığı ayarlarını sizin için yönetir. Bu, bazı seçimleri basitleştirir ancak bazı konuları da taşır:

* Reliable Services bir bölümleme şeması, anahtar aralığı (Aralık bölümleme şeması kullanırken) ve bölüm sayısı seçmenize olanak sağlar. Reliable Actors, Aralık bölümleme şeması (Tekdüzen Int64 şeması) ile kısıtlıdır ve tam Int64 anahtar aralığını kullanmanızı gerektirir.
* Varsayılan olarak, aktör, Tekdüzen dağıtımına neden olan bölümlere rastgele yerleştirilir.
* Aktör rastgele yerleştirildiğinden, aktör işlemlerinin her zaman, yöntem çağrı verilerinin serileştirme ve serisini kaldırma, gecikme süresi ve ek yükü dahil, her zaman ağ iletişimi gerektirmesinin beklenmelidir.
* Gelişmiş senaryolarda, belirli bölümlerle eşlenen Int64 aktör kimliklerini kullanarak aktör bölümü yerleşimini denetlemek mümkündür. Ancak bunu yapmak, bölümler arasında aktörlerin dengesiz bir dağıtımına neden olabilir.

Aktör hizmetlerinin nasıl bölümlenme hakkında daha fazla bilgi için [aktörlerin bölümlendirme kavramlarını](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)inceleyin.

## <a name="actor-communication"></a>Aktör iletişimi
Aktör etkileşimleri, arabirimini uygulayan aktör tarafından paylaşılan bir arabirimde ve bir aktör için aynı arabirim aracılığıyla bir proxy alan istemci tarafından tanımlanır. Bu arabirim aktör yöntemlerini zaman uyumsuz olarak çağırmak için kullanıldığından, arabirimdeki her yöntemin görev döndürmesi gerekir.

Yöntem etkinleştirmeleri ve yanıtları sonuç olarak kümedeki ağ isteklerine neden olur, bu nedenle döndürülen görevlerin bağımsız değişkenleri ve sonuç türleri platform tarafından seri hale getirilebilir olmalıdır. Özellikle, bunların [seri hale getirilebilir veri anlaşması](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)olmaları gerekir.

### <a name="the-actor-proxy"></a>Aktör proxy 'si
Reliable Actors istemci API 'SI, bir aktör örneği ve aktör istemcisi arasında iletişim sağlar. Bir aktör ile iletişim kurmak için, istemci aktör arabirimini uygulayan bir aktör proxy nesnesi oluşturur. İstemci, proxy nesnesi üzerinde Yöntemler çağırarak aktörle etkileşime girer. Aktör proxy 'si, istemci-aktör ve aktör-aktör iletişimi için kullanılabilir.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Aktör proxy nesnesini oluşturmak için kullanılan iki bilgi parçasının aktör KIMLIĞI ve uygulama adı olduğunu unutmayın. Aktör KIMLIĞI aktöri benzersiz şekilde tanımlar, ancak uygulama adı aktörün dağıtıldığı [Service Fabric uygulamayı](service-fabric-reliable-actors-platform.md#application-model) tanımlar.

`ActorProxy`İstemci tarafındaki (C#)/ `ActorProxyBase` (Java) sınıfı, aktöri kimliğe göre bulmak için gerekli çözümü gerçekleştirir ve bununla bir iletişim kanalı açar. Ayrıca, iletişim arızaları ve yük devretme durumlarında aktöri bulmayı yeniden dener. Sonuç olarak, ileti teslimi aşağıdaki özelliklere sahiptir:

* İleti teslimi en iyi çabadır.
* Aktörler aynı istemciden yinelenen iletiler alabilir.

## <a name="concurrency"></a>Eşzamanlılık
Reliable Actors çalışma zamanı, aktör yöntemlerine erişmek için basit bir açma tabanlı erişim modeli sağlar. Bu, bir aktör nesnesinin kodu içinde herhangi bir zamanda birden fazla iş parçacığının etkin olamayacağı anlamına gelir. Veri erişimi için eşitleme mekanizmalarına gerek olmadığı için, çift tabanlı erişim, eşzamanlı sistemleri büyük ölçüde basitleştirir. Ayrıca sistemler, her aktör örneğinin tek iş parçacıklı erişim doğası için özel hususlar ile tasarlanmalıdır.

* Tek bir aktör örneği aynı anda birden fazla isteği işleyemez. Bir aktör örneği, eşzamanlı isteklerin işlenmesi bekleniyorsa üretilen iş sorununa neden olabilir.
* İki aktör arasında döngüsel bir istek olduğunda aktör, bir dış istek aynı anda aktörlerin birine eşzamanlı olarak yapıldığında birbirini karşılıklı hale getirilebilir. Aktör çalışma zamanı, aktör çağrılarında otomatik olarak zaman aşımına uğrar ve olası kilitlenme durumlarını kesintiye uğratmak için çağırana özel durum oluşturur.

![Reliable Actors iletişim][3]

### <a name="turn-based-access"></a>Açma tabanlı erişim
Bir etkinleştirme, diğer aktörlerden veya istemcilerden gelen bir isteğe yanıt olarak bir aktör yönteminin tamamen yürütülmesinden veya bir [Zamanlayıcı/anımsatıcı](service-fabric-reliable-actors-timers-reminders.md) geri çağrısının tamamen yürütülmesinden oluşur. Bu yöntemler ve geri çağrılar zaman uyumsuz olsa da aktör çalışma zamanı bunları toplamaz. Yeni bir dönüşme izin verilinceye kadar bir sırayla tam olarak bitmesi gerekir. Diğer bir deyişle, bir yönteme veya geri çağrıya yönelik yeni bir çağrıya izin verilmeden önce yürütülmekte olan bir aktör yöntemi veya zamanlayıcı/anımsatıcı geri çağırma işlemi tamamen tamamlanmış olmalıdır. Bir yöntem veya geri çağırma, yürütme yöntemden veya geri aramadan döndürülürse ve yöntem veya geri çağırma tarafından döndürülen görev tamamlandığında tamamlandı olarak değerlendirilir. Farklı yöntemler, zamanlayıcılar ve geri çağırmaların yanında, çift yönlü eşzamanlılık olduğunu vurgulamayı unutmayın.

Aktör çalışma zamanı, bir aktör başına kilit alarak ve kilidin sonunda kilidi serbest bırakarak çift tabanlı eşzamanlılık uygular. Bu nedenle, açık tabanlı eşzamanlılık aktör genelinde değil, aktör temelinde zorlanır. Aktör yöntemleri ve Zamanlayıcı/anımsatıcı geri çağırmaları farklı aktör adına aynı anda çalıştırılabilir.

Aşağıdaki örnekte yukarıdaki kavramlar gösterilmektedir. İki zaman uyumsuz Yöntem (deyin, *Method1* ve *Method2*), bir süreölçeri ve bir anımsatıcı uygulayan bir aktör türü düşünün. Aşağıdaki diyagramda, bu aktör türüne ait olan iki aktör (*ActorId1* ve *ActorId2*) adına bu yöntemlerin ve geri çağırmaların yürütülmesi için bir zaman çizelgesi örneği gösterilmektedir.

![Reliable Actors çalışma zamanı açma tabanlı eşzamanlılık ve erişim][1]

Bu diyagram aşağıdaki kuralları izler:

* Her dikey çizgi, belirli bir aktör adına bir yöntem veya geri çağırma yürütme mantıksal akışını gösterir.
* Her dikey satırda işaretlenen olaylar kronolojik sırada oluşur ve daha yeni olaylar daha eski olanlarla daha sonra gerçekleşirler.
* Farklı aktörlerin karşılık gelen zaman çizelgeleri için farklı renkler kullanılır.
* Vurgulama, bir yöntem veya geri çağırma adına aktör başına kilidin tutulduğu süreyi belirtmek için kullanılır.

Göz önünde bulundurmanız gereken bazı önemli noktaları:

* *Method1* , istemci isteğine *Xyz789* yanıt olarak *ActorId2* adına yürütülürken, *Ayrıca Method1 tarafından yürütülmesini gerektiren başka* bir istemci isteği (*abc123*) varolur *.* Ancak, *Method1* 'in ikinci yürütmesi, önceki yürütme bitinceye kadar başlamaz. Benzer şekilde, *ActorId2* tarafından kaydedilen bir anımsatıcı, *Method1* *istemci isteğine yanıt* olarak yürütüldüğünde ateşlenir. Anımsatıcı geri çağırması yalnızca *Method1* yürütmeleri tamamlandıktan sonra yürütülür. Bunun hepsi, *ActorId2* için zorunlu tabanlı eşzamanlılık nedeniyle yapılır.
* Benzer şekilde, ActorId1 için açık tabanlı eşzamanlılık, *Method1*, *Method2* ve zamanlayıcı geri çağrısının bir seri bir *biçimde olması adına* bir şekilde yürütülmesi tarafından gösterildiği gibi, için de zorlanır.
* *ActorId1* adına *Method1* yürütmesi, *ActorId2* adına yürütme ile çakışıyor. Bunun nedeni, çift tabanlı eşzamanlılık yalnızca aktör içinde zorlanır ve aktörler arasında değil.
* Yöntem/geri çağırma yürütmelerinin bazılarında, yöntem `Task` `CompletableFuture` /geri çağırma tarafından döndürülen (C#)/(Java), yöntem dönüşinden sonra tamamlanır. Bazı bazılarında, zaman uyumsuz işlem, metodun/geri aramanın döndürdüğü zaman tarafından zaten bitmiştir. Her iki durumda da, aktör başına kilit yalnızca Yöntem/geri çağırma, ve zaman uyumsuz işlem bittikten sonra serbest bırakılır.

### <a name="reentrancy"></a>Yeniden giriş
Aktör çalışma zamanı varsayılan olarak yeniden giriş yapılmasına izin verir. Yani aktör bir aktör yöntemi, aktör *B* üzerinde bir *yöntemi çağırırsa,* bu yöntem *bir aktör* üzerinde başka bir yöntemi çağırırsa, bu yöntemin çalışmasına izin verilir. Bunun nedeni, aynı mantıksal çağrı zinciri bağlamının bir parçası olması olabilir. Tüm Zamanlayıcı ve anımsatıcı çağrıları yeni mantıksal çağrı bağlamıyla başlar. Daha fazla ayrıntı için [Reliable Actors yeniden](service-fabric-reliable-actors-reentrancy.md) giriş bölümüne bakın.

### <a name="scope-of-concurrency-guarantees"></a>Eşzamanlılık garantisi kapsamı
Aktör çalışma zamanı, bu yöntemlerin çağrılmasını denetlediği durumlarda bu eşzamanlılık garantisi sağlar. Örneğin, bir istemci isteğine yanıt olarak yapılan yöntem etkinleştirmeleri ve Zamanlayıcı ve anımsatıcı geri çağırmaları için bu onay garantisi sağlar. Ancak aktör kodu, aktör çalışma zamanı tarafından belirtilen mekanizmaların dışında doğrudan bu yöntemleri çağırdıysa, çalışma zamanı hiçbir eşzamanlılık garantisi sağlayamaz. Örneğin, yöntemi aktör yöntemleri tarafından döndürülen görevle ilişkilendirilmemiş bir görev bağlamında çağrılırsa, çalışma zamanı eşzamanlılık garantisi sağlayamaz. Yöntem, aktörün kendi kendine oluşturduğu bir iş parçacığından çağrılırsa, çalışma zamanı eşzamanlılık garantisi da sağlayamaz. Bu nedenle, arka plan işlemleri gerçekleştirmek için aktör, [aktör zamanlayıcıları ve](service-fabric-reliable-actors-timers-reminders.md) çift tabanlı eşzamanlılık kullanan aktör anımsatıcıları kullanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar
İlk Reliable Actors hizmetinizi oluşturmaya başlayın:
   * [.NET üzerinde Reliable Actors kullanmaya başlama](service-fabric-reliable-actors-get-started.md)
   * [Java 'da Reliable Actors kullanmaya başlama](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
