---
title: İleti çoğaltma görevleri ve uygulamalar-Azure Service Bus | Microsoft Docs
description: Bu makalede, Azure Işlevleri ile ileti çoğaltma görevleri ve uygulamalar oluşturmaya genel bakış sunulmaktadır.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 4db151f54a2ad236ba937b005ba6a1fd3edd5967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97657593"
---
# <a name="message-replication-tasks-and-applications"></a>İleti çoğaltma görevleri ve uygulamaları

[İleti çoğaltma ve bölgeler arası Federasyon](service-bus-federation-overview.md) makalesinde açıklandığı gibi, Service Bus varlık çiftleri arasında ve Service Bus ile diğer ileti kaynakları ve hedefler arasındaki ileti sıralarının çoğaltması Azure işlevleri üzerinde genel olarak çalışır.

[Azure işlevleri](../azure-functions/functions-overview.md) , [ileti çoğaltma ve Federasyon](service-bus-federation-overview.md) görevleri dahil olmak üzere sunucusuz uygulamalar yapılandırmaya ve çalıştırmaya yönelik ölçeklenebilir ve güvenilir bir yürütme ortamıdır.

Bu genel bakışta, bu tür uygulamalar için Azure Işlevleri 'nin yerleşik özellikleri hakkında bilgi edineceksiniz. dönüştürme görevleri için uyarlayabileceğiniz ve değiştirebileceğiniz kod blokları hakkında ve Azure Işlevleri uygulamasını Service Bus ve diğer Azure mesajlaşma hizmetleri ile ideal şekilde tümleştirerek nasıl yapılandırılacağı hakkında bilgi edineceksiniz. Birçok ayrıntı için, bu makale Azure Işlevleri belgelerini işaret edecektir.

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
