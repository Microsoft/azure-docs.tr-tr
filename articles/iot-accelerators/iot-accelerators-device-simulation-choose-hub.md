---
title: Mevcut IoT Hub 'ı cihaz benzetimi çözümü-Azure ile kullanma | Microsoft Docs
description: Bu makalede, cihaz benzetimi çözüm hızlandırıcının mevcut bir IoT Hub kullanacak şekilde nasıl yapılandırılacağı açıklanır.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889189"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Cihaz benzetimi çözüm hızlandırıcısında mevcut bir IoT Hub 'ı kullanma

Cihaz simülasyonu dağıtırken, simülasyonda kullanmak üzere bir IoT Hub 'ı dağıtmayı seçebilirsiniz. Bu seçenek [, tek bir ölçek birimiyle bir S2 katmanı IoT Hub 'ı](../iot-hub/iot-hub-scaling.md)dağıtır. Bu isteğe bağlı IoT Hub 'ını dağıtırsanız, bir simülasyon çalıştırması için başka bir IoT Hub hedeflemesini seçebilirsiniz.

İsteğe bağlı IoT Hub dağıtmadıysanız, çalıştırdığınız benzetimler için kendi merkezinizi kullanmanız gerekir.

IoT Hub 'ınız yoksa [Azure Portal](https://portal.azure.com)her zaman yeni bir tane oluşturabilirsiniz.

Önceden var olan bir IoT Hub 'ı kullanmak için, **ıothubowner** paylaşılan erişim ilkesi için bağlantı dizesine ihtiyacınız vardır. Bu bağlantı dizesini [Azure Portal](https://portal.azure.com)edinebilirsiniz:

1. Portalın yapılandırma sayfasında, **paylaşılan erişim ilkeleri**' ne tıklayın.

1. **İothubowner**öğesine tıklayın.

1. Birincil veya ikincil bağlantı dizesini kopyalayın.

[![bağlantı dizesi al](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Simülasyonu yapılandırırken kopyaladığınız bağlantı dizesini kullanın:

![Simülasyonu yapılandırma](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Sonraki adımlar

Bu nasıl yapılır kılavuzunda, var olan bir IoT Hub 'ını bir simülasyonda nasıl kullanacağınızı öğrendiniz. Daha sonra, bir benzetim için [Gelişmiş cihaz modeli oluşturmayı](iot-accelerators-device-simulation-advanced-device.md) öğrenmek isteyebilirsiniz.
