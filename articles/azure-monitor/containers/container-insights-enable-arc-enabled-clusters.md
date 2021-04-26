---
title: Azure Arc etkin Kubernetes kümelerini izleme
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Izleyicisi 'ni kullanarak Azure Arc etkin Kubernetes kümelerinin ölçümlerini ve günlüklerini toplayın
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443859"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Azure yay etkin Kubernetes kümeleri için Azure Izleyici kapsayıcı öngörüleri

[Azure Izleyici kapsayıcı öngörüleri](container-insights-overview.md) , Azure Arc etkin Kubernetes kümeleri için zengin izleme deneyimi sağlar.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Desteklenen yapılandırmalar

- Azure Izleyici kapsayıcı öngörüleri, canlı veriler (Önizleme) özelliği dışında [genel bakış](container-insights-overview.md) makalesinde açıklandığı gibi, Azure Arc etkinleştirilmiş Kubernetes 'in (Önizleme) izlenmesini destekler. Ayrıca, kullanıcıların [ölçümleri etkinleştirmek](container-insights-update-metrics.md) için [sahip](../../role-based-access-control/built-in-roles.md#owner) izinlerinin olması gerekmez
- `Docker`, `Moby` , ve CRı uyumlu kapsayıcı çalışma zamanları `CRI-O` ve `containerd` .
- Temel kimlik doğrulaması ile kimlik doğrulaması ve giden ara sunucu olmadan giden proxy desteklenir. Güvenilen sertifikaları bekleyen giden ara sunucu şu anda desteklenmiyor.

## <a name="prerequisites"></a>Önkoşullar

- [Genel küme uzantıları belgeleri](../../azure-arc/kubernetes/extensions.md#prerequisites)altında listelenen önkoşulları karşıladık.
- Log Analytics çalışma alanı: Azure Izleyici kapsayıcı öngörüleri, [bölgeye göre Azure ürünleri sayfasında](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)listelenen bölgelerde bir Log Analytics çalışma alanını destekler. [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md)veya [Azure Portal](../logs/quick-create-workspace.md)aracılığıyla kendi çalışma alanınızı oluşturabilirsiniz.
- Azure Arc etkin Kubernetes kaynağını içeren Azure aboneliğinde [katılımcı](../../role-based-access-control/built-in-roles.md#contributor) rolü atamanız gerekir. Log Analytics çalışma alanı farklı bir abonelikte yer alıyorsa, Log Analytics çalışma alanında [katkıda bulunan](../logs/manage-access.md#manage-access-using-azure-permissions) rol ataması Log Analytics gerekir.
- İzleme verilerini görüntülemek için, Log Analytics çalışma alanında [Log Analytics okuyucu](../logs/manage-access.md#manage-access-using-azure-permissions) rolü atamanız gerekir.
- Aşağıdaki uç noktaların, [bir Kubernetes kümesini Azure yaya bağlama](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements)altında belirtilenlerden bahsedilenlere ek olarak, giden erişim için etkinleştirilmesi gerekir.

    | Uç Nokta | Bağlantı noktası |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Arc, Kubernetes kaynağını etkin bir şekilde Azure ABD kamu ortamındaysanız, giden erişim için aşağıdaki uç noktaların etkinleştirilmesi gerekir:

    | Uç Nokta | Bağlantı noktası |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Bu kümede daha önce Azure Izleyici kapsayıcı öngörülerini küme uzantıları olmadan betiği kullanarak dağıttıysanız, bu Helm grafiğini silmek için [burada](container-insights-optout-hybrid.md) listelenen yönergeleri izleyin. Daha sonra Azure Izleyici kapsayıcı öngörüleri için bir küme uzantısı örneği oluşturmaya devam edebilirsiniz.

    >[!NOTE]
    > Azure Izleyici kapsayıcı öngörülerini (Önizleme) dağıtmaya yönelik betik tabanlı sürüm, dağıtımın [küme uzantısı](../../azure-arc/kubernetes/extensions.md) biçimi ile değiştiriliyor. Önceden betik aracılığıyla dağıtılan Azure Izleyici yalnızca 2021 Haziran 'ya kadar desteklenir ve bu nedenle en erken dağıtım kümesi uzantısı biçimine geçirilmesi önerilir.

### <a name="identify-workspace-resource-id"></a>Çalışma alanı kaynak KIMLIĞINI tanımla

Log Analytics çalışma alanının tam Azure Resource Manager tanımlayıcısını bulmak için aşağıdaki komutları çalıştırın. 

1. Aşağıdaki komutu kullanarak erişiminiz olan tüm abonelikleri listeleyin:

    ```azurecli
    az account list --all -o table
    ```

2. Aşağıdaki komutu kullanarak Log Analytics çalışma alanını barındıran aboneliğe geçin:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Aşağıdaki örnek, aboneliklerinizdeki çalışma alanlarının listesini varsayılan JSON biçiminde görüntüler.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Çıktıda, ilgilendiğiniz çalışma alanı adını bulun. `id`Öğesinin alanı, bu Log Analytics çalışma alanının Azure Resource Manager tanımlayıcısını temsil eder.

    >[!TIP]
    > Bu `id` Ayrıca, Log Analytics çalışma alanının *genel bakış* dikey penceresinde Azure Portal aracılığıyla da bulunabilir.

## <a name="create-extension-instance-using-azure-cli"></a>Azure CLı kullanarak uzantı örneği oluşturma

### <a name="option-1---with-default-values"></a>Seçenek 1-varsayılan değerlerle

Bu seçenek aşağıdaki Varsayılanları kullanır:

- Kümenin bölgesine karşılık gelen mevcut varsayılan Log Analytics çalışma alanını oluşturur veya kullanır
- Azure Izleyici kümesi uzantısı için otomatik yükseltme etkinleştirilir

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>Seçenek 2-mevcut Azure Log Analytics çalışma alanı Ile

*Katkıda* buluninizdeki veya daha fazla izin veren bir rol atamasında mevcut bir Azure Log Analytics çalışma alanını kullanabilirsiniz.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>Seçenek 3-Gelişmiş yapılandırma Ile

Varsayılan kaynak isteklerini ve sınırlarını ince ayar istiyorsanız Gelişmiş yapılandırma ayarlarını kullanabilirsiniz:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Kullanılabilir yapılandırma ayarları için [Held grafiğinin kaynak istekleri ve sınırları bölümünü](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) kullanıma alın.

### <a name="option-4---on-azure-stack-edge"></a>Seçenek 4-Azure Stack Edge

Azure Arc etkin Kubernetes kümesi Azure Stack Edge üzerinde ise, özel bir bağlama yolunun `/home/data/docker` kullanılması gerekir.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Oluşturma komutunda yüklenecek uzantının sürümünü açıkça belirtiyorsanız, belirtilen sürümün >= 2.8.2 olduğundan emin olun.

## <a name="create-extension-instance-using-azure-portal"></a>Azure portal kullanarak uzantı örneği oluşturma

>[!IMPORTANT]
>  Azure Izleyicisini Azure Stack Edge üzerinde çalışan bir Kubernetes kümesine dağıtıyorsanız, bu kümeler için özel bir bağlama yolu ayarlanması gerektiğinden, Azure portal seçeneği yerine Azure CLı seçeneğinin izlenmesi gerekir.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Azure Arc etkin Kubernetes kaynak dikey penceresinden ekleme

1. Azure portal, izlemek istediğiniz Kubernetes kümesini seçin.

2. Kaynak dikey penceresinin ' Izleme ' bölümündeki ' Öngörüler (Önizleme) ' öğesini seçin.

3. Ekleme sayfasında, ' Azure Izleyicisini Yapılandır ' düğmesini seçin

4. Artık ölçülerinizi göndermek için [Log Analytics çalışma alanını](../logs/quick-create-workspace.md) seçebilir ve verileri ' ye kaydedebilirsiniz.

5. Azure Izleyici kapsayıcı öngörüleri kümesi uzantısını dağıtmak için ' Yapılandır ' düğmesini seçin.

### <a name="onboarding-from-azure-monitor-blade"></a>Azure Izleyici dikey penceresinden ekleme

1. Azure portal ' Izleyici ' dikey penceresine gidin ve ' Öngörüler ' menüsü altında ' kapsayıcılar ' seçeneğini belirleyin.

2. İzlemeyi etkinleştirebilmeniz gereken Azure Arc etkin Kubernetes kümelerini görüntülemek için ' Izlenmeyen kümeler ' sekmesini seçin.

3. İzlemeyi etkinleştirmek istediğiniz kümenin yanındaki ' etkinleştir ' bağlantısına tıklayın.

4. Log Analytics çalışma alanını seçin ve devam etmek için ' Yapılandır ' düğmesini seçin.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Azure Resource Manager kullanarak uzantı örneği oluşturma

1. Azure Resource Manager şablonu ve parametreyi indir:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. Dosyadaki arc-k8s-azmon-extension-arm-template-params.jsparametre değerlerini güncelleştirin. Azure genel bulutu için, çalışma `opinsights.azure.com` alanı alanının değeri olarak kullanılması gerekir.

3. Azure Izleyici kapsayıcı öngörüleri uzantısı oluşturmak için şablonu dağıtma 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Uzantı örneğini Sil

Aşağıdaki komut yalnızca uzantı örneğini siler, ancak Log Analytics çalışma alanını silmez. Log Analytics kaynaktaki veriler bozulmadan bırakılır.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Bağlantısı kesilen küme
Kümenizin > 48 saat boyunca Azure bağlantısı kesilirse Azure Kaynak grafiğinde kümeniz hakkında bilgi olmaz. Sonuç olarak, Öngörüler dikey penceresi küme durumlarınız hakkında yanlış bilgiler gösterebilir.

## <a name="next-steps"></a>Sonraki adımlar

- İzleme etkinken, Arc etkin Kubernetes kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplamak için, kapsayıcı öngörülerini [nasıl](container-insights-analyze.md) kullanacağınızı öğrenin.

- Varsayılan olarak, Kapsayıcılı Aracı, Kuto-System hariç tüm ad alanlarında çalışan tüm kapsayıcıların stdout/stderr kapsayıcı günlüklerini toplar. Belirli bir ad alanı veya ad alanına özgü kapsayıcı günlüğü koleksiyonunu yapılandırmak için, istenen veri koleksiyonu ayarlarını ConfigMap yapılandırmaları dosyanıza yapılandırmak üzere [kapsayıcı öngörüleri aracı yapılandırmasını](container-insights-agent-config.md) gözden geçirin.

- Kümelediğiniz Prometheus ölçümlerini hurdaya almak ve analiz etmek için bkz. [Prometheus ölçümleri koruması](container-insights-prometheus-integration.md) 'nı inceleyin
