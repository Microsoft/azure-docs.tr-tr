---
title: Kapsayıcılar için Azure Izleyici sorunlarını giderme | Microsoft Docs
description: Bu makalede, kapsayıcılar için Azure Izleyici ile ilgili sorunları nasıl giderebileceğiniz ve giderebileceğiniz açıklanır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 3d6ed3b13c134d8e9c1df72ae2cb880a477a803a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477039"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Kapsayıcılar için Azure Izleyici sorunlarını giderme

Azure Kubernetes Service (AKS) kümenizin, kapsayıcılar için Azure Izleyici ile izlenmesini yapılandırdığınızda, veri toplamayı veya raporlama durumunu engellemeye yönelik bir sorunla karşılaşabilirsiniz. Bu makalede bazı yaygın sorunlar ve sorun giderme adımları ayrıntılı olarak anlatılmaktadır.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Ekleme veya güncelleştirme işlemi sırasında yetkilendirme hatası
Kapsayıcılar için Azure Izleyicisini etkinleştirirken veya bir kümeyi ölçüm toplamayı destekleyecek şekilde güncelleştirirken, *istemci < kullanıcının kimliği > ' ' nin nesne kimliği olan ' < kullanıcının objectıd > ' öğesinin kimlik doğrulama sahibi olmadığı konusunda bir hata alabilirsiniz. kapsam üzerinde ' Microsoft. Authorization/Roleatamalar/Write ' eylemini gerçekleştir*

Ekleme veya güncelleştirme işlemi sırasında, küme kaynağında **Izleme ölçümleri yayımcı** rolü atamasının verilmesi denenir. Kapsayıcılar için Azure Izleyicisini etkinleştirme işlemini başlatan kullanıcı veya ölçüm koleksiyonunu destekleyen güncelleştirme, AKS kümesi kaynak kapsamında **Microsoft. Authorization/Roleatamalar/Write** iznine sahip olmalıdır. Yalnızca **sahip** ve **Kullanıcı erişimi Yöneticisi** yerleşik rollerinin üyelerine bu izne erişim verilir. Güvenlik ilkeleriniz parçalı düzey izinler atanmasını gerektiriyorsa, [özel rolleri](../../role-based-access-control/custom-roles.md) görüntülemenizi ve bunu gerektiren kullanıcılara atamanızı öneririz. 

Ayrıca, aşağıdaki adımları uygulayarak bu rolü Azure portal el ile de verebilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın. 
2. Azure portalının sol alt köşesinde bulunan **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Kubernetes**yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Azure Kubernetes**' i seçin.
3. Kubernetes kümeleri listesinde, listeden bir tane seçin.
2. Sol taraftaki menüden **erişim denetimi (IAM)** öğesine tıklayın.
3. Bir rol ataması eklemek için **+ Ekle** ' yi seçin ve **izleme ölçümleri yayımcısı** rolünü seçin ve yalnızca abonelikte tanımlanan kümeler hizmet sorumluları ' nda sonuçları filtrelemek Için **Seç** kutusu tür **aks** ' i seçin. Bu kümeye özgü listeden birini seçin.
4. Rolü atamaya son vermek için **Kaydet** ' i seçin. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Kapsayıcılar için Azure Izleyici etkin ancak hiçbir bilgi bildirmiyor
Kapsayıcılar için Azure Izleyici başarıyla etkinleştirilip yapılandırılmışsa, ancak durum bilgilerini görüntüleyemez veya bir günlük sorgusundan hiçbir sonuç döndürülmezse, bu adımları izleyerek sorunu tanılayabilirsiniz: 

1. Şu komutu çalıştırarak aracının durumunu denetleyin: 

    `kubectl get ds omsagent --namespace=kube-system`

    Çıktının düzgün şekilde dağıtıldığını belirten aşağıdakine benzer olması gerekir:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Şu komutu kullanarak aracı sürümü *06072018* veya üzeri ile dağıtım durumunu denetleyin:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Çıktı, doğru şekilde dağıtıldığını belirten aşağıdaki örneğe benzer olmalıdır:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Şu komutu kullanarak çalıştığını doğrulamak için pod 'un durumunu denetleyin: `kubectl get pods --namespace=kube-system`

    Çıkış, omsagent için *çalışan* durumuyla aşağıdaki örneğe benzer olmalıdır:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Aracı günlüklerini denetleyin. Kapsayıcılı aracı dağıtıldığında, OMı komutlarını çalıştırarak ve aracının ve sağlayıcının sürümünü görüntüleyerek hızlı bir denetim çalıştırır. 

5. Aracının başarıyla dağıtıldığını doğrulamak için şu komutu çalıştırın: `kubectl logs omsagent-484hw --namespace=kube-system`

    Durum aşağıdaki örneğe benzemelidir:

    ```
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
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Hata iletileri

Aşağıdaki tabloda, kapsayıcılar için Azure Izleyicisini kullanırken karşılaşabileceğiniz bilinen hatalar özetlenmektedir.

| Hata iletileri  | Eylem |  
| ---- | --- |  
| Hata Iletisi `No data for selected filters`  | Yeni oluşturulan kümeler için izleme veri akışı oluşturulması biraz zaman alabilir. Kümenizde verilerin görünmesi için en az 10 ila 15 dakika bekleyin. |   
| Hata Iletisi `Error retrieving data` | Azure Kubernetes hizmet kümesi sistem durumu ve performans izleme için ayarlanırken, küme ve Azure Log Analytics çalışma alanı arasında bir bağlantı oluşturulur. Log Analytics çalışma alanı, kümenizin tüm izleme verilerini depolamak için kullanılır. Bu hata, Log Analytics çalışma alanınız silindiğinde oluşabilir. Çalışma alanının silinip silinmediğini ve olup olmadığını denetleyin, Kapsayıcınız için Azure Izleyici ile kümenizi izlemeyi yeniden etkinleştirmeniz ve var olan veya yeni bir çalışma alanı oluşturmanız gerekir. Yeniden etkinleştirmek için, küme için izlemeyi [devre dışı bırakmanız](container-insights-optout.md) ve kapsayıcılar Için Azure izleyicisini yeniden [etkinleştirmeniz](container-insights-enable-new-cluster.md) gerekir. |  
| az aks CLI aracılığıyla kapsayıcılar için Azure Izleyici eklendikten sonra `Error retrieving data` | `az aks cli`kullanarak izlemeyi etkinleştirdiğinizde, kapsayıcılar için Azure Izleyici düzgün şekilde dağıtılamaz. Çözümün dağıtılıp dağıtılmadığını denetleyin. Bunu yapmak için Log Analytics çalışma alanınıza gidin ve sol taraftaki bölmeden **çözümler** ' i seçerek çözümün kullanılabilir olup olmadığını görün. Bu sorunu çözmek için, [kapsayıcılar Için Azure izleyicisini dağıtma](container-insights-onboard.md) yönergelerini izleyerek çözümü yeniden dağıtmanız gerekir |  

Sorunu tanılamanıza yardımcı olması için [burada](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)bir sorun giderme betiği sunuyoruz.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Kapsayıcılar için Azure Izleyici aracı ReplicaSet pods, Azure olmayan Kubernetes kümesinde zamanlanmadı

Kapsayıcılar için Azure Izleyici aracı ReplicaSet pods, zamanlamaya ait çalışan (veya aracı) düğümlerinde aşağıdaki düğüm seçicilerini içeren bir bağımlılığa sahiptir:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Çalışan düğümlerinizde düğüm etiketleri ekli değilse, aracı ReplicaSet pods zamanlanmaz. Etiketi nasıl bağlayacağınız hakkında yönergeler için [Kubernetes etiket seçicileri atama](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) bölümüne bakın.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Performans grafikleri, Azure olmayan bir kümede CPU veya düğüm ve kapsayıcıların belleğini göstermez

Kapsayıcılar için Azure Izleyici aracı pods, performans ölçümlerini toplamak için düğüm aracısında Cadvizörü uç noktasını kullanır. Düğüm üzerindeki Kapsayıcılı aracının, performans ölçümlerini toplamak için kümedeki tüm düğümlerde `cAdvisor port: 10255` açılmasına izin verecek şekilde yapılandırıldığını doğrulayın.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Azure olmayan Kubernetes kümesi kapsayıcılar için Azure Izleyici 'de gösterilmiyor

Kapsayıcılar için Azure Izleyici 'de Azure Kubernetes kümesini görüntülemek için, bu Öngörüyi destekleyen Log Analytics çalışma alanında ve kapsayıcı öngörüleri çözüm kaynak **containerınsights (*çalışma alanı*)** üzerinde okuma erişimi gereklidir.

## <a name="next-steps"></a>Sonraki adımlar

Hem AKS küme düğümleri hem de pods için sistem durumu ölçümlerini yakalamak üzere izleme etkinken, bu durum ölçümleri Azure portal kullanılabilir. Kapsayıcılar için Azure Izleyici 'yi nasıl kullanacağınızı öğrenmek için bkz. [Azure Kubernetes hizmet durumunu görüntüleme](container-insights-analyze.md).