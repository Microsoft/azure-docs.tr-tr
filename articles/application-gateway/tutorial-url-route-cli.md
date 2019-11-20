---
title: Web trafiğini URL 'ye göre yönlendirme-Azure CLı
description: Bu makalede, Azure CLı kullanarak Web trafiğini URL 'ye göre ölçeklenebilir sunucuların belirli havuzlarıyla yönlendirmeyi öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/01/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: b6bc0b00579bdef0a358f756b8cf2b6034aca017
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688187"
---
# <a name="route-web-traffic-based-on-the-url-using-the-azure-cli"></a>Azure CLı kullanarak Web trafiğini URL 'ye göre yönlendirme

Web trafiğini yöneten bir BT yöneticisi olarak müşterilerinizin ihtiyaç duydukları bilgilere mümkün olan en hızlı şekilde ulaşmalarına yardımcı olmak istersiniz. Müşteri deneyimini iyileştirmenin bir yolu da farklı türlerdeki web trafiğini farklı sunucu kaynaklarına yönlendirmektir. Bu makalede, Azure CLı kullanarak uygulamanızdan farklı türde trafik için Application Gateway yönlendirmeyi ayarlama ve yapılandırma hakkında yönergeler verilmektedir. Yönlendirme, trafiği URL'ye göre farklı sunucu havuzlarına yönlendirir.

![URL yönlendirme örneği](./media/tutorial-url-route-cli/scenario.png)

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * İhtiyacınız olacak ağ kaynakları için bir kaynak grubu oluşturma
> * Ağ kaynaklarını oluşturma
> * Uygulamanızdan gelen trafik için bir uygulama ağ geçidi oluşturma
> * Farklı trafik türleri için sunucu havuzlarını ve yönlendirme kurallarını belirtme
> * Otomatik ölçeklendirme gerçekleştirmek için her havuzda bir ölçek kümesi oluşturma
> * Farklı trafik türlerinin doğru havuza gittiğini doğrulamak için bir test gerçekleştirme

İsterseniz, [Azure PowerShell](tutorial-url-route-powershell.md) veya [Azure Portal](create-url-route-portal.md)kullanarak bu yordamı tamamlayabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure CLı sürüm 2.0.4 veya üstünü çalıştırmanızı gerektirir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. `az group create` komutunu kullanarak bir kaynak grubu oluşturun.

Aşağıdaki örnek *eastus* konumunda *myResourceGroupAG* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

`az network vnet create` komutunu kullanarak *myVNet* adlı sanal ağı ve *myAGSubnet* adlı alt ağı oluşturun. Ardından `az network vnet subnet create` komutunu kullanarak arka uç sunucularının gerek duyduğu *myBackendSubnet* adlı alt ağı ekleyin. `az network public-ip create` komutunu kullanarak *myAGPublicIPAddress* adlı genel IP adresini oluşturun.

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

## <a name="create-the-app-gateway-with-a-url-map"></a>URL eşleme ile uygulama ağ geçidi oluşturma

`az network application-gateway create` komutunu kullanarak *myAppGateway* adlı bir uygulama ağ geçidi oluşturun. Azure CLI kullanarak bir uygulama ağ geçidi oluşturduğunuzda, kapasite, sku ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. Application Gateway, *Myagsubnet* ve *Myagpublicıpaddress*öğesine atanır.

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


|Özellik  |Açıklama  |
|---------|---------|
|appGatewayBackendPool     |Bir uygulama ağ geçidi en az bir arka uç adres havuzuna sahip olmalıdır.|
|appGatewayBackendHttpSettings     |İletişim için 80 numaralı bağlantı noktasının ve HTTP protokolünün kullanıldığını belirtir.|
|appGatewayHttpListener     |appGatewayBackendPool ile ilişkili varsayılan dinleyicidir|
|appGatewayFrontendIP     |appGatewayHttpListener’a myAGPublicIPAddress’i atar.|
|rule1     |appGatewayHttpListener ile ilişkili varsayılan yönlendirme kuralıdır.|

### <a name="add-image-and-video-backend-pools-and-a-port"></a>Görüntü ve video arka uç havuzlarını ve bağlantı noktasını ekleme

`az network application-gateway address-pool create` komutunu kullanarak *imagesBackendPool* ve *videoBackendPool* adlı arka uç havuzlarını uygulama ağ geçidinize ekleyin. `az network application-gateway frontend-port create` komutunu kullanarak havuzlar için ön uç bağlantı noktasını ekleyin.

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

### <a name="add-a-backend-listener"></a>Arka uç dinleyicisi ekleme

`az network application-gateway http-listener create` komutunu kullanarak, trafiği yönlendirmek için gereken *backendListener* adlı arka uç dinleyicisini ekleyin.


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-a-url-path-map"></a>URL yol eşlemesini ekleme

URL yol eşlemeleri belirli URL'lerin belirli arka uç havuzlarına yönlendirilmesini sağlar. `az network application-gateway url-path-map create` ve `az network application-gateway url-path-map rule create` komutlarını kullanarak *imagePathRule* ve *videoPathRule* adlı URL yol eşlemelerini oluşturun.

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

### <a name="add-a-routing-rule"></a>Yönlendirme kuralı ekle

Yönlendirme kuralı URL eşlemelerini oluşturduğunuz dinleyici ile ilişkilendirir. `az network application-gateway rule create` komutunu kullanarak *rule2* adlı bir kural ekleyin.

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

## <a name="create-virtual-machine-scale-sets"></a>Sanal makine ölçek kümelerini oluşturma

Bu makalede, oluşturduğunuz üç arka uç havuzunu destekleyen üç sanal makine ölçek kümesi oluşturacaksınız. Oluşturduğunuz ölçek kümeleri *myvmss1*, *myvmss2* ve *myvmss3* olarak adlandırılır. Her bir ölçek kümesi NGINX yükleyeceğiniz iki sanal makine örneği içerir.

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

Uygulama ağ geçidinin genel IP adresini almak için az network public-ip show komutunu kullanın. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. ,,, Veya`http://40.121.222.19:8080/video/test.htm`gibi. `http://40.121.222.19` `http://40.121.222.19:8080/images/test.htm`

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Temel URL’yi uygulama ağ geçidinde test etme](./media/tutorial-url-route-cli/application-gateway-nginx.png)

URL 'yi IP adresi için&lt; &lt;IP&gt;adresinizi http://&gt;IP adresi: 8080/images/test.html olarak değiştirin ve aşağıdaki örneğe benzer bir şey görmeniz gerekir:

![Görüntü URL’sini uygulama ağ geçidinde test etme](./media/tutorial-url-route-cli/application-gateway-nginx-images.png)

URL 'yi IP adresi için&lt; &lt;IP&gt;adresinizi http://&gt;IP adresi: 8080/video/test.html olarak değiştirin ve aşağıdaki örneğe benzer bir şey görmeniz gerekir.

![Video URL’sini uygulama ağ geçidinde test etme](./media/tutorial-url-route-cli/application-gateway-nginx-video.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, uygulama ağ geçidini ve tüm ilgili kaynakları silin.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Sonraki adımlar

[URL yolu tabanlı yönlendirme ile bir uygulama ağ geçidi oluşturma](./tutorial-url-redirect-cli.md)
