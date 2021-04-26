---
title: Azure CLı kullanarak birden çok IP adresi olan VM
titlesuffix: Azure Virtual Network
description: Azure komut satırı arabirimi 'ni (CLı) kullanarak bir sanal makineye birden çok IP adresi atamayı öğrenin.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: allensu
ms.openlocfilehash: 0b5e8f28be9960a2bb4e1406a293ab5e03962681
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680578"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Azure CLı kullanarak sanal makinelere birden çok IP adresi atama

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Bu makalede, Azure CLı kullanarak Azure Resource Manager dağıtım modeli aracılığıyla bir sanal makinenin (VM) nasıl oluşturulacağı açıklanmaktadır. Klasik dağıtım modeliyle oluşturulan kaynaklara birden çok IP adresi atanamaz. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için [dağıtım modellerini anlama](../azure-resource-manager/management/deployment-models.md) makalesini okuyun.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Birden çok IP adresi ile VM oluşturma

Aşağıdaki adımlarda, senaryoda açıklandığı gibi birden çok IP adresi ile örnek bir sanal makine oluşturma açıklanmaktadır. "" Ve IP adresi türlerindeki değişken değerlerini, uygulamanız için gereken şekilde değiştirin. 

1. Henüz yüklenmemişse [Azure CLI](/cli/azure/install-azure-cli) 'yı yükleme.
2. [Linux VM 'ler IÇIN SSH ortak ve özel anahtar çifti oluşturma](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)bölümündeki adımları tamamlayarak Linux VM 'ler için SSH ortak ve özel anahtar çifti oluşturun.
3. Komut kabuğundan komutuyla oturum açın `az login` ve kullanmakta olduğunuz aboneliği seçin.
4. Bir Linux veya Mac bilgisayarda aşağıdaki betiği yürüterek VM 'yi oluşturun. Betik bir kaynak grubu, bir sanal ağ (VNet), üç IP yapılandırmasına sahip bir NIC ve kendisine bağlı iki NIC içeren bir VM oluşturur. NIC, genel IP adresi, sanal ağ ve VM kaynaklarının hepsi aynı konum ve abonelikte bulunmalıdır. Kaynakların tümünün aynı kaynak grubunda mevcut olması gerekmese de, aşağıdaki betikte.

```azurecli
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Bir NIC ile 3 IP yapılandırmasına sahip bir VM oluşturmaya ek olarak, betik şunu oluşturur:

- Varsayılan olarak tek bir Premium yönetilen disk, ancak oluşturabileceğiniz disk türü için başka seçenekleriniz vardır. Ayrıntılar için [Azure CLI 'yı kullanarak LINUX VM oluşturma](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesini okuyun.
- Bir alt ağa ve iki genel IP adresine sahip bir sanal ağ. Alternatif olarak, *var olan* sanal ağ, alt ağ, NIC veya genel IP adresi kaynaklarını kullanabilirsiniz. Ek kaynaklar oluşturmak yerine var olan ağ kaynaklarını nasıl kullanacağınızı öğrenmek için, girin `az vm create -h` .

Genel IP adreslerinin nominal bir ücreti vardır. IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını okuyun. Bir abonelikte kullanılabilecek genel IP adresi sayısı için bir sınır vardır. Sınırlar hakkında daha fazla bilgi için [Azure limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) makalesini okuyun.

VM oluşturulduktan sonra, `az network nic show --name MyNic1 --resource-group myResourceGroup` NIC yapılandırmasını görüntülemek için komutunu girin. `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table`NIC ile ILIŞKILI IP yapılandırmalarının listesini görüntülemek için yazın.

Bu makalenin [BIR VM işletim SISTEMINE IP adresleri Ekle](#os-config) bölümünde işletim sisteminizin ADıMLARıNı tamamlayarak VM işletim SISTEMINE özel IP adreslerini ekleyin.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>VM 'ye IP adresleri ekleme

Aşağıdaki adımları tamamlayarak mevcut bir Azure ağ arabirimine ek özel ve genel IP adresleri ekleyebilirsiniz. Örnekler, bu makalede açıklanan [senaryoya](#scenario) göre derleme yapılır.

1. Bir komut kabuğu açın ve bu bölümde yer alan kalan adımları tek bir oturum içinde doldurun. Azure CLı yüklü ve yapılandırılmış değilse, [Azure CLI yükleme](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) makalesindeki adımları uygulayın ve komutuyla Azure hesabınızda oturum açın `az-login` .

2. Gereksinimlerinize göre aşağıdaki bölümlerden birindeki adımları doldurun:

    **Özel IP adresi ekle**
    
    Bir NIC 'ye özel bir IP adresi eklemek için, aşağıdaki komutu kullanarak bir IP yapılandırması oluşturmanız gerekir. Statik IP adresi, alt ağ için kullanılmamış bir adres olmalıdır.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Benzersiz yapılandırma adlarını ve özel IP adreslerini (statik IP adresleri olan yapılandırmalar için) kullanarak, ihtiyacınız olan sayıda yapılandırma oluşturun.

    **Genel IP adresi ekleme**
    
    Genel bir IP adresi, yeni bir IP yapılandırmasıyla ya da var olan bir IP yapılandırmasıyla ilişkilendirerek eklenir. İhtiyaç duyduğunuz gibi, izleyen bölümlerden birindeki adımları tamamlayın.

    Genel IP adreslerinin nominal bir ücreti vardır. IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını okuyun. Bir abonelikte kullanılabilecek genel IP adresi sayısı için bir sınır vardır. Sınırlar hakkında daha fazla bilgi için [Azure limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) makalesini okuyun.

    - **Kaynağı yeni bir IP yapılandırmasıyla ilişkilendir**
    
        Yeni bir IP yapılandırmasına genel IP adresi eklediğinizde, tüm IP yapılandırmalarının özel bir IP adresi olması gerektiğinden özel bir IP adresi de eklemeniz gerekir. Var olan bir genel IP adresi kaynağı ekleyebilir veya yeni bir tane oluşturabilirsiniz. Yeni bir tane oluşturmak için aşağıdaki komutu girin:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Statik bir özel IP adresi ve ilişkili *myPublicIP3* genel IP adresi kaynağı ile yenı bir IP yapılandırması oluşturmak için aşağıdaki komutu girin:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Kaynağı var olan BIR IP yapılandırmasıyla ilişkilendir** Genel IP adresi kaynağı, yalnızca bir tane ilişkilendirilmiş olmayan bir IP yapılandırmasıyla ilişkilendirilebilir. Aşağıdaki komutu girerek bir IP yapılandırmasının ilişkili bir genel IP adresine sahip olup olmadığını belirleyebilirsiniz:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Döndürülen çıkış:
    
        ```output
        Name        PublicIpAddressId
        
        ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3
        ```

        *Ipconfig-3* Için **Publicıpaddressıd** sütunu çıktıda boş olduğundan, şu anda onunla ilişkili genel IP adresi kaynağı yok. Ipconfig-3 ' e var olan bir genel IP adresi kaynağı ekleyebilir veya bir tane oluşturmak için aşağıdaki komutu girebilirsiniz:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Genel IP adresi kaynağını *Ipconfig-3* ADLı mevcut IP yapılandırması ile ilişkilendirmek için aşağıdaki komutu girin:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Aşağıdaki komutu girerek, NIC 'ye atanan özel IP adreslerini ve genel IP adresi kaynak kimliklerini görüntüleyin:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Döndürülen çıkış: <br>

    ```output
    Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
    
    ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```
    
4. Bu makalenin [BIR VM işletim SISTEMINE IP adresleri ekleme](#os-config) bölümündeki yönergeleri IZLEYEREK, NIC 'ye EKLEDIĞINIZ özel IP adreslerini VM işletim sistemine ekleyin. Genel IP adreslerini işletim sistemine eklemeyin.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]