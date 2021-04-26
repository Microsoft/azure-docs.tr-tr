---
title: Azure Red Hat OpenShift v3. x ' i kapsayıcı öngörüleri ile yapılandırma | Microsoft Docs
description: Bu makalede, Azure Red Hat OpenShift sürüm 3 ve üzerinde barındırılan Azure Izleyici ile bir Kubernetes kümesi izlemenin nasıl yapılandırılacağı açıklanır.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a2910655601548f39983547e12460d949901954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784686"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Azure Red Hat OpenShift v3 'i kapsayıcı öngörüleri ile yapılandırma

>[!IMPORTANT]
> Azure Red Hat OpenShift 3,11, 2022 Haziran tarihinde kullanımdan kaldırılacaktır.
>
> Ekim 2020 itibariyle, artık yeni 3,11 kümeleri oluşturamayacak.
> Mevcut 3,11 kümeleri, 2022 Haziran 'a kadar çalışmaya devam eder, ancak bu tarihten sonra daha fazla desteklenmeyecektir.
>
> [Azure Red Hat OpenShift 4 kümesi oluşturmak](../../openshift/tutorial-create-cluster.md)için bu kılavuzu izleyin.
> Belirli sorularınız varsa [lütfen bizimle iletişime geçin](mailto:aro-feedback@microsoft.com).

Kapsayıcı öngörüleri, Azure Kubernetes hizmeti (AKS) ve AKS motoru kümeleri için zengin izleme deneyimi sağlar. Bu makalede, benzer bir izleme deneyimi elde etmek için [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) sürüm 3 ve sürüm 3 ' ün en son desteklenen sürümünde barındırılan Kubernetes kümelerinin izlenmesini nasıl etkinleştireceğinizi açıklanmaktadır.

>[!NOTE]
>Azure Red Hat OpenShift desteği şu anda genel önizlemede bir özelliktir.
>

Azure Red Hat Openshıft 'in yeni veya bir veya daha fazla dağıtımı için, aşağıdaki desteklenen yöntemler kullanılarak kapsayıcı öngörüleri etkinleştirilebilir:

- Azure portal veya Azure Resource Manager şablonu kullanarak var olan bir küme için.
- Azure Resource Manager şablonu kullanan yeni bir küme için veya [Azure CLI](/cli/azure/openshift#az_openshift_create)kullanarak yeni bir küme oluştururken.

## <a name="supported-and-unsupported-features"></a>Desteklenen ve desteklenmeyen özellikler

Kapsayıcı öngörüleri, [genel bakış](container-insights-overview.md) makalesinde açıklandığı gibi Azure Red Hat OpenShift 'in izlenmesini destekler, ancak aşağıdaki özellikler hariç:

- Canlı veriler (Önizleme)
- Küme düğümlerinden ve yığınlardan [ölçümler toplayın](container-insights-update-metrics.md) ve bunları Azure izleyici ölçümleri veritabanında depolarsınız

## <a name="prerequisites"></a>Önkoşullar

- Bir [Log Analytics çalışma alanı](../logs/design-logs-deployment.md).

    Kapsayıcı öngörüleri [bölgeye göre Azure ürünlerinde](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)listelenen bölgelerde bir Log Analytics çalışma alanını destekler. Kendi çalışma alanınızı oluşturmak için [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)aracılığıyla veya [Azure Portal](../logs/quick-create-workspace.md)aracılığıyla oluşturulabilir.

- Kapsayıcı öngörülerine yönelik özellikleri etkinleştirmek ve bu özelliklere erişmek için, en azından Azure aboneliğinde Azure *katkıda* bulunan rolünün bir üyesi olmanız ve kapsayıcı öngörüleri ile yapılandırılmış Log Analytics çalışma alanının [*Log Analytics katkıda*](../logs/manage-access.md#manage-access-using-azure-permissions) bulunan rolünün bir üyesi olmanız gerekir.

- İzleme verilerini görüntülemek için, kapsayıcı öngörüleri ile yapılandırılmış Log Analytics çalışma alanına sahip [*Log Analytics okuyucu*](../logs/manage-access.md#manage-access-using-azure-permissions) rolü izninin bir üyesidir.

## <a name="identify-your-log-analytics-workspace-id"></a>Log Analytics çalışma alanı KIMLIĞINIZI tanımla

 Mevcut bir Log Analytics çalışma alanıyla tümleştirebilmek için, Log Analytics çalışma alanınızın tam kaynak KIMLIĞINI tanımlayarak başlayın. `workspaceResourceId`Azure Resource Manager şablon yöntemi kullanılarak izlemeyi etkinleştirdiğinizde parametresi için çalışma alanının kaynak kimliği gereklidir.

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak yeni küme için etkinleştirme

İzleme özelliği etkinken bir Azure Red Hat OpenShift kümesi dağıtmak için aşağıdaki adımları gerçekleştirin. Devam etmeden önce, ortamınızın doğru şekilde ayarlanabilmesi için yapılandırmanız gereken bağımlılıkları anlamak üzere [Azure Red Hat OpenShift kümesi oluşturma](../../openshift/tutorial-create-cluster.md) öğreticisini gözden geçirin.

Bu yöntem iki JSON şablonu içerir. Bir şablon, izleme etkin olan kümeyi dağıtmaya yönelik yapılandırmayı belirtir ve diğeri aşağıdakileri belirtmek için yapılandırdığınız parametre değerlerini içerir:

- Azure Red Hat OpenShift kümesi kaynak KIMLIĞI.

- Kümenin dağıtıldığı kaynak grubu.

- Azure Active Directory bir veya zaten oluşturulmuş bir oluşturma adımları gerçekleştirildikten sonra belirtilen [KIRACı kimliği](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) .

- Azure Active Directory bir veya zaten oluşturulmuş bir oluşturma adımları gerçekleştirildikten sonra belirtilen [istemci uygulama kimliği](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) .

- Bir veya zaten oluşturulmuş bir oluşturma adımları gerçekleştirildikten sonra belirtilen [istemci parolası Azure Active Directory](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) .

- [Azure AD güvenlik grubu](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) , bir veya zaten oluşturulmuş bir oluşturma adımları gerçekleştirildikten sonra belirtilmiştir.

- Mevcut bir Log Analytics çalışma alanının kaynak KIMLIĞI. Bu bilgileri nasıl alabileceğinizi öğrenmek için [Log Analytics çalışma alanı kimliğinizi belirleme](#identify-your-log-analytics-workspace-id) konusuna bakın.

- Kümede oluşturulacak ana düğüm sayısı.

- Aracı havuzu profilindeki işlem düğümlerinin sayısı.

- Aracı havuzu profilindeki altyapı düğümlerinin sayısı.

Bir şablon kullanarak kaynak dağıtma kavramı hakkında bilgi sahibi değilseniz, bkz:

- [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/templates/deploy-powershell.md)

- [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.65 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü tanımlamak için öğesini çalıştırın `az --version` . Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

1. Aşağıdaki komutları kullanarak izleme eklentisi ile bir küme oluşturmak için bir yerel klasöre, Azure Resource Manager şablonu ve parametre dosyasına indirip kaydedin:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Azure'da oturum açma

    ```azurecli
    az login
    ```

    Birden çok aboneliğe erişiminiz varsa, `az account set -s {subscription ID}` kullanmak istediğiniz abonelikle değiştirmeyi çalıştırın `{subscription ID}` .

3. Henüz bir tane yoksa kümeniz için bir kaynak grubu oluşturun. Azure üzerinde OpenShift 'i destekleyen Azure bölgelerinin listesi için bkz. [Desteklenen bölgeler](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. **newClusterWithMonitoringParam.js** JSON parametre dosyasını düzenleyin ve aşağıdaki değerleri güncelleştirin:

    - *konumuna*
    - *clusterName*
    - *Aadtenantıd*
    - *Aadclientıd*
    - *aadClientSecret*
    - *Aadcustomeradmingroupıd*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *Deniz Ranodecount*

5. Aşağıdaki adım, Azure CLı 'yı kullanarak kümeyi etkin izleme ile dağıtır.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Çıktı aşağıdakine benzer:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Var olan bir küme için etkinleştir

Azure 'da dağıtılan bir Azure Red Hat OpenShift kümesinin izlenmesini etkinleştirmek için aşağıdaki adımları gerçekleştirin. Bunu Azure portal veya belirtilen şablonları kullanarak gerçekleştirebilirsiniz.

### <a name="from-the-azure-portal"></a>Azure portalından

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portal menüsünde veya giriş sayfasından **Azure izleyici**' yi seçin. **Öngörüler** bölümünde **kapsayıcılar**' ı seçin.

3. **İzleyici-kapsayıcılar** sayfasında, **izlenmeyen kümeler**' ı seçin.

4. İzlenmeyen kümeler listesinden, listeden kümeyi bulun ve **Etkinleştir**' e tıklayın. Sütun **KÜMESI türü** altında, **Aro** değerini arayarak listedeki sonuçları belirleyebilirsiniz.

5. Kapsayıcı içgörüleri **ekleme** sayfasında, kümeyle aynı abonelikte bulunan bir Log Analytics çalışma alanınız varsa, bunu aşağı açılan listeden seçin.  
    Liste, varsayılan çalışma alanını ve kümenin abonelikte dağıtıldığı konumu önceden seçer.

    ![İzlenmeyen kümeler için izlemeyi etkinleştir](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >İzleme verilerini kümeden depolamak için yeni bir Log Analytics çalışma alanı oluşturmak istiyorsanız, [Log Analytics çalışma alanı oluşturma](../logs/quick-create-workspace.md)bölümündeki yönergeleri izleyin. Çalışma alanını, RedHat OpenShift kümesinin dağıtıldığı abonelikte oluşturmayı unutmayın.

İzlemeyi etkinleştirdikten sonra, küme için sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir.

### <a name="enable-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak etkinleştir

Bu yöntem iki JSON şablonu içerir. Bir şablon, izlemeyi etkinleştirmek için yapılandırmayı belirtir ve diğeri şunları belirtmek için yapılandırdığınız parametre değerlerini içerir:

- Azure RedHat OpenShift kümesi kaynak KIMLIĞI.

- Kümenin dağıtıldığı kaynak grubu.

- Log Analytics çalışma alanı. Bu bilgileri nasıl alabileceğinizi öğrenmek için [Log Analytics çalışma alanı kimliğinizi belirleme](#identify-your-log-analytics-workspace-id) konusuna bakın.

Bir şablon kullanarak kaynak dağıtma kavramı hakkında bilgi sahibi değilseniz, bkz:

- [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../../azure-resource-manager/templates/deploy-powershell.md)

- [Kaynak Yöneticisi şablonları ve Azure CLı ile kaynak dağıtma](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLı 'yı kullanmayı seçerseniz, önce CLı 'yi yerel olarak yüklemeniz ve kullanmanız gerekir. Azure CLı sürüm 2.0.65 veya üstünü çalıştırıyor olmanız gerekir. Sürümünüzü tanımlamak için öğesini çalıştırın `az --version` . Azure CLı 'yi yüklemeniz veya yükseltmeniz gerekiyorsa bkz. [Azure CLI 'Yı yüklemek](/cli/azure/install-azure-cli).

1. Aşağıdaki komutları kullanarak kümenizi izleme eklentisi ile güncelleştirmek için şablon ve parametre dosyasını indirin:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Azure'da oturum açma

    ```azurecli
    az login
    ```

    Birden çok aboneliğe erişiminiz varsa, `az account set -s {subscription ID}` kullanmak istediğiniz abonelikle değiştirmeyi çalıştırın `{subscription ID}` .

3. Azure RedHat OpenShift kümesi aboneliğini belirtin.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Küme konumunu ve kaynak KIMLIĞINI belirlemek için aşağıdaki komutu çalıştırın:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. **ÜzerindeexistingClusterParam.js** JSON parametre dosyasını düzenleyin ve *aroresourceıd* ve *Aroresourcelocation* değerlerini güncelleştirin. **WorkspaceResourceId** değeri, çalışma alanının adı da dahil olmak üzere Log Analytics çalışma alanınızın tam kaynak kimliğidir.

6. Azure CLı ile dağıtmak için aşağıdaki komutları çalıştırın:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Çıktı aşağıdakine benzer:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Sonraki adımlar

- RedHat OpenShift kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplamaya yönelik izleme özelliği, kapsayıcı öngörülerini [nasıl](container-insights-analyze.md) kullanacağınızı öğrenin.

- Varsayılan olarak, Kapsayıcılı Aracı, Kuto-System hariç tüm ad alanlarında çalışan tüm kapsayıcıların stdout/stderr kapsayıcı günlüklerini toplar. Belirli bir ad alanı veya ad alanına özgü kapsayıcı günlüğü koleksiyonunu yapılandırmak için, istenen veri koleksiyonu ayarlarını ConfigMap yapılandırmaları dosyanıza yapılandırmak üzere [kapsayıcı öngörüleri aracı yapılandırmasını](container-insights-agent-config.md) gözden geçirin.

- Kümelediğiniz Prometheus ölçümlerini hurdaya almak ve analiz etmek için bkz. [Prometheus ölçümleri koruması](container-insights-prometheus-integration.md) 'nı inceleyin

- Kümenizin kapsayıcı öngörüleri ile izlenmesini durdurmayı öğrenmek için bkz. [Azure Red Hat OpenShift kümenizi Izlemeyi durdurma](./container-insights-optout-openshift-v3.md).