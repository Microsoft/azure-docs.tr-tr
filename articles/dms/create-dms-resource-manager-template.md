---
title: DMS örneği oluştur (Azure Resource Manager şablonu)
description: Azure Resource Manager şablonu (ARM şablonu) kullanarak veritabanı geçiş hizmeti oluşturmayı öğrenin.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: dms
ms.openlocfilehash: a9c68bca4d50af734a0a2cd8a91c7e46d9b56ff1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94963224"
---
# <a name="quickstart-create-instance-of-azure-database-migration-service-using-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Azure veritabanı geçiş hizmeti örneği oluşturma

Azure veritabanı geçiş hizmeti 'nin bir örneğini dağıtmak için bu Azure Resource Manager şablonunu (ARM şablonu) kullanın. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure veritabanı geçiş hizmeti ARM şablonu şunları gerektirir: 

- [Azure CLI](/cli/azure/install-azure-cli) ve/veya [PowerShell](/powershell/scripting/install/installing-powershell)'in en son sürümü. 
- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-azure-database-migration-simple-deploy/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-azure-database-migration-simple-deploy/azuredeploy.json":::

Şablonda üç Azure kaynağı tanımlanmıştır: 

- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks): sanal ağ oluşturur. 
- [Microsoft. Network/virtualNetworks/alt ağları](/azure/templates/microsoft.network/virtualnetworks/subnets): alt ağ oluşturur. 
- [Microsoft. DataMigration/Services](/azure/templates/microsoft.datamigration/services): Azure veritabanı geçiş hizmeti 'nin bir örneğini dağıtır. 

[Hızlı başlangıç şablonu galerisinde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datamigration&pageNumber=1&sort=Popular)daha fazla Azure veritabanı geçiş Hizmetleri şablonu bulunabilir.


## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon, Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturur. 

   [![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-database-migration-simple-deploy%2fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.

    * **Abonelik**: bir Azure aboneliği seçin.
    * **Kaynak grubu**: açılan listeden var olan bir kaynak grubunu seçin veya yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' u seçin. 
    * **Bölge**: kaynakların dağıtılacağı konum.
    * **Hizmet adı**: yeni geçiş hizmetinin adı.
    * **Konum**: kaynak grubunun konumu, varsayılan olarak bırakın `[resourceGroup().location]` .
    * **VNET adı**: yeni sanal ağın adı.
    * **Alt ağ adı**: sanal ağla ilişkili yeni alt ağın adı.



3. **Gözden geçir ve oluştur**’u seçin. Azure veritabanı geçiş hizmeti örneği başarıyla dağıtıldıktan sonra bir bildirim alırsınız. 


Şablonu dağıtmak için Azure portalı kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtılan kaynakları denetlemek için Azure CLı ' yı kullanabilirsiniz. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```


## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silin:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Sonraki adımlar

Şablon oluşturma sürecinde size kılavuzluk eden adım adım bir öğretici için, bkz.:

> [!div class="nextstepaction"]
> [ Öğretici: ilk ARM şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Azure veritabanı geçiş hizmeti 'ni dağıtmanın diğer yolları için bkz.: 
- [Azure portalı](quickstart-create-data-migration-service-portal.md)

Daha fazla bilgi edinmek için bkz. [Azure veritabanı geçiş hizmeti 'ne genel bakış](dms-overview.md)