---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 178fa7d5f129a12736ec068fca605ba24cd37839
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955883"
---
Bir Azure Işlevlerinde oluşan hatalar aşağıdaki kaynaklardan herhangi birinden gelebilir:

- Yerleşik Azure Işlevleri [Tetikleyicileri ve bağlamaları](..\articles\azure-functions\functions-triggers-bindings.md) kullanımı
- Temel alınan Azure Hizmetleri API 'Leri çağrıları
- REST uç noktalarına çağrılar
- İstemci kitaplıkları, paketler veya üçüncü taraf API 'Leri çağrıları

Aşağıdaki katı hata işleme uygulamaları, veri kaybını veya eksik iletileri kaybetmemek için önemlidir. Önerilen hata işleme uygulamaları aşağıdaki eylemleri içerir:

- [Application Insights etkinleştir](../articles/azure-functions/functions-monitoring.md)
- [Yapılandırılmış hata işlemeyi kullan](#use-structured-error-handling)
- [Idempotlik tasarımı](../articles/azure-functions/functions-idempotent.md)
- [Yeniden deneme Ilkelerini Uygula](../articles/azure-functions/functions-reliable-event-processing.md) (uygun olduğunda)

### <a name="use-structured-error-handling"></a>Yapılandırılmış hata işlemeyi kullan

Yakalama ve yayımlama hataları, uygulamanızın sistem durumunu izlemek için kritik öneme sahiptir. Herhangi bir işlev kodunun en üst düzeyinde bir try/catch bloğu bulunmalıdır. Catch bloğunda, hataları yakalayabilir ve yayımlayabilirsiniz.

### <a name="retry-support"></a>Yeniden deneyin desteği

Aşağıdaki Tetikleyiciler yerleşik yeniden deneme desteğine sahiptir:

* [Azure Blob depolama](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure kuyruk depolama](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (kuyruk/konu)](../articles/azure-functions/functions-bindings-service-bus.md)

Bu, varsayılan olarak, istekleri beş kata kadar yeniden dener. Beşinci yeniden denemeden sonra her iki tetikleyici de bir [Poison kuyruğuna](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages)ileti yazar.

Diğer Tetikleyiciler veya bağlama türleri için yeniden deneme ilkelerini el ile uygulamanız gerekir. El ile uygulamalar, bir [zarar iletisi kuyruğuna](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs)hata bilgilerini yazmayı içerebilir. Bir Poison kuyruğuna yazarak, daha sonra işlemleri yeniden deneme şansınız vardır. Bu yaklaşım, BLOB depolama tetikleyicisi tarafından kullanılan bir yaklaşımdır.
