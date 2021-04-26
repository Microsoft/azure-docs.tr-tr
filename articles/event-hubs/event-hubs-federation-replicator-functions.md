---
title: Olay çoğaltma görevleri ve uygulamalar-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Azure Işlevleri ile olay çoğaltma görevlerinin ve uygulamaların oluşturulmasına ilişkin bir genel bakış sunulmaktadır
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: a65815c7da400af8b5b6d46358e6bca6edbd7543
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97663701"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Azure Işlevleri ile olay çoğaltma görevleri ve uygulamaları

> [!TIP]
> Olaylarınızın yüklerini göz önünde bulundurmanız, toplamanız, zenginleştirmeniz veya azaltmanız gereken durum bilgisi olan tüm çoğaltma görevleri için Azure Işlevleri yerine [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) kullanın.

[Olay çoğaltma ve çapraz bölge Federasyonu](event-hubs-federation-overview.md) makalesinde açıklandığı gibi, Azure işlevleri üzerinde Event Hubs çiftleri arasında ve Event Hubs ile diğer olay akışı kaynakları ve hedefleri arasında olay akışlarının durum bilgisiz yinelemesi.

[Azure işlevleri](../azure-functions/functions-overview.md) , olay çoğaltma ve Federasyon görevleri dahil olmak üzere sunucusuz uygulamalar yapılandırmaya ve çalıştırmaya yönelik ölçeklenebilir ve güvenilir bir yürütme ortamıdır.

Bu genel bakışta, bu tür uygulamalar için Azure Işlevleri 'nin yerleşik özellikleri hakkında bilgi edineceksiniz. dönüştürme görevleri için uyarlayabileceğiniz ve değiştirebileceğiniz kod blokları hakkında ve Azure Işlevleri uygulamasını Event Hubs ve diğer Azure mesajlaşma hizmetleri ile ideal şekilde tümleştirerek nasıl yapılandırılacağı hakkında bilgi edineceksiniz. Birçok ayrıntı için, bu makale Azure Işlevleri belgelerini işaret edecektir.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









