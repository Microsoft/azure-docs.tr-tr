---
title: 'PowerShell: bir otomatik yük devretme grubuna yönetilen bir örnek ekleme'
titleSuffix: Azure SQL Managed Instance
description: Yönetilen bir örnek oluşturmak için örnek betik Azure PowerShell, bir otomatik yük devretme grubuna ekleyin ve yük devretmeyi test edin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/16/2019
ms.openlocfilehash: af1d7110cccfd8b0617d6c79eb5373cc7c087159
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594020"
---
# <a name="use-powershell-to-add-a-managed-instance-to-a-failover-group"></a>Yük devretme grubuna yönetilen bir örnek eklemek için PowerShell 'i kullanma 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Bu PowerShell betiği örneği, iki yönetilen örnek oluşturur, bunları bir yük devretme grubuna ekler ve ardından birincil yönetilen örnekten yük devretmeyi ikinci yönetilen örneğe sınar. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure PowerShell 1.4.0 veya üzeri bir sürüm gerekir. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="sample-scripts"></a>Örnek betikler

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın. Kaynak grubunu iki kez kaldırmanız gerekecektir. Kaynak grubunun ilk kez kaldırılması yönetilen örneği ve sanal kümeleri kaldırır, ancak ardından hata iletisiyle başarısız olur `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` . Kalan kaynakları ve kaynak grubunu kaldırmak için Remove-AzResourceGroup komutunu ikinci kez çalıştırın.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Bir Azure Kaynak grubu oluşturur.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Sanal ağ oluşturur.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Bir sanal ağa alt ağ yapılandırması ekler. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Bir kaynak grubundaki sanal ağı alır. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Bir sanal ağ içindeki bir alt ağı alır. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Bir ağ güvenlik grubu oluşturur. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Bir yol tablosu oluşturur. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Bir sanal ağ için alt ağ yapılandırmasını güncelleştirir.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Bir sanal ağı güncelleştirir.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bir ağ güvenlik grubu alır. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ağ güvenlik grubuna ağ güvenlik kuralı yapılandırması ekler. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Bir ağ güvenlik grubunu güncelleştirir.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Rota tablosuna bir yol ekler. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Bir yol tablosunu güncelleştirir.  |
| [New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance) | Yönetilen bir örnek oluşturur.  |
| [Get-Azsqlınstance](/powershell/module/az.sql/get-azsqlinstance)| Azure SQL yönetilen örneği hakkında bilgi döndürür. |
| [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) | Genel bir IP adresi oluşturur.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Sanal ağ geçidi için bir IP yapılandırması oluşturur |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Bir sanal ağ geçidi oluşturur |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | İki sanal ağ geçidi arasında bir bağlantı oluşturur.   |
| [New-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Yeni bir Azure SQL yönetilen örnek yük devretme grubu oluşturur.  |
| [Get-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | SQL yönetilen örnek yük devretme gruplarını alır veya listeler.| 
| [Switch-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | SQL yönetilen örnek yük devretme grubunun yük devretmesini yürütür. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır. | 

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).

SQL yönetilen örneği için ek PowerShell betiği örnekleri, [Azure SQL yönetilen örnek PowerShell betikleri](../../database/powershell-script-content-guide.md)içinde bulunabilir.
