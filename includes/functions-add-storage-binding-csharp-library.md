---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592784"
---
Bir C# sınıf kitaplığı projesinde bağlamalar, işlev yönteminde bağlama öznitelikleri olarak tanımlanır. *Function. JSON* dosyası, bu özniteliklere göre otomatik olarak oluşturulur.

*HttpTrigger.cs* proje dosyasını açın ve aşağıdaki `using` ifadeyi ekleyin:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Aşağıdaki parametreyi `Run` Yöntem tanımına ekleyin:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

Parametresi, işlev tamamlandığında `ICollector<T>` çıkış bağlamaya yazılan bir ileti koleksiyonunu temsil eden bir türdür. `msg` Bu durumda, çıktı adlı `outqueue`bir depolama kuyruğudur. Depolama hesabının bağlantı dizesi tarafından `StorageAccountAttribute`ayarlanır. Bu öznitelik, depolama hesabı bağlantı dizesini içeren ayarı belirtir ve sınıfı, yöntemi veya parametre düzeyinde uygulanabilir. Bu durumda, zaten varsayılan depolama hesabını `StorageAccountAttribute` kullandığınızdan atlayabilirsiniz.

Çalıştırma yöntemi tanımı artık aşağıdaki gibi görünmelidir:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
