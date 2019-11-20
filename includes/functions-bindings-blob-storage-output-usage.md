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
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642103"
---
Blob 'ları yazmak için aşağıdaki türlere bağlayabilirsiniz:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> , *function. JSON* içinde `direction` veya `FileAccess.Read` bir C# sınıf kitaplığında "ın" bağlamasını gerektirir. Ancak, çalışma zamanının blob 'ları kapsayıcıya yükleme gibi yazma işlemleri için sağladığı kapsayıcı nesnesini kullanabilirsiniz.

<sup>2</sup> , *function. JSON* içinde `FileAccess.ReadWrite` veya `direction` bir C# sınıf kitaplığında "Inout" bağlamayı gerektirir.

Depolama SDK türlerinden birini bağlamaya çalışırsanız ve bir hata iletisi alırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

Zaman uyumsuz işlevleri'nde dönüş değerini kullanın veya `IAsyncCollector` yerine bir `out` parametresi.

`string` Veya`Byte[]` ' a bağlama yalnızca BLOB boyutu küçük olduğunda, Blob içeriğinin tamamı belleğe yüklendiği için önerilir. Genellikle, `Stream` veya `CloudBlockBlob` türünde kullanılması tercih edilir. Daha fazla bilgi için bu makalenin önceki kısımlarında yer alarak [Eşzamanlılık ve bellek kullanımı](#trigger---concurrency-and-memory-usage) bölümüne bakın.