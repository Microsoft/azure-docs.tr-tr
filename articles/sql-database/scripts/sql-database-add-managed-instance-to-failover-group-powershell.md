---
title: PowerShell örneği-yük devretme grubu-Azure SQL veritabanı yönetilen örneği
description: Azure SQL veritabanı yönetilen örneği oluşturmak, bir yük devretme grubuna eklemek ve yük devretme sınamasını yapmak için örnek betik Azure PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: e50877f6f3194885b139683fe865144384716b48
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691750"
---
# <a name="use-powershell-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>PowerShell kullanarak bir yük devretme grubuna Azure SQL veritabanı yönetilen örneği ekleme 

Bu PowerShell betiği örneği, iki yönetilen örnek oluşturur, bunları bir yük devretme grubuna ekler ve ardından birincil yönetilen örnekten yük devretmeyi ikinci yönetilen örneğe sınar. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici AZ PowerShell 1.4.0 veya üstünü gerektirir. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

## <a name="sample-scripts"></a>Örnek komut dosyaları

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme

Kaynak grubunu ve onunla ilişkili tüm kaynakları kaldırmak için aşağıdaki komutu kullanın. Kaynak grubunu iki kez kaldırmanız gerekecektir. Kaynak grubunun ilk kez kaldırılması yönetilen örneği ve sanal kümeleri kaldırır, ancak `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`hata iletisiyle başarısız olur. Kalan kaynakları ve kaynak grubunu kaldırmak için Remove-AzResourceGroup komutunu ikinci kez çalıştırın.

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
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Ağ güvenlik grubu oluşturur. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Bir yol tablosu oluşturur. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Bir sanal ağ için alt ağ yapılandırmasını güncelleştirir.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Bir sanal ağı güncelleştirir.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bir ağ güvenlik grubu alır. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ağ güvenlik grubuna ağ güvenlik kuralı yapılandırması ekler. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Bir ağ güvenlik grubunu güncelleştirir.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Rota tablosuna bir yol ekler. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Bir yol tablosunu güncelleştirir.  |
| [New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance) | Azure SQL veritabanı yönetilen örneği oluşturur.  |
| [Get-Azsqlınstance](/powershell/module/az.sql/get-azsqlinstance)| Azure SQL yönetilen veritabanı örneği hakkında bilgi döndürür. |
| [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) | Genel bir IP adresi oluşturur.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Sanal ağ geçidi için bir IP yapılandırması oluşturur |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Bir sanal ağ geçidi oluşturur |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | İki sanal ağ geçidi arasında bir bağlantı oluşturur.   |
| [New-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Yeni bir Azure SQL veritabanı yönetilen örneği yük devretme grubu oluşturur.  |
| [Get-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Yönetilen örnek yük devretme gruplarını alır veya listeler.| 
| [Switch-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Yönetilen örnek yük devretme grubunun yük devretmesini yürütür. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır. | 

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek SQL Veritabanı PowerShell betiği örnekleri [Azure SQL Veritabanı PowerShell betikleri](../sql-database-powershell-samples.md) içinde bulunabilir.
