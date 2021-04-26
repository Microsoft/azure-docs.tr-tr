---
title: Hızlı başlangıç-Azure CLı kullanarak Azure özel bağlantı hizmeti oluşturma
description: Bu hızlı başlangıçta Azure CLı kullanarak Azure özel bağlantı hizmeti oluşturmayı öğrenin
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: c8e32a56148326104c3514b8a2fdb5d6bbd3f00a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778490"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak özel bağlantı hizmeti oluşturma

Hizmetinize başvuran bir özel bağlantı hizmeti oluşturmaya başlayın.  Azure Standart Load Balancer arkasında dağıtılan hizmetinize veya kaynağa özel bağlantı erişimi verin.  Hizmetinizin kullanıcılarının sanal ağından özel erişimi vardır.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[Az Group Create](/cli/azure/group#az_group_create)ile bir kaynak grubu oluşturun:

* Adlandırılmış **Createprılinkservice-RG**. 
* **Eastus** konumunda.

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>İç yük dengeleyici oluşturma

Bu bölümde, bir sanal ağ ve bir iç Azure Load Balancer oluşturacaksınız.

### <a name="virtual-network"></a>Sanal ağ

Bu bölümde, özel bağlantı hizmetinize erişen yük dengeleyiciyi barındırmak için bir sanal ağ ve alt ağ oluşturursunuz.

[Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create)kullanarak bir sanal ağ oluşturun:

* **Myvnet** adında.
* **10.1.0.0/16** adres ön eki.
* **Mysubnet** adlı alt ağ.
* **10.1.0.0/24** alt ağ ön eki.
* **Createprılinkservice-RG** kaynak grubunda.
* **Eastus2** konumu.
* Alt ağda özel bağlantı hizmeti için ağ ilkesini devre dışı bırakın.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Alt ağı özel bağlantı hizmeti ağ ilkelerini devre dışı bırakacak şekilde güncelleştirmek için [az Network VNET subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)kullanın:

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Standart yük dengeleyici oluştur

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:

  * Yük dengeleyicide gelen ağ trafiğini alan bir ön uç IP Havuzu.
  * Ön uç havuzunun yük dengeli ağ trafiğini gönderdiği bir arka uç IP Havuzu.
  * Arka uç sanal makine örneklerinin sistem durumunu belirleyen bir sistem durumu araştırması.
  * Trafiğin VM 'lere nasıl dağıtıldığını tanımlayan bir yük dengeleyici kuralı.

### <a name="create-the-load-balancer-resource"></a>Yük dengeleyici kaynağı oluşturma

[Az Network lb Create](/cli/azure/network/lb#az_network_lb_create)komutuyla bir genel yük dengeleyici oluşturun:

* **Myloadbalancer** adlı.
* **Myön uç** adlı bir ön uç Havuzu.
* **Mybackendpool** adlı bir arka uç Havuzu.
* Sanal ağ **Myvnet** ile ilişkili.
* Arka uç alt ağı **mysubnet** ile ilişkilendirildi.

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Durum araştırması oluşturma

Bir sistem durumu araştırması, tüm sanal makine örneklerini denetleyerek ağ trafiği gönderebilecekleri emin olmanızı sağlar. 

Başarısız araştırma denetimine sahip bir sanal makine yük dengeleyiciden kaldırılır. Hata çözüldüğünde sanal makine yük dengeleyiciye geri eklenir.

[Az Network lb araştırması Create](/cli/azure/network/lb/probe#az_network_lb_probe_create)komutuyla bir sistem durumu araştırması oluşturun:

* Sanal makinelerin sistem durumunu izler.
* Adlandırılmış **Myhealtharaştırma**.
* Protokol **TCP**.
* İzleme **bağlantı noktası 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı şunları tanımlar:

* Gelen trafik için ön uç IP yapılandırması.
* Trafiği almak için arka uç IP Havuzu.
* Gerekli kaynak ve hedef bağlantı noktası. 

[Az Network lb Rule Create](/cli/azure/network/lb/rule#az_network_lb_rule_create)ile bir yük dengeleyici kuralı oluşturun:

* Adlandırılmış **Myhttprule**
* Ön **uç** havuzundaki **80 numaralı bağlantı noktası** dinleniyor.
* **80 numaralı bağlantı noktasını** kullanan **mybackendpool** arka uç adres havuzuna yük dengeli ağ trafiği gönderiliyor. 
* Sistem durumu araştırması **Myhealtharaştırması** kullanılıyor.
* Protokol **TCP**.
* **15 dakikalık** boşta zaman aşımı.
* TCP sıfırlamayı etkinleştirin.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Özel bağlantı hizmeti oluşturma

Bu bölümde, önceki adımda oluşturulan Azure Load Balancer kullanan bir özel bağlantı hizmeti oluşturun.

[Az Network Private-link-Service Create](/cli/azure/network/private-link-service#az_network_private_link_service_create)komutuyla standart yük dengeleyici ön uç IP yapılandırması kullanarak özel bir bağlantı hizmeti oluşturun:

* Adlandırılmış **Myprivatelinkservice**.
* Sanal ağ **\** sanal ağı 'nda.
* Standart yük dengeleyici **Myloadbalancer** ve ön uç yapılandırması **myön** ucu ile ilişkili.
* **Eastus2** konumunda.
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Özel bağlantı hizmetiniz oluşturulur ve trafik alabilir. Trafik akışlarını görmek istiyorsanız uygulamanızı standart yük dengeleyicinizin arkasında yapılandırın.


## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde özel bağlantı hizmetini özel bir uç nokta ile eşlersiniz. Bir sanal ağ, özel bağlantı hizmetinin özel uç noktasını içerir. Bu sanal ağ, özel bağlantı hizmetinize erişecek kaynakları içerir.

### <a name="create-private-endpoint-virtual-network"></a>Özel uç nokta sanal ağı oluştur

[Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create)kullanarak bir sanal ağ oluşturun:

* **Myvnetpe** adlı adlandırılmış.
* **11.1.0.0/16** adres ön eki.
* **Mysubnetpe** adlı alt ağ.
* **11.1.0.0/24** alt ağ ön eki.
* **Createprılinkservice-RG** kaynak grubunda.
* **Eastus2** konumu.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Alt ağı özel uç nokta ağ ilkelerini devre dışı bırakacak şekilde güncelleştirmek için [az Network VNET subnet Update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)kullanın:

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Uç nokta ve bağlantı oluştur

* Özel bağlantı hizmetinin kaynak KIMLIĞINI almak için [az Network Private-link-Service Show](/cli/azure/network/private-link-service#az_network_private_link_service_show) ' i kullanın. Komutu, kaynak KIMLIĞINI daha sonra kullanmak üzere bir değişkene koyar.

* Daha önce oluşturduğunuz sanal ağda özel uç noktayı oluşturmak için [az Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) kullanın.

* Adlandırılmış **Myprivateendpoint**.
* **Createprılinkservice-RG** kaynak grubunda.
* Bağlantı adı **Mtypeınfo**.
* **Eastus2** konumu.
* Sanal ağ, **Myvnetpe** ve subnet **mysubnetpe**.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, özel bağlantı hizmetini, yük dengeleyiciyi ve tüm ilgili kaynakları kaldırın.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta:

* Bir sanal ağ ve dahili Azure Load Balancer oluşturulur.
* Özel bir bağlantı hizmeti oluşturuldu

Azure özel uç noktası hakkında daha fazla bilgi edinmek için devam edin:
> [!div class="nextstepaction"]
> [Hızlı başlangıç: Azure CLı kullanarak özel uç nokta oluşturma](create-private-endpoint-cli.md)
