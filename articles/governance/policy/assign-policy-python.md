---
title: 'Hızlı başlangıç: Python ile yeni ilke ataması'
description: Bu hızlı başlangıçta, uyumlu olmayan kaynakları belirlemek üzere bir Azure Ilkesi ataması oluşturmak için Python 'u kullanırsınız.
ms.date: 03/02/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c9a0d6bb00d82748505304264aeaefa409c4b06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379411"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Hızlı başlangıç: Python kullanarak uyumlu olmayan kaynakları belirlemek için bir ilke ataması oluşturma

Azure’da uyumluluğu anlamanın ilk adımı, kaynaklarınızın durumunu belirlemektir. Bu hızlı başlangıçta, yönetilen disk kullanmayan sanal makineleri belirlemek için bir ilke ataması oluşturacaksınız. Bu tamamlandığında, _uyumlu olmayan_ sanal makineleri tanımlayacaksınız.

Python kitaplığı, Azure kaynaklarını komut satırından veya betiklerden yönetmek için kullanılır. Bu kılavuzda, bir ilke ataması oluşturmak için Python kitaplığı 'nın nasıl kullanılacağı açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Ilke kitaplığını ekleme

Python 'un Azure Ilkesiyle çalışmasını sağlamak için, kitaplığın eklenmesi gerekir. Bu kitaplık, [Windows 10 ' da Bash](/windows/wsl/install-win10) veya yerel olarak yüklü dahil olmak üzere Python 'un kullanılabileceği her yerde çalışmaktadır.

1. En son Python 'un yüklü olup olmadığını denetleyin (en az **3,8**). Henüz yüklenmemişse, [Python.org](https://www.python.org/downloads/)adresinden indirin.

1. En son Azure CLı 'nın yüklü olup olmadığını denetleyin (en azından **2.5.1**). Henüz yüklenmemişse bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLı, Python 'un aşağıdaki örneklerde **CLI tabanlı kimlik doğrulamasını** kullanmasını sağlamak için gereklidir. Diğer seçenekler hakkında daha fazla bilgi için bkz. [Python Için Azure Yönetim kitaplıklarını kullanarak kimlik doğrulama](/azure/developer/python/azure-sdk-authenticate).

1. Azure CLı aracılığıyla kimlik doğrulaması yapın.

   ```azurecli
   az login
   ```

1. Tercih ettiğiniz Python ortamınızda Azure Ilkesi için gerekli kitaplıkları yükler:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Tüm kullanıcılar için Python yüklüyse, bu komutların yükseltilmiş bir konsoldan çalıştırılması gerekir.

1. Kitaplıkların yüklü olduğunu doğrulayın. `azure-mgmt-policyinsights`**0.5.0** veya üzeri olmalıdır, `azure-mgmt-resource` **9.0.0** veya üzeri olmalıdır ve `azure-cli-core` **2.5.0** veya üzeri olmalıdır.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>İlke ataması oluşturma

Bu hızlı başlangıçta, bir ilke ataması oluşturup yönetilen diskler () tanımını **kullanmayan denetim sanal** makinelerini atarsınız `06a78e20-9358-41c9-923c-fb736d382a4d` . Bu ilke tanımı, ilke tanımında ayarlanan koşullarla uyumlu olmayan kaynakları belirler.

Yeni bir ilke ataması oluşturmak için aşağıdaki kodu çalıştırın:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

Yukarıdaki komutlarda aşağıdaki bilgiler kullanılmaktadır:

Atama ayrıntıları:
- **display_name** -ilke ataması için görünen ad. Bu durumda, _yönetilen disk ataması olmadan VM 'Leri denetleme_ kullanıyorsunuz demektir.
- **policy_definition_id** : atamayı oluşturmak için kullandığınız ilke tanımı yolu. Bu durumda, _yönetilen diskleri kullanmayan ilke tanımı denetim VM_'lerinin kimliğidir. Bu örnekte, ilke tanımı yerleşik bir örnektir ve yol yönetim grubu veya abonelik bilgileri içermez.
- **kapsam** -bir kapsam, ilke atamasının hangi kaynaklarda veya gruplandırmaya uygulanacağını belirler. Bir yönetim grubundan tek bir kaynağa kadar değişebilir. `{scope}`Aşağıdaki desenlerden biriyle değiştirdiğinizden emin olun:
  - Yönetim grubu: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonelik: `/subscriptions/{subscriptionId}`
  - Kaynak grubu: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Kaynak `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **Açıklama** -ilkenin ne yaptığını veya neden bu kapsama atandığını daha derin bir açıklama.

Atama oluşturma:

- Kapsam-bu kapsam, ilke atamasının nereye kaydedileceğini belirler. Atama ayrıntılarında ayarlanan kapsam bu kapsam içinde bulunmalıdır.
- Ad - Atamanın gerçek adı. Bu örnekte _audit-vm-manageddisks_ kullanıldı.
- İlke ataması-önceki adımda oluşturulan Python **PolicyAssignment** nesnesi.

Artık ortamınızın uyumluluk durumunu anlamak için uyumlu olmayan kaynakları belirlemek için hazırsınız demektir.

## <a name="identify-non-compliant-resources"></a>Uyumlu olmayan kaynakları belirleme

Oluşturduğunuz ilke atamasıyla uyumlu olmayan kaynakları belirlemek için aşağıdaki bilgileri kullanın. Aşağıdaki kodu çalıştırın:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

`{subscriptionId}`Bu ilke atamasının uyumluluk sonuçlarını görmek istediğiniz abonelikle değiştirin. Diğer kapsamların listesi ve verileri özetleme yolları için bkz. [Ilke durumu yöntemleri](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

Sonuçlarınız aşağıdaki örneğe benzer:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Sonuçlar, Azure portal görünümündeki bir ilke atamasının **kaynak uyumluluğu** sekmesinde gördüklerinize göre eşleşir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturulan atamayı kaldırmak için aşağıdaki komutu kullanın:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

`{scope}`İlke atamasını oluşturmak için kullandığınız kapsamla değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure ortamınızda uyumlu olmayan kaynakları belirlemek üzere bir ilke tanımı atadınız.

Yeni kaynakların uyumlu olduğunu doğrulamak üzere ilke tanımları atama hakkında daha fazla bilgi edinmek için öğreticiye geçin:

> [!div class="nextstepaction"]
> [İlke oluşturma ve yönetme](./tutorials/create-and-manage.md)
