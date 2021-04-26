---
title: Sunucu tarafı performans sorguları
description: API çağrıları aracılığıyla sunucu tarafı performans sorguları yapma
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594104"
---
# <a name="server-side-performance-queries"></a>Sunucu tarafı performans sorguları

Sunucu üzerinde iyi işleme performansı, kararlı çerçeve ücretleri ve iyi bir kullanıcı deneyimi için önemlidir. Sunucu üzerindeki performans özelliklerini dikkatle izlemek ve gerektiğinde iyileştirmek önemlidir. Performans verileri, adanmış API işlevleri aracılığıyla sorgulanabilir.

İşleme performansı için en etkili model giriş verileri ' dir. [Model dönüştürmeyi yapılandırma](../../how-tos/conversion/configure-model-conversion.md)bölümünde açıklandığı gibi giriş verilerini ince ayar yapabilirsiniz.

İstemci tarafı uygulama performansı bir performans sorunu olabilir. İstemci tarafı performansının derinlemesine çözümlenmesi için yapmanız önerilir [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>İstemci/sunucu zaman çizelgesi

Çeşitli gecikme değerleriyle ilgili ayrıntılarla karşılaşmadan önce, zaman çizelgesinde istemci ve sunucu arasındaki eşitleme noktalarına göz atalım:

![Ardışık düzen zaman çizelgesi](./media/server-client-timeline.png)

Çizimde şunları gösterilmektedir:

* bir *poz tahmini* , istemci tarafından sürekli olarak 60-Hz kare hızına göre yapılır (her 16,6 MS)
* daha sonra sunucu, poza göre işlemeye başlar
* sunucu, kodlanmış video görüntüsünü geri gönderiyor
* istemci görüntünün kodunu çözer, üzerine bazı CPU ve GPU işleri gerçekleştirir ve ardından görüntüyü görüntüler

## <a name="frame-statistics-queries"></a>Çerçeve istatistikleri sorguları

Çerçeve istatistikleri, son çerçeve için gecikme gibi bazı üst düzey bilgiler sağlar. Yapıda belirtilen veriler `FrameStatistics` istemci tarafında ölçülür, bu nedenle API zaman uyumlu bir çağrıdır:

```cs
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

Alınan `FrameStatistics` nesne aşağıdaki üyeleri barındırır:

| Üye | Açıklama |
|:-|:-|
| Latşifreli Posetoreceive | Bu poza yönelik bir sunucu çerçevesi istemci uygulaması tarafından tam olarak kullanılabilir olana kadar, kamera, istemci cihazında tahmine göre gecikme süresi. Bu değer, ağ gidiş dönüş, sunucu işleme süresi, video kod çözme ve değişim dengelemesi içerir. **Yukarıdaki çizimde bulunan Aralık 1 ' i inceleyin.**|
| Latşifreli Receivetopyeniden gönderildi | İstemci uygulaması CPU üzerinde PresentFrame çağrılana kadar alınan bir uzak çerçevenin kullanılabilirliğine gecikme süresi. **Yukarıdaki çizimde bulunan Aralık 2 '** ye bakın.|
| LatencyPresentToDisplay  | Ekran ışıkları bitene kadar CPU üzerinde bir çerçeve sunumu gecikme süresi. Bu değer, istemci GPU zamanını, işletim sistemi tarafından gerçekleştirilen tüm çerçeve ara belleği, donanım yeniden projeksiyonu ve cihaza bağlı görüntü tarama zamanı içerir. **Yukarıdaki çizimde bulunan Aralık 3 ' ü inceleyin.**|
| TimeSinceLastPresent | CPU üzerinde PresentFrame için sonraki çağrılar arasındaki süre. Görüntüleme süresinden daha büyük değerler (örneğin, 60-Hz istemci cihazında 16,6 MS), istemci uygulamanın zaman içinde CPU iş yükünü bitirmediğinden kaynaklanan sorunları gösterir.|
| Videoframpaalındı | Son saniye içinde sunucudan alınan çerçeve sayısı. |
| Videofoymereusedcount | Son saniye içinde, cihazda birden çok kez kullanılan alınan çerçeve sayısı. Sıfır olmayan değerler, çerçevelerin ağ değişimi veya aşırı sunucu işleme süresi nedeniyle yeniden kullanılması gerektiğini ve yeniden yansıtıldığını gösterir. |
| Videofınmesatlandı | Son saniye içinde kodu çözülen, ancak daha yeni bir çerçeve geldiği için görüntülenmemiş olan alınan çerçeve sayısı. Sıfır olmayan değerler, ağın birden çok çerçeveye ertelenmesini ve daha sonra istemci cihaza bir veri bloğu içinde birlikte ulaştığını gösterir. |
| Videofınmesatılır | **Videofesmesatine** çok benzer, ancak atılma nedeni, bir karenin daha geç bir şekilde verilmemesine neden olan herhangi bir bekleyen pozla ilişkilendirilmeyecektir. Bu atma gerçekleşdiğinde, önemli bir ağ çakışması vardır.|
| VideoFrameMinDelta | Son saniye içinde gelen ardışık iki kare arasındaki en az süre. VideoFrameMaxDelta ile birlikte, bu Aralık ağ veya video codec bileşeni tarafından neden olan bir değişim göstergesi sağlar. |
| VideoFrameMaxDelta | Son saniye boyunca ardışık iki kare arasındaki en fazla zaman miktarı. VideoFrameMinDelta ile birlikte, bu Aralık ağ veya video codec bileşeni tarafından neden olan bir değişim göstergesi sağlar. |

Tüm gecikme değerlerinin toplamı, genellikle 60 Hz 'deki kullanılabilir kare süresinden çok daha büyüktür. Bu, çok sayıda çerçeve paralel olarak uçuş aşamasında olduğundan ve çizimde gösterildiği gibi, yeni çerçeve isteklerinin istenen kare hızında kapalı olması nedeniyle Tamam ' dır. Ancak gecikme çok büyük olursa, [geç aşama yeniden projeksiyonunun](../../overview/features/late-stage-reprojection.md)kalitesini etkiler ve genel deneyimle tehlikeye atabilir.

`VideoFramesReceived`, `VideoFrameReusedCount` ve `VideoFramesDiscarded` Ağ ve sunucu performansını ölçmek için kullanılabilir. Düşük bir `VideoFramesReceived` değer ve yüksek bir `VideoFrameReusedCount` değer birleşimi, ağ tıkanıklığını veya zayıf sunucu performansını gösterebilir. Yüksek bir `VideoFramesDiscarded` değer de ağ tıkanıklığını gösterir.

Son olarak,, `TimeSinceLastPresent` `VideoFrameMinDelta` , ve `VideoFrameMaxDelta` gelen video çerçevelerinin ve yerel mevcut çağrıların varyansı hakkında fikir verir. Yüksek Varyans, çerçeve hızının anlamına gelir.

Yukarıdaki değerlerden hiçbiri, sunucunun işleme meşgul olduğu tam sürenin gidiş dönüş değerinden çıkarılmasıyla emin olması nedeniyle saf ağ gecikmesi (çizimdeki kırmızı oklar) üzerinde net bir işaret vermez `LatencyPoseToReceive` . Genel gecikme süresinin sunucu tarafı kısmı, istemci için kullanılamayan bir bilgi. Bununla birlikte, sonraki paragraf, bu değerin sunucudan alınan ek giriş ile nasıl yaklaşık olarak yapıldığını açıklar ve değer aracılığıyla gösterilir `NetworkLatency` .

## <a name="performance-assessment-queries"></a>Performans değerlendirmesi sorguları

*Performans değerlendirmesi sorguları* , sunucusundaki CPU ve GPU iş yükü hakkında daha ayrıntılı bilgi sağlar. Veriler sunucudan istendiğinden, bir performans anlık görüntüsünü sorgulamak olağan zaman uyumsuz örüntüyi izler:

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

Nesnenin aksine `FrameStatistics` , `PerformanceAssessment` nesne sunucu tarafı bilgilerini içerir:

| Üye | Açıklama |
|:-|:-|
| TimeCPU | Kare başına ortalama sunucu CPU süresi (milisaniye cinsinden) |
| TimeGPU | Çerçeve başına ortalama sunucu GPU süresi (milisaniye) |
| Kullanımı Zalationcpu | Toplam sunucu CPU kullanımı yüzdesi |
| Kullanımı Zationgpu | Toplam sunucu GPU kullanımı yüzdesi |
| MemoryCPU | Toplam sunucu ana bellek kullanımı yüzdesi |
| MemoryGPU | Sunucu GPU 'SU yüzdesi cinsinden toplam adanmış video belleği kullanımı |
| Ağ Gecikme Süresi | Milisaniye cinsinden yaklaşık ortalama gidiş dönüş ağ gecikmesi. Yukarıdaki çizimde, bu değer kırmızı okların toplamına karşılık gelir. Değer, gerçek sunucu işleme zamanının değerinden çıkarılmasıyla hesaplanır `LatencyPoseToReceive` `FrameStatistics` . Bu yaklaşık değer doğru olmasa da, istemci üzerinde hesaplanan gecikme değerlerinden yalıtılmış olan ağ gecikmesi hakkında bir gösterge sağlar. |
| PolygonsRendered | Bir çerçevede işlenen üçgenin sayısı. Bu sayı Ayrıca, işleme sırasında daha sonra gelen üçgenler de içerir. Yani bu sayı, farklı kamera konumlarında çok fazla farklılık göstermez, ancak üçgen yüzey kaldırma hızına bağlı olarak performans büyük ölçüde farklılık gösterebilir.|

Değerleri değerlendirmenize yardımcı olmak için her bölüm **harika**, **iyi**, **mediocre** veya **kötü** gibi bir kalite sınıflandırmasıyla gelir.
Bu değerlendirme ölçümü sunucunun sistem durumunu kabaca bir şekilde belirtir, ancak mutlak olarak görülmemelidir. Örneğin, GPU süresi için bir ' mediocre ' puanı gördüğünü varsayın. Bu, genel çerçeve süresi bütçesi sınırına yakın olduğundan mediocre olarak kabul edilir. Ancak, bir karmaşık modeli işlemekte olduğunuz için Nonetheless iyi bir değer olabilir.

## <a name="statistics-debug-output"></a>İstatistik hata ayıklama çıkışı

Sınıfı, `ServiceStatistics` hem çerçeve istatistikleri hem de performans değerlendirmesi sorguları etrafında kaydırılan ve istatistikleri toplanmış değerler olarak veya önceden oluşturulmuş bir dize olarak döndürmek için uygun işlevselliği sağlayan bir C# sınıfıdır. Aşağıdaki kod, istemci uygulamanızda sunucu tarafı istatistiklerini göstermek için en kolay yoldur.

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Yukarıdaki kod metin etiketini aşağıdaki metinle doldurur:

![ArrServiceStats String çıktısı](./media/arr-service-stats.png)

`GetStatsString`API, tüm değerlerin bir dizesini biçimlendirir, ancak her bir değer aynı zamanda örnekten programlı bir şekilde sorgulanabilir `ServiceStatistics` .

Ayrıca üyelerin, zaman içinde değerleri toplayan çeşitleri vardır. , Veya sonekine sahip üyelere bakın `*Avg` `*Max` `*Total` . Üye, `FramesUsedForAverage` Bu toplama için kaç çerçeve kullanıldığını gösterir.

## <a name="api-documentation"></a>API belgeleri

* [C# RenderingConnection. QueryServerPerformanceAssessmentAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection:: QueryServerPerformanceAssessmentAsync ()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Sonraki adımlar

* [Performans izlemeleri oluşturma](../../how-tos/performance-tracing.md)
* [Model dönüştürmeyi yapılandırma](../../how-tos/conversion/configure-model-conversion.md)