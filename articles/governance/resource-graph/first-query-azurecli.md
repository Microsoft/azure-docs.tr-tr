---
title: 'Hızlı başlangıç: ilk Azure CLı sorgunuz'
description: Bu hızlı başlangıçta, Azure CLı için kaynak grafik uzantısını etkinleştirmek ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5111f59eb760afda4e206837ca5bdf8bcc201338
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917850"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak ilk kaynak grafik sorgunuzu çalıştırma

Azure Kaynak Grafı’nı kullanmada ilk adım, [Azure CLI](/cli/azure/) uzantısının yüklü olup olmadığını kontrol etmektir. Bu hızlı başlangıç, Azure CLI yüklemenize uzantı ekleme işlemini incelemenizi sağlar. Uzantıyı Azure CLI ile, yerel veya [Azure Cloud Shell](https://shell.azure.com) aracılığıyla yüklü olarak kullanabilirsiniz.

Bu işlemin sonunda, uzantıyı seçtiğiniz Azure CLI yüklemesine eklemiş ve ilk Kaynak Grafı sorgunuzu çalıştırmış olacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Kaynak Grafiği uzantısı ekleme

Azure CLI’sinin Azure Kaynak Grafiği’ni sorgulamasını etkinleştirmek için uzantı eklenmelidir. Bu uzantı; [Windows 10 üzerinde bash](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (ikisi de tek başına ve portalın içinde), [Azure CLI Docker resmi](https://hub.docker.com/_/microsoft-azure-cli) dahil olmak üzere Azure CLI’sinin kullanılabildiği her yerde çalışır veya yerel olarak yüklenir.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.0.76**). Henüz yüklenmiş değilse, [bu yönergeleri](/cli/azure/install-azure-cli-windows) izleyin.

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

1. Sorguyu `order by`**Ad** özelliğine güncelleştirin:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. Sorguyu ilk önce `order by`**Ad** özelliğine ve ardından `limit`’e en iyi beş sonuca güncelleştirin:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak Grafiği uzantısını Azure CLI ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak grafiği uzantısını Azure CLı ortamınıza eklediniz ve ilk sorgunuzu çalıştırmalısınız. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)
