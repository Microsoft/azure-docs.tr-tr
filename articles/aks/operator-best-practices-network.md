---
title: Operatör en iyi yöntemleri-Azure Kubernetes hizmetlerinde (AKS) ağ bağlantısı
description: Azure Kubernetes Service (AKS) ' de sanal ağ kaynakları ve bağlantı için küme işletmeni en iyi uygulamalarını öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mlearned
ms.openlocfilehash: d1bc865b38b52c8a7c3ac6ec4dab6408a1d0430c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614762"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) üzerinde ağ bağlantısı ve güvenlik için en iyi yöntemler

Azure Kubernetes Service (AKS) içinde kümeler oluşturup yönetirken, düğümleriniz ve uygulamalarınız için ağ bağlantısı sağlarsınız. Bu ağ kaynakları, IP adresi aralıklarını, yük dengeleyicileri ve giriş denetleyicilerini içerir. Uygulamalarınız için yüksek oranda hizmet kalitesi sağlamak üzere bu kaynakları planlamanız ve yapılandırmanız gerekir.

Bu en iyi yöntemler makalesi, küme işleçleri için ağ bağlantısı ve güvenliğine odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * AKS 'deki Kubernetes kullanan ve Azure CNı ağ modlarını karşılaştırın
> * Gerekli IP adresleme ve bağlantı planlaması
> * Yük dengeleyiciler, giriş denetleyicileri veya Web uygulaması güvenlik duvarları (WAF) kullanarak trafiği dağıtma
> * Küme düğümlerine güvenli bir şekilde bağlanma

## <a name="choose-the-appropriate-network-model"></a>Uygun ağ modelini seçin

**En iyi Yöntem Kılavuzu** -mevcut sanal ağlarla veya şirket içi ağlarla tümleştirme için aks 'de Azure CNI ağı 'nı kullanın. Bu ağ modeli ayrıca kurumsal bir ortamdaki kaynakların ve denetimlerin daha fazla ayrılmasını sağlar.

Sanal ağlar, uygulamalarınıza erişmek için AKS düğümlerine ve müşterilere yönelik temel bağlantıyı sağlar. AKS kümelerini sanal ağlara dağıtmanın iki farklı yolu vardır:

* **Kubernetes kullanan Networking** -Azure, küme dağıtıldığında sanal ağ kaynaklarını yönetir ve [Kubernetes kullanan][kubenet] Kubernetes eklentisini kullanır.
* **Azure CNI ağı** -mevcut bir sanal ağa dağıtılır ve [Azure Container Network Interface (CNI)][cni-networking] Kubernetes eklentisini kullanır. IP 'Ler, diğer ağ hizmetlerine veya şirket içi kaynaklara yol alabilen ayrı IP 'Leri alır.

Kapsayıcı ağ arabirimi (CNı), kapsayıcı çalışma zamanının bir ağ sağlayıcısına istek yapmasını sağlayan satıcı tarafsız bir protokoldür. Azure CNI, IP adreslerini Pod ve düğümlere atar ve var olan Azure sanal ağlarına bağlandığınızda IP adresi yönetimi (IPAM) özellikleri sağlar. Her düğüm ve pod kaynağı, Azure sanal ağında bir IP adresi alır ve diğer kaynaklarla veya hizmetlerle iletişim kurmak için ek yönlendirmeye gerek yoktur.

![Her biri tek bir Azure VNet 'e bağlanan köprülerle iki düğüm gösteren diyagram](media/operator-best-practices-network/advanced-networking-diagram.png)

Çoğu üretim dağıtımı için Azure CNı ağı kullanmanız gerekir. Bu ağ modeli, kaynakların denetim ve yönetimi için ayrım sağlar. Bir güvenlik perspektifinden, genellikle farklı takımların bu kaynakları yönetmesini ve güvenliğini sağlamak isteyeceksiniz. Azure CNı Networking, mevcut Azure kaynaklarına, şirket içi kaynaklara veya diğer hizmetlere, her Pod 'a atanan IP adresleri aracılığıyla doğrudan bağlanmanızı sağlar.

Azure CNı ağı kullandığınızda, sanal ağ kaynağı AKS kümesine ayrı bir kaynak grubunda bulunur. Bu kaynaklara erişmek ve bunları yönetmek için AKS hizmet sorumlusu için temsilci izinleri. AKS kümesi tarafından kullanılan hizmet sorumlusu, sanal ağınızdaki alt ağda en az bir [ağ katılımcısı](../role-based-access-control/built-in-roles.md#network-contributor) iznine sahip olmalıdır. Yerleşik ağ katılımcısı rolünü kullanmak yerine [özel bir rol](../role-based-access-control/custom-roles.md) tanımlamak istiyorsanız aşağıdaki izinler gereklidir:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

AKS hizmet sorumlusu temsilcisi hakkında daha fazla bilgi için bkz. [diğer Azure kaynaklarına erişim yetkisi verme][sp-delegation].

Her düğüm ve pod kendi IP adresini aldığından, AKS alt ağlarının adres aralıklarını planlayın. Alt ağ, dağıttığınız her düğüm, pods ve ağ kaynağı için IP adresi sağlayacak kadar büyük olmalıdır. Her bir AKS kümesinin kendi alt ağına yerleştirilmesi gerekir. Azure 'da şirket içi veya eşlenmiş ağların bağlanmasına izin vermek için, mevcut ağ kaynaklarıyla çakışan IP adresi aralıklarını kullanmayın. Her düğümün hem Kubernetes kullanan hem de Azure CNı ağı ile çalıştığı düğüm sayısı için varsayılan sınırlar vardır. Ölçek artırma olaylarını veya küme yükseltmelerini işlemek için atanan alt ağda kullanabileceğiniz ek IP adresleri de gereklidir. Bu ek adres alanı, bu düğüm havuzlarının en son güvenlik düzeltme eklerini uygulamak için bir yükseltme gerektirdiğinden (Şu anda AKS 'de önizleme aşamasında) Windows Server kapsayıcıları kullanıyorsanız önemlidir. Windows Server düğümleri hakkında daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].

Gerekli IP adresini hesaplamak için bkz. [AKS 'de Azure CNI ağını yapılandırma][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet ağı

Kubernetes kullanan, küme dağıtılmadan önce sanal ağları ayarlamanızı gerektirmese de dezavantajları vardır:

* Düğümler ve düğüm 'ler farklı IP alt ağlarına yerleştirilir. Kullanıcı tanımlı yönlendirme (UDR) ve IP iletimi, trafiği ve düğümleri arasında trafiği yönlendirmek için kullanılır. Bu ek yönlendirme, ağ performansını azaltabilir.
* Mevcut şirket içi ağlara bağlantılar veya diğer Azure sanal ağlarına eşleme karmaşık olabilir.

Kubenet, AKS kümesinden ayrı olarak sanal ağ ve alt ağlar oluşturmanız gerekmiyorsa küçük geliştirme veya test iş yükleri için uygundur. Düşük trafiğe sahip basit Web siteleri veya iş yüklerini kapsayıcılara taşımak ve kaydırmak için, Kubernetes kullanan ağıyla dağıtılan AKS kümelerinin basitliğini de yararlı olabilir. Çoğu üretim dağıtımı için, Azure CNı ağını planlamanız ve kullanmanız gerekir. Ayrıca, [Kubernetes kullanan kullanarak kendı IP adresi aralıklarını ve sanal ağlarınızı yapılandırabilirsiniz][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Giriş trafiğini dağıtma

**En iyi Yöntem Kılavuzu** -uygulamalarınıza http veya HTTPS trafiği dağıtmak için giriş kaynaklarını ve denetleyicileri kullanın. Giriş denetleyicileri, düzenli bir Azure yük dengeleyici üzerinden ek özellikler sağlar ve yerel Kubernetes kaynakları olarak yönetilebilir.

Azure yük dengeleyici, AKS kümenizdeki uygulamalara müşteri trafiği dağıtabilir, ancak bu trafikle ilgili olarak daha fazla anlama gelir. Yük dengeleyici kaynağı 4. katman üzerinde çalışarak trafiği protokol veya bağlantı noktalarına göre dağıtır. HTTP veya HTTPS kullanan çoğu Web uygulaması, katman 7 ' de çalışan Kuberenetes giriş kaynaklarını ve denetleyicilerini kullanmalıdır. Giriş, uygulamanın URL 'sine göre trafiği dağıtabilir ve TLS/SSL sonlandırmasını idare edebilir. Bu özellik ayrıca sergileme ve eşleme yaptığınız IP adresi sayısını azaltır. Yük Dengeleyici ile, her uygulama genellikle AKS kümesinde hizmete atanmış ve eşlenmiş bir genel IP adresi gerektirir. Bir giriş kaynağıyla, tek bir IP adresi trafiği birden çok uygulamaya dağıtabilir.

![AKS kümesinde giriş trafiği akışını gösteren diyagram](media/operator-best-practices-network/aks-ingress.png)

 Giriş için iki bileşen vardır:

 * Giriş *kaynağı*ve
 * Giriş *denetleyicisi*

Giriş kaynağı, trafiği aks kümenizde çalışan hizmetlere yönlendiren `kind: Ingress` konak, sertifika ve kuralları tanımlayan bir YAML bildirimidir. Aşağıdaki örnek YAML bildirimi, *MyApp.com* için trafiği iki hizmetten birine, *blogservice* veya *StoreService*'e dağıtmaktır. Müşteri, eriştikleri URL 'ye bağlı olarak bir hizmete veya diğerine yönlendirilir.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Giriş denetleyicisi, AKS düğümünde çalışan ve gelen istekleri izleyen bir Daemon. Daha sonra trafik, giriş kaynağında tanımlanan kurallara göre dağıtılır. En yaygın giriş denetleyicisi [NGINX]'i temel alır. AKS, sizi belirli bir denetleyiciyle kısıtlayamaz, bu sayede [dağılım][contour], [HAProxy][haproxy]veya [Traefik][traefik]gibi diğer denetleyicileri kullanabilirsiniz.

Giriş denetleyicileri bir Linux düğümünde zamanlanmalıdır. Windows Server düğümleri (Şu anda AKS 'deki önizlemede) giriş denetleyicisini çalıştırmamalıdır. Kaynağın Linux tabanlı bir düğümde çalışması gerektiğini belirtmek için, YAML bildiriminizde veya Held grafik dağıtımınızda bir düğüm seçici kullanın. Daha fazla bilgi için bkz. [düğüm seçicileri kullanarak aks 'de Pod 'nin nerede zamanlandığını denetleme][concepts-node-selectors].

Giriş için aşağıdaki nasıl yapılır kılavuzlarıyla birlikte pek çok senaryo vardır:

* [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
* [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
* [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
* [Dinamik bir genel IP adresi][aks-ingress-tls] veya [STATIK bir genel IP adresi][aks-ingress-static-tls] ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Web uygulaması güvenlik duvarı (WAF) ile trafiği güvenli hale getirme

**En iyi Yöntem Kılavuzu** -gelen trafiği olası saldırılara karşı taramak Için, Azure veya Azure Application Gateway [BARRAMPADA WAF][barracuda-waf] gibi bir Web uygulaması güvenlik duvarı (WAF) kullanın. Bu daha gelişmiş ağ kaynakları, trafiği yalnızca HTTP ve HTTPS bağlantılarının ve temel SSL sonlandırmasının ötesinde de yönlendirebilir.

Trafiği hizmetlere ve uygulamalara dağıtan bir giriş denetleyicisi, genellikle AKS kümenizdeki bir Kubernetes kaynağıdır. Denetleyici bir AKS düğümünde bir daemon olarak çalışır ve CPU, bellek ve ağ bant genişliği gibi bazı düğümün kaynaklarından birini tüketir. Daha büyük ortamlarda, bu trafik yönlendirme veya TLS sonlandırmasının bazılarını AKS kümesi dışındaki bir ağ kaynağına devretmek istersiniz. Olası saldırılar için gelen trafiği de taramak istiyorsunuz.

![Azure uygulama ağ geçidi gibi bir Web uygulaması güvenlik duvarı (WAF), AKS kümeniz için trafiği koruyabilir ve dağıtabilir](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Web uygulaması güvenlik duvarı (WAF), gelen trafiği filtreleyerek ek bir güvenlik katmanı sağlar. Açık Web uygulaması güvenlik projesi (OWASP), siteler arası komut dosyası oluşturma veya tanımlama bilgisi kirişleme gibi saldırıları izlemek için bir kurallar kümesi sağlar. [Azure Application Gateway][app-gateway] (Şu anda AKS 'deki önizlemededir), aks kümeleriyle tümleştirilebilen bir WAF, trafikten ve uygulamalarınıza erişmeden önce bu güvenlik özelliklerini sağlamaktır. Diğer üçüncü taraf çözümleri de bu işlevleri gerçekleştirir, bu sayede belirli bir üründe mevcut yatırımları veya uzmanlığı kullanmaya devam edebilirsiniz.

Trafik dağıtımını daha da belirginleştirmek için yük dengeleyici veya giriş kaynakları AKS kümenizde çalışmaya devam eder. Uygulama ağ geçidi, kaynak tanımına sahip bir giriş denetleyicisi olarak merkezi olarak yönetilebilir. Başlamak için [bir Application Gateway giriş denetleyicisi oluşturun][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Ağ ilkeleriyle trafik akışını denetleme

**En iyi Yöntem Kılavuzu** -ağ ilkelerini kullanarak pods 'ye giden trafiğe izin verme veya bu trafiği reddetme. Varsayılan olarak, bir küme içindeki FID 'ler arasında tüm trafiğe izin verilir. Gelişmiş güvenlik için pod iletişimini sınırlayan kuralları tanımlayın.

Ağ ilkesi, düğüm arasındaki trafik akışını denetlemenizi sağlayan bir Kubernetes özelliğidir. Atanmış Etiketler, ad alanı veya trafik bağlantı noktası gibi ayarlara göre trafiğe izin vermeyi veya reddetme seçeneğini belirleyebilirsiniz. Ağ ilkelerinin kullanımı, trafik akışını denetlemek için bulutta yerel bir yol sağlar. Bir AKS kümesinde dinamik olarak oluşturulan bir ağ ilkesi varsa, gerekli ağ ilkeleri otomatik olarak uygulanabilir. Pod-Pod trafiğini denetlemek için Azure ağ güvenlik grupları kullanmayın, ağ ilkelerini kullanın.

Ağ ilkesini kullanmak için, bir AKS kümesi oluşturduğunuzda özelliğin etkinleştirilmesi gerekir. Mevcut bir AKS kümesinde ağ ilkesini etkinleştiremezsiniz. Kümelerdeki ağ ilkesini etkinleştirdiğinizden ve bunları gerektiği gibi kullanabilmesi için önce plan yapın. Ağ ilkesi yalnızca, AKS 'deki Linux tabanlı düğümler ve düğüm 'ler için kullanılmalıdır.

Bir ağ ilkesi, YAML bildirimi kullanılarak bir Kubernetes kaynağı olarak oluşturulur. İlkeler tanımlı yığınlara uygulanır, giriş veya çıkış kuralları trafiğin nasıl akabilir olduğunu tanımlar. Aşağıdaki örnek, *Uygulama: arka uç* etiketi uygulanmış olan bir ağ ilkesini bir ağ ilkesi için uygular. Giriş kuralı bundan sonra yalnızca uygulamayla gelen trafiğe izin verir *: ön uç* etiketi:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

İlkeleri kullanmaya başlamak için bkz. [Azure Kubernetes Service (aks) içindeki ağ ilkelerini kullanarak Pod arasındaki trafiği güvenli hale getirme][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Bir savunma ana bilgisayarı aracılığıyla düğümlere güvenli bir şekilde bağlanma

**En iyi Yöntem Kılavuzu** -aks düğümleriniz için uzak bağlantıyı kullanıma sunma. Bir yönetim sanal ağında bir savunma konağı veya geçiş kutusu oluşturun. Trafiği AKS kümenize uzaktan yönetim görevlerine güvenli bir şekilde yönlendirmek için savunma konağını kullanın.

AKS 'deki çoğu işlem, Azure yönetim araçları kullanılarak veya Kubernetes API sunucusu üzerinden tamamlanabilir. AKS düğümleri genel İnternet 'e bağlı değildir ve yalnızca özel bir ağda kullanılabilir. Düğümlere bağlanmak ve bakım yapmak veya sorunları gidermek için, bağlantılarınızı bir savunma ana bilgisayarı veya bağlantı kutusu üzerinden yönlendirin. Bu konak, AKS kümesi sanal ağına güvenli bir şekilde eşlenmiş ayrı bir yönetim sanal ağında olmalıdır.

![Savunma konağını veya geçiş kutusunu kullanarak AKS düğümlerine bağlanma](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Savunma konağının yönetim ağı da güvenli olmalıdır. Şirket içi ağa bağlanmak ve ağ güvenlik gruplarını kullanarak erişimi denetlemek için bir [Azure ExpressRoute][expressroute] veya [VPN Gateway][vpn-gateway] kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makale ağ bağlantısı ve güvenliğine odaklanılmıştır. Kubernetes 'deki Ağ temelleri hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (AKS) içindeki uygulamalar Için ağ kavramları][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[NGINX]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool