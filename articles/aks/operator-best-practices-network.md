---
title: Ağ kaynakları için en iyi uygulamalar
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ' de sanal ağ kaynakları ve bağlantı için küme işletmeni en iyi uygulamalarını öğrenin
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104958"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) hizmetinde ağ bağlantısı ve güvenlik için en iyi yöntemler

Azure Kubernetes Service (AKS) içinde kümeler oluşturup yönetirken, düğümleriniz ve uygulamalarınız için ağ bağlantısı sağlarsınız. Bu ağ kaynakları, IP adresi aralıklarını, yük dengeleyicileri ve giriş denetleyicilerini içerir. Uygulamalarınız için yüksek oranda hizmet kalitesi sağlamak üzere bu kaynakları stratejik hale getirmeniz ve yapılandırmanız gerekir.

Bu en iyi yöntemler makalesi, küme işleçleri için ağ bağlantısı ve güvenliğine odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * AKS 'deki Kubernetes kullanan ve Azure Container Networking Interface (CNı) ağ modlarını karşılaştırın.
> * Gerekli IP adresleme ve bağlantı için plan yapın.
> * Yük dengeleyiciler, giriş denetleyicileri veya bir Web uygulaması güvenlik duvarı (WAF) kullanarak trafiği dağıtın.
> * Küme düğümlerine güvenli bir şekilde bağlanın.

## <a name="choose-the-appropriate-network-model"></a>Uygun ağ modelini seçin

> **En iyi yöntemler kılavuzu** 
> 
> Mevcut sanal ağlarla veya şirket içi ağlarla tümleştirme için AKS 'de Azure CNı ağı 'nı kullanın. Bu ağ modeli, kurumsal bir ortamdaki kaynakların ve denetimlerin daha fazla ayrılmasını sağlar.

Sanal ağlar, uygulamalarınıza erişmek için AKS düğümlerine ve müşterilere yönelik temel bağlantıyı sağlar. AKS kümelerini sanal ağlara dağıtmanın iki farklı yolu vardır:

* **Azure CNı ağı**

    Bir sanal ağa dağıtılır ve [Azure CNI][cni-networking] Kubernetes eklentisini kullanır. IP 'Ler, diğer ağ hizmetlerine veya şirket içi kaynaklara yol alabilen ayrı IP 'Leri alır.
* **Kubenet ağı**

    Küme dağıtıldığında ve [Kubernetes kullanan][kubenet] Kubernetes eklentisini kullandığından Azure sanal ağ kaynaklarını yönetir.


Üretim dağıtımları için hem Kubernetes kullanan hem de Azure CNı geçerli seçeneklerdir.

### <a name="cni-networking"></a>CNı ağı

Azure CNı, kapsayıcı çalışma zamanının bir ağ sağlayıcısına istek yapmasını sağlayan satıcı tarafsız bir protokoldür. IP adreslerini Pod ve düğümlere atar ve var olan Azure sanal ağlarına bağlandığınızda IP adresi yönetimi (IPAM) özellikleri sağlar. Her düğüm ve pod kaynağı, Azure sanal ağında bir IP adresi alır. diğer kaynaklarla veya hizmetlerle iletişim kurmak için ek yönlendirmeye gerek yoktur.

![Her biri tek bir Azure VNet 'e bağlanan köprülerle iki düğüm gösteren diyagram](media/operator-best-practices-network/advanced-networking-diagram.png)

Özellikle, üretim için Azure CNı ağı, kaynakların denetimi ve yönetimi için ayrım sağlar. Bir güvenlik perspektifinden, genellikle farklı takımların bu kaynakları yönetmesini ve güvenliğini sağlamak isteyeceksiniz. Azure CNı ağı ile, mevcut Azure kaynaklarına, şirket içi kaynaklara veya diğer hizmetlere, her bir pod 'a atanan IP adresleri aracılığıyla doğrudan bağlanırsınız.

Azure CNı ağı kullandığınızda, sanal ağ kaynağı AKS kümesine ayrı bir kaynak grubunda bulunur. Bu kaynaklara erişmek ve bunları yönetmek için AKS kümesi kimliği için temsilci izinleri. AKS kümesi tarafından kullanılan küme kimliğinin, sanal ağınızdaki alt ağda en az [ağ katılımcısı](../role-based-access-control/built-in-roles.md#network-contributor) izinleri olması gerekir. 

Yerleşik ağ katılımcısı rolünü kullanmak yerine [özel bir rol](../role-based-access-control/custom-roles.md) tanımlamak istiyorsanız aşağıdaki izinler gereklidir:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Varsayılan olarak, AKS, küme kimliği için yönetilen bir kimlik kullanır. Ancak, bunun yerine bir hizmet sorumlusu kullanabilirsiniz. Şunlar hakkında daha fazla bilgi için:
* AKS hizmet sorumlusu temsilcisi, bkz. [diğer Azure kaynaklarına erişim yetkisi verme][sp-delegation]. 
* Yönetilen kimlikler, bkz. [Yönetilen kimlikler kullanma](use-managed-identity.md).

Her düğüm ve pod kendi IP adresini aldığından, AKS alt ağlarının adres aralıklarını planlayın. Göz önünde bulundurun:
* Alt ağ, dağıttığınız her düğüm, pods ve ağ kaynağı için IP adresi sağlayacak kadar büyük olmalıdır. 
    * Hem Kubernetes kullanan hem de Azure CNı ağıyla, çalışan her düğüm, düğüm sayısıyla ilgili varsayılan sınırlara sahiptir.
* Her bir AKS kümesinin kendi alt ağına yerleştirilmesi gerekir. 
* Mevcut ağ kaynaklarıyla çakışan IP adresi aralıklarını kullanmaktan kaçının. 
    * Azure 'da şirket içi veya eşlenmiş ağların bağlanmasına izin vermek için gereklidir.
* Ölçek Genişletme olaylarını veya küme yükseltmelerini işlemek için atanan alt ağda kullanılabilir ek IP adresleri gereklidir. 
    * Bu ek adres alanı özellikle Windows Server kapsayıcıları kullanıyorsanız önemlidir, çünkü bu düğüm havuzları en son güvenlik düzeltme eklerini uygulamak için bir yükseltme gerektirir. Windows Server düğümleri hakkında daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].

Gerekli IP adresini hesaplamak için bkz. [AKS 'de Azure CNI ağını yapılandırma][advanced-networking].

Azure CNı ağı ile bir küme oluştururken, küme için Docker köprü adresi, DNS hizmeti IP 'si ve hizmet adresi aralığı gibi diğer adres aralıklarını belirtirsiniz. Genel olarak, şu adres aralıklarının bulunduğundan emin olun:
* Birbirleriyle çakışmayın.
* Sanal ağlar, alt ağlar, şirket içi ve eşlenmiş ağlar dahil olmak üzere kümeyle ilişkili ağlarla çakışmayın. 

Bu adres aralıkları için sınırlara ve boyutlandırmalar etrafında belirli Ayrıntılar için bkz. [AKS 'de Azure CNI ağını yapılandırma][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet ağı

Kubernetes kullanan, küme dağıtılmadan önce sanal ağları ayarlamanıza gerek olmasa da, beklenmesi gereken dezavantajları vardır:

* Düğümler ve düğüm farklı IP alt ağlarına yerleştirildiğinden, Kullanıcı tanımlı yönlendirme (UDR) ve IP iletimi, trafiği ve düğümleri arasında trafiği yönlendirir. Bu ek yönlendirme, ağ performansını azaltabilir.
* Mevcut şirket içi ağlara bağlantılar veya diğer Azure sanal ağlarına eşleme karmaşık olabilir.

Sanal ağı ve alt ağları AKS kümesinden ayrı olarak oluşturmayınızdan, Kubenet şunları için idealdir:
* Küçük geliştirme veya test iş yükleri. 
* Düşük trafikli basit Web siteleri.
* İş yüklerini kapsayıcılara kaldırma ve kaydırma.

Çoğu üretim dağıtımı için, Azure CNı ağını planlamanız ve kullanmanız gerekir.

Ayrıca, [Kubernetes kullanan kullanarak kendı IP adresi aralıklarını ve sanal ağlarınızı yapılandırabilirsiniz][aks-configure-kubenet-networking]. Azure CNı ağı gibi bu adres aralıkları birbirleriyle çakışmamalıdır ve kümeyle ilişkili tüm ağlarla çakışmamalıdır (sanal ağlar, alt ağlar, şirket içi ve eşlenmiş ağlar). 

Bu adres aralıklarının sınırlarına ve boyutlandırılmasına ilişkin belirli Ayrıntılar için bkz. [AKS 'de kendı IP adresi aralığınızla Kubernetes kullanan ağı kullanma][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Giriş trafiğini dağıtma

> **En iyi yöntemler kılavuzu** 
> 
> Uygulamalarınıza HTTP veya HTTPS trafiği dağıtmak için giriş kaynakları ve denetleyiciler ' i kullanın. Azure yük dengeleyiciye kıyasla, giriş denetleyicileri ek özellikler sağlar ve yerel Kubernetes kaynakları olarak yönetilebilir.

Azure yük dengeleyici, müşteri trafiğini AKS kümenizdeki uygulamalara dağıtabiliyor olsa da, bu trafiği anlamak sınırlıdır. Yük dengeleyici kaynağı 4. katman üzerinde çalışarak trafiği protokol veya bağlantı noktalarına göre dağıtır. 

HTTP veya HTTPS kullanan çoğu Web uygulaması, katman 7 ' de çalışan Kubernetes giriş kaynaklarını ve denetleyicilerini kullanmalıdır. Giriş, uygulamanın URL 'sine göre trafiği dağıtabilir ve TLS/SSL sonlandırmasını idare edebilir. Giriş Ayrıca, sergileme ve eşleme yaptığınız IP adresi sayısını azaltır. 

Yük Dengeleyici ile, her uygulama genellikle AKS kümesinde hizmete atanmış ve eşlenmiş bir genel IP adresi gerektirir. Bir giriş kaynağıyla, tek bir IP adresi trafiği birden çok uygulamaya dağıtabilir.

![AKS kümesinde giriş trafiği akışını gösteren diyagram](media/operator-best-practices-network/aks-ingress.png)

 Giriş için iki bileşen vardır:

 * Giriş *kaynağı*
 * Giriş *denetleyicisi*

### <a name="ingress-resource"></a>Giriş kaynağı

Giriş *kaynağı* BIR YAML bildirimidir `kind: Ingress` . Trafiği AKS kümenizde çalışan hizmetlere yönlendirmek için konak, sertifika ve kuralları tanımlar. 

Aşağıdaki örnek YAML bildirimi, *MyApp.com* için trafiği iki hizmetten birine, *blogservice* veya *StoreService*'e dağıtmaktır. Müşteri, eriştikleri URL 'ye bağlı olarak bir hizmete veya diğerine yönlendirilir.

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

### <a name="ingress-controller"></a>Giriş denetleyicisi

Giriş *denetleyicisi* , aks düğümünde çalışan ve gelen istekleri izleyen bir Daemon. Daha sonra trafik, giriş kaynağında tanımlanan kurallara göre dağıtılır. En yaygın giriş denetleyicisi [NGINX]'i temel alsa da, aks, sizi belirli bir denetleyiciyle kısıtlayamaz. [Dağılım][contour], [HAProxy][haproxy], [Traefik][traefik]vb. kullanabilirsiniz.

Giriş denetleyicileri bir Linux düğümünde zamanlanmalıdır. YAML bildiriminiz veya Helu grafik dağıtımınızda bir düğüm seçici kullanarak, kaynağın bir Linux tabanlı düğümde çalışacağını belirtin. Daha fazla bilgi için bkz. [düğüm seçicileri kullanarak aks 'de Pod 'nin nerede zamanlandığını denetleme][concepts-node-selectors].

> [!NOTE]
> Giriş denetleyicisi, Windows Server düğümlerinde çalıştırılmamalıdır.

Giriş için aşağıdaki nasıl yapılır kılavuzlarıyla birlikte pek çok senaryo vardır:

* [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
* [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
* [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
* [Dinamik bir genel IP adresi][aks-ingress-tls] veya [STATIK bir genel IP adresi][aks-ingress-static-tls] ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Web uygulaması güvenlik duvarı (WAF) ile trafiği güvenli hale getirme

> **En iyi yöntemler kılavuzu**
> 
> Olası saldırılara karşı gelen trafiği taramak için, Azure veya Azure Application Gateway [Barçcuda WAF][barracuda-waf] gibi bir Web uygulaması güvenlik duvarı (WAF) kullanın. Bu daha gelişmiş ağ kaynakları, trafiği yalnızca HTTP ve HTTPS bağlantılarının ve temel TLS sonlandırmasının ötesinde de yönlendirebilir.

Genellikle, bir giriş denetleyicisi AKS kümenizdeki bir Kubernetes kaynağıdır ve bu, trafiği hizmetlere ve uygulamalara dağıtır. Denetleyici bir AKS düğümünde bir daemon olarak çalışır ve CPU, bellek ve ağ bant genişliği gibi bazı düğümün kaynaklarından birini tüketir. Daha büyük ortamlarda şunları yapmak isteyeceksiniz:
* Bu trafik yönlendirmesinin veya TLS sonlandırmasının bir kısmını, AKS kümesi dışındaki bir ağ kaynağına devretmek.
* Olası saldırılar için gelen trafiği tarayın.

![Azure uygulama ağ geçidi gibi bir Web uygulaması güvenlik duvarı (WAF), AKS kümeniz için trafiği koruyabilir ve dağıtabilir](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Bu ekstra güvenlik katmanı için, bir Web uygulaması güvenlik duvarı (WAF) gelen trafiği filtreler. Bir kural kümesi ile, açık Web uygulaması güvenlik projesi (OWASP), siteler arası betik oluşturma veya tanımlama bilgisi kirişleme gibi saldırıları izler. [Azure Application Gateway][app-gateway] (Şu anda aks ' de önizleme aşamasındadır), aks kümeleriyle tümleştirilen BIR WAF, trafik aks kümenize ve uygulamalarınıza ulaşmadan önce bu güvenlik özelliklerinde kilitleme. 

Diğer üçüncü taraf çözümleri de bu işlevleri gerçekleştirdiklerinden, tercih ettiğiniz üründe mevcut yatırımları veya uzmanlığı kullanmaya devam edebilirsiniz.

Yük dengeleyici veya giriş kaynakları, AKS kümenizde sürekli olarak çalışır ve trafik dağılımını iyileştirin. Uygulama ağ geçidi, kaynak tanımına sahip bir giriş denetleyicisi olarak merkezi olarak yönetilebilir. Başlamak için [bir Application Gateway giriş denetleyicisi oluşturun][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>Ağ ilkeleriyle trafik akışını denetleme

> **En iyi yöntemler kılavuzu** 
>
> Pods 'ye giden trafiğe izin vermek veya bu trafiği reddetmek için ağ ilkelerini kullanın. Varsayılan olarak, bir küme içindeki FID 'ler arasında tüm trafiğe izin verilir. Gelişmiş güvenlik için pod iletişimini sınırlayan kuralları tanımlayın.

Ağ ilkesi, AKS 'de bulunan ve pods arasındaki trafik akışını denetlemenize olanak tanıyan bir Kubernetes özelliğidir. Atanan Etiketler, ad alanı veya trafik bağlantı noktası gibi ayarlara bağlı olarak Pod 'a giden trafiğe izin verir veya vermeyebilirsiniz. Ağ ilkeleri, pods 'nin trafik akışını denetlemek için bulutta yerel bir yoldur. Bir AKS kümesinde dinamik olarak oluşturulan kaynaklar için gerekli ağ ilkeleri otomatik olarak uygulanabilir.

Ağ ilkesini kullanmak için yeni bir AKS kümesi oluşturduğunuzda özelliği etkinleştirin. Mevcut bir AKS kümesinde ağ ilkesini etkinleştiremezsiniz. Gerekli kümelerde ağ ilkesini etkinleştirmeyi planlayın. 

>[!NOTE]
>Ağ ilkesi yalnızca, AKS 'deki Linux tabanlı düğümler ve düğüm 'ler için kullanılmalıdır.

YAML bildirimini kullanarak bir Kubernetes kaynağı olarak bir ağ ilkesi oluşturursunuz. İlkeler, trafik akışını tanımlayan giriş veya çıkış kuralları ile tanımlı pods 'ler için uygulanır. 

Aşağıdaki örnek, *Uygulama: arka uç* etiketi uygulanmış olan bir ağ ilkesini bir ağ ilkesi için uygular. Giriş kuralı yalnızca *Uygulama: ön uç* etiketi:

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

> **En iyi yöntemler kılavuzu** 
>
> AKS düğümleriniz için uzak bağlantıyı kullanıma sunma. Bir yönetim sanal ağında bir savunma konağı veya geçiş kutusu oluşturun. Trafiği AKS kümenize uzaktan yönetim görevlerine güvenli bir şekilde yönlendirmek için savunma konağını kullanın.

AKS 'de Azure yönetim araçlarını kullanarak veya Kubernetes API sunucusu aracılığıyla birçok işlemi tamamlayabilirsiniz. AKS düğümleri yalnızca özel bir ağda kullanılabilir ve genel İnternet 'e bağlı değildir. Düğümlere bağlanmak ve bakım ve destek sağlamak, bağlantılarınızı bir savunma ana bilgisayarı veya bağlantı kutusu üzerinden yönlendirin. Bu konağın, AKS kümesi sanal ağı ile ayrı, güvenli bir şekilde eşlenmiş bir yönetim sanal ağında bulunduğundan emin olun.

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