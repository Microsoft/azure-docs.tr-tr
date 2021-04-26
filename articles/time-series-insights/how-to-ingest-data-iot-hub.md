---
title: IoT Hub olay kaynağı ekleme-Azure Time Series Insights | Microsoft Docs
description: Azure zaman serisi Insight ortamınıza IoT Hub olay kaynağı ekleme hakkında bilgi edinin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 85773ec66dbe567afcfd1afca1d14aa0119fb8f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103464029"
---
# <a name="add-an-iot-hub-event-source-to-your-azure-time-series-insight-environment"></a>Azure zaman serisi Insight ortamınıza IoT Hub olay kaynağı ekleme

Bu makalede, Azure IoT Hub verileri Azure Time Series Insights ortamınıza okuyan bir olay kaynağı eklemek için Azure portal nasıl kullanılacağı açıklanır.

> [!NOTE]
> Bu makaledeki yönergeler hem Azure Time Series Insights Gen 1 hem de Azure zaman serisi Insight Gen 2 ortamları için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Time Series Insights ortamı](./tutorial-set-up-environment.md)oluşturun.
* [Azure Portal kullanarak bir IoT Hub 'ı](../iot-hub/iot-hub-create-through-portal.md)oluşturun.
* IoT Hub 'ında etkin ileti olayları gönderilmesi gerekir.
* IoT Hub 'ında tarafından kullanılacak Azure zaman serisi Insight ortamı için adanmış bir tüketici grubu oluşturun. Her Azure zaman serisi Insight olay kaynağı, başka bir tüketici ile paylaşılmayan kendi adanmış bir tüketici grubuna sahip olmalıdır. Birden çok okuyucu aynı tüketici grubundan olayları tükettiği takdirde, tüm okuyucular hatalara neden olabilir. Ayrıntılar için [Azure IoT Hub Geliştirici Kılavuzu](../iot-hub/iot-hub-devguide.md)' nu okuyun.

### <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub 'ınıza bir tüketici grubu ekleme

Uygulamalar, Azure IoT Hub verileri çekmek için tüketici gruplarını kullanır. IoT Hub 'ınızdaki verileri güvenle okumak için, yalnızca bu Azure zaman serisi Insight ortamı tarafından kullanılan adanmış bir tüketici grubu sağlayın.

IoT Hub 'ınıza yeni bir tüketici grubu eklemek için:

1. [Azure Portal](https://portal.azure.com)IoT Hub 'ınızı bulun ve açın.

1. **Ayarlar**' ın altında, **yerleşik uç noktalar**' ı seçin ve ardından **Olaylar** uç noktasını seçin.

   [![Derleme uç noktaları sayfasında, olaylar düğmesini seçin](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-connect-iot-hub.png#lightbox)

1. **Tüketici grupları**' nın altında, Tüketici grubu için benzersiz bir ad girin. Yeni bir olay kaynağı oluştururken Azure zaman serisi Insight ortamınızdan aynı adı kullanın.

1. **Kaydet**’i seçin.

## <a name="add-a-new-event-source"></a>Yeni bir olay kaynağı ekleyin

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Soldaki menüden **Tüm kaynaklar**'ı seçin. Azure zaman serisi Insight ortamınızdan seçim yapın.

1. **Ayarlar** altında **olay kaynakları**' nı ve ardından **Ekle**' yi seçin.

   [![Olay kaynakları ' nı seçin ve ardından Ekle düğmesini seçin.](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-add-event-source.png#lightbox)

1. **Yeni olay kaynağı** bölmesinde, **olay kaynağı adı** Için, bu Azure zaman serisi Insight ortamına özgü bir ad girin. Örneğin, **olay akışını** girin.

1. **Kaynak** için **IoT Hub** seçin.

1. **Içeri aktarma seçeneği** için bir değer seçin:

   * Aboneliklerinizden birinde bir IoT Hub 'ınız zaten varsa, **kullanılabilir aboneliklerden IoT Hub kullan**' ı seçin. Bu seçenek en kolay yaklaşımdır.

     [![Yeni olay kaynağı bölmesinde seçenekleri belirleyin](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-select-an-import-option.png#lightbox)

   * Aşağıdaki tabloda, **kullanılabilir abonelikler arasından IoT Hub kullan** seçeneği için gereken özellikler açıklanmaktadır:

       [![Yeni olay kaynağı bölmesi-kullanılabilir aboneliklerden IoT Hub kullan seçeneğinde ayarlanacak Özellikler](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png)](media/time-series-insights-how-to-add-an-event-source-iothub/tsi-create-configure-confirm.png#lightbox)

       | Özellik | Açıklama |
       | --- | --- |
       | Abonelik | İstenen IoT Hub 'ına ait abonelik. |
       | IoT Hub adı | Seçilen IoT Hub 'ının adı. |
       | IoT Hub ilkesi adı | Paylaşılan erişim ilkesini seçin. Paylaşılan erişim ilkesini IoT Hub ayarları sekmesinde bulabilirsiniz. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. Olay kaynağınıza ait paylaşılan erişim ilkesinde **hizmet bağlantısı** izinleri *olmalıdır* . |
       | IoT Hub ilke anahtarı | Anahtar önceden doldurulur. |

   * IoT Hub 'ı abonelikleriniz için ise veya Gelişmiş Seçenekler ' i seçmek istiyorsanız, **IoT Hub ayarlarını el Ile sağla**' yı seçin.

      Aşağıdaki tabloda, **IoT Hub ayarlarının el Ile sağlanması** için gereken özellikler açıklanmaktadır:

       | Özellik | Açıklama |
       | --- | --- |
       | Abonelik Kimliği | İstenen IoT Hub 'ına ait abonelik. |
       | Kaynak grubu | IoT Hub 'ının oluşturulduğu kaynak grubu adı. |
       | IoT Hub adı | IoT Hub 'ınızın adı. IoT Hub 'ınızı oluşturduğunuzda IoT Hub için bir ad girdiniz. |
       | IoT Hub ilkesi adı | Paylaşılan erişim ilkesi. Paylaşılan erişim ilkesini IoT Hub ayarları sekmesinde oluşturabilirsiniz. Her paylaşılan erişim ilkesinin adı, sizin ayarladığınız izinler ve anahtarlara erişim vardır. Olay kaynağınıza ait paylaşılan erişim ilkesinde **hizmet bağlantısı** izinleri *olmalıdır* . |
       | IoT Hub ilke anahtarı | Azure Service Bus ad alanına erişimin kimliğini doğrulamak için kullanılan paylaşılan erişim anahtarı. Birincil veya ikincil anahtarı buraya girin. |

   * Her iki seçenek de aşağıdaki yapılandırma seçeneklerini paylaşır:

       | Özellik | Açıklama |
       | --- | --- |
       | IoT Hub 'ı Tüketici grubu | IoT Hub 'ından olayları okuyan Tüketici grubu. Olay kaynağınız için adanmış bir tüketici grubu kullanmanızı önemle tavsiye ederiz. |
       | Olay serileştirme biçimi | Şu anda JSON tek kullanılabilir serileştirme biçimidir. Olay iletileri bu biçimde olmalıdır veya hiçbir veri okunamaz. |
       | Zaman damgası özellik adı | Bu değeri öğrenmek için, IoT Hub 'ına gönderilen ileti verilerinin ileti biçimini anlamanız gerekir. Bu değer, olay zaman damgası olarak kullanmak istediğiniz ileti verilerinde belirli olay özelliğinin **adıdır** . Değer, büyük/küçük harfe duyarlıdır. Boş bırakılırsa olay kaynağı olay **sıraya alma süresi** olay zaman damgası olarak kullanılır. |

1. IoT Hub 'ınıza eklediğiniz adanmış Azure zaman serisi Insight Tüketici grubu adını ekleyin.

1. **Oluştur**’u seçin.

1. Olay kaynağını oluşturduktan sonra, Azure zaman serisi öngörüleri otomatik olarak ortamınıza akış verilerini başlatır.

## <a name="next-steps"></a>Sonraki adımlar

* Verilerin güvenliğini sağlamak için [veri erişim Ilkeleri tanımlayın](./concepts-access-policies.md) .

* [Olayları](time-series-insights-send-events.md) olay kaynağına gönderin.

* [Azure zaman serisi Insight Gezgini](https://insights.timeseries.azure.com)' nde ortamınıza erişin.
