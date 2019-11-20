---
title: Azure HDInsight kümeleri için sanal ağlar oluşturma
description: HDInsight 'ı diğer bulut kaynaklarına veya veri merkezinizdeki kaynaklara bağlamak için bir Azure sanal ağı oluşturmayı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 6fd23e3d41dda15b1ec439c1e8b02073722b8871
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71073628"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Azure HDInsight kümeleri için sanal ağlar oluşturma

Bu makalede, Azure HDInsight kümeleriyle kullanılmak üzere [Azure sanal ağları](../virtual-network/virtual-networks-overview.md) oluşturmak ve yapılandırmak için örnekler ve kod örnekleri sağlanmaktadır. Ağ güvenlik grupları (NSG 'ler) oluşturma ve DNS yapılandırma hakkında ayrıntılı örnekler sunulmaktadır. 

Azure HDInsight ile sanal ağları kullanma hakkında arka plan bilgileri için bkz. [Azure HDInsight için bir sanal ağ planlayın](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Kod örnekleri ve örnekler için Önkoşullar

Bu makaledeki kod örneklerinin hiçbirini yürütmeden önce, OU, TCP/IP ağ iletişimi 'nin anlaşılmasına sahip olmalıdır. TCP/IP ağı hakkında bilgi sahibi değilseniz, üretim ağlarında değişiklik yapmadan önce bir kişiye başvurun.

Bu makaledeki örneklere yönelik diğer Önkoşullar şunlardır:

* PowerShell kullanıyorsanız, [az Module](https://docs.microsoft.com/powershell/azure/overview)'ü yüklemeniz gerekecektir.
* Azure CLı 'yi kullanmak istiyorsanız ve henüz yüklemediyseniz, bkz. [Azure CLI 'Yı yükleme](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Azure sanal ağını kullanarak HDInsight 'ı şirket içi ağınıza bağlamak için adım adım yönergeler arıyorsanız, bkz. HDInsight 'ı Şirket [içi ağ belgenize bağlama](connect-on-premises-network.md) .

## <a id="hdinsight-nsg"></a>Örnek: HDInsight ile ağ güvenlik grupları

Bu bölümdeki örneklerde, HDInsight 'ın Azure yönetim hizmetleriyle iletişim kurmasına izin veren ağ güvenlik grubu kurallarının nasıl oluşturulacağı gösterilmektedir. Örnekleri kullanmadan önce, IP adreslerini kullandığınız Azure bölgesi için olanlarla eşleşecek şekilde ayarlayın. Bu bilgileri [HDInsight YÖNETIM IP adreslerinde](hdinsight-management-ip-addresses.md)bulabilirsiniz.

### <a name="azure-resource-management-template"></a>Azure Kaynak Yönetimi şablonu

Aşağıdaki kaynak yönetimi şablonu, gelen trafiği kısıtlayan bir sanal ağ oluşturur, ancak HDInsight için gereken IP adreslerinden gelen trafiğe izin verir. Bu şablon, sanal ağda bir HDInsight kümesi de oluşturur.

* [Güvenli bir Azure sanal ağı ve bir HDInsight Hadoop kümesi dağıtma](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Gelen trafiği kısıtlayan ve Kuzey Avrupa bölgenin IP adreslerinden gelen trafiğe izin veren bir sanal ağ oluşturmak için aşağıdaki PowerShell betiğini kullanın.

> [!IMPORTANT]  
> Bu örnekte ve `hdirule1` `hdirule2` için IP adreslerini, kullanmakta olduğunuz Azure bölgesiyle eşleşecek şekilde değiştirin. Bu bilgi [HDInsight YÖNETIM IP adreslerini](hdinsight-management-ip-addresses.md)bulabilirsiniz.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Bu örnek, gerekli IP adreslerinde gelen trafiğe izin vermek üzere kuralların nasıl ekleneceğini gösterir. Diğer kaynaklardan gelen erişimi kısıtlamak için bir kural içermez. Aşağıdaki kod, Internet 'ten SSH erişiminin nasıl etkinleştirileceğini göstermektedir:

```powershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Gelen trafiği kısıtlayan bir sanal ağ oluşturmak için aşağıdaki adımları kullanın, ancak HDInsight için gereken IP adreslerinden gelen trafiğe izin verir.

1. Adlı `hdisecure`yeni bir ağ güvenlik grubu oluşturmak için aşağıdaki komutu kullanın. Azure `RESOURCEGROUP` sanal ağını içeren kaynak grubuyla değiştirin. Grubun `LOCATION` oluşturulduğu konum (bölge) ile değiştirin.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Grup oluşturulduktan sonra, yeni grup hakkında bilgi alırsınız.

2. Azure HDInsight sistem durumu ve yönetim hizmetinden bağlantı noktası 443 üzerinde gelen iletişime izin veren yeni ağ güvenlik grubuna kurallar eklemek için aşağıdakileri kullanın. Azure `RESOURCEGROUP` sanal ağını içeren kaynak grubunun adıyla değiştirin.

    > [!IMPORTANT]  
    > Bu örnekte ve `hdirule1` `hdirule2` için IP adreslerini, kullanmakta olduğunuz Azure bölgesiyle eşleşecek şekilde değiştirin. Bu bilgileri [HDInsight YÖNETIM IP adreslerinde](hdinsight-management-ip-addresses.md)bulabilirsiniz.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Bu ağ güvenlik grubunun benzersiz tanımlayıcısını almak için aşağıdaki komutu kullanın:

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Bu komut aşağıdaki metne benzer bir değer döndürür:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. Ağ güvenlik grubunu bir alt ağa uygulamak için aşağıdaki komutu kullanın. `GUID` Ve`RESOURCEGROUP` değerlerini, önceki adımdan geri dönenler ile değiştirin. Ve `VNETNAME` ' `SUBNETNAME` i oluşturmak istediğiniz sanal ağ adı ve alt ağ adıyla değiştirin.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Bu komut tamamlandıktan sonra HDInsight 'ı sanal ağa yükleyebilirsiniz.


Bu adımlar yalnızca Azure bulutundaki HDInsight sistem durumu ve Yönetim hizmetine erişimi açar. Sanal Ağ dışından HDInsight kümesine herhangi bir erişim engellenir. Sanal Ağ dışından erişimi etkinleştirmek için, ek ağ güvenlik grubu kuralları eklemeniz gerekir.

Aşağıdaki kod, Internet 'ten SSH erişiminin nasıl etkinleştirileceğini göstermektedir:

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a id="example-dns"></a>Örneğinde DNS yapılandırması

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Bir sanal ağ ile bağlı şirket içi ağ arasında ad çözümlemesi

Bu örnekte aşağıdaki varsayımlar yapılır:

* VPN ağ geçidi kullanarak şirket içi ağa bağlı bir Azure sanal ağınız vardır.

* Sanal ağdaki özel DNS sunucusu işletim sistemi olarak Linux veya UNIX çalıştırıyor.

* [Bağlama](https://www.isc.org/downloads/bind/) , özel DNS sunucusuna yüklenir.

Sanal ağdaki özel DNS sunucusunda:

1. Sanal ağın DNS sonekini bulmak için Azure PowerShell ya da Azure CLı kullanın:

    Öğesini `RESOURCEGROUP` sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Sanal ağ için özel DNS sunucusunda, `/etc/bind/named.conf.local` dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Değeri, `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` sanal ağınızın DNS son eki ile değiştirin.

    Bu yapılandırma, sanal ağın DNS son ekine yönelik tüm DNS isteklerini Azure özyinelemeli çözümleyici 'ye yönlendirir.

2. Sanal ağ için özel DNS sunucusunda, `/etc/bind/named.conf.options` dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Değeri, `10.0.0.0/16` sanal Ağınızın IP adresi aralığıyla değiştirin. Bu giriş, bu aralıktaki ad çözümleme isteği adreslerine izin verir.

    * Şirket içi ağın `acl goodclients { ... }` IP adresi aralığını bölümüne ekleyin.  Giriş, şirket içi ağdaki kaynaklardan ad çözümleme isteklerine izin verir.
    
    * Değeri `192.168.0.1` , şirket içi DNS sunucunuzun IP adresi ile değiştirin. Bu giriş, diğer tüm DNS isteklerini şirket içi DNS sunucusuna yönlendirir.

3. Yapılandırmayı kullanmak için, bağla ' yı yeniden başlatın. Örneğin: `sudo service bind9 restart`.

4. Şirket içi DNS sunucusuna koşullu iletici ekleyin. Koşullu ileticiyi, 1. adımdan özel DNS sunucusuna DNS son eki için istek gönderecek şekilde yapılandırın.

    > [!NOTE]  
    > Koşullu iletici ekleme hakkında daha fazla bilgi için DNS yazılımınıza yönelik belgelere başvurun.

Bu adımları tamamladıktan sonra, tam etki alanı adlarını (FQDN) kullanarak herhangi bir ağdaki kaynaklara bağlanabilirsiniz. Artık HDInsight 'ı sanal ağa yükleyebilirsiniz.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>İki bağlı sanal ağ arasında ad çözümlemesi

Bu örnekte aşağıdaki varsayımlar yapılır:

* VPN ağ geçidi veya eşleme kullanılarak bağlı iki Azure sanal ağınız vardır.

* Her iki ağdaki özel DNS sunucusu işletim sistemi olarak Linux veya UNIX çalıştırıyor.

* [Bağlama](https://www.isc.org/downloads/bind/) , özel DNS sunucularına yüklenir.

1. Her iki sanal ağın da DNS sonekini bulmak için Azure PowerShell veya Azure CLı kullanın:

    Öğesini `RESOURCEGROUP` sanal ağı içeren kaynak grubunun adıyla değiştirin ve ardından şu komutu girin:

    ```powershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Özel DNS sunucusunda `/etc/bind/named.config.local` dosyanın içeriği olarak aşağıdaki metni kullanın. Bu değişikliği, her iki sanal ağdaki özel DNS sunucusunda yapın.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Değeri, `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` __diğer__ sanal ağın DNS son eki ile değiştirin. Bu giriş, uzak ağın DNS son ekine yönelik istekleri o ağdaki özel DNS 'e yönlendirir.

3. Her iki sanal ağdaki özel DNS sunucularında, `/etc/bind/named.conf.options` dosyanın içeriği olarak aşağıdaki metni kullanın:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   `10.0.0.0/16` Ve`10.1.0.0/16` değerlerini sanal ağlarınızın IP adresi aralıklarıyla değiştirin. Bu giriş, her ağdaki kaynakların DNS sunucularına istek yapmasına izin verir.

    Sanal ağların DNS sonekleri (örneğin, microsoft.com) için olmayan istekler Azure özyinelemeli çözümleyici tarafından işlenir.

4. Yapılandırmayı kullanmak için, bağla ' yı yeniden başlatın. Örneğin, `sudo service bind9 restart` her iki DNS sunucusunda.

Bu adımları tamamladıktan sonra, tam etki alanı adlarını (FQDN) kullanarak sanal ağdaki kaynaklara bağlanabilirsiniz. Artık HDInsight 'ı sanal ağa yükleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* HDInsight 'ı şirket içi ağa bağlanacak şekilde yapılandırmaya yönelik uçtan uca bir örnek için bkz. HDInsight 'ı Şirket [içi ağa bağlama](./connect-on-premises-network.md).
* Azure sanal ağlarında Apache HBase kümelerini yapılandırmak için bkz. [Azure sanal ağ 'Da HDInsight 'Ta Apache HBase kümeleri oluşturma](hbase/apache-hbase-provision-vnet.md).
* Apache HBase Coğrafi çoğaltmayı yapılandırmak için bkz. [Azure sanal ağlarında Apache HBase küme çoğaltmasını ayarlama](hbase/apache-hbase-replication.md).
* Azure sanal ağları hakkında daha fazla bilgi için bkz. [Azure sanal ağına genel bakış](../virtual-network/virtual-networks-overview.md).

* Ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [ağ güvenlik grupları](../virtual-network/security-overview.md).

* Kullanıcı tanımlı rotalar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı rotalar ve IP iletimi](../virtual-network/virtual-networks-udr-overview.md).
