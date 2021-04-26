---
title: Redis için Azure Cache istemci tarafı sorunlarını giderme
description: Redsıs istemci belleği baskısı, trafik patlaması, yüksek CPU, sınırlı bant genişliği, büyük istekler veya büyük yanıt boyutu gibi Redsıs için Azure önbelleğiyle ilgili yaygın istemci tarafı sorunlarını çözmeyi öğrenin.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008976"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Redis için Azure Cache istemci tarafı sorunlarını giderme

Bu bölümde, uygulamanızın kullandığı Redsıs istemcisindeki bir koşul nedeniyle oluşan sorun giderme sorunları ele alınmaktadır.

- [Redsıs istemcisinde bellek baskısı](#memory-pressure-on-redis-client)
- [Trafik veri bloğu](#traffic-burst)
- [Yüksek istemci CPU kullanımı](#high-client-cpu-usage)
- [İstemci tarafı bant genişliği sınırlaması](#client-side-bandwidth-limitation)
- [Büyük istek veya yanıt boyutu](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Redsıs istemcisinde bellek baskısı

İstemci makinedeki bellek baskısı, önbellekteki yanıtların işlenmesini geciktirebileceği tüm performans sorunlarına yol açar. Bellek baskısı ne zaman olursa, sistem diske veri sayfası verebilir. Bu _sayfa hatalı_ , sistemin önemli ölçüde yavaşlamasına neden olur.

İstemci üzerindeki bellek basıncını algılamak için:

- Kullanılabilir belleği aşmadığından emin olmak için makinedeki bellek kullanımını izleyin.
- İstemcinin `Page Faults/Sec` performans sayacını izleyin. Normal işlem sırasında çoğu sistemde bazı sayfa hataları vardır. İstek zaman aşımlarını ifade eden sayfa hatası artışları bellek baskısı olduğunu gösterebilir.

İstemci üzerindeki yüksek bellek baskısı, birkaç farklı şekilde azaltılabilir:

- İstemci üzerindeki bellek tüketimini azaltmak için bellek kullanım desenlerinizi inceleyin.
- İstemci sanal makinesini daha fazla bellek ile daha büyük bir boyuta yükseltin.

## <a name="traffic-burst"></a>Trafik veri bloğu

Kötü ayarlarla birleştirilmiş trafik, `ThreadPool` Redsıs sunucusu tarafından zaten gönderilmiş ancak istemci tarafında henüz tüketilmeyen verilerin işlenmesinde gecikmelere neden olabilir.

`ThreadPool` [Bir örnek `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs)kullanarak istatistiklerinizin zaman içindeki değişimini izleyin. `TimeoutException`Daha fazla araştırmak Için StackExchange. redin 'dan iletileri kullanabilirsiniz:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

Önceki özel durumda, ilginç olan birkaç sorun vardır:

- `IOCP`Bölümünde ve `WORKER` bölümünde `Busy` değerden daha büyük bir değer olduğuna dikkat edin `Min` . Bu fark, `ThreadPool` ayarlarınızın ayarlanması gereken anlamına gelir.
- Ayrıca, bkz `in: 64221` .. Bu değer, istemcinin çekirdek yuva katmanında 64.211 baytın alındığını, ancak uygulama tarafından okunmadığını gösterir. Bu fark genellikle uygulamanızın (örneğin, StackExchange. Redsıs), sunucu tarafından size gönderildiğinde, ağdan veri okuyamayacağı anlamına gelir.

İş parçacığı havuzunuzun, patlama senaryolarında hızlı bir şekilde ölçeklendirdiğinizden emin olmak için [ `ThreadPool` ayarlarınızı yapılandırabilirsiniz](cache-management-faq.md#important-details-about-threadpool-growth) .

## <a name="high-client-cpu-usage"></a>Yüksek istemci CPU kullanımı

Yüksek istemci CPU kullanımı, sistemin yapması istenen işi tutamayacağını belirtir. Önbellek yanıtı hızla gönderse de istemci, yanıtı zamanında işleyemeyebilir.

Azure portal veya makinedeki performans sayaçları aracılığıyla bulunan ölçümleri kullanarak istemcinin sistem genelinde CPU kullanımını izleyin. Tek bir işlemde düşük CPU kullanımı olabileceğinden ancak sistem genelindeki CPU yüksek olabileceğinden *işlem* CPU 'yu izlemenin dikkatli olun. CPU kullanımında, zaman aşımları ile karşılık gelen ani artışlar izleyin. Yüksek CPU Ayrıca `in: XXX` `TimeoutException` [trafik veri bloğu](#traffic-burst) bölümünde açıklandığı gibi hata iletilerinde yüksek değerlere neden olabilir.

> [!NOTE]
> StackExchange. redme 1.1.603 ve üzeri, `local-cpu` `TimeoutException` hata iletilerinde ölçüm içerir. [StackExchange. Redsıs NuGet paketinin](https://www.nuget.org/packages/StackExchange.Redis/)en son sürümünü kullandığınızdan emin olun. En son sürüme sahip olacak şekilde, zaman aşımlarını daha sağlam hale getirmek için kodda sürekli olarak düzeltilen hatalar vardır.
>

İstemcinin yüksek CPU kullanımını azaltmak için:

- CPU olumsuz artışlarına neden olduğunu araştırın.
- Daha fazla CPU kapasitesi ile istemcinizi daha büyük bir VM boyutuna yükseltin.

## <a name="client-side-bandwidth-limitation"></a>İstemci tarafı bant genişliği sınırlaması

İstemci makinelerin mimarisine bağlı olarak, kullanılabilir ağ bant genişliği miktarına göre sınırlamalar olabilir. İstemci, ağ kapasitesini aşırı yükleyerek kullanılabilir bant genişliğini aşarsa, sunucu onu gönderirken istemci tarafında veri işlenmeyecektir. Bu durum zaman aşımına yol açabilir.

[Bir örnek `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs)kullanarak bant genişliği kullanımınızın zaman içindeki değişimini izleyin. Bu kod, kısıtlanmış izinlerle (Azure Web siteleri gibi) bazı ortamlarda başarıyla çalışmayabilir.

Azaltmak için ağ bant genişliği tüketimini azaltın veya istemci VM boyutunu daha fazla ağ kapasitesine sahip bir şekilde artırın.

## <a name="large-request-or-response-size"></a>Büyük istek veya yanıt boyutu

Büyük bir istek/yanıt zaman aşımına neden olabilir. Örnek olarak, istemciniz üzerinde yapılandırılmış zaman aşımı değerinin 1 saniye olduğunu varsayalım. Uygulamanız aynı anda iki anahtar ister (örneğin, ' A ' ve ' B ') (aynı fiziksel ağ bağlantısını kullanarak). Çoğu istemci, her iki isteğin da ' A ' ve ' B ' isteklerinin yanıtlarını beklemeden bir diğeri gönderildiği "ardışık düzen oluşturma" isteğini destekler. Sunucu yanıtları aynı sırada geri gönderir. ' A ' yanıtı büyükse, sonraki istekler için zaman aşımının büyük bir kısmını boşa alabilir.

Aşağıdaki örnekte, ' A ' ve ' B ' istekleri sunucuya hızlı bir şekilde gönderilir. Sunucu, ' A ' ve ' B ' yanıtlarını hızla göndermeye başlıyor. Veri aktarım süreleriyle, "B" yanıtının, sunucu hızla yanıt vermiş olsa bile ' A ' zaman aşımına uğramadan Yanıt beklemesi gerekir.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

Bu istek/yanıt ölçmek için zor bir değer. Büyük istekleri ve yanıtları izlemek için istemci kodunuzu kullanabilirsiniz.

Büyük yanıt boyutları için çözümler farklılaştırılabilecek ancak şunları içerir:

1. Uygulamanızı birkaç büyük değer yerine çok sayıda küçük değer için iyileştirin.
    - Tercih edilen çözüm, verilerinizi ilgili küçük değerlere bölmek için kullanılır.
    - [Redsıs için ideal değer boyut aralığının ne olduğuna bakın? 100 KB çok büyük mi?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) daha küçük değerler önerilme hakkında daha fazla bilgi için.
1. Daha yüksek bant genişliği özellikleri almak için VM 'nizin boyutunu artırın
    - İstemci veya sunucu sanal makinenizde daha fazla bant genişliği, daha büyük yanıtlar için veri aktarımı sürelerini azaltabilir.
    - Her iki makinede geçerli ağ kullanımınızı geçerli VM boyutlarınızın sınırlarına göre karşılaştırın. Yalnızca sunucuda veya yalnızca istemcide daha fazla bant genişliği yeterli olmayabilir.
1. Uygulamanızın kullandığı bağlantı nesnelerinin sayısını artırın.
    - Farklı bağlantı nesneleri üzerinde istek yapmak için hepsini bir kez deneme yaklaşımı kullanın.

## <a name="additional-information"></a>Ek bilgiler

- [Redis için Azure Cache sunucu tarafı sorunlarını giderme](cache-troubleshoot-server.md)
- [Önbelleğim performansını nasıl kıyaslarım ve test edebilirim?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
