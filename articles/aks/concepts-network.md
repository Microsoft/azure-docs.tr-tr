---
title: Kavramlar-Azure Kubernetes hizmetlerinde (AKS) ağ oluşturma
description: Azure Kubernetes hizmeti 'nde (AKS), Kubernetes kullanan ve Azure CNı ağı, giriş denetleyicileri, yük dengeleyiciler ve statik IP adresleri dahil ağ hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105349"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki uygulamalar için ağ kavramları

Uygulama geliştirmeye yönelik kapsayıcı tabanlı, mikro hizmetler yaklaşımında, uygulama bileşenleri görevlerini işlemek için birlikte çalışır. Kubernetes, bu ortak işlemi etkinleştiren çeşitli kaynakları sağlar:
* Uygulamalara bir veya harici olarak bağlanabilir ve bunları kullanıma sunabilirsiniz. 
* Uygulamalarınızı yük dengeleyerek yüksek oranda kullanılabilir uygulamalar oluşturabilirsiniz. 
* Daha karmaşık uygulamalarınız için, giriş trafiğini SSL/TLS sonlandırmasına veya birden çok bileşeni yönlendirmeye yönelik olarak yapılandırabilirsiniz. 
* Güvenlik nedenleriyle, ağ trafiğinin akışını, düğüm ve düğümler arasında veya arasında kısıtlayabilir.

Bu makalede, AKS 'teki uygulamalarınıza ağ iletişimi sağlayan temel kavramlar tanıtılmaktadır:

- [Hizmetler](#services)
- [Azure sanal ağları](#azure-virtual-networks)
- [Giriş denetleyicileri](#ingress-controllers)
- [Ağ ilkeleri](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes hakkında temel bilgiler

Kubernetes, uygulamalarınıza veya uygulama bileşenleri arasında erişime izin vermek için sanal ağ için bir soyutlama katmanı sağlar. Kubernetes düğümleri bir sanal ağa bağlanarak, pods için gelen ve giden bağlantılar sağlar. *Kuin-proxy* bileşeni, bu ağ özelliklerini sağlamak için her düğümde çalışır.

Kubernetes 'te:
* *Hizmetler* , IP adresı veya DNS adı aracılığıyla belirli bir bağlantı noktasında doğrudan erişime izin vermek için mantıksal grupları gruplar. 
* *Yük dengeleyici* kullanarak trafiği dağıtabilirsiniz. 
* Uygulama trafiğinin daha karmaşık yönlendirmesi, giriş *denetleyicileriyle* de elde edilebilir. 
* Kubernetes *ağ ilkeleriyle*, pod için ağ trafiğinin güvenliği ve filtrelenmesi mümkündür.

Azure platformu, AKS kümeleri için sanal ağı da basitleştirir. Bir Kubernetes yük dengeleyici oluşturduğunuzda, temel alınan Azure yük dengeleyici kaynağını da oluşturup yapılandırırsınız. Ağ bağlantı noktalarını pods 'de açtığınızda, karşılık gelen Azure ağ güvenlik grubu kuralları yapılandırılır. HTTP uygulama yönlendirmesi için, Azure *dış DNS* 'i de yeni giriş yolları yapılandırıldığında yapılandırabilir.

## <a name="services"></a>Hizmetler

Kubernetes, uygulama iş yüklerine yönelik ağ yapılandırmasını basitleştirmek için *Hizmetleri* kullanarak bir grup kümesini mantıksal olarak gruplamak ve ağ bağlantısı sağlamaktır. Aşağıdaki hizmet türleri kullanılabilir:

- **Küme IP 'si** 
  
  AKS kümesi içinde kullanılmak üzere bir iç IP adresi oluşturur. Küme içindeki diğer iş yüklerini destekleyen yalnızca dahili uygulamalar için uygundur.

    ![AKS kümesinde Küme IP trafiği akışını gösteren diyagram][aks-clusterip]

- **NodePort** 

  Temel düğümde, uygulamaya doğrudan düğüm IP adresi ve bağlantı noktasıyla erişilmesine izin veren bir bağlantı noktası eşlemesi oluşturur.

    ![AKS kümesinde NodePort trafik akışını gösteren diyagram][aks-nodeport]

- **LoadBalancer** 

  Bir Azure yük dengeleyici kaynağı oluşturur, bir dış IP adresi yapılandırır ve istenen Pod 'leri yük dengeleyici arka uç havuzuna bağlar. Müşterilerin trafiğinin uygulamaya erişmesine izin vermek için, istenen bağlantı noktalarında Yük Dengeleme kuralları oluşturulur. 

    ![AKS kümesinde Load Balancer trafik akışını gösteren diyagram][aks-loadbalancer]

    Gelen trafiğin ek denetimi ve yönlendirmesi için, bunun yerine bir giriş [denetleyicisi](#ingress-controllers)de kullanabilirsiniz.

- **ExternalName** 

  Daha kolay uygulama erişimi için belirli bir DNS girişi oluşturur.

Yük dengeleyiciler ve hizmetler IP adresi dinamik olarak atanabilir ya da mevcut bir statik IP adresi belirtebilirsiniz. Hem iç hem de dış statik IP adreslerini atayabilirsiniz. Var olan statik IP adresleri genellikle bir DNS girdisine bağlıdır.

Hem *iç* hem de *dış* yük dengeleyiciler oluşturabilirsiniz. İç yük dengeleyiciler için yalnızca özel bir IP adresi atanır, bu nedenle Internet 'ten erişilemez.

## <a name="azure-virtual-networks"></a>Azure sanal ağları

AKS'de aşağıdaki iki ağ modelinden birini kullanan bir küme dağıtabilirsiniz:

- *Kubenet* ağı

  Ağ kaynakları genellikle AKS kümesi dağıtıldığında oluşturulur ve yapılandırılır.

- *Azure Container ağ arabirimi (CNı)* ağı
 
  AKS kümesi, var olan sanal ağ kaynaklarına ve yapılandırmalarına bağlanır.

### <a name="kubenet-basic-networking"></a>Kubenet (temel) ağ

*Kubernetes kullanan* Networking seçeneği, aks kümesi oluşturma için varsayılan yapılandırmadır. *Kubernetes kullanan* ile:
1. Düğümler Azure sanal ağ alt ağından bir IP adresi alır. 
1. Pods, düğümlerin Azure sanal ağ alt ağından mantıksal olarak farklı bir adres alanından bir IP adresi alır. 
1. Ardından podların Azure sanal ağındaki kaynaklara erişebilmesi için ağ adresi çevirisi (NAT) yapılandırması gerçekleştirilir. 
1. Trafiğin kaynak IP adresi, düğümün birincil IP adresine çevrilir.

Düğümler [Kubernetes kullanan][kubenet] Kubernetes eklentisini kullanır. Seçenekleriniz şunlardır:
* Azure platformunun sizin için sanal ağlar oluşturmasına ve yapılandırmasına izin verin veya 
* AKS kümenizi var olan bir sanal ağ alt ağına dağıtmayı seçin. 

Unutmayın, yalnızca düğümler yönlendirilebilir bir IP adresi alır. Pod, aks kümesi dışındaki diğer kaynaklarla iletişim kurmak için NAT kullanır. Bu yaklaşım, IP 'lerin kullanması için ağ alanınızda ayırmanız gereken IP adresi sayısını azaltır.

Daha fazla bilgi için bkz. [AKS kümesi için Kubernetes kullanan ağını yapılandırma][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNı (Gelişmiş) ağı

Azure CNI seçeneğinde her pod, alt ağdan doğrudan erişilebilen bir IP adresi alır. Bu IP adresleri, ağ alanınızda önceden ve benzersiz olarak planlanmalıdır. Her düğümün desteklediği en fazla sayıda düğüm için bir yapılandırma parametresi vardır. Düğüm başına düşen IP adresi sayısı bundan sonra öndedir. Bu yaklaşım, planlama olmadan IP adresi tükenmesi veya uygulamanızın beklentilerinde daha büyük bir alt ağda kümeleri yeniden oluşturma gereksinimine yol açabilir.

Kubenet 'ın aksine, aynı sanal ağdaki uç noktalara giden trafik, düğümün birincil IP 'si için NAT değildir. Sanal ağ içindeki trafiğin kaynak adresi Pod IP 'dir. Sanal ağ dışındaki trafik, düğümün birincil IP 'si için hala NAT 'Lar sağlar.

Düğümler [Azure CNI][cni-networking] Kubernetes eklentisini kullanır.

![Her biri tek bir Azure VNet 'e bağlanan köprülerle iki düğüm gösteren diyagram][advanced-networking-diagram]

Daha fazla bilgi için bkz. [AKS kümesi Için Azure CNI yapılandırma][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Ağ modellerini karşılaştırın

Kubernetes kullanan ve Azure CNı, AKS kümeleriniz için ağ bağlantısı sağlar. Ancak, her birinin avantajları ve dezavantajları vardır. Yüksek düzeyde, aşağıdaki noktalar geçerlidir:

* **Kubernetes kullanan**
    * IP adresi alanını korur.
    * Küme dışından yük dengelemek için Kubernetes iç veya dış yük dengeleyici kullanır.
    * Kullanıcı tanımlı yolları (UDRs) el ile yönetebilir ve koruyabilirsiniz.
    * Küme başına en fazla 400 düğüm.
* **Azure CNı**
    * Tam sanal ağ bağlantısı alın ve bağlı ağlardan özel IP adresleri aracılığıyla doğrudan erişilebilir.
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

Hem Kubernetes kullanan hem de Azure CNı eklentileri DNS ile ilgili olarak, kendi otomatik Scaler ile AKS 'de çalışan bir dağıtım olan CoreDNS tarafından sunulur. Kubernetes üzerinde CoreDNS hakkında daha fazla bilgi için bkz. [DNS hizmetini özelleştirme](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS varsayılan olarak, bilinmeyen etki alanlarını AKS kümesinin dağıtıldığı Azure sanal ağının DNS işlevselliğine iletecek şekilde yapılandırılmıştır. Bu nedenle, Azure DNS ve özel bölgeler aks 'de çalışan Pod 'ler için çalışacaktır.

### <a name="support-scope-between-network-models"></a>Ağ modelleri arasındaki Destek kapsamı

Kullandığınız ağ modeli ne olursa olsun, hem Kubernetes kullanan hem de Azure CNı aşağıdaki yollarla dağıtılabilir:

* Azure platformu, bir AKS kümesi oluştururken sanal ağ kaynaklarını otomatik olarak oluşturabilir ve yapılandırabilir.
* Sanal ağ kaynaklarını el ile oluşturup yapılandırabilir ve AKS kümenizi oluştururken bu kaynaklara iliştirebilirsiniz.

Service endpoints veya UDRs gibi yetenekler hem Kubernetes kullanan hem de Azure CNı ile desteklense de [AKS için destek ilkeleri][support-policies] , yapabileceğiniz değişiklikleri tanımlar. Örnek:

* Bir AKS kümesi için sanal ağ kaynaklarını el ile oluşturursanız, kendi UDRs veya hizmet uç noktalarınızı yapılandırırken bu kaynakları destekliyoruz.
* Azure platformu AKS kümeniz için sanal ağ kaynaklarını otomatik olarak oluşturursa, kendi UDRs veya hizmet uç noktalarınızı yapılandırmak için bu AKS tarafından yönetilen kaynakları el ile değiştiremezsiniz.

## <a name="ingress-controllers"></a>Giriş denetleyicileri

Yük dengeleyici-tür hizmeti oluşturduğunuzda, temel alınan bir Azure yük dengeleyici kaynağı da oluşturursunuz. Yük dengeleyici, trafiği belirli bir bağlantı noktasında hizmetinize dağıtmak üzere yapılandırılmıştır. 

LoadBalancer yalnızca katman 4 ' te kullanılabilir. Katman 4 ' te, hizmet gerçek uygulamalardan haberdar değildir ve daha fazla yönlendirme konusu yapamaz.

Giriş *denetleyicileri* katman 7 ' de çalışır ve uygulama trafiğini dağıtmak için daha akıllı kurallar kullanabilir. Giriş denetleyicileri genellikle HTTP trafiğini gelen URL 'yi temel alarak farklı uygulamalara yönlendirir.

![AKS kümesinde giriş trafiği akışını gösteren diyagram][aks-ingress]

### <a name="create-an-ingress-resource"></a>Giriş kaynağı oluşturma
AKS 'de, NGıNX, benzer bir araç veya AKS HTTP uygulama yönlendirme özelliğini kullanarak bir giriş kaynağı oluşturabilirsiniz. Bir AKS kümesi için HTTP uygulama yönlendirmeyi etkinleştirdiğinizde, Azure platformu giriş denetleyicisi ve bir *dış DNS* denetleyicisi oluşturur. Kubernetes 'te yeni giriş kaynakları oluşturulduğu için, gerekli DNS A kayıtları kümeye özgü bir DNS bölgesinde oluşturulur. 

Daha fazla bilgi için bkz. [http uygulaması yönlendirmeyi dağıtma][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Application Gateway giriş denetleyicisi (AGIC)

Application Gateway giriş denetleyicisi (AGIC) eklentisi ile, AKS müşterileri, bulut yazılımlarını Internet 'Te kullanıma sunmak için Azure 'ın yerel Application Gateway düzeyi 7 yük dengeleyiciyi kullanabilir. AGIC, ana bilgisayar Kubernetes kümesini izler ve seçili Hizmetleri Internet 'Te kullanıma sunarak Application Gateway sürekli olarak güncelleştirir. 

AKS için AGIC eklentisi hakkında daha fazla bilgi edinmek için bkz. [Application Gateway giriş denetleyicisi nedir?][agic-overview].

### <a name="ssltls-termination"></a>SSL/TLS sonlandırma

SSL/TLS sonlandırması, giriş ın başka bir yaygın özelliğidir. HTTPS üzerinden erişilen büyük Web uygulamalarında, giriş kaynağı uygulamanın kendisi yerine TLS sonlandırmasını işler. Otomatik TLS sertifikası oluşturma ve yapılandırma sağlamak için giriş kaynağını "şifrelemem" gibi sağlayıcıları kullanacak şekilde yapılandırabilirsiniz. 

NGıNX giriş denetleyicisini Izin Verirme ile yapılandırma hakkında daha fazla bilgi için bkz. giriş [ve TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>İstemci kaynak IP koruması

Alım denetleyicinizi, AKS kümenizdeki kapsayıcılara yönelik isteklerde istemci kaynak IP 'sini koruyacak şekilde yapılandırın. Giriş denetleyicinizde istemci isteğini AKS kümenizdeki bir kapsayıcıya yönlendirince, bu isteğin özgün kaynak IP 'si hedef kapsayıcıda kullanılamaz. *İstemci kaynak IP korumasını* etkinleştirdiğinizde, istemcisinin kaynak IP 'Si, *for Için X-iletilen* istek üstbilgisinde kullanılabilir. 

Giriş denetleyicinizde istemci kaynak IP korumasını kullanıyorsanız, TLS geçişi kullanamazsınız. İstemci kaynak IP koruması ve TLS geçişi, *yük dengeleyici* türü gibi diğer hizmetlerle birlikte kullanılabilir.

## <a name="network-security-groups"></a>Ağ güvenlik grupları

Bir ağ güvenlik grubu, AKS düğümleri gibi VM 'Ler için trafiği filtreler. Yük dengeleyici gibi hizmetler oluştururken Azure platformu, gerekli ağ güvenliği grubu kurallarını otomatik olarak yapılandırır. 

Bir aks kümesindeki Pod trafiğini filtrelemek için ağ güvenlik grubu kurallarını el ile yapılandırmanız gerekmez. Gerekli bağlantı noktalarını ve Kubernetes hizmet bildirimlerinizin bir parçası olarak iletmeyi tanımlamanız yeterlidir. Azure platformunun uygun kuralları oluşturmalarına veya güncelleştirmesine izin verin. 

Ayrıca, trafik filtresi kurallarını pods 'ye otomatik olarak uygulamak için ağ ilkelerini de kullanabilirsiniz.

## <a name="network-policies"></a>Ağ ilkeleri

Varsayılan olarak, bir AKS kümesindeki tüm FID 'ler kısıtlama olmadan trafik gönderebilir ve alabilir. Gelişmiş güvenlik için trafik akışını denetleyen kuralları tanımlayın, örneğin:
* Arka uç uygulamaları yalnızca gerekli ön uç hizmetlerine sunulur. 
* Veritabanı bileşenlerine yalnızca bunlara bağlanan uygulama katmanları erişilebilir.

Ağ ilkesi, AKS 'de bulunan ve pods arasındaki trafik akışını denetlemenize olanak tanıyan bir Kubernetes özelliğidir. Atanan Etiketler, ad alanı veya trafik bağlantı noktası gibi ayarlara bağlı olarak Pod 'a giden trafiğe izin verir veya vermeyebilirsiniz. Ağ güvenlik grupları AKS düğümlerinde daha iyidir, ancak ağ ilkeleri, pods 'nin trafik akışını denetlemek için daha uygun, bulutta yerel bir yoldur. Bir AKS kümesinde dinamik olarak oluşturulan kaynaklar için gerekli ağ ilkeleri otomatik olarak uygulanabilir.

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
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
