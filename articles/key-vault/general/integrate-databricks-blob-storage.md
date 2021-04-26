---
title: Azure Databricks ve Azure Key Vault'u kullanarak Azure Blob Depolama'ya erişme
description: Bu öğreticide, Azure Key Vault depolanan bir gizli dizi kullanarak Azure Databricks Azure Blob depolamaya nasıl erişebileceğinizi öğreneceksiniz.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: be1b0da23841b5a63ec044e04a5465e29345f9d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772208"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Öğretici: Azure Databricks ve Azure Key Vault kullanarak Azure Blob depolamaya erişin

Bu öğreticide, Azure Key Vault depolanan bir gizli dizi kullanarak Azure Databricks Azure Blob depolamaya nasıl erişebileceğinizi öğreneceksiniz. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure CLı ile bir depolama hesabı ve BLOB kapsayıcısı oluşturma
> * Key Vault oluşturma ve gizli anahtar ayarlama
> * Azure Databricks çalışma alanı oluşturma ve Key Vault gizli dizi kapsamı ekleme
> * Azure Databricks çalışma alanından blob kapsayıcınıza erişin

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Bu öğreticiye başlamadan önce [Azure CLI](/cli/azure/install-azure-cli-windows)'yı yükleyebilirsiniz.

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Azure CLı ile bir depolama hesabı ve BLOB kapsayıcısı oluşturma

Blob 'ları kullanmak için önce genel amaçlı bir depolama hesabı oluşturmanız gerekir. Bir [kaynak grubunuz](/cli/azure/group#az_group_create)yoksa, komutu çalıştırmadan önce bir tane oluşturun. Aşağıdaki komut, depolama kapsayıcısının meta verilerini oluşturur ve görüntüler. **Kimliği** kopyalayın.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Yukarıdaki komutun konsol çıktısı. Son kullanıcının göreceği KIMLIK için yeşil renkle vurgulanır.](../media/databricks-command-output-1.png)

Blobu karşıya yüklemek üzere bir kapsayıcı oluşturabilmeniz için önce [Depolama Blobu veri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rolünü kendinize atamanız gerekir. Bu örnekte, rol daha önce yaptığınız depolama hesabına atanır.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Rolü depolama hesabına atadığınıza göre, blob 'niz için bir kapsayıcı oluşturabilirsiniz.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Kapsayıcı oluşturulduktan sonra, bu kapsayıcıya bir blob (seçtiğiniz dosya) yükleyebilirsiniz. Bu örnekte, HelloWorld ile bir. txt dosyası karşıya yüklenir.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Kapsayıcının Bu kapsayıcıda olduğunu doğrulamak için kapsayıcıdaki Blobları listeleyin.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Yukarıdaki komutun konsol çıktısı. Yalnızca kapsayıcıda saklanan dosyayı görüntüler.](../media/databricks-command-output-2.png)

Aşağıdaki komutu kullanarak depolama kapsayıcının **KEY1** değerini alın. Değeri aşağı kopyalayın.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Yukarıdaki komutun konsol çıktısı. Key1 değeri yeşil bir kutu içinde vurgulanır.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Key Vault oluşturma ve gizli anahtar ayarlama

Aşağıdaki komutu kullanarak bir Key Vault oluşturacaksınız. Bu komut Key Vault meta verilerini de görüntüler. **Kimliği** ve **Vaulturi**'yi kopyalayın.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![](../media/databricks-command-output-4.png)
 ![ Yukarıdaki komutun görüntü konsolu çıktısı. Hem KIMLIK hem de vaultUri, Kullanıcı tarafından görülebilirlik için yeşil renkle vurgulanır.](../media/databricks-command-output-5.png)

Gizli dizi oluşturmak için aşağıdaki komutu kullanın. Gizli anahtar değerini depolama hesabınızdan **KEY1** değeri olarak ayarlayın.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Azure Databricks çalışma alanı oluşturma ve Key Vault gizli dizi kapsamı ekleme

Bu bölüm komut satırı aracılığıyla tamamlanamaz. Bu [kılavuzu](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope) kullanın. [Azure Portal](https://ms.portal.azure.com/#home) şu şekilde erişmeniz gerekir:

1. Azure Databricks kaynağınızı oluşturma
1. Çalışma alanınızı başlatın
1. Key Vault ile desteklenen gizli dizi kapsamı oluşturma

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Azure Databricks çalışma alanından blob kapsayıcınıza erişin

Bu bölüm komut satırı aracılığıyla tamamlanamaz. Bu [kılavuzu](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks) kullanın. Şunları yapmak için Azure Databricks çalışma alanını kullanmanız gerekir:

1. **Yeni küme** oluştur
1. Yeni bir **Not defteri** oluşturun
1. Python betikteki karşılık gelen alanları doldur
1. Python betiğini çalıştırma

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Sonraki adımlar

Key Vault kurtarılabilir olduğundan emin olun:
> [!div class="nextstepaction"]
> [Kaynaklarınızı temizleme](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)