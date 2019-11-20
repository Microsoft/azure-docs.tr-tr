---
title: Azure PowerShell betiği örneği - Azure Güvenlik Duvarı test ortamı oluşturma
description: Bu betik örneği, bir güvenlik duvarı ve bir test ağ ortamı oluşturur. Ağın, üç alt ağı olan bir VNet 'i vardır.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 79fce47c02decffa8132ed4b093992d2db2c937f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166927"
---
# <a name="create-an-azure-firewall-test-environment"></a>Azure Güvenlik Duvarı test ortamı oluşturma

Bu betik örneği, bir güvenlik duvarı ve bir test ağ ortamı oluşturur. Ağın bir Sanal Ağı ve üç alt ağı vardır: *AzureFirewallSubnet*, *ServersSubnet* ve *JumpboxSubnet*. ServersSubnet ve JumpboxSubnet'ten her birinin içinde bir tane 2 çekirdekli Windows Server bulunur.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Güvenlik Duvarı AzureFirewallSubnet ' dir ve `www.microsoft.com`erişimine izin veren tek bir kuralla bir uygulama kuralı koleksiyonu ile yapılandırılır.

Güvenlik duvarı kurallarının uygulandığı güvenlik duvarı üzerinden ServersSubnet'ten gelen ağ trafiğine işaret eden, kullanıcı tanımlı bir yol oluşturulur.

Azure [Cloud Shell](https://shell.azure.com/powershell)’den veya yerel bir PowerShell yüklemesinden betiği çalıştırabilirsiniz. 

PowerShell 'i yerel olarak çalıştırırsanız, bu betik Azure PowerShell gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. 

Yükseltmeniz gerekiyorsa, Windows 10 ve Windows Server 2016'da yerleşik olarak sağlanan `PowerShellGet` komutunu kullanabilirsiniz.

> [!NOTE]
>Diğer Windows sürümleri, kullanabilmek için `PowerShellGet` yüklemenizi gerektirir. Sisteminizde yüklü olup olmadığını saptamak için `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` komutunu çalıştırabilirsiniz. Çıkış boşsa, en son [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)'ü yüklemeniz gerekir.

Daha fazla bilgi için bkz. [ınstall Azure PowerShell](/powershell/azure/install-Az-ps)

Web Platformu yükleyicisiyle yapılmış mevcut Azure PowerShell yüklemeleri PowerShellGet yüklemesiyle çakışır ve kaldırılması gerekir.

PowerShell'i yerel olarak çalıştırırsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerektiğini unutmayın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, sanal makineyi ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, bir kaynak grubu, sanal ağ ve ağ güvenliği grupları oluşturmak için aşağıdaki komutları kullanır. Aşağıdaki tabloda yer alan her komut, komuta özgü belgelere yönlendirir:

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Bir alt ağ yapılandırma nesnesi oluşturur |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Bir Azure sanal ağı ve ön uç alt ağı oluşturur. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Bir ağ güvenlik grubuna atanacak güvenlik kuralları oluşturur. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Belirli alt ağlara yönelik belirli bağlantı noktalarına izin veren veya engelleyen NSG kuralları oluşturur. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | NSG’leri alt ağlarla ilişkilendirir. |
| [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) | İnternet’ten sanal makineye erişmek için genel IP adresi oluşturur. |
| [New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface) | Sanal ağ arabirimleri oluşturur ve bunları sanal ağın ön uç ve arka uç alt ağlarına ekler. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Sanal makine yapılandırması oluşturur. Bu yapılandırma; sanal makine adı, işletim sistemi ve yönetici kimlik bilgileri gibi bilgileri içerir. Yapılandırma, sanal makine oluşturulurken kullanılır. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Sanal makine oluşturur. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Kaynak grubunu ve grubun içerdiği tüm kaynakları kaldırır. |
|[New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)| Yeni bir Azure Güvenlik Duvarı oluşturur.|
|[Get-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/get-azfirewall)|Azure Güvenlik Duvarı nesnesini alır.|
|[New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule)|Yeni bir Azure Güvenlik Duvarı uygulama kuralı oluşturur.|
|[Set-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/set-azfirewall)|Değişiklikleri Azure Güvenlik Duvarı nesnesine işler.|

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

