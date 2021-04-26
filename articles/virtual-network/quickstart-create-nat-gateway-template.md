---
title: NAT Gateway-Kaynak Yöneticisi şablonu oluşturma
titleSuffix: Azure Virtual Network NAT
description: Bu hızlı başlangıçta, Azure Resource Manager şablonu (ARM şablonu) kullanılarak bir NAT Gateway oluşturma gösterilmektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: ea214cb98e1f75daccf783333a67c6d1b0eacfeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776616"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Hızlı başlangıç: NAT Gateway-ARM şablonu oluşturma

Azure Resource Manager şablonu kullanarak sanal ağ NAT ile çalışmaya başlama (ARM şablonu). Bu şablon bir sanal ağ, bir NAT ağ geçidi kaynağı ve Ubuntu sanal makinesini dağıtır. Ubuntu sanal makinesi, NAT ağ geçidi kaynağıyla ilişkili bir alt ağa dağıtılır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm) alınmıştır.

Bu şablon, şunu oluşturacak şekilde yapılandırıldı:

* Sanal ağ
* NAT ağ geçidi kaynağı
* Ubuntu sanal makinesi

Ubuntu VM, NAT ağ geçidi kaynağıyla ilişkili bir alt ağa dağıtılır.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

Şablonda dokuz Azure kaynağı tanımlanmıştır:

* **[Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)**: bir ağ güvenlik grubu oluşturur.
* **[Microsoft. Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: bir güvenlik kuralı oluşturur.
* **[Microsoft. Network/Publicıpaddresses](/azure/templates/microsoft.network/publicipaddresses)**: genel bir IP adresi oluşturur.
* **[Microsoft. Network/Publicıpprefix](/azure/templates/microsoft.network/publicipprefixes)**: genel bir IP öneki oluşturur.
* **[Microsoft. COMPUTE/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)**: bir sanal makine oluşturur.
* **[Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)**: bir sanal ağ oluşturur.
* **[Microsoft. Network/natgateway](/azure/templates/microsoft.network/natgateways)**: bir NAT ağ geçidi kaynağı oluşturur.
* **[Microsoft. Network/virtualNetworks/alt ağları](/azure/templates/microsoft.network/virtualnetworks/subnets)**: bir sanal ağ alt ağı oluşturur.
* **[Microsoft. Network/NetworkInterfaces](/azure/templates/microsoft.network/networkinterfaces)**: bir ağ arabirimi oluşturur.

## <a name="deploy-the-template"></a>Şablonu dağıtma

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure portalı**

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmeden **kaynak grupları** ' nı seçin.

1. Önceki bölümde oluşturduğunuz kaynak grubunu seçin. Varsayılan kaynak grubu adı **Myresourcegroupnat** ' dır

1. Kaynak grubunda aşağıdaki kaynakların oluşturulduğunu doğrulayın:

    ![Sanal ağ NAT kaynak grubu](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

**Azure CLI**

Artık gerekli değilse, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu ve içinde yer alan tüm kaynakları kaldırabilirsiniz.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içinde yer alan tüm kaynakları kaldırabilirsiniz.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure portalı**

Artık gerekli olmadığında kaynak grubunu, NAT ağ geçidini ve tüm ilgili kaynakları silin. NAT ağ geçidini içeren **Myresourcegroupnat** kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

* NAT ağ geçidi kaynağı
* Sanal ağ
* Ubuntu sanal makinesi

Sanal makine, NAT ağ geçidiyle ilişkili bir sanal ağ alt ağına dağıtılır.

Sanal ağ NAT ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

* [Sanal ağ NAT 'ye genel bakış](nat-overview.md) konusunu okuyun
* [NAT ağ geçidi kaynağı](nat-gateway-resource.md) hakkında bilgi edinin
* [Azure Resource Manager](../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin
