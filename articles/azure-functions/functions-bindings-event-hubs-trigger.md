---
title: Azure Işlevleri için Azure Event Hubs tetikleyicisi
description: Azure Işlevleri 'nde Azure Event Hubs tetikleyiciyi kullanmayı öğrenin.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608925"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Azure Işlevleri için Azure Event Hubs tetikleyicisi

Bu makalede, Azure Işlevleri için [azure Event Hubs](../event-hubs/event-hubs-about.md) tetikleyicisiyle nasıl çalışılacağı açıklanmaktadır. Azure Işlevleri Event Hubs için tetikleyiciyi ve [Çıkış bağlamalarını](functions-bindings-event-hubs-output.md) destekler.

Kurulum ve yapılandırma ayrıntıları hakkında bilgi için bkz. [genel bakış](functions-bindings-event-hubs.md).

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>Ayarlar üzerinde host.js

Dosyadaki [host.js](functions-host-json.md#eventhub) , Olay Hub 'ı tetikleme davranışını denetleyen ayarları içerir. Kullanılabilir ayarlarla ilgili ayrıntılar için [ ayarlarıhost.js](functions-bindings-event-hubs.md#hostjson-settings) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Olayları bir olay akışına yazma (çıkış bağlama)](./functions-bindings-event-hubs-output.md)
