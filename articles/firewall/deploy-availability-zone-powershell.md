---
title: Azure güvenlik duvarı Azure PowerShell kullanarak kullanılabilirlik alanları ile dağıtma
description: Bu makalede, bir Azure güvenlik duvarı Azure PowerShell ile kullanılabilirlik alanları ile dağıtmayı öğrenin.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704007"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Bir Azure güvenlik duvarı Azure PowerShell kullanarak kullanılabilirlik alanları ile dağıtma

Azure güvenlik duvarı, yüksek kullanılabilirlik için birden fazla kullanılabilirlik yaymasına izin dağıtımı sırasında yapılandırılabilir.

Bu özellik aşağıdaki senaryolar sağlar:

- % 99,99 çalışma süresi kullanılabilirliğini artırabilirsiniz. Daha fazla bilgi için bkz: Azure Güvenlik Duvarı [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). İki veya daha fazla kullanılabilirlik seçildiğinde % 99,99 çalışma süresi SLA sunulur.
- Azure güvenlik duvarı yalnızca yakınlığı nedeniyle için belirli bir bölgesine hizmeti standart % 99,95 oranında SLA kullanarak da ilişkilendirebilirsiniz.

Azure güvenlik duvarı kullanılabilirlik alanları hakkında daha fazla bilgi için bkz. [Azure güvenlik duvarı nedir?](overview.md)

Aşağıdaki Azure PowerShell örneği, kullanılabilirlik alanları ile bir Azure güvenlik duvarının nasıl dağıtılacağı gösterilmektedir.

## <a name="create-a-firewall-with-availability-zones"></a>Kullanılabilirlik alanları ile güvenlik duvarı oluşturma

Bu örnek, bölge 1, 2 ve 3 bir güvenlik duvarı oluşturur.

Belirli bir bölge yok, standart genel IP adresinin oluşturulduğu sırada belirtilir. Bu, varsayılan olarak bölgesel olarak yedekli bir IP adresi oluşturur. Standart genel IP adresleri, tek bir bölge veya tüm bölgeler yapılandırılabilir.

2 bölgesinde bir Güvenlik Duvarı'nda Bölge 1 ve bir IP adresi bulunamayacağından bilmesi önemlidir. Ancak, yakınlık amacıyla aynı tek bölgesinde bir Güvenlik Duvarı'nda Bölge 1 ve tüm bölgelerde IP adresi veya bir güvenlik duvarı ve bir IP adresi olabilir.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure güvenlik duvarı günlüklerini izleyin](./tutorial-diagnostics.md)
