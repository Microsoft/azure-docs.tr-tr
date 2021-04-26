---
title: 2. xD ve üzeri Işlevler için bağlama Azure Cosmos DB
description: Azure Işlevlerinde Azure Cosmos DB Tetikleyicileri ve bağlamaları nasıl kullanacağınızı anlayın.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: dde3b95c7997a7f742a9d48a964f4275169f6e9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92106935"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-and-higher-overview"></a>Azure Cosmos DB tetikleyicisi ve bağlamaları Azure Işlevleri 2. x ve üzeri genel bakış

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Işlevleri çalışma zamanının sürümünü seçin: "]
> * [Sürüm 1](functions-bindings-cosmosdb.md)
> * [Sürüm 2 ve üzeri](functions-bindings-cosmosdb-v2.md)

Bu makale kümesi, Azure Işlevleri 2. x ve üzeri sürümlerde [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) bağlamalarıyla nasıl çalışılacağını açıklamaktadır. Azure Işlevleri Azure Cosmos DB için tetikleyici, giriş ve çıkış bağlamalarını destekler.

| Eylem | Tür |
|---------|---------|
| Azure Cosmos DB bir belge oluşturulduğunda veya değiştirildiğinde bir işlev çalıştırma | [Tetikleyici](./functions-bindings-cosmosdb-v2-trigger.md) |
| Azure Cosmos DB belge okuma | [Giriş bağlama](./functions-bindings-cosmosdb-v2-input.md) |
| Azure Cosmos DB belgedeki değişiklikleri kaydetme  |[Çıkış bağlama](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Bu başvuru, [Azure işlevleri sürüm 2. x ve üzeri](functions-versions.md)içindir.  Bu bağlamaları 1. x Işlevleri içinde kullanma hakkında daha fazla bilgi için bkz. [Azure işlevleri için Azure Cosmos DB bağlamaları 1. x](functions-bindings-cosmosdb.md).
>
> Bu bağlamanın adı ilk olarak DocumentDB olarak adlandırılmıştır. 2. x ve üzeri Işlevlerde, tetikleyici, bağlamalar ve paketin hepsi Cosmos DB adlandırılır.

## <a name="supported-apis"></a>Desteklenen API'ler

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Işlevler uygulamanıza ekleme

### <a name="functions-2x-and-higher"></a>İşlevler 2.x ve üzeri

Tetikleyici ve bağlamalarla çalışma, uygun pakete başvurmanız gerekir. Diğer tüm uygulama türleri için Uzantı paketi kullanıldığında, .NET sınıf kitaplıkları için NuGet paketi kullanılır.

| Dil                                        | Ekleme ölçütü...                                   | Açıklamalar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet paketi]yükleniyor, sürüm 3. x | |
| C# betiği, Java, JavaScript, Python, PowerShell | [Uzantı paketi] kaydediliyor          | [Azure Araçları uzantısının] Visual Studio Code ile kullanılması önerilir. |
| C# betiği (yalnızca çevrimiçi-Azure portal)         | Bağlama ekleme                            | İşlev uygulamanızı yeniden yayımlamak zorunda kalmadan mevcut bağlama uzantılarını güncelleştirmek için bkz. [uzantılarınızı güncelleştirme]. |

[NuGet paketi]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[Uzantı paketi]: ./functions-bindings-register.md#extension-bundles
[Uzantılarınızı güncelleştirme]: ./functions-bindings-register.md
[Azure Araçları uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>İşlevler 1.x

1. x uygulamalarının [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet paketine, sürüm 2. x 'e başvuran bir başvurusu otomatik olarak vardır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB bir belge oluşturulduğunda veya değiştirildiğinde (tetikleyici) bir işlev çalıştırma](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure Cosmos DB belge okuma (giriş bağlama)](./functions-bindings-cosmosdb-v2-input.md)
- [Azure Cosmos DB belgedeki değişiklikleri kaydetme (çıktı bağlama)](./functions-bindings-cosmosdb-v2-output.md)