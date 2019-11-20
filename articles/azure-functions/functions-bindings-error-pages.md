---
title: Azure Işlevleri hata işleme Kılavuzu
description: Azure Işlevlerinde belirli bağlama hatalarının bağlantılarıyla ilgili hataları işleme hakkında bilgi edinin.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155279"
---
# <a name="azure-functions-error-handling"></a>Azure Işlevleri hata işleme

Azure Işlevlerinde hataları işleme, kayıp verilerin, unutulan olayların olmaması ve uygulamanızın durumunu izlemek için önemlidir.

Bu makalede, bağlama özgü hataların bağlantılarıyla birlikte hata işleme için genel stratejiler açıklanmaktadır.

## <a name="handling-errors"></a>Hataları işleme

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Bağlama hata kodları

Azure hizmetleriyle tümleştirilirken, hatalar temeldeki hizmetlerin API 'Lerinden kaynaklanmayabilir. Bağlama özgü hatalarla ilgili bilgiler aşağıdaki makalelerin **özel durumlar ve dönüş kodları** bölümünde bulunabilir:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob depolama](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Kuyruk depolama](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Tablo depolama](functions-bindings-storage-table.md#exceptions-and-return-codes)
