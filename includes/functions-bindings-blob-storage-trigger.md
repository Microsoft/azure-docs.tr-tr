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
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642168"
---
Tetikleme blobu için aşağıdaki parametre türlerini kullanabilirsiniz:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* JSON olarak seri hale getirilebilir bir POCO
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> , *function. JSON* içinde `FileAccess.ReadWrite` veya `direction` bir C# sınıf kitaplığında "Inout" bağlamasını gerektirir.

Depolama SDK türlerinden birini bağlamaya çalışırsanız ve bir hata iletisi alırsanız, [doğru depolama SDK sürümüne](#azure-storage-sdk-version-in-functions-1x)başvurunuz olduğundan emin olun.

`string` ,`Byte[]`Veya poco 'a bağlama yalnızca BLOB boyutu küçük olduğunda önerilir, tüm blob içerikleri belleğe yüklenir. Genellikle, `Stream` veya `CloudBlockBlob` türünde kullanılması tercih edilir. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [Eşzamanlılık ve bellek kullanımı](#trigger---concurrency-and-memory-usage) bölümüne bakın.