---
title: Azure PowerShell kullanarak bir Azure DDoS koruma planı oluşturma ve yapılandırma
description: Azure PowerShell kullanarak DDoS koruma planı oluşturmayı öğrenin
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 41d45b216337cc1b674a9be390f241c4e1bb062a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103139"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak Azure DDoS koruma standardı oluşturma ve yapılandırma

Azure PowerShell kullanarak Azure DDoS koruma standardı ile çalışmaya başlayın. 

DDoS koruma planı, aboneliklerde DDoS koruma standardı etkin olan bir sanal ağ kümesini tanımlar. Kuruluşunuz için bir DDoS koruma planı yapılandırabilir ve sanal ağları birden çok abonelikten aynı plana bağlayabilirsiniz. 

Bu hızlı başlangıçta bir DDoS koruma planı oluşturacak ve bunu bir sanal ağa bağlayacaksınız. 

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell yerel olarak veya Azure Cloud Shell yüklendi

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>DDoS koruma planı oluşturma

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. Var olan bir kaynak grubunu kullanabilir ya da yeni bir tane oluşturabilirsiniz.

Bir kaynak grubu oluşturmak için [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)komutunu kullanın. Bu örnekte kaynak grubumuzu _Myresourcegroup_ olarak adlandırın ve _Doğu ABD_ konumunu kullanın:

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Şimdi _Myddosprotectionplan_ adlı bir DDoS koruma planı oluşturun:

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Sanal ağ için DDoS 'yi etkinleştirme

### <a name="enable-ddos-for-a-new-virtual-network"></a>Yeni bir sanal ağ için DDoS 'ı etkinleştir

Bir sanal ağ oluştururken DDoS korumasını etkinleştirebilirsiniz. Bu örnekte sanal ağı _Myvnet_ olarak adlandırın: 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Mevcut bir sanal ağ için DDoS 'ı etkinleştir

DDoS koruma planı oluştururken var olan bir sanal ağı ilişkilendirebilirsiniz:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Doğrula ve test et

İlk olarak, DDoS koruma planınızın ayrıntılarını kontrol edin:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Komutun DDoS koruma planınızın doğru ayrıntılarını döndürdüğünden emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynaklarınızın bir sonraki öğreticide kalmasını sağlayabilirsiniz. Artık gerekmiyorsa, _Myresourcegroup_ kaynak grubunu silin. Kaynak grubunu sildiğinizde, DDoS koruma planını ve ilgili tüm kaynakları da silersiniz. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Bir sanal ağ için DDoS korumasını devre dışı bırakmak için: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

DDoS koruma planını silmek istiyorsanız, önce tüm sanal ağların ilişkilendirmesini kaldırmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

DDoS koruma planınız için telemetri görüntüleme ve yapılandırma hakkında bilgi edinmek için öğreticilere devam edin.

> [!div class="nextstepaction"]
> [DDoS konuma telemetrisini görüntüleme ve yapılandırma](telemetry.md)