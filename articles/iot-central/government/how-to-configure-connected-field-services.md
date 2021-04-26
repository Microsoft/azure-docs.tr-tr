---
title: Azure IoT Central uygulamanızı Dynamics 365 alan hizmetleriyle bağlama | Microsoft Docs
description: Azure IoT Central ve Dynamics 365 alan hizmeti ile uçtan uca bir çözüm oluşturmayı öğrenin
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1d098f56bbadfe115620580c8d93fb6dd021550d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586683"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Azure IoT Central ve Dynamics 365 alan hizmeti ile uçtan uca çözüm oluşturma 
Bir Oluşturucu olarak, Azure IoT Central uygulamanızın diğer iş sistemleriyle tümleştirilmesine olanak sağlayabilirsiniz. 

Örneğin, bağlı bir çöp yönetimi çözümünde çöp koleksiyonları kamyonları gönderimi iyileştirebilirsiniz. İyileştirme, bağlı çöp kutularının IoT algılayıcı verilerine göre yapılabilir. [IoT Central bağlı çöp yönetimi uygulamanızda](./tutorial-connected-waste-management.md) , kuralları ve eylemleri yapılandırabilir ve bu ayarı Dynamics alan hizmeti 'nde uyarı oluşturmayı tetikleyebilirsiniz. Bu senaryo, uygulamalar ve hizmetler genelinde iş akışlarını otomatikleştirmek için IoT Central doğrudan yapılandırılacak güç otomatikleştirme kullanılarak gerçekleştirilir. Ayrıca, alan hizmetindeki hizmet etkinliklerine bağlı olarak, bilgiler Azure IoT Central 'ye gönderilebilir. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Azure IoT Central uygulamanızı Dynamics 365 alan hizmetleriyle bağlama 

Aşağıdaki tümleştirme işlemlerine, saf bir yapılandırma deneyimine göre kolayca uygulanabilirler:
* Azure IoT Central, Tanılama için bağlı alan hizmeti (IoT uyarısı olarak) ile cihaz bozuklukları hakkında bilgi gönderebilir.
* Bağlı alan hizmeti, cihaz bozuklularından tetiklenen servis talepleri veya iş emirleri oluşturabilir.
* Bağlı alan hizmeti, kapalı kalma süresinin oluşmasını önlemeye yönelik teknisyenleri İnceleme için zamanlayabilir.
* Azure IoT Central cihaz panosu, ilgili hizmet ve zamanlama bilgileriyle güncelleştirilir.


## <a name="next-steps"></a>Sonraki adımlar
* [IoT Central kamu şablonları](./overview-iot-central-government.md) hakkında daha fazla bilgi edinin
* [IoT Central](../core/overview-iot-central.md) hakkında daha fazla bilgi edinin
* [Dynamics 365 alan Hizmetleri](/dynamics365/field-service/cfs-iot-overview) hakkında daha fazla bilgi edinin
