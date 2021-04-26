---
title: Kaynak yönetimi en iyi uygulamaları
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ' de kaynak yönetimine yönelik uygulama geliştiricisi en iyi uygulamalarını öğrenin
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105281"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Uygulama geliştiricilerinin Azure Kubernetes Service (AKS) içindeki kaynakları yönetmesi için en iyi uygulamalar

Azure Kubernetes Service 'te (AKS) uygulama geliştirip çalıştırırken göz önünde bulundurmanız gereken birkaç anahtar alan vardır. Uygulamanızın dağıtımlarını nasıl yöneteceğiniz, sağladığınız hizmetlerin son kullanıcı deneyimini olumsuz etkileyebilir. Başarılı olmak için, AKS 'de uygulama geliştirirken ve çalıştırırken kullanabileceğiniz en iyi uygulamaları aklınızda bulundurun.

Bu makale, bir uygulama geliştirici perspektifinden kümenizi ve iş yüklerinizi çalıştırmaya odaklanır. En iyi yönetim uygulamaları hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service 'te (AKS) yalıtım ve kaynak yönetimi Için küme işletmeni en iyi uygulamaları][operator-best-practices-isolation]. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Pod kaynak istekleri ve sınırları.
> * Kubernetes ve Visual Studio Code köprü ile uygulama geliştirme ve dağıtmaya yönelik yollar.
> * `kube-advisor`Dağıtımlarla ilgili sorunları denetlemek için aracı kullanma.

## <a name="define-pod-resource-requests-and-limits"></a>Pod kaynak isteklerini ve sınırlarını tanımlama

> **En iyi yöntemler kılavuzu**
> 
> YAML bildirimlerinizde tüm FID 'ler üzerinde pod isteklerini ve sınırlarını ayarlayın. AKS kümesi *kaynak kotalarını* kullanıyorsa ve bu değerleri tanımlamadıysanız, dağıtımınız reddedilebilir.

Bir AKS kümesi içindeki işlem kaynaklarını yönetmek için pod isteklerini ve sınırlarını kullanın. Pod istekleri ve limitleri Kubernetes Scheduler 'a bir pod 'a atanacak kaynakları bildirir.

### <a name="pod-cpumemory-requests"></a>Pod CPU/bellek istekleri
*Pod istekleri* , Pod 'un düzenli olarak ihtiyacı olan bir CPU ve bellek kümesi tanımlar.

Pod belirtimleriniz, yukarıdaki bilgilere göre bu istekleri ve limitleri tanımlamak için **en iyi uygulamadır ve çok önemlidir** . Bu değerleri eklemezseniz, Kubernetes Scheduler, uygulamalarınızın zamanlama kararlarına yardımcı olması için ihtiyaç duyduğu kaynakları hesaba katmaz.

Pod isteklerini ayarlamak için uygulamanızın performansını izleyin. 
* Pod isteklerini daha fazla tahmin ediyorsanız, bir düğüm üzerinde işlem zamanlamasından dolayı uygulamanız düşürülmüş performans alabilir. 
* İstekler fazla tahmin alıyorsa, uygulamanız zamanlanan zorluk derecesini artırabilir.

### <a name="pod-cpumemory-limits"></a>Pod CPU/bellek sınırları * * 
*Pod sınırları* , Pod 'ın kullanabileceği en yüksek CPU ve bellek miktarını ayarlar. 

* Yetersiz kaynaklar nedeniyle düğümler kararsız hale geldiğinde hangi yığınların sonlandırılanıyor *olduğunu tanımlar.* Doğru sınır kümesi olmadan, kaynak baskısı yükseltilmemiş olana kadar Pod sonlandırılacak. 
* Pod, *CPU sınırını* düzenli aralıklarla aşacağından, Pod, CPU sınırını aşmayacak şekilde sonlandırılamaz. 

Pod sınırları, Pod 'ın kaynak tüketimine ilişkin denetim kayıp olduğunu tanımlar. Sınırı aştığında Pod, sonlandırma için işaretlenir. Bu davranış, düğüm durumunu korur ve düğümü paylaşan bir etkiyi en aza indirir. Pod sınırı ayarlamamaya, belirli bir düğümdeki en yüksek kullanılabilir değere göre varsayılan değer verilmez.

Düğümlerinizin destekleyebileceğinden daha yüksek bir pod sınırı ayarlamaktan kaçının. Her AKS düğümü, çekirdek Kubernetes bileşenleri için ayarlanan bir CPU ve bellek miktarı ayırır. Uygulamanız, diğer yığınların başarıyla çalıştırılması için düğümde çok fazla kaynak kullanmayı deneyebilir.

Uygulamanızın performansını, gün veya hafta boyunca farklı zamanlarda izleyin. En yüksek talep zamanlarını belirleme ve pod sınırlarını, en fazla ihtiyaçları karşılamak için gereken kaynaklara hizalayın.

> [!IMPORTANT]
>
> Pod belirtimleriniz içinde, bu istekleri ve sınırları yukarıdaki bilgilere göre tanımlayın. Bu değerlerin dahil olmasının başarısız olması, Kubernetes Scheduler 'ın uygulamalarınızın zamanlama kararlarına yardımcı olması için ihtiyaç duyduğu kaynaklar için hesaplamasını önler.

Zamanlayıcı, kaynakları yetersiz olan bir düğüme bir pod yerleştiriyor, uygulama performansı düşecek. Küme yöneticileri, kaynak isteklerini ve sınırlarını ayarlamanızı gerektiren bir ad alanı üzerinde *kaynak kotaları* ayarlaması **gerekir** . Daha fazla bilgi için bkz. [AKS kümelerinde kaynak kotaları][resource-quotas].

Bir CPU isteği veya sınırı tanımladığınızda, değer CPU birimlerinde ölçülür. 
* *1,0* CPU, düğüm üzerindeki bir temel sanal CPU çekirdeğe eşit. 
    * GPU 'Lar için aynı ölçü kullanılır.
* Miliçekirdekte ölçülen kesirleri tanımlayabilirsiniz. Örneğin, *100 milyon* , temel alınan bir vCPU Core *0,1* ' dir.

Tek bir NGıNX pod için aşağıdaki temel örnekte Pod, *100* GB CPU süresi ve *128mı* bellek ister. Pod için kaynak sınırları *250E* CPU ve *256mı* bellek olarak ayarlanır:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Kaynak ölçümleri ve atamaları hakkında daha fazla bilgi için bkz. [kapsayıcılar için işlem kaynaklarını yönetme][k8s-resource-limits].

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>AKS kümesinde uygulama geliştirme ve hata ayıklama

> **En iyi yöntemler kılavuzu** 
>
> Geliştirme ekipleri, Kubernetes ile Köprü kullanarak bir AKS kümesine karşı hata ayıklamalıdır.

Kubernetes Köprüsü sayesinde, uygulamaları doğrudan bir AKS kümesinde geliştirebilir, hatalarını ayıklayabilir ve test edebilirsiniz. Bir ekip içindeki geliştiriciler uygulama yaşam döngüsü boyunca derleme ve test etme olanağı sağlar. Visual Studio veya Visual Studio Code gibi mevcut araçları, Kubernetes uzantılı köprü ile kullanmaya devam edebilirsiniz. 

Kubernetes ile tümleşik geliştirme ve test süreci kullanmak, [minikube][minikube]gibi yerel test ortamları gereksinimini azaltır. Bunun yerine, güvenli ve Yalıtılmış kümeler bile bir AKS kümesi geliştirip test edersiniz. 

> [!NOTE]
> Kubernetes Köprüsü, Linux Pod ve düğümlerinde çalışan uygulamalarla kullanılmak üzere tasarlanmıştır.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Kubernetes için Visual Studio Code (VS Code) uzantısını kullanma

> **En iyi yöntemler kılavuzu** 
>
> YAML bildirimleri yazarken Kubernetes için VS Code uzantısını yükleyip kullanın. Tümleşik dağıtım çözümü uzantısını da kullanabilirsiniz; Bu, AKS kümesiyle sık sık etkileşimde bulunan uygulama sahiplerine yardımcı olabilir.

[Kubernetes için Visual Studio Code uzantısı][vscode-kubernetes] , aks 'e uygulama geliştirmenize ve dağıtmanıza yardımcı olur. Uzantı şunları sağlar:
* Kubernetes kaynakları, HELI grafikleri ve şablonlar için IntelliSense. 
* VS Code içinden Kubernetes kaynakları için tarama, dağıtma ve düzenleme özellikleri. 
* Pod belirtimlerinde ayarlanan kaynak istekleri veya sınırlar için bir IntelliSense denetimi:

    ![Eksik bellek sınırları hakkında Kubernetes uyarısı için VS Code uzantısı](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Kuin-Advisor ile uygulama sorunlarını düzenli olarak denetleme

> **En iyi yöntemler kılavuzu** 
> 
> `kube-advisor`Kümenizdeki sorunları algılamak için, açık kaynak aracının en son sürümünü düzenli olarak çalıştırın. `kube-advisor`Kaynak istekleri ve sınırları tanımlı olmayan Pod 'leri bulmak için, mevcut bir aks kümesine kaynak kotaları uygulamadan önce ' i çalıştırın.

[Kuin-Advisor][kube-advisor] Aracı, bir Kubernetes kümesini tarayan ve tanımlanan sorunlar hakkında rapor veren ilişkili bir aks açık kaynaklı projem. Tek bir faydalı denetim, kaynak istekleri ve sınırları olmadan Pod 'yi belirlemektir.

Araç, `kube-advisor` kaynak istekleri ve Windows ve Linux uygulamaları Için Pod belirtimlerde bulunmayan sınırlar hakkında rapor verebilir, ancak bunların `kube-advisor` kendisi bir Linux pod üzerinde zamanlanmalıdır. Pod 'un, belirli bir işletim sistemi ile bir düğüm havuzunda çalışmasını zamanlamak için pod yapılandırmasında bir [düğüm seçici][k8s-node-selector] kullanın.

Birçok geliştirme ekiplerini ve uygulamayı barındıran bir aks kümesinde, kaynak isteklerini ve sınırlarını kullanarak Pod 'yi izlemeyi daha kolay bulacaksınız. En iyi uygulama olarak `kube-advisor` AKS kümelerinizde düzenli olarak çalışır.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, küme operatörü perspektifinden kümenizi ve iş yüklerinizi çalıştırma konusunda odaklanılmıştır. En iyi yönetim uygulamaları hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service 'te (AKS) yalıtım ve kaynak yönetimi Için küme işletmeni en iyi uygulamaları][operator-best-practices-isolation].

Bu en iyi uygulamalardan bazılarını uygulamak için aşağıdaki makalelere bakın:

* [Kubernetes ile Köprü ile geliştirme][btk]
* [Kuto Danışmanı ile ilgili sorunlar olup olmadığını denetleyin][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
