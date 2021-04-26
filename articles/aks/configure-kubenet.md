---
title: Azure Kubernetes Service (AKS) ' de Kubernetes kullanan ağını yapılandırma
description: Azure Kubernetes Service 'te (AKS) Kubernetes kullanan (temel) ağını, var olan bir sanal ağ ve alt ağa bir AKS kümesi dağıtmak için nasıl yapılandıracağınızı öğrenin.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: c373e45c8607f10c36f40a23c776bd081bf13207
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789528"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde kendi IP adresi aralıklarınız ile Kubernetes kullanan ağını kullanma

Varsayılan olarak, aks kümeleri [Kubernetes kullanan][kubenet]kullanır ve sizin için bir Azure sanal ağı ve alt ağı oluşturulur. *Kubernetes kullanan* ile, düğümler Azure sanal ağ alt ağından bir IP adresi alır. Podlar, düğümlerin Azure sanal ağ alt ağından mantıksal olarak farklı olan bir adres alanından IP adresi alır. Ardından podların Azure sanal ağındaki kaynaklara erişebilmesi için ağ adresi çevirisi (NAT) yapılandırması gerçekleştirilir. Trafiğin kaynak IP adresi NAT ' dır ve düğümün birincil IP adresidir. Bu yaklaşım, Pod 'nin kullanabilmesi için ağ alanınızda ayırmanız gereken IP adresi sayısını önemli ölçüde azaltır.

[Azure Container ağ arabirimi (CNı)][cni-networking]ile her Pod, alt ağdan bir IP adresi alır ve doğrudan erişilebilir. Bu IP adresleri, ağ alanınızda benzersiz olmalı ve önceden planlanmalıdır. Her düğümün desteklediği en fazla sayıda düğüm için bir yapılandırma parametresi vardır. Düğüm başına düşen IP adresi sayısı, bu düğüm için önde ayrılır. Bu yaklaşım daha fazla planlama gerektirir ve genellikle, uygulamanızın beklentilerine göre daha büyük bir alt ağda küme yeniden oluşturma gereksinimiyle sonuçlanır. Küme oluşturma sırasında veya yeni düğüm havuzları oluştururken bir düğüme dağıtılabilir maksimum dizin sayısını yapılandırabilirsiniz. Yeni düğüm havuzları oluştururken maxPods belirtmezseniz, kubenet için varsayılan bir 110 değeri alırsınız.

Bu makalede, bir aks kümesi için bir sanal ağ alt ağı oluşturmak ve kullanmak için *Kubernetes kullanan* Networking 'in nasıl kullanılacağı gösterilmektedir. Ağ seçenekleri ve konuları hakkında daha fazla bilgi için bkz. [Kubernetes ve AKS Için ağ kavramları][aks-network-concepts].

## <a name="prerequisites"></a>Önkoşullar

* AKS kümesinin sanal ağı giden internet bağlantısına izin vermelidir.
* Aynı alt ağda birden fazla AKS kümesi oluşturmayın.
* Aks kümeleri,,, `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` veya `192.0.2.0/24` Kubernetes hizmeti adres aralığı, Pod adres aralığı veya küme sanal ağ adresi aralığı için kullanılamıyor olabilir.
* AKS kümesi tarafından kullanılan küme kimliği, sanal ağınızdaki alt ağda en az bir [ağ katılımcısı](../role-based-access-control/built-in-roles.md#network-contributor) rolüne sahip olmalıdır. Ayrıca, bir küme kimliği oluşturmak ve bu izinleri atamak için abonelik sahibi gibi uygun izinlere sahip olmanız gerekir. Yerleşik ağ katılımcısı rolünü kullanmak yerine [özel bir rol](../role-based-access-control/custom-roles.md) tanımlamak istiyorsanız aşağıdaki izinler gereklidir:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Windows Server düğüm havuzlarını kullanmak için Azure CNı kullanmanız gerekir. Ağ modeli olarak Kubernetes kullanan kullanımı Windows Server kapsayıcıları için kullanılamaz.

## <a name="before-you-begin"></a>Başlamadan önce

Azure CLı sürüm 2.0.65 veya sonraki bir sürümün yüklü ve yapılandırılmış olması gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Kendi alt ağınızla Kubernetes kullanan ağlarına genel bakış

Birçok ortamda, ayrılmış IP adresi aralıklarına sahip sanal ağları ve alt ağları tanımladınız. Bu sanal ağ kaynakları, birden çok hizmeti ve uygulamayı desteklemek için kullanılır. Aks kümeleri, ağ bağlantısı sağlamak için *Kubernetes kullanan* (temel ağ) veya Azure CNI (*Gelişmiş ağ*) kullanabilir.

*Kubernetes kullanan* ile yalnızca düğümler sanal ağ alt ağında bir IP adresi alır. Pods birbirleriyle doğrudan iletişim kuramaz. Bunun yerine, düğümler arasında yer alan bağlantı için Kullanıcı tanımlı yönlendirme (UDR) ve IP iletimi kullanılır. Varsayılan olarak, UDRs ve IP iletme yapılandırması AKS hizmeti tarafından oluşturulur ve saklanır, ancak [özel yol yönetimi için kendi yol tablonuzu getirme][byo-subnet-route-table]seçeneğine ihtiyacınız vardır. Ayrıca, atanan IP adresi alan bir hizmetin arkasında yer alan ve uygulama için Yük Dengeleme trafiği dağıtımını yapabilirsiniz. Aşağıdaki diyagramda, AKS düğümlerinin sanal ağ alt ağında IP adresi alma, ancak bunların olmaması gösterilmektedir:

![AKS kümesi ile kubenet ağ modeli](media/use-kubenet/kubenet-overview.png)

Azure, bir UDR 'de en fazla 400 yolu destekler, bu nedenle 400 düğümden daha büyük bir AKS kümeniz olamaz. Aks [sanal düğümleri][virtual-nodes] ve Azure ağ ilkeleri, *Kubernetes kullanan* ile desteklenmez.  [Calıco ağ ilkelerini][calico-network-policies], kubenet ile desteklendiği için kullanabilirsiniz.

*Azure CNI* ile her Pod, IP alt ağında bir IP adresi alır ve diğer Pod ve hizmetlerle doğrudan iletişim kurabilir. Kümeleriniz, belirttiğiniz IP adresi aralığı kadar büyük olabilir. Bununla birlikte, IP adresi aralığı önceden planlanmalıdır ve tüm IP adresleri AKS düğümleri tarafından destekleyeceği maksimum düğüm sayısına göre tüketilir. *Azure CNI* Ile [sanal düğümler][virtual-nodes] veya ağ ilkeleri (Azure ya da calıco) gibi gelişmiş ağ özellikleri ve senaryolar desteklenir.

### <a name="limitations--considerations-for-kubenet"></a>Kubernetes kullanan için sınırlamalar & konuları

* Kubenet tasarımında, Pod iletişimine küçük gecikme süresi ekleyen ek bir atlama gerekir.
* Rota tabloları ve Kullanıcı tanımlı yollar, işlemlere karmaşıklık ekleyen kubenet kullanımı için gereklidir.
* Kubernetes kullanan tasarımı nedeniyle, Kubernetes kullanan için doğrudan Pod adresleme desteklenmez.
* Azure CNı kümelerinin aksine, birden fazla Kubernetes kullanan kümesi bir alt ağ paylaşamaz.
* **Kubernetes kullanan üzerinde desteklenmeyen** özellikler şunlardır:
   * [Azure ağ ilkeleri](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy), ancak calıco ağ ilkeleri Kubernetes kullanan üzerinde destekleniyor
   * [Windows düğüm havuzları](./windows-faq.md)
   * [Sanal düğümler eklentisi](virtual-nodes.md#network-requirements)

### <a name="ip-address-availability-and-exhaustion"></a>IP adresi kullanılabilirliği ve tükenmesi

*Azure CNI* ile yaygın bir sorun, atanan IP adresi aralığı, bir kümeyi ölçeklendirerek veya yükselttiğinizde ek düğümler ekleyebilmek için çok küçüktür. Ağ ekibi, beklenen uygulama taleplerinizi desteklemek için yeterince büyük bir IP adresi aralığı yayınlamayabilir.

Bir uzlaşma olması halinde, *Kubernetes kullanan* kullanan bir aks kümesi oluşturabilir ve var olan bir sanal ağ alt ağına bağlanabilirsiniz. Bu yaklaşım, düğüm tanımlı IP adreslerini, kümede çalışabilecek tüm olası düğüm için çok sayıda IP adresi ayırmak zorunda kalmadan bir şekilde almasına olanak tanır.

*Kubernetes kullanan* ile çok daha küçük bir IP adresi aralığı kullanabilir ve büyük kümeleri ve uygulama taleplerini destekleyebilirsiniz. Örneğin, alt ağınızda bir */27* IP adres aralığı olsa bile, ölçeklendirmek veya yükseltmek için yeterli odaya sahip bir 20-25 düğüm kümesi çalıştırabilirsiniz. Bu küme boyutu, *2200-2750* Pod 'yi (düğüm başına varsayılan en fazla 110 Pod) destekler. Aks 'de *Kubernetes kullanan* ile yapılandırabileceğiniz düğüm başına en fazla düğüm sayısı 110 ' dir.

Aşağıdaki temel hesaplamalar, ağ modellerindeki farkı karşılaştırın:

- **Kubernetes kullanan** -basit */24* IP adresi aralığı kümede en fazla *251* düğümü destekleyebilir (her Azure sanal ağ alt ağı yönetim işlemleri için ilk üç IP adresini ayırır)
  - Bu düğüm sayısı en fazla *27.610* Pod destekleyebilir ( *Kubernetes kullanan* ile düğüm başına varsayılan en fazla 110 Pod)
- **Azure CNI** -aynı temel */24* alt ağ aralığı yalnızca kümede en fazla *8* düğüm destekleyebilir
  - Bu düğüm sayısı yalnızca en fazla *240* tane ( *Azure CNI* ile düğüm başına en fazla 30 adet) kullanılabilir.

> [!NOTE]
> Bu en fazla UMS, hesap yükseltme veya ölçeklendirme işlemlerine sahip değildir. Uygulamada, alt ağ IP adresi aralığının desteklediği en fazla düğüm sayısını çalıştıramazsınız. Yükseltme işlemlerinin ölçeği sırasında kullanılmak üzere bazı IP adreslerini kullanılabilir bırakmanız gerekir.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Sanal ağ eşlemesi ve ExpressRoute bağlantıları

Şirket içi bağlantı sağlamak için hem *Kubernetes kullanan* hem de *Azure-CNI* ağ yaklaşımları, [Azure sanal ağ eşlemesi][vnet-peering] veya [ExpressRoute bağlantılarını][express-route]kullanabilir. Çakışan ve hatalı trafik yönlendirmeyi engellemek için IP adresi aralıklarınızı dikkatle planlayın. Örneğin, birçok şirket içi ağ, ExpressRoute bağlantısı üzerinden tanıtılan bir *10.0.0.0/8* adres aralığı kullanır. AKS kümelerinizi bu adres aralığının dışında ( *172.16.0.0/16* gibi) Azure sanal ağ alt ağlarına oluşturmanız önerilir.

### <a name="choose-a-network-model-to-use"></a>Kullanılacak bir ağ modeli seçin

AKS kümeniz için hangi ağ eklentisinin kullanılacağını tercih etmek, genellikle esneklik ve gelişmiş yapılandırma gereksinimleriyle ilgili bir dengedir. Aşağıdaki önemli noktalar, her bir ağ modeli en uygun olabilir.

Şu durumlarda *Kubernetes kullanan* kullanın:

- Sınırlı IP adresi alanı var.
- Pod iletişiminin çoğu küme içinde bulunur.
- Sanal düğümler veya Azure ağ Ilkesi gibi gelişmiş AKS özelliklerine ihtiyacınız yoktur.  [Calıco ağ ilkelerini][calico-network-policies]kullanın.

*Azure CNI* şu durumlarda kullanın:

- Kullanılabilir IP adresi alanı var.
- Pod iletişiminin çoğu, küme dışındaki kaynaklara göre yapılır.
- POD bağlantısı için Kullanıcı tanımlı yolları yönetmek istemezsiniz.
- Sanal düğümler veya Azure ağ Ilkesi gibi gelişmiş özelliklere ihtiyacınız vardır.  [Calıco ağ ilkelerini][calico-network-policies]kullanın.

Hangi ağ modelini kullanacağınıza karar vermenize yardımcı olacak daha fazla bilgi için bkz. [ağ modellerini ve bunların destek kapsamını karşılaştırın][network-comparisons].

## <a name="create-a-virtual-network-and-subnet"></a>Sanal ağ ve alt ağ oluşturma

*Kubernetes kullanan* ve kendi sanal ağ alt ağınızı kullanmaya başlamak için önce [az Group Create][az-group-create] komutunu kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Kullanmak için mevcut bir sanal ağınız ve alt ağınız yoksa, [az Network VNET Create][az-network-vnet-create] komutunu kullanarak bu ağ kaynaklarını oluşturun. Aşağıdaki örnekte, sanal ağ, *192.168.0.0/16* adres ön ekine sahip *myvnet* olarak adlandırılmıştır. *192.168.1.0/24* adres ön ekine sahip *Myakssubnet* adlı bir alt ağ oluşturulur.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Hizmet sorumlusu oluşturma ve izin atama

Bir AKS kümesinin diğer Azure kaynaklarıyla etkileşime geçmesini sağlamak için bir Azure Active Directory hizmet sorumlusu kullanılır. Hizmet sorumlusunun, AKS düğümlerinin kullandığı sanal ağı ve alt ağı yönetmek için izinleri olması gerekir. Hizmet sorumlusu oluşturmak için [az ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] komutunu kullanın:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Aşağıdaki örnek çıktı, hizmet sorumlunuz için uygulama KIMLIĞINI ve parolayı gösterir. Bu değerler, hizmet sorumlusuna bir rol atamak ve ardından AKS kümesini oluşturmak için ek adımlarda kullanılır:

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Kalan adımlarda doğru temsilcileri atamak için [az Network VNET Show][az-network-vnet-show] komutunu kullanın, az [Network VNET subnet][az-network-vnet-subnet-show] , gerekli kaynak kimliklerini almak için komutları göster komutlarını kullanın. Bu kaynak kimlikleri, değişkenler olarak depolanır ve kalan adımlarda başvurulur:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Şimdi, [az role atama Create][az-role-assignment-create] komutunu kullanarak sanal ağ üzerinde aks küme *ağı katılımcısı* izinleriniz için hizmet sorumlusu atayın. *\<appId>* Hizmet sorumlusu oluşturmak için önceki komutun çıktısında gösterilen şekilde kendinizinkini sağlayın:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Sanal ağda AKS kümesi oluşturma

Artık bir sanal ağ ve alt ağ oluşturdunuz ve bu ağ kaynaklarını kullanmak için bir hizmet sorumlusu için oluşturduğunuz ve atanan izinler. Şimdi [az aks Create][az-aks-create] komutunu kullanarak sanal ağınızda ve alt ağınızda bir aks kümesi oluşturun. *\<appId>* *\<password>* Hizmet sorumlusunu oluşturmak için önceki komutun çıktısında gösterildiği gibi, kendi hizmet sorumlunuzu tanımlayın.

Aşağıdaki IP adresi aralıkları, küme oluşturma işleminin parçası olarak da tanımlanmıştır:

* *--Service-CIDR* , aks kümesinde iç HIZMETLERI bir IP adresi atamak için kullanılır. Bu IP adresi aralığı, ağ ortamınızda başka bir yerde kullanımda olmayan bir adres alanı olmalıdır. Bu, şirket içi ağ aralıkları de dahil olmak üzere, Express Route veya siteden siteye VPN bağlantısı kullanarak Azure sanal ağlarınıza bağlanabilirsiniz.

* *--DNS-Service-ip* adresi, hizmet IP adresi aralığınızı *.10* adresi olmalıdır.

* *--Pod-CIDR* , ağ ortamınızda başka bir yerde kullanımda olmayan büyük bir adres alanı olmalıdır. Bu Aralık, Express Route veya siteden siteye VPN bağlantısı kullanarak Azure sanal ağlarınızı bağladığınızda veya bağlanmayı planlıyorsanız şirket içi ağ aralıklarını içerir.
    * Bu adres aralığı, ölçeğini genişletmek istediğiniz düğüm sayısına uyacak kadar büyük olmalıdır. Ek düğümler için daha fazla adrese ihtiyacınız varsa, küme dağıtıldıktan sonra bu adres aralığını değiştiremezsiniz.
    * Pod IP adresi aralığı, kümedeki her düğüme */24* adres alanı atamak için kullanılır. Aşağıdaki örnekte, *--Pod-CIDR* *10.244.0.0/16* ilk düğümü *10.244.0.0/24*, ikinci düğüm *10.244.1.0/24* ve üçüncü düğüm *10.244.2.0/24* olarak atar.
    * Küme ölçeklenirken veya yükseltirken, Azure platformu her yeni düğüme bir pod IP adres aralığı atamaya devam eder.
    
* *--Docker-Bridge-Address* , aks düğümlerinin temel alınan yönetim platformuyla iletişim kurmasına olanak tanır. Bu IP adresi, kümenizin sanal ağ IP adresi aralığı içinde olmamalı ve ağınızda kullanılmakta olan diğer adres aralıklarıyla çakışmamalıdır.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

> [!Note]
> Bir AKS kümesini [Calıco ağ ilkesi][calico-network-policies] içerecek şekilde etkinleştirmek istiyorsanız aşağıdaki komutu kullanabilirsiniz.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Bir AKS kümesi oluşturduğunuzda, otomatik olarak bir ağ güvenlik grubu ve yol tablosu oluşturulur. Bu ağ kaynakları AKS denetim düzlemi tarafından yönetilir. Ağ güvenlik grubu, düğümlerinizin sanal NIC 'Leri ile otomatik olarak ilişkilendirilir. Yol tablosu, sanal ağ alt ağıyla otomatik olarak ilişkilendirilir. Ağ güvenlik grubu kuralları ve yol tabloları, hizmetleri oluşturup kullanıma sunan otomatik olarak güncelleştirilir.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Kubernetes kullanan ile kendi alt ağını ve yol tablonuzu getirme

Kubenet ile, küme alt ağlarınız üzerinde bir yol tablosu bulunmalıdır. AKS, kendi mevcut alt ağlarınızı ve yol tablonuzu getirme desteği sunmaktadır.

Özel alt ağınız bir yol tablosu içermiyorsa, AKS sizin için bir tane oluşturur ve küme yaşam döngüsü boyunca ona kurallar ekler. Kümenizi oluştururken özel alt ağınız bir yol tablosu içeriyorsa, AKS, küme işlemleri sırasında mevcut yol tablosunu onaylar ve bulut sağlayıcısı işlemlerine göre kuralları ekler/güncelleştirir.

> [!WARNING]
> Özel kurallar özel yol tablosuna eklenebilir ve güncelleştirilir. Ancak, kurallar, güncelleştirilmeleri veya kaldırılması gereken Kubernetes bulut sağlayıcısı tarafından eklenir. 0.0.0.0/0 gibi kurallar her zaman belirli bir yol tablosunda bulunmalı ve bir NVA veya diğer çıkış ağ geçidi gibi internet ağ geçidinizin hedefine eşlenir. Yalnızca özel kurallarınızın değiştirildiği kuralları güncelleştirirken dikkatli olun.

[Özel bir yol tablosu][custom-route-table]ayarlama hakkında daha fazla bilgi edinin.

Kubenet Networking, istekleri başarıyla yönlendirmek için düzenlenmiş yol tablosu kuralları gerektirir. Bu tasarım nedeniyle, yol tablolarının kendisine bağımlı olan her küme için dikkatle tutulması gerekir. Farklı kümelerden Pod Cıdrs, beklenmedik ve hatalı yönlendirmeye yol açacağından, birden çok küme bir yol tablosu paylaşamaz. Aynı sanal ağ üzerinde birden fazla küme yapılandırırken veya bir sanal ağı her kümeye ayırma sırasında, aşağıdaki kısıtlamaların değerlendirildiğinden emin olun.

Sınırlamalar:

* Küme oluşturmadan önce izinler atanmalıdır, özel alt ağınız ve özel yol tablonuz için yazma izinlerine sahip bir hizmet sorumlusu kullandığınızdan emin olun.
* AKS kümesini oluşturmadan önce özel bir yol tablosunun alt ağ ile ilişkilendirilmesi gerekir.
* İlişkili yol tablosu kaynağı, küme oluşturulduktan sonra güncelleştirilemez. Yol tablosu kaynağı güncelleştirilemediğinden, yönlendirme tablosunda özel kurallar değiştirilebilir.
* Her bir AKS kümesi, kümeyle ilişkili tüm alt ağlar için tek bir benzersiz yol tablosu kullanmalıdır. Çakışan Pod Cıdrs ve çakışan yönlendirme kuralları nedeniyle potansiyel olarak birden çok küme içeren bir yol tablosunu yeniden kullanamazsınız.

Özel bir yol tablosu oluşturup sanal ağınızdaki alt ağınızla ilişkilendirdikten sonra, yol tablonuzu kullanan yeni bir AKS kümesi oluşturabilirsiniz.
AKS kümenizi dağıtmayı planladığınız alt ağ KIMLIĞINI kullanmanız gerekir. Bu alt ağ aynı zamanda özel yol tablonuz ile ilişkilendirilmesi gerekir.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>Sonraki adımlar

Var olan sanal ağ alt ağınıza dağıtılmış bir AKS kümesi ile, artık kümeyi normal olarak kullanabilirsiniz. [Held kullanarak yeni uygulamalar oluşturmaya][develop-helm] veya [Held kullanarak mevcut uygulamaları dağıtmaya][use-helm]başlayın.

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az_group_create
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az_network_vnet_show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
