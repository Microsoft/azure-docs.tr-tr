---
title: Azure CLı ile çalışma alanları oluşturma
titleSuffix: Azure Machine Learning
description: Yeni bir Azure Machine Learning çalışma alanı oluşturmak için Machine Learning için Azure CLı uzantısını nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 8a00f5474fb73677125b85e48fcc2a42f34fdeb0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876412"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Azure CLı ile Azure Machine Learning çalışma alanı oluşturma


Bu makalede, Azure CLı kullanarak Azure Machine Learning çalışma alanı oluşturmayı öğreneceksiniz. Azure CLı, Azure kaynaklarını yönetmeye yönelik komutlar sağlar. CLı 'ya makine öğrenimi uzantısı, Azure Machine Learning kaynaklarıyla çalışmaya yönelik komutlar sağlar.

## <a name="prerequisites"></a>Önkoşullar

* Bir **Azure aboneliği**. Bir tane yoksa, [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.

* **Yerel ortamınızdan** bu belgedeki CLI komutlarını kullanmak IÇIN [Azure CLI](/cli/azure/install-azure-cli)gerekir.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)KULLANıYORSANıZ, CLI tarayıcı aracılığıyla erişilir ve bulutta bulunur.

## <a name="limitations"></a>Sınırlamalar

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>CLı 'yı Azure aboneliğinize bağlama

> [!IMPORTANT]
> Azure Cloud Shell kullanıyorsanız, bu bölümü atlayabilirsiniz. Cloud Shell, Azure aboneliğinizde oturum açmak için kullandığınız hesabı kullanarak sizin için otomatik olarak kimlik doğrular.

CLı 'dan Azure aboneliğinizde kimlik doğrulayabilmeniz için çeşitli yollar vardır. En basit, bir tarayıcı kullanarak etkileşimli olarak kimlik doğrulaması yapmak için kullanılır. Etkileşimli olarak kimlik doğrulaması yapmak için bir komut satırı veya terminali açın ve aşağıdaki komutu kullanın:

```azurecli-interactive
az login
```

CLI varsayılan tarayıcınızı açabiliyorsa, tarayıcıyı açar ve oturum açma sayfasını yükler. Aksi takdirde, bir tarayıcı açmanız ve komut satırındaki yönergeleri izlemeniz gerekir. Yönergeler, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) bir yetkilendirme koduna göz atmaya ve girmeye yönelik bilgiler içerir.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Diğer kimlik doğrulama yöntemleri için bkz. [Azure CLI Ile oturum açma](/cli/azure/authenticate-azure-cli).

## <a name="create-a-workspace"></a>Çalışma alanı oluşturma

Azure Machine Learning çalışma alanı aşağıdaki Azure hizmetlerine veya varlıklara bağımlıdır:

> [!IMPORTANT]
> Mevcut bir Azure hizmeti belirtmezseniz, bir tane, çalışma alanı oluşturma sırasında otomatik olarak oluşturulur. Her zaman bir kaynak grubu belirtmeniz gerekir. Kendi depolama hesabınızı iliştirirken, aşağıdaki ölçütlere uyduğundan emin olun:
>
> * Depolama hesabı bir Premium hesap _değil_ (Premium_LRS ve Premium_GRS)
> * Azure Blob ve Azure dosya özellikleri etkin
> * Hiyerarşik ad alanı (ADLS Gen 2) devre dışı
>
> Bu gereksinimler yalnızca, çalışma alanı tarafından kullanılan _varsayılan_ depolama hesabı içindir.

| Hizmet | Var olan bir örneği belirtecek parametre |
| ---- | ---- |
| **Azure Kaynak grubu** | `-g <resource-group-name>`
| **Azure depolama hesabı** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) Şu anda kaynak grubu adlarında Unicode karakterleri desteklememektedir. Bu sorunu azaltmak için, bu karakterleri içermeyen bir kaynak grubu kullanın.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Machine Learning çalışma alanı bir kaynak grubu içinde oluşturulmalıdır. Mevcut bir kaynak grubunu kullanabilir ya da yeni bir kaynak grubu oluşturabilirsiniz. __Yeni bir kaynak grubu oluşturmak__ için aşağıdaki komutu kullanın. `<resource-group-name>`Bu kaynak grubu için kullanılacak adla değiştirin. `<location>`Bu kaynak grubu için kullanılacak Azure bölgesiyle değiştirin:

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

Kaynak gruplarıyla çalışma hakkında daha fazla bilgi için bkz. [az Group](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Gerekli kaynakları otomatik olarak oluştur

__Hizmetlerin otomatik olarak oluşturulduğu__ yeni bir çalışma alanı oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Çalışma alanı adı büyük/küçük harfe duyarlıdır.

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

### <a name="virtual-network-and-private-endpoint"></a>Sanal ağ ve özel uç nokta

> [!IMPORTANT]
> Azure Kamu bölgelerinde özel bağlantıyla bir Azure Machine Learning çalışma alanı kullanılması kullanılamaz.

Çalışma alanınıza erişimi bir sanal ağla kısıtlamak istiyorsanız, aşağıdaki parametreleri kullanabilirsiniz:

* `--pe-name`: Oluşturulan özel uç noktanın adı.
* `--pe-auto-approval`: Çalışma alanına özel uç nokta bağlantılarının otomatik olarak onaylanıp onaylanmayacağı.
* `--pe-resource-group`: İçinde özel uç nokta oluşturulacak kaynak grubu. Sanal ağı içeren aynı grup olmalıdır.
* `--pe-vnet-name`: İçinde özel uç nokta oluşturmak için var olan sanal ağ.
* `--pe-subnet-name`: İçinde özel uç nokta oluşturulacak alt ağın adı. `default` varsayılan değerdir.

Çalışma alanınıza özel bir uç nokta ve sanal ağ kullanma hakkında daha fazla bilgi için bkz. [sanal ağ yalıtımı ve gizliliği genel bakış](how-to-network-security-overview.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Müşteri tarafından yönetilen anahtar ve yüksek iş etkisi çalışma alanı

Varsayılan olarak, çalışma alanının meta verileri Microsoft 'un koruduğu bir Azure Cosmos DB örneğine depolanır. Bu veriler, Microsoft tarafından yönetilen anahtarlar kullanılarak şifrelenir.

> [!NOTE]
> Azure Cosmos DB model performansı, denemeleri tarafından günlüğe kaydedilen bilgiler veya model dağıtımlarınızdan günlüğe kaydedilen bilgiler gibi bilgileri depolamak __için kullanılmaz.__ Bu öğeleri izleme hakkında daha fazla bilgi için mimari ve kavramlar makalesinin [izleme ve günlüğe kaydetme](concept-azure-machine-learning-architecture.md) bölümüne bakın.

Microsoft tarafından yönetilen anahtarı kullanmak yerine kendi anahtarınızı sağlayın ' i kullanabilirsiniz. Bunun yapılması, meta verileri Azure aboneliğinizde depolayan Azure Cosmos DB örneğini oluşturur. `--cmk-keyvault`Anahtarı içeren Azure Key Vault belirtmek ve `--resource-cmk-uri` KASADAKI anahtarın URL 'sini belirtmek için parametresini kullanın.

`--cmk-keyvault`Ve parametrelerini kullanmadan önce `--resource-cmk-uri` , önce aşağıdaki eylemleri gerçekleştirmeniz gerekir:

1. __Machine Learning uygulamayı__ (kimlik ve erişim yönetimi 'nde) aboneliğinizde katkıda bulunan izinlerle yetkilendirin.
1. [Müşteri tarafından yönetilen anahtarları yapılandırma](../cosmos-db/how-to-setup-cmk.md) bölümündeki adımları izleyerek şunları yapın:
    * Azure Cosmos DB sağlayıcıyı kaydetme
    * Azure Key Vault oluşturma ve yapılandırma
    * Anahtar oluştur

Azure Cosmos DB örneğini el ile oluşturmanız gerekmez, bir tane, çalışma alanı oluşturma sırasında sizin için oluşturulur. Bu Azure Cosmos DB örneği, bu düzene göre bir ad kullanılarak ayrı bir kaynak grubunda oluşturulacak: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Çalışma alanınızda Microsoft 'un topladığı verileri sınırlandırmak için `--hbi-workspace` parametresini kullanın. 

> [!IMPORTANT]
> Yüksek iş etkisi seçilmesi, yalnızca bir çalışma alanı oluşturulurken yapılabilir. Çalışma alanı oluşturulduktan sonra bu ayarı değiştiremezsiniz.

Müşteri tarafından yönetilen anahtarlar ve yüksek iş etkisi çalışma alanı hakkında daha fazla bilgi için bkz. [Azure Machine Learning Için kuruluş güvenliği](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Mevcut kaynakları kullan

Mevcut kaynakları kullanan bir çalışma alanı oluşturmak için, kaynakların KIMLIĞINI sağlamanız gerekir. Hizmetlerin KIMLIĞINI almak için aşağıdaki komutları kullanın:

> [!IMPORTANT]
> Var olan tüm kaynakları belirtmeniz gerekmez. Bir veya daha fazla belirtebilirsiniz. Örneğin, var olan bir depolama hesabı belirtebilirsiniz ve çalışma alanı diğer kaynakları oluşturur.

+ **Azure depolama hesabı**: `az storage account show --name <storage-account-name> --query "id"`

    Bu komuttan gelen yanıt aşağıdaki metne benzer ve depolama hesabınızın KIMLIĞIDIR:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Mevcut bir Azure Depolama hesabını kullanmak istiyorsanız, bu bir Premium hesap (Premium_LRS ve Premium_GRS) olamaz. Ayrıca hiyerarşik bir ad alanına sahip olamaz (Azure Data Lake Storage 2. ile kullanılır). Çalışma alanının _varsayılan_ depolama hesabıyla Premium Depolama veya hiyerarşik ad alanı desteklenmez. Premium Storage veya sıradüzensel ad alanını _varsayılan olmayan_ depolama hesaplarıyla birlikte kullanabilirsiniz.

+ **Azure Application Insights**:

    1. Application Insights uzantısını yükler:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Application Insight Service 'in KIMLIĞINI alın:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Bu komuttan gelen yanıt aşağıdaki metne benzer ve Application Insights hizmetinizin KIMLIĞIDIR:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Bu komuttan gelen yanıt aşağıdaki metne benzer ve anahtar kasanızın KIMLIĞIDIR:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Bu komuttan gelen yanıt aşağıdaki metne benzer ve kapsayıcı kayıt defterinin KIMLIĞIDIR:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Kapsayıcı kayıt defterinde, bir Azure Machine Learning çalışma alanıyla kullanılmadan önce [yönetici hesabının](../container-registry/container-registry-authentication.md#admin-account) etkinleştirilmiş olması gerekir.

Çalışma alanıyla kullanmak istediğiniz kaynakların kimliklerine sahip olduktan sonra, temel `az workspace create -w <workspace-name> -g <resource-group-name>` komutunu kullanın ve var olan kaynaklar için parametreleri ve kimlikleri ekleyin. Örneğin, aşağıdaki komut, var olan bir kapsayıcı kayıt defteri kullanan bir çalışma alanı oluşturur:

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

Daha fazla bilgi için bkz. [az ml çalışma alanı listesi](/cli/azure/ml/workspace#az_ml_workspace_list) belgeleri.

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

Daha fazla bilgi için bkz. [az ml çalışma alanı belgeleri gösterme](/cli/azure/ml/workspace#az_ml_workspace_show) .

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

Daha fazla bilgi için, [az ml çalışma alanı güncelleştirme](/cli/azure/ml/workspace#az_ml_workspace_update) belgelerine bakın.

## <a name="share-a-workspace-with-another-user"></a>Çalışma alanını başka bir kullanıcıyla paylaşma

Bir çalışma alanını aboneliğinizdeki başka bir kullanıcıyla paylaşmak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Azure Machine Learning ile Azure rol tabanlı erişim denetimi (Azure RBAC) hakkında daha fazla bilgi için bkz. [kullanıcıları ve rolleri yönetme](how-to-assign-roles.md).

Daha fazla bilgi için, [az ml çalışma alanı paylaşma](/cli/azure/ml/workspace#az_ml_workspace_share) belgelerine bakın.

## <a name="sync-keys-for-dependent-resources"></a>Bağımlı kaynaklar için eşitleme anahtarları

Çalışma alanınız tarafından kullanılan kaynaklardan birinin erişim anahtarlarını değiştirirseniz, çalışma alanının yeni anahtarla eşitlenmesi bir saat boyunca zaman alır. Çalışma alanını yeni anahtarları hemen eşitlemeye zorlamak için aşağıdaki komutu kullanın:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Anahtarları değiştirme hakkında daha fazla bilgi için bkz. [depolama erişim anahtarlarını yeniden üretme](how-to-change-storage-access-key.md).

Daha fazla bilgi için, [az ml çalışma alanı eşitleme anahtarları](/cli/azure/ml/workspace#az_ml_workspace_sync-keys) belgelerine bakın.

## <a name="delete-a-workspace"></a>Çalışma alanı silme

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

Daha fazla bilgi için bkz. [az ml çalışma alanı silme](/cli/azure/ml/workspace#az_ml_workspace_delete) belgeleri.

## <a name="troubleshooting"></a>Sorun giderme

### <a name="resource-provider-errors"></a>Kaynak sağlayıcısı hataları

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Çalışma alanı taşınıyor

> [!WARNING]
> Azure Machine Learning çalışma alanınızı farklı bir aboneliğe taşımak veya sahip olunan aboneliğin yeni bir kiracıya taşınması desteklenmez. Bunun yapılması hatalara neden olabilir.

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry silme

Azure Machine Learning çalışma alanı bazı işlemler için Azure Container Registry (ACR) kullanır. İlk kez ihtiyaç duyduğunda, otomatik olarak bir ACR örneği oluşturur.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Sonraki adımlar

Machine Learning için Azure CLı uzantısı hakkında daha fazla bilgi için, [az ml](/cli/azure/ml) belgelerine bakın.