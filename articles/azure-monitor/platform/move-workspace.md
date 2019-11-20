---
title: Azure Izleyici 'de bir Log Analytics çalışma alanını taşıma | Microsoft Docs
description: Log Analytics çalışma alanınızı başka bir aboneliğe veya kaynak grubuna taşımayı öğrenin.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: fd7ff7aa2275defba57aa24b5ef0b9adc78a5355
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093793"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>Log Analytics çalışma alanını farklı bir aboneliğe veya kaynak grubuna taşıyın

Bu makalede, Log Analytics çalışma alanını aynı bölgedeki başka bir kaynak grubuna veya aboneliğe taşıma adımlarını öğreneceksiniz. Azure kaynaklarını Azure portal, PowerShell, Azure CLı veya REST API aracılığıyla taşıma hakkında daha fazla bilgi edinebilirsiniz. konumundaki [kaynakları yeni bir kaynak grubuna veya aboneliğe taşıyın](../../azure-resource-manager/resource-group-move-resources.md). 

> [!IMPORTANT]
> Çalışma alanını farklı bir bölgeye taşıyamazsınız.

## <a name="verify-active-directory-tenant"></a>Active Directory kiracısını doğrula
Çalışma alanı kaynağı ve hedef abonelikler aynı Azure Active Directory kiracısında bulunmalıdır. Her iki aboneliğin de aynı kiracı KIMLIĞINE sahip olduğunu doğrulamak için Azure PowerShell kullanın.

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="remove-solutions"></a>Çözümleri kaldır
Çalışma alanına yüklenen yönetilen çözümler Log Analytics çalışma alanı taşıma işlemiyle birlikte taşınır. Aynı halde bir Otomasyon hesabına olan bağlantıyı kaldırmanız gerektiğinden, o bağlantıyı kullanan çözümlerin kaldırılması gerekir.

Kaldırılması gereken çözümler şunları içerir: 

- Güncelleştirme Yönetimi
- Değişiklik İzleme
- Hizmetin kapalı olduğu saatlerde Sanal Makineleri Başlatma/Durdurma


### <a name="azure-portal"></a>Azure portal
Azure portal kullanarak çözümleri kaldırmak için aşağıdaki yordamı kullanın:

1. Üzerinde çözümlerin yüklendiği kaynak grubunun menüsünü açın.
2. Kaldırılacak çözümleri seçin.
3. **Kaynakları Sil** ' e tıklayın ve ardından **Sil**' e tıklayarak kaldırılacak kaynakları onaylayın.

![Çözümleri silme](media/move-workspace/delete-solutions.png)

### <a name="powershell"></a>PowerShell

PowerShell 'i kullanarak çözümleri kaldırmak için, aşağıdaki örnekte gösterildiği gibi [Remove-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) cmdlet 'ini kullanın:

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

## <a name="remove-alert-rules"></a>Uyarı kurallarını kaldır
**VM 'Leri Başlat/Durdur** çözümü için, çözüm tarafından oluşturulan uyarı kurallarını da kaldırmanız gerekir. Bu kuralları kaldırmak için Azure portal aşağıdaki yordamı kullanın.

1. **İzleyici** menüsünü açın ve ardından **Uyarılar**' ı seçin.
2. **Uyarı kurallarını yönet**' e tıklayın.
3. Aşağıdaki üç uyarı kuralını seçin ve ardından **Sil**' e tıklayın.

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![Kuralları Sil](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>Otomasyon hesabının bağlantısını kaldır
Azure portal kullanarak Otomasyon hesabının çalışma alanından bağlantısını kaldırmak için aşağıdaki yordamı kullanın:

1. **Otomasyon hesapları** menüsünü açın ve kaldırılacak hesabı seçin.
2. Menünün **Ilgili kaynaklar** bölümünde **bağlantılı çalışma alanı**' nı seçin. 
3. Çalışma alanının Otomasyon hesabından bağlantısını kaldırmak için **çalışma alanının bağlantısını kaldır** ' a tıklayın.

    ![Çalışma alanının bağlantısını kaldır](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>Çalışma alanınızı taşıyın

### <a name="azure-portal"></a>Azure portal
Azure portal kullanarak çalışma alanınızı taşımak için aşağıdaki yordamı kullanın:

1. **Log Analytics çalışma alanları** menüsünü açın ve ardından çalışma alanınızı seçin.
2. **Genel bakış** sayfasında, **kaynak grubu** veya **abonelik**' ın yanındaki **Değiştir** ' e tıklayın.
3. Çalışma alanıyla ilgili kaynak listesini içeren yeni bir sayfa açılır. Çalışma alanıyla aynı hedef aboneliğe ve kaynak grubuna taşınacak kaynakları seçin. 
4. Hedef **aboneliği** ve **kaynak grubunu**seçin. Çalışma alanını aynı abonelik içindeki başka bir kaynak grubuna taşıyorsanız, **abonelik** seçeneğini görmezsiniz.
5. Çalışma alanını ve seçili kaynakları taşımak için **Tamam** ' ı tıklatın.

    ![Portal](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
Çalışma alanınızı PowerShell kullanarak taşımak için [Move-AzResource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) öğesini aşağıdaki örnekte olduğu gibi kullanın:

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> Taşıma işleminden sonra, çalışma alanını önceki durumuna geri getirmek için kaldırılan çözümlerin ve Otomasyon hesabı bağlantısının yeniden yapılandırılması gerekir.


## <a name="next-steps"></a>Sonraki adımlar
- Hangi kaynakların taşınmasını desteklediğini bir liste için bkz. [kaynaklar Için taşıma işlemi desteği](../../azure-resource-manager/move-support-resources.md).
