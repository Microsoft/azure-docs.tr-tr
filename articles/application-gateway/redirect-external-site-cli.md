---
title: CLı kullanarak dış trafik yönlendirmesi-Azure Application Gateway
description: Azure CLı kullanarak iç Web trafiğini uygun havuza yönlendiren bir uygulama ağ geçidi oluşturmayı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: fc955b4959bb20628463f7699a0b66ec2b89a393
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74011602"
---
# <a name="create-an-application-gateway-with-external-redirection-using-the-azure-cli"></a>Azure CLı kullanarak dış yeniden yönlendirmeye sahip bir uygulama ağ geçidi oluşturma

[Uygulama ağ geçidi](overview.md)oluştururken [Web trafiği yeniden yönlendirmeyi](multiple-site-overview.md) yapılandırmak için Azure CLI 'yi kullanabilirsiniz. Bu öğreticide, uygulama ağ geçidine gelen Web trafiğini bir dış siteye yönlendiren bir dinleyici ve kural yapılandırırsınız.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Ağı ayarlama
> * Dinleyici ve yeniden yönlendirme kuralı oluşturma
> * Uygulama ağ geçidi oluşturma

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

*az network vnet create* komutunu kullanarak *myVNet* adlı sanal ağı ve [myAGSubnet](/cli/azure/network/vnet) adlı alt ağı oluşturun. *az network public-ip create* komutunu kullanarak [myAGPublicIPAddress](/cli/azure/network/public-ip) adlı genel IP adresini oluşturun. Bu kaynaklar, uygulama ağ geçidi ve ilişkili kaynakları ile ağ bağlantısı sağlamak için kullanılır.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Uygulama ağ geçidi oluşturma

[myAppGateway](/cli/azure/network/application-gateway) adlı uygulama ağ geçidini oluşturmak için *az network application-gateway create* komutunu kullanabilirsiniz. Azure CLI kullanarak bir uygulama ağ geçidi oluşturduğunuzda, kapasite, sku ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. Application Gateway, daha önce oluşturduğunuz *Myagsubnet* ve *Mypublicıpaddress* öğesine atanır. 

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
  --frontend-port 8080 \
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

### <a name="add-the-redirection-configuration"></a>Yeniden yönlendirme yapılandırmasını ekleyin

[Az Network Application-Gateway Redirect-config Create](/cli/azure/network/application-gateway/redirect-config)komutunu kullanarak *www\.* *\.* consoto.org 'ten uygulama ağ geçidine giden trafiği gönderen yeniden yönlendirme yapılandırmasını ekleyin.

```azurecli-interactive
az network application-gateway redirect-config create \
  --name myredirect \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Temporary \
  --target-url "https://bing.com"
```

### <a name="add-a-listener-and-routing-rule"></a>Dinleyici ve yönlendirme kuralı ekleme

Uygulama ağ geçidinin trafiği uygun şekilde yönlendirebilmesi için bir dinleyici gerekir. Az Network Application [-Gateway ön uç bağlantı noktası oluştur](/cli/azure/network/application-gateway)ile oluşturulan ön uç bağlantı noktasıyla [az Network Application-Gateway http-Listener Create](/cli/azure/network/application-gateway) kullanarak dinleyiciyi oluşturun. Dinleyicinin gelen trafiğin nereye gönderileceğini bilmesini sağlamak için bir kural gerekir. [Az Network Application-Gateway Rule Create](/cli/azure/network/application-gateway)kullanılarak *redirectrule* adlı bir temel kural oluşturun.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name redirectPort
az network application-gateway http-listener create \
  --name redirectListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port redirectPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectListener \
  --rule-type Basic \
  --redirect-config myredirect
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidinin genel IP adresini almak için [az network public-ip show](/cli/azure/network/public-ip) komutunu kullanın. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

Tarayıcınızda *Bing.com* göründüğünü görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> * Ağı ayarlama
> * Dinleyici ve yeniden yönlendirme kuralı oluşturma
> * Uygulama ağ geçidi oluşturma
