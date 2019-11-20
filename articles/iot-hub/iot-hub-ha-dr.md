---
title: Azure IoT Hub yüksek kullanılabilirlik ve olağanüstü durum kurtarma | Microsoft Docs
description: Olağanüstü durum kurtarma özellikleriyle yüksek düzeyde kullanılabilir Azure IoT çözümleri oluşturmanıza yardımcı olan Azure ve IoT Hub özelliklerini açıklar.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: philmea
ms.openlocfilehash: 173be8207df2f0128dfc9ae3c36aa3c3dc392bee
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748558"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Yüksek kullanılabilirlik ve olağanüstü durum kurtarma IoT Hub

Dayanıklı bir IoT çözümünü uygulamaya yönelik ilk adım olarak mimarlar, geliştiriciler ve iş sahipleri oluşturmakta oldukları çözümlerin çalışma süresi hedeflerini tanımlamalıdır. Bu hedefler, öncelikle her senaryo için belirli iş hedeflerine göre tanımlanabilir. Bu bağlamda, [Azure Iş sürekliliği Technical Guidance](https://docs.microsoft.com/azure/architecture/resiliency/) makalesinde iş sürekliliği ve olağanüstü durum kurtarma hakkında bilgi almanıza yardımcı olacak genel bir çerçeve açıklanmaktadır. [Azure uygulamaları Için olağanüstü durum kurtarma ve yüksek kullanılabilirlik](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) sayfası, yüksek KULLANıLABILIRLIK (ha) ve olağanüstü durum kurtarma (Dr) sağlamak için Azure uygulamalarına yönelik stratejiler hakkında mimari yönergeler sağlar.

Bu makalede, özellikle IoT Hub hizmeti tarafından sunulan HA ve DR özellikleri ele alınmaktadır. Bu makalede ele alınan geniş bölgeler şunlardır:

- Bölge içi HA
- Çapraz bölge DR
- Çapraz bölge HA elde

IoT çözümleriniz için tanımladığınız çalışma süresi hedeflerine bağlı olarak, aşağıda belirtilen seçeneklerden hangisinin iş hedeflerinize en uygun olduğunu belirlemelisiniz. Bu HA/DR alternatiflerini IoT çözümünüz içine eklemek, aşağıdaki gibi bir denge için dikkatli bir değerlendirme gerektirir:

- İhtiyaç duyduğunuz dayanıklılık düzeyi 
- Uygulama ve bakım karmaşıklığı
- SMM etkisi

## <a name="intra-region-ha"></a>Bölge içi HA

IoT Hub hizmeti, hizmetin neredeyse tüm katmanlarında artıklıkları uygulayarak bölge içi HA sağlar. [IoT Hub hizmeti tarafından yayımlanan SLA](https://azure.microsoft.com/support/legal/sla/iot-hub) , bu artıklıkları kullanılarak elde edilir. IoT çözümünün geliştiricileri bu HA özelliklerinden faydalanmak için ek bir iş gerektirmez. IoT Hub makul bir yüksek çalışma süresi garantisi sunsa da, geçici hataların herhangi bir dağıtılmış bilgi işlem platformunda olduğu gibi beklenmeye devam edilebilir. Çözümlerinizi şirket içi bir çözümden buluta geçirmeye başladıysanız, odaklanmanız "hatalara göre ortalama süre" ile "Ortalama kurtarma süresi" arasında kaydırma yapmanız gerekir. Diğer bir deyişle, karışımda buluttan çalışırken geçici arızalar normal olarak değerlendirilir. Uygun [yeniden deneme ilkeleri](iot-hub-reliability-features-in-sdks.md) , geçici hatalarla başa çıkmak için bir bulut uygulamasıyla etkileşime geçen bileşenlere yerleşik olmalıdır.

> [!NOTE]
> Bazı Azure hizmetleri ayrıca [kullanılabilirlik alanları (AZs)](../availability-zones/az-overview.md)ile tümleştirerek bölge içinde ek bir kullanılabilirlik katmanları sağlar. AZs Şu anda IoT Hub hizmeti tarafından desteklenmiyor.

## <a name="cross-region-dr"></a>Çapraz bölge DR

Bir veri merkezinde güç kesintileri veya fiziksel varlıklar içeren diğer hatalardan dolayı genişletilmiş kesintiler yaşandığında bazı nadir durumlar olabilir. Bu tür olaylar, yukarıda açıklanan bölge içi HA yeteneğinin her zaman yardımcı olabileceği nadir bir durumdur. IoT Hub, bu tür genişletilmiş kesintilerden kurtarmak için birden çok çözüm sağlar. 

Bu tür bir durumda müşterilere sunulan kurtarma seçenekleri [Microsoft tarafından başlatılan yük devretme](#microsoft-initiated-failover) ve [el ile yük devretme](#manual-failover)sağlar. İkisi arasındaki temel fark, Microsoft 'un daha önce başlattığı ve kullanıcının ikincisini başlatmanızdır. Ayrıca, el ile yük devretme, Microsoft tarafından başlatılan yük devretme seçeneğiyle karşılaştırıldığında daha düşük bir kurtarma süresi hedefi (RTO) sağlar. Her seçenekle sunulan belirli RTOs aşağıdaki bölümlerde ele alınmıştır. Birincil bölgesinden bir IoT Hub 'ı yük devretme işlemi gerçekleştirmek için bu seçeneklerden biri çalıştırıldığında, hub ilgili [Azure coğrafi eşlenmiş bölgesinde](../best-practices-availability-paired-regions.md)tam olarak işlevsel hale gelir.

Bu yük devretme seçeneklerinin her ikisi de aşağıdaki kurtarma noktası hedeflerini (RPOs) sunar:

| Veri türü | Kurtarma noktası hedefleri (RPO) |
| --- | --- |
| Kimlik kayıt defteri |0-5 dakikalık veri kaybı |
| Cihaz ikizi verileri |0-5 dakikalık veri kaybı |
| Buluttan cihaza iletiler<sup>1</sup> |0-5 dakikalık veri kaybı |
| Üst<sup>1</sup> ve cihaz işleri |0-5 dakikalık veri kaybı |
| Cihazdan buluta iletiler |Tüm okunmamış iletiler kayboluyor |
| İşlem izleme iletileri |Tüm okunmamış iletiler kayboluyor |
| Buluttan cihaza geri bildirim iletileri |Tüm okunmamış iletiler kayboluyor |

<sup>1</sup> Buluttan cihaza iletiler ve üst işler, el ile yük devretmenin bir parçası olarak kurtarılmaz.

IoT Hub 'ı için yük devretme işlemi tamamlandıktan sonra, cihazdaki ve arka uç uygulamalardaki tüm işlemlerin el ile müdahale gerekmeden çalışmaya devam etmesi beklenir. Bu, cihazdan buluta iletilerinizin çalışmaya devam etmesi ve tüm cihaz kayıt defterinin bozulmadan emin olması anlamına gelir. Event Grid aracılığıyla yayılan olaylar, daha önce yapılandırılan ve bu Event Grid abonelikleri kullanılabilir olmaya devam eden abonelik (ler) i üzerinden tüketilebilir.

> [!CAUTION]
> - IoT Hub yerleşik olaylar uç noktasının Olay Hub 'ı ile uyumlu adı ve uç noktası, yük devretmeden sonra değişir. Olay Hub 'ı istemcisini ya da olay işlemcisi konağını kullanarak yerleşik uç noktadan telemetri iletileri alırken, bağlantıyı kurmak için [IoT Hub bağlantı dizesini kullanmanız](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) gerekir. Bu, arka uç uygulamalarınızın el ile müdahale sonrası yük devretmeye gerek kalmadan çalışmaya devam etmesini sağlar. Arka uç uygulamanızda Olay Hub 'ı ile uyumlu adı ve uç noktayı doğrudan kullanırsanız, işleme devam etmek için yük devretmeden sonra [Yeni Olay Hub 'ı ile uyumlu adı ve uç noktasını getirerek](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) uygulamanızı yeniden yapılandırmanız gerekir.
>
> - Depolama alanına yönlendirirken, tüm Blobların veya dosyaların bölüm üzerinde herhangi bir varsayımından okunmalarını sağlamak için Blobları veya dosyaları listeleyip daha sonra bunları yinelemenizi öneririz. Bölüm aralığı, Microsoft tarafından başlatılan bir yük devretme veya el ile yük devretme sırasında değişebilir. Dosya listesi için Blobların listesini veya [ADLS 2. API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) listesini listelemek Için, [LISTE bloblarını API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 'sini kullanabilirsiniz. 

## <a name="microsoft-initiated-failover"></a>Microsoft tarafından başlatılan yük devretme

Microsoft tarafından başlatılan yük devretme, etkilenen bir bölgeden gelen tüm IoT Hub 'larını ilgili coğrafi eşleştirilmiş bölgeye devretmek için Microsoft tarafından sık görülen durumlarda gerçekleştirilir. Bu işlem, varsayılan bir seçenektir (kullanıcıların hiçbir şekilde geri çevirmesine yol yoktur) ve kullanıcıdan müdahale gerektirmez. Microsoft bu seçeneğin ne zaman belirlendiğini belirleme hakkını saklı tutar. Bu mekanizma, kullanıcının hub 'ının yük devretmediği bir kullanıcı onayı içermez. Microsoft tarafından başlatılan yük devretme, 2-26 saat boyunca bir kurtarma süresi hedefi (RTO) içerir. 

Büyük RTO, Microsoft 'un bu bölgedeki tüm etkilenen müşteriler adına yük devretme işlemini gerçekleştirmesi gerekir. Yaklaşık bir gün boyunca kapalı kalma süresine yol açmış daha az kritik bir IoT çözümü çalıştırıyorsanız, IoT çözümünüz için genel olağanüstü durum kurtarma hedeflerini karşılamak üzere bu seçeneğe bir bağımlılık uygulamanız gerekir. Bu işlem tetiklendikten sonra çalışma zamanının tamamen işlemsel hale gelmesi için geçen toplam süre, "kurtarma süresi" bölümünde açıklanmaktadır.

## <a name="manual-failover"></a>El ile yük devretme

İş çalışma süresi hedefleriniz, Microsoft 'un başlattığı yük devretmesinin sağladığı RTO tarafından karşılanmıyorsa, yük devretme işlemini kendiniz tetiklemek için el ile yük devretme kullanmayı düşünün. Bu seçeneği kullanan RTO, 10 dakikadan birkaç saat arasında bir süre olabilir. RTO Şu anda yük devretmekte olan IoT Hub örneğine göre kayıtlı cihazların sayısının bir işlevidir. Yaklaşık 100.000 cihazı barındıran bir hub için RTO 'ın 15 dakikalık tahmini olmasını sağlayabilirsiniz. Bu işlem tetiklendikten sonra çalışma zamanının tamamen işlemsel hale gelmesi için geçen toplam süre, "kurtarma süresi" bölümünde açıklanmaktadır.

El ile yük devretme seçeneği, birincil bölgenin kapalı kalma süresi yaşamadan bağımsız olarak her zaman kullanılabilir. Bu nedenle, bu seçenek planlı yük devretme işlemleri gerçekleştirmek için kullanılabilir. Planlı Yük devretmelerin bir örnek kullanımı, düzenli yük devretme detaylarını gerçekleştirmenin bir örneğidir. Bir sözcük, planlı bir yük devretme işleminin, bu seçenek için RTO tarafından tanımlanan süre için Hub için kapalı kalma süresi ile sonuçlanmasına ve ayrıca yukarıdaki RPO tablosu tarafından tanımlanan bir veri kaybına neden olmasına neden olur. Gerçek bir olağanüstü durum oluştuğunda uçtan uca çözümlerinizi çalışır duruma getirme olanınızdan güvenle yararlanmak üzere planlı yük devretme seçeneğini düzenli aralıklarla çalıştırmak için bir test IoT Hub örneği ayarlamayı düşünebilirsiniz.

> [!IMPORTANT]
> - Test ayrıntılarına, üretim ortamlarınızda kullanılmakta olan IoT Hub 'larda gerçekleştirilmemelidir.
>
> - El ile yük devretme, hub 'ınızı Azure coğrafi eşleştirilmiş bölgeler arasında kalıcı olarak geçirmek için bir mekanizma olarak kullanılmamalıdır. Bunun yapılması, eski birincil bölgede bulunan cihazlardan hub 'a karşı gerçekleştirilen işlemler için daha fazla gecikme süresine neden olur.

## <a name="failback"></a>Yeniden çalışma

Yük devretme eylemi başka bir kez tetiklenerek eski birincil bölgeye geri dönme elde edilebilir. Orijinal birincil bölgedeki genişletilmiş kesintiden kurtarmak için özgün yük devretme işlemi gerçekleştirildiyse, bu konum kesinti durumundan kurtarıldıktan sonra hub 'ın özgün konuma geri dönmesi önerilir.

> [!IMPORTANT]
> - Kullanıcılar günde yalnızca 2 başarılı yük devretme ve 2 başarılı yeniden çalışma işlemi gerçekleştirmeye izin verilir.
>
> - Geri yük devretme/yeniden çalışma işlemlerine geri izin verilmiyor. Bu işlemler arasında 1 saat beklemeniz gerekir.

## <a name="time-to-recover"></a>Kurtarılacak süre

IoT Hub örneğinin FQDN (ve bu nedenle bağlantı dizesi), yük devretme sonrası aynı kaldığı halde, temel alınan IP adresi değişir. Bu nedenle, yük devretme işlemi tetiklendikten sonra IoT Hub örneğiniz için gerçekleştirilen çalışma zamanı işlemleri için genel süre, aşağıdaki işlev kullanılarak ifade edilebilir.

Kurtarma zamanı = RTO [10 dk-2 saat, el ile yük devretme için | 2-26 saat | Microsoft tarafından başlatılan yük devretme için, istemci uygulaması tarafından alınan, önbelleğe alınmış IoT Hub IP adreslerini yenilemek için geçen süre.

> [!IMPORTANT]
> IoT SDK 'Ları IoT Hub 'ının IP adresini önbelleğe vermez. SDK 'lar ile Kullanıcı kodu oluşturma, IoT Hub 'ının IP adresini önbelleğe içermemelidir.

## <a name="achieve-cross-region-ha"></a>Çapraz bölge HA

İş çalışma süresi hedefleriniz, Microsoft tarafından başlatılan yük devretme veya el ile yük devretme seçeneklerinin sağladığı RTO tarafından karşılanmıyorsa, cihaz başına bir otomatik çapraz bölge yük devretme mekanizması uygulamayı düşünmeniz gerekir.
IoT çözümlerinde dağıtım topolojilerinin tamamına yönelik kapsamlı bir işleme, bu makalenin kapsamı dışındadır. Makalede, yüksek kullanılabilirlik ve olağanüstü durum kurtarma amacıyla *bölgesel yük devretme* dağıtım modeli ele alınmaktadır.

Bölgesel yük devretme modelinde, çözüm arka ucu öncelikle bir veri merkezi konumunda çalışır. İkincil bir IoT Hub 'ı ve arka ucu başka bir veri merkezi konumuna dağıtılır. Birincil bölgedeki IoT Hub 'ı bir kesinti yaşalıyorsa veya cihazdan birincil bölgeye ağ bağlantısı kesintiye uğrarsa, cihazlar ikincil hizmet uç noktası kullanır. Tek bir bölge içinde olmak yerine bir çapraz bölge yük devretme modeli uygulayarak çözüm kullanılabilirliğini geliştirebilirsiniz. 

Yüksek düzeyde, IoT Hub bir bölgesel yük devretme modeli uygulamak için aşağıdaki adımları gerçekleştirmeniz gerekir:

* **Ikincil bir IoT Hub ve cihaz yönlendirme mantığı**: birincil bölgenizde hizmet kesintiye uğradıysanız, cihazların ikincil bölgenize bağlanmasına başlaması gerekir. Birçok hizmetin durum durumunu algılayan doğası göz önüne alındığında, çözüm yöneticilerinin bölgeler arası yük devretme sürecini tetiklemesi yaygındır. Yeni uç noktayı cihazlara iletmenin en iyi yolu, işlem denetimini sürdürirken, geçerli etkin uç nokta için bir *danışman* hizmetini düzenli olarak denetlerken olur. Concierge Hizmeti, DNS yeniden yönlendirme teknikleri (örneğin, [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)kullanılarak) kullanılarak çoğaltılan ve erişilebilir durumda tutulan bir Web uygulaması olabilir.

   > [!NOTE]
   > IoT Hub hizmeti, Azure Traffic Manager 'de desteklenen bir uç nokta türü değil. Önerilen danışman hizmetini, Endpoint Health araştırma API 'sini uygulayarak Azure Traffic Manager ile tümleştirmeniz önerilir.

* **Kimlik kayıt defteri çoğaltması**: kullanılabilir olması için, ikincil IoT Hub 'ının çözüme bağlanabilecek tüm cihaz kimliklerini içermesi gerekir. Çözüm, cihaz kimliklerinin coğrafi olarak çoğaltılan yedeklemelerini tutmalı ve cihazların etkin uç noktasını değiştirmeden önce ikincil IoT Hub 'ına yükler. IoT Hub cihaz kimliği dışarı aktarma işlevselliği bu bağlamda yararlıdır. Daha fazla bilgi için bkz. [IoT Hub Geliştirici Kılavuzu-kimlik kayıt defteri](iot-hub-devguide-identity-registry.md).

* **Mantık birleştirme**: birincil bölge yeniden kullanılabilir olduğunda, ikincil sitede oluşturulmuş tüm durum ve verilerin birincil bölgeye geri geçirilmesi gerekir. Bu durum ve veriler genellikle, birincil IoT Hub ile ve birincil bölgedeki uygulamaya özel depolarla birleştirilmesi gereken cihaz kimlikleri ve uygulama meta verileri ile ilgilidir. 

Bu adımı basitleştirmek için ıdempotent işlemlerini kullanmanız gerekir. Idempotent işlemleri, olayların son tutarlı dağılımından ve yinelenen veya sıra dışı tesliminden gelen yan etkileri en aza indirir. Buna ek olarak, uygulama mantığı olası tutarsızlıkları veya biraz güncel durumu tolerans sağlayacak şekilde tasarlanmalıdır. Bu durum, sistemin kurtarma noktası hedeflerine (RPO) dayalı olması için gereken ek süre nedeniyle ortaya çıkabilir.

## <a name="choose-the-right-hadr-option"></a>Sağ HA/DR seçeneğini belirleyin

Bu makalede sunulan HA/DR seçeneklerinin Özeti, çözümünüz için uygun olan doğru seçeneği seçmek üzere bir başvuru çerçevesi olarak kullanılabilecek.

| HA/DR seçeneği | RTO | RPO | El ile müdahale gerektiriyor mu? | Uygulama karmaşıklığı | Ek maliyet etkisi|
| --- | --- | --- | --- | --- | --- |
| Microsoft tarafından başlatılan yük devretme |2-26 saat|Yukarıdaki RPO tablosuna başvurun|Hayır|None|None|
| El ile yük devretme |10 dk-2 saat|Yukarıdaki RPO tablosuna başvurun|Evet|Çok düşük. Bu işlemi yalnızca portaldan tetiklemeniz gerekir.|None|
| Çapraz bölge HA |< 1 dk|Özel HA çözümünüzün çoğaltma sıklığına bağlıdır|Hayır|Yüksek|> 1x, 1 IoT Hub 'ın maliyeti|

## <a name="next-steps"></a>Sonraki adımlar

* [Azure IoT Hub nedir?](about-iot-hub.md)
* [IoT Hub 'Larını kullanmaya başlama (hızlı başlangıç)](quickstart-send-telemetry-dotnet.md)
* [Öğretici: IoT Hub 'ı için el ile yük devretme gerçekleştirme](tutorial-manual-failover.md)