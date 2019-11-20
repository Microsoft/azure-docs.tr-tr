---
title: AKS altyapısı için Azure Ilkesi öğrenin
description: Azure Policy 'ın, AKS altyapısıyla kümeleri yönetmek için CustomResourceDefinitions 'ı ve ağ geçidi denetleyicisi 'nden açık Ilke aracısını nasıl kullandığını öğrenin.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 904575680b62233ad5ec7422abbf66cf57dc4e11
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072058"
---
# <a name="understand-azure-policy-for-aks-engine"></a>AKS altyapısı için Azure Ilkesini anlama

Azure Ilkesi, Azure 'da otomatik olarak yönetilen bir Kubernetes kümesini hızlı bir şekilde önyüklemek için kullanışlı araç sağlayan bir sistem olan [aks altyapısı](https://github.com/Azure/aks-engine/blob/master/docs/README.md)ile tümleşir. Bu tümleştirme, AKS motoru kendi kendine yönetilen kümelerinizde merkezi ve tutarlı bir şekilde ölçeklenebilir ve koruma sunar. Kubernetes için bir _giriş denetleyicisi Web kancası_ olan [Açık ilke aracısı](https://www.openpolicyagent.org/) (Opa) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (Beta) kullanımını genişleterek Azure ilkesi, Azure kaynaklarınızın ve aks altyapısının uyumluluk durumunu yönetme ve raporlama olanağı sağlar tek bir yerden kendi kendine yönetilen kümeler.

> [!NOTE]
> AKS altyapısı için Azure Ilkesi genel önizlemede ve SLA 'sı yoktur. Gatekeeper v3 beta aşamasındadır ve açık kaynaklı topluluk tarafından desteklenir. Hizmet yalnızca yerleşik ilke tanımlarını ve hizmet sorumlusu ile yapılandırılmış her kaynak grubu için tek bir AKS motoru kümesini destekler.

> [!IMPORTANT]
> AKS motoru, AKS altyapısı veya ağ geçidi denetleyicisi v3 için Azure Ilkesine yönelik destek almak için AKS motoru GitHub deposunda [Yeni bir sorun](https://github.com/Azure/aks-engine/issues/new/choose) oluşturun.

## <a name="overview"></a>Genel Bakış

Azure 'da kendi kendine yönetilen Kubernetes kümeniz ile AKS altyapısı için Azure Ilkesini etkinleştirmek ve kullanmak için aşağıdaki işlemleri gerçekleştirin:

- [Önkoşullar](#prerequisites)
- [Azure Ilke eklentisini yükler](#installing-the-add-on)
- [AKS altyapısı için ilke tanımı atama](#built-in-policies)
- [Doğrulamayı bekle](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Önkoşullar

Azure Ilke eklentisini yüklemeden veya hizmet özelliklerinden herhangi birini etkinleştirmeden önce aboneliğinizin **Microsoft. policınghts** kaynak sağlayıcısını etkinleştirmesi ve küme hizmeti sorumlusu için bir rol ataması oluşturması gerekir. 

1. Kaynak sağlayıcısını etkinleştirmek için [kaynak sağlayıcıları ve türlerindeki](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal) adımları izleyin ya da Azure clı veya Azure PowerShell komutunu çalıştırın:

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

1. Küme hizmeti sorumlusu için bir rol ataması oluşturma

   - Küme hizmeti sorumlusu uygulama KIMLIĞINI bilmiyorsanız aşağıdaki komutla bakın.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLı ile küme hizmeti sorumlusu uygulama KIMLIĞINE (önceki adımdan alınan değer _Aadclitıd_ ) ' ilke öngörüleri veri yazıcısı ataması atayın. `<subscriptionId>`, abonelik KIMLIĞINIZLE ve `<aks engine cluster resource group>`, AKS altyapısının kendi kendini yönettiği Kubernetes kümesinin bulunduğu kaynak grubuyla değiştirin.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure Ilke eklentisi

Kubernetes için _Azure Ilke eklentisi_ , Azure Ilke hizmetini ağ geçidi denetleyicisi giriş denetleyicisine bağlar. _Kuin-System_ ad alanına yüklenen eklenti aşağıdaki işlevleri yapar:

- AKS altyapısı kümesine atamalar için Azure Ilkesi 'ni denetler
- İlke ayrıntılarını, kısıtlama şablonlarını ve kısıtlamaları indirir ve yükler
- AKS motoru kümesinde tam tarama uyumluluk denetimini çalıştırır
- Denetim ve uyumluluk ayrıntılarını Azure Ilkesine geri bildirir

### <a name="installing-the-add-on"></a>Eklentiyi yükleme

Önkoşullar tamamlandığında, Azure Ilke eklentisi yüklenebilir. Yükleme, bir AKS altyapısının oluşturulması veya güncelleştirilmesi sırasında veya var olan bir kümede bağımsız bir işlem olarak olabilir.

- Oluşturma veya güncelleştirme çevrimi sırasında yüklemesi

  Yeni bir otomatik olarak yönetilen kümenin oluşturulması sırasında veya var olan bir kümeye güncelleştirme olarak Azure Ilke eklentisini etkinleştirmek için AKS altyapısının **addons** özellik kümesi tanımını ekleyin.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Hakkında daha fazla bilgi için bkz. dış kılavuz [aks altyapısı küme tanımı](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Held grafikleriyle mevcut kümede Install

  Kümeyi hazırlamak ve eklentiyi yüklemek için aşağıdaki adımları kullanın:

  1. Gatekeeper _-System_ ad alanı için Gatekeeper ' i yükler.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. _Kuto sistemine_ _Denetim düzlemi_ etiketi ekleyin. Bu etiket, ağ geçidi denetleyicisi ve Azure ilke eklentisi ile _Kuto sistemi_ Pod ve hizmetlerinin denetimini dışlar.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Kubernetes verilerini (Namespace, Pod, ınress, Service) OPA ile eşitleyin.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Daha fazla bilgi için bkz. [Opa-çoğaltma verileri](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Azure Policy deposu ' nu Held 'ye ekleyin.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Daha fazla bilgi için bkz. [HELI grafiği-hızlı başlangıç kılavuzu](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Eklentiyi Held grafiğiyle birlikte yükler. `<subscriptionId>`, abonelik KIMLIĞINIZLE ve `<aks engine cluster resource group>`, AKS altyapısının kendi kendini yönettiği Kubernetes kümesinin bulunduğu kaynak grubuyla değiştirin.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Eklenti helb grafiğinin ne olduğu hakkında daha fazla bilgi için GitHub 'daki [Azure Ilke eklentisi Held grafik tanımına](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) bakın.

     > [!NOTE]
     > Azure ilke eklentisi ve kaynak grubu kimliği arasındaki ilişki nedeniyle Azure Ilkesi, her kaynak grubu için yalnızca bir AKS motoru kümesini destekler.

Eklenti yüklemesinin başarılı olduğunu ve _Azure-Policy_ Pod 'un çalıştığını doğrulamak için şu komutu çalıştırın:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Doğrulama ve raporlama sıklığı

Eklenti, her 5 dakikada bir ilke atamalarındaki değişiklikler için Azure Ilkesiyle birlikte denetlenir. Bu yenileme sürecinde, eklenti değişiklikleri denetler. Bu değişiklikler tetikleyici, kısıtlama şablonları ve kısıtlamalarını oluşturur, güncelleştirir veya siler.

> [!NOTE]
> Bir _küme yöneticisinin_ kısıtlama şablonlarında ve kısıtlamalarında değişiklik yapma izni olabileceğinden, kısıtlama şablonlarında veya Azure ilkesi tarafından oluşturulan kısıtlamalara değişiklik yapmak önerilmez veya desteklenmez. Yapılan el ile yapılan değişiklikler yenileme çevrimi sırasında kaybedilir.

Her 5 dakikada bir eklenti, kümenin tam taramasını çağırır. Kümede değişiklik yapılmaya çalışılanmaz tam taramanın ayrıntılarını ve gerçek zamanlı değerlendirmelerinin ayrıntılarını topladıktan sonra, eklenti, tüm Azure Ilke atamaları gibi [Uyumluluk ayrıntılarına](../how-to/get-compliance-data.md) eklenmek üzere sonuçları Azure ilkesine geri bildirir. Denetim döngüsüyle yalnızca etkin ilke atamalarının sonuçları döndürülür. Denetim sonuçları, başarısız kısıtlamanın durum alanında listelenen ihlal olarak da görülebilir.

## <a name="policy-language"></a>İlke dili

AKS altyapısını yönetmeye yönelik Azure Ilke dil yapısı var olan ilkelerden daha sonra izler. AKS altyapısı kümelerinizi yönetmek ve [Opa kısıtlama çerçevesi](https://github.com/open-policy-agent/frameworks/tree/master/constraint) ve ağ geçidi denetleyicisi v3 ile çalışmaya özgü _Ayrıntılar_ özellikleri almak Için etkilidir _ceopaconstraint_ etkisi kullanılır. Ayrıntılar ve örnekler için bkz. [Enforceopaconstraint](effects.md#enforceopaconstraint) etkisi.

İlke tanımındaki _details. constraintTemplate_ ve _details. Constraint_ özelliklerinin bir parçası olarak Azure Ilkesi, bu [customresourcedefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) URI 'lerini eklentiye geçirir. Rego, Kubernetes kümesine yönelik bir isteği doğrulamak için OPA ve Gatekeeper desteğinin desteklediği dildir. Azure Ilkesi, Kubernetes yönetimi için mevcut bir standardı destekleyerek, mevcut kuralları yeniden kullanmayı ve birleştirilmiş bir bulut uyumluluk raporlama deneyimi için bunları Azure Ilkesiyle eşleştirmeye olanak tanır. Daha fazla bilgi için bkz. [rego nedir?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Yerleşik ilkeler

Azure portal kullanarak AKS motoru kümenizi yönetmeye yönelik yerleşik ilkeleri bulmak için şu adımları izleyin:

1. Azure portal Azure Ilke hizmetini başlatın. Sol bölmedeki **tüm hizmetler** ' i seçin ve ardından **ilkeyi**arayıp seçin.

1. Azure Ilkesi sayfasının sol bölmesinde **tanımlar**' ı seçin.

1. Kategori açılan listesi kutusundan, filtreyi temizlemek için **Tümünü Seç** ' i kullanın ve ardından **Kubernetes**' i seçin.

1. İlke tanımını seçin, sonra **ata** düğmesini seçin.

> [!NOTE]
> AKS altyapısı tanımı için Azure Ilkesi atarken, **kapsamın** aks motoru kümesinin kaynak grubu olması gerekir.

Alternatif olarak, bir AKS altyapısı ilkesini bulmak ve atamak için [Ilke atama-Portal](../assign-policy-portal.md) hızlı başlangıcı ' nı kullanın. ' Denetim VM 'leri ' örneği yerine bir AKS motoru ilke tanımı arayın.

> [!IMPORTANT]
> **Kubernetes** kategorisindeki yerleşik ilkeler yalnızca aks altyapısıyla birlikte kullanılabilir.

## <a name="logging"></a>Günlüğe kaydetme

### <a name="azure-policy-add-on-logs"></a>Azure Ilke eklentisi günlükleri

Bir Kubernetes denetleyicisi/kapsayıcısı olarak, Azure Ilke eklentisi AKS altyapısı kümesindeki günlükleri tutar.

Azure Ilke eklentisi günlüklerini görüntülemek için `kubectl`kullanın:

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Gatekeeper günlükleri

Gatekeeper, Gatekeeper- _Controller-Manager-0_, genellikle `gatekeeper-system` veya `kube-system` ad alanında bulunur, ancak nasıl dağıtıldığına bağlı olarak farklı bir ad alanında olabilir.

Gatekeeper günlüklerini görüntülemek için `kubectl`kullanın:

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Daha fazla bilgi için bkz. OPA belgelerinde [hata ayıklama ağ geçidi](https://github.com/open-policy-agent/gatekeeper#debugging) .

## <a name="remove-the-add-on"></a>Eklentiyi kaldır

Azure Ilke eklentisini ve ağ geçidi denetleyicisini AKS altyapısı kümenize kaldırmak için eklentinin nasıl yüklendiğine göre hizalanan yöntemi kullanın:

- AKS altyapısının küme tanımında **addons** özelliği ayarlanarak yüklendiyse:

  _Azure-Policy_ için **addons** özelliğini false olarak değiştirdikten sonra küme tanımını aks altyapısına yeniden dağıtın:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Held grafikleriyle yüklenmişse:

  1. Eski kısıtlamaları kaldır

     Şu anda kaldırma mekanizması yalnızca Gatekeeper sistemini kaldırır, Kullanıcı tarafından oluşturulan _ConstraintTemplate_, _kısıtlama_veya _yapılandırma_ kaynaklarını kaldırmaz, ya da bunlara karşılık gelen _crds 'yi kaldırır_ .

     Ağ geçidi denetleyicisi çalışırken, istenmeyen kısıtlamaları şu şekilde kaldırmak mümkündür:

     - Kısıtlama kaynağının tüm örnekleri siliniyor
     - _ConstraintTemplate_ kaynağını silme, bu, _CRD_ 'yi otomatik olarak temizlemelidir
     - _Yapılandırma_ kaynağını silme, eşitlenmiş kaynaklardaki sonlandırıcıları kaldırır

  1. Azure Ilke eklentisini kaldır
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Gatekeeper kaldırma
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Ilke eklentisi tarafından toplanan tanılama verileri

Kubernetes için Azure Ilke eklentisi, sınırlı küme tanılama verilerini toplar. Bu tanılama verileri, yazılım ve performansla ilgili önemli teknik verileri sağlar. Aşağıdaki yollarla kullanılır:

- Azure Ilkesi eklentisini güncel tutun
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