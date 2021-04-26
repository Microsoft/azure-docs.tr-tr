---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 0159ceb6e5d6d64a7a9bda383396607e4ce05b84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96020387"
---
#### <a name="built-in-log-streaming"></a>Yerleşik günlük akışı

`logstream`Aşağıdaki örnekte olduğu gibi, Azure 'da çalışan belirli bir işlev uygulamasının akış günlüklerini almaya başlamak için seçeneğini kullanın:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Bir tüketim planında Linux üzerinde çalışan işlev uygulamalarına yönelik temel araçlarda yerleşik günlük akışı henüz etkin değildir. Bu barındırma planları için, bunun yerine, günlükleri neredeyse gerçek zamanlı olarak görüntülemek için Canlı Ölçüm Akışı kullanmanız gerekir.

#### <a name="live-metrics-stream"></a>Canlı Ölçüm Akışı

[](../articles/azure-monitor/app/live-stream.md) `--browser` Aşağıdaki örnekte olduğu gibi seçeneğini ekleyerek işlev uygulamanızın canlı ölçüm akışı yeni bir tarayıcı penceresinde görüntüleyebilirsiniz:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
