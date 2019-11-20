---
title: Kapsayıcılar Aracısı için Azure Izleyicisini yönetme | Microsoft Docs
description: Bu makalede, kapsayıcılar için Azure Izleyici tarafından kullanılan Kapsayıcılı Log Analytics aracısıyla en yaygın bakım görevlerinin yönetilmesi açıklanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/06/2018
ms.openlocfilehash: bfedd7989e71bcb8cf58cef7ad7122749350ae26
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554061"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Kapsayıcılar aracısının Azure Izleyicisini yönetme
Kapsayıcılar için Azure Izleyici, Linux için Log Analytics aracısının kapsayıcılı bir sürümünü kullanır. İlk dağıtımdan sonra, yaşam döngüsü boyunca gerçekleştirmeniz gerekebilecek rutin veya isteğe bağlı görevler vardır. Bu makalede, aracıyı el ile yükseltme ve belirli bir kapsayıcıdan ortam değişkenlerinin toplanmasını devre dışı bırakma hakkında ayrıntılı bilgiler yer alır. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Kapsayıcılar aracısının Azure Izleyicisini yükseltme
Kapsayıcılar için Azure Izleyici, Linux için Log Analytics aracısının kapsayıcılı bir sürümünü kullanır. Aracının yeni bir sürümü bırakıldığında, aracı Azure Kubernetes Service (AKS) üzerinde barındırılan yönetilen Kubernetes kümelerinizde otomatik olarak yükseltilir.  

Aracı yükseltmesi başarısız olursa, bu makalede aracıyı el ile yükseltme işlemi açıklanır. Yayınlanan sürümleri izlemek için bkz. [Aracı Sürüm duyuruları](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>İzlenen Kubernetes kümesinde aracıyı yükseltme
Aracıyı yükseltme işlemi iki düz ileri doğru adımdan oluşur. İlk adım, Azure CLı kullanan kapsayıcılar için Azure Izleyici ile izlemeyi devre dışı bırakmlarıdır.  [Izlemeyi devre dışı bırakma](container-insights-optout.md?#azure-cli) makalesinde açıklanan adımları izleyin. Azure CLı kullanmak, çözümü ve çalışma alanında depolanan ilgili verileri etkilemeden aracıyı kümedeki düğümlerden kaldırmamızı sağlar. 

>[!NOTE]
>Bu bakım etkinliğini gerçekleştirirken, kümedeki düğümler toplanan verileri iletmez ve performans görünümleri aracıyı kaldırdığınız zamanla yeni sürümü yüklerken verileri göstermez. 
>

Aracının yeni sürümünü yüklemek için, bu işlemi gerçekleştirmek için [Azure CLI kullanarak izlemeyi etkinleştirme](container-insights-enable-new-cluster.md#enable-using-azure-cli)bölümünde açıklanan adımları izleyin.  

İzlemeyi yeniden etkinleştirdikten sonra, küme için güncelleştirilmiş sistem durumu ölçümlerini görüntüleyebilmeniz yaklaşık 15 dakika sürebilir. Aracının başarıyla yükseltildiğini doğrulamak için şu komutu çalıştırın: `kubectl logs omsagent-484hw --namespace=kube-system`

Bu durum, *OMI* ve *omsagent* değerinin [Aracı yayın geçmişinde](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)belirtilen en son sürümle eşleşmesi gereken örneğe benzer.  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Bir kapsayıcıda ortam değişkeni toplamayı devre dışı bırakma
Kapsayıcılar için Azure Izleyici, Pod içinde çalışan kapsayıcılardan ortam değişkenlerini toplar ve bunları **kapsayıcılar** görünümünde seçilen kapsayıcının Özellik bölmesinde sunar. AKS kümesinin dağıtımı sırasında veya *AZMON_COLLECT_ENV*ortam değişkenini ayarlayarak, belirli bir kapsayıcı için koleksiyonu devre dışı bırakarak bu davranışı denetleyebilirsiniz. Bu özellik, aracı sürümü – ciprod11292018 ve üzeri sürümlerde kullanılabilir.  

Ortam değişkenlerinin koleksiyonunu yeni veya mevcut bir kapsayıcıda devre dışı bırakmak için, **AZMON_COLLECT_ENV** değişkenini Kubernetes Deployment YAML yapılandırma dosyanızda **false** değeriyle ayarlayın.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Değişikliği AKS kapsayıcısına uygulamak için şu komutu çalıştırın: `kubectl apply -f  <path to yaml file>`.

Yapılandırma değişikliğinin geçerli olduğunu doğrulamak için kapsayıcılar için Azure Izleyici 'de **kapsayıcılar** görünümünde bir kapsayıcı seçin ve özellik panelinde **ortam değişkenleri**' ni genişletin.  Bölüm yalnızca önceki- **AZMON_COLLECT_ENV = false**olarak oluşturulan değişkeni göstermelidir. Diğer tüm kapsayıcılar için, ortam değişkenleri bölümü bulunan tüm ortam değişkenlerini listelemelidir.   

Ortam değişkenlerinin bulunmasını yeniden etkinleştirmek için, aynı işlemi daha önce uygulayın ve değeri **false** iken **true**olarak değiştirin ve ardından kapsayıcıyı güncelleştirmek için `kubectl` komutunu yeniden çalıştırın.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Sonraki adımlar
Aracıyı yükseltirken sorunlarla karşılaşırsanız, destek için [sorun giderme kılavuzunu](container-insights-troubleshoot.md) gözden geçirin.