---
title: Azure CLı kullanarak Azure özel bağlantı hizmeti oluşturma
description: Azure CLı kullanarak Azure özel bağlantı hizmeti oluşturmayı öğrenin
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 57ab18c8dfffb6994983179f434491b97589ebda
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693239"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Azure CLı kullanarak özel bağlantı hizmeti oluşturma
Bu makalede Azure CLı kullanarak Azure 'da özel bir bağlantı hizmeti oluşturma gösterilmektedir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Bunun yerine Azure CLı 'yı yüklemeye ve kullanmaya karar verirseniz, bu hızlı başlangıç, en son Azure CLı sürümünü kullanmanızı gerektirir. Yüklü sürümünüzü bulmak için `az --version`çalıştırın. Bkz. Install veya Upgrade Info for [Azure CLI](/cli/azure/install-azure-cli) .
## <a name="create-a-private-link-service"></a>Özel Bağlantı hizmeti oluşturma
### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bir sanal ağ oluşturabilmeniz için önce sanal ağı barındırmak üzere bir kaynak grubu oluşturmanız gerekir. [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Bu örnek *westcentralus* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma
[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) komutu ile bir sanal ağ oluşturun. Bu örnek, *Mysubnet*adlı bir alt ağ ile *myVirtualNetwork* adlı varsayılan bir sanal ağ oluşturur:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Alt ağ oluşturma
[Az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)komutuyla sanal ağ için bir alt ağ oluşturun. Bu örnek, *myVirtualNetwork* sanal ağında *mysubnet* adlı bir alt ağ oluşturur:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Iç Load Balancer oluşturma 
[Az Network lb Create](/cli/azure/network/lb#az-network-lb-create)ile bir iç yük dengeleyici oluşturun. Bu örnek, *Myresourcegroup*adlı kaynak grubunda *myılb* adlı bir iç yük dengeleyici oluşturur. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Yük dengeleyici durum yoklaması oluşturma

Durum araştırması tüm sanal makine örneklerini denetleyerek ağ trafiği aldıklarından emin olur. Sistem durumu denetimi başarısız olan sanal makine örnekleri tekrar çevrimiçi olana ve sistem durumu denetimi iyi olduğuna karar verene kadar yük dengeleyiciden kaldırılır. Sanal makinelerin durumunu izlemek için [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) ile bir durum araştırması oluşturun. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı, gerekli kaynak ve hedef bağlantı noktalarının yanı sıra gelen trafik için ön uç IP yapılandırmasını ve trafiği almak için arka uç IP havuzunu tanımlar. *myFrontEnd* ön uç havuzunda 80 numaralı bağlantı noktasını dinlemek ve yine 80 numaralı bağlantı noktasını kullanarak [myBackEndPool](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) arka uç adres havuzuna yük dengelemesi yapılmış ağ trafiğini göndermek için *az network lb rule create* ile *myHTTPRule* yük dengeleyici kuralı oluşturun. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Arka uç sunucular oluşturma

Bu örnekte, sanal makine oluşturmayı kapsamıyoruz. Yük Dengeleyici için arka uç sunucular olarak kullanılacak iki sanal makine oluşturmak üzere [Azure CLI kullanarak VM 'leri yük dengelemesi yapmak için iç yük dengeleyici oluşturma](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) bölümündeki adımları izleyebilirsiniz. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Alt ağda özel bağlantı hizmeti ağ ilkelerini devre dışı bırak 
Özel bağlantı hizmeti, sanal ağ içinde tercih ettiğiniz herhangi bir alt ağdan bir IP gerektirir. Şu anda bu IP 'lerde ağ Ilkelerini desteklemiyoruz.  Bu nedenle, alt ağdaki ağ ilkelerini devre dışı bıraktık. [Az Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)Ile özel bağlantı hizmeti ağ ilkelerini devre dışı bırakmak için alt ağı güncelleştirin.

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Özel Bağlantı hizmeti oluşturma  
 
[Az Network Private-link-Service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create)komutuyla standart Load Balancer ön uç IP yapılandırması kullanarak bir özel bağlantı hizmeti oluşturun. Bu örnek, *Myresourcegroup*adlı kaynak grubunda *myloadbalancer* adlı standart Load Balancer kullanarak *Mypls* adlı bir özel bağlantı hizmeti oluşturur. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Oluşturulduktan sonra, özel bağlantı hizmeti KIMLIĞI ' ni bir yere göz atın. Bu hizmetle bağlantı istemek için daha sonra ihtiyacınız olacak.  
 
Bu aşamada, özel bağlantı hizmetiniz başarıyla oluşturulur ve trafik almaya hazırdır. Yukarıdaki örneğin yalnızca Azure CLı kullanarak özel bağlantı hizmeti oluşturmayı gösterdiğine unutmayın.  Trafiği dinlemek için yük dengeleyici arka uç havuzlarını veya arka uç havuzlarındaki herhangi bir uygulamayı yapılandırmadınız. Uçtan uca trafik akışlarını görmek istiyorsanız uygulamanızı Standart Load Balancer arka planda yapılandırmanız önemle tavsiye edilir.  
 
Daha sonra, Azure CLı kullanarak bu hizmetin farklı bir sanal ağda özel bir uç noktaya nasıl eşleneceğini göstereceğiz. Bu örnek, Özel uç nokta oluşturma ve yukarıda oluşturulan özel bağlantı hizmetine Azure CLı kullanılarak bağlanma ile sınırlıdır. Ayrıca, Özel uç noktaya trafik göndermek/almak için sanal ağda sanal makineler oluşturabilirsiniz.        
 
## <a name="private-endpoints"></a>Özel uç noktalar

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma 
 [Az Network VNET Create](/cli/azure/network/vnet#az-network-vnet-create)komutuyla bir sanal ağ oluşturun. Bu örnek, *Myresourcegroup*adlı kaynak grubunda *Mypevnet* adlı bir sanal ağ oluşturur: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Alt ağ oluşturma 
 [Az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)komutuyla sanal ağ üzerinde bir alt ağ oluşturun. Bu örnek, *Myresourcegroup*adlı kaynak grubunda *Mypevnet* adlı sanal ağ  *mysubnet* adlı bir alt ağ oluşturur: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Alt ağda özel uç nokta ağ ilkelerini devre dışı bırak 
Özel uç nokta, bir sanal ağ içindeki tercih ettiğiniz herhangi bir alt ağda oluşturulabilir. Şu anda özel uç noktalarda ağ ilkelerini desteklemiyoruz.  Bu nedenle, alt ağdaki ağ ilkelerini devre dışı bıraktık. [Az Network VNET subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)ile özel uç nokta ağ ilkelerini devre dışı bırakmak için alt ağı güncelleştirin. 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Özel uç nokta oluştur ve özel bağlantı hizmetine bağlan 
Sanal ağınızda yukarıda oluşturulan özel bağlantı hizmeti için özel bir uç nokta oluşturun:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Özel bağlantı hizmetinde `az network private-link-service show` ile *özel bağlantı-kaynak kimliği* alabilirsiniz. KIMLIK şöyle görünür:   
/Subscriptions/subıd/ResourceGroups/*resourcegroupname*/Providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Özel bağlantı hizmeti bağlantılarını göster 
 
[Az Network Private-link-Service Show](/cli/azure/network/private-link-service#az-network-private-link-service-show)kullanarak özel bağlantı hizmetinizde bağlantı isteklerine bakın.    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Sonraki adımlar
- [Azure özel bağlantı hizmeti](private-link-service-overview.md) hakkında daha fazla bilgi edinin
 
