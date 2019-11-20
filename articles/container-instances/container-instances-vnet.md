---
title: Azure sanal ağına kapsayıcı örnekleri dağıtma
description: Yeni veya mevcut bir Azure sanal ağına kapsayıcı grupları dağıtmayı öğrenin.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: 05f1bcd5e80d7c06fbaca1abe89c84f6743a5979
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034976"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Azure sanal ağına kapsayıcı örnekleri dağıtma

[Azure sanal ağ](../virtual-network/virtual-networks-overview.md) , Azure ve şirket içi kaynaklarınız için güvenli, özel ağ sağlar. Kapsayıcı gruplarını bir Azure sanal ağına dağıtarak, kapsayıcılarınız sanal ağdaki diğer kaynaklarla güvenli bir şekilde iletişim kurabilir.

Bir Azure sanal ağına dağıtılan kapsayıcı grupları, şunun gibi senaryolar sağlar:

* Aynı alt ağdaki kapsayıcı grupları arasında doğrudan iletişim
* Kapsayıcı örneklerinden [görev tabanlı](container-instances-restart-policy.md) iş yükü çıkışını sanal ağdaki bir veritabanına gönderin
* Sanal ağdaki bir [hizmet uç](../virtual-network/virtual-network-service-endpoints-overview.md) noktasından kapsayıcı örnekleri için içerik alma
* Sanal ağdaki sanal makinelerle kapsayıcı iletişimi
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) veya [ExpressRoute](../expressroute/expressroute-introduction.md) aracılığıyla şirket içi kaynaklarla kapsayıcı iletişimi

> [!IMPORTANT]
> Bu özellik şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.


## <a name="virtual-network-deployment-limitations"></a>Sanal ağ dağıtım sınırlamaları

Bir sanal ağa kapsayıcı grupları dağıttığınızda bazı sınırlamalar geçerlidir.

* Alt ağa kapsayıcı grupları dağıtmak için, alt ağ başka bir kaynak türü içeremez. Mevcut bir alt ağdan kapsayıcı grupları dağıtmadan önce mevcut olan tüm kaynakları kaldırın veya yeni bir alt ağ oluşturun.
* Bir sanal ağa dağıtılan bir kapsayıcı grubunda [yönetilen bir kimlik](container-instances-managed-identity.md) kullanamazsınız.
* Dahil edilen ek ağ kaynakları nedeniyle, bir kapsayıcı grubunun bir sanal ağa dağıtımı genellikle standart bir kapsayıcı örneği dağıtmaktan biraz daha yavaştır.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

Bu özellik önizlemedeyken, kapsayıcı grupları bir sanal ağa dağıtıldığında aşağıdaki sınırlamalar geçerlidir. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

Kapsayıcı kaynak sınırları, bu bölgelerdeki ağa bağlı olmayan kapsayıcı örnekleri için sınırlara göre farklılık gösterebilir. Bu özellik için şu anda yalnızca Linux kapsayıcıları desteklenir. Windows desteği planlanmaktadır.

### <a name="unsupported-networking-scenarios"></a>Desteklenmeyen ağ senaryoları 

* **Azure Load Balancer** -ağa bağlı bir kapsayıcı grubundaki kapsayıcı örneklerinin önüne bir Azure Load Balancer yerleştirme desteklenmez
* **Sanal ağ eşlemesi** -başka bir sanal ağa Azure Container Instances için temsilci atanmış bir alt ağ içeren bir sanal ağı eşleyemiyoruz
* **Rota tabloları** -Kullanıcı tanımlı yollar, Azure Container Instances için temsilci atanmış bir alt ağda ayarlanamaz
* **Ağ güvenlik grupları** -Azure Container Instances atanmış bir alt ağa uygulanan NSG 'lerdeki giden güvenlik kuralları şu anda zorlanmıyor 
* **Genel IP veya DNS etiketi** -bir sanal ağa dağıtılan kapsayıcı grupları, şu anda KAPSAYıCıLARı genel IP adresi veya tam etki alanı adı ile doğrudan internet 'te kullanıma sunma desteği vermez
* İç **ad çözümleme** -sanal ağdaki Azure kaynakları için dahili Azure DNS ad çözümlemesi desteklenmez

**Ağ kaynağı silme** , kapsayıcı gruplarını sanal ağa dağıttıktan sonra [ek adımlar](#delete-network-resources) gerektirir.

## <a name="required-network-resources"></a>Gerekli ağ kaynakları

Kapsayıcı gruplarını bir sanal ağa dağıtmak için gereken üç Azure sanal ağ kaynağı vardır: [sanal ağın](#virtual-network) kendisi, sanal ağ içindeki [atanmış bir alt ağ](#subnet-delegated) ve bir [ağ profili](#network-profile). 

### <a name="virtual-network"></a>Sanal ağ

Bir sanal ağ, bir veya daha fazla alt ağ oluşturduğunuz adres alanını tanımlar. Daha sonra Azure kaynaklarını (kapsayıcı grupları gibi) sanal ağınızdaki alt ağlara dağıtırsınız.

### <a name="subnet-delegated"></a>Alt ağ (temsilci)

Alt ağlar sanal ağı, yerleştirdiğiniz Azure kaynakları tarafından kullanılabilen ayrı adres alanlarına bölüler. Bir sanal ağ içinde bir veya birden çok alt ağ oluşturursunuz.

Kapsayıcı grupları için kullandığınız alt ağ yalnızca kapsayıcı grupları içerebilir. Bir alt ağa ilk kez bir kapsayıcı grubu dağıttığınızda, Azure bu alt ağı Azure Container Instances için devreder. Temsilci seçildikten sonra alt ağ yalnızca kapsayıcı grupları için kullanılabilir. Bir temsilci alt ağına kapsayıcı grupları dışında kaynak dağıtmaya çalışırsanız, işlem başarısız olur.

### <a name="network-profile"></a>Ağ profili

Bir ağ profili, Azure kaynakları için bir ağ yapılandırması şablonudur. Kaynak için belirli ağ özelliklerini belirtir, örneğin, dağıtılması gereken alt ağ. Bir alt ağa (ve dolayısıyla bir sanal ağa) bir kapsayıcı grubu dağıtmak için [az Container Create][az-container-create] komutunu ilk kez kullandığınızda, Azure sizin için bir ağ profili oluşturur. Daha sonra bu ağ profilini alt ağa gelecek dağıtımlar için kullanabilirsiniz. 

Bir alt ağa kapsayıcı grubu dağıtmak için bir Kaynak Yöneticisi şablonu, YAML dosyası ya da bir programlama yöntemi kullanmak için, bir ağ profilinin tam Kaynak Yöneticisi kaynak KIMLIĞI sağlamanız gerekir. Daha önce [az Container Create][az-container-create]kullanılarak oluşturulmuş bir profil kullanabilir veya Kaynak Yöneticisi şablonu kullanarak bir profil oluşturabilirsiniz (bkz. [şablon örneği](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) ve [başvurusu](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Daha önce oluşturulmuş bir profilin KIMLIĞINI almak için [az Network Profile List][az-network-profile-list] komutunu kullanın. 

Aşağıdaki diyagramda, Azure Container Instances için temsilci atanmış bir alt ağa birkaç kapsayıcı grubu dağıtıldı. Bir alt ağa bir kapsayıcı grubu dağıttıktan sonra, aynı ağ profilini belirterek buna ek kapsayıcı grupları dağıtabilirsiniz.

![Bir sanal ağ içindeki kapsayıcı grupları][aci-vnet-01]

## <a name="deployment-scenarios"></a>Dağıtım senaryoları

Kapsayıcı gruplarını yeni bir sanal ağa dağıtmak ve Azure 'un sizin için gerekli ağ kaynaklarını oluşturmasına izin vermek veya mevcut bir sanal ağa dağıtmak için [az Container Create][az-container-create] ' i kullanabilirsiniz. 

### <a name="new-virtual-network"></a>Yeni sanal ağ

Yeni bir sanal ağa dağıtmak ve Azure 'un ağ kaynaklarını sizin için otomatik olarak oluşturmasını sağlamak için [az Container Create][az-container-create]çalıştırdığınızda şunları belirtin:

* Sanal ağ adı
* CıDR biçimindeki sanal ağ adresi ön eki
* Alt ağ adı
* CıDR biçimindeki alt ağ adresi öneki

Sanal ağ ve alt ağ adresi önekleri, sırasıyla sanal ağ ve alt ağ için adres alanlarını belirtir. Bu değerler, sınıfsız etki alanları arası yönlendirme (CıDR) gösterimi içinde temsil edilir, örneğin `10.0.0.0/16`. Alt ağlarla çalışma hakkında daha fazla bilgi için bkz. [sanal ağ alt ağı ekleme, değiştirme veya silme](../virtual-network/virtual-network-manage-subnet.md).

İlk kapsayıcı grubunuzu bu yöntemle dağıttıktan sonra, sanal ağ ve alt ağ adlarını veya Azure 'un sizin için otomatik olarak oluşturduğu ağ profilini belirterek aynı alt ağa dağıtım yapabilirsiniz. Azure, Azure Container Instances için alt ağ temsilciyle, alt ağa *yalnızca* kapsayıcı grupları dağıtabilirsiniz.

### <a name="existing-virtual-network"></a>Var olan sanal ağ

Mevcut bir sanal ağa bir kapsayıcı grubu dağıtmak için:

1. Var olan sanal ağınızda bir alt ağ oluşturun veya diğer *Tüm* kaynakların var olan alt ağını boşaltın
1. [Az Container Create][az-container-create] ile bir kapsayıcı grubu dağıtın ve aşağıdakilerden birini belirtin:
   * Sanal ağ adı ve alt ağ adı
   * Farklı bir kaynak grubundan bir sanal ağın kullanılmasına izin veren sanal ağ kaynak KIMLIĞI ve alt ağ kaynak KIMLIĞI
   * [Az Network Profile List][az-network-profile-list] kullanarak elde alabileceğiniz ağ profili adı veya kimliği

İlk kapsayıcı grubunuzu mevcut bir alt ağa dağıttıktan sonra, Azure bu alt ağı Azure Container Instances için devreder. Artık bu alt ağa kapsayıcı grupları dışındaki kaynakları dağıtamazsınız.

## <a name="deployment-examples"></a>Dağıtım örnekleri

Aşağıdaki bölümlerde, Azure CLı ile bir sanal ağa kapsayıcı gruplarının nasıl dağıtılacağı açıklanır. Komut örnekleri **Bash** kabuğu için biçimlendirilir. PowerShell veya komut Istemi gibi başka bir kabuğu tercih ediyorsanız, satır devamlılık karakterlerini uygun şekilde ayarlayın.

### <a name="deploy-to-a-new-virtual-network"></a>Yeni bir sanal ağa dağıtma

İlk olarak, bir kapsayıcı grubu dağıtın ve yeni bir sanal ağ ve alt ağ için parametreleri belirtin. Bu parametreleri belirttiğinizde, Azure sanal ağı ve alt ağı oluşturur, alt ağı Azure Container Instances 'a devreder ve ayrıca bir ağ profili oluşturur. Bu kaynaklar oluşturulduktan sonra kapsayıcı grubunuz alt ağa dağıtılır.

Yeni bir sanal ağ ve alt ağ için ayarları belirten aşağıdaki [az Container Create][az-container-create] komutunu çalıştırın. Bir sanal ağ içindeki kapsayıcı gruplarını [destekleyen](#preview-limitations) bir bölgede oluşturulmuş bir kaynak grubunun adını sağlamanız gerekir. Bu komut, statik bir Web sayfasına hizmet veren küçük bir Node. js web sunucusu çalıştıran genel Microsoft [aci-HelloWorld][aci-helloworld] kapsayıcısını dağıtır. Sonraki bölümde, aynı alt ağa ikinci bir kapsayıcı grubu dağıtırsınız ve iki kapsayıcı örneği arasındaki iletişimi test edeceksiniz.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

Bu yöntemi kullanarak yeni bir sanal ağa dağıtırken, ağ kaynakları oluşturulurken dağıtım birkaç dakika sürebilir. İlk dağıtımdan sonra ek kapsayıcı grubu dağıtımları daha hızlı tamamlanır.

### <a name="deploy-to-existing-virtual-network"></a>Var olan sanal ağa dağıt

Artık yeni bir sanal ağa bir kapsayıcı grubu dağıttığınıza göre, ikinci bir kapsayıcı grubunu aynı alt ağa dağıtıp iki kapsayıcı örneği arasındaki iletişimi doğrularsınız.

İlk olarak, dağıttığınız ilk kapsayıcı grubunun IP adresini alın, *AppContainer*:

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

Çıktının özel alt ağdaki kapsayıcı grubunun IP adresini görüntülemesi gerekir:

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

Şimdi, `CONTAINER_GROUP_IP` ' ı `az container show` komutuyla aldığınız IP 'ye ayarlayın ve aşağıdaki `az container create` komutunu yürütün. Bu ikinci kapsayıcı, *commchecker*, alp Linux tabanlı bir görüntü çalıştırır ve ilk kapsayıcı grubunun özel alt ağ IP adresine karşı `wget` ' i yürütür.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

Bu ikinci kapsayıcı dağıtımı tamamlandıktan sonra, yürütüldüğü `wget` komutunun çıkışını görebilmek için günlüklerini çekin:

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

İkinci kapsayıcı birinciyle başarılı bir şekilde iletiyse, çıkış şuna benzer olmalıdır:

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Günlük çıktısı `wget` ' ın, yerel alt ağdaki özel IP adresini kullanarak dizin dosyasını ilk kapsayıcıdan bağlanıp indirebilmesinin gerektiğini göstermelidir. İki kapsayıcı grubu arasındaki ağ trafiği sanal ağ içinde kaldı.

### <a name="deploy-to-existing-virtual-network---yaml"></a>Var olan sanal ağa dağıt-YAML

Ayrıca, bir YAML dosyası kullanarak var olan bir sanal ağa kapsayıcı grubu dağıtabilirsiniz. Bir sanal ağdaki bir alt ağa dağıtmak için, YAML 'de birkaç ek özellik belirtirsiniz:

* `ipAddress`: kapsayıcı grubu için IP adresi ayarları.
  * `ports`: varsa açılacak bağlantı noktaları.
  * `protocol`: açılan bağlantı noktası için protokol (TCP veya UDP).
* `networkProfile`: bir Azure kaynağı için sanal ağ ve alt ağ gibi ağ ayarlarını belirtir.
  * `id`: `networkProfile` ' in tam Kaynak Yöneticisi kaynak KIMLIĞI.

Bir kapsayıcı grubunu bir YAML dosyası içeren bir sanal ağa dağıtmak için, önce ağ profilinin KIMLIĞINI almanız gerekir. Sanal ağınızı ve Temsilcili alt ağını içeren kaynak grubunun adını belirterek [az Network Profile List][az-network-profile-list] komutunu yürütün.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

Komutun çıktısı, ağ profili için tam kaynak KIMLIĞINI görüntüler:

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Ağ profili KIMLIĞI ' ne sahip olduktan sonra, aşağıdaki YAML 'yi *VNET-Deploy-aci. YAML*adlı yeni bir dosyaya kopyalayın. @No__t-0 ' ın altında, `id` değerini yeni aldığınız KIMLIKLE değiştirin, sonra dosyayı kaydedin. Bu YAML, sanal ağınızda *appcontaineryaml* adlı bir kapsayıcı grubu oluşturur.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

@No__t-1 parametresi için YAML dosya adını belirterek [az Container Create][az-container-create] komutuyla kapsayıcı grubunu dağıtın:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

Dağıtım tamamlandıktan sonra, durumunu görüntülemek için [az Container Show][az-container-show] komutunu çalıştırın:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

### <a name="delete-container-instances"></a>Kapsayıcı örneklerini Sil

Oluşturduğunuz kapsayıcı örnekleriyle çalışmayı tamamladığınızda, bunları aşağıdaki komutlarla silin:

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>Ağ kaynaklarını silme


> [!NOTE]
> Ağ profilini kaldırmaya çalışırken bir hata alırsanız, platformun sorunu otomatik olarak azaltmanız ve silmeyi yeniden denemesi için 2-3 güne izin verin. Ağ profilini kaldırma konusunda hala sorun yaşıyorsanız [bir destek isteğinde](https://azure.microsoft.com/support/create-ticket/) bulunan ' i açın.

Bu özelliğin ilk önizlemesi, daha önce oluşturduğunuz ağ kaynaklarını silmek için birkaç ek komut gerektirir. Sanal ağınızı ve alt ağınızı oluşturmak için bu makalenin önceki bölümlerinde örnek komutları kullandıysanız, bu ağ kaynaklarını silmek için aşağıdaki betiği kullanabilirsiniz.

Betiği yürütmeden önce, `RES_GROUP` değişkenini, silinmesi gereken sanal ağı ve alt ağı içeren kaynak grubu adı olarak ayarlayın. Daha önce önerilen `aci-vnet` adını kullanmazsanız sanal ağın adını güncelleştirin. Komut dosyası bash kabuğu için biçimlendirilir. PowerShell veya komut Istemi gibi başka bir kabuğu tercih ediyorsanız, değişken atamasını ve erişimcileri buna uygun şekilde ayarlamanız gerekir.

> [!WARNING]
> Bu betik kaynakları siler! Sanal ağı ve içerdiği tüm alt ağları siler. Bu betiği çalıştırmadan önce, içerdiği tüm alt ağlar da dahil olmak üzere sanal ağdaki kaynakların *hiçbirine* artık ihtiyacınız olmadığından emin olun. **Bu kaynaklar**silindikten sonra kurtarılamaz.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Sonraki adımlar

Bir Kaynak Yöneticisi şablonu kullanarak yeni bir sanal ağ, alt ağ, ağ profili ve kapsayıcı grubu dağıtmak için bkz. [VNET Ile Azure Kapsayıcı grubu oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

Bu makalede kısaca çok sayıda sanal ağ kaynağı ve özelliği ele alınmıştır. Azure sanal ağ belgeleri bu konuları kapsamlı bir şekilde ele almaktadır:

* [Sanal ağ](../virtual-network/manage-virtual-network.md)
* [Alt ağ](../virtual-network/virtual-network-manage-subnet.md)
* [Hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
