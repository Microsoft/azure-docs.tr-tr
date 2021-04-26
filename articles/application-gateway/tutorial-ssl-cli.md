---
title: CLı kullanarak TLS sonlandırma-Azure Application Gateway
description: Azure CLı kullanarak bir uygulama ağ geçidi oluşturma ve TLS sonlandırma için sertifika ekleme hakkında bilgi edinin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a8779c2829ecd2e8975ff56011bf5f73631f2de9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772694"
---
# <a name="create-an-application-gateway-with-tls-termination-using-the-azure-cli"></a>Azure CLI kullanarak TSL sonlandırma ile bir uygulama ağ geçidi oluşturma

[TLS sonlandırmasına](ssl-overview.md)yönelik bir sertifika ile [uygulama ağ geçidi](overview.md) oluşturmak için Azure CLI 'yı kullanabilirsiniz. Arka uç sunucuları için bir [sanal makine ölçek kümesi](../virtual-machine-scale-sets/overview.md)kullanabilirsiniz. Bu örnekte örnek kümesi, uygulama ağ geçidinin varsayılan arka uç havuzuna eklenen iki sanal makine örneğini içerir.

Bu makalede şunları öğreneceksiniz:

* Otomatik olarak imzalanan sertifika oluşturma
* Ağ ayarlama
* Sertifikalı bir uygulama ağ geçidi oluşturma
* Varsayılan arka uç havuzuyla bir sanal makine ölçek kümesi oluşturma

İsterseniz, [Azure PowerShell](tutorial-ssl-powershell.md)kullanarak bu yordamı tamamlayabilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Bu öğretici, Azure CLı 'nin sürüm 2.0.4 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika oluşturma

Üretim sırasında kullanım için, güvenilen bir sağlayıcı tarafından imzalanan geçerli bir sertifikayı içeri aktarmalısınız. Bu makalede, OpenSSL komutunu kullanarak kendinden imzalı bir sertifika ve pfx dosyası oluşturacaksınız.

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

Uygulama ağ geçidini oluşturmak için [az network application-gateway create](/cli/azure/network/application-gateway) komutunu kullanabilirsiniz. Azure CLI kullanarak bir uygulama ağ geçidi oluşturduğunuzda, kapasite, sku ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. 

Uygulama ağ geçidi, *myAGSubnet*’e ve daha önce oluşturduğunuz *myAGPublicIPAddress*’e atanır. Bu örnekte olluşturduğunuz sertifika ve uygulama ağ geçidini oluşturduğunuz zamanki parolasını ilişkilendiirirsiniz. 

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

## <a name="create-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte uygulama ağ geçidinde varsayılan arka uç havuzu için sunucu sağlayan bir sanal makine ölçek kümesi oluşturursunuz. Ölçek kümesindeki sanal makineler *myBackendSubnet* ve *appGatewayBackendPool* ile ilişkilidir. Ölçek kümesini oluşturmak için [az vmss create](/cli/azure/vmss#az_vmss_create) komutunu kullanabilirsiniz.

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

Uygulama ağ geçidinin genel IP adresini almak için [az network public-ip show](/cli/azure/network/public-ip) komutunu kullanın.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın. Bu örnekte URL: **https://52.170.203.149** .

![Güvenli uyarı](./media/tutorial-ssl-cli/application-gateway-secure.png)

Otomatik olarak imzalanan bir sertifika kullandıysanız güvenlik uyarısını kabul etmek için **Ayrıntılar** ' ı seçin ve ardından **Web sayfasına gidin**. Güvenli NGINX siteniz, sonra aşağıdaki örnekte olduğu gibi görüntülenir:

![Temel URL’yi uygulama ağ geçidinde test etme](./media/tutorial-ssl-cli/application-gateway-nginx.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, uygulama ağ geçidini ve tüm ilgili kaynakları silin.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Sonraki adımlar

[Birden çok web sitesi barındıran bir uygulama ağ geçidi oluşturma](./tutorial-multiple-sites-cli.md)