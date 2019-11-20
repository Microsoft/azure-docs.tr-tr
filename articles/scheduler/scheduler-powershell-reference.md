---
title: PowerShell cmdlet 'leri başvurusu-Azure Zamanlayıcı
description: Azure Scheduler için PowerShell cmdlet 'leri hakkında bilgi edinin
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 1ad1d6b9f59fa51b9e27fe5b70ce4e2a5d36f3ad
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300888"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Azure Scheduler için PowerShell cmdlet 'leri başvurusu

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [devre dışı bırakılmakta](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)olan Azure Scheduler 'ı değiştiriyor. Zamanlayıcı 'da ayarladığınız işlerle çalışmaya devam etmek için lütfen en kısa sürede [Azure Logic Apps geçirin](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zamanlayıcı işleri ve iş koleksiyonları oluşturmak ve yönetmek için betikler yazmak üzere PowerShell cmdlet 'lerini kullanabilirsiniz. Bu makalede, Azure Scheduler için başvuru makaleleriyle bağlantılı ana PowerShell cmdlet 'leri listelenir. Azure aboneliğiniz için Azure PowerShell yüklemek için bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/overview). [Azure Resource Manager cmdlet 'leri](/powershell/azure/overview)hakkında daha fazla bilgi için bkz. [Azure Resource Manager ile Azure PowerShell kullanma](../powershell-azure-resource-manager.md).

| Cmdlet | Açıklama |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Bir iş koleksiyonunu devre dışı bırakır. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Bir iş koleksiyonunu etkinleştirilir. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Zamanlayıcı işlerini alır. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |İş koleksiyonlarını alır. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |İş geçmişini alır. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Bir HTTP işi oluşturur. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Bir iş koleksiyonu oluşturur. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Service Bus kuyruğu işi oluşturur. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Service Bus konu işi oluşturur. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Bir depolama kuyruğu işi oluşturur. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Bir zamanlayıcı işini kaldırır. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Bir iş koleksiyonunu kaldırır. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Bir Zamanlayıcı HTTP işini değiştirir. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Bir iş koleksiyonunu değiştirir. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Service Bus kuyruğu işini değiştirir. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Service Bus konu işini değiştirir. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Bir depolama kuyruğu işini değiştirir. |
||| 

Daha fazla ayrıntı için şu cmdlet 'lerden herhangi birini çalıştırabilirsiniz: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Scheduler nedir?](scheduler-intro.md)
* [Kavramlar, terminoloji ve varlık hiyerarşisi](scheduler-concepts-terms.md)
* [İlk işinizi oluşturma ve zamanlama-Azure portal](scheduler-get-started-portal.md)
* [Azure Scheduler REST API başvurusu](https://msdn.microsoft.com/library/mt629143)
