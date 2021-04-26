---
title: Azure Resource Manager şablonu kullanarak adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturun
description: Azure Resource Manager şablonu kullanarak Azure SYNAPSE Analytics SQL havuzu oluşturmayı öğrenin.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.author: jrasnick
ms.date: 06/09/2020
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 73f4baf1e48b5495d513fbabe66763538c8cca57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568480"
---
# <a name="quickstart-create-an-azure-synapse-analytics-dedicated-sql-pool-formerly-sql-dw-by-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonu kullanarak Azure SYNAPSE Analytics adanmış SQL Havuzu (eski adıyla SQL DW) oluşturma

Bu Azure Resource Manager şablonu (ARM şablonu), Saydam Veri Şifrelemesi etkin bir adanmış SQL Havuzu (eski adıyla SQL DW) oluşturacak. Adanmış SQL Havuzu (eski adıyla SQL DW), Azure SYNAPSE 'da genel kullanıma sunulan kurumsal veri ambarı özelliklerine başvurur.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/201-sql-data-warehouse-transparent-encryption-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/201-sql-data-warehouse-transparent-encryption-create/azuredeploy.json":::

Şablon bir kaynağı tanımlar:

- [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve şablonu açmak için aşağıdaki görüntüyü seçin. Bu şablon adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturur.
   
   [![Azure’a dağıtın](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-sql-data-warehouse-transparent-encryption-create%2Fazuredeploy.json)

1. Aşağıdaki değerleri girin veya güncelleştirin:

   * **Abonelik**: bir Azure aboneliği seçin.
   * **Kaynak grubu**: **Yeni oluştur** ' u seçin ve kaynak grubu için benzersiz bir ad girin ve **Tamam**' ı seçin. Yeni bir kaynak grubu, kaynak temizlemeyi kolaylaştırır.
   * **Bölge**: bir bölge seçin.  Örneğin **Orta ABD**.
   * **SQL Server adı**: varsayılan adı kabul edin veya SQL Server adı için bir ad girin.
   * **SQL Yöneticisi oturum açma**: SQL Server için yönetici kullanıcı adını girin.
   * **SQL yönetici parolası**: SQL Server için yönetici parolasını girin.
   * **Veri ambarı adı**: ADANMıŞ bir SQL havuzu adı girin.
   * **Saydam veri şifrelemesi**: varsayılan, etkin ' i kabul edin. 
   * **Hizmet düzeyi hedefi**: varsayılan, DW400c kabul edin.
   * **Konum**: kaynak grubunun varsayılan konumunu kabul edin.
   * **Gözden geçirin ve oluşturun**: seçin.
   * **Oluştur**: seç.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Dağıtılan kaynakları denetlemek için Azure portal kullanabilir ya da dağıtılan kaynakları listelemek için Azure CLı veya Azure PowerShell betiği kullanabilirsiniz.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group where your dedicated SQL pool (formerly SQL DW) exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your dedicated SQL pool (formerly SQL DW) account exists"
(Get-AzResource -ResourceType "Microsoft.Sql/servers/databases" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

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

Bu hızlı başlangıçta, ARM şablonunu kullanarak adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturdunuz ve dağıtımı doğruladı. Azure SYNAPSE Analytics ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın.

- [Azure SYNAPSE Analytics 'e genel bakış](sql-data-warehouse-overview-what-is.md) konusunu okuyun
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin
- [İlk ARM şablonunuzu oluşturma ve dağıtma](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
