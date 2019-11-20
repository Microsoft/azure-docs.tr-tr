---
title: Öğretici-Azure 'da SQL, IIS, .NET Stack çalıştıran VM 'Ler oluşturma
description: Bu öğreticide, Azure’daki bir Windows sanal makinesinde Azure SQL, IIS, .NET yığını yüklemeyi öğrenirsiniz.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 286d00ce718a34dea15a075984cc3b2a5e4cfec3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74064672"
---
# <a name="tutorial-install-the-sql-iis-net-stack-in-a-windows-vm-with-azure-powershell"></a>Öğretici: Azure PowerShell ile SQL, IIS, .NET Stack 'i Windows sanal makinesine yüklemeyin

Bu öğreticide, Azure PowerShell kullanarak bir SQL, IIS, .NET yığını yükleyeceğiz. Bu yığın, Windows Server 2016’da çalışan, biri IIS ve .NET, diğeri SQL Server’a sahip iki sanal makineden oluşur.

> [!div class="checklist"]
> * VM oluşturma 
> * Sanal makineye IIS ve .NET Core SDK’sı yükleme
> * SQL Server çalıştıran bir sanal makine oluşturma
> * SQL Server uzantısı yükleme

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell'i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. İsterseniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) adresine giderek Cloud Shell'i ayrı bir tarayıcı sekmesinde de başlatabilirsiniz. **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="create-an-iis-vm"></a>IIS VM oluşturma 

Bu örnekte, PowerShell Cloud Shell [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet 'ini hızla bir Windows Server 2016 VM oluşturmak ve ardından ııs ve .NET Framework yüklemek için kullanırız. IIS ve SQL sanal makineleri bir kaynak grubu ile sanal ağ paylaşır, dolayısıyla bu adlara yönelik değişkenler oluştururuz.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

[Set-Azvmexgersıon](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) cmdlet 'i ile özel Betik uzantısı 'Nı kullanarak IIS ve .NET Framework 'ü kurun.

```azurepowershell-interactive
Set-AzVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Başka bir alt ağ oluşturma

SQL sanal makinesi için ikincil alt ağ oluşturun. [Get-AzVirtualNetwork] {/PowerShell/Module/az.exe Network/Get-azvirtualnetwork} kullanarak vNet 'i alın.

```azurepowershell-interactive
$vNet = Get-AzVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig)kullanarak alt ağ için bir yapılandırma oluşturun.


```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

[Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) kullanarak sanal ağı yeni alt ağ bilgileriyle güncelleştirin
   
```azurepowershell-interactive   
$vNet | Set-AzVirtualNetwork
```

## <a name="azure-sql-vm"></a>Azure SQL VM

SQL VM oluşturmak için bir SQL Server'ın önceden yapılandırılmış Azure Market görüntüsünü kullanın. İlk olarak VM’i oluştururuz, ardından bu VM’e SQL Server Uzantısı’nı yükleriz. 


```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

[SQL Server UZANTıSıNı](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) SQL VM 'ye eklemek için [set-azvmsqlserverextension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsqlserverextension) komutunu kullanın.

```azurepowershell-interactive
Set-AzVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure PowerShell kullanarak SQL&#92;IIS&#92;.NET yığını yüklediniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * VM oluşturma 
> * Sanal makineye IIS ve .NET Core SDK’sı yükleme
> * SQL Server çalıştıran bir sanal makine oluşturma
> * SQL Server uzantısı yükleme

SSL sertifikalarını kullanarak IIS web sunucusunun güvenliğini nasıl sağlayabileceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [SSL sertifikalarını kullanarak IIS web sunucusunun güvenliğini sağlama](tutorial-secure-web-server.md)

