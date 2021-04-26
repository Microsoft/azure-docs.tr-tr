---
title: Genel IP adresini bir sanal makineyle ilişkilendir
titlesuffix: Azure Virtual Network
description: Azure portal veya Azure CLı kullanarak bir sanal makine (VM) ile genel IP adresi ilişkilendirmeyi öğrenin.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 6e8fe92f88a5934c55febf42a0768274211ed76f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767718"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Genel IP adresini bir sanal makineyle ilişkilendir

Bu makalede, genel IP adresini var olan bir sanal makineyle (VM) ilişkilendirmeyi öğreneceksiniz. Bir sanal makineye internet 'ten bağlanmak istiyorsanız, VM ile ilişkili bir genel IP adresi olmalıdır. Genel IP adresi ile yeni bir VM oluşturmak istiyorsanız, [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), [Azure komut SATıRı arabirimi (CLI)](virtual-network-deploy-static-pip-arm-cli.md)veya [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)'i kullanarak bunu yapabilirsiniz. Genel IP adreslerinin nominal bir ücreti vardır. Ayrıntılar için bkz. [fiyatlandırma](https://azure.microsoft.com/pricing/details/ip-addresses/). Abonelik başına kullanabileceğiniz genel IP adresi sayısı için bir sınır vardır. Ayrıntılar için bkz. [sınırlar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Genel IP adresini bir VM ile ilişkilendirmek için [Azure Portal](#azure-portal), Azure [komut satırı arabirimi](#azure-cli) 'ni (CLI) veya [PowerShell](#powershell) 'i kullanabilirsiniz.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Azure portalı

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Genel IP adresini eklemek istediğiniz sanal makineyi bulun veya arayın ve ardından seçin.
3. **Ayarlar** altında **ağ**' ı SEÇIN ve ardından genel IP adresini eklemek istediğiniz ağ arabirimini aşağıdaki resimde gösterildiği gibi seçin:

   ![Ağ arabirimi seçin](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Genel IP adresleri, bir VM 'ye bağlı ağ arabirimleriyle ilişkilidir. Önceki resimde, VM yalnızca bir ağ arabirimine sahiptir. VM 'nin birden çok ağ arabirimi varsa, hepsi görünür ve genel IP adresini ile ilişkilendirmek istediğiniz ağ arabirimini seçersiniz.

4. **IP yapılandırmalarını** seçin ve ardından aşağıdaki resimde gösterildiği gıbı bir IP yapılandırması seçin:

   ![IP yapılandırması seçin](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Genel IP adresleri, bir ağ arabirimi için IP yapılandırmalarına ilişkilendirilir. Önceki resimde, ağ arabiriminin bir IP yapılandırması vardır. Ağ arabiriminde birden çok IP yapılandırması varsa, hepsi listede görünür ve genel IP adresini ile ilişkilendirmek istediğiniz IP yapılandırmasını seçersiniz.

5. **Etkin**' i seçin ve **IP adresi ' ni seçin (*gerekli ayarları yapılandırın*)**. **Genel IP adresi Seç** kutusunu otomatik olarak kapatan mevcut BIR genel IP adresi seçin. Listelenen kullanılabilir genel IP adresiniz yoksa, bir tane oluşturmanız gerekir. Nasıl yapılacağını öğrenmek için bkz. [genel IP adresi oluşturma](virtual-network-public-ip-address.md#create-a-public-ip-address). Aşağıdaki resimde gösterildiği gibi **Kaydet**' i seçin ve ardından IP yapılandırması kutusunu kapatın.

   ![Genel IP adresini etkinleştir](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Görüntülenen genel IP adresleri, VM ile aynı bölgede bulunan olanlardır. Bölgede birden çok genel IP adresiniz varsa, bu adresler burada görünür. Herhangi biri gri ise, adresin zaten farklı bir kaynakla ilişkilendirilmiş olması gerekir.

6. Aşağıdaki resimde gösterildiği gibi, IP yapılandırmasına atanan genel IP adresini görüntüleyin. Bir IP adresinin görünmesi birkaç saniye sürebilir.

   ![Atanan genel IP adresini görüntüle](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adres, her bir Azure bölgesinde kullanılan bir adres havuzundan atanır. Her bölgede kullanılan adres havuzlarının listesini görmek için bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653). Atanan adres, bölge için kullanılan havuzlarda herhangi bir adres olabilir. Adresin bölgedeki belirli bir havuzdan atanması gerekiyorsa, [genel IP adresi ön ekini](public-ip-address-prefix.md)kullanın.

7. Ağ güvenlik grubundaki güvenlik kuralları ile [VM 'ye ağ trafiğine Izin verin](#allow-network-traffic-to-the-vm) .

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)'yi veya Azure Cloud Shell kullanın. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLI, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Aşağıdaki CLı komutlarında **deneyin** düğmesini seçin. **Dene** ' nin seçilmesi, ile Azure hesabınızda oturum açmak için kullanabileceğiniz bir Cloud Shell çağırır.

1. CLı 'yi Bash içinde yerel olarak kullanıyorsanız, ile Azure 'da oturum açın `az login` .
2. Genel IP adresi, bir VM 'ye bağlı bir ağ arabiriminin IP yapılandırmasıyla ilişkilendirilir. Genel IP adresini bir IP yapılandırmasıyla ilişkilendirmek için [az Network Nic-ip-config Update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) komutunu kullanın. Aşağıdaki örnek, *myVMPublicIP* adlı mevcut BIR genel IP adresini, *myresourcegroup* adlı bir kaynak grubunda bulunan *myvmvmnıc* adlı mevcut bir ağ ARABIRIMININ *ıpconfigmyvm* adlı IP yapılandırmasına ilişkilendirir.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Var olan bir genel IP adresiniz yoksa, oluşturmak için [az Network public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create) komutunu kullanın. Örneğin, aşağıdaki komut *Myresourcegroup* adlı bir kaynak grubunda *myVMPublicIP* adlı bir genel IP adresi oluşturur.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Önceki komut, özelleştirmek isteyebileceğiniz çeşitli ayarlar için varsayılan değerleri içeren bir genel IP adresi oluşturur. Tüm genel IP adresi ayarları hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres, her bir Azure bölgesi için kullanılan bir genel IP adresi havuzundan atanır. Her bölgede kullanılan adres havuzlarının listesini görmek için bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653).

   - Sanal makinenize bağlı bir ağ arabiriminin adını bilmiyorsanız, görüntülemek için [az VM Nic List](/cli/azure/vm/nic#az_vm_nic_list) komutunu kullanın. Örneğin aşağıdaki komut, *Myresourcegroup* adlı kaynak grubunda *MYVM* adlı bir VM 'ye bağlı ağ arabirimlerinin adlarını listeler:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Çıktı aşağıdaki örneğe benzer bir veya daha fazla satır içerir:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Önceki örnekte, *Myvmvmnıc* , ağ arabiriminin adıdır.

   - Bir ağ arabirimi için bir IP yapılandırmasının adını bilmiyorsanız, bunları almak için [az Network Nic IP-Config List](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) komutunu kullanın. Örneğin, aşağıdaki komut, *Myresourcegroup* adlı bir kaynak grubunda *Myvmvmnıc* adlı BIR ağ arabirimi için IP yapılandırmalarının adlarını listeler:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. [Az VM List-ip-addresses](/cli/azure/vm#az_vm_list_ip_addresses) komutuyla IP yapılandırmasına atanan genel IP adresini görüntüleyin. Aşağıdaki örnekte, *Myresourcegroup* adlı kaynak grubunda *myvm* adlı mevcut BIR VM 'ye atanan IP adresleri gösterilmektedir.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adres, her bir Azure bölgesinde kullanılan bir adres havuzundan atanır. Her bölgede kullanılan adres havuzlarının listesini görmek için bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653). Atanan adres, bölge için kullanılan havuzlarda herhangi bir adres olabilir. Adresin bölgedeki belirli bir havuzdan atanması gerekiyorsa, [genel IP adresi ön ekini](public-ip-address-prefix.md)kullanın.

4. Ağ güvenlik grubundaki güvenlik kuralları ile [VM 'ye ağ trafiğine Izin verin](#allow-network-traffic-to-the-vm) .

## <a name="powershell"></a>PowerShell

[PowerShell](/powershell/azure/install-az-ps)'i yükleyip Azure Cloud Shell kullanın. Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir kabuktur. PowerShell 'in önceden yüklenmiş ve hesabınızla birlikte kullanılmak üzere yapılandırılmış olması. İzleyen PowerShell komutlarında **deneyin** düğmesini seçin. **Dene** ' nin seçilmesi, ile Azure hesabınızda oturum açmak için kullanabileceğiniz bir Cloud Shell çağırır.

1. PowerShell 'i yerel olarak kullanıyorsanız, ile Azure 'da oturum açın `Connect-AzAccount` .
2. Genel IP adresi, bir VM 'ye bağlı bir ağ arabiriminin IP yapılandırmasıyla ilişkilendirilir. Ağ arabiriminin içinde bulunduğu sanal ağı ve alt ağı almak için [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) ve [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) komutlarını kullanın. Ardından, var olan genel IP adresini almak için bir ağ arabirimi ve [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutunu almak üzere [Get-aznetworkınterface](/powershell/module/Az.Network/Get-AzNetworkInterface) komutunu kullanın. Daha sonra [set-Aznetworkınterfaceipconfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) komutunu kullanarak genel IP adresini IP yapılandırmasıyla ve [set-aznetworkınterface](/powershell/module/Az.Network/Set-AzNetworkInterface) komutuyla ilişkilendirin ve ağ arabirimine yeni IP yapılandırmasını yazın.

   Aşağıdaki örnek, *myVMPublicIP* adlı mevcut BIR genel IP adresini, *myvmvnet* adlı bir sanal ağda *myvmsubnet* adlı bir alt ağda bulunan *myvmvmnıc* adlı mevcut BIR ağ arabiriminin *ıpconfigmyvm* adlı IP yapılandırmasına ilişkilendirir. Tüm kaynaklar *Myresourcegroup* adlı bir kaynak grubunda bulunur.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Mevcut bir genel IP adresiniz yoksa, [Yeni-Azpublicıpaddress](/powershell/module/Az.Network/New-AzPublicIpAddress) komutunu kullanarak bir tane oluşturun. Örneğin, aşağıdaki komut, *eastus* bölgesinde *myresourcegroup* adlı bir kaynak grubunda *MYVMPUBLICIP* adlı *dinamik* bir genel IP adresi oluşturur.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Önceki komut, özelleştirmek isteyebileceğiniz çeşitli ayarlar için varsayılan değerleri içeren bir genel IP adresi oluşturur. Tüm genel IP adresi ayarları hakkında daha fazla bilgi için bkz. [genel IP adresi oluşturma](virtual-network-public-ip-address.md#create-a-public-ip-address). Adres, her bir Azure bölgesi için kullanılan bir genel IP adresi havuzundan atanır. Her bölgede kullanılan adres havuzlarının listesini görmek için bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653).

   - Sanal makinenize bağlı bir ağ arabiriminin adını bilmiyorsanız, [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) komutunu kullanarak bunları görüntüleyin. Örneğin aşağıdaki komut, *Myresourcegroup* adlı kaynak grubunda *MYVM* adlı bir VM 'ye bağlı ağ arabirimlerinin adlarını listeler:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Çıktı, aşağıdaki örneğe benzer bir veya daha fazla satır içerir. Örnek çıktıda, *Myvmvmnıc* ağ arabiriminin adıdır.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Ağ arabiriminin içinde bulunduğu sanal ağın veya alt ağın adını bilmiyorsanız, bu `Get-AzNetworkInterface` bilgileri görüntülemek için komutunu kullanın. Örneğin, aşağıdaki komut, *Myresourcegroup* adlı bir kaynak grubunda *Myvmvmnıc* adlı bir ağ arabirimi için sanal ağ ve alt ağ bilgilerini alır:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Çıktı, aşağıdaki örneğe benzer bir veya daha fazla satır içerir. Örnek çıktıda, *Myvmvnet* sanal ağın adıdır ve *myvmsubnet* alt ağın adıdır.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Bir ağ arabirimi için bir IP yapılandırmasının adını bilmiyorsanız, [Get-Aznetworkınterface](/powershell/module/Az.Network/Get-AzNetworkInterface) komutunu kullanarak bunları alın. Örneğin, aşağıdaki komut, *Myresourcegroup* adlı bir kaynak grubunda *Myvmvmnıc* adlı BIR ağ arabirimi için IP yapılandırmalarının adlarını listeler:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Çıktı, aşağıdaki örneğe benzer bir veya daha fazla satır içerir. Örnek çıktıda, *ıpconfigmyvm* bir IP yapılandırmasının adıdır.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress) komutuyla IP yapılandırmasına atanan genel IP adresini görüntüleyin. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubunda *myVMPublicIP* adlı bir genel IP adresine atanan adresi gösterir.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Bir IP yapılandırmasına atanan genel IP adresinin adını bilmiyorsanız, almak için aşağıdaki komutları çalıştırın:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Çıktı, aşağıdaki örneğe benzer bir veya daha fazla satır içerir. Örnek çıktıda *myVMPublicIP* , IP yapılandırmasına atanan genel IP adresinin adıdır.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adres, her bir Azure bölgesinde kullanılan bir adres havuzundan atanır. Her bölgede kullanılan adres havuzlarının listesini görmek için bkz. [Microsoft Azure veri MERKEZI IP aralıkları](https://www.microsoft.com/download/details.aspx?id=41653). Atanan adres, bölge için kullanılan havuzlarda herhangi bir adres olabilir. Adresin bölgedeki belirli bir havuzdan atanması gerekiyorsa, [genel IP adresi ön ekini](public-ip-address-prefix.md)kullanın.

4. Ağ güvenlik grubundaki güvenlik kuralları ile [VM 'ye ağ trafiğine Izin verin](#allow-network-traffic-to-the-vm) .

## <a name="allow-network-traffic-to-the-vm"></a>VM 'ye ağ trafiğine izin ver

İnternet 'ten genel IP adresine bağlanabilmeniz için, ağ arabirimiyle ilişkilendirdiğiniz ağ güvenlik grubu, ağ arabiriminin içinde bulunduğu alt ağ veya her ikisinin de gerekli bağlantı noktalarının açık olduğundan emin olun. Güvenlik grupları trafiği ağ arabiriminin özel IP adresine filtrelemesine karşın, gelen internet trafiği genel IP adresine ulaştığında Azure genel adresi özel IP adresine çevirir, bu nedenle bir ağ güvenlik grubu trafik akışını engelliyorsa, genel IP adresi ile iletişim başarısız olur. [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)veya [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)kullanarak bir ağ arabirimi ve alt ağının etkin güvenlik kurallarını görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Ağ güvenlik grubuyla sanal makinenize gelen internet trafiğine izin verin. Ağ güvenlik grubu oluşturmayı öğrenmek için bkz. [ağ güvenlik gruplarıyla çalışma](manage-network-security-group.md#work-with-network-security-groups). Ağ güvenlik grupları hakkında daha fazla bilgi edinmek için bkz. [güvenlik grupları](./network-security-groups-overview.md).
