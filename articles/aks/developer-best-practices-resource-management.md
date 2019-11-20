---
title: Geliştirici en iyi uygulamaları-Azure Kubernetes hizmetlerinde kaynak yönetimi (AKS)
description: Azure Kubernetes Service (AKS) ' de kaynak yönetimine yönelik uygulama geliştiricisi en iyi uygulamalarını öğrenin
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: bfce7d77f214762a69857e74f0bb533ad1ce0f1b
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107648"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Uygulama geliştiricilerinin Azure Kubernetes Service (AKS) içindeki kaynakları yönetmesi için en iyi uygulamalar

Azure Kubernetes Service 'te (AKS) uygulama geliştirip çalıştırırken göz önünde bulundurmanız gereken birkaç anahtar alan vardır. Uygulamanızın dağıtımlarını nasıl yöneteceğiniz, sağladığınız hizmetlerin son kullanıcı deneyimini olumsuz etkileyebilir. Başarılı olmanıza yardımcı olması için, AKS 'te uygulama geliştirirken ve çalıştırırken kullanabileceğiniz en iyi uygulamaları aklınızda bulundurun.

Bu en iyi yöntemler makalesi, bir uygulama geliştirici perspektifinden kümenizi ve iş yüklerinizi nasıl çalıştıracağınızı odaklanır. En iyi yönetim uygulamaları hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service 'te (AKS) yalıtım ve kaynak yönetimi Için küme işletmeni en iyi uygulamaları][operator-best-practices-isolation]. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Pod kaynak istekleri ve limitleri nelerdir?
> * Geliştirme alanları ve Visual Studio Code uygulamalar geliştirme ve dağıtmaya yönelik yollar
> * Dağıtımlarla ilgili sorunları denetlemek için `kube-advisor` aracını kullanma

## <a name="define-pod-resource-requests-and-limits"></a>Pod kaynak isteklerini ve sınırlarını tanımlama

**En iyi Yöntem Kılavuzu** -YAML bildirimlerinizde tüm yığınlarda Pod isteklerini ve sınırlarını ayarlayın. AKS kümesi *kaynak kotalarını*kullanıyorsa, bu değerleri tanımlamadıysanız dağıtımınız reddedilebilir.

Bir AKS kümesindeki işlem kaynaklarını yönetmenin birincil yolu Pod isteklerini ve sınırlarını kullanmaktır. Bu istekler ve sınırlar, Kubernetes Scheduler 'ın bir pod 'ın hangi işlem kaynaklarına atanması gerektiğini bilmesini sağlar.

* **Pod CPU/bellek istekleri** , Pod 'un düzenli olarak ihtiyacı olan bir CPU ve bellek kümesi tanımlar.
    * Kubernetes Scheduler bir düğüme bir pod yerleştirmeyi denediğinde Pod istekleri, hangi düğümde zamanlama için kullanılabilir kaynak olduğunu tespit etmek için kullanılır.
    * Pod isteği ayarlamamaya, varsayılan olarak tanımlanan sınıra sahip olur.
    * Bu istekleri ayarlamak için uygulamanızın performansını izlemek çok önemlidir. Yetersiz istek yapılırsa, bir düğümün zamanlanması nedeniyle uygulamanız performans düşüklüğü alabilir. İstekler fazla tahmin alıyorsa, uygulamanız zamanlanan zorluk derecesini artırabilir.
* **Pod CPU/bellek sınırları** , Pod 'ın kullanabileceği en yüksek CPU ve bellek miktarıdır. Bu sınırlar, yetersiz kaynak nedeniyle düğüm kararsızlığı durumunda hangi yığınların sonlandırılanıp tanımlanabileceğine yardımcı olur. Uygun limitler olmadan, kaynak baskısı yükseltilmemiş olana kadar Pod kümesi sonlandırılacak.
    * Pod sınırları, Pod 'ın kaynak tüketimine ilişkin denetim kaybettiği ne zaman olduğunu tanımlamaya yardımcı olur. Sınır aşıldığında Pod, düğüm durumunun bakımını yapma ve düğümü paylaşan etkiyi en aza indirme için önceliklendirilir.
    * Pod sınırı ayarlamamaya, belirli bir düğümdeki en yüksek kullanılabilir değere göre varsayılan değer verilmez.
    * Düğümlerinizin destekleyebileceğinden daha yüksek bir pod sınırı ayarlama. Her AKS düğümü, çekirdek Kubernetes bileşenleri için ayarlanan bir CPU ve bellek miktarı ayırır. Uygulamanız, diğer yığınların başarıyla çalıştırılması için düğümde çok fazla kaynak kullanmayı deneyebilir.
    * Ayrıca, gün veya hafta boyunca uygulamanızın performansını farklı zamanlarda izlemek çok önemlidir. En yoğun talebin ne zaman olduğunu belirleme ve pod sınırlarını uygulamanın en fazla ihtiyaçlarını karşılamak için gereken kaynaklara hizalayın.

Pod belirtimleriniz, yukarıdaki bilgilere göre bu istekleri ve limitleri tanımlamak için **en iyi uygulamadır ve çok önemlidir** . Bu değerleri eklemezseniz, Kubernetes Scheduler, uygulamalarınızın zamanlama kararlarına yardımcı olması için ihtiyaç duyduğu kaynakları hesaba katmaz.

Zamanlayıcı, kaynakları yetersiz olan bir düğüme bir pod yerleştiriyor, uygulama performansı düşecek. Küme yöneticilerinin, kaynak isteklerini ve sınırlarını ayarlamanızı gerektiren bir ad alanı üzerinde *kaynak kotaları* ayarlaması önemle önerilir. Daha fazla bilgi için bkz. [AKS kümelerinde kaynak kotaları][resource-quotas].

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
    image: nginx:1.15.5
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

**En iyi yöntem kılavuzumuzu** geliştirme ekipleri, dev alanlarını kullanarak bir aks kümesine karşı dağıtım ve hata ayıklamalıdır. Bu geliştirme modeli, uygulama üretime dağıtılmadan önce rol tabanlı erişim denetimleri, ağ veya depolama gereksinimlerinizin uygulandığından emin olur.

Azure Dev Spaces ile, uygulamaları doğrudan bir AKS kümesine karşı geliştirin, hata ayıklayın ve test edersiniz. Bir ekip içindeki geliştiriciler, uygulama yaşam döngüsü boyunca derleme ve test yapmak için birlikte çalışır. Visual Studio veya Visual Studio Code gibi mevcut araçları kullanmaya devam edebilirsiniz. Bir AKS kümesinde uygulamayı çalıştırma ve hata ayıklama seçeneği sunan dev alanları için bir uzantı yüklenir:

![Dev Spaces ile bir AKS kümesindeki uygulamalarda hata ayıklama](media/developer-best-practices-resource-management/dev-spaces-debug.png)

Geliştirme alanları ile bu tümleşik geliştirme ve test süreci, [minikube][minikube]gibi yerel test ortamları gereksinimini azaltır. Bunun yerine, bir AKS kümesinde geliştirme ve test edersiniz. Bu küme güvenli hale getirilir ve bir kümeyi mantıksal olarak yalıtmak için ad alanları kullanmanın önceki bölümünde belirtildiği gibi yalıtılabilir. Uygulamalarınız üretime dağıtılmaya hazırsanız, geliştirmenin hepsi gerçek bir AKS kümesine karşı yapıldığından güvenle dağıtım yapabilirsiniz.

Azure dev Spaces, Linux Pod ve düğümlerinde çalışan uygulamalarla kullanılmak üzere tasarlanmıştır.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Kubernetes için Visual Studio Code uzantısını kullanma

**En iyi Yöntem Kılavuzu** -YAML bildirimleri yazarken Kubernetes için vs Code uzantısını yükler ve kullanın. Tümleşik dağıtım çözümü uzantısını da kullanabilirsiniz; Bu, AKS kümesiyle sık sık etkileşimde bulunan uygulama sahiplerine yardımcı olabilir.

[Kubernetes için Visual Studio Code uzantısı][vscode-kubernetes] , aks 'e uygulama geliştirmenize ve dağıtmanıza yardımcı olur. Uzantı, Kubernetes kaynakları ve HELI grafikleri ve şablonları için IntelliSense sağlar. Ayrıca, VS Code içinden Kubernetes kaynaklarını da tarayabilirsiniz, dağıtabilir ve düzenleyebilirsiniz. Uzantı Ayrıca, Pod belirtimlerinde ayarlanan kaynak istekleri veya sınırlar için bir IntelliSense denetimi sağlar:

![Eksik bellek sınırları hakkında Kubernetes uyarısı için VS Code uzantısı](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Kuin-Advisor ile uygulama sorunlarını düzenli olarak denetleme

**En iyi Yöntem Kılavuzu** -kümenizdeki sorunları algılamak için `kube-advisor` açık kaynak aracının en son sürümünü düzenli olarak çalıştırın. Mevcut bir aks kümesinde kaynak kotaları uygularsanız, kaynak isteği ve sınırları tanımlı olmayan Pod 'yi bulmak için önce `kube-advisor` çalıştırın.

[Kuin-Advisor][kube-advisor] Aracı, bir Kubernetes kümesini tarayan ve bulduğu sorunlar hakkında rapor veren ilişkili bir aks açık kaynak projesidir. Tek bir faydalı denetim, kaynak istekleri ve sınırları olmayan Pod 'yi belirlemektir.

Kumak-Advisor Aracı, Windows Uygulamaları ve Linux uygulamaları için pod özelliklerinin yanı sıra kaynak isteği ve limitleri rapor edebilir, ancak Kuto-Advisor aracının kendisi bir Linux pod üzerinde zamanlanmalıdır. Pod 'un yapılandırmasındaki [düğüm seçicisini][k8s-node-selector] kullanarak belirli bir işletim sistemine sahip bir düğüm havuzunda çalışacak bir pod zamanlayabilirsiniz.

Birçok geliştirme ekiplerini ve uygulamayı barındıran bir aks kümesinde, bu kaynak istekleri ve limitler kümesi olmadan Pod 'yi izlemek zor olabilir. En iyi uygulama olarak, AKS kümelerinizde `kube-advisor` düzenli olarak çalıştırın.

## <a name="next-steps"></a>Sonraki adımlar

Bu en iyi yöntemler, küme operatörü perspektifinden kümenizi ve iş yüklerinizi nasıl çalıştıracağınızı odaklanan makaledir. En iyi yönetim uygulamaları hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service 'te (AKS) yalıtım ve kaynak yönetimi Için küme işletmeni en iyi uygulamaları][operator-best-practices-isolation].

Bu en iyi uygulamalardan bazılarını uygulamak için aşağıdaki makalelere bakın:

* [Geliştirme alanları ile geliştirme][dev-spaces]
* [Kuto Danışmanı ile ilgili sorunlar olup olmadığını denetleyin][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
