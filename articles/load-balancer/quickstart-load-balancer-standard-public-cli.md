---
title: 'Hızlı başlangıç: ortak yük dengeleyici oluşturma-Azure CLı'
titleSuffix: Azure Load Balancer
description: Bu hızlı başlangıçta, Azure CLI kullanarak genel bir yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 9b332b18930d58ebb1d155c35a74eed69a90ce73
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788790"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Hızlı Başlangıç: Azure CLI kullanarak sanal makinelerin yük dengelemesi için genel yük dengeleyici oluşturma

Ortak yük dengeleyici ve üç sanal makine oluşturmak için Azure CLı kullanarak Azure Load Balancer kullanmaya başlayın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[Az Group Create](/cli/azure/group#az_group_create)ile bir kaynak grubu oluşturun:

* Adlandırılmış **Createpublbqs-RG**. 
* **Eastus** konumunda.

```azurecli-interactive
  az group create \
    --name CreatePubLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standart SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir. SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram.png" alt-text="Hızlı başlangıç için oluşturulan Standart yük dengeleyici kaynakları." border="false":::

## <a name="configure-virtual-network---standard"></a>Sanal ağ yapılandırma-standart

VM 'Leri dağıtmadan ve yük dengeleyicinizi test etmeden önce destekleyici sanal ağ kaynaklarını oluşturun.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_createt)kullanarak bir sanal ağ oluşturun:

* **Myvnet** adında.
* **10.1.0.0/16** adres ön eki.
* **Mybackendsubnet** adlı alt ağ.
* **10.1.0.0/24** alt ağ ön eki.
* **Createpublbqs-RG** kaynak grubunda.
* **Eastus** konumu.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Savunma konağı için genel bir IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) kullanın:

* **Mybastionıp** adlı standart bölge YEDEKLI genel IP adresi oluşturun.
* **Ccreatepublbqs-RG** içinde.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Savunma alt ağı oluşturma

Bir savunma alt ağı oluşturmak için [az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) kullanın:

* **AzureBastionSubnet** adlı.
* **10.1.1.0/24** adres ön eki.
* Sanal ağ **\** sanal ağı 'nda.
* **Createpublbqs-RG** kaynak grubunda.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Savunma Konağı oluştur

Bir savunma konağı oluşturmak için [az Network savunma Create](/cli/azure/network/bastion#az_network_bastion_create) kullanın:

* Adlandırılmış **Mybastionhost**.
* **Createpublbqs-RG** içinde.
* Genel IP **Mybastionıp** ile ilişkilendirildi.
* Sanal ağ **Myvnet** ile ilişkilendirildi.
* **Eastus** konumunda.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure savunma konağının dağıtılması birkaç dakika sürebilir.

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Standart yük dengeleyici için arka uç adresindeki VM 'Lerin bir ağ güvenlik grubuna ait olan ağ arabirimlerine sahip olması gerekir. 

[Az Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create)kullanarak bir ağ güvenlik grubu oluşturun:

* **Mynsg** adlı adlandırılmış.
* **Createpublbqs-RG** kaynak grubunda.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturma

[Az Network NSG Rule Create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)kullanarak bir ağ güvenlik grubu kuralı oluşturun:

* **Mynsgrutahttp** adında.
* Önceki adımda oluşturduğunuz ağ güvenlik grubunda, **Mynsg**.
* **Createpublbqs-RG** kaynak grubunda.
* Protokol **(*)**.
* Yön **gelen**.
* Kaynak **(*)**.
* Hedef **(*)**.
* Hedef bağlantı noktası **80**.
* Erişime **Izin ver**.
* Öncelik **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Arka uç sunucuları oluşturma-standart

Bu bölümde şunları oluşturursunuz:

* Sanal makineler için üç ağ arabirimi.
* Yük Dengeleyici için arka uç sunucular olarak kullanılacak üç sanal makine.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Sanal makineler için ağ arabirimleri oluşturma

[Az Network Nic Create](/cli/azure/network/nic#az_network_nic_create)ile üç ağ arabirimi oluşturun:

* Adlandırılmış **myNicVM1**, **myNicVM2** ve **myNicVM3**.
* **Createpublbqs-RG** kaynak grubunda.
* Sanal ağ **\** sanal ağı 'nda.
* Alt ağda **Mybackendsubnet**.
* Ağ güvenlik grubu ' nda, **Mynsg**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

[Az VM Create](/cli/azure/vm#az_vm_create)ile sanal makineler oluşturun:

### <a name="vm1"></a>VM1
* **MyVM1** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Ağ arabirimine bağlı **myNicVM1**.
* Sanal makine görüntüsü **win2019datacenter**.
* **Bölge 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 1 \
    --no-wait
```
#### <a name="vm2"></a>VM2
* **MyVM2** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Ağ arabirimine bağlı **myNicVM2**.
* Sanal makine görüntüsü **win2019datacenter**.
* **Bölge 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* **MyVM3** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Ağ arabirimine bağlı **myNicVM3**.
* Sanal makine görüntüsü **win2019datacenter**.
* **Bölge 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone 3 \
    --no-wait
```
VM 'Lerin dağıtılması birkaç dakika sürebilir.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---standard"></a>Genel IP adresi oluşturma-standart

Web uygulamanıza İnternet’ten erişmek için yük dengeleyicinin genel IP adresi gereklidir. 

[Az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) to kullanın:

* **Mypublicıp** adlı standart bölge YEDEKLI genel IP adresi oluşturun.
* **Createpublbqs-RG** içinde.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard
```

Bölge 1 ' de gereksiz bir genel IP adresi oluşturmak için:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Standart yük dengeleyici oluştur

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
* Önceki adımda oluşturduğunuz **Mypublicıp** genel IP adresiyle ilişkili. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
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
    --resource-group CreatePubLBQS-rg \
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
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true

```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Yük dengeleyici arka uç havuzuna sanal makineler ekleme

[Az Network Nic IP-Config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)komutuyla sanal makineleri arka uç havuzuna ekleyin:

* Arka uç adres havuzunda **Mybackendpool**.
* **Createpublbqs-RG** kaynak grubunda.
* Yük dengeleyici **Myloadbalancer** ile ilişkili.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

## <a name="create-outbound-rule-configuration"></a>Giden kuralı yapılandırması oluştur
Yük dengeleyici giden kuralları arka uç havuzundaki VM 'Ler için giden SNAT 'yi yapılandırır. 

Giden bağlantılar hakkında daha fazla bilgi için bkz. [Azure 'Da giden bağlantılar](load-balancer-outbound-connections.md).

Giden yapılandırma için genel bir IP veya ön ek kullanılabilir.

### <a name="public-ip"></a>Genel IP

Giden bağlantı için tek bir IP oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) kullanın.  

* Adlandırılmış **Mypublicıpoıb Utbağlanmadı**.
* **Createpublbqs-RG** içinde.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard
```

Bölge 1 ' de gereksiz bir genel IP adresi oluşturmak için:

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```

### <a name="public-ip-prefix"></a>Genel IP öneki

Giden bağlantı için genel bir IP öneki oluşturmak için [az Network public-ip Create oluştur](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) kullanın.

* **MyPublicIPPrefixOutbound** adlı.
* **Createpublbqs-RG** içinde.
* **28** önek uzunluğu.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Bölge 1 ' de gereksiz bir genel IP öneki oluşturmak için:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

Giden NAT ve giden bağlantıların ölçeklendirilmesi hakkında daha fazla bilgi için bkz. [birden çok IP adresi Ile genişleme gıden NAT](load-balancer-outbound-connections.md).

### <a name="create-outbound-frontend-ip-configuration"></a>Giden ön uç IP yapılandırması oluştur

[Az Network lb ön uç-IP Create ](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create)ile yeni bir ön uç IP yapılandırması oluşturun:

Önceki adımdaki kararı temel alarak genel IP veya genel IP öneki komutlarını seçin.

#### <a name="public-ip"></a>Genel IP

* **MyFrontEndOutbound** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Genel IP adresiyle ilişkili **Mypublicıpoıb Utbağlanmadı**.
* Yük dengeleyici **Myloadbalancer** ile ilişkili.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Genel IP ön eki

* **MyFrontEndOutbound** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Genel IP önekiyle ilişkili **myPublicIPPrefixOutbound**.
* Yük dengeleyici **Myloadbalancer** ile ilişkili.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Giden Havuz oluştur

[Az Network lb Address-Pool Create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create)komutuyla yeni bir giden havuz oluşturun:

* **Mybackendpooloutbound** adlı adlandırılmış.
* **Createpublbqs-RG** kaynak grubunda.
* Yük dengeleyici **Myloadbalancer** ile ilişkili.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Giden kuralı oluştur

Giden arka uç havuzu için [az Network lb giden kuralı oluştur](/cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_create)ile yeni bir giden kuralı oluşturun:

* Adlandırılmış **mbir Boundrule**.
* **Createpublbqs-RG** kaynak grubunda.
* Yük dengeleyici **Myloadbalancer** ile ilişkili
* Ön uç **myFrontEndOutbound** ile ilişkili.
* **Tüm** protokol.
* **15**' in boşta kalma süresi.
* **10000** giden bağlantı noktası.
* Arka uç havuzuyla ilişkili **Mybackendpooloutbound**.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Sanal makineleri giden havuzuna Ekle

[Az Network Nic IP-Config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)komutuyla sanal makineleri giden havuza ekleyin:


* Arka uç adres havuzunda **Mybackendpooloutbound**.
* **Createpublbqs-RG** kaynak grubunda.
* Yük dengeleyici **Myloadbalancer** ile ilişkili.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPoolOutbound \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

# <a name="basic-sku"></a>[**Temel SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir. SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

:::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/resources-diagram-basic.png" alt-text="Hızlı başlangıçta oluşturulan temel yük dengeleyici kaynakları." border="false"::: m

## <a name="configure-virtual-network---basic"></a>Sanal ağı yapılandırma-temel

VM 'Leri dağıtmadan ve yük dengeleyicinizi test etmeden önce destekleyici sanal ağ kaynaklarını oluşturun.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create)kullanarak bir sanal ağ oluşturun:

* **Myvnet** adında.
* **10.1.0.0/16** adres ön eki.
* **Mybackendsubnet** adlı alt ağ.
* **10.1.0.0/24** alt ağ ön eki.
* **Createpublbqs-RG** kaynak grubunda.
* **Eastus** konumu.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePubLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Savunma konağı için genel bir IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) kullanın:

* **Mybastionıp** adlı standart bölge YEDEKLI genel IP adresi oluşturun.
* **Createpublbqs-RG** içinde.

```azurecli-interactive
az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Savunma alt ağı oluşturma

Bir savunma alt ağı oluşturmak için [az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) kullanın:

* **AzureBastionSubnet** adlı.
* **10.1.1.0/24** adres ön eki.
* Sanal ağ **\** sanal ağı 'nda.
* **Createpublbqs-RG** kaynak grubunda.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreatePubLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Savunma Konağı oluştur

Bir savunma konağı oluşturmak için [az Network savunma Create](/cli/azure/network/bastion#az_network_bastion_create) kullanın:

* Adlandırılmış **Mybastionhost**.
* **Createpublbqs-RG** içinde.
* Genel IP **Mybastionıp** ile ilişkilendirildi.
* Sanal ağ **Myvnet** ile ilişkilendirildi.
* **Eastus** konumunda.

```azurecli-interactive
az network bastion create \
    --resource-group CreatePubLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure savunma konağının dağıtılması birkaç dakika sürebilir.

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Standart yük dengeleyici için arka uç adresindeki VM 'Lerin bir ağ güvenlik grubuna ait olan ağ arabirimlerine sahip olması gerekir. 

[Az Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create)kullanarak bir ağ güvenlik grubu oluşturun:

* **Mynsg** adlı adlandırılmış.
* **Createpublbqs-RG** kaynak grubunda.

```azurecli-interactive
  az network nsg create \
    --resource-group CreatePubLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturma

[Az Network NSG Rule Create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)kullanarak bir ağ güvenlik grubu kuralı oluşturun:

* **Mynsgrutahttp** adında.
* Önceki adımda oluşturduğunuz ağ güvenlik grubunda, **Mynsg**.
* **Createpublbqs-RG** kaynak grubunda.
* Protokol **(*)**.
* Yön **gelen**.
* Kaynak **(*)**.
* Hedef **(*)**.
* Hedef bağlantı noktası **80**.
* Erişime **Izin ver**.
* Öncelik **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreatePubLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Arka uç sunucuları oluşturma-temel

Bu bölümde şunları oluşturursunuz:

* Sanal makineler için üç ağ arabirimi.
* Sanal makineler için kullanılabilirlik kümesi
* Yük Dengeleyici için arka uç sunucular olarak kullanılacak üç sanal makine.


### <a name="create-network-interfaces-for-the-virtual-machines"></a>Sanal makineler için ağ arabirimleri oluşturma

[Az Network Nic Create](/cli/azure/network/nic#az_network_nic_create)ile üç ağ arabirimi oluşturun:


* Adlandırılmış **myNicVM1**, **myNicVM2** ve **myNicVM3**.
* **Createpublbqs-RG** kaynak grubunda.
* Sanal ağ **\** sanal ağı 'nda.
* Alt ağda **Mybackendsubnet**.
* Ağ güvenlik grubu ' nda, **Mynsg**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreatePubLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```
### <a name="create-availability-set-for-virtual-machines"></a>Sanal makineler için kullanılabilirlik kümesi oluştur

Kullanılabilirlik kümesini [az VM AVAILABILITY-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create)ile oluşturun:

* **MyAvSet** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Konum **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreatePubLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Sanal makineler oluşturma

[Az VM Create](/cli/azure/vm#az_vm_create)ile sanal makineler oluşturun:

### <a name="vm1"></a>VM1
* **MyVM1** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Ağ arabirimine bağlı **myNicVM1**.
* Sanal makine görüntüsü **win2019datacenter**.
* **Bölge 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
#### <a name="vm2"></a>VM2
* **MyVM2** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Ağ arabirimine bağlı **myNicVM2**.
* Sanal makine görüntüsü **win2019datacenter**.
* **Bölge 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```

#### <a name="vm3"></a>VM3
* **MyVM3** adlı.
* **Createpublbqs-RG** kaynak grubunda.
* Ağ arabirimine bağlı **myNicVM3**.
* Sanal makine görüntüsü **win2019datacenter**.
* **Bölge 3**.

```azurecli-interactive
   az vm create \
    --resource-group CreatePubLBQS-rg \
    --name myVM3 \
    --nics myNicVM3 \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvSet \
    --no-wait
```
VM 'Lerin dağıtılması birkaç dakika sürebilir.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address---basic"></a>Genel IP adresi oluşturma-temel

Web uygulamanıza İnternet’ten erişmek için yük dengeleyicinin genel IP adresi gereklidir. 

[Az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) to kullanın:

* **Mypublicıp** adlı standart bölge YEDEKLI genel IP adresi oluşturun.
* **Createpublbqs-RG** içinde.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Temel yük dengeleyici oluşturma

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
* Önceki adımda oluşturduğunuz **Mypublicıp** genel IP adresiyle ilişkili. 

```azurecli-interactive
  az network lb create \
    --resource-group CreatePubLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
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
    --resource-group CreatePubLBQS-rg \
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

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePubLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15
```

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Yük dengeleyici arka uç havuzuna sanal makineler ekleme

[Az Network Nic IP-Config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)komutuyla sanal makineleri arka uç havuzuna ekleyin:

* Arka uç adres havuzunda **Mybackendpool**.
* **Createpublbqs-RG** kaynak grubunda.
* Yük dengeleyici **Myloadbalancer** ile ilişkili.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic ip-config address-pool add \
     --address-pool myBackendPool \
     --ip-config-name ipconfig1 \
     --nic-name $vmnic \
     --resource-group CreatePubLBQS-rg \
     --lb-name myLoadBalancer
  done
```

---

## <a name="install-iis"></a>IIS yükleme

Sanal makinelere IIS yüklemek ve varsayılan Web sitesini bilgisayar adına ayarlamak için [az VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) kullanın.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreatePubLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

Yük dengeleyicinin genel IP adresini almak için [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) komutunu kullanın. 

Genel IP adresini kopyalayıp tarayıcınızın adres çubuğuna yapıştırın.

```azurecli-interactive
  az network public-ip show \
    --resource-group CreatePubLBQS-rg \
    --name myPublicIP \
    --query ipAddress \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Yük dengeleyiciyi test etme" border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları kaldırın.

```azurecli-interactive
  az group delete \
    --name CreatePubLBQS-rg
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta

* Standart veya genel yük dengeleyici oluşturdunuz
* Bağlı sanal makineler. 
* Yük dengeleyici trafik kuralı ve sistem durumu araştırması yapılandırıldı.
* Yük dengeleyici test edildi.

Azure Load Balancer hakkında daha fazla bilgi edinmek için devam edin:
> [!div class="nextstepaction"]
> [Azure Load Balancer nedir?](load-balancer-overview.md)