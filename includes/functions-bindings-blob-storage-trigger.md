---
title: include dosyası
description: include dosyası
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 16ab569428510b3b423d6727fd31ee450a8d197e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381622"
---
### <a name="default"></a>Varsayılan

Tetikleme blobu için aşağıdaki parametre türlerini kullanabilirsiniz:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> `direction` , bir C# sınıf kitaplığındaki veya *üzerindefunction.js* "Inout" bağlamasını gerektirir `FileAccess.ReadWrite` .

Depolama SDK türlerinden birini bağlamaya çalışırsanız ve bir hata iletisi alırsanız, [doğru depolama SDK sürümüne](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

`string` `Byte[]` Blob içeriğinin tamamı belleğe yüklendiği için, veya öğesine bağlama yalnızca BLOB boyutu küçük olduğunda önerilir. Genellikle, veya türünde kullanılması tercih edilir `Stream` `CloudBlockBlob` . Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [Eşzamanlılık ve bellek kullanımı](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) bölümüne bakın.

### <a name="additional-types"></a>Ek türler

[Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) kullanan uygulamalar, [.NET için Azure SDK 'sının](/dotnet/api/overview/azure/storage.blobs-readme)türlerini de kullanabilir. Bu sürüm, `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` aşağıdaki türlerin yerine eski,, ve `CloudAppendBlob` türleri için desteği bırakır:

- [Blobclient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [Blockblobclient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [Pageblobclient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [Appendblobclient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [Blobbaseclient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> `direction` , bir C# sınıf kitaplığındaki veya *üzerindefunction.js* "Inout" bağlamasını gerektirir `FileAccess.ReadWrite` .

Bu türleri kullanan örnekler için, [uzantının GitHub deposuna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)bakın.
