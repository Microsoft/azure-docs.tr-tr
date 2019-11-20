---
title: Azure Kubernetes hizmeti için Azure Ilkesi öğrenin
description: Azure Ilkesi 'nin Azure Kubernetes hizmetindeki kümeleri yönetmek için rego 'ı ve açık Ilke aracısını nasıl kullandığını öğrenin.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: dbac1ee762066fb59cd57c04839666026a2bd89b
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959793"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Azure Kubernetes hizmeti için Azure Ilkesini anlama

Azure Ilkesi [Azure Kubernetes hizmeti](../../../aks/intro-kubernetes.md) (aks) ile tümleşerek, kümelerinizde merkezi ve tutarlı bir şekilde ölçeklenebilir ve koruma uygular.
[Açık Ilke Aracısı](https://www.openpolicyagent.org/) (Opa) için bir _giriş denetleyicisi Web kancası_ olan [Gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2 kullanımını genişleterek Azure ilkesi, Azure kaynaklarınızın ve aks kümelerinin uyumluluk durumunu tek bir yerden yönetmeyi ve rapor yapmayı olanaklı kılar.

> [!NOTE]
> AKS için Azure Ilkesi, sınırlı önizlemededir ve yalnızca yerleşik ilke tanımlarını destekler.

## <a name="overview"></a>Genel Bakış

AKS kümenizdeki AKS için Azure Ilkesini etkinleştirmek ve kullanmak için aşağıdaki işlemleri gerçekleştirin:

- [Önizleme özellikleri için kabul etme](#opt-in-for-preview)
- [Azure Ilke eklentisini yükler](#installation-steps)
- [AKS için ilke tanımı atama](#built-in-policies)
- [Doğrulamayı bekle](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Önizleme için kabul etme

Azure Ilke eklentisini yüklemeden veya hizmet özelliklerinden herhangi birini etkinleştirmeden önce aboneliğiniz **Microsoft. ContainerService** kaynak sağlayıcısını ve **Microsoft. policınghts** kaynak sağlayıcısını etkinleştirip, sonra da onaylanmalıdır önizlemeye katın. Önizlemeye katmak için Azure portal veya Azure CLı ile bu adımları izleyin:

- Azure portal:

  1. **Microsoft. ContainerService** ve **Microsoft. policınghts** kaynak sağlayıcılarını kaydedin. Adımlar için bkz. [kaynak sağlayıcıları ve türleri](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Azure portalında **Tüm hizmetler**’e tıkladıktan sonra **İlke**'yi arayıp seçerek Azure İlkesi hizmetini başlatın.

     ![Tüm hizmetlerde Ilke ara](../media/rego-for-aks/search-policy.png)

  1. Azure Ilke sayfasının sol tarafındaki **önizlemeyi Birleştir** ' i seçin.

     ![AKS önizlemesi için Ilkeye katın](../media/rego-for-aks/join-aks-preview.png)

  1. Önizlemeye eklenmesini istediğiniz aboneliğin satırını seçin.

  1. Abonelikler listesinin en üstündeki **kabul** et düğmesini seçin.

- Azure CLı:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Azure Ilke eklentisi

Kubernetes için _Azure Ilke eklentisi_ , Azure Ilke hizmetini ağ geçidi denetleyicisi giriş denetleyicisine bağlar. _Azure-Policy_ ad alanına yüklenen eklenti aşağıdaki işlevleri yapar:

- AKS kümesinin atamaları için Azure Ilkesini denetler
- **Configmaps** olarak _rego_ ilke tanımı dahil olmak üzere ilke ayrıntılarını indirir ve önbelleğe alır
- AKS kümesinde tam tarama uyumluluk denetimini çalıştırır
- Denetim ve uyumluluk ayrıntılarını Azure Ilkesine geri bildirir

### <a name="installing-the-add-on"></a>Eklentiyi yükleme

#### <a name="prerequisites"></a>Önkoşullar

Eklentiyi AKS kümenize yüklemeden önce, önizleme uzantısının yüklenmesi gerekir. Bu adım Azure CLı ile yapılır:

1. Azure CLı sürüm 2.0.62 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

1. AKS kümesi sürüm _1,10_ veya üzeri olmalıdır. AKS küme sürümünüzü doğrulamak için aşağıdaki betiği kullanın:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. AKS için Azure CLı önizleme uzantısının sürüm _0.4.0_ 'nı yükleyip `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > _Aks-Preview_ uzantısını daha önce yüklediyseniz, `az extension update --name aks-preview` komutunu kullanarak tüm güncelleştirmeleri yükleyebilirsiniz.

#### <a name="installation-steps"></a>Yükleme adımları

Önkoşullar tamamlandıktan sonra, yönetmek istediğiniz AKS kümesinde Azure Ilke eklentisini yükleyebilirsiniz.

- Azure portalında

  1. **Tüm hizmetler**' e tıklayıp **Kubernetes Hizmetleri**' nı arayıp seçerek aks hizmetini Azure Portal başlatın.

  1. AKS kümelerinizdeki birini seçin.

  1. Kubernetes hizmet sayfasının sol tarafındaki **ilkeleri (Önizleme)** seçin.

     ![AKS kümesindeki ilkeler](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Ana sayfada **eklentiyi etkinleştir** düğmesini seçin.

     ![AKS eklentisi için Azure Ilkesini etkinleştirin](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > **Eklentiyi etkinleştir** düğmesi gri ise, abonelik henüz önizlemeye eklenmedi. Gerekli adımlar için bkz. [Önizleme Için kabul etme](#opt-in-for-preview) .

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Doğrulama ve raporlama sıklığı

Eklenti, her 5 dakikada bir ilke atamalarındaki değişiklikler için Azure Ilkesiyle birlikte denetlenir. Bu yenileme sürecinde eklenti, _Azure-Policy_ ad alanındaki tüm _configmaps_ 'ı kaldırır, ardından ağ geçidi denetleyicisi kullanımı için _configmaps_ 'ı yeniden oluşturur.

> [!NOTE]
> Bir _küme yöneticisinin_ _Azure-Policy_ ad alanı için izni olabilir, ancak ad alanında değişiklik yapmak önerilmez veya desteklenmez. Yapılan el ile yapılan değişiklikler yenileme çevrimi sırasında kaybedilir.

Her 5 dakikada bir eklenti, kümenin tam taramasını çağırır. Kümede değişiklik yapılmaya çalışılanmaz tam taramanın ayrıntılarını ve gerçek zamanlı değerlendirmelerinin ayrıntılarını topladıktan sonra, eklenti, tüm Azure Ilke atamaları gibi [Uyumluluk ayrıntılarına](../how-to/get-compliance-data.md) eklenmek üzere sonuçları Azure ilkesine geri bildirir. Denetim döngüsüyle yalnızca etkin ilke atamalarının sonuçları döndürülür.

## <a name="policy-language"></a>İlke dili

AKS 'leri yönetmeye yönelik Azure Ilke dil yapısı, var olan ilkelerden daha sonra izler. Etkin hale getiren _ilke_ , aks kümelerinizi yönetmek için kullanılır ve Opa ve Gatekeeper v2 ile çalışmaya özgü _Ayrıntılar_ özellikleri alır. Ayrıntılar ve örnekler için bkz. [Enforceregopolicy](effects.md#enforceregopolicy) etkisi.

İlke tanımındaki _Ayrıntılar. Policy_ özelliğinin bir parçası olarak Azure ilkesi bir rego ilkesinin URI 'sini eklentiye geçirir. Rego, Kubernetes kümesine yönelik bir isteği doğrulamak veya bu kümeye bir istek vermek için, OPA ve Gatekeeper desteği sunan dildir. Azure Ilkesi, Kubernetes yönetimi için mevcut bir standardı destekleyerek, mevcut kuralları yeniden kullanmayı ve birleştirilmiş bir bulut uyumluluk raporlama deneyimi için bunları Azure Ilkesiyle eşleştirmeye olanak tanır. Daha fazla bilgi için bkz. [rego nedir?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Yerleşik ilkeler

Azure portal kullanarak AKS 'leri yönetmeye yönelik yerleşik ilkeleri bulmak için şu adımları izleyin:

1. Azure portal Azure Ilke hizmetini başlatın. Sol bölmedeki **tüm hizmetler** ' i seçin ve ardından **ilkeyi**arayıp seçin.

1. Azure Ilkesi sayfasının sol bölmesinde **tanımlar**' ı seçin.

1. Kategori açılan listesi kutusundan, filtre seçimini kaldırmak için **Tümünü Seç** ' i kullanın ve ardından **Kubernetes hizmeti**' ni seçin.

1. İlke tanımını seçin, sonra **ata** düğmesini seçin.

> [!NOTE]
> AKS tanımı için Azure Ilkesi atarken, **kapsamın** aks küme kaynağını içermesi gerekir.

Alternatif olarak, bir AKS ilkesini bulmak ve atamak için [Ilke atama-Portal](../assign-policy-portal.md) hızlı başlangıcı ' nı kullanın. ' Denetim VM 'leri ' örneği yerine bir Kubernetes ilke tanımı arayın.

> [!IMPORTANT]
> **Kubernetes hizmeti** kategorisindeki yerleşik ilkeler yalnızca aks ile kullanılabilir.

## <a name="logging"></a>Günlüğe kaydetme

### <a name="azure-policy-add-on-logs"></a>Azure Ilke eklentisi günlükleri

Bir Kubernetes denetleyicisi/kapsayıcısı olarak, Azure Ilke eklentisi AKS kümesinde günlükleri tutar. Günlükler, AKS kümesinin **Öngörüler** sayfasında gösterilir. Daha fazla bilgi için bkz. [kapsayıcılar Için Azure izleyici Ile AKS küme performansını anlama](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Gatekeeper günlükleri

Yeni kaynak istekleri için Gatekeeper günlüklerini etkinleştirmek üzere [AKS 'Deki Kubernetes ana düğüm günlüklerini etkinleştirme ve gözden geçirme](../../../aks/view-master-logs.md)bölümündeki adımları izleyin.
Yeni kaynak isteklerindeki reddedilen olayları görüntülemek için örnek bir sorgu aşağıda verilmiştir:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Ağ geçidi denetleyicisi kapsayıcılarından günlükleri görüntülemek için, [AKS 'Deki Kubernetes ana düğüm günlüklerini etkinleştirme ve gözden geçirme](../../../aks/view-master-logs.md) bölümündeki adımları Izleyin ve **Tanılama ayarları** bölmesinde _kulane-apiserver_ seçeneğini işaretleyin.

## <a name="remove-the-add-on"></a>Eklentiyi kaldır

Azure Ilke eklentisini AKS kümenizdeki kaldırmak için Azure portal veya Azure CLı kullanın:

- Azure portalında

  1. **Tüm hizmetler**' e tıklayıp **Kubernetes Hizmetleri**' nı arayıp seçerek aks hizmetini Azure Portal başlatın.

  1. Azure Ilke eklentisini devre dışı bırakmak istediğiniz AKS kümenizi seçin.

  1. Kubernetes hizmet sayfasının sol tarafındaki **ilkeleri (Önizleme)** seçin.

     ![AKS kümesindeki ilkeler](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Ana sayfada, **eklentiyi devre dışı bırak** düğmesini seçin.

     ![AKS eklentisi için Azure Ilkesini devre dışı bırakma](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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
- Azure Ilke eklentisi tarafından yüklenmeyen Gatekeeper ilkelerinin sayısı

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ilke örneklerindeki](../samples/index.md)örnekleri gözden geçirin.
- [İlke tanım yapısını](definition-structure.md) gözden geçirin.
- [İlkenin etkilerini anlama](effects.md) konusunu gözden geçirin.
- [Program aracılığıyla ilkelerin nasıl oluşturulduğunu](../how-to/programmatically-create.md)anlayın.
- [Uyumluluk verilerini nasıl alabileceğinizi](../how-to/getting-compliance-data.md)öğrenin.
- [Uyumlu olmayan kaynakları nasıl düzelteceğinizi](../how-to/remediate-resources.md)öğrenin.
- [Kaynakları Azure Yönetim gruplarıyla düzenleme](../../management-groups/overview.md)ile yönetim grubunun ne olduğunu inceleyin.