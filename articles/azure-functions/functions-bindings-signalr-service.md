---
title: Azure Işlevleri SignalR hizmeti bağlamaları
description: Azure Işlevleri ile SignalR hizmeti bağlamalarını nasıl kullanacağınızı anlayın.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 9e60fd9a20720d75f96a0b78ee783bd5509a8f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763498"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure İşlevleri için SignalR Service bağlamaları

Bu makale kümesi, Azure Işlevleri 'nde SignalR hizmeti bağlamaları kullanılarak [Azure SignalR hizmetine](https://azure.microsoft.com/services/signalr-service/) bağlı istemcilere nasıl kimlik doğrulaması yapılacağını ve gerçek zamanlı iletilerin gönderileceğini açıklar. Azure İşlevleri, SignalR Hizmeti’ne yönelik giriş ve çıkış bağlamalarını destekler.

| Eylem | Tür |
|---------|---------|
| SignalR hizmetinden iletileri işle | [Bağlamayı Tetikle](./functions-bindings-signalr-service-trigger.md) |
| Hizmet uç noktası URL 'sini ve erişim belirtecini döndür | [Giriş bağlama](./functions-bindings-signalr-service-input.md) |
| SignalR hizmeti iletilerini gönder |[Çıkış bağlama](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Işlevler uygulamanıza ekleme

### <a name="functions-2x-and-higher"></a>İşlevler 2.x ve üzeri

Tetikleyici ve bağlamalarla çalışma, uygun pakete başvurmanız gerekir. Diğer tüm uygulama türleri için Uzantı paketi kullanıldığında, .NET sınıf kitaplıkları için NuGet paketi kullanılır.

| Dil                                        | Ekleme ölçütü...                                   | Açıklamalar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet paketi]yükleniyor, sürüm 3. x | |
| C# betiği, Java, JavaScript, Python, PowerShell | [Uzantı paketi] kaydediliyor          | [Azure Araçları uzantısının] Visual Studio Code ile kullanılması önerilir. |
| C# betiği (yalnızca çevrimiçi-Azure portal)         | Bağlama ekleme                            | İşlev uygulamanızı yeniden yayımlamak zorunda kalmadan mevcut bağlama uzantılarını güncelleştirmek için bkz. [uzantılarınızı güncelleştirme]. |

[NuGet paketi]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[Uzantı paketi]: ./functions-bindings-register.md#extension-bundles
[Uzantılarınızı güncelleştirme]: ./functions-bindings-register.md
[Azure Araçları uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

SignalR hizmetini ve Azure Işlevlerini birlikte yapılandırma ve kullanma hakkında ayrıntılı bilgi için Azure [SignalR hizmeti Ile Azure işlevleri geliştirme ve yapılandırma](../azure-signalr/signalr-concept-serverless-development-config.md)konusuna bakın.

### <a name="annotations-library-java-only"></a>Ek açıklamalar kitaplığı (yalnızca Java)

Java işlevlerinde SignalR hizmeti ek açıklamalarını kullanmak için, *Azure-Functions-Java-Library-SignalR* yapıtı (sürüm 1,0 veya üzeri) *pom.xml* dosyanıza bir bağımlılık eklemeniz gerekir.

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Sonraki adımlar

- [SignalR hizmetinden iletileri işleme (tetikleyici bağlama)](./functions-bindings-signalr-service-trigger.md)
- [Hizmet uç noktası URL 'sini ve erişim belirtecini (giriş bağlaması) döndürün](./functions-bindings-signalr-service-input.md)
- [SignalR hizmet iletileri gönderme (çıkış bağlama)](./functions-bindings-signalr-service-output.md)