---
title: Azure sanal makineler için birden çok IP adresi-PowerShell | Microsoft Docs
description: PowerShell kullanarak bir sanal makineye birden çok IP adresi atamayı öğrenin. | Kaynak Yöneticisi
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: e9bad6ad614855c543ee6d75d4e6f4dc8e2255aa
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876217"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>PowerShell kullanarak sanal makinelere birden çok IP adresi atama

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Bu makalede, PowerShell kullanarak Azure Resource Manager dağıtım modeli aracılığıyla bir sanal makinenin (VM) nasıl oluşturulacağı açıklanmaktadır. Klasik dağıtım modeliyle oluşturulan kaynaklara birden çok IP adresi atanamaz. Azure dağıtım modelleri hakkında daha fazla bilgi edinmek için [dağıtım modellerini anlama](../resource-manager-deployment-model.md) makalesini okuyun.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Birden çok IP adresi olan bir VM oluşturma

Aşağıdaki adımlarda, senaryoda açıklandığı gibi birden çok IP adresi ile örnek VM oluşturma açıklanmaktadır. Değer değerlerini uygulamanız için gereken şekilde değiştirin.

1. Bir PowerShell komut istemi açın ve bu bölümün içindeki kalan adımları tek bir PowerShell oturumunda doldurun. Zaten PowerShell 'i yükleyip yapılandırdıysanız, [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview) makalesindeki adımları uygulayın.
2. `Connect-AzAccount` Komutuyla hesabınızda oturum açın.
3. *Myresourcegroup* ve *westus* değerini, seçtiğiniz bir ad ve konum ile değiştirin. Bir kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Kaynak grubuyla aynı konumda bir sanal ağ (VNet) ve alt ağ oluşturun:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Bir ağ güvenlik grubu (NSG) ve bir kural oluşturun. NSG, gelen ve giden kurallarını kullanarak VM 'nin güvenliğini sağlar. Bu durumda, bağlantı noktası 3389 için gelen masaüstü bağlantılarına izin veren bir gelen kuralı oluşturulur.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. NIC için birincil IP yapılandırmasını tanımlayın. Daha önce tanımlanan değeri kullanmıyorsanız, 10.0.0.4 öğesini oluşturduğunuz alt ağda geçerli bir adrese değiştirin. Statik bir IP adresi atamadan önce, ilk olarak zaten kullanımda olmadığından emin olmanız önerilir. Komutunu `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`girin. Adres kullanılabiliyorsa, çıkış *true*değerini döndürür. Kullanılabilir değilse, çıkış *yanlış* değerini ve kullanılabilir adreslerin bir listesini döndürür. 

    Aşağıdaki komutlarda, replace- **WITH \<-Unique-name > değerini kullanılacak benzersiz DNS adıyla değiştirin.** Ad, bir Azure bölgesi içindeki tüm genel IP adresleri arasında benzersiz olmalıdır. Bu, isteğe bağlı bir parametredir. Yalnızca genel IP adresini kullanarak VM 'ye bağlanmak istiyorsanız, bu, kaldırılabilir.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Bir NIC 'ye birden çok IP yapılandırması atadığınızda, tek bir yapılandırmanın *-birincil*olarak atanması gerekir.

    > [!NOTE]
    > Genel IP adreslerinin nominal bir ücreti vardır. IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını okuyun. Bir abonelikte kullanılabilecek genel IP adresi sayısı için bir sınır vardır. Sınırlar hakkında daha fazla bilgi için [Azure limitleri](../azure-subscription-service-limits.md#networking-limits) makalesini okuyun.

7. NIC için ikincil IP yapılandırmasını tanımlayın. Gerektiğinde, yapılandırma ekleyebilir veya kaldırabilirsiniz. Her IP yapılandırmasına atanmış bir özel IP adresi olmalıdır. Her yapılandırmaya, isteğe bağlı olarak bir genel IP adresi atanabilir.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. NIC 'i oluşturun ve üç IP yapılandırmasını bu ile ilişkilendirin:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Bu makaledeki tüm konfigürasyonlar bir NIC 'ye atansa da, VM 'ye bağlı her NIC 'ye birden çok IP yapılandırması atayabilirsiniz. Birden çok NIC ile VM oluşturma hakkında bilgi edinmek için [birden çok NIC Ile VM oluşturma](../virtual-machines/windows/multiple-nics.md) makalesini okuyun.

9. Aşağıdaki komutları girerek VM 'yi oluşturun:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Bu makalenin [BIR VM işletim SISTEMINE IP adresleri Ekle](#os-config) bölümünde işletim sisteminizin ADıMLARıNı tamamlayarak VM işletim SISTEMINE özel IP adreslerini ekleyin. Genel IP adreslerini işletim sistemine eklemeyin.

## <a name="add"></a>VM 'ye IP adresleri ekleme

Aşağıdaki adımları tamamlayarak Azure ağ arabirimine özel ve genel IP adresleri ekleyebilirsiniz. Aşağıdaki bölümlerde yer alan örneklerde, bu makaledeki [senaryoda](#scenario) AÇıKLANAN üç IP yapılandırmasına sahıp bir VM zaten var, ancak bunu yapmanız gerekli değildir.

1. Bir PowerShell komut istemi açın ve bu bölümün içindeki kalan adımları tek bir PowerShell oturumunda doldurun. Zaten PowerShell 'i yükleyip yapılandırdıysanız, [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview) makalesindeki adımları uygulayın.
2. Aşağıdaki $Variables "Values" değerini, IP adresi eklemek istediğiniz NIC 'nin adı ve NIC 'nin bulunduğu kaynak grubu ve konuma değiştirin:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Değiştirmek istediğiniz NIC 'in adını bilmiyorsanız, aşağıdaki komutları girin ve ardından önceki değişkenlerin değerlerini değiştirin:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Aşağıdaki komutu yazarak bir değişken oluşturun ve mevcut NIC 'ye ayarlayın:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Aşağıdaki komutlarda, *Myvnet* ve *mysubnet* ' i, NIC 'nin bağlandığı VNET ve alt ağ adlarına değiştirin. NIC 'nin bağlandığı VNet ve alt ağ nesnelerini almak için komutları girin:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   NIC 'nin bağlı olduğu VNet veya alt ağ adını bilmiyorsanız, aşağıdaki komutu girin:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Çıktıda aşağıdaki örnek çıktıya benzer bir metin arayın:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Bu çıkışta, *Myvnet* VNET ve *MYSUBNET* , NIC 'nin bağlı olduğu alt ağ olur.

5. Gereksinimlerinize göre aşağıdaki bölümlerden birindeki adımları doldurun:

   **Özel IP adresi ekle**

   Bir NIC 'ye özel bir IP adresi eklemek için bir IP yapılandırması oluşturmanız gerekir. Aşağıdaki komut, kısmına 10.0.0.7 statik IP adresine sahip bir yapılandırma oluşturur. Statik bir IP adresi belirtirken, alt ağ için kullanılmayan bir adres olmalıdır. `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` Komutu girerek kullanılabilir olduğundan emin olmak için ilk olarak adresi test etmeniz önerilir. IP adresi kullanılabiliyorsa, çıkış *true*değerini döndürür. Kullanılabilir değilse, çıkış *false*değerini ve kullanılabilir adreslerin bir listesini döndürür.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Benzersiz yapılandırma adlarını ve özel IP adreslerini (statik IP adresleri olan yapılandırmalar için) kullanarak, ihtiyacınız olan sayıda yapılandırma oluşturun.

   Bu makalenin [BIR VM işletim SISTEMINE IP adresleri Ekle](#os-config) bölümünde işletim sisteminizin ADıMLARıNı tamamlayarak VM işletim SISTEMINE özel IP adresini ekleyin.

   **Genel IP adresi ekleme**

   Genel IP adresi kaynağı yeni bir IP yapılandırmasına veya var olan bir IP yapılandırmasına ilişkilendirerek genel bir IP adresi eklenir. İhtiyaç duyduğunuz gibi, izleyen bölümlerden birindeki adımları tamamlayın.

   > [!NOTE]
   > Genel IP adreslerinin nominal bir ücreti vardır. IP adresi fiyatlandırması hakkında daha fazla bilgi edinmek için [IP adresi fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses) sayfasını okuyun. Bir abonelikte kullanılabilecek genel IP adresi sayısı için bir sınır vardır. Sınırlar hakkında daha fazla bilgi için [Azure limitleri](../azure-subscription-service-limits.md#networking-limits) makalesini okuyun.
   >

   **Genel IP adresi kaynağını yeni bir IP yapılandırmasıyla ilişkilendir**

   Yeni bir IP yapılandırmasına genel IP adresi eklediğinizde, tüm IP yapılandırmalarının özel bir IP adresi olması gerektiğinden özel bir IP adresi de eklemeniz gerekir. Var olan bir genel IP adresi kaynağı ekleyebilir veya yeni bir tane oluşturabilirsiniz. Yeni bir tane oluşturmak için aşağıdaki komutu girin:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Statik bir özel IP adresi ve ilişkili *myPublicIp3* genel IP adresi kaynağı ile yenı bir IP yapılandırması oluşturmak için aşağıdaki komutu girin:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Genel IP adresi kaynağını mevcut bir IP yapılandırmasıyla ilişkilendir**

   Genel IP adresi kaynağı, yalnızca bir tane ilişkilendirilmiş olmayan bir IP yapılandırmasıyla ilişkilendirilebilir. Aşağıdaki komutu girerek bir IP yapılandırmasının ilişkili bir genel IP adresine sahip olup olmadığını belirleyebilirsiniz:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Aşağıdakine benzer bir çıktı görürsünüz:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   *Ipconfig-3* Için **publicıpaddress** sütunu boş olduğundan, şu anda onunla ilişkili genel IP adresi kaynağı yok. Ipconfig-3 ' e var olan bir genel IP adresi kaynağı ekleyebilir veya bir tane oluşturmak için aşağıdaki komutu girebilirsiniz:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Genel IP adresi kaynağını *Ipconfig-3*ADLı mevcut IP yapılandırması ile ilişkilendirmek için aşağıdaki komutu girin:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Aşağıdaki komutu girerek NIC 'ı yeni IP yapılandırması ile ayarlayın:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Aşağıdaki komutu girerek, NIC 'ye atanan özel IP adreslerini ve genel IP adresi kaynaklarını görüntüleyin:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Bu makalenin [BIR VM işletim SISTEMINE IP adresleri Ekle](#os-config) bölümünde işletim sisteminizin ADıMLARıNı tamamlayarak VM işletim SISTEMINE özel IP adresini ekleyin. Genel IP adresini işletim sistemine eklemeyin.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]