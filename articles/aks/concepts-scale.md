---
title: Kavramlar-Azure Kubernetes hizmetlerindeki (AKS) uygulamaları ölçeklendirme
description: Bkz. Azure Kubernetes hizmeti (AKS) ile yatay Pod otomatik Scaler, küme otomatik Scaler ve Azure Container Instances Bağlayıcısı dahil).
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: f6d2121403361c4225a7e0e64411875d83d93194
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305744"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) uygulamaları için ölçeklendirme seçenekleri

Azure Kubernetes Service 'te (AKS) uygulama çalıştırırken, işlem kaynaklarının miktarını artırmanız veya azaltmanız gerekebilir. İhtiyaç duyduğunuz uygulama örneklerinin sayısı değiştikçe, temeldeki Kubernetes düğümlerinin de değiştirilmesi gerekebilir. Ayrıca, çok sayıda ek uygulama örneğini hızlı bir şekilde sağlamanız gerekebilir.

Bu makalede, AKS 'teki uygulamaları ölçeklendirmenize yardımcı olan temel kavramlar tanıtılmaktadır:

- [El ile ölçeklendirme](#manually-scale-pods-or-nodes)
- [Yatay Pod otomatik Scaler (HPA)](#horizontal-pod-autoscaler)
- [Küme otomatik ölçeklendiricisi](#cluster-autoscaler)
- [AKS ile Azure Container Instance (acı) Tümleştirmesi](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Pod veya düğümleri el ile ölçeklendirme

Uygulamanızın kullanılabilir kaynaklardaki ve durumdaki bir değişikliğe nasıl yanıt vereceğini test etmek için çoğaltmaları (pods) ve düğümleri el ile ölçekleyebilirsiniz. Kaynakları el ile ölçeklendirirken, düğüm sayısı gibi sabit bir maliyeti korumak için kullanılacak bir miktar kaynak kümesi tanımlamanızı da sağlar. El ile ölçeklendirmek için çoğaltma veya düğüm sayısını tanımlarsınız. Kubernetes API 'SI daha sonra bu çoğaltma veya düğüm sayısına bağlı olarak ek düğüm veya boşaltma düğümleri oluşturmayı zamanlar.

Düğüm ölçeğini ölçeklendirirken, Kubernetes API 'SI, kümeniz tarafından kullanılan işlem türüne bağlı olan ilgili Azure Işlem API 'sini çağırır. Örneğin, VM Ölçek kümelerinde oluşturulan kümeler için, hangi düğümlerin kaldırılacağını seçme mantığı VM Ölçek Kümeleri API 'SI tarafından belirlenir. Düğümlerin ölçek azaltma sırasında kaldırılması için nasıl seçildiği hakkında daha fazla bilgi edinmek için bkz. [VMSS SSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#if-i-reduce-my-scale-set-capacity-from-20-to-15--which-vms-are-removed-).

Pod 'leri ve düğümleri el ile ölçeklendirmeye başlamak için bkz. [aks 'teki Uygulamaları ölçeklendirme][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Yatay pod otomatik ölçeklendiricisi

Kubernetes, kaynak talebini izlemek ve çoğaltmalar sayısını otomatik olarak ölçeklendirmek için yatay Pod otomatik Scaler (HPA) kullanır. Varsayılan olarak, yatay Pod otomatik Scaler, çoğaltma sayısında gerekli değişiklikler için ölçüm API 'sini her 30 saniyede bir denetler. Değişiklikler gerektiğinde, kopyaların sayısı artar veya buna göre azaltılır. Yatay Pod otomatik Scaler, Kubernetes 1.8 + için ölçüm sunucusunu dağıtmış olan AKS kümeleriyle birlikte çalışmaktadır.

![Kubernetes yatay Pod otomatik ölçeklendirme](media/concepts-scale/horizontal-pod-autoscaling.png)

Belirli bir dağıtım için yatay Pod otomatik Scaler 'ı yapılandırdığınızda, çalıştırılabilen en düşük ve en fazla çoğaltma sayısını tanımlarsınız. Ayrıca CPU kullanımı gibi ölçekleme kararlarını izlemek ve temel almak için ölçüm tanımlayabilirsiniz.

Aks 'deki yatay Pod otomatik Scaler 'ı kullanmaya başlamak için bkz. [aks 'de otomatik ölçeklendirme Pod][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Ölçeklendirme olaylarının cooli

Yatay Pod otomatik Scaler, ölçüm API 'sini her 30 saniyede bir denetlediğinde, önceki ölçek olayları, başka bir denetim yapılmadan önce başarıyla tamamlanmamış olabilir. Bu davranış, önceki ölçeklendirme olayı uygulama iş yükünü ve uygun şekilde ayarlanacak kaynak taleplerini almadan önce, yatay Pod otomatik Scaler 'ın yineleme sayısını değiştirmesine neden olabilir.

Yarış olaylarını en aza indirmek için bir gecikme değeri ayarlanır. Bu değer, başka bir ölçek olayının tetiklenmesi için bir ölçek olayından sonra yatay Pod otomatik Scaler 'ın ne kadar süre beklemesi gerektiğini tanımlar. Bu davranış, yeni çoğaltma sayısının, dağıtılmış iş yükünü yansıtması için etkili ve ölçüm API 'sine izin verir. [Kubernetes 1,12 itibariyle ölçek olayları için gecikme](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay)yoktur, ancak ölçek azaltma olaylardaki gecikme varsayılan olarak 5 dakikaya ayarlanır.

Şu anda bu coolvalues değerlerini varsayılan değerden ayarlayamıyoruz.

## <a name="cluster-autoscaler"></a>Küme otomatik ölçeklendiricisi

Kubernetes 'in değişen Pod taleplerini, düğüm havuzundaki istenen işlem kaynaklarını temel alan düğüm sayısını ayarlayan bir küme otomatik olarak ayarlamıştır. Varsayılan olarak, küme otomatik Scaler, düğüm sayısında gerekli değişiklikler için ölçüm API sunucusunu her 10 saniyede bir denetler. Küme otomatik ölçeklendirme bir değişikliğin gerekli olduğunu belirlerse, AKS kümenizdeki düğümlerin sayısı göre artar veya azaltılır. Küme otomatik Scaler, Kubernetes 1,10. x veya üstünü çalıştıran Kubernetes RBAC özellikli AKS kümeleriyle çalışır.

![Kubernetes kümesi otomatik Scaler](media/concepts-scale/cluster-autoscaler.png)

Küme otomatik ezici, genellikle yatay Pod otomatik Scaler ile birlikte kullanılır. Birleştirildiğinde, yatay Pod otomatik Scaler, uygulama talebine göre Pod sayısını artırır veya düşürür ve küme otomatik olarak bu ek sınırları yürütmek için gereken düğüm sayısını ayarlar.

AKS 'deki küme otomatik Scaler 'ı kullanmaya başlamak için bkz. [aks üzerinde küme otomatik Scaler][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Olayları genişletme

Bir düğümde istenen Pod çalıştırmak için yeterli işlem kaynağı yoksa, Pod, zamanlama sürecinde ilerleyemiyorum. Düğüm havuzu içinde ek bilgi işlem kaynakları kullanılabilir değilse Pod başlayamaz.

Küme otomatik yüklemesi, düğüm havuzu kaynak kısıtlamaları nedeniyle zamanlanabilecek Pod 'yi fark ediyorsa, düğüm havuzu içindeki düğümlerin sayısı ek işlem kaynakları sağlamak için artmıştır. Bu ek düğümler başarıyla dağıtıldığında ve düğüm havuzunda kullanıma hazır olduğunda, daha sonra bunlar üzerinde çalışacak şekilde zamanlanır.

Uygulamanızın hızla ölçeklendirilmesi gerekiyorsa, küme otomatik Scaler tarafından dağıtılan ek düğümler zamanlanan Pod 'yi kabul edebilene kadar bazı KADS zamanlanmayı bekleyen bir durumda kalabilir. Yüksek veri bloğu taleplerine sahip uygulamalarda, sanal düğümlerle ve Azure Container Instances ölçeklendirebilirsiniz.

### <a name="scale-in-events"></a>Olaylarda ölçeklendirme

Küme otomatik olarak yeni zamanlama istekleri almamış olan düğümler için pod zamanlama durumunu da izler. Bu senaryo düğüm havuzunda gerekenden daha fazla işlem kaynağı olduğunu ve düğüm sayısının Azaltılabilecek olduğunu gösterir.

Varsayılan olarak 10 dakika boyunca gerekli olmayan eşiği geçen bir düğüm silinmek üzere zamanlandı. Bu durum oluştuğunda, düğüm havuzu içindeki diğer düğümlerde çalışacak şekilde zamanlanır ve küme otomatik olarak düğüm sayısını düşürür.

Küme otomatik olarak düğüm sayısını azalttığında, uygulamalarınız farklı düğümlere zamanlandığı için, bazı kesintilere karşılaşabilir. Kesintiyi en aza indirmek için, tek bir pod örneği kullanan uygulamalardan kaçının.

## <a name="burst-to-azure-container-instances"></a>Azure Container Instances için patlama

AKS kümenizi hızlı bir şekilde ölçeklendirmek için Azure Container Instances (ACI) ile tümleştirilebilir. Kubernetes, çoğaltmayı ve düğüm sayısını ölçeklendirmek için yerleşik bileşenlere sahiptir. Ancak, uygulamanızın hızla ölçeklendirilmesi gerekiyorsa yatay Pod otomatik Scaler, düğüm havuzundaki mevcut işlem kaynakları tarafından sağlanabenden daha fazla sayıda bilgi zamanlayabilir. Yapılandırıldıysa, bu senaryo küme otomatik olarak düğüm havuzunda ek düğüm dağıtmak üzere tetikler, ancak bu düğümlerin başarılı bir şekilde sağlanması ve Kubernetes Scheduler 'ın bunlar üzerinde pod çalıştırmasına izin vermek birkaç dakika sürebilir.

![Kubernetes veri bloğu ölçeklendirmeyi ACI 'ya](media/concepts-scale/burst-scaling.png)

ACI ek altyapı yükü olmadan kapsayıcı örneklerini hızlı bir şekilde dağıtmanızı sağlar. AKS ile bağlandığınızda, AKS kümenizin güvenli, mantıksal bir uzantısı haline gelir. Sanal [Kubelet][virtual-kubelet]tabanlı [sanal düğümler][virtual-nodes-cli] bileşeni, sanal bir Kubernetes düğümü olarak acı 'yi sunan aks kümenize yüklenir. Kubernetes daha sonra sanal düğümler aracılığıyla aci örnekleri olarak çalışan ve VM düğümlerinde doğrudan aks kümenizdeki Pod olmayan bir düğüm zamanlayabilir.

Uygulamanız sanal düğümleri kullanmak için değişiklik gerektirmez. Dağıtımlar aks ve acı genelinde ölçeklendirebilir ve küme otomatik Scaler, AKS kümenizde yeni düğümler dağıttığında gecikme yok.

Sanal düğümler, AKS kümeniz ile aynı sanal ağdaki ek bir alt ağa dağıtılır. Bu sanal ağ yapılandırması, ACI ve AKS arasındaki trafiğin güvenliğini sağlar. AKS kümesi gibi, ACI örneği, diğer kullanıcılardan yalıtılmış güvenli, mantıksal bir işlem kaynağıdır.

## <a name="next-steps"></a>Sonraki adımlar

Ölçeklendirme uygulamaları kullanmaya başlamak için öncelikle [Azure CLI ile BIR AKS kümesi oluşturmak üzere hızlı][aks-quickstart]başlangıcı izleyin. Daha sonra, AKS kümenizdeki uygulamaları el ile veya otomatik olarak ölçeklendirmeye başlayabilirsiniz:

- [Pod][aks-manually-scale-pods] veya [düğümleri][aks-manually-scale-nodes] el ile ölçeklendirme
- [Yatay Pod otomatik Scaler][aks-hpa] 'ı kullanma
- [Küme otomatik Scaler][aks-cluster-autoscaler] 'ı kullanma

Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes/AKS kümeleri ve iş yükleri][aks-concepts-clusters-workloads]
- [Kubernetes/AKS erişimi ve kimliği][aks-concepts-identity]
- [Kubernetes/AKS güvenliği][aks-concepts-security]
- [Kubernetes/AKS sanal ağları][aks-concepts-network]
- [Kubernetes/AKS depolaması][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
