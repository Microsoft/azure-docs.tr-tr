---
title: Azure Işlevleri için Azure Blob depolama tetikleyicisi ve bağlamaları
description: Azure Işlevleri 'nde Azure Blob depolama tetikleyiciyi ve bağlamalarını kullanmayı öğrenin.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381537"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Işlevlerine yönelik Azure Blob depolama bağlamaları genel bakış

Azure Işlevleri [, Tetikleyiciler ve bağlamalar](./functions-triggers-bindings.md)aracılığıyla [Azure depolama](../storage/index.yml) ile tümleşir. BLOB depolama ile tümleştirme, blob verilerinde değişikliklere tepki veren işlevler ve okuma ve yazma değerlerini oluşturmanızı sağlar.

| Eylem | Tür |
|---------|---------|
| BLOB depolama veri değişiklikleri olarak bir işlev çalıştırma | [Tetikleyici](./functions-bindings-storage-blob-trigger.md) |
| Bir işlevde BLOB depolama verilerini okuma | [Giriş bağlama](./functions-bindings-storage-blob-input.md) |
| Bir işlevin BLOB depolama verilerini yazmasına izin ver |[Çıkış bağlama](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Işlevler uygulamanıza ekleme

### <a name="functions-2x-and-higher"></a>İşlevler 2.x ve üzeri

Tetikleyici ve bağlamalarla çalışma, uygun pakete başvurmanız gerekir. Diğer tüm uygulama türleri için Uzantı paketi kullanıldığında, .NET sınıf kitaplıkları için NuGet paketi kullanılır.

| Dil                                        | Ekleme ölçütü...                                   | Açıklamalar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet paketi]yükleniyor, sürüm 3. x | |
| C# betiği, Java, JavaScript, Python, PowerShell | [Uzantı paketi] kaydediliyor          | [Azure Araçları uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Visual Studio Code ile kullanılması önerilir. |
| C# betiği (yalnızca çevrimiçi-Azure portal)         | Bağlama ekleme                            | İşlev uygulamanızı yeniden yayımlamak zorunda kalmadan mevcut bağlama uzantılarını güncelleştirmek için bkz. [uzantılarınızı güncelleştirme]. |

#### <a name="storage-extension-5x-and-higher"></a>Depolama uzantısı 5. x ve üzeri

Depolama bağlamaları uzantısının yeni bir sürümü [Önizleme NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)olarak kullanılabilir. Bu önizleme, [gizli anahtar yerine bir kimlik kullanarak bağlanma](./functions-reference.md#configure-an-identity-based-connection)özelliği sunar. .NET uygulamaları için, bu, ' den `WindowsAzure.Storage` ve türlerini `Microsoft.Azure.Storage` [Azure. Storage. blob](/dotnet/api/azure.storage.blobs)'larındaki daha yeni türlerle değiştirerek, ' a bağlayacağınız türleri de değiştirir.

> [!NOTE]
> Önizleme paketi bir uzantı paketine dahil değildir ve el ile yüklenmelidir. .NET uygulamaları için pakete bir başvuru ekleyin. Diğer tüm uygulama türleri için bkz. [uzantılarınızı güncelleştirme].

[core tools]: ./functions-run-local.md
[Uzantı paketi]: ./functions-bindings-register.md#extension-bundles
[NuGet paketi]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Uzantılarınızı güncelleştirme]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>İşlevler 1.x

1. x uygulamalarının [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet paketine, sürüm 2. x 'e başvuran bir başvurusu otomatik olarak vardır.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>Ayarlar üzerinde host.js

> [!NOTE]
> Bu bölüm, 5.0.0 'den önceki uzantı sürümleri kullanılırken uygulanmaz. Bu sürümler için, Bloblar için genel yapılandırma ayarları yoktur.

Bu bölümde, [5.0.0 ve üzeri uzantı sürümü](#storage-extension-5x-and-higher)kullanılırken bu bağlama için kullanılabilen genel yapılandırma ayarları açıklanmaktadır. Aşağıdaki dosyada örnek *host.js* , bu bağlamanın yalnızca sürüm 2. x + ayarlarını içerir. Işlevler 2. x ve daha ötesi sürümlerindeki genel yapılandırma ayarları hakkında daha fazla bilgi için bkz. [ Azure işlevleri için başvuruhost.js](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------|
|Maxanalyticsunits|8 * (kullanılabilir çekirdek sayısı)|Blob ile tetiklenen her işlev için izin verilen eşzamanlı etkinleştirmeleri tamsayı sayısı. İzin verilen en düşük değer 1 ' dir.|

## <a name="next-steps"></a>Sonraki adımlar

- [BLOB depolama verileri değiştiğinde bir işlev Çalıştır](./functions-bindings-storage-blob-trigger.md)
- [Bir işlev çalıştığında BLOB depolama verilerini okuma](./functions-bindings-storage-blob-input.md)
- [Bir işlevden BLOB depolama verileri yazma](./functions-bindings-storage-blob-output.md)
