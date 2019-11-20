---
title: Azure Kubernetes Service (AKS) içinde kured ile Linux düğümlerini güncelleştirme ve yeniden başlatma
description: Linux düğümlerini güncelleştirmeyi öğrenin ve Azure Kubernetes Service (AKS) içinde kured ile otomatik olarak yeniden başlatın
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 580d1316c2bfc6514a148ed6fba78a8e77bd880e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67614914"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki Linux düğümlerine güvenlik ve çekirdek güncelleştirmelerini uygulama

Kümelerinizi korumak için güvenlik güncelleştirmeleri AKS 'deki Linux düğümlerine otomatik olarak uygulanır. Bu güncelleştirmeler, işletim sistemi güvenlik düzeltmelerini veya çekirdek güncelleştirmelerini içerir. Bu güncelleştirmelerden bazılarının işlemin tamamlanabilmesi için bir düğüm yeniden başlatması gerekir. AKS, güncelleştirme işlemini tamamlamaya yönelik olarak bu Linux düğümlerini otomatik olarak yeniden başlatır.

Windows Server düğümlerini (Şu anda AKS ' de önizleme aşamasında) güncel tutma işlemi biraz farklıdır. Windows Server düğümleri günlük güncelleştirme almaz. Bunun yerine, en son temel pencere sunucu görüntüsü ve düzeltme ekleriyle yeni düğümler dağıtan bir AKS yükseltmesi gerçekleştirirsiniz. Windows Server düğümleri kullanan AKS kümelerinde bkz. [aks 'de düğüm havuzunu yükseltme][nodepool-upgrade].

Bu makalede, yeniden başlatma gerektiren Linux düğümlerini izlemek için açık kaynaklı [kured (Kubernetes önyükleme cini)][kured] nasıl kullanılacağı gösterilmektedir ve ardından çalışan Pod ve düğüm yeniden başlatma işleminin yeniden çizelgeini otomatik olarak işler.

> [!NOTE]
> `Kured`, dalgalı bir proje tarafından açık kaynaklı bir projem. AKS 'deki bu proje için destek en iyi çaba temelinde sunulmaktadır. #Weave topluluk bolluk kanalında ek destek bulunabilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca Azure CLı sürüm 2.0.59 veya üzeri yüklü ve yapılandırılmış olmalıdır. Sürümü `az --version` bulmak için ' i çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse bkz. [Azure CLI 'Yı yüklemek][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>AKS düğüm güncelleştirme deneyimini anlayın

AKS kümesinde, Kubernetes düğümleriniz Azure sanal makineleri (VM 'Ler) olarak çalışır. Bu Linux tabanlı VM 'Ler, işletim sistemi tarafından her gece güncelleştirmeleri otomatik olarak denetleyecek şekilde yapılandırılmış bir Ubuntu görüntüsü kullanır. Güvenlik veya çekirdek güncelleştirmeleri varsa, bunlar otomatik olarak indirilir ve yüklenir.

![Kured ile AKS düğüm güncelleştirme ve yeniden başlatma işlemi](media/node-updates-kured/node-reboot-process.png)

Çekirdek güncelleştirmeleri gibi bazı güvenlik güncelleştirmeleri, işlemi tamamlamak için bir düğümün yeniden başlatılmasını gerektirir. Yeniden başlatma gerektiren bir Linux düğümü, */var/Run/reboot-adlı*bir dosya oluşturur. Bu yeniden başlatma işlemi otomatik olarak gerçekleşmez.

Düğüm yeniden başlatmaları işlemek için kendi iş akışlarınızı ve işlemlerinizi kullanabilir veya işlemi düzenlemek için `kured` kullanabilirsiniz. İle `kured`, kümedeki her Linux düğümünde Pod çalıştıran bir [DaemonSet][DaemonSet] dağıtılır. DaemonSet 'deki bu düğüm, */var/Run/reboot-Required* dosyasının varlığını izlemek ve sonra düğümleri yeniden başlatmak için bir işlem başlatır.

### <a name="node-upgrades"></a>Düğüm yükseltmeleri

AKS 'de bir kümeyi *yükseltmenize* imkan tanıyan ek bir süreç vardır. Yükseltme, genellikle düğüm güvenlik güncelleştirmeleri uygulamamaları değil, Kubernetes 'in daha yeni bir sürümüne geçiş yapmak için kullanılır. AKS yükseltmesi aşağıdaki eylemleri gerçekleştirir:

* Yeni bir düğüm, en son güvenlik güncelleştirmeleri ve Kubernetes sürümü uygulanmış şekilde dağıtılır.
* Eski bir düğüm, ve drenaj olur.
* Pods 'ler yeni düğüm üzerinde zamanlanır.
* Eski düğüm silinir.

Yükseltme olayı sırasında aynı Kubernetes sürümünde kalamıyorum. Kubernetes 'in daha yeni bir sürümünü belirtmeniz gerekir. En son Kubernetes sürümüne yükseltmek için [AKS kümenizi yükseltebilirsiniz][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>AKS kümesinde kured dağıtma

`kured` DaemonSet dağıtmak için, GitHub proje sayfasından aşağıdaki örnek YAML bildirimini uygulayın. Bu bildirim, bir rol ve küme rolü, bağlamalar ve bir hizmet hesabı oluşturur, ardından aks kümeleri 1,9 `kured` veya üstünü destekleyen sürüm 1.1.0 kullanarak DaemonSet dağıtır.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.2.0/kured-1.2.0-dockerhub.yaml
```

Ayrıca `kured`, Prometheus veya bolluk ile tümleştirme gibi ek parametreleri de yapılandırabilirsiniz. Ek yapılandırma parametreleri hakkında daha fazla bilgi için bkz. [kured yükleme belgeleri][kured-install].

## <a name="update-cluster-nodes"></a>Küme düğümlerini Güncelleştir

Varsayılan olarak, AKS 'deki Linux düğümleri her akşam güncelleştirme olup olmadığını denetler. Beklemek istemiyorsanız, doğru şekilde `kured` çalıştığını denetlemek için el ile bir güncelleştirme gerçekleştirebilirsiniz. İlk olarak, [AKS düğümlerinizin birine SSH][aks-ssh]eklemek için adımları izleyin. Linux düğümüne SSH bağlantısı olduktan sonra, güncelleştirmeleri denetleyin ve bunları şu şekilde uygulayın:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Bir düğümün yeniden başlatılmasını gerektiren güncelleştirmeler uygulanmışsa, */var/Run/reboot-dosyasına*bir dosya yazılır. `Kured`Varsayılan olarak her 60 dakikada bir yeniden başlatma gerektiren düğümleri denetler.

## <a name="monitor-and-review-reboot-process"></a>Yeniden başlatma işlemini izleme ve gözden geçirme

DaemonSet içindeki çoğaltmalardan biri, düğüm yeniden başlatmanın gerekli olduğunu algıladığında, düğüm üzerinde Kubernetes API 'SI aracılığıyla bir kilit yerleştirilir. Bu kilit, düğüm üzerinde ek yığınların zamanlanmasını önler. Kilit ayrıca tek seferde yalnızca bir düğümün yeniden başlatılması gerektiğini gösterir. Düğüm devre dışı olduğunda, düğüm oluşturma işlemi düğümden alınır ve düğüm yeniden başlatılır.

[Kubectl Get Nodes][kubectl-get-nodes] komutunu kullanarak düğümlerin durumunu izleyebilirsiniz. Aşağıdaki örnek çıktı, bir düğümü yeniden başlatma işlemi için hazırlanırken *SchedulingDisabled* durumuna sahip bir düğümü göstermektedir:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

Güncelleştirme işlemi tamamlandıktan sonra, `--output wide` parametresi ile [kubectl Get Nodes][kubectl-get-nodes] komutunu kullanarak düğümlerin durumunu görüntüleyebilirsiniz. Bu ek çıktı, aşağıdaki örnek çıktıda gösterildiği gibi, temeldeki düğümlerin *çekırdek sürümünde* bir farklılık görmenizi sağlar. *Aks-nodepool1-28993262-0* önceki bir adımda güncelleştirildi ve çekirdek sürümü *4.15.0-1039-Azure*' i gösterir. Güncelleştirilmemiş *aks-nodepool1-28993262-1* düğümü, çekirdek sürümü *4.15.0-1037-Azure*' i gösterir.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, güvenlik güncelleştirme sürecinin `kured` bir parçası olarak Linux düğümlerini otomatik olarak yeniden başlatmak için nasıl kullanılacağı ayrıntılı olarak açıklanır. En son Kubernetes sürümüne yükseltmek için [AKS kümenizi yükseltebilirsiniz][aks-upgrade].

Windows Server düğümleri kullanan AKS kümelerinde bkz. [aks 'de düğüm havuzunu yükseltme][nodepool-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
