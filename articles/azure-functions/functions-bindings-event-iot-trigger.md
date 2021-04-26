---
title: Azure Işlevleri için Azure IoT Hub tetikleyicisi
description: Azure Işlevlerinde bir IoT Hub olay akışına gönderilen olaylara yanıt vermeyi öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612295"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Işlevleri için Azure IoT Hub tetikleyicisi

Bu makalede, IoT Hub için Azure Işlevleri bağlamalarıyla nasıl çalışılacağı açıklanmaktadır. IoT Hub desteği, [Azure Event Hubs bağlamasını](functions-bindings-event-hubs.md)temel alır.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](functions-bindings-event-iot.md).

> [!IMPORTANT]
> Aşağıdaki kod örnekleri Olay Hub 'ı API 'sini kullanırken, verilen söz dizimi IoT Hub işlevleri için geçerlidir.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.jsözelliklerde

Dosyadaki [host.js](functions-host-json.md#eventhub) , Olay Hub 'ı tetikleme davranışını denetleyen ayarları içerir. Kullanılabilir ayarlarla ilgili ayrıntılar için [ ayarlarıhost.js](functions-bindings-event-iot.md#hostjson-settings) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Olayları bir olay akışına yazma (çıkış bağlama)](./functions-bindings-event-iot-output.md)
