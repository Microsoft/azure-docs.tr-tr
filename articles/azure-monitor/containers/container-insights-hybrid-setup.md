---
title: Karma Kubernetes kümelerini kapsayıcı öngörüleri ile yapılandırma | Microsoft Docs
description: Bu makalede, Azure Stack veya başka bir ortamda barındırılan Kubernetes kümelerini izlemek için kapsayıcı öngörülerinin nasıl yapılandırılacağı açıklanır.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 90a4c14397df8e70fc8f3d88bc339f826bb1ccc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767032"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Karma Kubernetes kümelerini kapsayıcı öngörüleri ile yapılandırma

Kapsayıcı öngörüleri, Azure 'da barındırılan kendinden yönetilen bir Kubernetes kümesi olan Azure Kubernetes hizmeti (AKS) ve [aks altyapısı](https://github.com/Azure/aks-engine)için zengin izleme deneyimi sağlar. Bu makalede, Azure dışında barındırılan Kubernetes kümelerinin izlenmesini etkinleştirme ve benzer bir izleme deneyimi elde etme açıklanmaktadır.

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

Aşağıdaki konfigürasyonlar, kapsayıcı öngörüleri ile resmi olarak desteklenir. Farklı bir Kubernetes ve işletim sistemi sürümü sürümüne sahipseniz lütfen adresine bir e-posta gönderin askcoin@microsoft.com .

- Lý

    - Şirket içi Kubernetes
    - Azure 'da AKS altyapısı ve Azure Stack. Daha fazla bilgi için bkz. [Azure Stack aks altyapısı](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) sürüm 4 ve üzeri, şirket içi veya diğer bulut ortamları.

- Kubernetes ve destek ilkesi sürümleri [desteklenen aks](../../aks/supported-kubernetes-versions.md)sürümleriyle aynıdır.

- Şu kapsayıcı çalışma zamanları desteklenir: Docker, Moby, ve CRı ile uyumlu çalışma zamanları, örneğin CRı-O ve ContainerD.

- Desteklenen ana ve çalışan düğümleri için Linux işletim sistemi sürümü: Ubuntu (18,04 LTS ve 16,04 LTS) ve CoreOS 43,81 Red Hat Enterprise Linux.

- Erişim denetimi destekleniyor: Kubernetes RBAC ve RBAC olmayan

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdakilere sahip olduğunuzdan emin olun:

- Bir [Log Analytics çalışma alanı](../logs/design-logs-deployment.md).

    Kapsayıcı öngörüleri [bölgeye göre Azure ürünlerinde](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)listelenen bölgelerde bir Log Analytics çalışma alanını destekler. Kendi çalışma alanınızı oluşturmak için [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)aracılığıyla veya [Azure Portal](../logs/quick-create-workspace.md)aracılığıyla oluşturulabilir.

    >[!NOTE]
    >Aynı küme adına sahip birden çok kümeyi aynı Log Analytics çalışma alanına izlemenin etkinleştirilmesi desteklenmez. Küme adları benzersiz olmalıdır.
    >

- Kapsayıcı izlemeyi etkinleştirmek için **Log Analytics katkıda bulunan rolünün** bir üyesisiniz. Log Analytics çalışma alanına erişimi denetleme hakkında daha fazla bilgi için bkz. [çalışma alanına erişimi yönetme ve günlük verileri](../logs/manage-access.md).

- İzleme verilerini görüntülemek için, kapsayıcı öngörüleri ile yapılandırılmış Log Analytics çalışma alanında [*Log Analytics okuyucu*](../logs/manage-access.md#manage-access-using-azure-permissions) rolüne sahip olmanız gerekir.

- Belirtilen Kubernetes kümesi için kapsayıcı öngörüleri grafiğini eklemek için [helk istemcisi](https://helm.sh/docs/using_helm/) .

- Linux için Log Analytics aracısının Kapsayıcılı sürümünün Azure Izleyici ile iletişim kurması için aşağıdaki proxy ve güvenlik duvarı yapılandırma bilgileri gereklidir:

    |Aracı Kaynağı|Bağlantı noktaları |
    |------|---------|
    |*.ods.opinsights.azure.com |Bağlantı noktası 443 |
    |*.oms.opinsights.azure.com |Bağlantı noktası 443 |
    |*. dc.services.visualstudio.com |Bağlantı noktası 443 |

- Kapsayıcılı Aracı, `cAdvisor secure port: 10250` `unsecure port :10255` performans ölçümlerini toplamak için, kümedeki tüm düğümlerde Kubelet 'in ve açık olmasını gerektirir. `secure port: 10250`Zaten yapılandırılmamışsa Kubelet 'In Cadvizörü üzerinde yapılandırmanızı öneririz.

- Kapsayıcılı Aracı, envanter verilerini toplamak için kümedeki Kubernetes API hizmetiyle iletişim kurmak üzere kapsayıcıda aşağıdaki çevresel değişkenlerin belirtilmesini gerektirir- `KUBERNETES_SERVICE_HOST` ve `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>Karma Kubernetes kümelerini izlemek için desteklenen en düşük aracı sürümü ciprod10182019 veya daha yeni bir sürüm.

## <a name="enable-monitoring"></a>İzlemeyi etkinleştirme

Karma Kubernetes kümesi için kapsayıcı öngörülerinin etkinleştirilmesi, aşağıdaki adımları sırasıyla gerçekleştirmekten oluşur.

1. Log Analytics çalışma alanınızı Container Insights çözümüyle yapılandırın.   

2. Log Analytics çalışma alanıyla kapsayıcı öngörüleri HELB grafiğini etkinleştirin.

Azure Izleyici 'de çözümleri Izleme hakkında daha fazla bilgi için [buraya](../../azure-monitor/insights/solutions.md)bakın.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure Izleyici kapsayıcıları çözümünü ekleme

Çözümü, Azure PowerShell cmdlet 'ini `New-AzResourceGroupDeployment` veya Azure CLI ile birlikte, belirtilen Azure Resource Manager şablonuyla dağıtabilirsiniz.

Bir şablon kullanarak kaynak dağıtma kavramı hakkında bilgi sahibi değilseniz, bkz:

- [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/templates/deploy-powershell.md)

- [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.59 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü tanımlamak için öğesini çalıştırın `az --version` . Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

Bu yöntem iki JSON şablonu içerir. Bir şablon, izlemeyi etkinleştirmek için yapılandırmayı belirtir ve diğeri şunları belirtmek için yapılandırdığınız parametre değerlerini içerir:

- **workspaceResourceId** -Log Analytics çalışma alanınızın tam kaynak kimliği.
- **workspaceRegion** -çalışma alanının oluşturulduğu bölge, Azure Portal görüntülenirken çalışma alanı özelliklerinde **konum** olarak da adlandırılır.

DosyacontainerSolutionParams.jsiçindeki parametre değeri için gereken Log Analytics çalışma alanınızın tam kaynak KIMLIĞINI belirlemek için `workspaceResourceId` , aşağıdaki adımları  uygulayın ve ardından çözümü eklemek için PowerShell cmdlet 'INI veya Azure CLI komutunu çalıştırın.

1. Aşağıdaki komutu kullanarak erişiminiz olan tüm abonelikleri listeleyin:

    ```azurecli
    az account list --all -o table
    ```

    Çıktı aşağıdakine benzeyecektir:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
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

5. Bu dosyayı yerel bir klasöre containerSolution.jsolarak kaydedin.

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

7. Adım 3 ' te kopyaladığınız değeri kullanarak **workspaceResourceId** değerlerini düzenleyin ve **WorkspaceRegion** for the Azure CLI komutunu çalıştırdıktan sonra **bölge** değerini kopyalayın [az Monitor Log-Analytics Workspace Show](/cli/azure/monitor/log-analytics/workspace#az_monitor-log-analytics-workspace-list&preserve-view=true).

8. Bu dosyayı yerel bir klasöre containerSolutionParams.jsolarak kaydedin.

9. Bu şablonu dağıtmaya hazırsınız.

   - Azure PowerShell ile dağıtmak için, şablonu içeren klasörde aşağıdaki komutları kullanın:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Azure CLı ile dağıtmak için aşağıdaki komutları çalıştırın:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Yapılandırma değişikliğinin tamamlanması birkaç dakika sürebilir. Bu tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonuç şunu içerir:

       ```azurecli
       provisioningState       : Succeeded
       ```

       İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

## <a name="install-the-helm-chart"></a>Held grafiğini yükler

Bu bölümde kapsayıcı öngörüleri için Kapsayıcılı aracıyı yüklersiniz. Devam etmeden önce, parametresi için gereken çalışma alanı KIMLIĞINI `omsagent.secret.wsid` ve parametresi için gereken birincil anahtarı belirlemeniz gerekir `omsagent.secret.key` . Aşağıdaki adımları gerçekleştirerek bu bilgileri tanımlayabilir ve ardından hele grafiğini kullanarak aracıyı yüklemek için komutları çalıştırın.

1. Çalışma alanı KIMLIĞINI tanımlamak için aşağıdaki komutu çalıştırın:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Çıktıda, alan **adı** altında çalışma alanı adını bulun ve sonra bu Log Analytics çalışma alanının çalışma alanı kimliğini **CustomerID** alanı altında kopyalayın.

2. Çalışma alanı için birincil anahtarı tanımlamak üzere aşağıdaki komutu çalıştırın:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Çıktıda, **Primarysharedkey** alanının altındaki birincil anahtarı bulun ve değeri kopyalayın.

>[!NOTE]
>Aşağıdaki komutlar yalnızca Held sürüm 2 için geçerlidir. `--name`Parametresinin kullanımı, Held sürüm 3 ile geçerli değildir. 

>[!NOTE]
>Kubernetes kümeniz bir ara sunucu üzerinden iletişim kuruyorsa, parametreyi `omsagent.proxy` proxy sunucusunun URL 'siyle yapılandırın. Küme bir ara sunucu üzerinden iletişim kurmazsanız bu parametreyi belirtmeniz gerekmez. Daha fazla bilgi için bu makalenin ilerleyen kısımlarında [proxy uç noktasını yapılandırma](#configure-proxy-endpoint) konusuna bakın.

3. Aşağıdaki komutu çalıştırarak Azure grafik deposunu yerel listenize ekleyin:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Aşağıdaki komutu çalıştırarak grafiği yüklersiniz:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Log Analytics çalışma alanı Azure Çin 21Vianet ' de ise aşağıdaki komutu çalıştırın:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Log Analytics çalışma alanı Azure ABD kamu 'da ise aşağıdaki komutu çalıştırın:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>API modelini kullanarak Held grafiğini etkinleştirme

AKS motoru küme belirtimi json dosyasında API modeli olarak da adlandırılan bir eklenti belirtebilirsiniz. Bu eklenti içinde, `WorkspaceGUID` `WorkspaceKey` toplanan izleme verilerinin depolandığı Log Analytics çalışma alanının Base64 kodlamalı sürümünü sağlayın. `WorkspaceGUID` `WorkspaceKey` Önceki bölümde 1 ve 2. adımları kullanarak ve ' i bulabilirsiniz.

Azure Stack hub kümesi için desteklenen API tanımları, bu örnekte [kubernetes-container-monitoring_existing_workspace_id_and_key.js](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json)bulunabilir. Özellikle, **Kubernetesconfig** içinde **addons** özelliğini bulabilirsiniz:

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Aracı veri toplamayı yapılandır

Grafik sürümü 1.0.0 ile birlikte, aracı veri toplama ayarları ConfigMap 'ten denetlenir. Aracı veri [toplama ayarları hakkındaki](container-insights-agent-config.md)belgelere bakın.

Grafiği başarıyla dağıttıktan sonra, karma Kubernetes kümenizdeki verileri Azure portal kapsayıcı öngörülerine göre gözden geçirebilirsiniz.  

>[!NOTE]
>Giriş gecikmesi, aracıdan Azure Log Analytics çalışma alanında yapılacak beş ila on dakikalık bir yerdedir. Tüm gerekli izleme verileri Azure Izleyici 'de kullanılabilir olana kadar kümenin durumu **veri yok** veya **bilinmiyor** değerini gösterir.

## <a name="configure-proxy-endpoint"></a>Proxy uç noktasını yapılandırma

Grafik sürümü 2.7.1 ile başlayarak grafik, ara uç noktasının grafik parametresiyle belirtilmesini destekleyecektir `omsagent.proxy` . Bu, proxy sunucunuz üzerinden iletişim kurmasını sağlar. Kapsayıcı içgörüler Aracısı ve Azure Izleyici arasındaki iletişim bir HTTP veya HTTPS proxy sunucusu olabilir ve hem anonim hem de temel kimlik doğrulaması (Kullanıcı adı/parola) desteklenir.

Ara sunucu yapılandırma değeri aşağıdaki sözdizimine sahiptir: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Proxy sunucunuz kimlik doğrulaması gerektirmiyorsa, yine de bir psuedo Kullanıcı adı/parolası belirtmeniz gerekir. Bu, herhangi bir Kullanıcı adı veya parola olabilir.

|Özellik| Açıklama |
|--------|-------------|
|Protokol | http veya https |
|kullanıcı | Proxy kimlik doğrulaması için isteğe bağlı Kullanıcı adı |
|password | Proxy kimlik doğrulaması için isteğe bağlı parola |
|proxyhost | Proxy sunucusunun adresi veya FQDN 'si |
|port | Proxy sunucusu için isteğe bağlı bağlantı noktası numarası |

Örnek: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Protokolü **http** olarak BELIRTIRSENIZ, http istekleri SSL/TLS güvenli bağlantı kullanılarak oluşturulur. Ara sunucunuzun SSL/TLS protokollerini desteklemesi gerekir.

## <a name="troubleshooting"></a>Sorun giderme

Karma Kubernetes kümeniz için izlemeyi etkinleştirmeye çalışırken bir hatayla karşılaşırsanız, PowerShell betiği [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) kopyalayın ve bilgisayarınızdaki bir klasöre kaydedin. Bu betik, karşılaşılan sorunları algılamaya ve gidermeye yardımcı olmak için sağlanır. Algılama ve düzeltme girişimi için tasarlanan sorunlar şunlardır:

- Belirtilen Log Analytics çalışma alanı geçerli
- Log Analytics çalışma alanı, kapsayıcı öngörüleri çözümü ile yapılandırılır. Aksi takdirde, çalışma alanını yapılandırın.
- Omsagent replicaset Pod çalışıyor
- Omsagent daemonset Pod çalışıyor
- OmsAgent Health hizmeti çalışıyor
- Kapsayıcılı aracıda yapılandırılan Log Analytics çalışma alanı KIMLIĞI ve anahtarı, Insight ile yapılandırılan çalışma alanıyla eşleşir.
- Tüm Linux çalışan düğümlerinin `kubernetes.io/role=agent` RS Pod zamanlamak için etiketine sahip olduğunu doğrulayın. Mevcut değilse, ekleyin.
- `cAdvisor secure port:10250` `unsecure port: 10255` Kümedeki tüm düğümlerde doğrulayın veya açılır.

Azure PowerShell ile yürütmek için, komut dosyasını içeren klasörde aşağıdaki komutları kullanın:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Sonraki adımlar

Karma Kubernetes kümenizin ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplamaya yönelik izleme özelliği, kapsayıcı öngörülerini [nasıl kullanacağınızı](container-insights-analyze.md) öğrenin.