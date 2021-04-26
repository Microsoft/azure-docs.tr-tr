---
title: 'Hızlı başlangıç: iç yük dengeleyici oluşturma-Azure CLı'
titleSuffix: Azure Load Balancer
description: Bu hızlı başlangıçta, Azure CLı kullanarak iç yük dengeleyicinin nasıl oluşturulacağı gösterilmektedir.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788844"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak iç yük dengeleyici oluşturma

İç yük dengeleyici ve üç sanal makine oluşturmak için Azure CLı kullanarak Azure Load Balancer kullanmaya başlayın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanıyorsanız, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Kaynak grubu, Azure kaynaklarınızı dağıttığınız ve yönettiğiniz mantıksal bir kapsayıcıdır.

[az group create](/cli/azure/group#az_group_create) ile bir kaynak grubu oluşturun. **Createıntlbqs-RG** kaynak grubunu adlandırın ve konumu **eastus** olarak belirtin.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**Standart SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir. SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

Bu bölümde, sanal makinelerin yükünü dengeleyen bir yük dengeleyici oluşturacaksınız. Bir iç yük dengeleyici oluşturduğunuzda, yük dengeleyici için ağ olarak bir sanal ağ yapılandırılır. Aşağıdaki diyagramda bu hızlı başlangıçta oluşturulan kaynaklar gösterilmektedir:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Hızlı başlangıç için oluşturulan Standart yük dengeleyici kaynakları." border="false":::

### <a name="configure-the-virtual-network"></a>Sanal ağı yapılandırma

VM 'Leri dağıtmadan ve yük dengeleyiciyi dağıtmadan önce destekleyici sanal ağ kaynaklarını oluşturun.

#### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create)kullanarak bir sanal ağ oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Myvnet**
* **10.1.0.0/16** adres ön eki
* **Mybackendsubnet** adlı alt ağ
* **10.1.0.0/24** alt ağ öneki
* **Createıntlbqs-RG** kaynak grubunda
* **Eastus** konumu

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Azure savunma ana bilgisayarı için genel bir IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) kullanın. Aşağıdakileri belirtin:

* **Mybastionıp** adlı standart bölge-YEDEKLI genel IP adresi oluşturma
* **Createıntlbqs-RG** içinde

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure savunma alt ağı oluşturma

Alt ağ oluşturmak için [az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) kullanın. Aşağıdakileri belirtin:

* **AzureBastionSubnet** adlı
* **10.1.1.0/24** adres ön eki
* Sanal ağ **\ VNET** 'te
* **Createıntlbqs-RG** kaynak grubunda

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion konağı oluşturma

Bir konak oluşturmak için [az ağ savunma oluştur](/cli/azure/network/bastion#az_network_bastion_create) seçeneğini kullanın. Aşağıdakileri belirtin:

* Adlandırılmış **Mybastionhost**
* **Createıntlbqs-RG** içinde
* Ortak IP ile ilişkili **Myeltionıp**
* Sanal ağ **Myvnet** ile ilişkili
* **Eastus** konumunda

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure savunma konağının dağıtılması birkaç dakika sürebilir.

#### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Standart yük dengeleyici için, sanal makinelerinizin bir ağ güvenlik grubuna ait olan ağ arabirimlerine sahip olduğundan emin olun. [Az Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create)kullanarak bir ağ güvenlik grubu oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Mynsg**
* **Createıntlbqs-RG** kaynak grubunda

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturma

[Az Network NSG Rule Create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)kullanarak bir ağ güvenlik grubu kuralı oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Mynsgrutahttp**
* Önceki adımda oluşturduğunuz ağ güvenlik grubunda, **Mynsg**
* **Createıntlbqs-RG** kaynak grubunda
* Protokol **(*)**
* Yön **geliş**
* Kaynak **(*)**
* Hedef **(*)**
* Hedef bağlantı noktası **80**
* Erişime **Izin ver**
* Öncelik **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Arka uç sunucuları oluşturma

Bu bölümde şunları oluşturursunuz:

* Sanal makineler için üç ağ arabirimi.
* Yük Dengeleyici için sunucu olarak kullanılacak üç sanal makine.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Sanal makineler için ağ arabirimleri oluşturma

[Az Network Nic Create](/cli/azure/network/nic#az_network_nic_create)ile üç ağ arabirimi oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **myNicVM1**, **myNicVM2** ve **myNicVM3**
* **Createıntlbqs-RG** kaynak grubunda
* Sanal ağ **\ VNET** 'te
* Alt ağda **Mybackendsubnet**
* Ağ güvenlik grubu ' nda **Mynsg**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-virtual-machines"></a>Sanal makineleri oluşturma

[az vm create](/cli/azure/vm#az_vm_create) komutunu kullanarak sanal makineleri oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **myVM1**, **myVM2** ve **myVM3**
* **Createıntlbqs-RG** kaynak grubunda
* **MyNicVM1**, **myNicVM2** ve **myNicVM3** ağ arabirimine eklendi
* Sanal makine görüntüsü **win2019datacenter**
* **Bölge 1**, **bölge 2** ve **Bölge 3**

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

VM 'Lerin dağıtılması birkaç dakika sürebilir.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Yük dengeleyiciyi oluşturma

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:

* Yük dengeleyicide gelen ağ trafiğini alan bir IP Havuzu.
* İlk havuzun yük dengeli ağ trafiğini gönderdiği ikinci bir IP Havuzu.
* VM örneklerinin sistem durumunu belirleyen bir sistem durumu araştırması.
* Trafiğin VM 'lere nasıl dağıtıldığını tanımlayan bir yük dengeleyici kuralı.

#### <a name="create-the-load-balancer-resource"></a>Yük dengeleyici kaynağı oluşturma

[Az Network lb Create](/cli/azure/network/lb#az_network_lb_create)komutuyla bir genel yük dengeleyici oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Myloadbalancer**
* **Myön uç** adlı bir havuz
* **Mybackendpool** adlı bir havuz
* Sanal ağ **Myvnet** ile ilişkili
* **Mybackendsubnet** alt ağıyla ilişkili

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Durum araştırması oluşturma

Bir sistem durumu araştırması, tüm sanal makine örneklerini denetleyerek ağ trafiği gönderebilecekleri emin olmanızı sağlar. Başarısız araştırma denetimine sahip bir sanal makine yük dengeleyiciden kaldırılır. Hata çözüldüğünde sanal makine yük dengeleyiciye geri eklenir.

[Az Network lb araştırması Create](/cli/azure/network/lb/probe#az_network_lb_probe_create)komutuyla bir sistem durumu araştırması oluşturun. Aşağıdakileri belirtin:

* Sanal makinelerin sistem durumunu izler
* Adlandırılmış **Myhealtharaştırması**
* Protokol **TCP**
* İzleme **bağlantı noktası 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı şunları tanımlar:

* Gelen trafik için IP yapılandırması.
* Trafiği alacak IP Havuzu.
* Gerekli kaynak ve hedef bağlantı noktası. 

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) komutuyla bir yük dengeleyici kuralı oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Myhttprule**
* **Myön uç** havuzundaki **80 numaralı bağlantı noktasını** dinleme
* **80 numaralı bağlantı noktasını** kullanarak **mybackendpool** adres havuzuna yük dengeli ağ trafiği gönderiliyor 
* Sistem durumu araştırması **Myhealtharaştırması** kullanma
* Protokol **TCP**
* **15 dakikalık** boşta zaman aşımı
* TCP sıfırlamayı etkinleştir

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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

#### <a name="add-vms-to-the-load-balancer-pool"></a>Yük dengeleyici havuzuna VM ekleme

[Az Network Nic IP-Config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)komutuyla sanal makineleri arka uç havuzuna ekleyin. Aşağıdakileri belirtin:

* Adres havuzunda **Mybackendpool**
* **Createıntlbqs-RG** kaynak grubunda
* **MyNicVM1**, **myNicVM2** ve **myNicVM3** ağ arabirimiyle ilişkili
* Yük dengeleyici **Myloadbalancer** ile ilişkili

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Temel SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standart SKU yük dengeleyici, üretim iş yükleri için önerilir. SKU 'lar hakkında daha fazla bilgi için bkz. **[Azure Load Balancer SKU 'lar](skus.md)**.

Bu bölümde, sanal makinelerin yükünü dengeleyen bir yük dengeleyici oluşturacaksınız. Bir iç yük dengeleyici oluşturduğunuzda, yük dengeleyici için ağ olarak bir sanal ağ yapılandırılır. Aşağıdaki diyagramda bu hızlı başlangıçta oluşturulan kaynaklar gösterilmektedir:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Hızlı başlangıç için oluşturulan temel yük dengeleyici kaynakları." border="false":::

### <a name="configure-the-virtual-network"></a>Sanal ağı yapılandırma

VM 'Leri dağıtmadan ve yük dengeleyiciyi dağıtmadan önce destekleyici sanal ağ kaynaklarını oluşturun.

#### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[Az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_createt)kullanarak bir sanal ağ oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Myvnet**
* **10.1.0.0/16** adres ön eki
* **Mybackendsubnet** adlı alt ağ
* **10.1.0.0/24** alt ağ öneki
* **Createıntlbqs-RG** kaynak grubunda
* **Eastus** konumu

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Azure savunma ana bilgisayarı için genel bir IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) kullanın. Aşağıdakileri belirtin:

* **Mybastionıp** adlı standart bölge-YEDEKLI genel IP adresi oluşturma
* **Createıntlbqs-RG** içinde

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Azure savunma alt ağı oluşturma

Alt ağ oluşturmak için [az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) kullanın. Aşağıdakileri belirtin:

* **AzureBastionSubnet** adlı
* **10.1.1.0/24** adres ön eki
* Sanal ağ **\ VNET** 'te
* **Createıntlbqs-RG** kaynak grubunda

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Azure Bastion konağı oluşturma

Bir konak oluşturmak için [az ağ savunma oluştur](/cli/azure/network/bastion#az_network_bastion_create) seçeneğini kullanın. Aşağıdakileri belirtin:

* Adlandırılmış **Mybastionhost**
* **Createıntlbqs-RG** içinde
* Ortak IP ile ilişkili **Myeltionıp**
* Sanal ağ **Myvnet** ile ilişkili
* **Eastus** konumunda

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure savunma konağının dağıtılması birkaç dakika sürebilir.

#### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Standart yük dengeleyici için, sanal makinelerinizin bir ağ güvenlik grubuna ait olan ağ arabirimlerine sahip olduğundan emin olun. [Az Network NSG Create](/cli/azure/network/nsg#az_network_nsg_create)kullanarak bir ağ güvenlik grubu oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Mynsg**
* **Createıntlbqs-RG** kaynak grubunda

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Ağ güvenlik grubu kuralı oluşturma

[Az Network NSG Rule Create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)kullanarak bir ağ güvenlik grubu kuralı oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Mynsgrutahttp**
* Önceki adımda oluşturduğunuz ağ güvenlik grubunda, **Mynsg**
* **Createıntlbqs-RG** kaynak grubunda
* Protokol **(*)**
* Yön **geliş**
* Kaynak **(*)**
* Hedef **(*)**
* Hedef bağlantı noktası **80**
* Erişime **Izin ver**
* Öncelik **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
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

### <a name="create-back-end-servers"></a>Arka uç sunucuları oluşturma

Bu bölümde şunları oluşturursunuz:

* Sanal makineler için üç ağ arabirimi.
* Sanal makineler için kullanılabilirlik kümesi.
* Yük Dengeleyici için sunucu olarak kullanılacak üç sanal makine.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Sanal makineler için ağ arabirimleri oluşturma

[Az Network Nic Create](/cli/azure/network/nic#az_network_nic_create)ile üç ağ arabirimi oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **myNicVM1**, **myNicVM2** ve **myNicVM3**
* **Createıntlbqs-RG** kaynak grubunda
* Sanal ağ **\ VNET** 'te
* Alt ağda **Mybackendsubnet**
* Ağ güvenlik grubu ' nda **Mynsg**

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Sanal makineler için kullanılabilirlik kümesi oluşturma

Kullanılabilirlik kümesini [az VM AVAILABILITY-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create)ile oluşturun. Aşağıdakileri belirtin:

* **MyAvailabilitySet** adlı
* **Createıntlbqs-RG** kaynak grubunda
* Konum **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Sanal makineleri oluşturma

[az vm create](/cli/azure/vm#az_vm_create) komutunu kullanarak sanal makineleri oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **myVM1**, **myVM2** ve **myVM3**
* **Createıntlbqs-RG** kaynak grubunda
* **MyNicVM1**, **myNicVM2** ve **myNicVM3** ağ arabirimine eklendi
* Sanal makine görüntüsü **win2019datacenter**
* **MyAvailabilitySet** içinde


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
VM 'Lerin dağıtılması birkaç dakika sürebilir.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>Yük dengeleyiciyi oluşturma

Bu bölümde yük dengeleyicinin aşağıdaki bileşenlerini nasıl oluşturabileceğiniz ve yapılandırabileceğiniz açıklanmaktadır:

* Yük dengeleyicide gelen ağ trafiğini alan bir IP Havuzu.
* İlk havuzun yük dengeli ağ trafiğini gönderdiği ikinci bir IP Havuzu.
* VM örneklerinin sistem durumunu belirleyen bir sistem durumu araştırması.
* Trafiğin VM 'lere nasıl dağıtıldığını tanımlayan bir yük dengeleyici kuralı.

#### <a name="create-the-load-balancer-resource"></a>Yük dengeleyici kaynağı oluşturma

[Az Network lb Create](/cli/azure/network/lb#az_network_lb_create)komutuyla bir genel yük dengeleyici oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Myloadbalancer**
* **Myön uç** adlı bir havuz
* **Mybackendpool** adlı bir havuz
* Sanal ağ **Myvnet** ile ilişkili
* **Mybackendsubnet** alt ağıyla ilişkili

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

#### <a name="create-the-health-probe"></a>Durum araştırması oluşturma

Bir sistem durumu araştırması, tüm sanal makine örneklerini denetleyerek ağ trafiği gönderebilecekleri emin olmanızı sağlar. Başarısız araştırma denetimine sahip bir sanal makine yük dengeleyiciden kaldırılır. Hata çözüldüğünde sanal makine yük dengeleyiciye geri eklenir.

[Az Network lb araştırması Create](/cli/azure/network/lb/probe#az_network_lb_probe_create)komutuyla bir sistem durumu araştırması oluşturun. Aşağıdakileri belirtin:

* Sanal makinelerin sistem durumunu izler
* Adlandırılmış **Myhealtharaştırması**
* Protokol **TCP**
* İzleme **bağlantı noktası 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Yük dengeleyici kuralı oluşturma

Yük dengeleyici kuralı şunları tanımlar:

* Gelen trafik için IP yapılandırması.
* Trafiği alacak IP Havuzu.
* Gerekli kaynak ve hedef bağlantı noktası. 

[az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) komutuyla bir yük dengeleyici kuralı oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Myhttprule**
* **Myön uç** havuzundaki **80 numaralı bağlantı noktasını** dinleme
* **80 numaralı bağlantı noktasını** kullanarak **mybackendpool** adres havuzuna yük dengeli ağ trafiği gönderiliyor 
* Sistem durumu araştırması **Myhealtharaştırması** kullanma
* Protokol **TCP**
* **15 dakikalık** boşta zaman aşımı

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Yük dengeleyici havuzuna VM ekleme

[Az Network Nic IP-Config Address-Pool Add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add)komutuyla sanal makineleri arka uç havuzuna ekleyin. Aşağıdakileri belirtin:

* Adres havuzunda **Mybackendpool**
* **Createıntlbqs-RG** kaynak grubunda
* **MyNicVM1**, **myNicVM2** ve **myNicVM3** ağ arabirimiyle ilişkili
* Yük dengeleyici **Myloadbalancer** ile ilişkili

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---
## <a name="test-the-load-balancer"></a>Yük dengeleyiciyi test etme

[Az Network Nic Create](/cli/azure/network/nic#az_network_nic_create)komutuyla ağ arabirimini oluşturun. Aşağıdakileri belirtin:

* Adlandırılmış **Mynictestvm**
* **Createıntlbqs-RG** kaynak grubunda
* Sanal ağ **\ VNET** 'te
* Alt ağda **Mybackendsubnet**
* Ağ güvenlik grubu ' nda **Mynsg**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
[Az VM Create](/cli/azure/vm#az_vm_create)ile sanal makineyi oluşturun. Aşağıdakileri belirtin:

* **Mytestvm** adlı adlandırılmış
* **Createıntlbqs-RG** kaynak grubunda
* Ağ arabirimine eklendi **Mynictestvm**
* Sanal makine görüntüsü **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Sanal makinenin dağıtılması için birkaç dakika beklemeniz gerekebilir.

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
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Test

1. Azure portalında [oturum açın](https://portal.azure.com).

2. **Genel bakış** sayfasında, yük dengeleyici IÇIN özel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler**  >  **tüm kaynaklar**  >  **myloadbalancer**' ı seçin.

3. **Myloadbalancer** genel görünümünde adresı **özel IP adresi**' nin yanına kopyalayın.

4. Soldaki menüden **tüm hizmetler**  >  **tüm kaynaklar**' ı seçin. Kaynaklar listesinde, **Createıntlbqs-RG** kaynak grubunda, **mytestvm**' yi seçin.

5. **Genel bakış** sayfasında, **Bağlan**' ı seçin  >  .

6. VM oluştururken girdiğiniz kullanıcı adını ve parolayı girin.

7. **Mytestvm**'de **Internet Explorer**'ı açın.

8. Önceki adımdan, tarayıcının adres çubuğuna IP adresini girin. IIS Web sunucusunun varsayılan sayfası tarayıcıda gösterilir.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Tarayıcının adres çubuğundaki IP adresinin ekran görüntüsü." border="true":::
   
Yük dengeleyicinin trafiği üç VM 'ye dağıtmasını görmek için, her bir sanal makinenin IIS Web sunucusunun varsayılan sayfasını özelleştirebilirsiniz. Daha sonra, Web tarayıcınızı istemci makinesinden el ile yenileyin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynaklarınızın artık gerekli olmadığı durumlarda, [az Group Delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu, yük dengeleyiciyi ve tüm ilgili kaynakları kaldırın.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure Load Balancer’a genel bakış bilgilerini edinin.
> [!div class="nextstepaction"]
> [Azure Load Balancer nedir?](load-balancer-overview.md)
