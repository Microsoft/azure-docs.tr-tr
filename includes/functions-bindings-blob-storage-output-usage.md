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
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100381672"
---
### <a name="default"></a>Varsayılan

Blob 'ları yazmak için aşağıdaki türlere bağlayabilirsiniz:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>iki</sup>
* `CloudBlockBlob`<sup>iki</sup>
* `CloudPageBlob`<sup>iki</sup>
* `CloudAppendBlob`<sup>iki</sup>

<sup>1</sup> `direction` , C# sınıf kitaplığındaki veya *üzerindefunction.js* içinde "ın" bağlamasını gerektirir `FileAccess.Read` . Ancak, çalışma zamanının blob 'ları kapsayıcıya yükleme gibi yazma işlemleri için sağladığı kapsayıcı nesnesini kullanabilirsiniz.

<sup>2</sup> `direction` , bir C# sınıf kitaplığındaki veya *üzerindefunction.js* "Inout" bağlamasını gerektirir `FileAccess.ReadWrite` .

Depolama SDK türlerinden birini bağlamaya çalışırsanız ve bir hata iletisi alırsanız, [doğru depolama SDK sürümüne](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

`string`Veya `Byte[]` ' a bağlama yalnızca BLOB boyutu küçük olduğunda, Blob içeriğinin tamamı belleğe yüklendiği için önerilir. Genellikle, veya türünde kullanılması tercih edilir `Stream` `CloudBlockBlob` . Daha fazla bilgi için bu makalenin önceki kısımlarında yer alarak [Eşzamanlılık ve bellek kullanımı](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) bölümüne bakın.

### <a name="additional-types"></a>Ek türler

[Depolama uzantısının 5.0.0 veya daha yüksek sürümünü](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) kullanan uygulamalar, [.NET için Azure SDK 'sının](/dotnet/api/overview/azure/storage.blobs-readme)türlerini de kullanabilir. Bu sürüm, `CloudBlobContainer` `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` `CloudAppendBlob` aşağıdaki türlerin yerine eski,,,, ve türleri için destek bırakır:

- [Blobcontainerclient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [Blobclient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [Blockblobclient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [Pageblobclient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [Appendblobclient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [Blobbaseclient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> `direction` , C# sınıf kitaplığındaki veya *üzerindefunction.js* içinde "ın" bağlamasını gerektirir `FileAccess.Read` . Ancak, çalışma zamanının blob 'ları kapsayıcıya yükleme gibi yazma işlemleri için sağladığı kapsayıcı nesnesini kullanabilirsiniz.

<sup>2</sup> `direction` , bir C# sınıf kitaplığındaki veya *üzerindefunction.js* "Inout" bağlamasını gerektirir `FileAccess.ReadWrite` .

Bu türleri kullanan örnekler için, [uzantının GitHub deposuna](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)bakın.
