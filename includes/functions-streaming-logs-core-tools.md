---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 1928a8238cd73087e3c199675574dd1395f4d76d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881361"
---
#### <a name="built-in-log-streaming"></a>Yerleşik günlük akışı

Aşağıdaki örnekte olduğu gibi, Azure 'da çalışan belirli bir işlev uygulamasının akış günlüklerini almaya başlamak için seçeneğinikullanın:`logstream`

```bash
func azure functionapp logstream <FunctionAppName>
```

#### <a name="live-metrics-stream"></a>Canlı Ölçüm Akışı

Ayrıca, aşağıdaki örnekte olduğu [](../articles/azure-monitor/app/live-stream.md) gibi `--browser` seçeneğini ekleyerek işlev uygulamanızın canlı ölçüm akışı yeni bir tarayıcı penceresinde görüntüleyebilirsiniz:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
