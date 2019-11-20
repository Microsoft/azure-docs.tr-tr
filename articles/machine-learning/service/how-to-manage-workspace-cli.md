---
title: Azure CLı ile çalışma alanları oluşturma
titleSuffix: Azure Machine Learning
description: Yeni bir Azure Machine Learning çalışma alanı oluşturmak için Azure CLı 'nın nasıl kullanılacağını öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/05/2019
ms.openlocfilehash: c7eb18cf2f8868001c1937b03a63c6013e9cbea8
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619883"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLı ile Azure Machine Learning çalışma alanı oluşturma
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Bu makalede, Azure CLı kullanarak Azure Machine Learning çalışma alanı oluşturmayı öğreneceksiniz. Azure CLı, Azure kaynaklarını yönetmeye yönelik komutlar sağlar. CLı 'ya makine öğrenimi uzantısı, Azure Machine Learning kaynaklarıyla çalışmaya yönelik komutlar sağlar.

## <a name="prerequisites"></a>Ön koşullar

* Bir **Azure aboneliği**. Bir tane yoksa, [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* **Yerel ortamınızdan**bu belgedeki CLI komutlarını kullanmak IÇIN [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)gerekir.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)KULLANıYORSANıZ, CLI tarayıcı aracılığıyla erişilir ve bulutta bulunur.

## <a name="connect-the-cli-to-your-azure-subscription"></a>CLı 'yı Azure aboneliğinize bağlama

> [!IMPORTANT]
> Azure Cloud Shell kullanıyorsanız, bu bölümü atlayabilirsiniz. Cloud Shell, Azure aboneliğinizde oturum açmak için kullandığınız hesabı kullanarak sizin için otomatik olarak kimlik doğrular.

CLı 'dan Azure aboneliğinizde kimlik doğrulayabilmeniz için çeşitli yollar vardır. En temel, bir tarayıcı kullanarak etkileşimli olarak kimlik doğrulaması yapmak için kullanılır. Etkileşimli olarak kimlik doğrulaması yapmak için bir komut satırı veya terminali açın ve aşağıdaki komutu kullanın:

```azurecli
az login
```

CLI varsayılan tarayıcınızı açabiliyorsa, tarayıcıyı açar ve oturum açma sayfasını yükler. Aksi takdirde, bir tarayıcı açmanız ve komut satırındaki yönergeleri izlemeniz gerekir. Yönergeler [https://aka.ms/devicelogin](https://aka.ms/devicelogin) göz atmayı ve bir yetkilendirme kodu girmeyi içerir.

Diğer kimlik doğrulama yöntemleri için bkz. [Azure CLI Ile oturum açma](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Machine Learning uzantısını yükler

Machine Learning uzantısını yüklemek için şu komutu kullanın:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Azure Machine Learning çalışma alanı aşağıdaki Azure hizmetlerine veya varlıklara bağımlıdır:

> [!IMPORTANT]
> Mevcut bir Azure hizmeti belirtmezseniz, bir tane, çalışma alanı oluşturma sırasında otomatik olarak oluşturulur. Her zaman bir kaynak grubu belirtmeniz gerekir.

| Hizmet | Var olan bir örneği belirtecek parametre |
| ---- | ---- |
| **Azure Kaynak grubu** | `-g <resource-group-name>`
| **Azure Depolama Hesabı** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Anahtar Kasası.** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Machine Learning çalışma alanı bir kaynak grubu içinde oluşturulmalıdır. Var olan bir kaynak grubunu kullanabilir veya yeni bir tane oluşturabilirsiniz. __Yeni bir kaynak grubu oluşturmak__için aşağıdaki komutu kullanın. `<resource-group-name>`, bu kaynak grubu için kullanılacak adla değiştirin. `<location>`, bu kaynak grubu için kullanılacak Azure bölgesiyle değiştirin:

> [!TIP]
> Azure Machine Learning kullanılabildiği bir bölge seçmelisiniz. Bilgi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Bu komuttan gelen yanıt aşağıdaki JSON ile benzerdir:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Kaynak gruplarıyla çalışma hakkında daha fazla bilgi için bkz. [az Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Gerekli kaynakları otomatik olarak oluştur

__Hizmetlerin otomatik olarak oluşturulduğu__yeni bir çalışma alanı oluşturmak için aşağıdaki komutu kullanın:

> [!TIP]
> Bu bölümdeki komutlar temel bir sürüm çalışma alanı oluşturur. Kurumsal çalışma alanı oluşturmak için, `az ml workspace create` komutuyla `--sku enterprise` anahtarını kullanın. Azure Machine Learning sürümleri hakkında daha fazla bilgi için bkz. [Azure Machine Learning nedir](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Mevcut kaynakları kullan

Mevcut kaynakları kullanan bir çalışma alanı oluşturmak için, kaynakların KIMLIĞINI sağlamanız gerekir. Hizmetlerin KIMLIĞINI almak için aşağıdaki komutları kullanın:

> [!IMPORTANT]
> Var olan tüm kaynakları belirtmeniz gerekmez. Bir veya daha fazla belirtebilirsiniz. Örneğin, var olan bir depolama hesabı belirtebilirsiniz ve çalışma alanı diğer kaynakları oluşturur.

+ **Azure depolama hesabı**: `az storage account show --name <storage-account-name> --query "id"`

    Bu komuttan gelen yanıt aşağıdaki metne benzer ve depolama hesabınızın KIMLIĞIDIR:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure Application Insights**:

    1. Application Insights uzantısını yükler:

        ```bash
        az extension add -n application-insights
        ```

    2. Application Insight Service 'in KIMLIĞINI alın:

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Bu komuttan gelen yanıt aşağıdaki metne benzer ve Application Insights hizmetinizin KIMLIĞIDIR:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: ' az keykasashow--Name < Key-kasa-adı >--Query "ID"

    Bu komuttan gelen yanıt aşağıdaki metne benzer ve anahtar kasanızın KIMLIĞIDIR:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Bu komuttan gelen yanıt aşağıdaki metne benzer ve kapsayıcı kayıt defterinin KIMLIĞIDIR:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Kapsayıcı kayıt defterinde, bir Azure Machine Learning çalışma alanıyla kullanılmadan önce [yönetici hesabının](/azure/container-registry/container-registry-authentication#admin-account) etkinleştirilmiş olması gerekir.

Çalışma alanıyla kullanmak istediğiniz kaynakların kimliklerine sahip olduktan sonra, temel `az workspace create -w <workspace-name> -g <resource-group-name>` komutunu kullanın ve var olan kaynaklar için parametreleri ve KIMLIKLERI ekleyin. Örneğin, aşağıdaki komut, var olan bir kapsayıcı kayıt defteri kullanan bir çalışma alanı oluşturur:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Çalışma alanlarını Listele

Azure aboneliğinizin tüm çalışma alanlarını listelemek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace list
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Daha fazla bilgi için bkz. [az ml çalışma alanı listesi](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) belgeleri.

## <a name="get-workspace-information"></a>Çalışma alanı bilgilerini al

Bir çalışma alanı hakkında bilgi almak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Daha fazla bilgi için bkz. [az ml çalışma alanı belgeleri gösterme](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) .

## <a name="update-a-workspace"></a>Çalışma alanını güncelleştirme

Bir çalışma alanını güncelleştirmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Bu komutun çıktısı aşağıdaki JSON 'a benzerdir:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Daha fazla bilgi için, [az ml çalışma alanı güncelleştirme](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) belgelerine bakın.

## <a name="share-a-workspace-with-another-user"></a>Çalışma alanını başka bir kullanıcıyla paylaşma

Bir çalışma alanını aboneliğinizdeki başka bir kullanıcıyla paylaşmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure Machine Learning ile rol tabanlı erişim denetimi (RBAC) hakkında daha fazla bilgi için bkz. [kullanıcıları ve rolleri yönetme](how-to-assign-roles.md).

Daha fazla bilgi için, [az ml çalışma alanı paylaşma](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) belgelerine bakın.

## <a name="sync-keys-for-dependent-resources"></a>Bağımlı kaynaklar için eşitleme anahtarları

Çalışma alanınız tarafından kullanılan kaynaklardan birinin erişim anahtarlarını değiştirirseniz, yeni anahtarları çalışma alanıyla eşitlemek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Anahtarları değiştirme hakkında daha fazla bilgi için bkz. [depolama erişim anahtarlarını yeniden üretme](how-to-change-storage-access-key.md).

Daha fazla bilgi için, [az ml çalışma alanı eşitleme anahtarları](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) belgelerine bakın.

## <a name="delete-a-workspace"></a>Çalışma alanını silme

Artık gerekli olmadığında bir çalışma alanını silmek için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Çalışma alanını silmek, uygulama öngörülerini, depolama hesabını, anahtar kasasını veya çalışma alanı tarafından kullanılan kapsayıcı kayıt defterini silmez.

Kaynak grubundaki çalışma alanını ve diğer tüm Azure kaynaklarını silen kaynak grubunu da silebilirsiniz. Kaynak grubunu silmek için şu komutu kullanın:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Daha fazla bilgi için bkz. [az ml çalışma alanı silme](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) belgeleri.

## <a name="next-steps"></a>Sonraki adımlar

Machine Learning için Azure CLı uzantısı hakkında daha fazla bilgi için, [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) belgelerine bakın.
