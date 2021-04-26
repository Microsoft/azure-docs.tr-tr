---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623349"
---
## <a name="add-to-your-functions-app"></a>Işlevler uygulamanıza ekleme

### <a name="functions-2x-and-higher"></a>İşlevler 2.x ve üzeri

Tetikleyici ve bağlamalarla çalışma, uygun pakete başvurmanız gerekir. Diğer tüm uygulama türleri için Uzantı paketi kullanıldığında, .NET sınıf kitaplıkları için NuGet paketi kullanılır.

| Dil                                        | Ekleme ölçütü...                                   | Açıklamalar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet paketi]yükleniyor, sürüm 3. x | |
| C# betiği, Java, JavaScript, Python, PowerShell | [Uzantı paketi] kaydediliyor          | [Azure Araçları uzantısının] Visual Studio Code ile kullanılması önerilir. |
| C# betiği (yalnızca çevrimiçi-Azure portal)         | Bağlama ekleme                            | İşlev uygulamanızı yeniden yayımlamak zorunda kalmadan mevcut bağlama uzantılarını güncelleştirmek için bkz. [uzantılarınızı güncelleştirme]. |

[NuGet paketi]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[Uzantı paketi]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Uzantılarınızı güncelleştirme]: ../articles/azure-functions/functions-bindings-register.md
[Azure Araçları uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs uzantısı 5. x ve üzeri

Event Hubs bağlamaları uzantısının yeni bir sürümü [Önizleme NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1)olarak kullanılabilir. Bu önizleme, [gizli anahtar yerine bir kimlik kullanarak bağlanma](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection)özelliği sunar. .NET uygulamaları için, aynı zamanda, bu tür `Microsoft.Azure.EventHubs` [Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs)' deki türleri daha yeni türlerle değiştirerek, bağlayacağınız türleri de değiştirir.

> [!NOTE]
> Önizleme paketi bir uzantı paketine dahil değildir ve el ile yüklenmelidir. .NET uygulamaları için pakete bir başvuru ekleyin. Diğer tüm uygulama türleri için bkz. [uzantılarınızı güncelleştirme].

[core tools]: ./functions-run-local.md
[Uzantı paketi]: ./functions-bindings-register.md#extension-bundles
[NuGet paketi]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Uzantılarınızı güncelleştirme]: ./functions-bindings-register.md
[Azure Araçları uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>İşlevler 1.x

1. x uygulamalarının [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet paketine, sürüm 2. x 'e başvuran bir başvurusu otomatik olarak vardır.

## <a name="hostjson-settings"></a>Ayarlar üzerinde host.js
<a name="host-json"></a>

Dosyadaki [host.js](../articles/azure-functions/functions-host-json.md#eventhub) , Event Hubs tetikleme davranışını denetleyen ayarları içerir. Yapılandırma, Azure Işlevleri sürümüne bağlı olarak farklılık görür.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]