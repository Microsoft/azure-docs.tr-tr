---
title: Azure Kubernetes hizmet kümenizi Izlemeyi durdurma | Microsoft Docs
description: Bu makalede, Azure AKS kümenizi kapsayıcılar için Azure Izleyici ile izlemeyi nasıl sonlandırabileceğiniz açıklanır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 08/19/2019
ms.openlocfilehash: 508bfa9cf7bff0084e7f0644ee5e053e683cb9cf
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554107"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Azure Kubernetes hizmeti 'ni (AKS) kapsayıcılar için Azure Izleyici ile izlemeyi durdurma

AKS kümenizi izlemeyi etkinleştirdikten sonra, artık bunu izlemek istediğinize karar verirseniz kümeyi izlemeyi durdurabilirsiniz. Bu makalede, Azure CLı kullanılarak veya belirtilen Azure Resource Manager şablonlarıyla nasıl yapılacağı gösterilmektedir.  


## <a name="azure-cli"></a>Azure CLI

Kapsayıcılar için Azure Izleyicisini devre dışı bırakmak için [az aks Disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) komutunu kullanın. Komut, aracıyı küme düğümlerinden kaldırır, çözümü veya daha önce toplanan ve Azure Izleyici kaynağınız içinde depolanan verileri kaldırmaz.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Kümeniz için izlemeyi yeniden etkinleştirmek üzere bkz. [Azure CLI kullanarak Izlemeyi etkinleştirme](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager şablonu

, Çözüm kaynaklarını kaynak grubunuzda sürekli olarak ve sürekli olarak kaldırmayı desteklemek için iki Azure Resource Manager şablonudur. Bunlardan biri, izlemeyi durduracak yapılandırmayı belirten bir JSON şablonudur ve diğeri, kümenin dağıtıldığı AKS kümesi kaynak KIMLIĞINI ve kaynak grubunu belirtmek için yapılandırdığınız parametre değerlerini içerir. 

Bir şablon kullanarak kaynak dağıtma kavramı hakkında bilgi sahibi değilseniz, bkz:
* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/resource-group-template-deploy.md)
* [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>Şablonun, kümenin aynı kaynak grubunda dağıtılması gerekir. Bu şablonu kullanırken başka özellikleri veya eklentileri atlarsanız, kümeden kaldırılmasına neden olabilir. Örneğin, kümenizde uygulanan RBAC ilkeleri için *Enablertzya* ya da aks kümesi için Etiketler belirtilmişse, *Aksresourcetagvalues* .  
>

Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.27 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü belirlemek için `az --version` ' ı çalıştırın. Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Şablon oluşturma

1. Aşağıdaki JSON söz dizimini kopyalayıp dosyanıza yapıştırın:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]"
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Bu dosyayı bir yerel klasöre **Optouttemplate. JSON** olarak kaydedin.

3. Aşağıdaki JSON sözdizimini dosyanıza yapıştırın:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. **Aksresourceıd** ve **Aksresourcelocation** değerlerini, seçilen kümenin **Özellikler** sayfasında bulabileceğiniz aks kümesinin değerlerini kullanarak düzenleyin.

    ![Kapsayıcı özellikleri sayfası](media/container-insights-optout/container-properties-page.png)

    **Özellikler** sayfasında, **çalışma alanı kaynak kimliğini**de kopyalayın. Log Analytics çalışma alanını daha sonra silmek istediğinize karar verirseniz bu değer gereklidir. Log Analytics çalışma alanı silindiğinde bu işlemin bir parçası olarak yapılmaz. 

    **Aksresourcetagvalues** değerlerini, aks kümesi için belirtilen varolan etiket değerleriyle eşleşecek şekilde düzenleyin.

5. Bu dosyayı bir yerel klasöre **Optoutparam. JSON** olarak kaydedin.

6. Bu şablonu dağıtmaya hazırsınız. 

### <a name="remove-the-solution-using-azure-cli"></a>Azure CLı kullanarak çözümü kaldırma

Çözümü kaldırmak ve AKS kümenizdeki yapılandırmayı temizlemek için Linux 'ta Azure CLı ile aşağıdaki komutu yürütün.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Tamamlandığında, sonucu içeren aşağıdakine benzer bir ileti döndürülür:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>PowerShell 'i kullanarak çözümü kaldırma

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Çözümü kaldırmak ve AKS kümenizdeki yapılandırmayı temizlemek için şablonu içeren klasörde aşağıdaki PowerShell komutlarını yürütün.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Tamamlandığında, sonucu içeren aşağıdakine benzer bir ileti döndürülür:

```powershell
ProvisioningState       : Succeeded
```

Çalışma alanı yalnızca kümeyi izlemeyi desteklemek için oluşturulduysa ve artık gerekmiyorsa, el ile silmeniz gerekir. Bir çalışma alanının nasıl silineceği konusunda bilgi sahibi değilseniz, bkz. [Azure Portal bir Azure Log Analytics çalışma alanını silme](../../log-analytics/log-analytics-manage-del-workspace.md). Daha önce 4. adımda kopyaladığımız **çalışma alanı kaynak kimliği** hakkında unutmayın, bunun için gerekli olacaktır. 

