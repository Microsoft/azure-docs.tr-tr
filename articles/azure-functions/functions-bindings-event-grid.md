---
title: Azure Işlevleri için Azure Event Grid bağlamaları
description: Azure Işlevlerinde Event Grid olaylarının nasıl işleneceğini anlayın.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104555"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Işlevleri için Azure Event Grid bağlamaları

Bu başvuru, Azure Işlevlerinde [Event Grid](../event-grid/overview.md) olaylarının nasıl işleneceğini açıklar. HTTP uç noktasındaki Event Grid iletilerinin nasıl işleneceği hakkında ayrıntılı bilgi için bkz. [http uç noktasına olay alma](../event-grid/receive-events.md).

Event Grid, *yayımcılardan* oluşan olayları BILDIRMEK üzere http istekleri gönderen bir Azure hizmetidir. Yayımcı, olayı oluşturan hizmet veya kaynaktır. Örneğin, bir Azure Blob depolama hesabı bir yayımcı, [BLOB karşıya yükleme veya silme bir olaydır](../storage/blobs/storage-blob-event-overview.md). Bazı [Azure hizmetlerinde Event Grid olay yayımlamaya yönelik yerleşik destek vardır](../event-grid/overview.md#event-sources).

Olay *işleyicileri* olayları alır ve işler. Azure Işlevleri, [Event Grid olaylarını işlemek için yerleşik desteğe sahip çeşitli Azure hizmetlerinden](../event-grid/overview.md#event-handlers)biridir. Bu başvuruda, Event Grid bir olay alındığında bir işlevi çağırmak ve bir [Event Grid özel konusuna](../event-grid/post-to-custom-topic.md)olay göndermek için çıkış bağlamayı kullanmak üzere bir Event Grid tetikleyicisi kullanmayı öğreneceksiniz.

İsterseniz, Event Grid olaylarını işlemek için bir HTTP tetikleyicisi kullanabilirsiniz; bkz. [http uç noktasına olay alma](../event-grid/receive-events.md). Şu anda, olay [Cloudevents şemasında](../event-grid/cloudevents-schema.md#azure-functions)teslim edildiğinde Azure işlevleri uygulaması için Event Grid tetikleyicisi kullanamazsınız. Bunun yerine, bir HTTP tetikleyicisi kullanın.

| Eylem | Tür |
|---------|---------|
| Event Grid bir olay dağıtılan bir işlevi Çalıştır | [Tetikleyici](./functions-bindings-event-grid-trigger.md) |
| Event Grid olayı gönderir |[Çıkış bağlama](./functions-bindings-event-grid-output.md) |

Bu başvurudaki kod, sürüm 2. x ve üzeri Işlevlerde kullanılan .NET Core söz dizimine varsayılan olarak sahiptir. 1. x sözdizimi hakkında daha fazla bilgi için, bkz. [1. x işlevleri şablonları](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Işlevler uygulamanıza ekleme

### <a name="functions-2x-and-higher"></a>İşlevler 2.x ve üzeri

Tetikleyici ve bağlamalarla çalışma, uygun pakete başvurmanız gerekir. Diğer tüm uygulama türleri için Uzantı paketi kullanıldığında, .NET sınıf kitaplıkları için NuGet paketi kullanılır.

| Dil                                        | Ekleme ölçütü...                                   | Açıklamalar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet paketi]yükleniyor, sürüm 2. x | |
| C# betiği, Java, JavaScript, Python, PowerShell | [Uzantı paketi] kaydediliyor          | [Azure Araçları uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Visual Studio Code ile kullanılması önerilir. |
| C# betiği (yalnızca çevrimiçi-Azure portal)         | Bağlama ekleme                            | İşlev uygulamanızı yeniden yayımlamak zorunda kalmadan mevcut bağlama uzantılarını güncelleştirmek için bkz. [uzantılarınızı güncelleştirme]. |

[core tools]: ./functions-run-local.md
[Uzantı paketi]: ./functions-bindings-register.md#extension-bundles
[NuGet paketi]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Uzantılarınızı güncelleştirme]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>İşlevler 1.x

1. x uygulamalarının [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet paketine, sürüm 2. x 'e başvuran bir başvurusu otomatik olarak vardır.

## <a name="next-steps"></a>Sonraki adımlar
* [Event Grid bir olay dağıtılan bir işlevi Çalıştır](./functions-bindings-event-grid-trigger.md)
* [Event Grid olayı gönderme](./functions-bindings-event-grid-trigger.md)