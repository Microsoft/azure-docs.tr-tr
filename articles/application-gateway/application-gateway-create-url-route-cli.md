---
title: CLı kullanan URL yolu tabanlı yönlendirme kuralları-Azure Application Gateway
description: Azure CLı kullanarak bir uygulama ağ geçidi ve sanal makine ölçek kümesi için URL yol tabanlı yönlendirme kuralları oluşturma hakkında bilgi edinin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 7198e68530a51e6c2002b3beb08f14615a5c70fb
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012337"
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>Azure CLı kullanarak URL yolu tabanlı yönlendirme kuralları ile uygulama ağ geçidi oluşturma

Azure CLI'yi kullanarak, bir [uygulama ağ geçidi](application-gateway-url-route-overview.md) oluştururken [URL yolu tabanlı yönlendirme kuralları](application-gateway-introduction.md) yapılandırabilirsiniz. Bu öğreticide, bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)kullanarak arka uç havuzları oluşturacaksınız. Daha sonra, Web trafiğinin havuzlardaki uygun sunuculara ulaştığınızdan emin olmak için yönlendirme kuralları oluşturursunuz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Ağı ayarlama
> * URL eşlemesi ile uygulama ağ geçidi oluşturma
> * Arka uç havuzları ile sanal makine ölçek kümeleri oluşturma

![URL yönlendirme örneği](./media/application-gateway-create-url-route-cli/scenario.png)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı seçerseniz bu hızlı başlangıç için Azure CLI 2.0.4 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun.

Aşağıdaki örnek *eastus* konumunda *myResourceGroupAG* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma 

*az network vnet create* komutunu kullanarak *myVNet* adlı sanal ağı ve [myAGSubnet](/cli/azure/network/vnet) adlı alt ağı oluşturun. Daha sonra *az network vnet subnet create* kullanan arka uç sunucularının gerek duyduğu [myBackendSubnet](/cli/azure/network/vnet/subnet) adlı alt ağı ekleyebilirsiniz. *az network public-ip create* komutunu kullanarak [myAGPublicIPAddress](/cli/azure/network/public-ip) adlı genel IP adresini oluşturun.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>URL eşlemesi ile uygulama ağ geçidi oluşturma

[myAppGateway](/cli/azure/network/application-gateway) adlı uygulama ağ geçidini oluşturmak için *az network application-gateway create* komutunu kullanabilirsiniz. Azure CLI kullanarak bir uygulama ağ geçidi oluşturduğunuzda, kapasite, sku ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. Uygulama ağ geçidi, *myAGSubnet*’e ve daha önce oluşturduğunuz *myAGPublicIPAddress*’e atanır. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Uygulama ağ geçidinin oluşturulması birkaç dakika sürebilir. Uygulama ağ geçidi oluşturulduktan sonra şu yeni özellikleri görürsünüz:

- *appGatewayBackendPool*: Bir uygulama ağ geçidi en az bir arka uç adres havuzuna sahip olmalıdır.
- *appGatewayBackendHttpSettings*: İletişim için 80 numaralı bağlantı noktasının ve HTTP protokolünün kullanıldığını belirtir.
- *appGatewayHttpListener*: *appGatewayBackendPool* ile ilişkili varsayılan dinleyicidir.
- *appGatewayFrontendIP*: *appGatewayHttpListener*’a *myAGPublicIPAddress*’i atar.
- *kural 1*: *appGatewayHttpListener* ile ilişkili varsayılan yönlendirme kuralıdır.


### <a name="add-image-and-video-backend-pools-and-port"></a>Görüntü ve video arka uç havuzlarını ve bağlantı noktasını ekleme

[Az Network Application-Gateway Address-Pool Create](/cli/azure/network/application-gateway/address-pool#az-network-application-gateway-address-pool-create)kullanarak, *ımaizbackendpool* ve *videobackendpool* adlı arka uç havuzlarını Application Gateway 'e ekleyebilirsiniz. [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port#az-network-application-gateway-frontend-port-create) kullanarak havuzlara ön uç bağlantı noktasını ekleyebilirsiniz. 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>Arka uç dinleyicisi ekleme

*az network application-gateway http-listener create* kullanarak trafiği yönlendirmek için gereken [backendListener](/cli/azure/network/application-gateway) adlı arka uç dinleyicisini ekleyin.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>URL yol eşlemesini ekleme

URL yol eşlemeleri belirli URL'lerin belirli arka uç havuzlarına yönlendirilmesini sağlar. *az network application-gateway url-path-map create* ve *az network application-gateway url-path-map rule create* kullanarak [imagePathRule](/cli/azure/network/application-gateway) ve [videoPathRule](/cli/azure/network/application-gateway) adlı URL yol eşlemelerini oluşturabilirsiniz.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>Yönlendirme kuralı ekleme

Yönlendirme kuralı URL eşlemelerini oluşturduğunuz dinleyici ile ilişkilendirir. [Az Network Application-Gateway Rule Create](/cli/azure/network/application-gateway/rule#az-network-application-gateway-rule-create)kullanılarak *bağlanma2* adlı kuralı ekleyebilirsiniz.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte, oluşturduğunuz üç arka uç havuzunu destekleyen üç sanal makine ölçek kümesi oluşturacaksınız. Oluşturduğunuz ölçek kümeleri *myvmss1*, *myvmss2* ve *myvmss3* olarak adlandırılır. Her bir ölçek kümesi NGINX yükleyeceğiniz iki sanal makine örneği içerir.

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>NGINX yükleme

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidinin genel IP adresini almak için [az network public-ip show](/cli/azure/network/public-ip) komutunu kullanın. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Örneğin, `http://40.121.222.19`, `http://40.121.222.19:8080/images/test.htm`veya `http://40.121.222.19:8080/video/test.htm`.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Temel URL’yi uygulama ağ geçidinde test etme](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

URL 'yi temel URL 'nin sonuna `http://<ip-address>:8080/video/test.html` değiştirin ve aşağıdaki örneğe benzer bir şey görmeniz gerekir:

![Görüntü URL’sini uygulama ağ geçidinde test etme](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

URL 'YI `http://<ip-address>:8080/video/test.html` değiştirin ve aşağıdaki örneğe benzer bir şey görmeniz gerekir.

![Video URL’sini uygulama ağ geçidinde test etme](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Ağı ayarlama
> * URL eşlemesi ile uygulama ağ geçidi oluşturma
> * Arka uç havuzları ile sanal makine ölçek kümeleri oluşturma

Uygulama ağ geçitleri ve bunlarla ilişkili kaynaklar hakkında daha fazla bilgi edinmek için nasıl yapılır makalelerine devam edin.
