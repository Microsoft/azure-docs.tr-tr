---
title: Kapsayıcı öngörülerini giderme | Microsoft Docs
description: Bu makalede, kapsayıcı öngörüleri ile ilgili sorunları nasıl giderebileceğiniz ve giderebileceğiniz açıklanır.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: b7618e9073308da67a8e17c82375a0f05925a542
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627124"
---
# <a name="troubleshooting-container-insights"></a>Kapsayıcı öngörüleri sorunlarını giderme

Azure Kubernetes Service (AKS) kümenizi kapsayıcı öngörüleri ile izlemeyi yapılandırdığınızda, veri toplamayı veya raporlama durumunu engellemeye yönelik bir sorunla karşılaşabilirsiniz. Bu makalede bazı yaygın sorunlar ve sorun giderme adımları ayrıntılı olarak anlatılmaktadır.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Ekleme veya güncelleştirme işlemi sırasında yetkilendirme hatası

Kapsayıcı öngörülerini desteklemek için kapsayıcı içgörüler veya bir kümeyi güncelleştirirken, aşağıdaki gibi bir hata alabilirsiniz. *<kullanıcının kimliği> ' <kullanıcının objectıd> ', kapsam üzerinde ' Microsoft. Authorization/Roleatamalar/Write ' eylemini gerçekleştirme yetkilendirmesi* yok

Ekleme veya güncelleştirme işlemi sırasında, küme kaynağında **Izleme ölçümleri yayımcı** rolü atamasının verilmesi denenir. Kapsayıcı öngörülerini etkinleştirme işlemini başlatan kullanıcı veya ölçüm koleksiyonunu destekleyen güncelleştirme, AKS kümesi kaynak kapsamında **Microsoft. Authorization/Roleatamalar/Write** iznine sahip olmalıdır. Yalnızca **sahip** ve **Kullanıcı erişimi Yöneticisi** yerleşik rollerinin üyelerine bu izne erişim verilir. Güvenlik ilkeleriniz parçalı düzey izinler atanmasını gerektiriyorsa, [özel rolleri](../../role-based-access-control/custom-roles.md) görüntülemenizi ve bunu gerektiren kullanıcılara atamanızı öneririz.

Ayrıca, aşağıdaki adımları uygulayarak bu rolü Azure portal el ile de verebilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Azure portalının sol alt köşesinde bulunan **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Kubernetes** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Azure Kubernetes**' i seçin.
3. Kubernetes kümeleri listesinde, listeden bir tane seçin.
2. Sol taraftaki menüden **erişim denetimi (IAM)** öğesine tıklayın.
3. Bir rol ataması eklemek için **+ Ekle** ' yi seçin ve **izleme ölçümleri yayımcısı** rolünü seçin ve yalnızca abonelikte tanımlanan kümeler hizmet sorumluları ' nda sonuçları filtrelemek Için **Seç** kutusu tür **aks** ' i seçin. Bu kümeye özgü listeden birini seçin.
4. Rolü atamaya son vermek için **Kaydet** ' i seçin.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Kapsayıcı öngörüleri etkin ancak hiçbir bilgi bildirilmiyor

Kapsayıcı öngörüleri başarıyla etkinleştirilip yapılandırılmışsa, ancak durum bilgilerini görüntüleyemez veya bir günlük sorgusundan hiçbir sonuç döndürülmezse, bu adımları izleyerek sorunu tanılayabilirsiniz:

1. Şu komutu çalıştırarak aracının durumunu denetleyin:

    `kubectl get ds omsagent --namespace=kube-system`

    Çıktı, doğru şekilde dağıtıldığını belirten aşağıdaki örneğe benzer olmalıdır:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Windows Server düğümleriniz varsa, komutunu çalıştırarak aracının durumunu denetleyin:

    `kubectl get ds omsagent-win --namespace=kube-system`

    Çıktı, doğru şekilde dağıtıldığını belirten aşağıdaki örneğe benzer olmalıdır:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Şu komutu kullanarak aracı sürümü *06072018* veya üzeri ile dağıtım durumunu denetleyin:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Çıktı, doğru şekilde dağıtıldığını belirten aşağıdaki örneğe benzer olmalıdır:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Komutunu kullanarak çalıştığını doğrulamak için pod 'un durumunu denetleyin: `kubectl get pods --namespace=kube-system`

    Çıkış, omsagent için *çalışan* durumuyla aşağıdaki örneğe benzer olmalıdır:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Hata iletileri

Aşağıdaki tabloda, kapsayıcı öngörülerini kullanırken karşılaşabileceğiniz bilinen hatalar özetlenmektedir.

| Hata iletileri  | Eylem |
| ---- | --- |
| Hata Iletisi `No data for selected filters`  | Yeni oluşturulan kümelerin veri akışını izlemek için bir süre beklemeniz gerekebilir. Kümenizde verilerin görünmesi için en az 10 ila 15 dakika bekleyin. |
| Hata Iletisi `Error retrieving data` | Azure Kubernetes hizmet kümesi sistem durumu ve performans izleme için ayarlanırken, küme ve Azure Log Analytics çalışma alanı arasında bir bağlantı oluşturulur. Log Analytics çalışma alanı, kümenizin tüm izleme verilerini depolamak için kullanılır. Bu hata, Log Analytics çalışma alanınız silindiğinde oluşabilir. Çalışma alanının silinip silinmediğini ve olup olmadığını kontrol edin, bu arada, kapsayıcı öngörüleri ile kümenizi izlemeyi yeniden etkinleştirmeniz ve var olan veya yeni bir çalışma alanı oluşturmanız gerekir. Yeniden etkinleştirmek için, küme için izlemeyi [devre dışı bırakmanız](container-insights-optout.md) ve kapsayıcı öngörülerini yeniden [etkinleştirmeniz](container-insights-enable-new-cluster.md) gerekir. |
| `Error retrieving data` az aks CLI aracılığıyla kapsayıcı öngörüleri eklendikten sonra | Kullanılarak izlemeyi etkinleştirmek için `az aks cli` , kapsayıcı öngörüleri düzgün şekilde dağıtılamaz. Çözümün dağıtılıp dağıtılmadığını denetleyin. Doğrulamak için Log Analytics çalışma alanınıza gidin ve sol taraftaki bölmeden **çözümler** ' i seçerek çözümün kullanılabilir olup olmadığını görün. Bu sorunu çözmek için, [kapsayıcı öngörülerini dağıtma](container-insights-onboard.md) yönergelerini izleyerek çözümü yeniden dağıtmanız gerekir |

Sorunu tanılamanıza yardımcı olması için bir [sorun giderme betiği](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot)sunuyoruz.

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Kapsayıcı öngörüleri aracı ReplicaSet pods, Azure olmayan Kubernetes kümesinde zamanlanmadı

Container Insights Aracısı ReplicaSet pods, zamanlama için çalışan (veya aracı) düğümlerinde aşağıdaki düğüm seçicilerini içeren bir bağımlılığa sahiptir:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Çalışan düğümlerinizde düğüm etiketleri ekli değilse, aracı ReplicaSet pods zamanlanmaz. Etiketi nasıl bağlayacağınız hakkında yönergeler için [Kubernetes etiket seçicileri atama](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) bölümüne bakın.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Performans grafikleri, Azure olmayan bir kümede CPU veya düğüm ve kapsayıcıların belleğini göstermez

Container Insights Aracısı pods, performans ölçümlerini toplamak için düğüm aracısında Cadvizörü uç noktasını kullanır. Düğüm üzerindeki Kapsayıcılı aracının, `cAdvisor port: 10255` performans ölçümlerini toplamak için kümedeki tüm düğümlerde açılmasına izin verecek şekilde yapılandırıldığını doğrulayın.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>Azure olmayan Kubernetes kümesi, kapsayıcı öngörülerine gösterilmiyor

Kapsayıcı öngörüleri içindeki Azure Kubernetes kümesini görüntülemek için, bu Öngörüyi destekleyen Log Analytics çalışma alanında ve kapsayıcı öngörüleri çözüm kaynak **containerınsights (*çalışma alanı*)** üzerinde okuma erişimi gereklidir.

## <a name="metrics-arent-being-collected"></a>Ölçümler toplanmıyor

1. Kümenin [özel ölçümler için desteklenen bir bölgede](../essentials/metrics-custom-overview.md#supported-regions)olduğunu doğrulayın.

2. Aşağıdaki CLı komutunu kullanarak **Izleme ölçümleri yayımcı** rolü atamasının mevcut olduğunu doğrulayın:

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    MSI ile kümeler için, her izleme etkinleştirildiğinde ve devre dışı bırakıldığında, omsagent için istemci kimliğini atanan kullanıcı, geçerli MSI İstemci kimliğinde rol atamasının mevcut olması gerekir. 

3. Azure Active Directory Pod kimliği etkin ve MSI kullanan kümeler için:

   - Kubernetes.Azure.com/ManagedBy etiketini doğrulayın: aşağıdaki komutu kullanarak omsagent pod üzerinde **aks**  mevcuttur:

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Üzerinde desteklenen yöntemlerden birini kullanarak Pod kimliği etkinken özel durumların etkinleştirildiğini doğrulayın https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity .

        Doğrulamak için aşağıdaki komutu çalıştırın:

        `kubectl get AzurePodIdentityException -A -o yaml`

        Aşağıdakine benzer bir çıktı almalısınız:

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```



## <a name="next-steps"></a>Sonraki adımlar

Hem AKS küme düğümleri hem de pods için sistem durumu ölçümlerini yakalamak üzere izleme etkinken, bu durum ölçümleri Azure portal kullanılabilir. Kapsayıcı öngörülerini nasıl kullanacağınızı öğrenmek için bkz. [Azure Kubernetes hizmet durumunu görüntüleme](container-insights-analyze.md).
