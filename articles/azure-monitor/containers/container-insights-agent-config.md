---
title: Container Insights Aracısı veri toplamayı yapılandırma | Microsoft Docs
description: Bu makalede, Container Insights aracısını stdout/stderr ve ortam değişkenleri günlük toplamayı denetlemek için nasıl yapılandırabileceğiniz açıklanmaktadır.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d866fec2013daf9b8edfdbfd703c7b1098ae91bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708399"
---
# <a name="configure-agent-data-collection-for-container-insights"></a>Kapsayıcı öngörüleri için aracı veri toplamayı yapılandırma

Kapsayıcı öngörüleri, Kapsayıcılı aracıdan yönetilen Kubernetes kümelerine dağıtılan kapsayıcı iş yüklerinden stdout, stderr ve ortam değişkenlerini toplar. Bu deneyimi denetlemek için özel bir Kubernetes ConfigMaps oluşturarak, aracı veri toplama ayarlarını yapılandırabilirsiniz. 

Bu makalede, gereksinimlerinize göre ConfigMap oluşturmayı ve veri toplamayı yapılandırmayı gösterir.

>[!NOTE]
>Azure Red Hat OpenShift için, *OpenShift-Azure-Logging* ad alanında bir şablon configmap dosyası oluşturulur. 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap dosya ayarlarına genel bakış

Bunu sıfırdan oluşturmak zorunda kalmadan özelleştirmelerinizle kolayca düzenlemenizi sağlayan bir şablon ConfigMap dosyası sağlanır. Başlamadan önce, [configmaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) hakkında Kubernetes belgelerini gözden geçirmeniz ve configmaps oluşturma, yapılandırma ve dağıtma konusunda bilgi edinin. Bu, ad alanı başına veya tüm küme genelinde stderr ve STDOUT filtrelemenize ve kümedeki tüm düğüm/düğümlerde çalışan herhangi bir kapsayıcı için ortam değişkenlerine izin verir.

>[!IMPORTANT]
>Kapsayıcı iş yüklerinden stdout, stderr ve ortam değişkenlerini toplamak için desteklenen en düşük aracı sürümü ciprod06142019 veya üzeri. Aracı sürümünüzü doğrulamak için **düğüm** sekmesinde bir düğüm seçin ve Özellikler bölmesinde **Aracı görüntüsü etiketi** özelliğinin değeri. Aracı sürümleri ve her sürüme nelerin dahil olduğu hakkında ek bilgi için bkz. [Aracı sürüm notları](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Veri toplama ayarları

Aşağıdaki tabloda, veri toplamayı denetlemek için yapılandırabileceğiniz ayarlar açıklanmaktadır:

| Anahtar | Veri türü | Değer | Açıklama |
|--|--|--|--|
| `schema-version` | Dize (büyük/küçük harfe duyarlı) | v1 | Bu, aracı tarafından kullanılan şema sürümüdür<br> Bu ConfigMap ayrıştırılırken.<br> Şu anda desteklenen şema sürümü v1.<br> Bu değerin değiştirilmesi desteklenmez ve<br> ConfigMap değerlendirilirken reddedildi. |
| `config-version` | Dize |  | , Kaynak denetimi sisteminizde/deponuzda bu yapılandırma dosyasının sürümünün izini sürme yeteneğini destekler.<br> İzin verilen en fazla karakter sayısı 10 ' dur ve diğer tüm karakterler kesilir. |
| `[log_collection_settings.stdout] enabled =` | Boole | true veya false | Bu, stdout kapsayıcı günlüğü koleksiyonunun etkinleştirilip etkinleştirilmediğini denetler. Olarak ayarlandığında `true` ve STDOUT günlük toplama için hiçbir ad alanı dışlanmazsa<br> ( `log_collection_settings.stdout.exclude_namespaces` aşağıda ayarı), stdout günlükleri kümedeki tüm düğüm/düğümler genelinde tüm kapsayıcılardan toplanacaktır. ConfigMaps içinde belirtilmemişse,<br> Varsayılan değer `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | Dize | Virgülle ayrılmış dizi | Stdout günlüklerinin toplanmayacak Kubernetes ad alanları dizisi. Bu ayar yalnızca şu durumlarda geçerlidir<br> `log_collection_settings.stdout.enabled`<br> , olarak ayarlanır `true` .<br> ConfigMap içinde belirtilmemişse, varsayılan değer<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Boole | true veya false | Bu, stderr kapsayıcı günlüğü koleksiyonunun etkinleştirilip etkinleştirilmediğini denetler.<br> Olarak ayarlandığında `true` ve STDOUT günlük toplama için hiçbir ad alanı dışlanmazsa<br> ( `log_collection_settings.stderr.exclude_namespaces` ayar), stderr günlükleri kümedeki tüm düğüm/düğümler genelinde tüm kapsayıcılardan toplanacak.<br> ConfigMaps içinde belirtilmemişse, varsayılan değer<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | Dize | Virgülle ayrılmış dizi | Stderr günlüklerinin toplanmayacak Kubernetes ad alanları dizisi.<br> Bu ayar yalnızca şu durumlarda geçerlidir<br> `log_collection_settings.stdout.enabled` , olarak ayarlanır `true` .<br> ConfigMap içinde belirtilmemişse, varsayılan değer<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Boole | true veya false | Bu ayar ortam değişkeni toplamayı denetler<br> kümedeki tüm pods/düğümler genelinde<br> ve belirtilmediğinde varsayılan olarak `enabled = true`<br> ConfigMaps 'ta.<br> Ortam değişkenlerinin toplanması genel olarak etkinleştirilmişse, belirli bir kapsayıcı için devre dışı bırakabilirsiniz<br> ortam değişkenini ayarlayarak<br> `AZMON_COLLECT_ENV`bir Dockerfile ayarıyla ya da **env:** bölümünün altındaki [Pod Için yapılandırma dosyasında](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) **yanlış** .<br> Ortam değişkenlerinin toplanması genel olarak devre dışıysa, belirli bir kapsayıcı için koleksiyonu etkinleştiremezsiniz (yani, kapsayıcı düzeyinde uygulanabilecek tek geçersiz kılma, genel olarak etkinleştirildiğinde koleksiyonu devre dışı bırakmalıdır.). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Boole | true veya false | Bu ayar, ad ve görüntü özelliği değerlerini doldurmak için kapsayıcı günlüğü zenginleştirme özelliğini denetler<br> kümedeki tüm kapsayıcı günlükleri için ContainerLog tablosuna yazılan her günlük kaydı için.<br> `enabled = false`ConfigMap içinde belirtilmediğinde varsayılan değer olarak belirlenir. |
| `[log_collection_settings.collect_all_kube_events]` | Boole | true veya false | Bu ayar tüm türlerin Kume olayları koleksiyonuna izin verir.<br> Varsayılan olarak, *normal* tür Kume olayları toplanmaz. Bu ayar olarak ayarlandığında `true` , *normal* olaylar artık filtrelenmez ve tüm olaylar toplanır.<br> Varsayılan olarak, bu olarak ayarlanır `false` . |

### <a name="metric-collection-settings"></a>Ölçüm koleksiyonu ayarları

Aşağıdaki tabloda, ölçüm koleksiyonunu denetlemek için yapılandırabileceğiniz ayarlar açıklanmaktadır:

| Anahtar | Veri türü | Değer | Açıklama |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Boole | true veya false | Bu ayar, KUIN-System ad alanında kalıcı birim (BD) kullanım ölçümlerinin toplanmasını sağlar. Varsayılan olarak, Kuto-System ad alanındaki kalıcı birim taleplerini içeren kalıcı birimler için kullanım ölçümleri toplanmaz. Bu ayar olarak ayarlandığında `true` , tüm ad alanları için BD kullanım ölçümleri toplanır. Varsayılan olarak, bu olarak ayarlanır `false` . |

ConfigMaps genel bir liste ve aracıya yalnızca bir ConfigMap uygulanmış olabilir. Koleksiyonlar üzerine başka bir ConfigMaps olamaz.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps yapılandırma ve dağıtma

ConfigMap yapılandırma dosyanızı yapılandırmak ve kümenize dağıtmak için aşağıdaki adımları gerçekleştirin.

1. [ConfigMap YAML dosyasını şablon](https://aka.ms/container-azm-ms-agentconfig) olarak indirin ve kapsayıcı-AZM-MS-agentconfig. YAML olarak kaydedin. 

   > [!NOTE]
   > ConfigMap şablonu kümede zaten varolduğundan, Azure Red Hat OpenShift ile çalışırken bu adım gerekli değildir.

2. Stdout, stderr ve/veya ortam değişkenlerini toplamak için, özelleştirmelerinizle ConfigMap YAML dosyasını düzenleyin. Azure Red Hat OpenShift için ConfigMap YAML dosyasını düzenliyorsanız, önce `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` dosyayı bir metin düzenleyicisinde açmak için komutunu çalıştırın.

    - Stdout günlük toplama için belirli ad alanlarını dışlamak için anahtar/değer aşağıdaki örneği kullanarak yapılandırılır: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Belirli bir kapsayıcı için ortam değişkeni toplamayı devre dışı bırakmak için, `[log_collection_settings.env_var] enabled = true` değişken toplamayı küresel olarak etkinleştirmek üzere anahtar/değer ayarlayın ve ardından belirli bir kapsayıcının yapılandırmasını gerçekleştirmek için [buradaki](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) adımları izleyin.
    
    - Stderr günlük toplama kümesi genelinde devre dışı bırakmak için anahtar/değer aşağıdaki örneği kullanarak yapılandırılır: `[log_collection_settings.stderr] enabled = false` .

3. Azure Red Hat OpenShift dışındaki kümeler için aşağıdaki kubectl komutunu çalıştırarak ConfigMap oluşturun: `kubectl apply -f <configmap_yaml_file.yaml>` Azure Red Hat OpenShift dışındaki kümeler üzerinde. 
    
    Örnek: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Azure Red Hat OpenShift için değişikliklerinizi düzenleyicide kaydedin.

Yapılandırma değişikliğinin, yürürlüğe girmeden önce tamamlanması birkaç dakika sürebilir ve kümedeki tüm omsagent 'lar yeniden başlatılır. Yeniden başlatma, tüm omsagent pods için aynı anda yeniden başlatma işlemi için bir yeniden başlatma işlemi yapılır. Yeniden başlatmalar tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonucu içerir: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Yapılandırmayı Doğrula

Yapılandırmanın Azure Red Hat OpenShift dışında bir kümeye başarıyla uygulandığını doğrulamak için, bir aracı Pod 'dan günlükleri gözden geçirmek üzere aşağıdaki komutu kullanın: `kubectl logs omsagent-fdf58 -n kube-system` . Omsagent pods 'den yapılandırma hataları varsa, çıktıda aşağıdakine benzer hatalar gösterilir:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Yapılandırma değişikliklerini uygulamayla ilgili hatalar İnceleme için de kullanılabilir. Yapılandırma değişikliklerinde ek sorun giderme işlemleri gerçekleştirmek için aşağıdaki seçenekler kullanılabilir:

- Aynı komutu kullanarak bir aracı Pod günlüklerinden `kubectl logs` . 

    >[!NOTE]
    >Bu komut, Azure Red Hat OpenShift kümesi için geçerli değildir.
    > 

- Canlı Günlükler. Canlı günlüklerde aşağıdakine benzer hatalar gösterilir:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Log Analytics çalışma alanınızdaki **KubeMonAgentEvents** tablosundan. Veriler, yapılandırma hataları için *hata* önem derecesine sahip saatte bir gönderilir. Herhangi bir hata yoksa, tablodaki *girişte, hiçbir hata raporlayan önem derecesine* sahip veriler olur. **Etiketler** özelliği, hatanın oluştuğu Pod ve kapsayıcı kimliği ve ayrıca ilk oluşum, son oluşum ve Son saatteki sayı hakkında daha fazla bilgi içerir.

- Azure Red Hat OpenShift ile, OpenShift-Azure-Logging günlük koleksiyonunun etkinleştirilip etkinleştirilmediğini doğrulamak üzere **ContainerLog** tablosunu arayarak omsagent günlüklerini kontrol edin.

Azure Red Hat OpenShift dışındaki kümelerdeki ConfigMap 'teki hataları düzelttikten sonra, YAML dosyasını kaydedin ve şu komutu çalıştırarak güncelleştirilmiş ConfigMaps 'leri uygulayın: `kubectl apply -f <configmap_yaml_file.yaml` . Azure Red Hat OpenShift için komutunu çalıştırarak güncelleştirilmiş ConfigMaps 'leri düzenleyin ve kaydedin:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Güncelleştirilmiş ConfigMap uygulanıyor

Azure Red Hat OpenShift dışındaki kümeler üzerinde zaten bir ConfigMap dağıttıysanız ve daha yeni bir yapılandırmayla güncelleştirmek istiyorsanız, daha önce kullandığınız ConfigMap dosyasını düzenleyebilir ve daha sonra aynı komutu kullanarak uygulayabilirsiniz `kubectl apply -f <configmap_yaml_file.yaml` . Azure Red Hat OpenShift için komutunu çalıştırarak güncelleştirilmiş ConfigMaps 'leri düzenleyin ve kaydedin:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Yapılandırma değişikliğinin, yürürlüğe girmeden önce tamamlanması birkaç dakika sürebilir ve kümedeki tüm omsagent 'lar yeniden başlatılır. Yeniden başlatma, tüm omsagent pods için aynı anda yeniden başlatma işlemi için bir yeniden başlatma işlemi yapılır. Yeniden başlatmalar tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonucu içerir: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Şema sürümü doğrulanıyor

Desteklenen yapılandırma şeması sürümleri, omsagent pod üzerinde pod ek açıklaması (şema-sürümler) olarak kullanılabilir. Aşağıdaki kubectl komutuyla bunları görebilirsiniz: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Bu çıktı, ek açıklama şeması sürümleriyle aşağıdakine benzer şekilde görünür:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Sonraki adımlar

- Kapsayıcı öngörüleri, önceden tanımlanmış bir uyarı kümesi içermez. DevOps veya işletimsel işlemlerinizi ve yordamlarını desteklemek üzere yüksek CPU ve bellek kullanımı için önerilen uyarılar oluşturmayı öğrenmek için [kapsayıcı öngörüleriyle performans uyarılarını oluşturma](./container-insights-log-alerts.md) ' yı gözden geçirin.

- İzleme etkinken, AKS veya hibrit kümeniz ve üzerinde çalışan iş yüklerinizin sistem durumunu ve kaynak kullanımını toplayıp, kapsayıcı öngörülerini [nasıl kullanacağınızı](container-insights-analyze.md) öğrenin.

- Daha önceden tanımlanmış sorguları ve Uyarıları izlemek için değerlendirmek veya özelleştirmek üzere ön tanımlı sorguları ve örnekleri görmek için [günlük sorgusu örneklerini](container-insights-log-search.md#search-logs-to-analyze-data) görüntüleyin, kümelerinizi görselleştirmeyi veya çözümlemeyi inceleyin.