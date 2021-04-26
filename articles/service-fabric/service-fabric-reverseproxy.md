---
title: Azure Service Fabric ters proxy
description: Kümenin içinden ve dışından mikro hizmetlere iletişim için Service Fabric ters ara sunucusunu kullanın.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791113"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric ters proxy
Azure 'da yerleşik olarak bulunan ters proxy Service Fabric, bir Service Fabric kümesinde çalışan mikro hizmetlerin HTTP uç noktalarına sahip diğer hizmetleri bulmasına ve iletişim kurmasına yardımcı olur.

## <a name="microservices-communication-model"></a>Mikro Hizmetler iletişim modeli
' Service Fabric deki mikro hizmetler, kümedeki düğümlerin bir alt kümesi üzerinde çalışır ve çeşitli nedenlerle düğümler arasında geçiş yapabilir. Sonuç olarak, mikro hizmetler için uç noktalar dinamik olarak değişebilir. Mikro hizmet, kümedeki diğer hizmetleri bulup iletişim kurmak için aşağıdaki adımlardan ilermelidir:

1. Adlandırma hizmeti aracılığıyla hizmet konumunu çözümleyin.
2. Hizmetine bağlanın.
3. Önceki adımları hizmet çözümlemesi uygulayan bir döngüde sarın ve bağlantı hatalarıyla ilgili yeniden deneme ilkeleri

Daha fazla bilgi için bkz. [Hizmetler Ile bağlanma ve iletişim kurma](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Ters proxy kullanarak iletişim kurma
Ters proxy, her düğümde çalışan ve uç nokta çözümleme, otomatik yeniden deneme ve diğer bağlantı başarısızlıklarını istemci hizmetleri adına işleyen bir hizmettir. Ters proxy, istemci hizmetlerinden gelen istekleri işlediği için çeşitli ilkeler uygulayacak şekilde yapılandırılabilir. Ters proxy kullanmak, istemci hizmetinin istemci tarafı HTTP iletişim kitaplıklarını kullanmasına izin verir ve hizmette özel çözüm ve yeniden deneme mantığı gerektirmez. 

Ters proxy, istemci hizmetlerinin diğer hizmetlere istek göndermek için kullanması için yerel düğümde bir veya daha fazla uç nokta gösterir.

![İç iletişim][1]

> [!NOTE]
> **Desteklenen platformlar**
>
> Service Fabric ters proxy Şu anda aşağıdaki platformları desteklemektedir
> * *Windows kümesi*: Windows 8 ve üzeri ya da windows Server 2012 ve üzeri
> * *Linux kümesi*: ters ara sunucu şu anda Linux kümelerinde kullanılamıyor
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Küme dışından mikro hizmetlere ulaşma
Mikro hizmetler için varsayılan dış iletişim modeli, her bir hizmete doğrudan dış istemcilerden erişilemeyen bir katılım modelidir. Mikro hizmetler ve dış istemciler arasındaki bir ağ sınırı olan [Azure Load Balancer](../load-balancer/load-balancer-overview.md), ağ adresi çevirisi gerçekleştirir ve dış ISTEKLERI iç IP 'ye iletir: bağlantı noktası uç noktaları. Mikro hizmet uç noktasını dış istemciler için doğrudan erişilebilir hale getirmek için, önce Load Balancer, trafiği, hizmetin kümede kullandığı her bağlantı noktasına iletecek şekilde yapılandırmanız gerekir. Ayrıca, çoğu mikro hizmet, özellikle de durum bilgisi olan mikro hizmetler, kümenin tüm düğümlerinde etkin değildir. Mikro hizmetler, yük devretmede düğümler arasında hareket edebilir. Böyle durumlarda Load Balancer, trafiği iletmesi gereken çoğaltmaların hedef düğümünün konumunu etkin bir şekilde belirleyemez.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Küme dışından ters proxy aracılığıyla mikro hizmetlere ulaşma
Load Balancer ' de tek bir hizmetin bağlantı noktasını yapılandırmak yerine, Load Balancer yalnızca ters proxy 'nin bağlantı noktasını yapılandırabilirsiniz. Bu yapılandırma, küme dışındaki istemcilerin ek yapılandırma gerektirmeden ters proxy 'yi kullanarak küme içindeki hizmetlere ulaşmasını sağlar.

![Dış iletişim][0]

> [!WARNING]
> Load Balancer ' de ters proxy 'nin bağlantı noktasını yapılandırdığınızda, kümedeki bir HTTP uç noktasını kullanıma sunan tüm mikro hizmetler, küme dışından adreslenemez. Diğer bir deyişle, mikro hizmetlerin iç olduğu, belirlenen kötü amaçlı kullanıcı tarafından bulunabilir olabileceği anlamına gelir. Bu potansiyel olarak yararlanılabilen ciddi güvenlik açıkları sunmaktadır. Örneğin:
>
> * Kötü niyetli bir Kullanıcı, yeterince sıkı bir saldırı yüzeyi olmayan bir iç hizmeti tekrar çağırarak bir hizmet reddi saldırısı başlatabilir.
> * Kötü niyetli bir Kullanıcı, dahili bir hizmete hatalı oluşturulmuş paketler sunabilir ve bu durum istenmeden davranışa neden olabilir.
> * İç olması amaçlanan bir hizmet, küme dışındaki hizmetlere sunulmayan özel veya hassas bilgileri döndürebilir ve bu nedenle bu hassas bilgileri kötü amaçlı bir kullanıcıya ortaya çıkarabiliriz. 
>
> Ters proxy bağlantı noktasını genel yapmadan önce kümeniz ve üzerinde çalışan uygulamalar için olası güvenlik kollerini tam olarak anladığınızdan ve azaltmanıza emin olun. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Ters proxy 'yi kullanarak hizmetleri adresleme için URI biçimi
Ters proxy, gelen isteğin iletilmesi gereken hizmet bölümünü belirlemek için belirli bir Tekdüzen Kaynak tanımlayıcısı (URI) biçimi kullanır:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** Ters proxy, HTTP veya HTTPS trafiğini kabul edecek şekilde yapılandırılabilir. HTTPS iletimi için, HTTPS 'yi dinlemek üzere ters ara sunucu kurulumuna sahip olduğunuzda [ters ara sunucu ile güvenli bir hizmete bağlanma](service-fabric-reverseproxy-configure-secure-communication.md) bölümüne bakın.
* **Küme tam etki alanı adı (FQDN) | Iç IP:** Dış istemciler için ters proxy 'yi, mycluster.eastus.cloudapp.azure.com gibi küme etki alanı üzerinden erişilebilmeleri için yapılandırabilirsiniz. Varsayılan olarak, ters proxy her düğümde çalışır. İç trafik için, ters ara sunucuya, localhost veya 10.0.0.1 gibi herhangi bir iç düğüm IP 'si üzerinden ulaşılanmış olabilir.
* **Bağlantı noktası:** Bu, ters proxy için belirtilen 19081 gibi bağlantı noktasıdır.
* **ServiceInstanceName:** Bu, "Fabric:/" olmadan ulaşmaya çalıştığınız dağıtılan hizmet örneğinin tam adıdır Şemadaki. Örneğin, *dokunun:/MyApp/hizmetim/* Service 'e ulaşmak için *MyApp/hizmetim* kullanırsınız.

    Hizmet örneği adı büyük/küçük harfe duyarlıdır. URL 'deki hizmet örneği adı için farklı bir büyük harf kullanılması, isteklerin 404 (bulunamadı) ile başarısız olmasına neden olur.
* **Sonek yolu:** Bu, bağlanmak istediğiniz hizmetin *uygı/değer/ekleme/3* gıbı asıl URL yoludur.
* **Partitionkey:** Bölümlenmiş bir hizmet için bu, ulaşmak istediğiniz bölümün hesaplanan bölüm anahtarıdır. Bunun bölüm KIMLIĞI GUID *'si olmadığına* unutmayın. Bu parametre, Singleton bölüm düzenini kullanan hizmetler için gerekli değildir.
* **Partitionkind:** Bu, hizmet bölümü şemadır. Bu, ' Int64Range ' veya ' adlandırılmış ' olabilir. Bu parametre, Singleton bölüm düzenini kullanan hizmetler için gerekli değildir.
* **Listenername** Hizmetten gelen uç noktalar {"endpoints": {"Listener1": "Endpoint1", "Listener2": "Endpoint2"...}} biçimindedir. Hizmet birden çok uç nokta kullanıma sunarsa, bu, istemci isteğinin iletilmesi gereken uç noktayı tanımlar. Bu, hizmetin yalnızca bir dinleyicisi varsa atlanabilir.
* **Targetreplicaselector** Bu, hedef çoğaltmanın veya örneğin nasıl seçilmesi gerektiğini belirtir.
  * Hedef hizmetin durum bilgisi olduğunda, TargetReplicaSelector aşağıdakilerden biri olabilir: ' PrimaryReplica ', ' Rasgelesecondaryreplica ' veya ' RandomReplica '. Bu parametre belirtilmediğinde, varsayılan değer ' PrimaryReplica ' olur.
  * Hedef hizmet durum bilgisiz olduğunda, ters proxy, isteği iletmek için hizmet bölümünün rastgele bir örneğini seçer.
* **Zaman aşımı:**  Bu, istemci isteği adına hizmete ters proxy tarafından oluşturulan HTTP isteğinin zaman aşımını belirtir. Varsayılan değer 120 saniyedir. Bu, isteğe bağlı bir parametredir.

### <a name="example-usage"></a>Örnek kullanım
Örnek olarak, aşağıdaki URL 'de bir HTTP dinleyicisi açan *doku:/MyApp/hizmetim* hizmetini ele alalım:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Hizmetin kaynakları aşağıda verilmiştir:

* `/index.html`
* `/api/users/<userId>`

Hizmet tekil bölümlendirme şemasını kullanıyorsa, *Partitionkey* ve *partitionkind* sorgu dizesi parametreleri gerekli değildir ve ağ geçidi şu şekilde kullanılarak hizmete ulaşılabilir:

* Dışarıdan `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Dahili olarak `http://localhost:19081/MyApp/MyService`

Hizmet Tekdüzen Int64 bölümlendirme şemasını kullanıyorsa, bir hizmetin bölümüne ulaşmak için *Partitionkey* ve *partitionkind* sorgu dizesi parametreleri kullanılmalıdır:

* Dışarıdan `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Dahili olarak `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Hizmetin sunduğu kaynaklara ulaşmak için, URL 'deki hizmet adından sonra kaynak yolunu yerleştirmeniz yeterlidir:

* Dışarıdan `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Dahili olarak `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Ağ Geçidi daha sonra bu istekleri hizmetin URL 'sine iletir:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Bağlantı noktası paylaşma Hizmetleri için özel işleme
Ters proxy Service Fabric, bir hizmet adresini yeniden çözümlemeye çalışır ve bir hizmete ulaşılamadığında isteği yeniden dener. Genellikle, bir hizmete ulaşılamadığından, hizmet örneği veya çoğaltma, normal yaşam döngüsünün bir parçası olarak farklı bir düğüme taşınır. Bu durumda, ters proxy, bir uç noktanın artık özgün olarak çözümlenen adreste açık olmadığını belirten bir ağ bağlantı hatası alabilir.

Ancak, çoğaltmalar veya hizmet örnekleri bir konak işlemini paylaşabilir ve ayrıca, http.sys tabanlı bir Web sunucusu tarafından barındırıldığı sırada bir bağlantı noktası paylaşabilir:

* [System .net. HttpListener](/dotnet/api/system.net.httplistener)
* [WebListener ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Bu durumda, Web sunucusu ana bilgisayar işleminde kullanılabilir ve isteklere yanıt verebilir, ancak çözümlenen hizmet örneği veya çoğaltma artık konakta kullanılamıyor olabilir. Bu durumda, ağ geçidi Web sunucusundan bir HTTP 404 yanıtı alır. Bu nedenle, bir HTTP 404 yanıtı iki ayrı anlam içerebilir:

- Büyük/küçük harf #1: hizmet adresi doğru, ancak kullanıcının istediği kaynak yok.
- Büyük/küçük harf #2: hizmet adresi hatalı ve kullanıcının istediği kaynak farklı bir düğümde bulunabilir.

İlk durum, bir kullanıcı hatası olarak kabul edilen normal bir HTTP 404 ' dir. Ancak, ikinci durumda Kullanıcı var olan bir kaynak istedi. Hizmetin kendisi taşındığı için ters proxy bunu bulamadı. Ters proxy 'nin adresi yeniden çözmesi ve isteği yeniden denemesi gerekiyor.

Bu nedenle, ters proxy bu iki durum arasında ayrım yapmak için bir yol gerektirir. Bu ayrım yapmak için sunucudan bir ipucu gerekir.

* Varsayılan olarak, ters proxy, büyük/küçük harfe #2 varsayar ve isteği yeniden çözmeye çalışır.
* Ters ara sunucu için büyük/küçük harf #1 belirtmek için, hizmetin aşağıdaki HTTP yanıt üst bilgisini döndürmesi gerekir:

  `X-ServiceFabric : ResourceNotFound`

Bu HTTP yanıt üst bilgisi, istenen kaynağın bulunmadığı normal bir HTTP 404 durumunu gösterir ve ters proxy, hizmet adresini yeniden çözümlemeyi denemez.

## <a name="special-handling-for-services-running-in-containers"></a>Kapsayıcılar üzerinde çalışan hizmetler için özel işleme

Kapsayıcılar içinde çalışan hizmetler için, `Fabric_NodeIPOrFQDN` aşağıdaki kodda gösterildiği gibi, [ters proxy URL 'sini](#uri-format-for-addressing-services-by-using-the-reverse-proxy) oluşturmak için ortam değişkenini kullanabilirsiniz:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Yerel küme için `Fabric_NodeIPOrFQDN` Varsayılan olarak "localhost" olarak ayarlanır. `-UseMachineName`Kapsayıcıların düğüm üzerinde çalışan ters proxy 'ye ulaşmasını sağlamak için yerel kümeyi parametresiyle başlatın. Daha fazla bilgi için bkz. bir [Geliştirici ortamınızı kapsayıcılara hata ayıklama Için yapılandırma](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Docker Compose kapsayıcıları içinde çalışan Service Fabric Hizmetleri, özel bir Docker-Compose. yml *bağlantı noktaları bölümü* http: veya https: Configuration gerektirir. Daha fazla bilgi için bkz. [Azure Service Fabric Docker Compose dağıtım desteği](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Sonraki adımlar
* [Bir kümede ters proxy ayarlayın ve yapılandırın](service-fabric-reverseproxy-setup.md).
* [Ters ara sunucu ile güvenli HTTP hizmeti için yönlendirmeyi ayarlama](service-fabric-reverseproxy-configure-secure-communication.md)
* [Ters proxy olaylarını tanılama](service-fabric-reverse-proxy-diagnostics.md)
* [GitHub üzerinde örnek bir projede](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)bulunan HIZMETLER arasındaki HTTP iletişimi örneğini inceleyin.
* [Reliable Services uzaktan iletişim ile uzak yordam çağrıları](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 'de OWıN kullanan Web API 'SI](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services kullanarak WCF iletişimi](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
