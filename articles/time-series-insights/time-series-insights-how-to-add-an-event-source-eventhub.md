---
title: Event Hubs olay kaynağı ekleme-Azure Time Series Insights | Microsoft Docs
description: Time Series Insights ortamınıza Azure Event Hubs olay kaynağı eklemeyi öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: 421a4635a80c5a7a45fb14bf900c205a06789279
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012612"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Zaman serisi görüşleri ortamınıza bir event hub olay kaynağı ekleme

Bu makalede, Azure zaman serisi görüşleri ortamınız için Azure Event Hubs'dan olay verilerini okuyan bir olay kaynağı eklemek için Azure portalını kullanmayı açıklar.

> [!NOTE]
> Bu makalede açıklanan adımlar, Time Series Insights GA ve Time Series Insights önizleme ortamları için de geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

- [Azure Time Series Insights ortamı oluşturma](./time-series-insights-update-create-environment.md)bölümünde açıklandığı gibi bir Time Series Insights ortamı oluşturun.
- Bir olay hub'ı oluşturun. Bkz. [Azure Portal kullanarak Event Hubs ad alanı ve Olay Hub 'ı oluşturma](../event-hubs/event-hubs-create.md).
- Gönderilen etkin ileti olayları olay hub'ı olması gerekir. [.NET Framework kullanarak olayları Azure Event Hubs gönderme](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md)hakkında bilgi edinin.
- Zaman serisi görüşleri ortamına gelen tüketebileceği olay hub'ında ayrılmış bir tüketici grubu oluşturun. Her zaman serisi görüşleri olay kaynağı, diğer bir tüketici ile paylaşılmaz kendi adanmış bir tüketici grubu olması gerekir. Aynı tüketici grubu olaylardan birden fazla okuyucuyu kapsayacak kullanmasına, tüm okuyucular hatalar görmeniz olasıdır. Olay hub'ı başına 20 tüketici grubu sınırı yoktur. Ayrıntılar için bkz [Event Hubs Programlama Kılavuzu](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Olay hub'ınıza bir tüketici grubu Ekle

Uygulamaları, Azure Event Hubs'dan veri çekmek için tüketici grubu kullanır. Olay Hub 'ından verileri güvenle okumak için, yalnızca bu Time Series Insights ortamı tarafından kullanılan ayrılmış bir tüketici grubu sağlayın.

Olay hub'ında yeni bir tüketici grubu eklemek için:

1. [Azure Portal](https://portal.azure.com), Olay Hub 'ı ad alanından Olay Hub 'ınızı bulun ve açın.

    [![Olay Hub 'ı ad alanınızı açın](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. Altında **varlıkları**seçin **tüketici grupları**ve ardından **tüketici grubu**.

   [Olay Hub 'ı ![-Tüketici grubu ekleme](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Üzerinde **tüketici grupları** sayfasında, yeni bir benzersiz değer için girin **adı**.  Zaman serisi görüşleri ortamına yeni bir olay kaynağı oluşturduğunuzda bu aynı adı kullanın.

1. **Oluştur**'u seçin.

## <a name="add-a-new-event-source"></a>Yeni bir olay kaynağı ekleme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Mevcut Time Series Insights ortamınızı bulun. Sol menüde **tüm kaynakları**ve ardından zaman serisi görüşleri ortamınızı seçin.

1. Altında **ortam topolojisinin**seçin **olay kaynakları**ve ardından **Ekle**.

   [Olay kaynakları altında ![Ekle düğmesini seçin](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. İçin bir değer girin **olay kaynağı adını** olan bu zaman serisi görüşleri ortamına benzersiz gibi **olay akışı**.

1. İçin **kaynak**seçin **olay hub'ı**.

1. İçin uygun değerleri seçin **içeri aktarma seçeneği**:

   * Mevcut bir olay hub'ı aboneliklerinizden biri varsa, seçin **kullanımı olay Hub'ından kullanılabilir abonelikleri**. Bu seçenek için kolay bir yaklaşımdır.

     [![bir olay kaynağı içeri aktarma seçeneği seçin](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  Aşağıdaki tablo için gerekli özellikleri açıklar **kullanımı olay Hub'ından kullanılabilir abonelikleri** seçeneği:

       [![aboneliği ve Olay Hub 'ı ayrıntıları](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Özellik | Açıklama |
       | --- | --- |
       | Abonelik | Abonelik, istenen Olay Hub 'ı örneği ve ad alanı öğesine aittir. |
       | Olay hub'ı ad alanı | İstenen olay hub 'ının örneğine ait olan olay hub 'ı ad alanı. |
       | Olay Hub'ı adı | İstenen olay hub 'ının adı. |
       | Olay Hub 'ı ilke değeri | İstenen paylaşılan erişim ilkesini seçin. Paylaşılan erişim ilkesini Event hub 'ı **Yapılandır** sekmesinde oluşturabilirsiniz. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. Olay kaynağınız için paylaşılan erişim ilkesi *gerekir* sahip **okuma** izinleri. |
       | Olay Hub'ı ilke anahtarı | Seçili olay hub 'ı ilke değerinden önceden dolduruldu. |

    * Olay hub'ı aboneliklerinize dış olup olmadığını veya Gelişmiş seçeneklerini seçmek istiyorsanız seçin **sağlayan olay hub'ı ayarlarını elle**.

       İçin gerekli özellikler aşağıdaki tabloda açıklanmıştır **sağlayan olay hub'ı ayarlarını elle** seçeneği:
 
       | Özellik | Açıklama |
       | --- | --- |
       | Abonelik Kimliği | Abonelik, istenen Olay Hub 'ı örneği ve ad alanı öğesine aittir. |
       | Kaynak grubu | İstenen olay hub 'ı örneğinin ve ad alanının ait olduğu kaynak grubu. |
       | Olay hub'ı ad alanı | İstenen olay hub 'ının örneğine ait olan olay hub 'ı ad alanı. |
       | Olay Hub'ı adı | İstenen olay hub 'ının adı. |
       | Olay Hub 'ı ilke değeri | İstenen paylaşılan erişim ilkesini seçin. Paylaşılan erişim ilkesini Event hub 'ı **Yapılandır** sekmesinde oluşturabilirsiniz. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. Olay kaynağınız için paylaşılan erişim ilkesi *gerekir* sahip **okuma** izinleri. |
       | Olay Hub'ı ilke anahtarı | Service Bus ad alanı kimlik doğrulaması yapmak için kullanılan paylaşılan erişim anahtarı. Birincil veya ikincil anahtarı buraya girin. |

    * Her iki seçenek de aşağıdaki yapılandırma seçeneklerini paylaşır:

       | Özellik | Açıklama |
       | --- | --- |
       | Olay Hub 'ı Tüketici grubu | Tüketici grubu olayları olay hub'ından okur. Olay kaynağınız için ayrılmış bir tüketici grubu kullanmanızı öneririz. |
       | Olay serileştirme biçimi | Şu anda, JSON yalnızca serileştirme biçimidir. Olay iletilerinin bu biçimde olması veya verilerin okunamaz olması gerekir. |
       | Zaman damgası özellik adı | Bu değeri belirlemek için olay hub'ına gönderilen ileti verileri ileti biçimi anlamanız gerekir. Bu değer **adı** ileti verileri, olay zaman damgası kullanmak istediğiniz belirli olay özelliğini. Değer büyük/küçük harf duyarlıdır. Boş bırakılırsa **olay sıraya alma süresi** olay kaynağı olarak olay zaman damgası kullanılır. |

1. Olay hub'ınıza eklediğiniz adanmış Time Series Insights tüketici grubu adını ekleyin.

1. **Oluştur**'u seçin.

   Olay kaynağı oluşturulduktan sonra, Time Series Insights ortamınızda akış verilerini otomatik olarak başlatır.

## <a name="next-steps"></a>Sonraki adımlar

* [Veri erişim ilkelerini tanımlama](time-series-insights-data-access.md) verilerin güvenliğini sağlamak için.

* [Olayları gönderme](time-series-insights-send-events.md) olay kaynağına.

* Ortamınızda erişim [Time Series Insights gezgininin](https://insights.timeseries.azure.com).
