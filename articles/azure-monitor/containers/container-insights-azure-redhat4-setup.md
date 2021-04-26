---
title: Azure Red Hat OpenShift v4. x 'i Container Insights ile yapılandırma | Microsoft Docs
description: Bu makalede, Azure Red Hat OpenShift sürüm 4 veya sonraki sürümlerde barındırılan Azure Izleyici ile bir Kubernetes kümesi için izlemenin nasıl yapılandırılacağı açıklanır.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 11c702d1f46725a12e90a01dc1b38467344a1123
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784650"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Azure Red Hat OpenShift v4. x 'i kapsayıcı öngörüleri ile yapılandırma

Kapsayıcı öngörüleri, Azure Kubernetes hizmeti (AKS) ve AKS motoru kümeleri için zengin bir izleme deneyimi sağlar. Bu makalede, [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) sürüm 4. x üzerinde barındırılan Kubernetes kümelerinin izlenmesini etkinleştirerek benzer bir izleme deneyiminin nasıl elde edileceğini açıklar.

>[!NOTE]
>Azure Red Hat OpenShift desteği şu anda genel önizlemede bir özelliktir.
>

Bu makalede açıklanan desteklenen yöntemleri kullanarak bir veya daha fazla mevcut Azure Red Hat OpenShift v4. x dağıtımı için kapsayıcı öngörülerini etkinleştirebilirsiniz.

Var olan bir küme için [Azure CLI 'da bu Bash betiğini](/cli/azure/openshift#az_openshift_create&preserve-view=true)çalıştırın.

## <a name="supported-and-unsupported-features"></a>Desteklenen ve desteklenmeyen özellikler

Kapsayıcı öngörüleri, [kapsayıcı öngörülerine genel bakış](container-insights-overview.md)bölümünde açıklandığı gibi Azure Red Hat OpenShift v4. x ' i izlemeyi destekler, ancak aşağıdaki özellikler hariç:

- Canlı veriler (Önizleme)
- Küme düğümlerinden ve yığınlardan [ölçümler toplama](container-insights-update-metrics.md) ve bunları Azure izleyici ölçüm veritabanında depolama

## <a name="prerequisites"></a>Önkoşullar

- Azure CLı sürüm 2.0.72 veya üzeri  

- [Helb 3](https://helm.sh/docs/intro/install/) CLI aracı

- [OpenShift CLI](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html) 'nın en son sürümü

- [Bash sürüm 4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) komut satırı aracı

- Bir [Log Analytics çalışma alanı](../logs/design-logs-deployment.md).

    Kapsayıcı öngörüleri [bölgeye göre Azure ürünlerinde](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)listelenen bölgelerde bir Log Analytics çalışma alanını destekler. Kendi çalışma alanınızı oluşturmak için [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)aracılığıyla veya [Azure Portal](../logs/quick-create-workspace.md)aracılığıyla oluşturulabilir.

- Kapsayıcı öngörülerine yönelik özellikleri etkinleştirmek ve erişmek için, Azure aboneliğindeki bir Azure *katılımcısı* rolüne ve kapsayıcı öngörüleri ile yapılandırılmış Log Analytics çalışma alanında bir [*Log Analytics katkıda*](../logs/manage-access.md#manage-access-using-azure-permissions) bulunan rolüne sahip olmanız gerekir.

- İzleme verilerini görüntülemek için, kapsayıcı öngörüleri ile yapılandırılmış Log Analytics çalışma alanında [*Log Analytics okuyucu*](../logs/manage-access.md#manage-access-using-azure-permissions) rolüne sahip olmanız gerekir.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Var olan bir küme için izlemeyi etkinleştir

Azure Red Hat OpenShift sürüm 4 veya sonraki bir küme için izlemeyi etkinleştirmek üzere, sunulan Bash betiği kullanılarak Azure 'da dağıtılan bir küme için izlemeyi etkinleştirmek üzere şunları yapın:

1. Aşağıdaki komutu çalıştırarak Azure'da oturum açın:

    ```azurecli
    az login
    ```

1. Aşağıdaki komutu çalıştırarak kümenizi izleme eklentisi ile yapılandıran betiği yerel bir klasöre indirip kaydedin:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. [Öğretici: Azure Red Hat OpenShift 4 kümesine bağlanma](../../openshift/tutorial-connect-cluster.md)YÖNERGELERINI kullanarak Aro v4 kümesine bağlanın.


### <a name="integrate-with-an-existing-workspace"></a>Mevcut bir çalışma alanıyla tümleştirin

Bu bölümde, daha önce indirdiğiniz Bash betiğini kullanarak kümenizin izlenmesini etkinleştirirsiniz. Mevcut bir Log Analytics çalışma alanıyla tümleştirmek için, parametresi için gereken Log Analytics çalışma alanınızın tam kaynak KIMLIĞINI tanımlayarak başlayın `logAnalyticsWorkspaceResourceId` ve ardından İzleme eklentisini belirtilen çalışma alanına karşı etkinleştirmek için komutunu çalıştırın.

Belirtmek için bir çalışma alanınız yoksa, [varsayılan çalışma alanı Ile tümleştir](#integrate-with-the-default-workspace) bölümüne atlayabilirsiniz ve betiğin sizin için yeni bir çalışma alanı oluşturmasına izin verebilirsiniz.

1. Aşağıdaki komutu çalıştırarak erişiminiz olan tüm abonelikleri listeleyin:

    ```azurecli
    az account list --all -o table
    ```

    Çıktı aşağıdaki gibi görünür:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. **SubscriptionID** değerini kopyalayın.

1. Aşağıdaki komutu çalıştırarak Log Analytics çalışma alanını barındıran aboneliğe geçin:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Aşağıdaki komutu çalıştırarak aboneliklerinizde bulunan çalışma alanlarının listesini varsayılan JSON biçiminde görüntüleyin:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Çıktıda, çalışma alanı adını bulun ve alan **kimliği** altında bu Log Analytics çalışma alanının tam kaynak kimliğini kopyalayın.

1. İzlemeyi etkinleştirmek için aşağıdaki komutu çalıştırın. `azureAroV4ClusterResourceId`Ve parametrelerinin değerlerini değiştirin `logAnalyticsWorkspaceResourceId` .

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Dışarı aktarma komutlarıyla 3 değişken doldurduktan sonra çalıştırmanız gereken komut aşağıda verilmiştir:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

### <a name="integrate-with-the-default-workspace"></a>Varsayılan çalışma alanıyla tümleştirin

Bu bölümde, indirdiğiniz Bash betiğini kullanarak Azure Red Hat OpenShift v4. x kümeniz için izlemeyi etkinleştirirsiniz.

Bu örnekte, var olan bir çalışma alanını önceden oluşturmanız veya belirtmeniz gerekmez. Bu komut, bölge içinde zaten mevcut değilse, küme aboneliğinin varsayılan kaynak grubunda varsayılan bir çalışma alanı oluşturarak işlemi sizin için basitleştirir.

Oluşturulan varsayılan çalışma alanı *defaultworkspace- \<GUID> - \<Region>* biçimindedir.  

Parametresinin değerini değiştirin `azureAroV4ClusterResourceId` .

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Örnek:

' Bash enable-monitoring.sh--Resource-id $azureAroV 4Clusterresourceıd 

İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

### <a name="enable-monitoring-from-the-azure-portal"></a>Azure portal izlemeyi etkinleştir

Kapsayıcı öngörülerinin çoklu küme görünümü, **izlenmeyen kümeler** sekmesi altında izleme özelliği etkinleştirilmemiş olan Azure Red Hat OpenShift kümelerinizi vurgular. Kümenizin yanındaki **Etkinleştir** seçeneği, portaldan izleme ekleme işlemini başlatmaz. Bu makalenin önceki kısımlarında açıklanan adımları izleyerek el ile izlemeyi etkinleştirmek için bu makaleye yönlendirilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol bölmedeki veya giriş sayfasından **Azure izleyici**' yi seçin.

1. **Öngörüler** bölümünde **kapsayıcılar**' ı seçin.

1. **İzleme kapsayıcıları** sayfasında, **izlenmeyen kümeler**' ı seçin.

1. İzlenmeyen kümeler listesinde kümeyi seçin ve ardından **Etkinleştir**' i seçin.

    **Küme türü** sütununda, **Aro** değerini arayarak listedeki sonuçları belirleyebilirsiniz. **Etkinleştir**' i seçtikten sonra bu makaleye yönlendirilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Artık, RedHat OpenShift sürüm 4. x kümenizin ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplamaya yönelik izlemeyi etkinleştirdiniz. kapsayıcı öngörülerini [nasıl kullanacağınızı](container-insights-analyze.md) öğrenin.

- Varsayılan olarak, Kapsayıcılı Aracı, Kuto-System hariç tüm ad alanlarında çalışan tüm kapsayıcıların *stdout* ve *stderr* kapsayıcı günlüklerini toplar. Belirli bir ad alanı veya ad alanına özgü bir kapsayıcı günlüğü koleksiyonunu yapılandırmak için, *Configmap* yapılandırma dosyanız için istediğiniz veri koleksiyonu ayarlarını yapılandırmak üzere [kapsayıcı öngörüleri Aracısı yapılandırmasını](container-insights-agent-config.md) gözden geçirin.

- Kümelediğiniz Prometheus ölçümlerini hurdaya almak ve analiz etmek için bkz. [Prometheus ölçümleri koruması](container-insights-prometheus-integration.md)'nı inceleyin.

- Kapsayıcı öngörülerini kullanarak kümenizi izlemeyi durdurmayı öğrenmek için bkz. [Azure Red Hat OpenShift kümenizi izlemeyi durdurma](./container-insights-optout-openshift-v3.md).
