---
title: Kapsayıcı grubu için statik IP adresi
description: Bir sanal ağda kapsayıcı grubu oluşturun ve bir Azure Application Gateway kullanarak kapsayıcılı bir Web uygulamasına statik bir ön uç IP adresi sunun
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: de9e06b457a9ea5485fe268bd2b7cf206f0a6c0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790950"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>Bir kapsayıcı grubu için statik IP adresi kullanıma sunma

Bu makalede, bir Azure [Application Gateway](../application-gateway/overview.md)kullanarak bir [kapsayıcı grubu](container-instances-container-groups.md) IÇIN statik, genel IP adresini açığa çıkarmak için bir yol gösterilmektedir. Azure Container Instances ' de çalışan, bir dış bağlantılı Kapsayıcılı uygulama için statik bir giriş noktasına ihtiyacınız olduğunda bu adımları izleyin. 

Bu makalede, bu senaryoya yönelik kaynakları oluşturmak için Azure CLı 'yi kullanırsınız:

* Bir Azure sanal ağı
* Küçük bir Web uygulaması barındıran [Sanal ağda](container-instances-vnet.md) dağıtılan bir kapsayıcı grubu
* Genel ön uç IP adresine sahip bir uygulama ağ geçidi, ağ geçidinde bir Web sitesini barındırmak için bir dinleyici ve arka uç kapsayıcı grubuna bir rota

Uygulama ağ geçidi çalıştığı ve kapsayıcı grubu, ağın Temsilcili alt ağında kararlı bir özel IP adresi kullanıma sunduğundan, kapsayıcı grubuna bu genel IP adresinden erişilebilir.

> [!NOTE]
> Bir uygulama ağ geçidi için, ağ geçidinin sağlandığı ve kullanılabilir olduğu süre miktarına ve işlediği veri miktarına göre Azure ücretleri. Bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/application-gateway/).

## <a name="create-virtual-network"></a>Sanal ağ oluşturma

Tipik bir durumda, zaten bir Azure sanal ağınız olabilir. Bir tane yoksa, aşağıdaki örnek komutlarla gösterildiği gibi bir tane oluşturun. Sanal ağın, uygulama ağ geçidi ve kapsayıcı grubu için ayrı alt ağlara ihtiyacı vardır.

Gerekiyorsa, bir Azure Kaynak grubu oluşturun. Örnek:

```azureci
az group create --name myResourceGroup --location eastus
```

[Az Network VNET Create][az-network-vnet-create] komutuyla bir sanal ağ oluşturun. Bu komut, ağda *Myagsubnet* alt ağını oluşturur.

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

Arka uç kapsayıcı grubu için bir alt ağ oluşturmak için [az Network VNET subnet Create][az-network-vnet-subnet-create] komutunu kullanın. Burada *Myacıubnet* olarak adlandırılmıştır.

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

Statik bir genel IP kaynağı oluşturmak için [az Network public-ip Create][az-network-public-ip-create] komutunu kullanın. Sonraki bir adımda, bu adres uygulama ağ geçidinin ön ucu olarak yapılandırılır.

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>Kapsayıcı grubu oluştur

Önceki adımda yapılandırdığınız sanal ağda bir kapsayıcı grubu oluşturmak için aşağıdaki [az Container Create][az-container-create] öğesini çalıştırın. 

Grup *Myacıubnet* alt ağına dağıtılır ve görüntüyü çeken *AppContainer* adlı tek bir örnek içerir `aci-helloworld` . Bu resimde, belgelerdeki diğer makalelerde gösterildiği gibi, statik bir HTML sayfasına hizmet veren Node.js yazılmış küçük bir Web uygulamasını paketler. 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

Başarıyla dağıtıldığında, kapsayıcı grubuna sanal ağda özel bir IP adresi atanır. Örneğin, aşağıdaki [az Container Show][az-container-show] komutunu ÇALıŞTıRARAK grubun IP adresini alın:

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

Çıktı şuna benzer olacaktır: `10.0.2.4`.

Daha sonraki bir adımda kullanmak için IP adresini bir ortam değişkenine kaydedin:

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

> [!IMPORTANT]
> Kapsayıcı grubu durdurulmuş, başlatılmış veya yeniden başlatılırsa, kapsayıcı grubunun özel IP 'si değişebilir. Bu durumda, uygulama ağ geçidi yapılandırmasını güncelleştirmeniz gerekecektir.

## <a name="create-application-gateway"></a>Uygulama ağ geçidi oluşturma

[Uygulama ağ geçidi hızlı](../application-gateway/quick-create-cli.md)başlangıçdaki adımları izleyerek sanal ağda bir uygulama ağ geçidi oluşturun. Aşağıdaki [az Network Application-Gateway Create][az-network-application-gateway-create] komutu, genel ön uç IP adresine sahip bir ağ geçidi ve arka uç kapsayıcı grubuna bir yol oluşturur. Ağ Geçidi ayarları hakkında daha fazla bilgi için [Application Gateway belgelerine](../application-gateway/index.yml) bakın.

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Azure 'un uygulama ağ geçidini oluşturması 15 dakika kadar sürebilir. 

## <a name="test-public-ip-address"></a>Ortak IP adresini sına
  
Artık uygulama ağ geçidinin arkasındaki kapsayıcı grubunda çalışan Web uygulamasına erişimi test edebilirsiniz.

Ağ geçidinin ön uç genel IP adresini almak için [az Network public-ip show][az-network-public-ip-show] komutunu çalıştırın:

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

Çıkış genel bir IP adresidir, şuna benzer: `52.142.18.133` .

Başarılı bir şekilde yapılandırıldığında çalışan Web uygulamasını görüntülemek için, tarayıcınızda ağ geçidinin genel IP adresine gidin. Başarılı erişim şuna benzerdir:

![Bir Azure kapsayıcı örneğinde çalışan uygulamayı gösteren tarayıcı ekran görüntüsü](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>Sonraki adımlar

* Bir uygulama ağ geçidinin arkasındaki arka uç sunucusu olarak WordPress kapsayıcı örneğiyle bir kapsayıcı grubu oluşturmak için [hızlı başlangıç şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) ' na bakın.
* Ayrıca, bir uygulama ağ geçidini SSL sonlandırma için bir sertifika ile yapılandırabilirsiniz. [Genel bakış](../application-gateway/ssl-overview.md) ve [öğreticiye](../application-gateway/create-ssl-portal.md)bakın.
* Senaryonuza bağlı olarak, Azure Container Instances diğer Azure Yük Dengeleme çözümlerini kullanmayı göz önünde bulundurun. Örneğin, trafiği birden çok kapsayıcı örneğine ve birden çok bölgeye dağıtmak için [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kullanın. Bu [blog gönderisine](https://aaronmsft.com/posts/azure-container-instances/)bakın.

[az-network-vnet-create]:  /cli/azure/network/vnet#az_network_vnet_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az_network-application-gateway-create
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
