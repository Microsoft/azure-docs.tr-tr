---
title: Azure PowerShell Betiği Örneği - RDP bağlantı noktası aralığını değiştirme | Microsoft Docs
description: Azure PowerShell Betiği Örneği - Dağıtılan bir kümenin RDP bağlantı noktası aralığını değiştirir.
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
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4ca7b4085a3978dc3218480c5c8a42679d80de23
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035665"
---
# <a name="update-the-rdp-port-range-values"></a>RDP bağlantı noktası aralığı değerlerini güncelleştirme

Bu örnek betik, küme dağıtıldıktan sonra küme düğümü sanal makinelerinde RDP bağlantı noktası aralığı değerlerini değiştirir.  Temel sanal makinelerin döngüye girmemesi için Azure PowerShell kullanılır.  Betik, kümenin kaynak grubundaki `Microsoft.Network/loadBalancers` kaynağını alıp `inboundNatPools.frontendPortRangeStart` ve `inboundNatPools.frontendPortRangeEnd` değerlerini güncelleştirir. Parametreleri gereken şekilde özelleştirin.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Gerekirse, [Azure PowerShell kılavuzunda](/powershell/azure/overview) bulunan yönergeyi kullanarak Azure PowerShell’i yükleyin. 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | `Microsoft.Network/loadBalancers` kaynağını alır. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|`Microsoft.Network/loadBalancers` kaynağını güncelleştirir.|

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Azure Service Fabric için ek Azure PowerShell örneklerini [Azure PowerShell örnekleri](../service-fabric-powershell-samples.md) bölümünde bulabilirsiniz.
