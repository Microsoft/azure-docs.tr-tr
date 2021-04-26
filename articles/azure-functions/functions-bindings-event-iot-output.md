---
title: Azure Işlevleri için Azure IoT Hub çıkış bağlaması
description: Azure Işlevleri 'ni kullanarak Azure IoT Hub 'Larına ileti yazmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91871788"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure Işlevleri için Azure IoT Hub çıkış bağlaması

Bu makalede, IoT Hub için Azure Işlevleri çıkış bağlamalarıyla nasıl çalışılacağı açıklanmaktadır. IoT Hub desteği, [Azure Event Hubs bağlamasını](functions-bindings-event-hubs.md)temel alır.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Aşağıdaki kod örnekleri Olay Hub 'ı API 'sini kullanırken, verilen söz dizimi IoT Hub işlevleri için geçerlidir.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Bir olay hub 'ı olay akışına (tetikleyici) gönderilen olaylara yanıt verme](./functions-bindings-event-iot-trigger.md)
