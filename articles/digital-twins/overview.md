---
title: Azure Digital Twins nedir?
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS ile nelerin yapılabileceklerini genel bakış.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: d03a4865c8db52f74f4130c458fec3028f5b95a4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481676"
---
# <a name="what-is-azure-digital-twins"></a>Azure Digital Twins nedir?

**Azure dijital TWINS** , tüm ortamların dijital modellerini temel alan bilgi grafiklerinin oluşturulmasına izin veren bir hizmet olarak platform (PaaS) sunumudur. Bu ortamlar binalar, oluşturucular, gruplar, enerji ağları, railler, lekeler ve daha fazlası gibi tüm şehirlerde bile olabilir. Bu dijital modeller, daha iyi ürünler, iyileştirilmiş işlemler, düşük maliyetler ve müşteri deneyimlerini ele alan Öngörüler elde etmek için kullanılabilir.

Azure dijital TWINS 'in en üstündeki etki alanı uzmanlığınızdan yararlanarak özelleştirilmiş, bağlı çözümler oluşturun:
* Tüm ortamları modelleyin ve dijital TWINS 'i ölçeklenebilir ve güvenli bir şekilde yaşam altına alın
* IoT cihazları ve mevcut iş sistemleri gibi varlıkları bağlama
* Dinamik iş mantığı ve veri işleme oluşturmak için güçlü bir olay sistemi kullanın
* Azure Data, Analytics ve AI hizmetleriyle tümleştirin, geçmişteki bir izleme ve daha sonra tahmin etmenize yardımcı olur

## <a name="azure-digital-twins-capabilities"></a>Azure dijital TWINS özellikleri

Azure dijital TWINS tarafından sunulan özelliklerin bir özeti aşağıda verilmiştir.

### <a name="open-modeling-language"></a>Modelleme dilini aç

Azure dijital TWINS 'de, [**modeller**](concepts-models.md)olarak adlandırılan özel ikizi türlerini kullanarak fiziksel ortamınızdaki kişileri, yerleri ve şeyleri temsil eden dijital varlıkları tanımlarsınız. 

Bu model tanımlarını, işletmenizi anlatmak için özel bir sözlük olarak düşünebilirsiniz. Örneğin, bir yapı yönetimi çözümü için, "derleme", "taban" ve "Asansör" gibi modeller tanımlayabilirsiniz. Böylece, belirli ortamınızı temsil etmek için bu modellere göre **dijital TWINS** oluşturabilirsiniz.

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

Modeller, [dijital TWINS tanım dili (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)adlı JSON benzeri bir dilde tanımlanır ve bunların durum özellikleri, telemetri olayları, komutlar, bileşenler ve ilişkiler açısından, TWINS 'leri tanımlar.
* Modeller, TWINS 'nizi, etkileşimlerini yansıtan bir bilgi grafiğine bağlayabilmeniz için varlıklarınız arasında anlamsal **ilişkiler** tanımlar. Modelleri, dünyanın bir açıklamasında ve ilişkileri fiiller olarak düşünebilir.
* Ayrıca, model devralmayı kullanarak TWINS 'yi özelleştirebilirsiniz. Bir model diğerinden kalıtımla alabilir.

DTDL, [ıot Tak ve kullan (PnP)](../iot-pnp/overview-iot-plug-and-play.md) ve [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md)dahil olmak üzere diğer Azure IoT hizmetlerinin tamamında veri modelleri için kullanılır. Bu, Azure dijital TWINS çözümünüzü bağlı ve Azure ekosisteminin diğer bölümleriyle uyumlu tutmanıza yardımcı olur.

### <a name="live-execution-environment"></a>Canlı yürütme ortamı

Azure dijital TWINS 'deki dijital modeller gerçek dünyanın canlı, güncel temsilleridir. Özel DTDL modellerinizde ilişkileri kullanarak, TWINS 'i ortamınızı temsil eden **canlı bir grafiğe** bağlayacaksınız.

Azure dijital TWINS grafınızı, [**Azure Digital TWINS Explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)örnek bir uygulamanın yardımıyla görüntüleyebilirsiniz.

Örnek görselleştirmenin nasıl görüneceğine ilişkin bir görünüm aşağıda verilmiştir:

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="Dijital TWINS 'i temsil eden düğümlerin grafiğini gösteren Azure Digital TWINS Explorer örnek uygulamasının ekran görüntüsü" lightbox="media/includes/azure-digital-twins-explorer.png":::

Azure dijital TWINS, bu grafiği veri işleme ve iş mantığı ile güncel tutmak için zengin bir **olay sistemi** sağlar. Bu veri işlemeyi esnek ve özelleştirilmiş yöntemlerle yönlendirmek için [Azure işlevleri](../azure-functions/functions-overview.md)gibi dış işlem kaynaklarını bağlayabilirsiniz.

Ayrıca, Azure Digital TWINS ' i güçlü **sorgu API 'si** kullanarak canlı yürütme ortamından öngörüleri de ayıklayabilirsiniz. API, özellik değerleri, ilişkiler, ilişki özellikleri, model bilgileri ve daha fazlasını içeren zengin arama koşulları ile sorgulama yapmanızı sağlar. Ayrıca, sorguları birleştirebilir, ortamınız hakkında çok çeşitli Öngörüler toplayıp sizin için önemli olan özel sorulara yanıt verebilirsiniz.

### <a name="input-from-iot-and-business-systems"></a>IoT ve iş sistemlerinden giriş

Azure dijital TWINS 'nin canlı yürütme ortamını gerçek dünyada güncel tutmak için, çözümünüzü IoT ve IoT Edge cihazlarına bağlamak üzere [IoT Hub](../iot-hub/about-iot-hub.md) kullanabilirsiniz. Hub ile yönetilen bu cihazlar ikizi grafınızın bir parçası olarak temsil edilir ve modelinize yönlendiren verileri sağlar.

Azure dijital TWINS ile bu amaçla yeni bir IoT Hub oluşturabilir veya var olan bir IoT Hub, zaten yönettiği cihazlarla birlikte bağlayabilirsiniz.

Ayrıca, REST API 'Leri veya bağlayıcıları [Logic Apps](../logic-apps/logic-apps-overview.md)gibi diğer hizmetlere kullanarak Azure dijital TWINS 'i diğer veri kaynaklarından de kullanabilirsiniz.

### <a name="output-to-tsi-storage-and-analytics"></a>TSI, Storage ve Analytics 'e çıkış

Azure dijital TWINS modelinizdeki veriler ek analiz veya depolama için aşağı akış Azure hizmetlerine yönlendirilebilir. Bu, istediğiniz veri akışlarınızı barındırmak için [Olay Hub](../event-hubs/event-hubs-about.md)'ı, [Event Grid](../event-grid/overview.md)veya [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) kullanan **olay yolları** aracılığıyla sağlanır.

Olay rotalarıyla gerçekleştirebileceğiniz bazı şeyler şunlardır:
* Azure Digital Twins verilerini [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md) içinde depolama
* Azure Digital Twins verilerini [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) veya diğer Microsoft veri analizi araçlarıyla analiz etme
* Daha büyük iş akışlarını Logic Apps ile tümleştirme
* Her ikizi zaman serisi geçmişini izlemek için Azure dijital TWINS 'i Time Series Insights bağlama
* Time Series Insights içindeki bir zaman serisi modelini Azure Digital Twins'deki bir kaynakla hizalama

Bu, Azure dijital TWINS 'in daha büyük bir çözüme bağlanabilme ve bu öngörülerle çalışmaya devam etmesi için özel ihtiyaçlarınızı desteklemeye yönelik başka bir yoldur.

## <a name="azure-digital-twins-in-a-solution-context"></a>Bir çözüm bağlamında Azure dijital TWINS

Azure dijital TWINS genellikle daha büyük bir IoT çözümünün bir parçası olarak diğer Azure hizmetleriyle birlikte kullanılır. 

Azure dijital TWINS kullanan tüm çözümler aşağıdaki bölümleri içerebilir:
* Azure dijital TWINS hizmet örneği. Bu, ikizi modellerinizi ve ikizi grafınızı kendi durumuyla depolar ve olay işlemeyi düzenler.
* Modelleri yapılandırarak, topoloji oluşturarak ve ikizi grafiğinden öngörüleri ayıklayarak Azure dijital TWINS örneğini çalıştıran bir veya daha fazla istemci uygulaması.
* Azure dijital TWINS tarafından oluşturulan olayları veya cihazlar gibi bağlı veri kaynaklarını işleyecek bir veya daha fazla dış işlem kaynağı. İşlem kaynaklarını sağlamanın yaygın bir yolu [Azure işlevleri](../azure-functions/functions-overview.md)aracılığıyla yapılır.
* Cihaz yönetimi ve IoT veri akışı özellikleri sağlamak için bir IoT Hub 'ı.
* İş akışı Tümleştirme ( [Logic Apps](../logic-apps/logic-apps-overview.md), soğuk depolama, zaman serisi tümleştirme veya analiz gibi) görevlerini işleyecek olan aşağı akış Hizmetleri.

Aşağıdaki diyagramda Azure Digital TWINS 'in daha büyük bir Azure IoT çözümü bağlamında bulunduğu yer gösterilmektedir.

:::image type="content" source="media/overview/solution-context.png" alt-text="Hem istemci uygulamalarıyla hem de dış işlem kaynaklarıyla birlikte giriş kaynaklarını, çıkış hizmetlerini ve iki yönlü iletişimi gösteren diyagram." border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>Hizmet sınırlamaları

Azure dijital TWINS **hizmet sınırlarını** buradan öğrenebilirsiniz: [Azure Digital TWINS hizmet limitleri](reference-service-limits.md). Bu, hizmetin işlev ve hız sınırlamalarını anlamak için hizmetle çalışırken ve gerektiğinde hangi limitlerin ayarlanabileceğine ilişkin yararlı olabilir.

## <a name="terminology"></a>Terminoloji

Azure Digital TWINS de dahil olmak üzere Azure IoT hizmetlerinde **yaygın IoT terimleri** ve kullanımları listesini görebilirsiniz: [Azure IoT sözlüğü](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json). Bu, Azure dijital TWINS ile çalışmaya başlarken ve bir IoT çözümü oluştururken yararlı bir başvuru olabilir.

## <a name="next-steps"></a>Sonraki adımlar

* Hızlı başlangıç: [*hızlı başlangıç: örnek bir senaryoyu araştırmak*](quickstart-azure-digital-twins-explorer.md)Için Azure dijital TWINS ile çalışmaya göz atın.

* Ya da Azure dijital TWINS kavramlarını şu [*kavramlarla okumayı başlatın: özel modeller*](concepts-models.md).