---
title: PowerShell kullanarak ilk sorgunuzu çalıştırma
description: Bu makale, Azure PowerShell için Kaynak Grafiği modülünü etkinleştirmek ve ilk sorgunuzu çalıştırmak için gereken adımları incelemenizi sağlar.
ms.date: 10/18/2019
ms.topic: quickstart
ms.openlocfilehash: 6aaddcfc6cae16501ba6544f9789aa3ac1d13136
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958774"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak ilk kaynak grafik sorgunuzu çalıştırın

Azure Kaynak Grafiği’ni kullanmada ilk adım, Azure PowerShell modülünün yüklenip yüklenmediğini denetlemektir. Bu hızlı başlangıç, Azure PowerShell yüklemenize modül ekleme işlemini incelemenizi sağlar.

Bu işlemin sonunda, modülü seçtiğiniz Azure PowerShell yüklemesine eklemiş ve ilk Kaynak Grafiği sorgunuzu çalıştırmış olacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="add-the-resource-graph-module"></a>Kaynak Grafiği modülü ekleme

Azure PowerShell’in Azure Kaynak Grafiği’ni sorgulamasını etkinleştirmek için modül eklenmelidir. Bu modül, [Azure Cloud Shell](https://shell.azure.com)Ile veya [PowerShell Docker görüntüsü](https://hub.docker.com/_/microsoft-powershell)Ile yerel olarak yüklü PowerShell ile kullanılabilir.

### <a name="base-requirements"></a>Temel gereksinimler

Azure Kaynak Grafiği modülü aşağıdaki yazılımı gerektirir:

- Azure PowerShell 1.0.0 veya üzeri. Henüz yüklenmiş değilse, [bu yönergeleri](/powershell/azure/install-az-ps) izleyin.

- PowerShellGet 2.0.1 veya üzeri. Henüz yüklenmiş ve güncellenmiş değilse, [bu yönergeleri](/powershell/gallery/installing-psget) izleyin.

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

Azure PowerShell modülünün seçtiğiniz ortamınıza eklenmesiyle birlikte şimdi basit bir Kaynak Grafiği sorgusu denemenin zamanı geldi. Sorgu ilk beş Azure kaynağını, her kaynağın **Adı** ve **Kaynak Türü** ile birlikte döndürür.

1. `Search-AzGraph` cmdlet’ini kullanarak İlk Azure Kaynak Grafiği sorgunuzu çalıştırın:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Bu sorgu örneği, `order by` gibi bir sıralama değiştirici sağlamadığı için, bu sorgunun birden çok kez çalıştırılması muhtemelen istek başına farklı bir kaynak kümesi sunacaktır.

1. Sorguyu `order by` **Ad** özelliğine güncelleştirin:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu, sorgu sonuçlarını önce sınırlar, sonra düzenler.

1. Sorguyu ilk önce `order by` **Ad** özelliğine ve ardından `limit`’e en iyi beş sonuca güncelleştirin:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Son sorgu birkaç kere çalıştırıldığında, ortamınızda hiçbir şeyin değişmediği varsayılarak döndürülen sonuçlar tutarlı ve beklendiği gibi olur, yani **Ad** özelliğine göre düzenlenir ama yine de en iyi beş sonuçla sınırlıdır.

> [!NOTE]
> Sorgu zaten erişiminiz olan bir abonelikten sonuç döndürmezse, `Search-AzGraph` cmdlet 'inin varsayılan bağlamdaki abonelikler olduğunu unutmayın. Varsayılan `(Get-AzContext).Account.ExtendedProperties.Subscriptions` bağlamın bir parçası olan abonelik kimliklerinin listesini görmek için, erişiminiz olan tüm aboneliklerde arama yapmak istiyorsanız, bir tane, `Search-AzGraph` cmdlet 'i çalıştırarak PSDefaultParameterValues değerlerini ayarlayabilir `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak Grafiği modülünü Azure PowerShell ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Bu işlem daha önce indirilmiş modül dosyasını silmez. Yalnızca çalışan PowerShell ortamından kaldırır.

## <a name="next-steps"></a>Sonraki adımlar

- [Sorgu dili](./concepts/query-language.md)hakkında daha fazla bilgi alın.
- [Kaynakları araştırma](./concepts/explore-resources.md)hakkında daha fazla bilgi edinin.
- [Azure Portal](first-query-portal.md)kullanarak ilk sorgunuzu çalıştırın.
- [Azure CLI](first-query-azurecli.md)ile ilk sorgunuzu çalıştırın.
- Bkz. [Başlangıç sorguları](./samples/starter.md)örnekleri.
- [Gelişmiş sorguların](./samples/advanced.md)örneklerine bakın.
- [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)hakkında geri bildirim sağlayın.