---
title: PowerShell 'de ağ güvenlik grubu kuralı ekleme
description: Azure PowerShell betik örneği-belirli bir bağlantı noktasında gelen trafiğe izin vermek için bir ağ güvenlik grubu ekler.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 167fa74b04af7c086c2c95b0fdd56e5932483080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076208"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Gelen ağ güvenlik grubu kuralı ekleme

Bu örnek betik, 8081 numaralı bağlantı noktasında gelen trafiğe izin veren bir ağ güvenlik grubu kuralı oluşturur.  Betik ağ güvenlik grubunu alır, yeni bir ağ güvenlik yapılandırması kuralı oluşturur ve ağ güvenlik grubunu güncelleştirir. Parametreleri gereken şekilde özelleştirin.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/)bulunan yönergeleri kullanarak Azure PowerShell ' yi yüklemelisiniz. 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | `Microsoft.Network/networkSecurityGroups` kaynağını alır. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Ağ güvenlik grubunu ada göre alır.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ağ güvenlik grubuna ağ güvenlik kuralı yapılandırması ekler. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Bir ağ güvenlik grubu için hedef durumunu ayarlar.|

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).
