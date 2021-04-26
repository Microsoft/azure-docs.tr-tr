---
title: Azure IoT cihaz geliştiricileri için bağlantı seçenekleri hakkında bilgi edinin
description: Azure IoT cihaz geliştiricileri için yaygın olarak kullanılan cihaz bağlantı seçenekleri ve araçları hakkında bilgi edinin.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 8669919192b1e6394043842d7d23f8829ec7c71e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589559"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>Genel Bakış: Azure IoT cihaz geliştiricileri için bağlantı seçenekleri
Cihazlarla çalışan bir geliştirici olarak, Azure IoT cihazlarını bağlamak ve yönetmek için çeşitli seçenekleriniz vardır. Bu makalede, cihazları bağlamanıza ve yönetmenize yardımcı olmak için en yaygın kullanılan seçenekler ve araçlar açıklanır.

Cihazlarınız için Azure IoT bağlantı seçeneklerini değerlendirirken, aşağıdaki öğeleri karşılaştırmak yararlı olur:
- Azure IoT cihaz uygulaması platformları
- Cihazları bağlamak ve yönetmek için Araçlar

## <a name="application-platforms-iot-central-and-iot-hub"></a>Uygulama platformları: IoT Central ve IoT Hub
Azure IoT, cihaz etkin bulut uygulamaları için platformlar olan iki hizmet içerir: Azure IoT Central ve Azure IoT Hub. IoT uygulamasını barındırmak ve cihazları bağlamak için bunlardan birini kullanabilirsiniz.
- [IoT Central](../iot-central/core/overview-iot-central.md) , IoT çözümleri ile çalışmanın karmaşıklığını ve maliyetini azaltmak için tasarlanmıştır. IoT uygulamalarını barındırmak için tam bir platform sağlayan bir hizmet olarak yazılım (SaaS) uygulamasıdır. IoT uygulamaları oluşturma ve yönetme yaşam döngüsünün tamamını kolaylaştırmak için IoT Central Web Kullanıcı arabirimini kullanabilirsiniz. Web Kullanıcı arabirimi, uygulama oluşturma ve milyonlarca cihazdan çok sayıda cihaza bağlanma ve yönetme görevlerini basitleştirir. IoT Central, uygulamalar oluşturmak ve yönetmek için IoT Hub kullanır, ancak ayrıntıları Kullanıcı tarafından saydam olarak tutar. Genellikle IoT Central, Web Kullanıcı arabirimi aracılığıyla daha az karmaşıklık ve geliştirme çabasıyla ve Basitleştirilmiş cihaz yönetimi sağlar.
- [IoT Hub](../iot-hub/about-iot-hub.md) , IoT uygulamaları ve bağlı cihazlar arasındaki çift yönlü iletişim için bir merkezi ileti hub 'ı olarak davranır. IoT uygulamalarını barındırmak için de bir platform sağlayan bir hizmet olarak platform (PaaS) uygulamasıdır. IoT Central gibi, milyonlarca cihazı destekleyecek şekilde ölçeklendirebilir. Genellikle IoT Hub, uygulama tasarımınız üzerinde daha fazla denetim ve özelleştirme ve hizmetle çalışmaya yönelik daha fazla geliştirici aracı seçeneği sunarak geliştirme ve yönetim karmaşıklığının bazı artışına göre daha fazla bilgi sağlar.

## <a name="tools-to-connect-and-manage-devices"></a>Cihazları bağlamak ve yönetmek için Araçlar
IoT uygulamanızı barındırmak için IoT Hub veya IoT Central ' yı seçtikten sonra, çeşitli geliştirici araçları seçenekleriniz vardır. Bu araçları, seçtiğiniz IoT uygulama platformuna bağlanmak ve uygulama ve cihaz oluşturup yönetmek için kullanabilirsiniz. Aşağıdaki tabloda ortak araç seçenekleri özetlenmektedir. 

> [!NOTE]
> Aşağıdaki araçlara ek olarak, REST API, Azure SDK 'larını veya Azure Resource Manager şablonlarını kullanarak IoT uygulamalarını programlı bir şekilde oluşturabilir ve yönetebilirsiniz. [IoT Hub](../iot-hub/about-iot-hub.md) ve [IoT Central](../iot-central/core/overview-iot-central.md) hizmeti belgelerinde daha fazla bilgi edinebilirsiniz.

|Araç  |IoT platformunu &nbsp; &nbsp; &nbsp; destekler &nbsp; |Belgeler  |Description  |
|---------|---------|---------|---------|
|Merkezi Web Kullanıcı arabirimi     | Orta | [Merkezi hızlı başlangıç](../iot-central/core/quick-deploy-iot-central.md) | IoT Central için tarayıcı tabanlı portal. |
|Azure portalı     | Merkez, orta      | [Azure Portal olan bir IoT Hub 'ı oluşturma](../iot-hub/iot-hub-create-through-portal.md), [Azure Portal IoT Central yönetme](../iot-central/core/howto-manage-iot-central-from-portal.md)| IoT Hub ve cihazlar için tarayıcı tabanlı portal. Ayrıca, IoT Central dahil diğer Azure kaynaklarıyla de birlikte kullanılabilir. |
|Azure IoT Gezgini     | Hub | [Azure IoT Gezgini](https://github.com/Azure/azure-iot-explorer#azure-iot-explorer-preview) | IoT Hub 'ları oluşturulamıyor. Cihazları yönetmek için mevcut bir IoT Hub 'ına bağlanır. Genellikle CLı veya Portal ile kullanılır.|
|Azure CLI     | Merkez, orta          | [CLI Ile IoT Hub 'ı oluşturma](../iot-hub/iot-hub-create-using-cli.md), [Azure CLI 'dan IoT Central yönetme](../iot-central/core/howto-manage-iot-central-from-cli.md) | IoT uygulamaları oluşturmak ve yönetmek için komut satırı arabirimi. |
|Azure PowerShell     | Merkez, orta   | [PowerShell ile bir IoT Hub 'ı oluşturma](../iot-hub/iot-hub-create-using-powershell.md), [Azure PowerShell IoT Central yönetme](../iot-central/core/howto-manage-iot-central-from-powershell.md) | IoT uygulamaları oluşturmak ve yönetmek için PowerShell arabirimi |
|VS Code için Azure IoT Araçları  | Hub | [VS Code araçlarla IoT Hub 'ı oluşturma](../iot-hub/iot-hub-create-use-iot-toolkit.md) | IoT Hub uygulamalar için VS Code uzantısı. |

## <a name="next-steps"></a>Sonraki adımlar
Cihazları Azure IoT 'ye bağlama seçenekleriniz hakkında daha fazla bilgi edinmek için aşağıdaki hızlı başlangıç yöntemlerini inceleyin:
- IoT Central: [Azure IoT Central uygulaması oluşturma](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [bir cihazdan IoT Hub 'ına telemetri gönderme ve Azure CLI ile izleme](../iot-hub/quickstart-send-telemetry-cli.md)