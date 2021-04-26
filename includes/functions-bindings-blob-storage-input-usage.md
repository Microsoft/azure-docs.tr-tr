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
ms.openlocfilehash: 938f55ae0ba911ea3a97cd49e6424bf8aaefdc76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381706"
---
### <a name="default"></a>Varsayılan

Blob giriş bağlaması için aşağıdaki parametre türlerini kullanabilirsiniz:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> `direction` , bir C# sınıf kitaplığındaki veya *üzerindefunction.js* "Inout" bağlamasını gerektirir `FileAccess.ReadWrite` .

Depolama SDK türlerinden birini bağlamaya çalışırsanız ve bir hata iletisi alırsanız, [doğru depolama SDK sürümüne](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

`string`Veya `Byte[]` ' a bağlama yalnızca BLOB boyutu küçük olduğunda, Blob içeriğinin tamamı belleğe yüklendiği için önerilir. Genellikle, veya türünde kullanılması tercih edilir `Stream` `CloudBlockBlob` . Daha fazla bilgi için bu makalenin önceki kısımlarında yer alarak [Eşzamanlılık ve bellek kullanımı](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) bölümüne bakın.

### <a name="additional-types"></a>Ek türler

[Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) kullanan uygulamalar, [.NET için Azure SDK 'sının](/dotnet/api/overview/azure/storage.blobs-readme)türlerini de kullanabilir. Bu sürüm, `CloudBlobContainer` `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` `CloudAppendBlob` aşağıdaki türlerin yerine eski,,,, ve türleri için destek bırakır:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)
- [Blobclient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [Blockblobclient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [Pageblobclient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [Appendblobclient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [Blobbaseclient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> `direction` , bir C# sınıf kitaplığındaki veya *üzerindefunction.js* "Inout" bağlamasını gerektirir `FileAccess.ReadWrite` .

Bu türleri kullanan örnekler için, [uzantının GitHub deposuna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)bakın.
