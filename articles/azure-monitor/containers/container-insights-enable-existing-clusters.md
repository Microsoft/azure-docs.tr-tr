---
title: Dağıtılmış bir Azure Kubernetes hizmeti (AKS) kümesini izleme | Microsoft Docs
description: Aboneliğinizde zaten dağıtılan bir Azure Kubernetes hizmeti (AKS) kümesinin izlenmesini nasıl etkinleştireceğinizi öğrenin.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: fba12fb8dd5c6c91883a88d4acfdca6c6d332c24
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109633"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Azure Kubernetes Service (AKS) kümesinin izlenmesini etkinleştirme zaten dağıtıldı

Bu makalede, aboneliğinizde zaten dağıtılmış olan [Azure Kubernetes hizmetinde](../../aks/index.yml) barındırılan yönetilen Kubernetes kümesini Izlemek için kapsayıcı öngörülerinin nasıl ayarlanacağı açıklanır.

Desteklenen yöntemlerden birini kullanarak zaten dağıtılmış bir AKS kümesinin izlenmesini etkinleştirebilirsiniz:

* Azure CLI’si
* Terraform
* [Azure Izleyici 'den](#enable-from-azure-monitor-in-the-portal) veya Azure Portal [doğrudan aks kümesinden](#enable-directly-from-aks-cluster-in-the-portal)
* [Azure Resource Manager şablonuyla](#enable-using-an-azure-resource-manager-template) , Azure PowerShell cmdlet 'Ini `New-AzResourceGroupDeployment` veya Azure CLI ile birlikte.

Mevcut bir AKS kümesini başka bir abonelikteki Azure Log Analytics çalışma alanına bağlıyorsanız, Microsoft. ContainerService kaynak sağlayıcısı, Log Analytics çalışma alanının oluşturulduğu abonelikte kayıtlı olmalıdır. Daha fazla bilgi için bkz. [kaynak sağlayıcısını kaydetme](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

## <a name="enable-using-azure-cli"></a>Azure CLI’yı kullanarak etkinleştirme

Aşağıdaki adım, Azure CLı kullanarak AKS kümenizi izlemeye izin vermez. Bu örnekte, var olan bir çalışma alanını önceden oluşturmanız veya belirtmeniz gerekmez. Bu komut, bölgede zaten mevcut değilse AKS küme aboneliğinin varsayılan kaynak grubunda varsayılan bir çalışma alanı oluşturarak işlemi sizin için basitleştirir.  Oluşturulan varsayılan çalışma alanı, *defaultworkspace- \<GUID> - \<Region>* biçimine benzer.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Çıktı aşağıdakine benzeyecektir:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Mevcut bir çalışma alanıyla tümleştirin

Mevcut bir çalışma alanıyla tümleştirileceğini tercih ediyorsanız, parametre için gereken Log Analytics çalışma alanınızın tam kaynak KIMLIĞINI belirlemek için aşağıdaki adımları uygulayın `--workspace-resource-id` ve ardından, belirtilen çalışma alanına karşı izleme eklentisini etkinleştirmek için komutunu çalıştırın.

1. Aşağıdaki komutu kullanarak erişiminiz olan tüm abonelikleri listeleyin:

    ```azurecli
    az account list --all -o table
    ```

    Çıktı aşağıdakine benzeyecektir:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    **SubscriptionID** değerini kopyalayın.

2. Aşağıdaki komutu kullanarak Log Analytics çalışma alanını barındıran aboneliğe geçin:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Aşağıdaki örnek, aboneliklerinizdeki çalışma alanlarının listesini varsayılan JSON biçiminde görüntüler.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Çıktıda, çalışma alanı adını bulun ve alan **kimliği** altında bu Log Analytics çalışma alanının tam kaynak kimliğini kopyalayın.

4. İzleme eklentisini etkinleştirmek için, parametresinin değerini değiştirerek aşağıdaki komutu çalıştırın `--workspace-resource-id` . Dize değeri çift tırnak içinde olmalıdır:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    Çıktı aşağıdakine benzeyecektir:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Terrayform kullanarak etkinleştir

1. Mevcut [azurerm_kubernetes_cluster kaynağına](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile) **oms_agent** eklenti profilini ekleyin

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

3. **İzleme kapsayıcıları** sayfasında, **izlenmeyen kümeler**' ı seçin.

4. İzlenmeyen kümeler listesinden, listeden kapsayıcıyı bulun ve **Etkinleştir**' e tıklayın.

5. Kapsayıcı içgörüleri **ekleme** sayfasında, kümeyle aynı abonelikte bulunan bir Log Analytics çalışma alanınız varsa, bunu aşağı açılan listeden seçin.
    Liste, varsayılan çalışma alanını ve AKS kapsayıcısının abonelikte dağıtıldığı konumu önceden seçer.

    ![AKS kapsayıcı öngörüleri izlemeyi etkinleştir](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >İzleme verilerini kümeden depolamak için yeni bir Log Analytics çalışma alanı oluşturmak istiyorsanız, [Log Analytics çalışma alanı oluşturma](../logs/quick-create-workspace.md)bölümündeki yönergeleri izleyin. Çalışma alanını, AKS kapsayıcısının dağıtıldığı aynı abonelikte oluşturmayı unutmayın.

İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Portalda AKS kümesinden doğrudan etkinleştirin

Azure portal doğrudan AKS kümelerinizin birinden izlemeyi etkinleştirmek için aşağıdakileri yapın:

1. Azure portal, **tüm hizmetler**' i seçin.

2. Kaynak listesinde **kapsayıcılar** yazmaya başlayın.  Liste, girişinizi temel alarak filtreler.

3. **Kubernetes Hizmetleri**' ni seçin.
    
4. Kubernetes hizmetleri listesinde bir hizmet seçin.

5. Kubernetes hizmetine genel bakış sayfasında, **izleme-Öngörüler**' i seçin.

6. Kapsayıcı içgörüleri **ekleme** sayfasında, kümeyle aynı abonelikte mevcut bir Log Analytics çalışma alanınız varsa, açılan listeden seçin.
    Liste, varsayılan çalışma alanını ve AKS kapsayıcısının abonelikte dağıtıldığı konumu önceden seçer.

    ![AKS kapsayıcı durumu izlemeyi etkinleştir](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >İzleme verilerini kümeden depolamak için yeni bir Log Analytics çalışma alanı oluşturmak istiyorsanız, [Log Analytics çalışma alanı oluşturma](../logs/quick-create-workspace.md)bölümündeki yönergeleri izleyin. Çalışma alanını, AKS kapsayıcısının dağıtıldığı aynı abonelikte oluşturmayı unutmayın.

İzlemeyi etkinleştirdikten sonra, küme için işletimsel verileri görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

## <a name="enable-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak etkinleştir

Bu yöntem iki JSON şablonu içerir. Bir şablon, izlemeyi etkinleştirmek için yapılandırmayı belirtir ve diğeri şunları belirtmek için yapılandırdığınız parametre değerlerini içerir:

* AKS kapsayıcı kaynak KIMLIĞI.
* Kümenin dağıtıldığı kaynak grubu.

>[!NOTE]
>Şablonun, kümeyle aynı kaynak grubunda dağıtılması gerekir.
>

Azure PowerShell veya CLı kullanılarak izlemeyi etkinleştirmeden önce Log Analytics çalışma alanı oluşturulmalıdır. Çalışma alanını oluşturmak için [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)aracılığıyla veya [Azure Portal](../logs/quick-create-workspace.md)aracılığıyla ayarlayabilirsiniz.

Bir şablon kullanarak kaynak dağıtma kavramı hakkında bilgi sahibi değilseniz, bkz:

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/templates/deploy-powershell.md)

* [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.59 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü tanımlamak için öğesini çalıştırın `az --version` . Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

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

2. Bu dosyayı yerel bir klasöre **existingClusterOnboarding.js** olarak kaydedin.

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

5. Bu dosyayı yerel bir klasöre **existingClusterParam.js** olarak kaydedin.

6. Bu şablonu dağıtmaya hazırsınız.

   * Azure PowerShell ile dağıtmak için, şablonu içeren klasörde aşağıdaki komutları kullanın:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```output
       provisioningState       : Succeeded
       ```

   * Azure CLı ile dağıtmak için aşağıdaki komutları çalıştırın:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az deployment group create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```output
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

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

Kümede Windows Server düğümleri varsa, aracının başarıyla dağıtıldığını doğrulamak için aşağıdaki komutu çalıştırabilirsiniz.

```
kubectl get ds omsagent-win --namespace=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:

```output
User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
```

Çözümün dağıtımını doğrulamak için şu komutu çalıştırın:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:

```output
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

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

## <a name="view-configuration-with-cli"></a>CLı ile yapılandırmayı görüntüleme

Çözümü, `aks show` çözüm etkinleştirilmiş veya değil, Log Analytics çalışma alanı RESOURCEID ve kümeyle ilgili özet ayrıntıları gibi ayrıntıları almak için kullanın.

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Birkaç dakika sonra komut tamamlanır ve çözüm hakkında JSON biçimli bilgileri döndürür.  Komutun sonuçları izleme eklentisi profilini göstermelidir ve aşağıdaki örnek çıktıya benzer:

```output
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

* İzleme etkinken, AKS kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplayıp, kapsayıcı öngörülerini [nasıl kullanacağınızı](container-insights-analyze.md) öğrenin.
