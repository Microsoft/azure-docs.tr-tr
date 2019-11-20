---
title: Azure Scheduler 'da sınırlamalar, Kotalar ve eşikler
description: Azure Scheduler için sınırlar, kotalar, varsayılan değerler ve kısıtlama eşikleri hakkında bilgi edinin
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300916"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Azure Scheduler 'da sınırlamalar, Kotalar ve kısıtlama eşikleri

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [devre dışı bırakılmakta](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)olan Azure Scheduler 'ı değiştiriyor. Zamanlayıcı 'da ayarladığınız işlerle çalışmaya devam etmek için lütfen en kısa sürede [Azure Logic Apps geçirin](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

## <a name="limits-quotas-and-thresholds"></a>Sınırlar, Kotalar ve eşikler

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-MS-istek kimliği üst bilgisi

Zamanlayıcı hizmetine yapılan her istek, **x-MS-Request-ID**adlı bir yanıt üst bilgisi döndürür. Bu üstbilgi, isteği benzersiz bir şekilde tanımlayan donuk bir değer içerir. Bu nedenle, bir istek sürekli olarak başarısız olursa ve isteğin düzgün şekilde biçimlendirildiğini onayladıysanız, **x-MS-Request-id** yanıt üst bilgisi değerini sağlayarak ve bu ayrıntıları dahil ederek hatayı Microsoft 'a bildirebilirsiniz: 

* **X-MS-Request-ID** değeri
* İsteğin yapıldığı yaklaşık süre 
* Azure aboneliği, iş koleksiyonu ve iş için tanımlayıcılar 
* İsteğin denendiği işlem türü

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Scheduler nedir?](scheduler-intro.md)
* [Azure Scheduler kavramları, terminolojisi ve varlık hiyerarşisi](scheduler-concepts-terms.md)
