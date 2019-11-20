---
title: Dağıtılmış bir Azure Kubernetes hizmeti (AKS) kümesini izleme | Microsoft Docs
description: Azure Kubernetes hizmeti (AKS) kümesini aboneliğinizde zaten dağıtılan kapsayıcılar için Azure Izleyici ile izlemeyi nasıl etkinleştireceğinizi öğrenin.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 09/12/2019
ms.openlocfilehash: 5d68c343fee5807c430ce42777b988a48b9227f8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73478612"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Azure Kubernetes Service (AKS) kümesinin izlenmesini etkinleştirme zaten dağıtıldı

Bu makalede, aboneliğinizde zaten dağıtılmış olan [Azure Kubernetes hizmetinde](https://docs.microsoft.com/azure/aks/) barındırılan yönetilen Kubernetes kümesini izlemek için kapsayıcılar Için Azure izleyici 'nin nasıl ayarlanacağı açıklanır.

Desteklenen yöntemlerden birini kullanarak zaten dağıtılmış bir AKS kümesinin izlenmesini etkinleştirebilirsiniz:

* Azure CLI
* Terraform
* [Azure Izleyici 'den](#enable-from-azure-monitor-in-the-portal) veya Azure Portal [doğrudan aks kümesinden](#enable-directly-from-aks-cluster-in-the-portal) 
* [Azure Resource Manager şablonuyla](#enable-using-an-azure-resource-manager-template) , Azure PowerShell cmdlet 'ini `New-AzResourceGroupDeployment` veya Azure CLI ile kullanın. 

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın. 

## <a name="enable-using-azure-cli"></a>Azure CLı kullanarak etkinleştirme

Aşağıdaki adım, Azure CLı kullanarak AKS kümenizi izlemeye izin vermez. Bu örnekte, oluşturma veya mevcut bir çalışma alanı belirtmeniz gerekmez. Bu komut, bölgede zaten mevcut değilse AKS küme aboneliğinin varsayılan kaynak grubunda varsayılan bir çalışma alanı oluşturarak işlemi sizin için basitleştirir.  Oluşturulan varsayılan çalışma alanı, *Defaultworkspace-\<guıd >-\<bölgesi >* biçimine benzer.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Çıktı aşağıdakine benzeyecektir:

```azurecli
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Mevcut bir çalışma alanıyla tümleştirin

Mevcut bir çalışma alanıyla tümleştirileceğini tercih ediyorsanız, `--workspace-resource-id` parametresi için gereken Log Analytics çalışma alanınızın tam kaynak KIMLIĞINI belirlemek için aşağıdaki adımları uygulayın ve sonra izleme eklentisini Belirtilen çalışma alanı.  

1. Aşağıdaki komutu kullanarak erişiminiz olan tüm abonelikleri listeleyin:

    ```azurecli
    az account list --all -o table
    ```

    Çıktı aşağıdakine benzeyecektir:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    **SubscriptionID**değerini kopyalayın.

2. Aşağıdaki komutu kullanarak Log Analytics çalışma alanını barındıran aboneliğe geçin:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Aşağıdaki örnek, aboneliklerinizdeki çalışma alanlarının listesini varsayılan JSON biçiminde görüntüler. 

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Çıktıda, çalışma alanı adını bulun ve alan **kimliği**altında bu Log Analytics çalışma alanının tam kaynak kimliğini kopyalayın.
 
4. İzleme eklentisini etkinleştirmek için aşağıdaki komutu çalıştırın, `--workspace-resource-id` parametresi için değeri değiştirin. Dize değeri çift tırnak içinde olmalıdır:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Çıktı aşağıdakine benzeyecektir:

    ```azurecli
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Terrayform kullanarak etkinleştir

1. **Oms_agent** eklenti profilini var olan [azurerm_kubernetes_cluster kaynağına](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) ekleyin

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Terrayform belgelerindeki adımları izleyerek [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) ekleyin.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Portalda Azure Izleyici 'den etkinleştir 

Azure Izleyici Azure portal AKS kümenizi izlemeyi etkinleştirmek için aşağıdakileri yapın:

1. Azure portal, **İzle**' yi seçin. 

2. Listeden **kapsayıcılar** ' ı seçin.

3. **İzleyici-kapsayıcılar** sayfasında, **izlenmeyen kümeler**' ı seçin.

4. İzlenmeyen kümeler listesinden kapsayıcıyı listede bulun ve **Etkinleştir**' e tıklayın.   

5. **Kapsayıcılar Için Azure Izleyicisine ekleme** sayfasında, kümeyle aynı abonelikte mevcut bir Log Analytics çalışma alanınız varsa, bu seçeneği, açılan listeden seçin.  
    Liste, varsayılan çalışma alanını ve AKS kapsayıcısının abonelikte dağıtıldığı konumu önceden seçer. 

    ![AKS kapsayıcı öngörüleri izlemeyi etkinleştir](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >İzleme verilerini kümeden depolamak için yeni bir Log Analytics çalışma alanı oluşturmak istiyorsanız, [Log Analytics çalışma alanı oluşturma](../../azure-monitor/learn/quick-create-workspace.md)bölümündeki yönergeleri izleyin. Çalışma alanını, AKS kapsayıcısının dağıtıldığı aynı abonelikte oluşturmayı unutmayın. 
 
İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir. 

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Portalda AKS kümesinden doğrudan etkinleştirin

Azure portal doğrudan AKS kümelerinizin birinden izlemeyi etkinleştirmek için aşağıdakileri yapın:

1. Azure portalda **Tüm hizmetler**’i seçin. 

2. Kaynak listesinde **kapsayıcılar**yazmaya başlayın.  Liste, girişinizi temel alarak filtreler. 

3. **Kubernetes Hizmetleri**' ni seçin.  

    ![Kubernetes Services bağlantısı](./media/container-insights-onboard/portal-search-containers-01.png)

4. Kapsayıcılar listesinde bir kapsayıcı seçin.

5. Kapsayıcıya Genel Bakış sayfasında, **Izleme kapsayıcıları**' nı seçin.  

6. **Kapsayıcılar Için Azure Izleyicisine ekleme** sayfasında, kümeyle aynı abonelikte var olan bir Log Analytics çalışma alanınız varsa, açılan listeden seçin.  
    Liste, varsayılan çalışma alanını ve AKS kapsayıcısının abonelikte dağıtıldığı konumu önceden seçer. 

    ![AKS kapsayıcı durumu izlemeyi etkinleştir](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >İzleme verilerini kümeden depolamak için yeni bir Log Analytics çalışma alanı oluşturmak istiyorsanız, [Log Analytics çalışma alanı oluşturma](../../azure-monitor/learn/quick-create-workspace.md)bölümündeki yönergeleri izleyin. Çalışma alanını, AKS kapsayıcısının dağıtıldığı aynı abonelikte oluşturmayı unutmayın. 
 
İzlemeyi etkinleştirdikten sonra, küme için işletimsel verileri görüntüleyebilmeniz yaklaşık 15 dakika sürebilir. 

## <a name="enable-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak etkinleştir

Bu yöntem iki JSON şablonu içerir. Bir şablon, izlemeyi etkinleştirmek için yapılandırmayı belirtir ve diğeri şunları belirtmek için yapılandırdığınız parametre değerlerini içerir:

* AKS kapsayıcı kaynak KIMLIĞI. 
* Kümenin dağıtıldığı kaynak grubu.

>[!NOTE]
>Şablonun, kümeyle aynı kaynak grubunda dağıtılması gerekir.
>

Azure PowerShell veya CLı kullanılarak izlemeyi etkinleştirmeden önce Log Analytics çalışma alanı oluşturulmalıdır. Çalışma alanını oluşturmak için [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)aracılığıyla veya [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)aracılığıyla ayarlayabilirsiniz.

Bir şablon kullanarak kaynak dağıtma kavramı hakkında bilgi sahibi değilseniz, bkz:

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/resource-group-template-deploy.md)

* [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.59 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü belirlemek için `az --version`çalıştırın. Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-a-template"></a>Şablon oluşturma ve yürütme

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
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Bu dosyayı yerel bir klasöre **Existingclusterekleme. JSON** olarak kaydedin.

3. Aşağıdaki JSON sözdizimini dosyanıza yapıştırın:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
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

4. AKS kümesi için **aks genel bakış** sayfasındaki değerleri kullanarak **aksresourceıd** ve **Aksresourcelocation** değerlerini düzenleyin. **WorkspaceResourceId** değeri, çalışma alanının adı da dahil olmak üzere Log Analytics çalışma alanınızın tam kaynak kimliğidir. 

    **Aksresourcetagvalues** değerlerini, aks kümesi için belirtilen varolan etiket değerleriyle eşleşecek şekilde düzenleyin.

5. Bu dosyayı yerel bir klasöre **Existingclusterparam. JSON** olarak kaydedin.

6. Bu şablonu dağıtmaya hazırsınız. 

   * Azure PowerShell ile dağıtmak için, şablonu içeren klasörde aşağıdaki komutları kullanın:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       
       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Azure CLı ile dağıtmak için aşağıdaki komutları çalıştırın:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir. 

## <a name="verify-agent-and-solution-deployment"></a>Aracıyı ve çözüm dağıtımını doğrulama

Aracı sürümü *06072018* veya sonraki sürümlerde, hem aracının hem de çözümün başarıyla dağıtıldığını doğrulayabilirsiniz. Aracının önceki sürümleriyle, yalnızca aracı dağıtımını doğrulayabilirsiniz.

### <a name="agent-version-06072018-or-later"></a>Aracı sürüm 06072018 veya üzeri

Aracının başarıyla dağıtıldığını doğrulamak için aşağıdaki komutu çalıştırın. 

```
kubectl get ds omsagent --namespace=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Çözümün dağıtımını doğrulamak için şu komutu çalıştırın:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018 'den önceki aracı sürümü

*06072018* ' den önce yayınlanan Log Analytics Agent sürümünün düzgün şekilde dağıtılmadığını doğrulamak için şu komutu çalıştırın:  

```
kubectl get ds omsagent --namespace=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>CLı ile yapılandırmayı görüntüleme

Çözüm etkin değil veya Not, Log Analytics çalışma alanı RESOURCEID nedir ve kümeyle ilgili Özet ayrıntılar gibi ayrıntıları almak için `aks show` komutunu kullanın.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Birkaç dakika sonra komut tamamlanır ve çözüm hakkında JSON biçimli bilgileri döndürür.  Komutun sonuçları izleme eklentisi profilini göstermelidir ve aşağıdaki örnek çıktıya benzer:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Sonraki adımlar

* Çözümü oluşturmaya çalışırken sorunlarla karşılaşırsanız, [sorun giderme kılavuzunu](container-insights-troubleshoot.md) gözden geçirin

* İzleme etkinken, AKS kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplayıp, kapsayıcılar için Azure Izleyicisini [nasıl kullanacağınızı](container-insights-analyze.md) öğrenin.


