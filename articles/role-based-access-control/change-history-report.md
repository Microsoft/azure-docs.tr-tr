---
title: Azure kaynaklarında RBAC değişiklikleri için etkinlik günlüklerini görüntüleme | Microsoft Docs
description: Son 90 gün boyunca Azure kaynaklarında rol tabanlı erişim denetimi (RBAC) değişiklikleri için etkinlik günlüklerini görüntüleyin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5758f480c9216cf71e47509682053b39f0b15bf
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172410"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Azure kaynaklarında RBAC değişiklikleri için etkinlik günlüklerini görüntüleme

Bazen, denetim veya sorun giderme amacıyla Azure kaynaklarında rol tabanlı erişim denetimi (RBAC) değişiklikleri hakkında bilgilere ihtiyacınız vardır. Herhangi bir kişi, aboneliklerinizde rol atamaları veya rol tanımlarında değişiklik yaptığında, değişiklikler [Azure etkinlik günlüğüne](../azure-monitor/platform/activity-logs-overview.md)kaydedilir. Son 90 güne ait tüm RBAC değişikliklerini görmek için etkinlik günlüklerini görüntüleyebilirsiniz.

## <a name="operations-that-are-logged"></a>Günlüğe kaydedilen işlemler

Aşağıda, etkinlik günlüğüne kaydedilen RBAC ile ilgili işlemler verilmiştir:

- Rol ataması oluştur
- Rol atamasını sil
- Özel rol tanımı oluştur veya güncelleştir
- Özel rol tanımını sil

## <a name="azure-portal"></a>Azure portal

Kullanmaya başlamanın en kolay yolu Azure portal etkinlik günlüklerini görüntüleme yöntemidir. Aşağıdaki ekran görüntüsünde, rol atamasını ve rol tanımı işlemlerini görüntüleyecek şekilde filtrelenmiş bir etkinlik günlüğü örneği gösterilmektedir. Ayrıca, günlükleri CSV dosyası olarak indirmek için bir bağlantı içerir.

![Portalı kullanan etkinlik günlükleri-ekran görüntüsü](./media/change-history-report/activity-log-portal.png)

Portaldaki etkinlik günlüğünde birkaç filtre vardır. RBAC ile ilgili filtreler şunlardır:

|Filtre  |Value  |
|---------|---------|
|Olay kategorisi     | <ul><li>Yönetim</li></ul>         |
|Çalışma     | <ul><li>Rol ataması oluştur</li> <li>Rol atamasını sil</li> <li>Özel rol tanımı oluştur veya güncelleştir</li> <li>Özel rol tanımını sil</li></ul>      |


Etkinlik günlükleri hakkında daha fazla bilgi için bkz. [etkinlik günlüğündeki olayları görüntüleme](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Etkinlik günlüklerini Azure PowerShell görüntülemek için [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) komutunu kullanın.

Bu komut, son yedi güne ait bir abonelikteki tüm rol atama değişikliklerini listeler:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Bu komut, son yedi güne ait bir kaynak grubundaki tüm rol tanımı değişikliklerini listeler:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Bu komut, bir abonelikteki son yedi güne ait tüm rol atamasını ve rol tanımı değişikliklerini listeler ve sonuçları bir listede görüntüler:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Azure CLı ile etkinlik günlüklerini görüntülemek için [az Monitor Activity-Log List](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) komutunu kullanın.

Bu komut, başlangıç zamanından bu yana bir kaynak grubundaki etkinlik günlüklerini listeler:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Bu komut, başlangıç zamanından bu yana yetkilendirme kaynak sağlayıcısı için etkinlik günlüklerini listeler:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri

[Azure izleyici günlükleri](../log-analytics/log-analytics-overview.md) , tüm Azure kaynaklarınızın RBAC değişikliklerini toplamak ve analiz etmek için kullanabileceğiniz başka bir araçtır. Azure Izleyici günlükleri aşağıdaki avantajlara sahiptir:

- Karmaşık sorguları ve mantığı yazma
- Uyarılar, Power BI ve diğer araçlarla tümleştirin
- Verileri daha uzun bekletme süreleri için Kaydet
- Güvenlik, sanal makine ve özel gibi diğer günlüklerle çapraz başvuru

Kullanmaya başlamak için temel adımlar aşağıda verilmiştir:

1. [Log Analytics çalışma alanı oluşturun](../azure-monitor/learn/quick-create-workspace.md).

1. Çalışma alanınızın [etkinlik günlüğü Analizi çözümünü yapılandırın](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

1. [Etkinlik günlüklerini görüntüleyin](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Etkinlik Günlüğü Analizi çözümüne genel bakış sayfasına gitmeniz için hızlı bir yol **Log Analytics** seçeneğine tıklamanız.

   ![Portalda Azure Izleyici günlükleri seçeneği](./media/change-history-report/azure-log-analytics-option.png)

1. İsteğe bağlı olarak, günlükleri sorgulamak ve görüntülemek için [günlük araması](../log-analytics/log-analytics-log-search.md) sayfasını veya [Gelişmiş analiz portalını](../azure-monitor/log-query/get-started-portal.md) kullanın. Bu iki seçenek hakkında daha fazla bilgi için bkz. [günlük arama sayfası veya gelişmiş analiz portalı](../azure-monitor/log-query/portals.md).

Hedef kaynak sağlayıcısına göre düzenlenmiş yeni rol atamaları döndüren bir sorgu aşağıda verilmiştir:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Bir grafikte görüntülenen rol ataması değişikliklerini döndüren bir sorgu aşağıda verilmiştir:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Gelişmiş analiz portalını kullanan etkinlik günlükleri-ekran görüntüsü](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Sonraki adımlar
* [Etkinlik günlüğünde olayları görüntüleme](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Azure Etkinlik Günlüğü ile Abonelik Etkinliğini İzleme](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
