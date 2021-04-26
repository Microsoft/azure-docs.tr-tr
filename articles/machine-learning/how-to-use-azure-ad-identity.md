---
title: Web hizmetinizdeki Azure AD kimliğini kullanın
titleSuffix: Azure Machine Learning
description: Puanlama sırasında bulut kaynaklarına erişmek için Azure Kubernetes hizmetindeki Web hizmetinizdeki Azure AD kimliği ' ni kullanın.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b6e6288f125da2a29a8eff56b64f327914f90cb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520481"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure Kubernetes Service'de makine öğrenmesi web hizmetinizle Azure AD kimliğini kullanma

Bu nasıl yapılır, Azure Kubernetes hizmetindeki dağıtılmış makine öğrenimi modelinize bir Azure Active Directory (Azure AD) kimliği atamayı öğrenirsiniz. [Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) projesi, [yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md) ve Kubernetes TEMELLERI kullanarak uygulamaların Azure AD ile güvenli bir şekilde bulut kaynaklarına erişmesini sağlar. Bu, Web hizmetinizin kimlik bilgilerini eklemek veya belirteçleri doğrudan komut dosyanızın içinde yönetmek zorunda kalmadan Azure kaynaklarınıza güvenli bir şekilde erişmesini sağlar `score.py` . Bu makalede, Azure Kubernetes hizmet kümenizde bir Azure kimliği oluşturma ve kurma adımları açıklanmakta ve bu kimlik dağıtılan Web hizmetinize atanır.

## <a name="prerequisites"></a>Önkoşullar

- [Machine Learning hizmeti Için Azure CLI uzantısı](reference-azure-machine-learning-cli.md), [Python için Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)veya [Azure Machine Learning Visual Studio Code uzantısı](tutorial-setup-vscode-extension.md).

- Komutunu kullanarak AKS kümenize erişin `kubectl` . Daha fazla bilgi için bkz [. kümeye bağlanma](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- AKS kümenize dağıtılan bir Azure Machine Learning Web hizmeti.

## <a name="create-and-install-an-azure-identity"></a>Azure kimliği oluşturma ve yüklemeyi

1. AKS kümenizin Kubernetes RBAC özelliğinin etkin olup olmadığını anlamak için aşağıdaki komutu kullanın:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Bu komut, `true` Kubernetes RBAC etkinse bir değer döndürür. Bu değer, bir sonraki adımda kullanılacak komutu belirler.

1. [Azure AD Pod kimliğini](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) aks kümenize yüklemeyin.

1. Azure AD Pod Identity proje sayfasında gösterilen adımları izleyerek [Azure 'da bir kimlik oluşturun](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) .

1. Azure AD Pod Identity proje sayfasında gösterilen adımları izleyerek [AzureIdentity dağıtın](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) .

1. Azure AD Pod Identity proje sayfasında gösterilen adımları izleyerek [AzureIdentityBinding dağıtın](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) .

1. Önceki adımda oluşturulan Azure kimliği AKS kümeniz için aynı düğüm kaynak grubunda değilse, Azure AD Pod Identity proje sayfasında gösterilen [rol atama](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) adımlarını izleyin.

## <a name="assign-azure-identity-to-web-service"></a>Web hizmetine Azure kimliği atama

Aşağıdaki adımlarda, önceki bölümde oluşturulan Azure kimliği kullanılır ve bir **seçici etiketi** aracılığıyla aks Web hizmetinize atanır.

İlk olarak, Azure kimliğini atamak istediğiniz AKS kümenizdeki dağıtımınızın adını ve ad alanını tanımlama. Aşağıdaki komutu çalıştırarak bu bilgileri alabilirsiniz. Ad alanları Azure Machine Learning çalışma alanı adınızın olması ve dağıtım adınızın, portalda gösterilen uç nokta adı olması gerekir.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Dağıtım belirtimini düzenleyerek Azure Identity Selector etiketini dağıtımınıza ekleyin. Seçici değeri, [AzureIdentityBinding dağıtımı](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)için 5. adımda tanımladığınız bir değer olmalıdır.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Azure kimlik Seçicisi etiketini eklemek için dağıtımı düzenleyin. Altında aşağıdaki bölüme gidin `/spec/template/metadata/labels` . Gibi değerler görmeniz gerekir `isazuremlapp: “true”` . Aşağıda gösterildiği gibi AAD-Pod Identity etiketini ekleyin.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Etiketin doğru eklendiğini doğrulamak için aşağıdaki komutu çalıştırın. Ayrıca, yeni oluşturulan pods 'nin durumlarını görmeniz gerekir.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Pod 'ler çalışır duruma getirildikten sonra, bu dağıtımın Web Hizmetleri Azure kaynaklarınıza Azure kimliğiniz üzerinden, kimlik bilgilerini kodunuza eklemek zorunda kalmadan erişebilecek.

## <a name="assign-roles-to-your-azure-identity"></a>Azure kimliğinize roller atama

Diğer Azure kaynaklarına erişmek için [Azure yönetilen kimliğinizi uygun rollerle atayın](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) . Atadığınız rollerin doğru **veri eylemlerine** sahip olduğundan emin olun. Örneğin, genel [okuyucu rolü](../role-based-access-control/built-in-roles.md#reader) , [Depolama Blobu veri okuyucusu rolünün](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Depolama Blobu için okuma izinlerine sahip olur.

## <a name="use-azure-identity-with-your-web-service"></a>Web hizmetinizdeki Azure kimliğini kullanın

AKS kümenize bir model dağıtın. `score.py`Betik, Azure kimliğinizin erişebileceği Azure kaynaklarına işaret eden işlemleri içerebilir. Erişmeye çalıştığınız kaynak için gerekli istemci kitaplığı bağımlılıklarınızı yüklediğinizden emin olun. Aşağıda, Azure kimliğinizi hizmetinize farklı Azure kaynaklarına erişmek için nasıl kullanabileceğinizi gösteren birkaç örnek verilmiştir.

### <a name="access-key-vault-from-your-web-service"></a>Web hizmetinizden erişim Key Vault

**Key Vault** içinde bir gizli dizi Için Azure kimlik okuma erişimi verildiyse, `score.py` aşağıdaki kodu kullanarak buna erişebilirsiniz.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> Bu örnek, DefaultAzureCredential kullanır. Belirli bir erişim ilkesi kullanarak kimlik erişimi vermek için bkz. [Azure CLI kullanarak Key Vault erişim Ilkesi atama](../key-vault/general/assign-access-policy-cli.md).

### <a name="access-blob-from-your-web-service"></a>Web hizmetinizden blob 'a erişin

Azure kimlik 'e bir **Depolama Blobu** içindeki verilere yönelik okuma erişimi verildiyse, `score.py` aşağıdaki kodu kullanarak buna erişebilirsiniz.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Sonraki adımlar

* Python Azure Identity istemci kitaplığını kullanma hakkında daha fazla bilgi için GitHub 'daki [depoya](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) bakın.
* Azure Kubernetes hizmet kümelerine modeller dağıtmaya ilişkin ayrıntılı bir kılavuz için bkz. [nasıl yapılır](how-to-deploy-azure-kubernetes-service.md).