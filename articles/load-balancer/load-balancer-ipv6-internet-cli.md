---
title: IPv6 - Azure CLI ile bir genel yük dengeleyici oluşturma
titleSuffix: Azure Load Balancer
description: Bu öğrenme yoluyla, Azure CLı kullanarak IPv6 ile genel yük dengeleyici oluşturmaya başlayın.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, azure yük dengeleyici, ikili yığın, genel IP, yerel IPv6, mobil veya IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: f7c0eb9bd258d2efbdb4df78f2cf86861f77a975
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076025"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Azure CLI kullanarak IPv6 ile genel yük dengeleyici oluşturma

>[! NOTE: IPv6 için En Iyi uygulamada değişiklik yapın] Bu makalede temel yük dengeleyiciler hem IPv4 hem de IPv6 bağlantısı sağlamasına izin veren bir giriş IPv6 özelliği açıklanır.  Daha kapsamlı IPv6 bağlantısı artık, sanal ağlarınızla IPv6 bağlantısını tümleştiren ve IPv6 ağ güvenlik grubu kuralları, IPv6 Kullanıcı tanımlı yönlendirme, IPv6 temel ve standart yük dengeleme gibi önemli özellikleri ve daha fazlasını içeren [Azure VNET 'ler Için IPv6](../virtual-network/ipv6-overview.md) ile kullanılabilir.  Azure sanal ağları için IPv6, Azure 'daki IPv6 uygulamaları için önerilen en iyi uygulamadır. 
>Lütfen bkz. [Azure VNET CLI dağıtımı Için IPv6](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)

Azure Load Balancer bir Katman 4 (TCP, UDP) yük dengeleyicidir. Yük Dengeleyici, gelen trafiği bulut hizmetlerindeki sağlıklı hizmet örnekleri veya bir yük dengeleyici kümesindeki sanal makineler arasında dağıtarak yüksek kullanılabilirlik sağlar. Yük Dengeleyiciler bu hizmetleri birden çok bağlantı noktası veya birden çok IP adresi veya her ikisini de sunabilir.

## <a name="example-deployment-scenario"></a>Örnek dağıtım senaryosu

Aşağıdaki diyagram, bu makalede açıklanan örnek şablonu kullanılarak dağıtılan çözüm yük dengelemeyi gösterir.

![Yük dengeleyici senaryosu](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Bu senaryoda, aşağıdaki Azure kaynakları oluşturun:

* İki sanal makine (VM)
* Atanmış IPv4 ve IPv6 adresleri ile her VM için bir sanal ağ arabirimi
* Bir genel yük dengeleyiciye bir IPv4 ve IPv6 genel IP adresi
* İki sanal makine içeren kullanılabilirlik kümesi
* İki Yük Dengeleme kuralları, genel VIP özel uç noktalar için eşlemek için

## <a name="deploy-the-solution-by-using-azure-cli"></a>Çözümü Azure CLI kullanarak dağıtma

Aşağıdaki adımlarda, Azure CLI kullanarak herkese açık yük dengeleyici oluşturulacağı gösterilmektedir. CLI kullanarak oluşturun ve her nesneyi ayrı ayrı yapılandırın ve ardından bunları bir kaynak oluşturmak için bir araya yerleştirin.

Yük Dengeleyici dağıtmak için oluşturun ve aşağıdaki nesneleri yapılandırın:

* **Ön uç IP yapılandırmasını**: gelen ağ trafiği için genel IP adreslerini içerir.
* **Arka uç adres havuzu**: sanal makinelerin yük dengeleyiciden ağ trafiği alması için ağ arabirimlerini (NIC'ler) içerir.
* **Yük Dengeleme kuralları**: yük dengeleyici üzerindeki bir genel bağlantı noktasını arka uç adres havuzundaki bağlantı noktasına eşleyen kuralları içerir.
* **Gelen NAT kuralları**: yük dengeleyici üzerindeki bir genel bağlantı noktasını arka uç adres havuzundaki belirli bir sanal makineye ait bağlantı noktasına eşlenen ağ adresi çevirisi (NAT) kuralları içerir.
* **Araştırmalar**: arka uç adres havuzundaki sanal makine örneklerinin kullanılabilirliğini kontrol etmek için kullanılan durum araştırmalarını içerir.

## <a name="set-up-azure-cli"></a>Azure CLI'yı ayarlama

Bu örnekte, bir PowerShell komut penceresinde Azure CLI araçları çalıştırın. Okunabilirlik ve yeniden artırmak için PowerShell'in yetileri, Azure PowerShell cmdlet'lerini kullanın.

1. [Azure CLI'yi yükleme ve yapılandırma](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure hesabınız için bağlantılı makaledeki adımları ve oturum açma izleyerek.

2. Azure CLI komutları ile kullanmak için PowerShell değişkenleri ayarlayın:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Bir kaynak grubu, bir yük dengeleyici, sanal ağ ve alt ağlar oluşturun

1. Kaynak grubu oluşturun:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Yük Dengeleyici oluşturma:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Sanal ağ oluşturma:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Bu sanal ağda iki alt ağ oluşturun:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Ön uç havuzu için genel IP adresi oluşturma

1. PowerShell değişkenleri ayarlayın:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Ön uç IP havuzu için genel bir IP adresi oluşturun:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Yük Dengeleyici tam etki alanı adı (FQDN) olarak genel IP'nin etki alanı etiketini kullanır. Bu bulut hizmeti kullanan Klasik dağıtımdan bir değişiklik olarak adlandırın yük dengeleyici FQDN olarak.
    >
    > Bu örnekte FQDN: *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Ön uç ve arka uç havuzları oluşturma

Bu bölümde, aşağıdaki IP havuzlarını oluşturun:
* Yük dengeleyicideki gelen ağ trafiğini alan ön uç IP havuzu.
* Burada ön uç havuzunun Yük Dengelemesi yapılmış ağ trafiğini gönderdiği arka uç IP havuzu.

1. PowerShell değişkenleri ayarlayın:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Ön uç IP havuzu oluşturun ve yük dengeleyici ve önceki adımda oluşturduğunuz genel IP ile ilişkilendirin.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>NAT kuralları, araştırma oluşturmak ve yük dengeleyici kuralları

Bu örnek aşağıdaki nesneleri oluşturur:

* Bağlantı 80 numaralı TCP bağlantı noktasını denetlemek için bir araştırma kuralı.
* RDP için bağlantı noktası 3389 3389 numaralı bağlantı noktasına gelen tüm trafiği yönlendiren NAT kuralı.\*
* Uzak Masaüstü Protokolü (RDP) için 3389 numaralı bağlantı noktasına 3391 numaralı bağlantı noktasına gelen tüm trafiği yönlendiren NAT kuralı.\*
* Bağlantı noktası 80 arka uç havuzundaki adreslerin 80 numaralı bağlantı noktasına gelen tüm trafiği dengelemek için bir yük dengeleyici kuralı.

\* NAT kuralları, yük dengeleyicinin arkasındaki belirli bir sanal makine örneğiyle ilişkilendirilmiş. Belirli bir sanal makine ve NAT kuralıyla ilişkilendirilmiş bağlantı noktası 3389 numaralı bağlantı noktasına ulaşan ağ trafiğini gönderilir. NAT kuralı için bir protokol (UDP veya TCP) belirtmeniz gerekir. Her iki protokolü aynı bağlantı noktasına atayamazsınız.

1. PowerShell değişkenleri ayarlayın:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Araştırması oluşturun.

    Aşağıdaki örnek, her 15 saniyede denetleyen bir TCP araştırması için arka uç TCP bağlantı noktası 80 bağlantı oluşturur. İki ardışık hatasından sonra arka uç kaynağı kullanılamaz olarak işaretler.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Arka uç kaynaklarına RDP bağlantılarına izin veren gelen NAT kurallarını oluşturun:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Bir istek aldı ön uç bağlı olarak farklı arka uç noktalarına giden trafik bir yük dengeleyici kuralları oluşturun.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Ayarlarınızı kontrol edin:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Beklenen çıktı:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>NIC’leri oluşturma

NIC'leri oluşturma ve bunları NAT kuralları, yük dengeleyici kuralları ve araştırmalarla ilişkilendirin.

1. PowerShell değişkenleri ayarlayın:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Her arka ucu için bir NIC oluşturup ve IPv6 yapılandırmasını ekleyin:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Arka uç VM kaynakları oluşturmak ve her NIC ekleme

Sanal makineler oluşturmak için bir depolama hesabı olmalıdır. Yük Dengeleme için Vm'leri bir kullanılabilirlik kümesi üyesi olmanız gerekir. Sanal makineleri oluşturma hakkında daha fazla bilgi için bkz. [PowerShell kullanarak bir Azure VM'si oluşturma](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. PowerShell değişkenleri ayarlayın:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Bu örnek düz metin olarak sanal makineleri için kullanıcı adı ve parola kullanır. Bu kimlik bilgilerini düz metin olarak kullandığınızda, uygun dikkat edin. Daha güvenli bir yöntem PowerShell kimlik bilgilerini işleme bilgi için bkz: [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) cmdlet'i.

2. Kullanılabilirlik kümesi oluşturun:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Sanal makineler ile ilişkili NIC oluşturun:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```


