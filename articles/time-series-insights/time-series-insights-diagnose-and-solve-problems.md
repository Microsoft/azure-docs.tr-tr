---
title: Sorunları tanılama, sorun giderme ve çözme-Azure Time Series Insights | Microsoft Docs
description: Bu makalede Azure Time Series Insights ortamınızdaki yaygın sorunların nasıl tanılanacağı, giderileceği ve çözüleceği açıklanır.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: ca38ebb015552042591fb4cc6b7edfe99527e79f
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007059"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Time Series Insights ortamınızdaki sorunları tanılayın ve çözün

Bu makalede Azure Time Series Insights ortamınızda karşılaşabileceğiniz bazı sorunlar açıklanmaktadır. Makalede olası nedenler ve çözüm çözümleri sunulmaktadır.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-customer-challenges-and-mitigationsbr"></a>Yaygın Time Series Insights müşteri sorunları ve azaltmaları hakkında bilgi edinin.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Sorun: veri gösterilmemiştir

[Azure Time Series Insights Gezgini](https://insights.timeseries.azure.com) 'nde bazı yaygın nedenlerle hiç veri gerçekleşemeyebilir:

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Neden: olay kaynak verileri JSON biçiminde değil

Azure Time Series Insights yalnızca JSON verilerini destekler. JSON örnekleri için bkz [desteklenen JSON şekilleri](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Neden B: olay kaynak anahtarında gerekli bir izin eksik

* Azure IoT Hub 'de bir IoT Hub 'ı için, **hizmet bağlantısı** izinleri olan anahtarı sağlamanız gerekir. Her iki **ıothubowner** veya **hizmet** ilkelerinden biri, her ikisi de **hizmet bağlantı** izinlerine sahip olduğundan çalışacaktır.

   [![IoT Hub hizmet bağlantısı izinleri](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Azure Event Hubs bir olay hub 'ı için, **dinleme** izinleri olan anahtarı sağlamalısınız. Her ikisinde de **dinleme** izinleri olduğundan, **okuma** veya **yönetme** ilkelerinden biri çalışır.

   [![Olay Hub 'ı dinleme izinleri](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Neden C: belirtilen tüketici grubu Time Series Insights özel değil

Bir IoT Hub 'ı veya bir olay hub 'ını kaydettiğinizde, verileri okumak için kullanmak istediğiniz tüketici grubunu ayarlamanız önemlidir. Bu Tüketici grubu *paylaşılamıyor*. Tüketici grubu paylaşılmışsa, temel alınan IoT Hub 'ı veya Olay Hub 'ı otomatik olarak okuyuculardan birinin bağlantısını keser. Okunacak Time Series Insights için benzersiz bir tüketici grubu sağlayın.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Sorun: bazı veriler gösteriliyor, ancak veriler eksik

Veriler yalnızca kısmen göründüğünde ve veriler ele alınması halinde görünüyorsa, birkaç olasılıkların dikkate alınması gerekir.

### <a name="cause-a-your-environment-is-being-throttled"></a>Neden: ortamınız kısıtlanıyor

Veri içeren bir olay kaynağı oluşturduktan sonra, ortamlar sağlandığında kısıtlama yaygın bir sorundur. Azure IoT Hub ve Azure olayları hub 'Ları, verileri yedi güne kadar depolar. Time Series Insights her zaman olay kaynağındaki en eski olayla (ilk kez, ilk çıkar veya *FIFO*) başlar.

Örneğin, bir S1, tek birimlik Time Series Insights ortamına bağlandığınızda bir olay kaynağında 5.000.000 olayındaysanız, Time Series Insights günde yaklaşık 1.000.000 olay okur. Time Series Insights beş günden fazla gecikme yaşmış olabilir. Ancak, bu durum ortamın kısıtlandığı şeydir.

Olay kaynağınızda eski olaylarınız varsa, azaltmayı iki şekilde değiştirebilirsiniz:

- Time Series Insights ' de görünmesini istemediğiniz eski olayları kaldırmaya yardımcı olması için olay kaynağınızın saklama sınırlarını değiştirin.
- Eski olayların aktarım hızını artırmak için daha büyük bir ortam boyutu sağlayın (birim sayısı). Yukarıdaki örneği kullanarak, aynı S1 ortamını bir gün için beş birim ile artırırsanız, ortam bir gün içinde yakalamalı. Sabit durum olay üretim, günde 1.000.000 veya daha az olay ise, olaydan sonra etkinliğin kapasitesini tek bir birimle azaltabilirsiniz.

Azaltma sınırı, ortamın SKU 'SU türü ve kapasitesine göre zorlanır. Ortamdaki tüm olay kaynakları bu kapasiteyi paylaşır. IoT Hub veya Olay Hub 'ınızın olay kaynağı verileri zorlanan limitlerin ötesinde alıyorsa, azaltma ve bir öteleme görürsünüz.

Aşağıdaki şekilde, S1 SKU 'SU ve 3 kapasitesi olan bir Time Series Insights ortamı gösterilmektedir. Günde 3.000.000 olay alabilir.

[![ortamı SKU 'SU geçerli kapasitesi](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Örnek olarak, bir ortam bir olay hub 'ından alınan iletileri kabul eder. Günlük giriş oranı ~ 67.000 iletilerdir. Bu oran her dakikada yaklaşık 46 ileti dönüştürür. 

* Her olay hub 'ı iletisi tek bir Time Series Insights olayına düzleştirilir, azaltma gerçekleşmez. 
* Her olay hub 'ı iletisi 100 Time Series Insights olayına düzleştirilir, her dakika 4.600 olay belirtilmelidir. 

Kapasitesi 3 olan bir S1 SKU ortamı, her dakikada yalnızca 2.100 olay alabilir (her gün 1.000.000 olay 700 = ayda, üç birim için dakikada olay = 2.100 olay). 

Düzleştirme mantığının nasıl çalıştığını yüksek düzeyde anlamak için bkz. [desteklenen JSON şekilleri](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Aşırı azaltma için önerilen çözümler

Gecikme sayısını onarmak için ortamınızın SKU kapasitesini artırın. Daha fazla bilgi için bkz. [Time Series Insights ortamınızı ölçeklendirme](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Neden B: geçmiş verilerin ilk alımı yavaşlar

Var olan bir olay kaynağını bağladığınızda, IoT Hub veya Olay Hub 'ınız zaten veri içeriyor olabilir. Ortam, olay kaynağının ileti bekletme döneminin başından itibaren veri çekmeye başlar. Bu varsayılan işlem geçersiz kılınamaz. Azaltmayı izleyebilirsiniz. Azaltma, geçmiş verileri alırken biraz zaman alabilir.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Büyük başlangıç alımı için önerilen çözümler

Öteleme 'i onarmak için:

1. SKU kapasitesini izin verilen maksimum değere (Bu durumda 10) yükseltin. Kapasiteyi artırdıktan sonra, giriş işlemi çok daha hızlı bir şekilde daha hızlı bir şekilde çalışmaya başlar. Daha fazla kapasite için ücretlendirilirsiniz. Ne kadar hızlı bir şekilde yakalamak istediğinizi görselleştirmek için, kullanılabilirlik grafiğini [Time Series Insights Gezgini](https://insights.timeseries.azure.com)'nde görüntüleyebilirsiniz.

2. Öteleme yakalandığında, SKU kapasitesini normal giriş oranınızı azaltın.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Sorun: olay kaynağınızın zaman damgası Özellik adı ayarı çalışmıyor

Zaman damgası özelliği adının ve değerinin aşağıdaki kurallara uygun olduğundan emin olun:

* Zaman damgası Özellik adı büyük/küçük harfe duyarlıdır.
* Olay kaynağınızdan JSON dizesi olarak gelen zaman damgası özelliği değeri _yyyy-mm-ddTHH: mm: ss biçiminde olmalıdır. GÖNDERILDIĞINDE FFFFFFFK BIÇIMINDE_. Bir örnek **: 2008-04-12T12:53Z**.

Zaman damgası özelliği adınızın yakalanıp düzgün şekilde çalışmasını sağlamanın en kolay yolu Time Series Insights Gezginini kullanmaktır. Time Series Insights Gezgini ' nde, grafiği kullanarak, zaman damgası özellik adını girdikten sonra bir süre seçin. Seçime sağ tıklayın ve ardından **olayları keşfet** seçeneğini belirleyin.

İlk sütun üst bilgisi, zaman damgası özelliğinin adı olmalıdır. Sözcük **zaman damgasının**yanında **($TS)** öğesini görmeniz gerekir.

Aşağıdaki değerleri görmemelisiniz:

- *(ABC)* : Time Series Insights, veri değerlerini dizeler olarak okudığını gösterir.
- *Takvim simgesi*: Time Series Insights, veri değerini *DateTime*olarak okudığını gösterir.
- *#* : Time Series Insights veri değerlerini tamsayı olarak okudığını gösterir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Time Series Insights gecikme süresini azaltma](time-series-insights-environment-mitigate-latency.md)hakkında bilgi edinin.

- [Time Series Insights ortamınızı ölçeklendirmeyi](time-series-insights-how-to-scale-your-environment.md)öğrenin.