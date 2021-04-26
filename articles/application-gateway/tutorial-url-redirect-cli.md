---
title: 'Öğretici: CLı kullanılarak URL yolu tabanlı yönlendirme'
titleSuffix: Azure Application Gateway
description: Bu öğreticide, Azure CLı kullanarak URL yol tabanlı yeniden yönlendirilen trafik ile uygulama ağ geçidi oluşturmayı öğreneceksiniz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/05/2021
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 042cb42d16fdc5394940b62709b5d25187775014
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772658"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>Öğretici: Azure CLI kullanarak URL yolu tabanlı yeniden yönlendirme ile bir uygulama ağ geçidi oluşturma

Azure CLI'yi kullanarak, bir [uygulama ağ geçidi](./overview.md) oluştururken [URL yolu tabanlı yönlendirme kuralları](tutorial-url-route-cli.md) yapılandırabilirsiniz. Bu öğreticide, [sanal makine ölçek kümelerini](../virtual-machine-scale-sets/overview.md)kullanarak arka uç havuzları oluşturacaksınız. Daha sonra, web trafiğinin uygun arka uç havuzuna yeniden yönlendirildiğinden emin olmak için URL yönlendirme kuralları oluşturacaksınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Ağı ayarlama
> * Uygulama ağ geçidi oluşturma
> * Dinleyiciler ve yönlendirme kuralları ekleme
> * Arka uç havuzları için sanal makine ölçek kümeleri oluşturma

Aşağıdaki örnekte, 8080 ve 8081 numaralı bağlantı noktalarından gelen ve aynı arka uç havuzlarına yönlendirilmekte olan site trafiği gösterilir:

![URL yönlendirme örneği](./media/tutorial-url-redirect-cli/scenario.png)

Tercih ederseniz, bu öğreticiyi [Azure PowerShell](tutorial-url-redirect-powershell.md) kullanarak tamamlayabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun.

Aşağıdaki örnek *eastus* konumunda *myResourceGroupAG* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma 

[az network vnet create](/cli/azure/network/vnet) komutunu kullanarak *myVNet* adlı sanal ağı ve *myAGSubnet* adlı alt ağı oluşturun. Daha sonra [az network vnet subnet create](/cli/azure/network/vnet/subnet) kullanan arka uç sunucularının gerek duyduğu *myBackendSubnet* adlı alt ağı ekleyebilirsiniz. [az network public-ip create](/cli/azure/network/public-ip) komutunu kullanarak *myAGPublicIPAddress* adlı genel IP adresini oluşturun.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

MyAppGateway adlı uygulama ağ geçidini oluşturmak için [az Network Application-Gateway Create](/cli/azure/network/application-gateway) kullanın. Azure CLI kullanarak bir uygulama ağ geçidi oluşturduğunuzda, kapasite, sku ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. Application Gateway, daha önce oluşturduğunuz *Myagsubnet* ve *Mypublicıpaddress* öğesine atanır.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
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


### <a name="add-backend-pools-and-ports"></a>Arka uç havuzları ve bağlantı noktaları ekleme

[az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool) kullanarak *imagesBackendPool* ve *videoBackendPool* adlı arka uç adres havuzlarını uygulama ağ geçidinize ekleyebilirsiniz. [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port) kullanarak havuzlara ön uç bağlantı noktalarını eklersiniz. 

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
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>Dinleyiciler ve kurallar ekleme

### <a name="add-listeners"></a>Dinleyiciler ekleme

[az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener) kullanarak trafiği yönlendirmek için gereken *backendListener* ve *redirectedListener* adlı arka uç dinleyicilerini ekleyin.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>Varsayılan URL yolu eşlemesi ekleme

URL yolu eşlemeleri belirli URL 'Lerin belirli arka uç havuzlarına yönlendirildiğinden emin olmanızı sağlar. [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) ve [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule) kullanarak *imagePathRule* ve *videoPathRule* adlı URL yol eşlemelerini oluşturabilirsiniz.

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>Yeniden yönlendirme yapılandırması ekleme

[az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) kullanarak dinleyici için yeniden yönlendirmeyi yapılandırabilirsiniz.

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>Yeniden yönlendirme URL yolu eşlemesi ekleme

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>Yönlendirme kuralları ekleme

Yönlendirme kuralları, URL yolu eşlemelerini oluşturduğunuz dinleyicilerle ilişkilendirir. [az network application-gateway rule create](/cli/azure/network/application-gateway/rule) kullanarak *defaultRule* ve *redirectedRule* adlı kuralları ekleyebilirsiniz.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte, oluşturduğunuz üç arka uç havuzunu destekleyen üç sanal makine ölçek kümesi oluşturacaksınız. Oluşturduğunuz ölçek kümeleri *myvmss1*, *myvmss2* ve *myvmss3* olarak adlandırılır. Her bir ölçek kümesi NGINX yükleyeceğiniz iki sanal makine örneği içerir.

\<azure-user>Ve yerine \<password> istediğiniz kullanıcı adını ve parolayı değiştirin.

```azurecli
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
    --admin-username <azure-user> \
    --admin-password <password> \
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

Uygulama ağ geçidinin genel IP adresini almak için [az Network public-IP Show](/cli/azure/network/public-ip#az_network_public_ip_show)kullanın. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Örneğin,, `http://40.121.222.19` , `http://40.121.222.19:8080/images/test.htm` `http://40.121.222.19:8080/video/test.htm` veya `http://40.121.222.19:8081/images/test.htm` .

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Temel URL’yi uygulama ağ geçidinde test etme](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

URL 'YI IP adresi &lt; için IP adresinizi http://IP adresi &gt; : 8080/ımages/test.html olacak şekilde değiştirin &lt; &gt; ve aşağıdaki örneğe benzer bir şey görmeniz gerekir:

![Görüntü URL’sini uygulama ağ geçidinde test etme](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

URL 'YI IP adresi &lt; için IP adresinizi http://IP adresi &gt; : 8080/video/test.html olacak şekilde değiştirin &lt; &gt; ve aşağıdaki örneğe benzer bir şey görmeniz gerekir:

![Video URL’sini uygulama ağ geçidinde test etme](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

Şimdi, URL 'YI http:// &lt; IP-adresi &gt; : 8081/ımages/test.htm olarak değiştirin, IP adresinizi IP adresi için değiştirin &lt; &gt; ve http:// &lt; IP adresi &gt; : 8080/Images konumundaki görüntüler arka uç havuzuna geri yönlendirilen trafiği görmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, uygulama ağ geçidini ve tüm ilgili kaynakları silin.

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulama ağ geçidi ile neler yapabileceğiniz hakkında daha fazla bilgi edinin](./overview.md)