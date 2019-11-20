---
title: Ortam oluşturma-Azure Time Series Insights | Microsoft Docs
description: Yeni bir Time Series Insights ortamı oluşturmak için Azure portal nasıl kullanacağınızı öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: fc3f21c95ad6f4bb033336018dddb9e966e9cc9a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006911"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Azure Portal’da yeni Zaman Serisi Görüşleri ortamı oluşturma

Bu makalede, Azure portal kullanılarak yeni bir Time Series Insights ortamının nasıl oluşturulacağı açıklanır.

Time Series Insights, Azure IoT Hub 'Larına veri akışını görselleştirmenizi ve sorgulamayı ve dakikalar içinde Event Hubs, büyük miktarlarda zaman serisi verilerini Saniyeler içinde sorgulamanızı sağlar.  Bu, Nesnelerin İnterneti (IoT) ölçeği için tasarlanmıştı ve terabayt veri işleyebilir.

## <a name="steps-to-create-the-environment"></a>Ortam oluşturma adımları

Ortam oluşturmak için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **+ Kaynak oluştur** düğmesini seçin.

1. **Nesnelerin interneti** kategorisini seçin ve **Time Series Insights**' ı seçin.

   [Time Series Insights ortamı oluşturma![](media/time-series-insights-get-started/1-new-tsi.png)](media/time-series-insights-get-started/1-new-tsi.png#lightbox))

1. **Time Series Insights** sayfasında **Oluştur**' u seçin.

1. Gerekli parametreleri girin. Aşağıdaki tabloda her bir parametre açıklanmaktadır:
   
   [![Time Series Insights kaynak grubu oluşturma](media/time-series-insights-get-started/2-create-tsi.png)](media/time-series-insights-get-started/2-create-tsi.png#lightbox)
   
   Ayar|Önerilen değer|Açıklama
   ---|---|---
   Ortam adı | Benzersiz bir ad | Bu ad, [zaman serisi Gezgininde](https://insights.timeseries.azure.com) ortamı temsil eder
   Abonelik | Aboneliğiniz | Birden çok aboneliğiniz varsa, tercihen olay kaynağınızı içeren aboneliği seçin. Zaman Serisi Görüşleri aynı abonelikteki mevcut Azure IoT Hub ve Event Hub kaynaklarını otomatik olarak algılayabilir.
   Kaynak grubu | Yeni oluştur veya var olanı kullan | Kaynak grubu, birlikte kullanılan Azure kaynakları koleksiyonudur. Örneğin, Olay Hub 'ınızı veya IoT Hub içeren mevcut bir kaynak grubunu seçebilirsiniz. Ya da bu kaynak diğer kaynaklarla ilgili değilse yeni bir tane oluşturabilirsiniz.
   Konum | Olay kaynağınızı en yakın | Tercihen, siteler arası ve çapraz bölge bant genişliği maliyetlerini ve verileri bölgenin dışına taşırken gecikme süresi eklenmesini önlemek için, olay kaynak verilerinizi içeren veri merkezi konumunu seçin.
   Fiyatlandırma katmanı | S1 | Gerekli aktarım hızını seçin. En düşük maliyetler ve başlangıç kapasitesi için S1 ' i seçin.
   Kapasite | 1 | Kapasite, seçili SKU ile ilişkili giriş fiyatı, depolama kapasitesi ve maliyet için geçerlidir.  Oluşturduktan sonra ortam kapasitesini değiştirebilirsiniz. En düşük maliyetler için 1 kapasitesini seçin. 
  
1. Hazırlama işlemini başlatmak için **Oluştur** ' u seçin. Bu işlem birkaç dakika sürebilir.

1. Dağıtım sürecini izlemek için **Bildirimler** simgesini (zil simgesi) seçin.

   [bildirimleri ![Izleyin](media/time-series-insights-get-started/3-notifications.png)](media/time-series-insights-get-started/3-notifications.png#lightbox)

    Dağıtım başarılı olduğunda diğer özellikleri yapılandırmak için **Kaynağa Git** ' i seçebilir, veri erişim ilkeleriyle güvenliği ayarlayabilir, olay kaynaklarını ekleyebilir ve diğer eylemleri yapabilirsiniz.

1. Kaynak **genel**görünümünde, gelecekte Time Series Insights ortamınıza kolayca erişmek için sağ üst köşedeki **sabitleme simgesini** seçin.

   [panoya Time Series Insights PIN ![oluşturma](media/time-series-insights-get-started/4-pin-create.png)](media/time-series-insights-get-started/4-pin-create.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

* Ortamınızın güvenliğini sağlamak için [veri erişim Ilkeleri tanımlayın](time-series-insights-data-access.md) .

* Azure Time Series Insights ortamınıza [bir olay hub 'ı olay kaynağı ekleyin](time-series-insights-how-to-add-an-event-source-eventhub.md) .

* [Olayları gönderme](time-series-insights-send-events.md) olay kaynağına.

* [Time Series Insights Explorer](https://insights.timeseries.azure.com)'da ortamınızı görüntüleyin.
