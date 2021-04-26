---
title: 'Azure Resource Manager: Azure SQL yönetilen örneği oluşturma'
description: Azure Resource Manager şablonu kullanarak Azure SQL yönetilen örneği oluşturmayı öğrenin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/22/2020
ms.openlocfilehash: badeb850e8d0346347a994f053fb0f64fd01240a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91283352"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure SQL yönetilen örneği oluşturma

Bu hızlı başlangıç, Azure SQL yönetilen örneği ve vNet oluşturmak için bir Azure Resource Manager şablonu dağıtma işlemine odaklanır (ARM şablonu). [Azure SQL yönetilen örneği](sql-managed-instance-paas-overview.md) , SQL Server veritabanı altyapısı ile neredeyse %100 Özellik eşliği olan akıllı, tam olarak yönetilen, ölçeklenebilir bir bulut veritabanıdır.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sqlmi-new-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Bu kaynaklar şablonda tanımlanmıştır:

- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft. SQL/ManagedInstances**](/azure/templates/microsoft.sql/managedinstances)

Daha fazla şablon örneği, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

Azure Cloud Shell açmak için aşağıdaki PowerShell kod bloğundan **deneyin** öğesini seçin.

> [!IMPORTANT]
> Yönetilen bir örneği dağıtmak uzun süren bir işlemdir. Alt ağdaki ilk Örneğin dağıtımı, genellikle mevcut yönetilen örneklerle bir alt ağa dağıtmaya kıyasla çok daha uzun sürer. Ortalama sağlama süreleri için bkz. [SQL yönetilen örnek yönetimi işlemleri](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

[Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) ziyaret edin ve yönetilen Örneğin seçili kaynak grubunuzda olduğunu doğrulayın. Yönetilen bir örnek oluşturmak biraz zaman alabilir, kaynak grubunuzun **genel bakış** sayfasında **dağıtımlar** bağlantısını denetlemeniz gerekebilir.

- Azure sanal makinesinden SQL yönetilen örneği 'ne bağlanmayı gösteren bir hızlı başlangıç için bkz. [Azure sanal makine bağlantısını yapılandırma](connect-vm-instance-configure.md).
- Noktadan siteye bağlantı kullanarak bir şirket içi istemci bilgisayarından SQL yönetilen örneği 'ne bağlanmayı gösteren bir hızlı başlangıç için bkz. [Noktadan siteye bağlantı yapılandırma](point-to-site-p2s-configure.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Sonraki adımlara gitmek istiyorsanız yönetilen örneği koruyun, ancak ek [öğreticileri](#next-steps)tamamladıktan sonra yönetilen örneği ve ilgili kaynakları silin. Yönetilen bir örneği sildikten sonra, bkz. [yönetilen bir örneği sildikten sonra bir alt ağı silme](virtual-cluster-delete.md).


Kaynak grubunu silmek için:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure SQL yönetilen örneğine bağlanmak için bir Azure VM yapılandırma](connect-vm-instance-configure.md)
