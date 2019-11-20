---
title: Azure IoT Central uygulamanızı Dynamics 365 alan hizmetleriyle bağlama | Microsoft Docs
description: Azure IoT Central ve Dynamics 365 alan hizmeti ile uçtan uca bir çözüm oluşturmayı öğrenin
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 957e9337bf8ea5941b140ba4f3266417d36df6a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498768"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Azure IoT Central ve Dynamics 365 alan hizmeti ile uçtan uca çözüm oluşturma 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bir Oluşturucu olarak, Azure IoT Central uygulamanızın diğer iş sistemleriyle tümleştirilmesine olanak sağlayabilirsiniz. 


Örneğin, bağlı bir çöp yönetimi çözümünde, bağlı çöp kutusu 'ndaki IoT sensörlerinden verileri temel alan çöp koleksiyonları 'nın dağıtım işlemini en iyileştirebilirsiniz. [IoT Central bağlı çöp yönetimi uygulamanızda](./tutorial-connected-waste-management.md) , kuralları ve eylemleri yapılandırabilir ve bu ayarı Dynamics alan hizmeti 'nde uyarı oluşturmayı tetikleyebilirsiniz. Bu senaryo, uygulamalar ve hizmetler genelinde iş akışlarını otomatikleştirmek için IoT Central doğrudan yapılandırabileceğiniz Microsoft Flow kullanılarak gerçekleştirilir. Ayrıca, alan hizmetindeki hizmet etkinliklerine bağlı olarak, bilgiler Azure IoT Central 'ye gönderilebilir. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Azure IoT Central uygulamanızı Dynamics 365 alan hizmetleriyle bağlama 

Aşağıdaki uçtan uca tümleştirme işlemlerine, saf bir yapılandırma deneyimine göre kolayca uygulanabilirler:
* Azure IoT Central, Tanılama için bağlı alan hizmeti (IoT uyarısı olarak) ile cihaz bozuklukları hakkında bilgi gönderebilir.
* Bağlı alan hizmeti, cihaz bozuklularından tetiklenen servis talepleri veya iş emirleri oluşturabilir.
* Bağlı alan hizmeti, kapalı kalma süresinin oluşmasını önlemeye yönelik teknisyenleri İnceleme için zamanlayabilir.
* Azure IoT Central cihaz panosu, ilgili hizmet ve zamanlama bilgileriyle güncelleştirilir.


## <a name="next-steps"></a>Sonraki adımlar
* [IoT Central kamu şablonları](./overview-iot-central-government.md) hakkında daha fazla bilgi edinin
* [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central) hakkında daha fazla bilgi edinin
* [Dynamics 365 alan Hizmetleri](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview) hakkında daha fazla bilgi edinin
