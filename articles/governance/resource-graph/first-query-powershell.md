---
title: 'Hızlı başlangıç: ilk PowerShell sorgunuz'
description: Bu hızlı başlangıçta, Azure PowerShell için kaynak Graph modülünü etkinleştirmek ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- mode-api
ms.openlocfilehash: e5e276e3be80354eeaaeba2821eb9e3242b368ad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533073"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak ilk kaynak grafik sorgunuzu çalıştırın

Azure Kaynak Grafiği’ni kullanmada ilk adım, Azure PowerShell modülünün yüklenip yüklenmediğini denetlemektir. Bu hızlı başlangıç, Azure PowerShell yüklemenize modül ekleme işlemini incelemenizi sağlar.

Bu işlemin sonunda, modülü seçtiğiniz Azure PowerShell yüklemesine eklemiş ve ilk Kaynak Grafiği sorgunuzu çalıştırmış olacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Kaynak Grafiği modülü ekleme

Azure PowerShell’in Azure Kaynak Grafiği’ni sorgulamasını etkinleştirmek için modül eklenmelidir. Bu modül, [Azure Cloud Shell](https://shell.azure.com)Ile veya [PowerShell Docker görüntüsü](https://hub.docker.com/_/microsoft-powershell)Ile yerel olarak yüklü PowerShell ile kullanılabilir.

### <a name="base-requirements"></a>Temel gereksinimler

Azure Kaynak Grafiği modülü aşağıdaki yazılımı gerektirir:

- Azure PowerShell 1.0.0 veya üzeri. Henüz yüklenmiş değilse, [bu yönergeleri](/powershell/azure/install-az-ps) izleyin.

- PowerShellGet 2.0.1 veya üzeri. Henüz yüklenmiş ve güncellenmiş değilse, [bu yönergeleri](/powershell/scripting/gallery/installing-psget) izleyin.

### <a name="install-the-module"></a>Modülü yükler

PowerShell için kaynak Graph modülü **az. ResourceGraph**' dir.

1. Bir **Yönetim** PowerShell isteminde aşağıdaki komutu çalıştırın:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Modülün içeri aktarıldığını ve en son sürümü (0.7.5) olduğunu doğrulayın:

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

Azure PowerShell modülünün seçtiğiniz ortamınıza eklenmesiyle birlikte şimdi basit bir Kaynak Grafiği sorgusu denemenin zamanı geldi. Sorgu, ilk beş Azure kaynağını her bir kaynağın **adı** ve **kaynak türü** ile döndürür.

1. `Search-AzGraph` cmdlet’ini kullanarak İlk Azure Kaynak Grafiği sorgunuzu çalıştırın:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Bu sorgu örneği, `order by` gibi bir sıralama değiştirici sağlamadığı için, bu sorgunun birden çok kez çalıştırılması muhtemelen istek başına farklı bir kaynak kümesi sunacaktır.

1. Sorguyu `order by`**Ad** özelliğine güncelleştirin:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. Sorguyu ilk önce `order by`**Ad** özelliğine ve ardından `limit`’e en iyi beş sonuca güncelleştirin:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

> [!NOTE]
> Sorgu, zaten erişiminiz olan bir abonelikteki sonuçları döndürmezse, `Search-AzGraph` cmdlet 'in varsayılan bağlamdaki abonelikler için varsayılan değerleri olduğunu unutmayın. Varsayılan bağlamın bir parçası olan abonelik kimliklerinin listesini görmek için `(Get-AzContext).Account.ExtendedProperties.Subscriptions` , erişiminiz olan tüm aboneliklerde arama yapmak istiyorsanız, bir tane `Search-AzGraph` çalıştırarak cmdlet Için PSDefaultParameterValues 'ı çalıştırabilirsiniz. `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak Grafiği modülünü Azure PowerShell ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Bu, daha önce indirilen modül dosyasını silmez. Yalnızca çalışan PowerShell ortamından kaldırır.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak Graph modülünü Azure PowerShell ortamınıza eklediniz ve ilk sorgunuzu çalıştırdık. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)
