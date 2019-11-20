---
title: Kavramlar-Azure Kubernetes hizmetlerinde (AKS) ağ oluşturma
description: Azure Kubernetes hizmeti 'nde (AKS), Kubernetes kullanan ve Azure CNı ağı, giriş denetleyicileri, yük dengeleyiciler ve statik IP adresleri dahil ağ hakkında bilgi edinin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 26ba3ff600ddca6158579941ab5d32b60ff13101
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950374"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki uygulamalar için ağ kavramları

Uygulama geliştirmeye yönelik kapsayıcı tabanlı mikro hizmetler yaklaşımında, uygulama bileşenlerinin görevlerini işlemek için birlikte çalışması gerekir. Kubernetes, bu uygulama iletişimini sağlayan çeşitli kaynaklar sağlar. Uygulamalara bir veya harici olarak bağlanabilir ve bunları kullanıma sunabilirsiniz. Yüksek oranda kullanılabilir uygulamalar oluşturmak için uygulamalarınızın yükünü dengeleyebilirsiniz. Daha karmaşık uygulamalar, SSL/TLS sonlandırması veya birden çok bileşenin yönlendirilmesi için giriş trafiğinin yapılandırılmasını gerektirebilir. Güvenlik nedenleriyle, ağ trafiğinin akışını veya düğüm ve düğümler arasında kısıtlamak da gerekebilir.

Bu makalede, AKS 'teki uygulamalarınıza ağ iletişimi sağlayan temel kavramlar tanıtılmaktadır:

- [Hizmetler](#services)
- [Azure sanal ağları](#azure-virtual-networks)
- [Giriş denetleyicileri](#ingress-controllers)
- [Ağ ilkeleri](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes temelleri

Uygulamalarınıza erişime izin vermek veya uygulama bileşenlerinin birbirleriyle iletişim kurması için Kubernetes, sanal ağ için bir soyutlama katmanı sağlar. Kubernetes düğümleri bir sanal ağa bağlanır ve pods için gelen ve giden bağlantılar sağlayabilir. *Kuin-proxy* bileşeni, bu ağ özelliklerini sağlamak için her düğümde çalışır.

Kubernetes 'te *Hizmetler* , IP adresi veya DNS adı üzerinden ve belirli bir bağlantı noktası üzerinden doğrudan erişime izin vermek üzere Pod 'yi mantıksal olarak gruplar. Ayrıca, *yük dengeleyici*kullanarak trafik dağıtabilirsiniz. Uygulama trafiğinin daha karmaşık yönlendirmesi, giriş *denetleyicileriyle*de elde edilebilir. Kubernetes *ağ ilkeleriyle*, pod için ağ trafiğinin güvenliği ve filtrelenmesi mümkündür.

Azure platformu, AKS kümeleri için sanal ağı basitleştirmeye de yardımcı olur. Bir Kubernetes yük dengeleyici oluşturduğunuzda, temel alınan Azure yük dengeleyici kaynağı oluşturulur ve yapılandırılır. Ağ bağlantı noktalarını pods 'de açtığınızda, karşılık gelen Azure ağ güvenlik grubu kuralları yapılandırılır. HTTP uygulama yönlendirmesi için, Azure *dış DNS* 'i de yeni giriş yolları yapılandırıldığında yapılandırabilir.

## <a name="services"></a>Hizmetler

Kubernetes, uygulama iş yüklerine yönelik ağ yapılandırmasını basitleştirmek için *Hizmetleri* kullanarak bir grup kümesini mantıksal olarak gruplamak ve ağ bağlantısı sağlamaktır. Aşağıdaki hizmet türleri kullanılabilir:

- **Küme IP** -aks kümesi içinde kullanılmak üzere BIR iç IP adresi oluşturur. Küme içindeki diğer iş yüklerini destekleyen yalnızca dahili uygulamalar için uygundur.

    ![AKS kümesinde Küme IP trafiği akışını gösteren diyagram][aks-clusterip]

- **NodePort** -temel düğümde, uygulamaya doğrudan düğüm IP adresi ve bağlantı noktasıyla erişilmesine izin veren bir bağlantı noktası eşlemesi oluşturur.

    ![AKS kümesinde NodePort trafik akışını gösteren diyagram][aks-nodeport]

- **LoadBalancer** -bir Azure yük dengeleyici kaynağı oluşturur, bir dış IP adresi yapılandırır ve istenen Pod 'leri yük dengeleyici arka uç havuzuna bağlar. Müşteri trafiğinin uygulamaya erişmesine izin vermek için, istenen bağlantı noktalarında Yük Dengeleme kuralları oluşturulur. 

    ![AKS kümesinde Load Balancer trafik akışını gösteren diyagram][aks-loadbalancer]

    Gelen trafiğin ek denetimi ve yönlendirmesi için, bunun yerine bir giriş [denetleyicisi](#ingress-controllers)de kullanabilirsiniz.

- **ExternalName** -daha kolay uygulama erişimi için belırlı bir DNS girişi oluşturur.

Yük dengeleyiciler ve hizmetler için IP adresi dinamik olarak atanabilir veya kullanılacak mevcut bir statik IP adresi belirtebilirsiniz. Hem iç hem de dış statik IP adresleri atanabilir. Bu var olan statik IP adresi genellikle bir DNS girdisine bağlıdır.

Hem *iç* hem de *dış* yük dengeleyiciler oluşturulabilir. İç yük dengeleyiciler için yalnızca özel bir IP adresi atanır, bu nedenle Internet 'ten erişilemez.

## <a name="azure-virtual-networks"></a>Azure sanal ağları

AKS 'de, aşağıdaki iki ağ modelinden birini kullanan bir küme dağıtabilirsiniz:

- *Kubenet* Networking-ağ kaynakları genellikle aks kümesi dağıtıldığında oluşturulur ve yapılandırılır.
- *Azure Container Network Interface (CNı)* ağı-aks kümesi var olan sanal ağ kaynaklarına ve yapılandırmalara bağlanır.

### <a name="kubenet-basic-networking"></a>Kubenet (temel) ağ

*Kubernetes kullanan* Networking seçeneği, aks kümesi oluşturma için varsayılan yapılandırmadır. *Kubernetes kullanan*ile, düğümler Azure sanal ağ alt ağından bir IP adresi alır. Pods, mantıksal olarak farklı bir adres alanından düğümlerin Azure sanal ağ alt ağına bir IP adresi alır. Ağ adresi çevirisi (NAT), daha sonra, Azure sanal ağındaki kaynaklara ulaşabilmesi için yapılandırılır. Trafiğin kaynak IP adresi NAT ' dır ve düğümün birincil IP adresidir.

Düğümler [Kubernetes kullanan][kubenet] Kubernetes eklentisini kullanır. Azure platformunun sizin için sanal ağlar oluşturmasına ve yapılandırmasına izin verebilir veya AKS kümenizi mevcut bir sanal ağ alt ağına dağıtmayı seçebilirsiniz. Yine, yalnızca düğümler yönlendirilebilir bir IP adresi alır ve IP 'ler, AKS kümesi dışındaki diğer kaynaklarla iletişim kurmak için NAT kullanır. Bu yaklaşım, Pod 'nin kullanabilmesi için ağ alanınızda ayırmanız gereken IP adresi sayısını önemli ölçüde azaltır.

Daha fazla bilgi için bkz. [AKS kümesi için Kubernetes kullanan ağını yapılandırma][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNı (Gelişmiş) ağı

Azure CNı ile her Pod, alt ağdan bir IP adresi alır ve doğrudan erişilebilir. Bu IP adresleri, ağ alanınızda benzersiz olmalı ve önceden planlanmalıdır. Her düğümün desteklediği en fazla sayıda düğüm için bir yapılandırma parametresi vardır. Düğüm başına düşen IP adresi sayısı, bu düğüm için önde ayrılır. Bu yaklaşım daha fazla planlama gerektirir, aksi takdirde IP adresi tükenmesi veya uygulamanızın beklentilerinde daha büyük bir alt ağda kümeleri yeniden oluşturma gereksinimine yol açabilir.

Düğümler [Azure Container Networking Interface (CNı)][cni-networking] Kubernetes eklentisini kullanır.

![Her biri tek bir Azure VNet 'e bağlanan köprülerle iki düğüm gösteren diyagram][advanced-networking-diagram]

Daha fazla bilgi için bkz. [AKS kümesi Için Azure CNI yapılandırma][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Ağ modellerini karşılaştırın

Kubernetes kullanan ve Azure CNı, AKS kümeleriniz için ağ bağlantısı sağlar. Ancak, her birinin avantajları ve dezavantajları vardır. Yüksek düzeyde, aşağıdaki noktalar geçerlidir:

* **Kubernetes kullanan**
    * IP adresi alanını korur.
    * Küme dışından yük dengelemek için Kubernetes iç veya dış yük dengeleyici kullanır.
    * Kullanıcı tanımlı yolları (UDRs) el ile yönetmeniz ve korumanız gerekir.
    * Küme başına en fazla 400 düğüm.
* **Azure CNı**
    * Tam sanal ağ bağlantısı alın ve küme dışından doğrudan erişilebilir.
    * Daha fazla IP adres alanı gerektirir.

Kubernetes kullanan ve Azure CNı arasında aşağıdaki davranış farklılıkları vardır:

| Özellik                                                                                   | Kubernetes kullanan   | Azure CNı |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Kümeyi var olan veya yeni bir sanal ağda dağıt                                            | Desteklenen-UDRs el ile uygulandı | Desteklenir |
| Pod-POD bağlantısı                                                                         | Desteklenir | Desteklenir |
| Pod-VM bağlantısı; Aynı sanal ağdaki VM                                          | Pod tarafından başlatıldığında çalışma | Her iki şekilde de geçerlidir |
| Pod-VM bağlantısı; Eşlenen sanal ağdaki VM                                            | Pod tarafından başlatıldığında çalışma | Her iki şekilde de geçerlidir |
| VPN veya Express Route kullanarak şirket içi erişim                                                | Pod tarafından başlatıldığında çalışma | Her iki şekilde de geçerlidir |
| Hizmet uç noktaları tarafından güvenli hale getirilmiş kaynaklara erişim                                             | Desteklenir | Desteklenir |
| Yük Dengeleyici Hizmeti, uygulama ağ geçidi veya giriş denetleyicisi kullanarak Kubernetes hizmetlerini kullanıma sunma | Desteklenir | Desteklenir |
| Varsayılan Azure DNS ve özel bölgeler                                                          | Desteklenir | Desteklenir |

### <a name="support-scope-between-network-models"></a>Ağ modelleri arasındaki Destek kapsamı

Kullandığınız ağ modelinden bağımsız olarak, hem Kubernetes kullanan hem de Azure CNı aşağıdaki yollarla dağıtılabilir:

* Azure platformu, bir AKS kümesi oluştururken sanal ağ kaynaklarını otomatik olarak oluşturabilir ve yapılandırabilir.
* Sanal ağ kaynaklarını el ile oluşturup yapılandırabilir ve AKS kümenizi oluştururken bu kaynaklara iliştirebilirsiniz.

Service endpoints veya UDRs gibi yetenekler hem Kubernetes kullanan hem de Azure CNı ile desteklense de [AKS için destek ilkeleri][support-policies] , yapabileceğiniz değişiklikleri tanımlar. Örneğin:

* Bir AKS kümesi için sanal ağ kaynaklarını el ile oluşturursanız, kendi UDRs veya hizmet uç noktalarınızı yapılandırırken desteklenemez.
* Azure platformu AKS kümeniz için sanal ağ kaynaklarını otomatik olarak oluşturursa, kendi UDRs veya hizmet uç noktalarınızı yapılandırmak için bu AKS tarafından yönetilen kaynakların el ile değiştirilmesi desteklenmez.

## <a name="ingress-controllers"></a>Giriş denetleyicileri

Bir LoadBalancer tür hizmeti oluşturduğunuzda, temel alınan bir Azure yük dengeleyici kaynağı oluşturulur. Yük dengeleyici, trafiği belirli bir bağlantı noktasında hizmetinize dağıtmak üzere yapılandırılmıştır. LoadBalancer yalnızca katman 4 ' te çalışıyor-hizmet gerçek uygulamalardan haberdar değildir ve ek yönlendirme konuları yapamaz.

Giriş *denetleyicileri* katman 7 ' de çalışır ve uygulama trafiğini dağıtmak için daha akıllı kurallar kullanabilir. Bir giriş denetleyicisinin yaygın kullanımı, HTTP trafiğini gelen URL 'yi temel alarak farklı uygulamalara yönlendirkullanmaktır.

![AKS kümesinde giriş trafiği akışını gösteren diyagram][aks-ingress]

AKS 'de NGıNX gibi bir giriş kaynağı oluşturabilir veya AKS HTTP uygulama yönlendirme özelliğini kullanabilirsiniz. Bir AKS kümesi için HTTP uygulama yönlendirmeyi etkinleştirdiğinizde, Azure platformu giriş denetleyicisi ve bir *dış DNS* denetleyicisi oluşturur. Kubernetes 'te yeni giriş kaynakları oluşturulduğu için, gerekli DNS A kayıtları kümeye özgü bir DNS bölgesinde oluşturulur. Daha fazla bilgi için bkz. [http uygulaması yönlendirmeyi dağıtma][aks-http-routing].

Giriş ın başka bir ortak özelliği SSL/TLS sonlandırmasından oluşur. HTTPS üzerinden erişilen büyük Web uygulamalarında TLS sonlandırma, uygulamanın kendisi yerine giriş kaynağı tarafından işlenebilir. Otomatik TLS sertifikası oluşturma ve yapılandırma sağlamak için giriş kaynağını, şifrelemem gibi sağlayıcıları kullanacak şekilde yapılandırabilirsiniz. NGıNX giriş denetleyicisini Izin Verirme ile yapılandırma hakkında daha fazla bilgi için bkz. giriş [ve TLS][aks-ingress-tls].

Ayrıca, Alım denetleyicinizi, AKS kümenizdeki kapsayıcılara yönelik isteklerde istemci kaynak IP 'sini koruyacak şekilde yapılandırabilirsiniz. Bir istemcinin isteği, Alım denetleyiciniz aracılığıyla AKS kümenizdeki bir kapsayıcıya yönlendirilince, bu isteğin özgün kaynak IP 'si hedef kapsayıcı tarafından kullanılamaz. *İstemci kaynak IP korumasını*etkinleştirdiğinizde, istemcisinin kaynak IP 'Si, *for Için X-iletilen*istek üstbilgisinde kullanılabilir. Giriş denetleyicinizde istemci kaynak IP Koruması kullanıyorsanız, SSL geçişini kullanamazsınız. İstemci kaynak IP koruması ve SSL geçişi, *yük dengeleyici* türü gibi diğer hizmetlerle birlikte kullanılabilir.

## <a name="network-security-groups"></a>Ağ güvenlik grupları

Ağ güvenlik grubu, sanal makineler için AKS düğümleri gibi trafiği filtreler. Yük dengeleyici gibi hizmetler oluştururken Azure platformu, gereken tüm ağ güvenlik grubu kurallarını otomatik olarak yapılandırır. Bir aks kümesindeki Pod trafiğini filtrelemek için ağ güvenlik grubu kurallarını el ile yapılandırmayın. Tüm gerekli bağlantı noktalarını ve Kubernetes hizmet bildirimlerinizi kapsamında iletme ve Azure platformunun uygun kuralları oluşturmasına veya güncelleştirmesine izin verin. Ayrıca, trafik filtresi kurallarını pods 'ye otomatik olarak uygulamak için, sonraki bölümde açıklandığı gibi ağ ilkelerini de kullanabilirsiniz.

## <a name="network-policies"></a>Ağ ilkeleri

Varsayılan olarak, bir AKS kümesindeki tüm FID 'ler kısıtlama olmadan trafik gönderebilir ve alabilir. Gelişmiş güvenlik için trafik akışını denetleyen kuralları tanımlamak isteyebilirsiniz. Arka uç uygulamaları genellikle yalnızca gerekli ön uç hizmetlerine sunulur veya veritabanı bileşenlerine yalnızca bunlara bağlanan uygulama katmanları erişilebilir.

Ağ ilkesi, AKS 'de bulunan ve pods arasındaki trafik akışını denetlemenize olanak tanıyan bir Kubernetes özelliğidir. Atanmış Etiketler, ad alanı veya trafik bağlantı noktası gibi ayarlara göre trafiğe izin vermeyi veya reddetme seçeneğini belirleyebilirsiniz. Ağ güvenlik grupları, pods değil AKS düğümleri için daha fazla. Ağ ilkelerinin kullanımı, trafik akışını denetlemek için daha uygun, bulutta yerel bir yoldur. Bir AKS kümesinde dinamik olarak oluşturulan bir ağ ilkesi varsa, gerekli ağ ilkeleri otomatik olarak uygulanabilir.

Daha fazla bilgi için bkz. [Azure Kubernetes Service (aks) içindeki ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme][use-network-policies].

## <a name="next-steps"></a>Sonraki adımlar

Aks ağıyla çalışmaya başlamak için, [Kubernetes kullanan][aks-configure-kubenet-networking] veya [Azure CNI][aks-configure-advanced-networking]kullanarak kendi IP adresi aralığınızla bir aks kümesi oluşturun ve yapılandırın.

İlişkili en iyi uygulamalar için bkz. [AKS 'de ağ bağlantısı ve güvenlik Için en iyi uygulamalar][operator-best-practices-network].

Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes/AKS kümeleri ve iş yükleri][aks-concepts-clusters-workloads]
- [Kubernetes/AKS erişimi ve kimliği][aks-concepts-identity]
- [Kubernetes/AKS güvenliği][aks-concepts-security]
- [Kubernetes/AKS depolaması][aks-concepts-storage]
- [Kubernetes/AKS ölçeği][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
