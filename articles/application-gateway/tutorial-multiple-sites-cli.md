---
title: CLı kullanarak birden çok Web sitesi barındırma
titleSuffix: Azure Application Gateway
description: Azure CLI kullanarak, birden çok web sitesi barındıran bir uygulama ağ geçidi oluşturmayı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cb924ab1f8947fefc83ed35a409628a576fad4b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772692"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>Azure CLı kullanarak birden çok Web sitesini barındıran bir uygulama ağ geçidi oluşturma

Bir [uygulama ağ geçidi](overview.md) oluştururken Azure CLI’sini [birden çok web sitesi barındırmayı yapılandırmak](multiple-site-overview.md) için kullanabilirsiniz. Bu makalede, sanal makine ölçek kümelerini kullanarak arka uç adres havuzları tanımlarsınız. Ardından sahip olduğunuz dinleyicileri ve kuralları, web trafiğinin havuzlardaki uygun sunuculara ulaşması için yapılandırırsınız. Bu makalede, birden çok etki alanına sahip olduğunuz ve *www \. contoso.com* ve *www \. fabrikam.com* örneklerini kullanan varsayılmaktadır.

Bu makalede şunları öğreneceksiniz:

* Ağı ayarlama
* Uygulama ağ geçidi oluşturma
* Arka uç dinleyicileri oluşturma
* Yönlendirme kuralları oluşturma
* Arka uç havuzları ile sanal makine ölçek kümeleri oluşturma
* Etki alanınızda bir CNAME kaydı oluşturma

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="Çok siteli Application Gateway":::

İsterseniz, [Azure PowerShell](tutorial-multiple-sites-powershell.md)kullanarak bu yordamı tamamlayabilirsiniz.

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

[az network vnet create](/cli/azure/network/vnet) komutunu kullanarak adlı sanal ağı ve *myAGSubnet* adlı alt ağı oluşturun. Daha sonra [az network vnet subnet create](/cli/azure/network/vnet/subnet) kullanan arka uç sunucularının gerek duyduğu alt ağı ekleyebilirsiniz. [az network public-ip create](/cli/azure/network/public-ip) komutunu kullanarak *myAGPublicIPAddress* adlı genel IP adresini oluşturun.

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

Uygulama ağ geçidini oluşturmak için [az network application-gateway create](/cli/azure/network/application-gateway#az_network_application_gateway_create) komutunu kullanabilirsiniz. Azure CLI kullanarak bir uygulama ağ geçidi oluşturduğunuzda, kapasite, sku ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. Uygulama ağ geçidi, *myAGSubnet*’e ve daha önce oluşturduğunuz *myAGPublicIPAddress*’e atanır. 

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

### <a name="add-the-backend-pools"></a>Arka uç havuzlarını ekleme

[Az Network Application-Gateway Address-Pool Create](/cli/azure/network/application-gateway/address-pool#az_network_application_gateway_address-pool_create) kullanılarak arka uç sunucularını içermesi için gereken arka uç havuzlarını ekleyin
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>Dinleyiciler ekleme

[Az Network Application-Gateway http-Listener Create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create)kullanılarak trafiği yönlendirmek için gereken dinleyicileri ekleyin.

>[!NOTE]
> Application Gateway veya WAF v2 SKU 'SU ile dinleyici başına 5 adede kadar ana bilgisayar adı yapılandırabilir ve ana bilgisayar adında joker karakterler kullanabilirsiniz. Daha fazla bilgi için bkz. [dinleyicide joker ana bilgisayar adları](multiple-site-overview.md#wildcard-host-names-in-listener-preview) .
>Azure CLı kullanarak bir dinleyicide birden çok konak adı ve joker karakter kullanmak için yerine kullanmanız gerekir `--host-names` `--host-name` . Ana bilgisayar adları ile en fazla beş ana bilgisayar adını boşlukla ayrılmış değerler olarak belirtebilirsiniz. Örneğin, `--host-names "*.contoso.com *.fabrikam.com"`

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>Yönlendirme kuralları ekleme

Kurallar listelendikleri sırada işlenir. Trafik, özellikten bağımsız olarak eşleşen ilk kural kullanılarak yönlendirilir. Örneğin, aynı bağlantı noktasında temel bir dinleyici kullanan bir kuralınız ve çok siteli dinleyici kullanan bir kuralınız varsa çok siteli kuralın beklendiği gibi çalışması için çok siteli dinleyicinin kuralı temel dinleyici kuralından önce listelenmelidir. 

Bu örnekte, iki yeni kural oluşturur ve uygulama ağ geçidini dağıtırken oluşturulan varsayılan kuralı silebilirsiniz. Kuralı [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create) komutunu kullanarak ekleyebilirsiniz.

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```

## <a name="create-virtual-machine-scale-sets"></a>Sanal makine ölçek kümesi oluşturma

Bu örnekte uygulama ağ geçidinde üç arka uç havuzunu destekleyen üç sanal makine ölçek kümesi oluşturursunuz. Oluşturduğunuz ölçek kümeleri *myvmss1*, *myvmss2* ve *myvmss3* olarak adlandırılır. Her bir ölçek kümesi IIS yükleyeceğiniz iki sanal makine örneği içerir.

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
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
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>Etki alanınızda bir CNAME kaydı oluşturma

Uygulama ağ geçidi genel IP adresiyle oluşturulduktan sonra DNS adresini alabilir ve etki alanınızda bir CNAME kaydı oluşturmak için kullanabilirsiniz. Uygulama ağ geçidinin DNS adresini almak için [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) komutunu kullanabilirsiniz. DNSSetting için *fqdn* değerini kopyalayın ve bu değeri oluşturduğunuz CNAME kaydının değeri olarak kullanın. 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

Uygulama ağ geçidi yeniden başlatıldığında VIP değiştirebileceğinden, A kayıtlarının kullanılması önerilmez.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Tarayıcınızın adres çubuğuna, etki alanı adınızı girin. Örneğin, http: \/ /www.contoso.com.

![Uygulama ağ geçidinde contoso test etme](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

Adresi diğer etki alanınızla değiştirin, aşağıdaki örneğe benzer bir şey görmeniz gerekir:

![Uygulama ağ geçidinde fabrikam sitesini test etme](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında kaynak grubunu, uygulama ağ geçidini ve tüm ilgili kaynakları silin.

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Sonraki adımlar

[URL yolu tabanlı yönlendirme kuralları ile bir uygulama ağ geçidi oluşturma](./tutorial-url-route-cli.md)
