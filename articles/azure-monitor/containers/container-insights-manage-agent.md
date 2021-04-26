---
title: Kapsayıcı öngörüleri aracısını yönetme | Microsoft Docs
description: Bu makalede, kapsayıcı öngörüleri tarafından kullanılan Kapsayıcılı Log Analytics aracısıyla en yaygın bakım görevlerinin yönetilmesi açıklanmaktadır.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 6b485f4d49f0dd80f712d96779098c26be3f6de1
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442584"
---
# <a name="how-to-manage-the-container-insights-agent"></a>Kapsayıcı öngörüleri aracısını yönetme

Kapsayıcı öngörüleri, Linux için Log Analytics aracısının kapsayıcılı bir sürümünü kullanır. İlk dağıtımdan sonra, yaşam döngüsü boyunca gerçekleştirmeniz gerekebilecek rutin veya isteğe bağlı görevler vardır. Bu makalede, aracıyı el ile yükseltme ve belirli bir kapsayıcıdan ortam değişkenlerinin toplanmasını devre dışı bırakma hakkında ayrıntılı bilgiler yer alır. 

## <a name="how-to-upgrade-the-container-insights-agent"></a>Kapsayıcı öngörüleri aracısını yükseltme

Kapsayıcı öngörüleri, Linux için Log Analytics aracısının kapsayıcılı bir sürümünü kullanır. Aracının yeni bir sürümü bırakıldığında, aracı Azure Kubernetes Service (AKS) ve Azure Red Hat OpenShift sürüm 3. x üzerinde barındırılan yönetilen Kubernetes kümelerinizde otomatik olarak yükseltilir. Karma bir [Kubernetes kümesi](container-insights-hybrid-setup.md) ve Azure Red Hat OpenShift sürüm 4. x için, aracı yönetilmez ve aracıyı el ile yükseltmeniz gerekir.

AKS veya Azure Red Hat OpenShift sürüm 3. x üzerinde barındırılan bir kümede aracı yükseltmesi başarısız olursa, bu makalede aracıyı el ile yükseltme işlemi de açıklanmaktadır. Yayınlanan sürümleri izlemek için bkz. [Aracı Sürüm duyuruları](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-aks-cluster"></a>AKS kümesinde aracıyı yükseltme

AKS kümelerinde aracıyı yükseltme işlemi iki düz ileri doğru adımdan oluşur. İlk adım, Azure CLı kullanarak kapsayıcı öngörüleri ile izlemeyi devre dışı bırakmada olur. [Izlemeyi devre dışı bırakma](container-insights-optout.md?#azure-cli) makalesinde açıklanan adımları izleyin. Azure CLı kullanmak, çözümü ve çalışma alanında depolanan ilgili verileri etkilemeden aracıyı kümedeki düğümlerden kaldırmamızı sağlar. 

>[!NOTE]
>Bu bakım etkinliğini gerçekleştirirken, kümedeki düğümler toplanan verileri iletmez ve performans görünümleri aracıyı kaldırdığınız zamanla yeni sürümü yüklerken verileri göstermez. 
>

Aracının yeni sürümünü yüklemek için, bu işlemi gerçekleştirmek için [Azure CLI kullanarak izlemeyi etkinleştirme](container-insights-enable-new-cluster.md#enable-using-azure-cli)bölümünde açıklanan adımları izleyin.  

İzlemeyi yeniden etkinleştirdikten sonra, küme için güncelleştirilmiş sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir. Aracının başarıyla yükseltildiğini doğrulamak için şunlardan birini yapabilirsiniz:

* Şu komutu çalıştırın: `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` . Döndürülen durum ' da, çıkışın *kapsayıcılar* bölümünde omsagent için **görüntü** altındaki değeri aklınızda edin.
* **Düğümler** sekmesinde, küme düğümünü seçin ve sağdaki **Özellikler** bölmesinde, **Aracı görüntüsü etiketi** altındaki değeri aklınızda edin.

Gösterilen aracının sürümü, [yayın geçmişi](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) sayfasında listelenen en son sürümle eşleşmelidir.

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Karma Kubernetes kümesinde aracıyı yükseltme

Üzerinde çalışan bir Kubernetes kümesinde aracıyı yükseltmek için aşağıdaki adımları gerçekleştirin:

* AKS altyapısı kullanılarak Azure üzerinde barındırılan, kendi kendine yönetilen Kubernetes kümeleri.
* AKS altyapısını kullanarak Azure Stack veya şirket içinde barındırılan, kendi kendine yönetilen Kubernetes kümeleri.
* Red Hat OpenShift sürüm 4. x.

Log Analytics çalışma alanı ticari Azure 'da ise aşağıdaki komutu çalıştırın:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Log Analytics çalışma alanı Azure Çin 21Vianet ' de ise aşağıdaki komutu çalıştırın:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Log Analytics çalışma alanı Azure ABD kamu 'da ise aşağıdaki komutu çalıştırın:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Azure Red Hat OpenShift v4 üzerinde aracıyı yükseltme

Azure Red Hat OpenShift sürüm 4. x üzerinde çalışan bir Kubernetes kümesinde aracıyı yükseltmek için aşağıdaki adımları gerçekleştirin. 

>[!NOTE]
>Azure Red Hat OpenShift sürüm 4. x yalnızca Azure ticari bulutunda çalışmayı destekler.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Bir kapsayıcıda ortam değişkeni toplamayı devre dışı bırakma

Kapsayıcı öngörüleri bir pod içinde çalışan kapsayıcılardan ortam değişkenlerini toplar ve bunları **kapsayıcılar** görünümünde seçilen kapsayıcının Özellik bölmesinde görüntüler. Bu davranışı, Kubernetes kümesinin dağıtımı sırasında veya *AZMON_COLLECT_ENV* ortam değişkenini ayarlayarak, belirli bir kapsayıcı için koleksiyonu devre dışı bırakarak denetleyebilirsiniz. Bu özellik, aracı sürümü – ciprod11292018 ve üzeri sürümlerde kullanılabilir.  

Ortam değişkenlerinin koleksiyonunu yeni veya mevcut bir kapsayıcıda devre dışı bırakmak için, **AZMON_COLLECT_ENV** değişkenini Kubernetes Deployment YAML yapılandırma dosyanızda **false** değeriyle ayarlayın. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Değişikliği Azure Red Hat Openshıft dışındaki Kubernetes kümelerine uygulamak için aşağıdaki komutu çalıştırın: `kubectl apply -f  <path to yaml file>` . ConfigMap 'i düzenlemek ve bu değişikliği Azure Red Hat OpenShift kümeleri için uygulamak üzere şu komutu çalıştırın:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Bu, varsayılan metin düzenleyicinizi açar. Değişkeni ayarladıktan sonra dosyayı düzenleyicide kaydedin.

Yapılandırma değişikliğinin geçerli olduğunu doğrulamak için, kapsayıcı öngörüleri içindeki **kapsayıcılar** görünümünde bir kapsayıcı seçin ve özellik panelinde **ortam değişkenleri**' ni genişletin.  Bölüm yalnızca daha önce oluşturulan **AZMON_COLLECT_ENV = false** değişkenini göstermelidir. Diğer tüm kapsayıcılar için, ortam değişkenleri bölümü bulunan tüm ortam değişkenlerini listelemelidir.

Ortam değişkenlerinin bulunmasını yeniden etkinleştirmek için, aynı işlemi daha önce uygulayın ve değeri **false** iken **true** olarak değiştirin ve ardından `kubectl` kapsayıcıyı güncelleştirmek için komutu yeniden çalıştırın.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Sonraki adımlar

Aracıyı yükseltirken sorunlarla karşılaşırsanız, destek için [sorun giderme kılavuzunu](container-insights-troubleshoot.md) gözden geçirin.
