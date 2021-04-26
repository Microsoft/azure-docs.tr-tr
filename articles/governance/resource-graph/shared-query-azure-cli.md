---
title: 'Hızlı başlangıç: Azure CLı ile paylaşılan bir sorgu oluşturma'
description: Bu hızlı başlangıçta, Azure CLı için kaynak grafik uzantısını etkinleştirmek ve paylaşılan bir sorgu oluşturmak için aşağıdaki adımları izleyin.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: 2befc2b6895cd9d2c797fc8ed0e28a27eb6e73e8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870562"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak kaynak grafiği paylaşılan sorgusu oluşturma

Azure [CLI](/cli/azure/) Ile Azure Resource Graph kullanmanın ilk adımı, uzantının yüklü olduğunu denetme. Bu hızlı başlangıç, Azure CLI yüklemenize uzantı ekleme işlemini incelemenizi sağlar. Uzantıyı Azure CLI ile, yerel veya [Azure Cloud Shell](https://shell.azure.com) aracılığıyla yüklü olarak kullanabilirsiniz.

Bu işlemin sonunda, uzantıyı tercih ettiğiniz Azure CLı yüklemesine eklediniz ve kaynak grafiği paylaşılan sorgusu oluşturacaksınız.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Kaynak Grafiği uzantısı ekleme

Azure CLı 'nın Azure Kaynak Graf ile çalışmasını sağlamak için uzantının eklenmesi gerekir. Bu uzantı; [Windows 10 üzerinde bash](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (ikisi de tek başına ve portalın içinde), [Azure CLI Docker resmi](https://hub.docker.com/_/microsoft-azure-cli) dahil olmak üzere Azure CLI’sinin kullanılabildiği her yerde çalışır veya yerel olarak yüklenir.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.8.0**). Henüz yüklenmiş değilse, [bu yönergeleri](/cli/azure/install-azure-cli-windows) izleyin.

1. Tercih ettiğiniz Azure CLı ortamınızda, kaynak grafiği uzantısını aşağıdaki komutla içe aktarmak için [az Extension Add](/cli/azure/extension#az_extension_add) komutunu kullanın:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Uzantının yüklendiğini ve az [extension List](/cli/azure/extension#az_extension_list)ile beklenen sürüm (en az **1.1.0**) olduğunu doğrulayın:

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Kaynak Grafiği paylaşılan sorgusu oluşturma

Azure CLı uzantısı tercih ettiğiniz ortamınıza eklendiğinde, kaynak grafiği paylaşılan sorgusunun zamanı da vardır. Paylaşılan sorgu, Azure Resource Graph Explorer 'da izin vereceğiniz veya çalıştırabileceğiniz bir Azure Resource Manager nesnesidir. Sorgu _konuma_ göre gruplandırılan tüm kaynakların sayısını özetler.

1. Azure Kaynak Grafiği paylaşılan sorgusunu depolamak için [az Group Create](/cli/azure/group#az_group_create) ile bir kaynak grubu oluşturun. Bu kaynak grubunun adı `resource-graph-queries` ve konumu `westus2` .

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Uzantıyı kullanarak Azure Kaynak Grafiği paylaşılan sorgusunu oluşturun `graph` ve [az Graph Shared-Query Create](/cli/azure/graph/shared-query#az_graph_shared_query_create) komutunu kullanın:

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Yeni kaynak grubundaki paylaşılan sorguları listeleyin. [Az Graph Shared-Query List](/cli/azure/graph/shared-query#az_graph_shared_query_list) komutu bir değer dizisi döndürür.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Yalnızca tek bir paylaşılan sorgu sonucu almak için [az Graph Shared-Query Show](/cli/azure/graph/shared-query#az_graph_shared_query_show) komutunu kullanın.

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. `{{shared-query-uri}}`Bir [az Graph Query](/cli/azure/graph#az_graph_query) komutunda SÖZDIZIMI ile Azure CLI 'de paylaşılan sorguyu çalıştırın.
   İlk olarak, `id` Önceki komutun sonucundan alanı kopyalayın `show` . `shared-query-uri`Örnekteki metni alanındaki değerle değiştirin `id` , ancak çevreden `{{` ve karakterlerinden ayrılın `}}` .

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > `{{shared-query-uri}}`Söz dizimi bir **Önizleme** özelliğidir.

Kaynak Grafiği paylaşılan sorgularını bulmanın bir başka yolu da Azure portal. Portalda, "Kaynak Grafiği sorguları" aramak için arama çubuğunu kullanın. Paylaşılan sorguyu seçin. **Genel bakış** sayfasında, **sorgu** sekmesi kayıtlı sorguyu görüntüler. **Düzenle** düğmesi [kaynak Graph Explorer](./first-query-portal.md)'da açılır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak Grafiği paylaşılan sorgusunu, kaynak grubunu ve uzantıyı Azure CLı ortamınızdan kaldırmak isterseniz, aşağıdaki komutları kullanarak bunu yapabilirsiniz:

- [az Graph Shared-Query Delete](/cli/azure/graph/shared-query#az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az Extension Remove](/cli/azure/extension#az_extension_remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak grafiği uzantısını Azure CLı ortamınıza eklediniz ve paylaşılan bir sorgu oluşturdunuz. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)