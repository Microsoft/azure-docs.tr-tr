---
title: CLı kullanarak HTTP 'den HTTPS 'ye yeniden yönlendirme
titleSuffix: Azure Application Gateway
description: HTTP-HTTPS yeniden yönlendirmesi oluşturmayı ve Azure CLı kullanarak TLS sonlandırma için bir sertifika eklemeyi öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 09/24/2020
ms.author: victorh
ms.openlocfilehash: e66eca305433a89496f72aac667512efd418a369
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784776"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Azure CLı kullanarak HTTP ile HTTPS yönlendirmesi arasında bir uygulama ağ geçidi oluşturma

Azure CLı 'yi, TLS/SSL sonlandırma sertifikasıyla bir [uygulama ağ geçidi](overview.md) oluşturmak için kullanabilirsiniz. Yönlendirme kuralı, HTTP trafiğini uygulama ağ geçidinizdeki HTTPS bağlantı noktasına yönlendirmek için kullanılır. Bu örnekte, iki sanal makine örneği içeren uygulama ağ geçidinin arka uç havuzu için bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/overview.md) de oluşturacaksınız.

Bu makalede şunları öğreneceksiniz:

* Otomatik olarak imzalanan sertifika oluşturma
* Ağ ayarlama
* Sertifikalı bir uygulama ağ geçidi oluşturma
* Dinleyici ve yeniden yönlendirme kuralı ekleme
* Varsayılan arka uç havuzuyla bir sanal makine ölçek kümesi oluşturma

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Üretim kullanımı için, güvenilir bir sağlayıcı tarafından imzalanmış geçerli bir sertifikayı içeri aktarmanız gerekir. Bu öğretici için openssl komutunu kullanarak otomatik olarak imzalanan bir sertifika ve pfx dosyası oluşturursunuz.

```console
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

Sertifikanız için anlamlı olan değerler girin. Varsayılan değerleri kabul edebilirsiniz.

```console
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

Sertifika için parola belirtin. Bu örnekte *Azure123456!* kullanılmaktadır.

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
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

*myAppGateway* adlı uygulama ağ geçidini oluşturmak için [az network application-gateway create](/cli/azure/network/application-gateway#az_network_application_gateway_create) komutunu kullanabilirsiniz. Azure CLI kullanarak bir uygulama ağ geçidi oluşturduğunuzda, kapasite, sku ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. 

Uygulama ağ geçidi, *myAGSubnet*’e ve daha önce oluşturduğunuz *myAGPublicIPAddress*’e atanır. Bu örnekte olluşturduğunuz sertifika ve uygulama ağ geçidini oluşturduğunuz zamanki parolasını ilişkilendiirirsiniz. 

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
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 Uygulama ağ geçidinin oluşturulması birkaç dakika sürebilir. Uygulama ağ geçidi oluşturulduktan sonra şu yeni özellikleri görürsünüz:

- *appGatewayBackendPool*: Bir uygulama ağ geçidi en az bir arka uç adres havuzuna sahip olmalıdır.
- *appGatewayBackendHttpSettings*: İletişim için 80 numaralı bağlantı noktasının ve HTTP protokolünün kullanıldığını belirtir.
- *appGatewayHttpListener*: *appGatewayBackendPool* ile ilişkili varsayılan dinleyicidir.
- *appGatewayFrontendIP*: *appGatewayHttpListener*’a *myAGPublicIPAddress*’i atar.
- *kural 1*: *appGatewayHttpListener* ile ilişkili varsayılan yönlendirme kuralıdır.

## <a name="add-a-listener-and-redirection-rule"></a>Dinleyici ve yeniden yönlendirme kuralı ekleme

### <a name="add-the-http-port"></a>HTTP bağlantı noktasını ekleme

HTTP bağlantı noktasını uygulama ağ geçidine eklemek için [az Network Application-Gateway ön uç bağlantı noktası oluştur](/cli/azure/network/application-gateway/frontend-port#az_network-application_gateway_frontend_port_create) ' i kullanabilirsiniz.

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>HTTP dinleyicisini ekleyin

Application Gateway 'e *MyListener* adlı dinleyiciyi eklemek için [az Network Application-Gateway http-Listener Create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) ' i kullanabilirsiniz.

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>Yeniden yönlendirme yapılandırmasını ekleyin

[Az Network Application-Gateway Redirect-config Create](/cli/azure/network/application-gateway/redirect-config#az_network_application_gateway_redirect_config_create)komutunu kullanarak http-https yeniden yönlendirme yapılandırmasını uygulama ağ geçidine ekleyin.

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>Yönlendirme kuralını ekleme

*Bağlanma2* adlı yönlendirme kuralını, [az Network Application-Gateway Rule Create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create)kullanılarak uygulama ağ geçidine yeniden yönlendirme yapılandırmasıyla ekleyin.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte, uygulama ağ geçidinde arka uç havuzu için sunucular sağlayan *myvmss* adlı bir sanal makine ölçek kümesi oluşturacaksınız. Ölçek kümesindeki sanal makineler *myBackendSubnet* ve *appGatewayBackendPool* ile ilişkilidir. Ölçek kümesini oluşturmak için [az vmss create](/cli/azure/vmss#az_vmss_create) komutunu kullanabilirsiniz.

```azurecli-interactive
az vmss create \
  --name myvmss \
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
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>NGINX yükleme

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Uygulama ağ geçidinin genel IP adresini almak için [az network public-ip show](/cli/azure/network/public-ip) komutunu kullanın. Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Güvenli uyarı](./media/redirect-http-to-https-cli/application-gateway-secure.png)

Otomatik olarak imzalanan bir sertifika kullandıysanız güvenlik uyarısını kabul etmek için **Ayrıntılar** ' ı seçin ve ardından **Web sayfasına gidin**. Güvenli NGINX siteniz, sonra aşağıdaki örnekte olduğu gibi görüntülenir:

![Temel URL’yi uygulama ağ geçidinde test etme](./media/redirect-http-to-https-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLı kullanarak iç yeniden yönlendirmeye sahip bir uygulama ağ geçidi oluşturma](redirect-internal-site-cli.md)