---
title: Kubernetes için Azure Defender ile karma ve çoklu bulut Kubernetes dağıtımlarını koruyun
description: Şirket içi ve çoklu bulut Kubernetes kümelerinizle Kubernetes için Azure Defender 'ı kullanma
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029208"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Azure Arc, şirket içi ve çoklu bulut ortamlarında çalışan Kubernetes kümelerini etkinleştirdi

**Kubernetes kümeleri uzantısı Için Azure Defender** , Azure Kubernetes hizmet kümeleri için sunulan aynı tehdit algılama özelliklerine sahip şirket içi kümelerinizi savunmasına imkan sağlayabilir. Kümelerinizde [Azure Arc etkin Kubernetes](../azure-arc/kubernetes/overview.md) 'i etkinleştirin ve uzantıyı bu sayfada açıklandığı şekilde dağıtın. 

Uzantı Ayrıca, diğer bulut sağlayıcılarındaki Kubernetes kümelerini, yönetilen Kubernetes hizmetlerinde olmasa da koruyabilir.

> [!TIP]
> Yükleme [örneklerinde GitHub 'daki](https://aka.ms/kubernetes-extension-installation-examples)yükleme işleminde yardımcı olacak bazı örnek dosyalar yerleştirdik.

## <a name="availability"></a>Kullanılabilirlik

| Görünüş | Ayrıntılar |
|--------|---------|
| Yayın durumu | **Önizleme**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Gerekli roller ve izinler | [Güvenlik Yöneticisi](../role-based-access-control/built-in-roles.md#security-admin) , uyarıları kapatabilir<br>[Güvenlik okuyucusu](../role-based-access-control/built-in-roles.md#security-reader) bulguları görüntüleyebilir |
| Fiyatlandırma | [Kubernetes Için Azure Defender](defender-for-kubernetes-introduction.md) gerektirir |
| Desteklenen Kubernetes dağıtımları | [Azure Stack CI üzerinde Azure Kubernetes hizmeti](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS Engine](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (sürüm 4,6 veya üzeri) |
| Sınırlamalar | Azure Arc etkin Kubernetes ve Azure Defender uzantısı, Google Kubernetes altyapısı ve elastik Kubernetes hizmeti gibi yönetilen Kubernetes tekliflerini **desteklemez** . Azure [Defender, Azure Kubernetes hizmeti (AKS) için yerel olarak kullanılabilir](defender-for-kubernetes-introduction.md) ve kümenin Azure yaya bağlanmasını gerektirmez. |
| Ortamlar ve bölgeler | Bu uzantının kullanılabilirliği, [Azure Arc etkinleştirilmiş Kubernetes](../azure-arc/kubernetes/overview.md) ile aynıdır|

## <a name="architecture-overview"></a>Mimariye genel bakış

AKS dışındaki tüm Kubernetes kümeleri için kümenizi Azure yaya bağlamanız gerekir. Bağlandıktan sonra, Kubernetes için Azure Defender, [Azure Arc etkinleştirilmiş Kubernetes](../azure-arc/kubernetes/overview.md) kaynaklarına bir [küme uzantısı](../azure-arc/kubernetes/extensions.md)olarak dağıtılabilir.

Uzantı bileşenleri, kümedeki tüm denetim düzlemi düğümlerinden Kubernetes denetim günlükleri verilerini toplar ve daha fazla analiz için bunları bulutta Kubernetes arka ucu için Azure Defender 'a gönderir. Uzantı, veri işlem hattı olarak kullanılan bir Log Analytics çalışma alanı ile kaydedilir, ancak denetim günlüğü verileri Log Analytics çalışma alanında depolanmaz.

Bu diyagramda, Kubernetes için Azure Defender ve Azure Arc etkin Kubernetes kümesi arasındaki etkileşim gösterilmektedir:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Kubernetes için Azure Defender ve Azure Arc özellikli bir Kubernetes kümesi arasındaki etkileşimi ana hat halinde olan üst düzey bir mimari diyagram." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Önkoşullar

- Azure Defender for Kubernetes [aboneliğinizden etkin](enable-azure-defender.md)
- Kubernetes kümeniz [Azure yaya bağlı](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- [Genel küme uzantıları belgeleri](../azure-arc/kubernetes/extensions.md#prerequisites)altında listelenen önkoşulları karşıladık.

## <a name="deploy-the-azure-defender-extension"></a>Azure Defender uzantısını dağıtma

Azure Defender uzantısını bir dizi yöntem kullanarak dağıtabilirsiniz. Ayrıntılı adımlar için ilgili sekmeyi seçin.

### <a name="azure-portal"></a>[**Azure portal**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Güvenlik Merkezi önerideki "hızlı düzeltme" seçeneğini kullanın

Azure Güvenlik Merkezi 'nde özel bir öneri şunları sağlar:

- Kümelerinizde Kubernetes uzantısı için Defender 'ın dağıtıldığı **görünürlük**
- Uzantısı olmadan bu kümelere dağıtmak için **bir "hızlı düzelme" seçeneği**

1. Azure Güvenlik Merkezi 'nin öneriler sayfasından **Azure Defender** güvenlik denetimini etkinleştir ' i açın.

1. **Azure Arc etkin Kubernetes kümelerinde adlı öneriyi bulmak için filtreyi kullanın Azure Defender 'ın uzantısının yüklü olması gerekir**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Güvenlik Merkezi 'nin Azure Arc etkin Kubernetes kümeleri için Azure Defender uzantısını dağıtmaya yönelik önerisi." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Eylemler sütunundaki hızlı düzelme simgesine dikkat edin

1. Uzantısı olan ve uzantısı olmayan sağlıklı ve sağlıksız kaynaklar-kümelerin ayrıntılarını görmek için uzantıyı seçin.

1. Sağlıksız kaynaklar listesinden bir küme seçin ve düzeltme seçenekleriyle bölmeyi açmak için **Düzelt** ' i seçin.

1. İlgili Log Analytics çalışma alanını seçin ve **x kaynağını Düzelt**' i seçin.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Azure için Azure Defender uzantısını Güvenlik Merkezi 'nin hızlı düzeltme seçeneğiyle dağıtın.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Azure Defender uzantısını dağıtmak için Azure CLı 'yi kullanma

1. Azure 'da oturum açın:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Kümenizi Azure yaya bağlarken kullanılan için aynı abonelik KIMLIĞINI kullandığınızdan emin olun ``<your-subscription-id>`` .

1. Uzantıyı Azure Arc etkin Kubernetes kümenizin üzerine dağıtmak için aşağıdaki komutu çalıştırın:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Azure Defender uzantı türündeki tüm desteklenen yapılandırma ayarlarının açıklaması aşağıda verilmiştir:

    | Özellik | Açıklama |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Isteğe bağlı**. Kendi Log Analytics çalışma alanınızın tam kaynak KIMLIĞI.<br>Sağlanmazsa, bölgenin varsayılan çalışma alanı kullanılacaktır.<br><br>Tam kaynak KIMLIĞINI almak için, aboneliklerinizdeki çalışma alanlarının listesini varsayılan JSON biçiminde göstermek üzere aşağıdaki komutu çalıştırın:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>Log Analytics çalışma alanı kaynak KIMLIĞI aşağıdaki sözdizimine sahiptir:<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>[Log Analytics çalışma alanlarında](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) daha fazla bilgi edinin |
    | auditLogPath |**Isteğe bağlı**. Denetim günlüğü dosyalarının tam yolu.<br>Sağlanmazsa, varsayılan yol ``/var/log/kube-apiserver/audit.log`` kullanılacaktır.<br>AKS altyapısı için standart yol ``/var/log/kubeaudit/audit.log`` |

    Aşağıdaki komut, tüm isteğe bağlı alanların örnek kullanımını gösterir:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Azure Defender uzantısını dağıtmak için Azure Resource Manager kullanma

Azure Defender uzantısını dağıtmak için Azure Resource Manager kullanmak için aboneliğinizde bir Log Analytics çalışma alanı gerekir. [Log Analytics çalışma alanlarında](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces)daha fazla bilgi edinin.

Güvenlik Merkezi 'nin [yükleme örneklerinden](https://aka.ms/kubernetes-extension-installation-examples)Kaynak Yöneticisi Şablon **azure-defender-extension-arm-template.js** kullanabilirsiniz.

> [!TIP]
> Kaynak Yöneticisi şablonlarla yeni başladıysanız buradan başlayın: [Azure Resource Manager şablonlar nelerdir?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Azure Defender uzantısını dağıtmak için REST API kullanma 

Azure Defender uzantısını dağıtmak için REST API kullanmak için aboneliğinizde bir Log Analytics çalışma alanı gerekir. [Log Analytics çalışma alanlarında](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces)daha fazla bilgi edinin.

> [!TIP]
> Azure Defender uzantısını dağıtmak için API kullanmanın en basit yolu, güvenlik merkezi 'nin [yükleme örneklerinden](https://aka.ms/kubernetes-extension-installation-examples)sağlanan **POSTMAN koleksiyonu JSON** örneğidir.
- Postman koleksiyonunu JSON olarak değiştirmek veya uzantıyı REST API ile el ile dağıtmak için aşağıdaki PUT komutunu çalıştırın:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Konum:

    | Name            | İçinde   | Gerekli | Tür   | Description                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Abonelik Kimliği | path | True     | string | Azure Arc, Kubernetes kaynağının abonelik KIMLIĞINI etkinleştirdi |
    | Kaynak Grubu  | path | True     | string | Azure Arc etkin Kubernetes kaynağını içeren kaynak grubunun adı |
    | Küme Adı    | path | True     | string | Azure Arc etkin Kubernetes kaynağınızın adı  |


    **Kimlik doğrulaması** için, üst bilgisinde bir taşıyıcı belirteç (diğer Azure API 'lerinde olduğu gibi) olmalıdır. Bir taşıyıcı belirteci almak için aşağıdaki komutu çalıştırın:

    ```az account get-access-token --subscription <your-subscription-id>``` İletinizin gövdesi için aşağıdaki yapıyı kullanın:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    Özelliklerin açıklaması aşağıda verilmiştir:

    | Özellik | Açıklama | 
    | -------- | ----------- |
    | logAnalytics. Workspace kimliği | Log Analytics kaynağın çalışma alanı KIMLIĞI |
    | logAnalytics. Key         | Log Analytics kaynağının anahtarı | 
    | auditLogPath             | **Isteğe bağlı**. Denetim günlüğü dosyalarının tam yolu. Varsayılan değer: ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>Dağıtımı doğrulama

Kümenizin üzerine Azure Defender uzantısının yüklendiğini doğrulamak için aşağıdaki sekmelerden birindeki adımları izleyin:

### <a name="azure-portal---security-center"></a>[**Azure portal-Güvenlik Merkezi**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Uzantınızın durumunu doğrulamak için Güvenlik Merkezi önerisi kullanın

1. Azure Güvenlik Merkezi 'nin öneriler sayfasından  **Azure Defender** güvenlik denetimini etkinleştir ' i açın.

1. **Azure Arc etkin Kubernetes kümelerinde adlı öneriyi seçin Azure Defender 'ın uzantısının yüklü olması gerekir**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Güvenlik Merkezi 'nin Azure Arc etkin Kubernetes kümeleri için Azure Defender uzantısını dağıtmaya yönelik önerisi." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Uzantıyı dağıttığınız kümenin **sağlıklı** olarak listelendiğini kontrol edin.


### <a name="azure-portal---azure-arc"></a>[**Azure portal-Azure yay**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Uzantınızın durumunu doğrulamak için Azure yay sayfalarını kullanın

1. Azure portal **Azure Arc**' ı açın.
1. Altyapı listesinden **Kubernetes kümeleri** ' ni seçin ve ardından belirli kümeyi seçin.
1. Uzantılar sayfasını açın. Kümedeki uzantılar listelenir. Azure Defender uzantısının düzgün yüklendiğini onaylamak için **yükleme durumu** sütununu kontrol edin.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Bir Kubernetes kümesinde yüklü tüm uzantıların durumunu denetlemek için Azure Arc sayfası." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Daha fazla ayrıntı için uzantıyı seçin.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Bir Kubernetes kümesinde Azure Arc uzantısının tam ayrıntıları.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Uzantının dağıtıldığını doğrulamak için Azure CLı 'yi kullanma

1. Azure CLı 'de şu komutu çalıştırın:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. Yanıtta "extensionType": "Microsoft. azuredefender. Kubernetes" ve "InstallState": "yüklü" ifadesini arayın.

    > [!NOTE]
    > İlk birkaç dakika için "InstallState": "bekliyor" gösterebilir.
    
1. Durum **yüklü** görünüyorsa, makinenizde `kubeconfig` "azuredefender-xxxxx" adlı bir pod 'un ' çalışıyor ' durumunda olup olmadığını denetlemek için kümenize işaret eden dosya ile makinenizde aşağıdaki komutu çalıştırın:
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Uzantının dağıtıldığını doğrulamak için REST API kullanın

Başarılı bir dağıtımı doğrulamak veya uzantınızın durumunu dilediğiniz zaman doğrulamak için:

1. Aşağıdaki GET komutunu çalıştırın:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. Yanıtta "InstallState": "yüklü" için "extensionType" ("Microsoft. azuredefender. Kubernetes") bölümüne bakın.

    > [!TIP]
    > İlk birkaç dakika için "InstallState": "bekliyor" gösterebilir.
    
1. Durum **yüklü** görünüyorsa, makinenizde `kubeconfig` "azuredefender-xxxxx" adlı bir pod 'un ' çalışıyor ' durumunda olup olmadığını denetlemek için kümenize işaret eden dosya ile makinenizde aşağıdaki komutu çalıştırın:
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Kubernetes için Azure Defender 'daki güvenlik uyarılarının benzetimini yapın

Desteklenen uyarıların tam listesi, [Azure Güvenlik Merkezi 'ndeki tüm güvenlik uyarılarının başvuru tablosunda](alerts-reference.md#alerts-akscluster)bulunabilir.

1. Bir Azure Defender uyarısının benzetimini yapmak için aşağıdaki komutu çalıştırın:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    Beklenen yanıt "kaynak bulunamadı".

    30 dakika içinde Azure Defender bu etkinliği algılayacak ve bir güvenlik uyarısı tetikleyecektir.

1. Azure portal, Azure Güvenlik Merkezi 'nin güvenlik uyarıları sayfasını açın ve ilgili kaynakta uyarıyı arayın:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Kubernetes için Azure Defender 'dan örnek uyarı." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Azure Defender uzantısı kaldırılıyor

Uzantıyı aşağıdaki sekmelerde açıklandığı gibi Azure portal, Azure CLı veya REST API kullanarak kaldırabilirsiniz.

### <a name="azure-portal---arc"></a>[**Azure portal yay**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Uzantıyı kaldırmak için Azure portal kullanma

1. Azure portal Azure Arc ' ı açın.
1. Altyapı listesinden **Kubernetes kümeleri** ' ni seçin ve ardından belirli kümeyi seçin.
1. Uzantılar sayfasını açın. Kümedeki uzantılar listelenir.
1. Kümeyi seçin ve **Kaldır**' ı seçin.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Arc etkin Kubernetes kümenizdeki bir uzantıyı kaldırma." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Azure Defender uzantısını kaldırmak için Azure CLı 'yi kullanma

1. Kubernetes Arc uzantısı için Azure Defender 'ı aşağıdaki komutlarla kaldırın:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    Uzantının kaldırılması birkaç dakika sürebilir. Başarılı olduğunu doğrulamayı denemeden önce beklemeniz önerilir.

1. Uzantının başarıyla kaldırıldığını doğrulamak için aşağıdaki komutları çalıştırın:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Uzantı kaynağında Azure Resource Manager silindiği için hiçbir gecikme olmamalıdır. Bundan sonra, kümenize işaret eden dosya ile aşağıdaki komutu çalıştırarak kümede "azuredefender-xxxxx" adlı bir pod olmadığını doğrulayın `kubeconfig` : 

    ```console
    kubectl get pods -n azuredefender
    ```

    Pod 'nin silinmesi birkaç dakika sürebilir.

### <a name="rest-api"></a>[**REST API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Azure Defender uzantısını kaldırmak için REST API kullanma 

REST API kullanarak uzantıyı kaldırmak için aşağıdaki DELETE komutunu çalıştırın:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | İçinde   | Gerekli | Tür   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Abonelik Kimliği | path | True     | string | Arc, Kubernetes kümesinin abonelik KIMLIĞINI etkinleştirdi |
| Kaynak Grubu  | path | True     | string | Arc, Kubernetes kümesinin kaynak grubunu etkinleştirdi  |
| Küme Adı    | path | True     | string | Arc Kubernetes kümesinin adını etkinleştirdi            |

**Kimlik doğrulaması** için, üst bilgisinde bir taşıyıcı belirteç (diğer Azure API 'lerinde olduğu gibi) olmalıdır. Bir taşıyıcı belirteci almak için aşağıdaki komutu çalıştırın:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

İsteğin tamamlanması birkaç dakika sürebilir.

---

## <a name="next-steps"></a>Sonraki adımlar

Bu sayfada, Azure Arc etkin Kubernetes kümeleri için Azure Defender uzantısının nasıl dağıtılacağı açıklanmaktadır. Aşağıdaki sayfalarda Azure Defender ve Azure Güvenlik Merkezi 'nin kapsayıcı güvenlik özellikleri hakkında daha fazla bilgi edinin:

- [Güvenlik Merkezi’nde kapsayıcı güvenliği](container-security.md)
- [Kubernetes için Azure Defender 'a giriş](defender-for-kubernetes-introduction.md)
- [Kubernetes iş yüklerinizi koruma](kubernetes-workload-protections.md)
