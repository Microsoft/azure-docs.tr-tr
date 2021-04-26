---
author: baanders
description: Azure dijital TWINS için dosya ekleme-örneği yönetme yolları
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 1f66101fc1231be2e5ce36dc348b1ca850113867
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303773"
---
Bu makalede, [Azure Digital TWINS .net (C#) **SDK 'sını**](/dotnet/api/overview/azure/digitaltwins/management)kullanarak farklı yönetim işlemlerinin nasıl tamamlanacağı vurgulanmıştır. Ayrıca, [*nasıl yapılır: Azure Digital TWINS API 'leri ve SDK 'Ları kullanma*](../articles/digital-twins/how-to-use-apis-sdks.md)bölümünde açıklanan diğer dil SDK 'larını kullanarak aynı yönetim çağrılarını da oluşturabilirsiniz.

> [!TIP] 
> Tüm SDK yöntemlerinin zaman uyumlu ve zaman uyumsuz sürümlerde yer aldığını unutmayın. Disk belleği çağrılarında zaman uyumsuz yöntemler, `AsyncPageable<T>` zaman uyumlu sürümler geri dönirken döndürülür `Pageable<T>` .

Diğer bir yönetim seçeneği, bu konu alanı için doğrudan Postman gibi bir REST istemcisi aracılığıyla Azure Digital TWINS [**REST API 'lerini**](/rest/api/azure-digitaltwins/) çağırmmdır. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [*nasıl yapılır: Istekleri Postman Ile oluşturma*](../articles/digital-twins/how-to-use-postman.md).

Son olarak, Azure Digital TWINS **CLI** kullanarak aynı yönetim işlemlerini tamamlayabilirsiniz. CLı kullanma hakkında daha fazla bilgi edinmek için bkz. [*nasıl yapılır: Azure dijital TWINS CLI 'Yi kullanma*](../articles/digital-twins/how-to-use-cli.md).