---
title: 'Hızlı başlangıç: CLı kullanarak doğrudan web trafiği'
titleSuffix: Azure Application Gateway
description: Web trafiğini bir arka uç havuzundaki sanal makinelere yönlendiren bir Azure Application Gateway oluşturmak için Azure CLı 'yi nasıl kullanacağınızı öğrenin.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: de1f30d949509ab48999d0080ccc4df74321ce01
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075195"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Hızlı Başlangıç: Azure Application Gateway ile web trafiğini yönlendirme - Azure CLI

Bu hızlı başlangıçta, bir uygulama ağ geçidi oluşturmak için Azure CLı 'yi kullanma gösterilmektedir.  Uygulama ağ geçidini oluşturduktan sonra, doğru çalıştığından emin olmak için test edersiniz. Azure Application Gateway ile, bağlantı noktalarına dinleyici atayarak, kurallar oluşturarak ve bir arka uç havuzuna kaynak ekleyerek uygulama Web trafiğinizi belirli kaynaklara yönlendirebilirsiniz. Bu makalede, genel ön uç IP 'si olan basit bir kurulum ve uygulama ağ geçidinde tek bir siteyi barındırmak için temel bir dinleyici, arka uç havuzu için iki sanal makine ve temel istek yönlendirme kuralı kullanılır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Önkoşullar

### <a name="azure-cli"></a>Azure CLI

CLı 'yi yerel olarak yükleyip kullanmayı tercih ederseniz Azure CLı sürüm 2.0.4 veya üstünü çalıştırın. Sürümü bulmak için **az--Version**çalıştırın. Yükleme veya yükseltme hakkında daha fazla bilgi için bkz. [Azure CLI 'Yı yükleme]( /cli/azure/install-azure-cli).

### <a name="resource-group"></a>Kaynak grubu

Azure 'da, ilgili kaynakları bir kaynak grubuna ayırabilirsiniz. [Az Group Create](/cli/azure/group#az-group-create)kullanarak bir kaynak grubu oluşturun. 

Aşağıdaki örnek *eastus* konumunda *myResourceGroupAG* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

### <a name="required-network-resources"></a>Gerekli ağ kaynakları 

Azure 'un, oluşturduğunuz kaynaklar arasında iletişim kurması için bir sanal ağa ihtiyacı vardır.  Application Gateway alt ağı yalnızca uygulama ağ geçitleri içerebilir. Başka hiçbir kaynağa izin verilmez.  Application Gateway için yeni bir alt ağ oluşturabilir veya var olan bir alt ağı kullanabilirsiniz. Bu örnekte, bu örnekte iki alt ağ oluşturursunuz: uygulama ağ geçidi için bir diğeri, arka uç sunucuları için bir diğeri. Application Gateway ön uç IP 'sini kullanım çalışmanıza göre genel veya özel olacak şekilde yapılandırabilirsiniz. Bu örnekte, genel ön uç IP 'si seçersiniz.

Sanal ağ ve alt ağ oluşturmak için [az Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create)kullanın. Genel IP adresini oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip) ' i çalıştırın.

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

### <a name="backend-servers"></a>Arka uç sunucuları

Arka uçta NIC 'Ler, sanal makine ölçek kümeleri, genel IP 'Ler, iç IP 'Ler, tam nitelikli etki alanı adları (FQDN) ve Azure App Service gibi çok kiracılı arka uçlar bulunabilir. Bu örnekte, uygulama ağ geçidi için arka uç sunucusu olarak kullanmak üzere iki sanal makine oluşturursunuz. Ayrıca, uygulama ağ geçidini test etmek için sanal makinelere IIS yüklersiniz.

#### <a name="create-two-virtual-machines"></a>İki sanal makine oluşturma

Uygulama ağ geçidinin başarıyla oluşturulduğunu doğrulamak için sanal makinelere [NGINX web sunucusunu](https://docs.nginx.com/nginx/) yükler. Bir Cloud-init yapılandırma dosyasını kullanarak NGıNX 'i yükleyebilir ve bir Linux sanal makinesinde bir "Merhaba Dünya" Node. js uygulaması çalıştırabilirsiniz. Cloud-init hakkında daha fazla bilgi için bkz. [Azure 'da sanal makineler Için Cloud-init desteği](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).

Azure Cloud Shell, aşağıdaki yapılandırmayı kopyalayın ve *kabuğunuzda Cloud-init. txt*adlı bir dosyaya yapıştırın. Dosyayı oluşturmak için *Editor kabuğunuzda Cloud-init. txt* yazın.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

[az network nic oluşturma](/cli/azure/network/nic#az-network-nic-create) ile ağ arabirimlerini oluşturun. Sanal makineleri oluşturmak için [az VM Create](/cli/azure/vm#az-vm-create)kullanın.

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Uygulama ağ geçidi oluşturma

[Az Network Application-Gateway Create](/cli/azure/network/application-gateway)kullanarak bir uygulama ağ geçidi oluşturun. Azure CLı ile bir uygulama ağ geçidi oluşturduğunuzda, kapasite, SKU ve HTTP ayarları gibi yapılandırma bilgilerini belirtirsiniz. Daha sonra Azure, ağ arabirimlerinin özel IP adreslerini uygulama ağ geçidinin arka uç havuzunda sunucu olarak ekler.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Azure 'un uygulama ağ geçidini oluşturması 30 dakika kadar sürebilir. Oluşturulduktan sonra, **uygulama ağ geçidi** sayfasının **Ayarlar** bölümünde aşağıdaki ayarları görebilirsiniz:

- **Appgatewaybackendpool**: **arka uç havuzları** sayfasında bulunur. Gerekli arka uç havuzunu belirtir.
- **Appgatewaybackendhttpsettings**: **http ayarları** sayfasında bulunur. Uygulama ağ geçidinin, iletişim için 80 bağlantı noktasını ve HTTP protokolünü kullandığını belirtir.
- **Appgatewayhttplistener**: **dinleyiciler sayfasında**bulunur. **Appgatewaybackendpool**ile ilişkili varsayılan dinleyiciyi belirtir.
- **Appgatewayfrontendıp**: **ön uç IP yapılandırması** sayfasında bulunur. **Appgatewayhttplistener**öğesine *Myagpublicıpaddress* atar.
- **rule1**: **Rules** sayfasında bulunur. **Appgatewayhttplistener**ile ilişkili varsayılan yönlendirme kuralını belirtir.

## <a name="test-the-application-gateway"></a>Uygulama ağ geçidini test etme

Azure, uygulama ağ geçidini oluşturmak için bir NGıNX Web sunucusu gerektirmese de, Azure 'un uygulama ağ geçidini başarıyla oluşturup oluşturmadığını doğrulamak için bu hızlı başlangıç hızlı yüklüdür. Yeni uygulama ağ geçidinin genel IP adresini almak için [az Network public-IP Show](/cli/azure/network/public-ip#az-network-public-ip-show)kullanın. 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.
    
![Uygulama ağ geçidini test etme](./media/quick-create-cli/application-gateway-nginxtest.png)

Tarayıcıyı yenilediğinizde ikinci VM 'nin adını görmeniz gerekir. Bu, uygulama ağ geçidinin başarıyla oluşturulduğunu ve arka uca bağlanıp bağlanamayacağını gösterir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulama ağ geçidiyle oluşturduğunuz kaynaklara artık ihtiyacınız kalmadığında, kaynak grubunu kaldırmak için [az Group Delete](/cli/azure/group#az-group-delete) komutunu kullanın. Kaynak grubunu kaldırarak, uygulama ağ geçidini ve ilgili tüm kaynakları da kaldırırsınız.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure CLI kullanarak bir uygulama ağ geçidi ile web trafiğini yönetme](./tutorial-manage-web-traffic-cli.md)

