---
title: 'Hızlı başlangıç: profil ve uç nokta Kaynak Yöneticisi şablonu oluşturma'
titleSuffix: Azure Content Delivery Network
description: Bu hızlı başlangıçta, bir Azure Content Delivery Network profili ve uç nokta Kaynak Yöneticisi şablonu oluşturmayı öğrenin
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: 31f0510ed50c9d48a46524a353d7c872b368f75c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779051"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Hızlı başlangıç: Azure CDN profili ve uç nokta ARM şablonu oluşturma

Bir Azure Resource Manager şablonu (ARM şablonu) kullanarak Azure Content Delivery Network (CDN) ile çalışmaya başlayın. Şablon bir profil ve bir uç nokta dağıtır.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/) alınmıştır.

Bu şablon, şunu oluşturacak şekilde yapılandırıldı:

* Profil
* Uç Nokta

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

Şablonda bir Azure kaynağı tanımlanmıştır:

* **[Microsoft. CDN/profiller](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Şablonu dağıtma

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portal

[![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Sol bölmeden **kaynak grupları** ' nı seçin.

3. Önceki bölümde oluşturduğunuz kaynak grubunu seçin. Varsayılan kaynak grubu adı **Myresourcegroupcdn**

4. Kaynak grubunda aşağıdaki kaynakların oluşturulduğunu doğrulayın:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN kaynak grubu" border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

### <a name="azure-cli"></a>Azure CLI

Artık gerekli değilse, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu ve içinde yer alan tüm kaynakları kaldırabilirsiniz.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içinde yer alan tüm kaynakları kaldırabilirsiniz.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portal

Artık gerekli olmadığında kaynak grubunu, CDN profilini ve tüm ilgili kaynakları silin. CDN profili ve uç noktası içeren **Myresourcegroupcdn** kaynak grubunu seçin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

* CDN profili
* Uç Nokta

Azure CDN ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: bir Web uygulamasından statik içerik sağlamak için CDN kullanma](cdn-add-to-web-app.md)
