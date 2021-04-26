---
title: Ortak Load Balancer kullanma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ile hizmetlerinizi kullanıma sunmak için standart SKU ile genel yük dengeleyiciyi nasıl kullanacağınızı öğrenin.
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 3f2219f5052aee0c0a9cd43aa87df8789adbcae2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783098"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde ortak Standart Load Balancer kullanma

Azure Load Balancer, hem gelen hem de giden senaryoları destekleyen açık sistemler arası bağlantı (OSı) modelinin L4 üzerinde bulunur. Yük dengeleyicinin ön ucuna gelen akışları, arka uç havuzu örneklerine dağıtır.

AKS ile tümleştirilen **ortak** Load Balancer iki amaca hizmet eder:

1. AKS sanal ağı içindeki küme düğümlerine giden bağlantılar sağlamak için. Bu amaca, düğümlerin özel IP adresini, *giden havuzunun* bir parçası olan genel bir IP adresine çevirerek bu amaca erişir.
2. Türündeki Kubernetes Hizmetleri aracılığıyla uygulamalara erişim sağlamak için `LoadBalancer` . Bununla birlikte, uygulamalarınızı kolayca ölçeklendirebilir ve yüksek oranda kullanılabilir hizmetler oluşturabilirsiniz.

Ön uç olarak yalnızca özel IP 'Lere izin verilen bir **iç (veya özel)** yük dengeleyici kullanılır. İç yük dengeleyiciler, bir sanal ağ içindeki trafiğin yükünü dengelemek için kullanılır. Yük dengeleyici ön uca karma senaryodaki bir şirket içi ağdan de erişilebilir.

Bu belge, ortak yük dengeleyici ile tümleştirmeyi içerir. İç Load Balancer tümleştirme için bkz. [aks Iç yük dengeleyici belgeleri](internal-lb.md).

## <a name="before-you-begin"></a>Başlamadan önce

Azure Load Balancer, *temel* ve *Standart* olmak üzere iki SKU 'da kullanılabilir. Varsayılan olarak, bir AKS kümesi oluşturduğunuzda *Standart* SKU kullanılır. Daha büyük bir arka uç havuzu, [**birden çok düğüm havuzu**](use-multiple-node-pools.md)ve [**kullanılabilirlik alanları**](availability-zones.md)gibi ek işlevlere erişebilmek için *Standart* SKU 'yu kullanın. AKS için önerilen Load Balancer SKU 'SU.

*Temel* ve *Standart* SKU 'lar hakkında daha fazla bilgi için bkz. [Azure yük dengeleyici SKU karşılaştırması][azure-lb-comparison].

Bu makalede, *Standart* SKU 'nun Azure Load Balancer bir aks kümeniz olduğunu ve yük dengeleyicinin bazı özelliklerini ve özelliklerini nasıl kullanacağınızı ve yapılandıracağınızı öğreneceksiniz. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

> [!IMPORTANT]
> Giden bağlantı sağlamak için Azure Load Balancer kullanmaktan yararlanmayı tercih ediyorsanız ve bu amaçla kendi ağ geçidinizin, güvenlik duvarınız veya ara sunucusu, [**Çıkış türü olarak UserDefinedRouting (UDR)**](egress-outboundtype.md)kullanarak yük dengeleyici giden havuzunun ve ilgili ön uç IP 'sinin oluşturulmasını atlayabilirsiniz. Giden türü bir küme için çıkış yöntemini tanımlar ve varsayılan olarak şunu yazın: yük dengeleyici.

## <a name="use-the-public-standard-load-balancer"></a>Ortak standart yük dengeleyiciyi kullanma

Giden türü Load Balancer (varsayılan) olan bir AKS kümesi oluşturduktan sonra, küme, hizmet sunmak için yük dengeleyiciyi kullanmaya hazır hale gelir.

Bu şekilde, `LoadBalancer` Aşağıdaki örnekte gösterildiği gibi türünde bir ortak hizmet oluşturabilirsiniz. Şunu adlı bir hizmet bildirimi oluşturarak başlayın `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

[Kubectl Apply][kubectl-apply] kullanarak genel hizmet bildirimini dağıtın ve YAML bildiriminizde adı belirtin:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

Azure Load Balancer, bu yeni hizmeti ön sağlayacak yeni bir genel IP ile yapılandırılır. Azure Load Balancer birden çok ön uç IP 'si olduğundan, dağıtılan her yeni hizmet, benzersiz olarak erişilecek yeni bir adanmış ön uç IP 'si alır.

Hizmetin oluşturulduğunu ve yük dengeleyiciyi çalıştırarak yapılandırıldığını doğrulayabilirsiniz, örneğin:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Hizmet ayrıntılarını görüntülediğinizde, yük dengeleyicide bu hizmet için oluşturulan genel IP adresi, *dış IP* sütununda gösterilir. *\<pending\>* Yukarıdaki örnekte gösterildiği gibi, IP adresinin gerçek bir genel IP adresine değiştirilmesi bir veya iki dakika sürebilir.

## <a name="configure-the-public-standard-load-balancer"></a>Ortak standart yük dengeleyiciyi yapılandırma

Standart SKU ortak yük dengeleyiciyi kullanırken, oluşturma zamanında özelleştirilebilen veya küme güncellenebilir bir seçenekler kümesi vardır. Bu seçenekler, Load Balancer iş yüklerinizin ihtiyaçlarını karşılayacak şekilde özelleştirmenize olanak tanır ve buna göre gözden geçirilmesi gerekir. Standart yük dengeleyici ile şunları yapabilirsiniz:

* Yönetilen giden IP sayısını ayarlama veya ölçeklendirme
* Kendi özel [giden IP 'leri veya giden IP ön ekini](#provide-your-own-outbound-public-ips-or-prefixes) getirin
* Kümenin her bir düğümüne ayrılan giden bağlantı noktası sayısını özelleştirin
* Boştaki bağlantılar için zaman aşımı ayarını yapılandırın

> [!IMPORTANT]
> Belirli bir zamanda yalnızca bir giden IP seçeneği (yönetilen IP 'Ler, kendi IP 'nizi getir veya IP öneki) kullanılabilir.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Yönetilen giden genel IP sayısını ölçeklendirin

Azure Load Balancer, gelen öğesine ek olarak bir sanal ağdan giden bağlantı sağlar. Giden kuralları, Genel Standart Load Balancer giden ağ adresi çevirisini yapılandırmayı basitleştirir.

Tüm Load Balancer kuralları gibi giden kurallar da yük dengeleme ve gelen NAT kuralları ile aynı tanıdık sözdizimini izler:

***ön uç IP + parametreler + arka uç havuzu***

Giden bir kural, arka uç havuzu tarafından belirtilen tüm sanal makineler için giden NAT 'yi ön uca çevrilecek şekilde yapılandırır. Ve parametreler giden NAT algoritması üzerinde ek ayrıntılı denetim sağlar.

Giden kuralı yalnızca tek bir genel IP adresiyle kullanılabilir ancak giden kuralları, giden NAT ölçeklendirme için yapılandırma yükünü kolaylaştırır. Büyük ölçekli senaryoları planlamak için birden çok IP adresi kullanabilir ve giden kurallarını, SNAT tükenmesi açısından yüksek desenleri azaltmak için kullanabilirsiniz. Ön uç tarafından sağlanan her ek IP adresi, Load Balancer SNAT bağlantı noktası olarak kullanılacak 64K kısa ömürlü bağlantı noktaları sağlar. 

Varsayılan olarak oluşturulan, yönetilen giden genel IP 'Ler ile *Standart* SKU yük dengeleyicisi kullanılırken, parametresini kullanarak yönetilen giden genel IP sayısını ölçeklendirebilirsiniz **`load-balancer-managed-ip-count`** .

Var olan bir kümeyi güncelleştirmek için aşağıdaki komutu çalıştırın. Bu parametre, birden çok yönetilen giden genel IP 'si olması için küme oluşturma zamanında de ayarlanabilir.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

Yukarıdaki örnek, *Myresourcegroup* Içindeki *Myakscluster* kümesi Için yönetilen giden genel IP sayısını *2* ' ye ayarlar. 

Ayrıca, parametreyi **`load-balancer-managed-ip-count`** ekleyerek **`--load-balancer-managed-outbound-ip-count`** ve istediğiniz değere ayarlayarak kümenizi oluştururken yönetilen giden genel IP 'lerin başlangıç sayısını ayarlamak için parametresini de kullanabilirsiniz. Varsayılan yönetilen giden genel IP sayısı 1 ' dir.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Kendi giden genel IP 'Leri veya öneklerinizi sağlayın

*Standart* SKU yük dengeleyiciyi KULLANDıĞıNıZDA, aks kümesi varsayılan olarak aks tarafından yönetilen altyapı kaynak grubunda BIR genel IP oluşturur ve bunu yük dengeleyici giden havuzuna atar.

AKS tarafından oluşturulan genel IP, AKS yönetilen kaynağı olarak kabul edilir. Bu, genel IP yaşam döngüsünün AKS tarafından yönetilmek üzere amaçlandığı ve doğrudan genel IP kaynağında Kullanıcı eylemi gerektirmeyeceği anlamına gelir. Alternatif olarak, küme oluşturma sırasında kendi özel genel IP veya genel IP ön ekini atayabilirsiniz. Özel IP 'niz Ayrıca var olan bir kümenin yük dengeleyici özellikleri üzerinde de güncelleştirilir.

Kendi genel IP 'nizi veya ön ekini kullanma gereksinimleri:

- Özel genel IP adreslerinin Kullanıcı tarafından oluşturulması ve sahibi olması gerekir. AKS tarafından oluşturulan yönetilen genel IP adresleri, yönetim çakışmalarına neden olabileceği için kendi özel IP 'nizi getir olarak yeniden kullanılamaz.
- AKS kümesi kimliğinin (hizmet sorumlusu veya yönetilen kimlik) giden IP 'ye erişme izinlerine sahip olduğundan emin olmanız gerekir. [Gerekli genel IP izinleri listesine](kubernetes-service-principal.md#networking)göre.
- Giden IP 'Leri veya giden IP öneklerini yapılandırmak için gerekli önkoşulları [ve kısıtlamaları](../virtual-network/public-ip-address-prefix.md#constraints) karşıladığınızdan emin olun.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Kümeyi kendi giden genel IP 'niz ile güncelleştirme

Genel IP 'nizin kimliklerini listelemek için [az Network public-ip show][az-network-public-ip-show] komutunu kullanın.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Yukarıdaki komut, *Myresourcegroup* kaynak grubundaki *Mypublicıp* genel IP 'nin kimliğini gösterir.

`az aks update` **`load-balancer-outbound-ips`** Kümenizi genel IP 'larınız ile güncelleştirmek için parametresiyle komutunu kullanın.

Aşağıdaki örnek, `load-balancer-outbound-ips` Önceki komuttan gelen kimliklerle parametresini kullanır.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Kümeyi kendi giden genel IP ön ekine göre güncelleştirin

*Standart* SKU yük dengeleyicinizle çıkış IÇIN genel IP öneklerini de kullanabilirsiniz. Aşağıdaki örnek, genel IP öneklerinizin kimliklerini listelemek için [az Network public-ip önekini göster][az-network-public-ip-prefix-show] komutunu kullanır:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Yukarıdaki komut, *Myresourcegroup* kaynak grubundaki *Mypublicipprefix* genel IP ön ekinin kimliğini gösterir.

Aşağıdaki örnek, önceki komuttan gelen kimliklerle *yük dengeleyici-giden-IP-önekleri* parametresini kullanır.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Kümeyi kendi genel IP 'niz veya önekleriyle oluşturun

Bir izin verilenler listesine Çıkış uç noktaları ekleme gibi senaryoları desteklemek üzere küme oluşturma sırasında çıkış için kendi IP adreslerinizi veya IP ön eklerini getirmek isteyebilirsiniz. Kümenin yaşam döngüsünün başlangıcında kendi genel IP 'Leri ve IP öneklerinizi tanımlamak için yukarıda gösterilen aynı parametreleri küme oluşturma adımınıza ekleyin.

Başlangıç aşamasında genel IP 'inizle yeni bir küme oluşturmak için, *Load-dengeleyici-giden-IP* parametresi ile *az aks Create* komutunu kullanın.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Başlangıçta genel IP öneklerinizi içeren yeni bir küme oluşturmak için, *Load-dengeleyici-giden-IP-önekleri* parametresiyle *az aks Create* komutunu kullanın.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>Ayrılan giden bağlantı noktalarını yapılandırma

> [!IMPORTANT]
> Kümenizde küçük hedef kümesine çok sayıda bağlantı kurması beklenen uygulamalar varsa, örn. SQL DB 'ye bağlanan çok sayıda ön uç örneği, SNAT bağlantı noktası tükenmesi (bağlantı noktası) ile karşılaşmanız çok açıktır. Bu senaryolar için, yük dengeleyicide ayrılan giden bağlantı noktalarını ve giden ön uç IP 'lerini artırmanız önemle önerilir. Artış, bir (1) ek IP adresinin tüm küme düğümlerine dağıtılacak 64K ek bağlantı noktaları ekleyeceğini göz önünde bulundurmalıdır.


Aksi belirtilmedikçe, AKS, yapılandırma sırasında Standart Load Balancer tanımlayan ayrılmış giden bağlantı noktalarının varsayılan değerini kullanacaktır. Aşağıdaki komutta gösterildiği gibi, bu değer AKS API 'sinde veya SLB API 'sindeki **0** ' da **null** olur:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Önceki komutlarda yük dengeleyiciniz için giden kuralı listelenir, örneğin:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Bu çıktı, 0 bağlantı noktasına sahip olduğunuz ancak [arka uç havuz boyutunu temel alan otomatik giden bağlantı noktası atamasını][azure-lb-outbound-preallocatedports]kullandığınız için, örneğin, bir kümede 50 veya daha az düğüm varsa, her bir düğüm için 1024 bağlantı noktaları ayrılır, bu sayede düğümlerin sayısını giderek, düğüm başına daha az bağlantı noktası alırsınız.


Ayrılan giden bağlantı noktası sayısını tanımlamak veya artırmak için aşağıdaki örneği izleyebilirsiniz:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Bu örnekte, kümemdeki her düğüm için 4000 için ayrılan giden bağlantı noktası ve 7 IP, *düğüm başına 4000 bağlantı noktasına sahip olur * 100 düğümleri = 400k toplam bağlantı noktası < = 448k toplam bağlantı noktası = 7 IP * IP başına 64K bağlantı* noktası. Bu, 100 düğüme güvenle ölçeklenebilme ve varsayılan bir yükseltme işlemi yapmanıza olanak sağlar. Yükseltme ve diğer işlemler için gereken ek düğümler için yeterli bağlantı noktası ayrılması kritik öneme sahiptir. AKS, yükseltme için bir arabellek düğümüne varsayılan olarak, bu örnekte belirli bir zamanda 4000 boş bağlantı noktası gerekir. [Maxdalgalanma değerleri](upgrade-cluster.md#customize-node-surge-upgrade)kullanılıyorsa, düğüm başına giden bağlantı noktalarını maxdalgalanma değerinizdeki değerle çarpın.

100 düğümüne güvenle gitmek için daha fazla IP eklemeniz gerekir.


> [!IMPORTANT]
> Bağlantı veya ölçeklendirme sorunlarından kaçınmak için, [gerekli kotayı hesaplamanız ve][requirements] *allocatedOutboundPorts* özelleştirmeden önce gereksinimleri denetlemeniz gerekir.

Ayrıca **`load-balancer-outbound-ports`** , bir küme oluştururken parametreleri de kullanabilirsiniz, ancak aynı zamanda, veya ' ı da belirtmeniz gerekir **`load-balancer-managed-outbound-ip-count`** **`load-balancer-outbound-ips`** **`load-balancer-outbound-ip-prefixes`** .  Örnek:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>Yük dengeleyici boşta kalma zaman aşımını yapılandırma

SNAT bağlantı noktası kaynakları tükendiğinde, mevcut akışlar SNAT bağlantı noktalarını yayınlana kadar giden akışlar başarısız olur. Flow kapandığında ve AKS tarafından yapılandırılan yük dengeleyici, boşta akışlardan geri kazanma SNAT bağlantı noktalarında 30 dakikalık bir boşta kalma zaman aşımı kullandığında, geri kazanır SNAT bağlantı noktalarını Load Balancer.
Ayrıca, taşıma (örneğin, **`TCP keepalives`** ) veya **`application-layer keepalives`** boş bir akışı yenilemek ve gerekirse bu boşta kalma zaman aşımını sıfırlamak için kullanabilirsiniz. Bu zaman aşımını aşağıdaki örnekte de yapılandırabilirsiniz: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Çok sayıda kısa süreli bağlantınız olması beklediğinizi ve uzun süreli ve uzun sürelerine sahip bir bağlantı yoksa `kubectl proxy` `kubectl port-forward` 4 dakika gibi düşük bir zaman aşımı değeri kullanmayı düşünebilirsiniz. Ayrıca, TCP keepcanlı tutma kullanılırken bağlantının bir tarafında etkinleştirilmesi yeterlidir. Örneğin, bunları sunucu tarafında etkinleştirmek yeterlidir ve akışın boşta kalma zamanlayıcısını sıfırlayın ve her iki taraf da TCP keepcanlı tutmayı başlatması gerekli değildir. Veritabanı istemci-sunucu yapılandırmalarına dahil olmak üzere uygulama katmanı için benzer kavramlar vardır. Uygulamaya özgü keepcanlı olarak hangi seçeneklerin mevcut olduğunu kontrol etmek için sunucu tarafını denetleyin.

> [!IMPORTANT]
> AKS, varsayılan olarak boşta durumunda TCP sıfırlamayı kolaylaştırır ve senaryolarınızda daha öngörülebilir uygulama davranışı için bu yapılandırmayı tutmanızı ve bundan yararlanmasını önerir.
> TCP RST yalnızca TCP bağlantısı sırasında, belırlenen durumda gönderilir. Bu konuda [burada](../load-balancer/load-balancer-tcp-reset.md) daha fazla bilgi edinebilirsiniz.

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Ayrılan giden bağlantı noktalarını ve boşta kalma zaman aşımını özelleştirme gereksinimleri

- *AllocatedOutboundPorts* için belirttiğiniz değer ayrıca 8 ' in katı olmalıdır.
- Düğüm sanal makinelerinizin sayısına ve gerekli ayrılmış giden bağlantı noktalarına bağlı olarak yeterli giden IP kapasitesine sahip olmanız gerekir. Yeterli sayıda çıkış IP kapasitesi olduğunu doğrulamak için aşağıdaki formülü kullanın: 
 
*Outboundıps* \* 64.000 \> *nodevms* \* *desiredAllocatedOutboundPorts*.
 
Örneğin, 3 *Nodevms* ve 50.000 *desiredAllocatedOutboundPorts* varsa, en az 3 *outboundıps* gerekir. İhtiyaç duyduğunuz süreden daha fazla giden IP kapasitesi eklemeniz önerilir. Ayrıca, giden IP kapasitesini hesaplarken küme otomatik Scaler ve düğüm havuzu yükseltmeleri olasılığa yönelik hesaba sahip olmanız gerekir. Küme otomatik yüklemesi için geçerli düğüm sayısını ve en fazla düğüm sayısını gözden geçirin ve daha yüksek değeri kullanın. Yükseltme için, yükseltmeye izin veren her düğüm havuzu için ek bir düğüm VM 'si hesabı.

- *Idletimeoutınminutes* değerini varsayılan değer olan 30 dakikadan farklı bir değere ayarlarken, iş yüklerinizin giden bir bağlantıya ne kadar süreyle ihtiyacı olacağını düşünün. Ayrıca, AKS dışında kullanılan *Standart* SKU yük dengeleyici için varsayılan zaman aşımı değerini 4 dakikadır. Belirli AKS iş yükünüzü daha doğru bir şekilde yansıtan bir *ıdletimeoutınminutes* değeri, artık kullanılmayan bağlantıların AYıKLANMASıNDAN kaynaklanan SNAT tükenmesi azalmasına yardımcı olabilir.

> [!WARNING]
> *AllocatedOutboundPorts* ve *ıdletimeoutınminutes* değerlerinin değiştirilmesi, yük dengeleyiciniz için giden kuralın davranışını önemli ölçüde değiştirebilir ve bu değerleri, avantajları ve uygulamanızın bağlantı düzenlerini anlamadan daha açık bir şekilde yapılmamalıdır, bu değerleri güncelleştirmeden önce, değişikliklerinizin etkisini tam olarak anlamak için [aşağıdaki][troubleshoot-snat] [Load Balancer giden kuralları][azure-lb-outbound-rules-overview] ve [giden bağlantıları][azure-lb-outbound-connections] gözden geçirin.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Gelen trafiği belirli IP aralıklarıyla kısıtla

Aşağıdaki bildirim, gelen dış trafik için yeni bir IP aralığı belirtmek üzere *Loadbalancersourceranges* kullanır:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> Gelen, dış trafik, AKS kümeniz için yük dengeleyiciden sanal ağa akar. Sanal ağın, yük dengeleyiciden gelen tüm trafiğe izin veren bir ağ güvenlik grubu (NSG) vardır. Bu NSG, yük dengeleyiciden gelen trafiğe izin vermek için *LoadBalancer* türünde bir [hizmet etiketi][service-tags] kullanır.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>İstemci IP 'sini gelen bağlantılarda koruyun

Varsayılan olarak, `LoadBalancer` [Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) ve aks içindeki türünde bir hizmet, Pod BAĞLANTıSı üzerindeki istemcinin IP adresini kalıcı olarak tutmaz. Pod 'a gönderilen paketteki kaynak IP, düğümün özel IP 'si olacaktır. İstemcinin IP adresini korumak için, `service.spec.externalTrafficPolicy` hizmetini hizmet tanımında olarak ayarlamanız gerekir `local` . Aşağıdaki bildirimde bir örnek gösterilmektedir:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Kubernetes ek açıklamaları aracılığıyla ek özelleştirmeler

Bu, türü ile Kubernetes Hizmetleri için desteklenen ek açıklamaların bir listesidir `LoadBalancer` , bu ek açıklamalar yalnızca **gelen** akışlar için geçerlidir:

| Ek Açıklama | Değer | Açıklama
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` veya `false`                     | Yük dengeleyicinin iç olup olmayacağını belirtin. Ayarlanmamışsa genel olarak varsayılan olarak ayarlanmıştır.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Alt ağın adı                    | İç yük dengeleyicinin hangi alt ağa bağlanması gerektiğini belirtin. Bu, ayarlanmamışsa, bulut yapılandırma dosyasında yapılandırılan alt ağın varsayılan olarak ayarlanmamakta.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Genel IP 'lerde DNS etiketinin adı   | **Ortak** HIZMET için DNS etiketi adını belirtin. Boş dizeye ayarlanırsa, genel IP 'deki DNS girişi kullanılmaz.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` veya `false`                     | Hizmetin, başka bir hizmetle paylaşılabilecek bir Azure güvenlik kuralı kullanılarak sunulduğunu ve açığa çıkarılabileceğiniz hizmet sayısında bir artış için bir artış kuralları için bir artış olduğunu belirtin. Bu ek açıklama, ağ güvenlik gruplarının Azure [genişletilmiş güvenlik kuralları](../virtual-network/network-security-groups-overview.md#augmented-security-rules) özelliğine bağımlıdır. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Kaynak grubunun adı            | Küme altyapısı (düğüm kaynak grubu) ile aynı kaynak grubunda olmayan yük dengeleyici genel IP 'Lerinin kaynak grubunu belirtin.
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | İzin verilen hizmet etiketlerinin listesi          | Virgülle ayrılmış izin verilen [hizmet etiketlerinin][service-tags] bir listesini belirtin.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Dakikalar içinde TCP boş zaman aşımları          | Yük dengeleyicide, TCP bağlantısı boşta kalma zaman aşımlarının gerçekleşmesi için dakika cinsinden süreyi belirtin. Varsayılan ve en küçük değer 4 ' dir. En büyük değer 30 ' dur. Bir tamsayı olmalıdır.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | SLB için devre dışı bırak `enableTcpReset`


## <a name="troubleshooting-snat"></a>SNAT sorunlarını giderme

Aynı hedef IP adresine ve bağlantı noktasına giden çok sayıda giden TCP veya UDP bağlantısı başlatdığınızı ve başarısız olmuş bağlantıları gözlemlebildiğinizi veya SNAT bağlantı noktalarını (PAT tarafından kullanılan önceden ayrılan kısa ömürlü bağlantı noktaları) tüketmenin bir şekilde önerdiğini biliyorsanız, çeşitli genel risk azaltma seçenekleriniz vardır. Bu seçenekleri gözden geçirin ve senaryonuz için nelerin kullanılabilir ve en iyisi olduğuna karar verin. Bir veya daha fazla bu senaryonun yönetilmesine yardımcı olabilir. Ayrıntılı bilgi için [giden bağlantılar sorun giderme kılavuzunu](../load-balancer/troubleshoot-outbound-connection.md)gözden geçirin.

SNAT tükenmesi 'nın temel nedeni, giden bağlantının kurulduğu, yönetildiği veya yapılandırılabilir zamanlayıcılar değerlerinin varsayılan değerlerinden nasıl değiştiği için bir kenar modeldir. Bu bölümü dikkatli bir şekilde inceleyin.

### <a name="steps"></a>Adımlar
1. Bağlantılarınızın uzun süre boşta kalıp kalmamasını denetleyin ve bu bağlantı noktasını serbest bırakmak için varsayılan boşta kalma zaman aşımını kullanır. Bu durumda 30 dakikalık varsayılan zaman aşımı, senaryonuz için azaltılmalıdır.
2. Uygulamanızın giden bağlantı oluşturma şeklini araştırın (örneğin, kod incelemesi veya paket yakalama).
3. Bu etkinliğin beklenen davranış olup olmadığını veya uygulamanın hatalı çalışıp çalışmadığını belirleme. Bulgularınızı eklemek için Azure Izleyici 'de [ölçümleri](../load-balancer/load-balancer-standard-diagnostics.md) ve [günlükleri](../load-balancer/load-balancer-monitor-log.md) kullanın. Örneğin, SNAT bağlantıları ölçümü için "başarısız" kategorisini kullanın.
4. Uygun [desenlerin](#design-patterns) izlendikten sonra değerlendirin.
5. SNAT bağlantı noktası tükenmesi 'nin [ek gıden IP adresleri + ayrılan ek giden bağlantı noktalarıyla](#configure-the-allocated-outbound-ports) azaltılıp azaltılmasının gerekip gerekmediğini değerlendirin.

### <a name="design-patterns"></a>Tasarım desenleri
Mümkün olduğunda her zaman bağlantı yeniden kullanımı ve bağlantı havuzlarından yararlanın. Bu desenler, kaynak tükenmesi sorunlarından kaçınır ve öngörülebilir davranışa neden olur. Bu desenlerin temelleri birçok geliştirme kitaplığı ve çerçeve içinde bulunabilir.

- Atomik istekler (bağlantı başına bir istek) genellikle iyi bir tasarım seçeneği değildir. Bu şekilde, bu tür bir desenler ölçeği sınırlar, performansı azaltır ve güvenilirliği azaltır. Bunun yerine, bağlantı sayısını ve ilişkili SNAT bağlantı noktalarını azaltmak için HTTP/S bağlantılarını yeniden kullanın. TLS kullanırken, daha düşük el sıkışmaları, ek yük ve şifreleme işlemi maliyeti nedeniyle uygulama ölçeği artar ve performans artar.
- Küme dışı/özel DNS kullanıyorsanız veya coreDNS üzerindeki özel yukarı akış sunucuları, istemci DNS çözümleyiciler sonucunu önbelleğe alırken her birimde çok sayıda akış getirebileceğini unutmayın. Özel DNS sunucuları kullanmak yerine coreDNS 'i özelleştirdiğinizden emin olun ve iyi bir önbelleğe alma değeri tanımlayın.
- UDP akışları (örneğin, DNS aramaları), boşta kalma zaman aşımı süresi boyunca SNAT bağlantı noktalarını ayırır. Boşta kalma zaman aşımı, SNAT bağlantı noktalarında basınç arttıkça daha uzun olur. Kısa boşta kalma zaman aşımı (örneğin 4 dakika) kullanın.
Bağlantı birimlerinizi şekillendirmek için bağlantı havuzlarını kullanın.
- Hiçbir şekilde hiçbir TCP akışını sessizce iptal edin ve akışı temizlemek için TCP zamanlayıcılarını güvenin. Bağlantıyı açık bir şekilde kapatmaya izin vermezseniz, durum ara sistemlere ve uç noktalara ayrılır ve SNAT bağlantı noktalarını diğer bağlantılar için kullanılamaz hale getirir. Bu model, uygulama başarısızlıklarını ve SNAT tükenmesi 'ni tetikleyebilir.
- Uzman etkisi olmadan, işletim sistemi düzeyinde TCP ile ilgili süreölçer değerlerini değiştirmeyin. TCP yığını kurtarılarken, bir bağlantının uç noktalarında yanlış beklentiler olduğunda uygulama performansı olumsuz etkilenebilir. Zamanlayıcı değiştirme sırasında, genellikle temel alınan tasarım sorununun bir işareti bulunur. Aşağıdaki önerileri gözden geçirin.


Yukarıdaki örnek, kuralı yalnızca *MY_EXTERNAL_IP_RANGE* aralığından gelen dış trafiğe izin verecek şekilde güncelleştirir. *MY_EXTERNAL_IP_RANGE* iç alt ağ IP adresiyle değiştirirseniz, trafik yalnızca Iç IP 'leri kümele kısıtlıdır. Bu, Kubernetes kümenizin dışından istemcilerin yük dengeleyiciye erişmesine izin vermez.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Temel bir SKU yük dengeleyiciden standart SKU 'ya taşıma

Temel SKU 'nun Load Balancer var olan bir kümeniz varsa, standart SKU Load Balancer sahip bir küme kullanmaya geçiş yaparken dikkat etmeniz önemli davranış farklılıkları vardır.

Örneğin, kümeleri geçirmek için mavi/yeşil dağıtımlar yapmak, `load-balancer-sku` küme türü için yalnızca küme oluşturma zamanında tanımlanmış olan yaygın bir uygulamadır. Ancak, *temel SKU* yük dengeleyiciler *, standart SKU IP adreslerini gerektirdiğinden* *Standart* SKU yük dengeleyiciler Ile uyumlu olmayan *temel SKU* IP adreslerini kullanır. Kümeleri Load Balancer SKU 'Larına geçirirken, uyumlu bir IP adresi SKU 'SU olan yeni bir IP adresi gerekir.

Kümeleri geçirme hakkında daha fazla bilgi için, geçiş yaparken göz önünde bulundurmanız gereken önemli konuların bir listesini görüntülemek için [geçiş konuları hakkındaki belgelerimizi](aks-migration.md) ziyaret edin. Aşağıdaki sınırlamalar Ayrıca AKS 'de standart SKU yük dengeleyicileri kullanırken dikkat edilecek önemli davranış farklarıdır.

## <a name="limitations"></a>Sınırlamalar

*Standart* SKU ile yük dengeleyiciyi destekleyen aks kümelerini oluşturup yönetirken aşağıdaki sınırlamalar geçerlidir:

* AKS kümesinden giden trafiğe izin vermek için en az bir genel IP veya IP öneki gereklidir. Genel IP veya IP öneki ayrıca denetim düzlemi ve aracı düğümleri arasındaki bağlantıyı sürdürmek ve AKS 'nin önceki sürümleriyle uyumluluğu sürdürmek için de gereklidir. *Standart* bir SKU yük dengeleyiciye sahip genel IP 'LERI veya IP öneklerini belirtmek için aşağıdaki seçenekleriniz vardır:
    * Kendi genel IP 'nizi sağlayın.
    * Kendi genel IP öneklerinizi sağlayın.
    * AKS kümesinin, genellikle başlangıçta *Mc_* olarak adlandırılan aks kümesi olarak oluşturulan aynı kaynak grubunda *Standart* SKU genel IP 'leri oluşturmasına izin vermek için en fazla 100 ' a kadar bir sayı belirtin. AKS, genel IP 'yi *Standart* SKU yük dengeleyicisine atar. Varsayılan olarak, genel IP, genel IP öneki veya IP sayısı belirtilmemişse, AKS kümesiyle aynı kaynak grubunda bir genel IP otomatik olarak oluşturulur. Ayrıca, genel adreslere izin vermeniz ve IP oluşturma ile ilgili herhangi bir Azure Ilkesinin oluşturulmasını önetmeniz gerekir.
* AKS tarafından oluşturulan genel IP, kendi genel IP adresini özel bir getir olarak yeniden kullanılamaz. Tüm özel IP adreslerinin Kullanıcı tarafından oluşturulması ve yönetilmesi gerekir.
* Yük dengeleyici SKU 'SU tanımlama yalnızca bir AKS kümesi oluşturduğunuzda yapılabilir. Bir AKS kümesi oluşturulduktan sonra yük dengeleyici SKU 'sunu değiştiremezsiniz.
* Tek bir kümede yalnızca bir tür yük dengeleyici SKU 'SU (temel veya standart) kullanabilirsiniz.
* *Standart* SKU yük dengeleyiciler yalnızca *Standart* SKU IP adreslerini destekler.


## <a name="next-steps"></a>Sonraki adımlar

Kubernetes Services [belgelerindeki][kubernetes-services]Kubernetes hizmetleri hakkında daha fazla bilgi edinin.

[Aks iç Load Balancer belgelerindeki](internal-lb.md)gelen trafik için dahili Load Balancer kullanma hakkında daha fazla bilgi edinin.

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
