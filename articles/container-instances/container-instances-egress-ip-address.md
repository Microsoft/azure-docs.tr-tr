---
title: Statik giden IP 'yi yapılandırma
description: Giriş ve çıkış için güvenlik duvarının genel IP adresini kullanan Azure Container Instances iş yükleri için Azure Güvenlik duvarını ve Kullanıcı tanımlı yolları yapılandırma
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: a03c59652b9409d54bbe63c63a31fdd2228ac34e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878695"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Bir kapsayıcı grubuna giden ve gelen trafik için tek bir genel IP adresi yapılandırın

Bir dış IP adresi olan bir [kapsayıcı grubu](container-instances-container-groups.md) ayarlamak, dış istemcilerin gruptaki bir kapsayıcıya erışmek için IP adresini kullanmasına izin verir. Örneğin, bir tarayıcı kapsayıcıda çalışan bir Web uygulamasına erişebilir. Ancak, şu anda bir kapsayıcı grubu giden trafik için farklı bir IP adresi kullanır. Bu çıkış IP adresi programlı bir şekilde gösterilmez, bu da kapsayıcı grubu izleme ve istemci Güvenlik Duvarı kurallarının yapılandırılmasını daha karmaşıktır.

Bu makalede, [Azure Güvenlik Duvarı](../firewall/overview.md)ile tümleştirilmiş bir [Sanal ağda](container-instances-virtual-network-concepts.md) kapsayıcı grubu yapılandırma adımları sağlanmaktadır. Kapsayıcı grubuna ve güvenlik duvarı kurallarına Kullanıcı tanımlı bir yol ayarlayarak kapsayıcı grubuna ve öğesinden gelen trafiği yönlendirebilir ve tanımlayabilirsiniz. Kapsayıcı grubu giriş ve çıkış, güvenlik duvarının genel IP adresini kullanır. Tek bir çıkış IP adresi, Azure Container Instances sanal ağın alt ağında dağıtılan birden çok kapsayıcı grubu tarafından kullanılabilir.

Bu makalede, bu senaryoya yönelik kaynakları oluşturmak için Azure CLı 'yi kullanırsınız:

* [Sanal ağdaki](container-instances-vnet.md) Temsilcili bir alt ağa dağıtılan kapsayıcı grupları 
* Statik bir genel IP adresi ile ağda dağıtılan bir Azure Güvenlik Duvarı
* Kapsayıcı grupları alt ağında Kullanıcı tanımlı bir yol
* Güvenlik Duvarı girişi için bir NAT kuralı ve çıkış için bir uygulama kuralı

Daha sonra, güvenlik duvarı aracılığıyla örnek kapsayıcı gruplarından giriş ve çıkış doğrulayabilirsiniz.

## <a name="deploy-aci-in-a-virtual-network"></a>Sanal ağda acı 'yi dağıtma

Tipik bir durumda, bir kapsayıcı grubunun dağıtılacağı bir Azure sanal ağınız zaten olabilir. Tanıtım amacıyla, aşağıdaki komutlar kapsayıcı grubu oluşturulduğunda bir sanal ağ ve alt ağ oluşturur. Alt ağ Azure Container Instances için temsilci olarak oluşturulur. 

Kapsayıcı grubu görüntüden küçük bir Web uygulaması çalıştırır `aci-helloworld` . Bu resimde, belgelerdeki diğer makalelerde gösterildiği gibi, statik bir HTML sayfasına hizmet veren Node.js yazılmış küçük bir Web uygulamasını paketler.

İhtiyacınız varsa, önce [az Group Create][az-group-create] komutuyla bir Azure Kaynak grubu oluşturun. Örnek:

```azurecli
az group create --name myResourceGroup --location eastus
```

Aşağıdaki komut örneklerini basitleştirmek için, kaynak grubunun adı için bir ortam değişkeni kullanın:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

[Az Container Create][az-container-create] komutuyla kapsayıcı grubunu oluşturun:

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> `--subnet address-prefix`Alt ağınız için gereken IP adresi alanının değerini ayarlayın. En küçük desteklenen alt ağ/29 olur ve bu da sekiz IP adresi sağlar. Bazı IP adresleri Azure tarafından kullanılmak üzere ayrılmıştır.

Daha sonraki bir adımda kullanmak için [az Container Show] [az-Container-Show] komutunu çalıştırarak kapsayıcı grubunun özel IP adresini alın:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Azure Güvenlik duvarını ağda dağıtma

Aşağıdaki bölümlerde, sanal ağda bir Azure Güvenlik Duvarı dağıtmak için Azure CLı 'yi kullanın. Arka plan için bkz. [öğretici: Azure Güvenlik duvarını Azure Portal kullanarak dağıtma ve yapılandırma](../firewall/deploy-cli.md).

İlk olarak, güvenlik duvarı için AzureFirewallSubnet adlı bir alt ağ eklemek için [az Network VNET subnet Create][az-network-vnet-subnet-create] kullanın. AzureFirewallSubnet, bu alt ağın *gerekli* adıdır.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Alt ağda bir güvenlik duvarı oluşturmak için aşağıdaki [Azure CLI komutlarını](../firewall/deploy-cli.md) kullanın.

Henüz yüklenmemişse, [az Extension Add][az-extension-add] komutunu kullanarak güvenlik duvarı UZANTıSıNı Azure CLI 'ya ekleyin:

```azurecli
az extension add --name azure-firewall
```

Güvenlik Duvarı kaynaklarını oluşturun:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

[Az Network Firewall Update][az-network-firewall-update] komutunu kullanarak güvenlik duvarı yapılandırmasını güncelleştirin:

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

[Az Network Firewall IP-Config List][az-network-firewall-ip-config-list] komutunu kullanarak güvenlik DUVARıNıN özel IP adresini alın. Bu özel IP adresi sonraki bir komutta kullanılır.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
[Az Network public-IP Show][az-network-public-ip-show] komutunu kullanarak güvenlik DUVARıNıN genel IP adresini alın. Bu genel IP adresi sonraki bir komutta kullanılır.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>ACı alt ağında Kullanıcı tanımlı rota tanımlama

Azure Güvenlik Duvarı 'na giden trafiği incelemek için acı alt ağında kullanım tanımlı bir yol tanımlayın. Daha fazla bilgi için bkz. [yönlendirme ağ trafiği](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Yönlendirme tablosu oluşturma

İlk olarak, Route tablosunu oluşturmak için aşağıdaki [az Network Route-Table Create][az-network-route-table-create] komutunu çalıştırın. Yol tablosunu sanal ağla aynı bölgede oluşturun.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Yol oluştur

Rota tablosunda bir yol oluşturmak için [az Network-Route-Table Route Create][az-network-route-table-route-create] ' i çalıştırın. Trafiği güvenlik duvarına yönlendirmek için, sonraki atlama türünü olarak ayarlayın `VirtualAppliance` ve güvenlik duvarının özel IP adresini sonraki atlama adresi olarak geçirin.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Yol tablosunu acı alt ağıyla ilişkilendir

Yol tablosunu Azure Container Instances için Temsilcili alt ağ ile ilişkilendirmek için [az Network VNET subnet Update][az-network-vnet-subnet-update] komutunu çalıştırın.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Güvenlik duvarında kuralları yapılandırma

Azure Güvenlik Duvarı varsayılan olarak gelen ve giden trafiği reddeder (engeller). 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Güvenlik duvarında acı alt ağına NAT kuralını yapılandırma

Güvenlik duvarında, gelen internet trafiğini, ağda daha önce başlattığınız uygulama kapsayıcısına çevirmek ve filtrelemek için bir [NAT kuralı](../firewall/rule-processing.md) oluşturun. Ayrıntılar için bkz. [Azure Güvenlik Duvarı ile gelen Internet trafiğini filtreleme DNAT](../firewall/tutorial-firewall-dnat.md)

[Az Network Firewall NAT-Rule Create][az-network-firewall-nat-rule-create] komutunu kullanarak bir NAT kuralı ve koleksiyonu oluşturun:

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Alt ağdaki diğer IP adresleriyle trafiği filtrelemek için gereken NAT kuralları ekleyin. Örneğin, alt ağdaki diğer kapsayıcı grupları gelen trafiğin IP adreslerini açığa çıkarır veya bir yeniden başlatmadan sonra kapsayıcı grubuna diğer iç IP adresleri atanabilir.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Güvenlik duvarında giden uygulama kuralı oluşturma

Güvenlik duvarında giden bir kural oluşturmak için şu [az Network Firewall Application-Rule Create][az-network-firewall-application-rule-create] komutunu çalıştırın. Bu örnek kural, temsil edilen alt ağdan Azure Container Instances FQDN 'sine erişim sağlar `checkip.dyndns.org` . Siteye HTTP erişimi, sonraki bir adımda Azure Container Instances çıkış IP adresini doğrulamak için kullanılır.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Güvenlik Duvarı üzerinden kapsayıcı grubu erişimini test etme

Aşağıdaki bölümler Azure Container Instances için temsilci alt ağın Azure Güvenlik duvarının arkasında düzgün şekilde yapılandırıldığını doğrular. Önceki adımlarda, gelen trafik hem alt ağa hem de alt ağdan giden trafiğe yönlendirilirken güvenlik duvarından geçer.

### <a name="test-ingress-to-a-container-group"></a>Bir kapsayıcı grubuna test girişi

Güvenlik duvarının genel IP adresine giderek sanal ağda çalışan *AppContainer* 'a gelen erişimi test edin. Daha önce, genel IP adresini $FW değişkende depoladıysanız _PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

Çıkış şuna benzer olacaktır:

```console
52.142.18.133
```

Güvenlik duvarındaki NAT kuralı düzgün şekilde yapılandırıldıysa, güvenlik duvarının genel IP adresini tarayıcınıza girdiğinizde aşağıdakileri görürsünüz:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Güvenlik duvarının genel IP adresine gidin":::

### <a name="test-egress-from-a-container-group"></a>Bir kapsayıcı grubundan test çıkış


Aşağıdaki örnek kapsayıcıyı sanal ağa dağıtın. Çalıştığında, `http://checkip.dyndns.org` GÖNDERENIN IP adresini (çıkış IP adresi) görüntüleyen öğesine tek BIR http isteği gönderir. Güvenlik duvarındaki uygulama kuralı düzgün şekilde yapılandırıldıysa, güvenlik duvarının genel IP adresi döndürülür.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

IP adresinin güvenlik duvarının genel IP adresiyle aynı olduğunu onaylamak için kapsayıcı günlüklerini görüntüleyin.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

Çıkış şuna benzer olacaktır:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Azure Güvenlik duvarının arkasındaki bir sanal ağda kapsayıcı grupları ayarlarsınız. Güvenlik duvarında Kullanıcı tanımlı bir yol ve NAT ve uygulama kuralları yapılandırdınız. Bu yapılandırmayı kullanarak Azure Container Instances giriş ve çıkış için tek ve statik bir IP adresi ayarlarsınız.

Trafiği yönetme ve Azure kaynaklarını koruma hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı](../firewall/index.yml) belgeleri.



[az-group-create]: /cli/azure/group#az_group_create
[az-container-create]: /cli/azure/container#az_container_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-network-firewall-update]: /cli/azure/network/firewall#az_network_firewall_update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az_network_public_ip_show
[az-network-route-table-create]:/cli/azure/network/route-table/#az_network_route_table_create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az_network_route_table_route_create
[az-network-firewall-ip-config-list]: /cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_update
[az-container-exec]: /cli/azure/container#az_container_exec
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-open-port]: /cli/azure/vm#az_vm_open_port
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[az-network-firewall-application-rule-create]: /cli/azure/network/firewall/application-rule#az_network_firewall_application_rule_create
[az-network-firewall-nat-rule-create]: /cli/azure/network/firewall/nat-rule#az_network_firewall_nat_rule_create
