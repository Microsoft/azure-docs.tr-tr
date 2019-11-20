---
title: Azure SignalR hizmeti için performans Kılavuzu
description: Azure SignalR hizmeti performansına ve kıyaslamaya genel bakış. Kapasiteyi planlarken dikkate alınması gereken önemli ölçümler.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157674"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Azure SignalR hizmeti için performans Kılavuzu

Azure SignalR hizmetini kullanmanın önemli avantajlarından biri, SignalR uygulamalarının ölçeklendirilmesine yönelik kolaylıklar. Büyük ölçekli bir senaryoda performans önemli bir faktördür. 

Bu kılavuzda, SignalR uygulama performansını etkileyen faktörler tanıtılmaktadır. Farklı kullanım örneği senaryolarında tipik performansı açıklayacağız. Sonunda, bir performans raporu oluşturmak için kullanabileceğiniz ortam ve araçlar tanıtılcağız.

## <a name="term-definitions"></a>Terim tanımları

*Gelen*: Azure SignalR hizmetine gelen ileti.

*Giden*: Azure SignalR hizmetinden giden ileti.

*Bant genişliği*: 1 saniye içinde tüm iletilerin toplam boyutu.

*Varsayılan mod*: bir Azure SignalR hizmeti örneği oluşturulduğunda varsayılan çalışma modu. Azure SignalR hizmeti, uygulama sunucusunun herhangi bir istemci bağlantısını kabul etmeden önce onunla bağlantı kurmasını bekliyor.

*Sunucusuz mod*: Azure SignalR hizmetinin yalnızca istemci bağlantılarını kabul ettiği bir mod. Sunucu bağlantısına izin verilmez.

## <a name="overview"></a>Genel Bakış

Azure SignalR hizmeti, farklı performans kapasiteleri için yedi Standart katman tanımlar. Bu kılavuzda aşağıdaki sorulara yanıt verilmiştir:

-   Her katman için tipik Azure SignalR hizmeti performansı nedir?

-   Azure SignalR hizmeti, ileti işleme için gereksinimlerimi karşılar (örneğin, saniye başına 100.000 ileti gönderiliyor)?

-   Özel senaryoum için hangi katman benim için uygun? Ya da uygun katmanı nasıl seçirim?

-   Ne tür bir App Server (VM boyutu) bana uygun? Kaç tane dağıtmalıyım?

Bu soruları yanıtlamak için, bu kılavuz ilk olarak, performansı etkileyen faktörlerin üst düzey bir açıklamasını verir. Daha sonra, her katmanın genel kullanım örnekleri için gelen ve giden en fazla ileti sayısını gösterir: **echo**, **Yayınla**, **gruba gönder**ve **bağlantıya gönder** (eşler arası sohbet).

Bu kılavuzda tüm senaryolar (ve farklı kullanım durumları, ileti boyutları, ileti gönderme düzenleri vb.) ele alınamaz. Ancak size yardımcı olmak için bazı yöntemler sağlar:

- Gelen veya giden iletiler için yaklaşık gereksiniminizi değerlendirin.
- Performans tablosunu denetleyerek uygun katmanları bulun.

## <a name="performance-insight"></a>Performans öngörüleri

Bu bölüm performans değerlendirme yöntemlerini açıklar ve ardından performansı etkileyen tüm faktörleri listeler. Sonunda, performans gereksinimlerini değerlendirmenize yardımcı olmak için yöntemler sağlar.

### <a name="methodology"></a>Yöntem

*Aktarım hızı* ve *gecikme süresi* , performans denetiminin iki tipik yönlerdir. Azure SignalR hizmeti için, her SKU katmanının kendi verimlilik Azaltma ilkesi vardır. İlke, *izin verilen en yüksek aktarım hızını (gelen ve giden bant genişliği),* iletilerin yüzde 99 ' luk bir gecikme süresi olduğunda maksimum elde edilen aktarım hızı olarak tanımlar.

Gecikme süresi, Azure SignalR hizmetinden yanıt iletisini almaya yönelik iletiyi gönderen bağlantıdan geçen süredir. Örnek olarak **yankı** alalım. Her istemci bağlantısı, iletiye bir zaman damgası ekler. Uygulama sunucusunun hub 'ı, özgün iletiyi istemciye geri gönderir. Bu nedenle, yayma gecikmesi her istemci bağlantısıyla kolayca hesaplanır. Zaman damgası, **yayın**içindeki her ileti için iliştirilir, **gruba gönder**ve **bağlantıya gönder**.

Binlerce eşzamanlı istemci bağlantısının benzetimini yapmak için Azure 'da bir sanal özel ağda birden fazla VM oluşturulur. Bu VM 'lerin tümü aynı Azure SignalR hizmeti örneğine bağlanır.

Azure SignalR hizmeti 'nin varsayılan modunda, App Server VM 'Leri istemci VM 'leriyle aynı sanal özel ağa dağıtılır. Tüm istemci VM 'Leri ve App Server VM 'Leri, bölgeler arası gecikme süresini önlemek için aynı bölgedeki aynı ağda dağıtılır.

### <a name="performance-factors"></a>Performans faktörleri

Teorik olarak, Azure SignalR hizmet kapasitesi hesaplama kaynaklarıyla sınırlıdır: CPU, bellek ve ağ. Örneğin, Azure SignalR hizmetine yönelik daha fazla bağlantı, hizmetin daha fazla bellek kullanmasına neden olur. Daha büyük ileti trafiği için (örneğin, her ileti 2.048 bayttan büyükse), Azure SignalR hizmeti trafiği işlemek için daha fazla CPU döngüsü harcaması gerekir. Bu arada, Azure ağ bant genişliği, en yüksek trafik için de bir sınır uygular.

Aktarım türü, performansı etkileyen başka bir faktördür. [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [sunucu tarafından gönderilen olay](https://en.wikipedia.org/wiki/Server-sent_events)ve [uzun yoklama](https://en.wikipedia.org/wiki/Push_technology)olmak üzere üç tür vardır. 

WebSocket, tek bir TCP bağlantısı üzerinden çift yönlü ve tam çift yönlü iletişim protokolüdür. Sunucu tarafından gönderilen olay, iletileri sunucudan istemciye göndermek için bir tek yönlü protokoldür. Uzun yoklama, istemcilerin bir HTTP isteği aracılığıyla sunucudan düzenli olarak bilgi yoklamasını gerektirir. Aynı koşullarda aynı API için, WebSocket en iyi performansa sahiptir, sunucu tarafından gönderilen olay daha yavaştır ve uzun yoklamaya en yavaş olur. Azure SignalR hizmeti, varsayılan olarak WebSocket önerir.

İleti yönlendirme maliyeti de performansı kısıtlar. Azure SignalR hizmeti bir rolü bir istemci veya sunucu kümesinden diğer istemcilere veya sunuculara yönlendiren bir ileti yönlendiricisi olarak yürütür. Farklı bir senaryo veya API farklı bir yönlendirme ilkesi gerektirir. 

**Yankı**için, istemci kendine bir ileti gönderir ve yönlendirme hedefi de kendisi olur. Bu düzenin en düşük yönlendirme maliyeti vardır. Ancak **yayın**için, **gruba gönder**ve **bağlantıya gönder**için Azure SignalR hizmeti 'nin, iç dağıtılmış veri yapısı aracılığıyla hedef bağlantıları araması gerekir. Bu ek işlem, daha fazla CPU, bellek ve ağ bant genişliği kullanır. Sonuç olarak, performans daha yavaştır.

Varsayılan modda, uygulama sunucusu belirli senaryolar için de performans sorunlarına yol açabilir. Azure SignalR SDK 'Sı, her istemciyle sinyal sinyalleri aracılığıyla canlı bağlantı sağlarken hub 'ı çağırmalıdır.

Sunucusuz modda istemci, WebSocket olarak verimli olmayan HTTP Post ile bir ileti gönderir.

Başka bir faktör protokol: JSON ve [MessagePack](https://msgpack.org/index.html). MessagePack boyut olarak küçüktür ve JSON 'dan daha hızlı dağıtılır. MessagePack, performansı iyileştirmeyebilir, ancak. Azure SignalR hizmeti 'nin performansı, istemcilerden sunuculara ileti iletme sırasında ileti yükünün kodunu çözmediğinden veya tam tersi olmadığından protokollerle hassas değildir.

Özet bölümünde, aşağıdaki etmenler gelen ve giden kapasiteyi etkiler:

-   SKU katmanı (CPU/bellek)

-   Bağlantı sayısı

-   İleti boyutu

-   İleti gönderme hızı

-   Aktarım türü (WebSocket, sunucu tarafından gönderilen-olay veya uzun yoklama)

-   Kullanım örneği senaryosu (yönlendirme maliyeti)

-   App Server ve hizmet bağlantıları (sunucu modunda)


### <a name="finding-a-proper-sku"></a>Uygun SKU 'YU bulma

Gelen/giden kapasiteyi nasıl değerlendirebilir veya belirli bir kullanım durumu için hangi katmanın uygun olduğunu nasıl bulabilirim?

Uygulama sunucusunun yeterince güçlü olduğunu ve performans sorunu olmadığını varsayın. Ardından, her katman için en yüksek gelen ve giden bant genişliğini denetleyin.

#### <a name="quick-evaluation"></a>Hızlı değerlendirme

Öncelikle bazı varsayılan ayarları kabul ederek değerlendirmeyi basitleştirelim: 

- Aktarım türü WebSocket 'dir.
- İleti boyutu 2.048 bayttır.
- Her 1 saniyede bir ileti gönderilir.
- Azure SignalR hizmeti varsayılan modda.

Her katmanın en yüksek gelen bant genişliği ve giden bant genişliği vardır. Gelen veya giden bağlantı sınırı aşdıktan sonra sorunsuz bir kullanıcı deneyimi garanti edilmez.

**Echo** en düşük yönlendirme maliyetine sahip olduğu Için, yankı en fazla gelen bant genişliğine sahip olur. **Yayın** , en fazla giden ileti bant genişliğini tanımlar.

Aşağıdaki iki tabloda vurgulanan değerleri *aşmayın* .

|       Girdilerinizi                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar                       | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| **Gelen bant genişliği** | **2 MBps**    | **4 MBps**    | **10 MBps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Giden bant genişliği | 2 Mb/sn   | 4 Mb/sn   | 10 Mb/sn  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Yayın             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Bağlantılar               | 1000 | 2,000 | 5\.000  | 10,000 | 20,000 | 50.000  | 100.000 |
| Gelen bant genişliği  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Giden bant genişliği** | **4 MBps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Gelen bant genişliği* ve *giden bant genişliği* , saniye başına toplam ileti boyutudur.  Bunlar için formüller aşağıda verilmiştir:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *InboundConnections*: iletiyi gönderen bağlantı sayısı.

- *OutboundConnections*: iletiyi alan bağlantı sayısı.

- *messageSize*: tek bir iletinin boyutu (ortalama değer). 1\.024 bayttan küçük bir ileti, 1.024 baytlık bir iletiye benzer bir performans etkisine sahiptir.

- *Sendınterval*: bir ileti gönderme zamanı. Genellikle ileti başına 1 saniyedir, bu da her saniye bir ileti gönderir. Daha küçük bir Aralık, zaman diliminde daha fazla ileti gönderilmesini anlamına gelir. Örneğin, ileti başına 0,5 saniye her saniyede iki ileti gönderilmesini anlamına gelir.

- *Bağlantılar*: her katman Için Azure SignalR hizmeti için taahhüt edilen en büyük eşik. Bağlantı numarası daha fazla arttırılırsa bağlantı azaltmasından de devam edilir.

#### <a name="evaluation-for-complex-use-cases"></a>Karmaşık kullanım örnekleri için değerlendirme

##### <a name="bigger-message-size-or-different-sending-rate"></a>Daha büyük ileti boyutu veya farklı gönderme hızı

Gerçek kullanım durumu daha karmaşıktır. 2\.048 bayttan daha büyük bir ileti gönderebilir veya gönderme iletisi hızı saniyede bir ileti değildir. Unit100's yayını bir örnek olarak ele alalım. bu şekilde, performansını değerlendirme hakkında bilgi edinebilirsiniz.

Aşağıdaki tabloda, **yayının**gerçek kullanım durumu gösterilmektedir. Ancak ileti boyutu, bağlantı sayısı ve mesaj gönderme hızı, önceki bölümde kabul ettiğimiz verilerden farklıdır. Bu, yalnızca iki tane olduğunu bildiğimiz takdirde bu öğelerden herhangi birini (ileti boyutu, bağlantı sayısı veya ileti gönderme hızı) nasıl vereceğimiz bir sorudır.

| Yayın  | İleti boyutu | Saniye başına gelen ileti | Bağlantılar | Gönderme aralıkları |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 sn                      |
| 2 | 256 KB               | 1                        | 8,000       | 5 sn                      |

Aşağıdaki formül, önceki formüle göre kolayca çıkarsılır:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Unit100 için, en fazla giden bant genişliği önceki tablodan 400 MB 'tır. 20 KB 'lik bir ileti boyutu için, en fazla giden bağlantı 400 MB \* 5/20 KB = 100.000 olmalıdır ve bu değer gerçek değerle eşleşir.

##### <a name="mixed-use-cases"></a>Karışık kullanım örnekleri

Gerçek kullanım durumu genellikle dört temel kullanım örneğini birlikte karıştırır: **echo**, **Yayınla**, **gruba gönder**ve **bağlantıya gönder**. Kapasiteyi değerlendirmek için kullandığınız metodolojiyi:

1. Karışık kullanım örneklerini dört temel kullanım durumuna bölün.
1. Yukarıdaki formülleri ayrı olarak kullanarak, en yüksek gelen ve giden ileti bant genişliğini hesaplayın.
1. Toplam gelen/giden bant genişliğini almak için bant genişliği hesaplamalarını toplayın. 

Ardından, en yüksek gelen/giden bant genişliği tablolarından uygun katmanı seçin.

> [!NOTE]
> Yüzlerce veya binlerce küçük gruba bir ileti göndermek veya birbirlerine ileti gönderen binlerce istemci için yönlendirme maliyeti baskın hale gelir. Bu etkiyi hesaba götürün.

İstemcilere ileti göndermenin kullanım durumu için, uygulama sunucusunun performans sorunu *olmadığından* emin olun. Aşağıdaki "olay incelemesi" bölümünde, kaç tane uygulama sunucusuna ihtiyacınız olduğunu ve kaç tane sunucu bağlantısı yapılandırılacağını gösteren yönergeler verilmektedir.

## <a name="case-study"></a>Örnek olay incelemesi

Aşağıdaki bölümler WebSocket taşıması için dört genel kullanım durumuna geçer: **echo**, **Yayınla**, **gruba gönder**ve **bağlantıya gönder**. Her senaryo için, Bölüm Azure SignalR hizmeti için geçerli gelen ve giden kapasiteyi listeler. Ayrıca, performansı etkileyen ana faktörleri de açıklar.

Varsayılan modda, App Server Azure SignalR hizmeti ile beş sunucu bağlantısı oluşturur. App Server, varsayılan olarak Azure SignalR hizmeti SDK 'sını kullanır. Aşağıdaki performans testi sonuçlarında, sunucu bağlantıları 15 ' e yükseltilir (veya büyük bir gruba yayın yapmak ve ileti göndermek için daha fazla).

Farklı kullanım örneklerinin, uygulama sunucuları için farklı gereksinimleri vardır. **Yayın** , az sayıda uygulama sunucusuna ihtiyaç duyuyor. **Yankı** veya **gönderme bağlantısı** birçok uygulama sunucusuna ihtiyaç duyuyor.

Tüm kullanım durumlarında varsayılan ileti boyutu 2.048 bayttır ve ileti gönderme aralığı 1 saniyedir.

### <a name="default-mode"></a>Varsayılan mod

İstemciler, Web App Servers ve Azure SignalR hizmeti varsayılan modda yer alır. Her istemci tek bir bağlantı için temsil eder.

#### <a name="echo"></a>Girdilerinizi

İlk olarak, bir Web uygulaması Azure SignalR hizmetine bağlanır. İkinci olarak, birçok istemci Web uygulamasına bağlanır ve bu da istemcileri, erişim belirteci ve uç noktası ile Azure SignalR hizmetine yeniden yönlendirir. Ardından, istemciler Azure SignalR hizmeti ile WebSocket bağlantıları kurar.

Tüm istemciler bağlantı kurduktan sonra, belirli bir hub 'a her saniye bir zaman damgası içeren bir ileti göndermeye başlarlar. Hub, iletiyi özgün istemcisine geri bildirir. Her istemci, yankı iletisini geri aldığında gecikme süresini hesaplar.

Aşağıdaki diyagramda, 5 ile 8 arasında (kırmızı vurgulanmış trafik) bir döngüdür. Döngü, varsayılan süre (5 dakika) boyunca çalışır ve tüm ileti gecikmesinin istatistiğini alır.

![Yankı kullanım örneği için trafik](./media/signalr-concept-performance/echo.png)

**Echo** davranışı, en fazla gelen bant genişliğinin en fazla giden bant genişliğine eşit olduğunu belirler. Ayrıntılar için aşağıdaki tabloya bakın.

|       Girdilerinizi                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar                       | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Saniye başına gelen/giden ileti | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Gelen/giden bant genişliği | 2 Mb/sn   | 4 Mb/sn   | 10 Mb/sn  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

Bu kullanım durumunda, her istemci, uygulama sunucusunda tanımlanan hub 'ı çağırır. Hub yalnızca özgün istemci tarafında tanımlanan yöntemi çağırır. Bu Merkez, **yankı**için en hafif merkezdir.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Bu basit Hub için bile, uygulama sunucusuna uygulanan trafik, **yankı** gelen ileti yükü arttıkça belirgin olur. Bu trafik baskısı, büyük SKU katmanları için birçok uygulama sunucusu gerektirir. Aşağıdaki tabloda her katman için uygulama sunucusu sayısı listelenmektedir.


|    Girdilerinizi          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> İstemci bağlantı numarası, ileti boyutu, ileti gönderme hızı, SKU katmanı ve App Server 'ın CPU/belleği, **echo**genel performansını etkiler.

#### <a name="broadcast"></a>Yayın

**Yayın**için, Web uygulaması iletiyi aldığında tüm istemcilere yayınlar. Daha fazla istemci yayınlamak durumunda tüm istemciler için daha fazla ileti trafiği vardır. Aşağıdaki diyagrama bakın.

![Yayın kullanım durumu için trafik](./media/signalr-concept-performance/broadcast.png)

Az sayıda istemci yayınlıyorsunuz. Gelen ileti bant genişliği küçüktür, ancak giden bant genişliği çok büyük. İstemci bağlantısı veya yayın hızı arttıkça giden ileti bant genişliği artar.

Aşağıdaki tabloda, en fazla istemci bağlantısı, gelen/giden ileti sayısı ve bant genişliği özetlenmektedir.

|     Yayın             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Bağlantılar               | 1000 | 2,000 | 5\.000  | 10,000 | 20,000 | 50.000  | 100.000 |
| Saniye başına gelen ileti  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Saniye başına giden ileti | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100.000 | 200,000 |
| Gelen bant genişliği  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Giden bant genişliği | 4 Mb/sn   | 8 Mb/sn   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

İleti gönderenden yayın istemcileri dörtten fazla değil. Gelen ileti miktarı küçük olduğundan, **echo** ile karşılaştırıldığında daha az uygulama sunucusu gerekir. İki uygulama sunucusu hem SLA hem de performans konuları için yeterlidir. Ancak, özellikle Unit50 ve Unit100 için, dengesizliği önlemek üzere varsayılan sunucu bağlantılarını artırmanız gerekir.

|   Yayın      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Azure SignalR hizmetine yönelik olası dengesiz sunucu bağlantılarını önlemek için, her uygulama sunucusunda 5 ' ten 40 ' a kadar varsayılan sunucu bağlantılarını yükseltin.
>
> İstemci bağlantı numarası, ileti boyutu, ileti gönderme hızı ve SKU katmanı, **yayının**genel performansını etkiler.

#### <a name="send-to-group"></a>Gruba gönder

**Gruba gönder** kullanım durumunun **yayınlanması**için benzer bir trafik deseninin olması vardır. Aradaki fark, istemciler Azure SignalR hizmeti ile WebSocket bağlantıları kurduktan sonra, belirli bir gruba ileti gönderebilmeleri için gruplara katılması gerekir. Aşağıdaki diyagramda trafik akışı gösterilmektedir.

![Send-Group kullanım örneği için trafik](./media/signalr-concept-performance/sendtogroup.png)

Grup üyesi ve grup sayısı, performansı etkileyen iki faktördir. Çözümlemeyi basitleştirmek için iki grup türü tanımlayacağız:

- **Küçük grup**: her grubun 10 bağlantısı vardır. Grup numarası eşittir (en fazla bağlantı sayısı)/10. Örneğin, Unit1 için 1.000 bağlantı sayısı varsa, 1000/10 = 100 gruplarıdır.

- **Büyük grup**: Grup numarası her zaman 10 ' dur. Grup üye sayısı eşittir (en fazla bağlantı sayısı)/10. Örneğin, Unit1 için 1.000 bağlantı sayısı varsa, her grubun 1000/10 = 100 üyesi vardır.

**Send to Group** , hedeflenen bağlantıları dağıtılmış bir veri yapısı aracılığıyla bulması gerektiğinden Azure SignalR hizmetine bir yönlendirme maliyeti getirir. Gönderim bağlantıları arttıkça maliyet artar.

##### <a name="small-group"></a>Küçük grup

Yönlendirme maliyeti birçok küçük gruba ileti göndermek için önemlidir. Şu anda Azure SignalR Hizmeti uygulamasının, Unit50 adresindeki yönlendirme maliyeti sınırını okuması gerekir. Daha fazla CPU ve bellek eklemek yardım etmez, bu nedenle Unit100 daha fazla tasarıma sahiptir. Daha fazla gelen bant genişliğine ihtiyacınız varsa müşteri desteğine başvurun.

|   Küçük gruba gönder     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Bağlantılar               | 1000 | 2,000 | 5\.000  | 10,000 | 20,000 | 50.000 | 100.000
| Grup üye sayısı        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Grup sayısı               | 100   | 200   | 500    | 1000  | 2,000  | 5\.000  | 10,000 
| Saniye başına gelen ileti  | 200   | 400   | 1000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Gelen bant genişliği  | 400 KBps  | 800 KBps  | 2 Mb/sn     | 5 Mb/sn     | 8 Mb/sn     | 14 MBps    | 14 MBps     |
| Saniye başına giden ileti | 2,000 | 4,000 | 10,000 | 25,000 | 40,000 | 70.000 | 70.000  |
| Giden bant genişliği | 4 Mb/sn    | 8 Mb/sn    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Birçok istemci bağlantısı hub 'ı çağırıyor, bu nedenle uygulama sunucu numarası performans için de önemlidir. Aşağıdaki tabloda, önerilen uygulama sunucusu sayıları listelenmektedir.

|  Küçük gruba gönder   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> İstemci bağlantı numarası, ileti boyutu, ileti gönderme hızı, yönlendirme maliyeti, SKU katmanı ve App Server 'ın CPU/belleği, **küçük gruplara gönder**'in genel performansını etkiler.

##### <a name="big-group"></a>Büyük grup

**Büyük gruba gönder**için, giden bant genişliği, yönlendirme maliyeti sınırına geçmeden önce performans sorunu haline gelir. Aşağıdaki tabloda, **yayında**neredeyse aynı olan en fazla giden bant genişliği listelenmektedir.

|    Büyük gruba gönder      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Bağlantılar               | 1000 | 2,000 | 5\.000  | 10,000 | 20,000 | 50.000  | 100.000
| Grup üye sayısı        | 100   | 200   | 500    | 1000  | 2,000  | 5\.000   | 10,000 
| Grup sayısı               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Saniye başına gelen ileti  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Gelen bant genişliği  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Saniye başına giden ileti | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100.000 | 200,000 |
| Giden bant genişliği | 8 Mb/sn    | 8 Mb/sn    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

Gönderme bağlantı sayısı 40 ' den fazla değil. Uygulama sunucusundaki yük küçüktür, bu nedenle önerilen Web uygulaması sayısı küçük.

|  Büyük gruba gönder  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Azure SignalR hizmetine yönelik olası dengesiz sunucu bağlantılarını önlemek için, her uygulama sunucusunda 5 ' ten 40 ' a kadar varsayılan sunucu bağlantılarını yükseltin.
> 
> İstemci bağlantı numarası, ileti boyutu, ileti gönderme hızı, yönlendirme maliyeti ve SKU katmanı, **büyük grubun**genel performansını etkiler.

#### <a name="send-to-connection"></a>Bağlantıya gönder

**Bağlantı gönder** kullanım durumunda, Istemciler Azure SignalR hizmeti bağlantılarını kurıyorsa, her istemci kendı bağlantı kimliklerini almak için özel bir hub çağırır. Performans kıyaslaması tüm bağlantı kimliklerini toplar, bunları karıştırın ve bir gönderme hedefi olarak tüm istemcilere yeniden atar. İstemciler, performans testi bitene kadar iletiyi hedef bağlantıya göndermeyi tutar.

![İstemciye gönder kullanım örneği için trafik](./media/signalr-concept-performance/sendtoclient.png)

**Bağlantı gönder** için yönlendirme maliyeti, **küçük gruba gönder**maliyetine benzer.

Bağlantı sayısı arttıkça, yönlendirme maliyeti genel performansı sınırlar. Unit50 sınıra ulaştı. Sonuç olarak, Unit100 daha fazla iyileştiremez.

Aşağıdaki tablo, **bağlantı gönderme** kıyaslaması çalıştırmanın birçok yuvarladıktan sonra istatistiksel bir özettir.

|   Bağlantıya gönder   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Bağlantılar                        | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000          | 100.000         |
| Saniye başına gelen/giden ileti | 1000 | 2,000 | 5\.000 | 8,000  | 9\.000  | 20,000 | 20,000 |
| Gelen/giden bant genişliği | 2 Mb/sn    | 4 Mb/sn    | 10 Mb/sn   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Bu kullanım örneği, uygulama sunucusu tarafında yüksek yük gerektirir. Aşağıdaki tabloda önerilen App Server sayısına bakın.

|  Bağlantıya gönder  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> İstemci bağlantı numarası, ileti boyutu, ileti gönderme hızı, yönlendirme maliyeti, SKU katmanı ve App Server için CPU/bellek, **bağlantı gönder bağlantısının**genel performansını etkiler.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET SignalR Echo, Yayınla ve küçük gruba gönder

Azure SignalR hizmeti, ASP.NET SignalR için aynı performans kapasitesini sağlar. 

Performans testi, ASP.NET SignalR için [Standart hizmet planı S3](https://azure.microsoft.com/pricing/details/app-service/windows/) ' dan Azure Web Apps kullanır.

Aşağıdaki tabloda, ASP.NET SignalR **echo**için önerilen Web uygulaması sayısı verilmiştir.

|   Girdilerinizi           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Aşağıdaki tabloda, ASP.NET SignalR **yayını**için önerilen Web uygulaması sayısı verilmiştir.

|  Yayın       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Aşağıdaki tabloda, ASP.NET SignalR **için küçük gruba gönder**önerilen Web uygulaması sayısı verilmiştir.

|  Küçük gruba gönder     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Bağlantılar      | 1000 | 2,000 | 5\.000 | 10,000 | 20,000 | 50.000 | 100.000 |
| Uygulama sunucusu sayısı | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Sunucusuz mod

İstemciler ve Azure SignalR hizmeti sunucusuz moda dahil edilir. Her istemci tek bir bağlantı için temsil eder. İstemci iletileri REST API aracılığıyla başka bir istemciye gönderir veya tüm iletileri yayınlar.

REST API üzerinden yüksek yoğunluklu iletiler gönderilmesi, WebSocket kullanımı kadar verimli değildir. Her seferinde yeni bir HTTP bağlantısı oluşturmanızı gerektirir ve bu, sunucusuz modda ek bir maliyettir.

#### <a name="broadcast-through-rest-api"></a>REST API üzerinden yayınla
Tüm istemciler Azure SignalR hizmeti ile WebSocket bağlantısı kurar. Daha sonra bazı istemciler REST API aracılığıyla yayını başlatır. İleti gönderme (gelen), WebSocket ile karşılaştırıldığında etkin olmayan HTTP POST aracılığıyla yapılır.

|   REST API üzerinden yayınla     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Bağlantılar               | 1000 | 2,000 | 5\.000  | 10,000 | 20,000 | 50.000  | 100.000 |
| Saniye başına gelen ileti  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Saniye başına giden ileti | 2,000 | 4,000 | 10,000 | 20,000 | 40,000 | 100.000 | 200,000 |
| Gelen bant genişliği  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Giden bant genişliği | 4 Mb/sn    | 8 Mb/sn    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>REST API aracılığıyla kullanıcıya gönder
Kıyaslama, Azure SignalR hizmetine bağlanmaya başlamadan önce tüm istemcilere Kullanıcı adları atar. İstemciler Azure SignalR hizmeti ile WebSocket bağlantıları kurduktan sonra, HTTP POST aracılığıyla diğerlerine ileti göndermeye başlarlar.

|   REST API aracılığıyla kullanıcıya gönder | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Bağlantılar               | 1000 | 2,000 | 5\.000  | 10,000 | 20,000 | 50.000  | 100.000 |
| Saniye başına gelen ileti  | 300   | 600   | 900    | 1\.300  | 2,000  | 10,000  | 18.000  |
| Saniye başına giden ileti | 300   | 600   | 900    | 1\.300  | 2,000  | 10,000  | 18.000 |
| Gelen bant genişliği  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mb/sn     | 10 Mb/sn     | 36 MBps    |
| Giden bant genişliği | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mb/sn     | 10 Mb/sn     | 36 MBps    |

## <a name="performance-test-environments"></a>Performans testi ortamları

Daha önce listelenen tüm kullanım örnekleri için, performans testlerini bir Azure ortamında sağlıyoruz. En çok 50 istemci VM 'Leri ve 20 App Server VM kullandık. Bazı ayrıntılar aşağıda verilmiştir:

- İstemci VM boyutu: StandardDS2V2 (2 vCPU, 7G belleği)

- App Server VM boyutu: StandardF4sV2 (4 vCPU, 8G belleği)

- Azure SignalR SDK sunucu bağlantıları: 15

## <a name="performance-tools"></a>Performans araçları

[GitHub](https://github.com/Azure/azure-signalr-bench/)'Da Azure SignalR hizmeti için performans araçları ' nı bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, tipik kullanım örneği senaryolarında Azure SignalR hizmeti performansına genel bir bakış aldınız.

Hizmetin iç işlevleri ve ölçeklendirilmesi hakkında ayrıntılı bilgi almak için aşağıdaki kılavuzlarını okuyun:

* [Azure SignalR Hizmeti iç işlevleri](signalr-concept-internals.md)
* [Azure SignalR hizmeti ölçeklendirme](signalr-howto-scale-multi-instances.md)
