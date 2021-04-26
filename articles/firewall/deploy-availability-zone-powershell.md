---
title: PowerShell kullanarak Kullanılabilirlik Alanları Azure Güvenlik duvarını dağıtma
description: Bu makalede, Azure PowerShell kullanarak Kullanılabilirlik Alanları bir Azure Güvenlik Duvarı dağıtmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 46ecc4754a064e26e61365ed6ca167606bef9d81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94656114"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Azure PowerShell kullanarak Kullanılabilirlik Alanlarıyla Azure Firewall'u dağıtma

Azure Güvenlik Duvarı, daha fazla kullanılabilirlik için dağıtım sırasında birden fazla Kullanılabilirlik Alanları yayılarak yapılandırılabilir.

Bu özellik aşağıdaki senaryolara izin vermez:

- Kullanılabilirliği% 99,99 çalışma süresi olarak artırabilirsiniz. Daha fazla bilgi için bkz. Azure Güvenlik Duvarı [hizmet düzeyi sözleşmesi (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). İki veya daha fazla Kullanılabilirlik Alanları seçildiğinde% 99,99 çalışma süresi SLA 'Sı sunulur.
- Ayrıca, Azure Güvenlik duvarını belirli bir bölgeye yalnızca yakınlık nedenleriyle, hizmet standardı% 99,95 SLA ile ilişkilendirebilirsiniz.

Azure Güvenlik Duvarı Kullanılabilirlik Alanları hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı nedir?](overview.md)

Aşağıdaki Azure PowerShell örnek, Kullanılabilirlik Alanları ile Azure Güvenlik duvarını nasıl dağıtacağınızı gösterir.

## <a name="create-a-firewall-with-availability-zones"></a>Kullanılabilirlik Alanları bir güvenlik duvarı oluşturma

Bu örnek, 1, 2 ve 3. bölgelerde bir güvenlik duvarı oluşturur.

Standart genel IP adresi oluşturulduğunda, belirli bir bölge belirtilmez. Bu, varsayılan olarak bölgesel olarak yedekli bir IP adresi oluşturur. Standart genel IP adresleri tüm bölgelerde ya da tek bir bölgede yapılandırılabilir.

Bölge 1 ' de bir güvenlik duvarına ve bölge 2 ' deki bir IP adresine sahip olmadığınızdan bilmeniz önemlidir. Ancak bölge 1 ' de, tüm bölgelerde IP adresinde bir güvenlik duvarınız veya bir güvenlik duvarı ve aynı tek bölgedeki bir IP adresi için yakınlık amaçlarına sahip olabilirsiniz.

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
  -PublicIpAddress @($pip1) `
  -Zone 1,2,3
```

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure Güvenlik Duvarı günlüklerini izleme](./firewall-diagnostics.md)