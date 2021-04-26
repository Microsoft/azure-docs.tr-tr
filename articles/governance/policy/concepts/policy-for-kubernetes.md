---
title: Kubernetes için Azure Ilkesi öğrenin
description: Azure Ilkesi 'nin Azure 'da veya şirket içinde Kubernetes çalıştıran kümeleri yönetmek için rego 'ı ve açık Ilke aracısını nasıl kullandığını öğrenin.
ms.date: 03/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ca33c3a937b0a155928f20469830388a95a08e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107506032"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Kubernetes kümeleri için Azure İlkesi'ni anlama

Azure Ilkesi, [Açık Ilke Aracısı](https://www.openpolicyagent.org/) (Opa) için bir _giriş denetleyicisi Web kancası_ [olan v3 'yi](https://github.com/open-policy-agent/gatekeeper) genişleterek, kümelerinizde merkezi ve tutarlı bir şekilde ölçeklenebilir ve koruma uygulamalarını uygular. Azure Ilkesi, Kubernetes kümelerinizin uyumluluk durumunu tek bir yerden yönetmeyi ve rapor yapmayı mümkün kılar. Eklenti aşağıdaki işlevleri üstlenir:

- Kümeye ilke atamaları için Azure Ilke hizmeti ile kontrol eder.
- İlke tanımlarını, [kısıtlama şablonu](https://github.com/open-policy-agent/gatekeeper#constraint-templates) ve [kısıtlama](https://github.com/open-policy-agent/gatekeeper#constraints) özel kaynakları olarak kümeye dağıtır.
- Denetim ve uyumluluk ayrıntılarını Azure Ilke hizmeti 'ne geri bildirir.

Kubernetes için Azure Ilkesi aşağıdaki küme ortamlarını destekler:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Azure Arc özellikli Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> AKS altyapısı ve Arc etkin Kubernetes eklentileri **önizlemededir**. Kubernetes için Azure Ilkesi yalnızca Linux düğüm havuzlarını ve yerleşik ilke tanımlarını destekler. Yerleşik ilke tanımları **Kubernetes** kategorisinde bulunur. **Enforceopaconstraint** ve **Enforceregopolicy** efektli sınırlı önizleme Ilkesi tanımları ve Ilgili **Kubernetes hizmet** kategorisi _kullanım dışıdır_. Bunun yerine, efekt _denetimi_ ve kaynak sağlayıcısı ile _reddetme_ modunu kullanın `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Genel Bakış

Azure Ilkesini Kubernetes kümenizle etkinleştirmek ve kullanmak için aşağıdaki işlemleri gerçekleştirin:

1. Kubernetes kümenizi yapılandırın ve eklentiyi yükledikten sonra:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc özellikli Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS Engine](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Yüklemeyle ilgili yaygın sorunlar için bkz. [sorun giderme-Azure Ilke eklentisi](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [Kubernetes için Azure Ilke dilini anlayın](#policy-language)

1. [Kubernetes kümenize yerleşik bir tanım atama](#assign-a-built-in-policy-definition)

1. [Doğrulamayı bekle](#policy-evaluation)

## <a name="limitations"></a>Sınırlamalar

Aşağıdaki genel sınırlamalar, Kubernetes kümeleri için Azure Ilke eklentisi için geçerlidir:

- Kubernetes için Azure Ilke eklentisi, Kubernetes sürüm **1,14** veya üzeri sürümlerde desteklenir.
- Kubernetes için Azure Ilke eklentisi yalnızca Linux düğüm havuzlarına dağıtılabilir
- Yalnızca yerleşik ilke tanımları desteklenir
- Küme başına ilke başına en fazla uyumlu olmayan kayıt sayısı: **500**
- Abonelik başına en fazla uyumlu olmayan kayıt sayısı: **1.000.000**
- Azure Ilke eklentisi dışında ağ geçidi denetleyicisi yüklemeleri desteklenmez. Azure Ilke eklentisini etkinleştirmeden önce önceki bir Gatekeeper yüklemesi tarafından yüklenen tüm bileşenleri kaldırın.
- [Uyumsuzluk nedenleri](../how-to/determine-non-compliance.md#compliance-reasons) `Microsoft.Kubernetes.Data` 
   [kaynak sağlayıcısı modu](./definition-structure.md#resource-provider-modes)için kullanılamaz. [Bileşen ayrıntılarını](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes)kullanın.
- [Muafiyet](./exemption-structure.md) , [kaynak sağlayıcısı modları](./definition-structure.md#resource-provider-modes)için desteklenmez.

Aşağıdaki sınırlamalar yalnızca AKS için Azure Ilke eklentisi için geçerlidir:

- [Aks Pod güvenlik ilkesinin](../../../aks/use-pod-security-policies.md) ve aks Için Azure ilke eklentisinin her ikisi de etkinleştirilebilir. Daha fazla bilgi için bkz. [aks Pod güvenlik sınırlaması](../../../aks/use-azure-policy.md).
- Ad alanları, değerlendirme için Azure Ilke eklentisi tarafından otomatik olarak dışlanır: _KUVE sistem_, _Gatekeeper-System_ ve _aks-Periscope_.

## <a name="recommendations"></a>Öneriler

Aşağıda, Azure Ilke eklentisinin kullanılmasına yönelik genel öneriler verilmiştir:

- Azure Ilke eklentisinin çalışması için üç Gatekeeper bileşeni gerekir: 1 denetim Pod ve 2 Web kancası Pod çoğaltmaları. Bu bileşenler, küme içinde denetim ve zorlama işlemleri gerektiren Kubernetes kaynakları ve ilke atamalarının sayısı arttıkça daha fazla kaynak kullanır.

  - En fazla 20 kısıtlama içeren tek bir kümede 500 ' den az pod için: bileşen başına 2 sanal CPU ve 350 MB bellek.
  - En fazla 40 kısıtlama içeren tek bir kümede 500 ' den fazla dizin için: bileşen başına 3 vCPU ve 600 MB bellek.

- Windows Pod [güvenlik bağlamlarını desteklemez](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Bu nedenle, kök ayrıcalıklarına izin verme gibi Azure ilke tanımlarının bazıları Windows yığınlarında ilerletilemiyor ve yalnızca Linux Pod 'ye uygulanamıyor.

Aşağıdaki öneri yalnızca AKS ve Azure Ilkesi eklentisi için geçerlidir:

- `CriticalAddonsOnly`Gatekeeper pods 'yi zamanlamak için Taint ile sistem düğüm havuzunu kullanın. Daha fazla bilgi için bkz. [sistem düğüm havuzlarını kullanma](../../../aks/use-system-pools.md#system-and-user-node-pools).
- AKS kümelerinizdeki giden trafiği güvenli hale getirin. Daha fazla bilgi için bkz. [küme düğümleri Için denetim çıkış trafiği](../../../aks/limit-egress-traffic.md).
- Küme `aad-pod-identity` etkinleştirilmişse, düğüm tarafından yönetilen kimlik (NMI) Pod, Azure örnek meta veri uç noktasına yapılan çağrıları ele almak için düğümlerin Iptables 'larını değiştirir. Bu yapılandırma, Pod kullanılmasa bile meta veri uç noktasına yapılan her türlü isteğin NMI tarafından yakalanmasıdır `aad-pod-identity` . AzurePodIdentityException CRD, `aad-pod-identity` CRD 'de tanımlanan etiketlerle eşleşen bir pod 'dan kaynaklanan meta veri uç noktasına yapılan tüm isteklerin NMI içinde herhangi bir işlem yapılmadan proxy olması gerektiğini bildirmek üzere yapılandırılabilir. `kubernetes.azure.com/managedby: aks` _Kuto-System_ ad alanındaki etiketli sistem KÖKLERI `aad-pod-identity` , AzurePodIdentityException CRD 'yi yapılandırarak içinde dışlanmalıdır. Daha fazla bilgi için bkz. [belirli bir pod veya uygulama için AAD-Pod kimliğini devre dışı bırakma](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Bir özel durum yapılandırmak için, [MIC özel durum YAML](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml)'yi yükler.

## <a name="install-azure-policy-add-on-for-aks"></a>AKS için Azure Ilke eklentisini yükler

Azure Ilke eklentisini yüklemeden veya hizmet özelliklerinden herhangi birini etkinleştirmeden önce aboneliğinizin **Microsoft. Poliınghts** kaynak sağlayıcılarını etkinleştirmesi gerekir.

1. Azure CLı sürüm 2.12.0 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

1. Kaynak sağlayıcılarını ve Önizleme özelliklerini kaydedin.

   - Azure portal:

     **Microsoft. Policınsıghts** kaynak sağlayıcılarını kaydedin. Adımlar için bkz. [kaynak sağlayıcıları ve türleri](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Azure CLı:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Sınırlı Önizleme ilkesi tanımları yüklenmişse, **ilkeler** sayfasında, aks kümenizdeki **devre dışı bırak** düğmesini kullanarak eklentiyi kaldırın.

1. AKS kümesi sürüm _1,14_ veya üzeri olmalıdır. AKS küme sürümünüzü doğrulamak için aşağıdaki betiği kullanın:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Azure CLı 'nin sürüm _2.12.0_ veya üstünü yükler. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli).

Yukarıdaki önkoşul adımları tamamlandıktan sonra, yönetmek istediğiniz AKS kümesine Azure Policy eklentisini yükleyebilirsiniz.

- Azure portalı

  1. **Tüm hizmetler**' i seçip **Kubernetes Hizmetleri**' nı arayıp seçerek aks hizmetini Azure Portal başlatın.

  1. AKS kümelerinizdeki birini seçin.

  1. Kubernetes hizmeti sayfasının sol tarafındaki **ilkeler** ' i seçin.

  1. Ana sayfada **eklentiyi etkinleştir** düğmesini seçin.

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > **Eklentiyi devre dışı bırak** düğmesi etkinse ve bir geçiş uyarısı v2 iletisi görüntülenirse, v1 eklentisi yüklenir ve v2 ilke tanımları atanmadan önce kaldırılmalıdır. _Kullanımdan kaldırılan_ v1 eklentisi, 24 Ağustos 'tan itibaren v2 eklentisi ile otomatik olarak değiştirilirler.
     > 2020. İlke tanımlarının yeni v2 sürümlerinin atanması gerekir. Şimdi yükseltmek için aşağıdaki adımları izleyin:
     >
     > 1. AKS kümenizin, AKS kümenizdeki **ilkeler** sayfasını ziyaret ederek ve "geçerli küme Azure ilke eklentisi v1..." İleti.
     > 1. [Eklentiyi kaldırın](#remove-the-add-on-from-aks).
     > 1. Eklentinin v2 sürümünü yüklemek için **eklentiyi etkinleştir** düğmesini seçin.
     > 1. [V1 yerleşik ilke tanımlarınızın V2 sürümlerini atayın](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Eklenti yüklemesinin başarılı olduğunu ve _Azure-Policy_ ve _Gatekeeper_ 'lerin çalıştığını doğrulamak için aşağıdaki komutu çalıştırın:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Son olarak, bu Azure CLı komutunu çalıştırarak, `<rg>` kaynak grubu adınızla ve `<cluster-name>` aks kümenizin adıyla değiştirerek en son eklentinin yüklendiğini doğrulayın: `az aks show --query addonProfiles.azurepolicy -g <rg> -n <cluster-name>` . Sonuç aşağıdaki çıkış ve yapılandırmaya benzer olmalıdır **. sürümü** şöyle olmalıdır `v2` :

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes için Azure Policy eklentisini yükler (Önizleme)

Azure Ilke eklentisini yüklemeden veya hizmet özelliklerinden herhangi birini etkinleştirmeden önce aboneliğinizin **Microsoft. policınghts** kaynak sağlayıcısını etkinleştirmesi ve küme hizmeti sorumlusu için bir rol ataması oluşturması gerekir.

1. Azure CLı sürüm 2.12.0 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

1. Kaynak sağlayıcısını etkinleştirmek için [kaynak sağlayıcıları ve türlerindeki](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) adımları izleyin ya da Azure clı veya Azure PowerShell komutunu çalıştırın:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Kubernetes kümesi sürüm _1,14_ veya üzeri olmalıdır.

1. [Held 3](https://v3.helm.sh/docs/intro/install/)' ü yükler.

1. Azure Arc için Kubernetes kümeniz etkinleştirildi. Daha fazla bilgi için bkz. [Azure yaya bir Kubernetes kümesi ekleme](../../../azure-arc/kubernetes/quickstart-connect-cluster.md).

1. Azure Arc etkin Kubernetes kümesinin tam Azure Kaynak KIMLIĞINE sahip olmalıdır.

1. Eklenti için bağlantı noktalarını açın. Azure Ilke eklentisi bu etki alanlarını ve bağlantı noktalarını, ilke tanımlarını ve atamalarını getirmek ve kümenin uyumluluğunu Azure Ilkesine geri bildirmek için kullanır.

   |Etki alanı |Bağlantı noktası |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Azure Arc etkin Kubernetes kümesine ' Policy Insights veri yazıcısı (Önizleme) ' rolü atamasını atayın. `<subscriptionId>` `<rg>` Azure Arc 'ın, Kubernetes kümesinin kaynak grubuyla ve `<clusterName>` Azure Arc etkinleştirilmiş Kubernetes kümesinin ADıYLA birlikte abonelik Kimliğinizle değiştirin. Yükleme adımları için _uygulama kimliği_, _parola_ ve _kiracı_ için döndürülen değerleri izleyin.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Yukarıdaki komutların örnek çıktısı:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Yukarıdaki önkoşul adımları tamamlandığında, Azure Arc etkin Kubernetes kümenize Azure Ilke eklentisini yüklersiniz:

1. Azure Ilkesi eklentisi deposunu Held 'ye ekleyin:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Held grafiğini kullanarak Azure Ilkesi eklentisini yükler:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Eklenti helb grafiğinin ne olduğu hakkında daha fazla bilgi için GitHub 'daki [Azure Ilke eklentisi Held grafik tanımına](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) bakın.

Eklenti yüklemesinin başarılı olduğunu ve _Azure-Policy_ ve _Gatekeeper_ 'lerin çalıştığını doğrulamak için aşağıdaki komutu çalıştırın:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>AKS altyapısı için Azure Ilke eklentisi 'ni (Önizleme) yükler

Azure Ilke eklentisini yüklemeden veya hizmet özelliklerinden herhangi birini etkinleştirmeden önce aboneliğinizin **Microsoft. policınghts** kaynak sağlayıcısını etkinleştirmesi ve küme hizmeti sorumlusu için bir rol ataması oluşturması gerekir.

1. Azure CLı sürüm 2.0.62 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

1. Kaynak sağlayıcısını etkinleştirmek için [kaynak sağlayıcıları ve türlerindeki](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) adımları izleyin ya da Azure clı veya Azure PowerShell komutunu çalıştırın:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Küme hizmeti sorumlusu için bir rol ataması oluşturun.

   - Küme hizmeti sorumlusu uygulama KIMLIĞINI bilmiyorsanız aşağıdaki komutla bakın.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLı ile küme hizmeti sorumlusu uygulama KIMLIĞINE (önceki adımdan alınan değer _Aadclitıd_ ) ' ilke öngörüleri veri yazıcısı ataması atayın. `<subscriptionId>`ABONELIK Kimliğiniz ile ve `<aks engine cluster resource group>` kendi kendine yönetilen Kubernetes kümesinin bulunduğu aks altyapısının bulunduğu kaynak grubuyla değiştirin.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Yukarıdaki önkoşul adımları tamamlandıktan sonra, Azure Ilke eklentisini yükler. Yükleme, bir AKS altyapısının oluşturulması veya güncelleştirilmesi sırasında veya var olan bir kümede bağımsız bir işlem olarak olabilir.

- Oluşturma veya güncelleştirme çevrimi sırasında yüklemesi

  Yeni bir otomatik olarak yönetilen kümenin oluşturulması sırasında veya var olan bir kümeye güncelleştirme olarak Azure Ilke eklentisini etkinleştirmek için AKS altyapısının [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) özellik kümesi tanımını ekleyin.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Hakkında daha fazla bilgi için bkz. dış kılavuz [aks altyapısı küme tanımı](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Held grafikleriyle mevcut kümede Install

  Kümeyi hazırlamak ve eklentiyi yüklemek için aşağıdaki adımları kullanın:

  1. [Held 3](https://v3.helm.sh/docs/intro/install/)' ü yükler.

  1. Azure Policy deposu ' nu Held 'ye ekleyin.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Daha fazla bilgi için bkz. [HELI grafiği-hızlı başlangıç kılavuzu](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Eklentiyi Held grafiğiyle birlikte yükler. `<subscriptionId>`ABONELIK Kimliğiniz ile ve `<aks engine cluster resource group>` kendi kendine yönetilen Kubernetes kümesinin bulunduğu aks altyapısının bulunduğu kaynak grubuyla değiştirin.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Eklenti helb grafiğinin ne olduğu hakkında daha fazla bilgi için GitHub 'daki [Azure Ilke eklentisi Held grafik tanımına](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) bakın.

     > [!NOTE]
     > Azure ilke eklentisi ve kaynak grubu kimliği arasındaki ilişki nedeniyle Azure Ilkesi, her kaynak grubu için yalnızca bir AKS motoru kümesini destekler.

Eklenti yüklemesinin başarılı olduğunu ve _Azure-Policy_ ve _Gatekeeper_ 'lerin çalıştığını doğrulamak için aşağıdaki komutu çalıştırın:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>İlke dili

Kubernetes yönetimine yönelik Azure Ilke dil yapısı, mevcut ilke tanımlarından sonra takip eder. [Kaynak sağlayıcısı moduyla](./definition-structure.md#resource-provider-modes) `Microsoft.Kubernetes.Data` , Kubernetes kümelerinizi yönetmek için etkiler [Denetim](./effects.md#audit) ve [reddetme](./effects.md#deny) kullanılır. _Denetim_ ve _reddetme_ , [Opa kısıtlama çerçevesi](https://github.com/open-policy-agent/frameworks/tree/master/constraint) ve Gatekeeper v3 ile çalışmaya özgü **Ayrıntılar** özellikleri sağlamalıdır.

İlke tanımındaki _details. constraintTemplate_ ve _details. Constraint_ özelliklerinin bir parçası olarak Azure Ilkesi, bu [customresourcedefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) URI 'lerini eklentiye geçirir. Rego, Kubernetes kümesine yönelik bir isteği doğrulamak için OPA ve Gatekeeper desteğinin desteklediği dildir. Azure Ilkesi, Kubernetes yönetimi için mevcut bir standardı destekleyerek, mevcut kuralları yeniden kullanmayı ve birleştirilmiş bir bulut uyumluluk raporlama deneyimi için bunları Azure Ilkesiyle eşleştirmeye olanak tanır. Daha fazla bilgi için bkz. [rego nedir?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Yerleşik ilke tanımı atama

Kubernetes kümenize bir ilke tanımı atamak için, uygun Azure rol tabanlı erişim denetimi (Azure RBAC) ilkesi atama işlemlerine atanmalısınız. Azure yerleşik rolleri **kaynak Ilkesi katılımcısı** ve **sahibi** bu işlemlere sahiptir. Daha fazla bilgi için bkz. [Azure Ilkesinde Azure RBAC izinleri](../overview.md#azure-rbac-permissions-in-azure-policy).

Aşağıdaki adımlarla Azure portal kullanarak kümenizi yönetmeye yönelik yerleşik ilke tanımlarını bulun:

1. Azure portal Azure Ilke hizmetini başlatın. Sol bölmedeki **tüm hizmetler** ' i seçin ve ardından **ilkeyi** arayıp seçin.

1. Azure Ilkesi sayfasının sol bölmesinde **tanımlar**' ı seçin.

1. Kategori açılan listesi kutusundan, filtreyi temizlemek için **Tümünü Seç** ' i kullanın ve ardından **Kubernetes**' i seçin.

1. İlke tanımını seçin, sonra **ata** düğmesini seçin.

1. **Kapsamı** , ilke atamasının uygulanacağı Kubernetes kümesinin yönetim grubu, abonelik veya kaynak grubu olarak ayarlayın.

   > [!NOTE]
   > Kubernetes tanımı için Azure Ilkesi atarken, **kapsamın** küme kaynağını içermesi gerekir. AKS motoru kümesi için **kapsam** , kümenin kaynak grubu olmalıdır.

1. İlke atamaya, kolayca tanımlamak için kullanabileceğiniz bir **ad** ve **Açıklama** sağlayın.

1. [İlke zorlamayı](./assignment-structure.md#enforcement-mode) aşağıdaki değerlerden birine ayarlayın.

   - **Etkin** -ilkeyi kümede zorunlu tutun. İhlal eden Kubernetes giriş istekleri reddedilir.

   - **Devre dışı** -ilke kümede zorlanmayın. İhlal eden Kubernetes giriş istekleri reddedildi. Uyumluluk değerlendirmesi sonuçları hala kullanılabilir. Küme çalıştırmaya yönelik yeni ilke tanımları kullanıma sunulmaya devam ederken, ihlal olan giriş istekleri reddedilerek ilke tanımını test etmek için _devre dışı_ seçeneği yararlıdır.

1. **İleri**’yi seçin.

1. **Parametre değerlerini** ayarla

   - Kubernetes ad alanlarını ilke değerlendirmesinden dışlamak için, parametre **ad uzayı dışlamaları** içindeki ad alanlarının listesini belirtin. Şunları hariç tutmak önerilir: _KUVE sistem_, _Gatekeeper-System_ ve _Azure-Arc_.

1. **Gözden geçir ve oluştur**’u seçin.

Alternatif olarak, bir Kubernetes ilkesini bulmak ve atamak için [Ilke atama-Portal](../assign-policy-portal.md) hızlı başlangıcı ' nı kullanın. ' Denetim VM 'leri ' örneği yerine bir Kubernetes ilke tanımı arayın.

> [!IMPORTANT]
> Yerleşik ilke tanımları, **Kubernetes** kategorisindeki Kubernetes kümeleri için kullanılabilir. Yerleşik ilke tanımlarının bir listesi için bkz. [Kubernetes örnekleri](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>İlke değerlendirmesi

Eklenti, 15 dakikada bir ilke atamalarındaki değişiklikler için Azure Ilke hizmeti ile birlikte denetlenir.
Bu yenileme sürecinde, eklenti değişiklikleri denetler. Bu değişiklikler tetikleyici, kısıtlama şablonları ve kısıtlamalarını oluşturur, güncelleştirir veya siler.

Bir Kubernetes kümesinde, bir ad alanı aşağıdaki etiketlerden birine sahipse ihlalleri olan giriş istekleri reddedilir. Uyumluluk değerlendirmesi sonuçları hala kullanılabilir.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Bir küme yöneticisinin, Azure Ilke eklentisi tarafından yüklenen kısıtlama şablonları ve kısıtlamalar kaynakları oluşturma ve güncelleştirme izni olabilir, ancak bu, el ile yapılan güncelleştirmelerin üzerine yazıldığı sürece bu desteklenmez. Gatekeeper, eklentiyi yüklemeden ve Azure Ilke ilkesi tanımları atamadan önce var olan ilkeleri değerlendirmeye devam eder.

Her 15 dakikada bir eklenti, kümenin tam taramasını çağırır. Kümede değişiklik yapılmaya çalışılanmaz tam taramanın ayrıntılarını ve gerçek zamanlı değerlendirmelerinin ayrıntılarını topladıktan sonra, eklenti, tüm Azure Ilke atamaları gibi [Uyumluluk ayrıntılarına](../how-to/get-compliance-data.md) eklenmek üzere sonuçları Azure ilkesine geri bildirir. Denetim döngüsüyle yalnızca etkin ilke atamalarının sonuçları döndürülür. Denetim sonuçları, başarısız kısıtlamanın durum alanında listelenen [ihlal](https://github.com/open-policy-agent/gatekeeper#audit) olarak da görülebilir. _Uyumlu olmayan_ kaynaklarla ilgili ayrıntılar için bkz. [kaynak sağlayıcısı modları için bileşen ayrıntıları](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Kubernetes kümeleriniz için Azure Ilkesindeki her uyumluluk raporu, son 45 dakika içindeki tüm ihlalleri içerir. Zaman damgası, bir ihlalin ne zaman oluştuğunu gösterir.

Diğer bazı hususlar:

- Küme aboneliği Azure Güvenlik Merkezi 'ne kayıtlıysa, Azure Güvenlik Merkezi Kubernetes ilkeleri kümeye otomatik olarak uygulanır.

- Mevcut Kubernetes kaynaklarıyla küme üzerinde bir reddetme ilkesi uygulandığında, yeni ilkeyle uyumlu olmayan önceden var olan herhangi bir kaynak çalışmaya devam eder. Uyumlu olmayan kaynak farklı bir düğümde yeniden zamanlandığında, Gatekeeper kaynak oluşturmayı engeller.

- Bir küme, kaynakları doğrulayan bir reddetme ilkesine sahip olduğunda, bir dağıtım oluştururken Kullanıcı bir Red iletisi görmez. Örneğin, replicasets ve pods içeren bir Kubernetes dağıtımını göz önünde bulundurun. Bir Kullanıcı yürütüldüğünde `kubectl describe deployment $MY_DEPLOYMENT` , olayların bir parçası olarak bir Red iletisi döndürmez. Ancak, `kubectl describe replicasets.apps $MY_DEPLOYMENT` reddetme ile ilişkili olayları döndürür.

## <a name="logging"></a>Günlüğe Kaydetme

Bir Kubernetes denetleyicisi/kapsayıcısı olarak, hem _Azure-Policy_ hem de _Gatekeeper_ Pod, Kubernetes kümesinde günlükleri saklar. Günlükler, Kubernetes kümesinin **Öngörüler** sayfasında gösterilebilir. Daha fazla bilgi için bkz. [kapsayıcılar Için Azure izleyici Ile Kubernetes küme Performansınızı izleme](../../../azure-monitor/containers/container-insights-analyze.md).

Eklenti günlüklerini görüntülemek için şunu kullanın `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Daha fazla bilgi için bkz. Gatekeeper belgelerindeki [hata ayıklama ağ](https://github.com/open-policy-agent/gatekeeper#debugging) geçidi.

## <a name="troubleshooting-the-add-on"></a>Eklenti sorunlarını giderme

Kubernetes eklentisinin sorunlarını giderme hakkında daha fazla bilgi için, Azure Ilkesi sorun giderme makalesindeki [Kubernetes bölümüne](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) bakın.

## <a name="remove-the-add-on"></a>Eklentiyi kaldır

### <a name="remove-the-add-on-from-aks"></a>AKS 'ten eklentiyi kaldırma

Azure Ilke eklentisini AKS kümenizdeki kaldırmak için Azure portal veya Azure CLı kullanın:

- Azure portalı

  1. **Tüm hizmetler**' i seçip **Kubernetes Hizmetleri**' nı arayıp seçerek aks hizmetini Azure Portal başlatın.

  1. Azure Ilke eklentisini devre dışı bırakmak istediğiniz AKS kümenizi seçin.

  1. Kubernetes hizmeti sayfasının sol tarafındaki **ilkeler** ' i seçin.

  1. Ana sayfada, **eklentiyi devre dışı bırak** düğmesini seçin.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Azure Arc etkin Kubernetes eklentisini kaldırma

Azure Arc etkin Kubernetes kümenizle Azure Ilke eklentisini ve ağ geçidi denetleyicisini kaldırmak için aşağıdaki Held komutunu çalıştırın:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>AKS altyapısından eklentiyi kaldırma

Azure Ilke eklentisini ve ağ geçidi denetleyicisini AKS altyapısı kümenize kaldırmak için eklentinin nasıl yüklendiğine göre hizalanan yöntemi kullanın:

- AKS altyapısının küme tanımında **addons** özelliği ayarlanarak yüklendiyse:

  _Azure-Policy_ için **addons** özelliğini false olarak değiştirdikten sonra küme tanımını aks altyapısına yeniden dağıtın:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Daha fazla bilgi için bkz. [aks altyapısı-Azure Ilke eklentisini devre dışı bırakma](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Held grafikleriyle birlikte yüklendiyse aşağıdaki Helu komutunu çalıştırın:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Ilke eklentisi tarafından toplanan tanılama verileri

Kubernetes için Azure Ilke eklentisi, sınırlı küme tanılama verilerini toplar. Bu tanılama verileri, yazılım ve performansla ilgili önemli teknik verileri sağlar. Aşağıdaki yollarla kullanılır:

- Azure Ilke eklentisini güncel tutun
- Azure Policy eklentisini güvenli tutun, güvenilir, performansı
- Eklentinin kullanılması için Azure Ilke eklentisini geliştirme ve toplama Analizi

Eklenti tarafından toplanan bilgiler kişisel veriler değildir. Şu anda toplanan Ayrıntılar:

- Azure Ilke eklentisi aracı sürümü
- Küme türü
- Küme bölgesi
- Küme kaynak grubu
- Küme kaynak KIMLIĞI
- Küme abonelik KIMLIĞI
- Küme işletim sistemi (örnek: Linux)
- Küme şehri (örnek: Seattle)
- Küme durumu veya bölgesi (örnek: Washington)
- Küme ülkesi veya bölgesi (örnek: Birleşik Devletler)
- İlke değerlendirmesinde aracı yüklemesi sırasında Azure Ilke eklentisi tarafından karşılaşılan özel durumlar/hatalar
- Azure Ilke eklentisi tarafından yüklenmeyen Gatekeeper ilke tanımlarının sayısı

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [İlke tanım yapısını](definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/get-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.
