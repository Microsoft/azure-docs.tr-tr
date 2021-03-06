---
title: Azure PowerShell Betiği Örneği - Web trafiğini kısıtlama | Microsoft Docs
description: Azure PowerShell Betik Örneği - Bir web uygulaması güvenlik duvarı ve trafiği kısıtlamak için OWASP kurallarını kullanan bir sanal makine ölçek kümesi ile bir uygulama ağ geçidi oluşturun.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: f20e5271cdca18ad40710e5defdf8175754ef891
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078693"
---
# <a name="restrict-web-traffic-using-azure-powershell"></a>Azure PowerShell kullanarak web trafiğini kısıtlama

Bu betik, arka uç sunucuları için bir sanal makine ölçek kümesi kullanan bir web uygulaması güvenlik duvarı ile bir uygulama ağ geçidi oluşturur. Web uygulaması güvenlik duvarı, OWASP kurallarına göre web trafiğini kısıtlar. Betiği çalıştırdıktan sonra, genel IP adresini kullanarak uygulama ağ geçidini test edebilirsiniz.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss-waf.ps1 "Create application gateway with WAF")]

## <a name="clean-up-deployment"></a>Dağıtımı temizleme 

Kaynak grubunu, uygulama ağ geçidini ve ilgili tüm kaynakları kaldırmak için aşağıdaki komutu çalıştırın.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, dağıtımı oluşturmak için aşağıdaki komutları kullanır. Tablodaki her öğe, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alt ağ yapılandırmasını oluşturur. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Alt ağ yapılandırmalarıyla kullanarak sanal ağı oluşturur. |
| [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) | Uygulama ağ geçidi için genel IP adresini oluşturur. |
| [New-Azapplicationgatewayıp yapılandırması](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Bir alt ağı uygulama ağ geçidiyle ilişkilendiren yapılandırmayı oluşturur. |
| [New-Azapplicationgatewayfrontendıpconfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Uygulama ağ geçidine genel bir IP adresi atayan yapılandırmayı oluşturur. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Uygulama ağ geçidine erişmek için kullanılacak bir bağlantı noktası atar. |
| [New-Azapplicationgatewaybackendadddresspool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Uygulama ağ geçidi için bir arka uç havuzu oluşturur. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Arka uç havuzu için ayarları yapılandırır. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Dinleyici oluşturur. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Yönlendirme kuralı oluşturur. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Uygulama ağ geçidi için katman ve kapasiteyi belirtin. |
| [New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) | Web uygulaması güvenlik duvarı yapılandırmasını oluşturur. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Uygulama ağ geçidi oluşturur. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Ölçek kümesi için bir depolama profili oluşturun. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | Ölçek kümesi için işletim sistemini tanımlayın. |
| [Add-Azvmssnetworkınterfaceconfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | Ölçek kümesi için ağ arabirimini tanımlayın. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | Sanal makine ölçek kümesi oluşturun. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Bir depolama hesabı oluşturur. |
| [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) | Veri kaydı için tanılamaları yapılandırır. |
| [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) | Uygulama ağ geçidinin genel IP adresini alır. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Kaynak grubunu ve grubun içerdiği tüm kaynakları kaldırır. | 
## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/).

Ek uygulama ağ geçidi PowerShell betiği örnekleri, [Azure Application Gateway belgeleri](../powershell-samples.md) içinde bulunabilir.
