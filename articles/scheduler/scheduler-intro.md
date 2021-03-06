---
title: Azure Scheduler nedir?
description: Zamanlama oluşturma ve Azure içindeki veya dışındaki Hizmetleri çağıran otomatikleştirilmiş işleri çalıştırma
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 66ec285554299214122a4093837d3506bf642b13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368137"
---
# <a name="what-is-azure-scheduler"></a>Azure Scheduler nedir?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [devre dışı bırakılmakta](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)olan Azure Scheduler 'ı değiştiriyor. Zamanlayıcı 'da ayarladığınız işlerle çalışmaya devam etmek için lütfen en kısa sürede [Azure Logic Apps geçirin](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Zamanlayıcı artık Azure portal kullanılamıyor, ancak iş ve iş koleksiyonlarınızı yönetebilmeniz için [REST API](/rest/api/scheduler) ve [Azure Scheduler PowerShell cmdlet 'leri](scheduler-powershell-reference.md) Şu anda kullanılabilir durumda kalır.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/), eylemleri açık bir şekilde bildirerek bulutta çalışan [işler](../scheduler/scheduler-concepts-terms.md) oluşturmanıza yardımcı olur. Hizmet bundan sonra bu eylemleri otomatik olarak zamanlar ve çalıştırır. Örneğin, Azure'un içinden ve dışında hizmetleri çağırabilir (HTTP veya HTTPS uç noktalarını çağırmak gibi), ayrıca Azure Depolama kuyruklarıyla Azure Service Bus kuyruklarına veya konularına iletiler gönderebilirsiniz. İşleri hemen çalıştırabileceğiniz gibi daha sonra da çalıştırabilirsiniz. Scheduler kolayca [karmaşık zamanlamalar ve gelişmiş yineleme oluşturmayı](../scheduler/scheduler-advanced-complexity.md) destekler. Scheduler işlerin ne zaman çalıştırılacağını belirtir, gözden geçirebilmeniz için iş sonuçlarının geçmişini tutar ve ardından iş yüklerini çalıştırmak için öngörülebilir ve güvenilir zamanlamalar yapar.

Diğer Azure zamanlama özellikleri, örneğin Azure App Service'te bir [Web Apps](https://azure.microsoft.com/services/app-service/web/) özelliği olan [Azure Web İşleri](../app-service/webjobs-create.md) de arka planda Scheduler'ı kullanır. Bu eylemlerin iletişimini, bu eylemlerin iletişimini yönetmenize yardımcı olan [Scheduler REST API](/rest/api/scheduler/)kullanarak yönetebilirsiniz.

Burada Scheduler'ın size yardımcı olabileceği bazı senaryolar verilmiştir:

* Yinelenen uygulama eylemlerini çalıştırma: Örneğin, Twitter'dan düzenli aralıklarla bir akışa veri toplama.

* Günlük bakım yapma: Günlüklerin günlük ayıklanması, yedeklemelerin ve diğer bakım görevlerinin gerçekleştirilmesi.

  Örneğin, yönetici olarak önümüzdeki dokuz ay boyunca veritabanınızı her gün saat 1:00'da yedeklemek isteyebilirsiniz.

Scheduler'ı kullanarak zamanlanmış iş yüklerini oluşturabilir, koruyabilir ve çalıştırabilirsiniz ama Scheduler iş yüklerini barındırmaz veya kod çalıştırmaz. Hizmet yalnızca başka bir yerde (örneğin Azure’da, şirket içinde veya başka sağlayıcıda) barındırılan hizmetleri veya kodu *çağırır*. Scheduler HTTP, HTTPS, Depolama kuyruğu, Service Bus kuyruğu veya Service Bus konusu aracılığıyla çağırabilir.

İşleri ve [iş koleksiyonlarını](../scheduler/scheduler-concepts-terms.md)oluşturmak, zamanlamak, yönetmek, güncelleştirmek veya silmek için kod, [Zamanlayıcı REST API](/rest/api/scheduler/)veya [Azure Zamanlayıcı PowerShell cmdlet 'lerini](scheduler-powershell-reference.md)kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Scheduler kavramları, terminolojisi ve varlık hiyerarşisi](scheduler-concepts-terms.md)
* [Azure Scheduler için planlar ve faturalandırma](scheduler-plans-billing.md)
* [Azure Scheduler ile karmaşık zamanlamalar ve gelişmiş yinelenme oluşturma](scheduler-advanced-complexity.md)
* [Azure Scheduler REST API başvurusu](/rest/api/scheduler)
* [Azure Scheduler PowerShell cmdlet’leri başvurusu](scheduler-powershell-reference.md)