---
title: Azure CLı kullanarak ilk sorgunuzu çalıştırma
description: Bu makale, Azure CLI için Kaynak Grafiği uzantısını etkinleştirmek ve ilk sorgunuzu çalıştırmak için gereken adımları incelemenizi sağlar.
ms.date: 10/18/2019
ms.topic: quickstart
ms.openlocfilehash: b74e4314a7755bb067831f955ca02d62485abe4f
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958962"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak ilk kaynak grafik sorgunuzu çalıştırma

Azure Kaynak Grafı’nı kullanmada ilk adım, [Azure CLI](/cli/azure/) uzantısının yüklü olup olmadığını kontrol etmektir. Bu hızlı başlangıç, Azure CLI yüklemenize uzantı ekleme işlemini incelemenizi sağlar. Uzantıyı Azure CLI ile, yerel veya [Azure Cloud Shell](https://shell.azure.com) aracılığıyla yüklü olarak kullanabilirsiniz.

Bu işlemin sonunda, uzantıyı seçtiğiniz Azure CLI yüklemesine eklemiş ve ilk Kaynak Grafı sorgunuzu çalıştırmış olacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="add-the-resource-graph-extension"></a>Kaynak Grafiği uzantısı ekleme

Azure CLI’sinin Azure Kaynak Grafiği’ni sorgulamasını etkinleştirmek için uzantı eklenmelidir. Bu uzantı; [Windows 10 üzerinde bash](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (ikisi de tek başına ve portalın içinde), [Azure CLI Docker resmi](https://hub.docker.com/r/microsoft/azure-cli/) dahil olmak üzere Azure CLI’sinin kullanılabildiği her yerde çalışır veya yerel olarak yüklenir.

1. En son Azure CLI’sinin (en az **2.0.45** sürümü) yüklü olup olmadığını kontrol edin. Henüz yüklenmiş değilse, [bu yönergeleri](/cli/azure/install-azure-cli-windows?view=azure-cli-latest) izleyin.

1. Seçtiğiniz Azure CLI ortamınızda, aşağıdaki komutu kullanarak içeri aktarın:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Uzantının yüklendiğini ve beklenen sürüm (en az **1.0.0**) olduğunu doğrulayın:

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

Azure CLI uzantısının seçtiğiniz ortamınıza eklenmesiyle birlikte şimdi basit bir Kaynak Grafı sorgusu denemenin zamanı geldi. Sorgu ilk beş Azure kaynağını, her kaynağın **Adı** ve **Kaynak Türü** ile birlikte döndürür.

1. İlk Azure Kaynak Grafiği sorgunuzu `graph` uzantısını ve `query` komutunu kullanarak çalıştırın:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Bu sorgu örneği, `order by` gibi bir sıralama değiştirici sağlamadığı için, bu sorgunun birden çok kez çalıştırılması muhtemelen istek başına farklı bir kaynak kümesi sunacaktır.

1. Sorguyu `order by` **Ad** özelliğine güncelleştirin:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu, sorgu sonuçlarını önce sınırlar, sonra düzenler.

1. Sorguyu ilk önce `order by` **Ad** özelliğine ve ardından `limit`’e en iyi beş sonuca güncelleştirin:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Son sorgu birkaç kere çalıştırıldığında, ortamınızda hiçbir şeyin değişmediği varsayılarak döndürülen sonuçlar tutarlı ve beklendiği gibi olur, yani **Ad** özelliğine göre düzenlenir ama yine de en iyi beş sonuçla sınırlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak Grafiği uzantısını Azure CLI ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Sonraki adımlar

- [Sorgu dili](./concepts/query-language.md)hakkında daha fazla bilgi alın.
- [Kaynakları araştırma](./concepts/explore-resources.md)hakkında daha fazla bilgi edinin.
- [Azure Portal](first-query-portal.md)kullanarak ilk sorgunuzu çalıştırın.
- [Azure PowerShell](first-query-powershell.md)ile ilk sorgunuzu çalıştırın.
- Bkz. [Başlangıç sorguları](./samples/starter.md)örnekleri.
- [Gelişmiş sorguların](./samples/advanced.md)örneklerine bakın.
- [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)hakkında geri bildirim sağlayın.