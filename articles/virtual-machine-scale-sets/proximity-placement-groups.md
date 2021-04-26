---
title: Sanal Makine Ölçek Kümeleri için yakınlık yerleşimi grupları önizlemesi
description: Azure 'da Windows sanal makine ölçek kümeleri için yakınlık yerleşimi grupları oluşturma ve kullanma hakkında bilgi edinin.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: eeb0dd6cd5ce838c0f3a68763c40612038ee82f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933500"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>Önizleme: PowerShell kullanarak yakınlık yerleşimi grupları oluşturma ve kullanma

Olası en düşük gecikme süresini elde etmek üzere VM 'Leri olabildiğince yakın bir şekilde almak için, ölçek kümesini bir [yakınlık yerleşimi grubu](../virtual-machines/co-location.md#proximity-placement-groups)içinde dağıtmanız gerekir.

Yakınlık yerleşimi grubu, Azure işlem kaynaklarının fiziksel olarak birbirlerine yakın bir yerde bulunduğundan emin olmak için kullanılan mantıksal bir gruplandırmadır. Yakınlık yerleşimi grupları, düşük gecikme süresinin gereksinim olduğu iş yükleri için faydalıdır.

> [!IMPORTANT]
> Yakınlık yerleşimi grupları Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Önizleme sırasında yakınlık yerleşimi grupları şu bölgelerde kullanılamıyor: **Japonya Doğu**, **Avustralya Doğu** ve **Hindistan Orta**.


## <a name="create-a-proximity-placement-group"></a>Yakınlık yerleştirme grubu oluşturma
[New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) cmdlet 'ini kullanarak bir yakınlık yerleşimi grubu oluşturun. 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Yakınlık yerleşimi gruplarını Listele

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) cmdlet 'ini kullanarak yakınlık yerleşimi gruplarının tümünü listeleyebilirsiniz.

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Ölçek kümesi oluşturma

`-ProximityPlacementGroup $ppg.Id`Ölçek kümesini oluşturmak Için [New-AzVMSS](/powershell/module/az.compute/new-azvmss) kullandığınızda YAKıNLıK yerleşimi grubu kimliğine başvurmak için kullanarak yakınlık yerleşimi grubunda bir ölçek oluşturun.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Örneği, [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup)kullanarak yerleştirme grubunda görebilirsiniz.

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, yakınlık yerleşimi grupları oluşturmak için [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) 'yi de kullanabilirsiniz.
