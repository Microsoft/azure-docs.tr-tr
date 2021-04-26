---
title: 'Hızlı başlangıç: Azure PowerShell paylaşılan bir sorgu oluşturma'
description: Bu hızlı başlangıçta, Azure PowerShell kullanarak kaynak grafiği paylaşılan sorgusu oluşturma adımlarını izleyin.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9015b6df99bdd6f153194e8f4cbbe7658cf1d6dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535798"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak kaynak grafiği paylaşılan sorgusu oluşturma

Bu makalede, [az. ResourceGraph](/powershell/module/az.resourcegraph) PowerShell modülünü kullanarak Azure Kaynak Grafiği paylaşılan sorgusunu nasıl oluşturabileceğiniz açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > **Az. ResourceGraph** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` .

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Birden çok Azure aboneliğiniz varsa, kaynakların faturalandırılması gereken uygun aboneliği seçin. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 'ini kullanarak belirli bir abonelik seçin.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Kaynak Grafiği paylaşılan sorgusu oluşturma

Tercih ettiğiniz ortamınıza **az. ResourceGraph** PowerShell modülü eklendiğinde, kaynak grafiği paylaşılan sorgusu oluşturma zamanı. Paylaşılan sorgu, Azure Resource Graph Explorer 'da izin vereceğiniz veya çalıştırabileceğiniz bir Azure Resource Manager nesnesidir. Sorgu _konuma_ göre gruplandırılan tüm kaynakların sayısını özetler.

1. Azure Kaynak Grafiği paylaşılan sorgusunu depolamak için [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) ile bir kaynak grubu oluşturun. Bu kaynak grubunun adı `resource-graph-queries` ve konumu `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. **Az. ResourceGraph** PowerShell modülünü ve [New-azresourcegraphquery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) cmdlet 'Ini kullanarak Azure Kaynak Grafiği paylaşılan sorgusunu oluşturun:

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Yeni kaynak grubundaki paylaşılan sorguları listeleyin. [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) cmdlet 'i bir değer dizisi döndürür.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Yalnızca tek bir paylaşılan sorgu sonucunu almak için parametresiyle kullanın `Get-AzResourceGraphQuery` `Name` .

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak Grafiği paylaşılan sorgu ve kaynak grubunu Azure ortamınızdan kaldırmak isterseniz, aşağıdaki komutları kullanarak bunu yapabilirsiniz:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure PowerShell kullanarak bir kaynak grafiği paylaşılan sorgusu oluşturdunuz. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)
