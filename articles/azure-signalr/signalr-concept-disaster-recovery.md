---
title: Azure SignalR hizmetinde dayanıklılık ve olağanüstü durum kurtarma
description: Dayanıklılık ve olağanüstü durum kurtarma sağlamak için birden çok SignalR hizmeti örneği ayarlama hakkında genel bakış
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: cf0f345b0fbf9fea2512f72c1996c9a1597cc0cd
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747655"
---
# <a name="resiliency-and-disaster-recovery"></a>Dayanıklılık ve olağanüstü durum kurtarma

Dayanıklılık ve olağanüstü durum kurtarma, çevrimiçi sistemlere yönelik yaygın bir gereksinimdir. Azure SignalR hizmeti% 99,9 kullanılabilirliği zaten garanti ediyor, ancak yine de bölgesel bir hizmet.
Hizmet örneğiniz her zaman bir bölgede çalışmaktadır ve bölge genelinde kesinti olduğunda başka bir bölgeye yük devremez.

Bunun yerine, hizmet SDK 'mız birden çok SignalR hizmeti örneğini desteklemeye yönelik bir işlevsellik sağlar ve bazıları kullanılabilir olmadığında otomatik olarak diğer örneklere geçiş yapar.
Bu özellikle, bir olağanüstü durum gerçekleştiğinde kurtarabileceksiniz, ancak doğru sistem topolojisini kendiniz ayarlamanız gerekecektir. Bu belgede nasıl yapılacağını öğreneceksiniz.

## <a name="high-available-architecture-for-signalr-service"></a>SignalR hizmeti için yüksek düzeyde kullanılabilir mimari

SignalR hizmeti için çapraz bölge dayanıklılığı sağlamak üzere, farklı bölgelerde birden çok hizmet örneği ayarlamanız gerekir. Bu nedenle, bir bölge kapalıysa, diğerleri yedekleme olarak kullanılabilir.
Birden çok hizmet örneğini App Server 'a bağlarken, birincil ve ikincil olmak üzere iki rol bulunur.
Birincil, çevrimiçi trafik alan ve ikincil için tam işlevli ancak yedekleme örneği olan bir örneğidir.
SDK uygulamamız içinde yalnızca birincil uç noktalar döndürülür, ancak normal durum istemcilerinin birincil uç noktalara yalnızca bağlanmasını sağlar.
Ancak birincil örnek aşağı çalıştığında, istemci yine de bağlantı yapabilmeleri için Negotiate ikincil uç noktalar döndürür.
Birincil örnek ve App Server normal sunucu bağlantıları aracılığıyla bağlanır, ancak ikincil örnek ve App Server, zayıf bağlantı adlı özel bir bağlantı türü aracılığıyla bağlanır.
Zayıf bir bağlantının ana farkı, ikincil örnek başka bir bölgede bulunduğundan istemci bağlantı yönlendirmeyi kabul etmemektedir. Bir istemciyi başka bir bölgeye yönlendirme en iyi seçenektir (gecikme süresini artırır).

Birden çok uygulama sunucusuna bağlanırken bir hizmet örneği farklı rollere sahip olabilir.
Çapraz bölge senaryosu için tipik bir kurulum, SignalR hizmet örneklerinin ve uygulama sunucularının iki (veya daha fazla) çiftine sahip olmak içindir.
Her bir çift App Server ve SignalR hizmeti aynı bölgede bulunur ve SignalR hizmeti, uygulama sunucusuna birincil rol olarak bağlanır.
Her bir çiftler arasında App Server ve SignalR hizmeti de bağlanır, ancak başka bir bölgedeki sunucuya bağlanırken SignalR ikincil hale gelir.

Bu topolojide, tüm uygulama sunucuları ve SignalR hizmeti örnekleri birbirine bağlı olduğu için bir sunucudan gelen ileti yine de tüm istemcilere teslim edilebilir.
Ancak, bir istemci bağlıyken, en iyi ağ gecikmesini sağlamak için her zaman aynı bölgedeki App Server 'a yönlendirilir.

Bu tür topolojiyi gösteren bir diyagram aşağıda verilmiştir:

![topology](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Birden çok SignalR hizmeti örneğiyle App Servers yapılandırma

Her bölgede SignalR hizmeti ve uygulama sunucuları oluşturulduktan sonra, uygulama sunucularınızı tüm SignalR hizmeti örneklerine bağlanacak şekilde yapılandırabilirsiniz.

Bunu iki şekilde yapabilirsiniz:

### <a name="through-config"></a>Yapılandırma üzerinden

`Azure:SignalR:ConnectionString`adlı bir yapılandırma girişinde, SignalR hizmeti bağlantı dizesinin ortam değişkenleri/uygulama ayarları/Web. cofıg aracılığıyla nasıl ayarlanacağını zaten bilmelisiniz.
Birden çok uç noktalarınız varsa, bunları aşağıdaki biçimde birden çok yapılandırma girişi halinde ayarlayabilirsiniz:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Burada `<name>` bitiş noktasının adı ve `<role>` rolü (birincil veya ikincil) olur.
Ad isteğe bağlıdır, ancak yönlendirme davranışını birden fazla uç nokta arasında daha fazla özelleştirmek istiyorsanız yararlı olacaktır.

### <a name="through-code"></a>Kod aracılığıyla

Bağlantı dizelerini başka bir yerde depolamayı tercih ediyorsanız, bunları kodunuzda okuyabilir ve `AddAzureSignalR()` (ASP.NET Core) veya `MapAzureSignalR()` (ASP.NET) çağırırken parametre olarak kullanabilirsiniz.

Örnek kod aşağıda verilmiştir:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

Birden çok birincil veya ikincil örnek yapılandırabilirsiniz. Birden çok birincil ve/veya ikincil örnek varsa, anlaş aşağıdaki sırada bir uç nokta döndürür:

1. Çevrimiçi olarak en az bir birincil örnek varsa, rastgele bir birincil çevrimiçi örnek döndürün.
2. Tüm birincil örnekler kapalıysa, rastgele bir ikincil çevrimiçi örnek döndürür.

## <a name="failover-sequence-and-best-practice"></a>Yük devretme sırası ve en iyi uygulama

Artık doğru sistem topolojisi kurulumuna sahipsiniz. Bir SignalR hizmeti örneği her çalıştığında çevrimiçi trafik diğer örneklere yönlendirilir.
Birincil örnek aşağı doğru olduğunda (ve bir süre sonra kurtarıldığında) Bu durum aşağıda verilmiştir:

1. Birincil hizmet örneği çalışmıyor, bu örnekteki tüm sunucu bağlantıları bırakılacak.
2. Bu örneğe bağlı tüm sunucular bu dosyayı çevrimdışı olarak işaretleyecek ve bu uç noktayı döndürmeyi ve ikincil uç noktayı döndürmeye başlayacak şekilde anlaşacak.
3. Bu örnekteki tüm istemci bağlantıları da kapatılacak, istemciler yeniden bağlanır. Uygulama sunucuları artık ikincil uç nokta döndürdüğü için istemciler ikincil örneğe bağlanır.
4. Artık ikincil örnek tüm çevrimiçi trafiği alır. Sunucudan istemcilere gönderilen tüm iletiler, ikincil olarak tüm uygulama sunucularına bağlı olduğundan yine de teslim edilebilir. Ancak, istemci-sunucu iletileri yalnızca aynı bölgedeki App Server 'a yönlendirilir.
5. Birincil örnek kurtarıldıktan ve yeniden çevrimiçi olduktan sonra, App Server bu bağlantıya yeniden bağlantı kuracak ve çevrimiçi olarak işaretleyecek. Negotiate şimdi birincil uç noktayı geri döndürmektedir, böylece yeni istemciler birincil ağa geri bağlanır. Ancak mevcut istemciler bırakılmayacaktır ve bunların bağlantısı kesilene kadar ikinciye yönlendirilmeye devam edecektir.

Aşağıdaki diyagramlarda, SignalR hizmetinde yük devretme işlemlerinin nasıl yapılacağı gösterilmektedir:

Yük devretmeden önce ![yük devretme Işleminden önce Fig. 1](media/signalr-concept-disaster-recovery/before-failover.png)

Yük devretmenin ardından ![yük devretme sonrasında Fig. 2](media/signalr-concept-disaster-recovery/after-failover.png)

Fig. 3 kısa süre sonra birincil kurtardıktan sonra kısa süre ![](media/signalr-concept-disaster-recovery/after-recover.png)

Yalnızca birincil uygulama sunucusu ve SignalR hizmetinin çevrimiçi trafiğe sahip olması durumunda (mavi renkli), normal olarak görebilirsiniz.
Yük devretmeden sonra, ikincil App Server ve SignalR hizmeti de etkin hale gelir.
Birincil SignalR hizmeti yeniden çevrimiçi olduktan sonra, yeni istemciler birincil SignalR 'ye bağlanır. Ancak mevcut istemciler, her iki örnek de trafiğe sahip olacak şekilde ikinciye bağlanır.
Tüm mevcut istemciler bağlantısını kestikten sonra sisteminiz normal (Fig. 1) durumuna geri alınacaktır.

Bir çapraz bölge yüksek kullanılabilir mimarisi uygulamak için iki ana model vardır:

1. Birincisi, tüm çevrimiçi trafiğe sahip bir dizi App Server ve SignalR hizmeti örneği ve yedek (etkin/Pasif, Fig. 1 ' de gösterilen) olarak başka bir çiftin bulunduğu bir çiftin olması olabilir. 
2. Diğeri, her biri çevrimiçi trafiğin bir parçası olan ve diğer çiftler için (Fig. 3 ' e benzer) yedekleme görevi gören iki (veya daha fazla) uygulama sunucusu ve SignalR hizmeti örneği olmalıdır.

SignalR hizmeti her iki modeli de destekleyebilir, ana fark uygulama sunucularını nasıl uygulayabileceğinizi de açıklamaktadır.
Uygulama sunucuları etkin/Pasif ise, SignalR hizmeti de etkin/Pasif olacaktır (birincil uygulama sunucusu yalnızca birincil SignalR hizmet örneğini döndürdüğünden).
Uygulama sunucuları etkin/etkin ise, SignalR hizmeti de etkin/etkin olacaktır (tüm uygulama sunucuları kendi birincil SignalR örneklerini döndür, bu nedenle bunların hepsi trafik alabilir).

Hangi desenleri kullanacağınızı bağımsız olarak Not etmeksizin, her bir SignalR hizmeti örneğini birincil olarak bir uygulama sunucusuna bağlamanız gerekir.

Ayrıca, SignalR bağlantısının doğası (uzun bir bağlantıdır) nedeniyle, bir olağanüstü durum ve yük devretme gerçekleşirken istemciler bağlantı düşmeye karşılaşacaktır.
Bu tür durumları, son müşterileriniz için saydam hale getirmek üzere istemci tarafında işlemeniz gerekir. Örneğin, bir bağlantı kapatıldıktan sonra yeniden bağlanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, uygulamanızı SignalR hizmeti için dayanıklılık elde etmek üzere nasıl yapılandıracağınızı öğrendiniz. SignalR hizmetinde sunucu/istemci bağlantısı ve bağlantı yönlendirme hakkında daha fazla ayrıntıyı anlamak için, SignalR hizmeti iç işlevleri için [Bu makaleyi](signalr-concept-internals.md) okuyabilirsiniz.

Çok sayıda bağlantıyı işlemek için birden çok örnek kullanan parçalama gibi ölçeklendirme senaryoları için, [birden çok örneği ölçeklendirme](signalr-howto-scale-multi-instances.md)konusunu okuyun.
