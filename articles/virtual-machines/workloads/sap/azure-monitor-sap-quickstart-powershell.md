---
title: Azure PowerShell ile SAP Çözümleri için Azure Izleyici dağıtma
description: Azure PowerShell ile SAP Çözümleri için Azure Izleyici dağıtma
author: sameeksha91
ms.author: sakhare
ms.date: 09/08/2020
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0af2611bada7a9aad206ce7463ef72ec930c89a2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538692"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell ile SAP Çözümleri için Azure İzleyici'yi dağıtma

Bu makalede, [az. HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell modülünü kullanarak SAP Çözümleri Için Azure izleyici kaynaklarını nasıl oluşturabileceğiniz açıklanmaktadır.

> [!CAUTION]
> SAP Çözümleri için Azure Izleyici Şu anda genel önizleme aşamasındadır. Bu önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır. Üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Gereksinimler

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps). Cloud Shell kullanmayı seçerseniz, daha fazla bilgi için bkz. [Azure Cloud Shell Genel Bakış](../../../cloud-shell/overview.md) .

> [!IMPORTANT]
> **Az. HanaOnAzure** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir [Azure Kaynak grubu](../../../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, belirtilen ada ve belirtilen konuma sahip bir kaynak grubu oluşturur.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP İzleyicisi

SAP izleyici oluşturmak için [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen abonelik, kaynak grubu ve kaynak adı için bir SAP İzleyicisi oluşturur.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

SAP izleyicisinin özelliklerini almak için [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen abonelik, kaynak grubu ve kaynak adı için bir SAP izleyicisinin özelliklerini alır.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Sağlayıcı örneği

Bir sağlayıcı örneği oluşturmak için [New-Azsapmonitorproviderınstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen abonelik, kaynak grubu ve kaynak adı için bir sağlayıcı örneği oluşturur.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Bir sağlayıcı örneğinin özelliklerini almak için [Get-Azsapmonitorproviderınstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen abonelik, kaynak grubu, Sapizleyici adı ve kaynak adı için bir sağlayıcı örneğinin özelliklerini alır.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynaklar gerekmiyorsa, aşağıdaki örnekleri çalıştırarak bunları silebilirsiniz.

### <a name="delete-the-provider-instance"></a>Sağlayıcı örneğini Sil

Bir sağlayıcı örneğini kaldırmak için [Remove-Azsapmonitorproviderınstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen abonelik, kaynak grubu, Sapizleyici adı ve kaynak adı için bir sağlayıcı örneği siler.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>SAP izleyicisini silme

Bir SAP izleyicisini kaldırmak için [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) cmdlet 'ini kullanırsınız. Aşağıdaki örnek, belirtilen abonelik, kaynak grubu ve izleyici adı için bir SAP izleyicisini siler.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Kaynak grubunu silme

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu makalenin kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[SAP Çözümleri Için Azure izleyici](azure-monitor-overview.md)hakkında daha fazla bilgi edinin.
