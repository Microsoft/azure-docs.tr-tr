---
title: PowerShell ile Azure portal panosu oluşturma
description: Azure PowerShell kullanarak Azure portal Pano oluşturmayı öğrenin.
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 03/25/2021
ms.openlocfilehash: cd001a8259c54f1d86aab5983da1413c8163008c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557454"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Hızlı başlangıç: PowerShell ile Azure portal panosu oluşturma

Azure portal bir Pano, bulut kaynaklarınızın odaklanmış ve düzenlenmiş bir görünümüdür. Bu makalede, bir pano oluşturmak için az. Portal PowerShell modülünü kullanma işlemine odaklanılır.
Pano, bir sanal makinenin (VM) ve bazı statik bilgi ve bağlantıların performansını gösterir.

## <a name="requirements"></a>Gereksinimler

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

PowerShell 'i yerel olarak kullanmayı seçerseniz, bu makale az PowerShell modülünü yüklemenizi ve [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure hesabınıza bağlanmanızı gerektirir. Az PowerShell modülünü yükleme hakkında daha fazla bilgi için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> **Az. Portal** PowerShell modülü önizlemedeyken, cmdlet 'Ini kullanarak az PowerShell modülünden ayrı olarak yüklenmelidir `Install-Module` . Bu PowerShell modülü genel kullanıma sunulduğunda, bu, gelecekteki az PowerShell modülü sürümlerinin bir parçası haline gelir ve Azure Cloud Shell içinden yerel olarak kullanılabilir.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Belirli bir Azure aboneliği seçin

Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Değişkenleri tanımlama

Birkaç bilgi parçasını sürekli olarak kullanacaksınız. Bilgileri depolamak için değişkenler oluşturun.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 'ini kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/management/overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek, değişkeninde belirtilen bölgedeki ada göre bir kaynak grubu oluşturur `$resourceGroupName` `$location` .

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Bu hızlı başlangıç bölümünde oluşturduğunuz Pano, mevcut bir VM gerektirir. Bu adımları izleyerek bir VM oluşturun.

VM için oturum açma kimlik bilgilerini bir değişkende depolayın. Parolanın karmaşık olması gerekir. Bu yeni bir Kullanıcı adı ve paroladır; Örneğin, Azure 'da oturum açmak için kullandığınız hesap değildir. Daha fazla bilgi için bkz. [Kullanıcı adı gereksinimleri](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) ve [Parola gereksinimleri](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

VM 'yi oluşturun.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

VM dağıtımı artık başlar ve genellikle birkaç dakika sürer. Dağıtım tamamlandıktan sonra bir sonraki bölüme geçin.

## <a name="download-the-dashboard-template"></a>Pano şablonunu indirme

Azure panoları kaynak olduğundan, bu değerler JSON olarak temsil edilebilir. Aşağıdaki kod, örnek bir panonun JSON temsilini indirir. Daha fazla bilgi için bkz. [Azure panoları yapısı](./azure-portal-dashboards-structure.md).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$HOME\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Şablonu özelleştirme

Aşağıdaki kodu çalıştırarak indirilen şablonu özelleştirin.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Daha fazla bilgi için bkz. [Microsoft Portal panoları şablon başvurusu](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Pano şablonunu dağıtma

`New-AzPortalDashboard`Şablonu doğrudan PowerShell 'den dağıtmak Için az. Portal modülünün bir parçası olan cmdlet 'ini kullanabilirsiniz.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Dağıtılan kaynakları gözden geçirin

Panonun başarıyla oluşturulduğunu denetleyin.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

VM 'yi ve ilişkili panoyu kaldırmak için, bunları içeren kaynak grubunu silin.

> [!CAUTION]
> Aşağıdaki örnek, belirtilen kaynak grubunu ve içinde yer alan tüm kaynakları siler.
> Bu makalenin kapsamı dışındaki kaynaklar belirtilen kaynak grubunda mevcutsa, bunlar da silinir.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
Remove-Item -Path "$HOME\portal-dashboard-template-testvm.json"
```

## <a name="next-steps"></a>Sonraki adımlar

Az. Portal PowerShell modülünde bulunan cmdlet 'ler hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: Portal panosu cmdlet 'leri](/powershell/module/Az.Portal/)