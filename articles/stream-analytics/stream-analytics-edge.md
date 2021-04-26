---
title: IoT Edge üzerinde Azure Stream Analytics
description: Azure Stream Analytics Edge işleri oluşturun ve Azure IoT Edge çalıştıran cihazlara dağıtın.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: c2a062b75caa84a0e3c342ca1ce45ccce12f2bb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012623"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>IoT Edge üzerinde Azure Stream Analytics
 
IoT Edge üzerinde Azure Stream Analytics, geliştiricilerin cihaz tarafından oluşturulan verilerin tam değerinin kilidini açabilmeleri için, IoT cihazlarına neredeyse gerçek zamanlı analitik zeka dağıtmayı güçler. Azure Stream Analytics düşük gecikme süresi, dayanıklılık, bant genişliğinin verimli kullanımı ve uyumluluk için tasarlanmıştır. Kuruluşlar, endüstriyel işlemlere yönelik denetim mantığını kapatabilir ve bulutta gerçekleştirilen büyük veri analizlerini tamamlayabilir.

IoT Edge üzerinde Azure Stream Analytics, [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) Framework içinde çalışır. Stream Analytics ' de iş oluşturulduktan sonra, IoT Hub kullanarak dağıtabilir ve yönetebilirsiniz.

## <a name="common-scenarios"></a>Genel senaryolar

Bu bölümde IoT Edge Stream Analytics yönelik yaygın senaryolar açıklanmaktadır. Aşağıdaki diyagramda, IoT cihazları ve Azure bulutu arasındaki veri akışı gösterilmektedir.

:::image type="content" source="media/stream-analytics-edge/edge-high-level-diagram.png" alt-text="IoT Edge yüksek düzey diyagramı":::

### <a name="low-latency-command-and-control"></a>Düşük gecikmeli komut ve denetim

Üretim güvenlik sistemleri, işlemsel verilere Ultra düşük gecikme süresiyle yanıt vermelidir. IoT Edge Stream Analytics ile, algılayıcı verilerini neredeyse gerçek zamanlı olarak çözümleyebilir ve bir makineyi durdurmayı veya uyarıları tetikleme bozukluklarını saptadığınızda komutları verebilirsiniz.

### <a name="limited-connectivity-to-the-cloud"></a>Buluta sınırlı bağlantı

Uzaktan araştırma Ekipmanı, bağlı Vessels veya daha fazla detaya gitme gibi görev açısından kritik sistemler, bulut bağlantısı aralıklı olarak olsa bile verileri analiz etmeniz ve bunlara yanıt vermelidirler. Stream Analytics, akış mantığınızı ağ bağlantısından bağımsız olarak çalışır ve daha fazla işlem veya depolama için buluta ne gönderdiklerinizi seçebilirsiniz.

### <a name="limited-bandwidth"></a>Sınırlı bant genişliği

Jet motorları veya bağlı otomobiller tarafından oluşturulan veri hacmi, verilerin buluta gönderilmeden önce filtrelenme veya önceden işlenmesi gereken büyüklükte olabilir. Stream Analytics kullanarak, buluta gönderilmesi gereken verileri filtreleyebilir veya toplayabilirsiniz.

### <a name="compliance"></a>Uyumluluk

Yasal uyumluluk, bazı verilerin buluta gönderilmeden önce yerel olarak anonimleştirmesini veya toplanmasını gerektirebilir.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure Stream Analytics Edge işleri

Stream Analytics Edge işleri, [Azure IoT Edge cihazlara](../iot-edge/about-iot-edge.md)dağıtılan kapsayıcılar üzerinde çalışır. Kenar işleri iki bölümden oluşur:

* İş tanımından sorumlu bir bulut bölümü: kullanıcılar, etkinlik, çıkış, sorgu ve sıra dışı olaylar gibi diğer ayarları bulutta tanımlar.

* IoT cihazlarınızda çalışan bir modül. Modül Stream Analytics altyapısını içerir ve iş tanımını buluttan alır. 

Stream Analytics, uç işleri cihazlara dağıtmak için IoT Hub kullanır. Daha fazla bilgi için bkz. [IoT Edge dağıtım](../iot-edge/module-deployment-monitoring.md).

:::image type="content" source="media/stream-analytics-edge/stream-analytics-edge-job.png" alt-text="Azure Stream Analytics Edge işi":::

## <a name="edge-job-limitations"></a>Edge iş sınırlamaları

Amaç IoT Edge işleri ve bulut işleri arasında eşlik sahibi olmaktır. Çoğu SQL sorgu dili özelliği hem Edge hem de bulut için desteklenir. Ancak, aşağıdaki özellikler Edge işleri için desteklenmez:
* JavaScript 'te Kullanıcı tanımlı işlevler (UDF). UDF, IoT Edge işleri (Önizleme) [Için C#](./stream-analytics-edge-csharp-udf.md) dilinde kullanılabilir.
* Kullanıcı tanımlı toplamalar (UDA).
* Azure ML işlevleri.
* Giriş/çıkış için AVRO biçimi. Şu anda yalnızca CSV ve JSON desteklenir.
* Aşağıdaki SQL işleçleri:
    * BÖLÜM ÖLÇÜTÜ
    * GetMetadataPropertyValue
* Geç varış ilkesi

### <a name="runtime-and-hardware-requirements"></a>Çalışma zamanı ve donanım gereksinimleri
IoT Edge Stream Analytics çalıştırmak için [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/)çalıştırabileceği cihazlara ihtiyacınız vardır. 

Stream Analytics ve Azure IoT Edge birden çok konak işletim sisteminde (Windows, Linux) çalışan taşınabilir bir çözüm sağlamak için **Docker** kapsayıcıları kullanın.

IoT Edge Stream Analytics, x86-64 veya ARM (Gelişmiş RıSC makineleri) mimarilerinde çalışan Windows ve Linux görüntüleri olarak kullanılabilir hale getirilir. 


## <a name="input-and-output"></a>Girdi ve çıktı

Stream Analytics Edge işleri, IoT Edge cihazlarda çalışan diğer modüllerden giriş ve çıkış alabilirler. Ve belirli modüllerden bağlanmak için, dağıtım zamanında yönlendirme yapılandırmasını ayarlayabilirsiniz. Daha fazla bilgi [IoT Edge modül oluşturma belgelerinde](../iot-edge/module-composition.md)açıklanmıştır.

Hem giriş hem de çıkış için CSV ve JSON biçimleri desteklenir.

Stream Analytics işte oluşturduğunuz her giriş ve çıkış akışı için, dağıtılan modülünüzün kendisine karşılık gelen bir uç nokta oluşturulur. Bu uç noktalar, dağıtımınızın rotalarında kullanılabilir.

Desteklenen akış giriş türleri şunlardır:
* Edge hub 'ı
* Olay Hub'ı
* IoT Hub

Desteklenen akış çıktı türleri şunlardır:
* Edge hub 'ı
* SQL Veritabanı
* Olay Hub'ı
* BLOB depolama/ADLS 2.

Başvuru girişi başvuru dosya türünü destekler. Diğer çıkışlara, bir bulut işi aşağı akış kullanılarak ulaşılabilir. Örneğin, Edge 'de barındırılan bir Stream Analytics işi uç hub 'ına çıktı göndererek, daha sonra çıktıyı IoT Hub gönderebilirler. IoT Hub giriş ve çıkış Power BI ya da başka bir çıkış türü ile, bir bulutta barındırılan ikinci Azure Stream Analytics işi kullanabilirsiniz.

## <a name="license-and-third-party-notices"></a>Lisans ve üçüncü taraf bildirimleri
* [IoT Edge üzerinde Azure Stream Analytics lisansı](https://go.microsoft.com/fwlink/?linkid=862827). 
* [IoT Edge üzerinde Azure Stream Analytics Için üçüncü taraf bildirimi](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Azure Stream Analytics modülü görüntü bilgileri 

Bu sürüm bilgileri 2020-09-21 tarihinde son güncelleştirilme tarihi:

- Görüntü: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-amd64`
   - temel görüntü: mcr.microsoft.com/dotnet/core/runtime:2.1.13-alpine
   - platformunun
      - Mimari: AMD64
      - işletim sistemi: Linux
 
- Görüntü: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm32v7`
   - temel görüntü: mcr.microsoft.com/dotnet/core/runtime:2.1.13-bionic-arm32v7
   - platformunun
      - Mimari: ARM
      - işletim sistemi: Linux
 
- Görüntü: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.9-linux-arm64`
   - temel görüntü: mcr.microsoft.com/dotnet/core/runtime:3.0-bionic-arm64v8
   - platformunun
      - Mimari: arm64
      - işletim sistemi: Linux
      
      
## <a name="get-help"></a>Yardım alın
Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure IoT Edge hakkında daha fazla bilgi](../iot-edge/about-iot-edge.md)
* [IoT Edge öğreticide Stream Analytics](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Visual Studio araçlarını kullanarak Stream Analytics Edge işleri geliştirme](./stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [API 'Leri kullanarak Stream Analytics için CI/CD uygulayın](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
