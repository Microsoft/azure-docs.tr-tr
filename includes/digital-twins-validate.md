---
author: baanders
description: Azure dijital TWINS modellerinin nasıl doğrulandığını açıklayan dosyayı Ekle
ms.service: digital-twins
ms.topic: include
ms.date: 8/7/2020
ms.author: baanders
ms.openlocfilehash: 32c02750f99f1aa6733d9c6dd673ffe4964978f4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303774"
---
> [!TIP]
> Bir model oluşturduktan sonra, Azure dijital TWINS örneğinizi karşıya yüklemeden önce modellerinizi çevrimdışı doğrulamanız önerilir.

DTDL 'nin örneğinizi örnekize yüklemeden önce doğru olduğundan emin olmak için model belgelerini doğrulamak üzere dil açısından belirsiz bir örnek mevcuttur. Burada bulunur: [**Dtdl Doğrulayıcı örneği**](/samples/azure-samples/dtdl-validator/dtdl-validator).

DTDL doğrulayıcısı örneği, NuGet üzerinde bir istemci tarafı kitaplığı olarak kullanılabilen bir .NET DTDL ayrıştırıcısı kitaplığı üzerine kurulmuştur: [**Microsoft. Azure. DigitalTwins. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). Kitaplığı doğrudan kendi doğrulama çözümünüzü tasarlamak için de kullanabilirsiniz. Ayrıştırıcı kitaplığını kullanırken, Azure Digital TWINS 'in çalıştığı sürümle uyumlu bir sürüm kullandığınızdan emin olun. Şu anda bu sürüm *3.12.4*.

Kullanım örnekleri de dahil olmak üzere Doğrulayıcı örneği ve ayrıştırıcı Kitaplığı hakkında daha fazla bilgi edinebilirsiniz; [*nasıl yapılır: ayrıştırma ve doğrulama modelleri*](../articles/digital-twins/how-to-parse-models.md).