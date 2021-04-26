---
title: Azure Işlevlerine genel bakış için Azure kuyruk depolama tetikleyicisi ve bağlamaları
description: Azure 'da Azure kuyruk depolama tetikleyicisi ve çıkış bağlamayı nasıl kullanacağınızı anlayın.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381486"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Işlevlerine genel bakış için Azure kuyruk depolama tetikleyicisi ve bağlamaları

Azure Işlevleri yeni Azure kuyruğu depolama iletileri oluşturulduğu ve bir işlev içinde sıra iletilerini yazacağı için çalıştırılabilir.

| Eylem | Tür |
|---------|---------|
| Bir işlevi kuyruk depolama veri değişiklikleri olarak çalıştırma | [Tetikleyici](./functions-bindings-storage-queue-trigger.md) |
| Sıra depolama iletilerini yaz |[Çıkış bağlama](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Işlevler uygulamanıza ekleme

### <a name="functions-2x-and-higher"></a>İşlevler 2.x ve üzeri

Tetikleyici ve bağlamalarla çalışma, uygun pakete başvurmanız gerekir. Diğer tüm uygulama türleri için Uzantı paketi kullanıldığında, .NET sınıf kitaplıkları için NuGet paketi kullanılır.

| Dil                                        | Ekleme ölçütü...                                   | Açıklamalar 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet paketi]yükleniyor, sürüm 3. x | |
| C# betiği, Java, JavaScript, Python, PowerShell | [Uzantı paketi] kaydediliyor          | [Azure Araçları uzantısının](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) Visual Studio Code ile kullanılması önerilir. |
| C# betiği (yalnızca çevrimiçi-Azure portal)         | Bağlama ekleme                            | İşlev uygulamanızı yeniden yayımlamak zorunda kalmadan mevcut bağlama uzantılarını güncelleştirmek için bkz. [uzantılarınızı güncelleştirme]. |

#### <a name="storage-extension-5x-and-higher"></a>Depolama uzantısı 5. x ve üzeri

Depolama bağlamaları uzantısının yeni bir sürümü [Önizleme NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)olarak kullanılabilir. Bu önizleme, [gizli anahtar yerine bir kimlik kullanarak bağlanma](./functions-reference.md#configure-an-identity-based-connection)özelliği sunar. .NET uygulamaları için, bu, Ayrıca, ' den `WindowsAzure.Storage` ve türlerini `Microsoft.Azure.Storage` [Azure. Storage. Queues](/dotnet/api/azure.storage.queues)' dan daha yeni türlerle değiştirerek, bağlayacağınız türleri de değiştirir.

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

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ayarlar üzerinde host.js

Bu bölümde, 2. x ve üzeri sürümlerde bu bağlama için kullanılabilen genel yapılandırma ayarları açıklanmaktadır. Aşağıdaki dosyada örnek *host.js* , bu bağlamanın yalnızca sürüm 2. x + ayarlarını içerir. 2. x ve daha ötesi sürümlerindeki genel yapılandırma ayarları hakkında daha fazla bilgi için bkz. [ Azure işlevleri için başvuruhost.js](functions-host-json.md).

> [!NOTE]
> 1. x Işlevleri içindeki host.jsbaşvurusu için bkz. [ Azure işlevleri için başvuru üzerindehost.js, 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Özellik  |Varsayılan | Description |
|---------|---------|---------|
|Maxpollingınterval|00:00:01|Sıra yoklamaları arasındaki en uzun Aralık. En az 00:00:00.100 (100 ms) ve 00:01:00 (1 dak) artar.  2. x ve üzeri Işlevlerde veri türü bir `TimeSpan` , 1. x sürümünde ise milisaniyedir.|
|visibilityTimeout|00:00:00|Bir ileti işlenirken yeniden denemeler arasındaki zaman aralığı başarısız olur. |
|batchSize|16|Işlevlerin çalışma zamanının aynı anda ve işlemleri paralel olarak aldığı sıra iletilerinin sayısı. İşlenen sayı öğesine doğru aldığında, `newBatchThreshold` çalışma zamanı başka bir Batch alır ve bu iletileri işlemeye başlar. Bu nedenle, işlev başına işlenen en fazla eşzamanlı ileti sayısı `batchSize` artı olur `newBatchThreshold` . Bu sınır, kuyruğa tetiklenen her bir işlev için ayrı olarak uygulanır. <br><br>Bir kuyrukta alınan iletiler için paralel yürütmeyi önlemek istiyorsanız, `batchSize` 1 olarak ayarlayabilirsiniz. Ancak, işlev uygulamanız yalnızca tek bir sanal makinede (VM) çalıştığı sürece bu ayar eşzamanlılık ortadan kaldırır. İşlev uygulaması birden çok VM 'ye ölçekleniyorsa, her VM, her bir kuyruk tetiklenen işlevin bir örneğini çalıştırabilir.<br><br>Maksimum `batchSize` değer 32 ' dir. |
|maxDequeueCount|5|Zarar sırasına taşımadan önce bir iletiyi işlemeyi deneme sayısı.|
|newBatchThreshold|batchSize/2|Aynı anda işlenen ileti sayısı bu sayıya indiğinde, çalışma zamanı başka bir toplu işi alır.|
|messageEncoding|base64| Bu ayar yalnızca [5.0.0 ve üzeri uzantı sürümlerinde](#storage-extension-5x-and-higher)kullanılabilir. İletiler için kodlama biçimini temsil eder. Geçerli değerler `base64` ve ' dir `none` .|

## <a name="next-steps"></a>Sonraki adımlar

- [Bir işlevi kuyruk depolama veri değişiklikleri (tetikleyici) olarak çalıştırma](./functions-bindings-storage-queue-trigger.md)
- [Yazma kuyruğu depolama iletileri (çıkış bağlaması)](./functions-bindings-storage-queue-output.md)
