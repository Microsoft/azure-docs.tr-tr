---
title: Azure Ilkesini kullanarak AKS Izleme eklentisini etkinleştirme
description: Azure özel Ilkesi kullanılarak AKS Izleme eklentisini etkinleştirmeyi açıklar.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713907"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Azure Ilkesini kullanarak AKS izleme eklentisini etkinleştirme
Bu makalede, Azure özel Ilkesi kullanılarak AKS Izleme eklentisi 'nin nasıl etkinleştirileceği açıklanır. İzleme eklentisi özel Ilkesi, abonelik ya da kaynak grubu kapsamında atanabilir. Azure Log Analytics çalışma alanı ve AKS kümesi farklı aboneliklerdeyse, ilke ataması tarafından kullanılan yönetilen kimliğin hem aboneliklerde hem de en azından Log Analytics çalışma alanının kaynağında gerekli rol izinleri olması gerekir. Benzer şekilde, ilke kaynak grubu kapsamınse, çalışma alanı seçili kaynak grubu kapsamında değilse, yönetilen kimliğin Log Analytics çalışma alanında gerekli rol izinlerine sahip olması gerekir.

İzleme eklentisi, Azure Ilkesi tarafından kullanılan yönetilen kimlik üzerinde aşağıdaki rolleri gerektirir:

 - [Azure-Kubernetes-servis katılımcısı-rol](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [Log Analytics-katkıda bulunan](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Azure portal kullanarak ilke tanımı oluşturma ve atama

### <a name="create-policy-definition"></a>İlke tanımı oluştur

1. AKS Izleme eklentisini etkinleştirmek için Azure özel Ilke tanımını indirin.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. İlke https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions tanımı oluştur iletişim kutusunda aşağıdaki ayrıntılarla gidin ve ilke tanımı oluşturun.
 
    - **Tanım konumu**: ilke tanımının depolanacağı Azure aboneliğini seçin.
    - **Ad**: *(Önizleme) aks-Monitoring-addon*
    - **Açıklama**: *belirtilen kapsamda Azure Kubernetes kümesi üzerinde izleme eklentisini etkinleştirmek Için Azure özel ilkesi*
    - **Kategori**: *mevcut olanı kullan* ' ı seçin ve açılan listeden *Kubernetes* ' i seçin.
    - **Ilke kuralı**: Mevcut örnek kuralları kaldırın ve yukarıdaki adım #1 indirilen *azurepolicy.js* içeriğini kopyalayın.

### <a name="assign-policy-definition-to-specified-scope"></a>Belirtilen kapsama ilke tanımı ata

> [!NOTE]
>  Yönetilen kimlik otomatik olarak oluşturulur ve Ilke tanımında belirtilen roller atanır.

1. Az önce oluşturduğunuz ilke tanımı *(Önizleme) AKS Izleme eklentisini* seçin.
4. *Ata** * öğesine tıklayın ve ilkenin atanması gereken bir **kapsam** belirtin. 
5. **İleri** ' ye tıklayın ve Azure Log Analytics çalışma ALANıNıN kaynak kimliğini sağlayın. Kaynak KIMLIĞI bu biçimde olmalıdır `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. Seçili kapsamdaki mevcut AKS kümelerine ilke uygulamak istiyorsanız, bir düzeltme görevi oluşturun.
7. İlke atamasını oluşturmak için **gözden geçir + oluştur** seçeneğine tıklayın.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Azure CLı kullanarak ilke tanımı oluşturma ve atama

### <a name="create-policy-definition"></a>İlke tanımı oluştur

1. Aşağıdaki komutlarla Azure özel ilke tanımı kuralları ve parametreleri dosyalarını indirin:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Aşağıdaki komutla ilke tanımını oluşturun:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Belirtilen kapsama ilke tanımı ata

- Aşağıdaki komutla ilke atamasını oluşturun:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilkesi](../../governance/policy/overview.md)hakkında daha fazla bilgi edinin.
- [Düzeltme güvenliğinin nasıl çalıştığını](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)öğrenin.
- [Kapsayıcı öngörüleri](./container-insights-overview.md)hakkında daha fazla bilgi edinin.
- [Azure CLI](/cli/azure/install-azure-cli)'yı yükler.