---
title: Kapsayıcı örnekleri ve kapsayıcı düzenlemesi
description: Azure Container Instances 'ın kapsayıcı düzenleyicilerinden nasıl etkileşime gireceğini anlayın.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: c7faeecfcc3a1d006cc923b48339a1242a0b9e9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148601"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances ve kapsayıcı yöneticileri

Küçük boyut ve uygulama yönü nedeniyle, kapsayıcılar çevik teslim ortamları ve mikro hizmet tabanlı mimarilere uygundur. Çok sayıda kapsayıcıyı otomatikleştirme ve yönetme görevi ve bunların nasıl etkileşimde bulunduğu, *düzenleme* olarak bilinir. Popüler kapsayıcı yöneticileri Kubernetes, DC/OS ve Docker Sısınma içerir.

Azure Container Instances, düzenleme platformlarının bazı temel zamanlama yeteneklerini sağlar. Ayrıca, bu platformların sağladığı daha yüksek değerli Hizmetleri kapsamadığından, Azure Container Instances bunlara yönelik tamamlayıcı olabilir. Bu makalede, Azure Container Instances ne tür tanıtıcılarının kapsamı ve tam kapsayıcı yöneticileri ile nasıl etkileşime girebileceği açıklanmaktadır.

## <a name="traditional-orchestration"></a>Geleneksel düzenleme

Orchestration 'un standart tanımı aşağıdaki görevleri içerir:

- **Zamanlama**: kapsayıcı görüntüsü ve kaynak isteği verildiğinde, kapsayıcının çalıştırılacağı uygun bir makine bulun.
- **Benzeşim/benzeşim**: bir kapsayıcı kümesinin birbirini yakın bir şekilde (performans için) veya yeterince uzakta (kullanılabilirlik için) çalışacağını belirtin.
- **Sistem durumu izleme**: kapsayıcı başarısızlıklarını izleyin ve bunları otomatik olarak yeniden zamanlayın.
- **Yük devretme**: her makinede nelerin çalıştığını izleyin ve başarısız olan makinelerden gelen kapsayıcıları sağlıklı düğümlere yeniden zamanlayın.
- **Ölçeklendirme**: kapsayıcı örneklerini el ile veya otomatik olarak eşleşecek şekilde ekleyin ya da kaldırın.
- **Ağ**: birden çok konak makinesi arasında iletişim kurmak için kapsayıcıları koordine etmek üzere bir kaplama ağı sağlayın.
- **Hizmet bulma**: kapsayıcıları, konak makineler arasında hareket ETTIKLERI ve IP adreslerini değiştirerek bile otomatik olarak konumlandırmak için etkinleştirin.
- **Eşgüdümlü uygulama yükseltmeleri**: uygulama kapalı kalma süresini önlemek için kapsayıcı yükseltmelerini yönetin ve bir sorun varsa geri alma özelliğini etkinleştirin.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Azure Container Instances düzenleme: katmanlı bir yaklaşım

Azure Container Instances, tek bir kapsayıcıyı çalıştırmak için gerekli olan tüm zamanlama ve yönetim özelliklerini sağlayarak düzenleme için katmanlı bir yaklaşım sağlar ve bu sayede Orchestrator platformları onun üzerine çok Kapsayıcılı görevleri yönetmesine izin verir.

Kapsayıcı örnekleri için temeldeki altyapı Azure tarafından yönetildiğinden, bir Orchestrator platformunun, tek bir kapsayıcının çalıştırılacağı uygun bir konak makinesini bulmayla kendine sorun olması gerekmez. Bulutun sürekliliği, her zaman kullanılabilir olmasını sağlar. Bunun yerine Orchestrator, ölçeklendirme ve eşgüdümlü yükseltmeler dahil olmak üzere çok Kapsayıcılı mimarilerin geliştirilmesini basitleştiren görevlere odaklanabilir.

## <a name="scenarios"></a>Senaryolar

Azure Container Instances ile Orchestrator tümleştirmesi hala Nascent olmakla birlikte, birkaç farklı ortamın ortaya kaynaklandığını tahmin ederiz:

### <a name="orchestration-of-container-instances-exclusively"></a>Kapsayıcı örneklerinin özel olarak düzenlenmesi

Daha hızlı ve faturalandırıldıkları için, yalnızca Azure Container Instances tabanlı bir ortam, başlamak ve yüksek değişkenli iş yükleriyle uğraşmak için en hızlı yolu sunar.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Sanal makinelerde kapsayıcı örnekleri ve kapsayıcıları birleşimi

Uzun süre çalışan, kararlı iş yükleri için, bir adanmış sanal makine kümesindeki kapsayıcıları düzenleme, genellikle Azure Container Instances ile aynı kapsayıcıları çalıştırmayacak şekilde kullanılır. Ancak, kapsayıcı örnekleri, kullanım açısından beklenmedik veya kısa süreli ani artışlar sağlamak üzere genel kapasitenizi hızlı bir şekilde genişletmek ve dağıtmak için harika bir çözüm sunar.

Bu makinelere ek kapsayıcılar dağıtmak yerine, kümenizdeki sanal makine sayısını ölçeklendirmeden ve daha sonra bu makinelere ek kapsayıcılar dağıtabilmeniz, Orchestrator yalnızca Azure Container Instances ek kapsayıcıları zamanlayabilir ve artık gerekli olmadığında bunları silebilir.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Örnek uygulama: Azure Kubernetes hizmeti (AKS) için sanal düğümler

[Azure Kubernetes Service](../aks/intro-kubernetes.md) (aks) kümesindeki uygulama iş yüklerini hızla ölçeklendirmek için Azure Container Instances içinde dinamik olarak oluşturulan *sanal düğümleri* kullanabilirsiniz. Sanal düğümler, aci ve aks kümesinde çalışan Pod 'ler arasındaki ağ iletişimini sağlar. 

Sanal düğümler Şu anda Linux kapsayıcı örneklerini desteklemektedir. [Azure CLI](../aks/virtual-nodes-cli.md) veya [Azure Portal](../aks/virtual-nodes-portal.md)kullanarak sanal düğümleri kullanmaya başlayın.

Sanal düğümler, sınırsız kapasiteye sahip bir düğüm olarak kaydederek Kubernetes [Kubelet][kubelet-doc] 'sini taklit etmek için açık kaynak [sanal Kubelet][aci-connector-k8s] kullanır. Sanal kubelet, Azure Container Instances ' de kapsayıcı grupları olarak [Pod][pod-doc] oluşturulmasını dağıtır.

Kubernetes API 'sini sunucusuz kapsayıcı platformlarına genişletmeye yönelik ek örnekler için bkz. [sanal Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) projesi.

## <a name="next-steps"></a>Sonraki adımlar

[Hızlı Başlangıç kılavuzunu](container-instances-quickstart.md)kullanarak ilk kapsayıcınızı Azure Container Instances oluşturun.

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/