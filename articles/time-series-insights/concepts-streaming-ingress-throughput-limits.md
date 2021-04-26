---
title: Akış alma işleme sınırlamaları-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2 'de giriş aktarım hızı sınırları hakkında bilgi edinin.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: d86cc6af34036f5bd638b4fc78abdb54d71e6859
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306526"
---
# <a name="streaming-ingestion-throughput-limits"></a>Akış alma performansı sınırları

Azure Time Series Insights Gen2 akış veri giriş sınırlamaları aşağıda açıklanmıştır.

> [!TIP]
> Tüm limitlerin kapsamlı bir listesi için [Azure Time Series Insights Gen2 ortamınızın planını](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) okuyun.

## <a name="per-environment-limitations"></a>Ortam başına sınırlamalar

Genel olarak giriş fiyatları, kuruluşunuzda bulunan cihazların sayısı, olay egörev sıklığı ve her olayın boyutu olarak görüntülenir:

* **Cihazların sayısı** × **olay emisi sıklığı** **her olayın boyutu**.

Varsayılan olarak, Azure Time Series Insights Gen2, gelen verileri **Azure Time Series Insights Gen2 ortamı başına saniyede 1 megabayta (Mbps) kadar** bir hızda alabilir. [Hub bölümü başına](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)ek sınırlamalar vardır.

> [!TIP]
>
> * 2 MBps 'ye kadar hızlara kadar olan ortam desteği istek tarafından sağlanarak temin edilebilir.
> * Azure portal aracılığıyla bir destek bileti göndererek daha yüksek aktarım hızı gerekiyorsa bizimle iletişim kurun.

* **Örnek 1:**

    Contoso Shipping, dakikada üç kez bir olay sunan 100.000 cihaza sahiptir. Bir olayın boyutu 200 bayttır. Bunlar, Azure Time Series Insights Gen2 olay kaynağı olarak dört bölümden oluşan bir IoT Hub kullanıyor.

  * Azure Time Series Insights Gen2 ortamı için alım oranı şu şekilde olacaktır: **100.000 cihaz * 200 bayt/olay * (3/60 olay/sn) = 1 MB**/sn.
    * Dengeli bölüm varsayıldığında, bölüm başına alım oranı 0,25 MBps olur.
    * Contoso sevkiyat alım oranı, ölçek sınırlamaları dahilinde olacaktır.

* **Örnek 2:**

    Contoso Fleet Analytics, her saniye bir olay sunan 10.000 cihaza sahiptir. Bunlar, Azure Time Series Insights Gen2 olay kaynağı olarak 2 bölüm sayısıyla bir olay hub 'ı kullanıyor. Bir olayın boyutu 200 bayttır.

  * Ortam alma hızı şu şekilde olacaktır: **10.000 cihaz * 200 bayt/olay * 1 olay/sn = 2 Mbps**.
    * Dengeli bölümler varsayıldığında, bölüm başına hız 1 MB/sn olur.
    * Contoso Fleet Analizi ' alım oranı, ortam ve bölüm sınırlarının üzerinde. Gen2 Azure portal aracılığıyla, ortamları için alma hızını artırmak için Azure Time Series Insights bir istek gönderebilir ve sınırlar dahilinde daha fazla bölüm içeren bir olay hub 'ı oluşturabilir.

## <a name="hub-partitions-and-per-partition-limits"></a>Merkez bölümleri ve bölüm sınırları başına

Azure Time Series Insights Gen2 ortamınızı planlarken, Azure Time Series Insights Gen2 'e bağlanacağınız olay kaynakları yapılandırmasını göz önünde bulundurmanız önemlidir. Hem Azure IoT Hub hem de Event Hubs, olay işleme için yatay ölçeklendirmeyi etkinleştirmek üzere bölümleri kullanır.

*Bölüm* , bir hub 'da tutulan olayların sıralı dizisidir. Bölüm sayısı, hub oluşturma aşamasında ayarlanır ve değiştirilemez.

Event Hubs bölümlendirme en iyi uygulamaları için [kaç bölümden Ihtiyacım olduğunu](../event-hubs/event-hubs-faq.yml#how-many-partitions-do-i-need-) gözden geçirin.

> [!NOTE]
> Azure Time Series Insights Gen2 ile kullanılan birçok IoT Hub 'ı yalnızca dört bölüme ihtiyaç duyar.

Azure Time Series Insights Gen2 ortamınız için yeni bir hub oluşturuyor ya da var olanı kullanarak, sınırlar dahilinde olup olmadığını anlamak için bölüm başına alma hızınızı hesaplamanız gerekir.

Azure Time Series Insights Gen2 Şu anda **0,5 MB/sn 'lik bölüm sınırına göre genel olarak** belirlenmiştir.

### <a name="iot-hub-specific-considerations"></a>IoT Hub özgü konular

Bir cihaz IoT Hub oluşturulduğunda, kalıcı olarak bir bölüme atanır. Bunu yaparken, IoT Hub olay sıralamasını garanti edebilir (atama hiçbir şekilde değişmeyeceğinden).

Sabit bir bölüm ataması Ayrıca, IoT Hub aşağı akış 'dan gönderilen verileri gösteren Azure Time Series Insights Gen2 örneklerini de etkiler. Birden çok cihazdan gelen iletiler hub 'a aynı ağ geçidi cihaz KIMLIĞI kullanılarak iletildiğinde, bu, bölüm başına ölçek limitlerini aşan büyük olasılıkla aynı anda aynı bölüme ulaşabilirler.

**Etki**:

* Tek bir bölüm, sınır üzerinden sürekli alma oranı yaşıyorsa, IoT Hub veri saklama süresi aşılmadan önce Azure Time Series Insights Gen2 tüm cihaz telemetrisini eşitlememesi mümkündür. Sonuç olarak, giriş sınırları sürekli olarak aşılırsa gönderilen veriler kaybolabilir.

Bu durumda, aşağıdaki en iyi yöntemleri öneririz:

* Çözümünüzü dağıtmaya başlamadan önce ortamınızı ve bölüm başına giriş oranlarını hesaplayın.
* IoT Hub cihazlarınızın mümkün olan en yüksek ölçüde yük dengelemesi yapıldığından emin olun.

> [!IMPORTANT]
> Bir olay kaynağı olarak IoT Hub kullanan ortamlarda, oran 'nin bölüm sınırlaması başına 0,5 MBps 'in altına düştüğünü sağlamak için kullanımdaki hub cihazı sayısını kullanarak alım oranını hesaplayın.
>
> * Aynı anda birkaç olay geldikçe bile, sınır aşılmayacak.

  ![IoT Hub bölüm diyagramı](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Hub aktarım hızını ve bölümleri iyileştirme hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [IoT Hub ölçeği](../iot-hub/iot-hub-scaling.md)
* [Olay Hub 'ı ölçeği](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Olay Hub 'ı bölümleri](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Sonraki adımlar

* Veri [depolama](./concepts-storage.md) hakkında bilgi edinin
