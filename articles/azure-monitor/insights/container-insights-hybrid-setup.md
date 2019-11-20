---
title: Kapsayıcılar için Azure Izleyici ile karma Kubernetes kümelerini yapılandırma | Microsoft Docs
description: Bu makalede, Azure Stack veya başka bir ortamda barındırılan Kubernetes kümelerini izlemek üzere kapsayıcılar için Azure Izleyicisini nasıl yapılandırabileceğiniz açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: d25b9b3bb155dced973d415b396ebfaa4403b011
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514621"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Kapsayıcılar için Azure Izleyici ile karma Kubernetes kümelerini yapılandırma

Kapsayıcılar için Azure Izleyici, Azure 'da barındırılan Azure Kubernetes hizmeti (AKS) ve AKS motoru kümeleri için zengin izleme deneyimi sağlar. Bu makalede, Azure dışında barındırılan Kubernetes kümelerinin izlenmesini etkinleştirme ve benzer bir izleme deneyimi elde etme açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdakilere sahip olduğunuzdan emin olun:

* Log Analytics çalışma alanı.

    Kapsayıcılar için Azure Izleyici, [bölgeye göre Azure ürünlerinde](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)listelenen bölgelerde bir Log Analytics çalışma alanını destekler. Kendi çalışma alanınızı oluşturmak için [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)aracılığıyla veya [Azure Portal](../learn/quick-create-workspace.md)aracılığıyla oluşturulabilir.

    >[!NOTE]
    >Aynı küme adına sahip birden çok kümeyi aynı Log Analytics çalışma alanına izlemenin etkinleştirilmesi desteklenmez. Küme adları benzersiz olmalıdır.
    >

* Kapsayıcı izlemeyi etkinleştirmek için **Log Analytics katkıda bulunan rolünün** bir üyesisiniz. Log Analytics çalışma alanına erişimi denetleme hakkında daha fazla bilgi için bkz. [çalışma alanına erişimi yönetme ve günlük verileri](../platform/manage-access.md)

* Belirtilen Kubernetes kümesine yönelik kapsayıcılar için Azure Izleyici grafiğini eklemek için [Helm istemcisi](https://helm.sh/docs/using_helm/) .

* Linux için Log Analytics aracısının Kapsayıcılı sürümünün Azure Izleyici ile iletişim kurması için aşağıdaki proxy ve güvenlik duvarı yapılandırma bilgileri gereklidir:

    |Aracı Kaynağı|Bağlantı Noktaları |
    |------|---------|   
    |*.ods.opinsights.azure.com |Bağlantı noktası 443 |  
    |*.oms.opinsights.azure.com |Bağlantı noktası 443 |  
    |*.blob.core.windows.net |Bağlantı noktası 443 |  
    |*. dc.services.visualstudio.com |Bağlantı noktası 443 | 

* Kapsayıcılı aracının, performans ölçümlerini toplamak için kümedeki tüm düğümlerde açılmasını `cAdvisor port: 10255` gerekir.

* Kapsayıcılı Aracı, envanter verilerini toplamak için küme içindeki Kubernetes API hizmetiyle iletişim kurmak üzere kapsayıcıda aşağıdaki çevresel değişkenlerin belirtilmesini gerektirir-`KUBERNETES_SERVICE_HOST` ve `KUBERNETES_PORT_443_TCP_PORT`. 

>[!IMPORTANT]
>Karma Kubernetes kümelerini izlemek için desteklenen en düşük aracı sürümü ciprod10182019 veya daha yeni bir sürüm. 

## <a name="supported-configurations"></a>Desteklenen konfigürasyonlar

Kapsayıcılar için Azure Izleyici ile resmi olarak şunlar desteklenir.

- Ortamlar: Azure 'da Kubernetes, AKS motoru ve Azure Stack. Daha fazla bilgi için bkz. [Azure Stack aks motoru](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Kubernetes ve destek ilkesi sürümleri [desteklenen aks](../../aks/supported-kubernetes-versions.md)sürümleriyle aynıdır. 
- Kapsayıcı çalışma zamanı: Docker ve Moby
- Ana ve çalışılan düğümler için Linux işletim sistemi sürümü: Ubuntu (18,04 LTS ve 16,04 LTS)
- Erişim denetimi destekleniyor: Kubernetes RBAC ve RBAC olmayan

## <a name="enable-monitoring"></a>İzlemeyi etkinleştirme

Karma Kubernetes kümesine yönelik kapsayıcılar için Azure Izleyicisini etkinleştirmek, aşağıdaki adımları sırasıyla gerçekleştirmekten oluşur.

1. Log Analytics çalışma alanınızı Container Insights çözümüyle yapılandırın.

2. Log Analytics çalışma alanıyla kapsayıcılar için Azure Izleyicisini HELM grafiğini etkinleştirin.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure Izleyici kapsayıcıları çözümünü ekleme

Çözümü, Azure PowerShell cmdlet 'ini `New-AzResourceGroupDeployment` veya Azure CLı ile kullanarak, sunulan Azure Resource Manager şablonuyla dağıtabilirsiniz.

Bir şablon kullanarak kaynak dağıtma kavramı hakkında bilgi sahibi değilseniz, bkz:

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/resource-group-template-deploy.md)

* [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.59 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü belirlemek için `az --version`çalıştırın. Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Bu yöntem iki JSON şablonu içerir. Bir şablon, izlemeyi etkinleştirmek için yapılandırmayı belirtir ve diğeri şunları belirtmek için yapılandırdığınız parametre değerlerini içerir:

- **workspaceResourceId** -Log Analytics çalışma alanınızın tam kaynak kimliği.
- **workspaceRegion** -çalışma alanının oluşturulduğu bölge, Azure Portal görüntülenirken çalışma alanı özelliklerinde **konum** olarak da adlandırılır.

**Containersolutionparams. JSON** dosyasındaki `workspaceResourceId` parametresi değeri için gereken Log Analytics çalışma alanınızın tam kaynak kimliğini belirlemek için, aşağıdaki adımları uygulayın ve ardından PowerShell cmdlet 'Ini veya Azure CLI komutunu çalıştırarak çözümden.

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

4. Aşağıdaki JSON söz dizimini kopyalayıp dosyanıza yapıştırın:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Bu dosyayı bir yerel klasöre containerSolution. JSON olarak kaydedin.

6. Aşağıdaki JSON sözdizimini dosyanıza yapıştırın:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Adım 3 ' te kopyaladığınız değeri kullanarak **workspaceResourceId** değerlerini düzenleyin ve **WorkspaceRegion** for the Azure CLI komutunu çalıştırdıktan sonra **bölge** değerini kopyalayın [az Monitor Log-Analytics Workspace Show](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list).

8. Bu dosyayı bir yerel klasöre containerSolutionParams. JSON olarak kaydedin.

9. Bu şablonu dağıtmaya hazırsınız. 

   * Azure PowerShell ile dağıtmak için, şablonu içeren klasörde aşağıdaki komutları kullanın:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```
       
       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```
       
       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Azure CLı ile dağıtmak için aşağıdaki komutları çalıştırın:
    
       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```azurecli
       provisioningState       : Succeeded
       ```
     
       İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir. 

## <a name="install-the-chart"></a>Grafiği yükler

HELı grafiğini etkinleştirmek için aşağıdakileri yapın:

1. Aşağıdaki komutu çalıştırarak Azure grafik deposunu yerel listenize ekleyin:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Aşağıdaki komutu çalıştırarak grafiği yüklersiniz:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Log Analytics çalışma alanı Azure Çin 'de ise aşağıdaki komutu çalıştırın:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers 
    ```

    Log Analytics çalışma alanı Azure ABD kamu 'da ise aşağıdaki komutu çalıştırın:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Aracı veri toplamayı yapılandır

Grafik sürümü 1.0.0 ile birlikte, aracı veri toplama ayarları ConfigMap 'ten denetlenir. Aracı veri [toplama ayarları hakkındaki](container-insights-agent-config.md)belgelere bakın. 

Grafiği başarıyla dağıttıktan sonra, karma Kubernetes kümenizin verilerini Azure portal kapsayıcılar için Azure Izleyici 'de gözden geçirebilirsiniz.  

>[!NOTE]
>Giriş gecikmesi, aracıdan Azure Log Analytics çalışma alanında yapılacak beş ila on dakikalık bir yerdedir. Tüm gerekli izleme verileri Azure Izleyici 'de kullanılabilir olana kadar kümenin durumu **veri yok** veya **bilinmiyor** değerini gösterir. 

## <a name="next-steps"></a>Sonraki adımlar

Karma Kubernetes kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplamaya yönelik izleme özelliği, kapsayıcılar için Azure Izleyicisini [nasıl](container-insights-analyze.md) kullanacağınızı öğrenin.
