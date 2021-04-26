---
title: Kapsayıcı grubunu Azure sanal ağı 'na dağıtma
description: Azure komut satırı arabirimini kullanarak bir kapsayıcı grubunu yeni veya mevcut bir Azure sanal ağına dağıtmayı öğrenin.
ms.topic: article
ms.date: 07/02/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 44be66957aa745179ffe4cd00db75f1d47237dfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771056"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Kapsayıcı örneklerini Azure sanal ağına dağıtma

[Azure sanal ağ](../virtual-network/virtual-networks-overview.md) , Azure ve şirket içi kaynaklarınız için güvenli, özel ağ sağlar. Kapsayıcı gruplarını bir Azure sanal ağına dağıtarak, kapsayıcılarınız sanal ağdaki diğer kaynaklarla güvenli bir şekilde iletişim kurabilir.

Bu makalede, Azure CLı 'de [az Container Create][az-container-create] komutunun yeni bir sanal ağa veya var olan bir sanal ağa kapsayıcı grupları dağıtmak için nasıl kullanılacağı gösterilmektedir. 

Ağ senaryoları ve sınırlamalar için bkz. [Azure Container Instances Için sanal ağ senaryoları ve kaynakları](container-instances-virtual-network-concepts.md).

> [!IMPORTANT]
> Bir sanal ağa kapsayıcı grubu dağıtımı, Azure Container Instances kullanılabildiği birçok bölgede genellikle Linux kapsayıcıları için kullanılabilir. Ayrıntılar için bkz. [bölgeler ve kaynak kullanılabilirliği][container-regions]. 

Bu makaledeki örnekler bash kabuğu için biçimlendirilir. PowerShell veya komut Istemi gibi başka bir kabuğu tercih ediyorsanız, satır devamlılık karakterlerini uygun şekilde ayarlayın.


## <a name="deploy-to-new-virtual-network"></a>Yeni sanal ağa dağıt

Yeni bir sanal ağa dağıtmak ve Azure 'un ağ kaynaklarını sizin için otomatik olarak oluşturmasını sağlamak için [az Container Create][az-container-create]çalıştırdığınızda şunları belirtin:

* Sanal ağın adı
* CıDR biçimindeki sanal ağ adresi ön eki
* Alt ağ adı
* CıDR biçimindeki alt ağ adresi öneki

Sanal ağ ve alt ağ adresi önekleri, sırasıyla sanal ağ ve alt ağ için adres alanlarını belirtir. Bu değerler, örneğin sınıfsız Inter-Domain yönlendirme (CıDR) gösteriminde temsil edilir `10.0.0.0/16` . Alt ağlarla çalışma hakkında daha fazla bilgi için bkz. [sanal ağ alt ağı ekleme, değiştirme veya silme](../virtual-network/virtual-network-manage-subnet.md).

İlk kapsayıcı grubunuzu bu yöntemle dağıttıktan sonra, sanal ağ ve alt ağ adlarını veya Azure 'un sizin için otomatik olarak oluşturduğu ağ profilini belirterek aynı alt ağa dağıtım yapabilirsiniz. Azure, Azure Container Instances için alt ağ temsilciyle, alt ağa *yalnızca* kapsayıcı grupları dağıtabilirsiniz.

### <a name="example"></a>Örnek

Aşağıdaki [az Container Create][az-container-create] komutu, yeni bir sanal ağ ve alt ağ için ayarları belirtir. Bir sanal ağdaki kapsayıcı grubu dağıtımlarının [kullanılabildiği](container-instances-region-availability.md)bir bölgede oluşturulan kaynak grubunun adını belirtin. Bu komut, statik bir Web sayfasına hizmet veren küçük bir Node.js Web sunucusu çalıştıran genel Microsoft [aci-HelloWorld][aci-helloworld] kapsayıcısını dağıtır. Sonraki bölümde, aynı alt ağa ikinci bir kapsayıcı grubu dağıtırsınız ve iki kapsayıcı örneği arasındaki iletişimi test edeceksiniz.

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

Bu yöntemi kullanarak yeni bir sanal ağa dağıtırken, ağ kaynakları oluşturulurken dağıtım birkaç dakika sürebilir. İlk dağıtımdan sonra, aynı alt ağa yönelik ek kapsayıcı grubu dağıtımları daha hızlı tamamlanır.

## <a name="deploy-to-existing-virtual-network"></a>Var olan sanal ağa dağıt

Mevcut bir sanal ağa bir kapsayıcı grubu dağıtmak için:

1. Var olan sanal ağınızda bir alt ağ oluşturun, bir kapsayıcı grubunun zaten dağıtıldığı var olan bir alt ağı kullanın veya diğer *Tüm* kaynakların boşaltılma var olan alt ağını kullanın
1. [Az Container Create][az-container-create] ile bir kapsayıcı grubu dağıtın ve aşağıdakilerden birini belirtin:
   * Sanal ağ adı ve alt ağ adı
   * Farklı bir kaynak grubundan bir sanal ağın kullanılmasına izin veren sanal ağ kaynak KIMLIĞI ve alt ağ kaynak KIMLIĞI
   * [Az Network Profile List][az-network-profile-list] kullanarak elde alabileceğiniz ağ profili adı veya kimliği

### <a name="example"></a>Örnek

Aşağıdaki örnek, daha önce oluşturulan aynı alt ağa ikinci bir kapsayıcı grubu dağıtır ve iki kapsayıcı örneği arasındaki iletişimi doğrular.

İlk olarak, dağıttığınız ilk kapsayıcı grubunun IP adresini alın, *AppContainer*:

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

Çıktı, özel alt ağdaki kapsayıcı grubunun IP adresini görüntüler. Örnek:

```console
10.0.0.4
```

Şimdi, `CONTAINER_GROUP_IP` komutuyla ALDıĞıNıZ IP 'ye ayarlayın `az container show` ve aşağıdaki `az container create` komutu yürütün. Bu ikinci kapsayıcı, *commchecker*, alp Linux tabanlı bir görüntü çalıştırır ve `wget` ilk kapsayıcı grubunun özel alt ağ IP adresine karşı yürütülür.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Bu ikinci kapsayıcı dağıtımı tamamlandıktan sonra, yürütüldüğü komutun çıktısını görebilmeniz için günlüklerini çekin `wget` :

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

İkinci kapsayıcı birinciyle başarılı bir şekilde iletiyse, çıkış şuna benzerdir:

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

Günlük çıktısı, `wget` yerel alt ağdaki özel IP adresini kullanarak dizin dosyasını ilk kapsayıcıdan bağlanıp indirebilmesinin gerektiğini göstermelidir. İki kapsayıcı grubu arasındaki ağ trafiği sanal ağ içinde kaldı.

### <a name="example---yaml"></a>Örnek-YAML

Ayrıca, bir YAML dosyası, [Kaynak Yöneticisi şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)veya Python SDK ile gibi başka bir programlama yöntemi kullanarak mevcut bir sanal ağa kapsayıcı grubu dağıtabilirsiniz. 

Örneğin, bir YAML dosyası kullanırken, Azure Container Instances için bir alt ağ ile sanal ağa dağıtım yapabilirsiniz. Aşağıdaki özellikleri belirtin:

* `ipAddress`: Kapsayıcı grubu için özel IP adresi ayarları.
  * `ports`: Varsa açılacak bağlantı noktaları.
  * `protocol`: Açılan bağlantı noktası için protokol (TCP veya UDP).
* `networkProfile`: Sanal ağ ve alt ağ için ağ ayarları.
  * `id`: Öğesinin tam Kaynak Yöneticisi kaynak KIMLIĞI `networkProfile` .

Ağ profilinin KIMLIĞINI almak için [az Network Profile List][az-network-profile-list] komutunu çalıştırarak Sanal ağınızı ve Temsilcili alt ağı içeren kaynak grubunun adını belirtin.

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

Örnek çıktı:

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

Ağ profili KIMLIĞI ' ne sahip olduktan sonra, aşağıdaki YAML 'yi *VNET-Deploy-aci. YAML* adlı yeni bir dosyaya kopyalayın. Altında `networkProfile` , `id` değerini az önce aldığınız kimlikle değiştirin, ardından dosyayı kaydedin. Bu YAML, sanal ağınızda *appcontaineryaml* adlı bir kapsayıcı grubu oluşturur.

```YAML
apiVersion: '2019-12-01'
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
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Parametre için YAML dosya adını belirterek, kapsayıcı grubunu [az Container Create][az-container-create] komutuyla dağıtın `--file` :

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

Dağıtım tamamlandıktan sonra, durumunu görüntülemek için [az Container Show][az-container-show] komutunu çalıştırın. Örnek çıktı:

```console
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

Bu özellik şu anda daha önce oluşturduğunuz ağ kaynaklarını silmek için birkaç ek komut gerektirir. Sanal ağınızı ve alt ağınızı oluşturmak için bu makalenin önceki bölümlerinde örnek komutları kullandıysanız, bu ağ kaynaklarını silmek için aşağıdaki betiği kullanabilirsiniz. Betik, kaynak grubunuzun tek bir ağ profiliyle tek bir sanal ağ içerdiğini varsayar.

Betiği yürütmeden önce, `RES_GROUP` değişkenini, silinmesi gereken sanal ağı ve alt ağı içeren kaynak grubu adı olarak ayarlayın. Daha önce önerilen adı kullanmıyorsanız, sanal ağın adını güncelleştirin `aci-vnet` . Komut dosyası bash kabuğu için biçimlendirilir. PowerShell veya komut Istemi gibi başka bir kabuğu tercih ediyorsanız, değişken atamasını ve erişimcileri buna uygun şekilde ayarlamanız gerekir.

> [!WARNING]
> Bu betik kaynakları siler! Sanal ağı ve içerdiği tüm alt ağları siler. Bu betiği çalıştırmadan önce, içerdiği tüm alt ağlar da dahil olmak üzere sanal ağdaki kaynakların *hiçbirine* artık ihtiyacınız olmadığından emin olun. **Bu kaynaklar** silindikten sonra kurtarılamaz.

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>Sonraki adımlar

Bir Kaynak Yöneticisi şablonu kullanarak yeni bir sanal ağ, alt ağ, ağ profili ve kapsayıcı grubu dağıtmak için bkz. [VNET Ile Azure Kapsayıcı grubu oluşturma](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-network-vnet-create]: /cli/azure/network/vnet#az_network_vnet_create
[az-network-profile-list]: /cli/azure/network/profile#az_network_profile_list
[container-regions]: container-instances-region-availability.md
