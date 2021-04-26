---
title: Sanal düğümleri kullanma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Hizmetleri (AKS) ile sanal düğüm kullanma konusuna genel bakış
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 3bba1155ec57db67968aec95d1d3386fc6cda006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634456"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Sanal düğümleri kullanmak için bir Azure Kubernetes hizmeti (AKS) kümesi oluşturma ve yapılandırma

Bir AKS kümesindeki uygulama iş yüklerini hızla ölçeklendirmek için sanal düğümleri kullanabilirsiniz. Sanal düğümlerde, pods 'yi hızlı bir şekilde sağlamaktan ve yürütme süresi boyunca yalnızca saniye başına ödeme yaparsınız. Kubernetes kümesi otomatik Scaler için, ek FID 'leri çalıştırmak üzere VM işlem düğümlerini dağıtmaya beklemeniz gerekmez. Sanal düğümler yalnızca Linux Pod ve düğümleri ile desteklenir.

AKS için sanal düğümler eklentisi, açık kaynaklı proje [sanal Kubelet][virtual-kubelet-repo]'i temel alır.

Bu makale, sanal düğümlerin kullanımı ve bilinen sınırlamaların yanı sıra bölge kullanılabilirliği ve ağ gereksinimleri hakkında genel bir bakış sağlar.

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

VNET SKU 'Larını destekleyen tüm bölgeler, sanal düğümlerin dağıtımları için desteklenir.

Her bölgedeki kullanılabilir CPU ve bellek SKU 'Ları için lütfen [Azure bölgelerindeki Azure Container Instances Azure Container Instances kaynak kullanılabilirliğini denetleyin-Linux kapsayıcı grupları](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Ağ gereksinimleri

Sanal düğümler Azure Container Instances (aci) ve aks kümesinde çalışan Pod 'ler arasında ağ iletişimini sağlar. Bu iletişim sağlamak için bir sanal ağ alt ağı oluşturulur ve temsilci izinleri atanır. Sanal düğümler yalnızca *Gelişmiş* ağ (Azure CNI) kullanılarak oluşturulan aks kümeleriyle çalışır. Varsayılan olarak, AKS kümeleri *temel* ağ (kubenet) ile oluşturulur.

Ağ yapılandırması için Azure Container Instances (ACI) ' de çalışan pods 'nin AKS API sunucu uç noktasına erişmesi gerekir.

## <a name="known-limitations"></a>Bilinen sınırlamalar

Sanal düğümler işlevselliği, ACI 'nin özellik kümesine yoğun bir şekilde bağımlıdır. [Azure Container Instances kotaları ve limitlerine](../container-instances/container-instances-quotas.md)ek olarak, aşağıdaki senaryolar sanal düğümlerde henüz desteklenmez:

* ACR görüntülerini çekmek için hizmet sorumlusu kullanma. [Geçici çözüm](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) , [Kubernetes gizli](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line) dizileri kullanmaktır
* VNet eşlemesi, Kubernetes ağ ilkeleri ve ağ güvenlik gruplarıyla internet 'e giden trafik dahil [sanal ağ sınırlamaları](../container-instances/container-instances-vnet.md) .
* Init kapsayıcıları
* [Ana bilgisayar diğer adları](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* ACI 'da exec için [bağımsız değişkenler](../container-instances/container-instances-exec.md#restrictions)
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) , sanal düğümlere Pod dağıtmayacak
* Sanal düğümler, Linux pods zamanlamasını destekler. Açık kaynak [sanal Kubelet aci](https://github.com/virtual-kubelet/azure-aci) sağlayıcısını, Windows Server kapsayıcılarını acı 'ye zamanlamak için el ile yükleyebilirsiniz.
* Sanal düğümler, Azure CNı ağı ile AKS kümeleri gerektirir.
* AKS için API sunucusu yetkilendirilmiş IP aralıklarını kullanma.
* Toplu bağlama Azure dosya paylaşma [genel amaçlı v1](../storage/common/storage-account-overview.md#types-of-storage-accounts)desteği. [Azure dosyaları paylaşımıyla birim](azure-files-volume.md) bağlama yönergelerini izleyin
* IPv6 kullanmak desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

Kümeleriniz için sanal düğümleri yapılandırın:

- [Azure CLı kullanarak sanal düğümler oluşturma](virtual-nodes-cli.md)
- [Azure Kubernetes hizmetlerinde (AKS) portalı kullanarak sanal düğümler oluşturma](virtual-nodes-portal.md)

Sanal düğümler genellikle AKS 'deki bir ölçeklendirme çözümünün bir bileşenidir. Çözümleri ölçeklendirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes yatay Pod otomatik Scaler 'ı kullanma][aks-hpa]
- [Kubernetes kümesi otomatik Scaler 'ı kullanma][aks-cluster-autoscaler]
- [Sanal düğümler için otomatik ölçeklendirme örneğine göz atın][virtual-node-autoscale]
- [Sanal Kubelet açık kaynak kitaplığı hakkında daha fazla bilgi edinin][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
